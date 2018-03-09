---
title: SAP HANA dostupnosti v oblastech Azure | Microsoft Docs
description: "Popisuje přehled aspektů dostupnosti při spuštění SPA HANA na virtuálních počítačích Azure"
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: 
author: msjuergent
manager: patfilot
editor: 
tags: azure-resource-manager
keywords: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/26/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 052394884f85d527caa88ff6c9464af669f47bb5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-availability-across-azure-regions"></a>SAP HANA dostupnosti v oblastech Azure
V tomto článku jsou popsané scénáře kolem SAP HANA dostupnosti v různých oblastech Azure a popsané. Vzhledem k tomu, že samostatné oblasti Azure mají větší vzdálenosti mezi nimi, existují zvláštní požadavky, které jsou uvedeny v tomto článku.

## <a name="motivation-to-deploy-across-multiple-azure-regions"></a>Motivace pro nasazení nad několika oblastmi Azure
Různých oblastech Azure jsou odděleny větší vzdálenosti. Závisí na geopolitické oblasti, může se jednat miles stovky nebo i několika tisíc miles, jako je ve Spojených státech amerických. Z důvodu vzdálenost mezi různých oblastech Azure síťový provoz mezi prostředky nasazené v latence umožňujícím zpětnou transformaci významné sítě prostředí dvou různých oblastech Azure. Významné dostatečně vyloučit exchange synchronní data mezi dvěma instancemi SAP HANA v typické zatížení SAP. Na druhé straně můžete často se potýkají s skutečnost, že je definovaná požadavek na vzdálenost mezi vaším datovým centrem primárního a sekundárního datového centra k zajištění dostupnosti v případě přírodní katastrofě stiskne širší oblasti. Například hurikány, stiskněte tlačítko oblasti karibské a Florida v září a říjen 2017. Nebo alespoň požadavek na minimální vzdálenost. Ve většině případů zákazník tuto definici minimální vzdálenost vyžaduje, abyste návrh pro dostupnost v rámci [oblasti Azure](https://azure.microsoft.com/regions/). Vzhledem k tomu, že je příliš velký mezi vzdálenost dvou oblastech Azure za účelem použití režimu synchronní replikace HANA, požadavky RTO a plánovaný bod obnovení může vynutit nasazení konfigurace dostupnosti v rámci jedné oblasti a pak doplnit s další nasazení za sekundu oblast.

Další aspekt zvážit v těchto konfiguracích je převzetí služeb při selhání a přesměrování klienta. Předpokladem je, že převzetí služeb při selhání mezi instancemi SAP HANA ve dvou různých oblastech Azure vždy je ruční převzetí služeb při selhání. Vzhledem k tomu, že režim replikace SAP HANA systému replikace je nastaven asynchronní, existuje možnost, že data potvrzeno u primární instance HANA nebyl dostal ještě sekundární HANA instance. Proto nemůže být přijat automatické převzetí služeb při selhání pro konfigurace, kde je asynchronní replikaci. Dokonce i s ruční kontrolou převzetí služeb při selhání, jako cvičení převzetí služeb při selhání budete muset použít opatření a ujistěte se, že všechna potvrzená data na primární straně dostal sekundární instance před ručně přesouvání v dané oblasti Azure.
 
Vzhledem k tomu můžete pracovat s jiný rozsah IP adres ve virtuální sítě Azure, které jsou nasazeny v druhé oblast Azure, buď muset změnit ve své konfiguraci klientů SAP HANA nebo lepší způsob je potřeba zavést kroky změnit překlad IP adresy. Proto že klienti jsou získávání přesměrován na nový sekundární je IP adresa serveru. Článek SAP na [obnovení připojení klienta po převzetí](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html) přejde do další podrobnosti.   

## <a name="simple-availability-between-two-azure-regions"></a>Jednoduché dostupnosti mezi dvěma oblastmi Azure
V tomto scénáři jste se rozhodli blokovat všechny konfigurace dostupnosti zavedené v jedné oblasti. Ale máte vyžádání tak, aby měl zatížení zpracování v případě havárie. Typickými případy systémy jako, které jsou mimo produkční systémy. I když může tolerovat tak, aby měl systém dolů poloviční denně nebo i za den, nelze povolit systému tak, aby nebyl k dispozici pro 48 hodin nebo více. Aby bylo možné instalaci menší nákladná, spustíte jiného systému, který je i méně důležité uchování virtuálního počítače, který funguje jako cíl nebo velikost virtuálního počítače v sekundární oblasti menší a zvolte nechcete předem načíst data. Vzhledem k tomu, že převzetí služeb při selhání je bude ruční a zahrnuje další postup převzetí služeb při selhání v hotové aplikace zásobníku, další čas uvést dolů virtuálního počítače, jeho velikost a znovu spusťte virtuální počítač je přijatelná.

> [!NOTE]
> I bez předběžně načíst data v cílové replikaci HANA systému, budete potřebovat minimálně 64 GB paměti a nad rámec tohoto dostatek paměti pro rowstore data mějte na paměti cílové instance.

![Dva virtuální počítače prostřednictvím dvou oblastí](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> V této konfiguraci, nemůžete zadat plánovaný bod obnovení = 0, protože vaše režim replikace systému HANA je asynchronní. Pokud budete muset zadat plánovaný bod obnovení = 0, tato konfigurace není konfigurace výběru.

Ke konfiguraci předběžné načítání dat může být malé změny v konfiguraci. Ale vzhledem k povaze ruční převzetí služeb při selhání a skutečnost, že aplikačními vrstvami potřebovat přesunout do druhé oblasti také, nemusí mít smysl předem načíst data. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>Kombinování dostupnosti v rámci jedné oblasti a v oblastech 
Kombinace dostupnosti v rámci a v oblastech mohou bude týkat:

- Požadavek na RPO = 0 v rámci oblasti Azure.
- Není ochoten nebo mohou mít globální operace společnosti ovlivnění ve hlavní přírodní havárie, týká větší oblast. Stejně jako tomu bylo v případě některých hurikány, které dosáhl Karibiku v posledních několika letech.
- Můžete zadat vývozu vyžádání vzdálenosti mezi primární a sekundární lokalitu, která jsou jasně nad co Azure dostupnost zóny.

 
V takových případech byste nakonfigurovali jaké volání SAP [konfigurace SAP HANA Multitier systému replikace](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) s replikací systému HANA. Architektura by vypadat podobně jako:

![tři virtuální počítače prostřednictvím dvou oblastí](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

Tato konfigurace poskytuje RPO = 0 s malé RTO časy v rámci primární oblasti a navíc poskytuje sestup plánovaný bod obnovení v případě přesunu přes druhý oblasti. Časy RTO ve druhé oblasti jsou závislé na tom, jestli nakonfigurujete předběžně načíst data nebo ne. V mnoha případech zákazníka virtuální počítač v sekundární oblasti slouží ke spouštění testovacího systému. V důsledku této využití nemůže být předem načíst data.

> [!NOTE]
> Vzhledem k tomu, že používáte operační režim logreplay pro replikaci systému HANA přecházející z vrstvy 1 na vrstvu 2 (synchronní replikace v primární oblasti), replikace mezi vrstvy 2 a 3 úrovně (replikace do sekundární lokality) nemůže být v operaci delta_datashipping režim. Podrobnosti o operaci režimy a určitá omezení popsané podle SAP v [operaci režimy pro replikaci systému SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html). 

## <a name="next-steps"></a>Další postup
Pokud potřebujete podrobné pokyny o tom, jak nastavit konfiguraci v Azure, přečtěte si články:

- [Nastavení replikace systému SAP HANA ve virtuálních počítačích Azure](sap-hana-high-availability.md)
- [Vaše SAP na Azure – část 4 – vysoká dostupnost pro použití replikace systému SAP HANA](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
