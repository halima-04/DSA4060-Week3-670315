# DSA4060 Week 3 – Content-Based Movie Recommender

**Student:** Halima Mohammed
**Course:** DSA 4060 – Recommender Systems  
**Practical:** Week 3 – Building a Simple Content-Based Recommender  
**Tools:** Python, Jupyter Notebook, pandas

---

## 1. Project Overview

This project implements a simple **content-based recommender system** for a small movie-streaming platform.

The recommender suggests movies to a user based on the **structured features of movies they have previously rated**.

Instead of using the sample movie dataset from the practical handout, this repository contains an original dataset of **15 movies**, with an additional unique movie added during the practical. Each movie is represented using five binary genre features:

- Action
- Comedy
- Drama
- Romance
- SciFi

The main idea is simple:

> If a user gives high ratings to movies containing certain features, the recommender learns that those features are important to the user and gives higher scores to unseen movies containing the same features.

---

## 2. Practical Objective

The purpose of this practical is to understand the basic workflow of **content-based filtering using structured features**.

The project demonstrates how to:

1. Represent movies using structured item features.
2. Create and inspect an item-feature matrix.
3. Create a user's rating history.
4. Build a user preference profile.
5. Weight movie features using ratings.
6. Normalize the user profile.
7. Calculate recommendation scores.
8. Rank candidate movies.
9. Remove movies the user has already watched.
10. Generate Top-N recommendations.
11. Test the recommender with more than one user.
12. Add a unique movie.
13. Create a personal user profile.
14. Interpret recommendation results.
15. Discuss limitations of a basic content-based recommender.

This practical deliberately focuses on **structured features**. TF-IDF and cosine similarity are not used because they are reserved for the Week 4 practical.

---

## 3. Recommendation Workflow

The system follows this workflow:

```text
Movie Metadata
      ↓
Item Profiles
      ↓
User Ratings
      ↓
Weighted User Profile
      ↓
Recommendation Scores
      ↓
Remove Watched Movies
      ↓
Rank Candidate Movies
      ↓
Top-N Recommendations
```

The most important idea is that the model learns a **profile of the user's preferences** from the features of the movies they rated.

---

## 4. Repository Structure

```text
DSA4060-Week3-StudentID/
│
├── content_based_lab.ipynb
├── movies.csv
└── README.md
```

### Files

#### `content_based_lab.ipynb`

The main Jupyter Notebook containing:

- Markdown explanations
- Python code
- Dataset exploration
- Item-feature matrix
- User preference modelling
- Recommendation scoring
- Ranking
- Removal of watched movies
- Top-N recommendations
- Second-user test
- Unique movie
- Personal-user experiment
- Interpretation
- Limitations
- Conclusion

#### `movies.csv`

The original structured movie dataset used by the notebook.

#### `README.md`

This document explains the project, methodology, dataset, implementation, limitations, and how to run the notebook.

---

## 5. Dataset Description

The project uses an original movie dataset rather than copying the example dataset from the practical instructions.

The dataset contains **15 movies**.

Each movie has:

| Column | Description |
|---|---|
| `movie_id` | Unique identifier for each movie |
| `title` | Movie title |
| `Action` | 1 if Action is present, otherwise 0 |
| `Comedy` | 1 if Comedy is present, otherwise 0 |
| `Drama` | 1 if Drama is present, otherwise 0 |
| `Romance` | 1 if Romance is present, otherwise 0 |
| `SciFi` | 1 if SciFi is present, otherwise 0 |

### Feature Encoding

The genre columns use binary encoding:

```text
1 = Feature is present
0 = Feature is absent
```

For example, a movie represented as:

```text
Action  Comedy  Drama  Romance  SciFi
1       0       0      0        1
```

contains Action and SciFi features but does not contain Comedy, Drama, or Romance.

---

## 6. Why Structured Features Are Used

A content-based recommender needs a way to represent each item.

In this project, every movie is represented by its structured genre features.

For example:

```text
Movie → [Action, Comedy, Drama, Romance, SciFi]
```

This allows the system to compare the characteristics of movies with the preferences learned from a user's ratings.

The approach is intentionally simple so that the recommendation process can be understood step by step.

---

## 7. Item-Feature Matrix

The genre columns form the **item-feature matrix**.

Conceptually, it looks like:

```text
                 Action  Comedy  Drama  Romance  SciFi
Movie 1             1       0      0       0       0
Movie 2             0       0      1       1       0
Movie 3             1       0      1       0       0
...
```

Each row represents a movie.

Each column represents a structured feature.

The matrix is the main representation of the movie catalogue used by the recommender.

---

## 8. User Ratings

