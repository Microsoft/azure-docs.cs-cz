---
title: Azure Container Registry úložiště na webu Azure Portal
description: Postup zobrazení Azure Container Registry úložiště na webu Azure Portal.
services: container-registry
author: cristy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 01/05/2018
ms.author: cristyg
ms.openlocfilehash: 685c978ff206e75d770918f2528a826ad522b706
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60427089"
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>Zobrazit úložiště registru kontejnerů na webu Azure Portal

Služba Azure Container Registry umožňuje ukládat Docker imagí kontejnerů v úložišti. Když ukládání imagí v úložištích, můžete ukládat skupiny imagí (nebo verze Image) v izolovaném prostředí. Při nahrávání imagí do registru a zobrazit jejich obsah na webu Azure Portal, můžete zadat tato úložiště.

## <a name="prerequisites"></a>Požadavky

* **Registr kontejnerů**: Vytvoření registru kontejnerů ve vašem předplatném Azure. Například použít [webu Azure portal](container-registry-get-started-portal.md) nebo [rozhraní příkazového řádku Azure](container-registry-get-started-azure-cli.md).
* **Docker CLI**: Nainstalujte [Docker] [ docker-install] na místním počítači, který vám poskytuje rozhraní příkazového řádku Dockeru.
* **Image kontejneru**: Odeslat image do vašeho registru kontejneru. Pokyny k nabízené a vyžádané instalace Image, najdete v části [nabízená a vyžádaná instalace image](container-registry-get-started-docker-cli.md).

## <a name="view-repositories-in-azure-portal"></a>Zobrazení úložišť na webu Azure portal

Zobrazí se seznam úložišť, který je hostitelem vašich imagí, stejně jako značky obrázku, na webu Azure Portal.

Pokud jste postupovali podle kroků v [nabízená a vyžádaná instalace image](container-registry-get-started-docker-cli.md) (a nebyl následně odstranit image) v registru kontejneru byste měli mít image serveru Nginx. Pokyny v tomto článku zadaný označit image s oborem názvů, "vzorky" v `/samples/nginx`. Jako aktualizačního programu [docker push] [ docker-push] byl příkaz zadaný v tomto článku:

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 Protože Azure Container Registry podporuje takové víceúrovňové obory názvů úložiště, můžete omezit rozsah kolekce imagí souvisejících s konkrétní aplikací nebo kolekcí aplikací pro různé vývojové nebo provozní týmy. Další informace o úložišti v container registry, najdete v článku [registry kontejnerů pro Private Docker v Azure](container-registry-intro.md).

Chcete-li zobrazit úložiště:

1. Přihlaste se k [webu Azure portal][portal]
1. Vyberte **Azure Container Registry** ke kterému jste nasdíleli image serveru Nginx
1. Vyberte **úložišť** zobrazíte seznam úložišť, které obsahují imagí v registru
1. Vyberte úložiště, který chcete zobrazit značky image v rámci tohoto úložiště

Například pokud jste nasdíleli image serveru Nginx jako vydal pokyn pro nástroje v [nabízená a vyžádaná instalace image](container-registry-get-started-docker-cli.md), by měl vypadat podobně jako:

![Úložiště na portálu](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>Další postup

Teď, když znáte základy zobrazení a práce s úložišť na portálu, zkuste pomocí Azure Container Registry se službou [Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md) clusteru.

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com
