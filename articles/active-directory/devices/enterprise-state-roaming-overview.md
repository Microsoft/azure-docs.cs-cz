---
title: Co je stav enterprise roaming v Azure Active Directory? | Dokumenty Microsoft
description: Enterprise State Roaming uživatelům poskytuje jednotné prostředí v jejich zařízeních Windows a snižuje čas potřebný ke konfiguraci nových zařízení.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5b60970592180a2353860369e637d4b9a9bb8f9
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481909"
---
# <a name="what-is-enterprise-state-roaming"></a>Co je Enterprise State Roaming?

S Windows 10 [Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) uživatelé získají možnost bezpečně synchronizaci svých uživatelských nastavení a nastavení data aplikací do cloudu. Enterprise State Roaming uživatelům poskytuje jednotné prostředí v jejich zařízeních Windows a snižuje čas potřebný ke konfiguraci nových zařízení. Enterprise State Roaming funguje podobně jako standardní [synchronizace nastavení příjemce](https://go.microsoft.com/fwlink/?linkid=2015135) , která byla poprvé zavedena v systému Windows 8. Enterprise State Roaming dál nabízí:

* **Oddělení podnikové a uživatelských dat** – organizace mají kontrolu nad jejich dat a neexistuje žádná kombinování firemních dat v cloudovém účtu příjemce nebo uživatelských dat v účtu služby enterprise cloud.
* **Zvýšené zabezpečení** – Data budou automaticky šifrována před opuštěním zařízení s Windows 10 uživatele pomocí Azure Rights Management (Azure RMS) a data zůstanou šifrovaná v klidovém stavu uložených v cloudu. Veškerý obsah zůstává zašifrovaný v klidovém stavu uložených v cloudu, s výjimkou obory názvů, jako jsou názvy nastavení a aplikace Windows.  
* **Lepší monitorování a správa** – poskytuje řízení a viditelnost, přes který se má synchronizovat nastavení ve vaší organizaci a v zařízení, která prostřednictvím portálu integrace služby Azure AD. 

Enterprise State Roaming je k dispozici v několika oblastech Azure. Aktualizovaný seznam dostupných oblastí najdete na [služeb Azure podle oblasti](https://azure.microsoft.com/regions/#services) stránce v části Azure Active Directory.

| Článek | Popis |
| --- | --- |
| [Povolení služby Enterprise State Roaming v Azure Active Directory](enterprise-state-roaming-enable.md) |Enterprise State Roaming je k dispozici pro všechny organizace s předplatným Premium služby Azure Active Directory (Azure AD). Další informace o tom, jak získat předplatné Azure AD, najdete v článku [produktu Azure AD](https://azure.microsoft.com/services/active-directory) stránky. |
| [Nastavení a datovému roamingu – nejčastější dotazy](enterprise-state-roaming-faqs.md) |Toto téma odpovědi na některé dotazy, které správce IT může mít informace o nastavení a synchronizace dat aplikace. |
| [Skupina zásad a nastavení MDM pro nastavení synchronizace](enterprise-state-roaming-group-policy-settings.md) |Windows 10 nabízí zásad skupiny a správy mobilních zařízení (MDM) nastavení zásad k omezení nastavení synchronizace. |
| [Informace o cestovní nastavení Windows 10](enterprise-state-roaming-windows-settings-reference.md) |Následuje úplný seznam všech nastavení, které bude možné s roamingem a/nebo zálohování ve Windows 10. |
| [Odstraňování potíží](enterprise-state-roaming-troubleshooting.md) |Toto téma prochází některé základní kroky pro řešení potíží a obsahuje seznam známých problémů. |

## <a name="next-steps"></a>Další postup

Informace o povolení enterprise stav roamingu najdete v tématu [povolit roaming stavu enterprise](enterprise-state-roaming-enable.md).
