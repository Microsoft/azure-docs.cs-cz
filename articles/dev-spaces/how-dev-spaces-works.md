---
title: Jak funguje Azure Dev mezery a je nakonfigurován
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 03/04/2019
ms.topic: conceptual
description: Popisuje procesy, které tento power Azure Dev mezery a jak jsou nakonfigurované v konfiguračním souboru azds.yaml
keywords: azds.yaml prostory vývoj Azure, vývoj mezery, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
ms.openlocfilehash: 0397a52e8cd838aafe44a35508f8a68caba4c94e
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/11/2019
ms.locfileid: "59489584"
---
# <a name="how-azure-dev-spaces-works-and-is-configured"></a>Jak funguje Azure Dev mezery a je nakonfigurován

Vývoj aplikace s Kubernetes může být náročné. Budete potřebovat konfigurační soubory Docker a Kubernetes. Musíte vymyslet, jak svou aplikaci lokálně otestovat a interakci s další závislé služby. Můžete potřebovat pro zpracování vývoj a testování na několik služeb najednou a s týmem vývojářů.

Azure Dev prostorech vývoj, nasazování a ladění aplikací Kubernetes přímo ve službě Azure Kubernetes Service (AKS). Azure Dev prostory taky umožňuje týmu sdílet místa vývoj. Sdílení místo vývoj v týmu umožňuje jednotlivých členů týmu k vývoji v izolaci bez nutnosti replikovat nebo navrhnete závislosti nebo jiné aplikace v clusteru.

Azure Dev prostory vytvoří a použije konfigurační soubor pro nasazení, spouštění a ladění aplikací Kubernetes ve službě AKS. Tento konfigurační soubor se nachází kódu vaší aplikace a mohou být přidány do systému správy verzí.

Tento článek popisuje procesy, které tento power Azure Dev mezery a konfiguraci těchto procesů v konfiguračním souboru Azure Dev mezery. Získat Azure Dev prostory rychle a pozorování v praxi, proveďte jeden z postupů rychlý start:

* [Java pomocí rozhraní příkazového řádku a Visual Studio Code](quickstart-java.md)
* [.NET core pomocí rozhraní příkazového řádku a Visual Studio Code](quickstart-netcore.md)
* [.NET Core with Visual Studio 2017](quickstart-netcore-visualstudio.md)
* [Node.js pomocí rozhraní příkazového řádku a Visual Studio Code](quickstart-nodejs.md)

## <a name="how-azure-dev-spaces-works"></a>Jak funguje Azure Dev mezery

Má dvě různé součásti, které pracují s Azure Dev prostory: kontroler a nástrojů na straně klienta.

![Komponenty Azure Dev mezery](media/how-dev-spaces-works/components.svg)

Kontroler provede následující akce:

* Spravuje vytváření místo vývoje a výběr.
* Nainstaluje grafu helmu vaší aplikace a vytváří objekty Kubernetes.
* Sestavení image kontejneru vaší aplikace.
* Nasadí vaši aplikaci do AKS.
* Přírůstková sestavení neodpovídá a restartuje při změně zdrojového kódu.
* Spravuje protokoly a trasování protokolu HTTP.
* Předává stdout a stderr do nástrojů na straně klienta.
* Umožňuje členům týmu vytvářet prostory dev podřízené odvozen z nadřazeného dev mezery.
* Nakonfiguruje, směrování pro aplikace v rámci mezeru, stejně jako mezi nadřazenými a podřízenými mezery.

Kontroler se nachází mimo AKS. Řídí chování a komunikace mezi klientským nástrojů a clusteru AKS. Kontroler se aktivuje pomocí Azure CLI při přípravě vašeho clusteru Azure Dev prostory. Jakmile ho povolíte, můžete pracovat s ním pomocí nástrojů na straně klienta.

Klientské nástroje mu umožní:
* Generovat soubor Dockerfile, diagram helmu a Azure Dev prostory konfigurační soubor aplikace.
* Vytvoření nadřazené a podřízené dev mezery.
* Zjistit řadič sestavení a spuštění aplikace.

Když aplikace běží, klientské nástroje také:
* Přijímá a zobrazuje stdout a stderr z vaší aplikace běžící ve službě AKS.
* Používá [port vpřed](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) povolit webový přístup do vaší aplikace pomocí protokolu http:\//localhost.
* Připojí ladicí program ke spuštěné aplikaci ve službě AKS.
* Synchronizuje zdrojový kód prostorem dev, když je zjištěna změna pro přírůstkové sestavení umožňující rychlé iterace.

Můžete použít straně klienta z příkazového řádku nástroje jako součást `azds` příkazu. Můžete použít také nástroje s straně klienta:

