---
title: Kurz – spuštění Azure Functions v úlohách Azure Stream Analytics
description: V tomto kurzu se naučíte nakonfigurovat Azure Functions jako výstupní jímku pro úlohy Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/27/2020
ms.openlocfilehash: 74e09e61a6132858d716686bdb6687bb670f0d33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98879507"
---
# <a name="tutorial-run-azure-functions-from-azure-stream-analytics-jobs"></a>Kurz: spuštění Azure Functions z úloh Azure Stream Analytics 

Azure Functions můžete spouštět z Azure Stream Analytics tak, že službu Functions nakonfigurujete jako jednu z výstupních jímek pro úlohu Stream Analytics. Služba Functions je událostmi řízené prostředí s výpočty na vyžádání, které umožňuje implementaci kódu aktivovaného událostmi, ke kterým dochází v Azure nebo ve službách třetích stran. Díky schopnosti reagovat na triggery je služba Functions přirozeným výstupem pro úlohy Azure Stream Analytics.

Stream Analytics volá službu Functions prostřednictvím triggerů HTTP. Adaptér pro výstup služby Functions umožňuje uživatelům připojit Functions k Stream Analytics tak, že události lze aktivovat na základě dotazů Stream Analytics. 

> [!NOTE]
> Připojení k Azure Functions uvnitř virtuální sítě (VNet) z úlohy Stream Analytics, která běží v clusteru s více klienty, se nepodporuje.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření a spuštění úlohy Stream Analytics
> * Vytvoření mezipaměti Azure pro instanci Redis
> * Vytvoření funkce Azure Function
> * Podívejte se na výsledky Azure cache for Redis

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="configure-a-stream-analytics-job-to-run-a-function"></a>Konfigurace úlohy Stream Analytics tak, aby spouštěla funkci 

Tato část ukazuje, jak nakonfigurovat úlohu Stream Analytics pro spuštění funkce, která zapisuje data do mezipaměti Azure pro Redis. Úloha Stream Analytics načítá události z Azure Event Hubs a spouští dotaz, který volá funkci. Tato funkce čte data z Stream Analytics úlohy a zapisuje je do mezipaměti Azure pro Redis.

![Diagram znázorňující vztahy mezi službami Azure](./media/stream-analytics-with-azure-functions/image1.png)

## <a name="create-a-stream-analytics-job-with-event-hubs-as-input"></a>Vytvoření úlohy Stream Analytics, ve které jako vstup bude Event Hubs

Postupem uvedeným v kurzu [Zjišťování možných podvodů v reálném čase](stream-analytics-real-time-fraud-detection.md) vytvořte centrum událostí, spusťte aplikaci generátoru událostí a vytvořte úlohu Stream Analytics. Přeskočte kroky pro vytvoření dotazu a výstupu. Místo toho si v následujících částech nastavte výstup Azure Functions.

## <a name="create-an-azure-cache-for-redis-instance"></a>Vytvoření mezipaměti Azure pro instanci Redis

1. Vytvořte mezipaměť v mezipaměti Azure pro Redis pomocí kroků popsaných v tématu [vytvoření mezipaměti](../azure-cache-for-redis/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).  

2. Po vytvoření mezipaměti v části **Nastavení** vyberte **Přístupové klíče**. Poznamenejte si **primární připojovací řetězec**.

   ![Snímek obrazovky s mezipamětí Azure cache pro připojovací řetězec Redis](./media/stream-analytics-with-azure-functions/image2.png)

## <a name="create-a-function-in-azure-functions-that-can-write-data-to-azure-cache-for-redis"></a>Vytvoření funkce v Azure Functions, která může zapisovat data do mezipaměti Azure pro Redis

1. Podívejte se v dokumentaci k Functions na část věnovanou [vytváření aplikací funkcí](../azure-functions/functions-get-started.md). V této části se dozvíte, jak vytvořit aplikaci funkcí a funkci aktivovanou [protokolem HTTP v Azure Functions](../azure-functions/functions-get-started.md)pomocí jazyka CSharp.  

