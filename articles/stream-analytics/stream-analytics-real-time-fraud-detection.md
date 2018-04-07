---
title: Zjišťování podvodů v reálném čase pomocí služby Azure Stream Analytics
description: Naučte se vytvářet řešení zjišťování podvodů v reálném čase pomocí služby Stream Analytics. Pomocí centra událostí ke zpracování událostí v reálném čase.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 1ebbdb22698ec1eab76b6b6b504fe27a6f0b28bf
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Začněte používat Azure Stream Analytics: odhalování podvodů v reálném čase

V tomto kurzu poskytuje začátku do konce obrázek o tom, jak používat Azure Stream Analytics. Získáte informace o těchto tématech: 

* Přepněte do instance služby Azure Event Hubs vysílání datových proudů. V tomto kurzu budete používat aplikaci, která poskytujeme která simuluje proud záznamů metadat mobilního telefonu.

* Psát dotazy podobné jazyku SQL Stream Analytics k transformaci dat, agregace informací o nebo hledáte vzory. Zobrazí se k ověření příchozího datového proudu a vyhledejte volání, které mohou být podvodné použití dotazu.

* Odešlou výsledky do výstupní jímku (úložiště), abyste mohli analyzovat další statistiky. V takovém případě budete odesílat data podezřelé volání do úložiště objektů Blob Azure.

V tomto kurzu používáme v příkladu odhalování podvodů v reálném čase, které jsou na základě dat telefonního hovoru. Ale technik, které jsme ilustrují vhodná taky pro jiné typy odhalování podvodů, jako je krádež platební karty podvodu nebo identity. 

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Scénář: Telecommunications a SIM odhalování podvodů v reálném čase

Společnost telecommunications má velký objem dat pro příchozí volání. Společnost chce k rozpoznání podvodné volání v reálném čase, tak, aby oznámit zákazníků nebo vypnout službu pro specifické číslo. Jeden typ SIM podvodů zahrnuje několik volání z stejnou identitu přibližně ve stejnou dobu, ale v geograficky různých umístěních. Ke zjištění tento typ podvodů, společnost potřebuje ke zkoumání příchozí phone záznamy a vyhledat konkrétní vzory – v takovém případě pro volání provedená v různých zemí přibližně ve stejnou dobu. Všechny záznamy phone, které do této kategorie patří se zapisují do úložiště pro další analýzu.

## <a name="prerequisites"></a>Požadavky

V tomto kurzu budete simulovat telefonní hovor dat pomocí klientskou aplikaci, která generuje ukázka telefonní hovor metadat. Některé záznamy, které vytváří aplikace vypadat jako podvodné volání. 

Než začnete, ujistěte se, že máte následující:

