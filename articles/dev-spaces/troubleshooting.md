---
title: Řešení potíží
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: troubleshooting
description: Zjistěte, jak řešit a řešit běžné problémy při povolení a používání Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery, Helm, síť služeb, směrování sítě služeb, kubectl, k8s '
ms.openlocfilehash: c12dfd385962d8dd7de8239a0d4ecd46746499c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239771"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Řešení potíží s Azure Dev Spaces

Tato příručka obsahuje informace o běžných problémech, které můžete mít při používání Azure Dev Spaces.

Pokud máte problém s používáním Azure Dev Spaces, vytvořte [problém v úložišti GitHub Azure Dev Spaces](https://github.com/Azure/dev-spaces/issues).

## <a name="before-you-begin"></a>Než začnete

Chcete-li řešit problémy efektivněji, může pomoci vytvořit podrobnější protokoly pro kontrolu.

Pro rozšíření Visual Studio `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` nastavte proměnnou prostředí na 1. Nezapomeňte restartovat Visual Studio pro proměnnou prostředí se projeví. Po povolení jsou do adresáře `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` zapsány podrobné protokoly.

V příkazovém příkazu k příkazu můžete `--verbose` výstup více informací během spuštění příkazu pomocí přepínače. Můžete také procházet podrobnější `%TEMP%\Azure Dev Spaces`protokoly v . Na Macu lze adresář *TEMP* nalézt `echo $TMPDIR` spuštěním z okna terminálu. Na počítači s *TEMP* Linuxem `/tmp`je adresář TEMP obvykle . Kromě toho ověřte, že protokolování je povoleno v [konfiguračním souboru Azure CLI](/cli/azure/azure-cli-configuration?view=azure-cli-latest#cli-configuration-values-and-environment-variables).

Azure Dev Spaces funguje také nejlépe při ladění jedné instance nebo pod. Soubor `azds.yaml` obsahuje *nastavení, replicaCount*, který označuje počet podů, které Kubernetes běží pro vaši službu. Pokud změníte *replicaCount* nakonfigurovat aplikaci pro spuštění více podů pro danou službu, ladicí program připojí k první pod, pokud jsou uvedeny abecedně. Ladicí program se připojí k jinému podu při recyklaci původnípod, což může mít za následek neočekávané chování.

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Běžné problémy při povolení Azure Dev Spaces

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Chyba "Vytvoření řadiče Azure Dev Spaces se nezdařilo"

Tato chyba se může zobrazit, když se něco pokazí při vytváření řadiče. Pokud se jedná o přechodnou chybu, odstraňte a znovu vytvořte ovladač, abyste ho opravili.

Můžete také zkusit odstranění ovladače:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Pomocí příkazového příkazu k odstranění řadiče pomocí příkazového příkazu Azure Dev Spaces. Není možné odstranit řadič z visual studia. V prostředí Azure Cloud Shell taky nemůžete nainstalovat příkaz cli Azure Dev Spaces, takže nemůžete odstranit řadič z Prostředí Azure Cloud Shell.

Pokud nemáte nainstalovaný Azure Dev Spaces CLI nainstalován, můžete nejprve nainstalovat pomocí následujícího příkazu a potom odstranit řadič:

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

Opětovné vytvoření řadiče lze provést z cli nebo Visual Studio. Podívejte se na [vývoj týmu](quickstart-team-development.md) nebo vývoj [s .NET Core](quickstart-netcore-visualstudio.md) rychlé starty pro příklady.

### <a name="controller-create-failing-because-of-controller-name-length"></a>Vytvoření řadiče se lhací z důvodu délky názvu řadiče

Název řadiče Azure Dev Spaces nemůže být delší než 31 znaků. Pokud název ovladače překročí 31 znaků při povolení funkce Dev Spaces v clusteru AKS nebo při vytvoření řadiče, zobrazí se chyba. Například:

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

Chcete-li tento problém vyřešit, vytvořte řadič s alternativním názvem. Například:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Povolení funkce Dev Spaces se lháde, pokud jsou do clusteru AKS přidány fondy uzlů systému Windows

V současné době Azure Dev Spaces je určen pro spuštění pouze na linuxových podech a uzlech. Pokud máte cluster AKS s fondem uzlů Windows, musíte zajistit, že pody Azure Dev Spaces jsou naplánované jenom v linuxových uzlech. Pokud je naplánováno spuštění podu Azure Dev Spaces v uzlu Windows, tento pod se nespustí a povolení funkce Dev Spaces se nezdaří.

Chcete-li tento problém [vyřešit, přidejte do clusteru](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) AKS počin, abyste zajistili, že pody Linuxu nebudou naplánované ke spuštění v uzlu Windows.

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Chyba "Nebyly nalezeny žádné neposkvrněné uzly Linuxu ve stavu Připraveno v clusteru. Musí existovat alespoň jeden neposkvrněný uzel Linuxu ve stavu Připraven o nasazení podů v oboru názvů AZDs."

Azure Dev Spaces nemohl vytvořit řadič v clusteru AKS, protože nemohl najít neposkvrněný uzel ve stavu *Připraveno* naplánovat pody. Azure Dev Spaces vyžaduje alespoň jeden uzel Linuxu ve stavu *Připraveno,* který umožňuje plánování podů bez zadání tolerací.

Chcete-li tento problém [vyřešit, aktualizujte konfiguraci počinvu](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) v clusteru AKS, abyste zajistili, že alespoň jeden uzel Linuxu umožňuje plánování podů bez určení tolerací. Také ujistěte se, že alespoň jeden uzel Linux, který umožňuje plánování podů bez zadání tolerace je ve stavu *Připraveno.* Pokud váš uzel trvá dlouhou dobu k dosažení *stavu Připraven,* můžete zkusit restartování uzlu.

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Chyba "Azure Dev Spaces CLI není správně nainstalován" při spuštění az aks use-dev-spaces

Aktualizace příkazového příkazu Azure Dev Spaces změnila svou instalační cestu. Pokud používáte verzi rozhraní příkazového příkazu Azure starší než 2.0.63, může se zobrazit tato chyba. Chcete-li zobrazit verzi příkazového `az --version`příkazu k příkazu Azure, použijte .

```azurecli
az --version
```

```output
azure-cli                         2.0.60 *
...
```

I přes chybovou `az aks use-dev-spaces` zprávu při spuštění s verzí rozhraní příkazového příkazu Azure před 2.0.63, instalace se podaří. Můžete pokračovat v `azds` používání bez problémů.

Chcete-li tento problém vyřešit, aktualizujte instalaci [příkazového příkazu Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) na 2.0.63 nebo novější. Tato aktualizace vyřeší chybovou zprávu, `az aks use-dev-spaces`která se zobrazí při spuštění programu . Případně můžete nadále používat aktuální verzi příkazového příkazového příkazu Azure a azure dev spaces CLI.

### <a name="error-unable-to-reach-kube-apiserver"></a>Chyba "Nelze dosáhnout kube-apiserver"

Tato chyba se může zobrazit, když se Azure Dev Spaces nemůže připojit k serveru API clusteru AKS. 

Pokud je přístup k serveru rozhraní API clusteru AKS uzamčen nebo pokud máte pro váš cluster AKS [povoleny rozsahy IP adres autorizovaného serveru API,](../aks/api-server-authorized-ip-ranges.md) musíte také [vytvořit](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled) nebo [aktualizovat](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges) cluster, aby [bylo možné povolit další rozsahy na základě vaší oblasti](https://github.com/Azure/dev-spaces/tree/master/public-ips).

Ujistěte se, že server API je k dispozici spuštěním příkazů kubectl. Pokud server rozhraní API není k dispozici, obraťte se na podporu AKS a opakujte akci, když server rozhraní API funguje.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Běžné problémy při přípravě projektu pro Azure Dev Spaces

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Upozornění "Soubor Dockerfile nelze vygenerovat z důvodu nepodporovaného jazyka"
Azure Dev Spaces poskytuje nativní podporu pro C# a Node.js. Když spustíte `azds prep` v adresáři s kódem napsaným v jednom z těchto jazyků, Azure Dev Spaces automaticky vytvoří příslušný dockerfile pro vás.

Stále můžete použít Azure Dev Spaces s kódem napsaným v jiných jazycích, ale před prvním spuštěním `azds up` je potřeba ručně vytvořit soubor Dockerfile.

Pokud je vaše aplikace napsaná v jazyce, který Azure Dev Spaces nativně nepodporuje, musíte poskytnout příslušný soubor Dockerfile k vytvoření image kontejneru se spuštěným kódem. Docker poskytuje [seznam osvědčených postupů pro psaní Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) a [dockerfile odkaz,](https://docs.docker.com/engine/reference/builder/) který vám pomůže napsat Dockerfile, který vyhovuje vašim potřebám.

Jakmile budete mít odpovídající Dockerfile na `azds up` místě, spustíte spuštění aplikace v Azure Dev Spaces.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Běžné problémy při spouštění nebo zastavování služeb pomocí Azure Dev Spaces

### <a name="error-config-file-not-found"></a>Chyba "Konfigurační soubor nebyl nalezen:"

Při `azds up`spuštění se může zobrazit tato chyba. Oba `azds up` `azds prep` a musí být spuštěnz kořenového adresáře projektu, který chcete spustit v prostoru pro spuštění.

Pokud chcete tento problém vyřešit:
1. Změňte aktuální adresář do kořenové složky obsahující kód služby. 
1. Pokud nemáte soubor _azds.yaml_ ve složce kódu, `azds prep` spusťte generování prostředků Docker, Kubernetes a Azure Dev Spaces.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Timeout ve společnosti "Waiting for container image build..." krok s virtuálními uzly AKS

K tomuto časovému udůsledku dochází při pokusu o spuštění služby, která je nakonfigurována pro spuštění na [virtuálním uzlu AKS](https://docs.microsoft.com/azure/aks/virtual-nodes-portal), pomocí funkce Dev Spaces. Dev Spaces aktuálně nepodporuje vytváření nebo ladění služeb na virtuálních uzlech.

Pokud spustíte `azds up` `--verbose` s přepínačem nebo povolíte podrobné protokolování v sadě Visual Studio, zobrazí se další podrobnosti:

```cmd
azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

Výše uvedený příkaz ukazuje, že pod služby byl přiřazen k *virtual-node-aci-linux*, což je virtuální uzel.

Chcete-li tento problém vyřešit, aktualizujte graf Helm pro službu odebrat všechny *hodnoty nodeSelector* nebo *tolerations,* které umožňují spuštění služby na virtuálním uzlu. Tyto hodnoty jsou obvykle definovány `values.yaml` v souboru grafu.

Stále můžete použít cluster AKS, který má povolenou funkci virtuálních uzlů, pokud služba, kterou chcete vytvořit nebo ladit pomocí funkce Dev Spaces, běží na uzlu virtuálního počítače. Spuštění služby s dev spaces na uzlu virtuálního počítače je výchozí konfigurace.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Chyba "nelze najít připravený modul pro obklad" při spouštění aplikací Dev Spaces

K této chybě dochází, pokud klient Helm již nemůže mluvit s podem Tiller spuštěné v clusteru.

Chcete-li tento problém vyřešit, restartujte uzly agenta v clusteru.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>Chyba "Release AZDs-\<identifier\>-\<spacename\>-\<servicename\> failed: services '\<servicename\>' \<již\>existuje" nebo "Pull access denied for servicename , repository neexistuje nebo může vyžadovat přihlášení dockeru".

K těmto chybám může dojít, pokud smícháte spuštěné příkazy přímého helmu (například `helm install` `helm upgrade`, nebo `helm delete`) s příkazy Dev Spaces (například `azds up` a `azds down`) uvnitř stejného prostoru pro spuštění. Dochází k nim, protože Dev Spaces má vlastní instanci Tiller, která je v konfliktu s vaší vlastní instancí Tiller spuštěnou ve stejném prostoru pro spuštění.

Je v pořádku použít příkazy Helm u stejného clusteru AKS, ale každý obor názvů s povoleným funkcemi Dev Spaces by měl používat jeden nebo druhý.

Předpokládejme například, že používáte příkaz Helm ke spuštění celé aplikace v nadřazeném dev prostoru. Můžete vytvořit podřízené dev mezery mimo tento nadřazený, pomocí funkce Dev Spaces spouštět jednotlivé služby uvnitř podřízených dev prostorů a společně testovat služby. Až budete připraveni změny odevzdátu, použijte příkaz Helm k nasazení aktualizovaného kódu do nadřazeného dev prostoru. Nepoužívejte `azds up` ke spuštění aktualizované služby v nadřazeném dev prostoru, protože bude v konfliktu se službou, která byla původně spuštěna pomocí helmu.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>Existující dockerfile, který se nepoužívá k vytvoření kontejneru

Azure Dev Spaces můžete nakonfigurovat tak, aby ukazoval na konkrétní _dockerfile_ ve vašem projektu. Pokud se zdá, Azure Dev Spaces nepoužívá _Dockerfile,_ který očekáváte k sestavení kontejnerů, možná budete muset explicitně sdělit Azure Dev Spaces, který Dockerfile použít. 

Chcete-li tento problém vyřešit, otevřete soubor _azds.yaml,_ který Azure Dev Spaces vygeneroval ve vašem projektu. *Aktualizace konfigurace: develop: build: dockerfile* přejděte na Dockerfile, který chcete použít. Například:

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>Při pokusu o použití bitové kopie Dockeru z privátního registru došlo k chybě "neautorizováno: je vyžadováno ověření".

Používáte image Dockeru ze soukromého registru, který vyžaduje ověření.

Chcete-li tento problém vyřešit, můžete povolit Dev Spaces k ověření a vyžádat bitové kopie z tohoto soukromého registru pomocí [imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets). Chcete-li použít imagePullSecrets, [vytvořte tajný klíč Kubernetes](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) v oboru názvů, kde používáte obrázek. Pak zadejte tajný klíč jako `azds.yaml`imagePullSecret v .

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
> Nastavení imagePullSecrets `azds.yaml` in přepíše imagePullSecrets `values.yaml`zadané v .

### <a name="error-service-cannot-be-started"></a>Chyba "Službu nelze spustit"

Tato chyba se může zobrazit, pokud se nespustí kód služby. Příčina je často v uživatelském kódu. Chcete-li získat další diagnostické informace, povolte podrobnější protokolování při spuštění služby.

Z příkazového řádku `--verbose` povolte podrobnější protokolování pomocí příkazu. Můžete také určit výstupní `--output`formát pomocí aplikace . Například:

```cmd
azds up --verbose --output json
```

V sadě Visual Studio:

1. Otevřete **nástroje > možnosti** a v části **Projekty a řešení**zvolte Sestavit a **spustit**.
2. Změňte nastavení **pro msbuild projektu vytváření výstup podrobností** **podrobné** nebo **diagnostické**.

    ![Snímek obrazovky s dialogem Možnosti nástrojů](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>Opětovné spuštění služby po opětovném vytvoření řadiče

Zobrazí *se služba nelze spustit* chybu při pokusu o opětovné spuštění služby po odebrání a znovu znovu řadič Azure Dev Spaces přidružené k tomuto clusteru. V takovém případě podrobný výstup obsahuje následující text:

```output
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

K této chybě dochází, protože odebrání řadiče Dev Spaces neodebere služby dříve nainstalované tímto řadičem. Opětovné vytvoření řadiče a pokus o spuštění služeb pomocí nového řadiče se nezdaří, protože staré služby jsou stále na místě.

Chcete-li tento problém `kubectl delete` vyřešit, pomocí příkazu ručně odeberte staré služby z clusteru a potom znovu spusťte funkce Dev Spaces a nainstalujte nové služby.

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>Chyba "Službu nelze spustit" při použití vícestupňových dockerových souborů

Při použití vícestupňového souboru Dockerfile se zobrazí chyba *služby.* V takovém případě podrobný výstup obsahuje následující text:

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

K této chybě dochází, protože Azure Dev Spaces aktuálně nepodporuje vícefázové sestavení. Chcete-li se vyhnout vícestupňové sestavení, přepište dockerfile.

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>Síťový provoz není při připojování vývojového počítače předáván do clusteru AKS

Při použití [Azure Dev Spaces k připojení clusteru AKS k vývojovému počítači](how-to/connect.md)se může vyskytnout problém, kdy není síťový provoz přepojen mezi vývojovým počítačem a clusterem AKS.

Při připojování vývojového počítače ke clusteru AKS předává Azure Dev Spaces síťový provoz mezi clusterem `hosts` AKS a vývojovým počítačem úpravou souboru vývojového počítače. Azure Dev Spaces vytvoří `hosts` položku s adresou služby Kubernetes, kterou nahrazujete jako název hostitele. Tato položka se používá s přesměrováním portů pro přímé síťové přenosy mezi vývojovým počítačem a clusterem AKS. Pokud je služba na vývojovém počítači v konfliktu s portem služby Kubernetes, kterou nahrazujete, azure dev spaces nemůže předávat síťový provoz pro službu Kubernetes. Například služba *Windows BranchCache* je obvykle vázána na *0.0.0.0:80*, což konflikty způsobí konflikt pro port 80 na všech místních IP serverech.

Chcete-li tento problém vyřešit, je třeba zastavit všechny služby nebo procesy, které jsou v konfliktu s portem služby Kubernetes, kterou se pokoušíte nahradit. Pomocí nástrojů, například *netstat*, můžete zkontrolovat, jaké služby nebo procesy ve vývojovém počítači jsou v konfliktu.

Chcete-li například zastavit a zakázat službu *Windows BranchCache:*
* Spusťte `services.msc` z příkazového řádku.
* Klikněte pravým tlačítkem myši na *Položku BranchCache* a vyberte příkaz *Vlastnosti*.
* Klepněte na tlačítko *Zastavit*.
* Volitelně jej můžete zakázat nastavením *typu Spuštění* na *Zakázat*.
* Klikněte na tlačítko *OK*.

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>Běžné problémy s použitím visual studia a kódu Sady Visual Studio s Azure Dev Spaces

### <a name="error-required-tools-and-configurations-are-missing"></a>Chyba "Chybí požadované nástroje a konfigurace"

K této chybě může dojít při spouštění kódu VS: "[Azure Dev Spaces] Požadované nástroje a konfigurace pro sestavení a ladění [název projektu]" chybí."
Chyba znamená, že azds.exe není v proměnné prostředí PATH, jak je vidět v VS Code.

Zkuste spustit Kód VS z příkazového řádku, kde je správně nastavena proměnná prostředí PATH.

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Chyba "Požadované nástroje pro sestavení a ladění názvu projektu jsou zastaralé."

Tato chyba se zobrazí v kódu sady Visual Studio, pokud máte novější verzi rozšíření VS Code pro Azure Dev Spaces, ale starší verze rozhraní příkazového příkazu Azure Dev Spaces.

Zkuste stáhnout a nainstalovat nejnovější verzi rozhraní příkazu Azure Dev Spaces:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>Chyba: "Nepodařilo se najít rozšíření ladicího programu pro type:coreclr"

Tato chyba se může zobrazit při spuštění ladicího programu kódu sady Visual Studio. Pravděpodobně nemáte na vývojovém počítači nainstalované rozšíření VS Code pro C#. Rozšíření Jazyka C# zahrnuje podporu ladění pro .NET Core (CoreCLR).

Chcete-li tento problém vyřešit, nainstalujte [rozšíření kódu VS pro C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>Chyba "Nakonfigurovaný typ ladění coreclr není podporován"

Tato chyba se může zobrazit při spuštění ladicího programu kódu sady Visual Studio. Možná nemáte na vývojovém počítači nainstalované rozšíření VS Code pro Azure Dev Spaces.

Chcete-li tento problém vyřešit, nainstalujte [rozšíření VS Code pro Azure Dev Spaces](get-started-netcore.md).

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Chyba "Neplatná hodnota cwd'/src". Systém nemůže najít zadaný soubor." nebo "launch: program '/src/[cesta k binárnímu projektu]" neexistuje"

Tato chyba se může zobrazit při spuštění ladicího programu kódu sady Visual Studio. Ve výchozím nastavení používá `src` rozšíření VS Code jako pracovní adresář pro projekt v kontejneru. Pokud jste aktualizovali `Dockerfile` svůj adresář, může se zobrazit tato chyba.

Chcete-li tento problém `launch.json` vyřešit, aktualizujte soubor pod `.vscode` podadresářem složky projektu. Změňte `configurations->cwd` direktivu tak, `WORKDIR` aby přecvakla na stejný adresář, jak je definován v projektu `Dockerfile`. Může být také nutné `configurations->program` aktualizovat směrnice také.

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Chyba "Program kanálu 'azds' neočekávaně ukončen s kódem 126."

Tato chyba se může zobrazit při spuštění ladicího programu kódu sady Visual Studio.

Chcete-li tento problém vyřešit, zavřete a znovu otevřete kód sady Visual Studio. Restartujte ladicí program.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Chyba "Interní sledování se nezdařilo: sledování ENOSPC" při připojování ladění k aplikaci Node.js

K této chybě dochází, když uzel s podem s aplikací Node.js, ke které se pokoušíte připojit pomocí ladicího programu, překročil hodnotu *fs.inotify.max_user_watches.* V některých případech [výchozí hodnota *fs.inotify.max_user_watches* může být příliš malý pro zpracování ladicí program přímo k pod](https://github.com/Azure/AKS/issues/772).

Dočasné řešení tohoto problému je zvýšit hodnotu *fs.inotify.max_user_watches* na každém uzlu v clusteru a restartujte tento uzel pro změny se projeví.

## <a name="other-common-issues"></a>Další běžné problémy

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Chyba "azds" není rozpoznán jako interní nebo externí příkaz, funkční program nebo dávkový soubor

K této chybě `azds.exe` může dojít, pokud není nainstalován nebo nakonfigurován správně.

Pokud chcete tento problém vyřešit:

1. Zkontrolujte umístění %ProgramFiles%/Microsoft SDKs\Azure\Azure Dev `azds.exe`Spaces CLI pro . Pokud je tam, přidejte toto umístění do proměnné prostředí PATH.
2. Pokud `azds.exe` není nainstalován, spusťte následující příkaz:

    ```azurecli
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>Chyba autorizace "Microsoft.DevSpaces/register/action"

Ke správě Azure Dev Spaces potřebujete přístup *vlastníka* nebo *přispěvatele* ve vašem předplatném Azure. Pokud se pokoušíte spravovat Dev Spaces a nemáte přístup *vlastníka* nebo *přispěvatele* k přidruženému předplatnému Azure, může se zobrazit chyba autorizace. Například:

```output
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

Chcete-li tento problém vyřešit, pomocí účtu s *přístupem vlastníka* nebo *přispěvatele* k předplatnému Azure, ručně zaregistrujte obor `Microsoft.DevSpaces` názvů:

```azurecli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>Nové pody se nespouštějí

Inicializátor Kubernetes nelze použít PodSpec pro nové pody z důvodu změny oprávnění RBAC role *clusteru správce* v clusteru. Nový pod může mít také neplatný PodSpec, například účet služby přidružené pod již neexistuje. Chcete-li zobrazit pody, které jsou ve stavu Čeká `kubectl get pods` na *vyřízení* z důvodu problému inicializátoru, použijte příkaz:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Tento problém může ovlivnit pody ve *všech oborech názvů* v clusteru, včetně oborů názvů, kde Azure Dev Spaces není povolena.

Chcete-li tento problém vyřešit, [aktualizujte příkaz cli dev spaces na nejnovější verzi](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) a pak odstraňte *azds InitializerConfiguration* z řadiče Azure Dev Spaces:

```azurecli
az aks get-credentials --resource-group <resource group name> --name <cluster name>
```

```bash
kubectl delete InitializerConfiguration azds
```

Po odebrání *azds InitializerConfiguration* z řadiče Azure Dev Spaces, použijte `kubectl delete` k odebrání všech podů ve stavu Čekající na *vyřízení.* Po odebrání všech čekajících podů znovu nasaďte pody.

Pokud nové pody jsou stále zablokované ve stavu `kubectl delete` Čekající *na* vyřízení po opětovném nasazení, použijte k odebrání všech podů ve stavu Čekající na *vyřízení.* Po odebrání všech čekajících podů odstraňte řadič z clusteru a znovu jej nainstalujte:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Po přeinstalaci řadiče znovu nasaďte pody.

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Nesprávná oprávnění RBAC pro volání řadiče Dev Spaces a api

Uživatel, který přistupuje k řadiči Azure Dev Spaces, musí mít přístup ke čtení *správce kubeconfig* v clusteru AKS. Toto oprávnění je například k dispozici v [integrované roli správce clusteru Azure Kubernetes .](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions) Uživatel, který přistupuje k řadiči Azure Dev Spaces, musí mít také roli *přispěvatele* nebo *vlastníka* RBAC pro řadič. Další podrobnosti o aktualizaci oprávnění uživatele pro cluster AKS jsou k dispozici [zde](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group).

Aktualizace role RBAC uživatele pro řadič:

1. Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.
1. Přejděte do skupiny prostředků obsahující řadič, který je obvykle stejný jako cluster AKS.
1. Povolte zaškrtávací políčko *Zobrazit skryté typy.*
1. Klikněte na ovladač.
1. Otevřete podokno *Řízení přístupu (IAM).*
1. Klikněte na kartu *Přiřazení rolí.*
1. Klepněte na tlačítko *Přidat* a potom *na Přidat přiřazení role*.
    * V *části Role*vyberte *přispěvatele* nebo *vlastníka*.
    * *Chcete-li přiřadit přístup k*, vyberte *uživatele, skupinu nebo instanční objekt Služby Azure AD*.
    * V *části Vybrat*vyhledejte uživatele, kterého chcete udělit.
1. Klikněte na *Uložit*.

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Překlad názvů DNS se nezdaří pro veřejnou adresu URL přidruženou ke službě Dev Spaces

Veřejný koncový bod adresy URL pro vaši službu můžete nakonfigurovat zadáním `--enable-ingress` přepínače na `azds prep` příkaz nebo zaškrtnutím `Publicly Accessible` políčka v sadě Visual Studio. Veřejný název DNS se automaticky zaregistruje při spuštění služby v aplikaci Dev Spaces. Pokud tento název DNS není zaregistrován, zobrazí se *stránka nelze zobrazit* nebo web nelze *dosáhnout* chyby ve webovém prohlížeči při připojování k veřejné adrese URL.

Pokud chcete tento problém vyřešit:

* Zkontrolujte stav všech adres URL přidružených ke službám Dev Spaces:

  ```console
  azds list-uris
  ```

* Pokud je adresa URL ve stavu *Čeká na vyřízení,* dev Spaces stále čeká na dokončení registrace DNS. Někdy trvá několik minut, než se registrace dokončí. Dev Spaces také otevře localhost tunelové propojení pro každou službu, které můžete použít při čekání na registraci DNS.
* Pokud adresa URL zůstane ve stavu Čekající na *vyřízení* po dobu delší než 5 minut, může znamenat problém s externípod DNS, který vytvoří veřejný koncový bod nebo řadič příchozího přenosu dat nginx, který získá veřejný koncový bod. Pomocí následujících příkazů odstraňte tyto pody a povolte AKS, aby je automaticky znovu vytvořil:
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Chyba "chyba připojení upstream nebo odpojení/obnovení před záhlavími"

Tato chyba se může zobrazit při pokusu o přístup ke službě. Například když přejdete na adresu URL služby v prohlížeči. Tato chyba znamená, že port kontejneru není k dispozici. To může z následujících důvodů:

* Kontejner je stále v procesu sestavu a nasazení. Tento problém může nastat, pokud spustíte `azds up` nebo spustíte ladicí program a potom zkuste získat přístup ke kontejneru před jeho úspěšné nasazení.
* Konfigurace portu není konzistentní v rámci _dockerového souboru_, grafu helmu a jakéhokoli kódu serveru, který otevře port.

Pokud chcete tento problém vyřešit:

1. Pokud je kontejner v procesu právě sestaví nebo nasadit, můžete počkat 2-3 sekunda mi a zkuste znovu získat přístup ke službě. 
1. Zkontrolujte konfiguraci portu v následujících datových zdrojích:
    * **[Graf síly](https://docs.helm.sh):** `deployment.containerPort` Určeno `azds prep` příkazem `service.port` a v values.yaml scaffolded.
    * Všechny porty, které se otevírají v kódu aplikace, například v souboru Node.js:`var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>Název typu nebo oboru názvů "MyLibrary" nebyl nalezen.

Projekt knihovny, který používáte, nebyl nalezen. S Dev Spaces je kontext sestavení ve výchozím nastavení na úrovni projektu/služby.  

Pokud chcete tento problém vyřešit:

1. Upravte `azds.yaml` soubor a nastavte kontext sestavení na úroveň řešení.
2. Upravte `Dockerfile` `Dockerfile.develop` soubory a tak, aby odkazovaly na soubory projektu, například `.csproj`, správně vzhledem k novému kontextu sestavení.
3. Přidejte `.dockerignore` a ve stejném `.sln` adresáři jako soubor.
4. `.dockerignore` Podle potřeby aktualizujte další položky.

Příklad najdete [zde](https://github.com/sgreenmsft/buildcontextsample).

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>Automatické škálování podu nefunguje v dev prostoru

Při spuštění služby v prostoru pro spuštění, pod této služby je [vložen s další kontejnery pro instrumentaci](how-dev-spaces-works-cluster-setup.md#prepare-your-aks-cluster) a všechny kontejnery v podu musí mít omezení prostředků a požadavky nastavené pro horizontální pod automatické škálování.

Chcete-li tento problém vyřešit, použijte požadavek na prostředky a omezit na vložené kontejnery Dev Spaces. Požadavky na prostředky a omezení lze použít pro vložený kontejner (devspaces-proxy) přidáním `azds.io/proxy-resources` poznámky do vašeho pod spec. Hodnota by měla být nastavena na objekt JSON představující část prostředků specifikace kontejneru pro proxy server.

Níže je uveden příklad anotace proxy zdrojů, která má být použita na vaše pod spec.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>Povolení Azure Dev Spaces v existujícím oboru názvů se spuštěnými pody

Můžete mít existující cluster AKS a obor názvů se spuštěnými pody, kde chcete povolit Azure Dev Spaces.

Chcete-li povolit Azure Dev Spaces v existujícím `use-dev-spaces` oboru `kubectl` názvů v clusteru AKS, spusťte a použijte k restartování všech podů v tomto oboru názvů.

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
```

```console
kubectl -n my-namespace delete pod --all
```

Po restartování podů můžete začít používat existující obor názvů s Azure Dev Spaces.

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>Povolení Azure Dev Spaces v clusteru AKS s omezeným přenosem odchozího přenosu pro uzly clusteru

Chcete-li povolit Azure Dev Spaces v clusteru AKS, pro který je omezen odchozí provoz z uzlů clusteru, budete muset povolit následující hlavní název sítě:

| FQDN                                    | Port      | Použití      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Stažení linuxalpine a dalších bitových kopií Azure Dev Spaces |
| gcr.io | HTTP:443 | Chcete-li vytáhnout helmu / kultivátor obrázky|
| storage.googleapis.com | HTTP:443 | Chcete-li vytáhnout helmu / kultivátor obrázky|
| azds-<guid>. <location>.azds.io | HTTPS:443 | Komunikace s back-endovými službami Azure Dev Spaces pro váš řadič. Přesný hlavní název sítě souborů lze nalézt v poli "dataplaneFqdn" v %USERPROFILE%\.azds\settings.json|

### <a name="error-could-not-find-the-cluster-cluster-in-subscription-subscriptionid"></a>Chyba "Nelze najít \<cluster\> v \<části\>SubscriptionSubscriptionId "

Tato chyba se může zobrazit, pokud váš soubor kubeconfig cílí na jiný cluster nebo předplatné, než se pokoušíte použít s nástroji na straně klienta Azure Dev Spaces. Nástroje azure dev spaces na straně klienta replikuje chování *kubectl*, který používá [jeden nebo více kubeconfig soubory](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/) pro výběr a komunikaci s clusteru.

Pokud chcete tento problém vyřešit:

* Slouží `az aks use-dev-spaces -g <resource group name> -n <cluster name>` k aktualizaci aktuálního kontextu. Tento příkaz také povolí Azure Dev Spaces v clusteru AKS, pokud ještě není povolena. Případně můžete aktualizovat `kubectl config use-context <cluster name>` aktuální kontext.
* Slouží `az account show` k zobrazení aktuálního předplatného Azure, na které cílíte, a ověřte, zda je to správné. Předplatné, na které cílíte, můžete změnit pomocí aplikace `az account set`.

### <a name="error-using-dev-spaces-after-rotating-aks-certificates"></a>Po otočení certifikátů AKS došlo k chybě při použití funkce Dev Spaces.

Po [otočení certifikátů v clusteru AKS](../aks/certificate-rotation.md), `azds space list` některé `azds up` operace, jako je například a se nezdaří. Certifikáty na řadiči Azure Dev Spaces je také potřeba aktualizovat po otočení certifikátů v clusteru.

Chcete-li tento problém vyřešit, ujistěte se, `az aks get-credentials` že `azds controller refresh-credentials` *vaše kubeconfig* má aktualizované certifikáty pomocí pak spustit příkaz. Například:

```azurecli
az aks get-credentials -g <resource group name> -n <cluster name>
```

```console
azds controller refresh-credentials -g <resource group name> -n <cluster name>
```
