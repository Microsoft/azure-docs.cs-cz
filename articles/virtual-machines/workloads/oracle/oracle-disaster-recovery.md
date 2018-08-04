---
title: Přehled scénáře zotavení po havárii Oracle ve vašem prostředí Azure | Dokumentace Microsoftu
description: Scénář zotavení po havárii pro databáze Oracle database 12c v prostředí Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: 9f525e68502e32a3f9c7e7cebe6d45627f9077c3
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495005"
---
# <a name="disaster-recovery-for-an-oracle-database-12c-database-in-an-azure-environment"></a>Zotavení po havárii pro databáze Oracle Database 12c v prostředí Azure

## <a name="assumptions"></a>Předpoklady

- Máte znalosti Oracle Data Guard návrhu a prostředí Azure.


## <a name="goals"></a>Cíle
- Návrh topologii a konfiguraci, která splňují vaše požadavky na zotavení po havárii.

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>Scénář 1: Servery primární a zotavení po Havárii v Azure

Zákazník má Oracle databáze set up v primární lokalitě. Zotavení po Havárii A lokalita je v jiné oblasti. Zákazník používá Oracle Data Guard pro rychlé obnovení mezi těmito lokalitami. Primární lokalita má také sekundární databáze pro vytváření sestav a jiné účely. 

### <a name="topology"></a>Topologie

Zde je souhrn instalace nástroje Azure:

- Dva servery (primární lokalitou a lokalitou zotavení po Havárii)
- Dvě virtuální sítě
- Dvě databáze Oracle s ochranou dat (primární a pohotovostní)
- Dvě databáze Oracle se brána Golden nebo Data Guard (pouze primární lokalita)
- Dva aplikační služby, jeden primární a v lokalitě zotavení po Havárii
- *Skupinu dostupnosti,* který se používá pro databáze a aplikace služby v primární lokalitě
- Jeden jumpbox v každé lokalitě, která omezuje přístup k privátní síti a umožňuje pouze přihlášení správce
- Jumpbox, aplikační služby, databáze a brány VPN v oddělených podsítích
- Skupina zabezpečení sítě pro aplikace a databáze podsítě vynucovat

![Snímek obrazovky stránky topologie zotavení po Havárii](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>Scénář 2: Primární lokalita v místním a zotavení po Havárii serveru v Azure

Zákazník má nastavení databáze Oracle v místním (primární lokalita). Zotavení po Havárii A lokalita je v Azure. Oracle Data Guard se používá pro rychlé obnovení mezi těmito lokalitami. Primární lokalita má také sekundární databáze pro vytváření sestav a jiné účely. 

Existují dvě metody pro tento instalační program.

### <a name="approach-1-direct-connections-between-on-premises-and-azure-requiring-open-tcp-ports-on-the-firewall"></a>Způsob 1: Přímé připojení mezi místními a Azure, by vyžadoval otevřené porty protokolu TCP v bráně firewall 

Přímé připojení nedoporučujeme, protože zveřejňovaly porty TCP zvnějšku.

#### <a name="topology"></a>Topologie

Toto je souhrn instalace nástroje Azure:

- Zotavení po Havárii jednoho webu 
- Jedna virtuální síť
- Jedna databáze Oracle s ochranou dat (aktivní)
- Jednu aplikaci služby v lokalitě zotavení po Havárii
- Jeden jumpboxu, což omezuje přístup k privátní síti a umožňuje pouze přihlášení správce
- Jumpbox, aplikační služby, databáze a brány VPN v oddělených podsítích
- Skupina zabezpečení sítě pro aplikace a databáze podsítě vynucovat
- Zásady/pravidlo NSG umožňuje příchozí port TCP 1521 (nebo uživatelem definovaný port)
- Pravidlo skupiny zabezpečení sítě zásady/omezit jenom IP adresa nebo adresy místní (databáze nebo aplikace) pro přístup k virtuální síti

![Snímek obrazovky stránky topologie zotavení po Havárii](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="approach-2-site-to-site-vpn"></a>Způsob 2: Site-to-site VPN
Site-to-site VPN není lepším řešením. Další informace o nastavení sítě VPN najdete v tématu [vytvoření virtuální sítě s připojením typu Site-to-Site VPN pomocí rozhraní příkazového řádku](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli).

#### <a name="topology"></a>Topologie

Toto je souhrn instalace nástroje Azure:

- Zotavení po Havárii jednoho webu 
- Jedna virtuální síť 
- Jedna databáze Oracle s ochranou dat (aktivní)
- Jednu aplikaci služby v lokalitě zotavení po Havárii
- Jeden jumpboxu, což omezuje přístup k privátní síti a umožňuje pouze přihlášení správce
- Jumpbox, aplikační služby, databáze a brány VPN jsou v samostatných podsítí
- Skupina zabezpečení sítě pro aplikace a databáze podsítě vynucovat
- Připojení VPN typu Site-to-site mezi místními a Azure

![Snímek obrazovky stránky topologie zotavení po Havárii](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-reading"></a>Další čtení

- [Návrh a implementace databáze Oracle v Azure](oracle-design.md)
- [Konfigurace Oracle Data Guard](configure-oracle-dataguard.md)
- [Konfigurace Oracle Golden brány](configure-oracle-golden-gate.md)
- [Oracle zálohování a obnovení](oracle-backup-recovery.md)


## <a name="next-steps"></a>Další postup

- [Kurz: Vytvoření vysoce dostupných virtuálních počítačů](../../linux/create-cli-complete.md)
- [Prozkoumejte ukázky nasazení virtuálního počítače pomocí Azure CLI](../../linux/cli-samples.md)
