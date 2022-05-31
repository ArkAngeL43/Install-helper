# Install-helper
simple tool i made in perl to attempt to install ALL black arch tools, all kali linux tools, and substitute tools based on OS, only ARCH and DEBIAN are supported


```
............................................................................                                                                       
:  _____ _         _         _____         _                               :
: | __  | |___ ___| |_      |  _  |___ ___| |_       ___ ___ ___ ___ ___   :
: | __ -| | .'|  _| '_|     |     |  _|  _|   |     |  _| -_| . | . |_ -|  :
: |_____|_|__,|___|_,_|_____|__|__|_| |___|_|_|_____|_| |___|  _|___|___|  :
:                     |_____|                 |_____|       |_|            :
:..........................................................................:
: Linux Installer -> Black_Arch_Mechanic                                   :
:                                                                          :
: install all kali linux and black arch tools from the repos               :
:                                                                          :
:                                                                          :
:Usage: sudo perl install.pl <os|debian|arch> <command>                    :
:                                                                          :
:All commands|                                                             :
:            |install_arch -> install all black arch repos on your system  :
:            |install_kali -> install all kali tools on your system        :
:            |install*     -> install both all kali and debian tools       :
:                                                                          :
: Example - sudo perl install.pl debian install_arch                       :
: Example - sudo perl install.pl debian install*                           :
: Example - sudo perl install.pl arch   install_arch                       :
: Example - sudo perl install.pl arch   install*                           :
:                                                                          :
: Total tools list counted -< 5,708 tools possible to install              :
:--------------------------------------------------------------------------:
```

# how to run 

`sudo perl install.pl <OS> <command>`

there are multiple ways of installing based on lists 

> specifying an OS 

when you run this tool the first argument must be debian or arch 
if it is not either then the tool wont do anything 

> specifying a command 

commands: install_arch, install*, install_arch

*install_arch : install all tools on the blackarch repo list 2,000+ tools 
*install_kali : make a GET request to kalis repo list to get all tools 2,000+ tools 

note: the install_kali one makes a GET request to the kali URL / repo list a total of 2000 times 
why? because in order to get the list and to get the proper name kali linux has their title of the page 
based on the tools number in the list, and since there are 2,891 tools in kali the header will be switched to 
1
2
3
... 
based on the number of requests you have made, for each request a new tool 
is installed 

*`install*` this will do both attempting to be installing a total of 5,705 tools 

# code 

```pl
use LWP::Simple;use 5.10.0;my$counter=0;$filename = "list.txt";my $OS=shift;my $CMD=shift;my$URL="https://en.kali.tools/all/?tool=";my @a = (1..2891);my @KALI_TOOLS;system("clear") if ($^O eq "linux");open(BNN,'<',"banner.txt") or die $!;while(<BNN>) { print $_; }close(BNN);print $banner;if ($OS eq "debian") {if ($CMD eq "install*") {&install_debi;&run_kali;}if ($CMD eq "install_kali") {&run_kali;}if ($CMD eq "install_black_arch") {&install_debi;}}if ($OS eq "arch") {if ($CMD eq "install*") {&install_arch;&run_kali;}if ($CMD eq "install_kali") {&run_kali;}if ($CMD eq "install_black_arch") {&install_arch;}}sub install_arch{open(F, '<', $filename) or die $!;while(<F>) { system("sudo pacman -S $_"); }close(F); }sub install_debi{open(F, '<', $filename) or die $!;while(<F>) { system("sudo apt-get install $_"); }close(F); }sub run_kali{say "\n";say "[!] WARNING: THIS MAY TAKE A WHILE, MAKING REQUESTS IN ORDER TO GET THE TOOL NAME";say "[!] WARNING: MAKING ROUGHLY (ESTIMATE::::: 2,891 HTTP REQUESTS TO DOWNLOAD ALL TOOLS)";for(@a){my $VAR = "https://en.kali.tools/all/?tool=$_";my $html = get ($VAR); $counter++; say"[+] Request #$counter"; $html =~ m{<TITLE>(.*?)</TITLE>}gism;push(@KALI_TOOLS, $1);}foreach my $TOOL(@KALI_TOOLS) {if ($OS eq "arch"){system("sudo pacman -S $TOOL");}if ($OS eq "debian"){system("sudo apt-get install $TOOL"); }}exit;}
```
