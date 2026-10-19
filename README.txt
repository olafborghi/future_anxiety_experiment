================================================================================
README
Future Anxiety Experiment — Analysis Repository
================================================================================

This repository contains all data and analysis scripts for the paper:
"Future Anxiety Affects Young People's Democratic Support and Engagement"

The scripts are written in R using Quarto (.qmd files). All analyses can be
reproduced by running the scripts in numerical order (01 through 10).


--------------------------------------------------------------------------------
SOFTWARE REQUIREMENTS
--------------------------------------------------------------------------------

The analyses were run with R 4.5.2 and Quarto 1.10.

All scripts use the {groundhog} package for package version management (date
"2026-01-01"), ensuring reproducibility. Groundhog will install the correct
package versions automatically on first run.

The Bayesian models (scripts 04 through 08) use the {brms} package with the
cmdstanr backend (instead of rstan). To set this up:

  1. Install cmdstanr:
     install.packages("cmdstanr", repos = c('https://stan-dev.r-universe.dev',
                       getOption("repos")))

  2. Install CmdStan:
     cmdstanr::install_cmdstan()

  3. RTools must also be installed on Windows. For full instructions see:
     https://mc-stan.org/cmdstanr/articles/cmdstanr.html

The LLM coding in script 02 used Claude Sonnet 4.5 via the Anthropic API. The API
calls are commented out and the coded outputs are provided in
02_output/LLM/, so no API key is needed to reproduce the analyses.


--------------------------------------------------------------------------------
FOLDER STRUCTURE
--------------------------------------------------------------------------------

future_anxiety_experiment/
|
|-- README.txt                          This file
|-- DATA_DICTIONARY.txt                 Variables in the analysis datasets
|-- future_anxiety_experiment.Rproj     R project file
|
|-- 00_scripts/                         Analysis scripts (run in order)
|-- 01_data/                            Raw, cleaned, and analysis-ready data
|-- 02_output/                          Model objects, figures, and tables
|-- 03_materials/                       Experimental stimuli and survey materials


--------------------------------------------------------------------------------
01_data/
--------------------------------------------------------------------------------

raw/
  exp_dat_raw.csv / .RData      Raw data as exported from the survey platform

clean/
  df_wide.csv / .RData          Cleaned data in wide format (one row per
                                participant), produced by script 01
  df_long.csv / .RData          Cleaned data in long format (one row per
                                conjoint candidate), produced by script 01

analysis/
  df_wide.csv / .RData          Final analysis dataset in wide format: the
                                cleaned data plus the LLM-coded comment
                                sentiment and emotion, produced by script 02
  df_long.csv / .RData          Final analysis dataset in long format, used
                                for the conjoint models, produced by script 02

validation/
  validation_sample.csv         Random sample of 210 comments (70 per
                                condition) drawn for the human validation of
                                the LLM sentiment coding (script 10)
  validation_sample.xlsx        The same sample with the human sentiment
                                ratings


--------------------------------------------------------------------------------
02_output/
--------------------------------------------------------------------------------

figures/
  combined_figure.svg           Main manuscript figure (treatment effects)
  combined_figure_controls.svg  Treatment effects with and without
                                demographic controls
  descriptives/                 Figures from the descriptives script
  exploratory/                  Figures from the exploratory analyses
  RQ1/                          Figures for Research Question 1
  RQ2/                          Figures for Research Question 2

models/
  RQ1/                          Saved Bayesian model objects for RQ1
  RQ2/                          Saved Bayesian model objects for RQ2
  exploratory/                  Saved manipulation check models (script 04)

tables/
  demographic_controls_ATE_table.docx
                                Treatment effects with and without
                                demographic controls
  descriptives/                 Tables from the cleaning and descriptives
                                scripts
  exploratory/                  Tables from the exploratory analyses
  RQ1/                          Tables for Research Question 1
  RQ2/                          Tables for Research Question 2

LLM/
  comments_sentiment.csv / .RData    LLM-coded sentiment scores for
                                     participant Instagram comments
  comments_emotion.csv / .RData      LLM-coded emotion labels for
                                     participant Instagram comments


--------------------------------------------------------------------------------
03_materials/
--------------------------------------------------------------------------------

