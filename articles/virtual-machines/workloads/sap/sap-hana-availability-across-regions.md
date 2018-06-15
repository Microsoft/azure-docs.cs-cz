---
title: Dostupnost SAP HANA v rámci oblasti Azure | Microsoft Docs
description: Přehled aspektů dostupnosti při spuštění SAP HANA na virtuálních počítačích Azure v několika oblastmi Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/26/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: edbd1885dd529e4ccd38f2012d56865a2147f64d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
ms.locfileid: "30842267"
---
# <a name="sap-hana-availability-across-azure-regions"></a>Dostupnost SAP HANA v rámci oblasti Azure

Tento článek popisuje scénáře související s SAP HANA dostupnost v různých oblastech Azure. Z důvodu vzdálenost mezi oblastmi Azure nastavení dostupnosti SAP HANA v několika oblastech Azure zahrnuje zvláštní požadavky.

## <a name="why-deploy-across-multiple-azure-regions"></a>Proč nasazení nad několika oblastmi Azure

Oblastí Azure jsou často oddělené bránou dlouhé vzdálenosti. V závislosti na geopolitické oblasti může vzdálenost mezi oblastmi Azure být stovky miles nebo i několika tisíc miles, jako je ve Spojených státech amerických. Z důvodu vzdálenost prostředí síťový provoz mezi prostředky, které jsou nasazeny ve dvou různých oblastech Azure latence umožňujícím zpětnou transformaci významné sítě. Latence je dostatečně významné vyloučit exchange synchronní data mezi dvěma instancemi SAP HANA u typické zatížení SAP. 

