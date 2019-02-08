---
title: Geografická replikace služby Azure container registry
description: Začínáme vytvářet a spravovat registry kontejnerů Azure geograficky replikovaný.
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: overview-article
ms.date: 04/10/2018
ms.author: stevelas
ms.openlocfilehash: a83cf6b37a28ec38165778faa7a9ecc266cce7bd
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55858252"
---
# <a name="geo-replication-in-azure-container-registry"></a>Geografická replikace ve službě Azure Container Registry

Společnosti, které má s lokální přítomností nebo hot zálohování zvolte ke spouštění služeb v několika oblastech Azure. Jako osvědčený postup umístění registru kontejnerů v jednotlivých oblastech, kde jsou spouštěny imagí umožňuje operace blízko sítě, umožňuje rychlé a spolehlivé image vrstvy přenosy. Geografická replikace umožňuje službě Azure container registry fungovala jako jeden registr obsluhuje více oblastí s několika hlavními databázemi regionální Registry.

Geograficky replikovaný registr poskytuje následující výhody:

* Jediné názvy registru / / značka obrázku je možné napříč několika oblastmi
* Přístup k registru blízko sítě z regionálních nasazení
* Žádné poplatky za další odchozí přenosy jako obrázky se berou z místní replikovaný registr ve stejné oblasti jako váš hostitel kontejneru
* Správu jednoho registru napříč několika oblastmi

> [!NOTE]
> Pokud je potřeba udržovat kopie imagí kontejnerů ve více než jeden Azure container registry, Azure Container Registry podporuje také [obrázku import](container-registry-import-images.md). Například v pracovním postupu DevOps, můžete importovat image z registru vývoje do registru produkčního prostředí, aniž by bylo nutné použít příkazy Dockeru.
>

## <a name="example-use-case"></a>Případy použití příklad
Contoso se spustí web veřejné přítomnost umístění v rámci USA, Kanadě a Evropa. K poskytování těchto trzích s místní a síťové blízkosti obsah, spustí Contoso [Azure Kubernetes Service](/azure/aks/) clusterů (AKS) v oblasti západní USA, USA – východ, Kanada – střed a západní Evropa. Webovou aplikaci, nasazené jako image Dockeru, využívá stejný kód a image ve všech oblastech. Z databáze, která je jednoznačně zřízené v jednotlivých oblastech se načítá obsah, místní pro tuto oblast. Každý místní nasazení má jeho jedinečnou konfiguraci pro zdroje, jako jsou místní databáze.

Vývojový tým se nachází v Seattlu, WA, využitím datové centrum západ USA.

