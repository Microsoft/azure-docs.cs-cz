---
title: Geografická replikace služby Azure Container Registry
description: Začněte vytvářet a spravovat geograficky replikované Registry kontejnerů Azure.
services: container-registry
author: stevelas
manager: gwallace
ms.service: container-registry
ms.topic: overview
ms.date: 05/24/2019
ms.author: stevelas
ms.openlocfilehash: 2fffa3b063969cbe68fb9a405f4198f15b3f9809
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845201"
---
# <a name="geo-replication-in-azure-container-registry"></a>Geografická replikace ve službě Azure Container Registry

Společnosti, které chtějí místní přítomnost, nebo horkou zálohu, se rozhodne spouštět služby z více oblastí Azure. Osvědčeným postupem je umístění registru kontejneru v každé oblasti, kde se image spouštějí, umožňuje operace zavírání sítě a povoluje rychlé a spolehlivé přenosy vrstev imagí. Geografická replikace umožňuje službě Azure Container Registry fungovat jako jeden registr, který obsluhuje více oblastí s více hlavními místními Registry. 

Geograficky replikovaný registr přináší následující výhody:

* V několika oblastech se dá použít jeden registr, image/názvy značek.
* Přístup k registru sítě – zavření z regionálních nasazení
* Neúčtují žádné další výstupní poplatky, protože se z místního replikovaného registru, který je v rámci stejné oblasti jako hostitel kontejneru, neúčtují image
* Jediná Správa registru v různých oblastech

> [!NOTE]
> Pokud potřebujete zachovat kopie imagí kontejneru ve více než jednom registru kontejneru Azure, Azure Container Registry také podporuje [Import obrázků](container-registry-import-images.md). Například v pracovním postupu DevOps můžete importovat image z registru pro vývoj do produkčního registru, aniž byste museli používat příkazy Docker.
>

## <a name="example-use-case"></a>Příklad případu použití
Společnost Contoso spouští web veřejné přítomnosti, který se nachází v USA, Kanadě a Evropě. V rámci poskytování těchto trhů s obsahem místního a síťového zavírání spustí contoso clustery [Azure Kubernetes Service](/azure/aks/) (AKS) v Západní USA, východní USA, Kanadě Central a západní Evropa. Webová aplikace, která je nasazena jako dokovací image, využívá stejný kód a obrázek napříč všemi oblastmi. Obsah, který je místní k této oblasti, se načte z databáze, která je v každé oblasti zřízená jednoznačně. Každé místní nasazení má svou jedinečnou konfiguraci pro prostředky, jako je místní databáze.

Vývojový tým se nachází v Seattlu WA, který využívá Západní USA datové centrum.

