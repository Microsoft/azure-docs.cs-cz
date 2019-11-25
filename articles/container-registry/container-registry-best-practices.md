---
title: Osvědčené postupy registru
description: Zjistěte, jak pomocí těchto osvědčených postupů efektivně používat službu Azure Container Registry.
ms.topic: article
ms.date: 09/27/2018
ms.openlocfilehash: 4b0512674358d4db2e29596408ebbf44af4ea2a9
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455329"
---
# <a name="best-practices-for-azure-container-registry"></a>Osvědčené postupy pro službu Azure Container Registry

Když se budete řídit těmito osvědčenými postupy, můžete maximalizovat výkon a nákladově efektivní používání svého privátního registru Dockeru v Azure.

## <a name="network-close-deployment"></a>Nasazení blízko sítě

Vytvořte registr kontejnerů ve stejné oblasti Azure, do které nasazujete kontejnery. Umístění registru do oblasti blízko sítě hostitelů kontejnerů může pomoct snížit latenci i náklady.

Nasazení blízko sítě je jedním z hlavních důvodů pro použití privátního registru kontejnerů. Image Dockeru obsahují efektivní [koncept vrstvení](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/), který umožňuje přírůstkové nasazování. Nové uzly však musí přetahovat všechny vrstvy požadované pro danou image. Toto počáteční přetáhnutí příkazem `docker pull` se rychle může rozrůst až na několik gigabajtů. Umístění privátního registru blízko nasazení minimalizuje latenci sítě.
Všechny veřejné cloudy včetně Azure navíc zahrnují poplatky za odchozí přenosy v síti. Přetahování imagí z jednoho datacentra do jiného zvyšuje kromě latence i poplatky za odchozí přenosy v síti.

## <a name="geo-replicate-multi-region-deployments"></a>Geografická replikace nasazení ve více oblastech

Pokud nasazujete kontejnery do více oblastí, využijte funkci [geografické replikace](container-registry-geo-replication.md) ve službě Azure Container Registry. Ať už obsluhujete globální zákazníky z místních datacenter nebo je váš vývojový tým rozmístěný v různých oblastech, díky geografické replikaci registru můžete zjednodušit správu registru a minimalizovat latenci. Geografická replikace je dostupná jen u registrů [Premium](container-registry-skus.md).

Informace o použití geografické replikace najdete v třídílném kurzu [Geografická replikace ve službě Azure Container Registry](container-registry-tutorial-prepare-registry.md).

## <a name="repository-namespaces"></a>Obory názvů úložiště

S využitím oborů názvů úložiště můžete umožnit sdílení jednoho registru napříč několika skupinami v rámci vaší organizace. Registry se můžou sdílet napříč nasazeními a týmy. Azure Container Registry podporuje vnořené obory názvů a díky tomu umožňuje izolaci skupin.

Představte si například následující značky image kontejneru. Image používané v rámci celého podniku, například `aspnetcore`, jsou umístěné v kořenovém oboru názvů, zatímco jednotlivé image kontejnerů vlastněné produkčními a marketingovými skupinami používají svůj vlastní obor názvů.

```
contoso.azurecr.io/aspnetcore:2.0
contoso.azurecr.io/products/widget/web:1
contoso.azurecr.io/products/bettermousetrap/refundapi:12.3
contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42
```

## <a name="dedicated-resource-group"></a>Vyhrazená skupina prostředků

Because container registries are resources that are used across multiple container hosts, a registry should reside in its own resource group.

I když můžete experimentovat s konkrétním typem hostitele, jako je služba Azure Container Instances, pravděpodobně budete chtít instanci kontejneru odstranit, jakmile budete hotovi. Můžete však také chtít zachovat kolekci imagí, které jste nasdíleli do služby Azure Container Registry. Umístěním registru do vlastní skupiny prostředků minimalizujete riziko nechtěného odstranění kolekce imagí v registru při odstraňování skupiny prostředků instance kontejneru.

## <a name="authentication"></a>Ověření

Při ověřování ve službě Azure Container Registry existují dva primární scénáře: jednotlivé ověření a ověření služby (neboli bezobslužné ověření). Následující tabulka obsahuje stručný přehled těchto scénářů a doporučenou metodu ověřování pro každý z nich.

| Typ | Příklad scénáře | Doporučená metoda |
|---|---|---|
| Jednotlivá identita | Vývojář přetahující image do svého vývojového počítače nebo sdílející image ze svého vývojového počítače. | [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) |
| Bezobslužné ověření/identita služby | Kanály sestavení a nasazení bez přímého zapojení uživatele. | [Instanční objekt](container-registry-authentication.md#service-principal) |

Podrobné informace o ověřování ve službě Azure Container Registry najdete v tématu [Ověřování ve službě Azure Container Registry](container-registry-authentication.md).

## <a name="manage-registry-size"></a>Správa velikosti registru

The storage constraints of each [container registry SKU][container-registry-skus] are intended to align with a typical scenario: **Basic** for getting started, **Standard** for the majority of production applications, and **Premium** for hyper-scale performance and [geo-replication][container-registry-geo-replication]. Po celou dobu životnosti vašeho registru byste měli spravovat jeho velikost pravidelným odstraňováním nevyužívaného obsahu.

Use the Azure CLI command [az acr show-usage][az-acr-show-usage] to display the current size of your registry:

```console
$ az acr show-usage --resource-group myResourceGroup --name myregistry --output table
NAME      LIMIT         CURRENT VALUE    UNIT
--------  ------------  ---------------  ------
Size      536870912000  185444288        Bytes
Webhooks  100                            Count
```

You can also find the current storage used in the **Overview** of your registry in the Azure portal:

![Informace o využití registru na webu Azure Portal][registry-overview-quotas]

### <a name="delete-image-data"></a>Delete image data

Azure Container Registry supports several methods for deleting image data from your container registry. You can delete images by tag or manifest digest, or delete a whole repository.

For details on deleting image data from your registry, including untagged (sometimes called "dangling" or "orphaned") images, see [Delete container images in Azure Container Registry](container-registry-delete.md).

## <a name="next-steps"></a>Další kroky

Služba Azure Container Registry je dostupná v několika úrovních, označovaných jako skladové položky, každá z nichž poskytuje různé možnosti. Podrobnosti o dostupných skladových položkách najdete v tématu [Skladové položky služby Azure Container Registry](container-registry-skus.md).

<!-- IMAGES -->
[delete-repository-portal]: ./media/container-registry-best-practices/delete-repository-portal.png
[registry-overview-quotas]: ./media/container-registry-best-practices/registry-overview-quotas.png

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-show-usage]: /cli/azure/acr#az-acr-show-usage
[azure-cli]: /cli/azure
[azure-portal]: https://portal.azure.com
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-skus]: container-registry-skus.md
