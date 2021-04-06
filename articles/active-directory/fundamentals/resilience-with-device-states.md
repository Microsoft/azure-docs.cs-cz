---
title: Odolnost sestavení pomocí stavů zařízení v Azure Active Directory
description: Příručka pro architekty a správce IT k vytváření odolnosti pomocí stavů zařízení
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d476be7a417cfc31cca76d3409074aaaa281a56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724606"
---
# <a name="build-resilience-with-device-states"></a>Odolnost sestavení se stavy zařízení

Díky povolení [stavů zařízení](../devices/overview.md) ve službě Azure AD můžou správci vytvářet [zásady podmíněného přístupu](../conditional-access/overview.md) , které řídí přístup k aplikacím na základě stavu zařízení. Přidaná výhoda zařízení spočívá v tom, že splňuje požadavky silného ověřování pro přístup k prostředkům, což snižuje nároky na ověřování MFA a zlepšuje odolnost. 

Následující vývojový diagram představuje různé způsoby připojení zařízení do služby Azure AD, která umožňují stav zařízení. Ve vaší organizaci můžete použít více než jeden.

![Vývojový diagram pro výběr stavů zařízení](./media/resilience-with-device-states/admin-resilience-devices.png)

Když použijete [stavy zařízení](../devices/overview.md), uživatelé ve většině případů budou mít jednotné přihlašování k prostředkům prostřednictvím [primárního obnovovacího tokenu](../devices/concept-primary-refresh-token.md) (PRT). PRT obsahuje deklarace identity uživatele a zařízení a dá se použít k získání tokenů ověřování pro přístup k aplikacím ze zařízení. PRT je platná 14 dní a průběžně se prodlouží, dokud uživatel aktivně používá zařízení a poskytuje uživatelům odolný zážitek. PRT může také získat deklarace identity Multi-Factor Authentication několika způsoby. Další informace najdete v tématu [kdy PRT získá deklaraci MFA](../devices/concept-primary-refresh-token.md).

## <a name="how-do-device-states-help"></a>Jak se stav zařízení poslouží?

Pokud se k žádosti o přístup k aplikaci, jejímu zařízení, relaci a deklaracím MFA používá PRT, důvěřuje Azure AD. Když správci vytvoří zásady, které vyžadují ovládací prvek založený na zařízení nebo řízení ověřování Multi-Factor Authentication, je možné požadavek na zásady splnit přes stav zařízení bez pokusu o vícefaktorové ověřování. Uživatelům se na stejném zařízení nezobrazí další výzvy pro službu Multi-Factor Authentication. Tím se zvyšuje odolnost vůči narušení služby Azure MFA nebo její závislosti, jako jsou místní poskytovatelé telekomunikace.

## <a name="how-do-i-implement-device-states"></a>Návody implementovat stavy zařízení?

* Povolte [připojené k hybridní službě Azure AD](../devices/hybrid-azuread-join-plan.md) a [připojení k Azure AD](../devices/azureadjoin-plan.md) pro zařízení s Windows vlastněná společností a vyžadovat, aby se připojili, pokud je to možné. Pokud je to možné, vyžadovat, aby byly zaregistrované.

  Pokud ve vaší organizaci existují starší verze Windows, upgradujte tato zařízení na použití Windows 10.

* Standardizace přístupu prohlížeče uživatelů k používání [Microsoft Edge](/deployedge/microsoft-edge-security-identity) nebo Google Chrome s [podporovanými](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji) [rozšířeními](https://chrome.google.com/webstore/detail/office/ndjpnladcallmjemlbaebfadecfhkepb) , která umožňují bezproblémové přihlašování k webovým aplikacím pomocí PRT.

* Pro osobní nebo vlastní zařízení s iOS a Androidem nasaďte [aplikaci Microsoft Authenticator](../user-help/user-help-auth-app-overview.md). Kromě funkcí Multi-Factor Authentication a možností přihlašování bez hesla umožňuje Microsoft Authenticator aplikace v rámci zprostředkovaných [ověřování](../develop/msal-android-single-sign-on.md) jednotné přihlašování s menším počtem výzev k ověřování pro koncové uživatele.

* U osobních zařízení s iOS a Androidem, která vlastní firma nebo společnost, používá [správu mobilních aplikací](/mem/intune/apps/app-management) k zabezpečenému přístupu k prostředkům společnosti s menšími nároky na ověření. 

* [Použijte modul plug-in Microsoft Enterprise SSO pro zařízení Apple (Preview)](../develop/apple-sso-plugin.md). Tím se zařízení zaregistruje a poskytuje jednotné přihlašování přes prohlížeč a nativní aplikace služby Azure AD. 

## <a name="next-steps"></a>Další kroky
Prostředky odolnosti pro správce a architekty
 
* [Odolnost sestavení se správou přihlašovacích údajů](resilience-in-credentials.md)

* [Odolnost sestavení pomocí vyhodnocení průběžného přístupu (CAE)](resilience-with-continuous-access-evaluation.md)

* [Odolnost sestavení při ověřování externích uživatelů](resilience-b2b-authentication.md)

* [Odolnost sestavení v hybridním ověřování](resilience-in-hybrid.md)

* [Odolnost sestavení v přístupu aplikace s proxy aplikací](resilience-on-premises-access.md)


Prostředky odolnosti pro vývojáře

* [Sestavování odolnosti IAM ve vašich aplikacích](resilience-app-development-overview.md)

* [Odolnost sestavení v systémech CIAM](resilience-b2c.md)