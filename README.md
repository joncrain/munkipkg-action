# munkipkg-action

_This action utilizes Greg Neagle's excellent pkg building tool [munkipkg](https://github.com/munki/munki-pkg)_

MunkiPkg action will create a build artifact in the build directory `build/*.pkg` and will output the version of the pkg being built.

* _Note: Must be run on a `macos` runner._

## Inputs

|      Input       |        type         |                                            Example/Default                                            |                             Description                             |
| :--------------: | :-----------------: | :---------------------------------------------------------------------------------------------------: | :-----------------------------------------------------------------: |
| munkipkg_version | `string` (optional) | `https://raw.githubusercontent.com/munki/munki-pkg/8d68abbab4c459857d28fdd84ad668ec6ccdf98a/munkipkg` | Location of munkipkg script. This will be downloaded by the action. |
| pkg_subdir       | `string` (optional) | `""`/`"subfolder"`                                                                                     | Location of folder to pkg. Defaults to the root of the repo         |

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
        uses: joncrain/munkipkg@v1.0
```

### Possible usage with [apple-actions/import-codesign-cert](https://github.com/Apple-Actions/import-codesign-certs) and [softprops/action-gh-release](https://github.com/softprops/action-gh-release):

If your MunkiPkg build file includes a certificate, you can store the certificate and password in a GitHub secret and use them in the build process by using the [apple-actions/import-codesign-cert](https://github.com/Apple-Actions/import-codesign-certs) action in your workflow. You can also create a release based on the version from the build file.

```yaml
on:
  workflow_dispatch: # manually triggered

jobs:
  munkipkg:
    runs-on: macos-latest
    timeout-minutes: 5 # Keeps your builds from running too long
    steps:
      - name: Checkout (this repo of a munkipkg project)
        uses: actions/checkout@v2
        with:
          lfs: true

      - name: Import Signing Cert
        uses: apple-actions/import-codesign-certs@v1
        with:
          p12-file-base64: ${{ secrets.CERTIFICATES_P12 }}
          p12-password: ${{ secrets.CERTIFICATES_P12_PASSWORD }}

      - name: Run munkipkg
        id: munkipkg
        uses: joncrain/munkipkg-action@v1.0

      - name: Create Release
        uses: softprops/action-gh-release@affa18ef97bc9db20076945705aba8c516139abd
        with:
          files: ${{ steps.munkipkg.outputs.filepath }}
          tag_name: ${{ steps.munkipkg.outputs.tag }}
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

* Free software: [MIT license](LICENSE)
