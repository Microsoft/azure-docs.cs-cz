---
title: 'Kurz: sestavení a spuštění vlastní image v Azure App Service'
description: Podrobný průvodce vytvořením vlastní image pro Linux, vložením image do Azure Container Registry a následným nasazením této image do Azure App Service.
keywords: azure app service, web app, linux, docker, container
author: msangapu-msft
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-python
ms.openlocfilehash: 1c1313c7333c20dac5e581025a571c6a733dafc6
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2020
ms.locfileid: "87850895"
---
# <a name="tutorial-run-a-custom-docker-image-in-app-service"></a>Kurz: spuštění vlastní image Docker v App Service

Azure App Service používá technologii kontejneru Docker k hostování vestavěných imagí i vlastních imagí. Pokud chcete zobrazit seznam předdefinovaných imagí, spusťte příkaz Azure CLI, ["AZ WebApp list-runtimes--Linux"](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes). Pokud tyto image nevyhovují vašim potřebám, můžete sestavit a nasadit vlastní image.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření vlastní image, pokud žádný vestavěný obrázek nevyhovuje vašim potřebám
> * Vložení vlastní image do privátního registru kontejnerů v Azure
> * Spuštění vlastní image v App Service
> * Konfigurace proměnných prostředí
> * Aktualizace a opětovné nasazení image
> * Přístup k diagnostickým protokolům
> * Připojit se ke kontejneru pomocí SSH

V tomto kurzu vznikne v rámci vašeho účtu Azure v registru kontejneru malý poplatek a může se stát, že se za hostování kontejneru dokončí déle než měsíc.

## <a name="set-up-your-initial-environment"></a>Nastavení počátečního prostředí

