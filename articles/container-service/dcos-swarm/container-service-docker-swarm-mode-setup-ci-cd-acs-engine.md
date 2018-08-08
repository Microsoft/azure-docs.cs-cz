---
title: CI/CD s modul Azure Container Service a režim Swarm
description: Použít modul Azure Container Service pomocí režimu Docker Swarm, Azure Container Registry a Visual Studio Team Services poskytovat průběžně vícekontejnerová aplikace .NET Core
services: container-service
author: diegomrtnzg
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/27/2017
ms.author: diegomrtnzg
ms.custom: mvc
ms.openlocfilehash: 6c41156383791fb7d72ac02dae919a25a0d15c84
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621058"
---
# <a name="full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-visual-studio-team-services"></a>Úplný kanál CI/CD pro nasazení aplikace ve službě Azure Container Service s modulem ACS a režimu Docker Swarm pomocí Visual Studio Team Services

*Tento článek je založen na [úplné kanály CI/CD pro nasazení aplikace ve službě Azure Container Service pomocí nástroje Docker Swarm pomocí Visual Studio Team Services](container-service-docker-swarm-setup-ci-cd.md) dokumentace*

V současné době jedním z největších problémů při vývoji moderních aplikací pro cloud je schopnost průběžné nasazení těchto aplikací. V tomto článku se dozvíte, jak k provádění úplné průběžné integrace a nasazování (CI/CD) kanálu pomocí: 
* Modul Azure Container Service s režimem Docker Swarm
* Azure Container Registry
* Visual Studio Team Services

