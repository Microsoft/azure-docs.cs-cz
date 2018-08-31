---
title: Upgradovat registr kontejnerů Classic Azure
description: Využijte výhod rozbalených sadě funkcí Basic, Standard a Premium spravované registry kontejnerů díky upgradu nespravované Classic registru kontejneru.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 08/28/2018
ms.author: marsma
ms.openlocfilehash: 7a377c607639f5c044e689b11380e9778f9c72c8
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189017"
---
# <a name="upgrade-a-classic-container-registry"></a>Upgrade registru Classic kontejneru

Azure Container Registry (ACR) je k dispozici na několika úrovních služby [označované jako skladové položky](container-registry-skus.md). Počáteční verze služby ACR nabízí jednotné SKU Classic, který chybí několik funkcí, které přináší Basic, Standard a SKU úrovně Premium (dále jen souhrnně označované jako *spravované* Registry).

Klasické SKU je zastaralé a budou k dispozici od března 2019. Tento článek podrobně popisuje, jak migrovat nespravované registru Classic k jednomu z spravovanými skladovými položkami tak, aby můžete využít své rozšířené funkce set.

## <a name="why-upgrade"></a>Proč upgradovat?

Registru Classic se skladová položka **zastaralé**a nebude k dispozici z **. března 2019**. Všechny existující registry Classic by měl být upgrade před březnem 2019.

Z důvodu plánovaného vyřazení a omezené možnosti registry Classic nespravované všechny registry Classic se neupgradovali na Basic, Standard nebo Premium spravované Registry. Tyto vyšší úrovně SKU hlouběji integrovat registru do funkce Azure.

Spravované registry poskytují:

* Integrace s Azure Active Directory pro [jednotlivých přihlášení](container-registry-authentication.md#individual-login-with-azure-ad)
* Podpora odstranění Image a značky
* [Geografická replikace](container-registry-geo-replication.md)
* [Webhooky](container-registry-webhook.md)

Registru Classic závisí na účet úložiště, Azure automaticky zřídí ve vašem předplatném Azure při vytváření registru. Naopak Basic, Standard a Premium SKU využít výhod Azure [pokročilé funkce úložiště](container-registry-storage.md) transparentně zpracovává úložiště imagí za vás. Samostatný účet úložiště není vytvořena ve svém vlastním předplatném.

Spravovaný registr úložiště poskytuje následující výhody:

* Imagí kontejneru [v klidovém stavu zašifrovaná](container-registry-storage.md#encryption-at-rest).
* Image se ukládají pomocí [geograficky redundantní úložiště](container-registry-storage.md#geo-redundant-storage), zajišťuje zálohování imagí pomocí replikace ve více oblastech.
* Možnost volně [přechodu mezi skladovými položkami](container-registry-skus.md#changing-skus), povolení vyšší propustnost při výběru SKU vyšší úrovně. S každou SKU ACR vyhoví vašim požadavkům na propustnost rostoucím potřebám.
* Zabezpečení jednotný model pro registru a jeho úložiště poskytuje zjednodušené rights management. Spravovat oprávnění jenom pro službu container registry, aniž byste museli také spravovat oprávnění pro samostatný účet úložiště.

Další podrobnosti o úložiště obrázků v ACR, naleznete v tématu [úložiště image kontejneru ve službě Azure Container Registry](container-registry-storage.md).

## <a name="migration-considerations"></a>Aspekty migrace

Při změně registru Classic na spravovaný registr Azure musíte zkopírovat všechny existující Image kontejneru z účtu úložiště ACR vytvořené v rámci vašeho předplatného na účet úložiště, který spravuje Azure. V závislosti na velikosti vašeho registru tento proces může trvat pár minut i několik hodin.

Během procesu převodu všechny `docker push` blokovány jsou operace, zatímco `docker pull` nadále funkční.

Nemazat ani neupravovat obsah účtu úložiště zálohování vašeho registru Classic během procesu převodu. To může vést k poškození imagí kontejnerů.

Po dokončení migrace účtu úložiště v rámci vašeho předplatného, který původně zálohována registru Classic nebude používat pomocí služby ACR. Po ověření, že migrace byla úspěšná, vezměte v úvahu odstraníte účet úložiště, aby vám pomohla minimalizovat náklady.

>[!IMPORTANT]
> Upgrade z modelu Classic na jednu z spravovanými skladovými položkami je **jednosměrný proces**. Po převodu Classic registru na Basic, Standard nebo Premium, nejde vrátit zpět a klasickým modelem. Můžete můžou, ale volně přesouvat mezi spravovanými skladovými položkami s dostatečnou kapacitu pro váš registr.

## <a name="how-to-upgrade"></a>Postup upgradu

Nespravované registru Classic můžete upgradovat na jednu z spravovanými skladovými položkami několika způsoby. V následujících částech popisujeme použití [rozhraní příkazového řádku Azure] [ azure-cli] a [webu Azure portal][azure-portal].

## <a name="upgrade-in-azure-cli"></a>Upgrade v Azure CLI

Chcete-li upgrade registru Classic v Azure CLI, spusťte [az acr update] [ az-acr-update] příkaz a zadejte novou skladovou Položku registru. V následujícím příkladu, s názvem registru Classic *myclassicregistry* se upgraduje na SKU úrovně Premium:

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

Po dokončení migrace byste měli vidět výstup podobný následujícímu. Všimněte si, že `sku` je "Premium" a `storageAccount` je "null" označující, že Azure teď spravuje úložiště obrázků pro tento registr.

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

Pokud provedete upgrade registru Classic pomocí webu Azure portal, Azure automaticky vybere nejnižší úroveň SKU, která zvládne vaše Image. Například pokud váš registr obsahuje 12 GB na obrázcích, Azure automaticky vybere a převede na Standard registru Classic (maximálně 100 GB).

Upgrade registru Classic pomocí webu Azure portal, přejděte do registru kontejneru **přehled** a vyberte **Upgrade na spravovaný registr**.

![Upgrade registru Classic tlačítko na webu Azure Portal uživatelského rozhraní][update-classic-01-upgrade]

Vyberte **OK** potvrďte, že chcete upgradovat na spravovaný registr.

![Upgrade registru Classic potvrzení na webu Azure Portal uživatelského rozhraní][update-classic-02-confirm]

Během migrace, portál bude informovat, která v registru **Stav zřizování** je *aktualizace*. Jak už bylo zmíněno dříve, `docker push` operace jsou zakázané během migrace a nesmí odstranit nebo aktualizovat účet úložiště používané registru Classic i když migrace probíhá – to může vést k poškození bitové kopie.

![Klasické registr průběh upgradu v na webu Azure portal uživatelského rozhraní][update-classic-03-updating]

Po dokončení migrace **Stav zřizování** označuje *Succeeded*, a můžete je znovu `docker push` do svého registru.

![Upgrade registru Classic stavu dokončení na webu Azure Portal uživatelského rozhraní][update-classic-04-updated]

## <a name="next-steps"></a>Další postup

Jakmile jste upgradovali Classic registru na Basic, Standard nebo Premium, Azure už používá účet úložiště, který původně zálohována registru Classic. Pokud chcete snížit náklady, zvažte odstranění účtu úložiště nebo kontejneru objektů Blob v rámci účtu, který obsahuje původní imagí kontejnerů.

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade\update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade\update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade\update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade\update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com