---
title: Začínáme s kombinovaná registrace pro samoobslužné resetování HESLA Azure AD a Vícefaktorové ověřování (preview) – Azure Active Directory
description: Povolit kombinované ověřování Azure Multi-Factor Authentication AD a resetování hesla pomocí samoobslužné služby registrace (preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3baf2690ae07b87bb4d5dba30fcd20f62a1a4506
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60358059"
---
# <a name="enable-combined-security-information-registration-preview"></a>Povolit kombinovat zabezpečení informace o registraci (preview)

Než povolíte nové prostředí, přečtěte si článek [registrační informace o zabezpečení (preview) v kombinaci](concept-registration-mfa-sspr-combined.md) zajistit pochopit funkce a efekty této funkce.

![Možnosti registrace rozšířené informace o kombinované zabezpečení](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Kombinované zabezpečení registrační informace pro ověřování Azure Multi-Factor Authentication a resetování hesla pomocí samoobslužné služby Azure Active Directory (Azure AD) je funkce ve verzi public preview služby Azure AD. Další informace o verzích Preview najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="enable-combined-registration"></a>Povolení kombinované registrace

Dokončete tyto kroky pro povolení kombinovaná registrace:

1. Přihlaste se k webu Azure portal jako globální správce nebo Správce uživatelů.
2. Přejděte na **Azure Active Directory** > **uživatelská nastavení** > **umožňuje spravovat nastavení přístupu funkce ve verzi preview panel**.
3. V části **uživatelé můžou používat funkce pro registraci a správu bezpečnostní údaje ve verzi preview – aktualizovat**, můžete povolit pro **vybrané** skupiny uživatelů nebo pro **všechny** uživatelů.

   ![Povolit prostředí ve verzi preview kombinované bezpečnostní údaje pro všechny uživatele](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> Od března 2019 se nebudou k dispozici pro Vícefaktorové ověřování a samoobslužné resetování HESLA uživatelů v tenantech bezplatné a zkušební verze Azure AD možnosti telefonního hovoru. Zprávy SMS, nejsou touto změnou ovlivněny. Možnosti telefonního hovoru bude stále k dispozici uživatelům v placené tenantů Azure AD.

> [!NOTE]
> Po povolení kombinovaná registrace, uživatelé, kteří registrace nebo potvrzení telefonního čísla nebo mobilních aplikací prostřednictvím nového prostředí můžete využít pro Vícefaktorové ověřování a samoobslužné resetování HESLA, pokud tyto metody jsou povolené Vícefaktorové ověřování a samoobslužné resetování HESLA zásady. Pokud zakážete pak toto prostředí, na stránce uživatelé přejít na předchozí registrace samoobslužného resetování HESLA `https:/aka.ms/ssprsetup` budou muset provádět ověřování službou Multi-Factor Authentication před přístupem na stránce.

Pokud jste nakonfigurovali web k zóně Assignment List v Internet Exploreru, musí být ve stejné zóně následující weby:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="next-steps"></a>Další postup

[Dostupné metody pro Vícefaktorové ověřování a samoobslužné resetování HESLA](concept-authentication-methods.md)

[Konfigurace samoobslužného resetování hesla](howto-sspr-deployment.md)

[Konfigurovat ověřování Azure Multi-Factor Authentication](howto-mfa-getstarted.md)

[Řešení potíží s kombinovat registrační informace o zabezpečení](howto-registration-mfa-sspr-combined-troubleshoot.md)