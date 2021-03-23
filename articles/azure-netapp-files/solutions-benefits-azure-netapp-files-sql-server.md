---
title: Výhody použití Azure NetApp Files pro SQL Server nasazení | Microsoft Docs
description: Zobrazuje podrobné informace o výkonu analýzy nákladů na používání Azure NetApp Files pro SQL Server nasazení.
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
ms.date: 02/08/2021
ms.author: b-juche
ms.openlocfilehash: 7bbedae3d2dd080819b295d815bec68309916a6e
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104803653"
---
#  <a name="benefits-of-using-azure-netapp-files-for-sql-server-deployment"></a>Výhody použití Azure NetApp Files pro nasazení SQL Server

Azure NetApp Files snižuje SQL Server celkové náklady na vlastnictví (celkových nákladů na vlastnictví) ve srovnání s blokujícími řešeními úložiště.  V případě blokového úložiště mají virtuální počítače omezení pro vstupně-výstupní operace a šířku pásma pro diskové operace, jenom omezení šířky pásma sítě se aplikují jenom na Azure NetApp Files.  Jinými slovy, na Azure NetApp Files se neúčtují žádné limity vstupně-výstupních operací na úrovni virtuálních počítačů. Bez těchto limitů vstupu a výstupu můžou SQL Server běžící na menších virtuálních počítačích, které jsou připojené k Azure NetApp Files, fungovat i SQL Server spouštění na mnohem větších virtuálních počítačích. Navýšení velikosti instancí snižuje náklady na výpočetní výkon na 25% předchozí cenové značky.  *Pomocí Azure NetApp Files můžete snížit výpočetní náklady.*  

