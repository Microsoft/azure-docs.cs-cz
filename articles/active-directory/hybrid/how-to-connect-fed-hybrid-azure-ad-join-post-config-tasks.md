---
title: 'Azure AD Connect: Úkoly konfigurace po připojení k hybridní službě Azure AD. | Dokumentace Microsoftu'
description: Tento dokument podrobně popisuje úkoly po konfiguraci potřebné k dokončení připojení k hybridní službě Azure AD
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: billmath
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 16fc7f1bb69efe94ce87f213627b78a4afa0fcc2
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52999235"
---
# <a name="post-configuration-tasks-for-hybrid-azure-ad-join"></a>Úlohy po dokončení konfigurace pro hybridní připojení k Azure AD

Po spuštění služby Azure AD Connect ke konfiguraci vaší organizaci pro připojení k hybridní službě Azure AD existuje několik dalších kroků, které je třeba provést pro dokončení této instalace.  Proveďte pouze kroky, které platí pro vaše zařízení.

## <a name="1-configure-controlled-rollout-optional"></a>1. Konfigurace řízeně uvádět (volitelné)
Všechna zařízení připojená k doméně s Windows 10 a Windows serveru 2016 automaticky zaregistrovat v Azure AD po dokončení všech kroků konfigurace. Pokud dáváte přednost řízeně uvádět, nikoli automatickou registraci, můžete použít zásady skupiny výběrově povolit nebo zakázat automatické zavedení.  Tyto zásady skupiny by měl nastavit před spuštěním další konfigurační kroky: Azure AD
* Vytvoření objektu zásad skupiny ve službě Active Directory.
* Pojmenujte ho (ex – hybridní službě Azure AD join).
* Upravit & Přejít na: Konfigurace počítače > zásady > šablony pro správu > součásti Windows > registrace zařízení.

>[!NOTE]
>Pro 2012R2 nastavení zásad jsou v **konfigurace počítače > zásady > šablony pro správu > součásti Windows > připojení k pracovní ploše > automaticky klientské počítače se připojení k síti na pracovišti**

* Toto nastavení zakázat: registrace připojené k doméně počítače jako zařízení.
* Použít a klikněte na tlačítko OK.
* Propojte objekt zásad skupiny do umístění podle vašeho výběru (organizační jednotky, zabezpečení, skupiny, nebo do domény pro všechna zařízení).

## <a name="2-configure-network-with-device-registration-endpoints"></a>2. Konfigurace sítě s koncovými body registrace zařízení
Ujistěte se, že jsou přístupné z počítače uvnitř sítě vaší organizace pro registraci do služby Azure AD následující adresy URL:

* https://enterpriseregistration.windows.net
* https://login.microsoftonline.com
* https://device.login.microsoftonline.com 

## <a name="3-implement-wpad-for-windows-10-devices"></a>3. Implementace zařízení WPAD pro Windows 10
Pokud vaše organizace má přístup k Internetu prostřednictvím odchozího proxy serveru, implementujte Proxy Auto-Discovery WPAD (Web) umožňující počítače s Windows 10 k registraci do služby Azure AD.

## <a name="4-configure-the-scp-in-any-forests-that-were-not-configured-by-azure-ad-connect"></a>4. Konfigurace spojovacího bodu služby ve všech doménových strukturách, které nebyly nakonfigurovány pomocí služby Azure AD Connect 

Spojovací bod služby (SCP) obsahuje informace o tenantovi Azure AD, který se použije pro automatickou registraci zařízení.  Spusťte skript prostředí PowerShell, ConfigureSCP.ps1, který jste stáhli ze služby Azure AD Connect.

## <a name="5-configure-any-federation-service-that-was-not-configured-by-azure-ad-connect"></a>5. Nakonfigurujte federační služby, který nebyl nakonfigurován pomocí služby Azure AD Connect

