# Cloudflare Rules

## Cache Rules

参考自：
> [CloudFlare Cache Rules （缓存规则）教程｜LinuxDo](https://linux.do/t/topic/126107)

缓存条件：

```text
(http.request.uri.path.extension in {"7z" "csv" "gif" "midi" "png" "tif" "zip" "avi" "doc" "gz" "mkv" "ppt" "tiff" "zst" "avif" "docx" "ico" "mp3" "pptx" "ttf" "apk" "dmg" "iso" "mp4" "ps" "webm" "bin" "ejs" "jar" "ogg" "rar" "webp" "bmp" "eot" "jpg" "otf" "svg" "woff" "bz2" "eps" "jpeg" "pdf" "svgz" "woff2" "class" "exe" "js" "pict" "swf" "xls" "css" "flac" "mid" "pls" "tar" "xlsx" "rtf" "txt" "md" "tar.gz" "tar.bz2" "tar.xz" "xz" "psd" "ai" "mov" "wmv" "wav" "aac" "m4a" "obj" "stl" "json" "xml" "yml" "yaml" "bat" "sh" "py" "m3u8" "ts"})
```



## WAF Rules

<暂时不写了>

