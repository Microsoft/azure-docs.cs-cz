---
title: Co je identita zařízení ve službě Azure Active Directory?
description: Zjistěte, jak vám správa identit zařízení může pomoci spravovat zařízení, která přistupují k prostředkům ve vašem prostředí.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 03/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba0630474224c34eb96429cd7592028362e03381
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024434"
---
# <a name="what-is-a-device-identity"></a>Co je identita zařízení?

S rozšířením zařízení všech tvarů a velikostí a konceptem Bring Your Own Device (BYOD) se IT profesionálové potýkají se dvěma poněkud protichůdnými cíli:

- Umožněte koncovým uživatelům, aby byli produktivní kdekoli a kdykoli
- Ochrana majetku organizace

K ochraně těchto prostředků musí pracovníci IT nejprve spravovat identity zařízení. Pracovníci IT mohou stavět na identitě zařízení pomocí nástrojů, jako je Microsoft Intune, aby zajistili, že budou splněny standardy zabezpečení a dodržování předpisů. Azure Active Directory (Azure AD) umožňuje jednotné přihlašování k zařízením, aplikacím a službám odkudkoli prostřednictvím těchto zařízení.

- Vaši uživatelé získají přístup k prostředkům vaší organizace, které potřebují. 
- Vaši pracovníci IT získají ovládací prvky, které potřebují k zabezpečení vaší organizace.

Správa identit zařízení je základem [podmíněného přístupu založeného na zařízení](../conditional-access/require-managed-devices.md). Pomocí zásad podmíněného přístupu založených na zařízení můžete zajistit, aby byl přístup k prostředkům ve vašem prostředí možný pouze se spravovanými zařízeními.

## <a name="getting-devices-in-azure-ad"></a>Získání zařízení ve službě Azure AD

Pokud chcete získat zařízení ve službě Azure AD, máte několik možností:

- **Registrováno v Azure AD**
   - Zařízení, která jsou registrovanými službou Azure AD, jsou obvykle vlastněná nebo mobilní zařízení a jsou přihlášená pomocí osobního účtu Microsoft nebo jiného místního účtu.
      - Windows 10
      - iOS
      - Android
      - MacOS
- **Připojené k Azure AD**
   - Zařízení, která jsou připojená k Azure AD, jsou vlastněná organizací a jsou přihlášená pomocí účtu Azure AD, který patří do této organizace. Existují pouze v cloudu.
      - Windows 10 
      - Windows Server 2019 (Jádro serveru není podporováno)
- **k hybridní službě Azure AD**.
   - Zařízení, která jsou hybridní Azure AD připojen jsou vlastněné organizací a jsou přihlášeni pomocí účtu Azure AD patřící do této organizace. Existují v cloudu a místně.
      - Windows 7, 8.1 nebo 10
      - Systém Windows Server 2008 nebo novější

![Zařízení zobrazená v okně Zařízení Azure AD](./media/overview/azure-active-directory-devices-all-devices.png)

> [!NOTE]
> Hybridní stav odkazuje na více než jen stav zařízení. Aby byl hybridní stav platný, je vyžadován také platný uživatel Služby Azure AD.

## <a name="device-management"></a>Správa zařízení

Zařízení ve službě Azure AD lze spravovat pomocí nástrojů pro správu mobilních zařízení (MDM), jako je Microsoft Intune, Microsoft Endpoint Configuration Manager, Zásady skupiny (hybridní připojení Azure AD), Nástroje pro správu mobilních aplikací (MAM) nebo jiné nástroje třetích stran.

## <a name="resource-access"></a>Přístup ke zdrojům

Registrace a připojení zařízení k Azure AD poskytuje uživatelům bezproblémové přihlašování (SSO) ke cloudovým prostředkům. Tento proces také umožňuje správcům používat zásady podmíněného přístupu na prostředky založené na zařízení, ze které jsou přístupné. 

> [!NOTE]
> Zásady podmíněného přístupu založené na zařízení vyžadují buď hybridní zařízení připojená k Azure AD, nebo kompatibilní zařízení azure ad registrovaná nebo registrovaná zařízení Azure AD.

Primární obnovovací token (PRT) obsahuje informace o zařízení a je vyžadován pro sso. Pokud máte zásady podmíněného přístupu založené na zařízení nastavené v aplikaci, bez PRT, přístup je odepřen. Hybridní zásady podmíněného přístupu vyžadují hybridní stav zařízení a platný uživatel, který je přihlášen.

Zařízení, která jsou azure ad připojen nebo hybridní Azure AD připojil výhody z připojovacího zabezpečení na místní prostředky vaší organizace, stejně jako cloudové prostředky. Další informace najdete v článku [Jak funguje přisyťit ém místní prostředky na zařízeních spojených s Azure AD](azuread-join-sso.md).

## <a name="device-security"></a>Zabezpečení zařízení

- **Zařízení registrovaná službou Azure AD** využívají účet spravovaný koncovým uživatelem, tento účet je buď účet Microsoft, nebo jiné místně spravované přihlašovací údaje zabezpečené jedním nebo více z následujících.
   - Heslo
   - PIN
   - Vzor
   - Windows Hello
- **Azure AD připojená nebo hybridní zařízení azure ad připojena** k využití účtu organizace ve službě Azure AD zabezpečené s jedním nebo více z následujících.
   - Heslo
   - Windows Hello pro firmy

## <a name="provisioning"></a>Zřizování

Získání zařízení do Azure AD lze provést samoobslužným způsobem nebo řízený proces zřizování správci.

## <a name="summary"></a>Souhrn

Se správou identit zařízení ve službě Azure AD můžete:

- Zjednodušení procesu přinášení a správy zařízení ve službě Azure AD
- Poskytněte uživatelům snadno použitelný přístup ke cloudovým prostředkům vaší organizace

## <a name="license-requirements"></a>Licenční požadavky

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Další kroky

- Další informace o [zařízeních registrovaných na Azure AD](concept-azure-ad-register.md)
- Další informace o [zařízeních s azure ad připojená](concept-azure-ad-join.md)
- Další informace o [hybridních zařízeních s Azure AD](concept-azure-ad-join-hybrid.md)
- Pokud chcete získat přehled o tom, jak spravovat identity zařízení na webu Azure Portal, přečtěte si část [Správa identit zařízení pomocí portálu Azure](device-management-azure-portal.md).
- Další informace o podmíněném přístupu založeném na zařízení najdete [v tématu Konfigurace zásad podmíněného přístupu založeným na zařízení Služby Azure Active Directory](../conditional-access/require-managed-devices.md).
