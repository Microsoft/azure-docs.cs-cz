---
title: Kontejnery pro vyrovnávání zatížení v clusteru Azure DC/OS
description: Článek popisuje, jak se vyrovnává zatížení více kontejnerů v clusteru Azure Container Service DC/OS.
services: container-service
author: rgardler
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 06/02/2017
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 62967636a4d80f72f731a666947d5d4d5e47f7e5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32163040"
---
# <a name="load-balance-containers-in-an-azure-container-service-dcos-cluster"></a>Vyrovnávání zatížení kontejnerů v clusteru Azure Container Service DC/OS

V tomto článku se podíváme, jak se pomocí nástroje Marathon-LB vytváří vnitřní nástroj pro vyrovnávání zatížení v prostředí Azure Container Service spravovaném systémem DC/OS. Tato konfigurace vám umožní horizontálně škálovat aplikace. Také vám umožní využít veřejný i soukromý cluster v tom smyslu, že na veřejný umístíte nástroje pro vyrovnávání zatížení, zatímco na soukromý kontejnery aplikací. V tomto kurzu:

> [!div class="checklist"]
> * Nakonfigurujete Marathon Load Balancer
> * Pomocí nástroje pro vyrovnávání zatížení nasadíte aplikaci
> * Nakonfigurujete nástroj pro vyrovnávání zatížení Azure

K provedení kroků v tomto kurzu potřebujete cluster DC/OS ACS. V případě potřeby si ho můžete nechat vytvořit pomocí [tohoto ukázkového skriptu](./../kubernetes/scripts/container-service-cli-deploy-dcos.md).

Tento kurz vyžaduje Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="load-balancing-overview"></a>Přehled vyrovnávání zatížení

V clusteru Azure Container Service DC/OS jsou dvě vrstvy vyrovnávající zatížení: 

**Azure Load Balancer** poskytuje veřejné vstupní body (ty, ke kterým mají přístup koncoví uživatelé). Nástroj Azure Load Balancer je automaticky poskytován službou Azure Container Service. Ve výchozím nastavení zpřístupňuje porty 80, 443 a 8080.

Nástroj **Marathon Load Balancer (marathon-lb)** směruje příchozí požadavky na instance kontejneru, které tyto požadavky vyřizují. Marathon-lb se bude dynamicky adaptovat podle toho, jak budeme navyšovat kapacitu kontejnerů, které poskytují naši webovou službu. Tento nástroj pro vyrovnávání zatížení není zahrnutý ve výchozí instalaci služby Container Service, dá se ale snadno doinstalovat.

## <a name="configure-marathon-load-balancer"></a>Konfigurace nástroje Marathon Load Balancer

Marathon Load Balancer se sám dynamicky rekonfiguruje na základě kontejnerů, které jste nasadili. Kromě toho je také odolný vůči ztrátě kontejneru nebo agenta. Když k takové ztrátě dojde, Apache Mesos restartuje kontejner na jiném místě a marathon-lb se adaptuje.

Spuštěním následujícího příkazu nainstalujte Marathon Load Balancer v clusteru veřejného agenta.

```azurecli-interactive
dcos package install marathon-lb
```

## <a name="deploy-load-balanced-application"></a>Nasazení aplikace s vyrovnáváním zatížení

Jakmile máme balíček marathon-lb, můžeme nasadit kontejner aplikace, u kterého chceme vyrovnávat zatížení. 

Nejdřív získejte plně kvalifikovaný název domény veřejně vystavených agentů.

```azurecli-interactive
az acs list --resource-group myResourceGroup --query "[0].agentPoolProfiles[0].fqdn" --output tsv
```

Potom vytvořte soubor s názvem *hello-web.json* a zkopírujte do něj následující obsah. Současně s plně kvalifikovaným názvem domény agentů DC/OS je potřeba aktualizovat popisky `HAPROXY_0_VHOST`. 

```json
{
  "id": "web",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "yeasy/simple-web",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  }
}
```

Pomocí rozhraní příkazového řádku DC/OS spusťte aplikaci. Ve výchozím nastavení nasadí Marathon aplikaci do privátního clusteru. To znamená, že výše popsané nasazení je přístupné jenom přes nástroj pro vyrovnávání zatížení, což je obvykle žádoucí chování.

```azurecli-interactive
dcos marathon app add hello-web.json
```

Jakmile je aplikace nasazená, přejděte na plně kvalifikovaný název domény clusteru agenta, kde můžete zobrazit aplikaci s vyrovnáváním zatížení.

![Obrázek aplikace s vyrovnáváním zatížení](./media/container-service-load-balancing/lb-app.png)

## <a name="configure-azure-load-balancer"></a>Konfigurace služby Azure Load Balancer

Služba Azure Load Balancer ve výchozím nastavení zpřístupňuje porty 80, 8080 a 443. Pokud používáte některý z těchto tří portů (jako my v příkladu výše), není třeba provádět žádnou další akci. Měli byste mít možnost volat plně kvalifikovaný název domény agenta nástroje pro vyrovnávání zatížení a při každé aktualizaci se bude volat jeden z vašich tří webových serverů – budou se cyklicky střídat. 

Pokud používáte jiný port, bude potřeba přidat pravidlo kruhového dotazování a otestovat v nástroji pro vyrovnávání zatížení port, který jste použili. To lze udělat z [rozhraní příkazového řádku Azure](../../azure-resource-manager/xplat-cli-azure-resource-manager.md) pomocí příkazů `azure network lb rule create` a `azure network lb probe create`.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o vyrovnávání zatížení ve službě ACS pomocí nástrojů na vyrovnávání zatížení Marathon a Azure, včetně následujících akcí:

> [!div class="checklist"]
> * Konfigurace nástroje Marathon Load Balancer
> * Nasazení aplikace pomocí nástroje pro vyrovnávání zatížení
> * Konfigurace nástroje pro vyrovnávání zatížení Azure

V dalším kurzu se naučíte integrovat úložiště Azure s DC/OS v Azure.

> [!div class="nextstepaction"]
> [Připojení sdílené složky Azure v clusteru DC/OS](container-service-dcos-fileshare.md)