---
title: Nastavení MDM a zásady skupiny | Dokumentace Microsoftu
description: Poskytuje informace o zásad skupiny a mobilní zařízení nastavení management (MDM), které byste neměli používat v zařízení vlastněných společností.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3f2b1afa67ec36da4d4da57b296e696fd6c6910
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481948"
---
# <a name="group-policy-and-mdm-settings"></a>Nastavení zásad skupiny a MDM
Použijte tyto zásady skupiny a nastavení správy mobilních zařízení jenom na zařízeních vlastněných společností, protože tyto zásady platí pro celé zařízení uživatele. Zásady MDM zakázání synchronizace nastavení pro osobní použití, zařízení ve vlastnictví uživatele bude mít negativní vliv na použití těchto zařízení. Kromě toho další uživatelské účty na zařízení také ovlivňuje zásady.

Podnikům, které chcete spravovat cestovní pro osobní zařízení (nespravovaného) můžete použít na webu Azure portal k povolení nebo zakázání roamingu, nikoli pomocí zásad skupiny nebo správy mobilních zařízení.
Následující tabulky popisují nastavení zásad, která je k dispozici.

## <a name="mdm-settings"></a>Nastavení MDM
Nastavení zásad MDM platí pro Windows 10 a Windows 10 Mobile.  Podpora Windows 10 Mobile je dostupná jenom pro účet Microsoft na základě roaming prostřednictvím účtu OneDrive.  Najdete [zařízení a koncových bodů](enterprise-state-roaming-windows-settings-reference.md) podrobnosti o jaká zařízení se podporují pro synchronizaci Azure AD na základě.

| Název | Popis |
| --- | --- |
| Povolit připojení k účtu Microsoft |Umožňuje uživatelům provést ověření pomocí účtu Microsoft na zařízení |
| Povolit synchronizaci nastavení |Umožňuje uživatelům zpřístupní Windows nastavení a data aplikací; Zakázání této zásady zakážete synchronizace, jakož i zálohování na mobilních zařízeních |

## <a name="group-policy-settings"></a>Nastavení zásad skupiny
Nastavení zásad skupiny platí pro zařízení s Windows 10, která jsou připojená k doméně služby Active Directory. V tabulce jsou zahrnuty i starší verze nastavení, která by se zobrazí ke správě nastavení synchronizace, ale nefungují pro Enterprise stav roamingu pro Windows 10, které jsou označeny pomocí "Nepoužívejte" v popisu.

Tato nastavení se nacházejí ve: `Computer Configuration > Administrative Templates > Windows Components > Sync your settings` 

| Název | Popis |
| --- | --- |
| Účty: Blokovat účty Microsoft |Nastavení této zásady zabrání uživatelům v přidávání nových účtů Microsoft na tomto počítači |
| Se nesynchronizují |Zabrání uživatelům zpřístupní Windows nastavení a data aplikací |
| Se nesynchronizují přizpůsobení |Zakáže synchronizaci skupiny motivy |
| Nastavení prohlížeče se nesynchronizují |Zakáže synchronizaci skupiny aplikace Internet Explorer |
| Hesla se nesynchronizují |Zakáže synchronizaci hesel skupiny |
| Další nastavení Windows se nesynchronizují |Zakáže synchronizaci ostatní Windows nastavení skupiny |
| Přizpůsobení plochy se nesynchronizují |Nepoužívejte; nemá žádný vliv |
| U měřených připojení se nesynchronizují |Zakáže roamingu v měřené připojení, například mobilní sítě 3 G |
| Aplikace se nesynchronizují |Nepoužívejte; nemá žádný vliv |
| Nastavení aplikace se nesynchronizují |Zakáže roaming dat aplikací |
| Nastavení spuštění se nesynchronizují |Nepoužívejte; nemá žádný vliv |

## <a name="next-steps"></a>Další postup

Přehled najdete v tématu [enterprise State Roaming přehled](enterprise-state-roaming-overview.md).


