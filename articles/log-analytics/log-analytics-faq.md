---
title: Log Analytics – nejčastější dotazy | Dokumentace Microsoftu
description: Odpovědi na nejčastější dotazy týkající se služby Azure Log Analytics.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ad536ff7-2c60-4850-a46d-230bc9e1ab45
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 403ac2fd0ab497239350c8b4a68a6b6f53bb24b4
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50963910"
---
# <a name="log-analytics-faq"></a>Nejčastější dotazy k Log Analytics
Tato FAQ Microsoft je uveden seznam nejčastější dotazy o službě Log Analytics v Microsoft Azure. Pokud máte nějaké další dotazy o službě Log Analytics, pokračujte [diskusní fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) a zveřejněte své dotazy. Pokud je dotaz pokládán často, přidáme ji k tomuto článku tak, aby jej lze rychle a snadno najít.


## <a name="new-logs-experience"></a>Nové prostředí pro protokoly

### <a name="q-whats-the-difference-between-the-new-logs-experience-and-log-analytics"></a>D: Jaký je rozdíl mezi nové prostředí pro protokoly a Log Analytics?

Odpověď: jsou to samé. [Log Analytics je integrované jako funkci ve službě Azure Monitor](../azure-monitor/azure-monitor-rebrand.md) poskytnout více sjednocené prostředí pro monitorování. Nové prostředí protokolů ve službě Azure Monitor je přesně stejný jako dotazy Log Analytics, které už jste dosud používali mnoho zákazníků.

### <a name="q-can-i-still-use-log-search"></a>Otázka: Mohu přesto použít prohledávání protokolů? 

A: prohledávání protokolů je stále k dispozici na portálu OMS a na webu Azure Portal v části názvu aktuálně **protokoly (classic)**. Na portálu OMS se oficiálně dostupné jenom do 15. ledna 2019. Klasickým prostředím protokolů na webu Azure portal postupně vyřadíme a nahradí nové protokoly prostředí. 

