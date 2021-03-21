---
title: SAP HANA dostupnost napříč oblastmi Azure | Microsoft Docs
description: Přehled informací o dostupnosti při spuštění SAP HANA na virtuálních počítačích Azure ve více oblastech Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f925e72493a94bcdbde64bdfbcef6acf83977f52
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101669722"
---
# <a name="sap-hana-availability-across-azure-regions"></a>SAP HANA dostupnost napříč oblastmi Azure

Tento článek popisuje scénáře týkající se SAP HANA dostupnosti napříč různými oblastmi Azure. Z důvodu vzdálenosti mezi oblastmi Azure, nastavení SAP HANA dostupnosti ve více oblastech Azure zahrnuje zvláštní požadavky.

## <a name="why-deploy-across-multiple-azure-regions"></a>Důvody nasazení v několika oblastech Azure

Oblasti Azure jsou často oddělené velkými vzdálenostmi. V závislosti na geopolitické oblasti může být vzdálenost mezi oblastmi Azure stovky mil nebo dokonce několik tisíc mil, jako v USA. Z důvodu vzdálenosti může síťový provoz mezi prostředky, které jsou nasazené ve dvou různých oblastech Azure, výrazně vycházet z větší latence při zpětném volání sítě. Latence je dostatečně významná pro vyloučení synchronního výměny dat mezi dvěma SAP HANA instancemi v rámci typických úloh SAP. 

