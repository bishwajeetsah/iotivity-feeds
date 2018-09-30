# iotivity-feeds

OpenWRT feed for Iotivity 2.0.0


# Patches

1. No Unit Tests
2. Do not download and configure google unit test
3. Do not run svr2cbor tool for json files. Copy the existing .dat files.

# Known bugs

1. Need to copy liboc.so and liboc_logger.so from build directory to /usr/lib directory of openwrt target staging directory when the linking of libresource_directory.so fails with the following error:
  "ld: warning: liboc.so, needed by libresource_directory.so, not found (try using -rpath or -rpath-link)
  ld: warning: liboc_logger.so, needed by libresource_directory.so, not found (try using -rpath or -rpath-link)"
