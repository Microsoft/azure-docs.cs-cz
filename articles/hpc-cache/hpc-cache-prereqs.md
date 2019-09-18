---
title: Požadavky na mezipaměť prostředí Azure HPC
description: Předpoklady pro použití mezipaměti HPC Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: 50c60e38b58815be04cfb892c3622b9579529e67
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036856"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Předpoklady pro mezipaměť Azure HPC

Než použijete Azure Portal k vytvoření nové mezipaměti prostředí Azure HPC, ujistěte se, že vaše prostředí splňuje tyto požadavky.

## <a name="azure-subscription"></a>Předplatné Azure

Doporučuje se placené předplatné.

> [!NOTE]
> Během vydání verze Preview musí tým Azure HPC cache přidat vaše předplatné do seznamu přístupu, aby ho bylo možné použít k vytvoření instance mezipaměti. Tento postup pomáhá zajistit, že každý zákazník získá vysoce kvalitní odezvu z testovacích mezipamětí. Vyplňte [Tento formulář](https://aka.ms/onboard-hpc-cache) a požádejte o přístup.

## <a name="network-infrastructure"></a>Síťová infrastruktura

Předtím, než budete moci použít mezipaměť, by měly být nastavené dvě možnosti související se sítí:

* Vyhrazená podsíť pro instanci mezipaměti Azure HPC
* Podpora DNS, aby mohla mezipaměť získat přístup k úložišti a dalším prostředkům

### <a name="cache-subnet"></a>Podsíť mezipaměti

Mezipaměť prostředí Azure HPC potřebuje vyhrazenou podsíť s těmito kvalitou:

* Podsíť musí mít k dispozici minimálně 64 IP adres.
* Podsíť nemůže hostovat žádné jiné virtuální počítače, a to ani pro související služby, jako jsou klientské počítače.
* Pokud používáte více instancí mezipaměti, každá z nich potřebuje vlastní podsíť.

Osvědčeným postupem je vytvořit novou podsíť pro mezipaměť. V rámci vytváření mezipaměti můžete vytvořit novou virtuální síť a podsíť.

### <a name="dns-access"></a>Přístup DNS

Mezipaměť prostředí Azure HPC potřebuje DNS pro přístup k prostředkům mimo svou virtuální síť. V závislosti na tom, jaké prostředky používáte, možná budete muset nastavit vlastní server DNS a nakonfigurovat přesměrování mezi tímto serverem a Azure DNS servery: 

* Pro přístup k koncovým bodům služby Azure Blob Storage a dalším interním prostředkům budete potřebovat server DNS založený na Azure.
* Pokud chcete získat přístup k místnímu úložišti, musíte nakonfigurovat vlastní server DNS, který dokáže přeložit názvy hostitelů úložiště.

Pokud potřebujete jenom přístup k úložišti objektů blob, můžete pro svou mezipaměť použít výchozí server DNS určený pro Azure. Pokud ale potřebujete přístup k jiným prostředkům, měli byste vytvořit vlastní server DNS a nakonfigurovat ho tak, aby přenesl všechny požadavky na rozlišení specifické pro Azure na Azure DNS Server. (Jednoduchý server DNS se dá použít taky k vyrovnávání zatížení klientů mezi všemi dostupnými přípojnými body mezipaměti.)

Přečtěte si další informace o virtuálních sítích Azure a konfiguracích serverů DNS v [překladu názvů pro prostředky v Azure Virtual Networks](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

## <a name="permissions"></a>Oprávnění

Než začnete vytvářet mezipaměť, ověřte tyto požadavky týkající se oprávnění.

* Mezipaměť HPC Azure musí být schopná vytvářet rozhraní virtuální sítě (nic). Uživatel, který vytváří mezipaměť, musí mít v předplatném dostatečná oprávnění pro vytváření síťových adaptérů.
<!-- There are several ways to authorize this access; read [Additional prerequisites](media/preview-prereqs.md) to learn more. -->

* Pokud používáte úložiště objektů blob, vyžaduje instance mezipaměti prostředí Azure HPC autorizaci pro přístup k vašemu účtu úložiště. Řízení přístupu na základě role (RBAC) můžete použít k poskytnutí přístupu k mezipaměti do úložiště objektů BLOB. Jsou vyžadovány dvě role: Přispěvatel účtu úložiště a přispěvatel dat objektu BLOB úložiště. Postupujte podle pokynů v části [Přidání úložiště do mezipaměti](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account).

## <a name="storage-infrastructure"></a>Infrastruktura úložiště

Mezipaměť podporuje exporty do kontejnerů objektů blob Azure nebo pro hardwarové úložiště NFS. Při vytváření mezipaměti můžete definovat cíle úložiště, ale můžete je také přidat později. 

### <a name="nfs-storage-requirements"></a>Požadavky na úložiště NFS

Pokud používáte místní hardwarové úložiště, musí mít mezipaměť síťový přístup s vysokou šířkou pásma do datového centra z jeho podsítě. Doporučuje se [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) nebo podobný přístup.

Back-end úložiště NFS musí být kompatibilní hardwarová a softwarová platforma. Podrobnosti získáte od týmu Azure HPC cache.

### <a name="blob-storage-requirements"></a>Požadavky na úložiště objektů BLOB

Pokud chcete používat úložiště objektů BLOB v Azure s mezipamětí Azure HPC, potřebujete kompatibilní účet úložiště a prázdný kontejner objektů BLOB nebo kontejner, který je naplněný daty ve formátu mezipaměti HPC Azure, jak je popsáno v tématu [přesun dat do služby Azure Blob Storage](hpc-cache-ingest.md).

Před pokusem o přidání účtu jako cíle úložiště vytvořte účet a kontejner.

Pokud chcete vytvořit kompatibilní účet úložiště, použijte Tato nastavení:

* Předepsané **Standard**
* Druh účtu: **StorageV2 (obecné účely v2)**
* Umístění **Místně redundantní úložiště (LRS)**
* Úroveň přístupu (výchozí): **za běhu**

Je vhodné použít účet úložiště ve stejném umístění jako mezipaměť.

Musíte taky dát aplikaci cache přístup k vašemu účtu úložiště Azure. Podle popisu v části [Přidání úložiště do mezipaměti](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) udělte mezipaměti účet úložiště role přispěvatel a přispěvatel dat objektů BLOB úložiště. Pokud nejste vlastníkem účtu úložiště, udělejte tohoto kroku vlastník.

## <a name="next-steps"></a>Další postup

* [Vytvoření instance mezipaměti prostředí Azure HPC](hpc-cache-create.md) z Azure Portal
