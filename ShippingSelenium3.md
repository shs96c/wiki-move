# User Visible Changes

## Migrate all drivers to use the status strings rather than status codes in responses

## Update client bindings to also cope with that

## Write a new runner for the html-suite tests

## Segment the build to remove RC

# Clean up

## Using WebDriver after quit() should be an IllegalStateException

## Actions to have a single end point

## Capabilities to be the same as the spec

Multiple calls to WebDriver.quit() should still be safe.

## Clean up WebDriver constructors, pulling heavy initialization logic into a Builder class

## Migrate to Netty or webbit server

## Delete unnecessary cruft

## Land a cleaner end point for the rc emulation