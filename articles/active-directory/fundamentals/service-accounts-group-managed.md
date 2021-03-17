---
title: Zabezpečení skupinových účtů spravované služby | Azure Active Directory
description: Průvodce zabezpečením skupinových účtů spravované služby skupiny účtů počítačů.
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
ms.openlocfilehash: bd4c1adddbf4b13f8e299bd656443c9aaab1d55b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644823"
---
# <a name="securing-group-managed-service-accounts"></a>Zabezpečení skupinových účtů spravované služby

Skupinové účty spravované služby (účty gMSA) jsou spravované účty domény, které se používají k zabezpečení služeb. Účty gMSA lze spustit na jednom serveru nebo v serverové farmě, jako jsou například systémy za sítí Load Balancer (NLB) nebo server Internetová informační služba (IIS). Jakmile nakonfigurujete služby tak, aby používaly instanční objekt gMSA, bude Správa hesel pro tento účet zpracována systémem Windows.

## <a name="benefits-of-using-gmsas"></a>Výhody použití účty gMSA

Účty gMSA nabízí řešení jediné identity s vyšším zabezpečením a současně snižuje administrativní režii:

* **Nastavení silných hesel**. Účty gMSA používá náhodně generovaná složitá hesla 240 bajtů. Složitost a délka gMSA hesel minimalizuje pravděpodobnost, že se služba při útoku hrubou silou nebo slovníkovým útokům snižuje.

* **Pravidelné zacyklování hesel** Účty gMSA Shift – Správa hesel na Windows, která mění heslo každých 30 dní. Správci služeb a domén už nemusí plánovat změny hesel ani spravovat výpadky služby, aby účty služeb byly zabezpečené. 

* **Podpora nasazení na serverové farmy**. Možnost nasazení účty gMSA na víc serverů umožňuje podporu řešení s vyrovnáváním zatížení, kde víc hostitelů spouští stejnou službu. 

* **Podpora zjednodušeného řízení hlavního názvu serveru (SPN)**. V době vytváření účtu můžete nastavit hlavní název služby (SPN) pomocí prostředí PowerShell. Služby, které podporují automatické registrace hlavního názvu služby (SPN), můžou navíc dělat v porovnání s gMSAem, poskytnuté gMSA oprávnění jsou správně nastavená. 

## <a name="when-to-use-gmsas"></a>Kdy použít účty gMSA

Použijte účty gMSA jako preferovaný typ účtu pro místní služby, pokud nepodporuje služba, jako je Clustering s podporou převzetí služeb při selhání.

> [!IMPORTANT]
> Před nasazením do produkčního prostředí musíte službu otestovat pomocí účty gMSA. Provedete to tak, že nastavíte testovací prostředí a zajistěte, aby aplikace mohla používat gMSA, a přístup k prostředkům, ke kterým potřebuje přístup. Další informace najdete v tématu [Podpora skupinových účtů spravované služby](/system-center/scom/support-group-managed-service-accounts?view=sc-om-2019).


Pokud služba nepodporuje použití služby účty gMSA, vaše další nejlepší možností je použít samostatný účet spravované služby (sMSA). sMSAs poskytují stejné funkce jako gMSA, ale jsou určené pro nasazení pouze na jednom serveru.

Pokud vaše služba nepodporuje gMSA nebo sMSA, musí být služba nakonfigurovaná tak, aby běžela jako standardní uživatelský účet. Správci služeb a domén jsou povinni sledovat procesy silné správy hesel, aby se zajistila bezpečnost účtu.

## <a name="assess-the-security-posture-of-gmsas"></a>Posouzení stav zabezpečení účty gMSA

Účty gMSA jsou z vlastního podstaty bezpečnější než standardní uživatelské účty, které vyžadují průběžnou správu hesel. Je ale důležité vzít v úvahu účty gMSA "rozsah přístupu", když se podíváte na celkové stav zabezpečení.

V následující tabulce jsou uvedeny potenciální problémy se zabezpečením a omezení pro používání účty gMSA.

