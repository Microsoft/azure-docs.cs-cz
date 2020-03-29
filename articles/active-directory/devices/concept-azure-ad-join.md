---
title: Co je zařízení s připojením k Azure AD?
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
ms.openlocfilehash: 40f89fbe19b93601f9e0525f0387e402bd175fe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672669"
---
# <a name="azure-ad-joined-devices"></a>Zařízení připojená k Azure AD

Připojení Azure AD je určené pro organizace, které chtějí být cloudové nebo cloudové. Každá organizace může nasadit zařízení připojená k Azure AD bez ohledu na velikost nebo odvětví. Azure AD připojení funguje i v hybridním prostředí, umožňující přístup ke cloudu i místní aplikace a prostředky.

|   | Připojení k Azure AD |
| --- | --- |
| **Definice** | Připojeno jenom k Azure AD, které vyžaduje, aby se k zařízení přihlašoval účet organizace. |
| **Primární publikum** | Vhodné pro cloudové i hybridní organizace. |
|   | Platí pro všechny uživatele v organizaci |
| **Vlastnictví zařízení** | Organizace |
| **Operační systémy** | Všechna zařízení s Windows 10 |
| **Zřizování** | Samoobslužná služba: Windows OOBE nebo Nastavení |
|   | Hromadný zápis |
|   | Windows Autopilot |
| **Možnosti přihlášení k zařízení** | Organizační účty pomocí: |
|   | Heslo |
|   | Windows Hello pro firmy |
|   | Bezpečnostní klíče FIDO2.0 (náhled) |
| **Správa zařízení** | Správa mobilních zařízení (příklad: Microsoft Intune) |
|   | Spoluspráva s Microsoft Intune a Microsoft Endpoint Configuration Manager |
| **Klíčové funkce** | Přiřazovat služby a zabezpečení cloudovým i místním prostředkům |
|   | Podmíněný přístup prostřednictvím registrace MDM a vyhodnocení dodržování předpisů MDM |
|   | Samoobslužné resetování hesla a reset ování PIN kódu kódu Windows Hello na zamykací obrazovce |
|   | Podnikový státní roaming napříč zařízeními |

Zařízení připojená k Azure AD jsou přihlášení k používání účtu Azure AD organizace. Přístup k prostředkům v organizaci může být dále omezen na základě toho, že účet Azure AD a [zásady podmíněného přístupu](../conditional-access/overview.md) použité pro identitu zařízení.

Správci můžou zabezpečit a dále řídit zařízení připojená k Azure AD pomocí nástrojů pro správu mobilních zařízení (MDM), jako je Microsoft Intune, nebo ve scénářích společné správy pomocí nástroje Microsoft Endpoint Configuration Manager. Tyto nástroje poskytují prostředky k vynucení konfigurací požadovaných organizací, jako je vyžadování šifrování úložiště, složitost hesla, instalace softwaru a aktualizace softwaru. Správci můžou zpřístupnit organizační aplikace pro zařízení připojená k Azure AD pomocí Nástroje configuration manageru pro [správu aplikací z Microsoft Storu pro firmy a vzdělávání](/configmgr/apps/deploy-use/manage-apps-from-the-windows-store-for-business).

Připojení azure ad lze provést pomocí samoobslužné možnosti, jako je možnost mimo pole (OOBE), hromadná registrace nebo [Windows Autopilot](/intune/enrollment-autopilot).

Zařízení připojená k Azure AD můžete stále udržovat přístup k přístupu k místním prostředkům jednotného přihlašování, když jsou v síti organizace. Zařízení, která jsou připojená k Azure AD, se pořád můžou ověřovat na místních serverech, jako jsou soubory, tisk y a další aplikace.

## <a name="scenarios"></a>Scénáře

Přestože je služba Azure AD Join primárně určená pro organizace, které nemají místní infrastrukturu Windows Server Active Directory, můžete ji využít i v následujících scénářích:

- Chcete přejít na cloudovou infrastrukturu s využitím Azure AD a MDM, jako je například Intune.
- Nemůžete použít připojení k místní doméně například v případě, že potřebujete získat kontrolu nad mobilními zařízeními, jako jsou tablety a telefony.
- Vaši uživatelé primárně potřebují přístup k Office 365 nebo dalším aplikacím SaaS integrovaným s Azure AD.
- Chcete spravovat skupinu uživatelů v Azure AD, a ne v Active Directory. Tento scénář se může vztahovat například na sezónní pracovníky, dodavatele nebo studenty.
- Chcete poskytnout možnosti připojení pracovníkům ve vzdálených pobočkách s omezenou místní infrastrukturou.

Připojení zařízení k Azure AD můžete nakonfigurovat pro zařízení s Windows 10.

Cílem zařízení připojených k Azure AD je zjednodušit:

- Nasazení Windows na pracovní zařízení
- Přístup k aplikacím a prostředkům organizace z jakéhokoli zařízení s Windows
- Cloudovou správu pracovních zařízení
- Uživatelé se přihlásit ke svým zařízením pomocí jejich Azure AD nebo synchronizované active directory pracovní nebo školní účty.

![Zařízení připojená k Azure AD](./media/concept-azure-ad-join/azure-ad-joined-device.png)

K nasazení služby Azure AD Join je možné použít jakoukoli z následujících metod:

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot)
- [Hromadné nasazení](/intune/windows-bulk-enroll)
- [Samoobslužné prostředí](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>Další kroky

- [Plánování implementace připojení ke službě Azure AD](azureadjoin-plan.md)
- [Jak spravovat skupinu místních správců na zařízeních spojených s Azure AD](assign-local-admin.md)
- [Správa identit zařízení pomocí portálu Azure](device-management-azure-portal.md)
- [Správa zastaralých zařízení ve službě Azure AD](manage-stale-devices.md)
