---
title: (ZASTARALÉ) CI/CD se službou Azure Container Service a roje
description: Použití služby Azure Container Service s Docker Swarm, registrem kontejnerů Azure a Azure DevOps k průběžnému doručování vícekontejnerové aplikace .NET Core
author: jcorioland
ms.service: container-service
ms.topic: conceptual
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: 11a6debe735459b617f6f93c3f67a32350dd4623
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76549049"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-azure-devops-services"></a>(ZASTARALÉ) Úplný kanál CI/CD pro nasazení vícekontejnerové aplikace ve službě Azure Container Service s Docker Swarm pomocí služeb Azure DevOps Services

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Jednou z největších výzev při vývoji moderních aplikací pro cloud je možnost dodávat tyto aplikace nepřetržitě. V tomto článku se dozvíte, jak implementovat kanál úplné průběžné integrace a nasazení (CI/CD) pomocí služby Azure Container Service se službou Docker Swarm, Azure Container Registry a Azure Pipelines.


![Ukázková aplikace MyShop](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

Cílem je poskytovat tuto aplikaci nepřetržitě v clusteru Docker Swarm pomocí služby Azure DevOps Services. Následující obrázek podrobně popisuje tento kanál průběžného doručování:

![Ukázková aplikace MyShop](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

Zde je stručné vysvětlení kroků:

1. Změny kódu jsou potvrzeny do úložiště zdrojového kódu (zde GitHub) 
1. GitHub aktivuje sestavení ve službách Azure DevOps Services 
1. Azure DevOps Services získá nejnovější verzi zdrojů a vytvoří všechny image, které tvoří aplikaci 
1. Azure DevOps Services odešle každou bitovou kopii do registru Dockeru vytvořeného pomocí služby Azure Container Registry 
1. Azure DevOps Services aktivuje novou verzi 
1. Verze spustí některé příkazy pomocí SSH na hlavním uzlu clusteru clusteru kontejnerů Azure 
1. Docker Swarm v clusteru vytáhne nejnovější verzi bitových kopií 
1. Nová verze aplikace se nasazuje pomocí Docker Compose 

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu je třeba dokončit následující úkoly:

- [Vytvoření clusteru Swarm v Azure Container Service](container-service-deployment.md)
- [Propojení s clusterem Swarm v Azure Container Service](../container-service-connect.md)
- [Vytvoření registru kontejnerů Azure](../../container-registry/container-registry-get-started-portal.md)
- [Mít vytvořenou organizaci a projekt Služby Azure DevOps Services](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Vyvlečte úložiště GitHub u svého účtu GitHub](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Potřebujete také počítač Ubuntu (14.04 nebo 16.04) s nainstalovaným Dockerem. Tento počítač se používá služby Azure DevOps během procesů Azure Pipelines. Jedním ze způsobů, jak vytvořit tento počítač, je použití image dostupné na Azure Marketplace. 

## <a name="step-1-configure-your-azure-devops-services-organization"></a>Krok 1: Konfigurace organizace služeb Azure DevOps Services 

V této části nakonfigurujete organizaci služeb Azure DevOps Services.

### <a name="configure-an-azure-devops-services-linux-build-agent"></a>Konfigurace agenta sestavení Azure DevOps Services Linux

Chcete-li vytvořit inas Dockeru a tlačit tyto image do registru kontejnerů Azure z sestavení Služby Azure DevOps, musíte zaregistrovat agenta Linuxu. Máte tyto možnosti instalace:

* [Nasazení agenta na Linuxu](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Použití Dockeru ke spuštění agenta služby Azure DevOps Services](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-azure-devops-services-extension"></a>Instalace rozšíření Azure DevOps Services integrace Dockeru

Microsoft poskytuje rozšíření Služby Azure DevOps Services pro práci s Dockerem v procesech Azure Pipelines. Toto rozšíření je k dispozici na [webu Azure DevOps Services Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker). Kliknutím na **Instalovat** přidáte toto rozšíření do organizace služeb Azure DevOps Services:

![Instalace integrace Dockeru](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

Budete vyzváni k připojení k organizaci Služby Azure DevOps Services pomocí vašich přihlašovacích údajů. 

### <a name="connect-azure-devops-services-and-github"></a>Připojení služeb Azure DevOps a GitHubu

Nastavte připojení mezi projektem Služby Azure DevOps services a účtem GitHub.

1. V projektu Azure DevOps Services klikněte na ikonu **Nastavení** na panelu nástrojů a vyberte **Služby**.

    ![Azure DevOps Services – externí připojení](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

1. Vlevo klikněte na **Nový koncový bod** > služby**GitHub**.

    ![Služby Azure DevOps – GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

1. Pokud chcete autorizovat služby Azure DevOps Services pro práci s vaším účtem GitHub, klikněte na **Autorizovat** a postupujte podle postupu v okně, které se otevře.

    ![Služby Azure DevOps – autorizace GitHubu](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-azure-devops-services-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Připojení služeb Azure DevOps services k registru kontejnerů Azure a clusteru služby Azure Container Service

Poslední kroky před vstupem do kanálu CI/CD jsou konfigurace externích připojení k registru kontejnerů a clusteru Docker Swarm v Azure. 

1. V nastavení **služeb** projektu Služby Azure DevOps přidejte koncový bod služby typu **Docker Registry**. 

1. Do místního okna, které se otevře, zadejte adresu URL a přihlašovací údaje registru kontejnerů Azure.

    ![Azure DevOps Services – registr Dockeru](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

1. Pro cluster Docker Swarm přidejte koncový bod typu **SSH**. Poté zadejte informace o připojení SSH clusteru Swarm.

    ![Služby Azure DevOps – SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

Veškerá konfigurace je nyní hotová. V dalších krocích vytvoříte kanál CI/CD, který vytvoří a nasadí aplikaci do clusteru Docker Swarm. 

## <a name="step-2-create-the-build-pipeline"></a>Krok 2: Vytvoření kanálu sestavení

V tomto kroku nastavíte kanál sestavení pro projekt služby Azure DevOps Services a definujete pracovní postup sestavení pro ibi kontejnerů.

### <a name="initial-pipeline-setup"></a>Počáteční nastavení kanálu

1. Pokud chcete vytvořit kanál sestavení, připojte se k projektu Služby Azure DevOps services a klikněte na **Build & Release**. 

1. V části **Definice sestavení** klikněte na **+ Nový**. Vyberte **prázdnou** šablonu.

    ![Azure DevOps – nový kanál sestavení](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

1. Nakonfigurujte nové sestavení se zdrojem úložiště GitHub, zkontrolujte **průběžnou integraci**a vyberte frontu agentů, kde jste zaregistrovali svého agenta Linuxu. Kliknutím na **Vytvořit** vytvořte kanál sestavení.

    ![Azure DevOps Services – vytvoření kanálu sestavení](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

1. Na stránce **Definice sestavení** nejprve otevřete kartu **Úložiště** a nakonfigurujte sestavení tak, aby používalo rozteč projektu MyShop, který jste vytvořili v požadavcích. Ujistěte se, že jste vybrali *acs-docs* jako **výchozí větev**.

    ![Azure DevOps Services – konfigurace úložiště sestavení](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

1. Na kartě **Aktivační události** nakonfigurujte sestavení, které se aktivuje po každém potvrzení. Vyberte **Možnost Průběžná integrace** **a Dávkové změny**.

    ![Azure DevOps Services – konfigurace aktivační události sestavení](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>Definování pracovního postupu sestavení
Další kroky definují pracovní postup sestavení. Existuje pět image kontejneru k vytvoření pro aplikaci *MyShop.* Každá bitová kopie je vytvořena pomocí souboru Dockerfile umístěného ve složkách projektu:

* ProduktyApi
* Proxy server
* RatingsApi
* DoporučeníApi
* ShopFront

Musíte přidat dva kroky Dockeru pro každou bitovou kopii, jeden pro sestavení image a jeden pro nabízení image v registru kontejnerů Azure. 

1. Chcete-li přidat krok v pracovním postupu sestavení, klikněte na **+ Přidat krok sestavení** a vyberte **Docker**.

    ![Azure DevOps Services – přidání kroků sestavení](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

1. Pro každou bitovou kopii `docker build` nakonfigurujte jeden krok, který používá příkaz.

    ![Azure DevOps Services – sestavení Dockeru](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    Pro operaci sestavení vyberte registr kontejnerů Azure, **akce Sestavení bitové kopie** a Dockerfile, který definuje každou bitovou kopii. Nastavte **kontext sestavení** jako kořenový adresář Dockerfile a definujte **název obrázku**. 
    
    Jak je znázorněno na předchozí obrazovce, spusťte název bitové kopie pomocí identifikátoru URI registru kontejnerů Azure. (Proměnnou sestavení můžete také použít k parametrizaci značky bitové kopie, například identifikátor sestavení v tomto příkladu.)

1. Pro každou bitovou kopii nakonfigurujte druhý krok, který používá `docker push` příkaz.

    ![Služby Azure DevOps – nabízená podpora dockeru](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    Pro operaci nabízená tiskovou nabídku vyberte registr kontejnerů Azure, akci **Push image** a zadejte **název bitové kopie,** která je vytvořená v předchozím kroku.

1. Po konfiguraci sestavení a push kroky pro každou z pěti bitových kopií, přidejte další dva kroky v pracovním postupu sestavení.

    a. Úloha příkazového řádku, která používá skript bash k nahrazení výskytu *BuildNumber* v souboru docker-compose.yml aktuálním ID sestavení. Podrobnosti najdete na následující obrazovce.

    ![Azure DevOps Services – aktualizovat soubor](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. Úloha, která klesne aktualizovaný soubor Compose jako artefakt sestavení, takže jej lze použít ve verzi. Podrobnosti najdete na následující obrazovce.

    ![Azure DevOps Services – publikovat soubor s publikováním](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

1. Klikněte na **Uložit** a pojmenujte kanál sestavení.

## <a name="step-3-create-the-release-pipeline"></a>Krok 3: Vytvoření kanálu vydání

Azure DevOps Services umožňuje [spravovat vydání napříč prostředími](https://www.visualstudio.com/team-services/release-management/). Můžete povolit průběžné nasazení a ujistěte se, že vaše aplikace je nasazena v různých prostředích (například vývoj, testování, předprodukční a produkční) hladkým způsobem. Můžete vytvořit nové prostředí, které představuje cluster Docker Swarm Služby Azure Container Service.

![Služby Azure DevOps – vydání do služby ACS](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Počáteční nastavení vydání

1. Chcete-li vytvořit kanál pro vydání, klikněte na **položku Releases** > **+ Release**

1. Chcete-li nakonfigurovat zdroj artefaktů, klepněte na **položku Artefakty** > **Propojit zdroj artefaktů**. Zde propojte tento kanál nové verze s sestavením, které jste definovali v předchozím kroku. Tímto způsobem docker-compose.yml soubor je k dispozici v procesu vydání.

    ![Azure DevOps Services – artefakty vydání](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

1. Chcete-li nakonfigurovat aktivační událost vydání, klepněte na **položku Aktivační události** a vyberte **možnost Průběžné nasazení**. Nastavte aktivační událost na stejném zdroji artefaktů. Toto nastavení zajistí, že se nová verze spustí, jakmile se sestavení úspěšně dokončí.

    ![Azure DevOps Services – aktivační události vydání](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>Definování pracovního postupu vydání

Pracovní postup vydání se skládá ze dvou úkolů, které přidáte.

1. Nakonfigurujte úlohu tak, aby bezpečně zkopírovala soubor compose do složky *nasazení* v uzlu hlavního serveru Docker Swarm pomocí dříve nakonfigurovaného připojení SSH. Podrobnosti najdete na následující obrazovce.

    ![Služby Azure DevOps – vydání SCP](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

1. Nakonfigurujte druhou úlohu `docker` pro `docker-compose` spuštění příkazu bash a příkazy v hlavním uzlu. Podrobnosti najdete na následující obrazovce.

    ![Azure DevOps Services – bash vydání](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    Příkaz provedený na hlavním serveru používá cli Dockeru a příkazka příkazu Kdocker-compose k provedení následujících úkolů:

   - Přihlášení do registru kontejneru Azure (používá tři sestavení variab'les, které jsou definovány na kartě **Proměnné)**
   - Definujte **proměnnou DOCKER_HOST** pro práci s koncovým bodem Swarm (:2375)
   - Přejděte do složky *nasazení,* která byla vytvořena předchozí úlohou zabezpečeného kopírování a obsahuje soubor docker-compose.yml 
   - Spouštějte `docker-compose` příkazy, které vytahují nové bitové kopie, zastavte služby, odeberte služby a vytvořte kontejnery.

     >[!IMPORTANT]
     > Jak je znázorněno na předchozí obrazovce, ponechte **zaškrtávací políčko Selhání na STDERR** nezaškrtnuté. Toto je důležité `docker-compose` nastavení, protože vytiskne několik diagnostických zpráv, jako jsou například kontejnery zastavení nebo odstranění, na výstup standardní chyby. Pokud zaškrtnete políčko, Azure DevOps Services hlásí, že chyby došlo během vydání, i když vše půjde dobře.
     >
1. Uložte tento nový kanál vydání.


>[!NOTE]
>Toto nasazení zahrnuje některé prostoje, protože jsme zastavení staré služby a spuštění nové. Je možné se tomu vyhnout provedením modrozeleného nasazení.
>

## <a name="step-4-test-the-cicd-pipeline"></a>Krok 4. Testování kanálu CI/CD

Nyní, když jste hotovi s konfigurací, je čas otestovat tento nový kanál CI/CD. Nejjednodušší způsob, jak ji otestovat, je aktualizovat zdrojový kód a potvrdit změny do úložiště GitHub. Několik sekund po nabízení kódu se zobrazí nové sestavení spuštěné ve službách Azure DevOps Services. Po úspěšném dokončení se aktivuje nová verze a nasadí novou verzi aplikace v clusteru služby Azure Container Service.

## <a name="next-steps"></a>Další kroky

* Další informace o CI/CD se službami Azure DevOps Services najdete v článku [dokumentace k azure kanálům.](/azure/devops/pipelines/?view=azure-devops)
