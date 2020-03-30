---
title: Infrastruktura a připojení k SAP HANA v Azure (velké instance) | Dokumenty společnosti Microsoft
description: Nakonfigurujte požadovanou infrastrukturu připojení tak, aby používala SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cea89087742f1987f693b8bfb627bd71038a0c14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616927"
---
# <a name="sap-hana-large-instances-deployment"></a>Nasazení SAP HANA (velké instance) 

Tento článek předpokládá, že jste dokončili nákup SAP HANA v Azure (velké instance) od Microsoftu. Před přečtením tohoto článku, obecné pozadí naleznete [v tématu HANA velké instance běžné termíny](hana-know-terms.md) a [HANA velké instance skum](hana-available-skus.md).


Společnost Microsoft vyžaduje následující informace k nasazení jednotek velkých instancí HANA:

- Jméno zákazníka.
- Obchodní kontaktní údaje (včetně e-mailové adresy a telefonního čísla).
- Technické kontaktní údaje (včetně e-mailové adresy a telefonního čísla).
- Technické kontaktní informace pro síť (včetně e-mailové adresy a telefonního čísla).
- Oblast nasazení Azure (například Západní USA, Austrálie – východ nebo Severní Evropa).
- SAP HANA v Azure (velké instance) Skladová položka (konfigurace).
- Pro každou oblast nasazení Azure:
    - Rozsah IP adres /29 pro připojení ER-P2P, které připojují virtuální sítě Azure k velkým instancím HANA.
    - Blok /24 CIDR používaný pro fond IP serveru velkých instancí HANA.
    - Volitelné při použití [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) povolit přímé směrování z místních jednotek velké instance HANA nebo směrování mezi jednotkami velké instance HANA v různých oblastech Azure, je třeba rezervovat další rozsah IP adres /29. Tento konkrétní rozsah se nesmí překrývat s žádným z ostatních rozsahů IP adres, které jste definovali dříve.
- Hodnoty rozsahu IP adres použité v atributu adresního prostoru virtuální sítě každé virtuální sítě Azure, která se připojuje k velkým instancím HANA.
- Data pro každý systém velkých instancí HANA:
  - Požadovaný název hostitele, v ideálním případě s plně kvalifikovaným názvem domény.
  - Požadovaná adresa IP pro jednotku velké instance HANA mimo rozsah adres fondu IP serveru. (Prvních 30 adres IP v rozsahu adres fondu IP serveru je vyhrazeno pro interní použití ve velkých instancích HANA.)
  - Název SAP HANA SID pro instanci SAP HANA (vyžadováno k vytvoření potřebných diskových svazků souvisejících s SAP HANA). Společnost Microsoft potřebuje HANA SID pro vytváření oprávnění pro sidadm na svazcích systému souborů NFS. Tyto svazky připojit k jednotce velké instance HANA. HANA SID se také používá jako jeden z názvů součástí diskových svazků, které si připojen. Pokud chcete spustit více než jednu instanci HANA na jednotce, měli byste uvést více SID HANA. Každý z nich získá samostatnou sadu svazků přiřazených.
  - V operačním systému Linux má uživatel sidadm ID skupiny. Toto ID je nutné k vytvoření potřebných svazků disku souvisejících s SAP HANA. Instalace SAP HANA obvykle vytvoří skupinu sapsys, s ID skupiny 1001. Uživatel sidadmu je součástí této skupiny.
  - V operačním systému Linux má uživatel sidadm ID uživatele. Toto ID je nutné k vytvoření potřebných svazků disku souvisejících s SAP HANA. Pokud na jednotce používáte několik instancí HANA, uveďte seznam všech uživatelů sidadmu. 
- ID předplatného Azure pro předplatné Azure, ke kterému bude SAP HANA ve velkých instancích Azure HANA přímo připojeno. Toto ID předplatného odkazuje na předplatné Azure, které se bude účtovat s jednotkou nebo jednotkami velké instance HANA.

Po poskytnutí předchozích informací, Microsoft zřídí SAP HANA v Azure (velké instance). Společnost Microsoft vám odesílá informace k propojení virtuálních sítí Azure s velkými instancemi HANA. Můžete také přistupovat k jednotkám velkých instancí HANA.

Pomocí následující sekvence se můžete připojit k velkým instancím HANA po jeho nasazení společností Microsoft:

1. [Připojení virtuálních počítačů Azure k velkým instancím HANA](hana-connect-azure-vm-large-instances.md)
2. [Připojení virtuální sítě k velkým instancím HANA ExpressRoute](hana-connect-vnet-express-route.md)
3. [Další požadavky na síť (volitelné)](hana-additional-network-requirements.md)

