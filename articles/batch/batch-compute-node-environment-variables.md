---
title: Proměnné prostředí výpočetních uzlů – Azure Batch | Dokumentace Microsoftu
description: Výpočetní uzel odkazu na proměnnou prostředí pro Azure Batch Analytics.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 01/03/2019
ms.author: lahugh
ms.openlocfilehash: 48c2172e02e935dde28ac323c776c8895b1d36b2
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54017357"
---
# <a name="azure-batch-compute-node-environment-variables"></a>Azure proměnné prostředí výpočetních uzlů služby Batch

[Služby Azure Batch](https://azure.microsoft.com/services/batch/) nastaví následující proměnné prostředí na výpočetních uzlech. Vám může odkazovat na tyto proměnné prostředí na příkazových řádcích úkolů a v aplikacích a spustit skripty těmito příkazovými řádky.

Další informace o použití proměnných prostředí pomocí služby Batch najdete v tématu [nastavení prostředí pro úkoly](https://docs.microsoft.com/azure/batch/batch-api-basics#environment-settings-for-tasks).

## <a name="environment-variable-visibility"></a>Viditelnost proměnné prostředí

Tyto proměnné prostředí jsou viditelné pouze v kontextu **uživatele úkolu**, uživatelský účet v uzlu, pod kterým je úkol spuštěn. *Neuvidíte je*, pokud se k výpočetnímu uzlu [vzdáleně připojíte](https://azure.microsoft.com/documentation/articles/batch-api-basics/#connecting-to-compute-nodes) prostřednictvím protokolu RDP (Remote Desktop) nebo Secure Shell (SSH) a vypíšete si seznam proměnných prostředí. Je to proto, že uživatelský účet, který se používá pro vzdálené připojení, není stejný jako účet, který je používán úkolem.

## <a name="command-line-expansion-of-environment-variables"></a>Příkazový řádek rozšíření proměnných prostředí

Příkazové řádky provedených úloh na výpočetních uzlech nespouštějte v shellu. Proto tyto příkazové řádky nemůže nativně využívat funkce shellu, jako je například rozšíření proměnné prostředí (to zahrnuje `PATH`). Abyste mohli využívat funkce, je nutné **vyvolejte shell** na příkazovém řádku. Příklad: launch `cmd.exe` na Windows výpočetních uzlů nebo `/bin/sh` na uzly s Linuxem:

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

## <a name="environment-variables"></a>Proměnné prostředí

| Název proměnné                     | Popis                                                              | Dostupnost | Příklad: |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | Název účtu Batch, které úkol patří.                  | Všechny úlohy.   | mybatchaccount |
| AZ_BATCH_AUTHENTICATION_TOKEN   | Ověřovací token, který uděluje přístup pro omezenou sadu operací služby Batch. Tato proměnná prostředí je k dispozici pouze pokud [authenticationTokenSettings](/rest/api/batchservice/task/add#authenticationtokensettings) jsou nastaveny, jakmile [se přidá úkol](/rest/api/batchservice/task/add#request-body). Hodnota tokenu se používá v rozhraní API služby Batch jako přihlašovací údaje k vytvoření klienta služby Batch, jako například v [BatchClient.Open() .NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_). | Všechny úlohy. | Přístupový token OAuth2 |
| AZ_BATCH_CERTIFICATES_DIR       | Adresáře v rámci [pracovního adresáře úkolu] [ files_dirs] v které certifikáty jsou uloženy pro Linux výpočetních uzlů. Všimněte si, že tato proměnná prostředí se nedá použít u Windows výpočetních uzlů.                                                  | Všechny úlohy.   |  /mnt/batch/Tasks/workitems/batchjob001/Job-1/task001/certs |
| AZ_BATCH_JOB_ID                 | Číslo ID úlohy, ke které úkol patří. | Všechny úlohy, s výjimkou spuštění úlohy. | batchjob001 |
| AZ_BATCH_JOB_PREP_DIR           | Úplná cesta přípravy úlohy [adresáře úkolu] [ files_dirs] na uzlu. | Všechny úlohy s výjimkou spouštěcího úkolu a úkol přípravy úlohy. K dispozici pouze pokud jsou nakonfigurované úloha Úkol Příprava úlohy. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_WORKING_DIR   | Úplná cesta přípravy úlohy [pracovního adresáře úkolu] [ files_dirs] na uzlu. | Všechny úlohy s výjimkou spouštěcího úkolu a úkol přípravy úlohy. K dispozici pouze pokud jsou nakonfigurované úloha Úkol Příprava úlohy. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_NODE_ID                | ID uzlu, který úkol je přidělen. | Všechny úlohy. | tvm-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_ROOT_DIR          | Úplná cesta kořenového všech [Batch adresáře] [ files_dirs] na uzlu. | Všechny úlohy. | C:\user\tasks |
| AZ_BATCH_NODE_SHARED_DIR        | Úplná cesta [sdíleného adresáře] [ files_dirs] na uzlu. Všechny úkoly, které jsou spouštěny na uzlu budou mít přístup pro čtení a zápis do tohoto adresáře. Úlohy, které jsou spouštěny na jiných uzlech nemají vzdálený přístup k tomuto adresáři (není adresář "sdílené"). | Všechny úlohy. | C:\user\tasks\shared |
| AZ_BATCH_NODE_STARTUP_DIR       | Úplná cesta [start adresáře úkolu] [ files_dirs] na uzlu. | Všechny úlohy. | C:\user\tasks\startup |
| AZ_BATCH_POOL_ID                | Číslo ID fondu, ve kterém je spuštěn úkol. | Všechny úlohy. | batchpool001 |
| AZ_BATCH_TASK_DIR               | Úplná cesta [adresáře úkolu] [ files_dirs] na uzlu. Tento adresář obsahuje `stdout.txt` a `stderr.txt` úkolu a AZ_BATCH_TASK_WORKING_DIR. | Všechny úlohy. | C:\user\tasks\workitems\batchjob001\job-1\task001 |
| AZ_BATCH_TASK_ID                | Číslo ID aktuálního úkolu. | Všechny úlohy, s výjimkou spuštění úlohy. | task001 |
| AZ_BATCH_TASK_WORKING_DIR       | Úplná cesta [pracovního adresáře úkolu] [ files_dirs] na uzlu. Aktuálně spuštěná úloha má přístup pro čtení a zápis do tohoto adresáře. | Všechny úlohy. | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| CCP_NODES                       | Seznam uzlů a počet jader na uzel, který jsou přiděleny [úkoly s více instancemi][multi_instance]. Uzly a počet jader, které jsou uvedeny ve formátu `numNodes<space>node1IP<space>node1Cores<space>`<br/>`node2IP<space>node2Cores<space> ...`, kde počet uzlů následuje jedna nebo víc IP adres uzlu a počet jader pro každý. |  Primární více instancemi a dílčí úkoly. |`2 10.0.0.4 1 10.0.0.5 1` |
| AZ_BATCH_NODE_LIST              | Seznam uzlů, které jsou přiděleny [úkoly s více instancemi] [ multi_instance] ve formátu `nodeIP;nodeIP`. | Primární více instancemi a dílčí úkoly. | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_HOST_LIST              | Seznam uzlů, které jsou přiděleny [úkoly s více instancemi] [ multi_instance] ve formátu `nodeIP,nodeIP`. | Primární více instancemi a dílčí úkoly. | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_MASTER_NODE            | IP adresy a portu výpočetním uzlu, na kterém primární úlohy [úkoly s více instancemi] [ multi_instance] běží. | Primární více instancemi a dílčí úkoly. | `10.0.0.4:6000`|
| AZ_BATCH_TASK_SHARED_DIR | Cestu k adresáři, který je stejný jako primární úlohy a každý dílčí úkol [úkoly s více instancemi][multi_instance]. Cesta existuje na všech uzlech, na kterém více instancemi úloha běží a je pro čtení/zápis přístupné pro příkazy úlohy běžící na tomto uzlu (jak [koordinace příkaz] [ coord_cmd] a [ příkaz aplikace][app_cmd]). Dílčí úkoly nebo primární úkol spustit na jiných uzlech nemají vzdálený přístup k tomuto adresáři (není adresář "sdílené"). | Primární více instancemi a dílčí úkoly. | C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | Určuje, zda je aktuální uzel hlavní uzel [úkoly s více instancemi][multi_instance]. Možné hodnoty jsou `true` a `false`.| Primární více instancemi a dílčí úkoly. | `true` |
| AZ_BATCH_NODE_IS_DEDICATED | Pokud `true`, aktuální uzel je vyhrazených uzlů. Pokud `false`, jde [uzly s nízkou prioritou](batch-low-pri-vms.md). | Všechny úlohy. | `true` |

[files_dirs]: https://azure.microsoft.com/documentation/articles/batch-api-basics/#files-and-directories
[multi_instance]: https://azure.microsoft.com/documentation/articles/batch-mpi/
[coord_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#coordination-command
[app_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#application-command
