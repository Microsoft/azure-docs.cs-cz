---
title: Výpočetní uzel proměnné prostředí Azure Batch | Microsoft Docs
description: Výpočetní uzel odkazu na proměnnou prostředí pro Azure Batch Analytics.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 05/05/2017
ms.author: danlep
ms.openlocfilehash: ca8d6a6484cd1f145e7d807681bf2d012f2399e0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
ms.locfileid: "30312697"
---
# <a name="azure-batch-compute-node-environment-variables"></a>Azure Batch výpočetní uzel prostředí proměnné
[Služby Azure Batch](https://azure.microsoft.com/services/batch/) nastaví následující proměnné prostředí na výpočetních uzlech. Tyto proměnné prostředí v příkazové řádky úkolu a v aplikacích, můžete odkazovat a skripty spouštěné příkazové řádky.

Další informace o použití proměnných prostředí pomocí služby Batch najdete v tématu [nastavení prostředí pro úkoly](https://docs.microsoft.com/azure/batch/batch-api-basics#environment-settings-for-tasks).

## <a name="environment-variable-visibility"></a>Viditelnost proměnné prostředí

Tyto proměnné prostředí jsou viditelné pouze v kontextu **úloh uživatele**, uživatelský účet na uzlu, ve kterém se spustí úlohu. *Neuvidíte je*, pokud se k výpočetnímu uzlu [vzdáleně připojíte](https://azure.microsoft.com/documentation/articles/batch-api-basics/#connecting-to-compute-nodes) prostřednictvím protokolu RDP (Remote Desktop) nebo Secure Shell (SSH) a vypíšete si seznam proměnných prostředí. Je to proto, že uživatelský účet, který se používá pro vzdálené připojení, není stejný jako účet, který je používán úkolem.

## <a name="command-line-expansion-of-environment-variables"></a>Příkazového řádku rozšíření proměnných prostředí

Příkazové řádky provést úlohy na výpočetní uzly nespouštějte v rámci prostředí. Proto tyto příkazy na příkazových řádcích nemohou využívat nativně prostředí funkcí, jako je například rozšíření proměnné prostředí (to zahrnuje `PATH`). Abyste mohli využívat funkce, je nutné **vyvolání prostředí** v příkazovém řádku. Například spusťte `cmd.exe` v systému Windows, výpočetní uzly nebo `/bin/sh` na Linuxových uzlů:

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

## <a name="environment-variables"></a>Proměnné prostředí

| Název proměnné                     | Popis                                                              | Dostupnost | Příklad: |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | Název účtu Batch, které patří úkol.                  | Všechny úlohy.   | mybatchaccount |
| AZ_BATCH_CERTIFICATES_DIR       | Adresáře v rámci [pracovního adresáře úkolu] [ files_dirs] v certifikáty jsou uložené pro Linux výpočetních uzlů. Všimněte si, že tato proměnná prostředí se nevztahuje na Windows výpočetních uzlů.                                                  | Všechny úlohy.   |  /mnt/batch/Tasks/workitems/batchjob001/Job-1/task001/certs |
| AZ_BATCH_JOB_ID                 | Číslo ID úlohy, ke které úkol patří. | Všechny úlohy s výjimkou spouštěcí úkol. | batchjob001 |
| AZ_BATCH_JOB_PREP_DIR           | Úplná cesta přípravy úlohy [adresáře úkolu] [ files_dirs] na uzlu. | Všechny úlohy s výjimkou spouštěcího úkolu a úkol přípravy úlohy. Dostupná, jenom Pokud úloha je nakonfigurovaný s úkol přípravy úlohy. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_WORKING_DIR   | Úplná cesta přípravy úlohy [pracovního adresáře úkolu] [ files_dirs] na uzlu. | Všechny úlohy s výjimkou spouštěcího úkolu a úkol přípravy úlohy. Dostupná, jenom Pokud úloha je nakonfigurovaný s úkol přípravy úlohy. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_NODE_ID                | ID uzlu, který úloha je přiřazená. | Všechny úlohy. | tvm-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_ROOT_DIR          | Úplná cesta kořenového adresáře všechny [dávky adresáře] [ files_dirs] na uzlu. | Všechny úlohy. | C:\user\tasks |
| AZ_BATCH_NODE_SHARED_DIR        | Úplná cesta [sdíleného adresáře] [ files_dirs] na uzlu. Všechny úlohy, které jsou spouštěny na uzlu mají přístup pro čtení a zápis do tohoto adresáře. Úlohy, které jsou spouštěny na jiných uzlech nemají vzdáleného přístupu do tohoto adresáře (není adresář "sdílené" sítě). | Všechny úlohy. | C:\user\tasks\shared |
| AZ_BATCH_NODE_STARTUP_DIR       | Úplná cesta [spustit adresáře úkolu] [ files_dirs] na uzlu. | Všechny úlohy. | C:\user\tasks\startup |
| AZ_BATCH_POOL_ID                | Číslo ID fondu, ve kterém je spuštěn úkol. | Všechny úlohy. | batchpool001 |
| AZ_BATCH_TASK_DIR               | Úplná cesta [adresáře úkolu] [ files_dirs] na uzlu. Obsahuje tento adresář `stdout.txt` a `stderr.txt` úlohy a AZ_BATCH_TASK_WORKING_DIR. | Všechny úlohy. | C:\user\tasks\workitems\batchjob001\job-1\task001 |
| AZ_BATCH_TASK_ID                | Číslo ID aktuálního úkolu. | Všechny úlohy s výjimkou spouštěcí úkol. | task001 |
| AZ_BATCH_TASK_WORKING_DIR       | Úplná cesta [pracovního adresáře úkolu] [ files_dirs] na uzlu. Aktuálně spuštěná úloha má přístup pro čtení a zápis do tohoto adresáře. | Všechny úlohy. | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| CCP_NODES                       | Seznam uzlů a počet jader na uzel, který jsou přiděleny [úkol s více instancemi][multi_instance]. Uzly a jader jsou uvedeny ve formátu `numNodes<space>node1IP<space>node1Cores<space>`<br/>`node2IP<space>node2Cores<space> ...`, kde počet uzlů následuje nejméně jeden uzel IP adresy a počet jader pro každou. |  S více instancemi primární a dílčí úkoly. |`2 10.0.0.4 1 10.0.0.5 1` |
| AZ_BATCH_NODE_LIST              | Seznam uzlů, které jsou přiděleny [úkol s více instancemi] [ multi_instance] ve formátu `nodeIP;nodeIP`. | S více instancemi primární a dílčí úkoly. | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_HOST_LIST              | Seznam uzlů, které jsou přiděleny [úkol s více instancemi] [ multi_instance] ve formátu `nodeIP,nodeIP`. | S více instancemi primární a dílčí úkoly. | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_MASTER_NODE            | IP adresa a port výpočetním uzlu, na kterém primární úlohy [úkol s více instancemi] [ multi_instance] běží. | S více instancemi primární a dílčí úkoly. | `10.0.0.4:6000`|
| AZ_BATCH_TASK_SHARED_DIR | Cestu k adresáři, který je stejný jako primární úlohy a dílčí každý úkol [úkol s více instancemi][multi_instance]. Zadaná cesta existuje na všech uzlech, na kterém s více instancemi úloha běží a je přístupný pro na tomto uzlu spuštěna úloha příkazy pro čtení a zápis (jak [koordinaci příkaz] [ coord_cmd] a [příkaz aplikace][app_cmd]). Dílčí úkoly nebo primární úlohu, která spustit na jiných uzlech nemá vzdáleného přístupu do tohoto adresáře (není adresář "sdílené" sítě). | S více instancemi primární a dílčí úkoly. | C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | Určuje, zda hlavní uzel pro aktuální uzel [úkol s více instancemi][multi_instance]. Možné hodnoty jsou `true` a `false`.| S více instancemi primární a dílčí úkoly. | `true` |
| AZ_BATCH_NODE_IS_DEDICATED | Pokud `true`, aktuální uzel je vyhrazené uzlu. Pokud `false`, je [nízkou prioritu uzlu](batch-low-pri-vms.md). | Všechny úlohy. | `true` |

[files_dirs]: https://azure.microsoft.com/documentation/articles/batch-api-basics/#files-and-directories
[multi_instance]: https://azure.microsoft.com/documentation/articles/batch-mpi/
[coord_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#coordination-command
[app_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#application-command
