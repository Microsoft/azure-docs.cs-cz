---
title: Nastavení MDM a zásad skupiny | Microsoft Docs
description: Poskytuje informace o zásadách skupiny a mobilních zařízení nastavení správy (MDM), které se mají použít na zařízeních vlastněných společností. Tyto zásady platí pro uživatele zařízení.
services: active-directory
keywords: Co jsou zásady skupiny a nastavení MDM Enterprise State Roaming, Enterprise State Roaming, cloudu systému windows
documentationcenter: ''
author: tanning
manager: mtillman
editor: curtand
ms.assetid: 6471a9b3-8dd4-4237-89d1-bfbeca9f8252
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: markvi
ms.openlocfilehash: 588084481ffc5cbbeed34e9527271179fa359ed5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2017
ms.locfileid: "26614904"
---
# <a name="group-policy-and-mdm-settings"></a>Nastavení zásad skupiny a správu mobilních zařízení
Používejte tyto zásady skupiny a nastavení správy mobilních zařízení jenom na zařízení ve vlastnictví firmy, protože tyto zásady se použijí pro uživatele zařízení. Zásady pro správu mobilních zařízení pro zakázat synchronizaci nastavení pro osobní použití, zařízení ve vlastnictví uživatele bude mít negativní vliv na používání těchto zařízení. Kromě toho by jiné uživatelské účty v zařízení nebude mít vliv také zásady.

Podnikům, které chcete spravovat cestovní pro osobní zařízení (nespravovaný) můžete použít portál Azure k povolení nebo zakázání roaming, nikoli pomocí zásad skupiny nebo správy mobilních zařízení.
Následující tabulka popisuje nastavení zásad, která je k dispozici.

## <a name="mdm-settings"></a>Nastavení MDM
Nastavení zásad MDM platí pro Windows 10 a Windows 10 Mobile.  Windows 10 Mobile podpora je dostupná pouze pro účet Microsoft na základě roaming prostřednictvím účtu OneDrive.  Naleznete v sekci "Koncové body a zařízení" podrobnosti o jaká zařízení jsou podporovány pro synchronizaci Azure AD na základě.

| Name (Název) | Popis |
| --- | --- |
| Povolit účet Microsoft připojení |Umožňuje uživatelům ověření pomocí účtu Microsoft na zařízení |
| Povolit synchronizaci nastavení |Umožňuje uživatelům používat roaming nastavení systému Windows a data aplikace; Zakázání této zásady zakážete synchronizaci, stejně jako zálohy na mobilních zařízeních |

## <a name="group-policy-settings"></a>Nastavení zásad skupiny
Nastavení zásad skupiny platí pro zařízení s Windows 10, které jsou připojené k doméně služby Active Directory. V tabulce také zahrnuje starší verze nastavení který objeví ke správě nastavení synchronizace, ale které nefungují pro podnikové stavu roamingu pro Windows 10, které jsou uvedené s, nepoužívejte, v popisu.

| Name (Název) | Popis |
| --- | --- |
| Účty: Blokovat účty Microsoft |Nastavení této zásady zabrání uživatelům v přidávání nových účtů Microsoft na tomto počítači |
| Není synchronizována |Zabrání uživatelům se bude používat roaming nastavení systému Windows a dat aplikací |
| Není synchronizována přizpůsobení |Zakáže synchronizuje skupiny motivů |
| Není synchronizována nastavení prohlížeče |Zakáže synchronizace skupiny pro Internet Explorer |
| Ne synchronizaci hesel |Zakáže synchronizaci hesel skupiny |
| Není synchronizována další nastavení Windows |Zakáže synchronizuje ostatní okna nastavení skupiny |
| Přizpůsobení plochy není synchronizována |Nepoužívejte; nemá žádný vliv |
| Není synchronizována u měřených připojení k |Zakáže roaming v měřená připojení, jako je například mobilní 3 G |
| Není synchronizována aplikace |Nepoužívejte; nemá žádný vliv |
| Není synchronizována nastavení aplikace |Zakáže roaming dat aplikací |
| Není synchronizována nastavení spuštění |Nepoužívejte; nemá žádný vliv |

## <a name="related-topics"></a>Související témata
* [Přehled Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
* [Povolit stav enterprise roaming v Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
* [Nastavení a datový roaming – nejčastější dotazy](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Odkaz nastavení roamingu Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [Řešení potíží](active-directory-windows-enterprise-state-roaming-troubleshooting.md)

