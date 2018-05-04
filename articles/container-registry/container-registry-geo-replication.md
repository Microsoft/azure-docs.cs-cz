---
title: Geografická replikace registru Azure kontejneru
description: Začínáme s vytváření a správa registrech geograficky replikované kontejner Azure.
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: overview-article
ms.date: 10/24/2017
ms.author: stevelas
ms.openlocfilehash: 6b82f49d2bf4ed321f5e847d11780535e01531f7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="geo-replication-in-azure-container-registry"></a>Geografická replikace ve službě Azure Container Registry

Společnosti, které chcete místní přítomnosti nebo zálohu aktivní zvolit ke spouštění služeb z několika oblastmi Azure. Jako osvědčený postup umístění registru kontejneru v jednotlivých oblastech, kde se spouštějí bitové kopie umožňuje operace, zavřete sítě, povolení rychlé, spolehlivé image vrstvy přenosy.

Geografická replikace umožňuje registru kontejner Azure jako jeden registru, obsluhovat více oblastí s více hlavních regionální registrech.

> [!IMPORTANT]
> Funkce geografické replikace ve službě Azure Container Registry je aktuálně ve verzi **Preview**. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Některé aspekty této funkce se můžou před zveřejněním změnit.
>

Registr geograficky replikované poskytuje následující výhody:

* Nad několika oblastmi se dají použít jeden názvy registru nebo nebo značky obrázku
* Přístup k síti zavřít registru z místního nasazení
* Žádné další odchozí poplatky jako obrázky vyjmutí z místní, replikované registru ve stejné oblasti jako hostitele kontejneru
* Správy registru nad několika oblastmi

## <a name="example-use-case"></a>Příklad případ použití
Contoso spustí web veřejné přítomnosti nachází v USA, Evropa i Kanada. K obsluze těchto trzích s obsahem, místní a sítě zavřít, spustí Contoso [Azure Container Service](/azure/container-service/kubernetes/) (ACS) Kubernetes clusterů v západní USA, Východ USA, Střední Kanada a západní Evropa. Webovou aplikaci, nasazené jako bitovou kopii Docker využívá stejný kód a image přes všechny oblasti. Obsah, místní pro danou oblast, se načtou z databáze, který je jednoznačně zřízené v každé oblasti. Každé místní nasazení má jedinečný konfiguraci pro prostředky jako místní databázi.

Vývojový tým je umístěný v Praze WA, využitím datového centra západní USA.

![Když zavedete více registry](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Když zavedete více registry*

Před použitím funkce geografická replikace, měl Contoso registru na základě USA v západní USA s další registru v oblasti západní Evropa. K obsluze těchto různých oblastech, vývojový tým museli push bitové kopie na dvou různých registrech.

```bash
docker push contoso.azurecr.io/pubic/products/web:1.2
docker push contosowesteu.azurecr.io/pubic/products/web:1.2
```
![Stahování z více registry](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Stahování z více registry*

Typické výzvy více registrech patří:

* Východ USA, západní USA a Kanada centrální clustery všechny načítat z registru západní USA, by docházelo poplatky za odchozí data, jak každý z těchto hostitelů vzdálené kontejneru načítat bitové kopie z datových centrech západní USA.
* Vývojový tým musí nabízená bitové kopie registrech západní USA a západní Evropa.
* Vývojový tým musí konfigurovat a spravovat každý místní nasazení s názvy bitové kopie odkazující na místním registru.
* Přístup k registru musí být nakonfigurované pro každou oblast.

## <a name="benefits-of-geo-replication"></a>Výhody geografická replikace

![Stahování z geograficky replikované registru](media/container-registry-geo-replication/after-geo-replicate-pull.png)

Pomocí funkce geografická replikace registru kontejner Azure, jsou realizovány tyto výhody:

* Spravovat jeden registru přes všechny oblasti: `contoso.azurecr.io`
* Správa konfigurací jedné nasazení bitové kopie jako všech oblastech použít stejná adresa URL obrázku: `contoso.azurecr.io/public/products/web:1.2`
* Nabízená jeden registru, zatímco ACR spravuje geografickou replikaci, včetně regionální webhooky pro místní oznámení

## <a name="configure-geo-replication"></a>Konfigurace geografické replikace
Konfigurace geografická replikace je stejně snadná jako při kliknutí oblasti na mapě.

Geografická replikace je funkce [Premium registrech](container-registry-skus.md) pouze. Pokud vaše registru není ještě Premium, můžete změnit na Basic a Standard Premium v [portál Azure](https://portal.azure.com):

![Přepínání SKU na portálu Azure](media/container-registry-skus/update-registry-sku.png)

Ke konfiguraci geografická replikace pro váš registru Premium, přihlaste se k portálu Azure v http://portal.azure.com.

Přejděte do kontejneru registr Azure a vyberte **replikace**:

![Replikace v uživatelském rozhraní registru kontejnerů na webu Azure Portal](media/container-registry-geo-replication/registry-services.png)

Zobrazí se mapu, zobrazuje všechny aktuální oblasti Azure:

 ![Mapa oblastí na webu Azure Portal](media/container-registry-geo-replication/registry-geo-map.png)

* Modré Šestiúhelníky představují aktuální repliky
* Zelená Šestiúhelníky představují možné repliky oblastí
* Šedé Šestiúhelníky představují oblastí Azure ještě není k dispozici pro replikaci

Pokud chcete nakonfigurovat repliku, vyberte zelená šestiúhelníku a pak vyberte **vytvořit**:

 ![Vytvoření replikace v uživatelském rozhraní na webu Azure Portal](media/container-registry-geo-replication/create-replication.png)

Pokud chcete nakonfigurovat další repliky, vyberte zelená šestiúhelníků pro jiné oblasti a pak klikněte na **vytvořit**.

ACR zahájí synchronizaci bitové kopie napříč nakonfigurované repliky. Po dokončení portálu odráží *připraven*. Stav repliky na portálu nebude aktualizovat automaticky. Použijte tlačítko Aktualizovat zobrazíte aktualizovaný stav.

## <a name="geo-replication-pricing"></a>Geografická replikace ceny

Geografická replikace je funkce [skladová položka Premium](container-registry-skus.md) registru kontejner Azure. Při replikaci registru na vaše požadované oblasti, platit poplatky registru Premium pro každou oblast.

V předchozím příkladu Contoso konsolidovat dvě registrech na jedinou, přidání repliky do Východ USA, Střední Kanada a západní Evropa. Contoso by platila Premium čtyřikrát za měsíc bez další konfigurace a správy. Každá oblast nyní vrátí jejich obrázků místně, zvýšení výkonu, spolehlivosti bez poplatků odchozí sítě z západní USA Kanady a východní USA.

## <a name="summary"></a>Souhrn

S geografickou replikací můžete spravovat místní datových centrech jako globální jeden cloud. Bitové kopie se používají mezi řadou služeb Azure, můžete využívat výhod správy roviny při zachování sítě zavřít, rychlé a spolehlivé obrázek místní vrátí.

## <a name="next-steps"></a>Další postup

Podívejte se na kurz řady třemi částmi, [geografická replikace v registru kontejner Azure](container-registry-tutorial-prepare-registry.md). Provede procesem vytvoření registru geograficky replikované, sestavování kontejner a jeho nasazení s jedním `docker push` příkaz více místní webové aplikace pro kontejnery instancí.

> [!div class="nextstepaction"]
> [Geografická replikace v registru kontejner Azure](container-registry-tutorial-prepare-registry.md)