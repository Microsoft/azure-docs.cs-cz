---
title: Kurz – vytvoření vysoce dostupné aplikace s úložištěm BLOB
titleSuffix: Azure Storage
description: K zajištění vysoké dostupnosti dat aplikací použijte úložiště geografického a redundantního úložiště s přístupem pro čtení (RA-GZRS).
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 04/16/2020
ms.author: tamram
ms.reviewer: artek
ms.custom: mvc, devx-track-python, devx-track-js, devx-track-csharp
ms.subservice: blobs
ms.openlocfilehash: dfb7e7c7c93a8af2b59f6d3d7049e2c14b8f382a
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611045"
---
# <a name="tutorial-build-a-highly-available-application-with-blob-storage"></a>Kurz: vytvoření vysoce dostupné aplikace s úložištěm BLOB

Tento kurz je první částí série. V takovém případě se naučíte, jak zajistit vysokou dostupnost dat aplikace v Azure.

Po dokončení tohoto kurzu budete mít konzolovou aplikaci, která nahrává a načte objekt BLOB z účtu úložiště [geograficky redundantního pro přístup pro čtení](../common/storage-redundancy.md) (RA-GZRS).

Geografická redundance v Azure Storage replikuje transakce asynchronně z primární oblasti do sekundární oblasti, která je od sebe stovky kilometrů. Tento proces replikace zaručuje, že data v sekundární oblasti jsou nakonec konzistentní. Konzolová aplikace používá ke zjištění, ke kterému koncovému bodu se připojuje, automatické přepínání mezi koncovými body, jako jsou chyby a obnovení, a to pomocí vzoru [Break](/azure/architecture/patterns/circuit-breaker) .

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

V první části tohoto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření účtu úložiště
> * Nastavit připojovací řetězec
> * Spustit konzolovou aplikaci

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

# <a name="net"></a>[.NET](#tab/dotnet)

