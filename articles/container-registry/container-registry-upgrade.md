---
title: Upgrade kontejneru registru Classic Azure
description: Využít výhod rozšířená sada funkcí Basic, Standard a Premium spravuje upgradu vaší nespravované kontejneru registru Classic registrech kontejneru.
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 03/15/2018
ms.author: marsma
ms.openlocfilehash: c5a61941bab2aa49cd8205e0a07dd2b5f7378ce9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="upgrade-a-classic-container-registry"></a>Upgrade kontejneru registru Classic

Je k dispozici v několika úrovně služeb Azure kontejneru registru (ACR) [známé jako SKU](container-registry-skus.md). Počáteční verze ACR nabízí jeden SKU, Classic, který nemá několik funkcí vyplývajících Basic, Standard a Premium SKU (souhrnně známé jako *spravované* registrech). Tento článek podrobně popisují migraci nespravované registr Classic na jednu z spravované SKU tak, aby můžete využít výhod sady jejich rozšířené funkce.

## <a name="why-upgrade"></a>Proč upgradovat?

Kvůli omezené možnosti registrech Classic nespravované doporučujeme, abyste že všechny registrech Classic se neupgradovali na Basic, Standard nebo Premium spravované registrech. Tyto vyšší úrovně SKU hlubšímu integrovat registru do služby Azure.

Spravované registrech poskytují:

* Integrace služby Azure Active Directory [jednotlivých přihlášení](container-registry-authentication.md#individual-login-with-azure-ad)
* Podpora odstranění bitové kopie a značka
* [Geografická replikace](container-registry-geo-replication.md)
* [Webhooks](container-registry-webhook.md)

Většina všech Classic registru závisí na účet úložiště, Azure automaticky zřídí ve vašem předplatném Azure při vytváření registru. Naopak Basic, Standard a Premium SKU využít výhod Azure [pokročilé funkce úložiště](container-registry-storage.md) pomocí transparentního zpracování úložiště obrázků za vás. Účet samostatného úložiště není vytvořená ve svého vlastního předplatného.

Spravované registru úložiště poskytuje následující výhody:

* Kontejner Image jsou [zašifrovaná přinejmenším](container-registry-storage.md#encryption-at-rest).
* Image jsou uložené pomocí [geograficky redundantní úložiště](container-registry-storage.md#geo-redundant-storage), zajišťuje zálohování obrázků s více oblast replikace.
* Schopnost volně [přesouvat mezi SKU](container-registry-skus.md#changing-skus), povolení vyšší propustnost, když zvolíte vyšší úrovně SKU. S každou SKU můžete ACR splňují požadavky propustnost zvýšit vašim potřebám.
* Model jednotná zabezpečení pro registru a jeho úložiště poskytuje zjednodušenou rights management. Správa oprávnění pouze pro kontejner registru, aniž by bylo nutné také spravovat oprávnění pro účet samostatného úložiště.

Další informace o úložiště bitové kopie v ACR v [kontejneru úložiště bitové kopie v registru kontejner Azure](container-registry-storage.md).

## <a name="migration-considerations"></a>Posouzení migrace

Při změně registru Classic spravované registru, Azure musíte zkopírovat všechny existující kontejner Image z účtu úložiště ACR vytvořené v rámci vašeho předplatného na účet úložiště, který spravuje Azure. V závislosti na velikosti registru systému Windows tento proces může trvat několik minut až několik hodin.

Během procesu převodu všechny `docker push` blokovány jsou operace, při `docker pull` nadále funkční.

Odstranit nebo změnit obsah účtu úložiště zálohování v registru Classic během převodu. Díky tomu může způsobit poškození systému souborů obrázků kontejneru.

Po dokončení migrace je účet úložiště v rámci vašeho předplatného, který původně zálohována registr Classic již používán ACR. Jakmile ověříte, že migrace byla úspěšná, zvažte odstranění účtu úložiště, které pomáhají minimalizovat náklady.

>[!IMPORTANT]
> Upgrade z klasického na jednu z spravované SKU je **jednosměrný proces**. Po převodu Classic registru na edici Basic, Standard nebo Premium, nelze se vrátit k Classic. Můžete, ale volně přesouvat mezi spravované SKU s dostatečnou kapacitu pro vaše registru.

## <a name="how-to-upgrade"></a>Postup upgradu

Nespravované Classic registru můžete upgradovat na jednu z spravované SKU několika způsoby. V následujících částech se budeme popisují proces pro použití [rozhraní příkazového řádku Azure] [ azure-cli] a [portál Azure][azure-portal].

## <a name="upgrade-in-azure-cli"></a>Upgrade v rozhraní příkazového řádku Azure

Chcete-li upgradovat Classic registru v Azure CLI, spusťte [az acr aktualizace] [ az-acr-update] příkaz a zadejte nové SKU registru. V následujícím příkladu, Classic registru s názvem *myclassicregistry* upgraduje na skladová položka Premium:

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

Po dokončení migrace byste měli vidět výstup podobný následujícímu. Všimněte si, že `sku` "Premium" a `storageAccount` "null," indikující, že Azure nyní spravuje úložiště bitové kopie pro tento registru.

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

Pokud zadáte spravované registru SKU, jehož maximální kapacita je menší než velikost Classic registr, obdržíte chybovou zprávu podobný následujícímu.

`Cannot update the registry SKU due to reason: Registry size 12936251113 bytes exceeds the quota value 10737418240 bytes for SKU Basic. The suggested SKU is Standard.`

Pokud obdržíte chybu podobné, spusťte [az acr aktualizace] [ az-acr-update] příkaz znovu a zadejte navrhované SKU, což je další nejvyšší úrovně SKU, které zvládne vaše Image.

## <a name="upgrade-in-azure-portal"></a>Upgrade na portálu Azure

Když upgradujete registru Classic pomocí portálu Azure, Azure automaticky vybere SKU nejnižší úrovně, které zvládne vaše Image. Například pokud registr obsahuje 12 GiB v obrázcích, Azure automaticky vybere a převede Classic registru Standard (100 maximální GiB).

Upgrade registr Classic pomocí portálu Azure, přejděte do kontejneru registru **přehled** a vyberte **Upgrade na spravované registru**.

![Upgrade tlačítka na uživatelské rozhraní portálu Azure Classic registru][update-classic-01-upgrade]

Vyberte **OK** potvrďte chcete upgradovat na spravované registru.

![Upgrade potvrzení v uživatelské rozhraní portálu Azure Classic registru][update-classic-02-confirm]

Během migrace portálu znamená, že v registru **Stav zřizování** je *aktualizace*. Jak už bylo zmíněno dříve, `docker push` operace jsou zakázané během migrace a nesmí odstranit nebo aktualizovat účet úložiště používané registru Classic při migraci probíhá – díky tomu může mít za následek poškození bitové kopie.

![Průběh v uživatelské rozhraní portálu Azure Classic registru upgradu][update-classic-03-updating]

Po dokončení migrace **Stav zřizování** označuje *úspěšné*, a můžete je znovu `docker push` do registru.

![Stav dokončení v uživatelské rozhraní portálu Azure Classic registru aktualizace][update-classic-04-updated]

## <a name="next-steps"></a>Další postup

Jakmile jste upgradovali Classic registru na edici Basic, Standard nebo Premium, Azure už používá účet úložiště, který původně založenou na registru Classic. Snížit náklady, zvažte odstranění účtu úložiště nebo kontejneru objektů Blob v rámci účtu, který obsahuje vaše staré Image kontejneru.

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade\update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade\update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade\update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade\update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az_acr_update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com