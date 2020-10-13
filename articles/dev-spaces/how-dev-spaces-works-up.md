---
title: Způsob spuštění kódu s Azure Dev Spaces funguje
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Popisuje procesy spuštění kódu ve službě Azure Kubernetes pomocí Azure Dev Spaces
keywords: azds. yaml, Azure Dev Spaces, vývojářské prostory, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers
ms.openlocfilehash: 1cace325f9415d46210636e5c04cc2d75589cc11
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91975463"
---
# <a name="how-running-your-code-with-azure-dev-spaces-works"></a>Způsob spuštění kódu s Azure Dev Spaces funguje

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces poskytuje několik způsobů, jak rychle iterovat a ladit aplikace Kubernetes a spolupracovat s týmem v clusteru Azure Kubernetes Service (AKS). Jakmile [je projekt připraven ke spuštění ve vývojovém prostoru][how-it-works-prep], můžete k sestavení a spuštění projektu v clusteru AKS použít vývojové prostory.

Tento článek popisuje, co se stane, když spustíte váš kód v AKS s využitím vývojových prostorů.

## <a name="run-your-code"></a>Spuštění kódu

Chcete-li spustit kód ve vývojovém prostoru, vydejte `up` příkaz ve stejném adresáři jako `azds.yaml` soubor:

```cmd
azds up
```

`up`Příkaz nahraje zdrojové soubory vaší aplikace a další artefakty, které jsou potřeba k sestavení a spuštění projektu, do vývojového prostoru. Odtud máte kontroler ve vašem vývojovém prostoru:

1. Vytvoří objekty Kubernetes pro nasazení aplikace.
1. Vytvoří kontejner pro vaši aplikaci.
1. Nasadí aplikaci do vývojového prostoru.
1. Vytvoří veřejně přístupný název DNS pro koncový bod aplikace, pokud je nakonfigurován.
1. Pomocí *předávaného portu* poskytne přístup ke koncovému bodu aplikace pomocí http://localhost .
1. Přepošle stdout a stderr do nástrojů na straně klienta.


## <a name="starting-a-service"></a>Spuštění služby

Když službu spustíte ve vývojovém prostoru, nástroje na straně klienta a kontroler pracují ve spolupráci při synchronizaci zdrojových souborů, vytvoření kontejneru a objektů Kubernetes a spuštění vaší aplikace.

Na podrobnější úrovni, co se stane, když spustíte `azds up` :

1. [Soubory jsou synchronizované][sync-section] z počítače uživatele do služby Azure File Storage, která je jedinečná pro cluster AKS uživatele. Nahrají se zdrojový kód, graf Helm a konfigurační soubory.
1. Kontroler vytvoří požadavek na spuštění nové relace. Tato žádost obsahuje několik vlastností, včetně jedinečného ID, názvu prostoru, cesty ke zdrojovému kódu a příznaku ladění.
1. Kontroler v grafu Helm nahrazuje zástupný symbol *$ (tag)* s jedinečným ID relace a pro vaši službu nainstaluje graf Helm. Přidání odkazu na jedinečné ID relace do grafu Helm umožňuje, aby byl kontejner nasazený do clusteru AKS pro tuto konkrétní relaci přizpůsobený zpátky k žádosti o relaci a přidruženým informacím.
1. Během instalace grafu Helm přidá server pro přístup k Webhooku Kubernetes další kontejnery do vaší aplikace pro instrumentaci a přístup ke zdrojovému kódu vašeho projektu. K zajištění trasování protokolu HTTP a směrování prostoru jsou přidány kontejnery devspaces-proxy a devspaces-proxy-init. Přidá se kontejner devspaces-Build, který poskytne pod s přístupem k instanci Docker a zdrojovému kódu projektu pro sestavení kontejneru aplikace.
1. Po spuštění aplikace pod je kontejner devspaces-Build a devspaces-proxy-init použit k sestavení kontejneru aplikace. Pak se spustí kontejner aplikace a kontejnery devspaces-proxy.
1. Po spuštění kontejneru aplikace bude funkce na straně klienta používat Kubernetes funkce pro *přeposílání portů* k poskytnutí přístupu http do vaší aplikace přes http://localhost . Toto přesměrování portu připojí váš vývojový počítač ke službě ve vývojovém prostoru.
1. Po zahájení všech kontejnerů pod ní je služba spuštěná. V tomto okamžiku začnou funkce na straně klienta streamovat trasování HTTP, stdout a stderr. Tyto informace jsou zobrazeny funkcemi na straně klienta pro vývojáře.

