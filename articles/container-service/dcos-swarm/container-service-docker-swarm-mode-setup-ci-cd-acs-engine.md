---
title: CI/CD s Azure Container Service modul a režim Swarm
description: Použití stroj kontejneru služby Azure se Docker Swarm režimu, registru kontejner Azure a Visual Studio Team Services k poskytování nepřetržitě aplikace .NET Core více kontejneru
services: container-service
author: diegomrtnzg
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/27/2017
ms.author: diegomrtnzg
ms.custom: mvc
ms.openlocfilehash: 01126f3eef988eb1787bafea92e7384aad1a703c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32179567"
---
# <a name="full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-visual-studio-team-services"></a>Úplné kanálu CI nebo CD pro nasazení aplikace s více kontejnerů v Azure Container Service pomocí modulu služby ACS a Docker Swarm režimu pomocí Visual Studio Team Services

*Tento článek vychází z [kanálu úplné CI nebo CD pro nasazení aplikace s více kontejnerů v Azure Container Service pomocí nástroje Docker Swarm, pomocí sady Visual Studio Team Services](container-service-docker-swarm-setup-ci-cd.md) dokumentace*

V současné době jeden z největších problémů, když vývoj moderních aplikací pro cloud je schopnost poskytovat nepřetržitě tyto aplikace. V tomto článku se naučíte, jak implementovat úplné průběžnou integraci a nasazení (CI/CD) s použitím kanálu: 
* Stroj služby Azure kontejner s režimem Docker Swarm
* Azure Container Registry
* Visual Studio Team Services

