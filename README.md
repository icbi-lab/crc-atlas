# The single-cell **Co**lorectal **C**ancer **A**tlas
  
> Marteau V., Nemati N., Handler K., ... & Arnold IC, Salcher S., Trajanoski Z. (2026). Single-cell integration and multi-modal profiling reveals phenotypes and spatial organization of neutrophils in colorectal cancer. Cancer Cell, 44, 146-165.e14. [doi:10.1016/j.ccell.2025.12.003](https://www.cell.com/cancer-cell/fulltext/S1535-6108(25)00537-9)

The single cell colorectal cancer atlas is a resource integrating more than 4.27 million cells from 650 patients across 49 studies (77 datasets) representing 7 billion expression values. These samples encompass the full spectrum of disease progression, from normal colon to polyps, primary tumors, and metastases, covering both early and advanced stages of CRC.

The atlas is publicly available for interactive exploration through a *cell-x-gene* instance. We also provide
`h5ad` objects which allows custom analyses of the atlas. For more information, check out the

 * [project website](https://crc.icbi.at),
 * [publication](https://www.cell.com/cancer-cell/fulltext/S1535-6108(25)00537-9), and
 * [preprint](https://www.biorxiv.org/content/10.1101/2024.08.26.609563).

This repository contains the source-code to reproduce the single-cell data analysis for the paper.
The analyses are wrapped into [nextflow](https://github.com/nextflow-io/nextflow/) pipelines, all dependencies are
provided as [singularity](https://sylabs.io/guides/3.0/user-guide/quick_start.html) containers, and input data are
available from zenodo (coming soon).

For clarity, the project is split up into two separate workflows:

 * `build_atlas`: Takes one `AnnData` object with UMI counts per dataset and integrates them into an atlas.
 * `downstream_analyses`: Runs analysis tools on the annotated, integrated atlas and produces plots for the publication.

The `build_atlas` step requires specific hardware (CPU + GPU) for exact reproducibility
(see [notes on reproducibility](#notes-on-reproducibility)) and is relatively computationally
expensive. Therefore, the `downstream_analysis` step can also operate on pre-computed results of the `build_atlas` step,
which are available from zenodo.

## Structure of this repository

* `analyses`: Place for e.g. jupyter/rmarkdown notebooks, gropued by their respective (sub-)workflows.
* `bin`: executable scripts called by the workflow
* `conf`: nextflow configuration files for all processes
* `containers`: place for singularity image files. Not part of the git repo and gets created by the download command.
* `data`: place for input data and results in different subfolders. Gets populated by the download commands and by running the workflows.
* `envs`: conda environment yaml files
* `src`: custom libraries and helper functions
* `modules`: nextflow DSL2.0 modules
* `subworkflows`: nextflow subworkflows
* `tables`: contains static content that should be under version control (e.g. manually created tables)
* `workflows`: the main nextflow workflows

## Contact

For reproducibility issues or any other requests regarding single-cell data analysis, please use the [issue tracker](https://github.com/icbi-lab/crc-atlas/issues). For anything else, you can reach out to the corresponding author(s) as indicated in the manuscript.

## Notes on reproducibility

We aimed to make this workflow reproducible by providing all input data, containerizing dependencies, and integrating all analysis steps into a Nextflow workflow. This setup allows execution on any system that can run Nextflow and Singularity. However, certain single-cell analysis algorithms like scVI/scANVI and UMAP may yield slightly different results depending on hardware, with variations in cores or CPU/GPU architecture affecting results. For details, see [this](https://github.com/scverse/scanpy/issues/2014) discussion.

Since cell-type annotations depend on clustering and the scANVI embedding, running build_atlas on different hardware may alter cell-type labels.

Below is the hardware used to execute the `build_atlas` workflow. While results should be consistent across CPUs/GPUs of the same generation, this has not been tested.

 * Compute node CPU: `Intel(R) Xeon(R) Gold 6342 CPU @ 2.80GHz` (2x)
 * GPU node GPU: `NVIDIA A100 80GB PCIe`
