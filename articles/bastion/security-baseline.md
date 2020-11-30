---
title: Základní hodnoty zabezpečení Azure pro Azure bastionu
description: Základní plán zabezpečení Azure bastionu poskytuje pokyny a materiály k postupům pro implementaci doporučení zabezpečení, která jsou určená ve srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: bastion
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 85638d2ef3499ca71057879852eb582b950b7e2c
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325673"
---
# <a name="azure-security-baseline-for-azure-bastion"></a>Základní hodnoty zabezpečení Azure pro Azure bastionu

Tato základní hodnota zabezpečení se vztahuje na pokyny od [verze Azure Security test 2,0](../security/benchmarks/overview.md) pro Azure bastionu. Srovnávací test zabezpečení Azure nabízí doporučení k zabezpečení cloudových řešení v Azure. Obsah se seskupuje podle **ovládacích prvků zabezpečení** , které definuje srovnávací test zabezpečení Azure, a souvisejících pokynů vztahujících se na Azure bastionu. **Ovládací prvky** , které se nevztahují na Azure bastionu, se vyloučily.

Pokud chcete zjistit, jak Azure bastionu kompletně mapuje srovnávací test zabezpečení Azure, přečtěte si [kompletní soubor mapování standardních hodnot zabezpečení Azure bastionu](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: zabezpečení sítě](../security/benchmarks/security-controls-v2-network-security.md).*

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

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="ns-2-connect-private-networks-together"></a>NS-2: spojování privátních sítí

**Pokyny**: Azure bastionu nevystavuje žádné koncové body, ke kterým se dá dostat prostřednictvím privátní sítě. Azure bastionu podporuje nasazení do sítě s partnerským vztahem pro centralizaci nasazení bastionu a povolení připojení mezi sítěmi.

