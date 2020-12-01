---
title: 'Rychlý Start: webový portál Poradce pro metriky'
titleSuffix: Azure Cognitive Services
description: Naučte se, jak začít používat webový portál Advisor metriky.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 09/30/2020
ms.author: mbullwin
ms.openlocfilehash: 5dbf98d363429e6d22a0b7719cdc669deebd21a0
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348632"
---
# <a name="quickstart-monitor-your-first-metric-using-the-web-portal"></a>Rychlý Start: monitorování první metriky pomocí webového portálu

Při zřizování instance služby Advisor metriky můžete použít rozhraní API a webový pracovní prostor pro práci se službou. Webový pracovní prostor lze použít jako jednoduchý způsob, jak rychle začít se službou. Poskytuje také vizuální způsob konfigurace nastavení, přizpůsobení modelu a provádění analýz hlavní příčiny. 

* Připojení dat metriky
* Zobrazit vaše metriky a vizualizace
* Jemné ladění konfigurací detekce
* Prozkoumejte diagnostické poznatky
* Vytvoření a přihlášení k odběru výstrah anomálií

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* Jakmile budete mít předplatné Azure, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title=" vytvořte prostředek Advisoru metriky "  target="_blank"> vytvořením prostředku Advisoru metriky <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal k nasazení instance služby Advisor pro metriky.  

    
> [!TIP]
> * K nasazení prostředku Advisoru metriky může být 10 až 30 minut. Po úspěšném nasazení klikněte na **Přejít k prostředku** .
> * Pokud chcete použít REST API ke komunikaci se službou, budete potřebovat klíč a koncový bod z vytvořeného prostředku. Můžete je najít na kartě  **klíče a koncové body** ve vytvořeném prostředku.

Tento dokument používá SQL Database jako příklad pro vytvoření prvního monitoru.

## <a name="sign-in-to-your-workspace"></a>Přihlaste se ke svému pracovnímu prostoru.

