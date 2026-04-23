================================================================================
README
Future Feelings Experiment — Analysis Repository
================================================================================

This repository contains all data and analysis scripts for the paper:
"Future Feelings Experiment"

The scripts are written in R using Quarto (.qmd files). All analyses can be
reproduced by running the scripts in numerical order (01 through 09).


--------------------------------------------------------------------------------
SOFTWARE REQUIREMENTS
--------------------------------------------------------------------------------

All scripts use the {groundhog} package for package version management, ensuring
reproducibility. Groundhog will install the correct package versions
automatically on first run.

The main modelling scripts (04 through 08) use Bayesian regression via the
{brms} package with the cmdstanr backend (instead of rstan). To set this up:

  1. Install cmdstanr:
     install.packages("cmdstanr", repos = c('https://stan-dev.r-universe.dev',
                       getOption("repos")))

  2. Install CmdStan:
     cmdstanr::install_cmdstan()

  3. RTools must also be installed on Windows. For full instructions see:
     https://mc-stan.org/cmdstanr/articles/cmdstanr.html

Script 02 (LLM coding) additionally requires an API key for LLM access. The
API key has been removed from the shared code; users wishing to reproduce the
LLM-based sentiment analysis will need to provide their own key.


--------------------------------------------------------------------------------
FOLDER STRUCTURE
--------------------------------------------------------------------------------

future_feelings_experiment/
|
|-- README.txt                          This file
|-- future_feelings_experiment.Rproj   R project file (open this to start)
|
|-- 00_scripts/                         Analysis scripts (run in order)
|-- 01_data/                            Raw, cleaned, and analysis-ready data
|-- 02_output/                          All model outputs, figures, and tables
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
                                conjoint trial), produced by script 01

analysis/
  df_wide.csv / .RData          Final analysis dataset in wide format,
                                with all composite scores and coded variables
  df_long.csv / .RData          Final analysis dataset in long format,
                                used for conjoint models


--------------------------------------------------------------------------------
02_output/
--------------------------------------------------------------------------------

figures/
  combined_figure.png           Combined main manuscript figure
  descriptives/                 Figures from the descriptives script
  exploratory/                  Figures from exploratory analyses
  RQ1/                          Figures for Research Question 1
  RQ2/                          Figures for Research Question 2

models/
  RQ1/                          Saved Bayesian model objects for RQ1
  RQ2/                          Saved Bayesian model objects for RQ2

tables/
  descriptives/                 Tables from the descriptives script
  exploratory/                  Tables from exploratory analyses
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
Reads the raw data and produces the cleaned analysis datasets.

  - Applies pre-registered participant exclusions
  - Constructs composite scores and scale indices:
      * Pre-treatment scales: political participation, authoritarian and
        democratic preferences, emotion regulation, ambiguity avoidance,
        actively open-minded thinking (AOT)
      * Post-treatment scales: democratic support, political participation
  - Computes reliability estimates for all multi-item scales
  - Checks the manipulation (treatment assignment and emotion ratings)
  - Codes conjoint candidate profiles as democratic or undemocratic
  - Creates wide-format (participant-level) and long-format (trial-level)
    analysis datasets and saves them to 01_data/analysis/

--------------------------------------------------------------------------------
02_LLM_coding.qmd — Exploratory: Instagram Comments Text Analysis
--------------------------------------------------------------------------------
Exploratory analysis of the open-text comments participants wrote in response
to the experimental Instagram posts.

  - Generates word clouds of comment content by experimental condition
  - Uses a large language model (LLM) to code each comment for:
      * Sentiment (continuous score, 1–99)
      * Expressed emotion
  - Saves coded comment data to 02_output/LLM/

  Note: The LLM API key has been removed from this script. Users wishing to
  re-run the LLM coding step must supply their own API credentials.
  The coded output files are provided in 02_output/LLM/ so that downstream
  scripts do not depend on re-running this step.

--------------------------------------------------------------------------------
03_descriptives.qmd — Descriptives
--------------------------------------------------------------------------------
Produces sample and variable descriptive statistics.

  - Fieldwork dates and survey duration
  - Attention check performance
  - Socio-demographic distributions (including age)
  - Correlations among key variables
  - Checks of treatment assignment and randomisation balance
  - Summary of post-treatment survey outcomes