- [Partnerský vztah virtuálních sítí Azure bastionu a Virtual Network](vnet-peering.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

## <a name="identity-management"></a>Správa identit

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: Správa identit](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardizace Azure Active Directory jako centrálního systému identit a ověřování

**Pokyny**: Služba Azure bastionu je integrovaná s Azure Active Directory (Azure AD), což je výchozí služba pro správu identit a přístupu v Azure. Uživatelé mají přístup k Azure Portal pomocí ověřování Azure AD ke správě služby Azure bastionu (vytváření, aktualizace a odstraňování prostředků bastionu).

Připojení k virtuálním počítačům pomocí Azure bastionu spoléhá buď na klíč SSH, nebo na uživatelské jméno a heslo, a v současné době nepodporuje použití přihlašovacích údajů Azure AD.

Při připojování k virtuálním počítačům pomocí Azure bastionu se kromě klíče SSH nebo uživatelského jména a hesla budou potřebovat následující přiřazení rolí:
- Role čtenáře na cílovém virtuálním počítači
- Role čtecího zařízení na síťové kartě s privátní IP adresou cílového virtuálního počítače
- Role čtenáře u prostředku Azure Bastion

Další informace najdete na následujících odkazech:

- [Připojení k virtuálnímu počítači se systémem Linux pomocí Azure bastionu](bastion-connect-vm-ssh.md)

- [Připojení k virtuálnímu počítači s Windows pomocí Azure bastionu](bastion-connect-vm-rdp.md)

- [Předdefinované role v Azure](../role-based-access-control/built-in-roles.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Používání jednotného přihlašování Azure AD pro přístup k aplikacím

**Pokyny**: Azure bastionu nepodporuje jednotné přihlašování pro ověřování při ověřování prostředků virtuálních počítačů, podporují se jenom SSH nebo uživatelské jméno a heslo. Azure bastionu ale používá Azure Active Directory (Azure AD) k zajištění správy identit a přístupu pro celou službu. Uživatelé se můžou ověřit ve službě Azure AD a získat tak přístup k prostředkům Azure bastionu a spravovat je bez jakýchkoli vlastních synchronizovaných podnikových identit prostřednictvím Azure AD Connect. 

- [Principy jednotného přihlašování k aplikacím s využitím Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Služba Azure AD Connect](../active-directory/hybrid/whatis-azure-ad-connect.md)

- [Připojení k virtuálnímu počítači se systémem Linux pomocí Azure bastionu](bastion-connect-vm-ssh.md)

- [Připojení k virtuálnímu počítači s Windows pomocí Azure bastionu](bastion-connect-vm-rdp.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Používání kontrolních mechanismů silného ověřování pro veškerý přístup na základě Azure Active Directory

**Doprovodné** materiály: Služba Azure bastionu je integrovaná s Azure Active Directory (Azure AD) pro přístup a správu služby. Nakonfigurujte Azure Multi-Factor Authentication pro vašeho tenanta Azure AD. Azure AD podporuje ovládací prvky silného ověřování prostřednictvím služby Multi-Factor Authentication (MFA) a metod silného hesla.  
- Multi-Factor Authentication: Povolte Azure AD MFA a sledujte Azure Security Center doporučení pro správu identit a přístupu pro vaše nastavení MFA. Vícefaktorové ověřování se dá vyhovět všem uživatelům, vybrat uživatele nebo na úrovni jednotlivých uživatelů na základě podmínek přihlášení a rizikových faktorů. 

- Ověřování bez hesla: k dispozici jsou tři možnosti ověřování bez hesla: Windows Hello pro firmy, Microsoft Authenticator aplikace a místní metody ověřování, jako jsou čipové karty. 

- [Povolení vícefaktorového ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Úvod do možností ověřování bez hesla pro Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorování anomálií u účtů a upozorňování na ně

**Doprovodné** materiály: Povolte protokoly diagnostiky pro vzdálené relace Azure bastionu a pomocí těchto protokolů si můžete zobrazit, kteří uživatelé se k nimž úlohám připojí, v jakém čase, od kterého a dalších relevantních informací o protokolování. Vytvořte výstrahy pro určité protokolované bastionu relace pomocí Azure Monitor, abyste byli informováni v případě, že se v protokolech zjistily anomálie.

- [Další informace o tom, jak povolit protokolování diagnostiky, najdete tady.](diagnostic-logs.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM – 6: omezit přístup k prostředkům Azure na základě podmínek

**Pokyny**: ke službě Azure bastionu můžete přistupovat jenom prostřednictvím Azure Portal. přístup k Azure Portal se dá omezit pomocí podmíněného přístupu Azure Active Directory (Azure AD). Podmíněný přístup Azure AD můžete použít pro přesnější řízení přístupu na základě uživatelem definovaných podmínek, jako je třeba vyžadování přihlášení uživatelů z určitých rozsahů IP adres pro použití MFA.

Zákazník může k dalšímu omezení přístupu k virtuálnímu počítači použít také jiné zásady řízení přístupu na základě rolí na úrovni virtuálních počítačů připojených k doméně.

- [Další informace o podmíněném přístupu ke službě Azure AD najdete tady.](../active-directory/conditional-access/overview.md)

- [Přehled podmíněného přístupu Azure](../active-directory/conditional-access/overview.md)

- [Společné zásady podmíněného přístupu](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Konfigurace správy relace ověřování pomocí podmíněného přístupu](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

## <a name="privileged-access"></a>Privilegovaný přístup

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: Privilegovaný přístup](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: omezení přístupu pro správu na systémy kritické pro podnikání

**Pokyny**: Azure bastionu používá řízení přístupu na základě role Azure (Azure RBAC) k izolaci přístupu k podnikovým systémům tím, že omezuje přístup k určitým virtuálním počítačům, kterým je udělený přístup. Nezapomeňte postupovat podle principu minimálního oprávnění, aby uživatelé měli jenom oprávnění potřebná k provádění jejich konkrétních úloh.

Ujistěte se, že také omezíte přístup k systémům pro správu, identitu a zabezpečení, které mají přístup pro správu k vašemu podnikovému přístupu, jako jsou Doména služby Active Directory Controllers (DCs), nástroje zabezpečení a nástroje pro správu systému s agenty nainstalovanými v důležitých podnikových systémech. Útočníci, kteří tyto systémy pro správu a zabezpečení ohrožují, si je můžou hned weaponize, aby vyzrazeni důležité obchodní prostředky.

Všechny typy ovládacích prvků přístupu by se měly zarovnávat na strategii segmentace vaší organizace, aby se zajistilo jednotné řízení přístupu.

- [Požadované role pro přístup k virtuálnímu počítači pomocí Azure bastionu](bastion-faq.md#roles)

- [Komponenty a referenční model Azure](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Přístup ke skupině pro správu](../governance/management-groups/overview.md#management-group-access)

- [Správci předplatného Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Pravidelné kontroly a sjednocování uživatelského přístupu

**Pokyny**: Azure bastionu používá ke správě svých prostředků účty Azure Active Directory (Azure AD) a Azure RBAC. Pravidelně kontrolujte uživatelské účty a přiřazení přístupu, abyste měli jistotu, že jsou účty a jejich přístup platné. Kontroly přístupu Azure AD můžete použít ke kontrole členství ve skupinách, přístupu k podnikovým aplikacím a přiřazování rolí. Vytváření sestav Azure AD může poskytovat protokoly, které vám pomůžou zjistit zastaralé účty. K usnadnění procesu kontroly můžete použít také Azure AD Privileged Identity Management k vytvoření pracovního postupu sestavy kontroly přístupu.

Kromě toho je možné Azure Privileged Identity Management nakonfigurovat tak, aby výstrahu při vytváření nadměrného počtu účtů správců a identifikaci účtů správce, které jsou zastaralé nebo nesprávně nakonfigurované.

- [Vytvoření kontroly přístupu pro role prostředků Azure v Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Odebrání přístupu k delegování](../lighthouse/how-to/remove-delegation.md)

- [Používání kontrol přístupu a identit Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: nastavení nouzového přístupu v Azure AD

**Doprovodné** materiály: Azure bastionu je integrovaný do Azure Active Directory a Azure RBAC pro správu svých prostředků. Pokud se chcete vyhnout náhodnému uzamčení vaší organizace Azure AD, nastavte účet pro nouzový přístup pro přístup, když nebudete moct použít normální účty pro správu. Účty pro nouzový přístup jsou obvykle vysoce privilegované a neměly by být přiřazeny konkrétním jednotlivcům. Účty pro nouzový přístup jsou omezené na nouzové nebo "oddělovací sklo", kde se nedají použít běžné účty pro správu.

Měli byste zajistit, aby pověření (například heslo, certifikát nebo čipová karta) pro účty pro nouzový přístup byla zabezpečená a známá jenom jednotlivcům, kteří jsou oprávněni je používat jenom v nouzi.

- [Správa účtů pro nouzový přístup v Azure AD](../active-directory/roles/security-emergency-access.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatizace správy nároků 

**Pokyny**: Služba Azure bastionu je integrovaná s Azure Active Directory (Azure AD) a Azure RBAC pro správu prostředků. Pomocí funkcí správy opravňujících ke službě Azure AD můžete automatizovat pracovní postupy pro žádosti o přístup, včetně přiřazení přístupu, revizí a vypršení platnosti. Podporuje se také duální nebo vícenásobné schválení.

- [Co jsou kontroly přístupu Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Co je Správa nároků Azure AD](../active-directory/governance/entitlement-management-overview.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: použití pracovních stanic s privilegovaným přístupem

**Doprovodné** materiály: zabezpečené, izolované pracovní stanice jsou kriticky důležité pro zabezpečení citlivých rolí, jako jsou správci, vývojáři a kritické operátory služby. V závislosti na vašich požadavcích můžete pomocí vysoce zabezpečených uživatelských pracovních stanic provádět úlohy správy pro správu pomocí prostředků Azure bastionu v produkčních prostředích. Pomocí Azure Active Directory, programu Microsoft Defender Advanced Threat Protection (ATP) a/nebo Microsoft Intune nasaďte zabezpečenou a spravovanou pracovní stanici uživatelů pro úlohy správy. Zabezpečená pracovní stanice se dají centrálně spravovat tak, aby vynutila zabezpečenou konfiguraci, včetně silného ověřování, standardních hodnot softwaru a hardwaru a omezeného logického a síťového přístupu. 

- [Pochopení pracovních stanic s privilegovaným přístupem](../active-directory/devices/concept-azure-managed-workstation.md)

- [Nasazení privilegované pracovní stanice přístupu](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Využívání správy jen v potřebném rozsahu (princip nejnižší úrovně nutných oprávnění) 

**Pokyny**: Azure bastionu je integrovaný s řízením přístupu na základě role (RBAC) Azure pro správu prostředků. Azure RBAC umožňuje spravovat přístup k prostředkům Azure prostřednictvím přiřazení rolí. Tyto předdefinované role můžete přiřadit uživatelům, skupinám, objektům služby a spravovaným identitám. Pro určité prostředky existují předdefinované role, které je možné inventarizovat nebo dotazovat pomocí nástrojů, jako jsou Azure CLI, Azure PowerShell nebo Azure Portal. Oprávnění k prostředkům přiřazená prostřednictvím Azure RBAC by vždy měla být omezená pouze na to, co konkrétní role vyžadují. Tento přístup doplňuje přístup založený na přístupu podle potřeby (JIT) ve službě Azure AD Privileged Identity Management (PIM) a měl by se pravidelně kontrolovat. Pomocí integrovaných rolí můžete přidělit oprávnění a v případě potřeby vytvářet pouze vlastní role.

Při připojování k virtuálním počítačům pomocí Azure bastionu bude uživatel potřebovat následující přiřazení rolí:
- Role čtenáře na cílovém virtuálním počítači
- Role čtecího zařízení na síťové kartě s privátní IP adresou cílového virtuálního počítače
- Role čtenáře u prostředku Azure Bastion

Další informace najdete na následujících odkazech:

- [Připojení k virtuálnímu počítači se systémem Linux pomocí Azure bastionu](bastion-connect-vm-ssh.md)

- [Připojení k virtuálnímu počítači s Windows pomocí Azure bastionu](bastion-connect-vm-rdp.md)

- [Předdefinované role v Azure](../role-based-access-control/built-in-roles.md)

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

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>DOP. 2: Ujistěte se, že má tým zabezpečení přístup k inventáři prostředků a metadatům.

**Doprovodné** materiály: použití značek pro prostředky Azure bastionu, skupiny prostředků a předplatná k jejich logickému uspořádání do taxonomie. Každá značka se skládá z názvu a páru hodnot. Můžete například použít název Prostředí a hodnotu Produkční na všechny prostředky v produkčním prostředí. Ujistěte se, že týmy zabezpečení mají přístup k průběžně aktualizovaným inventáři prostředků v Azure. Můžou použít Azure Resource Graph k dotazování a zjišťování všech prostředků v předplatných, včetně služeb Azure, aplikací a síťových prostředků.

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

- [Správa inventáře Azure Security Center prostředků](../security-center/asset-inventory.md)

- [Další informace o označování prostředků najdete v průvodci pro pojmenování a označení prostředku.](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: pomocí Azure Policy můžete auditovat a omezovat, které služby můžou uživatelé zřídit ve vašem prostředí. to zahrnuje možnost povolit nebo zakázat nasazení prostředků Azure bastionu. Pomocí Azure Resource graphu se můžete dotazovat a zjišťovat prostředky v rámci svých předplatných. Pomocí Azure Monitor můžete také vytvořit pravidla pro aktivaci výstrah při zjištění neschválené služby.

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Zajištění zabezpečení správy životního cyklu prostředků

**Pokyny**: Odeberte přístup k prostředkům Azure bastionu, které byly nasazeny, když už nepotřebujete k minimalizaci prostoru pro útoky. Uživatelé můžou spravovat svoje prostředky Azure bastionu prostřednictvím rozhraní API pro Azure Portal, CLI nebo REST. Můžete také odstranit nebo vynutit odpojení probíhající vzdálené relace, pokud již nepotřebujete nebo jste identifikovali jako potenciální hrozbu.

- [Odstranění vynuceného odpojení vzdálené relace](session-monitoring.md#view)

- [Rozhraní příkazového řádku Azure](/powershell/module/az.network/?amp;preserve-view=true&view=azps-5.1.0#networking)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>DOP. 5: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Doprovodné** materiály: Služba Azure bastionu je integrovaná s Azure Active Directory (Azure AD) pro identitu a ověřování. Pomocí podmíněného přístupu Azure můžete omezit schopnost uživatelů pracovat se správcem prostředků Azure tak, že nakonfigurujete "blokování přístupu" pro aplikaci Microsoft Azure Management.

- [Jak nakonfigurovat podmíněný přístup k blokování přístupu k Azure Resource Manageru](../role-based-access-control/conditional-access-azure-management.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

## <a name="logging-and-threat-detection"></a>Protokolování a detekce hrozeb

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: Protokolování a detekce hrozeb](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Povolení detekce hrozeb pro správu identit a přístupu v Azure

**Pokyny**: Azure bastionu se integruje s Azure Active Directory (Azure AD) a služba je dostupná přes Azure Portal. Ve výchozím nastavení jsou akce správy služby (například vytváření, aktualizace a odstraňování) zachyceny prostřednictvím protokolu aktivit Azure. Uživatelé by také měli povolit protokoly prostředků Azure bastionu, jako je třeba relace BastionAuditLogs pro sledování relací bastionu.

Azure AD poskytuje následující protokoly uživatelů, které se dají zobrazit v sestavách Azure AD nebo v nich integrovat s Azure Monitor, Azure Sentinelem nebo jinými nástroji SIEM/Monitoring pro složitější účely monitorování a analýzy: 
-   Přihlášení – Sestava přihlášení poskytuje informace o využití spravovaných aplikací a aktivitách přihlašování uživatelů.

-   Protokoly auditu – Zajišťuje sledovatelnost prostřednictvím protokolů všech změn provedených různými funkcemi v rámci Azure AD. Mezi příklady protokolů auditu patří změny jakýchkoli prostředků v rámci Azure AD, jako jsou přidání nebo odebrání uživatelů, aplikací, skupin, rolí nebo zásad.

-   Riziková přihlášení – Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.

-   Uživatelé označení příznakem rizika – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený.

Azure Security Center může také upozorňovat na určité podezřelé aktivity, jako je například nadměrný počet neúspěšných pokusů o ověření a zastaralých účtů v rámci předplatného. Kromě základního monitorování hygieny zabezpečení může modul ochrany před internetovými útoky Azure Security Center taky shromažďovat podrobnější výstrahy zabezpečení z jednotlivých výpočetních prostředků Azure (jako jsou virtuální počítače, kontejnery, App Service), datové prostředky (například SQL DB a Storage) a vrstvy služeb Azure. Tato funkce umožňuje zobrazit anomálie účtů v jednotlivých prostředcích.

- [Protokoly prostředků Azure bastionu](diagnostic-logs.md)

- [Sestavy aktivit auditu v Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Povolení ochrany identit Azure](../active-directory/identity-protection/overview-identity-protection.md)

- [Ochrana před hrozbami v Azure Security Center](../security-center/azure-defender.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: povolení protokolování pro síťové aktivity Azure

**Doprovodné** materiály: Povolte protokoly prostředků Azure bastionu a pomocí těchto diagnostických protokolů si můžete zobrazit, kteří uživatelé se k nimž úlohám připojí, v jakém čase, od kterého a dalších relevantních informací o protokolování. Uživatelé můžou tyto protokoly nakonfigurovat tak, aby se odesílaly do účtu úložiště pro dlouhodobé uchovávání a auditování.

Povolte a Shromážděte protokoly prostředků skupiny zabezpečení sítě (NSG) a protokoly NSG Flow ve skupinách zabezpečení sítě, které se používají pro virtuální sítě, které máte nasazený prostředek Azure bastionu. Tyto protokoly se pak dají použít k analýze zabezpečení sítě a k podpoře vyšetřování incidentů, lovu hrozeb a generování výstrah zabezpečení. Protokoly toku můžete odeslat do pracovního prostoru Azure Monitor Log Analytics a pak pomocí Analýza provozu získat přehledy.

- [Povolení a práce s protokoly Azure bastionu](diagnostic-logs.md)

- [Jak povolit protokoly toku skupin zabezpečení sítě](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Metriky a protokoly Azure Firewallu](../firewall/logs-and-metrics.md)

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

- [Monitorování pomocí Network Watcher](../network-watcher/network-watcher-monitoring-overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: povolení protokolování pro prostředky Azure

**Doprovodné** materiály: protokoly aktivit, které jsou automaticky k dispozici, obsahují všechny operace zápisu (Put, post, DELETE) pro prostředky Azure bastionu s výjimkou operací čtení (Get). Protokoly aktivit se dají použít k nalezení chyby při odstraňování potíží nebo sledování, jak uživatel ve vaší organizaci změnil prostředek.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Principy protokolování a různých typů protokolů v Azure](../azure-monitor/platform/platform-logs-overview.md)

- [Povolení protokolů prostředků Azure pro Azure bastionu ](diagnostic-logs.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: centralizace správy protokolu zabezpečení a analýzy

**Doprovodné** materiály: centralizace úložiště protokolování a analýzy pro povolení korelace. U každého zdroje protokolu se ujistěte, že jste přiřadili vlastníka dat, pokyny pro přístup, umístění úložiště, jaké nástroje se používají ke zpracování a přístupu k datům a k požadavkům na uchovávání dat.

Ujistěte se, že Integrujte protokoly aktivit Azure do svého centrálního protokolování. Ingestování protokolů prostřednictvím Azure Monitor k agregaci dat zabezpečení generovaných zařízeními koncového bodu, síťovými prostředky a dalšími systémy zabezpečení. V Azure Monitor použijte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty Azure Storage pro dlouhodobé a archivační úložiště.

Kromě toho povolte a zaveďte data do Azure Sentinel nebo SIEM třetí strany.

Řada organizací zvolí použití Azure Sentinel pro "horká" data, která se používají často a Azure Storage pro "studená" data, která se používají méně často.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurace uchovávání úložiště protokolu

**Doprovodné** materiály: Zajistěte, aby všechny účty úložiště nebo pracovní prostory Log Analytics používané pro ukládání protokolů Azure bastionu měly dobu uchování protokolu nastavenou v souladu s předpisy pro dodržování předpisů vaší organizace.

V Azure Monitor můžete nastavit dobu uchování pracovního prostoru Log Analytics podle předpisů pro dodržování předpisů vaší organizace.

- [Jak nakonfigurovat dobu uchování Log Analytics pracovního prostoru](../azure-monitor/platform/manage-cost-storage.md)

- [Ukládání protokolů prostředků v Azure Storagem účtu](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)

- [Povolení a práce s protokoly Azure bastiony](diagnostic-logs.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

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

**Doprovodné** materiály: Ujistěte se, že máte proces pro vytváření vysoce kvalitních výstrah a měření kvality výstrah. To vám umožní učit se z minulých incidentů a určovat prioritu upozornění pro analytiky, aby neztráceli čas falešně pozitivními výsledky.

Vysoce kvalitní výstrahy se dají vytvářet na základě zkušeností z minulých incidentů, ověřených zdrojů komunity a nástrojů určených k vygenerování a vyčištění výstrah odmítnutím a korelacemi různých zdrojů signálu. 

Azure Security Center poskytuje vysoce kvalitní výstrahy napříč mnoha prostředky Azure. Pomocí datového konektoru ASC můžete upozornění streamovat do služby Azure Sentinel. Azure Sentinel umožňuje vytvářet rozšířená pravidla upozornění, která automaticky generují incidenty pro účely šetření. 

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

Azure Security Center jednotlivým upozorněním přiřazuje závažnost, která vám pomůže určit, jaká upozornění by se měla vyšetřit jako první. Závažnost je založena na tom, jak se Security Center ve vyhledávání, nebo na analýze, která se používá k vystavení výstrahy, a také na úrovni spolehlivosti, u které došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

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

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: Správa stavu a ohrožení zabezpečení](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Vytvoření zabezpečených konfigurací pro služby Azure 

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro Azure bastionu s Azure Policy. Pomocí aliasů Azure Policy v oboru názvů Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo vymáhání konfigurace sítě pro Azure bastionu. Zákazníci mohou také vytvořit zabezpečenou konfiguraci pomocí plánů Azure nebo šablon ARM k bezpečnému a konzistentnímu nasazení prostředků bastionu.

- [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias?amp;preserve-view=true&view=azps-4.8.0)

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Další informace o šablonách ARM](../azure-resource-manager/templates/overview.md)

- [Přehled o Azure modrotisky](../governance/blueprints/overview.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: udržování zabezpečených konfigurací pro služby Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro Azure bastionu s Azure Policy. Pomocí aliasů Azure Policy v oboru názvů Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace sítě vašich prostředků bastionu.

- [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias?amp;preserve-view=true&view=azps-4.8.0)

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

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

-   Použití nativních funkcí Azure a ochrany dat třetích stran

-   Požadavky na šifrování přenášených a neaktivních uložených dat

-   Příslušné kryptografické standardy

Další informace najdete na následujících odkazech:
- [Doporučení pro architekturu zabezpečení Azure – Úložiště, data a šifrování](/azure/architecture/framework/security/storage-data-encryption?amp;bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Základy zabezpečení Azure – Zabezpečení dat v Azure, šifrování a úložiště](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework – Osvědčené postupy šifrování a zabezpečení dat v Azure](../security/fundamentals/data-encryption-best-practices.md?amp;bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Srovnávací test zabezpečení Azure – Správa prostředků](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Srovnávací test zabezpečení Azure – Ochrana dat](/azure/security/benchmarks/security-controls-v2-data-protection)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definování podnikové strategie segmentace 

**Pokyny:** Vytvořte celopodnikovou strategii segmentace přístupu k prostředkům s využitím kombinace identity, sítě, aplikace, předplatného, skupiny pro správu a dalších kontrolních mechanismů.

Opatrně najděte rovnováhu mezi potřebou oddělit zabezpečení a potřebou zajistit denní provoz systémů, které potřebují vzájemně komunikovat a přistupovat k datům.

Zajistěte konzistentní implementaci této strategie segmentace napříč různými typy kontrolních mechanismů, včetně zabezpečení sítě, modelů identit a přístupu, modelů oprávnění a přístupu k aplikacím a kontrolních mechanismů lidských procesů.

- [Pokyny ke strategii segmentace v Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Pokyny ke strategii segmentace v Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Sladění segmentace sítě s podnikovou strategií segmentace](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definování strategie správy stavu zabezpečení

**Pokyny:** Průběžně měřte a zmírňujte rizika pro jednotlivé prostředky a prostředí, ve kterém jsou hostované. Jako prioritu si stanovte prostředky s vysokou hodnotou a vysoce exponované oblasti útoku, jako jsou publikované aplikace, vstupní a výstupní body sítě, koncové body uživatelů a správců atd.

- [Srovnávací test zabezpečení Azure – Správa stavu a ohrožení zabezpečení](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

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

- [Srovnávací test zabezpečení Azure – Zabezpečení sítě](/azure/security/benchmarks/security-controls-v2-network-security)

- [Přehled zabezpečení sítě Azure](../security/fundamentals/network-overview.md)

- [Strategie pro architekturu podnikové sítě](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definování strategie privilegovaného přístupu a identit

**Pokyny:** Nastavte v rámci celkové bezpečnostní strategie řízení přístupu vaší organizace přístupy k privilegovanému přístupu a identitám Azure.  

Tato strategie by měla zahrnovat zdokumentované pokyny, zásady a standardy pro následující aspekty: 

-   Centralizovaný systém identit a ověřování a jeho propojení s dalšími interními a externími systémy identit

-   Metody silného ověřování v různých případech použití a podmínkách

-   Ochrana vysoce privilegovaných uživatelů

-   Monitorování a zpracování neobvyklých aktivit uživatelů  

-   Proces kontroly a odsouhlasení identit a přístupu uživatelů

Další informace najdete na následujících odkazech:

- [Srovnávací test zabezpečení Azure – Správa identit](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Srovnávací test zabezpečení Azure – Privilegovaný přístup](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Osvědčený postup zabezpečení Azure 11 – Architektura: Jednotná bezpečnostní strategie](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Přehled zabezpečení správy identit Azure](../security/fundamentals/identity-management-overview.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definování strategie protokolování a reakce na hrozby

**Pokyny:** Nastavte strategii protokolování a reakce na hrozby, abyste mohli rychle detekovat a zmírňovat hrozby při současném splnění požadavků na dodržování předpisů. Určete prioritu poskytování analytiků s vysoce kvalitními výstrahami a bezproblémové prostředí, aby se mohly soustředit na hrozby, nikoli na integraci a ruční kroky. 

Tato strategie by měla zahrnovat zdokumentované pokyny, zásady a standardy pro následující aspekty: 

-   Role a odpovědnost organizace s ohledem na operace zabezpečení (SecOps) 

-   Jasně definovaný proces reakce na incidenty, který je v souladu s NIST nebo jiným průmyslovým rámcem 

-   Zaznamenávání a uchovávání protokolů pro potřeby detekce hrozeb, reakce na incidenty a zajištění dodržování předpisů

-   Centralizovaný přehled a korelace informací o hrozbách s využitím řešení SIEM, nativních funkcí Azure a dalších zdrojů 

-   Plán komunikace a oznamování pro vaše zákazníky, dodavatele a zúčastněné strany z řad veřejnosti

-   Používání nativních platforem Azure a platforem třetích stran při řešení incidentů, například pro účely protokolování a detekce hrozeb, zmírňování a likvidace útoků a forenzní účely

-   Procesy pro řešení incidentů a aktivit po incidentech, jako je uchovávání poznatků, zkušeností a důkazů

Další informace najdete na následujících odkazech:

- [Srovnávací test zabezpečení Azure – Protokolování a detekce hrozeb](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Srovnávací test zabezpečení Azure – Reakce na incidenty](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Osvědčený postup zabezpečení Azure 4 – Procesy: Aktualizace procesů reakce na incidenty pro cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Cloud Adoption Framework – Průvodce rozhodováním ohledně protokolování a generování sestav](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Správa a monitorování na podnikové úrovni v Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

## <a name="next-steps"></a>Další kroky

- Projděte si [přehled srovnávacího testu zabezpečení Azure v2](../security/benchmarks/overview.md).
- Přečtěte si další informace o [standardních hodnotách zabezpečení Azure](../security/benchmarks/security-baselines-overview.md).