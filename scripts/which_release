#!/bin/bash

# set -x                         # Uncomment to enable debugging

WORKDIR=~/projects/linux/vulns
REVIEW=${WORKDIR}/cve/review
INPUT=${REVIEW}/6.7.proposed
RELEASE=v6.7

while read -r line; do
    subject=$(echo ${line} | cut -d' ' -f 2-)
    found=false

    if git --no-pager log --format=%s -F --grep="${subject}" ${RELEASE}..${RELEASE}.1 | grep -qF "${subject}"; then
        echo -e "${RELEASE}.1:\t\t${line}"
        echo "${line}" >> ${REVIEW}/${RELEASE}.1-sasha
        continue
    fi

    for minor in {1..4}; do
        next=$((${minor} + 1))
        if git --no-pager log --format=%s -F --grep="${subject}" ${RELEASE}.${minor}..${RELEASE}.${next} | grep -qF "${subject}"; then
            echo -e "${RELEASE}.${next}:\t\t${line}"
            echo "${line}" >> ${REVIEW}/${RELEASE}.${next}-sasha
            found=true
            break
        fi
    done

    if [ "${found}" == "true" ]; then
        continue
    fi

    for rc in {1..7}; do
        next=$((${rc} + 1))
        if git --no-pager log --format=%s -F --grep="${subject}" ${RELEASE}-rc${rc}..${RELEASE}-rc${next} | grep -qF "${subject}"; then
            echo -e "${RELEASE}-rc${next}:\t${line}"
            echo "${line}" >> ${REVIEW}/rcs-sasha
            found=true
            break
        fi
    done

    if [ "${found}" != "true" ]; then
        if git --no-pager log --format=%s -F --grep="${subject}" stable/linux-6.7.y..mainline/master | grep -qF "${subject}"; then
            echo -e "Mainline:\t${line}"
            echo "${line}" >> ${REVIEW}/future-sasha
        else
            echo -e "ERROR: ${line}"
        fi
    fi
done < ${INPUT}
