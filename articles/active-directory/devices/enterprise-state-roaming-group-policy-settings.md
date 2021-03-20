---
title: Nastavení Zásady skupiny a MDM pro ESR-Azure Active Directory
description: Nastavení správy pro Enterprise State Roaming
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab24b3113f9dc69b8f3907037e228ba212a03106
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85252929"
---
# <a name="group-policy-and-mdm-settings"></a>Nastavení Zásady skupiny a MDM

Tato nastavení zásad skupiny a správy mobilních zařízení (MDM) použijte jenom na zařízeních vlastněných společností, protože tyto zásady se použijí na celé zařízení uživatele. Když použijete zásadu MDM a zakážete synchronizaci nastavení pro osobní zařízení vlastněná uživatelem, bude to mít negativní dopad na použití tohoto zařízení. Kromě toho zásady ovlivní i další uživatelské účty v zařízení.

Podniky, které chtějí spravovat roaming osobních (nespravovaných) zařízení, můžou použít Azure Portal k povolení nebo zakázání roamingu místo použití Zásady skupiny nebo MDM.
V následujících tabulkách jsou popsána nastavení zásad, která jsou k dispozici.

> [!NOTE]
> Tento článek se týká starší verze prohlížeče založeného na HTML v Microsoft Edge, který se spouští s Windows 10 v červenci 2015. Článek neplatí pro nový prohlížeč založený na Microsoft Edge chrom vydaný 15. ledna 2020. Další informace o chování synchronizace pro nové Microsoft Edge najdete v článku o [synchronizaci Microsoft Edge](/deployedge/microsoft-edge-enterprise-sync).

## <a name="mdm-settings"></a>Nastavení MDM

Nastavení zásad MDM se vztahuje na systémy Windows 10 a Windows 10 Mobile.  Podpora Windows 10 Mobile existuje jenom pro účet Microsoft roamingu prostřednictvím účtu OneDrive uživatele. Podrobnosti o tom, jaká zařízení jsou podporovaná pro synchronizaci na základě služby Azure AD, najdete v tématu [zařízení a koncové body](enterprise-state-roaming-windows-settings-reference.md) .

| Název | Description |
| --- | --- |
| Umožňuje připojení k účtu Microsoft. |Umožňuje uživatelům ověřování pomocí účet Microsoft na zařízení. |
| Povolí synchronizaci mých nastavení. |Umožňuje uživatelům roaming nastavení systému Windows a dat aplikací. Zakázáním těchto zásad se zakáže synchronizace i zálohování na mobilních zařízeních. |

## <a name="group-policy-settings"></a>Nastavení zásad skupiny

Nastavení zásad skupiny platí pro zařízení s Windows 10, která jsou připojená k doméně služby Active Directory. Tabulka obsahuje taky starší nastavení, která se zobrazí při správě nastavení synchronizace, ale která nefungují pro Enterprise State Roaming pro Windows 10, která se v popisu poznamenala jako nepoužívat.

Tato nastavení se nacházejí v: `Computer Configuration > Administrative Templates > Windows Components > Sync your settings` 

| Název | Description |
| --- | --- |
| Účty: blokovat účty Microsoft |Nastavení této zásady zabrání uživatelům v přidávání nových účtů Microsoft na tento počítač. |
| Nesynchronizovat |Brání uživatelům v roamingu nastavení Windows a dat aplikací. |
| Nesynchronizovat přizpůsobení |Zakáže synchronizaci skupiny motivů. |
| Nesynchronizovat nastavení prohlížeče |Zakáže synchronizaci skupiny Internet Exploreru. |
| Nesynchronizovat hesla |Zakáže synchronizaci skupiny hesel. |
| Nesynchronizovat ostatní nastavení Windows |Zakáže synchronizaci ostatních skupin nastavení systému Windows. |
| Nesynchronizovat individuální nastavení plochy |Nepoužívejte; nemá žádný vliv |
| Nesynchronizovat u monitorovaných připojení |Zakáže roaming u připojení účtovaná podle objemu dat, jako je například mobilní aplikace 3G. |
| Nesynchronizovat aplikace |Nepoužívejte; nemá žádný vliv |
| Nesynchronizovat nastavení aplikace |Zakáže roaming dat aplikace. |
| Nesynchronizovat počáteční nastavení |Nepoužívejte; nemá žádný vliv |

## <a name="next-steps"></a>Další kroky

Přehled najdete v tématu [Přehled služby Enterprise State Roaming](enterprise-state-roaming-overview.md).
