---
title: Vysoká dostupnost a zotavení po havárii SAP HANA v Azure (velké instance) | Dokumenty společnosti Microsoft
description: Navázání vysoké dostupnosti a plánování zotavení po havárii SAP HANA v Azure (velké instance)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0585c1251ba18e1390f3eee28a989edee6eb8591
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616934"
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Sap HANA Velké instance vysoká dostupnost a zotavení po havárii v Azure 

>[!IMPORTANT]
>Tato dokumentace není náhradou dokumentace pro správu SAP HANA nebo poznámky SAP. Očekává se, že čtenář má solidní znalosti a odborné znalosti v sap hana správy a operací, zejména s tématy zálohování, obnovení, vysokou dostupnost a zotavení po havárii.

Je důležité, abyste provedli kroky a procesy provedené ve vašem prostředí a s verzemi a verzemi HANA. Některé procesy popsané v této dokumentaci jsou zjednodušeny pro lepší obecné pochopení a nejsou určeny k použití jako podrobné kroky pro případné provozní příručky. Chcete-li vytvořit provozní příručky pro konfigurace, musíte testovat a cvičit procesy a dokumentovat procesy související s vašimi konkrétními konfiguracemi. 


Vysoká dostupnost a zotavení po havárii (DR) jsou klíčové aspekty spuštění vašeho kritického SAP HANA na serveru Azure (velké instance). Je důležité pracovat se společností SAP, systémovým integrátorem nebo společností Microsoft, abyste správně napracovali a implementovali správné strategie vysoké dostupnosti a zotavení po havárii. Je také důležité zvážit cíl bodu obnovení (RPO) a cíl doby obnovení, které jsou specifické pro vaše prostředí.

Společnost Microsoft podporuje některé funkce sap HANA s vysokou dostupností s velkými instancemi HANA. Mezi tyto možnosti patří:

- **Replikace úložiště**: Možnost systému úložiště replikovat všechna data do jiného razítka velké instance HANA v jiné oblasti Azure. SAP HANA pracuje nezávisle na této metodě. Tato funkce je výchozí mechanismus zotavení po havárii, který je nabízen pro velké instance HANA.
- **Replikace systému HANA**: [Replikace všech dat v SYSTÉMU SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) do samostatného systému SAP HANA. Cíl doby obnovení je minimalizován prostřednictvím replikace dat v pravidelných intervalech. SAP HANA podporuje asynchronní, synchronní v paměti a synchronní režimy. Synchronní režim se používá pouze pro systémy SAP HANA, které jsou ve stejném datovém centru nebo méně než 100 km od sebe. S aktuálním návrhem razítek velké instance HANA lze replikaci systému HANA použít pouze pro vysokou dostupnost v rámci jedné oblasti. Replikace systému HANA vyžaduje reverzní proxy server nebo součást směrování od jiného výrobce pro konfigurace zotavení po havárii do jiné oblasti Azure. 
- **Automatické převzetí služeb při selhání**hostitele : Místní řešení pro obnovení selhání pro SAP HANA, které je alternativou k replikaci systému HANA. Pokud hlavní uzel přestane být k dispozici, nakonfigurujete jeden nebo více pohotovostních uzlů SAP HANA v režimu horizontálního navýšení kapacity a SAP HANA automaticky převezme služby při selhání do pohotovostního uzlu.

SAP HANA v Azure (velké instance) se nabízí ve dvou oblastech Azure ve čtyřech geopolitických oblastech (USA, Austrálie, Evropa a Japonsko). Dvě oblasti v rámci geopolitické oblasti, které hostují razítka velké instance HANA, jsou připojeny k samostatným vyhrazeným síťovým obvodům. Ty se používají pro replikaci snímků úložiště k poskytování metod zotavení po havárii. Replikace není ve výchozím nastavení vytvořena, ale je nastavena pro zákazníky, kteří si objednají funkce zotavení po havárii. Replikace úložiště závisí na využití snímků úložiště pro velké instance HANA. Není možné zvolit oblast Azure jako oblast zotavení po Havárii, která se nachází v jiné geopolitické oblasti. 

V následující tabulce jsou uvedeny aktuálně podporované metody a kombinace zotavení po havárii:

| Scénář podporovaný ve velkých instancích HANA | Možnost vysoké dostupnosti | Možnost zotavení po havárii | Komentáře |
| --- | --- | --- | --- |
| Jeden uzel | Není k dispozici. | Vyhrazené nastavení zotavení po havárii.<br /> Víceúčelové nastavení zotavení po havárii. | |
| Automatické převzetí služeb při selhání hostitele: Horizontální navýšení kapacity (s pohotovostním režimem nebo bez něj)<br /> včetně 1+1 | Možné s pohotovostním režimem při aktivní roli.<br /> HANA řídí přepínač rolí. | Vyhrazené nastavení zotavení po havárii.<br /> Víceúčelové nastavení zotavení po havárii.<br /> Synchronizace zotavení po havárii pomocí replikace úložiště. | Sady svazků HANA jsou připojeny ke všem uzlům.<br /> Lokalita zotavení po Havárii musí mít stejný počet uzlů. |
| Replikace systému HANA | Možné s primárním nebo sekundárním nastavením.<br /> Sekundární přesune na primární roli v případě převzetí služeb při selhání.<br /> Replikace systému HANA a převzetí služeb při selhání ovládacího prvku operačního systému. | Vyhrazené nastavení zotavení po havárii.<br /> Víceúčelové nastavení zotavení po havárii.<br /> Synchronizace zotavení po havárii pomocí replikace úložiště.<br /> Zotavení po havárii pomocí replikace systému HANA ještě není možné bez komponent třetích stran. | Ke každému uzlu jsou připojeny samostatné sady diskových svazků.<br /> Do umístění zotavení po Havárii se replikují pouze diskové svazky sekundární repliky v produkční lokalitě.<br /> V lokalitě zotavení po Havárii je vyžadována jedna sada svazků. | 

