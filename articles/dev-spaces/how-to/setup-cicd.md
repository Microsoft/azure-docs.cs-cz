---
title: Použití CI/CD s Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: conceptual
manager: yuvalm
description: Rychlý vývoj na platformě Kubernetes s využitím kontejnerů a mikroslužeb v Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kontejnery
ms.openlocfilehash: 983af0dd75e6ae62630c85d04ac3819c7e260439
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60687265"
---
# <a name="use-cicd-with-azure-dev-spaces"></a>CI/CD pomocí Azure Dev mezery

Tento článek vás provede nastavením průběžné integrace a nasazování (CI/CD) do Azure Kubernetes Service (AKS) mezerami Dev povolena. CI/CD a AKS umožňuje instalaci aktualizací aplikace má být nasazen automaticky pokaždé, když se Potvrdit kód se vloží do zdrojového úložiště. Pomocí CI/CD ve spojení s mezerami Dev povoleno clusteru je užitečné vzhledem k tomu, že ho můžete aktualizovat směrný plán aplikace pro tým pracovat.

![Příklad diagramu CI/CD](../media/common/ci-cd-simple.png)

Přestože tento článek vás provede s Azure DevOps, by stejné koncepty platí i pro systémy CI/CD, jako je Jenkins, TeamCity, atd.

