---
title: Základní hodnoty zabezpečení Azure pro přední dveře Azure
description: Základní hodnoty zabezpečení služby Azure front-Security poskytují pokyny a zdroje pro implementaci doporučení zabezpečení, která jsou určená v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: frontdoor
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6d6a392d25aa96ab9b4dbb7763b37c1021db71aa
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026280"
---
# <a name="azure-security-baseline-for-azure-front-door"></a>Základní hodnoty zabezpečení Azure pro přední dveře Azure

Tato základní hodnota zabezpečení se vztahuje na doprovodné materiály k [Azure Security test 2,0](../security/benchmarks/overview.md) na Azure front-dveří. Srovnávací test zabezpečení Azure poskytuje doporučení, jak můžete zabezpečit cloudová řešení v Azure. Obsah se seskupuje podle **ovládacích prvků zabezpečení** , které definuje srovnávací test zabezpečení Azure, a souvisejících pomocných mechanismů, které se vztahují na přední dveře Azure. **Ovládací prvky** , které se nevztahují na přední dveře Azure, se vyloučily.

Pokud chcete zjistit, jak se přední dveře Azure kompletně mapují k srovnávacímu testu zabezpečení Azure, přečtěte si [kompletní soubor mapování standardních hodnot zabezpečení služby Azure front-dveří](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: zabezpečení sítě](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: Ochrana aplikací a služeb před útoky z externích sítí

**Pokyny**: pomocí Azure PowerShell vytvořte zásadu geografického filtrování a přidružte ji k vašemu stávajícímu hostiteli front-endu Azure. Tato zásada geografického filtrování zablokuje žádosti z externích sítí, jako jsou ty z jiných zemí nebo oblastí s výjimkou USA.

- [Kurz – jak nastavit geograficky filtrovací WAF zásady pro vaše přední dveře](front-door-tutorial-geo-filtering.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: zjednodušení pravidel zabezpečení sítě

**Doprovodné** materiály: použití značek Virtual Network služby k definování řízení přístupu k síti u skupin zabezpečení sítě nakonfigurovaných pro vaše přední dveře Azure pro vaše nabídky. Značky služeb lze použít místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (AzureFrontDoor. front; AzureFrontDoor. back-end, AzureFrontDoor. FirstParty) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo zamítnout provoz pro odpovídající službu. 

Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

- [Pochopení a použití značek služeb](../virtual-network/service-tags-overview.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="identity-management"></a>Správa identit

*Další informace najdete v tématu [testování výkonnosti Azure Security: Správa identit](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM – 6: omezit přístup k prostředkům Azure na základě podmínek

**Doprovodné** materiály: ve výchozím nastavení odpoví přední dveře Azure na všechny požadavky uživatelů bez ohledu na to, odkud požadavek pochází. Zákazníci mohou také omezit přístup ke své webové aplikaci podle zemí nebo oblastí. Služba Firewall webových aplikací v Azure front-dveří umožňuje zákazníkům definovat zásady pomocí pravidel vlastního přístupu pro konkrétní cestu ke svému koncovému bodu, aby povolily nebo blokovaly přístup ze zadaných zemí nebo oblastí.

- [Kurz – jak nastavit geograficky filtrovací WAF zásady pro vaše přední dveře Azure](front-door-tutorial-geo-filtering.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="privileged-access"></a>Privilegovaný přístup

*Další informace najdete v tématu [Azure Security benchmark: Privileged Access](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: omezení přístupu pro správu na systémy kritické pro podnikání

**Pokyny**: přední vrátka Azure používá řízení přístupu na základě role Azure (Azure RBAC) k izolaci přístupu k důležitým podnikovým systémům. Použijte službu Azure RBAC k omezení účtů, kterým je udělen privilegovaný přístup k předplatným a skupinám pro správu, kde se nacházejí.

Zajistěte omezený přístup ke správě, identitám a systémům zabezpečení, které mají přístup pro správu k důležitým podnikovým systémům, jako jsou Doména služby Active Directory řadiče, nástroje zabezpečení a nástroje pro správu systému. Zarovnává všechny typy přístupových prvků k strategii segmentace vaší organizace pro průběžné a konzistentní implementace.

- [Komponenty a referenční model Azure](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Přístup ke skupině pro správu](../governance/management-groups/overview.md#management-group-access) 

- [Správci předplatného Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitorování Azure Security Center**: Ano

**Odpovědnost**: sdílená

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: použití pracovních stanic s privilegovaným přístupem

**Doprovodné** materiály: zabezpečené, izolované pracovní stanice jsou kriticky důležité pro zabezpečení citlivých rolí, jako jsou správci, vývojáři a kritické operátory služby. 

Používejte vysoce zabezpečené pracovní stanice uživatelů s Azure bastionu pro úlohy správy. Vyberte Azure Active Directory (Azure AD), Microsoft Defender Advanced Threat Protection (ATP) a Microsoft Intune k nasazení zabezpečených a spravovaných pracovních stanic uživatelů pro úlohy správy. Zabezpečené pracovní stanice musí být centrálně spravované, aby vynutila zabezpečenou konfiguraci, včetně silného ověřování, standardních hodnot softwaru a hardwaru, omezeného logického a síťového přístupu.

- [Pochopení pracovních stanic s privilegovaným přístupem](../active-directory/devices/concept-azure-managed-workstation.md) 

- [Nasazení privilegované pracovní stanice přístupu](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: sledování pouze dostatečné správy (princip nejnižší úrovně oprávnění) 

**Doprovodné** materiály: přední dvířka Azure jsou integrovaná s řízením přístupu na základě role Azure (Azure RBAC) za účelem správy prostředků. Azure RBAC umožňuje spravovat přístup k prostředkům Azure prostřednictvím přiřazení rolí. Tyto role můžete přiřadit uživatelům, skupinám instančních objektů a spravovaným identitám. Pro určité prostředky jsou předem definované předdefinované role a tyto role můžou být v inventáři nebo prostřednictvím nástrojů, jako je Azure CLI, Azure PowerShell nebo Azure Portal. 

Použijte model nejnižší oprávnění pro oprávnění na základě rolí přiřazených k prostředkům pomocí Azure RBAC a zajistěte, aby byly založené na potřebě firmy. Tím se doplňují přístup JIT (just in time) Azure AD Privileged Identity Management (PIM) a měl by se pravidelně kontrolovat.

Pomocí integrovaných rolí můžete přidělit oprávnění a vytvářet pouze vlastní role založené na podnikových požadavcích.

- [Co je řízení přístupu na základě role v Azure (Azure RBAC)](../role-based-access-control/overview.md) 

- [Jak nakonfigurovat RBAC v Azure](../role-based-access-control/role-assignments-portal.md) 

- [Jak používat kontroly identity a přístupu v Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitorování Azure Security Center**: Ano

**Odpovědnost**: sdílená

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [srovnávací testy zabezpečení Azure: Ochrana dat](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: šifrování citlivých informací při přenosu

**Doprovodné** materiály: pro doplnění řízení přístupu by se data v přenosu měla chránit před útoky typu "vzdálené správy" (např. zachycení provozu) pomocí šifrování, aby útočníci mohli snadno číst nebo upravovat data.

Přední dvířka podporují protokol TLS verze 1,0, 1,1 a 1,2. TLS 1,3 není zatím podporován. Všechny profily front-dveří vytvořené po září 2019 jako výchozí minimum používají TLS 1,2.

I když je to pro přenosy v privátních sítích volitelné, je to pro provoz v externích i veřejných sítích velmi důležité. U přenosů HTTP zajistěte, aby všichni klienti připojující se k prostředkům Azure mohli vyjednávat TLS v 1.2 nebo vyšší verzi. Pro vzdálenou správu použijte místo nešifrovaného protokolu SSH (pro Linux) nebo RDP/TLS (pro Windows). Zastaralé verze a protokoly SSL, TLS a SSH a slabé šifry by měly být zakázané.

Ve výchozím nastavení poskytuje Azure šifrování dat při přenosu mezi datovými centry Azure.

- [Kurz – jak nakonfigurovat HTTPS na vlastní doméně front-dveří](front-door-custom-domain-https.md)

- [Pochopení šifrování při přenosu pomocí Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

- [Informace o zabezpečení TLS](/security/engineering/solving-tls1-problem) 

- [Dvojité šifrování pro Azure data při přenosu](../security/fundamentals/double-encryption.md#data-in-transit)

**Monitorování Azure Security Center**: Ano

**Odpovědnost**: sdílená

## <a name="asset-management"></a>Správa aktiv

*Další informace najdete v článku [Srovnávací test zabezpečení Azure: Správa prostředků](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>DOP. 1: Zajistěte, aby měl tým zabezpečení přehled o rizicích pro prostředky

**Doprovodné** materiály: Zajistěte, aby se týmům zabezpečení udělila oprávnění čtenářů zabezpečení v tenantovi Azure a předplatných, aby mohli monitorovat rizika zabezpečení pomocí Azure Security Center. 

V závislosti na tom, jak jsou strukturované zodpovědnosti týmu zabezpečení, může být monitorování rizik zabezpečení zodpovědností centrálního týmu zabezpečení nebo místního týmu. Přehledy a rizika zabezpečení se ale musí vždycky agregovat centrálně v rámci organizace. 

Oprávnění čtenář zabezpečení je možné využít široce pro celého tenanta (kořenovou skupinu pro správu) nebo vymezenou pro skupiny pro správu nebo konkrétní předplatná. 

Poznámka: Tato další oprávnění se můžou vyžadovat pro přehlednost v úlohách a službách. 

- [Přehled role čtecího modulu zabezpečení](../role-based-access-control/built-in-roles.md#security-reader)

- [Přehled služby Azure Skupiny pro správu](../governance/management-groups/overview.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>DOP. 2: Ujistěte se, že má tým zabezpečení přístup k inventáři prostředků a metadatům.

**Doprovodné** materiály: použití značek pro prostředky Azure, skupiny prostředků a předplatná k jejich logickému uspořádání do taxonomie. Každá značka se skládá z názvu a páru hodnot. Můžete například použít název "prostředí" a hodnotu "produkce" na všechny prostředky v produkčním prostředí.

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource graphu](../governance/resource-graph/first-query-portal.md) 

- [Správa inventáře Azure Security Center prostředků](../security-center/asset-inventory.md) 

- [Průvodce rozhodováním ohledně pojmenování a označování prostředků](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: pomocí Azure Policy můžete auditovat a omezovat, které služby můžou uživatelé zřídit ve vašem prostředí. Dotazování a zjišťování prostředků v rámci předplatných pomocí Azure Resource graphu

Pomocí Azure Monitor můžete vytvořit pravidla pro aktivaci výstrah při zjištění neschválené služby.

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>DOP. 4: zajištění zabezpečení správy životního cyklu prostředků

**Pokyny**: je zodpovědností zákazníka udržovat atributy a síťové konfigurace prostředků front-dveří pro Azure, které jsou považovány za vysoce ovlivněné.

Doporučuje se, aby zákazník vytvořil proces pro zachycení atributů a změny konfigurace sítě, jak je to možné, a to měření vlivu na změny a vytváření úloh.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="logging-and-threat-detection"></a>Protokolování a detekce hrozeb

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a detekce hrozeb](/azure/security/benchmarks/security-controls-v2-logging-threat-detection).*

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: povolení protokolování pro síťové aktivity Azure

**Doprovodné** materiály: Síťová dvířka Azure nejsou určená k nasazení do virtuálních sítí. vzhledem k tomu, že tento zákazník nemůže povolit protokolování toku skupin zabezpečení sítě, směrovat provoz přes bránu firewall nebo provádět zachycení paketů.

Přední dvířka Azure zapisuje všechny síťové přenosy, které IT zpracovává pro přístup zákazníků. Povolte funkci protokoly toku sítě a nakonfigurujte tyto protokoly, aby se odesílaly do účtu úložiště pro dlouhodobé uchovávání a auditování.

- [Kurz – jak nastavit sledování metrik a protokolů v frontách Azure](front-door-diagnostics.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Odpovědnost**: sdílená

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: povolení protokolování pro prostředky Azure

**Doprovodné** materiály: protokoly aktivit, které jsou automaticky k dispozici, obsahují všechny operace zápisu (Put, post, DELETE) pro prostředky front-dveří Azure s výjimkou operací čtení (Get). Protokoly aktivit se dají použít k nalezení chyby při odstraňování potíží nebo sledování, jak uživatel ve vaší organizaci změnil prostředek.

Povolte protokoly prostředků Azure pro přední dveře Azure. Pomocí Azure Security Center a Azure Policy můžete povolit protokoly prostředků a shromažďovat data protokolů. Tyto protokoly můžou být klíčové pro pozdější zkoumání incidentů zabezpečení a provádění forenzních cvičení.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Principy protokolování a různých typů protokolů v Azure](../azure-monitor/platform/platform-logs-overview.md) 

- [Pochopení Azure Security Center shromažďování dat](../security-center/security-center-enable-data-collection.md)

**Monitorování Azure Security Center**: Ano

**Odpovědnost**: sdílená

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v odpovědi na [incidenty Azure Security test:](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Příprava – aktualizace procesu reakce na incidenty pro Azure

**Doprovodné** materiály: Ujistěte se, že vaše organizace má definované procesy pro reakci na incidenty zabezpečení. Potvrďte, že tyto procesy jsou aktualizované pro prostředky Azure a pravidelně je testuje, aby se zajistila připravenost.

- [Implementace zabezpečení v podnikovém prostředí](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Referenční příručka k reakci na incidenty](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Příprava – oznámení o nastavení incidentu

**Doprovodné** materiály: nastavte kontaktní informace incidentu zabezpečení v Azure Security Center. Tyto kontaktní informace používá společnost Microsoft k tomu, aby vás kontaktovala v případě, že služba MSRC (Microsoft Security Response Center) zjistí, že vaše data jsou přístupná ze strany, která není protiprávní nebo oprávněná. Máte také možnost přizpůsobit upozornění na incidenty a oznámení v různých službách Azure v závislosti na potřebách reakce na incidenty. 

- [Jak nastavit kontakt zabezpečení Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: detekce a analýza – vytvoření incidentů na základě výstrah s vysokou kvalitou

**Doprovodné** materiály: Ujistěte se, že máte proces pro vytváření vysoce kvalitních výstrah a měření kvality výstrah. To vám umožní naučit se lekce z minulých incidentů a upřednostnit výstrahy pro analytiky, takže nemusejí ztrácet čas u falešně pozitivních výsledků. 

Výstrahy s vysokou kvalitou se dají vytvářet na základě zkušeností z minulých incidentů, ověřených zdrojů komunity a nástrojů určených k vygenerování a vyčištění výstrah odmítání a korelace různých zdrojů signálu. 

Azure Security Center poskytuje výstrahy vysoké kvality napříč mnoha prostředky Azure. K streamování výstrah do Azure Sentinel můžete použít datový konektor ASC. Azure Sentinel umožňuje vytvářet Pokročilá pravidla výstrah pro automatické generování incidentů pro účely šetření. 

Vyexportujte výstrahy a doporučení Azure Security Center pomocí funkce exportu, která vám usnadní identifikaci rizik pro prostředky Azure. Vyexportujte výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem.

- [Postup konfigurace exportu](../security-center/continuous-export.md)

- [Jak streamovat výstrahy do Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: detekce a analýza – prozkoumání incidentu

**Doprovodné** materiály: Zajistěte, aby Analytiki mohli zadávat dotazy a používat různé zdroje dat, když procházejí potenciální incidenty, aby mohli sestavit úplný přehled o tom, k čemu K tomu, aby bylo možné sledovat aktivity potenciálního útočníka v rámci dezaktivačního řetězu, by měly být shromažďovány různé typy protokolů.  Ujistěte se, že jsou přehledy a poznatky zachyceny pro ostatní analytiky a budoucí historické reference.  

Zdroje dat pro účely šetření zahrnují centralizované zdroje protokolování, které jsou již shromažďovány ze služeb v oboru a spuštěných systémů, ale mohou také zahrnovat:

- Síťová data – pomocí protokolů toků služby skupiny zabezpečení sítě, Azure Network Watcher a Azure Monitor zaznamenává protokoly toku sítě a další informace o analýze. 

- Snímky spuštěných systémů: 

    - Použijte schopnost snímku virtuálního počítače Azure a vytvořte snímek disku běžícího systému. 

    - Pomocí funkce nativního výpisu paměti operačního systému vytvořte snímek paměti běžícího systému.

    - Pomocí funkce snímků služeb Azure nebo vlastní schopnosti svého softwaru můžete vytvářet snímky spuštěných systémů.

Azure Sentinel poskytuje rozsáhlou analýzu dat napříč všemi zdroji protokolů a portálem pro správu případů, který umožňuje spravovat úplný životní cyklus incidentů. Informace o dataintelligence během šetření můžou být přidruženy k incidentu pro účely sledování a vytváření sestav. 

- [Snímek disku s počítačem s Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Vytvoření snímku disku počítače se systémem Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure podporu pro diagnostické informace a shromažďování výpisu paměti](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Prozkoumat incidenty pomocí služby Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: detekce a analýza – určení priorit incidentů

**Doprovodné** materiály: Poskytněte kontext analytikům, na kterých incidenty zaměřte na první, na základě závažnosti výstrahy a citlivosti prostředků. 

Azure Security Center přiřadí každému upozornění závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u které došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Navíc můžete označovat prostředky pomocí značek a vytvořit systém pojmenování k identifikaci a kategorizaci prostředků Azure, zejména těch, které zpracovávají citlivá data.  Máte zodpovědnost za to, že je možné určit prioritu nápravy výstrah na základě závažnosti prostředků a prostředí Azure, ve kterých došlo k incidentu.

- [Výstrahy zabezpečení ve službě Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Používání značek k uspořádání prostředků Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: zahrnutí, vyhubení a obnovení – automatizace zpracování incidentů

**Doprovodné** materiály: automatizace ručních opakujících se úloh, které urychlují dobu odezvy a snižují zátěž analytiků. Ruční provádění úloh trvá déle, což zpomaluje jednotlivé incidenty a snižuje počet incidentů, které může analytik zvládnout. Ruční úlohy také zvyšují únavu analytiků, což zvyšuje riziko lidské chyby, která způsobuje zpoždění, a snižuje schopnost analytiků efektivně se zaměřit na složité úlohy. Pomocí funkcí automatizace pracovních postupů v Azure Security Center a Azure Sentinel můžete automaticky aktivovat akce nebo spustit PlayBook a reagovat na příchozí výstrahy zabezpečení. PlayBook provádí akce, jako je odesílání oznámení, zakázání účtů a izolace problematických sítí. 

- [Konfigurace automatizace pracovního postupu v Security Center](../security-center/workflow-automation.md)

- [Nastavení automatických odpovědí na hrozby v Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Nastavení automatických odpovědí na hrozby v Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="posture-and-vulnerability-management"></a>Stav a Správa ohrožení zabezpečení

*Další informace najdete v článku o [stav a správě ohrožení zabezpečení Azure Security test](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management).*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>Souč_hod-3: Vytvoření zabezpečených konfigurací pro výpočetní prostředky

**Pokyny**: pomocí Azure Security Center a Azure Policy navažte zabezpečené konfigurace na všech výpočetních prostředcích, včetně Virtual Machines, kontejnerů a dalších.

- [Jak monitorovat Azure Security Center doporučení](../security-center/security-center-recommendations.md) 

- [Doporučení k zabezpečení – Referenční příručka](../security-center/recommendations-reference.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Odpovědnost**: sdílená

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: rychlé a automatické napravení ohrožení zabezpečení softwaru

**Doprovodné** materiály: rychlé nasazení aktualizací softwaru k nápravě ohrožení zabezpečení softwaru v operačních systémech a aplikacích.

Určete prioritu používání programu pro společné hodnocení rizik (například běžný systém pro vyhodnocování ohrožení zabezpečení) nebo výchozí hodnocení rizika poskytované skenovacím nástrojem jiného výrobce, který používáte. a přizpůsobte si prostředí pomocí kontextu, které aplikace prezentují vysoké riziko zabezpečení a které vyžadují vysokou dobu provozu.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: provedení pravidelné simulace útoků

**Doprovodné** materiály: jak je potřeba, proveďte testování průniku nebo červené týmové aktivity na svých prostředcích Azure a zajistěte nápravu všech kritických zjištění zabezpečení.
Postupujte podle pravidel pro testování průniku Microsoft Cloud a zajistěte, aby testy průniku nenarušily zásady Microsoftu. Využijte strategii a provádění testování na základě červeného týmového seskupování a živého průniku na cloudové infrastruktuře, služby a aplikace spravované společností Microsoft.

- [Testování průniku v Azure](../security/fundamentals/pen-testing.md)

- [Pravidla testování průniku pro zapojení](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

## <a name="governance-and-strategy"></a>Zásady správného řízení a strategie

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: zásady správného řízení a strategie](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: definování správy prostředků a strategie ochrany dat 

**Doprovodné** materiály: Ujistěte se, že máte dokument a sdělíte jasné strategii pro nepřetržité monitorování a ochranu systémů a dat. Určení priorit zjišťování, hodnocení, ochrany a monitorování důležitých podnikových dat a systémů. 

Tato strategie by měla zahrnovat zdokumentované doprovodné materiály, zásady a standardy pro následující prvky: 

-   Standardní klasifikace dat v souladu s obchodními riziky

-   Organizace zabezpečení – přehled o rizicích a inventáři prostředků 

-   Schválení organizace služby Azure pomocí organizace zabezpečení 

-   Zabezpečení prostředků prostřednictvím jejich životního cyklu

-   Požadovaná strategie řízení přístupu v souladu s klasifikací dat organizace

-   Používání funkcí Azure Native a ochrany dat třetích stran

-   Požadavky na šifrování dat pro případy použití v přenosech a v REST

-   Příslušné kryptografické standardy

Další informace jsou k dispozici na odkazovaných odkazech.

- [Doporučení architektury zabezpečení Azure – úložiště, data a šifrování](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Základy zabezpečení Azure – zabezpečení, šifrování a úložiště dat Azure](../security/fundamentals/encryption-overview.md)

- [Azure Security test – ochrana dat](/azure/security/benchmarks/security-controls-v2-data-protection)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: definování strategie podnikové segmentace 

**Doprovodné** materiály: zřízení strategie pro celé podniky za účelem segmentace přístupu k assetům pomocí kombinace identity, sítě, aplikace, předplatného, skupiny pro správu a dalších ovládacích prvků.

Pečlivě vyvážit nutnost oddělení zabezpečení s nutností povolit každodenní provoz systémů, které musí vzájemně komunikovat, a přistupovat k datům.

Zajistěte, aby se strategie segmentace implementovala konzistentně napříč typy ovládacích prvků včetně zabezpečení sítě, modelů identit a přístupu a oprávnění aplikace/modelů přístupu a ovládacích prvků pro lidské procesy.

- [Doprovodné materiály k strategii segmentace v Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Doprovodné materiály k strategii segmentace v Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Sjednotit segmentaci sítě pomocí strategie segmentace v podniku](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: definování strategie správy stav zabezpečení

**Doprovodné** materiály: nepřetržitě měřit a zmírnit rizika pro jednotlivé prostředky a prostředí, ve kterém jsou hostovány. Určete prioritu prostředků s vysokou hodnotou a vysoce vydaných ploch útoků, jako jsou publikované aplikace, příchozí a výstupní body sítě, koncových bodů uživatelů a správců atd.

- [Azure Security test – stav a Správa ohrožení zabezpečení](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: zarovnání rolí organizace, odpovědností a accountabilities

**Doprovodné** materiály: Ujistěte se, že máte dokument a komunikujete s jasnými strategiemi pro role a zodpovědnost ve vaší organizaci zabezpečení. Stanovte prioritu poskytování jasných zodpovědností při rozhodování o zabezpečení, informování všech uživatelů na sdílené zodpovědnosti a vzdělávání technických týmů na technologii pro zabezpečení cloudu.

- [Osvědčené postupy zabezpečení Azure 1 – lidé: vzdělávání týmů na cestě cloudového zabezpečení](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Osvědčené postupy zabezpečení Azure 2 – lidé: vzdělávání týmů na technologii Cloud Security](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Osvědčené postupy zabezpečení Azure 3 – proces: přiřazení zodpovědnosti pro rozhodování o zabezpečení cloudu](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="gs-5-define-network-security-strategy"></a>GS-5: definování strategie zabezpečení sítě

**Doprovodné** materiály: navažte přístup k zabezpečení sítě Azure v rámci celkové strategie řízení přístupu zabezpečení vaší organizace.  

Tato strategie by měla zahrnovat zdokumentované doprovodné materiály, zásady a standardy pro následující prvky: 

-   Centralizovaná správa sítě a zodpovědnost zabezpečení

-   Model segmentace virtuální sítě zarovnané na strategii podnikového segmentace

-   Strategie oprav v různých scénářích hrozeb a útoků

-   Strategie pro Internet Edge a příchozí a odchozí přenosy

-   Hybridní cloudová a místní vzájemné propojení strategie

-   Aktuální artefakty zabezpečení sítě (například síťové diagramy, referenční Síťová architektura)

Další informace jsou k dispozici na odkazovaných odkazech.

- [Osvědčené postupy zabezpečení Azure 11 – architektura. Jednotná strategie zabezpečení](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Testování zabezpečení Azure – zabezpečení sítě](/azure/security/benchmarks/security-controls-v2-network-security)

- [Přehled zabezpečení sítě Azure](../security/fundamentals/network-overview.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: definování strategie identity a privilegovaného přístupu

**Doprovodné** materiály: navažte přístup k identitě Azure a privilegovanému přístupu v rámci celkové strategie řízení přístupu zabezpečení vaší organizace.  

Tato strategie by měla zahrnovat zdokumentované doprovodné materiály, zásady a standardy pro následující prvky: 

-   Centralizovaný systém identit a ověřování a jeho vzájemné propojení s dalšími interními a externími systémy identit

-   Metody silného ověřování v různých případech a podmínkách použití

-   Ochrana vysoce privilegovaných uživatelů

-   Anomálie monitorování a zpracování aktivit uživatelů  

-   Uživatelská identita a kontrola přístupu a proces odsouhlasení

Další informace najdete v následujících odkazech:

- [Azure Security benchmark – Správa identit](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Azure Security test – privilegovaný přístup](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Osvědčené postupy zabezpečení Azure 11 – architektura. Jednotná strategie zabezpečení](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Přehled zabezpečení služby Azure Identity Management](../security/fundamentals/identity-management-overview.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: definování strategie protokolování a reakce na hrozby

**Doprovodné** materiály: vytvoření strategie protokolování a reakce na hrozby pro rychlé detekci a nápravu hrozeb při splnění požadavků na dodržování předpisů. Určete prioritu poskytování analytiků s vysoce kvalitními výstrahami a bezproblémové prostředí, aby se mohly soustředit na hrozby, nikoli na integraci a ruční kroky. 

Tato strategie by měla zahrnovat zdokumentované doprovodné materiály, zásady a standardy pro následující prvky: 

-   Role a odpovědnosti organizace v oblasti operací zabezpečení (SecOps) 

-   Dobře definovaný proces reakce na incidenty, který je v souladu s NIST nebo jiným oborovým rozhraním 

-   Shromažďování a uchovávání protokolů pro podporu detekce hrozeb, reakce na incidenty a požadavků na dodržování předpisů

-   Centralizované zobrazení a korelační informace o hrozbách, používání SIEM, nativních možností Azure a dalších zdrojů 

-   Komunikace a plán oznámení vašim zákazníkům, dodavatelům a veřejným stranám zájmu

-   Použití nativních platforem Azure a platforem třetích stran pro zpracování incidentů, jako je například protokolování a detekce hrozeb, forenzní a náprava či eradikace útoků

-   Procesy pro zpracování incidentů a aktivit po incidentech, jako jsou zjištěné lekce a uchovávání důkazů

Další informace jsou k dispozici na odkazovaných odkazech.

- [Azure Security test benchmark – protokolování a detekce hrozeb](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Srovnávací test zabezpečení Azure – odpověď na incident](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Doporučený postup zabezpečení Azure 4 – proces. Aktualizace procesů reakce na incidenty pro Cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Průvodce rozhodnutím o přijetí, protokolování a vytváření sestav pro Azure](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="next-steps"></a>Další kroky

- Další informace najdete v tématu [Přehled Azure Security benchmark v2](/azure/security/benchmarks/overview) .
- Další informace o [plánech zabezpečení Azure](/azure/security/benchmarks/security-baselines-overview)
