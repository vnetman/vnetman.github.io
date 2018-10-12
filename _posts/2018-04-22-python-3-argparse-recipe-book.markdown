---
layout: post
title:  "Python 3 argparse recipes"
date:   2018-04-21 19:07:03 +0530
categories: argparse-recipes
---

### About this page

This is *not* an introduction to argparse. All we have here is a bunch of recipes that make argparse behave in desired ways. I hope to cover the most commonly used command-line actions.

### The "main" program

All the recipes below are assumed to be invoked like this:

{% highlight python %}
def main():
    parser = argparse.ArgumentParser(description = 'argparse recipe book')
    recipe_xx(parser)

if __name__ == '__main__':
    main()
{% endhighlight %}


--------------------------------------------------------------------------------
#### **Recipe 01:**
~~~~~~~~
Optional flag --kill
~~~~~~~~

**Code:**

{% highlight python %}
def recipe_01(parser):
    """Optional flag --kill

    action='store_true' => no argument expected after --kill
    """

    parser.add_argument('--kill', action='store_true', help='Die, die, die!!!')
    args = parser.parse_args()
    print(args.kill)
{% endhighlight %}

**Recipe in action:**

~~~~~~~~
vnetman:mint:~> ./argparse_recipe_book.py 
False

vnetman:mint:~> ./argparse_recipe_book.py -h
usage: argparse_recipe_book.py [-h] [--kill]

argparse recipe book

optional arguments:
  -h, --help  show this help message and exit
  --kill      Die, die, die!!!

vnetman:mint:~> ./argparse_recipe_book.py --kill
True
~~~~~~~~
--------------------------------------------------------------------------------
#### **Recipe 02**:
~~~~~~~~
 --repeat <count>
 --repeat is optional; if given, <count> is compulsory
~~~~~~~~

**Code:**

{% highlight python %}
def recipe_02(parser):
    """ --repeat <count>
    --repeat is optional; if given, <count> is compulsory

    'metavar' is used in the help text
    No meaningful help for -r though; see recipe_03 for a better version
    Note that there is no 'action = ...' => there is an argument that follows
    """
    parser.add_argument('--repeat', metavar='<n>', type=int)
    args = parser.parse_args()
    print(args.repeat)
{% endhighlight %}

**Recipe in action:**

~~~~~~~~
vnetman:mint:~> ./argparse_recipe_book.py
None

vnetman:mint:~> ./argparse_recipe_book.py --help
usage: argparse_recipe_book.py [-h] [--repeat <n>]

argparse recipe book

optional arguments:
  -h, --help    show this help message and exit
  --repeat <n>

vnetman:mint:~> ./argparse_recipe_book.py --repeat
usage: argparse_recipe_book.py [-h] [--repeat <n>]
argparse_recipe_book.py: error: argument --repeat: expected one argument

vnetman:mint:~> ./argparse_recipe_book.py --repeat 10
10
~~~~~~~~
--------------------------------------------------------------------------------
#### **Recipe 03**:
~~~~~~~~
 Same as recipe_02, but better help
~~~~~~~~

**Code:**

{% highlight python %}
def recipe_03(parser):
    """ Same as recipe_02, but better help
    """

    parser.add_argument('--repeat', metavar='<n>', type=int,
                        help='how many repeats')
    args = parser.parse_args()
    print(args.repeat)
{% endhighlight %}

**Recipe in action:**

~~~~~~~~
vnetman:mint:~> ./argparse_recipe_book.py
None

vnetman:mint:~> ./argparse_recipe_book.py --help
usage: argparse_recipe_book.py [-h] [--repeat <n>]

argparse recipe book

optional arguments:
  -h, --help    show this help message and exit
  --repeat <n>  how many repeats

vnetman:mint:~> ./argparse_recipe_book.py --repeat
usage: argparse_recipe_book.py [-h] [--repeat <n>]
argparse_recipe_book.py: error: argument --repeat: expected one argument

vnetman:mint:~> ./argparse_recipe_book.py --repeat 10
10
~~~~~~~~
--------------------------------------------------------------------------------
#### **Recipe 04**:
~~~~~~~~
 --out <filename>
 Compulsory argument (i.e. --out is compulsory, and a filename must follow it)
~~~~~~~~

**Code:**

{% highlight python %}
def recipe_04(parser):
    """ --out <filename>
    Compulsory argument (i.e. --out is compulsory, and a filename must follow it)

    'type=..' unspecified => string is assumed as default
    required=True => you guessed it, the argument is compulsory
    """

    parser.add_argument('--out', metavar='<file name>',
                        help='destination file', required=True)
    args = parser.parse_args()
    print(args.out)
{% endhighlight %}