The recommender uses explicit ratings to learn a user's preferences.

Example:

```text
Movie                  Rating
Midnight Chase           5
Nairobi Love Story       2
The Last Guardian       4
Orbit Beyond Earth      5
```

A rating of `5` has a stronger influence on the user profile than a rating of `2`.

This means that highly rated movies contribute more strongly to the features they contain.

---

## 9. Building the User Profile

The user profile is created in several stages.

### Step 1: Select the features

The model takes the genre features of movies that the user has rated.

### Step 2: Weight the features

Each movie's feature vector is multiplied by the user's rating.

Conceptually:

```text
Weighted Feature = Movie Feature × User Rating
```

For example, if:

```text
Action = 1
Rating = 5
```

then the Action contribution from that movie is:

```text
1 × 5 = 5
```

If:

```text
Romance = 1
Rating = 2
```

then the Romance contribution is:

```text
1 × 2 = 2
```

### Step 3: Sum the weighted features

The weighted feature values are added across all movies the user has rated.

This produces a raw user preference profile.

### Step 4: Normalize the profile

The raw profile is divided by the total feature score.

This converts the values into proportions and makes the profile easier to interpret.

---

## 10. Recommendation Score

Once the normalized user profile has been created, every movie receives a recommendation score.

The calculation used in the notebook is:

```text
Recommendation Score
=
sum(Movie Feature × Normalized User Preference)
```

A higher score means that the movie contains more of the features that are important to the user.

For example, if a user has a strong Action preference, an unseen movie containing Action will receive a larger contribution to its recommendation score.

---

## 11. Removing Watched Movies

A recommender should normally avoid suggesting items the user has already rated.

The notebook therefore creates a list of watched movie IDs and removes them from the ranked results.

The process is:

```text
All Movies
    ↓
Calculate Scores
    ↓
Sort by Score
    ↓
Remove Watched Movies
    ↓
Return Top-N
```

This ensures that the final recommendations are selected from movies the user has not already watched.

---

## 12. Top-N Recommendation

The reusable recommendation function accepts an `n` parameter.

For example:

```python
recommend_movies(
    movies,
    ratings,
    features,
    n=3
)
```

returns the Top 3 unseen movies.

For the personal-user experiment, the notebook requests:

```python
n=5
```

to generate Top 5 recommendations.

---

## 13. Testing Different Users

The project tests the recommender using different rating histories.

This is important because a content-based recommender should not produce one fixed recommendation list for everyone.

Two users can watch the same catalogue but receive different recommendations because their ratings can create different preference profiles.

For example:

```text
User A
Action ↑
SciFi  ↑

User B
Drama  ↑
Romance ↑
Comedy ↑
```

Since the profiles are different, the scores assigned to unseen movies can also be different.

---

## 14. Unique Movie

The practical requires each student to add a unique movie.

This project adds:

```text
Digital Shadows
```

with the following feature profile:

```text
Action = 1
Comedy = 0
Drama = 0
Romance = 0
SciFi = 1
```

Therefore:

```text
Digital Shadows → Action + SciFi
```

This movie is especially relevant to the example User A profile because Action and SciFi are among the strongest preferences learned from that user's ratings.

---

## 15. Personal User Experiment

The notebook also creates a separate personal-user rating history.

The purpose is to demonstrate that the same recommender function can be reused with another user's ratings.

The notebook:

1. Creates the user's rating history.
2. Builds the user's feature profile.
3. Generates Top 5 recommendations.
4. Examines the strongest preferences.
5. Interprets why movies receive high scores.
6. Discusses whether any recommendation is surprising.

---

## 16. Cold-Start Example

The notebook also considers a new user who has watched only one movie.

For example:

```text
Midnight Chase → 5 stars
```

The system would learn a profile almost entirely from the features of that one movie.

This is a problem because one movie does not provide enough information to understand a user's complete taste.

The user could actually enjoy Drama, Romance, Comedy, and SciFi, but the recommender would not know this without additional interactions.

This is known as a **cold-start problem** for new users.

---

## 17. Main Limitations

Although the recommender works, it is intentionally basic.

### 17.1 Limited Number of Features

Only five genre features are used:

```text
Action
Comedy
Drama
Romance
SciFi
```

Real movie recommendation systems can use much richer metadata.

### 17.2 Missing Movie Information

The system does not consider:

- Actors
- Directors
- Release year
- Language
- Movie descriptions
- Storyline
- Runtime
- Popularity
- Reviews
- Production information

These could provide additional information about whether a movie is suitable for a user.

### 17.3 Overspecialization

Content-based systems can recommend items that are too similar to what the user already likes.

