---
title: Směrný plán zabezpečení Azure pro centra událostí
description: Směrný plán zabezpečení Azure pro centra událostí
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 1c7e18214ec175b8b21d25643098631a044da19c
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758314"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Směrný plán zabezpečení Azure pro centra událostí

Směrný plán zabezpečení Azure pro centra událostí obsahuje doporučení, která vám pomohou zlepšit stav zabezpečení vašeho nasazení.

Směrný plán pro tuto službu vychází z [azure security benchmark verze 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), který poskytuje doporučení, jak můžete zabezpečit cloudová řešení v Azure s našimi pokyny pro osvědčené postupy.

Další informace naleznete v [tématu Přehled směrného plánu zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace naleznete v [tématu Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Ochrana prostředků pomocí skupin zabezpečení sítě nebo brány Azure Firewall ve virtuální síti

**Pokyny:** Integrace centra událostí s koncovými body služby virtuální sítě umožňuje zabezpečený přístup k možnostem zasílání zpráv z úloh, jako jsou virtuální počítače, které jsou vázané na virtuální sítě, přičemž cesta síťového provozu je zabezpečená na obou koncích.

Jakmile je příslušný obor názvů Event Hubs vázán alespoň na jeden koncový bod služby podsítě virtuální sítě, již nepřijímá provoz odkudkoli, ale autorizované podsítě ve virtuálních sítích. Z hlediska virtuální sítě vazba oboru názvů Event Hubs na koncový bod služby konfiguruje izolovaný síťový tunel z podsítě virtuální sítě do služby zasílání zpráv. 

Můžete také vytvořit privátní koncový bod, což je síťové rozhraní, které vás soukromě a bezpečně připojí ke službě Azure Event Hubs pomocí služby Azure Private Link. Privátní koncový bod používá privátní IP adresu z vaší virtuální sítě, efektivně uvedení služby do virtuální sítě. Veškerý provoz na službu lze směrovat přes soukromý koncový bod, takže nejsou potřeba žádné brány, zařízení NAT, připojení ExpressRoute nebo VPN nebo veřejné IP adresy. 

Obor názvů Azure Event Hubs můžete také zabezpečit pomocí bran firewall. Azure Event Hubs podporuje ovládací prvky přístupu založené na IP adresách pro příchozí podporu brány firewall. Pravidla brány firewall můžete nastavit pomocí portálu Azure, šablon Azure Resource Manager nebo pomocí azure cli nebo Azure PowerShell.

Jak používat koncové body služby virtuální sítě s Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Další informace najdete v tématu Integrace Center https://docs.microsoft.com/azure/event-hubs/private-link-serviceudálostí Azure s Privátním propojením Azure: .

Povolit integraci virtuálních sítí a brány firewall v oboru názvů Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-tutorial-virtual-networks-firewalls

Jak nakonfigurovat pravidla brány firewall IP pro obory názvů Centra událostí Azure:https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Sledování a protokolování konfigurace a provozu sítí, podsítí a síťových rozhraní

**Pokyny:** Použijte Azure Security Center a postupujte podle doporučení ochrany sítě, abyste pomohli zabezpečit prostředky Event Hubs v Azure. Pokud používáte virtuální počítače Azure pro přístup k vašim centrům událostí, povolte protokoly toku skupiny zabezpečení sítě (NSG) a odesílejte protokoly do účtu úložiště pro audit provozu.

Povolení protokolů toku nsg:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Principy zabezpečení sítě poskytované službou Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="13-protect-critical-web-applications"></a>1.3: Ochrana kritických webových aplikací

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro webové aplikace spuštěné ve službě Azure App Service nebo výpočetních prostředcích.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odepřít komunikaci se známými škodlivými IP adresami

**Pokyny:** Povolte standard ochrany DDoS ve virtuálních sítích přidružených k rozbočovačům událostí, abyste se ochránili před distribuovanými útoky dohledu (DDoS). Pomocí integrované analýzy hrozeb Centra zabezpečení Azure můžete odepřít komunikaci se známými škodlivými nebo nepoužívanými internetovými IP adresami.

Jak nakonfigurovat ochranu Před sdos:[https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](/azure/virtual-network/manage-ddos-protection)

Další informace o integrované hrozbové inteligenci Centra zabezpečení Azure:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Záznam síťových paketů a protokolů toku

**Pokyny:** Pokud pomocí virtuálních počítačů Azure pro přístup k rozbočovačům událostí povolte protokoly toku skupiny zabezpečení sítě (NSG) a odesílejte protokoly do účtu úložiště pro audit provozu. Protokoly toku nsg můžete také odesílat do pracovního prostoru Analýzy protokolů a pomocí traffic analytics poskytovat přehled o toku provozu ve vašem cloudu Azure. Některé výhody Traffic Analytics jsou schopnost vizualizovat aktivitu v síti a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzorce toku provozu a určit chybné konfigurace sítě.

Pokud je to nutné pro zkoumání neobvyklé aktivity, povolte zachytávání paketů Sledování sítě.

Povolení protokolů toku nsg:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak povolit a používat Traffic Analytics:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Jak povolit sledování sítě:https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Nasazení síťových systémů detekce vniknutí/prevence vniknutí (IDS/IPS)

**Pokyny:** Pokud používáte virtuální počítače Azure pro přístup k vašim centrům událostí, vyberte nabídku z Azure Marketplace, která podporuje funkce IDS/IPS s možnostmi kontroly datové části. Pokud detekce vniknutí nebo prevence na základě kontroly datové části není vyžadováno pro vaši organizaci, můžete použít integrovanou funkci brány firewall Služby Azure Event Hubs. Přístup k oboru názvů centra Event Hubs můžete omezit na omezený rozsah adres IP nebo na určitou adresu IP pomocí pravidel brány firewall.

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

Jak přidat pravidlo brány firewall do event hubů pro zadanou adresu IP:

 https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Monitorování Centra zabezpečení Azure:** Ještě není k dispozici

**Odpovědnost**: Zákazník

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Správa návštěvnosti webových aplikací

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro webové aplikace spuštěné ve službě Azure App Service nebo výpočetních prostředcích.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimalizace složitosti a administrativní režie pravidel zabezpečení sítě

**Pokyny**: Není použitelné, toto doporučení je určeno pro webové aplikace spuštěné ve službě Azure App Service nebo výpočetních prostředcích.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Udržovat standardní konfigurace zabezpečení pro síťová zařízení

**Pokyny:** Definujte a implementujte standardní konfigurace zabezpečení pro síťové prostředky přidružené k oborům názvů Azure Event Hubs pomocí zásad Azure. Pomocí aliasů zásad Azure v oborech názvů Microsoft.EventHub a Microsoft.Network vytvořte vlastní zásady pro auditování nebo vynucení síťové konfigurace oborů názvů Event Hubs. Můžete také využít předdefinované definice zásad související s Azure Event Hubs, jako jsou:

- Centrum událostí by mělo používat koncový bod služby virtuální sítě.

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Integrované zásady Azure pro obor názvů Event Hubs:https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub



Ukázky zásad Azure pro sítě:https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network



Jak vytvořit Azure Blueprint:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentovat pravidla konfigurace provozu

**Pokyny:** Používejte značky pro virtuální sítě a další prostředky související se zabezpečením sítě a tokem provozu, které jsou přidruženy k rozbočovačům událostí.

Jak vytvářet a používat značky:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Použití automatizovaných nástrojů ke sledování konfigurace síťových prostředků a zjišťování změn

**Pokyny:** Pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků souvisejících s azure event huby. Vytvořte výstrahy v rámci Azure Monitor, který se aktivuje, když dojde ke změnám kritických síťových prostředků.

Jak zobrazit a načíst události protokolu aktivit Azure:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Jak vytvořit výstrahy ve službě Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace naleznete [v tématu Security Control: Logging and Monitoring](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Použití schválených zdrojů synchronizace času

**Pokyny**: Nepoužije se; Microsoft udržuje zdroj času používaný pro prostředky Azure, jako jsou Azure Event Hubs, pro časová razítka v protokolech.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurace správy centrálního protokolu zabezpečení

**Pokyny:** V rámci Azure Monitor, nakonfigurujte protokoly související s centra událostí v rámci protokolu aktivit a centrum událostí nastavení diagnostiky pro odesílání protokolů do pracovního prostoru Log Analytics, které mají být dotazovány nebo do účtu úložiště pro dlouhodobé archivní úložiště.

Jak nakonfigurovat nastavení diagnostiky pro Centra událostí Azure:https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Principy protokolu aktivit Azure:https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Povolení protokolování auditování prostředků Azure

**Pokyny:** Povolte nastavení diagnostiky pro obor názvů Centra událostí Azure. Existují tři kategorie nastavení diagnostiky pro Centra událostí Azure: protokoly archivu, provozní protokoly a protokoly automatického škálování. Povolte provozní protokoly, abyste mohli zachytit informace o tom, co se děje během operací Event Hubs, konkrétně typu operace, včetně vytvoření centra událostí, použitých prostředků a stavu operace.

Kromě toho můžete povolit nastavení diagnostiky protokolu aktivit Azure a odeslat je do účtu úložiště Azure, centra událostí nebo do pracovního prostoru Log Analytics. Protokoly aktivit poskytují přehled o operacích, které byly provedeny na azure event huby a další prostředky. Pomocí protokolů aktivit můžete určit "co, kdo a kdy" pro všechny operace zápisu (PUT, POST, DELETE) převzaté ve vašich oborech názvů Centra událostí Azure.

Jak povolit nastavení diagnostiky pro Centra událostí Azure:https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Jak povolit diagnostická nastavení pro protokol aktivit Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Shromažďování protokolů zabezpečení z operačních systémů

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Pokyny:** V rámci Azure Monitor, nastavte dobu uchovávání pracovního prostoru Log Analytics podle předpisů vaší organizace dodržování předpisů pro zachycení a kontrolu událostí hub-související incidenty.

Jak nastavit parametry uchovávání protokolu pro pracovní prostory Analýzy protokolů:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="26-monitor-and-review-logs"></a>2.6: Sledování a kontrola protokolů

**Pokyny:** Analyzujte a monitorujte protokoly pro neobvyklé chování a pravidelně kontrolujte výsledky související s centry událostí. Pomocí analýzy protokolů Azure Monitor můžete kontrolovat protokoly a provádět dotazy na data protokolu. Případně můžete povolit a na palubě data azure sentinelu nebo třetí strany SIEM.
 

Další informace o pracovním prostoru Analýzy protokolů:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Jak provádět vlastní dotazy v Azure Monitoru:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

Jak napalubě Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Povolit upozornění na anomální aktivitu

**Pokyny:** V rámci Azure Monitor, nakonfigurujte protokoly související s Azure Event Hubs v rámci protokolu aktivit a nastavení diagnostiky centra událostí pro odesílání protokolů do pracovního prostoru Log Analytics, který má být dotazován, nebo do účtu úložiště pro dlouhodobé úložiště archivace. Pomocí pracovního prostoru Log Analytics můžete vytvářet výstrahy pro neobvyklé aktivity nalezené v protokolech zabezpečení a událostech.

Případně můžete povolit a na palubě data do Azure Sentinelu. 

Seznamte se s protokolem aktivit Azure:https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

Jak nakonfigurovat nastavení diagnostiky pro Centra událostí Azure:https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

How to alert on Log Analytics workspace log data:https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

Jak napalubě Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorování Centra zabezpečení Azure:** Ještě není k dispozici

**Odpovědnost**: Zákazník

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizovat protokolování antimalwaru

**Pokyny**: Nepoužije se; Centrum událostí nezpracovává protokolování antimalwaru.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="29-enable-dns-query-logging"></a>2.9: Povolení protokolování dotazů DNS

**Pokyny**: Nepoužije se; Centra událostí nezpracovává ani nevytváří protokoly související se službou DNS.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="210-enable-command-line-audit-logging"></a>2.10: Povolení protokolování auditu příkazového řádku

**Pokyny**: Nepoužije se; Tento pokyn je určen pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace naleznete [v tématu Zabezpečení: Identita a řízení přístupu](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Vedení soupisu administrativních účtů

**Pokyny:** Služba Azure Active Directory (AD) má předdefinované role, které musí být explicitně přiřazeny a jsou dotazovatelné. Pomocí modulu Azure AD PowerShell můžete provádět ad hoc dotazy ke zjišťování účtů, které jsou členy skupin pro správu. 

Jak získat roli adresáře ve službě Azure AD s PowerShellem:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Jak získat členy role adresáře ve službě Azure AD s PowerShellem:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Změna výchozích hesel, pokud je to možné

**Navádění:** Řízení přístupu k rozbočovačům událostí je řízeno prostřednictvím služby Azure Active Directory (AD). Azure AD nemá koncept výchozí hesla.

Přístup k centru událostí se řídí prostřednictvím Azure AD se spravovanými identitami nebo registracemi aplikací a také se sdílenými přístupovými podpisy. Sdílené přístupové podpisy používají klienti, kteří se připojují k vašim centrům událostí, a lze je kdykoli obnovit.

Principy sdílených přístupových podpisů pro centra událostí:https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Použití vyhrazených administrativních účtů

**Pokyny**: Vytvořit standardní operační postupy týkající se používání vyhrazených administrativních účtů. Pomocí správy identit a přístupu Centra zabezpečení Azure můžete sledovat počet účtů pro správu.

Kromě toho můžete použít doporučení z Azure Security Center nebo integrované zásady Azure, jako jsou:

- K vašemu předplatnému by mělo být přiřazeno více než jednoho vlastníka.

- Zastaralé účty s oprávněními vlastníka by měly být odebrány z vašeho předplatného

- Z předplatného by měly být odebrány externí účty s oprávněními vlastníka.

Jak pomocí Azure Security Center sledovat identitu a přístup (Preview):https://docs.microsoft.com/azure/security-center/security-center-identity-access

Jak používat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Použití jednotného přihlašování (SSO) s Azure Active Directory

**Pokyny:** Microsoft Azure poskytuje integrovanou správu řízení přístupu pro prostředky a aplikace založené na Azure Active Directory (AD). Klíčovou výhodou používání Azure AD s Azure Event Hubs je, že už nemusíte ukládat přihlašovací údaje do kódu. Místo toho můžete požádat o přístupový token OAuth 2.0 z platformy Microsoft Identity. Název prostředku pro vyžádání tokenu\/je https: /eventhubs.azure.net/. Azure AD ověřuje intezuru zabezpečení (uživatele, skupiny nebo instančního objektu) spuštěné aplikace. Pokud je ověření úspěšné, Azure AD vrátí přístupový token do aplikace a aplikace pak můžete použít přístupový token k autorizaci požadavku na prostředky Azure Event Hubs.

Jak ověřit aplikaci pomocí Azure AD pro přístup k prostředkům Centra událostí:https://docs.microsoft.com/azure/event-hubs/authenticate-application

Principy přisážacího zabezpečení pomocí Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Použití vícefaktorového ověřování pro veškerý přístup založený na službě Azure Active Directory

**Pokyny:** Povolte vícefaktorové ověřování Azure Active Directory (MFA) a postupujte podle doporučení pro správu identit centra zabezpečení Azure a správy přístupu, abyste pomohli chránit prostředky centra událostí.

Jak povolit vícefaktorové povolení v Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Jak sledovat identitu a přístup v rámci Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Pro všechny administrativní úkoly používejte vyhrazené počítače (pracovní stanice s privilegovaným přístupem)

**Pokyny**: Používejte pracovní stanice s privilegovaným přístupem (PAW) s vícefaktorovým ověřováním (MFA) nakonfigurovaným pro přihlášení a konfiguraci prostředků s podporou centra událostí.

Další informace o pracovních stanicích s privilegovaným přístupem:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Jak povolit vícefaktorové povolení v Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Protokolování a upozornění na podezřelou aktivitu z administrativních účtů

**Pokyny:** Použití Azure Active Directory (AD) Privileged Identity Management (PIM) pro generování protokolů a výstrah, když dojde k podezřelé nebo nebezpečné aktivity v prostředí. Pomocí zjišťování rizik Azure AD můžete zobrazit výstrahy a sestavy o rizikovém chování uživatelů. Další protokolování můžete zasílat výstrahy Azure Security Center pro zjišťování rizik do Azure Monitoru a konfigurovat vlastní výstrahy/oznámení pomocí skupin akcí.

Jak nasadit správu privilegovaných identit (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Seznamte se s detekcí rizik Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Jak nakonfigurovat skupiny akcí pro vlastní výstrahy a oznámení:https://docs.microsoft.com/azure/azure-monitor/platform/action-groups

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Správa prostředků Azure z jenom schválených umístění

**Pokyny:** Pomocí pojmenovaných umístění podmíněného přístupu můžete povolit přístup pouze z určitých logických seskupení oblastí IP adres nebo zemí nebo oblastí.



Jak nakonfigurovat pojmenovaná umístění v Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="39-use-azure-active-directory"></a>3.9: Použití služby Azure Active Directory

**Pokyny:** Použijte Azure Active Directory (AD) jako centrální ověřovací a autorizační systém pro prostředky Azure, jako jsou centra událostí. To umožňuje řízení přístupu na základě rolí (RBAC) na prostředky citlivé na správu.

 Jak vytvořit a nakonfigurovat instanci Azure AD:https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Informace o tom, jak se Azure Event Hubs integrují s Azure Active Directory (AAD), najdete v tématu Autorizace přístupu k prostředkům Centra událostí pomocí Azure Active Directory:https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Pravidelně kontrolujte a slaďujte přístup uživatelů

**Pokyny:** Služba Azure Active Directory (AD) poskytuje protokoly, které vám pomohou zjistit zastaralé účty. Kromě toho můžete pomocí azure identity access reviews efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazení rolí. Přístup uživatelů lze pravidelně kontrolovat, aby se zajistilo, že pouze ti praví uživatelé mají trvalý přístup.

V dalším případě pravidelně otáčejte sdílenými přístupovými podpisy centra událostí.

Principy sestav Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Jak používat recenze přístupu k identitám Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

Principy sdílených přístupových podpisů pro centra událostí:https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Sledování pokusů o přístup k deaktivovaným účtům

**Pokyny:** Máte přístup k aktivitě přihlášení služby Azure Active Directory (AD), zdrojům protokolu auditu a protokolu rizikových událostí, které umožňují integraci s libovolným nástrojem SIEM/Monitoring.

Tento proces můžete zjednodušit vytvořením diagnostických nastavení pro uživatelské účty Azure AD a odesláním protokolů auditu a protokolů přihlášení do pracovního prostoru Log Analytics. Můžete nakonfigurovat požadované výstrahy protokolu v rámci Log Analytics.

Jak integrovat protokoly aktivit Azure do Azure Monitoru:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Autorizace přístupu k prostředkům Centra událostí pomocí Služby Azure Active Directory:https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Upozornění na odchylku chování přihlášení k účtu

**Pokyny:** Pomocí funkcí ochrany identity azure active directory a funkce detekce rizik nakonfigurujte automatické odpovědi na zjištěné podezřelé akce související s prostředky s povolenými centry centra událostí. Měli byste povolit automatické odpovědi prostřednictvím Azure Sentinelu k implementaci odpovědí na zabezpečení vaší organizace.

Jak zobrazit Azure AD riskantní přihlášení:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Jak nakonfigurovat a povolit zásady rizik a zásady ochrany identity:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Jak napalubě Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Poskytněte společnosti Microsoft přístup k relevantním zákaznickým datům během scénářů podpory

**Pokyny**: V současné době není k dispozici; Customer Lockbox ještě není podporován pro centra událostí.

Seznam služeb podporovaných customer lockboxem:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Momentálně není k dispozici

## <a name="data-protection"></a>Ochrana dat

*Další informace naleznete [v tématu Security Control: Data Protection](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Udržovat soupis citlivých informací

**Pokyny:** Pomocí značek na prostředky související s event huby pomoci při sledování prostředků Azure, které ukládají nebo zpracovávají citlivé informace.

Jak vytvořit a používat značky:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Izolovat systémy ukládající nebo zpracovávající citlivé informace

**Pokyny**: Implementujte samostatné odběry nebo skupiny pro správu pro vývoj, testování a výrobu. Obory názvů Event Hubs by měly být odděleny virtuální sítí s povolenými a odpovídajícím označením koncových bodů služby.

Obor názvů Azure Event Hubs můžete také zabezpečit pomocí bran firewall. Azure Event Hubs podporuje ovládací prvky přístupu založené na IP adresách pro příchozí podporu brány firewall. Pravidla brány firewall můžete nastavit pomocí portálu Azure, šablon Azure Resource Manager nebo pomocí azure cli nebo Azure PowerShell.

Jak vytvořit další předplatná Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak vytvořit skupiny pro správu:https://docs.microsoft.com/azure/governance/management-groups/create

Konfigurace pravidel brány firewall IP pro obory názvů Centra událostí Azure:https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Jak vytvářet a využívat značky:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Jak vytvořit virtuální síť:https://docs.microsoft.com/azure/virtual-network/quick-create-portal

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Sledování a blokování neoprávněného přenosu citlivých informací

**Pokyny:** Při použití virtuálních počítačů pro přístup k rozbočovačům událostí využijte virtuální sítě, koncové body služby, bránu firewall centra událostí, skupiny zabezpečení sítě a značky služeb, abyste zmírnili možnost exfiltrace dat.

Microsoft spravuje základní infrastrukturu pro Azure Event Hubs a zavedl přísné ovládací prvky, aby zabránil ztrátě nebo vystavení zákaznických dat.

Konfigurace pravidel brány firewall IP pro obory názvů Centra událostí Azure:https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Principy koncových bodů služby Virtuální síť pomocí center událostí Azure:https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Integrace Center událostí Azure s Privátním propojením Azure:https://docs.microsoft.com/azure/event-hubs/private-link-service

Principy skupin zabezpečení sítě a značek služeb:https://docs.microsoft.com/azure/virtual-network/security-overview

Seznamte se s ochranou zákaznických dat v Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Šifrování všech citlivých informací při přepravě

**Pokyny:** Azure Event Hubs ve výchozím nastavení vynucuje komunikaci šifrovanou tls. V současné době jsou podporovány verze TLS 1.0, 1.1 a 1.2. TLS 1.0 a 1.1 jsou však na cestě k vyřazení v celém odvětví, takže pokud je to možné, použijte TLS 1.2.

Informace o funkcích zabezpečení centra událostí naleznete v tématu Zabezpečení sítě:https://docs.microsoft.com/azure/event-hubs/network-security

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Použití aktivního nástroje pro zjišťování k identifikaci citlivých dat

**Pokyny:** Funkce identifikace, klasifikace a prevence ztrát dat ještě nejsou k dispozici pro Centra událostí Azure. Implementujte řešení třetích stran, pokud je to nutné pro účely dodržování předpisů.

Pro základní platformu, která je spravována společností Microsoft, společnost Microsoft považuje veškerý obsah zákazníků za citlivý a zachází velmi daleko, aby se ochránila před ztrátou a expozicí dat zákazníků. Aby byla zákaznická data v Azure stále zabezpečená, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a funkcí ochrany dat.

Seznamte se s ochranou zákaznických dat v Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Sdíleno

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Použití Azure RBAC k řízení přístupu k prostředkům

**Pokyny:** Centra událostí Azure podporuje použití Služby Azure Active Directory (AD) k autorizaci požadavků na prostředky centra událostí. S Azure AD, můžete použít řízení přístupu na základě rolí (RBAC) udělit oprávnění k zaregistrovaný objekt zabezpečení, který může být uživatel nebo instanční objekt služby aplikace.

Principy Azure AD RBAC a dostupné role pro Centra událostí Azure:https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Použití ochrany před ztrátou dat na hostiteli k vynucení kontroly přístupu

**Pokyny**: Nepoužije se; Tento pokyn je určen pro výpočetní prostředky.

Společnost Microsoft spravuje základní infrastrukturu pro centra událostí a zavedla přísné kontroly, aby zabránila ztrátě nebo vystavení zákaznických dat.

Seznamte se s ochranou zákaznických dat v Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Šifrování citlivých informací v klidovém stavu

**Pokyny**: Azure Event Hubs podporuje možnost šifrování dat v klidovém stavu pomocí klíčů spravovaných společností Microsoft nebo klíčů spravovaných zákazníky. Tato funkce umožňuje vytvářet, otáčet, zakázat a odvolat přístup ke klíčům spravovaným zákazníkem, které se používají k šifrování dat centra událostí Azure v klidovém stavu.

Jak nakonfigurovat klíče spravované zákazníkem pro šifrování Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokolování a upozorňování na změny kritických prostředků Azure

**Pokyny:** Pomocí Azure Monitor s protokolem aktivit Azure vytvořit výstrahy pro kdy dojde ke změnám na produkční instance Centra událostí Azure a dalších kritických nebo souvisejících prostředků.

Jak vytvořit výstrahy pro události protokolu aktivit Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace naleznete v [tématu Security Control: Vulnerability Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Spuštění automatizovaných nástrojů pro vyhledávání chyb zabezpečení

**Pokyny**: Nepoužije se; Společnost Microsoft provádí správu chyb zabezpečení v základních systémech, které podporují centra událostí.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Nasazení řešení pro správu automatických oprav operačního systému

**Pokyny**: Nepoužije se; Společnost Microsoft provádí správu oprav v základních systémech, které podporují centra událostí.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Microsoft

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Nasazení automatizovaného řešení pro správu softwarových oprav od jiných výrobců

**Pokyny**: Nepoužije se; referenční hodnota je určena pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Microsoft

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Porovnání prohledávačů ohrožení zabezpečení zády k zadní straně

**Pokyny**: Nepoužije se; Společnost Microsoft provádí správu chyb zabezpečení v základních systémech, které podporují centra událostí.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Použití procesu hodnocení rizik a upřednostnění nápravy zjištěných zranitelných míst

**Pokyny**: Nepoužije se; Společnost Microsoft provádí správu chyb zabezpečení v základních systémech, které podporují centra událostí.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Microsoft

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace naleznete v [tématu Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Použití Azure Asset Discovery

**Pokyny:** Pomocí Azure Resource Graph můžete dotazovat a zjišťovat všechny prostředky (včetně oborů názvů Centra událostí Azure) v rámci vašeho předplatného. Ujistěte se, že máte příslušná (číst) oprávnění ve vašem tenantovi a jsou schopni vytvořit výčet všech předplatných Azure, stejně jako prostředky v rámci vašich předplatných.

Jak vytvářet dotazy pomocí Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Jak zobrazit předplatná Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Principy Azure RBAC:https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="62-maintain-asset-metadata"></a>6.2: Udržování metadat datových zdrojů

**Pokyny:** Použijte značky pro prostředky Azure, které poskytují metadata, aby je logicky uspořádali do taxonomie.

Jak vytvářet a používat značky:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Odstranění neautorizovaných prostředků Azure

**Pokyny:** Pomocí značkování, skupin pro správu a samostatných předplatných, kde je to vhodné, můžete uspořádat a sledovat obory názvů Centra událostí Azure a související prostředky. Pravidelně odsouhlasete zásoby a zajistěte, aby byly z předplatného včas odstraněny neoprávněné prostředky.

Jak vytvořit další předplatná Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak vytvořit skupiny pro správu:https://docs.microsoft.com/azure/governance/management-groups/create

Jak vytvořit a používat značky:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Udržovat soupis schválených prostředků Azure a softwarových titulů

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky a Azure jako celek.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorování neschválených prostředků Azure

**Pokyny**: Pomocí zásad Azure můžete omezit typ prostředků, které lze vytvořit v předplatných zákazníků pomocí následujících předdefinovaných definic zásad:

- Nepovolené typy prostředků

- Povolené typy prostředků

Kromě toho použijte Azure Resource Graph k dotazování/zjišťování prostředků v rámci předplatného.

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak vytvářet dotazy pomocí Azure Graphu:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Sledování neschválených softwarových aplikací v rámci výpočetních prostředků

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Odebrání neschválených prostředků Azure a softwarových aplikací

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky a Azure jako celek.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="68-use-only-approved-applications"></a>6.8: Používejte pouze schválené aplikace

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="69-use-only-approved-azure-services"></a>6.9: Používejte jenom schválené služby Azure

**Pokyny**: Pomocí zásad Azure můžete omezit typ prostředků, které lze vytvořit v předplatných zákazníků pomocí následujících předdefinovaných definic zásad:

- Nepovolené typy prostředků

- Povolené typy prostředků

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak odepřít konkrétní typ prostředku pomocí zásad Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="610-implement-approved-application-list"></a>6.10: Implementace schváleného seznamu žádostí

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>Omezte možnost uživatelů komunikovat se Správcem prostředků Azure pomocí skriptů</div>

**Pokyny:** Konfigurace podmíněného přístupu Azure, chcete-li omezit možnost uživatelů pracovat se Správcem prostředků Azure konfigurací "Blokovat přístup" pro aplikaci "Správa Microsoft Azure".

Jak nakonfigurovat podmíněný přístup pro blokování přístupu ke Správci prostředků Azure:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Omezení možnosti uživatelů spouštět skripty v rámci výpočetních prostředků

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fyzicky nebo logicky oddělit vysoce rizikové aplikace

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro webové aplikace spuštěné ve službě Azure App Service nebo výpočetních prostředcích.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace naleznete v [tématu Zabezpečení: Zabezpečená konfigurace](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny:** Definujte a implementujte standardní konfigurace zabezpečení pro nasazení centra událostí Azure. Pomocí aliasů zásad Azure v oboru názvů Microsoft.EventHub vytvořte vlastní zásady pro auditování nebo vynucení konfigurací. Můžete také využít předdefinované definice zásad pro Azure Event Hubs, jako jsou:

- Diagnostické protokoly v centru událostí by měly být povoleny

- Centrum událostí by mělo používat koncový bod služby virtuální sítě.

Integrované zásady Azure pro obor názvů Event Hubs:https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub

Jak zobrazit dostupné aliasy zásad Azure:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Vytvoření bezpečných konfigurací operačního systému

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Udržování zabezpečených konfigurací prostředků Azure

**Pokyny:** Pomocí zásad Azure [odepřít] a [nasadit, pokud neexistují] vynutit zabezpečené nastavení v rámci prostředků s povolenými event huby. 

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

 
Další informace o efektech zásad Azure:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Udržovat bezpečné konfigurace operačního systému

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Konfigurace prostředků Azure bezpečně ukládá

**Pokyny:** Pokud používáte vlastní definice zásad Azure pro vaše centra událostí nebo související prostředky, použijte Azure Repos k bezpečnému ukládání a správě kódu.

Jak ukládat kód v Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentace k azure repos:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Bezpečné ukládání vlastních bitových kopií operačního systému

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Nasazení nástrojů pro správu konfigurace systému

**Pokyny**: Pomocí aliasů zásad Azure v oboru názvů "Microsoft.EventHub" můžete vytvořit vlastní zásady pro výstrahu, audit ování a vynucení konfigurací systému. Kromě toho vytvořte proces a kanál pro správu výjimek zásad.

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Nasazení nástrojů pro správu konfigurace systému pro operační systémy

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementace automatického monitorování konfigurace pro služby Azure

**Pokyny**: Pomocí aliasů zásad Azure v oboru názvů "Microsoft.EventHub" můžete vytvořit vlastní zásady pro výstrahu, audit ování a vynucení konfigurací systému. Pomocí zásad Azure [audit] [odepřít] a [nasadit, pokud neexistují] automaticky vynutit konfigurace pro nasazení Azure Event Hubs a související prostředky.

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementace automatizovaného monitorování konfigurace operačních systémů

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpečná správa tajných kódů Azure

**Pokyny:** Pro virtuální počítače Azure nebo webové aplikace spuštěné ve službě Azure App Service, které se používají pro přístup k vašim centrům událostí, použijte identitu spravované služby ve spojení s Azure Key Vault ke zjednodušení a zabezpečení správy sdílených přístupových podpisů pro nasazení center událostí Azure. Ujistěte se, že je povoleno obnovitelné odstranění trezoru klíčů.

Ověření spravované identity pomocí Služby Azure Active Directory pro přístup k prostředkům Centra událostí:https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest

Konfigurace klíčů spravovaných zákazníkem pro centra událostí:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Jak integrovat s Azure Spravované identity:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Jak vytvořit trezor klíčů:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Jak zajistit ověřování trezoru klíčů se spravovanou identitou:https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpečná a automatická správa identit

**Pokyny:** Pro virtuální počítače Azure nebo webové aplikace spuštěné ve službě Azure App Service, které se používají pro přístup k vašim centrům událostí, použijte identitu spravované služby ve spojení s Azure Key Vault ke zjednodušení a zabezpečení Center událostí Azure. Ujistěte se, že je povoleno obnovitelné odstranění trezoru klíčů.

Pomocí spravovaných identit můžete poskytovat služby Azure s automaticky spravovanou identitou ve službě Azure Active Directory (AD). Spravované identity umožňuje ověření na libovolnou službu, která podporuje ověřování Azure AD, včetně Azure Key Vault, bez pověření ve vašem kódu.

Ověření spravované identity pomocí Služby Azure Active Directory pro přístup k prostředkům centra událostí:https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest 

Konfigurace klíčů spravovaných zákazníkem pro centra událostí:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Jak nakonfigurovat spravované identity:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Jak integrovat s Azure Spravované identity:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminujte nezamýšlenou expozici pověření

**Pokyny**: Implementujte skener pověření k identifikaci přihlašovacích údajů v rámci kódu. Skener přihlašovacích údajů také podpoří přesunutí zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je azure key vault.

Jak nastavit skener pověření:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace naleznete v [tématu Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Používejte centrálně spravovaný antimalwarový software

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

Microsoft anti-malware je povolena na podkladovém hostiteli, který podporuje služby Azure (například Azure App Service), ale neběží na obsah zákazníka.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Předprohledávací soubory, které se mají nahrát do nevýpočetních prostředků Azure

**Pokyny:** Před skenování veškerý obsah, který se nahrává do nevýpočetních prostředků Azure, jako jsou Azure Event Hubs, App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL atd. Společnost Microsoft nemá v těchto případech přístup k vašim datům.

Microsoft anti-malware je povolena na podkladovém hostiteli, který podporuje služby Azure (například Azure Cache pro Redis), ale neběží na obsah zákazníka.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Zajistěte aktualizaci antimalwarového softwaru a podpisů

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

## <a name="data-recovery"></a>Obnovení dat

*Další informace naleznete [v tématu Security Control: Data Recovery](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zajistěte pravidelné automatické zálohování

**Pokyny:** Konfigurace geografické zotavení po havárii pro Azure Event Hubs. Když se v ypracují prostoje celé oblasti Azure nebo datových center (pokud se nepoužívají žádné zóny dostupnosti), je důležité, aby zpracování dat dál fungovalo v jiné oblasti nebo datovém centru. Jako takové geografické zotavení po havárii a geografická replikace jsou důležité funkce pro všechny podniky. Azure Event Hubs podporuje geografické zotavení po havárii i geografickou replikaci na úrovni oboru názvů. 

Principy geograficky zotavení po havárii pro Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Proveďte kompletní zálohy systému a zálohujte všechny klienty spravované klíče

**Pokyny:** Azure Event Hubs poskytuje šifrování dat v klidovém stavu pomocí šifrování služby Azure Storage Service Encryption (Azure SSE). Event Hubs spoléhá na Azure Storage pro ukládání dat a ve výchozím nastavení, všechna data, která jsou uložená s Azure Storage je šifrována pomocí klíčů spravovaných Microsoftem. Pokud používáte Azure Key Vault pro ukládání klíčů spravovaných zákazníkem, zajistěte pravidelné automatické zálohování klíčů.

Zajistěte pravidelné automatické zálohování tajných kódů trezoru klíčů pomocí následujícího příkazu prostředí PowerShell: Backup-AzKeyVaultSecret

Jak nakonfigurovat klíče spravované zákazníkem pro šifrování dat služby Azure Event Hubs v klidovém stavu:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Jak zálohovat tajné kódy trezoru klíčů:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Ověření všech záloh včetně klíčů spravovaných zákazníkem

**Pokyny:** Testování obnovení zálohovaných klíčů spravovaných zákazníkem.

 

Jak obnovit klíče trezoru klíčů v Azure:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zajištění ochrany záloh a klíčů spravovaných zákazníkem

**Pokyny:** Povolte obnovitelné odstranění v trezoru klíčů, abyste ochránili klíče před náhodným nebo škodlivým odstraněním. Azure Event Hubs vyžaduje, aby byly nakonfigurovány klíče spravované zákazníky, aby byly nakonfigurovány obnovitelné odstranění a nevyčistit.

Nakonfigurujte obnovitelné odstranění pro účet Azure Storage, který se používá pro sběr dat centra událostí. Všimněte si, že tato funkce ještě není podporovaná pro Azure Data Lake Storage Gen 2.

Jak povolit obnovitelné odstranění v trezoru klíčů:https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

Nastavení trezoru klíčů s klíči:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Obnovitelné odstranění pro objekty BLOB úložiště Azure:https://docs.microsoft.com//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace naleznete [v tématu Security Control: Incident Response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Vytvoření průvodce odezvou na incidenty

**Pokyny**: Ujistěte se, že existují písemné plány reakce na incidenty, které definují role personálu, jakož i fáze zpracování incidentů / správy.

Jak nakonfigurovat automatizaci pracovních postupů v Centru zabezpečení Azure:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Vytvoření postupu hodnocení incidentů a stanovení priorit

**Pokyny**: Centrum zabezpečení přiřazuje výstrahám závažnost, která vám pomůže určit pořadí, ve kterém se účastníte jednotlivých výstrah, takže když dojde k ohrožení zabezpečení prostředku, můžete se k němu hned dostat. Závažnost je založena na tom, jak sebevědomý Security Center je v hledání nebo analytice slouží k vydání výstrahy, stejně jako úroveň spolehlivosti, že tam byl škodlivý záměr za aktivitu, která vedla k upozornění.

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="103-test-security-response-procedures"></a>10.3: Zkušební postupy odezvy zabezpečení

**Pokyny**: Proveďte cvičení k testování schopností reakce na incidenty vašich systémů na pravidelné kadenci. Identifikujte slabá místa a mezery a podle potřeby plán revidujte.

Podívejte se na publikaci NIST: Průvodce testovacími, školicími a cvičebními programy pro plány a schopnosti IT:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Poskytněte kontaktní údaje bezpečnostních incidentů a nakonfigurujte upozornění na bezpečnostní incidenty

**Pokyny**: Kontaktní informace o bezpečnostních incidentech společnost Microsoft použije k tomu, aby vás kontaktovala, pokud centrum MSRC (Microsoft Security Response Center) zjistí, že k datům zákazníka byla přístupná nezákonná nebo neoprávněná strana.  Zkontrolujte incidenty po faktu, abyste zajistili, že budou problémy vyřešeny. 

Jak nastavit kontakt zabezpečení Centra zabezpečení Azure:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Začleňte výstrahy zabezpečení do systému reakce na incidenty

**Pokyny:** Exportujte výstrahy a doporučení Centra zabezpečení Azure pomocí funkce Nepřetržitý export. Nepřetržitý export umožňuje exportovat výstrahy a doporučení buď ručně, nebo průběžně. Datový konektor Azure Security Center můžete použít k streamování výstrah Sentinel.

Jak nakonfigurovat nepřetržitý export:https://docs.microsoft.com/azure/security-center/continuous-export

Jak streamovat výstrahy do Azure Sentinelu:https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizace reakce na výstrahy zabezpečení

**Pokyny:** Pomocí funkce Automatizace pracovních postupů v Centru zabezpečení Azure můžete automaticky aktivovat odpovědi prostřednictvím "Logic Apps" na výstrahy zabezpečení a doporučení.

Konfigurace automatizace pracovních postupů a aplikací logiky:https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace naleznete [v tématu Bezpečnostní kontrola: Penetrační testy a cvičení červeného týmu](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Proveďte pravidelné penetrační testování prostředků Azure a zajistěte nápravu všech kritických zjištění zabezpečení do 60 dnů

**Pokyny**: Postupujte podle pravidel společnosti Microsoft a ujistěte se, https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1že penetrační testy neporušují zásady společnosti Microsoft: .
Další informace o strategii společnosti Microsoft a provádění červených týmů a testování pronikání živých webů proti cloudové infrastruktuře, službám a aplikacím společnosti Microsoft naleznete zde:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

## <a name="next-steps"></a>Další kroky

- Podívejte se na [srovnávací test zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Další informace o [plánech zásad zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
