---
title: Vysoká dostupnost a zotavení po havárii SAP Hana v Azure (velké instance) | Dokumentace Microsoftu
description: Vytvoření vysoké dostupnosti a plánování zotavení po havárii SAP Hana v Azure (velké instance)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 61f536ee5eb27982bd63daf0b278e6c7a836fe08
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44390735"
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Velké instance SAP HANA vysokou dostupnost a zotavení po havárii v Azure 

>[!IMPORTANT]
>Tato dokumentace je nahrazen dokumentace správy SAP HANA nebo SAP poznámky. Očekává se, zda má čtečka důkladného porozumění a odborných znalostí v SAP HANA správu a provoz, zejména s tématy zálohování, obnovení, vysokou dostupnost a zotavení po havárii.

Je důležité věnovat kroky a postupy provést ve vašem prostředí a s HANA verzí a verzí. Některé procesy popsané v této dokumentaci je zjednodušená pro lepší porozumění obecné a neměly by má být použit jako podrobný postup pro konečný výsledek operace příruček. Pokud chcete vytvořit operaci příruček pro konkrétní konfiguraci, musíte pro testování a výkon vašich procesů a zdokumentujte procesy související s konkrétní konfigurací. 


Vysoká dostupnost a zotavení po havárii (DR) jsou důležité aspekty spuštění klíčových SAP HANA v Azure (velké instance) serveru. Je důležité pro práci s SAP, systémoví integrátoři nebo Microsoft správně navrhovat a implementovat strategie zotavení po havárii a správné s vysokou dostupností. Je také důležité vzít v úvahu plánovaného bodu obnovení (RPO) a plánovaná doba obnovení, které jsou specifické pro vaše prostředí.

Společnost Microsoft podporuje některé možnosti vysoké dostupnosti SAP HANA s velkých instancích HANA. Mezi tyto možnosti patří:

- **Replikace úložiště mezi**: systém úložiště možnost replikace všech dat na jiné razítko velká Instance HANA v jiné oblasti Azure. SAP HANA funguje nezávisle na tuto metodu. Tato funkce je výchozí mechanismus pro zotavení po havárii nabízí pro velké instance HANA.
- **Systémové replikace HANA**: [replikace všech dat v SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) do samostatného systému SAP HANA. Plánovaná doba obnovení je minimalizován prostřednictvím replikace dat v pravidelných intervalech. SAP HANA podporuje asynchronní, která je synchronní režimy v paměti a synchronní. Synchronním režimu se používá pouze pro systémy SAP HANA, které jsou ve stejném datacentru nebo menší než 100 km od sebe. V aktuální návrhu razítka velká Instance HANA je možné pro zajištění vysoké dostupnosti v rámci jedné oblasti jenom systémové replikace HANA. Systémové replikace HANA vyžaduje reverzní proxy server jiného výrobce nebo směrování komponenty pro konfigurace zotavení po havárii do jiné oblasti Azure. 
- **Hostování – automatické převzetí služeb při selhání**: místní selhání obnovení řešení pro SAP HANA, které je alternativou k HANA system replication. Pokud hlavní uzel stane nedostupným, nakonfigurujte jednu nebo více pohotovostní SAP HANA uzly v režimu horizontální navýšení kapacity a SAP HANA automaticky převezme služby při selhání pohotovostní uzel.

SAP HANA v Azure (velké instance) se nabízí ve dvou oblastech Azure čtyři geopolitické oblasti (USA, Austrálie, Evropa a Japonsko). Dvě oblasti v geopolitické oblasti, které jsou hostiteli velká Instance HANA razítka jsou připojené k samostatné vyhrazené sítě okruhů. Ty se používají pro replikaci snímků úložiště zadali způsoby zotavení po havárii. Replikace nedojde ve výchozím nastavení, ale je nastavit pro zákazníky, kteří pořadí funkce zotavení po havárii. Replikace úložiště je závislý na použití snímků úložiště pro velké instance HANA. Není možné vybrat oblasti Azure jako, který je v jiné oblasti geopolitické oblasti zotavení po Havárii. 

Následující tabulka uvádí aktuálně se podporují vysokou dostupnost a obnovení metody a kombinace:

| Scénář ve velkých instancích HANA | Možnost vysoké dostupnosti | Možnost obnovení po havárii | Komentáře |
| --- | --- | --- | --- |
| Jeden uzel | Není k dispozici. | Vyhrazené nastavení zotavení po Havárii.<br /> Nastavení zotavení po Havárii Multipurpose. | |
| Hostování – automatické převzetí služeb při selhání: horizontální navýšení kapacity (s nebo bez pohotovostní)<br /> včetně 1 + 1 | Možná s úsporný režim s ohledem aktivní roli.<br /> HANA řídí přepínač role. | Vyhrazené nastavení zotavení po Havárii.<br /> Nastavení zotavení po Havárii Multipurpose.<br /> Synchronizace zotavení po Havárii pomocí replikace úložiště. | HANA svazek sady jsou připojeny ke všem uzlům.<br /> Zotavení po Havárii lokalita musí mít stejný počet uzlů. |
| Systémové replikace HANA | Možná se primární nebo sekundární instalační program.<br /> Přesune sekundární do primární role v případě převzetí služeb při selhání.<br /> Systémové replikace HANA a operačního systému řízení převzetí služeb při selhání. | Vyhrazené nastavení zotavení po Havárii.<br /> Nastavení zotavení po Havárii Multipurpose.<br /> Synchronizace zotavení po Havárii pomocí replikace úložiště.<br /> Zotavení po Havárii s využitím systémové replikace HANA ještě není možná bez komponenty třetích stran. | Samostatnou sadu pro diskové svazky jsou připojené k jednotlivým uzlům.<br /> Pouze diskové svazky sekundární replika v produkční lokality se replikují do umístění pro zotavení po Havárii.<br /> Jedna sada svazků vyžádáním v lokalitě zotavení po Havárii. | 

