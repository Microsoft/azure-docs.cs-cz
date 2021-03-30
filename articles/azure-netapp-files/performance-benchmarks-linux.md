---
title: Azure NetApp Files srovnávacích testů výkonu pro Linux | Microsoft Docs
description: Popisuje srovnávací testy výkonu Azure NetApp Files poskytuje pro Linux.
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
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: b-juche
ms.openlocfilehash: b763a734866dd5fed5bf0500d4d52b9324c92a79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "82614587"
---
# <a name="azure-netapp-files-performance-benchmarks-for-linux"></a>Azure NetApp Files srovnávacích testů výkonu pro Linux

Tento článek popisuje srovnávací testy výkonu Azure NetApp Files poskytuje pro Linux.

## <a name="linux-scale-out"></a>Rozšíření pro Linux na více instancí

V této části jsou popsány testy výkonnosti propustnosti úloh systému Linux a IOPS úloh.

### <a name="linux-workload-throughput"></a>Propustnost úloh pro Linux  

Následující graf znázorňuje sekvenční úlohy 64-kibibyte (KiB) a TiB pracovní sadu. Ukazuje, že jeden Azure NetApp Files svazek může zvládnout mezi čistě sekvenčními zápisy ~ 1 600 MiB/s a čistě sekvenčním čtením. ~ 4 500 MiB/s.  

Graf znázorňuje snížení v 10% po dobu od čistého čtení po čistě zápis. Ukazuje, co můžete očekávat při použití proměnlivých poměrů pro čtení a zápis (100%: 0%, 90%: 10%, 80%: 20% atd.).

![Propustnost úloh pro Linux](../media/azure-netapp-files/performance-benchmarks-linux-workload-throughput.png)  

### <a name="linux-workload-iops"></a>Systém Linux – IOPS  

Následující graf představuje náhodný pracovní vytížení 4 – kibibyte (KiB) a 1 – TiB pracovní sadu. Graf ukazuje, že Azure NetApp Files svazek může zvládnout mezi ~ 130 000 čistě náhodnými zápisy a ~ 460 000 čistě náhodné čtení.  

Tento graf znázorňuje snížení v 10% najednou, od čistého čtení po čistě zápis. Ukazuje, co můžete očekávat při použití proměnlivých poměrů pro čtení a zápis (100%: 0%, 90%: 10%, 80%: 20% atd.).

![Systém Linux – IOPS](../media/azure-netapp-files/performance-benchmarks-linux-workload-iops.png)  

## <a name="linux-scale-up"></a>Rozšíření pro Linux  

Jádro Linux 5,3 umožňuje sítě pro systém souborů NFS s možností horizontálního navýšení kapacity v jednom klientovi `nconnect` . Grafy v této části ukazují výsledky ověřovacích testů pro možnost připojení na straně klienta pomocí NFSv3. Tato funkce je k dispozici na SUSE (počínaje SLES12SP4) a Ubuntu (počínaje verzí 19,10). Je podobný v konceptu pro rozhraní SMB vícekanálový i Oracle Direct NFS.

Grafy porovnávají výhody nástroje `nconnect` na připojeném svazku, který není připojen. V grafech FIO vygenerovala úlohu z jedné instance D32s_v3 v oblasti Azure US-west2.

### <a name="linux-read-throughput"></a>Propustnost čtení pro Linux  

Následující grafy znázorňují sekvenční čtení souboru. 3 500 MiB/s `nconnect` , přibližně 2.3 x než `nconnect` .

![Propustnost čtení pro Linux](../media/azure-netapp-files/performance-benchmarks-linux-read-throughput.png)  

### <a name="linux-write-throughput"></a>Propustnost zápisu pro Linux  

Následující grafy zobrazují sekvenční zápisy. Označují, že `nconnect` pro sekvenční zápis neexistují žádné znatelné výhody. 1 500 MiB/s je zhruba sekvenční limit svazku pro zápis a limit odchozího D32s_v3 instance.

![Propustnost zápisu pro Linux](../media/azure-netapp-files/performance-benchmarks-linux-write-throughput.png)  

### <a name="linux-read-iops"></a>Linux Read IOPS  

Následující grafy ukazují náhodné čtení ~ 200 000 čtení IOPS s `nconnect` , zhruba 3x ne `nconnect` .

![Linux Read IOPS](../media/azure-netapp-files/performance-benchmarks-linux-read-iops.png)  

### <a name="linux-write-iops"></a>Pro Linux zápis IOPS  

Následující grafy ukazují náhodné zápisy ~ 135 000 zápis IOPS s `nconnect` , zhruba 3x ne `nconnect` .

![Pro Linux zápis IOPS](../media/azure-netapp-files/performance-benchmarks-linux-write-iops.png)  

## <a name="next-steps"></a>Další kroky

- [Azure NetApp Files: využití cloudového úložiště na maximum](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf?hsCtaTracking=f2f560e9-9d13-4814-852d-cfc9bf736c6a%7C764e9d9c-9e6b-4549-97ec-af930247f22f)
