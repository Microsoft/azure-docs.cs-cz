---
title: Kurz – vytvoření vysoce dostupné aplikace s úložištěm objektů blob
titleSuffix: Azure Storage
description: Pomocí geograficky redundantního úložiště pro přístup ke čtení můžete data aplikace zpřístupnit.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: tamram
ms.reviewer: artek
ms.custom: mvc
ms.subservice: blobs
ms.openlocfilehash: a80504f43a14b7b710fc735ebe269285299b94bc
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459198"
---
# <a name="tutorial-build-a-highly-available-application-with-blob-storage"></a>Kurz: Vytvoření vysoce dostupné aplikace s úložištěm objektů blob

Tento kurz je první částí série. V něm se dozvíte, jak zpřístupnit data aplikace vysoce dostupná v Azure.

Po dokončení tohoto kurzu budete mít konzolovou aplikaci, která nahraje a načte objekt blob z [geograficky redundantního](../common/storage-redundancy.md) účtu pro čtení (RA-GRS).

RA-GRS funguje replikací transakcí z primární oblasti do sekundární oblasti. Tento proces replikace zaručuje, že data v sekundární oblasti jsou nakonec konzistentní. Aplikace používá [vzor jističe](/azure/architecture/patterns/circuit-breaker) k určení, ke kterému koncovému bodu se má připojit, automatické přepínání mezi koncovými body, protože jsou simulovány chyby a obnovení.

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

V první části tohoto kurzu se naučíte:

> [!div class="checklist"]
> * vytvořit účet úložiště
> * Nastavit připojovací řetězec
> * Spustit konzolovou aplikaci

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

# <a name="net"></a>[.NET](#tab/dotnet)

