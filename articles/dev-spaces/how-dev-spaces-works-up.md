---
title: Jak funguje spuštění kódu pomocí Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Popisuje procesy spuštění kódu ve službě Azure Kubernetes pomocí azure dev spaces.
keywords: azds.yaml, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
ms.openlocfilehash: 6851c04ac0b72db1bd13c991875c16b0beadc573
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241358"
---
# <a name="how-running-your-code-with-azure-dev-spaces-works"></a>Jak funguje spuštění kódu pomocí Azure Dev Spaces

Azure Dev Spaces vám nabízí několik způsobů, jak rychle itetovat a ladit aplikace Kubernetes a spolupracovat s týmem v clusteru Služby Azure Kubernetes (AKS). Jakmile [je projekt připraven ke spuštění v dev prostoru][how-it-works-prep], můžete použít Dev Spaces k sestavení a spuštění projektu v clusteru AKS.

Tento článek popisuje, co se stane spustit kód v AKS s Dev Spaces.

## <a name="run-your-code"></a>Spuštění kódu

Chcete-li spustit kód v prostoru `up` pro spuštění, vydejte příkaz ve stejném adresáři jako `azds.yaml` soubor:

```cmd
azds up
```

Příkaz `up` nahraje zdrojové soubory aplikace a další artefakty potřebné k sestavení a spuštění projektu do prostoru pro vývoj. Odtud ovladač ve vašem dev prostoru:

1. Vytvoří Objekty Kubernetes k nasazení aplikace.
1. Vytvoří kontejner pro vaši aplikaci.
1. Nasazuje aplikaci do prostoru pro vývoj.
1. Pokud je koncový bod aplikace nakonfigurován, vytvoří veřejně přístupný název DNS pro koncový bod aplikace.
1. Používá *port-forward* k poskytnutí přístupu http://localhostke koncovému bodu aplikace pomocí .
1. Přeposílá stdout a stderr na straně klienta nástroje.


## <a name="starting-a-service"></a>Spuštění služby

Při spuštění služby v prostoru pro vývoj, nástroje na straně klienta a řadič pracovat v koordinaci synchronizovat zdrojové soubory, vytvořit kontejner a Kubernetes objekty a spustit aplikaci.

Na podrobnější úrovni, zde je to, `azds up`co se stane, když spustíte :

1. [Soubory jsou synchronizovány][sync-section] z počítače uživatele do úložiště souborů Azure, které je jedinečné pro cluster AKS uživatele. Nahraje se zdrojový kód, Helmův graf a konfigurační soubory.
1. Řadič vytvoří požadavek na spuštění nové relace. Tento požadavek obsahuje několik vlastností, včetně jedinečného ID, názvu oboru, cesty ke zdrojovému kódu a příznaku ladění.
1. Řadič nahradí zástupný symbol *$(tag)* v grafu Helm jedinečným ID relace a nainstaluje graf Helm pro vaši službu. Přidání odkazu na jedinečné ID relace do grafu Helm umožňuje kontejneru nasazeného do clusteru AKS pro tuto konkrétní relaci svázat zpět k požadavku relace a přidruženým informacím.
1. Během instalace grafu Helm, Kubernetes webhook přijímací server přidá další kontejnery do pod u aplikace pro instrumentaci a přístup ke zdrojovému kódu projektu. Devspaces-proxy a devspaces-proxy-init kontejnery jsou přidány k poskytování http trasování a směrování prostoru. Kontejner sestavení devspaces je přidán, aby poskytl podu přístup k instanci Dockeru a zdrojovému kódu projektu pro vytváření kontejneru vaší aplikace.
1. Při spuštění podu aplikace devspaces sestavení kontejneru a devspaces proxy-init kontejneru se používají k vytvoření kontejneru aplikace. Kontejner aplikace a kontejnery proxy devspaces jsou pak spuštěny.
1. Po spuštění kontejneru aplikace funkce na straně klienta používá funkce *Port-forward* Kubernetes poskytnout http://localhostpřístup HTTP k vaší aplikaci přes . Toto předávání portů propojuje vývojový počítač se službou v vývojovém prostoru.
1. Po spuštění všech kontejnerů v podu je spuštěna služba. V tomto okamžiku funkce na straně klienta začne streamovat trasování PROTOKOLU HTTP, stdout a stderr. Tyto informace jsou zobrazeny na straně klienta funkce pro vývojáře.

## <a name="updating-a-running-service"></a>Aktualizace spuštěné služby

