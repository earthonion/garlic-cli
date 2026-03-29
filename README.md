# garlic

CLI tool for PS4/PS5 save management via the [garlicsaves.com](https://garlicsaves.com) API.

Decrypt, encrypt, resign, and re-region PS4/PS5 saves from the command line. Auto-detects PS4 vs PS5 saves from file headers.

## Install

```bash
# Clone and add to PATH
git clone https://github.com/earthonion/garlic-cli.git
ln -s $(pwd)/garlic-cli/garlic ~/.local/bin/garlic

# Or just download the script
curl -o garlic https://raw.githubusercontent.com/earthonion/garlic-cli/main/garlic
chmod +x garlic
```

No dependencies — pure Python 3 stdlib.

## Setup

Get an API key from [garlicsaves.com/contribute](https://garlicsaves.com/contribute), then:

```bash
garlic config --key YOUR_API_KEY
```

Or set the `GARLIC_API_KEY` environment variable, or pass `-K KEY` to any command.

## Usage

### Decrypt

```bash
# PS4 save (requires sealed key)
garlic d SAVEFILE -k SAVEFILE.bin

# Auto-finds .bin if same name exists in directory
garlic d SAVEFILE

# PS5 save (no sealed key needed)
garlic d sdimg_savefile

# Exclude sce_sys from output
garlic d SAVEFILE -k SAVEFILE.bin --no-sce-sys

# Custom output path
garlic d SAVEFILE -k SAVEFILE.bin -o decrypted.zip
```

### Encrypt

```bash
# Encrypt a decrypted save folder (must contain sce_sys/param.sfo)
garlic e ./my_save/ -a 0000000000000000

# PS5 save
garlic e ./my_save/ -a 0000000000000000 -p ps5
```

### Resign

```bash
# Re-sign to a different account
garlic r SAVEFILE -k SAVEFILE.bin -a 1234567890abcdef
```

### Re-region

```bash
# Re-region using a sample save's keystone
garlic rr TARGET -k TARGET.bin -s SAMPLE -a 0000000000000000 --sample-key SAMPLE.bin
```

### Other Commands

```bash
# Check job status
garlic s JOB_ID

# Download a completed job's result
garlic dl JOB_ID -o result.zip

# List recent jobs
garlic ls

# Show config
garlic config --show
```

## Batch Decrypt

```bash
# Decrypt all saves from a PS4 USB backup
for dir in /media/usb/PS4/SAVEDATA/*/; do
  title=$(basename "$dir")
  for bin in "$dir"*.bin; do
    [ -f "$bin" ] || continue
    save="${bin%.bin}"
    [ -f "$save" ] || continue
    garlic d "$save" -k "$bin" -o "decrypted/${title}_$(basename $save).zip"
  done
done
```

## Config

Config is stored at `~/.config/garlic/config.json`. API key can also be set via the `GARLIC_API_KEY` environment variable or the `-K` flag.

## License

GPL-3.0 — see [LICENSE](LICENSE).
