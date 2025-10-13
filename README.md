# expo-video-thumbref

A community-maintained fork of `expo-video-thumbnails` that provides efficient video thumbnail generation using **SharedRef** for direct use with `expo-image`. This package eliminates the need to cache thumbnail images in your app's file system.

> **Why this fork?** The original `expo-video-thumbnails` package has been [deprecated by the Expo team](https://github.com/expo/expo/pull/35641#issuecomment-2111031234) in favor of `expo-video`. However, the `expo-video` approach has significant limitations that make this fork necessary for many use cases.

## Key Features

- 🚀 **SharedRef Integration** - Generate thumbnails as native image references (no file system caching)
- 🖼️ **Direct expo-image Support** - Use thumbnails directly with `expo-image` component
- ⚡ **Memory Efficient** - No temporary files cluttering your app's storage
- ✅ **Expo 53 & 54 Compatible** - Updated to work with the latest Expo versions
- ✅ **Modern Expo Modules** - Built with `expo-modules-core@^3.0.21`
- ✅ **TypeScript Support** - Full TypeScript definitions included
- ✅ **Cross-Platform** - Works on both iOS and Android

## Installation

### Prerequisites

This package requires:

- **Expo SDK 53 or 54** (or higher)
- `expo` package installed in your project
- `expo-image` package for displaying thumbnails
- Expo Modules configured in your project

### Install the packages

```bash
npm install expo-video-thumbref expo-image
```

## Usage

### Recommended: Using SharedRef with expo-image

The preferred way to use this package is with `getNativeThumbnailAsync`, which returns a `SharedRef` that can be used directly with `expo-image`:

```typescript
import * as VideoThumbnails from 'expo-video-thumbref';
import { Image } from 'expo-image';

// Generate a thumbnail as a native image reference
const thumbnailRef = await VideoThumbnails.getNativeThumbnailAsync(
  'file://path/to/your/video.mp4',
  {
    time: 1000, // Time in milliseconds
    quality: 0.8, // Quality from 0 to 1
  }
);

// Use directly with expo-image (no file system caching!)
if (thumbnailRef) {
  return (
    <Image
      source={thumbnailRef}
      style={{ width: thumbnailRef.width, height: thumbnailRef.height }}
    />
  );
}
```

### Alternative: Traditional file-based approach

If you need a file URI for other use cases:

```typescript
import * as VideoThumbnails from 'expo-video-thumbref';

// Generate a thumbnail and save to file system
const result = await VideoThumbnails.getThumbnailAsync(
  'file://path/to/your/video.mp4',
  {
    time: 1000, // Time in milliseconds
    quality: 0.8, // Quality from 0 to 1
  }
);

console.log('Thumbnail URI:', result.uri);
```

## API Reference

### `getNativeThumbnailAsync(videoSource, options)` ⭐ **Recommended**

Generates a thumbnail as a native image reference that can be used directly with `expo-image`.

**Parameters:**

- `videoSource` (string): URI of the video file
- `options` (object): Configuration options
  - `time` (number): Time position in milliseconds to extract thumbnail from
  - `quality` (number): Quality of the thumbnail (0.0 to 1.0)
  - `headers` (object): HTTP headers for remote video sources

**Returns:**

- Promise that resolves to a `NativeVideoThumbnail` object (extends `SharedRef<'image'>`) with:
  - `width` (number): Width of the thumbnail
  - `height` (number): Height of the thumbnail
  - `requestedTime` (number): The time in seconds at which the thumbnail was requested
  - `actualTime` (number): The time in seconds at which the thumbnail was actually generated (iOS only)

**Benefits:**

- ✅ No file system caching
- ✅ Direct use with `expo-image`
- ✅ Memory efficient
- ✅ Better performance

### `getThumbnailAsync(videoSource, options)`

Generates a thumbnail image and saves it to the file system (traditional approach).

**Parameters:**

- `videoSource` (string): URI of the video file
- `options` (object): Configuration options
  - `time` (number): Time position in milliseconds to extract thumbnail from
  - `quality` (number): Quality of the thumbnail (0.0 to 1.0)
  - `headers` (object): HTTP headers for remote video sources

**Returns:**

- Promise that resolves to an object with:
  - `uri` (string): URI of the generated thumbnail image file
  - `width` (number): Width of the thumbnail
  - `height` (number): Height of the thumbnail

## Migration from expo-video-thumbnails

This package is designed to be a drop-in replacement for `expo-video-thumbnails`. The migration is simple:

### 1. Uninstall the old package

```bash
npm uninstall expo-video-thumbnails
```

### 2. Install this fork and expo-image

```bash
npm install expo-video-thumbref expo-image
```

### 3. Update your imports

```typescript
// Before
import * as VideoThumbnails from 'expo-video-thumbnails';

// After
import * as VideoThumbnails from 'expo-video-thumbref';
```

### 4. Using SharedRef approach

If you don't require the URI from the generated image, and just wish to display an image, consider using, consider using `getNativeThumbnailAsync` instead of `getThumbnailAsync`:

```typescript
// Old approach (still works)
const result = await VideoThumbnails.getThumbnailAsync(videoUri, options);

// New approach (recommended)
const thumbnailRef = await VideoThumbnails.getNativeThumbnailAsync(
  videoUri,
  options
);
// Use directly with expo-image - no file system caching!
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

MIT License - see [LICENSE](LICENSE) file for details.

## Why Not Use expo-video Instead?

The Expo team [deprecated `expo-video-thumbnails`](https://github.com/expo/expo/pull/35641#issuecomment-2111031234) in favor of `expo-video`, but there are significant differences that make this fork necessary:

### expo-video Limitations:

- **Requires VideoPlayer Instance**: `expo-video.generateThumbnailsAsync()` only works with an active VideoPlayer, not standalone video files
- **Complex URI Conversion**: Returns native image references that require `expo-image-manipulator` to convert to usable file URIs
- **More Complex API**: Requires creating a video player, generating thumbnails, then converting references to URIs

### This Package Advantages:

- **Standalone Operation**: Generate thumbnails directly from video URIs without creating video players
- **Direct expo-image Support**: Native image references work directly with `expo-image` components
- **Simpler API**: One function call to get thumbnails ready for display
- **Memory Efficient**: No file system caching or complex conversion steps

## What's Different from the Original?

This fork includes several improvements over the original `expo-video-thumbnails`:

- **SharedRef Integration**: New `getNativeThumbnailAsync` function returns native image references
- **expo-image Compatibility**: Direct use with `expo-image` component without file system caching
- **Memory Efficiency**: No temporary files cluttering your app's storage
- **Updated Dependencies**: Uses `expo-modules-core@^3.0.21` for better compatibility

## Technical Background

This package was created based on the work from [PR #35641](https://github.com/expo/expo/pull/35641) which introduced the `getNativeThumbnailAsync` method to solve cache bloat issues in list components. The PR was closed because `expo-video-thumbnails` was being deprecated, but the functionality it provided was essential for many use cases.

The original issue described in the PR:

> "The current getThumbnailAsync() method saves the generated image in cache and returns a URI of the cached image. If used on a component like a list, that gets re-rendered and mount/unmounted, it causes the cache to bloat and ultimately break the app."

This fork preserves that solution while providing continued support for the deprecated package.

## Acknowledgments

This package is based on the original `expo-video-thumbnails` package by the Expo team and the work from [PR #35641](https://github.com/expo/expo/pull/35641). We thank them for their excellent work and foundation.
