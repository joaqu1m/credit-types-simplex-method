# Phased Implementation Simplex Method

## Overview
This project demonstrates how to model and solve "project phased implementation" as a Linear Programming (LP) problem using the Simplex Method/Algorithm, which is a powerful technique for solving linear optimization problems.

## Problem Description
Project phased implementation involves determining the optimal way to implement project phases while considering various constraints such as resources, budget, and time. By formulating this as a linear programming problem, we can find the optimal solution that maximizes benefits or minimizes costs.

## Project Structure
- `notebook/`: Contains Jupyter notebooks with different solver implementations
  - `graphic-solver.ipynb`: Visual representation of the solution process
  - `sliders-solver.ipynb`: Interactive solver with slider controls
  - `solver.ipynb`: Core implementation of the Simplex Method
- `resources/`: Supporting data files
  - `solver-data.xlsx`: Excel file with problem data
  - `old-expl.txt`: Additional explanatory notes

## Development Environment
This project uses a DevContainer for consistent development experience. The DevContainer:
- Includes pre-installed Python 3 and pip
- Comes with Python language extensions for enhanced development
- Ensures all dependencies are available in the container environment
- Allows for easy setup across different machines and operating systems

### Running the DevContainer
1. Install Docker and VS Code with the "Remote - Containers" extension
2. Open the project folder in VS Code
3. Click the green icon in the bottom-left corner (or press F1 and search for "Remote-Containers")
4. Select "Reopen in Container"
5. Wait for the container to build and start (first time may take a few minutes)
6. Start coding with all dependencies ready to use!
