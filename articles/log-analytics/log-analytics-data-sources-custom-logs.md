---
title: Shromažďovat vlastní protokoly v Azure Log Analytics | Microsoft Docs
description: Analýzy protokolů můžete shromažďovat události z textových souborů v počítačích Windows a Linux.  Tento článek popisuje, jak můžete definovat podrobnosti záznamů, které vytvoří v pracovním prostoru analýzy protokolů a nový vlastní protokol.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: aca7f6bb-6f53-4fd4-a45c-93f12ead4ae1
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/27/2018
ms.author: bwren
ms.openlocfilehash: 28523ce3671a8104d91f04575b3e88647dde16f4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34637068"
---
# <a name="custom-logs-in-log-analytics"></a>Vlastní protokoly v analýzy protokolů
Zdroj dat vlastní protokoly ve analýzy protokolů umožňuje shromažďování událostí z textových souborů v počítačích Windows a Linux. Mnoho aplikací protokolování informací k textovým souborům místo standardní protokolování služby, jako je například protokol událostí systému Windows nebo Syslog.  Jakmile se shromažďují, můžete analyzovat každý záznam v přihlášení do jednotlivých polí pomocí [vlastní pole](log-analytics-custom-fields.md) funkce analýzy protokolů.

![Vlastní protokol kolekce](media/log-analytics-data-sources-custom-logs/overview.png)

Soubory protokolů, které se mají shromažďovat musí splňují následující kritéria.

- Protokol musí mít jednu položku na každý řádek nebo používat časové razítko odpovídající jednu z následujících formátů na začátku každé položky.

    RRRR MM-DD HH: MM:<br>M/D/RRRR HH: MM: SS DOP. / ODP<br>MON DD, rrrr hh: mm:<br />rrmmdd hh: mm:<br />ddmmrr hh: mm:<br />Hh: mm: MMMM d<br />dd/MMMM/yyyy:HH:mm:ss zzz<br />rrrr-MM-ddTHH:mm:ssK

- Soubor protokolu nesmí povolit cyklické protokolování nebo oběh protokolu, kde je soubor přepsán pomocí nové položky.
- Soubor protokolu musí používat kódování ASCII nebo UTF-8.  Ostatní formáty například UTF-16 nejsou podporovány.

>[!NOTE]
>Pokud existují duplicitní položky v souboru protokolu, bude shromažďovat analýzy protokolů je.  Výsledky hledání však bude být nekonzistentní kde výsledky filtru zobrazit další události než počet výsledků.  Je důležité, abyste ověřili protokolu, který chcete zjistit, zda je aplikace, která ji vytvoří, příčinou toto chování a je-li to možné adres před vytvořením definice kolekce vlastního protokolu.  
>
  
## <a name="defining-a-custom-log"></a>Definování vlastní protokol
Následující postup použijte k definování vlastní soubor protokolu.  Přejděte na konci tohoto článku podrobný ukázkové přidání vlastního protokolu.

### <a name="step-1-enable-custom-logs-preview"></a>Krok 1. Povolit vlastní protokoly náhled
1. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.
2. V podokně analýzy protokolů předplatných, vyberte pracovní prostor a pak vyberte **portálu OMS** dlaždici.<br><br> ![Tlačítko Prohledávání protokolů](media/log-analytics-data-sources-custom-logs/azure-portal-01.png)<br><br> 
3. Až budete přesměrováni na portálu OMS, klikněte na dlaždici nastavení na pravé straně horní části stránky.<br><br> ![Portál OMS možnosti nastavení](media/log-analytics-data-sources-custom-logs/oms-portal-settings-option.png)<br><br> 
4. Z **nastavení** vyberte **funkce verze Preview** a na stránce vyberte **povolit** pro vlastní protokoly.    

### <a name="step-2-open-the-custom-log-wizard"></a>Krok 2. Otevřete Průvodce vlastního protokolu
Vlastní Průvodce protokolu běží na portálu Azure a umožňuje definovat vlastní nový protokol ke shromažďování.

1. Na portálu Azure vyberte **analýzy protokolů** > pracovního prostoru > **Upřesnit nastavení**.
2. Klikněte na **Data** > **vlastní protokoly**.
3. Ve výchozím nastavení všechny změny konfigurace automaticky odesílají na všechny agenty.  Pro agenty Linux konfigurační soubor posílá kolekcí dat Fluentd.  Pokud chcete upravit soubor ručně na každý agenta systému Linux, poté zrušte zaškrtnutí políčka *použít dole uvedenou konfiguraci u mých Linuxových počítačů*.
4. Klikněte na tlačítko **přidat +** otevřete Průvodce vlastní protokol.

### <a name="step-3-upload-and-parse-a-sample-log"></a>Krok 3. Nahrání a analyzovat ukázkový protokol
Můžete začít odesílání vzorku vlastního protokolu.  Průvodce analyzovat a zobrazit položky v tomto souboru můžete ověřit.  Analýzy protokolů použije oddělovač, který určíte k identifikaci jednotlivých záznamů.