For example, a user who strongly prefers Action could receive mostly Action movies and have fewer opportunities to discover something outside that preference.

### 17.4 Cold-Start Problem

A new user with no ratings does not have a meaningful preference profile.

Even one rating may not be enough to understand the user's interests.

### 17.5 Dependence on Metadata

The model can only learn from the features it is given.

If a movie has incorrect or incomplete genre labels, the recommendation score may also be misleading.

### 17.6 No Collaborative Filtering

The system does not learn from other users.

It only uses:

```text
The current user's ratings
+
Movie features
```

A collaborative recommender would also use patterns from multiple users.

### 17.7 Simple Rating Assumption

The approach assumes that higher ratings indicate stronger preference.

It does not account for more complex behaviours, such as a user giving ratings differently from another user or the possibility that a rating was influenced by factors outside the movie's genres.

---

## 18. Why This Is a Content-Based Recommender

This system is content-based because recommendations are generated from the **content/features of the items** and the user's learned preferences for those features.

The model does not need other users to generate a recommendation.

The central relationship is:

```text
User Ratings
     ↓
User Preference Profile
     ↓
Compare With Movie Features
     ↓
Recommendation Score
```

This distinguishes the approach from collaborative filtering, where recommendations are based primarily on interactions and similarities among users and/or items.

---

## 19. Technologies Used

### Python

Python is used to implement the recommendation workflow.

### pandas

pandas is used for:

- Creating and loading datasets
- Data inspection
- Data merging
- Feature selection
- Feature weighting
- Aggregation
- Sorting
- Filtering
- Recommendation generation

### Jupyter Notebook

The notebook combines executable Python code with Markdown explanations, making it possible to document the reasoning behind each stage of the recommender.

---

## 20. Requirements

The project requires:

- Python 3.x
- Jupyter Notebook or JupyterLab
- pandas

Optional:

- VS Code with the Jupyter extension
- An environment such as Anaconda or a Python virtual environment

---

## 21. How to Run the Project

### Option 1: Jupyter Notebook

Open a terminal in the repository folder and run:

```bash
jupyter notebook
```

Then open:

```text
content_based_lab.ipynb
```

Run the cells from top to bottom.

### Option 2: VS Code

1. Open the repository folder in VS Code.
2. Open `content_based_lab.ipynb`.
3. Select a Python 3 kernel.
4. Make sure `pandas` is installed.
5. Run the notebook cells from top to bottom.

To install pandas if necessary:

```bash
pip install pandas
```

---

## 22. Expected Project Output

After running the notebook, the project should produce:

- Dataset inspection results
- Item-feature matrix
- Individual movie profile
- User rating history
- Weighted feature values
- User preference profile
- Normalized user profile
- Recommendation scores
- Ranked movie list
- Unseen movie recommendations
- Top 3 recommendations
- Second-user recommendations
- Unique movie
- Personal Top 5 recommendations
- Interpretation of user preferences
- Discussion of limitations

---

## 23. Project Learning Outcomes

By completing this project, the following recommender-system concepts are demonstrated:

### Item Representation

Movies can be represented as structured feature vectors.

### User Representation

A user's preferences can be represented as weighted feature values derived from their ratings.

### Preference Modelling

Higher ratings can contribute more strongly to a user's profile.

### Candidate Scoring

Each unseen movie can be assigned a score based on how well its features match the user's preferences.

### Ranking

Movies can be sorted according to their recommendation scores.

### Top-N Recommendation

The highest-ranked unseen movies can be returned to the user.

### Explainability

Because the score is based directly on known features, the recommendation can be explained using the user's strongest preferences.

---

## 24. Conclusion

This project demonstrates the complete basic workflow of a structured-feature content-based recommender.

The recommender starts with movie metadata and user ratings. It converts the user's rating history into a weighted preference profile, uses that profile to score movies, removes movies that the user has already watched, and returns the highest-ranked unseen movies.

The approach is simple, transparent, and easy to understand, which makes it useful for learning the fundamental concepts behind content-based recommendation.

However, the system is limited by its small feature set and does not use richer metadata or information from other users. More advanced recommendation systems can combine content-based methods with collaborative filtering, richer item representations, and other modelling approaches.

---

## 25. Final Reflection

An important lesson from this practical is that recommendation quality depends heavily on the quality of the item representation and the amount of information available about the user.

If the system has only five genre features and a few ratings, it can identify broad preferences, but it cannot fully understand a user's taste.

As more meaningful features and user interactions are added, the recommendation system can build a more informative representation of both the user and the items.

The Week 3 implementation therefore provides a foundation for understanding more advanced recommender-system techniques.
