---
title: Nastavení MDM a zásady skupiny | Dokumentace Microsoftu
description: Poskytuje informace o zásad skupiny a mobilní zařízení nastavení management (MDM), které byste neměli používat v zařízení vlastněných společností. Tyto zásady se použijí pro celé zařízení uživatele.
services: active-directory
keywords: Co jsou skupiny zásad a nastavení MDM Enterprise State Roaming, Enterprise State Roaming, cloudu systému windows
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: curtand
ms.subservice: devices
ms.assetid: 6471a9b3-8dd4-4237-89d1-bfbeca9f8252
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33638d9b22fb1717fadcb1717f816e95076a7d66
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56161092"
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


