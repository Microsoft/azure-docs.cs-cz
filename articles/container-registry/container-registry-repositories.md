---
title: Zobrazit úložiště na portálu
description: Na portálu Azure můžete zobrazit úložiště registru kontejnerů Azure, která hostují ionty kontejnerů Dockeru a další podporované artefakty.
ms.topic: article
ms.date: 01/05/2018
ms.openlocfilehash: 1da72706d2554610a685f71199ab14af5e30ce1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456289"
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>Zobrazení úložišť registru kontejnerů na webu Azure Portal

Azure Container Registry umožňuje ukládat ibi kontejnerů Dockeru v úložištích. Uložením obrázků do úložišť můžete ukládat skupiny obrázků (nebo verze obrázků) v izolovaných prostředích. Tyto úložiště můžete zadat při nabízení bitových kopií do registru a zobrazení jejich obsahu na webu Azure Portal.

## <a name="prerequisites"></a>Požadavky

* **Registr kontejnerů:** Vytvořte registr kontejnerů v předplatném Azure. Například použijte [portál Azure](container-registry-get-started-portal.md) nebo [Azure CLI](container-registry-get-started-azure-cli.md).
* **Docker CLI**: Nainstalujte [Docker][docker-install] na místním počítači, který vám poskytuje rozhraní příkazového řádku Dockeru.
* **Image kontejneru**: Zasuňte bitovou kopii do registru kontejnerů. Pokyny k nabízení a tažení obrázků najdete v [tématu Tlačení a tažení obrázku](container-registry-get-started-docker-cli.md).

## <a name="view-repositories-in-azure-portal"></a>Zobrazení úložišť na webu Azure Portal

Seznam úložišť, které hostují vaše image, a také značky obrázků najdete na webu Azure Portal.

Pokud jste postupovali podle kroků v [Push and pull image](container-registry-get-started-docker-cli.md) (a následně neodstranili bitovou kopii), měli byste mít bitovou kopii Nginx v registru kontejneru. Pokyny v tomto článku určené, že označení obrázku s oborem názvů, "ukázky" v `/samples/nginx`. Jako aktualizační příkaz [příkaz udockeru][docker-push] zadaný v tomto článku byl:

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 Vzhledem k tomu, že Azure Container Registry podporuje takové víceúrovňové obory názvů úložiště, můžete obor kolekce ibi související s konkrétní aplikaci nebo kolekce aplikací do různých vývojových nebo provozních týmů. Další informace o repozitářích v registrech kontejnerů najdete v [tématu Registry kontejnerů Privátního Dockeru v Azure](container-registry-intro.md).

Zobrazení úložiště:

1. Přihlášení k [portálu Azure][portal]
1. Vyberte **registr kontejnerů Azure,** do kterého jste strčili bitovou kopii Nginx.
1. Výběrem **možnosti Repozitáře** zobrazíte seznam úložišť, které obsahují obrázky v registru.
1. Výběrem úložiště zobrazíte obrazové značky v tomto úložišti.

Pokud jste například posunuli obraz Nginx podle pokynů v [tématu Push and pull a image](container-registry-get-started-docker-cli.md), měli byste vidět něco podobného:

![Repozitáře na portálu](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>Další kroky

Teď, když znáte základy zobrazení a práce s úložišti na portálu, zkuste použít Azure Container Registry s clusterem [Služby Azure Kubernetes (AKS).](../aks/tutorial-kubernetes-prepare-app.md)

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com