Když je spuštěná služba, Azure Dev Spaces má možnost aktualizovat tuto službu, pokud se změní některý ze zdrojových souborů projektu. Dev Spaces také zpracovává aktualizaci služby odlišně v závislosti na typu souboru, který je změněn. Spuštěnou službu lze aktualizovat třemi způsoby, jak může aplikace Dev Spaces aktualizovat:

* Přímá aktualizace souboru
* Opětovné sestavení a restartování procesu aplikace v kontejneru spuštěné aplikace
* Opětovné sestavení a opětovné nasazení kontejneru aplikace

![Synchronizace souborů Azure Dev Spaces](media/how-dev-spaces-works/file-sync.svg)

Některé soubory projektu, které jsou statické prostředky, jako je například html, css a cshtml soubory, lze aktualizovat přímo v kontejneru aplikace bez restartování nic. Pokud se změní statický datový zdroj, nový soubor se synchronizuje do prostoru pro spuštění a pak jej použije spuštěný kontejner.

Změny souborů, jako je zdrojový kód nebo konfigurační soubory aplikace lze použít restartováním procesu aplikace v rámci spuštěného kontejneru. Jakmile jsou tyto soubory synchronizovány, proces aplikace je restartován v rámci spuštěné kontejneru pomocí procesu *devhostagent.* Při počátečním vytváření kontejneru aplikace řadič nahradí příkaz spuštění aplikace jiným procesem nazývaným *devhostagent*. Skutečný proces aplikace je pak spuštěn jako podřízený proces pod *devhostagent*a jeho výstup je odváděn pomocí výstupu *devhostagent.* Proces *devhostagent* je také součástí Dev Spaces a můžete spouštět příkazy ve spuštěném kontejneru jménem Dev Spaces. Při restartování *devhostagent*:

* Zastaví aktuální proces nebo procesy přidružené k aplikaci.
* Znovu vytvoří aplikaci.
* Restartuje proces nebo procesy přidružené k aplikaci.

Způsob, jakým *devhostagent* provádí předchozí kroky, je [nakonfigurován v . `azds.yaml` ][azds-yaml-section]

Aktualizace souborů projektu, jako jsou Soubory Dockerfiles, csproj soubory nebo libovolné části grafu Helm vyžadují kontejneru aplikace, které mají být znovu sestaveny a znovu nasazené. Pokud je jeden z těchto souborů synchronizován do prostoru pro vývoj, řadič spustí příkaz [upgrade helmu][helm-upgrade] a kontejner aplikace je znovu sestaven a znovu nasazen.

## <a name="file-synchronization"></a>Synchronizace souborů

Při prvním spuštění aplikace v prostoru pro vývoj jsou odeslány všechny zdrojové soubory aplikace. Když je aplikace spuštěna a později se restartuje, nahrají se pouze změněné soubory. Dva soubory se používají ke koordinaci tohoto procesu: soubor na straně klienta a soubor na straně řadiče.

Soubor na straně klienta je uložen v dočasném adresáři a je pojmenován na základě hash adresáře projektu, který používáte v aplikaci Dev Spaces. Například v systému Windows byste měli soubor jako *Users\USERNAME\AppData\Local\Temp\1234567890abcdef123457890abcdef1234567890abcdef123457890abcdef.synclog* pro váš projekt. V Linuxu je soubor na straně klienta uložen v adresáři */tmp.* Adresář v systému macOS najdete spuštěním příkazu. `echo $TMPDIR`

Tento soubor je ve formátu JSON a obsahuje:

* Položka pro každý soubor projektu, který je synchronizován s prostorem pro dev
* ID synchronizace
* Časové razítko poslední operace synchronizace

Každá položka souboru projektu obsahuje cestu k souboru a jeho časové razítko.

Soubor na straně řadiče je uložen v clusteru AKS. Obsahuje ID synchronizace a časové razítko poslední synchronizace.

Synchronizace se stane, když časová razítka synchronizace neodpovídají mezi soubory na straně klienta a na straně řadiče. Během synchronizace nástroje na straně klienta itetují položky souborů v souboru na straně klienta. Pokud je časové razítko souboru za časovým razítkem synchronizace, bude tento soubor synchronizován do prostoru pro spuštění. Po dokončení synchronizace jsou časová razítka synchronizace aktualizována v souborech na straně klienta i na straně řadiče.

Všechny soubory projektu jsou synchronizovány, pokud soubor na straně klienta není k dispozici. Toto chování umožňuje vynutit úplnou synchronizaci odstraněním souboru na straně klienta.

## <a name="how-running-your-code-is-configured"></a>Jak je nakonfigurován oběh ový kód

