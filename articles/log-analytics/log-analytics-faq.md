---
title: Protokolu Analytics – nejčastější dotazy | Microsoft Docs
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
ms.date: 06/19/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: eb1a60ff533e9e24f3dc80057129da47a2d9a726
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128526"
---
# <a name="log-analytics-faq"></a>Nejčastější dotazy k Log Analytics
Tato FAQ Microsoft je seznam často kladené otázky týkající se analýzy protokolů v Microsoft Azure. Pokud máte další dotazy o analýzy protokolů, přejděte k [diskusní fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) a zveřejněte svoje otázky. Dotaz je dotaz, často, přidáme ji k tomuto článku tak, aby se nachází snadno a rychle.

## <a name="general"></a>Obecné

### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>OTÁZKY. Používá analýzy protokolů stejného agenta jako Azure Security Center?

A. V časná června 2017 začal Azure Security Center pomocí agenta Microsoft Monitoring Agent shromažďovat a ukládat data. Další informace najdete v tématu [Azure Security Center platformy migrace – nejčastější dotazy](../security-center/security-center-platform-migration-faq.md).

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>OTÁZKY. Jaké ověřování AD a vyhodnocení SQL řešení?

A. Následující dotaz zobrazí popis aktuálně provést všechny kontroly:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Výsledky lze poté exportovat do Excelu hodnocení.

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>Otázka: Proč se zobrazuje něco jiného než OMS v konzole nástroje System Center Operations Manager?

Odpověď: v závislosti na jaké aktualizace souhrnu nástroje Operations Manager na, mohou se zobrazit uzel pro *System Center Advisor*, *Statistika provozu*, nebo *analýzy protokolů*.

Textový řetězec aktualizace *OMS* je součástí sady management pack, které je nutné ručně naimportovat. Pokud chcete zobrazit aktuální text a funkce, postupujte podle pokynů v článku nejnovější System Center Operations Manager aktualizace kumulativní KB a aktualizací konzoly nástroje.

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>Otázka: je k dispozici na místní verzi Log Analytics?

Odpověď: Ne. Analýzy protokolů je škálovatelná Cloudová služba, která zpracovává a ukládá velké objemy dat. 

### <a name="q-how-do-i-troubleshoot-if-log-analytics-is-no-longer-collecting-data"></a>OTÁZKY. Jak odstranit Pokud analýzy protokolů je už shromažďování dat?

Odpověď: pro předplatné a prostoru vytvořil před 2. dubna 2018, který je na *volné* cenovou úroveň, pokud je více než 500 MB dat odeslaných za den, shromažďování dat zastaví pro zbytek dne. Dosažení denního limitu je běžným důvodem, který analýzy protokolů zastaví shromažďování dat, nebo se zdá být chybí data.  

Analýzy protokolů vytváří událost typu *prezenčního signálu* a slouží k určení, pokud se shromažďování dat zastaví. 

Spuštěním následujícího dotazu v hledání, zkontrolujte, zda jsou dosažení denního limitu a chybějící data: `Heartbeat | summarize max(TimeGenerated)`

Pokud chcete zkontrolovat v určitém počítači, spusťte následující dotaz: `Heartbeat | where Computer=="contosovm" | summarize max(TimeGenerated)`

Když se shromažďování dat zastaví, v závislosti na časové rozmezí vybrána, neuvidíte žádné záznamy, vrátí.   

Následující tabulka popisuje důvody, které shromažďování dat zastaví a navrhovanou akci obnovíte shromažďování dat:

| Důvod shromažďování dat zastaví                       | Chcete-li obnovit shromažďování dat |
| -------------------------------------------------- | ----------------  |
| Dosažen limit volné dat<sup>1</sup>       | Počkejte, až do následujícího měsíce pro kolekci k automatickému restartu, nebo<br> Změnit na placené cenovou úroveň. |
| Předplatné Azure je v pozastaveném stavu z důvodu: <br> Bezplatná zkušební verze skončila <br> Platnost Azure průchodu <br> Měsíčně útrat dosažen (například na předplatné MSDN nebo v sadě Visual Studio)                          | Převést na placené předplatné <br> Převést na placené předplatné <br> Odeberte limit nebo počkejte, až se obnoví limit |