**Recipe in action:**

~~~~~~~~
vnetman:mint:~> ./argparse_recipe_book.py
usage: argparse_recipe_book.py [-h] --out <file name>
argparse_recipe_book.py: error: the following arguments are required: --out

vnetman:mint:~> ./argparse_recipe_book.py --help
usage: argparse_recipe_book.py [-h] --out <file name>

argparse recipe book

optional arguments:
  -h, --help         show this help message and exit
  --out <file name>  where to write

vnetman:mint:~> ./argparse_recipe_book.py --out
usage: argparse_recipe_book.py [-h] --out <file name>
argparse_recipe_book.py: error: argument --out: expected one argument

vnetman:mint:~> ./argparse_recipe_book.py --out foobar
foobar
~~~~~~~~
--------------------------------------------------------------------------------
#### **Recipe 05**:
~~~~~~~~
 <in-file>
 One compulsory argument without any switches
~~~~~~~~

**Code:**

{% highlight python %}
def recipe_05(parser):
    """ <in-file>
    One compulsory argument without any switches
    """

    parser.add_argument('in_file', metavar='<input-file>',
                        help='file containing input')
    args = parser.parse_args()
    print('input file is "' + args.in_file + '"')
{% endhighlight %}

**Recipe in action:**

~~~~~~~~
vnetman:mint:~> ./argparse_recipe_book.py
usage: argparse_recipe_book.py [-h] <input-file>
argparse_recipe_book.py: error: the following arguments are required: <input-file>

vnetman:mint:~> ./argparse_recipe_book.py --help
usage: argparse_recipe_book.py [-h] <input-file>

argparse recipe book

positional arguments:
  <input-file>  file containing input

optional arguments:
  -h, --help    show this help message and exit

vnetman:mint:~> ./argparse_recipe_book.py foobar
input file is "foobar"
~~~~~~~~
--------------------------------------------------------------------------------
#### **Recipe 06**:
~~~~~~~~
 Zero or more arguments without any switches
 (See recipe_07 for better-looking help text)
~~~~~~~~

**Code:**

{% highlight python %}
def recipe_06(parser):
    """Zero or more arguments without any switches
    (See recipe_07 for better-looking help text)
    """

    parser.add_argument('pcaps', nargs='*')
    args = parser.parse_args()
    for _ in args.pcaps:
        print(_)
{% endhighlight %}

**Recipe in action:**

~~~~~~~~
vnetman:mint:~> ./argparse_recipe_book.py

vnetman:mint:~> ./argparse_recipe_book.py --help
usage: argparse_recipe_book.py [-h] [pcaps [pcaps ...]]

argparse recipe book

positional arguments:
  pcaps

optional arguments:
  -h, --help  show this help message and exit

vnetman:mint:~> ./argparse_recipe_book.py alpha
alpha

vnetman:mint:~> ./argparse_recipe_book.py alpha beta gamma delta epsilon
alpha
beta
gamma
delta
epsilon
vnetman:mint:~> 
~~~~~~~~
--------------------------------------------------------------------------------
#### **Recipe 07**:
~~~~~~~~
 Same as recipe_06, but with a better-looking help text
 [<pcap-1> <pcap-2> ...]
 Zero or more arguments without any switches
~~~~~~~~

**Code:**

{% highlight python %}
def recipe_07(parser):
    """ Same as recipe_06, but with a better-looking help text
    [<pcap-1> <pcap-2> ...]
    Zero or more arguments without any switches
    """

    parser.add_argument('pcaps', nargs='*', metavar='<pcap-file>',
                        help='input pcap file for streams')
    args = parser.parse_args()
    for _ in args.pcaps:
        print(_)
{% endhighlight %}

**Recipe in action:**

~~~~~~~~
vnetman:mint:~> ./argparse_recipe_book.py

vnetman:mint:~> ./argparse_recipe_book.py --help
usage: argparse_recipe_book.py [-h] [<pcap-file> [<pcap-file> ...]]

argparse recipe book

positional arguments:
  <pcap-file>  input pcap file for streams

optional arguments:
  -h, --help   show this help message and exit

vnetman:mint:~> ./argparse_recipe_book.py mercury venus earth mars
mercury
venus
earth
mars
vnetman:mint:~> 
~~~~~~~~
--------------------------------------------------------------------------------
#### **Recipe 08**:
~~~~~~~~
 <pcap-1> [<pcap-2> ...]
 One or more arguments without any switches
