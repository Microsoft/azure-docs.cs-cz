---
title: Spusťte Azure Functions z úlohy Azure Stream Analytics
description: Tento článek popisuje postup konfigurace Azure Functions jako výstupní jímku v úlohy Stream Analytics na jednotce událostí úlohy.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/19/2017
ms.openlocfilehash: a8eebfa0c40caa455eb20431e5cf4acb8eeb248c
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="run-azure-functions-from-azure-stream-analytics-jobs"></a>Spusťte Azure Functions z úlohy Azure Stream Analytics 

Azure Functions ze služby Azure Stream Analytics můžete spustit tak, že nakonfigurujete funkce jako jeden z jímky výstup do úlohy Stream Analytics. Funkce jsou událostmi, výpočetní na vyžádání prostředí, které umožňuje implementovat kód, který je aktivovaného událostmi, ke kterým dochází v Azure nebo služby třetích stran. Tato schopnost funkce reagovat na aktivační události je přirozené výstup do úlohy Stream Analytics.

Stream Analytics vyvolá funkce prostřednictvím protokolu HTTP aktivační události. Adaptér pro výstup funkce umožňuje uživatelům připojit funkce do služby Stream Analytics, tak, že události můžete spustit založená na dotazech Stream Analytics. 

Tento kurz ukazuje, jak připojit Stream Analytics k [Azure Redis Cache](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md), pomocí [Azure Functions](../azure-functions/functions-overview.md). 

## <a name="configure-a-stream-analytics-job-to-run-a-function"></a>Konfigurace funkce spuštění úlohy Stream Analytics 

V této části ukazuje, jak nakonfigurovat spuštění funkci, která zapisuje data do Azure Redis Cache úlohy Stream Analytics. Úloha Stream Analytics načítá události z Azure Event Hubs a spustí dotaz, který volá funkci. Tato funkce čte data z úlohy Stream Analytics a zapíše ho do Azure Redis Cache.

![Diagram znázorňující vztahy mezi služeb Azure](./media/stream-analytics-with-azure-functions/image1.png)

