---
title: 'Kurz: sestavení a spuštění vlastní image v Azure App Service'
description: Podrobný průvodce vytvořením vlastní image systému Linux nebo Windows, nahrajte image do Azure Container Registry a potom ji nasaďte do Azure App Service. Přečtěte si, jak migrovat vlastní software do App Service ve vlastním kontejneru.
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: msangapu
keywords: Azure App Service, Web App, Linux, Windows, Docker, kontejner
ms.custom: devx-track-csharp, mvc, seodec18, devx-track-python, devx-track-azurecli
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: b5682275a9e5f3993de715ab5f23a708d5df47ae
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130098"
---
# <a name="migrate-custom-software-to-azure-app-service-using-a-custom-container"></a>Migrace vlastního softwaru na Azure App Service pomocí vlastního kontejneru

::: zone pivot="container-windows"  

[Azure App Service](overview.md) poskytuje předdefinované zásobníky aplikací ve Windows, jako je ASP.NET nebo Node.js, které běží ve službě IIS. Předkonfigurované prostředí Windows uzamkne přístup k operačnímu systému pro správu a zamezí instalaci softwaru, změnám globální mezipaměti sestavení (GAC) atd. (viz [Funkce operačního systému ve službě Azure App Service](operating-system-functionality.md)). Pomocí vlastního kontejneru Windows ve službě App Service (Preview) ale můžete dělat změny operačního systému, které vaše aplikace potřebuje, takže je snadné migrovat místní aplikaci, která vyžaduje vlastní operační systém a konfiguraci softwaru. Tento kurz ukazuje, jak do služby App Service migrovat aplikaci ASP.NET, která využívá vlastní písma nainstalovaná v knihovně písem Windows. Do služby [Azure Container Registry](../container-registry/index.yml) nasadíte vlastní nakonfigurovanou image Windows ze sady Visual Studio a pak ji spustíte ve službě App Service.

![Zobrazuje webovou aplikaci spuštěnou v kontejneru Windows.](media/tutorial-custom-container/app-running.png)

## <a name="prerequisites"></a>Předpoklady

Pro absolvování tohoto kurzu potřebujete:

- <a href="https://hub.docker.com/" target="_blank">Zaregistrovat si účet Centra Dockeru</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Nainstalujte Docker for Windows</a>.
- <a href="/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Přepnout Docker na spouštění kontejnerů Windows</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Nainstalujte Visual Studio 2019</a> s pracovními procesy pro **vývoj ASP.NET a web** a **vývoj pro Azure** . Pokud jste již nainstalovali Visual Studio 2019:
    - Po **kliknutí na** tlačítko  >  **Vyhledat aktualizace** nainstalujte nejnovější aktualizace v aplikaci Visual Studio.
    - Přidejte úlohy do sady Visual Studio tak, že kliknete na **nástroje**  >  **získat nástroje a funkce** .

## <a name="set-up-the-app-locally"></a>Místní nastavení aplikace

### <a name="download-the-sample"></a>Stažení ukázky

V tomto kroku nastavíte místní projekt .NET.

