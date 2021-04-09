---
title: Přehled scénáře zotavení po havárii Oracle v prostředí Azure | Microsoft Docs
description: Scénář zotavení po havárii pro databázi Oracle Database 12c v prostředí Azure
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 08/02/2018
ms.author: kegorman
ms.openlocfilehash: 68b5b9dfd205628c9d7c430df4c0230267752e01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101669950"
---
# <a name="disaster-recovery-for-an-oracle-database-12c-database-in-an-azure-environment"></a>Zotavení po havárii pro databázi Oracle Database 12c v prostředí Azure

## <a name="assumptions"></a>Předpoklady

- Znáte návrh Oracle data Guard a prostředí Azure.


## <a name="goals"></a>Cíle
- Navrhněte topologii a konfiguraci, které odpovídají požadavkům zotavení po havárii (DR).

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>Scénář 1: primární lokality a weby DR v Azure

Zákazník má nastavenou databázi Oracle v primární lokalitě. Lokalita DR je v jiné oblasti. Zákazník používá Oracle data Guard pro rychlé obnovení mezi těmito lokalitami. Primární lokalita má také sekundární databázi pro vytváření sestav a další použití. 

### <a name="topology"></a>Topologie

Tady je souhrn nastavení Azure:

- Dvě lokality (primární lokalita a lokalita DR)
- Dvě virtuální sítě
- Dvě databáze Oracle s ochranou dat (primární a pohotovostní)
- Dvě databáze Oracle se zlatou bránou nebo ochranou dat (jenom primární lokalita)
- Dvě aplikační služby, jedna primární a jedna na webu DR
- *Skupina dostupnosti,* která se používá pro databázi a službu aplikace v primární lokalitě
- Jeden JumpBox na každé lokalitě, který omezuje přístup k privátní síti a umožňuje pouze přihlášení správcem
- JumpBox, Aplikační služba, databáze a brána sítě VPN v samostatných podsítích
- NSG vynutilo pro podsítě aplikace a databáze

![Diagram, který zobrazuje primární lokality a weby DR v Azure.](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>Scénář 2: místní lokalita a lokalita DR v Azure

Zákazník má místní nastavení Oracle Database (primární lokalita). Lokalita DR je v Azure. Oracle data Guard slouží k rychlému obnovení mezi těmito lokalitami. Primární lokalita má také sekundární databázi pro vytváření sestav a další použití. 

Existují dva přístupy k tomuto nastavení.

### <a name="approach-1-direct-connections-between-on-premises-and-azure-requiring-open-tcp-ports-on-the-firewall"></a>Přístup 1: přímé připojení mezi místními počítači a Azure a vyžadováním otevřených portů TCP v bráně firewall 

Nedoporučujeme přímé připojení, protože zveřejňuje porty TCP na vnějším světě.

#### <a name="topology"></a>Topologie

Toto je souhrn nastavení Azure:

- Jeden web DR 
- Jedna virtuální síť
- Jedna databáze Oracle s ochranou dat (aktivní)
- Jedna Aplikační služba na webu DR
- Jeden JumpBox, který omezuje přístup k privátní síti a umožňuje pouze přihlášení správcem
- JumpBox, Aplikační služba, databáze a brána sítě VPN v samostatných podsítích
- NSG vynutilo pro podsítě aplikace a databáze
- Zásada nebo pravidlo NSG, které povolí příchozí port TCP 1521 (nebo uživatelem definovaný port)
- Zásady nebo pravidlo NSG, které omezí jenom IP adresu/adresy v místním prostředí (DB nebo Application) pro přístup k virtuální síti.

![Diagram, který zobrazuje přímá propojení mezi místními a Azure a vyžadováním otevírání portů TCP v bráně firewall.](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="approach-2-site-to-site-vpn"></a>Přístup 2: síť VPN typu Site-to-site
VPN typu Site-to-site je lepší přístup. Další informace o nastavení sítě VPN najdete v tématu [vytvoření virtuální sítě s připojením VPN typu Site-to-site pomocí](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)rozhraní příkazového řádku (CLI).

#### <a name="topology"></a>Topologie

Toto je souhrn nastavení Azure:

- Jeden web DR 
- Jedna virtuální síť 
- Jedna databáze Oracle s ochranou dat (aktivní)
- Jedna Aplikační služba na webu DR
- Jeden JumpBox, který omezuje přístup k privátní síti a umožňuje pouze přihlášení správcem
- JumpBox, služba Application Service, databáze a VPN Gateway jsou v samostatných podsítích.
- NSG vynutilo pro podsítě aplikace a databáze
- Připojení VPN typu Site-to-site mezi místními počítači a Azure

![Snímek obrazovky se stránkou topologie DR](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-reading"></a>Další materiály ke čtení

- [Návrh a implementace databáze Oracle v Azure](oracle-design.md)
- [Konfigurace Oracle Data Guardu](configure-oracle-dataguard.md)
- [Konfigurace Oracle Zlaté brány](configure-oracle-golden-gate.md)
- [Zálohování a obnovení Oracle](./oracle-overview.md)


## <a name="next-steps"></a>Další kroky

- [Kurz: Vytvoření virtuálních počítačů s vysokou dostupností](../../linux/create-cli-complete.md)
- [Ukázky ukázek Azure CLI pro nasazení virtuálních počítačů](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)