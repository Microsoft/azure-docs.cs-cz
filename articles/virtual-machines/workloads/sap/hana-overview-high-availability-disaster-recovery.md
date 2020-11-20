---
title: Vysoká dostupnost a zotavení po havárii SAP HANA v Azure (velké instance) | Microsoft Docs
description: Zajištění vysoké dostupnosti a plánování pro zotavení po havárii SAP HANA v Azure (velké instance)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 56e2e2a048e44dcad626208b059e258d55ba7057
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967595"
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Velké instance SAP HANA vysoká dostupnost a zotavení po havárii v Azure 

>[!IMPORTANT]
>Tato dokumentace nenahrazuje dokumentaci ke správě SAP HANA ani SAP poznámky. Očekává se, že čtenář má plnou znalost a zkušenosti v SAP HANA správě a operacích, zejména s tématy o zálohování, obnovení, vysoké dostupnosti a zotavení po havárii.

Je důležité postupovat podle kroků a procesů pořízených ve vašem prostředí a ve verzích HANA a vydání. Některé procesy popsané v této dokumentaci jsou zjednodušené pro lepší porozumění a nejsou určeny k použití jako podrobného postupu pro případné provozní Handbooks. Pokud chcete pro své konfigurace vytvořit operaci Handbooks, je třeba otestovat a vykonat procesy a zdokumentovat procesy související s vašimi konkrétní konfigurací. 


Vysoká dostupnost a zotavení po havárii (DR) jsou zásadními aspekty provozování důležitých SAP HANA na serveru Azure (velké instance). Je důležité pracovat s SAP, vaším systémovým integrátorem nebo Microsoftem, aby správně architekty a implementovaly správné strategie pro vysokou dostupnost a zotavení po havárii. Je také důležité zvážit cíl bodu obnovení (RPO) a plánovaný čas obnovení, které jsou specifické pro vaše prostředí.

Microsoft podporuje některé SAP HANA vysoce dostupné funkce s velkými instancemi HANA. Mezi tyto možnosti patří:

- **Replikace úložiště**: schopnost systému úložiště replikovat všechna data na další časová razítka pro velké instance Hana v jiné oblasti Azure. SAP HANA funguje nezávisle na této metodě. Tato funkce je výchozím mechanismem zotavení po havárii nabízeným pro velké instance HANA.
- **Replikace systému Hana**: [replikace všech dat v SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) do samostatného systému SAP HANA. Cíl doby obnovení je minimalizován prostřednictvím replikace dat v pravidelných intervalech. SAP HANA podporuje asynchronní, synchronní a synchronní režimy v paměti. Synchronní režim se používá jenom pro SAP HANA systémy, které jsou ve stejném datovém centru nebo méně než 100 km od sebe. Díky aktuálnímu návrhu razítek velkých instancí HANA se replikace systému HANA dá použít jenom pro vysokou dostupnost v rámci jedné oblasti. Replikace systému HANA vyžaduje reverzní proxy server nebo komponentu směrování třetí strany pro konfiguraci zotavení po havárii v jiné oblasti Azure. 
- **Automatické převzetí služeb při selhání hostitele**: místní řešení pro obnovení chyb pro SAP HANA, které je alternativou replikace systému Hana. Pokud hlavní uzel přestane být k dispozici, nakonfigurujete jeden nebo více pohotovostních SAP HANA uzlů v režimu škálování na více instancí a SAP HANA automaticky převezme převzetí služeb při selhání v pohotovostním uzlu.

SAP HANA v Azure (velké instance) se nabízí ve dvou oblastech Azure ve čtyřech geopolitických oblastech (USA, Austrálie, Evropa a Japonsko). Dvě oblasti v geopolitické oblasti, které hostují Velká časová razítka instance HANA, jsou připojené k odděleným vyhrazeným síťovým okruhům. Používají se pro replikaci snímků úložiště k poskytování metod zotavení po havárii. Replikace není ve výchozím nastavení navázaná, ale je nastavená pro zákazníky, kteří doplňují funkce zotavení po havárii. Replikace úložiště závisí na využití snímků úložiště pro velké instance HANA. Nemůžete zvolit oblast Azure jako oblast zotavení po havárii, která je v jiné geopolitické oblasti. 

Následující tabulka uvádí aktuálně podporované metody a kombinace vysoké dostupnosti a zotavení po havárii:

| Scénář podporovaný ve velkých instancích HANA | Možnost vysoké dostupnosti | Možnost zotavení po havárii | Komentáře |
| --- | --- | --- | --- |
| Jeden uzel | Není k dispozici. | Vyhrazené nastavení zotavení po havárii<br /> Nastavení Multipurpose DR. | |
| Automatické převzetí služeb při selhání hostitele: horizontální navýšení kapacity (s úsporným nebo bez)<br /> včetně 1 + 1 | Je možné, že se v pohotovostním režimu vezme aktivní role.<br /> HANA řídí přepínač role. | Vyhrazené nastavení zotavení po havárii<br /> Nastavení Multipurpose DR.<br /> Synchronizace DR pomocí replikace úložiště. | Sady svazků HANA jsou připojeny ke všem uzlům.<br /> Lokalita DR musí mít stejný počet uzlů. |
| Replikace systému HANA | Možné s primárním nebo sekundárním nastavením.<br /> Sekundární přesun do primární role v případě převzetí služeb při selhání.<br /> Replikace systému HANA a převzetí služeb při selhání ovládacího prvku operačního systému | Vyhrazené nastavení zotavení po havárii<br /> Nastavení Multipurpose DR.<br /> Synchronizace DR pomocí replikace úložiště.<br /> Zotavení po havárii pomocí replikace systému HANA ještě není možné bez součástí jiných výrobců. | K jednotlivým uzlům jsou připojeny samostatné sady diskových svazků.<br /> Replikují se do umístění DR jenom diskové svazky sekundární repliky v produkční lokalitě.<br /> Na webu DR se vyžaduje jedna sada svazků. | 

