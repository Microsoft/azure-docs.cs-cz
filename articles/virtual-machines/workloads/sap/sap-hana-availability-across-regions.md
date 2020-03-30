---
title: Dostupnost SAP HANA v různých oblastech Azure | Dokumenty společnosti Microsoft
description: Přehled aspektů dostupnosti při spuštění SAP HANA na virtuálních počítačích Azure ve více oblastech Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 68a393865038722f2fd7fa5e42334f8d5e760951
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70078853"
---
# <a name="sap-hana-availability-across-azure-regions"></a>Dostupnost SAP HANA v různých oblastech Azure

Tento článek popisuje scénáře související s dostupností SAP HANA v různých oblastech Azure. Vzhledem k vzdálenosti mezi oblastmi Azure, nastavení sap hana dostupnost ve více oblastech Azure zahrnuje zvláštní aspekty.

## <a name="why-deploy-across-multiple-azure-regions"></a>Proč nasazovat ve více oblastech Azure

Oblasti Azure jsou často odděleny velkými vzdálenostmi. V závislosti na geopolitické oblasti může být vzdálenost mezi oblastmi Azure stovky mil nebo dokonce několik tisíc mil, jako ve Spojených státech. Vzhledem k vzdálenosti, síťový provoz mezi prostředky, které jsou nasazené ve dvou různých oblastech Azure zaznamenat významné latence sítě roundtrip. Latence je dostatečně významná k vyloučení synchronní výměny dat mezi dvěma instancemi SAP HANA pod typickými úlohami SAP. 

