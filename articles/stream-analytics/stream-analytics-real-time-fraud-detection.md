---
title: Zjišťování možných podvodů v reálném čase pomocí Stream Analytics
description: Zjistěte, jak pomocí Služby Stream Analytics vytvořit řešení pro odhalování podvodů v reálném čase. Centrum událostí použijte pro zpracování událostí v reálném čase.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: c0b2943e1f0d7f2386ec09da03d297a570eede7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80276474"
---
# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Začínáme používat Azure Stream Analytics: Zjišťování podvodů v reálném čase

Tento kurz obsahuje komplexní ilustraci, jak používat Azure Stream Analytics. Získáte informace o těchto tématech: 

* Přenesete události streamování do instance Azure Event Hubs. V tomto kurzu budete používat aplikaci, která simuluje datový proud záznamů metadat mobilního telefonu.

* Napište dotazy služby Stream Analytics podobné SQL, abyste transformovali data, agregovali informace nebo hledali vzory. Uvidíte, jak pomocí dotazu prozkoumat příchozí datový proud a vyhledejte volání, která mohou být podvodná.

* Odešlete výsledky do jímky výstupu (úložiště), které můžete analyzovat pro další přehledy. V takovém případě odešlete data podezřelého volání do úložiště objektů Blob Azure.

Tento kurz používá příklad detekce podvodů v reálném čase na základě dat telefonního hovoru. Ilustrovaná technika je také vhodná pro jiné typy odhalování podvodů, jako je podvod y kreditních karet nebo krádež identity. 

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Scénář: Telekomunikace a detekce podvodů sim v reálném čase

Telekomunikační společnost má velký objem dat pro příchozí hovory. Společnost chce odhalit podvodné hovory v reálném čase, aby mohli informovat zákazníky nebo vypnout službu pro konkrétní číslo. Jeden typ podvodu se SIM kartami zahrnuje více hovorů ze stejné identity přibližně ve stejnou dobu, ale v geograficky odlišných místech. Chcete-li zjistit tento typ podvodu, společnost musí prozkoumat příchozí telefonní záznamy a hledat konkrétní vzory - v tomto případě pro hovory provedené přibližně ve stejnou dobu v různých zemích nebo oblastech. Všechny telefonní záznamy, které spadají do této kategorie, jsou zapsány do úložiště pro následnou analýzu.

## <a name="prerequisites"></a>Požadavky

V tomto kurzu simulujete data telefonního hovoru pomocí klientské aplikace, která generuje metadata ukázkového telefonního hovoru. Některé záznamy, které aplikace vytváří, vypadají jako podvodné hovory. 

Než začnete, ujistěte se, že jste provedli následující akce:

