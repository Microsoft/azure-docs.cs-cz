---
title: Řešení Azure VMware od CloudSimple – privátní cloudy
description: Seznamte se s cloudovými privátními cloudy a koncepty.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4fb930603455ed1a5df5d357fcab669f41a0c28c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024941"
---
# <a name="cloudsimple-private-cloud-overview"></a>CloudSimple Private Cloud – přehled

CloudSimple transformuje a rozšiřuje úlohy VMware do veřejných cloudů během několika minut. Pomocí služby CloudSimple můžete nasadit VMware nativně na azure holé infrastruktury. Vaše nasazení žije v lokalitách Azure a plně se integruje se zbytkem cloudu Azure.

Řešení CloudSimple poskytuje kompletní provozní kontinuitu společnosti VMware. Toto řešení vám poskytuje výhody veřejného cloudu:

* Elasticita
* Innovation
* Efektivita

S CloudSimple můžete těžit z modelu spotřeby cloudu, který snižuje celkové náklady na vlastnictví. Nabízí také zřizování na vyžádání, růst průběžných plateb a optimalizaci kapacity.

CloudSimple je plně kompatibilní s:

* Stávající nástroje
* Dovednosti
* Procesy

Tato kompatibilita umožňuje vašim týmům spravovat úlohy v cloudu Azure, aniž by došlo k narušení těchto typů zásad:

* Network (Síť)
* Zabezpečení  
* Ochrana dat  
* Auditování

CloudSimple spravuje infrastrukturu a všechny potřebné síťové a správu služeb. Služba CloudSimple umožňuje vašemu týmu zaměřit se na:

* Obchodní hodnota
* Zřizování aplikací
* Kontinuita podnikových procesů
* Podpora
* Vynucování zásad

## <a name="private-cloud-environment-overview"></a>Přehled prostředí privátního cloudu

Privátní cloud je izolovaný zásobník VMware, který podporuje:

* EsXi hostitelé
* vCenter
* vSAN
* NSX

Privátní cloudy jsou spravovány prostřednictvím portálu CloudSimple. Mají svůj vlastní server vCenter ve své vlastní doméně pro správu.

Zásobník běží na:

* Vyhrazené uzly
* Izolované holé kovové hardwarové uzly

Uživatelé spotřebovávají zásobník prostřednictvím nativních nástrojů VMware, včetně:

* vCenter
* NSX manažer

Vyhrazené uzly můžete nasadit v umístěních Azure. Pak je můžete spravovat pomocí Azure a CloudSimple. Privátní cloud se skládá z jednoho nebo více clusterů vSphere a každý cluster obsahuje 3 až 16 uzlů.

Privátní cloud můžete vytvořit pomocí zakoupených uzlů s průběžnými platbami nebo vyhrazených vyhrazených uzlů.

Privátní cloud můžete připojit k místnímu prostředí a síti Azure pomocí následujících připojení:

* Zabezpečení
* Soukromá VPN
* Azure ExpressRoute

Prostředí Privátního cloudu je navrženo tak, aby eliminovalo jednotlivé body selhání:

* Clustery ESXi jsou konfigurovány s vysokou dostupností vSphere a mají velikost alespoň jednoho náhradního uzlu pro odolnost proti chybám.
* vSAN poskytuje redundantní primární úložiště. vSan vyžaduje alespoň tři uzly pro ochranu proti jedné chybě. VSAN můžete nakonfigurovat tak, aby poskytovala vyšší odolnost proti chybám pro větší clustery.
* Virtuální počítače vCenter, PSC a NSX Manager můžete nakonfigurovat pomocí zásad úložiště RAID-10, které chrání před selháním úložiště. vSphere HA chrání před selháním uzlů a sítí.

## <a name="scenarios-for-deploying-a-private-cloud"></a>Scénáře pro nasazení privátního cloudu

Tady jsou některé příklady případů použití pro nasazení privátního cloudu.

### <a name="data-center-retirement-or-migration"></a>Odchod do důchodu nebo migrace datového centra

* Získejte další kapacitu, když dosáhnete limitů stávajícího hardwaru datového centra nebo aktualizačního hardwaru.
* Přidejte potřebnou kapacitu v cloudu a eliminujte bolesti hlavy při správě hardwarových aktualizací.
* Snižte riziko a náklady na migrace do cloudu ve srovnání s časově náročnými konverzemi nebo rearchitekturou.
* K urychlení migrace do cloudu použijte známé nástroje a dovednosti společnosti VMware. V cloudu můžete využívat služby Azure k modernizaci aplikací svým tempem.

### <a name="expand-on-demand"></a>Rozšiřte poptávku

* Expandujte do cloudu tak, aby vyhovoval neočekávaným potřebám, jako jsou nová vývojová prostředí nebo sezónní nárůsty kapacity.
* Vytvořte novou kapacitu na vyžádání a udržujte ji pouze tak dlouho, jak ji budete potřebovat.
* Snižte počáteční investice, urychlete rychlost zřizování a snižte složitost pomocí stejné architektury a zásad v místním prostředí i v cloudu.

### <a name="disaster-recovery-and-virtual-desktops-in-the-azure-cloud"></a>Zotavení po havárii a virtuální plochy v cloudu Azure

* Nastořte vzdálený přístup k datům, aplikacím a plochám v cloudu Azure. S připojením s velkou šířkou pásma můžete rychle nahrávat / stahovat data, abyste se zotavili z incidentů. Sítě s nízkou latencí poskytují rychlé doby odezvy, které uživatelé očekávají od desktopové aplikace.

* Replikujte všechny své zásady a sítě v cloudu pomocí portálu CloudSimple a známých nástrojů VMware. Replikace snižuje úsilí a riziko vytváření a správy implementací zotavení po havárii a VDI.

### <a name="high-performance-applications-and-databases"></a>Vysoce výkonné aplikace a databáze

* Spusťte nejnáročnější úlohy s hyperkonvergovní architekturou poskytovanou cloudem Simple.
* Spusťte oracle, microsoft sql server, middlewarové systémy a vysoce výkonné databáze bez SQL.
* Zažijte cloud jako vlastní datové centrum s vysokorychlostními síťovými připojeními 25 Gb/s. Vysokorychlostní připojení umožňují spouštět hybridní aplikace, které se rozprostírají místně, v prostředí VMware v Azure a privátní úlohy Azure, aniž by došlo ke snížení výkonu.

### <a name="true-hybrid"></a>Skutečný hybrid

* Sjednocuj devops napříč službami VMware a Azure.
* Optimalizujte správu vmware pro služby a řešení Azure, které lze použít ve všech vašich úlohách.
* Získejte přístup k veřejným cloudovým službám, aniž byste museli rozšiřovat datové centrum nebo renovovat aplikace.
* Centralizujte identity, zásady řízení přístupu, protokolování a monitorování aplikací VMware v Azure.

## <a name="limits"></a>Omezení

V následující tabulce jsou uvedena omezení uzlů pro prostředky privátního cloudu.

| Prostředek | Omezení |
|----------|-------|
| Minimální počet uzlů pro vytvoření privátního cloudu | 3 |
| Maximální počet uzlů v clusteru v privátním cloudu | 16 |
| Maximální počet uzlů v privátním cloudu | 64 |
| Minimální počet uzlů v novém clusteru | 3 |

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [vytvořit privátní cloud](create-private-cloud.md)
* Přečtěte si, jak [nakonfigurovat prostředí privátního cloudu](quickstart-create-private-cloud.md)
