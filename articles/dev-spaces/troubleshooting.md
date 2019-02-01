---
title: Řešení potíží | Dokumentace Microsoftu
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 09/11/2018
ms.topic: article
description: Rychlý vývoj na platformě Kubernetes s využitím kontejnerů a mikroslužeb v Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
ms.openlocfilehash: 5be6f99067f1209fcd131dfc33c46995b2a537f8
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55498297"
---
# <a name="troubleshooting-guide"></a>Průvodce odstraňováním potíží

Tato příručka obsahuje informace o běžných problémů, možná bude při používání Azure Dev mezery.

## <a name="enabling-detailed-logging"></a>Povolení podrobného protokolování

K odstraňování problémů efektivněji, může pomoct vytvořit podrobnější protokoly ke kontrole.

Pro rozšíření sady Visual Studio, nastavte `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` proměnné prostředí na hodnotu 1. Je potřeba restartovat Visual Studio pro proměnné prostředí se projeví. Po povolení podrobných protokolů zapisován vaše `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` adresáře.

V rozhraní příkazového řádku, můžete pomocí výstupní informace během provádění příkazu `--verbose` přepnout. Můžete také procházet podrobnější protokoly v `%TEMP%\Azure Dev Spaces`. Na počítači Mac, můžete najít svého adresáře TEMP spuštěním `echo $TMPDIR` z okna terminálu. Na počítači s Linuxem adresář TEMP je obvykle `/tmp`.

## <a name="debugging-services-with-multiple-instances"></a>Ladění služeb s více instancemi

V současné době Azure Dev prostory funguje nejlépe při ladění jednu instanci nebo pod. Soubor azds.yaml obsahuje nastavení, *replicaCount*, určující počet podů Kubernetes spuštěné pro vaši službu. Pokud změníte replicaCount nakonfigurovat svoji aplikaci spouštět několik podů se pro dané služby, ladicí program připojí k první pod při uvedeny v abecedním pořadí. Ladicí program připojí k jiné pod dojde k recyklování původní pod, což může způsobit neočekávané chování.

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Chyba "nepovedlo se vytvořit Azure Dev prostory kontroleru.

Tato chyba může zobrazit, když dojde k chybě při vytváření kontroleru. Pokud se jedná o přechodnou chybu, odstranit a znovu vytvořte kontroler ho opravit.

### <a name="try"></a>Zkuste:

Pokud chcete odstranit kontroleru, použijte rozhraní příkazového řádku Azure Dev mezery. Není možné provést v sadě Visual Studio nebo službě Cloud Shell. Pokud chcete nainstalovat rozhraní příkazového řádku AZDS, nejprve nainstalovat rozhraní příkazového řádku Azure a pak spusťte tento příkaz:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

A pak spuštěním následujícího příkazu odstraňte kontroleru:

```cmd
azds remove -g <resource group name> -n <cluster name>
```

Opětovné vytvoření kontroleru můžete udělat v sadě Visual Studio nebo rozhraní příkazového řádku. Postupujte podle pokynů v kurzech, jako kdyby spouští poprvé.


## <a name="error-service-cannot-be-started"></a>Chyba "službu nelze spustit."

Tato chyba může zobrazit, když kódu služby se nepodaří spustit. Příčinou je často v uživatelském kódu. Pokud chcete získat další diagnostické informace, proveďte následující změny k příkazům a nastavení:

### <a name="try"></a>Zkuste:

Na příkazovém řádku:

Při použití _azds.exe_, použijte--verbose možnost příkazového řádku a použijte možnost příkazového řádku--output k určení formátu výstupního.
 
```cmd
azds up --verbose --output json
```

V sadě Visual Studio:

1. Otevřít **nástroje > Možnosti** a v části **projekty a řešení**, zvolte **sestavíte a spustíte**.
2. Změnit nastavení pro **podrobnosti výstupu sestavení projektu nástroje MSBuild** k **podrobné** nebo **diagnostických**.

    ![Možnosti nástrojů – snímek obrazovky dialogového okna](media/common/VerbositySetting.PNG)
    
