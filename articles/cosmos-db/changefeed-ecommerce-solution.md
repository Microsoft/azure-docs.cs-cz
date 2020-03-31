---
title: Použití informačního kanálu o změnách Azure Cosmos DB k vizualizaci analýzy dat v reálném čase
description: Tento článek popisuje, jak může maloobchodní společnost použít informační kanál o změnách k pochopení uživatelských vzorců, k provádění analýzy a vizualizace dat v reálném čase.
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: sngun
ms.openlocfilehash: c0c1a28dc399d3f176f92e656621fec1bc92dbfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513488"
---
# <a name="use-azure-cosmos-db-change-feed-to-visualize-real-time-data-analytics"></a>Použití informačního kanálu o změnách Azure Cosmos DB k vizualizaci analýzy dat v reálném čase

Kanál změn Azure Cosmos DB je mechanismus pro získání nepřetržitého a přírůstkového přenosu záznamů z kontejneru Azure Cosmos, protože tyto záznamy se vytvářejí nebo upravují. Podpora kanálu změna funguje tak, že naslouchá kontejneru pro všechny změny. Výstupem je pak seznam změněných dokumentů v pořadí podle času úprav. Další informace o kanálu změn najdete v [článku práce s kanálem o změnách.](change-feed.md) 

Tento článek popisuje, jak může společnost elektronického obchodu používat informační kanál o změně k pochopení uživatelských vzorců, provádění analýzy a vizualizace dat v reálném čase. Budete analyzovat události, jako je například zobrazení položky uživatelem, přidání položky do košíku nebo zakoupení položky. Dojde-li k jedné z těchto událostí, je vytvořen nový záznam a protokoly kanálu změn tento záznam. Kanál změn pak aktivuje řadu kroků, které vedou k vizualizaci metrik, které analyzují výkon a aktivitu společnosti. Mezi metriky, které můžete vizualizovat, patří tržby, jedineční návštěvníci webu, nejoblíbenější položky a průměrná cena položek, které jsou zobrazeny a přidány do košíku oproti zakoupeným. Tyto ukázkové metriky mohou pomoci e-commerce společnosti vyhodnotit její popularitu webu, rozvíjet své reklamní a cenové strategie a rozhodovat o tom, do jakého inventáře investovat.

Máte zájem o sledování videa o řešení před zahájením, podívejte se na následující video:

