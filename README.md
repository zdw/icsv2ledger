icsv2ledger
===========

This is a command-line utility to convert CSV files of transactions, such as you might download from an online banking service, into the format used by John Wiegley's excellent [Ledger](http://ledger-cli.org) system.

The 'i' stands for _interactive_. Here's what it's designed to do:

* For each .csv file you give it, it creates a .ledger file output (unless you specify an output file, in which case all output goes into the one file).

* As it runs through the entries in the CSV file, it tries to guess which Ledger account and Ledger payee they should be posted against, based on your historical decisions.

* It _shows you_ which account or payee it's going to use, giving you the opportunity to change it.  If it got it right, just hit return.

* When you are entering an account/payee name, you get _auto-completion_ if you press the Tab key.  You don't have to match the _start_ of the name, so on my system, typing 'foo[tab]' inserts 'Expenses:Food'.

* It stores the history in a mapping file, for converting transaction descriptions onto account/payee names. You can also edit this by hand. It can load this in future as the basis of its guesses.  It uses simple string-matching by default, but if you put a '/' at the start and end of a string it will instead be interpreted as a regular expression.

* The account names used in the autocompletion are read both from the mapping file and, optionally, from a Ledger file or files. (It runs 'ledger --format %(account) reg" to get the names')

To use icsv2ledger you need to create a config file.
The default config file is called .icsv2ledger in the current directory.
The following is an example configuration file.

<pre>
[SAV]
account=Assets:Bank:Savings Account
currency=AUD
date=1
date_format=%d-%b-%y
desc=6
credit=2
debit=-1
accounts_map=mappings.SAV
payees_map=payees.SAV
no_header=True

[CHQ]
account=Assets:Bank:Cheque Account
currency=AUD
date=1
date_format=%d/%m/%Y
desc=2
credit=3
debit=4
accounts_map=mappings.CHQ
payees_map=payees.CHQ
no_header=False
</pre>

The configuration file contains one section per bank account you wish to import.
In the above example there are two bank accounts: SAV and CHQ.

Now for each account you need to specify the following.
"account" is the ledger account to post the entries in.
"currency" is the the currency to prepend to each transaction.
If you don't wish to record any currency just leave the value to be blank.
"date" is the column in the CSV file which records the transaction date.
The first column in the CSV file is numbered 1.
"date_format" describes the format of the date.
See the [python documentation](http://docs.python.org/library/datetime.html#strftime-strptime-behavior) for the various format codes supported in this expression.
"desc" is the column containing the transaction description as supplied by the bank.
This is the column that will be used as the input for determing which payee and account to use by the auto-completion.
"credit" is the column which contains credits to the account.
"debit" is the column which contains debits to the account.
If your bank represents debits as negative numbers in the credit column,
than just set "debit" to be "-1" and icsv2ledger will do the right thing.
"accounts_map" is the file which holds the mapping between the description and the account name to use.
"payees_map" is the file which holds the mapping between the description and the payee to use.
"no_header" should be set to true if first row in the CSV file is not a header.

To run, use the following command

<pre>
./icsv2ledger -a SAV file.csv
</pre>

The above command will use the [SAV] section of the config file to process the csv file.

A typical mapping file might look like:

<pre>
  "/SAFEWAY.*/","Expenses:Food"
  "/ITUNES.*/","Expenses:Entertainment"
  "THE WRESTLERS INN","Expenses:Food"
  "MY COMPANY 1234", "Income:Salary"
</pre>

Later matching entries overwrite earlier ones.

Feedback/contributions most welcome.

Quentin Stafford-Fraser
http://qandr.org/quentin
