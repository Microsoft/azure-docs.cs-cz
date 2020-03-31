---
title: (ZASTARALÉ) Správa clusteru Azure Kubernetes pomocí webového uživatelského uživatelského panelu
description: Použití webového uživatelského uživatelského uživatelského uživatelského uživatelského uživatelského uživatelského uživatelského
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 02/21/2017
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 01abcc961d1c2ad9d3e2cf35f82e62929bc2fb89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371133"
---
# <a name="deprecated-using-the-kubernetes-web-ui-with-azure-container-service"></a>(ZASTARALÉ) Používání webového uživatelského uživatelského uživatelského uživatelského uživatelského uživatelského uživatelského

> [!TIP]
> Aktualizovanou verzi tohoto článku, který používá službu Azure Kubernetes, najdete [v článku Přístup k webovému řídicímu panelu Kubernetes ve službě Azure Kubernetes Service (AKS).](../../aks/kubernetes-dashboard.md)

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Požadavky
Tento návod předpokládá, že jste [vytvořili cluster Kubernetes pomocí služby Azure Container Service](container-service-kubernetes-walkthrough.md).


Také předpokládá, že máte nainstalované `kubectl` azure cli a nástroje.

Můžete otestovat, zda `az` máte nástroj nainstalován spuštěním:

```azurecli
az --version
```

Pokud nástroj nemáte nainstalovaný, `az` jsou [zde](https://github.com/azure/azure-cli#installation)pokyny .

Můžete otestovat, zda `kubectl` máte nástroj nainstalován spuštěním:

```console
kubectl version
```

Pokud nemáte `kubectl` nainstalovaný, můžete spustit:

```azurecli
az acs kubernetes install-cli
```

## <a name="overview"></a>Přehled

### <a name="connect-to-the-web-ui"></a>Připojení k webovému uživatelskému uživatelskému uživatelskému
Webové uživatelské uzla Kubernetes můžete spustit spuštěním:

```azurecli
az acs kubernetes browse -g [Resource Group] -n [Container service instance name]
```

To by mělo otevřít webový prohlížeč nakonfigurován tak, aby mluvit s bezpečným proxy spojující místní počítač k webovému uživatelskému uživatelského prostředí Kubernetes.

### <a name="create-and-expose-a-service"></a>Vytvoření a vystavení služby
1. Ve webovém uživatelském okně Kubernetes klikněte v pravém horním okně na **tlačítko Vytvořit.**

    ![Kubernetes vytvořit ui](./media/container-service-kubernetes-ui/create.png)

    Otevře se dialogové okno, ve kterém můžete začít vytvářet aplikaci.

2. Dejte mu `hello-nginx`jméno . Použijte [ `nginx` kontejner z Dockeru](https://hub.docker.com/_/nginx/) a nasaďte tři repliky této webové služby.

    ![Dialogové okno Vytvořit kubernetes pod](./media/container-service-kubernetes-ui/nginx.png)

3. V části **Služba**vyberte **Externí** a zadejte port 80.

    Toto nastavení zatížení vyvažuje provoz na tři repliky.

    ![Dialogové okno Vytvoření služby Kubernetes](./media/container-service-kubernetes-ui/service.png)

4. Kliknutím na **Nasadit** tyto kontejnery a služby nasadíte.

    ![Nasazení Kubernetes](./media/container-service-kubernetes-ui/deploy.png)

### <a name="view-your-containers"></a>Zobrazení kontejnerů
Po klepnutí na tlačítko **Nasadit**se v ui zobrazí zobrazení vaší služby při jeho nasazení:

![Stav Kubernetes](./media/container-service-kubernetes-ui/status.png)

Stav každého Objektu Kubernetes můžete vidět v kruhu na levé straně ui **pods, pods**. Pokud je částečně plný kruh, objekt je stále nasazuje. Když je objekt plně nasazen, zobrazí zelenou značku zaškrtnutí:

![Kubernetes nasazen](./media/container-service-kubernetes-ui/deployed.png)

Jakmile je vše spuštěno, klikněte na jeden z podů a podívejte se na podrobnosti o spuštěné webové službě.

![Kubernetes Lusky](./media/container-service-kubernetes-ui/pods.png)

V zobrazení **Pods** můžete zobrazit informace o kontejnerech v podu, stejně jako cpu a paměťové prostředky používané těmito kontejnery:

![Zdroje společnosti Kubernetes](./media/container-service-kubernetes-ui/resources.png)

Pokud nevidíte prostředky, budete muset počkat několik minut pro data monitorování k šíření.

Chcete-li zobrazit protokoly pro kontejner, klepněte na tlačítko **Zobrazit protokoly**.

![Kubernetes protokoly](./media/container-service-kubernetes-ui/logs.png)

### <a name="viewing-your-service"></a>Zobrazení služby
Kromě spuštění kontejnerů vytvořilo unové i. kubernetes externí, `Service` které zřizovat eviduje vyrovnávání zatížení, aby přenosy do kontejnerů ve vašem clusteru.

V levém navigačním podokně klikněte na **Služby,** chcete-li zobrazit všechny služby (měla by být pouze jedna).

![Služby Kubernetes](./media/container-service-kubernetes-ui/service-deployed.png)

V tomto zobrazení byste měli vidět externí koncový bod (IP adresa), který byl přidělen vaší službě.
Pokud kliknete na tuto IP adresu, měli byste vidět váš kontejner Nginx běží za vyrovnávání zatížení.

![nginx zobrazení](./media/container-service-kubernetes-ui/nginx-page.png)

### <a name="resizing-your-service"></a>Změna velikosti služby
Kromě zobrazení objektů v ui můžete upravovat a aktualizovat objekty rozhraní API Kubernetes.

Nejprve klikněte na **Nasazení** v levém navigačním podokně a podívejte se na nasazení vaší služby.

Po zobrazení klikněte na sadu replik a potom klikněte na **upravit** na horním navigačním panelu:

![Kubernetes Upravit](./media/container-service-kubernetes-ui/edit.png)

Upravte `spec.replicas` pole, `2`které má být , a klepněte na tlačítko **Aktualizovat**.

To způsobí, že počet replik klesnout na dvě odstraněním jednoho z podů.

 