**Nový řádek** oddělovač výchozí a slouží pro soubory protokolů, které mají jednu položku na každý řádek.  Pokud řádek začíná datum a čas v jednom z formátů k dispozici, pak můžete zadat **časové razítko** oddělovač, který podporuje položky, které jsou rozmístěny více než jeden řádek.

Pokud se používá oddělovač časové razítko, bude vlastnost TimeGenerated sestavy analýzy protokolů vyplňovat datum a čas zadaný pro tuto položku v souboru protokolu.  Pokud se používá nový řádek oddělovač, pak TimeGenerated naplněný datum a čas, analýzy protokolů shromážděných položku.


1. Klikněte na tlačítko **Procházet** a přejděte do ukázkový soubor.  Všimněte si, že to může tlačítko může být označeno jako **zvolit soubor** v některé prohlížeče.
2. Klikněte na **Další**.
3. Vlastní Průvodce protokolu nahrát soubor a seznam záznamů, které ji identifikuje.
4. Změňte oddělovač, který se používá k identifikaci nový záznam a vyberte oddělovač, který nejlépe identifikuje záznamy v souboru protokolu.
5. Klikněte na **Další**.

### <a name="step-4-add-log-collection-paths"></a>Krok 4. Přidat cesty ke kolekcím protokolů
Je nutné zadat jednu nebo více cest v agentovi, kde najdou vlastního protokolu.  Můžete buď zadat konkrétní cestu a název souboru protokolu, nebo se zástupnými znaky pro název můžete zadat cestu.  To podporuje aplikace, které každý den nebo pokud jeden soubor dosáhne určité velikosti, vytvořte nový soubor.  Můžete zadat také více cest pro jeden soubor protokolu.

Například aplikace může vytvořit soubor protokolu každý den s datem součástí název jako log20100316.txt. Vzor takové protokolu může být *protokolu\*.txt* které bude platit pro všechny souboru protokolu následující aplikace je pojmenování schématu.

Následující tabulka obsahuje příklady platných vzorů k určení různých protokolových souborech.

| Popis | Cesta |
|:--- |:--- |
| Všechny soubory v *C:\Logs* s příponou .txt pro agenta služby Windows |C:\Logs\\\*.txt |
| Všechny soubory v *C:\Logs* s názvem počínaje protokolu a příponu .txt pro agenta služby Windows |C:\Logs\log\*.txt |
| Všechny soubory v */var/log/audit* s příponou .txt na agenta systému Linux |/var/log/audit/*.txt |
| Všechny soubory v */var/log/audit* s názvem počínaje protokolu a příponu .txt na agenta systému Linux |/var/log/audit/log\*.txt |

1. Chcete-li určit formát cesty vyberte systému Windows nebo Linux, které přidáváte.
2. Zadejte cestu a klikněte na **+** tlačítko.
3. Opakujte postup pro žádné další cesty.

### <a name="step-5-provide-a-name-and-description-for-the-log"></a>Krok 5. Zadejte název a popis pro protokol
Název, který zadáte, se použije pro typ protokolu, jak je popsáno výše.  Bude vždy končit _CL odlišující jej jako vlastní protokol.

1. Zadejte název pro protokol.  **\_CL** automaticky zajištěna příponu.
2. Přidejte volitelné **popis**.
3. Klikněte na tlačítko **Další** se uložit definici vlastního protokolu.

### <a name="step-6-validate-that-the-custom-logs-are-being-collected"></a>Krok 6. Ověřte, že vlastní protokoly jsou shromažďovány
Ho může trvat hodinu počáteční data z nové vlastní protokol se objeví v analýzy protokolů.  Spustí shromažďování položky z protokolů najít v cestě, který jste zadali z bodu, že jste definovali vlastního protokolu.  Nezachovají položky, které jste odeslali při vytváření vlastního protokolu, ale shromáždí již existující položky v souborech protokolů, které je možné vyhledat.

Jakmile analýzy protokolů spustí shromažďování z vlastního protokolu, bude k dispozici s hledání protokolů svoje záznamy.  Použijte název, který jste zadali vlastní protokol jako **typ** v dotazu.

> [!NOTE]
> Pokud hledání chybí vlastnost RawData, můžete zavřít a znovu otevřít prohlížeč.
>
>

### <a name="step-7-parse-the-custom-log-entries"></a>Krok 7. Analyzovat položky vlastní protokolu
Záznam celý protokolu bude uložen v jedné vlastnost s názvem **RawData**.  Budete pravděpodobně chtít oddělit různé části informací v každé položky do jednotlivé vlastnosti, které jsou uložené v záznamu.  To provedete pomocí [vlastní pole](log-analytics-custom-fields.md) funkce analýzy protokolů.

Podrobné kroky k analýze záznam vlastní protokolu nejsou uvedeny zde.  Podrobnosti najdete [vlastní pole](log-analytics-custom-fields.md) dokumentaci pro tyto informace.

## <a name="removing-a-custom-log"></a>Odebrání vlastního protokolu
Odebrat vlastní protokol, který jste dřív zadali, použijte tento postup na portálu Azure.

1. Z **Data** v nabídce **Upřesnit nastavení** vašeho pracovního prostoru, vyberte **vlastní protokoly** seznam všechny vlastní protokoly.
2. Klikněte na tlačítko **odebrat** vedle vlastní protokolu, který chcete odebrat.


