---
title: Shromažďování vlastních protokolů s využitím agenta Log Analytics ve službě Azure Monitor
description: Azure Monitor může shromažďovat události z textových souborů na počítačích s Windows i Linux.  Tento článek popisuje, jak definovat nový vlastní protokol a podrobnosti o záznamech, které vytvoří v Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/21/2020
ms.openlocfilehash: 73496e350a5e40a3945343271b76c6d883991b62
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100610570"
---
# <a name="collect-custom-logs-with-log-analytics-agent-in-azure-monitor"></a>Shromažďování vlastních protokolů s využitím agenta Log Analytics ve službě Azure Monitor

Zdroj dat vlastních protokolů pro agenta Log Analytics v Azure Monitor umožňuje shromažďovat události z textových souborů na počítačích s Windows i Linux. Mnoho aplikací protokoluje informace do textových souborů namísto standardních protokolovacích služeb, jako je například protokol událostí systému Windows nebo syslog. Po shromáždění můžete data analyzovat do jednotlivých polí v dotazech nebo data extrahovat během shromažďování do jednotlivých polí.

> [!IMPORTANT]
> Tento článek popisuje shromažďování vlastních protokolů pomocí [agenta Log Analytics](../platform/log-analytics-agent.md) , který je jedním z agentů používaných Azure monitor. Jiní agenti shromažďují různá data a nakonfigurují se jinak. Seznam dostupných agentů a data, která mohou shromažďovat, najdete v tématu [Přehled agentů Azure monitor](../agents/agents-overview.md) .

![Vlastní kolekce protokolů](media/data-sources-custom-logs/overview.png)

Soubory protokolů, které mají být shromažďovány, se musí shodovat s následujícími kritérii.

- Protokol musí mít buď jednu položku na řádek, nebo použít časové razítko, které odpovídá jednomu z následujících formátů na začátku každé položky.

    RRRR-MM-DD HH: MM: SS<br>M/D/RRRR HH: MM: SS AM/PM<br>Mon DD, RRRR HH: MM: SS<br />rrmmdd HH: mm: SS<br />ddMMyy HH: mm: SS<br />MMM d hh: mm: SS<br />dd/MMM/rrrr: HH: mm: SS ZZZ<br />RRRR-MM-ddTHH: mm: ssK

- Soubor protokolu nesmí umožňovat cyklické protokolování nebo otočení v protokolu, kde je soubor přepsán novými položkami.
- Soubor protokolu musí používat kódování ASCII nebo UTF-8.  Jiné formáty jako UTF-16 se nepodporují.
- Pro Linux se převod časového pásma nepodporuje pro časová razítka v protokolech.

>[!NOTE]
> V případě, že v souboru protokolu existují duplicitní položky, Azure Monitor bude shromažďovat. Výsledky dotazu ale budou nekonzistentní, kde výsledky filtru zobrazují více událostí, než je počet výsledků. Je důležité, abyste ověřili protokol, abyste zjistili, jestli aplikace, která ji vytvořila, toto chování způsobuje, a pokud je to možné, před vytvořením vlastní definice kolekce protokolů ji vyřešit.  
>

>[!NOTE]
> Pracovní prostor Log Analytics podporuje následující omezení:
> 
> * Je možné vytvořit pouze vlastní protokoly 500.
> * Tabulka podporuje pouze sloupce až 500 sloupců. 
> * Maximální počet znaků v názvu sloupce je 500. 
>

>[!IMPORTANT]
>Vlastní kolekce protokolů vyžaduje, aby aplikace, která zapisuje soubor protokolu, vyprázdní obsah protokolu na disk pravidelně. Důvodem je to, že vlastní shromažďování protokolů závisí na upozorněních na změnu systému souborů pro sledovaný soubor protokolu.

## <a name="defining-a-custom-log"></a>Definování vlastního protokolu
K definování vlastního souboru protokolu použijte následující postup.  Posuňte se na konec tohoto článku a Projděte si návod k přidání vlastního protokolu.

### <a name="step-1-open-the-custom-log-wizard"></a>Krok 1. Otevření Průvodce vlastním protokolem
Průvodce vlastním protokolem se spouští v Azure Portal a umožňuje definovat nový vlastní protokol ke shromáždění.

1. V Azure Portal vyberte **Log Analytics pracovní prostory** > > **Rozšířená nastavení**.
2. Klikněte na   >  **vlastní protokoly** dat.
3. Ve výchozím nastavení jsou všechny změny konfigurace automaticky vloženy do všech agentů. Pro agenty Linux se konfigurační soubor pošle do Fluent sběrače dat.
4. Kliknutím na tlačítko **Přidat +** otevřete Průvodce vlastním protokolem.

### <a name="step-2-upload-and-parse-a-sample-log"></a>Krok 2. Nahrát a analyzovat ukázkový protokol
Začnete tím, že nahrajete ukázku vlastního protokolu.  Průvodce bude analyzovat a zobrazovat položky v tomto souboru, aby je bylo možné ověřit.  Azure Monitor použije oddělovač, který zadáte k identifikaci každého záznamu.

