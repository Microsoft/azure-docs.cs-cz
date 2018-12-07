---
title: (NEPOUŽÍVANÉ) Správa clusteru Kubernetes v Azure pomocí webového uživatelského rozhraní
description: Pomocí webového uživatelského rozhraní Kubernetes ve službě Azure Container Service
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/21/2017
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: c3a79b2e4fab807613a54d2792f5f5b97570293b
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52996136"
---
# <a name="deprecated-using-the-kubernetes-web-ui-with-azure-container-service"></a>(NEPOUŽÍVANÉ) Pomocí webového uživatelského rozhraní Kubernetes pomocí služby Azure Container Service

> [!TIP]
> Pro aktualizovanou verzi, tento článek, který používá Azure Kubernetes Service, najdete v článku [přístup k řídicímu panelu Kubernetes web ve službě Azure Kubernetes Service (AKS)](../../aks/kubernetes-dashboard.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Požadavky
Tento názorný průvodce předpokládá, že máte [vytvořit cluster Kubernetes pomocí služby Azure Container Service](container-service-kubernetes-walkthrough.md).


Dále předpokládá, že máte Azure CLI a `kubectl` nainstalované nástroje.

Můžete otestovat, pokud máte `az` nástroj pro instalaci spuštěním:

```console
$ az --version
```

Pokud nemáte k dispozici `az` nástroj nainstalovali, jsou k dispozici pokyny [tady](https://github.com/azure/azure-cli#installation).

Můžete otestovat, pokud máte `kubectl` nástroj pro instalaci spuštěním:

```console
$ kubectl version
```

Pokud nemáte `kubectl` nainstalované, můžete spustit:

```console
$ az acs kubernetes install-cli
```

## <a name="overview"></a>Přehled

### <a name="connect-to-the-web-ui"></a>Připojte se k webu uživatelského rozhraní
Webové uživatelské rozhraní Kubernetes můžete spustit spuštěním:

```console
$ az acs kubernetes browse -g [Resource Group] -n [Container service instance name]
```

To by měla otevřít webový prohlížeč nakonfigurován pro komunikaci se proxy server zabezpečené připojení místního počítače do webového uživatelského rozhraní Kubernetes.

### <a name="create-and-expose-a-service"></a>Vytvoření a vystavení služby
1. V webovým Uživatelským rozhraním Kubernetes, klikněte na tlačítko **vytvořit** tlačítko v horním pravém okně.

    ![Vytvoření uživatelského rozhraní Kubernetes](./media/container-service-kubernetes-ui/create.png)

    Otevře se dialogové okno kde můžete začít vytvářet aplikace.

2. Přiřaďte jí název `hello-nginx`. Použití [ `nginx` kontejneru z Docker](https://hub.docker.com/_/nginx/) a nasazení tři repliky této webové služby.

    ![Dialogové okno Vytvořit Podů Kubernetes](./media/container-service-kubernetes-ui/nginx.png)

3. V části **služby**vyberte **externí** a zadejte port 80.

    Toto nastavení zatížení zůstatky přenosů na třech replikách.

    ![Dialogové okno Vytvoření služby Kubernetes](./media/container-service-kubernetes-ui/service.png)

4. Klikněte na tlačítko **nasadit** k nasazování těchto kontejnerů a služeb.

    ![Nasazení Kubernetes](./media/container-service-kubernetes-ui/deploy.png)

### <a name="view-your-containers"></a>Zobrazení kontejnerů
Po kliknutí na **nasadit**, uživatelské rozhraní se teď zobrazují služby jako nasazení:

![Stav Kubernetes](./media/container-service-kubernetes-ui/status.png)

Stav každého objektu Kubernetes v kruhu na levé straně uživatelského rozhraní, můžete zobrazit v části **Podů**. Pokud je částečně úplné kruh, pak objekt ještě probíhá jeho nasazení. Při objektu je plně nasazena, se zobrazí zelená značka zaškrtnutí:

![Nasazení Kubernetes](./media/container-service-kubernetes-ui/deployed.png)

Jakmile všechno běží, klikněte na některou pody zobrazíte podrobnosti o spuštěnou webovou službu.

![Podů Kubernetes](./media/container-service-kubernetes-ui/pods.png)

V **Podů** zobrazení, zobrazí se informace o kontejnerech v pod, jakož i prostředky procesoru a paměti používané těchto kontejnerů:

![Prostředky Kubernetes](./media/container-service-kubernetes-ui/resources.png)

Pokud nevidíte prostředky, budete muset počkat několik minut, než se data monitorování šíření.

Pokud chcete zobrazit protokoly pro kontejner, klikněte na tlačítko **zobrazit protokoly**.

![Protokoly Kubernetes](./media/container-service-kubernetes-ui/logs.png)

### <a name="viewing-your-service"></a>Zobrazení vaší služby
Kromě spuštění kontejnerů, uživatelské rozhraní Kubernetes vytvořil externí `Service` který zřídí nástroj pro vyrovnávání zatížení provozu přenést do kontejnerů v clusteru.

V levém navigačním podokně klikněte na tlačítko **služby** Chcete-li zobrazit všechny služby (měl by existovat pouze jeden).

![Služby Kubernetes](./media/container-service-kubernetes-ui/service-deployed.png)

V tomto zobrazení měli byste vidět externí koncový bod (IP adresa), který byl přidělen do vaší služby.
Pokud kliknete na tuto IP adresu, měli byste vidět váš kontejner Nginx a spuštění za nástrojem pro vyrovnávání zatížení.

![zobrazení serveru nginx](./media/container-service-kubernetes-ui/nginx-page.png)

### <a name="resizing-your-service"></a>Změna velikosti služby
Kromě zobrazování vašich objektů v uživatelském rozhraní, můžete upravit a aktualizovat objekty rozhraní Kubernetes API.

Nejprve kliknutím na **nasazení** v levém navigačním podokně k nasazení pro vaši službu.

Až budete v tomto zobrazení, klikněte na sady replik a pak klikněte na tlačítko **upravit** v horním navigačním panelu:

![Upravit Kubernetes](./media/container-service-kubernetes-ui/edit.png)

Upravit `spec.replicas` pole bylo `2`a klikněte na tlačítko **aktualizace**.

To způsobí, že počet replik vyřadit do dvou odstraněním pody.

 

