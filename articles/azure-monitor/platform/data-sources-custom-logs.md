---
title: Shromažďování vlastních protokolů ve službě Azure Monitor | Dokumenty společnosti Microsoft
description: Azure Monitor může shromažďovat události z textových souborů na počítačích se systémem Windows i Linux.  Tento článek popisuje, jak definovat nový vlastní protokol a podrobnosti o záznamy, které vytvářejí v Azure Monitoru.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/26/2019
ms.openlocfilehash: 1e889aaef7cd01cd743e8063a8a1dd5138ba9d0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670589"
---
# <a name="custom-logs-in-azure-monitor"></a>Vlastní protokoly ve službě Azure Monitor

Zdroj dat vlastní protokoly v Azure Monitor umožňuje shromažďovat události z textových souborů na počítačích se systémem Windows i Linux. Mnoho aplikací protokoluje informace o textových souborech namísto standardních služeb protokolování, jako je protokol událostí systému Windows nebo Syslog. Po shromáždění můžete data analyzovat do jednotlivých polí v dotazech nebo je během shromažďování extrahovat do jednotlivých polí.

![Vlastní kolekce protokolů](media/data-sources-custom-logs/overview.png)

Shromažďované soubory protokolu musí odpovídat následujícím kritériím.

- Protokol musí mít buď jednu položku na řádek, nebo na začátku každé položky použít časové razítko odpovídající jednomu z následujících formátů.

    YYYY-MM-DD HH:MM:SS<br>M/D/YYYY HH:MM:SS AM/PM<br>Po DD, YYYY HH: MM: SS<br />yyMMdd HH:mm:ss<br />ddMMyy HH:mm:ss<br />MMM d hh:mm:ss<br />dd/MMM/yyyy:HH:mm:ss zzz<br />yyyy-MM-ddTHH:mm:ssK

- Soubor protokolu nesmí umožňovat cyklické protokolování nebo střídání protokolu, kde je soubor přepsán novými položkami.
- Soubor protokolu musí používat kódování ASCII nebo UTF-8.  Jiné formáty, například UTF-16 nejsou podporovány.

>[!NOTE]
> Pokud jsou duplicitní položky v souboru protokolu, Azure Monitor bude shromažďovat. Výsledky dotazu však budou nekonzistentní, kde výsledky filtru ukazují více událostí než počet výsledků. Bude důležité ověřit protokol k určení, zda aplikace, která jej vytvoří, je příčinou tohoto chování a adresu, pokud je to možné před vytvořením vlastní definice kolekce protokolu.  
>

>[!NOTE]
> Pracovní prostor Log Analytics podporuje následující omezení:
> 
> * Lze vytvořit pouze 500 vlastních protokolů.
> * Tabulka podporuje pouze až 500 sloupců. 
> * Maximální počet znaků pro název sloupce je 500. 
>

>[!IMPORTANT]
>Vlastní kolekce protokolů vyžaduje, aby aplikace, která zapisuje soubor protokolu, pravidelně vyprázdnila obsah protokolu na disk. Důvodem je, že vlastní kolekce protokolu závisí na oznámení o změně souborového systému pro sledovaný soubor protokolu.

## <a name="defining-a-custom-log"></a>Definování vlastního protokolu
Pomocí následujícího postupu definujte vlastní soubor protokolu.  Přejděte na konec tohoto článku pro návod ukázky přidání vlastního protokolu.

### <a name="step-1-open-the-custom-log-wizard"></a>Krok 1. Otevření Průvodce vlastním protokolem
Průvodce vlastním protokolem se spustí na webu Azure Portal a umožňuje definovat nový vlastní protokol ke shromažďování.

1. Na portálu Azure vyberte **pracovní prostory Analýzy protokolů** > pracovní prostor > **upřesnit nastavení**.
2. Klikněte na **vlastní** > **protokoly dat**.
3. Ve výchozím nastavení jsou všechny změny konfigurace automaticky odesílány všem agentům. Pro agenty Linuxu je konfigurační soubor odeslán do sběrače dat Fluentd.
4. Kliknutím na **Přidat+** otevřete Průvodce vlastním protokolem.

### <a name="step-2-upload-and-parse-a-sample-log"></a>Krok 2. Nahrání a analýza ukázkového protokolu
Začnete nahráním ukázky vlastního protokolu.  Průvodce provede analýzu a zobrazení položek v tomto souboru, které můžete ověřit.  Azure Monitor použije oddělovač, který zadáte k identifikaci jednotlivých záznamů.

**Nový řádek** je výchozí oddělovač a používá se pro soubory protokolu, které mají jednu položku na řádek.  Pokud řádek začíná datem a časem v jednom z dostupných formátů, můžete zadat oddělovač **časového razítka,** který podporuje položky, které pokrývají více než jeden řádek.

