---
title: 'Azure AD Connect: Úlohy konfigurace hybridní Azure AD join post | Microsoft Docs'
description: Tento dokument podrobnosti úlohy konfigurace post potřebné k dokončení připojení k hybridní Azure AD
services: active-directory
documentationcenter: ''
author: anandyadavmsft
manager: samueld
editor: billmath
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.component: hybrid
ms.author: anandyadavmsft
ms.openlocfilehash: b337594f53c03ca1ddbc230473fe191bc968c92b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593295"
---
# <a name="post-configuration-tasks-for-hybrid-azure-ad-join"></a>Úlohy po konfiguraci pro připojení k hybridní Azure AD

Po spuštění Azure AD Connect ke konfiguraci vaší organizace pro připojení k hybridní Azure AD, existuje několik dalších kroků, které je třeba provést pro dokončení této instalace.  Proveďte pouze kroky, které platí pro vaše zařízení.

## <a name="1-configure-controlled-rollout-optional"></a>1. Konfigurace řízené zavedení (volitelné)
Všechny připojené k doméně zařízení se systémem Windows 10 a Windows Server 2016 automaticky zaregistrovat s Azure AD, až po dokončení všech konfiguračních kroků. Pokud dáváte přednost řízené zavedení, nikoli automatickou registraci, můžete selektivně povolit nebo zakázat automatické zavedení zásad skupiny.  Tato zásada skupiny by se měla stanovit před spuštěním další konfigurační kroky: Azure AD
* Vytvoření objektu zásad skupiny ve službě Active Directory.
* Pojmenujte ji (ex hybridní Azure AD join).
* Upravit & Přejít na: Konfigurace počítače > zásady > šablony pro správu > součásti systému Windows > registrace zařízení.

>[!NOTE]
>Pro 2012R2 nastavení zásad jsou v **konfigurace počítače > zásady > šablony pro správu > součásti systému Windows > připojení k pracovní ploše > automaticky klientské počítače se připojení k síti na pracovišti**

* Toto nastavení zakázat: registrace počítačů připojených k doméně jako zařízení.
* Použít a klikněte na tlačítko OK.
* Tento OBJEKT propojte k umístění podle vaší volby (organizační jednotky, zabezpečení, skupiny, nebo do domény pro všechna zařízení).

## <a name="2-configure-network-with-device-registration-endpoints"></a>2. Konfigurace sítě s koncovými body registrace zařízení
Ujistěte se, že následující adresy URL jsou přístupné z počítače uvnitř sítě vaší organizace pro registraci do služby Azure AD:

* https://enterpriseregistration.windows.net
* https://login.microsoftonline.com
* https://device.login.microsoftonline.com 

## <a name="3-implement-wpad-for-windows-10-devices"></a>3. Implementace zařízení WPAD pro Windows 10
Pokud vaše organizace přistupovat k Internetu prostřednictvím odchozího proxy serveru, implementujte Proxy Auto-Discovery WPAD (Web) Chcete-li povolit počítače s Windows 10 k registraci do služby Azure AD.

## <a name="4-configure-the-scp-in-any-forests-that-were-not-configured-by-azure-ad-connect"></a>4. Konfigurace spojovací bod služby ve všech doménových strukturách, které nebyly nakonfigurovány přes Azure AD Connect 

Spojovací bod služby (SCP) obsahuje vaše informace o Azure AD klienta, který se použije pro automatickou registraci zařízení.  Spusťte skript prostředí PowerShell, ConfigureSCP.ps1, který jste stáhli z Azure AD Connect.

## <a name="5-configure-any-federation-service-that-was-not-configured-by-azure-ad-connect"></a>5. Nakonfigurujte federační služby, který nebyl nakonfigurován přes Azure AD Connect

