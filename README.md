
[![Build Status](https://travis-ci.com/espm-157/final-project-individual-option-sarahtang.svg?token=N7vjQnawxqcEd7wx9dvu&branch=master)](https://travis-ci.com/espm-157/final-project-individual-option-sarahtang)

## Team Members:

- Sarah Tang, sarahtang

## assignment

In this module, I use and analyze spatial vector data as well as visualize the spatial data. I also use more than 5 dplyr verbs.

All work for this assignment is in the `analysis` directory.

## Special files

All team repositories will also include most of the special files found here:

### Common files

- `README.md` this file, a general overview of the repository in markdown format.  
- `.gitignore` Optional file, ignore common file types we don't want to accidentally commit to GitHub. Most projects should use this. 
- `<REPO-NAME>.Rproj` Optional, an R-Project file created by RStudio for it's own configuration.  Some people prefer to `.gitignore` this file.


### Infrastructure for Testing

- `.travis.yml`: A configuration file for automatically running [continuous integration](https://travis-ci.com) checks to verify reproducibility of all `.Rmd` notebooks in the repo.  If all `.Rmd` notebooks can render successfully, the "Build Status" badge above will be green (`build success`), otherwise it will be red (`build failure`).  
- `DESCRIPTION` a metadata file for the repository, based on the R package standard. It's main purpose here is as a place to list any additional R packages/libraries needed for any of the `.Rmd` files to run.
- `tests/render_rmds.R` an R script that is run to execute the above described tests, rendering all `.Rmd` notebooks. 




