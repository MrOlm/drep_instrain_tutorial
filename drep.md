# dRep Tutorial
In this tutorial we are going to dereplicate the set of ocean genomes downloaded during the [setup](README.md) steps.

Check the dRep readthedocs for more information: https://drep.readthedocs.io/en/latest/

## Step 1) Verify everything is set up correctly

dRep relies on a number of different dependencies to run correctly. Before running dRep it's always best to make sure everything is installed correctly using the following command:

```
$ dRep check_dependencies
```

You should see an output like so:

```
mash.................................... all good        (location = /var/lib/miniconda3/envs/ebame/bin/mash)
nucmer.................................. all good        (location = /var/lib/miniconda3/envs/ebame/bin/nucmer)
checkm.................................. !!! ERROR !!!   (location = None)
ANIcalculator........................... !!! ERROR !!!   (location = None)
prodigal................................ all good        (location = /var/lib/miniconda3/envs/ebame/bin/prodigal)
centrifuge.............................. !!! ERROR !!!   (location = None)
nsimscan................................ !!! ERROR !!!   (location = None)
fastANI................................. all good        (location = /var/lib/miniconda3/envs/ebame/bin/fastANI)
```

As you can see, the programs `checkm`, `ANIcalculator`, `centrifuge`, and `nsimscan` are not properly installed, but the rest are working. For our particular run settings this is fine.

## Step 2) Decide on your dereplication parameters

Check the help with:

```
$ dRep dereplicate -h
```

The key parameters are X, Y, and Z. Check the dRep documentation (https://drep.readthedocs.io/en/latest/) for more information on what these mean.


## Step 3) Run dRep

Run dRep using a command like the following. Feel free to adjust the parameters if you'd like to be fancy