Tento článek vychází jednoduchou aplikaci, k dispozici na [Githubu](https://github.com/jcorioland/MyShop/tree/docker-linux), vyvinuté pomocí ASP.NET Core. Aplikace se skládá ze čtyř různých služeb: tři webového rozhraní API a jeden web front-endu:

![MyShop ukázkové aplikace](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

Cílem je zajistit tuto aplikaci nepřetržitě v clusteru Docker Swarm režimu, pomocí sady Visual Studio Team Services. Následující obrázek podrobnosti tohoto kanálu nastavené průběžné doručování:

![MyShop ukázkové aplikace](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

Následuje stručné vysvětlení kroky:

1. Změny kódu se zaměřuje na úložiště zdrojového kódu (tady Githubu) 
2. GitHub aktivuje build ve Visual Studio Team Services 
3. Visual Studio Team Services získá nejnovější verzi zdroje a vytvoří všechny bitové kopie, které tvoří aplikace 
4. Visual Studio Team Services doručí každé bitové kopie do registru Docker vytvořené pomocí služby Azure kontejneru registru 
5. Visual Studio Team Services aktivuje novou verzí 
6. Verze spouští některé příkazy na hlavního uzlu clusteru Azure container service pomocí protokolu SSH 
7. Vrátí nejnovější verzi bitové kopie docker Swarm režimu v clusteru 
8. Nová verze aplikace se nasazuje pomocí Docker zásobníku 

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu, budete muset provést následující úlohy:

- [Vytvoření clusteru Swarm režimu v Azure Container Service s modulem služby ACS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Propojení s clusterem Swarm ve službě Azure Container Service](../container-service-connect.md)
- [Vytvoření služby Azure kontejneru registru](../../container-registry/container-registry-get-started-portal.md)
- [Máte účet a tým projekt Visual Studio Team Services vytvořen](https://www.visualstudio.com/en-us/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)
- [Větev úložiště GitHub ke svému účtu GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> Orchestrátor Docker Swarm v Azure Container Service používá starší verzi samostatného Swarmu. Integrovaný [režim Swarm](https://docs.docker.com/engine/swarm/) (v Dockeru 1.12 a novějším) aktuálně není podporovaným orchestrátorem v Azure Container Service. Z tohoto důvodu se používá [modul služby ACS](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md), komunity podílí [šablony rychlý Start](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/), nebo v řešení Docker [Azure Marketplace](https://azuremarketplace.microsoft.com).
>

## <a name="step-1-configure-your-visual-studio-team-services-account"></a>Krok 1: Konfigurace účtu Visual Studio Team Services 

V této části můžete nakonfigurovat účet Visual Studio Team Services. Chcete-li konfigurovat služby VSTS služby koncových bodů, ve vašem projektu Visual Studio Team Services, klikněte na tlačítko **nastavení** v panelu nástrojů a vyberte ikonu **služby**.

![Koncový bod služby otevřete](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-visual-studio-team-services-and-azure-account"></a>Připojení účtu Visual Studio Team Services a Azure

Nastavte připojení mezi projektu služby VSTS a účtu Azure.

1. Na levé straně klikněte na tlačítko **nový koncový bod služby** > **Azure Resource Manager**.
2. Autorizace služby VSTS pro práci s vaším účtem Azure, vyberte vaše **předplatné** a klikněte na tlačítko **OK**.

    ![Visual Studio Team Services - autorizovat Azure](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-visual-studio-team-services-and-github"></a>Připojit Visual Studio Team Services a GitHub

Nastavte připojení mezi projektu služby VSTS a účtu Githubu.

1. Na levé straně klikněte na tlačítko **nový koncový bod služby** > **Githubu**.
2. Chcete-li autorizovat služby VSTS pro práci s účtu Githubu, klikněte na tlačítko **Authorize** a postupujte podle pokynů v okně, které se otevře.

    ![Visual Studio Team Services - autorizovat Githubu](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-vsts-to-your-azure-container-service-cluster"></a>Služby VSTS připojit ke clusteru Azure Container Service

Poslední kroky před získáním do kanálu CI/CD jsou konfigurace externí připojení ke clusteru Docker Swarm v Azure. 

1. Pro cluster Docker Swarm, přidání koncového bodu typu **SSH**. Zadejte informace o připojení SSH clusteru Swarm (hlavní uzel).

    ![Visual Studio Team Services - SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

Všechny konfigurace se provádí teď. V dalších krocích můžete vytvořit kanál CI/CD, který vytvoří a nasadí aplikaci do clusteru Docker Swarm. 

## <a name="step-2-create-the-build-definition"></a>Krok 2: Vytvoření definici sestavení

V tomto kroku můžete nastavit definice sestavení pro svůj projekt služby VSTS a definovat sestavení pracovního postupu pro kontejner obrázků

### <a name="initial-definition-setup"></a>Definice počáteční instalace

1. K vytvoření definice sestavení, připojení k vašemu projektu Visual Studio Team Services a klikněte na tlačítko **sestavení a verze**. V **sestavení definice** klikněte na tlačítko **+ nový**. 

    ![Visual Studio Team Services – nové sestavení definice](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. Vyberte **prázdný proces**.

    ![Visual Studio Team Services – nové definice buildu prázdný](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. Potom klikněte **proměnné** kartě a vytvořte dva nové proměnné: **RegistryURL** a **AgentURL**. Vložení hodnoty registru a DNS agenty clusteru.

    ![Visual Studio Team Services - proměnné konfigurace sestavení](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. Na **definice sestavení** otevřete **aktivační události** a nakonfigurujte sestavení pro použití průběžnou integraci s pokračovatelem MyShop projekt, který jste vytvořili v požadavky. Pak vyberte **dávky změny**. Ujistěte se, že jste vybrali *docker linux* jako **větev specifikace**.

    ![Visual Studio Team Services - úložiště konfigurace sestavení](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. Nakonec klikněte na **možnosti** a nakonfigurujte agenta frontě výchozí **Preview Linux hostované**.

    ![Visual Studio Team Services - Host Agent Configuration](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>Definice pracovního postupu sestavení
Další kroky definovat sestavení pracovního postupu. Nejdřív musíte nakonfigurovat zdrojového kódu. Chcete-li provést, vyberte **Githubu** a **úložiště** a **větve** (docker-linux).

![Visual Studio Team Services - konfigurace zdrojového kódu](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

Pět kontejneru Image na sestavení pro *MyShop* aplikace. Každé bitové kopie je sestaven pomocí soubor Docker umístěný ve složce projektu:

* ProductsApi
* Proxy server
* RatingsApi
* RecommandationsApi
* ShopFront

Budete potřebovat dva kroky Docker pro každé bitové kopie, jeden vytvořit bitovou kopii a jeden pro uložení image v registru kontejner Azure. 

1. Chcete-li přidat krok v pracovním postupu sestavení, klikněte na tlačítko **+ přidat krok sestavení** a vyberte **Docker**.

    ![Visual Studio Team Services - přidat kroky sestavení](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. Pro každé bitové kopie, nakonfigurovat jeden krok, který používá `docker build` příkaz.

    ![Visual Studio Team Services - Docker sestavení](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    Pro operaci sestavení, vyberte kontejner registr Azure, **vytvořit bitovou kopii** akce a soubor Docker, která definuje každé bitové kopie. Nastavte **pracovní adresář** jako soubor Docker kořenový adresář, zadejte **název bitové kopie**a vyberte **zahrnují nejnovější značky**.
    
    Název bitové kopie musí být v tomto formátu: ```$(RegistryURL)/[NAME]:$(Build.BuildId)```. Nahraďte **[NAME]** s název bitové kopie:
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. Pro každé bitové kopie, nakonfigurujte druhý krok, který používá `docker push` příkaz.

    ![Visual Studio Team Services - nabízené Docker](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    Pomocí operace push, vyberte možnost kontejner Azure registr, **Push bitovou kopii** akce, zadejte **název bitové kopie** který je vytvořen v předchozím kroku a vyberte **zahrnout nejnovější značky**.

4. Po dokončení konfigurace sestavení a nabízených kroky pro každý z pěti bitové kopie, přidejte tři další kroky v pracovním postupu sestavení.

   ![Visual Studio Team Services - přidat úlohu příkazového řádku](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

      1. Úlohu příkazového řádku, která používá skript bash nahradit *RegistryURL* výskyt v soubor docker-compose.yml s RegistryURL proměnnou. 
    
          ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

          ![Visual Studio Team Services - vytvářené aktualizace souboru s adresou URL registru](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

      2. Úlohu příkazového řádku, která používá skript bash nahradit *AgentURL* výskyt v soubor docker-compose.yml s AgentURL proměnnou.
  
          ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

     3. Úloha, která zahodí aktualizovaný soubor vytvářené jako sestavení artefaktů, takže ho můžete použít ve verzi. V následující obrazovku podrobnosti.

         ![Visual Studio Team Services - publikování artefaktů](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

         ![Visual Studio Team Services - publikování vytvářené souboru](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. Klikněte na tlačítko **Uložit & fronty** k testování vaší definice sestavení.

   ![Visual Studio Team Services - uložit a fronty](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![Visual Studio Team Services – nové fronty](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. Pokud **sestavení** je správný, budete muset tato obrazovka:

  ![Visual Studio Team Services - sestavení bylo úspěšně dokončeno](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-definition"></a>Krok 3: Vytvoření definice verze

Visual Studio Team Services umožňuje [Správa verzí v různých prostředích](https://www.visualstudio.com/team-services/release-management/). Průběžné nasazování, abyste měli jistotu, že vaše aplikace je nasazená na vaše různých prostředích (například vývojářů, testovací, předprodukční a produkční) smooth způsobem můžete povolit. Můžete vytvořit prostředí, které představuje váš cluster Azure Container Service Docker Swarm režimu.

![Visual Studio Team Services - verze služby ACS](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Instalační program původní verze

1. K vytvoření definice vydání, klikněte na tlačítko **verze** > **+ verze**

2. Chcete-li nastavit zdroj artefaktů, klikněte na tlačítko **artefakty** > **odkaz na zdroj artefaktů**. Tato nová verze definice zde odkaz na sestavení, který jste zadali v předchozím kroku. Potom je k dispozici v procesu verze soubor docker-compose.yml.

    ![Visual Studio Team Services - artefakty verze](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. Konfigurace verze aktivační událost, klikněte na tlačítko **aktivační události** a vyberte **průběžné nasazování**. Nastavte aktivační události na stejném zdroji artefaktů. Toto nastavení zajistí, že nová verze začne po úspěšném dokončení sestavení.

    ![Visual Studio Team Services - verze aktivační události](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. Chcete-li nakonfigurovat proměnné verze, klikněte na tlačítko **proměnné** a vyberte **+ proměnné** vytvořit tři nové proměnné s informacemi registru: **docker.username**, **docker.password**, a **docker.registry**. Vložení hodnoty registru a DNS agenty clusteru.

    ![Visual Studio Team Services - úložiště konfigurace sestavení](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > Jak je znázorněno na předchozí obrazovce, klikněte na tlačítko **zámku** zaškrtnout políčko docker.password. Toto nastavení je důležité omezit heslo.
    >

### <a name="define-the-release-workflow"></a>Zadejte verzi pracovního postupu

Verze pracovní postup se skládá z dvě úlohy, které přidáte.

1. Nakonfigurujte úlohu bezpečně zkopírovat soubor vytvářené *nasazení* složky na Docker Swarm hlavního uzlu pomocí připojení SSH jste nakonfigurovali dříve. V následující obrazovku podrobnosti.
    
    Zdrojová složka: ```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![Visual Studio Team Services - verze spojovací bod služby](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. Nakonfigurujte druhý úlohy ke spuštění příkazu bash ke spuštění `docker` a `docker stack deploy` příkazů na hlavní uzel. V následující obrazovku podrobnosti.

    ```docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth```

    ![Visual Studio Team Services - Bash verze](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    Příkaz proveden na hlavním serveru používá rozhraní příkazového řádku Dockeru a rozhraní příkazového řádku Docker Compose provádění následujících úloh:

    - Přihlaste se k Azure kontejneru registru (používá tři proměnné sestavení, které jsou definovány v **proměnné** karty)
    - Definování **DOCKER_HOST** proměnnou pro práci s koncovému bodu Swarm (: 2375)
    - Přejděte na *nasazení* složky, který byl vytvořen předchozí zabezpečené kopírování úlohy a který obsahuje soubor docker-compose.yml 
    - Spuštění `docker stack deploy` příkazy, které nových bitových kopií pro vyžádání obsahu a vytvoření kontejnerů.

    >[!IMPORTANT]
    > Jak je znázorněno na předchozí obrazovce, ponechte **nezdaří do datového proudu STDERR** nezaškrtnuté políčko. Toto nastavení umožňuje nám dokončete proces verzi z důvodu `docker-compose` vytiskne několik diagnostické zprávy, jako jsou kontejnery se zastavit nebo odstraňuje na standardní chyba výstup. Pokud zaškrtnete políčko, Visual Studio Team Services hlásí, že nastaly chyby během verzí, i v případě, že všechno proběhne správně.
    >
3. Uložte tuto novou verzi definici.

## <a name="step-4-test-the-cicd-pipeline"></a>Krok 4: Test CI/CD kanálu

Teď, když jste hotovi s konfigurací, je otestovat, tento nový kanál CI/CD. Aktualizujte zdrojový kód a provést změny do úložiště GitHub je nejjednodušší způsob, jak otestovat. Několik sekund poté, co push kód, zobrazí se nové sestavení spuštěná ve Visual Studio Team Services. Po úspěšném dokončení novou verzi se aktivuje a nasadit novou verzi aplikace v clusteru Azure Container Service.

## <a name="next-steps"></a>Další postup

* Další informace o CI/CD s Visual Studio Team Services najdete v tématu [služby VSTS sestavení přehled](https://www.visualstudio.com/docs/build/overview).
* Další informace o modulu služby ACS, najdete v článku [úložiště GitHub modul ACS](https://github.com/Azure/acs-engine).
* Další informace o Docker Swarm režimu najdete v tématu [Docker Swarm přehled režimu](https://docs.docker.com/engine/swarm/).