* Účet Azure.
* Aplikace generátoru událostí [volání, TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip), kterou lze stáhnout ze služby Stažení softwaru. Rozbalte tento balíček do složky v počítači. Pokud chcete zobrazit zdrojový kód a spustit aplikaci v ladicím programu, můžete získat zdrojový kód aplikace z [GitHubu](https://aka.ms/azure-stream-analytics-telcogenerator). 

    >[!NOTE]
    >Systém Windows může zablokovat stažený soubor ZIP. Pokud ji nemůžete rozbalit, klepněte pravým tlačítkem myši na soubor a vyberte **příkaz Vlastnosti**. Pokud se zobrazí zpráva "Tento soubor pochází z jiného počítače a může být blokován za účelem ochrany tohoto počítače", vyberte možnost **Odblokovat** a klepněte na tlačítko **Použít**.

Pokud chcete prozkoumat výsledky úlohy Streamování Analytics, budete také potřebovat nástroj pro zobrazení obsahu kontejneru úložiště objektů blob Azure. Pokud používáte Visual Studio, můžete použít [Nástroje Azure pro Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) nebo Visual Studio Cloud [Explorer](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer). Případně můžete nainstalovat samostatné nástroje, jako je [Azure Storage Explorer](https://storageexplorer.com/) nebo [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

## <a name="create-an-azure-event-hubs-to-ingest-events"></a>Vytvoření Centra událostí Azure pro ingestování událostí

Chcete-li analyzovat datový proud, *můžete ingestovat* do Azure. Typickým způsobem ingestování dat je použití [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md), který vám umožní ingestovat miliony událostí za sekundu a pak zpracovat a uložit informace o událostech. V tomto kurzu vytvoříte centrum událostí a potom budete mít aplikaci generátoru událostí volání odesílat data volání do tohoto centra událostí. Další informace o rozbočovačích událostí najdete v [dokumentaci k Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).

>[!NOTE]
>Podrobnější verzi tohoto postupu najdete [v tématu Vytvoření oboru názvů Centra událostí a centra událostí pomocí portálu Azure](../event-hubs/event-hubs-create.md). 

### <a name="create-a-namespace-and-event-hub"></a>Vytvoření oboru názvů a centra událostí
V tomto postupu nejprve vytvoříte obor názvů centra událostí a potom do tohoto oboru názvů přidáte centrum událostí. Obory názvů centra událostí se používají k logickému seskupení instancí sběrnice událostí souvisejících s událostmi. 

1. Přihlaste se k portálu Azure a klikněte na **Vytvořit prostředek** v levém horním rohu obrazovky.

2. V levé nabídce vyberte **Všechny služby** a v kategorii **Analytics** vyberte **`*`hvězdičku ( )** vedle centra **událostí.** Zkontrolujte, zda je **centrum událostí** přidáno do **oblíbených položek** v levé navigační nabídce. 

   ![Hledání centra událostí](./media/stream-analytics-real-time-fraud-detection/select-event-hubs-menu.png)

3. V levé navigační nabídce vyberte **Centra událostí** pod **oblíbenými položkami** a na panelu nástrojů vyberte **Přidat.**

   ![Tlačítko Přidat](./media/stream-analytics-real-time-fraud-detection/event-hubs-add-toolbar.png)

4. V podokně **Vytvořit obor názvů** zadejte `<yourname>-eh-ns-demo`název oboru názvů, například . Pro obor názvů můžete použít libovolný název, ale název musí být platný pro adresu URL a musí být jedinečný v rámci Azure. 
    
5. Vyberte předplatné a vytvořte nebo zvolte skupinu prostředků a klepněte na tlačítko **Vytvořit**.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-namespace-new-portal.png" alt="Create event hub namespace in Azure portal" width="300px"/>

6. Po dokončení nasazení oboru názvů vyhledejte obor názvů centra událostí v seznamu prostředků Azure. 

7. Klepněte na nový obor názvů a v podokně oboru názvů klepněte na **položku Centrum událostí**.

   ![Tlačítko Přidat centrum událostí pro vytvoření nového centra událostí](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-button-new-portal.png)    
 
8. Pojmenujte nové `asa-eh-frauddetection-demo`centrum událostí . Můžete použít jiný název. Pokud tak učiníte, poznamenejte si to, protože budete později potřebovat jméno. Pro centrum událostí teď nemusíte nastavovat žádné další možnosti.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-new-portal.png" alt="Name event hub in Azure portal" width="400px"/>
    
9. Klikněte na **Vytvořit**.

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Udělení přístupu k centru událostí a získání připojovacího řetězce

Než může proces odesílat data do centra událostí, musí mít centrum událostí zásadu, která umožňuje odpovídající přístup. Zásady přístupu vytváří připojovací řetězec, který obsahuje informace o autorizaci.

1. V podokně oboru názvů událostí klikněte na **Centra událostí** a potom klikněte na název nového centra událostí.

2. V podokně centra událostí klikněte na ** + &nbsp;** **Zásady sdíleného přístupu** a potom klikněte na Přidat .

    > [!NOTE]
    > Ujistěte se, že pracujete s centrem událostí, ne s oborem názvů centra událostí.

3. Přidejte zásadu s názvem `asa-policy-manage-demo` a pro **Deklaraci**vyberte **Spravovat**.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-shared-access-policy-manage-new-portal.png" alt="Create shared access policy for Stream Analytics" width="300px"/>
 
4. Klikněte na **Vytvořit**.

5. Po nasazení zásady na ni klikněte v seznamu zásad sdíleného přístupu.

6. Najděte pole označené **SPOJOVACÍ ŘETĚZEC-PRIMÁRNÍ KLÍČ** A klikněte na tlačítko kopírovat vedle připojovacího řetězce. 

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-shared-access-policy-copy-connection-string-new-portal.png" alt="Stream Analytics shared access policy" width="300px"/>
 
7. Vložte připojovací řetězec do textového editoru. Tento připojovací řetězec potřebujete pro další část poté, co provedete některé malé úpravy.

    Připojovací řetězec vypadá takto:

    `Endpoint=sb://YOURNAME-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=Gw2NFZwU1Di+rxA2T+6hJYAtFExKRXaC2oSQa0ZsPkI=;EntityPath=asa-eh-frauddetection-demo`

    Všimněte si, že připojovací řetězec obsahuje více `Endpoint`párů `SharedAccessKeyName` `SharedAccessKey`klíč-hodnota, oddělených středníky: , , , a `EntityPath`.  


## <a name="configure-and-start-the-event-generator-application"></a>Konfigurace a spuštění aplikace generátoru událostí

Před spuštěním aplikace TelcoGenerator je nutné ji nakonfigurovat tak, aby odesílala záznamy hovorů do centra událostí, které jste vytvořili.

### <a name="configure-the-telcogenerator-app"></a>Konfigurace aplikace TelcoGenerator

1. V editoru, kde jste zkopírovali připojovací řetězec, poznamenejte si hodnotu `EntityPath` a potom dvojici `EntityPath` odeberte (nezapomeňte odebrat středník, který mu předchází). 

2. Ve složce, ve které jste rozbalili soubor TelcoGenerator.zip, otevřete soubor telcodatagen.exe.config v editoru. (Existuje více než jeden soubor .config, takže se ujistěte, že otevřete ten správný.)

3. V `<appSettings>` prvku:

   * Nastavte hodnotu `EventHubName` klíče na název centra událostí (to znamená na hodnotu cesty entity).
   * Nastavte hodnotu `Microsoft.ServiceBus.ConnectionString` klíče pro připojovací řetězec. 

   Oddíl `<appSettings>` bude vypadat jako následující příklad:

    ```xml
    <appSettings>
     <!-- Service Bus specific app setings for messaging connections -->
     <add key="EventHubName" value="asa-eh-ns-demo"/>
     <add key="Microsoft.ServiceBus.ConnectionString" value="Endpoint=sb://asa-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=GEcnTKf2//1MRn6SN1A2u0O76MP9pj3v0Ccyf1su4Zo="/>
   </appSettings>
    ```
 
4. Uložte soubor. 

### <a name="start-the-app"></a>Spuštění aplikace

1. Otevřete příkazové okno a změňte na složku, kde je rozbalená aplikace TelcoGenerator.

2. Zadejte následující příkaz:

    ```console
    telcodatagen.exe 1000 0.2 2
    ```

   Parametry jsou: 

   * Počet CDR za hodinu. 
   * Pravděpodobnost podvodu se SIM kartou: Jak často by aplikace měla jako procento všech hovorů simulovat podvodný hovor. Hodnota 0,2 znamená, že přibližně 20 % záznamů volání bude falešných.
   * Doba trvání v hodinách. Počet hodin, které by měla aplikace spustit. Aplikaci můžete kdykoli zastavit stisknutím ctrl+c na příkazovém řádku.

   Po několika sekundách aplikace začne zobrazovat záznamy telefonních hovorů na obrazovce, když je odešle do centra událostí.

Některé z klíčových polí, které budete používat v této aplikaci pro detekci podvodů v reálném čase, jsou následující:

|**Záznam**|**Definice**|
|----------|--------------|
|`CallrecTime`|Časové razítko pro počáteční čas volání. |
|`SwitchNum`|Telefonní ústředna použitá pro spojení volání. V tomto příkladu jsou přepínače řetězce, které představují zemi nebo oblast původu (USA, Čína, Velká Británie, Německo nebo Austrálie). |
|`CallingNum`|Telefonní číslo volajícího. |
|`CallingIMSI`|IMSI (International Mobile Subscriber Identity). Toto je jedinečný identifikátor volajícího. |
|`CalledNum`|Telefonní číslo příjemce volání. |
|`CalledIMSI`|IMSI (International Mobile Subscriber Identity). Toto je jedinečný identifikátor příjemce volání. |


## <a name="create-a-stream-analytics-job-to-manage-streaming-data"></a>Vytvoření úlohy Stream Analytics pro správu streamovaných dat

Teď, když máte proud událostí volání, můžete nastavit úlohu Stream Analytics. Úloha bude číst data z centra událostí, které jste nastavili. 

### <a name="create-the-job"></a>Vytvoření úlohy 

1. Na portálu Azure klikněte na **Vytvořit zdroj** > **Internet věcí** > **Stream Analytics úloha**.

2. Pojmenujte `asa_frauddetection_job_demo`úlohu , zadejte předplatné, skupinu prostředků a umístění.

    Je vhodné umístit úlohu a centrum událostí do stejné oblasti pro dosažení nejlepšího výkonu a proto, abyste neplatili za přenos dat mezi oblastmi.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-job-new-portal.png" alt="Create Stream Analytics job in portal" width="300px"/>

3. Klikněte na **Vytvořit**.

    Úloha je vytvořena a portál zobrazí podrobnosti o úloze. Zatím však není nic spuštěno – před spuštěním úlohy je nutné úlohu nakonfigurovat.

### <a name="configure-job-input"></a>Konfigurace vstupu úlohy

1. Na řídicím panelu nebo v podokně **Všechny prostředky** vyhledejte a vyberte úlohu `asa_frauddetection_job_demo` Stream Analytics. 
2. V části **Přehled** podokna úloh Stream Analytics klikněte na pole **Vstup.**

   ![Vstupní pole v části Topologie v podokně úloh Analýza streamování](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-input-box-new-portal.png)
 
3. Klikněte na **Přidat vstup datového proudu** a vyberte Centrum **událostí**. Poté vyplňte stránku Nový vstup následujícími informacemi:

   |**Nastavení**  |**Navrhovaná hodnota**  |**Popis**  |
   |---------|---------|---------|
   |Alias vstupu  |  CallStream   |  Zadejte název pro identifikaci vstupu úlohy.   |
   |Předplatné   |  \<Vaše předplatné\> |  Vyberte předplatné Azure, které má centrum událostí, které jste vytvořili.   |
   |Obor názvů centra událostí  |  asa-eh-ns-demo |  Zadejte název oboru názvů Centra událostí.   |
   |Název centra událostí  | asa-eh-frauddetection-demo | Vyberte název centra událostí.   |
   |Název zásad centra událostí  | asa-policy-manage-demo | Vyberte zásady přístupu, které jste vytvořili dříve.   |

    </br>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-input-new-portal.png" alt="Create Stream Analytics input in portal" width="300px"/>


4. Klikněte na **Vytvořit**.

## <a name="create-queries-to-transform-real-time-data"></a>Vytváření dotazů pro transformaci dat v reálném čase

V tomto okamžiku máte úlohu Stream Analytics nastavenou pro čtení příchozího datového proudu. Dalším krokem je vytvoření dotazu, který analyzuje data v reálném čase. Stream Analytics podporuje jednoduchý, deklarativní dotaz model, který popisuje transformace pro zpracování v reálném čase. Dotazy používají jazyk podobný SQL, který má některá rozšíření specifická pro Stream Analytics. 

Jednoduchý dotaz může jen číst všechna příchozí data. Často však vytváříte dotazy, které hledají konkrétní data nebo relace v datech. V této části kurzu vytvoříte a otestujete několik dotazů, abyste se naučili několik způsobů, ve kterých můžete transformovat vstupní datový proud pro analýzu. 

Dotazy, které zde vytvoříte, zobrazí transformovaná data na obrazovce. V pozdější části nakonfigurujete výstupní jímku a dotaz, který zapisuje transformovaná data do tohoto jímky.

Další informace o jazyce najdete v [tématu Azure Stream Analytics Query Language Reference](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

### <a name="get-sample-data-for-testing-queries"></a>Získání ukázkových dat pro testovací dotazy

Aplikace TelcoGenerator odesílá záznamy hovorů do centra událostí a úloha Stream Analytics je nakonfigurována tak, aby se četla z centra událostí. Dotaz můžete použít k testování úlohy a ujistěte se, že je správně čte. Chcete-li otestovat dotaz v konzole Azure, potřebujete ukázková data. V tomto návodu budete extrahovat ukázková data z datového proudu, který přichází do centra událostí.

1. Ujistěte se, že aplikace TelcoGenerator je spuštěna a vytváří záznamy volání.
2. Na portálu se vraťte do podokna úloh Analýza streamování. (Pokud jste podokno zavřeli, vyhledejte `asa_frauddetection_job_demo` ho v podokně Všechny **zdroje.)**
3. Klikněte na pole **Dotaz.** Azure uvádí vstupy a výstupy, které jsou nakonfigurované pro úlohu a umožňuje vytvořit dotaz, který vám umožní transformovat vstupní datový proud, jak je odeslándo výstupu.
4. V podokně **Dotaz** klepněte na tečky vedle `CallStream` vstupu a vyberte **ukázková data ze vstupu**.

   ![Možnosti nabídky pro použití ukázkových dat pro položku úlohy Analýzy streamování s vybranou možností "Ukázková data ze vstupu"](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sample-data-from-input.png)


5. Nastavte **minuty** na 3 a klepněte na tlačítko **OK**. 
    
   ![Možnosti vzorkování vstupního datového proudu s vybranou 3 minutami](./media/stream-analytics-real-time-fraud-detection/stream-analytics-input-create-sample-data.png)

    Azure ukveká data za 3 minuty ze vstupního datového proudu a upozorní vás, když jsou ukázková data připravená. (To trvá krátkou dobu.) 

Ukázková data jsou dočasně uložena a jsou dostupná, dokud je otevřené okno dotazu. Pokud okno dotazu zavřete, budou ukázková data odstraněna a budete muset vytvořit novou sadu ukázkových dat. 

Jako alternativu můžete získat soubor JSON, který obsahuje ukázková data [z GitHubu](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json), a potom nahrát `CallStream` soubor JSON, který se použije jako ukázková data pro vstup. 

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
    >Stejně jako u SQL klíčová slova nejsou malá a velká písmena a mezery není významné.

    V tomto `CallStream` dotazu je alias, který jste zadali při vytváření vstupu. Pokud jste použili jiný alias, použijte místo toho tento název.

2. Klepněte na tlačítko **Testovat**.

    Úloha Stream Analytics spustí dotaz proti ukázkovým datům a zobrazí výstup v dolní části okna. Výsledky označují, že centrum událostí a úloha Analýzy streamování jsou správně nakonfigurovány. (Jak již bylo uvedeno, později vytvoříte výstupní jímku, do které může dotaz zapisovat data.)

   ![Výstup úlohy Stream Analytics, který zobrazuje 73 generovaných záznamů](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output.png)

    Přesný počet záznamů, které vidíte, bude záviset na tom, kolik záznamů bylo zachyceno ve vašem tříminutovém vzorku.
 
### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Snížení počtu polí pomocí sloupové projekce

V mnoha případech vaše analýza nepotřebuje všechny sloupce ze vstupního datového proudu. Dotaz můžete použít k promítat menší sadu vrácených polí než v předávací dotaz.

1. Změňte dotaz v editoru kódu na následující:

    ```SQL
    SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum 
    FROM 
        CallStream
    ```

2. Klikněte znovu na **Testovat.** 

   ![Výstup úlohy Stream Analytics pro projekci zobrazuje 25 záznamů](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-projection.png)
 
### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Počet příchozích hovorů podle oblasti: Omílání okno s agregací

Předpokládejme, že chcete spočítat počet příchozích hovorů v oblasti. V datových proudech, pokud chcete provádět agregační funkce, jako je počítání, musíte segmentovat datový proud do časových jednotek (protože samotný datový proud je efektivně nekonečný). To provést pomocí funkce [okna](stream-analytics-window-functions.md)Streaming Analytics . Potom můžete pracovat s daty uvnitř tohoto okna jako celek.

Pro tuto transformaci chcete posloupnost temporálních oken, která se nepřekrývají – každé okno bude mít samostatnou sadu dat, která můžete seskupit a agregovat. Tento typ okna se označuje jako *omílání okno*. V okně Omílání můžete získat počet příchozích `SwitchNum`hovorů seskupených podle , který představuje zemi nebo oblast, odkud volání pochází. 

1. Změňte dotaz v editoru kódu na následující:

    ```SQL
    SELECT 
        System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
    FROM
        CallStream TIMESTAMP BY CallRecTime 
    GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
    ```

    Tento dotaz `Timestamp By` používá klíčové `FROM` slovo v klauzuli k určení, které pole časového razítka ve vstupním datovém proudu se má použít k definování okna Omílání. V tomto případě okno rozdělí data do segmentů `CallRecTime` podle pole v každém záznamu. (Pokud není zadáno žádné pole, operace okna používá čas, kdy každá událost dorazí do centra událostí. Viz "Čas příjezdu vs čas aplikace" v [Odkaz na dotaz Ový analytika .](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) 

    Projekce zahrnuje `System.Timestamp`, který vrátí časové razítko na konci každého okna. 

    Chcete-li určit, že chcete použít okno Oumbling, použijte `GROUP BY` funkci [TUMBLINGWINDOW](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) v klauzuli. Ve funkci zadáte časovou jednotku (kdekoli od mikrosekundy do jednoho dne) a velikost okna (kolik jednotek). V tomto příkladu se okno Omílání skládá z 5 sekundových intervalů, takže získáte počet podle země nebo oblasti pro volání za každých 5 sekund.

2. Klikněte znovu na **Testovat.** Ve výsledcích všimněte si, že časová razítka pod **WindowEnd** jsou v krocích po 5 sekundách.

   ![Výstup úlohy Stream Analytics pro agregaci zobrazující 13 záznamů](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-aggregation.png)
 
### <a name="detect-sim-fraud-using-a-self-join"></a>Detekce podvodů se SIM kartou pomocí vlastního připojení

V tomto příkladu zvažte podvodné použití volání, které pocházejí od stejného uživatele, ale v různých umístěních do 5 sekund od sebe. Například stejný uživatel nemůže legitimně uskutečnit volání z USA a Austrálie současně. 

Chcete-li zkontrolovat pro tyto případy, můžete použít vlastní spojení dat streamování připojit `CallRecTime` datový proud k sobě na základě hodnoty. Potom můžete vyhledat záznamy volání, kde `CallingIMSI` je hodnota (původní číslo) `SwitchNum` stejná, ale hodnota (země nebo oblast původu) není stejná.

Při použití spojení s daty streamování, spojení musí poskytnout některá omezení na tom, jak daleko odpovídající řádky mohou být odděleny v čase. (Jak již bylo uvedeno dříve, streamovaná data jsou efektivně nekonečná.) Časové hranice pro vztah jsou určeny uvnitř `ON` klauzule `DATEDIFF` spojení pomocí funkce. V tomto případě je spojení založeno na intervalu volání 5 sekund.

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

    Tento dotaz je jako jakékoli `DATEDIFF` spojení SQL s výjimkou funkce v spojení. Tato verze `DATEDIFF` aplikace je specifická pro službu `ON...BETWEEN` Streaming Analytics a musí se zobrazit v klauzuli. Parametry jsou časová jednotka (sekundy v tomto příkladu) a aliasy dvou zdrojů pro spojení. To se liší od `DATEDIFF` standardní funkce SQL.

    Klauzule `WHERE` obsahuje podmínku, která označuje podvodné volání: původní přepínače nejsou stejné. 

2. Klikněte znovu na **Testovat.** 

   ![Výstup úlohy Stream Analytics pro vlastní připojení, zobrazující 6 vygenerovaných záznamů](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-self-join.png)

3. Kliknutím na **Uložit** uložte dotaz pro vlastní připojení jako součást úlohy Analýza streamování. (Neukládá ukázková data.)

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-save-button-new-portal.png" alt="Save Stream Analytics query in portal" width="300px"/>

## <a name="create-an-output-sink-to-store-transformed-data"></a>Vytvoření výstupníjím jímky pro uložení transformovaných dat

Definovali jste datový proud událostí, vstup centra událostí pro události ingestování a dotaz k provedení transformace přes datový proud. Posledním krokem je definování výstupníjím jímky pro úlohu – to znamená místo pro zápis transformovaného datového proudu. 

Jako výstupní propady můžete použít mnoho prostředků – databázi SQL Serveru, úložiště tabulek, úložiště datového jezera, Power BI a dokonce i další centrum událostí. V tomto kurzu zapíšete datový proud do úložiště objektů blob Azure, což je typická volba pro shromažďování informací o událostech pro pozdější analýzu, protože pojme nestrukturovaná data.

Pokud máte existující účet úložiště objektů blob, můžete ho použít. V tomto kurzu se dozvíte, jak vytvořit nový účet úložiště.

### <a name="create-an-azure-blob-storage-account"></a>Vytvoření účtu úložiště objektů blob Azure

1. V levém horním rohu portálu Azure vyberte **Vytvořit** > **účet úložiště****prostředků** > . Vyplňte stránku úlohy účtu úložiště s **názvem** nastaveným na "asaehstorage", **Umístění** nastavené na "Východní USA", **Skupina prostředků** nastavená na "asa-eh-ns-rg" (hostuj účet úložiště ve stejné skupině prostředků jako úloha streamování pro zvýšení výkonu). Ostatní nastavení můžou zůstat na výchozích hodnotách.  

   ![Vytvoření účtu úložiště na Webu Azure Portal](./media/stream-analytics-real-time-fraud-detection/stream-analytics-storage-account-create.png)

2. Na webu Azure Portal se vraťte do podokna úloh Streamování Analytics. (Pokud jste podokno zavřeli, vyhledejte `asa_frauddetection_job_demo` ho v podokně Všechny **zdroje.)**

3. V části **Topologie úlohklikněte** na pole **Výstup.**

4. V podokně **Výstupy** klikněte na **Přidat** a vyberte **úložiště objektů blob**. Poté vyplňte stránku Nový výstup následujícími informacemi:

   |**Nastavení**  |**Navrhovaná hodnota**  |**Popis**  |
   |---------|---------|---------|
   |Alias pro výstup  |  CallStream-PodvodnéHovory   |  Zadejte název pro identifikaci výstupu úlohy.   |
   |Předplatné   |  \<Vaše předplatné\> |  Zadejte předplatné Azure vytvořeného účtu úložiště. Účet úložiště můžete využívat v rámci stejného, ale i jiného předplatného. V tomto příkladu se předpokládá, že jste účet vytvořili v rámci stejného předplatného. |
   |Účet úložiště  |  asaehstorage |  Zadejte název účtu úložiště, který jste vytvořili. |
   |Kontejner  | asa-scamcalls-demo | Zvolte Vytvořit nový a zadejte název kontejneru. |

    <br/>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-output-blob-storage-new-console.png" alt="Create blob output for Stream Analytics job" width="300px"/>
    
5. Klikněte na **Uložit**. 


## <a name="start-the-streaming-analytics-job"></a>Spuštění úlohy Analýza streamování

Úloha je nyní nakonfigurována. Zadali jste vstup (centrum událostí), transformaci (dotaz pro vyhledávání podvodných volání) a výstup (úložiště objektů blob). Nyní můžete začít pracovat. 

1. Ujistěte se, že je spuštěná aplikace TelcoGenerator.

2. V podokně úloh klepněte na tlačítko **Start**. V podokně **Spustit úlohu** vyberte v části Čas zahájení výstupu úlohvmožnosti **Nyní**. 

   ![Spuštění úlohy Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start.png)



## <a name="examine-the-transformed-data"></a>Zkontrolujte transformovaná data

Nyní máte kompletní úlohu Služby Streamování analytics. Úkolem je zkoumání proudu metadat telefonních hovorů, hledání podvodných telefonních hovorů v reálném čase a psaní informací o těchto podvodných voláních do úložiště. 

Chcete-li dokončit tento kurz, můžete se podívat na data zachycená úlohou Streaming Analytics. Data se zapisují do úložiště blogu Azure v blocích (souborech). Můžete použít libovolný nástroj, který čte Azure Blob Storage. Jak je uvedeno v části Požadavky, můžete použít rozšíření Azure v Sadě Visual Studio, nebo můžete použít nástroj, jako je [Azure Storage Explorer](https://storageexplorer.com/) nebo [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

Když zkontrolujete obsah souboru v úložišti objektů blob, uvidíte něco jako následující:

   ![Úložiště objektů blob Azure s výstupem Analýzy streamování](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-blob-storage-view.png)
 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Existují další články, které pokračují ve scénáři zjišťování podvodů a používají prostředky, které jste vytvořili v tomto kurzu. Pokud chcete pokračovat, podívejte se na návrhy v části **Další kroky**.

Pokud jste hotovi a nepotřebujete prostředky, které jste vytvořili, můžete je odstranit, abyste vám nevznikly zbytečné poplatky Azure. V takovém případě doporučujeme provést následující kroky:

1. Zastavte úlohu Analýzy streamování. V podokně **Úlohy** klikněte nahoře na **Zastavit.**
2. Zastavte aplikaci Telco Generator. V příkazovém okně, ve kterém jste aplikaci spustili, stiskněte Ctrl+C.
3. Pokud jste vytvořili nový účet úložiště objektů blob pouze pro tento kurz, odstraňte jej. 
4. Odstraňte úlohu Analýza streamování.
5. Odstraňte centrum událostí.
6. Odstraňte obor názvů centra událostí.

## <a name="get-support"></a>Získat podporu

Další pomoc našlápnete na [fóru Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další kroky

V tomto kurzu můžete pokračovat v následujícím článku:

* [Stream Analytics a Power BI: Řídicí panel analýzy v reálném čase pro streamování dat](stream-analytics-power-bi-dashboard.md). V tomto článku se můžete v tomto článku naučit odesílat výstup TelCo úlohy Stream Analytics do Power BI pro vizualizaci a analýzu v reálném čase.

Další informace o službě Stream Analytics obecně naleznete v těchto článcích:

* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
