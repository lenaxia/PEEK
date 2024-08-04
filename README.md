https://forums.openkore.com/viewtopic.php?f=6&t=207278
https://forums.openkore.com/viewtopic.php?f=36&t=13305&p=49440&hilit=Peek#p49440

PEEK - Packet Extraction Evaluation Kit designed by Yommy and Neo.

This extractor works for new files whose size is greater than 6 MB.

But the file, output, the conversion of the need for openkore!!!

SkylorD wrote:

> Just select the file and "Extract Lengths".
> 
> It'll create a Output Folder, with a file containing metadata for openkore :
> 
> 0x0187 = 6
> 
> To use on Openkore, must:
> 0187 6

Keep reading to learn how to convert this metadata from extractor to be able for use in Openkore
Links:
https://github.com/MStr3am/PEEK
https://github.com/ThemonChan/PEEK
http://nbsxey3vnrsxgltxom.cmle.ru/board ... -analyzer/

Mini guide:
1. download PEEK
2. run PEEK.exe
3. click "Browse" and select your client file (not necessarily exe!)
4. click "Start Analyzer"
5. close PEEK
6. open folder: ..\PEEK\Output\
~7. run 1.bat -> Since mediafire files doesn't contains our script?! Do this :~

Download the code in the end of this post, paste in notepad, save as 'recvoutput.pl' and close it. Move it to Output folder inside PEEK directory. Run it.
8. file will appear: recvpackets.txt

Script to get the clean recvpackets :

```
#!/usr/bin/env/perl

use strict;
use warnings;

package iwant_myrecvpackets;

my %packets;

&start();

sub start {
&main();
system("pause");
}

sub main {
my ($message, $file, $input, $output, $files, $found) = 0;
$found = -1;
   foreach (<*>) {
   $files .= $_."\n";
   if (-d $_ && $_ =~ /^Output$/ig) { $found = 1;};
   }
      if ($files =~ /(recvpackets.+\.ini)/ig) {
      #$message = "Type 1 from ThemonChan.\n";
      $file = $1;
      } elsif ($files =~ /(packetlengths.+\.ini)/ig) {
      #$message .= "Type 2 from MrStr3am\n";
      $file = $1;
      } elsif (substr(Win32::GetCwd(), eval (length(Win32::GetCwd()) - 6), length(Win32::GetCwd()) ) ne 'Output') {
      $message = "\nDo you have sure this file is inside \'Output\' folder?\n";
         if ($found eq 1) {         #If we aren't inside output folder, we could be on root, check for existance!
         $message .= "Please bro, move this script to \'Output\' folder! I can see it\n";
         }
      } else {
      $message = "\nPlease, start PEEK first to extract recvpackets initial file.\nThen run this script again.\nIf you've already done it, Try asking on OK forum\n";
      } if (substr(Win32::GetCwd(), eval (length(Win32::GetCwd()) - 20), length(Win32::GetCwd()) ) !~ /([P][E][E][K]([-]?[m][a][s][t][e][r])?)|ThemonChan|MrStr.am/ig) {
      $message .= "Please, move this script to PEEK/Output folder!\n";
      }
goto end if ($message);

open $input, "<$file" or $message = "Can't open the input file\n";
open $output, ">recvpackets.txt" or $message = "Can't create recvpackets output file\n";

goto end if ($message);

read_lines($input);
get_output($output);
close_handles($input, $output);

end:

printf("Length = %d packets\nPackets were extracted with success...\n", eval(keys %packets)) if (!$message);
printf("Errors:%d\n%s\n", eval($message && 1), $message) if ($message);
return 1;
}

sub close_handles {
my ($dw1, $dw2) = @_;
   close($dw1);
   close($dw2);
return 1;
}

sub read_lines {
my $file = shift;
my ($lines, $packetid);
   while (<$file>) {
   $lines = $_;
      if ($lines =~ /0x(0...) = ((-)?\d+)/ig) {
      undef $packetid;
      $packetid = uc($1);
         if (!exists($packets{$packetid})) {
         $packets{$packetid} = $2;
         }
      }
   }
return 1;
}

sub get_output {
my $file = shift;
   foreach my $value (sort keys %packets) {
   print $file "$value $packets{$value}\n";
   }
return 1;
}

1;
```
