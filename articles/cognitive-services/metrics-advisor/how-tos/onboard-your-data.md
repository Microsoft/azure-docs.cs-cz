---
title: Připojení datového kanálu k Advisoru metrik
titleSuffix: Azure Cognitive Services
description: Jak začít s připojováním datových kanálů k poradci metrik.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: fe3b87c733f54d8bd52c4d973977e3c8cbfefe19
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043191"
---
# <a name="how-to-onboard-your-metric-data-to-metrics-advisor"></a>Postupy: připojení dat metrik do poradce metrik

V tomto článku se dozvíte, jak získat data do poradce metrik. 

## <a name="data-schema-requirements-and-configuration"></a>Požadavky a konfigurace schématu dat

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

## <a name="avoid-loading-partial-data"></a>Vyhnout se načítání částečných dat

Částečná data způsobují nekonzistence mezi daty uloženými v poradci metrik a zdroji dat. K tomu může dojít, když se zdroj dat aktualizuje poté, co poradce pro metriky dokončí přijímání dat. Poradce metriky získává data pouze jednou z daného zdroje dat.

Například pokud byla metrika připojená k nástroji Advisor metriky pro monitorování. Poradce metriky úspěšně přibližuje data metriky v časovém razítku a a v ní provádí detekci anomálií. Pokud se ale data metriky tohoto konkrétního časového razítka A aktualizovala po ingestování dat. Nová hodnota dat nebude načtena.

