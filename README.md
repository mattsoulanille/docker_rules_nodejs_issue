# rules_nodejs + rules_docker m1 mac issue

This repo reproduces a toolchain resolution error when using `rules_nodejs >= 5.6.0` with `rules_docker == 0.25.0` on Apple Silicon. The error appears only if `container_deps()` is run, and affects any rules that use the nodejs toolchain, as far as I can tell.

# How to run
Do a `bazel clean` first to make sure you see the toolchain resolve.

```
bazel clean
bazel run :index_bin --toolchain_resolution_debug='nodejs'
```

## Output when working

```
% bazel run :index_bin --toolchain_resolution_debug='nodejs'
INFO: ToolchainResolution:     Type @rules_nodejs//nodejs:toolchain_type: target platform @local_config_platform//:host: Rejected toolchain @nodejs_linux_amd64//:node_toolchain; mismatching values: linux, x86_64
INFO: ToolchainResolution:     Type @rules_nodejs//nodejs:toolchain_type: execution platform @local_config_platform//:host: Rejected toolchain @nodejs_linux_amd64//:node_toolchain; mismatching values: linux, x86_64
INFO: ToolchainResolution:     Type @rules_nodejs//nodejs:toolchain_type: target platform @local_config_platform//:host: Rejected toolchain @nodejs_linux_arm64//:node_toolchain; mismatching values: linux
INFO: ToolchainResolution:     Type @rules_nodejs//nodejs:toolchain_type: execution platform @local_config_platform//:host: Rejected toolchain @nodejs_linux_arm64//:node_toolchain; mismatching values: linux
INFO: ToolchainResolution:     Type @rules_nodejs//nodejs:toolchain_type: target platform @local_config_platform//:host: Rejected toolchain @nodejs_linux_s390x//:node_toolchain; mismatching values: linux, s390x
INFO: ToolchainResolution:     Type @rules_nodejs//nodejs:toolchain_type: execution platform @local_config_platform//:host: Rejected toolchain @nodejs_linux_s390x//:node_toolchain; mismatching values: linux, s390x
INFO: ToolchainResolution:     Type @rules_nodejs//nodejs:toolchain_type: target platform @local_config_platform//:host: Rejected toolchain @nodejs_linux_ppc64le//:node_toolchain; mismatching values: linux, ppc
INFO: ToolchainResolution:     Type @rules_nodejs//nodejs:toolchain_type: execution platform @local_config_platform//:host: Rejected toolchain @nodejs_linux_ppc64le//:node_toolchain; mismatching values: linux, ppc
INFO: ToolchainResolution:     Type @rules_nodejs//nodejs:toolchain_type: target platform @local_config_platform//:host: Rejected toolchain @nodejs_darwin_amd64//:node_toolchain; mismatching values: x86_64
INFO: ToolchainResolution:     Type @rules_nodejs//nodejs:toolchain_type: execution platform @local_config_platform//:host: Rejected toolchain @nodejs_darwin_amd64//:node_toolchain; mismatching values: x86_64
INFO: ToolchainResolution:   Type @rules_nodejs//nodejs:toolchain_type: target platform @local_config_platform//:host: execution @local_config_platform//:host: Selected toolchain @nodejs_darwin_arm64//:node_toolchain
INFO: ToolchainResolution:     Type @rules_nodejs//nodejs:toolchain_type: target platform @local_config_platform//:host: Rejected toolchain @nodejs_windows_amd64//:node_toolchain; mismatching values: windows, x86_64
INFO: ToolchainResolution: Target platform @local_config_platform//:host: Selected execution platform @local_config_platform//:host, type @rules_nodejs//nodejs:toolchain_type -> toolchain @nodejs_darwin_arm64//:node_toolchain, type @bazel_tools//tools/sh:toolchain_type -> toolchain @local_config_sh//:local_sh
INFO: ToolchainResolution: Target platform @local_config_platform//:host: Selected execution platform @local_config_platform//:host, 
INFO: ToolchainResolution: Target platform @local_config_platform//:host: Selected execution platform @local_config_platform//:host, 
INFO: Analyzed target //:index_bin (47 packages loaded, 296 targets configured).
INFO: Found 1 target...
Target //:index_bin up-to-date:
  bazel-bin/index_bin.sh
  bazel-bin/index_bin_require_patch.cjs
INFO: Elapsed time: 0.444s, Critical Path: 0.01s
INFO: 6 processes: 6 internal.
INFO: Build completed successfully, 6 total actions
INFO: Build completed successfully, 6 total actions
This is a test.
```

