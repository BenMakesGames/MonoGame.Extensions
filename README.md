## What Is It?

BenMakesGames.MonoGame.Extensions contains extension methods for MonoGame's Texture2D and Color classes aimed at texture manipulation. This is a new package, and so the set of methods is still quite small.

* nuget package: https://www.nuget.org/packages/BenMakesGames.MonoGame.Extensions
* GitHub repo: https://github.com/BenMakesGames/MonoGame.Extensions

[![Buy Me a Coffee at ko-fi.com](https://raw.githubusercontent.com/BenMakesGames/AssetsForNuGet/main/buymeacoffee.png)](https://ko-fi.com/A0A12KQ16)

---

## Color Extensions

### `double EuclideanDistance(this Color a, Color b)`

Extension method for `Color` that returns the "Euclidean distance" to another `Color`.

"Euclidean distance" means that the R, G, B components are treated as X, Y, Z coordinates, and the distance is the length of the straight line between those coordinates for `Color a` and `Color b`. Computing this distance is relatively fast, but does not always yield the most visually-pleasing results.

Example usage:

```c#
// compute the Euclidean distance between Goldenrod and SeaGreen:
var distance = Color.Goldenrod.EuclideanDistance(Color.SeaGreen);
```

### `Color FindNearestColor(this Color color, IList<Color> palette, Func<Color, Color, double> distanceFunction)`

Returns the `Color` in the `palette` which is closest to the given color according to the `distanceFunction`.

This method can be used to reduce a full-color image to a lower color count. Example:

```c#
public static readonly Color[] BlackAndWhitePalette = new[] { Color.White, Color.Black };

public static Texture2D MakeBlackAndWhite(Texture2D originalTexture)
{
    return originalTexture.CloneWithFilter(color => color
        .FindNearestColor(BlackAndWhitePalette, ColorExtensions.EuclideanDistance)
    );
}
```

### `(Color Min, Color Max) GetMinMax(this IEnumerable<Color> colors)`

Returns the minimum and maximum RGB values found within a given collection of colors.

Example usage:

```c#
var colors = new Color[] { Color.Red, Color.Blue, Color.Green };
var (min, max) = colors.GetMinMax();

Console.WriteLine($"Min: {min.R}, {min.G}, {min.B}");
Console.WriteLine($"Max: {max.R}, {max.G}, {max.B}");
```

### `Color IncreaseBrightness(this Color color, double brightness)`

Returns a new color which is a brightened (or darkened) version of the original color. This method uses a simple linear adjustment based on RGB values, so does not always yield the most natural results. You may get better results by converting to HSL, increasing luminosity (L), then converting back to RGB (not currently possible with this library).

Example usage:

```c#
var lighterGoldenrod = Color.Goldenrod.IncreaseBrightness(0.5); // 50% increase
var darkerGoldenrod = Color.Goldenrod.IncreaseBrightness(-0.5); // 50% decrease
```

### `Color IncreaseContrast(this Color color, double contrast)`

Returns a new color which is a more (or less) saturated version of the original color. This method uses a simple linear adjustment based on RGB values, so does not always yield the most natural results. You may get better results by converting to HSL, increasing saturation (S), then converting back to RGB (not currently possible with this library).

Example usage:

```c#
var strongerGoldenrod = Color.Goldenrod.IncreaseContrast(0.5); // 50% increase
var mutedGoldenrod = Color.Goldenrod.IncreaseContrast(-0.5); // 50% decrease
```

## Texture2D Extensions

### `Texture2D Clone(this Texture2D originalTexture)`

Returns a clone of the original texture.

Example usage:

```c#
Texture2D newTexture = oldTexture.Clone();

// modifying newTexture will not affect oldTexture
```

### `Texture2D CloneWithFilter(this Texture2D originalTexture, Func<Color, Color> filter)`

Returns a new texture which is a copy of the old texture, with some filter applied to every pixel.

```c#
var desaturatedImage = originalTexture.CloneWithFilter(c => c.IncreaseContrast(-1));
```
