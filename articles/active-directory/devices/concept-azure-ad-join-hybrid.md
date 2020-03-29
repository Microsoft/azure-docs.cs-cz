---
title: Co je hybridní zařízení s připojením k Azure AD?
description: Zjistěte, jak vám správa identit zařízení může pomoci spravovat zařízení, která přistupují k prostředkům ve vašem prostředí.
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
ms.openlocfilehash: 15cdaba7d63d72aab25757e7ba6f5eadc48e026a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76512245"
---
# <a name="hybrid-azure-ad-joined-devices"></a>Hybridní zařízení připojená k Azure AD

Už více než deset let využívá řada organizací připojení k doméně místní služby Active Directory k následujícím účelům:

- Umožnit IT oddělením spravovat pracovní zařízení z centrálního umístění.
- Umožnit uživatelům přihlašovat se ke svým zařízením pomocí svých pracovních nebo školních účtů Active Directory.

Organizace s místní stopou obvykle spoléhají na metody zpracování bitových kopií při zřizování zařízení a často k jejich správě používají **nástroj Configuration Manager** nebo **zásady skupiny (GP).**

Pokud se ve vašem prostředí využívá AD a také chcete využít možnosti, které poskytuje Azure Active Directory, můžete implementovat hybridní zařízení připojená k Azure AD. Tato zařízení jsou zařízení, která jsou připojená k místní službě Active Directory a registrovaná ve službě Azure Active Directory.

|   | Hybridní připojení k azure reklamě |
| --- | --- |
| **Definice** | Připojeno k místnímu službě AD a Azure AD vyžadujícímu, aby se k zařízení přihlašoval účet organizace. |
| **Primární publikum** | Vhodné pro hybridní organizace s existující místní infrastrukturou AD |
|   | Platí pro všechny uživatele v organizaci |
| **Vlastnictví zařízení** | Organizace |
| **Operační systémy** | Windows 10, 8.1 a 7 |
|   | Windows Server 2008/R2, 2012/R2, 2016 a 2019 |
| **Zřizování** | Windows 10, Windows Server 2016/2019 |
|   | Připojení k doméně pomocí IT a automatické připojení prostřednictvím konfigurace Azure AD Connect nebo ADFS |
|   | Připojení k doméně pomocí automatického pilota Windows a automatické připojení prostřednictvím konfigurace Azure AD Connect nebo ADFS |
|   | Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 a Windows Server 2008 R2 – vyžadují msi |
| **Možnosti přihlášení k zařízení** | Organizační účty pomocí: |
|   | Heslo |
|   | Windows Hello pro firmy pro Win10 |
| **Správa zařízení** | Zásady skupiny |
|   | Samostatný nebo spoluspráva nástroje Configuration Manager se službou Microsoft Intune |
| **Klíčové funkce** | Přiřazovat služby a zabezpečení cloudovým i místním prostředkům |
|   | Podmíněný přístup prostřednictvím připojení k doméně nebo prostřednictvím Intune, pokud je spravovaný |
|   | Samoobslužné resetování hesla a reset ování PIN kódu kódu Windows Hello na zamykací obrazovce |
|   | Podnikový státní roaming napříč zařízeními |

![Hybridní zařízení připojená k Azure AD](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>Scénáře

Hybridní připojená zařízení Azure AD použijte, pokud:

- Máte na těchto zařízeních nasazené aplikace Win32, které se spoléhají na strojové ověřování v Active Directory.
- Chcete pokračovat v používání zásad skupiny ke správě konfigurace zařízení.
- Chcete pokračovat v používání existujících řešení pro zpracování obrázků k nasazení a konfiguraci zařízení.
- Kromě Windows 10 musíte podporovat zařízení se systémem Windows 7 a 8.1 nižší úrovně.

## <a name="next-steps"></a>Další kroky

- [Plánování implementace připojení k hybridní službě Azure AD](hybrid-azuread-join-plan.md)
- [Správa identit zařízení pomocí portálu Azure](device-management-azure-portal.md)
- [Správa zastaralých zařízení ve službě Azure AD](manage-stale-devices.md)
