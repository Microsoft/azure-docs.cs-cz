---
title: Přehled Oracle zotavení po havárii v prostředí Azure | Microsoft Docs
description: Na scénář zotavení po havárii pro databázi Oracle Database 12c v prostředí Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/2/2017
ms.author: rclaus
ms.openlocfilehash: bb319c4ba9bbfba584803b35a0db0763fcf97b86
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657857"
---
# <a name="disaster-recovery-for-an-oracle-database-12c-database-in-an-azure-environment"></a>Zotavení po havárii pro databázi Oracle Database 12c v prostředí Azure

## <a name="assumptions"></a>Předpoklady

- Máte představu o Oracle Data Guard návrhu a prostředí Azure.


## <a name="goals"></a>Cíle
- Návrh topologie a konfigurace, které splňují vaše požadavky na zotavení po havárii.

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>Scénář 1: Primární a zotavení po Havárii weby na Azure

Zákazník má Oracle databáze sadu nahoru v primární lokalitě. Zotavení po Havárii A lokalita je v jiné oblasti. Zákazník používá Oracle Data Guard pro rychlé obnovení mezi těmito lokalitami. Primární lokalita má také sekundární databáze pro vytváření sestav a jiné účely. 

### <a name="topology"></a>Topologie

Zde je souhrn nastavení Azure:

- Dvěma lokalitami (primární lokalitou a lokalitou zotavení po Havárii)
- Dvě virtuální sítě
- Dvě databáze Oracle se Data Guard (primární i pohotovostní režim)
- Dvě databáze Oracle se Golden brány nebo Data Guard (pouze primární lokalita)
- Dvě aplikace služby, jeden primární a jeden v lokalitě zotavení po Havárii
- *Nastavení dostupnosti,* sloužící pro databázi a aplikace služby v primární lokalitě
- Jeden jumpbox v každé lokalitě, která omezuje přístup k privátní síti a umožňuje pouze přihlášení správce
- Jumpbox, služba aplikace, databáze a brány VPN oddělené podsítě
- Skupina NSG vynucená u aplikace a databáze podsítě

![Snímek obrazovky stránky topologie zotavení po Havárii](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>Scénář 2: Primární lokality na místě a zotavení po Havárii webu v Azure

Zákazník má nastavení databáze Oracle místní (primární lokalita). Zotavení po Havárii A lokalita je v Azure. Oracle Data Guard se používá pro rychlé obnovení mezi těmito lokalitami. Primární lokalita má také sekundární databáze pro vytváření sestav a jiné účely. 

Existují dva přístupy pro tento instalační program.

### <a name="approach-1-direct-connections-between-on-premises-and-azure-requiring-open-tcp-ports-on-the-firewall"></a>Způsob 1: Přímé připojení mezi místními a Azure, vyžadování otevřené porty protokolu TCP v bráně firewall. 

Přímé připojení nedoporučujeme, protože potenciálně zobrazovat porty TCP k vnějším světem.

#### <a name="topology"></a>Topologie

Následuje souhrn nastavení Azure:

- Jeden DR lokality 
- Jedna virtuální síť
- Jedna databáze Oracle s Data Guard (aktivní)
- Služba jednu aplikaci na webu zotavení po Havárii
- Jeden jumpbox, která omezuje přístup k privátní síti a umožňuje pouze přihlášení správce
- Jumpbox, služba aplikace, databáze a brány VPN oddělené podsítě
- Skupina NSG vynucená u aplikace a databáze podsítě
- Skupina NSG zásad nebo pravidlo povolit příchozí port TCP 1521 (nebo na uživatelem definované port)
- Skupina NSG zásad nebo pravidlo omezit pouze IP adresa nebo adresy místní (DB nebo aplikace) pro přístup k virtuální síti

![Snímek obrazovky stránky topologie zotavení po Havárii](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="approach-2-site-to-site-vpn"></a>Způsob 2: Site-to-site VPN
Site-to-site VPN je lepší přístup. Další informace o nastavení sítě VPN najdete v tématu [vytvoření virtuální sítě pomocí připojení Site-to-Site VPN pomocí rozhraní příkazového řádku](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli).

#### <a name="topology"></a>Topologie

Následuje souhrn nastavení Azure:

- Jeden DR lokality 
- Jedna virtuální síť 
- Jedna databáze Oracle s Data Guard (aktivní)
- Služba jednu aplikaci na webu zotavení po Havárii
- Jeden jumpbox, která omezuje přístup k privátní síti a umožňuje pouze přihlášení správce
- Jumpbox, služba aplikace, databáze a brány VPN jsou v samostatných podsítích
- Skupina NSG vynucená u aplikace a databáze podsítě
- Připojení Site-to-site VPN mezi místními a Azure

![Snímek obrazovky stránky topologie zotavení po Havárii](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-reading"></a>Další čtení

- [Návrh a implementaci databáze Oracle na Azure](oracle-design.md)
- [Konfigurace Oracle Data Guard](configure-oracle-dataguard.md)
- [Konfigurace brány Golden Oracle](configure-oracle-golden-gate.md)
- [Oracle zálohování a obnovení](oracle-backup-recovery.md)


## <a name="next-steps"></a>Další postup

- [Kurz: Vytvoření vysoce dostupné virtuální počítače](../../linux/create-cli-complete.md)
- [Prozkoumejte ukázky rozhraní příkazového řádku Azure nasazení virtuálních počítačů](../../linux/cli-samples.md)
