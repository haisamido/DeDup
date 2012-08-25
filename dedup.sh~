#!/bin/bash

sFLAG="-s"
bFLAG="-b"
nFLAG="-n"

SPLIT_SIZE="1"
SPLIT_EXE="/usr/bin/split -b ${SPLIT_SIZE} -d -a 32 "
#SPLIT_EXE="split -n 10 -d -a 32 "

FILES=( "files" "1024x1024" )
FILES=( "1024x1024" )
#FILES=( "1x1024" )
#FILES=( "10x1024" )
#FILES=( "x" )

SIGNATURE_EXE="/usr/bin/sha512sum "

WORKSPACE_DIR="./workspace"
BLOBS="blobs"
SPLIT_DIR="${WORKSPACE_DIR}/splitfiles"
BLOBS_DIR="${WORKSPACE_DIR}/${BLOBS}"

if [ ! -d "$SPLIT_DIR" ]; then mkdir -p "$SPLIT_DIR" ; fi
if [ ! -d "$BLOBS_DIR" ]; then mkdir -p "$BLOBS_DIR" ; fi

time for FILES_DIR in "${FILES[@]}"
do

  FILES_LINKS_DIR="${WORKSPACE_DIR}/${FILES_DIR}"
  
  if [ ! -d "$FILES_LINKS_DIR" ]; then mkdir -p "$FILES_LINKS_DIR" ; fi

  FILES=($(find "$FILES_DIR" -maxdepth 1 -type f -name '*'))

  ################################################################################
  # Split Files
  ################################################################################
  time for f in "${FILES[@]}"
  do
    fn="$(basename $f)"
    #echo "Splitting $fn : "
    if [ ! -d "${SPLIT_DIR}/$fn" ];       then mkdir -p "${SPLIT_DIR}/$fn" ; fi
    if [ ! -d "${FILES_LINKS_DIR}/$fn" ]; then mkdir -p "${FILES_LINKS_DIR}/$fn" ; fi
    $SPLIT_EXE ${f} ${SPLIT_DIR}/$fn/${fn}_split_
  done

  ################################################################################
  # Get Hash Signature of each split file
  ################################################################################
  time for f in "${FILES[@]}"
  do
    fn="$(basename $f)"
    FILES_SPLIT=($(ls -f ${SPLIT_DIR}/$fn/*_split_*))
    for (( i = 0 ; i < ${#FILES_SPLIT[@]}; i++ ))
    do
      fns=$(basename ${FILES_SPLIT[$i]})
      _HASH_SIG=($($SIGNATURE_EXE ${FILES_SPLIT[$i]}))
      if [ ! -e "${BLOBS_DIR}/${_HASH_SIG[0]}" ]; then
     #   echo " Not FOUND: Moving ${FILES_SPLIT[$i]} to ${BLOBS_DIR}/${_HASH_SIG[0]}"
        mv ${FILES_SPLIT[$i]} ${BLOBS_DIR}/${_HASH_SIG[0]}
      #else
     #   echo " FOUND:     Not Moving ${FILES_SPLIT[$i]} to ${BLOBS_DIR}/${_HASH_SIG[0]}" 
      fi
      
      CWD="$(pwd)"
      cd "${FILES_LINKS_DIR}/$fn/"
      
      if [ ! -L "$fns" ]; then
        #echo " Not FOUND: Symlinking $fns to ../../${BLOBS}/${_HASH_SIG[0]}"
        ln -s "../../${BLOBS}/${_HASH_SIG[0]}" "$fns"
      #else
       # echo " FOUND:     Not Symlinking $fns to ../../${BLOBS}/${_HASH_SIG[0]}"
      fi
      cd "$CWD"
      
    done
  done

  echo ""
  #echo "Removing $SPLIT_DIR"
  rm -rf $SPLIT_DIR
  echo "-----------------------------------------------------------------------"
  
  ORIGINALS=($(du -sb $FILES_DIR))
  FINALS=($(du -sb $BLOBS_DIR))
  echo "-----------------------------------------------------------------------"
  echo ""
  echo "$ORIGINALS,$FINALS,$SPLIT_SIZE"
#  echo $FINALS $SPLIT_SIZE
  
done


#log() { logger -t scriptname "$@"; echo "$@"; }
