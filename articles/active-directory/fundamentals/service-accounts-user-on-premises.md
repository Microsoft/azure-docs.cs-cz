---
title: Zabezpečení účtů služeb založených na uživatelích | Azure Active Directory
description: Průvodce zabezpečením místních uživatelských účtů.
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
ms.openlocfilehash: e484bdda33142024f2067649eaa67042fe7776f8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100417177"
---
# <a name="securing-user-based-service-accounts-in-active-directory"></a>Zabezpečení účtů služeb založených na uživateli ve službě Active Directory

Místní uživatelské účty představují tradiční přístup k zabezpečení služeb spuštěných v systému Windows. Tyto účty používejte jako poslední možnost, pokud vaše služba nepodporuje globální účty spravované služby (účty gMSA) a samostatné účty spravované služby (sMSAs). Informace o výběru nejlepšího typu účtu, který se má použít, najdete v tématu Přehled účtů místních služeb. Prozkoumejte také, jestli můžete službu přesunout tak, aby používala účet služby Azure, jako je spravovaná identita nebo Princip služby. 

Místní uživatelské účty je možné vytvářet za účelem poskytování kontextu zabezpečení pro služby a udělení oprávnění, která jsou vyžadována pro služby pro přístup k místním a síťovým prostředkům. Vyžadují ruční správu hesel podobně jako jakýkoli jiný uživatelský účet služby Active Directory (AD). Správci služeb a domén jsou povinni sledovat procesy silné správy hesel a zajistit tak zabezpečení těchto účtů.

Při použití uživatelského účtu jako účtu služby ho používejte jenom pro jednu službu. Pojmenujte je tak, aby bylo jasné, že se jedná o účet služby a pro kterou službu. 

## <a name="benefits-and-challenges"></a>Výhody a problémy

Výhody

Místní uživatelské účty jsou nejflexibilním typem účtu pro použití se službami. Uživatelské účty používané jako účty služeb se dají řídit všemi zásadami, které řídí běžné uživatelské účty. Ty je ale používejte, jenom pokud nemůžete použít MSA. Vyhodnoťte také, jestli je účet počítače lepší volbou. 

Problémy s místními uživatelskými účty

K používání místních uživatelských účtů jsou přidruženy následující problémy.

| Výzvy| Omezení rizik |
| - | - |
| Správa hesel je ruční proces, který může vést ke slabému výpadku zabezpečení a provozu.| Zajistěte, aby se složitost hesla a změna hesla řídily robustním procesem, který zajišťuje pravidelné aktualizace se silným heslem. <br> Koordinovat změnu hesla pomocí aktualizace hesla ve službě, což bude mít za následek výpadek služby. |
| Identifikace místních uživatelských účtů, které fungují jako účty služeb, může být obtížné.| Dokumentování a údržba záznamů účtů služeb nasazených ve vašem prostředí. <br> Sledujte název účtu a prostředky, ke kterým mají přiřazený přístup. <br> Zvažte přidání prefixu svc_ do všech uživatelských účtů používaných jako účty služeb. |


## <a name="find-on-premises-user-accounts-used-as-service-accounts"></a>Vyhledání místních uživatelských účtů používaných jako účtů služeb

Místní uživatelské účty jsou stejné jako u jakéhokoli jiného uživatelského účtu služby AD. V důsledku toho může být obtížné tyto účty najít, protože není k dispozici žádný atribut uživatelského účtu, který ho identifikuje jako účet služby. 

Doporučujeme vytvořit snadno identifikovatelnou konvenci pojmenování pro libovolný uživatelský účet, který se používá jako účet služby.

Přidejte například "Service-" jako předponu a pojmenujte službu: "Service-HRDataConnector".

K vyhledání těchto účtů služeb můžete použít některé z těchto ukazatelů. tyto účty ale nemusí najít všechny takové účty.

* Účty důvěryhodné pro delegování

* Účty s názvy instančních objektů.

* Účty, jejichž heslo je nastavené na nikdy nevyprší platnost.

K vyhledání místních uživatelských účtů vytvořených pro služby můžete spustit následující příkazy PowerShellu.

### <a name="find-accounts-trusted-for-delegation"></a>Vyhledání účtů důvěryhodných pro delegování

