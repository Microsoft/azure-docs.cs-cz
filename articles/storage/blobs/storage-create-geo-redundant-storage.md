---
title: Zajištění vysoké dostupnosti dat aplikací v Azure | Microsoft Docs
description: Zajištění vysoké dostupnosti dat aplikací pomocí geograficky redundantního úložiště jen pro čtení
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 03/26/2018
ms.author: tamram
ms.custom: mvc
ms.component: blobs
ms.openlocfilehash: b3916fb0de48f30bd4809d1fc42dde6ac78c07b6
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "44023262"
---
# <a name="make-your-application-data-highly-available-with-azure-storage"></a>Zajištění vysoké dostupnosti dat aplikací pomocí úložiště Azure

Tento kurz je první částí série, která ukazuje, jak zajistit vysokou dostupnost dat aplikací v Azure. Po dokončení budete mít konzolovou aplikaci, která nahraje a načte objekt blob na účet [geograficky redundantního úložiště jen pro čtení](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage). Geograficky redundantní účet úložiště jen pro čtení funguje tak, že replikuje transakce z primární oblasti do sekundární. Tento proces replikace zaručuje, že data v sekundární oblasti jsou nakonec konzistentní. Aplikace používá vzor [jističe](/azure/architecture/patterns/circuit-breaker) k určení koncového bodu, ke kterému se připojit. Aplikace se při simulaci chyby přepne do sekundárního koncového bodu.

V první části této série se naučíte:

> [!div class="checklist"]
> * vytvořit účet úložiště
> * Stažení ukázky
> * Nastavit připojovací řetězec
> * Spustit konzolovou aplikaci

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:
 
# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)
* Nainstalovat [Visual Studio 2017](https://www.visualstudio.com/downloads/) s následujícími sadami funkcí:
  - **Azure – vývoj**

  ![Azure – vývoj (v části Web a cloud)](media/storage-create-geo-redundant-storage/workloads.png)

* (Volitelné) Stáhnout a nainstalovat [Fiddler](https://www.telerik.com/download/fiddler)
 
# <a name="python-tabpython"></a>[Python] (#tab/python) 

* Nainstalovat [Python](https://www.python.org/downloads/).
* Stáhnout a nainstalovat [sadu SDK služby Azure Storage pro Python](https://github.com/Azure/azure-storage-python)
* (Volitelné) Stáhnout a nainstalovat [Fiddler](https://www.telerik.com/download/fiddler)

# <a name="java-tabjava"></a>[Java] (#tab/java)

* Nainstalovat a nakonfigurovat [Maven](http://maven.apache.org/download.cgi) pro práci z příkazového řádku
* Nainstalovat a nakonfigurovat sadu [JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

---

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-the-azure-portal"></a>Přihlášení k portálu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Účet služby Storage poskytuje jedinečný obor názvů pro ukládání datových objektů Azure Storage a přístup k nim.

Podle těchto kroků můžete vytvořit účet geograficky redundantního úložiště jen pro čtení:

1. Vyberte tlačítko **Vytvořit prostředek** v levém horním rohu webu Azure Portal.

2. Na stránce **Nový** vyberte **Storage** a v části **Doporučené** vyberte **Účet úložiště – objekt blob, soubor, tabulka, fronta**.
3. Vyplňte formulář účtu úložiště následujícími informacemi, jak ukazuje následující obrázek, a vyberte **Vytvořit**:

   | Nastavení       | Navrhovaná hodnota | Popis |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Název** | mystorageaccount | Jedinečná hodnota pro váš účet úložiště |
   | **Model nasazení** | Resource Manager  | Resource Manager obsahuje nejnovější funkce.|
   | **Druh účtu** | StorageV2 | Podrobnosti o typech účtů najdete v tématu [Typy účtů úložiště](../common/storage-introduction.md#types-of-storage-accounts). |
   | **Výkon** | Standard | Pro ukázkový scénář stačí Standard. |
   | **Replikace**| Geograficky redundantní úložiště s přístupem pro čtení (RA-GRS) | To je nezbytné, aby ukázka fungovala. |
   |**Vyžádání bezpečného přenosu** | Zakázáno| Zabezpečený přenos se pro tento scénář nevyžaduje. |
   |**Předplatné** | Vaše předplatné |Podrobnosti o vašich předplatných najdete v tématu [Předplatná](https://account.windowsazure.com/Subscriptions). |
   |**ResourceGroup** | myResourceGroup |Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   |**Umístění** | USA – východ | Zvolte umístění. |

![Vytvoření účtu úložiště](media/storage-create-geo-redundant-storage/createragrsstracct.png)

## <a name="download-the-sample"></a>Stažení ukázky

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)

[Stáhněte si ukázkový projekt](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) a extrahujte (rozbalte) soubor storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip. Můžete také použít [git](https://git-scm.com/) a stáhnout si kopii aplikace do vývojového prostředí. Ukázkový projekt obsahuje konzolovou aplikaci.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git 
```
# <a name="python-tabpython"></a>[Python] (#tab/python) 

[Stáhněte si ukázkový projekt](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) a extrahujte (rozbalte) soubor storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip. Můžete také použít [git](https://git-scm.com/) a stáhnout si kopii aplikace do vývojového prostředí. Ukázkový projekt obsahuje základní aplikaci Pythonu.

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="java-tabjava"></a>[Java] (#tab/java)
[Stáhněte si ukázkový projekt](https://github.com/Azure-Samples/storage-java-ha-ra-grs) a extrahujte soubor storage-java-ragrs.zip. Můžete také použít [git](https://git-scm.com/) a stáhnout si kopii aplikace do vývojového prostředí. Ukázkový projekt obsahuje základní aplikaci v Javě.

```bash
git clone https://github.com/Azure-Samples/storage-java-ha-ra-grs.git
```
---


## <a name="set-the-connection-string"></a>Nastavit připojovací řetězec

V aplikaci je potřeba zadat připojovací řetězec pro váš účet úložiště. Doporučujeme uložit tento připojovací řetězec do proměnné prostředí v místním počítači, na kterém aplikaci spouštíte. V závislosti na operačním systém vytvořte proměnnou prostředí pomocí jednoho z následujících příkladů.

Na portálu Azure Portal přejděte k účtu úložiště. V části **Nastavení** v účtu úložiště vyberte **Přístupové klíče**. Zkopírujte **připojovací řetězec** z primárního nebo sekundárního klíče. Nahraďte \<yourconnectionstring\> skutečným připojovacím řetězcem pomocí spuštění jednoho z následujících příkazů podle vašeho operačního systému. Tento příkaz uloží proměnnou prostředí v místním počítači. Ve Windows tato proměnná prostředí není dostupná, dokud se znovu nenačte **příkazový řádek** nebo používané prostředí. Nahraďte **\<storageConnectionString\>** v následujícím příkladu:

# <a name="linux-tablinux"></a>[Linux] (#tab/linux) 
export storageconnectionstring=\<yourconnectionstring\> 

# <a name="windows-tabwindows"></a>[Windows] (#tab/windows) 
setx storageconnectionstring "\<yourconnectionstring\>"

---


## <a name="run-the-console-application"></a>Spustit konzolovou aplikaci

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)
V sadě Visual Studio stiskněte klávesu **F5** nebo vyberte **Spustit**, aby se spustilo ladění aplikace. Visual Studio automaticky obnoví chybějící balíčky NuGet, pokud jsou nakonfigurované. Přejděte na článek [Instalace a přeinstalace balíčků pomocí obnovení balíčků](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview), kde se dozvíte další informace.

Spustí se okno konzoly a aplikace začne běžet. Aplikace nahraje obrázek **HelloWorld.png** z řešení na účet úložiště. Aplikace zkontroluje, jestli se obrázek replikoval na sekundární koncový bod geograficky redundantního úložiště jen pro čtení (RA-GRS). Potom začne stahovat obrázek až 999x. Každé přečtení představuje **P** nebo **S**. **P** představuje primární koncový bod a **S** představuje sekundární koncový bod.

![Spuštěná konzolová aplikace](media/storage-create-geo-redundant-storage/figure3.png)

Ve vzorovém kódu slouží úloha `RunCircuitBreakerAsync` v souboru `Program.cs` ke stažení obrázku z účtu úložiště pomocí metody [DownloadToFileAsync](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_DownloadToFileAsync_System_String_System_IO_FileMode_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_). Před stažením se definuje [OperationContext](/dotnet/api/microsoft.windowsazure.storage.operationcontext?view=azure-dotnet). Kontext operace definuje obslužné rutiny událostí, které se spustí po úspěšném stažení nebo pokud se stažení nepovede a opakuje se.

# <a name="python-tabpython"></a>[Python] (#tab/python) 
Pokud chcete aplikaci spustit na terminálu nebo v příkazovém řádku, přejděte do adresáře **circuitbreaker.py** a potom zadejte `python circuitbreaker.py`. Aplikace nahraje obrázek **HelloWorld.png** z řešení na účet úložiště. Aplikace zkontroluje, jestli se obrázek replikoval na sekundární koncový bod geograficky redundantního úložiště jen pro čtení (RA-GRS). Potom začne stahovat obrázek až 999x. Každé přečtení představuje **P** nebo **S**. **P** představuje primární koncový bod a **S** představuje sekundární koncový bod.

![Spuštěná konzolová aplikace](media/storage-create-geo-redundant-storage/figure3.png)

Ve vzorovém kódu slouží metoda `run_circuit_breaker` v souboru `circuitbreaker.py` ke stažení obrázku z účtu úložiště pomocí metody [get_blob_to_path](https://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html). 

Funkce opakování pro objekt Storage je nastavená na zásadu lineárního opakování. Funkce opakování určuje, jestli se má opakovat žádost, a určuje, kolik sekund se má čekat před opakováním žádosti. Nastavte hodnotu **retry\_to\_secondary** na true, pokud by se žádost měla opakovat na sekundární koncový bod v případě, že původní žádost na primární byla neúspěšná. V ukázkové aplikaci je vlastní zásada opakování definovaná ve funkci `retry_callback` objektu úložiště.
 
Před stažením se definuje funkce [retry_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) a [response_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) objektu Service. Tyto funkce definují obslužné rutiny událostí, které se spustí po úspěšném stažení nebo pokud se stažení nepovede a opakuje se.  

# <a name="java-tabjava"></a>[Java] (#tab/java)
Aplikaci můžete spustit tak, že otevřete terminál nebo příkazový řádek v oboru složky stažené aplikace. Odtud aplikaci spustíte zadáním příkazu `mvn compile exec:java`. Aplikace pak nahraje obrázek **HelloWorld.png** z adresáře do vašeho účtu úložiště a zkontroluje, že se obrázek replikoval do sekundárního koncového bodu RA-GRS. Po dokončení kontroly zahájí aplikace opakované stahování obrázku a hlášení zpět do koncového bodu, ze kterého stahování provádí.

Funkce opakování pro objekt Storage je nastavená tak, aby používala zásadu lineárního opakování. Funkce opakování určuje, jestli se má opakovat žádost, a určuje, kolik sekund se má čekat mezi opakováními. Vlastnost **LocationMode** objektu **BlobRequestOptions** je nastavená na **PRIMARY\_THEN\_SECONDARY**. To umožňuje aplikaci automaticky přepnout na sekundární umístění v případě, že se jí při pokusu o stažení obrázku **HelloWorld.png** nepodaří spojit s primárním umístěním.

---

## <a name="understand-the-sample-code"></a>Vysvětlení vzorového kódu

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)

### <a name="retry-event-handler"></a>Obslužná rutina události opakování

Obslužná rutina události `OperationContextRetrying` se volá, když se obrázek nepodaří stáhnout a je nastavená na opakování. Pokud se dosáhne maximálního počtu opakování, který je definovaný v aplikaci, [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) žádosti se změní na `SecondaryOnly`. Toto nastavení přinutí aplikaci, aby se pokusila stáhnout obrázek ze sekundárního koncového bodu. Tato konfigurace snižuje čas potřebný k vyžádání obrázku, protože opakování pro primární koncový bod není neomezené.
 
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

Obslužná rutina události `OperationContextRequestCompleted` se volá, když se obrázek podaří stáhnout. Pokud aplikace používá sekundární koncový bod, použije ho dále až 20x. Po 20 opakováních aplikace nastaví [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) zpět na `PrimaryThenSecondary` a zkusí znovu primární koncový bod. Pokud je žádost úspěšná, bude aplikace dále číst z primárního koncového bodu.
 
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

# <a name="python-tabpython"></a>[Python] (#tab/python) 

### <a name="retry-event-handler"></a>Obslužná rutina události opakování

Obslužná rutina události `retry_callback` se volá, když se obrázek nepodaří stáhnout a je nastavená na opakování. Pokud se dosáhne maximálního počtu opakování, který je definovaný v aplikaci, [LocationMode](https://docs.microsoft.com/python/api/azure.storage.common.models.locationmode?view=azure-python) žádosti se změní na `SECONDARY`. Toto nastavení přinutí aplikaci, aby se pokusila stáhnout obrázek ze sekundárního koncového bodu. Tato konfigurace snižuje čas potřebný k vyžádání obrázku, protože opakování pro primární koncový bod není neomezené.  

```python
def retry_callback(retry_context):
    global retry_count
    retry_count = retry_context.count
    sys.stdout.write("\nRetrying event because of failure reading the primary. RetryCount= {0}".format(retry_count))
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

Obslužná rutina události `response_callback` se volá, když se obrázek podaří stáhnout. Pokud aplikace používá sekundární koncový bod, použije ho dále až 20x. Po 20 opakováních aplikace nastaví [LocationMode](https://docs.microsoft.com/python/api/azure.storage.common.models.locationmode?view=azure-python) zpět na `PRIMARY` a zkusí znovu primární koncový bod. Pokud je žádost úspěšná, bude aplikace dále číst z primárního koncového bodu.

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

# <a name="java-tabjava"></a>[Java] (#tab/java)

U Javy není nutné definovat obslužné rutiny zpětných volání, pokud je vlastnost **LocationMode** objektu **BlobRequestOptions** nastavená na **PRIMARY\_THEN\_SECONDARY**. To umožňuje aplikaci automaticky přepnout na sekundární umístění v případě, že se jí při pokusu o stažení obrázku **HelloWorld.png** nepodaří spojit s primárním umístěním.

```java
    BlobRequestOptions myReqOptions = new BlobRequestOptions();
    myReqOptions.setRetryPolicyFactory(new RetryLinearRetry(deltaBackOff, maxAttempts));
    myReqOptions.setLocationMode(LocationMode.PRIMARY_THEN_SECONDARY);
    blobClient.setDefaultRequestOptions(myReqOptions);

    blob.downloadToFile(downloadedFile.getAbsolutePath(),null,blobClient.getDefaultRequestOptions(),opContext);
```
---


## <a name="next-steps"></a>Další kroky

V první části série jste se dozvěděli o tom, jak zajistit vysokou dostupnost aplikace pomocí účtů geograficky redundantního úložiště jen pro čtení, například jak:

> [!div class="checklist"]
> * vytvořit účet úložiště
> * Stažení ukázky
> * Nastavit připojovací řetězec
> * Spustit konzolovou aplikaci

Přejděte k druhé části série, kde se dozvíte, jak simulovat selhání a přinutit aplikaci použít sekundární koncový bod geograficky redundantního účtu úložiště jen pro čtení.

> [!div class="nextstepaction"]
> [Simulace selhání v připojení k primárnímu koncovému bodu úložiště](storage-simulate-failure-ragrs-account-app.md)