Pokud se používá oddělovač časového razítka, pak TimeGenerated vlastnost každého záznamu uloženého v Azure Monitor bude naplněna datum a čas zadaný pro tuto položku v souboru protokolu.  Pokud se používá nový oddělovač řádků, pak TimeGenerated je naplněn a datum a čas, který Azure Monitor shromážděné položky.

1. Klikněte na **Procházet** a vyhledejte ukázkový soubor.  Všimněte si, že toto tlačítko může být v některých prohlížečích označeno **jako Zvolit soubor.**
2. Klikněte na **Další**.
3. Průvodce vlastním protokolem nahraje soubor a zobrazí seznam záznamů, které identifikuje.
4. Změňte oddělovač, který slouží k identifikaci nového záznamu, a vyberte oddělovač, který nejlépe identifikuje záznamy v souboru protokolu.
5. Klikněte na **Další**.

### <a name="step-3-add-log-collection-paths"></a>Krok 3. Přidání cest shromažďování protokolů
Je nutné definovat jednu nebo více cest na agenta, kde můžete najít vlastní protokol.  Můžete zadat určitou cestu a název souboru protokolu nebo můžete zadat cestu se zástupným znakem pro název. To podporuje aplikace, které vytvářejí nový soubor každý den nebo když jeden soubor dosáhne určité velikosti. Můžete také zadat více cest pro jeden soubor protokolu.

Aplikace může například vytvořit soubor protokolu každý den s datem zahrnutým v názvu jako v log20100316.txt. Vzor pro takový protokol může být *log\*.txt,* který by se vztahoval na libovolný soubor protokolu po schéma pojmenování aplikace.

Následující tabulka obsahuje příklady platných vzorů pro určení různých souborů protokolu.

| Popis | Cesta |
|:--- |:--- |
| Všechny soubory v *C:\Protokoly* s příponou TXT v agentovi systému Windows |C:\Protokoly\\\*.txt |
| Všechny soubory v *C:\Protokoly* s názvem začínajícím loga a příponou TXT v agentovi systému Windows |C:\Protokoly\protokol\*.txt |
| Všechny soubory v */var/log/audit* s příponou TXT na linuxovém agentovi |/var/log/audit/*.txt |
| Všechny soubory v */var/log/audit* s názvem začínajícím logem a příponou .txt na agentovi Linuxu |/var/log/audit/log\*.txt |

1. Výběrem možnosti Windows nebo Linux určete, který formát cesty přidáváte.
2. Zadejte cestu a **+** klepněte na tlačítko.
3. Opakujte postup pro všechny další cesty.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>Krok 4. Zadejte název a popis protokolu.
Název, který zadáte, bude použit pro typ protokolu, jak je popsáno výše.  Vždy skončí s _CL jej odlišit jako vlastní protokol.

1. Zadejte název protokolu.  Přípona ** \_CL** je poskytována automaticky.
2. Přidejte volitelný **popis**.
3. Chcete-li uložit vlastní definici protokolu, klepněte na tlačítko **Další.**

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>Krok 5. Ověření, zda jsou shromažďovány vlastní protokoly
Může trvat až hodinu, než se počáteční data z nového vlastního protokolu zobrazí ve službě Azure Monitor.  Začne shromažďovat položky z protokolů nalezených v cestě, kterou jste zadali od bodu, který jste definovali vlastní protokol.  Nezachová položky, které jste nahráli během vytváření vlastního protokolu, ale bude shromažďovat již existující položky v souborech protokolu, které vyhledá.

Jakmile Azure Monitor začne shromažďovat z vlastního protokolu, jeho záznamy budou k dispozici s dotazem protokolu.  Použijte název, který jste přidali vlastní protokol jako **Typ** v dotazu.

> [!NOTE]
> Pokud v dotazu chybí vlastnost RawData, bude pravděpodobně nutné zavřít a znovu otevřít prohlížeč.

### <a name="step-6-parse-the-custom-log-entries"></a>Krok 6. Analyzovat vlastní položky protokolu
Celá položka protokolu bude uložena v jedné vlastnosti s názvem **RawData**.  S největší pravděpodobností budete chtít oddělit různé informace v každé položce do jednotlivých vlastností pro každý záznam. Informace o možnostech při analýzách **rawdata** na více vlastnostech najdete v části [Analýza textových dat v Azure Monitoru.](../log-query/parse-text.md)

## <a name="removing-a-custom-log"></a>Odebrání vlastního protokolu
Pomocí následujícího postupu na webu Azure Portal odeberte vlastní protokol, který jste dříve definovali.

1. V nabídce **Data** v **rozšířeném nastavení** pracovního prostoru vyberte **Vlastní protokoly,** chcete-li zobrazit všechny vlastní protokoly.
2. Klikněte na **Odebrat** vedle vlastního protokolu, který chcete odebrat.

