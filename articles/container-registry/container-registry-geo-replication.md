---
title: Geografická replikace registru
description: Začněte vytvářet a spravovat geograficky replikovaný registr kontejnerů Azure, což umožňuje, aby registr poskytoval více oblastí s více hlavními místními replikami. Geografická replikace je funkcí úrovně Premium Service.
author: stevelas
ms.topic: article
ms.date: 07/21/2020
ms.author: stevelas
ms.openlocfilehash: 4e82be0e81e5e8c0182e061a0fba0f880bd45cc6
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632386"
---
# <a name="geo-replication-in-azure-container-registry"></a>Geografická replikace v Azure Container Registry

Společnosti, které chtějí místní přítomnost, nebo horkou zálohu, se rozhodne spouštět služby z více oblastí Azure. Je osvědčeným postupem umístit registr kontejneru do každé oblasti, kde se image budou spouštět. Díky tomu je možné provádět operace blízko sítě, což umožňuje rychlé a spolehlivé přenosy vrstvy image. Geografická replikace umožňuje službě Azure Container Registry fungovat jako jeden registr, který obsluhuje více oblastí s místními registry multi-master. 

Geograficky replikovaný registr nabízí tyto výhody:

* V několika oblastech se dá použít jeden registr, obrázek a názvy značek.
* Zvýšení výkonu a spolehlivosti regionálních nasazení s přístupem k registru pro ukončení sítě
* Snížení nákladů na přenos dat pomocí vrstev imagí z místního, replikovaného registru ve stejné nebo blízké oblasti jako hostitele kontejneru
* Jedna správa registru mezi různými oblastmi
* Odolnost registru, pokud dojde k oblastnímu výpadku

> [!NOTE]
> Pokud potřebujete zachovat kopie imagí kontejneru ve více než jednom registru kontejneru Azure, Azure Container Registry také podporuje [Import obrázků](container-registry-import-images.md). Například v pracovním postupu DevOps můžete importovat image z registru pro vývoj do produkčního registru, aniž byste museli používat příkazy Docker.
>

## <a name="example-use-case"></a>Příklad případu použití
Společnost Contoso spouští web veřejné přítomnosti, který se nachází v USA, Kanadě a Evropě. V rámci poskytování těchto trhů s obsahem místního a síťového zavírání spustí contoso clustery [Azure Kubernetes Service](../aks/index.yml) (AKS) v Západní USA, východní USA, Kanadě Central a západní Evropa. Webová aplikace, která je nasazena jako dokovací image, využívá stejný kód a obrázek napříč všemi oblastmi. Obsah, který je místní k této oblasti, se načte z databáze, která je v každé oblasti zřízená jednoznačně. Každé místní nasazení má svou jedinečnou konfiguraci pro prostředky, jako je místní databáze.

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

* Správa jednoho registru ve všech oblastech: `contoso.azurecr.io`
* Spravujte jednu konfiguraci nasazení imagí, protože všechny oblasti používají stejnou adresu URL obrázku: `contoso.azurecr.io/public/products/web:1.2`
* Doručovat do jednoho registru, zatímco ACR spravuje geografickou replikaci. ACR replikuje jenom jedinečné vrstvy, což snižuje přenos dat napříč oblastmi. 
* Nakonfigurujte místní [Webhooky](container-registry-webhook.md) , aby vás upozornily na události v konkrétních replikách.
* Poskytněte vysoce dostupný registr, který je odolný vůči výpadkům v oblasti regionu.

Azure Container Registry taky podporuje [zóny dostupnosti](zone-redundancy.md) pro vytvoření odolného a vysoce dostupného registru kontejnerů Azure v oblasti Azure. Kombinace zón dostupnosti pro redundanci v rámci jedné oblasti a geografická replikace napříč několika oblastmi zvyšuje spolehlivost a výkon registru.

## <a name="configure-geo-replication"></a>Konfigurace geografické replikace

