---
title: Rychlý Start – nasazení kontejneru Docker do kontejneru rozhraní příkazového řádku Docker instance
description: V tomto rychlém startu použijete Docker CLI k rychlému nasazení kontejnerové webové aplikace, která běží v izolované instanci kontejneru Azure.
ms.topic: quickstart
ms.date: 07/16/2020
ms.custom: ''
ms.openlocfilehash: 684bb3537d26b60afb0fc0796a94e1a134aa50c8
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87101998"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-docker-cli"></a>Rychlý Start: nasazení instance kontejneru v Azure pomocí Docker CLI

Použijte Azure Container Instances ke spouštění kontejnerů Docker bez serveru v Azure s využitím jednoduchosti a rychlosti. Nasazení do instance kontejneru na vyžádání při vývoji nativních aplikací cloudu a chcete hladce přepnout z místního vývoje na nasazení do cloudu.

V tomto rychlém startu použijete nativní příkazy rozhraní příkazového řádku Docker pro nasazení kontejneru Docker a zpřístupnění jeho aplikace v Azure Container Instances. Tato funkce je povolená [integrací mezi Docker a Azure](https://docs.docker.com/engine/context/aci-integration/) (beta). Několik sekund po provedení `docker run` příkazu můžete přejít na aplikaci spuštěnou v kontejneru:

:::image type="content" source="media/container-instances-quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="Aplikace nasazená pomocí služby Azure Container Instances zobrazená v prohlížeči":::

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet][azure-account], ještě než začnete.

V tomto rychlém startu je potřeba nainstalovat Docker Desktop Edge verze 2.3.2.0 nebo novější, který je k dispozici pro [Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) nebo [MacOS](https://desktop.docker.com/mac/edge/Docker.dmg). Nebo nainstalujte rozhraní příkazového [řádku Docker ACI Integration CLI pro Linux](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux) (beta verze). 

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview a vyžaduje funkce beta verze (Preview) v Docker. Přečtěte si další informace o [stabilních a hraničních verzích Docker desktopu](https://docs.docker.com/desktop/#stable-and-edge-versions). Ne všechny funkce Azure Container Instances jsou podporovány. Poskytněte zpětnou vazbu o integraci Docker – Azure vytvořením problému v úložišti GitHub [ACI-Integration-beta](https://github.com/docker/aci-integration-beta) .

## <a name="create-azure-context"></a>Vytvořit kontext Azure

Pokud chcete použít příkazy Docker ke spuštění kontejnerů v Azure Container Instances, nejdřív se přihlaste do Azure:

```bash
docker login azure
```

Po zobrazení výzvy zadejte nebo vyberte svoje přihlašovací údaje Azure.


Spusťte `docker context create aci` pro vytvoření kontextu ACI. Tento kontext přidruží Docker ke svému předplatnému Azure, abyste mohli vytvářet instance kontejnerů. Například pro vytvoření kontextu s názvem *myacicontext*:

```
docker context create aci myacicontext
```

Po zobrazení výzvy vyberte ID vašeho předplatného Azure a pak vyberte existující skupinu prostředků nebo **vytvořte novou skupinu prostředků**. Pokud zvolíte novou skupinu prostředků, vytvoří se se systémem generovaným názvem. Instance kontejnerů Azure, stejně jako všechny prostředky Azure, se musí nasadit do skupiny prostředků. Skupiny prostředků vám umožňují organizaci a správu souvisejících prostředků Azure.


Spusťte `docker context ls` a potvrďte, že jste přidali kontext ACI do kontextů Docker:

```
docker context ls
```

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

:::image type="content" source="media/container-instances-quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="Aplikace nasazená pomocí služby Azure Container Instances zobrazená v prohlížeči":::

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

Můžete také použít [rozšíření Docker](https://aka.ms/VSCodeDocker) pro Visual Studio Code pro integrované prostředí pro vývoj, spouštění a správu kontejnerů, obrázků a kontextů.

Pokud chcete pomocí nástrojů Azure vytvářet a spravovat instance kontejnerů, přečtěte si další rychlé starty pomocí [Azure CLI](container-instances-quickstart.md), [Azure PowerShell](container-instances-quickstart-powershell.md), [Azure Portal](container-instances-quickstart-portal.md)a [šablony Azure Resource Manager](container-instances-quickstart-template.md).

Pokud si chcete sestavit image kontejneru a nasadit ji z privátního registru kontejnerů Azure, pokračujte ke kurzu služby Azure Container Instances.

> [!div class="nextstepaction"]
> [Kurz služby Azure Container Instances](./container-instances-tutorial-prepare-app.md)

<!-- LINKS - External -->

[azure-account]: https://azure.microsoft.com/free/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

