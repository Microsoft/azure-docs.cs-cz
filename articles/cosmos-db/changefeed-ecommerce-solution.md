---
title: Použití Azure Cosmos DB změnit informační kanál k vizualizaci dat v reálném čase analýzy
description: Tento článek popisuje, jak kanál změn umožňuje společností maloobchodní porozumět trendům uživatele, provedení analýzy dat v reálném čase a vizualizace.
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 08/12/2018
ms.author: sngun
ms.openlocfilehash: 3f42e149b88c2405a37366c3c7b886c7a8613e00
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54382547"
---
# <a name="use-azure-cosmos-db-change-feed-to-visualize-real-time-data-analytics"></a>Použití Azure Cosmos DB změnit informační kanál k vizualizaci dat v reálném čase analýzy

Kanál změn služby Azure Cosmos DB je mechanismus pro získání informační kanál průběžné a přírůstkové záznamů z kontejneru služby Azure Cosmos DB, jak se tyto záznamy vytvořené nebo změněné. Změna kanálu funguje podpora prostřednictvím naslouchání kontejner pro všechny změny. Potom vypíše seřazený seznam dokumentů, které byly změněny v pořadí, ve kterém byly změněny. Další informace o kanálu změn najdete v tématu [práce díky kanálu změn](change-feed.md) článku. 

Tento článek popisuje, jak kanál změn umožňuje společnost elektronického obchodování porozumět trendům uživatele, provedení analýzy dat v reálném čase a vizualizace. Probereme události, jako je například uživatel položku zobrazení, přidání položky do jejich košíku nebo zakoupení položku. Při jedné z těchto událostí se vytvoří nový záznam a změny kanálu protokolů, které zaznamenávají. Změna kanálu pak aktivační události sérii kroků, což vede k vizualizaci metrik, které analýzu výkonu společnosti a aktivity. Ukázkové metriky, které můžete vizualizovat zahrnout výnosy, jedinečných návštěvníků, Nejoblíbenější položky a průměrná cena zboží, které lze zobrazit a přidat do košíku a zakoupit. Tyto ukázkové metriky může pomoct e-commerce společnosti vyhodnotit své popularitě už web, vývoj jeho reklamy a cenové strategie a učinit rozhodnutí týkající se co inventáře investovat do.

Zájem o sledování na video o řešení než začnete, podívejte se na následující video:

