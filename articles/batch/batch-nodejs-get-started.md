---
title: Kurz – použití klientské knihovny Azure Batch pro Node.js | Dokumentace Microsoftu
description: Informace o základních konceptech služby Azure Batch a vytvoření jednoduchého řešení pomocí Node.js.
services: batch
author: shwetams
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: nodejs
ms.topic: hero-article
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: shwetams
ms.openlocfilehash: 807fd49a54c82b0930134beb8413e14c1c28b278
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2018
ms.locfileid: "39115557"
---
# <a name="get-started-with-batch-sdk-for-nodejs"></a>Začínáme se sadou SDK služby Batch pro Node.js

Naučíte se základy vytvoření klienta služby Batch v Node.js pomocí sady [SDK služby Azure Batch pro Node.js](/javascript/api/overview/azure/batch). Pro pochopení scénáře pro aplikaci služby Batch si ho projdeme krok za krokem a pak aplikaci nastavíme pomocí klienta Node.js.  

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že máte praktické znalosti Node.js a umíte do jisté míry pracovat s Linuxem. Předpokládá se také, že máte nastavený účet Azure s přístupovými právy k vytvoření služeb Batch a Storage.

Doporučujeme, abyste si přečetli článek [Technický přehled služby Azure Batch](batch-technical-overview.md), než budete postupovat podle kroků popsaných v tomto článku.

## <a name="the-tutorial-scenario"></a>Scénář tohoto kurzu
Podívejme se na scénář pracovního postupu služby Batch. Máme jednoduchý skript napsaný v Pythonu, který z kontejneru služby Azure Blob Storage stáhne všechny soubory CSV a převede je do formátu JSON. Pokud chceme paralelně zpracovávat více kontejnerů účtu úložiště, můžeme skript nasadit jako úlohu služby Azure Batch.

## <a name="azure-batch-architecture"></a>Architektura služby Azure Batch
Následující diagram znázorňuje možnost škálování skriptu Pythonu pomocí služby Azure Batch a klienta Node.js.

![Scénář služby Azure Batch](./media/batch-nodejs-get-started/BatchScenario.png)

Klient Node.js nasadí dávkovou úlohu s přípravným úkolem (podrobně si ho popíšeme později) a sadou úkolů v závislosti na počtu kontejnerů v účtu úložiště. Skripty si můžete stáhnout z úložiště GitHub.

