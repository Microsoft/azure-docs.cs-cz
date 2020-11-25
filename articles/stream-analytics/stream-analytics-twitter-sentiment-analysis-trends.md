---
title: Analýza mínění na Twitteru v reálném čase pomocí Azure Stream Analytics
description: Tento článek popisuje, jak použít Stream Analytics analýzy mínění pro Twitter v reálném čase. Podrobné pokyny pro generování událostí pro data na aktivním řídicím panelu.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: how-to
ms.date: 02/10/2020
ms.openlocfilehash: 0941e3d5141b5b8841f5d37e3db0d0b1b1474547
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019816"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Analýza subjektivního hodnocení na Twitteru v reálném čase v Azure Stream Analytics

V tomto článku se dozvíte, jak vytvořit řešení pro analýzu mínění na sociálních médiích prostřednictvím služby Twitter v reálném čase do Azure Event Hubs. Napíšete Azure Stream Analytics dotaz k analýze dat a uložení výsledků pro pozdější použití nebo vytvoření [Power BIho](https://powerbi.com/) řídicího panelu pro poskytování přehledů v reálném čase.

Nástroje pro analýzu sociálních médií můžou organizacím pochopit témata týkající se trendů. Témata týkající se trendů jsou předměty a postojů, které mají velký objem příspěvků na sociálních médiích. Analýza mínění, která se také nazývá *dolování*, využívá analytické nástroje pro sociální média k určení postojů k produktu nebo nápadu. 

Analýza trendů na Twitteru v reálném čase je skvělým příkladem analytického nástroje, protože model předplatného hashtagu vám umožňuje naslouchat konkrétním klíčovým slovům (hashtagy) a vyvíjet mínění analýzu informačního kanálu.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Scénář: analýza míněníů na sociálních médiích v reálném čase

Společnost, která má web s novinkami na pracovišti, má za následek získání výhody oproti konkurenci díky obsahu webu, který je okamžitě relevantní pro příslušné čtenáře. Společnost používá analýzu sociálních médií v tématech, která jsou relevantní pro čtenáře, díky analýze mínění dat na Twitteru v reálném čase.

Aby bylo možné v reálném čase na Twitteru identifikovat témata týkající se trendů, společnost potřebuje analýzy v reálném čase o svazku na disku a mínění pro klíčové témata.

## <a name="prerequisites"></a>Požadavky

V této příručce se naučíte používat klientskou aplikaci, která se připojuje k Twitteru a hledá tweety s určitými hashtagy (které můžete nastavit). Pokud chcete spustit aplikaci a analyzovat tweety pomocí Azure Stream Analytics, musíte mít následující:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).

