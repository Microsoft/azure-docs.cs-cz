---
title: 'Kurz: Spouštění Azure Functions pomocí úloh Azure Stream Analytics | Microsoft Docs'
description: V tomto kurzu se naučíte nakonfigurovat Azure Functions jako výstupní jímku pro úlohy Stream Analytics.
services: stream-analytics
author: jasonwhowell
manager: kfile
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: mvc
ms.workload: data-services
ms.date: 04/09/2018
ms.author: jasonh
ms.reviewer: jasonh
ms.openlocfilehash: 1d33c3f0a4c36dc681aaa42bc68ae56eec234401
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="run-azure-functions-from-azure-stream-analytics-jobs"></a>Spouštění Azure Functions z úloh Azure Stream Analytics 

Azure Functions můžete spouštět z Azure Stream Analytics tak, že službu Functions nakonfigurujete jako jednu z výstupních jímek pro úlohu Stream Analytics. Služba Functions je událostmi řízené prostředí s výpočty na vyžádání, které umožňuje implementaci kódu aktivovaného událostmi, ke kterým dochází v Azure nebo ve službách třetích stran. Díky schopnosti reagovat na triggery je služba Functions přirozeným výstupem pro úlohy Azure Stream Analytics.

Stream Analytics volá službu Functions prostřednictvím triggerů HTTP. Adaptér pro výstup služby Functions umožňuje uživatelům připojit Functions k Stream Analytics tak, že události lze aktivovat na základě dotazů Stream Analytics. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvářet úlohy Stream Analytics
> * Vytvořit funkci Azure
> * Nakonfigurovat funkci Azure jako výstup pro vaši úlohu

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="configure-a-stream-analytics-job-to-run-a-function"></a>Konfigurace úlohy Stream Analytics tak, aby spouštěla funkci 

Tato část ukazuje, jak nakonfigurovat úlohu Stream Analytics tak, aby spouštěla funkci, která zapisuje data do Azure Redis Cache. Úloha Stream Analytics načítá události z Azure Event Hubs a spouští dotaz, který volá funkci. Tato funkce načítá data z úlohy Stream Analytics a zapisuje je do Azure Redis Cache.

![Diagram znázorňující vztahy mezi službami Azure](./media/stream-analytics-with-azure-functions/image1.png)