**Nový řádek** je výchozí oddělovač, který se používá pro soubory protokolů, které mají jednu položku na řádek.  Pokud řádek začíná datem a časem v jednom z dostupných formátů, můžete zadat oddělovač **časového razítka** , který podporuje položky, které jsou rozloženy na více než jeden řádek.

Pokud se použije oddělovač časového razítka, pak vlastnost TimeGenerated každého záznamu uloženého v Azure Monitor bude naplněna s datem a časem zadanými pro tuto položku v souboru protokolu.  Pokud se použije nový oddělovač řádků, TimeGenerated se naplní datem a časem, které položka Azure Monitor shromáždila.

1. Klikněte na **Procházet** a přejděte k ukázkovému souboru.  Všimněte si, že toto tlačítko může být označeno jako označit v některých prohlížečích **možnost soubor** .
2. Klikněte na **Next** (Další).
3. Průvodce vlastním protokolem nahraje soubor a zobrazí seznam záznamů, které identifikuje.
4. Změňte oddělovač, který se používá k identifikaci nového záznamu a vyberte oddělovač, který nejlépe identifikuje záznamy v souboru protokolu.
5. Klikněte na **Next** (Další).

### <a name="step-3-add-log-collection-paths"></a>Krok 3. Přidat cesty ke kolekcím protokolů
Musíte definovat jednu nebo více cest v agentovi, kde může najít vlastní protokol.  Můžete buď zadat konkrétní cestu a název souboru protokolu, nebo můžete zadat cestu se zástupným znakem pro název. Tato podpora podporuje aplikace, které každý den vytvoří nový soubor, nebo když jeden soubor dosáhne určité velikosti. Můžete také zadat více cest pro jeden soubor protokolu.

Aplikace může například vytvořit soubor protokolu každý den s datem zahrnutým do názvu jako v log20100316.txt. Vzor takového protokolu může být *log \* . txt* , který by se měl vztahovat na libovolný soubor protokolu, který následuje po schématu pojmenování aplikace.

Následující tabulka uvádí příklady platných vzorů k určení různých souborů protokolu.

| Description | Cesta |
|:--- |:--- |
| Všechny soubory v *c:\Logs.* s příponou. txt v agentovi Windows |C:\Logs. \\ \* . txt |
| Všechny soubory v *c:\Logs.* s názvem začínajícím protokolem a příponou. txt v agentovi Windows |C:\Logs\log \* . txt |
| Všechny soubory v */var/log/audit* s příponou. txt v agentovi Linux |/var/log/audit/*. txt |
| Všechny soubory v */var/log/audit* s názvem začínajícím protokolem a příponou. txt v agentovi Linux |/var/log/audit/log \* . txt |

1. Vyberte Windows nebo Linux a určete, který formát cesty přidáváte.
2. Zadejte cestu a klikněte na **+** tlačítko.
3. Opakujte tento postup pro všechny další cesty.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>Krok 4: Zadejte název a popis protokolu.
Název, který zadáte, bude použit pro typ protokolu, jak je popsáno výše.  Bude vždycky končit _CL, aby ho rozlišil jako vlastní protokol.

1. Zadejte název protokolu.  Přípona **\_ CL** se poskytuje automaticky.
2. Přidejte volitelný **Popis**.
3. Kliknutím na tlačítko **Další** uložte definici vlastního protokolu.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>Krok 5. Ověření, jestli se vlastní protokoly shromažďují
Zobrazení počátečních dat z nového vlastního protokolu v Azure Monitor může trvat až hodinu.  Začne shromažďovat záznamy z protokolů nalezených v zadané cestě od okamžiku, kdy jste definovali vlastní protokol.  Nezachová položky, které jste nahráli během vytváření vlastního protokolu, ale budou shromažďovat již existující položky v souborech protokolu, které najde.

Jakmile Azure Monitor začne shromažďovat z vlastního protokolu, budou záznamy k dispozici s dotazem protokolu.  Použijte název, který jste přiřadili vlastní protokol jako **typ** v dotazu.

> [!NOTE]
> Pokud v dotazu chybí vlastnost RawData, může být nutné zavřít a znovu otevřít prohlížeč.

### <a name="step-6-parse-the-custom-log-entries"></a>Krok 6. Analyzovat položky vlastního protokolu
Celý záznam v protokolu bude uložen v jediné vlastnosti s názvem **rawData**.  Pravděpodobně budete chtít jednotlivé údaje v každé položce oddělit do jednotlivých vlastností každého záznamu. Pokud chcete použít možnosti analýzy **rawData** do více vlastností, přečtěte si téma [Analýza textových dat v Azure monitor](../log-query/parse-text.md) .

## <a name="removing-a-custom-log"></a>Odebrání vlastního protokolu
Pomocí následujícího postupu v Azure Portal odeberte vlastní protokol, který jste předtím definovali.

1. V nabídce **data** v **upřesňujících nastaveních** pracovního prostoru vyberte **vlastní protokoly** a seznamte se s vlastními protokoly.
2. Pro odebrání klikněte na **Odebrat** vedle vlastního protokolu.

