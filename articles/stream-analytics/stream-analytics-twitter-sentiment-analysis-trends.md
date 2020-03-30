---
title: Analýza mínění na Twitteru v reálném čase s Azure Stream Analytics
description: Tento článek popisuje, jak používat Stream Analytics pro analýzu mínění na Twitteru v reálném čase. Podrobné pokyny od generování událostí až po data na živém řídicím panelu.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 4b265bb574895e4728ad93ee25c9dad0da226ea4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240303"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Analýza subjektivního hodnocení na Twitteru v reálném čase v Azure Stream Analytics

Tento článek vás naučí, jak vytvořit řešení analýzy mínění v sociálních médiích tím, že do Azure Event Hubs přivedete události v reálném čase. Napíšete dotaz Azure Stream Analytics, abyste analyzovali data a ukládali výsledky pro pozdější použití nebo vytvořili řídicí panel [Power BI,](https://powerbi.com/) abyste poskytli přehledy v reálném čase.

Nástroje pro analýzu sociálních médií pomáhají organizacím porozumět trendovým tématům. Populární témata jsou témata a postoje, které mají velký objem příspěvků na sociálních médiích. Analýza sentimentu, která se také nazývá *těžba názorů*, používá nástroje pro analýzu sociálních médií k určení postojů k produktu nebo myšlence. 

Analýza trendů twitteru v reálném čase je skvělým příkladem analytického nástroje, protože model předplatného hashtagu umožňuje poslouchat konkrétní klíčová slova (hashtagy) a rozvíjet analýzu mínění zdroje.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Scénář: Analýza sentimentu sociálních médií v reálném čase

Společnost, která má webové stránky zpravodajských médií, má zájem získat výhodu nad svými konkurenty tím, že obsahuje obsah stránek, který je okamžitě relevantní pro své čtenáře. Společnost používá analýzu sociálních médií na témata, která jsou relevantní pro čtenáře tím, že provádí analýzu sentimentu dat Twitteru v reálném čase.

Chcete-li identifikovat trendová témata v reálném čase na Twitteru, společnost potřebuje analýzy v reálném čase o objemu tweetů a sentimentu pro klíčová témata.

## <a name="prerequisites"></a>Požadavky

V tomto návodu používáte klientskou aplikaci, která se připojuje k Twitteru a hledá tweety, které mají určité hashtagy (které můžete nastavit). Chcete-li spustit aplikaci a analyzovat tweety pomocí Azure Streaming Analytics, musíte mít následující:

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).

