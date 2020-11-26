---
title: Základní hodnoty zabezpečení Azure pro Azure Advisor
description: Základní Azure Advisor zabezpečení poskytuje postupy a prostředky pro implementaci doporučení zabezpečení uvedených v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: advisor
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 24935f1b10ec11bb546cb08e06430d1e0b5c31fa
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183324"
---
# <a name="azure-security-baseline-for-azure-advisor"></a>Základní hodnoty zabezpečení Azure pro Azure Advisor

Tato základní hodnota zabezpečení platí pro Azure Advisor pokynů od [zabezpečení Azure Security test 2,0](../security/benchmarks/overview.md) . Srovnávací test zabezpečení Azure nabízí doporučení k zabezpečení cloudových řešení v Azure. Obsah je seskupen podle **řízení zabezpečení** definovaného srovnávacím testem zabezpečení Azure a souvisejících pokynů, které se vztahují na Azure Advisor. **Ovládací prvky** , které se nevztahují k Azure Advisor byly vyloučeny.

Pokud chcete zjistit, jak Azure Advisor kompletně mapuje srovnávací test zabezpečení Azure, přečtěte si [celý soubor mapování standardních hodnot Azure Advisor Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-management"></a>Správa identit

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: Správa identit](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardizace Azure Active Directory jako centrálního systému identit a ověřování

**Pokyny**: Azure Advisor jako výchozí službu pro správu identit a přístupu používá Azure Active Directory (Azure AD). Standardizace Azure AD pro řízení identity a správy přístupu vaší organizace v nástroji:

- Microsoft Cloud prostředky, jako jsou Azure Portal, Azure Storage, virtuální počítač Azure (Linux a Windows), Azure Key Vault, PaaS a SaaS aplikace
- Prostředky vaší organizace, jako jsou aplikace v Azure nebo podnikové síťové prostředky

Ujistěte se, že je služba Azure AD s vysokou prioritou v praxi cloudového zabezpečení vaší organizace. Azure AD taky poskytuje bezpečnostní skóre identity, které vám pomůžou posoudit stav zabezpečení identity relativně k osvědčeným postupům Microsoftu. S využitím tohoto skóre můžete změřit, nakolik vaše konfigurace odpovídá doporučeným osvědčeným postupům, a zlepšit stav zabezpečení.

Upozorňujeme, že Azure AD podporuje externí identity, které umožňují uživatelům bez účet Microsoft přihlašovat se k aplikacím a prostředkům s jejich externí identitou.

- [Tenantů v Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Vytvoření a konfigurace instance Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Použít externí zprostředkovatele identity pro aplikaci](../active-directory/external-identities/identity-providers.md) 

- [Jaké je bezpečné skóre identity v Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Používání jednotného přihlašování Azure AD pro přístup k aplikacím

**Pokyny**: Azure Advisor používá Azure Active Directory (Azure AD) k zajištění správy identit a přístupu k prostředkům Azure, cloudovým aplikacím a místním aplikacím. To zahrnuje podnikové identity, jako jsou zaměstnanci, i externí identity, jako jsou partneři, prodejci a dodavatelé. 

Pomocí jednotného přihlašování můžete spravovat a zabezpečit přístup k datům a prostředkům vaší organizace místně a v cloudu. Připojením všech uživatelů, aplikací a zařízení k Azure AD zajistíte bezproblémový a zabezpečený přístup a získáte větší přehled a kontrolu.

- [Principy jednotného přihlašování k aplikacím s využitím Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Používání kontrolních mechanismů silného ověřování pro veškerý přístup na základě Azure Active Directory

**Doprovodné** materiály: Azure Advisor používá Azure Active Directory (Azure AD), která podporuje ovládací prvky silného ověřování prostřednictvím vícefaktorového ověřování a metod se silnými hesly.
- Vícefaktorové ověřování – povolte vícefaktorové ověřování Azure AD a sledujte doporučení pro správu identit a přístupu Azure Security Center pro některé osvědčené postupy v nastavení vícefaktorového ověřování. Vícefaktorové ověřování se dá vyhovět všem, vybírat uživatele nebo na úrovni jednotlivých uživatelů na základě podmínek přihlášení a rizikových faktorů.
- Ověřování nepřipojeného hesla – k dispozici jsou tři možnosti ověřování s heslem. Jedná se o metody Windows Hello pro firmy, Microsoft Authenticator aplikace a místní ověřování, jako jsou čipové karty.

Zajistěte, aby se pro správce a privilegované uživatele používala nejvyšší úroveň konkrétní metody silného ověřování, a pro ostatní uživatele zaveďte odpovídající zásady silného ověřování.

- [Povolení vícefaktorového ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Úvod do možností ověřování bez hesla pro Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorování anomálií u účtů a upozorňování na ně

**Doprovodné** materiály: Azure Advisor je integrována s Azure Active Directory (Azure AD), v rámci které poskytuje následující zdroje dat:
- Přihlášení – sestava přihlášení poskytuje informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů.
- Protokoly auditu – Zajišťuje sledovatelnost prostřednictvím protokolů všech změn provedených různými funkcemi v rámci Azure AD. Mezi příklady protokolů auditu patří změny jakýchkoli prostředků v rámci Azure AD, jako jsou přidání nebo odebrání uživatelů, aplikací, skupin, rolí nebo zásad.
- Rizikové přihlášení – rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.
- Uživatelé označení příznakem rizika – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený.

Pomocí těchto zdrojů dat se můžete integrovat s Azure Monitor, Azure Sentinel nebo systémy SIEM třetích stran. Nastavte výstrahy na Azure Security Center pro určité podezřelé aktivity, jako je například nadměrný počet neúspěšných pokusů o ověření, zastaralých účtů v předplatném.

Rozšířená ochrana před internetovými útoky (ATP) v Azure je řešení zabezpečení, které dokáže na základě signálů Active Directory identifikovat, detekovat a vyšetřovat pokročilé hrozby, ohrožené identity a škodlivé činnosti vnitřních účastníků.

- [Sestavy aktivit auditu v Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Monitorování identit a aktivit přístupu uživatelů ve službě Azure Security Center](../security-center/security-center-identity-access.md) 

- [Upozornění v modulu ochrany na základě analýzy hrozeb služby Azure Security Center](../security-center/alerts-reference.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM – 6: omezit přístup k prostředkům Azure na základě podmínek

**Doprovodné** materiály: Azure Advisor podporuje funkci podmíněného přístupu Azure Active Directory (Azure AD) pro podrobnější řízení přístupu na základě uživatelem definovaných podmínek. Například přihlášení uživatelů z určitých rozsahů IP adres bude vyžadovat službu Multi-Factor Authentication pro přihlášení. Podrobné zásady správy relace ověřování se dají použít i pro různé případy použití.

- [Přehled podmíněného přístupu Azure](../active-directory/conditional-access/overview.md) 

- [Společné zásady podmíněného přístupu](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Konfigurace správy relace ověřování pomocí podmíněného přístupu](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

## <a name="privileged-access"></a>Privilegovaný přístup

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: Privilegovaný přístup](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: omezení přístupu pro správu na systémy kritické pro podnikání

**Pokyny**: Azure Advisor používá řízení přístupu na základě role Azure (Azure RBAC) k izolaci přístupu k důležitým podnikovým systémům tím, že omezí, kterým účtům se udělí privilegovaný přístup k předplatným a skupinám pro správu, ke kterým patří.

Omezte přístup k systémům pro správu, identitu a zabezpečení, které mají přístup pro správu k vašemu podnikovému přístupu, jako jsou Doména služby Active Directory Controllers (DCs), nástroje zabezpečení a nástroje pro správu systému s agenty nainstalovanými v důležitých podnikových systémech. Útočníci, kteří tyto systémy pro správu a zabezpečení ohrožují, si je můžou hned weaponize, aby vyzrazeni důležité obchodní prostředky.

Všechny typy ovládacích prvků přístupu by se měly zarovnávat na strategii segmentace vaší organizace, aby se zajistilo jednotné řízení přístupu.

- [Komponenty a referenční model Azure](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Přístup ke skupině pro správu](../governance/management-groups/overview.md#management-group-access)

- [Správci předplatného Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Pravidelné kontroly a sjednocování uživatelského přístupu

**Pokyny**: Azure Advisor používá ke správě svých prostředků účty Azure Active Directory (Azure AD), Projděte si účty uživatelských účtů a přiřazení přístupu pravidelně a ujistěte se, že jsou účty a jejich přístup platné. Implementací kontrol přístupu Azure AD zkontrolujte členství ve skupinách, přístup k podnikovým aplikacím a přiřazení rolí. Vytváření sestav Azure AD může poskytovat protokoly, které vám pomůžou zjistit zastaralé účty. 

Kromě toho můžete pomocí funkcí Privileged Identity Management služby Azure AD vytvořit pracovní postup sestavy kontroly přístupu a usnadnit tak proces kontroly. Privileged Identity Management lze také nakonfigurovat na upozornění při vytvoření nadměrného počtu účtů správců a pro identifikaci účtů správců, které jsou zastaralé nebo nesprávně nakonfigurované.

Všimněte si, že některé služby Azure podporují místní uživatele a role, které se nespravují přes Azure AD. Zákazníci budou muset spravovat tyto uživatele samostatně.

- [Vytvoření kontroly přístupu pro role prostředků Azure v Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Používání kontrol přístupu a identit Azure AD](/azure/active-directory/governance/access-reviews-overvie)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: použití pracovních stanic s privilegovaným přístupem

**Doprovodné** materiály: zabezpečené, izolované pracovní stanice jsou kriticky důležité pro zabezpečení citlivých rolí, jako jsou správci, vývojáři a kritické operátory služby. 

Pro úlohy správy Používejte vysoce zabezpečené uživatelské pracovní stanice nebo Azure bastionu. Vyberte Azure Active Directory (Azure AD), Microsoft Defender Advanced Threat Protection (ATP), včetně Microsoft Intune k nasazení zabezpečené a spravované pracovní stanice uživatelů pro úlohy správy. 

Centrálně spravujte zabezpečené pracovní stanice, abyste vynutili zabezpečenou konfiguraci, včetně silného ověřování, standardních hodnot softwaru a hardwaru, omezeného logického a síťového přístupu.

- [Pochopení pracovních stanic s privilegovaným přístupem](../active-directory/devices/concept-azure-managed-workstation.md) 
 
- [Nasazení privilegované pracovní stanice přístupu](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Využívání správy jen v potřebném rozsahu (princip nejnižší úrovně nutných oprávnění) 

**Pokyny**: Azure Advisor je integrován s řízením přístupu na základě role Azure (Azure RBAC) za účelem správy prostředků. Využijte Azure RBAC ke správě přístupu k prostředkům Azure prostřednictvím přiřazení rolí. 

Přiřazení rolí uživatelům, skupin instančních objektů a spravovaných identit. Pro určité prostředky jsou předem definované předdefinované role, které můžou být v inventáři nebo prostřednictvím nástrojů, jako je Azure CLI, Azure PowerShell nebo Azure Portal. Oprávnění přiřazená k prostředkům prostřednictvím Azure RBAC by se měla vždycky omezit na to, co role vyžadují. Tento přístup doplňuje přístup založený na přístupu podle potřeby (JIT) ve službě Azure AD Privileged Identity Management (PIM) a měl by se pravidelně kontrolovat.

Využijte k přidělování oprávnění předdefinované role a vlastní role vytvářejte pouze v případě potřeby.

[Co je řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md) 

- [Konfigurace řízení přístupu na základě role Azure](../role-based-access-control/role-assignments-portal.md) 

- [Používání kontrol přístupu a identit Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

## <a name="asset-management"></a>Správa aktiv

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: Správa prostředků](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Zajištění přehledu bezpečnostního týmu o rizicích pro prostředky

**Pokyny:** Zajistěte udělení oprávnění Čtenář zabezpečení ve vašem tenantovi Azure a vašich předplatných bezpečnostním týmům, aby mohly monitorovat bezpečnostní rizika s využitím služby Azure Security Center. 

V závislosti na nastavení struktury odpovědnosti bezpečnostních týmů může být monitorování bezpečnostních rizik odpovědností centrálního nebo místního bezpečnostního týmu. Přehledy zabezpečení a bezpečnostní rizika se však v rámci organizace musí vždy agregovat centrálně. 

Oprávnění Čtenář zabezpečení je možné přidělit pro celého tenanta (kořenová skupina pro správu) nebo je vymezit na konkrétní skupiny pro správu nebo konkrétní předplatná. 

Poznámka: K získání přehledu o úlohách a službách se můžou vyžadovat další oprávnění. 

- [Přehled role Čtenář zabezpečení](../role-based-access-control/built-in-roles.md#security-reader)

- [Přehled skupin pro správu Azure](../governance/management-groups/overview.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>DOP. 5: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: Azure Advisor používá pro identitu a ověřování Azure Active Directory (Azure AD), zatímco Azure Portal a Azure Resource Manager se používají ke správě služby Advisor. 

Podmíněný přístup Azure použijte k omezení schopnosti uživatele pracovat se správcem prostředků Azure tak, že v závislosti na podnikových požadavcích nakonfigurujete "blokování přístupu" pro aplikaci Microsoft Azure Management. 

- [Jak nakonfigurovat podmíněný přístup k blokování přístupu k Azure Resource Manageru](../role-based-access-control/conditional-access-azure-management.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

## <a name="logging-and-threat-detection"></a>Protokolování a detekce hrozeb

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: Protokolování a detekce hrozeb](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: povolení protokolování pro prostředky Azure

**Doprovodné** materiály: protokoly aktivit jsou automaticky dostupné a obsahují všechny operace zápisu (Put, post, DELETE) pro prostředky Azure Advisor s výjimkou operací čtení (Get). 

Protokoly aktivit se dají použít k nalezení chyby při odstraňování potíží nebo sledování, jak uživatel ve vaší organizaci změnil prostředek.

- [Principy protokolování a různých typů protokolů v Azure](../azure-monitor/platform/platform-logs-overview.md)

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: centralizace správy protokolu zabezpečení a analýzy

**Doprovodné** materiály: centralizace úložiště protokolování a analýzy pro povolení korelace. U každého zdroje protokolu se ujistěte, že jste přiřadili vlastníka dat, pokyny pro přístup, umístění úložiště, jaké nástroje se používají ke zpracování a přístupu k datům a k požadavkům na uchovávání dat.

Ujistěte se, že Integrujte protokoly aktivit Azure do svého centrálního protokolování. Ingestování protokolů prostřednictvím Azure Monitor k agregaci dat zabezpečení generovaných zařízeními koncového bodu, síťovými prostředky a dalšími systémy zabezpečení. V Azure Monitor použijte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty Azure Storage pro dlouhodobé a archivační úložiště.

Kromě toho povolte a zaveďte data do Azure Sentinel nebo SIEM třetí strany. Řada organizací zvolí použití Azure Sentinel pro "horká" data, která se používají často a Azure Storage pro "studená" data, která se používají méně často.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurace uchovávání úložiště protokolu

**Doprovodné** materiály: Zajistěte, aby všechny účty úložiště nebo pracovní prostory Log Analytics používané pro ukládání Azure Advisor protokolů měly dobu uchování protokolu nastavenou v souladu s předpisy pro dodržování předpisů vaší organizace.
V Azure Monitor můžete nastavit dobu uchování pracovního prostoru Log Analytics podle předpisů pro dodržování předpisů vaší organizace. Používejte Azure Storage, Data Lake nebo Log Analytics účty pracovního prostoru pro dlouhodobé a archivační úložiště.

- [Jak nakonfigurovat dobu uchování Log Analytics pracovního prostoru](../azure-monitor/platform/manage-cost-storage.md) 

- [Ukládání protokolů prostředků v Azure Storagem účtu](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: Reakce na incidenty](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Příprava – Aktualizace procesu reakce na incidenty pro Azure

**Pokyny:** Ujistěte se, že jsou ve vaší organizaci nastavené procesy pro reakci na incidenty zabezpečení, jsou aktualizované pro Azure a pravidelně se uplatňují, aby se zajistila připravenost na tyto incidenty.

- [Implementace zabezpečení v podnikovém prostředí](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Referenční příručka k reakci na incidenty](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Příprava – Nastavení oznámení o incidentech

**Pokyny:** Nastavte ve službě Azure Security Center kontaktní informace pro incidenty zabezpečení. Prostřednictvím těchto kontaktních informací vás bude Microsoft kontaktovat, pokud středisko Microsoft Security Response Center (MSRC) zjistí, že k vašim datům nezákonně nebo neoprávněně přistupovala třetí strana. Můžete také upravit upozornění na incidenty a oznámení o incidentech v různých službách Azure podle vašich požadavků na reakci na incidenty. 

- [Nastavení kontaktu ve službě Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Detekce a analýza – Vytváření incidentů na základě vysoce kvalitních upozornění

**Pokyny:** Ujistěte se, že máte nastavené procesy vytváření vysoce kvalitních upozornění a měření kvality upozornění. To vám umožní učit se z minulých incidentů a určovat prioritu upozornění pro analytiky, aby neztráceli čas falešně pozitivními výsledky. 

Vysoce kvalitní upozornění je možné vytvářet na základě zkušeností z minulých incidentů, ověřených komunitních zdrojů a nástrojů, které spojují a korelují signály z různorodých zdrojů a generují a mažou upozornění. 

Azure Security Center nabízí vysoce kvalitní upozornění pro celou řadu prostředků Azure. Pomocí datového konektoru ASC můžete upozornění streamovat do služby Azure Sentinel. Azure Sentinel umožňuje vytvářet rozšířená pravidla upozornění, která automaticky generují incidenty pro účely šetření. 

Pomocí funkce exportu můžete upozornění a doporučení služby Azure Security Center exportovat, abyste mohli lépe identifikovat rizika pro prostředky Azure. Upozornění a doporučení můžete exportovat ručně nebo průběžně a nepřetržitě.

- [Konfigurace exportu](../security-center/continuous-export.md)

- [Streamování upozornění do služby Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Detekce a analýza – Vyšetřování incidentu

**Pokyny:** Zajistěte, aby analytici mohli při vyšetřování potenciálních incidentů dotazovat a používat různorodé zdroje dat a mohli tak získat ucelenou představu o tom, co se stalo. Aby se zabránilo slepým místům, měly by se shromažďovat různorodé protokoly, které umožní sledovat aktivity potenciálního útočníka v celém průběhu útoku.  Měli byste také zajistit zaznamenávání přehledů a poznatků pro další analytiky a budoucí použití jako historické referenční informace.  

Mezi zdroje dat pro vyšetřování patří centrální zdroje protokolování, ve kterých se již shromažďují protokoly ze služeb v příslušném oboru a spuštěných systémů, ale můžou mezi ně patřit také:

- Síťová data – S využitím protokolů toků skupin zabezpečení sítě a služeb Azure Network Watcher a Azure Monitor můžete zachytávat protokoly toků sítě a další analytické informace. 

- Snímky spuštěných systémů: 

    - S využitím funkce vytváření snímků virtuálního počítače Azure můžete vytvořit snímek disku spuštěného systému. 

    - S využitím nativní funkce výpisu paměti operačního systému můžete vytvořit snímek paměti spuštěného systému.

    - S využitím funkce vytváření snímků služeb Azure nebo vlastních funkcí vašeho softwaru můžete vytvářet snímky spuštěných systémů.

Azure Sentinel nabízí rozsáhlé analýzy dat z prakticky jakéhokoli zdroje protokolů a portál pro správu případů, na kterém můžete spravovat celý životní cyklus incidentů. Analytické informace zjištěné během vyšetřování je možné přidružovat k incidentům pro účely sledování a generování sestav. 

- [Snímek disku počítače s Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Snímek disku počítače s Linuxem](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Shromažďování výpisu paměti a diagnostických informací ze strany podpory Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Vyšetřování incidentů s využitím služby Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Detekce a analýza – Určování priority incidentů

**Pokyny:** Poskytněte analytikům kontext, aby věděli, na jaké incidenty se zaměřit jako první v závislosti na závažnosti upozornění a citlivosti prostředků. 

Azure Security Center jednotlivým upozorněním přiřazuje závažnost, která vám pomůže určit, jaká upozornění by se měla vyšetřit jako první. Závažnost vychází z míry důvěry služby Security Center v závěr nebo analýzu, na základě kterých se upozornění vygenerovalo, a také z úrovně spolehlivosti, že za aktivitou, která vedla k upozornění, byl škodlivý záměr.

Kromě toho můžete prostředky označit pomocí značek a vytvořit systém vytváření názvů, který vám pomůže identifikovat a kategorizovat prostředky Azure, zejména prostředky zpracovávající citlivá data.  Je vaší zodpovědností určit prioritu nápravy upozornění v závislosti na důležitosti prostředků Azure a prostředí, ve kterém k incidentu došlo.

- [Výstrahy zabezpečení ve službě Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Používání značek k uspořádání prostředků Azure](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Izolace, odstranění a obnovení – Automatizace řešení incidentů

**Pokyny:** Zrychlete reakci a snižte zátěž analytiků automatizací opakovaných ručních úloh. Provádění ručních úloh trvá déle, což zpomaluje řešení jednotlivých incidentů a snižuje počet incidentů, které můžou jednotliví analytici vyřešit. Ruční úlohy také analytiky více unavují, což zvyšuje riziko zpoždění v důsledku lidských chyb a snižuje schopnost analytiků efektivně se soustředit na složité úlohy. S využitím funkcí automatizace pracovních postupů ve službách Azure Security Center a Azure Sentinel můžete v reakci na příchozí upozornění zabezpečení automaticky aktivovat akce nebo spouštět playbooky. Playbook provádí akce, jako jsou odesílání oznámení, zakazování účtů nebo izolace problematických sítí. 

- [Konfigurace automatizace pracovních postupů ve službě Security Center](../security-center/workflow-automation.md)

- [Nastavení automatizovaných reakcí na hrozby ve službě Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Nastavení automatizovaných reakcí na hrozby ve službě Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="posture-and-vulnerability-management"></a>Správa stavu a ohrožení zabezpečení

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: Správa stavu a ohrožení zabezpečení](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Provádění pravidelných simulací útoku

**Pokyny:** Provádějte u vašich prostředků Azure podle potřeby penetrační testování nebo aktivity etického testování průniku a zajistěte nápravu všech kritických zjištění o zabezpečení.
Postupujte podle pravidel penetračního testování cloudu Microsoftu, abyste zajistili, že vaše penetrační testy nejsou v rozporu se zásadami Microsoftu. Využijte strategii Microsoftu a provádění etického testování průniku a penetračního testování živých webů proti cloudové infrastruktuře, službám a aplikacím spravovaným Microsoftem.

- [Penetrační testování v Azure](../security/fundamentals/pen-testing.md)

- [Pravidla penetračního testování](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Etické testování průniku do cloudu Microsoftu](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Sdílená

## <a name="governance-and-strategy"></a>Zásady správného řízení a strategie

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: Zásady správného řízení a strategie](../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definování strategie správy prostředků a ochrany dat 

**Pokyny:** Zajistěte zdokumentování a komunikaci jasné strategie průběžného monitorování a ochrany systémů a dat. Jako prioritu si stanovte zjišťování, hodnocení, ochranu a monitorování důležitých obchodních dat a systémů. 

Tato strategie by měla zahrnovat zdokumentované pokyny, zásady a standardy pro následující aspekty: 

-   Standard klasifikace dat podle obchodních rizik

-   Přehled bezpečnostní organizace o rizicích a inventáři prostředků 

-   Schvalování použití služeb Azure ze strany bezpečnostní organizace 

-   Zabezpečení prostředků v rámci jejich životního cyklu

-   Požadovaná strategie řízení přístupu podle klasifikace dat organizace

-   Využívání funkcí ochrany dat nativních pro Azure a funkcí ochrany dat třetích stran

-   Požadavky na šifrování přenášených a neaktivních uložených dat

-   Příslušné kryptografické standardy

Další informace najdete na následujících odkazech:
- [Doporučení pro architekturu zabezpečení Azure – Úložiště, data a šifrování](/azure/architecture/framework/security/storage-data-encryption?amp;bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Základy zabezpečení Azure – Zabezpečení dat v Azure, šifrování a úložiště](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework – Osvědčené postupy šifrování a zabezpečení dat v Azure](../security/fundamentals/data-encryption-best-practices.md?amp;bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Srovnávací test zabezpečení Azure – Správa prostředků](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Srovnávací test zabezpečení Azure – Ochrana dat](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definování podnikové strategie segmentace 

**Doprovodné** materiály: Vytvořte strategii pro celé podniky pro segmentaci přístupu k prostředkům pomocí kombinace identity, sítě, aplikace, předplatného, skupiny pro správu, a to mimo jiné ovládací prvky.

Opatrně najděte rovnováhu mezi potřebou oddělit zabezpečení a potřebou zajistit denní provoz systémů, které potřebují vzájemně komunikovat a přistupovat k datům.

Zajistěte, aby se strategie segmentace implementovala konzistentně napříč typy ovládacích prvků včetně zabezpečení sítě, modelů identit a přístupu a oprávnění aplikace nebo modelů přístupu a ovládacích prvků pro lidské procesy.

- [Pokyny ke strategii segmentace v Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Pokyny ke strategii segmentace v Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Sladění segmentace sítě s podnikovou strategií segmentace](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definování strategie správy stavu zabezpečení

**Pokyny:** Průběžně měřte a zmírňujte rizika pro jednotlivé prostředky a prostředí, ve kterém jsou hostované. Jako prioritu si stanovte prostředky s vysokou hodnotou a vysoce exponované oblasti útoku, jako jsou publikované aplikace, vstupní a výstupní body sítě, koncové body uživatelů a správců atd.

- [Srovnávací test zabezpečení Azure – Správa stavu a ohrožení zabezpečení](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Sladění rolí, povinností a odpovědnosti v rámci organizace

**Pokyny:** Zajistěte zdokumentování a komunikaci jasné strategie pro role a odpovědnost ve vaší bezpečnostní organizaci. Jako prioritu si stanovte zajištění jasné odpovědnosti za rozhodnutí ohledně zabezpečení, informování všech o modelu sdílené odpovědnosti a informování technických týmů o technologiích pro zabezpečení cloudu.

- [Osvědčený postup zabezpečení Azure 1 – Lidé: Informování týmů o cestě ke cloudovému zabezpečení](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Osvědčený postup zabezpečení Azure 2 – Lidé: Informování týmů o technologiích cloudového zabezpečení](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Osvědčený postup zabezpečení Azure 3 – Procesy: Přiřazení odpovědnosti za rozhodnutí ohledně cloudového zabezpečení](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definování strategie zabezpečení sítě

**Pokyny:** Nastavte v rámci celkové bezpečnostní strategie řízení přístupu vaší organizace přístup k zabezpečení sítě Azure.  

Tato strategie by měla zahrnovat zdokumentované pokyny, zásady a standardy pro následující aspekty: 

-   Centralizovaná správa sítě a odpovědnost za zabezpečení

-   Model segmentace virtuálních sítí v souladu s podnikovou strategií segmentace

-   Strategie náprav v různých scénářích hrozeb a útoků

-   Strategie pro hraniční, příchozí a výchozí internetový přenos dat

-   Strategie propojení hybridního cloudu a místního prostředí

-   Aktuální artefakty zabezpečení sítě (např. síťové diagramy, referenční síťová architektura)

Další informace najdete na následujících odkazech:
- [Osvědčený postup zabezpečení Azure 11 – Architektura: Jednotná bezpečnostní strategie](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Srovnávací test zabezpečení Azure – Zabezpečení sítě](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Přehled zabezpečení sítě Azure](../security/fundamentals/network-overview.md)

- [Strategie pro architekturu podnikové sítě](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definování strategie privilegovaného přístupu a identit

**Pokyny**: navažte přístup k identitě Azure a privilegovanému přístupu v rámci celkové strategie řízení přístupu organizace.  

Tato strategie by měla zahrnovat zdokumentované pokyny, zásady a standardy pro následující aspekty: 

-   Centralizovaný systém identit a ověřování a jeho propojení s dalšími interními a externími systémy identit

-   Metody silného ověřování v různých případech použití a podmínkách

-   Ochrana vysoce privilegovaných uživatelů

-   Monitorování a zpracování neobvyklých aktivit uživatelů  

-   Proces kontroly a odsouhlasení identit a přístupu uživatelů

Další informace najdete v odkazovaných odkazech.

- [Srovnávací test zabezpečení Azure – Správa identit](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Srovnávací test zabezpečení Azure – Privilegovaný přístup](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Osvědčený postup zabezpečení Azure 11 – Architektura: Jednotná bezpečnostní strategie](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Přehled zabezpečení správy identit Azure](../security/fundamentals/identity-management-overview.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definování strategie protokolování a reakce na hrozby

**Pokyny:** Nastavte strategii protokolování a reakce na hrozby, abyste mohli rychle detekovat a zmírňovat hrozby při současném splnění požadavků na dodržování předpisů. Jako prioritu si stanovte poskytování vysoce kvalitních upozornění a bezproblémových prostředí analytikům, aby se mohli soustředit na hrozby, a ne na integraci a ruční kroky. 

Tato strategie by měla zahrnovat zdokumentované pokyny, zásady a standardy pro následující aspekty: 

-   Role a odpovědnost organizace s ohledem na operace zabezpečení (SecOps) 

-   Jasně definovaný proces reakce na incidenty, který je v souladu s NIST nebo jiným průmyslovým rámcem 

-   Zaznamenávání a uchovávání protokolů pro potřeby detekce hrozeb, reakce na incidenty a zajištění dodržování předpisů

-   Centralizovaný přehled a korelace informací o hrozbách s využitím řešení SIEM, nativních funkcí Azure a dalších zdrojů 

-   Plán komunikace a oznamování pro vaše zákazníky, dodavatele a zúčastněné strany z řad veřejnosti

-   Používání nativních platforem Azure a platforem třetích stran při řešení incidentů, například pro účely protokolování a detekce hrozeb, zmírňování a likvidace útoků a forenzní účely

-   Procesy pro řešení incidentů a aktivit po incidentech, jako je uchovávání poznatků, zkušeností a důkazů

Další informace najdete na následujících odkazech:

- [Srovnávací test zabezpečení Azure – Protokolování a detekce hrozeb](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Srovnávací test zabezpečení Azure – Reakce na incidenty](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Osvědčený postup zabezpečení Azure 4 – Procesy: Aktualizace procesů reakce na incidenty pro cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Cloud Adoption Framework – Průvodce rozhodováním ohledně protokolování a generování sestav](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Správa a monitorování na podnikové úrovni v Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

## <a name="next-steps"></a>Další kroky

- Projděte si [přehled srovnávacího testu zabezpečení Azure v2](../security/benchmarks/overview.md).
- Přečtěte si další informace o [standardních hodnotách zabezpečení Azure](../security/benchmarks/security-baselines-overview.md).