---
title: Nejčastější dotazy k Log Analytics | Microsoft Docs
description: Odpovědi na nejčastější dotazy týkající se služby Azure Monitor Log Analytics.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 11/01/2019
ms.openlocfilehash: 9eb921fc8ea19486db0fc3311764931f09e11464
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579311"
---
# <a name="log-analytics-faq"></a>Nejčastější dotazy k Log Analytics

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Toto je seznam nejčastějších dotazů k pracovnímu prostoru Azure Monitor Log Analytics. Pokud máte další dotazy týkající se Log Analytics, navštivte [diskuzní fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) a Vystavte své dotazy. V případě častého dotazu přidáme Tento článek do tohoto článku, aby ho bylo možné rychle a snadno najít.


## <a name="new-logs-experience"></a>Nové možnosti protokolování

### <a name="q-whats-the-difference-between-the-new-logs-experience-and-log-analytics"></a>Otázka: Jaký je rozdíl mezi novým rozhraním a Log Analyticsm protokolů?

Odpověď: jsou to stejné věci. [Log Analytics se integruje jako funkce v Azure monitor](../../azure-monitor/azure-monitor-rebrand.md) , aby poskytovala sjednocené prostředí pro monitorování. Nové protokolování v Azure Monitor je přesně stejné jako Log Analytics dotazování, které mnoho zákazníků již používalo.

### <a name="q-can-i-still-use-log-search"></a>Otázka: Mohu stále používat prohledávání protokolu? 

Odpověď: prohledávání protokolu je nyní stále k dispozici na portálu OMS a v Azure Portal v části **protokoly názvů (Classic)** . Portál OMS bude oficiálně vyřazen z 15. ledna 2019. Prostředí klasických protokolů v Azure Portal se postupně vyřadí a nahradí nové možnosti protokolování. 

### <a name="q-can-i-still-use-advanced-analytics-portal"></a>Otázka: Můžu dál používat portál pro pokročilou analýzu? 
Nové protokoly v Azure Portal jsou založené na portálu pro pokročilou analýzu, ale pořád k nim můžete přicházet mimo Azure Portal. Plán pro vyřazení tohoto externího portálu bude brzy vyhlášený.

### <a name="q-why-cant-i-see-query-explorer-and-save-buttons-in-the-new-logs-experience"></a>Otázka: Proč se mi nedá zobrazit Průzkumník dotazů a tlačítka pro uložení v nových prostředích protokolů?

Při zkoumání protokolů v kontextu konkrétního prostředku nejsou k dispozici tlačítka pro zobrazování a **nastavování výstrah** v **Průzkumníku dotazů**. Aby bylo možné vytvářet výstrahy, ukládat nebo načítat dotaz, musí být protokoly vymezeny na pracovní prostor. Pokud chcete v kontextu pracovního prostoru otevřít protokoly, vyberte **všechny služby** > **monitorování** **protokolů** > . Vybere se poslední použitý pracovní prostor, ale můžete vybrat libovolný jiný pracovní prostor. Další informace najdete [v tématu zobrazení a analýza dat v Log Analytics](../log-query/portals.md) .

### <a name="q-how-do-i-extract-custom-fields-in-the-new-logs-experience"></a>Otázka: Návody extrahovat vlastní pole v nových prostředích protokolů? 

Odpověď: extrakce vlastních polí je aktuálně podporována v prostředí klasických protokolů. 

### <a name="q-where-do-i-find-list-view-in-the-new-logs"></a>Otázka: Kde najdu zobrazení seznamu v nových protokolech? 

A: zobrazení seznamu není v nových protokolech k dispozici. V tabulce výsledků se nachází šipka vlevo od každého záznamu. Kliknutím na tuto šipku otevřete podrobnosti konkrétního záznamu. 

### <a name="q-after-running-a-query-a-list-of-suggested-filters-are-available-how-can-i-see-filters"></a>Otázka: Po spuštění dotazu jsou k dispozici seznam navrhovaných filtrů. Jak můžu zobrazit filtry? 

O: kliknutím na ' filtry ' v levém podokně zobrazíte náhled nové implementace filtrů. Teď je na základě vaší úplné sady výsledků místo omezení počtu záznamů 10 000 pro uživatelské rozhraní. Toto je aktuálně seznam nejoblíbenějších filtrů a 10 nejběžnějších hodnot pro každý filtr. 

