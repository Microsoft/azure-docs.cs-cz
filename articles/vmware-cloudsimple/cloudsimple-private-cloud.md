---
title: Privátní cloudy v řešení VMware podle CloudSimple – Azure
description: Další informace o konceptech a CloudSimple privátních cloudů.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e3ab53466cae5bd39d85f8d846c8e59ad94f1f2d
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165750"
---
# <a name="cloudsimple-private-cloud-overview"></a>Přehled privátního cloudu CloudSimple

CloudSimple transformuje a rozšiřuje úloh VMware do veřejných cloudů během několika minut. Pomocí služby CloudSimple, můžete nasadit VMware nativně na holé počítače infrastruktury Azure. Nasazení se nachází v umístění Azure a plně integruje se zbytkem cloudu Azure.

* CloudSimple řešení poskytuje kompletní provozní kontinuity podnikových procesů VMware. Toto řešení poskytuje výhody veřejných cloudů:
  * Pružnost
  * Inovace
  * Efektivita
* S CloudSimple můžete těžit z modelu využití cloudu, který snižuje celkové náklady na vlastnictví. Nabízí také na vyžádání zřizování, platit jako růstu a optimalizace kapacity.
* CloudSimple je plně kompatibilní s:
  * Stávající nástroje
  * Dovednosti
  * Procesy
* Tato kompatibilita umožní vašim týmům spravovat úlohy v cloudu Azure, bez narušení běžného zásad:
  * Síť
  * Zabezpečení  
  * Ochrana dat  
  * Auditování
* CloudSimple spravuje infrastrukturu nástroje a všechny nezbytné sítě a správy služeb. Služba CloudSimple umožňuje zaměřit se na váš tým:
  * Obchodní hodnota
  * Zřizování aplikací
  * Kontinuita podnikových procesů
  * Podpora
  * Vynucování zásad

## <a name="private-cloud-environment-overview"></a>Přehled prostředí privátního cloudu

Privátní cloud je izolované zásobníku VMware, například těchto prostředích:

* Hostitelé ESXi
* vCenter
* vSAN
* NSX

Privátní cloudy jsou spravovány serverem vCenter ve vlastní doméně pro správu.

Spouští na platformách zásobníku:

* Vyhrazené uzly
* Izolované bare metal hardware do uzlů

Uživatelé využívají zásobníku pomocí nativních nástrojů VMware, včetně:

* vCenter
* NSX správce

Vyhrazené uzly v umístění Azure můžete nasadit. Potom je můžete spravovat pomocí služeb Azure a CloudSimple. Privátní cloud se skládá z jednoho nebo více clusterech vSphere a každý cluster obsahuje 3 až 16 uzlů.

Můžete vytvořit privátní cloud pomocí zřízeného uzlů:

* Uzly s průběžnými platbami
* Vyhrazené, vyhrazené uzly

Privátního cloudu můžete připojit k místním prostředím a síť Azure, pomocí následujících připojení:

* Zabezpečení
* Privátní sítě VPN
* Azure ExpressRoute

Prostředí privátního cloudu je určen k odstranění nutnosti jediný bod selhání:

* ESXi clustery jsou nakonfigurovány s vysokou dostupností vSphere a velikosti mít alespoň jeden uzel za chodu pro odolnost proti chybám.
* síť vSAN poskytuje redundantní primárního úložiště. síť vSan vyžaduje alespoň tři uzly a zajistit ochranu proti selhání jedné. Můžete nakonfigurovat síť vSAN zajistit vyšší odolnost pro větší clustery.
* VCenter, PSČ a NSX Správce virtuálních počítačů můžete nakonfigurovat pomocí diskového pole RAID 10 úložiště zásad ochrany před selháním úložiště. Poté že jsou chráněné pomocí vSphere HA proti selhání uzlů a sítě.

## <a name="scenarios-for-deploying-a-private-cloud"></a>Scénáře nasazení privátního cloudu

* **Vyřazení datového centra nebo migrace**

  * Když narazíte na omezení vašeho existujícího datacentra nebo aktualizujte hardwaru, dostanete dodatečnou kapacitu.
  * Přidání potřebných kapacity v cloudu a eliminovat obrovskému správy aktualizace hardwaru.
  * Snižte riziko a náklady na migraci do cloudu, ve srovnání s časově náročné převody nebo rearchitecture.
  * Zrychlení migrace do cloudu pomocí známých nástrojů VMware a dovednosti. V cloudu modernizace aplikací vlastním tempem pomocí služby Azure.

* **Rozbalte na vyžádání**

  * Rozšiřte do cloudu pro potřeby neočekávané, jako je například nový vývojových prostředích nebo nárůstem sezónní kapacity.
  * Vytvoření nové kapacity na vyžádání a zachování jejich pouze tak dlouho, dokud ji budete potřebovat.
  * Snížit počáteční investici, rychlost zřizování urychlení a zjednodušení stejné architektury a zásady pro místní i cloud.

* **Zotavení po havárii a virtuální klienty v cloudu Azure**

  * Vytvořte vzdálený přístup k datům, aplikacím a stolních počítačů v cloudu Azure. S velkou šířkou pásma připojení, odesílání / stahování dat rychle k zotavení z incidentů. Časový limit sítě s nízkou latencí poskytují rychlé odpovědi, který uživatelé očekávají, že z desktopové aplikace.

  * Replikujte všechny zásady a sítě v cloudu s využitím portálu CloudSimple a dobře známých nástrojů VMware. Tato replikace zkracuje úsilí a riziko vytváření a správa implementace zotavení po Havárii a infrastruktury virtuálních klientských počítačů.

* **Vysoce výkonné aplikace a databáze**

  * Spouštět nejnáročnější úlohy, s architekturou hyperkonvergovaného poskytované CloudSimple.
  * Spuštění Oracle, Microsoft SQL serveru, middleware systémy a vysoce výkonné bez technologie SQL databází.

  * Prostředí cloud jako vašeho vlastního datového centra s vysokorychlostní 25 GB/s síťové připojení. Vysokorychlostní připojení umožňují spuštění hybridní aplikace umístěné místně, VMware v Azure, a privátní úlohy v Azure, bez omezení výkonu.

* **Skutečně hybridní**

  * Sjednoťte DevOps napříč službami Azure a prostředí VMware.
  * Optimalizujte VMware správy služeb Azure a řešení, které mohou být použity napříč vašimi úlohami.
  * Přístup ke službám veřejný cloud bez nutnosti rozšířit svoje datové centrum nebo změna na architekturu vašich aplikací.
  * Centralizujte identit, zásady řízení přístupu, protokolování a monitorování aplikací VMware v Azure.

## <a name="limits"></a>Limits

Následující tabulka ukazuje uzlu limity pro prostředky privátního cloudu.

| Resource | Omezení |
|----------|-------|
| Minimální počet uzlů pro vytvoření privátního cloudu | 3 |
| Maximální počet uzlů v clusteru v privátním cloudu | 16 |
| Maximální počet uzlů v privátním cloudu | 64 |
| Minimální počet uzlů v novém clusteru | 3 |

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [vytvoření privátního cloudu](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Zjistěte, jak [konfigurace prostředí privátního cloudu](quickstart-create-private-cloud.md)