# ZMK Board Iris CE

[ZMK](https://zmk.dev) Board definition for the [Iris CE](https://keeb.io/products/iris-ce-keyboard).

## DISCLAIMER
This is not associated with or supported by Keebio. Using this may affect your warranty.

## Zephyr Patchs
ZMK added wired split support in v0.3.

For the WS2812 LED's, this board definition is using the PIO driver for RP2040 boards. Support has been added upstream in Zephyr 3.6, so these changes need to be backported to the ZMK tracked version..
Commits [6699d4d4f931f5f686bae65da5f89589395d86ba](https://github.com/zephyrproject-rtos/zephyr/commit/6699d4d4f931f5f686bae65da5f89589395d86ba) and [0f458c9564d76e9e077230dc321cbddb1e801cc9](https://github.com/zephyrproject-rtos/zephyr/commit/0f458c9564d76e9e077230dc321cbddb1e801cc9) are required
as they are not in ZMK yet.

## Building
### Locally
Currently you will need to run patched versions of [ZMK + Zephyr](https://github.com/paulshir/zmk/tree/v0.3-branch+pio-led).

1. Clone this board repository locally.
2. Follow the [building and flashing instructions from ZMK](https://zmk.dev/docs/development/build-flash)
3. For the build command, specify this board repository as an extra module when building.

Example usage
```
# After setting up zmk
git clone https://github.com/paulshir/zmk-board-iris-ce
cd zmk/app
git remote add paulshir http://github.com/paulshir/zmk
git checkout paulshir v0.3-branch+pio-led
west update

west build -p -d build/iris_ce_left -b iris_ce_rev1_left -- -DZMK_EXTRA_MODULES=/path/to/zmk-board-iris-ce/
# Connect left half and press reset button underneath keyboard
west flash -d build/iris_ce_left

west build -p -d build/iris_ce_right -b iris_ce_rev1_right -- -DZMK_EXTRA_MODULES=/path/to/zmk-board-iris-ce/
# Connect left half and press reset button underneath keyboard
west flash -d build/iris_ce_right
```

### In ZMK Config
```
manifest:
  defaults:
    revision: v0.3
  remotes:
    - name: zmkfirmware
      url-base: https://github.com/zmkfirmware
    - name: paulshir
      url-base: https://github.com/paulshir
  projects:
    # - name: zmk
    #   remote: zmkfirmware
    #   revision: main
    #   import: app/west.yml
    - name: zmk
      remote: paulshir
      import: app/west.yml
    - name: zmk-board-iris-ce
      remote: paulshir
  self:
    path: config
```

## ZMK Studio
This board definition has support for ZMK Studio. See https://zmk.dev/docs/features/studio for building with ZMK Studio enabled.
When connecting to ZMK studio, you need to press a key bound to `@studio_unlock`. On the default keymap this has been added and is 
accessed via the right shift button on the raise layer. This is located at the `CLEAR MEM` button shown on the Raise layer in 
the [Keeb.io default Iris keymap](https://docs.keeb.io/assets/files/Iris%20Default%20Keymap-6d56a01516f12c3cc9727515d32d49a2.pdf).

ZMK Studio requires the number of layers to be defined in the code. Two extra empty layers have been added to the default keymap 
so it has a total of 5 layers to work with.

To build for studio, your `build.yaml` needs to be updated [as documented here](https://zmk.dev/docs/features/studio). For
example, add the following definition

```
  - board: iris_ce_rev1_left
    snippet: studio-rpc-usb-uart
    cmake-args: -DCONFIG_ZMK_STUDIO=y
    artifact-name: iris_ce_rev1_left_studio
```

There are build artifacts with studio support [autobuilt here](https://github.com/paulshir/zmk-board-iris-ce/actions/workflows/build.yml?query=is%3Acompleted+branch%3Amain)

