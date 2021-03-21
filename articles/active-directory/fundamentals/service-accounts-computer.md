---
title: Zabezpečení účtů počítačů | Azure Active Directory
description: Průvodce zabezpečením místních účtů počítačů.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22faba20cb12ae755f19fe43c295d98f9b364cbe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100417194"
---
# <a name="securing-computer-accounts"></a>Zabezpečení počítačových účtů

Účet počítače nebo účet LocalSystem je vestavěný, vysoce privilegovaný účet s přístupem k prakticky všem prostředkům v místním počítači. Tento účet není přidružen k žádnému přihlášenému uživatelskému účtu. Služby spuštěné s přístupem k síťovým prostředkům v uživatelském účtu představují přihlašovací údaje počítače na vzdálené servery. Zobrazuje pověření ve formuláři <domain_name>\<computer_name> $. Předem definovaný název účtu počítače je NT AUTHORITY\SYSTEM.. Dá se použít ke spuštění služby a poskytování kontextu zabezpečení pro tuto službu.

![[Obrázek 4] (.\media\securing-service-accounts\secure-computer-accounts-image-1.png)](.\media\securing-service-accounts\secure-computer-accounts-image-1.png)

## <a name="benefits-of-using-the-computer-account"></a>Výhody použití účtu počítače

Účet počítače přináší následující výhody.

* **Neomezený místní přístup**: účet počítače poskytuje úplný přístup k místním prostředkům počítače.

* **Automatická správa hesel**: účet počítače odstraňuje nutnost ruční změny hesel. Místo toho je tento účet členem služby Active Directory a heslo účtu se mění automaticky. Eliminuje také nutnost registrovat hlavní název služby pro službu.

* **Omezená přístupová práva mimo počítač**: výchozí seznam Access Control (ACL) v Active Directory Domain Services umožňuje minimální přístup pro účty počítačů. Pokud by tato služba byla napadená, měl by mít jenom omezený přístup k prostředkům ve vaší síti.

## <a name="assess-security-posture-of-computer-accounts"></a>Posouzení stav zabezpečení účtů počítačů

Potenciální problémy a související zmírnění při použití účtů počítačů. 

| Problémy| Omezení rizik |
| - | - |
| Účty počítačů podléhají odstranění a rekreace, když počítač opustí a znovu připojí doménu.| Ověřte, že je potřeba přidat počítač do skupiny AD, a ověřte, který účet počítače se přidal do skupiny, a to pomocí ukázkových skriptů uvedených na této stránce.| 
| Pokud přidáte účet počítače do skupiny, budou mít všechny služby spuštěné jako LocalSystem v tomto počítači přístupová práva ke skupině.| Vycházet z výběru členství účtu počítače ve skupině. Vyhněte se vytváření účtů počítačů u všech skupin správců domény, protože přidružená služba má úplný přístup k Active Directory Domain Services. |
| Nesprávné výchozí hodnoty sítě pro LocalSystem| Nepředpokládá se, že účet počítače má výchozí omezený přístup k síťovým prostředkům. Místo toho si pečlivě Projděte členství ve skupinách pro tento účet. |
| Neznámé služby spuštěné pod účtem LocalSystem| Ujistěte se, že všechny služby spuštěné pod účtem LocalSystem jsou služby Microsoftu nebo důvěryhodné služby od třetích stran. |


## <a name="find-services-running-under-the-computer-account"></a>Najít služby spuštěné pod účtem počítače

K vyhledání služeb spuštěných v kontextu LocalSystem použijte následující rutinu PowerShellu.

```powershell

Get-WmiObject win32_service | select Name, StartName | Where-Object {($_.StartName -eq "LocalSystem")}
```

**Najde účty počítačů, které jsou členy konkrétní skupiny.**

K vyhledání účtů počítačů, které jsou členy konkrétní skupiny, použijte následující rutinu prostředí PowerShell.

```powershell

```Get-ADComputer -Filter {Name -Like "*"} -Properties MemberOf | Where-Object {[STRING]$_.MemberOf -like "Your_Group_Name_here*"} | Select Name, MemberOf
```

**Najde účty počítačů, které jsou členy privilegovaných skupin.**

K vyhledání účtů počítačů, které jsou členy skupin identity Administrators (Domain Admins, Enterprise Admins, Administrators), použijte následující rutinu PowerShellu.

```powershell
Get-ADGroupMember -Identity Administrators -Recursive | Where objectClass -eq "computer"
```
## <a name="move-from-computer-accounts"></a>Přesunout z účtů počítačů

> [!IMPORTANT]
> Účty počítačů jsou účty s vysokou úrovní oprávnění a měly by se používat jenom v případě, že vaše služba potřebuje neomezený přístup k místním prostředkům v počítači a nemůžete použít účet spravované služby (MSA).

* Obraťte se na vlastníka služby, pokud je možné spustit službu pomocí MSA, a pokud ji vaše služba podporuje, použijte skupinový účet spravované služby (gMSA) nebo samostatný účet spravované služby (sMSA).

* Použijte účet uživatele domény, který má jenom oprávnění potřebná ke spuštění vaší služby.

## <a name="next-steps"></a>Další kroky 

Přečtěte si následující články týkající se zabezpečení účtů služeb.

* [Seznámení s místními účty služeb](service-accounts-on-premises.md)

* [Zabezpečené skupiny účtů spravované služby](service-accounts-group-managed.md)

* [Zabezpečení samostatných účtů spravované služby](service-accounts-standalone-managed.md)

* [Zabezpečení účtů počítačů](service-accounts-computer.md)

* [Zabezpečení uživatelských účtů](service-accounts-user-on-premises.md)

* [Řízení účtů místních služeb](service-accounts-govern-on-premises.md)

 

 
