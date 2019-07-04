---
title: Co je hybridní služby Azure AD připojené zařízení?
description: Zjistěte, jak Správa identit zařízení vám mohou pomoci při správě zařízení, která mají přístup k prostředkům ve vašem prostředí.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c57180ba10322cb790c05b3f8f48043ca08b545
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462743"
---
# <a name="hybrid-azure-ad-joined-devices"></a>Hybridní zařízení připojená k Azure AD

Už více než deset let využívá řada organizací připojení k doméně místní služby Active Directory k následujícím účelům:

- Umožnit IT oddělením spravovat pracovní zařízení z centrálního umístění.
- Umožnit uživatelům přihlašovat se ke svým zařízením pomocí svých pracovních nebo školních účtů Active Directory.

Organizace využívající místní prostředí se při zřizování zařízení obvykle spoléhají na metody vytváření imagí a ke správě těchto zařízení často využívají **System Center Configuration Manager (SCCM)** nebo **zásady skupiny**.

Pokud se ve vašem prostředí využívá AD a také chcete využít možnosti, které poskytuje Azure Active Directory, můžete implementovat hybridní zařízení připojená k Azure AD. Tato zařízení jsou zařízení, která jsou připojená k vaší místní Active Directory a zaregistrované v Azure Active Directory.

|   | Připojení k hybridní službě Azure AD |
| --- | --- |
| **Definice** | Připojené k místní AD a Azure AD, které vyžadují účet organizace pro přihlášení k zařízení |
| **Primární cílovou skupinu** | Vhodná pro hybridní organizace se stávající místní infrastrukturu AD |
|   | Platí pro všechny uživatele v organizaci |
| **Vlastnictví zařízení** | Organizace |
| **Operační systémy** | Windows 10, 8.1 a 7 |
|   | Windows Server 2008/R2, 2012/R2 2016 a 2019 |
| **Zřizování** | Windows 10, Windows Server 2016/2019 |
|   | Připojení k doméně pomocí IT a automatické spojení přes Azure AD Connect nebo služby AD FS config |
|   | Připojení k doméně Windows Autopilotu a automatické spojení prostřednictvím konfigurace služby Azure AD Connect nebo služby AD FS |
|   | Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 a Windows Server 2008 R2 - vyžadují MSI |
| **Možnosti přihlášení zařízení** | Organizační účty pomocí: |
|   | Heslo |
|   | Windows Hello pro firmy pro Win10 |
| **Správa zařízení** | Zásady skupiny |
|   | Samostatný System Center Configuration Manager nebo spolusprávy v Microsoft Intune |
| **Klíčové funkce** | Jednotné přihlašování do cloudových a místních prostředků |
|   | Podmíněný přístup prostřednictvím připojení k doméně nebo přes Intune, pokud společně spravovat. |
|   | Samoobslužné resetování hesla a Windows Hello PIN kód resetovat na zamykací obrazovce |
|   | Enterprise State Roaming v zařízení |

![Hybridní zařízení připojená k Azure AD](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>Scénáře

Použití služby Azure AD hybridní zařízení připojená k pokud:

- Máte na těchto zařízeních nasazené aplikace Win32, které se spoléhají na strojové ověřování v Active Directory.
- Chcete dál používat zásady skupiny pro správu konfigurace zařízení.
- Chcete pokračovat v používání existujícího řešení pro zpracování obrázků k nasazení a konfigurace zařízení.
- Musí podporovat 7 Windows nižší úrovně a zařízení s Windows 8.1 kromě Windows 10

## <a name="next-steps"></a>Další postup

- [Plánování implementace připojení k hybridní službě Azure AD](hybrid-azuread-join-plan.md)
- [Správa identit zařízení pomocí webu Azure portal](device-management-azure-portal.md)
- [Správa zastaralých zařízení ve službě Azure AD](manage-stale-devices.md)
