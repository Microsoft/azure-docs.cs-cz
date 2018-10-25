---
title: Zjišťování možných podvodů v reálném čase pomocí Azure Stream Analytics
description: Zjistěte, jak vytvořit řešení, zjišťování možných podvodů v reálném čase pomocí Stream Analytics. Používání centra událostí pro zpracování událostí v reálném čase.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 43202e88482933aed7952f6cc97dcaf1e0dcb5e7
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986028"
---
# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Začněte používat Azure Stream Analytics: zjišťování možných podvodů v reálném čase

Tento kurz obsahuje začátku do konce ilustraci, jak používat Azure Stream Analytics. Získáte informace o těchto tématech: 

* Přeneste streamování událostí do instance služby Azure Event Hubs. V tomto kurzu použijete aplikaci, která simuluje datový proud metadat mobilního telefonu záznamů.

* Psaní dotazů SQL jako Stream Analytics k transformaci dat, agregace informace nebo pokud chcete najít vzory. Uvidíte jak pomocí dotazu můžete prozkoumat příchozí datový proud a hledat volání, které mohou být podvodné.

* Výsledky odešlete do výstupní jímky (úložiště), která můžete analyzovat další přehledy. V takovém případě budete odesílat data podezřelé volání do úložiště objektů Blob v Azure.

V tomto kurzu se používá příklad podvodů v reálném čase podle dat telefonních hovorů. Techniku ukazuje vhodná taky pro jiné typy zjišťování možných podvodů, jako je například platební karty podvodu nebo krádež identity. 

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Scénář: Telekomunikace a SIM zjišťování možných podvodů v reálném čase

Telekomunikační firma má velký objem dat pro příchozí volání. Společnost chce zjistit podvodných volání v reálném čase tak, aby se upozornění zákazníků nebo vypnutí služby pro konkrétní číslo. Jeden typ SIM podvodů zahrnuje několik volání z stejnou identitu přibližně ve stejnou dobu, ale v geograficky různých umístěních. Ke zjištění tohoto typu podvodů, společnost potřebuje k prozkoumání příchozí sestavě pro telefon a vyhledávat vzory konkrétní – v takovém případě pro volání přibližně ve stejnou dobu v různých zemích. Telefon záznamy, které do této kategorie patří se zapisují do úložiště pro další analýzu.

## <a name="prerequisites"></a>Požadavky

V tomto kurzu budete simulovat dat telefonních hovorů pomocí klientskou aplikaci, která generuje ukázkové telefonního hovoru metadat. Některé záznamy, které vytvoří aplikace vypadat podvodná volání. 

Než začnete, ujistěte se, že jste provedli následující akce:

