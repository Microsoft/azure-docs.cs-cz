---
title: Modul plug-in Microsoft Enterprise SSO pro zařízení Apple
titleSuffix: Microsoft identity platform | Azure
description: Přečtěte si informace o modulu plug-in Azure Active Directory SSO Microsoftu pro zařízení s iOS a macOS.
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
ms.openlocfilehash: a6af1a78582ebf62aed82bad8fa06cac37f511ea
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "80982578"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Microsoft Enterprise SSO – modul plug-in pro zařízení Apple (Preview)

> [!NOTE]
> Tato funkce je ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

*Modul plug-in Microsoft Enterprise SSO pro zařízení Apple* poskytuje jednotné přihlašování (SSO) pro účty Azure Active Directory (Azure AD) ve všech aplikacích, které podporují funkci [podnikového jednotného přihlašování](https://developer.apple.com/documentation/authenticationservices) od společnosti Apple. Společnost Microsoft úzce spolupracuje s Applem při vývoji tohoto modulu plug-in, aby zvýšila použitelnost vaší aplikace a současně poskytovala nejlepší ochranu, kterou může společnost Apple a Microsoft poskytnout.

V této Public Preview vydané verzi je modul plug-in jednotného přihlašování (SSO) Enterprise dostupný jenom pro zařízení s iOS a distribuuje se do určitých aplikací Microsoftu.

Náš první způsob použití modulu plug-in jednotného přihlašování (SSO) je naše nová funkce [režimu sdíleného zařízení](msal-ios-shared-devices.md) .

## <a name="features"></a>Funkce

Modul plug-in Microsoft Enterprise SSO pro zařízení Apple nabízí tyto výhody:

- Poskytuje jednotné přihlašování pro účty Azure AD napříč všemi aplikacemi, které podporují funkci podnikového jednotného přihlašování od společnosti Apple.
- Služba se automaticky doručí do Microsoft Authenticator a může ji povolit jakékoli řešení pro správu mobilních zařízení (MDM).

## <a name="requirements"></a>Požadavky

Chcete-li použít modul plug-in jednotného přihlašování Microsoft Enterprise pro zařízení Apple:

- v zařízení musí být nainstalovaná iOS 13,0 nebo vyšší.
- V zařízení musí být nainstalovaná aplikace Microsoftu, která poskytuje modul plug-in Microsoft Enterprise SSO pro zařízení Apple. Pro Public Preview tyto aplikace zahrnují [aplikaci Microsoft Authenticator](../user-help/user-help-auth-app-overview.md).
- Zařízení musí být zaregistrované v MDM (například pomocí Microsoft Intune).
- Aby bylo možné povolit modul plug-in Microsoft Enterprise SSO pro zařízení Apple na zařízení, musí být do zařízení vložena konfigurace. Toto omezení zabezpečení je vyžadováno společností Apple.

## <a name="enable-the-sso-extension-with-mobile-device-management-mdm"></a>Povolení rozšíření jednotného přihlašování se správou mobilních zařízení (MDM)

Aby bylo možné povolit modul plug-in Microsoft Enterprise SSO pro zařízení Apple, musí být vaše zařízení odesíláno signálem přes službu MDM. Vzhledem k tomu, že společnost Microsoft obsahuje v [aplikaci Microsoft Authenticator](..//user-help/user-help-auth-app-overview.md)modul plug-in jednotného přihlašování (SSO), pomocí své správy mobilních zařízení nakonfigurujte aplikaci tak, aby povolovala modul plug-in Microsoft Enterprise SSO.

Ke konfiguraci modulu plug-in Microsoft Enterprise SSO pro zařízení Apple použijte následující parametry:

- **Typ**: přesměrování
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

Jako službu MDM můžete použít Microsoft Intune k usnadnění konfigurace modulu plug-in Microsoft Enterprise SSO. Další informace najdete v dokumentaci ke [konfiguraci Intune](https://docs.microsoft.com/intune/configuration/ios-device-features-settings).

## <a name="using-the-sso-extension-in-your-application"></a>Použití rozšíření jednotného přihlašování v aplikaci

[Knihovna Microsoft Authentication Library (MSAL) pro zařízení Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) verze 1.1.0 a vyšší podporuje modul plug-in Microsoft Enterprise SSO pro zařízení Apple.

Pokud chcete podporovat režim sdíleného zařízení, který poskytuje modul plug-in Microsoft Enterprise SSO pro zařízení Apple, ujistěte se, že vaše aplikace používají určenou minimální požadovanou verzi MSAL.

## <a name="next-steps"></a>Další kroky

Další informace o režimu sdíleného zařízení v iOS najdete v tématu [režim sdíleného zařízení pro zařízení s iOS](msal-ios-shared-devices.md).
