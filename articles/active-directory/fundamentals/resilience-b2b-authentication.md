---
title: Odolnost sestavení při ověřování externích uživatelů pomocí Azure Active Directory
description: Průvodce pro správce IT a architekty pro vytváření odolného ověřování externích uživatelů
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae8e24341c321fbfffb84d9b072abc4cf925aff3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919675"
---
# <a name="build-resilience-in-external-user-authentication"></a>Odolnost sestavení při ověřování externích uživatelů

[Azure Active Directory spolupráce B2B](https://docs.microsoft.com/azure/active-directory/external-identities/what-is-b2b) (Azure AD B2B) je funkce [externích identit](https://docs.microsoft.com/azure/active-directory/external-identities/delegate-invitations) , která umožňuje spolupráci s jinými organizacemi a jednotlivci. Umožňuje zabezpečené připojování uživatelů typu Host do tenanta služby Azure AD bez nutnosti spravovat jejich přihlašovací údaje. Externí uživatelé přinášejí svoji identitu a přihlašovací údaje od externího poskytovatele identity (IdP), takže nemusejí pamatovat nové přihlašovací údaje. 

## <a name="ways-to-authenticate-external-users"></a>Způsoby ověřování externích uživatelů

Můžete zvolit metody ověřování externích uživatelů v adresáři. Můžete použít Microsoft zprostředkovatelů identity nebo jiné zprostředkovatelů identity.

U každé externí IdP se pořídit dostupnost tohoto IdP. U některých metod připojení k zprostředkovatelů identity existují věci, které vám pomůžou zvýšit odolnost.

> [!NOTE] 
> Azure AD B2B má vestavěnou možnost ověřovat libovolného uživatele z libovolného tenanta [Azure Active Directory](https://docs.microsoft.com/azure/active-directory) nebo pomocí osobního [účtu Microsoft](https://account.microsoft.com/account). Pomocí těchto integrovaných možností není nutné provádět žádnou konfiguraci.

### <a name="considerations-for-resilience-with-other-idps"></a>Požadavky na odolnost s ostatními zprostředkovatelů identity

Pokud používáte externí zprostředkovatelů identity pro ověřování uživatelů typu Host, existují určité konfigurace, které je potřeba zajistit, abyste zabránili výpadkům.

| Metoda ověřování| Požadavky na odolnost |
| - | - |
| Federace se sociálními zprostředkovatelů identity, jako je [Facebook](https://docs.microsoft.com/azure/active-directory/external-identities/facebook-federation) nebo [Google](https://docs.microsoft.com/azure/active-directory/external-identities/google-federation).| Musíte udržovat svůj účet pomocí IdP a nakonfigurovat ID klienta a tajný klíč klienta. |
| [Přímá federace pomocí zprostředkovatelů identity SAML a WS-Federation](https://docs.microsoft.com/azure/active-directory/external-identities/direct-federation)| Pro přístup ke svým koncovým bodům, na kterých jsou závislé, musíte spolupracovat se svým vlastníkem IdP. <br>Je nutné zachovat metadata, která obsahují certifikáty a koncové body. |
| [Jednorázové heslo e-mailu](https://docs.microsoft.com/azure/active-directory/external-identities/one-time-passcode)| Tato metoda je závislá na e-mailovém systému Microsoftu, e-mailovém systému uživatele a e-mailovém klientovi uživatele. |


 

## <a name="self-service-sign-up-preview"></a>Samoobslužná registrace (Preview)

Jako alternativu k odesílání pozvánek nebo odkazů můžete povolit [samoobslužnou registraci](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-overview).  To umožňuje externím uživatelům požádat o přístup k aplikaci. Musíte vytvořit [konektor rozhraní API](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-api-connector) a přidružit ho k toku uživatele. Přiřadíte uživatelské toky, které definují činnost uživatele s jednou nebo více aplikacemi. 

Je možné používat [konektory rozhraní API](https://docs.microsoft.com/azure/active-directory/external-identities/api-connectors-overview) k integraci samoobslužného uživatelského toku přihlášení k rozhraním API externích systémů. Tato integrace rozhraní API se dá použít pro [vlastní pracovní postupy schvalování](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-approvals), [provádění ověření identity](https://docs.microsoft.com/azure/active-directory/external-identities/code-samples-self-service-sign-up)a další úkoly, jako je přepis uživatelských atributů. Použití rozhraní API vyžaduje, abyste spravovali následující závislosti.

* **Ověřování pomocí konektoru API**: nastavení konektoru vyžaduje adresu URL koncového bodu, uživatelské jméno a heslo. Nastavte proces, pomocí kterého se tyto přihlašovací údaje udržují, a spolupracujte s vlastníkem rozhraní API, abyste měli jistotu, že budete mít nějaký plán vypršení platnosti.

* **Odpověď konektoru API**: Navrhněte konektory rozhraní API v procesu registrace, aby nedošlo k řádnému selhání, pokud rozhraní API není k dispozici. Prověřte a poskytněte vývojářům rozhraní API tyto [příklady odpovědí rozhraní API](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-api-connector) a [osvědčených postupů pro řešení potíží](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-api-connector). Spolupracujte se vývojovým týmem rozhraní API, abyste otestovali všechny možné scénáře reakce, včetně pokračování, ověřování – chyby a blokování odpovědí. 

## <a name="next-steps"></a>Další kroky
Prostředky odolnosti pro správce a architekty
 
* [Odolnost sestavení se správou přihlašovacích údajů](resilience-in-credentials.md)

* [Odolnost sestavení se stavy zařízení](resilience-with-device-states.md)

* [Odolnost sestavení pomocí vyhodnocení průběžného přístupu (CAE)](resilience-with-continuous-access-evaluation.md)

* [Odolnost sestavení v hybridním ověřování](resilience-in-hybrid.md)

* [Odolnost sestavení v přístupu aplikace s proxy aplikací](resilience-on-premises-access.md)

Prostředky odolnosti pro vývojáře

* [Sestavování odolnosti IAM ve vašich aplikacích](resilience-app-development-overview.md)

* [Odolnost sestavení v systémech CIAM](resilience-b2c.md)
 