stimuli/
  anxiety_stimulus.jpg          Experimental stimulus — anxiety condition
  hope_stimulus.jpg             Experimental stimulus — hope condition
  control_stimulus.jpg          Experimental stimulus — control condition

survey/
  survey_materials.pdf          Full survey instrument


--------------------------------------------------------------------------------
00_scripts/ — ANALYSIS PIPELINE
--------------------------------------------------------------------------------

Scripts are numbered and should be run in order. Each script saves its outputs
to 01_data/ or 02_output/ so that downstream scripts can load them directly.
Each script also renders to a self-contained HTML file for easy inspection.

--------------------------------------------------------------------------------
01_cleaning.qmd — Variable Preparations
--------------------------------------------------------------------------------
Reads the raw data and produces the cleaned datasets.

  - Applies pre-registered participant exclusions
  - Constructs composite scores and scale indices:
      * Pre-treatment scales: political participation, authoritarian and
        democratic preferences, emotion regulation, ambiguity intolerance,
        actively open-minded thinking (AOT), parental ideology
      * Post-treatment scales: democratic support, political participation
  - Computes reliability estimates for all multi-item scales
  - Computes the anxiety–hope dimension (manipulation check)
  - Codes conjoint candidate profiles as democratic or undemocratic
  - Creates wide-format (participant-level) and long-format (candidate-level)
    datasets and saves them to 01_data/clean/

--------------------------------------------------------------------------------
02_LLM_coding.qmd — Exploratory: Instagram Comments Text Analysis
--------------------------------------------------------------------------------
Exploratory analysis of the open-text comments participants wrote in response
to the experimental Instagram posts.

  - Generates word clouds of comment content by experimental condition
  - Uses large language models to code each comment for:
      * Sentiment (Claude Sonnet 4.5; 1 = very negative to 7 = very positive;
        non-responses coded as missing)
      * Expressed emotion (Claude Sonnet 4; angry, anxious, hopeful, happy,
        calm)
  - Adds the codes to the cleaned data and saves the analysis datasets to
    01_data/analysis/

  Note: The API calls are commented out and the coded comments are loaded from
  02_output/LLM/. The script still needs to be run, as it creates the analysis
  datasets used by all later scripts.

--------------------------------------------------------------------------------
03_descriptives.qmd — Descriptives
--------------------------------------------------------------------------------
Produces sample and variable descriptive statistics.

  - Fieldwork dates (including the distribution of completes over time) and
    survey duration
  - Attention check performance
  - Socio-demographic distributions (overall and by condition), including age
  - Correlations among key variables
  - Checks of treatment assignment and summary of survey outcomes

--------------------------------------------------------------------------------
04_post_engagement.qmd — Exploratory: Post Engagement and Manipulation Checks
--------------------------------------------------------------------------------
Exploratory analysis of the manipulation checks and how participants engaged
with the experimental posts.

  - Emotion ratings, comment sentiment, emoji reactions, sharing, and liking
    by condition
  - Bayesian models for the manipulation check outcomes (anxiety–hope
    dimension, comment sentiment, sharing, liking, and emotion ratings)
  - Emotion ratings within the control condition
  - Moderation of the effects on the anxiety–hope dimension and the emotion
    ratings by age group, gender, subjective living standard, age
    (continuous), and parental ideology

--------------------------------------------------------------------------------
05_democratic_support_models.qmd — RQ1.1: Democratic Support Models
--------------------------------------------------------------------------------
Tests how feelings about the future affect young people's democratic support.

  Main models:
    Model 1 — Primary model (average treatment effect, ATE)
    Model 2 — Interaction with age group (conditional ATE)
    Model 3 — Interaction with gender (conditional ATE)

  Supplementary models:
    Model 4 — Controlling for demographics (age, gender, ideology, education,
              subjective living standard)
    Model 5 — Pre-registered sample only
    Model 6 — Attentive participants only
    Model 7 — Item-level differences across democratic support items

  Exploratory moderation models:
    Model 8  — Interaction with subjective living standard
    Model 9  — Interaction with age (continuous)
    Model 10 — Interaction with parental ideology

  Each model reports posterior quantities of interest (contrasts, marginal
  means, HDIs) and, where applicable, a frequentist comparison.

