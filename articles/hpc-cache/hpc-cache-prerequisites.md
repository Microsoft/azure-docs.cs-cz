---
title: Požadavky na mezipaměť prostředí Azure HPC
description: Předpoklady pro použití mezipaměti HPC Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/03/2020
ms.author: v-erkel
ms.openlocfilehash: 9454dd8d1d6648396980f5148384d2e0119e0dab
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91612978"
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
* Pokud chcete získat přístup k místnímu úložišti, musíte nakonfigurovat vlastní server DNS, který dokáže přeložit názvy hostitelů úložiště.

Pokud potřebujete jenom přístup k úložišti objektů blob, můžete pro svou mezipaměť použít výchozí server DNS určený pro Azure. Pokud ale potřebujete přístup k jiným prostředkům, měli byste vytvořit vlastní server DNS a nakonfigurovat ho tak, aby přenesl všechny požadavky na rozlišení specifické pro Azure na Azure DNS Server.

K vyrovnávání zatížení připojení klientů mezi všemi dostupnými přípojnými body mezipaměti se dá použít taky jednoduchý server DNS.

Přečtěte si další informace o virtuálních sítích Azure a konfiguracích serverů DNS v [překladu názvů pro prostředky v Azure Virtual Networks](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

## <a name="permissions"></a>Oprávnění

Než začnete vytvářet mezipaměť, ověřte tyto požadavky týkající se oprávnění.

* Instance mezipaměti musí být schopná vytvářet rozhraní virtuální sítě (nic). Uživatel, který vytváří mezipaměť, musí mít v předplatném dostatečná oprávnění pro vytváření síťových adaptérů.

* Pokud používáte službu BLOB Storage, mezipaměť prostředí Azure HPC potřebuje autorizaci pro přístup k vašemu účtu úložiště. Pomocí řízení přístupu na základě role (RBAC) udělte mezipaměti přístup k úložišti objektů BLOB. Jsou vyžadovány dvě role: Přispěvatel účtu úložiště a přispěvatel dat objektu BLOB úložiště.

  Pokud chcete přidat role, postupujte podle pokynů v části [Přidání cílů úložiště](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) .

## <a name="storage-infrastructure"></a>Infrastruktura úložiště

Mezipaměť podporuje exporty do kontejnerů objektů blob Azure nebo pro hardwarové úložiště NFS. Po vytvoření mezipaměti přidejte cíle úložiště.

Každý typ úložiště má specifické požadavky.

### <a name="blob-storage-requirements"></a>Požadavky na úložiště objektů BLOB

Pokud chcete používat úložiště objektů BLOB v Azure s mezipamětí, potřebujete kompatibilní účet úložiště a prázdný kontejner objektů BLOB nebo kontejner, který je naplněný daty ve formátu mezipaměti HPC Azure, jak je popsáno v tématu [přesun dat do služby Azure Blob Storage](hpc-cache-ingest.md).

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

* **Připojení k síti:** Mezipaměť prostředí Azure HPC vyžaduje síťový přístup s vysokou šířkou pásma mezi podsítí mezipaměti a datovým centrem systému souborů NFS. Doporučuje se [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) nebo podobný přístup. Pokud používáte síť VPN, může být potřeba ji nakonfigurovat tak, aby se zablokovala TCP MSS v 1350, aby se zajistilo, že nebudou zablokované Velké pakety. Další pomoc při řešení potíží s nastavením sítě VPN najdete v tématu [omezení velikosti paketů sítě VPN](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) .

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
  > Pokud váš systém úložiště NFS používá operační systém ONTAP 9,2 NetApp, **nepovolujte `showmount` **ho. Pro pomoc [se obraťte na službu a podporu společnosti Microsoft](hpc-cache-support-ticket.md) .

  Další informace o přístupu k výpisu adresářů najdete v [článku věnovaném řešení potíží s](troubleshoot-nas.md#enable-export-listing)cílovým úložištěm NFS.

* **Root Access** (čtení a zápis): mezipaměť se připojuje k back-endovému systému jako ID uživatele 0. Ověřte tato nastavení v systému úložiště:
  
  * Povolit `no_root_squash` . Tato možnost zajistí, že uživatel vzdáleného kořenového uživatele bude moci přistupovat k souborům vlastněných kořenem.

  * Zaškrtněte políčka Exportovat zásady a ujistěte se, že neobsahují omezení přístupu rootu z podsítě mezipaměti.

  * Pokud má vaše úložiště nějaké exporty, které jsou podadresáři jiného exportu, ujistěte se, že má mezipaměť kořenový přístup k nejnižšímu segmentu cesty. Podrobnosti najdete v článku věnovaném [přístupu ke kořenu na cestách k adresáři](troubleshoot-nas.md#allow-root-access-on-directory-paths) v tématu řešení potíží s cílovým ÚLOŽIŠTĚm NFS

* Back-end úložiště NFS musí být kompatibilní hardwarová a softwarová platforma. Podrobnosti získáte od týmu Azure HPC cache.

## <a name="set-up-azure-cli-access-optional"></a>Nastavení přístupu přes rozhraní příkazového řádku Azure (volitelné)

Pokud chcete vytvořit nebo spravovat mezipaměť prostředí Azure HPC z rozhraní příkazového řádku Azure (CLI), musíte nainstalovat software CLI a rozšíření mezipaměti HPC. Postupujte podle pokynů v části [Nastavení Azure CLI pro mezipaměť HPC Azure](az-cli-prerequisites.md).

## <a name="next-steps"></a>Další kroky

* [Vytvoření instance mezipaměti prostředí Azure HPC](hpc-cache-create.md) z Azure Portal