Tento článek je založen na jednoduchou aplikaci k dispozici na [Githubu](https://github.com/jcorioland/MyShop/tree/docker-linux), vyvinuté pomocí ASP.NET Core. Aplikace se skládá ze čtyř různých služeb: tři webové rozhraní API a jednoho webového front-endu:

![MyShop ukázkové aplikace](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

Cílem je průběžné nasazení této aplikace v režimu Docker Swarm clusteru pomocí sady Visual Studio Team Services. Následující obrázek podrobně popisuje tento kanál průběžného doručování:

![MyShop ukázkové aplikace](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

Tady je stručný postup:

1. Změny kódu se zaměřuje na úložiště zdrojového kódu (tady Githubu) 
2. GitHub aktivuje sestavení v sadě Visual Studio Team Services 
3. Visual Studio Team Services získá nejnovější verzi zdroje a sestaví všechny Image, které tvoří aplikaci 
4. Visual Studio Team Services předá každé image do registru Dockeru vytvořené pomocí služby Azure Container Registry 
5. Visual Studio Team Services se aktivuje nové vydané verze 
6. Běží na verzi některé příkazy pomocí SSH na hlavní uzel clusteru Azure container service 
7. Režimu docker Swarm v clusteru si vyžádá nejnovější verzi Image 
8. Nová verze aplikace se nasadí pomocí Docker Stack 

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu, budete muset provést následující úlohy:

- [Vytvořit cluster v režimu Swarm ve službě Azure Container Service s modulem ACS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Propojení s clusterem Swarm ve službě Azure Container Service](../container-service-connect.md)
- [Vytvoření služby Azure container registry](../../container-registry/container-registry-get-started-portal.md)
- [Visual Studio Team Services účtu a týmový projekt, vytvořili jste](https://docs.microsoft.com/vsts/organizations/accounts/create-organization-msa-or-work-student)
- [Rozvětvení úložiště GitHub do účtu Githubu](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> Orchestrátor Docker Swarm v Azure Container Service používá starší verzi samostatného Swarmu. Integrovaný [režim Swarm](https://docs.docker.com/engine/swarm/) (v Dockeru 1.12 a novějším) aktuálně není podporovaným orchestrátorem v Azure Container Service. Z tohoto důvodu používáme [modul ACS](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md), komunitou [šablonu pro rychlý Start](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/), nebo řešení Dockeru v [Azure Marketplace](https://azuremarketplace.microsoft.com).
>

## <a name="step-1-configure-your-visual-studio-team-services-account"></a>Krok 1: Konfigurace účtu služby Visual Studio Team Services 

V této části nakonfigurujete svůj účet Visual Studio Team Services. Ke konfiguraci koncových bodů služby VSTS, v projektu Visual Studio Team Services, klikněte na tlačítko **nastavení** v panelu nástrojů a vyberte ikonu **služby**.

![Otevřít služby koncového bodu](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-visual-studio-team-services-and-azure-account"></a>Připojení účtu služby Visual Studio Team Services a Azure

Nastavte připojení mezi projektu VSTS a účtu Azure.

1. Na levé straně klikněte na tlačítko **nový koncový bod služby** > **Azure Resource Manageru**.
2. Chcete-li povolit VSTS pro práci se svým účtem Azure, vyberte vaše **předplatné** a klikněte na tlačítko **OK**.

    ![Autorizovat aplikaci Visual Studio Team Services – Azure](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-visual-studio-team-services-and-github"></a>Připojení sady Visual Studio Team Services a Githubu

Nastavte připojení mezi projektu VSTS a účtu GitHub.

1. Na levé straně klikněte na tlačítko **nový koncový bod služby** > **Githubu**.
2. K autorizaci VSTS tak může fungovat s vaším účtem Githubu, klikněte na tlačítko **Authorize** a postupujte podle pokynů v okně, které se otevře.

    ![Visual Studio Team Services - povolit Githubu](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-vsts-to-your-azure-container-service-cluster"></a>Propojit služby VSTS do clusteru Azure Container Service

Poslední kroky před získáním do kanálu CI/CD se konfigurovat externí připojení ke clusteru Docker Swarm v Azure. 

1. Pro cluster Docker Swarm přidat koncový bod typu **SSH**. Zadejte informace o připojení SSH clusteru Swarm (hlavní uzly).

    ![Visual Studio Team Services - SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

Všechny konfigurace se teď provádí. V dalších krocích vytvoříte kanál CI/CD, který vytvoří a nasadí aplikaci do clusteru Docker Swarm. 

## <a name="step-2-create-the-build-definition"></a>Krok 2: Vytvoření definice sestavení

V tomto kroku nastavíte definici sestavení pro projekt VSTS a definice pracovního postupu sestavení imagí kontejnerů

### <a name="initial-definition-setup"></a>Nastavení počáteční definice

1. Chcete-li vytvořit definici sestavení, připojte se k projektu Visual Studio Team Services a klikněte na **sestavení a vydání**. V **definice sestavení** klikněte na tlačítko **+ nová**. 

    ![Visual Studio Team Services – nové definice sestavení](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. Vyberte **prázdný proces**.

    ![Visual Studio Team Services – nová prázdná definice sestavení](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. Klikněte **proměnné** kartu a vytvořit dvě nové proměnné: **RegistryURL** a **AgentURL**. Vložte hodnoty registru a DNS agentů clusteru.

    ![Visual Studio Team Services - proměnné konfigurace sestavení](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. Na **definice sestavení** otevřete stránku **triggery** kartu a konfiguraci sestavení pro průběžnou integraci pomocí forku MyShop projekt, který jste vytvořili v rámci požadavků. Vyberte **Batch změny**. Ujistěte se, že jste vybrali *docker-linux* jako **větev specifikace**.

    ![Visual Studio Team Services – úložiště konfigurace sestavení](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. Nakonec klikněte na tlačítko **možnosti** kartu a nakonfigurovat výchozí fronta agenta k **hostované Linuxové verze Preview**.

    ![Visual Studio Team Services - Host Agent Configuration](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>Definování pracovního postupu sestavení
Další kroky definování pracovního postupu sestavení. Nejdřív musíte nakonfigurovat zdrojový kód. Provedete to výběrem **Githubu** a **úložiště** a **větev** (docker-linux).

![Visual Studio Team Services – konfigurace zdrojového kódu](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

Existuje pět imagí kontejnerů k sestavení pro *MyShop* aplikace. Každá image se sestavuje pomocí souboru Dockerfile umístěný ve složce projektu:

* ProductsApi
* Proxy server
* RatingsApi
* RecommandationsApi
* ShopFront

Budete potřebovat dva kroky Dockeru pro každé bitové kopie, jeden pro sestavení image a jeden nasdílejte image do registru kontejneru Azure. 

1. Chcete-li přidat krok v pracovním postupu sestavení, klikněte na tlačítko **+ přidat krok sestavení** a vyberte **Docker**.

    ![Visual Studio Team Services – přidání kroků sestavení](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. Pro každý obrázek nakonfigurovat jeden krok, který používá `docker build` příkazu.

    ![Sestavení sady Visual Studio Team Services - Dockeru](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    Pro operaci sestavení, vyberte služby Azure Container Registry **sestavte image** akce a soubor Dockerfile, který definuje každá image. Nastavte **pracovní adresář** jako kořenový adresář souboru Dockerfile, definujte **název Image**a vyberte **zahrnout nejnovější značky**.
    
    Název Image musí být v tomto formátu: ```$(RegistryURL)/[NAME]:$(Build.BuildId)```. Nahraďte **[název]** s názvem obrázku:
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. Každé bitové kopie, nakonfigurujte druhý krok, který používá `docker push` příkazu.

    ![Visual Studio Team Services – Docker Push](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    Pomocí operace push, vyberte svůj registr kontejneru Azure **nasdílet image** akce, zadejte **název Image** , která je vytvořená v předchozím kroku a vyberte **zahrnout nejnovější značka**.

4. Po dokončení kroků sestavení a odeslání konfigurace pro každý z pěti obrázků, přidejte tři další kroky v postupu sestavení.

   ![Visual Studio Team Services – přidání úlohy pomocí příkazového řádku](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

      1. Úlohu příkazového řádku pomocí skriptu bash nahrazuje *RegistryURL* výskytu v souboru docker-compose.yml RegistryURL proměnnou. 
    
          ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

          ![Visual Studio Team Services – soubor Compose aktualizace se adresa URL registru](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

      2. Úlohu příkazového řádku pomocí skriptu bash nahrazuje *AgentURL* výskytu v souboru docker-compose.yml AgentURL proměnnou.
  
          ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

     3. Úloha, která sníží aktualizovaný soubor Compose jako artefakt sestavení, takže ho můžete použít ve vydané verzi. Podívejte se na následující obrazovce podrobnosti.

         ![Visual Studio Team Services – publikování artefaktů](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

         ![Visual Studio Team Services – publikování vytvořit soubor](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. Klikněte na tlačítko **Uložit & frontu** k otestování vaší definice sestavení.

   ![Visual Studio Team Services – uložit a fronty](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![Visual Studio Team Services - novou frontu](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. Pokud **sestavení** je správný, budete muset zobrazí tato obrazovka:

  ![Visual Studio Team Services – sestavení proběhlo úspěšně](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-definition"></a>Krok 3: Vytvoření definice verze

Visual Studio Team Services vám umožní [správě vydávání verzí napříč prostředími](https://www.visualstudio.com/team-services/release-management/). Můžete povolit průběžné nasazování, abyste měli jistotu, že vaše aplikace bude nasazena v různých prostředích (třeba dev, test, předprodukčních a produkčních) smooth způsobem. Můžete vytvořit prostředí, která představuje váš cluster Azure Container Service režimu Docker Swarm.

![Visual Studio Team Services - vydanou verzi ACS](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Nastavení počáteční verze

1. Vytvořte definici vydané verze, klikněte na tlačítko **verze** > **+ vydání**

2. Konfigurovat zdroj artefaktu, klikněte na tlačítko **artefakty** > **propojit zdroj artefaktu**. Této nové definice vydané verze, odkaz na sestavení, které jste definovali v předchozím kroku. Poté je k dispozici v procesu vydání soubor docker-compose.yml.

    ![Visual Studio Team Services - artefakty verzí](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. Konfigurace aktivační události verze, klikněte na tlačítko **triggery** a vyberte **průběžné nasazování**. Nastavením aktivační události na stejném zdroji artefaktů. Toto nastavení zajistí, že nová verze spustí po úspěšném dokončení sestavení.

    ![Visual Studio Team Services – aktivační události vydané verze](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. Nakonfigurovat proměnné vydání, klikněte na **proměnné** a vyberte **+ proměnné** vytvořte tři nové proměnné s informacemi v registru: **docker.username**, **docker.password**, a **docker.registry**. Vložte hodnoty registru a DNS agentů clusteru.

    ![Visual Studio Team Services – úložiště konfigurace sestavení](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > Jak je znázorněno na předchozí obrazovce, klikněte na tlačítko **Zámek** zaškrtávacího políčka v docker.password. Toto nastavení je důležité omezit heslo.
    >

### <a name="define-the-release-workflow"></a>Definování pracovního postupu verze

Pracovní postup vydávání se skládá ze dvou úkolů, které přidáte.

1. Konfigurace úloh a bezpečně zkopírovat soubor compose *nasazení* složky na Docker Swarm hlavního uzlu, pomocí připojení SSH, který jste nakonfigurovali dříve. Podívejte se na následující obrazovce podrobnosti.
    
    Zdrojová složka: ```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![Visual Studio Team Services – verze spojovací bod služby](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. Nakonfigurujte druhý úkol ke spuštění příkazu bashe ke spuštění `docker` a `docker stack deploy` příkazy na hlavní uzel. Podívejte se na následující obrazovce podrobnosti.

    ```docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth```

    ![Visual Studio Team Services – verze Bash](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    Příkaz provedený na hlavním serveru používá rozhraní příkazového řádku Dockeru a rozhraní příkazového řádku Docker-Compose a proveďte následující úlohy:

    - Přihlaste se do služby Azure container registry (používá tří proměnných sestavení, které jsou definovány v **proměnné** kartu)
    - Definovat **DOCKER_HOST** proměnné pro práci s koncovým bodům Swarmu (: 2375)
    - Přejděte *nasazení* složku, která byla vytvořena předchozí úlohou zabezpečené kopie, který obsahuje soubor docker-compose.yml 
    - Spustit `docker stack deploy` příkazy, které o přijetí změn nové Image a vytvoření kontejnerů.

    >[!IMPORTANT]
    > Jak je vidět na předchozí obrazovku, nechat **neúspěšné, když STDERR** nezaškrtnuté políčko. Toto nastavení umožňuje nám k dokončení procesu vydávání verzí z důvodu `docker-compose` vytiskne několik diagnostické zprávy, jako jsou kontejnery, zastavení nebo odstranění, standardního chybového výstupu. Pokud zaškrtnete políčko, sestavy Visual Studio Team Services, že došlo k chybám při vydání, i pokud všechno proběhne správně.
    >
3. Uložení této nové definice vydané verze.

## <a name="step-4-test-the-cicd-pipeline"></a>Krok 4: Testování kanálu CI/CD

Teď, když jste hotovi s konfigurací, je čas k otestování tohoto nového kanálu CI/CD. Nejjednodušší způsob, jak ji otestovat je aktualizovat zdrojový kód a potvrzení změn do vašeho úložiště GitHub. Několik sekund poté, co vložíte kód, zobrazí se nové sestavení spuštěná ve službě Visual Studio Team Services. Po úspěšném dokončení novou verzi se aktivuje a nasadili novou verzi aplikace v clusteru Azure Container Service.

## <a name="next-steps"></a>Další postup

* Další informace o CI/CD pomocí Visual Studio Team Services, najdete v článku [sestavení VSTS přehled](https://www.visualstudio.com/docs/build/overview).
* Další informace o modulu ACS, najdete v článku [úložiště GitHub modul ACS](https://github.com/Azure/acs-engine).
* Další informace o režimu Docker Swarm, najdete v článku [přehled režimu Docker Swarm](https://docs.docker.com/engine/swarm/).
