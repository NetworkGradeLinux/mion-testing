# Mion Testing

This repo serves two purposes: 

1. A home for snippets, scripts, and tools to assist with mion testing
2. To store and track test results.

During regular development, logs can be placed in `dev_<TEST TYPE>/` and named
`<VENDOR>_<MACHINE>.log`. For testing release candidates, the folder names are
`<CODE_NAME>_<TEST TYPE>/`. 

Any scripts or tools should be placed in `utils/<NAME OF UTIL>/`. i.e.,
`utils/foo_check/foo_check.sh`
