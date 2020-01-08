---
title: Zjišťování podvodů v reálném čase pomocí Azure Stream Analytics
description: Naučte se vytvářet řešení pro detekci podvodů v reálném čase pomocí Stream Analytics. Použijte centrum událostí pro zpracování událostí v reálném čase.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 168f11e82305a0e08923289e71ae6ea0d36c1734
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75458791"
---
# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Začínáme používat Azure Stream Analytics: zjišťování podvodů v reálném čase

Tento kurz nabízí ucelenou ukázku použití Azure Stream Analytics. Získáte informace o těchto tématech: 

* Přeneste události streamování do instance Azure Event Hubs. V tomto kurzu použijete aplikaci, která simuluje Stream záznamů metadat mobilního telefonu.

* Psaní dotazů Stream Analytics, jako jsou například dotazy, pro transformaci dat, agregaci informací nebo hledání vzorů. Uvidíte, jak použít dotaz k prohlédnutí příchozího datového proudu a hledání volání, která mohou být podvodné.

* Odešlete výsledky do výstupní jímky (úložiště), kterou můžete analyzovat pro další přehledy. V takovém případě odešlete podezřelá data do služby Azure Blob Storage.

V tomto kurzu se používá příklad zjišťování podvodů v reálném čase v závislosti na datech telefonního hovoru. Tato technika je vhodná i pro jiné typy odhalování podvodů, jako je podvod kreditních karet nebo krádež identity. 

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Scénář: Detekce podvodů v odvětví telekomunikací a SIM v reálném čase

Telekomunikační společnost má velký objem dat pro příchozí hovory. Společnost chce detekovat podvodné hovory v reálném čase, aby mohli informovat zákazníky nebo ukončit službu pro konkrétní číslo. Jeden z typů podvodů na kartě SIM zahrnuje několik volání ze stejné identity kolem stejné doby, ale v geograficky různých umístěních. Aby bylo možné zjistit tento typ podvodů, společnost potřebuje prošetřit příchozí telefonní záznamy a vyhledat konkrétní vzory – v tomto případě pro volání v různých zemích nebo oblastech v rámci stejné doby. Všechny záznamy telefonů, které spadají do této kategorie, jsou zapsány do úložiště pro následnou analýzu.

## <a name="prerequisites"></a>Požadavky

V tomto kurzu simulujete data telefonního hovoru pomocí klientské aplikace, která generuje ukázková metadata telefonního hovoru. Některé záznamy, které aplikace vytváří, vypadají jako podvodné hovory. 

Než začnete, ujistěte se, že jste provedli následující akce:

* Účet Azure.
* Aplikace generátoru událostí volání ( [TelcoGenerator. zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip)), kterou je možné stáhnout z webu Microsoft Download Center. Rozbalí tento balíček do složky ve vašem počítači. Pokud chcete zobrazit zdrojový kód a spustit aplikaci v ladicím programu, můžete získat zdrojový kód aplikace z [GitHubu](https://aka.ms/azure-stream-analytics-telcogenerator). 

    >[!NOTE]
    >Systém Windows může blokovat stažený soubor. zip. Pokud ho nemůžete rozbalit, klikněte pravým tlačítkem na soubor a vyberte **vlastnosti**. Pokud se zobrazí zpráva "Tento soubor pochází z jiného počítače a může být blokovaný pro lepší ochranu tohoto počítače", vyberte možnost **odblokovat** a klikněte na **použít**.

Pokud chcete prošetřit výsledky úlohy Stream Analytics, budete také potřebovat nástroj pro zobrazení obsahu kontejneru Azure Blob Storage. Pokud používáte Visual Studio, můžete použít [nástroje Azure pro Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) nebo [Visual Studio Cloud Explorer](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer). Alternativně můžete nainstalovat samostatné nástroje, jako je [Průzkumník služby Azure Storage](https://storageexplorer.com/) nebo [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

## <a name="create-an-azure-event-hubs-to-ingest-events"></a>Vytvoření Event Hubs Azure pro ingestování událostí

Pokud chcete analyzovat datový proud, ingestujte *ho do* Azure. Typický způsob, jak ingestovat data, je použít [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md), který umožňuje ingestovat miliony událostí za sekundu a pak zpracovávat a ukládat informace o událostech. V tomto kurzu vytvoříte centrum událostí a potom budete mít aplikaci generátoru událostí volání poslat data volání do tohoto centra událostí. Další informace o centrech událostí najdete v [dokumentaci k Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).

>[!NOTE]
>Podrobnější verzi tohoto postupu najdete v tématu [Vytvoření oboru názvů Event Hubs a centra událostí pomocí Azure Portal](../event-hubs/event-hubs-create.md). 

### <a name="create-a-namespace-and-event-hub"></a>Vytvořit obor názvů a Centrum událostí
V tomto postupu nejprve vytvoříte obor názvů centra událostí a pak přidáte centrum událostí do tohoto oboru názvů. Obory názvů centra událostí se používají k logickému seskupení souvisejících instancí sběrnice událostí. 

1. Přihlaste se k Azure Portal a klikněte na **vytvořit prostředek** > **Internet věcí** > **centrum událostí**. 

2. V podokně **vytvořit obor názvů** zadejte název oboru názvů, například `<yourname>-eh-ns-demo`. Můžete použít libovolný název oboru názvů, ale název musí být platný pro adresu URL a musí být jedinečný v rámci Azure. 
    
3. Vyberte předplatné a vytvořte nebo zvolte skupinu prostředků a pak klikněte na **vytvořit**.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-namespace-new-portal.png" alt="Create event hub namespace in Azure portal" width="300px"/>

4. Po dokončení nasazení oboru názvů vyhledejte v seznamu prostředků Azure obor názvů centra událostí. 

5. Klikněte na nový obor názvů a v podokně obor názvů klikněte na **centrum událostí**.

   ![Tlačítko Přidat centrum událostí pro vytvoření nového centra událostí](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-button-new-portal.png)    
 
6. Pojmenujte nové `asa-eh-frauddetection-demo`centra událostí. Můžete použít jiný název. Pokud to uděláte, poznamenejte si ho, protože ho budete potřebovat později. Pro centrum událostí teď nemusíte nastavovat žádné další možnosti.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-new-portal.png" alt="Name event hub in Azure portal" width="400px"/>
    
 
7. Klikněte na **Vytvořit**.

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Udělení přístupu k centru událostí a získání připojovacího řetězce

Předtím, než může proces odesílat data do centra událostí, musí mít centrum událostí zásadu, která umožňuje odpovídající přístup. Zásady přístupu vytváří připojovací řetězec, který obsahuje informace o autorizaci.

1.  V podokně obor názvů událostí klikněte na **Event Hubs** a pak klikněte na název nového centra událostí.

2.  V podokně centra událostí klikněte na **zásady sdíleného přístupu** a pak klikněte na **+&nbsp;přidat**.

    >[!NOTE]
    >Ujistěte se, že pracujete s centrem událostí, nikoli s oborem názvů centra událostí.

3.  Přidejte zásadu nazvanou `sa-policy-manage-demo` a pro **deklaraci identity**vyberte **Spravovat**.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-shared-access-policy-manage-new-portal.png" alt="Create shared access policy for Stream Analytics" width="300px"/>
 
4.  Klikněte na **Vytvořit**.

5.  Po nasazení zásady klikněte na ni v seznamu zásad sdíleného přístupu.

6.  Vyhledejte box s názvem **připojovací řetězec – primární klíč** a klikněte na tlačítko Kopírovat vedle připojovacího řetězce. 

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-shared-access-policy-copy-connection-string-new-portal.png" alt="Stream Analytics shared access policy" width="300px"/>
 
7.  Vložte připojovací řetězec do textového editoru. Tento připojovací řetězec budete potřebovat pro další část, a to po provedení některých malých úprav.

    Připojovací řetězec vypadá takto:

        Endpoint=sb://YOURNAME-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=Gw2NFZwU1Di+rxA2T+6hJYAtFExKRXaC2oSQa0ZsPkI=;EntityPath=asa-eh-frauddetection-demo

    Všimněte si, že připojovací řetězec obsahuje několik párů klíč-hodnota oddělených středníky: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`a `EntityPath`.  

## <a name="configure-and-start-the-event-generator-application"></a>Konfigurace a spuštění aplikace generátoru událostí

Před spuštěním aplikace TelcoGenerator je nutné ji nakonfigurovat tak, aby odesílala záznamy volání do centra událostí, které jste vytvořili.

### <a name="configure-the-telcogenerator-app"></a>Konfigurace aplikace TelcoGenerator

1. V editoru, kam jste zkopírovali připojovací řetězec, poznamenejte si hodnotu `EntityPath` a pak odeberte dvojici `EntityPath` (Nezapomeňte odebrat středník, který ho předchází). 

2. Ve složce, ve které jste rozTelcoGeneratori soubor. zip, otevřete soubor telcodatagen. exe. config v editoru. (Existuje více než jeden soubor. config, proto nezapomeňte otevřít ten správný.)

3. V elementu `<appSettings>`:

   * Nastavte hodnotu klíče `EventHubName` na název centra událostí (tj. na hodnotu v cestě k entitě).
   * Nastavte hodnotu `Microsoft.ServiceBus.ConnectionString` klíč na připojovací řetězec. 

   Oddíl `<appSettings>` bude vypadat jako v následujícím příkladu. (Pro přehlednost jsou řádky zabaleny a některé znaky byly z autorizačního tokenu odebrány.)

   ![Konfigurační soubor TelcoGenerator zobrazuje název a připojovací řetězec centra událostí.](./media/stream-analytics-real-time-fraud-detection/stream-analytics-telcogenerator-config-file-app-settings.png)
 
4. Uložte soubor. 

### <a name="start-the-app"></a>Spuštění aplikace
1.  Otevřete příkazové okno a přejděte do složky, ve které je aplikace TelcoGenerator nekomprimovaná.

2.  Zadejte následující příkaz:

   ```cmd
   telcodatagen.exe 1000 0.2 2
   ```

   Parametry jsou: 

   * Počet CDRs za hodinu 
   * Pravděpodobnost podvodů s kartami SIM: jak často, jako procento všech volání by měla aplikace simulovat podvodné volání. Hodnota 0,2 znamená, že přibližně 20 % záznamů volání bude falešných.
   * Doba trvání v hodinách Počet hodin, po které by měla aplikace běžet. Aplikaci můžete kdykoli zastavit stisknutím kombinace kláves CTRL + C na příkazovém řádku.

   Po několika sekundách aplikace začne zobrazovat záznamy telefonních hovorů na obrazovce, když je odešle do centra událostí.

Některá klíčová pole, která budete používat v této aplikaci pro detekci podvodů v reálném čase, jsou následující:

|**Záznam**|**Definice**|
|----------|--------------|
|`CallrecTime`|Časové razítko pro počáteční čas volání. |
|`SwitchNum`|Telefonní ústředna použitá pro spojení volání. V tomto příkladu jsou přepínače řetězce reprezentující zemi nebo oblast původu (USA, Čína, Spojené království, Německo nebo Austrálie). |
|`CallingNum`|Telefonní číslo volajícího. |
|`CallingIMSI`|IMSI (International Mobile Subscriber Identity). Toto je jedinečný identifikátor volajícího. |
|`CalledNum`|Telefonní číslo příjemce volání. |
|`CalledIMSI`|IMSI (International Mobile Subscriber Identity). Toto je jedinečný identifikátor příjemce volání. |


## <a name="create-a-stream-analytics-job-to-manage-streaming-data"></a>Vytvoření úlohy Stream Analytics pro správu streamovaná data

Teď, když máte proud událostí volání, můžete nastavit Stream Analytics úlohu. Tato úloha načte data z centra událostí, které jste nastavili. 

### <a name="create-the-job"></a>Vytvoření úlohy 

1. V Azure Portal klikněte na **vytvořit prostředek** > **Internet věcí** **úlohu > Stream Analytics**.

2. Pojmenujte úlohu `asa_frauddetection_job_demo`zadejte předplatné, skupinu prostředků a umístění.

    Je vhodné umístit úlohu a centrum událostí ve stejné oblasti, aby se co nejlépe vyzpůsobilo, a nebudete platit za přenos dat mezi oblastmi.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-job-new-portal.png" alt="Create Stream Analytics job in portal" width="300px"/>

3. Klikněte na **Vytvořit**.

    Úloha se vytvoří a na portálu se zobrazí podrobnosti o úloze. Nic se ještě nepoužívá, ale je potřeba nejdřív nakonfigurovat úlohu, aby ji bylo možné spustit.

### <a name="configure-job-input"></a>Konfigurace vstupu úlohy

1. Na řídicím panelu nebo v podokně **všechny prostředky** vyhledejte a vyberte úlohu `asa_frauddetection_job_demo` Stream Analytics. 
2. V části **Přehled** v podokně Stream Analytics úlohy klikněte na **vstupní** pole.

   ![Vstupní pole pod topologií v podokně úlohy Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-input-box-new-portal.png)
 
3. Klikněte na **Přidat vstup streamu** a vyberte **centrum událostí**. Pak vyplňte novou vstupní stránku následujícími informacemi:

   |**Nastavení**  |**Navrhovaná hodnota**  |**Popis**  |
   |---------|---------|---------|
   |Alias vstupu  |  CallStream   |  Zadejte název pro identifikaci vstupu úlohy.   |
   |Předplatné   |  \<Vaše předplatné\> |  Vyberte předplatné Azure, které obsahuje centrum událostí, které jste vytvořili.   |
   |Obor názvů centra událostí  |  asa-eh-ns-demo |  Zadejte název oboru názvů centra událostí.   |
   |Název centra událostí  | asa-eh-frauddetection-demo | Vyberte název centra událostí.   |
   |Název zásad centra událostí  | ASA – zásady-Správa-ukázka | Vyberte zásadu přístupu, kterou jste vytvořili dříve.   |

    </br>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-input-new-portal.png" alt="Create Stream Analytics input in portal" width="300px"/>


4. Klikněte na **Vytvořit**.

## <a name="create-queries-to-transform-real-time-data"></a>Vytváření dotazů pro transformaci dat v reálném čase

V tomto okamžiku máte nastavenou úlohu Stream Analytics pro čtení příchozího datového proudu. Dalším krokem je vytvoření dotazu, který analyzuje data v reálném čase. Stream Analytics podporuje jednoduchý deklarativní model dotazu, který popisuje transformace pro zpracování v reálném čase. Dotazy používají jazyk podobný jazyku SQL, který obsahuje některá rozšíření specifická pro Stream Analytics. 

Jednoduchý dotaz může jednoduše číst všechna příchozí data. Často ale vytváříte dotazy, které vyhledávají konkrétní data nebo relace v datech. V této části kurzu vytvoříte a otestujete několik dotazů, abyste se dozvěděli několik způsobů, jak transformovat vstupní datový proud k analýze. 

Dotazy, které tady vytvoříte, budou jenom zobrazovat transformovaná data na obrazovku. V pozdější části nakonfigurujete výstupní jímku a dotaz, který zapisuje transformovaná data do této jímky.

Další informace o tomto jazyce najdete v referenčních informacích k jazyku [Azure Stream Analytics dotazů](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

### <a name="get-sample-data-for-testing-queries"></a>Získat ukázková data pro testovací dotazy

Aplikace TelcoGenerator odesílá záznamy volání do centra událostí a vaše úloha Stream Analytics je nakonfigurována pro čtení z centra událostí. K otestování úlohy můžete použít dotaz, abyste se ujistili, že je správně čtena. K otestování dotazu v konzole Azure budete potřebovat ukázková data. V tomto návodu extrahujete ukázková data z datového proudu, který přichází do centra událostí.

1. Ujistěte se, že aplikace TelcoGenerator běží a vytváří záznamy volání.
2. Na portálu se vraťte do podokna úlohy Stream Analytics. (Pokud jste podokno zavřeli, vyhledejte `asa_frauddetection_job_demo` v podokně **všechny prostředky** .)
3. Klikněte na pole **dotazu** . Azure obsahuje seznam vstupů a výstupů nakonfigurovaných pro úlohu a umožňuje vytvořit dotaz, který vám umožní transformovat vstupní datový proud při odeslání do výstupu.
4. V podokně **dotazu** klikněte na tečky vedle `CallStream`ho vstupu a pak vyberte **vzorová data ze vstupu**.

   ![Možnosti nabídky pro použití ukázkových dat pro položku úlohy Stream Analytics s vybranou možností ukázková data ze vstupu](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sample-data-from-input.png)


5. Nastavte **minuty** na 3 a pak klikněte na **OK**. 
    
   ![Možnosti vzorkování vstupního datového proudu se zvolenými 3 minutami](./media/stream-analytics-real-time-fraud-detection/stream-analytics-input-create-sample-data.png)

    Ukázky Azure 3 minuty v hodnotě dat ze vstupního datového proudu a upozorní vás, až budou ukázková data připravena. (To trvá krátce.) 

Ukázková data jsou dočasně uložena a jsou dostupná, dokud je otevřené okno dotazu. Pokud okno dotazu zavřete, budou ukázková data odstraněna a budete muset vytvořit novou sadu ukázkových dat. 

Alternativně můžete získat soubor. JSON, který obsahuje ukázková data [z GitHubu](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json), a pak nahrát tento soubor. JSON, který se použije jako vzorová data pro vstup `CallStream`. 

### <a name="test-using-a-pass-through-query"></a>Testování pomocí předávacího dotazu

Pokud chcete archivovat každou událost, můžete použít předávací dotaz ke čtení všech polí v datové části události.

1. V okně dotazu zadejte tento dotaz:
        
   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >Stejně jako u jazyka SQL nejsou klíčová slova rozlišovat velká a malá písmena a prázdné znaky nejsou významné.

    V tomto dotazu je `CallStream` alias, který jste zadali při vytváření vstupu. Pokud jste použili jiný alias, použijte místo něj tento název.

2. Klikněte na tlačítko **test**.

    Úloha Stream Analytics spustí dotaz proti ukázkovým datům a zobrazí výstup v dolní části okna. Výsledky indikují, že centrum událostí a úloha analýzy streamování jsou správně nakonfigurované. (Jak je uvedeno, později vytvoříte výstupní jímku, do které dotaz může zapisovat data.)

   ![Výstup úlohy Stream Analytics, který zobrazuje 73 vygenerovaných záznamů](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output.png)

    Přesný počet záznamů, které vidíte, bude záviset na počtu záznamů, které byly zachyceny ve vaší ukázce 3 minut.
 
### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Snížení počtu polí pomocí projekce sloupce

V mnoha případech vaše analýza nepotřebuje všechny sloupce ze vstupního streamu. Dotaz můžete použít pro projekt menší sady vrácených polí než v předávacím dotazu.

1. Změňte dotaz v editoru kódu na následující:

   ```SQL
   SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum 
   FROM 
       CallStream
   ```

2. Znovu klikněte na tlačítko **test** . 

   ![Výstup úlohy Stream Analytics pro projekci zobrazuje 25 záznamů](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-projection.png)
 
### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Počet příchozích volání podle oblasti: okno bubnu s agregací

Předpokládejme, že chcete spočítat počet příchozích volání na oblast. V případě streamování dat, pokud chcete provádět agregační funkce, jako je počítání, potřebujete segmentovat datový proud na dočasné jednotky (protože samotný datový proud je efektivně nekonečný). Provedete to pomocí [funkce okna](stream-analytics-window-functions.md)Stream Analytics. Pak můžete pracovat s daty v tomto okně jako s jednotkou.

Pro tuto transformaci budete chtít sekvenci dočasných oken, která se nepřekrývají – každé okno bude mít diskrétní sadu dat, která můžete seskupit a agregovat. Tento typ okna se označuje jako *bubnové okno*. V okně bubnu můžete získat počet příchozích volání seskupených podle `SwitchNum`, který představuje zemi nebo oblast, ve které volání vzniklo. 

1. Změňte dotaz v editoru kódu na následující:

        ```SQL
        SELECT 
            System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
        FROM
            CallStream TIMESTAMP BY CallRecTime 
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
        ```

    Tento dotaz pomocí klíčového slova `Timestamp By` v klauzuli `FROM` určuje, které pole časového razítka ve vstupním datovém proudu má být použito k definování bubnového okna. V tomto případě okno rozdělí data do segmentů podle pole `CallRecTime` v každém záznamu. (Pokud není zadané žádné pole, bude operace oken používat čas, kdy každá událost dorazí do centra událostí. Viz část "doba doručení vs – čas použití aplikace" v tématu [referenční informace k jazyku pro dotaz Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). 

    Projekce obsahuje `System.Timestamp`, která vrací časové razítko pro konec každého okna. 

    Chcete-li určit, že chcete použít okno bubnu, použijte funkci [TUMBLINGWINDOW](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) v klauzuli `GROUP BY`. Ve funkci zadáte časovou jednotku (kdekoliv od sekundy do dne) a velikost okna (počet jednotek). V tomto příkladu se bubnové okno skládá z 5 sekund, takže pro každé 5 sekund se získá počet podle země/oblasti.

2. Znovu klikněte na tlačítko **test** . Ve výsledcích si všimněte, že časová razítka v rámci **WindowEnd** jsou v přírůstcích 5 sekund.

   ![Výstup úlohy Stream Analytics pro agregaci zobrazující 13 záznamů](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-aggregation.png)
 
### <a name="detect-sim-fraud-using-a-self-join"></a>Zjišťování podvodů SIM pomocí automatického spojení

V tomto příkladu zvažte podvodné použití volání, která pocházejí od stejného uživatele, ale v různých umístěních do 5 sekund od sebe. Například stejný uživatel nemůže legitimně uskutečnit volání z USA a Austrálie současně. 

Chcete-li tyto případy vyhledat, můžete k připojení ke streamu na základě hodnoty `CallRecTime` použít samoobslužné spojení dat streamování. Potom můžete vyhledat záznamy volání, kde hodnota `CallingIMSI` (původní číslo) je stejná, ale hodnota `SwitchNum` (země/oblast původu) není shodná.

Použijete-li spojení s datovými proudy, je nutné, aby spojení poskytovalo určité meze, jak daleko může být srovnávací řádky odděleny v čase. (Jak bylo uvedeno dříve, streamovaná data jsou efektivně nekonečna.) Časová omezení pro relaci jsou uvedena v klauzuli `ON` spojení pomocí funkce `DATEDIFF`. V takovém případě se spojení vychází z intervalu 5 sekund volání dat.

1. Změňte dotaz v editoru kódu na následující: 

        ```SQL
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
        ```

    Tento dotaz je stejný jako jakékoli spojení SQL s výjimkou funkce `DATEDIFF` ve spojení. Tato verze `DATEDIFF` je specifická pro Stream Analytics a musí se objevit v klauzuli `ON...BETWEEN`. Parametry jsou časovou jednotkou (sekundy v tomto příkladu) a aliasy dvou zdrojů pro spojení. To se liší od standardní funkce `DATEDIFF` SQL.

    Klauzule `WHERE` obsahuje podmínku, která označuje podvodný hovor: původní přepínače nejsou stejné. 

2. Znovu klikněte na tlačítko **test** . 

   ![Výstup úlohy Stream Analytics pro samoobslužné spojení, což zobrazuje 6 generovaných záznamů](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-self-join.png)

3. Kliknutím na **Uložit** Uložte dotaz na sebe v rámci úlohy Stream Analytics. (Ukázková data neuloží.)

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-save-button-new-portal.png" alt="Save Stream Analytics query in portal" width="300px"/>

## <a name="create-an-output-sink-to-store-transformed-data"></a>Vytvoření výstupní jímky pro ukládání transformovaných dat

Definovali jste datový proud událostí, vstup centra událostí pro ingestování událostí a dotaz k provedení transformace v datovém proudu. Posledním krokem je definování výstupní jímky pro úlohu – to znamená místo pro zápis transformačního streamu do. 

Můžete použít mnoho prostředků jako výstupní jímky – SQL Server databázi, úložiště tabulek, Data Lake úložiště, Power BI a dokonce i další centra událostí. Pro účely tohoto kurzu zapíšete datový proud do Azure Blob Storage, což je typická volba pro shromažďování informací o událostech pro pozdější analýzu, protože vyhovuje nestrukturovaným datům.

Pokud máte existující účet Blob Storage, můžete ho použít. V tomto kurzu se naučíte, jak vytvořit nový účet úložiště.

### <a name="create-an-azure-blob-storage-account"></a>Vytvoření účtu Azure Blob Storage

1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek** > **Úložiště** > **Účet úložiště**. Vyplňte stránku úlohy účtu úložiště s **názvem** nastaveným na "asaehstorage", **umístění** nastaveným na "východní USA", **skupinu prostředků** nastavenou na "ASA-eh-NS-RG" (jako hostitele účtu úložiště ve stejné skupině prostředků jako úlohu streamování pro vyšší výkon). Ostatní nastavení můžou zůstat na výchozích hodnotách.  

   ![Vytvořit účet úložiště v Azure Portal](./media/stream-analytics-real-time-fraud-detection/stream-analytics-storage-account-create.png)

2. V Azure Portal se vraťte do podokna úlohy Stream Analytics. (Pokud jste podokno zavřeli, vyhledejte `asa_frauddetection_job_demo` v podokně **všechny prostředky** .)

3. V části **topologie úlohy** klikněte na **výstupní** pole.

4. V podokně **výstupy** klikněte na **Přidat** a vyberte **úložiště objektů BLOB**. Pak vyplňte novou výstupní stránku následujícími informacemi:

   |**Nastavení**  |**Navrhovaná hodnota**  |**Popis**  |
   |---------|---------|---------|
   |Alias pro výstup  |  CallStream-FraudulentCalls   |  Zadejte název pro identifikaci výstupu úlohy.   |
   |Předplatné   |  \<Vaše předplatné\> |  Zadejte předplatné Azure vytvořeného účtu úložiště. Účet úložiště můžete využívat v rámci stejného, ale i jiného předplatného. V tomto příkladu se předpokládá, že jste účet vytvořili v rámci stejného předplatného. |
   |Účet úložiště  |  asaehstorage |  Zadejte název účtu úložiště, který jste vytvořili. |
   |Kontejner  | asa-fraudulentcalls-demo | Vyberte vytvořit nový a zadejte název kontejneru. |

    <br/>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-output-blob-storage-new-console.png" alt="Create blob output for Stream Analytics job" width="300px"/>
    
5. Klikněte na možnost **Uložit**. 


## <a name="start-the-streaming-analytics-job"></a>Spuštění úlohy Stream Analytics

Úloha je teď nakonfigurovaná. Zadali jste vstup (centrum událostí), transformaci (dotaz pro hledání podvodných volání) a výstup (úložiště objektů BLOB). Nyní můžete úlohu spustit. 

1. Ujistěte se, že je aplikace TelcoGenerator spuštěná.

2. V podokně Úlohy klikněte na tlačítko **Start**. V podokně **Spustit úlohu** pro čas spuštění výstupu úlohy vyberte **nyní**. 

   ![Spustit úlohu Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start.png)



## <a name="examine-the-transformed-data"></a>Kontrola transformovaných dat

Teď máte kompletní úlohu Stream Analytics. Úloha zkoumá proud metadat telefonních hovorů, hledá podvodné telefonní hovory v reálném čase a zapisuje informace o těchto podvodných voláních do úložiště. 

K dokončení tohoto kurzu se můžete podívat na data zaznamenaná úlohou Stream Analytics. Data se zapisují do služby Azure blog Storage v blocích (soubory). Můžete použít libovolný nástroj, který čte Azure Blob Storage. Jak je uvedeno v části požadavky, můžete použít rozšíření Azure v aplikaci Visual Studio nebo můžete použít nástroj jako [Průzkumník služby Azure Storage](https://storageexplorer.com/) nebo [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

Když prohlížíte obsah souboru v úložišti objektů blob, uvidíte něco jako v následujících případech:

   ![Azure Blob Storage s výstupem Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-blob-storage-view.png)
 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Existují další články, které pokračují ve scénáři zjišťování podvodů a používají prostředky, které jste vytvořili v tomto kurzu. Pokud chcete pokračovat, přečtěte si návrhy v části **Další kroky**.

Pokud jste ale hotovi a nepotřebujete prostředky, které jste vytvořili, můžete je odstranit, abyste nemuseli účtovat žádné poplatky za Azure. V takovém případě vám doporučujeme, abyste provedli následující akce:

1. Zastavte úlohu Stream Analytics. V podokně **úlohy** klikněte v horní části na možnost **zastavit** .
2. Zastavte aplikaci generátoru výpovědi. V příkazovém okně, kde jste aplikaci spustili, stiskněte klávesy CTRL + C.
3. Pokud jste vytvořili nový účet Blob Storage jenom pro tento kurz, odstraňte ho. 
4. Odstraňte úlohu Stream Analytics.
5. Odstraňte centrum událostí.
6. Odstraňte obor názvů centra událostí.

## <a name="get-support"></a>Získat podporu

Potřebujete další pomoc, zkuste [fóru Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další kroky

V tomto kurzu můžete pokračovat následujícím článkem:

* [Stream Analytics a Power BI: řídicí panel analýzy v reálném čase pro streamovaná data](stream-analytics-power-bi-dashboard.md). V tomto článku se dozvíte, jak odeslat výstup výpovědi úlohy Stream Analytics do Power BI pro vizualizaci a analýzu v reálném čase.

Další informace o Stream Analytics obecně najdete v těchto článcích:

* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