```PowerShell

Get-ADObject -Filter {(msDS-AllowedToDelegateTo -like '*') -or (UserAccountControl -band 0x0080000) -or (UserAccountControl -band 0x1000000)} -prop samAccountName,msDS-AllowedToDelegateTo,servicePrincipalName,userAccountControl | select DistinguishedName,ObjectClass,samAccountName,servicePrincipalName, @{name='DelegationStatus';expression={if($_.UserAccountControl -band 0x80000){'AllServices'}else{'SpecificServices'}}}, @{name='AllowedProtocols';expression={if($_.UserAccountControl -band 0x1000000){'Any'}else{'Kerberos'}}}, @{name='DestinationServices';expression={$_.'msDS-AllowedToDelegateTo'}}

```

### <a name="find-accounts-with-service-principle-names"></a>Vyhledání účtů s názvy principů služby

```PowerShell

Get-ADUser -Filter * -Properties servicePrincipalName | where {$_.servicePrincipalName -ne $null}

```

 

### <a name="find-accounts-with-passwords-set-to-never-expire"></a>Vyhledá účty s hesly nastavenými na nikdy nevyprší platnost.

```PowerShell

Get-ADUser -Filter * -Properties PasswordNeverExpires | where {$_.PasswordNeverExpires -eq $true}

```


Můžete také Auditovat přístup k citlivým prostředkům a archivovat protokoly auditu do systému správy událostí a informací o zabezpečení (SIEM). Pomocí systémů, jako je Azure Log Analytics nebo Azure Sentinel, můžete vyhledávat a analyzovat účty a služby.

## <a name="assess-security-of-on-premises-user-accounts"></a>Posouzení zabezpečení místních uživatelských účtů

Vyhodnoťte zabezpečení místních uživatelských účtů používaných jako účty služeb pomocí následujících kritérií:

* Co jsou zásady správy hesel?

* Je účet členem všech privilegovaných skupin?

* Má účet přístup pro čtení a zápis k důležitým prostředkům?

### <a name="mitigate-potential-security-issues"></a>Zmírnění potenciálních problémů se zabezpečením

V následující tabulce jsou uvedeny možné problémy se zabezpečením a odpovídající zmírnění pro místní uživatelské účty.

| Problémy se zabezpečením| Omezení rizik |
| - | - |
| Správa hesel|* Zajistěte, aby se složitost hesla a změna hesla řídily robustním procesem, který zajišťuje pravidelné aktualizace se silnými požadavky na heslo. <br> * Koordinuje změnu hesla pomocí aktualizace hesla, aby se minimalizoval výpadek služeb. |
| Účet je členem privilegovaných skupin.| Kontrola členství ve skupinách. Odeberte účet z privilegovaných skupin. Udělte účtu jenom práva a oprávnění, která vyžaduje ke spuštění jeho služby (obraťte se na dodavatele služby). Můžete například povolit místní přihlášení nebo zamítnout interaktivní přihlašování. |
| Účet má přístup pro čtení a zápis citlivých prostředků.| Auditujte přístup k citlivým prostředkům. Archivujte protokoly auditu do SIEM (Azure Log Analytics nebo Azure Sentinel) pro účely analýzy. Napravit oprávnění prostředku, pokud je zjištěna nežádoucí úroveň přístupu. |


## <a name="move-to-more-secure-account-types"></a>Přesunout na bezpečnější typy účtů

Společnost Microsoft nedoporučuje, aby zákazníci používali místní uživatelské účty jako účty služeb. Pro jakoukoliv službu, která používá tento typ účtu, vyhodnoťte, jestli se dá nakonfigurovat tak, aby používala gMSA nebo sMSA.

Navíc můžete vyhodnotit, jestli se služba mohla přesunout do Azure, aby bylo možné používat bezpečnější typy účtů služby. 

## <a name="next-steps"></a>Další kroky
Přečtěte si následující články týkající se zabezpečení účtů služeb.

* [Seznámení s místními účty služeb](service-accounts-on-premises.md)

* [Zabezpečené skupiny účtů spravované služby](service-accounts-group-managed.md)

* [Zabezpečení samostatných účtů spravované služby](service-accounts-standalone-managed.md)

* [Zabezpečení účtů počítačů](service-accounts-computer.md)

* [Zabezpečení uživatelských účtů](service-accounts-user-on-premises.md)

* [Řízení účtů místních služeb](service-accounts-govern-on-premises.md)

 