--------------------------------------------------------------------------------
06_conjoint_candidate_models.qmd — RQ1.2: Democratic Candidate Choice Models
--------------------------------------------------------------------------------
Tests how feelings about the future affect young people's preferences for
democratic versus undemocratic political candidates in a conjoint experiment.

  Main models:
    Model 0 — Baseline conjoint model: Average Marginal Component Effects
              (AMCEs) and marginal means for all candidate attributes
    Model 1 — Primary model: conditional AMCEs by experimental condition
    Model 2 — Interaction with age group
    Model 3 — Interaction with gender

  Supplementary models:
    Model 4 — Controlling for demographics (each interacted with democratic
              behaviour)
    Model 5 — Pre-registered sample only
    Party model — Marginal means of party by condition, without and with
              demographic controls

  Exploratory moderation models:
    Model 8  — Interaction with subjective living standard
    Model 9  — Interaction with age (continuous)
    Model 10 — Interaction with parental ideology

--------------------------------------------------------------------------------
07_political_participation_models.qmd — RQ2.1: Political Participation Models
--------------------------------------------------------------------------------
Tests how feelings about the future affect young people's willingness to
participate in politics.

  Follows the same modelling structure as script 05 (Models 1 to 10), with
  Model 7 testing item-level differences across the participation items.

--------------------------------------------------------------------------------
08_information_seeking_models.qmd — RQ2.2: Political Information Seeking Models
--------------------------------------------------------------------------------
Tests how feelings about the future affect young people's interest in
democratic involvement, modelled with a hurdle log-normal model:

  - Initiation: whether participants chose to view information on how to get
    involved in democracy (hurdle part)
  - Extent: how long they viewed it, among those who chose to view it

  Models:
    Model 1  — Primary model (ATE)
    Model 2  — Interaction with age group
    Model 3  — Interaction with gender
    Model 4  — Controlling for demographics
    Model 5  — Pre-registered sample only
    Model 6  — Attentive participants only
    Model 7  — Outliers in viewing time removed
    Model 8  — Interaction with subjective living standard
    Model 9  — Interaction with age (continuous)
    Model 10 — Interaction with parental ideology

--------------------------------------------------------------------------------
09_figures_tables.qmd — Figures and Tables
--------------------------------------------------------------------------------
Loads the saved model objects from scripts 04–08 and produces the figures and
tables for the manuscript and supplementary materials.

  - Model summary and comparison tables for RQ1.1, RQ1.2, RQ2.1, and RQ2.2,
    including the moderation by age group, gender, and living standard
  - Pre-registered sample and continuous age tables
  - Figures of treatment effects overall and by subgroup, and item-level
    difference figures
  - Marginal means of party by condition (table and figure)
  - Anxiety–hope dimension by subgroup (table)
  - Combined main manuscript figure
  - Treatment effects with and without demographic controls (table and figure)

--------------------------------------------------------------------------------
10_LLM_validation.qmd — LLM Sentiment Validation
--------------------------------------------------------------------------------
Validates the LLM sentiment coding against human ratings.

  - Draws the random validation sample (saved once; not re-run on render)
  - Loads the human ratings and computes correlations with the LLM ratings
    and the anxiety–hope dimension, the intraclass correlation, and a mixed
    ANOVA of condition and rater


--------------------------------------------------------------------------------
REPRODUCIBILITY NOTES
--------------------------------------------------------------------------------

- All file paths are managed via the {here} package, relative to the project
  root (the folder containing this file).

- Scripts should be run in order (01 → 10). Scripts 04–08 save model objects
  to 02_output/models/; script 09 loads these saved objects rather than
  re-fitting models. To re-fit models, delete the folder 02_output/models/.

- Bayesian models are computationally intensive. Fitting times will vary
  depending on hardware; using multiple CPU cores (set via brms/cmdstanr
  options within each script) is recommended.

- The LLM coding in script 02 requires an Anthropic API key to re-run. The
  coded outputs are provided in 02_output/LLM/, so all other steps are fully
  reproducible without it.

================================================================================
