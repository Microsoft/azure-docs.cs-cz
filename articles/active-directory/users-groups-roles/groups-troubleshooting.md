---
title: Řešení problémů s dynamickými členstvími ve skupinách – Azure AD | Dokumenty společnosti Microsoft
description: Tipy pro řešení potíží s dynamickým členstvím ve skupinách ve službě Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f685ac63e3b4a8cf466be4eb4561472fb084d49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74026545"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>Poradce při potížích se skupinami a jejich řešení

## <a name="troubleshooting-group-creation-issues"></a>Poradce při potížích s vytvářením skupiny

**Zakázal jsem vytváření skupin zabezpečení na webu Azure Portal, ale skupiny se pořád můžou vytvářet přes Powershell.** **Uživatel může vytvářet skupiny zabezpečení v** azure portálech nastavení na webu Azure Portal řídí, zda uživatelé bez oprávnění správce můžete vytvořit skupiny zabezpečení v panelu Access nebo na portálu Azure. Neřídí vytváření skupin zabezpečení prostřednictvím prostředí Powershell.

Zakázání vytváření skupin pro uživatele, kteří nejsou správci v Powershellu:
1. Ověřte, zda mohou uživatelé, kteří nejsou správci, vytvářet skupiny:
   

   ```powershell
   Get-MsolCompanyInformation | Format-List UsersPermissionToCreateGroupsEnabled
   ```

  
2. Pokud se `UsersPermissionToCreateGroupsEnabled : True`vrátí , mohou skupiny vytvářet uživatelé, kteří nejsou správci. Zakázání této funkce:
  

   ``` 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```

<br/>**Při pokusu o vytvoření dynamické skupiny v prostředí Powershellu se zobrazila chyba max skupin, která byla povolena**<br/>
Pokud se v powershellu zobrazí zpráva o tom, že bylo _dosaženo maximálního počtu povolených skupin dynamických zásad skupin_, znamená to, že jste dosáhli maximálního limitu pro dynamické skupiny ve vašem tenantovi. Maximální počet dynamických skupin na klienta je 5 000.

Chcete-li vytvořit nové dynamické skupiny, musíte nejprve odstranit některé existující dynamické skupiny. Neexistuje žádný způsob, jak zvýšit limit.

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>Řešení potíží s dynamickým členstvím ve skupinách

**Nakonfiguroval jsem pravidlo pro skupinu, ale žádná členství se ve skupině neaktualizovala**<br/>
1. Ověřte hodnoty atributů uživatele nebo zařízení v pravidle. Ujistěte se, že existují uživatelé, kteří splňují pravidlo. U zařízení zkontrolujte vlastnosti zařízení, abyste se ujistili, že všechny synchronizované atributy obsahují očekávané hodnoty.<br/>
2. Zkontrolujte stav zpracování členství a potvrďte, zda je dokončen. Stav zpracování [členství](groups-create-rule.md#check-processing-status-for-a-rule) a poslední aktualizované datum můžete zkontrolovat na stránce **Přehled** pro skupinu.

Pokud vše vypadá dobře, prosím, vyčkejte nějaký čas pro skupinu naplnit. V závislosti na velikosti tenanta může první naplnění skupiny nebo její naplnění po změně pravidel trvat až 24 hodin.

**Nakonfiguroval jsem pravidlo, ale stávající členové pravidla jsou odebráni**<br/>Toto chování je očekávané. Stávající členové skupiny jsou odebráni, pokud je pravidlo povoleno nebo změněno. Uživatelé vrácené z vyhodnocení pravidla jsou přidáni jako členové do skupiny.

**Při přidání nebo změně pravidla se členství nezobrazí okamžitě, proč ne?**<br/>Vyhrazené hodnocení členství se provádí pravidelně v procesu asynchronního pozadí. Jak dlouho proces trvá, je určen počtem uživatelů v adresáři a velikostí skupiny vytvořené v důsledku pravidla. Adresářům s malým počtem uživatelů se obvykle změny členství ve skupině změní za méně než několik minut. Naplnění adresářů s velkým počtem uživatelů může trvat 30 minut nebo déle.

**Jak mohu vynutit, aby byla skupina zpracována nyní?**<br/>
V současné době neexistuje žádný způsob, jak automaticky spustit skupinu, která má být zpracována na vyžádání. Opětovné zpracování však můžete ručně spustit aktualizací pravidla členství a přidat na konec prázdné znaky.  

**Došlo k chybě při zpracování pravidla**<br/>V následující tabulce jsou uvedeny běžné chyby pravidel dynamického členství a jejich oprava.

| Chyba analyzátoru pravidel | Použití chyby | Opravené použití |
| --- | --- | --- |
| Chyba: Atribut není podporován. |(user.invalidProperty -eq "Hodnota") |(user.department -eq "hodnota")<br/><br/>Zkontrolujte, zda je atribut uveden v [seznamu podporovaných vlastností](groups-dynamic-membership.md#supported-properties). |
| Chyba: Operátor není u atributu podporován. |(user.accountEnabled -obsahuje true) |(user.accountEnabled -eq true)<br/><br/>Použitý operátor není podporován pro typ vlastnosti (v tomto příkladu -contains nelze použít pro typ logické hodnoty). Pro typ vlastnosti použijte správné operátory. |
| Chyba: Chyba kompilace dotazu. | 1. (user.department -eq "Prodej") (user.department -eq "Marketing")<br>2. (user.userPrincipalName -match@domain.ext"* ") | 1. Chybějící operátor. Použití -a nebo -nebo dvě předindikáty spojení<br>(user.department -eq "Prodej") -nebo (user.department -eq "Marketing")<br>2. Chyba v regulárním výrazu použitém s -match<br>(user.userPrincipalName -match ".*@domain.ext")<br>nebo alternativně: (user.userPrincipalName@domain.ext-match " $") |

## <a name="next-steps"></a>Další kroky

Následující články poskytují další informace o službě Azure Active Directory.

* [Správa přístupu k prostředkům pomocí skupin služby Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Správa aplikací ve službě Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Představení služby Azure Active Directory](../fundamentals/active-directory-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md)