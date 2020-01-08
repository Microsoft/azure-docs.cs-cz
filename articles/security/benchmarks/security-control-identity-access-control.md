---
title: Řízení zabezpečení Azure – identita a Access Control
description: Identita a Access Control ovládacího prvku zabezpečení
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 9081f74aee7ff503c7fe29cef6ca76e6d6b46a4f
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564267"
---
# <a name="security-control-identity-and-access-control"></a>Řízení zabezpečení: identita a Access Control

Doporučení pro správu identit a přístupu se zaměřují na problémy související s řízením přístupu na základě identity, uzamykání přístupu pro správu, upozorňování na události související s identitou, abnormální chování účtů a řízení přístupu na základě rolí.

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 3.1 | 4.1 | Zákazník |

Azure AD má předdefinované role, které se musí explicitně přiřadit a jsou Queryable. Pomocí modulu Azure AD PowerShell můžete provádět ad hoc dotazy a zjišťovat účty, které jsou členy skupin pro správu.

Jak získat roli adresáře ve službě Azure AD pomocí prostředí PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Jak získat členy role adresáře ve službě Azure AD pomocí prostředí PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

## <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 3,2 | 4.2 | Zákazník |

Azure AD nemá koncept výchozích hesel. Další prostředky Azure, které vyžadují heslo, vynutí vytvoření hesla s požadavky na složitost a minimální délkou hesla, která se liší v závislosti na službě. Zodpovídáte za aplikace třetích stran a služby Marketplace, které mohou používat výchozí hesla.

## <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 3.3 | 4.3 | Zákazník |

Vytvořte standardní operační postupy kolem použití vyhrazených účtů pro správu. Pomocí Azure Security Center správy identit a přístupu můžete monitorovat počet účtů pro správu.

Pomocí Azure AD Privileged Identity Management privilegovaných rolí pro služby společnosti Microsoft a Azure Resource Manager můžete také povolit přístup za běhu nebo jen tolik. 

Další informace: https://docs.microsoft.com/azure/active-directory/privileged-identity-management/

## <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: použijte jednotné přihlašování (SSO) s Azure Active Directory

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 3.4 | 4.4 | Zákazník |

Kdykoliv je to možné, použijte Azure Active Directory jednotného přihlašování (SSO), než nakonfigurujete jednotlivé samostatné přihlašovací údaje na službu. Použijte Azure Security Center doporučení pro správu identit a přístupu.

Princip jednotného přihlašování s Azure AD: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: použijte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory.

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 3,5 | 4,5, 11,5, 12,11, 16,3 | Zákazník |

Povolte Azure AD MFA a sledujte Azure Security Center doporučení pro správu identit a přístupu.

Jak povolit vícefaktorové ověřování v Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Jak monitorovat identitu a přístup v rámci Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

## <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 3.6 | 4,6, 11,6, 12,12 | Zákazník |

Použijte privilegovaným přístupem (Privileged Access Workstations) s MFA nakonfigurovaným pro přihlášení k prostředkům Azure a jejich konfigurace.

Další informace o pracovních stanicích s privilegovaným přístupem: https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Jak povolit vícefaktorové ověřování v Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


## <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: protokolování a upozornění na podezřelou aktivitu z účtů pro správu

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 3.7 | 4,8, 4,9 | Zákazník |

Pomocí Azure Active Directorych sestav zabezpečení můžete generovat protokoly a výstrahy, když v prostředí dojde k podezřelé nebo nebezpečné aktivitě. Pomocí Azure Security Center můžete monitorovat aktivitu identity a přístupu.

Jak identifikovat uživatele Azure AD označené příznakem rizika pro rizikové aktivity:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Jak monitorovat aktivitu identity a přístupu uživatelů v Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

## <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 3.8 | 11,7 | Zákazník |

Pomocí pojmenovaných umístění podmíněného přístupu povolíte přístup jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí.

Jak nakonfigurovat pojmenovaná umístění v Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

## <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 3.9 | 16,1, 16,2, 16,4, 16,5, 16,6 | Zákazník |

Jako centrální ověřování a systém autorizací použijte Azure Active Directory (AAD). AAD chrání data pomocí silného šifrování pro data v klidovém umístění a při přenosu. AAD taky soli, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele.

Postup vytvoření a konfigurace instance AAD: https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

## <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 3.1 | 16,9, 16,10 | Zákazník |

Azure AD poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Navíc můžete pomocí kontrol přístupu Azure identity efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatelů se dá pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé. 

https://docs.microsoft.com/azure/active-directory/reports-monitoring/ vytváření sestav Azure AD

Jak používat kontroly přístupu Azure identity: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

## <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: monitorování pokusů o přístup k deaktivovaným účtům

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 3,11 | 16,12 | Zákazník |

Máte přístup ke zdrojům přihlašovacích aktivit, auditu a rizikových událostí Azure AD, které vám umožní integraci s jakýmkoli nástrojem SIEM/monitoring.

Tento proces můžete zjednodušit vytvořením nastavení diagnostiky pro Azure Active Directory uživatelských účtů a odesláním protokolů auditu a protokolů přihlášení do pracovního prostoru Log Analytics. Požadované výstrahy můžete nakonfigurovat v pracovním prostoru Log Analytics.

Jak integrovat protokoly aktivit Azure do Azure Monitor:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

## <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: upozornění na odchylku chování přihlášení k účtu

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 3,12 | 16,13 | Zákazník |

Pomocí funkcí pro rizika a ochranu identity v Azure AD můžete nakonfigurovat automatizované odezvy na zjištěné podezřelé akce týkající se identit uživatelů. Můžete také ingestovat data do služby Azure Sentinel pro další šetření.

Jak zobrazit rizikové přihlašování Azure AD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Jak nakonfigurovat a povolit zásady pro rizika ochrany identity:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Jak připojit Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

## <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 3,13 | 16 | Zákazník |

Ve scénářích podpory, kde Microsoft potřebuje získat přístup k zákaznickým datům, Customer Lockbox poskytuje rozhraní pro kontrolu a schválení nebo odmítnutí žádostí o přístup k datům zákazníků.

Princip Customer Lockbox:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

## <a name="next-steps"></a>Další kroky

Podívejte se na další ovládací prvek zabezpečení: [Ochrana dat](security-control-data-protection.md)
