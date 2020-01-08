---
title: Log Analytics – nejčastější dotazy | Dokumentace Microsoftu
description: Odpovědi na nejčastější dotazy týkající se služby Azure Monitor Log Analytics.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/01/2019
ms.openlocfilehash: 77159e0fa73a1f56688c867c55ae46f28016992c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75394790"
---
# <a name="log-analytics-faq"></a>Nejčastější dotazy k Log Analytics

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Toto je seznam nejčastějších dotazů k pracovnímu prostoru Azure Monitor Log Analytics. Pokud máte nějaké další dotazy o službě Log Analytics, pokračujte [diskusní fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) a zveřejněte své dotazy. Pokud je dotaz pokládán často, přidáme ji k tomuto článku tak, aby jej lze rychle a snadno najít.


## <a name="new-logs-experience"></a>Nové prostředí pro protokoly

### <a name="q-whats-the-difference-between-the-new-logs-experience-and-log-analytics"></a>D: Jaký je rozdíl mezi nové prostředí pro protokoly a Log Analytics?

Odpověď: jsou to samé. [Log Analytics je integrované jako funkci ve službě Azure Monitor](../../azure-monitor/azure-monitor-rebrand.md) poskytnout více sjednocené prostředí pro monitorování. Nové prostředí protokolů ve službě Azure Monitor je přesně stejný jako dotazy Log Analytics, které už jste dosud používali mnoho zákazníků.

### <a name="q-can-i-still-use-log-search"></a>Otázka: Mohu přesto použít prohledávání protokolů? 

A: prohledávání protokolů je stále k dispozici na portálu OMS a na webu Azure Portal v části názvu aktuálně **protokoly (classic)** . Na portálu OMS se oficiálně dostupné jenom do 15. ledna 2019. Klasickým prostředím protokolů na webu Azure portal postupně vyřadíme a nahradí nové protokoly prostředí. 

### <a name="q-can-i-still-use-advanced-analytics-portal"></a>Otázka: Použití portálu Advanced Analytics 
Nové prostředí protokolů na webu Azure Portal je založen na portálu pro pokročilé analýzy, ale stále k němu mimo na webu Azure portal. Plán služby vyřazení z provozu tento portál externí bude brzy oznámena.

### <a name="q-why-cant-i-see-query-explorer-and-save-buttons-in-the-new-logs-experience"></a>Otázka: Proč nelze zobrazit Průzkumník dotazů a tlačítka Uložit v novém prostředí protokoly?

**Dotazování Explorer**, **Uložit** a **nastavit upozornění** při prohlížení protokolů v kontextu konkrétní prostředek nejsou k dispozici tlačítka. Vytvořit upozornění, uložit nebo načíst dotaz, musíte nastavit rozsah protokoly do pracovního prostoru. Chcete-li otevřít protokoly v kontextu pracovního prostoru, vyberte **všechny služby** > **monitorování** > **protokoly**. Naposledy použité pracovní prostor je vybráno, ale můžete vybrat jiný pracovní prostor. Zobrazit [zobrazení a analýza dat v Log Analytics](../log-query/portals.md) Další informace.

### <a name="q-how-do-i-extract-custom-fields-in-the-new-logs-experience"></a>Otázka: Jak se extrahovat vlastní pole v novém prostředí protokoly? 

Odpověď: pole extrakci se aktuálně podporují v modelu classic, které protokoly prostředí. 

### <a name="q-where-do-i-find-list-view-in-the-new-logs"></a>Otázka: Kde najít seznam v nové protokoly? 

Odpověď: zobrazení seznamu není k dispozici v nové protokoly. Je šipka vlevo jednomu záznamu v tabulce výsledků. Klepnutím na šipku otevřete podrobnosti konkrétního záznamu. 

### <a name="q-after-running-a-query-a-list-of-suggested-filters-are-available-how-can-i-see-filters"></a>Otázka: Po spuštění dotazu jsou k dispozici seznam navrhovaných filtrů. Jak můžu zobrazit filtry? 

O: kliknutím na ' filtry ' v levém podokně zobrazíte náhled nové implementace filtrů. To je teď na základě sady úplné výsledků namísto podle limit 10 000 záznamů uživatelského rozhraní. Toto je momentálně seznam nejpopulárnější filtry a 10 nejběžnějších hodnot u každého filtru. 