* Nainstalujte [Visual Studio 2019](https://www.visualstudio.com/downloads/) s úlohou **vývoj pro Azure** .

  ![Azure – vývoj (v části Web a cloud)](media/storage-create-geo-redundant-storage/workloads.png)

# <a name="python"></a>[Python](#tab/python)

* Instalace [Pythonu](https://www.python.org/downloads/)
* Stáhnout a nainstalovat [sadu SDK služby Azure Storage pro Python](https://github.com/Azure/azure-storage-python)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

* Nainstalujte [Node.js](https://nodejs.org).

---

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na [Azure Portal](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

Účet úložiště poskytuje jedinečný obor názvů pro ukládání a přístup k datovým objektům Azure Storage.

Pomocí těchto kroků vytvořte účet úložiště s přístupem Geo-Zone-redundantního (RA-GZRS) pro čtení:

1. V Azure Portal vyberte tlačítko **vytvořit prostředek** .
2. Z **nové** stránky vyberte **účet úložiště – objekt blob, soubor, tabulka, fronta** .
4. Vyplňte formulář účtu úložiště následujícími informacemi, jak ukazuje následující obrázek, a vyberte **Vytvořit**:

   | Nastavení       | Ukázková hodnota | Popis |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Předplatné** | *Moje předplatné* | Podrobnosti o vašich předplatných najdete v tématu [Předplatná](https://account.azure.com/Subscriptions). |
   | **ResourceGroup** | *myResourceGroup* | Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming). |
   | **Název** | *mystorageaccount* | Jedinečný název vašeho účtu úložiště. |
   | **Umístění** | *East US* | Zvolte umístění. |
   | **Výkon** | *Standard* | Standardní výkon je dobrou možností pro ukázkový scénář. |
   | **Druh účtu** | *StorageV2* | Doporučuje se použít účet úložiště pro obecné účely v2. Další informace o typech účtů úložiště Azure najdete v tématu [Přehled účtu úložiště](../common/storage-account-overview.md). |
   | **Replikace**| *Geograficky zónově redundantní úložiště s přístupem pro čtení (RA-GZRS)* | Primární oblast je redundantní v zóně a replikuje se do sekundární oblasti s povoleným přístupem pro čtení do sekundární oblasti. |
   | **Úroveň přístupu**| *Horká* | Pro často používaná data použijte vrstvu Hot. |

    ![Vytvoření účtu úložiště](media/storage-create-geo-redundant-storage/createragrsstracct.png)

## <a name="download-the-sample"></a>Stažení ukázky

# <a name="net"></a>[.NET](#tab/dotnet)

[Stáhněte si ukázkový projekt](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) a extrahujte (rozbalte) soubor storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip. Můžete také použít [git](https://git-scm.com/) a stáhnout si kopii aplikace do vývojového prostředí. Ukázkový projekt obsahuje konzolovou aplikaci.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="python"></a>[Python](#tab/python)

[Stáhněte si ukázkový projekt](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) a extrahujte (rozbalte) soubor storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip. Můžete také použít [git](https://git-scm.com/) a stáhnout si kopii aplikace do vývojového prostředí. Ukázkový projekt obsahuje základní aplikaci Pythonu.

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

[Stáhněte si ukázkový projekt](https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs) a rozbalte soubor. Můžete také použít [git](https://git-scm.com/) a stáhnout si kopii aplikace do vývojového prostředí. Ukázkový projekt obsahuje základní aplikaci Node.js.

```bash
git clone https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs
```

---

## <a name="configure-the-sample"></a>Konfigurace ukázky

# <a name="net"></a>[.NET](#tab/dotnet)

V aplikaci je potřeba zadat připojovací řetězec pro váš účet úložiště. Tento připojovací řetězec můžete uložit do proměnné prostředí v místním počítači, na kterém aplikaci spouštíte. V závislosti na operačním systém vytvořte proměnnou prostředí pomocí jednoho z následujících příkladů.

Na portálu Azure Portal přejděte k účtu úložiště. V části **Nastavení** v účtu úložiště vyberte **Přístupové klíče**. Zkopírujte **připojovací řetězec** z primárního nebo sekundárního klíče. V závislosti na vašem operačním systému spusťte jeden z následujících příkazů a nahraďte \<yourconnectionstring\> skutečným připojovacím řetězcem. Tento příkaz uloží proměnnou prostředí v místním počítači. V systému Windows není k dispozici proměnná prostředí, dokud znovu nenačtete **příkazový řádek** nebo prostředí, které používáte.

### <a name="linux"></a>Linux

```
export storageconnectionstring=<yourconnectionstring>
```

### <a name="windows"></a>Windows

```powershell
setx storageconnectionstring "<yourconnectionstring>"
```

# <a name="python"></a>[Python](#tab/python)

V aplikaci musíte zadat přihlašovací údaje účtu úložiště. Tyto informace můžete uložit do proměnných prostředí v místním počítači, na kterém je aplikace spuštěná. Použijte jeden z následujících příkladů v závislosti na operačním systému a vytvořte proměnné prostředí.

Na portálu Azure Portal přejděte k účtu úložiště. V části **Nastavení** v účtu úložiště vyberte **Přístupové klíče**. Vložte **název účtu úložiště** a hodnoty **klíče** do následujících příkazů a nahraďte \<youraccountname\> \<youraccountkey\> zástupné symboly a. Tento příkaz uloží proměnné prostředí do místního počítače. V systému Windows není k dispozici proměnná prostředí, dokud znovu nenačtete **příkazový řádek** nebo prostředí, které používáte.

### <a name="linux"></a>Linux

```
export accountname=<youraccountname>
export accountkey=<youraccountkey>
```

### <a name="windows"></a>Windows

```powershell
setx accountname "<youraccountname>"
setx accountkey "<youraccountkey>"
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Pokud chcete tuto ukázku spustit, musíte do souboru přidat svoje přihlašovací údaje účtu úložiště `.env.example` a pak ho přejmenovat na `.env` .

```
AZURE_STORAGE_ACCOUNT_NAME=<replace with your storage account name>
AZURE_STORAGE_ACCOUNT_ACCESS_KEY=<replace with your storage account access key>
```

Tyto informace najdete v Azure Portal tak, že přejdete na svůj účet úložiště a vyberete **přístupové klíče** v části **Nastavení** .

Nainstalujte požadované závislosti. Provedete to tak, že otevřete příkazový řádek, přejdete do ukázkové složky a pak zadáte `npm install` .

---

## <a name="run-the-console-application"></a>Spustit konzolovou aplikaci

# <a name="net"></a>[.NET](#tab/dotnet)

V aplikaci Visual Studio stiskněte klávesu **F5** nebo vyberte **začít** a zahajte ladění aplikace. Sada Visual Studio automaticky obnoví chybějící balíčky NuGet, pokud jsou nakonfigurované, a další informace najdete [v instalaci a přeinstalaci balíčků s obnovením balíčků](/nuget/consume-packages/package-restore#package-restore-overview) .

Spustí se okno konzoly a aplikace začne běžet. Aplikace nahraje obrázek **HelloWorld.png** z řešení na účet úložiště. Aplikace zkontroluje, jestli se image replikuje do sekundárního koncového bodu RA-GZRS. Potom začne stahovat obrázek až 999x. Každý přečtený je reprezentován hodnotou **P** nebo **s**. Kde **P** představuje primární koncový bod a **y** představuje sekundární koncový bod.

![Spuštěná konzolová aplikace](media/storage-create-geo-redundant-storage/figure3.png)

Ve vzorovém kódu slouží úloha `RunCircuitBreakerAsync` v souboru `Program.cs` ke stažení obrázku z účtu úložiště pomocí metody [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync). Před stažením se definuje [OperationContext](/dotnet/api/microsoft.azure.cosmos.table.operationcontext) . Kontext operace definuje obslužné rutiny událostí, které se spustí po úspěšném stažení nebo pokud se stažení nepovede a opakuje se.

# <a name="python"></a>[Python](#tab/python)

Pokud chcete aplikaci spustit na terminálu nebo v příkazovém řádku, přejděte do adresáře **circuitbreaker.py** a potom zadejte `python circuitbreaker.py`. Aplikace nahraje obrázek **HelloWorld.png** z řešení na účet úložiště. Aplikace zkontroluje, jestli se image replikuje do sekundárního koncového bodu RA-GZRS. Potom začne stahovat obrázek až 999x. Každý přečtený je reprezentován hodnotou **P** nebo **s**. Kde **P** představuje primární koncový bod a **y** představuje sekundární koncový bod.

![Spuštěná konzolová aplikace](media/storage-create-geo-redundant-storage/figure3.png)

Ve vzorovém kódu slouží metoda `run_circuit_breaker` v souboru `circuitbreaker.py` ke stažení obrázku z účtu úložiště pomocí metody [get_blob_to_path](/python/api/azure-storage-blob/azure.storage.blob.baseblobservice.baseblobservice#get-blob-to-path-container-name--blob-name--file-path--open-mode--wb---snapshot-none--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--lease-id-none--if-modified-since-none--if-unmodified-since-none--if-match-none--if-none-match-none--timeout-none-).

Funkce opakování pro objekt Storage je nastavená na zásadu lineárního opakování. Funkce opakování určuje, jestli se má opakovat žádost, a určuje, kolik sekund se má čekat před opakováním žádosti. Nastavte hodnotu **retry\_to\_secondary** na true, pokud by se žádost měla opakovat na sekundární koncový bod v případě, že původní žádost na primární byla neúspěšná. V ukázkové aplikaci je vlastní zásada opakování definovaná ve funkci `retry_callback` objektu úložiště.

Před stažením se definuje objekt služby [retry_callback](/python/api/azure-storage-common/azure.storage.common.storageclient.storageclient) a funkce [response_callback](/python/api/azure-storage-common/azure.storage.common.storageclient.storageclient) . Tyto funkce definují obslužné rutiny událostí, které se spustí po úspěšném stažení nebo pokud se stažení nepovede a opakuje se.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Chcete-li spustit ukázku, otevřete příkazový řádek, přejděte do složky Sample a pak zadejte `node index.js` .

Ukázka vytvoří kontejner v účtu služby Blob Storage, nahraje **HelloWorld.png** do kontejneru a pak opakovaně kontroluje, zda se kontejner a image replikují do sekundární oblasti. Po dokončení replikace se zobrazí výzva k zadání **D** nebo **Q** (následovaný ENTER) ke stažení nebo ukončení. Výstup by měl vypadat podobně jako v následujícím příkladu:

```
Created container successfully: newcontainer1550799840726
Uploaded blob: HelloWorld.png
Checking to see if container and blob have replicated to secondary region.
[0] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[1] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
...
[31] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[32] Container found, but blob has not replicated to secondary region yet.
...
[67] Container found, but blob has not replicated to secondary region yet.
[68] Blob has replicated to secondary region.
Ready for blob download. Enter (D) to download or (Q) to quit, followed by ENTER.
> D
Attempting to download blob...
Blob downloaded from primary endpoint.
> Q
Exiting...
Deleted container newcontainer1550799840726
```

---

## <a name="understand-the-sample-code"></a>Vysvětlení vzorového kódu

### <a name="net"></a>[.NET](#tab/dotnet)

### <a name="retry-event-handler"></a>Obslužná rutina události opakování

Obslužná rutina události `OperationContextRetrying` se volá, když se obrázek nepodaří stáhnout a je nastavená na opakování. Pokud se dosáhne maximálního počtu opakování, který je definovaný v aplikaci, [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode) žádosti se změní na `SecondaryOnly`. Toto nastavení přinutí aplikaci, aby se pokusila stáhnout obrázek ze sekundárního koncového bodu. Tato konfigurace snižuje čas potřebný k vyžádání obrázku, protože opakování pro primární koncový bod není neomezené.

```csharp
private static void OperationContextRetrying(object sender, RequestEventArgs e)
{
    retryCount++;
    Console.WriteLine("Retrying event because of failure reading the primary. RetryCount = " + retryCount);

    // Check if we have had more than n retries in which case switch to secondary.
    if (retryCount >= retryThreshold)
    {

        // Check to see if we can fail over to secondary.
        if (blobClient.DefaultRequestOptions.LocationMode != LocationMode.SecondaryOnly)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.SecondaryOnly;
            retryCount = 0;
        }
        else
        {
            throw new ApplicationException("Both primary and secondary are unreachable. Check your application's network connection. ");
        }
    }
}
```

### <a name="request-completed-event-handler"></a>Obslužná rutina události provedené žádosti

Obslužná rutina události `OperationContextRequestCompleted` se volá, když se obrázek podaří stáhnout. Pokud aplikace používá sekundární koncový bod, použije ho dále až 20x. Po 20 opakováních aplikace nastaví [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode) zpět na `PrimaryThenSecondary` a zkusí znovu primární koncový bod. Pokud je žádost úspěšná, bude aplikace dále číst z primárního koncového bodu.

```csharp
private static void OperationContextRequestCompleted(object sender, RequestEventArgs e)
{
    if (blobClient.DefaultRequestOptions.LocationMode == LocationMode.SecondaryOnly)
    {
        // You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        //    then switch back to the primary and see if it's available now.
        secondaryReadCount++;
        if (secondaryReadCount >= secondaryThreshold)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.PrimaryThenSecondary;
            secondaryReadCount = 0;
        }
    }
}
```

### <a name="python"></a>[Python](#tab/python)

### <a name="retry-event-handler"></a>Obslužná rutina události opakování

Obslužná rutina události `retry_callback` se volá, když se obrázek nepodaří stáhnout a je nastavená na opakování. Pokud se dosáhne maximálního počtu opakování, který je definovaný v aplikaci, [LocationMode](/python/api/azure-storage-common/azure.storage.common.models.locationmode) žádosti se změní na `SECONDARY`. Toto nastavení přinutí aplikaci, aby se pokusila stáhnout obrázek ze sekundárního koncového bodu. Tato konfigurace snižuje čas potřebný k vyžádání obrázku, protože opakování pro primární koncový bod není neomezené.

```python
def retry_callback(retry_context):
    global retry_count
    retry_count = retry_context.count
    sys.stdout.write(
        "\nRetrying event because of failure reading the primary. RetryCount= {0}".format(retry_count))
    sys.stdout.flush()

    # Check if we have more than n-retries in which case switch to secondary
    if retry_count >= retry_threshold:

        # Check to see if we can fail over to secondary.
        if blob_client.location_mode != LocationMode.SECONDARY:
            blob_client.location_mode = LocationMode.SECONDARY
            retry_count = 0
        else:
            raise Exception("Both primary and secondary are unreachable. "
                            "Check your application's network connection.")
```

### <a name="request-completed-event-handler"></a>Obslužná rutina události provedené žádosti

Obslužná rutina události `response_callback` se volá, když se obrázek podaří stáhnout. Pokud aplikace používá sekundární koncový bod, použije ho dále až 20x. Po 20 opakováních aplikace nastaví [LocationMode](/python/api/azure-storage-common/azure.storage.common.models.locationmode) zpět na `PRIMARY` a zkusí znovu primární koncový bod. Pokud je žádost úspěšná, bude aplikace dále číst z primárního koncového bodu.

```python
def response_callback(response):
    global secondary_read_count
    if blob_client.location_mode == LocationMode.SECONDARY:

        # You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        # then switch back to the primary and see if it is available now.
        secondary_read_count += 1
        if secondary_read_count >= secondary_threshold:
            blob_client.location_mode = LocationMode.PRIMARY
            secondary_read_count = 0
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

V sadě Node.js v10 za účelem SDK nejsou potřebné obslužné rutiny zpětného volání. Místo toho ukázka vytvoří kanál nakonfigurovaný s možnostmi opakování a sekundárním koncovým bodem. To umožňuje aplikaci automaticky přepnout na sekundární kanál, pokud se nepovede k vašim datům přes primární kanál.

```javascript
const accountName = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const storageAccessKey = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
const sharedKeyCredential = new SharedKeyCredential(accountName, storageAccessKey);

const primaryAccountURL = `https://${accountName}.blob.core.windows.net`;
const secondaryAccountURL = `https://${accountName}-secondary.blob.core.windows.net`;

const pipeline = StorageURL.newPipeline(sharedKeyCredential, {
  retryOptions: {
    maxTries: 3,
    tryTimeoutInMs: 10000,
    retryDelayInMs: 500,
    maxRetryDelayInMs: 1000,
    secondaryHost: secondaryAccountURL
  }
});
```

---

## <a name="next-steps"></a>Další kroky

V první části série jste se dozvěděli o tom, že je aplikace vysoce dostupná pomocí účtů úložiště RA-GZRS.

Přejděte k druhé části série, kde se dozvíte, jak simulovat selhání a aplikace vynutí použití sekundárního koncového bodu RA-GZRS.

> [!div class="nextstepaction"]
> [Simulace selhání při čtení z primární oblasti](simulate-primary-region-failure.md)