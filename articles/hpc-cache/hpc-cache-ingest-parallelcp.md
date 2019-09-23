---
title: Ingestování dat ve verzi Preview mezipaměti prostředí Azure HPC – paralelní kopírování skriptu
description: Jak použít skript paralelního kopírování k přesunu dat do cíle úložiště objektů BLOB v mezipaměti HPC Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: v-erkell
ms.openlocfilehash: 852b4e692a4316c7701c8c179039104bee561949
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71180949"
---
# <a name="azure-hpc-cache-preview-data-ingest---parallel-copy-script-method"></a>Azure HPC cache (Preview) data ingestování – paralelní kopírování metody skriptu

Tento článek obsahuje pokyny pro vytvoření ``parallelcp`` skriptu a jeho použití k přesunu dat do kontejneru úložiště objektů BLOB pro použití s mezipamětí Azure HPC.

Další informace o přesouvání dat do úložiště objektů BLOB pro mezipaměť prostředí Azure HPC najdete v tématu [přesun dat do Azure Blob Storage pro Azure HPC cache](hpc-cache-ingest.md).

## <a name="create-the-parallelcp-script"></a>Vytvoření skriptu parallelcp

Do následujícího skriptu se přidá spustitelný soubor `parallelcp`. (Tento skript je určený pro Ubuntu; Pokud používáte jinou distribuci, musíte nainstalovat ``parallel`` samostatně.)

```bash
sudo touch /usr/bin/parallelcp && sudo chmod 755 /usr/bin/parallelcp && sudo sh -c "/bin/cat >/usr/bin/parallelcp" <<EOM 
#!/bin/bash

display_usage() { 
    echo -e "\nUsage: \$0 SOURCE_DIR DEST_DIR\n" 
} 

if [  \$# -le 1 ] ; then 
    display_usage
    exit 1
fi 
 
if [[ ( \$# == "--help") ||  \$# == "-h" ]] ; then 
    display_usage
    exit 0
fi 

SOURCE_DIR="\$1"
DEST_DIR="\$2"

if [ ! -d "\$SOURCE_DIR" ] ; then
    echo "Source directory \$SOURCE_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -d "\$DEST_DIR" ] && ! mkdir -p \$DEST_DIR ; then
    echo "Destination directory \$DEST_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -w "\$DEST_DIR" ] ; then
    echo "Destination directory \$DEST_DIR is not writeable, or is not a directory"
    display_usage
    exit 3
fi

if ! which parallel > /dev/null ; then
    sudo apt-get update && sudo apt install -y parallel
fi

DIRJOBS=225
JOBS=225
find \$SOURCE_DIR -mindepth 1 -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$DIRJOBS -0 "mkdir -p \$DEST_DIR/{}"
find \$SOURCE_DIR -mindepth 1 ! -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$JOBS -0 "cp -P \$SOURCE_DIR/{} \$DEST_DIR/{}"
EOM
```

## <a name="parallel-copy-example"></a>Příklad paralelního kopírování

V tomto příkladu se používá skript paralelního kopírování ``glibc`` ke kompilaci pomocí zdrojových souborů v mezipaměti HPC Azure.

Zdrojové soubory jsou ukládány do mezipaměti v bodu připojení mezipaměti služby Azure HPC a soubory objektů jsou uloženy na místním pevném disku.

V tomto příkladu se používá paralelní kopírování skriptu s možností ``-j`` a ``make`` získání paralelismu.

```bash
sudo apt-get update
sudo apt install -y gcc bison gcc binutils make parallel
cd
wget https://mirrors.kernel.org/gnu/libc/glibc-2.27.tar.bz2
tar jxf glibc-2.27.tar.bz2
ln -s /nfs/cache1 hpccache
time parallelcp glibc-2.27 avere/glibc-2.27
cd
mkdir obj
mkdir usr
cd obj
/home/azureuser/avere/glibc-2.27/configure --prefix=/home/azureuser/usr
time make -j
```
