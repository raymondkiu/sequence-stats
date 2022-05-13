#!/bin/bash
#print the options
usage () {
  echo ""
  echo "Sequence-stats generates statistics from FASTQ reads or FASTA assemblies"
  echo ""
  echo "For user manual please go to: https://github.com/raymondkiu/sequence-stats"
  echo ""
  echo "Usage: sequence-stats [options] FASTA/FASTQ"
  echo ""
  echo "Options:"
  echo " -a Print FASTA stats"
  echo " -q Print FASTQ stats"
  echo " -t Convert FASTQ to FASTA. Usage: ./sequence-stats -t FASTQ > NEWFILENAME"
  echo " -c Print individual contig's stats (FASTA)"
  echo " -d Dereplicate contigs in (multi)FASTA. Usage: ./sequence-stats -d FASTA > NEWFILENAME"
  echo " -n Rename contigs. Usage: ./sequence-stats -n FASTA PREFIX > NEWFILENAME"
  echo " -b Print FASTA stats in tabular format"
  echo " -r Print FASTQ stats in tabular format"
  echo " -e Extract contig(s) from FASTA sequences. Usage: ./sequence-stats -e CONTIG-IDs.txt FASTA > NEWFILENAME"
  echo " -s Print summary of FASTA tabular stats of multiple files using common suffix. Usage: ./sequence-stats -s SUFFIX > NEWFILENAME"
  echo " -h Print usage and exit"
  echo " -v Print version and exit"
  echo ""
  echo "Version 1.0 (2022)"
  echo "Author: Raymond Kiu Raymond.Kiu@quadram.ac.uk"
  echo "";
}

version (){
echo "sequence-stats 1.0"
}


FILE=$2
name=$3 # for renamecontigs usage

fasta (){
if [ -e "$FILE" ];then
:
else
    echo "$FILE file does not seem to exist. Program will now exit."
    exit 1
fi
# one-liner commands
# Calculate genome size
GENOMESIZE=$(awk '$0 ~ ">" {print c; c=0;printf substr(2,100); } $0 !~ ">" {c+=length($0);} END { print c; }' $FILE|awk '{ sum += $1 } END { print sum }')
# Compute contig number
CONTIGSUM=$(grep ">" $FILE|wc -l)
# Compute A,T,G,G % and GC%
sumG=$(grep -v ">" $FILE|grep -o "G"|wc -l)
sumC=$(grep -v ">" $FILE|grep -o "C"|wc -l)
GC=$(echo "scale=2;($sumG+$sumC)*100/$GENOMESIZE"|bc)
Gperc=$(echo "scale=2;($sumG*100)/$GENOMESIZE"|bc -l)
Cperc=$(echo "scale=2;($sumC*100)/$GENOMESIZE"|bc -l)
sumA=$(grep -v ">" $FILE|grep -o "A"|wc -l)
Aperc=$(echo "scale=2;($sumA*100)/$GENOMESIZE"|bc -l)
sumT=$(grep -v ">" $FILE|grep -o "T"|wc -l)
Tperc=$(echo "scale=2;($sumT*100)/$GENOMESIZE"|bc -l)
# Compute uncertain bases: N, gaps(-) and small a,t,g,c
sumN=$(grep -v ">" $FILE|grep -o "N"|wc -l)
Nperc=$(echo "scale=4;($sumN*100)/$GENOMESIZE"|bc -l)
gap=$(grep -v ">" $FILE|grep -o "-"|wc -l)
suma=$(grep -v ">" $FILE|grep -o "a"|wc -l)
sumt=$(grep -v ">" $FILE|grep -o "t"|wc -l)
sumg=$(grep -v ">" $FILE|grep -o "g"|wc -l)
sumc=$(grep -v ">" $FILE|grep -o "c"|wc -l)
uncertain=$(echo "$suma+$sumt+$sumg+$sumc+$sumN"|bc)
# Determine min and max contig size
mincontig=$(awk '$0 ~ ">" {print c; c=0;printf substr(2,100); } $0 !~ ">" {c+=length($0);} END { print c; }' $FILE |tail -n 1)
maxcontig=$(awk '$0 ~ ">" {print c; c=0;printf substr(2,100); } $0 !~ ">" {c+=length($0);} END { print c; }' $FILE |head -n 2|tail -n 1)
# Determine N50
N50=$(awk '$0 ~ ">" {print c; c=0;printf substr(2,100); } $0 !~ ">" {c+=length($0);} END { print c; }' $FILE |sort -n | awk '{len[i++]=$1;sum+=$1} END {for (j=0;j<i+1;j++) {csum+=len[j]; if (csum>=sum/2) {print len[j];break}}}')
# print all info
echo "Sample: $FILE"
echo "Genome(bp): $GENOMESIZE"
echo "Contigs: $CONTIGSUM"
echo "GC(%): $GC"
echo "A(%): $Aperc"
echo "T(%): $Tperc"
echo "G(%): $Gperc"
echo "C(%): $Cperc"
echo "N50: $N50"
echo "Max Contig: $maxcontig"
echo "Min Contig: $mincontig"
echo "N count: $sumN"
echo "N(%): $Nperc"
echo "Gap(-): $gap"
echo "Uncertain(bp): $uncertain"
exit 0;
}

