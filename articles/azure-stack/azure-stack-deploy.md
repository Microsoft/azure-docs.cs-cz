---
title: "Požadavky nasazení Azure zásobníku Development Kit | Microsoft Docs"
description: "Zobrazte prostředí a hardwarové požadavky pro Azure zásobníku Development Kit (operátor cloudu)."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 32a21d9b-ee42-417d-8e54-98a7f90f7311
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/02/2018
ms.author: jeffgilb
ms.openlocfilehash: 03682e7f5867360cc3d854f0dff2bc5d1c1c9619
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
# <a name="azure-stack-deployment-prerequisites"></a>Požadavky nasazení Azure Stack

*Platí pro: Azure zásobníku Development Kit*

Před nasazením [Azure zásobníku Development Kit (ASDK)](azure-stack-poc.md), ujistěte se, že hostitelského počítače development kit splňuje požadavky popsané v tomto článku.

> [!NOTE]
> ASDK musí být nainstalován na fyzickém hostitelském počítači. Instalace ASDK ve virtuálním počítači Azure není podporována.

## <a name="hardware"></a>Hardware
| Komponenta | Minimální | Doporučené |
| --- | --- | --- |
| Diskové jednotky: Operační systém |Jeden disk s operačním systémem a alespoň 200 GB místa pro systémový oddíl (SSD nebo pevný disk) |Jeden disk s operačním systémem a alespoň 200 GB místa pro systémový oddíl (SSD nebo pevný disk) |
| Diskové jednotky: Obecné development kit dat * |Čtyři disky. Každý disk nabízí minimálně 140 GB místa (SSD nebo pevný disk). Použijí se všechny dostupné disky. |Čtyři disky. Každý disk nabízí minimálně 250 GB místa (SSD nebo pevný disk). Použijí se všechny dostupné disky. |
| Výpočetní služby: Procesor |Duální soket: 12 fyzických jader (celkem) |Duální soket: 16 fyzických jader (celkem) |
| Výpočetní služby: Paměť |96 GB RAM |128 GB paměti RAM (to je minimální pro podporu zprostředkovatele prostředků PaaS.)|
| Výpočetní služby: BIOS |Povolená technologie Hyper-V (s podporou SLAT) |Povolená technologie Hyper-V (s podporou SLAT) |
| Síť: NIC |V případě NIC se vyžaduje certifikace Windows Serveru 2012 R2. Specializované funkce se nepožadují |V případě NIC se vyžaduje certifikace Windows Serveru 2012 R2. Specializované funkce se nepožadují |
| Hardwarová certifikace loga |[Certifikované pro systém Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[Certifikované pro systém Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |

\*Budete potřebovat větší, než to doporučeno kapacity, pokud chcete použít k přidání řadu [položky marketplace](azure-stack-download-azure-marketplace-item.md) z Azure.

**Konfigurace disku dat:** všechny datové jednotky musí být stejného typu (všechny SAS, všechny SATA nebo všechny NVMe) a výkonu. Pokud použijete disky SAS, musí být diskové jednotky připojené pomocí jedné cesty (žádné funkce MPIO, podpora více cest je zajištěna).

**Možnosti konfigurace hostitelského adaptéru**

* (Upřednostňované) Jednoduchý adaptér HBA
* RAID HBA – adaptér musí být konfigurovaný v režimu průchodu
* RAID HBA – disky musí být konfigurované jako jeden disk, RAID-0

**Podporované sběrnice a média zadejte kombinace**

* Pevný disk SATA
* Pevný disk SAS
* Pevný disk RAID
* RAID SSD (pokud je typ média neurčeno/neznámé\*)
* SATA SSD + pevný disk SATA
* SAS SSD + pevný disk SAS
* NVMe

\* Řadičů RAID bez průchozí schopnosti nemůže rozpoznat typ média. Tyto řadiče označí pevný disk i SSD jako Neurčeno. V takovém případě se místo mezipaměťových zařízení použije SSD jako trvalé úložiště. Proto můžete nasadit development kit na těchto jednotkách SSD.

**Příklad HBA**: LSI 9207-8i, LSI-9300-8i nebo LSI-9265-8i v režimu průchodu

Dostupné jsou ukázkové OEM konfigurace.

## <a name="operating-system"></a>Operační systém
|  | **Požadavky** |
| --- | --- |
| **Verze operačního systému** |Windows Server 2012 R2 nebo novější. Verze operačního systému není důležité před zahájením nasazení, jak budete restartovat hostitelský počítač do virtuální pevný disk, který je součástí instalace Azure zásobníku. Všechny požadované opravy a operačního systému jsou již integrované do bitové kopie. Nepoužívejte žádné klíče k aktivaci žádné instance systému Windows Server používá v sadě pro vývoj. |

## <a name="deployment-requirements-check-tool"></a>Zkontrolujte požadavky na nasazení nástroje
Po instalaci operačního systému, můžete použít [kontrolu nasazení pro Azure zásobníku](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) potvrďte, že váš hardware splňuje všechny požadavky.

## <a name="account-requirements"></a>Požadavky na účet
Zpravidla nasazujete, sadě pro vývoj s připojením k Internetu, kde se můžete připojit k Microsoft Azure. V takovém případě musíte nakonfigurovat účet služby Azure Active Directory (Azure AD) k nasazení development kit.

Pokud vaše prostředí není připojený k Internetu nebo nechcete používat Azure AD, můžete nasadit zásobník Azure pomocí služby Active Directory Federation Services (AD FS). Vývoj sada obsahuje vlastní instancí služby AD FS a Active Directory Domain Services. Pokud nasadíte pomocí této možnosti, nemáte vytváření účtů předem.

>[!NOTE]
Pokud nasadíte pomocí možnosti služby AD FS, je nutné znovu nasadit zásobník Azure přejděte do služby Azure AD.

### <a name="azure-active-directory-accounts"></a>Účty Azure Active Directory
Nasazení zásobník Azure pomocí účtu Azure AD, je nutné připravit účet Azure AD, před spuštěním nasazení skript prostředí PowerShell. Tento účet se změní na globálního správce pro tenanta Azure AD. Použije se pro zajišťování a delegovat aplikací a objekty služby pro všechny služby zásobník Azure, které se s Azure Active Directory a rozhraní Graph API. Používá se také jako vlastník předplatného výchozí poskytovatele (který můžete později změnit). Na portálu správce vašeho systému Azure zásobníku se může přihlásit pomocí tohoto účtu.

1. Vytvořte účet Azure AD, který je správcem adresáře pro alespoň jeden Azure AD. Pokud už účet máte, můžete ho použít. Jinak, můžete jej vytvořit zdarma v [http://azure.microsoft.com/en-us/pricing/free-trial/](http://azure.microsoft.com/pricing/free-trial/) (v Číně, navštivte <http://go.microsoft.com/fwlink/?LinkID=717821> místo). Pokud budete chtít později [zaregistrovat zásobník Azure s Azure](azure-stack-register.md), je také nutné mít předplatné v tomto nově vytvořený účet.
   
    Uložit tyto přihlašovací údaje pro použití v kroku 6 v [nasazení development kit](azure-stack-run-powershell-script.md). Tento účet *správce služby* může konfigurovat a spravovat cloudy prostředků, uživatelské účty, plány tenantů, kvóty a ceny. Na portálu může vytvářet webové cloudy, soukromé cloudy pro virtuální počítače a plány a spravovat předplatné uživatelů.
2. [Vytvoření](azure-stack-add-new-user-aad.md) alespoň jeden účet tak, aby se můžete přihlásit development Kit jako klient.
   
   | **Účet Azure Active Directory** | **Podporovány?** |
   | --- | --- |
   | Pracovní nebo školní účet s platným předplatným veřejný Azure |Ano |
   | Účet Microsoft s platným veřejným předplatným Azure |Ano |
   | Pracovní nebo školní účet s platným předplatným Azure Čína |Ano |
   | Pracovní nebo školní účet s platným US Government Azure předplatným |Ano |

## <a name="network"></a>Síť
### <a name="switch"></a>Přepínač
Jeden dostupný port na přepínači pro počítač development kit.  

Vývojovém počítači kit podporuje připojení k přístupu k portu přepínače nebo portu trunk. Přepínač nevyžaduje žádné specializované funkce. Pokud používáte kmenový port nebo pokud potřebujete nakonfigurovat ID sítě VLAN, musíte ID sítě VLAN zadat jako parametr nasazení. Příklady v uvidíte [seznam parametrů nasazení](azure-stack-run-powershell-script.md).

### <a name="subnet"></a>Podsíť
Nespojujte vývojovém počítači kit následující podsítě:

* 192.168.200.0/24
* 192.168.100.0/27
* 192.168.101.0/26
* 192.168.102.0/24
* 192.168.103.0/25
* 192.168.104.0/25

Tyto podsítě jsou vyhrazené pro interní sítě ve vývojovém prostředí sady.

### <a name="ipv4ipv6"></a>IPv4/IPv6
Podporovaný je jenom protokol IPv4. Nemůžete vytvořit sítě IPv6.

### <a name="dhcp"></a>DHCP
Ověřte si dostupnost serveru DHCP v síti, do které se síťová karta připojuje. Pokud server DHCP není dostupný, připravte další statickou síť IPv4 (kromě té, kterou používá hostitel). Tuto IP adresu a bránu zadejte jako parametr nasazení. Příklady v uvidíte [seznam parametrů nasazení](azure-stack-run-powershell-script.md).

### <a name="internet-access"></a>Přístup k internetu
Zásobník Azure vyžaduje přístup k Internetu, buď přímo nebo prostřednictvím proxy serveru transparentní. Azure zásobník nepodporuje konfiguraci webového proxy serveru pro povolení přístupu k Internetu. IP adresa hostitele a nových IP přiřazená MAS-BGPNAT01 (pomocí protokolu DHCP nebo statickou IP adresu) musí být mít přístup k Internetu. V rámci domény graph.windows.net a login.microsoftonline.com používají porty 80 a 443.


## <a name="next-steps"></a>Další postup
[Stažení balíčku pro nasazení Azure zásobníku development kit](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

[Nasazení zásobník Azure development kit](azure-stack-run-powershell-script.md)
