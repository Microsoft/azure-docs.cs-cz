---
title: Geografická replikace registru
description: Začínáme vytvářet a spravovat geograficky replikovaný registr kontejnerů Azure, který umožňuje registru obsluhovat více oblastí s místními replikami s více servery.
author: stevelas
ms.topic: article
ms.date: 08/16/2019
ms.author: stevelas
ms.openlocfilehash: d238de30e458261a11c941c03ac127c732ca8d3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456442"
---
# <a name="geo-replication-in-azure-container-registry"></a>Geografická replikace v registru kontejnerů Azure

Společnosti, které chtějí místní přítomnost nebo horkou zálohu, se rozhodnou spouštět služby z více oblastí Azure. Je osvědčeným postupem umístit registr kontejneru do každé oblasti, kde se image budou spouštět. Díky tomu je možné provádět operace blízko sítě, což umožňuje rychlé a spolehlivé přenosy vrstvy image. Geografická replikace umožňuje službě Azure Container Registry fungovat jako jeden registr, který obsluhuje více oblastí s místními registry multi-master. 

Geograficky replikovaný registr nabízí tyto výhody:

* V několika oblastech se dá použít jediný název registru, image nebo značky.
* Přístup k registru v blízkosti sítě z místních nasazení
* Žádné další poplatky za výchozí provoz, protože se image načítají z místního replikovaného registru ve stejné oblasti jako hostitel kontejneru
* Jedna správa registru mezi různými oblastmi

> [!NOTE]
> Pokud potřebujete udržovat kopie ibi kontejnerů ve více než jednom registru kontejnerů Azure, Azure Container Registry také podporuje [import bitových kopií](container-registry-import-images.md). Například v pracovním postupu DevOps můžete importovat bitovou kopii z vývojového registru do produkčního registru, aniž byste museli používat příkazy Dockeru.
>

## <a name="example-use-case"></a>Příklad případu použití
Contoso provozuje webovou stránku veřejné prezentace umístěné v USA, Kanadě a Evropě. Aby společnost Contoso obsluhuje tyto trhy s místním obsahem a obsahem blízkým sítě, provozuje clustery [Služby Azure Kubernetes](/azure/aks/) Service (AKS) v západních USA, východních USA, Kanada – střed a Západní Evropa. Webová aplikace, nasazená jako image Dockeru, využívá stejný kód a image ve všech oblastech. Obsah, místní pro tuto oblast, se načte z databáze, která je zřízena jedinečně v každé oblasti. Každé regionální nasazení má svou jedinečnou konfiguraci pro prostředky, jako je místní databáze.

Vývojový tým se nachází v Seattlu WA, s využitím západní americké datové centrum.

