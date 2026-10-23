# Models

## baseline_fraud_mlp

The classifier the lab defends: a PyTorch multilayer perceptron (48 inputs, then 64, 32, 2 units, ReLU) trained on the `train` partition of `datasets/ieee_fraud_sample.csv.gz` with a class-weighted cross-entropy loss.

| File | Contents |
| --- | --- |
| `baseline_fraud_mlp.pt` | PyTorch `state_dict` (weights only) |
| `baseline_fraud_mlp.json` | Model card: architecture, feature order, preprocessing (imputation medians, scaler mean and scale), training settings, test metrics, and the SHA-256 hash of the `.pt` file |

The notebook verifies the SHA-256 hash against the model card before loading, and loads the weights with `torch.load(..., weights_only=True)`.

### Features

The model card's `features` list is the authoritative feature order. It is built from the raw sample by:

1. replacing `TransactionAmt` with `log_amount` (`log1p`),
2. filling missing numeric values with the medians recorded in `impute_medians`,
3. encoding `DeviceType` as the single binary column `device_mobile`,
4. one-hot encoding `ProductCD`, `card4`, `card6`, and `P_emaildomain`,
5. standardising with the scaler statistics recorded in the card.

Constant columns are dropped when the feature set is defined, so every column in the card carries signal.

Because the features are standardised, a perturbation budget expressed in feature units is a budget in standard deviations. The lab's `EPS = 0.1` means no feature may move by more than one tenth of its standard deviation.

### Retraining

The lab retrains this architecture from scratch on an augmented dataset, reusing the hyperparameters recorded in the card's `training` block (seed 178, 30 epochs, batch size 256, learning rate 0.001, fraud class weight 5.0). Keeping them fixed means any change in measured behaviour is attributable to the training data rather than to a different model.
