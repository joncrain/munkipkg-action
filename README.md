# munkipkg-action

MunkiPkg action will create a build artifact in the build directory `build/*.pkg` and will output the version of the pkg being built.

## Outputs

|  Output  |   type   |                               Example                               |                    Description                    |
| :------: | :------: | :-----------------------------------------------------------------: | :-----------------------------------------------: |
|   tag    | `string` |                        `v1.0` *OR* `v8.8.2`                         | Returns the pkg version from the build-info file. |
| filename | `string` |                           `pkg-name.pkg`                            |      Returns the filename of the built pkg.       |
| filepath | `string` | `/Users/runner/work/pkg-name/test-munki-pkg/build/pkg-name-1.0.pkg` |      Returns the filepath of the built pkg.       |

## Usage

```yaml
...
    steps:
      - uses: actions/checkout@v2
      - name: Munkpkg
        id: munkipkg
        uses: joncrain/munkipkg@main
```

## Examples

```yaml
...
    steps:
      - uses: actions/checkout@v2
      - name: Get branch names
```

### Possible usage with [softprops/action-gh-release](https://github.com/softprops/action-gh-release):

```yaml
on:
  workflow_dispatch: # manually triggered

jobs:
  munkipkg:
    runs-on: macos-latest
    timeout-minutes: 5 # Keeps your builds from running too long
    steps:
      - name: Checkout (this repo of a munkipkg project)
        uses: actions/checkout@25a956c84d5dd820d28caab9f86b8d183aeeff3d # Pin SHA1 hash instead of version
        with:
          lfs: true

      - name: Run munkipkg
        id: munkipkg
        uses: joncrain/munkipkg-action@main

      - name: Create Release
        uses: softprops/action-gh-release@affa18ef97bc9db20076945705aba8c516139abd
        with:
          files: ${{ steps.munkipkg.outputs.filepath }}
          tag_name: ${{ steps.munkipkg.outputs.tag }}
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

* Free software: [MIT license](LICENSE)