![Doručování do více registrů](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Doručování do více registrů*

Před použitím funkcí geografické replikace měl společnost Contoso v Západní USA registr založený na USA, s dalším registrem v Západní Evropa. Pro obsluhu těchto různých oblastí vývojový tým odeslal obrázky do dvou různých registrů.

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![Přijímání z více registrů](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Přijímání z více registrů*

Mezi obvyklé výzvy k více registrům patří:

* Centrální clustery Východní USA, Západní USA a Kanada vyžádají z registru Západní USA a vyžádají si výstupní poplatky, protože každý z těchto vzdálených kontejnerů přebírá obrázky z Západní USA datových center.
* Vývojový tým musí vkládat obrázky do Západní USA a Západní Evropa Registry.
* Vývojový tým musí nakonfigurovat a spravovat každé místní nasazení s názvy imagí, které odkazují na místní registr.
* Pro každou oblast je nutné nakonfigurovat přístup k registru.

## <a name="benefits-of-geo-replication"></a>Výhody geografické replikace

![Přijímání z geograficky replikovaného registru](media/container-registry-geo-replication/after-geo-replicate-pull.png)

Pomocí funkce geografické replikace Azure Container Registry jsou tyto výhody realizované:

* Správa jednoho registru ve všech oblastech:`contoso.azurecr.io`
* Spravujte jednu konfiguraci nasazení imagí, protože všechny oblasti používají stejnou adresu URL obrázku:`contoso.azurecr.io/public/products/web:1.2`
* Doručovat do jednoho registru, zatímco ACR spravuje geografickou replikaci. Místní Webhooky [](container-registry-webhook.md) můžete nakonfigurovat tak, aby vás upozornily na události v určitých replikách.

## <a name="configure-geo-replication"></a>Konfigurace geografické replikace

Konfigurace geografické replikace je stejně jednoduchá jako při kliknutí na oblasti na mapě. Můžete taky spravovat geografickou replikaci pomocí nástrojů, včetně příkazů [AZ ACR](/cli/azure/acr/replication) Replication v Azure CLI.

Geografická replikace je funkce jenom pro [Registry úrovně Premium](container-registry-skus.md) . Pokud váš registr ještě nemáte Premium, můžete v [Azure Portal](https://portal.azure.com)změnit z úrovně Basic a Standard na Premium:

![Přepínání SKU v Azure Portal](media/container-registry-skus/update-registry-sku.png)

Pokud chcete nakonfigurovat geografickou replikaci pro registr Premium, přihlaste se k Azure Portal https://portal.azure.com v.

Přejděte do Azure Container Registry a vyberte **replikace**:

![Replikace v uživatelském rozhraní registru kontejnerů na webu Azure Portal](media/container-registry-geo-replication/registry-services.png)

Zobrazí se mapa zobrazující všechny aktuální oblasti Azure:

 ![Mapa oblastí na webu Azure Portal](media/container-registry-geo-replication/registry-geo-map.png)

* Modré šestiúhelníky reprezentují aktuální repliky.
* Zelené šestiúhelníky reprezentují možné oblasti repliky.
* Šedé šestiúhelníky reprezentují oblasti Azure, které ještě nejsou k dispozici pro replikaci.

Pokud chcete repliku nakonfigurovat, vyberte zelený šestiúhelník a pak vyberte **vytvořit**:

 ![Vytvoření replikace v uživatelském rozhraní na webu Azure Portal](media/container-registry-geo-replication/create-replication.png)

Pokud chcete nakonfigurovat další repliky, vyberte zelené šestiúhelníky pro ostatní oblasti a pak klikněte na **vytvořit**.

ACR zahájí synchronizaci imagí napříč nakonfigurovanými replikami. Po dokončení se portál odráží jako *připravený*. Stav repliky na portálu se neaktualizuje automaticky. Aktualizovaný stav zobrazíte pomocí tlačítka aktualizovat.

## <a name="considerations-for-using-a-geo-replicated-registry"></a>Předpoklady pro použití geograficky replikovaného registru

* Každá oblast v geograficky replikovaném registru je po nastavení nezávislá. Azure Container Registry SLA platí pro každou geograficky replikovanou oblast.
* Když nahrajete nebo vyžádáte image z geograficky replikovaného registru, Azure Traffic Manager na pozadí odešle požadavek do registru v oblasti, která je nejblíže vám.
* Po nahrání obrázku nebo aktualizace značky do nejbližší oblasti trvá Azure Container Registry pro replikaci manifestů a vrstev do zbývajících oblastí, do kterých jste se přihlásili. Větším imagí trvá replikace déle než menší. Image a značky jsou synchronizované v rámci replikačních oblastí s konečným modelem konzistence.
* Chcete-li spravovat pracovní postupy, které jsou závislé na nabízených aktualizacích do geograficky replikovaného [](container-registry-webhook.md) registru, doporučujeme nakonfigurovat Webhooky, aby reagovaly na nabízené události. Můžete nastavit regionální Webhooky v rámci geograficky replikovaného registru a sledovat tak nabízené události, které se dokončí napříč geograficky replikovanými oblastmi.


## <a name="geo-replication-pricing"></a>Ceny geografické replikace

Geografická replikace je funkce Azure Container Registry SKU úrovně [Premium](container-registry-skus.md) . Při replikaci registru do požadovaných oblastí se vám za každou oblast účtují poplatky za prémiové Registry.

V předchozím příkladu společnost Contoso konsoliduje dvě Registry dolů na jednu a přidávají repliky do Východní USA, Kanady Central a Západní Evropa. Společnost Contoso by platila čtyřikrát za měsíc, a to bez další konfigurace nebo správy. Každá oblast teď znovu načte své image a zlepší výkon, spolehlivost bez poplatků za přenos ze sítě z Západní USA do Kanady a Východní USA.

## <a name="next-steps"></a>Další postup

Podívejte se na tři části kurzu, geografickou [replikaci v Azure Container Registry](container-registry-tutorial-prepare-registry.md). Projděte si vytvoření geograficky replikovaného registru, vytvoření kontejneru a jeho nasazení s jediným `docker push` příkazem pro více oblastí Web Apps pro instance kontejnerů.

> [!div class="nextstepaction"]
> [Geografická replikace v Azure Container Registry](container-registry-tutorial-prepare-registry.md)