~~~~~~~~

**Code:**

{% highlight python %}
def recipe_08(parser):
    """ <pcap-1> [<pcap-2> ...]
    One or more arguments without any switches

    Note: nargs = '+' for one or more, '*' for zero or more
    """

    parser.add_argument('pcaps', nargs='+', metavar='<pcap-file>',
                        help='input pcap file for streams')
    args = parser.parse_args()
    for _ in args.pcaps:
        print(_)
{% endhighlight %}

**Recipe in action:**

~~~~~~~~
vnetman:mint:~> ./argparse_recipe_book.py
usage: argparse_recipe_book.py [-h] <pcap-file> [<pcap-file> ...]
argparse_recipe_book.py: error: the following arguments are required: <pcap-file>

vnetman:mint:~> ./argparse_recipe_book.py --help
usage: argparse_recipe_book.py [-h] <pcap-file> [<pcap-file> ...]

argparse recipe book

positional arguments:
  <pcap-file>  input pcap file for streams

optional arguments:
  -h, --help   show this help message and exit

vnetman:mint:~> ./argparse_recipe_book.py japan
japan

vnetman:mint:~> ./argparse_recipe_book.py japan korea china
japan
korea
china
~~~~~~~~
--------------------------------------------------------------------------------
#### **Recipe 09**:
~~~~~~~~
 Optional switch --colors
 If given, at least one arg must be provided, possibly more
~~~~~~~~

**Code:**

{% highlight python %}
def recipe_09(parser):
    """Optional switch --colors
    If given, at least one arg must be provided, possibly more
    """

    parser.add_argument('--colors', nargs='+')
    args = parser.parse_args()
    if args.colors:
        for _ in args.colors:
            print(_)
    else:
        print('No colors specified')
{% endhighlight %}

**Recipe in action:**

~~~~~~~~
vnetman:mint:~> ./argparse_recipe_book.py
No colors specified

vnetman:mint:~> ./argparse_recipe_book.py --help
usage: argparse_recipe_book.py [-h] [--colors COLORS [COLORS ...]]

argparse recipe book

optional arguments:
  -h, --help            show this help message and exit
  --colors COLORS [COLORS ...]

vnetman:mint:~> ./argparse_recipe_book.py --colors
usage: argparse_recipe_book.py [-h] [--colors COLORS [COLORS ...]]
argparse_recipe_book.py: error: argument --colors: expected at least one argument

vnetman:mint:~> ./argparse_recipe_book.py --colors red
red

vnetman:mint:~> ./argparse_recipe_book.py --colors black brown red orange yellow green blue
black
brown
red
orange
yellow
green
blue
~~~~~~~~
--------------------------------------------------------------------------------
#### **Recipe 10**:
~~~~~~~~
 <--get | --set | --clear>
 Mutually exclusive boolean flags
 Any one must be specified
~~~~~~~~

**Code:**

{% highlight python %}
def recipe_10(parser):
    """<--get | --set | --clear>
    Mutually exclusive boolean flags
    Any one must be specified

    Use 'add_mutually_exclusive_group', and add the arguments to the group\
    """

    get_set_clear_group = parser.add_mutually_exclusive_group(required=True)

    get_set_clear_group.add_argument('--get', action='store_true',
                                     help='get the available stock quantity')
    get_set_clear_group.add_argument('--set', action='store_true',
                                     help='set the available stock quantity')
    get_set_clear_group.add_argument('--clear', action='store_true',
                                     help='clear the available stock quantity')

    args = parser.parse_args()
    print('get = {}, set = {}, clear = {}'.
          format(args.get, args.set, args.clear))
{% endhighlight %}

**Recipe in action:**

~~~~~~~~
vnetman:mint:~> ./argparse_recipe_book.py
usage: argparse_recipe_book.py [-h] (--get | --set | --clear)
argparse_recipe_book.py: error: one of the arguments --get --set --clear is required

vnetman:mint:~> ./argparse_recipe_book.py --help
usage: argparse_recipe_book.py [-h] (--get | --set | --clear)

argparse recipe book

optional arguments:
  -h, --help  show this help message and exit
  --get       get the available stock quantity
  --set       set the available stock quantity
  --clear     clear the available stock quantity

vnetman:mint:~> ./argparse_recipe_book.py --set
get = False, set = True, clear = False

