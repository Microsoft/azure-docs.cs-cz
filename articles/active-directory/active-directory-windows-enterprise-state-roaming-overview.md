---
title: Enterprise State Roaming – přehled | Dokumentace Microsoftu
description: Poskytuje informace o Enterprise State Roaming nastavení v zařízení s Windows. Enterprise State Roaming uživatelům poskytuje jednotné prostředí v jejich zařízeních Windows a snižuje čas potřebný ke konfiguraci nových zařízení.
services: active-directory
keywords: Co je Enterprise State Roaming, synchronizaci podnikových cloudu systému windows
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: curtand
ms.component: devices
ms.assetid: 83b3b58f-94c1-4ab0-be05-20e01f5ae3f0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: markvi
ms.openlocfilehash: c1332679af57a4994f4337e241845a87bf16b341
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42054233"
---
# <a name="enterprise-state-roaming-overview"></a>Přehled služby Enterprise State Roaming
S Windows 10 [Azure Active Directory (Azure AD)](fundamentals/active-directory-whatis.md) uživatelé získají možnost bezpečně synchronizaci svých uživatelských nastavení a nastavení data aplikací do cloudu. Enterprise State Roaming uživatelům poskytuje jednotné prostředí v jejich zařízeních Windows a snižuje čas potřebný ke konfiguraci nových zařízení. Enterprise State Roaming funguje podobně jako standardní [synchronizace nastavení příjemce](http://windows.microsoft.com/windows-8/sync-settings-pcs) , která byla poprvé zavedena v systému Windows 8. Enterprise State Roaming dál nabízí:

* **Oddělení podnikové a uživatelských dat** – organizace mají kontrolu nad jejich dat a neexistuje žádná kombinování firemních dat v cloudovém účtu příjemce nebo uživatelských dat v účtu služby enterprise cloud.
* **Zvýšené zabezpečení** – Data budou automaticky šifrována před opuštěním zařízení s Windows 10 uživatele pomocí Azure Rights Management (Azure RMS) a data zůstanou šifrovaná v klidovém stavu uložených v cloudu. Veškerý obsah zůstává zašifrovaný v klidovém stavu uložených v cloudu, s výjimkou obory názvů, jako jsou názvy nastavení a aplikace Windows.  
* **Lepší monitorování a správa** – poskytuje řízení a viditelnost, přes který se má synchronizovat nastavení ve vaší organizaci a v zařízení, která prostřednictvím portálu integrace služby Azure AD. 

Enterprise State Roaming je k dispozici v několika oblastech Azure. Aktualizovaný seznam dostupných oblastí najdete na [služeb Azure podle oblasti](https://azure.microsoft.com/regions/#services) stránce v části Azure Active Directory.

| Článek | Popis |
| --- | --- |
| [Povolení služby Enterprise State Roaming v Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md) |Enterprise State Roaming je k dispozici pro všechny organizace s předplatným Premium služby Azure Active Directory (Azure AD). Další informace o tom, jak získat předplatné Azure AD, najdete v článku [produktu Azure AD](https://azure.microsoft.com/services/active-directory) stránky. |
| [Nastavení a datovému roamingu – nejčastější dotazy](active-directory-windows-enterprise-state-roaming-faqs.md) |Toto téma odpovědi na některé dotazy, které správce IT může mít informace o nastavení a synchronizace dat aplikace. |
| [Skupina zásad a nastavení MDM pro nastavení synchronizace](active-directory-windows-enterprise-state-roaming-group-policy-settings.md) |Windows 10 nabízí zásad skupiny a správy mobilních zařízení (MDM) nastavení zásad k omezení nastavení synchronizace. |
| [Informace o cestovní nastavení Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md) |Následuje úplný seznam všech nastavení, které bude možné s roamingem a/nebo zálohování ve Windows 10. |
| [Řešení potíží](active-directory-windows-enterprise-state-roaming-troubleshooting.md) |Toto téma prochází některé základní kroky pro řešení potíží a obsahuje seznam známých problémů. |