* Účet Azure.
* Aplikace generátoru událostí volání, [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip), který si můžete stáhnout z webu Microsoft Download Center. Rozbalte tento balíček do složky v počítači. Pokud chcete zobrazit zdrojový kód a spusťte aplikaci v ladicí program, můžete získat zdrojový kód aplikace z [Githubu](https://aka.ms/azure-stream-analytics-telcogenerator). 

    >[!NOTE]
    >Windows může blokovat ZIP staženého souboru. Pokud nelze rozbalit ho, klikněte pravým tlačítkem na soubor a vyberte **vlastnosti**. Pokud se zobrazí zpráva "Tento soubor pochází z jiného počítače a může být blokovaný k ochraně tohoto počítače", vyberte **Odblokovat** možnost a potom klikněte na tlačítko **použít**.

Pokud chcete prozkoumat výsledky úlohy Stream Analytics, musíte také nástroj pro zobrazení obsahu kontejneru úložiště objektů Blob v Azure. Pokud používáte sadu Visual Studio, můžete použít [nástroje Azure pro sadu Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) nebo [Visual Studio Cloud Explorer](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer). Alternativně můžete nainstalovat samostatných nástrojů, jako je [Průzkumníka služby Azure Storage](http://storageexplorer.com/) nebo [Průzkumníka služby Azure](http://www.cerebrata.com/products/azure-explorer/introduction). 

## <a name="create-an-azure-event-hubs-to-ingest-events"></a>Vytvoření Azure Event Hubs k ingestování událostí

Analyzovat datový proud, můžete *ingestování* ji do Azure. Typické způsob k ingestování dat je použití [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md), která umožňuje ingestovat miliony událostí za sekundu a následně zpracovat a ukládání informací o události. Pro účely tohoto kurzu vytvoříte Centrum událostí a pak je mít aplikaci generátoru událostí volání odešle data volání do tohoto centra událostí. Další informace o službě event hubs, najdete v článku [dokumentace ke službě Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).

>[!NOTE]
>Podrobnější verzi tohoto postupu, naleznete v části [vytvořit obor názvů služby Event Hubs a centra událostí pomocí webu Azure portal](../event-hubs/event-hubs-create.md). 

### <a name="create-a-namespace-and-event-hub"></a>Vytvořit obor názvů a Centrum událostí
V tomto postupu vytvoříte obor názvů centra událostí a pak přidáte do daného oboru názvů centra událostí. Obory názvů centra událostí se používají k logickému seskupení souvisejících událostí Service bus instancí. 

1. Přihlaste se k webu Azure portal a klikněte na tlačítko **vytvořit prostředek** > **Internet of Things** > **centra událostí**. 

2. V **vytvoření oboru názvů** podokně, zadejte název oboru názvů, jako například `<yourname>-eh-ns-demo`. Můžete použít libovolný název pro obor názvů, ale název musí být platnou adresu URL a musí být jedinečný v Azure. 
    
3. Vyberte předplatné a vytvořte nebo zvolte skupinu prostředků a potom klikněte na tlačítko **vytvořit**.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-namespace-new-portal.png" alt="drawing" width="300px"/>

4. Po dokončení nasazení oboru názvů najdete obor názvů centra událostí v seznamu prostředků Azure. 

5. Klikněte na nový obor názvů a v podokně oboru názvů klikněte na tlačítko **centra událostí**.

   ![Tlačítko Přidat Centrum událostí pro vytvoření nové Centrum událostí ](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-button-new-portal.png)    
 
6. Název nové Centrum událostí `asa-eh-frauddetection-demo`. Můžete použít jiný název. Pokud tak učiníte, si poznamenejte, protože je budete později potřebovat názvu. Není nutné nastavit další možnosti pro Centrum událostí hned teď.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-new-portal.png" alt="drawing" width="400px"/>
    
 
7. Klikněte na možnost **Vytvořit**.

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Udělení přístupu k centru událostí a získání připojovacího řetězce

Předtím, než se proces může odesílat data do centra událostí, musí mít Centrum událostí zásady, které povolí odpovídající přístup. Zásady přístupu vytváří připojovací řetězec, který obsahuje informace o autorizaci.

1.  V podokně oboru názvů událostí, klikněte na tlačítko **Event Hubs** a pak klikněte na název vaší nové Centrum událostí.

2.  V podokně centra událostí, klikněte na tlačítko **zásady sdíleného přístupu** a potom klikněte na tlačítko  **+ &nbsp;přidat**.

    >[!NOTE]
    >Ujistěte se, že pracujete s centrem událostí, ne obor názvů centra událostí.

3.  Přidání zásad s názvem `sa-policy-manage-demo` a **deklarace identity**vyberte **spravovat**.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-shared-access-policy-manage-new-portal.png" alt="drawing" width="300px"/>
 
4.  Klikněte na možnost **Vytvořit**.

5.  Po nasazení zásady, klikněte na něj v seznamu zásad sdíleného přístupu.

6.  Najít pole s popiskem **PŘIPOJOVACÍ řetězec – primární klíč** a klikněte na tlačítko kopírování vedle připojovacího řetězce. 

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-shared-access-policy-copy-connection-string-new-portal.png" alt="drawing" width="300px"/>
 
7.  Vložte připojovací řetězec do textového editoru. Tento připojovací řetězec budete potřebovat pro další části se po provedení některé malými úpravami.

    Připojovací řetězec vypadá takto:

        Endpoint=sb://YOURNAME-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=Gw2NFZwU1Di+rxA2T+6hJYAtFExKRXaC2oSQa0ZsPkI=;EntityPath=asa-eh-frauddetection-demo

    Všimněte si, že připojovací řetězec obsahuje více párů klíč hodnota oddělené středníky: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`, a `EntityPath`.  

## <a name="configure-and-start-the-event-generator-application"></a>Nakonfigurujte a spusťte aplikaci generátoru událostí

Před spuštěním aplikace TelcoGenerator, musíte ji nakonfigurovat tak, aby záznamy volání se odešle do centra událostí, které jste vytvořili.

### <a name="configure-the-telcogenerator-app"></a>Konfigurace aplikace TelcoGenerator

1.  V editoru, kterého jste zkopírovali připojovací řetězec, poznamenejte si `EntityPath` hodnotu a pak odeberte `EntityPath` pár (Nezapomeňte odebrat středník, který mu předchází). 

2.  Ve složce, kde odblokujte TelcoGenerator.zip soubor otevřete soubor telcodatagen.exe.config v editoru. (Existuje více než jeden soubor .config, proto se ujistěte, že otevíráte ten správný.)

3.  V `<appSettings>` element:

    * Nastavte hodnotu `EventHubName` klíče název centra událostí (to znamená, hodnotu cesta entity).
    * Nastavte hodnotu `Microsoft.ServiceBus.ConnectionString` na připojovací řetězec klíče. 

    `<appSettings>` Části bude vypadat jako v následujícím příkladu. (Pro přehlednost jsou zabaleny řádky a některé znaky se odstranily z autorizační token.)

   ![Konfigurační soubor aplikace TelcoGenerator zobrazující řetězec připojení a názvu centra událostí](./media/stream-analytics-real-time-fraud-detection/stream-analytics-telcogenerator-config-file-app-settings.png)
 
4.  Uložte soubor. 

### <a name="start-the-app"></a>Aplikace se spustila
1.  Otevřete okno příkazového řádku a přejděte do složky, ve kterém je aplikace TelcoGenerator rozbaleny.
2.  Zadejte následující příkaz:

        telcodatagen.exe 1000 0.2 2

    Parametry jsou: 

    * Počet disky CDR za hodinu. 
    * Pravděpodobnosti podvodů SIM karty: Jak často jako procento všech volání, aby měla aplikace simulovat podvodné volání. Hodnota 0,2 znamená, že přibližně 20 % záznamů volání bude falešných.
    * Doba trvání v hodinách. Počet hodin, které by měla spustit aplikace. Můžete také zastavit aplikaci kdykoli stisknutím kombinace kláves Ctrl + C v příkazovém řádku.

    Po několika sekundách aplikace začne zobrazovat záznamy telefonních hovorů na obrazovce, když je odešle do centra událostí.

Mezi klíčová pole, které budete používat v této aplikaci zjišťování možných podvodů v reálném čase patří následující:

|**Záznam**|**Definice**|
|----------|--------------|
|`CallrecTime`|Časové razítko pro počáteční čas volání. |
|`SwitchNum`|Telefonní ústředna použitá pro spojení volání. V tomto příkladu jsou ústředny řetězce, které představují zemi původu (USA, Čína, VB, Německo nebo Austrálie). |
|`CallingNum`|Telefonní číslo volajícího. |
|`CallingIMSI`|IMSI (International Mobile Subscriber Identity). Toto je jedinečný identifikátor volajícího. |
|`CalledNum`|Telefonní číslo příjemce volání. |
|`CalledIMSI`|IMSI (International Mobile Subscriber Identity). Toto je jedinečný identifikátor příjemce volání. |


## <a name="create-a-stream-analytics-job-to-manage-streaming-data"></a>Vytvoření úlohy Stream Analytics ke správě streamovaných dat

Teď, když máte stream událostí volání, můžete nastavit úlohu Stream Analytics. Úloha bude číst data z centra událostí, které jste nastavili. 

### <a name="create-the-job"></a>Vytvoření úlohy 

1. Na webu Azure Portal, klikněte na tlačítko **vytvořit prostředek** > **Internet of Things** > **úlohy Stream Analytics**.

2. Název úlohy `asa_frauddetection_job_demo`, určete předplatné, skupinu prostředků a umístění.

    Je vhodné umístit tato úloha a centra událostí ve stejné oblasti pro zajištění nejlepšího výkonu a tak, že neplatíte k přenosu dat mezi oblastmi.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-job-new-portal.png" alt="drawing" width="300px"/>

3. Klikněte na možnost **Vytvořit**.

    Vytvoření úlohy a na portálu se zobrazí podrobnosti o úloze. Neběží nic. ještě, i když – budete muset nakonfigurovat úlohu, než ho bude možné spustit.

### <a name="configure-job-input"></a>Konfigurace vstupu úlohy

1. Na řídicím panelu nebo **všechny prostředky** podokno, vyhledejte a vyberte `asa_frauddetection_job_demo` úlohy Stream Analytics. 
2. V **přehled** oddílu v podokně úlohy Stream Analytics, klikněte na tlačítko **vstup** pole.

   ![Vstupní pole v části topologie v podokně úlohy Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-input-box-new-portal.png)
 
3. Klikněte na tlačítko **přidat vstup streamu** a vyberte **centra událostí**. Poté zadejte nové vstupní stránky s následujícími informacemi:

   |**Nastavení**  |**Navrhovaná hodnota**  |**Popis**  |
   |---------|---------|---------|
   |Alias vstupu  |  CallStream   |  Zadejte název pro identifikaci vstupu úlohy.   |
   |Předplatné   |  \<Vaše předplatné\> |  Vyberte předplatné Azure, který má centra událostí, které jste vytvořili.   |
   |Obor názvů centra událostí  |  Azure Stream Analytics eh-ns – ukázka |  Zadejte název pro obor názvů centra událostí.   |
   |Název centra událostí  | Azure Stream Analytics eh – frauddetection – ukázka | Vyberte název vašeho centra událostí.   |
   |Název zásad centra událostí  | Azure Stream Analytics – zásady – Správa – ukázka | Vyberte zásady přístupu, který jste vytvořili dříve.   |
    </br>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-input-new-portal.png" alt="drawing" width="300px"/>


4. Klikněte na možnost **Vytvořit**.

## <a name="create-queries-to-transform-real-time-data"></a>Vytváření dotazů k transformaci dat v reálném čase

V tomto okamžiku máte úlohu Stream Analytics nastavit ke čtení příchozího datového streamu. Dalším krokem je vytvoření dotazu, která analyzuje data v reálném čase. Stream Analytics podporuje jednoduchý deklarativní dotazování modelu, který popisuje transformace pro zpracování v reálném čase. Dotazy využívají jazyce podobném SQL, který má některá rozšíření specifické pro Stream Analytics. 

Jednoduchý dotaz může jen číst všechna příchozí data. Ale často vytvořit dotazy, které vypadají pro konkrétní data nebo vztahy v datech. V této části kurzu vytvoříte a otestujete několik dotazů se dozvíte několik způsobů, jimiž můžete transformovat vstupní datový proud pro analýzy. 

Dotazy, které tady vytvoříte se zobrazí jenom Transformovaná data na obrazovku. V další části budete konfigurovat výstupní jímky a dotaz, který zapíše Transformovaná data do tohoto jímky.

Další informace o jazyku, najdete v článku [referenčních informacích k Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/dn834998.aspx).

### <a name="get-sample-data-for-testing-queries"></a>Načíst ukázková data pro účely testování dotazů

Aplikace TelcoGenerator záznamy volání odesílá do centra událostí a vaší úlohy Stream Analytics je nakonfigurovaná pro čtení z centra událostí. Dotaz můžete použít k otestování úlohy, abyste měli jistotu, že je správně čtení. K otestování dotazu v konzole Azure, potřebujete ukázková data. V tomto návodu budete extrahovat ukázková data z datového proudu, který přichází do centra událostí.

1. Ujistěte se, že je aplikace TelcoGenerator spuštěná a vytváří záznamy volání.
2. Na portálu vraťte se do v podokně úlohy Stream Analytics. (Pokud jste zavřeli podokně, vyhledejte `asa_frauddetection_job_demo` v **všechny prostředky** podokně.)
3. Klikněte na tlačítko **dotazu** pole. Azure zobrazuje vstupy a výstupy, které jsou nakonfigurovány pro úlohy a umožňuje vám vytvořit dotaz, který umožňuje transformovat vstupní datový proud, jako jsou odeslána do výstupu.
4. V **dotazu** podokně klikněte na tečky vedle `CallStream` vstup a potom vyberte **ukázková data ze vstupu**.

   ![Možnosti nabídky ukázková data pro položku projektu Stream Analytics, s "Ukázková data ze vstupu" vybraná](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sample-data-from-input.png)


5. Nastavte **minut** 3 a pak klikněte na tlačítko **OK**. 
    
   ![Možnosti vzorkování vstupního datového proudu s "3" – vybraný počet minut.](./media/stream-analytics-real-time-fraud-detection/stream-analytics-input-create-sample-data.png)

    Azure ukázky data ze vstupního datového proudu za 3 minuty a vás upozorní, když jsou ukázková data připravená. (To nějakou dobu trvá.) 

Ukázková data jsou dočasně uložena a jsou dostupná, dokud je otevřené okno dotazu. Pokud okno dotazu zavřete, budou ukázková data odstraněna a budete muset vytvořit novou sadu ukázkových dat. 

Jako alternativu můžete získat soubor .json, který obsahuje ukázková data [z Githubu](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json)a pak nahrajte tento soubor .json, který se použije jako ukázková data pro `CallStream` vstupu. 

### <a name="test-using-a-pass-through-query"></a>Testování pomocí předávací dotaz.

Pokud chcete archivovat každou událost, vám pomůže předávací dotaz. Přečtěte si všechna pole v datové části události.

1. V okně dotazu zadejte tento dotaz:

        SELECT 
            *
        FROM 
            CallStream

    >[!NOTE]
    >Stejně jako u SQL, klíčová slova nejsou velká a malá písmena a prázdný znak není důležité.

    V tomto dotazu `CallStream` je, že jste zadali při vytváření vstupní alias. Pokud jste použili jiný alias, použijte tento název.

2. Klikněte na tlačítko **Test**.

    Úlohy Stream Analytics spustí dotaz na ukázková data a zobrazí výstup v dolní části okna. Výsledky označuje, zda jsou správně nakonfigurovány centra událostí a úloha Stream Analytics. (Jak je uvedeno, později vytvoříte, který dotaz může zapisovat data do výstupní jímky.)

   ![Výstup úlohy Stream Analytics zobrazující 73 záznamů vygenerovaných](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output.png)

    Přesný počet záznamů, které se zobrazí bude záviset na tom, kolik záznamů se nezachytily ve vaší ukázce 3 minuty.
 
### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Snížit počet polí pomocí sloupce projekce

V mnoha případech se nemusí analýzy všechny sloupce ze vstupního datového proudu. Dotaz můžete použít k projekci menší sadu protokolovaných vrácené polí než v předávací dotaz.

1. Změňte dotaz v editoru kódu takto:

        SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum 
        FROM 
            CallStream

2. Klikněte na tlačítko **Test** znovu. 

   ![Stream Analytics výstup úlohy pro projekci se zobrazuje 25 záznamů vygenerované](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-projection.png)
 
### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Počet příchozích volání podle oblastí: aktivační událost pro Přeskakující okno s průběhem

Předpokládejme, že budete chtít zjistit počet příchozích volání v jedné oblasti. V streamovaných dat, pokud chcete provádět agregační funkce, jako je další přibývají, je potřeba rozdělit datový proud do dočasné jednotky (protože samotný datový proud je efektivně nekonečné). Můžete to provést pomocí Stream Analytics [oddílovou funkci](stream-analytics-window-functions.md). Pak můžete pracovat s daty v tomto okně jako celek.

Pro tuto transformaci chcete posloupnost dočasné windows, které se nepřekrývají – každé okno bude mít samostatnou sadu dat, která můžete seskupit a agregovat. Tento typ okna se označuje jako *aktivační událost pro Přeskakující okno*. V rámci aktivační událost pro Přeskakující okno, můžete získat počet příchozích volání seskupené podle `SwitchNum`, která představuje zemi, ve kterém bylo volání provedeno. 

1. Změňte dotaz v editoru kódu takto:

        SELECT 
            System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
        FROM
            CallStream TIMESTAMP BY CallRecTime 
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum

    Tento dotaz používá `Timestamp By` – klíčové slovo v `FROM` klauzule, která určíte, které pole časového razítka v vstupního datového proudu k definování aktivační událost pro Přeskakující okno. V tomto případě okno rozděluje data do segmentů podle `CallRecTime` v záznamech. (Pokud není zadána žádná pole, operace oddílová používá čas, který každé události dorazí na Centrum událostí. Naleznete v části "Čas aplikace Vs čas doručení" v [Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx). 

    Projekce zahrnuje `System.Timestamp`, který vrátí časové razítko na konci každé okno. 

    Chcete-li určit, že chcete použít aktivační událost pro Přeskakující okno, můžete použít [TUMBLINGWINDOW](https://msdn.microsoft.com/library/dn835055.aspx) fungovat v `GROUP BY `klauzuli. Ve funkci zadejte časovou jednotku (od úrovni mikrosekund na den) a velikost okna (kolik jednotek). V tomto příkladu aktivační událost pro Přeskakující okno se skládá z 5 intervalech, proto se zobrazí počet podle země pro každých 5 sekund za volání.

2. Klikněte na tlačítko **Test** znovu. Ve výsledcích, Všimněte si, že časová razítka v rámci **WindowEnd** jsou v přírůstcích po 5 sekund.

   ![Stream Analytics výstup úlohy pro agregaci, zobrazuje 13 záznamů vygenerované](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-aggregation.png)
 
### <a name="detect-sim-fraud-using-a-self-join"></a>Odhalování podvodů SIM pomocí spojení sama na sebe

V tomto příkladu Zvažte podvodné použití Chcete-li být volání, které pocházejí ze stejného uživatele, ale v různých umístěních v rámci 5 sekund mezi sebou. Například stejný uživatel nemůže legitimně uskutečnit volání z USA a Austrálie současně. 

Ke kontrole pro tyto případy, můžete použít vlastní spojení streamovaná data k datový proud na sebe sama na základě `CallRecTime` hodnotu. Pak vyhledejte volání záznamy, jejichž `CallingIMSI` hodnota (číslo volajícího) je stejná, ale `SwitchNum` hodnotu (země původu) se neshoduje s.

Použijete-li spojení se streamovanými daty, musí spojení určit, že některá omezení, jak daleko odpovídající řádky je možné oddělit v čase. (Jak je uvedeno výše, streamování dat je efektivně nekonečné.) Jsou časové hranice pro relaci je zadat v rámci `ON` klauzuli spojení, pomocí `DATEDIFF` funkce. V tomto případě připojení je založené na 5 sekund intervalu dat volání.

1. Změňte dotaz v editoru kódu takto: 

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

    Tento dotaz je stejně jako všechny SQL připojení s výjimkou `DATEDIFF` funkce ve spojení. Tato verze `DATEDIFF` je specifická pro Streaming Analytics, a musí být uvedena v `ON...BETWEEN` klauzuli. Parametry jsou jednotky doby (v sekundách v tomto příkladu) a aliasy dva zdroje pro spojení. Tím se liší od standardní SQL `DATEDIFF` funkce.

    `WHERE` Klauzule obsahuje podmínku, která označí podvodných volání: původní přepínače nejsou stejné. 

2. Klikněte na tlačítko **Test** znovu. 

   ![Výstup úlohy Stream Analytics pro spojení sama na sebe, zobrazení 6 záznamy vygenerované](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-self-join.png)

3. Klikněte na tlačítko **Uložit** Uložte dotaz spojení sama na sebe jako součást úlohy Stream Analytics. (Ho neukládá ukázková data.)

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-save-button-new-portal.png" alt="drawing" width="300px"/>

## <a name="create-an-output-sink-to-store-transformed-data"></a>Vytvoření výstupní jímku pro uložení transformovaných dat

Jste definovali datového proudu událostí, Centrum událostí vstupu pro ingestování událostí a dotaz, který provádí transformaci u datového proudu. Posledním krokem je definování výstupní jímky pro úlohu – to znamená, místo, kam můžete napsat transformovaný datový proud do. 

Mnoho prostředků můžete použít jako výstupní jímky – databáze serveru SQL, table storage, úložiště Data Lake, Power BI a dokonce pro jiný centra událostí. Pro účely tohoto kurzu zapíšete datový proud do Azure Blob Storage, což je typické volbou pro shromažďování informací o událostech pro pozdější analýzu, protože obsáhne Nestrukturovaná data.

Pokud máte existující účet úložiště objektů blob, můžete použít, který. V tomto kurzu se dozvíte, jak vytvořit nový účet úložiště.

### <a name="create-an-azure-blob-storage-account"></a>Vytvoření účtu úložiště objektů Blob v Azure

1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek** > **Úložiště** > **Účet úložiště**. Vyplňte stránky úlohy účet úložiště s **název** nastavena na "asaehstorage" **umístění** nastavena na "Východ USA", **skupiny prostředků** nastavena na "Azure Stream Analytics eh-ns-rg" (hostitele v účtu úložiště stejnou skupinu prostředků jako úloha streamování pro zajištění zvýšeného výkonu). Ostatní nastavení můžou zůstat na výchozích hodnotách.  

   ![Vytvoření účtu úložiště](./media/stream-analytics-real-time-fraud-detection/stream-analytics-storage-account-create.png)

2. Na webu Azure Portal vraťte se do podokna úloh Stream Analytics. (Pokud jste zavřeli podokně, vyhledejte `asa_frauddetection_job_demo` v **všechny prostředky** podokně.)

3. V **topologie úlohy** klikněte na tlačítko **výstup** pole.

4. V **výstupy** podokně klikněte na tlačítko **přidat** a vyberte **úložiště objektů Blob**. Pak zadejte novou stránku výstup s následujícími informacemi:

   |**Nastavení**  |**Navrhovaná hodnota**  |**Popis**  |
   |---------|---------|---------|
   |Alias pro výstup  |  CallStream Podvodnávolání   |  Zadejte název pro identifikaci výstupu úlohy.   |
   |Předplatné   |  \<Vaše předplatné\> |  Zadejte předplatné Azure vytvořeného účtu úložiště. Účet úložiště můžete využívat v rámci stejného, ale i jiného předplatného. V tomto příkladu se předpokládá, že jste účet vytvořili v rámci stejného předplatného. |
   |Účet úložiště  |  asaehstorage |  Zadejte název účtu úložiště, který jste vytvořili. |
   |Kontejner  | Azure Stream Analytics – podvodnávolání – ukázka | Zvolte možnost vytvořit novou a zadejte název kontejneru. |
    <br/>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-output-blob-storage-new-console.png" alt="drawing" width="300px"/>
    
5. Klikněte na **Uložit**. 


## <a name="start-the-streaming-analytics-job"></a>Spuštění úlohy Stream Analytics

Úloha je nyní nakonfigurována. Jste zadali vstup (centra událostí) a transformace (dotaz a hledat podvodná volání), výstup (úložiště objektů blob). Nyní můžete spustit úlohu. 

1. Ujistěte se, že je aplikace TelcoGenerator spuštěná.

2. V podokně úloh klikněte na tlačítko **Start**. V **spuštění úlohy** podokno pro výstupní čas spuštění úlohy, vyberte **nyní**. 

   ![Spuštění úlohy Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start.png)



## <a name="examine-the-transformed-data"></a>Prozkoumejte transformovaných dat

Teď máte kompletní úlohy Stream Analytics. Úloha je zkoumání datový proud metadat telefonního hovoru, hledají podvodných volání v reálném čase a zápisu informací o těchto podvodných volání do úložiště. 

K dokončení tohoto kurzu, můžete chtít podívat na data, zachytí úlohy Stream Analytics. Data nepsalo se do Azure Blog Storage v blocích (soubory). Můžete použít jakýkoli nástroj, který čte úložiště objektů Blob v Azure. Jak je uvedeno v části požadavky, můžete použít rozšíření Azure v sadě Visual Studio, nebo můžete použít nástroje, jako je [Průzkumníka služby Azure Storage](http://storageexplorer.com/) nebo [Průzkumníka služby Azure](http://www.cerebrata.com/products/azure-explorer/introduction). 

Při zkoumání obsah souboru v úložišti objektů blob, se zobrazí přibližně takto:

   ![Azure blob storage s využitím analýzy datových proudů výstupu](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-blob-storage-view.png)
 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Existují další články, zjišťování možných podvodů scénáře, které používají prostředky, které jste vytvořili v tomto kurzu. Pokud chcete pokračovat, přečtěte si téma návrhů v části **další kroky**.

Ale pokud to uděláte a nepotřebujete prostředky, které jste vytvořili, můžete je odstranit tak, aby se vám neúčtovaly zbytečné poplatky za Azure. V takovém případě doporučujeme, abyste udělali toto:

1. Po zastavení úlohy Stream Analytics. V **úlohy** podokně klikněte na tlačítko **Zastavit** v horní části.
2. Zastavit Telco aplikaci. V příkazovém okně, ve kterém jste spustili aplikaci stiskněte kombinaci kláves Ctrl + C.
3. Pokud jste vytvořili nový účet úložiště blob jenom pro účely tohoto kurzu, odstraňte ho. 
4. Odstraňte úlohu Stream Analytics.
5. Odstraňte Centrum událostí.
6. Odstraňte obor názvů centra událostí.

## <a name="get-support"></a>Získat podporu

Potřebujete další pomoc, zkuste [fóru Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup

Můžete pokračovat v tomto kurzu se v následujícím článku:

* [Stream Analytics a Power BI: řídicí panel analýzy v reálném čase pro streamovaná data](stream-analytics-power-bi-dashboard.md). Tento článek ukazuje, jak odeslat TelCo výstup úlohy Stream Analytics pro Power BI pro vizualizace v reálném čase a analýzy.

Další informace o Stream Analytics obecně platí, najdete v těchto článcích:

* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