Azure Dev Spaces `azds.yaml` používá soubor k instalaci a konfiguraci služby. Řadič používá `install` vlastnost v `azds.yaml` souboru k instalaci grafu Helm a vytvoření objektů Kubernetes:

```yaml
...
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
      # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
      # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
      # For more information see https://aka.ms/devspaces/routing
      - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
...
```

Ve výchozím `prep` nastavení bude příkaz generovat graf Helm. Nastaví také vlastnost *install.chart* do adresáře grafu Helm. Pokud jste chtěli použít graf Helm v jiném umístění, můžete tuto vlastnost aktualizovat tak, aby používala toto umístění.

Při instalaci grafů Helm poskytuje Azure Dev Spaces způsob, jak přepsat hodnoty v grafu Helm. Výchozí hodnoty grafu Helm jsou `charts/APP_NAME/values.yaml`v aplikaci .

Pomocí vlastnosti *install.values* můžete v grafu Helm uvést jeden nebo více souborů, které definují hodnoty, které chcete nahradit. Například pokud jste chtěli název hostitele nebo konfigurace databáze konkrétně při spuštění aplikace v prostoru pro vývoj, můžete použít tuto funkci přepsání. Můžete také přidat *?* na konci některého z názvů souborů nastavit jako volitelné.

Vlastnost *install.set* umožňuje konfigurovat jednu nebo více hodnot, které chcete nahradit v grafu Helm. Všechny hodnoty nakonfigurované v *souboru install.set* přepíší hodnoty nakonfigurované v souborech uvedených v *souboru install.values*. Vlastnosti v rámci *install.set* jsou závislé na hodnotách v grafu Helm a mohou se lišit v závislosti na generovaném grafu Helm.

Ve výše uvedeném příkladu vlastnost *install.set.replicaCount* informuje řadič, kolik instancí aplikace má být spuštěno ve vašem dev prostoru. V závislosti na scénáři můžete zvýšit tuto hodnotu, ale bude mít vliv na připojení ladicího programu k podu aplikace. Další informace naleznete v [článku o řešení potíží][troubleshooting].

V generovaném grafu Helm je bitová kopie kontejneru nastavena na *{{ . Values.image.repository }}:{{. Hodnota.image.tag }}*. Soubor `azds.yaml` definuje vlastnost *install.set.image.tag* jako *$(tag),* která se používá jako hodnota pro *{{ . Hodnota.image.tag }}*. Nastavením vlastnosti *install.set.image.tag* tímto způsobem umožňuje, aby byla image kontejneru pro vaši aplikaci při spuštění Azure Dev Spaces zřetelně tagována. V tomto konkrétním případě je obrázek označen jako * \<hodnota z image.repository>:$(tag)*. Proměnnou *$(tag)* je nutné použít jako hodnotu *install.set.image.tag,* aby aplikace Dev Spaces rozpoznala a vyhledala kontejner v clusteru AKS.

Ve výše uvedeném příkladu `azds.yaml` definuje *install.set.ingress.hosts*. Vlastnost *install.set.ingress.hosts* definuje formát názvu hostitele pro veřejné koncové body. Tato vlastnost také používá *$(spacePrefix)*, *$(rootSpacePrefix)* a *$(hostSuffix)*, což jsou hodnoty poskytované řadičem.

*$(spacePrefix)* je název podřízeného dev prostoru, který má podobu *SPACENAME.s*. *$(rootSpacePrefix)* je název nadřazeného prostoru. Například pokud *azureuser* je podřízený prostor *výchozí*, hodnota *pro $(rootSpacePrefix)* je *výchozí* a hodnota *$(spacePrefix)* je *azureuser.s*. Pokud mezera není podřízený prostor, *$(spacePrefix)* je prázdný. Například pokud *výchozí* mezera nemá nadřazený prostor, hodnota *pro $(rootSpacePrefix)* je *výchozí* a hodnota *$(spacePrefix)* je prázdná. *$(hostSuffix)* je přípona DNS, která odkazuje na řadič příchozího přenosu dat Azure Dev Spaces, který běží v clusteru AKS. Tato přípona DNS odpovídá například * \*zástupné položce DNS . RANDOM_VALUE.eus.azds.io*, který byl vytvořen při přidání řadiče Azure Dev Spaces do clusteru AKS.

Ve výše `azds.yaml` uvedeném souboru můžete také aktualizovat *soubor install.set.ingress.hosts* a změnit název hostitele aplikace. Například pokud jste chtěli zjednodušit název hostitele aplikace z *$(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)* na *$(spacePrefix)$(rootSpacePrefix)web$(hostSuffix)*.