Následující kroky jsou nezbytné pro dosažení této úlohy:
* [Vytvořit úlohu služby Stream Analytics službou Event Hubs jako vstup](#create-stream-analytics-job-with-event-hub-as-input)  
* [Vytvořte instanci služby Azure Redis Cache](#create-an-azure-redis-cache)  
* [Vytvoří funkci, v Azure Functions, který může zapsat data do Azure Redis Cache](#create-an-azure-function-that-can-write-data-to-the-redis-cache)    
* [Aktualizace úlohy služby Stream Analytics s funkcí jako výstup](#update-the-stream-analytic-job-with-azure-function-as-output)  
* [Zkontrolujte výsledky Azure Redis Cache](#check-redis-cache-for-results)  

## <a name="create-a-stream-analytics-job-with-event-hubs-as-input"></a>Vytvořit úlohu služby Stream Analytics službou Event Hubs jako vstup

Postupujte podle [odhalování podvodů v reálném čase](stream-analytics-real-time-fraud-detection.md) kurzu vytváření centra událostí, spusťte aplikaci generátor události a vytvořit úlohu služby Stream Analytics. (Přeskočit kroky k vytvoření dotaz a výstup. Místo toho najdete v následujících částech k nastavení funkce výstupu.)

## <a name="create-an-azure-redis-cache-instance"></a>Vytvořte instanci služby Azure Redis Cache

1. Vytvoření mezipaměti ve službě Azure Redis Cache pomocí kroků popsaných v [vytvoření mezipaměti](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).  

2. Po vytvoření mezipaměti, v části **nastavení**, vyberte **přístupové klíče**. Poznamenejte si **primární připojovací řetězec**.

   ![Snímek obrazovky z Azure Redis Cache připojovací řetězec](./media/stream-analytics-with-azure-functions/image2.png)

## <a name="create-a-function-in-azure-functions-that-can-write-data-to-azure-redis-cache"></a>Vytvoří funkci, v Azure Functions, který může zapsat data do Azure Redis Cache

1. Najdete v článku [vytvořit aplikaci function app](../azure-functions/functions-create-first-azure-function.md#create-a-function-app) části dokumentace funkce. To vás provede procesem vytvoření funkce aplikace a [funkce aktivované protokolem HTTP v Azure Functions](../azure-functions/functions-create-first-azure-function.md#create-function), pomocí jazyka CSharp.  

2. Vyhledejte **run.csx** funkce. Aktualizujte jej s následujícím kódem. (Nezapomeňte nahradit "\<místo připojovacího řetězce redis cache\>" s Azure Redis Cache primární připojovací řetězec, který jste získali v předchozí části.)  

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

   Když Stream Analytics přijme "HTTP požadavku Entity příliš velké" výjimky z funkce, snižuje velikost dávky, který odesílá funkce. Ve funkci použijte následující kód zkontrolujte, že Stream Analytics neodešle nadměrné velikosti dávky. Ujistěte se, že jsou konzistentní s hodnotou zadanou v portálu Stream Analytics batch maximální počet a velikost hodnoty používané ve funkci.

   ```csharp
   if (dataArray.ToString().Length > 262144)
      {        
        return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
      }
   ```

3. V textovém editoru podle vaší volby, vytvořte soubor JSON s názvem **project.json**. Použít následující kód a uložte ho do místního počítače. Tento soubor obsahuje závislosti balíčku NuGet, požadované funkce jazyka C#.  
   
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
 
4. Přejděte zpět na portálu Azure. Z **funkce** kartě, přejděte na funkce. V části **nástroje pro vývoj**, vyberte **App Service Editor**. 
 
   ![Snímek obrazovky z aplikace služby editoru](./media/stream-analytics-with-azure-functions/image3.png)

5. V editoru aplikace služby klikněte pravým tlačítkem na kořenový adresář a odeslat **project.json** souboru. Po úspěšném odeslání aktualizujte stránku. Teď byste měli vidět automaticky generovaný soubor s názvem **project.lock.json**. Automaticky generovaný soubor obsahuje odkazy na soubory .dll, které jsou určené v souboru project.json.  

   ![Snímek obrazovky z aplikace služby editoru](./media/stream-analytics-with-azure-functions/image4.png)

 

## <a name="update-the-stream-analytics-job-with-the-function-as-output"></a>Aktualizace úlohy služby Stream Analytics s funkcí jako výstup

1. Spusťte úlohu služby Stream Analytics na portálu Azure.  

2. Přejděte do funkce a vyberte **přehled** > **výstupy** > **přidat**. Chcete-li přidat nový výstup, vyberte **funkce Azure** pro možnost jímky. Adaptér pro výstup nové funkce je k dispozici, s následujícími vlastnostmi:  

   |**Název vlastnosti**|**Popis**|
   |---|---|
   |Alias pro výstup| Popisný název, který používáte v dotazu úlohy k odkazu na výstup. |
   |Možnost importu| Můžete použít funkci z aktuálního předplatného, nebo ručně zadejte nastavení, pokud se funkce nachází v jiné předplatné. |
   |Function App| Název funkce aplikace. |
   |Funkce| Název funkce v aplikaci funkce (název funkce run.csx).|
   |Velikost dávky maximální|Nastaví maximální velikost pro každou dávku výstup, která se posílají funkce. Ve výchozím nastavení je tato hodnota nastavena na 256 KB.|
   |Maximální počet dávek|Určuje maximální počet událostí v každé dávce, které je odesláno funkce. Výchozí hodnota je 100. Tato vlastnost je nepovinná.|
   |Klíč|Umožňuje používat funkce z jiné předplatné. Zadejte hodnotu klíče pro přístup k vaší funkci. Tato vlastnost je nepovinná.|

3. Zadejte název pro alias pro výstup. V tomto kurzu jsme název **saop1** (můžete použít libovolný název zvoleného). Zadejte další podrobnosti.  

4. Otevřete úlohu služby Stream Analytics a aktualizujte následující dotaz. (Nezapomeňte nahradit text "saop1" Pokud výstupní jímku mít jiný název.)  

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

5. Spustit aplikaci telcodatagen.exe spuštěním následujícího příkazu na příkazovém řádku (použijte formát `telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]`):  
   
   **telcodatagen.exe 1000.2 2**
    
6.  Spustíte úlohu služby Stream Analytics.

## <a name="check-azure-redis-cache-for-results"></a>Zkontrolujte výsledky Azure Redis Cache

1. Přejděte na portál Azure a najít Azure Redis Cache. Vyberte **konzoly**.  

2. Použití [Redis cache příkazy](https://redis.io/commands) k ověření, že vaše data jsou v mezipaměti Redis. (Příkaz má formát {klíč} Get.) Příklad:

   **Získat "12/19/2017 21:32:24-123414732"**

   Tento příkaz má tisk hodnoty pro zadaný klíč:

   ![Snímek obrazovky z Azure Redis Cache výstup](./media/stream-analytics-with-azure-functions/image5.png)

## <a name="known-issues"></a>Známé problémy

Na portálu Azure, při pokusu o resetování velikost dávky maximální / hodnotu prázdný (výchozí), počet dávek maximální hodnota změní zpět na dříve zadanou hodnotu na Uložit. Ručně zadejte výchozí hodnoty pro tato pole v tomto případě.

