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
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 09/11/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: ab5a3b00d063dfdd42e67247bb2cdc37866d0164
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55164125"
---
# <a name="troubleshooting-dynamic-memberships-for-groups"></a>Řešení potíží s dynamickým členstvím ve skupinách

**Mohu konfigurovat pravidlo pro skupinu, ale aktualizovat žádné členství ve skupině**<br/>Ověřte hodnoty pro atributy uživatele v pravidle: existují uživatelé, kteří splňují pravidla? Pokud vše vypadá v pořádku, počkejte prosím chvíli se skupina naplní. V závislosti na velikosti tenanta může první naplnění skupiny nebo její naplnění po změně pravidel trvat až 24 hodin.

**Jsem nakonfiguroval pravidla, ale teď jsou odebrána existující členy pravidla**<br/>Toto je očekávané chování. Existující členy skupiny se odeberou, když pravidlo povolit nebo změnit. Uživatelé vrácená vyhodnocení tohoto pravidla jsou přidáni jako členové do skupiny.

**Nevidím, že se změní okamžitě při přidání nebo změna pravidla, případně proč bezpečná není?**<br/>Hodnocení vyhrazené členství se provádí pravidelně v procesu asynchronní na pozadí. Jak dlouho trvá procesu se určuje podle počtu uživatelů ve vašem adresáři a velikost skupiny vytvořené v důsledku pravidlo. Obvykle adresáře s malý počet uživatelů se zobrazí změny členství ve skupině za méně než několik minut. Adresáře s velkým množstvím uživatelů může trvat 30 minut nebo i delší dobu naplnit.

**Vyskytl se pravidlo chyba zpracování**<br/>V následující tabulce jsou uvedeny běžné chyby pravidla dynamického členství a jak je opravit.

| Chyba analyzátoru pravidla | Chyba využití | Opravené využití |
| --- | --- | --- |
| Chyba: Atribut není podporován. |(user.invalidProperty - eq "Value") |(user.department - eq "value")<br/><br/>Ujistěte se, že se o atribut [podporované seznam vlastností](groups-dynamic-membership.md#supported-properties). |
| Chyba: Operátor není podporován u atributu. |(user.accountEnabled – obsahuje hodnotu true) |(user.accountEnabled - eq true)<br/><br/>Operátor používá není podporován pro tento typ vlastnosti (v tomto příkladu – obsahuje nedá použít u typu boolean). Použijte správnou operátory pro tento typ vlastnosti. |
| Chyba: Chyba při kompilaci dotazu. | 1. (user.department - eq "Prodej") (user.department - eq "Marketing")<br>2. (user.userPrincipalName-odpovídají "*@domain.ext") | 1. Chybějící operátor. Pomocí parametru - a - nebo dvě spojení predikátů<br>(user.department - eq "Prodej")- nebo (user.department - eq "Marketing")<br>2. Chyba v regulární výraz používaný s parametrem - odpovídat<br>(user.userPrincipalName-odpovídají ". *@domain.ext")<br>nebo alternativně: (user.userPrincipalName-odpovídají "@domain.ext$") |

## <a name="next-steps"></a>Další postup

Následující články poskytují další informace o službě Azure Active Directory.

* [Správa přístupu k prostředkům pomocí skupin služby Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Správa aplikací ve službě Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Představení služby Azure Active Directory](../fundamentals/active-directory-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
