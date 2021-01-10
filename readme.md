This repository contains backgammon nets and their training histories.

The first two, below, are from training sessions a long time ago, and
their training histories are not preserved here.  They originally used
a different file format but they have been converted for inclusion here.

The others provide their training histories here, in subdirectories whose
names correspond to the given network.

In any **.w** network file, the first line says what the network structure
is for that net, and you can consult the source code for details of
the inputs used, the net topology, etc., for that version.

The subdirectory for a given net also contains the script which was
used for training, and by consulting it you can see the schedule of
hyperparameters used.

Finally, the subdirectory should have a log of playoff performance and
perhaps a plot of it too, showing how the performance evolved.

Comments about strength relate to fast play with no lookahead.

* **early.w**: One of the nets from the early 1990s, which trained on about
         15 million games.  It is a competent but fairly weak player,
         and is sometimes useful to compare to other training
         experiments early in the process.

* **drc.w**: The network which was used with the Dr. Chouette GUI in the
       late 1990s.  A fairly strong player, trained about 64 million
       games, which back then took months.  We use this net as a
       common opponent when evaluating a net under training for
       performance.  We do not train against this net, only use it as
       an indicator of relative strength.  (All indications are that
       playing strength is mostly transitive.)  This net used to be
       called "good.w", and you might see that name in the playoff
       histories of some of the nets below.

* **h60-200m-200.w**: A strong player, trained in 2020, 200 million games.

* **h120-200m-200.w**: Currently the best net we have.  Trained as described below.

Experiments
-----------

In these experiments, lambda has been fixed at 0.85, and alpha starts at 0.001
and decays exponentially.  Halfway through each run, alpha has decayed
by a factor of 20, to 5e-5, and by the end of the run alpha has decreased
by a factor of 400, to 2.5e-6.

Each plot shows four differently-sized nets, all using the same
feature set as netv3, but differing in the number of hidden units as
indicated.

Every million training games a snapshot is taken, and a playoff of 100 thousand games
is done against a fixed opponenet, `drc.w`.

The foreground plot has been smoothed by a Savitzky-Golay first-order filter of width 7, and
the actual playoff scores are shown receded.

Alpha decayed by a factor of 20 at 200 million games:

![200 million](img/200m.png)

Alpha decayed by a factor of 20 at 100 million games:

![100 million](img/100m.png)

Alpha decayed by a factor of 20 at 50 million games:

![50 million](img/50m.png)

Given the typical variance of a single game in these nets (measured to
be about 1.7), the standard error for a 100-thousand trial is about
0.004 equity units, a little less than half a percent.  This is broadly
seen in the receded plots, and the smoothed plots are simply an efficient
way to simulate a larger playoff size.

If we increase the playoff games to 1 million, the standard error goes down
to about 0.001 equity units, allowing us to more-or-less believe tenths of
a percent in a playoff result.

Here are some playoff results at this higher 1-million game level:

```
white,black,trials,equity,sw,sl,gw,gl,bw,bl

 h30-200m-200.w,           drc.w, 1000000, 0.0263, 374941, 389909, 119574, 102189, 7776, 5611
 h60-200m-200.w,           drc.w, 1000000, 0.0801, 376778, 375870, 134571, 100320, 8017, 4444
 h90-200m-200.w,           drc.w, 1000000, 0.101,  379074, 366493, 139647, 102016, 8521, 4249
h120-200m-200.w,           drc.w, 1000000, 0.105,  374360, 364899, 144597, 102928, 8653, 4563

# Mostly transitive.
h120-200m-200.w,  h90-200m-200.w, 1000000, 0.0051, 363932, 369848, 131129, 125103, 4822, 5166
 h90-200m-200.w,  h60-200m-200.w, 1000000, 0.0212, 377278, 366260, 125482, 121257, 5154, 4569
h120-200m-200.w,  h60-200m-200.w, 1000000, 0.0277, 372616, 364886, 131111, 121425, 5074, 4888


# It appears that h120-200m-300.w is slightly better than the others:
h120-200m-400.w,           drc.w, 1000000, 0.111,    379693, 364271, 142518, 100772, 8403, 4343
h120-200m-300.w,           drc.w, 1000000, 0.113,    380449, 365342, 141968, 99631,  8487, 4123
h120-200m-400.w, h120-200m-300.w, 1000000, -0.00301, 369556, 368488, 125167, 126750, 4868, 5171

h120-200m-400.w, h120-200m-200.w, 1000000, 0.00516,  368352, 363323, 128854, 129076, 5294, 5101
h120-200m-300.w, h120-200m-200.w, 1000000, 0.00627,  369020, 364703, 128026, 128129, 5421, 4701
```

Plots by Network Size
---------------------

![120](img/h120m.png)

![90](img/h90m.png)

![60](img/h60m.png)

![30](img/h30m.png)

Varying Lambda
--------------

The net `h60-200m-200.w` was trained according to the `200m` alpha schedule and with lambda equal to `0.85`.
Playoffs against nets trained with other fixed values of lambda are as indicated.

```
h60-200m-200.w, h60-200m-50L-200.w,  1000000, 0.00142, 370333, 377440, 122724, 118897, 5449, 5157
h60-200m-200.w, h60-200m-25L-200.w,  1000000, 0.00589, 374059, 375443, 121800, 119148, 5103, 4447
h60-200m-200.w, h60-200m-0L-200.w,   1000000, 0.00604, 373574, 369626, 123779, 122564, 5172, 5285
h60-200m-200.w, h60-200m-100L-200.w, 1000000, 0.0191,  374003, 374033, 125074, 116310, 5559, 5021
```

![h60 lambda](img/h60-200m-lambda.png)

So our historical use of lambda equal to `0.85` is justified at least for this learning schedule,
although it is possible that the results would vary if re-run.

A similar experiment for `h30` nets yields results that are somewhat different.  The value of `0.85` for
lambda is still the best, but the other values are significantly worse.

![h30 lambda](img/h30-200m-lambda.png)

Training against an already-trained net.
---------------------------------------

A randomly-initialized large net is trained against a trained net
which is much smaller.

One net was trained with fixed alpha and lambda as shown.  Then
at 66 thousand games, one path continued under that schedule, and
another exponentially decayed lambda so that at 100 thousand games
the total the value of alpha would be 20 times less, `5e-5`.

For comparison, the reference net of the same size is shown, which
was trained through self-play only.

![cross training](img/cross.png)

```
h120-34m--h30-100.w, ../h120-200m/h120-200m-200.w, 1000000, -0.00413, 366406, 363657, 127569, 131977, 5518, 4873
```