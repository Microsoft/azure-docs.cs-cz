---
title: Řešení potíží
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/11/2018
ms.topic: conceptual
description: Rychlý vývoj na platformě Kubernetes s využitím kontejnerů a mikroslužeb v Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s '
ms.openlocfilehash: 6ab2e0866c4e6c5cc8f89cb490504f6ca6a076fc
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019652"
---
# <a name="troubleshooting-guide"></a>Průvodce odstraňováním potíží

Tato příručka obsahuje informace o běžných problémů, možná bude při používání Azure Dev mezery.

Pokud máte problém s použitím Azure Dev Spaces, vytvořte [problém v úložišti Azure dev Spaces GitHubu](https://github.com/Azure/dev-spaces/issues).

## <a name="enabling-detailed-logging"></a>Povolení podrobného protokolování

Pokud chcete řešit problémy efektivněji, může vám pomoct vytvořit podrobnější protokoly pro kontrolu.

Pro rozšíření sady Visual Studio, nastavte `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` proměnné prostředí na hodnotu 1. Je potřeba restartovat Visual Studio pro proměnné prostředí se projeví. Po povolení budou do vašeho `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` adresáře zapsány podrobné protokoly.

V rozhraní příkazového řádku, můžete pomocí výstupní informace během provádění příkazu `--verbose` přepnout. Můžete také procházet podrobnější protokoly v `%TEMP%\Azure Dev Spaces`. Na počítači Mac, můžete najít svého adresáře TEMP spuštěním `echo $TMPDIR` z okna terminálu. Na počítači s Linuxem adresář TEMP je obvykle `/tmp`.

## <a name="debugging-services-with-multiple-instances"></a>Ladění služeb s více instancemi

V současné době Azure Dev Spaces fungovat nejlépe při ladění jedné instance nebo pod. Soubor azds. yaml obsahuje nastavení *replicaCount*, které označuje počet lusků, které Kubernetes pro vaši službu spouští. Pokud změníte replicaCount a nakonfigurujete aplikaci tak, aby spouštěla více lusků pro danou službu, ladicí program se připojí k prvnímu pod, pokud je uveden abecedně. Ladicí program se připojí k jinému pod při recyklování původní pod, což může vést k neočekávanému chování.

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Chyba "nepovedlo se vytvořit Azure Dev prostory kontroleru.

### <a name="reason"></a>Reason
Tato chyba může zobrazit, když dojde k chybě při vytváření kontroleru. Pokud se jedná o přechodnou chybu, odstraňte a znovu vytvořte kontroler, abyste ho opravili.

### <a name="try"></a>Vyzkoušení

Odstraňte kontroler:

```bash
azds remove -g <resource group name> -n <cluster name>
```

K odstranění kontroleru je nutné použít Azure Dev Spaces CLI. Z aplikace Visual Studio není možné odstranit kontroler. Nemůžete také nainstalovat Azure Dev Spaces CLI do Azure Cloud Shell, takže nemůžete odstranit řadič z Azure Cloud Shell.

Pokud nemáte nainstalované rozhraní příkazového řádku Azure Dev Spaces, můžete ho nejdřív nainstalovat pomocí následujícího příkazu a pak odstranit kontroler:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

Opětovné vytvoření kontroleru můžete udělat v sadě Visual Studio nebo rozhraní příkazového řádku. Příklady najdete v tématu věnovaném [vývoji týmu](quickstart-team-development.md) nebo vývoji pomocí Průvodce rychlým startem [.NET Core](quickstart-netcore-visualstudio.md) .

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
Při použití souboru Dockerfile s více fázemi se zobrazí chyba, že se *Služba nedá spustit* . V takové situaci obsahuje podrobný výstup následující text:

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

K této chybě dochází, protože uzly AKS používají starší verzi Docker, která nepodporuje buildy ve více fázích. Chcete-li se vyhnout sestavením s více fázemi, přepište své souboru Dockerfile.

### <a name="rerunning-a-service-after-controller-re-creation"></a>Opětovné spuštění služby po opětovném vytvoření kontroleru
Po odebrání a opětovném vytvoření kontroleru Azure Dev Spaces přidruženého k tomuto clusteru se zobrazí chyba, že se *služba nemůže spustit* , když se pokusíte znovu spustit službu. V takové situaci obsahuje podrobný výstup následující text:

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

K této chybě dochází, protože odebráním kontroleru pro vývoj prostorů neodeberete služby dříve nainstalované tímto kontrolérem. Opětovné vytvoření kontroleru a pak zkusíte ke spouštění služeb pomocí nového řadiče se nezdaří, protože staré služby jsou stále na místě.

Pokud chcete tento problém vyřešit, pomocí `kubectl delete` příkazu ručně odeberte staré služby z clusteru a pak znovu spusťte vývojové prostory a nainstalujte nové služby.

## <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Překlad názvů DNS pro veřejnou adresu URL související se službou Dev prostory nezdaří

Můžete nakonfigurovat koncový bod veřejné adresy URL pro vaši službu zadáním `--public` přepínače `azds prep` na příkaz nebo zaškrtnutím `Publicly Accessible` políčka v aplikaci Visual Studio. Veřejný název DNS se automaticky zaregistruje při spuštění služby ve vývojových prostorech. Pokud tento název DNS není zaregistrovaný, zobrazí se ve webovém prohlížeči při připojování k veřejné adrese URL *Stránka nemůžete zobrazit* nebo *se k webu nedá dostat* chyba.

### <a name="try"></a>Zkuste:

Můžete použít následující příkaz, který seznam všech adres URL související s vašimi službami Dev mezery:

```cmd
azds list-uris
```

Pokud je adresa URL v *čekající* stavu, která znamená, že Dev prostory stále čeká registraci DNS pro dokončení. V některých případech trvá několik minut, než registraci dokončit. Vývoj prostory otevře také tunel localhost pro každou službu, kterou můžete použít při čekání na registraci DNS.

Pokud adresa URL zůstane v nevyřízeném stavu po dobu delší než 5 minut, může to znamenat problém s externím DNS pod tím, že vytvoří veřejný koncový bod nebo Nginx příchozí řadič domény pod tím, který získá veřejný koncový bod. Pokud chcete odstranit tyto pody můžete použít následující příkazy. AKS automaticky znovu vytvoří odstraněnou lusky.

```cmd
kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
```

## <a name="error-required-tools-and-configurations-are-missing"></a>Chyba "Vyžaduje nástroje a konfigurace nebyly nalezeny"

K této chybě může dojít při spuštění VS Code: "[Azure Dev prostory] nástroje a konfigurací pro sestavení a ladění. [název projektu]' chybí požadované."
Chyba znamená, že tento azds.exe není v proměnné prostředí PATH, jak je vidět ve VS Code.

### <a name="try"></a>Zkuste:

Spusťte VS Code z příkazového řádku, kde je správně nastavit proměnné prostředí PATH.

## <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Chyba: požadované nástroje pro sestavení a ladění ProjectName jsou zastaralé.

Tato chyba se zobrazí v Visual Studio Code, pokud máte novější verzi rozšíření VS Code pro Azure Dev Spaces, ale starší verzi Azure Dev Spaces CLI.

### <a name="try"></a>Vyzkoušení

Stáhněte a nainstalujte si nejnovější verzi rozhraní příkazového řádku Azure Dev Spaces:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

## <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Chyba 'azds' nebyl rozpoznán jako vnitřního ani vnějšího příkazu, spustitelného programu nebo dávkového souboru
 
Pokud azds.exe není nainstalovaná nebo správně nakonfigurovaný, může se zobrazit tato chyba.

### <a name="try"></a>Zkuste:

1. Ověřte umístění% ProgramFiles%/Microsoft SDKs\Azure\Azure dev Spaces CLI pro azds. exe. Pokud existuje, přidejte do proměnné prostředí PATH v tomto umístění.
2. Pokud není nainstalovaná azds.exe, spusťte následující příkaz:

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

## <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Upozornění "soubor Dockerfile se nepodařilo vygenerovat z důvodu nepodporovaný jazyk.
Azure Dev prostory poskytuje nativní podporu pro C# nebo Node.js. Při spuštění *azds prep* do adresáře, který obsahuje kód napsaný v jednom z těchto jazyků, prostory vývoj Azure automaticky vytvoří odpovídající soubor Dockerfile za vás.

Azure Dev Spaces můžete dál používat s kódem napsaným v jiných jazycích, ale před prvním spuštěním *azds* musíte ručně vytvořit souboru Dockerfile.

### <a name="try"></a>Zkuste:
Pokud je vaše aplikace napsána v jazyce, který Azure Dev Spaces není nativně podporován, je nutné poskytnout odpovídající souboru Dockerfile k vytvoření image kontejneru, ve které je spuštěn váš kód. Docker poskytuje [seznam osvědčených postupů pro psaní fázemi](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) a [souboru Dockerfile odkaz](https://docs.docker.com/engine/reference/builder/) , který vám může poznamenat psaní souboru Dockerfile, které vyhovuje vašim potřebám.

Jakmile budete mít odpovídající soubor Dockerfile v místě, abyste mohli pokračovat spuštění *azds nahoru* ke spuštění aplikace v Azure Dev mezery.

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Chyba "upstream Chyba připojení nebo odpojení/reset před záhlaví"
Při pokusu o přístup ke službě, může se zobrazit tato chyba. Například když přejdete na adresu URL služby v prohlížeči. 

### <a name="reason"></a>Důvod 
Port kontejneru není k dispozici. Tomuto problému může dojít, protože: 
* Kontejner se stále ještě probíhá sestavíte a nasadíte. Tento problém může nastat, pokud spustíte `azds up` nebo spuštění ladicího programu a pak zkuste přístup ke kontejneru předtím, než byl úspěšně nasazen.
* Konfigurace portu není konzistentní napříč vaší _soubor Dockerfile_, diagram helmu a libovolný kód serveru, které se otevře port.

### <a name="try"></a>Zkuste:
1. Jestli je kontejner právě vytvořená/nasazuje, můžete počkejte 2-3 sekund a zkuste to znovu přístupu ke službě. 
1. Zkontrolujte konfiguraci portů. Zadaná čísla portů by měla být **shodná** se všemi těmito prostředky:
    * **Souboru Dockerfile** `EXPOSE` Určené instrukcí.
    * **[Graf Helm](https://docs.helm.sh):** Určené hodnotami `internalPort` `values.yml` a pro službu (často se nachází v souboru), `externalPort`
    * Žádné porty se otevřely v kódu aplikace, například v Node.js: `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>Nebyl nalezen konfigurační soubor
Spuštění `azds up` a zobrazí následující chyba: `Config file not found: .../azds.yaml`

### <a name="reason"></a>Důvod
Je nutné spustit `azds up` z kořenového adresáře kódu, které chcete spustit, a je třeba inicializovat složce kód ke spuštění Azure Dev mezer.

### <a name="try"></a>Zkuste:
1. Do kořenové složky, která obsahuje kód služby změňte aktuální adresář. 
1. Pokud nemáte _azds.yaml_ souboru ve složce kód spustit `azds prep` ke generování Docker, Kubernetes a Azure Dev prostory prostředky.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Chyba: ' Program kanálu ' azds ' se neočekávaně ukončil s kódem 126. '
Spouští se ladicí program VS Code může někdy vést k této chybě.

### <a name="try"></a>Zkuste:
1. Zavřete a znovu spusťte VS Code.
2. Znovu, stiskněte F5.

## <a name="debugging-error-failed-to-find-debugger-extension-for-typecoreclr"></a>Chyba: Nepodařilo se najít rozšíření ladicího programu pro typ: coreclr"ladění
Spuštění ladicího programu VS Code zaznamená chybu: `Failed to find debugger extension for type:coreclr.`

### <a name="reason"></a>Důvod
Rozšíření VS Codu pro jazyk C# nainstalována na vývojovém počítači nemáte. Toto C# rozšíření zahrnuje podporu ladění pro .NET Core (CoreCLR).

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

### <a name="reason"></a>Reason 
Kontext sestavení je ve výchozím nastavení na úrovni projektu nebo služby, proto nelze najít projekt knihovny, který používáte.

### <a name="try"></a>Zkuste:
Co je potřeba udělat:
1. Upravit _azds.yaml_ souboru nastavit kontext sestavení na úrovni řešení.
2. Upravit _soubor Dockerfile_ a _Dockerfile.develop_ soubory k odkazování na projekt ( _.csproj_) soubory správně, vzhledem k nové sestavení kontextu.
3. Místo _.dockerignore_ souboru vedle souboru .sln a podle potřeby upravte.

Můžete najít na příklad https://github.com/sgreenmsft/buildcontextsample

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>Chyba autorizace "Microsoft.DevSpaces/register/action.
Ke správě Azure Dev Spaces potřebujete ve svém předplatném Azure přístup *vlastníka* nebo *přispěvatele* . Tato chyba se může zobrazit, pokud se pokoušíte spravovat vývojové prostory a nemáte oprávnění *vlastníka* nebo přispěvatele k přidruženému předplatnému Azure.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Reason
Vybrané předplatné Azure nebyla zaregistrována `Microsoft.DevSpaces` oboru názvů.

### <a name="try"></a>Zkuste:
Někdo s přístupem k roli vlastníka nebo přispěvatele k předplatnému Azure spuštěním následujícího příkazu rozhraní příkazového řádku Azure k ruční registraci `Microsoft.DevSpaces` obor názvů:

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="dev-spaces-times-out-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Při *čekání na sestavení image kontejneru* se vyprší doba vývojového prostoru... krok s AKS virtuálními uzly

### <a name="reason"></a>Reason
K tomuto časovému limitu dojde, když se pokusíte použít vývojové prostory ke spuštění služby, která je nakonfigurovaná tak, aby běžela ve [virtuálním uzlu AKS](https://docs.microsoft.com/azure/aks/virtual-nodes-portal). Vývojové prostory aktuálně nepodporují vytváření ani ladění služeb na virtuálních uzlech.

Pokud spustíte `azds up` `--verbose` s přepínačem nebo povolíte podrobné protokolování v aplikaci Visual Studio, zobrazí se další podrobnosti:

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

Výše uvedený příkaz ukazuje, že je uzel Service přiřazený k *virtuálnímu uzlu-ACI-Linux*, který je virtuálním uzlem.

### <a name="try"></a>Zkuste:
Aktualizujte graf Helm, aby služba odstranila všechny hodnoty *nodeSelector* a/nebo *tolerovány* , které umožňují spuštění služby ve virtuálním uzlu. Tyto hodnoty jsou obvykle definovány v `values.yaml` souboru grafu.

I nadále můžete používat cluster AKS s povolenou funkcí virtuálních uzlů, pokud služba, kterou chcete sestavit/ladit prostřednictvím vývojových prostorů, běží na uzlu virtuálního počítače. Toto je výchozí konfigurace.

## <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>"Chyba: Nelze nalézt připravený tiller pod" při spuštění vývoje prostorů

### <a name="reason"></a>Důvod
K této chybě dochází, pokud klient Helm může už sdělit pod Tiller spuštěné v clusteru.

### <a name="try"></a>Zkuste:
Obvykle restartování agentské uzly v clusteru vyřeší tento problém.

## <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>"Chyba: Release azds-\<identifikátor\>-\<Space\>ServiceNamese\> nezdařil: služby"\<ServiceName-\<\>' už existuje "nebo" přístup k získání byl \<odepřen\>pro ServiceName, úložiště neexistuje nebo může vyžadovat "Docker login"

### <a name="reason"></a>Reason
K `helm install`těmto chybám může docházet v případě, že ve stejném vývojovém prostoru budete kombinovat spuštěné příkazy Helm (například, `helm upgrade`nebo `azds up` `helm delete`) `azds down`s příkazy pro vývoj prostorů (například a). K nim dochází, protože vývojové prostory mají svou vlastní instanci pokladny, která je v konfliktu s vaší vlastní instancí pokladny spuštěnou ve stejném vývojovém prostoru.

### <a name="try"></a>Zkuste:
Je možné použít příkazy Helm i příkazy pro vývoj v rámci stejného clusteru AKS, ale každý obor názvů s povoleným místem pro vývoj by měl použít buď jednu, nebo druhou.

Předpokládejme například, že použijete příkaz Helm ke spuštění celé aplikace v nadřazeném vývojovém prostoru. Můžete vytvořit podřízené vývojové prostory z této nadřazené položky, pomocí vývojových prostorů spouštět jednotlivé služby uvnitř podřízených vývojových prostorů a testovat služby společně. Až budete připraveni k vrácení změn se změnami, nasaďte aktualizovaný kód do nadřazeného vývojového prostoru pomocí příkazu Helm. Nepoužívejte `azds up` ke spuštění aktualizované služby v nadřazeném vývojovém prostoru, protože bude v konfliktu se službou nejprve spuštěna pomocí Helm.

## <a name="azure-dev-spaces-proxy-can-interfere-with-other-pods-running-in-a-dev-space"></a>Proxy služby Azure Dev mezer může narušovat dalších podů se spuštěnou v prostoru vývoj

### <a name="reason"></a>Důvod
Když povolíte prostory vývoj oboru názvů v clusteru AKS, volá se kontejnerem Další _mindaro proxy_ je nainstalován ve všech pody spuštěné v tomto oboru názvů. Tento kontejner zachycuje volání služeb v rozhraní pod, které je nedílnou součástí možností vývoje týmu pro vývojové prostory; může ale kolidovat s některými službami běžícími v těchto luskech. Je známo, že brání v luskech se spuštěnou mezipamětí Azure pro Redis, což způsobuje chyby a selhání připojení v primární nebo sekundární komunikaci.

### <a name="try"></a>Zkuste:
Ovlivněné lusky můžete přesunout do oboru názvů v clusteru, ve kterém _nejsou_ povolené vývojové prostory. Zbytek vaší aplikace může dál běžet v oboru názvů s povoleným vývojovým prostorem. Vývoj prostory nelze nainstalovat _mindaro proxy_ kontejneru uvnitř Dev prostory povolených oborů názvů.

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Azure Dev prostory vypadá, že nepodporuje použití Můj existující soubor Dockerfile k vytvoření kontejneru

### <a name="reason"></a>Důvod
Azure Dev prostory můžete nakonfigurovat tak, aby odkazoval na konkrétní _soubor Dockerfile_ ve vašem projektu. Pokud se zobrazí Azure Dev Spaces nepoužívá _souboru Dockerfile_ , kterou očekáváte k sestavování kontejnerů, možná budete muset explicitně sdělit Azure dev Spaces které souboru Dockerfile se mají použít. 

### <a name="try"></a>Zkuste:
Otevřete soubor _azds. yaml_ , který Azure dev Spaces vygeneroval v projektu. Pomocí direktivy *Configuration-> vývoj-> Build-> souboru Dockerfile* nasměrujte na souboru Dockerfile, který chcete použít:

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

## <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Chyba "vnitřní kukátko se nezdařilo: Sledujte ENOSPC" při připojování ladění k aplikaci Node. js

### <a name="reason"></a>Reason

Uzel, na kterém je spuštěná aplikace Node. js, ke které se pokoušíte připojit pomocí ladicího programu, překročil hodnotu *_user_watches FS. inotify. Max* . V některých případech [může být výchozí hodnota *FS. inotify. Max _user_watches* příliš malá, aby bylo možné zpracovat připojení ladicího programu přímo k poli pod](https://github.com/Azure/AKS/issues/772).

### <a name="try"></a>Vyzkoušení
Dočasným řešením tohoto problému je zvýšit hodnotu *FS. inotify. Max _user_watches* na každém uzlu v clusteru a restartovat tento uzel, aby se změny projevily.

## <a name="new-pods-are-not-starting"></a>Nespouští se nové lusky.

### <a name="reason"></a>Reason

Inicializátor Kubernetes nemůže použít PodSpec pro nové lusky z důvodu změn oprávnění RBAC do role *Správce clusteru* v clusteru. Nový objekt pod může mít také neplatnou PodSpec, například účet služby spojený s objektem pod již neexistuje. Chcete-li zobrazit lusky, které jsou ve stavu *čekání* v důsledku problému s inicializátorem `kubectl get pods` , použijte příkaz:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Tento problém může mít vliv na lusky ve *všech oborech názvů* v clusteru včetně oborů názvů, kde není povolený Azure dev Spaces.

### <a name="try"></a>Vyzkoušení

[Aktualizujte vývojové prostory CLI na nejnovější verzi](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) a pak odstraňte *azds InitializerConfiguration* z kontroleru Azure dev Spaces:

```bash
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl delete InitializerConfiguration azds
```

Po odebrání *azds InitializerConfiguration* z kontroleru Azure dev Spaces použijte `kubectl delete` k odebrání všech lusků ve stavu čekání na vyřízení . Po odebrání všech probíhajících lusků znovu nasaďte své lusky.

Pokud se nové lusky po opětovném nasazení stále zablokují ve stavu čekání `kubectl delete` , použijte k odebrání všech lusků ve stavu *čekání* na vyřízení. Po odebrání všech probíhajících lusků odstraňte řadič z clusteru a znovu ho nainstalujte:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Po opětovné instalaci kontroleru znovu nasaďte své lusky.

## <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Nesprávná oprávnění RBAC pro volání řadiče pro vývoj prostorů a rozhraní API

### <a name="reason"></a>Reason
Uživatel, který přistupuje k řadiči Azure Dev Spaces, musí mít přístup, aby mohl číst *kubeconfig* správce v clusteru AKS. Toto oprávnění je například k dispozici v [předdefinované roli Správce clusteru služby Azure Kubernetes](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions). Uživatel, který přistupuje k řadiči Azure Dev Spaces, musí mít také roli přispěvatele nebo *vlastníka* RBAC pro kontroler.

### <a name="try"></a>Vyzkoušení
Další podrobnosti o aktualizaci oprávnění uživatele pro cluster AKS jsou k dispozici [zde](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group).

Aktualizace role uživatele RBAC pro kontroler:

1. Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.
1. Přejděte do skupiny prostředků obsahující kontroler, který je obvykle stejný jako cluster AKS.
1. Zaškrtněte políčko *Zobrazit skryté typy* .
1. Klikněte na kontroler.
1. Otevřete podokno *Access Control (IAM)* .
1. Klikněte na kartu *přiřazení rolí* .
1. Klikněte na *Přidat* a pak na *Přidat přiřazení role*.
    * V případě *role* vyberte možnost *Přispěvatel* nebo *vlastník*.
    * Pro *přiřazení přístupu k* výběru *uživatele, skupiny nebo instančního objektu služby Azure AD*.
    * V poli *Vyberte* hledání pro uživatele, kterému chcete udělit oprávnění.
1. Klikněte na *Uložit*.

## <a name="controller-create-failing-due-to-controller-name-length"></a>Vytvoření kontroleru se nepodaří kvůli délce názvu kontroleru.

### <a name="reason"></a>Reason
Název kontroleru Azure Dev Spaces nemůže být delší než 31 znaků. Pokud je název vašeho kontroleru delší než 31 znaků, když povolíte vývojářské prostory v clusteru AKS nebo vytvoříte kontroler, zobrazí se chyba, například:

*Nepovedlo se vytvořit kontroler pro vývoj prostorů pro cluster "a-Controller-Name---------Long-AKS-východ-US": Název kontroleru Azure Dev Spaces a-Controller-Name-to------------------Long-------AKS Porušení omezení: Názvy řadičů Azure Dev Spaces můžou být jenom nanejvýš 31 znaků dlouhé.*

### <a name="try"></a>Vyzkoušení

Vytvořte kontroler s alternativním názvem:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

## <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Povolení neúspěšných vývojových prostorů při přidání fondů uzlů Windows do clusteru AKS

### <a name="reason"></a>Reason
V současné době je Azure Dev Spaces určen ke spouštění pouze v systémech Linux a pouze na uzlech. Pokud máte cluster AKS s fondem uzlů systému Windows, musíte zajistit, aby se Azure Dev Spaces lusky naplánovaly jenom na uzlech se systémem Linux. Pokud je naplánované spuštění Azure Dev Spaces pod uzlem v systému Windows, nespustí se ani povolení vývojových prostorů.

### <a name="try"></a>Vyzkoušení
[Přidáním příchuti](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) do clusteru AKS zajistěte, aby se na uzlu Windows neplánovalo spouštění oken Linux.

## <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Chyba: nenašly se žádné neobsahované uzly Linux ve stavu připraveno v clusteru. Aby bylo možné nasadit lusky v oboru názvů ' azds ', musí být v připraveném stavu aspoň jeden nev neaktivním uzlu Linux. "

### <a name="reason"></a>Reason

Azure Dev Spaces ve vašem clusteru AKS nešlo vytvořit kontroler, protože v připraveném stavu nemohlo najít uzel, který by byl v připraveném stavu, aby bylo možné naplánovat lusky. Azure Dev Spaces vyžaduje aspoň jeden uzel Linux v připraveném stavu, který umožňuje plánování lusků bez určení tolerování.

### <a name="try"></a>Vyzkoušení
[Aktualizujte konfiguraci chuti](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) v clusteru AKS, abyste zajistili, že alespoň jeden uzel pro Linux umožňuje plánování lusků bez určení jejich tolerovánosti. Také se ujistěte, že alespoň jeden uzel pro Linux, který umožňuje plánování v luskech bez určení tolerování, je ve stavu *připraveno* . Pokud bude mít váš uzel dlouhou dobu, než se dorazí na stav *připraveno* , můžete zkusit restartovat uzel.

## <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Chyba "Azure Dev Spaces rozhraní příkazového řádku není správně nainstalováno" při spuštění`az aks use-dev-spaces`

### <a name="reason"></a>Reason
Aktualizace rozhraní příkazového řádku Azure Dev Spaces změnila jeho cestu instalace. Pokud používáte verzi Azure CLI starší než 2.0.63, může se zobrazit tato chyba. Pokud chcete zobrazit verzi rozhraní příkazového řádku Azure CLI `az --version`, použijte.

```bash
$ az --version
azure-cli                         2.0.60 *
...
```

Bez ohledu na chybovou zprávu `az aks use-dev-spaces` při použití verze Azure CLI před 2.0.63 instalace proběhne úspěšně. Bez problémů můžete pokračovat v `azds` používání.

### <a name="try"></a>Vyzkoušení
Aktualizujte instalaci [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) na 2.0.63 nebo novější. Tím se vyřeší chybová zpráva, která se zobrazí při `az aks use-dev-spaces`spuštění. Případně můžete dál používat aktuální verzi rozhraní příkazového řádku Azure CLI a Azure Dev Spaces CLI.


## <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>Automatické škálování pod automatickým škálováním nefunguje ve vývojovém prostoru

### <a name="reason"></a>Reason

Když službu spustíte ve vývojovém prostoru, je tato služba [vložená s dalšími kontejnery pro instrumentaci](how-dev-spaces-works.md#prepare-your-aks-cluster) a všechny kontejnery v rámci musí mít omezení prostředků a požadavky nastavené na automatické škálování vodorovně pod. 


Požadavky na prostředky a omezení lze použít pro vložený kontejner (devspaces-proxy) přidáním `azds.io/proxy-resources` poznámky k specifikaci pod. Hodnota by měla být nastavena na objekt JSON, který představuje oddíl prostředků specifikace kontejneru pro proxy server.

### <a name="try"></a>Vyzkoušení

Níže je uveden příklad anotace prostředků proxy, která se má použít pro specifikaci pod.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```