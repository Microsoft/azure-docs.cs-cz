---
title: Opravte problémy dynamické členství pro skupiny – Azure Active Directory | Dokumentace Microsoftu
description: Tipy pro řešení potíží pro dynamické členství ve skupinách ve službě Azure AD.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a1210360690384b07e6d88007ccd118731ecce0
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57405432"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>Odstraňovat potíže a řešit problémy skupiny

## <a name="troubleshooting-group-creation-issues"></a>Řešení potíží s vytvořením skupiny
**Můžu zakázané vytvoření skupiny zabezpečení na webu Azure Portal, ale skupiny stále možné vytvářet přes Powershell** **uživatele můžou na portálech Azure vytvářet skupiny zabezpečení** nastavení v ovládacích prvcích Azure portal, zda je či není bez oprávnění správce. uživatelé můžou vytvářet skupiny zabezpečení v přístupovém panelu nebo na webu Azure portal. Ho neřídí vytvoření skupiny zabezpečení přes Powershell.

Chcete-li zakázat vytvoření skupiny pro uživatele bez oprávnění správce v prostředí Powershell:
1. Ověřte, že uživatelé bez oprávnění správce můžou vytvářet skupiny:
   
  ```
  PS C:\> Get-MsolCompanyInformation | fl UsersPermissionToCreateGroupsEnabled
  ```
  
2. Vrátí-li `UsersPermissionToCreateGroupsEnabled : True`, pak uživatelé bez oprávnění správce můžou vytvářet skupiny. Chcete zakázat tuto funkci:
  
  ``` 
  Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
  ```

<br/>**Zobrazila se mi maximální počet skupin povolena Chyba při pokusu o vytvoření dynamické skupiny v prostředí Powershell**<br/>
Pokud se zobrazí zpráva v prostředí Powershell, která označuje _dynamická skupina zásady maximální povolený počet skupin dosaženo_, to znamená, že jste dosáhli maximálního limitu pro dynamické skupiny ve vašem tenantovi. Maximální počet dynamické skupiny na tenanta je 5 000.

Vytvářet žádné nové dynamické skupiny, budete nejdřív muset odstraňte některé stávající dynamických skupin. Neexistuje žádný způsob, jak zvýšit limit.

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>Řešení potíží s dynamickým členstvím ve skupinách

**Mohu konfigurovat pravidlo pro skupinu, ale aktualizovat žádné členství ve skupině**<br/>
1. Ověřte hodnoty pro uživatele nebo zařízení atributy v pravidle. Ujistěte se, že uživatelé, kteří splňují pravidla. Pro zařízení zkontrolujte vlastnosti zařízení, aby všechny synchronizované atributy obsahují očekávané hodnoty.<br/>
2. Zkontrolujte členství zpracování stavu potvrzení, pokud je kompletní. Můžete zkontrolovat [členství stav zpracování](\groups-create-rule.md#check-processing-status-for-a-rule) a datum poslední aktualizace na **přehled** stránce pro skupinu.

Pokud vše vypadá v pořádku, počkejte prosím chvíli se skupina naplní. V závislosti na velikosti tenanta může první naplnění skupiny nebo její naplnění po změně pravidel trvat až 24 hodin.

**Jsem nakonfiguroval pravidla, ale teď jsou odebrána existující členy pravidla**<br/>Toto je očekávané chování. Existující členy skupiny se odeberou, když pravidlo povolit nebo změnit. Uživatelé vrácená vyhodnocení tohoto pravidla jsou přidáni jako členové do skupiny.

**Nevidím, že se změní okamžitě při přidání nebo změna pravidla, případně proč bezpečná není?**<br/>Hodnocení vyhrazené členství se provádí pravidelně v procesu asynchronní na pozadí. Jak dlouho trvá procesu se určuje podle počtu uživatelů ve vašem adresáři a velikost skupiny vytvořené v důsledku pravidlo. Obvykle adresáře s malý počet uživatelů se zobrazí změny členství ve skupině za méně než několik minut. Adresáře s velkým množstvím uživatelů může trvat 30 minut nebo i delší dobu naplnit.

**Jak můžete vynutit skupinu, kterou chcete zpracovat, nyní?**<br/>
V současné době neexistuje žádný způsob, jak automaticky aktivuje skupina ke zpracování na vyžádání. Můžete však ručně aktivujete opětovné zpracování pravidla členství, chcete-li přidat prázdný znak na konci aktualizací.  

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