## <a name="updating-a-running-service"></a>Aktualizace spuštěné služby

Když je služba spuštěná, Azure Dev Spaces má možnost aktualizovat tuto službu, pokud se změní některý ze zdrojových souborů projektu. Vývojové prostory také zpracovávají aktualizaci služby odlišně v závislosti na typu souboru, který se změnil. Existují tři způsoby, jak mohou vývojářské prostory aktualizovat běžící službu:

* Přímá aktualizace souboru
* Opětovné sestavení a restartování procesu aplikace uvnitř kontejneru běžící aplikace
* Opětovné sestavení a opětovné nasazení kontejneru aplikace

![Azure Dev Spaces synchronizace souborů](media/how-dev-spaces-works/file-sync.svg)

Některé soubory projektu, které jsou statické prostředky, jako jsou soubory HTML, CSS a cshtml, lze aktualizovat přímo v kontejneru aplikace bez nutnosti restartovat vše. Pokud se statický prostředek změní, nový soubor se synchronizuje do vývojového prostoru a pak ho použije spuštěný kontejner.

Změny souborů, jako je zdrojový kód nebo konfigurační soubory aplikace, lze použít restartováním procesu aplikace v rámci běžícího kontejneru. Po synchronizaci těchto souborů se proces aplikace restartuje v rámci běžícího kontejneru pomocí procesu *devhostagent* . Při počátečním vytváření kontejneru aplikace, kontroler nahradí spouštěcí příkaz pro aplikaci jiným procesem s názvem *devhostagent*. Skutečný proces aplikace je pak spuštěn jako podřízený proces v rámci *devhostagent*a jeho výstup je pomocí výstupu *devhostagent*přesměrované na kanál. Proces *devhostagent* je také součástí vývojových prostorů a může provádět příkazy v běžícím kontejneru jménem vývojových prostorů. Při provádění restartování *devhostagent*:

* Zastaví aktuální proces nebo procesy přidružené k aplikaci.
* Znovu sestaví aplikaci.
* Restartuje proces nebo procesy přidružené k aplikaci.

Způsob, jakým *devhostagent* spustí předchozí kroky, je [nakonfigurován v `azds.yaml` ][azds-yaml-section].

Aktualizace souborů projektu, jako jsou fázemi, soubory csproj nebo jakékoli části grafu Helm, vyžadují, aby byl kontejner aplikace znovu sestaven a znovu nasazen. Když je jeden z těchto souborů synchronizovaný do vývojového prostoru, kontroler spustí příkaz pro [upgrade Helm][helm-upgrade] a kontejner aplikace se znovu sestaví a znovu nasadí.

## <a name="file-synchronization"></a>Synchronizace souborů

Při prvním spuštění aplikace ve vývojovém prostoru se nahrají všechny zdrojové soubory aplikace. Když je aplikace spuštěná a později se restartuje, nahrají se jenom změněné soubory. Pro koordinaci tohoto procesu se používají dva soubory: soubor na straně klienta a soubor na straně kontroleru.

Soubor na straně klienta je uložený v dočasném adresáři a má název založený na hodnotě hash adresáře projektu, který běží ve vývojových prostorech. Například ve Windows byste měli mít soubor jako *Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog* pro váš projekt. V systému Linux je soubor na straně klienta uložen v adresáři *adresáře/TMP* . Adresář v macOS můžete najít spuštěním `echo $TMPDIR` příkazu.

Tento soubor je ve formátu JSON a obsahuje:

* Záznam pro každý soubor projektu, který je synchronizován s místem pro vývoj
* ID synchronizace
* Časové razítko poslední operace synchronizace

Každá položka souboru projektu obsahuje cestu k souboru a jeho časovému razítku.

Soubor na straně kontroleru je uložený v clusteru AKS. Obsahuje ID synchronizace a časové razítko poslední synchronizace.

K synchronizaci dochází, když se časová razítka synchronizace neshodují mezi soubory na straně klienta a řadiči. Během synchronizace nástroj na straně klienta provádí iteraci položek souborů v souboru na straně klienta. Je-li časové razítko souboru po časovém razítku synchronizace, je tento soubor synchronizován do vývojového prostoru. Po dokončení synchronizace se časová razítka synchronizace aktualizují jak na straně klienta, tak i na straně řadiče.

