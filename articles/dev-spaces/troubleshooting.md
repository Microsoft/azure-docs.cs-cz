---
title: Poradce při potížích
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/25/2019
ms.topic: conceptual
description: Rychlý vývoj na platformě Kubernetes s využitím kontejnerů a mikroslužeb v Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s '
ms.openlocfilehash: 5d327dd1041172bc546b2e0cb5ec3a140f401d84
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072197"
---
# <a name="troubleshooting-guide"></a>Průvodce odstraňováním potíží

Tato příručka obsahuje informace o běžných problémů, možná bude při používání Azure Dev mezery.

Pokud máte problém s použitím Azure Dev Spaces, vytvořte [problém v úložišti Azure dev Spaces GitHubu](https://github.com/Azure/dev-spaces/issues).

## <a name="before-you-begin"></a>Než začnete

Pokud chcete řešit problémy efektivněji, může vám pomoct vytvořit podrobnější protokoly pro kontrolu.

Pro rozšíření sady Visual Studio, nastavte `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` proměnné prostředí na hodnotu 1. Je potřeba restartovat Visual Studio pro proměnné prostředí se projeví. Po povolení se do adresáře `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` zapisují podrobné protokoly.

V rozhraní příkazového řádku, můžete pomocí výstupní informace během provádění příkazu `--verbose` přepnout. Můžete také procházet podrobnější protokoly v `%TEMP%\Azure Dev Spaces`. Na počítači Mac, můžete najít svého adresáře TEMP spuštěním `echo $TMPDIR` z okna terminálu. Na počítači s Linuxem adresář TEMP je obvykle `/tmp`.

Azure Dev Spaces také funguje nejlépe při ladění jedné instance nebo pod. `azds.yaml` soubor obsahuje nastavení *replicaCount*, které označuje počet lusků, které Kubernetes pro vaši službu spustí. Změníte-li *replicaCount* pro konfiguraci aplikace tak, aby spouštěla více lusků pro danou službu, ladicí program se připojí k prvnímu pod, pokud je uveden abecedně. Ladicí program se připojí k jinému pod při recyklování původní pod, což může vést k neočekávanému chování.

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Běžné problémy při povolování Azure Dev Spaces

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Chyba: nepovedlo se vytvořit kontroler Azure Dev Spaces.

Tato chyba může zobrazit, když dojde k chybě při vytváření kontroleru. Pokud se jedná o přechodnou chybu, odstraňte a znovu vytvořte kontroler, abyste ho opravili.

Můžete také zkusit odstranit kontroler:

```bash
azds remove -g <resource group name> -n <cluster name>
```

K odstranění kontroleru použijte Azure Dev Spaces CLI. Z aplikace Visual Studio není možné odstranit kontroler. Nemůžete také nainstalovat Azure Dev Spaces CLI do Azure Cloud Shell, takže nemůžete odstranit řadič z Azure Cloud Shell.

Pokud nemáte nainstalované rozhraní příkazového řádku Azure Dev Spaces, můžete ho nejdřív nainstalovat pomocí následujícího příkazu a pak odstranit kontroler:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

Opětovné vytvoření kontroleru můžete udělat v sadě Visual Studio nebo rozhraní příkazového řádku. Příklady najdete v tématu věnovaném [vývoji týmu](quickstart-team-development.md) nebo vývoji pomocí Průvodce rychlým startem [.NET Core](quickstart-netcore-visualstudio.md) .

### <a name="controller-create-failing-because-of-controller-name-length"></a>Řadič se nepodařilo vytvořit kvůli délce názvu kontroleru.

Název kontroleru Azure Dev Spaces nemůže být delší než 31 znaků. Pokud je název řadiče v clusteru AKS nebo vytvoření kontroleru delší než 31 znaků, dojde k chybě. Příklad:

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

Chcete-li tento problém vyřešit, vytvořte kontrolér s alternativním názvem. Příklad:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Povolení neúspěšných vývojových prostorů při přidání fondů uzlů Windows do clusteru AKS

V současné době je Azure Dev Spaces určen ke spouštění pouze v systémech Linux a pouze na uzlech. Pokud máte cluster AKS s fondem uzlů systému Windows, musíte zajistit, aby se Azure Dev Spaces lusky naplánovaly jenom na uzlech se systémem Linux. Pokud je naplánované spuštění Azure Dev Spaces pod uzlem v systému Windows, nebude možné začít a povolit vývojové prostory nebudou úspěšné.

Pokud chcete tento problém vyřešit, přidejte do clusteru AKSi [chuti](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) , abyste zajistili, že nebudete moct spouštět Linux lusky na uzlu Windows.

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Chyba: nenašly se žádné neobsahované uzly Linux ve stavu připraveno v clusteru. Aby bylo možné nasadit lusky v oboru názvů ' azds ', musí být v připraveném stavu aspoň jeden nev neaktivním uzlu Linux. "

Azure Dev Spaces se nepovedlo vytvořit kontrolér v clusteru AKS, protože se nepovedlo najít uzel, který není v *připraveném* stavu, aby bylo možné naplánovat lusky. Azure Dev Spaces vyžaduje aspoň jeden uzel Linux v *připraveném* stavu, který umožňuje plánování lusků bez určení tolerování.

Pokud chcete tento problém vyřešit, aktualizujte v clusteru AKS [konfiguraci vaší chuti](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) , abyste zajistili, že aspoň jeden uzel Linux umožňuje plánování lusků bez určení jejich tolerovánosti. Také se ujistěte, že alespoň jeden uzel pro Linux, který umožňuje plánování v luskech bez určení tolerování, je ve stavu *připraveno* . Pokud bude mít váš uzel dlouhou dobu, než se dorazí na stav *připraveno* , můžete zkusit restartovat uzel.

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Chyba "Azure Dev Spaces rozhraní příkazového řádku není správně nainstalováno" při spuštění `az aks use-dev-spaces`

Aktualizace rozhraní příkazového řádku Azure Dev Spaces změnila jeho cestu instalace. Pokud používáte verzi Azure CLI starší než 2.0.63, může se zobrazit tato chyba. K zobrazení vaší verze rozhraní příkazového řádku Azure použijte `az --version`.

```bash
$ az --version
azure-cli                         2.0.60 *
...
```

Navzdory chybové zprávě při spuštění `az aks use-dev-spaces` s verzí rozhraní příkazového řádku Azure CLI před 2.0.63 bude instalace úspěšná. `azds` můžete dál používat bez jakýchkoli problémů.

Pokud chcete tento problém vyřešit, aktualizujte instalaci [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) na 2.0.63 nebo novější. Tato aktualizace vyřeší chybovou zprávu, která se zobrazí při spuštění `az aks use-dev-spaces`. Případně můžete dál používat aktuální verzi rozhraní příkazového řádku Azure CLI a Azure Dev Spaces CLI.

### <a name="error-unable-to-reach-kube-apiserver"></a>Chyba "nepovedlo se kontaktovat Kube-apiserver".

Tato chyba se může zobrazit, když se Azure Dev Spaces nedokáže připojit k serveru rozhraní API clusteru AKS. 

Pokud je přístup k serveru API clusteru AKS uzamčený nebo pokud máte povolené [rozsahy IP adres serveru API](../aks/api-server-authorized-ip-ranges.md) pro váš cluster AKS, musíte taky [vytvořit](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled) nebo [aktualizovat](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges) cluster a [Povolit další rozsahy založené na vaší oblasti](https://github.com/Azure/dev-spaces/tree/master/public-ips).

Zajistěte, aby byl server rozhraní API dostupný spuštěním příkazů kubectl. Pokud server rozhraní API není k dispozici, obraťte se prosím na podporu AKS a zkuste to znovu, až Server API funguje.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Běžné problémy při přípravě projektu na Azure Dev Spaces

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Upozornění "souboru Dockerfile nebylo možné vygenerovat z důvodu nepodporovaného jazyka"
Azure Dev prostory poskytuje nativní podporu pro C# nebo Node.js. Když spustíte `azds prep` v adresáři s kódem napsaným v jednom z těchto jazyků, Azure Dev Spaces pro vás automaticky vytvoří odpovídající souboru Dockerfile.

Azure Dev Spaces můžete dál používat s kódem napsaným v jiných jazycích, ale před prvním spuštěním `azds up` musíte ručně vytvořit souboru Dockerfile.

Pokud je vaše aplikace napsána v jazyce, který Azure Dev Spaces netivně podporuje, je nutné poskytnout odpovídající souboru Dockerfile k vytvoření image kontejneru, ve které je spuštěn váš kód. Docker poskytuje [seznam osvědčených postupů pro psaní fázemi](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) a [souboru Dockerfile odkaz](https://docs.docker.com/engine/reference/builder/) , který vám může poznamenat psaní souboru Dockerfile, které vyhovuje vašim potřebám.

Jakmile budete mít vhodný souboru Dockerfile, spustíte `azds up` ke spuštění aplikace v Azure Dev Spaces.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Běžné problémy při spouštění nebo zastavování služeb pomocí Azure Dev Spaces

### <a name="error-config-file-not-found"></a>Chyba "konfigurační soubor nebyl nalezen:"

Při spuštění `azds up`se může zobrazit tato chyba. `azds up` i `azds prep` musí být spuštěny z kořenového adresáře projektu, který chcete spustit ve vývojovém prostoru.

Pokud chcete tento problém vyřešit:
1. Do kořenové složky, která obsahuje kód služby změňte aktuální adresář. 
1. Pokud ve složce kódu nemáte soubor _azds. yaml_ , spusťte `azds prep` pro vygenerování Docker, Kubernetes a Azure dev Spaces assetů.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Vypršel časový limit při čekání na sestavení image kontejneru... krok s virtuálními uzly AKS

K tomuto časovému limitu dojde, když se pokusíte použít vývojové prostory ke spuštění služby, která je nakonfigurovaná tak, aby běžela ve [virtuálním uzlu AKS](https://docs.microsoft.com/azure/aks/virtual-nodes-portal). Vývojové prostory v současné době nepodporují vytváření nebo ladění služeb na virtuálních uzlech.

Pokud spustíte `azds up` s přepínačem `--verbose` nebo povolíte podrobné protokolování v aplikaci Visual Studio, zobrazí se další podrobnosti:

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

Výše uvedený příkaz ukazuje, že je uzel Service přiřazený k *virtuálnímu uzlu-ACI-Linux*, který je virtuálním uzlem.

Pokud chcete tento problém vyřešit, aktualizujte graf Helm pro službu a odeberte všechny hodnoty *nodeSelector* nebo *tolerování* , které umožní službě běžet ve virtuálním uzlu. Tyto hodnoty jsou obvykle definovány v `values.yaml`m souboru grafu.

I nadále můžete používat cluster AKS s povolenou funkcí virtuálních uzlů, pokud služba, kterou chcete sestavit nebo ladit pomocí vývojových prostorů, běží na uzlu virtuálního počítače. Výchozí konfigurace je spuštění služby s vývojářskými prostory na uzlu virtuálního počítače.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Chyba: nepovedlo se najít připraveného pokladny pod při spouštění vývojových prostorů.

K této chybě dochází, pokud klient Helm může už sdělit pod Tiller spuštěné v clusteru.

Chcete-li tento problém vyřešit, restartujte uzly agenta v clusteru.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>Chyba "verze azds-\<Identifier\>-\<mezerník\>-\<ServiceName\> selhala: služby\<ServiceName\>již existuje nebo byl odepřen přístup k přístupu pro \<ServiceName\>, úložiště neexistuje nebo může vyžadovat příkaz Docker Login.

K těmto chybám může dojít v případě, že budete kombinovat spuštěné příkazy Direct Helm (například `helm install`, `helm upgrade`nebo `helm delete`) s příkazy pro vývoj prostorů (například `azds up` a `azds down`) ve stejném vývojovém prostoru. K nim dochází, protože vývojové prostory mají svou vlastní instanci pokladny, která je v konfliktu s vaší vlastní instancí pokladny spuštěnou ve stejném vývojovém prostoru.

Je možné použít příkazy Helm i příkazy pro vývoj v rámci stejného clusteru AKS, ale každý obor názvů s povoleným místem pro vývoj by měl použít buď jednu, nebo druhou.

Předpokládejme například, že použijete příkaz Helm ke spuštění celé aplikace v nadřazeném vývojovém prostoru. Můžete vytvořit podřízené vývojové prostory z této nadřazené položky, pomocí vývojových prostorů spouštět jednotlivé služby uvnitř podřízených vývojových prostorů a testovat služby společně. Až budete připraveni k vrácení změn se změnami, nasaďte aktualizovaný kód do nadřazeného vývojového prostoru pomocí příkazu Helm. Nepoužívejte `azds up` ke spuštění aktualizované služby v nadřazeném vývojovém prostoru, protože bude v konfliktu s počátečním spuštěním služby pomocí Helm.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>Stávající souboru Dockerfile se nepoužilo k sestavení kontejneru.

Azure Dev prostory můžete nakonfigurovat tak, aby odkazoval na konkrétní _soubor Dockerfile_ ve vašem projektu. Pokud se zobrazí Azure Dev Spaces nepoužívá _souboru Dockerfile_ , kterou očekáváte k sestavování kontejnerů, možná budete muset explicitně sdělit Azure dev Spaces které souboru Dockerfile se mají použít. 

Chcete-li tento problém vyřešit, otevřete soubor _azds. yaml_ , který Azure dev Spaces vygeneroval v projektu. *Konfigurace aktualizací: vývoj: sestavení: souboru Dockerfile* odkazuje na souboru Dockerfile, který chcete použít. Příklad:

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>Chyba "Neautorizováno: vyžaduje se ověřování" při pokusu o použití image Docker z privátního registru

Používáte image Docker z privátního registru, který vyžaduje ověření.

Pokud chcete tento problém vyřešit, můžete vývojářům dovolit, aby k ověřování a vyžádání imagí z tohoto privátního registru používali [imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets). Pokud chcete použít imagePullSecrets, vytvořte v oboru názvů, kde používáte image, [tajný klíč Kubernetes](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) . Pak zadejte tajný klíč jako imagePullSecret v `azds.yaml`.

Níže je uveden příklad zadání imagePullSecrets v `azds.yaml`.

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: $BUILD_CONTEXT$
  dockerfile: Dockerfile
install:
  chart: $CHART_DIR$
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    # Optional, specify an array of imagePullSecrets. These secrets must be manually created in the namespace.
    # This will override the imagePullSecrets array in values.yaml file.
    # If the dockerfile specifies any private registry, the imagePullSecret for the registry must be added here.
    # ref: https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod
    #
    # This uses credentials from secret "myRegistryKeySecretName".
    imagePullSecrets:
      - name: myRegistryKeySecretName
```

> [!IMPORTANT]
> Nastavení imagePullSecrets v `azds.yaml` přepíše imagePullSecrets zadané v `values.yaml`.

### <a name="error-service-cannot-be-started"></a>Chyba "službu nelze spustit."

Tato chyba může zobrazit, když kódu služby se nepodaří spustit. Příčinou je často v uživatelském kódu. Pokud chcete získat další diagnostické informace, při spouštění služby povolte podrobnější protokolování.

Z příkazového řádku použijte `--verbose` k povolení podrobnějšího protokolování. Výstupní formát můžete zadat také pomocí `--output`. Příklad:

```cmd
azds up --verbose --output json
```

V sadě Visual Studio:

1. Otevřít **nástroje > Možnosti** a v části **projekty a řešení**, zvolte **sestavíte a spustíte**.
2. Změnit nastavení pro **podrobnosti výstupu sestavení projektu nástroje MSBuild** k **podrobné** nebo **diagnostických**.

    ![Možnosti nástrojů – snímek obrazovky dialogového okna](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>Opětovné spuštění služby po opětovném vytvoření kontroleru

Po odebrání a opětovném vytvoření kontroleru Azure Dev Spaces přidruženého k tomuto clusteru se zobrazí chyba, že se *služba nemůže spustit* , když se pokusíte znovu spustit službu. V takové situaci obsahuje podrobný výstup následující text:

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

K této chybě dochází, protože odebráním kontroleru pro vývoj prostorů nedojde k odebrání služeb dříve nainstalovaných tímto kontrolérem. Opětovné vytvoření kontroleru a pak zkusíte ke spouštění služeb pomocí nového řadiče se nezdaří, protože staré služby jsou stále na místě.

Pokud chcete tento problém vyřešit, pomocí příkazu `kubectl delete` ručně odeberte staré služby z clusteru a pak znovu spusťte vývojové prostory a nainstalujte nové služby.

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>Chyba "službu nelze spustit." Při použití fázemi s více fázemi

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

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>Běžné problémy s používáním sady Visual Studio a Visual Studio Code s Azure Dev Spaces

### <a name="error-required-tools-and-configurations-are-missing"></a>Chyba: chybí požadované nástroje a konfigurace.

K této chybě může dojít při spuštění VS Code: "[Azure Dev prostory] nástroje a konfigurací pro sestavení a ladění. [název projektu]' chybí požadované."
Chyba znamená, že tento azds.exe není v proměnné prostředí PATH, jak je vidět ve VS Code.

Zkuste spustit VS Code z příkazového řádku, kde je správně nastavená proměnná prostředí PATH.

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Chyba: požadované nástroje pro sestavení a ladění ProjectName jsou zastaralé.

Tato chyba se zobrazí v Visual Studio Code, pokud máte novější verzi rozšíření VS Code pro Azure Dev Spaces, ale starší verzi Azure Dev Spaces CLI.

Zkuste stáhnout a nainstalovat nejnovější verzi rozhraní příkazového řádku Azure Dev Spaces:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>Chyba: nepovedlo se najít rozšíření ladicího programu pro typ: CoreCLR.

Tato chyba se může zobrazit při spuštění ladicího programu Visual Studio Code. Ve vývojovém počítači možná nemáte C# nainstalované rozšíření vs Code. Toto C# rozšíření zahrnuje podporu ladění pro .NET Core (CoreCLR).

Chcete-li tento problém vyřešit, nainstalujte [rozšíření vs Code C#pro ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>Chyba "nakonfigurovaný typ ladění" CoreCLR "není podporován"

Tato chyba se může zobrazit při spuštění ladicího programu Visual Studio Code. Možná nemáte rozšíření VS Code pro Azure Dev Spaces ve vývojovém počítači nainstalované.

Chcete-li tento problém vyřešit, nainstalujte [rozšíření vs Code pro Azure dev Spaces](get-started-netcore.md).

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Chyba "neplatná" hodnota "CWD"/src ". Systém nemůže najít zadaný soubor." nebo "spuštění: program '/ src / [cesta k projektu binární]' neexistuje."

Tato chyba se může zobrazit při spuštění ladicího programu Visual Studio Code. Ve výchozím nastavení, používá rozšíření VS Codu `src` jako pracovní adresář pro projekt v kontejneru. Pokud jste aktualizovali vaši `Dockerfile` Pokud chcete zadat jinou pracovní adresář, může se zobrazit tato chyba.

Chcete-li tento problém vyřešit, aktualizujte soubor `launch.json` v podadresáři `.vscode` složky projektu. Změnit `configurations->cwd` směrnice tak, aby odkazoval do stejného adresáře jako `WORKDIR` definované ve vašem projektu `Dockerfile`. Budete muset taky aktualizovat `configurations->program` také směrnice.

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Chyba "program kanálu ' azds ' se neočekávaně ukončil s kódem 126."

Tato chyba se může zobrazit při spuštění ladicího programu Visual Studio Code.

Chcete-li tento problém vyřešit, zavřete a znovu otevřete Visual Studio Code. Restartujte ladicí program.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Chyba "vnitřní kukátko se nezdařilo: Sledujte ENOSPC" při připojování ladění k aplikaci Node. js

K této chybě dochází, pokud uzel, na kterém je spuštěný, s aplikací Node. js, ke které se pokoušíte připojit pomocí ladicího programu, překročil hodnotu *FS. inotify. max_user_watches* . V některých případech [může být výchozí hodnota *FS. inotify. max_user_watches* příliš malá, aby bylo možné manipulovat s připojením ladicího programu přímo k poli pod](https://github.com/Azure/AKS/issues/772).

Dočasným řešením tohoto problému je zvýšit hodnotu *FS. inotify. max_user_watches* v každém uzlu v clusteru a restartovat tento uzel, aby se změny projevily.

## <a name="other-common-issues"></a>Další běžné problémy

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Chyba "azds" není rozpoznána jako interní nebo externí příkaz, spustitelný program nebo dávkový soubor.

K této chybě může dojít, pokud `azds.exe` není správně nainstalován nebo nakonfigurován.

Pokud chcete tento problém vyřešit:

1. Pro `azds.exe`se podívejte na umístění% ProgramFiles%/Microsoft SDKs\Azure\Azure dev Spaces CLI. Pokud existuje, přidejte do proměnné prostředí PATH v tomto umístění.
2. Pokud `azds.exe` není nainstalován, spusťte následující příkaz:

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>Chyba autorizace "Microsoft. DevSpaces/Register/Action"

Ke správě Azure Dev Spaces potřebujete ve svém předplatném Azure přístup *vlastníka* nebo *přispěvatele* . Pokud se pokoušíte spravovat vývojové prostory a nemáte oprávnění *vlastníka* nebo *přispěvatele* k přidruženému předplatnému Azure, může se zobrazit chyba autorizace. Příklad:

```console
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

Pokud chcete tento problém vyřešit, pomocí účtu s přístupem *vlastníka* nebo *přispěvatele* k předplatnému Azure ručně zaregistrujte `Microsoft.DevSpaces` obor názvů:

```console
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>Nová Luska se nespouští.

Inicializátor Kubernetes nemůže použít PodSpec pro nové lusky z důvodu změn oprávnění RBAC do role *Správce clusteru* v clusteru. Nový objekt pod může mít také neplatnou PodSpec, například účet služby spojený s objektem pod již neexistuje. Chcete-li zobrazit lusky, které jsou ve stavu *čekání* v důsledku problému s inicializátorem, použijte příkaz `kubectl get pods`:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Tento problém může mít vliv na lusky ve *všech oborech názvů* v clusteru včetně oborů názvů, kde není povolený Azure dev Spaces.

Pokud chcete tento problém vyřešit, [aktualizujte vývojové prostory CLI na nejnovější verzi](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) a pak odstraňte *azds InitializerConfiguration* z kontroleru Azure dev Spaces:

```bash
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl delete InitializerConfiguration azds
```

Po odebrání *Azds InitializerConfiguration* z kontroleru Azure Dev Spaces pomocí `kubectl delete` odebrat jakékoli lusky ve stavu *čekání na vyřízení* . Po odebrání všech probíhajících lusků znovu nasaďte své lusky.

Pokud se nové lusky po opětovném nasazení stále zablokují ve stavu *čekání* , použijte `kubectl delete` k odebrání všech lusků ve stavu *čekání na vyřízení* . Po odebrání všech probíhajících lusků odstraňte řadič z clusteru a znovu ho nainstalujte:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Po opětovné instalaci kontroleru znovu nasaďte své lusky.

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Nesprávná oprávnění RBAC pro volání řadiče pro vývoj prostorů a rozhraní API

Uživatel, který přistupuje k řadiči Azure Dev Spaces, musí mít přístup, aby mohl číst *kubeconfig* správce v clusteru AKS. Toto oprávnění je například k dispozici v [předdefinované roli Správce clusteru služby Azure Kubernetes](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions). Uživatel, který přistupuje k řadiči Azure Dev Spaces, musí mít také roli *přispěvatele* nebo *vlastníka* RBAC pro kontroler. Další podrobnosti o aktualizaci oprávnění uživatele pro cluster AKS jsou k dispozici [zde](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group).

Aktualizace role uživatele RBAC pro kontroler:

1. Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.
1. Přejděte do skupiny prostředků obsahující kontroler, který je obvykle stejný jako cluster AKS.
1. Zaškrtněte políčko *Zobrazit skryté typy* .
1. Klikněte na kontroler.
1. Otevřete podokno *Access Control (IAM)* .
1. Klikněte na kartu *přiřazení rolí* .
1. Klikněte na *Přidat* a pak na *Přidat přiřazení role*.
    * V případě *role*vyberte možnost *Přispěvatel* nebo *vlastník*.
    * V případě *přiřazení přístupu k*vyberte možnost *uživatel, skupina nebo instanční objekt služby Azure AD*.
    * V části *Vybrat*vyhledejte uživatele, kterému chcete udělit oprávnění.
1. Klikněte na *Uložit*.

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Překlad názvů DNS pro veřejnou adresu URL související se službou Dev prostory nezdaří

Můžete nakonfigurovat koncový bod veřejné adresy URL pro vaši službu zadáním `--public` přepínačem na příkaz `azds prep`, nebo zaškrtnutím políčka `Publicly Accessible` v aplikaci Visual Studio. Veřejný název DNS se automaticky zaregistruje při spuštění služby ve vývojových prostorech. Pokud tento název DNS není zaregistrovaný, zobrazí se ve webovém prohlížeči při připojování k veřejné adrese URL *Stránka nemůžete zobrazit* nebo *se k webu nedá dostat* chyba.

Pokud chcete tento problém vyřešit:

* Ověřte stav všech adres URL přidružených k vašim službám dev Spaces:

  ```console
  azds list-uris
  ```

* Pokud je adresa URL ve stavu *čekání na vyřízení* , vývojové prostory stále čekají na dokončení registrace DNS. V některých případech trvá několik minut, než registraci dokončit. Vývoj prostory otevře také tunel localhost pro každou službu, kterou můžete použít při čekání na registraci DNS.
* Pokud adresa URL zůstane v *nevyřízeném* stavu po dobu delší než 5 minut, může to znamenat problém s externím DNS pod tím, že vytvoří veřejný koncový bod nebo Nginx příchozí řadič domény pod tím, který získá veřejný koncový bod. Pomocí následujících příkazů odstraňte tyto lusky a umožněte AKS jejich automatickému opětovnému vytvoření:
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Chyba "Chyba nadřazeného připojení nebo odpojení/resetování před záhlavími"

Při pokusu o přístup ke službě, může se zobrazit tato chyba. Například když přejdete na adresu URL služby v prohlížeči. Tato chyba znamená, že port kontejneru není k dispozici. To může být z následujících důvodů:

* Kontejner se stále ještě probíhá sestavíte a nasadíte. Tento problém může nastat, pokud spustíte `azds up` nebo spuštění ladicího programu a pak zkuste přístup ke kontejneru předtím, než byl úspěšně nasazen.
* Konfigurace portu není konzistentní napříč vaší _soubor Dockerfile_, diagram helmu a libovolný kód serveru, které se otevře port.

Pokud chcete tento problém vyřešit:

1. Jestli je kontejner právě vytvořená/nasazuje, můžete počkejte 2-3 sekund a zkuste to znovu přístupu ke službě. 
1. Zkontrolujte konfiguraci portů. Zadaná čísla portů by měla být **shodná** se všemi těmito prostředky:
    * **Soubor Dockerfile:** určené `EXPOSE` instrukce.
    * **[Diagram helmu](https://docs.helm.sh):** určené `externalPort` a `internalPort` hodnoty pro službu (nacházejí se často ve `values.yml` souboru),
    * Žádné porty se otevřely v kódu aplikace, například v Node.js: `var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>Název typu nebo oboru názvů "MyLibrary" se nenašel.

Projekt knihovny, který používáte, se nenašel. Pomocí vývojových prostorů je kontext buildu ve výchozím nastavení na úrovni projektu nebo služby.  

Pokud chcete tento problém vyřešit:

1. Upravte soubor `azds.yaml` pro nastavení kontextu sestavení na úroveň řešení.
2. Upravte soubory `Dockerfile` a `Dockerfile.develop` tak, aby odkazovaly na soubory projektu, například `.csproj`správně vzhledem k novému kontextu sestavení.
3. Přidejte `.dockerignore` do stejného adresáře jako `.sln` soubor.
4. V případě potřeby aktualizujte `.dockerignore` s dalšími položkami.

Příklad najdete [tady](https://github.com/sgreenmsft/buildcontextsample).

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>Automatické škálování pod automatickým škálováním nefunguje ve vývojovém prostoru

Když službu spustíte ve vývojovém prostoru, je tato služba [vložená s dalšími kontejnery pro instrumentaci](how-dev-spaces-works.md#prepare-your-aks-cluster) a všechny kontejnery v rámci musí mít omezení prostředků a požadavky nastavené na automatické škálování vodorovně pod.

Chcete-li tento problém vyřešit, použijte požadavek prostředku a omezte na vložené kontejnery pro vývoj prostorů. Požadavky na prostředky a omezení lze použít pro vložený kontejner (devspaces-proxy) přidáním anotace `azds.io/proxy-resources` do specifikace pod. Hodnota by měla být nastavena na objekt JSON, který představuje oddíl prostředků specifikace kontejneru pro proxy server.

Níže je uveden příklad anotace prostředků proxy, která se má použít pro specifikaci pod.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>Povolit Azure Dev Spaces v existujícím oboru názvů s běžícími lusky

Je možné, že máte existující cluster AKS a obor názvů se spuštěnými lusky, kde chcete povolit Azure Dev Spaces.

Pokud chcete povolit Azure Dev Spaces v existujícím oboru názvů v clusteru AKS, spusťte `use-dev-spaces` a pomocí `kubectl` restartujte všechny lusky v daném oboru názvů.

```console
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
kubectl -n my-namespace delete pod --all
```

Po restartování lusků můžete začít používat stávající obor názvů s Azure Dev Spaces.