fastq () {
if [ -e "$FILE" ];then
:
else
    echo "$FILE file does not seem to exist. Program will now exit."
    exit 1
fi
COUNT=$(cat $FILE|echo $((`wc -l`/4)))
READ=$(awk 'NR % 4 == 2 { s += length($1); t++} END {print s/t}' $FILE)
SIZE=$(awk 'BEGIN{sum=0;}{if(NR%4==2){sum+=length($0);}}END{print sum;}' $FILE)
filesize=$(du -sh $FILE|awk '{print $1}')
MaxRead=$(awk 'NR%4==2{print length($0)}' $FILE|sort -n|tail -n 1)
MinRead=$(awk 'NR%4==2{print length($0)}' $FILE|sort -n|head -n 1)

echo "Sample: $FILE"
echo "File size: $filesize"
echo "Total bases: $SIZE"
echo "Reads: $COUNT"
echo "Max read length: $MaxRead"
echo "Min read length: $MinRead"
echo "Mean read length: $READ"
exit 0
}

fastq2fasta (){
sed -n '1~4s/^@/>/p;2~4p' $FILE
}

contigstats (){
awk '$0 ~ ">" {print c; c=0;printf substr($0,2,100) "\t"; } $0 !~ ">" {c+=length($0);} END { print c; }' $FILE
}

dereplicate (){
if [ -e "$FILE" ];then
:
else
    echo "$FILE file does not seem to exist. Program will now exit."
    exit 1
fi

# Dereplicate contigs in AWK
awk 'BEGIN {i = 1;} { if ($1 ~ /^>/) { tmp = h[i]; h[i] = $1; } else if (!a[$1]) { s[i] = $1; a[$1] = "1"; i++; } else { h[i] = tmp; } } END { for (j = 1; j < i; j++) { print h[j]; print s[j]; } }' $FILE

exit 0
}


renamecontigs (){
awk ' \
    BEGIN { \
        contigIdx = 1; \
    } \
    { \
        if ($0 ~ /^>/) { \
            print ">'$name'."contigIdx; \
            contigIdx++; \
        } \
        else { \
            print $0; \
        } \
    }' $FILE
    
exit 0
}

fastatabular (){
if [ -e "$FILE" ];then
:
else
    echo "$FILE file does not seem to exist. Program will now exit."
    exit 1
fi
# one-liner commands
# Calculate genome size
GENOMESIZE=$(awk '$0 ~ ">" {print c; c=0;printf substr(2,100); } $0 !~ ">" {c+=length($0);} END { print c; }' $FILE|awk '{ sum += $1 } END { print sum }')
# Compute contig number
CONTIGSUM=$(grep ">" $FILE|wc -l)
# Compute A,T,G,G % and GC%
sumG=$(grep -v ">" $FILE|grep -o "G"|wc -l)
sumC=$(grep -v ">" $FILE|grep -o "C"|wc -l)
GC=$(echo "scale=2;($sumG+$sumC)*100/$GENOMESIZE"|bc)
Gperc=$(echo "scale=2;($sumG*100)/$GENOMESIZE"|bc -l)
Cperc=$(echo "scale=2;($sumC*100)/$GENOMESIZE"|bc -l)
sumA=$(grep -v ">" $FILE|grep -o "A"|wc -l)
Aperc=$(echo "scale=2;($sumA*100)/$GENOMESIZE"|bc -l)
sumT=$(grep -v ">" $FILE|grep -o "T"|wc -l)
Tperc=$(echo "scale=2;($sumT*100)/$GENOMESIZE"|bc -l)
# Compute uncertain bases: N, gaps(-) and small a,t,g,c
sumN=$(grep -v ">" $FILE|grep -o "N"|wc -l)
Nperc=$(echo "scale=4;($sumN*100)/$GENOMESIZE"|bc -l)
gap=$(grep -v ">" $FILE|grep -o "-"|wc -l)
suma=$(grep -v ">" $FILE|grep -o "a"|wc -l)
sumt=$(grep -v ">" $FILE|grep -o "t"|wc -l)
sumg=$(grep -v ">" $FILE|grep -o "g"|wc -l)
sumc=$(grep -v ">" $FILE|grep -o "c"|wc -l)
uncertain=$(echo "$suma+$sumt+$sumg+$sumc+$sumN"|bc)
# Determine min and max contig size
mincontig=$(awk '$0 ~ ">" {print c; c=0;printf substr(2,100); } $0 !~ ">" {c+=length($0);} END { print c; }' $FILE |tail -n 1)
maxcontig=$(awk '$0 ~ ">" {print c; c=0;printf substr(2,100); } $0 !~ ">" {c+=length($0);} END { print c; }' $FILE |head -n 2|tail -n 1)
# Determine N50
N50=$(awk '$0 ~ ">" {print c; c=0;printf substr(2,100); } $0 !~ ">" {c+=length($0);} END { print c; }' $FILE |sort -n | awk '{len[i++]=$1;sum+=$1} END {for (j=0;j<i+1;j++) {csum+=len[j]; if (csum>=sum/2) {print len[j];break}}}')
# print all info in tabular format:
echo -n -e "SampleID\t"; 
echo -n -e "Genome\t";
echo -n -e "Contigs\t";
echo -n -e "GC(%)\t";
echo -n -e "A(%)\t";
echo -n -e "T(%)\t";
echo -n -e "G(%)\t";
echo -n -e "C(%)\t";
echo -n -e "N50\t";
echo -n -e "Max\t";
echo -n -e "Min\t";
echo -n -e "Ncount\t";
echo -n -e "N(%)\t";
echo -n -e "Gap(-)\t";
echo -e "Uncertain(bp)\t";

echo -n -e "$FILE\t"
echo -n -e "$GENOMESIZE\t"
echo -n -e "$CONTIGSUM\t"
echo -n -e "$GC\t"
echo -n -e "$Aperc\t"
echo -n -e "$Tperc\t"
echo -n -e "$Gperc\t"
echo -n -e "$Cperc\t"
echo -n -e "$N50\t"
echo -n -e "$maxcontig\t"
echo -n -e "$mincontig\t"
echo -n -e "$sumN\t"
echo -n -e "$Nperc\t"
echo -n -e "$gap\t"
echo -e "$uncertain\t"
exit 0;
}

