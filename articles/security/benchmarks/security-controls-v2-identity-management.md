---
title: Azure Security test benchmark v2 – Správa identit
description: Správa identit Azure Security benchmark v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 4a36bd69ff5ddbc79e358d6f8a2c5b4d640c6d5c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102051442"
---
# <a name="security-control-v2-identity-management"></a>Řízení zabezpečení v2: Správa identit

Správa identit pokrývá ovládací prvky pro vytvoření zabezpečené identity a řízení přístupu pomocí Azure Active Directory. To zahrnuje použití jednotného přihlašování, silného ověřování, spravovaných identit (a principů služeb) pro aplikace, podmíněný přístup a monitorování anomálií.

Pokud se chcete podívat na příslušný integrovaný Azure Policy, přečtěte si [Podrobnosti o integrovaném iniciativě Azure Security test dodržování předpisů: Správa identit](../../governance/policy/samples/azure-security-benchmark.md#identity-management)

## <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardizace Azure Active Directory jako centrálního systému pro identifikaci a ověřování

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| IM-1 | 16,1, 16,2, 16,4, 16,5 | IA-2, IA-8, AC-2, AC-3 |

Azure Active Directory (Azure AD) je výchozí služba pro správu identit a přístupu v Azure. Ve službě Azure AD byste se měli standardizovat, aby bylo možné spravovat identitu a správu přístupu vaší organizace v nástroji:
- cloudové prostředky Microsoftu, jako jsou Azure Portal, Azure Storage, virtuální počítače Azure (s Linuxem a Windows), Azure Key Vault a aplikace PaaS a SaaS

- prostředky vaší organizace, jako jsou aplikace v Azure nebo prostředky vaší podnikové sítě

Zabezpečení služby Azure AD by mělo mít vysokou prioritu v praxi cloudového zabezpečení vaší organizace. Azure AD poskytuje bezpečnostní skóre identity, které vám pomůžou vyhodnotit stav zabezpečení identity vzhledem k osvědčeným postupům Microsoftu. S využitím tohoto skóre můžete změřit, nakolik vaše konfigurace odpovídá doporučeným osvědčeným postupům, a zlepšit stav zabezpečení.

Poznámka: Azure AD podporuje externí zprostředkovatele identit, kteří umožňují uživatelům bez účtu Microsoft přihlašovat se ke svým aplikacím a prostředkům s využitím své externí identity.

- [Architektura tenantů v Azure AD](../../active-directory/develop/single-and-multi-tenant-apps.md)

- [Vytvoření a konfigurace instance Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Definování tenantů Azure AD](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/)

- [Používání externích zprostředkovatelů identit pro aplikaci](../../active-directory/external-identities/identity-providers.md)

- [Co je skóre zabezpečení identit v Azure AD?](../../active-directory/fundamentals/identity-secure-score.md)

**Odpovědnost:** Zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Správa identit a klíčů](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys) 

