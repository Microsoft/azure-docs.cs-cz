---
title: Azure MFA Server nebo služba, místní nebo v cloudu?
description: Jako správce Azure AD je potřeba pochopit, kterou verzi MFA možné nasadit?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9fb7785487fc79daca56e35ce0c6a19efd5b0605
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56195749"
---
# <a name="which-version-of-azure-mfa-is-right-for-my-organization"></a>Kterou verzi Azure MFA je nejvhodnější pro moji organizaci?

Předtím, než se můžete rozhodnout, kde a jak nasadit služby Multi-Factor Authentication (MFA), je potřeba zodpovědět tři základní otázky.

* [Co se pokouším zabezpečit?](#what-am-i-trying-to-secure)
* [Kde se nacházejí uživatelé?](#where-are-the-users-located)
* [Jaké funkce budu potřebovat?](#what-features-do-i-need)

Každá z těchto částí poskytuje podrobnosti, které vám pomůžou odpovědět na předchozí otázky.

## <a name="what-am-i-trying-to-secure"></a>Co se pokouším zabezpečit?

Pokud chcete zjistit správného řešení dvoustupňového ověření, nejprve je nutné odpovědět na otázku, co se pokoušíte zabezpečit pomocí dodatečného faktoru ověřování. Jedná se o aplikaci, která je v Azure? Nebo systém vzdáleného přístupu? Tak, že určíte, co se pokoušíte zabezpečit, vám pomůže odpovědět na otázku, kde musí být povoleno ověřování Multi-Factor Authentication.

| Co se pokoušíte zabezpečit | MFA v cloudu | Server MFA |
| --- |:---:|:---:|
| Vlastní aplikace Microsoftu |● |● |
| Aplikace Saas v galerii aplikací |● |  |
| Webové aplikace publikované prostřednictvím proxy aplikace Azure AD |● |  |
| Aplikace služby IIS nepublikované prostřednictvím proxy aplikace Azure AD | |● |
| Vzdálený přístup, jako je například síť VPN, RDG | ● | ● |

## <a name="where-are-the-users-located"></a>Kde se nachází uživatelé

Dále určete, kde uživatelé ve vaší organizaci jsou umístěné pomáhá určit správné řešení, aby používalo, ať už v cloudu nebo místní využití serveru MFA.

| Umístění uživatele | MFA v cloudu | Server MFA |
| --- |:---:|:---:|
| Azure Active Directory |● | |
| Azure AD a místní AD využívající federaci se službou AD FS |● |● |
| Azure AD a místní AD pomocí služby Azure AD Connect – bez synchronizace hodnot hash hesel nebo předávací ověřování. |● |● |
| Azure AD a místní AD pomocí služby Azure AD Connect – hodnoty hash hesla synchronizace předávacím ověřováním |● | |
| Místní služby Active Directory | |● |

## <a name="what-features-do-i-need"></a>Jaké funkce budu potřebovat?

Následující tabulka porovnává funkce, které jsou dostupné se službou Multi-Factor Authentication v cloudu a s Multi-Factor Authentication Serverem.

| Funkce | MFA v cloudu | Server MFA |
| --- |:---:|:---:|
| Oznámení mobilní aplikace jako druhý faktor | ● | ● |
| Kód ověření mobilní aplikace jako druhý faktor | ● | ● |
| Telefonní hovor jako druhý faktor | ● | ● |
| Jednosměrné služby SMS jako druhý faktor | ● | ● |
| Tokeny hardwaru jako druhý faktor | ● (Public preview) | ● |
| Hesla aplikací pro klienty Office 365, kteří nepodporují MFA | ● | |
| Kontrola správce nad metodami ověřování | ● | ● |
| Režim kódu PIN | | ● |
| Výstraha podvodů | ● | ● |
| Sestavy MFA | ● | ● |
| Jednorázové potlačení | | ● |
| Vlastní přivítání pro telefonní hovory | ● | ● |
| Přizpůsobitelné ID volajícího pro telefonní hovory | ● | ● |
| Důvěryhodné IP adresy | ● | ● |
| Zapamatovat MFA pro důvěryhodná zařízení | ● | |
| Podmíněný přístup | ● | ● |
| Mezipaměť |  | ● |

## <a name="next-steps"></a>Další postup

Když teď znáte rozdíl mezi službou Azure Multi-Factor Authentication v cloudu a místním MFA Serverem, je čas nastavit a začít používat službu Azure Multi-Factor Authentication. **Vyberte ikonu, která představuje váš scénář.**

<center>

[![MFA v cloudu](./media/concept-mfa-whichversion/cloud2.png)](howto-mfa-getstarted.md)  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [![MFA Server](./media/concept-mfa-whichversion/server2.png)](howto-mfaserver-deploy.md) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; </center>
