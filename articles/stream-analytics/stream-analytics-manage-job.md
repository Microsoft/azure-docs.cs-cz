---
title: 'Kurz: Vytvoření a správa úlohy Stream Analytics pomocí webu Azure Portal | Microsoft Docs'
description: V tomto kurzu najdete ucelenou ukázku použití Azure Stream Analytics k analýze podvodných volání ve streamu telefonních hovorů.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.workload: data-services
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/30/2018
ms.openlocfilehash: 9ad4462bba861e2bcc940661242d8801355c2f6c
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50240797"
---
# <a name="create-a-stream-analytics-job-to-analyze-phone-call-data-and-visualize-results-in-a-power-bi-dashboard"></a>Vytvoření úlohy Stream Analytics k analýze dat telefonních hovorů a vizualizaci výsledků v řídicím panelu Power BI

V tomto kurzu se dozvíte, jak analyzovat data telefonních hovorů pomocí Azure Stream Analytics. Data telefonních hovorů generovaná klientskou aplikací obsahují některá podvodná volání, která se budou filtrovat pomocí úlohy Stream Analytics.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Generování ukázkových dat telefonních hovorů a jejich odesílání do služby Azure Event Hubs  
> * Vytvoření úlohy Stream Analytics
> * Konfigurace vstupu a výstupu úlohy  
> * Definovat dotaz pro filtrování podvodných volání  
> * Testovat a spustit úlohu
> * Vizualizovat výsledky v Power BI

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že jste provedli následující akce:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).  
* Přihlaste se k webu [Azure Portal](https://portal.azure.com/).  
* Stáhněte si aplikaci pro generování událostí telefonních hovorů [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) z webu Microsoft Download Center. Případně získejte zdrojový kód z [GitHubu](https://aka.ms/azure-stream-analytics-telcogenerator).  

## <a name="create-an-azure-event-hub"></a>Vytvoření centra událostí Azure 

Než bude Stream Analytics moct analyzovat datový proud podvodných volání, musí se data odeslat do Azure. V tomto kurzu budete data odesílat do Azure pomocí služby [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs).

Pomocí následujícího postupu vytvořte centrum událostí a odešlete do něj data volání:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).  
2. Vyberte **Vytvořit prostředek** > **Internet věcí** > **Event Hubs**.  

   ![Vytvoření centra událostí Azure](media/stream-analytics-manage-job/find-eh.png)
3. Vyplňte podokno **Vytvoření oboru názvů** následujícími hodnotami:  

   |**Nastavení**  |**Navrhovaná hodnota** |**Popis**  |
   |---------|---------|---------|
   |Název     | myEventHubsNS        |  Jedinečný název pro identifikaci oboru názvů centra událostí.       |
   |Předplatné     |   \<Vaše předplatné\>      |   Vyberte předplatné Azure, ve kterém chcete vytvořit centrum událostí.      |
   |Skupina prostředků     |   MyASADemoRG      |  Vyberte**Vytvořit nový** a zadejte název nové skupiny prostředků pro váš účet.       |
   |Umístění     |   Západní USA 2      |    Umístění, kde můžete nasadit obor názvů centra událostí.     |

4. Pro zbývající nastavení použijte výchozí možnosti a vyberte **Vytvořit**.  

   ![Vytvoření oboru názvů centra událostí](media/stream-analytics-manage-job/create-ehns.png)

5. Po dokončení nasazení oboru názvů přejděte do části **Všechny prostředky** a v seznamu prostředků Azure vyhledejte *myEventHubsNS*. Výběrem oboru názvů *myEventHubsNS* ho otevřete.  
6. Pak vyberte **+ Centrum událostí** a jako **Název** zadejte *MyEventHub* nebo jiný název podle vašeho výběru. Pro zbývající nastavení použijte výchozí možnosti a vyberte **Vytvořit**. Potom počkejte na úspěšné dokončení nasazení.

   ![Vytvoření centra událostí](media/stream-analytics-manage-job/create-eh.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Udělení přístupu k centru událostí a získání připojovacího řetězce

Předtím než aplikace může odesílat data do služby Azure Event Hubs, musí mít centrum událostí zásady, které povolí odpovídající přístup. Zásady přístupu vytváří připojovací řetězec, který obsahuje informace o autorizaci.

1. Přejděte do centra událostí *MyEventHub*, které jste vytvořili v předchozím kroku. V části **Nastavení** vyberte **Zásady sdíleného přístupu** a pak vyberte **+ Přidat**.

2. Pojmenujte zásadu **MyPolicy** a ujistěte se, že je zaškrtnutá možnost **Spravovat**. Potom vyberte **Vytvořit**.  

   ![Vytvoření zásad sdíleného přístupu k centru událostí](media/stream-analytics-manage-job/create-ehpolicy.png)

3. Po vytvoření zásady ji výběrem otevřete a vyhledejte **Připojovací řetězec – primární klíč**. Vyberte modré tlačítko **kopírovat** vedle připojovacího řetězce.

   ![Uložení připojovacího řetězce zásady sdíleného přístupu](media/stream-analytics-manage-job/save-connection-string.png)

4. Vložte připojovací řetězec do textového editoru. Tento připojovací řetězec budete potřebovat v další části.  

   Připojovací řetězec vypadá takto:

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>` 

   Všimněte si, že připojovací řetězec obsahuje více párů klíč-hodnota oddělené středníky: **Endpoint**, **SharedAccessKeyName**, **SharedAccessKey** a **EntityPath**.  

5. Odeberte z připojovacího řetězce pár **EntityPath** a středník, který mu předchází.

## <a name="start-the-event-generator-application"></a>Spuštění aplikace generátoru událostí

Před spuštěním aplikace TelcoGenerator byste ji měli nakonfigurovat tak, aby odesílala data do služby Azure Event Hubs, kterou jste vytvořili dříve.

1. Extrahujte obsah souboru [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip).  
2. Otevřete soubor `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` v textovém editoru podle vašeho výběru (existuje více než jeden soubor .config, proto se ujistěte, že otevíráte správný soubor).  

3. Aktualizujte element <appSettings> v konfiguračním souboru následujícím způsobem:

   * Nastavte hodnotu klíče *EventHubName* na hodnotu EntityPath v připojovacím řetězci.  
   * Nastavte hodnotu klíče *Microsoft.ServiceBus.ConnectionString* na připojovací řetězec bez hodnoty EntityPath.

4. Uložte soubor.  
5. Dále otevřete příkazové okno a přejděte do složky, do které jste extrahovali aplikaci TelcoGenerator. Potom zadejte následující příkaz:

   ```
   telcodatagen.exe 1000 0.2 2
   ```

   Tento příkaz má následující parametry:
   * Počet záznamů dat volání za hodinu.  
   * Procento pravděpodobnosti podvodů – to znamená, jak často by měla aplikace simulovat podvodné volání. Hodnota 0,2 znamená, že přibližně 20 % záznamů volání bude falešných.  
   * Doba v hodinách – počet hodin, po které by aplikace měla být spuštěná. Aplikaci můžete také kdykoli zastavit ukončením procesu (**Ctrl + C**) na příkazovém řádku.

   Po několika sekundách aplikace začne zobrazovat záznamy telefonních hovorů na obrazovce, když je odešle do centra událostí. Data telefonních hovorů obsahují následující pole:

   |**Záznam**  |**Definice**  |
   |---------|---------|
   |CallrecTime    |  Časové razítko pro počáteční čas volání.       |
   |SwitchNum     |  Telefonní ústředna použitá pro spojení volání. V tomto příkladu jsou ústředny řetězce, které představují zemi původu (USA, Čína, VB, Německo nebo Austrálie).       |
   |CallingNum     |  Telefonní číslo volajícího.       |
   |CallingIMSI     |  IMSI (International Mobile Subscriber Identity). Jedinečný identifikátor volajícího.       |
   |CalledNum     |   Telefonní číslo příjemce volání.      |
   |CalledIMSI     |  IMSI (International Mobile Subscriber Identity). Jedinečný identifikátor příjemce volání.       |

## <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics

Teď, když máte stream událostí volání, můžete vytvořit úlohu Stream Analytics, která načte data z centra událostí.

1. Pokud chcete vytvořit úlohu Stream Analytics, přejděte na web [Azure Portal](https://portal.azure.com/).  

2. Vyberte **Vytvořit prostředek** > **Internet věcí** > **Úloha Stream Analytics**.

3. Vyplňte podokno **Nová úloha Stream Analytics** následujícími hodnotami:  

   |**Nastavení**  |**Navrhovaná hodnota**  |**Popis**  |
   |---------|---------|---------|
   |Název úlohy     |  ASATutorial       |   Jedinečný název pro identifikaci oboru názvů centra událostí.      |
   |Předplatné    |  \<Vaše předplatné\>   |   Vyberte předplatné Azure, ve kterém chcete vytvořit úlohu.       |
   |Skupina prostředků   |   MyASADemoRG      |   Vyberte **Použít existující** a zadejte název nové skupiny prostředků pro váš účet.      |
   |Umístění   |    Západní USA 2     |      Umístění, kde můžete nasadit úlohu. Doporučuje se umístit úlohu a centrum událostí do stejné oblasti, abyste dosáhli nejlepšího výkonu a abyste neplatili za přenos dat mezi oblastmi.      |
   |Hostitelské prostředí    | Cloud        |     Úlohy Stream Analytics můžete nasadit do cloudu nebo do hraničního zařízení. Možnost Cloud umožňuje nasazení do Azure Cloud, možnost Edge do zařízení IoT Edge.    |
   |Jednotky streamování     |    1       |      Jednotky streamování představují výpočetní prostředky nutné k provedení úlohy. Ve výchozím nastavení je tato hodnota nastavená na 1. Podrobnosti o škálování jednotek streamování najdete v článku věnovaném [principům a úpravám jednotek streamování](stream-analytics-streaming-unit-consumption.md).      |   

4. Pro zbývající nastavení použijte výchozí možnosti, vyberte **Vytvořit** a počkejte na úspěšné nasazení.

   ![Vytvoření úlohy](media/stream-analytics-manage-job/create-a-job.png)

## <a name="configure-job-input"></a>Konfigurace vstupu úlohy

Dalším krokem je definování vstupního zdroje, ze kterého bude úloha číst data pomocí centra událostí, které jste vytvořili v předchozí části.

1. Na webu Azure Portal otevřete podokno **Všechny prostředky** a vyhledejte úlohu Stream Analytics *ASATutorial*.  

2. V podokně úlohy Stream Analytics v části **Topologie úlohy** vyberte možnost **Vstupy**.  

3. Vyberte **+ Přidat vstup streamu** a **Centrum událostí**. Vyplňte podokno následujícími hodnotami:  

   |**Nastavení**  |**Navrhovaná hodnota**  |**Popis**  |
   |---------|---------|---------|
   |Alias vstupu     |  CallStream       |  Zadejte popisný název, který bude identifikovat váš vstup. Vstupní alias může obsahovat jenom alfanumerické znaky, spojovníky a podtržítka a musí být dlouhý 3 až 63 znaků.       |
   |Předplatné    |   \<Vaše předplatné\>      |   Vyberte předplatné Azure, ve kterém jste vytvořili centrum událostí. Centrum událostí může být ve stejném předplatném jako úloha Stream Analytics, ale i v jiném.       |
   |Obor názvů centra událostí    |  myEventHubsNS       |  Vyberte obor názvů centra událostí, který jste vytvořili v předchozí části. Všechny obory názvů centra událostí dostupné v aktuálním předplatném jsou uvedeny v rozevírací nabídce.       |
   |Název centra událostí    |   MyEventHub      |  Vyberte centrum událostí, které jste vytvořili v předchozí části. Všechna centra událostí dostupná v aktuálním předplatném jsou uvedena v rozevírací nabídce.       |
   |Název zásad centra událostí   |  Mypolicy       |  Vyberte zásady sdíleného přístupu k centru událostí, které jste vytvořili v předchozí části. Všechny zásady centra událostí dostupné v aktuálním předplatném jsou uvedeny v rozevírací nabídce.       |

4. Pro zbývající nastavení použijte výchozí možnosti a vyberte **Uložit**.

   ![Konfigurace vstupu](media/stream-analytics-manage-job/configure-input.png)

## <a name="configure-job-output"></a>Konfigurace výstupu úlohy 

Posledním krokem je definování výstupní jímky pro úlohu, do které může zapisovat transformovaná data. V tomto kurzu data vypíšete a vizualizujete pomocí Power BI.

1. Na webu Azure Portal otevřete podokno **Všechny prostředky** a pak úlohu Stream Analytics *ASATutorial*.  

2. V podokně úlohy Stream Analytics v části **Topologie úlohy** vyberte možnost **Výstupy**.  

3. Vyberte **+ Přidat** > **Power BI**. Potom ve formuláři vyplňte následující podrobnosti a vyberte **Autorizovat**:  

   |**Nastavení**  |**Navrhovaná hodnota**  |
   |---------|---------|---------|
   |Alias pro výstup  |  MyPBIoutput  |
   |Název datové sady  |   ASAdataset  | 
   |Název tabulky |  ASATable  | 

   ![Konfigurace výstupu](media/stream-analytics-manage-job/configure-output.png)  

4. Když vyberete **Autorizovat**, otevře se místní okno s výzvou k zadání přihlašovacích údajů kvůli ověření vašeho účtu Power BI. Po úspěšné autorizaci uložte nastavení výběrem možnosti **Uložit**. 

## <a name="define-a-query-to-analyze-input-data"></a>Definování dotazu pro analýzu vstupních dat

Dalším krokem je vytvoření transformace, která v reálném čase analyzuje data. Transformační dotaz definujete pomocí jazyka [Stream Analytics Query Language](https://msdn.microsoft.com/library/dn834998.aspx). Dotaz použitý v tomto kurzu detekuje podvodná volání z dat telefonních hovorů.

V tomto příkladu podvodná volání provádí stejný uživatel v pětisekundových rozestupech, ale z různých umístění. Například stejný uživatel nemůže legitimně uskutečnit volání z USA a Austrálie současně. Definování transformačního dotazu pro úlohu Stream Analytics:

1. Na webu Azure Portal otevřete podokno **Všechny prostředky** a přejděte k úloze Stream Analytics **ASATutorial**, kterou jste vytvořili dříve.  

2. V podokně úlohy Stream Analytics v části **Topologie úlohy** vyberte možnost **Dotaz**. V okně dotazu se zobrazí vstupy a výstupy, které jsou pro úlohu nakonfigurované, a můžete v něm vytvořit dotaz, který transformuje vstupní stream.  

3. Nahraďte existující dotaz v editoru následujícím dotazem, který provádí spojení sama na sebe v 5sekundovém intervalu dat volání:

   ```sql
   SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
   INTO "MyPBIoutput"
   FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
   JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime
   ON CS1.CallingIMSI = CS2.CallingIMSI
   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
   WHERE CS1.SwitchNum != CS2.SwitchNum
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

   Při kontrole podvodných volání můžete ověřovat spojení sama na sebe ve streamovaných datech podle hodnoty `CallRecTime`. Potom můžete hledat záznamy volání, kde hodnota `CallingIMSI` (číslo volajícího) je stejná, ale hodnota `SwitchNum` (země původu) se liší. Když použijete operaci JOIN se streamovanými daty, musí spojení určit nějaké limity, jak daleko mohou být odpovídající řádky vzdáleny v čase. Protože streamování dat je nekonečné, jsou časové hranice pro relaci určené v klauzuli spojení **ON** pomocí funkce [DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/datediff-azure-stream-analytics). 

   Tento dotaz je jako normální spojení SQL, kromě funkce **DATEDIFF**. Funkce **DATEDIFF** použitá v tomto dotazu je specifická pro Stream Analytics a musí se nacházet v rámci klauzule `ON...BETWEEN`.  

4. **Uložte** dotaz.  

   ![Definice dotazu služby Stream Analytics](media/stream-analytics-manage-job/define-query.png)

## <a name="test-your-query"></a>Otestování dotazu

Dotaz z editoru dotazů můžete otestovat s použitím ukázkových dat. Otestujte dotaz provedením následujících kroků:

1. Ujistěte se, že je aplikace TelcoGenerator spuštěná a vytváří záznamy telefonních hovorů.  

2. V podokně **Dotaz** vyberte tečky vedle vstupu *CallStream* a pak vyberte **Ukázková data ze vstupu**. 

3. V poli **Minuty** nastavte hodnotu 3 a vyberte **OK**. Ze vstupního streamu se pak vytvoří ukázka ze tří minut dat. Když jsou ukázková data připravená, zobrazí se vám upozornění. Stav vytváření ukázkových dat můžete sledovat v oznamovacím pruhu. 

   Ukázková data jsou dočasně uložena a jsou dostupná, dokud je otevřené okno dotazu. Pokud okno dotazu zavřete, ukázková data se zahodí, a pokud budete chtít provést testování, budete muset vytvořit novou sadu ukázkových dat. Alternativně můžete použít soubor JSON s ukázkovými daty z [GitHubu](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json) a potom tento soubor JSON nahrát a použít jako ukázková data pro vstup *CallStream*.

   ![Ukázková vstupní data](media/stream-analytics-manage-job/sample-input-data.png)

4. Vyberte **Test** a otestujte dotaz. Měly by se zobrazit následující výsledky:  

   ![Výstup testu](media/stream-analytics-manage-job/test-output.png)

## <a name="start-the-job-and-visualize-output"></a>Spuštění úlohy a vizualizace výstupu

1. Pokud chcete úlohu spustit, přejděte do podokna **Přehled** vaší úlohy a vyberte **Spustit**.  

2. Vyberte **Nyní** pro čas spuštění výstupu úlohy a vyberte **Spustit**. Stav úlohy můžete sledovat v oznamovacím pruhu.  

3. Po úspěšném provedení úlohy přejděte do [Power BI](https://powerbi.com/) a přihlaste se pomocí svého pracovního nebo školního účtu. Pokud dotaz úlohy Stream Analytics vypisuje výsledky, na kartě **Datové sady** se zobrazí existující datová sada *ASAdataset*, kterou jste vytvořili.  

4. V pracovním prostoru Power BI vyberte **+ Vytvořit** a vytvořte nový řídicí panel *Podvodná volání*.  

5. V horní části okna vyberte **Přidat dlaždici**. Potom vyberte **Vlastní streamovaná data** a **Další**. V části **Vaše datové sady** zvolte **ASAdataset**. V rozevírací nabídce **Typ vizualizace** vyberte **Karta** a do části **Pole** přidejte **fraudulentcalls**. Vyberte **Další**, zadejte název dlaždice a pak výběrem možnosti **Použít** dlaždici vytvořte.  

   ![Vytvoření dlaždice](media/stream-analytics-manage-job/create-tiles.png)

6. Zopakujte kroky 4 a 5 s následujícími možnostmi:
   * Jako typ vizualizace vyberte spojnicový graf.  
   * Přidejte osu a vyberte **windowend**.  
   * Přidejte hodnotu a vyberte **podvodnávolání**.  
   * Jako **časové okno pro zobrazení** vyberte posledních 10 minut.  

7. Po přidání obou dlaždic by váš řídicí panel měl vypadat jako v následujícím příkladu. Všimněte si, že pokud je spuštěná aplikace odesílající data do centra událostí a také aplikace Streaming Analytics, řídicí panel PowerBI se pravidelně aktualizuje po příchodu nových dat.  

   ![Výsledky Power BI](media/stream-analytics-manage-job/power-bi-results.png)

## <a name="embedding-your-powerbi-dashboard-in-a-web-application"></a>Vložení řídicího panelu PowerBI do webové aplikace

Pro tuto část kurzu budete pro vložení řídicího panelu používat ukázkovou webovou aplikaci [ASP.NET](https://asp.net/) vytvořenou týmem PowerBI. Další informace o vkládání řídicích panelů najdete v tématu [Vkládání pomocí Power BI](https://docs.microsoft.com/power-bi/developer/embedding).

Pokud chcete nastavit aplikaci, přejděte do úložiště GitHub [PowerBI-Developer-Samples](https://github.com/Microsoft/PowerBI-Developer-Samples) a postupujte podle pokynů v části **User Owns Data** (použijte adresu URL pro přesměrování a adresu URL domovské stránky, které jsou uvedené v podsekci **integrate-dashboard-web-app**). Vzhledem k tomu, že používáme příklad pro řídicí panel, použijte vzorový kód **integrate-dashboard-web-app**, který je umístěný v [úložišti GitHub](https://github.com/Microsoft/PowerBI-Developer-Samples/tree/master/User%20Owns%20Data/integrate-dashboard-web-app).
Jakmile aplikace běží v prohlížeči, postupujte podle těchto kroků a vložte do webové stránky řídicí panel, který jste vytvořili dříve:

1. Vyberte **Přihlásit se k Power BI**, čím aplikaci udělíte přístup k řídicím panelům ve vašem účtu Power BI.  

2. Vyberte tlačítko **Získat řídicí panely**, které zobrazí tabulku s přehledem řídicích panelů ve vašem účtu. Vyhledejte název řídicího panelu **powerbi-embedded-dashboard**, který jste vytvořili dříve, a zkopírujte odpovídající hodnotu **EmbedUrl**.  

3. Nakonec vložte hodnotu **EmbedUrl** do odpovídajícího textového pole a vyberte **Vložit řídicí panel**. Nyní můžete vidět stejný řídicí panel vložený do webové aplikace.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili jednoduchou úlohu Stream Analytics, analyzovali jste příchozí data a zobrazili výsledky na řídicím panelu Power BI. Další informace o úlohách Stream Analytics získáte v dalším kurzu:

> [!div class="nextstepaction"]
> [Spouštění Azure Functions z úloh Stream Analytics](stream-analytics-with-azure-functions.md)
