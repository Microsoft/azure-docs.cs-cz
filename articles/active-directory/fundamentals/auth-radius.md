---
title: Ověřování pomocí protokolu RADIUS s Azure Active Directory
description: Pokyny pro architekturu k dosažení ověřování RADIUS pomocí Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ec8062eb864c877b0f3659ca2dd8f103e935071
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836780"
---
# <a name="radius-authentication-with-azure-active-directory"></a>Ověřování pomocí protokolu RADIUS s Azure Active Directory

Protokol RADIUS (Remote Authentication Dial-In User Service) (RADIUS) je síťový protokol, který zabezpečuje síť tím, že umožňuje centralizované ověřování a autorizaci uživatelů telefonického připojení. Mnoho aplikací se pořád spoléhá na protokol RADIUS k ověřování uživatelů.

Microsoft Windows Server má roli nazvanou server NPS (Network Policy Server), která může fungovat jako server RADIUS a podporuje ověřování RADIUS.

Azure Active Directory (Azure AD) umožňuje ověřování službou Multi-Factor Authentication se systémy založenými na protokolu RADIUS. Pokud chce zákazník použít službu Azure AD Multi-Factor Authentication na některou z výše uvedených úloh protokolu RADIUS, může na svém serveru Windows NPS nainstalovat rozšíření Azure AD Multi-Factor Authentication NPS. 

Server NPS systému Windows ověřuje přihlašovací údaje uživatele proti službě Active Directory a poté odesílá požadavek Multi-Factor Authentication do Azure. Uživatel pak obdrží výzvu ke svému mobilnímu ověřovateli. Po úspěšném přihlášení se klientská aplikace může připojit ke službě. 

## <a name="use-when"></a>Použijte, když: 

Je potřeba přidat Multi-Factor Authentication do aplikací, jako například
* Virtuální privátní síť (VPN)
* Přístup přes Wi-Fi
* Brána vzdálené plochy (RDG)
* Infrastruktura virtuálních klientských počítačů (VDI)
* Jakékoli jiné, které závisí na protokolu RADIUS k ověřování uživatelů ve službě. 

> [!NOTE]
> Místo toho, abyste se spoléhali na protokol RADIUS a rozšíření služby Azure AD Multi-Factor Authentication NPS pro použití Multi-Factor Authentication Azure AD pro úlohy VPN, doporučujeme, abyste provedli upgrade na SAML a přímo federovat vaši síť VPN pomocí Azure AD. Díky tomu vaše síť VPN zajišťuje plnou škálu ochrany Azure AD, včetně podmíněného přístupu, Multi-Factor Authentication, dodržování předpisů pro zařízení a ochrany identity.

![Diagram architektury](./media/authentication-patterns/radius-auth.png)


## <a name="components-of-the-system"></a>Součásti systému 

* **Klientská aplikace (klient VPN)**: odešle požadavek na ověření do klienta protokolu RADIUS.

* **Klient RADIUS**: převede žádosti z klientské aplikace a pošle je na server RADIUS, na kterém je nainstalovaná přípona NPS.

* **Server RADIUS**: připojí se ke službě Active Directory a provede primární ověřování pro požadavek protokolu RADIUS. Po úspěšném dokončení předá požadavek do rozšíření Azure AD Multi-Factor Authentication NPS.

* **Rozšíření serveru NPS**: aktivuje požadavek do služby Azure AD Multi-Factor Authentication pro sekundární ověřování. V případě úspěchu rozšíření serveru NPS dokončí požadavek na ověření tím, že server RADIUS poskytne tokeny zabezpečení, které zahrnují Multi-Factor Authentication deklaraci identity, kterou vystavila služba tokenů zabezpečení Azure.

* **Azure AD Multi-Factor Authentication**: komunikuje se službou Azure AD, aby načetla podrobnosti uživatele a prováděla sekundární ověřování pomocí metody ověřování nakonfigurované uživatelem.

## <a name="implement-radius-with-azure-ad"></a>Implementace protokolu RADIUS pomocí Azure AD 

* [Poskytování možností Multi-Factor Authentication Azure AD pomocí serveru NPS](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension) 

* [Konfigurace rozšíření Azure AD Multi-Factor Authentication NPS](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension-advanced) 

* [SÍŤ VPN se Multi-Factor Authentication Azure AD pomocí rozšíření serveru NPS](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension-vpn) 

  
‎ 

 