Vyhrazené nastavení zotavení po havárii je místo, kde se jednotka velké instance HANA v lokalitě zotavení po Havárii nepoužívá pro spuštění jiného pracovního vytížení nebo neprodukčního systému. Jednotka je pasivní a je nasazena pouze v případě, že je spuštěno převzetí služeb při selhání. Toto nastavení však není pro mnoho zákazníků preferovanou volbou.

Odkazovat [HLI podporované scénáře](hana-supported-scenario.md) se dozvíte rozložení úložiště a ethernet podrobnosti pro vaši architekturu.

> [!NOTE]
> [Nasazení SAP HANA MCOD](https://launchpad.support.sap.com/#/notes/1681092) (více instancí HANA na jedné jednotce) jako scénáře překrytí pracují s metodami HA a ZOTAVENÍ uvedenými v tabulce. Výjimkou je použití replikace systému HANA s automatickým clusterem s podporou převzetí služeb při selhání založeným na kardiostimulátoru. Takový případ podporuje pouze jednu instanci HANA na jednotku. Pro nasazení [SAP HANA MDC](https://launchpad.support.sap.com/#/notes/2096000) fungují pouze metody HA a ZOTAVENÍ založené na úložišti, pokud je nasazeno více než jeden tenant. S jedním nasazeným tenantem jsou platné všechny uvedené metody.  

Víceúčelové nastavení zotavení po Havárii je místo, kde jednotka velké instance HANA na pracovišti zotavení po Havárii spouští neprodukční úlohu. V případě havárie vypněte neprodukční systém, připojte sady svazků replikované úložištěm a spusťte produkční instanci HANA. Většina zákazníků, kteří používají funkci zotavení po havárii hana velké instance, používá tuto konfiguraci. 


Další informace o vysoké dostupnosti SAP HANA naleznete v následujících článcích SAP: 

- [Sap HANA Dokument white paper s vysokou dostupností](https://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [Průvodce správou SAP HANA](https://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [Sap HANA Academy Video na replikaci systému SAP HANA](https://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [Poznámka k podpoře SAP #1999880 – nejčastější dotazy týkající se replikace systému SAP HANA](https://apps.support.sap.com/sap/support/knowledge/preview/en/1999880)
- [Poznámka k podpoře SAP #2165547 – zálohování a obnovení SAP HANA v prostředí replikace systému SAP HANA](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [Sap Support Note #1984882 – použití replikace systému SAP HANA pro výměnu hardwaru s minimální/nulovou prostoji](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>Důležité informace o síti pro zotavení po havárii s velkými instancemi HANA

Chcete-li využít funkce zotavení po havárii hana velké instance, je třeba navrhnout připojení k síti pro dvě oblasti Azure. Potřebujete připojení okruhu Azure ExpressRoute z místního prostředí v hlavní oblasti Azure a další připojení okruhu z místního do oblasti zotavení po havárii. Toto opatření zahrnuje situaci, ve které došlo k problému v oblasti Azure, včetně umístění Směrovače Microsoft Enterprise Edge (MSEE).

Jako druhé měřítko můžete připojit všechny virtuální sítě Azure, které se připojují k SAP HANA v Azure (velké instance) v jedné oblasti, k okruhu ExpressRoute, který spojuje velké instance HANA v jiné oblasti. Díky tomuto *křížovému připojení*se služby spuštěné ve virtuální síti Azure v oblasti 1 můžou připojit k jednotkám velké instance HANA v oblasti 2 a naopak. Toto opatření řeší případ, ve kterém pouze jeden z umístění MSEE, který se připojuje k místní umístění s Azure přejde do offline.

Následující obrázek znázorňuje odolnou konfiguraci pro případy zotavení po havárii:

![Optimální konfigurace pro zotavení po havárii](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>Další požadavky s replikací úložiště velkých instancí HANA pro zotavení po havárii

Kromě předchozích požadavků na nastavení zotavení po havárii s velkými instancemi HANA je nutné:

- Objednejte SAP HANA v Azure (velké instance) skum stejné velikosti jako vaše produkční skutény a nasadit je v oblasti zotavení po havárii. V aktuálním nasazení zákazníka se tyto instance používají ke spuštění neprodukčních instancí HANA. Tyto konfigurace jsou označovány jako *víceúčelová nastavení zotavení po Havárii*.   
- Objednejte další úložiště na webu zotavení po havárii pro každý z vašich skladových míst SAP HANA v Azure (velké instance), které chcete obnovit v lokalitě zotavení po havárii. Nákup dalšího úložiště umožňuje přidělit svazky úložiště. Můžete přidělit svazky, které jsou cílem replikace úložiště z produkční oblasti Azure do oblasti Azure zotavení po havárii.
- V případě, že máte nastavení HSR na primární a nastavení replikace založené na úložišti do lokality zotavení po Havárii, je nutné zakoupit další úložiště v lokalitě zotavení po Havárii, aby se data primárních i sekundárních uzlů replikovala do lokality zotavení po Havárii.

  **Další kroky**
- Viz [Zálohování a obnovení](hana-backup-restore.md).













