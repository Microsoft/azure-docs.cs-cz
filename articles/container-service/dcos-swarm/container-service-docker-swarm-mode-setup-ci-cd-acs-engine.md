---
title: (ZASTARALÉ) CI/CD s azure kontejnerovým servisním strojem a režimem roje
description: Použití Azure Container Service Engine s režimem Docker Swarm, registrem kontejnerů Azure a Azure DevOps k průběžnému doručování vícekontejnerové aplikace .NET Core
author: diegomrtnzg
ms.service: container-service
ms.topic: conceptual
ms.date: 05/27/2017
ms.author: dimart
ms.custom: mvc
ms.openlocfilehash: 1ec7ece6f5afd1bbd2613ae08af04b82e8a156b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277918"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-azure-devops"></a>(ZASTARALÉ) Úplný kanál CI/CD pro nasazení aplikace s více kontejnery ve službě Azure Container Service pomocí služby ACS Engine a Docker Swarm Mode pomocí Azure DevOps

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

*Tento článek je založený na [kanálu Úplné CI/CD k nasazení aplikace s více kontejnery ve službě Azure Container Service s Docker Swarm pomocí dokumentace Azure DevOps](container-service-docker-swarm-setup-ci-cd.md)*

V současné době je jednou z největších výzev při vývoji moderních aplikací pro cloud možnost i průběžné doručování těchto aplikací. V tomto článku se dozvíte, jak implementovat kanál úplné průběžné integrace a nasazení (CI/CD) pomocí: 
* Azure Container Service Engine s režimem Docker Swarm Mode
* Azure Container Registry
* Azure DevOps


