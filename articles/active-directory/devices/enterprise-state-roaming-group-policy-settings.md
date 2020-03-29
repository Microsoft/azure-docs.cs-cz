---
title: Nastavení zásad skupiny a MDM pro ESR – Azure Active Directory
description: Nastavení správy pro podnikový státní roaming
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdffbc3a140bd13dcd6d352db8c192803d39b03e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672370"
---
# <a name="group-policy-and-mdm-settings"></a>Nastavení zásad skupiny a MDM

Tato nastavení zásad skupiny a správy mobilních zařízení (MDM) používejte pouze na zařízeních vlastněných společností, protože tyto zásady se vztahují na celé zařízení uživatele. Použití zásady MDM k zakázání synchronizace nastavení pro osobní zařízení vlastněné uživatelem negativně ovlivní použití tohoto zařízení. Kromě toho budou zásady ovlivněny i další uživatelské účty v zařízení.

Podniky, které chtějí spravovat roaming pro osobní (nespravovaná) zařízení, můžou pomocí portálu Azure povolit nebo zakázat roaming, místo aby používaly zásady skupiny nebo MDM.
Následující tabulky popisují nastavení zásad, které je k dispozici.

> [!NOTE]
> Tento článek se týká prohlížeče microsoft edge legacy založeného na HTML spuštěném v systému Windows 10 v červenci 2015. Tento článek se nevztahuje na nový prohlížeč založený na microsoft edge chromu vydaném 15. Další informace o chování synchronizace pro nový microsoft edge naleznete v článku [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

## <a name="mdm-settings"></a>Nastavení MDM

Nastavení zásad MDM platí pro Windows 10 i Windows 10 Mobile.  Podpora Windows 10 Mobile existuje jenom pro roaming založený na účtu Microsoft prostřednictvím účtu OneDrive uživatele. Podrobnosti o tom, která zařízení jsou podporovaná pro synchronizaci na základě Azure AD, najdete v článku [Zařízení a koncové body.](enterprise-state-roaming-windows-settings-reference.md)

| Name (Název) | Popis |
| --- | --- |
| Povolit připojení účtu Microsoft |Umožňuje uživatelům ověřovat pomocí účtu Microsoft na zařízení |
| Povolit synchronizaci nastavení |Umožňuje uživatelům potulovat se po nastavení systému Windows a data aplikací; Zakázáním této zásady zakážete synchronizaci i zálohování na mobilních zařízeních. |

## <a name="group-policy-settings"></a>Nastavení zásad skupiny

Nastavení zásad skupiny platí pro zařízení s Windows 10, která jsou připojena k doméně služby Active Directory. Tabulka také obsahuje starší nastavení, která se zdají spravovat nastavení synchronizace, ale které nefungují pro enterprise state roaming pro Windows 10, které jsou v popisu uvedeny v popisu s položkou Nepoužívejte.

Tato nastavení jsou umístěna na adrese:`Computer Configuration > Administrative Templates > Windows Components > Sync your settings` 

| Name (Název) | Popis |
| --- | --- |
| Účty: Blokovat účty Microsoft |Toto nastavení zásad zabraňuje uživatelům přidávat do tohoto počítače nové účty Microsoft. |
| Nesynchronizovat |Zabrání uživatelům v přístupu k nastavení windows a dataplikací. |
| Nesynchronizovat přizpůsobení |Zakáže synchronizaci skupiny Motivy. |
| Nesynchronizovat nastavení prohlížeče |Zakáže synchronizaci skupiny Internet Explorer. |
| Nesynchronizovat hesla |Zakáže synchronizaci skupiny Hesla. |
| Nesynchronizovat jiná nastavení systému Windows |Zakáže synchronizaci jiné skupiny nastavení systému Windows. |
| Nesynchronizovat individuální nastavení plochy |Nepoužívejte; nemá žádný účinek |
| Nesynchronizovat u připojení účtovaných podle objemu dat |Zakáže roaming u připojení účtovaných podle objemu dat, například mobilní 3G |
| Nesynchronizovat aplikace |Nepoužívejte; nemá žádný účinek |
| Nesynchronizovat nastavení aplikace |Zakáže roaming dat aplikace. |
| Nesynchronizovat nastavení spuštění |Nepoužívejte; nemá žádný účinek |

## <a name="next-steps"></a>Další kroky

Přehled najdete v tématu [Přehled podnikového státního roamingu](enterprise-state-roaming-overview.md).