## <a name="prerequisites"></a>Požadavky
* [Cluster Azure Kubernetes Service (AKS) s Azure Dev prostory povoleno](../get-started-netcore.md)
* [Nainstalované Azure CLI prostory vývoj](upgrade-tools.md)
* [Azure DevOps organizace s projektem](https://docs.microsoft.com/azure/devops/user-guide/sign-up-invite-teammates?view=vsts)
* [Azure Container Registry (ACR)](../../container-registry/container-registry-get-started-azure-cli.md)
    * Služba Azure Container Registry [účet správce](../../container-registry/container-registry-authentication.md#admin-account) podrobnosti, které jsou k dispozici
* [Povolit clusteru AKS na vyžádání ze služby Azure Container Registry](../../container-registry/container-registry-auth-aks.md)

## <a name="download-sample-code"></a>Stáhněte si ukázkový kód
Pro účely čas můžeme vytvořit fork našeho úložiště GitHub ukázkový kód. Přejděte na https://github.com/Azure/dev-spaces a vyberte **Forku**. Po dokončení procesu rozvětvení **klonování** vašeho rozvětveného verzi úložiště místně. Ve výchozím nastavení _hlavní_ větev rezervovat, ale zahrnuli jsme některé změny ušetří čas v _azds_updates_ větev, která by také bylo přeneseno při svém forku. _Azds_updates_ větev obsahuje aktualizace vás požádáme o provést ručně v částech kurzu vývoje prostorů, jakož i některé předem vytvořené soubory YAML a JSON pro zjednodušení nasazení systému CI/CD. Můžete použít příkaz podobný `git checkout -b azds_updates origin/azds_updates` se podívat _azds_updates_ větev v místním úložišti.

## <a name="dev-spaces-setup"></a>Instalační program dev mezery
Vytvoření nového prostoru volá _dev_ pomocí `azds space select` příkazu. _Dev_ místa se použije ve vašem kanálu CI/CD nasdílejte změny kódu. Bude také použije k vytvoření _podřízené prostory_ na základě _dev_.

```cmd
azds space select -n dev
```

Po zobrazení výzvy k výběru místa dev nadřazené vyberte  _\<žádný\>_.

Po vytvoření prostoru dev, je nutné určit přípona hostitele. Použití `azds show-context` příkazu můžete zobrazit hostitele příponu Kontroleru příchozího přenosu dat Azure Dev mezery.

```cmd
$ azds show-context
Name   ResourceGroup    DevSpace  HostSuffix
-----  ---------------  --------  ----------------------
MyAKS  MyResourceGroup  dev       fedcba098.eus.azds.io
```

V předchozím příkladu je přípona hostitele _fedcba098.eus.azds.io_. Tato hodnota se používá později při vytváření definice vydané verze.

_Dev_ místo bude vždy obsahovat nejnovější stav úložiště směrný plán, tak, aby vývojáři mohou vytvářet _podřízené prostory_ z _dev_ otestovat jejich izolovaných změn v rámci větší aplikace. Tento koncept je popsáno podrobněji v kurzech vývoje mezery.

## <a name="creating-the-build-definition"></a>Vytváří se definice sestavení
Ve webovém prohlížeči otevřete týmový projekt Azure DevOps a přejděte _kanály_ oddílu. Nejprve, klikněte na fotografie profilu v pravém horním rohu webu Azure DevOps, otevřete podokno funkce ve verzi preview a zakažte _uživatelského prostředí při vytváření nového YAML kanálu_:

![Otevření podokna s funkcemi ve verzi preview](../media/common/preview-feature-open.png)

Možnost zakázat:

![Vytvoření nového kanálu YAML prostředí – možnost](../media/common/yaml-pipeline-preview-feature.png)

> [!Note]
> Azure DevOps _uživatelského prostředí při vytváření nového YAML kanálu_ ve verzi preview funkce je v konfliktu s vytvářením předem definovaných v tuto chvíli vytvářet kanály. Je nutné zakázat ho teď za účelem nasazení našeho kanálu předem daného sestavení.

V _azds_updates_ větve jsme zahrnuli jednoduchý [YAML kanálu Azure](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=vsts&tabs=schema) , který definuje kroky sestavení, které jsou potřebné k *mywebapi* a *webfrontend* .

V závislosti na jazyku, který jste zvolili byl kanál YAML vrácené se změnami do cesty, podobně jako: `samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml`

K vytvoření kanálu z tohoto souboru:
1. Na hlavní stránce projektu DevOps přejděte do kanálů > sestavení.
1. Vyberte možnost vytvořit **nový** kanálu pro sestavování.
1. Vyberte **Githubu** jako zdroj, autorizovat s vaším účtem Githubu Pokud nezbytné a vyberte _azds_updates_ větve z vašeho rozvětveného verzi vývoje prostorů úložiště ukázková aplikace.
1. Vyberte **konfigurace jako kódu**, nebo **YAML**, jako šablony.
1. Stránka konfigurace se teď zobrazí pro vašeho kanálu sestavení. Jak je uvedeno výše, přejděte do umístění konkrétní jazyk pro **cesta k souboru YAML** pomocí **...**  tlačítko. Například, `samples/dotnetcore/getting-started/azure-pipelines.dotnet.yml`.
1. Přejděte **proměnné** kartu.
1. Ruční přidání _dockerId_ jako proměnnou, která je uživatelské jméno vašeho [účet správce Azure Container Registry](../../container-registry/container-registry-authentication.md#admin-account). (Jak je uvedeno v článku požadavků)
1. Ruční přidání _dockerPassword_ jako proměnnou, která je heslo vaší [účet správce Azure Container Registry](../../container-registry/container-registry-authentication.md#admin-account). Nezapomeňte zadat _dockerPassword_ jako tajný kód (výběrem ikonu zámku) z bezpečnostních důvodů.
1. Vyberte **Uložit & frontu**.

Teď máte řešení CI, které se automaticky sestaví *mywebapi* a *webfrontend* pro žádnou aktualizaci do _azds_updates_ větví z forku Githubu. Můžete ověřit tak, že přejdete na web Azure Portal, výběr služby Azure Container Registry a procházení byly nahrány imagí Dockeru **úložišť** kartu. Může trvat několik minut, než bitové kopie sestavení a zobrazí se v registru kontejneru.

![Úložiště Azure Container Registry](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>Vytváří se definice vydané verze

1. Na hlavní stránce projektu DevOps přejděte do kanálů > vydané verze
1. Pokud pracujete v zcela nový projekt DevOps, které ještě neobsahuje definici vydané verze, musíte nejprve vytvořit definici prázdný verzi než budete pokračovat. Možnost importu se nezobrazí v uživatelském rozhraní, dokud nebudete mít existující definice vydané verze.
1. Na levé straně klikněte na tlačítko **+ nová** tlačítko a pak klikněte na **importovat kanálu**.
1. Klikněte na tlačítko **Procházet** a vyberte `samples/release.json` z projektu.
1. Klikněte na **OK**. Všimněte si, že v podokně kanálu byl načten pomocí stránky pro úpravu definice vydání. Všimněte si, že jsou také některé red varovné ikony označující clusteru konkrétní podrobnosti, které musí být pořád nakonfigurované.
1. V levém podokně kanálu, klikněte na tlačítko **přidání artefaktu** bublinu.
1. V **zdroj** rozevíracím seznamu vyberte sestavení kanálu, kterou jste vytvořili dříve.
1. Pro **výchozí verze**, zvolte **nejnovější z výchozí větve kanálu sestavení se značkami**.
1. Ponechte **značky** prázdný.
1. Nastavte **alias zdroje** k `drop`. **Alias zdroje** hodnotu používají předdefinované uvolnění úloh, je nutné ji nastavit.
1. Klikněte na tlačítko **Add** (Přidat).
1. Nyní klikněte na ikonu blesku na nově vytvořený `drop` zdroj artefaktu, jak je znázorněno níže:

    ![Nastavení průběžného nasazování verze artefaktu](../media/common/release-artifact-cd-setup.png)
1. Povolit **trigger průběžného nasazování**.
1. Najeďte myší **úlohy** kartu vedle **kanálu** a klikněte na tlačítko _dev_ upravit _dev_ Příprava úlohy.
1. Ověřte **Azure Resource Manageru** zvolena možnost **typ připojení.** a uvidíte tři ovládacích prvků rozevírací seznam zvýrazněné červeně: ![Nastavení definice verze](../media/common/release-setup-tasks.png)
1. Vyberte předplatné Azure, které používáte s Azure Dev mezerami. Budete také muset klikněte na tlačítko **Authorize**.
1. Vyberte skupinu prostředků a cluster, který používáte s Azure Dev mezery.
1. Klikněte na **úlohu agenta**.
1. Vyberte **hostované 1604 Ubuntu** pod **fondu Agentských**.
1. Najeďte myší **úlohy** selektoru v horní části stránky, klikněte na tlačítko _produkční_ upravit _produkční_ Příprava úlohy.
1. Ověřte **Azure Resource Manageru** zvolena možnost **typ připojení.** a vyberte předplatné Azure, skupinu prostředků a cluster, který používáte s Azure Dev mezery.
1. Klikněte na **úlohu agenta**.
1. Vyberte **hostované 1604 Ubuntu** pod **fondu Agentských**.
1. Klikněte na tlačítko **proměnné** kartu a aktualizovat proměnné pro svou verzi.
1. Aktualizujte hodnotu **DevSpacesHostSuffix** z **UPDATE_ME** na vaše přípona hostitele. Přípona hostitele se zobrazí, když jste spustili `azds show-context` příkaz dříve.
1. Klikněte na tlačítko **Uložit** v pravém horním rohu, a **OK**.
1. Klikněte na tlačítko **+ vydání** (vedle tlačítka Uložit), a **vytvořit vydanou verzi**.
1. V části **artefakty**, ověřte vybrané na nejnovější verzi z vašeho kanálu sestavení.
1. Klikněte na možnost **Vytvořit**.

Automatický proces vydávání nyní se spustí proces, nasazení *mywebapi* a *webfrontend* grafy pro vaše Kubernetes cluster v _dev_ prostor nejvyšší úrovně. Můžete sledovat průběh vydaných verzí na webovém portálu Azure DevOps:

1. Přejděte **verze** části **kanály**.
1. Klikněte na kanál pro vydávání verzí pro ukázkovou aplikaci.
1. Klikněte na název nejnovější verzi.
1. Najeďte myší na **dev** pole v rámci **fáze** a klikněte na tlačítko **protokoly**.

Vydání se provádí po dokončení všech úkolů.

> [!TIP]
> Pokud vydaných verzí se nezdaří s chybovou zprávou *upgradu se nezdařilo: Vypršel časový limit čekání na podmínku*, zkuste kontrola podů ve vašem clusteru [na řídicím panelu Kubernetes](../../aks/kubernetes-dashboard.md). Pokud se zobrazí, se nedaří podů začít s chybové zprávy, jako jsou *Nepodařilo se vyžádat image "azdsexample.azurecr.io/mywebapi:122": chyby rpc: kód = Neznámý desc = chybová odpověď z démona: Získat https://azdsexample.azurecr.io/v2/mywebapi/manifests/122: neoprávněným: je vyžadováno ověření*, může to být proto, že cluster nemá autorizaci k vyžádání ze služby Azure Container Registry. Ujistěte se, že jste dokončili [autorizovat clusteru AKS na vyžádání ze služby Azure Container Registry](../../container-registry/container-registry-auth-aks.md) požadovaných součástí.

Teď máte plně automatizované kanály CI/CD pro svého forku Githubu Dev prostory ukázkových aplikací. Pokaždé, když potvrzení a nasdílení změn kódu, sestavení kanálu se vytváření a nasdílení změn *mywebapi* a *webfrontend* bitové kopie do vlastní instance služby ACR. Pak kanál pro vydávání verzí nasadíte pro každou aplikaci do grafu helmu _dev_ místa v clusteru povolené prostory vývoj.

## <a name="accessing-your-dev-services"></a>Přístup k vaší _dev_ služby
Po nasazení _dev_ verzi *webfrontend* přístupné přes veřejnou adresu URL podobnou této: `http://dev.webfrontend.fedcba098.eus.azds.io`. Tuto adresu URL můžete najít spuštěním `azds list-uri` příkaz: 

```cmd
$ azds list-uris

Uri                                           Status
--------------------------------------------  ---------
http://dev.webfrontend.fedcba098.eus.azds.io  Available
```

## <a name="deploying-to-production"></a>Nasazení do produkčního prostředí

Chcete-li ručně převést konkrétní vydání do _produkční_ pomocí systému CI/CD vytvořené v tomto kurzu:
1. Přejděte **verze** části **kanály**.
1. Klikněte na kanál pro vydávání verzí pro ukázkovou aplikaci.
1. Klikněte na název nejnovější verzi.
1. Najeďte myší **produkční** pole v rámci **fáze** a klikněte na tlačítko **nasadit**.
    ![Přesun do produkčního prostředí](../media/common/prod-promote.png)
1. Najeďte myší na **produkční** pole znovu pod **fáze** a klikněte na tlačítko **protokoly**.

Vydání se provádí po dokončení všech úkolů.

_Produkční_ fázi kanálu CI/CD používá nástroj pro vyrovnávání zatížení místo řadiče Dev prostory příchozího přenosu dat pro poskytnutí přístupu k _produkční_ služby. V nasazení služby _produkční_ fázi jsou k dispozici jako IP adres místo názvů DNS. V produkčním prostředí můžete vytvořit vlastní kontroler příchozího přenosu dat pro hostování vašeho službám v závislosti na konfiguraci DNS.

Pokud chcete zjistit IP webfrontend služby, klikněte na **tisk webfrontend veřejnou IP adresu** krok rozbalte výstup protokolu. Pomocí zobrazené v protokolu výstupu pro přístup k IP **webfrontend** aplikace.

```cmd
...
2019-02-25T22:53:02.3237187Z webfrontend can be accessed at http://52.170.231.44
2019-02-25T22:53:02.3320366Z ##[section]Finishing: Print webfrontend public IP
...
```

## <a name="dev-spaces-instrumentation-in-production"></a>Vývoj prostory instrumentace v produkčním prostředí
I když byly navrženy Dev prostory instrumentace _není_ do normálního provozu vaší aplikace bránit, doporučujeme spustit vaše produkční úlohy v oboru názvů Kubernetes, který není povolený Dev mezer. Tento typ oboru názvů Kubernetes znamená, že byste měli buď vytvořit pomocí oboru názvů produkčního prostředí `kubectl` rozhraní příkazového řádku, nebo umožnit systému CI/CD a vytvořit při prvním nasazení Helm. _Výběr_ nebo jinak vytváření místo použití prostorů vývoj nástrojů přidá Dev prostory instrumentace do daného oboru názvů.

Tady je příklad obor názvů struktury, která podporuje vývoj funkcí, prostředí "dev", _a_ produkčního prostředí, vše v jednom clusteru Kubernetes:

![Ukázková struktura oboru názvů](../media/common/cicd-namespaces.png)

> [!Tip]
> Pokud jste už vytvořili `prod` místa, a bude jednoduše jako ke vyloučit z instrumentace Dev mezery (aniž by ji!), můžete tak učinit pomocí následujícího příkazu rozhraní příkazového řádku vývojáře mezery:
>
> `azds space remove -n prod --no-delete`
>
> Je nutné odstranit všechny podů v `prod` obor názvů za to, takže se můžete znovu vytvořit bez mezery Dev instrumentace.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o týmový vývoj pomocí Azure Dev mezery](../team-development-netcore.md)