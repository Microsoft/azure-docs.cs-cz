---
title: Základní hodnoty zabezpečení Azure pro Azure bastionu
description: Základní plán zabezpečení Azure bastionu poskytuje pokyny a materiály k postupům pro implementaci doporučení zabezpečení, která jsou určená ve srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: bastion
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 007b82b4466dfc2fbfc9c11340583175da45d92e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025760"
---
# <a name="azure-security-baseline-for-azure-bastion"></a>Základní hodnoty zabezpečení Azure pro Azure bastionu

Tato základní hodnota zabezpečení se vztahuje na pokyny od [verze Azure Security test 2,0](../security/benchmarks/overview.md) pro Azure bastionu. Srovnávací test zabezpečení Azure poskytuje doporučení, jak můžete zabezpečit cloudová řešení v Azure. Obsah se seskupuje podle **ovládacích prvků zabezpečení** , které definuje srovnávací test zabezpečení Azure, a souvisejících pokynů vztahujících se na Azure bastionu. **Ovládací prvky** , které se nevztahují na Azure bastionu, se vyloučily.

Pokud chcete zjistit, jak Azure bastionu kompletně mapuje srovnávací test zabezpečení Azure, přečtěte si [kompletní soubor mapování standardních hodnot zabezpečení Azure bastionu](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: zabezpečení sítě](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementace zabezpečení pro interní provoz

**Pokyny**: když nasadíte prostředky Azure bastionu, musíte vytvořit nebo použít stávající virtuální síť. Zajistěte, aby všechny virtuální sítě Azure dodržovaly princip segmentace podniku, který se rovná obchodním rizikům. Každý systém, který by mohl organizaci zvýšit riziko, by se měl izolovat v rámci vlastní virtuální sítě a dostatečně zabezpečený pomocí skupiny zabezpečení sítě (NSG).

Služba Azure bastionu vyžaduje, aby pro správnou funkci služby měly být otevřené následující porty:

- Příchozí přenos dat:
   - Příchozí přenos dat z veřejného Internetu: Azure bastionu vytvoří veřejnou IP adresu, která vyžaduje port 443 na veřejné IP adrese pro přenos příchozích dat. Na AzureBastionSubnet se nevyžaduje otevřít port 3389/22. 

   - Příchozí přenos dat z roviny ovládacího prvku Azure bastionu: pro připojení řídicí roviny povolte port 443 příchozí ze značky služby GatewayManager. To umožňuje, aby řídicí plocha, která je správcem brány, mohla komunikovat s Azure bastionu.

- Odchozí přenos dat:

   - Odchozí přenos dat do cílových virtuálních počítačů (VM): Azure bastionu se dostane k cílovým virtuálním počítačům přes privátní IP adresu. Skupin zabezpečení sítě musí umožňovat přenos odchozího provozu do jiných cílových podsítí virtuálních počítačů pro porty 3389 a 22.

   - Odchozí přenos dat do jiných veřejných koncových bodů v Azure: Azure bastionu musí být schopný se připojit k různým veřejným koncovým bodům v rámci Azure (například pro ukládání protokolů diagnostiky a měřičů měření). Z tohoto důvodu Azure bastionu potřebuje pro značku služby AzureCloud odchozí až 443.

Připojení ke Správci brány a značce služby Azure je chráněné (uzamčené) pomocí certifikátů Azure. Externí entity, včetně spotřebitelů těchto prostředků, nemůžou komunikovat na těchto koncových bodech. 

- [Jak vytvořit skupinu zabezpečení sítě s pravidly zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

- [Další informace o požadavcích na bastionu NSG najdete tady.](bastion-nsg.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="ns-2-connect-private-networks-together"></a>NS-2: spojování privátních sítí

**Pokyny**: Azure bastionu nevystavuje žádné koncové body, ke kterým se dá dostat prostřednictvím privátní sítě. Azure bastionu podporuje nasazení do sítě s partnerským vztahem pro centralizaci nasazení bastionu a povolení připojení mezi sítěmi.

- [Partnerský vztah virtuálních sítí Azure bastionu a Virtual Network](vnet-peering.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="identity-management"></a>Správa identit

*Další informace najdete v tématu [testování výkonnosti Azure Security: Správa identit](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: standardizace Azure Active Directory jako centrální systém pro ověřování a identifikaci

**Pokyny**: Služba Azure bastionu je integrovaná s Azure Active Directory (Azure AD), což je výchozí služba pro správu identit a přístupu v Azure. Uživatelé mají přístup k Azure Portal pomocí ověřování Azure AD ke správě služby Azure bastionu (vytváření, aktualizace a odstraňování prostředků bastionu).

Připojení k virtuálním počítačům pomocí Azure bastionu spoléhá buď na klíč SSH, nebo na uživatelské jméno a heslo, a v současné době nepodporuje použití přihlašovacích údajů Azure AD.

Při připojování k virtuálním počítačům pomocí Azure bastionu se kromě klíče SSH nebo uživatelského jména a hesla budou potřebovat následující přiřazení rolí:
- Role čtenáře na cílovém virtuálním počítači
- Role čtecího zařízení na síťové kartě s privátní IP adresou cílového virtuálního počítače
- Role čtenáře u prostředku Azure Bastion

Další informace najdete v následujících odkazech:

- [Připojení k virtuálnímu počítači se systémem Linux pomocí Azure bastionu](bastion-connect-vm-ssh.md)

- [Připojení k virtuálnímu počítači s Windows pomocí Azure bastionu](bastion-connect-vm-rdp.md)

- [Předdefinované role v Azure](../role-based-access-control/built-in-roles.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: použití jednotného přihlašování (SSO) Azure AD pro přístup k aplikacím

**Pokyny**: Azure bastionu nepodporuje jednotné přihlašování pro ověřování při ověřování prostředků virtuálních počítačů, podporují se jenom SSH nebo uživatelské jméno a heslo. Azure bastionu ale používá Azure Active Directory (Azure AD) k zajištění správy identit a přístupu pro celou službu. Uživatelé se můžou ověřit ve službě Azure AD a získat tak přístup k prostředkům Azure bastionu a spravovat je bez jakýchkoli vlastních synchronizovaných podnikových identit prostřednictvím Azure AD Connect. 

- [Principy jednotného přihlašování k aplikacím pomocí Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Služba Azure AD Connect](../active-directory/hybrid/whatis-azure-ad-connect.md)

- [Připojení k virtuálnímu počítači se systémem Linux pomocí Azure bastionu](bastion-connect-vm-ssh.md)

- [Připojení k virtuálnímu počítači s Windows pomocí Azure bastionu](bastion-connect-vm-rdp.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: použití ovládacích prvků pro silné ověřování pro všechny přístupy založené na Azure Active Directory

**Doprovodné** materiály: Služba Azure bastionu je integrovaná s Azure Active Directory (Azure AD) pro přístup a správu služby. Nakonfigurujte Azure Multi-Factor Authentication pro vašeho tenanta Azure AD. Azure AD podporuje ovládací prvky silného ověřování prostřednictvím služby Multi-Factor Authentication (MFA) a metod silného hesla.  
- Multi-Factor Authentication: Povolte Azure AD MFA a sledujte Azure Security Center doporučení pro správu identit a přístupu pro vaše nastavení MFA. Vícefaktorové ověřování se dá vyhovět všem uživatelům, vybrat uživatele nebo na úrovni jednotlivých uživatelů na základě podmínek přihlášení a rizikových faktorů. 

- Ověřování bez hesla: k dispozici jsou tři možnosti ověřování bez hesla: Windows Hello pro firmy, Microsoft Authenticator aplikace a místní metody ověřování, jako jsou čipové karty. 

- [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Seznámení s možnostmi ověřování s neheslem pro Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: monitorování a upozornění na anomálie účtů

**Doprovodné** materiály: Povolte protokoly diagnostiky pro vzdálené relace Azure bastionu a pomocí těchto protokolů si můžete zobrazit, kteří uživatelé se k nimž úlohám připojí, v jakém čase, od kterého a dalších relevantních informací o protokolování. Vytvořte výstrahy pro určité protokolované bastionu relace pomocí Azure Monitor, abyste byli informováni v případě, že se v protokolech zjistily anomálie.

- [Další informace o tom, jak povolit protokolování diagnostiky, najdete tady.](diagnostic-logs.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM – 6: omezit přístup k prostředkům Azure na základě podmínek

**Pokyny**: ke službě Azure bastionu můžete přistupovat jenom prostřednictvím Azure Portal. přístup k Azure Portal se dá omezit pomocí podmíněného přístupu Azure Active Directory (Azure AD). Podmíněný přístup Azure AD můžete použít pro přesnější řízení přístupu na základě uživatelem definovaných podmínek, jako je třeba vyžadování přihlášení uživatelů z určitých rozsahů IP adres pro použití MFA.

Zákazník může k dalšímu omezení přístupu k virtuálnímu počítači použít také jiné zásady řízení přístupu na základě rolí na úrovni virtuálních počítačů připojených k doméně.

- [Další informace o podmíněném přístupu ke službě Azure AD najdete tady.](../active-directory/conditional-access/overview.md)

- [Přehled podmíněného přístupu Azure](../active-directory/conditional-access/overview.md)

- [Společné zásady podmíněného přístupu](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Konfigurace správy relace ověřování pomocí podmíněného přístupu](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="privileged-access"></a>Privilegovaný přístup

*Další informace najdete v tématu [Azure Security benchmark: Privileged Access](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: omezení přístupu pro správu na systémy kritické pro podnikání

**Pokyny**: Azure bastionu používá řízení přístupu na základě role Azure (Azure RBAC) k izolaci přístupu k podnikovým systémům tím, že omezuje přístup k určitým virtuálním počítačům, kterým je udělený přístup. Nezapomeňte postupovat podle principu minimálního oprávnění, aby uživatelé měli jenom oprávnění potřebná k provádění jejich konkrétních úloh.

Ujistěte se, že také omezíte přístup k systémům pro správu, identitu a zabezpečení, které mají přístup pro správu k vašemu podnikovému přístupu, jako jsou Doména služby Active Directory Controllers (DCs), nástroje zabezpečení a nástroje pro správu systému s agenty nainstalovanými v důležitých podnikových systémech. Útočníci, kteří tyto systémy pro správu a zabezpečení ohrožují, si je můžou hned weaponize, aby vyzrazeni důležité obchodní prostředky.

Všechny typy ovládacích prvků přístupu by se měly zarovnávat na strategii segmentace vaší organizace, aby se zajistilo jednotné řízení přístupu.

- [Požadované role pro přístup k virtuálnímu počítači pomocí Azure bastionu](bastion-faq.md#roles)

- [Komponenty a referenční model Azure](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Přístup ke skupině pro správu](../governance/management-groups/overview.md#management-group-access)

- [Správci předplatného Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: pravidelně kontrolovat a sjednotit přístup uživatelů

**Pokyny**: Azure bastionu používá ke správě svých prostředků účty Azure Active Directory (Azure AD) a Azure RBAC. Pravidelně kontrolujte uživatelské účty a přiřazení přístupu, abyste měli jistotu, že jsou účty a jejich přístup platné. Kontroly přístupu Azure AD můžete použít ke kontrole členství ve skupinách, přístupu k podnikovým aplikacím a přiřazování rolí. Vytváření sestav Azure AD může poskytovat protokoly, které vám pomůžou zjistit zastaralé účty. K usnadnění procesu kontroly můžete použít také Azure AD Privileged Identity Management k vytvoření pracovního postupu sestavy kontroly přístupu.

Kromě toho je možné Azure Privileged Identity Management nakonfigurovat tak, aby výstrahu při vytváření nadměrného počtu účtů správců a identifikaci účtů správce, které jsou zastaralé nebo nesprávně nakonfigurované.

- [Vytvoření kontroly přístupu pro role prostředků Azure v Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Odebrání přístupu k delegování](../lighthouse/how-to/remove-delegation.md)

- [Jak používat kontroly identity a přístupu v Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: nastavení nouzového přístupu v Azure AD

**Doprovodné** materiály: Azure bastionu je integrovaný do Azure Active Directory a Azure RBAC pro správu svých prostředků. Pokud se chcete vyhnout náhodnému uzamčení vaší organizace Azure AD, nastavte účet pro nouzový přístup pro přístup, když nebudete moct použít normální účty pro správu. Účty pro nouzový přístup jsou obvykle vysoce privilegované a neměly by být přiřazeny konkrétním jednotlivcům. Účty pro nouzový přístup jsou omezené na nouzové nebo "oddělovací sklo", kde se nedají použít běžné účty pro správu.

Měli byste zajistit, aby pověření (například heslo, certifikát nebo čipová karta) pro účty pro nouzový přístup byla zabezpečená a známá jenom jednotlivcům, kteří jsou oprávněni je používat jenom v nouzi.

- [Správa účtů pro nouzový přístup v Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatizace správy nároků 

**Pokyny**: Služba Azure bastionu je integrovaná s Azure Active Directory (Azure AD) a Azure RBAC pro správu prostředků. Pomocí funkcí správy opravňujících ke službě Azure AD můžete automatizovat pracovní postupy pro žádosti o přístup, včetně přiřazení přístupu, revizí a vypršení platnosti. Podporuje se také duální nebo vícenásobné schválení.

- [Co jsou kontroly přístupu Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Co je Správa nároků Azure AD](../active-directory/governance/entitlement-management-overview.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: použití pracovních stanic s privilegovaným přístupem

**Doprovodné** materiály: zabezpečené, izolované pracovní stanice jsou kriticky důležité pro zabezpečení citlivých rolí, jako jsou správci, vývojáři a kritické operátory služby. V závislosti na vašich požadavcích můžete pomocí vysoce zabezpečených uživatelských pracovních stanic provádět úlohy správy pro správu pomocí prostředků Azure bastionu v produkčních prostředích. Pomocí Azure Active Directory, programu Microsoft Defender Advanced Threat Protection (ATP) a/nebo Microsoft Intune nasaďte zabezpečenou a spravovanou pracovní stanici uživatelů pro úlohy správy. Zabezpečená pracovní stanice se dají centrálně spravovat tak, aby vynutila zabezpečenou konfiguraci, včetně silného ověřování, standardních hodnot softwaru a hardwaru a omezeného logického a síťového přístupu. 

- [Pochopení pracovních stanic s privilegovaným přístupem](../active-directory/devices/concept-azure-managed-workstation.md)

- [Nasazení privilegované pracovní stanice přístupu](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: sledování pouze dostatečné správy (princip nejnižší úrovně oprávnění) 

**Pokyny**: Azure bastionu je integrovaný s řízením přístupu na základě role (RBAC) Azure pro správu prostředků. Azure RBAC umožňuje spravovat přístup k prostředkům Azure prostřednictvím přiřazení rolí. Tyto předdefinované role můžete přiřadit uživatelům, skupinám, objektům služby a spravovaným identitám. Pro určité prostředky jsou předem definované předdefinované role a tyto role můžou být v inventáři nebo prostřednictvím nástrojů, jako je Azure CLI, Azure PowerShell nebo Azure Portal. Oprávnění, která přiřadíte k prostředkům prostřednictvím Azure RBAC, by měla být vždy omezená na to, co role vyžadují. Tím se doplňují přístup JIT (just in time) Azure AD Privileged Identity Management (PIM) a měl by se pravidelně kontrolovat. Pomocí integrovaných rolí můžete přidělit oprávnění a v případě potřeby vytvářet pouze vlastní role.

Při připojování k virtuálním počítačům pomocí Azure bastionu bude uživatel potřebovat následující přiřazení rolí:
- Role čtenáře na cílovém virtuálním počítači
- Role čtecího zařízení na síťové kartě s privátní IP adresou cílového virtuálního počítače
- Role čtenáře u prostředku Azure Bastion

Další informace najdete v následujících odkazech:

- [Připojení k virtuálnímu počítači se systémem Linux pomocí Azure bastionu](bastion-connect-vm-ssh.md)

- [Připojení k virtuálnímu počítači s Windows pomocí Azure bastionu](bastion-connect-vm-rdp.md)

- [Předdefinované role v Azure](../role-based-access-control/built-in-roles.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="asset-management"></a>Správa aktiv

*Další informace najdete v článku [Srovnávací test zabezpečení Azure: Správa prostředků](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>DOP. 1: Zajistěte, aby měl tým zabezpečení přehled o rizicích pro prostředky

**Doprovodné** materiály: Ujistěte se, že jsou bezpečnostním týmům udělená oprávnění čtenáře zabezpečení ve vašem tenantovi Azure a předplatných, aby mohli monitorovat bezpečnostní rizika pomocí Azure Security Center. 

V závislosti na tom, jak jsou strukturované zodpovědnosti týmu zabezpečení, může být monitorování rizik zabezpečení zodpovědností centrálního týmu zabezpečení nebo místního týmu. V takovém případě musí být přehledy a rizika zabezpečení vždy agregované centrálně v rámci organizace. 

Oprávnění čtenář zabezpečení je možné využít široce pro celého tenanta (kořenovou skupinu pro správu) nebo vymezenou pro skupiny pro správu nebo konkrétní předplatná. 

Poznámka: k získání přehledu o úlohách a službách může být potřeba další oprávnění. 

- [Přehled role čtecího modulu zabezpečení](../role-based-access-control/built-in-roles.md#security-reader)

- [Přehled služby Azure Skupiny pro správu](../governance/management-groups/overview.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>DOP. 2: Ujistěte se, že má tým zabezpečení přístup k inventáři prostředků a metadatům.

**Doprovodné** materiály: použití značek pro prostředky Azure bastionu, skupiny prostředků a předplatná k jejich logickému uspořádání do taxonomie. Každá značka se skládá z názvu a páru hodnot. Můžete například použít název Prostředí a hodnotu Produkční na všechny prostředky v produkčním prostředí. Ujistěte se, že týmy zabezpečení mají přístup k průběžně aktualizovaným inventáři prostředků v Azure. Můžou použít Azure Resource Graph k dotazování a zjišťování všech prostředků v předplatných, včetně služeb Azure, aplikací a síťových prostředků.

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

- [Správa inventáře Azure Security Center prostředků](../security-center/asset-inventory.md)

- [Další informace o označování prostředků najdete v průvodci pro pojmenování a označení prostředku.](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: pomocí Azure Policy můžete auditovat a omezovat, které služby můžou uživatelé zřídit ve vašem prostředí. to zahrnuje možnost povolit nebo zakázat nasazení prostředků Azure bastionu. Pomocí Azure Resource graphu se můžete dotazovat a zjišťovat prostředky v rámci svých předplatných. Pomocí Azure Monitor můžete také vytvořit pravidla pro aktivaci výstrah při zjištění neschválené služby.

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>DOP. 4: zajištění zabezpečení správy životního cyklu prostředků

**Pokyny**: Odeberte přístup k prostředkům Azure bastionu, které byly nasazeny, když už nepotřebujete k minimalizaci prostoru pro útoky. Uživatelé můžou spravovat svoje prostředky Azure bastionu prostřednictvím rozhraní API pro Azure Portal, CLI nebo REST. Můžete také odstranit nebo vynutit odpojení probíhající vzdálené relace, pokud již nepotřebujete nebo jste identifikovali jako potenciální hrozbu.

- [Odstranění vynuceného odpojení vzdálené relace](session-monitoring.md#view)

- [Rozhraní příkazového řádku Azure](https://docs.microsoft.com/powershell/module/az.network/?view=azps-5.1.0#networking&amp;preserve-view=true)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>DOP. 5: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Doprovodné** materiály: Služba Azure bastionu je integrovaná s Azure Active Directory (Azure AD) pro identitu a ověřování. Pomocí podmíněného přístupu Azure můžete omezit schopnost uživatelů pracovat se správcem prostředků Azure tak, že nakonfigurujete "blokování přístupu" pro aplikaci Microsoft Azure Management.

- [Jak nakonfigurovat podmíněný přístup k blokování přístupu k Azure Resource Manageru](../role-based-access-control/conditional-access-azure-management.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

## <a name="logging-and-threat-detection"></a>Protokolování a detekce hrozeb

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a detekce hrozeb](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: povolení detekce hrozeb pro správu identit a přístupu v Azure

**Pokyny**: Azure bastionu se integruje s Azure Active Directory (Azure AD) a služba je dostupná přes Azure Portal. Ve výchozím nastavení jsou akce správy služby (například vytváření, aktualizace a odstraňování) zachyceny prostřednictvím protokolu aktivit Azure. Uživatelé by také měli povolit protokoly prostředků Azure bastionu, jako je třeba relace BastionAuditLogs pro sledování relací bastionu.

Azure AD poskytuje následující protokoly uživatelů, které se dají zobrazit v sestavách Azure AD nebo v nich integrovat s Azure Monitor, Azure Sentinelem nebo jinými nástroji SIEM/Monitoring pro složitější účely monitorování a analýzy: 
-   Přihlášení – sestava přihlášení poskytuje informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů.

-   Protokoly auditu – Zajišťuje sledovatelnost prostřednictvím protokolů všech změn provedených různými funkcemi v rámci Azure AD. Mezi příklady protokolů auditu patří změny provedené u všech prostředků v rámci Azure AD, jako je přidávání nebo odebírání uživatelů, aplikací, skupin, rolí a zásad.

-   Riziková přihlášení – Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.

-   Uživatelé označení příznakem rizika – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený.

Azure Security Center může také upozorňovat na určité podezřelé aktivity, jako je například nadměrný počet neúspěšných pokusů o ověření a zastaralých účtů v rámci předplatného. Kromě základního monitorování hygieny zabezpečení může modul ochrany před internetovými útoky Azure Security Center taky shromažďovat podrobnější výstrahy zabezpečení z jednotlivých výpočetních prostředků Azure (jako jsou virtuální počítače, kontejnery, App Service), datové prostředky (například SQL DB a Storage) a vrstvy služeb Azure. Tato funkce umožňuje zobrazit anomálie účtů v jednotlivých prostředcích.

- [Protokoly prostředků Azure bastionu](diagnostic-logs.md)

- [Sestavy aktivit auditu v Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Povolit Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Ochrana před hrozbami v Azure Security Center](/azure/security-center/threat-protection)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: povolení protokolování pro síťové aktivity Azure

**Doprovodné** materiály: Povolte protokoly prostředků Azure bastionu a pomocí těchto diagnostických protokolů si můžete zobrazit, kteří uživatelé se k nimž úlohám připojí, v jakém čase, od kterého a dalších relevantních informací o protokolování. Uživatelé můžou tyto protokoly nakonfigurovat tak, aby se odesílaly do účtu úložiště pro dlouhodobé uchovávání a auditování.

Povolte a Shromážděte protokoly prostředků skupiny zabezpečení sítě (NSG) a protokoly NSG Flow ve skupinách zabezpečení sítě, které se používají pro virtuální sítě, které máte nasazený prostředek Azure bastionu. Tyto protokoly se pak dají použít k analýze zabezpečení sítě a k podpoře vyšetřování incidentů, lovu hrozeb a generování výstrah zabezpečení. Protokoly toku můžete odeslat do pracovního prostoru Azure Monitor Log Analytics a pak pomocí Analýza provozu získat přehledy.

- [Povolení a práce s protokoly Azure bastionu](diagnostic-logs.md)

- [Jak povolit protokoly toku skupin zabezpečení sítě](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Metriky a protokoly Azure Firewallu](../firewall/logs-and-metrics.md)

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

- [Monitorování pomocí Network Watcher](../network-watcher/network-watcher-monitoring-overview.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: povolení protokolování pro prostředky Azure

**Doprovodné** materiály: protokoly aktivit, které jsou automaticky k dispozici, obsahují všechny operace zápisu (Put, post, DELETE) pro prostředky Azure bastionu s výjimkou operací čtení (Get). Protokoly aktivit se dají použít k nalezení chyby při odstraňování potíží nebo sledování, jak uživatel ve vaší organizaci změnil prostředek.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Principy protokolování a různých typů protokolů v Azure](../azure-monitor/platform/platform-logs-overview.md)

- [Povolení protokolů prostředků Azure pro Azure bastionu ](diagnostic-logs.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: centralizace správy protokolu zabezpečení a analýzy

**Doprovodné** materiály: centralizace úložiště protokolování a analýzy pro povolení korelace. U každého zdroje protokolu se ujistěte, že jste přiřadili vlastníka dat, pokyny pro přístup, umístění úložiště, jaké nástroje se používají ke zpracování a přístupu k datům a k požadavkům na uchovávání dat.

Ujistěte se, že Integrujte protokoly aktivit Azure do svého centrálního protokolování. Ingestování protokolů prostřednictvím Azure Monitor k agregaci dat zabezpečení generovaných zařízeními koncového bodu, síťovými prostředky a dalšími systémy zabezpečení. V Azure Monitor použijte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty Azure Storage pro dlouhodobé a archivační úložiště.

Kromě toho povolte a zaveďte data do Azure Sentinel nebo SIEM třetí strany.

Řada organizací zvolí použití Azure Sentinel pro "horká" data, která se používají často a Azure Storage pro "studená" data, která se používají méně často.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurace uchovávání úložiště protokolu

**Doprovodné** materiály: Zajistěte, aby všechny účty úložiště nebo pracovní prostory Log Analytics používané pro ukládání protokolů Azure bastionu měly dobu uchování protokolu nastavenou v souladu s předpisy pro dodržování předpisů vaší organizace.

V Azure Monitor můžete nastavit dobu uchování pracovního prostoru Log Analytics podle předpisů pro dodržování předpisů vaší organizace.

- [Jak nakonfigurovat dobu uchování Log Analytics pracovního prostoru](../azure-monitor/platform/manage-cost-storage.md)

- [Ukládání protokolů prostředků v Azure Storagem účtu](/azure/azure-monitor/platform/resource-logs-collect-storage)

- [Povolení a práce s protokoly Azure bastiony](diagnostic-logs.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v odpovědi na [incidenty Azure Security test:](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Příprava – aktualizace procesu reakce na incidenty pro Azure

**Doprovodné** materiály: Ujistěte se, že vaše organizace má procesy, které reagují na incidenty zabezpečení, aktualizovala tyto procesy pro Azure a pravidelně je vykonává za účelem zajištění připravenosti.

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

Vysoce kvalitní výstrahy se dají vytvářet na základě zkušeností z minulých incidentů, ověřených zdrojů komunity a nástrojů určených k vygenerování a vyčištění výstrah odmítnutím a korelacemi různých zdrojů signálu. 

Azure Security Center poskytuje vysoce kvalitní výstrahy napříč mnoha prostředky Azure. K streamování výstrah do Azure Sentinel můžete použít datový konektor ASC. Azure Sentinel umožňuje vytvářet Pokročilá pravidla výstrah pro automatické generování incidentů pro účely šetření. 

Vyexportujte výstrahy a doporučení Azure Security Center pomocí funkce exportu, která vám usnadní identifikaci rizik pro prostředky Azure. Vyexportujte výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem.

- [Postup konfigurace exportu](../security-center/continuous-export.md)

- [Jak streamovat výstrahy do Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: detekce a analýza – prozkoumání incidentu

**Doprovodné** materiály: Zajistěte, aby Analytiki mohli zadávat dotazy a používat různé zdroje dat při zkoumání potenciálních incidentů, abyste mohli vytvořit úplný přehled o tom, co se stalo. K tomu, aby bylo možné sledovat aktivity potenciálního útočníka v rámci dezaktivačního řetězu, by měly být shromažďovány různé protokoly.  Měli byste taky zajistit, aby byly informace o studiu a učení pro jiné analytiky a budoucí historické odkazy.  

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

Azure Security Center přiřadí každému upozornění závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se Security Center ve vyhledávání, nebo na analýze, která se používá k vystavení výstrahy, a také na úrovni spolehlivosti, u které došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

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

*Další informace najdete v článku o [stav a správě ohrožení zabezpečení Azure Security test](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Vytvoření zabezpečených konfigurací pro služby Azure 

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro Azure bastionu s Azure Policy. Pomocí aliasů Azure Policy v oboru názvů Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo vymáhání konfigurace sítě pro Azure bastionu. Zákazníci mohou také vytvořit zabezpečenou konfiguraci pomocí plánů Azure nebo šablon ARM k bezpečnému a konzistentnímu nasazení prostředků bastionu.

- [Jak zobrazit dostupné aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Další informace o šablonách ARM](../azure-resource-manager/templates/overview.md)

- [Přehled o Azure modrotisky](../governance/blueprints/overview.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: udržování zabezpečených konfigurací pro služby Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro Azure bastionu s Azure Policy. Pomocí aliasů Azure Policy v oboru názvů Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace sítě vašich prostředků bastionu.

- [Jak zobrazit dostupné aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

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

-   Použití nativních funkcí Azure a ochrany dat třetích stran

-   Požadavky na šifrování dat pro případy použití v přenosech a v REST

-   Příslušné kryptografické standardy

Další informace najdete v následujících odkazech:
- [Doporučení architektury zabezpečení Azure – úložiště, data a šifrování](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Základy zabezpečení Azure – zabezpečení, šifrování a úložiště dat Azure](../security/fundamentals/encryption-overview.md)

- [Rozhraní pro přijetí do cloudu – osvědčené postupy zabezpečení a šifrování dat Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Test zabezpečení Azure – Správa prostředků](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Azure Security test – ochrana dat](/azure/security/benchmarks/security-benchmark-v2-data-protection)

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

**Doprovodné** materiály: nepřetržitě měřit a zmírnit rizika pro jednotlivé prostředky a prostředí, ve kterém jsou hostovány. Určete prioritu prostředků s vysokou hodnotou a vysoce vydaných ploch útoků, jako jsou publikované aplikace, příchozí a výstupní body sítě, koncové body uživatele a správce atd.

- [Azure Security test – stav a Správa ohrožení zabezpečení](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

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

Další informace najdete v následujících odkazech:
- [Osvědčené postupy zabezpečení Azure 11 – architektura. Jednotná strategie zabezpečení](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Testování zabezpečení Azure – zabezpečení sítě](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Přehled zabezpečení sítě Azure](../security/fundamentals/network-overview.md)

- [Strategie architektury podnikové sítě](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

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

- [Azure Security benchmark – Správa identit](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure Security test – privilegovaný přístup](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

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

Další informace najdete v následujících odkazech:

- [Azure Security test benchmark – protokolování a detekce hrozeb](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Srovnávací test zabezpečení Azure – odpověď na incident](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Doporučený postup zabezpečení Azure 4 – proces. Aktualizace procesů reakce na incidenty pro Cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Průvodce rozhodnutím o přijetí, protokolování a vytváření sestav pro Azure](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure Enterprise Scale, Management a monitoring](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="next-steps"></a>Další kroky

- Další informace najdete v tématu [Přehled Azure Security benchmark v2](/azure/security/benchmarks/overview) .
- Další informace o [plánech zabezpečení Azure](/azure/security/benchmarks/security-baselines-overview)
