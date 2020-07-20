# TreasureMap
Scripts to send DECam pointings to TreasureMap.

## Quick Start
You'll need the start and end date of the observations, the Prop-ID, and the name of the event in GraceDB.
For S190814bv, the script would be used like this:

```
$ cd /data/des41.a/data/desgw_public/TreasureMap
$ source setup_tm.sh
$ python query_tm.py --outfile pointings.csv --start 20190813 --end 20190905 --propid 2019B-0372
$ python submit_tm.py --infile pointings.csv --graceid S190814bv --preview
```

That's it! 
Below I go into details about how to setup the directory structure if you want to be listed as an author.
In a real-time follow-up, though, the above four commands are all you need.

## Detailed Instructions

### Environment set up
This repository exists on the Fermilab DES cluster. Start by ssh-ing to the DES machines. 
The following commands will get you to the right place and set up your environment:

- `cd /data/des41.a/data/desgw_public/TreasureMap`
- `source setup_tm.sh`

### Getting listed as a DESGW Author
When we submit pointings to TreasureMap, a DOI is automatically generated.
To get your name added to the DOI, it must be listed in the `authors.yaml`.
If your information is missing from this file, contact Rob Morgan or Mandeep Gill.

**Important:** If you plan on submitting pointings yourself, do `touch api_tokens/<FNAL_services_username>/<Full_Name>.name`. 
This will make it possible for your name to be listed as the first author on the DOI.
`<Full_Name>` must match exactly what you put in the `authors.yaml` file, with the exceptions that all space characters need to be replaced with underscores.
If you do not have a FNAL services username, you can skip this step.

### Getting a TreasureMap API token (required only if you plan on submitting pointings yourself)
You'll need this to tag the pointings with your name in the TreasureMap API.
If you decide not to do this step, the script will default to M. Gill's API token and list M. Gill as the first author on the DOI request.

1. Head to [http://treasuremap.space/](http://treasuremap.space/)
1. Click "Register" and follow the prompts
1. Verify your email address with the confirmation email you just received
1. Once logged-in to the TreasureMap website, click "Profile"
1. Copy your API token (I'll use <API_token> to represent it in these instructions)

Next you'll head back to the TreasureMap directory on the DES machines. 
Perform the following commands:

- `mkdir api_tokens/<FNAL_services_username>`
- `touch api_tokens/<FNAL_services_username>/<API_token>.api_token`

## Usage
Now we're ready to run the scripts.
You will need the following information:

- The GraceDB ID of the GW alert (e.g. S190814bv, S200224ca)
- The Prop-ID of the DECam observations
- The start and end dates of the DECam observations

_Note:_ I recommend adding one day of buffer on either side of the start and end dates to make sure the DB query doesn't miss any exposures.

### Step 1: Compile the DECam pointings
Choose a file name for a csv file that will contain the results of querying the SISPI database.
For this example, I'll call it `S190814bv_pointings.csv`.

`python query_tm.py --outfile S190814bv_pointings.csv --start 20190813 --end 20190905 --propid 2019B-0372`

Take a look at the outfile to make sure nothing looks ridiculous.
It will be in the the current working directory.

### Step 2: Send the pointings to TreasureMap
When you're ready, send the pointings to TreasureMap with the command:

`python submit_tm.py --infile S190814bv_pointings.csv --graceid S190814bv --preview`

The `--preview` argument will print the json pointings to the screen before submitting them and allow you to approve or reject them.
You can omit it to automatically approve all pointings.

There is also a `--test` argument, and if specified the scirpt will do everything except send the pointings to TreasureMap. 

### Step 3: That's it!
You're done. 
Information about the run of these scripts and all the json files generated from the pointings are in the `logs` directory.
Look for the subdirectorry of `logs` with the timestamp of when you started the `submit.py` script.
