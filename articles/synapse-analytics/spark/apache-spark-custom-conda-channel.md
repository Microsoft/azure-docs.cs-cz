---
title: Vytvoření vlastního Condaového kanálu pro správu balíčků
description: Naučte se vytvářet vlastní conda kanál pro správu balíčků.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 528ba4a1be3650a81772d78a438f03611b9bd761
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107470"
---
# <a name="create-a-custom-conda-channel-for-package-management"></a>Vytvoření vlastního Condaového kanálu pro správu balíčků 
Správce balíčků conda při instalaci balíčků Pythonu používá kanály k hledání balíčků. Možná budete muset vytvořit vlastní conda kanál z různých důvodů. Můžete například zjistit, že:

- Váš pracovní prostor je exfiltrace chráněná data a odchozí připojení jsou blokovaná.  
- máte balíčky, které nechcete nahrávat do veřejných úložišť.
- Chcete pro uživatele v pracovním prostoru nastavit alternativní úložiště.

V tomto článku poskytneme podrobný průvodce, který vám pomůže vytvořit vlastní conda kanál v rámci vašeho účtu Azure Data Lake Storage.

## <a name="set-up-your-local-machine"></a>Nastavení místního počítače

1. Nainstalujte conda do místního počítače. Můžete se podívat na [modul runtime Azure synapse Spark](./apache-spark-version-support.md) , který identifikuje verzi Conda, která se používá ve stejném modulu runtime.
   
2. Pokud chcete vytvořit vlastní kanál, nainstalujte conda-Build.
```
conda install conda-build
```
3. Uspořádejte všechny balíčky v nástroji pro platformu, kterou chcete zajišťovat. V tomto příkladu budeme na váš místní počítač instalovat Anaconda Archive.

```
sudo wget https://repo.continuum.io/archive/Anaconda3-4.4.0-Linux-x86_64.sh 
sudo chmod +x Anaconda3-4.4.0-Linux-x86_64.sh  
sudo bash Anaconda3-4.4.0-Linux-x86_64.sh -b -p /usr/lib/anaconda3 
export PATH="/usr/lib/anaconda3/bin:$PATH" 
sudo chmod 777 -R /usr/lib/anaconda3a.  
```
## <a name="mount-the-storage-account-onto-your-machine"></a>Připojte účet úložiště do svého počítače.
Dále připojíme účet Azure Data Lake Storage Gen2 k vašemu místnímu počítači. Tento proces je také možné provést s účtem WASB; Budeme si ale projít příkladem účtu ADLSg2. 
 
Další informace o tom, jak připojit účet úložiště na místním počítači, najdete na [této stránce](https://github.com/Azure/azure-storage-fuse#blobfuse ). 

1. Blobfuse můžete nainstalovat z úložiště softwaru pro Linux pro produkty Microsoftu.

```
wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb 
sudo dpkg -i packages-microsoft-prod.deb 
sudo apt-get update 
sudo apt-get install blobfuse fuse 
export AZURE_STORAGE_ACCOUNT=<<myaccountname>
export AZURE_STORAGE_ACCESS_KEY=<<myaccountkey>>
export AZURE_STORAGE_BLOB_ENDPOINT=*.dfs.core.windows.net 
```

2. Vytvořte přípojný bod ( ```mkdir /path/to/mount``` ) a připojte kontejner objektů BLOB pomocí blobfuse. V tomto příkladu použijeme hodnotu **PrivateChannel** pro proměnnou **myContainer** .
   
```
blobfuse /path/to/mount --container-name=mycontainer --tmp-path=/mnt/blobfusetmp --use-adls=true --log-level=LOG_DEBUG 
sudo mkdir -p /mnt/blobfusetmp
sudo chown <myuser> /mnt/blobfusetmp
```
## <a name="create-the-channel"></a>Vytvoření kanálu
V další sadě kroků vytvoříme vlastní conda kanál. 

1. Na místním počítači vytvořte adresář pro uspořádání všech balíčků pro vlastní kanál.
   
```
mkdir /home/trusted-service-user/privatechannel 
cd ~/privatechannel/ 
mkdir channel1/linux64 
```

2. Uspořádejte všechny ```tar.bz2``` balíčky z https://repo.anaconda.com/pkgs/main/linux-64/ do podadresáře. Nezapomeňte taky zahrnout i všechny závislé balíčky tar. bz2. 

```
cd channel1 
mkdir noarch 
echo '{}' > noarch/repodata.json 
bzip2 -k noarch/repodata.json 

// Create channel 

conda index channel1/noarch 
conda index channel1/linux-64 
conda index channel1 
```

Další informace najdete také [v uživatelské příručce k conda](https://docs.conda.io/projects/conda/latest/user-guide/tasks/create-custom-channels.html) , kde můžete vytvářet vlastní kanály. 

## <a name="storage-account-permissions"></a>Oprávnění účtu úložiště
Nyní bude nutné ověřit oprávnění účtu úložiště. Tato oprávnění nastavíte tak, že přejdete na cestu, kde se vytvoří vlastní kanál. Pak vytvořte token SAS pro ```privatechannel``` , který má oprávnění ke čtení, výpisu a spouštění. 

Název kanálu teď bude adresa URL SAS objektu blob, která se generuje z tohoto procesu.  

## <a name="create-a-sample-conda-environment-configuration-file"></a>Vytvoření ukázkového konfiguračního souboru prostředí conda
Nakonec ověřte proces instalace vytvořením ukázkového ```environment.yml``` souboru conda. Pokud máte v pracovním prostoru s povolenou funkcí DEP, musíte ``nodefaults`` v souboru prostředí zadat kanál.

Tady je příklad konfiguračního souboru conda:
```
name: sample 
channels: 
  - https://<<storage account name>>.blob.core.windows.net/privatechannel/channel?<<SAS Token>
  - nodefaults 
dependencies: 
  - openssl 
  - ncurses 
```
Po vytvoření ukázkového souboru conda můžete vytvořit prostředí Virtual conda. 

```
conda env create –file sample.yml  
source activate env 
conda list 
```
Teď, když jste ověřili svůj vlastní kanál, můžete pomocí procesu [správy fondu Pythonu](./apache-spark-manage-python-packages.md) aktualizovat knihovny ve fondu Apache Spark.

## <a name="next-steps"></a>Další kroky
- Zobrazení výchozích knihoven: [podpora Apache Spark verzí](apache-spark-version-support.md)
- Správa balíčků Pythonu: [Správa balíčků v Pythonu](./apache-spark-manage-python-packages.md)

