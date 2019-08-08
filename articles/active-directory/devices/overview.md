---
title: Co je identita zařízení v Azure Active Directory?
description: Přečtěte si, jak vám Správa identit zařízení může pomáhat při správě zařízení, která přistupují k prostředkům ve vašem prostředí.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1d7d2ad6adefb83c3c313063a9e630458ccebc5
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851773"
---
# <a name="what-is-a-device-identity"></a>Co je identita zařízení?

Díky rozšiřování zařízení všech tvarů a velikostí a pojmu Přineste si vlastní zařízení (BYOD) se profesionálům v oblasti IT setkávají se dvěma trochu protichůdnými cíli:

- Umožnění produktivity koncových uživatelů bez ohledu na to, kdy
- Ochrana prostředků organizace

Pro ochranu těchto prostředků musí IT pracovníci nejdřív spravovat identity zařízení. Zaměstnanci IT můžou sestavovat identitu zařízení pomocí nástrojů, jako je Microsoft Intune, abyste zajistili splnění standardů zabezpečení a dodržování předpisů. Azure Active Directory (Azure AD) umožňuje jednotné přihlašování k zařízením, aplikacím a službám odkudkoli přes tato zařízení.

- Uživatelé získají přístup k potřebným prostředkům vaší organizace. 
- Vaši pracovníci IT získají ovládací prvky, které potřebují k zabezpečení vaší organizace.

Správa identit zařízení je základem pro [podmíněný přístup na základě zařízení](../conditional-access/require-managed-devices.md). Pomocí zásad podmíněného přístupu na základě zařízení můžete zajistit, aby byl přístup k prostředkům ve vašem prostředí možný jenom se spravovanými zařízeními.

## <a name="getting-devices-in-azure-ad"></a>Získávají se zařízení v Azure AD.

Pokud chcete v Azure AD získat zařízení, máte několik možností:

- **Registrace Azure AD**
   - Zařízení, která jsou registrovaná v Azure AD, jsou obvykle osobně vlastněná nebo mobilní zařízení a jsou přihlášená pomocí osobního účet Microsoft nebo jiného místního účtu.
      - Windows 10
      - iOS
      - Android
      - MacOS
- **Připojeno k Azure AD**
   - Zařízení, která jsou připojená k Azure AD, jsou vlastněna organizací a přihlásili jste se k účtu Azure AD patřícímu do této organizace. Existují jenom v cloudu.
      - Windows 10 
- **k hybridní službě Azure AD**.
   - Zařízení, která jsou připojená k hybridní službě Azure AD, jsou vlastněna organizací a přihlásili jste se k účtu Azure AD patřícímu do této organizace. Existují v cloudu i v místním prostředí.
      - Windows 7, 8,1 nebo 10
      - Windows Server 2008 nebo novější

![Zařízení zobrazená v okně zařízení Azure AD](./media/overview/azure-ad-devices-all-devices-overview.png)

## <a name="device-management"></a>Správa zařízení

Zařízení ve službě Azure AD je možné spravovat pomocí nástrojů pro správu mobilních zařízení (MDM), jako je Microsoft Intune, System Center Configuration Manager Zásady skupiny (připojení k hybridní službě Azure AD), nástrojů pro správu mobilních aplikací (MAM) nebo jiných nástrojů třetích stran.

## <a name="resource-access"></a>Přístup k prostředkům

Registrace a spojování umožní uživatelům bezproblémové přihlašování (SSO) ke cloudovým prostředkům a správcům možnost uplatňovat zásady podmíněného přístupu na tyto prostředky. 

Zařízení, která jsou připojená k Azure AD nebo hybridní služba Azure AD, využívají výhody jednotného přihlašování k místním prostředkům vaší organizace i k prostředkům cloudu. Další informace najdete v článku [o tom, jak jednotné přihlašování k místním prostředkům funguje na zařízeních připojených k Azure AD](azuread-join-sso.md).

## <a name="device-security"></a>Zabezpečení zařízení

- **Zařízení registrovaná v Azure AD** využívají účet spravovaný koncovým uživatelem, tento účet je účet Microsoft nebo jiné místně spravované přihlašovací údaje, které jsou zabezpečené pomocí jedné nebo několika z následujících možností.
   - Heslo
   - PIN
   - Vzor
   - Windows Hello
- **Připojené služby Azure AD nebo zařízení připojená k hybridní službě Azure AD** využívají účet organizace ve službě Azure AD, který je zabezpečený pomocí jedné nebo několika z následujících možností.
   - Heslo
   - Windows Hello pro firmy

## <a name="provisioning"></a>Zřizování

Získání zařízení v rámci služby Azure AD se dá provádět samoobslužným způsobem nebo řízeným procesem zřizování správců.

## <a name="summary"></a>Souhrn

Správa identit zařízení ve službě Azure AD vám umožní:

- Zjednodušení procesu zavedení a správy zařízení v Azure AD
- Poskytnout uživatelům snadný přístup ke cloudovým prostředkům vaší organizace

## <a name="license-requirements"></a>Licenční požadavky

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Další kroky

- Další informace o [zařízeních registrovaných v Azure AD](concept-azure-ad-register.md)
- Další informace o [zařízeních připojených k Azure AD](concept-azure-ad-join.md)
- Další informace o [zařízeních připojených k hybridní službě Azure AD](concept-azure-ad-join-hybrid.md)
- Přehled správy identit zařízení v Azure Portal najdete v tématu [Správa identit zařízení pomocí Azure Portal](device-management-azure-portal.md).
- Další informace o podmíněném přístupu na základě zařízení najdete v tématu [Konfigurace zásad podmíněného přístupu na základě zařízení Azure Active Directory](../conditional-access/require-managed-devices.md).
