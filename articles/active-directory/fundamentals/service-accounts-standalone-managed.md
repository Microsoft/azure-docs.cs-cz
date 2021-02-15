---
title: Zabezpečení samostatných účtů spravované služby | Azure Active Directory
description: Průvodce zabezpečením samostatných účtů spravované služby.
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
ms.openlocfilehash: 08a919338789a02d50cbb6976ee50b214cb0d612
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417178"
---
# <a name="securing-standalone-managed-service-accounts"></a>Zabezpečení samostatných účtů spravované služby

Samostatné účty spravované služby (sMSAs) jsou spravované účty domény používané k zabezpečení jedné nebo více služeb spuštěných na serveru. Nelze je znovu použít na více serverech. sMSAs poskytují automatickou správu hesel, zjednodušenou správu hlavního názvu služby (SPN) a schopnost delegovat správu na jiné správce. 

Ve službě Active Directory jsou sMSAs vázané na konkrétní server, na kterém běží služba. Tyto účty najdete v modulu snap-in Uživatelé a počítače služby Active Directory konzoly Microsoft Management Console.

![Snímek obrazovky s modulem snap-in Uživatelé a počítače služby Active Directory zobrazující organizační jednotku s účty spravované služby.](./media/securing-service-accounts/secure-standalone-msa-image-1.png)

Účty spravované služby byly představeny se schématem služby Windows Server 2008 R2 Active Directory a vyžadují minimální úroveň operačního systému Windows Server 2008 R2. 

## <a name="benefits-of-using-smsas"></a>Výhody použití sMSAs

sMSAs nabízí lepší zabezpečení než uživatelské účty používané jako účty služeb a současně snižuje administrativní režii:

* Nastavení silných hesel. sMSAs používá náhodně generovaná složitá hesla 240 bajtů. Složitost a délka sMSA hesel minimalizuje pravděpodobnost, že se služba při útoku hrubou silou nebo slovníkovým útokům snižuje.

* Pravidelné zacyklování hesel Systém Windows automaticky změní heslo sMSA každých 30 dnů. Správci služeb a domén nepotřebují plánovat změny hesla ani spravovat související výpadky.

* Zjednodušení správy hlavního názvu služby (SPN). Hlavní názvy služby se aktualizují automaticky, pokud je úroveň funkčnosti domény (úrovni funkčnosti domény) Windows Server 2008 R2. Například hlavní název služby se automaticky aktualizuje v následujících scénářích:

   * Účet hostitelského počítače se přejmenuje. 

   * Název DNS hostitelského počítače se změní.

   * Přidání nebo odebrání dalších parametrů Sam-Account nebo DNS-hostname pomocí [prostředí PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-adserviceaccount?view=win10-ps)

## <a name="when-to-use-smsas"></a>Kdy použít sMSAs

sMSAs může zjednodušit úlohy správy a zabezpečení. SMSAs použijte, když máte jednu nebo více služeb nasazených na jeden server a nemůžete použít gMSA. 

> [!NOTE] 
> I když můžete sMSAs použít pro více než jednu službu, doporučujeme, aby každá služba měla svou vlastní identitu pro účely auditování. 

