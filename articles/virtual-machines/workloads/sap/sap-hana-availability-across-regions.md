---
title: Dostupnost SAP HANA v různých oblastech Azure | Dokumentace Microsoftu
description: Přehled aspektů dostupnost při spuštění SAP HANA na virtuálních počítačích Azure v několika oblastech Azure.
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
ms.date: 09/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ae03e1498d948e7d044561c3e6bea8c343d7b165
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713965"
---
# <a name="sap-hana-availability-across-azure-regions"></a>Dostupnost SAP HANA v různých oblastech Azure

Tento článek popisuje scénáře týkající se dostupnosti SAP HANA v různých oblastech Azure. Z důvodu vzdálenosti mezi oblastmi Azure nastavení dostupnosti SAP HANA v několika oblastech Azure zahrnuje zvláštní požadavky.

## <a name="why-deploy-across-multiple-azure-regions"></a>Proč nasadit napříč několika oblastmi Azure

Oblasti Azure, které často jsou odděleny dlouhé vzdálenosti. V závislosti na geopolitické oblasti vzdálenosti mezi oblastmi Azure pravděpodobně stovky mil, nebo dokonce několik tisíc mil, jako jsou ve Spojených státech. Z důvodu vzdálenost síťový provoz mezi prostředky, které jsou nasazené ve dvou různých oblastech Azure prostředí umožňujícím zpětnou transformaci významné sítích s latencí. Latence je natolik závažné, aby vyloučení synchronní výměna dat mezi dvě instance SAP HANA v části typickou úloh SAP. 