![Ukázková aplikace MyShop](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

Cílem je poskytovat tuto aplikaci nepřetržitě v clusteru Docker Swarm Mode pomocí Azure DevOps. Následující obrázek podrobně popisuje tento kanál průběžného doručování:

![Ukázková aplikace MyShop](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

Zde je stručné vysvětlení kroků:

1. Změny kódu jsou potvrzeny do úložiště zdrojového kódu (zde GitHub) 
2. GitHub aktivuje sestavení v Azure DevOps 
3. Azure DevOps získá nejnovější verzi zdrojů a vytvoří všechny image, které tvoří aplikaci 
4. Azure DevOps odešle každou bitovou kopii do registru Dockeru vytvořeného pomocí služby Azure Container Registry 
5. Azure DevOps aktivuje novou verzi 
6. Verze spustí některé příkazy pomocí SSH na hlavním uzlu clusteru clusteru kontejnerů Azure 
7. Režim Docker Swarm v clusteru vytáhne nejnovější verzi bitových kopií 
8. Nová verze aplikace se nasazuje pomocí zásobníku Dockeru. 

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu je třeba dokončit následující úkoly:

- [Vytvoření clusteru režimu roje ve službě Azure Container Service pomocí modulu ACS Engine](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Propojení s clusterem Swarm v Azure Container Service](../container-service-connect.md)
- [Vytvoření registru kontejnerů Azure](../../container-registry/container-registry-get-started-portal.md)
- [Vytvoření organizace a projektu Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Vyvlečte úložiště GitHub u svého účtu GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> Orchestrátor Docker Swarm v Azure Container Service používá starší verzi samostatného Swarmu. Integrovaný [režim Swarm](https://docs.docker.com/engine/swarm/) (v Dockeru 1.12 a novějším) aktuálně není podporovaným orchestrátorem v Azure Container Service. Z tohoto důvodu používáme [ACS Engine](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md), komunitou inověovnou [šablonu rychlého startu](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/)nebo řešení Dockeru na [Azure Marketplace](https://azuremarketplace.microsoft.com).
>

## <a name="step-1-configure-your-azure-devops-organization"></a>Krok 1: Konfigurace organizace Azure DevOps 

V této části nakonfigurujete organizaci Azure DevOps. Pokud chcete nakonfigurovat koncové body služby Azure DevOps, klikněte v projektu Azure DevOps na ikonu **Nastavení** na panelu nástrojů a vyberte **Služby**.

![Otevřít koncový bod služeb](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-azure-devops-and-azure-account"></a>Připojení účtu Azure DevOps a Azure

Nastavte si připojení mezi projektem Azure DevOps a účtem Azure.

1. Vlevo klikněte na **New Service Endpoint** > **Azure Resource Manager**.
2. Pokud chcete autorizovat Azure DevOps pro práci s vaším účtem Azure, vyberte **předplatné** a klikněte na **OK**.

    ![Azure DevOps – autorizace Azure](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-azure-devops-and-github"></a>Připojení Azure DevOps a GitHubu

Nastavte připojení mezi projektem Azure DevOps a účtem GitHub.

1. Vlevo klikněte na **Nový koncový bod** > služby**GitHub**.
2. Pokud chcete autorizovat Azure DevOps pro práci s vaším účtem GitHub, klikněte na **Autorizovat** a postupujte podle postupu v okně, které se otevře.

    ![Azure DevOps – autorizace GitHubu](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-azure-devops-to-your-azure-container-service-cluster"></a>Připojení Azure DevOps ke clusteru Služby kontejnerů Azure

Poslední kroky před vstupem do kanálu CI/CD jsou konfigurace externích připojení ke clusteru Docker Swarm v Azure. 

1. Pro cluster Docker Swarm přidejte koncový bod typu **SSH**. Poté zadejte informace o připojení SSH clusteru Swarm (hlavní uzel).

    ![Azure DevOps – SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

Veškerá konfigurace je nyní hotová. V dalších krocích vytvoříte kanál CI/CD, který vytvoří a nasadí aplikaci do clusteru Docker Swarm. 

## <a name="step-2-create-the-build-pipeline"></a>Krok 2: Vytvoření kanálu sestavení

V tomto kroku nastavíte kanál sestavení pro váš projekt Azure DevOps a definujete pracovní postup sestavení pro ibi kontejnerů

### <a name="initial-pipeline-setup"></a>Počáteční nastavení kanálu

1. Pokud chcete vytvořit kanál sestavení, připojte se k projektu Azure DevOps a klikněte na **Build & Release**. V části **Definice sestavení** klikněte na **+ Nový**. 

    ![Azure DevOps – nový kanál sestavení](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. Vyberte **prázdný proces**.

    ![Azure DevOps – nový prázdný kanál sestavení](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. Potom klepněte na kartu **Proměnné** a vytvořte dvě nové proměnné: **RegistryURL** a **AgentURL**. Vložte hodnoty služby DNS agentů registru a clusteru.

    ![Azure DevOps – konfigurace proměnných sestavení](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. Na stránce **Definice sestavení** otevřete kartu **Aktivační události** a nakonfigurujte sestavení tak, aby používalo průběžnou integraci s roztečí projektu MyShop, který jste vytvořili v požadavcích. Potom vyberte **Dávkové změny**. Ujistěte se, že jste vybrali *docker-linux* jako **specifikaci větve**.

    ![Azure DevOps – konfigurace úložiště sestavení](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. Nakonec klepněte na kartu **Možnosti** a nakonfigurujte výchozí frontu agentů na **hostovaný linuxový náhled**.

    ![Azure DevOps – konfigurace agenta hostitele](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>Definování pracovního postupu sestavení
Další kroky definují pracovní postup sestavení. Nejprve je třeba nakonfigurovat zdroj kódu. Chcete-li to provést, vyberte **GitHub** a **úložiště** a **větev** (docker-linux).

![Azure DevOps – konfigurace zdroje kódu](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

Existuje pět image kontejneru k vytvoření pro aplikaci *MyShop.* Každá bitová kopie je vytvořena pomocí souboru Dockerfile umístěného ve složkách projektu:

* ProduktyApi
* Proxy server
* RatingsApi
* DoporučeníApi
* ShopFront

Potřebujete dva kroky Dockeru pro každou bitovou kopii, jeden pro sestavení image a jeden pro nabízení image v registru kontejnerů Azure. 

1. Chcete-li přidat krok v pracovním postupu sestavení, klikněte na **+ Přidat krok sestavení** a vyberte **Docker**.

    ![Azure DevOps – přidání kroků sestavení](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. Pro každou bitovou kopii `docker build` nakonfigurujte jeden krok, který používá příkaz.

    ![Azure DevOps – sestavení Dockeru](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    Pro operaci sestavení vyberte registr kontejnerů Azure, **akci Sestavení bitové kopie** a dockerový soubor, který definuje každou bitovou kopii. Nastavte **pracovní adresář** jako kořenový adresář dockerového souboru, definujte **název obrázku**a vyberte **Zahrnout nejnovější značku**.
    
    Název obrázku musí být v ```$(RegistryURL)/[NAME]:$(Build.BuildId)```tomto formátu: . Nahraďte **[NÁZEV]** názvem obrázku:
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. Pro každou bitovou kopii nakonfigurujte druhý krok, který používá `docker push` příkaz.

    ![Azure DevOps – nabízená podpora dockeru](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    Pro operaci nabízená tiskovou nabídku vyberte registr kontejnerů Azure, akci **Push image,** zadejte **název obrázku,** který je vytvořený v předchozím kroku, a vyberte **Zahrnout nejnovější značku**.

4. Po konfiguraci sestavení a push kroky pro každou z pěti bitových kopií, přidejte další tři kroky v pracovním postupu sestavení.

   ![Azure DevOps – přidat úlohu příkazového řádku](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

   1. Úloha příkazového řádku, která používá skript bash k nahrazení výskytu *RegistryURL* v souboru docker-compose.yml proměnnou RegistryURL. 
    
       ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

       ![Azure DevOps – aktualizovat soubor s adresou URL registru](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

   2. Úloha příkazového řádku, která používá skript bash k nahrazení výskytu *AgentURL* v souboru docker-compose.yml proměnnou AgentURL.
  
       ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

      1. Úloha, která klesne aktualizovaný soubor Compose jako artefakt sestavení, takže jej lze použít ve verzi. Podrobnosti najdete na následující obrazovce.

      ![Azure DevOps – publikování artefaktu](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

      ![Azure DevOps – publikovat soubor s publikováním](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. Chcete-li otestovat kanál sestavení, klepněte na tlačítko **Uložit & fronty.**

   ![Azure DevOps – uložení a zařazení do fronty](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![Azure DevOps – nová fronta](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. Pokud **sestavení** je správné, musíte vidět tuto obrazovku:

   ![Azure DevOps – sestavení proběhlo úspěšně.](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-pipeline"></a>Krok 3: Vytvoření kanálu vydání

Azure DevOps umožňuje [spravovat vydání napříč prostředími](https://www.visualstudio.com/team-services/release-management/). Můžete povolit průběžné nasazení a ujistěte se, že vaše aplikace je nasazena v různých prostředích (například vývoj, testování, předprodukční a produkční) hladkým způsobem. Můžete vytvořit prostředí, které představuje váš cluster Azure Container Service Docker Swarm Mode.

![Azure DevOps – vydání do ACS](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Počáteční nastavení vydání

1. Chcete-li vytvořit kanál pro vydání, klikněte na **položku Releases** > **+ Release**

2. Chcete-li nakonfigurovat zdroj artefaktů, klepněte na **položku Artefakty** > **Propojit zdroj artefaktů**. Zde propojte tento kanál nové verze s sestavením, které jste definovali v předchozím kroku. Poté je soubor docker-compose.yml k dispozici v procesu vydání.

    ![Azure DevOps – artefakty vydání](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. Chcete-li nakonfigurovat aktivační událost vydání, klepněte na **položku Aktivační události** a vyberte **možnost Průběžné nasazení**. Nastavte aktivační událost na stejném zdroji artefaktů. Toto nastavení zajišťuje, že se po úspěšném dokončení sestavení spustí nová verze.

    ![Azure DevOps – aktivační události vydání](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. Chcete-li nakonfigurovat proměnné vydání, klepněte na **položku Proměnné** a výběrem **možnosti +Variable** vytvořte tři nové proměnné s informacemi o registru: **docker.username**, **docker.password**a **docker.registry**. Vložte hodnoty služby DNS agentů registru a clusteru.

    ![Azure DevOps – konfigurace úložiště sestavení](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > Jak je znázorněno na předchozí obrazovce, klikněte na zaškrtávací políčko **Zamknout** v docker.password. Toto nastavení je důležité omezit heslo.
    >

### <a name="define-the-release-workflow"></a>Definování pracovního postupu vydání

Pracovní postup vydání se skládá ze dvou úkolů, které přidáte.

1. Nakonfigurujte úlohu tak, aby bezpečně zkopírovala soubor compose do složky *nasazení* v uzlu hlavního serveru Docker Swarm pomocí dříve nakonfigurovaného připojení SSH. Podrobnosti najdete na následující obrazovce.
    
    Zdrojová složka:```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![Azure DevOps – vydání SCP](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. Nakonfigurujte druhou úlohu `docker` pro `docker stack deploy` spuštění příkazu bash a příkazy v hlavním uzlu. Podrobnosti najdete na následující obrazovce.

    ```
    docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth
    ```

    ![Azure DevOps – bash vydání](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    Příkaz provedený na předloze používá cli Dockeru a cli docker-compose k provedení následujících úkolů:

   - Přihlaste se do registru kontejnerů Azure (používá tři proměnné sestavení, které jsou definovány na kartě **Proměnné)**
   - Definujte **proměnnou DOCKER_HOST** pro práci s koncovým bodem Swarm (:2375)
   - Přejděte do složky *nasazení,* která byla vytvořena předchozí úlohou zabezpečeného kopírování a obsahuje soubor docker-compose.yml 
   - Spouštějte `docker stack deploy` příkazy, které vytahují nové obrázky a vytvářejí kontejnery.

     >[!IMPORTANT]
     > Jak je znázorněno na předchozí obrazovce, ponechte **zaškrtávací políčko Selhání na STDERR** nezaškrtnuté. Toto nastavení nám umožňuje dokončit `docker-compose` proces vydání z důvodu tisku několika diagnostických zpráv, jako jsou kontejnery, které se zastavují nebo odstraňují, na standardním výstupu chyb. Pokud zaškrtnete políčko, Azure DevOps hlásí, že chyby došlo během vydání, i když vše půjde dobře.
     >
3. Uložte tento nový kanál vydání.

## <a name="step-4-test-the-cicd-pipeline"></a>Krok 4: Testování kanálu CI/CD

Nyní, když jste hotovi s konfigurací, je čas otestovat tento nový kanál CI/CD. Nejjednodušší způsob, jak ji otestovat, je aktualizovat zdrojový kód a potvrdit změny do úložiště GitHub. Několik sekund po nabízení kódu se zobrazí nové sestavení spuštěné v Azure DevOps. Po úspěšném dokončení se aktivuje nová verze a nasadí novou verzi aplikace v clusteru služby Azure Container Service.

## <a name="next-steps"></a>Další kroky

* Další informace o CI/CD s Azure DevOps najdete v článku [dokumentace k Azure Pipelines.](/azure/devops/pipelines/?view=azure-devops)
* Další informace o modulu ACS engine naleznete v [úložišti GitHub modulu ACS](https://github.com/Azure/acs-engine).
* Další informace o režimu Docker Swarm najdete v tématu [Přehled režimu Docker Swarm](https://docs.docker.com/engine/swarm/).
