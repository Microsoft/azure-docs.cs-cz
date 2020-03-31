---
title: Azure Security Control – řízení identit y a přístupu
description: Identita a řízení přístupu ovládacího prvku zabezpečení
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 543573610c2ea3ab0bcd89e1b8f4ee5f5a34dbc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934435"
---
# <a name="security-control-identity-and-access-control"></a>Řízení zabezpečení: Identita a řízení přístupu

Doporučení pro správu identit a přístupu se zaměřují na řešení problémů souvisejících s řízením přístupu na základě identity, uzamčenípřístupu pro správu, upozornění na události související s identitou, abnormální chování účtu a řízení přístupu na základě rolí.

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Vedení soupisu administrativních účtů

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 3.1 | 4.1 | Zákazník |

Azure AD má předdefinované role, které musí být explicitně přiřazeny a jsou dotazovatelné. Pomocí modulu Azure AD PowerShell můžete provádět ad hoc dotazy ke zjišťování účtů, které jsou členy skupin pro správu.

Jak získat roli adresáře ve službě Azure AD s PowerShellem:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Jak získat členy role adresáře ve službě Azure AD s PowerShellem:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

## <a name="32-change-default-passwords-where-applicable"></a>3.2: Změna výchozích hesel, pokud je to možné

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 3,2 | 4.2 | Zákazník |

Azure AD nemá koncept výchozí hesla. Jiné prostředky Azure, které vyžadují heslo, vynutí vytvoření hesla s požadavky na složitost a minimální délkou hesla, která se liší v závislosti na službě. Nesete odpovědnost za aplikace a služby marketplace jiných výrobců, které mohou používat výchozí hesla.

## <a name="33-use-dedicated-administrative-accounts"></a>3.3: Použití vyhrazených administrativních účtů

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 3.3 | 4.3 | Zákazník |

Vytvořte standardní operační postupy týkající se používání vyhrazených administrativních účtů. Pomocí správy identit a přístupu Centra zabezpečení Azure můžete sledovat počet účtů pro správu.

Můžete také povolit just-in-time / just-enough-Access pomocí Privilegované role správy privilegovaných identit Azure AD pro služby Microsoft a Azure Resource Manager. 

Víc se uč:https://docs.microsoft.com/azure/active-directory/privileged-identity-management/

## <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Použití jednotného přihlašování (SSO) s Azure Active Directory

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 3.4 | 4.4 | Zákazník |

Kdykoli je to možné, použijte místo toho automatické přistupující služby Azure Active Directory než nakonfigurujte jednotlivá samostatná pověření podle jednotlivých služeb. Používejte doporučení Centra zabezpečení Azure pro správu identit a přístupu.

Principy přisátého řešení zabezpečení pomocí Azure AD:

https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Použití vícefaktorového ověřování pro veškerý přístup založený na službě Azure Active Directory

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 3,5 | 4.5, 11.5, 12.11, 16.3 | Zákazník |

Povolte Azure AD MFA a postupujte podle doporučení Centra identit a správy přístupu Azure Security Center.

Jak povolit vícefaktorové povolení v Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Jak sledovat identitu a přístup v rámci Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

## <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Pro všechny administrativní úkoly používejte vyhrazené počítače (pracovní stanice s privilegovaným přístupem)

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 3.6 | 4.6, 11.6, 12.12 | Zákazník |

Používejte paws (privilegovaný přístup pracovnístanice) s MFA nakonfigurované pro přihlášení a konfiguraci prostředků Azure.

Další informace o pracovních stanicích s privilegovaným přístupem:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Jak povolit vícefaktorové povolení v Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


## <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Protokolování a upozornění na podezřelou aktivitu z administrativních účtů

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 3.7 | 4.8, 4.9 | Zákazník |

Sestavy zabezpečení služby Azure Active Directory slouží ke generování protokolů a výstrah v případě, že v prostředí dojde k podezřelé nebo nebezpečné aktivitě. Azure Security Center slouží ke sledování aktivity identity a přístupu.

Jak identifikovat uživatele Azure AD označené pro rizikové aktivity:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Jak sledovat identitu uživatelů a aktivitu přístupu v Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

## <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Správa prostředků Azure z jenom schválených umístění

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 3.8 | 11.7 | Zákazník |

Pomocí pojmenovaných umístění podmíněného přístupu můžete povolit přístup pouze z určitých logických seskupení oblastí IP adres nebo zemí nebo oblastí.

Jak nakonfigurovat pojmenovaná umístění v Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

## <a name="39-use-azure-active-directory"></a>3.9: Použití služby Azure Active Directory

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 3.9 | 16.1, 16.2, 16.4, 16.5, 16.6 | Zákazník |

Jako centrální ověřovací a autorizační systém použijte službu Azure Active Directory (AAD). AAD chrání data pomocí silného šifrování pro data v klidovém stavu a při přenosu. AAD také soli, hasha a bezpečně ukládá přihlašovací údaje uživatele.

Jak vytvořit a nakonfigurovat instanci AAD:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Pravidelně kontrolujte a slaďujte přístup uživatelů

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 3.10 | 16.9, 16.10 | Zákazník |

Azure AD poskytuje protokoly, které pomáhají zjistit zastaralé účty. Kromě toho můžete pomocí azure identity access reviews efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazení rolí. Přístup uživatelů lze pravidelně kontrolovat, aby se zajistilo, že pouze ti praví uživatelé mají trvalý přístup. 

Sestavy Azure AD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Jak používat recenze přístupu k identitám Azure:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

## <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Sledování pokusů o přístup k deaktivovaným účtům

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 3.11 | 16.12 | Zákazník |

Máte přístup ke zdrojům aktivit y přihlášení do Služby Azure AD, auditu a protokolu rizikových událostí, které umožňují integraci s libovolným nástrojem SIEM/Monitoring.

Tento proces můžete zjednodušit vytvořením diagnostických nastavení pro uživatelské účty Služby Azure Active Directory a odesláním protokolů auditování a protokolů přihlášení do pracovního prostoru Analýzy protokolů. Požadované výstrahy můžete nakonfigurovat v pracovním prostoru Analýzy protokolů.

Jak integrovat protokoly aktivit Azure do Azure Monitoru:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

## <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Upozornění na odchylku chování přihlášení k účtu

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 3.12 | 16.13 | Zákazník |

Pomocí funkcí Azure AD Risk and Identity Protection nakonfigurujte automatické odpovědi na zjištěné podezřelé akce související s identitami uživatelů. Můžete také ingestovat data do Azure Sentinelu pro další šetření.

Jak zobrazit Azure AD riskantní přihlášení:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Jak nakonfigurovat a povolit zásady rizik a zásady ochrany identity:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Jak napalubě Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

## <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Poskytněte společnosti Microsoft přístup k relevantním zákaznickým datům během scénářů podpory

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 3.13 | 16 | Zákazník |

Ve scénářích podpory, kde společnost Microsoft potřebuje přístup k zákaznickým datům, poskytuje customer lockbox rozhraní, které můžete zkontrolovat a schválit nebo odmítnout požadavky na přístup k datům zákazníků.

Pochopit bezpečnostní okno zákazníka:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

## <a name="next-steps"></a>Další kroky

Podívejte se na další ovládací prvek zabezpečení: [Ochrana dat](security-control-data-protection.md)
