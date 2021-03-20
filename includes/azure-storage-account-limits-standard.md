---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ef9efe389894af7c792e980922ca422e9d05929b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96026184"
---
V následující tabulce jsou popsány výchozí limity pro účty úložiště Azure pro obecné účely V1, v2, BLOB Storage a Block. Limit  příchozího přenosu dat odkazuje na všechna data, která se odesílají do účtu úložiště. Limit *odchozího* přenosu dat odkazuje na všechna data přijatá z účtu úložiště.

> [!NOTE]
> Můžete požadovat vyšší omezení kapacity a příchozího přenosu dat. Pokud chcete požádat o zvýšení, obraťte se na [podporu Azure](https://azure.microsoft.com/support/faq/).

| Prostředek | Omezení |
| --- | --- |
| Počet účtů úložiště podle jednotlivých oblastí a předplatných, včetně účtů úložiště Standard a Premium.| 250 |
| Maximální kapacita účtu úložiště | 5 PiB <sup>1</sup>|
| Maximální počet kontejnerů objektů blob, objektů blob, sdílených složek, tabulek, front, entit nebo zpráv na účet úložiště | Bez omezení |
| Maximální počet požadavků<sup>1</sup> na účet úložiště | počet požadavků 20 000 za sekundu |
| Maximální příchozí<sup>1</sup> na účet úložiště (oblasti USA, Evropa) | 10 Gb/s |
| Maximální příchozí<sup>1</sup> na účet úložiště (jiné oblasti než USA a Evropa) | 5 GB/s, pokud je povolená možnost RA-GRS/GRS, 10 GB/s pro LRS/ZRS<sup>2</sup> |
| Maximální výstup pro účty pro obecné účely v2 a BLOB Storage (všechny oblasti) | 50 GB/s |
| Maximální výstup pro účty úložiště V1 pro obecné účely (oblasti USA) | 20 GB/s, pokud je povolená možnost RA-GRS/GRS, 30 GB/s pro LRS/ZRS<sup>2</sup> |
| Maximální výstup pro účty úložiště pro obecné účely V1 (oblasti mimo USA) | 10 GB/s, pokud je povolená možnost RA-GRS/GRS, 15 GB/s pro LRS/ZRS<sup>2</sup> |
| Maximální počet pravidel virtuální sítě na účet úložiště | 200 |
| Maximální počet pravidel IP adres na účet úložiště | 200 |

<sup>1</sup> Azure Storage standardní účty podporují vyšší limity kapacity a vyšší limity pro příchozí zpracování žádostí. Pokud chcete požádat o zvýšení limitů účtu, obraťte se na [podporu Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup> Pokud váš účet úložiště má povolený přístup pro čtení s geograficky redundantním úložištěm (RA-GRS) nebo geograficky redundantním úložištěm (RA-GZRS), pak jsou cíle výstupu sekundárního umístění stejné jako v primárním umístění. Další informace najdete v tématu [replikace Azure Storage](../articles/storage/common/storage-redundancy.md).

> [!NOTE]
> Microsoft doporučuje, abyste pro většinu scénářů používali účet úložiště pro obecné účely v2. Účet Azure Blob Storage pro obecné účely v1 nebo Azure můžete snadno upgradovat na účet pro obecné účely v2 bez výpadků a bez nutnosti kopírovat data. Další informace najdete v tématu [upgrade na účet úložiště pro obecné účely v2](../articles/storage/common/storage-account-upgrade.md).

Všechny účty úložiště běží v ploché síťové topologii bez ohledu na to, kdy byly vytvořeny. Další informace o architektuře Azure Storage ploché sítě a o škálovatelnosti najdete v článku [Microsoft Azure Storage: vysoce dostupná služba cloudového úložiště s silnou konzistencí](/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets).