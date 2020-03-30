---
title: Použití CI/CD s Azure Dev Spaces
services: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: conceptual
manager: gwallace
description: Zjistěte, jak nastavit průběžnou integraci a průběžné nasazování pomocí Azure DevOps s Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kontejnery
ms.openlocfilehash: 66ff2080ad44098757a5d9360fd3307e65f7431a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438450"
---
# <a name="use-cicd-with-azure-dev-spaces"></a>Použití CI/CD s Azure Dev Spaces

Tento článek vás provede nastavením průběžné integrace/průběžného nasazování (CI/CD) do služby Azure Kubernetes Service (AKS) s povolenou funkcí Dev Spaces. CI/CD do AKS umožňuje automatické nasazení aktualizací aplikací při každém zasazení potvrzeného kódu do zdrojového úložiště. Použití CI/CD ve spojení s vývojářské prostory povoleno clusteru je užitečné, protože může udržovat směrný plán aplikace aktuální pro tým pracovat.

![Příklad diagramu CI/CD](../media/common/ci-cd-simple.png)

I když tento článek vás provede Azure DevOps, stejné koncepty by se vztahovaly na CI/CD systémy jako Jenkins, TeamCity atd.

## <a name="prerequisites"></a>Požadavky
* [Cluster Azure Kubernetes Service (AKS) s povolenými Azure Dev Spaces](../get-started-netcore.md)
* [Nainstalovaný příkaz CLI Azure Dev Spaces](upgrade-tools.md)
* [Organizace Azure DevOps s projektem](https://docs.microsoft.com/azure/devops/user-guide/sign-up-invite-teammates?view=vsts)
* [Registr kontejnerů Azure (ACR)](../../container-registry/container-registry-get-started-azure-cli.md)
    * K dispozici jsou podrobnosti o [účtu správce](../../container-registry/container-registry-authentication.md#admin-account) registru azure kontejneru
* [Autorizace clusteru AKS k vytažení z registru kontejnerů Azure](../../aks/cluster-container-registry-integration.md)

## <a name="download-sample-code"></a>Stáhnout ukázkový kód
V zájmu času vytvoříme rozbočovač našeho ukázkového kódu úložiště GitHub. Přejděte https://github.com/Azure/dev-spaces na položku **Fork**. Po dokončení procesu rozsváděček **naklonujte** místně rozvislou verzi úložiště. Ve výchozím nastavení bude _hlavní_ větev rezervována, ale do _větve azds_updates,_ která měla být také převedena během rozvětvení, jsme zahrnuli některé změny šetřící čas. Větev _azds_updates_ obsahuje aktualizace, které vás žádáme, abyste provedli ručně v oddílech výuky Dev Spaces, stejně jako některé předem vyrobené soubory YAML a JSON pro zjednodušení nasazení systému CI/CD. Pomocí příkazu `git checkout -b azds_updates origin/azds_updates` můžete rezervovat _azds_updates_ větev v místním úložišti.

## <a name="dev-spaces-setup"></a>Nastavení dev spaces
Vytvořte nový prostor s `azds space select` názvem _dev_ pomocí příkazu. _Dev_ prostor bude použit váš kanál CI/CD k nabízení změn kódu. Bude také použit k vytvoření _podřízených prostorů_ na základě _dev_.

```cmd
azds space select -n dev
```

Po zobrazení výzvy k výběru _ \<\>_ nadřazené dev mezery vyberte možnost Žádný .

Po vytvoření dev prostoru je třeba určit příponu hostitele. Pomocí `azds show-context` příkazu zobrazte příponu hostitele řadiče příchozího přenosu dat Azure Dev Spaces.

```cmd
$ azds show-context
Name   ResourceGroup    DevSpace  HostSuffix
-----  ---------------  --------  ----------------------
MyAKS  MyResourceGroup  dev       fedcba098.eus.azds.io
```

Ve výše uvedeném příkladu je přípona hostitele _fedcba098.eus.azds.io_. Tato hodnota se používá později při vytváření definice verze.

_Dev_ prostor bude vždy obsahovat nejnovější stav úložiště, směrný plán, takže vývojáři mohou vytvářet _podřízené prostory_ z _dev_ otestovat své izolované změny v kontextu větší aplikace. Tento koncept je podrobněji popsán v kurzech dev spaces.

## <a name="creating-the-build-definition"></a>Vytvoření definice sestavení
Otevřete týmový projekt Azure DevOps ve webovém prohlížeči a přejděte do části _Kanály._ Nejprve klikněte na profilovou fotku v pravém horním bodě webu Azure DevOps, otevřete podokno funkcí náhledu a zakažte _nové prostředí pro vytváření kanálu YAML_:

![Otevření podokna funkcí náhledu](../media/common/preview-feature-open.png)

Možnost zakázat:

![Nová možnost vytváření kanálu YAML](../media/common/yaml-pipeline-preview-feature.png)

> [!Note]
> Azure DevOps _Nové prostředí yaml prostředí náhledu_ funkce s vytvářením předdefinovaných kanálů sestavení v tomto okamžiku. Musíte zakázat pro tuto chvíli, aby bylo možné nasadit naše předdefinované sestavení kanálu.

Ve _azds_updates_ větev jsme zahrnuli jednoduchý [Azure Pipeline YAML,](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=vsts&tabs=schema) který definuje kroky sestavení požadované pro *mywebapi* a *webfrontend*.

V závislosti na zvoleném jazyce byl kanál YAML odbaven se změnami na cestě podobné:`samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml`

Vytvoření kanálu z tohoto souboru:
1. Na hlavní stránce projektu DevOps přejděte na > buildů.
1. Vyberte možnost pro vytvoření kanálu **nového** sestavení.
1. Vyberte **GitHub** jako zdroj, v případě potřeby autorizujte pomocí svého účtu GitHub a vyberte _azds_updates_ větev z rozvětvené verze ukázkového úložiště aplikací _pro dev-spaces._
1. Jako **šablonu**vyberte Konfigurace jako kód nebo **YAML**.
1. Nyní se zobrazí konfigurační stránka pro kanál sestavení. Jak bylo uvedeno výše, přejděte na jazykově specifickou cestu pro **cestu k souboru YAML** pomocí tlačítka **....** Například, `samples/dotnetcore/getting-started/azure-pipelines.dotnet.yml`.
1. Přejděte na kartu **Proměnné.**
1. Ručně přidejte _dockerId_ jako proměnnou, což je uživatelské jméno vašeho [účtu správce registru kontejnerů Azure](../../container-registry/container-registry-authentication.md#admin-account). (Uvedeno v článku předpoklady)
1. Ručně přidejte _dockerPassword_ jako proměnnou, což je heslo vašeho [účtu správce registru kontejnerů Azure](../../container-registry/container-registry-authentication.md#admin-account). Nezapomeňte zadat _dockerPassword_ jako tajný klíč (výběrem ikony zámku) pro účely zabezpečení.
1. Vyberte **uložit & fronty**.

Nyní máte řešení CI, které bude automaticky vytvářet *mywebapi* a *webfrontend* pro všechny aktualizace tlačil do _azds_updates_ větve vašeho githubu rozvětvení. Můžete ověřit, že image Dockeru byly posunuty tak, že přejdete na portál Azure, vyberete svůj registr kontejnerů Azure a projdete si kartu **Úložiště.** Může trvat několik minut, než se ifotky vytvoří a zobrazí v registru kontejnerů.

![Úložiště registru kontejnerů Azure](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>Vytvoření definice vydání

1. Na hlavní stránce projektu DevOps přejděte na kanály > vydání.
1. Pokud pracujete ve zbrusu novém projektu DevOps, který ještě neobsahuje definici verze, budete muset před pokračováním nejprve vytvořit prázdnou definici verze. Možnost Importovat se v ui nezobrazí, dokud nemáte existující definici verze.
1. Vlevo klikněte na tlačítko **+ Nový** a potom klikněte na **Importovat kanál**.
1. Klikněte na `samples/release.json` **Procházet** a vyberte z projektu.
1. Klikněte na tlačítko **OK**. Všimněte si, že podokno Potrubí bylo načteno se stránkou úprav definic vydání. Všimněte si také, že existují některé červené ikony upozornění označující podrobnosti specifické pro cluster, které musí být stále nakonfigurovány.
1. Na levé straně podokna Potrubí klikněte na **bublinu Přidat artefakt.**
1. V rozevíracím seznamu **Zdroj** vyberte kanál sestavení, který jste vytvořili dříve.
1. Pro **výchozí verzi**zvolte **Nejnovější z výchozí větve kanálu sestavení se značkami**.
1. Ponechte **značky** prázdné.
1. Nastavte **alias** Source `drop`na . Hodnota **aliasu Zdroje** je používána předdefinovanými úlohami vydání, takže musí být nastavena.
1. Klikněte na **Přidat**.
1. Nyní klikněte na ikonu blesku na nově vytvořeném `drop` zdroji artefaktů, jak je znázorněno níže:

    ![Uvolnění nastavení nepřetržitého nasazení artefaktů](../media/common/release-artifact-cd-setup.png)
1. Povolte **aktivační událost průběžného nasazení**.
1. Najeďte na kartu **Úkoly** vedle **položky Pipeline** a kliknutím na _dev_ upravte úkoly _dev_ fáze.
1. V části Typ připojení ověřte, že správce **prostředků Azure** je **vybraný.** a zobrazí se tři ovládací prvky ![rozevíracího souboru zvýrazněné červeně: Nastavení definice verze](../media/common/release-setup-tasks.png)
1. Vyberte předplatné Azure, které používáte s Azure Dev Spaces. Možná také budete muset klepnout na **tlačítko Autorizovat**.
1. Vyberte skupinu prostředků a cluster, který používáte s Azure Dev Spaces.
1. Klikněte na **úlohu agenta**.
1. Vyberte **Hosted Ubuntu 1604** v rámci **fondu agentů**.
1. Najeďte na volič **úkolů** v horní části a kliknutím na _prod_ upravte úkoly fáze _prod._
1. V části Typ připojení ověřte, že správce **prostředků Azure** je **vybraný.** a vyberte předplatné Azure, skupinu prostředků a cluster, který používáte s Azure Dev Spaces.
1. Klikněte na **úlohu agenta**.
1. Vyberte **Hosted Ubuntu 1604** v rámci **fondu agentů**.
1. Kliknutím na kartu **Proměnné** aktualizujte proměnné pro vydání.
1. Aktualizujte hodnotu **devSpacesHostSuffix** z **UPDATE_ME** do přípony hostitele. Přípona hostitele se zobrazí při `azds show-context` dřívějším spuštění příkazu.
1. V pravém horním setu klikněte na **Uložit** a **OK**.
1. Klikněte na **+ Uvolněte** (vedle tlačítka Uložit) a **Vytvořte verzi**.
1. V části **Artefakty**ověřte, zda je vybráno nejnovější sestavení z kanálu sestavení.
1. Klikněte na **Vytvořit**.

Nyní začne proces automatizovaného vydávání, který nasazuje grafy *mywebapi* a *webfrontend* do clusteru Kubernetes v prostoru nejvyšší úrovně _pro vývoj._ Průběh vydání můžete sledovat na webovém portálu Azure DevOps:

1. Přejděte do části **Zprávy** v části **Kanály**.
1. Klikněte na kanál vydání ukázkové aplikace.
1. Klikněte na název nejnovější verze.
1. Najeďte na **pole dev** v části **Fáze** a klepněte na **protokoly**.

Vydání se provádí po dokončení všech úkolů.

> [!TIP]
> Pokud se vydání nezdaří s chybovou zprávou, jako *je UPGRADE SE NEZDAŘILO: časový čas čekání na podmínku*, zkuste zkontrolovat pody v [clusteru pomocí řídicího panelu Kubernetes](../../aks/kubernetes-dashboard.md). Pokud se zobrazí pody se nedaří spustit s chybové zprávy, jako *je nepodařilo vytáhnout image "azdsexample.azurecr.io/mywebapi:122": rpc chyba: kód = Neznámý desc = Chyba odpověď od daemon: Získat https://azdsexample.azurecr.io/v2/mywebapi/manifests/122: neoprávněné: ověřování povinné*, může to být proto, že váš cluster nebyl autorizován k vyprosit z vašeho registru kontejnerů Azure. Ujistěte se, že jste dokončili [autorizovat clusterAKS navádět z předpokladu registru kontejnerů Azure.](../../aks/cluster-container-registry-integration.md)

Nyní máte plně automatizovaný kanál CI/CD pro vaši githubovou rozprašovací aplikaci Dev Spaces. Pokaždé, když potvrdíte a push kód, bude kanál sestavení sestavení a push *mywebapi* a *webfrontend* obrázky do vlastní instance ACR. Kanál pro uvolnění pak nasadí graf Helm pro každou aplikaci do _vývojového_ prostoru v clusteru s podporou funkce Dev Spaces.

## <a name="accessing-your-_dev_-services"></a>Přístup k _vašim dev službám_
Po nasazení je _dev_ verze *webfrontendu* přístupná s `http://dev.webfrontend.fedcba098.eus.azds.io`veřejnou adresou URL, například: . Tuto adresu URL najdete `azds list-uri` spuštěním příkazu: 

```cmd
$ azds list-uris

Uri                                           Status
--------------------------------------------  ---------
http://dev.webfrontend.fedcba098.eus.azds.io  Available
```

## <a name="deploying-to-production"></a>Nasazení do produkčního prostředí

Ruční povýšení konkrétní verze na _prod_ pomocí systému CI/CD vytvořeného v tomto kurzu:
1. Přejděte do části **Zprávy** v části **Kanály**.
1. Klikněte na kanál vydání ukázkové aplikace.
1. Klikněte na název nejnovější verze.
1. Najeďte na pole **prod** v části **Fáze** a klikněte na **Nasadit**.
    ![Zvýšení úrovně do produkčního prostředí](../media/common/prod-promote.png)
1. Najeďte znovu na pole **prod** **v** části Fáze a klepněte na **položku Protokoly**.

Vydání se provádí po dokončení všech úkolů.

Fáze _prod_ kanálu CI/CD používá namísto řadiče příchozího přenosu dat dev spaces k poskytování přístupu ke _službám prod._ Služby nasazené ve fázi _prod_ jsou přístupné jako IP adresy namísto názvů DNS. V produkčním prostředí se můžete rozhodnout vytvořit vlastní řadič příchozího přenosu dat pro hostování služeb na základě vlastní konfigurace DNS.

Chcete-li zjistit IP adresu služby webfrontend, klikněte na **tiskové webfrontend veřejné IP** krok rozbalte výstup protokolu. Použijte IP zobrazenou ve výstupu protokolu pro přístup k **webové** aplikaci.

```cmd
...
2019-02-25T22:53:02.3237187Z webfrontend can be accessed at http://52.170.231.44
2019-02-25T22:53:02.3320366Z ##[section]Finishing: Print webfrontend public IP
...
```

## <a name="dev-spaces-instrumentation-in-production"></a>Přístrojové vybavení Dev Spaces ve výrobě
Přestože je instrumentace _not_ Dev Spaces navržena tak, aby nepřekážela normálnímu provozu vaší aplikace, doporučujeme spustit produkční úlohy v oboru názvů Kubernetes, který není povolen s funkcemi Dev Spaces. Použití tohoto typu oboru názvů Kubernetes znamená, že byste `kubectl` měli buď vytvořit obor názvů produkčního prostředí pomocí příkazového příkazu k příkazu příkazu, nebo povolit systému CI/CD, aby jej vytvořil během prvního nasazení helmy. _Výběrem_ nebo jiným vytvořením mezery pomocí nástroje Dev Spaces přidáte instrumentaci Dev Spaces do tohoto oboru názvů.

Zde je ukázková struktura oboru názvů, která podporuje vývoj funkcí, prostředí "dev" _a_ produkční prostředí, to vše v jednom clusteru Kubernetes:

![Příklad struktury oboru názvů](../media/common/cicd-namespaces.png)

> [!Tip]
> Pokud jste již vytvořili mezeru `prod` a jednoduše byste ji chtěli vyloučit z instrumentace Dev Spaces (bez odstranění!), můžete tak učinit pomocí následujícího příkazu příkazu Příkaz příkazu příkazu příkazu Příkaz příkazu příkazu příkazu Příkaz příkazu příkazu Příkazpříkaz pro dev Spaces:
>
> `azds space remove -n prod --no-delete`
>
> Možná budete muset odstranit všechny `prod` pody v oboru názvů po provedení tohoto, takže mohou být znovu vytvořeny bez funkce Dev Spaces instrumentace.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Informace o vývoji týmu pomocí Azure Dev Spaces](../team-development-netcore.md)