# nicobot

🤟 A collection of *cool* chat bots 🤟

It features :

- Participating in [Signal](https://www.signal.org/fr/) conversations
- Using [IBM Watson™ Language Translator](https://cloud.ibm.com/apidocs/language-translator) cloud API


## Requirements & installation

Requires :

- Python 3
- [signal-cli](https://github.com/AsamK/signal-cli) (for the *Signal* backend)
- An IBM Cloud account ([free account ok](https://www.ibm.com/cloud/free))

Install Python dependencies with :

    pip3 install -r requirements.txt

See below for Signal requirements.


## Transbot

*Transbot* is a chatbot interface to IBM Watson™ Language Translator service that translates messages.
Whenever it sees a keyword in a conversation, *transbot* will translate the whole message into a random language.

### Quick start

1. [Create a *Language Translator* service instance on IBM Cloud](https://cloud.ibm.com/catalog/services/language-translator) and [get the URL and API key from your console](https://cloud.ibm.com/resources?groups=resource-instance)
2. Fill them into `test/sample-conf/config.yml` (`ibmcloud_url` and `ibmcloud_apikey`)
3. Run `python3 nicobot/transbot.py -C test/sample-conf`
4. Input `Hello world` in the console : the bot will print a random translation of "Hello World"
5. Input `Bye nicobot` : the bot will terminate

If you want to send & receive messages through *Signal* instead of reading from the keyboard & printing to the console :

1. Install and configure `signal-cli` (see below for details)
2. Run `python3 nicobot/transbot.py -C test/sample-conf -b signal -u '+33123456789' -r '+34987654321'` with `-u +33123456789` your *Signal* number and `-r +33987654321` the one of the person you want to make the bot chat with

See below for more options...


### Main configuration options and files

Run `transbot.py -h` to get a description of all options.
A sample configuration is available in the `test/sample-conf/` directory.

Below are the most important configuration options :

- **--config-file** and **--config-dir** let you change the default configuration directory and file. All configuration files will be looked up from this directory ; `--config-file` allows overriding the location of `config.yml`.
- **--keyword** and **--keywords-file** will help you generate the list of keywords that will trigger the bot. To do this, run `transbot.py --keyword <a_keyword> --keyword <another_keyword> ...` a **first time with** : this will download all known translations for these keywords and save them into a `keywords.json` file. Next time you run the bot, **don't** use the `--keyword` option : it will reuse this saved keywords list. You can use `--keywords-file` to change the default name.
- **--language**, **--languages-file** : you should not need to use these options unless you only want to translate into a given set of languages. The first time the bot runs, it will download the list of supported languages into `languages.json` and reuse it afterwards (or the file indicated with `--languages-file`).
- **--ibmcloud-url** and **--ibmcloud-apikey** can be obtained from your IBM Cloud account ([create a Language Translator instance](https://cloud.ibm.com/apidocs/language-translator) then go to [the resource list](https://cloud.ibm.com/resources?groups=resource-instance))
- **--backend** selects the *chatter* system to use : it currently supports "console" and "signal" (see below)
- **--username** selects the account to use to send and read message ; its format depends on the backend
- **--recipient** and **--group** select the recipient (only one of them should be given) ; its format depends on the backend

The **i18n.\<locale>.yml** file contains localization strings for your locale and fun :
- *Transbot* will say "Hello" when started and "Goodbye" before shutting down : you can configure those banners in this file.
- It also defines the message pattern that terminates the bot.

Finally, see the following chapter about the **config.yml** file.


### Config.yml configuration file

Options can also be taken from a configuration file : by default it reads the `config.yml` file in the current directory but can be changed with the `--config-file` and `--config-dir` options.
This file is in YAML format with all options at the root level. Keys have the same name as command line options, with middle dashes `-` replaced with underscores `_`.

E.g. `--ibmcloud-url https://api...` will become `ibmcloud_url: https://api...`.

A sample configuration is available in the `test/sample-conf/` directory.

Please first review [YAML syntax](https://yaml.org/spec/1.1/#id857168) if you don't know about YAML.


## Using the Signal backend

By using `--backend signal` you can make the bot chat with Signal users.

### Prerequiste

You must first [install and configure *signal-cli*](https://github.com/AsamK/signal-cli#installation).

Then you must [*register* or *link*](https://github.com/AsamK/signal-cli/blob/master/man/signal-cli.1.adoc) the computer when the bot will run ; e.g. :

    signal-cli link --name MyComputer

### Parameters

With signal, make sure :

- the `--username` parameter is your phone number in international format (e.g. `+33123456789`). In `config.yml`, make sure to put quotes around it to prevent YAML thinking it's an integer (because of the 'plus' sign)
- specify either `--recipient` as an international phone number or `--group` with a base 64 group ID (e.g. `--group "mABCDNVoEFGz0YeZM1234Q=="`). Once registered with Signal, you can list the IDs of the groups you are in with `signal-cli -u +336123456789 listGroups`

Sample command line to run the bot with Signal :

    python3 nicobot/transbot.py -b signal -u +33612345678 -g "mABCDNVoEFGz0YeZM1234Q==" --ibmcloud-url https://api.eu-de.language-translator.watson.cloud.ibm.com/instances/a234567f-4321-abcd-efgh-1234abcd7890 --ibmcloud-apikey "f5sAznhrKQyvBFFaZbtF60m5tzLbqWhyALQawBg5TjRI"



## Resources

### Python libraries

- [xmpppy](https://github.com/xmpppy/xmpppy) : this library is very easy to use but it does allow easy access to thread or timestamp
- [https://lab.louiz.org/poezio/slixmpp](slixmpp) : seems like a cool library too and pretends to require minimal dependencies ; however the quick start example does not work OOTB...
- https://github.com/horazont/aioxmpp : the official library, seems the most complete but misses practical introduction

None of them seems to support OMEMO out of the box :-(

### IBM Cloud

- [Language Translator service](https://cloud.ibm.com/catalog/services/language-translator)
- [Language Translator API documentation](https://cloud.ibm.com/apidocs/language-translator)

### Signal

- [Signal home](https://signal.org/)
- [signal-cli man page](https://github.com/AsamK/signal-cli/blob/master/man/signal-cli.1.adoc)