### <a name="multi-stage-dockerfiles"></a>Vícefázové soubory Dockerfile:
Zobrazí *službu nelze spustit* při použití souboru Dockerfile vícefázové došlo k chybě. V takovém případě podrobný výstup obsahuje následující text:

```cmd
$ azds up -v
Using dev space 'default' with target 'AksClusterName'
Synchronizing files...6s
Installing Helm chart...2s
Waiting for container image build...10s
Building container image...
Step 1/12 : FROM [imagename:tag] AS base
Error parsing reference: "[imagename:tag] AS base" is not a valid repository/tag: invalid reference format
Failed to build container image.
Service cannot be started.
```

K této chybě dochází, protože sestavení používají starší verzi dockeru, který nepodporuje vícefázové uzlů AKS. Aby se zabránilo vícefázových sestavení, přepište vašem souboru Dockerfile.

### <a name="rerunning-a-service-after-controller-re-creation"></a>Opětovné spuštění služby po opětovné vytvoření kontroleru
Zobrazí *službu nelze spustit* při pokusu o znovu spustit službu po odebrat a pak znovu vytvářejí řadičem Azure Dev prostory přidružené k tomuto clusteru došlo k chybě. V takovém případě podrobný výstup obsahuje následující text:

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

K této chybě dochází, protože odebrání řadiče Dev prostory neodebere služby dříve nainstalovala služba kontroleru. Opětovné vytvoření kontroleru a pak zkusíte ke spouštění služeb pomocí nového řadiče se nezdaří, protože staré služby jsou stále na místě.

Chcete-li tento problém vyřešit, použijte `kubectl delete` příkaz ručně odebrat starý služby z clusteru a pak znovu spusťte prostory Dev k instalaci nových služeb.

## <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Překlad názvů DNS pro veřejnou adresu URL související se službou Dev prostory nezdaří

Veřejný koncový bod adresy URL pro vaši službu můžete nakonfigurovat tak, že zadáte `--public` přepněte `azds prep` příkaz, nebo výběrem `Publicly Accessible` zaškrtávacího políčka v sadě Visual Studio. Při spuštění služby v prostorách Dev, se automaticky registruje veřejný název DNS. Pokud tento název DNS není zaregistrovaný, zobrazí se *nelze zobrazit stránku* nebo *lokalitě je nedostupné* chyby ve webovém prohlížeči při připojování k veřejnou adresu URL.

### <a name="try"></a>Zkuste:

Můžete použít následující příkaz, který seznam všech adres URL související s vašimi službami Dev mezery:

```cmd
azds list-uris
```

Pokud je adresa URL v *čekající* stavu, která znamená, že Dev prostory stále čeká registraci DNS pro dokončení. V některých případech trvá několik minut, než registraci dokončit. Vývoj prostory otevře také tunel localhost pro každou službu, kterou můžete použít při čekání na registraci DNS.

Je-li adresy URL v *čekající* stavu po dobu více než 5 minut, může to znamenat problém s externí pod DNS, který vytvoří koncový bod veřejné nebo pod kontroleru příchozího přenosu dat nginx, která získá veřejný koncový bod. Pokud chcete odstranit tyto pody můžete použít následující příkazy. AKS automaticky znovu vytvoří odstraněné tyto pody.

```cmd
kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
```

## <a name="error-required-tools-and-configurations-are-missing"></a>Chyba "Vyžaduje nástroje a konfigurace nebyly nalezeny"

K této chybě může dojít při spuštění VS Code: "[Azure Dev prostory] nástroje a konfigurací pro sestavení a ladění. [název projektu]' chybí požadované."
Chyba znamená, že tento azds.exe není v proměnné prostředí PATH, jak je vidět ve VS Code.

### <a name="try"></a>Zkuste:

Spusťte VS Code z příkazového řádku, kde je správně nastavit proměnné prostředí PATH.

## <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Chyba "vyžaduje nástroje pro vytváření a ladění"projectname"jsou zastaralé."

