# Walker2d Multi-Output Regression with TOPSIS Model Selection

## Problem

Predict next state of Walker2d robot given current state and action.

- Input: 23 features (17 observations + 6 actions)
- Output: 17 features (next state observations)

## Data

### Collection

- Environment: Gymnasium Walker2d-v5
- Episodes: 50
- Samples: 1002
- Method: Random action sampling

### Features

**Observations (17)**
- rootz, rooty: torso position and angle
- thigh_joint, leg_joint, foot_joint: right leg angles
- thigh_left_joint, leg_left_joint, foot_left_joint: left leg angles
- rootx_vel, rootz_vel: torso velocities
- rooty_angvel: torso angular velocity
- joint angular velocities (6 joints)

**Actions (6)**
- Torque values [-1, 1] for 6 joints: thigh, leg, foot (both sides)

### Split

- Train: 80% (801 samples)
- Test: 20% (201 samples)
- Random state: 316

## Models

### Trained

1. **Random Forest**: 100 trees, max_depth=10
2. **Gradient Boosting**: 100 estimators, max_depth=6
3. **Ridge**: alpha=1.0

All wrapped with MultiOutputRegressor except Ridge (native support).

## Evaluation

### Metrics

- RMSE: error magnitude
- R2: variance explained
- InfTime: inference time per sample (seconds)

### TOPSIS Selection

Weights: [0.4, 0.4, 0.2] for [RMSE, R2, InfTime]
Impact: ['-', '+', '-'] (minimize, maximize, minimize)

## Results

| Model | RMSE | R2 | InfTime (s) | Score | Rank |
|-------|------|----|-------------|-------|------|
| GradBst | 1.2578 | 0.9459 | 0.000119 | 0.9536 | 1 |
| Ridge | 1.6562 | 0.9147 | 0.000000 | 0.7524 | 2 |
| RandFor | 1.2774 | 0.9405 | 0.002436 | 0.2380 | 3 |

### Analysis

Gradient Boosting won with best accuracy (lowest RMSE: 1.26, highest R2: 0.946) and fast inference (0.12ms). Ridge second with fastest inference but 3% lower R2. Random Forest last due to 20x slower inference despite good accuracy.

## Files

- data_gen_and_modelling.ipynb: main code
- data.csv: generated dataset
- mdl_cmp.csv: model comparison

## Dependencies

see requirements.txt

## Notes

TOPSIS ranked models balancing accuracy and inference speed. Gradient Boosting selected as best overall performer for deployment scenarios requiring both accuracy and fast prediction.
