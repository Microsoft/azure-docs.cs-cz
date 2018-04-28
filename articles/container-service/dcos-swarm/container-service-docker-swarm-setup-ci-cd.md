---
title: CI/CD s Azure Container Service a Swarm
description: Dostat nepřetržitě aplikace .NET Core více kontejnerů pomocí Docker Swarm, registru kontejner Azure a Visual Studio Team Services pomocí Azure Container Service
services: container-service
author: jcorioland
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: 81a07fdfe1c862bc30fb9d567db9a393c0610990
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-visual-studio-team-services"></a>Úplné kanálu CI nebo CD pro nasazení aplikace s více kontejnerů v Azure Container Service pomocí nástroje Docker Swarm, pomocí sady Visual Studio Team Services

Jeden z největších problémů při vývoji moderní aplikace pro cloud je schopnost poskytovat nepřetržitě tyto aplikace. V tomto článku zjistěte, jak implementovat úplné průběžnou integraci a nasazení (CI/CD) kanálu Azure Container Service pomocí Docker Swarm, registru kontejner Azure a Visual Studio Team Services sestavení a správa verzí.

Tento článek vychází jednoduchou aplikaci, k dispozici na [Githubu](https://github.com/jcorioland/MyShop/tree/acs-docs), vyvinuté pomocí ASP.NET Core. Aplikace se skládá ze čtyř různých služeb: tři webového rozhraní API a jeden web front-endu:

![MyShop ukázkové aplikace](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

Cílem je zajistit tuto aplikaci nepřetržitě v clusteru Docker Swarm, pomocí sady Visual Studio Team Services. Následující obrázek podrobnosti tohoto kanálu nastavené průběžné doručování:

![MyShop ukázkové aplikace](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

Následuje stručné vysvětlení kroky:

1. Změny kódu se zaměřuje na úložiště zdrojového kódu (tady Githubu) 
2. GitHub aktivuje build ve Visual Studio Team Services 
3. Visual Studio Team Services získá nejnovější verzi zdroje a vytvoří všechny bitové kopie, které tvoří aplikace 
4. Visual Studio Team Services doručí každé bitové kopie do registru Docker vytvořené pomocí služby Azure kontejneru registru 
5. Visual Studio Team Services aktivuje novou verzí 
6. Verze spouští některé příkazy na hlavního uzlu clusteru Azure container service pomocí protokolu SSH 
7. Vrátí nejnovější verzi bitové kopie docker Swarm v clusteru 
8. Nová verze aplikace se nasazuje pomocí Docker Compose 

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu, budete muset provést následující úlohy:

- [Vytvoření clusteru Swarm v Azure Container Service](container-service-deployment.md)
- [Propojení s clusterem Swarm ve službě Azure Container Service](../container-service-connect.md)
- [Vytvoření služby Azure kontejneru registru](../../container-registry/container-registry-get-started-portal.md)
- [Máte účet a tým projekt Visual Studio Team Services vytvořen](https://www.visualstudio.com/en-us/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)
- [Větev úložiště GitHub ke svému účtu GitHub](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Budete také potřebovat počítače Ubuntu (14.04 a 16.04) s Docker nainstalována. Tento počítač používá Visual Studio Team Services během procesy sestavení a verze. Jeden způsob, jak vytvořit tento počítač je používat k dispozici v bitovou kopii [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/canonicalandmsopentech/dockeronubuntuserver1404lts/). 

## <a name="step-1-configure-your-visual-studio-team-services-account"></a>Krok 1: Konfigurace účtu Visual Studio Team Services 

V této části můžete nakonfigurovat účet Visual Studio Team Services.

### <a name="configure-a-visual-studio-team-services-linux-build-agent"></a>Konfigurace agenta pro Visual Studio Team Services Linux sestavení

K vytvoření imagí Dockeru a vkládání těchto bitových kopií do registru kontejner Azure ze sestavení sady Visual Studio Team Services, potřebujete zaregistrovat agenta systému Linux. Máte tyto možnosti instalace:

* [Nasazení agenta v systému Linux](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Spusťte služby VSTS agenta pomocí Docker](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-vsts-extension"></a>Nainstalujte rozšíření služby VSTS integrace Docker

Společnost Microsoft poskytuje rozšíření služby VSTS práci s Docker v sestavení a verze procesy. Toto rozšíření je k dispozici v [služby VSTS Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker). Klikněte na tlačítko **nainstalovat** k účtu služby VSTS přidat tuto příponu:

![Nainstalujte integrace Dockeru](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

Zobrazí se výzva k připojení k účtu služby VSTS pomocí svých přihlašovacích údajů. 

### <a name="connect-visual-studio-team-services-and-github"></a>Připojit Visual Studio Team Services a GitHub

Nastavte připojení mezi projektu služby VSTS a účtu Githubu.

1. V projektu Visual Studio Team Services, klikněte **nastavení** v panelu nástrojů a vyberte ikonu **služby**.

    ![Visual Studio Team Services - externí připojení](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

2. Na levé straně klikněte na tlačítko **nový koncový bod služby** > **Githubu**.

    ![Visual Studio Team Services - GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

3. Chcete-li autorizovat služby VSTS pro práci s účtu Githubu, klikněte na tlačítko **Authorize** a postupujte podle pokynů v okně, které se otevře.

    ![Visual Studio Team Services - autorizovat Githubu](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-vsts-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Služby VSTS připojení k registru kontejner Azure a cluster Azure Container Service

Poslední kroky před získáním do kanálu CI/CD jsou nakonfigurovat externí připojení k registru systému kontejneru a clusteru Docker Swarm v Azure. 

1. V **služby** nastavení projektu Visual Studio Team Services přidat koncový bod služby typu **Docker registru**. 

2. V místní nabídce, která otevře zadejte adresu URL a pověření registru systému Windows Azure kontejneru.

    ![Visual Studio Team Services - Docker registru](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

3. Pro cluster Docker Swarm, přidání koncového bodu typu **SSH**. Zadejte informace o clusteru Swarm připojení SSH.

    ![Visual Studio Team Services - SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

Všechny konfigurace se provádí teď. V dalších krocích můžete vytvořit kanál CI/CD, který vytvoří a nasadí aplikaci do clusteru Docker Swarm. 

## <a name="step-2-create-the-build-definition"></a>Krok 2: Vytvoření definici sestavení

V tomto kroku můžete nastavit definitionfor sestavení projektu služby VSTS a definovat sestavení pracovního postupu pro kontejner obrázků

### <a name="initial-definition-setup"></a>Definice počáteční instalace

1. K vytvoření definice sestavení, připojení k vašemu projektu Visual Studio Team Services a klikněte na tlačítko **sestavení a verze**. 

2. V **sestavení definice** klikněte na tlačítko **+ nový**. Vyberte **prázdný** šablony.

    ![Visual Studio Team Services – nové sestavení definice](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

3. Konfigurace nového sestavení se zdrojem úložiště Githubu, zkontrolujte **průběžnou integraci**a vyberte frontu agenta, kde jste zaregistrovali Linux agent. Klikněte na tlačítko **vytvořit** k vytvoření definice sestavení.

    ![Visual Studio Team Services – vytvoření definice sestavení](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

4. Na **definice sestavení** stránky, poprvé otevřete **úložiště** a nakonfigurujte sestavení používat pokračovatelem MyShop projekt, který jste vytvořili v požadavky. Ujistěte se, že jste vybrali *acs dokumentace* jako **výchozí větev**.

    ![Visual Studio Team Services - úložiště konfigurace sestavení](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

5. Na **aktivační události** nakonfigurujte sestavení, aby se spouštěly po každém potvrzení. Vyberte **průběžnou integraci** a **dávky změny**.

    ![Visual Studio Team Services - konfigurace aktivační události sestavení](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>Definice pracovního postupu sestavení
Další kroky definovat sestavení pracovního postupu. Pět kontejneru Image na sestavení pro *MyShop* aplikace. Každé bitové kopie je sestaven pomocí soubor Docker umístěný ve složce projektu:

* ProductsApi
* Proxy server
* RatingsApi
* RecommandationsApi
* ShopFront

Je nutné přidat dva kroky Docker pro každé bitové kopie, jeden vytvořit bitovou kopii a jeden pro uložení image v registru kontejner Azure. 

1. Chcete-li přidat krok v pracovním postupu sestavení, klikněte na tlačítko **+ přidat krok sestavení** a vyberte **Docker**.

    ![Visual Studio Team Services - přidat kroky sestavení](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

2. Pro každé bitové kopie, nakonfigurovat jeden krok, který používá `docker build` příkaz.

    ![Visual Studio Team Services - Docker sestavení](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    Pro operaci sestavení, vyberte kontejner Azure registr, **vytvořit bitovou kopii** akce a soubor Docker, která definuje každé bitové kopie. Nastavte **sestavení kontextu** jako soubor Docker kořenový adresář a definujte **název bitové kopie**. 
    
    Jak je znázorněno na předchozí obrazovce, spusťte název bitové kopie s identifikátorem URI registru systému Windows Azure kontejneru. (Můžete také použijete sestavení proměnnou o parametrizaci značka obrázku, jako je například identifikátor sestavení v tomto příkladu.)

3. Pro každé bitové kopie, nakonfigurujte druhý krok, který používá `docker push` příkaz.

    ![Visual Studio Team Services - nabízené Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    Pomocí operace push, vyberte možnost kontejner Azure registr, **Push bitovou kopii** akce a zadejte **název bitové kopie** který je vytvořen v předchozím kroku.

4. Po dokončení konfigurace sestavení a nabízených kroky pro každý z pěti bitové kopie, přidejte dva další kroky v pracovním postupu sestavení.

    a. Úlohu příkazového řádku, která používá skript bash nahradit *BuildNumber* sestavení výskyt v soubor docker-compose.yml s aktuálním ID. V následující obrazovku podrobnosti.

    ![Visual Studio Team Services - vytvářené aktualizace souboru](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. Úloha, která zahodí aktualizovaný soubor vytvářené jako sestavení artefaktů, takže ho můžete použít ve verzi. V následující obrazovku podrobnosti.

    ![Visual Studio Team Services - publikování vytvářené souboru](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

5. Klikněte na tlačítko **Uložit** a název vaší definice sestavení.

## <a name="step-3-create-the-release-definition"></a>Krok 3: Vytvoření definice verze

Visual Studio Team Services umožňuje [Správa verzí v různých prostředích](https://www.visualstudio.com/team-services/release-management/). Průběžné nasazování, abyste měli jistotu, že vaše aplikace je nasazená na vaše různých prostředích (například vývojářů, testovací, předprodukční a produkční) smooth způsobem můžete povolit. Můžete vytvořit nové prostředí, které představuje váš cluster Azure Container Service Docker Swarm.

![Visual Studio Team Services - verze služby ACS](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Instalační program původní verze

1. K vytvoření definice vydání, klikněte na tlačítko **verze** > **+ verze**

2. Chcete-li nastavit zdroj artefaktů, klikněte na tlačítko **artefakty** > **odkaz na zdroj artefaktů**. Tato nová verze definice zde odkaz na sestavení, který jste zadali v předchozím kroku. Díky tomu je k dispozici v procesu verze soubor docker-compose.yml.

    ![Visual Studio Team Services - artefakty verze](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

3. Konfigurace verze aktivační událost, klikněte na tlačítko **aktivační události** a vyberte **průběžné nasazování**. Nastavte aktivační události na stejném zdroji artefaktů. Toto nastavení zajistí, že začne novou verzi také sestavení se dokončí úspěšně.

    ![Visual Studio Team Services - verze aktivační události](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>Zadejte verzi pracovního postupu

Verze pracovní postup se skládá z dvě úlohy, které přidáte.

1. Nakonfigurujte úlohu bezpečně zkopírovat soubor vytvářené *nasazení* složky na Docker Swarm hlavního uzlu pomocí připojení SSH jste nakonfigurovali dříve. V následující obrazovku podrobnosti.

    ![Visual Studio Team Services - verze spojovací bod služby](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

2. Nakonfigurujte druhý úlohy ke spuštění příkazu bash ke spuštění `docker` a `docker-compose` příkazů na hlavní uzel. V následující obrazovku podrobnosti.

    ![Visual Studio Team Services - Bash verze](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    Příkaz provést v hlavní použijte rozhraní příkazového řádku Dockeru a rozhraní příkazového řádku Docker Compose k provádění následujících úloh:

    - Přihlášení k Azure kontejneru registru (používá tři variab'les sestavení, která jsou definována v **proměnné** karty)
    - Definování **DOCKER_HOST** proměnnou pro práci s koncovému bodu Swarm (: 2375)
    - Přejděte na *nasazení* složky, který byl vytvořen předchozí zabezpečené kopírování úlohy a který obsahuje soubor docker-compose.yml 
    - Spuštění `docker-compose` příkazy, které nových bitových kopií pro vyžádání obsahu zastavení služeb, odeberte služby a vytvoření kontejnerů.

    >[!IMPORTANT]
    > Jak je znázorněno na předchozí obrazovce, ponechte **nezdaří do datového proudu STDERR** nezaškrtnuté políčko. To je důležité nastavit, protože `docker-compose` vytiskne několik diagnostické zprávy, jako jsou kontejnery se zastavit nebo odstraňuje na standardní chyba výstup. Pokud zaškrtnete políčko, Visual Studio Team Services hlásí, že nastaly chyby během verzí, i v případě, že všechno proběhne správně.
    >
3. Uložte tuto novou verzi definici.


>[!NOTE]
>Toto nasazení obsahuje výpadky, protože jsme zastavení staré služeb a systémem novým. Je možné, abyste tomu předešli díky blue zelená nasazení.
>

## <a name="step-4-test-the-cicd-pipeline"></a>Krok 4. Testování CI/CD kanálu

Teď, když jste hotovi s konfigurací, je otestovat, tento nový kanál CI/CD. Aktualizujte zdrojový kód a provést změny do úložiště GitHub je nejjednodušší způsob, jak otestovat. Několik sekund poté, co push kód, zobrazí se nové sestavení spuštěná ve Visual Studio Team Services. Po úspěšném dokončení novou verzi se spustí a provede nasazení nové verze aplikace v clusteru Azure Container Service.

## <a name="next-steps"></a>Další kroky

* Další informace o CI/CD s Visual Studio Team Services najdete v tématu [služby VSTS sestavení přehled](https://www.visualstudio.com/docs/build/overview).
