---
title: Proměnné prostředí za běhu úloh – Azure Batch | Dokumenty společnosti Microsoft
description: Pokyny a reference proměnných prostředí za běhu úloh pro Azure Batch Analytics.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 09/12/2019
ms.author: labrenne
ms.openlocfilehash: ebaa06acf309a0f941b8b4efd76fa4e9e7460810
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053953"
---
# <a name="azure-batch-runtime-environment-variables"></a>Proměnné prostředí runtime Azure Batch

[Služba Azure Batch](https://azure.microsoft.com/services/batch/) nastaví následující proměnné prostředí na výpočetních uzlech. Tyto proměnné prostředí můžete odkazovat v řádcích příkazů úloh a v programech a skriptech spuštěných příkazovými řádky.

Další informace o používání proměnných prostředí s dávkou naleznete v [tématu Nastavení prostředí pro úkoly](https://docs.microsoft.com/azure/batch/batch-api-basics#environment-settings-for-tasks).

## <a name="environment-variable-visibility"></a>Viditelnost proměnných prostředí

Tyto proměnné prostředí jsou viditelné pouze v kontextu **uživatele úlohy**, uživatelského účtu v uzlu, pod kterým je úloha prováděna. *Neuvidíte je*, pokud se k výpočetnímu uzlu [vzdáleně připojíte](https://azure.microsoft.com/documentation/articles/batch-api-basics/#connecting-to-compute-nodes) prostřednictvím protokolu RDP (Remote Desktop) nebo Secure Shell (SSH) a vypíšete si seznam proměnných prostředí. Je to proto, že uživatelský účet, který se používá pro vzdálené připojení, není stejný jako účet, který je používán úkolem.

Chcete-li získat aktuální hodnotu `cmd.exe` proměnné prostředí, spusťte na výpočetním uzlu Windows nebo `/bin/sh` na uzlu Linux:

`cmd /c set <ENV_VARIABLE_NAME>`

`/bin/sh printenv <ENV_VARIABLE_NAME>`

## <a name="command-line-expansion-of-environment-variables"></a>Rozšíření proměnných prostředí příkazového řádku

Příkazové řádky prováděné úlohami na výpočetních uzlech nespouštějí pod prostředím. Proto tyto příkazové řádky nemohou nativně využívat funkce prostředí, jako `PATH`je například rozšíření proměnné prostředí (to zahrnuje ). Chcete-li tyto funkce využít, musíte **vyvolat prostředí** v příkazovém řádku. Spuštění `cmd.exe` například na výpočetních `/bin/sh` uzlech Windows nebo na linuxových uzlech:

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

## <a name="environment-variables"></a>Proměnné prostředí

| Název proměnné                     | Popis                                                              | Dostupnost | Příklad |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | Název účtu Batch, ke kterému úkol patří.                  | Všechny úkoly.   | mybatchaccount |
| AZ_BATCH_ACCOUNT_URL            | Adresa URL účtu Batch. | Všechny úkoly. | `https://myaccount.westus.batch.azure.com` |
| AZ_BATCH_APP_PACKAGE            | Předpona všech proměnných prostředí balíčku aplikace. Například pokud aplikace "FOO" verze "1" je nainstalován do fondu, proměnná prostředí je AZ_BATCH_APP_PACKAGE_FOO_1 (na Linuxu) nebo AZ_BATCH_APP_PACKAGE_FOO #1 (v systému Windows). AZ_BATCH_APP_PACKAGE_FOO_1 odkazuje na umístění, ve které byl balíček stažen (složka). Při použití výchozí verze balíčku aplikace použijte proměnnou prostředí AZ_BATCH_APP_PACKAGE bez čísel verzí. Pokud v Linuxu a název balíčku aplikace je "Agent-linux-x64" a verze je "1.1.46.0, název prostředí je vlastně: AZ_BATCH_APP_PACKAGE_agent_linux_x64_1_1_46_0, pomocí podtržítka a malá písmena. Další podrobnosti najdete [tady](https://docs.microsoft.com/azure/batch/batch-application-packages#execute-the-installed-applications). | Jakýkoli úkol s přidruženým balíčkem aplikace. K dispozici také pro všechny úkoly, pokud uzel sám má balíčky aplikací. | AZ_BATCH_APP_PACKAGE_FOO_1 (Linux) nebo AZ_BATCH_APP_PACKAGE_FOO #1 (Windows) |
| AZ_BATCH_AUTHENTICATION_TOKEN   | Ověřovací token, který uděluje přístup k omezené sadě operací služby Batch. Tato proměnná prostředí je k dispozici pouze v případě, [že authenticationTokenSettings](/rest/api/batchservice/task/add#authenticationtokensettings) jsou nastaveny při [přidání úlohy](/rest/api/batchservice/task/add#request-body). Hodnota tokenu se používá v rozhraní API dávky jako pověření k vytvoření klienta batch, například v [rozhraní BatchClient.Open() rozhraní API .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_). | Všechny úkoly. | Přístupový token OAuth2 |
| AZ_BATCH_CERTIFICATES_DIR       | Adresář v rámci [pracovního adresáře úlohy,][files_dirs] ve kterém jsou uloženy certifikáty pro výpočetní uzly Linuxu. Tato proměnná prostředí se nevztahuje na výpočetní uzly systému Windows.                                                  | Všechny úkoly.   |  /mnt/batch/tasks/workitems/batchjob001/job-1/task001/certs |
| AZ_BATCH_HOST_LIST              | Seznam uzlů, které jsou přiděleny k úkolu s `nodeIP,nodeIP`více [instancemi][multi_instance] ve formátu . | Primární a dílčí úkoly s více instancemi. | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | Určuje, zda je aktuální uzel hlavním uzlem [pro úlohu s více instancemi][multi_instance]. Možné hodnoty `true` `false`jsou a .| Primární a dílčí úkoly s více instancemi. | `true` |
| AZ_BATCH_JOB_ID                 | Číslo ID úlohy, ke které úkol patří. | Všechny úkoly kromě zahájení úkolu. | dávková úloha001 |
| AZ_BATCH_JOB_PREP_DIR           | Úplná cesta k [adresáři úlohy][files_dirs] přípravy úlohy úlohy úlohy v uzlu. | Všechny úkoly kromě zahájení úkolu a úlohy přípravy úkolu. K dispozici pouze v případě, že je úloha nakonfigurována s úlohou přípravy úlohy. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_WORKING_DIR   | Úplná cesta [pracovního adresáře úlohy][files_dirs] přípravy úlohy v uzlu. | Všechny úkoly kromě zahájení úkolu a úlohy přípravy úkolu. K dispozici pouze v případě, že je úloha nakonfigurována s úlohou přípravy úlohy. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_MASTER_NODE            | Ip adresa a port výpočetního uzlu, na kterém je spuštěn primární úkol [úlohy s více instancemi.][multi_instance] | Primární a dílčí úkoly s více instancemi. | `10.0.0.4:6000` |
| AZ_BATCH_NODE_ID                | ID uzlu, ke kterému je úkol přiřazen. | Všechny úkoly. | tvm-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_IS_DEDICATED      | Pokud `true`je aktuální uzel vyhrazený uzel. Pokud `false`se jedná o [uzel s nízkou prioritou](batch-low-pri-vms.md). | Všechny úkoly. | `true` |
| AZ_BATCH_NODE_LIST              | Seznam uzlů, které jsou přiděleny k úkolu s `nodeIP;nodeIP`více [instancemi][multi_instance] ve formátu . | Primární a dílčí úkoly s více instancemi. | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_NODE_MOUNTS_DIR        | Úplná cesta umístění [připojení systému souborů](virtual-file-mount.md) na úrovni uzlu, kde jsou umístěny všechny připojovací adresáře. Sdílené složky systému Windows používají písmeno jednotky, takže pro systém Windows je přípojná jednotka součástí zařízení a jednotek.  |  Všechny úkoly včetně úlohy spuštění mají přístup k uživateli, vzhledem k tomu, že uživatel si je vědom oprávnění k připojení pro připojený adresář. | V Ubuntu, například, umístění je:`/mnt/batch/tasks/fsmounts` |
| AZ_BATCH_NODE_ROOT_DIR          | Úplná cesta kořenového adresáře všech [částí adresářů][files_dirs] v uzlu. | Všechny úkoly. | C:\uživatel\úkoly |
| AZ_BATCH_NODE_SHARED_DIR        | Úplná cesta [sdíleného adresáře][files_dirs] v uzlu. Všechny úkoly, které se provádějí v uzlu, mají přístup pro čtení a zápis do tohoto adresáře. Úlohy, které se provádějí v jiných uzlech, nemají vzdálený přístup k tomuto adresáři (nejedná se o "sdílený" síťový adresář). | Všechny úkoly. | C:\uživatel\úkoly\sdíleno |
| AZ_BATCH_NODE_STARTUP_DIR       | Úplná cesta [k adresáři počáteční úlohy][files_dirs] v uzlu. | Všechny úkoly. | C:\uživatel\úkoly\spuštění |
| AZ_BATCH_POOL_ID                | Číslo ID fondu, ve kterém je spuštěn úkol. | Všechny úkoly. | dávková dávka001 |
| AZ_BATCH_TASK_DIR               | Úplná cesta [k adresáři úkolů][files_dirs] v uzlu. Tento adresář `stdout.txt` obsahuje `stderr.txt` a pro úkol a AZ_BATCH_TASK_WORKING_DIR. | Všechny úkoly. | C:\user\tasks\workitems\batchjob001\job-1\task001 C:\user\tasks\workitems\batchjob001\job-1\task001 C:\user\tasks\workitems\batchjob001\job-1\task001 C: |
| AZ_BATCH_TASK_ID                | Číslo ID aktuálního úkolu. | Všechny úkoly kromě zahájení úkolu. | úkol001 |
| AZ_BATCH_TASK_SHARED_DIR | Cesta k adresáři, která je identická pro primární úkol a každý dílčí úkol [úlohy s více instancemi][multi_instance]. Cesta existuje na každém uzlu, na kterém je spuštěna úloha s více instancemi, a je pro čtení a zápis přístupná příkazům úlohy spuštěným na tomto uzlu [(příkaz koordinace][coord_cmd] i [příkaz aplikace).][app_cmd] Dílčí úkoly nebo primární úlohy, které se provádějí v jiných uzlech, nemají vzdálený přístup k tomuto adresáři (nejedná se o "sdílený" síťový adresář). | Primární a dílčí úkoly s více instancemi. | C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask C: |
| AZ_BATCH_TASK_WORKING_DIR       | Úplná cesta [k pracovnímu adresáři úkolu][files_dirs] v uzlu. Aktuálně spuštěná úloha má přístup pro čtení a zápis do tohoto adresáře. | Všechny úkoly. | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| CCP_NODES                       | Seznam uzlů a počet jader na uzel, které jsou přiděleny [k úloze s více instancemi][multi_instance]. Uzly a jádra jsou uvedeny ve formátu`numNodes<space>node1IP<space>node1Cores<space>`<br/>`node2IP<space>node2Cores<space> ...`, kde za počtem uzlů následuje jeden nebo více IP adres uzlů a počet jader pro každou z nich. |  Primární a dílčí úkoly s více instancemi. |`2 10.0.0.4 1 10.0.0.5 1` |

[files_dirs]: https://azure.microsoft.com/documentation/articles/batch-api-basics/#files-and-directories
[multi_instance]: https://azure.microsoft.com/documentation/articles/batch-mpi/
[coord_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#coordination-command
[app_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#application-command
