---
title: 'Azure AD Connect: Úlohy konfigurace příspěvků v hybridní službě Azure AD | Microsoft Docs'
description: Tento dokument podrobně popisuje úlohy konfigurace, které jsou potřeba k dokončení připojení k hybridní službě Azure AD.
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
ms.openlocfilehash: 70e3267792f27a170efa26cc4267d1b25045a099
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231235"
---
# <a name="post-configuration-tasks-for-hybrid-azure-ad-join"></a>Úlohy po dokončení konfigurace pro hybridní připojení k Azure AD

Po spuštění Azure AD Connect ke konfiguraci vaší organizace pro připojení k hybridní službě Azure AD existuje několik dalších kroků, které je potřeba dokončit, aby bylo možné instalaci dokončit.  Proveďte kroky, které platí pro vaše zařízení.

## <a name="1-configure-controlled-rollout-optional"></a>1. Konfigurovat řízené zavedení (volitelné)
Všechna zařízení připojená k doméně se systémem Windows 10 a Windows Server 2016 se po dokončení všech kroků konfigurace automaticky zaregistrují ve službě Azure AD. Pokud upřednostňujete řízené zavedení místo této automatické registrace, můžete pomocí zásad skupiny selektivně povolit nebo zakázat automatické zavedení.  Tyto zásady skupiny by měly být nastavené před zahájením dalších kroků konfigurace:
* Vytvořte objekt zásad skupiny ve službě Active Directory.
* Pojmenujte ji (připojení ke službě Azure AD Hybrid).
* Upravit a přejít na:  Konfigurace počítačů > zásady > Šablony pro správu > součásti systému Windows > registrace zařízení.

>[!NOTE]
>2012R2 nastavení zásad v části **Konfigurace počítače zásady > > Šablony pro správu > součásti systému Windows > Workplace Join > automatické připojení klientských počítačů k síti** na pracovišti

* Povolit toto nastavení:  Zaregistrujte počítače připojené k doméně jako zařízení.
* Použijte a klikněte na OK.
* Propojte objekt zásad skupiny s vámi zvoleným umístěním (organizační jednotka, skupina zabezpečení nebo doména pro všechna zařízení).

## <a name="2-configure-network-with-device-registration-endpoints"></a>2. Konfigurace sítě pomocí koncových bodů registrace zařízení
Ujistěte se, že k registraci do služby Azure AD jsou dostupné následující adresy URL z počítačů v síti vaší organizace:

* https://enterpriseregistration.windows.net
* https://login.microsoftonline.com
* https://device.login.microsoftonline.com 

## <a name="3-implement-wpad-for-windows-10-devices"></a>3. Implementace protokolu WPAD pro zařízení s Windows 10
Pokud vaše organizace přistupuje k Internetu prostřednictvím odchozího proxy serveru, implementujte automatické zjišťování webových proxy serveru (WPAD), aby se počítače s Windows 10 mohly registrovat do Azure AD.

## <a name="4-configure-the-scp-in-any-forests-that-were-not-configured-by-azure-ad-connect"></a>4. Konfigurace spojovacího bodu služby v jakékoli doménové struktuře, která nebyla nakonfigurovaná pomocí Azure AD Connect 

Spojovací bod služby (SCP) obsahuje informace o tenantovi Azure AD, které budou vaše zařízení používat k automatické registraci.  Spusťte skript prostředí PowerShell ConfigureSCP. ps1, který jste si stáhli z Azure AD Connect.

## <a name="5-configure-any-federation-service-that-was-not-configured-by-azure-ad-connect"></a>5. Konfigurace jakékoli federační služby, která nebyla nakonfigurovaná pomocí Azure AD Connect