| Problémy se zabezpečením| Omezení rizik |
| - | - |
| gMSA je členem privilegovaných skupin. | Zkontrolujte členství ve skupině. K tomu můžete vytvořit PowerShellový skript, který vytvoří výčet všech členství ve skupině, a pak vyfiltruje výsledný soubor CSV podle názvů vašich souborů gMSA. <br>Odeberte gMSA z privilegovaných skupin.<br> Udělte gMSA jenom práva a oprávnění, která vyžaduje ke spuštění své služby (obraťte se na dodavatele služby). 
| gMSA má přístup pro čtení i zápis citlivých prostředků. | Auditujte přístup k citlivým prostředkům. Archivujte protokoly auditu do SIEM, například Azure Log Analytics nebo Azure Sentinel, pro účely analýzy. Odstraňte nepotřebná oprávnění prostředku, pokud je zjištěna nežádoucí úroveň přístupu. |


## <a name="find-gmsas"></a>Najít účty gMSA

Vaše organizace už možná vytvořila účty gMSA. K načtení těchto účtů spusťte následující rutinu prostředí PowerShell:

Aby bylo možné efektivně pracovat, musí být účty gMSA v organizační jednotce (OU) účty spravované služby.

  
![Snímek obrazovky organizační jednotky účtu spravované služby](./media/securing-service-accounts/secure-gmsa-image-1.png)

Pokud chcete najít účty spravované služby služeb, které se nemusí nacházet, přečtěte si následující příkazy.

**Vyhledání všech účtů služeb, včetně účty gMSA a sMSAs:**


```powershell

Get-ADServiceAccount -Filter *

# This PowerShell cmdlet will return all Managed Service Accounts (both gMSAs and sMSAs). An administrator can differentiate between the two by examining the ObjectClass attribute on returned accounts.

# For gMSA accounts, ObjectClass = msDS-GroupManagedServiceAccount

# For sMSA accounts, ObjectClass = msDS-ManagedServiceAccount

# To filter results to only gMSAs:

Get-ADServiceAccount –Filter * | where $_.ObjectClass -eq "msDS-GroupManagedServiceAccount”}
```

## <a name="manage-gmsas"></a>Správa účty gMSA

Pro správu účty gMSA můžete použít následující rutiny PowerShellu služby Active Directory:

`Get-ADServiceAccount`

`Install-ADServiceAccount`

`New-ADServiceAccount`

`Remove-ADServiceAccount`

`Set-ADServiceAccount`

`Test-ADServiceAccount`

`Uninstall-ADServiceAccount`

> [!NOTE]
> Počínaje systémem Windows Server 2012 budou rutiny *-ADServiceAccount ve výchozím nastavení spolupracovat s účty gMSA. Další informace o použití výše uvedených rutin najdete v tématu [**Začínáme se skupinovými účty spravované služby**](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts).

## <a name="move-to-a-gmsa"></a>Přesunout na gMSA
Účty gMSA jsou nejbezpečnější typ účtu služby pro místní potřeby. Pokud se můžete přesunout k jednomu, měli byste. Kromě toho zvažte přesunutí služeb do Azure a účtů služeb do služby Azure Active Directory.

1.  Zajistěte, aby byl [v doménové struktuře nasazený kořenový klíč KDS](/windows-server/security/group-managed-service-accounts/create-the-key-distribution-services-kds-root-key). Jedná se o jednorázovou operaci.

2. [Vytvořte nový gMSA](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts).

3. Nainstalujte nový gMSA na každého hostitele, na kterém je spuštěná služba.
   > [!NOTE] 
   > Další informace o vytváření a instalaci gMSA na hostiteli před konfigurací služby tak, aby používala gMSA, najdete v tématu [Začínáme se skupinovými účty spravované služby](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11)) .

 
4. Změňte identitu služby na gMSA a zadejte prázdné heslo.

5. Ověřte, že vaše služba funguje pod novou identitou gMSA.

6. Odstraňte starou identitu účtu služby.

 

## <a name="next-steps"></a>Další kroky
Přečtěte si následující články týkající se zabezpečení účtů služeb.

* [Seznámení s místními účty služeb](service-accounts-on-premises.md)

* [Zabezpečené skupiny účtů spravované služby](service-accounts-group-managed.md)

* [Zabezpečení samostatných účtů spravované služby](service-accounts-standalone-managed.md)

* [Zabezpečení účtů počítačů](service-accounts-computer.md)

* [Zabezpečení uživatelských účtů](service-accounts-user-on-premises.md)

* [Řízení účtů místních služeb](service-accounts-govern-on-premises.md)