K vytvoření kontejneru pro vaši aplikaci používá `azds.yaml` řadič níže uvedené části konfiguračního souboru:

```yaml
build:
  context: .
  dockerfile: Dockerfile
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
...
```

Řadič používá Dockerfile k sestavení a spuštění aplikace.

Vlastnost *build.context* uvádí adresář, kde existují Soubory Dockerfiles. Vlastnost *build.dockerfile* definuje název souboru Dockerfile pro vytváření produkční verze aplikace. Vlastnost *configurations.develop.build.dockerfile* konfiguruje název souboru Dockerfile pro vývojovou verzi aplikace.

S různými Dockerfiles pro vývoj a produkční umožňuje povolit určité věci během vývoje a zakázat tyto položky pro produkční nasazení. Můžete například povolit ladění nebo podrobnější protokolování během vývoje a zakázat v produkčním prostředí. Můžete také aktualizovat tyto vlastnosti, pokud vaše Dockerfiles jsou pojmenovány jinak nebo jsou v jiném umístění.

Aby chomnaběhem vývoje rychle iterate, Azure Dev Spaces bude synchronizovat změny z místního projektu a postupně aktualizovat aplikaci. Níže uvedená `azds.yaml` část v konfiguračním souboru slouží ke konfiguraci synchronizace a aktualizace:

```yaml
...
configurations:
  develop:
    ...
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
...
```

Soubory a adresáře, které budou synchronizovat změny, jsou uvedeny ve vlastnosti *configurations.develop.container.sync.* Tyto adresáře jsou synchronizovány `up` zpočátku při spuštění příkazu, stejně jako při zjištění změn. Pokud existují další nebo různé adresáře, které chcete synchronizovat s vaším prostorem pro vaše technologie, můžete tuto vlastnost změnit.

Vlastnost *configurations.develop.container.iterate.buildCommands* určuje způsob sestavení aplikace ve scénáři vývoje. Vlastnost *configurations.develop.container.command* poskytuje příkaz pro spuštění aplikace ve vývojovém scénáři. Můžete chtít aktualizovat některou z těchto vlastností, pokud existují další sestavení nebo runtime příznaky nebo parametry, které chcete použít během vývoje.

*Configurations.develop.container.iterate.processesToKill* uvádí procesy, které mají být usmrceny, aby se aplikace zastavila. Tuto vlastnost můžete aktualizovat, pokud chcete změnit chování restartování aplikace během vývoje. Pokud jste například *aktualizovali vlastnosti configurations.develop.container.iterate.buildCommands* nebo *configurations.develop.container.command,* aby se změnil způsob sestavení nebo spuštění aplikace, bude pravděpodobně nutné změnit, jaké procesy jsou zastaveny.

Při přípravě kódu `azds prep` pomocí příkazu máte možnost `--enable-ingress` přidat příznak. Přidání `--enable-ingress` příznaku vytvoří veřejně přístupnou adresu URL pro vaši aplikaci. Pokud tento příznak vynesete, aplikace je přístupná pouze v rámci clusteru nebo pomocí tunelu localhost. Po spuštění `azds prep` příkazu můžete toto nastavení změnit úpravou vlastnosti *ingress.enabled* v : `charts/APPNAME/values.yaml`

```yaml
ingress:
  enabled: true
```

## <a name="next-steps"></a>Další kroky

Další informace o sítích a způsobu směrování požadavků v Azure Dev Spaces najdete v [tématu Jak funguje směrování s Azure Dev Spaces][how-it-works-routing].

Další informace o rychlém zpracování a vývoji azure dev spaces najdete v [tématu Jak funguje připojení vývojového počítače k vývojovému prostoru][how-it-works-connect] a [Jak funguje vzdálené ladění kódu pomocí Azure Dev Spaces][how-it-works-remote-debugging].

Pokud chcete začít s azure dev spaces ke spuštění projektu, podívejte se na následující rychlé starty:

* [Rychlé itereje a ladění pomocí kódu Visual Studio a Javy][quickstart-java]
* [Rychlé itered a ladění pomocí kódu Sady Visual Studio a rozhraní .NET][quickstart-netcore]
* [Rychlé itered a ladění pomocí kódu sady Visual Studio a souboru Node.js][quickstart-node]
* [Rychlé itered a ladění pomocí sady Visual Studio a .NET Core][quickstart-vs]
* [Použití CLI k vývoji aplikace na Kubernetes][quickstart-cli]


[azds-yaml-section]: #how-running-your-code-is-configured
[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[quickstart-cli]: quickstart-cli.md
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md
[sync-section]: #file-synchronization
[troubleshooting]: troubleshooting.md