vnetman:mint:~> ./argparse_recipe_book.py --get
get = True, set = False, clear = False

vnetman:mint:~> ./argparse_recipe_book.py --clear
get = False, set = False, clear = True

vnetman:mint:~> ./argparse_recipe_book.py --get --clear
usage: argparse_recipe_book.py [-h] (--get | --set | --clear)
argparse_recipe_book.py: error: argument --clear: not allowed with argument --get
~~~~~~~~
--------------------------------------------------------------------------------
#### **Recipe 11**:
~~~~~~~~
 --opcode <get | set | clear>
 --opcode is mandatory, and must be followed by one of 'get', 'set', or 'clear'
 In other words, this is a different approach to achieve the same end behaviour
 as recipe_10
~~~~~~~~

**Code:**

{% highlight python %}
def recipe_11(parser):
    """--opcode <get | set | clear>
    --opcode is mandatory, and must be followed by one of 'get', 'set', or 'clear'
    In other words, this is a different approach to achieve the same end
    behaviour as recipe_10

    'choices = ' limits the allowed arguments
    remove the 'required = True' if you don't want --opcode to be mandatory
    """

    parser.add_argument('--opcode', choices=['get', 'set', 'clear'],
                        help='desired operation', required=True)
    args = parser.parse_args()
    print(args.opcode)
{% endhighlight %}

**Recipe in action:**

~~~~~~~~
vnetman:mint:~> ./argparse_recipe_book.py
usage: argparse_recipe_book.py [-h] --opcode {get,set,clear}
argparse_recipe_book.py: error: the following arguments are required: --opcode

vnetman:mint:~> ./argparse_recipe_book.py --help
usage: argparse_recipe_book.py [-h] --opcode {get,set,clear}

argparse recipe book

optional arguments:
  -h, --help            show this help message and exit
  --opcode {get,set,clear}
                        desired operation

vnetman:mint:~> ./argparse_recipe_book.py --opcode get
get

vnetman:mint:~> ./argparse_recipe_book.py --opcode put
usage: argparse_recipe_book.py [-h] --opcode {get,set,clear}
argparse_recipe_book.py: error: argument --opcode: invalid choice: 'put' (choose from 'get', 'set', 'clear')

vnetman:mint:~> ./argparse_recipe_book.py --opcode get set
usage: argparse_recipe_book.py [-h] --opcode {get,set,clear}
argparse_recipe_book.py: error: unrecognized arguments: set
~~~~~~~~
#### **Recipe 12**:
~~~~~~~~
 --retries <1-10>
 '--retries' is optional. If provided, must be followed by an integer argument from 1 to 10 inclusive.
~~~~~~~~

**Code:**

{% highlight python %}
def recipe_12(parser):
    """--retries <1-10>
    --retries is optional. If provided, must be followed by an integer argument
    from 1 to 10 inclusive.
    """

    parser.add_argument('--retries', choices=range(1, 11), type=int,
                        metavar='<attempts (1-10)>',
                        help='how many times to retry')
    args = parser.parse_args()
    if args.retries:
        print('args.retries is of type "{}", and has value {}'.
              format(type(args.retries), args.retries))
    else:
        print('retries not specified')
{% endhighlight %}

**Recipe in action:**

~~~~~~~~
vnetman:mint:~> ./argparse_recipe_book.py 
retries not specified

vnetman:mint:~> ./argparse_recipe_book.py --help
usage: argparse_recipe_book.py [-h] [--retries <attempts 1-10>]

argparse recipe book

optional arguments:
  -h, --help            show this help message and exit
  --retries <attempts (1-10)>
                        how many times to retry

vnetman:mint:~> ./argparse_recipe_book.py --retries
usage: argparse_recipe_book.py [-h] [--retries <attempts 1-10>]
argparse_recipe_book.py: error: argument --retries: expected one argument

vnetman:mint:~> ./argparse_recipe_book.py --retries 3
args.retries is of type "<class 'int'>", and has value 3

