# ZMK Board Iris CE

[ZMK](https://zmk.dev) Board definition for the [Iris CE](https://keeb.io/products/iris-ce-keyboard).

## DISCLAIMER
This is not associated with or supported by Keebio. Using this may affect your warranty.

## ZMK/Zephyr Patchs
ZMK currently doesn't support split wired keyboards. The [wired split over serial support PR](https://github.com/zmkfirmware/zmk/pull/2080) is required to build this.

ZMK currently does not support WS2812 for RP2040 boards. Support has been added upstream in Zephyr 3.6, so these changes need to be backported to the ZMK tracked version..
Commits [6699d4d4f931f5f686bae65da5f89589395d86ba](https://github.com/zephyrproject-rtos/zephyr/commit/6699d4d4f931f5f686bae65da5f89589395d86ba) and [0f458c9564d76e9e077230dc321cbddb1e801cc9](https://github.com/zephyrproject-rtos/zephyr/commit/0f458c9564d76e9e077230dc321cbddb1e801cc9) are required.

## Feature Support
LEDs are supported but they do not synchronise between halfs. Additionally, as the `split-serial-pr` currently only supports
communication in one direction, changing RGB settings will only update the central (left) half of the keyboard.
If you want to use LED's it is best to set the desired settings in `iris_ce.conf` in your `zmk-config`.


### Settings
To allow storing settings in flash enabled `CONFIG_SETTINGS=y`. The board definition is configured for storing settings but
it is disabled by default.

## Building
### Locally
Currently you will need to run patched versions of [ZMK + Zephyr](https://github.com/paulshir/zmk/tree/split-serial-pr%2Bpio-led).

1. Clone this board repository locally.
2. Follow the [building and flashing instructions from ZMK](https://zmk.dev/docs/development/build-flash)
3. For the build command, specify this board repository as an extra module when building.

Example usage
```
# After setting up zmk
git clone https://github.com/paulshir/zmk-board-iris-ce
cd zmk/app
git remote add paulshir http://github.com/paulshir/zmk
git checkout paulshir split-serial-pr+pio-led
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
      revision: split-serial-pr+pio-led
      import: app/west.yml
    - name: zmk-board-iris-ce
      remote: paulshir
      revision: main
  self:
    path: config
```