<sup>1</sup> Pokud pracovního prostoru na *volné* cenovou úroveň, jste omezené s odesíláním 500 MB dat za den ke službě. Po dosažení denního limitu shromažďování dat zastaví až další den. Data odeslaná při ukončení shromažďování dat je není indexované a není k dispozici pro vyhledávání. Když se obnoví shromažďování dat, dojde k zpracování pouze pro nová data odeslána. 

Čas UTC využívá analýzy protokolů a spustí každý den o půlnoci času UTC. Pokud v pracovním prostoru dosáhne denního limitu, obnoví zpracování během první hodiny následujícího dne jeden UTC.

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>OTÁZKY. Jak můžete se upozornění, když se shromažďování dat zastaví?

Odpověď: pomocí kroků popsaných v [vytvořit novou výstrahu protokolu](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) oznámení o shromažďování dat zastaví.

Při vytváření výstrahy pro při shromažďování dat zastaví, nastavte:

- **Definujte podmínku upozornění** – Jako cíl prostředku zadejte svůj pracovní prostor Log Analytics.
- **Kritéria upozornění** – Zadejte následující:
   - **Název signálu** vyberte **hledání protokolů vlastní**.
   - **Vyhledávací dotaz** na `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`.
   - **Logika upozornění** je **Založená na** *počtu výsledků* a **Podmínka** je *Větší než* **Prahová hodnota** *0*.
   - **Časové období** z *30* minut a **výstrahy frekvence** ke každému *10* minut
- **Definujte podrobnosti upozornění** – Zadejte následující:
   - **Název** k *zastavit shromažďování dat*
   - **Závažnost** na *Upozornění*.

Zadejte existující, nebo vytvořte novou [akce skupiny](../monitoring-and-diagnostics/monitoring-action-groups.md) tak, aby při protokolu výstrahy odpovídající kritériím, budete upozorněni, pokud máte prezenční signál pro více než 15 minut.

## <a name="configuration"></a>Konfigurace
### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>OTÁZKY. Můžete změnit název tabulky nebo objekt blob kontejneru použít ke čtení z Azure Diagnostics (WAD)?

A. Ne, není aktuálně možné číst z libovolné tabulky nebo kontejnery v úložišti Azure.

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>OTÁZKY. Použití služby analýzy protokolů IP adres podporuje? Jak zjistím, branou firewall umožňuje pouze provoz ke službě Analýza protokolů?

