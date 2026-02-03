# Renamed Methods Reference

This document lists C++ methods that have been renamed in the JavaScript/TypeScript bindings.
Renaming is necessary when C++ has overloaded methods (same name, different parameters),
since JavaScript does not support function overloading.

## Naming Convention

Overloaded methods are renamed with a suffix indicating parameter types:
- `_D` = double
- `_L` = long
- `_LL` = long long
- `_I` = int
- `_B` = bool
- `_F` = float
- Class names are abbreviated (e.g., `EllipticalObjectElements` becomes the suffix)

**Example:** `CAAElliptical::Calculate(double, Object, bool)` → `Calculate_DOB`

---

## Renamed Methods by Class

### `CAADate`

| JavaScript Name | Original C++ Name | Parameters | Return Type |
|-----------------|-------------------|------------|-------------|
| `AfterPapalReform_LLD` | `AfterPapalReform` | `long Year, long Month, double Day` | `bool` |
| `AfterPapalReform_D` | `AfterPapalReform` | `double JD` | `bool` |

### `CAAELP2000`

| JavaScript Name | Original C++ Name | Parameters | Return Type |
|-----------------|-------------------|------------|-------------|
| `EclipticLongitude_D` | `EclipticLongitude` | `double JD` | `double` |
| `EclipticLatitude_D` | `EclipticLatitude` | `double JD` | `double` |
| `RadiusVector_D` | `RadiusVector` | `double JD` | `double` |
| `MoonMeanMeanLongitude_D` | `MoonMeanMeanLongitude` | `double JD` | `double` |
| `MeanLongitudeLunarPerigee_D` | `MeanLongitudeLunarPerigee` | `double JD` | `double` |
| `MeanLongitudeLunarAscendingNode_D` | `MeanLongitudeLunarAscendingNode` | `double JD` | `double` |
| `MeanHeliocentricMeanLongitudeEarthMoonBarycentre_D` | `MeanHeliocentricMeanLongitudeEarthMoonBarycentre` | `double JD` | `double` |
| `MeanLongitudeOfPerihelionOfEarthMoonBarycentre_D` | `MeanLongitudeOfPerihelionOfEarthMoonBarycentre` | `double JD` | `double` |
| `MoonMeanSolarElongation_D` | `MoonMeanSolarElongation` | `double JD` | `double` |
| `SunMeanAnomaly_D` | `SunMeanAnomaly` | `double JD` | `double` |
| `MoonMeanAnomaly_D` | `MoonMeanAnomaly` | `double JD` | `double` |
| `MoonMeanArgumentOfLatitude_D` | `MoonMeanArgumentOfLatitude` | `double JD` | `double` |

### `CAAElliptical`

| JavaScript Name | Original C++ Name | Parameters | Return Type |
|-----------------|-------------------|------------|-------------|
| `Calculate_DOB` | `Calculate` | `double JD, Object object, bool bHighPrecision` | `CAAEllipticalPlanetaryDetails` |
| `Calculate_DEllipticalObjectElementsBD` | `Calculate` | `double JD, const CAAEllipticalObjectElements & elements, bool bHighPrecision, double epsilon` | `CAAEllipticalObjectDetails` |

### `CAAIlluminatedFraction`

| JavaScript Name | Original C++ Name | Parameters | Return Type |
|-----------------|-------------------|------------|-------------|
| `PhaseAngle_DDD` | `PhaseAngle` | `double r, double R, double Delta` | `double` |
| `PhaseAngle_DDDDDD` | `PhaseAngle` | `double R, double R0, double B, double L, double L0, double Delta` | `double` |
| `IlluminatedFraction_D` | `IlluminatedFraction` | `double PhaseAngle` | `double` |
| `IlluminatedFraction_DDD` | `IlluminatedFraction` | `double r, double R, double Delta` | `double` |

### `CAAInterpolate`

| JavaScript Name | Original C++ Name | Parameters | Return Type |
|-----------------|-------------------|------------|-------------|
| `Interpolate_DDDD` | `Interpolate` | `double n, double Y1, double Y2, double Y3` | `double` |
| `Interpolate_DDDDDD` | `Interpolate` | `double n, double Y1, double Y2, double Y3, double Y4, double Y5` | `double` |
| `Zero_DDDD` | `Zero` | `double Y1, double Y2, double Y3, double epsilon` | `double` |
| `Zero_DDDDDD` | `Zero` | `double Y1, double Y2, double Y3, double Y4, double Y5, double epsilon` | `double` |
| `Zero2_DDDD` | `Zero2` | `double Y1, double Y2, double Y3, double epsilon` | `double` |
| `Zero2_DDDDDD` | `Zero2` | `double Y1, double Y2, double Y3, double Y4, double Y5, double epsilon` | `double` |

### `CAANodes`

| JavaScript Name | Original C++ Name | Parameters | Return Type |
|-----------------|-------------------|------------|-------------|
| `PassageThroAscendingNode_EllipticalObjectElements` | `PassageThroAscendingNode` | `const CAAEllipticalObjectElements & elements` | `CAANodeObjectDetails` |
| `PassageThroDescendingNode_EllipticalObjectElements` | `PassageThroDescendingNode` | `const CAAEllipticalObjectElements & elements` | `CAANodeObjectDetails` |
| `PassageThroAscendingNode_ParabolicObjectElements` | `PassageThroAscendingNode` | `const CAAParabolicObjectElements & elements` | `CAANodeObjectDetails` |
| `PassageThroDescendingNode_ParabolicObjectElements` | `PassageThroDescendingNode` | `const CAAParabolicObjectElements & elements` | `CAANodeObjectDetails` |

### `CAAStellarMagnitudes`

| JavaScript Name | Original C++ Name | Parameters | Return Type |
|-----------------|-------------------|------------|-------------|
| `CombinedMagnitude_DD` | `CombinedMagnitude` | `double m1, double m2` | `double` |

---

## Enum Types

Some methods use C++ enum types. These are exposed with the following names:

| TypeScript Type | C++ Type | Values |
|-----------------|----------|--------|
| `CAAElliptical_Object` | `CAAElliptical::Object` | SUN, MERCURY, VENUS, MARS, JUPITER, SATURN, URANUS, NEPTUNE |
| `CAAPlanetaryPhenomena_Planet` | `CAAPlanetaryPhenomena::Planet` | MERCURY, VENUS, MARS, JUPITER, SATURN, URANUS, NEPTUNE |
| `CAAPlanetaryPhenomena_Type` | `CAAPlanetaryPhenomena::Type` | INFERIOR_CONJUNCTION, SUPERIOR_CONJUNCTION, OPPOSITION, CONJUNCTION, EASTERN_ELONGATION, WESTERN_ELONGATION, STATION1, STATION2 |
| `CAARiseTransitSet2_Object` | `CAARiseTransitSet2::Object` | SUN, MOON, MERCURY, VENUS, MARS, JUPITER, SATURN, URANUS, NEPTUNE |
| `CAAPlanetPerihelionAphelion2_Object` | `CAAPlanetPerihelionAphelion2::Object` | MERCURY, VENUS, EARTH, MARS, JUPITER, SATURN, URANUS, NEPTUNE |

### Usage Example

```javascript
// Use enum values like this:
const marsEnum = Module.CAAElliptical_Object.MARS;
const marsDetails = Module.CAAElliptical.Calculate_DOB(jd, marsEnum, true);
```
