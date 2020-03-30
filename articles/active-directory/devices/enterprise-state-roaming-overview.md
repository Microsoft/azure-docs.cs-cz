---
title: Co je roaming ve stavu organizace ve službě Azure Active Directory?
description: Program Enterprise State Roaming poskytuje uživatelům jednotné prostředí na všech svých zařízeních s Windows
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: c22baf0a08718883f0c0c9844cc395f607b5b20d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77194275"
---
# <a name="what-is-enterprise-state-roaming"></a>Co je Enterprise State Roaming?

S Windows 10 získají uživatelé [Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) možnost bezpečně synchronizovat svá uživatelská nastavení a data nastavení aplikací do cloudu. Program Enterprise State Roaming poskytuje uživatelům jednotné prostředí navšech svých zařízeních se systémem Windows a zkracuje dobu potřebnou ke konfiguraci nového zařízení. Program Enterprise State Roaming funguje podobně jako standardní [synchronizace nastavení pro spotřebitele,](https://go.microsoft.com/fwlink/?linkid=2015135) která byla poprvé zavedena v systému Windows 8. Podnikový státní roaming navíc nabízí:

* **Oddělení podnikových a spotřebitelských dat** – Organizace mají kontrolu nad svými daty a v účtu podnikového cloudu není žádné míchání podnikových dat v účtu spotřebitelského cloudu nebo spotřebitelských dat v účtu podnikového cloudu.
* **Rozšířené zabezpečení** – data se automaticky zašifrují před opuštěním zařízení s Windows 10 uživatele pomocí Azure Rights Management (Azure RMS) a data zůstanou šifrovaná v klidovém stavu v cloudu. Veškerý obsah zůstane šifrovaný v klidovém stavu v cloudu, s výjimkou oborů názvů, jako jsou názvy nastavení a názvy aplikací pro Windows.  
* **Lepší správa a monitorování** – poskytuje kontrolu a přehled o tom, kdo synchronizuje nastavení ve vaší organizaci a na kterých zařízeních prostřednictvím integrace portálu Azure AD. 

Enterprise State Roaming je k dispozici ve více oblastech Azure. Aktualizovaný seznam dostupných oblastí najdete na stránce [Služby Azure podle oblastí](https://azure.microsoft.com/regions/#services) v části Azure Active Directory.

| Článek | Popis |
| --- | --- |
| [Povolení služby Enterprise State Roaming v Azure Active Directory](enterprise-state-roaming-enable.md) |Roaming stavu enterprise je k dispozici pro všechny organizace s předplatným Služby Premium Azure Active Directory (Azure AD). Další informace o tom, jak získat předplatné Azure AD, najdete na stránce [produktu Azure AD.](https://azure.microsoft.com/services/active-directory) |
| [Nejčastější dotazy k nastavení a datovému roamingu](enterprise-state-roaming-faqs.md) |Tento článek odpovídá na některé otázky, které mohou správci IT mít ohledně nastavení a synchronizace dat aplikací. |
| [Zásady skupiny a nastavení MDM pro synchronizaci nastavení](enterprise-state-roaming-group-policy-settings.md) |Windows 10 poskytuje nastavení zásad správy zásad skupiny a mobilních zařízení (MDM), aby se omezila synchronizace nastavení. |
| [Referenční informace k nastavení roamingu pro Windows 10](enterprise-state-roaming-windows-settings-reference.md) |Seznam nastavení, která se budou přepínat nebo zálohovat ve Windows 10. |
| [Řešení potíží](enterprise-state-roaming-troubleshooting.md) |Tento článek prochází některé základní kroky pro řešení potíží a obsahuje seznam známých problémů. |

## <a name="next-steps"></a>Další kroky

Informace o povolení podnikového státního roamingu naleznete v [tématu povolení podnikového státního roamingu](enterprise-state-roaming-enable.md).
