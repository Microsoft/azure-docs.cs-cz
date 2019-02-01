---
title: 'Kurz: Vytvářet vysoce dostupné aplikace s Blob storage – Azure Storage'
description: Zajištění vysoké dostupnosti dat aplikací pomocí geograficky redundantního úložiště jen pro čtení
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: tamram
ms.custom: mvc
ms.subservice: blobs
ms.openlocfilehash: d2fef3a47cbcb4cfd8bce8978003eca1044d7de3
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55510629"
---
# <a name="tutorial-build-a-highly-available-application-with-blob-storage"></a>Kurz: Sestavení aplikace s vysokou dostupností s úložištěm objektů Blob

Tento kurz je první částí série. V ní se dozvíte, jak provést vašich aplikačních dat s vysokou dostupností v Azure.

Po dokončení tohoto kurzu budete mít konzolovou aplikaci, která nahraje a načte z objektu blob [čtení geograficky redundantní](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) účet úložiště (pro čtení RA-GRS).

RA-GRS funguje tak, že replikuje transakce z primární oblasti do sekundární oblasti. Tento proces replikace zaručuje, že data v sekundární oblasti jsou nakonec konzistentní. Aplikace používá [jistič](/azure/architecture/patterns/circuit-breaker) vzor k určení koncového bodu připojení, automaticky přepínání mezi koncovými body jako selhání a obnovení jsou simulované.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

V první části této série se naučíte:

> [!div class="checklist"]
> * vytvořit účet úložiště
> * Nastavit připojovací řetězec
> * Spustit konzolovou aplikaci

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)