* Mít účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Nainstalujte [Docker](https://docs.docker.com/get-started/#setup), který použijete k sestavení imagí Docker. Instalace Docker může vyžadovat restart počítače.
* Nainstalujte rozhraní příkazového <a href="/cli/azure/install-azure-cli" target="_blank">řádku Azure CLI</a> 2.0.80 nebo novější, ve kterém spouštíte příkazy v jakémkoli prostředí pro zřizování a konfiguraci prostředků Azure.

Po instalaci Docker a rozhraní příkazového řádku Azure otevřete okno terminálu a ověřte, že je Docker nainstalovaný:

```bash
docker --version
```

Ověřte také, že verze Azure CLI je 2.0.80 nebo vyšší:

```azurecli
az --version
```

Pak se přihlaste k Azure prostřednictvím rozhraní příkazového řádku:

```azurecli
az login
```

`az login`Příkaz otevře prohlížeč pro shromáždění vašich přihlašovacích údajů. Po dokončení příkazu se zobrazí výstup JSON obsahující informace o vašich předplatných.

Po přihlášení můžete spustit příkazy Azure pomocí Azure CLI a pracovat s prostředky ve vašem předplatném.

## <a name="clone-or-download-the-sample-app"></a>Klonování nebo stažení ukázkové aplikace

Ukázku pro tento kurz můžete získat pomocí klonování Git nebo stažení.

### <a name="clone-with-git"></a>Klonovat pomocí Gitu

Naklonujte ukázkové úložiště:

```terminal
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
```

Nezapomeňte zahrnout `--config core.autocrlf=input` argument pro zaručení správných konců řádků v souborech, které se používají v kontejneru Linux:

Pak přejít do této složky:

```terminal
cd docker-django-webapp-linux
```

### <a name="download-from-github"></a>Stažení z GitHubu

Místo použití klonu Git můžete navštívit [https://github.com/Azure-Samples/docker-django-webapp-linux](https://github.com/Azure-Samples/docker-django-webapp-linux) , vybrat **klonovat**a pak vybrat **Stáhnout ZIP**. 

Rozbalte soubor ZIP do složky s názvem *Docker-Django-WebApp-Linux*. 

Pak otevřete okno terminálu v této složce *Docker-Django-WebApp-Linux* .

## <a name="optional-examine-the-docker-file"></a>Volitelné Kontrola souboru Docker

Soubor v ukázce s názvem _souboru Dockerfile_ , který popisuje bitovou kopii Docker a obsahuje pokyny ke konfiguraci:

```Dockerfile
FROM tiangolo/uwsgi-nginx-flask:python3.6

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt --no-cache-dir
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222

#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

* První skupina příkazů nainstaluje požadavky aplikace do prostředí.
* Druhá skupina příkazů vytvoří server [SSH](https://www.ssh.com/ssh/protocol/) pro zabezpečenou komunikaci mezi kontejnerem a hostitelem.
* Poslední řádek, `ENTRYPOINT ["init.sh"]` , vyvolá `init.sh` spuštění služby SSH a serveru Python.

## <a name="build-and-test-the-image-locally"></a>Místní sestavení a otestování image

1. Spusťte následující příkaz, který sestaví bitovou kopii:

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```
    
1. Otestujte, jestli sestavení funguje, spuštěním kontejneru Docker v místním počítači:

    ```bash
    docker run -p 8000:8000 appsvc-tutorial-custom-image
    ```
    
    Tento [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) příkaz určuje port s `-p` argumentem následovaným názvem obrázku. 
    
    > [!TIP]
    > Pokud pracujete v systému Windows a zobrazí se chyba, *standard_init_linux. přejít: 211: exec uživatelský proces způsobil "žádný takový soubor nebo adresář"*, soubor *init.sh* obsahuje konce řádků CR-LF místo očekávaného LF. K této chybě dochází, pokud jste použili Git ke klonování ukázkového úložiště, ale tento parametr se vynechal `--config core.autocrlf=input` . V takovém případě znovu naklonujte úložiště pomocí argumentu--config. Chyba se může zobrazit také v případě, že jste upravili *init.sh* a uložili ji se zakončením CRLF. V takovém případě soubor uložte znovu s končícím LF.

1. Pokud `http://localhost:8000` chcete ověřit, že webová aplikace a kontejner fungují správně, přejděte k části.

    ![Místní test webové aplikace](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

V této části a těch, které následují, zřídíte prostředky v Azure, do kterých nahrajete image, a pak nasadíte kontejner, který Azure App Service. Začnete vytvořením skupiny prostředků, ve které se budou shromažďovat všechny tyto prostředky.

Spuštěním příkazu [AZ Group Create](/cli/azure/group?view=azure-cli-latest#az-group-create) vytvořte skupinu prostředků:

```azurecli-interactive
az group create --name AppSvc-DockerTutorial-rg --location westus2
```

Můžete změnit `--location` hodnotu a zadat oblast poblíž.

## <a name="push-the-image-to-azure-container-registry"></a>Odeslat obrázek do Azure Container Registry

V této části nahrajete obrázek do Azure Container Registry, ze kterého ho App Service může nasadit.

1. Spuštěním [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create) příkazu vytvořte Azure Container Registry:

    ```azurecli-interactive
    az acr create --name <registry-name> --resource-group AppSvc-DockerTutorial-rg --sku Basic --admin-enabled true
    ```
    
    Nahraďte `<registry-name>` vhodným názvem pro váš registr. Název musí obsahovat jenom písmena a číslice a musí být jedinečný ve všech Azure.

1. Spusťte [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az-acr-show) příkaz pro načtení přihlašovacích údajů registru:

    ```azurecli-interactive
    az acr credential show --resource-group AppSvc-DockerTutorial-rg --name <registry-name>
    ```
    
    Výstup JSON tohoto příkazu poskytuje dvě hesla spolu s uživatelským jménem registru.
    
1. `docker login`K přihlášení do registru kontejneru použijte příkaz:

    ```bash
    docker login <registry-name>.azurecr.io --username <registry-username>
    ```
    
    `<registry-name>`Hodnoty a nahraďte `<registry-username>` hodnotami z předchozích kroků. Po zobrazení výzvy zadejte jedno z hesel z předchozího kroku.

    Stejný název registru použijete ve všech zbývajících krocích této části.

1. Po úspěšném přihlášení označte místní image Docker pro registr:

    ```bash
   docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```    

1. Pomocí příkazu nahrajte `docker push` image do registru:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    Nahrává se obrázek poprvé může trvat několik minut, protože zahrnuje základní image. Další nahrávání jsou obvykle rychlejší.

    Až budete čekat, můžete dokončit kroky v další části a nakonfigurovat App Service k nasazení z registru.

1. Pomocí `az acr repository list` příkazu ověřte, že byla nabízená oznámení úspěšná:

    ```azurecli-interactive
    az acr repository list -n <registry-name>
    ```
    
    Výstup by měl zobrazovat název obrázku.


## <a name="configure-app-service-to-deploy-the-image-from-the-registry"></a>Konfigurace App Service k nasazení bitové kopie z registru

Chcete-li nasadit kontejner pro Azure App Service, nejprve vytvořte webovou aplikaci v App Service a pak připojte webovou aplikaci k registru kontejneru. Po spuštění webové aplikace App Service automaticky načítat image z registru.

1. Pomocí příkazu vytvořte App Service plán [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) :

    ```azurecli-interactive
    az appservice plan create --name AppSvc-DockerTutorial-plan --resource-group AppSvc-DockerTutorial-rg --is-linux
    ```

    Plán App Service odpovídá virtuálnímu počítači, který je hostitelem webové aplikace. Ve výchozím nastavení používá předchozí příkaz [cenovou úroveň](https://azure.microsoft.com/pricing/details/app-service/linux/) nelevného B1, která je v prvním měsíci zdarma. Úroveň můžete řídit pomocí `--sku` parametru.

1. Pomocí příkazu vytvořte webovou aplikaci [`az webpp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) :

    ```azurecli-interactive
    az webapp create --resource-group AppSvc-DockerTutorial-rg --plan AppSvc-DockerTutorial-plan --name <app-name> --deployment-container-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```
    
    Nahraďte `<app-name>` názvem webové aplikace, který musí být v rámci všech Azure jedinečný. Nahraďte také `<registry-name>` názvem vašeho registru z předchozí části.

1. Použijte [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) k nastavení `WEBSITES_PORT` proměnné prostředí podle očekávání v kódu aplikace: 

    ```azurecli-interactive
    az webapp config appsettings set --resource-group AppSvc-DockerTutorial-rg --name <app-name> --settings WEBSITES_PORT=8000
    ```

    Nahraďte `<app-name>` názvem, který jste použili v předchozím kroku.
    
    Další informace o této proměnné prostředí najdete v [souboru Readme v úložišti GitHub ukázky](https://github.com/Azure-Samples/docker-django-webapp-linux).

1. Povolte [spravovanou identitu](/azure/app-service/overview-managed-identity) webové aplikace pomocí [`az webapp identity assign`](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) příkazu:

    ```azurecli-interactive
    az webapp identity assign --resource-group AppSvc-DockerTutorial-rg --name <app-name> --query principalId --output tsv
    ```

    Nahraďte `<app-name>` názvem, který jste použili v předchozím kroku. Výstupem příkazu (filtrovaný podle `--query` `--output` argumentů a) je instanční objekt přiřazené identity, který využijete krátce.

    Spravovaná identita umožňuje udělit oprávnění k této webové aplikaci pro přístup k jiným prostředkům Azure, aniž by bylo potřeba zadat konkrétní přihlašovací údaje.

1. Pomocí [`az account show`](/cli/azure/account?view=azure-cli-latest#az-account-show) příkazu, který budete potřebovat v dalším kroku, načtěte ID vašeho předplatného:

    ```azurecli-interactive
    az account show --query id --output tsv
    ``` 

1. Udělte webové aplikaci oprávnění pro přístup k registru kontejneru:

    ```azurecli-interactive
    az role assignment create --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourceGroups/AppSvc-DockerTutorial-rg/providers/Microsoft.ContainerRegistry/registries/<registry-name> --role "AcrPull"
    ```

    Nahraďte následující hodnoty:
    - `<principal-id>`s ID objektu služby z `az webapp identity assign` příkazu
    - `<registry-name>`s názvem vašeho registru kontejneru
    - `<subscription-id>`s ID předplatného načteným z `az account show` příkazu

Další informace o těchto oprávněních najdete v tématu [co je řízení přístupu na základě role Azure](/azure/role-based-access-control/overview) . 

## <a name="deploy-the-image-and-test-the-app"></a>Nasazení image a testování aplikace

Po nahrání image do registru kontejneru můžete tyto kroky dokončit a App Service je plně zřízené.

1. Pomocí [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) příkazu zadejte registr kontejneru a image, která se má nasadit pro webovou aplikaci:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-custom-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest --docker-registry-server-url https://<registry-name>.azurecr.io
    ```
    
    Nahraďte `<app_name>` názvem vaší webové aplikace a nahraďte je `<registry-name>` na dvou místech názvem svého registru. 

    - Pokud používáte jiný registr než Docker Hub (jak ukazuje tento příklad), `--docker-registry-server-url` musí být formátovaný jako `https://` následovaný plně kvalifikovaným názvem domény registru.
    - Zpráva: pro přístup k Azure Container Registry nebyla poskytnuta žádná pověření. Probíhá pokus o vyhledání... " oznamuje vám, že Azure používá spravovanou identitu aplikace k ověřování pomocí registru kontejnerů, ale nedotazuje se na uživatelské jméno a heslo.
    - Pokud dojde k chybě, objekt "AttributeError: ' NoneType ' neobsahuje atribut ' rezervovaný ', ujistěte se, že `<app-name>` je správný.

    > [!TIP]
    > Nastavení kontejneru webové aplikace můžete načíst kdykoli pomocí příkazu `az webapp config container show --name <app-name> --resource-group AppSvc-DockerTutorial-rg` . Obrázek je zadán ve vlastnosti `DOCKER_CUSTOM_IMAGE_NAME` . Při nasazení webové aplikace prostřednictvím šablon Azure DevOps nebo Azure Resource Manager se obrázek může zobrazit také ve vlastnosti s názvem `LinuxFxVersion` . Obě vlastnosti slouží ke stejnému účelu. Pokud je v konfiguraci webové aplikace přítomen obojí, `LinuxFxVersion` má přednost.

1. Po `az webapp config container set` dokončení příkazu by měla být webová aplikace spuštěná v kontejneru na App Service.

    K otestování aplikace přejděte na adresu `http://<app-name>.azurewebsites.net` a nahraďte `<app-name>` názvem vaší webové aplikace. Při prvním přístupu může aplikace nějakou dobu trvat, protože App Service musí z registru přebírat celou image. Pokud vyprší časový limit prohlížeče, stačí stránku aktualizovat. Po obnovení počáteční image se budou další testy spouštět mnohem rychleji.

    ![Úspěšný test webové aplikace v Azure](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="modify-the-app-code-and-redeploy"></a>Úprava kódu aplikace a opětovné nasazení

V této části provedete změnu kódu webové aplikace, znovu sestavíte kontejner a potom kontejner nahrajete do registru. App Service pak automaticky načte aktualizovanou bitovou kopii z registru, aby se aktualizovala spuštěná webová aplikace.

1. V místní složce *Docker-Django-WebApp-Linux* otevřete soubor *App/Templates/App/index.html*.

1. Změňte první prvek HTML tak, aby odpovídal následujícímu kódu.

    ```html
    <nav class="navbar navbar-inverse navbar-fixed-top">
      <div class="container">
        <div class="navbar-header">
          <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>
        </div>
      </div>
    </nav>
    ```
    
1. Uložte provedené změny.

1. Přejděte do složky *Docker-Django-WebApp-Linux* a znovu sestavte bitovou kopii:

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```

1. Aktualizujte číslo verze ve značce obrázku na verzi v 1.0.1:

    ```bash
    docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    `<registry-name>` nahraďte názvem vašeho registru.

1. Odeslat image do registru:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

1. Restartujte webovou aplikaci:

    ```azurecli-interactive
    az webapp restart --name <app_name> --resource-group AppSvc-DockerTutorial-rg
    ```

    `<app_name>` nahraďte názvem vaší webové aplikace. Po restartování App Service načte aktualizovanou bitovou kopii z registru kontejneru.

1. Ověřte nasazení aktualizace tak, že přejdete na adresu `http://<app-name>.azurewebsites.net` .

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

1. Zapnout protokolování kontejneru:

    ```azurecli-interactive
    az webapp log config --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-container-logging filesystem
    ```
    
1. Povolte datový proud protokolu:

    ```azurecli-interactive
    az webapp log tail --name <app-name> --resource-group AppSvc-DockerTutorial-rg
    ```
    
    Pokud nevidíte protokoly konzoly okamžitě, podívejte se znovu za 30 sekund.

    Soubory protokolu můžete také zkontrolovat v prohlížeči na adrese `https://<app-name>.scm.azurewebsites.net/api/logs/docker` .

1. Pokud chcete streamování protokolů kdykoli zastavit, zadejte **CTRL** + **C**.

## <a name="connect-to-the-container-using-ssh"></a>Připojit se ke kontejneru pomocí SSH

SSH umožňuje zabezpečenou komunikaci mezi kontejnerem a klientem. Pokud chcete povolit připojení SSH ke kontejneru, musíte pro něj nakonfigurovat vlastní image. Po spuštění kontejneru můžete otevřít připojení SSH.

### <a name="configure-the-container-for-ssh"></a>Konfigurace kontejneru pro SSH

Ukázková aplikace použitá v tomto kurzu už má v *souboru Dockerfile*potřebnou konfiguraci, která nainstaluje server SSH a také nastaví přihlašovací údaje. Tato část je jenom informativní. Pokud se chcete připojit ke kontejneru, přejděte k další části.

```Dockerfile
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
  && apt-get install -y --no-install-recommends openssh-server \
  && echo "$SSH_PASSWD" | chpasswd 
```

> [!NOTE]
> Tato konfigurace neumožňuje externí připojení ke kontejneru. SSH je k dispozici pouze prostřednictvím webu Kudu/SCM. Web Kudu/SCM se ověřuje pomocí vašeho účtu Azure.

*Souboru Dockerfile* také zkopíruje soubor *sshd_config* do složky */etc/ssh/* a zpřístupní port 2222 na kontejneru:

```Dockerfile
COPY sshd_config /etc/ssh/

# ...

EXPOSE 8000 2222
```

Port 2222 je interní port přístupný pouze pro kontejnery v rámci mostu sítě privátní virtuální sítě. 

Nakonec skript vstupu *init.sh*SPUSTÍ Server SSH.

```bash
#!/bin/bash
service ssh start
```

### <a name="open-ssh-connection-to-container"></a>Otevření připojení SSH ke kontejneru

1. Přejděte na `https://<app-name>.scm.azurewebsites.net/webssh/host` účet Azure a přihlaste se k němu. `<app-name>` nahraďte názvem vaší webové aplikace.

1. Jakmile se přihlásíte, budete přesměrováni na informační stránku webové aplikace. V horní části stránky vyberte **SSH** a otevřete prostředí a použijte příkazy.

    Například můžete prozkoumávat procesy běžící v rámci pomocí `top` příkazu.
    
## <a name="clean-up-resources"></a>Vyčištění prostředků

Prostředky, které jste vytvořili v tomto článku, můžou účtovat průběžné náklady. k vyčištění prostředků je potřeba jenom odstranit skupinu prostředků, která je obsahuje:

```azurecli
az group delete --name AppSvc-DockerTutorial-rg
```

## <a name="next-steps"></a>Další kroky

Naučili jste se:

> [!div class="checklist"]
> * Nasazení vlastní image do privátního registru kontejnerů
> * Nasazení a vlastní image v App Service
> * Aktualizace a opětovné nasazení image
> * Přístup k diagnostickým protokolům
> * Připojit se ke kontejneru pomocí SSH

V dalším kurzu se dozvíte, jak namapovat vlastní název DNS na svou aplikaci.

> [!div class="nextstepaction"]
> [Kurz: mapování vlastního názvu DNS na aplikaci](../app-service-web-tutorial-custom-domain.md)

Nebo si prohlédněte další zdroje informací:

> [!div class="nextstepaction"]
> [Konfigurace vlastního kontejneru](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Kurz: aplikace pro více kontejnerů WordPress](tutorial-multi-container-app.md)
