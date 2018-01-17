# Convert audible .aax files to usable format on a Mac


## First we need to get all our deps and then our Audible authcode as outlined bellow

> These steps where found here: https://apple.stackexchange.com/questions/222917/how-do-i-use-ffmpeg-to-convert-audible-files/243670#243670

### Run these commands in your terminal

```bash
brew install chromedriver ffmpeg
sudo easy_install pip
pip install selenium requests
git clone https://github.com/inAudible-NG/audible-activator
cd audible-activator
sed -i '' 's,chromedriver_path = "./chromedriver",chromedriver_path = "/usr/local/bin/chromedriver",' audible-activator.py
./audible-activator.py
```

### Then enter your Audible username and password and wait for a while for the eight character activation key to be printed

> Save your key. Maybe in a .authcode file where your doing all this??


## Then we will grab this fancy JS script @r15ch13 thoughtfully created for us

> [npm package url here](https://github.com/r15ch13/audible-converter)

```bash
npm -g install audible-converter
```


## Now you can run the script with your authcode to convert your files

```bash
audible-converter *.aax -a YOUR_AUTH_CODE
```

## If you need to convert a bunch of files at once and want to store the audio and cover img in a single directory heres a bash function I created

> It requires you to have **coreutils** from brew installed though

```bash
brew install coreutils
```

```bash
__convert_aax_file() {
  # realpath comes from brew coreutils package
  FULL_FILE_PATH=$(realpath $1)

  FILE="${FULL_FILE_PATH##*/}"

  # bash regex i don't really get for parsing title from audible file names.
  re="^([^_]+)_(.*)$"
  [[ $FILE =~ $re ]] && TITLE="${BASH_REMATCH[1]}"

  mkdir -p ~/Documents/audiobooks

  FULL_OUTPUT_PATH=$(realpath  ~/Documents/audiobooks)

  # dir path for new files
  STASH_PATH="$FULL_OUTPUT_PATH/$TITLE"

  mkdir -p $STASH_PATH

  audible-converter $1 -a 589b8805 -p $STASH_PATH
}
# convert audible files
# Deps: brew coreutils
# usage:
# > caf some_directory/**.aax
# output path is ~/Documents/audiobooks
caf() {
  for file in $@
  do
    __convert_aax_file $file
  done
}
```


### If you want to convert files without the npm script

After that run a command like this:

ffmpeg -activation_bytes youractivationkey -i input.aax -vn -c:a copy output.m4a;mv output.m4{a,b}
If you try to use the ffmpeg command with an aa file instead of an aax file, it results in an error like Option activation_bytes not found. To download a book from Audible as aax instead of aa, choose "Enhanced" from the "Audio Quality" dropdown in the view for downloading a book.

aax files are encrypted mp4 / m4a / m4b files so the ffmpeg command does not re-encode audio and it preserves metadata such as chapters. mp4, m4a, and m4b are alternative filename extensions for the MPEG-4 Part 14 container format. One difference between the m4a and m4b extensions is that the option to remember the previous playback position in iTunes is enabled by default for files with an m4b extension but not for files with an m4a extension. iTunes also displays files with an m4b extension under audiobooks by default.


## Anti-Piracy Notice

Note that this project does NOT ‘crack’ the DRM. It simply allows the user to use their own encryption key (fetched from Audible servers) to decrypt the audiobook in the same manner that the official audiobook playing software does.

Please only use this application for gaining full access to your own audiobooks for archiving/conversion/convenience. DeDRMed audiobooks should not be uploaded to open servers, torrents, or other methods of mass distribution.