### <a name="q-why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-in-logs-after-drilling-in-from-vm"></a>Otázka: Proč se mi zobrazuje chyba: "zaregistrovat poskytovatele prostředků" Microsoft. Insights "pro toto předplatné povolíte tento dotaz" v protokolech po rozbalení z virtuálního počítače? 

Odpověď: ve výchozím nastavení je mnoho poskytovatelů prostředků zaregistrovaných automaticky, ale možná budete muset některé poskytovatele prostředků zaregistrovat ručně. Tím se nakonfiguruje předplatné pro práci s poskytovatelem prostředků. Obor pro registraci je vždy předplatné. Další informace najdete v tématu [Poskytovatelé a typy prostředků](../../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

### <a name="q-why-am-i-am-getting-no-access-error-message-when-accessing-logs-from-a-vm-page"></a>Otázka: Proč se při přístupu k protokolům ze stránky virtuálního počítače zobrazuje chybová zpráva o přístupu? 

Odpověď: Chcete-li zobrazit protokoly virtuálních počítačů, je třeba udělit oprávnění ke čtení pro pracovní prostory, ve kterých jsou uloženy protokoly virtuálních počítačů. V těchto případech vám správce musí udělit oprávnění v Azure.

### <a name="q-why-can-i-can-access-my-workspace-in-oms-portal-but-i-get-the-error-you-have-no-access-in-the-azure-portal"></a>Otázka: Proč mám přístup k pracovnímu prostoru na portálu OMS, ale zobrazí se chyba "nemáte přístup" v Azure Portal?  

Odpověď: Chcete-li získat přístup k pracovnímu prostoru v Azure, musíte mít přiřazená oprávnění Azure. V některých případech je možné, že nemáte příslušná přístupová oprávnění. V těchto případech vám správce musí udělit oprávnění v Azure. Další informace najdete v tématu věnovaném [přesunutí portálu OMS do Azure](oms-portal-transition.md) .

### <a name="q-why-cant-i-cant-see-view-designer-entry-in-logs"></a>Otázka: Proč nejde zobrazit položku návrháře zobrazení v protokolech?

Odpověď: Návrhář zobrazení je k dispozici pouze v protokolech pro uživatele, kteří jsou přiřazeni pomocí oprávnění přispěvatele nebo vyšší.

### <a name="q-can-i-still-use-the-analytics-portal-outside-of-azure"></a>Otázka: Můžu I nadále používat portál Analytics mimo Azure?

A. Ano, stránka protokoly v Azure a portál pro pokročilou analýzu jsou založené na stejném kódu. Log Analytics se integruje jako funkce v Azure Monitor, aby poskytovala sjednocené prostředí pro monitorování. Přístup k portálu Analytics můžete mít pořád pomocí adresy URL: https:\/\/Portal. loganalytics. IO/Subscriptions/{subscriptionId}/ResourceGroups/{resourceGroupName}/pracovní prostory/{pracovníhoy}.



## <a name="general"></a>Obecné

### <a name="q-how-can-i-see-my-views-and-solutions-in-azure-portal"></a>Otázka: Jak můžu zobrazit Moje zobrazení a řešení v Azure Portal? 

Odpověď: seznam zobrazení a nainstalovaných řešení jsou k dispozici v Azure Portal. Klikněte na **Všechny služby**. V seznamu prostředků vyberte **monitorování**a pak klikněte na **... Další**. Vybere se poslední použitý pracovní prostor, ale můžete vybrat libovolný jiný pracovní prostor. 

### <a name="q-why-i-cant-create-workspaces-in-west-central-us-region"></a>Otázka: Proč nemůžu vytvořit pracovní prostory v Středozápadní USA oblasti? 

Odpověď: Tato oblast je v dočasném limitu kapacity. Toto omezení se plánuje až do konce září 2019.


### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>Otázka: Používá Log Analytics stejného agenta jako Azure Security Center?

Odpověď: v 2017. června Azure Security Center začala používat Microsoft Monitoring Agent ke shromažďování a ukládání dat. Další informace najdete v tématu [Nejčastější dotazy k migraci platforem Azure Security Center Platform](../../security-center/security-center-enable-data-collection.md).

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>Otázka: Jaké kontroly provádí AD a SQL Assessment řešení?

Odpověď: následující dotaz ukazuje popis všech aktuálně prováděných kontrol:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Výsledky pak lze exportovat do aplikace Excel pro další kontrolu.

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>Otázka: Proč se v konzole pro System Center Operations Manager zobrazuje něco jiného než OMS?

Odpověď: v závislosti na tom, jakou kumulativní aktualizaci Operations Manager máte, se může zobrazit uzel pro *System Center Advisor*, *Operational Insights*nebo *Log Analytics*.

Aktualizace textového řetězce na *OMS* je obsažená v Management Pack, kterou je třeba importovat ručně. Pokud chcete zobrazit aktuální text a funkci, postupujte podle pokynů v části nejnovější kumulativní aktualizace System Center Operations Manager článku znalostní báze Knowledge Base a aktualizujte konzolu.

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>Otázka: je k dispozici místní verze Log Analytics?

Odpověď: ne. Log Analytics je škálovatelná cloudová služba, která zpracovává a ukládá velké objemy dat. 

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>Otázka: Jak se dá upozornit, když se shromažďování dat zastaví?

Odpověď: použijte postup popsaný v tématu [Vytvoření nového upozornění protokolu](../../azure-monitor/platform/alerts-metric.md) pro oznámení o zastavení shromažďování dat.

Při vytváření výstrahy pro při zastavení shromažďování dat nastavte:

- **Definujte podmínku upozornění** – Jako cíl prostředku zadejte svůj pracovní prostor služby Log Analytics.
- **Kritéria upozornění** – Zadejte následující:
   - **Název signálu** vyberte **hledání vlastního protokolu**.
   - **Vyhledávací dotaz** na `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`.
   - **Logika upozornění** je **Založená na** *počtu výsledků* a **Podmínka** je *Větší než* **Prahová hodnota** *0*.
   - **Časové období** *30* minut a **četnosti výstrah** každých *10* minut
- **Definujte podrobnosti upozornění** – Zadejte následující:
   - **Název** pro *shromažďování dat se zastavil* .
   - **Závažnost** na *Upozornění*.

Určete existující nebo vytvořte novou [skupinu akcí](../../azure-monitor/platform/action-groups.md) , aby když výstraha protokolu odpovídala kritériím, zobrazí se oznámení v případě, že chybí prezenční signál po dobu více než 15 minut.

## <a name="configuration"></a>Konfigurace

### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>Otázka: Můžu změnit název tabulky nebo kontejneru objektů blob, který se používá pro čtení z Azure Diagnostics (WAD)?

A. Ne, v tuto chvíli není možné číst z libovolných tabulek nebo kontejnerů ve službě Azure Storage.

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>Otázka: Jaké IP adresy používá služba Log Analytics? Návody zajistěte, aby brána firewall povolovala jenom provoz do služby Log Analytics?

A. Služba Log Analytics je postavená nad Azure. Log Analytics IP adresy jsou v [rozsahu IP adres datového centra Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

V případě nasazení služeb jsou aktuální IP adresy služby Log Analytics změněny. Názvy DNS, které se mají přes bránu firewall použít, jsou popsané v části [požadavky na síť](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements).

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>Otázka: Používám ExpressRoute pro připojení k Azure. Používá můj Log Analytics provoz moje připojení ExpressRoute?

A. Různé typy provozu ExpressRoute jsou popsány v [dokumentaci k ExpressRoute](../../expressroute/expressroute-faqs.md#supported-services).

Provoz do Log Analytics využívá okruh ExpressRoute s veřejným partnerským vztahem.

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>Otázka: Existuje jednoduchý a snadný způsob, jak přesunout existující Log Analytics pracovní prostor do jiného pracovního prostoru Log Analytics nebo předplatného Azure?

A. Rutina `Move-AzResource` umožňuje přesunout pracovní prostor Log Analytics a také účet Automation z jednoho předplatného Azure do jiného. Další informace najdete v tématu [Move-AzResource](https://msdn.microsoft.com/library/mt652516.aspx).

Tato změna se dá udělat taky v Azure Portal.

Data nemůžete přesunout z jednoho pracovního prostoru Log Analytics do jiného, nebo můžete změnit oblast, ve které jsou data Log Analytics uložená.

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>Otázka: Návody přidat Log Analytics do System Center Operations Manager?

Odpověď: aktualizace na nejnovější kumulativní aktualizaci a Import sad Management Pack umožňuje připojit Operations Manager k Log Analytics.

>[!NOTE]
>Připojení Operations Manager k Log Analytics je dostupné jenom pro System Center Operations Manager 2012 SP1 a novější.

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>Otázka: Jak mohu potvrdit, že agent může komunikovat s Log Analytics?

Odpověď: Chcete-li zajistit, že agent může komunikovat s Log Analytics pracovním prostorem, přejít na: Ovládací panely, nastavení & zabezpečení **Microsoft Monitoring Agent**.

Na kartě **Azure Log Analytics (OMS)** vyhledejte zelenou značku zaškrtnutí. Zelená ikona zaškrtnutí potvrzuje, že agent může komunikovat se službou Azure.

Žlutá ikona upozornění znamená, že agent má při komunikaci s Log Analytics problémy. Jednou z běžných příčin je, že se služba Microsoft Monitoring Agent zastavila. Pro restartování služby použijte Správce řízení služeb.

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>Otázka: Návody zastavit komunikaci agenta s Log Analytics?

Odpověď: v System Center Operations Manager odeberte počítač ze seznamu spravovaných počítačů s Log Analytics. Operations Manager aktualizuje konfiguraci agenta tak, aby se už nehlásila Log Analytics. Agenty, kteří jsou připojení k Log Analytics přímo, je můžete zastavit z komunikace prostřednictvím ovládacího panelu, nastavení & zabezpečení **Microsoft Monitoring Agent**.
V části **Azure Log Analytics (OMS)** odeberte všechny uvedené pracovní prostory.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>Otázka: Proč se při pokusu o přesunutí pracovního prostoru z jednoho předplatného Azure do jiného stala chyba?

Odpověď: Chcete-li přesunout pracovní prostor do jiného předplatného nebo skupiny prostředků, je třeba nejprve zrušit propojení účtu Automation v pracovním prostoru. Odpojení účtu Automation vyžaduje odebrání těchto řešení, pokud jsou nainstalovaná v pracovním prostoru: Update Management, Change Tracking nebo Start/Stop VMs during off-hours se odeberou. Po odebrání těchto řešení odpojte účet Automation výběrem **propojených pracovních prostorů** v levém podokně prostředku účtu Automation a kliknutím na **Zrušit propojení pracovního prostoru** na pásu karet.
 > Odebraná řešení je potřeba přeinstalovat v pracovním prostoru a po přesunu je potřeba přestavovat odkaz Automation na pracovní prostor.

Ujistěte se, že máte oprávnění v předplatných Azure.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-update-a-savedsearch"></a>Otázka: Proč se při pokusu o aktualizaci elementu savedsearch zobrazí chyba?

Odpověď: je nutné přidat ' ETag ' do těla rozhraní API nebo vlastnosti šablony Azure Resource Manager:
```
"properties": {
   "etag": "*",
   "query": "SecurityEvent | where TimeGenerated > ago(1h) | where EventID == 4625 | count",
   "displayName": "An account failed to log on",
   "category": "Security"
}
```

## <a name="agent-data"></a>Data agenta
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>Otázka: Kolik dat je možné odeslat agentem, aby se Log Analytics? Je pro každého zákazníka k dispozici maximální množství dat?
A. Množství dat, která se nahrávají, se nijak neomezuje, vychází z cenové možnosti, kterou vyberete, rezervace kapacity a průběžné platby. Pracovní prostor Log Analytics je navržený tak, aby se při zpracování objemu od zákazníka automaticky škáloval, a to i v případě, že je terabajty za den. Další informace najdete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/monitor/).

Agent Log Analytics byl navržený tak, aby zajistil, že má malé nároky. Objem dat se liší v závislosti na řešeních, která povolíte. Můžete najít podrobné informace o objemu dat a zobrazit rozpis podle řešení na stránce [využití](../../azure-monitor/platform/data-usage.md) .

Další informace si můžete přečíst na [blogu zákazníka](https://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) , který zobrazuje výsledky po vyhodnocení využití prostředků (nároky) agenta Log Analytics.

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>Otázka: Kolik šířky pásma sítě používá agent MMA (Microsoft Management Agent) při odesílání dat do Log Analytics?

A. Šířka pásma je funkce na množství odesílaných dat. Data se komprimují při posílání přes síť.

### <a name="q-how-much-data-is-sent-per-agent"></a>Otázka: Kolik dat se odesílá na agenta?

A. Množství dat odeslaných na agenta závisí na:

* Řešení, která jste povolili
* Počet shromažďovaných protokolů a čítačů výkonu
* Objem dat v protokolech

Celkové využití se zobrazí na stránce [využití](../../azure-monitor/platform/data-usage.md) .

Pro počítače, na kterých je možné spustit agenta WireData, použijte následující dotaz k zobrazení množství dat, která jsou odesílána:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>Další kroky

Začněte [s Azure monitor](../../azure-monitor/overview.md) , kde se dozvíte víc o Log Analytics a začněte pracovat během několika minut.
