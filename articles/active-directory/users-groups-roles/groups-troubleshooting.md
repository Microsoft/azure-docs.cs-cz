---
title: Řešení potíží s dynamické členství ve skupinách | Dokumentace Microsoftu
description: Tipy pro řešení potíží pro dynamické členství ve skupinách ve službě Azure AD.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: aaded644635ab93cef9323ad38d1d150b15fe743
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449780"
---
# <a name="troubleshooting-dynamic-memberships-for-groups"></a>Řešení potíží s dynamickým členstvím ve skupinách
**Mohu konfigurovat pravidlo pro skupinu, ale aktualizovat žádné členství ve skupině**<br/>Ověřte hodnoty pro atributy uživatele v pravidle: existují uživatelé, kteří splňují pravidla? Pokud vše vypadá v pořádku, počkejte prosím chvíli se skupina naplní. V závislosti na velikosti tenanta může první naplnění skupiny nebo její naplnění po změně pravidel trvat až 24 hodin.

**Jsem nakonfiguroval pravidla, ale teď jsou odebrána existující členy pravidla**<br/>Toto je očekávané chování. Existující členy skupiny se odeberou, když pravidlo povolit nebo změnit. Uživatelé vrácená vyhodnocení tohoto pravidla jsou přidáni jako členové do skupiny.     

**Nevidím, že se změní okamžitě při přidání nebo změna pravidla, případně proč bezpečná není?**<br/>Hodnocení vyhrazené členství se provádí pravidelně v procesu asynchronní na pozadí. Jak dlouho trvá procesu se určuje podle počtu uživatelů ve vašem adresáři a velikost skupiny vytvořené v důsledku pravidlo. Obvykle adresáře s malý počet uživatelů se zobrazí změny členství ve skupině za méně než několik minut. Adresáře s velkým množstvím uživatelů může trvat 30 minut nebo i delší dobu naplnit.

### <a name="next-steps"></a>Další postup
Následující články poskytují další informace o službě Azure Active Directory.

* [Správa přístupu k prostředkům pomocí skupin služby Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Rejstřík článků o správě aplikací ve službě Azure Active Directory](../active-directory-apps-index.md)
* [Představení služby Azure Active Directory](../fundamentals/active-directory-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](../connect/active-directory-aadconnect.md)