### <a name="q-can-i-still-use-advanced-analytics-portal"></a>Otázka: Použití portálu Advanced Analytics 
Nové prostředí protokolů na webu Azure Portal je na základě [Advanced portál Analytics](https://portal.loganalytics.io/), ale stále k němu mimo na webu Azure portal. Plán služby vyřazení z provozu tento portál externí bude brzy oznámena.

### <a name="q-why-cant-i-see-query-explorer-and-save-buttons-in-the-new-logs-experience"></a>Otázka: Proč nelze zobrazit Průzkumník dotazů a tlačítka Uložit v novém prostředí protokoly?

**Dotazování Explorer**, **Uložit** a **nastavit upozornění** při prohlížení protokolů v kontextu konkrétní prostředek nejsou k dispozici tlačítka. Vytvořit upozornění, uložit nebo načíst dotaz, musíte nastavit rozsah protokoly do pracovního prostoru. Chcete-li otevřít protokoly v kontextu pracovního prostoru, vyberte **všechny služby** > **monitorování** > **protokoly**. Naposledy použité pracovní prostor je vybráno, ale můžete vybrat jiný pracovní prostor. Zobrazit [zobrazení a analýza dat v Log Analytics](../log-analytics/log-analytics-log-search-portals.md) Další informace.

### <a name="q-how-do-i-extract-custom-fields-in-the-new-logs-experience"></a>Otázka: Jak se extrahovat vlastní pole v novém prostředí protokoly? 

Odpověď: pole extrakci se aktuálně podporují v modelu classic, které protokoly prostředí. 

### <a name="q-where-do-i-find-list-view-in-the-new-logs"></a>Otázka: Kde najít seznam v nové protokoly? 

Odpověď: zobrazení seznamu není k dispozici v nové protokoly. Je šipka vlevo jednomu záznamu v tabulce výsledků. Klepnutím na šipku otevřete podrobnosti konkrétního záznamu. 

### <a name="q-after-running-a-query-a-list-of-suggested-filters-shows-up-but-it-doesnt-include-all-filters-how-can-i-see-the-rest"></a>Otázka: Po spuštění dotazu se zobrazí seznam filtrů, ale neobsahuje všechny filtry. Jak můžete zobrazit zbývající? 

O: co aktuálně uvidíte, bude ve verzi preview novou implementaci filtry. To je teď na základě sady úplné výsledků namísto podle limit 10 000 záznamů uživatelského rozhraní. Toto je momentálně seznam nejpopulárnější filtry a 10 nejběžnějších hodnot u každého filtru. 

### <a name="q-why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-in-logs-after-drilling-in-from-vm"></a>Otázka: Proč se zobrazuje chyba: "zaregistrujte poskytovatele prostředků"Microsoft.Insights' pro toto předplatné chcete povolit tento dotaz "v protokolech, při procházení se změnami z virtuálního počítače? 

Odpověď: ve výchozím nastavení řada poskytovatelů prostředků jsou automaticky registrované, ale budete muset ručně zaregistrovat někteří poskytovatelé prostředků. Tím se nakonfiguruje vaše předplatné pro práci s poskytovatelem prostředků. Obor pro registraci je vždy předplatné. Další informace najdete v tématu [Poskytovatelé a typy prostředků](../azure-resource-manager/resource-manager-supported-services.md#portal).

### <a name="q-why-am-i-am-getting-no-access-error-message-when-accessing-logs-from-a-vm-page"></a>Otázka: Proč mi Power BI Desktop zobrazuje žádná chybová zpráva přístup při přístupu k protokoly ze stránky virtuálního počítače? 

Odpověď: Pokud chcete zobrazit protokolů virtuálního počítače, budete muset udělit oprávnění ke čtení pro pracovní prostory, které se ukládají protokoly virtuálního počítače. V těchto případech se musí udělit správce oprávnění v Azure vám.

### <a name="q-why-can-i-can-access-my-workspace-in-oms-portal-but-i-get-the-error-you-have-no-access-in-the-azure-portal"></a>Otázka: Proč mám může přístup pracovního prostoru v portálu OMS, ale zobrazí chyba "nemáte přístup" na webu Azure Portal?  

Odpověď: pro přístup k pracovní prostor v Azure, musíte mít oprávnění Azure, které jsou přiřazeny. Existují případy, kdy nemusí mít odpovídající přístupová oprávnění. V těchto případech musí správce udělit oprávnění v Azure.See [portálu OMS do Azure](../log-analytics/log-analytics-oms-portal-transition.md) Další informace.

### <a name="q-why-cant-i-cant-see-view-designer-entry-in-logs"></a>Otázka: Proč nelze nezobrazují položky Návrhář zobrazení v protokolech? 
A: Návrhář zobrazení je dostupná jenom v protokolech pro uživatelé s oprávněním Přispěvatel nebo vyšší.

### <a name="q-can-i-still-use-the-analytics-portal-outside-of-azure"></a>Otázka: Použití portálu Analytics mimo Azure
A. Ano, v protokolech stránce v Azure a [portálu pro pokročilou analýzu](https://portal.loganalytics.io) jsou založeny na stejný kód. Log Analytics je integrované jako funkci ve službě Azure Monitor k poskytování více jednotné možnosti monitorování. Můžete nadále přistupovat pomocí adresy URL portálu Analytics: https://portal.loganalytics.io/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/workspaces/{workspaceName}.



## <a name="general"></a>Obecné

### <a name="q-how-can-i-see-my-views-and-solutions-in-azure-portal"></a>Otázka: Jak lze zobrazit Moje zobrazení a řešení na webu Azure portal? 

Odpověď: seznam zobrazení a nainstalované řešení jsou k dispozici na webu Azure portal. Klikněte na **Všechny služby**. V seznamu prostředků vyberte **monitorování**, pak klikněte na tlačítko **... Další**. Naposledy použité pracovní prostor je vybráno, ale můžete vybrat jiný pracovní prostor. 

### <a name="q-why-i-cant-create-workspaces-in-west-central-us-region"></a>Otázka: Proč nelze vytvořit pracovní prostory v oblasti Středozápad USA? 

Odpověď: Tato oblast je v dočasné kapacity. Limit se plánuje se zákazníky a vyřešené v první polovině roku 2019.


### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>Otázka: Používá Log Analytics stejný agent, kterého jako Azure Security Center?

Odpověď: v rané fázi. června 2017 Azure Security Center začal, shromažďování a ukládání dat pomocí agenta Microsoft Monitoring Agent. Další informace najdete v tématu [Azure Security Center platformy migrace – nejčastější dotazy](../security-center/security-center-enable-data-collection.md).

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>Otázka: AD provádí jaké kontroly a řešení pro posouzení SQL?

Odpověď: následující dotaz zobrazí popis všech aktuálně provádí kontroly:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Výsledky lze exportovat do Excelu pro další kontrolu.

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>Otázka: Proč se zobrazuje něco jiného než OMS v konzole nástroje System Center Operations Manager?

Odpověď: v závislosti na jaké aktualizace souhrnu nástroje Operations Manager na, může se zobrazit uzel pro *System Center Advisor*, *Operational Insights*, nebo *Log Analytics*.

Aktualizace textu řetězce *OMS* je zahrnuta v sadě management pack, které se mají importovat ručně. Pokud chcete zobrazit aktuální text a funkce, postupujte podle pokynů na nejnovější systém System Center Operations Manager aktualizace znalostní BÁZE kumulativní článku a aktualizací konzoly nástroje.

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>Otázka: existuje místní verzi služby Log Analytics?

Odpověď: Ne. Log Analytics je škálovatelná Cloudová služba, která zpracovává a ukládá velké objemy dat. 

### <a name="q-how-do-i-troubleshoot-if-log-analytics-is-no-longer-collecting-data"></a>Otázka: Jak řešit Pokud Log Analytics je už shromažďování dat?

Odpověď: pro předplatné a pracovní prostor vytvořený před 2. dubnem 2018, tj. v *Free* cenovou úroveň, pokud je za jeden den odeslána víc než 500 MB dat, shromažďování dat zastaví zbytek dne. Dosažení denního limitu je běžným důvodem Log Analytics se zastaví shromažďování dat, nebo se zdá být chybějící data.  

Log Analytics, vytváří událost typu *prezenčního signálu* a slouží k určení, pokud se shromažďování dat zastaví. 

Spuštěním následujícího dotazu ve službě search zkontrolujte, jestli jsou dosažení denního limitu a chybějící data: `Heartbeat | summarize max(TimeGenerated)`

Pokud chcete zkontrolovat konkrétní počítače, spusťte následující dotaz: `Heartbeat | where Computer=="contosovm" | summarize max(TimeGenerated)`

Po zastavení shromažďování dat, v závislosti na časový rozsah vybraný, neuvidíte žádné vrácených záznamů.   

Následující tabulka popisuje důvody, které zastaví shromažďování dat a navrhovanou akci pokračování shromažďování dat:

| Zastaví shromažďování dat z důvodu                       | Chcete-li obnovit sběr dat. |
| -------------------------------------------------- | ----------------  |
| Byl dosažen limit bezplatná data<sup>1</sup>       | Počkejte, až do následujícího měsíce pro kolekci k automatickému restartu, nebo<br> Změnit na placenou cenovou úroveň |
| Předplatné Azure je v pozastaveném stavu z důvodu: <br> Bezplatná zkušební verze skončila <br> Vypršení platnosti Azure passu <br> Měsíční limit útraty dosaženo (například na předplatné MSDN nebo Visual Studio)                          | Převést na placené předplatné <br> Převést na placené předplatné <br> Odeberte limit, nebo počkejte, dokud limit se resetuje |

<sup>1</sup> Pokud váš pracovní prostor je na *Free* cenovou úroveň, jste omezeni na 500 MB dat denně odesílá do služby. Pokud denní limit překročíte, shromažďování dat zastaví až další den. Data odeslaná při zastavení shromažďování dat se indexují a není k dispozici pro hledání. Při shromažďování dat bude pokračovat, dojde k jenom pro nová data odesílaná zpracování. 

Log Analytics používá čas UTC a spustí každý den o půlnoci UTC. Pokud pracovní prostor dosáhne denní limit, zpracování bude pokračovat za první hodinu UTC dalšího dne.

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>Otázka: Jak si nastavím upozorňování při zastavení shromažďování dat?

Odpověď: použijte postup popsaný v [vytvořit nové oznámení protokolu](../monitoring-and-diagnostics/alert-metric.md) která vás upozorní, když se shromažďování dat zastaví.

Při vytváření upozornění pro při zastavení shromažďování dat, nastavte:

- **Definujte podmínku upozornění** – Jako cíl prostředku zadejte svůj pracovní prostor Log Analytics.
- **Kritéria upozornění** – Zadejte následující:
   - **Název signálu** vyberte **prohledávání protokolů vlastní**.
   - **Vyhledávací dotaz** na `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`.
   - **Logika upozornění** je **Založená na** *počtu výsledků* a **Podmínka** je *Větší než* **Prahová hodnota** *0*.
   - **Časové období** z *30* minut a **četnosti upozornění** ke každému *10* minut
- **Definujte podrobnosti upozornění** – Zadejte následující:
   - **Název** k *zastavit shromažďování dat*
   - **Závažnost** na *Upozornění*.

Zadejte existující, nebo vytvořte nový [skupiny akcí](../monitoring-and-diagnostics/monitoring-action-groups.md) tak, aby po upozornění protokolu by odpovídala kritériím, budete upozorněni, pokud máte pro víc než 15 minut chybí prezenční signál.

## <a name="configuration"></a>Konfigurace
### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>Otázka: Můžete změnit název tabulky/kontejneru objektů blob použít ke čtení z Azure Diagnostics (WAD)?

A. Ne, není v tuto chvíli možné si číst z libovolného tabulky nebo kontejnerů ve službě Azure storage.

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>Otázka: Jaké IP adresy se používá služba Log Analytics? Jak zajistím, aby se Moje brány firewall umožňuje pouze provoz do služby Log Analytics?

A. Služba Log Analytics je postavená na Azure. Log Analytics IP adresy jsou ve [Microsoft Azure rozsahů IP adres Datacentra](http://www.microsoft.com/download/details.aspx?id=41653).

Jakmile dojde k nasazení služeb, změňte skutečné IP adresy služby Log Analytics. Názvy DNS, aby prostřednictvím brány firewall jsou dokumentovány v článku [požadavky na síťovou](log-analytics-concept-hybrid.md#network-firewall-requirements).

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>Otázka: Můžu použít ExpressRoute pro připojení k Azure. Používá Moje Log Analytics provoz připojení ExpressRoute?

A. Různé typy provozu ExpressRoute jsou popsány v [dokumentace ke službě ExpressRoute](../expressroute/expressroute-faqs.md#supported-services).

Přenos dat do služby Log Analytics používá veřejné partnerské vztahy okruhu ExpressRoute.

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>Otázka: Je jednoduché a snadný způsob, jak přesunout existující pracovní prostor Log Analytics do jiného předplatného Azure pracovní prostor Log Analytics?

A. `Move-AzureRmResource` Rutina umožňuje přesunout do jiného pracovního prostoru Log Analytics a účet Automation z jednoho předplatného Azure. Další informace najdete v tématu [Move-AzureRmResource](http://msdn.microsoft.com/library/mt652516.aspx).

Tato změna můžete také provést na webu Azure Portal.

Nelze přesunout data z jednoho pracovního prostoru Log Analytics do jiného nebo změnit oblasti, která Log Analytics data jsou uložena v.

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>Otázka: Jak mohu přidat Log Analytics na System Center Operations Manager?

Odpověď: aktualizace na nejnovější kumulativní aktualizaci a import sad management Pack vám umožní připojit Operations Manageru k Log Analytics.

>[!NOTE]
>Připojení nástroje Operations Manager ke službě Log Analytics je dostupná jenom pro System Center Operations Manager 2012 SP1 a novější.

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>Otázka: jak můžete ověřit, že agent je schopen komunikovat s Log Analytics?

Odpověď: Pokud chcete mít jistotu, že agent může komunikovat s OMS, přejděte na: ovládací Panel, zabezpečení a nastavení, **agenta Microsoft Monitoring Agent**.

V části **Azure Log Analytics (OMS)** kartu, vyhledejte zelená značka zaškrtnutí. Ikona s zelenou značku zaškrtnutí potvrdí, že agent není schopen komunikovat se službou Azure.

Žlutá ikona upozornění znamená, že agent má problémy komunikace se službou Log Analytics. Jedním z běžných důvodů je, že služba Microsoft Monitoring Agent byla zastavena. Pomocí Správce řízení služeb se restartovat službu.

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>Otázka: Jak můžu Zastavit agenta v komunikaci se službou Log Analytics?

Odpověď: V nástroji System Center Operations Manager, odeberte počítač ze seznamu spravovaných počítačů OMS. Nástroj Operations Manager aktualizuje konfiguraci agenta k již ukládání dat do Log Analytics. U agentů přímo připojené ke službě Log Analytics, můžete je nezastavíte komunikují prostřednictvím: ovládací Panel, zabezpečení a nastavení, **agenta Microsoft Monitoring Agent**.
V části **Azure Log Analytics (OMS)**, odeberte všechny pracovní prostory uvedené.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>Otázka: Proč se při pokusu o přesunutí Můj pracovní prostor z jednoho předplatného Azure na jiný dochází k chybě?

Odpověď: Pokud používáte na webu Azure portal, ujistěte se, že je vybrána pouze pracovní prostor pro přesunutí. Nesmí být zvolen řešení – bude automaticky přesunut po přesunutí pracovního prostoru. 

Ujistěte se, že máte oprávnění v obou předplatných Azure.

## <a name="agent-data"></a>Data agenta
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>Otázka: Kolik dat můžu poslat přes agenta ke službě Log Analytics? Je maximální objem dat jednoho zákazníka?
A. Plán free Nastaví denní limit 500 MB za jednotlivé pracovní prostory. Plány standard a premium nemají žádné omezení na množství dat, který se odeslal. Jako cloudová služba Log Analytics byla navržena automatické škálování až na popisovač svazku pocházející od zákazníka – i v případě, že je TB za den.

Agenta Log Analytics byla navržena k zajištění, že má malou stopu. Objem dat se liší v závislosti na řešení, které povolíte. Můžete najít podrobné informace o objemu dat a zobrazit rozpis řešením v [využití](log-analytics-usage.md) stránky.

Další informace si můžete přečíst [zákazníka blogu](http://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) zobrazující jejich výsledky po vyhodnocení využití prostředků (nároky na místo) agenta OMS.

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>Otázka: Jak velkou šířku pásma sítě se používá ve Microsoft Management Agent (MMA), při odesílání dat do služby Log Analytics?

A. Šířka pásma je funkce na množství dat odesílaných. Data se komprimují, při přenosu přes síť.

### <a name="q-how-much-data-is-sent-per-agent"></a>Otázka: Kolik dat se odesílají na agenta?

A. Množství dat odesílaných na agenta, závisí na:

* Řešení, která jste povolili
* Počet shromažďovaných čítačů výkonu a protokolování
* Objem dat v protokolech

Cenová úroveň free je dobrým způsobem, jak připojit několik serverů a měřidla typické datový svazek. Celkové využití se zobrazí na [využití](log-analytics-usage.md) stránky.

Pro počítače, které je možné spouštět WireData agenta použijte tento dotaz Pokud chcete zobrazit, kolik dat je odesíláno:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>Další postup
* [Začínáme se službou Log Analytics](../azure-monitor/overview.md) Další informace o službě Log Analytics a uvedení do provozu během několika minut.
