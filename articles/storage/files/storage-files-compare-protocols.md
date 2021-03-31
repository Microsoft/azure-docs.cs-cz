---
title: Dostupné protokoly souborů Azure – systém souborů NFS a SMB
description: Před vytvořením sdílené složky Azure, včetně protokolu SMB (Server Message Block) a systému souborů NFS (Network File System), se dozvíte o dostupných protokolech.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 0ed41cc01fcf5aa35b3d2195518b8e2bb0f3b9c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104588235"
---
# <a name="azure-file-share-protocols"></a>Protokoly sdílení souborů Azure

Soubory Azure nabízí dva protokoly pro připojení a připojení ke sdíleným složkám Azure. [Protokol SMB (Server Message Block)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) a [protokol NFS (Network File System](https://en.wikipedia.org/wiki/Network_File_System) ) (Preview). Služba soubory Azure v současné době nepodporuje přístup s více protokoly, takže sdílená složka může být pouze sdílená složka systému souborů NFS nebo sdílená složka protokolu SMB. Z tohoto důvodu doporučujeme určit, který protokol nejlépe vyhovuje vašim potřebám před vytvořením sdílených složek Azure.

## <a name="differences-at-a-glance"></a>Rozdíly na první pohled

|Funkce  |NFS (Preview)  |SMB  |
|---------|---------|---------|
|Přístup k protokolům     |SYSTÉM SOUBORŮ NFS 4,1         |SMB 2,1, SMB 3,0         |
|Doporučený operační systém     |Jádro Linux verze 4.3 +         |Windows 2008 R2 +, jádro Linux verze 4.11 +         |
|[Dostupné úrovně](storage-files-planning.md#storage-tiers)     |Premium Storage         |Premium Storage, transakce optimalizovaná, horká, studená         |
|Model fakturace         |[Plaťte za zřízenou kapacitu](./understanding-billing.md#provisioned-model)         |[Platíte za zřízenou kapacitu pro úroveň Premium](./understanding-billing.md#provisioned-model), průběžné [platby pro úroveň Standard](./understanding-billing.md#pay-as-you-go-model)         |
|[Redundance](storage-files-planning.md#redundancy)     |LRS, ZRS         |LRS, ZRS, GRS         |
|Authentication     |Pouze ověřování založené na hostiteli        |Ověřování na základě identity, ověřování uživatelů         |
|Oprávnění     |Oprávnění ve stylu systému UNIX         |Oprávnění ve stylu NTFS         |
|Sémantika systému souborů     |Kompatibilní s POSIX         |Není kompatibilní se standardem POSIX         |
|Rozlišovat velká a malá písmena     |Malá a velká písmena se rozlišují.         |Nerozlišuje velká a malá písmena         |
|Podpora pevných odkazů     |Podporováno         |Nepodporováno         |
|Podpora symbolických odkazů     |Podporováno         |Nepodporováno         |
|Odstraňování a úpravy otevřených souborů     |Podporováno         |Nepodporováno         |
|Uzamčení     |Správce zámků síťových zámků v bajtovém rozsahu         |Podporováno         |
|Seznam bezpečných veřejných IP adres | Nepodporováno | Podporováno|
|Komunikace protokolu| Nepodporováno | REST|

## <a name="nfs-shares-preview"></a>Sdílené složky NFS (Preview)

Připojení sdílených složek Azure se systémem souborů NFS 4,1 je momentálně ve verzi Preview. Nabízí užší integraci se systémem Linux. Jedná se o úplnou nabídku kompatibilní se standardem POSIX, která je standardem v různých variantách systému UNIX a dalších * operačních systémech založených na NIX. Tato služba úložiště souborů na podnikové úrovni škáluje až do splnění potřeb úložiště a dá se k nim přistupoval souběžně tisícům výpočetních instancí.

### <a name="limitations"></a>Omezení

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

#### <a name="regional-availability"></a>Regionální dostupnost

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

### <a name="best-suited"></a>Nejlépe vyhovující

NFS se soubory Azure je ideální pro:

- Úlohy, které vyžadují sdílené složky kompatibilní se standardem POSIX, rozlišování velkých a malých písmen nebo oprávnění ke stylům UNIX (UID/GID).
- Úlohy zaměřené na Linux, které nevyžadují přístup k Windows.

### <a name="security"></a>Zabezpečení

Všechna data souborů Azure jsou v klidovém stavu zašifrovaná. Pro šifrování při přenosu poskytuje Azure vrstvu šifrování pro všechna data při přenosu mezi datacentry Azure pomocí [MACSec](https://en.wikipedia.org/wiki/IEEE_802.1AE). V takovém případě existují při přenosu dat mezi datacentry Azure šifrování. Na rozdíl od souborů Azure, které používají protokol SMB, nenabízí sdílení souborů pomocí protokolu NFS ověřování na základě uživatele. Ověřování pro sdílené složky NFS vychází z nakonfigurovaných pravidel zabezpečení sítě. Vzhledem k tomu, že pro sdílenou složku systému souborů NFS jsou navázána pouze zabezpečená připojení, je nutné použít koncové body služby nebo privátní koncové body. Pokud chcete ke sdíleným složkám přistupovat z místního prostředí, musíte kromě privátního koncového bodu nastavit síť VPN nebo ExpressRoute. Žádosti, které nepocházejí z následujících zdrojů, se odmítnou:

- [Soukromý koncový bod](storage-files-networking-overview.md#private-endpoints)
- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
    - [SÍŤ VPN typu Point-to-Site (P2S)](../../vpn-gateway/point-to-site-about.md)
    - [Site-to-site](../../vpn-gateway/design.md#s2smulti)
- [ExpressRoute](../../expressroute/expressroute-introduction.md)
- [Omezený veřejný koncový bod](storage-files-networking-overview.md#storage-account-firewall-settings)

Další informace o dostupných možnostech sítě najdete v tématu věnovaném [důležitým informacím o sítích Azure Files](storage-files-networking-overview.md).

## <a name="smb-shares"></a>Sdílené složky SMB

Sdílené složky Azure připojené k protokolu SMB nabízejí více funkcí souborů Azure a nemají žádná omezení funkcí Azure Files, protože jsou všeobecně dostupné.

### <a name="features"></a>Funkce

- Azure File Sync
- Ověřování na základě identity
- Podpora Azure Backup
- Snímky
- Obnovitelné odstranění
- Šifrování a zašifrování v klidovém režimu

### <a name="best-suited"></a>Nejlépe vyhovující

Protokol SMB se soubory Azure je ideální pro:

- Produkční prostředí
- Zákazníci, kteří vyžadují některou z funkcí uvedených v části [funkce](#features)

## <a name="next-steps"></a>Další kroky

- [Vytvoření sdílené složky NFS](storage-files-how-to-create-nfs-shares.md)
- [Vytvoření sdílené složky SMB](storage-how-to-create-file-share.md)