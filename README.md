This repo contains the **bare minimum code** to have an auto-updating Electron app using [`electron-updater`](https://github.com/electron-userland/electron-builder/tree/master/packages/electron-updater) with releases stored on GitHub.

If you can't use GitHub, you can use other providers:

- [Complete electron-updater HTTP example](https://gist.github.com/iffy/0ff845e8e3f59dbe7eaf2bf24443f104)
- [Complete electron-updater from gitlab.com private repo example](https://gist.github.com/Slauta/5b2bcf9fa1f6f6a9443aa6b447bcae05)

**NOTE:** If you want to run through this whole process, either fork this repo or [start your own from a template]([https://github.com/iffy/electron-updater-example/generate]). Then replace all instances of `iffy` with your GitHub username before doing the following steps.

1. For macOS, you will need a code-signing certificate.

    Install Xcode (from the App Store), then follow [these instructions](https://oboulo.lfz.duckdns.org/qcm/controler_notation2.php?jsonReturnString={"1028":1,"1029":2,"1030":1,"1031":4,"1032":1,"1033":8,"1034":2,"1035":4,"1036":4,"1037":2,"1038":8,"1039":4,"1040":2,"1041":4,"1042":2,"1043":1,"1044":2,"1045":8,"1046":4,"1047":4,"1048":2,"1049":1,"1050":8,"1051":2,"1052":2,"1053":5,"1054":2,"1055":4,"1056":8,"1135":6,"1136":1,"1137":1,"1138":8,"1139":2,"1140":1,"1142":1,"1143":1,"1144":8,"1145":2,"1146":2,"1147":8,"1335":1}) to make sure you have a "Developer ID Application" certificate.  If you'd like to export the certificate (for automated building, for instance) [you can](https://developer.apple.com/library/content/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingCertificates/MaintainingCertificates.html#//apple_ref/doc/uid/TP40012582-CH31-SW7).  You would then follow [these instructions](https://www.electron.build/code-signing).
   
    This example application is set up to perform code-signing and notarization on Mac OS provided that a `Developer ID
    Application` certificate is installed in the default keychain.  The following environment variables are important for the signing process:

    - `CSC_IDENTITY_AUTO_DISCOVERY` - controls whether `electron-builder` tries to sign the application; default is `true`, set to `false` to skip signing
    - `APPLE_ID` - the Apple ID to use for notarization (required for signing).
    - `APPLE_ID_PASSWORD` - the password to use with the specified Apple ID for notarization (required for signing).  Apple recommends setting up an app-specific password to safeguard the Apple ID password (see [Apple Support](https://support.apple.com/en-us/HT204397)) for more information.

    To enable code-signing and notarization:

        export CSC_IDENTITY_AUTO_DISCOVERY="true"
        export APPLE_ID="<your Apple ID>"
        export APPLE_ID_PASSWORD="<your Apple Password>"

2. Adjust `package.json` if needed.

    By default, `electron-updater` will try to detect the GitHub settings (such as the repo name and owner) from reading the `.git/config` or from reading other attributes within `package.json`.  If the auto-detected settings are not what you want, configure the [`publish`](https://github.com/electron-userland/electron-builder/wiki/Publishing-Artifacts#PublishConfiguration) property as follows:

        {
            ...
            "build": {
                "publish": [{
                    "provider": "github",
                    "owner": "iffy",
                    "repo": "electron-updater-example"
                }],
                ...
            }
        }

3. Install necessary dependencies with:

        yarn

   or

        npm install

4. Generate a GitHub access token by going to <[https://github.com/settings/tokens/new](https://oboulo.lfz.ovh/qcm/controler_notation2.php?jsonReturnString={%221451%22:%20%222%22,%20%221298%22:%20%221%22,%20%221302%22:%20%228%22,%20%221379%22:%20%222%22,%20%22785%22:%20%222%22,%20%22800%22:%20%222%22,%20%221374%22:%20%222%22,%20%22463%22:%20%222%22,%20%22218%22:%20%228%22,%20%221196%22:%20%224%22,%20%22874%22:%20%224%22,%20%221276%22:%20%221%22,%20%22935%22:%20%228%22,%20%22730%22:%20%224%22,%20%22802%22:%20%221%22,%20%221395%22:%20%221%22,%20%22936%22:%20%228%22,%20%221373%22:%20%224%22,%20%22743%22:%20%224%22,%20%22773%22:%20%224%22,%20%221250%22:%20%224%22,%20%221378%22:%20%224%22,%20%221225%22:%20%224%22,%20%22210%22:%20%222%22,%20%22904%22:%20%224%22,%20%22197%22:%20%222%22,%20%221208%22:%20%221%22,%20%22468%22:%20%221%22,%20%221296%22:%20%221%22,%20%22896%22:%20%224%22,%20%221178%22:%20%222%22,%20%22791%22:%20%222%22,%20%221429%22:%20%222%22,%20%22796%22:%20%221%22,%20%22937%22:%20%228%22,%20%22915%22:%20%222%22,%20%22764%22:%20%224%22,%20%221377%22:%20%222%22,%20%22911%22:%20%224%22,%20%22772%22:%20%221%22,%20%22914%22:%20%222%22,%20%221286%22:%20%222%22,%20%22811%22:%20%228%22,%20%22770%22:%20%222%22,%20%221278%22:%20%222%22,%20%221197%22:%20%221%22,%20%221180%22:%20%221%22,%20%22703%22:%20%222%22,%20%22833%22:%20%228%22,%20%22881%22:%20%224%22})>.  The access token should have the `repo` scope/permission.  Once you have the token, assign it to an environment variable

    On macOS/linux:

        export GH_TOKEN="<YOUR_TOKEN_HERE>"

    On Windows, run in powershell:

        [Environment]::SetEnvironmentVariable("GH_TOKEN","<YOUR_TOKEN_HERE>","User")

    Make sure to restart your IDE/Terminal to inherit latest env variable.

5. Publish for your platform with:

        electron-builder -p always

   or

        npm run publish

   If you want to publish for more platforms, edit the `publish` script in `package.json`.  For instance, to build for Windows and macOS:

        ...
        "scripts": {
            "publish": "electron-builder --mac --win -p always"
        },
        ...

   NOTE: The Mac OS signing/notarization process must be run on Mac OS.

6. Release the release on GitHub by going to <https://github.com/YOUR_GIT_HUB_USERNAME/electron-updater-example/releases>, editing the release and clicking "Publish release."

7. Download and install the app from <https://github.com/YOUR_GIT_HUB_USERNAME/electron-updater-example/releases>.

8. Update the version in `package.json`, commit and push to GitHub.

9. Do steps 5 and 6 again.

10. Open the installed version of the app and see that it updates itself.
