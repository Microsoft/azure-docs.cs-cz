---
title: ZASTARALÉ CI/CD s modulem Azure Container Service a režimem Swarm
description: Použití Azure Container Serviceho modulu s režimem Docker Swarm, Azure Container Registry a Azure DevOps pro průběžnou distribuci aplikace .NET Core s více kontejnery
services: container-service
author: diegomrtnzg
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/27/2017
ms.author: dimart
ms.custom: mvc
ms.openlocfilehash: fe24ab21a9a7d227d58e50c58f9aff2bd91e767f
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598549"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-azure-devops"></a>ZASTARALÉ Úplný kanál CI/CD pro nasazení aplikace s více kontejnery na Azure Container Service s modulem ACS a režimem Docker Swarm s využitím Azure DevOps

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

*Tento článek je založený na [úplném kanálu CI/CD pro nasazení aplikace s více kontejnery na Azure Container Service s využitím nástroje Docker Swarm v dokumentaci ke službě Azure DevOps](container-service-docker-swarm-setup-ci-cd.md) .*

Současné době jedním z největších problémů při vývoji moderních aplikací pro Cloud je schopnost doručovat tyto aplikace průběžně. V tomto článku se dozvíte, jak implementovat kompletní kanál průběžné integrace a nasazování (CI/CD) pomocí: 
* Azure Container Service modul s režimem Docker Swarm
* Registr kontejneru Azure
* Azure DevOps

