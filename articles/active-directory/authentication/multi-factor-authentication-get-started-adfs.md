---
title: Dvoustupňové ověřování Azure AD MFA a ADFS – Azure Active Directory
description: Toto je stránka Multi-Factor Authentication služby Azure AD, která popisuje, jak začít s Azure AD MFA a AD FS.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0023d40fdc26fa1c42a67ce78a9259643098abb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96741401"
---
# <a name="getting-started-with-azure-ad-multi-factor-authentication-and-active-directory-federation-services"></a>Začínáme se službou Azure AD Multi-Factor Authentication a Active Directory Federation Services (AD FS)

<center>

![Začínáme s Azure AD MFA a ADFS](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Pokud má vaše organizace federované místní služby Active Directory s Azure Active Directory pomocí AD FS, existují dvě možnosti použití služby Azure AD Multi-Factor Authentication.

* Zabezpečení cloudových prostředků pomocí Multi-Factor Authentication Azure AD nebo Active Directory Federation Services (AD FS)
* Zabezpečení cloudu a místních prostředků pomocí ověřování Azure Multi-Factor Authentication Server

Následující tabulka shrnuje možnosti ověřování mezi zabezpečením prostředků pomocí Multi-Factor Authentication Azure AD a AD FS

| Zkušenosti s ověřováním – aplikace založené na prohlížeči | Zkušenosti s ověřováním – aplikace nezaložené na prohlížeči |
|:--- |:--- |
| Zabezpečení prostředků Azure AD pomocí Multi-Factor Authentication Azure AD |<li>První krok ověření se provádí místně pomocí služby AD FS.</li> <li>Druhý krok využívá telefon a provádí se pomocí cloudového ověřování.</li> |
| Zabezpečení prostředků Azure AD pomocí Active Directory Federation Services |<li>První krok ověření se provádí místně pomocí služby AD FS.</li><li>Druhý krok se provádí místně dodržením deklarace identity.</li> |

Upozornění s hesly aplikací pro federované uživatele:

* Hesla aplikace se ověřují pomocí cloudového ověřování, takže obcházejí federaci. Federace se aktivně používá jenom při nastavování hesla aplikace.
* Místní nastavení služby Access Control klienta nejsou dodržená hesly aplikace.
* U hesel aplikací ztratíte schopnost ověřování přihlašování v místě.
* Zakázání/odstranění účtu může pro synchronizaci adresáře trvat až tři hodiny, přičemž dojde ke zpoždění zakázání/odstranění hesla aplikace v cloudové identitě.

Informace o nastavení Multi-Factor Authentication Azure AD nebo Azure Multi-Factor Authentication Server pomocí AD FS najdete v následujících článcích:

* [Zabezpečení cloudových prostředků pomocí Multi-Factor Authentication Azure AD a AD FS](howto-mfa-adfs.md)
* [Zabezpečený cloud a místní prostředky používající ověřování Azure Multi-Factor Authentication Server s Windows Server 2012 R2 AD FS](howto-mfaserver-adfs-2012.md)
* [Zabezpečené cloudové a lokální prostředky používající Microsoft Azure Multi-Factor Authentication Serveru s AD FS 2.0](howto-mfaserver-adfs-2.md)
