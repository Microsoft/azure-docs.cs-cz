---
title: Shromažďovat vlastní protokoly ve službě Azure Log Analytics | Dokumentace Microsoftu
description: Log Analytics může shromažďovat události z textových souborů v počítačích se systémy Windows a Linux.  Tento článek popisuje, jak definovat nové vlastní protokol a podrobnosti o záznamy, které vytvářejí v pracovním prostoru Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: aca7f6bb-6f53-4fd4-a45c-93f12ead4ae1
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 2f0c552c29021400e901e94c643c8f20171638b8
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52875056"
---
# <a name="custom-logs-in-log-analytics"></a>Vlastní protokolů v Log Analytics
Zdroj dat vlastních protokolů v Log Analytics umožňuje shromažďovat události z textových souborů v počítačích se systémy Windows a Linux. Mnoho aplikací protokolovat informace k textovým souborům místo standardní protokolování služby, jako je protokol událostí Windows nebo protokolu Syslog.  Po shromáždění, můžete analyzovat každý záznam v přihlášení u jednotlivých polí pomocí [vlastní pole](../../azure-monitor/platform/custom-fields.md) funkce Log Analytics.

![Kolekce vlastních protokolů](media/data-sources-custom-logs/overview.png)

Soubory protokolů, které se mají shromažďovat musí odpovídat následujícím kritériím.

- V protokolu musí mít každý řádek jednu položku nebo použít časové razítko odpovídající jednomu z následujících formátů na začátku každé položky.

    RRRR MM-DD HH: MM:<br>M/D/RRRR HH: MM: SS AM/PM<br>MON DD, rrrr hh: mm:<br />rrmmdd hh: mm:<br />ddmmrr hh: mm:<br />MMM d hh<br />dd/MMM/yyyy:HH:mm:ss zzz<br />rrrr-MM-ddTHH:mm:ssK

- Soubor protokolu nesmí povolit cyklické protokolování nebo rotace protokolů, kde je soubor přepsán pomocí nové položky.
- Soubor protokolu musí používat kódování ASCII nebo UTF-8.  Nejsou podporovány jiných formátů, například UTF-16.

>[!NOTE]
>Pokud existují duplicitní položky v souboru protokolu, Log Analytics je shromažďovat.  Ale výsledky hledání budou nekonzistentní kde vyfiltrovali výsledky zobrazit více událostí, než počet výsledků.  Je důležité, abyste ověřili protokol, který chcete zjistit, zda je aplikace, která ji vytvoří, příčinou tohoto chování a pokud je to možné jej vyřešit před vytvořením definice kolekce vlastní protokol.  
>
  
>[!NOTE]
> Pokud vaše aplikace vytvoří nový soubor protokolu, každý den nebo když dosáhne určité velikosti, agenta Log Analytics pro Linux nevyhledává až po restartování. Je to proto, že agent pouze vytvoří výčet a zahájí monitorování pro vzory pomocí zadaného protokolů při spuštění a z tohoto důvodu je potřeba naplánovat automatickou restartování agenta.  Toto omezení neexistuje pomocí agenta Log Analytics pro Windows.  
>

## <a name="defining-a-custom-log"></a>Definování vlastního protokolu
Následující postup použijte k definování vlastního souboru protokolu.  Přejděte na konci tohoto článku návod vzorku přidání vlastního protokolu.

### <a name="step-1-open-the-custom-log-wizard"></a>Krok 1. Spustit Průvodce účtem vlastního protokolu
Vlastní Průvodce protokolované běží na webu Azure Portal a můžete zadat nový vlastní protokol ke shromažďování.

1. Na webu Azure Portal, vyberte **Log Analytics** > váš pracovní prostor > **Upřesnit nastavení**.
2. Klikněte na **Data** > **vlastní protokoly**.
3. Standardně jsou všechny změny konfigurace automaticky nahrány do všech agentů.  U agentů systému Linux je konfigurační soubor odeslané do kolektoru dat Fluentd.  Pokud chcete upravit tento soubor ručně na každý agent systému Linux, poté zrušte zaškrtnutí políčka *použít uvedenou konfiguraci u mých Linuxových počítačů*.
4. Klikněte na tlačítko **přidat +** otevřete Průvodce vlastní protokol.

### <a name="step-2-upload-and-parse-a-sample-log"></a>Krok 2. Uložit a analyzovat ukázkový protokol
Začnete tím, že nahrajete vzorek vlastního protokolu.  Tento průvodce analyzovat a zobrazit položky v tomto souboru pro vás k ověření.  Log Analytics se použít oddělovač, který zadáte pro identifikaci každý záznam.

**Nový řádek** je výchozím oddělovačem a používá se pro soubory protokolů, které mají jednu položku na každý řádek.  Pokud řádek začíná datum a čas v jednom z formátů k dispozici, pak můžete zadat **časové razítko** oddělovač, který podporuje položky, které zahrnují více než jeden řádek.

Pokud se používá oddělovač časové razítko, pak vlastnost TimeGenerated každého záznamu uložené v Log Analytics se plnit datum a čas zadaný pro tuto položku v souboru protokolu.  Pokud se používá nový oddělovač řádků, je TimeGenerated vyplní s datem a časem, Log Analytics shromažďovat položce.