Pokud máte novější verzi rozšíření VS Codu pro Azure Dev mezery, ale starší verzi rozhraní příkazového řádku Azure Dev prostory se zobrazí tato chyba ve Visual Studio Code.

### <a name="try"></a>Vyzkoušení

Stáhněte a nainstalujte nejnovější verzi rozhraní příkazového řádku Azure Dev mezery:

* [Windows](http://aka.ms/get-azds-windows)
* [Mac](http://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

## <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Chyba 'azds' nebyl rozpoznán jako vnitřního ani vnějšího příkazu, spustitelného programu nebo dávkového souboru
 
Pokud azds.exe není nainstalovaná nebo správně nakonfigurovaný, může se zobrazit tato chyba.

### <a name="try"></a>Zkuste:

1. Zkontrolujte umístění %ProgramFiles%/Microsoft SDKs\Azure\Azure Dev prostory CLI (Preview) pro azds.exe. Pokud existuje, přidejte do proměnné prostředí PATH v tomto umístění.
2. Pokud není nainstalovaná azds.exe, spusťte následující příkaz:

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

## <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Upozornění "soubor Dockerfile se nepodařilo vygenerovat z důvodu nepodporovaný jazyk.
Azure Dev prostory poskytuje nativní podporu pro C# nebo Node.js. Při spuštění *azds prep* do adresáře, který obsahuje kód napsaný v jednom z těchto jazyků, prostory vývoj Azure automaticky vytvoří odpovídající soubor Dockerfile za vás.

Stále můžete Azure Dev prostory pomocí kódu napsaného v jiných jazycích, ale musíte ručně vytvořit soubor Dockerfile dřív, než spustíte *azds nahoru* poprvé.

### <a name="try"></a>Zkuste:
Pokud vaše aplikace je napsán v jazyce, Azure Dev prostory nepodporuje nativně, budete muset zadat příslušný soubor Dockerfile pro sestavení image kontejneru, spouštění kódu. Docker nabízí [seznam osvědčených postupů pro psaní soubory Dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) a [odkaz na soubor Dockerfile](https://docs.docker.com/engine/reference/builder/) , které vám umožňují se píše soubor Dockerfile, který vyhovuje vašim potřebám.

Jakmile budete mít odpovídající soubor Dockerfile v místě, abyste mohli pokračovat spuštění *azds nahoru* ke spuštění aplikace v Azure Dev mezery.

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Chyba "upstream Chyba připojení nebo odpojení/reset před záhlaví"
Při pokusu o přístup ke službě, může se zobrazit tato chyba. Například když přejdete na adresu URL služby v prohlížeči. 

### <a name="reason"></a>Důvod 
Port kontejneru není k dispozici. Tomuto problému může dojít, protože: 
* Kontejner se stále ještě probíhá sestavíte a nasadíte. Tento problém může nastat, pokud spustíte `azds up` nebo spuštění ladicího programu a pak zkuste přístup ke kontejneru předtím, než byl úspěšně nasazen.
* Konfigurace portu není konzistentní napříč vaší _soubor Dockerfile_, diagram helmu a libovolný kód serveru, které se otevře port.

### <a name="try"></a>Zkuste:
1. Jestli je kontejner právě vytvořená/nasazuje, můžete počkejte 2-3 sekund a zkuste to znovu přístupu ke službě. 
1. Zkontrolujte konfiguraci portů. Zadaný port čísla by měla být **identické** ve všech následujících prostředků:
    * **Soubor Dockerfile:** Určená `EXPOSE` instrukce.
    * **[Diagram helmu](https://docs.helm.sh):** Určená `externalPort` a `internalPort` hodnoty pro službu (nacházejí se často ve `values.yml` souboru),
    * Žádné porty se otevřely v kódu aplikace, například v Node.js: `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>Nebyl nalezen konfigurační soubor
Spuštění `azds up` a zobrazí následující chyba: `Config file not found: .../azds.yaml`

### <a name="reason"></a>Důvod
Je nutné spustit `azds up` z kořenového adresáře kódu, které chcete spustit, a je třeba inicializovat složce kód ke spuštění Azure Dev mezer.

### <a name="try"></a>Zkuste:
1. Do kořenové složky, která obsahuje kód služby změňte aktuální adresář. 
1. Pokud nemáte _azds.yaml_ souboru ve složce kód spustit `azds prep` ke generování Docker, Kubernetes a Azure Dev prostory prostředky.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Chyba: "Cílového programu"azds"neočekávaně ukončil s kódem 126."
Spouští se ladicí program VS Code může někdy vést k této chybě.

### <a name="try"></a>Zkuste:
1. Zavřete a znovu spusťte VS Code.
2. Znovu, stiskněte F5.

## <a name="debugging-error-failed-to-find-debugger-extension-for-typecoreclr"></a>Chyba: Nepodařilo se najít rozšíření ladicího programu pro typ: coreclr"ladění
Spuštění ladicího programu VS Code zaznamená chybu: `Failed to find debugger extension for type:coreclr.`

### <a name="reason"></a>Důvod
Rozšíření VS Codu pro jazyk C# nainstalována na vývojovém počítači nemáte. Rozšíření jazyka C# obsahuje podporu ladění pro.Net Core (CoreCLR).

### <a name="try"></a>Zkuste:
Nainstalujte [rozšíření VS Codu pro jazyk C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>Chyba ladění není podporován typ ladění nakonfigurováno "coreclr.
Spuštění ladicího programu VS Code zaznamená chybu: `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>Důvod
Rozšíření VS Codu pro Azure Dev prostory nainstalována na vývojovém počítači nemáte.

### <a name="try"></a>Zkuste:
Nainstalujte [rozšíření VS Codu pro Azure Dev prostory](get-started-netcore.md).

## <a name="debugging-error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Chyba ladění "neplatný"cwd"hodnota" / src ". Systém nemůže najít zadaný soubor." nebo "spuštění: program '/ src / [cesta k projektu binární]' neexistuje."
Spuštění ladicího programu VS Code zaznamená chybu `Invalid 'cwd' value '/src'. The system cannot find the file specified.` a/nebo `launch: program '/src/[path to project executable]' does not exist`

### <a name="reason"></a>Důvod
Ve výchozím nastavení, používá rozšíření VS Codu `src` jako pracovní adresář pro projekt v kontejneru. Pokud jste aktualizovali vaši `Dockerfile` Pokud chcete zadat jinou pracovní adresář, může se zobrazit tato chyba.

### <a name="try"></a>Zkuste:
Aktualizace `launch.json` soubor `.vscode` podadresář složky projektu. Změnit `configurations->cwd` směrnice tak, aby odkazoval do stejného adresáře jako `WORKDIR` definované ve vašem projektu `Dockerfile`. Budete muset taky aktualizovat `configurations->program` také směrnice.

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>Název typu nebo oboru názvů 'Moje knihovna' nebyl nalezen

### <a name="reason"></a>Důvod 
Kontext sestavení na úrovni projektu nebo služby ve výchozím nastavení se proto projekt knihovny, které používáte nebyl nalezen.

### <a name="try"></a>Zkuste:
Co je potřeba udělat:
1. Upravit _azds.yaml_ souboru nastavit kontext sestavení na úrovni řešení.
2. Upravit _soubor Dockerfile_ a _Dockerfile.develop_ soubory k odkazování na projekt (_.csproj_) soubory správně, vzhledem k nové sestavení kontextu.
3. Místo _.dockerignore_ souboru vedle souboru .sln a podle potřeby upravte.

Můžete najít na příklad https://github.com/sgreenmsft/buildcontextsample

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>Chyba autorizace "Microsoft.DevSpaces/register/action.
Potřebujete *vlastníka* nebo *Přispěvatel* přístup ve vašem předplatném Azure ke správě Azure Dev mezery. Tato chyba může zobrazit, pokud se snažíte Správa vývoje prostorů a není nutné *vlastníka* nebo *Přispěvatel* přístup k přidruženému předplatnému Azure.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Důvod
Vybrané předplatné Azure nebyla zaregistrována `Microsoft.DevSpaces` oboru názvů.

### <a name="try"></a>Zkuste:
Někdo s přístupem k roli vlastníka nebo přispěvatele k předplatnému Azure spuštěním následujícího příkazu rozhraní příkazového řádku Azure k ruční registraci `Microsoft.DevSpaces` obor názvů:

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="dev-spaces-times-out-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Vývoj prostory vyprší časový limit na *čekání na sestavení image kontejneru...*  krok s virtuálních uzlů AKS

### <a name="reason"></a>Důvod
Proběhne, když použijete prostory Dev ke spuštění služby, který je konfigurován pro běh [virtuálního uzlu AKS](https://docs.microsoft.com/azure/aks/virtual-nodes-portal). Vývoj prostory aktuálně nepodporuje sestavování nebo ladění služeb na virtuální uzly.

Pokud spustíte `azds up` s `--verbose` přepínač nebo povolit podrobné protokolování v sadě Visual Studio se zobrazí další podrobnosti:

```cmd
Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

Ukazuje to, že byl přiřazen služby pod *virtuální node-aci-linux*, což je virtuálního uzlu.

### <a name="try"></a>Zkuste:
Aktualizace grafu Helm pro službu odstraňte přitom všechny *nodeSelector* a/nebo *tolerations* hodnoty, které umožňují bude služba spouštět ve virtuálním uzlu. Tyto hodnoty jsou obvykle definovány v grafu `values.yaml` souboru.

Stále můžete použít cluster AKS, který má funkci virtuální uzly povolena, pokud se službu, kterou chcete sestavení/ladění prostřednictvím prostorů Dev běží na uzlu virtuálního počítače. Toto je výchozí konfigurace.

## <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>"Chyba: Nelze nalézt připravený tiller pod" při spuštění vývoje prostorů

### <a name="reason"></a>Důvod
K této chybě dochází, pokud klient Helm může už sdělit pod Tiller spuštěné v clusteru.

### <a name="try"></a>Zkuste:
Obvykle restartování agentské uzly v clusteru vyřeší tento problém.

## <a name="azure-dev-spaces-proxy-can-interfere-with-other-pods-running-in-a-dev-space"></a>Proxy služby Azure Dev mezer může narušovat dalších podů se spuštěnou v prostoru vývoj

### <a name="reason"></a>Důvod
Když povolíte prostory vývoj oboru názvů v clusteru AKS, volá se kontejnerem Další _mindaro proxy_ je nainstalován ve všech pody spuštěné v tomto oboru názvů. Tento kontejner zachycuje volání služeb v podu, která je nedílnou součástí vývoje prostorů týmu vývojářských funkcí; Nicméně by mohl narušovat určité služby spuštěné v tyto pody. Je známo ovlivňovat podů běží mezipaměti Azure Redis, příčinou chyb připojení a chyby v komunikaci typu hlavní/podřízený.

### <a name="try"></a>Zkuste:
Ovlivněné podů můžete přesunout do oboru názvů v clusteru, který nemá _není_ Dev prostory povolena. Zbytek aplikace můžete dál ke spouštění uvnitř oboru názvů s povoleným Dev mezery. Vývoj prostory nelze nainstalovat _mindaro proxy_ kontejneru uvnitř Dev prostory povolených oborů názvů.

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Azure Dev prostory vypadá, že nepodporuje použití Můj existující soubor Dockerfile k vytvoření kontejneru

### <a name="reason"></a>Důvod
Azure Dev prostory můžete nakonfigurovat tak, aby odkazoval na konkrétní _soubor Dockerfile_ ve vašem projektu. Pokud se zdá, že nepoužívá prostory vývoj Azure _soubor Dockerfile_ očekáváte, že k vytvoření kontejnerů, bude pravděpodobně nutné explicitně zjistit prostory Azure Dev, které soubor Dockerfile používat. 

### <a name="try"></a>Zkuste:
Otevřít _azds.yaml_ souboru této Azure Dev prostory vygenerováno v projektu. Použít *-> Konfigurace vývoj -> build -> soubor dockerfile* směrnice tak, aby odkazoval na soubor Dockerfile, který chcete použít:

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```
