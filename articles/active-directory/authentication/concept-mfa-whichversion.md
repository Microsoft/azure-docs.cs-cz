---
title: Výběr mezi cloudovým a serverovým Azure MFA | Dokumentace Microsoftu
description: Zvolte pro vás ideální řešení zabezpečení Multi-Factor Authentication položením otázky, co se pokoušíte zabezpečit a kde se nachází vaši uživatelé.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: get-started-article
ms.date: 10/02/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 8314d72aa2cc6787d3f65dd48cd693a0ac332c0a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33866348"
---
# <a name="choose-the-azure-multi-factor-authentication-solution-for-you"></a>Výběr vhodného řešení Azure Multi-Factor Authentication
Vzhledem k tomu, že existuje několik typů Azure Multi-Factor Authentication (MFA), musíme odpovědět na několik otázek, aby bylo možné zjistit, která verze je pro vás nejlepší.  Jsou to tyto otázky:

* [Co se pokouším zabezpečit?](#what-am-i-trying-to-secure)
* [Kde se nacházejí uživatelé?](#where-are-the-users-located)
* [Jaké funkce budu potřebovat?](#what-features-do-i-need)

V následujících částech najdete tipy k zodpovězení každé z těchto otázek.

## <a name="what-am-i-trying-to-secure"></a>Co se pokouším zabezpečit?
Pro určení správného řešení dvoustupňového ověření si nejdříve musíme odpovědět na otázku, co se pokoušíte zabezpečit pomocí druhé metody ověřování.  Jedná se o aplikaci, která je v Azure?  Nebo systém vzdáleného přístupu?  Tím, že určíme, co se pokoušíme zabezpečit, si můžeme odpovědět na otázku, kde musí být povoleno ověřování Multi-Factor Authentication.  

| Co se pokoušíte zabezpečit | MFA v cloudu | Server MFA |
| --- |:---:|:---:|
| Vlastní aplikace Microsoftu |● |● |
| Aplikace Saas v galerii aplikací |● |  |
| Webové aplikace publikované prostřednictvím proxy aplikace Azure AD |● |  |
| Aplikace služby IIS nepublikované prostřednictvím proxy aplikace Azure AD | |● |
| Vzdálený přístup, jako je například síť VPN, RDG | ● | ● |

## <a name="where-are-the-users-located"></a>Kde se nachází uživatelé
Když se dále podíváme, kde se naši uživatelé nacházejí, pomůže nám to určit správné řešení – jestli je to v cloudu nebo místně pomocí serveru MFA.

| Umístění uživatele | MFA v cloudu | Server MFA |
| --- |:---:|:---:|
| Azure Active Directory |● | |
| Azure AD a místní AD využívající federaci se službou AD FS |● |● |
| Azure AD a místní AD pomocí DirSync, Azure AD Sync, Azure AD Connect – bez synchronizace hodnoty hash hesel nebo předávacího ověřování |● |● |
| Azure AD a místní AD pomocí DirSync, Azure AD Sync, Azure AD Connect – se synchronizací hodnoty hash hesel nebo předávacím ověřováním |● | |
| Místní služby Active Directory | |● |

## <a name="what-features-do-i-need"></a>Jaké funkce budu potřebovat?
Následující tabulka porovnává funkce, které jsou dostupné se službou Multi-Factor Authentication v cloudu a s Multi-Factor Authentication Serverem.

| Funkce | MFA v cloudu | Server MFA |
| --- |:---:|:---:|
| Oznámení mobilní aplikace jako druhý faktor | ● | ● |
| Kód ověření mobilní aplikace jako druhý faktor | ● | ● |
| Telefonní hovor jako druhý faktor | ● | ● |
| Jednosměrné služby SMS jako druhý faktor | ● | ● |
| Obousměrné služby SMS jako druhý faktor | | ● (zastaralé)| 
| Tokeny hardwaru jako druhý faktor | | ● |
| Hesla aplikací pro klienty Office 365, kteří nepodporují MFA | ● | |
| Kontrola správce nad metodami ověřování | ● | ● |
| Režim kódu PIN | | ● |
| Výstraha podvodů |● | ● |
| Sestavy MFA |● | ● |
| Jednorázové potlačení | | ● |
| Vlastní přivítání pro telefonní hovory | ● | ● |
| Přizpůsobitelné ID volajícího pro telefonní hovory | ● | ● |
| Důvěryhodné IP adresy | ● | ● |
| Zapamatovat MFA pro důvěryhodná zařízení | ● | |
| Podmíněný přístup | ● | ● |
| Mezipaměť |  | ● |

## <a name="next-steps"></a>Další kroky

Když teď znáte rozdíl mezi službou Azure Multi-Factor Authentication v cloudu a místním MFA Serverem, je čas nastavit a začít používat službu Azure Multi-Factor Authentication. **Vyberte ikonu, která představuje váš scénář.**

<center>

[![MFA v cloudu](./media/concept-mfa-whichversion/cloud2.png)](howto-mfa-getstarted.md)  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [![MFA Server](./media/concept-mfa-whichversion/server2.png)](howto-mfaserver-deploy.md) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; </center>