## <a name="data-collection"></a>Shromažďování dat
Analýzy protokolů bude shromažďovat nové položky z každé vlastní protokolu přibližně každých 5 minut.  Agent zaznamená v jednotlivých souborů protokolu, který shromáždí z jeho místo.  Pokud agenta přejde do režimu offline dobu, bude položky od posledního místa vypnutý, shromažďovat analýzy protokolů i v případě, že tyto položky byly vytvořeny v době, kdy agent offline.

Celý obsah položky protokolu se zapisují do jednoho vlastnost s názvem **RawData**.  To můžete analyzovat na více vlastností, které mohou být analyzován a vyhledávat samostatně definováním [vlastní pole](log-analytics-custom-fields.md) po vytvoření vlastního protokolu.

## <a name="custom-log-record-properties"></a>Vlastnosti záznamu vlastní protokolu
Vlastní protokol záznamů mít typ s názvem protokolu, které poskytujete a vlastnosti v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| TimeGenerated |Datum a čas, které záznam byl shromáždit pomocí analýzy protokolů.  Pokud protokol používá oddělovač založené na čase Toto je čas shromážděných z položky. |
| SourceSystem |Typ agenta záznamu nebyla shromážděna z. <br> Připojit OpsManager – agent systému Windows, buď přímo nebo System Center Operations Manager <br> Linux – všechny agenty Linux |
| RawData |Úplný text shromážděných položky. |
| ManagementGroupName |Název skupiny pro správu pro System Center Operations spravovat agenty.  Pro jiné agenty jde AOI -\<ID pracovního prostoru\> |

## <a name="log-searches-with-custom-log-records"></a>Protokol hledání se záznamy vlastního protokolu
V pracovním prostoru analýzy protokolů stejně jako záznamy z jiného zdroje dat jsou uloženy záznamy ze vlastní protokoly.  Budou mít typ odpovídající název, který zadáte, když definujete protokol, abyste mohli používat vlastnost Type v hledání pro načtení záznamů shromážděné z konkrétní protokolu.

Následující tabulka obsahuje různé příklady vyhledávání protokolu, které načtení záznamů z vlastní protokoly.

| Dotaz | Popis |
|:--- |:--- |
| MyApp_CL |Všechny události z vlastní protokolu s názvem MyApp_CL. |
| MyApp_CL &#124; kde Severity_CF == "error. |Všechny události z vlastní protokolu s názvem MyApp_CL s hodnotou *chyba* do vlastní pole s názvem *Severity_CF*. |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Ukázka návod přidání vlastního protokolu
V následující části vás provede příklad vytvoření vlastního protokolu.  Příklad protokolu shromažďovaných má jednu položku na každý řádek počínaje datum a čas a potom oddělených čárkou pole pro kód, stav a zprávy.  Níže jsou uvedeny několik položek příkladu.

    2016-03-10 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
    2016-03-10 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
    2016-03-10 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
    2016-03-10 01:38:22 302,Error,Application could not connect to database
    2016-03-10 01:31:34 303,Error,Application lost connection to database

### <a name="upload-and-parse-a-sample-log"></a>Nahrání a analyzovat ukázkový protokol
Poskytujeme jeden ze souborů protokolu a zobrazit události, které bude shromažďování.  Nový řádek v tomto případě je dostatečná oddělovač.  Pokud jeden záznam v protokolu může zahrnovat více řádků ale nutné časové razítko oddělovač, který se má použít.

![Nahrání a analyzovat ukázkový protokol](media/log-analytics-data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Přidat cesty ke kolekcím protokolů
Soubory protokolu budou umístěny v *C:\MyApp\Logs*.  Nový soubor se vytvoří každý den s názvem, který obsahuje data ve vzoru *appYYYYMMDD.log*.  Dostatečná vzor pro tento protokol by *C:\MyApp\Logs\\\*.log*.

![Cesta kolekce protokolu](media/log-analytics-data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Zadejte název a popis pro protokol
Používáme název *MyApp_CL* a zadejte **popis**.

![Název protokolu](media/log-analytics-data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>Ověřte, že vlastní protokoly jsou shromažďovány
Používáme dotaz *typ = MyApp_CL* chcete zobrazit všechny záznamy z shromážděných protokolu.

![Dotaz protokolu s žádné vlastní pole](media/log-analytics-data-sources-custom-logs/query-01.png)

### <a name="parse-the-custom-log-entries"></a>Analyzovat položky vlastní protokolu
Používáme vlastní pole definovat *EventTime*, *kód*, *stav*, a *zpráva* pole a jsme viděli rozdíl v záznamech který jsou vrácených dotazem.

![Protokol dotazu s vlastními poli](media/log-analytics-data-sources-custom-logs/query-02.png)

## <a name="next-steps"></a>Další postup
* Použití [vlastní pole](log-analytics-custom-fields.md) analyzovat položky ve vlastních přihlášení do jednotlivých polí.
* Další informace o [protokolu hledání](log-analytics-log-searches.md) analyzovat data shromážděná ze zdrojů dat a řešení.
