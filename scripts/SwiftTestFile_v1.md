---
  title: 'SwiftTestFile.swift'
  description: 'component description'
  pubDate: 'July 29, 2024'
  author: 'Carlos P'
  ---
  
  
  
  # SwiftTestFile.swift
  ### Code Explanation

This code snippet is written in Swift, a programming language commonly used for iOS and macOS development.

#### Functions:
1. `calculateBMI(weight: Double, height: Double) -> Double`: This function calculates the Body Mass Index (BMI) using the formula `weight / (height * height)`. It takes two parameters, `weight` (in kilograms) and `height` (in meters), and returns a `Double` value representing the BMI.

2. `getHealthRecommendation(bmi: Double) -> String`: This function provides a health recommendation based on the calculated BMI. It uses a `switch` statement to determine the BMI range and returns a corresponding recommendation message. The BMI ranges and recommendations are:
   - Less than 18.5: "Underweight - You should eat more nutritious food."
   - Between 18.5 and 24.9: "Normal weight - Keep up the good work!"
   - Between 25 and 29.9: "Overweight - Consider a balanced diet and exercise."
   - 30 and above: "Obesity - Consult a healthcare provider."

#### Usage:
1. The code initializes `weight` as 70.0 (in kilograms) and `height` as 1.75 (in meters).
2. It then calculates the BMI by calling `calculateBMI(weight: weight, height: height)` and stores the result in the `bmi` variable.
3. The `getHealthRecommendation(bmi: bmi)` function is called with the calculated BMI to get a health recommendation, which is stored in the `recommendation` variable.
4. Finally, the BMI value and the corresponding recommendation are printed to the console.

#### Example Usage:
```swift
let weight = 70.0  // weight in kilograms
let height = 1.75  // height in meters

let bmi = calculateBMI(weight: weight, height: height)
let recommendation = getHealthRecommendation(bmi: bmi)

print("BMI: \(bmi)")
print("Recommendation: \(recommendation)")
```

#### External Libraries or Dependencies:
There are no external libraries or dependencies required for this code snippet as it uses only the standard Swift Foundation framework.
  
  ## Component Code
  ```jsx
  ///jsx
  import Foundation

func calculateBMI(weight: Double, height: Double) -> Double {
    return weight / (height * height)
}

func getHealthRecommendation(bmi: Double) -> String {
    switch bmi {
    case ..<18.5:
        return "Underweight - You should eat more nutritious food."
    case 18.5..<24.9:
        return "Normal weight - Keep up the good work!"
    case 25..<29.9:
        return "Overweight - Consider a balanced diet and exercise."
    default:
        return "Obesity - Consult a healthcare provider."
    }
}

let weight = 70.0  // weight in kilograms
let height = 1.75  // height in meters

let bmi = calculateBMI(weight: weight, height: height)
let recommendation = getHealthRecommendation(bmi: bmi)

print("BMI: \(bmi)")
print("Recommendation: \(recommendation)")
  ///
  ```
  