Vyhrazené zotavení po Havárii instalační program, ve kterém se nepoužívá velká Instance HANA jednotek v lokalitě zotavení po Havárii pro spouštění jiných úloh nebo testovacím systému. Jednotka je pasivní činnost a nasazuje se pouze v případě převzetí služeb při havárii provádí. Toto nastavení však není upřednostňovaný volba pro mnoho zákazníků.

Přečtěte si [HLI Podporované scénáře](hana-supported-scenario.md) další úložiště rozložení a sítě ethernet a podrobnosti o vaší architektuře.

> [!NOTE]
> [Nasazení SAP HANA MCOD](https://launchpad.support.sap.com/#/notes/1681092) (více instancí HANA na jednu jednotku) jako zakreslovat pracovní scénáře s vysokou dostupnost a zotavení po Havárii metody uvedené v tabulce. Výjimka je použití systémové replikace HANA s clusterem automatické převzetí služeb při selhání podle Pacemaker. Takovém podporuje pouze jednu instanci HANA na jednotku. Pro [SAP HANA MDC](https://launchpad.support.sap.com/#/notes/2096000) nasazení, pouze mimo úložiště – metody založené na vysokou dostupnost a zotavení po Havárii fungovat, pokud je nasazení více než jednoho tenanta. Pomocí jednoho tenanta, nasazení jsou platné všechny metody, které jsou uvedeny.  

Multipurpose nastavení zotavení po Havárii je, kde velká Instance HANA jednotek v lokalitě zotavení po Havárii běží mimo produkční úlohy. V případě havárie, vypnout mimo produkční systém připojit replikované úložiště (Další) svazek sady a spusťte instance HANA produkčního prostředí. Většina zákazníků, kteří používají funkce zotavení po havárii velká Instance HANA pomocí této konfigurace. 


Další informace o vysoké dostupnosti SAP HANA můžete najít v těchto článcích SAP: 

- [Dokument White Paper o vysokou dostupnost v SAP HANA](http://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [Příručka věnovaná SAP HANA](http://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [SAP HANA Academy Video o systémové replikace SAP HANA](http://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [Poznámka: podpora #1999880 – nejčastější dotazy k SAP HANA System Replication SAP](https://apps.support.sap.com/sap/support/knowledge/preview/en/1999880)
- [SAP Support Poznámka #2165547 – SAP HANA zpět nahoru a obnovení v rámci prostředí replikace systému SAP HANA](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [Poznámka: podpora #1984882 – použití SAP HANA System Replication SAP pro výměnu hardwaru s minimální nebo nulové výpadky](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>Požadavky sítě pro zotavení po havárii pomocí velké instance HANA

Abyste mohli využívat funkce zotavení po havárii velkých instancích HANA, budete muset navrhnout síťové připojení ke dvěma oblastmi Azure. Budete potřebovat připojení okruhu Azure ExpressRoute z místně ve vaší hlavní oblasti Azure a další připojení okruhu z místního vaší oblasti pro zotavení po havárii. Tato míra popisuje situaci, ve kterém dojde k problému v oblasti Azure, včetně Microsoft Enterprise Edge směrovač (MSEE) umístění.

Druhá míra budete moct připojit virtuální sítě Azure, které se připojují k SAP HANA v Azure (velké instance) v jedné oblasti s okruhem ExpressRoute, který se připojuje velké instance HANA v jiné oblasti. S tímto *křížové připojení*, služby spuštěné v Azure virtuální síť v oblast 1 může připojit k velká Instance HANA jednotky v oblasti 2 a naopak. Tato míra řeší případ, ve kterém pouze jeden z míst směrovači MSEE, která se připojuje vaše místní umístění s využitím Azure přejde do režimu offline.

Odolného configuration případech zotavení po havárii je znázorněný následujícím obrázku:

![Optimální konfiguraci zotavení po havárii](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>Další požadavky na velkých instancích HANA úložiště replikaci pro zotavení po havárii

Kromě předchozích požadavků pro nastavení zotavení po havárii s velkých instancích HANA musíte mít:

- Pořadí SAP HANA v Azure (velké instance) SKU pro stejnou velikost jako skladové položky v produkčním prostředí a nasaďte je do oblasti pro zotavení po havárii. V aktuální nasazení zákazníků tyto instance se používají ke spuštění instance HANA-li se o neprodukční. Tyto konfigurace se označují jako *multipurpose nastavení zotavení po Havárii*.   
- Objednat další úložiště v lokalitě zotavení po Havárii pro každou SAP HANA v Azure (velké instance) skladové položky, které chcete obnovit v lokalitě zotavení po havárii. Nakupování dalších úložišť vám umožní přidělit svazky úložiště. Můžete přidělit svazků, které jsou cílem replikace úložiště z oblasti Azure v produkčním prostředí na zotavení po havárii oblasti Azure.
- V případě, kde máte HSR nastavený na primární a nastavení replikace úložiště založené na lokalitě zotavení po Havárii, je nutné zakoupit další úložiště v lokalitě zotavení po Havárii tak i primární a sekundární uzly data získá replikují do lokality pro zotavení po Havárii.

 **Další kroky**
- Přečtěte si [zálohování a obnovení](hana-backup-restore.md).













