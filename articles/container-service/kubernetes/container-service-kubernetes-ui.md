---
title: Správě Azure Kubernetes clusteru pomocí webového uživatelského rozhraní
description: V Azure Container Service pomocí Kubernetes webového uživatelského rozhraní
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/21/2017
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 0680551d3a87c942574a4eac70fa380cc1e9b5d9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32163244"
---
# <a name="using-the-kubernetes-web-ui-with-azure-container-service"></a>Pomocí Azure Container Service Kubernetes webového uživatelského rozhraní

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>Požadavky
Tento návod předpokládá, že máte [vytvořit Kubernetes clusteru Azure Container Service pomocí](container-service-kubernetes-walkthrough.md).


Také předpokládá, že máte Azure CLI 2.0 a `kubectl` nástroje nainstalované.

Můžete otestovat, pokud máte `az` nainstalovaná, spuštěním nástroje:

```console
$ az --version
```

Pokud nemáte `az` nástroj nainstalovali, jsou k dispozici pokyny [zde](https://github.com/azure/azure-cli#installation).

Můžete otestovat, pokud máte `kubectl` nainstalovaná, spuštěním nástroje:

```console
$ kubectl version
```

Pokud nemáte `kubectl` nainstalován, můžete spustit:

```console
$ az acs kubernetes install-cli
```

## <a name="overview"></a>Přehled

### <a name="connect-to-the-web-ui"></a>Připojit k serveru webového uživatelského rozhraní
Spuštěním můžete spustit Kubernetes webového uživatelského rozhraní:

```console
$ az acs kubernetes browse -g [Resource Group] -n [Container service instance name]
```

To by měla otevřít webový prohlížeč nakonfigurován tak, aby obraťte se na proxy server zabezpečené připojení místního počítače k Kubernetes webového uživatelského rozhraní.

### <a name="create-and-expose-a-service"></a>Vytvoření a vystavení služby
1. V Kubernetes webového uživatelského rozhraní, klikněte na **vytvořit** tlačítko v horním pravém okně.

    ![Kubernetes vytvoření uživatelského rozhraní](./media/container-service-kubernetes-ui/create.png)

    Otevře se dialogové okno kde můžete začít vytvářet aplikace.

2. Poskytněte název `hello-nginx`. Použití [ `nginx` kontejneru z Docker](https://hub.docker.com/_/nginx/) a nasadit tři repliky této webové služby.

    ![Dialogové okno Vytvořit Kubernetes Pod](./media/container-service-kubernetes-ui/nginx.png)

3. V části **služby**, vyberte **externí** a zadejte port 80.

    Toto nastavení zatížení zůstatky provoz na tři repliky.

    ![Dialogové okno Vytvořit Kubernetes služby](./media/container-service-kubernetes-ui/service.png)

4. Klikněte na tlačítko **nasadit** k nasazení těchto kontejnerů a služeb.

    ![Kubernetes nasazení](./media/container-service-kubernetes-ui/deploy.png)

### <a name="view-your-containers"></a>Zobrazit vaše kontejnery
Po kliknutí na tlačítko **nasadit**, uživatelské rozhraní zobrazí zobrazení vaší služby jako nasazení:

![Stav Kubernetes](./media/container-service-kubernetes-ui/status.png)

Stav každého objektu Kubernetes na zobrazený na levé straně uživatelského rozhraní, můžete zobrazit v části **pracovními stanicemi soustředěnými kolem**. Pokud je částečně úplné kruh, objekt stále nasazení. Po nasazení objekt zobrazí zelená značka zaškrtnutí:

![Kubernetes nasazení](./media/container-service-kubernetes-ui/deployed.png)

Jakmile všechno běží, klikněte na jednu z vaší pracovními stanicemi soustředěnými kolem zobrazíte podrobnosti o webové službě spuštěné.

![Kubernetes pracovními stanicemi soustředěnými kolem](./media/container-service-kubernetes-ui/pods.png)

V **pracovními stanicemi soustředěnými kolem** zobrazení, se zobrazí informace o kontejnery v pod, jakož i prostředky procesoru a paměti používané těchto kontejnerů:

![Kubernetes prostředky](./media/container-service-kubernetes-ui/resources.png)

Pokud nevidíte prostředky, musíte Počkejte několik minut pro data sledování rozšíření.

Chcete-li zobrazit protokoly pro váš kontejner, klikněte na tlačítko **zobrazit protokoly**.

![Protokoly Kubernetes](./media/container-service-kubernetes-ui/logs.png)

### <a name="viewing-your-service"></a>Zobrazení služby
Kromě spuštění kontejnerů, rozhraní Kubernetes vytvořil externí `Service` která zřizuje Vyrovnávání zatížení, aby provoz do kontejnerů v clusteru.

V levém navigačním podokně klikněte na tlačítko **služby** zobrazíte všechny služby (měla by existovat pouze jedna).

![Kubernetes služby](./media/container-service-kubernetes-ui/service-deployed.png)

V tomto zobrazení měli byste vidět externí koncový bod (IP adresa) přiřazené k službě.
Pokud kliknete na tuto IP adresu, měli byste vidět vaše kontejner nginx a sváže s za nástrojem pro vyrovnávání zatížení.

![nginx zobrazení](./media/container-service-kubernetes-ui/nginx-page.png)

### <a name="resizing-your-service"></a>Změna velikosti služby
Kromě zobrazování vašich objektů v uživatelském rozhraní, můžete upravit a aktualizovat objekty Kubernetes rozhraní API.

Nejprve, klikněte na tlačítko **nasazení** v levém navigačním podokně zobrazíte nasazení pro vaši službu.

Až se v tomto zobrazení, klikněte na sady replik a pak klikněte na tlačítko **upravit** v horním navigačním panelu:

![Upravit Kubernetes](./media/container-service-kubernetes-ui/edit.png)

Upravit `spec.replicas` pole, které chcete být `2`a klikněte na tlačítko **aktualizace**.

To způsobí, že počet replik odpojení ke dvěma odstraněním vaší pracovními stanicemi soustředěnými kolem.

 