![Tlačení do více registrů](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Tlačení do více registrů*

Před použitím funkcí geografické replikace měla společnost Contoso v západní CHUF registr se sídlem v USA s dalším registrem v západní Evropě. Chcete-li sloužit těmto různým oblastem, vývojový tým posunul obrázky do dvou různých registrů.

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![Vytažení z více registrů](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Vytažení z více registrů*

Mezi typické problémy více registrů patří:

* Východní US, Západ USA a Kanada Centrální clustery všechny vytáhnout z registru západ USA, vznikat odchozí poplatky jako každý z těchto vzdálených kontejnerů hostitelů vytáhnout obrázky ze západních amerických datových center.
* Vývojový tým musí obrázky tlačit do registrů západní USA a západní Evropy.
* Vývojový tým musí nakonfigurovat a udržovat každé regionální nasazení s názvy bitových obrázků odkazujících na místní registr.
* Přístup k registru musí být nakonfigurován pro každou oblast.

## <a name="benefits-of-geo-replication"></a>Výhody geografické replikace

![Vytažení z geograficky replikovaného registru](media/container-registry-geo-replication/after-geo-replicate-pull.png)

Pomocí funkce geografické replikace azure container registru jsou tyto výhody realizovány:

* Správa jednoho registru ve všech oblastech:`contoso.azurecr.io`
* Spravujte jednu konfiguraci nasazení bitových bitových zjištění, protože všechny oblasti používaly stejnou adresu URL bitové kopie:`contoso.azurecr.io/public/products/web:1.2`
* Push do jednoho registru, zatímco ACR spravuje geografickou replikaci. Můžete nakonfigurovat místní [webhooky,](container-registry-webhook.md) které vás upozorní na události v konkrétních replikách.

## <a name="configure-geo-replication"></a>Konfigurace geografické replikace

Konfigurace geografické replikace je stejně snadná jako kliknutí na oblasti na mapě. Geografickou replikaci můžete také spravovat pomocí nástrojů, včetně příkazů [replikace az acr](/cli/azure/acr/replication) v rozhraní příkazového příkazu Azure CLI, nebo nasadit registr povolený pro geografickou replikaci pomocí [šablony Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry-geo-replication).

Geografická replikace je pouze funkce [prémiových registrů.](container-registry-skus.md) Pokud váš registr ještě není Premium, můžete změnit ze základní a standardní na Premium na [webu Azure Portal](https://portal.azure.com):

![Přepínání sou na webu Azure Portal](media/container-registry-skus/update-registry-sku.png)

Chcete-li nakonfigurovat geografickou replikaci pro váš https://portal.azure.comregistr Premium, přihlaste se na webu .

Přejděte do registru kontejnerů Azure a vyberte **Replikace**:

![Replikace v uživatelském rozhraní registru kontejnerů na webu Azure Portal](media/container-registry-geo-replication/registry-services.png)

Zobrazí se mapa zobrazující všechny aktuální oblasti Azure:

 ![Mapa oblastí na webu Azure Portal](media/container-registry-geo-replication/registry-geo-map.png)

* Modré šestiúhelníky představují aktuální repliky
* Zelené šestiúhelníky představují možné oblasti replik
* Šedé šestiúhelníky představují oblasti Azure, které ještě nejsou k dispozici pro replikaci.

Chcete-li nakonfigurovat repliku, vyberte zelený šestiúhelník a pak vyberte **Vytvořit**:

 ![Vytvoření replikace v uživatelském rozhraní na webu Azure Portal](media/container-registry-geo-replication/create-replication.png)

Chcete-li konfigurovat další repliky, vyberte zelené šestiúhelníky pro jiné oblasti a klepněte na tlačítko **Vytvořit**.

ACR začne synchronizovat bitové kopie mezi nakonfigurovanými replikami. Po dokončení, portál odráží *Ready*. Stav repliky na portálu se automaticky neaktualizuje. Pomocí tlačítka aktualizace zobrazíte aktualizovaný stav.

## <a name="considerations-for-using-a-geo-replicated-registry"></a>Důležité informace o použití geograficky replikovaného registru

* Každá oblast v geograficky replikovaném registru je po nastavení nezávislá. SLA registru kontejnerů Azure platí pro každou geograficky replikovanou oblast.
* Když stlačíte nebo vyžádáte image z geograficky replikovaného registru, Azure Traffic Manager na pozadí odešle požadavek do registru umístěného v oblasti, která je vám nejblíže.
* Po nabízení aktualizace bitové kopie nebo značky do nejbližší oblasti trvá nějakou dobu, než registr kontejnerů Azure replikuje manifesty a vrstvy do zbývajících oblastí, do kterých jste se přihlásili. Větší obrázky trvat déle než menší. Bitové kopie a značky jsou synchronizovány v rámci oblastí replikace s případným modelem konzistence.
* Chcete-li spravovat pracovní postupy, které závisí na nabízených aktualizacích geograficky replikované , doporučujeme nakonfigurovat [webhooky](container-registry-webhook.md) tak, aby reagovaly na události nabízených služeb. Můžete nastavit místní webhooky v rámci geograficky replikovaného registru pro sledování nabízených událostí při jejich dokončení v oblastech geograficky replikovaných.

## <a name="delete-a-replica"></a>Odstranění repliky

Po nakonfigurování repliky pro registr ji můžete kdykoli odstranit, pokud již není potřeba. Odstraňte repliku pomocí portálu Azure nebo jiných nástrojů, jako je příkaz [odstranění replikace az acr](/cli/azure/acr/replication#az-acr-replication-delete) v příkazu Azure CLI.

Odstranění repliky na webu Azure Portal:

1. Přejděte do registru kontejnerů Azure a vyberte **Replikace**.
1. Vyberte název repliky a vyberte **Odstranit**. Potvrďte, že chcete repliku odstranit.

> [!NOTE]
> Repliku registru nelze odstranit v *domovské oblasti* registru, tj. Domovskou repliku můžete odstranit pouze odstraněním samotného registru.

## <a name="geo-replication-pricing"></a>Ceny geografické replikace

Geografická replikace je funkce [hlavní skladové položky](container-registry-skus.md) registru kontejnerů Azure. Při replikaci registru do požadovaných oblastí vám za každou oblast účtují poplatky za registr Premium.

V předchozím příkladu contoso konsolidoval dva registry dolů do jednoho, přidání repliky do východní USA, Kanada – střed a západní Evropa. Společnost Contoso by platila čtyřikrát premium měsíčně bez další konfigurace nebo správy. Každý region nyní stahuje své obrázky místně, zlepšuje výkon, spolehlivost bez poplatků za odchozí sítě ze západních USA do Kanady a východních USA.

## <a name="troubleshoot-push-operations-with-geo-replicated-registries"></a>Poradce při potížích s nabízenými operacemi s geograficky replikované registry
 
Klient Dockeru, který odesílá bitovou kopii do geograficky replikovaného registru, nemusí odesílat všechny vrstvy bitové kopie a jeho manifest do jedné replikované oblasti. K tomu může dojít, protože Azure Traffic Manager směruje požadavky registru do replikovaného registru nejbližší sítě. Pokud registr obsahuje dvě *blízké* oblasti replikace, vrstvy bitové kopie a manifestu mohou být distribuovány do dvou lokalit a operace nabízení se nezdaří při ověření manifestu. K tomuto problému dochází z důvodu způsobu, jakým je vyřešen název DNS registru na některých hostitelích Linuxu. K tomuto problému nedochází v systému Windows, který poskytuje mezipaměť DNS na straně klienta.
 
Pokud k tomuto problému dojde, jedním z řešení je `dnsmasq` použít mezipaměti DNS na straně klienta, například na hostiteli Linuxu. To pomáhá zajistit, že název registru je vyřešen konzistentně. Pokud používáte virtuální počítač s Linuxem v Azure k nabízení do registru, přečtěte si možnosti v [možnostech překladu názvů DNS pro virtuální počítače s Linuxem v Azure](../virtual-machines/linux/azure-dns.md).

Chcete-li optimalizovat rozlišení DNS na nejbližší repliku při odesílání ibi, nakonfigurujte geograficky replikovaný registr ve stejných oblastech Azure jako zdroj operací nabízených služeb nebo nejbližší oblasti při práci mimo Azure.

## <a name="next-steps"></a>Další kroky

Podívejte se na třídílnou sérii kurzů [Geografická replikace v registru kontejnerů Azure](container-registry-tutorial-prepare-registry.md). Projděte si vytvoření geograficky replikovaného registru, sestavení kontejneru `docker push` a jeho nasazení pomocí jediného příkazu do více místních instancí webových aplikací pro kontejnery.

> [!div class="nextstepaction"]
> [Geografická replikace v registru kontejnerů Azure](container-registry-tutorial-prepare-registry.md)
