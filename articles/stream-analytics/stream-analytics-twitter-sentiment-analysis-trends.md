---
title: Analýzy v reálném čase postojích Twitter pomocí služby Azure Stream Analytics
description: Tento článek popisuje postup použití Stream Analytics ke postojích analýzy v reálném čase služby Twitter. Podrobné pokyny z generování událostí k datům na řídicím panelu za provozu.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/29/2017
ms.openlocfilehash: 4c77c8a7209825477929ddc0997b75ace5fe04a1
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
ms.locfileid: "30910522"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Analýzy v reálném čase postojích Twitter v Azure Stream Analytics

Naučte se vytvářet řešení analysis postojích pro analýzy sociálních médií tak, že převedou do centra událostí Azure v reálném čase události služby Twitter. Můžete pak použijte zápisu dotaz Azure Stream Analytics k analýze dat a buď ukládat výsledky pro později, nebo použijte řídicí panel a [Power BI](https://powerbi.com/) poskytnout statistiky v reálném čase.

Analýza nástrojů sociálních médiích pomůžou organizacím pochopit trendů témata. Trendů témata jsou témata a postojích, které mají k velkému počtu příspěvcích v sociálních sítích. Postojích analýzy, která se označuje taky jako *stanovisko dolování*, nástrojů sociálních médiích analytics používá k určení postoje směrem k produktu, je vhodné a tak dále. 

Analýza trendu v reálném čase Twitter je skvělé příklad nástroj pro analýzu, protože modelu předplatného hashtag umožňuje naslouchání na konkrétní klíčová slova (hashtags) a vývoj postojích analysis informačního kanálu.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Scénář: Sociálních médií postojích analýzy v reálném čase

Společnost, která má web zprávy média má zájem o získání několik výhod před konkurencí podle s funkcí obsah webu, okamžitě vztahující se k jeho čtečky. Společnost používá analýzy sociálních médiích na témata, která jsou relevantní pro čtečky díky v reálném čase postojích analýza dat Twitteru.

K identifikaci trendů témata v reálném čase v síti Twitter, musí společnost analýzu v reálném čase o tweet svazku a postojích klíče témata. Jinými slovy potřeba je modul postojích analysis analytics je založena na této sociálních médií informačního kanálu.

## <a name="prerequisites"></a>Požadavky
V tomto kurzu použijete klientská aplikace, která se připojuje k Twitteru a hledá tweetů, které mají určité hashtags (kterou můžete nastavit). Aby bylo možné spouštět aplikace a analyzovat tweetů pomocí Azure streamování Analytics, musíte mít následující:

* Předplatné Azure
* Účet na Twitteru 
* Aplikace pomocí služby Twitter a [přístupového tokenu OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens) pro příslušnou aplikaci. Poskytujeme vysoké úrovně pokyny, jak vytvořit aplikaci služby Twitter později.
* Aplikace TwitterWPFClient, který čte informační kanál sítě Twitter. Chcete-li získat tuto aplikaci, stáhněte [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) z Githubu a potom rozbalte balíček do složky v počítači. Pokud chcete zobrazit zdrojový kód a spusťte aplikaci v ladicí program, můžete získat zdrojového kódu z [Githubu](https://aka.ms/azure-stream-analytics-telcogenerator). 

## <a name="create-an-event-hub-for-streaming-analytics-input"></a>Vytvoření centra událostí pro vstup analýzy datových proudů

Ukázkové aplikace vygeneruje události a jejich nabízených oznámení do centra událostí Azure. Azure event hubs je upřednostňovaný způsob přijímání událostí pro Stream Analytics. Další informace najdete v tématu [dokumentace Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md).


### <a name="create-an-event-hub-namespace-and-event-hub"></a>Vytvoření centra událostí obor názvů a centra událostí
V tomto postupu vytvoříte na obor názvů centra událostí a poté přidejte centra událostí do daného oboru názvů. Obory názvů centra událostí se používají k logickému seskupení souvisejících událostí sběrnice instancí. 

1. Přihlaste se k portálu Azure a klikněte na tlačítko **vytvořit prostředek** > **Internet věcí** > **centra událostí**. 

2. V **vytvoření oboru názvů** okno, zadejte název oboru názvů, jako `<yourname>-socialtwitter-eh-ns`. Můžete použít libovolný název pro obor názvů, ale název musí být platné adresy URL a musí být jedinečný v Azure. 
    
3. Vyberte předplatné a vytvořit nebo vybrat skupinu prostředků a potom klikněte na tlačítko **vytvořit**. 

    ![Vytvoření oboru názvů události rozbočovače](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-namespace.png)
 
4. Po dokončení nasazení obor názvů v seznamu prostředků Azure najdete názvový prostor události rozbočovače. 

5. Klikněte na tlačítko Nový obor názvů a v okně obor názvů, klikněte na tlačítko  **+ &nbsp;centra událostí**. 

    ![Tlačítko Přidat centra událostí pro vytvoření nového centra událostí ](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-button.png)    
 
6. Název nového centra událostí `socialtwitter-eh`. Můžete použít jiný název. Pokud tak učiníte, poznamenejte si, protože potřebujete název později. Nemusíte nastavte další možnosti pro centra událostí.

    ![Okno pro vytvoření nového centra událostí](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub.png)
 
7. Klikněte na možnost **Vytvořit**.


### <a name="grant-access-to-the-event-hub"></a>Udělení přístupu do centra událostí

Předtím, než se proces může odesílat data do centra událostí, musí mít centra událostí zásadu, která umožňuje odpovídající přístup. Zásady přístupu vytvoří připojovací řetězec, který obsahuje informace o ověření.

1.  V okně oboru názvů událostí, klikněte na tlačítko **Event Hubs** a pak klikněte na název vašeho nového centra událostí.

2.  V okně centra událostí, klikněte na **zásady sdíleného přístupu** a pak klikněte na  **+ &nbsp;přidat**.

    >[!NOTE]
    >Ujistěte se, že pracujete s centrem událostí, není oboru názvů centra událostí.

3.  Přidat zásadu s názvem `socialtwitter-access` a **deklarace identity**, vyberte **spravovat**.

    ![Okno pro vytvoření nové zásady přístupu centra událostí](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-shared-access-policy-manage.png)
 
4.  Klikněte na možnost **Vytvořit**.

5.  Po nasazení zásady, klikněte na něj v seznamu zásady sdíleného přístupu.

6.  Vyhledejte seznam **PŘIPOJOVACÍ řetězec primární klíč** a klikněte na tlačítko Kopírovat vedle připojovací řetězec. 
    
    ![Kopírování klíče primární připojovací řetězec ze zásad přístupu](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-shared-access-policy-copy-connection-string.png)
 
7.  Vložte připojovací řetězec do textového editoru. Tento připojovací řetězec pro další části, musíte po provedení některé malé úpravy.

    Připojovací řetězec vypadá takto:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh

    Všimněte si, že připojovací řetězec obsahuje více párů klíč hodnota oddělené středníky: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`, a `EntityPath`.  

    > [!NOTE]
    > Pro zabezpečení se odebraly částí připojovacího řetězce v příkladu.

8.  V textovém editoru, odeberte `EntityPath` pár z připojovacího řetězce (Nezapomeňte odebrat středník předcházejícího). Když jste hotovi, připojovací řetězec vypadá takto:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=


## <a name="configure-and-start-the-twitter-client-application"></a>Nakonfigurovat a spustit klientskou aplikaci služby Twitter.
Klientská aplikace získá tweet události přímo ze služby Twitter. Chcete-li tak učinit, potřebuje oprávnění k volání API služby Twitter streamování. Pokud chcete nakonfigurovat tato oprávnění, vytvoříte aplikaci v Twitter, který generuje jedinečné přihlašovací údaje (například tokenu OAuth). Potom můžete nakonfigurovat klientskou aplikaci použít tyto přihlašovací údaje při volání rozhraní API. 

### <a name="create-a-twitter-application"></a>Vytvoření aplikace Twitter
Pokud již nemáte Twitter aplikace, která můžete použít pro tento kurz, můžete vytvořit jeden. Již musí mít účet služby Twitter.

> [!NOTE]
> Přesný postup v Twitter pro vytváření aplikací a získávání klíčů, tajných klíčů a token může změnit. Pokud tyto pokyny neodpovídají, najdete na webu služby Twitter, naleznete v dokumentaci pro vývojáře služby Twitter.

1. Přejděte na [stránku Správa aplikací Twitter](https://apps.twitter.com/). 

2. Vytvoření nové aplikace. 

    * Pro adresu URL webu zadejte platnou adresu URL. Nemá být živý web. (Není možné určit pouze `localhost`.)
    * Pole zpětného volání zůstat prázdné. Klientská aplikace, kterou použijete pro tento kurz nevyžaduje zpětných volání.

    ![Vytváření aplikací v Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/create-twitter-application.png)

3. Volitelně můžete změňte aplikaci oprávnění jen pro čtení.

4. Při vytvoření aplikace, přejděte na **klíče a přístupové tokeny** stránky.

5. Klikněte na tlačítko Generovat token a přístup tajný klíč přístupového tokenu.

Ponechat tyto informace užitečné, protože je budete potřebovat v dalším postupu.

>[!NOTE]
>Klíče a tajné klíče pro aplikaci služby Twitter poskytují přístup k vašemu účtu služby Twitter. S takovými informacemi nakládat jako velká a malá písmena, stejné jako heslo služby Twitter. Například Nevnořujte tyto informace v aplikaci, která poskytnout ostatním uživatelům. 


### <a name="configure-the-client-application"></a>Nakonfigurovat klientskou aplikaci
Vytvořili jsme klientskou aplikaci, která se připojuje k dat pomocí služby Twitter [rozhraní API pro Streaming na Twitteru](https://dev.twitter.com/streaming/overview) shromažďování událostí tweet o konkrétní sadu témat. Aplikace používá [Sentiment140](http://help.sentiment140.com/) nástroj s otevřeným zdrojem, který se přiřadí každé tweet následující hodnotu postojích:

* 0 = záporná
* 2 = neutral
* 4 = kladné

Po události tweet byly přiřazeny postojích hodnotu, se instaluje do centra událostí, který jste vytvořili dříve.

Předtím, než je aplikace spuštěná, vyžaduje určité informace z vašeho počítače, jako jsou služby Twitter klíče a připojovací řetězec centra událostí. Můžete zadat informace o konfiguraci těmito způsoby:

* Spusťte aplikaci a pomocí uživatelského rozhraní aplikace zadejte klíčů, tajné klíče a připojovací řetězec. Pokud to uděláte, informace o konfiguraci se používá pro aktuální relaci, ale nebyla uložena.
* Upravte soubor .config aplikace a nastavte hodnoty existuje. Tento přístup udržuje informace o konfiguraci, ale je také znamená, že tento potenciálně citlivé informace je uložena ve formátu prostého textu ve vašem počítači.

Následující postup dokumenty obou přístupů. 

1. Zkontrolujte, že jste stažené a rozbalené [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) aplikaci, jak je uvedené v předpokladech.

2. Chcete-li nastavit hodnoty v době běhu (a pouze pro aktuální relaci) spustit `TwitterWPFClient.exe` aplikace. Pokud vás aplikace vyzve, zadejte následující hodnoty:

    * Twitter uživatelský klíč (klíč rozhraní API).
    * Twitter uživatelský tajný klíč (tajný klíč rozhraní API).
    * Přístupový Token služby Twitter.
    * Na Twitteru tajný klíč přístupového tokenu.
    * Připojovací řetězec informace, které jste předtím uložili. Ujistěte se, že používáte připojovací řetězec, který jste odebrali `EntityPath` dvojice klíč hodnota z.
    * Twitter klíčová slova, která chcete určit postojích pro.

   ![TwitterWpfClient aplikace spuštěna, zobrazující zakryt nastavení](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

3. Pokud chcete trvale nastavit hodnoty, pomocí textového editoru otevřete soubor TwitterWpfClient.exe.config. Potom v `<appSettings>` elementu, to udělat:

    * Nastavit `oauth_consumer_key` na uživatelský klíč pro Twitter (klíč rozhraní API). 
    * Nastavit `oauth_consumer_secret` na Twitteru uživatelský tajný klíč (tajný klíč rozhraní API).
    * Nastavit `oauth_token` na přístupový Token služby Twitter.
    * Nastavit `oauth_token_secret` na Twitteru tajný klíč tokenu přístupu.

    Dále v `<appSettings>` elementu, tyto změny:

    * Nastavit `EventHubName` na název centra událostí (to znamená na hodnotu cesty entity).
    * Nastavit `EventHubNameConnectionString` připojovací řetězec. Ujistěte se, že používáte připojovací řetězec, který jste odebrali `EntityPath` dvojice klíč hodnota z.

    `<appSettings>` Části vypadá jako v následujícím příkladu. (Pro přehlednost a zabezpečení, jsme zabalená některé řádky a odebrat některé znaky.)

    ![Konfigurační soubor aplikace TwitterWpfClient v textovém editoru, zobrazující Twitter klíče a tajné klíče a informace o události rozbočovače připojovacím řetězci](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-tiwtter-app-config.png)
 
4. Pokud již jste nezahájili aplikace, spusťte nyní TwitterWpfClient.exe. 

5. Klepněte na tlačítko start zelená ke shromažďování sociálních postojích. Tweet události se zobrazí **CreatedAt**, **tématu**, a **SentimentScore** hodnoty, které jsou odesílány do vašeho centra událostí.

    ![Spuštění aplikace TwitterWpfClient zobrazující seznam tweetů](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-app-listing.png)

    >[!NOTE]
    >Pokud se zobrazí chyby a nevidíte proud tweetů zobrazí v dolní části okna, zkontrolujte klíče a tajné klíče. Také zkontrolujte připojovací řetězec (ujistěte se, že nebude obsahovat `EntityPath` klíče a hodnoty.)


## <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics

Teď, když jsou události tweet streamování v reálném čase z Twitteru, můžete nastavit úlohu služby Stream Analytics k analýze těchto událostí v reálném čase.

1. Na portálu Azure klikněte na tlačítko **vytvořit prostředek** > **Internet věcí** > **úlohy služby Stream Analytics**.

2. Název úlohy `socialtwitter-sa-job` a určete předplatné, skupinu prostředků a umístění.

    Je vhodné umístit úlohy a centra událostí ve stejné oblasti pro nejlepší výkon, a tak, že nemáte platíte k přenosu dat mezi oblastmi.

    ![Vytvoření nové úlohy Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

3. Klikněte na možnost **Vytvořit**.

    Vytvoření úlohy a na portálu se zobrazí podrobnosti úlohy.


## <a name="specify-the-job-input"></a>Zadejte vstup úlohy

1. V úloze Stream Analytics v části **úlohy topologie** uprostřed okno úlohy, klikněte na tlačítko **vstupy**. 

2. V **vstupy** okně klikněte na tlačítko  **+ &nbsp;přidat** a potom vyplňte v okně s těmito hodnotami:

    * **Vstupní alias**: použijte název `TwitterStream`. Pokud použijete jiný název, poznamenejte si jeho protože ji budete potřebovat později.
    * **Typ zdroje**: vyberte **datový proud**.
    * **Zdroj**: vyberte **centra událostí**.
    * **Import možnost**: vyberte **použijte Centrum událostí z aktuálního předplatného**. 
    * **Obor názvů sběrnice**: Vyberte obor názvů centra událostí, který jste vytvořili dříve (`<yourname>-socialtwitter-eh-ns`).
    * **Centra událostí**: Vyberte centra událostí, které jste vytvořili dříve (`socialtwitter-eh`).
    * **Název zásady centra událostí**: vyberte zásady přístupu, které jste vytvořili dříve (`socialtwitter-access`).

    ![Vytvořit nový vstupní úlohy streamování Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-new-input.png)

3. Klikněte na možnost **Vytvořit**.


## <a name="specify-the-job-query"></a>Zadejte dotaz úlohy

Stream Analytics podporuje jednoduchý a deklarativní dotazu model, který popisuje transformace. Další informace o jazyk, najdete v článku [referenční příručka Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx).  Tento kurz vám pomůže vytvořit a vyzkoušet několik dotazů přes Twitter data.

Chcete-li porovnejte počet zmínkami mezi témata, můžete použít [Přeskakující okno](https://msdn.microsoft.com/library/azure/dn835055.aspx) získat počet zmínkami podle tématu každých pět sekund.

1. Zavřít **vstupy** okno, pokud jste tak ještě neučinili.

2. V okně úlohy klikněte **dotazu** pole. Azure uvádí vstupy a výstupy, které jsou nakonfigurovány pro úlohy a umožňuje vám vytvořit dotaz, který vám umožňuje transformovat vstupní datový proud, jako jsou odeslána do výstupu.

3. Ujistěte se, že je spuštěna aplikace TwitterWpfClient. 

3. V **dotazu** okně klikněte na tlačítko se tečkami vedle `TwitterStream` vstup a pak vyberte **vzorová data ze vstupu**.

    ![Položka, s "Ukázkových dat ze vstupu" vybrané úlohy možnosti nabídky ukázková data pro analýzy datových proudů](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-sample-data-from-input.png)

    Otevře se okno, které umožňuje určit, kolik ukázková data získat, definována v tom, jak dlouho ke čtení vstupního datového proudu.

4. Nastavit **minut** na 3 a pak klikněte na **OK**. 
    
    ![Možnosti vzorkování vstupního datového proudu s vybrané "3 minut".](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-input-create-sample-data.png)

    Azure ukázky data z vstupního datového proudu za 3 minuty a vás upozorní, jakmile je připraven ukázková data. (To trvá nějakou dobu.) 

    Ukázková data jsou ukládána dočasně a je k dispozici při otevření okna dotazu. Pokud zavřete okno dotazu se zahodí ukázková data a je nutné vytvořit novou sadu ukázková data. 

5. Změna dotazu v editoru kódu takto:

    ```
    SELECT System.Timestamp as Time, Topic, COUNT(*)
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY TUMBLINGWINDOW(s, 5), Topic
    ```

    Pokud nepoužili `TwitterStream` jako alias pro vstup, nahraďte váš alias pro `TwitterStream` v dotazu.  

    Tento dotaz používá **TIMESTAMP BY** – klíčové slovo a určete pole časového razítka v datové části pro použití v dočasné výpočty. Pokud toto pole nezadáte, operace oddílová se provádí na základě doby, které byly přijaty každé události centra událostí. Další informace najdete v části "Čas doručení vs doba aplikace" [referenční příručka Stream Analytics Query](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Tento dotaz také přistoupí časového razítka na konci každé okno pomocí **System.Timestamp** vlastnost.

5. Klikněte na tlačítko **Test**. Spuštění dotazu na data, která jste vzorků.
    
6. Klikněte na **Uložit**. To umožňuje ušetřit dotaz jako součást úlohy streamování Analytics. (Ho nebude uložen ukázková data.)


## <a name="experiment-using-different-fields-from-the-stream"></a>Experiment pomocí různých polí z datového proudu 

Následující tabulka uvádí pole, které jsou součástí JSON streamovaných dat užitečné. Nebojte se, že experiment v editoru dotazů.

|Vlastnost JSON | Definice|
|--- | ---|
|CreatedAt | Čas, který byl vytvořen tweet|
|Téma | Téma, které odpovídá zadané klíčové slovo|
|SentimentScore | Skóre postojích z Sentiment140|
|Autor | Odeslaný tweet popisovač služby Twitter.|
|Text | Úplné tělo tweet|


## <a name="create-an-output-sink"></a>Vytvoření výstupní jímku

Nyní jste definovali datového proudu událostí, centra událostí vstup pro načítání událostí a dotaz, který provádět prostřednictvím datového proudu transformace. Posledním krokem je definování výstupní jímku pro úlohu.  

V tomto kurzu můžete zapsat události agregované tweet z dotazu úlohy do úložiště objektů Blob v Azure.  Výsledky můžete také nabízené k databázi SQL Azure, Azure Table storage, Event Hubs, nebo Power BI, v závislosti na vaší aplikace potřebuje.

## <a name="specify-the-job-output"></a>Zadejte výstup úlohy

1. V **úlohy topologie** klikněte na položku **výstup** pole. 

2. V **výstupy** okně klikněte na tlačítko  **+ &nbsp;přidat** a potom vyplňte v okně s těmito hodnotami:

    * **Alias pro výstup**: použijte název `TwitterStream-Output`. 
    * **Jímky**: vyberte **úložiště objektů Blob**.
    * **Možnosti importu**: vyberte **pomocí úložiště objektů blob z aktuálního předplatného**.
    * **Účet úložiště**. Vyberte **vytvořit nový účet úložiště.**
    * **Účet úložiště** (druhé pole). Zadejte `YOURNAMEsa`, kde `YOURNAME` je název vaší nebo jiné jedinečné řetězce. Název můžete použít jenom malá písmena a čísla a musí být jedinečný v Azure. 
    * **Kontejner**. Zadejte `socialtwitter`.
    Název účtu úložiště a název kontejneru se používají společně zajistit identifikátor URI pro úložiště objektů blob, například takto: 

    `http://YOURNAMEsa.blob.core.windows.net/socialtwitter/...`
    
    !["Nové výstup" okno úlohy Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-output-blob-storage.png)
    
4. Klikněte na možnost **Vytvořit**. 

    Azure vytvoří účet úložiště a automaticky vygeneruje klíč. 

5. Zavřít **výstupy** okno. 


## <a name="start-the-job"></a>Spustit úlohu

Úloha vstup, dotaz a výstup nejsou zadány. Jste připraveni začít úlohu služby Stream Analytics.

1. Ujistěte se, že je spuštěna aplikace TwitterWpfClient. 

2. V okně úlohy klikněte na tlačítko **spustit**.

    ![Spustit úlohu služby Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-output.png)

3. V **spuštění úlohy** okně pro **výstup úlohy počáteční čas**, vyberte **nyní** a pak klikněte na **spustit**. 

    !["Spuštění úlohy" okno pro úlohu služby Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-job-blade.png)

    Azure vás upozorní, jakmile byla úloha spuštěna, a v okně úlohy, stav se zobrazí jako **systémem**.

    ![Spuštěná úloha](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>Zobrazit výstup pro analýzu postojích

Po zahájení systémem úlohu zpracovává datový proud v reálném čase Twitter, můžete zobrazit výstup pro analýzu postojích.

Můžete použít nástroje, jako je [Azure Storage Explorer](https://http://storageexplorer.com/) nebo [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction) k zobrazení výstupu úlohy v reálném čase. Tady můžete použít [Power BI](https://powerbi.com/) rozšířit vaše aplikace a zahrnují přizpůsobený řídicí panel stejný, jako je znázorněno na následujícím snímku obrazovky:

![Power BI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)


## <a name="create-another-query-to-identify-trending-topics"></a>Vytvořte další dotaz k identifikaci trendů témata

Podle další dotaz můžete použít k pochopení sentimentu Twitter [posuvné okno](https://msdn.microsoft.com/library/azure/dn835051.aspx). K identifikaci trendů témata, vyhledejte témata, které zasahují prahová hodnota pro zmínkami ve stanoveném čase.

Pro účely tohoto kurzu vyhledejte témata, která jsou uveden více než 20 výskyty v posledních 5 sekund.

1. V okně úlohy klikněte na tlačítko **Zastavit** k zastavení úlohy. 

2. V **úlohy topologie** klikněte na položku **dotazu** pole. 

3. Změňte dotaz na následující:

    ```    
    SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY SLIDINGWINDOW(s, 5), topic
    HAVING COUNT(*) > 20
    ```

4. Klikněte na **Uložit**.

5. Ujistěte se, že je spuštěna aplikace TwitterWpfClient. 

6. Klikněte na tlačítko **spustit** k restartování úlohy pomocí nový dotaz.


## <a name="get-support"></a>Získat podporu
Pro další pomoc, vyzkoušejte naše [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
