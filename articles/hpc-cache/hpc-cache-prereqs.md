---
title: Požadavky na mezipaměť prostředí Azure HPC
description: Předpoklady pro použití mezipaměti HPC Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 90b84d936bda4e3a974e60934e82ac6c3389d85a
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645765"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Předpoklady pro mezipaměť Azure HPC

Než použijete Azure Portal k vytvoření nové mezipaměti prostředí Azure HPC, ujistěte se, že vaše prostředí splňuje tyto požadavky.

## <a name="azure-subscription"></a>Předplatné Azure

Doporučuje se placené předplatné.

> [!NOTE]
> Během prvních několika měsíců od verze GA musí tým Azure HPC cache přidat vaše předplatné do seznamu přístupu předtím, než bude možné ho použít k vytvoření instance mezipaměti. Tento postup pomáhá zajistit, že každý zákazník získá vysoce kvalitní odezvu z mezipamětí. Vyplňte [Tento formulář](https://aka.ms/onboard-hpc-cache) a požádejte o přístup.

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

### <a name="nfs-storage-requirements"></a>Požadavky na úložiště NFS

Pokud používáte místní hardwarové úložiště, musí mít mezipaměť síťový přístup s vysokou šířkou pásma do datového centra z jeho podsítě. Doporučuje se [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) nebo podobný přístup.

Back-end úložiště NFS musí být kompatibilní hardwarová a softwarová platforma. Podrobnosti získáte od týmu Azure HPC cache.

### <a name="blob-storage-requirements"></a>Požadavky na úložiště objektů BLOB

Pokud chcete používat úložiště objektů BLOB v Azure s mezipamětí, potřebujete kompatibilní účet úložiště a prázdný kontejner objektů BLOB nebo kontejner, který je naplněný daty ve formátu mezipaměti HPC Azure, jak je popsáno v tématu [přesun dat do služby Azure Blob Storage](hpc-cache-ingest.md).

Vytvořte účet před tím, než se pokusíte přidat cíl úložiště. Když přidáte cíl, můžete vytvořit nový kontejner.

Pokud chcete vytvořit kompatibilní účet úložiště, použijte Tato nastavení:

* Výkon: **Standard**
* Druh účtu: **StorageV2 (pro obecné účely v2)**
* Replikace: **místně redundantní úložiště (LRS)**
* Úroveň přístupu (výchozí): **Hot**

Je vhodné použít účet úložiště ve stejném umístění jako mezipaměť.
<!-- clarify location - same region or same resource group or same virtual network? -->

K vašemu účtu služby Azure Storage musíte taky udělit přístup k aplikaci cache, jak je uvedeno výše v části [oprávnění](#permissions). Postupujte podle pokynů v části [Přidání cílů úložiště](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) a poskytněte mezipaměti požadované role přístupu. Pokud nejste vlastníkem účtu úložiště, udělejte tohoto kroku vlastník.

## <a name="next-steps"></a>Další kroky

* [Vytvoření instance mezipaměti prostředí Azure HPC](hpc-cache-create.md) z Azure Portal
