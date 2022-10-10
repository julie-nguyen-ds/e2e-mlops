# Demo guide

This demo is hosted inside the [Field-Eng West demo workspace](https://adb-2290777133481849.9.azuredatabricks.net/)

You **DO NOT** have to run and set up the demo from the beginning. The job clusters take time to be provisioned; the multi-task jobs themselves take time to run.
Instead, we will start from a steady state where we have a model already trained and deployed to production.  See the registered model [here](https://adb-2290777133481849.9.azuredatabricks.net/?o=2290777133481849#mlflow/models/e2e_mlops_telco_churn_prod)

The crux of this demo lies in walking the customer through how **a code change triggers a CI/CD process using `dbx` and Github Actions**. In other words, this demo is not a completely standalone artefact. Instead, it is meant to add color to the [**Reference Architecture in our Big Book of MLOps**](https://www.databricks.com/p/ebook/the-big-book-of-mlops). 
It is also mean to complement [these slides](https://docs.google.com/presentation/d/1cghsAPcDp0ILDQdZ0CbgFY9l_B2TFfAnp29ITZEjGMI/edit), meaning that it's a good idea to also walk the customer through an explanation of the project folder structure, who owns what in the Repo, etc.) 

So you should perform the steps in the **Set up** section of the README.md, but skip Step 1 of the **Workflow** section. This demo should start from Step 2 in the README.md. For smoothness, note that it's not compulsory to actually make a code change and pull request to trigger a Github workflow (again, this is time-consuming and no one wants to be waiting around). Instead, you can simulate making a change, commit and push from your local IDE, but not actually push to Git. 
Then, switch to the [Github page](https://github.com/jeannefukumaru/e2e-mlops/actions) where there are existing pull requests and previously run Github Actions workflows. There, you can walkthrough the logs of the `CI pipeline`Github Actions workflow to give the customer an idea of what has been triggered. 
You can also walk through the `/.github` folder to show how a Github Actions workflow has been specified to be triggered upon a code request. 

From Step 2, move on to Step 3. Similar to. Step 2, it's not needed to push a new tag and create a new release. Go to the Actions tab in the Github repo and point out the `release pipeline` Github Action workflow

The subsequent steps are manually triggered. Again, they have already been triggered to save time. Navigate to the Workflows page, search for the `PROD-telco-churn-model-train`, `PROD-telco-churn-model-deployment` and `PROD-telco-churn-model-inference` jobs

As outlined in the README.md
1. `PROD-telco-churn-model-train` will result in two model versions registered in MLflow Model Registry:

- Version 1 (Production): RandomForestClassifier (max_depth=4)
- Version 2 (Staging): RandomForestClassifier (max_depth=8)

2. `PROD-telco-churn-model-deployment`
- Compare new “candidate model” in stage='Staging' versus current Production model in stage='Production'.
- Comparison criteria set through model_deployment.yml
- Compute predictions using both models against a specified reference dataset
- If Staging model performs better than Production model, promote Staging model to Production and archive existing Production model
- If Staging model performs worse than Production model, archive Staging model

3. Batch model inference steps (PROD-telco-churn-model-inference-batch)

Load model from stage=Production in Model Registry
NOTE: model must have been logged to MLflow using the Feature Store API
Use primary keys in specified inference input data to load features from feature store
Apply loaded model to loaded features
Write predictions to specified Delta path

**END**
