---
title: Přehled scénáře zotavení po havárii společnosti Oracle ve vašem prostředí Azure | Dokumenty společnosti Microsoft
description: Scénář zotavení po havárii pro databázi Oracle Database 12c ve vašem prostředí Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: DavidCBerry13
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: daberry
ms.openlocfilehash: 527c4cfdad3fc371bddd42388aa432c27a2877df
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272963"
---
# <a name="disaster-recovery-for-an-oracle-database-12c-database-in-an-azure-environment"></a>Zotavení po havárii pro databázi Oracle Database 12c v prostředí Azure

## <a name="assumptions"></a>Předpoklady

- Rozumíte návrhu oracle data guard a prostředím Azure.


## <a name="goals"></a>Cíle
- Navrhněte topologii a konfiguraci, které splňují vaše požadavky na zotavení po havárii.

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>Scénář 1: Primární a zotavení po Havárii v Azure

Zákazník má databázi Oracle nastavenou v primární lokalitě. Lokalita zotavení po Havárii se nachází v jiné oblasti. Zákazník používá službu Oracle Data Guard pro rychlé obnovení mezi těmito weby. Primární lokalita má také sekundární databázi pro vytváření sestav a další použití. 

### <a name="topology"></a>Topologie

Tady je souhrn nastavení Azure:

- Dvě lokality (primární lokalita a lokalita zotavení po Havárii)
- Dvě virtuální sítě
- Dvě databáze Oracle s ochranou dat (primární a pohotovostní režimní režim)
- Dvě databáze Oracle s Golden Gate nebo Data Guard (pouze primární lokalita)
- Dvě aplikační služby, jedna primární a jedna na webu zotavení po Havárii
- *Sada dostupnosti,* která se používá pro databázovou a aplikační službu v primární lokalitě
- Jeden jumpbox na každé stránce, který omezuje přístup k privátní síti a umožňuje pouze přihlášení správcem
- Brána jumpbox, aplikační služby, databáze a sítě VPN v samostatných podsítích
- Skupina zabezpečení sítě vynucená v podsítích aplikací a databází

![Snímek obrazovky stránky topologie zotavení po Havárii](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>Scénář 2: Primární lokalita v místním prostředí a lokalita zotavení po Havárii v Azure

Zákazník má místní nastavení databáze Oracle (primární lokalita). Lokalita zotavení po Havárii je v Azure. Oracle Data Guard se používá pro rychlé obnovení mezi těmito weby. Primární lokalita má také sekundární databázi pro vytváření sestav a další použití. 

Existují dva přístupy pro toto nastavení.

### <a name="approach-1-direct-connections-between-on-premises-and-azure-requiring-open-tcp-ports-on-the-firewall"></a>Přístup 1: Přímé připojení mezi místním a Azure, vyžadování otevřených portů TCP na bráně firewall 

Nedoporučujeme přímé připojení, protože zveřejňují porty TCP vnějšímu světu.

#### <a name="topology"></a>Topologie

Následuje souhrn nastavení Azure:

- Jeden web zotavení po havárii 
- Jedna virtuální síť
- Jedna databáze Oracle s ochranou dat (aktivní)
- Jedna aplikační služba na webu zotavení po Havárii
- Jeden jumpbox, který omezuje přístup k privátní síti a umožňuje pouze přihlášení správcem
- Brána jumpbox, aplikační služby, databáze a sítě VPN v samostatných podsítích
- Skupina zabezpečení sítě vynucená v podsítích aplikací a databází
- Zásada/pravidlo skupiny nsg umožňující příchozí port TCP 1521 (nebo uživatelem definovaný port)
- Zásada/pravidlo skupiny zabezpečení sítě pro omezení přístupu k virtuální síti jenom místní IP adresy/adresy (DB nebo aplikace)

![Snímek obrazovky stránky topologie zotavení po Havárii](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="approach-2-site-to-site-vpn"></a>Přístup 2: VPN site-to-site
Site-to-site VPN je lepší přístup. Další informace o nastavení sítě VPN naleznete v [tématu Vytvoření virtuální sítě s připojením VPN site-to-site pomocí funkce CLI](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli).

#### <a name="topology"></a>Topologie

Následuje souhrn nastavení Azure:

- Jeden web zotavení po havárii 
- Jedna virtuální síť 
- Jedna databáze Oracle s ochranou dat (aktivní)
- Jedna aplikační služba na webu zotavení po Havárii
- Jeden jumpbox, který omezuje přístup k privátní síti a umožňuje pouze přihlášení správcem
- Jumpbox, aplikační služba, databáze a brána VPN jsou v samostatných podsítích
- Skupina zabezpečení sítě vynucená v podsítích aplikací a databází
- Připojení VPN mezi místním a Azure mezi místním i Azure

![Snímek obrazovky stránky topologie zotavení po Havárii](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-reading"></a>Dodatečné čtení

- [Návrh a implementace databáze Oracle v Azure](oracle-design.md)
- [Konfigurace Oracle Data Guardu](configure-oracle-dataguard.md)
- [Konfigurace služby Oracle Golden Gate](configure-oracle-golden-gate.md)
- [Zálohování a obnovení společnosti Oracle](oracle-backup-recovery.md)


## <a name="next-steps"></a>Další kroky

- [Kurz: Vytvoření vysoce dostupných virtuálních měn](../../linux/create-cli-complete.md)
- [Prozkoumejte ukázky azure cli nasazení virtuálních montovek virtuálních montovek](../../linux/cli-samples.md)
