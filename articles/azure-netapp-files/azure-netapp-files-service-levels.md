---
title: Úrovně služby pro Azure NetApp Files | Dokumentace Microsoftu
description: Popisuje propustnost výkonu pro úrovně služeb Azure NetApp souborů.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: concepts
ms.date: 02/14/2019
ms.author: b-juche
ms.openlocfilehash: a10ac319fe362011531e00f832bb8e471fd56fdf
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430896"
---
# <a name="service-levels-for-azure-netapp-files"></a>Úrovně služeb pro soubory Azure NetApp
Služba soubory Azure NetApp podporuje dvě úrovně služby: Premium a Standard. 

## <a name="Premium"></a>Storage úrovně Premium

*Premium* storage poskytuje až 64 MiB/s na TiB propustnosti. Propustnost se indexuje zpětně proti kvóty. Například svazek ze služby storage úrovně Premium s 2 TB zřízené kvóty (bez ohledu na skutečné spotřebě) má propustnost 128 MiB/s.

## <a name="Standard"></a>Storage úrovně Standard

*Standardní* storage poskytuje až 16 MiB/s na TiB propustnosti. Propustnost se indexuje zpětně proti kvóty. Například svazek z úložiště úrovně Standard s 2 TB zřízené kvóty (bez ohledu na skutečné spotřebě) má propustností 32 MiB/s.

## <a name="next-steps"></a>Další postup

- Najdete v článku [souborů NetApp Azure stránce s cenami](https://azure.microsoft.com/pricing/details/storage/netapp/) za cenu úrovní různých služeb
- [Nastavení fondu kapacity](azure-netapp-files-set-up-capacity-pool.md)