--------------------------------------------------------------------------------
04_post_engagement.qmd — Exploratory: Post Engagement
--------------------------------------------------------------------------------
Exploratory analysis of how participants engaged with the experimental posts.

  - Time spent viewing the post
  - Rates of commenting, liking, and sharing by condition
  - Bayesian models for manipulation check outcomes (anxiety-hope dimension,
    comment sentiment, sharing, liking, and emotion ratings)
  - Visualises treatment effects on all engagement outcomes

--------------------------------------------------------------------------------
05_democratic_support_models.qmd — RQ1.1: Democratic Support Models
--------------------------------------------------------------------------------
Tests how feelings about the future affect young people's democratic attitudes.

  Main models:
    Model 1 — Primary model (average treatment effect, ATE)
    Model 2 — Interaction with age group (conditional ATE)
    Model 3 — Interaction with gender (conditional ATE)

  Supplementary models:
    Model 4 — Controlling for demographics and pre-treatment measures
    Model 5 — Pre-registered sample only
    Model 6 — Attentive participants only
    Model 7 — Item-level differences across democratic support items

  Each model reports posterior quantities of interest (contrasts, marginal
  means, HDIs) and a frequentist comparison for robustness.

--------------------------------------------------------------------------------
06_conjoint_candidate_models.qmd — RQ1.2: Democratic Candidate Choice Models
--------------------------------------------------------------------------------
Tests how feelings about the future affect young people's preferences for
democratic versus undemocratic political candidates in a conjoint experiment.

  - Model 0 — Baseline conjoint model: Average Marginal Component Effects
    (AMCEs) and marginal means for all candidate attributes
  - Model 1 — Primary model: conditional AMCEs (cAMCEs) by experimental
    condition
  - Model 2 — Interaction with age group
  - Model 3 — Interaction with gender

  Reports AMCEs, marginal means, and pairwise contrasts across conditions.

--------------------------------------------------------------------------------
07_political_participation_models.qmd — RQ2.1: Political Participation Models
--------------------------------------------------------------------------------
Tests how feelings about the future affect young people's willingness to
participate politically.

  Follows the same modelling structure as script 05:
    Model 1 — Primary model (ATE)
    Model 2 — Interaction with age group
    Model 3 — Interaction with gender
    Model 4 — Controlling for demographics and pre-treatment measures
    Model 5 — Pre-registered sample only
    Model 6 — Attentive participants only
    Model 7 — Item-level differences

--------------------------------------------------------------------------------
08_information_seeking_models.qmd — RQ2.2: Political Information Seeking Models
--------------------------------------------------------------------------------
Tests how feelings about the future affect young people's political information
seeking, modelled as a two-part outcome:

  - Initiation: whether participants sought out additional political information
    (binary)
  - Extent: how much additional information they sought (continuous, among
    those who initiated)

  Models:
    Model 1 — Primary model (ATE)
    Model 2 — Interaction with age group
    Model 3 — Interaction with gender
    Model 4 — Controlling for demographics
    Model 5 — Pre-registered sample only

--------------------------------------------------------------------------------
09_figures_tables.qmd — Figures and Tables
--------------------------------------------------------------------------------
Loads the saved model objects from scripts 05–08 and produces all publication-
ready figures and tables.

  - Model summary tables for RQ1.1, RQ1.2, RQ2.1, and RQ2.2
  - Comparison (contrasts) tables for each RQ
  - Pre-registered sample robustness tables
  - Main manuscript figures combining ATEs and conditional effects
  - Item-level difference figures
  - Saves all outputs to 02_output/figures/ and 02_output/tables/


--------------------------------------------------------------------------------
REPRODUCIBILITY NOTES
--------------------------------------------------------------------------------

- Open future_feelings_experiment.Rproj in RStudio before running any scripts.
  This sets the working directory correctly so that all relative file paths
  (managed via the {here} package) resolve properly.

- Scripts should be run in order (01 → 09). Scripts 05–08 save model objects
  to 02_output/models/; script 09 loads these saved objects rather than
  re-fitting models. To re-run models, delete the folder 02_output/models/.

- Bayesian models are computationally intensive. Fitting times will vary
  depending on hardware; using multiple CPU cores (set via brms/cmdstanr
  options within each script) is recommended.

- The LLM coding step in script 02 requires an external API key and cannot be
  reproduced without one. The coded outputs are provided directly in
  02_output/LLM/ so all other scripts remain fully reproducible without
  re-running script 02.

================================================================================