Na druhé straně mají často organizace požadavek vzdálenost mezi umístění primární datové centrum a do sekundárního datacentra. Vzdálenost požadavek pomáhá zajištění dostupnosti v případě přírodní katastrofě v širší zeměpisné umístění. Mezi příklady patří hurikány, které dosáhl karibské a Florida v září a říjen 2017. Vaše organizace může mít alespoň požadavek na minimální vzdálenost. Většina Azure zákazníků definici minimální vzdálenost vyžaduje, abyste návrh pro dostupnost v rámci [oblastí Azure](https://azure.microsoft.com/regions/). Protože je příliš velká pro použití režimu synchronní replikace HANA vzdálenost mezi dvěma oblastí Azure, požadavky na RTO a plánovaný bod obnovení může vynutit nasazení konfigurace dostupnosti v jedné oblasti, a pak doplnit s další nasazení za sekundu oblast.

Další aspekt vzít v úvahu v tomto scénáři je převzetí služeb při selhání a přesměrování klienta. Předpokladem je, že převzetí služeb při selhání mezi instancemi SAP HANA ve dvou různých oblastech Azure vždy je ruční převzetí služeb při selhání. Protože režim replikace replikace systému SAP HANA je nastavena na asynchronní, existuje možnost, že data potvrzeno u primární instance HANA dosud neprovedl ho na sekundární HANA instanci. Proto automatické převzetí služeb při selhání není možnost konfigurace, kde je asynchronní replikaci. I když ručně řízené převzetí služeb při selhání, jako cvičení převzetí služeb při selhání budete muset opatření k zajištění, že všechna potvrzená data na primární straně dostal sekundární instance před ručně přesouvání v dané oblasti Azure.
 
Virtuální síť Azure používá jiný rozsah IP adres. IP adresy jsou nasazeny v druhé oblasti Azure. Takže byste měli změnit konfiguraci klienta SAP HANA nebo pokud možno, budete muset vytvořit postup změny překlad IP adresy. Tímto způsobem, klienti jsou přesměrovány na IP adresu serveru nové sekundární lokality. Další informace najdete v článku SAP [obnovení připojení klienta po převzetí](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html).   

## <a name="simple-availability-between-two-azure-regions"></a>Jednoduché dostupnosti mezi dvěma oblastmi Azure

Můžete se umístí všechny konfigurace dostupnosti zavedené v jedné oblasti, ale stále mít vyžádání tak, aby měl úlohy zpracování, pokud dojde k havárii. Typickými případy systémů, například to jsou neprovozním systémy. I když s systému dolů pro poloviční denně nebo i den udržitelné, nemůže povolit systém, aby byl k dispozici pro 48 hodin nebo více. Chcete-li instalace levněji, spusťte jiného systému, který je i méně důležité ve virtuálním počítači. Jiné systémy funguje jako cíl. Také můžete velikost virtuálního počítače v sekundární oblasti menší a zvolit nepoužívání předběžně načíst data. Protože převzetí služeb při selhání je ruční a zahrnuje další postup převzetí služeb při selhání v zásobníku hotové aplikace, je přijatelné další čas vypnutí virtuálního počítače, jeho velikost a pak restartujte virtuální počítač.

> [!NOTE]
> I když nepoužijete předběžného načítání dat v cílem replikace systému HANA, potřebujete minimálně 64 GB paměti. Budete také potřebovat dostatek paměti kromě 64 GB dat rowstore mějte na paměti cílové instance.

![Diagram dva virtuální počítače prostřednictvím dvou oblastí](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> V této konfiguraci, nemůžete zadat plánovaný bod obnovení = 0, protože vaše režim replikace systému HANA je asynchronní. Pokud budete muset zadat plánovaný bod obnovení = 0, tato konfigurace není konfigurace výběru.

Malé změny, která můžete použít v konfiguraci může být konfigurace dat jako předběžného načítání. Však vzhledem k povaze ruční převzetí služeb při selhání a skutečnost, že aplikace vrstvy také potřebovat přesunout do druhé oblasti, se nemusí mít smysl předběžně načíst data. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>Kombinování dostupnosti v rámci jedné oblasti a v oblastech 

Kombinace dostupnosti v rámci a v oblastech mohou být způsobeny tyto faktory:

- Požadavek RPO = 0 v rámci oblasti Azure.
- Organizace není ochoten nebo mohou mít vliv na hlavní přirozené katastrofická, který má vliv na větší oblast globální operace. To bylo v případě některých hurikány, které dosáhl Karibiku v posledních několika letech.
- Předpisy, které vyžádání vzdálenosti mezi primárních a sekundárních lokalit, které jsou jasně nad rámec co Azure dostupnosti může poskytnout zóny.

V těchto případech můžete nastavit tak, jaký SAP volání [konfiguraci replikace vícevrstvé systému SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) za použití replikace systému HANA. Architektura bude vypadat takto:

![Diagram tři virtuální počítače prostřednictvím dvou oblastí](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

Tato konfigurace poskytuje RPO = 0, s nízkou RTO, v rámci primární oblasti. Konfigurace také poskytuje dostatečnou plánovaný bod obnovení, pokud se jedná o přesunu druhý oblasti. Časy RTO v oblasti druhý jsou závislé na tom, jestli se předem načtou data. Mnoho zákazníků použít ke spuštění testovacího systému virtuálního počítače v sekundární oblasti. V tom, že případ použití, nelze předem načtena data.

> [!NOTE]
> Vzhledem k tomu, že používáte **logreplay** operační režim pro replikaci systému HANA přejdete z vrstvy 1 vrstvy 2 (synchronní replikace v primární oblasti), replikace mezi vrstvy 2 a vrstvy 3 (replikace do sekundární lokality) nemůže být v **delta_datashipping** operační režim. Informace o režimech operace a určitá omezení, najdete v článku SAP [operaci režimy pro replikaci systému SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html). 

## <a name="next-steps"></a>Další postup

Podrobné pokyny o nastavení těchto konfigurací v Azure najdete v tématu:

- [Nastavení replikace systému SAP HANA ve virtuálních počítačích Azure](sap-hana-high-availability.md)
- [Vysoká dostupnost pro SAP HANA za použití replikace systému](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
