---
title: Privátní cloudy v řešení VMware podle CloudSimple – Azure
description: Přečtěte si o privátních cloudech a konceptech CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9885366d5987870fe2739083ff47abaae9ef6ed1
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816201"
---
# <a name="cloudsimple-private-cloud-overview"></a>Přehled privátního cloudu CloudSimple

CloudSimple transformuje a rozšiřuje úlohy VMware na veřejné cloudy během několika minut. Pomocí služby CloudSimple můžete VMware nasazovat nativně na holé infrastruktuře Azure. Vaše nasazení žije ve službě Azure location a plně se integruje s ostatními cloudy Azure.

* Řešení CloudSimple poskytuje úplnou provozní kontinuitu VMware. Toto řešení poskytuje výhody veřejného cloudu pro:
  * Elasticitu
  * Inovace
  * Účinnost
* S CloudSimple můžete využívat výhod modelu cloudové spotřeby, který snižuje vaše celkové náklady na vlastnictví. Nabízí také zřizování na vyžádání, průběžné platby a optimalizaci kapacity.
* CloudSimple je plně kompatibilní s:
  * Existující nástroje
  * Dovednosti
  * Procesy
* Tato kompatibilita umožňuje vašim týmům spravovat úlohy v cloudu Azure, aniž by narušily vaše zásady:
  * Síť
  * Zabezpečení  
  * Ochrana dat  
  * Auditování
* CloudSimple spravuje infrastrukturu a všechny nezbytné služby sítě a správy. Služba CloudSimple umožňuje vašemu týmu soustředit se na:
  * Obchodní hodnota
  * Zřizování aplikací
  * Kontinuita podnikových procesů
  * Podpora
  * Vynucování zásad

## <a name="private-cloud-environment-overview"></a>Přehled prostředí privátního cloudu

Privátní cloud je izolovaný zásobník VMware, jako jsou tato prostředí:

* Hostitelé ESXi
* vCenter
* vSAN
* NSX

Privátní cloudy spravuje Server vCenter ve vlastní doméně pro správu.

Zásobník běží na:

* Vyhrazené uzly
* Izolované hardwarové uzly v holém systému

Uživatelé využívají zásobník prostřednictvím nativních nástrojů VMware, včetně:

* vCenter
* NSX Manager

Vyhrazené uzly můžete nasadit v umístěních Azure. Pak je můžete spravovat pomocí Azure a CloudSimple. Privátní cloud se skládá z jednoho nebo více clusterů vSphere a každý cluster obsahuje 3 až 16 uzlů.

Privátní Cloud můžete vytvořit pomocí zřízených uzlů:

* Uzly s průběžnými platbami
* Rezervované, vyhrazené uzly

Privátní Cloud můžete připojit k místnímu prostředí a síti Azure pomocí následujících připojení:

* Zabezpečení
* Privátní síť VPN
* Azure ExpressRoute

Prostředí privátního cloudu je určené k odstranění jednoho bodu selhání:

* Clustery ESXi jsou nakonfigurovány s vysokou dostupností vSphere a mají velikost pro zajištění odolnosti alespoň jednoho náhradního uzlu.
* Síti vSAN poskytuje redundantní primární úložiště. Síti vSAN vyžaduje alespoň tři uzly, aby se zajistila ochrana před jediným selháním. Síti vSAN můžete nakonfigurovat tak, aby poskytovaly vyšší odolnost proti většímu objemu clusterů.
* Pro zajištění ochrany před selháním úložiště můžete nakonfigurovat virtuální počítače vCenter, PSC a NSX Manager pomocí zásad úložiště RAID-10. Pak jsou chráněny pomocí vSphere HA proti selháním uzlů a sítí.

## <a name="scenarios-for-deploying-a-private-cloud"></a>Scénáře nasazení privátního cloudu

* **Vyřazení nebo migrace datového centra**

  * Získejte další kapacitu, když dosáhnete limitů stávajícího datového centra nebo obnovíte hardware.
  * Přidejte potřebnou kapacitu do cloudu a Eliminujte souvisejícím problémům správou správy aktualizací hardwaru.
  * Snižte riziko a náklady na migrace do cloudu v porovnání s časově náročnými převody nebo rearchitekturou.
  * K urychlení migrace do cloudu používejte známé nástroje a dovednosti VMware. V cloudu pomocí služeb Azure modernizovat své aplikace podle svého tempa.

* **Rozbalit na vyžádání**

  * Rozbalíte do cloudu, abyste splnili neočekávané potřeby, jako jsou nová vývojová prostředí nebo sezónní nárůst kapacity.
  * Vytvořte novou kapacitu na vyžádání a zachovejte ji jenom tak dlouho, jak potřebujete.
  * Snižte svoji investici předem, Zrychlete zřizování a snižte složitost se stejnou architekturou a zásadami v místním prostředí i v cloudu.

* **Zotavení po havárii a virtuální plochy v cloudu Azure**

  * Navažte vzdálený přístup k datům, aplikacím a plochám v cloudu Azure. Díky připojením s vysokou šířkou pásma můžete data rychle nahrávat a stahovat z incidentů. Sítě s nízkou latencí poskytují rychlou odezvu, kterou uživatelé očekávají z desktopové aplikace.

  * Všechny zásady a sítě v cloudu se replikují pomocí portálu CloudSimple a známých nástrojů VMware. Tato replikace snižuje úsilí a riziko vytváření a správy implementací DR a VDI.

* **Vysoce výkonné aplikace a databáze**

  * Spouštějte nejnáročnější úlohy s využitím CloudSimple architektury, kterou nabízí.
  * Spouštějte Oracle, Microsoft SQL Server, middlewarové systémy a vysoce výkonné databáze bez databází SQL.

  * Vyzkoušejte si Cloud jako své vlastní datové centrum s vysokorychlostními síťovými připojeními 25 GB/s. Vysokorychlostní připojení umožňují spouštět hybridní aplikace, které se nacházejí v místním prostředí, VMware v Azure a soukromé úlohy Azure, aniž by došlo k narušení výkonu.

* **Skutečná hybridní**

  * Sjednotte DevOps napříč VMware a službami Azure.
  * Optimalizujte správu VMware pro služby a řešení Azure, které se dají použít napříč všemi vašimi úlohami.
  * Přístup k veřejným cloudovým službám bez nutnosti rozšíření datového centra nebo změna architekta aplikací.
  * Centralizovat identity, zásady řízení přístupu, protokolování a monitorování pro aplikace VMware v Azure.

## <a name="limits"></a>Omezení

Následující tabulka ukazuje omezení uzlu na prostředky privátního cloudu.

| Resource | Omezení |
|----------|-------|
| Minimální počet uzlů pro vytvoření privátního cloudu | 3 |
| Maximální počet uzlů v clusteru v privátním cloudu | 16 |
| Maximální počet uzlů v privátním cloudu | 64 |
| Minimální počet uzlů v novém clusteru | 3 |

## <a name="next-steps"></a>Další postup

* Informace o tom, jak [vytvořit privátní cloud](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Informace o tom, jak [nakonfigurovat prostředí privátního cloudu](quickstart-create-private-cloud.md)