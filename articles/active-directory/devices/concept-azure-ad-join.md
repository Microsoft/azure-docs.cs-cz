---
title: Co je Azure AD připojené zařízení?
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
ms.openlocfilehash: 4af3aea7218ea8792bb66188e8df7baf9f460b0b
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462808"
---
# <a name="azure-ad-joined-devices"></a>Zařízení připojená k Azure AD

Připojení k Azure AD je určený pro organizace, které mají být cloud na prvním nebo jenom pro cloud. Všechny organizace může nasadit zařízení připojených k Azure AD bez ohledu na velikost nebo oboru. Připojení k Azure AD funguje i v hybridním prostředí, povolení přístupu ke cloudovým i místním aplikacím a prostředkům.

|   | Připojení k Azure AD |
| --- | --- |
| **Definice** | Připojená pouze k Azure AD vyžaduje účet organizace pro přihlášení k zařízení |
| **Primární cílovou skupinu** | Vhodné pro obě čistě cloudové a hybridní organizace. |
|   | Platí pro všechny uživatele v organizaci |
| **Vlastnictví zařízení** | Organizace |
| **Operační systémy** | Všechna zařízení s Windows 10 |
| **Zřizování** | Samoobslužné funkce: Windows OOBE nebo nastavení |
|   | Hromadná registrace |
|   | Windows Autopilot |
| **Možnosti přihlášení zařízení** | Organizační účty pomocí: |
|   | Heslo |
|   | Windows Hello pro firmy |
|   | FIDO2.0 zabezpečení klíčů (preview) |
| **Správa zařízení** | Správa mobilních zařízení (Příklad: Microsoft Intune) |
|   | Společná správa s Microsoft Intune a System Center Configuration Manager |
| **Klíčové funkce** | Jednotné přihlašování do cloudových a místních prostředků |
|   | Podmíněný přístup prostřednictvím registrace MDM a vyhodnocování dodržování předpisů MDM |
|   | Samoobslužné resetování hesla a Windows Hello PIN kód resetovat na zamykací obrazovce |
|   | Enterprise State Roaming v zařízení |

Podpora k zařízením Azure AD, které jsou připojené k přihlášení do organizace pomocí účtu Azure AD. Přístup k prostředkům v organizaci může být dále omezená založené na tomto účtu služby Azure AD a [zásady podmíněného přístupu](../conditional-access/overview.md) použitý pro identitu zařízení.

Správci se dají zabezpečit a další zařízení pomocí správy mobilních zařízení (MDM) nástroje, jako je Microsoft Intune nebo ve scénářích spolusprávy pomocí System Center Configuration Manager připojená k řízení služby Azure AD. Tyto nástroje umožňují vynucovat organizace vyžaduje konfigurace, jako je vyžadování šifrování úložiště, složitost hesla, instalace softwaru a aktualizací softwaru. Správce může zpřístupnit aplikacím organizace zařízení připojených k Azure AD pomocí [System Center Configuration Manager a Microsoft Store pro firmy](https://docs.microsoft.com/sccm/apps/deploy-use/manage-apps-from-the-windows-store-for-business).

Připojení k Azure AD, můžete to provést pomocí samoobslužné služby možnosti jako z pole prostředí (OOBE), hromadnou registraci, nebo [Windows Autopilot](https://docs.microsoft.com/intune/enrollment-autopilot).

Podpora k zařízením Azure AD, které jsou připojené k můžete stále Udržovat přístup jednotné přihlašování k místním prostředkům, když jsou v síti organizace. Zařízení, která jsou připojená k Azure AD můžete stále ověřovat na místní servery, jako je soubor, tisk a další aplikace.

## <a name="scenarios"></a>Scénáře

Přestože je služba Azure AD Join primárně určená pro organizace, které nemají místní infrastrukturu Windows Server Active Directory, můžete ji využít i v následujících scénářích:

- Chcete přejít na cloudovou infrastrukturu s využitím Azure AD a MDM, jako je například Intune.
- Nemůžete použít připojení k místní doméně například v případě, že potřebujete získat kontrolu nad mobilními zařízeními, jako jsou tablety a telefony.
- Vaši uživatelé primárně potřebují přístup k Office 365 nebo dalším aplikacím SaaS integrovaným s Azure AD.
- Chcete spravovat skupinu uživatelů v Azure AD, a ne v Active Directory. V tomto scénáři můžete použít například k sezónním zaměstnancům, smluvní pracovníci ani interní studentů.
- Chcete poskytnout možnosti připojení pracovníkům ve vzdálených pobočkách s omezenou místní infrastrukturou.

Připojení zařízení k Azure AD můžete nakonfigurovat pro zařízení s Windows 10.

Cílem zařízení připojených k Azure AD je zjednodušit:

- Nasazení Windows na pracovní zařízení
- Přístup k aplikacím a prostředkům organizace z jakéhokoli zařízení s Windows
- Cloudovou správu pracovních zařízení
- Uživatelům umožní přihlásit se svými zařízeními s jejich Azure AD nebo synchronizace služby Active Directory pracovní nebo školní účty.

![Zařízení připojená k Azure AD](./media/concept-azure-ad-join/azure-ad-joined-device.png)

K nasazení služby Azure AD Join je možné použít jakoukoli z následujících metod:

- [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot)
- [Hromadné nasazení](https://docs.microsoft.com/intune/windows-bulk-enroll)
- [Samoobslužné prostředí](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>Další postup

- [Naplánovat vaši implementaci služby Azure AD join](azureadjoin-plan.md)
- [Zařízení připojená k tom, jak spravovat místní skupiny administrators na Azure AD](assign-local-admin.md)
- [Správa identit zařízení pomocí webu Azure portal](device-management-azure-portal.md)
- [Správa zastaralých zařízení ve službě Azure AD](manage-stale-devices.md)
