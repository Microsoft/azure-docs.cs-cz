---
title: Co je identitu zařízení v Azure Active Directory?
description: Zjistěte, jak Správa identit zařízení vám mohou pomoci při správě zařízení, které mají přístup k prostředkům ve vašem prostředí.
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
ms.openlocfilehash: 5d38ca8bdf93ff201b3f5842f4cb0e8409dcd0c3
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481674"
---
# <a name="what-is-a-device-identity"></a>Co je identita zařízení?

S čím zařízení všech tvarů a velikostí a koncept přineste si vlastní zařízení (BYOD) se odborníci v oblasti IT potýkají s dva poněkud dosáhnout cíle:

- Povolit koncovým uživatelům, aby byli produktivní, kdykoli a kdekoli
- Chránit prostředky organizace.

K ochraně těchto prostředků, pracovníci IT muset nejdřív Správa identit zařízení. Pracovníci IT můžete vytvořit na identitě zařízení pomocí nástrojů, jako je Microsoft Intune a ujistěte se, že jsou splněné standardy zabezpečení a dodržování předpisů. Azure Active Directory (Azure AD) umožňuje jednotné přihlašování pro zařízení, aplikací a službám odkudkoli prostřednictvím těchto zařízení.

- Uživatelé získají přístup k prostředkům vaší organizace, které potřebují. 
- Pracovníci IT získat ovládací prvky, které potřebují k zabezpečení vaší organizace.

Správa identit zařízení je základem pro [podmíněného přístupu podle zařízení](../conditional-access/require-managed-devices.md). Pomocí zásad podmíněného přístupu podle zařízení můžete zajistit, že přístup k prostředkům ve vašem prostředí je možné pouze se spravovanými zařízeními.

## <a name="getting-devices-in-azure-ad"></a>Získání zařízení ve službě Azure AD

Pokud chcete získat zařízení ve službě Azure AD, máte několik možností:

- **Registrováno v Azure AD**
   - Zařízení, která jsou registrováno v Azure AD jsou obvykle osobním vlastnictví nebo mobilní zařízení a přihlášení pomocí osobního účtu Microsoft nebo jiný místní účet.
      - Windows 10
      - iOS
      - Android
      - MacOS
- **Připojeno k Azure AD**
   - Zařízení, která jsou připojená k Azure AD jsou vlastní určitá organizace a jsou k přihlášení s účtem služby Azure AD, které patří k dané organizace. Existují jenom v cloudu.
      - Windows 10 
- **k hybridní službě Azure AD**.
   - Zařízení, která jsou připojená k hybridní Azure AD jsou vlastní určitá organizace a jsou k přihlášení s účtem služby Azure AD, které patří k dané organizace. Existují v cloudu a 
      - Windows 7, 8.1 nebo 10
      - Windows Server 2008 nebo novější

![Zařízení zobrazí v okně podpora k zařízením Azure AD](./media/overview/azure-ad-devices-all-devices-overview.png)

## <a name="device-management"></a>Správa zařízení

Zařízení ve službě Azure AD můžete spravovat pomocí nástrojů správy mobilních zařízení (MDM), jako je Microsoft Intune, System Center Configuration Manager, zásad skupiny (hybridní služby Azure AD join), nástroje pro správu mobilních aplikací (MAM) nebo jiných nástrojů třetích stran.

## <a name="resource-access"></a>Přístup k prostředkům

Registrace a připojit se k dát uživatelům bezproblémové přihlašování (SSO) ke cloudovým prostředkům a správci schopnosti uplatňovat zásady podmíněného přístupu k těmto prostředkům. 

Zařízení, která jsou připojená k Azure AD nebo hybridní službě Azure AD připojené výhody jednotného přihlašování pro vaše organizace místní prostředky i cloudových prostředků. Další informace najdete v článku, [jak jednotné přihlašování funguje s místními prostředky v Azure AD zařízení připojená k](azuread-join-sso.md).

## <a name="device-security"></a>Zabezpečení zařízení

- **Azure AD registrované zařízení** účet spravovaný koncový uživatel využívat, tento účet je účet Microsoft nebo jiné místně spravované přihlašovací údaje, které jsou zabezpečené s jedním nebo více z následujících akcí.
   - Heslo
   - PIN
   - Vzor
   - Windows Hello
- **Připojená k Azure AD nebo zařízení připojená k hybridní službě Azure AD** využít účet organizace ve službě Azure AD zabezpečené s jedním nebo více z následujících akcí.
   - Heslo
   - Windows Hello pro firmy

## <a name="provisioning"></a>Zřizování

Získání zařízení v Azure AD můžete udělat v podobě samoobslužných služeb nebo řízený proces zřizování správci.

## <a name="summary"></a>Souhrn

Správa identit zařízení ve službě Azure AD můžete:

- Zjednodušení procesu přináší a správě zařízení ve službě Azure AD
- Poskytnout uživatelům snadný přístup ke cloudovým prostředkům vaší organizace

## <a name="license-requirements"></a>Licenční požadavky

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Další postup

- Další informace o [Azure AD zaregistrované zařízení](concept-azure-ad-register.md)
- Další informace o [zařízení připojená k Azure AD](concept-azure-ad-join.md)
- Další informace o [zařízení připojená k hybridní službě Azure AD](concept-azure-ad-join-hybrid.md)
- Pokud chcete získat přehled o tom, jak Správa identit zařízení na portálu Azure portal, najdete v článku [Správa identit zařízení pomocí webu Azure portal](device-management-azure-portal.md).
- Další informace o podmíněném přístupu na základě zařízení, najdete v článku [zásad podmíněného přístupu podle zařízení konfigurovat Azure Active Directory](../conditional-access/require-managed-devices.md).