Pokud soubor na straně klienta není k dispozici, jsou všechny soubory projektu synchronizovány. Toto chování umožňuje vynutit úplnou synchronizaci odstraněním souboru na straně klienta.

## <a name="how-running-your-code-is-configured"></a>Způsob konfigurace spuštění kódu

Azure Dev Spaces používá `azds.yaml` soubor k instalaci a konfiguraci služby. Řadič používá `install` vlastnost v `azds.yaml` souboru pro instalaci grafu Helm a vytváření objektů Kubernetes:

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

Ve výchozím nastavení `prep` příkaz vytvoří graf Helm. Také nastaví vlastnost *install. Chart* na adresář grafu Helm. Pokud jste chtěli použít graf Helm v jiném umístění, můžete tuto vlastnost aktualizovat tak, aby používala toto umístění.

Při instalaci Helm grafů poskytuje Azure Dev Spaces způsob, jak přepsat hodnoty v grafu Helm. Výchozí hodnoty pro graf Helm jsou v `charts/APP_NAME/values.yaml` .

Pomocí vlastnosti *install. Values* můžete zobrazit seznam jednoho nebo více souborů, které definují hodnoty, které chcete nahradit v grafu Helm. Pokud jste například chtěli nakonfigurovat název hostitele nebo databázi konkrétně při spuštění aplikace ve vývojovém prostoru, můžete použít tuto funkci přepsání. Můžete také přidat *?* na konci kteréhokoliv názvu souboru pro nastavení jako volitelné.

Vlastnost *install. set* umožňuje nakonfigurovat jednu nebo více hodnot, které mají být nahrazeny v grafu Helm. Všechny hodnoty nakonfigurované v *install. set* přepíšou hodnoty nakonfigurované v souborech, které jsou uvedené v souboru *install. Values*. Vlastnosti v rámci *install. set* jsou závislé na hodnotách v grafu Helm a mohou být odlišné v závislosti na generovaném grafu Helm.

V předchozím příkladu vlastnost *install. set. replicaCount* přikáže kontroleru, kolik instancí aplikace se má spustit ve vývojovém prostoru. V závislosti na vašem scénáři můžete tuto hodnotu zvýšit, ale bude mít vliv na připojení ladicího programu k objektu pod vaší aplikace. Další informace najdete v [článku věnovaném řešení potíží][troubleshooting].

V generovaném grafu Helm je image kontejneru nastavená na *{{. Hodnoty. Image. úložiště}}: {{. Values. Image. Tag}}*. `azds.yaml`Soubor definuje ve výchozím nastavení vlastnost *install. set. Image. Tag* jako *$ (tag)* , která se používá jako hodnota pro *{{. Values. Image. Tag}}*. Nastavením vlastnosti *install. set. Image. Tag* tímto způsobem umožníte, aby se image kontejneru pro vaši aplikaci označila odlišným způsobem při spuštění Azure dev Spaces. V tomto konkrétním případě je obrázek označen jako * \<value from image.repository> : $ (tag)*. Proměnnou *$ (tag)* musíte použít jako hodnotu   *install. set. Image. Tag* , aby prostory pro vývoj rozpoznaly a našli kontejner v clusteru AKS.

V předchozím příkladu `azds.yaml` definuje *install. set. příchozí. Hosts*. Vlastnost *install. set.. Hosts* definuje formát názvu hostitele pro veřejné koncové body. Tato vlastnost také používá *$ (spacePrefix)*, *$ (rootSpacePrefix)* a *$ (hostSuffix)*, což jsou hodnoty poskytované řadičem.

*$ (SpacePrefix)* je název podřízeného vývojového prostoru, který má formu *MEZERNÍK. s*. *$ (RootSpacePrefix)* je název nadřazeného prostoru. Například pokud je *azureuser* podřízeným prostorem *výchozí*hodnoty, je *výchozí* hodnota *$ (rootSpacePrefix)* a hodnota *$ (spacePrefix)* je *azureuser. s*. Pokud prostor není podřízeným prostorem, je argument *$ (spacePrefix)* prázdný. Pokud například *výchozí* prostor nemá žádný nadřazený prostor, hodnota *$ (rootSpacePrefix)* je *výchozí* a hodnota *$ (spacePrefix)* je prázdná. *$ (HostSuffix)* je přípona DNS, která odkazuje Azure dev Spaces na kontroler příchozího přenosu, který běží v clusteru AKS. Tato přípona DNS odpovídá zástupnému záznamu DNS, například * \* . RANDOM_VALUE. EUS. azds. IO*, který byl vytvořen při přidání řadiče Azure dev Spaces do vašeho clusteru AKS.