Po vytvoření prostředku se přihlaste k [portálu Poradce pro metriky](https://go.microsoft.com/fwlink/?linkid=2143774). Vyberte svůj pracovní prostor, abyste mohli začít monitorovat vaše metriky. 
 
V současné době můžete v každé dostupné oblasti vytvořit jeden prostředek Advisoru metriky. Pracovní prostory můžete kdykoli přepnout na portálu pro poradce pro metriky.


## <a name="onboard-time-series-data"></a>Připojování dat časových řad

Poradce pro metriky poskytuje konektory pro různé zdroje dat, jako jsou SQL Database, Azure Průzkumník dat a Azure Table Storage. Postup pro připojení dat je podobný pro různé konektory, i když se některé parametry konfigurace můžou lišit. Požadované parametry pro konkrétní zdroje dat najdete v tématu [připojení dat z různých zdrojů](../data-feeds-from-different-sources.md) .

V tomto rychlém startu se jako příklad používá SQL Database. Pomocí stejných kroků můžete také přijímat vlastní data.

Začněte tím, že se přihlásíte k pracovnímu prostoru Advisor metriky s vaším účtem služby Active Directory. Z cílové stránky vyberte svůj **adresář**, **předplatné** a **pracovní prostor** , který jste právě vytvořili, a pak klikněte na **Začínáme**. Po načtení hlavní stránky úlohy vyberte v nabídce vlevo možnost **přidat datový kanál** .

### <a name="data-schema-requirements-and-configuration"></a>Požadavky a konfigurace schématu dat

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

### <a name="configure-connection-settings"></a>Konfigurovat nastavení připojení

> [!TIP]
> Podrobnosti o dostupných parametrech najdete v tématu [Postup přidání datových kanálů](../how-tos/onboard-your-data.md) .

Přidejte datový kanál tím, že se připojíte k datovému zdroji časových řad. Začněte výběrem následujících parametrů:

* **Typ zdroje**: typ zdroje dat, ve kterém jsou uložená data časových řad.
* **Členitost**: interval mezi po sobě jdoucími datovými body v datech časových řad, například ročně, měsíčně, denně. Minimální interval přizpůsobení je 60 sekund.
* Ingestovat **data od (UTC)**: počáteční čas pro příjem prvního časového razítka. 


Dále zadejte **připojovací řetězec** s přihlašovacími údaji pro váš zdroj dat a vlastní **dotaz**. Dotaz slouží k určení dat k ingestování a převodu do požadovaného schématu.

[!INCLUDE [query requirements](../includes/query-requirements.md)]

:::image type="content" source="../media/connection-settings.png" alt-text="Nastavení připojení" lightbox="../media/connection-settings.png":::


### <a name="verify-the-connection-and-load-the-data-schema"></a>Ověření připojení a načtení schématu dat

Po vytvoření připojovacího řetězce a řetězce dotazu vyberte možnost **ověřit a získat schéma** pro ověření připojení a spusťte dotaz pro získání schématu dat ze zdroje dat. Normálně trvá několik sekund v závislosti na připojení ke zdroji dat. Pokud v tomto kroku dojde k chybě, potvrďte, že:

1. Připojovací řetězec a dotaz jsou správné.
2. Instance poradce metriky se může připojit ke zdroji dat, pokud se nachází v nastavení brány firewall.

### <a name="schema-configuration"></a>Konfigurace schématu

Po načtení schématu dat a zobrazení jako níže vyberte příslušná pole.


|Výběr  |Popis  |Poznámky  |
|---------|---------|---------|
|**Timestamp**     | Časové razítko datového bodu. Pokud tento parametr vynecháte, poradce metriky použije časové razítko, když se místo toho bude přijímat datový bod. U každého datového kanálu můžete zadat maximálně jeden sloupec jako časové razítko.        | Nepovinný parametr. By měl být zadaný s maximálně jedním sloupcem.       |
|**Míra**     |  Číselné hodnoty v datovém kanálu. U každého datového kanálu můžete zadat více měr, ale jako míru by měl být vybrán alespoň jeden sloupec.        | By měla být zadána alespoň v jednom sloupci.        |
|**Rozměr**     | Kategorií hodnoty. Kombinace různých hodnot identifikuje konkrétní časovou řadu s jednou dimenzí, například: Country (země), Language (tenant). Jako rozměry můžete vybrat žádný nebo libovolný počet sloupců. Poznámka: Pokud jako dimenzi vybíráte sloupec, který není typu řetězec, buďte opatrní při rozbalení dimenze. | Nepovinný parametr.        |
|**Ohled**     | Ignoruje vybraný sloupec.        |         |


:::image type="content" source="../media/schema-configuration.png" alt-text="Konfigurace schématu" lightbox="../media/schema-configuration.png":::

### <a name="automatic-roll-up-settings"></a>Nastavení automatického shrnutí

> [!IMPORTANT]
> Pokud chcete povolit **analýzu hlavní příčiny** a další diagnostické funkce, je nutné nakonfigurovat nastavení automatické shrnutí. Po povolení nejde nastavení automatického shrnutí změnit.

Poradce metriky může automaticky provádět agregaci (SUM/MAX/MIN...) v každé dimenzi během ingestování a pak vytvoří hierarchii, která bude použita při analýze kořenového případu a dalších diagnostických funkcí. Další podrobnosti najdete v tématu [Automatická kumulativní nastavení](../how-tos/onboard-your-data.md#automatic-roll-up-settings) .

Zadejte vlastní název datového kanálu, který se zobrazí ve vašem pracovním prostoru. Klikněte na **Odeslat**. 

## <a name="tune-detection-configuration"></a>Ladit konfiguraci zjišťování

Po přidání datového kanálu se Poradce pro metriky pokusí ingestovat data metriky od zadaného počátečního data. Bude nějakou dobu trvat, než se data dostanou úplně ingestovat, a kliknutím na **průběh** příjmu v horní části stránky datového kanálu se můžete podívat na stav přijímání. Pokud jsou data ingestovaná, bude Poradce pro metriky používat detekci a dál monitorovat zdroj pro nová data.

Po použití detekce klikněte na jednu z metrik uvedených v datovém kanálu, abyste našli **stránku podrobností metriky** : 
- Zobrazit vizualizace všech řezů časových řad v této metrikě
- Aktualizace zjišťování konfigurace, aby splňovala očekávané výsledky
- Nastavení oznámení pro zjištěné anomálie

:::image type="content" source="../media/metric-details.png" alt-text="Podrobnosti metriky" lightbox="../media/metric-details.png":::

## <a name="view-the-diagnostic-insights"></a>Zobrazit diagnostické přehledy

Po optimalizaci konfigurace zjišťování by nalezené anomálie měly odrážet skutečné anomálie ve vašich datech. Poradce pro metriky provádí analýzu multidimenzionálních metrik, jako je clusteringu anomálií, korelace incidentů a analýza hlavní příčiny. Pomocí těchto funkcí můžete analyzovat a diagnostikovat incidenty ve vašich datech.

Pokud chcete zobrazit diagnostické poznatky, klikněte na červené tečky v vizualizacích časových řad, které reprezentují zjištěné anomálie. Zobrazí se okno s odkazem na stránku analýzy incidentů. 

:::image type="content" source="../media/incident-link.png" alt-text="Odkaz na incident" lightbox="../media/incident-link.png":::

Po kliknutí na tento odkaz bude na stránce analýzy incidentů převedená analýza incidentu, která analyzuje odpovídající anomálii a spoustu diagnostických poznatků. V horní části se zobrazí statistické údaje o incidentu, jako je **závažnost**, ovlivnění **anomálií** a **čas spuštění** a **čas ukončení**. 

V dalším kroku se zobrazí anomálie nadřazeného incidentu a automatizovaná Rada pro hlavní příčiny. Tato automatizovaná pomocná událost je vygenerována analýzou stromové struktury všech souvisejících anomálií, včetně odchylek, distribuce a příspěvků nadřazených anomálií. 

:::image type="content" source="../media/incident-diagnostic.png" alt-text="Diagnostika incidentů" lightbox="../media/incident-diagnostic.png":::

Na základě těchto informací už můžete získat přehled o tom, co se děje, a ovlivnit dopad incidentu a také největší možnou hlavní příčinu. Proto by mohla být provedena okamžitá akce pro vyřešení incidentu co nejdříve. 

Ale můžete si také projít více diagnostických přehledů, které využívají další funkce k přechodu k podrobnostem anomálií podle dimenzí, zobrazení podobných anomálií a porovnávání napříč metrikami. Další informace najdete v tématu [Postup: Diagnostika incidentu](../how-tos/diagnose-incident.md). 

## <a name="get-notified-when-new-anomalies-are-found"></a>Dostávat oznámení, když se najde nové anomálie

Pokud chcete dostávat upozornění, když se ve vašich datech zjistí anomálii, můžete vytvořit předplatné pro jednu nebo více vašich metrik. Poradce pro metriky používá k odesílání výstrah zavěšení. Podporují se tři typy háčků: zavěšení e-mailu, Webhook a Azure DevOps. Jako příklad použijeme Webhook. 

### <a name="create-a-web-hook"></a>Vytvoření webového zavěšení

Webhook je vstupní bod, který získá anomálii upozorňující z programového způsobu služby Advisor, který při aktivaci výstrahy volá uživatelské rozhraní API. Podrobnosti o tom, jak vytvořit zavěšení, najdete v části **Vytvoření zavěšení** v tématu [Postupy: Konfigurace výstrah a získání oznámení pomocí zavěšení](../how-tos/alerts.md#create-a-hook). 

### <a name="configure-alert-settings"></a>Konfigurace nastavení upozornění

Po vytvoření zavěšení se v nastavení výstrahy určí, jak se mají posílat oznámení o výstrahách. U každé metriky můžete nastavit více nastavení výstrah. k dispozici jsou dvě důležitá nastavení **výstrah, pro** které se určují anomálie, které se mají zahrnout, a **filtrovat možnosti anomálií** , které definují anomálie zahrnuté do výstrahy. Další podrobnosti najdete v části Postup pro **Přidání nebo úpravu nastavení výstrah** v tématu [Postupy: Konfigurace výstrah a získání oznámení pomocí zavěšení](../how-tos/alerts.md#add-or-edit-alert-settings) .


## <a name="next-steps"></a>Další kroky

- [Zprovoznění datových kanálů](../how-tos/onboard-your-data.md)
    - [Správa datových kanálů](../how-tos/manage-data-feeds.md)
    - [Konfigurace pro různé zdroje dat](../data-feeds-from-different-sources.md)
- [Použití REST API nebo klientských knihoven](./rest-api-and-client-library.md)
- [Konfigurace metrik a doladění konfigurace zjišťování](../how-tos/configure-metrics.md)