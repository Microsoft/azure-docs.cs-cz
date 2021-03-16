---
title: Požadavky na mezipaměť prostředí Azure HPC
description: Předpoklady pro použití mezipaměti HPC Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/11/2021
ms.author: v-erkel
ms.openlocfilehash: 7a91cf5f9341d2b42f1c8f242d288b4ee59b632d
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/15/2021
ms.locfileid: "103471795"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Předpoklady pro mezipaměť Azure HPC

Než použijete Azure Portal k vytvoření nové mezipaměti prostředí Azure HPC, ujistěte se, že vaše prostředí splňuje tyto požadavky.

## <a name="video-overviews"></a>Přehledy videí

Podívejte se na tato videa a Získejte rychlý přehled o komponentách systému a o tom, co potřebují k vzájemné spolupráci.

(Klikněte na obrázek videa nebo na odkaz pro sledování.)

* [Jak to funguje](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/) – vysvětluje, jak funguje mezipaměť prostředí Azure HPC s úložištěm a klienty.

  [![obrázek miniatury videa: Azure HPC cache: Jak funguje (kliknutím navštívíte stránku video)](media/video-2-components.png)](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/)  

* [Požadavky](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/) – popisuje požadavky pro úložiště NAS, úložiště objektů BLOB v Azure, přístup k síti a klientský přístup.

  [![obrázek miniatury videa: předpoklady Azure HPC cache (kliknutím navštívíte stránku video)](media/video-3-prerequisites.png)](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/)

V části tohoto článku najdete konkrétní doporučení.

## <a name="azure-subscription"></a>Předplatné Azure

Doporučuje se placené předplatné.

## <a name="network-infrastructure"></a>Síťová infrastruktura

Předtím, než budete moci použít mezipaměť, by měly být nastavené dvě požadavky týkající se sítě:

* Vyhrazená podsíť pro instanci mezipaměti Azure HPC
* Podpora DNS, aby mohla mezipaměť získat přístup k úložišti a dalším prostředkům

### <a name="cache-subnet"></a>Podsíť mezipaměti

Mezipaměť prostředí Azure HPC potřebuje vyhrazenou podsíť s těmito kvalitou:

* Podsíť musí mít k dispozici minimálně 64 IP adres.
* Podsíť nemůže hostovat žádné jiné virtuální počítače, a to ani pro související služby, jako jsou klientské počítače.
* Pokud používáte více instancí Azure HPC cache, každá z nich potřebuje vlastní podsíť.

Osvědčeným postupem je vytvořit novou podsíť pro každou mezipaměť. V rámci vytváření mezipaměti můžete vytvořit novou virtuální síť a podsíť.

### <a name="dns-access"></a>Přístup DNS

Mezipaměť potřebuje DNS pro přístup k prostředkům mimo svou virtuální síť. V závislosti na tom, jaké prostředky používáte, možná budete muset nastavit vlastní server DNS a nakonfigurovat přesměrování mezi tímto serverem a Azure DNS servery:

* Pro přístup k koncovým bodům služby Azure Blob Storage a dalším interním prostředkům budete potřebovat server DNS založený na Azure.
* Pokud chcete získat přístup k místnímu úložišti, musíte nakonfigurovat vlastní server DNS, který dokáže přeložit názvy hostitelů úložiště. Tuto možnost je nutné provést **před** vytvořením mezipaměti.

Pokud potřebujete jenom přístup k úložišti objektů blob, můžete pro svou mezipaměť použít výchozí server DNS určený pro Azure. Pokud ale potřebujete přístup k jiným prostředkům, měli byste vytvořit vlastní server DNS a nakonfigurovat ho tak, aby přenesl všechny požadavky na rozlišení specifické pro Azure na Azure DNS Server.

Chcete-li použít vlastní server DNS, musíte provést tyto kroky instalace před vytvořením mezipaměti:

* Vytvořte virtuální síť, která bude hostovat mezipaměť prostředí Azure HPC.
* Vytvořte server DNS.
* Přidejte server DNS do virtuální sítě mezipaměti.

  Pomocí těchto kroků přidejte server DNS do virtuální sítě v Azure Portal:

  1. Otevřete virtuální síť v Azure Portal.
  1. V nabídce **Nastavení** na bočním panelu vyberte **servery DNS** .
  1. Vybrat **vlastní**
  1. Do pole zadejte IP adresu serveru DNS.

K vyrovnávání zatížení připojení klientů mezi všemi dostupnými přípojnými body mezipaměti se dá použít taky jednoduchý server DNS.

Přečtěte si další informace o virtuálních sítích Azure a konfiguracích serverů DNS v [překladu názvů pro prostředky v Azure Virtual Networks](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="permissions"></a>Oprávnění

Než začnete vytvářet mezipaměť, ověřte tyto požadavky týkající se oprávnění.

* Instance mezipaměti musí být schopná vytvářet rozhraní virtuální sítě (nic). Uživatel, který vytváří mezipaměť, musí mít v předplatném dostatečná oprávnění pro vytváření síťových adaptérů.

* Pokud používáte službu BLOB Storage, mezipaměť prostředí Azure HPC potřebuje autorizaci pro přístup k vašemu účtu úložiště. Pomocí řízení přístupu na základě role Azure (Azure RBAC) udělte mezipaměti přístup k úložišti objektů BLOB. Jsou vyžadovány dvě role: Přispěvatel účtu úložiště a přispěvatel dat objektu BLOB úložiště.

  Pokud chcete přidat role, postupujte podle pokynů v části [Přidání cílů úložiště](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) .

## <a name="storage-infrastructure"></a>Infrastruktura úložiště
<!-- heading is linked in create storage target GUI as aka.ms/hpc-cache-prereq#storage-infrastructure - make sure to fix that if you change the wording of this heading -->

Mezipaměť podporuje kontejnery objektů blob Azure, exporty hardwarového úložiště NFS a kontejnery objektů BLOB ADLS připojené k systému souborů NFS (aktuálně ve verzi Preview). Po vytvoření mezipaměti přidejte cíle úložiště.

Každý typ úložiště má specifické požadavky.

### <a name="blob-storage-requirements"></a>Požadavky na úložiště objektů BLOB

Pokud chcete používat úložiště objektů BLOB v Azure s mezipamětí, potřebujete kompatibilní účet úložiště a prázdný kontejner objektů BLOB nebo kontejner, který je naplněný daty ve formátu mezipaměti HPC Azure, jak je popsáno v tématu [přesun dat do služby Azure Blob Storage](hpc-cache-ingest.md).

> [!NOTE]
> Jiné požadavky platí pro úložiště objektů BLOB připojené k systému souborů NFS. Podrobnosti najdete v článku [požadavky na úložiště adls-NFS](#nfs-mounted-blob-adls-nfs-storage-requirements-preview) .

Vytvořte účet před tím, než se pokusíte přidat cíl úložiště. Když přidáte cíl, můžete vytvořit nový kontejner.

Pokud chcete vytvořit kompatibilní účet úložiště, použijte Tato nastavení:

* Výkon: **Standard**
* Druh účtu: **StorageV2 (pro obecné účely v2)**
* Replikace: **místně redundantní úložiště (LRS)**
* Úroveň přístupu (výchozí): **Hot**

Je vhodné použít účet úložiště ve stejném umístění jako mezipaměť.

K vašemu účtu služby Azure Storage musíte taky udělit přístup k aplikaci cache, jak je uvedeno výše v části [oprávnění](#permissions). Postupujte podle pokynů v části [Přidání cílů úložiště](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) a poskytněte mezipaměti požadované role přístupu. Pokud nejste vlastníkem účtu úložiště, udělejte tohoto kroku vlastník.

### <a name="nfs-storage-requirements"></a>Požadavky na úložiště NFS
<!-- linked from configuration.md -->

Pokud používáte úložný systém NFS (například místní hardwarový systém NAS), ujistěte se, že splňuje tyto požadavky. Abyste mohli ověřit tato nastavení, možná budete muset spolupracovat s správci sítě nebo správci brány firewall pro váš systém úložiště (nebo datové centrum).

> [!NOTE]
> Vytvoření cíle úložiště se nezdaří, pokud mezipaměť nemá dostatečný přístup k systému úložiště NFS.

Další informace najdete v tématu [řešení problémů s cílovým úložištěm a konfigurací serveru pro systém souborů NFS](troubleshoot-nas.md).

* **Připojení k síti:** Mezipaměť prostředí Azure HPC vyžaduje síťový přístup s vysokou šířkou pásma mezi podsítí mezipaměti a datovým centrem systému souborů NFS. Doporučuje se [ExpressRoute](../expressroute/index.yml) nebo podobný přístup. Pokud používáte síť VPN, může být potřeba ji nakonfigurovat tak, aby se zablokovala TCP MSS v 1350, aby se zajistilo, že nebudou zablokované Velké pakety. Další pomoc při řešení potíží s nastavením sítě VPN najdete v tématu [omezení velikosti paketů sítě VPN](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) .

* **Přístup k portu:** Mezipaměť potřebuje přístup ke konkrétním portům TCP/UDP v systému úložiště. Různé typy úložiště mají různé požadavky na porty.

  Pokud chcete ověřit nastavení systému úložiště, postupujte podle tohoto postupu.

  * `rpcinfo`Pro kontrolu potřebných portů vydejte příkaz do systému úložiště. Následující příkaz vypíše porty a naformátuje příslušné výsledky v tabulce. (Použijte IP adresu vašeho systému místo *<storage_IP>* termínu.)

    Tento příkaz můžete vydat ze všech klientů se systémem Linux, na kterých je nainstalována infrastruktura systému souborů NFS. Pokud používáte klienta v podsíti clusteru, může vám také pomáhat ověřit připojení mezi podsítí a systémem úložiště.

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  Zajistěte, aby všechny porty vrácené ``rpcinfo`` dotazem umožňovaly neomezený provoz z podsítě mezipaměti HPC Azure.

  * Pokud nemůžete použít `rpcinfo` příkaz, ujistěte se, že tyto běžně používané porty umožňují příchozí a odchozí provoz:

    | Protokol | Port  | Služba  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr |
    | TCP/UDP  | 4046  | připojeno   |
    | TCP/UDP  | 4047  | status   |

    Některé systémy používají pro tyto služby různá čísla portů – Prohlédněte si dokumentaci k systému úložiště, abyste si je jisti.

  * Zkontrolujte nastavení brány firewall a ujistěte se, že jsou povolené přenosy na všech těchto požadovaných portech. Nezapomeňte zkontrolovat brány firewall používané v Azure a také místní brány firewall ve vašem datovém centru.

* **Přístup k adresáři:** Povolte `showmount` příkaz v systému úložiště. Azure HPC Cache používá tento příkaz ke kontrole, zda konfigurace cíle úložiště odkazuje na platný export, a také k tomu, aby se zajistilo, že více připojení nemá přístup ke stejným podadresářům (riziko kolizí souborů).

  > [!NOTE]
  > Pokud váš systém úložiště NFS používá operační systém ONTAP 9,2 NetApp, **nepovolujte `showmount`** ho. Pro pomoc [se obraťte na službu a podporu společnosti Microsoft](hpc-cache-support-ticket.md) .

  Další informace o přístupu k výpisu adresářů najdete v [článku věnovaném řešení potíží s](troubleshoot-nas.md#enable-export-listing)cílovým úložištěm NFS.

* **Root Access** (čtení a zápis): mezipaměť se připojuje k back-endovému systému jako ID uživatele 0. Ověřte tato nastavení v systému úložiště:
  
  * Povolit `no_root_squash` . Tato možnost zajistí, že uživatel vzdáleného kořenového uživatele bude moci přistupovat k souborům vlastněných kořenem.

  * Zaškrtněte políčka Exportovat zásady a ujistěte se, že neobsahují omezení přístupu rootu z podsítě mezipaměti.

  * Pokud má vaše úložiště nějaké exporty, které jsou podadresáři jiného exportu, ujistěte se, že má mezipaměť kořenový přístup k nejnižšímu segmentu cesty. Podrobnosti najdete v článku věnovaném [přístupu ke kořenu na cestách k adresáři](troubleshoot-nas.md#allow-root-access-on-directory-paths) v tématu řešení potíží s cílovým ÚLOŽIŠTĚm NFS

* Back-end úložiště NFS musí být kompatibilní hardwarová a softwarová platforma. Podrobnosti získáte od týmu Azure HPC cache.

### <a name="nfs-mounted-blob-adls-nfs-storage-requirements-preview"></a>Požadavky na úložiště namontovaného objektu BLOB (ADLS-NFS) NFS (verze PREVIEW)

Mezipaměť HPC Azure taky může použít kontejner objektů BLOB připojený k protokolu NFS jako cíl úložiště.

> [!NOTE]
> Podpora protokolu NFS 3,0 pro Azure Blob Storage je ve verzi Public Preview. Dostupnost je omezená a funkce se můžou v současnosti měnit a když je funkce všeobecně dostupná. V produkčních systémech Nepoužívejte technologii verze Preview.
>
> Přečtěte si další informace o této funkci ve verzi Preview v tématu [Podpora protokolů NFS 3,0 ve službě Azure Blob Storage](../storage/blobs/network-file-system-protocol-support.md).

Požadavky na účet úložiště se liší pro cíl úložiště objektů BLOB ADLS-NFS a pro standardní cíl úložiště BLOB. Pokud chcete vytvořit a nakonfigurovat účet úložiště s povoleným systémem souborů NFS, postupujte podle pokynů v části [připojení úložiště objektů BLOB pomocí protokolu NFS (Network File System) 3,0](../storage/blobs/network-file-system-protocol-support-how-to.md) .

Toto je obecný přehled kroků:

1. Ujistěte se, že funkce, které potřebujete, jsou k dispozici v oblastech, kde plánujete pracovat.

1. Povolte funkci protokolu NFS pro vaše předplatné. Provedete to *ještě před* vytvořením účtu úložiště.

1. Vytvořte zabezpečenou virtuální síť (VNet) pro účet úložiště. Pro účet úložiště s povoleným systémem souborů NFS a pro mezipaměť HPC Azure byste měli použít stejnou virtuální síť.

1. Vytvořte účet úložiště.

   * Místo použití nastavení účtu úložiště pro standardní účet úložiště BLOB postupujte podle pokynů v [dokumentu s postupy](../storage/blobs/network-file-system-protocol-support-how-to.md). Typ podporovaného účtu úložiště se může lišit podle oblasti Azure.

   * V části **sítě** vyberte privátní koncový bod v zabezpečené virtuální síti, kterou jste vytvořili (doporučeno), nebo vyberte veřejný koncový bod s omezeným přístupem z zabezpečené virtuální sítě.

   * Nezapomeňte dokončit oddíl **Upřesnit** , kde povolíte přístup k systému souborů NFS.

   * Poskytněte aplikaci cache přístup k vašemu účtu služby Azure Storage, jak je uvedeno v [oprávněních](#permissions)výše. Můžete to udělat při prvním vytvoření cíle úložiště. Postupujte podle pokynů v části [Přidání cílů úložiště](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) a poskytněte mezipaměti požadované role přístupu.

     Pokud nejste vlastníkem účtu úložiště, udělejte tohoto kroku vlastník.

## <a name="set-up-azure-cli-access-optional"></a>Nastavení přístupu přes rozhraní příkazového řádku Azure (volitelné)

Pokud chcete vytvořit nebo spravovat mezipaměť prostředí Azure HPC z rozhraní příkazového řádku Azure (CLI), musíte nainstalovat software CLI a rozšíření mezipaměti HPC. Postupujte podle pokynů v části [Nastavení Azure CLI pro mezipaměť HPC Azure](az-cli-prerequisites.md).

## <a name="next-steps"></a>Další kroky

* [Vytvoření instance mezipaměti prostředí Azure HPC](hpc-cache-create.md) z Azure Portal