A. Službu analýzy protokolů je postavená na Azure. Log Analytics IP adresy jsou v [Microsoft Azure Datacenter rozsahy IP adres](http://www.microsoft.com/download/details.aspx?id=41653).

Podle nasazení služby jsou vytvářeny, skutečný IP adresy služby analýzy protokolů se nezmění. Názvy DNS umožňuje prostřednictvím brány firewall jsou dokumentovány v článku [požadavky na síťovou](log-analytics-concept-hybrid.md#network-firewall-requirements).

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>OTÁZKY. Je možné použít ExpressRoute pro připojení k Azure. Používá Moje analýzy protokolů přenosů Moje připojení ExpressRoute?

A. Různé typy provozu ExpressRoute jsou popsány v [dokumentace ExpressRoute](../expressroute/expressroute-faqs.md#supported-services).

Přenos dat k analýze protokolů používá partnerský vztah veřejné okruh ExpressRoute.

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>OTÁZKY. Je jednoduchý a snadný způsob, jak přesunout do jiného předplatného pracovního prostoru nebo Azure Log Analytics existujícímu pracovnímu prostoru analýzy protokolů?

A. `Move-AzureRmResource` Rutina umožňuje přesunout pracovní prostor analýzy protokolů a účet služby Automation z jedno předplatné do jiného. Další informace najdete v tématu [přesunutí AzureRmResource](http://msdn.microsoft.com/library/mt652516.aspx).

Tato změna můžete také provést na portálu Azure.

Nelze přesunout data z jednoho pracovního prostoru analýzy protokolů na jiný, nebo změňte oblast, která data Log Analytics je uložena v.

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>Otázka: jak přidat analýzy protokolů pro System Center Operations Manager?

Odpověď: aktualizace na nejnovější kumulativní aktualizaci a import sad management Pack umožňuje připojení k analýze protokolů nástroje Operations Manager.

>[!NOTE]
>Připojení nástroje Operations Manager k analýze protokolů je dostupná jenom pro System Center Operations Manager 2012 SP1 a novější.

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>Otázka: jak můžete potvrdit, že je agent komunikovat s analýzy protokolů

Odpověď: aby se zajistilo, že agent může komunikovat s OMS, přejděte na: Ovládací panely, zabezpečení a nastavení, **agenta Microsoft Monitoring Agent**.

V části **Azure Log Analytics (OMS)** kartě, vyhledejte zelená značka zaškrtnutí. Ikona zelená značka zaškrtnutí potvrdí, že agent moci komunikovat se službou Azure.

Žlutá ikona s upozorněním znamená, že agent má problémy komunikace s analýzy protokolů. Jeden obvyklým důvodem je, že služba Microsoft Monitoring Agent byla zastavena. Pomocí Správce řízení služeb se restartovat službu.

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>Otázka: Jak zabráním agenta v komunikaci s Log Analytics?

Odpověď: V nástroji System Center Operations Manager, odeberte počítač ze seznamu spravovaných počítačů OMS. Nástroje Operations Manager aktualizuje konfiguraci agenta k analýze protokolů už sestavy. Pro agenty přímo připojené k analýze protokolů, můžete zastavit je komunikaci prostřednictvím: Ovládací panely, zabezpečení a nastavení, **agenta Microsoft Monitoring Agent**.
V části **Azure Log Analytics (OMS)**, odeberte všechny pracovní prostory uvedené.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>Otázka: Proč dochází k chybě při pokusu o přesunutí pracovního prostoru z jedno předplatné na jiný?

Odpověď: Pokud používáte portál Azure, ověřte, že je vybraná jenom v pracovním prostoru pro přesunutí. Nevybírejte řešení – se automaticky přesune po přesune pracovním prostoru. 

Zkontrolujte, zda že máte oprávnění v obou předplatných Azure.

## <a name="agent-data"></a>Data agenta
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>OTÁZKY. Množství dat, můžete odeslat prostřednictvím agenta k analýze protokolů? Existuje maximální množství dat za zákazníka?
A. Plánu úrovně free Nastaví denní cap 500 MB za pracovního prostoru. Plány standard a premium mají omezení není nastaveno na množství dat, který je nahraný. Jako cloudová služba, analýzy protokolů slouží k automatické škálování až popisovač svazku pocházejících z zákazník – i když je terabajtů za den.

Analýzy protokolů agenta byla navržená tak, aby Ujistěte se, že má malé nároky. Datový svazek se liší v závislosti na řešení, která je povolit. Můžete najít podrobné informace o objemu dat a řešení v najdete v části rozdělení [využití](log-analytics-usage.md) stránky.

Další informace si můžete přečíst [zákazníka blog](http://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) zobrazující jejich výsledky po vyhodnocení využití prostředků (nároků) agenta OMS.

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>OTÁZKY. Při odesílání dat k analýze protokolů, jaký poměr šířky pásma sítě se používá pomocí správy agenta MMA (Microsoft)?

A. Šířka pásma je funkce na množství dat odesílaných. Data se komprimují při přenosu přes síť.

### <a name="q-how-much-data-is-sent-per-agent"></a>OTÁZKY. Množství dat, které jsou odeslány na jednoho agenta?

A. Množství dat odesílaných na jednoho agenta závisí na:

* Řešení, které jste povolili
* Počet shromažďovaných čítače výkonu a protokolování
* Objem dat v protokolech

Cenová úroveň free je dobrý způsob, jak připojit několik serverů a měřidla typické datový svazek. Celkové využití se zobrazí na [využití](log-analytics-usage.md) stránky.

Pro počítače, které je možné spouštět WireData agenta použijte následující dotaz Pokud chcete zobrazit, kolik data je odesílána:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>Další postup
* [Začínáme s analýzy protokolů](log-analytics-get-started.md) získáte další informace o analýzy protokolů a zprovoznění v minutách.