- [Architektura zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Zabezpečení aplikací a DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Zabezpečená a automatická správa identit aplikací

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| IM – 2 | – | AC-2, AC-3, IA-2, IA-4, IA-9 |

Pro jiné než lidské účty, jako jsou služby nebo automatizace, použijte spravované identity Azure, místo abyste vytvořili výkonnější účet pro přístup k prostředkům nebo spustit kód. Spravované identity Azure se můžou ověřit u služeb a prostředků Azure, které podporují ověřování Azure AD. Ověřování je povoleno prostřednictvím předdefinovaných pravidel udělení přístupu, což vyloučí pevně zakódované přihlašovací údaje ve zdrojovém kódu nebo konfiguračních souborech. 

Pro služby, které nepodporují spravované identity, použijte službu Azure AD k vytvoření instančního objektu s omezenými oprávněními na úrovni prostředků. Doporučuje se nakonfigurovat instanční objekty s přihlašovacími údaji certifikátu a vrátit se k klientským tajným klíčům. V obou případech se Azure Key Vault dá použít společně se spravovanými identitami Azure, takže běhové prostředí (například funkce Azure) může přihlašovací údaje načíst z trezoru klíčů.

- [Spravované identity Azure](../../active-directory/managed-identities-azure-resources/overview.md)

- [Služby, které podporují spravované identity prostředků Azure](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Instanční objekt Azure](/powershell/azure/create-azure-service-principal-azureps)

- [Vytvoření instančního objektu s certifikáty](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

Použití Azure Key Vault k registraci objektu zabezpečení: ověřování # autorizovat-a-Security-Principal-to-Access-Key-trezor

**Odpovědnost:** Zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Správa identit a klíčů](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Zabezpečení aplikací a DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Použití jednotného přihlašování (SSO) Azure AD pro přístup k aplikacím

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| IM – 3 | 4.4 | IA-2, IA-4 |

Azure AD poskytuje správu identit a přístupů do prostředků Azure, cloudových aplikací a místních aplikací. Správa identit a přístupu se vztahuje na podnikové identity, jako jsou zaměstnanci, i na externí identity, jako jsou partneři, dodavatelé a dodavatelé.

Pomocí jednotného přihlašování (SSO) Azure AD můžete spravovat a zabezpečit přístup k datům a prostředkům vaší organizace místně a v cloudu. Připojte všechny své uživatele, aplikace a zařízení do Azure AD pro zajištění bezproblémového, zabezpečeného přístupu a lepší viditelnosti a řízení. 

- [Principy jednotného přihlašování k aplikacím pomocí Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

**Odpovědnost:** Zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Správa identit a klíčů](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Zabezpečení aplikací a DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Použití řídicích prvků silného ověřování pro veškerý přístup založený na Azure Active Directory

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| IM – 4 | 4,2, 4,4 4,5, 11,5, 12,11, 16,3 | AC-2, AC-3, IA-2, IA-4 |

Azure AD podporuje ovládací prvky silného ověřování prostřednictvím služby Multi-Factor Authentication (MFA) a metod silného hesla.

- Multi-Factor Authentication: Povolte Azure AD MFA a sledujte Azure Security Center doporučení pro správu identit a přístupu pro vaše nastavení MFA. Vícefaktorové ověřování se dá vyhovět všem uživatelům, vybrat uživatele nebo na úrovni jednotlivých uživatelů na základě podmínek přihlášení a rizikových faktorů.

- Ověřování bez hesla: k dispozici jsou tři možnosti ověřování bez hesla: Windows Hello pro firmy, Microsoft Authenticator aplikace a místní metody ověřování, jako jsou čipové karty.

Pro správce a privilegované uživatele zkontrolujte, že se používá nejvyšší úroveň metody silného ověřování, a pak uveďte příslušné zásady silného ověřování pro ostatní uživatele.

Pokud se pro ověřování Azure AD pořád používá starší verze ověřování založené na heslech, pamatujte na to, že účty jenom pro Cloud (uživatelské účty vytvořené přímo v Azure) mají výchozí základní zásady hesel. A hybridní účty (uživatelské účty, které pocházejí z místní služby Active Directory) dodržují místní zásady hesel. Při použití ověřování založeného na hesle poskytuje Azure AD funkci ochrany heslem, která uživatelům brání v nastavení hesla, která se dají snadno uhodnout. Společnost Microsoft poskytuje globální seznam zakázaných hesel, která jsou aktualizována na základě telemetrie, a zákazníci mohou seznam rozšířit podle svých potřeb (například branding, kulturní odkazy atd.). Tato ochrana heslem se dá použít jenom pro cloudové a hybridní účty.

Poznámka: ověřování založené jenom na přihlašovacích údajích hesla je náchylné k oblíbeným metodám útoku. Pro zajištění vyššího zabezpečení používejte silné ověřování, jako je MFA a zásady silného hesla. Pro aplikace třetích stran a služby Marketplace, které můžou mít výchozí hesla, je byste při počátečním nastavení služby měli změnit.

- [Jak povolit vícefaktorové ověřování v Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Úvod do možností ověřování bez hesla pro Azure Active Directory](../../active-directory/authentication/concept-authentication-passwordless.md)

- [Výchozí zásady hesel Azure AD](../../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Eliminace špatných hesel s využitím ochrany hesel Azure AD](../../active-directory/authentication/concept-password-ban-bad.md)

**Odpovědnost:** Zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Správa identit a klíčů](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Zabezpečení aplikací a DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorování a upozornění na anomálie účtů

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| IM 5 | 4,8, 4,9, 16,12, 16,13 | AC-2, AC-3, AC-7, AU-6 |

Azure AD poskytuje následující zdroje dat: 
-   Přihlášení – Sestava přihlášení poskytuje informace o využití spravovaných aplikací a aktivitách přihlašování uživatelů.

-   Protokoly auditu – poskytuje sledovatelnost prostřednictvím protokolů pro všechny změny provedené prostřednictvím různých funkcí služby Azure AD. Příklady protokolů auditu v protokolovaných změnách zahrnují přidávání nebo odebírání uživatelů, aplikací, skupin, rolí a zásad.

-   Riziková přihlášení – Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.

-   Uživatelé označení příznakem rizika – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený.

Tyto zdroje dat je možné integrovat s Azure Monitor, s Sentinelou v Azure a systémy SIEM třetích stran.

Azure Security Center může také upozorňovat na určité podezřelé aktivity, jako je například nadměrný počet neúspěšných pokusů o ověření a zastaralých účtů v rámci předplatného. 

Rozšířená ochrana před internetovými útoky (ATP) je řešení zabezpečení, které umožňuje pomocí místních signálů služby Active Directory identifikovat, detekovat a prozkoumat pokročilé hrozby, ohrožené identity a škodlivé akce programu Insider.

- [Sestavy aktivit auditu v Azure AD](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Zobrazení rizikových přihlášení Azure AD](../../active-directory/identity-protection/overview-identity-protection.md)

- [Identifikace uživatelů Azure AD označených příznakem rizikové aktivity](../../active-directory/identity-protection/overview-identity-protection.md)

- [Monitorování identit a aktivit přístupu uživatelů ve službě Azure Security Center](../../security-center/security-center-identity-access.md)

- [Upozornění v modulu ochrany na základě analýzy hrozeb služby Azure Security Center](../../security-center/alerts-reference.md)

- [Jak integrovat protokoly aktivit Azure do Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Připojení dat z Azure AD Identity Protection](../../sentinel/connect-azure-ad-identity-protection.md)

- [Microsoft Defender for Identity](/azure-advanced-threat-protection/what-is-atp)

**Odpovědnost:** Zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpečení aplikací a DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Omezení přístupu k prostředkům Azure na základě podmínek

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| IM – 6 | – | AC-2, AC-3 |

Podmíněný přístup Azure AD můžete použít pro přesnější řízení přístupu na základě uživatelem definovaných podmínek, jako je třeba vyžadování přihlášení uživatelů z určitých rozsahů IP adres pro použití MFA. Pomocí zásad podmíněného přístupu Azure AD pro různé případy použití lze také použít podrobnou správu relace ověřování. 

- [Přehled podmíněného přístupu Azure](../../active-directory/conditional-access/overview.md)

- [Společné zásady podmíněného přístupu](../../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Konfigurace správy relací ověřování pomocí podmíněného přístupu](../../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Odpovědnost:** Zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Správa identit a klíčů](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Zabezpečení aplikací a DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Analýza hrozeb](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Eliminace nezamýšleného prozrazení přihlašovacích údajů

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| IM – 7 | 18,1, 18,7 | IA-5 |

Implementujte ke službě Azure DevOps Credential Scanner k identifikaci přihlašovacích údajů v rámci kódu. Kontrola přihlašovacích údajů také podporuje přesun zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je například Azure Key Vault.

V případě GitHubu můžete k identifikaci přihlašovacích údajů nebo jiné formy tajných kódů v kódu použít funkci nativního vyhledávání tajného klíče.

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Skenování tajných kódů GitHubu](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Odpovědnost:** Zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpečení aplikací a DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-8-secure-user-access-to-legacy-applications"></a>IM-8: zabezpečený přístup uživatelů k starším aplikacím

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| IM-8 | 14,6 | AC-2, AC-3, SC-11 |

Ujistěte se, že máte k dispozici moderní řízení přístupu a monitorování relací pro starší verze aplikací a data, která ukládají a zpracovávají. I když se sítě VPN běžně používají pro přístup k starším aplikacím, často mají jenom základní řízení přístupu a omezené monitorování relací.

Azure Proxy aplikací služby AD umožňuje publikovat starší verze místních aplikací pro vzdálené uživatele s jednotným přihlašováním (SSO) a přitom explicitně ověřovat důvěryhodnost vzdálených uživatelů a zařízení pomocí podmíněného přístupu Azure AD.

Alternativně Microsoft Cloud App Security je služba CASB (Cloud Access Security Broker), která poskytuje ovládací prvky pro monitorování uživatelských relací a blokování akcí (pro starší místní aplikace a aplikace cloudového softwaru jako služby (SaaS)).

- [Proxy aplikací služby AD Azure](../../active-directory/manage-apps/application-proxy.md#what-is-application-proxy)

- [Microsoft Cloud App Security osvědčené postupy](/cloud-app-security/best-practices)

**Odpovědnost:** Zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Zabezpečení aplikací a DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)