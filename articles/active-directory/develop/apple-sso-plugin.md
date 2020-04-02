---
title: Modul plug-in Microsoft Enterprise SSO pro zařízení Apple
titleSuffix: Microsoft identity platform | Azure
description: Přečtěte si o modulu plug-in Microsoft U Azure Active Directory Pro zařízení s rozhraním ASo pro iOS a macOS.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 7233a0bba5cf30eab018bfa744a7322303300604
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550315"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Modul plug-in Microsoft Enterprise SSO pro zařízení Apple (preview)

> [!NOTE]
> Tato funkce je ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

*Modul plug-in jednotného přihlašování microsoft enterprise pro zařízení Apple* poskytuje jednotné přihlašování (SSO) pro účty Služby Active Directory ve všech aplikacích, které podporují funkci [jednotného přihlašování společnosti](https://developer.apple.com/documentation/authenticationservices) Apple Enterprise. Společnost Microsoft úzce spolupracovala se společností Apple na vývoji tohoto modulu plug-in, aby zvýšila použitelnost vaší aplikace a zároveň poskytla nejlepší ochranu, kterou mohou společnosti Apple a Microsoft poskytnout.

V této verzi Public Preview je modul plug-in enterprise sso k dispozici pouze pro zařízení se systémem iOS a distribuuje se v některých aplikacích společnosti Microsoft.

Naše první použití modulu plug-in Enterprise SSO je s naší novou funkcí [režimu sdíleného zařízení.](msal-ios-shared-devices.md)

## <a name="features"></a>Funkce

Modul plug-in Microsoft Enterprise SSO pro zařízení Apple nabízí následující výhody:

- Poskytuje jednotné přihlašování pro účty služby Active Directory ve všech aplikacích, které podporují funkci jednotného přihlašování k podniku Apple.
- Automaticky dodané v microsoft authenticatoru a může být povoleno libovolným řešením správy mobilních zařízení (MDM).

## <a name="requirements"></a>Požadavky

Použití modulu plug-in Microsoft Enterprise SSO pro zařízení Apple:

- na zařízení musí být nainstalován iOS 13.0 nebo vyšší.
- V zařízení musí být nainstalována aplikace společnosti Microsoft, která poskytuje modul plug-in microsoft enterprise sso pro zařízení Apple. Pro verzi Public Preview tyto aplikace zahrnují [aplikaci Microsoft Authenticator](../user-help/user-help-auth-app-overview.md).
- Zařízení musí být registrované v MDM (například s Microsoft Intune).
- Konfigurace musí být zasunutdo zařízení povolit Microsoft Enterprise SSO plug-in pro zařízení Apple v zařízení. Toto bezpečnostní omezení vyžaduje společnost Apple.

## <a name="enable-the-sso-extension-with-mobile-device-management-mdm"></a>Povolení rozšíření s přiřazováním zařízení pomocí správy mobilních zařízení (MDM)

Chcete-li povolit modul plug-in Microsoft Enterprise SSO pro zařízení Apple, musí být vaše zařízení odeslána signál prostřednictvím služby MDM. Vzhledem k tomu, že Společnost Microsoft obsahuje modul plug-in enterprise spřiso v [aplikaci Microsoft Authenticator](..//user-help/user-help-auth-app-overview.md), použijte mdm ke konfiguraci aplikace tak, aby povolila modul plug-in Microsoft Enterprise SSO.

Ke konfiguraci modulu plug-in Microsoft Enterprise SSO pro zařízení Apple použijte následující parametry:

- **Typ**: Přesměrování
- **ID rozšíření**:`com.microsoft.azureauthenticator.ssoextension`
- **ID týmu**:`SGGM6D27TK`
- **Adresy URL**:
  - `https://login.microsoftonline.com`
  - `https://login.windows.net`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`

Microsoft Intune můžete použít jako službu MDM pro usnadnění konfigurace modulu plug-in Microsoft Enterprise SSO. Další informace najdete v [dokumentaci ke konfiguraci Intune](https://docs.microsoft.com/intune/configuration/ios-device-features-settings).

## <a name="using-the-sso-extension-in-your-application"></a>Použití rozšíření s přihlašováním k opětovnému přihlašování ve vaší aplikaci

[Knihovna ověřování Microsoft (MSAL) pro zařízení Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) verze 1.1.0 a vyšší podporuje modul plug-in Microsoft Enterprise SSO pro zařízení Apple.

Pokud chcete podporovat režim sdíleného zařízení poskytovaný modulem plug-in jednotného přihlašování microsoft enterprise pro zařízení Apple, ujistěte se, že vaše aplikace používají zadanou minimální požadovanou verzi MSAL.

## <a name="next-steps"></a>Další kroky

Další informace o režimu sdíleného zařízení v systému iOS najdete [v tématu Režim sdíleného zařízení pro zařízení se systémem iOS](msal-ios-shared-devices.md).
