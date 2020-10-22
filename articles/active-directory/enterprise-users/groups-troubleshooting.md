---
title: Oprava problémů s dynamickými členství ve skupinách – Azure AD | Microsoft Docs
description: Tipy pro řešení potíží pro členství v dynamické skupině v Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8730ac8aa6a6056db67613f2ac8decf11740c467
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92374892"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>Řešení potíží se skupinami a řešení problémů

## <a name="troubleshooting-group-creation-issues"></a>Řešení potíží s vytvářením skupin

**V Azure Portal bylo zakázané vytvořit skupinu zabezpečení, ale skupiny se dají vytvářet i přes PowerShell** . **Uživatel může vytvořit skupiny zabezpečení na portálech Azure** v nastavení Azure Portal určuje, jestli uživatelé, kteří nejsou správci, můžou vytvářet skupiny zabezpečení na přístupovém panelu nebo v Azure Portal. Neřídí vytváření skupin zabezpečení prostřednictvím PowerShellu.

Zakázání vytváření skupin pro uživatele, kteří nejsou správci, v PowerShellu:
1. Ověřte, že uživatelé, kteří nejsou správci, můžou vytvářet skupiny:
   

   ```powershell
   Get-MsolCompanyInformation | Format-List UsersPermissionToCreateGroupsEnabled
   ```

  
2. Pokud se vrátí `UsersPermissionToCreateGroupsEnabled : True` , pak uživatelé bez role správce můžou vytvářet skupiny. Zakázání této funkce:
  

   ``` 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```

<br/>**Při pokusu o vytvoření dynamické skupiny v prostředí PowerShell se mi zobrazila chyba maximálního počtu povolených skupin**<br/>
Pokud se v PowerShellu zobrazí zpráva, že se _dosáhlo maximálního počtu povolených skupin zásad dynamické skupiny_, znamená to, že jste dosáhli maximálního limitu pro dynamické skupiny ve vaší organizaci. Maximální počet dynamických skupin na organizaci je 5 000.

Pokud chcete vytvořit nové dynamické skupiny, musíte nejdřív odstranit některé existující dynamické skupiny. Neexistuje žádný způsob, jak tento limit navýšit.

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>Řešení potíží s dynamickým členstvím ve skupinách

**Nakonfiguroval (a) jsem pravidlo pro skupinu, ale ve skupině se neaktualizovalo žádné členství.**<br/>
1. Ověřte hodnoty atributů uživatele nebo zařízení v pravidle. Ujistěte se, že existují uživatelé, kteří pravidlo splní. U zařízení zkontrolujte vlastnosti zařízení a zajistěte, aby synchronizované atributy obsahovaly očekávané hodnoty.<br/>
2. Zkontrolujte stav zpracování členství a potvrďte, zda je dokončeno. [Stav zpracování členství](groups-create-rule.md#check-processing-status-for-a-rule) a datum poslední aktualizace můžete ověřit na stránce **Přehled** pro danou skupinu.

Pokud vše vypadá dobře, počkejte prosím nějakou dobu, než se skupina naplní. V závislosti na velikosti vaší organizace Azure AD může tato skupina trvat až 24 hodin, než se poprvé naplní nebo se změní pravidlo.

**Nakonfiguroval (a) jsem pravidlo, ale teď se odeberou stávající členové pravidla**<br/>Toto chování je očekávané. Existující členové skupiny se odeberou, když je pravidlo povolené nebo se změní. Uživatelé, kteří se vrátili z vyhodnocení pravidla, se přidají do skupiny jako členové.

**Nevidím při přidání nebo změně pravidla změny členství, proč ne?**<br/>Vyhrazené vyhodnocení členství je provedeno pravidelně v asynchronním procesu na pozadí. Doba trvání procesu je určena počtem uživatelů v adresáři a velikostí skupiny vytvořenou v důsledku pravidla. Obvykle se v adresářích s malým počtem uživatelů zobrazí změny členství ve skupinách během méně než několik minut. V adresářích s velkým počtem uživatelů může být naplnění trvat 30 minut nebo déle.

**Jak můžu vynutit, aby se skupina právě zpracovala?**<br/>
V současné době neexistuje způsob, jak automaticky aktivovat skupinu, která se má zpracovat na vyžádání. Opětovnou rezpracování můžete ale aktivovat ručně tak, že aktualizujete pravidlo členství, aby se na konci přidalo prázdné místo.  

**Zjistila se chyba zpracování pravidla**<br/>V následující tabulce jsou uvedeny běžné chyby pravidla dynamického členství a jejich oprava.

| Chyba analyzátoru pravidla | Chyba použití | Opravené použití |
| --- | --- | --- |
| Chyba: atribut není podporován. |(User. invalidProperty-EQ "value") |(User. Department-EQ "hodnota")<br/><br/>Ujistěte se, že je atribut v [seznamu podporovaných vlastností](groups-dynamic-membership.md#supported-properties). |
| Chyba: operátor není podporován u atributu. |(User. accountEnabled-Contains true) |(User. accountEnabled-EQ true)<br/><br/>Použitý operátor není podporován pro typ vlastnosti (v tomto příkladu-Contains nelze použít pro typ Boolean). Použijte správné operátory pro typ vlastnosti. |
| Chyba: Chyba kompilace dotazu. | 1. (User. Department-EQ "prodej") (User. Department-EQ "marketing")<br>2. (User. userPrincipalName-Match "* @domain.ext ") | 1. chybí operátor. Použití nebo dva predikáty JOIN<br>(User. Department-EQ "Sales")-or (User. Department-EQ "marketing")<br>2. Chyba v regulárním výrazu se používá s-Match.<br>(User. userPrincipalName-Match ". * @domain.ext ")<br>nebo alternativně: (User. userPrincipalName-Match " @domain.ext $") |

## <a name="next-steps"></a>Další kroky

Následující články poskytují další informace o službě Azure Active Directory.

* [Správa přístupu k prostředkům pomocí skupin služby Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Správa aplikací ve službě Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Co je Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md)