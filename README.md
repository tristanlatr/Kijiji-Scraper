# Kijiji-Scraper 3.0.1
![Build](https://github.com/CRutkowski/Kijiji-Scraper/workflows/Build/badge.svg?branch=master)  

#### Track Kijiji ad information and sends out an email when a new ads are found.

## Install
### Requirements
- **Git** ([Git bash for Windows](https://gitforwindows.org))
- **Python 3** ([install guide](https://realpython.com/installing-python/))

Open terminal or Gitbash.

##### With `pip`
   Run the following to install automatically Kijiji-Scraper on your system.
   ```bash
   pip install git+https://github.com/CRutkowski/Kijiji-Scraper.git
   ```
##### Manually
   ```bash
   git clone https://github.com/CRutkowski/Kijiji-Scraper.git
   cd Kijiji-Scraper
   python3 setup.py install
   ```
**Dependencies: requests, BeautifulSoup and PyYaml**  
Run `pip install requests bs4 pyyaml` to manually install all the dependencies
## Try out
For instance `kijiji --url https://www.kijiji.ca/b-cars-trucks/alberta/tesla-new__used/c174l9003a54a49`

## Configure
The script **must read a configuration file to set mail server settings**. Default config file `config.yalm` is located in `~/.kijiji_scraper/` (MacOS/Linux), `%APPDATA%/.kijiji_scraper` (Windows) or directly in the install folder.
 - **Use `kijiji --init` to create config file and open with default text editor**, set the `sender`, `password` and `receiver` fields in config file.
 - You can specify the Kijji URLs you wish to scrape at the bottom of the config file. There are a few examples in the config to show the syntax.  
 - Alternatively you can use `--url URLs` to configure URLs to scrape and `--email` to set receivers addresses.

**Note**: If you're using gmail, you'll have to go to 'My Account>Sign in & security>Connected apps & sites' then turn "Allow less secure apps" to "On" to allow the script to sign into gmail.

**For development and retro-compatibility** You can also use default `config.yalm` file as the config file in the install folder but you must call `./main.py` directly, not `kijiji` command. 

## Usage
 
 To run the script execute `kijiji` command. You can always run `python3 ./main.py` from install folder.

```
% kijiji --help           
usage: kijiji [-h] [--init] [--conf File path] [--url URL [URL ...]]
               [--email Email [Email ...]] [--skipmail] [--all]
               [--ads File path] [--version]

Kijiji scraper: Track ad informations and sends out an email when a new ads
are found

optional arguments:
  -h, --help            show this help message and exit
  --init, --setup       Create config file if doesn't exist and open with
                        default text editor
  --conf File path, -c File path
                        The script * must read a configuration file to set
                        mail server settings *. Default config file
                        config.yalm is located in ~/.kijiji_scraper/
                        (MacOS/Linux), APPDATA/.kijiji_scraper (Windows) or
                        directly in the install folder.
  --url URL [URL ...], -u URL [URL ...]
                        Kijiji seacrh URLs to scrape
  --email Email [Email ...], -e Email [Email ...]
                        Email recepients
  --skipmail, -s        Do not send emails. This is useful for the first time
                        you scrape a Kijiji as the current ads will be indexed
                        and after removing the flag you will only be sent new
                        ads.
  --all, -a             Consider all ads as new, do not load ads.json file
  --ads File path       Load specific ads JSON file. Default file will be
                        store in the config folder
  --version, -V         Print Kijiji-Scraper version
```

**Note**: The script stores current ads in `ads.json` file located by default in the config folder `~/.kijiji_scraper/` or `%APPDATA%/.kijiji_scraper`. If a `./ads.json` file exist, it will be loaded

## Mail message example
![Mail](/mail.png "Mail")

## How to run the script on set intervals

### Windows:

The windows `Task Scheduler` can be used to have the script run at set intervals.

1. Create a new task
   - Fill in name and description

2. Add a trigger
   - Under `Settings` select `Daily`
   - Set `Repeat task every:` to your desired interval i.e. 5 mins to run the script every 5 mins
   - Set `for a duration of:` to indefinitely
   
3. Add an action
   - Action is Start a program
   - Set Program/script to the location of your python executable i.e. `C:\Users\{username}\AppData\Local\Programs\Python\Python36-32\pythonw.exe` (use pythonw.exe to run quietly, no window)
   - Set Add arguments to `main.py`
   - Set Start in to the location of the main.py file i.e. `C:\Users\{username}\Documents\Scripts\Kijiji-Scraper\`
   
4. Under Settings
   - Enable `Run task as soon as possible after a scheduled start is missed`
   
   
### Linux and MacOS:
Crontab can be used on linux to easily run the script on a set interval.  
To search for new ads every 5mn: 
```
*/5 * * * * kijiji --url "URL1" "URL2" --email me@gmail.com you@gmail.com
```

Alternatively, you can use `screen` and launch continuous execution with `watch` command (Linux and MacOS with Homebrew)
```
screen -dm watch -n 300 kijiji --url "URL1" "URL2" --email me@gmail.com you@gmail.com
```

Use `screen -r`, then Ctrl+C to stop process

## Running several searches configurations

If you want to share your tech skills with your friends, you need to do an extra step, but it's worth it !  

In order to avoid concurrent accesses to ads JSON file and corrupt the file , you'll need to dedicate one file per searches (this WILL happend to you if you don't follow this advise). In the following example, ads matching URL1 will be sent to robert, you and me (because robert is using a different ads file) - same for URL2 and laura. 


```crontab
# Crontab
*/5 * * * * kijiji --email me@gmail.com you@gmail.com --ads ~/our-ads.json --url "URL1" "URL2" 
*/5 * * * * kijiji --email robert@gmail.com --ads ~/roberts-ads.json --url "URL1" 
*/5 * * * * kijiji --email laura@gmail.com --ads ~/lauras-ads.json --url "URL3" "URL2"
```

You might run into crontab command too long error if you have too many URLs. Just create a bash script and run it instead of having the entire command in crontab. 

Exemple
```bash
#! /bin/bash
# /root/Ads/kijiji.sh

kijiji --email me@gmail.com --ads /root/Ads/my-ads.json \
        --url     "URL1" "URL2"

kijiji  --email robert@gmail.com --ads /root/Ads/robert-ads.json \
        --url   "URL1"

kijiji  --email laura@gmail.com --ads /root/Ads/lauras-ads.json \
        --url   "URL3"
```

## See also
[Craiglist scraper](https://github.com/bth5032/CLscraper)