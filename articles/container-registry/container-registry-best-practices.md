---
title: Osvědčené postupy ve službě Azure Container Registry
description: Zjistěte, jak pomocí těchto osvědčených postupů efektivně používat službu Azure Container Registry.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: quickstart
ms.date: 04/10/2018
ms.author: marsma
ms.openlocfilehash: 1272a4b547a4ba650678ce083a44bde3a5a3e583
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
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

Vzhledem k tomu, že registry kontejnerů jsou prostředky, které se používají napříč několika hostiteli kontejnerů, měl by se registr nacházet ve své vlastní skupině prostředků.

I když můžete experimentovat s konkrétním typem hostitele, jako je služba Azure Container Instances, pravděpodobně budete chtít instanci kontejneru odstranit, jakmile budete hotovi. Můžete však také chtít zachovat kolekci imagí, které jste nasdíleli do služby Azure Container Registry. Umístěním registru do vlastní skupiny prostředků minimalizujete riziko nechtěného odstranění kolekce imagí v registru při odstraňování skupiny prostředků instance kontejneru.

## <a name="authentication"></a>Authentication

Při ověřování ve službě Azure Container Registry existují dva primární scénáře: jednotlivé ověření a ověření služby (neboli bezobslužné ověření). Následující tabulka obsahuje stručný přehled těchto scénářů a doporučenou metodu ověřování pro každý z nich.

| Typ | Příklad scénáře | Doporučená metoda |
|---|---|---|
| Jednotlivá identita | Vývojář přetahující image do svého vývojového počítače nebo sdílející image ze svého vývojového počítače. | [az acr login](/cli/azure/acr?view=azure-cli-latest#az_acr_login) |
| Bezobslužné ověření/identita služby | Kanály sestavení a nasazení bez přímého zapojení uživatele. | [Instanční objekt](container-registry-authentication.md#service-principal) |

Podrobné informace o ověřování ve službě Azure Container Registry najdete v tématu [Ověřování ve službě Azure Container Registry](container-registry-authentication.md).

## <a name="manage-registry-size"></a>Správa velikosti registru

Omezení úložiště pro jednotlivé [skladové položky registru kontejneru][container-registry-skus] by měla odpovídat obvyklému scénáři: **Basic** pro začátek, **Standard** pro většinu produkčních aplikací a **Premium** pro zajištění vysoce škálovatelného výkonu a [geografické replikace][container-registry-geo-replication]. Po celou dobu životnosti vašeho registru byste měli spravovat jeho velikost pravidelným odstraňováním nevyužívaného obsahu.

Aktuální využití registru najdete na stránce **Přehled** daného registru kontejnerů na webu Azure Portal:

![Informace o využití registru na webu Azure Portal][registry-overview-quotas]

Velikost svého registru můžete spravovat pomocí [Azure CLI][azure-cli][ nebo webu Azure Portal][azure-portal]. Odstraňování úložišť a imagí podporují pouze spravované skladové položky (Basic, Standard, Premium) – v registru úrovně Classic nemůžete odstraňovat úložiště, image ani značky.

### <a name="delete-in-azure-cli"></a>Odstranění v Azure CLI

Pomocí příkazu [az acr repository delete][az-acr-repository-delete] můžete odstranit úložiště nebo obsah v úložišti.

Pokud chcete odstranit úložiště, včetně všech značek a dat na úrovni image v rámci tohoto úložiště, při spouštění příkazu [az acr repository delete][az-acr-repository-delete] zadejte pouze název úložiště. V následujícím příkladu odstraníme úložiště *myapplication* i všechny značky a data na úrovni image v rámci tohoto úložiště:

```azurecli
az acr repository delete --name myregistry --repository myapplication
```

Data imagí můžete z úložiště odstranit také pomocí argumentů `--tag` a `--manifest`. Podrobnosti o těchto argumentech najdete v [referenčních informacích k příkazu az acr repository delete][az-acr-repository-delete].

### <a name="delete-in-azure-portal"></a>Odstranění na webu Azure Portal

Pokud chcete z registru odstranit úložiště na webu Azure Portal, nejprve přejděte do vašeho registru kontejneru. Pak v části **SLUŽBY** vyberte **Úložiště** a klikněte pravým tlačítkem na úložiště, které chcete odstranit. Vyberte **Odstranit** a odstraňte úložiště i image Dockeru, které obsahuje.

![Odstranění úložiště na webu Azure Portal][delete-repository-portal]

Podobným způsobem můžete z úložiště odstranit také značky. Přejděte do úložiště, v části **ZNAČKY** klikněte pravým tlačítkem na značku, kterou chcete odstranit, a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

Služba Azure Container Registry je dostupná v několika úrovních, označovaných jako skladové položky, každá z nichž poskytuje různé možnosti. Podrobnosti o dostupných skladových položkách najdete v tématu [Skladové položky služby Azure Container Registry](container-registry-skus.md).

<!-- IMAGES -->
[delete-repository-portal]: ./media/container-registry-best-practices/delete-repository-portal.png
[registry-overview-quotas]: ./media/container-registry-best-practices/registry-overview-quotas.png

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
[azure-cli]: /cli/azure
[azure-portal]: https://portal.azure.com
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-skus]: container-registry-skus.md
