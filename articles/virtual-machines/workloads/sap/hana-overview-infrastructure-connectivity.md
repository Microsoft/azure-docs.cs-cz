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
ms.openlocfilehash: 1d8bbe2fc218004116177c4c9d95777d9ec57503
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426042"
---
# <a name="sap-hana-large-instances-deployment"></a>Nasazení SAP HANA (velké instance) 

Tento článek předpokládá, že jste dokončili nákupu SAP Hana v Azure (velké instance) od Microsoftu. Před čtením tohoto článku, Obecné, naleznete v tématu [HANA velké instance běžných termínů](hana-know-terms.md) a [HANA velké instance SKU](hana-available-skus.md).


Microsoft vyžaduje následující informace k nasazení jednotek velká instance HANA:

- Jméno zákazníka.
- Obchodní kontaktní údaje (včetně e-mailovou adresu a telefonní číslo).
- Technické kontaktní údaje (včetně e-mailovou adresu a telefonní číslo).
- Technické sítě kontaktní údaje (včetně e-mailovou adresu a telefonní číslo).
- Oblast Azure nasazení (například USA – Západ, Austrálie – východ nebo Severní Evropa).
- SAP HANA v Azure (velké instance) skladové položky (konfigurace).
- Pro každou oblast Azure nasazení:
    - Minimální velikostí/29 rozsah IP adres pro ER P2P připojení virtuální sítě Azure pro velké instance HANA.
    - / 24 je používá blok CIDR pro fond IP serveru velkých instancích HANA.
- IP adresu rozsahu hodnot použitých v atribut místo adresy virtuální sítě každý Azure, která se připojuje k velké instance HANA virtuální sítě.
- Data pro každý systém velkých instancích HANA:
  - Požadovaný název hostitele, ideálně s použitím plně kvalifikovaného názvu domény.
  - Požadovanou IP adresu pro jednotku velká instance HANA mimo rozsah adres fondu IP adresa serveru. (Prvních 30 IP adresy v rozsahu serveru fond IP adres jsou vyhrazené pro interní použití v rámci velkých instancích HANA).
  - SAP HANA SID název instance SAP HANA (nutné pro vytvoření nezbytné týkající se SAP HANA diskové svazky). Microsoft potřebuje HANA SID pro vytvoření oprávnění pro sidadm na svazky systému souborů NFS. Tyto svazky se připojit k jednotce velká instance HANA. Identifikátor SID HANA se také používá jako jeden název komponenty diskové svazky, které připojit. Pokud chcete spustit více než jednu instanci HANA na jednotce, můžete zveřejnit více identifikátorů SID HANA. Každá z nich dostane samostatnou sadu svazky přiřazené.
  - V operačním systému Linux má uživatel sidadm ID skupiny. Toto ID je potřeba vytvořit nezbytné týkající se SAP HANA diskové svazky. Instalace SAP HANA obvykle vytvoří skupiny sapsys s ID skupiny 1001. Uživatel sidadm je součástí této skupiny.
  - V operačním systému Linux má uživatel sidadm ID uživatele. Toto ID je potřeba vytvořit nezbytné týkající se SAP HANA diskové svazky. Pokud používáte několik instance HANA na jednotce, vypsání sidadm uživatelů. 
- ID předplatného Azure pro předplatné Azure, do které SAP HANA v Azure HANA velké instance budou přímo připojený k Internetu. Toto ID předplatného odkazuje na předplatné Azure, který se bude účtovat s HANA velká instance jednotku nebo jednotky.

Po zadání výše uvedených informací Microsoftu zřídí SAP HANA v Azure (velké instance). Microsoft vám pošle informace o propojení virtuálním sítím Azure pro velké instance HANA. Můžete také přistupovat jednotky velká instance HANA.

Pro připojení k velké instance HANA po Microsoft nasadila, použijte následující posloupnost:

1. [Připojení virtuálních počítačů Azure pro velké instance HANA](hana-connect-azure-vm-large-instances.md)
2. [Propojení virtuální sítě pro ExpressRoute velké instance HANA](hana-connect-vnet-express-route.md)
3. [Další síťové požadavky (volitelné)](hana-additional-network-requirements.md)