Pokud tvůrce softwaru nemůže sdělit, jestli může používat MSA, musíte otestovat svoji aplikaci. Pokud to chcete udělat, vytvořte testovací prostředí a ujistěte se, že má přístup ke všem požadovaným prostředkům. Podrobné pokyny najdete v tématu [Vytvoření a instalace SMSA](https://docs.microsoft.com/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting) .

### <a name="assess-security-posture-of-smsas"></a>Posouzení stav zabezpečení sMSAs

sMSAs jsou z vlastního podstaty bezpečnější než standardní uživatelské účty, které vyžadují průběžnou správu hesel. Je ale důležité vzít v úvahu rozsah přístupu sMSAs jako součást celkového stav zabezpečení.

Následující tabulka ukazuje, jak zmírnit potenciální problémy se zabezpečením, které představují sMSAs.

| Problémy se zabezpečením| Omezení rizik |
| - | - |
| sMSA je členem privilegovaných skupin.|Odeberte sMSA ze zvýšených privilegovaných skupin (například Domain Admins). <br> Použijte nejméně privilegovaný model a udělte sMSA jenom práva a oprávnění, která vyžaduje ke spuštění jeho služeb. <br> Pokud si nejste jisti potřebnými oprávněními, obraťte se na tvůrce služby. |
| sMSA má přístup pro čtení i zápis citlivých prostředků.|Auditujte přístup k citlivým prostředkům. Archivujte protokoly auditu do SIEM (Azure Log Analytics nebo Azure Sentinel) pro účely analýzy. <br> Napravit oprávnění prostředku, pokud je zjištěna nežádoucí úroveň přístupu. |
| Ve výchozím nastavení je frekvence změny hesla sMSA 30 dní.| Zásady skupiny se dají použít k vyladění doby trvání v závislosti na podnikových požadavcích na zabezpečení. <br> * Nastavení trvání vypršení platnosti hesla můžete nastavit pomocí následující cesty. <br>Počítač cestě konfigurace heslo \ Options\Domain pro členství v počítačích: maximální stáří hesla účtu počítače |



### <a name="challenges-with-smsas"></a>Problémy s sMSAs

Výzvy spojené s sMSAs jsou následující:

| Výzvy| Omezení rizik |
| - | - |
| Dají se použít na jednom serveru.| Účty gMSA použijte, pokud potřebujete účet použít na všech serverech. |
| Nelze je použít napříč doménami.| Účty gMSA použijte, pokud potřebujete účet použít napříč doménami. |
| Ne všechny aplikace podporují sMSAs.| Pokud je to možné, použijte účty gMSA. Pokud nepoužíváte standardní uživatelský účet nebo účet počítače doporučený pro Tvůrce aplikace. |


## <a name="find-smsas"></a>Najít sMSAs

Na jakémkoli řadiči domény spusťte DSA. msc a rozbalte kontejner spravované služby účty, abyste zobrazili všechny sMSAs. 

Následující příkaz prostředí PowerShell vrátí všechny sMSAs a účty gMSA v doméně služby Active Directory. 

`Get-ADServiceAccount -Filter *`

Následující příkaz vrátí pouze sMSAs v doméně služby Active Directory.

`Get-ADServiceAccount -Filter * | where { $_.objectClass -eq "msDS-ManagedServiceAccount" }`

## <a name="manage-smsas"></a>Správa sMSAs

Pro správu sMSAs můžete použít následující rutiny PowerShellu služby Active Directory:

`Get-ADServiceAccount`

` Install-ADServiceAccount`

` New-ADServiceAccount`

` Remove-ADServiceAccount`

`Set-ADServiceAccount`

`Test-ADServiceAccount`

`Ininstall-ADServiceAccount`

## <a name="move-to-smsas"></a>Přesunout na sMSAs

Pokud Aplikační služba podporuje sMSA, ale ne účty gMSA a aktuálně používá uživatelský účet nebo účet počítače pro kontext zabezpečení, [vytvořte a nainstalujte SMSA](https://docs.microsoft.com/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting) na serveru. 

V ideálním případě přesuňte prostředky do Azure a použijte spravované identity nebo instanční objekty Azure.

 

## <a name="next-steps"></a>Další kroky
Přečtěte si následující články týkající se zabezpečení účtů služeb.

* [Seznámení s místními účty služeb](service-accounts-on-premises.md)

* [Zabezpečené skupiny účtů spravované služby](service-accounts-group-managed.md)

* [Zabezpečení samostatných účtů spravované služby](service-accounts-standalone-managed.md)

* [Zabezpečení účtů počítačů](service-accounts-computer.md)

* [Zabezpečení uživatelských účtů](service-accounts-user-on-premises.md)

* [Řízení účtů místních služeb](service-accounts-govern-on-premises.md)

 
