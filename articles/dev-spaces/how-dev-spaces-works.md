---
title: Jak Azure Dev Spaces fungují a jsou nakonfigurované
services: azure-dev-spaces
ms.date: 03/04/2019
ms.topic: conceptual
description: Popisuje procesy, které Azure Dev Spaces a způsob jejich konfigurace v konfiguračním souboru azds. yaml.
keywords: azds. yaml, Azure Dev Spaces, vývojářské prostory, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers
ms.openlocfilehash: e96541b0008dca9cbaeda92152f835c188036971
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79259913"
---
# <a name="how-azure-dev-spaces-works-and-is-configured"></a>Jak Azure Dev Spaces fungují a jsou nakonfigurované

Vývoj aplikace Kubernetes může být náročný. Potřebujete konfigurační soubory Docker a Kubernetes. Musíte zjistit, jak aplikaci testovat v místním prostředí a pracovat s dalšími závislými službami. Možná budete muset zvládnout vývoj a testování ve více službách najednou a s týmem vývojářů.

Azure Dev Spaces pomáhá vyvíjet, nasazovat a ladit aplikace Kubernetes přímo ve službě Azure Kubernetes Service (AKS). Azure Dev Spaces také umožňuje týmu sdílet prostor pro vývoj. Sdílení prostoru pro vývoj v rámci týmu umožňuje jednotlivým členům týmu vyvíjet izolaci bez nutnosti replikovat nebo nastavovat závislosti nebo jiné aplikace v clusteru.

Azure Dev Spaces vytvoří a použije konfigurační soubor pro nasazení, spuštění a ladění aplikací Kubernetes v AKS. Tento konfigurační soubor se nachází v kódu vaší aplikace a lze jej přidat do systému správy verzí.

Tento článek popisuje procesy, které Azure Dev Spaces a způsob konfigurace těchto procesů v konfiguračním souboru nástroje Azure Dev Spaces. Chcete-li rychle spustit Azure Dev Spaces a zjistit, jak je vidět v praxi, proveďte jednu z rychlých startů:

* [Java s rozhraním CLI a Visual Studio Code](quickstart-java.md)
* [.NET Core s rozhraními CLI a Visual Studio Code](quickstart-netcore.md)
* [.NET Core se sadou Visual Studio](quickstart-netcore-visualstudio.md)
* [Node. js s rozhraním CLI a Visual Studio Code](quickstart-nodejs.md)

## <a name="how-azure-dev-spaces-works"></a>Jak Azure Dev Spaces funguje

Azure Dev Spaces má dvě různé komponenty, se kterými pracujete: kontroler a nástroje na straně klienta.

![Azure Dev Spaces komponenty](media/how-dev-spaces-works/components.svg)

Kontroler provádí následující akce:

* Spravuje vytváření a výběr vývojového prostoru.
* Nainstaluje graf Helm vaší aplikace a vytvoří objekty Kubernetes.
* Vytvoří Image kontejneru aplikace.
* Nasadí vaši aplikaci do AKS.
* Provede přírůstkové sestavení a restart při změně zdrojového kódu.
* Spravuje protokoly a trasování HTTP.
* Přepošle stdout a stderr do nástrojů na straně klienta.
* Umožňuje členům týmu vytvářet podřízené vývojové prostory odvozené z nadřazeného vývojového prostoru.
* Nakonfiguruje směrování pro aplikace v rámci prostoru i mezi nadřazenými a podřízenými mezerami.

Kontroler se nachází mimo AKS. Řídí chování a komunikaci mezi nástroji na straně klienta a clusterem AKS. Pokud připravujete cluster pro použití Azure Dev Spaces, kontroler je povolený pomocí Azure CLI. Jakmile je tato možnost povolená, můžete s ní pracovat pomocí nástrojů na straně klienta.

Nástroje na straně klienta umožňují uživateli:
* Vygenerujte souboru Dockerfile, Helm graf a Azure Dev Spaces konfigurační soubor pro aplikaci.
* Vytvoření nadřazených a podřízených vývojových prostorů.
* Řekněte řadiči, aby sestavil a spustil vaši aplikaci.

I když je vaše aplikace spuštěná, nástroje na straně klienta:
* Přijímá a zobrazuje stdout a stderr z vaší aplikace spuštěné v AKS.
* Pomocí [předávaného portu](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) povolí webový přístup k vaší aplikaci pomocí protokolu http:\//localhost.
* Připojí ladicí program ke svojí spuštěné aplikaci v AKS.
* Synchronizuje zdrojový kód s vývojovým místem, když je zjištěna změna přírůstkových sestavení, což umožňuje rychlou iteraci.

Můžete použít nástroje na straně klienta z příkazového řádku jako součást `azds`ho příkazu. Můžete také použít nástroje na straně klienta s nástrojem:

* Visual Studio Code pomocí [rozšíření Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds).
* Visual Studio s [Visual Studio Tools for Kubernetes](https://aka.ms/get-vsk8stools).

Tady je základní postup pro nastavení a používání Azure Dev Spaces:
1. Příprava clusteru AKS pro Azure Dev Spaces
1. Příprava kódu pro spuštění v Azure Dev Spaces
1. Spuštění kódu v prostoru pro vývoj
1. Ladění kódu v prostoru pro vývoj
1. Sdílení prostoru pro vývoj

Podíváme se na další podrobnosti o tom, jak Azure Dev Spaces funguje v každé z následujících částí.

## <a name="prepare-your-aks-cluster"></a>Příprava clusteru AKS

Příprava clusteru AKS zahrnuje:
* Ověření clusteru AKS je v oblasti [podporované Azure dev Spaces][supported-regions].
* Ověřuje se, že používáte Kubernetes 1.10.3 nebo novější.
* Povolení Azure Dev Spaces v clusteru pomocí `az aks use-dev-spaces`

Další informace o tom, jak vytvořit a nakonfigurovat cluster AKS pro Azure Dev Spaces, najdete v některém z průvodců Začínáme:
* [Začínáme s Azure Dev Spaces pomocí Java](get-started-java.md)
* [Začínáme s Azure Dev Spaces pomocí .NET Core a sady Visual Studio](get-started-netcore-visualstudio.md)
* [Začínáme s Azure Dev Spaces pomocí .NET Core](get-started-netcore.md)
* [Začínáme s Azure Dev Spaces s využitím Node. js](get-started-nodejs.md)

Když je v clusteru AKS povolená možnost Azure Dev Spaces, nainstaluje se kontrolér pro váš cluster. Kontroler je samostatný prostředek Azure mimo váš cluster a u prostředků v clusteru provádí následující akce:

* Vytvoří nebo určí obor názvů Kubernetes, který se má použít jako místo pro vývoj.
* Odebere libovolný obor názvů Kubernetes s názvem *azds*, pokud existuje, a vytvoří nový.
* Nasadí konfiguraci Webhooku Kubernetes.
* Nasadí Server pro přístup Webhooku.
    

Používá také stejný instanční objekt, který cluster AKS používá k tomu, aby vyvolaly služby na jiné součásti Azure Dev Spaces.

![Azure Dev Spaces Příprava clusteru](media/how-dev-spaces-works/prepare-cluster.svg)

Aby bylo možné použít Azure Dev Spaces, musí existovat alespoň jeden vývojového prostoru. Azure Dev Spaces používá obory názvů Kubernetes v rámci clusteru AKS pro vývojové prostory. Při instalaci kontroleru se zobrazí výzva k vytvoření nového oboru názvů Kubernetes nebo výběru existujícího oboru názvů, který se použije jako první místo pro vývoj. Pokud je obor názvů určen jako místo pro vývoj, kontroler přidá k tomuto oboru názvů jmenovku *azds.IO/Space=true* , aby jej identifikoval jako místo pro vývoj. Počáteční prostor pro vývoj, který vytvoříte nebo označíte, se po přípravě clusteru vybere ve výchozím nastavení. Když je vybraná mezera, používá se Azure Dev Spaces k vytváření nových úloh.

Ve výchozím nastavení vytvoří kontroler prostor pro vývoj s názvem *výchozí* tím, že se upgraduje stávající *výchozí* obor názvů Kubernetes. Pomocí nástrojů na straně klienta můžete vytvořit nové vývojové prostory a odebrat existující vývojové prostory. V důsledku omezení v Kubernetes nejde *výchozí* místo pro vývoj odebrat. Kontroler také odebere všechny existující obory názvů Kubernetes s názvem *azds* , aby nedocházelo ke konfliktům s příkazem `azds` použitým nástroji na straně klienta.

Přidaný server Webhooku Kubernetes se používá k vkládání lusků se třemi kontejnery během nasazování do instrumentace: kontejner devspaces-proxy, kontejner devspaces-proxy-init a kontejner devspaces-buildu. **Všechny tři tyto kontejnery se spouštějí s kořenovým přístupem v clusteru AKS.** Používají také stejný instanční objekt, který cluster AKS používá k tomu, aby vyvolaly služby na jiné součásti Azure Dev Spaces.

![Kubernetes Server pro přístup Webhooku Webhooku Azure Dev Spaces](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

Kontejner devspaces-proxy je kontejner postranového vozíku, který zpracovává veškerý provoz TCP do kontejneru aplikace a z něj a pomáhá provádět směrování. Kontejner devspaces-proxy přesměruje zprávy HTTP, pokud se používají určité mezery. Může například přispět k směrování zpráv HTTP mezi aplikacemi v nadřazených a podřízených prostorech. Veškerý provoz jiného typu než HTTP projde prostřednictvím devspaces-proxy beze změny. Kontejner devspaces-proxy také zaznamenává všechny příchozí a odchozí zprávy HTTP a odesílá je do nástrojů na straně klienta jako trasování. Tato trasování pak mohou vývojáři zobrazit a zkontrolovat chování aplikace.

Kontejner devspaces-proxy-init je [inicializační kontejner](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) , který přidává další pravidla směrování založená na hierarchii prostoru do kontejneru vaší aplikace. Přidává pravidla směrování tím, že aktualizuje soubor */etc/resolv.conf* kontejneru aplikace a konfiguraci softwaru iptables před tím, než se spustí. Aktualizace */etc/resolv.conf* umožňují překlad DNS služeb v nadřazených prostorech. Aktualizace konfigurace softwaru iptables zajistí směrování všech přenosů dat TCP do kontejneru aplikace a z něj, i když devspaces-proxy. Všechny aktualizace z devspaces-proxy-init nastávají kromě pravidel, která Kubernetes přidává.

Kontejner devspaces-Build je inicializační kontejner a má připojený zdrojový kód projektu a Docker Socket. Zdrojový kód projektu a přístup k Docker umožňuje, aby byl kontejner aplikace sestaven přímo pod ním.

> [!NOTE]
> Azure Dev Spaces používá stejný uzel k sestavení kontejneru aplikace a jeho spuštění. V důsledku toho Azure Dev Spaces pro sestavování a spouštění vaší aplikace potřebovat externí registr kontejnerů.

Server pro příjem Webhooku Kubernetes naslouchá každému novému z nich, který je vytvořený v clusteru AKS. Pokud je tento příkaz nasazený do libovolného oboru názvů s popiskem *azds.IO/Space=true* , vloží ho pod další kontejnery. Kontejner devspaces-Build je vložen pouze v případě, že je kontejner aplikace spuštěn pomocí nástrojů na straně klienta.

Po přípravě clusteru AKS můžete pomocí nástrojů na straně klienta připravit a spustit kód ve vývojovém prostoru.

## <a name="prepare-your-code"></a>Příprava kódu

Aby bylo možné aplikaci spustit ve vývojovém prostoru, je nutné ji zařadit do kontejneru a je třeba definovat, jak by měla být nasazena do Kubernetes. K kontejnerizaceí vaší aplikace potřebujete souboru Dockerfile. K definování způsobu nasazení aplikace na Kubernetes potřebujete [graf Helm](https://docs.helm.sh/). Pro pomoc při vytváření grafu souboru Dockerfile i Helm pro vaši aplikaci poskytují nástroje na straně klienta příkaz `prep`:

```cmd
azds prep --public
```

Příkaz `prep` se podívá na soubory v projektu a pokusí se vytvořit graf souboru Dockerfile a Helm pro spuštění aplikace v Kubernetes. V současné době příkaz `prep` vytvoří graf souboru Dockerfile a Helm s následujícími jazyky:

* Java
* Node.js
* .NET Core

Příkaz `prep` *musíte* spustit z adresáře, který obsahuje zdrojový kód. Spuštění příkazu `prep` ze správného adresáře umožňuje nástrojům na straně klienta identifikovat jazyk a vytvořit odpovídající souboru Dockerfile pro kontejnerizace aplikace. Můžete také spustit příkaz `prep` z adresáře, který obsahuje soubor *pom. XML* pro projekty Java.

Pokud spustíte příkaz `prep` z adresáře, který neobsahuje zdrojový kód, nástroje na straně klienta nebudou generovat souboru Dockerfile. Zobrazí se také chyba s oznámením, že *souboru Dockerfile nebylo možné vygenerovat z důvodu nepodporovaného jazyka*. K této chybě dochází také v případě, že nástroje na straně klienta nerozpoznají typ projektu.

Když spustíte příkaz `prep`, máte možnost zadat příznak `--public`. Tento příznak oznamuje řadiči, aby vytvořil koncový bod přístupný z Internetu pro tuto službu. Pokud tento příznak nezadáte, služba je přístupná jenom v rámci clusteru nebo pomocí tunelového připojení localhost vytvořeného nástroji na straně klienta. Můžete povolit nebo zakázat toto chování po spuštění příkazu `prep` aktualizací generovaného grafu Helm.

Příkaz `prep` nenahradí žádné existující grafy fázemi nebo Helm, které máte v projektu. Pokud existující souboru Dockerfile nebo Helm graf používá stejné zásady vytváření názvů jako soubory generované příkazem `prep`, příkaz `prep` přeskočí generování těchto souborů. V opačném případě příkaz `prep` vytvoří svůj vlastní souboru Dockerfile nebo Helm graf vedle sebe stávající soubory.

Příkaz `prep` také vygeneruje `azds.yaml` soubor v kořenu projektu. Azure Dev Spaces používá tento soubor k sestavení, instalaci, konfiguraci a spuštění vaší aplikace. Tento konfigurační soubor obsahuje seznam umístění vašeho grafu souboru Dockerfile a Helm a také poskytuje další konfiguraci nad těmito artefakty.

Tady je příklad souboru azds. yaml vytvořeného pomocí [ukázkové aplikace .NET Core](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend):

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: .
  dockerfile: Dockerfile
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
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
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
```

`azds.yaml` soubor generovaný pomocí příkazu `prep` by měl fungovat správně pro jednoduchý scénář vývoje jednoduchého projektu. Pokud váš konkrétní projekt zvyšuje složitost, možná budete muset tento soubor po spuštění `prep` příkazu aktualizovat. Například váš projekt může vyžadovat některé vylepšení procesu sestavení nebo spuštění na základě potřeb vývoje nebo ladění. V projektu může být také více aplikací, které vyžadují více procesů sestavení nebo jiný obsah sestavení.

## <a name="run-your-code"></a>Spuštění kódu

Chcete-li spustit kód ve vývojovém prostoru, vydejte příkaz `up` ve stejném adresáři jako soubor `azds.yaml`:

```cmd
azds up
```

Příkaz `up` nahraje zdrojové soubory vaší aplikace a další artefakty, které jsou potřebné k sestavení a spuštění projektu, do vývojového prostoru. Odtud máte kontroler ve vašem vývojovém prostoru:

1. Vytvoří objekty Kubernetes pro nasazení aplikace.
1. Vytvoří kontejner pro vaši aplikaci.
1. Nasadí aplikaci do vývojového prostoru.
1. Vytvoří veřejně přístupný název DNS pro koncový bod aplikace, pokud je nakonfigurován.
1. K poskytnutí přístupu ke koncovému bodu aplikace pomocí http://localhostpoužívá *předávaný port* .
1. Přepošle stdout a stderr do nástrojů na straně klienta.


### <a name="starting-a-service"></a>Spuštění služby

Když službu spustíte ve vývojovém prostoru, nástroje na straně klienta a kontroler pracují ve spolupráci při synchronizaci zdrojových souborů, vytvoření kontejneru a objektů Kubernetes a spuštění vaší aplikace.

Na podrobnější úrovni se tady stane, co se stane, když spustíte `azds up`:

1. Soubory jsou synchronizované z počítače uživatele do služby Azure File Storage, která je jedinečná pro cluster AKS uživatele. Nahrají se zdrojový kód, graf Helm a konfigurační soubory. Další podrobnosti o procesu synchronizace jsou k dispozici v následující části.
1. Kontroler vytvoří požadavek na spuštění nové relace. Tato žádost obsahuje několik vlastností, včetně jedinečného ID, názvu prostoru, cesty ke zdrojovému kódu a příznaku ladění.
1. Kontroler v grafu Helm nahrazuje zástupný symbol *$ (tag)* s jedinečným ID relace a pro vaši službu nainstaluje graf Helm. Přidání odkazu na jedinečné ID relace do grafu Helm umožňuje, aby byl kontejner nasazený do clusteru AKS pro tuto konkrétní relaci přizpůsobený zpátky k žádosti o relaci a přidruženým informacím.
1. Během instalace grafu Helm přidá server pro přístup k Webhooku Kubernetes další kontejnery do vaší aplikace pro instrumentaci a přístup ke zdrojovému kódu vašeho projektu. K zajištění trasování protokolu HTTP a směrování prostoru jsou přidány kontejnery devspaces-proxy a devspaces-proxy-init. Přidá se kontejner devspaces-Build, který poskytne pod s přístupem k instanci Docker a zdrojovému kódu projektu pro sestavení kontejneru aplikace.
1. Po spuštění aplikace pod je kontejner devspaces-Build a devspaces-proxy-init použit k sestavení kontejneru aplikace. Pak se spustí kontejner aplikace a kontejnery devspaces-proxy.
1. Po spuštění kontejneru aplikace bude funkce na straně klienta používat Kubernetes funkce pro *přeposílání portů* k poskytnutí přístupu http do vaší aplikace přes http://localhost. Toto přesměrování portu připojí váš vývojový počítač ke službě ve vývojovém prostoru.
1. Po zahájení všech kontejnerů pod ní je služba spuštěná. V tomto okamžiku začnou funkce na straně klienta streamovat trasování HTTP, stdout a stderr. Tyto informace jsou zobrazeny funkcemi na straně klienta pro vývojáře.

### <a name="updating-a-running-service"></a>Aktualizace spuštěné služby

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

Způsob, jakým *devhostagent* spustí předchozí kroky, je nakonfigurován v konfiguračním souboru `azds.yaml`. Tato konfigurace je popsána v pozdější části.

Aktualizace souborů projektu, jako jsou fázemi, soubory csproj nebo jakékoli části grafu Helm, vyžadují, aby byl kontejner aplikace znovu sestaven a znovu nasazen. Když je jeden z těchto souborů synchronizovaný do vývojového prostoru, kontroler spustí příkaz pro [upgrade Helm](https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure) a kontejner aplikace se znovu sestaví a znovu nasadí.

### <a name="file-synchronization"></a>Synchronizace souborů

Při prvním spuštění aplikace ve vývojovém prostoru se nahrají všechny zdrojové soubory aplikace. Když je aplikace spuštěná a později se restartuje, nahrají se jenom změněné soubory. Pro koordinaci tohoto procesu se používají dva soubory: soubor na straně klienta a soubor na straně kontroleru.

Soubor na straně klienta je uložený v dočasném adresáři a má název založený na hodnotě hash adresáře projektu, který běží ve vývojových prostorech. Například ve Windows byste měli mít soubor jako *Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog* pro váš projekt. V systému Linux je soubor na straně klienta uložen v adresáři *adresáře/TMP* . Adresář v macOS můžete najít spuštěním příkazu `echo $TMPDIR`.

Tento soubor je ve formátu JSON a obsahuje:

* Záznam pro každý soubor projektu, který je synchronizován s místem pro vývoj
* ID synchronizace
* Časové razítko poslední operace synchronizace

Každá položka souboru projektu obsahuje cestu k souboru a jeho časovému razítku.

Soubor na straně kontroleru je uložený v clusteru AKS. Obsahuje ID synchronizace a časové razítko poslední synchronizace.

K synchronizaci dochází, když se časová razítka synchronizace neshodují mezi soubory na straně klienta a řadiči. Během synchronizace nástroj na straně klienta provádí iteraci položek souborů v souboru na straně klienta. Je-li časové razítko souboru po časovém razítku synchronizace, je tento soubor synchronizován do vývojového prostoru. Po dokončení synchronizace se časová razítka synchronizace aktualizují jak na straně klienta, tak i na straně řadiče.

Pokud soubor na straně klienta není k dispozici, jsou všechny soubory projektu synchronizovány. Toto chování umožňuje vynutit úplnou synchronizaci odstraněním souboru na straně klienta.

### <a name="how-routing-works"></a>Jak funguje směrování

Vývojové místo je postavené na AKS a používá stejné [Koncepty sítě](../aks/concepts-network.md). Azure Dev Spaces má taky centralizovanou službu *ingressmanager* a nasadí svůj vlastní kontroler příchozího přenosu do clusteru AKS. Služba *ingressmanager* monitoruje clustery AKS pomocí vývojových prostorů a rozšiřuje Azure dev Spaces řadič příchozího přenosu v clusteru s objekty příchozího přenosu dat pro směrování do lusků aplikací. Kontejner devspaces-proxy v každém z nich přidá do vývojového prostoru založeného na adrese URL `azds-route-as` hlavičku protokolu HTTP pro přenosy HTTP. Například požadavek na adresu URL *http://azureuser.s.default.serviceA.fedcba09...azds.io* by získal hlavičku HTTP s `azds-route-as: azureuser`. Kontejner devspaces-proxy nebude přidávat hlavičku `azds-route-as`, pokud již existuje.

Když se ve službě mimo cluster provede požadavek HTTP, požadavek přejde do kontroleru příchozího přenosu dat. Kontroler příchozího přenosu trasuje požadavek přímo na příslušné pole na základě jeho objektů a pravidel příchozího přenosu dat. Kontejner devspaces-proxy v rámci obdrží požadavek, přidá hlavičku `azds-route-as` na základě adresy URL a potom směruje požadavek do kontejneru aplikace.

Pokud se ve službě v rámci clusteru provede požadavek HTTP služby z jiné služby, požadavek se nejprve doprovází do kontejneru devspaces-proxy služby volající služby. Kontejner devspaces-proxy vyhledá požadavek HTTP a zkontroluje hlavičku `azds-route-as`. V závislosti na záhlaví kontejner devspaces-proxy vyhledá IP adresu služby přidružené k hodnotě hlavičky. Pokud je nalezena IP adresa, kontejner devspaces-proxy přesměruje požadavek na tuto IP adresu. Pokud se IP adresa nenajde, kontejner devspaces-proxy směruje požadavek do nadřazeného kontejneru aplikace.

Například *Služba* Applications a *serviceB* jsou nasazeny do nadřazeného vývojového prostoru s názvem *Default*. *služba Service* spoléhá na *serviceB* a provede volání http. Uživatel Azure vytvoří podřízený prostor pro vývoj založený na *výchozím* prostoru s názvem *azureuser*. Uživatel Azure také nasadí svou vlastní verzi *služby* do jejich podřízeného prostoru. Když se dovede požadavek na *http://azureuser.s.default.serviceA.fedcba09...azds.io* :

![Směrování Azure Dev Spaces](media/how-dev-spaces-works/routing.svg)

1. Kontroler příchozího přenosu dat vyhledá adresu pod adresou URL, která je přidružená k adrese URL, která je *Service. azureuser*.
1. Kontroler příchozího přenosu dat vyhledá na zařízení pod vývojovým místem uživatele Azure a směruje požadavek do *služby Service. azureuser* pod.
1. Kontejner devspaces-proxy v rámci *služby Service. azureuser* obdrží požadavek a přidá `azds-route-as: azureuser` jako hlavičku protokolu HTTP.
1. Kontejner devspaces-proxy v nástroji *Service. azureuser* pod směruje požadavek do kontejneru Application aplikace *služby* v této *službě. azureuser* pod.
1. Aplikace *služby* v nástroji *Service. Azureuser* pod volá *serviceB*. Aplikace *služby* také obsahuje kód pro zachování existující hlavičky `azds-route-as`, která v tomto případě je `azds-route-as: azureuser`.
1. Kontejner devspaces-proxy v *Service. azureuser* pod obdrží požadavek a VYHLEDÁ IP adresu *serviceB* na základě hodnoty v hlavičce `azds-route-as`.
1. Kontejner devspaces-proxy v nástroji *Service. azureuser* pod nenalezne IP adresu pro *serviceB. azureuser*.
1. Kontejner devspaces-proxy v rámci *služby Service. azureuser* vyhledá v nadřazeném prostoru IP adresu *serviceB* , která je *serviceB. Default*.
1. Kontejner devspaces-proxy v nástroji *Service. azureuser* NALEZNE IP adresu pro *serviceB. Default* a směruje požadavek do *serviceB. Default* pod.
1. Kontejner devspaces-proxy v *serviceB. Default* pod obdrží požadavek a směruje požadavek do kontejneru aplikace *serviceB* v *serviceB. Default* pod.
1. Aplikace *serviceB* ve *serviceB. Default* pod vrátí odpověď na *službu Service. azureuser* pod.
1. Kontejner devspaces-proxy v *Service. azureuser* pod obdrží odpověď a směruje odpověď do kontejneru aplikace *služby* v nástroji *Service. azureuser* pod.
1. Aplikace *služby* obdrží odpověď a potom vrátí svoji vlastní odpověď.
1. Kontejner devspaces-proxy v rámci *služby Service. azureuser* pod obdrží odpověď z kontejneru aplikace *služby* a směruje odpověď původnímu volajícímu mimo cluster.

Všechny ostatní přenosy TCP, které neprošly protokolem HTTP, prostřednictvím kontroleru příchozího přenosu dat a kontejnerů devspaces-proxy se nezměnily.

### <a name="how-running-your-code-is-configured"></a>Způsob konfigurace spuštění kódu

Azure Dev Spaces používá soubor `azds.yaml` k instalaci a konfiguraci služby. Řadič používá vlastnost `install` v souboru `azds.yaml` k instalaci grafu Helm a vytváření objektů Kubernetes:

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

Ve výchozím nastavení příkaz `prep` vytvoří graf Helm. Také nastaví vlastnost *install. Chart* na adresář grafu Helm. Pokud jste chtěli použít graf Helm v jiném umístění, můžete tuto vlastnost aktualizovat tak, aby používala toto umístění.

Při instalaci Helm grafů poskytuje Azure Dev Spaces způsob, jak přepsat hodnoty v grafu Helm. Výchozí hodnoty pro graf Helm jsou v `charts/APP_NAME/values.yaml`.

Pomocí vlastnosti *install. Values* můžete zobrazit seznam jednoho nebo více souborů, které definují hodnoty, které chcete nahradit v grafu Helm. Pokud jste například chtěli nakonfigurovat název hostitele nebo databázi konkrétně při spuštění aplikace ve vývojovém prostoru, můžete použít tuto funkci přepsání. Můžete také přidat *?* na konci kteréhokoliv názvu souboru pro nastavení jako volitelné.

Vlastnost *install. set* umožňuje nakonfigurovat jednu nebo více hodnot, které mají být nahrazeny v grafu Helm. Všechny hodnoty nakonfigurované v *install. set* přepíšou hodnoty nakonfigurované v souborech, které jsou uvedené v souboru *install. Values*. Vlastnosti v rámci *install. set* jsou závislé na hodnotách v grafu Helm a mohou být odlišné v závislosti na generovaném grafu Helm.

V předchozím příkladu vlastnost *install. set. replicaCount* přikáže kontroleru, kolik instancí aplikace se má spustit ve vývojovém prostoru. V závislosti na vašem scénáři můžete tuto hodnotu zvýšit, ale bude mít vliv na připojení ladicího programu k objektu pod vaší aplikace. Další informace najdete v [článku věnovaném řešení potíží](troubleshooting.md).

V generovaném grafu Helm je image kontejneru nastavená na *{{. Hodnoty. Image. úložiště}}: {{. Values. Image. Tag}}* . Soubor `azds.yaml` definuje ve výchozím nastavení vlastnost *install. set. Image. Tag* jako *$ (tag)* , která se používá jako hodnota pro *{{. Values. Image. Tag}}* . Nastavením vlastnosti *install. set. Image. Tag* tímto způsobem umožníte, aby se image kontejneru pro vaši aplikaci označila odlišným způsobem při spuštění Azure dev Spaces. V tomto konkrétním případě je obrázek označen jako *\<hodnota z image. > úložiště: $ (tag)* . Proměnnou *$ (tag)* musíte použít jako hodnotu *install. set. Image. Tag* , aby prostory pro vývoj rozpoznaly a našli kontejner v clusteru AKS.

V předchozím příkladu `azds.yaml` definuje *install. set. příchozí. Hosts*. Vlastnost *install. set. Hosts* definuje formát názvu hostitele pro veřejné koncové body. Tato vlastnost také používá *$ (spacePrefix)* , *$ (rootSpacePrefix)* a *$ (hostSuffix)* , což jsou hodnoty poskytované řadičem. 

*$ (SpacePrefix)* je název podřízeného vývojového prostoru, který má formu *MEZERNÍK. s*. *$ (RootSpacePrefix)* je název nadřazeného prostoru. Například pokud je *azureuser* podřízeným prostorem *výchozí*hodnoty, je *výchozí* hodnota *$ (rootSpacePrefix)* a hodnota *$ (spacePrefix)* je *azureuser. s*. Pokud prostor není podřízeným prostorem, je argument *$ (spacePrefix)* prázdný. Pokud například *výchozí* prostor nemá žádný nadřazený prostor, hodnota *$ (rootSpacePrefix)* je *výchozí* a hodnota *$ (spacePrefix)* je prázdná. *$ (HostSuffix)* je přípona DNS, která odkazuje Azure dev Spaces na kontroler příchozího přenosu, který běží v clusteru AKS. Tato přípona DNS odpovídá zástupnému záznamu DNS, například *\*. RANDOM_VALUE. EUS. azds. IO*, který byl vytvořen při přidání řadiče Azure dev Spaces do vašeho clusteru AKS.

Ve výše uvedeném `azds.yaml` souboru můžete také aktualizovat *install. set. příchozí. Hosts* a změnit název hostitele vaší aplikace. Například pokud jste chtěli zjednodušit název hostitele vaší aplikace z *$ (spacePrefix) $ (rootSpacePrefix) webendu $ (hostSuffix)* na *$ (spacePrefix) $ (rootSpacePrefix) Web $ (hostSuffix*).

Pro sestavení kontejneru pro aplikaci používá kontroler následující oddíly konfiguračního souboru `azds.yaml`:

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

Vlastnost *Build. Context* obsahuje adresář, ve kterém existuje fázemi. Vlastnost *Build. souboru Dockerfile* definuje název souboru Dockerfile pro sestavení produkční verze aplikace. Vlastnost *configurations. vývoj. Build. souboru Dockerfile* konfiguruje název souboru Dockerfile pro vývojovou verzi aplikace.

Různé fázemi pro vývoj a produkci vám umožní povolit určité věci během vývoje a zakázat tyto položky pro produkční nasazení. Můžete například povolit ladění nebo podrobnější protokolování během vývoje a zakázat v produkčním prostředí. Tyto vlastnosti můžete také aktualizovat, pokud jsou fázemi pojmenované jinak nebo v jiném umístění.

Abychom vám pomohli rychle iterovat během vývoje, Azure Dev Spaces budou synchronizovat změny z místního projektu a přírůstkově aktualizovat vaši aplikaci. V části `azds.yaml` konfiguračního souboru se používá ke konfiguraci synchronizace a aktualizace:

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

Soubory a adresáře, ve kterých budou synchronizovány změny, jsou uvedeny ve vlastnosti *configurations. vývoj. Container. Sync* . Tyto adresáře jsou při spuštění příkazu `up` synchronizovány, a také když jsou zjištěny změny. Pokud máte další nebo různé adresáře, které byste chtěli synchronizovat do vývojového prostoru, můžete tuto vlastnost změnit.

Vlastnost *configurations. Development. Container. ITER. buildCommands* určuje, jak se má aplikace sestavit ve vývojářském scénáři. Vlastnost *configurations. vývoj. Container. Command* poskytuje příkaz pro spuštění aplikace ve vývojovém scénáři. Některou z těchto vlastností můžete chtít aktualizovat, pokud jsou k dispozici další příznaky sestavení nebo modulu runtime nebo parametry, které byste chtěli použít při vývoji.

*Konfigurace. vývoj. Container. ITER. processesToKill* zobrazí seznam procesů, které mají skončit, aby bylo možné aplikaci zastavit. Tuto vlastnost je vhodné aktualizovat, pokud chcete během vývoje změnit chování při restartování vaší aplikace. Například pokud jste aktualizovali *konfigurace. vývoj. Container. iterace. buildCommands* nebo *configurations. vývoj. Container. Command* – vlastnosti pro změnu způsobu, jakým je aplikace sestavená nebo spuštěná, může být potřeba změnit, které procesy se mají zastavit.

Při přípravě kódu pomocí příkazu `azds prep` máte možnost přidat příznak `--public`. Přidáním příznaku `--public` se vytvoří veřejně přístupná adresa URL pro vaši aplikaci. Pokud tento příznak vynecháte, aplikace je přístupná pouze v rámci clusteru nebo pomocí tunelového propojení localhost. Po spuštění příkazu `azds prep` můžete změnit toto nastavení, které upraví vlastnost příchozí *. Enabled* v `charts/APPNAME/values.yaml`:

```yaml
ingress:
  enabled: true
```

## <a name="debug-your-code"></a>Ladění kódu

Pro aplikace Java, .NET a Node. js můžete ladit aplikaci běžící přímo ve vývojovém prostoru pomocí Visual Studio Code nebo sady Visual Studio. Visual Studio Code a Visual Studio poskytují nástroje pro připojení k vývojovému prostoru, spuštění aplikace a připojení ladicího programu. Po spuštění `azds prep`můžete projekt otevřít v Visual Studio Code nebo Visual Studiu. Visual Studio Code nebo Visual Studio vygeneruje vlastní konfigurační soubory pro připojení, které je oddělené od spuštění `azds prep`. Z aplikace Visual Studio Code nebo Visual Studio můžete nastavit zarážky a spustit aplikaci do vývojového prostoru.

![Ladění kódu](media/get-started-node/debug-configuration-nodejs2.png)

Když aplikaci spustíte pomocí Visual Studio Code nebo sady Visual Studio pro účely ladění, pořídí spouštění a připojování k vývojovému prostoru stejným způsobem jako při spuštění `azds up`. Nástroje na straně klienta v Visual Studio Code a Visual Studio také poskytují další parametr s konkrétními informacemi pro ladění. Parametr obsahuje název bitové kopie ladicího programu, umístění ladicího programu v rámci bitové kopie ladicího programu a cílové umístění v rámci kontejneru aplikace pro připojení složky ladicího programu.

Obrázek ladicího programu se automaticky určí pomocí nástrojů na straně klienta. Používá metodu podobnou té jako ta, která se používá během souboru Dockerfile a Helm grafu generují při spuštění `azds prep`. Po připojení ladicího programu k imagi aplikace se spustí pomocí `azds exec`.

## <a name="sharing-a-dev-space"></a>Sdílení prostoru pro vývoj

Při práci s týmem můžete [sdílet vývojové místo v celém týmu](how-to/share-dev-spaces.md) a vytvářet odvozené vývojové prostory. Místo pro vývoj může použít kdokoli s přístupem přispěvatele ke skupině prostředků vývojového prostoru.

Můžete také vytvořit nové vývojové místo, které je odvozeno z jiného vývojového prostoru. Při vytváření odvozeného vývojového prostoru je popisek *azds.IO/Parent-Space=Parent-Space-Name* přidán do oboru názvů odvozeného vývojového prostoru. Všechny aplikace z nadřazeného vývojového prostoru jsou také sdíleny s odvozeným místem pro vývoj. Pokud nasadíte aktualizovanou verzi aplikace do odvozeného vývojového prostoru, bude existovat pouze v odvozeném vývojovém prostoru a nadřazené vývojové místo zůstane neovlivněno. Můžete mít maximálně tři úrovně odvozených vývojových prostorů nebo *prarodičů* .

Odvozené místo pro vývoj také inteligentně směruje požadavky mezi vlastní aplikace a aplikace sdílené z jejího nadřazeného objektu. Směrování funguje tak, že se pokusí směrovat požadavek do aplikace v odvozeném vývojovém prostoru a vrátit se do sdílené aplikace z nadřazeného vývojového prostoru. Směrování se vrátí do sdílené aplikace v prostoru No, pokud aplikace není v nadřazeném prostoru.

Příklad:
* *Výchozí* místo pro vývoj má aplikace *Service* a *serviceB* .
* Vývojové místo *azureuser* je odvozeno od *výchozího nastavení*.
* Do *azureuser*se nasadí aktualizovaná verze *služby Service* .

Při použití *azureuser*budou všechny požadavky na *službu* směrovány do aktualizované verze v *azureuser*. Požadavek na *serviceB* se nejprve pokusí o směrování na *azureuser* verzi *serviceB*. Protože neexistuje, bude směrována do *výchozí* verze *serviceB*. Pokud je odebrána verze *služby* *azureuser* , všechny požadavky na *službu* se vrátí k používání *výchozí* verze *služby*.

## <a name="next-steps"></a>Další kroky

Pokud chcete začít používat Azure Dev Spaces, přečtěte si následující rychlé starty:

* [Java s rozhraním CLI a Visual Studio Code](quickstart-java.md)
* [.NET Core s rozhraními CLI a Visual Studio Code](quickstart-netcore.md)
* [.NET Core se sadou Visual Studio](quickstart-netcore-visualstudio.md)
* [Node. js s rozhraním CLI a Visual Studio Code](quickstart-nodejs.md)

Chcete-li začít s vývojem týmu, přečtěte si následující články s postupy:

* [Vývoj pro týmy – Java s CLI a Visual Studio Code](team-development-java.md)
* [Vývoj pro týmy – .NET Core s rozhraními CLI a Visual Studio Code](team-development-netcore.md)
* [Vývoj pro týmy – .NET Core se sadou Visual Studio](team-development-netcore-visualstudio.md)
* [Vývoj pro týmy – Node. js s rozhraním CLI a Visual Studio Code](team-development-nodejs.md)



[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
