project(ADVANCED_TYPES_VENDOR)

include(FetchContent)

FetchContent_Declare(
  PREPROC_CONFIG
  GIT_REPOSITORY https://github.com/vitimiti/preproc_config.git
  GIT_TAG main)

FetchContent_MakeAvailable(PREPROC_CONFIG)
