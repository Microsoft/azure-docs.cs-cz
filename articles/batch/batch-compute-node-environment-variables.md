---
title: Proměnné prostředí modulu runtime úloh
description: Doprovodné materiály k proměnnou prostředí modulu runtime úlohy a referenční informace pro Azure Batch Analytics.
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 6b8ade312146802ede6e12181a082a8fcd3842fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85960907"
---
# <a name="azure-batch-runtime-environment-variables"></a>Proměnné prostředí Azure Batch runtime

[Služba Azure Batch](https://azure.microsoft.com/services/batch/) v výpočetních uzlech nastaví následující proměnné prostředí. Na tyto proměnné prostředí můžete odkazovat na příkazových řádcích úkolů a v programech a skriptech spouštěných pomocí příkazových řádků.

Další informace o používání proměnných prostředí ve službě Batch najdete v tématu [nastavení prostředí pro úlohy](./jobs-and-tasks.md#environment-settings-for-tasks).

## <a name="environment-variable-visibility"></a>Viditelnost proměnné prostředí

Tyto proměnné prostředí jsou viditelné pouze v kontextu **uživatele úkolu**, uživatelský účet v uzlu, pod nímž je úloha spuštěna. *Neuvidíte je*, pokud se k výpočetnímu uzlu [vzdáleně připojíte](./error-handling.md#connect-to-compute-nodes) prostřednictvím protokolu RDP (Remote Desktop) nebo Secure Shell (SSH) a vypíšete si seznam proměnných prostředí. Je to proto, že uživatelský účet, který se používá pro vzdálené připojení, není stejný jako účet, který je používán úkolem.

Chcete-li získat aktuální hodnotu proměnné prostředí, spusťte `cmd.exe` program na výpočetním uzlu Windows nebo `/bin/sh` na uzlu Linux:

`cmd /c set <ENV_VARIABLE_NAME>`

`/bin/sh printenv <ENV_VARIABLE_NAME>`

## <a name="command-line-expansion-of-environment-variables"></a>Rozšíření proměnných prostředí z příkazového řádku

Příkazové řádky spouštěné úkoly na výpočetních uzlech neběží v prostředí. Proto tyto příkazové řádky nemohou nativně využívat funkce prostředí, jako je například rozšíření proměnné prostředí (to zahrnuje i `PATH` ). Chcete-li využít výhod těchto funkcí, je nutné **vyvolat prostředí** v příkazovém řádku. Například spusťte `cmd.exe` na výpočetních uzlech systému Windows nebo `/bin/sh` na uzlech se systémem Linux:

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

## <a name="environment-variables"></a>Proměnné prostředí

| Název proměnné                     | Description                                                              | Dostupnost | Příklad |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | Název účtu Batch, ke kterému úkol patří                  | Všechny úlohy.   | mybatchaccount |
| AZ_BATCH_ACCOUNT_URL            | Adresa URL účtu Batch. | Všechny úlohy. | `https://myaccount.westus.batch.azure.com` |
| AZ_BATCH_APP_PACKAGE            | Předpona všech proměnných prostředí balíčku aplikace. Například pokud je aplikace "FOO" verze "1" nainstalována do fondu, proměnná prostředí je AZ_BATCH_APP_PACKAGE_FOO_1 (v systému Linux) nebo AZ_BATCH_APP_PACKAGE_FOO č. 1 (ve Windows). AZ_BATCH_APP_PACKAGE_FOO_1 odkazuje na umístění, do kterého se balíček stáhl (složka). Při použití výchozí verze balíčku aplikace použijte proměnnou prostředí AZ_BATCH_APP_PACKAGE bez čísel verzí. Pokud je v systému Linux a název balíčku aplikace je "agent-Linux-x64" a verze je "1.1.46.0", název prostředí je ve skutečnosti: AZ_BATCH_APP_PACKAGE_agent_linux_x64_1_1_46_0, s použitím podtržítka a malých písmen. Další podrobnosti najdete [tady](./batch-application-packages.md#execute-the-installed-applications). | Libovolný úkol s přidruženým balíčkem aplikace. K dispozici také pro všechny úlohy, pokud uzel obsahuje balíčky aplikací. | AZ_BATCH_APP_PACKAGE_FOO_1 (Linux) nebo AZ_BATCH_APP_PACKAGE_FOO č. 1 (Windows) |
| AZ_BATCH_AUTHENTICATION_TOKEN   | Ověřovací token, který uděluje přístup k omezené sadě operací služby Batch. Tato proměnná prostředí je přítomna pouze v případě, že je [authenticationTokenSettings](/rest/api/batchservice/task/add#authenticationtokensettings) nastavena při [Přidání úkolu](/rest/api/batchservice/task/add#request-body). Hodnota tokenu se používá v rozhraních API dávky jako přihlašovací údaje k vytvoření klienta Batch, jako je například v rozhraní [.NET API BatchClient. Open ()](/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_). | Všechny úlohy. | Přístupový token OAuth2 |
| AZ_BATCH_CERTIFICATES_DIR       | Adresář v [pracovním adresáři úkolu][files_dirs] , ve kterém jsou uloženy certifikáty pro výpočetní uzly Linux. Tato proměnná prostředí se nevztahuje na výpočetní uzly Windows.                                                  | Všechny úlohy.   |  /mnt/batch/tasks/workitems/batchjob001/job-1/task001/certs |
| AZ_BATCH_HOST_LIST              | Seznam uzlů, které jsou přiděleny úloze s [více instancemi][multi_instance] ve formátu `nodeIP,nodeIP` . | Primární a dílčí úkoly s více instancemi. | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | Určuje, zda je aktuální uzel hlavním uzlem pro [úlohu s více instancemi][multi_instance]. Možné hodnoty jsou `true` a `false` .| Primární a dílčí úkoly s více instancemi. | `true` |
| AZ_BATCH_JOB_ID                 | Číslo ID úlohy, ke které úkol patří. | Všechny úlohy s výjimkou spouštěcího úkolu. | batchjob001 |
| AZ_BATCH_JOB_PREP_DIR           | Úplná cesta [adresáře úkolu][files_dirs] přípravy úlohy na uzlu. | Všechny úlohy kromě úlohy spustit úlohu a příprava úlohy. K dispozici pouze v případě, že je úloha konfigurována s úlohou přípravy úlohy. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_WORKING_DIR   | Úplná cesta [pracovního adresáře úkolu][files_dirs] přípravy úlohy na uzlu | Všechny úlohy kromě úlohy spustit úlohu a příprava úlohy. K dispozici pouze v případě, že je úloha konfigurována s úlohou přípravy úlohy. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_MASTER_NODE            | IP adresa a port výpočetního uzlu, na kterém se spouští primární úloha [úlohy s více instancemi][multi_instance] . Nepoužívejte zde zadaný port pro komunikaci MPI nebo NCCL – je vyhrazený pro službu Azure Batch. Místo toho použijte MASTER_PORT proměnné, a to buď nastavením hodnoty předané prostřednictvím argumentu příkazového řádku (port 6105 je dobrá výchozí volba), nebo pomocí hodnoty AML sady, pokud k tomu dojde. | Primární a dílčí úkoly s více instancemi. | `10.0.0.4:6000` |
| AZ_BATCH_NODE_ID                | ID uzlu, ke kterému je úkol přiřazen | Všechny úlohy. | TVM – 1219235766_3 – 20160919t172711z |
| AZ_BATCH_NODE_IS_DEDICATED      | Pokud `true` je aktuální uzel vyhrazený uzel. `false`Je-li, jedná se o [uzel s nízkou prioritou](batch-low-pri-vms.md). | Všechny úlohy. | `true` |
| AZ_BATCH_NODE_LIST              | Seznam uzlů, které jsou přiděleny úloze s [více instancemi][multi_instance] ve formátu `nodeIP;nodeIP` . | Primární a dílčí úkoly s více instancemi. | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_NODE_MOUNTS_DIR        | Úplná cesta k umístění pro [připojení systému souborů](virtual-file-mount.md) na úrovni uzlu, kde jsou umístěny všechny adresáře připojení. Sdílené složky systému Windows používají písmeno jednotky, takže pro Windows je připojená jednotka součástí zařízení a jednotek.  |  Všechny úlohy, včetně spouštěcího úkolu, mají přístup k uživateli, protože uživatel ví o oprávněních k připojení k připojenému adresáři. | V Ubuntu je například umístění: `/mnt/batch/tasks/fsmounts` |
| AZ_BATCH_NODE_ROOT_DIR          | Úplná cesta kořene všech [adresářů Batch][files_dirs] na uzlu. | Všechny úlohy. | C:\user\tasks |
| AZ_BATCH_NODE_SHARED_DIR        | Úplná cesta ke [sdílenému adresáři][files_dirs] na uzlu. Všechny úlohy, které se spouštějí na uzlu, mají k tomuto adresáři přístup pro čtení a zápis. Úlohy, které se spouštějí na jiných uzlech, nemají vzdálený přístup k tomuto adresáři (nejedná se o sdílený síťový adresář). | Všechny úlohy. | C:\user\tasks\shared |
| AZ_BATCH_NODE_STARTUP_DIR       | Úplná cesta k [adresáři spouštěcího úkolu][files_dirs] na uzlu. | Všechny úlohy. | C:\user\tasks\startup |
| AZ_BATCH_POOL_ID                | Číslo ID fondu, ve kterém je spuštěn úkol. | Všechny úlohy. | batchpool001 |
| AZ_BATCH_TASK_DIR               | Úplná cesta k [adresáři úloh][files_dirs] na uzlu. Tento adresář obsahuje `stdout.txt` a `stderr.txt` pro úlohu a AZ_BATCH_TASK_WORKING_DIR. | Všechny úlohy. | C:\user\tasks\workitems\batchjob001\job-1\task001 |
| AZ_BATCH_TASK_ID                | Číslo ID aktuálního úkolu. | Všechny úlohy s výjimkou spouštěcího úkolu. | task001 |
| AZ_BATCH_TASK_SHARED_DIR | Cesta k adresáři, která je stejná pro primární úlohu a každý dílčí úkol úlohy s [více instancemi][multi_instance]. Cesta existuje na každém uzlu, na kterém je spuštěná úloha s více instancemi, a je přístup pro čtení a zápis dostupný pro příkazy úkolu spuštěné v tomto uzlu ( [příkaz koordinace][coord_cmd] i [příkaz aplikace][app_cmd]). Dílčí úkoly nebo primární úlohy, které se spouštějí na jiných uzlech, nemají vzdálený přístup k tomuto adresáři (nejedná se o sdílený síťový adresář). | Primární a dílčí úkoly s více instancemi. | C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask |
| AZ_BATCH_TASK_WORKING_DIR       | Úplná cesta [pracovního adresáře úkolu][files_dirs] na uzlu. Aktuálně spuštěná úloha má k tomuto adresáři přístup pro čtení a zápis. | Všechny úlohy. | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| CCP_NODES                       | Seznam uzlů a počet jader na uzel, které jsou přiděleny k [úloze s více instancemi][multi_instance]. Uzly a jádra jsou uvedené ve formátu. `numNodes<space>node1IP<space>node1Cores<space>`<br/>`node2IP<space>node2Cores<space> ...`, kde je počet uzlů následován jednou nebo více IP adresami uzlu a počtem jader pro každý z nich. |  Primární a dílčí úkoly s více instancemi. |`2 10.0.0.4 1 10.0.0.5 1` |

[files_dirs]: ./files-and-directories.md
[multi_instance]: ./batch-mpi.md
[coord_cmd]: ./batch-mpi.md#coordination-command
[app_cmd]: ./batch-mpi.md#application-command
