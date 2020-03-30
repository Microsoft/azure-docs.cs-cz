---
title: 'Azure AD Connect: Hybridní Azure AD připojit úkoly po konfiguraci | Dokumenty společnosti Microsoft'
description: Tento dokument podrobně popisuje úlohy po konfiguraci potřebné k dokončení připojení hybridní azure ad
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: billmath
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffb8243041bb93ba8be6a65bb83df6f84affaee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049660"
---
# <a name="post-configuration-tasks-for-hybrid-azure-ad-join"></a>Úlohy po dokončení konfigurace pro hybridní připojení k Azure AD

Po spuštění Služby Azure AD Connect ke konfiguraci vaší organizace pro hybridní připojení Azure AD je několik dalších kroků, které je nutné provést, abyste toto nastavení dokončili.  Proveďte pouze kroky, které platí pro vaše zařízení.

## <a name="1-configure-controlled-rollout-optional"></a>1. Konfigurace řízeného zavádění (volitelné)
Všechna zařízení připojená k doméně se systémem Windows 10 a Windows Server 2016 se automaticky zaregistrují ve službě Azure AD po dokončení všech kroků konfigurace. Pokud dáváte přednost řízenému zavedení před touto automatickou registrací, můžete pomocí zásad skupiny selektivně povolit nebo zakázat automatické zavádění.  Tato zásada skupiny by měla být nastavena před zahájením dalších kroků konfigurace:
* Vytvořte objekt zásad skupiny ve službě Active Directory.
* Pojmenujte jej (ex-hybridní připojení Azure AD).
* Úpravy a přejděte na: Zásady konfigurace počítače > > šablony pro správu > součásti systému Windows > registrace zařízení.

>[!NOTE]
>Pro 2012R2 nastavení zásad jsou na **konfigurace počítače > zásady > šablony pro správu > součásti Windows > pracoviště připojit se > automaticky pracoviště připojit klientské počítače**

* Povolit toto nastavení: Registrace počítačů spojených s doménou jako zařízení.
* Použít a kliknout na OK.
* Propojte objekt zásad skupiny s umístěním podle vašeho výběru (organizační jednotka, skupina zabezpečení nebo doména pro všechna zařízení).

## <a name="2-configure-network-with-device-registration-endpoints"></a>2. Konfigurace sítě s koncovými body registrace zařízení
Ujistěte se, že následující adresy URL jsou přístupné z počítačů uvnitř vaší organizační sítě pro registraci do Azure AD:

* `https://enterpriseregistration.windows.net`
* `https://login.microsoftonline.com`
* `https://device.login.microsoftonline.com` 

## <a name="3-implement-wpad-for-windows-10-devices"></a>3. Implementace WPAD pro zařízení s Windows 10
Pokud vaše organizace přistupuje k Internetu přes odchozí proxy server, implementujte automatické zjišťování webového proxy serveru (WPAD), abyste počítačům s Windows 10 umožnili registraci do Azure AD.

## <a name="4-configure-the-scp-in-any-forests-that-were-not-configured-by-azure-ad-connect"></a>4. Konfigurace scp ve všech doménových strukturách, které nebyly nakonfigurované službou Azure AD Connect 

Bod připojení služby (SCP) obsahuje informace o tenantovi Azure AD, které budou vaše zařízení používat pro automatickou registraci.  Spusťte skript PowerShell, ConfigureSCP.ps1, který jste stáhli z Azure AD Connect.

## <a name="5-configure-any-federation-service-that-was-not-configured-by-azure-ad-connect"></a>5. Konfigurace jakékoli federační služby, která nebyla nakonfigurovaná službou Azure AD Connect

