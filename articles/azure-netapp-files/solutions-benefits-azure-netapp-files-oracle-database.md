---
title: Výhody použití Azure NetApp Files s Oracle Database | Microsoft Docs
description: Popisuje technologii a poskytuje srovnání výkonu mezi systémem Oracle Direct NFS (dNFS) a tradičním klientem NFS. Ukazuje výhody použití dNFS s Azure NetApp Files.
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
ms.date: 04/23/2020
ms.author: b-juche
ms.openlocfilehash: 0f431015caf5bb3364fe5628a599f760c6318c47
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91932494"
---
# <a name="benefits-of-using-azure-netapp-files-with-oracle-database"></a>Výhody použití Azure NetApp Files s Oracle Database

Oracle Direct NFS (dNFS) umožňuje zvýšit výkon, než je vlastní ovladač systému souborů NFS operačního systému. Tento článek vysvětluje technologii a poskytuje porovnání výkonu mezi dNFS a tradičním klientem NFS (NFS). Také vám ukáže výhody a jednoduchost použití dNFS s Azure NetApp Files.  

## <a name="how-oracle-direct-nfs-works"></a>Jak funguje systém Oracle Direct NFS

Následující přehled vysvětluje, jak funguje systém Oracle Direct NFS na vysoké úrovni:

* Oracle Direct NFS obchází mezipaměť vyrovnávací paměti operačního systému. Data se ukládají do mezipaměti jenom jednou v uživatelském prostoru a odstraňují se nároky na kopie paměti.  

* Tradiční klient systému souborů NFS používá jeden tok sítě, jak je znázorněno níže:    

    ![Tradiční klient systému souborů NFS s jedním síťovým tokem](../media/azure-netapp-files/solutions-traditional-nfs-client-using-single-network-flow.png)

    Oracle Direct NFS dále vylepšuje výkon pomocí síťového provozu vyrovnáváním zatížení napříč několika síťovými toky. Jak bylo testováno a zobrazeno níže, 650 různá síťová připojení byla dynamicky vytvořena Oracle Database:  

    ![Oracle Direct NFS – zlepšení výkonu](../media/azure-netapp-files/solutions-oracle-direct-nfs-performance-load-balancing.png)

V tématu [Oracle Nejčastější dotazy pro přímý systém souborů NFS](http://www.orafaq.com/wiki/Direct_NFS) se zobrazuje, že Oracle dNFS je optimalizovaný klient systému souborů NFS. Poskytuje rychlý a škálovatelný přístup k úložišti NFS, které se nachází na zařízeních úložišť úložiště (přístupné přes protokol TCP/IP). dNFS je součástí jádra databáze stejně jako ASM, který se primárně používá v rámci úložiště DAS nebo SAN. V takovém případě se *doporučuje použít dNFS při implementaci úložiště NAS a při implementaci úložiště San použít ASM.*

dNFS je výchozí možnost v Oracle 18c.

dNFS je k dispozici od Oracle Database 11g. Diagram níže porovnává dNFS s nativním systémem souborů NFS. Při použití dNFS může databáze Oracle, která běží na virtuálním počítači Azure, probíhat více vstupně-výstupních operací než nativní klient systému souborů NFS.

![Oracle a Azure NetApp Files porovnání dNFS s nativním systémem souborů NFS](../media/azure-netapp-files/solutions-oracle-azure-netapp-files-comparing-dnfs-native-nfs.png)

DNFS můžete povolit nebo zakázat spuštěním dvou příkazů a restartováním databáze.

Povolení:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_on`

Chcete-li zakázat:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_off`

## <a name="azure-netapp-files-combined-with-oracle-direct-nfs"></a>Azure NetApp Files kombinované se systémem Oracle Direct NFS

Můžete zvýšit výkon Oracle dNFS pomocí služby Azure NetApp Files. Služba poskytuje úplnou kontrolu nad výkonem vaší aplikace. Může splňovat extrémně náročné aplikace. Kombinace Oracle dNFS s Azure NetApp Files poskytuje skvělé výhody pro vaše úlohy.

## <a name="next-steps"></a>Další kroky

- [Architektury řešení s využitím Azure NetApp Files](azure-netapp-files-solution-architectures.md)
- [Přehled aplikací a řešení Oracle v Azure](../virtual-machines/workloads/oracle/oracle-overview.md)