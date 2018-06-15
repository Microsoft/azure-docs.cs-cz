---
title: Požadavky Azure zásobníku Development Kit nasazení (ASDK) | Microsoft Docs
description: Zkontrolujte požadavky na prostředí a hardware pro Azure zásobníku Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: f4b55bb3287f67792b3257c3f62256437f5625ca
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
ms.locfileid: "30164963"
---
# <a name="azure-stack-deployment-planning-considerations"></a>Plánování nasazení Azure zásobníku
Před nasazením Azure zásobníku Development Kit (ASDK), ujistěte se, že vaše development kit hostitelský počítač splňuje požadavky popsané v tomto článku.


## <a name="hardware"></a>Hardware
| Komponenta | Minimální | Doporučené |
| --- | --- | --- |
| Diskové jednotky: Operační systém |Jeden disk s operačním systémem a alespoň 200 GB místa pro systémový oddíl (SSD nebo pevný disk) |Jeden disk s operačním systémem a alespoň 200 GB místa pro systémový oddíl (SSD nebo pevný disk) |
| Diskové jednotky: Obecné development kit dat<sup>*</sup>  |Čtyři disky. Každý disk nabízí minimálně 140 GB místa (SSD nebo pevný disk). Všechny dostupné disky se používají. |Čtyři disky. Každý disk nabízí minimálně 250 GB místa (SSD nebo pevný disk). Všechny dostupné disky se používají. |
| Výpočetní služby: Procesor |Duální soket: 12 fyzických jader (celkem) |Duální soket: 16 fyzických jader (celkem) |
| Výpočetní služby: Paměť |96 GB RAM |128 GB paměti RAM (to je minimální pro podporu zprostředkovatele prostředků PaaS.)|
| Výpočetní služby: BIOS |Povolená technologie Hyper-V (s podporou SLAT) |Povolená technologie Hyper-V (s podporou SLAT) |
| Síť: NIC |V případě NIC se vyžaduje certifikace Windows Serveru 2012 R2. Specializované funkce se nepožadují |V případě NIC se vyžaduje certifikace Windows Serveru 2012 R2. Specializované funkce se nepožadují |
| Hardwarová certifikace loga |[Certifikované pro systém Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[Certifikované pro systém Windows Server 2016](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |

<sup>*</sup> Budete potřebovat větší, než to doporučeno kapacity, pokud chcete použít k přidání řadu [položky marketplace](asdk-marketplace-item.md) z Azure.

**Konfigurace disku dat:** všechny datové jednotky musí být stejného typu (všechny SAS, všechny SATA nebo všechny NVMe) a výkonu. Pokud použijete disky SAS, musí být diskové jednotky připojené pomocí jedné cesty (žádné funkce MPIO, podpora více cest je zajištěna).

**Možnosti konfigurace hostitelského adaptéru**

* (Upřednostňované) Jednoduchý adaptér HBA
* RAID HBA – adaptér musí být konfigurovaný v režimu průchodu
* RAID HBA – disky musí být konfigurované jako jeden disk, RAID-0

**Podporované sběrnice a média zadejte kombinace**

* Pevný disk SATA
* Pevný disk SAS
* Pevný disk RAID
* SSD diskového pole RAID (Pokud je tento parametr/Neznámý typ média<sup>*</sup>)
* SATA SSD + pevný disk SATA
* SAS SSD + pevný disk SAS
* NVMe

<sup>*</sup> Řadičů RAID bez průchozí schopnosti nemůže rozpoznat typ média. Tyto řadiče označit HDD a SSD Neurčeno. V takovém případě SSD slouží jako trvalé úložiště místo ukládání do mezipaměti v zařízení. Proto můžete nasadit development kit na těchto jednotkách SSD.

**Příklad HBA**: LSI 9207-8i, LSI-9300-8i nebo LSI-9265-8i v režimu průchodu

Dostupné jsou ukázkové OEM konfigurace.

## <a name="operating-system"></a>Operační systém
|  | **Požadavky** |
| --- | --- |
| **Verze operačního systému** |Windows Server 2012 R2 nebo novější. Verze operačního systému není důležité před zahájením nasazení, jak budete restartovat hostitelský počítač do virtuální pevný disk, který je součástí instalace Azure zásobníku. Všechny požadované opravy a operačního systému jsou již integrované do bitové kopie. Nepoužívejte žádné klíče k aktivaci žádné instance systému Windows Server používá v sadě pro vývoj. |

> [!TIP]
> Po instalaci operačního systému, můžete použít [kontrolu nasazení pro Azure zásobníku](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) potvrďte, že váš hardware splňuje všechny požadavky.

## <a name="account-requirements"></a>Požadavky na účet
Zpravidla nasazujete, sadě pro vývoj s připojením k Internetu, kde se můžete připojit k Microsoft Azure. V takovém případě musíte nakonfigurovat účet služby Azure Active Directory (Azure AD) k nasazení development kit.

Pokud vaše prostředí není připojený k Internetu nebo nechcete používat Azure AD, můžete nasadit zásobník Azure pomocí služby Active Directory Federation Services (AD FS). Vývoj sada obsahuje vlastní instancí služby AD FS a Active Directory Domain Services. Pokud nasadíte pomocí této možnosti, nemáte vytváření účtů předem.

>[!NOTE]
Pokud nasadíte pomocí možnosti služby AD FS, je nutné znovu nasadit zásobník Azure přejděte do služby Azure AD.

### <a name="azure-active-directory-accounts"></a>Účty Azure Active Directory
Nasazení zásobník Azure pomocí účtu Azure AD, je nutné připravit účet Azure AD, před spuštěním nasazení skript prostředí PowerShell. Tento účet se změní na globálního správce pro tenanta Azure AD. Použije se pro zajišťování a delegovat aplikací a objekty služby pro všechny služby zásobník Azure, které se s Azure Active Directory a rozhraní Graph API. Používá se také jako vlastník předplatného výchozí poskytovatele (který můžete později změnit). Na portálu správce vašeho systému Azure zásobníku se může přihlásit pomocí tohoto účtu.

1. Vytvořte účet Azure AD, který je správcem adresáře pro alespoň jeden Azure AD. Pokud už účet máte, můžete ho použít. Jinak, můžete jej vytvořit zdarma v [ https://azure.microsoft.com/free/ ](http://azure.microsoft.com/pricing/free/) (v Číně, navštivte <http://go.microsoft.com/fwlink/?LinkID=717821> místo). Pokud budete chtít později [zaregistrovat zásobník Azure s Azure](asdk-register.md), je také nutné mít předplatné v tomto nově vytvořený účet.
   
    Tyto přihlašovací údaje pro použití uložte jako správce služeb. Tento účet můžete nakonfigurovat a spravovat prostředků cloudy, uživatelské účty, plány klienta, kvóty a ceny. Na portálu může vytvářet webové cloudy, soukromé cloudy pro virtuální počítače a plány a spravovat předplatné uživatelů.
1. Vytvořte alespoň jeden testovací uživatelský účet ve službě Azure AD, takže můžete přihlásit development Kit jako klient.
   
   | **Účet Azure Active Directory** | **Podporovány?** |
   | --- | --- |
   | Pracovní nebo školní účet s platným předplatným veřejný Azure |Ano |
   | Účet Microsoft s platným veřejným předplatným Azure |Ano |
   | Pracovní nebo školní účet s platným předplatným Azure Čína |Ano |
   | Pracovní nebo školní účet s platným US Government Azure předplatným |Ano |

## <a name="network"></a>Síť
### <a name="switch"></a>Přepínač
Jeden dostupný port na přepínači pro počítač development kit.  

Vývojovém počítači kit podporuje připojení k přístupu k portu přepínače nebo portu trunk. Přepínač nevyžaduje žádné specializované funkce. Pokud používáte kmenový port nebo pokud potřebujete nakonfigurovat ID sítě VLAN, musíte ID sítě VLAN zadat jako parametr nasazení.

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
Ověřte si dostupnost serveru DHCP v síti, do které se síťová karta připojuje. Pokud server DHCP není dostupný, připravte další statickou síť IPv4 (kromě té, kterou používá hostitel). Tuto IP adresu a bránu zadejte jako parametr nasazení.

### <a name="internet-access"></a>Přístup k internetu
Zásobník Azure vyžaduje přístup k Internetu, buď přímo nebo prostřednictvím proxy serveru transparentní. Azure zásobník nepodporuje konfiguraci webového proxy serveru pro povolení přístupu k Internetu. IP adresa hostitele a nových IP přiřazená MAS-BGPNAT01 (pomocí protokolu DHCP nebo statickou IP adresu) musí být mít přístup k Internetu. V rámci domény graph.windows.net a login.microsoftonline.com používají porty 80 a 443.


## <a name="next-steps"></a>Další postup
[Stáhněte si balíček nasazení ASDK](asdk-download.md)
