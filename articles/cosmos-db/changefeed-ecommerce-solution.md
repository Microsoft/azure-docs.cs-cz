---
title: Použití Azure Cosmos DB změnového kanálu k vizualizaci analýzy dat v reálném čase
description: Tento článek popisuje, jak může maloobchodní společnost využít změnu kanálu pro pochopení uživatelských vzorů, provádění analýzy a vizualizace dat v reálném čase.
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: how-to
ms.date: 05/28/2019
ms.author: sngun
ms.custom: devx-track-java
ms.openlocfilehash: b1de0fa2e6601e4350b52caea32f8bc379909f85
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91356362"
---
# <a name="use-azure-cosmos-db-change-feed-to-visualize-real-time-data-analytics"></a>Použití Azure Cosmos DB změnového kanálu k vizualizaci analýzy dat v reálném čase

Kanál změny Azure Cosmos DB je mechanismus, který získá průběžné a přírůstkové kanály záznamů z kontejneru Azure Cosmos při vytváření nebo úpravách těchto záznamů. Změna kanálu podpory funguje tak, že se poslouchat jakýmkoli změnám v kontejneru. Výstupem je pak seznam změněných dokumentů v pořadí podle času úprav. Další informace o službě Change feed najdete v článku [práce s informačním kanálem změn](change-feed.md) . 

Tento článek popisuje, jak je možné pomocí e-Commerce společnosti používat kanál ke změně uživatelských vzorů a jak provádět analýzu a vizualizaci dat v reálném čase. Budete analyzovat události, jako je uživatel, který si zobrazuje položku, přidá položku do svého košíku nebo položku si můžete koupit. Když dojde k jedné z těchto událostí, vytvoří se nový záznam a zaznamená se záznam o změně kanálu. Změna informačního kanálu potom spustí řadu kroků, které mají za následek vizualizaci metrik, které analyzují výkon a aktivitu společnosti. Ukázkové metriky, které je možné vizualizovat, zahrnují tržby, jedinečné návštěvníky webu, Nejoblíbenější položky a průměrnou cenu položek, které jsou zobrazené a přidané na košík a zakoupené. Tyto ukázkové metriky pomáhají společnosti elektronického obchodování vyhodnocovat oblíbenou lokalitu svého webu, vyvíjet své reklamy a cenové strategie a rozhodovat o tom, do jakého inventáře investovat.

Zajímá Vás video o řešení před zahájením práce, podívejte se na toto video:

