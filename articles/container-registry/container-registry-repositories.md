---
title: Azure registru kontejneru úložiště na portálu Azure
description: Postup zobrazení registru kontejner Azure úložiště na portálu Azure.
services: container-registry
author: cristy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 01/05/2018
ms.author: cristyg
ms.openlocfilehash: 4479c78eb2eb4138dbacfa3619616a3b16d51121
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>Zobrazení kontejneru registru úložiště na portálu Azure

Azure kontejneru registru umožňuje ukládání Docker obrázků kontejneru v úložiště. Uložením bitové kopie do úložiště můžete ukládat skupin bitové kopie (nebo verze bitových kopií) v izolovaném prostředí. Tyto úložiště můžete určit, když nabízená bitové kopie v registru a zobrazit jejich obsah na webu Azure portal.

## <a name="prerequisites"></a>Požadavky

* **Kontejner registru**: vytvoření kontejneru registru ve vašem předplatném Azure. Například použít [portál Azure](container-registry-get-started-portal.md) nebo [rozhraní příkazového řádku Azure](container-registry-get-started-azure-cli.md).
* **Rozhraní příkazového řádku dockeru**: Nainstalujte [Docker] [ docker-install] na místním počítači, který vám poskytne rozhraní příkazového řádku Dockeru.
* **Kontejner image**: Push bitovou kopii do registru kontejneru. Pokyny o tom, jak nabízení a vyžadování bitové kopie najdete v tématu [Push a bitovou kopii pro vyžádání obsahu](container-registry-get-started-docker-cli.md).

## <a name="view-repositories-in-azure-portal"></a>Zobrazení úložiště na portálu Azure

Zobrazí se seznam úložiště, který je hostitelem vaší bitové kopie, stejně jako značky obrázku, na portálu Azure.

Pokud jste postupovali podle kroků v [Push a bitovou kopii pro vyžádání obsahu](container-registry-get-started-docker-cli.md) (a následně neodstranila bitovou kopii), musí mít bitovou kopii Nginx v registru kontejneru. Zadaný podle pokynů tohoto článku můžete označit bitovou kopii k oboru názvů "samples" v `/samples/nginx`. Jako aktualizačního programu [docker nabízené] [ docker-push] byl příkaz zadaný v tomto článku:

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 Protože registru kontejner Azure podporuje tyto obory názvů víceúrovňové úložiště, můžete určit obor kolekce obrázků vztahujících se ke konkrétní aplikaci nebo kolekce aplikací na různých vývoj nebo provozní týmy. Další informace o úložiště v registrech kontejneru, najdete v části [registrech kontejner Docker privátní v Azure](container-registry-intro.md).

Chcete-li zobrazit úložiště:

1. Přihlaste se k [portálu Azure][portal]
1. Vyberte **registru kontejner Azure** na kterou instaluje bitovou kopii Nginx
1. Vyberte **úložiště** zobrazíte seznam úložiště, které obsahují Image v registru
1. Vyberte úložiště zobrazíte značky bitové kopie v rámci tohoto úložiště

Například pokud nabídnutých Nginx bitové kopie jako pokynů v [Push a bitovou kopii pro vyžádání obsahu](container-registry-get-started-docker-cli.md), měli byste vidět něco podobného jako:

![Úložiště na portálu](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>Další postup

Nyní když znáte základní informace o zobrazení a práci s úložiště na portálu, zkuste použít registru kontejner Azure s [Azure Container Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md) clusteru.

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com
