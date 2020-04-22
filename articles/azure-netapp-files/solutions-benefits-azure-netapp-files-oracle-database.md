---
title: Výhody používání souborů Azure NetApp s databází Oracle Database | Dokumenty společnosti Microsoft
description: Popisuje technologii a poskytuje porovnání výkonu mezi službou Oracle Direct NFS (dNFS) a tradičním klientem služby NFS. Zobrazuje výhody použití dNFS se soubory Azure NetApp.
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
ms.date: 04/20/2020
ms.author: b-juche
ms.openlocfilehash: d28f5469174a2659869ebb01ef01653b7190e30e
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81772077"
---
# <a name="benefits-of-using-azure-netapp-files-with-oracle-database"></a>Výhody používání souborů Azure NetApp s databází Oracle Database

Systém Oracle Direct NFS (dNFS) umožňuje zvýšit výkon než vlastní ovladač systému souborů NFS operačního systému. Tento článek vysvětluje technologii a poskytuje porovnání výkonu mezi dNFS a tradiční klient nfs (kernel nfs). Také ukazuje výhody a snadnost použití dNFS se soubory Azure NetApp.  

## <a name="how-oracle-direct-nfs-works"></a>Jak služba Oracle Direct NFS funguje

Systém Oracle Direct NFS (dNFS) obchází vyrovnávací paměť operačního systému. Data jsou uložena do mezipaměti pouze jednou v uživatelském prostoru, což eliminuje režii kopií paměti.  

Tradiční klient služby NFS používá jeden síťový tok, jak ukazuje následující příklad: 

![Tradiční klient nfs pomocí jednoho síťového toku](../media/azure-netapp-files/solutions-traditional-nfs-client-using-single-network-flow.png)

Naproti tomu oracle dNFS zlepšuje výkon tím, že vyrovnává zatížení síťového provozu napříč více síťovými toky. Tato funkce umožňuje databázi Oracle dynamicky vytvořit významný počet 650 různých síťových připojení, jak je znázorněno v příkladu níže:  

![Technologie Oracle Direct NFS zlepšují cívýkonu](../media/azure-netapp-files/solutions-oracle-direct-nfs-performance-load-balancing.png)

[Nejčastější dotazy k řešení Oracle pro rozhraní Direct NFS](http://www.orafaq.com/wiki/Direct_NFS) ukazují, že služba Oracle dNFS je optimalizovaným klientem služby NFS. Poskytuje rychlý a škálovatelný přístup k úložišti NFS, které se nachází na úložných zařízeních NAS (přístupných přes Protokol TCP/IP). dNFS je integrován do jádra databáze stejně jako ASM, který se používá především s DAS nebo SAN úložiště. Jako takové *je hlavní zásadou použití dNFS při implementaci úložiště NAS a použití ASM při implementaci úložiště SAN.*

dNFS je výchozí možnost v oracle 18c a výchozí pro RAC.

dNFS je k dispozici počínaje databází Oracle Database 11g. Níže uvedený diagram porovnává dNFS s nativnínfs. Při použití dNFS, databáze Oracle, která běží na virtuálním počítači Azure můžete řídit více vstupně-va než nativní klient nfs.

![Oracle a Azure NetApp soubory porovnání dNFS s nativní nfs](../media/azure-netapp-files/solutions-oracle-azure-netapp-files-comparing-dnfs-native-nfs.png)

Službu dNFS můžete povolit nebo zakázat spuštěním dvou příkazů a restartováním databáze.

Chcete-li povolit:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_on`

Chcete-li zakázat:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_off`

## <a name="azure-netapp-files-combined-with-oracle-direct-nfs"></a>Soubory Azure NetApp v kombinaci se službou Oracle Direct NFS

Můžete zvýšit výkon Oracle dNFS pomocí služby Azure NetApp Files. Služba poskytuje úplnou kontrolu nad výkonem aplikace. Dokáže vyhovět extrémně náročným aplikacím. Kombinace oracle dNFS se soubory Azure NetApp poskytuje velkou výhodu pro vaše úlohy.

## <a name="next-steps"></a>Další kroky

- [Architektury řešení s využitím Azure NetApp Files](azure-netapp-files-solution-architectures.md)
- [Přehled aplikací a řešení Oracle v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-overview)