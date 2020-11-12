This h60 net was trained after the major refactoring of the code, which enabled
easy experimentation with different network topologies, different input
features, and a new unified serialization scheme.

The h60 topology is the same as that of the legacy netv3, except it has 60
hidden units instead of 30.  It uses the same input features.

It was trained by self-play only, and every million games a non-training playoff
was run against the legacy drc.w net.  It was very surprising that it quickly
surpassed the performance of the legacy net.  See the plot.  It ends up about
+0.08 per game over drc.w.

The csv file on which the plot is based further divides the games into singles,
gammons, and backgammons.  Generally, this net seems to break about even on
single games compared to drc.w, but gets a lot more gammons, and backgammons,
while relatively rare, are significantly skewed to it too.

In other experiments, we were able to train a legacy netv3 net to about +0.02
over drc.w using a similar training schedule.  The increased equity was due to
more gammons and backgammons, but in that case it was at the expense of singles.
But the h60 net achieved superior performance quite early, initially trading off
singles for gammons and backgammons too, but eventually figuring out how to keep
up with singles too.

It seems to have leveled off around 200 or 300 million games.  We let the
training continue on past what is shown here, at a reduced learning rate, and
observed no increase in performance.

We did not make any systematic effort to find the best hyperparameters, instead
using the range of values we found worked well for netv3.  See the h60.py file
for the training schedule.

We trained a few different nets in this structure up to about 50 million games,
and the trajectories of all were quite similar.  We did not cherry pick this
one, and we believe the training should be quite reproducible.

In other experiments, we tried 15 hidden units instead of 60, and the
performance relative to drc.w was poor.  So an obvious thing to try is to
increase the number of hidden units, maybe to 120, and see what happens.  And
there are vastly different topologies to try also.

The h60 net takes about 45 minutes to train a million games on the Thinkpad T430
laptop.  The netv3 is about twice as fast, so probably an h120 would be about
twice as slow.  It would still be fast enough to get some early results in a few
days.