Náklady na výpočetní výkon jsou ale malé v porovnání s SQL Servermi licenčními náklady.  Microsoft SQL Server [licencování](https://download.microsoft.com/download/B/C/0/BC0B2EA7-D99D-42FB-9439-2C56880CAFF4/SQL_Server_2017_Licensing_Datasheet.pdf) je vázané na fyzický počet jader. V takovém případě snížení velikosti instance přináší ještě větší náklady na úsporu softwaru. *Můžete snížit náklady na licenci na software pomocí Azure NetApp Files.*

Náklady samotného úložiště jsou proměnné v závislosti na skutečné velikosti databáze. Bez ohledu na zvolené úložiště se kapacita účtuje, ať už se jedná o spravovaný disk nebo sdílenou složku.  Jak roste velikosti databáze a úložiště zvyšuje náklady, přispívá úložiště k nárůstu nákladů na vlastnictví, které ovlivňuje celkové náklady.  V takovém případě je kontrolní výraz upraven následujícím způsobem: *náklady na nasazení SQL Server můžete snížit pomocí Azure NetApp Files.* 

V tomto článku se dozvíte, jak pomocí Azure NetApp Files pro SQL Server nasazení využít podrobné analýzy nákladů a výkonové výhody. Nejenom menší instance mají dostatečný procesor, aby databáze fungovala jenom s blokem na větších instancích, ale *v mnoha případech jsou menší instance ještě více výkonné, než jejich větší, a to z důvodu Azure NetApp Files.* 

## <a name="detailed-cost-analysis"></a>Podrobná analýza nákladů 

V této části jsou uvedeny dvě sady grafiky v tomto oddílu.  Počet a typ spravovaných disků, úroveň služby Azure NetApp Files a kapacita pro každý scénář byly vybrány, aby se dosáhlo nejlepšího výkonu s cenami.  Jednotlivé obrázky se skládají z seskupených počítačů (D16 s Azure NetApp Files, ve srovnání s D64 se spravovaným diskem) a ceny se pro každý typ počítače rozdělují.  

První sada grafiky zobrazuje celkové náklady na řešení s využitím 1 TiB velikosti databáze, porovnává D16s_v3 k D64, D8 do D32 a D4 k D16. Předpokládané vstupně-výstupní operace pro každou konfiguraci jsou označeny zelenou nebo žlutou čárou a odpovídají ose Y na pravé straně.

[![Obrázek, který zobrazuje celkové náklady na řešení s využitím 1 TIB velikosti databáze. ](../media/azure-netapp-files/solution-sql-server-cost-1-tib.png)](../media/azure-netapp-files/solution-sql-server-cost-1-tib.png#lightbox)


Druhá sada grafiky zobrazuje celkové náklady pomocí databáze 50-TiB. Porovnávání jsou jinak stejné – D16 v porovnání s Azure NetApp Files a D64 s blokem. 

[![Obrázek, který zobrazuje celkové náklady pomocí 50 TIB velikosti databáze. ](../media/azure-netapp-files/solution-sql-server-cost-50-tib.png)](../media/azure-netapp-files/solution-sql-server-cost-50-tib.png#lightbox)
 
## <a name="performance-and-lots-of-it"></a>Výkon a velké množství IT  

Za účelem splnění významného kontrolního výrazu snížení nákladů je potřeba velký výkon – největší instance v obecné podpoře Azure Inventory support 80 000 (například). Jeden Azure NetApp Files svazek může dosahovat 80 000 databázových IOPS a instance, jako je například D16, jsou schopné spotřebovat stejné. D16, obvykle schopný 25 600 disk IOPS, má 25% velikost D64.  D64s_v3 je schopný 80 000 diskových vstupně-výstupních operací, takže prezentuje vynikající bod porovnání nejvyšší úrovně.

D16s_v3 může řídit svazek Azure NetApp Files na 80 000 databázový IOPS. Jak bylo ověřeno nástrojem srovnávacích testů výkonnosti SQL úložiště (SSB), instance D16 dosáhla zatížení 125% většího, než je dosažitelné pro disk z instance D64.  Podrobnosti o nástroji najdete v části [SSB test Tool](#ssb-testing-tool) .

S využitím TiB velikosti pracovní sady a 80% čtení, 20% aktualizace SQL Server úlohy, možnosti výkonu většiny instancí ve třídě D instance byly měřeny. Většina, ne všechny, protože samotné instance D2 a D64 byly vyloučeny z testování. Bývalé bylo vyčerpáno, protože nepodporuje urychlené síťové služby a druhá, protože se jedná o bod porovnání. Následující graf vám pomůže pochopit omezení D4s_v3, D8s_v3, D16s_v3 a D32s_v3, v uvedeném pořadí.  Testy úložiště spravovaného disku se v grafu nezobrazují. Hodnoty porovnání se vykreslují přímo z [tabulky omezení virtuálních počítačů Azure](../virtual-machines/dv3-dsv3-series.md) pro typ instance D.

U Azure NetApp Files může každá instance třídy D splňovat nebo překročit možnosti výkonu disku v instancích dvakrát.  *Náklady na licence na software můžete významně snížit pomocí Azure NetApp Files.*  

* V D4 v 75% využití procesoru se shodovalo s možnostmi disku D16.  
    * Rychlost D16 je omezená na 25 600 diskových IOPS.  
* D8 v 75% využití procesoru se shoduje s možnostmi disků D32.  
    * Rychlost D32 je omezená na 51 200 diskových IOPS.  
* D16 v 55% využití procesoru se shoduje s možnostmi disků D64.  
    * Rychlost D64 je omezená na 80 000 diskových IOPS.  
* D32 o 15% využití procesoru se shoduje s funkcemi disku D64.  
    * Výše uvedená D64 je sazba omezená na 80 000 disk IOPS.  

### <a name="s3b-cpu-limits-test--performance-versus-processing-power"></a>Omezení S3B procesoru test – výkon vs. výpočetní výkon

Následující diagram shrnuje test omezení S3B procesoru:

![Diagram, který zobrazuje průměrné procento CPU pro SQL Server s jednou instancí přes Azure NetApp Files](../media/azure-netapp-files/solution-sql-server-single-instance-average-cpu.png)

Škálovatelnost je pouze součástí tohoto scénáře. Druhá část je latence.  Je to pro menší virtuální počítače, které mají možnost řídit mnohem vyšší rychlost vstupně-výstupních operací, je to další postup s nízkými latencemi s jednou číslicí, jak je vidět níže.  

* D4 vyvolaly poptávku 26 000 IOPS proti Azure NetApp Files při latenci 2,3-MS.  
* D8 vyvolaly poptávku 51 000 IOPS proti Azure NetApp Files při latenci 2,0-MS.  
* D16 vyvolaly poptávku 88 000 IOPS proti Azure NetApp Files při latenci 2,8-ms.
* D32 vyvolaly poptávku 80 000 IOPS proti Azure NetApp Files při latenci 2,4-MS.  

### <a name="s3b-per-instance-type-latency-results"></a>S3B na výsledky latence typu instance

Následující diagram znázorňuje latenci pro SQL Server s jednou instancí Azure NetApp Files:

![Diagram, který zobrazuje latenci pro SQL Server s jednou instancí přes Azure NetApp Files](../media/azure-netapp-files/solution-sql-server-single-instance-latency.png)

## <a name="ssb-testing-tool"></a>Nástroj pro testování SSB 
 
Nástroj [pro srovnávací testy TPC-E](http://www.tpc.org/tpce/) podle návrhu nastresuje *výpočetní* výkon místo *úložiště*. Výsledky testů uvedené v této části jsou založené na nástroji pro zátěžové testování s názvem srovnávací test SQL úložiště (SSB).  Srovnávací test úložiště SQL Server může řídit obrovské provádění SQL na databázi SQL Server a simulovat úlohu OLTP, podobně jako [Nástroj SLOB2 pro testování Oracle](https://kevinclosson.net/slob/). 

Nástroj SSB vygeneruje úlohu na základě výběru a aktualizace, která vydává tyto příkazy přímo do SQL Server databáze běžící v rámci virtuálního počítače Azure.  Pro tento projekt se SSB úlohy, které se v počtu 1 až 100 SQL Server uživatelů po dobu 10 minut a na počet uživatelů vykonaly na 15 minut.  Všechny metriky výkonu z těchto spuštění byly z hlediska programu Perfmon, takže SSB opakovatelnosti běžela třikrát pro jeden scénář. 

Testy samy byly nakonfigurovány jako 80% SELECT a 20% příkaz UPDATE, takže 90% náhodným čtením.  Velikost samotné databáze, kterou SSB vytvořil, byla 1000 GB. Skládá se z 15 uživatelských tabulek a 9 000 000 řádků na každou uživatelskou tabulku a 8192 bajtů na řádek. 

Srovnávací SSB je open source nástroj.  Je volně k dispozici na [stránce GitHubu srovnávacích testů SQL úložiště](https://github.com/NetApp/SQL_Storage_Benchmark.git).  


## <a name="in-summary"></a>V souhrnu  

Díky Azure NetApp Files můžete zvýšit výkon SQL serveru a významně snížit celkové náklady na vlastnictví. 

## <a name="next-steps"></a>Další kroky

* [Vytvoření svazku SMB pro službu Azure NetApp Files](azure-netapp-files-create-volumes-smb.md) 
* [Architektury řešení pomocí Azure NetApp Files – SQL Server](azure-netapp-files-solution-architectures.md#sql-server) 

