---
title: Upgrade klasického služby Azure Container Registry
description: Upgrade nespravovaného klasického registru kontejnerů vám povede k využití rozšířené sady funkcí základních, standardních a Premium spravovaných registrů.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 03/26/2019
ms.author: danlep
ms.openlocfilehash: 05c227e7de078c6bb371049f16e191598b9ca4e5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68310374"
---
# <a name="upgrade-a-classic-container-registry"></a>Upgrade klasického registru kontejnerů

Azure Container Registry (ACR) je k dispozici na několika úrovních služby, [označovaných jako SKU](container-registry-skus.md). Počáteční verze ACR nabídla jednu SKU, Classic, která nemá několik funkcí, které jsou součástí základních SKU Basic, Standard a Premium (souhrnně označovaných jako *spravované* Registry).

Klasická SKU se již nepoužívá a po uplynutí dubna 2019 nebude k dispozici. Tento článek podrobně popisuje, jak migrovat nespravovaný klasický registr do jedné ze spravovaných SKU, abyste mohli využít jeho vylepšenou sadu funkcí.

## <a name="why-upgrade"></a>Proč upgradovat?

Skladová položka registru Classic jezastaralá a nebude k dispozici po uplynutí **dubna 2019**. Všechny existující Registry Classic by měly být upgradovány před vydáním dubna 2019. Funkce správy portálu pro klasické Registry budou postupně vycházet. Vytváření nových klasických registrů bude zakázáno po dubna 2019.

Kvůli plánovanému vyřazení a omezené možnosti klasických nespravovaných registrů by se všechny klasické Registry měly upgradovat na spravované Registry (Basic, Standard nebo Premium). Tyto skladové položky vyšší úrovně jsou podrobněji integrovány do možností Azure Integration Registry. Další informace o cenách a možnostech různých úrovní služeb najdete v tématu [Container Registry SKU](container-registry-skus.md).

Klasický registr závisí na účtu úložiště, který Azure automaticky zřídí ve vašem předplatném Azure při vytváření registru. Naproti tomu SKU Basic, Standard a Premium využívají [Pokročilé funkce úložiště](container-registry-storage.md) Azure, které transparentně zpracovávají úložiště vašich imagí. Samostatný účet úložiště se ve vašem vlastním předplatném nevytvoří.

Spravované úložiště registru přináší následující výhody:

* Image kontejneru jsou [v klidovém stavu šifrované](container-registry-storage.md#encryption-at-rest).
* Image se ukládají pomocí [geograficky redundantního úložiště](container-registry-storage.md#geo-redundant-storage), které zajišťuje Zálohování imagí pomocí replikace ve více oblastech (jenom SKU úrovně Premium).
* Možnost volně se [pohybovat mezi SKU](container-registry-skus.md#changing-skus)a povolením vyšší propustnosti při výběru SKU vyšší úrovně. U každé SKU může ACR splňovat požadavky vaší propustnosti podle toho, jak se vaše potřeby zvyšují.
* Jednotný model zabezpečení pro registry a jeho úložiště poskytuje zjednodušenou správu přístupových práv. Oprávnění spravujete jenom pro registr kontejnerů, aniž byste museli spravovat taky oprávnění pro samostatný účet úložiště.

Další podrobnosti o úložišti imagí v ACR najdete v tématu [úložiště imagí kontejneru v Azure Container Registry](container-registry-storage.md).

## <a name="migration-considerations"></a>Požadavky na migraci

Když upgradujete klasický registr do spravovaného registru, musí Azure zkopírovat všechny existující image kontejnerů z účtu úložiště ACR vytvořeného v předplatném do účtu úložiště spravovaného službou Azure. V závislosti na velikosti registru může tento proces trvat několik minut až několik hodin. Pro účely odhadu je očekávána doba migrace přibližně 0,5 GiB za minutu.

Během procesu `docker push` převodu jsou operace během posledních 10% migrace zakázané. `docker pull`nadále funguje normálně.

Během procesu převodu neodstraňujte ani neměňte obsah účtu úložiště, který je v rámci svého klasického registru zálohovaný. V důsledku toho může dojít k poškození imagí kontejneru.

Až se migrace dokončí, účet úložiště ve vašem předplatném, který původně zálohovaný váš klasický registr už nepoužívá, Azure Container Registry. Až ověříte, že migrace proběhla úspěšně, zvažte odstranění účtu úložiště, abyste minimalizovali náklady.

>[!IMPORTANT]
> Upgrade z klasického na jednu ze spravovaných SKU je **jednosměrný proces**. Po převedení klasického registru na Basic, Standard nebo Premium se nemůžete vrátit na klasický. Můžete však volně přesouvat mezi spravovanými SKU a dostatečnou kapacitou pro váš registr.

## <a name="how-to-upgrade"></a>Postup upgradu

Nespravovaný klasický registr můžete upgradovat na jednu ze spravovaných SKU několika způsoby. V následujících částech popisujeme proces používání rozhraní příkazového [řádku Azure][azure-cli] a [Azure Portal][azure-portal].

## <a name="upgrade-in-azure-cli"></a>Upgrade v Azure CLI

Pokud chcete upgradovat klasický registr v rozhraní příkazového řádku Azure CLI, spusťte příkaz [AZ ACR Update][az-acr-update] a zadejte novou skladovou položku pro registr. V následujícím příkladu se klasický registr s názvem *myclassicregistry* upgraduje na SKU úrovně Premium:

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

Po dokončení migrace by se měl zobrazit výstup podobný následujícímu. Všimněte si, `sku` že je "Premium" `storageAccount` a je `null`, což znamená, že Azure teď spravuje úložiště imagí pro tento registr.

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

Pokud zadáte spravovanou SKU registru, jehož maximální kapacita je menší než velikost klasického registru, zobrazí se chybová zpráva podobná následující.

`Cannot update the registry SKU due to reason: Registry size 12936251113 bytes exceeds the quota value 10737418240 bytes for SKU Basic. The suggested SKU is Standard.`

Pokud obdržíte podobnou chybu, spusťte znovu příkaz [AZ ACR Update][az-acr-update] a určete navrhovanou SKU, která je na další nejvyšší úrovni SKU, která může vyhovovat vašim obrázkům.

## <a name="upgrade-in-azure-portal"></a>Upgrade v Azure Portal

Když upgradujete klasický registr pomocí Azure Portal, Azure automaticky vybere buď SKU Standard nebo Premium, podle toho, kterou SKU můžou vaše image přizpůsobit. Pokud například váš registr obsahuje méně než 100 GiB na obrázcích, Azure automaticky vybere a převede klasický registr na Standard (100 GiB max).

Pokud chcete upgradovat klasický registr pomocí Azure Portal, přejděte do části **Přehled** registru kontejnerů a vyberte **upgradovat na Managed Registry**.

![Klasické tlačítko upgradu registru v uživatelském rozhraní Azure Portal][update-classic-01-upgrade]

Kliknutím na **OK** potvrďte, že chcete upgradovat na spravovaný registr.

Během migrace portál informuje o tom, že **stav zřizování** registru se *aktualizuje*. Jak už bylo zmíněno dříve, `docker push` operace jsou během posledních 10% migrace zakázané. Během probíhající migrace neodstraňujte ani neaktualizujete účet úložiště používaný v klasickém registru. v důsledku toho může dojít k poškození bitové kopie.

![Průběh upgradu v registru Classic v uživatelském rozhraní Azure Portal][update-classic-03-updating]

Po dokončení migrace se **stav zřizování** indikuje jako *úspěšný*a můžete obnovit normální operace s registrem.

![Stav dokončení upgradu na klasický registr v uživatelském rozhraní Azure Portal][update-classic-04-updated]

## <a name="next-steps"></a>Další postup

Po upgradu klasického registru na spravovaný registr Azure už nepoužívá účet úložiště, který byl původně zálohovaný v klasickém registru. Pokud chcete snížit náklady, zvažte odstranění účtu úložiště nebo kontejneru objektů BLOB v rámci účtu, který obsahuje vaše staré image kontejneru.

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade/update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade/update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade/update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade/update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com