Na druhou stranu organizace mají často požadavek na vzdálenost mezi umístěním primárního datového centra a sekundárním datovým centrem. Požadavek na vzdálenost pomáhá zajistit dostupnost v případě, že dojde k přírodní katastrofě v širší zeměpisné poloze. Příklady zahrnují hurikány, které zasáhly Karibik a Floridu v září a říjnu 2017. Vaše organizace může mít alespoň požadavek na minimální vzdálenost. Pro většinu zákazníků Azure vyžaduje definice minimální vzdálenosti, že navrhnete dostupnost napříč [oblastmi Azure](https://azure.microsoft.com/regions/). Vzhledem k tomu, že vzdálenost mezi dvěma oblastmi Azure je příliš velká na to, aby bylo možné použít režim synchronní replikace HANA, mohou vás požadavky rto a rpo vynutit nasazení konfigurací dostupnosti v jedné oblasti a pak je doplnit o další nasazení v sekundě. Oblasti.

Dalším aspektem, který je třeba zvážit v tomto scénáři, je převzetí služeb při selhání a přesměrování klienta. Předpokladem je, že převzetí služeb při selhání mezi instancemi SAP HANA ve dvou různých oblastech Azure je vždy ruční převzetí služeb při selhání. Vzhledem k tomu, že režim replikace replikace replikace systému SAP HANA je nastavena na asynchronní, existuje potenciál, že data potvrzená v primární instanci HANA ještě nebyla provedena do sekundární instance HANA. Automatické převzetí služeb při selhání proto není možnost pro konfigurace, kde replikace je asynchronní. I s ručně řízené převzetí služeb při selhání, jako v cvičení převzetí služeb při selhání, musíte přijmout opatření k zajištění, že všechna potvrzená data na primární straně se dostal do sekundární instance před ručním přechodem do jiné oblasti Azure.
 
Virtuální síť Azure používá jiný rozsah IP adres. IP adresy se nasazují v druhé oblasti Azure. Takže je třeba změnit konfiguraci klienta SAP HANA, nebo pokud možno musíte vytvořit kroky ke změně překladu názvů. Tímto způsobem jsou klienti přesměrováni na adresu IP serveru nové sekundární lokality. Další informace naleznete v článku SAP [Obnovení připojení klienta po převzetí](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html).   

## <a name="simple-availability-between-two-azure-regions"></a>Jednoduchá dostupnost mezi dvěma oblastmi Azure

Můžete se rozhodnout, že nebude umístit žádné konfigurace dostupnosti na místě v rámci jedné oblasti, ale stále mají požadavek, aby zatížení obsluhované, pokud dojde k havárii. Typické případy pro takové scénáře jsou neprodukční systémy. I když je systém vypnutý na půl dne nebo dokonce den, je udržitelný, nemůžete dovolit, aby byl systém nedostupný po dobu 48 hodin nebo déle. Chcete-li nastavení méně nákladné, spusťte jiný systém, který je ještě méně důležité ve virtuálním počítači. Druhý systém funguje jako cíl. Můžete také velikost virtuálního počítače v sekundární oblasti být menší a zvolte, aby přednačíst data. Vzhledem k tomu, že převzetí služeb při selhání je ruční a zahrnuje mnoho dalších kroků k převzetí služeb při selhání přes kompletní zásobníku aplikace, další čas vypnout virtuální počítač, změnit jeho velikost a restartovat virtuální počítač je přijatelné.

Pokud používáte scénář sdílení cíle zotavení po Havárii se systémem qa v jednom virtuálním počítače, je třeba vzít v úvahu tyto aspekty:

- Existují dva [provozní režimy](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html) s delta_datashipping a logreplay, které jsou k dispozici pro takový scénář
- Oba provozní režimy mají různé požadavky na paměť bez předběžného načítání dat
- Delta_datashipping může vyžadovat výrazně méně paměti bez možnosti předběžného načtení, než může vyžadovat logreplay. Viz kapitola 4.3 dokumentu SAP [Jak provádět replikaci systému pro SAP HANA](https://archive.sap.com/kmuuid2/9049e009-b717-3110-ccbd-e14c277d84a3/How%20to%20Perform%20System%20Replication%20for%20SAP%20HANA.pdf)
- Požadavek na paměť protokolu replay provozní režim bez předběžného načtení není deterministický a závisí na columnstore struktury načten. V extrémních případech může vyžadovat 50 % paměti primární instance. Paměť pro režim operace logreplay je nezávislá na tom, zda jste zvolili nastavení předinstalovaného data.


![Diagram dvou virtuálních připojení ve dvou oblastech](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> V této konfiguraci nelze zadat RPO = 0, protože režim replikace systému HANA je asynchronní. Pokud potřebujete zadat RPO = 0, tato konfigurace není konfigurace volby.

Malou změnou, kterou můžete provést v konfiguraci, může být konfigurace dat jako předběžného načtení. Však vzhledem k ruční povaze převzetí služeb při selhání a skutečnost, že vrstvy aplikace také nutné přesunout do druhé oblasti, nemusí mít smysl přednačtení dat. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>Kombinování dostupnosti v rámci jednoho regionu a napříč oblastmi 

Kombinace dostupnosti v rámci regionů a napříč oblastmi může být způsobena těmito faktory:

- Požadavek RPO = 0 v rámci oblasti Azure.
- Organizace není ochotna nebo schopna mít globální operace ovlivněné velkou přírodní katastrofou, která postihuje větší region. To byl případ některých hurikánů, které zasáhly Karibik v posledních několika letech.
- Předpisy, které vyžadují vzdálenosti mezi primární a sekundární lokality, které jsou jasně nad rámec toho, co azure dostupnost zóny můžete poskytnout.

V těchto případech můžete nastavit, co SAP nazývá [konfigurace vícevrstvé replikace systému SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) pomocí replikace systému HANA. Architektura by vypadala takto:

![Diagram tří virtuálních připojení ve dvou oblastech](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

Společnost SAP zavedla [vícecílovou replikaci systému](https://help.sap.com/viewer/42668af650f84f9384a3337bcd373692/2.0.03/en-US/0b2c70836865414a8c65463180d18fec.html) s HANA 2.0 SPS3. Vícecílová replikace systému přináší některé výhody ve scénářích aktualizace. Například lokalita zotavení po Havárii (oblast 2) není ovlivněna, pokud je sekundární lokalita HA z důvodu údržby nebo aktualizací vypnuta. Další informace o replikaci vícesměrového systému HANA naleznete [zde](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/ba457510958241889a459e606bbcf3d3.html).
Možná architektura s replikací s více cíli by vypadala takto:

![Diagram tří virtuálních připojení nad dvěma oblastmi milti-cíl](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_3VMs.PNG)

Pokud má organizace požadavky na připravenost na vysokou dostupnost v oblasti Azure ve druhé části(DR), bude architektura vypadat takto:

![Diagram tří virtuálních připojení nad dvěma oblastmi milti-cíl](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_4VMs.PNG)


Pomocí logreplay jako provozní režim, tato konfigurace poskytuje RPO = 0, s nízkou RTO, v rámci primární oblasti. Konfigurace také poskytuje slušné RPO, pokud se jedná o přesun do druhé oblasti. Časy RTO v druhé oblasti závisí na tom, zda jsou data předinstalována. Mnoho zákazníků používá virtuální ho virtuálního zařízení v sekundární oblasti ke spuštění testovacího systému. V takovém případě použití nelze data předčíst.

> [!IMPORTANT]
> Provozní režimy mezi různými úrovněmi musí být homogenní. Nelze **can't** použít logreply jako provozní režim mezi úrovní 1 a úrovní 2 a delta_datashipping k dodání úrovně 3. Můžete zvolit pouze jeden nebo druhý provozní režim, který musí být konzistentní pro všechny úrovně. Vzhledem k tomu, že delta_datashipping není vhodné poskytnout rpo = 0, jediný rozumný provozní režim pro takovou vícevrstvou konfiguraci zůstává logreplay. Podrobnosti o provozních režimech a některých omezeních naleznete v článku SAP [Provozní režimy pro replikaci systému SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html). 

## <a name="next-steps"></a>Další kroky

Podrobné pokyny k nastavení těchto konfigurací v Azure najdete v následujících tématech:

- [Nastavení replikace systému SAP HANA ve virtuálních počítačích Azure](sap-hana-high-availability.md)
- [Vysoká dostupnost pro SAP HANA pomocí replikace systému](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
