---
title: ZASTARALÉ CI/CD s Azure Container Service a Swarm
description: Použití Azure Container Service s Docker Swarm, Azure Container Registry a Azure DevOps pro průběžné doručování aplikace .NET Core s více kontejnery
author: jcorioland
ms.service: container-service
ms.topic: conceptual
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: 11a6debe735459b617f6f93c3f67a32350dd4623
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549049"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-azure-devops-services"></a>ZASTARALÉ Úplný kanál CI/CD pro nasazení aplikace s více kontejnery na Azure Container Service s využitím Docker Swarm pomocí Azure DevOps Services

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Jedním z největších problémů při vývoji moderních aplikací pro Cloud je schopnost doručovat tyto aplikace průběžně. V tomto článku se dozvíte, jak implementovat kompletní kanál průběžné integrace a nasazování (CI/CD) pomocí Azure Container Service s Docker Swarm, Azure Container Registry a správou Azure Pipelines.


![Ukázková aplikace MyShop](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

Cílem je doručovat tuto aplikaci průběžně v clusteru Docker Swarm pomocí Azure DevOps Services. Následující obrázek podrobně popisuje tento kanál průběžného doručování:

![Ukázková aplikace MyShop](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

Zde je stručné vysvětlení kroků:

1. Změny kódu se potvrdí do úložiště zdrojového kódu (tady, GitHub). 
1. GitHub spustí sestavení v Azure DevOps Services 
1. Azure DevOps Services získá nejnovější verzi zdrojů a vytvoří všechny image, které tvoří aplikaci. 
1. Azure DevOps Services vloží jednotlivé image do registru Docker vytvořeného pomocí služby Azure Container Registry. 
1. Azure DevOps Services aktivuje novou verzi. 
1. Vydaná verze spouští některé příkazy pomocí SSH v hlavním uzlu clusteru Azure Container Service. 
1. Docker Swarm v clusteru vyžádá nejnovější verzi imagí. 
1. Nová verze aplikace je nasazena pomocí Docker Compose 

## <a name="prerequisites"></a>Požadavky

Než začnete tento kurz, musíte provést následující úlohy:

- [Vytvoření clusteru Swarm v Azure Container Service](container-service-deployment.md)
- [Propojení s clusterem Swarm ve službě Azure Container Service](../container-service-connect.md)
- [Vytvoření služby Azure Container Registry](../../container-registry/container-registry-get-started-portal.md)
- [Vytvoření organizace a projektu Azure DevOps Services](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Rozvětvení úložiště GitHubu do vašeho účtu GitHubu](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Budete také potřebovat počítač s Ubuntu (14,04 nebo 16,04) s nainstalovaným Docker. Tento počítač používá Azure DevOps Services během Azure Pipelines procesů. Jedním ze způsobů, jak vytvořit tento počítač, je použití bitové kopie dostupné v Azure Marketplace. 

## <a name="step-1-configure-your-azure-devops-services-organization"></a>Krok 1: konfigurace Azure DevOps Services organizace 

V této části nakonfigurujete Azure DevOps Services organizaci.

### <a name="configure-an-azure-devops-services-linux-build-agent"></a>Konfigurace agenta sestavení Azure DevOps Services Linux

Pokud chcete vytvořit image Docker a vložit tyto image do služby Azure Container Registry z Azure DevOps Services buildu, musíte zaregistrovat agenta pro Linux. Máte tyto možnosti instalace:

* [Nasazení agenta v systému Linux](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Použití Docker ke spuštění agenta Azure DevOps Services](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-azure-devops-services-extension"></a>Instalace rozšíření Docker Integration Azure DevOps Services

Microsoft poskytuje Azure DevOps Services rozšíření pro práci s Docker v procesech Azure Pipelines. Toto rozšíření je k dispozici na [webu Azure DevOps Services Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker). Kliknutím na **instalovat** přidejte toto rozšíření do vaší Azure DevOps Services organizace:

![Nainstalovat integraci Docker](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

Budete požádáni o připojení k vaší Azure DevOps Services organizaci pomocí vašich přihlašovacích údajů. 

### <a name="connect-azure-devops-services-and-github"></a>Připojení Azure DevOps Services a GitHubu

Nastavte připojení mezi Azure DevOps Services projektu a vaším účtem GitHub.

1. V projektu Azure DevOps Services klikněte na ikonu **Nastavení** na panelu nástrojů a vyberte **služby**.

    ![Azure DevOps Services – externí připojení](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

1. Na levé straně klikněte na **Nový koncový bod služby** > **GitHub**.

    ![Azure DevOps Services – GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

1. Pokud chcete autorizovat Azure DevOps Services pracovat s vaším účtem GitHubu, klikněte na **autorizovat** a postupujte podle pokynů v okně, které se otevře.

    ![Azure DevOps Services – autorizovat GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-azure-devops-services-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Připojení Azure DevOps Services ke službě Azure Container registry a Azure Container Service clusteru

Poslední kroky před tím, než se dostanete do kanálu CI/CD, jsou konfigurace externích připojení ke svému registru kontejnerů a clusteru Docker Swarm v Azure. 

1. V nastavení **služeb** projektu Azure DevOps Services přidejte koncový bod služby typu **Docker Registry**. 

1. V místní nabídce, která se otevře, zadejte adresu URL a přihlašovací údaje služby Azure Container Registry.

    ![Registr Azure DevOps Services-Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

1. Pro cluster Docker Swarm přidejte koncový bod typu **SSH**. Pak zadejte informace o připojení SSH vašeho clusteru Swarm.

    ![Azure DevOps Services – SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

Všechny konfigurace se provedly nyní. V dalších krocích vytvoříte kanál CI/CD, který sestaví a nasadí aplikaci do clusteru Docker Swarm. 

## <a name="step-2-create-the-build-pipeline"></a>Krok 2: vytvoření kanálu sestavení

V tomto kroku nastavíte pro svůj projekt Azure DevOps Services kanál sestavení a definujete pracovní postup sestavení pro Image kontejnerů.

### <a name="initial-pipeline-setup"></a>Počáteční nastavení kanálu

1. Pokud chcete vytvořit kanál sestavení, připojte se k projektu Azure DevOps Services a klikněte na **sestavit & vydání**. 

1. V části **definice sestavení** klikněte na **+ Nový**. Vyberte **prázdnou** šablonu.

    ![Azure DevOps – nový kanál sestavení](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

1. Nakonfigurujte nové sestavení se zdrojem úložiště GitHub, zaškrtněte možnost **průběžná integrace**a vyberte frontu agentů, do které jste zaregistrovali agenta pro Linux. Kliknutím na **vytvořit** vytvořte kanál sestavení.

    ![Azure DevOps Services – vytvoření kanálu sestavení](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

1. Na stránce **definice sestavení** nejprve otevřete kartu **úložiště** a nakonfigurujte sestavení tak, aby používalo rozvětvení projektu MyShop, který jste vytvořili v části požadavky. Ujistěte se, že jako **výchozí větev**vyberete možnost *ACS-docs* .

    ![Azure DevOps Services – konfigurace úložiště sestavení](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

1. Na kartě **triggery** nakonfigurujte sestavení tak, aby se aktivovalo po každém potvrzení. Vyberte **průběžnou integraci** a **změny v dávce**.

    ![Azure DevOps Services – konfigurace aktivační události sestavení](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>Definovat pracovní postup sestavení
Další kroky definují pracovní postup sestavení. K sestavení aplikace *MyShop* je k dispozici pět imagí kontejneru. Každý obrázek je sestaven pomocí souboru Dockerfile nacházející se ve složkách projektu:

* ProductsApi
* Proxy server
* RatingsApi
* RecommendationsApi
* ShopFront

Musíte přidat dva kroky Docker pro každý obrázek, jednu pro sestavení image a jednu pro vložení image do služby Azure Container Registry. 

1. Pokud chcete přidat krok do pracovního postupu sestavení, klikněte na **+ Přidat krok sestavení** a vyberte **Docker**.

    ![Azure DevOps Services – přidání kroků sestavení](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

1. Pro každý obrázek nakonfigurujte jeden krok, který používá příkaz `docker build`.

    ![Sestavení Azure DevOps Services-Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    V případě operace sestavení vyberte svůj registr kontejneru Azure, akci **sestavení image** a souboru Dockerfile, která definuje jednotlivé obrázky. Nastavte **kontext sestavení** jako kořenový adresář souboru Dockerfile a zadejte **název bitové kopie**. 
    
    Jak je znázorněno na předchozí obrazovce, spusťte název image s identifikátorem URI vašeho registru kontejneru Azure. (Můžete také použít proměnnou sestavení pro zobrazení značky obrázku, jako je například identifikátor sestavení v tomto příkladu.)

1. Pro každý obrázek nakonfigurujte druhý krok, který používá příkaz `docker push`.

    ![Nabízená oznámení Azure DevOps Services Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    Pro operaci Push vyberte svůj registr kontejneru Azure, akci **Odeslat obrázek** a zadejte **název Image** , která je vytvořená v předchozím kroku.

1. Po nakonfigurování kroků sestavení a vložení pro každý z pěti imagí přidejte do pracovního postupu sestavení dva další kroky.

    a. Úloha příkazového řádku, která používá skript bash k nahrazení *BuildNumber* výskytu v souboru Docker-Compose. yml s ID aktuálního buildu. Podrobnosti najdete na následující obrazovce.

    ![Azure DevOps Services – aktualizovat soubor s psaním](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. Úkol, který uvolní aktualizovaný soubor pro sestavení jako artefakt sestavení, aby jej bylo možné použít v této verzi. Podrobnosti najdete na následující obrazovce.

    ![Azure DevOps Services – publikování souboru s psaním](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

1. Klikněte na **Uložit** a pojmenujte svůj kanál sestavení.

## <a name="step-3-create-the-release-pipeline"></a>Krok 3: vytvoření kanálu pro vydávání verzí

Azure DevOps Services umožňuje [správu vydaných verzí napříč prostředími](https://www.visualstudio.com/team-services/release-management/). Průběžné nasazování můžete povolit, abyste se ujistili, že vaše aplikace je nasazená v různých prostředích (například vývoj, testování, předprodukční a produkční prostředí) plynule. Můžete vytvořit nové prostředí, které představuje váš cluster Azure Container Service Docker Swarm.

![Azure DevOps Services – vydání na službu ACS](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Instalace počáteční verze

1. Chcete-li vytvořit kanál verze, klikněte na položku **verze** >  **+ verze**

1. Chcete-li nakonfigurovat zdroj artefaktů, klikněte na **artefakty** > **propojit zdroj artefaktů**. Zde propojte tento nový kanál verze se sestavením, které jste definovali v předchozím kroku. Tím dojde k tomu, že soubor Docker-Compose. yml je k dispozici v procesu vydávání verzí.

    ![Azure DevOps Services – artefakty vydaných verzí](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

1. Pokud chcete nakonfigurovat aktivační událost vydání, klikněte na **triggery** a vyberte **průběžné nasazování**. Nastavte Trigger na stejném zdroji artefaktů. Toto nastavení zajistí, že se nová verze začne hned po úspěšném dokončení sestavení.

    ![Aktivační události Azure DevOps Services-Release](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>Definovat pracovní postup vydání

Pracovní postup vydání se skládá ze dvou úloh, které přidáte.

1. Nakonfigurujte úlohu pro bezpečné kopírování souboru s příponou do složky *Deploy* v hlavním uzlu Docker Swarm pomocí připojení SSH, které jste nakonfigurovali dříve. Podrobnosti najdete na následující obrazovce.

    ![Spojovací bod služby Azure DevOps Services-Release](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

1. Nakonfigurujte druhý úkol, který spustí příkaz bash ke spuštění příkazů `docker` a `docker-compose` na hlavním uzlu. Podrobnosti najdete na následující obrazovce.

    ![Azure DevOps Services verze bash](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    Příkaz spuštěný v hlavním serveru používá rozhraní Docker CLI a rozhraní příkazového řádku Docker, které provádí následující úlohy:

   - Přihlaste se ke službě Azure Container Registry (používá tři sestavení variab'les, která jsou definovaná na kartě **proměnné** ).
   - Zadejte **DOCKER_HOST** proměnnou pro práci s koncovým bodem Swarm (: 2375)
   - Přejděte do složky pro *nasazení* , kterou vytvořila předchozí úloha zabezpečeného kopírování a která obsahuje soubor Docker-Compose. yml. 
   - Spouštět `docker-compose` příkazy, které nastavují nové image, zastaví služby, odstraní služby a vytvoří kontejnery.

     >[!IMPORTANT]
     > Jak je znázorněno na předchozí obrazovce, nechejte políčko **při selhání u stderr** nezaškrtnuté. Toto je důležité nastavení, protože `docker-compose` tiskne několik diagnostických zpráv, jako je například zastavení nebo odstranění kontejnerů, na standardním výstupu chyby. Pokud zaškrtnete toto políčko, Azure DevOps Services oznamuje, že při vydání došlo k chybám, i když vše bude dobré.
     >
1. Uložte tento nový kanál pro vydávání verzí.


>[!NOTE]
>Toto nasazení zahrnuje nějaké výpadky, protože zastavuje staré služby a spustíme nové. K tomu je možné se vyhnout provedením nasazení Blue-zelená.
>

## <a name="step-4-test-the-cicd-pipeline"></a>Krok 4. Testování kanálu CI/CD

Teď, když jste s konfigurací hotovi, je čas otestovat tento nový kanál CI/CD. Nejjednodušší způsob, jak ho otestovat, je aktualizovat zdrojový kód a potvrdit změny do úložiště GitHub. Několik sekund po vložení kódu se zobrazí nové sestavení běžící v Azure DevOps Services. Po úspěšném dokončení se aktivuje nová vydaná verze a nasadí se nová verze aplikace v clusteru Azure Container Service.

## <a name="next-steps"></a>Další kroky

* Další informace o CI/CD s Azure DevOps Services najdete v článku [dokumentace Azure Pipelines](/azure/devops/pipelines/?view=azure-devops) .