Konfigurace geografické replikace je stejně jednoduchá jako při kliknutí na oblasti na mapě. Můžete také spravovat geografickou replikaci pomocí nástrojů, včetně příkazů [AZ ACR Replication](/cli/azure/acr/replication) v rozhraní příkazového řádku Azure CLI, nebo nasadit registr s povolenou geografickou replikaci pomocí [šablony Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry-geo-replication).

Geografická replikace je funkce pro [Registry Premium](container-registry-skus.md). Pokud váš registr ještě nemáte Premium, můžete v [Azure Portal](https://portal.azure.com)změnit z úrovně Basic a Standard na Premium:

![Přepínání úrovní služeb v Azure Portal](media/container-registry-skus/update-registry-sku.png)

Pokud chcete nakonfigurovat geografickou replikaci pro registr Premium, přihlaste se k Azure Portal v https://portal.azure.com .

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
* Když nahrajete nebo vyžádáte image z geograficky replikovaného registru, Azure Traffic Manager na pozadí pošle požadavek do registru v oblasti, která je nejblíže vaší latenci v síti.
* Po nahrání obrázku nebo aktualizace značky do nejbližší oblasti trvá Azure Container Registry pro replikaci manifestů a vrstev do zbývajících oblastí, do kterých jste se přihlásili. Větším imagí trvá replikace déle než menší. Image a značky jsou synchronizované v rámci replikačních oblastí s konečným modelem konzistence.
* Chcete-li spravovat pracovní postupy, které jsou závislé na nabízených aktualizacích do geograficky replikovaného registru, doporučujeme nakonfigurovat [Webhooky](container-registry-webhook.md) , aby reagovaly na nabízené události. Můžete nastavit regionální Webhooky v rámci geograficky replikovaného registru a sledovat tak nabízené události, které se dokončí napříč geograficky replikovanými oblastmi.
* Pro poskytování objektů blob, které představují vrstvy obsahu, Azure Container Registry používá koncové body dat. V každé z geograficky replikovaných oblastí v registru můžete povolit [vyhrazené koncové body dat](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) pro váš registr. Tyto koncové body umožňují konfiguraci přísně vymezených pravidel přístupu brány firewall. Pro účely řešení potíží můžete volitelně [zakázat směrování na replikaci](#temporarily-disable-routing-to-replication) při zachování replikovaných dat.
* Pokud nakonfigurujete [privátní odkaz](container-registry-private-link.md) pro váš registr pomocí privátních koncových bodů ve virtuální síti, budou ve výchozím nastavení povoleny vyhrazené koncové body dat v každé z geograficky replikovaných oblastí. 

## <a name="delete-a-replica"></a>Odstranění repliky

Jakmile nakonfigurujete repliku pro svůj registr, můžete ji kdykoli odstranit, pokud už ji nepotřebujete. Odstraňte repliku pomocí Azure Portal nebo jiných nástrojů, jako je příkaz [AZ ACR Replication Delete](/cli/azure/acr/replication#az-acr-replication-delete) v rozhraní příkazového řádku Azure CLI.

Odstranění repliky v Azure Portal:

1. Přejděte do Azure Container Registry a vyberte **replikace**.
1. Vyberte název repliky a vyberte **Odstranit**. Potvrďte, že chcete repliku odstranit.

Použití rozhraní příkazového řádku Azure k odstranění repliky *myregistry* v oblasti východní USA:

```azurecli
az acr replication delete --name eastus --registry myregistry
```

## <a name="geo-replication-pricing"></a>Ceny geografické replikace

Geografická replikace je funkce [úrovně Premium služby](container-registry-skus.md) Azure Container Registry. Při replikaci registru do požadovaných oblastí se vám za každou oblast účtují poplatky za prémiové Registry.

V předchozím příkladu společnost Contoso konsoliduje dvě Registry dolů na jednu a přidávají repliky do Východní USA, Kanady Central a Západní Evropa. Společnost Contoso by platila čtyřikrát za měsíc, a to bez další konfigurace nebo správy. Každá oblast teď znovu načte své image a zlepší výkon, spolehlivost bez poplatků za přenos ze sítě z Západní USA do Kanady a Východní USA.

## <a name="troubleshoot-push-operations-with-geo-replicated-registries"></a>Řešení potíží s nabízenými operacemi s geograficky replikovanými Registry
 
Klient Docker, který nahraje image do geograficky replikovaného registru, nemusí do jedné replikované oblasti vložit všechny vrstvy obrázků a její manifest. K tomu může dojít, protože Azure Traffic Manager směruje požadavky registru do replikovaného registru nejbližšího ze sítě. Pokud registr obsahuje dvě *blízké* oblasti replikace, vrstvy obrázků a manifesty, mohou být distribuovány do těchto dvou lokalit a při ověření manifestu dojde k chybě operace push. K tomuto problému dochází kvůli způsobu, jakým se název DNS registru vyřeší na některých hostitelích se systémem Linux. K tomuto problému nedochází ve Windows, který poskytuje mezipaměť DNS na straně klienta.
 
Pokud k tomuto problému dojde, jedním z řešení je použít mezipaměť DNS na straně klienta, například `dnsmasq` na hostiteli se systémem Linux. To pomáhá zajistit, že se název registru vyřeší konzistentně. Pokud k odeslání do registru používáte virtuální počítač Linux v Azure, přečtěte si část možnosti [překladu názvů DNS pro virtuální počítače se systémem Linux v Azure](../virtual-machines/linux/azure-dns.md).

K optimalizaci překladu DNS na nejbližší replice při vkládání imagí nakonfigurujete geograficky replikovaný registr ve stejných oblastech Azure jako zdroj nabízených operací nebo nejbližší oblast při práci mimo Azure.

### <a name="temporarily-disable-routing-to-replication"></a>Dočasné zakázání směrování do replikace

Pokud chcete řešit potíže s geograficky replikovaným registrem, můžete chtít dočasně zakázat Traffic Manager směrování do jedné nebo více replikací. Počínaje verzí 2,8 Azure CLI můžete nakonfigurovat `--region-endpoint-enabled` možnost (Preview) při vytváření nebo aktualizaci replikované oblasti. Když nastavíte `--region-endpoint-enabled` možnost replikace na `false` , Traffic Manager už do této oblasti směrovat žádosti Docker push nebo Pull. Ve výchozím nastavení je směrování na všechny replikace povolené a synchronizace dat mezi všemi replikacemi probíhá, ať už je směrování povolené nebo zakázané.

Pokud chcete zakázat směrování do existující replikace, nejdřív spusťte příkaz [AZ ACR Replication list][az-acr-replication-list] a seznamte se s replikacemi v registru. Pak spusťte příkaz [AZ ACR replikace Update][az-acr-replication-update] a nastavte `--region-endpoint-enabled false` pro konkrétní replikaci. Například ke konfiguraci nastavení pro *westus* replikace v *myregistry*:

```azurecli
# Show names of existing replications
az acr replication list --registry --output table

# Disable routing to replication
az acr replication update --name westus \
  --registry myregistry --resource-group MyResourceGroup \
  --region-endpoint-enabled false
```

Postup obnovení směrování do replikace:

```azurecli
az acr replication update --name westus \
  --registry myregistry --resource-group MyResourceGroup \
  --region-endpoint-enabled true
```

## <a name="next-steps"></a>Další kroky

Podívejte se na tři části kurzu, [geografickou replikaci v Azure Container Registry](container-registry-tutorial-prepare-registry.md). Projděte si vytvoření geograficky replikovaného registru, vytvoření kontejneru a jeho nasazení s jediným `docker push` příkazem pro více oblastí Web Apps pro instance kontejnerů.

> [!div class="nextstepaction"]
> [Geografická replikace v Azure Container Registry](container-registry-tutorial-prepare-registry.md)

[az-acr-replication-list]: /cli/azure/acr/replication#az-acr-replication-list
[az-acr-replication-update]: /cli/azure/acr/replication#az-acr-replication-update
