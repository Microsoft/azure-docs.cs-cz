---
title: Zobrazit úložiště na portálu
description: Pomocí Azure Portal můžete zobrazit Azure Container Registry úložiště, která hostují image kontejneru Docker a další podporované artefakty.
ms.topic: article
ms.date: 01/05/2018
ms.openlocfilehash: 1da72706d2554610a685f71199ab14af5e30ce1a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "74456289"
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>Zobrazení úložišť registru kontejnerů v Azure Portal

Azure Container Registry umožňuje ukládat image kontejneru Docker v úložištích. Díky ukládání obrázků do úložišť můžete ukládat skupiny imagí (nebo verze imagí) do izolovaných prostředí. Tato úložiště můžete zadat, když nahrajete obrázky do svého registru a zobrazíte jejich obsah v Azure Portal.

## <a name="prerequisites"></a>Předpoklady

* **Registr kontejnerů**: Vytvořte registr kontejnerů ve vašem předplatném Azure. Použijte například [Azure Portal](container-registry-get-started-portal.md) nebo rozhraní příkazového [řádku Azure CLI](container-registry-get-started-azure-cli.md).
* **Docker CLI**: nainstalujte [Docker][docker-install] do místního počítače, který vám poskytne rozhraní příkazového řádku Docker.
* **Image kontejneru**: Nahrajte image do vašeho registru kontejneru. Pokyny k nasdílení a vyžádání imagí najdete v tématu [vložení a načtení obrázku](container-registry-get-started-docker-cli.md).

## <a name="view-repositories-in-azure-portal"></a>Zobrazit úložiště v Azure Portal

V Azure Portal můžete zobrazit seznam úložišť, která jsou hostitelem imagí, a také značky obrázků.

Pokud jste postupovali podle kroků v části [push a pull image](container-registry-get-started-docker-cli.md) (a následně image neodstraníte), měli byste mít Nginx image ve vašem registru kontejneru. V pokynech v tomto článku jste určili, že obrázek označíte pomocí oboru názvů, "Samples" v `/samples/nginx` . Jako aktualizační program, příkaz [Docker push][docker-push] uvedený v tomto článku:

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 Vzhledem k tomu, že Azure Container Registry podporuje takové víceúrovňové obory názvů úložiště, můžete vytvořit obor kolekcí imagí souvisejících s konkrétní aplikací nebo kolekcí aplikací pro různé vývojové nebo provozní týmy. Další informace o úložištích v registrech kontejnerů najdete v tématu [soukromé Registry kontejnerů Docker v Azure](container-registry-intro.md).

Zobrazení úložiště:

1. Přihlaste se k portálu [Azure Portal][portal].
1. Vyberte **Azure Container Registry** , do které jste posunuli obrázek Nginx.
1. Vyberte **úložiště** a zobrazte seznam úložišť, která obsahují image v registru.
1. Vyberte úložiště, ve kterém se mají zobrazit značky imagí v tomto úložišti.

Pokud jste například nahráli image Nginx jako na [vyžádání a](container-registry-get-started-docker-cli.md)načetli obrázek, měla by se zobrazit něco podobného:

![Úložiště na portálu](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>Další kroky

Teď, když znáte základy zobrazení a práce s úložišti na portálu, zkuste použít Azure Container Registry s clusterem [Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md) .

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com