Tento článek je založený na jednoduché aplikaci, která je dostupná [](https://github.com/jcorioland/MyShop/tree/docker-linux)na GitHubu, vyvinutá pomocí ASP.NET Core. Aplikace se skládá ze čtyř různých služeb: tři webová rozhraní API a jeden webový front-end:

![Ukázková aplikace MyShop](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

Cílem je zajistit nepřetržitou distribuci této aplikace v clusteru režimu Docker Swarm pomocí Azure DevOps. Následující obrázek podrobně popisuje tento kanál průběžného doručování:

![Ukázková aplikace MyShop](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

Zde je stručné vysvětlení kroků:

1. Změny kódu se potvrdí do úložiště zdrojového kódu (tady, GitHub). 
2. GitHub spustí sestavení ve službě Azure DevOps 
3. Azure DevOps získá nejnovější verzi zdrojů a sestaví všechny image, které tvoří aplikaci. 
4. Azure DevOps vloží jednotlivé image do registru Docker vytvořeného pomocí služby Azure Container Registry. 
5. Azure DevOps aktivuje novou verzi. 
6. Vydaná verze spouští některé příkazy pomocí SSH v hlavním uzlu clusteru Azure Container Service. 
7. Režim Docker Swarm v clusteru vyžádá nejnovější verzi imagí. 
8. Nová verze aplikace je nasazena pomocí zásobníku Docker 

## <a name="prerequisites"></a>Požadavky

Než začnete tento kurz, musíte provést následující úlohy:

- [Vytvoření clusteru Swarm režimu v Azure Container Service s modulem ACS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Propojení s clusterem Swarm ve službě Azure Container Service](../container-service-connect.md)
- [Vytvoření služby Azure Container Registry](../../container-registry/container-registry-get-started-portal.md)
- [Máte vytvořenou organizaci a projekt Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Rozvětvení úložiště GitHubu do vašeho účtu GitHubu](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> Orchestrátor Docker Swarm v Azure Container Service používá starší verzi samostatného Swarmu. Integrovaný [režim Swarm](https://docs.docker.com/engine/swarm/) (v Dockeru 1.12 a novějším) aktuálně není podporovaným orchestrátorem v Azure Container Service. Z tohoto důvodu používáme [modul ACS](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md), [šablonu pro rychlý Start](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/)od komunity nebo řešení Docker v [Azure Marketplace](https://azuremarketplace.microsoft.com).
>

## <a name="step-1-configure-your-azure-devops-organization"></a>Krok 1: Konfigurace organizace Azure DevOps 

V této části nakonfigurujete organizaci Azure DevOps. Pokud chcete nakonfigurovat koncové body Azure DevOps Services, v projektu Azure DevOps klikněte na ikonu **Nastavení** na panelu nástrojů a vyberte **služby**.

![Koncový bod služby Open Services](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-azure-devops-and-azure-account"></a>Připojení Azure DevOps a účtu Azure

Nastavte připojení mezi vaším projektem Azure DevOps a vaším účtem Azure.

1. Na levé straně klikněte na **Nový koncový bod** > služby**Azure Resource Manager**.
2. Pokud chcete autorizovat Azure DevOps pracovat s vaším účtem Azure, vyberte své **předplatné** a klikněte na **OK**.

    ![Azure DevOps – autorizace Azure](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-azure-devops-and-github"></a>Připojení Azure DevOps a GitHubu

Nastavte připojení mezi vaším projektem Azure DevOps a vaším účtem GitHub.

1. Na levé straně klikněte na **Nový koncový bod** > služby**GitHub**.
2. Pokud chcete autorizovat Azure DevOps pracovat s vaším účtem GitHubu, klikněte na autorizovat a postupujte podle pokynů v okně, které se otevře.

    ![Azure DevOps – autorizace GitHubu](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-azure-devops-to-your-azure-container-service-cluster"></a>Připojení Azure DevOps ke clusteru Azure Container Service

Poslední kroky před tím, než se dostanete do kanálu CI/CD, jsou konfigurace externích připojení k vašemu clusteru Docker Swarm v Azure. 

1. Pro cluster Docker Swarm přidejte koncový bod typu **SSH**. Pak zadejte informace o připojení SSH pro váš cluster Swarm (hlavní uzel).

    ![Azure DevOps – SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

Všechny konfigurace se provedly nyní. V dalších krocích vytvoříte kanál CI/CD, který sestaví a nasadí aplikaci do clusteru Docker Swarm. 

## <a name="step-2-create-the-build-pipeline"></a>Krok 2: Vytvoření kanálu sestavení

V tomto kroku nastavíte kanál sestavení pro projekt Azure DevOps a definujete pracovní postup sestavení pro Image kontejnerů.

### <a name="initial-pipeline-setup"></a>Počáteční nastavení kanálu

1. Pokud chcete vytvořit kanál sestavení, připojte se k projektu Azure DevOps a klikněte na **sestavit & verzi**. V části **definice sestavení** klikněte na **+ Nový**. 

    ![Azure DevOps – nový kanál sestavení](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. Vyberte **prázdný proces**.

    ![Azure DevOps – nový prázdný kanál sestavení](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. Pak klikněte na kartu **proměnné** a vytvořte dvě nové proměnné: **RegistryURL** a **AgentURL**. Vložte hodnoty registru a DNS agentů clusteru.

    ![Azure DevOps – konfigurace proměnných sestavení](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. Na stránce **definice sestavení** otevřete kartu triggery a nakonfigurujte sestavení tak, aby používalo kontinuální integraci s rozvětvem projektu MyShop, který jste vytvořili v části požadavky. Pak vyberte **změny v dávce**. Ujistěte se, že jako **specifikaci větve**vyberete *Docker-Linux* .

    ![Azure DevOps – konfigurace úložiště sestavení](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. Nakonec klikněte na kartu **Možnosti** a nakonfigurujte výchozí frontu agenta na **hostovaná verze Linux Preview**.

    ![Azure DevOps – konfigurace agenta hostitele](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>Definovat pracovní postup sestavení
Další kroky definují pracovní postup sestavení. Nejprve je třeba nakonfigurovat zdroj kódu. Pokud to chcete udělat, vyberte **GitHub** a vaše **úložiště** a **větev** (Docker – Linux).

![Azure DevOps – konfigurace zdroje kódu](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

K sestavení aplikace *MyShop* je k dispozici pět imagí kontejneru. Každý obrázek je sestaven pomocí souboru Dockerfile nacházející se ve složkách projektu:

* ProductsApi
* Proxy
* RatingsApi
* RecommendationsApi
* ShopFront

Pro každý obrázek budete potřebovat dva kroky Docker, jednu pro sestavení image a jednu pro vložení image do služby Azure Container Registry. 

1. Pokud chcete přidat krok do pracovního postupu sestavení, klikněte na **+ Přidat krok sestavení** a vyberte **Docker**.

    ![Azure DevOps – přidání kroků sestavení](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. Pro každý obrázek nakonfigurujte jeden krok, který používá `docker build` příkaz.

    ![Sestavení Azure DevOps-Docker](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    V případě operace sestavení vyberte Azure Container Registry, akci **sestavení obrázku** a souboru Dockerfile, který definuje jednotlivé obrázky. Nastavte **pracovní adresář** jako kořenový adresář souboru Dockerfile, definujte **název bitové kopie**a vyberte **zahrnout nejnovější značku**.
    
    Název bitové kopie musí být v tomto formátu: ```$(RegistryURL)/[NAME]:$(Build.BuildId)```. Nahraďte **[name]** názvem Image:
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. Pro každý obrázek nakonfigurujte druhý krok, který používá `docker push` příkaz.

    ![Nabízená oznámení Azure DevOps-Docker](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    Pro operaci Push vyberte svůj registr kontejnerů Azure, akci **Odeslat obrázek** , zadejte **název Image** , která je vytvořená v předchozím kroku, a vyberte **Zahrnout poslední značku**.

4. Po nakonfigurování kroků sestavení a vložení pro každý z pěti imagí přidejte do pracovního postupu sestavení tři další kroky.

   ![Azure DevOps – přidání úlohy příkazového řádku](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

   1. Úloha příkazového řádku, která používá skript bash k nahrazení výskytu *RegistryURL* v souboru Docker-Compose. yml pomocí proměnné RegistryURL 
    
       ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

       ![Azure DevOps – aktualizace souboru s použitím adresy URL registru](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

   2. Úloha příkazového řádku, která používá skript bash k nahrazení výskytu *AgentURL* v souboru Docker-Compose. yml pomocí proměnné AgentURL
  
       ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

      1. Úkol, který uvolní aktualizovaný soubor pro sestavení jako artefakt sestavení, aby jej bylo možné použít v této verzi. Podrobnosti najdete na následující obrazovce.

      ![Azure DevOps – publikování artefaktu](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

      ![Azure DevOps – publikování souboru s psaním](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. Kliknutím na **uložit & frontu** otestujete svůj kanál sestavení.

   ![Azure DevOps – uložení a zařazení do fronty](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![Azure DevOps – nová fronta](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. Pokud je **sestavení** správné, je třeba zobrazit tuto obrazovku:

   ![Azure DevOps – sestavení bylo úspěšné.](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-pipeline"></a>Krok 3: Vytvořit kanál pro vydávání verzí

Azure DevOps umožňuje [Spravovat verze v různých prostředích](https://www.visualstudio.com/team-services/release-management/). Průběžné nasazování můžete povolit, abyste se ujistili, že vaše aplikace je nasazená v různých prostředích (například vývoj, testování, předprodukční a produkční prostředí) plynule. Můžete vytvořit prostředí, které představuje cluster Azure Container Service Docker Swarm Mode.

![Azure DevOps – vydání na ACS](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Instalace počáteční verze

1. Chcete-li vytvořit kanál verze, klikněte na tlačítko **verze** > **a verze** .

2. Pokud chcete nakonfigurovat zdroj artefaktů, > klikněte na artefakty**propojit se zdrojem artefaktu**. Zde propojte tento nový kanál verze se sestavením, které jste definovali v předchozím kroku. Následně je soubor Docker-Compose. yml k dispozici v procesu vydávání verzí.

    ![Azure DevOps – artefakty vydaných verzí](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. Pokud chcete nakonfigurovat aktivační událost vydání, klikněte na triggery a vyberte **průběžné nasazování**. Nastavte Trigger na stejném zdroji artefaktů. Toto nastavení zajistí, že se nové vydání začne po úspěšném dokončení sestavení.

    ![Aktivační události Azure DevOps-Release](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. Pokud chcete nakonfigurovat proměnné verze, klikněte **na proměnné** a **Vyberte + proměnná** a vytvořte tři nové proměnné s informacemi v registru: **Docker. username**, Docker **. Password**a Docker **. Registry**. Vložte hodnoty registru a DNS agentů clusteru.

    ![Azure DevOps – konfigurace úložiště sestavení](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > Jak je znázorněno na předchozí obrazovce, klikněte na zaškrtávací políčko **Uzamknout** v Docker. Password. Toto nastavení je důležité k omezení hesla.
    >

### <a name="define-the-release-workflow"></a>Definovat pracovní postup vydání

Pracovní postup vydání se skládá ze dvou úloh, které přidáte.

1. Nakonfigurujte úlohu pro bezpečné kopírování souboru s příponou do složky *Deploy* v hlavním uzlu Docker Swarm pomocí připojení SSH, které jste nakonfigurovali dříve. Podrobnosti najdete na následující obrazovce.
    
    Zdrojová složka:```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![BOD připojení služby Azure DevOps-Release](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. Nakonfigurujte druhý úkol, který spustí příkaz bash ke spuštění `docker` příkazů a `docker stack deploy` příkazů v hlavním uzlu. Podrobnosti najdete na následující obrazovce.

    ```
    docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth
    ```

    ![Azure DevOps – Release bash](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    Příkaz provedený v hlavním serveru používá rozhraní Docker CLI a rozhraní příkazového řádku Docker, které provádí následující úlohy:

   - Přihlaste se ke službě Azure Container Registry (používá tři proměnné sestavení, které jsou definovány na kartě **proměnné** ).
   - Definujte proměnnou **DOCKER_HOST** pro práci s koncovým bodem Swarm (: 2375).
   - Přejděte do složky pro *nasazení* , kterou vytvořila předchozí úloha zabezpečeného kopírování a která obsahuje soubor Docker-Compose. yml. 
   - Spusťte `docker stack deploy` příkazy, které vyžádají nové image a vytvoří kontejnery.

     >[!IMPORTANT]
     > Jak je znázorněno na předchozí obrazovce, nechejte zaškrtávací políčko nezaškrtnuto **při selhání u stderr** . Toto nastavení umožňuje dokončení procesu vydávání verzí kvůli `docker-compose` vytištění několika diagnostických zpráv, jako je například zastavení nebo odstranění kontejnerů, na standardním výstupu chyby. Pokud zaškrtnete toto políčko, Azure DevOps oznámí, že při vydání došlo k chybám, i když vše bude dobré.
     >
3. Uložte tento nový kanál pro vydávání verzí.

## <a name="step-4-test-the-cicd-pipeline"></a>Krok 4: Testování kanálu CI/CD

Teď, když jste s konfigurací hotovi, je čas otestovat tento nový kanál CI/CD. Nejjednodušší způsob, jak ho otestovat, je aktualizovat zdrojový kód a potvrdit změny do úložiště GitHub. Několik sekund po nahrání kódu se zobrazí nové sestavení běžící v Azure DevOps. Po úspěšném dokončení se spustí nová verze a nasadí se nová verze aplikace v clusteru Azure Container Service.

## <a name="next-steps"></a>Další postup

* Další informace o CI/CD s Azure DevOps najdete v článku věnovaném [dokumentaci Azure Pipelines](/azure/devops/pipelines/?view=azure-devops) .
* Další informace o modulu ACS najdete v [úložišti GitHub modulu ACS](https://github.com/Azure/acs-engine).
* Další informace o režimu Docker Swarm najdete v tématu [Přehled režimu Docker Swarm](https://docs.docker.com/engine/swarm/).