* Účet Azure.
* Generátor aplikace události volání. To můžete získat tak, že stáhnete [TelcoGenerator.zip soubor](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) z webu Microsoft Download Center. Rozbalte tento balíček do složky v počítači. Pokud chcete zobrazit zdrojový kód a spusťte aplikaci v ladicí program, můžete získat zdrojovém kódu aplikace z [Githubu](https://aka.ms/azure-stream-analytics-telcogenerator). 

    >[!NOTE]
    >Windows mohou blokovat soubor stažený .zip. Pokud nelze rozbalte ji, klikněte pravým tlačítkem na soubor a vyberte **vlastnosti**. Pokud se zobrazí zpráva "Tento soubor pochází z jiného počítače a mohou být blokovány k ochraně tohoto počítače", vyberte **Odblokovat** a potom klikněte na **použít**.

Pokud chcete podívejte se na výsledky úlohy streamování Analytics, musíte taky nástroj pro zobrazení obsahu kontejner úložiště objektů Blob Azure. Pokud používáte Visual Studio, můžete použít [nástroje Azure pro sadu Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) nebo [cloudu Průzkumníka Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer). Alternativně můžete nainstalovat samostatných nástrojů, jako je například [Azure Storage Explorer](http://storageexplorer.com/) nebo [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction). 

## <a name="create-an-azure-event-hubs-to-ingest-events"></a>Vytvoření Azure event hubs pro načítání událostí

Analyzovat datový proud je *ingestování* do Azure. Typické způsob pro načítání dat je použití [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md), což vám umožní přijímat miliony událostí za sekundu a pak zpracování a ukládání informací o události. V tomto kurzu vytvoříte centra událostí a pak mít události volání generátor aplikace posílat data volání do tohoto centra událostí. Další informace o službě event hubs naleznete v tématu [dokumentace Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).

>[!NOTE]
>Podrobnější verzi tohoto postupu, naleznete v části [vytvořit obor názvů Event Hubs a centra událostí pomocí portálu Azure](../event-hubs/event-hubs-create.md). 

### <a name="create-a-namespace-and-event-hub"></a>Vytvořit obor názvů a události rozbočovače
V tomto postupu vytvoříte na obor názvů centra událostí a poté přidejte centra událostí do daného oboru názvů. Obory názvů centra událostí se používají k logickému seskupení souvisejících událostí sběrnice instancí. 

1. Přihlaste se k portálu Azure a klikněte na tlačítko **vytvořit prostředek** > **Internet věcí** > **centra událostí**. 

2. V **vytvoření oboru názvů** podokně, zadejte název oboru názvů, jako `<yourname>-eh-ns-demo`. Můžete použít libovolný název pro obor názvů, ale název musí být platné adresy URL a musí být jedinečný v Azure. 
    
3. Vyberte předplatné a vytvořit nebo vybrat skupinu prostředků a potom klikněte na tlačítko **vytvořit**. 

    ![Vytvoření oboru názvů události rozbočovače](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-namespace-new-portal.png)
 
4. Po dokončení nasazení obor názvů v seznamu prostředků Azure najdete názvový prostor události rozbočovače. 

5. Klikněte na tlačítko Nový obor názvů a v podokně obor názvů, klikněte na tlačítko **centra událostí**.

    ![Tlačítko Přidat centra událostí pro vytvoření nového centra událostí ](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-button-new-portal.png)    
 
6. Název nového centra událostí `sa-eh-frauddetection-demo`. Můžete použít jiný název. Pokud tak učiníte, poznamenejte si, protože potřebujete název později. Nemusíte nyní nastavte další možnosti pro centra událostí.

    ![Okno pro vytvoření nového centra událostí](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-new-portal.png)
    
 
7. Klikněte na možnost **Vytvořit**.
### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Udělit přístup k Centru událostí a získat připojovací řetězec

Předtím, než se proces může odesílat data do centra událostí, musí mít centra událostí zásadu, která umožňuje odpovídající přístup. Zásady přístupu vytvoří připojovací řetězec, který obsahuje informace o ověření.

1.  V podokně oboru názvů událostí, klikněte na tlačítko **Event Hubs** a pak klikněte na název vašeho nového centra událostí.

2.  V podokně centra událostí, klikněte na tlačítko **zásady sdíleného přístupu** a pak klikněte na  **+ &nbsp;přidat**.

    >[!NOTE]
    >Ujistěte se, že pracujete s centrem událostí, není oboru názvů centra událostí.

3.  Přidat zásadu s názvem `sa-policy-manage-demo` a **deklarace identity**, vyberte **spravovat**.

    ![Okno pro vytvoření nové zásady přístupu centra událostí](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-shared-access-policy-manage-new-portal.png)
 
4.  Klikněte na možnost **Vytvořit**.

5.  Po nasazení zásady, klikněte na něj v seznamu zásady sdíleného přístupu.

6.  Vyhledejte seznam **PŘIPOJOVACÍ řetězec primární klíč** a klikněte na tlačítko Kopírovat vedle připojovací řetězec. 
    
    ![Kopírování klíče primární připojovací řetězec ze zásad přístupu](./media/stream-analytics-real-time-fraud-detection/stream-analytics-shared-access-policy-copy-connection-string-new-portal.png)
 
7.  Vložte připojovací řetězec do textového editoru. Tento připojovací řetězec pro další části, musíte po provedení některé malé úpravy.

    Připojovací řetězec vypadá takto:

        Endpoint=sb://YOURNAME-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=sa-policy-manage-demo;SharedAccessKey=Gw2NFZwU1Di+rxA2T+6hJYAtFExKRXaC2oSQa0ZsPkI=;EntityPath=sa-eh-frauddetection-demo

    Všimněte si, že připojovací řetězec obsahuje více párů klíč hodnota oddělené středníky: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`, a `EntityPath`.  

## <a name="configure-and-start-the-event-generator-application"></a>Nakonfigurujte a spusťte aplikaci generátor událostí

Než začnete TelcoGenerator aplikace, musíte ho nakonfigurovat tak, aby se budou odesílat záznamy volání do centra událostí, kterou jste vytvořili.

### <a name="configure-the-telcogeneratorapp"></a>Konfigurace TelcoGeneratorapp

1.  V editoru, kam jste zkopírovali připojovací řetězec, poznamenejte si `EntityPath` hodnotu a pak odeberte `EntityPath` pár (Nezapomeňte odebrat středník předcházejícího). 

2.  Ve složce, kde unzipped soubor TelcoGenerator.zip otevřete soubor telcodatagen.exe.config v editoru. (Existuje více než jeden soubor .config, proto se ujistěte, že otevřete ten správný.)

3.  V `<appSettings>` element:

    * Nastavte hodnotu `EventHubName` klíče na název centra událostí (tedy na hodnotu cesty entity).
    * Nastavte hodnotu `Microsoft.ServiceBus.ConnectionString` klíče připojovací řetězec. 

    `<appSettings>` Části bude vypadat jako v následujícím příkladu. (Pro přehlednost, jsme zabalená čar a odebrat některé znaky z tokenu autorizace.)

    ![Soubor konfigurace aplikace TelcoGenerator zobrazující event hub název a připojovací řetězec](./media/stream-analytics-real-time-fraud-detection/stream-analytics-telcogenerator-config-file-app-settings.png)
 
4.  Uložte soubor. 

### <a name="start-the-app"></a>Spusťte aplikaci
1.  Otevřete okno příkazového řádku a přejděte do složky, kde je rozbalené TelcoGenerator aplikace.
2.  Zadejte následující příkaz:

        telcodatagen.exe 1000 .2 2

    Parametry jsou: 

    * Počet disky CDR za hodinu. 
    * SIM karta podvod pravděpodobnosti: Jak často jako procento všechna volání, že aplikace má simulovat podvodné volání. Hodnota.2 znamená, že který přibližně 20 % záznamy volání bude vypadat podvodné.
    * Doba v hodinách. Počet hodin, které by měla spustit aplikace. Můžete také zastavit aplikaci kdykoli stisknutím kombinace kláves Ctrl + C na příkazovém řádku.

    Za několik sekund spuštění aplikace zobrazení záznamů telefonního hovoru na obrazovce, jako je odešle do centra událostí.

Některá pole klíče, které budete používat v této aplikaci zjišťování podvodů v reálném čase jsou následující:

|**záznam**|**Definice**|
|----------|--------------|
|`CallrecTime`|Časové razítko pro volání počáteční čas. |
|`SwitchNum`|Přepínač telefonní používaná k připojení volání. V tomto příkladu přepínače jsou řetězce, které představují země původu (USA, Čína, UK, Německu nebo Austrálie). |
|`CallingNum`|Telefonní číslo volajícího. |
|`CallingIMSI`|Mezinárodní mobilní odběratele Identity (IMSI). Toto je jedinečný identifikátor volajícím. |
|`CalledNum`|Telefonní číslo příjemce volání. |
|`CalledIMSI`|Mezinárodní mobilní odběratele Identity (IMSI). Toto je jedinečný identifikátor hovoru příjemce. |


## <a name="create-a-stream-analytics-job-to-manage-streaming-data"></a>Vytvořit úlohu služby Stream Analytics ke správě dat

Teď, když máte datový proud událostí volání, můžete nastavit úlohu služby Stream Analytics. Úloha bude číst data z centra událostí, které jste nastavili. 

### <a name="create-the-job"></a>Vytvoření úlohy 

1. Na portálu Azure klikněte na tlačítko **vytvořit prostředek** > **Internet věcí** > **úlohy služby Stream Analytics**.

2. Název úlohy `sa_frauddetection_job_demo`, určete předplatné, skupinu prostředků a umístění.

    Je vhodné umístit úlohy a centra událostí ve stejné oblasti pro nejlepší výkon, a tak, že nemáte platíte k přenosu dat mezi oblastmi.

    ![Vytvoření nové úlohy Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-job-new-portal.png)

3. Klikněte na možnost **Vytvořit**.

    Vytvoření úlohy a na portálu se zobrazí podrobnosti úlohy. Nic běží ještě, přestože – budete muset nakonfigurovat úloha může být zahájena.

### <a name="configure-job-input"></a>Konfigurace úlohy vstup

1. Na řídicím panelu nebo **všechny prostředky** , vyhledejte a vyberte `sa_frauddetection_job_demo` úlohy služby Stream Analytics. 
2. V **úlohy topologie** části podokno úlohy Stream Analytics, klikněte na tlačítko **vstup** pole.

    ![Vstupní pole v části topologie v podokně úlohy streamování Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-input-box-new-portal.png)
 
3. Klikněte na tlačítko  **+ &nbsp;přidat** a potom vyplňte v podokně s těmito hodnotami:

    * **Vstupní alias**: použijte název `CallStream`. Pokud použijete jiný název, poznamenejte si jeho vzhledem k tomu, že ho budete potřebovat později.
    * **Typ zdroje**: vyberte **datový proud**. (**Referenční data** vztahuje se k datům statickou vyhledávání, který nebudete používat v tomto kurzu.)
    * **Zdroj**: vyberte **centra událostí**.
    * **Import možnost**: vyberte **použijte Centrum událostí z aktuálního předplatného**. 
    * **Obor názvů sběrnice**: Vyberte obor názvů centra událostí, který jste vytvořili dříve (`<yourname>-eh-ns-demo`).
    * **Centra událostí**: Vyberte centra událostí, které jste vytvořili dříve (`sa-eh-frauddetection-demo`).
    * **Název zásady centra událostí**: vyberte zásady přístupu, které jste vytvořili dříve (`sa-policy-manage-demo`).

    ![Vytvořit nový vstupní úlohy streamování Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-input-new-portal.png)

4. Klikněte na možnost **Vytvořit**.

## <a name="create-queries-to-transform-real-time-data"></a>Vytváření dotazů k transformaci dat v reálném čase

V tuto chvíli máte úloha Stream Analytics nastavit tak, aby číst příchozí datový proud. Dalším krokem je vytvoření transformaci, která analyzuje data v reálném čase. To uděláte tak, že vytvoříte dotaz. Stream Analytics podporuje jednoduchý a deklarativní dotazu model, který popisuje transformace pro zpracování v reálném čase. Dotazy pomocí SQL jako jazyk, který má některé rozšíření konkrétní do služby stream analytics. 

Jednoduchý dotaz můžou jenom číst všechny příchozí data. Ale často vytvořit dotazy, které vypadají pro konkrétní data, nebo pro vztahy v datech. V této části kurzu vytvoříte a otestujete několik dotazů další několik způsobů, ve kterých můžete převést vstupní datový proud pro analýzu. 

Dotazy, které zde vytvoříte, se zobrazí právě Transformovaná data na obrazovku. V další části nakonfigurujete výstupní jímku a dotaz, který zapíše Transformovaná data do tohoto jímky.

Další informace o jazyk, najdete v článku [referenční příručka Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/dn834998.aspx).

### <a name="get-sample-data-for-testing-queries"></a>Načíst ukázková data pro testování dotazů

TelcoGenerator aplikace odesílá záznamy volání do centra událostí a vaše úlohy služby Stream Analytics je konfigurován ke čtení z centra událostí. Dotaz můžete použít k testování úlohy, abyste měli jistotu, že je správně čtení. Chcete-li otestovat dotazu v konzole Azure, musíte ukázková data. V tomto návodu budete extrahovat ukázková data z datového proudu, který přichází do centra událostí.

1. Ujistěte se, že je spuštěna a vytváření záznamů volání TelcoGenerator aplikace.
2. Na portálu vrátíte se do podokna úlohy streamování Analytics. (Pokud jste zavřeli podokně, vyhledejte `sa_frauddetection_job_demo` v **všechny prostředky** podokně.)
3. Klikněte **dotazu** pole. Azure uvádí vstupy a výstupy, které jsou nakonfigurovány pro úlohy a umožňuje vám vytvořit dotaz, který vám umožňuje transformovat vstupní datový proud, jako jsou odeslána do výstupu.
4. V **dotazu** podokně klikněte na tlačítko se tečkami vedle `CallStream` vstup a pak vyberte **vzorová data ze vstupu**.

    ![Položka, s "Ukázkových dat ze vstupu" vybrané úlohy možnosti nabídky ukázková data pro analýzy datových proudů](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sample-data-from-input.png)

    Otevře se podokno, které umožňuje určit, kolik ukázková data získat, definována v tom, jak dlouho ke čtení vstupního datového proudu.

5. Nastavit **minut** na 3 a pak klikněte na **OK**. 
    
    ![Možnosti vzorkování vstupního datového proudu s vybrané "3 minut".](./media/stream-analytics-real-time-fraud-detection/stream-analytics-input-create-sample-data.png)

    Azure ukázky data z vstupního datového proudu za 3 minuty a vás upozorní, jakmile je připraven ukázková data. (To trvá nějakou dobu.) 

Ukázková data jsou ukládána dočasně a je k dispozici při otevření okna dotazu. Pokud zavřete okno dotazu se zahodí ukázkových dat a budete muset vytvořit novou sadu ukázková data. 

Jako alternativu můžete získat soubor .json, který se nachází ukázková data [z Githubu](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json)a pak tento soubor .json, který bude použit jako ukázková data pro nahrajte `CallStream` vstupní. 

### <a name="test-using-a-pass-through-query"></a>Testování pomocí předávacího dotazu

Pokud chcete archivovat každé události, můžete načíst všechna pole v datové části události předávací dotaz.

1. V okně dotazu zadejte tento dotaz:

        SELECT 
            *
        FROM 
            CallStream

    >[!NOTE]
    >Stejně jako u SQL, klíčová slova nejsou malá a velká písmena a prázdný znak není důležité.

    V tomto dotazu `CallStream` je alias, kterou jste zadali při vytvoření vstupu. Pokud jste použili jiný alias, použijte tento název.

2. Klikněte na tlačítko **Test**.

    Úloha Stream Analytics spouští dotaz ukázkových dat a zobrazí výstup v dolní části okna. Znamená to, zda jsou správně nakonfigurovány centra událostí a úlohy streamování Analytics. (Jak je uvedeno, později vytvoříte výstupní jímku který dotaz může zapisovat data.)

    ![Výstup úlohy Stream Analytics zobrazující 73 záznamů vygenerovaných](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output.png)

    Přesný počet záznamů, které vidíte bude záviset na počet záznamů zaznamenalo v 3 minut ukázky.
 
### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Snižte počet polí pomocí sloupce projekce

V mnoha případech nepotřebuje analýzy všechny sloupce ze vstupního datového proudu. Dotaz můžete projektu menší sadu vrácený pole než v předávací dotaz.

1. Změna dotazu v editoru kódu takto:

        SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum 
        FROM 
            CallStream

2. Klikněte na tlačítko **Test** znovu. 

    ![Úlohy Stream Analytics se výstup. pro projekci, zobrazují se 25 záznamy vygenerované](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-projection.png)
 
### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Počet příchozích volání podle oblasti: Přeskakující okno s průběhem

Předpokládejme, že chcete zjistit počet příchozích hovorů každou oblast. Potřebujete v streamovaných dat užitečné, když chcete provést agregační funkce, jako je počítání, segmentovat datový proud do dočasné jednotky (protože je datový proud samotné efektivně nekonečná). Můžete to udělat pomocí Analytics se streamování [oddílovou funkci](stream-analytics-window-functions.md). Potom můžete pracovat s daty v dané okno jako jednotku.

Tato transformace se má pořadí dočasné Windows, které nepřekrývaly – každé okno bude mít diskrétní sadu dat, která můžete seskupovat a agregaci. Tento typ okna se označuje jako *Přeskakující okno* . V rámci Přeskakující okno, můžete získat počet příchozích hovorů seskupené podle `SwitchNum`, která představuje zemi, kde volání vytvořena. 

1. Změna dotazu v editoru kódu takto:

        SELECT 
            System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
        FROM
            CallStream TIMESTAMP BY CallRecTime 
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum

    Tento dotaz používá `Timestamp By` – klíčové slovo v `FROM` klauzule určete kterých časové razítko pole v vstupního datového proudu k definování Přeskakující okno. V takovém případě okno rozděluje data do segmentů pomocí `CallRecTime` pole všechny záznamy. (V případě žádné pole operaci oddílová používá čas, všechny události dorazí na centra událostí. V části "Čas aplikace Vs čas doručení" v [Stream Analytics referenční příručka jazyka dotazů](https://msdn.microsoft.com/library/azure/dn834998.aspx). 

    Projekce zahrnuje `System.Timestamp`, která vrací časového razítka na konci každého období. 

    Chcete-li určit, zda chcete použít Přeskakující okno, je použít [TUMBLINGWINDOW](https://msdn.microsoft.com/library/dn835055.aspx) fungovat v `GROUP BY `klauzule. Ve funkci zadejte časovou jednotku (od mikrosekund na den) a velikost okna (počet jednotek). V tomto příkladu Přeskakující okno se skládá z 5 sekund intervalech, zobrazí se počet podle země pro volání za každých 5 sekund.

2. Klikněte na tlačítko **Test** znovu. Ve výsledcích, Všimněte si, že časová razítka v části **WindowEnd** jsou v přírůstcích po 5 sekund.

    ![Úlohy Stream Analytics se výstup za účelem agregace, zobrazují se 13 záznamy vygenerované](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-aggregation.png)
 
### <a name="detect-sim-fraud-using-a-self-join"></a>Odhalování podvodů SIM pomocí vlastní spojení

V tomto příkladu jsme Zvažte podvodné použití být volání, které pocházejí ze stejného uživatele, ale v různých umístěních do 5 sekund od sebe navzájem. Například stejného uživatele nelze vytvořit legálně volání z USA a Austrálie ve stejnou dobu. 

Pokud chcete zkontrolovat pro tyto případy, můžete vlastní spojení dat spojení, datový proud na základě `CallRecTime` hodnotu. Potom můžete vyhledat volání záznamy, kde `CallingIMSI` hodnotu (výchozí číslo) je stejné, ale `SwitchNum` hodnotu (země původu) není stejný.

Když použijete ke spojení s streamovaných dat užitečné, spojení musíte zadat, že některá omezení na tom, jak úplně odpovídající řádky je možné oddělit v čase. (Jak již bylo uvedeno dříve, streamování dat je efektivně nekonečná.) Zadat čas hranice pro relaci v rámci `ON` klauzuli spojení, pomocí `DATEDIFF` funkce. V takovém případě spojení je založena na 5 sekund intervalu dat volání.

1. Změna dotazu v editoru kódu takto: 

        SELECT  System.Timestamp as Time, 
            CS1.CallingIMSI, 
            CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, 
            CS1.SwitchNum as Switch1, 
            CS2.SwitchNum as Switch2 
        FROM CallStream CS1 TIMESTAMP BY CallRecTime 
            JOIN CallStream CS2 TIMESTAMP BY CallRecTime 
            ON CS1.CallingIMSI = CS2.CallingIMSI 
            AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5 
        WHERE CS1.SwitchNum != CS2.SwitchNum

    Tento dotaz není třeba žádné připojení k SQL s výjimkou pro `DATEDIFF` funkce ve spojení. Toto je verze `DATEDIFF` která je specifická pro analýzy datových proudů, a musí se nacházet v `ON...BETWEEN` klauzule. Parametry jsou časovou jednotku (v sekundách v tomto příkladu) a aliasy dvou zdrojů pro spojení. (To se liší od standardní SQL `DATEDIFF` funkce.) 

    `WHERE` Klauzule zahrnuje podmínku, která flags podvodné volání: původní přepínače nejsou stejné. 

2. Klikněte na tlačítko **Test** znovu. 

    ![Výstup pro spojení sama na sebe, zobrazení 6 záznamů vygenerovaných úlohy Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-self-join.png)

3. Klikněte na **Uložit**. To umožňuje ušetřit dotazu spojení sama na sebe jako součást úlohy streamování Analytics. (Ho nebude uložen ukázková data.)

    ![Uložit úlohu služby Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-save-button-new-portal.png)

## <a name="create-an-output-sink-to-store-transformed-data"></a>Vytvoření výstupní jímku pro ukládání Transformovaná data

Jste definovali datového proudu událostí, centra událostí vstup pro načítání událostí a dotaz, který provádět prostřednictvím datového proudu transformace. Posledním krokem je definování výstupní jímku pro úlohu – to znamená, místo pro zápis transformovaný datový proud do. 

Mnoho prostředků můžete použít jako výstup jímky – databázi systému SQL Server, úložiště table, úložiště Data Lake, Power BI a i jiné centra událostí. V tomto kurzu napíšete datový proud do úložiště objektů Blob Azure, což je typické volbou pro shromažďování informací o události pro pozdější analýzu, protože počítá Nestrukturovaná data.

Pokud máte stávající účet úložiště objektů blob, můžete použít, který. V tomto kurzu jsme budete ukazují, jak vytvořit nový účet úložiště jenom pro účely tohoto kurzu.

### <a name="create-an-azure-blob-storage-account"></a>Vytvoření účtu Azure Blob Storage

1. V portálu Azure vrátíte do podokna úloh streamování Analytics. (Pokud jste zavřeli podokně, vyhledejte `sa_frauddetection_job_demo` v **všechny prostředky** podokně.)
2. V **úlohy topologie** klikněte na položku **výstup** pole. 
3. V **výstupy** podokně klikněte na tlačítko  **+ &nbsp;přidat** a potom vyplňte v podokně s těmito hodnotami:

    * **Alias pro výstup**: použijte název `CallStream-FraudulentCalls`. 
    * **Jímky**: vyberte **úložiště objektů Blob**.
    * **Možnosti importu**: vyberte **pomocí úložiště objektů blob z aktuálního předplatného**.
    * **Účet úložiště**. Vyberte **vytvořit nový účet úložiště.**
    * **Účet úložiště** (druhé pole). Zadejte `YOURNAMEsademo`, kde `YOURNAME` je název vaší nebo jiné jedinečné řetězce. Název můžete použít jenom malá písmena a čísla a musí být jedinečný v Azure. 
    * **Kontejner**. Zadejte `sa-fraudulentcalls-demo`.
    Název účtu úložiště a název kontejneru se používají společně zajistit identifikátor URI pro úložiště objektů blob, například takto: 

    `http://yournamesademo.blob.core.windows.net/sa-fraudulentcalls-demo/...`
    
    !["Nové výstup" v podokně úlohy služby Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-output-blob-storage-new-console.png)
    
4. Klikněte na možnost **Vytvořit**. 

    Azure vytvoří účet úložiště a automaticky vygeneruje klíč. 

5. Zavřít **výstupy** podokně. 

## <a name="start-the-streaming-analytics-job"></a>Spustit úlohu streamování Analytics

Úloha je nyní nakonfigurována. Zadané vstup (centra událostí), transformaci (dotazu k vyhledání podvodné volání) a výstup (úložiště objektů blob). Nyní můžete spustit úlohu. 

1. Zajistěte, aby že telcogenerator aplikace běží.

2. V podokně úlohy klikněte na tlačítko **spustit**.

    ![Spustit úlohu služby Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start-output.png)

3. V **spuštění úlohy** podokno výstup čas spuštění úlohy, vyberte **nyní**. 

4. Klikněte na tlačítko **spustit**. 

    !["Spuštění úlohy" v podokně úlohy služby Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start-job-blade.png)

    Azure vás upozorní, jakmile byla úloha spuštěna, a v podokně úlohy, stav se zobrazí jako **systémem**.

    ![Stav úlohy Stream Analytics, zobrazující "Spuštěný"](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-running-status.png)
    

## <a name="examine-the-transformed-data"></a>Zkontrolujte Transformovaná data

Nyní máte dokončení úlohy streamování Analytics. Úloha je zkoumání proud metadata telefonní hovor, hledá podvodné telefonní hovory v reálném čase a zápis informace o těchto podvodné volání do úložiště. 

K dokončení tohoto kurzu, můžete se podívat na získanými v úloze streamování Analytics. Probíhá zápis dat do služby Azure Blog Storage v bloky dat (soubory). Můžete použít jakýkoli nástroj, který čte Azure Blob Storage. Jak jsme uvedli v části požadavky, můžete použít rozšíření Azure v sadě Visual Studio, nebo můžete použít nástroje, jako je [Azure Storage Explorer](http://storageexplorer.com/) nebo [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction). 

Pokud byste zkontrolovat obsah souboru v úložišti objektů blob, zobrazí přibližně takto:

![Úložiště objektů blob v Azure s výstupem analýzy datových proudů](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-blob-storage-view.png)
 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Máme další články, které dál se scénářem odhalování podvodů a které používají prostředky, které jste vytvořili v tomto kurzu. Pokud chcete pokračovat, přečtěte si téma návrhy pod **další kroky** později.

Ale pokud s tím budete hotovi a nepotřebujete prostředky, které jste vytvořili, odstraněním je tak, aby vám zbytečně nenabíhaly poplatky nepotřebné Azure. V takovém případě doporučujeme, abyste provedli následující:

1. Zastavte úlohu streamování Analytics. V **úlohy** podokně klikněte na tlačítko **Zastavit** v horní části.
2. Zastavit Telco generátor aplikace. V příkazovém okně, kde jste spustili aplikaci stiskněte kombinaci kláves Ctrl + C.
3. Pokud jste vytvořili nový účet úložiště objektů blob jenom pro účely tohoto kurzu, odstraňte jej. 
4. Odstraňte úlohu streamování Analytics.
5. Odstraňte centra událostí.
6. Odstraňte názvový prostor události rozbočovače.

## <a name="get-support"></a>Získat podporu

Pro další pomoc, vyzkoušejte naše [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup

Můžete pokračovat v tomto kurzu se v následujícím článku:

* [Stream Analytics a Power BI: řídicí panel analýzy v reálném čase pro datový proud](stream-analytics-power-bi-dashboard.md). Tento článek ukazuje, jak odeslat TelCo výstup úlohy Stream Analytics k Power BI pro v reálném čase vizualizaci a analýzu.

Další informace o Stream Analytics obecně platí, najdete v těchto článcích:

* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