## Output when broken

```
% bazel run :index_bin --toolchain_resolution_debug='nodejs'
INFO: ToolchainResolution:     Type @rules_nodejs//nodejs:toolchain_type: target platform @local_config_platform//:host: Rejected toolchain @nodejs_linux_amd64//:node_toolchain; mismatching values: linux, x86_64
INFO: ToolchainResolution:     Type @rules_nodejs//nodejs:toolchain_type: execution platform @local_config_platform//:host: Rejected toolchain @nodejs_linux_amd64//:node_toolchain; mismatching values: linux, x86_64
INFO: ToolchainResolution:     Type @rules_nodejs//nodejs:toolchain_type: target platform @local_config_platform//:host: Rejected toolchain @nodejs_linux_arm64//:node_toolchain; mismatching values: linux, arm64
INFO: ToolchainResolution:     Type @rules_nodejs//nodejs:toolchain_type: execution platform @local_config_platform//:host: Rejected toolchain @nodejs_linux_arm64//:node_toolchain; mismatching values: linux, arm64
INFO: ToolchainResolution:     Type @rules_nodejs//nodejs:toolchain_type: target platform @local_config_platform//:host: Rejected toolchain @nodejs_linux_s390x//:node_toolchain; mismatching values: linux, s390x
INFO: ToolchainResolution:     Type @rules_nodejs//nodejs:toolchain_type: execution platform @local_config_platform//:host: Rejected toolchain @nodejs_linux_s390x//:node_toolchain; mismatching values: linux, s390x
INFO: ToolchainResolution:     Type @rules_nodejs//nodejs:toolchain_type: target platform @local_config_platform//:host: Rejected toolchain @nodejs_linux_ppc64le//:node_toolchain; mismatching values: linux, ppc
INFO: ToolchainResolution:     Type @rules_nodejs//nodejs:toolchain_type: execution platform @local_config_platform//:host: Rejected toolchain @nodejs_linux_ppc64le//:node_toolchain; mismatching values: linux, ppc
INFO: ToolchainResolution:     Type @rules_nodejs//nodejs:toolchain_type: target platform @local_config_platform//:host: Rejected toolchain @nodejs_darwin_amd64//:node_toolchain; mismatching values: x86_64
INFO: ToolchainResolution:     Type @rules_nodejs//nodejs:toolchain_type: execution platform @local_config_platform//:host: Rejected toolchain @nodejs_darwin_amd64//:node_toolchain; mismatching values: x86_64
INFO: ToolchainResolution:     Type @rules_nodejs//nodejs:toolchain_type: target platform @local_config_platform//:host: Rejected toolchain @nodejs_darwin_arm64//:node_toolchain; mismatching values: arm64
INFO: ToolchainResolution:     Type @rules_nodejs//nodejs:toolchain_type: execution platform @local_config_platform//:host: Rejected toolchain @nodejs_darwin_arm64//:node_toolchain; mismatching values: arm64
INFO: ToolchainResolution:     Type @rules_nodejs//nodejs:toolchain_type: target platform @local_config_platform//:host: Rejected toolchain @nodejs_windows_amd64//:node_toolchain; mismatching values: windows, x86_64
INFO: ToolchainResolution:     Type @rules_nodejs//nodejs:toolchain_type: execution platform @local_config_platform//:host: Rejected toolchain @nodejs_windows_amd64//:node_toolchain; mismatching values: windows, x86_64
INFO: ToolchainResolution:   Type @rules_nodejs//nodejs:toolchain_type: target platform @local_config_platform//:host: No toolchains found.
ERROR: /Users/matthew/Projects/docker_rules_nodejs_issue/BUILD.bazel:3:14: While resolving toolchains for target //:index_bin: no matching toolchains found for types @rules_nodejs//nodejs:toolchain_type
ERROR: Analysis of target '//:index_bin' failed; build aborted: 
INFO: Elapsed time: 0.302s
INFO: 0 processes.
FAILED: Build did NOT complete successfully (38 packages loaded, 259 targets configured)
FAILED: Build did NOT complete successfully (38 packages loaded, 259 targets configured)
```