Pokud vaše organizace používá pro přihlášení k Azure AD službou federation service, pravidla deklarace identity ve vztahu důvěryhodnosti přijímající strany Azure AD, musíte povolit ověřování zařízení. Pokud používáte federační službou AD FS, přejděte na [nápovědy k AD FS](https://aka.ms/aadrptclaimrules) generovat pravidla deklarací identity. Pokud používáte řešení federace jiného subjektu než Microsoft, požádejte o pokyny daného poskytovatele.  

>[!NOTE]
>Pokud máte zařízení Windows nižší úrovně, služba musí podporovat vystavování deklarací identity authenticationmethod a wiaormultiauthn při přijímání požadavků na vztah důvěryhodnosti Azure AD. Ve službě AD FS měli byste přidat pravidel transformace pro vystavování, která předá prostřednictvím metody ověřování.

## <a name="6-enable-azure-ad-seamless-sso-for-windows-down-level-devices"></a>6. Povolení zařízením s Azure AD bezproblémové jednotné přihlašování pro Windows nižší úrovně

Pokud vaše organizace používá synchronizaci hodnot Hash hesel nebo předávací ověřování pro přihlášení k Azure AD, jednotné přihlašování Azure AD bezproblémové pomocí této metody přihlašování k ověřování zařízení Windows nižší úrovně: https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso. 

## <a name="7-set-azure-ad-policy-for-windows-down-level-devices"></a>7. Nastavení zásad služby Azure AD pro zařízení s Windows nižší úrovně

K registraci zařízení s Windows nižší úrovně, budete muset Ujistěte se, že zásady služby Azure AD umožňuje uživatelům zaregistrovat zařízení. 

* Přihlášení ke svému účtu na webu Azure Portal.
* Přejděte na: Azure Active Directory > zařízení > Nastavení zařízení
* Ke všem nastavte "Uživatelé můžou registrovat svoje zařízení s Azure AD".
* Kliknutí na Uložit

## <a name="8-add-azure-ad-endpoint-to-windows-down-level-devices"></a>8. Přidání koncového bodu Azure AD do zařízení Windows nižší úrovně

Koncový bod ověřování zařízení Azure AD přidejte do zóny místního intranetu v zařízeních Windows nižší úrovně se vyhnout výzev ohledně certifikátů při ověřování zařízení: https://device.login.microsoftonline.com 

Pokud používáte [bezproblémového jednotného přihlašování](how-to-connect-sso.md), také povolit "Povolit aktualizace stavového řádku prostřednictvím skriptu" v této zóně a přidejte následující koncový bod: https://autologon.microsoftazuread-sso.com 

## <a name="9-install-microsoft-workplace-join-on-windows-down-level-devices"></a>9. Nainstalujte Microsoft síti na pracovišti na zařízeních s Windows nižší úrovně

Tento instalační program vytvoří naplánovanou úlohu v systému zařízení, která běží v kontextu uživatele. Úloha se aktivuje, když uživatel přihlásí do Windows. Úloha tiše připojí zařízení s Azure AD s přihlašovacími údaji uživatele po ověření pomocí integrovaného ověřování Windows. Stažení softwaru je v https://www.microsoft.com/download/details.aspx?id=53554. 

## <a name="10-configure-group-policy-to-allow-device-registration"></a>10. Nakonfigurovat zásady skupiny pro povolení registrace zařízení

* Vytvoření objektu zásad skupiny ve službě Active Directory – Pokud ještě není vytvořena.
* Pojmenujte ho (ex – hybridní službě Azure AD join).
* Upravit & Přejít na: Konfigurace počítače > zásady > šablony pro správu > součásti Windows > registrace zařízení
* Povolit: Registrace připojené k doméně počítače jako zařízení
* Použít a klikněte na tlačítko OK.
* Propojte objekt zásad skupiny do umístění podle vašeho výběru (organizační jednotky, zabezpečení, skupiny, nebo do domény pro všechna zařízení).

>[!NOTE]
>Pro 2012R2 nastavení zásad jsou v **konfigurace počítače > zásady > šablony pro správu > součásti Windows > připojení k pracovní ploše > automaticky klientské počítače se připojení k síti na pracovišti**

## <a name="next-steps"></a>Další postup
[Nakonfigurujte zpětný zápis zařízení](how-to-connect-device-writeback.md)
