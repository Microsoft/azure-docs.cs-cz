---
title: ZASTARALÉ Správa clusteru Azure Kubernetes s webovým uživatelským rozhraním
description: Použití webového uživatelského rozhraní Kubernetes v Azure Container Service
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 02/21/2017
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 01abcc961d1c2ad9d3e2cf35f82e62929bc2fb89
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371133"
---
# <a name="deprecated-using-the-kubernetes-web-ui-with-azure-container-service"></a>ZASTARALÉ Použití webového uživatelského rozhraní Kubernetes s Azure Container Service

> [!TIP]
> Aktualizovanou verzi tohoto článku, který používá službu Azure Kubernetes, najdete v tématu [přístup k webovému řídicímu panelu Kubernetes ve službě Azure Kubernetes Service (AKS)](../../aks/kubernetes-dashboard.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Požadavky
Tento návod předpokládá, že jste [vytvořili cluster Kubernetes pomocí Azure Container Service](container-service-kubernetes-walkthrough.md).


Také předpokládá, že máte nainstalované nástroje Azure CLI a `kubectl`.

Můžete otestovat, jestli máte nainstalovaný nástroj `az`, a to spuštěním:

```azurecli
az --version
```

Pokud nemáte nainstalovaný nástroj `az`, [tady](https://github.com/azure/azure-cli#installation)najdete pokyny.

Můžete otestovat, jestli máte nainstalovaný nástroj `kubectl`, a to spuštěním:

```console
kubectl version
```

Pokud nemáte nainstalované `kubectl`, můžete spustit:

```azurecli
az acs kubernetes install-cli
```

## <a name="overview"></a>Přehled

### <a name="connect-to-the-web-ui"></a>Připojení k webovému uživatelskému rozhraní
Webové uživatelské rozhraní Kubernetes můžete spustit spuštěním:

```azurecli
az acs kubernetes browse -g [Resource Group] -n [Container service instance name]
```

Mělo by se otevřít webový prohlížeč nakonfigurovaný tak, aby komunikoval s zabezpečeným proxy serverem, který připojuje váš místní počítač k webovému uživatelskému rozhraní Kubernetes.

### <a name="create-and-expose-a-service"></a>Vytvoření a vystavení služby
1. Ve webovém uživatelském rozhraní Kubernetes klikněte na tlačítko **vytvořit** v pravém horním rohu okna.

    ![Kubernetes vytvořit uživatelské rozhraní](./media/container-service-kubernetes-ui/create.png)

    Otevře se dialogové okno, kde můžete začít vytvářet aplikace.

2. Dejte mu název `hello-nginx`. Použijte [kontejner`nginx` z Docker](https://hub.docker.com/_/nginx/) a nasaďte tři repliky této webové služby.

    ![Dialog vytvořit Kubernetes pod](./media/container-service-kubernetes-ui/nginx.png)

3. V části **Služba**vyberte **externí** a zadejte port 80.

    Toto nastavení vyrovnává zatížení provozu do tří replik.

    ![Dialog vytvořit službu Kubernetes](./media/container-service-kubernetes-ui/service.png)

4. Kliknutím na **nasadit** nasaďte tyto kontejnery a služby.

    ![Nasazení Kubernetes](./media/container-service-kubernetes-ui/deploy.png)

### <a name="view-your-containers"></a>Zobrazení kontejnerů
Po kliknutí na **nasadit**se v uživatelském rozhraní zobrazí zobrazení vaší služby při nasazení:

![Stav Kubernetes](./media/container-service-kubernetes-ui/status.png)

Stav každého objektu Kubernetes můžete zobrazit v kruhu na levé straně uživatelského rozhraní v části **lusky**. Pokud se jedná o částečně plný kroužek, je objekt stále nasazen. Když je objekt plně nasazený, zobrazí se zelená značka zaškrtnutí:

![Nasazené Kubernetes](./media/container-service-kubernetes-ui/deployed.png)

Jakmile bude vše spuštěné, klikněte na jednu z lusků a zobrazte si podrobnosti o běžící webové službě.

![Kubernetes lusky](./media/container-service-kubernetes-ui/pods.png)

V zobrazení **lusky** můžete zobrazit informace o kontejnerech v poli pod a také prostředky procesoru a paměti, které tyto kontejnery používají:

![Prostředky Kubernetes](./media/container-service-kubernetes-ui/resources.png)

Pokud prostředky nevidíte, možná budete muset několik minut počkat, než se data monitorování rozšíří.

Pokud chcete zobrazit protokoly pro svůj kontejner, klikněte na **Zobrazit protokoly**.

![Protokoly Kubernetes](./media/container-service-kubernetes-ui/logs.png)

### <a name="viewing-your-service"></a>Zobrazení vaší služby
Kromě spuštění kontejnerů vytvořil uživatelské rozhraní Kubernetes externí `Service`, který zřídí Nástroj pro vyrovnávání zatížení k přenosu provozu do kontejnerů ve vašem clusteru.

V levém navigačním podokně klikněte na **služby** , aby se zobrazily všechny služby (měla by existovat jenom jedna).

![Služby Kubernetes](./media/container-service-kubernetes-ui/service-deployed.png)

V tomto zobrazení by se měl zobrazit externí koncový bod (IP adresa), který byl přidělen vaší službě.
Pokud kliknete na tuto IP adresu, měli byste vidět, že váš kontejner Nginx běží za nástrojem pro vyrovnávání zatížení.

![zobrazení Nginx](./media/container-service-kubernetes-ui/nginx-page.png)

### <a name="resizing-your-service"></a>Změna velikosti vaší služby
Kromě zobrazení objektů v uživatelském rozhraní můžete upravit a aktualizovat objekty rozhraní API Kubernetes.

Nejdřív v levém navigačním podokně klikněte na **nasazení** , abyste viděli nasazení služby.

Až budete v tomto zobrazení, klikněte na sadu replik a potom v horním navigačním panelu klikněte na **Upravit** :

![Kubernetes úpravy](./media/container-service-kubernetes-ui/edit.png)

Upravte pole `spec.replicas`, které chcete `2`, a klikněte na tlačítko **aktualizovat**.

To způsobí, že počet replik může být mezi dvěma odstraněním jednoho z vašich lusků.

 

