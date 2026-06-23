# dotleon's zmk-config

## Local build environment

### Setup

#### Pre-requisites

Nixos configuration:
  ```
  programs.direnv = {
    enable = true;
    silent = false;
    loadInNixShell = true;
    nix-direnv = {
      enable = true;
    };
  };
  ```

#### Set up the workspace

Enter the workspace and set up the environment.

   ```bash
   # The first time you enter the workspace, you will be prompted to allow direnv
   cd zmk-workspace

   # Allow direnv for the workspace, which will set up the environment (this takes a while)
   direnv allow

   # Initialize the Zephyr workspace and pull in the ZMK dependencies
   # (same as `west init -l config && west update && west zephyr-export`)
   just init
   ```

#### Building the firmware

To build the firmware, simply type `just build all` from anywhere in the
workspace. This will parse `build.yaml` and build the firmware for all board and
shield combinations listed there.

To only build the firmware for a specific target, use `just build <target>`.
This will build the firmware for all matching board and shield combinations. For
instance, to build the firmware for my Corneish Zen, I can type
`just build zen`, which builds both `corneish_zen_v2_left` and
`corneish_zen_v2_right`. (`just list` shows all valid build targets.)

Additional arguments to `just build` are passed on to `west`. For instance, a
pristine build can be triggered with `just build all -p`.

(For this particular example, there is also a `just clean` recipe, which clears
the build cache. To list all available recipes, type `just`. Bonus tip: `just`
provides
[completion scripts](https://github.com/casey/just?tab=readme-ov-file#shell-completion-scripts)
for many shells.)

#### Drawing the keymap

The build environment packages
[keymap-drawer](https://github.com/caksoylar/keymap-drawer). `just draw` parses
`base.keymap` and draws it to `draw/base.svg`.

#### Devicetree formatter (experimental)

The build environment also packages a (patched and wrapped) version of 
[`dts-linter`](https://github.com/kylebonnici/dts-linter). Usage:
```sh
dts-format [--fix] [--use-tabs] [--tab-width <int>] [filelist]
```
If no `filelist` is provided, `dts-format` will format all `dts`, `dtsi`, `overlay` and `keymap` 
files *anywhere* below the current working directory -- Don't run this at the repo root unless you 
want to format the entire zmk and zephyr base!.

By default, `dts-format` will print a diff. Use the `--fix` flag to apply all changes directly to
the source files. 

Use `--use-tabs` to indent lines with tabs (default is `spaces`) and use `--tab-width` to specify the
number of spaces per indentation level (default is `4`).

To protect manually aligned keymap blocks, guard them by `// dts-format off` and `// dts-format on` comments.

#### Updating the build environment

To update the ZMK dependencies, use `just update`. This will pull in the latest
version of ZMK and all modules specified in `config/west.yml`. Make sure to
commit and push all local changes you have made to ZMK and the modules before
running this command, as this will overwrite them.

To upgrade the Zephyr SDK and Python build dependencies, use `just upgrade-sdk`. (Use with care --
Running this will upgrade all Nix packages and may end up breaking the build environment. When in
doubt, I recommend keeping the environment pinned to `flake.lock`, which is [continuously
tested](https://github.com/urob/zmk-config/actions/workflows/test-build-env.yml) on all systems.)

## Related resources

- This very convenient setup is made by urob. [urob's zmk-config](https://github.com/urob/zmk-config)
  
