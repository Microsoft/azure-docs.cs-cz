---
title: Co je roaming státních přenosů v Azure Active Directory?
description: Enterprise State Roaming poskytuje uživatelům jednotný zážitek ze svých zařízení s Windows.
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "77194275"
---
# <a name="what-is-enterprise-state-roaming"></a>Co je Enterprise State Roaming?

S Windows 10 uživatelé [Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) získají možnost bezpečně synchronizovat data uživatelských nastavení a nastavení aplikace do cloudu. Enterprise State Roaming poskytuje uživatelům jednotný zážitek ze svých zařízení s Windows a zkracuje dobu potřebnou ke konfiguraci nového zařízení. Enterprise State Roaming funguje podobně jako standardní [synchronizace nastavení zákazníků](https://go.microsoft.com/fwlink/?linkid=2015135) , která byla poprvé zavedena ve Windows 8. Kromě toho Enterprise State Roaming nabídky:

* **Oddělení podnikových a zákaznických dat** – organizace mají kontrolu nad svými daty a neexistují žádné kombinace podnikových dat v účtu cloudového a zákaznického data v podnikovém cloudovém účtu.
* **Rozšířené zabezpečení** – data se šifrují automaticky před tím, než se zařízení s Windows 10 opustí pomocí Azure Rights Management (Azure RMS), a data zůstanou v cloudu zašifrovaná v klidovém prostředí. Veškerý obsah zůstává zašifrovaný v klidovém stavu v cloudu, s výjimkou oborů názvů, jako jsou názvy nastavení a názvy aplikací pro Windows.  
* **Lepší správa a monitorování** – poskytuje kontrolu a viditelnost při synchronizaci nastavení ve vaší organizaci a na tom, která zařízení prostřednictvím integrace portálu Azure AD. 

Enterprise State Roaming je k dispozici ve více oblastech Azure. Aktualizovaný seznam dostupných oblastí najdete na stránce [služby Azure podle oblastí](https://azure.microsoft.com/regions/#services) v části Azure Active Directory.

| Článek | Popis |
| --- | --- |
| [Povolení služby Enterprise State Roaming v Azure Active Directory](enterprise-state-roaming-enable.md) |Enterprise State Roaming je k dispozici pro libovolnou organizaci s předplatným Premium Azure Active Directory (Azure AD). Další informace o tom, jak získat předplatné služby Azure AD, najdete na stránce [produktu Azure AD](https://azure.microsoft.com/services/active-directory) . |
| [Nejčastější dotazy k nastavení a datovému roamingu](enterprise-state-roaming-faqs.md) |Tento článek obsahuje odpovědi na některé otázky, které správci IT můžou narazit na nastavení a synchronizaci dat aplikace. |
| [Zásady skupiny a nastavení MDM pro synchronizaci nastavení](enterprise-state-roaming-group-policy-settings.md) |Windows 10 poskytuje Zásady skupiny a nastavení zásad správy mobilních zařízení (MDM) k omezení synchronizace nastavení. |
| [Referenční informace k nastavení roamingu pro Windows 10](enterprise-state-roaming-windows-settings-reference.md) |Seznam nastavení, která budou v systému Windows 10 roamingovaná a zálohovaná. |
| [Řešení potíží](enterprise-state-roaming-troubleshooting.md) |Tento článek prochází několik základních kroků pro řešení potíží a obsahuje seznam známých problémů. |

## <a name="next-steps"></a>Další kroky

Informace o povolení roamingu podnikového stavu najdete v tématu [Povolení služby Enterprise State Roaming](enterprise-state-roaming-enable.md).