1. Klikněte na tlačítko **Procházet** a přejděte do ukázkového souboru.  Všimněte si, že to může tlačítko může být označeno jako **zvolit soubor** v některé prohlížeče.
2. Klikněte na **Další**.
3. Průvodce protokolu vlastní soubor nahrát a seznam záznamů, které identifikuje.
4. Změňte oddělovač, který se používá k identifikaci nový záznam a vyberte oddělovač, který nejlépe identifikuje záznamy v souboru protokolu.
5. Klikněte na **Další**.

### <a name="step-3-add-log-collection-paths"></a>Krok 3. Přidat cesty ke kolekcím protokolů
Na agentovi, kde najdou vlastního protokolu je nutné zadat jednu nebo více cest.  Můžete buď zadat konkrétní cestu a název souboru protokolu, nebo se zástupnými znaky pro název můžete zadat cestu. To podporuje aplikace, které každý den nebo když jeden soubor dosáhne určité velikosti, vytvořte nový soubor. Můžete také zadat více cest pro jeden soubor protokolu.

Například aplikace může vytvořit soubor protokolu každý den s datem zahrnuté v názvu jako log20100316.txt. Vzor pro tyto protokoly mohou být *protokolu\*.txt* názvů které bude platit pro jakéhokoli souboru protokolu po aplikaci prvku schéma.

>[!NOTE]
> Pokud vaše aplikace vytvoří nový soubor protokolu, každý den nebo když dosáhne určité velikosti, agenta Log Analytics pro Linux nevyhledává až po restartování. Je to proto, že agent pouze vytvoří výčet a zahájí monitorování pro vzory pomocí zadaného protokolů při spuštění a z tohoto důvodu je potřeba naplánovat automatickou restartování agenta.  Toto omezení neexistuje pomocí agenta Log Analytics pro Windows.  
>

Následující tabulka obsahuje příklady platných vzorů k určení různých protokolových souborech.

| Popis | Cesta |
|:--- |:--- |
| Všechny soubory v *C:\Logs* s příponou .txt agenta Windows |C:\Logs\\\*.txt |
| Všechny soubory v *C:\Logs* s názvem počínaje protokolu a příponu .txt agenta Windows |C:\Logs\log\*.txt |
| Všechny soubory v */var/log/audit* s příponou .txt na agenta pro Linux |/var/log/audit/*.txt |
| Všechny soubory v */var/log/audit* s názvem počínaje protokolu a příponu .txt na agenta pro Linux |/var/log/audit/log\*txt |

1. Vyberte Windows nebo Linux k určení, jaký formát cesty, kterou přidáváte.
2. Zadejte cestu a kliknutím **+** tlačítko.
3. Postup opakujte pro všechny další cesty.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>Krok 4. Zadejte název a popis pro protokol
Vámi určeným názvem se použije pro typ protokolu, jak je popsáno výše.  Vždy skončí s _CL odlišující jej jako vlastní protokol.

1. Zadejte název protokolu.   **\_CL** přípona je poskytována automaticky.
2. Přidat volitelný **popis**.
3. Klikněte na tlačítko **Další** se uložit definici vlastního protokolu.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>Krok 5. Ověřte, že je shromažďováno vlastní protokoly
To může trvat až hodinu počáteční data z nového vlastního protokolu se zobrazí v Log Analytics.  Spustí shromažďování položky z protokolů vyhledána v cestě zadané od bodu, který jste definovali vlastního protokolu.  Nezachovají položky, které jste nahráli při vytváření vlastního protokolu, ale bude shromažďovat soubory protokolů, které je možné vyhledat již existující položky.

Po spuštění shromažďování z vlastního protokolu Log Analytics bude k dispozici s prohledáváním protokolů své záznamy.  Použijte název, který jste zadali vlastní protokol, jako **typ** v dotazu.

> [!NOTE]
> Pokud chybí vlastnost RawData hledání, budete muset zavřít a znovu otevřít prohlížeč.
>
>

### <a name="step-6-parse-the-custom-log-entries"></a>Krok 6. Analyzovat položky vlastního protokolu
Celý záznam se uloží v jedné vlastnosti **RawData**.  Budete pravděpodobně chtít oddělit různé druhy údajů v každé položky do jednotlivých vlastností uložených v záznamu.  Můžete to udělat [vlastní pole](../../azure-monitor/platform/custom-fields.md) funkce Log Analytics.

Podrobné kroky postupu k analýze položka vlastního protokolu nejsou uvedeny zde.  Najdete [vlastní pole](../../azure-monitor/platform/custom-fields.md) dokumentaci pro tyto informace.

## <a name="removing-a-custom-log"></a>Odebírá vlastní protokol
Pomocí následujícího postupu na webu Azure Portal odeberete vlastní protokol, který jste dříve definovali.

1. Z **Data** v nabídce **Upřesnit nastavení** pro váš pracovní prostor, vyberte **vlastní protokoly** vypsat všechny vlastní protokoly.
2. Klikněte na tlačítko **odebrat** vedle vlastní protokol na odebrat.