* [Node.js](https://github.com/Azure/azure-batch-samples/blob/master/Node.js/GettingStarted/nodejs_batch_client_sample.js)
* [Skripty prostředí pro přípravný úkol](https://github.com/Azure/azure-batch-samples/blob/master/Node.js/GettingStarted/startup_prereq.sh)
* [Procesor formátu CSV do formátu JSON v Pythonu](https://github.com/Azure/azure-batch-samples/blob/master/Node.js/GettingStarted/processcsv.py)

> [!TIP]
> Klient Node.js na uvedeném odkazu neobsahuje konkrétní kód umožňující nasadit ho jako aplikaci Azure Function App. Na následujících odkazech najdete pokyny k jejímu vytvoření.
> - [Vytvoření aplikace Function App](../azure-functions/functions-create-first-azure-function.md)
> - [Vytvoření funkce pro aktivaci časovače](../azure-functions/functions-bindings-timer.md)
>
>

## <a name="build-the-application"></a>Sestavení aplikace

Nyní si krok za krokem projdeme vytvoření klienta Node.js:

### <a name="step-1-install-azure-batch-sdk"></a>Krok 1: Nainstalování sady SDK služby Azure Batch

Sadu SDK služby Azure Batch pro Node.js můžete nainstalovat pomocí příkazu npm install.

`npm install azure-batch`

Tento příkaz nainstaluje nejnovější verzi sady azure-batch node SDK.

>[!Tip]
> V aplikaci Azure Function App můžete spouštět příkazy npm install z konzoly Kudu na kartě Nastavení funkce Azure. V tomto případě k instalaci sady SDK služby Azure Batch pro Node.js.
>
>

### <a name="step-2-create-an-azure-batch-account"></a>Krok 2: Vytvoření účtu Azure Batch

Můžete ho vytvořit na webu [Azure Portal](batch-account-create-portal.md) nebo z příkazového řádku ([PowerShell](batch-powershell-cmdlets-get-started.md)  / [Azure CLI](/cli/azure)).

Následující příkazy vytvoří účet prostřednictvím Azure CLI.

Vytvořte skupinu prostředků (tento krok přeskočte, pokud již máte skupinu prostředků, ve které chcete vytvořit účet Batch):

`az group create -n "<resource-group-name>" -l "<location>"`

Dále vytvořte účet Azure Batch.

`az batch account create -l "<location>"  -g "<resource-group-name>" -n "<batch-account-name>"`

Každý účet Batch má odpovídající přístupové klíče. Tyto klíče jsou potřeba k vytvoření dalších prostředků v účtu Azure Batch. V produkčním prostředí je vhodné použít k uložení těchto klíčů službu Azure Key Vault. Potom můžete pro aplikaci vytvořit instanční objekt. Pomocí tohoto instančního objektu může aplikace vytvořit token OAuth pro přístup ke klíčům ve službě Key Vault.

`az batch account keys list -g "<resource-group-name>" -n "<batch-account-name>"`

Zkopírujte a uložte klíč, který použijete v dalších krocích.

### <a name="step-3-create-an-azure-batch-service-client"></a>Krok 3: Vytvoření klienta služby Azure Batch
Následující fragment kódu nejprve importuje modul Node.js azure-batch a pak vytvoří klienta služby Batch. Nejprve musíte vytvořit objekt SharedKeyCredentials s klíčem účtu Batch, který jste zkopírovali v předchozím kroku.

```nodejs
// Initializing Azure Batch variables

var batch = require('azure-batch');

var accountName = '<azure-batch-account-name>';

var accountKey = '<account-key-downloaded>';

var accountUrl = '<account-url>'

// Create Batch credentials object using account name and account key

var credentials = new batch.SharedKeyCredentials(accountName,accountKey);

// Create Batch service client

var batch_client = new batch.ServiceClient(credentials,accountUrl);

```

Identifikátor URI služby Azure Batch najdete na kartě Přehled na webu Azure Portal. Je ve formátu:

`https://accountname.location.batch.azure.com`

Podívejte se na snímek obrazovky:

![Identifikátor URI služby Azure Batch](./media/batch-nodejs-get-started/azurebatchuri.png)



### <a name="step-4-create-an-azure-batch-pool"></a>Krok 4: Vytvoření fondu služby Azure Batch
Fond služby Azure Batch se skládá z několika virtuálních počítačů (označovaných také jako uzly služby Batch). Služba Azure Batch do uzlů nasazuje úkoly a spravuje je. Pro váš fond můžete definovat následující parametry konfigurace.

* Typ image virtuálních počítačů
* Velikost uzlů virtuálních počítačů
* Počet uzlů virtuálních počítačů

> [!Tip]
> Velikost a počet uzlů virtuálních počítačů úzce souvisí s počtem úkolů, které chcete paralelně spouštět, a se samotnými úkoly. K určení ideálního počtu a velikosti doporučujeme otestovat různé možnosti.
>
>

Následující fragment kódu vytvoří objekty parametrů konfigurace.

```nodejs
// Creating Image reference configuration for Ubuntu Linux VM
var imgRef = {publisher:"Canonical",offer:"UbuntuServer",sku:"14.04.2-LTS",version:"latest"}

// Creating the VM configuration object with the SKUID
var vmconfig = {imageReference:imgRef,nodeAgentSKUId:"batch.node.ubuntu 14.04"}

// Setting the VM size to Standard F4
var vmSize = "STANDARD_F4"

//Setting number of VMs in the pool to 4
var numVMs = 4
```

> [!Tip]
> Seznam imagí virtuálních počítačů s Linuxem, které jsou dostupné pro Azure Batch, a ID jejich skladových jednotek (SKU) najdete v tématu se [seznamem imagí virtuálních počítačů](batch-linux-nodes.md#list-of-virtual-machine-images).
>
>

Jakmile je konfigurace fondu definovaná, můžete vytvořit fond služby Azure Batch. Příkaz k vytvoření fondu služby Batch vytvoří uzly virtuálních počítačů Azure a připraví je na příjem úkolů ke zpracování. Každý fond musí mít jedinečné ID, abyste na něj mohli odkazovat v dalších krocích.

Následující fragment kódu vytvoří fond služby Azure Batch.

```nodejs
// Create a unique Azure Batch pool ID
var poolid = "pool" + customerDetails.customerid;
var poolConfig = {id:poolid, displayName:poolid,vmSize:vmSize,virtualMachineConfiguration:vmconfig,targetDedicatedComputeNodes:numVms,enableAutoScale:false };
// Creating the Pool for the specific customer
var pool = batch_client.pool.add(poolConfig,function(error,result){
    if(error!=null){console.log(error.response)};
});
```

Než budete pokračovat s odesláním úlohy do tohoto fondu, můžete zkontrolovat jeho stav a ověřit, že je „aktivní“.

```nodejs
var cloudPool = batch_client.pool.get(poolid,function(error,result,request,response){
        if(error == null)
        {

            if(result.state == "active")
            {
                console.log("Pool is active");
            }
        }
        else
        {
            if(error.statusCode==404)
            {
                console.log("Pool not found yet returned 404...");    

            }
            else
            {
                console.log("Error occurred while retrieving pool data");
            }
        }
        });
```

Následuje ukázka objektu výsledků vráceného funkcí pool.get.

```
{ id: 'processcsv_201721152',
  displayName: 'processcsv_201721152',
  url: 'https://<batch-account-name>.centralus.batch.azure.com/pools/processcsv_201721152',
  eTag: '<eTag>',
  lastModified: 2017-03-27T10:28:02.398Z,
  creationTime: 2017-03-27T10:28:02.398Z,
  state: 'active',
  stateTransitionTime: 2017-03-27T10:28:02.398Z,
  allocationState: 'resizing',
  allocationStateTransitionTime: 2017-03-27T10:28:02.398Z,
  vmSize: 'standard_a1',
  virtualMachineConfiguration:
   { imageReference:
      { publisher: 'Canonical',
        offer: 'UbuntuServer',
        sku: '14.04.2-LTS',
        version: 'latest' },
     nodeAgentSKUId: 'batch.node.ubuntu 14.04' },
  resizeTimeout:
   { [Number: 900000]
     _milliseconds: 900000,
     _days: 0,
     _months: 0,
     _data:
      { milliseconds: 0,
        seconds: 0,
        minutes: 15,
        hours: 0,
        days: 0,
        months: 0,
        years: 0 },
     _locale:
      Locale {
        _calendar: [Object],
        _longDateFormat: [Object],
        _invalidDate: 'Invalid date',
        ordinal: [Function: ordinal],
        _ordinalParse: /\d{1,2}(th|st|nd|rd)/,
        _relativeTime: [Object],
        _months: [Object],
        _monthsShort: [Object],
        _week: [Object],
        _weekdays: [Object],
        _weekdaysMin: [Object],
        _weekdaysShort: [Object],
        _meridiemParse: /[ap]\.?m?\.?/i,
        _abbr: 'en',
        _config: [Object],
        _ordinalParseLenient: /\d{1,2}(th|st|nd|rd)|\d{1,2}/ } },
  currentDedicated: 0,
  targetDedicated: 4,
  enableAutoScale: false,
  enableInterNodeCommunication: false,
  maxTasksPerNode: 1,
  taskSchedulingPolicy: { nodeFillType: 'Spread' } }
```


### <a name="step-4-submit-an-azure-batch-job"></a>Krok 4: Odeslání úlohy služby Azure Batch
Úloha služby Azure Batch je logická skupina podobných úkolů. V našem scénáři se jedná o úlohu „Zpracování formátu CSV do formátu JSON“. Každý z těchto úkolů může zpracovávat soubory CSV v jednotlivých kontejnerech služby Azure Storage.

Tyto úkoly budou spouštěné paralelně, nasazené v několika uzlech a orchestrované službou Azure Batch.

> [!Tip]
> Pomocí vlastnosti [maxTasksPerNode](http://azure.github.io/azure-sdk-for-node/azure-batch/latest/Pool.html#add) můžete zadat maximální počet úkolů, které můžou být spuštěné současně v jednom uzlu.
>
>

#### <a name="preparation-task"></a>Přípravný úkol

Vytvořené uzly virtuálních počítačů jsou prázdné uzly s Ubuntu. Často musíte pro splnění požadavků nainstalovat řadu programů.
Pro uzly s Linuxem obvykle můžete mít skript prostředí, který požadavky nainstaluje před spuštěním vlastních úkolů. Může se ale jednat o jakýkoli programovatelný spustitelný soubor.
[Skript prostředí](https://github.com/shwetams/azure-batchclient-sample-nodejs/blob/master/startup_prereq.sh) v tomto příkladu nainstaluje Python-pip a sadu SDK služby Azure Storage pro Python.

Skript můžete nahrát do účtu služby Azure Storage a vygenerovat identifikátor URI SAS pro přístup ke skriptu. Tento proces je také možné automatizovat pomocí sady SDK služby Azure Storage pro Node.js.

> [!Tip]
> Přípravný úkol pro úlohu se spustí pouze na uzlech virtuálních počítačů, na kterých je potřeba spustit konkrétní úkol. Pokud chcete, aby se požadované programy nainstalovaly na všech uzlech bez ohledu na to, jaké úkoly se na nich spouští, můžete během přidávání fondu použít vlastnost [startTask](http://azure.github.io/azure-sdk-for-node/azure-batch/latest/Pool.html#add). Pro srovnání můžete použít následující definici přípravného úkolu.
>
>

Přípravný úkol se zadává během odesílání úlohy služby Azure Batch. Následují parametry konfigurace přípravného úkolu:

* **ID:** Jedinečný identifikátor přípravného úkolu.
* **commandLine:** Příkazový řádek, který spustí spustitelný soubor s úkolem.
* **resourceFiles:** Pole objektů poskytujících podrobnosti o souborech, které je potřeba stáhnout pro spuštění tohoto úkolu.  Následují jeho možnosti:
    - blobSource: Identifikátor URI SAS souboru.
    - filePath: Místní cesta pro stažení a uložení souboru.
    - fileMode: Režim souboru, který lze použít pouze pro uzly s Linuxem, v osmičkovém formátu a s výchozí hodnotou 0770.
* **waitForSuccess:** Pokud je tento parametr nastavený na hodnotu true, úkol se nespustí, pokud dojde k chybě přípravného úkolu.
* **runElevated:** Nastavte tento parametr na hodnotu true, pokud jsou ke spuštění úkolu potřeba zvýšená oprávnění.

Následující fragment kódu ukazuje příklad konfigurace skriptu přípravného úkolu:

```nodejs
var job_prep_task_config = {id:"installprereq",commandLine:"sudo sh startup_prereq.sh > startup.log",resourceFiles:[{'blobSource':'Blob SAS URI','filePath':'startup_prereq.sh'}],waitForSuccess:true,runElevated:true}
```

Pokud ke spuštění vašich úkolů není potřeba instalovat žádné požadavky, můžete přípravné úkoly přeskočit. Následující kód vytvoří úlohu se zobrazovaným názvem „process csv files“.

 ```nodejs
 // Setting up Batch pool configuration
 var pool_config = {poolId:poolid}
 // Setting up Job configuration along with preparation task
 var jobId = "processcsvjob"
 var job_config = {id:jobId,displayName:"process csv files",jobPreparationTask:job_prep_task_config,poolInfo:pool_config}
 // Adding Azure batch job to the pool
 var job = batch_client.job.add(job_config,function(error,result){
     if(error != null)
     {
         console.log("Error submitting job : " + error.response);
     }});
```


### <a name="step-5-submit-azure-batch-tasks-for-a-job"></a>Krok 5: Odeslání úkolů služby Azure Batch pro úlohu

Nyní, když máme vytvořenou úlohu pro zpracování formátu CSV, vytvoříme pro tuto úlohu úkoly. Za předpokladu, že máme čtyři kontejnery, musíme vytvořit čtyři úkoly, jeden pro každý kontejner.

Když se podíváme na [skript Pythonu](https://github.com/shwetams/azure-batchclient-sample-nodejs/blob/master/processcsv.py), uvidíme, že přijímá dva parametry:

* container name: Kontejner služby Storage, ze kterého se mají stáhnout soubory.
* pattern: Volitelný parametr se vzorem názvu souboru.

Za předpokladu, že máme čtyři kontejnery con1, con2, con3 a con4, následující kód ukazuje odesílání úkolů do úlohy služby Azure Batch „process csv“, kterou jsme vytvořili dříve.

```nodejs
// storing container names in an array
var container_list = ["con1","con2","con3","con4"]
    container_list.forEach(function(val,index){           

           var container_name = val;
           var taskID = container_name + "_process";
           var task_config = {id:taskID,displayName:'process csv in ' + container_name,commandLine:'python processcsv.py --container ' + container_name,resourceFiles:[{'blobSource':'<blob SAS URI>','filePath':'processcsv.py'}]}
           var task = batch_client.task.add(poolid,task_config,function(error,result){
                if(error != null)
                {
                    console.log(error.response);     
                }
                else
                {
                    console.log("Task for container : " + container_name + "submitted successfully");
                }



           });

    });
```

Kód do fondu přidá několik úkolů. Každý z úkolů se provede na uzlu ve vytvořeném fondu virtuálních počítačů. Pokud počet úkolů překročí počet virtuálních počítačů ve fondu nebo hodnotu vlastnosti maxTasksPerNode, úkoly počkají na uvolnění uzlu. Tuto orchestraci automaticky zařizuje služba Azure Batch.

Na portálu jsou podrobná zobrazení stavů úkolů a úloh. Můžete také použít funkce list a get v sadě SDK Azure pro Node.js. Podrobnosti jsou uvedeny v [dokumentaci](http://azure.github.io/azure-sdk-for-node/azure-batch/latest/Job.html).

## <a name="next-steps"></a>Další kroky

- Přečtěte si článek [Přehled funkcí Azure Batch](batch-api-basics.md), který doporučujeme všem novým uživatelům služby.
- Pokud chcete prozkoumat rozhraní API služby Batch, přečtěte si článek [Reference k Batch Node.js](/javascript/api/overview/azure/batch).

