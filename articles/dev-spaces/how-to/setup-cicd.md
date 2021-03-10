---
title: Použití CI/CD s Azure Dev Spaces
services: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: conceptual
manager: gwallace
description: Přečtěte si, jak nastavit průběžnou integraci a průběžné nasazování pomocí Azure DevOps s využitím Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kontejnery
ms.openlocfilehash: 142f01a04066b919a81e1ad1ea95efa31f51f6ca
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102548729"
---
# <a name="use-cicd-with-azure-dev-spaces"></a>Použití CI/CD s Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Tento článek vás provede nastavením služby průběžná integrace/průběžné nasazování (CI/CD) do Azure Kubernetes Service (AKS) s povolenými vývojovým prostorem. CI/CD do AKS umožňuje automaticky nasadit aktualizace aplikací při vložení potvrzeného kódu do zdrojového úložiště. Použití CI/CD ve spojení s clusterem s podporou vývojových prostorů je užitečné, protože může uchovávat základní hodnoty aplikace v aktuálním stavu, se kterými tým pracuje.

![Příklad diagramu CI/CD](../media/common/ci-cd-simple.png)

I když vás tento článek provede s Azure DevOps, stejné koncepty by se měly vztahovat na systémy CI/CD, jako je Jenkinse, TeamCity atd.

