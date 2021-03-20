---
title: 'Azure AD Connect: hybridní úlohy konfigurace připojení ke službě Azure AD | Microsoft Docs'
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
ms.topic: how-to
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: da5cefbacbd3851d2609a687c1948d9bcba5ffae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88612465"
---
# <a name="post-configuration-tasks-for-hybrid-azure-ad-join"></a>Úlohy po dokončení konfigurace pro hybridní připojení k Azure AD

Po spuštění Azure AD Connect ke konfiguraci vaší organizace pro připojení k hybridní službě Azure AD existuje několik dalších kroků, které je potřeba dokončit, aby bylo možné instalaci dokončit.  Proveďte kroky, které platí pro vaše zařízení.

## <a name="1-configure-controlled-rollout-optional"></a>1. konfigurace řízeného zavedení (volitelné)
Všechna zařízení připojená k doméně se systémem Windows 10 a Windows Server 2016 se po dokončení všech kroků konfigurace automaticky zaregistrují ve službě Azure AD. Pokud upřednostňujete řízené zavedení místo této automatické registrace, můžete pomocí zásad skupiny selektivně povolit nebo zakázat automatické zavedení.  Tyto zásady skupiny by měly být nastavené před zahájením dalších kroků konfigurace:
* Vytvořte objekt zásad skupiny ve službě Active Directory.
* Pojmenujte ji (připojení ke službě Azure AD Hybrid).
* Upravit a přejít na: Konfigurace počítače > zásady > Šablony pro správu > součásti systému Windows > registrace zařízení.

>[!NOTE]
>2012R2 nastavení zásad v části **Konfigurace počítače zásady > > Šablony pro správu > součásti systému Windows > Workplace Join > automatické připojení klientských počítačů k síti na pracovišti**

* Povolit toto nastavení: Registrace počítačů připojených k doméně jako zařízení.
* Použijte a klikněte na OK.
* Propojte objekt zásad skupiny s vámi zvoleným umístěním (organizační jednotka, skupina zabezpečení nebo doména pro všechna zařízení).

## <a name="2-configure-network-with-device-registration-endpoints"></a>2. konfigurace sítě pomocí koncových bodů registrace zařízení
Ujistěte se, že k registraci do služby Azure AD jsou dostupné následující adresy URL z počítačů v síti vaší organizace:

* `https://enterpriseregistration.windows.net`
* `https://login.microsoftonline.com`
* `https://device.login.microsoftonline.com` 

## <a name="3-implement-wpad-for-windows-10-devices"></a>3. implementace protokolu WPAD pro zařízení s Windows 10
Pokud vaše organizace přistupuje k Internetu prostřednictvím odchozího proxy serveru, implementujte automatické zjišťování webových proxy serveru (WPAD), aby se počítače s Windows 10 mohly registrovat do Azure AD.

## <a name="4-configure-the-scp-in-any-forests-that-were-not-configured-by-azure-ad-connect"></a>4. Nakonfigurujte spojovací bod služby v jakékoli doménové struktuře, která nebyla nakonfigurovaná pomocí Azure AD Connect 

Spojovací bod služby (SCP) obsahuje informace o tenantovi Azure AD, které budou vaše zařízení používat k automatické registraci.  Spusťte skript prostředí PowerShell, ConfigureSCP.ps1, který jste si stáhli z Azure AD Connect.

## <a name="5-configure-any-federation-service-that-was-not-configured-by-azure-ad-connect"></a>5. Nakonfigurujte jakoukoli federační službu, která nebyla nakonfigurovaná pomocí Azure AD Connect

Pokud vaše organizace používá pro přihlášení ke službě Azure AD službu Federation Service, musí pravidla deklarace identity ve vztahu důvěryhodnosti předávající strany Azure AD umožňovat ověřování zařízení. Pokud používáte federaci s AD FS, přečtěte si v [tématu AD FS Help](https://aka.ms/aadrptclaimrules) , kde můžete vygenerovat pravidla deklarace identity. Pokud používáte federační řešení od jiného výrobce než Microsoftu, kontaktujte tohoto poskytovatele s pokyny.  

>[!NOTE]
>Pokud máte zařízení Windows nižší úrovně, musí služba při přijímání požadavků na vztah důvěryhodnosti služby Azure AD podporovat vydávání deklarací authenticationmethod a wiaormultiauthn. V AD FS byste měli přidat pravidlo transformace vystavování, které projde metodou ověřování.

## <a name="6-enable-azure-ad-seamless-sso-for-windows-down-level-devices"></a>6. povolení služby Azure AD bezproblémové jednotné přihlašování pro zařízení se systémem Windows nižší úrovně

Pokud vaše organizace používá synchronizaci hodnot hash hesel nebo předávací ověřování pro přihlášení ke službě Azure AD, povolte Azure AD bez problémů pomocí jednotného přihlašování s touto metodou přihlášení k ověření zařízení se systémem Windows nižší úrovně:  https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso . 

## <a name="7-set-azure-ad-policy-for-windows-down-level-devices"></a>7. nastavení zásad Azure AD pro zařízení nižší úrovně Windows

Pokud chcete zaregistrovat zařízení Windows na nižší úrovni, musíte zajistit, aby zásady služby Azure AD umožňovaly uživatelům registrovat zařízení. 

* Přihlaste se ke svému účtu v Azure Portal.
* Přejít na: Azure Active Directory > zařízení > nastavení zařízení
* Nastavte možnost Uživatelé můžou svá zařízení zaregistrovat do služby Azure AD.
* Kliknutí na Uložit

## <a name="8-add-azure-ad-endpoint-to-windows-down-level-devices"></a>8. Přidání koncového bodu Azure AD do zařízení se škálováním na Windows nižší úrovně

Přidejte koncový bod ověřování zařízení Azure AD do zón místního intranetu na zařízeních nižší úrovně Windows, abyste se vyhnuli zobrazování výzev k certifikátu při ověřování zařízení: `https://device.login.microsoftonline.com` 

Pokud používáte [bezproblémové přihlašování (SSO](how-to-connect-sso.md)), povolte taky možnost povolit aktualizace stavového řádku prostřednictvím skriptu v této zóně a přidat následující koncový bod: `https://autologon.microsoftazuread-sso.com` 

## <a name="9-install-microsoft-workplace-join-on-windows-down-level-devices"></a>9. instalace aplikace Microsoft Workplace Join na zařízeních nižší úrovně Windows

Tento instalační program vytvoří v systému zařízení naplánovanou úlohu, která běží v kontextu uživatele. Úkol se aktivuje, když se uživatel přihlásí k Windows. Úloha se tiše připojí k zařízení pomocí Azure AD s přihlašovacími údaji uživatele po ověření pomocí integrovaného ověřování systému Windows. Stažení softwaru je v https://www.microsoft.com/download/details.aspx?id=53554 . 

## <a name="10-configure-group-policy-to-allow-device-registration"></a>10. konfigurace zásad skupiny pro povolení registrace zařízení

Informace o tom, jak pro jednotlivá zařízení dovolit hybridní připojení ke službě Azure AD, najdete v tématu [řízené ověřování hybridního připojení k Azure AD](../devices/hybrid-azuread-join-control.md).

## <a name="next-steps"></a>Další kroky
[Konfigurace zpětného zápisu zařízení](how-to-connect-device-writeback.md)