> [!VIDEO https://www.youtube.com/embed/AYOiMkvxlzo]
>

## <a name="solution-components"></a>Součásti řešení
Následující diagram znázorňuje tok dat a součásti účastnící se řešení:

![Projekt visual](./media/changefeed-ecommerce-solution/project-visual.png)
 
1. **Generování dat:** Simulátor dat slouží ke generování prodejní data, která představuje události, jako je například uživatel položku zobrazení, přidání položky do jejich košíku a zakoupení položku. Velkou sadu ukázkových dat můžete vygenerovat pomocí generátoru dat. Generovaných vzorových dat obsahuje dokumenty v následujícím formátu:
   
   ```json
   {      
     "CartID": 2486,
     "Action": "Viewed",
     "Item": "Women's Denim Jacket",
     "Price": 31.99
   }
   ```

2. **Cosmos DB:** Generovaná data se ukládá v kolekci Azure Cosmos DB.  

3. **Kanál změn:** Kanál změn bude naslouchat změny kolekce Azure Cosmos DB. Pokaždé, když do kolekce (která je při výskytu události, uživatelem zobrazení položky, přidání položky do jejich košíku nebo zakoupením položky) se přidá nový dokument, změna kanálu bude aktivovat [funkce Azure Functions](../azure-functions/functions-overview.md).  

4. **Funkce Azure:** Funkce Azure Functions zpracovává nová data a odesílá je do [Azure Event Hubs](../event-hubs/event-hubs-about.md).  

5. **Centrum událostí:** Azure Event Hubs uchovává tyto události a odesílá je do [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) k další analýze.  

6. **Azure Stream Analytics:** Azure Stream Analytics definuje dotazů pro zpracování událostí a analýze dat v reálném čase. Tato data se pak posílají do [Microsoft Power BI](https://docs.microsoft.com/power-bi/desktop-what-is-desktop).  

7. **Power BI:** Power BI se využívá k vizualizaci dat odesílaných v Azure Stream Analytics. Můžete vytvořit řídicí panel zobrazit, jak změnit metrik v reálném čase.  

## <a name="prerequisites"></a>Požadavky

* Microsoft .NET Framework 4.7.1 nebo vyšší

* Microsoft .NET Core 2.1 (nebo vyšší)

* Visual Studio pomocí úlohy vývoj pro ASP.NET a web, vývoj pro univerzální platformu Windows a vývoj desktopových aplikací .NET

* Předplatné Microsoft Azure

* Účet Microsoft Power BI

* Stáhněte si [kanálu změn databáze Azure Cosmos DB lab](https://github.com/Azure-Samples/azure-cosmos-db-change-feed-dotnet-retail-sample) z Githubu. 

## <a name="create-azure-resources"></a>Vytvoření prostředků Azure 

Vytvoření prostředků Azure – Azure Cosmos DB, účet úložiště, Centrum událostí, Stream Analytics požadováno řešením. Naučíte se nasadit tyto prostředky pomocí šablony Azure Resource Manageru. Použijte následující kroky pro nasazování těchto prostředků: 

1. Nastavte zásady spouštění prostředí Windows PowerShell na **Unrestricted**. Chcete-li to provést, otevřete **prostředí Windows PowerShell jako správce** a spusťte následující příkazy:

   ```powershell
   Get-ExecutionPolicy
   Set-ExecutionPolicy Unrestricted 
   ```

2. Z úložiště GitHub, který jste si stáhli v předchozím kroku, přejděte **Azure Resource Manageru** složky a otevřete soubor, který nazývá **parameters.json** souboru.  

3. Zadejte hodnoty pro cosmosdbaccount_name eventhubnamespace_name, storageaccount_name, parametry, jak je uvedeno v **parameters.json** souboru. Musíte použít názvy, které poskytnete později ke každé z vašich prostředků.  

4. Z **prostředí Windows PowerShell**, přejděte **Azure Resource Manageru** složky a spusťte následující příkaz:

   ```powershell
   .\deploy.ps1
   ```
5. Po zobrazení výzvy zadejte Azure **ID předplatného**, **changefeedlab** pro název skupiny prostředků a **run1** pro název nasazení. Jakmile začít nasazovat prostředky, může trvat až 10 minut, než se dokončí.

## <a name="create-a-database-and-the-collection"></a>Vytvoření databáze a kolekce

Teď vytvoříte kolekci, podržte událostí webu elektronického obchodování. Když uživatel zobrazí položka, přidá položku do jejich košíku nebo nákupy položky, kolekce se zobrazí záznam, který se týká také akce ("Zobrazit", "Přidání" nebo "koupit"), název položky ověřovaného cena související položky a identifikační číslo uživatele košíku i nvolved.

1. Přejděte na [webu Azure Portal](https://portal.azure.com/) a najít **účet služby Azure Cosmos DB** , který je vytvořil šablonu nasazení.  

2. Z **Průzkumník dat** vyberte **novou kolekci** , vyplňte formulář s následujícími podrobnostmi:  

   * Pro **id databáze** pole, vyberte **vytvořit nový**, zadejte **changefeedlabdatabase**. Nechte **propustnosti zřídit databáze** políčko nezaškrtnuté.  
   * Pro **kolekce** id zadejte **changefeedlabcollection**.  
   * Pro **klíč oddílu** zadejte **/položky**. Toto je velká a malá písmena, proto zkontrolujte, že zadáte jeho správně.  
   * Pro **propustnost** zadejte **10000**.  
   * Vyberte tlačítko **OK**.  

3. Dále vytvořte další kolekci s názvem **zapůjčení** změnu informačního kanálu zpracování. Souřadnice kolekci zapůjčení zpracování kanálu změn napříč několika pracovních procesů. Samostatné kolekce slouží k uložení zapůjčení s jeden zapůjčení na oddíl.  

4.  Vraťte se **Průzkumník dat** podokně a vyberte **novou kolekci** , vyplňte formulář s následujícími podrobnostmi:

   * Pro **id databáze** pole, vyberte **použít existující**, zadejte **changefeedlabdatabase**.  
   * Pro **id kolekce** zadejte **zapůjčení**.  
   * Pro **kapacitu úložiště**vyberte **Fixed**.  
   * Nechte **propustnost** nastaveno na výchozí hodnotu.  
   * Vyberte tlačítko **OK**.

## <a name="get-the-connection-string-and-keys"></a>Získejte připojovací řetězec a klíčů

### <a name="get-the-azure-cosmos-db-connection-string"></a>Získání připojovacího řetězce služby Azure Cosmos DB

1. Přejděte na [webu Azure Portal](https://portal.azure.com/) a najít **účet služby Azure Cosmos DB** , který je vytvořil šablonu nasazení.  

2. Přejděte **klíče** podokně zkopírujte primární PŘIPOJOVACÍ řetězec a zkopírovat ho do poznámkového bloku nebo jiného dokumentu, zda bude mít přístup k v rámci testovacího prostředí. Byste měli popsat **připojovací řetězec Cosmos DB**. Budete muset později zkopírujte řetězec do kódu, takže poznamenejte a mějte na paměti, kde jsou ukládání.

### <a name="get-the-storage-account-key-and-connection-string"></a>Získat řetězec připojení a klíč účtu úložiště

Účty Azure Storage umožňují ukládat data. V tomto testovacím prostředí použijete účet úložiště k ukládání dat, který používá funkce Azure functions. Funkce Azure Functions se aktivuje, když je každá změna provedená v kolekci.

1. Vraťte se do vaší skupiny prostředků a otevřít účet úložiště, který jste vytvořili dříve  

2. Vyberte **přístupové klíče** z nabídky na levé straně.  

3. Zkopírujte hodnoty v rámci **klíč 1** do poznámkového bloku nebo jiného dokumentu, zda bude mít přístup k v rámci testovacího prostředí. Byste měli popsat **klíč** jako **klíč úložiště** a **připojovací řetězec** jako **připojovací řetězec úložiště**. Budete muset zkopírovat tyto řetězce do kódu později, tak poznamenejte a mějte na paměti, kde jsou jejich ukládání.  

### <a name="get-the-event-hub-namespace-connection-string"></a>Získat připojovací řetězec obor názvů centra událostí

Azure Event Hubs přijímá data událostí, úložiště, procesy a předá data. V tomto testovacím prostředí, Azure Event Hubs obdrží dokumentu pokaždé, když dojde k vytvoření nové události (například zobrazena uživateli, přidat do košíku uživatele nebo položky měnách uživatel) a pak předá tento dokument ke službě Azure Stream Analytics.

1. Vraťte se do vaší skupiny prostředků a otevřít **Event Hub Namespace** , který jste vytvořili a s názvem v prelab.  

2. Vyberte **zásady sdíleného přístupu** z nabídky na levé straně.  

3. Vyberte **RootManageSharedAccessKey**. Kopírovat **připojovací řetězec – primární klíč** do poznámkového bloku nebo jiného dokumentu, zda bude mít přístup k v rámci testovacího prostředí. Byste měli popsat **Event Hub Namespace** připojovací řetězec. Budete muset později zkopírujte řetězec do kódu, takže poznamenejte a mějte na paměti, kde jsou ukládání.

## <a name="set-up-azure-function-to-read-the-change-feed"></a>Nastavení funkce Azure Functions ke čtení kanálu změn

Při vytvoření nového dokumentu nebo změně aktuální dokument v kolekci Cosmos DB, automaticky kanálu změn přidá daný upravený dokument do jeho historii změn kolekce. Nyní bude sestavovat a spouštět funkce Azure, která zpracovává kanálu změn. Při vytvoření nebo úpravě v kolekci, kterou jste vytvořili dokument, funkce Azure Functions se aktivuje pomocí kanálu změn. Funkce Azure Functions bude odešlete upravený dokument do centra událostí.

1. Vraťte se do úložiště, které jste naklonovali na vašem zařízení.  

2. Klikněte pravým tlačítkem na soubor s názvem **ChangeFeedLabSolution.sln** a vyberte **otevřít pomocí sady Visual Studio**.  

3. Přejděte do **local.settings.json** v sadě Visual Studio. Potom použijte hodnoty, které jste si poznamenali dříve vyplnit prázdné hodnoty.  

4. Přejděte do **ChangeFeedProcessor.cs**. V parametrech **spustit** funkce, proveďte následující akce:  

   * Nahraďte text **vaše KOLEKCE název zde** s názvem vaší kolekce. Pokud jste postupovali podle předchozích pokynů, je název vaší kolekce changefeedlabcollection.  
   * Nahraďte text **YOUR ZAPŮJČENÍ KOLEKCE název zde** s názvem kolekci zapůjčení. Pokud jste postupovali podle předchozích pokynů, je název vaší kolekce zapůjčení **zapůjčení**.  
   * V horní části sady Visual Studio, ujistěte se, že pole spouštěný projekt na levé straně na zelenou šipku říká **ChangeFeedFunction**.  
   * Vyberte **Start** v horní části stránky na spuštění programu  
   * Můžete potvrdit, že je funkce spuštěná, když aplikace konzoly říká "spuštění úlohy hostitele".

## <a name="insert-data-into-azure-cosmos-db"></a>Vložení dat do služby Azure Cosmos DB 

Chcete-li zobrazit zpracování kanálu změn nové akce na webu elektronického obchodování, třeba simulovat data, která představuje uživatele zobrazení položek z katalogu produktů, přidání položky do jejich košíky a při nákupu položek v jejich košíky. Tato data jsou libovolná a pro účely replikace jaká data na elektronického obchodování lokality vypadat nějak takto.

1. Přejděte zpět do úložiště v Průzkumníkovi souborů a klikněte pravým tlačítkem na **ChangeFeedFunction.sln** znovu otevřít nové okno Visual Studio.  

2. Přejděte **App.config** souboru. V rámci <appSettings> blokovat, přidejte identifikátor URI a jedinečný **primární klíč** u účtu služby Azure Cosmos DB, který jste získali dříve.  

3. Přidejte **kolekce** a **databáze** názvy. (Tyto názvy musí být **changefeedlabcollection** a **changefeedlabdatabase** nerozhodnete pojmenujte svůj odlišně.)

   ![Aktualizovat připojovací řetězce](./media/changefeed-ecommerce-solution/update-connection-string.png)
 
4. Uložte změny na všechny soubory upravovat.  

5. V horní části sady Visual Studio, ujistěte se, že **spouštěný projekt** říká políčko nalevo od zelené šipce **DataGenerator**. Potom vyberte **Start** v horní části stránky a spusťte program.  
 
6. Počkejte spuštění programu. Hvězdiček znamená, že data se chystá v! Zachovat spuštěný program – je důležité, že velké množství dat shromažďovaných.  

7. Když přejdete na [webu Azure Portal](https://portal.azure.com/) , Cosmos DB účtu v rámci vaší skupiny prostředků, klikněte na **Průzkumník dat**, uvidíte náhodná data naimportovat do vaší  **changefeedlabcollection** .
 
   ![Data generovaná v portálu](./media/changefeed-ecommerce-solution/data-generated-in-portal.png)

## <a name="set-up-a-stream-analytics-job"></a>Nastavení úlohy stream analytics

Azure Stream Analytics je plně spravovaná Cloudová služba pro zpracování streamovaných dat v reálném čase. V tomto testovacím prostředí použijete stream analytics zpracovávat nové události z centra událostí (například při zobrazit, přidat do košíku nebo položky zakoupili), začlenit tyto události do analýzy dat v reálném čase a odeslat je do Power BI pro vizualizace.

1. Z [webu Azure Portal](https://portal.azure.com/), přejděte do vaší skupiny prostředků klikněte na **streamjob1** (úloha stream analytics, které jste vytvořili prelab).  

2. Vyberte **vstupy** jak je znázorněno níže.  

   ![Vytvořit zadání](./media/changefeed-ecommerce-solution/create-input.png)

3. Vyberte **+ přidat vstup streamu**. Potom vyberte **centra událostí** z rozevírací nabídky.  

4. Zadejte nový vstupní formulář s následujícími podrobnostmi:

   * V **vstupní** alias pole, zadejte **vstupní**.  
   * Vyberte možnost pro **vyberte Centrum událostí z vašich předplatných**.  
   * Nastavte **předplatné** pole do vašeho předplatného.  
   * V **obor názvů centra událostí** pole, zadejte název vaší Namespace centra událostí, kterou jste vytvořili během prelab.  
   * V **název centra událostí** pole, vyberte možnost pro **použít existující** a zvolte **události hub1** z rozevírací nabídky.  
   * Ponechte **zásady centra událostí** název pole nastaveno na výchozí hodnotu.  
   * Ponechte **formát serializace události** jako **JSON**.  
   * Ponechte **pole Encoding** nastavena na **UTF-8**.  
   * Ponechte **typ komprese události** pole nastaveno **žádný**.  
   * Vyberte tlačítko **Uložit**.

5. Přejděte zpět na stránku úlohy stream analytics a vyberte **výstupy**.  

6. Vyberte **+ Přidat**. Potom vyberte **Power BI** z rozevírací nabídky.  

7. Pokud chcete vytvořit nový výstup Power BI k vizualizaci průměrnou cenu, udělejte toto:

   * V **alias pro výstup** zadejte **averagePriceOutput**.  
   * Nechte **pracovního prostoru skupiny** pole nastaveno **autorizovat připojení pro načítání pracovních prostorů**.  
   * V **název datové sady** zadejte **averagePrice**.  
   * V **název tabulky** zadejte **averagePrice**.  
   * Vyberte **Authorize** tlačítko a pak postupujte podle pokynů k autorizaci připojení k Power BI.  
   * Vyberte tlačítko **Uložit**.  

8. Pak přejděte zpátky do **streamjob1** a vyberte **upravit dotaz**.

   ![Upravit dotaz](./media/changefeed-ecommerce-solution/edit-query.png)
 
9. Vložte do okna dotazu následující dotaz. **Průměrná cena** dotaz vypočítá průměrnou cenu všechny položky, které jsou zobrazeny podle uživatelů, průměrná cena všechny položky, které jsou přidány do košíky uživatelů a průměrná cena všechny položky, které se kupují uživateli. Tato metrika může pomoct rozhodnout, jaké cen a prodávat položky v a co inventáře investovat do společnosti elektronického obchodování. Například pokud průměrná cena položky zobrazení je mnohem větší než průměrná cena zakoupených položek, pak společnost zvolit levnější položky přidejte do jeho inventáře.

   ```sql
   /*AVERAGE PRICE*/      
   SELECT System.TimeStamp AS Time, Action, AVG(Price)  
    INTO averagePriceOutput  
    FROM input  
    GROUP BY Action, TumblingWindow(second,5) 
   ```
10. Potom vyberte **Uložit** v levém horním rohu.  

11. Nyní se vraťte k **streamjob1** a vyberte **Start** tlačítko v horní části stránky. Azure Stream Analytics může trvat několik minut se spustí, ale nakonec uvidíte ho změnit na "Spuštěno" z "Výchozí".

## <a name="connect-to-power-bi"></a>Připojte se k Power BI

Power BI je sada nástrojů pro obchodní analýzy k analýze dat a sdílet přehledy. To je skvělé příklad, jak můžete strategicky vizualizovat analyzovaná data.

1. Přihlaste se k Power BI a přejděte do **pracovní prostor** otevřením nabídky na levé straně stránky.  

2. Vyberte **+ vytvořit** v pravém horním rohu a pak vyberte **řídicí panel** vytvoření řídicího panelu.  

3. Vyberte **+ přidat dlaždici** v pravém horním rohu.  

4. Vyberte **vlastní streamovaná Data**a pak **Další** tlačítko.  
 
5. Vyberte **averagePrice** z **vaše datové sady**a pak vyberte **Další**.  

6. V **typ vizualizace** zvolte **skupinový pruhový graf** z rozevírací nabídky. V části **osy**, přidání akce. Přeskočit **legendy** bez přidání cokoli. Poté, v dalším oddílu volat **hodnotu**, přidejte **avg**. Vyberte **Další**, pak nadpisu grafu a vyberte **použít**. Zobrazí se nový graf na řídicí panel!  

7. Nyní, pokud chcete zobrazit další metriky, můžete přejít zpět k **streamjob1** a vytvořit další tři výstupy u následujících polí.

   a. **Alias pro výstup:** incomingRevenueOutput, název datové sady: incomingRevenue, název tabulky: incomingRevenue  
   b. **Alias pro výstup:** top5Output, název datové sady: top5, název tabulky: top5  
   c. **Alias pro výstup:** uniqueVisitorCountOutput, název datové sady: uniqueVisitorCount, název tabulky: uniqueVisitorCount

   Potom vyberte **upravit dotaz** a vložte následující dotazy **nad** ten již byl napsán.

   ```sql
    /*TOP 5*/
    WITH Counter AS
    (
    SELECT Item, Price, Action, COUNT(*) AS countEvents
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY Item, Price, Action, TumblingWindow(second,30)
    ), 
    top5 AS
    (
    SELECT DISTINCT
    CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
    FROM Counter
    GROUP BY TumblingWindow(second,30)
    ), 
    arrayselect AS 
    (
    SELECT arrayElement.ArrayValue
    FROM top5
    CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
    ) 
    SELECT arrayvalue.value.item, arrayvalue.value.price,   arrayvalue.value.countEvents
    INTO top5Output
    FROM arrayselect

    /*REVENUE*/
    SELECT System.TimeStamp AS Time, SUM(Price)
    INTO incomingRevenueOutput
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY TumblingWindow(hour, 1)

    /*UNIQUE VISITORS*/
    SELECT System.TimeStamp AS Time, COUNT(DISTINCT CartID) as uniqueVisitors
    INTO uniqueVisitorCountOutput
    FROM input
    GROUP BY TumblingWindow(second, 5)
   ```
   
   PRVNÍCH 5 dotaz vypočítá prvních 5 položek, seřazené podle počtu případů, kdy jste zakoupili. Tato metrika pomáhá společnostem elektronického obchodování vyhodnotit, položky, které jsou nejoblíbenější a můžete ovlivnit společnosti reklamy, ceny a inventáře rozhodnutí.

   Dotaz tržby vypočítá výnosy sečtením ceny pro všechny položky zakoupené každou minutu. Tato metrika pomáhá společnostem elektronického obchodování, vyhodnotit její finanční výkonnosti a také pochopit, co denní doby, přispívají k většinu výnosů. To může mít vliv na celkové strategie společnosti, zejména uvádění na trh.

   Dotaz JEDINEČNÝCH NÁVŠTĚVNÍKŮ počítá počet jedinečných návštěvníků jsou na webu každých 5 sekund podle rozpoznání košíku jedinečné ID. Tato metrika pomáhá společnostem elektronického obchodování vyhodnotit jejich aktivity na webu a určit, jak získat více zákazníků.

8. Nyní můžete přidat dlaždice pro tyto datové sady i.

   * Pro prvních 5 to dává smysl provedete skupinový sloupcový graf s položkami osy a počet jako hodnotu.  
   * Výnosy to dává smysl provedete spojnicový graf s časem jako osu a součet cen jako hodnotu. Časové okno k zobrazení by měl být největší možné, aby bylo možné poskytovat co nejvíce informací.  
   * Pro jedinečných návštěvníků to dává smysl provedete vizualizace karet s počtem jedinečných návštěvníků jako hodnotu.

   Je to, jak vypadá ukázkový řídicí panel s tyto grafy:

   ![vizualizace](./media/changefeed-ecommerce-solution/visualizations.png)

## <a name="optional-visualize-with-an-e-commerce-site"></a>Volitelné: Vizualizace s webem elektronického obchodování

Nyní zjistíte, jak můžete váš nový nástroj pro analýzu dat pro připojení k webu skutečné elektronického obchodování. Aby bylo možné vytvářet webu elektronického obchodování, použijte databázi Azure Cosmos DB k ukládání seznam kategorií produktů (ženy, Pánské, Unisex), katalog produktů a seznamu Oblíbené položky.

1. Přejděte zpět na [webu Azure Portal](https://portal.azure.com/), pak na vaše **účtu služby Cosmos DB**, klikněte na **Průzkumník dat**.  

   Přidat dvě kolekce pod **changefeedlabdatabase** - **produkty** a **kategorie** s kapacitu dlouhodobého úložiště.

   Přidat jiné kolekce pod **changefeedlabdatabase** s názvem **topItems** a **/položky** jako klíč oddílu.

2. Vyberte **topItems** kolekce a v části **škálování a nastavení** nastavit **TTL** bude **30 sekund** tak, že topItems aktualizací každých 30 sekund.

   ![Hodnota TTL (Time to Live)](./media/changefeed-ecommerce-solution/time-to-live.png)

3. Aby bylo možné naplnit **topItems** kolekce se nejčastěji zakoupené položky, přejděte zpět na **streamjob1** a přidat nový **výstup**. Vyberte **Cosmos DB**.

4. Vyplňte požadovaná pole, jak můžete vidět níže.

   ![Výstup cosmos](./media/changefeed-ecommerce-solution/cosmos-output.png)
 
5. Pokud jste přidali nepovinný dotaz TOP 5 v předchozí části tohoto prostředí, přejděte k části 5a. V opačném případě přejděte k části 5b.

   5a. V **streamjob1**vyberte **upravit dotaz** a vložte následující dotaz Azure Stream Analytics editoru dotazů následující dotaz TOP 5, ale než zbytek dotazy.

   ```sql
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```
   5b. V **streamjob1**vyberte **upravit dotaz** a vložte následující dotaz editoru dotazu Azure Stream Analytics nad všemi další dotazy.

   ```sql
   /*TOP 5*/
   WITH Counter AS
   (
   SELECT Item, Price, Action, COUNT(*) AS countEvents
   FROM input
   WHERE Action = 'Purchased'
   GROUP BY Item, Price, Action, TumblingWindow(second,30)
   ), 
   top5 AS
   (
   SELECT DISTINCT
   CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
   FROM Counter
   GROUP BY TumblingWindow(second,30)
   ), 
   arrayselect AS 
   (
   SELECT arrayElement.ArrayValue
   FROM top5
   CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
   ) 
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```

6. Otevřít **EcommerceWebApp.sln** a přejděte do **Web.config** soubor **Průzkumníku řešení**.  

7. V rámci `<appSettings>` blokovat, přidejte **URI** a **primární klíč** , který jste předtím uložili kde říká **váš identifikátor URI** a **váš primární klíč zde**. Potom přidat do vaší **název_databáze** a **název kolekce** jak je uvedeno. (Tyto názvy musí být **changefeedlabdatabase** a **changefeedlabcollection** Pokud jste se nerozhodli pojmenujte svůj odlišně.)

   Vyplňte vaše **název kolekce produktů**, **název kolekce kategorie**, a **název kolekce položky na nejvyšší úrovni** jak je uvedeno. (Tyto názvy musí být **produkty, kategorie a topItems** Pokud jste se nerozhodli pojmenujte svůj odlišně.)  

8. Přejděte na a otevřete **rezervační složka** v rámci **EcommerceWebApp.sln.** Otevřete **Web.config** soubor v této složce.  

9. V rámci `<appSettings>` blokovat, přidejte **URI** a **primární klíč** , že jste předtím uložili na označené. Potom přidat do vaší **název_databáze** a **název kolekce** jak je uvedeno. (Tyto názvy musí být **changefeedlabdatabase** a **changefeedlabcollection** Pokud jste se nerozhodli pojmenujte svůj odlišně.)  

10. Stisknutím klávesy **Start** v horní části stránky a spusťte program.  

11. Nyní můžete pohrát na webu elektronického obchodování. Při zobrazení položky, přidání položky do košíku, změnit množství zboží v košíku nebo zakoupit položky, tyto události se předá prostřednictvím Cosmos DB do centra událostí, Azure Stream Analytics a Power BI pak kanálu změn. Doporučujeme pokračování v používání DataGenerator generovat významné webových přenosů dat a poskytování realistické sady produktů"Hot" na webu elektronického obchodování.

## <a name="delete-the-resources"></a>Odstranit prostředky

Pokud chcete odstranit prostředky, které jste vytvořili v tomto prostředí, přejděte do skupiny prostředků na [webu Azure Portal](https://portal.azure.com/)a pak vyberte **odstranit skupinu prostředků** v nabídce v horní části stránky a postupujte podle pokynů k dispozici.

## <a name="next-steps"></a>Další postup 
  
* Další informace o kanálu změn najdete v tématu [podpora práce s Změna kanálu ve službě Azure Cosmos DB](change-feed.md) 
* [Změna kanálu oznámení řešení](change-feed-hl7-fhir-logic-apps.md) zdravotnické organizace pomocí služby Azure Cosmos DB.