## <a name="prerequisites"></a>Požadavky
* Cluster služby Azure Kubernetes (AKS) s povoleným Azure Dev Spaces
* [Nainstalované rozhraní příkazového řádku Azure Dev Spaces](upgrade-tools.md)
* [Organizace Azure DevOps s projektem](/azure/devops/user-guide/sign-up-invite-teammates)
* [Azure Container Registry (ACR)](../../container-registry/container-registry-get-started-azure-cli.md)
    * Podrobnosti [účtu správce](../../container-registry/container-registry-authentication.md#admin-account) Azure Container Registry k dispozici
* [Autorizace clusteru AKS pro vyžádání z Azure Container Registry](../../aks/cluster-container-registry-integration.md)

## <a name="download-sample-code"></a>Stáhnout vzorový kód
V průběhu času vytvoříme větev našeho úložiště GitHub ukázkového kódu. Přejít na https://github.com/Azure/dev-spaces a vybrat **rozvětvení**. Po dokončení procesu rozvětvení **naklonujte** rozvětvenou verzi úložiště místně. Ve výchozím nastavení bude _Hlavní_ větev rezervována, ale zahrnuli jsme do větve _azds_updates_ nějaké změny času, které by se také měly přenést během rozvětvení. Větev _azds_updates_ obsahuje aktualizace, které požádáme o ruční provedení v oddílech kurzu pro vývoj prostorů a také některých předem připravených souborů YAML a JSON pro zjednodušení nasazení systému CI/CD. Pomocí příkazu, který se má `git checkout -b azds_updates origin/azds_updates` rezervovat _azds_updates_ větvi v místním úložišti, můžete použít příkaz.

## <a name="dev-spaces-setup"></a>Nastavení vývojových prostorů
Pomocí příkazu vytvořte nové místo  s názvem dev `azds space select` . Místo pro _vývoj_ bude použit kanál CI/CD, aby bylo možné vložit změny kódu. Také se použije k vytvoření _podřízených prostorů_ na základě _vývoje_.

```cmd
azds space select -n dev
```

Po zobrazení výzvy k výběru nadřazeného prostoru pro vývoj vyberte _\<none\>_ .

Po vytvoření vývojového prostoru musíte určit příponu hostitele. Pomocí `azds show-context` příkazu Zobrazte příponu hostitele Azure dev Spacesho adaptéru příchozího přenosu dat.

```cmd
$ azds show-context
Name   ResourceGroup    DevSpace  HostSuffix
-----  ---------------  --------  ----------------------
MyAKS  MyResourceGroup  dev       fedcba098.eus.azds.io
```

V předchozím příkladu je přípona hostitele _fedcba098.EUS.azds.IO_. Tato hodnota se použije později při vytváření definice vydané verze.

Místo pro _vývoj_ bude vždy obsahovat poslední stav úložiště, a to tak, aby vývojáři mohli vytvářet _podřízené prostory_ z _vývoje_ a testovat jejich izolované změny v kontextu větší aplikace. Tento koncept se podrobněji popisuje v výukových kurzech pro vývoj.

## <a name="creating-the-build-definition"></a>Vytvoření definice sestavení
Ve webovém prohlížeči otevřete týmový projekt Azure DevOps a přejděte do části _kanály_ . Nejdřív klikněte na fotografii profilu v pravém horním rohu webu Azure DevOps, otevřete podokno funkce ve verzi Preview a zakažte _nové prostředí pro vytváření kanálů YAML_:

![Otevírá se podokno funkcí ve verzi Preview.](../media/common/preview-feature-open.png)

Možnost zakázání:

![Nová možnost prostředí pro vytváření kanálů YAML](../media/common/yaml-pipeline-preview-feature.png)

> [!Note]
> Funkce Preview _prostředí pro vytváření nových kanálů YAML_ v Azure DevOps je v konfliktu s vytvářením předem definovaných kanálů sestavení. Pokud chcete nasadit náš předem definovaný kanál sestavení, musíte ho teď zakázat.

Ve větvi _azds_updates_ jsme zahrnuli jednoduché [YAML kanálu Azure](/azure/devops/pipelines/yaml-schema?tabs=schema) definující kroky sestavení vyžadované pro *mywebapi* a *webendu*.

V závislosti na zvoleném jazyce se YAML kanálu vrátil se změnami v cestě, která vypadá přibližně takto: `samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml`

Vytvoření kanálu z tohoto souboru:
1. Na hlavní stránce projektu DevOps přejděte na kanály > sestavení.
1. Vyberte možnost pro vytvoření **nového** kanálu sestavení.
1. Jako zdroj vyberte **GitHub** , v případě potřeby proveďte autorizaci s vaším účtem GitHub a vyberte větev _azds_updates_ z větvené verze úložiště ukázkové aplikace pro _vývoj a prostory_ .
1. Jako šablonu vyberte **Konfigurace jako kód** nebo **YAML**.
1. Nyní se zobrazí stránka konfigurace pro váš kanál sestavení. Jak bylo zmíněno výše, přejděte k cestě k **souboru YAML** pomocí tlačítka **...** v konkrétní jazyku. Například, `samples/dotnetcore/getting-started/azure-pipelines.dotnet.yml`.
1. Přejít na kartu **proměnné** .
1. Ručně přidejte _dockerId_ jako proměnnou, která je uživatelské jméno [účtu správce Azure Container Registry](../../container-registry/container-registry-authentication.md#admin-account). (Zmíněné v článku požadavky)
1. Ručně přidejte _dockerPassword_ jako proměnnou, což je heslo [účtu správce Azure Container Registry](../../container-registry/container-registry-authentication.md#admin-account). Nezapomeňte zadat _dockerPassword_ jako tajný kód (výběrem ikony zámku) z bezpečnostních důvodů.
1. Vyberte **uložit & frontu**.

Teď máte řešení CI, které bude automaticky vytvářet *mywebapi* a *webendu* pro všechny aktualizace odeslané do _azds_updates_ větve vaší větve GitHubu. Image Docker můžete ověřit tak, že přejdete na Azure Portal, vyberete Azure Container Registry a prohlížíte kartu **úložiště** . Může trvat několik minut, než se image sestaví a zobrazí ve vašem registru kontejneru.

![Azure Container Registry úložišť](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>Vytváření definice verze

1. Na hlavní stránce projektu DevOps přejděte na kanály > vydání.
1. Pokud pracujete v rámci zcela nového projektu DevOps, který zatím neobsahuje definici vydané verze, před pokračováním musíte nejprve vytvořit prázdnou definici vydané verze. Možnost importu se v uživatelském rozhraní nezobrazí, dokud nebudete mít existující definici verze.
1. Na levé straně klikněte na tlačítko **+ Nový** a pak klikněte na **importovat kanál**.
1. Klikněte na **Procházet** a vyberte `samples/release.json` si z projektu.
1. Klikněte na **OK**. Všimněte si, že podokno kanálu se načetlo pomocí stránky pro úpravu definice verze. Všimněte si také, že jsou k dispozici červeně výstražné ikony indikující konkrétní informace o clusteru, které je nutné nakonfigurovat.
1. Vlevo od podokna kanálu klikněte na bublinu přidat bublinu **artefaktu** .
1. V rozevíracím seznamu **zdroj** vyberte kanál sestavení, který jste vytvořili dříve.
1. U **výchozí verze** vyberte možnost **nejnovější z výchozí větve kanálu sestavení s značkami**.
1. Nechejte **značky** prázdné.
1. Nastavte **alias zdroje** na `drop` . Hodnota **zdrojového aliasu** se používá v předdefinovaných úlohách vydaných verzí, takže je nutné ji nastavit.
1. Klikněte na **Přidat**.
1. Nyní klikněte na ikonu blesku na nově vytvořeném `drop` zdroji artefaktů, jak je znázorněno níže:

    ![Nastavení průběžného nasazování artefaktů verzí](../media/common/release-artifact-cd-setup.png)
1. Povolte **aktivační událost průběžného nasazování**.
1. Najeďte myší na kartu **úlohy** vedle **kanálu** a kliknutím na možnost _vývoj_ upravte úkoly fáze _vývoje_ .
1. Ověřte, **Azure Resource Manager** je vybrána možnost **Typ připojení.** a uvidíte tři ovládací prvky rozevíracího seznamu zvýrazněné červeně: ![ nastavení definice vydané verze](../media/common/release-setup-tasks.png)
1. Vyberte předplatné Azure, které používáte se službou Azure Dev Spaces. Může být také nutné kliknout na **autorizovat**.
1. Vyberte skupinu prostředků a cluster, který používáte se službou Azure Dev Spaces.
1. Klikněte na **úlohu agenta**.
1. V části **fond agentů** vyberte **hostovaná Ubuntu 1604** .
1. Najeďte myší na selektor **úkolů** v horní části a kliknutím na položku _prod_ upravíte úkoly fáze _výroby_ .
1. Ověřte, **Azure Resource Manager** je vybrána možnost **Typ připojení.** a vyberte předplatné Azure, skupinu prostředků a cluster, který používáte se službou Azure Dev Spaces.
1. Klikněte na **úlohu agenta**.
1. V části **fond agentů** vyberte **hostovaná Ubuntu 1604** .
1. Klikněte na kartu **proměnné** a aktualizujte proměnné pro danou verzi.
1. Aktualizujte hodnotu **DevSpacesHostSuffix** z **UPDATE_ME** na vaši příponu hostitele. Přípona hostitele se zobrazí při `azds show-context` předchozím spuštění příkazu.
1. Klikněte na **Uložit** v pravém horním rohu a pak na **OK**.
1. Klikněte na **+ release** (vedle tlačítka Uložit) a **vytvořte vydání**.
1. V části **artefakty** ověřte, zda je vybráno nejnovější sestavení z kanálu sestavení.
1. Klikněte na **Vytvořit**.

Nyní začne proces automatizované verze, nasazování grafů *mywebapi* a *webendu* do clusteru Kubernetes v _prostoru nejvyšší úrovně_ . Průběh vydaných verzí můžete monitorovat na webovém portálu Azure DevOps:

1. V části **kanály** přejděte do části **vydané verze** .
1. Pro ukázkovou aplikaci klikněte na kanál pro vydání verze.
1. Klikněte na název nejnovější verze.
1. Najeďte myší na **vývojové** pole v části **fáze** a klikněte na **protokoly**.

Vydaná verze se provádí po dokončení všech úloh.

> [!TIP]
> Pokud se vaše verze nezdařila s chybovou zprávou, jako *je upgrade se nezdařil: vypršel časový limit při čekání na podmínku*, zkuste zkontrolovat lusky v clusteru [pomocí řídicího panelu Kubernetes](../../aks/kubernetes-dashboard.md). Pokud vidíte, že se v části nedaří začít s chybovými zprávami, jako je například získání *bitové kopie "azdsexample.azurecr.IO/mywebapi:122" se nezdařilo: Chyba RPC: Code = neznámá funkce DESC = chybová odezva z procesu démon: Get https: \/ /azdsexample.azurecr.IO/v2/mywebapi/Manifests/122: Neautorizováno: vyžaduje se ověření*, protože váš cluster nemá oprávnění k vyžádání z Azure Container Registry. Ujistěte se, že jste dokončili [autorizaci svého clusteru AKS, který bude vyžádanou z vašich potřeb Azure Container Registry](../../aks/cluster-container-registry-integration.md) .

Teď máte plně automatizovaný kanál CI/CD pro vaše větve GitHubu ukázkových aplikací pro vývoj prostorů. Pokaždé, když potvrdíte a napíšete kód, kanál sestavení vytvoří a vloží image *mywebapi* a *webendu* do vlastní instance ACR. Kanál pro vydávání verzí pak nasadí graf Helm pro každou aplikaci do _vývojového_ prostoru v clusteru s povolenými prostory pro vývoj.

## <a name="accessing-your-_dev_-services"></a>Přístup k _vývojovým_ službám
Po nasazení je k _vývojové_ verzi *webendu* možné přistupovat pomocí veřejné adresy URL, jako je: `http://dev.webfrontend.fedcba098.eus.azds.io` . Tuto adresu URL můžete najít spuštěním `azds list-uri` příkazu: 

```cmd
$ azds list-uris

Uri                                           Status
--------------------------------------------  ---------
http://dev.webfrontend.fedcba098.eus.azds.io  Available
```

## <a name="deploying-to-production"></a>Nasazení do produkčního prostředí

Ruční zvýšení úrovně konkrétní verze do _výrobního_ seznamu pomocí systému CI/CD vytvořeného v tomto kurzu:
1. V části **kanály** přejděte do části **vydané verze** .
1. Pro ukázkovou aplikaci klikněte na kanál pro vydání verze.
1. Klikněte na název nejnovější verze.
1. Najeďte myší na pole **kat** v části **fáze** a klikněte na **nasadit**.
    ![Zvýšení úrovně do produkčního prostředí](../media/common/prod-promote.png)
1. Najeďte myší na pole **výr** . v části **fáze** a klikněte na **protokoly**.

Vydaná verze se provádí po dokončení všech úloh.

Fáze _výroby_ kanálu CI/CD používá nástroj pro vyrovnávání zatížení namísto kontroleru pro řízení přístupu pro vývoj prostorů pro poskytování přístupu k _jednotkovým službám._ Služby nasazené v _produkční_ fázi jsou přístupné jako IP adresy místo názvů DNS. V produkčním prostředí se můžete rozhodnout vytvořit vlastní kontroler příchozího přenosu dat, který bude hostovat vaše služby, a to na základě vlastní konfigurace DNS.

Pokud chcete zjistit IP adresu služby webendu, rozbalte výstup protokolu kliknutím na krok  **vytištění veřejné IP adresy webendu** . Pro přístup k aplikaci **webendu** použijte IP adresu zobrazenou ve výstupu protokolu.

```cmd
...
2019-02-25T22:53:02.3237187Z webfrontend can be accessed at http://52.170.231.44
2019-02-25T22:53:02.3320366Z ##[section]Finishing: Print webfrontend public IP
...
```

## <a name="dev-spaces-instrumentation-in-production"></a>Instrumentace prostorů pro vývoj v produkčním prostředí
I když je instrumentace vývojových prostorů navržena tak, _aby se_ nedostala do normálního provozu vaší aplikace, doporučujeme spouštět provozní úlohy v oboru názvů Kubernetes, který není povolený pomocí vývojových prostorů. Tento typ Kubernetes oboru názvů znamená, že byste buď měli vytvořit produkční obor názvů pomocí rozhraní `kubectl` příkazového řádku, nebo nechat systém CI/CD, aby se vytvořil během prvního nasazení Helm. _Výběr_ nebo jiné vytvoření prostoru pomocí nástrojů pro vývoj prostorů přidá do tohoto oboru názvů instrumentaci pro vývojové prostory.

Tady je příklad struktury oboru názvů, která podporuje vývoj funkcí, prostředí pro vývoj a produkci, _a_ to všechno v jednom Kubernetes clusteru:

![Příklad struktury oboru názvů](../media/common/cicd-namespaces.png)

> [!Tip]
> Pokud jste už vytvořili `prod` prostor a chtěli byste ho jednoduše vyloučit z instrumentace pro vývojové prostory (bez odstranění IT!), můžete to udělat pomocí následujícího příkazu pro vývoj rozhraní příkazového řádku:
>
> `azds space remove -n prod --no-delete`
>
> Po provedení tohoto postupu možná budete muset odstranit všechny lusky v `prod` oboru názvů, aby se mohly znovu vytvořit bez instrumentace pro vývojové prostory.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak Azure Dev Spaces funguje.

> [!div class="nextstepaction"]
> [Jak Azure Dev Spaces funguje](../how-dev-spaces-works.md)
