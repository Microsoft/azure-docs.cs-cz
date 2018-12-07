---
title: (NEPOUŽÍVANÉ) CI/CD s Azure Container Service a Swarm
description: Průběžné nasazení aplikace .NET Core pomocí Azure Container Service s Docker Swarm, Azure Container Registry a Azure DevOps
services: container-service
author: jcorioland
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: db0a16fa44dd23cbc32159889fe8b8ec28c77a5f
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52992548"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-azure-devops-services"></a>(NEPOUŽÍVANÉ) Úplný kanál CI/CD pro nasazení aplikace ve službě Azure Container Service pomocí nástroje Docker Swarm pomocí služby Azure DevOps

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Jedním z největších problémů při vývoji moderních aplikací pro cloud je schopnost průběžné nasazení těchto aplikací. V tomto článku se dozvíte, jak k provádění úplné průběžné integrace a nasazování (CI/CD) kanálů pomocí služby Azure Container Service pomocí Docker Swarm, Azure Container Registry a Azure kanály správy.

Tento článek je založen na jednoduchou aplikaci k dispozici na [Githubu](https://github.com/jcorioland/MyShop/tree/acs-docs), vyvinuté pomocí ASP.NET Core. Aplikace se skládá ze čtyř různých služeb: tři webové rozhraní API a jednoho webového front-endu:

![MyShop ukázkové aplikace](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

Cílem je průběžné nasazení této aplikace v clusteru Docker Swarm, pomocí služby Azure DevOps. Následující obrázek podrobně popisuje tento kanál průběžného doručování:

![MyShop ukázkové aplikace](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

Tady je stručný postup:

1. Změny kódu se zaměřuje na úložiště zdrojového kódu (tady Githubu) 
1. GitHub aktivuje sestavení ve službách Azure DevOps 
1. Získá nejnovější verzi zdroje a sestaví všechny Image, které tvoří aplikaci služby Azure DevOps 
1. Azure DevOps služby nabízených oznámení každé image do registru Dockeru vytvořené pomocí služby Azure Container Registry 
1. Spustí novou verzi služby Azure DevOps 
1. Běží na verzi některé příkazy pomocí SSH na hlavní uzel clusteru Azure container service 
1. Docker Swarm v clusteru si vyžádá nejnovější verzi Image 
1. Nová verze aplikace se nasadí pomocí Docker Compose 

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu, budete muset provést následující úlohy:

- [Vytvoření clusteru Swarm v Azure Container Service](container-service-deployment.md)
- [Propojení s clusterem Swarm ve službě Azure Container Service](../container-service-connect.md)
- [Vytvoření služby Azure container registry](../../container-registry/container-registry-get-started-portal.md)
- [Mít organizace služby Azure DevOps a vytvoření projektu](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Rozvětvení úložiště GitHub do účtu Githubu](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Budete také potřebovat počítače s Ubuntu (14.04 a 16.04) s nainstalovaným Dockerem. Tento počítač používá Azure DevOps služby během procesů kanály Azure. Jeden způsob, jak vytvořit tento počítač je chcete použít image dostupných v [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/canonicalandmsopentech/dockeronubuntuserver1404lts/). 

## <a name="step-1-configure-your-azure-devops-services-organization"></a>Krok 1: Konfigurace vaší organizace služby Azure DevOps 

V této části nakonfigurujete vaše organizace služby Azure DevOps.

### <a name="configure-an-azure-devops-services-linux-build-agent"></a>Konfigurace agenta sestavení služby DevOps Azure s Linuxem

Můžete vytvořit Image Dockeru a ze Azure DevOps služby sestavení těchto imagí do služby Azure container registry, musíte registraci agenta pro Linux. Máte tyto možnosti instalace:

* [Nasazení agenta v Linuxu](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Použití Docker ke spuštění agenta služeb Azure DevOps](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-azure-devops-services-extension"></a>Instalace rozšíření Dockeru integrace Azure DevOps služby

Společnost Microsoft poskytuje Azure DevOps služby rozšíření pro práci s Dockerem v Azure kanály procesy. Toto rozšíření je k dispozici v [Azure DevOps služby Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker). Klikněte na tlačítko **nainstalovat** přidáte toto rozšíření pro vaši organizaci Azure DevOps služby:

![Instalace integrace Dockeru](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

Zobrazí se výzva k připojení k organizaci služeb Azure DevOps pomocí svých přihlašovacích údajů. 

### <a name="connect-azure-devops-services-and-github"></a>Připojení Azure DevOps služby a Githubu

Nastavte připojení mezi váš projekt Azure DevOps služby a účtu GitHub.

1. V projektu Azure DevOps služby, klikněte na tlačítko **nastavení** v panelu nástrojů a vyberte ikonu **služby**.

    ![Služby Azure DevOps – externí připojení](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

1. Na levé straně klikněte na tlačítko **nový koncový bod služby** > **Githubu**.

    ![Služby Azure DevOps – GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

1. Chcete-li povolit službám Azure DevOps pro práci s vaším účtem Githubu, klikněte na tlačítko **Authorize** a postupujte podle pokynů v okně, které se otevře.

    ![Povolit službám Azure DevOps – GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-azure-devops-services-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Připojení k Azure container registry a clusteru Azure Container Service služeb Azure DevOps

Poslední kroky před získáním do kanálu CI/CD se konfigurovat externí připojení k registru kontejneru a cluster Docker Swarm v Azure. 

1. V **služby** nastavení projekt Azure DevOps služby přidat koncový bod služby typu **registru Dockeru**. 

1. V místní nabídce, která se otevře zadejte adresu URL a pověření svůj registr kontejneru Azure.

    ![Služby Azure DevOps – registr Dockeru](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

1. Pro cluster Docker Swarm přidat koncový bod typu **SSH**. Zadejte informace o připojení SSH clusteru Swarm.

    ![Azure DevOps služby - SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

Všechny konfigurace se teď provádí. V dalších krocích vytvoříte kanál CI/CD, který vytvoří a nasadí aplikaci do clusteru Docker Swarm. 

## <a name="step-2-create-the-build-pipeline"></a>Krok 2: Vytvoření kanálu sestavení

V tomto kroku nastavíte kanálu sestavení pro projekt Azure DevOps služby a definice pracovního postupu sestavení imagí kontejnerů

### <a name="initial-pipeline-setup"></a>Nastavení počáteční kanálu

1. K vytvoření kanálu sestavení, připojte se k projektu služby Azure DevOps a klikněte na tlačítko **sestavení a vydání**. 

1. V **definice sestavení** klikněte na tlačítko **+ nová**. Vyberte **prázdný** šablony.

    ![Vytvoření kanálu Azure DevOps – nové](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

1. Konfigurace nového sestavení se zdrojem úložiště GitHub, zkontrolujte **kontinuální integrace**a vyberte frontu agenta, kde jste zaregistrovali svého linuxového agenta. Klikněte na tlačítko **vytvořit** k vytvoření kanálu sestavení.

    ![Kanály sestavení pro služby Azure DevOps –](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

1. Na **definice sestavení** stránce, nejdřív otevřete **úložiště** kartu a konfigurace sestavení, forku MyShop projekt, který jste vytvořili v rámci požadavků. Ujistěte se, že jste vybrali *acs-docs* jako **výchozí větev**.

    ![Služby Azure DevOps – úložiště konfigurace sestavení](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

1. Na **triggery** kartu, nakonfigurujte sestavení aktivovat po každém potvrzení. Vyberte **kontinuální integrace** a **Batch změny**.

    ![Služby Azure DevOps – konfigurace aktivační události sestavení](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>Definování pracovního postupu sestavení
Další kroky definování pracovního postupu sestavení. Existuje pět imagí kontejnerů k sestavení pro *MyShop* aplikace. Každá image se sestavuje pomocí souboru Dockerfile umístěný ve složce projektu:

* ProductsApi
* Proxy server
* RatingsApi
* RecommandationsApi
* ShopFront

Musíte přidat dva kroky Dockeru pro každé bitové kopie, jeden pro sestavení image a jeden nasdílejte image do registru kontejneru Azure. 

1. Chcete-li přidat krok v pracovním postupu sestavení, klikněte na tlačítko **+ přidat krok sestavení** a vyberte **Docker**.

    ![Služby Azure DevOps – přidání kroků sestavení](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

1. Pro každý obrázek nakonfigurovat jeden krok, který používá `docker build` příkazu.

    ![Služby Azure DevOps – sestavení Dockeru](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    Pro operaci sestavení, vyberte svůj registr kontejneru Azure **sestavte image** akce a soubor Dockerfile, který definuje každá image. Nastavte **kontext sestavení** jako soubor Dockerfile kořenový adresář a definovat **název Image**. 
    
    Jak je znázorněno na předchozí obrazovce, začněte název bitové kopie s identifikátorem URI svůj registr kontejneru Azure. (Můžete také použít proměnnou sestavení parametrizovat značka obrázku, jako je například identifikátor sestavení v tomto příkladu).

1. Každé bitové kopie, nakonfigurujte druhý krok, který používá `docker push` příkazu.

    ![Služby Azure DevOps – Docker Push](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    Pomocí operace push, vyberte svůj registr kontejneru Azure **nasdílet image** akce a zadejte **název Image** , která je vytvořená v předchozím kroku.

1. Po dokončení kroků sestavení a odeslání konfigurace pro každý z pěti obrázků, přidejte další dva kroky v postupu sestavení.

    a. Úlohu příkazového řádku pomocí skriptu bash nahrazuje *BuildNumber* sestavení výskytu v souboru docker-compose.yml s aktuálním ID. Podívejte se na následující obrazovce podrobnosti.

    ![Služby Azure DevOps – psaní aktualizace souboru](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. Úloha, která sníží aktualizovaný soubor Compose jako artefakt sestavení, takže ho můžete použít ve vydané verzi. Podívejte se na následující obrazovce podrobnosti.

    ![Služby Azure DevOps – publikování vytvořit soubor](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

1. Klikněte na tlačítko **Uložit** a název vašeho kanálu sestavení.

## <a name="step-3-create-the-release-pipeline"></a>Krok 3: Vytvoření kanál pro vydávání verzí

Služby Azure DevOps vám umožní [správě vydávání verzí napříč prostředími](https://www.visualstudio.com/team-services/release-management/). Můžete povolit průběžné nasazování, abyste měli jistotu, že vaše aplikace bude nasazena v různých prostředích (třeba dev, test, předprodukčních a produkčních) smooth způsobem. Můžete vytvořit nové prostředí, která představuje váš cluster Azure Container Service Docker Swarm.

![Služby Azure DevOps – verze k ACS](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Nastavení počáteční verze

1. Chcete-li vytvořit kanál pro vydávání verzí, klikněte na tlačítko **verze** > **+ vydání**

1. Konfigurovat zdroj artefaktu, klikněte na tlačítko **artefakty** > **propojit zdroj artefaktu**. Tento nový kanál pro vydávání verzí, odkaz na sestavení, které jste definovali v předchozím kroku. Tímto způsobem, je k dispozici v procesu vydání soubor docker-compose.yml.

    ![Služby Azure DevOps – artefakty verzí](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

1. Konfigurace aktivační události verze, klikněte na tlačítko **triggery** a vyberte **průběžné nasazování**. Nastavením aktivační události na stejném zdroji artefaktů. Toto nastavení zajistí, že nová verze spustí ihned po úspěšném dokončení sestavení.

    ![Služby Azure DevOps – aktivační události vydané verze](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>Definování pracovního postupu verze

Pracovní postup vydávání se skládá ze dvou úkolů, které přidáte.

1. Konfigurace úloh a bezpečně zkopírovat soubor compose *nasazení* složky na Docker Swarm hlavního uzlu, pomocí připojení SSH, který jste nakonfigurovali dříve. Podívejte se na následující obrazovce podrobnosti.

    ![Služby Azure DevOps – verze spojovací bod služby](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

1. Nakonfigurujte druhý úkol ke spuštění příkazu bashe ke spuštění `docker` a `docker-compose` příkazy na hlavní uzel. Podívejte se na následující obrazovce podrobnosti.

    ![Bash služby Azure DevOps – verze](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    Příkaz provést na hlavní použití rozhraní příkazového řádku Dockeru a rozhraní příkazového řádku Docker-Compose provádět následující úlohy:

    - Přihlaste se k Azure container registry (používá tři variab'les sestavení, které jsou definovány v **proměnné** kartu)
    - Definovat **DOCKER_HOST** proměnné pro práci s koncovým bodům Swarmu (: 2375)
    - Přejděte *nasazení* složku, která byla vytvořena předchozí úlohou zabezpečené kopie, který obsahuje soubor docker-compose.yml 
    - Spustit `docker-compose` příkazy, které o přijetí změn nové Image zastavení služeb, odeberte služby a vytvoření kontejnerů.

    >[!IMPORTANT]
    > Jak je znázorněno na předchozí obrazovce, nechat **neúspěšné, když STDERR** nezaškrtnuté políčko. To je důležité nastavení, protože `docker-compose` vytiskne několik diagnostické zprávy, jako jsou kontejnery, zastavení nebo odstranění, standardního chybového výstupu. Pokud zaškrtnete políčko, sestavy služby Azure DevOps, že došlo k chybám při vydání, i pokud všechno proběhne správně.
    >
1. Uložte tento nový kanál pro vydávání verzí.


>[!NOTE]
>Toto nasazení obsahuje nějaké prostoje, protože jsme staré služeb a spuštění nové. Je možné se tomu chcete vyhnout provedením nasazení modrá zelená.
>

## <a name="step-4-test-the-cicd-pipeline"></a>Krok 4. Test kanálu CI/CD

Teď, když jste hotovi s konfigurací, je čas k otestování tohoto nového kanálu CI/CD. Nejjednodušší způsob, jak ji otestovat je aktualizovat zdrojový kód a potvrzení změn do vašeho úložiště GitHub. Několik sekund poté, co vložíte kód, zobrazí se nové sestavení spuštěná ve službách Azure DevOps. Po úspěšném dokončení novou verzi se aktivuje a nasadí novou verzi aplikace v clusteru Azure Container Service.

## <a name="next-steps"></a>Další kroky

* Další informace o CI/CD pomocí služeb Azure DevOps, najdete v článku [přehled Azure DevOps služby Build](https://www.visualstudio.com/docs/build/overview).