> [!VIDEO https://www.youtube.com/embed/AYOiMkvxlzo]
>

## <a name="solution-components"></a>Součásti řešení
Následující diagram představuje tok dat a součásti, které se účastní řešení:

![Vizuální nastavení projektu](./media/changefeed-ecommerce-solution/project-visual.png)
 
1. **Generování dat:** Simulátor dat se používá ke generování maloobchodních dat, která představují události, jako je například uživatel zobrazení položky, přidání položky do košíku a nákup položky. Pomocí generátoru dat můžete generovat velkou sadu ukázkových dat. Vygenerovaná ukázková data obsahují dokumenty v následujícím formátu:
   
   ```json
   {      
     "CartID": 2486,
     "Action": "Viewed",
     "Item": "Women's Denim Jacket",
     "Price": 31.99
   }
   ```

2. **Cosmos DB:** Vygenerovaná data se ukládají v kontejneru Azure Cosmos.  

3. **Změnit informační kanál:** Kanál změn bude naslouchat změnám kontejneru Azure Cosmos. Pokaždé, když je do kolekce přidán nový dokument (to znamená, že dojde k události, takový uživatel si prohlíží položku, přidá položku do košíku nebo zakoupí položku), kanál změn spustí [funkci Azure](../azure-functions/functions-overview.md).  

4. **Funkce Azure:** Funkce Azure zpracuje nová data a odešle je do [Centra událostí Azure](../event-hubs/event-hubs-about.md).  

5. **Centrum událostí:** Azure Event Hub ukládá tyto události a odesílá je do [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) k provedení další analýzy.  

6. **Azure Stream Analytics:** Azure Stream Analytics definuje dotazy pro zpracování událostí a provádění analýzy dat v reálném čase. Tato data se pak odešlou do [Microsoft Power BI](https://docs.microsoft.com/power-bi/desktop-what-is-desktop).  

7. **Power BI:** Power BI se používá k vizualizaci dat odeslaných službou Azure Stream Analytics. Řídicí panel můžete vytvořit a zjistit, jak se metriky mění v reálném čase.  

## <a name="prerequisites"></a>Požadavky

* Rozhraní Microsoft .NET Framework 4.7.1 nebo vyšší

* Microsoft .NET Core 2.1 (nebo vyšší)

* Visual Studio s vývojem univerzální platformy Windows, vývoj desktopů .NET a úlohy ASP.NET a vývoj webových aplikací

* Předplatné Microsoft Azure

* Účet Microsoft Power BI

* Stáhněte si [testovací kanál změn Azure Cosmos DB](https://github.com/Azure-Samples/azure-cosmos-db-change-feed-dotnet-retail-sample) z GitHubu. 

## <a name="create-azure-resources"></a>Vytvoření prostředků Azure 

Vytvořte prostředky Azure – Azure Cosmos DB, účet úložiště, Centrum událostí, Stream Analytics vyžadované řešením. Tyto prostředky nasadíte prostřednictvím šablony Azure Resource Manager. K nasazení těchto prostředků použijte následující kroky: 

1. Nastavte zásadu spuštění prostředí Windows PowerShell na **neomezenou**. Chcete-li tak učinit, otevřete **prostředí Windows PowerShell jako správce** a spusťte následující příkazy:

   ```powershell
   Get-ExecutionPolicy
   Set-ExecutionPolicy Unrestricted 
   ```

2. Z úložiště GitHub, které jste stáhli v předchozím kroku, přejděte do složky **Azure Resource Manager** a otevřete soubor s názvem **parameters.json.**  

3. Zadejte hodnoty pro cosmosdbaccount_name, eventhubnamespace_name, storageaccount_name, parametry, jak je uvedeno v **souboru parameters.json.** Později budete muset použít názvy, které přilovíte každému ze svých prostředků.  

4. Z **prostředí Windows PowerShell**přejděte do složky **Správce prostředků Azure** a spusťte následující příkaz:

   ```powershell
   .\deploy.ps1
   ```
5. Po zobrazení výzvy zadejte **ID předplatného**Azure , **changefeedlab** pro název skupiny prostředků a **spusťte1** pro název nasazení. Jakmile se prostředky začnou nasazovat, může trvat až 10 minut, než se dokončí.

## <a name="create-a-database-and-the-collection"></a>Vytvoření databáze a kolekce

Nyní vytvoříte kolekci pro pořádání událostí webu elektronického obchodování. Když uživatel zobrazí položku, přidá položku do košíku nebo zakoupí položku, kolekce obdrží záznam, který obsahuje akci ("zobrazeno", "přidáno" nebo "zakoupeno"), název položky, cenu položky a číslo id uživatelského košíku Zapojeny.

1. Přejděte na [portál Azure](https://portal.azure.com/) a najděte účet **Azure Cosmos DB,** který je vytvořený nasazením šablony.  

2. V podokně **Průzkumník dat** vyberte **Nová kolekce** a vyplňte formulář následujícími podrobnostmi:  

   * Pro pole **Id databáze** vyberte **vytvořit nový**, a zadejte **changefeedlabdatabase**. Ponechejte políčko **Propustnost databáze zřízení** nezaškrtnuté.  
   * Do pole **Id kolekce** zadejte **changefeedlabcollection**.  
   * Do pole **Klíče oddílu** zadejte **/Item**. To to je malá a velká písmena, takže se ujistěte, že jste jej zadali správně.  
   * Do pole **Propustnost** zadejte **hodnotu 10000**.  
   * Vyberte tlačítko **OK**.  

3. Dále vytvořte jinou kolekci s názvem **zapůjčení** pro zpracování zdroje změn. Kolekce zapůjčení koordinuje zpracování kanálu změn napříč více pracovníky. Samostatná kolekce se používá k ukládání zapůjčení s jedním zapůjčení na oddíl.  

4. Vraťte se do podokna **Průzkumník dat** a vyberte **Nová kolekce** a vyplňte formulář následujícími podrobnostmi:

   * Pro pole **ID databáze** vyberte **Použít existující**a zadejte **changefeedlabdatabase**.  
   * Do pole **Id kolekce** zadejte **nájemní smlouvy**.  
   * V **případě kapacity úložiště**vyberte možnost **Pevná**.  
   * Pole **Propustnost** ponechejte nastaveno na výchozí hodnotu.  
   * Vyberte tlačítko **OK**.

## <a name="get-the-connection-string-and-keys"></a>Získání připojovacího řetězce a klíčů

### <a name="get-the-azure-cosmos-db-connection-string"></a>Získání připojovacího řetězce Azure Cosmos DB

1. Přejděte na [portál Azure](https://portal.azure.com/) a najděte účet **Azure Cosmos DB,** který je vytvořený nasazením šablony.  

2. Přejděte do podokna **Klíče,** zkopírujte primární připojovací řetězec a zkopírujte jej do poznámkového bloku nebo jiného dokumentu, ke kterému budete mít přístup v celém testovacím prostředí. Měli byste jej označit jako **připojovací řetězec Cosmos DB**. Řetězec budete muset později zkopírovat do kódu, takže si poznamenejte a zapamatujte si, kde jej ukládáte.

### <a name="get-the-storage-account-key-and-connection-string"></a>Získání klíče účtu úložiště a připojovacího řetězce

Účty úložiště Azure umožňují uživatelům ukládat data. V tomto testovacím prostředí použijete účet úložiště k ukládání dat, která používá funkce Azure. Funkce Azure se aktivuje při jakékoli změny v kolekci.

1. Vraťte se do skupiny prostředků a otevřete účet úložiště, který jste vytvořili dříve  

2. Z nabídky na levé straně vyberte **Přístupové klávesy.**  

3. Zkopírujte hodnoty pod **klávesou 1** do poznámkového bloku nebo jiného dokumentu, ke kterému budete mít přístup v celém testovacím prostředí. **Klíč** byste měli označit jako **klíč úložiště** a **připojovací řetězec** jako **připojovací řetězec úložiště**. Tyto řetězce budete muset zkopírovat do kódu později, takže si poznamenejte a zapamatujte si, kde je ukládáte.  

### <a name="get-the-event-hub-namespace-connection-string"></a>Získání připojovacího řetězce centra událostí

Centrum událostí Azure přijímá data událostí, ukládá, zpracovává a předává data. V tomto testovacím prostředí azure event hub obdrží dokument pokaždé, když dojde k nové události (tj. položka je zobrazena uživatelem, přidána do uživatelského košíku nebo zakoupena uživatelem) a pak tento dokument předá Azure Stream Analytics.

1. Vraťte se do skupiny prostředků a otevřete **obor názvů centra událostí,** který jste vytvořili a pojmenovali v předběžném prostředí.  

2. V nabídce na levé straně vyberte **Zásady sdíleného přístupu.**  

3. Vyberte **rootmanagesharedaccesskey**. Zkopírujte **primární klíč připojovacířetězec** do poznámkového bloku nebo do jiného dokumentu, ke kterému budete mít přístup v celém testovacím prostředí. Měli byste jej označit připojovacím řetězce centra názvů Event **Hub.** Řetězec budete muset později zkopírovat do kódu, takže si poznamenejte a zapamatujte si, kde jej ukládáte.

## <a name="set-up-azure-function-to-read-the-change-feed"></a>Nastavení funkce Azure pro čtení kanálu změn

Když je vytvořen nový dokument nebo aktuální dokument je změněn v kontejneru Cosmos, zdroj změn automaticky přidá, že upravený dokument do své historie změn kolekce. Teď vytvoříte a spustíte funkci Azure, která zpracovává kanál změn. Když je dokument vytvořen nebo upraven v kolekci, kterou jste vytvořili, funkce Azure se aktivuje zdrojezměn. Pak funkce Azure odešle upravený dokument do centra událostí.

1. Vraťte se do úložiště, které jste naklonovali v zařízení.  

2. Klepněte pravým tlačítkem myši na soubor s názvem **ChangeFeedLabSolution.sln** a vyberte **otevřít pomocí sady Visual Studio**.  

3. Přejděte v sadě Visual Studio na soubor **local.settings.json.** Pak použijte hodnoty, které jste zaznamenali dříve vyplnit prázdná místa.  

4. Přejděte na **ChangeFeedProcessor.cs**. V parametrech funkce **Spustit** proveďte následující akce:  

   * Zde nahraďte text **Vaše jmenovka sbírky** názvem vaší sbírky. Pokud jste postupovali podle předchozích pokynů, název kolekce je changefeedlabcollection.  
   * Nahraďte text **VAŠE LEASES COLLECTION NAME ZDE** názvem vaší kolekce leasingů. Pokud jste postupovali podle předchozích pokynů, název kolekce zapůjčení je **zapůjčení**.  
   * V horní části sady Visual Studio se ujistěte, že pole Startup Project na levé straně zelené šipky říká **ChangeFeedFunction**.  
   * Chcete-li spustit program, vyberte **možnost Spustit** v horní části stránky.  
   * Můžete potvrdit, že funkce je spuštěna, když aplikace konzoly říká "Job host started".

## <a name="insert-data-into-azure-cosmos-db"></a>Vložení dat do služby Azure Cosmos DB 

Chcete-li zjistit, jak zdroj změn zpracovává nové akce na webu elektronického obchodování, je třeba simulovat data, která představují uživatele, kteří si prohlíží položky z katalogu produktů, přidávají tyto položky do košíků a nakupují položky v košíku. Tato data jsou libovolná a za účelem replikace, jak by data na webu elektronického obchodu vypadala.

1. Přejděte zpět do úložiště v Průzkumníkovi souborů a kliknutím pravým tlačítkem myši na **ChangeFeedFunction.sln** jej znovu otevřete v novém okně sady Visual Studio.  

2. Přejděte do souboru **App.config.** V `<appSettings>` rámci bloku přidejte koncový bod a jedinečný **primární klíč,** který jste načetli dříve.  

3. Přidejte do **kolekce** a názvy **databází.** (Tyto názvy by měly být **changefeedlabcollection** a **changefeedlabdatabase,** pokud se rozhodnete pojmenovat vaše jinak.)

   ![Aktualizace připojovacích řetězců](./media/changefeed-ecommerce-solution/update-connection-string.png)
 
4. Uložte změny na všechny upravené soubory.  

5. V horní části sady Visual Studio, ujistěte se, že **pole Projekt po spuštění** na levé straně zelené šipky říká **DataGenerator**. Pak vyberte **Spustit** v horní části stránky a spusťte program.  
 
6. Počkejte, až bude program spuštěn. Hvězdy znamenají, že data přicházejí! Udržujte program spuštěný - je důležité, aby bylo shromážděno velké množství dat.  

7. Pokud přejdete na [portál Azure](https://portal.azure.com/) , pak na účet Cosmos DB v rámci skupiny prostředků, pak do **Průzkumníka dat**, uvidíte nahodilá data importovaná ve vaší **kolekci changefeedlab .**
 
   ![Data generovaná na portálu](./media/changefeed-ecommerce-solution/data-generated-in-portal.png)

## <a name="set-up-a-stream-analytics-job"></a>Nastavení úlohy analýzy datového proudu

Azure Stream Analytics je plně spravovaná cloudová služba pro zpracování streamovaných dat v reálném čase. V tomto testovacím prostředí použijete analýzu datového proudu ke zpracování nových událostí z Centra událostí (tj. když se položka zobrazí, přidá do košíku nebo zakoupí), začleníte tyto události do analýzy dat v reálném čase a odešlete je do Power BI pro vizualizaci.

1. Z [portálu Azure](https://portal.azure.com/)přejděte do skupiny prostředků a pak **do streamjobu1** (úloha analýzy datového proudu, kterou jste vytvořili v předběžném čísle).  

2. Vyberte **Vstupy,** jak je znázorněno níže.  

   ![Vytvořit vstup](./media/changefeed-ecommerce-solution/create-input.png)

3. Vyberte **+ Přidat vstup datového proudu**. Pak v rozevírací nabídce vyberte **Centrum událostí.**  

4. Vyplňte nový vstupní formulář následujícími údaji:

   * Do pole **Vstupní** alias zadejte **vstup**.  
   * Vyberte možnost **Vybrat centrum událostí z vašich předplatných**.  
   * Nastavte pole **Předplatné** na předplatné.  
   * Do pole **obor názvů Centra událostí** zadejte název oboru názvů centra událostí, který jste vytvořili během předběžného období.  
   * V poli **Název centra událostí** vyberte možnost Použít **existující** a z rozbalovací nabídky zvolte **centrum událostí1.**  
   * Ponechejte pole názvu **zásad centra událostí** nastavené na výchozí hodnotu.  
   * Ponechat **formát serializace události** jako **JSON**.  
   * Ponechat **pole Kódování** nastaveno na **UTF-8**.  
   * Ponechat pole **typu komprese události** nastaveno na **žádnou**.  
   * Vyberte tlačítko **Uložit**.

5. Přejděte zpět na stránku úlohy analýzy datového proudu a vyberte **Výstupy**.  

6. Vyberte **+ Přidat**. Pak v rozevírací nabídce vyberte **Power BI.**  

7. Pokud chcete vytvořit nový výstup Power BI pro vizualizaci průměrné ceny, proveďte následující akce:

   * Do pole **Výstupní alias** zadejte **averagePriceOutput**.  
   * Ponechte pole **pracovního prostoru skupiny** nastavené na **Autorizovat připojení k načtení pracovních prostorů**.  
   * Do pole **Název datové sady** zadejte **hodnotu averagePrice**.  
   * Do pole **Název tabulky** zadejte **hodnotu averagePrice**.  
   * Vyberte tlačítko **Autorizovat** a podle pokynů autorizujte připojení k Power BI.  
   * Vyberte tlačítko **Uložit**.  

8. Potom se vraťte k **funkci streamjob1** a vyberte **upravit dotaz**.

   ![Upravit dotaz](./media/changefeed-ecommerce-solution/edit-query.png)
 
9. Vložte následující dotaz do okna dotazu. Dotaz **PRŮMĚRNÁ CENA** vypočítá průměrnou cenu všech položek, které jsou zobrazeny uživateli, průměrnou cenu všech položek, které jsou přidány do košíků uživatelů, a průměrnou cenu všech položek zakoupených uživateli. Tato metrika může společnostem elektronického obchodu pomoci rozhodnout se, za jaké ceny mají položky prodávat a do jakého inventáře investovat. Pokud je například průměrná cena zobrazených položek mnohem vyšší než průměrná cena zakoupených položek, může se společnost rozhodnout přidat do svého inventáře levnější položky.

   ```sql
   /*AVERAGE PRICE*/      
   SELECT System.TimeStamp AS Time, Action, AVG(Price)  
    INTO averagePriceOutput  
    FROM input  
    GROUP BY Action, TumblingWindow(second,5) 
   ```
10. Pak v levém horním rohu vyberte **Uložit.**  

11. Nyní se vraťte do **streamjob1** a vyberte tlačítko **Start** v horní části stránky. Azure Stream Analytics může trvat několik minut ke spuštění, ale nakonec uvidíte, že se změní z "Spuštění" na "Spuštěno".

## <a name="connect-to-power-bi"></a>Připojení k Power BI

Power BI je sada nástrojů pro obchodní analýzu, která umožňuje analyzovat data a sdílet poznatky. Je to skvělý příklad toho, jak můžete strategicky vizualizovat analyzovaná data.

1. Přihlaste se k Power BI a přejděte do **sloužiny pracovního prostoru** otevřením nabídky na levé straně stránky.  

2. Vyberte **+ Vytvořit** v pravém horním rohu a pak vyberte **Řídicí panel** a vytvořte řídicí panel.  

3. Vyberte **+ Přidat dlaždici** v pravém horním rohu.  

4. Vyberte **Vlastní streamovaná data**a pak vyberte tlačítko **Další.**  
 
5. Vyberte **průměrnou cenu** ze **svých datových sad a**pak vyberte **Další**.  

6. V poli **Typ vizualizace** zvolte Z rozevírací nabídky **pruhový graf clusterovaného** grafu. V části **Axis**přidejte akci. Přeskočit **Legend** bez přidání nic. Potom v další části s názvem **Hodnota**přidejte **avg**. Vyberte **Další**, pak zadejte název grafu a vyberte **Použít**. Měli byste vidět nový graf na palubní desce!  

7. Nyní, pokud chcete vizualizovat více metrik, můžete se vrátit k **streamjob1** a vytvořit další tři výstupy s následujícími poli.

   a. **Výstupní alias:** incomingRevenueOutput, Název datové sady: incomingRevenue, Název tabulky: incomingRevenue  
   b. **Výstupní alias:** top5Output, Název datové sady: top5, Název tabulky: top5  
   c. **Výstupní alias:** uniqueVisitorCountOutput, Název datové sady: uniqueVisitorCount, Název tabulky: uniqueVisitorCount

   Pak vyberte **Upravit dotaz** a vložte následující dotazy **nad** ten, který jste již napsali.

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
   
   Dotaz TOP 5 vypočítá prvních 5 položek seřazených podle počtu zakoupených položek. Tato metrika může společnostem elektronického obchodování pomoci vyhodnotit, které položky jsou nejoblíbenější, a může ovlivnit rozhodování společnosti o reklamě, cenách a skladových zásobách.

   Dotaz VÝNOSY vypočítá výnosy sečtením cen všech položek zakoupených každou minutu. Tato metrika může společnostem elektronického obchodu pomoci vyhodnotit její finanční výkonnost a také pochopit, jaké denní doby přispívají k většině příjmů. To může mít vliv na celkovou strategii společnosti, zejména na marketing.

   Unikátní návštěvníci dotaz vypočítá, kolik unikátních návštěvníků jsou na místě každých 5 sekund tím, že detekuje unikátní cart ID. Tato metrika může společnostem elektronického obchodu pomoci vyhodnotit jejich aktivitu na webu a strategii, jak získat více zákazníků.

8. Nyní můžete přidat dlaždice pro tyto datové sady také.

   * Pro Top 5 by mělo smysl provést skupinový sloupcový graf s položkami jako osou a počítat jako hodnotu.  
   * Pro výnosy by mělo smysl udělat spojnicový graf s časem jako osou a součtem cen jako hodnotou. Časové okno, které se má zobrazit, by mělo být co největší, aby bylo možné poskytnout co nejvíce informací.  
   * Pro unikátní návštěvníky by mělo smysl udělat vizualizaci karty s počtem jedinečných návštěvníků jako hodnotou.

   Takto vypadá ukázkový řídicí panel s těmito grafy:

   ![vizualizace](./media/changefeed-ecommerce-solution/visualizations.png)

## <a name="optional-visualize-with-an-e-commerce-site"></a>Volitelné: Vizualizace pomocí webu elektronického obchodování

Nyní budete sledovat, jak můžete použít nový nástroj pro analýzu dat pro připojení se skutečným e-commerce stránky. Chcete-li vytvořit web elektronického obchodování, použijte databázi Azure Cosmos k uložení seznamu kategorií produktů (dámské, pánské, unisex), katalogu produktů a seznam nejoblíbenějších položek.

1. Přejděte zpět na [portál Azure](https://portal.azure.com/)a potom na **účet Cosmos DB**a potom na Průzkumník a **dat**.  

   Přidejte dvě kolekce pod **changefeedlabdatabase** - **produkty** a **kategorie** s pevnou úložnou kapacitou.

   Přidejte další kolekci pod **changefeedlabdatabase** s názvem **topItems** a **/Item** jako klíč oddílu.

2. Vyberte **kolekci topItems** a v části **Měřítko a Nastavení** nastavte čas na život **na** **30 sekund,** aby se topItems každých 30 sekund aktualizují.

   ![Hodnota TTL (Time to Live)](./media/changefeed-ecommerce-solution/time-to-live.png)

3. Chcete-li naplnit **kolekci topItems** nejčastěji zakoupenými položkami, přejděte zpět na **streamjob1** a přidejte nový **výstup**. Vyberte **možnost Cosmos DB**.

4. Vyplňte požadovaná pole, jak je znázorněno níže.

   ![Výstup Cosmos](./media/changefeed-ecommerce-solution/cosmos-output.png)
 
5. Pokud jste přidali volitelný dotaz TOP 5 v předchozí části testovacího prostředí, přejděte do části 5a. Pokud ne, pokračujte do části 5b.

   5a. V **streamjob1**vyberte **Upravit dotaz** a vložte následující dotaz do editoru dotazů Azure Stream Analytics pod dotaz TOP 5, ale nad zbytek dotazů.

   ```sql
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```
   5b. V **streamjob1**vyberte **Upravit dotaz** a vložte následující dotaz do editoru dotazů Azure Stream Analytics nad všechny ostatní dotazy.

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

6. Otevřete **soubor EcommerceWebApp.sln** a přejděte do souboru **Web.config** v **Průzkumníku řešení**.  

7. V `<appSettings>` rámci bloku přidejte **identifikátor URI** a **primární klíč,** který jste uložili dříve, kde se **uvádí,** že váš identifikátor URI a **primární klíč zde**. Pak přidejte **název databáze** a název **kolekce,** jak je uvedeno. (Tyto názvy by měly být **changefeedlabdatabase** a **changefeedlabcollection,** pokud jste se rozhodli pojmenovat vaše jinak.)

   Vyplňte **název kolekce produktů**, **název kolekce kategorií**a název kolekce **nejlepších položek,** jak je uvedeno. (Tyto názvy by měly být **produkty, kategorie a topItems,** pokud jste se rozhodli pojmenovat vaše jinak.)  

8. Přejděte do **složky Pokladna** v **souboru EcommerceWebApp.sln** a otevřete ji. Potom otevřete soubor **Web.config** v této složce.  

9. V `<appSettings>` rámci bloku přidejte **identifikátor URI** a **primární klíč,** který jste uložili dříve, kde je uvedeno. Pak přidejte **název databáze** a název **kolekce,** jak je uvedeno. (Tyto názvy by měly být **changefeedlabdatabase** a **changefeedlabcollection,** pokud jste se rozhodli pojmenovat vaše jinak.)  

10. Stisknutím **klávesy Start** v horní části stránky spusťte program.  

11. Nyní si můžete hrát na e-commerce stránky. Když si zobrazíte položku, přidáte položku do košíku, změníte množství položky v košíku nebo zakoupíte položku, tyto události budou předány prostřednictvím informačního kanálu o změně Cosmos DB do Centra událostí, ASA a pak Power BI. Doporučujeme pokračovat ve spuštění DataGenerator generovat významné údaje o webovém provozu a poskytnout realistickou sadu "Hot Products" na e-commerce webu.

## <a name="delete-the-resources"></a>Odstranění prostředků

Pokud chcete odstranit prostředky, které jste vytvořili během tohoto testovacího prostředí, přejděte do skupiny prostředků na [webu Azure Portal](https://portal.azure.com/), vyberte odstranit **skupinu prostředků** z nabídky v horní části stránky a postupujte podle uvedených pokynů.

## <a name="next-steps"></a>Další kroky 
  
* Další informace o kanálu změn najdete v [tématu práce s podporou zdrojů změn v Azure Cosmos DB](change-feed.md) 