## <a name="data-collection"></a>Shromažďování dat
Azure Monitor bude shromažďovat nové položky z každého vlastního protokolu přibližně každých 5 minut.  Agent zaznamená své místo v každém souboru protokolu, ze kterého shromažďuje.  Pokud agent přejde do offline po určitou dobu, pak Azure Monitor bude shromažďovat položky z místa, kde naposledy skončil, i v případě, že tyto položky byly vytvořeny v době, kdy agent byl offline.

Celý obsah položky protokolu jsou zapsány do jedné vlastnosti s názvem **RawData**.  Viz [Analýza textových dat v Azure Monitoru](../log-query/parse-text.md) pro metody analyzovat každý importovaný záznam protokolu do více vlastností.

## <a name="custom-log-record-properties"></a>Vlastní vlastnosti záznamu protokolu
Vlastní záznamy protokolu mají typ s názvem protokolu, který zadáte, a vlastnostmi v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| TimeGenerated |Datum a čas, kdy byl záznam shromážděn službou Azure Monitor.  Pokud protokol používá oddělovač založený na čase, pak je to čas shromážděný z položky. |
| SourceSystem |Typ agenta, od kterého byl záznam shromážděn. <br> OpsManager – agent windows, přímé připojení nebo správce operací system center <br> Linux – Všichni linuxoví agenti |
| Rawdata |Úplné znění shromážděné položky. S největší pravděpodobností budete chtít [analyzovat tato data do jednotlivých vlastností](../log-query/parse-text.md). |
| ManagementGroupName |Název skupiny pro správu agentů řízení provozu systémového centra.  Pro ostatní agenty se jedná\<o ID pracovního prostoru AOI.\> |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Ukázkový návod k přidání vlastního protokolu
V následující části prochází příklad vytvoření vlastního protokolu.  Ukázkový protokol, který se shromažďuje, má jednu položku na každém řádku začínající datem a časem a poté pole oddělená čárkami pro kód, stav a zprávu.  Níže je uvedeno několik ukázkových položek.

    2019-08-27 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
    2019-08-27 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
    2019-08-27 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
    2019-08-27 01:38:22 302,Error,Application could not connect to database
    2019-08-27 01:31:34 303,Error,Application lost connection to database

### <a name="upload-and-parse-a-sample-log"></a>Nahrání a analýza ukázkového protokolu
Poskytujeme jeden ze souborů protokolu a můžete vidět události, které bude shromažďovat.  V tomto případě je Nová linka dostatečným oddělovačem.  Pokud jedna položka v protokolu může span více řádků i když, pak oddělovač časového razítka by bylo nutné použít.

![Nahrání a analýza ukázkového protokolu](media/data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Přidání cest shromažďování protokolů
Soubory protokolu budou umístěny v *c:\MyApp\Logs*.  Každý den bude vytvořen nový soubor s názvem, který obsahuje datum v *vzorové aplikaci YYYYMMDD.log*.  Dostatečným vzorem pro tento protokol by byl *\\\*c:\MyApp\Logs .*

![Cesta k kolekci protokolu](media/data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Zadejte název a popis protokolu.
Používáme název *MyApp_CL* a zadejte **popis**.

![Název protokolu](media/data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>Ověření, zda jsou shromažďovány vlastní protokoly
Používáme jednoduchý dotaz *MyApp_CL* vrátit všechny záznamy ze shromážděných protokolu.

![Protokolovat dotaz bez vlastních polí](media/data-sources-custom-logs/query-01.png)


## <a name="alternatives-to-custom-logs"></a>Alternativy k vlastním protokolům
Zatímco vlastní protokoly jsou užitečné, pokud vaše data odpovídají kritériím uvedeným v aplikaci, ale existují případy, jako je například následující, kde potřebujete jinou strategii:

- Data neodpovídají požadované struktuře, jako je například časové razítko v jiném formátu.
- Soubor protokolu nesplňuje požadavky, jako je kódování souborů nebo nepodporovaná struktura složek.
- Data před sběrem vyžadují předběžné zpracování nebo filtrování. 

V případech, kdy vaše data nelze shromažďovat pomocí vlastních protokolů, zvažte následující alternativní strategie:

- K zápisu dat do událostí [systému Windows](data-sources-windows-events.md) nebo [syslogu,](data-sources-syslog.md) která jsou shromažďovány službou Azure Monitor, použijte vlastní skript nebo jinou metodu. 
- Odešlete data přímo do Azure Monitoru pomocí [rozhraní HTTP Data Collector API](data-collector-api.md). 

## <a name="next-steps"></a>Další kroky
* Viz [Analýza textových dat v Azure Monitoru](../log-query/parse-text.md) pro metody analyzovat každý importovaný záznam protokolu do více vlastností.
* Přečtěte si o [dotazech protokolu](../log-query/log-query-overview.md) k analýze dat shromážděných ze zdrojů dat a řešení.
