---
title: Podpora systému souborů sítě 3,0 ve službě Azure Blob Storage (Preview) | Microsoft Docs
description: BLOB Storage teď podporuje protokol NFS (Network File System) 3,0. Tato podpora umožňuje klientům Windows a Linux připojit kontejner ve službě BLOB Storage z virtuálního počítače Azure nebo z počítače, který běží místně.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 78455659d76d259c626745c93770428c25042b2d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95908808"
---
# <a name="network-file-system-nfs-30-protocol-support-in-azure-blob-storage-preview"></a>Podpora protokolů systému souborů NFS (Network File System) 3,0 v úložišti objektů BLOB v Azure (Preview)

BLOB Storage teď podporuje protokol NFS (Network File System) 3,0. Tato podpora umožňuje klientům systému Windows nebo Linux připojit kontejner v úložišti objektů BLOB z virtuálního počítače Azure nebo místního počítače. 

> [!NOTE]
> Podpora protokolu NFS 3,0 v úložišti objektů BLOB v Azure je ve verzi Public Preview a je dostupná v těchto oblastech: USA – východ, USA – střed, USA – středozápad, Austrálie – jihovýchod, Severní Evropa, Velká Británie – západ, Korea – jih, Jižní Korea a Kanada – střed.

## <a name="general-workflow-mounting-a-storage-account-container"></a>Obecný pracovní postup: připojení kontejneru účtu úložiště

Pokud chcete připojit kontejner účtu úložiště, musíte provést tyto akce.

1. Zaregistrujte funkci protokolu NFS 3,0 v rámci vašeho předplatného.

2. Ověřte, zda je funkce zaregistrována.

3. Vytvořte Azure Virtual Network (virtuální síť).

4. Nakonfigurujte zabezpečení sítě.

5. Vytvořte a nakonfigurujte účet úložiště, který přijímá přenosy jenom z virtuální sítě.

6. Vytvořte kontejner v účtu úložiště.

7. Připojte kontejner.

Podrobné pokyny najdete v tématu [připojení úložiště objektů BLOB pomocí protokolu NFS (Network File System) 3,0 (Preview)](network-file-system-protocol-support-how-to.md).

> [!IMPORTANT]
> Je důležité, abyste tyto úlohy dokončili v uvedeném pořadí. Předtím, než na svém účtu povolíte protokol NFS 3,0, nemůžete připojit kontejnery, které vytvoříte. Po povolení protokolu NFS 3,0 na účtu ho nemůžete zakázat.

## <a name="network-security"></a>Zabezpečení sítě

Váš účet úložiště musí být obsažený v rámci virtuální sítě. Virtuální síť umožňuje klientům zabezpečené připojení k vašemu účtu úložiště. Jediným způsobem, jak zabezpečit data v účtu, je použít virtuální síť a další nastavení zabezpečení sítě. Jakýkoli jiný nástroj, který se používá k zabezpečení dat, včetně ověřování klíčů účtu, Azure Active Directory (AD) a seznamů řízení přístupu (ACL), se zatím nepodporují v účtech s povolenou podporou protokolu NFS 3,0. 

Další informace najdete v tématu [doporučení zabezpečení sítě pro úložiště objektů BLOB](security-recommendations.md#networking).

## <a name="supported-network-connections"></a>Podporovaná síťová připojení

Klient se může připojit přes Veřejný nebo [soukromý koncový bod](../common/storage-private-endpoints.md)a může se připojit z některého z těchto umístění v síti:

- Virtuální síť, kterou nakonfigurujete pro váš účet úložiště. 

  Pro účely tohoto článku odkazujeme na tuto virtuální síť jako na *primární virtuální síť*. Další informace najdete v tématu [udělení přístupu z virtuální sítě](../common/storage-network-security.md#grant-access-from-a-virtual-network).

- Virtuální síť s partnerským vztahem, která je ve stejné oblasti jako primární virtuální síť.

  Abyste povolili přístup k této virtuální síti s partnerským vztahem, budete muset nakonfigurovat svůj účet úložiště. Další informace najdete v tématu [udělení přístupu z virtuální sítě](../common/storage-network-security.md#grant-access-from-a-virtual-network).

- Místní síť, která je připojená k vaší primární virtuální síti pomocí [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) nebo [brány ExpressRoute](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md). 

  Další informace najdete v tématu [Konfigurace přístupu z místních sítí](../common/storage-network-security.md#configuring-access-from-on-premises-networks).

- Místní síť, která je připojená k síti s partnerským vztahem.

  To se dá udělat pomocí [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) nebo [brány ExpressRoute](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md) spolu s [přenosem brány](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering#gateway-transit). 

> [!IMPORTANT]
> Pokud se připojujete z místní sítě, ujistěte se, že váš klient umožňuje odchozí komunikaci prostřednictvím portů 111 a 2048. Protokol NFS 3,0 používá tyto porty.

## <a name="azure-storage-features-not-yet-supported"></a>Azure Storage funkce ještě nejsou podporované.

Následující funkce Azure Storage nejsou podporované, když ve svém účtu povolíte protokol NFS 3,0. 

- Zabezpečení Azure Active Directory (AD)

- Seznamy řízení přístupu (ACL) typu POSIX

- Možnost povolit podporu systému souborů NFS 3,0 u stávajících účtů úložiště

- Možnost zakázat podporu systému souborů NFS 3,0 v účtu úložiště (po jeho povolení)

- Možnost zapisovat do objektů BLOB pomocí rozhraní REST API nebo sad SDK. 
  
## <a name="nfs-30-features-not-yet-supported"></a>Funkce NFS 3,0, které ještě nejsou podporované

U Azure Data Lake Storage Gen2 se zatím nepodporují tyto funkce NFS 3,0.

- NFS 3,0 přes protokol UDP. Podporuje se jenom NFS 3,0 přes TCP.

- Uzamykání souborů pomocí nástroje Network Lock Manager (NLM). Příkazy Mount musí zahrnovat `-o nolock` parametr.

- Připojování dílčích adresářů. Je možné připojit pouze kořenový adresář (kontejner).

- Výpis přípojných (například: pomocí příkazu `showmount -a` )

- Výpis exportů (například: pomocí příkazu `showmount -e` )

- Export kontejneru jako jen pro čtení

## <a name="pricing"></a>Ceny

Ve verzi Preview se data uložená ve vašem účtu úložiště účtují podle stejné míry kapacity, jakou jsou poplatky za úložiště objektů blob za GB za měsíc. 

Během období Preview není účtována transakce. Ceny za transakce se mohou změnit a budou se určovat, kdy je všeobecně dostupná.

## <a name="next-steps"></a>Další kroky

Informace o tom, jak začít, najdete v tématu [připojení úložiště objektů BLOB pomocí protokolu NFS (Network File System) 3,0 (Preview)](network-file-system-protocol-support-how-to.md).