## <a name="data-collection"></a>Shromažďování dat
Azure Monitor bude shromažďovat nové záznamy z každého vlastního protokolu přibližně každých 5 minut.  Agent zaznamená své místo do každého souboru protokolu, ze kterého bude shromažďovat.  Pokud agent přejde do režimu offline po určitou dobu, bude Azure Monitor shromažďovat položky z místa, kde byla naposledy ponechána, a to i v případě, že byly tyto položky vytvořeny v době, kdy byl agent offline.

Celý obsah záznamu protokolu je zapsán do jediné vlastnosti s názvem **rawData**.  Metody pro analýzu jednotlivých importovaných položek protokolu na více vlastností naleznete v tématu [Analýza textových dat v Azure monitor](../log-query/parse-text.md) .

## <a name="custom-log-record-properties"></a>Vlastnosti záznamu vlastního protokolu
Vlastní záznamy protokolu mají typ s názvem protokolu, který zadáte, a vlastnostmi v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| TimeGenerated |Datum a čas, kdy byl záznam shromážděn nástrojem Azure Monitor.  Pokud protokol používá oddělovač založený na čase, pak se jedná o čas získaný z položky. |
| SourceSystem |Typ agenta, ze kterého byl záznam shromážděn <br> OpsManager – Agent pro Windows, buď Direct Connect, nebo System Center Operations Manager <br> Linux – všichni agenti se systémem Linux |
| RawData |Celý text shromážděné položky Tato data pravděpodobně budete chtít [analyzovat do jednotlivých vlastností](../log-query/parse-text.md). |
| ManagementGroupName |Název skupiny pro správu pro agenty služby System Center Operations Management Agents.  Pro jiné agenty je to AOI-\<workspace ID\> |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Ukázkový návod k přidání vlastního protokolu
V následující části se seznámíte s příkladem vytvoření vlastního protokolu.  Shromážděný vzorový protokol obsahuje jednu položku na každém řádku počínaje datem a časem a pak pole s oddělovači pro kód, stav a zprávu.  Níže je uvedeno několik ukázkových položek.

```output
2019-08-27 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2019-08-27 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2019-08-27 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2019-08-27 01:38:22 302,Error,Application could not connect to database
2019-08-27 01:31:34 303,Error,Application lost connection to database
```

### <a name="upload-and-parse-a-sample-log"></a>Nahrát a analyzovat ukázkový protokol
Poskytujeme jeden ze souborů protokolu a uvidí události, které bude shromažďovat.  V tomto případě je nový řádek dostatečným oddělovačem.  Pokud by mohla jedna položka v protokolu zahrnovat více řádků, je nutné použít oddělovač časového razítka.

![Nahrát a analyzovat ukázkový protokol](media/data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Přidat cesty ke kolekcím protokolů
Soubory protokolu se budou nacházet v *C:\MyApp\Logs*.  Každý den se vytvoří nový soubor s názvem, který bude obsahovat datum ve vzoru *appYYYYMMDD. log*.  Dostatečným vzorem pro tento protokol by byl *C:\MyApp\Logs \\ \* . log*.

![Cesta ke kolekci protokolů](media/data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Zadejte název a popis protokolu.
Používáme název *MyApp_CL* a do **popisu** zadáte.

![Název protokolu](media/data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>Ověření, jestli se vlastní protokoly shromažďují
K vrácení všech záznamů z shromážděného protokolu používáme jednoduchý dotaz na *MyApp_CL* .

![Dotaz protokolu bez vlastních polí](media/data-sources-custom-logs/query-01.png)


## <a name="alternatives-to-custom-logs"></a>Alternativy k vlastním protokolům
I když jsou vlastní protokoly užitečné v případě, že se data vejdou na výše uvedená kritéria, existují případy, jako například následující, kde potřebujete jinou strategii:

- Data neodpovídají požadované struktuře, jako je například časové razítko v jiném formátu.
- Soubor protokolu nedodržuje požadavky, jako je například kódování souboru nebo nepodporovaná struktura složky.
- Data vyžadují před shromažďováním předběžné zpracování nebo filtrování. 

V případech, kdy vaše data nejde shromažďovat s vlastními protokoly, zvažte následující alternativní strategie:

- Pomocí vlastního skriptu nebo jiné metody Zapište data do událostí nebo [syslog](data-sources-syslog.md) [systému Windows](data-sources-windows-events.md) , které jsou shromažďovány pomocí Azure monitor. 
- Odešlete data přímo do Azure Monitor pomocí [rozhraní API kolekce dat http](../platform/data-collector-api.md). 

## <a name="next-steps"></a>Další kroky
* Metody pro analýzu jednotlivých importovaných položek protokolu na více vlastností naleznete v tématu [Analýza textových dat v Azure monitor](../log-query/parse-text.md) .
* Přečtěte si o [dotazech protokolů](../log-query/log-query-overview.md) , které analyzují data shromážděná ze zdrojů dat a řešení.
