---
title: Infrastruktura a možnosti připojení k SAP HANA v Azure (velké instance) | Dokumentace Microsoftu
description: Konfigurace infrastruktury vyžaduje připojení k použití SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c61dffc6fd9d0c65f5e925daebdf2a02cfb5d6ba
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44161352"
---
# <a name="sap-hana-large-instances-deployment"></a>Nasazení SAP HANA (velké instance) 

Po nákupu SAP HANA v Azure (velké instance) se dokončuje mezi vámi a zákaznický tým Microsoftu enterprise, tyto informace se společností Microsoft vyžadovány pro nasazení jednotky velké Instance HANA:

- Jméno zákazníka
- Obchodní kontaktní údaje (včetně e-mailovou adresu a telefonní číslo)
- Technické kontaktní údaje (včetně e-mailovou adresu a telefonní číslo)
- Technické síťové kontaktní informace (včetně e-mailovou adresu a telefonní číslo)
- Nasazení Azure oblast (USA – Západ, USA – východ, Austrálie – východ, Austrálie – jihovýchod, západní Evropa a Severní Evropa k červenci 2017)
- Potvrďte SAP HANA v Azure (velké instance) skladové položky (konfigurace).
- Jak již podrobně popsané v dokumentu přehled a architektura pro velké instance HANA, pro každou oblast Azure se nasazuje do:
    - Minimální velikostí/29 rozsah IP adres pro ER P2P připojení, která propojení virtuálních sítí Azure pro velké instance HANA
    - / 24 je používá blok CIDR pro HANA velké instance serveru fond IP adres
- IP adresa rozsahu hodnoty použité v atributu adresní prostor virtuální sítě každou virtuální síť Azure, která se připojuje k velké instance HANA
- Data pro každou z velkých instancích HANA system:
  - Požadovaný název hostitele – v ideálním případě s plně kvalifikovaným názvem domény.
  - Požadovanou IP adresu pro jednotku velká Instance HANA mimo rozsah adres fondu IP serveru – mějte na paměti, že prvních 30 IP adresy v rozsahu adres fondu IP serveru jsou vyhrazeny pro interní použití v rámci velkých instancích HANA
  - SAP HANA SID název instance SAP HANA (nutné pro vytvoření nezbytné týkající se SAP HANA diskové svazky). Je potřeba pro vytvoření oprávnění pro sidadm na svazky systému souborů NFS, které jsou získávání připojené jednotce velká Instance HANA identifikátor SID HANA. Také se používá jako jeden název komponenty diskové svazky, které připojit. Pokud chcete spustit více než jednu instanci HANA na jednotce, budete muset seznamu více identifikátorů SID HANA. Každá z nich dostane samostatnou sadu svazky přiřazené.
  - ID skupiny, které má uživatel sidadm v operačním systému Linux, je potřeba vytvořit nezbytné týkající se SAP HANA diskové svazky. Instalace SAP HANA obvykle vytvoří skupinu sapsys s id skupiny 1001. Uživatel sidadm je součástí této skupiny
  - ID uživatele, které má uživatel sidadm v operačním systému Linux je nutné vytvořit nezbytné týkající se SAP HANA diskové svazky. Pokud používáte více instancích HANA na jednotce, budete muset všechny <sid>ADM – uživatelé 
- ID předplatného Azure pro předplatné Azure, do které SAP HANA v Azure HANA velké instance budou přímo připojený k Internetu. Toto ID předplatného odkazuje na předplatné Azure, který se bude účtovat s jednotek velká Instance HANA.

Po zadání informací, společnost Microsoft zřídí SAP HANA v Azure (velké instance) a se vrátit informace potřebné k propojení virtuální sítě Azure pro velké instance HANA a pro přístup k velká Instance HANA jednotky.

Před čtením tohoto článku, seznamte se s [velkých instancích HANA běžných termínů](hana-know-terms.md) a [SKU velké instance HANA](hana-available-skus.md).

Pro připojení k velké instance HANA po nasazení Microsoft můžete použít následující text:

1. [Připojení virtuálních počítačů Azure pro velké instance HANA](hana-connect-azure-vm-large-instances.md)
2. [Propojení virtuální sítě ExpressRoute velké instance HANA](hana-connect-vnet-express-route.md)
3. [Další síťové požadavky (volitelné)](hana-additional-network-requirements.md)

**Další kroky**

- Přečtěte si [připojení virtuálních počítačů Azure pro velké instance HANA](hana-connect-azure-vm-large-instances.md).
- Přečtěte si [propojení virtuální sítě ExpressRoute velká Instance HANA](hana-connect-vnet-express-route.md).