Můžete zkusit zpětně [vyplnit](manage-data-feeds.md#backfill-your-data-feed) historická data (popsaná dále), abyste zmírnili nekonzistence, ale neaktivovali jsme nové výstrahy anomálií, pokud už výstrahy pro tyto časové body již byly aktivovány. Tento proces může do systému přidat další úlohy a není automatický.

Aby nedošlo k načítání částečných dat, doporučujeme dva způsoby:

* Generovat data v jedné transakci:

    Zajistěte, aby hodnoty metrik pro všechny kombinace dimenzí ve stejném časovém razítku byly uloženy do zdroje dat v jedné transakci. Ve výše uvedeném příkladu počkejte, dokud nebudou data ze všech zdrojů dat připravena, a pak je načtěte do poradce metrik v jedné transakci. Poradce pro metriky může datový kanál pravidelně dotazovat, dokud nejsou data úspěšně (nebo částečně) načtena.

* Odložit příjem dat nastavením správné hodnoty pro parametr **posunu doby přijímání** :

    Nastavte parametr **posunu času** příjmu pro datový kanál, aby se zpozdil příjem dat, dokud nejsou data plně připravená. To může být užitečné pro některé zdroje dat, které nepodporují transakce jako Azure Table Storage. Podrobnosti najdete v tématu [Rozšířená nastavení](manage-data-feeds.md#advanced-settings) .

## <a name="add-a-data-feed-using-the-web-based-workspace"></a>Přidání datového kanálu pomocí webového pracovního prostoru

Po přihlášení k portálu Poradce pro metriky a výběru pracovního **prostoru klikněte na Začínáme.** Pak na hlavní stránce pracovního prostoru klikněte v levé nabídce na **přidat datový kanál** .

### <a name="add-connection-settings"></a>Přidat nastavení připojení

Dále zadáte sadu parametrů pro připojení zdroje dat časové řady. 
* **Typ zdroje**: typ zdroje dat, ve kterém jsou uložená data časových řad.
* **Členitost**: interval mezi po sobě jdoucími datovými body v datech časové řady. Aktuální metrika podporuje: roční, měsíční, týdenní, denní, hodinová a vlastní. Nejnižší interval, který podporuje možnost přizpůsobení, je 60 sekund.
  * **Sekund**: počet sekund, po které je *GranularityName* nastaveno na *přizpůsobení*.
* Ingestovat **data od (UTC)**: začátek základního času pro příjem dat. *startOffsetInSeconds* se často používá k přidání posunu, který vám umožní zajistit konzistenci dat.

V dalším kroku budete muset zadat informace o připojení pro zdroj dat a vlastní dotazy použité k převodu dat do požadovaného schématu. Podrobnosti o ostatních polích a připojení různých typů zdrojů dat najdete v tématu [Přidání datových kanálů z různých zdrojů dat](../data-feeds-from-different-sources.md).

[!INCLUDE [query requirements](../includes/query-requirements.md)]

### <a name="verify-and-get-schema"></a>Ověření a získání schématu

Po nastavení připojovacího řetězce a řetězce dotazu vyberte možnost **ověřit a získat schéma** pro ověření připojení a spusťte dotaz pro získání schématu dat ze zdroje dat. Normálně trvá několik sekund v závislosti na připojení ke zdroji dat. Pokud v tomto kroku dojde k chybě, potvrďte, že:

* Připojovací řetězec a dotaz jsou správné.
* Instance poradce metriky se může připojit ke zdroji dat, pokud se nachází v nastavení brány firewall.

### <a name="schema-configuration"></a>Konfigurace schématu

Po načtení schématu dat vyberte příslušná pole.

Pokud je časové razítko datového bodu vynecháno, poradce metriky použije časové razítko při ingestování datového bodu. U každého datového kanálu můžete zadat maximálně jeden sloupec jako časové razítko. Pokud se zobrazí zpráva, že sloupec nelze zadat jako časové razítko, ověřte dotaz nebo zdroj dat a určete, zda je ve výsledku dotazu více časových razítek – nejenom v datech verze Preview. Při příjmu dat může poradce pro metriky spotřebovat pouze jeden blok (například jeden den, jednu hodinu podle členitosti) dat časových řad z daného zdroje.

|Výběr  |Popis  |Poznámky  |
|---------|---------|---------|
| **Zobrazované jméno** | Název, který se má zobrazit v pracovním prostoru místo původního názvu sloupce | |
|**Timestamp**     | Časové razítko datového bodu. Pokud tento parametr vynecháte, poradce metriky použije časové razítko, když se místo toho bude přijímat datový bod. U každého datového kanálu můžete zadat maximálně jeden sloupec jako časové razítko.        | Nepovinný parametr. By měl být zadaný s maximálně jedním sloupcem. Pokud získáte sloupec, který **nelze zadat jako chybu časového razítka** , ověřte, zda dotaz nebo zdroj dat má duplicitní časová razítka.      |
|**Measure**     |  Číselné hodnoty v datovém kanálu. U každého datového kanálu můžete zadat více měr, ale jako míru by měl být vybrán alespoň jeden sloupec.        | By měla být zadána alespoň v jednom sloupci.        |
|**Rozměr**     | Kategorií hodnoty. Kombinace různých hodnot identifikuje konkrétní časovou řadu s jednou dimenzí, například: Country (země), Language (tenant). Jako rozměry můžete vybrat nula nebo více sloupců. Poznámka: při výběru sloupce bez řetězce jako dimenze buďte opatrní. | Nepovinný parametr.        |
|**Ignorovat**     | Ignoruje vybraný sloupec.        | Nepovinný parametr. Podívejte se na následující text.       |

Pokud chcete sloupce ignorovat, doporučujeme, abyste aktualizovali dotaz nebo zdroj dat, aby tyto sloupce vyloučily. Můžete také ignorovat sloupce pomocí **Ignorovat sloupce** a potom je **Ignorovat** na konkrétní sloupce. Pokud by měl být sloupec dimenze a je omylem nastaven jako *ignorovaný*, Poradce pro metriky může ukončit ingestování částečných dat. Například Předpokládejme, že data z dotazu jsou uvedená níže:

| ID řádku | Timestamp | Země | Jazyk | Income |
| --- | --- | --- | --- | --- |
| 1 | 2019/11/10 | Čína | ZH-CN | 10000 |
| 2 | 2019/11/10 | Čína | EN-US | 1000 |
| 3 | 2019/11/10 | USA | ZH-CN | 12000 |
| 4 | 2019/11/11 | USA | EN-US | 23000 |
| ... | ...| ... | ... | ... |

Pokud má *země* hodnotu dimenze a *jazyk* je nastaven jako *ignorovaný*, budou mít první a druhý řádek stejné rozměry. Poradce pro metriky bude libovolně používat jednu hodnotu ze dvou řádků. Poradce pro metriky nebude v tomto případě agregovat řádky.

### <a name="automatic-roll-up-settings"></a>Nastavení automatického shrnutí

> [!IMPORTANT]
> Pokud chcete povolit analýzu hlavní příčiny a další diagnostické funkce, je nutné nakonfigurovat **Nastavení automatického shrnutí** . Po povolení se nastavení automatického zahrnutí nedá změnit.

Poradce metriky může automaticky provádět agregaci (například SUM, MAX, MIN) v každé dimenzi během příjmu a pak vytvoří hierarchii, která bude použita při analýze kořenového případu a dalších diagnostických funkcí. 

Zvažte následující scénáře:

* *Nemusíte zahrnovat souhrnnou analýzu pro moje data.*

    Nemusíte používat kumulativní aktualizaci pro metriky.

* *Moje data již byla zahrnuta a hodnota dimenze je reprezentována hodnotou NULL nebo prázdné (výchozí), pouze NULL, jiné.*

    Tato možnost znamená, že nástroj pro vyhodnocení metrik nemusí data shrnout, protože řádky už jsou shrnuté. Pokud například vyberete *pouze hodnotu null*, bude druhý řádek dat v následujícím příkladu zobrazen jako agregace všech zemí a jazyk *en-US*; Čtvrtý řádek dat, který má prázdnou hodnotu pro *zemi* , se ale bude zobrazovat jako běžný řádek, který může označovat neúplná data.
    
    | Země | Jazyk | Income |
    |---------|----------|--------|
    | Čína   | ZH-CN    | 10000  |
    | PLATNOST  | EN-US    | 999999 |
    | USA      | EN-US    | 12000  |
    |         | EN-US    | 5000   |

* *Potřebuji pro shrnutí svých dat nástroj Advisor metriky, a to tak, že vypočítá hodnoty Sum/Max/min/AVG/Count a reprezentují je <some string>*

    Některé zdroje dat, například Cosmos DB nebo Azure Blob Storage, nepodporují určité výpočty, jako je například *Group by* nebo *Cube*. Poradce metrik nabízí možnost Shrnutí k automatickému generování datové krychle během přijímání.
    Tato možnost znamená, že k výpočtu souhrnu pomocí algoritmu, který jste vybrali, potřebujete nástroj Advisor metriky a pomocí zadaného řetězce zastupujete zahrnutí do poradce metrik. Tato změna nemění žádná data ve zdroji dat.
    Předpokládejme například, že máte sadu časových řad, která představuje prodejní metriky s dimenzí (země, oblast). U daného časového razítka může vypadat takto:


    | Country (Země)       | Region (Oblast)           | Sales |
    |---------------|------------------|-------|
    | Kanada        | Alberta          | 100   |
    | Kanada        | British Columbia | 500   |
    | USA | Montana          | 100   |


    Po povolení automatického shrnutí se *souhrnem*metriky Advisor vypočítá kombinace dimenzí a sečte metriky během příjmu dat. Výsledek může být následující:

    | Country (Země)       | Region (Oblast)           | Sales |
    | ------------ | --------------- | ---- |
    | Kanada        | Alberta          | 100   |
    | NULL          | Alberta          | 100   |
    | Kanada        | British Columbia | 500   |
    | NULL          | British Columbia | 500   |
    | USA | Montana          | 100   |
    | NULL          | Montana          | 100   |
    | NULL          | NULL             | 700   |
    | Kanada        | NULL             | 600   |
    | USA | NULL             | 100   |

    `(Country=Canada, Region=NULL, Sales=600)` znamená, že součet prodejů v Kanadě (všechny oblasti) je 600.

    Následuje transformace v jazyce SQL.

    ```mssql
    SELECT
        dimension_1,
        dimension_2,
        ...
        dimension_n,
        sum (metrics_1) AS metrics_1,
        sum (metrics_2) AS metrics_2,
        ...
        sum (metrics_n) AS metrics_n
    FROM
        each_timestamp_data
    GROUP BY
        CUBE (dimension_1, dimension_2, ..., dimension_n);
    ```

    Než použijete funkci automatického zahrnutí, zvažte následující:

    * Pokud chcete použít *součet* k agregaci dat, ujistěte se, že vaše metriky jsou v každé dimenzi doplňkové. Tady je několik příkladů *neaditivních* metrik:
      * Metriky založené na zlomcích. To zahrnuje poměr, procento atd. Například byste neměli přidat míru nezaměstnanosti každého stavu pro výpočet míry nezaměstnanosti celé země.
      * Překrývání v dimenzi. Například byste neměli do každého sportu přidat počet lidí, abyste mohli vypočítat počet lidí, kteří rádi spokojeni, protože mezi nimi je překrytí, jedna osoba může jako více sportovních.
    * Pro zajištění stavu celého systému je velikost datové krychle omezená. V současné době je limit 1 000 000. Pokud vaše data překročí tento limit, ingestování pro toto časové razítko se nezdaří.

## <a name="advanced-settings"></a>Rozšířená nastavení

K dispozici je několik pokročilých nastavení, která umožňují přizpůsobovat data přizpůsobeným způsobem, například určení posunu příjmu nebo souběžnosti. Další informace najdete v části [Rozšířená nastavení](manage-data-feeds.md#advanced-settings) v článku Správa datového kanálu.

## <a name="specify-a-name-for-the-data-feed-and-check-the-ingestion-progress"></a>Zadejte název datového kanálu a ověřte průběh přijímání.
 
Zadejte vlastní název datového kanálu, který se zobrazí ve vašem pracovním prostoru. Pak klikněte na **Odeslat**. Na stránce s podrobnostmi datového kanálu můžete k zobrazení informací o stavu použít indikátor průběhu přijímání.

:::image type="content" source="../media/datafeeds/ingestion-progress.png" alt-text="Indikátor průběhu přijímání" lightbox="../media/datafeeds/ingestion-progress.png":::


Zjištění podrobností o selhání ingestování: 

1. Klikněte na **Zobrazit podrobnosti**.
2. Klikněte na **stav** a pak zvolte **neúspěšné** nebo **Chyba**.
3. Najeďte myší na neúspěšnou příjem dat a podívejte se na zobrazenou zprávu s podrobnostmi.

:::image type="content" source="../media/datafeeds/check-failed-ingestion.png" alt-text="Indikátor průběhu přijímání":::

Stav *selhání* znamená, že ingestování pro tento zdroj dat se bude opakovat později.
*Chybový* stav označuje, že se pro zdroj dat neopakuje Poradce pro metriky. Chcete-li znovu načíst data, je třeba aktivovat ruční naplnění nebo znovu načíst.

Průběh příjmu můžete znovu znovu načíst kliknutím na možnost **aktualizovat průběh**. Po dokončení přijímání dat můžete kliknout na metriky a kontrolovat výsledky detekce anomálií.

## <a name="next-steps"></a>Další kroky
- [Správa datových kanálů](manage-data-feeds.md)
- [Konfigurace pro různé zdroje dat](../data-feeds-from-different-sources.md)
- [Konfigurace metrik a doladění konfigurace zjišťování](configure-metrics.md)