Pokud vaše organizace používá federační službu k přihlášení k Azure AD, pravidla deklarací deklarací ve vašem vztahu důvěryhodnosti předávající strany Služby Azure AD musí umožňovat ověřování zařízení. Pokud používáte federaci se službou AD FS, přejděte na [nápovědu služby AD FS](https://aka.ms/aadrptclaimrules) a vygenerujte pravidla deklarací. Pokud používáte federační řešení jiného výrobce, obraťte se na tohoto zprostředkovatele pro pokyny.  

>[!NOTE]
>Pokud máte windows down-level zařízení, služba musí podporovat vydávání metody ověřování a wiaormultiauthn deklarace identity při příjmu požadavků na důvěryhodnost Azure AD. Ve službě AD FS byste měli přidat pravidlo transformace vystavování, které předá metodu ověřování.

## <a name="6-enable-azure-ad-seamless-sso-for-windows-down-level-devices"></a>6. Povolení bezproblémového jednotného přihlašovacího systému Azure AD pro zařízení nižší úrovně Windows

Pokud vaše organizace používá synchronizaci hash hesla nebo předávací ověřování k přihlášení k Azure AD, povolte Azure https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ssoAD Bezproblémové jednotné přihlašování s tímto způsobem přihlášení k ověření zařízení windows nižší úrovně: . 

## <a name="7-set-azure-ad-policy-for-windows-down-level-devices"></a>7. Nastavení zásad Azure AD pro zařízení nižší úrovně Windows

Chcete-li zaregistrovat zařízení windows nižší úrovně, musíte se ujistit, že zásady Azure AD umožňuje uživatelům zaregistrovat zařízení. 

* Přihlaste se ke svému účtu na webu Azure Portal.
* Přejít na: Nastavení služby Azure Active Directory > zařízení > zařízení
* Nastavte "Uživatelé mohou zaregistrovat svá zařízení s Azure AD" na ALL.
* Kliknutí na Uložit

## <a name="8-add-azure-ad-endpoint-to-windows-down-level-devices"></a>8. Přidání koncového bodu Azure AD do zařízení nižší úrovně Windows

Přidejte koncový bod ověřování zařízení Azure AD do místních zón intranetu na zařízeních s windows nižší úrovní, abyste se vyhnuli výzvám k certifikátu při ověřování zařízení:`https://device.login.microsoftonline.com` 

Pokud používáte [bezproblémové jednotné přihlašování](how-to-connect-sso.md), povolte také "Povolit aktualizace stavového řádku pomocí skriptu" v této zóně a přidejte následující koncový bod:`https://autologon.microsoftazuread-sso.com` 

## <a name="9-install-microsoft-workplace-join-on-windows-down-level-devices"></a>9. Instalace připojení microsoft workplace na zařízeních nižší úrovně systému Windows

Tento instalační program vytvoří naplánovanou úlohu v systému zařízení, který běží v kontextu uživatele. Úloha se spustí, když se uživatel přihlásí k systému Windows. Úloha bezobslužně připojí zařízení s Azure AD s přihlašovacími údaji uživatele po ověření pomocí integrovaného ověřování systému Windows. Centrum pro stahování https://www.microsoft.com/download/details.aspx?id=53554je na adrese . 

## <a name="10-configure-group-policy-to-allow-device-registration"></a>10. Konfigurace zásad skupiny tak, aby umožňovala registraci zařízení

* Pokud již není vytvořen, vytvořte objekt zásad skupiny ve službě Active Directory.
* Pojmenujte jej (ex-hybridní připojení Azure AD).
* Upravit & přejít na: Zásady konfigurace počítače > > šablony pro správu > součásti systému Windows > registrace zařízení
* Povolit: Registrace počítačů spojených s doménou jako zařízení
* Použít a kliknout na OK.
* Propojte objekt zásad skupiny s umístěním podle vašeho výběru (organizační jednotka, skupina zabezpečení nebo doména pro všechna zařízení).

>[!NOTE]
>Pro 2012R2 nastavení zásad jsou na **konfigurace počítače > zásady > šablony pro správu > součásti Windows > pracoviště připojit se > automaticky pracoviště připojit klientské počítače**

## <a name="next-steps"></a>Další kroky
[Konfigurace zpětného zápisu zařízení](how-to-connect-device-writeback.md)
