---
title: Co je zařízení připojené k hybridní službě Azure AD?
description: Přečtěte si, jak vám Správa identit zařízení může pomáhat při správě zařízení, která přistupují k prostředkům ve vašem prostředí.
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
ms.openlocfilehash: 259a1324c412dad40d32a8b8e026d84e6f5aa066
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85554927"
---
# <a name="hybrid-azure-ad-joined-devices"></a>Hybridní zařízení připojená k Azure AD

Už více než deset let využívá řada organizací připojení k doméně místní služby Active Directory k následujícím účelům:

- Umožnit IT oddělením spravovat pracovní zařízení z centrálního umístění.
- Umožnit uživatelům přihlašovat se ke svým zařízením pomocí svých pracovních nebo školních účtů Active Directory.

Organizace s místními nároky obvykle při zřizování zařízení spoléhají na metody vytváření imagí a často k jejich správě používají **Configuration Manager** nebo **Zásady skupiny (GP)** .

Pokud se ve vašem prostředí využívá AD a také chcete využít možnosti, které poskytuje Azure Active Directory, můžete implementovat hybridní zařízení připojená k Azure AD. Tato zařízení jsou zařízení, která jsou připojená k vaší místní službě Active Directory a zaregistrovaná ve vašem Azure Active Directory.

| Připojení k hybridní službě Azure AD | Description |
| --- | --- |
| **Definice** | Připojeno k místní službě AD a službě Azure AD, která vyžaduje účet organizace pro přihlášení k zařízení |
| **Primární cílová skupina** | Vhodné pro hybridní organizace se stávající místní infrastrukturou AD |
|   | Platí pro všechny uživatele v organizaci. |
| **Vlastnictví zařízení** | Organizace |
| **Operační systémy** | Windows 10, 8,1 a 7 |
|   | Windows Server 2008/R2, 2012/R2, 2016 a 2019 |
| **Zřizování** | Windows 10, Windows Server 2016/2019 |
|   | Připojení k doméně a automatické spojení prostřednictvím Azure AD Connect nebo konfigurace ADFS |
|   | Připojení k doméně pomocí autopilotu Windows a automatické spojení prostřednictvím Azure AD Connect nebo konfigurace ADFS |
|   | Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 a Windows Server 2008 R2 – vyžadovat MSI |
| **Možnosti přihlášení zařízení** | Účty organizace pomocí: |
|   | Heslo |
|   | Windows Hello pro firmy pro Win10 |
| **Správa zařízení** | Zásady skupiny |
|   | Configuration Manager samostatnou nebo spolusprávou Microsoft Intune |
| **Klíčové funkce** | Jednotné přihlašování pro cloudové i místní prostředky |
|   | Podmíněný přístup prostřednictvím připojení k doméně nebo prostřednictvím Intune, pokud se spoluspravuje |
|   | Samoobslužné resetování hesla a Resetování PIN kódu Windows Hello na zamykací obrazovce |
|   | Enterprise State Roaming napříč zařízeními |

![Hybridní zařízení připojená k Azure AD](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>Scénáře

Použijte hybridní zařízení připojená k Azure AD, pokud:

- Máte na těchto zařízeních nasazené aplikace Win32, které se spoléhají na strojové ověřování v Active Directory.
- Chcete pokračovat v používání Zásady skupiny ke správě konfigurace zařízení.
- Chcete dál používat stávající řešení pro vytváření imagí k nasazení a konfiguraci zařízení.
- Kromě Windows 10 musíte podporovat i zařízení s Windows 7 a 8,1 nižší úrovně.

## <a name="next-steps"></a>Další kroky

- [Plánování implementace připojení k hybridní službě Azure AD](hybrid-azuread-join-plan.md)
- [Správa identit zařízení přes Azure Portal](device-management-azure-portal.md)
- [Správa zastaralých zařízení v Azure AD](manage-stale-devices.md)
