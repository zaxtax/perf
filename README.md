perf
====

Perf calculates a variety of performance metrics suitable for boolean classification problems.
Fork of [KDD-CUP](http://osmot.cs.cornell.edu/kddcup/software.html) software

Metrics include: accuracy, root-mean-squared-error, cross-entropy, precision, recall, precision/recall break-even point and F-score, area under the ROC curve, lift, weighted cost, top 1, top 10, rank of lowest positive case, q-score, several measures of probability calibration, etc.

Perf can read from the standard input, or from files containing target values and predicted values. When reading from the standard input, the input to perf is a series of lines, each of which contains a target value and predicted value separated by whitespace. Perf reads the entire input corresponding to the targets and predictions for a train or test set, and then calculates the performance measures you request. Here is a short example of the input file. The first column is the target class (0 or 1). The second column is the probabilities the model predicts the case is in class 1. The input format allows any kind of whitespace to separate the two columns (e.g. spaces, tabs, commas). 

1 0.80962
0 0.48458
1 0.65812
0 0.16117
0 0.47375
0 0.26587
1 0.71517
1 0.63866
0 0.36296
1 0.89639
0 0.35936
0 0.22413
0 0.36402
1 0.41459
1 0.83148
0 0.23271

If you specify no options, perf prints a variety of performance measures. Typically you will specify options so that perf only calculates the performance metric(s) you are interested in, but here is sample output of perf when run on one of the test data sets included in the perf_sample_test_data directory with no options specified: 

[caruana] perf < testperfdata
ACC 0.83292 pred_thresh 0.500000
PPV 0.35294 pred_thresh 0.500000
NPV 0.96203 pred_thresh 0.500000
SEN 0.71429 pred_thresh 0.500000
SPC 0.84680 pred_thresh 0.500000
PRE 0.35294 pred_thresh 0.500000
REC 0.71429 pred_thresh 0.500000
PRF 0.47244 pred_thresh 0.500000
LFT 3.36975 pred_thresh 0.500000
SAR 0.78902 pred_thresh 0.500000 wacc 1.000000 wroc 1.000000 wrms 1.000000 

ACC 0.90524 freq_thresh 0.617802
PPV 0.54762 freq_thresh 0.617802
NPV 0.94708 freq_thresh 0.617802
SEN 0.54762 freq_thresh 0.617802
SPC 0.94708 freq_thresh 0.617802
PRE 0.54762 freq_thresh 0.617802
REC 0.54762 freq_thresh 0.617802
PRF 0.54762 freq_thresh 0.617802
LFT 5.22846 freq_thresh 0.617802
SAR 0.81313 freq_thresh 0.617802 wacc 1.000000 wroc 1.000000 wrms 1.000000 

ACC 0.91521 max_acc_thresh 0.712250
PPV 0.68182 max_acc_thresh 0.712250
NPV 0.92876 max_acc_thresh 0.712250
SEN 0.35714 max_acc_thresh 0.712250
SPC 0.98050 max_acc_thresh 0.712250
PRE 0.68182 max_acc_thresh 0.712250
REC 0.35714 max_acc_thresh 0.712250
PRF 0.46875 max_acc_thresh 0.712250
LFT 6.50974 max_acc_thresh 0.712250
SAR 0.81645 max_acc_thresh 0.712250 wacc 1.000000 wroc 1.000000 wrms 1.000000 

PRB 0.54762
APR 0.51425
ROC 0.88380
R50 0.49954
RKL 273
TOP1 1.00000
TOP10 1.00000
SLQ 0.80851 Bin_Width 0.010000
RMS 0.34966
CXE 0.57335
CA1 0.22115 19_0.05_bins
CA2 0.22962 Bin_Size 100


To make the output simpler, you can specify only the measure(s) you are interested in. For example, to compute just the ROC Area or just the average precision: 

[caruana] perf -roc < testperfdata
ROC 0.88380
[caruana] perf -apr < testperfdata
APR 0.51425

To compute the accuracy, cross-entropy, and root-mean-squared-error: 

[caruana] perf -acc -cxe -rms < testperfdata
ACC 0.83292 pred_thresh 0.500000
RMS 0.34966
CXE 0.57335

Note that accuracy needed a threshold and perf used a default threshold of 0.5. If you want to use a different threshold (e.g. a threshold of 0 when using SVMs), the threshold can be specified with a "-threshold" option: 

[caruana] perf -acc -threshold 0.0 -cxe -rms < testperfdata
ACC 0.10474 pred_thresh 0.000000
RMS 0.34966
CXE 0.57335

Note that the threshold changed only the accuracy, but not the RMS or CXE. Predictions below threshold are treated as class 0 and predictions above threshold are treated as class 1. When submitting predictions for the KDD-CUP for accuracy (the only performance measure we are using in the cup that depends on a threshold) you will be asked to submit a threshold as well.

Perf can read from files instead of from the standard input: 

[caruana] perf -acc -threshold 0.0 -cxe -rms -file testperfdata
ACC 0.10474 pred_thresh 0.000000
RMS 0.34966
CXE 0.57335

Note that the file option must be the last option specified.

Perf has a variety of other options not described here. Perf can plot ROC curves and precision-recall plots, automatically select thresholds that maximize accuracy or make the frequency of the cases predicted to be positive match the number of cases that are positive in the data set (both of these should be used to find thresholds on train or validation sets, and then you should specify that threshold with the "-threshold" option when testing on test sets -- finding optimal thresholds directly on test sets usually is a no-no), display confusion matrices, calculate cost when unequal costs apply to false positives and false negatives, etc. A tutorial on perf is currently being prepared, but you really won't need for the KDD-CUP. To see a list of perf's options, run perf with an illegal option such as "-help": 

[caruana] perf -help

Error: Unrecognized program option -help
Version 5.00 [KDDCUP-2004]

Usage:
./perf [options] < input 
OR ./perf [options] -file <input file> 
OR ./perf [options] -files <targets file> <predictions file> 

Allowed options:

PERFORMANCE MEASURES
-ACC Accuracy
-RMS Root Mean Squared Error
-CXE Mean Cross-Entropy
-ROC ROC area [default, if nothing else selected]
-R50 ROC area up to 50 negative examples
-SEN Sensitivity
-SPC Specificity
-NPV Negative Predictive Value
-PPV Positive Predictive Value
-PRE Precision
-REC Recall
-PRF F1 score
-PRB Precision/Recall Break Even Point
-APR Mean Average Precision
-LFT Lift (at threshold)
-TOP1 Top 1: is the top ranked case positive
-TOP10 Top 10: is there a positive in the top 10 ranked cases
-NTOP <N> How many positives in the top N ranked cases
-RKL Rank of *last* (poorest ranked) positive case
-NRM <arg> Norm error using L<arg> metric
-CST <tp> <fn> <fp> <tn>
Total cost using these cost values, plus min-cost results
-SAR <wACC> <wROC> <wRMS><br> SAR = (wACC*ACC + wROC*ROC + wRMS(1-RMS))/(wACC+wROC+wRMS)
typically wACC = wROC = wRMS = 1.0
-CAL <bin_size> CA1/CA2 scores
-SLQ <bin_width> Slac Q-score

METRIC GROUPS
-all display most metrics (the default if no options are specified)
-easy ROC, ACC and RMS
-stats Accuracy, confusion table metrics, lift
-confusion Confusion table plus all metrics in stat

PLOTS (Only one plot is drawn at a time)
-plot roc Draw ROC plot
-plor pr Draw Precision/Recall plot
-plot lift Draw Lift versus threshold plot
-plot cost Draw Cost versus threshold plot
-plot acc Draw Accuracy versus threshold plot

PARAMETERS
-t <arg> Set threshold [default threshold is 0.5 if not set]
-percent <arg> Set threshold so <arg> percent of data falls above threshold (predicted positive)

INPUT
-blocks Input has BLOCK ID numbers in first column. Calculate performance for each block and
report the mean performance across the blocks. Only works with APR, TOP1, RKL, and RMS.
If using separate files for target and predictions input (-file option), the BLOCK ID numbers
must be the first column of the target file, with no block numbers in the predictions file.

-file <file> Read input from one file (1st col targets, 2nd col predictions)
-files <targets file> <predictions file> Read input from two separate files
