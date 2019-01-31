# Unix

Task from exam (25p):
(25 поени) Да се напише командна процедура која ќе ги најде сите студенти кои се логирале од лабораториите на ФИНКИ во периодот од X до Y часот на 20.06.2018. X и Y се цели броеви кои означуваат час и се даваат како прв и втор аргумент на скриптата. За сите логирани студенти ќе се креираат посебни именици (името на именикот е всушност корисничкото име од студентот) во именикот destination даден како трет аргумент на скриптата. Потоа за секој логиран студент ќе се ископираат сите датотеки од неговиот домашен именик (пр. /home/112345) од форматот zadA-tB.sh (каде A и B се цифри) во новокреираниот именик за студентот во именикот destination. Внимавајте, во домашниот именик на еден студент можно е да има подименици и подименици на подимениците, итн. Ваша задача е да ги пронајдете рекурзивно сите барани датотеки и да ги ископирате. Доколку се случи две датотеки да имаат исто име, тогаш се споредува нивната содржина. Доколку се исти се копира само едната, доколку се различни се остава во дестинацискиот именик онаа која е поголема. На крај во дестинацискиот именик треба да имате само по една копија од датотеките во бараниот формат.
 
На почеток на скриптата направете проверка за тоа дали се проследени сите аргументи. Доколку не се, прикажете соодветно упатство за употреба и излезете неуспешно. Да се внимава, доколку дестинацискиот именик не постои, да се креира нов, а ако постоел истиот да се избрише.
 
Командната процедура треба да се зачува како zad4-t1.sh.
 
Помош: Форматот на IP адресите на лабораториите од ФИНКИ е 10.10.X.Y
 
Пример:
 
./zad4-t1.sh 10 14 /home/user/destination/
 
Излез: /home/user/destination/ 112233: zad3-t1.sh zad4-t1.sh
 
222222: zad4-t2.sh
 
111111: zad3-t2.sh zad4-t2.sh
 
222111: zad3-t1.sh
 
555555: zad4-t2.sh

-----------
Source code:
#!/bin/bash

recursiveTraversing(){ #root directory is argument 1 ($1);              destination directory is argument 2 ($2)

        directories=`ls -l $1 | grep '^d' | awk -v rootPath=$1 '{printf "%s/%s", rootPath, $10}'`
        for d in $directories
        do
          	recursiveTraversing $d
        done
	
	files=`ls -l $1 | grep '^-' | awk -v rootPath=$1 '($10 ~ /zad[0-9]\.sh/) {printf "%s/%s", rootPath, $10}'`
        for f in files
        do
          	fileName=`echo $f | awk -F/ '{print $NF}'`
                if [ -f $destination/$fileName ]
                then
                    	diff=`cmp $destination/$fileName $f`
                        if [ ${#diff} -neq 0 ]
                        then
                            	f1Size=`ls -l $destination/$fileName | awk 'print $6'`
                                f2Size=`ls -l $f | awk 'print $6'`
                                if [ $f1Size -lt $f2Size ]
                                then
                                    	cp $f $destination
                                fi
                        fi
                else
                    	cp $f $destination
                fi
        done
}

sTime=$1
eTime=$2
destination=$3
if [ -d $destination ]
then
    	echo "Removing directory..."
#        rm -r $destination
        echo "Creating directory..."
        mkdir -p $destination
else
    	echo "Creating directory..."
        mkdir -p $destination
fi


students=`last | awk '($3 ~ /89\.205\./) {print $1, $10;}' | sed -e 's/(//' -e 's/)//' -e 's/:/ /' | grep ' [0-9][0-9]* ' | awk -v sTime=$sTime -v eTime=$eTime
'(sTime <= $2) && (eTime >= $2){print $1;}' | uniq`

for s in $students
do
  	mkdir -p $destination/$s
fi


students=`last | awk '($3 ~ /89\.205\./) {print $1, $10;}' | sed -e 's/(//' -e 's/)//' -e 's/:/ /' | grep ' [0-9][0-9]* ' | awk -v sTime=$sTime -v eTime=$eTime
'(sTime <= $2) && (eTime >= $2){print $1;}' | uniq`

for s in $students
do
  	mkdir -p $destination/$s
done

#update list only with the uniq indexes. BTW: uniq command from above expression of students does not work so well.
students=`ls $destination`
echo "Length of destination folder: `echo $students | wc -w`"

for s in $students
do
  	recursiveTraversing /home/$s $destination/$s
done





=====================================================
Example:

echo a/b/c/d | awk -F/ '{print $NF}'
