# Localize
Keep iOS Localization files sane


## Why
Because **Localization** files tend to **rot over time** and become a hassle to work with. **Stressful** when you have to test your app against many different Localizations.

## How
By using a **script** running automatically, you have a **safety net** keeping them **sane**, checking for **translation issues** and preventing then to **rot over time.**

## What

Automatically (On build)
  - **Cleans** you localization files (removes comments and spaces)
  - **Sorts** keys Alphabetically
  - Checks for **Unused Keys**
  - Checks for **Missing Keys**
  - Checks for **Untranslated** (which you can ignore with a flag)

## Usage

- Add the following `Run Script` in XCode, this will run the script at every build.

```shell
# 1 Folder containing your xx.lproj folders
LOCALIZATIONS_FOLDER="$PROJECT_DIR/{ REPLACE ME 1}"
## Ex : LOCALIZATIONS_FOLDER="$PROJECT_DIR/Resources/Languages"

find $LOCALIZATIONS_FOLDER -name "*.strings" -exec sed -i '' '/^\/\// d' {} \;
find $LOCALIZATIONS_FOLDER -name "*.strings" -exec sed -i '' '/^\/\*/ d' {} \;
find $LOCALIZATIONS_FOLDER -name "*.strings" -exec sed -i '' '/^$/d' {} \;
find $LOCALIZATIONS_FOLDER -name "*.strings" -exec sort -f {} -o {} \;

# 2 Path to where you copied Localize.swift script
${SRCROOT}/{REPLACE ME}}
# Ex: ${SRCROOT}/Libs/Localize

```

- Add the path of your localization files `1`
- Add The path of Localize.swift script `2`

- Copy Localize.swift script in your project.
- Modify `relativeLocalizableFolders`


Configure "never used" keys.

Run and Enjoy \o/

## More

### Ignore Same Translation warnings
Just Add `//ignore-same-translation-warning` next to the translation.
Example :
```
"PhotoKey" = "Photo"; //ignore-same-translation-warning
```
This will take care of ignoring `[Potentialy Untranslated] "XXX" in FR file doesn't seem to be localized`

## Unused false positive
The script parses your project sources and checks if your keys are called within `NSLocalizedString` calls.
But chances are you have a helper for a shorter NSLocalizedString syntax.
This is indeed supported but you have to give the script what to look for.

You can modify the script to include other ways of localizations:

```swift
let patterns = [
    "NSLocalizedString\\(@?\"(\\w+)\"", // Swift and Objc Native
    "Localizations\\.((?:[A-Z]{1}[a-z]*[A-z]*)*(?:\\.[A-Z]{1}[a-z]*[A-z]*)*)", // Laurine Calls
    //Add your own matching regex here
    "fsLocalized\\(@?\"(\\w+)\""
]
```


## TODO
- `ignoredFromUnusedKeys` find a way to pass that as a param?
- // Removes default top comments not cool
- Full Swift, do not use Sed & Awk to sort, clean etc.
- emit nonzero exit code when failing
`Command /bin/sh emitted errors but did not return a nonzero exit code to indicate failure`
- multilanguage ? en, fr es are hardcodes at the moment