Pokud vaše organizace používá pro přihlášení ke službě Azure AD službu Federation Service, musí pravidla deklarace identity ve vztahu důvěryhodnosti předávající strany Azure AD umožňovat ověřování zařízení. Pokud používáte federaci s AD FS, přečtěte si v [tématu AD FS Help](https://aka.ms/aadrptclaimrules) , kde můžete vygenerovat pravidla deklarace identity. Pokud používáte federační řešení od jiného výrobce než Microsoftu, kontaktujte tohoto poskytovatele s pokyny.  

>[!NOTE]
>Pokud máte zařízení Windows nižší úrovně, musí služba při přijímání požadavků na vztah důvěryhodnosti služby Azure AD podporovat vydávání deklarací authenticationmethod a wiaormultiauthn. V AD FS byste měli přidat pravidlo transformace vystavování, které projde metodou ověřování.

## <a name="6-enable-azure-ad-seamless-sso-for-windows-down-level-devices"></a>6. Povolení služby Azure AD bezproblémové jednotné přihlašování pro zařízení se systémem Windows nižší úrovně

Pokud vaše organizace používá synchronizaci hodnot hash hesel nebo předávací ověřování pro přihlášení ke službě Azure AD, povolte Azure AD bez problémů pomocí jednotného přihlašování s touto metodou přihlášení k ověření zařízení se systémem Windows nižší https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso úrovně:. 

## <a name="7-set-azure-ad-policy-for-windows-down-level-devices"></a>7. Nastavení zásad Azure AD pro zařízení s nižší úrovní Windows

Pokud chcete zaregistrovat zařízení Windows na nižší úrovni, musíte zajistit, aby zásady služby Azure AD umožňovaly uživatelům registrovat zařízení. 

* Přihlaste se ke svému účtu v Azure Portal.
* Přejít na:  Azure Active Directory > zařízení > Nastavení zařízení
* Nastavte možnost Uživatelé můžou svá zařízení zaregistrovat do služby Azure AD.
* Kliknutí na Uložit

## <a name="8-add-azure-ad-endpoint-to-windows-down-level-devices"></a>8. Přidání koncového bodu Azure AD do zařízení Windows nižší úrovně

Přidejte koncový bod ověřování zařízení Azure AD do zón místního intranetu na zařízeních nižší úrovně Windows, abyste se vyhnuli zobrazování výzev k certifikátu při ověřování zařízení: https://device.login.microsoftonline.com 

Pokud používáte [bezproblémové přihlašování (SSO](how-to-connect-sso.md)), povolte taky možnost povolit aktualizace stavového řádku prostřednictvím skriptu v této zóně a přidat následující koncový bod: https://autologon.microsoftazuread-sso.com 

## <a name="9-install-microsoft-workplace-join-on-windows-down-level-devices"></a>9. Instalace aplikace Microsoft Workplace Join na zařízeních Windows nižší úrovně

Tento instalační program vytvoří v systému zařízení naplánovanou úlohu, která běží v kontextu uživatele. Úkol se aktivuje, když se uživatel přihlásí k Windows. Úloha se tiše připojí k zařízení pomocí Azure AD s přihlašovacími údaji uživatele po ověření pomocí integrovaného ověřování systému Windows. Stažení softwaru je v https://www.microsoft.com/download/details.aspx?id=53554. 

## <a name="10-configure-group-policy-to-allow-device-registration"></a>10. Konfigurace zásad skupiny pro povolení registrace zařízení

* Vytvořte objekt zásad skupiny ve službě Active Directory – Pokud ještě není vytvořený.
* Pojmenujte ji (připojení ke službě Azure AD Hybrid).
* Upravit & Přejít na:  Konfigurace počítače zásady > > Šablony pro správu > součásti systému Windows > registrace zařízení
* Aby  Registrace počítačů připojených k doméně jako zařízení
* Použijte a klikněte na OK.
* Propojte objekt zásad skupiny s vámi zvoleným umístěním (organizační jednotka, skupina zabezpečení nebo doména pro všechna zařízení).

>[!NOTE]
>2012R2 nastavení zásad v části **Konfigurace počítače zásady > > Šablony pro správu > součásti systému Windows > Workplace Join > automatické připojení klientských počítačů k síti** na pracovišti

## <a name="next-steps"></a>Další postup
[Konfigurace zpětného zápisu zařízení](how-to-connect-device-writeback.md)