* Nainstalovat [Visual Studio 2017](https://www.visualstudio.com/downloads/) s následujícími sadami funkcí:
  - **Azure – vývoj**

  ![Azure – vývoj (v části Web a cloud)](media/storage-create-geo-redundant-storage/workloads.png)

# <a name="python-tabpython"></a>[Python] (#tab/python)

* Nainstalovat [Python](https://www.python.org/downloads/).
* Stáhnout a nainstalovat [sadu SDK služby Azure Storage pro Python](https://github.com/Azure/azure-storage-python)

# <a name="java-v7-sdk--tabjava-v7"></a>[Java V7 SDK] (# kartu nebo java – v7)

* Nainstalovat a nakonfigurovat [Maven](http://maven.apache.org/download.cgi) pro práci z příkazového řádku
* Nainstalovat a nakonfigurovat sadu [JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

# <a name="java-v10-sdk-tabjava-v10"></a>[Java V10 SDK] (#tab/java-v10)

* Nainstalovat a nakonfigurovat [Maven](http://maven.apache.org/download.cgi) pro práci z příkazového řádku
* Nainstalovat a nakonfigurovat sadu [JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

---

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Účet úložiště poskytuje jedinečný obor názvů pro ukládání a přístup k datových objektů Azure Storage.

Podle těchto kroků můžete vytvořit účet geograficky redundantního úložiště jen pro čtení:

1. Vyberte tlačítko **Vytvořit prostředek** v levém horním rohu webu Azure Portal.
2. Vyberte **úložiště** z **nový** stránky.
3. Vyberte **účet úložiště – objekt blob, soubor, tabulka, fronta** pod **doporučené**.
4. Vyplňte formulář účtu úložiště následujícími informacemi, jak ukazuje následující obrázek, a vyberte **Vytvořit**:

   | Nastavení       | Navrhovaná hodnota | Popis |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Název** | mystorageaccount | Jedinečná hodnota pro váš účet úložiště |
   | **Model nasazení** | Resource Manager  | Resource Manager obsahuje nejnovější funkce.|
   | **Druh účtu** | StorageV2 | Podrobnosti o typech účtů najdete v tématu [Typy účtů úložiště](../common/storage-introduction.md#types-of-storage-accounts). |
   | **Výkon** | Standard | Pro ukázkový scénář stačí Standard. |
   | **Replikace**| Geograficky redundantní úložiště s přístupem pro čtení (RA-GRS) | To je nezbytné, aby ukázka fungovala. |
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

# <a name="java-v7-sdk--tabjava-v7"></a>[Java V7 SDK] (# kartu nebo java – v7)

[Stáhněte si ukázkový projekt](https://github.com/Azure-Samples/storage-java-ha-ra-grs) a extrahujte soubor storage-java-ragrs.zip. Můžete také použít [git](https://git-scm.com/) a stáhnout si kopii aplikace do vývojového prostředí. Ukázkový projekt obsahuje základní aplikaci v Javě.

```bash
git clone https://github.com/Azure-Samples/storage-java-ha-ra-grs.git
```

# <a name="java-v10-sdk-tabjava-v10"></a>[Java V10 SDK] (#tab/java-v10)

[Stáhněte si ukázkový projekt](https://github.com/Azure-Samples/storage-java-V10-ha-ra-grs) a extrahujte soubor storage-java-ragrs.zip. Můžete také použít [git](https://git-scm.com/) a stáhnout si kopii aplikace do vývojového prostředí. Ukázkový projekt obsahuje základní aplikaci v Javě.

```bash
git clone https://github.com/Azure-Samples/storage-java-V10-ha-ra-grs
```

---

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)

V aplikaci je potřeba zadat připojovací řetězec pro váš účet úložiště. Doporučujeme uložit tento připojovací řetězec do proměnné prostředí v místním počítači spustíte aplikaci. V závislosti na operačním systém vytvořte proměnnou prostředí pomocí jednoho z následujících příkladů.

Na portálu Azure Portal přejděte k účtu úložiště. V části **Nastavení** v účtu úložiště vyberte **Přístupové klíče**. Zkopírujte **připojovací řetězec** z primárního nebo sekundárního klíče. Nahraďte \<yourconnectionstring\> skutečným připojovacím řetězcem pomocí spuštění jednoho z následujících příkazů podle vašeho operačního systému. Tento příkaz uloží proměnnou prostředí v místním počítači. Ve Windows, je proměnná prostředí není k dispozici dokud znovu nenačtete **příkazového řádku** nebo používané prostředí. Nahraďte **\<storageConnectionString\>** v následujícím příkladu:

### <a name="linux"></a>Linux

```
export storageconnectionstring=\<yourconnectionstring\> 
```
### <a name="windows"></a>Windows

```PowerShell
setx storageconnectionstring "\<yourconnectionstring\>"
```

# <a name="python-tabpython"></a>[Python] (#tab/python)

V aplikaci je potřeba zadat připojovací řetězec pro váš účet úložiště. Doporučujeme uložit tento připojovací řetězec do proměnné prostředí v místním počítači spustíte aplikaci. V závislosti na operačním systém vytvořte proměnnou prostředí pomocí jednoho z následujících příkladů.

Na portálu Azure Portal přejděte k účtu úložiště. V části **Nastavení** v účtu úložiště vyberte **Přístupové klíče**. Zkopírujte **připojovací řetězec** z primárního nebo sekundárního klíče. Nahraďte \<yourconnectionstring\> skutečným připojovacím řetězcem pomocí spuštění jednoho z následujících příkazů podle vašeho operačního systému. Tento příkaz uloží proměnnou prostředí v místním počítači. Ve Windows, je proměnná prostředí není k dispozici dokud znovu nenačtete **příkazového řádku** nebo používané prostředí. Nahraďte **\<storageConnectionString\>** v následujícím příkladu:

### <a name="linux"></a>Linux

```
export storageconnectionstring=\<yourconnectionstring\> 
```
### <a name="windows"></a>Windows

```PowerShell
setx storageconnectionstring "\<yourconnectionstring\>"
```

# <a name="java-v7-sdk--tabjava-v7"></a>[Java V7 SDK] (# kartu nebo java – v7)

V aplikaci je potřeba zadat připojovací řetězec pro váš účet úložiště. Doporučujeme uložit tento připojovací řetězec do proměnné prostředí v místním počítači spustíte aplikaci. V závislosti na operačním systém vytvořte proměnnou prostředí pomocí jednoho z následujících příkladů.

Na portálu Azure Portal přejděte k účtu úložiště. V části **Nastavení** v účtu úložiště vyberte **Přístupové klíče**. Zkopírujte **připojovací řetězec** z primárního nebo sekundárního klíče. Nahraďte \<yourconnectionstring\> skutečným připojovacím řetězcem pomocí spuštění jednoho z následujících příkazů podle vašeho operačního systému. Tento příkaz uloží proměnnou prostředí v místním počítači. Ve Windows, je proměnná prostředí není k dispozici dokud znovu nenačtete **příkazového řádku** nebo používané prostředí. Nahraďte **\<storageConnectionString\>** v následujícím příkladu:

### <a name="linux"></a>Linux

```
export storageconnectionstring=\<yourconnectionstring\> 
```
### <a name="windows"></a>Windows

```PowerShell
setx storageconnectionstring "\<yourconnectionstring\>"
```

# <a name="java-v10-sdk-tabjava-v10"></a>[Java V10 SDK] (#tab/java-v10)

Tato ukázka vyžaduje bezpečné uložení názvu a klíče účtu úložiště. Store je v počítači, který se spustí ukázku místních proměnných prostředí. Použijte Linuxu nebo Windows příklad, v závislosti na operačním systému vytvořte proměnné prostředí. Ve Windows, je proměnná prostředí není k dispozici dokud znovu nenačtete **příkazového řádku** nebo používané prostředí.

### <a name="linux-example"></a>Příklad pro Linux

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

### <a name="windows-example"></a>Příklad pro Windows

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

---

## <a name="run-the-console-application"></a>Spustit konzolovou aplikaci

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)

V sadě Visual Studio, stiskněte klávesu **F5** nebo vyberte **Start** zahájíte ladění aplikace. Visual studio automaticky obnoví chybějící balíčky NuGet, pokud je nakonfigurovaná, navštivte [instalace a přeinstalace balíčků pomocí obnovení balíčků](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview) Další informace.

Spustí se okno konzoly a aplikace začne běžet. Aplikace nahraje obrázek **HelloWorld.png** z řešení na účet úložiště. Aplikace zkontroluje, jestli se obrázek replikoval na sekundární koncový bod geograficky redundantního úložiště jen pro čtení (RA-GRS). Potom začne stahovat obrázek až 999x. Každé přečtení představuje **P** nebo **S**. **P** představuje primární koncový bod a **S** představuje sekundární koncový bod.

![Spuštěná konzolová aplikace](media/storage-create-geo-redundant-storage/figure3.png)

Ve vzorovém kódu slouží úloha `RunCircuitBreakerAsync` v souboru `Program.cs` ke stažení obrázku z účtu úložiště pomocí metody [DownloadToFileAsync](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_DownloadToFileAsync_System_String_System_IO_FileMode_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_). Před stažením se definuje [OperationContext](/dotnet/api/microsoft.windowsazure.storage.operationcontext?view=azure-dotnet). Kontext operace definuje obslužné rutiny událostí, které se spustí po úspěšném stažení nebo pokud se stažení nepovede a opakuje se.

# <a name="python-tabpython"></a>[Python] (#tab/python)

Pokud chcete aplikaci spustit na terminálu nebo v příkazovém řádku, přejděte do adresáře **circuitbreaker.py** a potom zadejte `python circuitbreaker.py`. Aplikace nahraje obrázek **HelloWorld.png** z řešení na účet úložiště. Aplikace zkontroluje, jestli se obrázek replikoval na sekundární koncový bod geograficky redundantního úložiště jen pro čtení (RA-GRS). Potom začne stahovat obrázek až 999x. Každé přečtení představuje **P** nebo **S**. **P** představuje primární koncový bod a **S** představuje sekundární koncový bod.

![Spuštěná konzolová aplikace](media/storage-create-geo-redundant-storage/figure3.png)

Ve vzorovém kódu slouží metoda `run_circuit_breaker` v souboru `circuitbreaker.py` ke stažení obrázku z účtu úložiště pomocí metody [get_blob_to_path](https://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html). 

Funkce opakování pro objekt Storage je nastavená na zásadu lineárního opakování. Funkce opakování určuje, jestli se má opakovat žádost, a určuje, kolik sekund se má čekat před opakováním žádosti. Nastavte hodnotu **retry\_to\_secondary** na true, pokud by se žádost měla opakovat na sekundární koncový bod v případě, že původní žádost na primární byla neúspěšná. V ukázkové aplikaci je vlastní zásada opakování definovaná ve funkci `retry_callback` objektu úložiště.

Před stažením se definuje funkce [retry_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) a [response_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) objektu Service. Tyto funkce definují obslužné rutiny událostí, které se spustí po úspěšném stažení nebo pokud se stažení nepovede a opakuje se.  

# <a name="java-v7-sdk-tabjava-v7"></a>[Java V7 SDK] (#tab/java-v7)

Aplikaci můžete spustit tak, že otevřete terminál nebo příkazový řádek v oboru složky stažené aplikace. Odtud aplikaci spustíte zadáním příkazu `mvn compile exec:java`. Aplikace pak nahraje obrázek **HelloWorld.png** z adresáře do vašeho účtu úložiště a zkontroluje, že se obrázek replikoval do sekundárního koncového bodu RA-GRS. Po dokončení kontroly zahájí aplikace opakované stahování obrázku a hlášení zpět do koncového bodu, ze kterého stahování provádí.

Funkce opakování pro objekt Storage je nastavená tak, aby používala zásadu lineárního opakování. Funkce opakování určuje, jestli se má opakovat žádost, a určuje, kolik sekund se má čekat mezi opakováními. Vlastnost **LocationMode** objektu **BlobRequestOptions** je nastavená na **PRIMARY\_THEN\_SECONDARY**. To umožňuje aplikaci automaticky přepnout na sekundární umístění v případě, že se jí při pokusu o stažení obrázku **HelloWorld.png** nepodaří spojit s primárním umístěním.

# <a name="java-v10-sdk-tabjava-v10"></a>[Java V10 SDK] (#tab/java-v10)

Ke spuštění ukázky použití Mavenu na příkazovém řádku.

1. Otevřete prostředí a přejděte do **storage-blobs-java-v10-quickstart** uvnitř naklonovaného adresáře.
2. Zadejte `mvn compile exec:java`.

Tato ukázka vytvoří testovací soubor ve výchozím adresáři, pro uživatele windows je tento adresář **AppData\Local\Temp**. Ukázka pak uvede příkazů, které můžete zadat následující možnosti:

- Zadejte **P** k provedení operace vložení objektu blob, to dočasný soubor nahraje do účtu úložiště.
- Zadejte **L** k provedení operace objektů blob v seznamu, tento seznam aktuálně ve vašem kontejneru objektů BLOB.
- Zadejte **G** provést operaci načíst objekt blob, tato akce stáhne soubor z vašeho účtu úložiště do svého místního počítače.
- Zadejte **D** provést operaci odstranění objektů blob, tím se odstraní objekt blob z účtu úložiště.
- Zadejte **E** zavřít ukázky, tím se odstraní také všechny prostředky ukázka vytvoří.

Tento příklad ukazuje výstup při spuštění aplikace ve Windows.

```
Created quickstart container
Enter a command
(P)utBlob | (L)istBlobs | (G)etBlob | (D)eleteBlobs | (E)xitSample
# Enter a command :
P
Uploading the sample file into the container: https://<storageaccount>.blob.core.windows.net/quickstart
# Enter a command :
L
Listing blobs in the container: https://<storageaccount>.blob.core.windows.net/quickstart
Blob name: SampleBlob.txt
# Enter a command :
G
Get the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
The blob was downloaded to C:\Users\<useraccount>\AppData\Local\Temp\downloadedFile13097087873115855761.txt
# Enter a command :
D
Delete the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt

# Enter a command :
>> Blob deleted: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
E
Cleaning up the sample and exiting!
```

Máte možnost ovládat tuto ukázku a zadat příkazy, které spustí kód. Vstupy jsou malá a velká písmena.

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

# <a name="java-v7-sdk--tabjava-v7"></a>[Java V7 SDK] (# kartu nebo java – v7)

U Javy není nutné definovat obslužné rutiny zpětných volání, pokud je vlastnost **LocationMode** objektu **BlobRequestOptions** nastavená na **PRIMARY\_THEN\_SECONDARY**. To umožňuje aplikaci automaticky přepnout na sekundární umístění v případě, že se jí při pokusu o stažení obrázku **HelloWorld.png** nepodaří spojit s primárním umístěním.

```java
    BlobRequestOptions myReqOptions = new BlobRequestOptions();
    myReqOptions.setRetryPolicyFactory(new RetryLinearRetry(deltaBackOff, maxAttempts));
    myReqOptions.setLocationMode(LocationMode.PRIMARY_THEN_SECONDARY);
    blobClient.setDefaultRequestOptions(myReqOptions);

    blob.downloadToFile(downloadedFile.getAbsolutePath(),null,blobClient.getDefaultRequestOptions(),opContext);
```

# <a name="java-v10-sdk-tabjava-v10"></a>[Java V10 SDK] (#tab/java-v10)

Definování obslužné rutiny zpětných volání je zbytečné stále V10 sady Java SDK a SDK má teď některé základní rozdíl oproti sadě SDK V7. Místo LocationMode, máme sekundární **kanálu**. Můžete definovat sekundární kanál prostřednictvím **RequestRetryOptions** a pokud definována, vám umožní aplikaci automaticky přepnout na sekundární kanálu, pokud se nepodaří kontaktovat vaše data přes primární kanál.

```java
// We create pipeline options here so that they can be easily used between different pipelines
PipelineOptions myOptions = new PipelineOptions();
myOptions.withRequestRetryOptions(new RequestRetryOptions(RetryPolicyType.EXPONENTIAL, 3, 10, 500L, 1000L, accountName + "-secondary.blob.core.windows.net"));
// We are using a default pipeline here, you can learn more about it at https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview
final ServiceURL serviceURL = new ServiceURL(new URL("https://" + accountName + ".blob.core.windows.net"), StorageURL.createPipeline(creds, myOptions));
```
---

## <a name="next-steps"></a>Další postup

V druhé části série jste se dozvěděli o tom, že aplikace s vysokou dostupností s účty úložiště RA-GRS.

Přejděte k druhé části série, kde se dozvíte, jak simulovat selhání a přinutit aplikaci použít sekundární koncový bod geograficky redundantního účtu úložiště jen pro čtení.

> [!div class="nextstepaction"]
> [Simulace selhání v připojení k primárnímu koncovému bodu úložiště](storage-simulate-failure-ragrs-account-app.md)