* Pomocí Visual Studio Code [rozšíření Azure Dev prostory](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds).
* Visual Studio 2017 s [Visual Studio Tools pro systém Kubernetes](https://aka.ms/get-vsk8stools).

Tady je základní postup pro nastavení a používání Azure Dev mezery:
1. Příprava Azure Dev prostory clusteru AKS
1. Připravte váš kód pro spuštění v Azure Dev mezery
1. Spuštění kódu v prostoru vývoj
1. Ladění kódu v prostoru vývoj
1. Sdílet místa vývoj

Probereme podrobnosti o fungování Azure Dev mezery v každém následující části.

## <a name="prepare-your-aks-cluster"></a>Příprava vašeho clusteru AKS

Připravuje se váš cluster AKS vyžaduje:
* Ověření vaší AKS je cluster v oblasti [podporuje Azure Dev prostory](https://docs.microsoft.com/azure/dev-spaces/#a-rapid,-iterative-kubernetes-development-experience-for-teams).
* Ověřování používáte Kubernetes 1.10.3 nebo novější.
* Povolení Azure Dev mezery v clusteru pomocí `az aks use-dev-spaces`

Další informace o tom, jak vytvořit a nakonfigurovat AKS cluster prostory Dev Azure naleznete v získávání příručky Začínáme:
* [Začínáme s Javou v Azure Dev Spaces](get-started-java.md)
* [Zahájení práce se službou Azure Dev Spaces s .NET Core a sadou Visual Studio](get-started-netcore-visualstudio.md)
* [Začínáme s .NET Core v Azure Dev Spaces](get-started-netcore.md)
* [Začínáme v Azure Dev Spaces s Node.js](get-started-nodejs.md)

Když Azure Dev prostory je povolené ve vašem clusteru AKS, nainstaluje kontroleru pro váš cluster. Kontroler je samostatné prostředky Azure mimo váš cluster a provede následující k prostředkům ve vašem clusteru:

* Vytvoří nebo určuje obor názvů Kubernetes pro použití jako vývoj místa.
* Odebere všechny Kubernetes oboru názvů s názvem *azds*, pokud existuje a vytvoří nový.
* Nasadí do Kubernetes inicializátoru objektu.

Také využívá stejné instanční objekt, který váš cluster AKS pomocí provést volání služby do jiné komponenty Azure Dev mezery.

![Příprava Azure Dev prostory clusteru](media/how-dev-spaces-works/prepare-cluster.svg)

Chcete-li používat Azure Dev mezery, musí být alespoň jedna mezera vývoj. Azure Dev prostory používá obory názvů Kubernetes v clusteru AKS dev prostory. Při instalaci kontroleru vyzve k vytvoření nového oboru názvů Kubernetes nebo zvolte existující obor názvů pro použití jako první prostor vývoj. Pokud obor názvů je určena jako vývoj místa, kontroler přidá *azds.io/space=true* popisku na tento obor názvů k jeho identifikaci jako vývoj místa. Počáteční vývoj místo vytvoření nebo určení je standardně vybraná, po přípravě vašeho clusteru. Pokud je vybrána mezeru, se používá mezerami vývoj Azure pro vytvoření nové úlohy.

Ve výchozím nastavení, vytvoří kontroler dev prostor s názvem *výchozí* upgradujte stávající *výchozí* Kubernetes oboru názvů. Nástroje na straně klienta můžete vytvořit nový vývoj prostory a odebrat existující dev mezery. Kvůli omezením v systému Kubernetes *výchozí* dev prostor nelze odebrat. Kontroler taky odebere všechny existující Kubernetes obory názvů s názvem *azds* aby nedocházelo ke konfliktům s `azds` příkaz používá nástrojů na straně klienta.

Inicializátor objektu Kubernetes slouží k vložení podů s tři kontejnery během nasazení pro instrumentaci: devspaces proxy kontejner, kontejner devspaces – proxy-init a devspaces sestavení kontejneru. **Všechny tři těchto kontejnerů pomocí kořenový přístup ve vašem clusteru AKS.** Také používají stejné instanční objekt, který váš cluster AKS pomocí provést volání služby do jiné komponenty Azure Dev mezery.

![Inicializátor Azure Dev prostorů Kubernetes](media/how-dev-spaces-works/kubernetes-initializer.svg)

Kontejner devspaces proxy je kontejner sajdkára, která zpracovává všechny přenosy TCP do proměnné a z kontejneru aplikace a pomáhá provádět směrování. Kontejneru devspaces proxy přesměrovává zprávy protokolu HTTP, pokud používají určité prostory. Například může být snazší směrovat zprávy protokolu HTTP mezi aplikacemi v nadřazené a podřízené mezery. Veškerý provoz jiným protokolem než HTTP prochází devspaces proxy serverů bez jakýchkoli úprav. Kontejner devspaces proxy také všechny příchozí a odchozí zprávy HTTP protokolu a odesílá je do klientské nástroje jako trasování. Toto trasování můžete prohlížet vývojář ke kontrole chování aplikace.

Je kontejner devspaces – proxy-init [init kontejneru](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) , který přidá další pravidla směrování na základě hierarchie místo do vaší aplikace kontejneru. Přidá pravidla směrování aktualizací aplikace kontejneru */etc/resolv.conf* Souborová služba a iptables konfigurace před spuštěním. Aktualizace */etc/resolv.conf* povolit pro překlad názvů DNS služby v nadřazené mezery. Aktualizace konfigurace iptables zajistěte veškerý provoz TCP do a mimo kontejner aplikace jsou směrovány přes devspaces proxy. Všechny aktualizace z devspaces – proxy-init dojít kromě pravidla, která přidá Kubernetes.

Devspaces sestavení kontejneru je kontejner init a má zdrojový kód projektu a připojený soket Dockeru. Zdrojový kód projektu a přístup k Docker umožňuje kontejner aplikace, které mají být vytvořené přímo pod.

> [!NOTE]
> Azure Dev prostory používají stejný uzel k sestavení aplikace kontejneru a spustíme ji. Mezery pro vývojáře Azure v důsledku toho nemusí registr kontejneru externí pro vytváření a používání aplikace.

Inicializátor objektu Kubernetes čeká na všechny nové pod vytvořený v clusteru AKS. Pokud nasadíte tohoto podu pro libovolný obor názvů s *azds.io/space=true* popisek tohoto podu ji vkládá s další kontejnery. Kontejner devspaces sestavení se vloží pouze pokud spouštění kontejneru aplikace pomocí nástrojů na straně klienta.

Jakmile připravíte clusteru AKS, můžete k příprav a spuštění kódu v prostoru vývoj nástrojů na straně klienta.

## <a name="prepare-your-code"></a>Připravte váš kód

Chcete-li spustit aplikaci v prostoru dev, musí být kontejnerizovaných a je třeba definovat, jak by měly být nasazeny do Kubernetes. Kontejnerizujte své aplikace, musíte soubor Dockerfile. Pokud chcete definovat, jak vaše aplikace bude nasazena do Kubernetes, musíte [grafu helmu](https://docs.helm.sh/). Které pomáhají při vytváření souboru Dockerfile i Helm grafu pro aplikaci, na straně klienta nástroje poskytují `prep` příkaz:

```cmd
azds prep --public
```

`prep` Příkazu se podívá na soubory ve vašem projektu a pokuste se vytvořit soubor Dockerfile a Helm grafu pro spuštění aplikace v Kubernetes. V současné době `prep` příkaz vygeneruje soubor Dockerfile a Helm graf s následujícími jazyky:

* Java
* Node.js
* .NET Core

Můžete *musí* spustit `prep` příkaz z adresáře, který obsahuje zdrojový kód. Spuštění `prep` příkaz ze správného adresáře umožňuje určit jazyk a vytvořit odpovídající soubor Dockerfile kontejnerizaci aplikace nástroje na straně klienta. Můžete taky spustit `prep` příkaz z adresáře, který obsahuje *pom.xml* souborů pro projekty Java.

Pokud spustíte `prep` příkaz z adresáře, který obsahuje zdrojový kód, nástroje na straně klienta nebude generovat soubor Dockerfile. Zobrazí se také o tom, že chyba: *Z důvodu nepodporovaný jazyk nebylo možné vygenerovat soubor Dockerfile*. K této chybě dojde, pokud nástrojů na straně klienta nebyl rozpoznán typ projektu.

Při spuštění `prep` příkazu, máte možnost zadat `--public` příznak. Tento příznak říká řadič, který k vytvoření přístupné z Internetu koncového bodu pro tuto službu. Pokud nezadáte tento příznak, služba přístupný jenom v rámci clusteru nebo použitím tunelu localhost vytvořené pomocí nástrojů na straně klienta. Můžete povolit nebo zakázat toto chování po spuštění `prep` příkaz aktualizací generované grafu helmu.

`prep` Příkaz nebude nahradit všechny existující soubory Dockerfile nebo Helm grafy mají ve vašem projektu. Pokud existující soubor Dockerfile nebo Helm graf používá stejné zásady vytváření názvů jako soubory generované záznamem `prep` příkazu `prep` příkaz přeskočí generování těchto souborů. V opačném případě `prep` příkaz vygeneruje svůj vlastní soubor Dockerfile nebo grafu helmu po straně stávající soubory.

`prep` Příkaz také vygeneruje `azds.yaml` soubor v kořenové složce vašeho projektu. Azure Dev prostory tento soubor používá k vytváření, instalaci, konfiguraci a spuštění aplikace. Tento konfigurační soubor obsahuje umístění souboru Dockerfile a Helm grafu a také poskytuje další konfiguraci nad tyto artefakty.

Tady je příklad souboru azds.yaml vytvořené pomocí [ukázkovou aplikaci .NET Core](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend):

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

`azds.yaml` Soubor generovaný nástrojem `prep` příkaz by měl fungovat správně pro scénáře vývoje jednoduchý, jeden projekt. Pokud váš konkrétní projekt má tato problematika ještě složitější, budete muset aktualizovat tento soubor po spuštění `prep` příkazu. Váš projekt může například vyžadovat drobné k sestavení nebo spustit proces založený na vývoj nebo ladění potřebám. Může mít také více aplikací ve vašem projektu, které vyžadují více procesů sestavení nebo jiné sestavení obsahu.

## <a name="run-your-code"></a>Spuštění kódu

Ke spuštění kódu v prostoru dev, vydávat `up` příkaz ve stejném adresáři jako vaše `azds.yaml` souboru:

```cmd
azds up
```

`up` Příkaz odešle zdrojové soubory své aplikace a jiných artefaktů potřebných k vytváření a spouštění vašeho projektu do prostoru pro vývoj. Odtud řadič v prostoru vývoj:

1. Vytvoří objekty Kubernetes pro nasazení aplikace.
1. Vytvoří kontejner pro vaši aplikaci.
1. Nasadí vaši aplikaci do prostoru pro vývoj.
1. Veřejně přístupný název DNS pro váš koncový bod aplikace vytvoří, pokud nakonfigurované.
1. Používá *port vpřed* pro poskytnutí přístupu k aplikaci pomocí koncového bodu http://locahost.
1. Předává stdout a stderr do nástrojů na straně klienta.


### <a name="starting-a-service"></a>Spouští se služba

Při spuštění služby v prostoru vývoj nástrojů na straně klienta a kontroler fungují v koordinaci synchronizace zdrojových souborů, vytvoření kontejneru a objekty Kubernetes a spusťte aplikaci.

Na podrobnější úrovni, tady je co se stane, když spustíte `azds up`:

1. Soubory jsou synchronizovány z počítače uživatele do služby Azure file storage, který je jedinečný pro uživatele clusteru AKS. Zdrojový kód, diagram helmu a konfigurační soubory se nahrávají. Další informace o procesu synchronizace jsou k dispozici v další části.
1. Správce vytvoří požadavek na zahájení nové relace. Tato žádost obsahuje několik vlastností, včetně jedinečné ID, název místa, cesta ke zdrojovému kódu a ladění příznak.
1. Nahradí kontroleru *$(tag)* zástupný symbol v grafu helmu pomocí jedinečného ID relace a nainstaluje grafu Helm pro vaši službu. Přidat že odkaz na jedinečný relace ID grafu helmu umožňuje kontejner nasadí do clusteru AKS pro tuto konkrétní relaci zpět na žádost o relace a související informace.
1. Během instalace grafu helmu inicializátor objektu Kubernetes přidá další kontejnery pod vaší aplikace pro instrumentaci a přístup ke zdrojovému kódu projektu. Devspaces proxy a devspaces – proxy-init kontejnery jsou přidány do trasování protokolu HTTP a místo směrování. Kontejner devspaces sestavení je přidána do pod poskytnout přístup k instanci Dockeru a zdrojový kód projektu pro vytváření kontejnerů vaší aplikace.
1. Při spuštění aplikace pod devspaces sestavení kontejner a kontejner devspaces – proxy-init se používají k vytvoření kontejneru aplikace. Spustí se potom kontejner aplikace a kontejnery devspaces proxy.
1. Po spuštění kontejneru aplikace funkcí na straně klienta používá Kubernetes *port vpřed* funkce s cílem poskytnout přístup protokolu HTTP pro vaši aplikaci nad http://localhost. Toto přesměrování portu připojí ke službě v prostoru vývoj svého vývojového počítače.
1. Když jste spustili všechny kontejnery v pod, se službou. Funkce na straně klienta v tomto okamžiku začne streamování trasování protokolu HTTP, stdout a stderr. Tyto informace se zobrazují podle funkcí na straně klienta pro vývojáře.

### <a name="updating-a-running-service"></a>Aktualizuje se spuštěnou službu

Je spuštěna služba Azure Dev prostory má možnost Aktualizovat tuto službu, pokud žádné zdrojové soubory projektu změnit. Vývoj prostory také zpracovává, aktualizuje se služba odlišně v závislosti na typu souboru, která se změnila. Existují tři způsoby vývoje prostory můžete aktualizovat spuštěnou službu:

* Aktualizuje se přímo soubor
* Znovu sestavit a restartování procesu aplikace uvnitř kontejneru spuštěné aplikace
* Znovu sestavovat a nasazovat aplikace kontejneru

![Synchronizace služby Azure file Dev mezery](media/how-dev-spaces-works/file-sync.svg)

Některé soubory projektu, které jsou statické prostředky, jako jsou html, css a soubory cshtml, můžete aktualizovat přímo v kontejneru aplikace bez restartování cokoli. Pokud se změní statických prostředků, je nový soubor synchronizovány do prostoru pro vývoj a potom využijí spuštěný kontejner.

Změny souborů, jako jsou zdrojový kód nebo konfigurační soubory aplikace můžou použít tak restartování procesu vaší aplikace v rámci spuštěný kontejner. Jakmile tyto soubory jsou synchronizovány, je v rámci spuštěného kontejneru pomocí restartování procesu vaší aplikace *devhostagent* procesu. Při prvotním vytvoření aplikace kontejnerů, kontroler nahradí jiný proces s názvem spouštěcí příkaz pro aplikaci *devhostagent*. Následně se spustí aplikace skutečný proces jako podřízený proces v části *devhostagent*, a její výstup je přesměrovaná horizontálně s využitím *devhostagent*výstup. *Devhostagent* proces je také součástí prostory vývoj a spouštění příkazů v spuštěný kontejner jménem Dev prostory. Při provádění restartování, *devhostagent*:

* Zastaví aktuální proces nebo procesy, které jsou přidružené k aplikaci
* Znovu sestaví aplikaci
* Restartování procesu nebo procesy, které jsou přidružené k aplikaci

Způsob, jakým *devhostagent* provede předchozí kroky je nakonfigurovaný v `azds.yaml` konfigurační soubor. Tato konfigurace je popsané v další části.

Aktualizace souborů projektu, jako jsou soubory Dockerfile, soubory csproj nebo libovolné části grafu helmu vyžadují kontejner aplikace znovu sestavit a znovu nasadit. Když některý z těchto souborů se synchronizují do prostoru pro vývoj, spustí kontroleru [příkaz helm upgrade](https://helm.sh/docs/helm/#helm-upgrade) příkazu a aplikačním kontejneru je znovu sestavit a znovu nasadit.

### <a name="file-synchronization"></a>Synchronizace souborů

Při prvním spuštění aplikace v prostoru dev, se nahraje všechny aplikace zdrojové soubory. Když je aplikace spuštěna a na pozdější restartování pouze změněné soubory jsou odeslány. Ke koordinaci tento proces se používají dva soubory: soubor na straně klienta a soubor straně řadiče.

Soubor na straně klienta je uložen v dočasném adresáři a je pojmenován podle hodnoty hash z adresáře projektu, které jsou spuštěné v prostorách vývoj. Například na Windows by mít soubor *Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog* pro váš projekt. V Linuxu, soubor na straně klienta je uložený v *tmp* adresáře. Adresář v systému macOS můžete najít spuštěním `echo $TMPDIR` příkazu.

Tento soubor je ve formátu JSON a obsahuje:

* Záznam pro každý soubor projektu, který je synchronizován s místem pro vývoj
* ID synchronizace
* Časové razítko poslední operaci synchronizace

Každá položka soubor projektu obsahuje cestu k souboru a jeho časové razítko.

Straně řadiče soubor je uložen v clusteru AKS. Obsahuje ID synchronizace a časové razítko poslední synchronizace.

Synchronizace se stane, když časová razítka synchronizace mezi na straně klienta a soubory straně řadiče se neshodují. Během synchronizace na straně klienta nástroje iteruje položky souboru v souboru straně klienta. Pokud časové razítko souboru po časové razítko synchronizace, tento soubor se synchronizuje do prostoru pro vývoj. Po dokončení synchronizace časová razítka synchronizace se aktualizují na straně klienta a kontroler soubory.

Všechny soubory projektu jsou synchronizované, pokud není k dispozici soubor na straně klienta. Toto chování můžete vynutit úplnou synchronizaci tak, že odstraníte soubor na straně klienta.

### <a name="how-routing-works"></a>Jak funguje směrování

Vývoj místa je nástavbou AKS a používá stejná [koncepty síťových služeb](../aks/concepts-network.md). Azure Dev prostory má také centralizovaná *ingressmanager* služby a nasadí vlastní kontroler příchozího přenosu dat do clusteru AKS. *Ingressmanager* služby monitorování clusterů s prostory dev AKS a argumentech kontroler příchozího přenosu dat Azure Dev mezery v clusteru s objekty příchozího přenosu dat pro směrování pody aplikací. Přidá kontejner devspaces proxy v každé pod `azds-route-as` hlavičku protokolu HTTP pro přenos HTTP do prostoru dev podle zadané adresy URL. Například požadavek na adresu URL *http://azureuser.s.default.serviceA.fedcba09...azds.io* byste získali hlavičky protokolu HTTP s `azds-route-as: azureuser`. Kontejner devspaces proxy nebude přidávat `azds-route-as` záhlaví, pokud je již k dispozici.

Při vytvoření požadavku HTTP a service od mimo cluster, požadavek přejde do kontroleru příchozího přenosu. Kontroler příchozího přenosu dat přesměruje požadavek na přímo do příslušné pod na základě jeho objekty příchozího přenosu dat a pravidla. Kontejner devspaces proxy v pod obdrží požadavek, přidá `azds-route-as` záhlaví podle zadané adresy URL a potom přesměruje požadavek do kontejneru aplikace.

Když se požadavek HTTP je ke službě z jiné služby v rámci clusteru, žádost nejprve prochází kontejneru devspaces proxy volání služby. Kontejner devspaces proxy vyhledá požadavek HTTP a kontroly `azds-route-as` záhlaví. Založené na hlavičce, kontejner devspaces proxy bude vyhledávat IP adresu službu přidruženou hodnotu hlavičky. Pokud se najde IP adresu kontejneru devspaces proxy přesměrovává požadavek na tuto IP adresu. Pokud IP adresa není nalezen, kontejner devspaces proxy server přesměruje požadavek na nadřazený kontejner aplikace.

Například aplikace *serviceA* a *serviceB* jsou nasazené na nadřazené místo dev nazývané *výchozí*. *serviceA* spoléhá na *serviceB* a provádí volání HTTP. Uživatele Azure vytvoří prostor podřízené vývoje na základě *výchozí* místo nazývané *azureuser*. Uživatel Azure také nasadí vlastní verzi *serviceA* jejich podřízené prostoru. Když vytvoří požadavek na *http://azureuser.s.default.serviceA.fedcba09...azds.io*:

![Azure Dev prostory směrování](media/how-dev-spaces-works/routing.svg)

1. Kontroler příchozího přenosu dat vyhledá integrační balíček pro pod přidružené k adrese URL, která je *serviceA.azureuser*.
1. Kontroler příchozího přenosu dat najde IP adresa podu v prostoru uživatele Azure dev a směruje žádosti *serviceA.azureuser* pod.
1. Kontejner devspaces proxy v *serviceA.azureuser* pod obdrží požadavek a přidá `azds-route-as: azureuser` jako hlavičku protokolu HTTP.
1. Kontejneru devspaces proxy v *serviceA.azureuser* pod směruje žádosti *serviceA* kontejner aplikace v *serviceA.azureuser* pod.
1. *ServiceA* v aplikaci *serviceA.azureuser* pod zavolá *serviceB*. *ServiceA* aplikace také obsahuje kód, který zachovat existující `azds-route-as` hlavičky, která v tomto případě je `azds-route-as: azureuser`.
1. Kontejner devspaces proxy v *serviceA.azureuser* pod obdrží požadavek a vyhledá IP *serviceB* podle hodnoty `azds-route-as` záhlaví.
1. Kontejner devspaces proxy v *serviceA.azureuser* pod nenalezne IP pro *serviceB.azureuser*.
1. Kontejner devspaces proxy v *serviceA.azureuser* pod vyhledá integrační balíček pro *serviceB* v nadřazené místo, což je *serviceB.default*.
1. Kontejneru devspaces proxy v *serviceA.azureuser* pod najde integrační balíček pro *serviceB.default* a přesměruje žádost *serviceB.default* pod.
1. Kontejner devspaces proxy v *serviceB.default* pod obdrží požadavek a přesměruje požadavek na *serviceB* kontejner aplikace v *serviceB.default*pod.
1. *ServiceB* v aplikaci *serviceB.default* pod vrátí odpověď *serviceA.azureuser* pod.
1. Kontejneru devspaces proxy v *serviceA.azureuser* pod obdrží odpověď a přesměruje odpověď *serviceA* kontejner aplikace v *serviceA.azureuser* pod.
1. *ServiceA* aplikace obdrží odpověď a vrátí jeho vlastní odpovědi.
1. Kontejner devspaces proxy v *serviceA.azureuser* pod obdrží odpověď od *serviceA* kontejner aplikace a přesměruje odpověď na původní volající mimo cluster.

Veškerý ostatní provoz TCP, který není HTTP prochází kontroler příchozího přenosu dat a kontejnery devspaces proxy bez jakýchkoli úprav.

### <a name="how-running-your-code-is-configured"></a>Způsob, jakým je nakonfigurované spouštění kódu

Používá Azure Dev mezery `azds.yaml` soubor k instalaci a konfiguraci služby. Využívá kontroler `install` vlastnost `azds.yaml` soubor nainstalovat Helm chart a vytvořit objekty Kubernetes:

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

Ve výchozím nastavení `prep` příkaz vygeneruje grafu helmu. Také nastaví *install.chart* vlastnost do adresáře grafu helmu. Pokud jste chtěli použít grafu helmu v jiném umístění, můžete aktualizovat tuto vlastnost použije toto umístění.

Při instalaci grafy Helm, Azure Dev prostory poskytují způsob, jak přepsat hodnoty v grafu helmu. Výchozí hodnoty pro grafu helmu jsou v `charts/APP_NAME/values.yaml`.

Použití *install.values* vlastností, můžete vytvořit seznam jednoho nebo více souborů, které definují hodnoty v grafu helmu požadujete nahrazené. Například pokud byste chtěli název hostitele nebo databáze konfigurace konkrétně při spuštění aplikace v prostoru dev, můžete tuto funkci přepsání. Můžete také přidat *?* na konci žádné názvy souborů nastavte jako volitelné.

*Install.set* vlastnost umožňuje nakonfigurovat jeden nebo více hodnot, které chcete v grafu helmu nahrazené. Všechny hodnoty nakonfigurované v *install.set* přepíše hodnoty nastavené v souborech, které jsou uvedeny v *install.values*. Vlastnosti z kategorie *install.set* jsou závislé na hodnoty v grafu helmu a může lišit v závislosti na vygenerované grafu helmu.

Ve výše uvedeném příkladu *install.set.replicaCount* vlastnost říká kontroleru kolik instancí aplikace na spouštění v prostoru vývoj. V závislosti na vašem scénáři můžete tuto hodnotu zvýšit, ale bude mít vliv na připojení ladicího programu k pod vaší aplikace. Další informace najdete v tématu [článek pro řešení potíží](troubleshooting.md).

V generované grafu helmu image kontejneru je nastavený na *{{. VALUES.Image.Repository}} :{{. VALUES.Image.tag}}*. `azds.yaml` Soubor definuje *install.set.image.tag* vlastnost jako *$(tag)* ve výchozím nastavení, která se používá jako hodnota *{{. VALUES.Image.tag}}*. Tím, že nastavíte *install.set.image.tag* vlastnost tímto způsobem umožňuje image kontejneru pro vaši aplikaci zařazen odlišné způsobem při spuštění Azure Dev mezery. V tomto konkrétním případě je označí image  *<value from image.repository>: $(tag)*. Je nutné použít *$(tag)* jako hodnotu proměnné *install.set.image.tag* v pořadí pro vývoj prostory rozpoznat a najděte kontejner v clusteru AKS.

Ve výše uvedeném příkladu `azds.yaml` definuje *install.set.ingress.hosts*. *Install.set.ingress.hosts* vlastnost definuje formát názvu hostitele pro veřejné koncové body. Tato vlastnost se používá také *$(spacePrefix)*, *$(rootSpacePrefix)*, a *$(hostSuffix)*, které jsou hodnoty podle kontroleru. 

*$(SpacePrefix)* je název místa dev podřízené, což má formu *SPACENAME.s*. *$(RootSpacePrefix)* je název nadřazené místo. Například pokud *azureuser* je podřízený místa *výchozí*, hodnota *$(rootSpacePrefix)* je *výchozí* a hodnota *$(spacePrefix)* je *azureuser.s*. Pokud pole není podřízený mezeru, *$(spacePrefix)* je prázdný. Například pokud *výchozí* prostor nemá nadřazené místo, hodnota *$(rootSpacePrefix)* je *výchozí* a hodnota *$(spacePrefix)* je prázdný. *$(HostSuffix)* je přípona DNS, který odkazuje Azure Dev prostory kontroler příchozího přenosu dat, která běží v clusteru AKS. Tato přípona DNS záznam DNS zástupný znak odpovídá například  *\*. RANDOM_VALUE.eus.azds.IO*, který byl vytvořen při přidání řadičem Azure Dev prostory ke svému clusteru AKS.

V uvedeném `azds.yaml` souboru, může také aktualizaci *install.set.ingress.hosts* můžete změnit název hostitele vaší aplikace. Například Kdybyste chtěli zjednodušit název hostitele vaší aplikace z *$(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)* k *$(spacePrefix)$(rootSpacePrefix)web$(hostSuffix)*.

Pokud chcete vytvořit kontejner pro vaši aplikaci, využívá kontroler následující části `azds.yaml` konfigurační soubor:

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

Kontroler používá soubor Dockerfile pro sestavení a spuštění aplikace.

*Build.context* adresář, ve kterém soubory Dockerfile existují seznamy vlastností. *Build.dockerfile* vlastnost definuje název souboru Dockerfile pro sestavení produkční verzi aplikace. *Configurations.develop.build.dockerfile* konfiguruje vlastnost název souboru Dockerfile pro vývoj verzi aplikace.

Různé soubory Dockerfile pro vývoj a provoz s umožňuje povolit některé aspekty během vývoje a zakažte ty položky pro nasazení v produkčním prostředí. Můžete třeba povolit ladění nebo podrobnější protokolování během vývoje a zakázat v produkčním prostředí. Tyto vlastnosti můžete také aktualizovat, pokud vaše soubory Dockerfile jsou pojmenované jinak nebo v jiném umístění.

Můžete rychle iterovat během vývoje, bude Azure Dev prostory synchronizace změn z vaší místní projekt a přírůstkově aktualizovat aplikaci. Níže v části `azds.yaml` konfigurační soubor se používá ke konfiguraci synchronizace a aktualizace:

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

Soubory a adresáře, které se budou synchronizovat změny jsou uvedeny v *configurations.develop.container.sync* vlastnost. Tyto adresáře se nejprve synchronizují při spuštění `up` příkaz i když se zjistí změny. Pokud jsou dalšími nebo jinými adresáře, které chcete synchronizovat s prostorem dev, můžete tuto vlastnost změnit.

*Configurations.develop.container.iterate.buildCommands* vlastnost určuje, jak vytvořit aplikaci ve scénáři vývoje. *Configurations.develop.container.command* vlastnost obsahuje příkaz pro spuštění aplikace ve scénáři vývoje. Můžete aktualizovat některou z těchto vlastností, pokud existují další příznaky sestavení nebo modul runtime nebo parametry, které chcete použít při vývoji.

*Configurations.develop.container.iterate.processesToKill* vypíše procesy, ukončit o zastavení aplikace. Můžete aktualizovat tuto vlastnost, pokud chcete změnit chování při restartování aplikací během vývoje. Například, pokud jste aktualizovali *configurations.develop.container.iterate.buildCommands* nebo *configurations.develop.container.command* vlastnosti, které chcete změnit, jak je aplikace sestavená nebo spustit, budete muset změnit, které procesy jsou zastaveny.

Při přípravě kódu pomocí `azds prep` příkazu, máte možnost Přidat `--public` příznak. Přidávání `--public` příznak se vytvoří adresa URL veřejně přístupná pro vaši aplikaci. Vynecháte-li tento příznak, bude aplikace přístupná jenom v rámci clusteru nebo pomocí tunelového připojení místního hostitele. Po spuštění `azds prep` příkazu, změna tohoto nastavení můžete změnit *ingress.enabled* vlastnost `charts/APPNAME/values.yaml`:

```yaml
ingress:
  enabled: true
```

## <a name="debug-your-code"></a>Ladění kódu

Pro aplikace Java, .NET a Node.js můžete ladit vaši aplikaci běžící přímo v prostoru vývoj pomocí Visual Studio Code nebo Visual Studio 2017. Visual Studio Code a Visual Studio 2017 poskytuje nástroje pro připojení k prostoru dev, spustí vaši aplikaci a připojit ladicí program. Po spuštění `azds prep`, váš projekt lze otevřít v aplikaci Visual Studio Code nebo Visual Studio 2017. Visual Studio Code nebo Visual Studio 2017 bude generovat vlastní konfigurační soubory pro připojení, které jsou oddělené od spuštění `azds prep`. Z v rámci Visual Studio Code nebo Visual Studio 2017, můžete nastavit zarážky a spuštění aplikace prostorem vývoj.

![Ladění kódu](media/get-started-node/debug-configuration-nodejs2.png)

Při spuštění aplikace pomocí Visual Studio Code nebo Visual Studio 2017 pro ladění, které zpracovávají spuštění a připojení prostorem dev stejným způsobem jako spuštění `azds up`. Klientské nástroje Visual Studio Code a Visual Studio 2017 také poskytují další parametr najdete specifické informace pro ladění. Parametr obsahuje název image ladicího programu, umístění ladicího programu v rámci obrázku ladicího programu a cílové umístění v rámci aplikace kontejneru připojit ladicí program složky. 

Obrázek ladicí program je automaticky určeno nástrojů na straně klienta. Pomocí metody podobné identifikátoru použitému při soubor Dockerfile a grafu helmu generovat při spuštění `azds prep`. Po připojení ladicího programu v bitové kopii aplikace se spustí pomocí `azds exec`.

## <a name="sharing-a-dev-space"></a>Vývoj prostor pro sdílení obsahu

Při práci s týmem, lze [sdílet místa vývoj napříč celý tým](how-to/share-dev-spaces.md) a vytvářet prostory odvozené vývoj. Vývoj místa je možné každému uživateli s přístupem Přispěvatel do skupiny prostředků místo vývoj.

Můžete také vytvořit nový vývoj prostor, který je odvozen z jiného místa na vývoj. Když vytváříte prostor odvozené dev, *azds.io/nadřazený prostor = název NADŘAZENÉ místo* popisek se přidá do oboru názvů prostor odvozené vývoj. Navíc všechny aplikace z prostoru dev nadřazené dostaly prostor odvozené vývoj. Pokud nasadíte aktualizovanou verzi aplikace do místa odvozené dev, bude existovat pouze v prostoru odvozené dev a místo dev nadřazené nebude nijak ovlivněn. Může mít maximálně tři úrovně odvozené dev mezery nebo *dvě úrovně nadřazený* mezery.

Prostor odvozené dev bude také inteligentně směrovat požadavky mezi vlastním aplikacím a aplikacím sdíleny ze svého nadřazeného objektu. Směrování funguje tak, že pokouší směrovat žádost do aplikace v prostoru odvozené dev a považován za sdílené aplikace z prostoru dev nadřazené. Směrování přejde zpět do sdílené aplikace v prostoru dvě úrovně nadřazený Pokud aplikace není v oboru nadřazené.

Příklad:
* Místo dev *výchozí* má aplikace *serviceA* a *serviceB* .
* Místo dev *azureuser* je odvozen z *výchozí*.
* Najít aktualizovanou verzi *serviceA* je nasazený do *azureuser*.

Při použití *azureuser*, všechny požadavky na *serviceA* budou směrovány na aktualizovanou verzi v *azureuser*. Požadavek na *serviceB* se nejprve pokusí směrovat *azureuser* verzi *serviceB*. Protože neexistuje, bude směrována na *výchozí* verzi *serviceB*. Pokud *azureuser* verzi *serviceA* Odebereme, všechny požadavky na *serviceA* se vrátit zpět k použití *výchozí* verzi *serviceA*.

## <a name="next-steps"></a>Další postup

Abyste mohli začít používat Azure Dev mezery, najdete v následujících úvodních příručkách:

* [Java pomocí rozhraní příkazového řádku a Visual Studio Code](quickstart-java.md)
* [.NET core pomocí rozhraní příkazového řádku a Visual Studio Code](quickstart-netcore.md)
* [.NET Core with Visual Studio 2017](quickstart-netcore-visualstudio.md)
* [Node.js pomocí rozhraní příkazového řádku a Visual Studio Code](quickstart-nodejs.md)

Abyste mohli začít s vývojem pro tým, naleznete v tématu některého z těchto článků:

* [Vývoj v týmu – Java pomocí rozhraní příkazového řádku a Visual Studio Code](team-development-java.md)
* [Vývoj v týmu – .NET Core pomocí rozhraní příkazového řádku a Visual Studio Code](team-development-netcore.md)
* [Vývoj v týmu – .NET Core pomocí sady Visual Studio 2017](team-development-netcore-visualstudio.md)
* [Vývoj v týmu - Node.js pomocí rozhraní příkazového řádku a Visual Studio Code](team-development-nodejs.md)