### <a name="q-why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-in-logs-after-drilling-in-from-vm"></a>Otázka: Proč se zobrazuje chyba: "zaregistrujte poskytovatele prostředků"Microsoft.Insights' pro toto předplatné chcete povolit tento dotaz "v protokolech, při procházení se změnami z virtuálního počítače? 

Odpověď: ve výchozím nastavení řada poskytovatelů prostředků jsou automaticky registrované, ale budete muset ručně zaregistrovat někteří poskytovatelé prostředků. Tím se nakonfiguruje vaše předplatné pro práci s poskytovatelem prostředků. Obor pro registraci je vždy předplatné. Další informace najdete v tématu [Poskytovatelé a typy prostředků](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

### <a name="q-why-am-i-am-getting-no-access-error-message-when-accessing-logs-from-a-vm-page"></a>Otázka: Proč mi Power BI Desktop zobrazuje žádná chybová zpráva přístup při přístupu k protokoly ze stránky virtuálního počítače? 

Odpověď: Pokud chcete zobrazit protokolů virtuálního počítače, budete muset udělit oprávnění ke čtení pro pracovní prostory, které se ukládají protokoly virtuálního počítače. V těchto případech se musí udělit správce oprávnění v Azure vám.

### <a name="q-why-can-i-can-access-my-workspace-in-oms-portal-but-i-get-the-error-you-have-no-access-in-the-azure-portal"></a>Otázka: Proč mám může přístup pracovního prostoru v portálu OMS, ale zobrazí chyba "nemáte přístup" na webu Azure Portal?  

Odpověď: pro přístup k pracovní prostor v Azure, musíte mít oprávnění Azure, které jsou přiřazeny. Existují případy, kdy nemusí mít odpovídající přístupová oprávnění. V těchto případech musí správce udělit oprávnění v Azure.See [portálu OMS do Azure](oms-portal-transition.md) Další informace.

### <a name="q-why-cant-i-cant-see-view-designer-entry-in-logs"></a>Otázka: Proč nelze nezobrazují položky Návrhář zobrazení v protokolech?

A: Návrhář zobrazení je dostupná jenom v protokolech pro uživatelé s oprávněním Přispěvatel nebo vyšší.

### <a name="q-can-i-still-use-the-analytics-portal-outside-of-azure"></a>Otázka: Použití portálu Analytics mimo Azure

A. Ano, na stránce protokoly v Azure a portál pro pokročilou analýzu jsou založeny na stejný kód. Log Analytics je integrované jako funkci ve službě Azure Monitor k poskytování více jednotné možnosti monitorování. Přístup k portálu Analytics můžete mít pořád pomocí adresy URL: https:\/\/Portal. loganalytics. IO/Subscriptions/{subscriptionId}/ResourceGroups/{resourceGroupName}/pracovní prostory/{pracovníhoy}.



## <a name="general"></a>Obecné

### <a name="q-how-can-i-see-my-views-and-solutions-in-azure-portal"></a>Otázka: Jak lze zobrazit Moje zobrazení a řešení na webu Azure portal? 

Odpověď: seznam zobrazení a nainstalované řešení jsou k dispozici na webu Azure portal. Klikněte na **Všechny služby**. V seznamu prostředků vyberte **monitorování**, pak klikněte na tlačítko **... Další**. Naposledy použité pracovní prostor je vybráno, ale můžete vybrat jiný pracovní prostor. 

### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>Otázka: Používá Log Analytics stejný agent, kterého jako Azure Security Center?

Odpověď: v rané fázi. června 2017 Azure Security Center začal, shromažďování a ukládání dat pomocí agenta Microsoft Monitoring Agent. Další informace najdete v tématu [Azure Security Center platformy migrace – nejčastější dotazy](../../security-center/security-center-enable-data-collection.md).

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

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>Otázka: Jak si nastavím upozorňování při zastavení shromažďování dat?

Odpověď: použijte postup popsaný v [vytvořit nové oznámení protokolu](../../azure-monitor/platform/alerts-metric.md) která vás upozorní, když se shromažďování dat zastaví.

Při vytváření upozornění pro při zastavení shromažďování dat, nastavte:

- **Definujte podmínku upozornění** – Jako cíl prostředku zadejte svůj pracovní prostor služby Log Analytics.
- **Kritéria upozornění** – Zadejte následující:
   - **Název signálu** vyberte **prohledávání protokolů vlastní**.
   - **Vyhledávací dotaz** na `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`.
   - **Logika upozornění** je **Založená na** *počtu výsledků* a **Podmínka** je *Větší než* **Prahová hodnota** *0*.
   - **Časové období** z *30* minut a **četnosti upozornění** ke každému *10* minut
- **Definujte podrobnosti upozornění** – Zadejte následující:
   - **Název** k *zastavit shromažďování dat*
   - **Závažnost** na *Upozornění*.

Zadejte existující, nebo vytvořte nový [skupiny akcí](../../azure-monitor/platform/action-groups.md) tak, aby po upozornění protokolu by odpovídala kritériím, budete upozorněni, pokud máte pro víc než 15 minut chybí prezenční signál.

## <a name="configuration"></a>Konfigurace

### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>Otázka: Můžete změnit název tabulky/kontejneru objektů blob použít ke čtení z Azure Diagnostics (WAD)?

A. Ne, není v tuto chvíli možné si číst z libovolného tabulky nebo kontejnerů ve službě Azure storage.

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>Otázka: Jaké IP adresy se používá služba Log Analytics? Jak zajistím, aby se Moje brány firewall umožňuje pouze provoz do služby Log Analytics?

A. Služba Log Analytics je postavená na Azure. Log Analytics IP adresy jsou ve [Microsoft Azure rozsahů IP adres Datacentra](https://www.microsoft.com/download/details.aspx?id=41653).

Jakmile dojde k nasazení služeb, změňte skutečné IP adresy služby Log Analytics. Názvy DNS, aby prostřednictvím brány firewall jsou dokumentovány v článku [požadavky na síťovou](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements).

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>Otázka: Můžu použít ExpressRoute pro připojení k Azure. Používá Moje Log Analytics provoz připojení ExpressRoute?

A. Různé typy provozu ExpressRoute jsou popsány v [dokumentace ke službě ExpressRoute](../../expressroute/expressroute-faqs.md#supported-services).

Přenos dat do služby Log Analytics používá veřejné partnerské vztahy okruhu ExpressRoute.

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>Otázka: Je jednoduché a snadný způsob, jak přesunout existující pracovní prostor Log Analytics do jiného předplatného Azure pracovní prostor Log Analytics?

A. `Move-AzResource` Rutina umožňuje přesunout do jiného pracovního prostoru Log Analytics a účet Automation z jednoho předplatného Azure. Další informace najdete v tématu [Move-AzResource](https://msdn.microsoft.com/library/mt652516.aspx).

Tato změna můžete také provést na webu Azure Portal.

Nelze přesunout data z jednoho pracovního prostoru Log Analytics do jiného nebo změnit oblasti, která Log Analytics data jsou uložena v.

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>Otázka: Jak mohu přidat Log Analytics na System Center Operations Manager?

Odpověď: aktualizace na nejnovější kumulativní aktualizaci a import sad management Pack vám umožní připojit Operations Manageru k Log Analytics.

>[!NOTE]
>Připojení nástroje Operations Manager ke službě Log Analytics je dostupná jenom pro System Center Operations Manager 2012 SP1 a novější.

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>Otázka: jak můžete ověřit, že agent je schopen komunikovat s Log Analytics?

Odpověď: Chcete-li zajistit, že agent může komunikovat s Log Analytics pracovním prostorem, přejít na: Ovládací panely, nastavení & zabezpečení **Microsoft Monitoring Agent**.

V části **Azure Log Analytics (OMS)** kartu, vyhledejte zelená značka zaškrtnutí. Ikona s zelenou značku zaškrtnutí potvrdí, že agent není schopen komunikovat se službou Azure.

Žlutá ikona upozornění znamená, že agent má problémy komunikace se službou Log Analytics. Jedním z běžných důvodů je, že služba Microsoft Monitoring Agent byla zastavena. Pomocí Správce řízení služeb se restartovat službu.

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>Otázka: Jak můžu Zastavit agenta v komunikaci se službou Log Analytics?

Odpověď: v System Center Operations Manager odeberte počítač ze seznamu spravovaných počítačů s Log Analytics. Nástroj Operations Manager aktualizuje konfiguraci agenta k již ukládání dat do Log Analytics. U agentů přímo připojené ke službě Log Analytics, můžete je nezastavíte komunikují prostřednictvím: ovládací Panel, zabezpečení a nastavení, **agenta Microsoft Monitoring Agent**.
V části **Azure Log Analytics (OMS)** , odeberte všechny pracovní prostory uvedené.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>Otázka: Proč se při pokusu o přesunutí Můj pracovní prostor z jednoho předplatného Azure na jiný dochází k chybě?

Odpověď: pracovní prostor přesunout do jiné předplatné nebo skupinu prostředků, je nutné nejprve zrušit účtu Automation v pracovním prostoru. Zrušení propojení účtu Automation vyžaduje odebrání těchto řešení, pokud jsou nainstalovány v pracovním prostoru: Update Management, Change Tracking nebo spuštění/zastavení virtuálních počítačů mimo špičku se odeberou. Po odebrání těchto řešení zrušení propojení účtu Automation vyberte **propojené pracovní prostory** v levém podokně v automatizaci účtu prostředek a klikněte na tlačítko **zrušit propojení pracovního prostoru** na pásu karet.
 > Odebrat řešení je třeba znovu nainstalovat v pracovním prostoru a propojení služby Automation s pracovním prostorem musí revidovat po přesunutí.

Ujistěte se, že máte oprávnění v obou předplatných Azure.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-update-a-savedsearch"></a>Otázka: Proč se při pokusu aktualizovat elementu SavedSearch dochází k chybě?

Odpověď: je třeba přidat 'etag' v těle rozhraní API nebo vlastností šablony Azure Resource Manageru:
```
"properties": {
   "etag": "*",
   "query": "SecurityEvent | where TimeGenerated > ago(1h) | where EventID == 4625 | count",
   "displayName": "An account failed to log on",
   "category": "Security"
}
```

## <a name="agent-data"></a>Data agenta
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>Otázka: Kolik dat můžu poslat přes agenta ke službě Log Analytics? Je maximální objem dat jednoho zákazníka?
A. Množství dat, která se nahrávají, se nijak neomezuje, vychází z cenové možnosti, kterou vyberete, rezervace kapacity a průběžné platby. Pracovní prostor Log Analytics je navržený tak, aby se při zpracování objemu od zákazníka automaticky škáloval, a to i v případě, že je terabajty za den. Další informace najdete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/monitor/).

Agenta Log Analytics byla navržena k zajištění, že má malou stopu. Objem dat se liší v závislosti na řešení, které povolíte. Můžete najít podrobné informace o objemu dat a zobrazit rozpis řešením v [využití](../../azure-monitor/platform/data-usage.md) stránky.

Další informace si můžete přečíst na [blogu zákazníka](https://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) , který zobrazuje výsledky po vyhodnocení využití prostředků (nároky) agenta Log Analytics.

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>Otázka: Jak velkou šířku pásma sítě se používá ve Microsoft Management Agent (MMA), při odesílání dat do služby Log Analytics?

A. Šířka pásma je funkce na množství dat odesílaných. Data se komprimují, při přenosu přes síť.

### <a name="q-how-much-data-is-sent-per-agent"></a>Otázka: Kolik dat se odesílají na agenta?

A. Množství dat odesílaných na agenta, závisí na:

* Řešení, která jste povolili
* Počet shromažďovaných čítačů výkonu a protokolování
* Objem dat v protokolech

Celkové využití se zobrazí na [využití](../../azure-monitor/platform/data-usage.md) stránky.

Pro počítače, které je možné spouštět WireData agenta použijte tento dotaz Pokud chcete zobrazit, kolik dat je odesíláno:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>Další kroky

Začněte [s Azure monitor](../../azure-monitor/overview.md) , kde se dozvíte víc o Log Analytics a začněte pracovat během několika minut.