K provedení této úlohy jsou nezbytné následující kroky:
* [Vytvoření úlohy Stream Analytics, ve které jako vstup bude Event Hubs](#create-stream-analytics-job-with-event-hub-as-input)  
* [Vytvoření instance Azure Redis Cache](#create-an-azure-redis-cache)  
* [Vytvoření funkce v Azure Functions, která umožňuje zápis dat do Azure Redis Cache](#create-an-azure-function-that-can-write-data-to-the-redis-cache)    
* [Aktualizace úlohy Stream Analytics, ve které jako výstup bude daná funkce](#update-the-stream-analytic-job-with-azure-function-as-output)  
* [Kontrola výsledků v Azure Redis Cache](#check-redis-cache-for-results)  

## <a name="create-a-stream-analytics-job-with-event-hubs-as-input"></a>Vytvoření úlohy Stream Analytics, ve které jako vstup bude Event Hubs

Postupem uvedeným v kurzu [Zjišťování možných podvodů v reálném čase](stream-analytics-real-time-fraud-detection.md) vytvořte centrum událostí, spusťte aplikaci generátoru událostí a vytvořte úlohu Stream Analytics. (Přeskočte kroky pro vytvoření dotazu a výstupu. Místo toho nastavte výstup Functions pomocí následujících částí.)

## <a name="create-an-azure-redis-cache-instance"></a>Vytvoření instance Azure Redis Cache

1. Vytvořte mezipaměť v Azure Redis Cache postupem uvedeným v části [Vytvoření mezipaměti](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).  

2. Po vytvoření mezipaměti v části **Nastavení** vyberte **Přístupové klíče**. Poznamenejte si **primární připojovací řetězec**.

   ![Snímek obrazovky s připojovacím řetězcem pro Azure Redis Cache](./media/stream-analytics-with-azure-functions/image2.png)

## <a name="create-a-function-in-azure-functions-that-can-write-data-to-azure-redis-cache"></a>Vytvoření funkce v Azure Functions, která umožňuje zápis dat do Azure Redis Cache

1. Podívejte se v dokumentaci k Functions na část věnovanou [vytváření aplikací funkcí](../azure-functions/functions-create-first-azure-function.md#create-a-function-app). Provede vás postupem vytvoření aplikace funkcí a [funkce aktivované přes HTTP v Azure Functions](../azure-functions/functions-create-first-azure-function.md#create-function) pomocí jazyka CSharp.  

2. Vyhledejte funkci **run.csx**. Aktualizujte ji následujícím kódem. (Nezapomeňte nahradit text \<your redis cache connection string goes here\> primárním připojovacím řetězcem pro Azure Redis Cache, který jste získali v předchozí části.)  

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
      var connection = ConnectionMultiplexer.Connect("<your redis cache connection string goes here>");
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

   Pokud Stream Analytics přijme z této funkce výjimku „HTTP Request Entity Too Large“ (Entita požadavku HTTP je příliš velká), sníží velikost dávek, které odesílá do Functions. Použijte ve funkci následující kód, který zkontroluje, jestli Stream Analytics neodesílá dávky nadměrné velikosti. Ujistěte se, že hodnoty maximálního počtu a velikosti pro dávku, které se používají ve funkci, jsou konzistentní s hodnotami zadanými na portálu Stream Analytics.

   ```csharp
   if (dataArray.ToString().Length > 262144)
      {        
        return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
      }
   ```

3. V libovolném textovém editoru vytvořte soubor JSON s názvem **project.json**. Použijte následující kód a uložte ho do místního počítače. Tento soubor obsahuje závislosti balíčku NuGet, které vyžaduje funkce jazyka C#.  
   
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
 
   ![Snímek obrazovky s App Service Editorem](./media/stream-analytics-with-azure-functions/image3.png)

5. V App Service Editoru klikněte pravým tlačítkem myši na kořenový adresář a nahrajte soubor **project.json**. Po úspěšném nahrání aktualizujte stránku. Teď by se měl zobrazit automaticky vygenerovaný soubor s názvem **project.lock.json**. Automaticky vygenerovaný soubor obsahuje odkazy na soubory .dll, které jsou určené v souboru project.json.  

   ![Snímek obrazovky s App Service Editorem](./media/stream-analytics-with-azure-functions/image4.png)

 

## <a name="update-the-stream-analytics-job-with-the-function-as-output"></a>Aktualizace úlohy Stream Analytics, ve které jako výstup bude daná funkce

1. Otevřete úlohu Stream Analytics na portálu Azure Portal.  

2. Přejděte k dané funkci a vyberte **Přehled** > **Výstupy** > **Přidat**. Pokud chcete přidat nový výstup, vyberte **funkci Azure** pro možnost jímky. Nový adaptér pro výstup služby Functions je dostupný s následujícími vlastnostmi:  

   |**Název vlastnosti**|**Popis**|
   |---|---|
   |Alias pro výstup| Popisný název, který v dotazu úlohy používáte k odkazu na výstup |
   |Možnost importu| Můžete použít funkci z aktuálního předplatného nebo ručně zadat nastavení, pokud se funkce nachází v jiném předplatném. |
   |Function App| Název vaší aplikace Functions |
   |Funkce| Název funkce ve vaší aplikaci Functions (název vaší funkce run.csx)|
   |Maximální velikost dávky|Nastavuje maximální velikost pro každou výstupní dávku, která se odesílá do dané funkce. Ve výchozím nastavení je tato hodnota nastavená na 256 kB.|
   |Maximální počet v dávce|Určuje maximální počet událostí v každé dávce, která se odesílá do dané funkce. Výchozí hodnota je 100. Tato vlastnost je nepovinná.|
   |Klíč|Umožňuje vám použít funkci z jiného předplatného. Zadejte hodnotu klíče pro přístup k dané funkci. Tato vlastnost je nepovinná.|

3. Zadejte název aliasu pro výstup. V tomto kurzu používáme název **saop1** (můžete použít libovolný název). Zadejte další podrobnosti.  

4. Otevřete úlohu Stream Analytics a aktualizujte dotaz následujícím způsobem. (Pokud jste určili pro výstupní jímku jiný název, nezapomeňte nahradit název saop1.)  

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

5. Spusťte aplikaci telcodatagen.exe tak, že na příkazovém řádku spustíte následující příkaz (použijte formát `telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]`):  
   
   **telcodatagen.exe 1000 .2 2**
    
6.  Spusťte úlohu Stream Analytics.

## <a name="check-azure-redis-cache-for-results"></a>Kontrola výsledků v Azure Redis Cache

1. Přejděte na Azure Portal a vyhledejte Azure Redis Cache. Vyberte **Konzola**.  

2. Pomocí [příkazů mezipaměti Redis](https://redis.io/commands) ověřte, že vaše data jsou v mezipaměti Redis. (Příkaz má formát Get {klíč}.) Příklad:

   **Get "12/19/2017 21:32:24 - 123414732"**

   Tento příkaz by měl vytisknout hodnotu pro zadaný klíč:

   ![Snímek obrazovky s výstupem Azure Redis Cache](./media/stream-analytics-with-azure-functions/image5.png)

## <a name="known-issues"></a>Známé problémy

Když se na portálu Azure Portal provede pokus o resetování maximální velikosti dávky nebo maximálního počtu v dávce na prázdnou (výchozí) hodnotu, po uložení se tato hodnota změní zpět na dříve zadanou hodnotu. V tom případě zadejte výchozí hodnoty pro tato pole ručně.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte skupinu prostředků, úlohu streamování a všechny související prostředky, pokud je už nepotřebujete. Odstraněním úlohy se zabrání zaúčtování jednotek streamování, které daná úloha spotřebovává. Pokud plánujete používat tuto úlohu v budoucnu, můžete ji zastavit a znovu ji spustit později, až ji budete potřebovat. Pokud nebudete tuto úlohu nadále používat, odstraňte všechny prostředky vytvořené podle tohoto rychlého startu pomocí následujícího postupu:

1. V nabídce vlevo na portálu Azure Portal klikněte na **Skupiny prostředků** a pak klikněte na název vytvořeného prostředku.  
2. Na stránce skupiny prostředků klikněte na **Odstranit**, do textového pole zadejte prostředek, který chcete odstranit, a pak klikněte na **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili jednoduchou úlohu Stream Analytics, která spouští funkci Azure. Pokud chcete získat další informace o úlohách Stream Analytics, pokračujte dalším kurzem:

> [!div class="nextstepaction"]
> [Spouštění uživatelem definovaných funkcí jazyka JavaScript v úlohách Stream Analytics](stream-analytics-javascript-user-defined-functions.md)