2. Vyhledejte funkci **run.csx**. Aktualizujte ji následujícím kódem. Nahraďte **" \<your Azure Cache for Redis connection string goes here\> "** pomocí mezipaměti Azure pro primární připojovací řetězec Redis, který jste získali v předchozí části. 

    ```csharp
    using System;
    using System.Net;
    using System.Threading.Tasks;
    using StackExchange.Redis;
    using Newtonsoft.Json;
    using System.Configuration;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
    
        // Get the request body
        dynamic dataArray = await req.Content.ReadAsAsync<object>();

        // Throw an HTTP Request Entity Too Large exception when the incoming batch(dataArray) is greater than 256 KB. Make sure that the size value is consistent with the value entered in the Stream Analytics portal.

        if (dataArray.ToString().Length > 262144)
        {
            return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
        }
        var connection = ConnectionMultiplexer.Connect("<your Azure Cache for Redis connection string goes here>");
        log.Info($"Connection string.. {connection}");
    
        // Connection refers to a property that returns a ConnectionMultiplexer
        IDatabase db = connection.GetDatabase();
        log.Info($"Created database {db}");
    
        log.Info($"Message Count {dataArray.Count}");

        // Perform cache operations using the cache object. For example, the following code block adds few integral data types to the cache
        for (var i = 0; i < dataArray.Count; i++)
        {
            string time = dataArray[i].time;
            string callingnum1 = dataArray[i].callingnum1;
            string key = time + " - " + callingnum1;
            db.StringSet(key, dataArray[i].ToString());
            log.Info($"Object put in database. Key is {key} and value is {dataArray[i].ToString()}");
       
            // Simple get of data types from the cache
            string value = db.StringGet(key);
            log.Info($"Database got: {value}");
        }

        return req.CreateResponse(HttpStatusCode.OK, "Got");
    }

   ```

   Pokud Stream Analytics přijme z této funkce výjimku „HTTP Request Entity Too Large“ (Entita požadavku HTTP je příliš velká), sníží velikost dávek, které odesílá do Functions. Následující kód zajišťuje, že Stream Analytics neposílá příliš velké dávky. Ujistěte se, že hodnoty maximálního počtu a velikosti pro dávku, které se používají ve funkci, jsou konzistentní s hodnotami zadanými na portálu Stream Analytics.

    ```csharp
    if (dataArray.ToString().Length > 262144)
        {
            return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
        }
   ```

3. V libovolném textovém editoru vytvořte soubor JSON s názvem **project.json**. Vložte následující kód a uložte ho do místního počítače. Tento soubor obsahuje závislosti balíčku NuGet, které vyžaduje funkce jazyka C#.  
   
    ```json
    {
        "frameworks": {
            "net46": {
                "dependencies": {
                    "StackExchange.Redis":"1.1.603",
                    "Newtonsoft.Json": "9.0.1"
                }
            }
        }
    }

   ```
 
4. Vraťte se na Azure Portal. Na kartě **Funkce platformy** vyhledejte danou funkci. V části **Vývojové nástroje** vyberte **App Service Editor**. 
 
   ![Snímek obrazovky zobrazuje kartu funkce platformy s vybraným Editor služby App Service.](./media/stream-analytics-with-azure-functions/image3.png)

5. V App Service Editoru klikněte pravým tlačítkem myši na kořenový adresář a nahrajte soubor **project.json**. Po úspěšném nahrání aktualizujte stránku. Teď by se měl zobrazit automaticky vygenerovaný soubor s názvem **project.lock.json**. Automaticky vygenerovaný soubor obsahuje odkazy na soubory .dll, které jsou určené v souboru project.json.  

   ![Snímek obrazovky s informacemi o nahrání souborů vybraných z nabídky](./media/stream-analytics-with-azure-functions/image4.png)

## <a name="update-the-stream-analytics-job-with-the-function-as-output"></a>Aktualizace úlohy Stream Analytics, ve které jako výstup bude daná funkce

1. Otevřete úlohu Stream Analytics na portálu Azure Portal.  

2. Přejděte na svou funkci a vyberte **Přehled**  >  **výstupy**  >  **Přidat**. Pokud chcete přidat nový výstup, vyberte **funkci Azure** pro možnost jímky. Adaptér pro výstup funkcí má následující vlastnosti:  

   |**Název vlastnosti**|**Popis**|
   |---|---|
   |Alias pro výstup| Popisný název, který v dotazu úlohy používáte k odkazu na výstup |
   |Možnost importu| Můžete použít funkci z aktuálního předplatného nebo ručně zadat nastavení, pokud se funkce nachází v jiném předplatném. |
   |Function App| Název vaší aplikace Functions |
   |Funkce| Název funkce ve vaší aplikaci Functions (název vaší funkce run.csx)|
   |Maximální velikost dávky|Nastaví maximální velikost pro každou výstupní dávku, která se pošle do vaší funkce v bajtech. Ve výchozím nastavení je tato hodnota nastavená na 262 144 bajtů (256 KB).|
   |Maximální počet v dávce|Určuje maximální počet událostí v každé dávce, která se odesílá do dané funkce. Výchozí hodnota je 100. Tato vlastnost je nepovinná.|
   |Klíč|Umožňuje vám použít funkci z jiného předplatného. Zadejte hodnotu klíče pro přístup k dané funkci. Tato vlastnost je nepovinná.|

