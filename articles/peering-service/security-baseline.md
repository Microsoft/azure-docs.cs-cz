---
title: Základní hodnota zabezpečení Azure pro službu Microsoft Azure peering Service
description: Základní plán zabezpečení služby Microsoft Azure peering Service poskytuje pokyny a prostředky pro implementaci doporučení zabezpečení, která jsou určená v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: peering-service
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 854c669861c6aff71861481843bff97fdb7157ab
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315483"
---
# <a name="azure-security-baseline-for-microsoft-azure-peering-service"></a>Základní hodnota zabezpečení Azure pro službu Microsoft Azure peering Service

Tato základní hodnota zabezpečení platí pro Microsoft Azure služby peering Service pokyny od [verze Azure Security test 2,0](../security/benchmarks/overview.md) . Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure. Obsah je seskupen podle **ovládacích prvků zabezpečení** , které jsou definovány pomocí srovnávacího testu zabezpečení Azure, a souvisejících pokynů vztahujících se na službu Microsoft Azure peering Service.

> [!NOTE]
> **Ovládací prvky** , které se nevztahují na službu Microsoft Azure peering Service nebo pro které je odpovědnost od Microsoftu, jsou vyloučené. Pokud chcete zjistit, jak Microsoft Azure služba peering Service kompletně namapována na srovnávací test zabezpečení Azure, přečtěte si **[kompletní soubor mapování standardních hodnot zabezpečení služby Microsoft Azure peering Service](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)**.

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [Azure Security Benchmark: zabezpečení sítě](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: nasazení systémů pro zabránění vniknutí/prevence vniknutí (ID/IP adresy)

**Doprovodné** materiály: služba partnerského vztahu nenabízí nativní zjišťování neoprávněných vniknutí ani služby prevence. Zákazníci by měli dodržovat osvědčené postupy zabezpečení, jako je například použití filtrování založeného na základě logiky hrozeb z Azure Firewall k upozornění a blokování provozu z a do známých a neškodlivých IP adres a domén, které se vztahují na své obchodní požadavky. Tyto IP adresy a domény se nacházejí ze služby Microsoft Threat-Intelligence feed. 

V případech, kdy je třeba kontrolovat datovou část, nasaďte z Azure Marketplace systémy pro detekci vniknutí nebo prevence od jiných výrobců, které mají funkce kontroly zatížení.  
Alternativně použijte řešení detekce a odezvy hostitele založeného na hostiteli ve spojení s (nebo namísto) systémů zjišťování nebo ochrany před internetovými útoky v síti.  

Pokud máte regulativní požadavek na využívání zjišťování neoprávněných vniknutí nebo systému ochrany před neoprávněnými vniknutími, zajistěte, aby byl vždy vyladěn, aby využíval nebo poskytoval vysoce kvalitní výstrahy. 

- [Postup nasazení Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketplace zahrnuje možnosti ID třetích stran.](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [EDR schopnost služby Microsoft Defender ATP](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="identity-management"></a>Správa identit

*Další informace najdete v tématu [Azure Security Benchmark: správa identit](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardizace Azure Active Directory jako centrálního systému pro identifikaci a ověřování

**Doprovodné** materiály: udržování inventáře uživatelských účtů s přístupem správce k řídicí rovině (například Azure Portal) vašich prostředků služby Microsoft Azure peering Service.

K nakonfigurování řízení přístupu na základě role Azure (Azure RBAC) použijte podokno Správa identit a přístupu (IAM) v Azure Portal pro vaše předplatné. Role se aplikují na uživatele, skupiny, instanční objekty a spravované identity ve službě Azure Active Directory (Azure AD).

- [Přidání nebo odebrání přiřazení rolí Azure pomocí portálu Azure Portal](../role-based-access-control/role-assignments-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Zabezpečená a automatická správa identit aplikací

**Doprovodné** materiály: spravované identity se pro službu peering Service nepodporují. Pro služby, které nepodporují spravované identity, jako je služba partnerského vztahu, použijte službu Azure Active Directory (Azure AD) k vytvoření instančního objektu s omezenými oprávněními na úrovni prostředků. 

- [Spravované identity Azure](../active-directory/managed-identities-azure-resources/overview.md)

- [Služby, které podporují spravované identity prostředků Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Instanční objekt Azure](/powershell/azure/create-azure-service-principal-azureps)

- [Vytvoření instančního objektu s certifikáty](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Použití jednotného přihlašování (SSO) Azure AD pro přístup k aplikacím

**Pokyny**: Služba Microsoft Azure peering Service používá Azure Active Directory (Azure AD) k zajištění správy identit a přístupu k prostředkům Azure. Patří sem podnikové identity, jako jsou zaměstnanci, i externí identity, jako jsou partneři a dodavatelé. 

Pomocí jednotného přihlašování můžete spravovat a zabezpečit přístup k datům a prostředkům vaší organizace místně a v cloudu. Připojte všechny své uživatele, aplikace a zařízení k Azure AD, ať máte bezproblémový, zabezpečený přístup a lepší viditelnost a kontrolu.

- [Principy jednotného přihlašování k aplikacím pomocí Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Použití řídicích prvků silného ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: Povolte vícefaktorové ověřování pomocí Azure Active Directory (Azure AD) a použijte doporučení pro správu identit a přístupu z Azure Security Center.

- [Postup povolení vícefaktorového ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorování a upozornění na anomálie účtů

**Doprovodné** materiály: použití PRIVILEGED Identity Management (PIM) s Azure Active Directory (Azure AD) pro generování protokolů a výstrah v případě, že v prostředí dojde k podezřelé nebo nebezpečné aktivitě. Navíc můžete pomocí zjišťování rizik Azure AD zobrazovat výstrahy a sestavy týkající se rizikového chování uživatelů.

- [Postup nasazení Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Vysvětlení zjišťování rizik Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Omezení přístupu k prostředkům Azure na základě podmínek

**Doprovodné** materiály: použití podmíněného přístupu s Azure Active Directory (Azure AD) pro přesnější řízení přístupu na základě uživatelem definovaných podmínek, jako je třeba vyžadování přihlášení uživatelů z určitých ROZSAHŮ IP adres pro použití vícefaktorového ověřování. Pomocí zásad podmíněného přístupu Azure AD pro různé případy použití lze také použít podrobnou správu relace ověřování. 

- [Přehled podmíněného přístupu Azure](../active-directory/conditional-access/overview.md)

- [Společné zásady podmíněného přístupu](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Konfigurace správy relací ověřování pomocí podmíněného přístupu](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Eliminace nezamýšleného prozrazení přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů v rámci Azure DevOps k identifikaci přihlašovacích údajů používaných v kódu. Kontrola přihlašovacích údajů také podporuje přesun zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je například Azure Key Vault.

V případě GitHubu můžete k identifikaci přihlašovacích údajů nebo jiné formy tajných kódů v kódu použít funkci nativního skenování tajných kódů.

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Skenování tajných kódů GitHubu](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="privileged-access"></a>Privilegovaný přístup

*Další informace najdete v tématu [Azure Security Benchmark: privilegovaný přístup](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Ochrana a omezení vysoce privilegovaných uživatelů

**Doprovodné** materiály: Omezte počet vysoce privilegovaných uživatelských účtů a chraňte tyto účty na vyšší úrovni. 

Nejdůležitější předdefinované role v Azure Active Directory (Azure AD) jsou globální správce a správce privilegovaných rolí, protože uživatelé přiřazení k těmto dvěma rolím můžou delegovat role správce. 

Díky těmto oprávněním můžou uživatelé přímo nebo nepřímo číst a upravovat všechny prostředky v prostředí Azure:

- Globální správce/správce společnosti: uživatelé s touto rolí mají přístup ke všem funkcím pro správu ve službě Azure AD a službám, které používají identity Azure AD.

- Správce privilegovaných rolí: uživatelé s touto rolí můžou spravovat přiřazení rolí v Azure AD i v Azure AD Privileged Identity Management (PIM). Tato role navíc umožňuje správu všech aspektů PIM a administrativních jednotek.

Pokud používáte vlastní role s konkrétními privilegovanými oprávněními, které jsou jim přiřazeny, můžou existovat i další kritické role, které je potřeba řídit. Použijte podobné ovládací prvky pro účet správce důležitých obchodních prostředků.  

Povolte privilegovanému přístupu k prostředkům Azure a Azure AD pomocí Azure AD Privileged Identity Management (PIM) privilegovaný přístup za běhu (just-in-time). JIT uděluje dočasná oprávnění k provádění privilegovaných úloh, pouze když je uživatelé potřebují. PIM může také generovat upozornění zabezpečení v případě podezřelých nebo nebezpečných aktivit ve vaší organizaci Azure AD.

- [Oprávnění role správce v Azure AD](/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

- [Používání upozornění zabezpečení služby Azure Privileged Identity Management](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Zabezpečení privilegovaného přístupu pro hybridní a cloudová nasazení v Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Omezení přístupu pro správu k nejdůležitějším podnikovým systémům

**Doprovodné** materiály: izolujte přístup k podnikovým systémům tím, že omezíte účty, kterým byl udělen privilegovaný přístup k předplatným a skupinám pro správu, ke kterým patří. 

Omezte přístup k systémům pro správu, identitu a zabezpečení, které mají přístup pro správu k důležitým podnikovým prostředkům, jako jsou Doména služby Active Directory Controllers (DC), nástroje zabezpečení a nástroje pro správu systému, s agenty nainstalovanými v důležitých podnikových systémech. Útočníci, kteří tyto systémy pro správu a zabezpečení ohrožují, si je můžou hned weaponize, aby vyzrazeni důležité obchodní prostředky. 

Všechny typy ovládacích prvků přístupu by se měly zarovnávat na strategii segmentace vaší organizace, aby se zajistilo jednotné řízení přístupu. 

Ujistěte se, že přiřadíte samostatné privilegované účty, které se liší od standardních uživatelských účtů používaných pro úlohy e-mailu, procházení a produktivity.

- [Komponenty a referenční model Azure](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Přístup ke skupině pro správu](https://docs.microsoft.com/azure/governance/management-groups/overview#management-group-access)

- [Správci předplatného Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Pravidelná kontrola a sjednocování přístupu uživatelů

**Doprovodné** materiály: Azure Active Directory (AD) poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Navíc můžete pomocí kontrol přístupu Azure identity efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatelů se dá pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé.

- [Hledání sestav aktivit služby Azure AD](../active-directory/reports-monitoring/howto-find-activity-reports.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Nastavení nouzového přístupu v Azure AD

**Pokyny**: nastavte nouzový účet pro přístup, pokud nejsou k dispozici běžné účty pro správu, abyste zabránili nechtěnému uzamknutí vaší organizace Azure Active Directory (Azure AD). Účty pro nouzový přístup mohou obsahovat oprávnění vyšší úrovně a neměla by být přiřazena konkrétním jednotlivcům. Omezte účty pro nouzový přístup na nouzové nebo "rozbité" scénáře.

Ujistěte se, že přihlašovací údaje (například heslo, certifikát nebo čipová karta) pro účty pro nouzový přístup jsou zabezpečené a známé jenom jednotlivcům, kteří mají oprávnění k jejich použití v Emergent situacích.

- [Správa účtů pro nouzový přístup v Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatizace správy nároků 

**Pokyny**: použití funkcí správy opravňujících k přístupu Azure Active Directory (Azure AD) k automatizaci pracovních postupů žádostí o přístup, včetně přiřazení přístupu, revizí a vypršení platnosti. Podporuje se také duální nebo vícenásobné schválení.

- [Co jsou recenze přístupu Azure Active Directory (Azure AD)](../active-directory/governance/access-reviews-overview.md)

- [Co je Správa nároků na Azure Active Directory (Azure AD)](../active-directory/governance/entitlement-management-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Použití pracovních stanic s privilegovaným přístupem

**Pokyny**: použití pracovní stanice s privilegovaným přístupem (privilegovaným přístupem) s vícefaktorového ověřováním z Azure Active Directory (Azure AD), které umožňuje přihlášení a konfiguraci prostředků souvisejících s ověřováním v Azure.

- [Pracovní stanice s privilegovaným přístupem](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Plánování nasazení vícefaktorového ověřování Azure AD založeného na cloudu](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Využívání správy jen v potřebném rozsahu (princip nejnižší úrovně nutných oprávnění) 

**Pokyny**: řízení přístupu na základě role v Azure (Azure RBAC) umožňuje spravovat přístup k prostředkům Azure prostřednictvím přiřazení rolí. Tyto role můžete přiřadit uživatelům, objektům služby skupin a spravovaným identitám. Pro určité prostředky jsou předem definované předdefinované role a tyto role můžou být v inventáři nebo prostřednictvím nástrojů, jako je Azure CLI, Azure PowerShell a Azure Portal.

Oprávnění, která přiřadíte k prostředkům prostřednictvím Azure RBAC, by měla být vždy omezená na to, co role vyžadují. Omezená oprávnění doplňují přístup k předběhu služby Azure Active Directory (Azure AD) Privileged Identity Management (PIM) a tato oprávnění by se měla pravidelně kontrolovat.

Využijte k přidělování oprávnění předdefinované role a vlastní role vytvářejte pouze v případě potřeby.

- [Co je řízení přístupu na základě role v Azure (Azure RBAC)](../role-based-access-control/overview.md)

- [Jak nakonfigurovat službu Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Používání kontrol přístupu a identit Azure AD](../active-directory/governance/access-reviews-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="asset-management"></a>Správa aktiv

*Další informace najdete v tématu [Azure Security Benchmark: správa prostředků](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Zajištění přehledu bezpečnostního týmu o rizicích pro prostředky

**Pokyny:** Zajistěte udělení oprávnění Čtenář zabezpečení ve vašem tenantovi Azure a vašich předplatných bezpečnostním týmům, aby mohly monitorovat bezpečnostní rizika s využitím služby Azure Security Center. 

V závislosti na nastavení struktury odpovědnosti bezpečnostních týmů může být monitorování bezpečnostních rizik odpovědností centrálního nebo místního bezpečnostního týmu. Přehledy zabezpečení a bezpečnostní rizika se však v rámci organizace musí vždy agregovat centrálně. 

Oprávnění Čtenář zabezpečení je možné přidělit pro celého tenanta (kořenová skupina pro správu) nebo je vymezit na konkrétní skupiny pro správu nebo konkrétní předplatná. 

Aby bylo možné získat přehled o úlohách a službách, může být potřeba další oprávnění. 

- [Přehled role Čtenář zabezpečení](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader)

- [Přehled skupin pro správu Azure](../governance/management-groups/overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Zajištění přístupu k inventáři a metadatům prostředků pro bezpečnostní tým

**Pokyny**: Ujistěte se, že týmy zabezpečení mají přístup k průběžně aktualizovaným inventáři prostředků v Azure. Bezpečnostní týmy tento inventář často potřebují k tomu, aby vyhodnotily potenciální slabá místa organizace vůči vznikajícím rizikům, a jako vstup pro průběžná vylepšování zabezpečení. 

Funkce inventáře Azure Security Center a Azure Resource Graph se můžou dotazovat na všechny prostředky v předplatných, včetně služeb Azure, aplikací a síťových prostředků.  

Uspořádejte logicky prostředky podle taxonomie vaší organizace pomocí značek i dalších metadat v Azure (název, popis a kategorie).  

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource Graphu](../governance/resource-graph/first-query-portal.md)

- [Správa inventáře Azure Security Center prostředků](../security-center/asset-inventory.md)

- [Další informace o označování prostředků najdete v průvodci pro pojmenování a označení prostředku.](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Používání jenom schválených služeb Azure

**Pokyny**: Služba Microsoft Azure peering Service podporuje nasazení na základě Azure Resource Manager a vynucení konfigurace pomocí Azure Policy v oboru názvů Microsoft. peering/peering. Pomocí Azure Policy můžete auditovat a omezovat služby, které můžou uživatelé zřídit ve vašem prostředí. Pomocí Azure Resource Graphu se můžete dotazovat na prostředky v rámci jejich předplatných a zjišťovat je. Pomocí Azure Monitoru můžete také vytvořit pravidla pro aktivaci upozornění při zjištění neschválené služby.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource Graphu](../governance/resource-graph/first-query-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>DOP. 5: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: Nakonfigurujte podmíněný přístup Azure tak, aby uživatelé mohli komunikovat s Azure Resource Manager konfigurací možnosti blokovat přístup pro aplikaci Microsoft Azure Management.

- [Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="logging-and-threat-detection"></a>Protokolování a detekce hrozeb

*Další informace najdete v tématu [Azure Security Benchmark: protokolování a detekce hrozeb](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: povolení detekce hrozeb pro prostředky Azure

**Doprovodné** materiály: Ujistěte se, že sledujete různé typy prostředků Azure pro potenciální hrozby a anomálie. Zaměřte se na získávání vysoce kvalitních výstrah, které omezí falešně pozitivní výsledky pro analytiky k řazení. Výstrahy se dají nacházet z dat protokolů, agentů nebo jiných dat.

Využijte Azure Security Center integrovanou funkci detekce hrozeb, která vychází z monitorování telemetrie služeb Azure a analýzy protokolů služby. Data se shromažďují pomocí Log Analytics agenta, který čte různé konfigurace a protokoly událostí související se zabezpečením ze systému a kopíruje data do vašeho pracovního prostoru pro účely analýzy. 

Kromě toho použijte Azure Sentinel k vytváření pravidel analýzy, které usnadňují hrozby, které odpovídají konkrétním kritériím v rámci vašeho prostředí. Pravidla generují incidenty, když jsou kritéria shodná, takže můžete prozkoumat jednotlivé incidenty. Funkce Sentinel Azure může taky importovat analýzy hrozeb jiných výrobců, aby se zlepšila její schopnost detekce hrozeb. 

- [Ochrana před hrozbami v Azure Security Center](/azure/security-center/threat-protection)

- [Referenční příručka pro Azure Security Center výstrahy zabezpečení](../security-center/alerts-reference.md)

- [Vytváření vlastních pravidel analýzy pro detekci hrozeb](../sentinel/tutorial-detect-threats-custom.md)

- [Funkce Azure Sentinel pro internetovou analýzu hrozeb](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Povolení detekce hrozeb pro správu identit a přístupu v Azure

**Doprovodné** materiály: Azure Active Directory (Azure AD) poskytují uživatelské protokoly, které se dají zobrazit s vytvářením sestav Azure AD nebo je integrovat s Azure monitor. Tyto protokoly je taky možné integrovat s protokolem Sentinel Azure nebo dalšími řešeními a nástroji pro monitorování SIEM (Security Information and Event Management) pro výkonnější monitorování a analytické případy použití:

- Přihlášení – sestava přihlášení poskytuje informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů.

- Protokoly auditu – poskytuje sledovatelnost prostřednictvím protokolů pro všechny změny provedené různými funkcemi v rámci služby Azure AD. Mezi příklady protokolů auditu patří změny provedené u všech prostředků v rámci Azure AD, jako je přidání nebo odebrání uživatelů, aplikací, skupin, rolí a zásad.

- Rizikové přihlášení – rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.

- Uživatelé označení příznakem rizika – rizikové uživatel je indikátorem uživatelského účtu, který mohl být napadený.

Azure Security Center může také upozorňovat na určité podezřelé aktivity, jako je například nadměrný počet neúspěšných pokusů o ověření u zastaralých účtů v rámci předplatného. Kromě základního monitorování hygieny zabezpečení může modul ochrany před internetovými útoky ve službě Security Center také shromažďovat další podrobné výstrahy zabezpečení od jednotlivých výpočetních prostředků Azure (například virtuálních počítačů, kontejnerů, App Service), datových prostředků (například databáze SQL a úložiště) a vrstev služeb Azure. Tato funkce umožňuje zobrazit anomálie účtů v jednotlivých prostředcích.

- [Sestavy aktivit auditu v Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Povolení ochrany identit Azure](../active-directory/identity-protection/overview-identity-protection.md)

- [Ochrana před hrozbami v Azure Security Center](/azure/security-center/threat-protection)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Povolení protokolování pro síťové aktivity Azure

**Pokyny**: Konfigurace telemetrie připojení, která poskytuje přehled o připojení mezi umístěním připojení a

Síť Microsoftu prostřednictvím služby Microsoft Azure peering Service.

- [Konfigurace telemetrie připojení](measure-connection-telemetry.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Povolení protokolování pro prostředky Azure

**Pokyny**: povolení protokolu aktivit Azure a odesílání protokolů do Log Analytics pracovního prostoru, centra událostí Azure nebo účtu Azure Storage pro archivaci 

Protokoly aktivit poskytují přehled o operacích provedených na vašich prostředcích Azure ExpressRoute na úrovni řídicích rovin. Pomocí dat protokolu aktivit Azure můžete určit "co, kdo a kdy" pro všechny operace zápisu (PUT, POST, DELETE) prováděné na úrovni řídicích roviny pro vaše prostředky.

- [Protokol aktivit Azure](/azure/azure-monitor/platform/activity-log)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizace správy a analýz protokolu zabezpečení

**Doprovodné** materiály: centralizace úložiště protokolování a analýzy pro povolení korelace. U každého zdroje protokolu se ujistěte, že jste přiřadili vlastníka dat, pokyny pro přístup, umístění úložiště, jaké nástroje se používají ke zpracování a přístupu k datům a k požadavkům na uchovávání dat.

Ujistěte se, že Integrujte protokoly aktivit Azure do svého centrálního protokolování. Ingestování protokolů prostřednictvím Azure Monitor k agregaci dat zabezpečení generovaných zařízeními koncového bodu, síťovými prostředky a dalšími systémy zabezpečení. V Azure Monitor použijte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty Azure Storage pro dlouhodobé a archivační úložiště.

Kromě toho povolte a zaveďte data do Azure Sentinel nebo SIEM třetí strany.

Řada organizací zvolí použití Azure Sentinel pro "horká" data, která se používají často a Azure Storage pro "studená" data, která se používají méně často.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurace uchovávání úložiště protokolů

**Doprovodné** materiály: v Azure monitor nastavte dobu uchování protokolu pro Log Analytics pracovní prostory, které jsou přidružené k vašim prostředkům Azure, podle předpisů pro dodržování předpisů vaší organizace.

- [Postup nastavení parametrů uchovávání protokolů](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [Azure Security Benchmark: reakce na incidenty](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Příprava – aktualizace procesu reakce na incidenty pro Azure

**Pokyny:** Zajistěte, aby vaše organizace měla procesy, jak reagovat na incidenty zabezpečení, aby tyto procesy aktualizovala pro Azure a pravidelně je testovala za účelem zajištění připravenosti.

- [Implementace zabezpečení v podnikovém prostředí](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Referenční příručka k reakcím na incidenty](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Příprava – nastavení oznámení o incidentu

**Pokyny:** Nastavte kontaktní informace pro incidenty zabezpečení ve službě Azure Security Center. Tyto kontaktní informace používá společnost Microsoft k tomu, aby vás kontaktovala v případě, že služba MSRC (Microsoft Security Response Center) zjistí, že s vašimi daty někdo nezákonně nebo neoprávněně pracoval. Máte také možnost přizpůsobit upozornění na incidenty a oznámení o incidentech v různých službách Azure v závislosti na tom, jak potřebujete na incidenty reagovat. 

- [Jak nastavit kontakt zabezpečení pro Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: detekce a analýza – vytvoření incidentů na základě vysoce kvalitních výstrah 

**Pokyny:** Zajistěte, abyste měli proces pro vytváření vysoce kvalitních upozornění a měření jejich kvality. To vám umožní poučit se z minulých incidentů a stanovit priority upozornění pro analytiky, aby nemuseli ztrácet čas s falešně pozitivními výsledky. 

Vysoce kvalitní upozornění se dají vytvářet na základě zkušeností z minulých incidentů, ověřených zdrojů komunity a nástrojů určených k vygenerování a vyčištění upozornění spojením a sladěním různých zdrojů signálu. 

Azure Security Center poskytuje vysoce kvalitní výstrahy napříč mnoha prostředky Azure. Pomocí datového konektoru ASC můžete upozornění streamovat do služby Azure Sentinel. Azure Sentinel umožňuje vytvářet rozšířená pravidla upozornění, která automaticky generují incidenty pro účely šetření. 

Pomocí funkce exportu můžete upozornění a doporučení služby Azure Security Center exportovat, abyste mohli lépe identifikovat rizika pro prostředky Azure. Upozornění a doporučení můžete exportovat ručně nebo průběžně a nepřetržitě.

- [Konfigurace exportu](../security-center/continuous-export.md)

- [Streamování upozornění do služby Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Detekce a analýza – prozkoumání incidentu

**Doprovodné** materiály: Zajistěte, aby Analytiki mohli zadávat dotazy a používat různé zdroje dat, protože při zkoumání potenciálních incidentů sestaví úplný přehled o tom, co se stalo Aby se zabránilo slepým místům, měly by se shromažďovat různorodé protokoly, které umožní sledovat aktivity potenciálního útočníka v celém průběhu útoku.  Měli byste také zajistit zaznamenávání přehledů a poznatků pro další analytiky a budoucí použití jako historické referenční informace.  

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

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Detekce a analýza – stanovení priorit incidentů

**Pokyny:** Poskytněte analytikům kontext, aby věděli, na jaké incidenty se zaměřit jako první v závislosti na závažnosti upozornění a citlivosti prostředků. 

Azure Security Center jednotlivým upozorněním přiřazuje závažnost, která vám pomůže určit, jaká upozornění by se měla vyšetřit jako první. Závažnost je založena na tom, jak se Security Center ve vyhledávání, nebo na analýze, která se používá k vystavení výstrahy, a také na úrovni spolehlivosti, u které došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Kromě toho můžete prostředky označit pomocí značek a vytvořit systém vytváření názvů, který vám pomůže identifikovat a kategorizovat prostředky Azure, zejména prostředky zpracovávající citlivá data.  Je vaší zodpovědností určit prioritu nápravy upozornění v závislosti na důležitosti prostředků Azure a prostředí, ve kterém k incidentu došlo.

- [Výstrahy zabezpečení ve službě Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Používání značek k uspořádání prostředků Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Zadržení, zničení a obnovení – automatizace zpracování incidentů

**Pokyny:** Automatizujte ruční opakující se úlohy, ay se zrychlila doba reakce a snížilo se zatížení analytiků. Ruční provádění úloh trvá déle, což zpomaluje jednotlivé incidenty a snižuje počet incidentů, které může analytik zvládnout. Ruční úlohy také analytiky více unavují, což zvyšuje riziko zpoždění v důsledku lidských chyb a snižuje schopnost analytiků efektivně se soustředit na složité úlohy. S využitím funkcí automatizace pracovních postupů ve službách Azure Security Center a Azure Sentinel můžete v reakci na příchozí upozornění zabezpečení automaticky aktivovat akce nebo spouštět playbooky. Playbook provádí akce, jako jsou odesílání oznámení, zakazování účtů nebo izolace problematických sítí. 

- [Konfigurace automatizace pracovních postupů ve službě Security Center](../security-center/workflow-automation.md)

- [Nastavení automatizovaných reakcí na hrozby ve službě Azure Security Center](https://docs.microsoft.com/azure/security-center/tutorial-security-incident#triage-security-alerts)

- [Nastavení automatizovaných reakcí na hrozby ve službě Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="posture-and-vulnerability-management"></a>Stav a správa ohrožení zabezpečení

*Další informace najdete v tématu [Azure Security Benchmark: stav a správa ohrožení zabezpečení](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Zřízení zabezpečených konfigurací pro služby Azure 

**Doprovodné** materiály: definování guardrails zabezpečení pro infrastrukturu a DevOps týmy tím, že usnadňují zabezpečenou konfiguraci služeb Azure, které používají. 

Zahajte konfiguraci zabezpečení služeb Azure pomocí standardních hodnot služeb ve srovnávacím testu zabezpečení Azure a upravte podle potřeby vaší organizace. 

Pomocí Azure Security Center můžete nakonfigurovat Azure Policy pro audit a prosazování konfigurací vašich prostředků Azure. 

Plány Azure můžete použít k automatizaci nasazení a konfigurace služeb a prostředí aplikací, včetně šablon Azure Resource Manager, řízení a zásad Azure RBAC a zásad v jediné definici podrobného plánu.

- [Ilustrace implementace guardrails v hraniční zóně na podnikové úrovni](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Práce se zásadami zabezpečení v Azure Security Center](../security-center/tutorial-security-policy.md)

- [Vytváření a Správa zásad pro vymáhání dodržování předpisů](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint](../governance/blueprints/overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Udržování zabezpečených konfigurací pro služby Azure

**Doprovodné** materiály: nepoužitelné;  Toto doporučení je určené pro výpočetní prostředky.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Provádění pravidelné simulace útoku

**Pokyny:** Podle potřeby provádějte testování průniku nebo aktivity červeného týmu na svých prostředcích Azure a zajistěte nápravu všech kritických zjištění v oblasti zabezpečení.
Postupujte podle pravidel zapojení testování průniku cloudových služeb Microsoftu (Microsoft Cloud Penetration Testing), abyste měli jistotu, že testy průniku neporušují zásady Microsoftu. Využijte strategii Microsoftu a spuštění červeného týmu a testování průniku na živém webu na cloudové infrastruktuře, službách a aplikacích spravovaný Microsoftem.

- [Testování průniku v Azure](../security/fundamentals/pen-testing.md)

- [Pravidla zapojení testování průniku](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Červený tým cloudových služeb Microsoftu](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

## <a name="governance-and-strategy"></a>Zásady správného řízení a strategie

*Další informace najdete v tématu [Azure Security Benchmark: zásady správného řízení a strategie](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definování strategie správy prostředků a ochrany dat 

**Pokyny:** Zadokumentujte si a dále sdělujte jasnou strategii pro nepřetržité monitorování a ochranu systémů a dat. Stanovte priority zjišťování, hodnocení, ochrany a monitorování nejdůležitějších podnikových dat a systémů. 

Tato strategie by měla zahrnovat zdokumentované pokyny, zásady a standardy pro následující prvky: 

-   Standard klasifikace dat v souladu s obchodními riziky

-   Přehled organizace zabezpečení o rizicích a inventáři prostředků 

-   Schválení služeb Azure k používání provedené organizací zabezpečení 

-   Zabezpečení prostředků po dobu jejich životního cyklu

-   Požadovaná strategie řízení přístupu v souladu s klasifikací dat organizace

-   Použití funkcí pro ochranu dat nativních pro Azure i třetích stran

-   Požadavky na šifrování dat pro případy použití v přenosech a u neaktivních uložených dat

-   Vhodné kryptografické standardy

Další informace najdete v následujících referenčních materiálech:
- [Doporučení architektury zabezpečení Azure – úložiště, data a šifrování](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Základy zabezpečení Azure – zabezpečení, šifrování a úložiště dat Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework – osvědčené postupy zabezpečení a šifrování dat Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure Security Benchmark – správa prostředků](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Azure Security Benchmark – ochrana dat](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definování strategie segmentace podniku 

**Pokyny:** Zřiďte celopodnikovou strategii za účelem segmentace přístupu k prostředkům pomocí kombinace identity, sítě, aplikace, předplatného, skupiny pro správu a dalších řídicích prvků.

Pečlivě vyvažte nutnost oddělení zabezpečení s nutností povolit každodenní provoz systémů, které musí vzájemně komunikovat a pracovat s daty.

Zajistěte, aby se strategie segmentace implementovala konzistentně ve všech typech řídicích prvků včetně zabezpečení sítě, modelů identit a přístupu a modelů oprávnění/přístupu aplikací a řídicích prvků pro lidské procesy.

- [Pokyny ke strategii segmentace v Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Pokyny ke strategii segmentace v Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Soulad segmentace sítě a strategie segmentace podniku](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definování strategie správy stavu zabezpečení

**Pokyny:** Nepřetržitě měřte a zmírňujte rizika pro jednotlivé prostředky a prostředí, ve kterém jsou hostovány. Stanovte prioritu prostředků s vysokou hodnotou a míst nejvíce vystavených útokům, jako jsou publikované aplikace, body sítě pro příchozí a odchozí přenosy dat, koncové body uživatelů a správců atd.

- [Azure Security Benchmark – správa stavu a ohrožení zabezpečení](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Soulad rolí a odpovědností organizace

**Pokyny:** Je nutné zadokumentovat a dále sdělovat jasnou strategii pro role a odpovědnosti ve vaší organizaci zabezpečení. Stanovte prioritu poskytování jasné odpovědnosti při rozhodování o zabezpečení, zároveň vyškolte všechny uživatele na model sdílené odpovědnosti a vyškolte technické týmy na technologii pro zabezpečení cloudu.

- [Osvědčený postup zabezpečení Azure 1 – Lidé: školení týmů v oblasti zabezpečení cloudu](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Osvědčený postup zabezpečení Azure 2 – Lidé: školení týmů na technologii zabezpečení cloudu](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Osvědčený postup zabezpečení Azure 3 – Proces: přiřazení odpovědnosti za rozhodování o zabezpečení cloudu](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definování strategie zabezpečení sítě

**Pokyny:** Zřiďte postup pro zabezpečení sítě Azure jako součást celkové strategie řízení přístupu zabezpečení vaší organizace.  

Tato strategie by měla zahrnovat zdokumentované pokyny, zásady a standardy pro následující prvky: 

-   Centralizovaná správa sítě a odpovědnost za zabezpečení

-   Model segmentace virtuální sítě v souladu se strategií segmentace podniku

-   Strategie náprav v různých situacích ohrožení a útoků

-   Strategie pro přechodový bod na internet a příchozí a odchozí přenosy

-   Strategie vzájemného propojení hybridního cloudu a místního připojení

-   Aktuální artefakty zabezpečení sítě (například síťové diagramy, referenční síťová architektura)

Další informace najdete v následujících referenčních materiálech:
- [Osvědčený postup zabezpečení Azure 11 – Architektura: jednotná strategie zabezpečení](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark – zabezpečení sítě](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Přehled zabezpečení sítě v Azure](../security/fundamentals/network-overview.md)

- [Strategie architektury podnikové sítě](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definování strategie identity a privilegovaného přístupu

**Pokyny:** Zřiďte postupy pro identitu a privilegovaný přístup Azure jako součást celkové strategie řízení přístupu zabezpečení vaší organizace.  

Tato strategie by měla zahrnovat zdokumentované pokyny, zásady a standardy pro následující prvky: 

-   Centralizovaný systém identit a ověřování a jeho vzájemné propojení s dalšími interními a externími systémy identit

-   Metody silného ověřování v různých případech a podmínkách použití

-   Ochrana vysoce privilegovaných uživatelů

-   Monitorování a zpracování anomálních aktivit uživatelů  

-   Uživatelská identita a kontrola přístupu a proces odsouhlasení

Další informace najdete v následujících referenčních materiálech:

- [Azure Security Benchmark – správa identit](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure Security Benchmark – privilegovaný přístup](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Osvědčený postup zabezpečení Azure 11 – Architektura: jednotná strategie zabezpečení](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Přehled zabezpečení správy identit v Azure](../security/fundamentals/identity-management-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definování strategie protokolování a reakcí na hrozby

**Pokyny:** Zřiďte strategii protokolování a reakcí na hrozby, která umožní rychle zjistit a zneškodnit hrozby při současném splnění požadavků na dodržování předpisů. Stanovte prioritu poskytování vysoce kvalitních upozornění a bezproblémového prostředí analytikům, aby se mohli soustředit na hrozby, nikoli na integraci a ruční kroky. 

Tato strategie by měla zahrnovat zdokumentované pokyny, zásady a standardy pro následující prvky: 

-   Role a odpovědnosti organizace v oblasti operací zabezpečení (SecOps) 

-   Dobře definovaný proces reakce na incidenty, který je v souladu s NIST nebo jinou oborovou architekturou 

-   Shromažďování a uchovávání protokolů pro podporu detekce hrozeb, reakce na incidenty a požadavků na dodržování předpisů

-   Centralizované zobrazení a korelační informace o hrozbách, používání systému SIEM (správa akcí a informací o zabezpečení), nativních možností Azure a dalších zdrojů 

-   Plán komunikace a oznámení pro vaše zákazníky, dodavatele a veřejné partnery

-   Použití nativních platforem Azure a platforem třetích stran pro zpracování incidentů, jako je například protokolování a detekce hrozeb, forenzní zkoumání a náprava po útocích či jejich zneškodnění

-   Procesy pro zpracování incidentů a aktivity po incidentech, jako je poučení a uchovávání důkazů

Další informace najdete v následujících referenčních materiálech:

- [Azure Security Benchmark – protokolování a detekce hrozeb](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure Security Benchmark – reakce na incidenty](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Osvědčený postup zabezpečení Azure 4 – Proces: aktualizace procesů reakcí na incidenty pro cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Architektura přechodu na Azure a průvodce rozhodováním o protokolování a vytváření sestav](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Škálování, správa a monitorování Azure na podnikové úrovni](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="next-steps"></a>Další kroky

- Další informace najdete v článku [Přehled Azure Security Benchmark v2](/azure/security/benchmarks/overview).
- Další informace o [základních úrovních zabezpečení Azure](/azure/security/benchmarks/security-baselines-overview)