* Nainstalujte [Visual Studio 2019](https://www.visualstudio.com/downloads/) s **úlohou vývoje Azure.**

  ![Azure – vývoj (v části Web a cloud)](media/storage-create-geo-redundant-storage/workloads.png)

# <a name="python"></a>[Python](#tab/python)

* Instalace [Pythonu](https://www.python.org/downloads/)
* Stáhnout a nainstalovat [sadu SDK služby Azure Storage pro Python](https://github.com/Azure/azure-storage-python)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

* Nainstalujte [soubor Node.js](https://nodejs.org).

---

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Účet úložiště poskytuje jedinečný obor názvů pro ukládání a přístup k datovým objektům Azure Storage.

Podle těchto kroků můžete vytvořit účet geograficky redundantního úložiště jen pro čtení:

1. Vyberte tlačítko **Vytvořit prostředek** v levém horním rohu webu Azure Portal.
2. Na stránce **Nový** vyberte **Úložiště.**
3. Vyberte **účet úložiště – objekt blob, soubor, tabulka, fronta** v části **Doporučené**.
4. Vyplňte formulář účtu úložiště následujícími informacemi, jak ukazuje následující obrázek, a vyberte **Vytvořit**:

   | Nastavení       | Navrhovaná hodnota | Popis |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Název** | mystorageaccount | Jedinečná hodnota pro váš účet úložiště |
   | **Model nasazení** | Resource Manager  | Resource Manager obsahuje nejnovější funkce.|
   | **Druh účtu** | StorageV2 | Podrobnosti o typech účtů najdete v tématu [Typy účtů úložiště](../common/storage-introduction.md#types-of-storage-accounts). |
   | **Výkon** | Standard | Pro ukázkový scénář stačí Standard. |
   | **Replikace**| Geograficky redundantní úložiště s přístupem pro čtení (RA-GRS) | To je nezbytné, aby ukázka fungovala. |
   |**Předplatné** | Vaše předplatné |Podrobnosti o vašich předplatných najdete v tématu [Předplatná](https://account.azure.com/Subscriptions). |
   |**ResourceGroup** | myResourceGroup |Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming). |
   |**Umístění** | USA – východ | Zvolte umístění. |

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

[Stáhněte ukázkový projekt](https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs) a rozbalte soubor. Můžete také použít [git](https://git-scm.com/) a stáhnout si kopii aplikace do vývojového prostředí. Ukázkový projekt obsahuje základní aplikaci Node.js.

```bash
git clone https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs
```

---

## <a name="configure-the-sample"></a>Konfigurace ukázky

# <a name="net"></a>[.NET](#tab/dotnet)

V aplikaci je potřeba zadat připojovací řetězec pro váš účet úložiště. Tento připojovací řetězec můžete uložit do proměnné prostředí v místním počítači, na kterém aplikaci spouštíte. V závislosti na operačním systém vytvořte proměnnou prostředí pomocí jednoho z následujících příkladů.

Na portálu Azure Portal přejděte k účtu úložiště. V části **Nastavení** v účtu úložiště vyberte **Přístupové klíče**. Zkopírujte **připojovací řetězec** z primárního nebo sekundárního klíče. Spusťte jeden z následujících příkazů založených \<na operačním\> systému a nahraďte řetězec připojení skutečným připojovacím řetězcem. Tento příkaz uloží proměnnou prostředí v místním počítači. V systému Windows není proměnná prostředí k dispozici, dokud znovu nenačtete **příkazový řádek** nebo prostředí, které používáte.

### <a name="linux"></a>Linux

```
export storageconnectionstring=<yourconnectionstring>
```

### <a name="windows"></a>Windows

```powershell
setx storageconnectionstring "<yourconnectionstring>"
```

# <a name="python"></a>[Python](#tab/python)

V aplikaci je nutné zadat pověření účtu úložiště. Tyto informace můžete uložit v proměnných prostředí v místním počítači se spuštěnou aplikací. Postupujte podle jednoho z níže uvedených příkladů v závislosti na operačním systému k vytvoření proměnných prostředí.

Na portálu Azure Portal přejděte k účtu úložiště. V části **Nastavení** v účtu úložiště vyberte **Přístupové klíče**. Vložte **název účtu úložiště** a hodnoty **klíče** do \<následujících\> příkazů \<a\> nahraďte zástupné symboly vašeho účtu a klíče vašeho účtu. Tento příkaz uloží proměnné prostředí do místního počítače. V systému Windows není proměnná prostředí k dispozici, dokud znovu nenačtete **příkazový řádek** nebo prostředí, které používáte.

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

Chcete-li spustit tuto ukázku, je nutné `.env.example` do souboru přidat `.env`pověření účtu úložiště a potom je přejmenovat na .

```
AZURE_STORAGE_ACCOUNT_NAME=<replace with your storage account name>
AZURE_STORAGE_ACCOUNT_ACCESS_KEY=<replace with your storage account access key>
```

Tyto informace najdete na webu Azure Portal tak, že přejdete ke svému účtu úložiště a vyberete **přístupové klíče** v části **Nastavení.**

Nainstalujte požadované závislosti. Chcete-li to provést, otevřete příkazový řádek, `npm install`přejděte do ukázkové složky a zadejte .

---

## <a name="run-the-console-application"></a>Spustit konzolovou aplikaci

# <a name="net"></a>[.NET](#tab/dotnet)

V sadě Visual Studio stiskněte **klávesu F5** nebo vyberte **spustit** a začněte ladit aplikaci. Visual Studio automaticky obnoví chybějící balíčky NuGet, pokud je nakonfigurováno, navštivte [stránku Instalace a přeinstalace balíčků s obnovením balíčků,](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview) abyste se dozvěděli další informace.

Spustí se okno konzoly a aplikace začne běžet. Aplikace nahraje obrázek **HelloWorld.png** z řešení na účet úložiště. Aplikace zkontroluje, jestli se obrázek replikoval na sekundární koncový bod geograficky redundantního úložiště jen pro čtení (RA-GRS). Potom začne stahovat obrázek až 999x. Každé čtení je reprezentováno **P** nebo **S**. Kde **P** představuje primární koncový bod a **S** představuje sekundární koncový bod.

![Spuštěná konzolová aplikace](media/storage-create-geo-redundant-storage/figure3.png)

Ve vzorovém kódu slouží úloha `RunCircuitBreakerAsync` v souboru `Program.cs` ke stažení obrázku z účtu úložiště pomocí metody [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync). Před stažením [OperationContext](/dotnet/api/microsoft.azure.cosmos.table.operationcontext) je definován. Kontext operace definuje obslužné rutiny událostí, které se spustí po úspěšném stažení nebo pokud se stažení nepovede a opakuje se.

# <a name="python"></a>[Python](#tab/python)

Pokud chcete aplikaci spustit na terminálu nebo v příkazovém řádku, přejděte do adresáře **circuitbreaker.py** a potom zadejte `python circuitbreaker.py`. Aplikace nahraje obrázek **HelloWorld.png** z řešení na účet úložiště. Aplikace zkontroluje, jestli se obrázek replikoval na sekundární koncový bod geograficky redundantního úložiště jen pro čtení (RA-GRS). Potom začne stahovat obrázek až 999x. Každé čtení je reprezentováno **P** nebo **S**. Kde **P** představuje primární koncový bod a **S** představuje sekundární koncový bod.

![Spuštěná konzolová aplikace](media/storage-create-geo-redundant-storage/figure3.png)

Ve vzorovém kódu slouží metoda `run_circuit_breaker` v souboru `circuitbreaker.py` ke stažení obrázku z účtu úložiště pomocí metody [get_blob_to_path](https://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html).

Funkce opakování pro objekt Storage je nastavená na zásadu lineárního opakování. Funkce opakování určuje, jestli se má opakovat žádost, a určuje, kolik sekund se má čekat před opakováním žádosti. Nastavte hodnotu **retry\_to\_secondary** na true, pokud by se žádost měla opakovat na sekundární koncový bod v případě, že původní žádost na primární byla neúspěšná. V ukázkové aplikaci je vlastní zásada opakování definovaná ve funkci `retry_callback` objektu úložiště.

Před stažením je definován objekt Service [retry_callback](https://docs.microsoft.com/python/api/azure-storage-common/azure.storage.common.storageclient.storageclient?view=azure-python) a [response_callback](https://docs.microsoft.com/python/api/azure-storage-common/azure.storage.common.storageclient.storageclient?view=azure-python) funkce. Tyto funkce definují obslužné rutiny událostí, které se spustí po úspěšném stažení nebo pokud se stažení nepovede a opakuje se.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Chcete-li ukázku spustit, otevřete příkazový řádek, `node index.js`přejděte do ukázkové složky a zadejte .

Ukázka vytvoří kontejner v účtu úložiště objektů Blob, nahraje **HelloWorld.png** do kontejneru a pak opakovaně zkontroluje, zda kontejner a image replikovány do sekundární oblasti. Po replikaci vás vyzve k zadání **D** nebo **Q** (následované enterem) ke stažení nebo ukončení. Výstup by měl vypadat podobně jako v následujícím příkladu:

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

Obslužná rutina události `retry_callback` se volá, když se obrázek nepodaří stáhnout a je nastavená na opakování. Pokud se dosáhne maximálního počtu opakování, který je definovaný v aplikaci, [LocationMode](https://docs.microsoft.com/python/api/azure-storage-common/azure.storage.common.models.locationmode?view=azure-python) žádosti se změní na `SECONDARY`. Toto nastavení přinutí aplikaci, aby se pokusila stáhnout obrázek ze sekundárního koncového bodu. Tato konfigurace snižuje čas potřebný k vyžádání obrázku, protože opakování pro primární koncový bod není neomezené.

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

Obslužná rutina události `response_callback` se volá, když se obrázek podaří stáhnout. Pokud aplikace používá sekundární koncový bod, použije ho dále až 20x. Po 20 opakováních aplikace nastaví [LocationMode](https://docs.microsoft.com/python/api/azure-storage-common/azure.storage.common.models.locationmode?view=azure-python) zpět na `PRIMARY` a zkusí znovu primární koncový bod. Pokud je žádost úspěšná, bude aplikace dále číst z primárního koncového bodu.

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

S Node.js V10 SDK, obslužné rutiny zpětného volání jsou zbytečné. Místo toho ukázka vytvoří kanál nakonfigurovaný s možnostmi opakování a sekundární koncový bod. To umožňuje aplikaci automaticky přepnout na sekundární kanál, pokud se nezdaří dosáhnout dat prostřednictvím primárního kanálu.

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

V první části série jste se dozvěděli o tom, že aplikace je vysoce dostupná s účty úložiště RA-GRS.

Přejděte k druhé části série, kde se dozvíte, jak simulovat selhání a přinutit aplikaci použít sekundární koncový bod geograficky redundantního účtu úložiště jen pro čtení.

> [!div class="nextstepaction"]
> [Simulace selhání čtení z primární oblasti](storage-simulate-failure-ragrs-account-app.md)
