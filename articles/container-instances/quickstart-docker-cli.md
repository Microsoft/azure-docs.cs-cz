---
title: Rychlý Start – nasazení kontejneru Docker do kontejneru rozhraní příkazového řádku Docker instance
description: V tomto rychlém startu použijete Docker CLI k rychlému nasazení kontejnerové webové aplikace, která běží v izolované instanci kontejneru Azure.
ms.topic: quickstart
ms.date: 09/14/2020
ms.custom: devx-track-js
ms.openlocfilehash: 9904fb8c2142816196a1939a16445318bdb245d0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91262310"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-docker-cli"></a>Rychlý Start: nasazení instance kontejneru v Azure pomocí Docker CLI

Použijte Azure Container Instances ke spouštění kontejnerů Docker bez serveru v Azure s využitím jednoduchosti a rychlosti. Nasazení do instance kontejneru na vyžádání při vývoji nativních aplikací cloudu a chcete hladce přepnout z místního vývoje na nasazení do cloudu.

V tomto rychlém startu použijete nativní příkazy rozhraní příkazového řádku Docker pro nasazení kontejneru Docker a zpřístupnění jeho aplikace v Azure Container Instances. Tato funkce je povolená [integrací mezi Docker a Azure](https://docs.docker.com/engine/context/aci-integration/). Několik sekund po provedení `docker run` příkazu můžete přejít na aplikaci spuštěnou v kontejneru:

:::image type="content" source="media/quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="Aplikace nasazená pomocí služby Azure Container Instances zobrazená v prohlížeči":::

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet][azure-account].

V tomto rychlém startu potřebujete Docker Desktop verze 2.3.0.5 nebo novější, který je k dispozici pro [Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) nebo [MacOS](https://desktop.docker.com/mac/edge/Docker.dmg). Nebo nainstalujte rozhraní příkazového [řádku Docker ACI Integration CLI pro Linux](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux). 

> [!IMPORTANT]
> Ne všechny funkce Azure Container Instances jsou podporovány. Poskytněte zpětnou vazbu o integraci Docker – Azure vytvořením problému v úložišti GitHub [ACI-Integration-beta](https://github.com/docker/aci-integration-beta) .

[!INCLUDE [container-instances-create-docker-context](../../includes/container-instances-create-docker-context.md)]

## <a name="create-a-container"></a>Vytvoření kontejneru

Po vytvoření kontextu Docker můžete vytvořit kontejner v Azure. V tomto rychlém startu použijete veřejnou `mcr.microsoft.com/azuredocs/aci-helloworld` image. Tento obrázek zabalí malou webovou aplikaci napsanou v Node.js, která slouží jako statická stránka HTML.

Nejprve přejděte do kontextu ACI. Všechny následné příkazy Docker se spouštějí v tomto kontextu.

```
docker context use myacicontext
```

Spuštěním následujícího `docker run` příkazu vytvořte instanci kontejneru Azure s portem 80 zveřejněným pro Internet:

```
docker run -p 80:80 mcr.microsoft.com/azuredocs/aci-helloworld
```

Ukázkový výstup pro úspěšné nasazení:

```
[+] Running 2/2
 ⠿ hungry-kirch            Created                                                                               5.1s
 ⠿ single--container--aci  Done                                                                                 11.3s
hungry-kirch
```

Spusťte `docker ps` pro získání podrobností o běžícím kontejneru, včetně veřejné IP adresy:

```
docker ps
```


Vzorový výstup zobrazuje veřejnou IP adresu, v tomto případě *52.230.225.232*:

```
CONTAINER ID        IMAGE                                        COMMAND             STATUS              PORTS
hungry-kirch        mcr.microsoft.com/azuredocs/aci-helloworld                       Running             52.230.225.232:80->80/tcp
```

 Teď v prohlížeči přejdete na IP adresu. Pokud zobrazená webová stránka vypadá přibližně takto, blahopřejeme! Úspěšně jste nasadili aplikaci spuštěnou v kontejneru Dockeru do Azure.

:::image type="content" source="media/quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="Aplikace nasazená pomocí služby Azure Container Instances zobrazená v prohlížeči":::

## <a name="pull-the-container-logs"></a>Vyžádání protokolů kontejneru

Pokud potřebujete odstranit potíže s kontejnerem nebo aplikací, která je v něm spuštěna (nebo pouze zobrazit její výstup), začněte zobrazením protokolů instance kontejneru.

Například spuštěním `docker logs` příkazu zobrazíte protokoly kontejneru *náročné-Kirch* v kontextu ACI:

```azurecli-interactive
docker logs hungry-kirch
```

Ve výstupu se zobrazí protokoly kontejneru a měly by se zobrazit i požadavky HTTP GET, které se vygenerovaly, když jste aplikaci zobrazili v prohlížeči.

```output
listening on port 80
::ffff:10.240.255.55 - - [07/Jul/2020:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [07/Jul/2020:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [07/Jul/2020:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```


## <a name="clean-up-resources"></a>Vyčištění prostředků

Až s kontejnerem skončíte, spusťte `docker rm` ho a odstraňte ho. Tento příkaz zastaví a odstraní instanci kontejneru Azure.

```
docker rm hungry-kirch
```


## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili instanci kontejneru Azure z veřejné image pomocí integrace mezi Docker a Azure. Přečtěte si další informace o scénářích integrace v [dokumentaci k Docker](https://docs.docker.com/engine/context/aci-integration/). 

Můžete také použít [rozšíření Docker pro Visual Studio Code](https://aka.ms/VSCodeDocker) pro integrované prostředí pro vývoj, spouštění a správu kontejnerů, obrázků a kontextů.

Pokud chcete pomocí nástrojů Azure vytvářet a spravovat instance kontejnerů, přečtěte si další rychlé starty pomocí [Azure CLI](container-instances-quickstart.md), [Azure PowerShell](container-instances-quickstart-powershell.md), [Azure Portal](container-instances-quickstart-portal.md)a [šablony Azure Resource Manager](container-instances-quickstart-template.md).

Pokud chcete použít Docker Compose k definování a spuštění aplikace s více kontejnery místně a pak přepnout na Azure Container Instances, pokračujte v tomto kurzu.

> [!div class="nextstepaction"]
> [Kurz Docker Compose](./tutorial-docker-compose.md)

<!-- LINKS - External -->

[azure-account]: https://azure.microsoft.com/free/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