- [Stáhněte si ukázkový projekt](https://github.com/Azure-Samples/custom-font-win-container/archive/master.zip).
- Rozbalte (rozzipujte) soubor *custom-font-win-container.zip* .

Tento ukázkový projekt obsahuje jednoduchou aplikaci ASP.NET, která využívá vlastní písmo nainstalované do knihovny písem Windows. Písma není potřeba instalovat, jde ale o příklad aplikace, která je integrovaná s podkladovým operačním systémem. Pokud chcete takovou aplikaci migrovat do služby App Service, buď úpravou kódu odeberete tuto integraci, nebo ji migrujete tak jak je ve vlastním kontejneru Windows.

### <a name="install-the-font"></a>Instalace písma

V Průzkumníku Windows přejděte na _custom-font-win-container-master/CustomFontSample_ , klikněte pravým tlačítkem na _FrederickatheGreat-Regular.ttf_ a vyberte **Nainstalovat** .

Toto písmo je veřejně dostupné na webu [Google Fonts](https://fonts.google.com/specimen/Fredericka+the+Great).

### <a name="run-the-app"></a>Spuštění aplikace

Otevřete soubor *custom-font-win-container/CustomFontSample.sln* v sadě Visual Studio. 

Zadáním `Ctrl+F5` spusťte aplikaci bez zapnutého ladění. Aplikace se zobrazí ve vašem výchozím prohlížeči. 

:::image type="content" source="media/tutorial-custom-container/local-app-in-browser.png" alt-text="Snímek obrazovky zobrazující aplikaci zobrazenou ve výchozím prohlížeči":::

Protože tato aplikace používá nainstalované písmo, nemůže běžet v sandboxu služby App Service. Můžete ji ale místo toho nasadit pomocí kontejneru Windows, protože písmo můžete nainstalovat do tohoto kontejneru Windows.

### <a name="configure-windows-container"></a>Konfigurace kontejneru Windows

V Průzkumníku řešení klikněte pravým tlačítkem na projekt **CustomFontSample** a vyberte **Přidat** > **Podpora orchestrace kontejnerů** .

:::image type="content" source="media/tutorial-custom-container/enable-container-orchestration.png" alt-text="Snímek obrazovky zobrazující aplikaci zobrazenou ve výchozím prohlížeči":::

Vyberte **Docker Compose**  >  **OK** .

Projekt je teď nastavený tak, aby běžel v kontejneru Windows. Do projektu **CustomFontSample** se přidá soubor _Dockerfile_ a projekt **docker-compose** se přidá do řešení. 

V Průzkumníku řešení otevřete soubor **Dockerfile** .

Musíte použít [podporovanou nadřazenou image](configure-custom-container.md#supported-parent-images). Nadřazenou image změníte tak, že řádek `FROM` nahradíte následujícím kódem:

```dockerfile
FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
```

Na konec souboru přidejte následující řádek a pak tento soubor uložte:

```dockerfile
RUN ${source:-obj/Docker/publish/InstallFont.ps1}
```

_InstallFont.ps1_ najdete v projektu **CustomFontSample** . Jde o jednoduchý skript, který nainstaluje toto písmo. Složitější verzi tohoto skriptu najdete v [centru skriptů](https://gallery.technet.microsoft.com/scriptcenter/fb742f92-e594-4d0c-8b79-27564c575133).

> [!NOTE]
> Pokud chcete místně testovat kontejner Windows, ujistěte se, že je v místním počítači spuštěný Docker.
>

## <a name="publish-to-azure-container-registry"></a>Publikování do služby Azure Container Registry

Služba [Azure Container Registry](../container-registry/index.yml) uchovává vaše image pro nasazení kontejnerů. Službu App Service můžete nakonfigurovat tak, aby používala image hostované ve službě Azure Container Registry.

### <a name="open-publish-wizard"></a>Otevření průvodce publikováním

V Průzkumníku řešení klikněte pravým tlačítkem na projekt **CustomFontSample** a vyberte **Publikovat** .

:::image type="content" source="media/tutorial-custom-container/open-publish-wizard.png" alt-text="Snímek obrazovky zobrazující aplikaci zobrazenou ve výchozím prohlížeči":::

### <a name="create-registry-and-publish"></a>Vytvoření registru a publikování

V Průvodci publikováním vyberte **Container Registry**  >  **vytvořit nové Azure Container Registry**  >  **publikovat** .

:::image type="content" source="media/tutorial-custom-container/create-registry.png" alt-text="Snímek obrazovky zobrazující aplikaci zobrazenou ve výchozím prohlížeči":::

### <a name="sign-in-with-azure-account"></a>Přihlášení pomocí účtu Azure

V dialogu **Vytvořit nový Azure Container Registry** vyberte **Přidat účet** a přihlaste se ke svému předplatnému Azure. Pokud už jste přihlášeni, vyberte z rozevíracího seznamu účet, který obsahuje požadované předplatné.

![Přihlášení k Azure](./media/tutorial-custom-container/add-an-account.png)

### <a name="configure-the-registry"></a>Konfigurace registru

Nakonfigurujte nový registr kontejneru podle navržených hodnot v následující tabulce. Jakmile budete hotovi, klikněte na **Vytvořit** .

| Nastavení  | Navrhovaná hodnota | Další informace |
| ----------------- | ------------ | ----|
|**Předpona DNS**| Ponechejte vygenerovaný název registru nebo ho změňte na jiný jedinečný název. |  |
|**Skupina prostředků**| Klikněte na **Nový** , zadejte **myResourceGroup** a klikněte na **OK** . |  |
|**Skladová jednotka (SKU)**| Základní | [Cenové úrovně](https://azure.microsoft.com/pricing/details/container-registry/)|
|**Umístění registru**| West Europe | |

![Konfigurace Azure Container Registry](./media/tutorial-custom-container/configure-registry.png)

Otevře se okno terminálu s průběhem nasazování image. Počkejte, až se nasazení dokončí.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

V nabídce vlevo vyberte **vytvořit prostředek**  >  **Web**  >  **Web App for Containers** .

### <a name="configure-app-basics"></a>Konfigurace základních informací o aplikaci

Na kartě **základy** nakonfigurujte nastavení podle následující tabulky a pak klikněte na **Další: Docker** .

| Nastavení  | Navrhovaná hodnota | Další informace |
| ----------------- | ------------ | ----|
|**Předplatné**| Ujistěte se, že je vybráno správné předplatné. |  |
|**Skupina prostředků**| Vyberte **vytvořit nový** , zadejte **myResourceGroup** a klikněte na **OK** . |  |
|**Název**| Zadejte jedinečný název. | Adresa URL webové aplikace je `http://<app-name>.azurewebsites.net`, kde `<app-name>` je název vaší aplikace. |
|**Publikovat**| Kontejner Docker | |
|**Operační systém**| Windows | |
|**Oblast**| West Europe | |
|**Plán Windows**| Vyberte **vytvořit nový** , zadejte **myAppServicePlan** a klikněte na **OK** . | |

Karta **základy** by měla vypadat takto:

![Zobrazuje kartu základy, která slouží ke konfiguraci webové aplikace.](media/tutorial-custom-container/configure-app-basics.png)

### <a name="configure-windows-container"></a>Konfigurace kontejneru Windows

Na kartě **Docker** nakonfigurujte vlastní kontejner Windows, jak je znázorněno v následující tabulce, a vyberte **zkontrolovat + vytvořit** .

| Nastavení  | Navrhovaná hodnota |
| ----------------- | ------------ |
|**Zdroj image**| Azure Container Registry |
|**Registr**| Vyberte [registr, který jste vytvořili dříve](#publish-to-azure-container-registry). |
|**Image**| customfontsample |
|**Tag**| nejnovější |

### <a name="complete-app-creation"></a>Dokončení vytvoření aplikace

Klikněte na **Vytvořit** a počkejte, až Azure vytvoří požadované prostředky.

## <a name="browse-to-the-web-app"></a>Přechod na webovou aplikaci

Po dokončení operace Azure se zobrazí okno s oznámením.

![Ukazuje, že se operace Azure dokončila.](media/tutorial-custom-container/portal-create-finished.png)

1. Klikněte na **Přejít k prostředku** .

2. Na stránce aplikace klikněte na odkaz v části **Adresa URL** .

Na nové stránce prohlížeče se otevře následující stránka:

![Zobrazuje novou stránku prohlížeče pro webovou aplikaci.](media/tutorial-custom-container/app-starting.png)

Počkejte pár minut a zkuste to znovu, dokud se nedostanete na domovskou stránku s písmem, které očekáváte:

![Zobrazuje domovskou stránku s nakonfigurovaným písmem.](media/tutorial-custom-container/app-running.png)

**Blahopřejeme!** Migrovali jste aplikaci ASP.NET do služby Azure App Service v kontejneru Windows.

## <a name="see-container-start-up-logs"></a>Zobrazení protokolů spuštění kontejneru

Načtení kontejneru s Windows může nějakou dobu trvat. Pokud chcete zobrazit průběh, přejděte na následující adresu URL nahrazením *\<app-name>* názvem vaší aplikace.
```
https://<app-name>.scm.azurewebsites.net/api/logstream
```

Streamované protokoly vypadají přibližně takto:

```
14/09/2018 23:16:19.889 INFO - Site: fonts-win-container - Creating container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest.
14/09/2018 23:16:19.928 INFO - Site: fonts-win-container - Create container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest succeeded. Container Id 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:23.405 INFO - Site: fonts-win-container - Start container succeeded. Container: 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Configuring container
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container start-up and configuration completed successfully
```

::: zone-end

::: zone pivot="container-linux"

Azure App Service používá technologii kontejneru Docker k hostování vestavěných imagí i vlastních imagí. Pokud chcete zobrazit seznam předdefinovaných imagí, spusťte příkaz Azure CLI, ["AZ WebApp list-runtimes--Linux"](/cli/azure/webapp?view=azure-cli-latest&preserve-view=true#az-webapp-list-runtimes). Pokud tyto image nevyhovují vašim potřebám, můžete sestavit a nasadit vlastní image.

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

Místo použití klonu Git můžete navštívit [https://github.com/Azure-Samples/docker-django-webapp-linux](https://github.com/Azure-Samples/docker-django-webapp-linux) , vybrat **klonovat** a pak vybrat **Stáhnout ZIP** . 

Rozbalte soubor ZIP do složky s názvem *Docker-Django-WebApp-Linux* . 

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

> [!NOTE]
> Docker Hub má [kvóty pro počet anonymních přijetí změn na jednu IP adresu a počet ověřených přijetí na bezplatného uživatele (viz **přenos dat** )](https://www.docker.com/pricing). Pokud si všimnete, že se vaše přijetí z dokovacího centra bude omezovat, zkuste, jestli ještě nejste `docker login` přihlášení.
> 

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
    > Pokud pracujete v systému Windows a zobrazí se chyba, *standard_init_linux. přejít: 211: exec uživatelský proces způsobil "žádný takový soubor nebo adresář"* , soubor *init.sh* obsahuje konce řádků CR-LF místo očekávaného LF. K této chybě dochází, pokud jste použili Git ke klonování ukázkového úložiště, ale tento parametr se vynechal `--config core.autocrlf=input` . V takovém případě znovu naklonujte úložiště pomocí argumentu--config. Chyba se může zobrazit také v případě, že jste upravili *init.sh* a uložili ji se zakončením CRLF. V takovém případě soubor uložte znovu s končícím LF.

1. Pokud `http://localhost:8000` chcete ověřit, že webová aplikace a kontejner fungují správně, přejděte k části.

    ![Místní test webové aplikace](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

[!INCLUDE [Try Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

V této části a těch, které následují, zřídíte prostředky v Azure, do kterých nahrajete image, a pak nasadíte kontejner, který Azure App Service. Začnete vytvořením skupiny prostředků, ve které se budou shromažďovat všechny tyto prostředky.

Spuštěním příkazu [AZ Group Create](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-create) vytvořte skupinu prostředků:

```azurecli-interactive
az group create --name AppSvc-DockerTutorial-rg --location westus2
```

Můžete změnit `--location` hodnotu a zadat oblast poblíž.

## <a name="push-the-image-to-azure-container-registry"></a>Odeslat obrázek do Azure Container Registry

V této části nahrajete obrázek do Azure Container Registry, ze kterého ho App Service může nasadit.

1. Spuštěním [`az acr create`](/cli/azure/acr?view=azure-cli-latest&preserve-view=true#az-acr-create) příkazu vytvořte Azure Container Registry:

    ```azurecli-interactive
    az acr create --name <registry-name> --resource-group AppSvc-DockerTutorial-rg --sku Basic --admin-enabled true
    ```
    
    Nahraďte `<registry-name>` vhodným názvem pro váš registr. Název musí obsahovat jenom písmena a číslice a musí být jedinečný ve všech Azure.

1. Spusťte [`az acr show`](/cli/azure/acr?view=azure-cli-latest&preserve-view=true#az-acr-show) příkaz pro načtení přihlašovacích údajů registru:

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

1. Pomocí příkazu vytvořte App Service plán [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest&preserve-view=true#az-appservice-plan-create) :

    ```azurecli-interactive
    az appservice plan create --name AppSvc-DockerTutorial-plan --resource-group AppSvc-DockerTutorial-rg --is-linux
    ```

    Plán App Service odpovídá virtuálnímu počítači, který je hostitelem webové aplikace. Ve výchozím nastavení používá předchozí příkaz [cenovou úroveň](https://azure.microsoft.com/pricing/details/app-service/linux/) nelevného B1, která je v prvním měsíci zdarma. Úroveň můžete řídit pomocí `--sku` parametru.

1. Pomocí příkazu vytvořte webovou aplikaci [`az webpp create`](/cli/azure/webapp?view=azure-cli-latest&preserve-view=true#az-webapp-create) :

    ```azurecli-interactive
    az webapp create --resource-group AppSvc-DockerTutorial-rg --plan AppSvc-DockerTutorial-plan --name <app-name> --deployment-container-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```
    
    Nahraďte `<app-name>` názvem webové aplikace, který musí být v rámci všech Azure jedinečný. Nahraďte také `<registry-name>` názvem vašeho registru z předchozí části.

1. Použijte [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest&preserve-view=true#az-webapp-config-appsettings-set) k nastavení `WEBSITES_PORT` proměnné prostředí podle očekávání v kódu aplikace: 

    ```azurecli-interactive
    az webapp config appsettings set --resource-group AppSvc-DockerTutorial-rg --name <app-name> --settings WEBSITES_PORT=8000
    ```

    Nahraďte `<app-name>` názvem, který jste použili v předchozím kroku.
    
    Další informace o této proměnné prostředí najdete v [souboru Readme v úložišti GitHub ukázky](https://github.com/Azure-Samples/docker-django-webapp-linux).

1. Povolte [spravovanou identitu](./overview-managed-identity.md) webové aplikace pomocí [`az webapp identity assign`](/cli/azure/webapp/identity?view=azure-cli-latest&preserve-view=true#az-webapp-identity-assign) příkazu:

    ```azurecli-interactive
    az webapp identity assign --resource-group AppSvc-DockerTutorial-rg --name <app-name> --query principalId --output tsv
    ```

    Nahraďte `<app-name>` názvem, který jste použili v předchozím kroku. Výstupem příkazu (filtrovaný podle `--query` `--output` argumentů a) je instanční objekt přiřazené identity, který využijete krátce.

    Spravovaná identita umožňuje udělit oprávnění k této webové aplikaci pro přístup k jiným prostředkům Azure, aniž by bylo potřeba zadat konkrétní přihlašovací údaje.

1. Pomocí [`az account show`](/cli/azure/account?view=azure-cli-latest&preserve-view=true#az-account-show) příkazu, který budete potřebovat v dalším kroku, načtěte ID vašeho předplatného:

    ```azurecli-interactive
    az account show --query id --output tsv
    ``` 

1. Udělte webové aplikaci oprávnění pro přístup k registru kontejneru:

    ```azurecli-interactive
    az role assignment create --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourceGroups/AppSvc-DockerTutorial-rg/providers/Microsoft.ContainerRegistry/registries/<registry-name> --role "AcrPull"
    ```

    Nahraďte následující hodnoty:
    - `<principal-id>` s ID objektu služby z `az webapp identity assign` příkazu
    - `<registry-name>` s názvem vašeho registru kontejneru
    - `<subscription-id>` s ID předplatného načteným z `az account show` příkazu

Další informace o těchto oprávněních najdete v tématu [co je řízení přístupu na základě role Azure](../role-based-access-control/overview.md) . 

## <a name="deploy-the-image-and-test-the-app"></a>Nasazení image a testování aplikace

Po nahrání image do registru kontejneru můžete tyto kroky dokončit a App Service je plně zřízené.

1. Pomocí [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest&preserve-view=true#az-webapp-config-container-set) příkazu zadejte registr kontejneru a image, která se má nasadit pro webovou aplikaci:

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

1. V místní složce *Docker-Django-WebApp-Linux* otevřete soubor *App/Templates/App/index.html* .

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

    Soubory protokolu můžete také zkontrolovat v prohlížeči na `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

1. Pokud chcete streamování protokolů kdykoli zastavit, zadejte **CTRL** + **C** .

## <a name="connect-to-the-container-using-ssh"></a>Připojit se ke kontejneru pomocí SSH

SSH umožňuje zabezpečenou komunikaci mezi kontejnerem a klientem. Pokud chcete povolit připojení SSH ke kontejneru, musíte pro něj nakonfigurovat vlastní image. Po spuštění kontejneru můžete otevřít připojení SSH.

### <a name="configure-the-container-for-ssh"></a>Konfigurace kontejneru pro SSH

Ukázková aplikace použitá v tomto kurzu už má v *souboru Dockerfile* potřebnou konfiguraci, která nainstaluje server SSH a také nastaví přihlašovací údaje. Tato část je jenom informativní. Pokud se chcete připojit ke kontejneru, přejděte k další části.

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

Nakonec skript vstupu *init.sh* SPUSTÍ Server SSH.

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

::: zone-end

## <a name="next-steps"></a>Další kroky

Naučili jste se:

> [!div class="checklist"]
> * Nasazení vlastní image do privátního registru kontejnerů
> * Nasazení a vlastní image v App Service
::: zone pivot="container-linux"
> * Aktualizace a opětovné nasazení image
::: zone-end
> * Přístup k diagnostickým protokolům
::: zone pivot="container-linux"
> * Připojit se ke kontejneru pomocí SSH
::: zone-end

V dalším kurzu se dozvíte, jak namapovat vlastní název DNS na svou aplikaci.

> [!div class="nextstepaction"]
> [Kurz: mapování vlastního názvu DNS na aplikaci](app-service-web-tutorial-custom-domain.md)

Nebo si prohlédněte další zdroje informací:

> [!div class="nextstepaction"]
> [Konfigurace vlastního kontejneru](configure-custom-container.md)

::: zone pivot="container-linux"
> [!div class="nextstepaction"]
> [Kurz: aplikace pro více kontejnerů WordPress](tutorial-multi-container-app.md)
::: zone-end