fastqtabular () {
if [ -e "$FILE" ];then
:
else
    echo "$FILE file does not seem to exist. Program will now exit."
    exit 1
fi
COUNT=$(cat $FILE|echo $((`wc -l`/4)))
READ=$(awk 'NR % 4 == 2 { s += length($1); t++} END {print s/t}' $FILE)
SIZE=$(awk 'BEGIN{sum=0;}{if(NR%4==2){sum+=length($0);}}END{print sum;}' $FILE)
filesize=$(du -sh $FILE|awk '{print $1}')
MaxRead=$(awk 'NR%4==2{print length($0)}' $FILE|sort -n|tail -n 1)
MinRead=$(awk 'NR%4==2{print length($0)}' $FILE|sort -n|head -n 1)

# Print info in tabular format, -e is for backslash, -n for new line:
echo -n -e "SampleID\t";
echo -n -e "Size\t";
echo -n -e "Total_bases\t";
echo -n -e "Reads\t";
echo -n -e "MaxRL\t";
echo -n -e "MinRL\t";
echo -e "MeanRL\t";

echo -n -e "$FILE\t"
echo -n -e "$filesize\t"
echo -n -e "$SIZE\t"
echo -n -e "$COUNT\t"
echo -n -e "$MaxRead\t"
echo -n -e "$MinRead\t"
echo -e "$READ"
exit 0
}

extractcontig () {
if [ -e "$FILE" ];then
:
else
    echo "$FILE file does not seem to exist. Program will now exit."
    exit 1
fi

awk 'FNR==NR{a[$0];next} /^>/{val=$0;sub(/^>/,"",val);flag=val in a?1:0} flag' $FILE $name

exit 0
}

fastasummarystats (){

echo -n -e "SampleID\t";
echo -n -e "Genome\t";
echo -n -e "Contigs\t";
echo -n -e "GC(%)\t";
echo -n -e "A(%)\t";
echo -n -e "T(%)\t";
echo -n -e "G(%)\t";
echo -n -e "C(%)\t";
echo -n -e "N50\t";
echo -n -e "Max\t";
echo -n -e "Min\t";
echo -n -e "Ncount\t";
echo -n -e "N(%)\t";
echo -n -e "Gap(-)\t";
echo -e "Uncertain(bp)\t";

cat *.$FILE|grep -v "Genome"

exit 0
}



# Skip over processed options
shift $((OPTIND-1))
# check for mandatory positional parameters, only 1 positional argument will be checked
if [ $# -lt 1 ]; then
   echo "Missing optional argument or positional argument, please supply your fastq reads or fasta assembly"
   echo ""
   echo "Options: ./sequence-stats -h"
   echo ""
   echo ""
   exit 1
fi

# Call options
while getopts ':aqtcdnbreshv' opt;do
  case $opt in
    a) fasta; exit;;
    q) fastq; exit;;
    t) fastq2fasta; exit;;
    c) contigstats; exit;;
    d) dereplicate; exit;;
    n) renamecontigs; exit;;
    b) fastatabular; exit;;
    r) fastqtabular; exit;;
    e) extractcontig; exit;;
    s) fastasummarystats;exit;; 
    h) usage; exit;;
    v) version; exit;;
    \?) echo "Invalid option: -$OPTARG" >&2; exit 1;;
    :) echo "Missing option argument for -$OPTARG" >&2; exit 1;;
    *) echo "Unimplemented option: -$OPTARG" >&2; exit 1;;
   esac
done


shift $((OPTIND-1))
if [ $OPTIND -eq 1 ];then
   echo "Missing optional argument"
   echo ""
   echo "Options: ./sequence-stats -h"
   echo ""
   exit 1
fi

# Check if file exists
#if [ -e "$FILE" ];then
:
#else
#    echo "$FILE file does not seem to exist. Program will now exit."
#    exit 1
#fi
#exit 0
