# Hands on: relational data model

## 1. A customer wants

As a customer I want to track my daily meals.
Each meal has to include the name of the meal, the date and time when the meal was eaten and all the ingredients which have been used to prepare the meal.
Each ingredient has a name, a quantity and a unit.

As a customer I want to capture my personal well-being any time. It has to be possible to save information about any thing which describes my wealth (e.g. headache, sniffles, rash, ...) and information about where those symptomes occure on the body. In addition a general status has to be defined which describes the well-being on a scale from 1 (bad) to 3 (good).

As a customer I want to track when I took my medicine. I want to track the date and time when I took the medicine, which type of medicine and the amount.

I want to be able to gather information about meals and well-beings within a given data range.
I want to be able to gather information about a specific well-being status and the list of meals until the last well-being status.

## 1.1 Identify principal data objects

Entities: meal, ingredient

## 1.2 Find relationships

|            | meal | ingredient    |
| ---------- | ---- | ------------- |
| meal       | -    | 1,<br><br>1:n |
| ingredient | -    | -             |

One meal can have one or multiple ingredients.
One ingredient can belong to exactly one meal.

## 1.3 Select attributes

Meal: name, datetime, location
Ingredient: name, quantity, unit, category
