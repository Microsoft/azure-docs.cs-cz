---
title: Seznámení s účty služby Active Directory | Azure Active Directory
description: Úvod k typům účtů služeb ve službě Active Directory a o tom, jak je zabezpečit.
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
ms.openlocfilehash: a9024bc9fbd460f403db2da8a65af1e9bd2e771b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645612"
---
# <a name="introduction-to-active-directory-service-accounts"></a>Seznámení s účty služby Active Directory

Služba má primární identitu zabezpečení, která určuje přístupová práva pro místní a síťové prostředky. Kontext zabezpečení pro službu Microsoft Win32 je určený účtem služby, který se používá ke spuštění služby. Účet služby se používá pro:
* identifikace a ověření služby
* Služba se úspěšně spustila.
* přístup nebo spuštění kódu nebo aplikace
* zahajte proces. 

## <a name="types-of-on-premises-service-accounts"></a>Typy místních účtů služeb

Na základě vašeho případu použití můžete použít účet spravované služby (MSA), počítačový účet nebo uživatelský účet ke spuštění služby. Služby musí být testovány, aby bylo možné potvrdit, že mohou používat účet spravované služby. Pokud je to možné, měli byste použít jeden.

### <a name="group-msa-accounts"></a>Skupinové účty MSA

[Skupinové účty spravované služby](service-accounts-group-managed.md) (účty gMSA) používejte, kdykoli je to možné pro služby běžící v místním prostředí. Účty gMSA poskytují řešení s jedinou identitou pro službu spuštěnou v serverové farmě nebo za nástroj pro vyrovnávání zatížení sítě. Lze je také použít pro službu běžící na jednom serveru. [Účty gMSA mají specifické požadavky, které je třeba splnit.](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)

### <a name="standalone-msa-accounts"></a>Samostatné účty MSA

Pokud nemůžete použít gMSA, použijte [samostatné účty spravované služby](service-accounts-standalone-managed.md)(SMSA). sMSAs vyžaduje minimálně Windows Server 2008 R2. Na rozdíl od účty gMSA se sMSAs spouští jenom na jednom serveru. Je možné je použít pro více služeb na tomto serveru.

### <a name="computer-account"></a>Účet počítače

Pokud nemůžete použít MSA, prozkoumejte je pomocí [účtů počítačů](service-accounts-computer.md). Účet LocalSystem je předdefinovaný místní účet, který má rozsáhlá oprávnění v místním počítači a funguje jako identita počítače v síti.   
Služby, které jsou spouštěny jako síťový prostředek přístup k účtu LocalSystem pomocí pověření účtu počítače ve formátu <domain_name>\<computer_name> .

NT AUTHORITY\SYSTEM je předdefinovaný název účtu LocalSystem. Dá se použít ke spuštění služby a k poskytnutí kontextu zabezpečení pro tuto službu.

> [!NOTE]
> Při použití účtu počítače nemůžete určit, která služba v počítači používá tento účet, a proto nemůže auditovat, které služby provádějí změny. 

### <a name="user-account"></a>Uživatelský účet

Pokud nemůžete použít MSA, prozkoumejte je pomocí [uživatelských účtů](service-accounts-user-on-premises.md). Uživatelské účty můžou být doménový uživatelský účet nebo účet místního uživatele.

Účet uživatele domény umožňuje službě plně využít funkce zabezpečení služby Windows a Microsoft Active Directory Domain Services. Služba bude mít k účtu udělený místní a síťový přístup. Bude mít taky oprávnění pro všechny skupiny, jejichž členem je účet. Účty doménové služby podporují vzájemné ověřování pomocí protokolu Kerberos.

Místní uživatelský účet (formát názvu: ".\UserName") existuje pouze v databázi SAM hostitelského počítače; neobsahuje objekt uživatele v Active Directory Domain Services. Místní účet nemůže doména ověřit. Proto služba, která běží v kontextu zabezpečení místního uživatelského účtu, nemá přístup k síťovým prostředkům (kromě anonymního uživatele). Služby spuštěné v kontextu místního uživatele nepodporují vzájemné ověřování pomocí protokolu Kerberos, ve kterém se služba ověřuje pomocí klientů. Z těchto důvodů jsou pro služby s povolenými adresáři obvykle nevhodné místní uživatelské účty.

> [!IMPORTANT]
> Účty služby by neměly být členy žádné privilegované skupiny, protože privilegované členství ve skupině uděluje oprávnění, která mohou představovat bezpečnostní riziko. Každá služba by měla mít vlastní účet služby pro účely auditování a zabezpečení.

## <a name="choose-the-right-type-of-service-account"></a>Volba správného typu účtu služby


