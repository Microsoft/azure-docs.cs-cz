---
title: Infrastruktura a připojení k SAP HANA v Azure (velké instance) | Microsoft Docs
description: Nakonfigurujte požadovanou infrastrukturu připojení pro použití SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 27dc2f6c647308984a02f2cf52dbc0213abad088
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676826"
---
# <a name="sap-hana-large-instances-deployment"></a>Nasazení SAP HANA (velké instance) 

V tomto článku se předpokládá, že jste dokončili nákup SAP HANA v Azure (velké instance) od Microsoftu. Než si přečtete tento článek, Obecné informace najdete v tématu [běžné výrazy pro velké instance Hana](hana-know-terms.md) a [SKU velkých instancí Hana](hana-available-skus.md).


Aby bylo možné nasadit jednotky velkých instancí HANA, společnost Microsoft vyžaduje následující informace:

- Jméno zákazníka.
- Obchodní kontaktní informace (včetně e-mailové adresy a telefonního čísla).
- Technické kontaktní informace (včetně e-mailové adresy a telefonního čísla)
- Kontaktní informace pro technické sítě (včetně e-mailové adresy a telefonního čísla)
- Oblast nasazení Azure (například Západní USA, Austrálie – východ nebo Severní Evropa).
- SAP HANA v Azure (velké instance) SKU (konfigurace).
- Pro každou oblast nasazení Azure:
    - Rozsah IP adres a/29 pro připojení ER-P2P, která spojují virtuální sítě Azure s velkými instancemi HANA.
    - Blok CIDR a/24 použitý pro fond IP adres serveru rozsáhlých instancí HANA.
    - Volitelné při použití [Global REACH ExpressRoute](../../../expressroute/expressroute-global-reach.md) k umožnění přímého směrování z místního prostředí do Hana velkých jednotek instancí nebo směrování mezi velkými jednotkami instancí v různých oblastech Azure, je potřeba rezervovat jiný rozsah IP adres/29. Tento konkrétní rozsah se nesmí překrývat s žádným z dalších rozsahů IP adres, které jste definovali dříve.
- Hodnoty rozsahu IP adres, které se používají v atributu adresního prostoru virtuální sítě v každé službě Azure Virtual Network, která se připojuje ke velkých instancím HANA.
- Data pro každý systém velkých instancí HANA:
  - Požadovaný název hostitele, v ideálním případě s plně kvalifikovaným názvem domény.
  - Požadovaná IP adresa pro jednotku velkých instancí HANA se nenachází mimo rozsah adres fondu IP adres serveru. (Prvních 30 IP adres v rozsahu adresního fondu IP adres je rezervovaný pro interní použití v rámci velkých instancí HANA.)
  - SAP HANA název SID pro instanci SAP HANA (vyžaduje se k vytvoření potřebných svazků disku souvisejících s SAP HANA). Microsoft potřebuje pro vytváření oprávnění pro sidadm na svazcích NFS identifikátor zabezpečení HANA. Tyto svazky se připojují k jednotce velké instance HANA. Identifikátor SID pro HANA se používá také jako jedna z komponent názvu diskových svazků, které jsou připojeny. Pokud chcete na jednotce spustit více než jednu instanci HANA, měli byste uvést více identifikátorů SID pro HANA. Každý z nich získá samostatnou sadu svazků, které jsou přiřazeny.
  - V operačním systému Linux má uživatel sidadm ID skupiny. Toto ID je nutné k vytvoření potřebných svazků disku souvisejících s SAP HANA. Instalace SAP HANA obvykle vytvoří skupinu sapsys s ID skupiny 1001. Uživatel sidadm je součástí této skupiny.
  - V operačním systému Linux má uživatel sidadm ID uživatele. Toto ID je nutné k vytvoření potřebných svazků disku souvisejících s SAP HANA. Pokud na jednotce spouštíte několik instancí HANA, Seznamte se s sidadm uživateli. 
- ID předplatného Azure pro předplatné Azure, ke kterému SAP HANA ve velkých instancích Azure HANA, bude přímo připojeno. Toto ID předplatného odkazuje na předplatné Azure, které se bude účtovat s jednotkou nebo jednotkami velké instance HANA.

Po zadání výše uvedených informací Microsoft zřídí SAP HANA v Azure (velké instance). Microsoft pošle informace, aby propojí vaše virtuální sítě Azure s velkými instancemi HANA. Můžete také přistupovat k jednotkám velkých instancí HANA.

Pomocí následujícího postupu se připojte k velkým instancím HANA poté, co ji Microsoft nasadí:

1. [Připojení virtuálních počítačů Azure k velkým instancím HANA](hana-connect-azure-vm-large-instances.md)
2. [Připojení virtuální sítě k ExpressRoute velkým instancím v HANA](hana-connect-vnet-express-route.md)
3. [Další požadavky na síť (volitelné)](hana-additional-network-requirements.md)
