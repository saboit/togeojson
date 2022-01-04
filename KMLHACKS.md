## Hacks made to prevent crashes with very large KMLs

With very large KMLs (>0.5GB), the original `JSON.stringify` on whole GeoJSON object runs out of memory, despite naive countermeasures like NodeJS cmdline option `max-old-space-size`.

Only the [commandline wrapper](./togeojson) was modified to minimize the diff, even if it means that the GeoJSON toplevel object creation [fc()](./togeojson.js#L75) is ignored and recreated as string concatenation in the commandline wrapper.

1. GeoJSON is stringified and written to output stream incrementally, per each _Feature_. The total number of features is reported when logging is enabled (see below).

2. number of stringify indentation whitespaces is configurable via new cmdline option `-s` and **defaults to 0** (no identation). _This change alone already fixed the OOM for some "smaller" files_.

3. The output can be not only stdout but also explicit filename via new cmdline option `-o`.

4. the terrific one-liner in `convert()` function was broken down to enable conversion progress logging

If `-o` is given, stdout is used for `console.log`-ging the conversion progress with elapsed time in seconds. If `-o` is not given, stdout is used for GeoJSON output and no conversion progress is reported (original behavior).