vnetman:mint:~> ./argparse_recipe_book.py --retries 30
usage: argparse_recipe_book.py [-h] [--retries <attempts 1-10>]
argparse_recipe_book.py: error: argument --retries: invalid choice: 30 (choose from 1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
vnetman:mint:~> # Note that when a value outside the range is specified, the error message prints out *all* the allowed values. This can be an issue when the range is large.
~~~~~~~~
#### **Recipe 13**:
~~~~~~~~
 Multiple sub-commands, each with its own set of options

   add     --name <name> --age <age> --gender (male|female)
   view    [--males-only | --females-only] [--seniors | --no-seniors]
   modify  --id <id> --points <delta>
   delete  --id <id> [--force]
~~~~~~~~

**Code:**

{% highlight python %}
def recipe_13(parser):
    """Multiple sub-commands, each with its own set of options

    add     --name <name> --age <age> --gender (male|female)
    view    [--males-only | --females-only] [--seniors | --no-seniors]
    modify  --id <id> --points <delta>
    delete  --id <id> [--force]
    """

    # Setting dest='operation' means that the value returned from the final
    # parse_args() call will contain an attribute called 'operation', which
    # indicates which sub-command (i.e. add/view/modify/delete) was specified on
    # the command line.
    subparsers = parser.add_subparsers(help='Database record operation',
                                       dest='operation')
    subparsers.required = True

    parser_op_add = subparsers.add_parser('add', help='Add a new record')
    parser_op_view = subparsers.add_parser('view', help='View records')
    parser_op_modify = subparsers.add_parser('modify', help='Change a record')
    parser_op_delete = subparsers.add_parser('delete', help='Delete a record')

    # Options for 'add'
    parser_op_add.add_argument('--name', metavar='<name>', required=True)
    parser_op_add.add_argument('--age', metavar='<age>', required=True,
                               type=int)
    parser_op_add.add_argument('--gender', required=True,
                               choices=['male', 'female'])

    # Options for 'view'
    gender_group = parser_op_view.add_mutually_exclusive_group()
    age_group = parser_op_view.add_mutually_exclusive_group()

    gender_group.add_argument('--males-only', action='store_true')
    gender_group.add_argument('--females-only', action='store_true')

    age_group.add_argument('--seniors', action='store_true')
    age_group.add_argument('--no-seniors', action='store_true')

    # Options for 'modify'
    parser_op_modify.add_argument('--id', required=True, type=int,
                                  metavar='<id>')
    parser_op_modify.add_argument('--points', required=True, type=int,
                                  metavar='<points>')

    # Options for 'delete'
    parser_op_delete.add_argument('--id', required=True, type=int,
                                  metavar='<id>')
    parser_op_delete.add_argument('--force', action='store_true')

    args = parser.parse_args()

    # Setting dest='which' in the add_subparsers() invocation above, which means
    # that the 'which' attribute of args tells us which sub-command was entered
    if args.operation == 'add':
        print('Adding name "{}", age "{}", gender "{}"'.
              format(args.name, args.age, args.gender))

    elif args.operation == 'view':
        if args.males_only:
            gender_sel = 'only males'
        elif args.females_only:
            gender_sel = 'only females'
        else:
            gender_sel = 'all genders'

        if args.seniors:
            age_sel = 'seniors only'
        elif args.no_seniors:
            age_sel = 'no seniors'
        else:
            age_sel = 'all ages'

        print('Viewing records for {}, {}'.format(gender_sel, age_sel))

    elif args.operation == 'modify':
        print('Modifying record for ID {}, points = {}'.
              format(args.id, args.points))

    elif args.operation == 'delete':
        print('Deleting record for ID {}, force = {}'.
              format(args.id, args.force))

    else:
        raise ValueError('invalid operation')
{% endhighlight %}

**Recipe in action:**

~~~~~~~~
vnetman@mint:~/work/argparse_recipe_book$ ./argparse_recipe_book.py 
usage: argparse_recipe_book.py [-h] {add,view,modify,delete} ...
argparse_recipe_book.py: error: the following arguments are required: operation
vnetman@mint:~/work/argparse_recipe_book$ 
vnetman@mint:~/work/argparse_recipe_book$ ./argparse_recipe_book.py -h
usage: argparse_recipe_book.py [-h] {add,view,modify,delete} ...

argparse recipe book

positional arguments:
  {add,view,modify,delete}
                        Database record operation
    add                 Add a new record
    view                View records
    modify              Change a record
    delete              Delete a record

optional arguments:
  -h, --help            show this help message and exit
vnetman@mint:~/work/argparse_recipe_book$ 
vnetman@mint:~/work/argparse_recipe_book$ ./argparse_recipe_book.py add
usage: argparse_recipe_book.py add [-h] --name <name> --age <age> --gender {male,female}
argparse_recipe_book.py add: error: the following arguments are required: --name, --age, --gender
vnetman@mint:~/work/argparse_recipe_book$ 
vnetman@mint:~/work/argparse_recipe_book$ ./argparse_recipe_book.py add --help
usage: argparse_recipe_book.py add [-h] --name <name> --age <age> --gender {male,female}

optional arguments:
  -h, --help            show this help message and exit
  --name <name>
  --age <age>
  --gender {male,female}
vnetman@mint:~/work/argparse_recipe_book$ 
vnetman@mint:~/work/argparse_recipe_book$ ./argparse_recipe_book.py view --help
usage: argparse_recipe_book.py view [-h] [--males-only | --females-only] [--seniors | --no-seniors]

optional arguments:
  -h, --help      show this help message and exit
  --males-only
  --females-only
  --seniors
  --no-seniors
vnetman@mint:~/work/argparse_recipe_book$ 
vnetman@mint:~/work/argparse_recipe_book$ ./argparse_recipe_book.py modify --help
usage: argparse_recipe_book.py modify [-h] --id <id> --points <points>

optional arguments:
  -h, --help         show this help message and exit
  --id <id>
  --points <points>
vnetman@mint:~/work/argparse_recipe_book$ 
vnetman@mint:~/work/argparse_recipe_book$ ./argparse_recipe_book.py delete --help
usage: argparse_recipe_book.py delete [-h] --id <id> [--force]

optional arguments:
  -h, --help  show this help message and exit
  --id <id>
  --force

vnetman@mint:~/work/argparse_recipe_book$ ./argparse_recipe_book.py add --name "harry"
usage: argparse_recipe_book.py add [-h] --name <name> --age <age> --gender {male,female}
argparse_recipe_book.py add: error: the following arguments are required: --age, --gender

vnetman@mint:~/work/argparse_recipe_book$ ./argparse_recipe_book.py add --name "harry" --age 12 --gender
usage: argparse_recipe_book.py add [-h] --name <name> --age <age> --gender {male,female}
argparse_recipe_book.py add: error: argument --gender: expected one argument

vnetman@mint:~/work/argparse_recipe_book$ ./argparse_recipe_book.py add --name "harry" --age 12 --gender male
Adding name "harry", age "12", gender "male"

vnetman@mint:~/work/argparse_recipe_book$ ./argparse_recipe_book.py view
Viewing records for all genders, all ages

vnetman@mint:~/work/argparse_recipe_book$ ./argparse_recipe_book.py view --help
usage: argparse_recipe_book.py view [-h] [--males-only | --females-only] [--seniors | --no-seniors]

optional arguments:
  -h, --help      show this help message and exit
  --males-only
  --females-only
  --seniors
  --no-seniors

vnetman@mint:~/work/argparse_recipe_book$ ./argparse_recipe_book.py view --males-only
Viewing records for only males, all ages

vnetman@mint:~/work/argparse_recipe_book$ ./argparse_recipe_book.py modify 
usage: argparse_recipe_book.py modify [-h] --id <id> --points <points>
argparse_recipe_book.py modify: error: the following arguments are required: --id, --points

vnetman@mint:~/work/argparse_recipe_book$ ./argparse_recipe_book.py modify --help
usage: argparse_recipe_book.py modify [-h] --id <id> --points <points>

optional arguments:
  -h, --help         show this help message and exit
  --id <id>
  --points <points>

vnetman@mint:~/work/argparse_recipe_book$ ./argparse_recipe_book.py modify --id 101 
usage: argparse_recipe_book.py modify [-h] --id <id> --points <points>

argparse_recipe_book.py modify: error: the following arguments are required: --points
vnetman@mint:~/work/argparse_recipe_book$ ./argparse_recipe_book.py modify --id 101 --points -20
Modifying record for ID 101, points = -20

vnetman@mint:~/work/argparse_recipe_book$ ./argparse_recipe_book.py remove
usage: argparse_recipe_book.py [-h] {add,view,modify,delete} ...
argparse_recipe_book.py: error: argument operation: invalid choice: 'remove' (choose from 'add', 'view', 'modify', 'delete')

vnetman@mint:~/work/argparse_recipe_book$ ./argparse_recipe_book.py delete
usage: argparse_recipe_book.py delete [-h] --id <id> [--force]
argparse_recipe_book.py delete: error: the following arguments are required: --id

vnetman@mint:~/work/argparse_recipe_book$ ./argparse_recipe_book.py delete --id 101
Deleting record for ID 101, force = False

vnetman@mint:~/work/argparse_recipe_book$ ./argparse_recipe_book.py delete --id 101 --force
Deleting record for ID 101, force = True
~~~~~~~~