> [!VIDEO https://www.youtube.com/embed/AYOiMkvxlzo]
>

## <a name="solution-components"></a>Součásti řešení
Následující diagram představuje tok dat a součásti zapojené do řešení:

:::image type="content" source="./media/changefeed-ecommerce-solution/project-visual.png" alt-text="Vizuál projektu" border="false":::
 
1. **Generování dat:** Simulátor dat se používá ke generování maloobchodních dat, která představují události, jako je například uživatel, který zobrazuje položku, přidání položky na košík a nákup položky. Pomocí generátoru dat můžete vygenerovat rozsáhlou sadu ukázkových dat. Vygenerovaná ukázková data obsahují dokumenty v následujícím formátu:
   
   ```json
   {      
     "CartID": 2486,
     "Action": "Viewed",
     "Item": "Women's Denim Jacket",
     "Price": 31.99
   }
   ```

2. **Cosmos DB:** Vygenerovaná data se ukládají do kontejneru Azure Cosmos.  

3. **Změnit kanál:** Kanál změn bude naslouchat změnám v kontejneru Azure Cosmos. Pokaždé, když se do kolekce přidá nový dokument (to znamená, že když k události dojde, když uživatel zobrazuje položku, přidá položku na košík nebo si položku koupí), kanál změn aktivuje [funkci Azure Functions](../azure-functions/functions-overview.md).  

4. **Funkce Azure:** Funkce Azure zpracuje nová data a pošle je do [centra událostí Azure](../event-hubs/event-hubs-about.md).  

5. **Centrum událostí:** Centrum událostí Azure tyto události uloží a pošle je [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) k provedení další analýzy.  

6. **Azure Stream Analytics:** Azure Stream Analytics definuje dotazy, které zpracovávají události a provádějí analýzu dat v reálném čase. Tato data se pak odesílají do [Microsoft Power BI](https://docs.microsoft.com/power-bi/desktop-what-is-desktop).  

7. **Power BI:** Power BI slouží k vizualizaci dat odesílaných Azure Stream Analytics. Můžete vytvořit řídicí panel, abyste viděli, jak se metriky mění v reálném čase.  

## <a name="prerequisites"></a>Požadavky

* Microsoft .NET Framework 4.7.1 nebo novější

* Microsoft .NET Core 2,1 (nebo vyšší)

* Visual Studio s vývojem Univerzální platforma Windows, vývoj desktopových aplikací pro .NET a ASP.NET a webové vývojové úlohy

* Předplatné Microsoft Azure

* Účet Microsoft Power BI

* Stáhněte si [prostředí Azure Cosmos DB Change feed Labs](https://github.com/Azure-Samples/azure-cosmos-db-change-feed-dotnet-retail-sample) z GitHubu. 

## <a name="create-azure-resources"></a>Vytvoření prostředků Azure 

Vytvořte prostředky Azure – Azure Cosmos DB, účet úložiště, centrum událostí Stream Analytics vyžadované řešením. Tyto prostředky budete nasazovat prostřednictvím šablony Azure Resource Manager. K nasazení těchto prostředků použijte následující postup: 

1. Nastavte zásady spouštění prostředí Windows PowerShell na **neomezeno**. Provedete to tak, **že otevřete Windows PowerShell jako správce** a spustíte následující příkazy:

   ```powershell
   Get-ExecutionPolicy
   Set-ExecutionPolicy Unrestricted 
   ```

2. Z úložiště GitHub, které jste stáhli v předchozím kroku, přejděte do složky **Azure Resource Manager** a otevřete soubor s názvem **parameters.jsv** souboru.  

3. Zadejte hodnoty pro cosmosdbaccount_name, eventhubnamespace_name, storageaccount_name parametrů, které jsou uvedeny v **parameters.js** souboru. Později budete muset použít názvy, které pro každý z vašich prostředků udělíte.  

4. Z **Windows PowerShellu**přejděte do složky **Azure Resource Manager** a spusťte následující příkaz:

   ```powershell
   .\deploy.ps1
   ```
5. Po zobrazení výzvy zadejte **ID předplatného**Azure, **changefeedlab** pro název skupiny prostředků a **Run1** pro název nasazení. Až se prostředky začnou nasazovat, může trvat až 10 minut, než se dokončí.

## <a name="create-a-database-and-the-collection"></a>Vytvoření databáze a kolekce

Nyní vytvoříte kolekci, která bude uchovávat události webu elektronického obchodování. Když uživatel zobrazí položku, přidá položku do svého košíku nebo zakoupí položku, kolekce obdrží záznam, který obsahuje akci ("zobrazené", "přidané" nebo "koupené"), název příslušné položky, cenu příslušné položky a identifikační číslo uživatele, který je součástí.

1. Přejít na [Azure Portal](https://portal.azure.com/) a najít **účet Azure Cosmos DB** , který byl vytvořen nasazením šablony.  

2. V podokně **Průzkumník dat** vyberte možnost **Nová kolekce** a vyplňte formulář následujícími podrobnostmi:  

   * V poli **ID databáze** vyberte **vytvořit novou**a potom zadejte **changefeedlabdatabase**. Nechejte políčko **zřídit propustnost databáze** nezaškrtnuté.  
   * Do pole ID **kolekce** zadejte **changefeedlabcollection**.  
   * Do pole **klíč oddílu** zadejte **/Item**. V takovém případě se rozlišují velká a malá písmena, proto je nezapomeňte zadat správně.  
   * Do pole **propustnost** zadejte **10000**.  
   * Vyberte tlačítko **OK**.  

3. Dále vytvořte další kolekci s názvem **zapůjčení** pro zpracování kanálu změn. Kolekce zapůjčení koordinuje zpracování kanálu změn napříč několika procesy. Samostatná kolekce se používá k uložení zapůjčení s jednou zapůjčením na oddíl.  

4. Vraťte se do podokna **Průzkumník dat** a vyberte **Nová kolekce** a vyplňte formulář následujícími podrobnostmi:

   * V poli **ID databáze** vyberte **použít existující**a pak zadejte **changefeedlabdatabase**.  
   * V poli **ID kolekce** zadejte **zapůjčení**.  
   * Jako **kapacitu úložiště**vyberte **pevná**.  
   * V poli **propustnost** nechejte nastavenou výchozí hodnotu.  
   * Vyberte tlačítko **OK**.

## <a name="get-the-connection-string-and-keys"></a>Získání připojovacího řetězce a klíčů

### <a name="get-the-azure-cosmos-db-connection-string"></a>Získání připojovacího řetězce Azure Cosmos DB

1. Přejít na [Azure Portal](https://portal.azure.com/) a najít **účet Azure Cosmos DB** , který byl vytvořen nasazením šablony.  

2. Přejděte do podokna **klíče** , ZKOPÍRUJTE primární připojovací řetězec a zkopírujte ho do poznámkového bloku nebo jiného dokumentu, ke kterému budete mít přístup v průběhu tohoto testovacího prostředí. Měli byste označit **Cosmos DB připojovací řetězec**pro IT. Později budete muset řetězec zkopírovat do kódu, takže si poznamenejte, kam ho ukládáte.

### <a name="get-the-storage-account-key-and-connection-string"></a>Získání klíče účtu úložiště a připojovacího řetězce

Účty Azure Storage umožňují uživatelům ukládat data. V tomto testovacím prostředí použijete účet úložiště k ukládání dat, která používá funkce Azure Functions. Funkce Azure se aktivuje, když se v kolekci provede nějaká změna.

1. Vraťte se do skupiny prostředků a otevřete účet úložiště, který jste vytvořili dříve.  

2. V nabídce na levé straně vyberte **přístupové klíče** .  

3. Zkopírujte hodnoty v části **klíč 1** do poznámkového bloku nebo jiného dokumentu, ke kterým budete mít přístup v průběhu tohoto testovacího prostředí. **Klíč** byste měli označit jako **klíč úložiště** a **připojovací řetězec** jako **připojovací řetězec úložiště**. Tyto řetězce budete muset zkopírovat do kódu později, takže si poznamenejte, kam je ukládáte.  

### <a name="get-the-event-hub-namespace-connection-string"></a>Získání připojovacího řetězce oboru názvů centra událostí

Centrum událostí Azure přijme data události, uloží, zpracuje a přepošle data. V tomto testovacím prostředí centrum událostí Azure obdrží dokument pokaždé, když dojde k nové události (tj. položka je zobrazená uživatelem, přidala se k vozíku uživatele nebo je koupená uživatelem) a potom tento dokument přepošle na Azure Stream Analytics.

1. Vraťte se do skupiny prostředků a otevřete **obor názvů centra událostí** , který jste vytvořili, a pojmenujte ho v prelab.  

2. V nabídce na levé straně vyberte **zásady sdíleného přístupu** .  

3. Vyberte **RootManageSharedAccessKey**. Zkopírujte **připojovací řetězec – primární klíč** do poznámkového bloku nebo jiného dokumentu, ke kterému budete mít přístup v průběhu tohoto testovacího prostředí. Měli byste označit připojovací řetězec **oboru názvů IT centra událostí** . Později budete muset řetězec zkopírovat do kódu, takže si poznamenejte, kam ho ukládáte.

## <a name="set-up-azure-function-to-read-the-change-feed"></a>Nastavení funkce Azure pro čtení kanálu změn

Když se vytvoří nový dokument nebo se v kontejneru Cosmos upraví aktuální dokument, kanál změn automaticky přidá tento změněný dokument ke své historii změn kolekce. Nyní vytvoříte a spustíte funkci Azure, která zpracovává kanál změn. Když se v kolekci, kterou jste vytvořili, vytvoří nebo upraví dokument, funkce Azure se aktivuje pomocí kanálu změn. Funkce Azure pak odešle změněný dokument do centra událostí.

1. Vraťte se do úložiště, které jste naklonoval na svém zařízení.  

2. Klikněte pravým tlačítkem na soubor s názvem **ChangeFeedLabSolution. sln** a vyberte **otevřít v aplikaci Visual Studio**.  

3. V aplikaci Visual Studio přejděte na **local.settings.js** . Pak použijte hodnoty, které jste si poznamenali dříve, k vyplnění prázdných hodnot.  

4. Přejděte na **ChangeFeedProcessor.cs**. V parametrech pro funkci **Run** proveďte následující akce:  

   * Sem napište text **vaší kolekce** s názvem vaší kolekce. Pokud jste postupovali podle předchozích pokynů, název vaší kolekce je changefeedlabcollection.  
   * Text **vaší kolekce zapůjčení** nahraďte názvem vaší kolekce zapůjčení. Pokud jste postupovali podle předchozích pokynů, je název vaší kolekce zapůjčení **zapůjčení**.  
   * V horní části sady Visual Studio se ujistěte, že je v poli spouštěcí projekt nalevo od zelené šipky zobrazená zpráva **ChangeFeedFunction**.  
   * Vyberte možnost **začít**  v horní části stránky a spusťte program.  
   * Můžete potvrdit, že je funkce spuštěná, když aplikace konzoly říká "hostitel úlohy je spuštěný".

## <a name="insert-data-into-azure-cosmos-db"></a>Vložit data do Azure Cosmos DB 

Chcete-li zjistit, jak kanál změny zpracovává nové akce na webu elektronického obchodování, je nutné simulovat data, která představují uživatele sledující položky z katalogu produktů, přidávat tyto položky do košíků a kupovat položky do svých košíků. Tato data jsou libovolná a za účelem replikace toho, jaká data na webu elektronického obchodování budou vypadat jako.

1. Přejděte zpět do úložiště v Průzkumníkovi souborů a klikněte pravým tlačítkem na **ChangeFeedFunction. sln** a znovu ho otevřete v novém okně sady Visual Studio.  

2. Přejděte do souboru **App.config** . V rámci `<appSettings>` bloku přidejte koncový bod a jedinečný **primární klíč** , který jste načetli Azure Cosmos DB účtu, který jste získali dříve.  

3. Přidejte do názvů **kolekcí** a **databází** . (Tyto názvy by měly být **changefeedlabcollection** a **changefeedlabdatabase** , pokud se nerozhodnete pro pojmenování jiným způsobem.)

   :::image type="content" source="./media/changefeed-ecommerce-solution/update-connection-string.png" alt-text="Aktualizace připojovacích řetězců":::
 
4. Uložte změny ve všech upravovaných souborech.  

5. V horní části sady Visual Studio se ujistěte, že se v poli **spouštěcí projekt** nalevo od zelené šipky říká **DataGenerator**. Pak vyberte **Spustit** v horní části stránky a spusťte program.  
 
6. Počkejte, než se program spustí. Hvězdičky znamenají, že data přicházejí. Nechte program spuštěný – je důležité, aby se shromáždila spousta dat.  

7. Pokud přejdete na [Azure Portal](https://portal.azure.com/) , pak na účet Cosmos DB v rámci skupiny prostředků a potom na **Průzkumník dat**, zobrazí se náhodovaná data importovaná v **changefeedlabcollection** .
 
   :::image type="content" source="./media/changefeed-ecommerce-solution/data-generated-in-portal.png" alt-text="Data generovaná na portálu":::

## <a name="set-up-a-stream-analytics-job"></a>Nastavení úlohy Stream Analytics

Azure Stream Analytics je plně spravovaná cloudová služba pro zpracování dat streamování v reálném čase. V tomto testovacím prostředí budete pomocí služby Stream Analytics zpracovávat nové události z centra událostí (to znamená, že když se položka zobrazuje, přidá na košík nebo je koupená), zahrňte tyto události do analýzy dat v reálném čase a pošle je Power BI pro vizualizaci.

1. Z [Azure Portal](https://portal.azure.com/)přejděte do skupiny prostředků a pak na **streamjob1** (úloha Stream Analytics, kterou jste vytvořili v prelab).  

2. Vyberte **vstupy** , jak je znázorněno níže.  

   :::image type="content" source="./media/changefeed-ecommerce-solution/create-input.png" alt-text="Vytvořit vstup":::

3. Vyberte **+ Přidat vstup streamu**. Pak z rozevírací nabídky vyberte **centrum událostí** .  

4. Vyplňte nový vstupní formulář s následujícími podrobnostmi:

   * Do pole alias **vstupu** zadejte **input**.  
   * Vyberte možnost **Vybrat centrum událostí z vašich předplatných**.  
   * Nastavte pole **předplatné** na vaše předplatné.  
   * Do pole **obor názvů centra událostí** zadejte název oboru názvů centra událostí, který jste vytvořili během prelab.  
   * V poli **název centra událostí** vyberte možnost **použít existující** a z rozevírací nabídky vyberte **Event-hub1** .  
   * Pole název **zásady centra událostí** se nastaví na výchozí hodnotu.  
   * Nechte **formát serializace události** jako **JSON**.  
   * Nechejte **pole kódování** nastaveno na **UTF-8**.  
   * Pole pro **typ komprese události** je nastaveno na **hodnotu None**.  
   * Vyberte tlačítko **Uložit**.

5. Přejděte zpátky na stránku úlohy Stream Analytics a vyberte **výstupy**.  

6. Vyberte **+ Přidat**. Pak v rozevírací nabídce vyberte **Power BI** .  

7. Chcete-li vytvořit nový výstup Power BI pro vizualizaci průměrné ceny, proveďte následující akce:

   * Do pole **alias výstupu** zadejte **averagePriceOutput**.  
   * Ponechejte pole **pracovní prostor skupiny** nastaveno na **autorizovaní připojení pro načtení pracovních prostorů**.  
   * Do pole **název datové sady** zadejte **averagePrice**.  
   * Do pole **název tabulky** zadejte **averagePrice**.  
   * Vyberte tlačítko **autorizovat** a potom podle pokynů autorizujte připojení k Power BI.  
   * Vyberte tlačítko **Uložit**.  

8. Pak se vraťte na **streamjob1** a vyberte **Upravit dotaz**.

   :::image type="content" source="./media/changefeed-ecommerce-solution/edit-query.png" alt-text="Upravit dotaz":::
 
9. Vložte následující dotaz do okna dotazu. Dotaz na **průměrnou cenu** vypočítá průměrnou cenu všech položek zobrazených uživateli, průměrnou cenu všech položek přidaných do košíků uživatelů a průměrnou cenu všech položek, které uživatelé zakoupili. Tato metrika může pomáhat firmám elektronického obchodování rozhodnout, jaké ceny prodávat zboží a kde se má v inventáři investovat. Například pokud Průměrná cena zobrazených položek je mnohem vyšší než průměrná cena zakoupených položek, pak může společnost zvolit, že se má do inventáře přidat levnější položky.

   ```sql
   /*AVERAGE PRICE*/      
   SELECT System.TimeStamp AS Time, Action, AVG(Price)  
    INTO averagePriceOutput  
    FROM input  
    GROUP BY Action, TumblingWindow(second,5) 
   ```
10. Pak v levém horním rohu vyberte **Save (Uložit** ).  

11. Nyní se vraťte na **streamjob1** a v horní části stránky vyberte tlačítko **Start** . Spuštění Azure Stream Analytics může trvat několik minut, ale nakonec se změní z "spouštění" na "spuštěno".

## <a name="connect-to-power-bi"></a>Připojení k Power BI

Power BI je sada nástrojů pro obchodní analýzu, která umožňuje analyzovat data a sdílet poznatky. Je to skvělý příklad, jak můžete strategicky vizualizovat Analyzovaná data.

1. Přihlaste se k Power BI a přejděte do **pracovního prostoru** otevřením nabídky na levé straně stránky.  

2. Vyberte **+ vytvořit** v pravém horním rohu a pak vyberte **řídicí panel** pro vytvoření řídicího panelu.  

3. V pravém horním rohu vyberte **+ Přidat dlaždici** .  

4. Vyberte **vlastní streamovaná data**a pak klikněte na tlačítko **Další** .  
 
5. V **datových sadách**vyberte **averagePrice** a pak vyberte **Další**.  

6. V poli **typ vizualizace** vyberte v rozevírací nabídce **Skupinový pruhový graf** . V části **osa**přidejte akci. Přeskočit **legendu** bez přidání cokoli V další části s názvem **hodnota**přidejte **prům**. Vyberte **Další**, potom název grafu a vyberte **použít**. Na řídicím panelu by se měl zobrazit nový graf.  

7. Pokud teď chcete vizualizovat další metriky, můžete přejít zpátky na **streamjob1** a vytvořit tři další výstupy pomocí následujících polí.

   a. **Alias pro výstup:** incomingRevenueOutput, název sady dat: incomingRevenue, název tabulky: incomingRevenue  
   b. **Alias pro výstup:** top5Output, název sady dat: top5, název tabulky: top5  
   c. **Alias pro výstup:** uniqueVisitorCountOutput, název sady dat: uniqueVisitorCount, název tabulky: uniqueVisitorCount

   Pak vyberte **Upravit dotaz** a vložte následující dotazy **nad** ten, který jste už napsali.

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
   
   HORNÍ 5 dotaz vypočítá prvních 5 položek, seřazené podle počtu pokusů o zakoupení. Tato metrika může pomáhat firmám elektronického obchodování vyhodnocovat, které položky jsou nejoblíbenější a můžou mít vliv na reklamu, ceny a rozhodnutí o vyúčtování společnosti.

   Dotaz výnosů vypočítá tržby sečtením cen všech položek zakoupených každou minutu. Tato metrika může pomoci firmám elektronického obchodování zhodnotit svůj finanční výkon a také porozumět tomu, co denní přispívá k většině výnosů. To může mít vliv na celkovou strategii společnosti, zejména na marketing.

   JEDINEČNÝ dotaz návštěvníků vypočítá počet jedinečných návštěvníků v lokalitě každých 5 sekund zjišťováním ID jedinečného košíku. Tato metrika může pomáhat firmám elektronického obchodování zhodnotit svou činnost lokality a strategize, jak získat další zákazníky.

8. Nyní můžete přidat dlaždice pro tyto datové sady.

   * V horních 5 by mělo být vhodné provést skupinový sloupcový graf s položkami jako osu a počítat jako hodnotu.  
   * U výnosů by měla smysl vytvořit spojnicový graf s časem jako osou a součtem cen jako hodnoty. Časový interval, který se má zobrazit, by měl být největším možným způsobem, aby bylo možné doručovat co nejvíc informací.  
   * Pro jedinečné návštěvníky by to vedlo k tomu, že by se měla provádět vizualizace karet s počtem jedinečných návštěvníků jako hodnota.

   Tímto způsobem vypadá vzorový řídicí panel s těmito grafy:

   :::image type="content" source="./media/changefeed-ecommerce-solution/visualizations.png" alt-text="Snímek obrazovky ukazuje vzorový řídicí panel s grafy s názvem Průměrná cena položek podle akcí, jedinečných návštěvníků, výnosů a hlavních 5 položek koupených.":::

## <a name="optional-visualize-with-an-e-commerce-site"></a>Volitelné: vizualizace pomocí webu elektronického obchodování

Teď budete sledovat, jak můžete použít nový nástroj pro analýzu dat pro připojení k reálnému webu elektronického obchodování. Aby bylo možné sestavit web elektronického obchodování, použijte databázi Azure Cosmos k uložení seznamu kategorií produktů (ženy, muži, Unisex), katalogu produktů a seznamu nejoblíbenějších položek.

1. Přejděte zpět do [Azure Portal](https://portal.azure.com/)a potom na **účet Cosmos DB**a pak na **Průzkumník dat**.  

   Přidejte dvě kolekce v kategorii **changefeedlabdatabase**  -  **Products** and **Categories** s pevnou kapacitou úložiště.

   Do **changefeedlabdatabase** s názvem **topItems** a **/Item** přidejte další kolekci jako klíč oddílu.

2. Vyberte kolekci **topItems** a v části **škálování a nastavení** nastavte **čas na živé** na **30 sekund** , aby se aktualizace topItems každých 30 sekund.

   :::image type="content" source="./media/changefeed-ecommerce-solution/time-to-live.png" alt-text="Hodnota TTL (Time to Live)":::

3. Pro naplnění kolekce **topItems** s nejčastěji zakoupenými položkami přejděte zpět na **streamjob1** a přidejte nový **výstup**. Vyberte **Cosmos DB**.

4. Vyplňte požadovaná pole na obrázku níže.

   :::image type="content" source="./media/changefeed-ecommerce-solution/cosmos-output.png" alt-text="Výstup Cosmos":::
 
5. Pokud jste přidali volitelný dotaz TOP 5 v předchozí části testovacího prostředí, přejděte k části 5a. V takovém případě pokračujte na část 5b.

   5a. V **streamjob1**vyberte **Upravit dotaz** a vložte následující dotaz do Azure Stream Analytics editoru dotazů pod horním 5 dotazem, ale nad rámec dalších dotazů.

   ```sql
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```
   5b. V **streamjob1**vyberte **Upravit dotaz** a vložte následující dotaz do editoru dotazů Azure Stream Analytics nad všemi ostatními dotazy.

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

6. Otevřete **EcommerceWebApp. sln** a v **Průzkumník řešení**přejděte do souboru **Web.config** .  

7. V rámci `<appSettings>` bloku přidejte **identifikátor URI** a **primární klíč** , který jste předtím uložili, kde uvádí **identifikátor URI tady** a **váš primární klíč**. Pak přidejte **název databáze** a **název kolekce** , jak je uvedeno níže. (Tyto názvy by měly být **changefeedlabdatabase** a **changefeedlabcollection** , pokud se nerozhodnete své pojmenovat jinak.)

   Zadejte název **kolekce Products**, **název kolekce kategorií**a **název kolekce hlavních položek** , jak je uvedeno. (Tato jména by měla být **Products, categories a topItems,** Pokud se nerozhodnete, že si nebudete pojmenovat jinak.)  

8. Přejděte do **složky pro registraci** v **EcommerceWebApp. sln** a otevřete ji. Pak otevřete soubor **Web.config** v této složce.  

9. V rámci `<appSettings>` bloku přidejte **identifikátor URI** a **primární klíč** , který jste uložili dříve tam, kde jsou uvedeny. Pak přidejte **název databáze** a **název kolekce** , jak je uvedeno níže. (Tyto názvy by měly být **changefeedlabdatabase** a **changefeedlabcollection** , pokud se nerozhodnete své pojmenovat jinak.)  

10. Spusťte program stisknutím klávesy **Start** v horní části stránky.  

11. Nyní se můžete pohybovat na webu elektronického obchodování. Když si zobrazíte položku, přidáte položku do košíku, změníte množství položky na vozíku nebo si položku koupíte, tyto události budou předány prostřednictvím Cosmos DBho kanálu změny do centra událostí, ASA a pak Power BI. Doporučujeme, abyste dál spouštěli data Generator a vygenerovali významné údaje o webových přenosech a na webu elektronického obchodování zajistili realistickou sadu "Hot Products".

## <a name="delete-the-resources"></a>Odstranit prostředky

Pokud chcete odstranit prostředky, které jste vytvořili během tohoto testovacího prostředí, přejděte do skupiny prostředků v [Azure Portal](https://portal.azure.com/), v nabídce v horní části stránky vyberte **Odstranit skupinu prostředků** a postupujte podle uvedených pokynů.

## <a name="next-steps"></a>Další kroky 
  
* Další informace o službě Change feed najdete [v tématu práce s podporou změny kanálu v Azure Cosmos DB](change-feed.md) 
