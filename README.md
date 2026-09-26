# react-native-body-highlighter

[![npm](https://img.shields.io/npm/v/react-native-body-highlighter.svg)](https://www.npmjs.com/package/react-native-body-highlighter) [![Downloads](https://img.shields.io/npm/dt/react-native-body-highlighter.svg)](https://www.npmjs.com/package/react-native-body-highlighter)

> SVG human body parts highlighter for react-native (Expo compatible).

> [!NOTE]
> This is my fork of [HichamELBSI/react-native-body-highlighter](https://github.com/HichamELBSI/react-native-body-highlighter) (I'm Elijah Chang, [@elijdh](https://github.com/elijdh)). I split several muscle groups into finer regions (chest, deltoids, lats, abductors) for workout tracking in my fitness app, [FE2](https://fe2.nivis.ca/). See [What's changed in this fork](#whats-changed-in-this-fork) and [Migrating from the original package](#migrating-from-the-original-package).

<div style="text-align:center;width:100%;">
  <img src="./docs/screenshots/example-female-front.PNG" width="150" alt="body-highlighter" />
  <img src="./docs/screenshots/example-female-back.PNG" width="150" alt="body-highlighter" />
  <img src="./docs/screenshots/example-male-front.PNG" width="150" alt="body-highlighter" />
  <img src="./docs/screenshots/example-male-back.PNG" width="150" alt="body-highlighter" />
</div>

<sub>Screenshots are from the original library and don't show this fork's split muscle regions.</sub>

## Installation

The changes in this fork are not published to npm. Install it straight from GitHub (the compiled `dist/` folder is committed):

```bash
$ npm install github:elijdh/react-native-body-highlighter
# or
$ yarn add github:elijdh/react-native-body-highlighter
```

To use the original package from npm instead:

npm

```bash
$ npm install react-native-body-highlighter
```

yarn

```bash
$ yarn add react-native-body-highlighter
```

## Usage

### Basic example

```jsx
import { StyleSheet, View } from "react-native";
import Body from "react-native-body-highlighter";

export default function App() {
  return (
    <View style={styles.container}>
      <Body
        data={[
          { slug: "chest-mid", intensity: 1, side: "left" },
          { slug: "biceps", intensity: 2 },
        ]}
        gender="female"
        side="front"
        scale={1.7}
        border="#dfdfdf"
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: "#fff",
    alignItems: "center",
    justifyContent: "center",
  },
});
```

<details>
<summary style="font-size:18px; font-weight: bold;">Complete example</summary>
<p>

```tsx
import { StyleSheet, Switch, Text, View } from "react-native";
import { useState } from "react";
import Body, { ExtendedBodyPart } from "react-native-body-highlighter";

export default function App() {
  const [selectedBodyPart, setSelectedBodyPart] = useState<ExtendedBodyPart>({
    slug: "biceps",
    intensity: 2,
    side: "right",
  });
  const [side, setSide] = useState<"back" | "front">("front");
  const [gender, setGender] = useState<"male" | "female">("male");

  const sideSwitch = () =>
    setSide((previousState) => (previousState === "front" ? "back" : "front"));

  const toggleGenderSwitch = () => {
    setGender((previousState) =>
      previousState === "male" ? "female" : "male"
    );
  };

  return (
    <View style={styles.container}>
      <Body
        data={[
          { slug: "chest-mid", intensity: 1, side: "left" },
          { slug: "biceps", intensity: 1 },
          selectedBodyPart,
        ]}
        onBodyPartPress={(e, side) =>
          setSelectedBodyPart({ slug: e.slug, intensity: 2, side })
        }
        gender={gender}
        side={side}
        scale={1.7}
        border="#dfdfdf"
      />
      <View style={styles.switchContainer}>
        <View style={styles.switch}>
          <Text>Side ({side})</Text>
          <Switch onValueChange={sideSwitch} value={side === "front"} />
        </View>
        <View style={styles.switch}>
          <Text>Gender ({gender})</Text>
          <Switch
            onValueChange={toggleGenderSwitch}
            value={gender === "male"}
          />
        </View>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: "#fff",
    alignItems: "center",
    justifyContent: "center",
  },
  switchContainer: {
    flexDirection: "row",
    gap: 30,
  },
  switch: {
    flex: 1,
    alignItems: "center",
    justifyContent: "center",
  },
});
```

</p>
</details>

## Props

| Prop                | Required | Purpose                                                                                                       |
| ------------------- | -------- | ------------------------------------------------------------------------------------------------------------- |
| data                | Yes      | `BodyPartObject[]` - Array of `BodyPartObject` to highlight                                                   |
| onBodyPartPress     | No       | `Func` - `(bodyPart: BodyPartObject, side?: left \| right) => {}` Callback called when a user tap a body part |
| colors              | No       | `string[]` - Defaults to `['#0984e3', '#74b9ff']`                                                             |
| side                | No       | `front \| back` - Defaults to `front`                                                                         |
| gender              | No       | `string` - Can be "male" or "female", Defaults to `male`                                                      |
| scale               | No       | `number` - Defaults to `1`                                                                                    |
| border              | No       | `string` - Defaults to `#dfdfdf` (`none` to hide the border)                                                  |
| disabledParts       | No       | `Slug[]` - Contains array of Slugs to be disabled                                                  |
| hiddenParts         | No       | `Slug[]` - Contains array of Slugs to be hidden (not rendered)                                                  |
| defaultFill         | No       | `string` - Default fill color for body parts. Defaults to `#3f3f3f`                                           |
| defaultStroke       | No       | `string` - Default stroke color for body parts. Defaults to `none`                                            |
| defaultStrokeWidth  | No       | `number` - Default stroke width for body parts. Defaults to `0`                                               |

## BodyPart object model

### Accessibility

Each `<Body />` component and its SVG wrappers (`SvgMaleWrapper`, `SvgFemaleWrapper`) are now accessible to screen readers.

- Default accessibility labels are automatically provided for gender and side (e.g., `"male-body-front"`, `"female-body-back"`).
- This improves overall screen reader compatibility by making the visual body component identifiable.
- Adding accessibility per individual body part would require a deeper refactor of how SVG paths are structured.

- #### BodyPartObject: `{ slug: BodyPartName, color?: colorHexValue, intensity?: IntensityNumber, side?: 'left' | 'right', styles?: BodyPartStyles }`

- #### BodyPartName: Body part name to highlight (See the list of available body parts below)

- #### colorHexValue: The Color of specific body part. Accepts HEX string. E.g `#ff0000ff`

- #### IntensityNumber: Color intensity (if the `colors` property is set: from 1 to `colors.length` + 1. If not, intensity can be 1 or 2)

- #### Side (optional): Can be `left`, `right`. Useful for selecting a single part or a pair (Do not set the side if you need to select the pair)

- #### BodyPartStyles (optional): Custom styling object for individual body parts
  - `fill?: string` - Custom fill color for this specific body part (overrides color and intensity)
  - `stroke?: string` - Custom stroke color for this specific body part
  - `strokeWidth?: number` - Custom stroke width for this specific body part

### Styling Priority
The fill color for each body part is determined in the following priority order (highest to lowest):
1. `styles.fill` - Per-part custom fill style
2. `color` - Per-part color property
3. `intensity` - Color from the `colors` array based on intensity value
4. `defaultFill` - Global default fill color

## List of body parts

| BodyParts      | Side                         |
| -------------- | ---------------------------- |
| trapezius      | Both                         |
| triceps        | Both                         |
| forearm        | Both                         |
| adductors      | Both                         |
| calves         | Both                         |
| hair           | Both                         |
| neck           | Both                         |
| deltoids-side  | Both                         |
| hands          | Both                         |
| feet           | Both                         |
| head           | Both (Front only for female) |
| ankles         | Both (Front only for female) |
| tibialis       | Front                        |
| obliques       | Front                        |
| chest-upper    | Front                        |
| chest-mid      | Front                        |
| chest-lower    | Front                        |
| deltoids-front | Front                        |
| biceps         | Front                        |
| abs            | Front                        |
| quadriceps     | Front                        |
| knees          | Front                        |
| abductors      | Back                         |
| deltoids-rear  | Back                         |
| lats           | Back                         |
| upper-back     | Back                         |
| lower-back     | Back                         |
| hamstring      | Back                         |
| gluteal        | Back                         |

## Migrating from the original package

This fork renames and splits some slugs, so data written for the original package needs updating. TypeScript will flag any slug that no longer exists.

| Old slug     | New slug(s)                                    | Notes                                                                  |
| ------------ | ---------------------------------------------- | ---------------------------------------------------------------------- |
| `chest`      | `chest-upper`, `chest-mid`, `chest-lower`      | Front only. Highlight all three to get the old full chest.             |
| `deltoids`   | `deltoids-front`, `deltoids-side`, `deltoids-rear` | `deltoids-front` is front only, `deltoids-rear` is back only, `deltoids-side` shows on both. |
| `upper-back` | `lats`, `upper-back`                           | `lats` is the large mid-back area. `upper-back` is now only the smaller pieces around the shoulder blade. |
| `hamstring`  | `hamstring`, `abductors`                       | Back only. The outer-thigh pieces are now `abductors`.                 |

```js
// Before
{ slug: "chest", intensity: 2 }

// After: same look as before
{ slug: "chest-upper", intensity: 2 },
{ slug: "chest-mid", intensity: 2 },
{ slug: "chest-lower", intensity: 2 },
```

## What's changed in this fork

I made these changes for my fitness app, [FE2](https://fe2.nivis.ca/). Exercises there are logged against specific muscles. The original body map grouped whole regions together, so an incline bench press and a decline press lit up the same "chest", and a lateral raise looked the same as a rear-delt fly. Splitting the regions lets the app show which part of a muscle group a workout actually trained.

Here's what I changed:

- **Chest split into upper, mid and lower** (`chest-upper`, `chest-mid`, `chest-lower`) on the male and female front, with redrawn paths.
- **Deltoids split into front, side and rear heads** (`deltoids-front`, `deltoids-side`, `deltoids-rear`) on both views and both genders, with redrawn paths.
- **Abductors on both genders.** I extended the `abductors` slug from [@Adutchguy](https://github.com/Adutchguy)'s upstream contribution (female back, #93) to the male back. On both genders the abductor pieces are carved out of `hamstring`, and the slug is now in the TypeScript sources, not just `dist/`.
- **Lats separated from the upper back.** A new `lats` slug on the male and female back, so `upper-back` now covers only the smaller pieces around the shoulder blade.
- I updated the `Slug` type, rebuilt `dist/`, updated the tests and brought this README up to date.

## Credits

- Original library by [@HichamELBSI](https://github.com/HichamELBSI) and its [contributors](https://github.com/HichamELBSI/react-native-body-highlighter/graphs/contributors).
- Abductor slug (female back) by Michael Miller ([@Adutchguy](https://github.com/Adutchguy)).
- The fork's chest, deltoid, lats and abductor separation is my work (Elijah Chang, [@elijdh](https://github.com/elijdh)).

Licensed under [MIT](./LICENSE).