Na druhé straně organizace často mají požadavek vzdálenost mezi umístění primárního datového centra a sekundárního datacentra. Požadavek na dálku zajišťuje dostupnost případě přírodní katastrofě v širší zeměpisné oblasti. Mezi příklady patří hurikány, které přístupů Karibská oblast a Florida v září a. října 2017. Vaše organizace může mít alespoň požadavek na minimální vzdálenost. Pro většinu služeb Azure zákazníky definice minimální vzdálenost vyžaduje, abyste návrhu pro dostupnost v rámci [oblastí Azure](https://azure.microsoft.com/regions/). Protože je příliš velká pro použití režimu synchronní replikace HANA vzdálenost mezi dvěma oblastmi Azure, RTO a RPO požadavky může vynutit nasazení konfigurace dostupnost v jedné oblasti a pak doplnit další nasazení za sekundu oblast.

Dalším aspektem vzít v úvahu v tomto scénáři je převzetí služeb při selhání a přesměrování klienta. Předpokladem je, že převzetí služeb při selhání mezi instance systému SAP HANA ve dvou různých oblastech Azure vždy je ruční převzetí služeb při selhání. Vzhledem k tomu, replikace systému SAP HANA má nastaven režim replikace asynchronní, existuje riziko, že předávání dat v primární instance HANA nebyl dosud proveden ho sekundární instance HANA. Proto automatické převzetí služeb při selhání není možnost konfigurace, kde je asynchronní replikace. I přes ručně řízené převzetí služeb při selhání, stejně jako v cvičení převzetí služeb při selhání musíte přijmout opatření pro zajištění, že všechny potvrzené data na primární straně dostal sekundární instanci před ručně přesunout do jiné oblasti Azure.
 
Virtuální síť Azure používá jiný rozsah IP adres. IP adresy se nasazují do druhé oblasti Azure. Buď musíte proto chcete-li změnit konfiguraci klienta SAP HANA nebo nejlépe, je potřeba vytvořit postup pro změnu překlad názvů. Tímto způsobem, klienti budou přesměrovaní na IP adresu serveru nové sekundární lokality. Další informace najdete v článku SAP [obnova připojení klienta po převzetí](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html).   

## <a name="simple-availability-between-two-azure-regions"></a>Jednoduché dostupnosti mezi dvěma oblastmi Azure

Můžete rozhodnout není zavedený žádnou konfiguraci dostupnosti v rámci jedné oblasti, ale ještě další požadavek mít úlohu obsluhuje, pokud dojde k havárii. Typické případy pro systémy, jako je to jsou neprovozním systémy. Sice udržitelné poloviční denně nebo dokonce i za den s systému dolů nemůže povolit systém, aby byl k dispozici na 48 hodin nebo více. Chcete-li nastavení levnější, spusťte další systémy, které je i méně důležitá ve virtuálním počítači. Druhý systém funguje jako cíl. Můžete také velikost virtuálního počítače v sekundární oblasti menší a rozhodnou předběžné načtení dat. Protože převzetí služeb při selhání je ruční a zahrnuje mnoho kroků další převzít služby při selhání aplikace dokončena a zásobníku, je přijatelné další čas vypnout virtuální počítač, změňte jeho velikost a restartujte virtuální počítač.

Pokud používáte scénáři sdílení cíle zotavení po Havárii systémem dotazů a odpovědí v jeden virtuální počítač, budete muset vzít v úvahu tyto aspekty:

- Existují dva [režimy operace](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html) delta_datashipping a logreplay, které jsou k dispozici pro takové situaci
- Oba režimy operace mají jiné paměťové požadavky bez předběžného načítání dat
- Delta_datashipping potřebovat výrazně méně paměti bez předběžného načítání možnosti než logreplay může vyžadovat. Naleznete v kapitole 4.3 dokument SAP [jak k provádění systémové replikace pro SAP HANA](https://archive.sap.com/kmuuid2/9049e009-b717-3110-ccbd-e14c277d84a3/How%20to%20Perform%20System%20Replication%20for%20SAP%20HANA.pdf)
- Požadavek na paměť logreplay operace režimu bez předběžné načtení není deterministický. a závisí na načíst columnstore struktury. V extrémních případech můžete potřebovat 50 % paměti primární instance. Paměť pro režim operace logreplay je nezávislá na tom, jestli jste se rozhodli mají data předem nastavené.


![Diagram dvou virtuálních počítačů přes dvě oblasti](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> V této konfiguraci je nelze zadat plánovaný bod obnovení = 0, protože režim replikace systému HANA je asynchronní. Pokud je potřeba zadat plánovaný bod obnovení = 0, tato konfigurace není konfigurace podle výběru.

Malé změny provedené v konfiguraci může být konfigurace dat jako předběžné načtení. Ale vzhledem k povaze ruční převzetí služeb při selhání a skutečnosti, že aplikace potřebovat přesunout do druhé oblasti, se nemusí mít smysl předběžné načtení dat. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>Kombinovat dostupnost v rámci jedné oblasti i napříč oblastmi 

Kombinace dostupnost v různých oblastech i mohou být způsobeny tyto faktory:

- Požadavek cíle bodu obnovení = 0 v rámci oblasti Azure.
- Organizace není ochoten nebo schopen mít globální operace ovlivněny hlavní přírodní katastrofa, který má vliv větší oblasti. To se stává třeba u některých hurikány, které přístupů Karibiku během posledních několika let.
- Předpisy o vývozu vyžádání vzdálenosti mezi primárních a sekundárních lokalit, které jsou jasně nad rámec jaké dostupnost Azure může poskytnout zóny.

V těchto případech můžete nastavit jaké SAP volání [konfigurace replikace vícevrstvé systému SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) s využitím systémové replikace HANA. Architektura bude vypadat takto:

![Diagram tři virtuální počítače prostřednictvím dvou oblastech](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

Použití logreplay jako režim operace, tato konfigurace poskytuje plánovaný bod obnovení = 0, s nízké RTO, v rámci primární oblasti. Konfigurace také poskytuje vrazíme cíle bodu obnovení, pokud se jedná o přesun do druhé oblasti. RTO časy v druhé oblasti jsou závislé na tom, jestli se předem načtou data. Mnozí uživatelé používají k provozování systému testovacího virtuálního počítače v sekundární oblasti. V tomto případ použití, nemůže být předem načtena data.

> [!IMPORTANT]
> Režimy operace mezi různé vrstvy musí být homogenní. Můžete **nelze** použít logreply režim operace mezi 1 a vrstvu 2 a delta_datashipping slouží k poskytování vrstvy 3. Můžete pouze jednu nebo jiné operace režim, ve kterém musí být konzistentní pro všechny úrovně. Protože delta_datashipping není vhodné poskytnout plánovaný bod obnovení = 0, režim pouze přiměřené operace pro taková více úroveň konfigurace zůstane logreplay. Podrobnosti o operaci režimech a určitá omezení, najdete v článku SAP [režimy operace pro systémové replikace SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html). 

## <a name="next-steps"></a>Další postup

Podrobné pokyny k nastavení těchto konfigurací v Azure najdete v tématu:

- [Nastavení systémové replikace SAP HANA ve virtuálních počítačích Azure](sap-hana-high-availability.md)
- [Vysoká dostupnost pro SAP HANA s využitím systémové replikace](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