![Doručením (push) do více registrů](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Doručením (push) do více registrů*

Před použitím funkce geografickou replikaci, měl Contoso registru umístěné v USA v oblasti západní USA s další registru v oblasti západní Evropa. K poskytování těchto různých oblastech, vývojový tým do dvou různých registry nabídnout Image.

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![Přijímání změn z více registrů](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Přijímání změn z více registrů*

Typické obtíže spojené s více registrů patří:

* Východní USA, západní USA a Kanada – střed clustery všechny o přijetí změn z registru USA – Západ, jak každá z těchto hostitelů vzdáleném kontejneru přetahování imagí z datových centrech USA – západ za něho poplatky za odchozí přenosy.
* Vývojový tým musí nahrávání imagí do registrů západní USA a západní Evropa.
* Vývojový tým musí konfiguraci a údržbu každé místní nasazení s názvy bitové kopie odkazující na místním registru.
* Přístup k registru musí být nakonfigurovaný pro každou oblast.

## <a name="benefits-of-geo-replication"></a>Výhody funkce geografické replikace

![Přijímání změn z geograficky replikovaného registru](media/container-registry-geo-replication/after-geo-replicate-pull.png)

Pomocí funkce geografické replikace ve službě Azure Container Registry, jsou realizované tyto výhody:

* Správa jednoho registru napříč všemi oblastmi: `contoso.azurecr.io`
* Spravovat jediné konfiguraci nasazení bitových kopií všech oblastech použít stejná adresa URL obrázku: `contoso.azurecr.io/public/products/web:1.2`
* Odeslat do jednoho registru, zatímco ACR spravuje geografickou replikaci, včetně regionální webhooky pro místní oznámení

## <a name="configure-geo-replication"></a>Konfigurace geografické replikace
Konfigurace geografické replikace je stejně snadné jako kliknutí na oblasti na mapě.

Geografická replikace je funkce [Premium Registry](container-registry-skus.md) pouze. Pokud váš registr není dosud Premium, můžete změnit z úrovní Basic a Standard na Premium v [webu Azure portal](https://portal.azure.com):

![Přepínání skladové položky na webu Azure Portal](media/container-registry-skus/update-registry-sku.png)

Konfigurace geografické replikace pro svůj registr úrovně Premium, přihlaste se k webu Azure portal na http://portal.azure.com.

Přejděte do služby Azure Container Registry a vyberte **replikace**:

![Replikace v uživatelském rozhraní registru kontejnerů na webu Azure Portal](media/container-registry-geo-replication/registry-services.png)

Zobrazí se mapa se zobrazují všechny aktuální oblasti Azure:

 ![Mapa oblastí na webu Azure Portal](media/container-registry-geo-replication/registry-geo-map.png)

* Modré Šestiúhelníky představují aktuální repliky
* Zelená Šestiúhelníky představují možné repliky oblastí
* Šedé Šestiúhelníky představují oblasti Azure, které ještě není k dispozici pro replikaci

Abyste konfigurovali repliku, vyberte zelený šestiúhelník a pak vyberte **vytvořit**:

 ![Vytvoření replikace v uživatelském rozhraní na webu Azure Portal](media/container-registry-geo-replication/create-replication.png)

Pokud chcete nakonfigurovat další repliky, vyberte zelenou Šestiúhelníky pro ostatní oblasti a pak klikněte na **vytvořit**.

ACR zahájí synchronizaci imagí v nakonfigurovaných repliky. Jakmile budete hotovi, portálu odráží *připravené*. Stav repliky na portálu nebude automaticky aktualizovat. Pomocí tlačítka Aktualizovat zobrazí aktualizovaný stav.

## <a name="geo-replication-pricing"></a>Ceny za geografickou replikaci

Geografická replikace je funkce [SKU úrovně Premium](container-registry-skus.md) ve službě Azure Container Registry. Při replikaci registru do požadované oblasti se vám účtovat žádné poplatky registru úrovně Premium pro každou oblast.

V předchozím příkladu Contoso konsolidovat dvě registry dolů, přidání repliky do USA – východ, Kanada – střed a západní Evropa. Contoso v domácnosti platíte Premium čtyřikrát za měsíc, bez další konfigurace nebo správy. Každá oblast nyní načítá své obrázky místně, zvýšení výkonu, spolehlivosti bez sítě poplatky za odchozí přenosy z USA – západ na Kanada a USA – východ.

## <a name="summary"></a>Souhrn

S geografickou replikací můžete spravovat místní datová centra jako jeden globální cloud. Image využívají napříč řadou služeb Azure, můžete využívat výhod rovina správy a přitom síťové blízkosti rychlé a spolehlivé místní image si vyžádá.

## <a name="next-steps"></a>Další postup

Podívejte se na část třídílné série kurzů, [geografické replikace ve službě Azure Container Registry](container-registry-tutorial-prepare-registry.md). Provede vytvoření geograficky replikovaného registru sestavení kontejneru a jeho nasazení pomocí jediného `docker push` příkazu k více místní Web Apps for Containers instancí.

> [!div class="nextstepaction"]
> [Geografická replikace ve službě Azure Container Registry](container-registry-tutorial-prepare-registry.md)