3. Zadejte název aliasu pro výstup. V tomto kurzu se jmenuje **saop1**, ale můžete použít libovolný název. Zadejte další podrobnosti.

4. Otevřete úlohu Stream Analytics a aktualizujte dotaz následujícím způsobem. Pokud jste své výstupní jímky nevytvořili **saop1**, nezapomeňte je změnit v dotazu.  

   ```sql
    SELECT
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1,
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        INTO saop1
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
           JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum
   ```

5. Spusťte aplikaci telcodatagen.exe spuštěním následujícího příkazu v příkazovém řádku. Příkaz používá formát `telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]` .  
   
   ```cmd
   telcodatagen.exe 1000 0.2 2
   ```
    
6.  Spusťte úlohu Stream Analytics.

## <a name="check-azure-cache-for-redis-for-results"></a>Podívejte se na výsledky Azure cache for Redis

1. Přejděte do Azure Portal a vyhledejte mezipaměť Azure pro Redis. Vyberte **Konzola**.  

2. Pomocí [Azure cache pro příkazy Redis](https://redis.io/commands) ověřte, že jsou vaše data v mezipaměti Azure pro Redis. (Příkaz má formát Get {klíč}.) Například:

   **Get "12/19/2017 21:32:24 - 123414732"**

   Tento příkaz by měl vytisknout hodnotu pro zadaný klíč:

   ![Snímek obrazovky Azure cache pro výstup Redis](./media/stream-analytics-with-azure-functions/image5.png)

## <a name="error-handling-and-retries"></a>Zpracování chyb a opakování

Pokud při posílání událostí do Azure Functions dojde k selhání, Stream Analytics opakuje většinu operací. Všechny výjimky http se zopakují až do úspěchu s výjimkou chyby HTTP 413 (entita je moc velká). Entita příliš velká chyba je považována za chybu dat, která je předmětem [zásad opakování nebo vyřazení](stream-analytics-output-error-policy.md).

> [!NOTE]
> Časový limit pro požadavky HTTP od Stream Analytics do Azure Functions je nastaven na 100 sekund. Pokud vaše aplikace Azure Functions pro zpracování dávky trvá déle než 100 sekund, Stream Analytics chyby a rety se pro dávku.

Opakovaný pokus o vypršení časového limitu může způsobit duplicitní události zapsané do výstupní jímky. Když se Stream Analytics pokusy o selhání dávky, pokusy se znovu pokusí o všechny události v dávce. Představte si například dávku 20 událostí, které se odesílají do Azure Functions z Stream Analytics. Předpokládejme, že Azure Functions zpracování prvních 10 událostí v této dávce trvá 100 sekund. Po 100 sekundách Pass Stream Analytics pozastaví požadavek, protože neobdržel kladnou odpověď od Azure Functions a pošle se do stejné dávky další požadavek. Prvních 10 událostí v dávce se znovu zpracuje pomocí Azure Functions, což způsobí duplicitní. 

## <a name="known-issues"></a>Známé problémy

Když se na portálu Azure Portal provede pokus o resetování maximální velikosti dávky nebo maximálního počtu v dávce na prázdnou (výchozí) hodnotu, po uložení se tato hodnota změní zpět na dříve zadanou hodnotu. V tom případě zadejte výchozí hodnoty pro tato pole ručně.

Použití [Směrování http](/sandbox/functions-recipes/routes?tabs=csharp) na vašem Azure Functions v současnosti není v Stream Analytics podporováno.

Podpora pro připojení k Azure Functions hostované ve virtuální síti není povolená.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte skupinu prostředků, úlohu streamování a všechny související prostředky, pokud je už nepotřebujete. Odstraněním úlohy se zabrání zaúčtování jednotek streamování, které daná úloha spotřebovává. Pokud plánujete používat tuto úlohu v budoucnu, můžete ji zastavit a znovu ji spustit později, až ji budete potřebovat. Pokud nebudete tuto úlohu nadále používat, odstraňte všechny prostředky vytvořené podle tohoto rychlého startu pomocí následujícího postupu:

1. V nabídce vlevo na portálu Azure Portal klikněte na **Skupiny prostředků** a pak klikněte na název vytvořeného prostředku.  
2. Na stránce skupiny prostředků klikněte na **Odstranit**, do textového pole zadejte prostředek, který chcete odstranit, a pak klikněte na **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili jednoduchou úlohu Stream Analytics, která spouští funkci Azure Functions. Další informace o úlohách Stream Analytics získáte v dalším kurzu:

> [!div class="nextstepaction"]
> [Spouštění uživatelem definovaných funkcí jazyka JavaScript v úlohách Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