* Účet na [Twitteru](https://twitter.com) .

* Aplikace TwitterClientCore, která čte kanál Twitteru. Chcete-li získat tuto aplikaci, Stáhněte si [TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore).

* Nainstalujte [.NET Core CLI](/dotnet/core/tools/?tabs=netcore2x) verze 2.1.0.

## <a name="create-an-event-hub-for-streaming-input"></a>Vytvoření centra událostí pro vstup streamování

Ukázková aplikace generuje události a předává je do centra událostí Azure. Azure Event Hubs jsou upřednostňovanou metodou přijímání událostí pro Stream Analytics. Další informace najdete v dokumentaci k [Azure Event Hubs](../event-hubs/event-hubs-about.md).

### <a name="create-an-event-hub-namespace-and-event-hub"></a>Vytvoření oboru názvů centra událostí a centra událostí
V této části vytvoříte obor názvů centra událostí a přidáte do tohoto oboru názvů centrum událostí. Obory názvů centra událostí se používají k logickému seskupení souvisejících instancí sběrnice událostí. 

1. Přihlaste se k Azure Portal a vyberte **vytvořit prostředek**. Stisknutím. Vyhledejte **Event Hubs** a vyberte **vytvořit**.

2. Na stránce **vytvořit obor názvů** zadejte název oboru názvů. Můžete použít libovolný název oboru názvů, ale název musí být platný pro adresu URL a musí být jedinečný v rámci Azure. 
    
3. Vyberte cenovou úroveň a předplatné a vytvořte nebo zvolte skupinu prostředků. Pak zvolte umístění a vyberte **vytvořit**. 
 
4. Až se dokončí nasazení oboru názvů, přejděte do skupiny prostředků a v seznamu prostředků Azure Najděte obor názvů centra událostí. 

5. Z nového oboru názvů vyberte **+ &nbsp; centrum událostí**. 

6. Pojmenujte nové centrum událostí *socialtwitter-eh*. Můžete použít jiný název. Pokud to uděláte, poznamenejte si ho, protože ho budete potřebovat později. Pro centrum událostí není nutné nastavovat žádné jiné možnosti.
 
7. Vyberte **Vytvořit**.

### <a name="grant-access-to-the-event-hub"></a>Udělení přístupu k centru událostí

Než může proces odesílat data do centra událostí, centrum událostí potřebuje zásadu, která umožňuje přístup. Zásady přístupu vytváří připojovací řetězec, který obsahuje informace o autorizaci.

1.  V navigačním panelu na levé straně oboru názvů centra událostí vyberte **Event Hubs**, který se nachází v části **entity** . Pak vyberte centrum událostí, které jste právě vytvořili.

2.  V navigačním panelu na levé straně vyberte **zásady sdíleného přístupu** umístěné v části **Nastavení**.

    >[!NOTE]
    >V části pro obor názvů centra událostí a pro centrum událostí se používá možnost Zásady sdíleného přístupu. Ujistěte se, že pracujete v kontextu centra událostí, nikoli na celkovém oboru názvů centra událostí.

3.  Na stránce zásady přístupu vyberte **+ Přidat**. Pak jako **název zásady** zadejte *socialtwitter-Access* a zaškrtněte políčko **Spravovat** .
 
4.  Vyberte **Vytvořit**.

5.  Po nasazení zásady v seznamu zásad sdíleného přístupu vyberte zásadu.

6.  Vyhledejte pole s popiskem " **primární připojovací řetězec-klíč** " a vyberte tlačítko Kopírovat vedle připojovacího řetězce.
 
7.  Vložte připojovací řetězec do textového editoru. Tento připojovací řetězec budete potřebovat pro další oddíl po provedení některých malých úprav.

   Připojovací řetězec vypadá takto:
   
   ```
   Endpoint=sb://EVENTHUBS-NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh
   ```

   Všimněte si, že připojovací řetězec obsahuje několik párů klíč-hodnota oddělených středníky: `Endpoint` , `SharedAccessKeyName` , `SharedAccessKey` a `EntityPath` .  

   > [!NOTE]
   > Z důvodu zabezpečení byly části připojovacího řetězce v příkladu odebrány.

## <a name="configure-and-start-the-twitter-client-application"></a>Konfigurace a spuštění klientské aplikace Twitter

Klientská aplikace získává události v rámci služby Twitter přímo z Twitteru. Aby to bylo možné, potřebuje mít oprávnění volat rozhraní API pro streamování Twitteru. Ke konfiguraci tohoto oprávnění vytvoříte aplikaci na Twitteru, která generuje jedinečné přihlašovací údaje (například token OAuth). Pak můžete nakonfigurovat, aby klientská aplikace používala tyto přihlašovací údaje při volání rozhraní API. 

### <a name="create-a-twitter-application"></a>Vytvoření aplikace Twitter
Pokud ještě nemáte aplikaci Twitter, kterou můžete použít pro tento průvodce, můžete ji vytvořit. Musíte mít už účet na Twitteru.

> [!NOTE]
> Přesný proces na Twitteru pro vytvoření aplikace a získání klíčů, tajných kódů a tokenu se může změnit. Pokud se tyto pokyny neshodují s tím, co vidíte na webu Twitteru, přečtěte si dokumentaci pro vývojáře na Twitteru.

1. Z webového prohlížeče, navštivte [Twitter pro vývojáře](https://developer.twitter.com/en/apps), vytvořte vývojářský účet a vyberte **vytvořit aplikaci**. Může se zobrazit zpráva oznamující, že je potřeba požádat o vývojářský účet pro Twitter. Nebojte se tak a po schválení vaší aplikace by se měl zobrazit potvrzovací e-mail. Schválení pro vývojářský účet může trvat několik dní.

   ![Snímek obrazovky se zobrazí tlačítko vytvořit aplikaci.](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Podrobnosti o aplikaci Twitter")

2. Na stránce **Create an application** (Vytvoření aplikace) zadejte podrobnosti o nové aplikaci a pak vyberte **Create your Twitter application** (Vytvořit aplikaci Twitter).

   ![Snímek obrazovky se zobrazí v podokně podrobností aplikace, kde můžete zadat hodnoty pro vaši aplikaci.](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Podrobnosti o aplikaci Twitter")

3. Na stránce aplikace vyberte kartu **klíče a tokeny** a zkopírujte hodnoty **klíč rozhraní API příjemce** a **tajného klíče rozhraní API příjemce**. Pokud chcete generovat přístupové tokeny, vyberte také možnost **vytvořit** v části **přístupový token a tajný klíč přístupového tokenu** . Zkopírujte hodnoty **Access Token** (Přístupový token) a **Access Token Secret** (Tajný klíč přístupového tokenu).

   Uložte hodnoty, které jste načetli pro aplikaci Twitter. Hodnoty budete potřebovat později.

> [!NOTE]
> Klíče a tajné klíče pro aplikaci Twitter poskytují přístup k vašemu účtu na Twitteru. Tyto informace považovat za citlivé, stejně jako u vašeho hesla na Twitteru. Například tyto informace nevložíte do aplikace, kterou udělíte ostatním. 

### <a name="configure-the-client-application"></a>Konfigurace klientské aplikace

Vytvořili jsme klientskou aplikaci, která se připojuje k datům na Twitteru pomocí [rozhraní API streamování na Twitteru](https://dev.twitter.com/streaming/overview) ke shromažďování událostí na základě konkrétní sady témat.

Před spuštěním aplikace bude nutné od sebe vyžadovat určité informace, jako jsou klíče Twitteru a připojovací řetězec centra událostí.

1. Ujistěte se, že jste stáhli aplikaci [TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore) , jak je uvedeno v části požadavky.

2. Pomocí textového editoru otevřete soubor *App.config* . Proveďte následující změny `<appSettings>` elementu:

   * Nastavte `oauth_consumer_key` na klíč příjemce Twitteru (klíč rozhraní API). 
   * Nastavte `oauth_consumer_secret` na klíč příjemce Twitteru (tajný klíč rozhraní API).
   * Nastavte `oauth_token` na přístupový token Twitteru.
   * Nastavte `oauth_token_secret` tajný kód přístupového tokenu Twitteru.
   * Nastavte `EventHubNameConnectionString` na připojovací řetězec.
   * Nastavte `EventHubName` na název centra událostí (tj. hodnotu cesty k entitě).

3. Otevřete příkazový řádek a přejděte do adresáře, kde se nachází vaše aplikace TwitterClientCore. Pomocí příkazu `dotnet build` Sestavte projekt. Pak použijte příkaz `dotnet run` ke spuštění aplikace. Aplikace pošle tweety do centra událostí.

## <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics

Teď, když jsou události ve službě Twitter streamované v reálném čase, můžete nastavit úlohu Stream Analytics pro analýzu těchto událostí v reálném čase.

1. V Azure Portal přejděte do skupiny prostředků a vyberte **+ Přidat**. Pak vyhledejte **Stream Analytics úlohu** a vyberte **vytvořit**.

2. Pojmenujte úlohu `socialtwitter-sa-job` a zadejte předplatné, skupinu prostředků a umístění.

    Je vhodné umístit úlohu a centrum událostí ve stejné oblasti, aby se co nejlépe vyzpůsobilo, a nebudete platit za přenos dat mezi oblastmi.

3. Vyberte **Vytvořit**. Až se nasazení dokončí, přejděte k vaší úloze.

## <a name="specify-the-job-input"></a>Zadat vstup úlohy

1. V úloze Stream Analytics v nabídce vlevo v části **topologie úlohy** vyberte **vstupy** .

2. Vyberte **+ &nbsp; přidat datový proud**  >  **centrum událostí** vstupu. Vyplňte **nový vstupní** formulář s následujícími informacemi:

   |**Nastavení**  |**Navrhovaná hodnota**  |**Popis**  |
   |---------|---------|---------|
   |Alias vstupu| *TwitterStream* | Zadejte alias pro vstup. |
   |Předplatné  | \<Your subscription\> |  Vyberte předplatné Azure, které chcete použít. |
   |Obor názvů centra událostí | *ASA – Twitter – eventhub* |
   |Název centra událostí | *socialtwitter – eh* | Vyberte možnost *použít existující*. Pak vyberte centrum událostí, které jste vytvořili.|
   |Typ komprese události| GZip | Typ komprese dat|

   Zbývající výchozí hodnoty ponechte a vyberte **Uložit**.

## <a name="specify-the-job-query"></a>Zadat dotaz úlohy

Stream Analytics podporuje jednoduchý deklarativní model dotazu, který popisuje transformace. Další informace o tomto jazyce najdete v referenčních informacích k jazyku [Azure Stream Analytics dotazů](/stream-analytics-query/stream-analytics-query-language-reference). Tento průvodce vám pomůže vytvořit a otestovat několik dotazů přes data z Twitteru.

Chcete-li porovnat počet zmínk mezi tématy, můžete použít [bubnové okno](/stream-analytics-query/tumbling-window-azure-stream-analytics) a získat počet slov, který bude v tématu každých pět sekund.

1. V okně **Přehled** úlohy vyberte **Upravit dotaz** v pravém horním rohu pole dotazu. Azure obsahuje seznam vstupů a výstupů, které jsou pro úlohu nakonfigurované, a umožňuje vytvořit dotaz pro transformaci vstupního streamu, když se pošle do výstupu.

2. V editoru dotazů změňte dotaz na následující:

   ```sql
   SELECT *
   FROM TwitterStream
   ```

3. Data události ze zpráv by se měla zobrazit v okně **Náhled vstupu** pod vaším dotazem. Ujistěte se, že je **zobrazení** nastaveno na **JSON**. Pokud nevidíte žádná data, ujistěte se, že váš generátor dat odesílá události do centra událostí a že jste jako typ komprese pro vstup vybrali **gzip** .

4. Vyberte **test Query** a Všimněte si výsledků v okně **výsledky testu** pod vaším dotazem.

5. Změňte dotaz v editoru kódu na následující a vyberte **test Query**:

   ```sql
   SELECT System.Timestamp as Time, text
   FROM TwitterStream
   WHERE text LIKE '%Azure%'
   ```

6. Tento dotaz vrátí všechny tweety, které obsahují klíčové slovo *Azure*.

## <a name="create-an-output-sink"></a>Vytvoření výstupní jímky

Nyní jste definovali datový proud událostí, vstup centra událostí pro ingestování událostí a dotaz k provedení transformace v datovém proudu. Posledním krokem je definování výstupní jímky pro úlohu.  

V této příručce se naučíte zapisovat agregované události se změnami z dotazu úlohy do úložiště objektů BLOB v Azure.  Výsledky můžete také nabízet Azure SQL Database, Azure Table Storage, Event Hubs nebo Power BI, a to v závislosti na potřebách vaší aplikace.

## <a name="specify-the-job-output"></a>Zadejte výstup úlohy.

1. V části **topologie úlohy** v levé navigační nabídce vyberte **výstupy**. 

2. Na stránce **výstupy** klikněte na **+ &nbsp; Přidat** a **BLOB Storage/Data Lake Storage Gen2**:

   * **Alias pro výstup**: použijte název `TwitterStream-Output` . 
   * **Možnosti importu**: vyberte **ze svých předplatných možnost vybrat úložiště**.
   * **Účet úložiště**. Vyberte svůj účet úložiště.
   * **Kontejner**. Vyberte **vytvořit nové** a zadejte `socialtwitter` .
   
4. Vyberte **Uložit**.   

## <a name="start-the-job"></a>Spuštění úlohy

Je určen vstup, dotaz a výstup úlohy. Jste připraveni začít úlohu Stream Analytics.

1. Ujistěte se, že je aplikace TwitterClientCore spuštěná. 

2. V přehledu úlohy vyberte **Spustit**.

3. Na stránce **Spustit úlohu** pro **čas spuštění výstupu úlohy** vyberte **nyní** a pak vyberte **Spustit**.

## <a name="get-support"></a>Získání podpory
Pokud chcete získat další pomoc, vyzkoušejte si naši [stránku Microsoft Q&Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Další kroky
* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](/rest/api/streamanalytics/)