---
title: Azure Security test benchmark v2 – Správa identit
description: Správa identit Azure Security benchmark v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: bcd88f9f21c68f7f6cdda7299ac97d67e97dc009
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059213"
---
# <a name="security-control-identity-management"></a>Řízení zabezpečení: Správa identit

Správa identit pokrývá ovládací prvky pro vytvoření zabezpečené identity a řízení přístupu pomocí Azure Active Directory. To zahrnuje použití jednotného přihlašování, silného ověřování, spravovaných identit (a principů služeb) pro aplikace, podmíněný přístup a monitorování anomálií.

## <a name="id-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>ID-1: standardizace Azure Active Directory jako centrální systém identit a ověřování

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| ID – 1 | 16,1, 16,2, 16,4, 16,5 | IA-2, IA-8, AC-2, AC-3 |

Azure Active Directory (Azure AD) je výchozí služba pro správu identit a přístupu v Azure. Ve službě Azure AD byste se měli standardizovat, aby bylo možné spravovat identitu a správu přístupu vaší organizace v nástroji:
- Cloudové prostředky Microsoftu, jako jsou Azure Portal, Azure Storage, Azure Virtual Machines (Linux a Windows), Azure Key Vault, PaaS a SaaS aplikace.

- Prostředky vaší organizace, jako jsou aplikace v Azure nebo podnikové síťové prostředky.

Zabezpečení služby Azure AD by mělo mít vysokou prioritu v praxi cloudového zabezpečení vaší organizace. Azure AD poskytuje bezpečnostní skóre identity, které vám pomůžou vyhodnotit stav zabezpečení identity vzhledem k osvědčeným postupům Microsoftu. Pomocí skóre můžete vyhodnotit, jak pečlivě vaše konfigurace vyhovuje doporučení osvědčených postupů, a v stav zabezpečení dělat vylepšení.

Poznámka: Azure AD podporuje externí poskytovatele identity, který umožňuje uživatelům bez účet Microsoft přihlašovat se k aplikacím a prostředkům s jejich externí identitou.

- [Nájem v Azure AD](../../active-directory/develop/single-and-multi-tenant-apps.md)

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Definování tenantů Azure AD](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/)  

- [Použití externích zprostředkovatelů identity pro aplikaci](/azure/active-directory/b2b/identity-providers)

