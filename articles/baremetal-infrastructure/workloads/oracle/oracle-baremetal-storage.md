---
title: Úložiště v BareMetal pro úlohy Oracle
description: Přečtěte si informace o úložišti, které nabízí infrastruktura BareMetal pro úlohy Oracle.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 685f4d401d05d897b91ad12dbd79f7d175db05c6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107558982"
---
# <a name="storage-on-baremetal-for-oracle-workloads"></a>Úložiště v BareMetal pro úlohy Oracle

V tomto článku poskytneme Přehled úložiště nabízeného infrastrukturou BareMetal pro úlohy Oracle.

BareMetal infrastruktura pro Oracle nabízí úložiště NetApp NFS (Network File System). Úložiště NFS nevyžaduje certifikaci Oracle Real Application Clusters (RAC). Další informace najdete v tématu [matice technologie Oracle RAC pro clustery se systémem Linux](https://www.oracle.com/database/technologies/tech-generic-linux-new.html).

Tato nabídka úložiště zahrnuje podporu vrstvy 3 od partnera OEM, a to buď pomocí řadičů úložiště A700s nebo A800s.

Úložiště infrastruktury BareMetal nabízí tyto možnosti Premium Storage:

- Svazky úložiště pro data/protokol/kvorum/FSA nabízené prostřednictvím protokolu dNFS
- Redundance disku (*Ochrana proti selhání po dvou discích*).
- Nahorizontální navýšení kapacity dat na více svazků omezený na 100 TB na jeden svazek.
- Horizontální navýšení kapacity na několik řadičů úložiště až na 12 řadičů.
- Bez správy na úrovni disku (*přidávání a odebírání disků*) se automaticky nebere v potaz.
- Bez výpadků pro redistribuci obsahu souboru na jiné svazky.
- Schopnost zvětšit nebo zmenšit svazky.
- SnapCenter Integration pro zálohování pomocí klonování a SnapVault.
- Šifrování dat v klidovém umístění, podporující standard FIPS (140-2).

## <a name="next-steps"></a>Další kroky

Seznamte se s informacemi o BareMetal infrastruktury pro opravy.

> [!div class="nextstepaction"]
> [Požadavky na opravy pro BareMetal pro Oracle](oracle-baremetal-patching.md)

