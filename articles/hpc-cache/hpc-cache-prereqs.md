---
title: Předpoklady mezipaměti Azure HPC
description: Předpoklady pro používání mezipaměti Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: rohogue
ms.openlocfilehash: ab342dba5c8be2ff3793c0eb36926969b3e364e5
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537283"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Požadavky pro azure hpc mezipaměť

Než použijete portál Azure k vytvoření nové mezipaměti Azure HPC, ujistěte se, že vaše prostředí splňuje tyto požadavky.

## <a name="azure-subscription"></a>Předplatné Azure

Doporučujeme placené předplatné.

## <a name="network-infrastructure"></a>Síťová infrastruktura

Před použitím mezipaměti je třeba nastavit dva požadavky související se sítí:

* Vyhrazená podsíť pro instanci mezipaměti Azure HPC
* Podpora DNS, aby mohla mezipaměť přistupovat k úložišti a dalším prostředkům

### <a name="cache-subnet"></a>Podsíť mezipaměti

Mezipaměť Azure HPC cache potřebuje vyhrazenou podsíť s těmito vlastnostmi:

* Podsíť musí mít k dispozici alespoň 64 adres IP.
* Podsíť nemůže hostovat žádné jiné virtuální počítače, a to ani pro související služby, jako jsou klientské počítače.
* Pokud používáte více instancí mezipaměti Azure HPC, každá z nich potřebuje vlastní podsíť.

Osvědčeným postupem je vytvořit novou podsíť pro každou mezipaměť. Jako součást vytváření mezipaměti můžete vytvořit novou virtuální síť a podsíť.

### <a name="dns-access"></a>Přístup k DNS

Mezipaměť potřebuje DNS pro přístup k prostředkům mimo svou virtuální síť. V závislosti na tom, které prostředky používáte, může být nutné nastavit přizpůsobený server DNS a nakonfigurovat předávání mezi tímto serverem a servery Azure DNS:

* Pro přístup ke koncovým bodům úložiště objektů blob Azure a dalším interním prostředkům potřebujete server DNS založený na Azure.
* Chcete-li získat přístup k místnímu úložišti, musíte nakonfigurovat vlastní server DNS, který dokáže přeložit názvy hostitelů úložiště.

Pokud potřebujete jenom přístup k úložišti objektů Blob, můžete pro svou mezipaměť použít výchozí server DNS poskytovovaný Službou Azure. Pokud však potřebujete přístup k jiným prostředkům, měli byste vytvořit vlastní server DNS a nakonfigurovat ho tak, aby přesměrovává všechny požadavky na řešení Azure DNS na server Azure DNS.

Jednoduchý server DNS lze také použít k vyrovnávání zatížení klientských připojení mezi všechny dostupné body připojení mezipaměti.