Na druhé straně organizace často mají požadavek na vzdálenost mezi umístěním primárního datacentra a sekundárním datacenterm. Požadavek na vzdálenost pomáhá zajistit dostupnost, pokud dojde k přirozené havárii v širším geografickém umístění. Příklady zahrnují hurikány, který dosáhl oblasti karibské a Florida v září a říjnu 2017. Vaše organizace může mít aspoň minimální požadavek na minimální délku. Pro většinu zákazníků Azure vyžaduje definice minimální vzdálenosti návrh a dostupnost v různých [oblastech Azure](https://azure.microsoft.com/regions/). Vzhledem k tomu, že vzdálenost mezi dvěma oblastmi Azure je moc velká a nedá se použít režim synchronní replikace HANA, můžou požadavky RTO a RPO vynutit nasazení konfigurací dostupnosti v jedné oblasti a potom doplnit další nasazení v druhé oblasti.

Dalším aspektem, který je třeba vzít v úvahu, je převzetí služeb při selhání a přesměrování klienta. Předpokladem je, že převzetí služeb při selhání mezi instancemi SAP HANA ve dvou různých oblastech Azure vždycky představuje ruční převzetí služeb při selhání. Vzhledem k tomu, že režim replikace SAP HANA replikace systému je nastaven na hodnotu asynchronní, je možné, že data potvrzená v primární instanci HANA ještě neudělala sekundární instance HANA. Proto automatické převzetí služeb při selhání není možností pro konfigurace, kde je replikace asynchronní. I s ručně kontrolovaným převzetím služeb při selhání, jako v rámci cvičení při převzetí služeb při selhání, musíte přijmout opatření, abyste zajistili, že všechna potvrzená data na primární straně se před ručním přesunutím do jiné oblasti Azure převezmou v rámci sekundární instance.
 
Azure Virtual Network používá jiný rozsah IP adres. IP adresy se nasazují ve druhé oblasti Azure. Proto je třeba změnit konfiguraci klienta SAP HANA, případně můžete vytvořit kroky pro změnu překladu názvů. Tímto způsobem budou klienti přesměrováni na IP adresu serveru nové sekundární lokality. Další informace najdete v článku věnovaném [obnovení připojení klienta SAP po jeho převzetí](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html).   

## <a name="simple-availability-between-two-azure-regions"></a>Jednoduchá dostupnost mezi dvěma oblastmi Azure

V rámci jedné oblasti se můžete rozhodnout, že neumístíte žádnou konfiguraci dostupnosti, ale pokud dojde k havárii, bude mít požadavek na obsluhu zatížení. Typické případy takových scénářů jsou nevýrobní systémy. I když je systém rozmístěný po dobu půl dne nebo dokonce i jeden den, nemůžete, aby byl systém k dispozici po dobu 48 hodin nebo déle. Pokud chcete nastavit méně nákladné nastavení, spusťte na virtuálním počítači jiný systém, který je ještě méně důležitý. Další systém funguje jako cíl. Můžete také změnit velikost virtuálního počítače v sekundární oblasti na menší a zvolit, aby se data nečetla. Vzhledem k tomu, že převzetí služeb při selhání je ruční a zahrnuje mnoho dalších kroků pro převzetí služeb při selhání kompletního zásobníku aplikace, další čas vypnutí virtuálního počítače, jeho změna velikosti a restartování virtuálního počítače je přijatelné.

Pokud používáte scénář sdílení cíle DR se systémem QA v jednom virtuálním počítači, musíte vzít v úvahu tyto okolnosti:

- Existují dva [režimy operací](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html) s delta_datashipping a logreplay, které jsou k dispozici pro takový scénář.
- Oba režimy operace mají různé požadavky na paměť bez přednačtení dat.
- Delta_datashipping může vyžadovat drastickou méně paměti bez možnosti přednačtení, než logreplay. Viz kapitola 4,3 dokumentu SAP [Jak provést replikaci systému pro SAP HANA](https://archive.sap.com/kmuuid2/9049e009-b717-3110-ccbd-e14c277d84a3/How%20to%20Perform%20System%20Replication%20for%20SAP%20HANA.pdf)
- Požadavek na paměť pro režim operace logreplay bez přednačtení není deterministický a závisí na načtených strukturách columnstore. V extrémních případech může být nutné vyžadovat 50% paměti primární instance. Paměť pro režim operace logreplay je nezávislá na tom, jestli se rozhodnete, že chcete předčítat sadu dat, nebo ne.


![Diagram dvou virtuálních počítačů ve dvou oblastech](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> V této konfiguraci nemůžete zadat RPO = 0, protože režim replikace systému HANA je asynchronní. Pokud potřebujete zadat RPO = 0, tato konfigurace není konfigurací volby.

Malá změna, kterou můžete v konfiguraci provést, může být konfigurace dat jako přednačtení. Nicméně vzhledem k ručnímu charakteru převzetí služeb při selhání a skutečnost, že vrstvy aplikace potřebují také přejít do druhé oblasti, nemusí mít smysl předčítat data. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>Kombinování dostupnosti v rámci jedné oblasti a napříč oblastmi 

Kombinací dostupnosti v rámci a napříč oblastmi můžou být tyto faktory založené na těchto faktorech:

- Požadavek RPO = 0 v oblasti Azure.
- Organizace není ochotná ani schopná mít globální operace ovlivněné většími přirozenými pohromě, které mají vliv na větší oblast. V tomto případě se jednalo o některé hurikány, které dosáhly oblasti karibské za posledních několik let.
- Předpisy, které vyžadují vzdálenost mezi primárními a sekundárními lokalitami, které jsou jasně nad rámec toho, co můžou poskytovat zóny dostupnosti Azure.

V těchto případech můžete nastavit, co SAP volá [SAP HANA konfiguraci replikace s více vrstvami](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) , pomocí replikace systému Hana. Architektura by vypadala takto:

![Diagram tří virtuálních počítačů ve dvou oblastech](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

SAP zavádí [replikaci s více cílovými systémy](https://help.sap.com/viewer/42668af650f84f9384a3337bcd373692/2.0.03/en-US/0b2c70836865414a8c65463180d18fec.html) s Hana 2,0 SPS3. Replikace s více cílovými systémy přináší ve scénářích aktualizace některé výhody. Například lokalita DR (region 2) nemá vliv, pokud je sekundární lokalita HA mimo provoz kvůli údržbě nebo aktualizacím. Další informace o replikaci více cílů systému HANA najdete [tady](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/ba457510958241889a459e606bbcf3d3.html).
Možná architektura s replikací s více cíli vypadá takto:

![Diagram tří virtuálních počítačů ve dvou oblastech milti-Target](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_3VMs.PNG)

Pokud má organizace požadavky na připravenost na vysokou dostupnost v druhé oblasti Azure (DR), pak by architektura vypadala takto:

![Diagram znázorňující organizaci, která má požadavky na připravenost vysoké dostupnosti v druhé oblasti Azure (DR).](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_4VMs.PNG)


Tato konfigurace poskytuje v rámci operace logreplay jako provozní režim RPO RPO = 0 s nízkou RTO v rámci primární oblasti. Konfigurace také poskytuje dát RPO, pokud je zahrnut přesun do druhé oblasti. Doba RTO ve druhé oblasti závisí na tom, zda jsou data předem načtena. Mnoho zákazníků používá virtuální počítač v sekundární oblasti ke spuštění testovacího systému. V takovém případě se data nedají předem načíst.

> [!IMPORTANT]
> Režimy operací mezi různými vrstvami musí být homogenní. Logreply se **nedá** použít jako provozní režim mezi vrstvou 1 a vrstvou 2 a delta_datashipping k poskytnutí vrstvy 3. Můžete zvolit jenom jeden nebo jiný provozní režim, který musí být konzistentní pro všechny úrovně. Vzhledem k tomu, že delta_datashipping není vhodný k poskytnutí cíle bodu obnovení (RPO), zůstane logreplay jenom přiměřený provozní režim pro takovou konfiguraci s více vrstvami. Podrobnosti o režimech operací a některých omezeních najdete v článku [provozní režimy článků SAP pro replikaci SAP HANA systému](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html). 

## <a name="next-steps"></a>Další kroky

Podrobné pokyny k nastavení těchto konfigurací v Azure najdete tady:

- [Nastavení replikace systému SAP HANA na virtuálních počítačích Azure](sap-hana-high-availability.md)
- [Vysoká dostupnost pro SAP HANA pomocí replikace systému](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