- [Co je bezpečné skóre identity ve službě Azure AD](../../active-directory/fundamentals/identity-secure-score.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníka**:

- [Identita a klíče](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys) 

- [Architektura zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Zabezpečení aplikací a DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="id-2-manage-application-identities-securely-and-automatically"></a>ID-2: zabezpečená a Automatická správa identit aplikací

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| ID – 2 | – | AC-2, AC-3, IA-2, IA-4, IA-9 |

Pro jiné než lidské účty, jako jsou služby nebo automatizace, použijte spravované identity Azure, místo abyste vytvořili výkonnější účet pro přístup k prostředkům nebo spustit kód. Spravované identity Azure se můžou ověřit u služeb a prostředků Azure, které podporují ověřování Azure AD. Ověřování je povoleno prostřednictvím předdefinovaných pravidel udělení přístupu, což vyloučí pevně zakódované přihlašovací údaje ve zdrojovém kódu nebo konfiguračních souborech. 

Pro služby, které nepodporují spravované identity, použijte službu Azure AD k vytvoření instančního objektu s omezenými oprávněními na úrovni prostředků.  Doporučuje se nakonfigurovat instanční objekty s přihlašovacími údaji certifikátu a vrátit se k klientským tajným klíčům. V obou případech se Azure Key Vault dá použít společně se spravovanými identitami Azure, takže běhové prostředí (například funkce Azure) může přihlašovací údaje načíst z trezoru klíčů.

- [Spravované identity Azure](../../active-directory/managed-identities-azure-resources/overview.md)

- [Služby, které podporují spravované identity prostředků Azure](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Instanční objekt Azure](/powershell/azure/create-azure-service-principal-azureps)

- [Vytvoření instančního objektu s certifikáty](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

Použití Azure Key Vault k registraci objektu zabezpečení: ověřování # autorizovat-a-Security-Principal-to-Access-Key-trezor

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníka**:

- [Identita a klíče](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Zabezpečení aplikací a DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="id-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>ID-3: použití jednotného přihlašování (SSO) Azure AD pro přístup k aplikacím

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| ID – 3 | 4.4 | IA-2, IA-4 |

Azure AD poskytuje správu identit a přístupů do prostředků Azure, cloudových aplikací a místních aplikací. Správa identit a přístupu se vztahuje na podnikové identity, jako jsou zaměstnanci, i na externí identity, jako jsou partneři, dodavatelé a dodavatelé.

Pomocí jednotného přihlašování (SSO) Azure AD můžete spravovat a zabezpečit přístup k datům a prostředkům vaší organizace místně a v cloudu. Připojte všechny své uživatele, aplikace a zařízení do Azure AD pro zajištění bezproblémového, zabezpečeného přístupu a lepší viditelnosti a řízení. 

- [Principy jednotného přihlašování k aplikacím pomocí Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníka**:

- [Architektura zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Identita a klíče](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Zabezpečení aplikací a DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="id-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>ID-4: použijte ovládací prvky silného ověřování pro veškerý přístup založený na Azure Active Directory

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| ID – 4 | 4,2, 4,4 4,5, 11,5, 12,11, 16,3 | AC-2, AC-3, IA-2, IA-4 |

Azure AD podporuje ovládací prvky silného ověřování prostřednictvím služby Multi-Factor Authentication (MFA) a metod silného hesla.  
- Multi-Factor Authentication: Povolte Azure AD MFA a sledujte Azure Security Center doporučení pro správu identit a přístupu pro vaše nastavení MFA. Vícefaktorové ověřování se dá vyhovět všem uživatelům, vybrat uživatele nebo na úrovni jednotlivých uživatelů na základě podmínek přihlášení a rizikových faktorů. 

- Ověřování bez hesla: k dispozici jsou tři možnosti ověřování bez hesla: Windows Hello pro firmy, Microsoft Authenticator aplikace a místní metody ověřování, jako jsou čipové karty. 

Pro správce a privilegované uživatele zkontrolujte, že se používá nejvyšší úroveň metody silného ověřování, a pak uveďte příslušné zásady silného ověřování pro ostatní uživatele.

Pokud se pro ověřování Azure AD pořád používá starší verze ověřování založené na heslech, pamatujte na to, že účty jenom pro Cloud (uživatelské účty vytvořené přímo v Azure) mají výchozí základní zásady hesel. A hybridní účty (uživatelské účty, které pocházejí z místní služby Active Directory) dodržují místní zásady hesel. Při použití ověřování založeného na hesle poskytuje Azure AD funkci ochrany heslem, která uživatelům brání v nastavení hesla, která se dají snadno uhodnout. Společnost Microsoft poskytuje globální seznam zakázaných hesel, která jsou aktualizována na základě telemetrie, a zákazníci mohou seznam rozšířit podle svých potřeb (například branding, kulturní odkazy atd.). Tato ochrana heslem se dá použít jenom pro cloudové a hybridní účty. 

Poznámka: ověřování založené jenom na přihlašovacích údajích hesla je náchylné k oblíbeným metodám útoku. Pro zajištění vyššího zabezpečení používejte silné ověřování, jako je MFA a zásady silného hesla. Pro aplikace třetích stran a služby Marketplace, které můžou mít výchozí hesla, je byste při počátečním nastavení služby měli změnit. 

- [Jak povolit vícefaktorové ověřování v Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Seznámení s možnostmi ověřování s neheslem pro Azure Active Directory](../../active-directory/authentication/concept-authentication-passwordless.md)

- [Výchozí zásady hesel pro Azure AD](../../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Odstranění chybných hesel pomocí ochrany heslem Azure AD](../../active-directory/authentication/concept-password-ban-bad.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníka**:

- [Architektura zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Identita a klíče](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Zabezpečení aplikací a DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="id-5-monitor-and-alert-on-account-anomalies"></a>ID-5: monitorování a upozornění na anomáliích účtů

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| ID-5 | 4,8, 4,9, 16,12, 16,13 | AC-2, AC-3, AC-7, AU-6 |

Azure AD poskytuje následující zdroje dat: 
-   Přihlášení – sestava přihlášení poskytuje informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů.

-   Protokoly auditu – poskytuje sledovatelnost prostřednictvím protokolů pro všechny změny provedené prostřednictvím různých funkcí služby Azure AD. Příklady protokolů auditu v protokolovaných změnách zahrnují přidávání nebo odebírání uživatelů, aplikací, skupin, rolí a zásad.

-   Riziková přihlášení – Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.

-   Uživatelé označení příznakem rizika – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený.

Tyto zdroje dat je možné integrovat s Azure Monitor, službou Azure Sentinel nebo systémy SIEM třetích stran.

Azure Security Center může také upozorňovat na určité podezřelé aktivity, jako je například nadměrný počet neúspěšných pokusů o ověření a zastaralých účtů v rámci předplatného. 

Rozšířená ochrana před internetovými útoky (ATP) je řešení zabezpečení, které umožňuje pomocí místních signálů služby Active Directory identifikovat, detekovat a prozkoumat pokročilé hrozby, ohrožené identity a škodlivé akce programu Insider.

- [Sestavy aktivit auditu v Azure AD](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Jak zobrazit rizikové přihlašování Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Jak identifikovat uživatele Azure AD označené příznakem rizika pro rizikové aktivity](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Jak monitorovat identitu uživatelů a aktivity přístupu v Azure Security Center](../../security-center/security-center-identity-access.md)

- [Výstrahy v modulu Azure Security Center ochrany před hrozbami](//azure/security-center/alerts-reference)

- [Jak integrovat protokoly aktivit Azure do Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Připojení dat z Azure AD Identity Protection](../../sentinel/connect-azure-ad-identity-protection.md)

- [Azure Advanced Threat Protection](/azure-advanced-threat-protection/what-is-atp)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníka**:

- [Zabezpečení aplikací a DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="id-6-restrict-azure-resource-access-based-on-conditions"></a>ID – 6: omezit přístup k prostředkům Azure na základě podmínek

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| ID – 6 | – | AC-2, AC-3 |

Podmíněný přístup Azure AD můžete použít pro přesnější řízení přístupu na základě uživatelem definovaných podmínek, jako je třeba vyžadování přihlášení uživatelů z určitých rozsahů IP adres pro použití MFA. Pomocí zásad podmíněného přístupu Azure AD pro různé případy použití lze také použít podrobnou správu relace ověřování. 

- [Přehled podmíněného přístupu Azure](../../active-directory/conditional-access/overview.md)

- [Běžné zásady podmíněného přístupu](../../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Konfigurace správy relací ověřování pomocí podmíněného přístupu](../../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníka**:

- [Identita a klíče](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Zabezpečení aplikací a DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Analýza hrozeb](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="id-7-eliminate-unintended-credential-exposure"></a>ID-7: Eliminujte nezamýšlenou expozici přihlašovacích údajů

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| ID – 7 | 18,1, 18,7 | IA-5 |

Implementujte kontrolu přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Kontrola přihlašovacích údajů také podporuje přesun zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je například Azure Key Vault.

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníka**:

- [Zabezpečení aplikací a DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="id-8-secure-user-access-to-legacy-applications"></a>ID-8: zabezpečený přístup uživatelů k starším aplikacím

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| ID – 8 | 14,6 | AC-2, AC-3, SC-11 |

Ujistěte se, že máte k dispozici moderní řízení přístupu a monitorování relací pro starší verze aplikací a data, která ukládají a zpracovávají. I když se sítě VPN běžně používají pro přístup k starším aplikacím, často mají jenom základní řízení přístupu a omezené monitorování relací.

Azure Proxy aplikací služby AD umožňuje publikovat starší verze místních aplikací pro vzdálené uživatele s jednotným přihlašováním (SSO) a přitom explicitně ověřovat důvěryhodnost vzdálených uživatelů a zařízení pomocí podmíněného přístupu Azure AD. 

Alternativně Microsoft Cloud App Security je služba CASB (Cloud Access Security Broker), která poskytuje ovládací prvky pro monitorování uživatelských relací a blokování akcí (pro starší místní aplikace a aplikace cloudového softwaru jako služby (SaaS)). 

- [Proxy aplikací služby AD Azure](../../active-directory/manage-apps/application-proxy.md#what-is-application-proxy)

- [Microsoft Cloud App Security osvědčené postupy](/cloud-app-security/best-practices)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníka**:

- [Architektura zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Zabezpečení aplikací a DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