Pokud vaše organizace používá služby FS se přihlásit k Azure AD, pravidla deklarace identity v důvěryhodnosti předávající strany služby Azure AD, musíte povolit ověřování zařízení. Pokud používáte federační službou AD FS, přejděte na [Nápověda k AD FS](https://aka.ms/aadrptclaimrules) ke generování pravidel deklarace identity. Pokud používáte řešení jiných společností než Microsoft federation, požádejte o pokyny tohoto zprostředkovatele.  

>[!NOTE]
>Pokud máte zařízení s Windows nižší úrovně, musí podporovat službu vystavování deklarací identity authenticationmethod a wiaormultiauthn při přijímání požadavků do vztahu důvěryhodnosti služby Azure AD. Ve službě AD FS měli byste přidat pravidel transformace pro vystavování, předává prostřednictvím metody ověřování.

## <a name="6-enable-azure-ad-seamless-sso-for-windows-down-level-devices"></a>6. Povolit Azure AD bezproblémové jednotného přihlašování pro nižší úrovně zařízení se systémem Windows

Pokud vaše organizace používá synchronizaci hodnoty Hash hesla nebo předávací ověřování pro přihlášení k Azure AD, povolte Azure AD bezproblémové SSO pomocí dané metody přihlašování k ověření zařízení se systémem Windows nižší úrovně: https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso. 

## <a name="7-set-azure-ad-policy-for-windows-down-level-devices"></a>7. Nastavení zásad služby Azure AD pro zařízení se systémem Windows nižší úrovně

Chcete-li zaregistrovat zařízení se systémem Windows nižší úrovně, ujistěte se, že zásady služby Azure AD umožňuje uživatelům zaregistrovat zařízení. 

* Přihlaste se ke svému účtu na portálu Azure.
* Přejděte na: Azure Active Directory > zařízení > Nastavení zařízení
* Ke všem nastavte "Uživatelé mohou registrovat svá zařízení s Azure AD".
* Kliknutí na Uložit

## <a name="8-add-azure-ad-endpoint-to-windows-down-level-devices"></a>8. Přidání koncového bodu Azure AD pro zařízení se systémem Windows nižší úrovně

Přidáte koncový bod ověřování zařízení služby Azure AD do místní zóny intranetu na vaše zařízení s Windows nižší úrovně předejdete výzev ohledně certifikátů při ověřování zařízení: https://device.login.microsoftonline.com 

Pokud používáte [bezproblémové SSO](https://aka.ms/hybrid/sso), také povolit "Povolit aktualizace stavového řádku pomocí skriptu" na zóny a přidejte následující koncový bod: https://autologon.microsoftazuread-sso.com 

## <a name="9-install-microsoft-workplace-join-on-windows-down-level-devices"></a>9. Nainstalujte Microsoft k síti na pracovišti na zařízení se systémem Windows nižší úrovně

Tento instalační program vytvoří naplánovanou úlohu na zařízení systému, který běží v kontextu uživatele. Úloha se aktivuje, když se uživatel přihlásí systému Windows. Úloha bezobslužně připojí zařízení s Azure AD s pověřeními uživatele po ověřování pomocí integrovaného ověřování systému Windows. Stažení softwaru je v https://www.microsoft.com/en-us/download/details.aspx?id=53554. 

## <a name="10-configure-group-policy-to-allow-device-registration"></a>10. Konfigurace zásad skupiny umožňující registraci zařízení

* Vytvoření objektu zásad skupiny v Active Directory – Pokud ještě není vytvořena.
* Pojmenujte ji (ex hybridní Azure AD join).
* Upravit & Přejít na: Konfigurace počítače > zásady > šablony pro správu > součásti systému Windows > registrace zařízení
* Povolit: Registrace počítačů připojených k doméně jako zařízení
* Použít a klikněte na tlačítko OK.
* Tento OBJEKT propojte k umístění podle vaší volby (organizační jednotky, zabezpečení, skupiny, nebo do domény pro všechna zařízení).

>[!NOTE]
>Pro 2012R2 nastavení zásad jsou v **konfigurace počítače > zásady > šablony pro správu > součásti systému Windows > připojení k pracovní ploše > automaticky klientské počítače se připojení k síti na pracovišti**

## <a name="next-steps"></a>Další postup
[Nakonfigurovat zpětný zápis zařízení.](./active-directory-aadconnect-feature-device-writeback.md)