| Kritéria| gMSA| sMSA| Účet počítače| Uživatelský účet |
| - | - | - | - | - |
| Aplikace běží na jednom serveru.| Ano| Ano. Pokud je to možné, použijte gMSA| Ano. Pokud je to možné, použijte MSA| Ano. Pokud je to možné, použijte MSA. |
| Aplikace běží na více serverech| Yes| No| No. Účet je svázán se serverem| Ano. Pokud je to možné, použijte MSA. |
| Spuštění aplikace za nástroji pro vyrovnávání zatížení| Yes| No| No| Yes. Použijte jenom v případě, že nemůžete použít gMSA. |
| Aplikace běží na Windows serveru 2008 R2.| No| Ano| Ano. Pokud je to možné, použijte MSA.| Ano. Pokud je to možné, použijte MSA. |
| Běží na Windows Serveru 2012.| Ano| Ano. Pokud je to možné, použijte gMSA| Ano. Pokud je to možné, použijte MSA| Ano. Pokud je to možné, použijte MSA. |
| Požadavek na omezení účtu služby na jeden server| No| Ano| Ano. Pokud je to možné, použijte sMSA| No. |


 

### <a name="use-server-logs-and-powershell-to-investigate"></a>Použití protokolů serveru a PowerShellu k prozkoumání

Pomocí protokolu serveru můžete určit, které servery a kolik serverů aplikace běží.

Spuštěním následujícího příkazu PowerShellu můžete získat seznam verzí Windows serveru pro všechny servery v síti. 

```PowerShell

Get-ADComputer -Filter 'operatingsystem -like "*server*" -and enabled -eq "true"' `

-Properties Name,Operatingsystem,OperatingSystemVersion,IPv4Address |

sort-Object -Property Operatingsystem |

Select-Object -Property Name,Operatingsystem,OperatingSystemVersion,IPv4Address |

Out-GridView

```

## <a name="find-on-premises-service-accounts"></a>Najít účty místních služeb

Doporučujeme přidat předponu, například "SVC". Na všechny účty používané jako účty služeb. Tato konvence pojmenování usnadňuje hledání a správu. Zvažte také použití atributu Description pro účet služby a vlastníka účtu služby, může se jednat o alias týmu nebo vlastníka týmu zabezpečení.

Hledání místních účtů služby je klíčem k zajištění jejich zabezpečení. A můžou být obtížné pro účty, které nejsou MSA. Doporučujeme zkontrolovat všechny účty, které mají přístup k důležitým místním prostředkům, a určit, které počítače nebo uživatelské účty můžou vystupovat jako účty služeb. K vyhledání účtů můžete také použít následující metody.

* Články pro každý typ účtu obsahují podrobné pokyny pro vyhledání tohoto typu účtu. Odkazy na tyto články najdete v části Další kroky v tomto článku.

## <a name="document-service-accounts"></a>Účty služby dokumentů

Jakmile najdete účty služeb v místním prostředí, zdokumentujte si následující informace o každém účtu. 

* Vlastník. Osoba, která je k údržbě účtu oprávněná.

* Účel. Aplikace, kterou účet představuje, nebo jiné účely. 

* Rozsahy oprávnění Jaká oprávnění má a jak mají? Co když jsou některé skupiny členem?

* Profil rizika. Jaké je riziko pro vaši firmu v případě ohrožení bezpečnosti tohoto účtu? Pokud je vysoké riziko, použijte MSA.

* Očekávaná životnost a pravidelná ověření identity. Jak dlouho předpokládáte, že tento účet je živý? Jak často musí vlastník kontrolovat a potvrzovat, aby bylo možné průběžně potřebovat?

* Zabezpečení hesla. Pro účty uživatelů a místních počítačů, kde je uloženo heslo. Ujistěte se, že jsou hesla udržována v bezpečí, a dokument, který má přístup. Zvažte použití [Privileged Identity Management](../privileged-identity-management/pim-configure.md) k zabezpečení uložených hesel. 

  

## <a name="next-steps"></a>Další kroky

Přečtěte si následující články týkající se zabezpečení účtů služeb.

* [Seznámení s místními účty služeb](service-accounts-on-premises.md)

* [Zabezpečené skupiny účtů spravované služby](service-accounts-group-managed.md)

* [Zabezpečení samostatných účtů spravované služby](service-accounts-standalone-managed.md)

* [Zabezpečení účtů počítačů](service-accounts-computer.md)

* [Zabezpečení uživatelských účtů](service-accounts-user-on-premises.md)

* [Řízení účtů místních služeb](service-accounts-govern-on-premises.md)

