---
title: Podpora systému souborů sítě 3,0 ve službě Azure Blob Storage (Preview) | Microsoft Docs
description: BLOB Storage teď podporuje protokol NFS (Network File System) 3,0. Tato podpora umožňuje klientům Linux připojit kontejner ve službě BLOB Storage z virtuálního počítače Azure nebo z počítače, který běží místně.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 02/19/2021
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: b00956a6fb5a79b09602ca1752cc547595f32db6
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2021
ms.locfileid: "103224590"
---
# <a name="network-file-system-nfs-30-protocol-support-in-azure-blob-storage-preview"></a>Podpora protokolů systému souborů NFS (Network File System) 3,0 v úložišti objektů BLOB v Azure (Preview)

BLOB Storage teď podporuje protokol NFS (Network File System) 3,0. Tato podpora zajišťuje kompatibilitu systému souborů Linux v rozsahu úložiště a cenách objektů a umožňuje klientům Linux připojit kontejner v úložišti objektů BLOB z virtuálního počítače Azure nebo místního počítače. 

> [!NOTE]
> Podpora protokolu NFS 3,0 ve službě Azure Blob Storage je ve verzi Public Preview. Podporuje GPV2 účty úložiště s výkonem úrovně Standard v následujících oblastech: Austrálie – východ, Korea – střed a Střed USA – jih. Verze Preview také podporuje objekty blob bloku s úrovní výkonu Premium ve všech veřejných oblastech.

Vždycky se jedná o výzvu ke spouštění rozsáhlých zastaralých úloh, jako je například prostředí HPC (High Performance Computing) v cloudu. Jedním z důvodů je, že aplikace často pro přístup k datům používají tradiční protokoly souborů, jako je třeba NFS nebo SMB (Server Message Block). Nativní služby cloudového úložiště zaměřené na úložiště objektů, které mají plochý obor názvů a rozsáhlá metadata, místo systémů souborů, které poskytují hierarchický obor názvů a efektivní operace s metadaty. 

Blob Storage teď podporuje hierarchický obor názvů a v kombinaci s podporou systému souborů NFS 3,0 podporuje Azure mnohem jednodušší spouštění starších aplikací nad rozsáhlým cloudovým úložištěm objektů. 

## <a name="applications-and-workloads-suited-for-this-feature"></a>Aplikace a úlohy, které jsou pro tuto funkci vhodné

Funkce protokolu NFS 3,0 se nejlépe hodí pro zpracování vysoké propustnosti, vysokého rozsahu, čtení těžkých úloh, jako je zpracování médií, simulace rizik a řazení genomiky. Měli byste zvážit použití této funkce pro jakýkoli jiný typ úlohy, která používá více čtenářů a mnoho vláken, což vyžaduje velkou šířku pásma. 

## <a name="nfs-30-and-the-hierarchical-namespace"></a>NFS 3,0 a hierarchický obor názvů

Podpora protokolu NFS 3,0 vyžaduje, aby objekty blob byly uspořádány do hierarchického oboru názvů. Při vytváření účtu úložiště můžete povolit hierarchický obor názvů. Možnost použít hierarchický obor názvů byla představena Azure Data Lake Storage Gen2. Uspořádává objekty (soubory) do hierarchie adresářů a podadresářů stejným způsobem, jakým je uspořádán systém souborů v počítači.  Hierarchický obor názvů se škáluje lineárně a nesnižuje datovou kapacitu ani výkon. Různé protokoly přesahují z hierarchického oboru názvů. Protokol NFS 3,0 je jedním z těchto dostupných protokolů.   

> [!div class="mx-imgBorder"]
> ![hierarchický obor názvů](./media/network-protocol-support/hierarchical-namespace-and-nfs-support.png)
  
## <a name="data-stored-as-block-blobs"></a>Data uložená jako objekty blob bloku

Pokud povolíte podporu protokolu NFS 3,0, všechna data v účtu úložiště se uloží jako objekty blob bloku. Objekty blob bloku jsou optimalizované k efektivnímu zpracování velkých objemů dat, která jsou těžká pro čtení. Objekty blob bloku se skládají z bloků. Každý blok je identifikovaný ID bloku. Objekt blob bloku může zahrnovat až 50 000 bloků. Každý blok v objektu blob bloku může mít různou velikost až do maximální velikosti, která je povolená pro verzi služby, kterou používá váš účet.

Když vaše aplikace odešle požadavek pomocí protokolu NFS 3,0, tento požadavek se převede na kombinaci operací objektů blob bloku. Například požadavky NFS 3,0 čtení vzdáleného volání procedur (RPC) jsou přeloženy do operace [Get objektu BLOB](/rest/api/storageservices/get-blob) . Požadavky na zápis protokolu RPC pro systém souborů NFS 3,0 jsou přeloženy do kombinace seznamu [blokovaných bloků](/rest/api/storageservices/get-block-list), [vložení bloku](/rest/api/storageservices/put-block)a [seznamu blokovaných bloků](/rest/api/storageservices/put-block-list).

## <a name="general-workflow-mounting-a-storage-account-container"></a>Obecný pracovní postup: připojení kontejneru účtu úložiště

Klienti se systémem Linux mohou připojit kontejner ve službě BLOB Storage z virtuálního počítače Azure nebo místního počítače. Pokud chcete připojit kontejner účtu úložiště, musíte provést tyto akce.

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

  V tomto článku odkazujeme na tuto virtuální síť jako na *primární virtuální síť*. Další informace najdete v tématu [udělení přístupu z virtuální sítě](../common/storage-network-security.md#grant-access-from-a-virtual-network).

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

Následující funkce NFS 3,0 ještě nejsou podporované.

- NFS 3,0 přes protokol UDP. Podporuje se jenom NFS 3,0 přes TCP.

- Uzamykání souborů pomocí nástroje Network Lock Manager (NLM). Příkazy Mount musí zahrnovat `-o nolock` parametr.

- Připojování podadresářů. Je možné připojit pouze kořenový adresář (kontejner).

- Výpis přípojných (například: pomocí příkazu `showmount -a` )

- Výpis exportů (například: pomocí příkazu `showmount -e` )

- Pevné propojení

- Export kontejneru jako jen pro čtení

## <a name="nfs-30-clients-not-yet-supported"></a>Klienti NFS 3,0 ještě nejsou podporované

Následující klienti NFS 3,0 nejsou ještě podporováni.

- Klient Windows pro systém souborů NFS

## <a name="pricing"></a>Ceny

Ve verzi Preview se data uložená ve vašem účtu úložiště účtují podle stejné míry kapacity, jakou jsou poplatky za úložiště objektů blob za GB za měsíc. 

Během období Preview není účtována transakce. Ceny za transakce se mohou změnit a budou se určovat, kdy je všeobecně dostupná.

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak začít, najdete v tématu [připojení úložiště objektů BLOB pomocí protokolu NFS (Network File System) 3,0 (Preview)](network-file-system-protocol-support-how-to.md).

- Informace o optimalizaci výkonu najdete [v tématu požadavky na výkon systému souborů NFS (Network File System) 3,0 ve službě Azure Blob Storage (Preview)](network-file-system-protocol-support-performance.md).
