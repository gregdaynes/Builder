#!/bin/bash
# call this script with folder to copy
# path will be variable $1
# "sh scriptname /path/to/folder archive_name"

# folder name
FLDR=`basename $1`;

# make tmp dir
mkdir ./tmp

# make builds dir
mkdir ./builds

echo ''
# copy folder to ./
echo 'Copying...'
cp -r $1 ./tmp/;

# find javascript files
echo ''
echo 'Finding javascript files...'
for i in $( find ./tmp -type f -name '*.js'); do
	
	# compress with yuicompressor
	# writes over file
	echo 'Compressing '$i
	java -jar ./tools/yuicompressor-2.4.2.jar -o $i $i;
	
done;

# find stylesheet/less files
echo ''
echo 'Finding less stylesheets...'
for i in $( find ./tmp -type f -name '*.less'); do
	# complie less to css with ruby gem
	lessc $i;
done;

# find stylesheet files
echo ''
echo 'Finding stylesheets...'
for i in $( find ./tmp -type f -name '*.css'); do

	# compress with yuicompressor
	# writes over file
	echo 'Compressing '$i
	java -jar ./tools/yuicompressor-2.4.2.jar -o $i $i;
	
done;

# find html files
echo ''
echo 'Finding html files...'
for i in $( find ./tmp -type f -name '*.html'); do
	
	# compress with htmlcompressor
	# writes over file
	echo 'Compressing '$i
	java -jar ./tools/htmlcompressor-0.9.4.jar -o $i $i;
	
done;

# smush images (gif png jpg)
echo ''
echo 'Smushing images...'
java -jar ./tools/smushit.jar -imageDir=./tmp;

# get newest svn revision number
cd ./tmp/$FLDR
REV=`svn info | grep -i "Revision" | cut -d ' ' -f 2`
REVNUM='version>$Rev: '$REV' $<\/version'
DATESTAMP='creationDate>'`date '+%B %Y'`'<\/creationDate>'

# change xml file version to newest svn version
# change date to now
for fl in *.xml; do
	mv $fl $fl.old
	sed "s/version>\$Rev: [0-9]* \$<\/version/$REVNUM/g" $fl.old > $fl
	sed "s/creationDate>\$Date: .*\$/$DATESTAMP/g" $fl.old > $fl
	rm -f $fl.old
done
cd ../../

# remove svn info
echo ''
echo 'Removing svn info...'
find ./tmp -name '*.svn' | xargs rm -rf;

# remove os x info
echo ''
echo 'Removing resource forks...'
find ./tmp -name '*.DS_Store' | xargs rm -rf;
export COPYFILE_DISABLE=true

# remove resources
echo ''
echo 'Removing design resources...'
find ./tmp -name '*.psd' | xargs rm -rf;
find ./tmp -name '*.esproj' | xargs rm -rf;
find ./tmp -name '*.less' | xargs rm -rf;

# compress
echo ''
echo 'Creating archive...'
cd ./tmp/$FLDR
filename=$FLDR".r"$REV".tgz"
tar -czf $filename --exclude=$filename ./;

# move archive back
# mv ./$filename ../../builds/
mv ./$filename ~/Desktop/

# remove tmp dir
cd ../../
rm -rf ./tmp

# all done
echo ''
echo 'Done!'