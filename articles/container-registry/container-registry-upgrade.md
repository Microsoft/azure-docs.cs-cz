---
title: Upgradovat registr kontejnerů Classic Azure
description: Využijte výhod rozbalených sadě funkcí Basic, Standard a Premium spravované registry kontejnerů díky upgradu nespravované Classic registru kontejneru.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/26/2019
ms.author: danlep
ms.openlocfilehash: a5099feee34eb5497b68987485412e29ad5d5365
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60480307"
---
# <a name="upgrade-a-classic-container-registry"></a>Upgrade registru Classic kontejneru

Azure Container Registry (ACR) je k dispozici na několika úrovních služby [označované jako skladové položky](container-registry-skus.md). Počáteční verze služby ACR nabízí jednotné SKU Classic, který chybí několik funkcí, které přináší Basic, Standard a SKU úrovně Premium (dále jen souhrnně označované jako *spravované* Registry).

Klasické SKU je zastaralé a budou k dispozici od dubna 2019. Tento článek podrobně popisuje, jak migrovat nespravované registru Classic k jednomu z spravovanými skladovými položkami tak, aby můžete využít své rozšířené funkce set.

## <a name="why-upgrade"></a>Proč upgradovat?

Registru Classic se skladová položka **zastaralé**a nebude k dispozici po **. dubna 2019**. Všechny existující registry Classic by měl upgradovat před dubnem 2019. Funkce pro správu portálu Classic registrů se postupně. Vytvoření nové registry Classic se deaktivuje po dubna 2019.

Z důvodu plánovaného vyřazení a omezené možnosti registry Classic nespravované je potřeba upgradovat všechny registry Classic na spravované Registry (Basic, Standard nebo Premium). Tyto vyšší úrovně SKU hlouběji integrovat registru do funkce Azure. Další informace o cenách a funkcích různých úrovních služeb najdete v tématu [skladové položky registru kontejneru](container-registry-skus.md).

Registru Classic závisí na účet úložiště, Azure automaticky zřídí ve vašem předplatném Azure při vytváření registru. Naopak Basic, Standard a Premium SKU využít výhod Azure [pokročilé funkce úložiště](container-registry-storage.md) transparentně zpracovává úložiště imagí za vás. Samostatný účet úložiště není vytvořena ve svém vlastním předplatném.

Spravovaný registr úložiště poskytuje následující výhody:

* Imagí kontejneru [v klidovém stavu zašifrovaná](container-registry-storage.md#encryption-at-rest).
* Image se ukládají pomocí [geograficky redundantní úložiště](container-registry-storage.md#geo-redundant-storage), zajišťuje zálohování imagí pomocí replikace ve více oblastech (jenom SKU úrovně Premium).
* Možnost volně [přechodu mezi skladovými položkami](container-registry-skus.md#changing-skus), povolení vyšší propustnost při výběru SKU vyšší úrovně. S každou SKU ACR vyhoví vašim požadavkům na propustnost rostoucím potřebám.
* Zabezpečení jednotný model pro registru a jeho úložiště poskytuje zjednodušené rights management. Spravovat oprávnění jenom pro službu container registry, aniž byste museli také spravovat oprávnění pro samostatný účet úložiště.

Další podrobnosti o úložiště obrázků v ACR, naleznete v tématu [úložiště image kontejneru ve službě Azure Container Registry](container-registry-storage.md).

## <a name="migration-considerations"></a>Aspekty migrace

Když provedete upgrade registru Classic na spravovaný registr, Azure musíte zkopírovat všechny existující Image kontejneru z účtu úložiště ACR vytvořené v rámci vašeho předplatného na účet úložiště, který spravuje Azure. V závislosti na velikosti vašeho registru tento proces může trvat pár minut i několik hodin. Pro účely odhad očekávat, že čas migrace přibližně 0,5 GB za minutu.

Během procesu převodu `docker push` operace jsou zakázané během posledních 10 % migrace. `docker pull` bude dál normálně fungovat.

Nemazat ani neupravovat obsah účtu úložiště zálohování vašeho registru Classic během procesu převodu. To může vést k poškození imagí kontejnerů.

Po dokončení migrace účtu úložiště v rámci vašeho předplatného, který původně zálohována registru Classic se už používá služba Azure Container Registry. Po ověření, že migrace byla úspěšná, vezměte v úvahu odstraníte účet úložiště, aby vám pomohla minimalizovat náklady.

>[!IMPORTANT]
> Upgrade z modelu Classic na jednu z spravovanými skladovými položkami je **jednosměrný proces**. Po převodu Classic registru na Basic, Standard nebo Premium, nejde vrátit zpět a klasickým modelem. Můžete můžou, ale volně přesouvat mezi spravovanými skladovými položkami s dostatečnou kapacitu pro váš registr.

## <a name="how-to-upgrade"></a>Postup upgradu

Nespravované registru Classic můžete upgradovat na jednu z spravovanými skladovými položkami několika způsoby. V následujících částech popisujeme použití [rozhraní příkazového řádku Azure] [ azure-cli] a [webu Azure portal][azure-portal].

## <a name="upgrade-in-azure-cli"></a>Upgrade v Azure CLI

Chcete-li upgrade registru Classic v Azure CLI, spusťte [az acr update] [ az-acr-update] příkaz a zadejte novou skladovou Položku registru. V následujícím příkladu, s názvem registru Classic *myclassicregistry* se upgraduje na SKU úrovně Premium:

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

Po dokončení migrace byste měli vidět výstup podobný následujícímu. Všimněte si, že `sku` je "Premium" a `storageAccount` je `null`, která udává, že Azure teď spravuje úložiště obrázků pro tento registr.

```JSON
{
  "adminUserEnabled": false,
  "creationDate": "2017-12-12T21:23:29.300547+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "name": "Premium",
    "tier": "Premium"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Pokud zadáte spravovaného registru skladovou Položku, jejíž maximální kapacita je menší než velikost svého registru Classic, zobrazí se chybová zpráva podobná této.

`Cannot update the registry SKU due to reason: Registry size 12936251113 bytes exceeds the quota value 10737418240 bytes for SKU Basic. The suggested SKU is Standard.`

Pokud se zobrazí podobné chyby, spusťte [az acr update] [ az-acr-update] příkaz znovu a zadat navrhované SKU, která je druhou nejvyšší úrovně jednotky SKU, zvládne vaše Image.

## <a name="upgrade-in-azure-portal"></a>Upgrade na webu Azure portal

Pokud provedete upgrade registru Classic pomocí webu Azure portal, Azure vybere standardní nebo SKU úrovně Premium, podle toho, která zvládne skladová položka vaší Image. Například pokud váš registr obsahuje méně než 100 GB na obrázcích, Azure automaticky vybere a převede na Standard registru Classic (maximálně 100 GB).

Upgrade registru Classic pomocí webu Azure portal, přejděte do registru kontejneru **přehled** a vyberte **Upgrade na spravovaný registr**.

![Upgrade registru Classic tlačítko na webu Azure Portal uživatelského rozhraní][update-classic-01-upgrade]

Vyberte **OK** potvrďte, že chcete upgradovat na spravovaný registr.

Během migrace, portál bude informovat, která v registru **Stav zřizování** je *aktualizace*. Jak už bylo zmíněno dříve, `docker push` operace jsou zakázané během posledních 10 % migrace. Není delete či update účet úložiště používané registru Classic i když migrace probíhá – to může způsobit poškození bitové kopie.

![Klasické registr průběh upgradu v na webu Azure portal uživatelského rozhraní][update-classic-03-updating]

Po dokončení migrace **Stav zřizování** označuje *Succeeded*, a mohli obnovit normální provoz k vašemu registru.

![Upgrade registru Classic stavu dokončení na webu Azure Portal uživatelského rozhraní][update-classic-04-updated]

## <a name="next-steps"></a>Další postup

Po upgradu registru Classic na spravovaný registr Azure už používá účet úložiště, který původně zálohována registru Classic. Pokud chcete snížit náklady, zvažte odstranění účtu úložiště nebo kontejneru objektů Blob v rámci účtu, který obsahuje původní imagí kontejnerů.

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade/update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade/update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade/update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade/update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com