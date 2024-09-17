# Batch Integration


<!--
This file is automatically generated from the tasks's api/*.yaml files.
Do not edit this file directly.
-->

Remove unwanted batch effects from scRNA-seq data while retaining
biologically meaningful variation.

Repository:
[openproblems-bio/task_batch_integration](https://github.com/openproblems-bio/task_batch_integration)

## Description

As single-cell technologies advance, single-cell datasets are growing
both in size and complexity. Especially in consortia such as the Human
Cell Atlas, individual studies combine data from multiple labs, each
sequencing multiple individuals possibly with different technologies.
This gives rise to complex batch effects in the data that must be
computationally removed to perform a joint analysis. These batch
integration methods must remove the batch effect while not removing
relevant biological information. Currently, over 200 tools exist that
aim to remove batch effects scRNA-seq datasets \[@zappia2018exploring\].
These methods balance the removal of batch effects with the conservation
of nuanced biological information in different ways. This abundance of
tools has complicated batch integration method choice, leading to
several benchmarks on this topic \[@luecken2020benchmarking;
@tran2020benchmark; @chazarragil2021flexible; @mereu2020benchmarking\].
Yet, benchmarks use different metrics, method implementations and
datasets. Here we build a living benchmarking task for batch integration
methods with the vision of improving the consistency of method
evaluation.

In this task we evaluate batch integration methods on their ability to
remove batch effects in the data while conserving variation attributed
to biological effects. As input, methods require either normalised or
unnormalised data with multiple batches and consistent cell type labels.
The batch integrated output can be a feature matrix, a low dimensional
embedding and/or a neighbourhood graph. The respective batch-integrated
representation is then evaluated using sets of metrics that capture how
well batch effects are removed and whether biological variance is
conserved. We have based this particular task on the latest, and most
extensive benchmark of single-cell data integration methods.

## Authors & contributors

| name              | roles              |
|:------------------|:-------------------|
| Michaela Mueller  | maintainer, author |
| Malte Luecken     | author             |
| Daniel Strobl     | author             |
| Robrecht Cannoodt | contributor        |
| Scott Gigante     | contributor        |
| Kai Waldrant      | contributor        |
| Nartin Kim        | contributor        |

## API

``` mermaid
flowchart LR
  file_common_dataset("Common Dataset")
  comp_process_dataset[/"Data processor"/]
  file_dataset("Dataset")
  file_solution("Solution")
  comp_control_method[/"Control method"/]
  comp_method[/"Method"/]
  comp_metric[/"Metric"/]
  file_integrated("Integrated Dataset")
  file_score("Score")
  file_common_dataset---comp_process_dataset
  comp_process_dataset-->file_dataset
  comp_process_dataset-->file_solution
  file_dataset---comp_control_method
  file_dataset---comp_method
  file_solution---comp_control_method
  file_solution---comp_metric
  comp_control_method-->file_integrated
  comp_method-->file_integrated
  comp_metric-->file_score
  file_integrated---comp_metric
```

## File format: Common Dataset

A subset of the common dataset.

Example file:
`resources_test/common/cxg_mouse_pancreas_atlas/dataset.h5ad`

Format:

<div class="small">

    AnnData object
     obs: 'cell_type', 'batch'
     var: 'hvg', 'hvg_score', 'feature_name'
     obsm: 'X_pca'
     obsp: 'knn_distances', 'knn_connectivities'
     layers: 'counts', 'normalized'
     uns: 'dataset_id', 'dataset_name', 'dataset_url', 'dataset_reference', 'dataset_summary', 'dataset_description', 'dataset_organism', 'normalization_id', 'knn'

</div>

Data structure:

<div class="small">

| Slot | Type | Description |
|:---|:---|:---|
| `obs["cell_type"]` | `string` | Cell type information. |
| `obs["batch"]` | `string` | Batch information. |
| `var["hvg"]` | `boolean` | Whether or not the feature is considered to be a ‘highly variable gene’. |
| `var["hvg_score"]` | `double` | A ranking of the features by hvg. |
| `var["feature_name"]` | `string` | A human-readable name for the feature, usually a gene symbol. |
| `obsm["X_pca"]` | `double` | The resulting PCA embedding. |
| `obsp["knn_distances"]` | `double` | K nearest neighbors distance matrix. |
| `obsp["knn_connectivities"]` | `double` | K nearest neighbors connectivities matrix. |
| `layers["counts"]` | `integer` | Raw counts. |
| `layers["normalized"]` | `double` | Normalized expression values. |
| `uns["dataset_id"]` | `string` | A unique identifier for the dataset. |
| `uns["dataset_name"]` | `string` | Nicely formatted name. |
| `uns["dataset_url"]` | `string` | (*Optional*) Link to the original source of the dataset. |
| `uns["dataset_reference"]` | `string` | (*Optional*) Bibtex reference of the paper in which the dataset was published. |
| `uns["dataset_summary"]` | `string` | Short description of the dataset. |
| `uns["dataset_description"]` | `string` | Long description of the dataset. |
| `uns["dataset_organism"]` | `string` | (*Optional*) The organism of the sample in the dataset. |
| `uns["normalization_id"]` | `string` | Which normalization was used. |
| `uns["knn"]` | `object` | (*Optional*) Supplementary K nearest neighbors data. |

</div>

## Component type: Data processor

A label projection dataset processor.

Arguments:

<div class="small">

| Name | Type | Description |
|:---|:---|:---|
| `--input` | `file` | A subset of the common dataset. |
| `--output_dataset` | `file` | (*Output*) Unintegrated AnnData HDF5 file. |
| `--output_solution` | `file` | (*Output*) Uncensored dataset containing the true labels. |
| `--obs_label` | `string` | (*Optional*) NA. Default: `cell_type`. |
| `--obs_batch` | `string` | (*Optional*) NA. Default: `batch`. |
| `--hvgs` | `integer` | (*Optional*) NA. Default: `2000`. |
| `--subset_hvg` | `boolean` | (*Optional*) NA. Default: `FALSE`. |

</div>

## File format: Dataset

Unintegrated AnnData HDF5 file.

Example file:
`resources_test/batch_integration/cxg_mouse_pancreas_atlas/dataset.h5ad`

Format:

<div class="small">

    AnnData object
     obs: 'batch', 'label'
     var: 'hvg', 'hvg_score', 'feature_name'
     obsm: 'X_pca'
     obsp: 'knn_distances', 'knn_connectivities'
     layers: 'counts', 'normalized'
     uns: 'dataset_id', 'normalization_id', 'dataset_organism', 'knn'

</div>

Data structure:

<div class="small">

| Slot | Type | Description |
|:---|:---|:---|
| `obs["batch"]` | `string` | Batch information. |
| `obs["label"]` | `string` | label information. |
| `var["hvg"]` | `boolean` | Whether or not the feature is considered to be a ‘highly variable gene’. |
| `var["hvg_score"]` | `double` | A ranking of the features by hvg. |
| `var["feature_name"]` | `string` | A human-readable name for the feature, usually a gene symbol. |
| `obsm["X_pca"]` | `double` | The resulting PCA embedding. |
| `obsp["knn_distances"]` | `double` | K nearest neighbors distance matrix. |
| `obsp["knn_connectivities"]` | `double` | K nearest neighbors connectivities matrix. |
| `layers["counts"]` | `integer` | Raw counts. |
| `layers["normalized"]` | `double` | Normalized expression values. |
| `uns["dataset_id"]` | `string` | A unique identifier for the dataset. |
| `uns["normalization_id"]` | `string` | Which normalization was used. |
| `uns["dataset_organism"]` | `string` | (*Optional*) The organism of the sample in the dataset. |
| `uns["knn"]` | `object` | Supplementary K nearest neighbors data. |

</div>

## File format: Solution

Uncensored dataset containing the true labels.

Example file:
`resources_test/batch_integration/cxg_mouse_pancreas_atlas/solution.h5ad`

Format:

<div class="small">

    AnnData object
     obs: 'batch', 'label'
     var: 'hvg', 'hvg_score', 'feature_name'
     obsm: 'X_pca'
     obsp: 'knn_distances', 'knn_connectivities'
     layers: 'counts', 'normalized'
     uns: 'dataset_id', 'dataset_name', 'dataset_url', 'dataset_reference', 'dataset_summary', 'dataset_description', 'dataset_organism', 'normalization_id', 'knn'

</div>

Data structure:

<div class="small">

| Slot | Type | Description |
|:---|:---|:---|
| `obs["batch"]` | `string` | Batch information. |
| `obs["label"]` | `string` | label information. |
| `var["hvg"]` | `boolean` | Whether or not the feature is considered to be a ‘highly variable gene’. |
| `var["hvg_score"]` | `double` | A ranking of the features by hvg. |
| `var["feature_name"]` | `string` | A human-readable name for the feature, usually a gene symbol. |
| `obsm["X_pca"]` | `double` | The resulting PCA embedding. |
| `obsp["knn_distances"]` | `double` | K nearest neighbors distance matrix. |
| `obsp["knn_connectivities"]` | `double` | K nearest neighbors connectivities matrix. |
| `layers["counts"]` | `integer` | Raw counts. |
| `layers["normalized"]` | `double` | Normalized expression values. |
| `uns["dataset_id"]` | `string` | A unique identifier for the dataset. |
| `uns["dataset_name"]` | `string` | Nicely formatted name. |
| `uns["dataset_url"]` | `string` | (*Optional*) Link to the original source of the dataset. |
| `uns["dataset_reference"]` | `string` | (*Optional*) Bibtex reference of the paper in which the dataset was published. |
| `uns["dataset_summary"]` | `string` | Short description of the dataset. |
| `uns["dataset_description"]` | `string` | Long description of the dataset. |
| `uns["dataset_organism"]` | `string` | (*Optional*) The organism of the sample in the dataset. |
| `uns["normalization_id"]` | `string` | Which normalization was used. |
| `uns["knn"]` | `object` | Supplementary K nearest neighbors data. |

</div>

## Component type: Control method

A control method for the batch integration task.

Arguments:

<div class="small">

| Name               | Type   | Description                                    |
|:-------------------|:-------|:-----------------------------------------------|
| `--input_dataset`  | `file` | Unintegrated AnnData HDF5 file.                |
| `--input_solution` | `file` | Uncensored dataset containing the true labels. |
| `--output`         | `file` | (*Output*) An integrated AnnData dataset.      |

</div>

## Component type: Method

A method for the batch integration task.

Arguments:

<div class="small">

| Name       | Type   | Description                               |
|:-----------|:-------|:------------------------------------------|
| `--input`  | `file` | Unintegrated AnnData HDF5 file.           |
| `--output` | `file` | (*Output*) An integrated AnnData dataset. |

</div>

## Component type: Metric

A metric for evaluating batch integration methods.

Arguments:

<div class="small">

| Name                 | Type   | Description                                    |
|:---------------------|:-------|:-----------------------------------------------|
| `--input_integrated` | `file` | An integrated AnnData dataset.                 |
| `--input_solution`   | `file` | Uncensored dataset containing the true labels. |
| `--output`           | `file` | (*Output*) Metric score file.                  |

</div>

## File format: Integrated Dataset

An integrated AnnData dataset.

Example file:
`resources_test/batch_integration/cxg_mouse_pancreas_atlas/integrated.h5ad`

Description:

Must contain at least one of:

- Feature: the corrected_counts layer
- Embedding: the X_emb obsm
- Graph: the connectivities and distances obsp

Format:

<div class="small">

    AnnData object
     obsm: 'X_emb'
     obsp: 'connectivities', 'distances'
     layers: 'corrected_counts'
     uns: 'dataset_id', 'normalization_id', 'dataset_organism', 'method_id', 'neighbors'

</div>

Data structure:

<div class="small">

| Slot | Type | Description |
|:---|:---|:---|
| `obsm["X_emb"]` | `double` | (*Optional*) Embedding output - 2D coordinate matrix. |
| `obsp["connectivities"]` | `double` | (*Optional*) Graph output - neighbor connectivities matrix. |
| `obsp["distances"]` | `double` | (*Optional*) Graph output - neighbor distances matrix. |
| `layers["corrected_counts"]` | `double` | (*Optional*) Feature output - corrected counts. |
| `uns["dataset_id"]` | `string` | A unique identifier for the dataset. |
| `uns["normalization_id"]` | `string` | Which normalization was used. |
| `uns["dataset_organism"]` | `string` | (*Optional*) The organism of the sample in the dataset. |
| `uns["method_id"]` | `string` | A unique identifier for the method. |
| `uns["neighbors"]` | `object` | Supplementary K nearest neighbors data. |

</div>

## File format: Score

Metric score file

Example file: `score.h5ad`

Format:

<div class="small">

    AnnData object
     uns: 'dataset_id', 'normalization_id', 'method_id', 'metric_ids', 'metric_values'

</div>

Data structure:

<div class="small">

| Slot | Type | Description |
|:---|:---|:---|
| `uns["dataset_id"]` | `string` | A unique identifier for the dataset. |
| `uns["normalization_id"]` | `string` | Which normalization was used. |
| `uns["method_id"]` | `string` | A unique identifier for the method. |
| `uns["metric_ids"]` | `string` | One or more unique metric identifiers. |
| `uns["metric_values"]` | `double` | The metric values obtained for the given prediction. Must be of same length as ‘metric_ids’. |

</div>