Vyhrazená instalace DR je tam, kde se jednotka velkých instancí HANA v lokalitě DR nepoužívá ke spuštění žádné jiné úlohy nebo neprodukčního systému. Jednotka je pasivní a je nasazena pouze v případě, že je provedeno převzetí služeb při selhání po havárii. Tato instalace ale není upřednostňovanou volbou pro mnoho zákazníků.

Pokud chcete zjistit rozložení úložiště a podrobnosti o síti Ethernet pro vaši architekturu, přečtěte si [scénáře podporované HLI](hana-supported-scenario.md) .

> [!NOTE]
> [SAP HANA nasazení MCOD](https://launchpad.support.sap.com/#/notes/1681092) (více instancí Hana na jedné jednotce) jako překrytí scénářů fungují s metodami ha a zotavení po havárii, které jsou uvedené v tabulce. Výjimkou je použití replikace systému HANA s automatickým clusterem s podporou převzetí služeb při selhání založeném na Pacemaker. Takový případ podporuje pouze jednu instanci HANA na jednotku. U [SAP HANA nasazení MDC](https://launchpad.support.sap.com/#/notes/2096000) budou fungovat jenom metody HA a ha, které nevyužívají úložiště a zotavení po havárii, pokud je nasazený víc než jeden tenant. U jednoho nasazeného tenanta jsou všechny uvedené metody platné.  

Víceúčelový instalační program se používá v případě, že velká jednotka instance HANA na webu DR spouští neprodukční úlohy. V případě havárie vypněte neprodukční systém, připojte sady svazků replikované úložiště (další) a pak spusťte instanci provozní HANA. Většina zákazníků, kteří používají funkci zotavení po havárii velké instance HANA, tuto konfiguraci používají. 


Další informace o SAP HANA vysoké dostupnosti najdete v následujících článcích SAP: 

- [Dokument white paper k SAP HANA vysoké dostupnosti](https://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [Průvodce správou SAP HANA](https://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [Academy video o SAP HANA systémové replikace SAP HANA](https://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [Poznámka k podpoře SAP #1999880 – Nejčastější dotazy týkající se replikace systému SAP HANA](https://apps.support.sap.com/sap/support/knowledge/preview/en/1999880)
- [SAP – Poznámka k podpoře #2165547 – SAP HANA zálohování a obnovení v rámci SAP HANA prostředí replikace systému](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP – Poznámka k podpoře #1984882 – použití replikace SAP HANA systému pro výměnu hardwaru s minimálním/nulovým výpadkem](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>Požadavky na síť pro zotavení po havárii s velkými instancemi HANA

Aby bylo možné využít funkce zotavení po havárii velkých instancí HANA, je třeba navrhnout síťové připojení ke dvěma oblastem Azure. Potřebujete připojení okruhu Azure ExpressRoute z místního prostředí v hlavní oblasti Azure a další připojení okruhu z místního prostředí k vaší oblasti zotavení po havárii. Tato míra se věnuje situaci, kdy došlo k potížím v oblasti Azure, včetně umístění směrovače Microsoft Enterprise Edge (MSEE).

V rámci druhé míry můžete připojit všechny virtuální sítě Azure, které se připojují k SAP HANA v Azure (velké instance) v jedné oblasti do okruhu ExpressRoute, který propojuje velké instance HANA v jiné oblasti. V rámci tohoto *vzájemného připojení* se můžou služby běžící na virtuální síti Azure v oblasti 1 připojit k jednotkám velkých instancí Hana v oblasti 2 a druhým způsobem. Tato míra adresuje případ, ve kterém se do offline režimu připojuje jenom jedno umístění MSEE, která se připojují k místnímu umístění s Azure.

Následující obrázek znázorňuje odolnou konfiguraci pro případy zotavení po havárii:

![Optimální konfigurace pro zotavení po havárii](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>Jiné požadavky na replikaci úložiště velkých instancí HANA pro zotavení po havárii

Kromě předchozích požadavků pro nastavení zotavení po havárii s velkými instancemi HANA musíte:

- Objednání SAP HANA v Azure (velké instance) SKU stejné velikosti jako produkčních skladových jednotek a jejich nasazení v oblasti zotavení po havárii. V současných zákaznických nasazeních se tyto instance používají ke spouštění instancí HANA, které nejsou v produkčním prostředí. Tyto konfigurace se označují jako *víceúčelové nastavení pro zotavení po havárii*.   
- Přiobjednat další úložiště na webu DR pro každý SAP HANA v SKU Azure (velké instance), které chcete obnovit na webu pro zotavení po havárii. Nákup dalšího úložiště vám umožní přidělit svazky úložiště. Můžete přidělit svazky, které jsou cílem replikace úložiště z produkční oblasti Azure, do oblasti Azure pro zotavení po havárii.
- V případě, kdy máte HSR instalaci na primárním počítači a nastavili jste replikaci založenou na úložišti na lokalitu DR, je nutné v lokalitě DR zakoupit další úložiště, aby se data primárních i sekundárních uzlů mohla replikovat do lokality DR.

  **Další kroky**
- Přečtěte si téma [zálohování a obnovení](hana-backup-restore.md).