Další informace o virtuálních sítích Azure a konfiguracích DNS serverů najdete v [části Překlad názvů pro prostředky ve virtuálních sítích Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

## <a name="permissions"></a>Oprávnění

Před zahájením vytváření mezipaměti zkontrolujte tyto požadavky související s oprávněními.

* Instance mezipaměti musí být schopna vytvářet virtuální síťová rozhraní (NIC). Uživatel, který vytvoří mezipaměť, musí mít dostatečná oprávnění v předplatném k vytvoření síťové karty.

* Pokud používáte úložiště objektů Blob, azure hpc cache potřebuje autorizaci pro přístup k účtu úložiště. Pomocí řízení přístupu na základě rolí (RBAC) udělit přístup do mezipaměti k úložišti objektů Blob. Jsou vyžadovány dvě role: Přispěvatel účtu úložiště a Datový datový přispěvatel objektů blob úložiště.

  Podle pokynů v [části Přidání cílů úložiště](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) přidejte role.

## <a name="storage-infrastructure"></a>Infrastruktura úložišť

Mezipaměť podporuje kontejnery objektů blob Azure nebo exporty hardwarového úložiště NFS. Po vytvoření mezipaměti přidejte cíle úložiště.

Každý typ úložiště má specifické požadavky.

### <a name="blob-storage-requirements"></a>Požadavky na úložiště objektů blob

Pokud chcete používat úložiště objektů blob Azure s mezipamětí, potřebujete kompatibilní účet úložiště a prázdný kontejner objektů Blob nebo kontejner, který je naplněn daty formátovaný službou Azure HPC Cache, jak je popsáno v části [Přesunout data do úložiště objektů Blob Azure](hpc-cache-ingest.md).

Vytvořte účet před pokusem o přidání cíle úložiště. Nový kontejner můžete vytvořit při přidání cíle.

Chcete-li vytvořit kompatibilní účet úložiště, použijte tato nastavení:

* Výkon: **Standardní**
* Druh účtu: **StorageV2 (pro všeobecné použití v2)**
* Replikace: **Místně redundantní úložiště (LRS)**
* Přístupová úroveň (výchozí): **Hot**

Je vhodné používat účet úložiště ve stejném umístění jako vaše mezipaměť.

Také musíte udělit aplikaci mezipaměti přístup k účtu úložiště Azure, jak je uvedeno v [oprávnění](#permissions), výše. Postupujte podle postupu v [přidat cíle úložiště,](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) aby mezipaměti požadované role přístupu. Pokud nejste vlastníkem účtu úložiště, aby vlastník provést tento krok.

### <a name="nfs-storage-requirements"></a>Požadavky na úložiště systému systému systému
<!-- linked from configuration.md -->

Pokud používáte systém úložiště systému souborů NFS (například místní hardwarový systém NAS), ujistěte se, že splňuje tyto požadavky. Možná budete muset spolupracovat se správci sítě nebo správci brány firewall pro váš úložný systém (nebo datové centrum) k ověření těchto nastavení.

> [!NOTE]
> Vytvoření cíle úložiště se nezdaří, pokud mezipaměť nemá dostatečný přístup k systému úložiště systému souborů NFS.

Další informace jsou zahrnuty v [tématu Poradce při potížích s konfigurací NAS a problémy s cílem úložiště systému NFS](troubleshoot-nas.md).

* **Připojení k síti:** Azure HPC Cache potřebuje přístup k síti s velkou šířkou pásma mezi podsítí mezipaměti a datovým centrem systému souborů NFS. Doporučuje se [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) nebo podobný přístup. Pokud používáte síť VPN, možná ji budete muset nakonfigurovat tak, aby upevňuje protokol TCP MSS na 1350, abyste se ujistili, že nejsou blokovány velké pakety. Další nápovědu k řešení potíží s nastavením sítě VPN naleznete v [omezenívelikosti paketů VPN.](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions)

* **Přístup k přístavu:** Mezipaměť potřebuje přístup ke konkrétním portům TCP/UDP v systému úložiště. Různé typy úložišť mají různé požadavky na porty.

  Chcete-li zkontrolovat nastavení úložného systému, postupujte podle tohoto postupu.

  * Vydejte `rpcinfo` příkaz do systému úložiště a zkontrolujte potřebné porty. Následující příkaz uvádí porty a formátuje příslušné výsledky v tabulce. (Místo *<storage_IP>* termín použijte IP adresu vašeho systému.)

    Tento příkaz můžete vydat z libovolného klienta Linuxu, který má nainstalovanou infrastrukturu systému NFS. Pokud používáte klienta uvnitř podsítě clusteru, může také pomoci ověřit připojení mezi podsítí a systémem úložiště.

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  Ujistěte se, že všechny ``rpcinfo`` porty vrácené dotazem umožňují neomezený provoz z podsítě mezipaměti Azure HPC.

  * Pokud `rpcinfo` příkaz nemůžete použít, ujistěte se, že tyto běžně používané porty umožňují příchozí a odchozí přenosy:

    | Protocol (Protokol) | Port  | Služba  |
    |----------|-------|----------|
    | Protokol TCP/UDP  | 111   | rpcbind  |
    | Protokol TCP/UDP  | 2049  | NFS      |
    | Protokol TCP/UDP  | 4045  | nlockmgr řekl: |
    | Protokol TCP/UDP  | 4046  | připnout   |
    | Protokol TCP/UDP  | 4047  | status   |

    Některé systémy používají pro tyto služby různá čísla portů – pro jistotu se obraťte na dokumentaci systému úložiště.

  * Zkontrolujte nastavení brány firewall, abyste se ujistili, že umožňují provoz na všech těchto požadovaných portech. Zkontrolujte brány firewall používané v Azure i místní brány firewall ve vašem datovém centru.

* **Přístup k adresáři:** Povolte `showmount` příkaz v systému úložiště. Azure HPC Cache používá tento příkaz ke kontrole, že vaše konfigurace cíle úložiště odkazuje na platný export a také ujistěte se, že více připojení nemají přístup ke stejným podadresářům (riziko kolize souborů).

  > [!NOTE]
  > Pokud váš systém úložiště systému souborů NFS používá operační systém NetTap 9.2, **nepovolujte `showmount` **program . [Požádejte o pomoc službu a podporu společnosti Microsoft.](hpc-cache-support-ticket.md)

  Další informace o přístupu k seznamu adresářů naleznete v [článku o řešení potíží s úložištěm systému](troubleshoot-nas.md#enable-export-listing)souborů NFS .

* **Root přístup** (čtení a zápis): Mezipaměť se připojí k back-end systému jako ID uživatele 0. Zkontrolujte tato nastavení v úložném systému:
  
  * Povolit `no_root_squash`. Tato možnost zajišťuje, že vzdálený kořenový uživatel může přistupovat k souborům vlastněným root.

  * Zkontrolujte zásady exportu a ujistěte se, že neobsahují omezení přístupu ke kořenům z podsítě mezipaměti.

  * Pokud vaše úložiště obsahuje exporty, které jsou podadresáři jiného exportu, ujistěte se, že mezipaměť má kořenový přístup k nejnižšímu segmentu cesty. Podrobnosti najdete [v článku](troubleshoot-nas.md#allow-root-access-on-directory-paths) pro řešení potíží s úložištěm systému souborů NFS.

* Back-end ové úložiště systému NFS musí být kompatibilní hardwarovou/softwarovou platformou. Podrobnosti vám poskytne tým Azure HPC Cache.

## <a name="next-steps"></a>Další kroky

* [Vytvoření instance mezipaměti Azure HPC](hpc-cache-create.md) z webu Azure Portal