* [Účet na Twitteru.](https://twitter.com)

* Aplikace TwitterClientCore, která čte twitterový kanál. Chcete-li získat tuto aplikaci, stáhněte si [TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore).

* Nainstalujte [rozhraní .NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x) verze 2.1.0.

## <a name="create-an-event-hub-for-streaming-input"></a>Vytvoření centra událostí pro streamování vstupu

Ukázková aplikace generuje události a odesílá je do centra událostí Azure. Azure Event Hubs jsou upřednostňovanou metodou příjemudálostí pro Stream Analytics. Další informace najdete v [dokumentaci](../event-hubs/event-hubs-what-is-event-hubs.md)k centru událostí Azure .

### <a name="create-an-event-hub-namespace-and-event-hub"></a>Vytvoření oboru názvů centra událostí a centra událostí
V této části vytvoříte obor názvů centra událostí a přidáte do tohoto oboru názvů centrum událostí. Obory názvů centra událostí se používají k logickému seskupení instancí sběrnice událostí souvisejících s událostmi. 

1. Přihlaste se k portálu Azure a vyberte **Vytvořit prostředek**. Pak. vyhledejte **centra událostí** a vyberte **Vytvořit**.

2. Na stránce **Vytvořit obor názvů** zadejte název oboru názvů. Pro obor názvů můžete použít libovolný název, ale název musí být platný pro adresu URL a musí být jedinečný v rámci Azure. 
    
3. Vyberte cenovou úroveň a předplatné a vytvořte nebo zvolte skupinu prostředků. Potom zvolte umístění a vyberte **Vytvořit**. 
 
4. Po dokončení nasazení oboru názvů přejděte do skupiny prostředků a vyhledejte obor názvů centra událostí v seznamu prostředků Azure. 

5. V novém oboru názvů ** + &nbsp;** vyberte Centrum událostí . 

6. Název nové události hub *socialtwitter-eh*. Můžete použít jiný název. Pokud tak učiníte, poznamenejte si to, protože budete později potřebovat jméno. Pro centrum událostí nemusíte nastavovat žádné další možnosti.
 
7. Vyberte **Vytvořit**.

### <a name="grant-access-to-the-event-hub"></a>Udělení přístupu do centra událostí

Než může proces odesílat data do centra událostí, centrum událostí potřebuje zásadu, která umožňuje přístup. Zásady přístupu vytváří připojovací řetězec, který obsahuje informace o autorizaci.

1.  Na navigačním panelu na levé straně oboru názvů centra událostí vyberte **Rozbočovače událostí**, která se nachází v části **Entity.** Potom vyberte centrum událostí, které jste právě vytvořili.

2.  Na navigačním panelu na levé straně vyberte **Zásady sdíleného přístupu** umístěné v části **Nastavení**.

    >[!NOTE]
    >V části pro obor názvů centra událostí a pro centrum událostí je možnost Zásady sdíleného přístupu. Ujistěte se, že pracujete v kontextu centra událostí, ne v celkovém oboru názvů centra událostí.

3.  Na stránce zásad přístupu vyberte **+ Přidat**. Poté zadejte *přístup na sociální síti* pro název **zásady** a zaškrtněte políčko **Spravovat.**
 
4.  Vyberte **Vytvořit**.

5.  Po nasazení zásady vyberte zásadu ze seznamu zásad sdíleného přístupu.

6.  Najděte pole s názvem **Připojovací řetězec primární klíč** a vyberte tlačítko kopírovat vedle připojovacího řetězce.
 
7.  Vložte připojovací řetězec do textového editoru. Tento připojovací řetězec potřebujete pro další část po proveďte některé malé úpravy.

   Připojovací řetězec vypadá takto:
   
   ```
   Endpoint=sb://EVENTHUBS-NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh
   ```

   Všimněte si, že připojovací řetězec obsahuje více `Endpoint`párů `SharedAccessKeyName` `SharedAccessKey`klíč-hodnota, oddělených středníky: , , , a `EntityPath`.  

   > [!NOTE]
   > Z důvodu zabezpečení byly odebrány části připojovacího řetězce v příkladu.

## <a name="configure-and-start-the-twitter-client-application"></a>Konfigurace a spuštění klientské aplikace Twitter

Klientská aplikace dostane pípání události přímo z Twitteru. Aby tak učinila, potřebuje oprávnění k volání Twitter Streaming API. Chcete-li nakonfigurovat toto oprávnění, vytvořte aplikaci v Rozechvění, která generuje jedinečné přihlašovací údaje (například token OAuth). Potom můžete nakonfigurovat klientskou aplikaci pro použití těchto pověření při volání rozhraní API. 

### <a name="create-a-twitter-application"></a>Vytvoření aplikace Twitter
Pokud ještě nemáte aplikaci Twitter, kterou můžete použít pro tento návod, můžete jej vytvořit. Musíte již mít twitterový účet.

> [!NOTE]
> Přesný proces v Rozechvění pro vytvoření aplikace a získání klíče, tajné klíče a token může změnit. Pokud tyto pokyny neodpovídají tomu, co vidíte na webu Twitter, přečtěte si dokumentaci pro vývojáře twitteru.

1. Ve webovém prohlížeči přejděte na [Twitter pro vývojáře](https://developer.twitter.com/en/apps), vytvořte vývojářský účet a vyberte **Vytvořit aplikaci**. Může se zobrazit zpráva, že je třeba požádat o vývojářský účet Twitter. Nebojte se tak učinit, a poté, co vaše žádost byla schválena, měli byste vidět potvrzovací e-mail. Schválení vývojářského účtu může trvat několik dní.

   ![Podrobnosti o aplikaci Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Podrobnosti o aplikaci Twitter")

2. Na stránce **Create an application** (Vytvoření aplikace) zadejte podrobnosti o nové aplikaci a pak vyberte **Create your Twitter application** (Vytvořit aplikaci Twitter).

   ![Podrobnosti o aplikaci Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Podrobnosti o aplikaci Twitter")

3. Na stránce aplikace vyberte kartu **Klíče a tokeny** a zkopírujte hodnoty pro **klíč rozhraní API příjemce** a tajný klíč rozhraní API pro **spotřebitele**. Chcete-li generovat přístupové tokeny, vyberte také možnost **Vytvořit** v části **Tajný klíč přístupového tokenu a přístupového tokenu.** Zkopírujte hodnoty **Access Token** (Přístupový token) a **Access Token Secret** (Tajný klíč přístupového tokenu).

   Uložte hodnoty, které jste načetli pro aplikaci Twitter. Hodnoty budete potřebovat později.

> [!NOTE]
> Klíče a tajné klíče pro aplikaci Twitter poskytují přístup k vašemu účtu Twitter. Zacházejte s tímto údajem jako s citlivými, stejně jako s vaším heslem pro Twitter. Nevkládejte například tyto informace do aplikace, kterou dáváte ostatním. 

### <a name="configure-the-client-application"></a>Konfigurace klientské aplikace

Vytvořili jsme klientskou aplikaci, která se připojuje k datům Twitteru pomocí [twitterových datových api pro](https://dev.twitter.com/streaming/overview) streamování ke shromažďování událostí tweetu o konkrétní sadě témat.

Před spuštěním aplikace vyžaduje od vás určité informace, jako jsou klávesy Twitter a připojovací řetězec centra událostí.

1. Ujistěte se, že jste si stáhli aplikaci [TwitterClientCore,](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore) jak je uvedeno v požadavcích.

2. K otevření souboru *App.config* použijte textový editor. Proveďte následující změny `<appSettings>` prvku:

   * Nastavte `oauth_consumer_key` na twitterový klíč spotřebitele (klíč rozhraní API). 
   * Nastavte `oauth_consumer_secret` na twitterový tajný klíč spotřebitele (tajný klíč rozhraní API).
   * Nastavte `oauth_token` na token Přístupu k Twitteru.
   * Nastavte `oauth_token_secret` na tajný klíč tokenu přístupu Twitter.
   * Nastavte `EventHubNameConnectionString` připojovací řetězec.
   * Nastavte `EventHubName` název centra událostí (to je hodnota cesty entity).

3. Otevřete příkazový řádek a přejděte do adresáře, kde je umístěna vaše aplikace TwitterClientCore. Pomocí příkazu `dotnet build` k sestavení projektu. Pak pomocí `dotnet run` příkazu spustit aplikaci. Aplikace odesílá tweety do centra událostí.

## <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics

Nyní, když se události tweetu streamují v reálném čase z Twitteru, můžete nastavit úlohu Stream Analytics pro analýzu těchto událostí v reálném čase.

1. Na webu Azure Portal přejděte do skupiny prostředků a vyberte **+ Přidat**. Pak vyhledejte **úlohu Stream Analytics** a vyberte **Vytvořit**.

2. Pojmenujte `socialtwitter-sa-job` úlohu a zadejte předplatné, skupinu prostředků a umístění.

    Je vhodné umístit úlohu a centrum událostí do stejné oblasti pro dosažení nejlepšího výkonu a proto, abyste neplatili za přenos dat mezi oblastmi.

3. Vyberte **Vytvořit**. Po dokončení nasazení přejděte na úlohu.

## <a name="specify-the-job-input"></a>Určení vstupu úlohy

1. V úloze Stream Analytics vyberte **Vstupy** v levé nabídce v části **Topologie úlohy**.

2. Vyberte ** + &nbsp;Přidat centrum**událostí vstupu > **datového proudu**. Vyplňte **nový vstupní** formulář s následujícími informacemi:

   |**Nastavení**  |**Navrhovaná hodnota**  |**Popis**  |
   |---------|---------|---------|
   |Alias vstupu| *TwitterStream* | Zadejte alias pro vstup. |
   |Předplatné  | \<Vaše předplatné\> |  Vyberte předplatné Azure, které chcete použít. |
   |Obor názvů centra událostí | *asa-twitter-eventhub* |
   |Název centra událostí | *socialtwitter-eh* | Zvolte *Použít existující*. Pak vyberte Centrum událostí, které jste vytvořili.|
   |Typ komprese událostí| Gzip | Typ komprese dat.|

   Ponechte zbývající výchozí hodnoty a vyberte **Uložit**.

## <a name="specify-the-job-query"></a>Určení dotazu na úlohu

Stream Analytics podporuje jednoduchý, deklarativní dotaz model, který popisuje transformace. Další informace o jazyce najdete v [tématu Azure Stream Analytics Query Language Reference](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). Tento návod vám pomůže vytvořit a otestovat několik dotazů přes data Twitteru.

Chcete-li porovnat počet zmínek mezi tématy, můžete použít [okno Omílání,](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) abyste získali počet zmínek podle tématu každých pět sekund.

1. V **přehledu**úlohy vyberte **Upravit dotaz** v pravém horním rohu pole Dotaz. Azure uvádí vstupy a výstupy, které jsou nakonfigurované pro úlohu a umožňuje vytvořit dotaz pro transformaci vstupní datový proud, jak je odeslándo výstupu.

2. Změňte dotaz v editoru dotazů na následující:

   ```sql
   SELECT *
   FROM TwitterStream
   ```

3. Data událostí ze zpráv by se měla zobrazit v okně **Vstupní náhled** pod dotazem. Ujistěte se, **že zobrazení** je nastaveno na **JSON**. Pokud nevidíte žádná data, ujistěte se, že váš generátor dat odesílá události do centra událostí a že jste vybrali **GZip** jako typ komprese pro vstup.

4. Vyberte **Testovat dotaz** a všimněte si výsledků v okně **Výsledky testu** pod dotazem.

5. Změňte dotaz v editoru kódu na následující a vyberte **Testovat dotaz**:

   ```sql
   SELECT System.Timestamp as Time, text
   FROM TwitterStream
   WHERE text LIKE '%Azure%'
   ```

6. Tento dotaz vrátí všechny tweety, které obsahují klíčové slovo *Azure*.

## <a name="create-an-output-sink"></a>Vytvoření výstupní jímky

Nyní jste definovali datový proud událostí, vstup centra událostí pro události ingestování a dotaz k provedení transformace přes datový proud. Posledním krokem je definování výstupníjím jímky pro úlohu.  

V tomto návodu k průvodci napíšete agregované události pípání z dotazu úloh do úložiště objektů blob Azure.  Výsledky můžete taky v závislosti na potřebách vaší aplikace směrovat do Azure SQL Database, Azure Table Storage, Event Hubs nebo Power BI.

## <a name="specify-the-job-output"></a>Určení výstupu úlohy

1. V části **Topologie úlohy** v levé navigační nabídce vyberte **Výstupy**. 

2. Na stránce **Výstupy** ** + &nbsp;** klikněte na Přidat a **úložiště objektů blob/Úložiště datových jezer Gen2**:

   * **Výstupní alias**: `TwitterStream-Output`Použijte název . 
   * **Možnosti importu**: Vyberte **vybrat úložiště z předplatných**.
   * **Účet úložiště**. Vyberte svůj účet úložiště.
   * **Kontejner**. Vyberte **Vytvořit** `socialtwitter`nový a zadejte .
   
4. Vyberte **Uložit**.   

## <a name="start-the-job"></a>Spuštění úlohy

Je zadán vstup úlohy, dotaz a výstup. Jste připraveni zahájit úlohu Stream Analytics.

1. Ujistěte se, že je spuštěna aplikace TwitterClientCore. 

2. V přehledu úloh vyberte **Spustit**.

3. Na stránce **Počáteční úloha** vyberte pro **čas zahájení výstupu úlohy** **možnost Nyní** a potom vyberte Možnost **Start**.

## <a name="get-support"></a>Získat podporu
Další pomoc našlápneme na fórum [Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další kroky
* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
