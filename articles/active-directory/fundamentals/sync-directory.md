---
title: Synchronizace adresářů s Azure Active Directory
description: Pokyny pro architekturu k dosažení synchronizace adresářů pomocí Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98aa697c0120fd8a20adf11ad83e02406499a0d1
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101648488"
---
# <a name="directory-synchronization"></a>Synchronizace adresářů

Mnoho organizací má hybridní infrastrukturu zahrnující místní i cloudové součásti. Synchronizace identit uživatelů mezi místními a cloudovým adresářem umožňuje uživatelům přístup k prostředkům s jednou sadou přihlašovacích údajů. 

Synchronizace je proces 

* Vytvoření objektu na základě určitých podmínek
* zachování aktualizovaného objektu
* odebrání objektu, pokud podmínky již nejsou splněny. 

Místní zřizování zahrnuje zřizování z místních zdrojů (například služby Active Directory) pro Azure Active Directory (Azure AD). 

## <a name="use-when"></a>Kdy použít

Je potřeba synchronizovat data identity z místních prostředí Active Directory do Azure AD.

![Diagram architektury](./media/authentication-patterns/dir-sync-auth.png)

## <a name="components-of-system"></a>Součásti systému

* **Uživatel**: přistupuje k aplikaci pomocí Azure AD.

* **Webový prohlížeč**: komponenta, s jakou uživatel pracuje, aby mohla přistupovat k externí adrese URL aplikace.

* **Aplikace**: webová aplikace, která spoléhá na použití Azure AD pro účely ověřování a autorizace.

* **Azure AD**: synchronizuje informace o identitě z místního adresáře organizace prostřednictvím Azure AD Connect. 

* **Azure AD Connect**: Nástroj pro připojení k místním infrastrukturám identity, který se má Microsoft Azure AD. Průvodce a průvodce vám pomůžou nasadit a nakonfigurovat požadované součásti a komponenty potřebné pro připojení, včetně synchronizace a přihlášení z aktivních adresářů do Azure AD. 

* **Active Directory**: Služba Active Directory je adresářová služba obsažená ve většině operačních systémů Windows Server. Servery se systémem Active Directory Domain Services (služba AD DS) se nazývají řadiče domény. Ověřují a autorizují všechny uživatele a počítače v doméně.

## <a name="implement-directory-synchronization-with-azure-ad"></a>Implementace synchronizace adresářů pomocí Azure AD

* [Co je zřizování identit?](../cloud-sync/what-is-provisioning.md) 

* [Nástroje pro integraci adresáře Hybrid identity](../hybrid/plan-hybrid-identity-design-considerations-tools-comparison.md) 

* [Průvodce instalací Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md)