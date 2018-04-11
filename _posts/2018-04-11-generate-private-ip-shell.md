---
published: false
---
Below is the shell script to generate private CIDR range. The CIDR range can be used to usecases lke creating VPC in Amazon AWS.

Below script does this /20. You can change the logic accordingly for other addresss format


`##### Generate CIDR #####
value=1
while [ $value -eq 1 ]
do
case $(( RANDOM % 3 )) in
    0)
        a=10
        ;;
    1)
        a=172
        ;;
    2)
        a=192
        ;;
esac
if [ $a -eq 192 ]
then
b=168
c=1
while [ $(($c % 16)) -ne 0 ]
do
        c=$((RANDOM%255))
done
d=0
fi
if [ $a -eq 172 ]
then
b=$[ ( $RANDOM % 30 )  + 16 ]   # 172.31 already taken by default VPC
c=1
while [ $(($c % 16)) -ne 0 ]
do
        c=$((RANDOM%255))
done
d=0
fi
if [ $a -eq 10 ]
then
b=$[ ( $RANDOM % 255 ) ]
c=1
while [ $(($c % 16)) -ne 0 ]
do
        c=$((RANDOM%255))
done
d=0
fi

##### Generate CIDR END #####`
