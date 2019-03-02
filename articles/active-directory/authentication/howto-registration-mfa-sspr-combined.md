---
title: Začínáme s kombinovaná registrace pro samoobslužné resetování HESLA Azure AD a vícefaktorové ověřování (preview)
description: Povolit kombinované ověřování Azure Multi-Factor Authentication AD a resetování hesla pomocí samoobslužné služby registrace (preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9623cf727998348c313b7184c29e968afd33c6cc
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/01/2019
ms.locfileid: "57214142"
---
# <a name="enable-combined-security-information-registration-preview"></a>Povolit kombinovat zabezpečení informace o registraci (preview)

Než povolíte nové prostředí, přečtěte si článek [registrační informace o zabezpečení (preview) v kombinaci](concept-registration-mfa-sspr-combined.md) zajistit pochopit funkce a dopad této funkce.

![Informace o registraci kombinované zabezpečení rozšířené prostředí pro žádosti o další informace při přihlášení. Příklad ukazuje, jako první metoda registruje aplikaci Microsoft Authenticator.](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Kombinované zabezpečení informace o registraci pro resetování hesla pomocí samoobslužné služby Azure Multi-Factor Authentication a Azure AD je funkce ve verzi public preview služby Azure Active Directory. Další informace o verzích Preview najdete v tématu [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="enable-combined-registration"></a>Povolení kombinované registrace

Proveďte následující kroky, aby kombinovaná registrace:

1. Přihlaste se k webu Azure portal jako globální správce nebo Správce uživatelů.
2. Přejděte do **Azure Active Directory** > **uživatelská nastavení** > **umožňuje spravovat nastavení přístupu funkce ve verzi preview panel**.
3. V části **uživatelé můžou používat funkce pro registraci a správu bezpečnostní údaje ve verzi preview – aktualizovat**, můžete povolit pro **vybrané** skupiny uživatelů nebo pro **všechny** uživatelů.

![Povolit prostředí ve verzi preview kombinované bezpečnostní údaje pro všechny uživatele na portálu Azure AD](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> Počínaje březnem 2019 možnosti telefonního hovoru nebudou k dispozici uživatelům vícefaktorové ověřování a samoobslužné resetování HESLA v tenantech bezplatné a zkušební verze Azure AD. Zprávy SMS, nejsou ovlivněny tuto změnu. Telefonní hovor budou nadále dostupné uživatelům v placené tenantů Azure AD. Tato změna ovlivní jenom klienty bezplatné a zkušební verze Azure AD.

> [!NOTE]
> Po povolení kombinované registrace uživatelů, kteří registrace nebo potvrzení, že se že jejich telefonní číslo nebo mobilních aplikací prostřednictvím nového prostředí můžete využít pro vícefaktorové ověřování a samoobslužné resetování HESLA, pokud tyto metody jsou povolené v zásadách vícefaktorového ověřování a samoobslužné resetování HESLA. Pokud zakážete pak toto prostředí, na stránce uživatelé přejít na předchozí registrace samoobslužného resetování HESLA [https:/aka.ms/ssprsetup](https:/aka.ms/ssprsetup) budou muset provádět ověřování službou Multi-Factor Authentication před přístupem na stránce.

## <a name="next-steps"></a>Další postup

[Dostupné metody pro vícefaktorové ověřování a samoobslužné resetování HESLA](concept-authentication-methods.md)

[Konfigurace samoobslužného resetování hesla](howto-sspr-deployment.md)

[Konfigurovat ověřování Azure Multi-Factor Authentication](howto-mfa-getstarted.md)

[Řešení potíží s kombinovat registrační informace o zabezpečení](howto-registration-mfa-sspr-combined-troubleshoot.md)