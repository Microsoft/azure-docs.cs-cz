---
title: Požadavky na výkon systému souborů NFS 3,0 ve službě Azure Blob Storage (Preview) | Microsoft Docs
description: Optimalizujte výkon svých požadavků na úložiště NFS (Network File System) 3,0 pomocí doporučení v tomto článku.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 28fb5ffc0ce9c554294eaca9e460694856cb95a0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589867"
---
# <a name="network-file-system-nfs-30-performance-considerations-in-azure-blob-storage-preview"></a>Požadavky na výkon systému souborů NFS (Network File System) 3,0 v úložišti objektů BLOB v Azure (Preview)

BLOB Storage teď podporuje protokol NFS (Network File System) 3,0. Tento článek obsahuje doporučení, která vám pomůžou optimalizovat výkon vašich požadavků na úložiště. Další informace o podpoře systému souborů NFS 3,0 v Azure Blob Storage najdete v tématu [Podpora protokolu NFS (Network File System) 3,0 ve službě Azure Blob Storage (Preview)](network-file-system-protocol-support.md).

> [!NOTE]
> Podpora protokolu NFS 3,0 ve službě Azure Blob Storage je ve verzi Public Preview. Podporuje GPV2 účty úložiště s výkonem úrovně Standard v následujících oblastech: Austrálie – východ, Korea – střed a Střed USA – jih. Verze Preview také podporuje objekty blob bloku s úrovní výkonu Premium ve všech veřejných oblastech.

## <a name="add-clients-to-increase-throughput"></a>Přidání klientů pro zvýšení propustnosti 

Azure Blob Storage škáluje lineárně, dokud nedosáhne maximálního omezení odchozího a příchozího účtu úložiště. Proto můžou vaše aplikace dosáhnout vyšší propustnosti pomocí více klientů.  Pokud chcete zobrazit omezení pro výstup a výstup účtu úložiště, přečtěte si téma [škálovatelnost a výkonnostní cíle pro účty úložiště úrovně Standard](../common/scalability-targets-standard-account.md).

Následující graf ukazuje, jak se šířka pásma zvyšuje při přidávání dalších klientů. V tomto grafu je klient virtuální počítač (VM) a účet používá standardní úroveň výkonu. 

> [!div class="mx-imgBorder"]
> ![Standardní výkon](./media/network-file-system-protocol-support-performance/standard-performance-tier.png)

Následující graf ukazuje stejný efekt při použití na účet, který používá úroveň výkonu Premium.

> [!div class="mx-imgBorder"]
> ![Výkon úrovně Premium](./media/network-file-system-protocol-support-performance/premium-performance-tier.png)

## <a name="use-premium-performance-tier-for-small-scale-applications"></a>Použití úrovně Premium Performance pro aplikace v malém měřítku

Ne všechny aplikace se můžou škálovat až po přidání dalších klientů. Pro tyto aplikace nabízí [účet úložiště blob bloku Azure Premium](storage-blob-create-account-block-blob.md) konzistenci s nízkou latencí a vysokou mírou transakcí. Účet úložiště objektů blob bloku Premium může dosáhnout maximální šířky pásma s menším počtem vláken a klientů. Například u jednoho klienta může účet úložiště blob bloku úrovně Premium dosahovat **2,3** šířky pásma v porovnání se stejným nastavením, které bylo použito pro účet úložiště pro obecné účely verze 2. 

Každý pruh v následujícím grafu znázorňuje rozdíl v dosažené šířce pásma mezi účty úložiště úrovně Premium a Standard. Jak roste počet klientů, tento rozdíl se zkrátí.  

> [!div class="mx-imgBorder"]
> ![Relativní výkon](./media/network-file-system-protocol-support-performance/relative-performance.png)

## <a name="avoid-frequent-overwrites-on-data"></a>Vyhnout se častým přepsáním dat

Dokončení operace přepsání, než je nová operace zápisu, trvá delší dobu. Důvodem je to, že operace přepsání systému souborů NFS, zejména částečné úpravy souborů, je kombinací několika základních operací blobů: čtení, úpravy a operace zápisu. Proto aplikace, která vyžaduje časté úpravy na místě, není vhodná pro účty BLOB Storage s povoleným systémem souborů NFS. 

## <a name="other-best-practice-recommendations"></a>Další doporučení k osvědčeným postupům 

- Používejte virtuální počítače s dostatečnou šířkou pásma sítě.

- Použijte několik přípojných bodů, pokud je to vaše zatížení povoluje.

- Použijte tolik vláken, kolik jich bylo možné.

- Používejte velikosti velkých bloků.

- Poznamenejte si požadavky na úložiště z klienta, který je umístěný ve stejné oblasti jako účet úložiště. To může zlepšit latenci sítě.

## <a name="next-steps"></a>Další kroky

- Další informace o podpoře systému souborů NFS 3,0 v Azure Blob Storage najdete v tématu [Podpora protokolu NFS (Network File System) 3,0 ve službě Azure Blob Storage (Preview)](network-file-system-protocol-support.md).

- Informace o tom, jak začít, najdete v tématu [připojení úložiště objektů BLOB pomocí protokolu NFS (Network File System) 3,0 (Preview)](network-file-system-protocol-support-how-to.md).