## <a name="data-collection"></a>Shromažďování dat
Log Analytics bude shromažďovat nové položky z každého vlastního protokolu přibližně každých 5 minut.  Agent zaznamená příslušné místo v jednotlivých souborů protokolu, který shromažďuje z.  Pokud agenta přejde do režimu offline pro určitou dobu, bude položky od posledního místa, shromažďovat Log Analytics i v případě, že tyto položky byly vytvořeny v době, kdy agent offline.

Celý obsah položky protokolu se zapisují do jedné vlastnosti **RawData**.  Je možné analyzovat do více vlastností, které je možné analyzovat a prohledávat samostatně tak, že definujete [vlastních polí](../../azure-monitor/platform/custom-fields.md) po vytvoření vlastního protokolu.

## <a name="custom-log-record-properties"></a>Vlastnosti záznamu vlastního protokolu
Vlastní protokol záznamy mají typ, který je zadat název protokolu a vlastnosti v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| TimeGenerated |Datum a čas, který záznam se shromážděná službou Log Analytics.  Pokud v protokolu používá oddělovač podle času to je čas odebrané položky. |
| SourceSystem |Typ agenta, který záznam se shromažďovala ze. <br> OpsManager – Windows agent, buď přímé připojení nebo System Center Operations Manager <br> Linux – všichni agenti systému Linux |
| RawData |Shromážděné vstupu textu v plném znění. |
| ManagementGroupName |Název skupiny pro správu pro agenty System Center Operations Manageru.  Pro ostatní agenty to je AOI -\<ID pracovního prostoru\> |

## <a name="log-searches-with-custom-log-records"></a>Prohledávání protokolů se záznamy vlastního protokolu
V pracovním prostoru Log Analytics, stejně jako záznamy z jakéhokoli jiného zdroje dat jsou uloženy záznamy z vlastní protokoly.  Mají typ odpovídající název, který zadáte při definování protokolu, takže vlastnost typu můžete použít při hledání načíst záznamy shromážděné z konkrétní protokolu.

Následující tabulka obsahuje příklady různých prohledávání protokolů, které načítají záznamy ze vlastní protokoly.

| Dotaz | Popis |
|:--- |:--- |
| MyApp_CL |Všechny události z vlastního protokolu pojmenovaných MyApp_CL. |
| MyApp_CL &#124; kde Severity_CF == "Chyba" |Všechny události z vlastního protokolu pojmenovaných MyApp_CL s hodnotou *chyba* ve vlastní pole s názvem *Severity_CF*. |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Ukázkový názorný postup přidání vlastního protokolu
Následující části vás provede příklad vytvoření vlastního protokolu.  Ukázkový protokol shromažďují má jednu položku na každý řádek začínající datum a čas a potom oddělených čárkou pole pro kód, stav a zprávy.  Níže se zobrazují několik ukázkových záznamů.

    2016-03-10 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
    2016-03-10 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
    2016-03-10 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
    2016-03-10 01:38:22 302,Error,Application could not connect to database
    2016-03-10 01:31:34 303,Error,Application lost connection to database

### <a name="upload-and-parse-a-sample-log"></a>Uložit a analyzovat ukázkový protokol
Poskytujeme jeden ze souborů protokolu a zobrazit události, které se shromažďují.  Nový řádek v tomto případě je dostatečná oddělovač.  Pokud jedna položka v protokolu může zahrnovat více řádků, i když bude nutné oddělovač časového razítka má být použit.

![Uložit a analyzovat ukázkový protokol](media/data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Přidat cesty ke kolekcím protokolů
Soubory protokolu budou umístěny v *C:\MyApp\Logs*.  Nový soubor se vytvoří každý den s názvem, který obsahuje datum ve vzoru *appYYYYMMDD.log*.  Dostatečná vzor pro tento protokol bude *C:\MyApp\Logs\\\*.log*.

![Cesta k protokolu kolekce](media/data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Zadejte název a popis pro protokol
Používáme název *MyApp_CL* a zadejte **popis**.

![Název protokolu](media/data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>Ověřte, že je shromažďováno vlastní protokoly
Pomocí dotazu *typ = MyApp_CL* vrátit všechny záznamy z shromážděného protokolu.

![Dotaz protokolu žádné vlastní pole](media/data-sources-custom-logs/query-01.png)

### <a name="parse-the-custom-log-entries"></a>Analyzovat položky vlastního protokolu
Používáme vlastní pole k definování *čas události*, *kód*, *stav*, a *zpráva* pole a můžeme vidět rozdíl v záznamech které jsou vrácené dotazem.

![Dotaz protokol o vlastní pole](media/data-sources-custom-logs/query-02.png)

## <a name="next-steps"></a>Další postup
* Použití [vlastních polí](../../azure-monitor/platform/custom-fields.md) k analýze položek ve vlastní přihlášení u jednotlivých polí.
* Další informace o [prohledávání protokolů](../../azure-monitor/log-query/log-query-overview.md) analyzovat data shromážděná ze zdrojů dat a jejich řešení.