Ve výše uvedeném `azds.yaml` souboru můžete také aktualizovat *instalaci. set.* příchozí. hostitelů, abyste změnili název hostitele vaší aplikace. Například pokud jste chtěli zjednodušit název hostitele vaší aplikace z *$ (spacePrefix) $ (rootSpacePrefix) webendu $ (hostSuffix)* na *$ (spacePrefix) $ (rootSpacePrefix) Web $ (hostSuffix*).

Pro sestavení kontejneru pro aplikaci používá kontroler následující části `azds.yaml` konfiguračního souboru:

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

Kontroler používá souboru Dockerfile k sestavení a spuštění vaší aplikace.

Vlastnost *Build. Context* obsahuje adresář, ve kterém existuje fázemi. Vlastnost *build.dockerfile* definuje název souboru Dockerfile pro sestavení produkční verze aplikace. Vlastnost *configurations.develop.build.dockerfile* konfiguruje název souboru Dockerfile pro vývojovou verzi aplikace.

Různé fázemi pro vývoj a produkci vám umožní povolit určité věci během vývoje a zakázat tyto položky pro produkční nasazení. Můžete například povolit ladění nebo podrobnější protokolování během vývoje a zakázat v produkčním prostředí. Tyto vlastnosti můžete také aktualizovat, pokud jsou fázemi pojmenované jinak nebo v jiném umístění.

Abychom vám pomohli rychle iterovat během vývoje, Azure Dev Spaces budou synchronizovat změny z místního projektu a přírůstkově aktualizovat vaši aplikaci. Následující část `azds.yaml` konfiguračního souboru se používá ke konfiguraci synchronizace a aktualizace:

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

Soubory a adresáře, ve kterých budou synchronizovány změny, jsou uvedeny ve vlastnosti *configurations. vývoj. Container. Sync* . Tyto adresáře jsou při spuštění příkazu synchronizovány na začátku `up` a také při zjištění změn. Pokud máte další nebo různé adresáře, které byste chtěli synchronizovat do vývojového prostoru, můžete tuto vlastnost změnit.

Vlastnost *configurations. Development. Container. ITER. buildCommands* určuje, jak se má aplikace sestavit ve vývojářském scénáři. Vlastnost *configurations. vývoj. Container. Command* poskytuje příkaz pro spuštění aplikace ve vývojovém scénáři. Některou z těchto vlastností můžete chtít aktualizovat, pokud jsou k dispozici další příznaky sestavení nebo modulu runtime nebo parametry, které byste chtěli použít při vývoji.

*Konfigurace. vývoj. Container. ITER. processesToKill* zobrazí seznam procesů, které mají skončit, aby bylo možné aplikaci zastavit. Tuto vlastnost je vhodné aktualizovat, pokud chcete během vývoje změnit chování při restartování vaší aplikace. Například pokud jste aktualizovali *konfigurace. vývoj. Container. iterace. buildCommands* nebo *configurations. vývoj. Container. Command* – vlastnosti pro změnu způsobu, jakým je aplikace sestavená nebo spuštěná, může být potřeba změnit, které procesy se mají zastavit.

Při přípravě kódu pomocí `azds prep` příkazu máte možnost Přidat `--enable-ingress` příznak. Přidáním `--enable-ingress` příznaku se vytvoří veřejně přístupná adresa URL pro vaši aplikaci. Pokud tento příznak vynecháte, aplikace je přístupná pouze v rámci clusteru nebo pomocí tunelového propojení localhost. Po spuštění `azds prep` příkazu můžete změnit toto nastavení, které upraví vlastnost příchozí *. Enabled* v `charts/APPNAME/values.yaml` :

```yaml
ingress:
  enabled: true
```

## <a name="next-steps"></a>Další kroky

Další informace o sítích a způsobu směrování požadavků v Azure Dev Spaces najdete v tématu [Jak funguje směrování s Azure dev Spaces][how-it-works-routing].

Další informace o rychlé iteraci a vývoji pomocí Kubernetes najdete v článku [jak přemostění na Kubernetes funguje][how-it-works-bridge-to-kubernetes] a [jak vzdálené ladění kódu pomocí Azure dev Spaces funguje][how-it-works-remote-debugging].


[azds-yaml-section]: #how-running-your-code-is-configured
[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[sync-section]: #file-synchronization
[troubleshooting]: troubleshooting.md