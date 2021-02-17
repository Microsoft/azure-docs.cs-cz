---
title: Základní hodnoty zabezpečení Azure pro Azure Managed Applications
description: Základní Azure Managed Applications zabezpečení poskytuje postupy a prostředky pro implementaci doporučení zabezpečení uvedených v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: managed-applications
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 2a5c31270f18c2e6149d93fa522818704b9747d8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588615"
---
# <a name="azure-security-baseline-for-azure-managed-applications"></a>Základní hodnoty zabezpečení Azure pro Azure Managed Applications

Tato základní hodnota zabezpečení platí pro Azure Managed Applications pokynů od [zabezpečení Azure Security test 2,0](../../security/benchmarks/overview.md) . Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure. Obsah je seskupen podle **řízení zabezpečení** definovaného srovnávacím testem zabezpečení Azure a souvisejících pokynů, které se vztahují na Azure Managed Applications. **Ovládací prvky** , které se nevztahují k Azure Managed Applications byly vyloučeny.

Pokud chcete zjistit, jak Azure Managed Applications kompletně mapuje srovnávací test zabezpečení Azure, přečtěte si [celý soubor mapování standardních hodnot Azure Managed Applications Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [Azure Security Benchmark: zabezpečení sítě](../../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: zjednodušení pravidel zabezpečení sítě

**Pokyny**: pomocí značek služeb Azure Virtual Network můžete definovat řízení přístupu k síti u skupin zabezpečení sítě nebo Azure firewall nakonfigurovaných pro prostředky spravované aplikace Azure. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (například: AzureResourceManager) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

- [Použití značek Azure Resource Manager služeb](../../virtual-network/service-tags-overview.md#available-service-tags)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="identity-management"></a>Správa identit

*Další informace najdete v tématu [Azure Security Benchmark: správa identit](../../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardizace Azure Active Directory jako centrálního systému pro identifikaci a ověřování

**Pokyny**: Azure Managed Applications jako výchozí službu pro správu identit a přístupu používá Azure Active Directory (Azure AD). Standardizace v Azure AD, aby bylo možné řídit identitu a správu přístupu vaší organizace.

Azure poskytuje následující předdefinované role Azure pro autorizaci přístupu ke spravovaným aplikacím pomocí Azure AD a OAuth:

- Role Přispěvatel spravovaných aplikací: umožňuje vytváření prostředků spravovaných aplikací.

- Role operátora spravované aplikace: umožňuje číst a provádět akce s prostředky spravovaných aplikací.

- Čtecí modul spravovaných aplikací: umožňuje číst prostředky ve spravované aplikaci a vyžadovat přístup JIT.

Další informace najdete v následujících referenčních materiálech:

- [Role Přispěvatel spravovaných aplikací](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Role operátora spravované aplikace](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Čtecí modul spravovaných aplikací](../../role-based-access-control/built-in-roles.md#managed-applications-reader)

- [Architektura tenantů v Azure Active Directory](../../active-directory/develop/single-and-multi-tenant-apps.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Zabezpečená a automatická správa identit aplikací

**Doprovodné** materiály: Používejte identity spravované Azure pro udělení oprávnění vašim spravovaným aplikacím. Místo vytvoření výkonnějšího lidského účtu pro přístup k prostředkům nebo jejich vykonání za účelem omezení nutnosti spravovat další přihlašovací údaje se doporučuje použít funkci Azure Managed identity. K nativně ověření pro jiné služby a prostředky Azure, které podporují ověřování Azure AD, se dá také službě Azure Managed Applications přiřadit samotnou spravovanou identitu. To může být užitečné, pokud chcete pro Azure Key Vault při načítání tajných kódů povolit snadný přístup z Azure Managed Applications. Při použití spravovaných identit se identita spravuje na platformě Azure a nevyžaduje zřizování ani střídání tajných kódů.

Azure Managed Applications podporuje vaši aplikaci pro udělení dvou typů identit:

- Identita přiřazená systémem je svázána s vaším prostředkem konfigurace. Odstraní se, pokud je prostředek konfigurace odstraněný. Prostředek konfigurace může mít pouze jednu identitu přiřazenou systémem.

- Identita přiřazená uživatelem je samostatný prostředek Azure, který se dá přiřadit k prostředku konfigurace. Prostředek konfigurace může mít několik uživatelsky přiřazených identit.

Pokud se spravované identity nedají využít, vytvořte instanční objekt s omezenými oprávněními na úrovni prostředků spravované aplikace Azure. Konfigurujte tyto instanční objekty pomocí přihlašovacích údajů k certifikátu a vraťte se pouze k klientským tajným klíčům. V obou případech se Azure Key Vault dají použít ve spojení se spravovanými identitami Azure, takže běhové prostředí (například funkce Azure) může přihlašovací údaje získat z trezoru klíčů.

- [Jak používat spravované identity pro Azure Managed Applications](publish-managed-identity.md)

- [Spravované identity Azure](../../active-directory/managed-identities-azure-resources/overview.md)

- [Instanční objekt Azure](/powershell/azure/create-azure-service-principal-azureps) 

- [Použití Azure Key Vault k registraci objektu zabezpečení](../../key-vault/general/authentication.md#app-identity-and-security-principals)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Použití jednotného přihlašování (SSO) Azure AD pro přístup k aplikacím

**Doprovodné** materiály: Azure Managed Applications použít Azure Active Directory k zajištění správy identit a přístupu k prostředkům Azure, cloudovým aplikacím a místním aplikacím. Patří sem podnikové identity, jako jsou zaměstnanci, i externí identity, jako jsou partneři a dodavatelé. Díky tomu je možné použít jednotné přihlašování (SSO) ke správě a zabezpečení přístupu k datům a prostředkům vaší organizace v místním prostředí a v cloudu. Připojte všechny své uživatele, aplikace a zařízení k Azure AD, ať máte bezproblémový, zabezpečený přístup a lepší viditelnost a kontrolu.

- [Principy jednotného přihlašování k aplikacím pomocí Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Použití řídicích prvků silného ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: Azure Managed Applications používá Azure Active Directory, které podporují ovládací prvky silného ověřování prostřednictvím vícefaktorového ověřování a metod se silnými hesly.
- Vícefaktorové ověřování – povolte vícefaktorové ověřování Azure AD a sledujte Azure Security Center doporučení pro správu identit a přístupu pro některé osvědčené postupy při nastavení vícefaktorového ověřování. Vícefaktorové ověřování lze vyhovět všem uživatelům nebo na úrovni jednotlivých uživatelů, a to na základě podmínek přihlášení a rizikových faktorů.
- Ověřování bez hesla – k dispozici jsou tři možnosti ověřování bez hesla: Windows Hello pro firmy, aplikace Microsoft Authenticator a místní metody ověřování, jako jsou čipové karty.

Pro správce a privilegované uživatele zkontrolujte, že se používá nejvyšší úroveň silného ověřování.

- [Postup povolení vícefaktorového ověřování v Azure](../../active-directory/authentication/howto-mfa-getstarted.md) 

- [Seznámení s možnostmi ověřování bez hesla pro Azure Active Directory](../../active-directory/authentication/concept-authentication-passwordless.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorování a upozornění na anomálie účtů

**Doprovodné** materiály: Azure Managed Applications je integrována s Azure Active Directory v, která poskytuje následující zdroje dat:
- Přihlášení – Sestava přihlášení poskytuje informace o využití spravovaných aplikací a aktivitách přihlašování uživatelů.
- Protokoly auditu – Zajišťuje sledovatelnost prostřednictvím protokolů všech změn provedených různými funkcemi v rámci Azure AD. Mezi příklady protokolů auditu patří změny jakýchkoli prostředků v rámci Azure AD, jako jsou přidání nebo odebrání uživatelů, aplikací, skupin, rolí nebo zásad.
- Riziková přihlášení – Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.
- Uživatelé označení příznakem rizika – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený.

Tyto zdroje dat je možné integrovat s Azure Monitor, s Sentinelou v Azure a systémy SIEM třetích stran.

Azure Security Center může také upozorňovat na určité podezřelé aktivity, jako jsou nadměrný počet neúspěšných pokusů o ověření nebo zastaralé účty v předplatném.

Rozšířená ochrana před internetovými útoky (ATP) v Azure je řešení zabezpečení, které dokáže na základě signálů Active Directory identifikovat, detekovat a vyšetřovat pokročilé hrozby, ohrožené identity a škodlivé činnosti vnitřních účastníků.

- [Sestavy aktivit auditu v Azure Active Directory](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Zobrazení rizikových přihlášení Azure AD](../../active-directory/identity-protection/overview-identity-protection.md)

- [Monitorování identit a aktivit přístupu uživatelů ve službě Azure Security Center](../../security-center/security-center-identity-access.md)

- [Integrace protokolů aktivit Azure do služby Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Omezení přístupu k prostředkům Azure na základě podmínek

**Doprovodné** materiály: Azure Managed Applications podporuje podmíněný přístup Azure AD pro přesnější řízení přístupu na základě uživatelem definovaných podmínek, jako je třeba přihlášení uživatelů z určitých ROZSAHŮ IP adres, pro přihlášení bude potřeba použít vícefaktorové ověřování. Podrobné zásady správy relace ověřování se také dají použít pro různé případy použití.

- [Přehled podmíněného přístupu Azure](../../active-directory/conditional-access/overview.md) 

- [Obecné zásady podmíněného přístupu](../../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Konfigurace správy relací ověřování pomocí podmíněného přístupu](../../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

## <a name="privileged-access"></a>Privilegovaný přístup

*Další informace najdete v tématu [Azure Security Benchmark: privilegovaný přístup](../../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Ochrana a omezení vysoce privilegovaných uživatelů

**Pokyny**: Azure Managed Applications používá pro identitu a přístup Azure Active Directory (Azure AD). Nejdůležitější předdefinované role jsou globální správce a správce privilegovaných rolí, protože uživatelé přiřazení k těmto dvěma rolím můžou delegovat role správce:
- Globální správce: uživatelé s touto rolí mají přístup ke všem funkcím pro správu v Azure AD a službám, které používají identity Azure AD.
- Správce privilegovaných rolí: uživatelé s touto rolí můžou spravovat přiřazení rolí v Azure AD i v Azure AD Privileged Identity Management (PIM). Tato role navíc umožňuje správu všech aspektů PIM a administrativních jednotek.

Poznámka: Pokud používáte vlastní role s přiřazenými konkrétními privilegovanými oprávněními, můžete mít i jiné kritické role, které je potřeba řídit. A můžete také chtít použít podobné ovládací prvky pro účet správce důležitých obchodních prostředků.

S využitím služby Azure AD Privileged Identity Management (PIM) můžete povolit privilegovaný přístup podle potřeby (JIT) k prostředkům Azure a Azure AD. JIT uděluje dočasná oprávnění k provádění privilegovaných úloh, pouze když je uživatelé potřebují. PIM může také generovat upozornění zabezpečení v případě podezřelých nebo nebezpečných aktivit ve vaší organizaci Azure AD.

- [Oprávnění role správce v Azure AD](../../active-directory/roles/permissions-reference.md)

- [Používání upozornění zabezpečení služby Azure Privileged Identity Management](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Zabezpečení privilegovaného přístupu pro hybridní a cloudová nasazení v Azure AD](../../active-directory/roles/security-planning.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Omezení přístupu pro správu k nejdůležitějším podnikovým systémům

**Doprovodné** materiály: Azure Managed Applications využívá Azure RBAC k izolaci přístupu k důležitým podnikovým systémům tím, že omezí, kterým účtům se udělí privilegovaný přístup k předplatným a skupinám pro správu, ve kterých jsou.

Ujistěte se, že také omezíte přístup k systémům pro správu, identitu a zabezpečení, které mají přístup pro správu k vašemu podnikovému přístupu, jako jsou Doména služby Active Directory Controllers (DCs), nástroje zabezpečení a nástroje pro správu systému s agenty nainstalovanými v důležitých podnikových systémech. Útočníci, kteří tyto systémy pro správu a zabezpečení ohrožují, si je můžou hned weaponize, aby vyzrazeni důležité obchodní prostředky.

Všechny typy ovládacích prvků přístupu by se měly zarovnávat na strategii segmentace vaší organizace, aby se zajistilo jednotné řízení přístupu.

- [Komponenty a referenční model Azure](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Přístup ke skupině pro správu](../../governance/management-groups/overview.md#management-group-access)

- [Správci předplatného Azure](../../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Pravidelná kontrola a sjednocování přístupu uživatelů

**Pokyny**: pravidelně kontrolujte uživatelské účty a přiřazení přístupu, abyste měli jistotu, že účty a jejich úroveň přístupu jsou platné.

Azure Managed Applications používá ke správě svých prostředků účty Azure Active Directory (AAD), pravidelně kontrolují uživatelské účty a přiřazení přístupu, aby bylo zajištěno, že jsou účty a jejich přístup platné. Kontroly přístupu Azure AD můžete použít ke kontrole členství ve skupinách, přístupu k podnikovým aplikacím a přiřazování rolí. Vytváření sestav Azure AD může poskytovat protokoly, které vám pomůžou zjistit zastaralé účty. K usnadnění procesu kontroly můžete použít také Azure AD Privileged Identity Management k vytvoření pracovního postupu sestavy kontroly přístupu.

Kromě toho je možné Azure Privileged Identity Management nakonfigurovat tak, aby výstrahu při vytváření nadměrného počtu účtů správců a identifikaci účtů správce, které jsou zastaralé nebo nesprávně nakonfigurované.

Poznámka: některé služby Azure podporují místní uživatele a role, které se nespravují prostřednictvím služby Azure AD. Tyto uživatele budete muset spravovat samostatně.

- [Vytvoření kontroly přístupu pro role prostředků Azure v Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Používání kontrol přístupu a identit Azure AD](../../active-directory/governance/access-reviews-overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Nastavení nouzového přístupu v Azure AD

**Doprovodné** materiály: Azure Managed Applications používá ke správě prostředků Azure Active Directory. Pokud se chcete vyhnout náhodnému uzamčení vaší organizace Azure AD, nastavte účet pro nouzový přístup pro přístup, když nebudete moct použít normální účty pro správu. Účty pro nouzový přístup jsou obvykle vysoce privilegované a neměly by se přiřazovat konkrétním jednotlivcům. Jejich použití je omezené na nouzové nebo kritické situace, kdy se nedají použít běžné účty pro správu.

Měli byste zajistit, aby přihlašovací údaje (například heslo, certifikát nebo čipová karta) pro účty pro nouzový přístup byly zabezpečené a aby o nich věděli jenom ti, kteří jsou oprávněni je použít jenom v případě nouze.

- [Správa účtů pro nouzový přístup v Azure AD](../../active-directory/roles/security-emergency-access.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatizace správy nároků 

**Doprovodné** materiály: Azure Managed Applications je integrována s Azure Active Directory ke správě prostředků. Pomocí funkcí správy opravňujících ke službě Azure AD můžete automatizovat pracovní postupy pro žádosti o přístup, včetně přiřazení přístupu, revizí a vypršení platnosti. Podporuje se také duální nebo vícenásobné schválení.

- [Co jsou kontroly přístupu Azure AD](../../active-directory/governance/access-reviews-overview.md)

- [Co je Správa nároků Azure AD](../../active-directory/governance/entitlement-management-overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Použití pracovních stanic s privilegovaným přístupem

**Pokyny:** Zabezpečené, izolované pracovní stanice jsou nesmírně důležité pro zabezpečení citlivých rolí, jako jsou správci, vývojáři a obsluha nejdůležitějších služeb. Pro úlohy správy související s vašimi spravovanými aplikacemi Používejte vysoce zabezpečené uživatelské pracovní stanice nebo Azure bastionu. K nasazení zabezpečené a spravované uživatelské pracovní stanice pro úlohy správy použijte Azure Active Directory, Microsoft Defender Advanced Threat Protection (ATP) nebo Microsoft Intune. Zabezpečené pracovní stanice se dají centrálně spravovat, aby byla vynucena zabezpečená konfigurace, včetně silného ověřování, základních úrovní softwaru a hardwaru a omezeného logického a síťového přístupu.

- [Pochopení pracovních stanic s privilegovaným přístupem](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Nasazení pracovní stanice s privilegovaným přístupem](/security/compass/privileged-access-deployment)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Využívání správy jen v potřebném rozsahu (princip nejnižší úrovně nutných oprávnění) 

**Pokyny**: Azure Managed Applications je integrován s řízením přístupu na základě role (RBAC) v Azure za účelem správy prostředků. Azure RBAC umožňuje spravovat přístup k prostředkům Azure prostřednictvím přiřazení rolí. Tyto role můžete přiřadit uživatelům, skupinám instančních objektů a spravovaným identitám. Pro určité prostředky existují předdefinované role, které je možné inventarizovat nebo dotazovat pomocí nástrojů, jako jsou Azure CLI, Azure PowerShell nebo Azure Portal. Oprávnění k prostředkům přiřazená prostřednictvím Azure RBAC by vždy měla být omezená pouze na to, co konkrétní role vyžadují. Tento přístup doplňuje přístup založený na přístupu podle potřeby (JIT) ve službě Azure AD Privileged Identity Management (PIM) a měl by se pravidelně kontrolovat.

Pokud je to možné, použijte předdefinované role k přidělení oprávnění a v případě potřeby pouze vlastní role.

Azure poskytuje následující předdefinované role Azure pro autorizaci přístupu ke spravovaným aplikacím pomocí Azure AD a OAuth:

- Role Přispěvatel spravovaných aplikací: umožňuje vytváření prostředků spravovaných aplikací.

- Role operátora spravované aplikace: umožňuje číst a provádět akce s prostředky spravovaných aplikací.

- Čtecí modul spravovaných aplikací: umožňuje číst prostředky ve spravované aplikaci a vyžadovat přístup JIT.

Další informace najdete v následujících referenčních materiálech:

- [Role Přispěvatel spravovaných aplikací](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Role operátora spravované aplikace](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Čtecí modul spravovaných aplikací](../../role-based-access-control/built-in-roles.md#managed-applications-reader)

- [Co je řízení přístupu na základě role v Azure (Azure RBAC)](../../role-based-access-control/overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: výběr procesu schválení pro podporu Microsoftu  

**Pokyny**: Implementujte schvalovací proces organizace pro scénáře podpory, kde Microsoft může potřebovat přístup k datům spravovaných aplikací Azure. Customer Lockbox není aktuálně k dispozici pro scénáře spravované aplikace.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [Azure Security Benchmark: ochrana dat](../../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Ochrana citlivých dat

**Doprovodné** materiály: pro zavedení šifrování s vlastními klíči můžete využít vlastní účet úložiště pro ukládání konfiguračních souborů spravované aplikace.

- [Ochrana dat spravovaných konfiguračních souborů pomocí vlastního úložiště](./publish-service-catalog-app.md?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Šifrování citlivých neaktivních uložených dat

**Doprovodné** materiály: Definice spravovaných aplikací, které definují vaši aplikaci a její prostředky, mohou být uloženy ve vlastních účtech úložiště, které lze konfigurovat pomocí šifrovacích klíčů spravovaných zákazníkem.

Ve scénářích, kdy nechcete využít vlastní úložiště pro definice spravovaných aplikací, poskytuje Azure standardně šifrování dat v klidovém stavu.

- [Přineste si vlastní úložiště pro definice spravovaných aplikací](./publish-service-catalog-app.md?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [Principy šifrování neaktivních uložených dat v Azure](../../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Postup konfigurace šifrovacích klíčů spravovaných zákazníkem](../../storage/common/customer-managed-keys-configure-key-vault.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Sdílené

## <a name="asset-management"></a>Správa aktiv

*Další informace najdete v tématu [Azure Security Benchmark: správa prostředků](../../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Zajištění přehledu bezpečnostního týmu o rizicích pro prostředky

**Pokyny:** Zajistěte udělení oprávnění Čtenář zabezpečení ve vašem tenantovi Azure a vašich předplatných bezpečnostním týmům, aby mohly monitorovat bezpečnostní rizika s využitím služby Azure Security Center. 

V závislosti na nastavení struktury odpovědnosti bezpečnostních týmů může být monitorování bezpečnostních rizik odpovědností centrálního nebo místního bezpečnostního týmu. Přehledy zabezpečení a bezpečnostní rizika se však v rámci organizace musí vždy agregovat centrálně. 

Oprávnění Čtenář zabezpečení je možné přidělit pro celého tenanta (kořenová skupina pro správu) nebo je vymezit na konkrétní skupiny pro správu nebo konkrétní předplatná. 

Poznámka: K získání přehledu o úlohách a službách se můžou vyžadovat další oprávnění. 

- [Přehled role Čtenář zabezpečení](../../role-based-access-control/built-in-roles.md#security-reader)

- [Přehled skupin pro správu Azure](../../governance/management-groups/overview.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Zajištění přístupu k inventáři a metadatům prostředků pro bezpečnostní tým

**Doprovodné** materiály: použití značek pro prostředky Azure, skupiny prostředků a předplatná k jejich logickému uspořádání do taxonomie. Každá značka se skládá z názvu a páru hodnot. Můžete například použít název Prostředí a hodnotu Produkční na všechny prostředky v produkčním prostředí.

Značky, které jsou k dispozici v době vytváření spravované aplikace, se vztahují také na spravovanou skupinu prostředků. Vydavatel vaší aplikace může po nasazení poskytnout své vlastní dodatečné označení spravovaných prostředků.

- [Značky podle prvku uživatelského rozhraní Application spravovaného prostředků](microsoft-common-tagsbyresource.md)

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource Graphu](../../governance/resource-graph/first-query-portal.md) 

- [Další informace o označování prostředků najdete v průvodci pro pojmenování a označení prostředku.](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Používání jenom schválených služeb Azure

**Doprovodné** materiály: Azure Managed Applications podporuje nasazení na základě Azure Resource Manager a vynucení konfigurace pomocí Azure Policy. Pomocí Azure Policy můžete auditovat a omezovat služby, které můžou uživatelé zřídit ve vašem prostředí. Pomocí Azure Resource Graphu se můžete dotazovat na prostředky v rámci jejich předplatných a zjišťovat je. Pomocí Azure Monitoru můžete také vytvořit pravidla pro aktivaci upozornění při zjištění neschválené služby.

- [Jak nakonfigurovat a spravovat Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../../governance/policy/samples/built-in-policies.md#general)

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource Graphu](../../governance/resource-graph/first-query-portal.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Zajištění zabezpečení správy životního cyklu prostředků

**Pokyny**: prostředky spravovaných aplikací a jejich připojená spravovaná skupina prostředků lze odstranit odstraněním prostředku spravované aplikace. Pokud se prostředek spravované aplikace odstraní, odstraní se i skupina spravovaných prostředků a její obsah. Další možnosti životního cyklu jsou určené vydavatelem aplikace, kde můžou spotřebiteli poskytnout dodatečná práva v průběhu životního cyklu základních spravovaných prostředků prostřednictvím povolených akcí. Obraťte se prosím na vydavatele spravované aplikace, kde se prostředky spravují pro příjemce.

- [Vyčištění prostředků spravovaných aplikací](./tutorial-create-managed-app-with-custom-provider.md?tabs=azurecli-interactive#clean-up-resources)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>DOP. 5: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: pomocí podmíněného přístupu Azure omezte schopnost uživatelů pracovat se správcem prostředků Azure tak, že nakonfigurujete "blokování přístupu" pro aplikaci "Microsoft Azure Management".

- [Jak nakonfigurovat podmíněný přístup k blokování přístupu k Azure Resource Manageru](../../role-based-access-control/conditional-access-azure-management.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

## <a name="logging-and-threat-detection"></a>Protokolování a detekce hrozeb

*Další informace najdete v tématu [Azure Security Benchmark: protokolování a detekce hrozeb](../../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Povolení detekce hrozeb pro správu identit a přístupu v Azure

**Doprovodné** materiály: Azure AD poskytuje následující protokoly uživatelů, které se dají zobrazit v sestavách Azure AD nebo které jsou integrované s Azure monitor, Azure Sentinel nebo jiné nástroje Siem/Monitoring pro výkonnější monitorování a analytické případy použití: přihlášení – sestava přihlášení nabízí informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů.
Protokoly auditu – Zajišťuje sledovatelnost prostřednictvím protokolů všech změn provedených různými funkcemi v rámci Azure AD. Mezi příklady protokolů auditu patří změny jakýchkoli prostředků v rámci Azure AD, jako jsou přidání nebo odebrání uživatelů, aplikací, skupin, rolí nebo zásad.
Riziková přihlášení – Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.
Uživatelé označení příznakem rizika – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený.

Azure Security Center může také upozorňovat na určité podezřelé aktivity, jako jsou nadměrný počet neúspěšných pokusů o ověření nebo zastaralé účty v předplatném. Kromě základního monitorování hygieny zabezpečení může modul ochrany před hrozbami služby Azure Security Center také shromažďovat podrobnější upozornění zabezpečení z jednotlivých výpočetních prostředků Azure (virtuální počítače, kontejnery, App Service), datových prostředků (databáze SQL a úložiště) a vrstev služeb Azure. Tato funkce poskytuje přehled o anomáliích účtů v jednotlivých prostředcích.

- [Sestavy aktivit auditu v Azure Active Directory](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Povolení ochrany identit Azure](../../active-directory/identity-protection/overview-identity-protection.md)

- [Ochrana před hrozbami v Azure Security Center](../../security-center/azure-defender.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Povolení protokolování pro prostředky Azure

**Doprovodné** materiály: protokoly aktivit, které jsou automaticky k dispozici, obsahují všechny operace zápisu (Put, post, DELETE) pro prostředky spravované aplikace s výjimkou operací čtení (Get). Protokoly aktivit se dají použít k nalezení chyby při odstraňování potíží nebo sledování, jak uživatel ve vaší organizaci změnil prostředek.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md) 

- [Principy protokolování a různých typů protokolů v Azure](../../azure-monitor/essentials/platform-logs-overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizace správy a analýz protokolu zabezpečení

**Doprovodné** materiály: centralizaci protokolování, úložiště a analýzy pro povolení korelace. U každého zdroje protokolu se ujistěte, že jste přiřadili vlastníka dat, pokyny pro přístup, umístění úložiště, jaké nástroje se používají ke zpracování a přístupu k datům a k požadavkům na uchovávání dat.
Ujistěte se, že Integrujte protokoly aktivit Azure do svého centrálního protokolování. Ingestování protokolů prostřednictvím Azure Monitor k agregaci dat zabezpečení generovaných zařízeními koncového bodu, síťovými prostředky a dalšími systémy zabezpečení. V Azure Monitor použijte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty Azure Storage pro dlouhodobé a archivační úložiště.

Kromě toho povolte a zaveďte data do Azure Sentinel nebo SIEM třetí strany.

Řada organizací zvolí použití Azure Sentinel pro "horká" data, která se používají často a Azure Storage pro "studená" data, která se používají méně často.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md)

- [Jak připojit Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurace uchovávání úložiště protokolů

**Pokyny**: Ujistěte se, že všechny účty úložiště nebo pracovní prostory Log Analytics používané pro ukládání protokolů vytvořených prostředky spravované aplikace mají dobu uchování protokolu nastavenou v souladu s předpisy pro dodržování předpisů vaší organizace.
V Azure Monitor můžete nastavit dobu uchování pracovního prostoru Log Analytics podle předpisů pro dodržování předpisů vaší organizace. Používejte Azure Storage, Data Lake nebo Log Analytics účty pracovního prostoru pro dlouhodobé a archivační úložiště.

- [Jak nakonfigurovat dobu uchování Log Analytics pracovního prostoru](../../azure-monitor/logs/manage-cost-storage.md)

- [Ukládání protokolů prostředků v Azure Storagem účtu](../../azure-monitor/platform/resource-logs.md#send-to-azure-storage)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [Azure Security Benchmark: reakce na incidenty](../../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Příprava – aktualizace procesu reakce na incidenty pro Azure

**Pokyny:** Zajistěte, aby vaše organizace měla procesy, jak reagovat na incidenty zabezpečení, aby tyto procesy aktualizovala pro Azure a pravidelně je testovala za účelem zajištění připravenosti.

- [Implementace zabezpečení v podnikovém prostředí](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Referenční příručka k reakcím na incidenty](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Příprava – nastavení oznámení o incidentu

**Pokyny:** Nastavte kontaktní informace pro incidenty zabezpečení ve službě Azure Security Center. Tyto kontaktní informace používá společnost Microsoft k tomu, aby vás kontaktovala v případě, že služba MSRC (Microsoft Security Response Center) zjistí, že s vašimi daty někdo nezákonně nebo neoprávněně pracoval. Máte také možnost přizpůsobit upozornění na incidenty a oznámení o incidentech v různých službách Azure v závislosti na tom, jak potřebujete na incidenty reagovat. 

- [Nastavení kontaktu ve službě Azure Security Center](../../security-center/security-center-provide-security-contact-details.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Detekce a analýza – vytvoření incidentů na základě upozornění s vysokou kvalitou

**Pokyny:** Zajistěte, abyste měli proces pro vytváření vysoce kvalitních upozornění a měření jejich kvality. To vám umožní poučit se z minulých incidentů a stanovit priority upozornění pro analytiky, aby nemuseli ztrácet čas s falešně pozitivními výsledky. 

Vysoce kvalitní upozornění se dají vytvářet na základě zkušeností z minulých incidentů, ověřených zdrojů komunity a nástrojů určených k vygenerování a vyčištění upozornění spojením a sladěním různých zdrojů signálu. 

Azure Security Center poskytuje vysoce kvalitní výstrahy napříč mnoha prostředky Azure. Pomocí datového konektoru ASC můžete upozornění streamovat do služby Azure Sentinel. Azure Sentinel umožňuje vytvářet rozšířená pravidla upozornění, která automaticky generují incidenty pro účely šetření. 

Pomocí funkce exportu můžete upozornění a doporučení služby Azure Security Center exportovat, abyste mohli lépe identifikovat rizika pro prostředky Azure. Upozornění a doporučení můžete exportovat ručně nebo průběžně a nepřetržitě.

- [Konfigurace exportu](../../security-center/continuous-export.md)

- [Streamování upozornění do služby Azure Sentinel](../../sentinel/connect-azure-security-center.md)

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

- [Snímek disku počítače s Windows](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Snímek disku počítače s Linuxem](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Shromažďování výpisu paměti a diagnostických informací ze strany podpory Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Vyšetřování incidentů s využitím služby Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Detekce a analýza – stanovení priorit incidentů

**Pokyny:** Poskytněte analytikům kontext, aby věděli, na jaké incidenty se zaměřit jako první v závislosti na závažnosti upozornění a citlivosti prostředků. 

Azure Security Center jednotlivým upozorněním přiřazuje závažnost, která vám pomůže určit, jaká upozornění by se měla vyšetřit jako první. Závažnost je založena na tom, jak se Security Center ve vyhledávání, nebo na analýze, která se používá k vystavení výstrahy, a také na úrovni spolehlivosti, u které došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Kromě toho můžete prostředky označit pomocí značek a vytvořit systém vytváření názvů, který vám pomůže identifikovat a kategorizovat prostředky Azure, zejména prostředky zpracovávající citlivá data. Je vaší zodpovědností určit prioritu nápravy upozornění v závislosti na důležitosti prostředků Azure a prostředí, ve kterém k incidentu došlo.

- [Výstrahy zabezpečení ve službě Azure Security Center](../../security-center/security-center-alerts-overview.md)

- [Používání značek k uspořádání prostředků Azure](../management/tag-resources.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Zadržení, zničení a obnovení – automatizace zpracování incidentů

**Pokyny:** Automatizujte ruční opakující se úlohy, ay se zrychlila doba reakce a snížilo se zatížení analytiků. Ruční provádění úloh trvá déle, což zpomaluje jednotlivé incidenty a snižuje počet incidentů, které může analytik zvládnout. Ruční úlohy také analytiky více unavují, což zvyšuje riziko zpoždění v důsledku lidských chyb a snižuje schopnost analytiků efektivně se soustředit na složité úlohy. 

S využitím funkcí automatizace pracovních postupů ve službách Azure Security Center a Azure Sentinel můžete v reakci na příchozí upozornění zabezpečení automaticky aktivovat akce nebo spouštět playbooky. Playbook provádí akce, jako jsou odesílání oznámení, zakazování účtů nebo izolace problematických sítí. 

- [Konfigurace automatizace pracovních postupů ve službě Security Center](../../security-center/workflow-automation.md)

- [Nastavení automatizovaných reakcí na hrozby ve službě Azure Security Center](../../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Nastavení automatizovaných reakcí na hrozby ve službě Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="posture-and-vulnerability-management"></a>Stav a správa ohrožení zabezpečení

*Další informace najdete v tématu [Azure Security Benchmark: stav a správa ohrožení zabezpečení](../../security/benchmarks/security-controls-v2-posture-vulnerability-management.md).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Zřízení zabezpečených konfigurací pro služby Azure 

**Doprovodné** materiály: definování guardrails zabezpečení pro infrastrukturu a DevOps týmy tím, že usnadňují zabezpečenou konfiguraci služeb Azure, které používají.

Nakonfigurujte Azure Policy pro audit a vymáhání konfigurací vašich prostředků souvisejících s nasazeními spravovaných aplikací.

Pomocí Azure modrotisky můžete automatizovat nasazení a konfiguraci služeb a prostředí aplikací, včetně šablon Azure Resource Manager, přiřazení služby Azure RBAC a přiřazení Azure Policy v rámci jedné definice podrobného plánu.

- [Cílová zóna rozhraní pro přijetí v cloudu](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Vytváření a Správa zásad pro vymáhání dodržování předpisů](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint](../../governance/blueprints/overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Udržování zabezpečených konfigurací pro služby Azure

**Pokyny**: pomocí Azure Security Center můžete monitorovat prostředky související s Azure Managed Applications a používat Azure Policy [odepřít] a [nasadit, pokud neexistují] důsledky pro udržení zabezpečených konfigurací.
- [Pochopení Azure Policych efektů](../../governance/policy/concepts/effects.md)

- [Vytváření a Správa zásad pro vymáhání dodržování předpisů](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint](../../governance/blueprints/overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Provádění pravidelné simulace útoku

**Pokyny:** Podle potřeby provádějte testování průniku nebo aktivity červeného týmu na svých prostředcích Azure a zajistěte nápravu všech kritických zjištění v oblasti zabezpečení.
Postupujte podle pravidel zapojení testování průniku cloudových služeb Microsoftu (Microsoft Cloud Penetration Testing), abyste měli jistotu, že testy průniku neporušují zásady Microsoftu. Využijte strategii Microsoftu a spuštění červeného týmu a testování průniku na živém webu na cloudové infrastruktuře, službách a aplikacích spravovaný Microsoftem.

- [Testování průniku v Azure](../../security/fundamentals/pen-testing.md)

- [Pravidla zapojení testování průniku](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Červený tým cloudových služeb Microsoftu](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

## <a name="backup-and-recovery"></a>Zálohování a obnovy

*Další informace najdete v tématu [Azure Security Benchmark: zálohování a obnovení](../../security/benchmarks/security-controls-v2-backup-recovery.md).*

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Ověření všech záloh včetně klíčů spravovaných zákazníkem

**Pokyny**: při ukládání definic spravovaných aplikací do vlastního účtu úložiště se ujistěte, že můžete obnovit všechny přidružené klíče spravované zákazníky, které se použijí pro šifrování tohoto účtu, které jsou uložené v Azure Key Vault.

- [Přineste si vlastní úložiště pro definice spravovaných aplikací](./publish-service-catalog-app.md?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [Postup obnovení klíčů Key Vault v Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?amp;preserve-view=true&view=azps-5.1.0)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: Zmírnění rizika ztracených klíčů

**Doprovodné** materiály: Pokud pro definice spravovaných aplikací přidáváte vlastní úložiště, ujistěte se, že máte k dispozici míry, které zabrání a obnoví ztráty klíčů používaných k šifrování definic. Povolit ochranu před náhodným odstraněním a vyprázdněním na Azure Key Vault, který ukládá vaše klíče spravované zákazníkem k ochraně klíčů proti náhodnému nebo škodlivému odstranění.  

- [Přineste si vlastní úložiště pro definice spravovaných aplikací](./publish-service-catalog-app.md?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [Jak povolit obnovitelné odstranění a ochranu před vymazáním v Key Vaultu](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

## <a name="governance-and-strategy"></a>Zásady správného řízení a strategie

*Další informace najdete v tématu [Azure Security Benchmark: zásady správného řízení a strategie](../../security/benchmarks/security-controls-v2-governance-strategy.md).*

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
- [Doporučení architektury zabezpečení Azure – úložiště, data a šifrování](/azure/architecture/framework/security/storage-data-encryption?amp;bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Základy zabezpečení Azure – zabezpečení, šifrování a úložiště dat Azure](../../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework – osvědčené postupy zabezpečení a šifrování dat Azure](../../security/fundamentals/data-encryption-best-practices.md?amp;bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Security Benchmark – správa prostředků](../../security/benchmarks/security-controls-v2-asset-management.md)

- [Azure Security Benchmark – ochrana dat](../../security/benchmarks/security-controls-v2-data-protection.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definování strategie segmentace podniku 

**Pokyny:** Zřiďte celopodnikovou strategii za účelem segmentace přístupu k prostředkům pomocí kombinace identity, sítě, aplikace, předplatného, skupiny pro správu a dalších řídicích prvků.

Pečlivě vyvažte nutnost oddělení zabezpečení s nutností povolit každodenní provoz systémů, které musí vzájemně komunikovat a pracovat s daty.

Zajistěte, aby se strategie segmentace implementovala konzistentně ve všech typech řídicích prvků včetně zabezpečení sítě, modelů identit a přístupu a modelů oprávnění/přístupu aplikací a řídicích prvků pro lidské procesy.

- [Pokyny ke strategii segmentace v Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Pokyny ke strategii segmentace v Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Soulad segmentace sítě a strategie segmentace podniku](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definování strategie správy stavu zabezpečení

**Pokyny:** Nepřetržitě měřte a zmírňujte rizika pro jednotlivé prostředky a prostředí, ve kterém jsou hostovány. Stanovte prioritu prostředků s vysokou hodnotou a míst nejvíce vystavených útokům, jako jsou publikované aplikace, body sítě pro příchozí a odchozí přenosy dat, koncové body uživatelů a správců atd.

- [Azure Security Benchmark – správa stavu a ohrožení zabezpečení](../../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Soulad rolí a odpovědností organizace

**Pokyny:** Je nutné zadokumentovat a dále sdělovat jasnou strategii pro role a odpovědnosti ve vaší organizaci zabezpečení. Stanovte prioritu poskytování jasné odpovědnosti při rozhodování o zabezpečení, zároveň vyškolte všechny uživatele na model sdílené odpovědnosti a vyškolte technické týmy na technologii pro zabezpečení cloudu.

- [Osvědčený postup zabezpečení Azure 1 – Lidé: školení týmů v oblasti zabezpečení cloudu](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Osvědčený postup zabezpečení Azure 2 – Lidé: školení týmů na technologii zabezpečení cloudu](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Osvědčený postup zabezpečení Azure 3 – Proces: přiřazení odpovědnosti za rozhodování o zabezpečení cloudu](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

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

- [Azure Security Benchmark – zabezpečení sítě](../../security/benchmarks/security-controls-v2-network-security.md)

- [Přehled zabezpečení sítě v Azure](../../security/fundamentals/network-overview.md)

- [Strategie architektury podnikové sítě](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definování strategie identity a privilegovaného přístupu

**Pokyny:** Zřiďte postupy pro identitu a privilegovaný přístup Azure jako součást celkové strategie řízení přístupu zabezpečení vaší organizace.  

Tato strategie by měla zahrnovat zdokumentované pokyny, zásady a standardy pro následující prvky: 

-   Centralizovaný systém identit a ověřování a jeho vzájemné propojení s dalšími interními a externími systémy identit

-   Metody silného ověřování v různých případech a podmínkách použití

-   Ochrana vysoce privilegovaných uživatelů

-   Monitorování a zpracování anomálních aktivit uživatelů  

-   Uživatelská identita a kontrola přístupu a proces odsouhlasení

Další informace najdete v následujících referenčních materiálech:

- [Azure Security Benchmark – správa identit](../../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure Security Benchmark – privilegovaný přístup](../../security/benchmarks/security-controls-v2-privileged-access.md)

- [Osvědčený postup zabezpečení Azure 11 – Architektura: jednotná strategie zabezpečení](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Přehled zabezpečení správy identit v Azure](../../security/fundamentals/identity-management-overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

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

- [Azure Security Benchmark – protokolování a detekce hrozeb](../../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark – reakce na incidenty](../../security/benchmarks/security-controls-v2-incident-response.md)

- [Osvědčený postup zabezpečení Azure 4 – Proces: aktualizace procesů reakcí na incidenty pro cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Architektura přechodu na Azure a průvodce rozhodováním o protokolování a vytváření sestav](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Škálování, správa a monitorování Azure na podnikové úrovni](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="next-steps"></a>Další kroky

- Další informace najdete v článku [Přehled Azure Security Benchmark v2](../../security/benchmarks/overview.md).
- Další informace o [základních úrovních zabezpečení Azure](../../security/benchmarks/security-baselines-overview.md)
