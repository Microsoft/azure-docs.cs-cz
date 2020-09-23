---
title: Řešení potíží
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: troubleshooting
description: Naučte se řešit problémy a řešit běžné problémy při povolování a používání Azure Dev Spaces.
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s '
ms.openlocfilehash: d697a11f3087c31a49d9b88e99b18bab686a2b59
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90981064"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Řešení potíží s Azure Dev Spaces

Tato příručka obsahuje informace o běžných problémech, které můžete mít při použití Azure Dev Spaces.

Pokud máte problém s použitím Azure Dev Spaces, vytvořte [problém v úložišti Azure dev Spaces GitHubu](https://github.com/Azure/dev-spaces/issues).

## <a name="before-you-begin"></a>Než začnete

Pokud chcete řešit problémy efektivněji, může vám pomoct vytvořit podrobnější protokoly pro kontrolu.

V sadě Visual Studio nastavte `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` proměnnou prostředí na 1. Nezapomeňte restartovat Visual Studio, aby se proměnná prostředí projevila. Po povolení budou do vašeho adresáře zapsány podrobné protokoly `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` .

V rozhraní příkazového řádku můžete při provádění příkazu pomocí přepínače vymezit výstup dalších informací `--verbose` . Můžete také procházet podrobnější protokoly v nástroji `%TEMP%\Azure Dev Spaces` . V počítači Mac můžete *dočasný* adresář najít spuštěním `echo $TMPDIR` z okna terminálu. V počítači se systémem Linux je *dočasný* adresář obvykle `/tmp` . Dále ověřte, že je v [konfiguračním souboru Azure CLI](/cli/azure/azure-cli-configuration?view=azure-cli-latest#cli-configuration-values-and-environment-variables)povolené protokolování.

Azure Dev Spaces také funguje nejlépe při ladění jedné instance nebo pod. `azds.yaml`Soubor obsahuje nastavení *replicaCount*, které označuje počet lusků, které Kubernetes pro vaši službu spustí. Změníte-li *replicaCount* pro konfiguraci aplikace tak, aby spouštěla více lusků pro danou službu, ladicí program se připojí k prvnímu pod, pokud je uveden abecedně. Ladicí program se připojí k jinému pod při recyklování původní pod, což může vést k neočekávanému chování.

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Běžné problémy při povolování Azure Dev Spaces

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Chyba: nepovedlo se vytvořit kontroler Azure Dev Spaces.

Tato chyba se může zobrazit v případě, že došlo k potížím s vytvořením kontroleru. Pokud se jedná o přechodnou chybu, odstraňte a znovu vytvořte kontroler, abyste ho opravili.

Můžete také zkusit odstranit kontroler:

```bash
azds remove -g <resource group name> -n <cluster name>
```

K odstranění kontroleru použijte Azure Dev Spaces CLI. Z aplikace Visual Studio není možné odstranit kontroler. Nemůžete také nainstalovat Azure Dev Spaces CLI do Azure Cloud Shell, takže nemůžete odstranit řadič z Azure Cloud Shell.

Pokud nemáte nainstalované rozhraní příkazového řádku Azure Dev Spaces, můžete ho nejdřív nainstalovat pomocí následujícího příkazu a pak odstranit kontroler:

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

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

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Chyba "Azure Dev Spaces rozhraní příkazového řádku není správně nainstalováno" při spuštění příkazu AZ AKS use-dev-Spaces

Aktualizace rozhraní příkazového řádku Azure Dev Spaces změnila jeho cestu instalace. Pokud používáte verzi Azure CLI starší než 2.0.63, může se zobrazit tato chyba. Pokud chcete zobrazit verzi rozhraní příkazového řádku Azure CLI, použijte `az --version` .

```azurecli
az --version
```

```output
azure-cli                         2.0.60 *
...
```

Bez ohledu na chybovou zprávu při použití `az aks use-dev-spaces` verze Azure CLI před 2.0.63 instalace proběhne úspěšně. Bez problémů můžete pokračovat v používání `azds` .

Pokud chcete tento problém vyřešit, aktualizujte instalaci [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) na 2.0.63 nebo novější. Tato aktualizace vyřeší chybovou zprávu, která se zobrazí při spuštění `az aks use-dev-spaces` . Případně můžete dál používat aktuální verzi rozhraní příkazového řádku Azure CLI a Azure Dev Spaces CLI.

### <a name="error-unable-to-reach-kube-apiserver"></a>Chyba "nepovedlo se kontaktovat Kube-apiserver".

Tato chyba se může zobrazit, když se Azure Dev Spaces nedokáže připojit k serveru rozhraní API clusteru AKS.

Pokud je přístup k serveru API clusteru AKS uzamčený nebo pokud máte povolené [rozsahy IP adres serveru API](../aks/api-server-authorized-ip-ranges.md) pro váš cluster AKS, musíte taky [vytvořit](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled) nebo [aktualizovat](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges) cluster a [Povolit další rozsahy na základě vaší oblasti](configure-networking.md#aks-cluster-network-requirements) .

Zajistěte, aby byl server rozhraní API dostupný spuštěním příkazů kubectl. Pokud server rozhraní API není k dispozici, obraťte se prosím na podporu AKS a zkuste to znovu, až Server API funguje.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Běžné problémy při přípravě projektu na Azure Dev Spaces

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Upozornění "souboru Dockerfile nebylo možné vygenerovat z důvodu nepodporovaného jazyka"
Azure Dev Spaces poskytuje nativní podporu pro C# a Node.js. Když pracujete `azds prep` v adresáři s kódem napsaným v jednom z těchto jazyků, Azure dev Spaces pro vás automaticky vytvoří odpovídající souboru Dockerfile.

Azure Dev Spaces můžete dál používat s kódem napsaným v jiných jazycích, ale před prvním spuštěním musíte ručně vytvořit souboru Dockerfile `azds up` .

Pokud je vaše aplikace napsána v jazyce, který Azure Dev Spaces netivně podporuje, je nutné poskytnout odpovídající souboru Dockerfile k vytvoření image kontejneru, ve které je spuštěn váš kód. Docker poskytuje [seznam osvědčených postupů pro psaní fázemi](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) a [souboru Dockerfile odkaz](https://docs.docker.com/engine/reference/builder/) , který vám může poznamenat psaní souboru Dockerfile, které vyhovuje vašim potřebám.

Jakmile budete mít vhodný souboru Dockerfile, spustíte program, který spustí `azds up` aplikaci v Azure dev Spaces.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Běžné problémy při spouštění nebo zastavování služeb pomocí Azure Dev Spaces

### <a name="error-config-file-not-found"></a>Chyba "konfigurační soubor nebyl nalezen:"

Při spuštění `azds up` se může zobrazit tato chyba. Obojí `azds up` a `azds prep` musí být spuštěny z kořenového adresáře projektu, který chcete spustit ve vývojovém prostoru.

Pokud chcete tento problém vyřešit:
1. Změňte aktuální adresář na kořenovou složku, která obsahuje váš kód služby. 
1. Pokud ve složce kódu nemáte soubor _azds. yaml_ , spusťte příkaz `azds prep` a vygenerujte prostředky Docker, Kubernetes a Azure dev Spaces.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Vypršel časový limit při čekání na sestavení image kontejneru... krok s virtuálními uzly AKS

K tomuto časovému limitu dojde, když se pokusíte použít vývojové prostory ke spuštění služby, která je nakonfigurovaná tak, aby běžela ve [virtuálním uzlu AKS](../aks/virtual-nodes-portal.md). Vývojové prostory v současné době nepodporují vytváření nebo ladění služeb na virtuálních uzlech.

Pokud spustíte `azds up` s `--verbose` přepínačem nebo povolíte podrobné protokolování v aplikaci Visual Studio, zobrazí se další podrobnosti:

```cmd
azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

Výše uvedený příkaz ukazuje, že je uzel Service přiřazený k *virtuálnímu uzlu-ACI-Linux*, který je virtuálním uzlem.

Pokud chcete tento problém vyřešit, aktualizujte graf Helm pro službu a odeberte všechny hodnoty *nodeSelector* nebo *tolerování* , které umožní službě běžet ve virtuálním uzlu. Tyto hodnoty jsou obvykle definovány v `values.yaml` souboru grafu.

I nadále můžete používat cluster AKS s povolenou funkcí virtuálních uzlů, pokud služba, kterou chcete sestavit nebo ladit pomocí vývojových prostorů, běží na uzlu virtuálního počítače. Výchozí konfigurace je spuštění služby s vývojářskými prostory na uzlu virtuálního počítače.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Chyba: nepovedlo se najít připraveného pokladny pod při spouštění vývojových prostorů.

K této chybě dochází, pokud klient Helm již nemůže komunikovat s pokladnou pod spuštěným v clusteru.

Chcete-li tento problém vyřešit, restartujte uzly agenta v clusteru.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>Chyba "verze azds – \<identifier\> - \<spacename\> - \<servicename\> neúspěšná: služby" \<servicename\> už existují "nebo" přístup k přístupu na vyžádání "byl odepřen pro \<servicename\> , úložiště neexistuje nebo může vyžadovat" Docker login ".

K těmto chybám může docházet v případě, že ve stejném vývojovém prostoru budete kombinovat spuštěné příkazy Helm (například `helm install` , `helm upgrade` nebo `helm delete` ) s příkazy pro vývoj prostorů (například `azds up` a `azds down` ). K nim dochází, protože vývojové prostory mají svou vlastní instanci pokladny, která je v konfliktu s vaší vlastní instancí pokladny spuštěnou ve stejném vývojovém prostoru.

Je možné použít příkazy Helm i příkazy pro vývoj v rámci stejného clusteru AKS, ale každý obor názvů s povoleným místem pro vývoj by měl použít buď jednu, nebo druhou.

Předpokládejme například, že použijete příkaz Helm ke spuštění celé aplikace v nadřazeném vývojovém prostoru. Můžete vytvořit podřízené vývojové prostory z této nadřazené položky, pomocí vývojových prostorů spouštět jednotlivé služby uvnitř podřízených vývojových prostorů a testovat služby společně. Až budete připraveni k vrácení změn se změnami, nasaďte aktualizovaný kód do nadřazeného vývojového prostoru pomocí příkazu Helm. Nepoužívejte `azds up` ke spuštění aktualizované služby v nadřazeném vývojovém prostoru, protože bude v konfliktu se službou nejprve spuštěna pomocí Helm.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>Stávající souboru Dockerfile se nepoužilo k sestavení kontejneru.

Azure Dev Spaces lze nakonfigurovat tak, aby odkazovaly na konkrétní _souboru Dockerfile_ v projektu. Pokud se zobrazí Azure Dev Spaces nepoužívá _souboru Dockerfile_ , kterou očekáváte k sestavování kontejnerů, možná budete muset explicitně sdělit Azure dev Spaces které souboru Dockerfile se mají použít. 

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

Pokud chcete tento problém vyřešit, můžete vývojářům dovolit, aby k ověřování a vyžádání imagí z tohoto privátního registru používali [imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets). Pokud chcete použít imagePullSecrets, vytvořte v oboru názvů, kde používáte image, [tajný klíč Kubernetes](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) . Pak zadejte tajný klíč jako imagePullSecret v `azds.yaml` .

Níže je uveden příklad zadání imagePullSecrets v `azds.yaml` .

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
> Nastavení imagePullSecrets v přepíše `azds.yaml` imagePullSecrets zadané v `values.yaml` .

### <a name="error-service-cannot-be-started"></a>Chyba "službu nelze spustit."

Tato chyba se může zobrazit, když se kód služby nepovede spustit. Příčinou je často v uživatelském kódu. Pokud chcete získat další diagnostické informace, při spouštění služby povolte podrobnější protokolování.

Z příkazového řádku, použijte `--verbose` k povolení podrobnějšího protokolování. Výstupní formát můžete zadat také pomocí `--output` . Příklad:

```cmd
azds up --verbose --output json
```

V aplikaci Visual Studio:

1. Otevřete **nástroje > možnosti** a v části **projekty a řešení**vyberte **vytvořit a spustit**.
2. Změňte nastavení pro **Podrobnosti výstupu sestavení projektu MSBuild** na **podrobné** nebo **diagnostické**.

    ![Snímek obrazovky s dialogem možnosti nástrojů](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>Opětovné spuštění služby po opětovném vytvoření kontroleru

Po odebrání a opětovném vytvoření kontroleru Azure Dev Spaces přidruženého k tomuto clusteru se zobrazí chyba, že se *služba nemůže spustit* , když se pokusíte znovu spustit službu. V takové situaci obsahuje podrobný výstup následující text:

```output
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

K této chybě dochází, protože odebráním kontroleru pro vývoj prostorů nedojde k odebrání služeb dříve nainstalovaných tímto kontrolérem. Opětovné vytvoření kontroleru a následné pokusu o spuštění služeb pomocí nového kontroleru se nezdaří, protože staré služby jsou pořád na svém místě.

Pokud chcete tento problém vyřešit, pomocí `kubectl delete` příkazu ručně odeberte staré služby z clusteru a pak znovu spusťte vývojové prostory a nainstalujte nové služby.

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

K této chybě dochází, protože Azure Dev Spaces v současné době nepodporuje sestavení ve více fázích. Chcete-li se vyhnout sestavením s více fázemi, přepište své souboru Dockerfile.

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>Při připojování vývojového počítače se síťový provoz nepřepošle do vašeho clusteru AKS

Při použití [Azure dev Spaces k připojení clusteru AKS k vývojovému počítači](https://code.visualstudio.com/docs/containers/bridge-to-kubernetes)se může vyskytnout problém, kdy se síťový provoz nepředává mezi vaším vývojovým počítačem a clusterem AKS.

Při připojování vývojového počítače ke clusteru AKS Azure Dev Spaces předávány síťový provoz mezi clusterem AKS a vývojovým počítačem úpravou souboru vývojového počítače `hosts` . Azure Dev Spaces vytvoří položku `hosts` s adresou služby Kubernetes, kterou nahrazujete jako název hostitele. Tato položka se používá s předáváním portů pro přímý přenos síťového provozu mezi vývojovým počítačem a clusterem AKS. Je-li služba na vašem vývojovém počítači v konfliktu s portem služby Kubernetes, kterou nahrazujete, Azure Dev Spaces nemůže dopředt síťový provoz pro službu Kubernetes. Například služba *BranchCache systému Windows* je obvykle svázaná s *hodnotou 0.0.0.0:80*, což může způsobit konflikt pro port 80 na všech místních IP adresách.

Chcete-li tento problém vyřešit, je třeba zastavit všechny služby nebo procesy, které jsou v konfliktu s portem služby Kubernetes, kterou se pokoušíte nahradit. Pomocí nástrojů, jako je například *netstat*, můžete zkontrolovat, které služby nebo procesy ve vývojovém počítači jsou v konfliktu.

Chcete-li například zastavit a zakázat službu *Windows BranchCache* :
* Spusťte `services.msc` z příkazového řádku.
* Klikněte pravým tlačítkem na *BranchCache* a vyberte *vlastnosti*.
* Klikněte na tlačítko *zastavit*.
* Volitelně ho můžete zakázat nastavením *Typ spuštění* na *zakázáno*.
* Klikněte na *OK*.

### <a name="error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state"></a>Chyba "nenašel se žádný AzureAssignedIdentity pro pod: azds/azds-Webhook-nasazení- \<id\> v přiřazeném stavu"

Když spustíte službu s Azure Dev Spaces v clusteru AKS s nainstalovanou spravovanými [identitami spravované identity](../aks/use-managed-identity.md) a [pod](../aks/developer-best-practices-pod-security.md#use-pod-managed-identities) , proces může po kroku *instalace grafu* přestat reagovat. Pokud provedete kontrolu *azds-injektor-Webhooku* v prostoru názvů *azds* , může se zobrazit tato chyba.

Služby Azure Dev Spaces v clusteru používají spravovanou identitu clusteru ke komunikaci se službami back-endu Azure Dev Spaces mimo cluster. Když je nainstalovaná spravovaná identita pod, na uzlech clusteru se nakonfigurují Síťová pravidla, která budou přesměrovat všechna volání pro spravovaná pověření identity na [NMI (Node Managed identity) DaemonSet nainstalovanou v clusteru](https://github.com/Azure/aad-pod-identity#node-managed-identity). Tento NMI DaemonSet identifikuje volající a zajistí, že pod ním byl označen odpovídajícím způsobem přístup k požadované spravované identitě. Azure Dev Spaces nemůže zjistit, jestli je v clusteru nainstalovaná spravovaná identita, a nemůže provést potřebnou konfiguraci, aby služby Azure Dev Spaces mohly přistupovat ke spravované identitě clusteru. Vzhledem k tomu, že služba Azure Dev Spaces Services není nakonfigurovaná pro přístup ke spravované identitě clusteru, DaemonSet NMI jim neumožní získat token Azure AD pro spravovanou identitu a nebude komunikovat se službami Azure Dev Spaces back-endu.

Pokud chcete tento problém vyřešit, použijte pro přístup ke spravované identitě [AzurePodIdentityException](https://github.com/Azure/aad-pod-identity/blob/master/docs/readmes/README.app-exception.md) pro *azds-injektor – Webhook* a Update lusks instrumentované Azure dev Spaces.

Vytvořte soubor s názvem *webhookException. yaml* a zkopírujte následující definici YAML:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: azds-infrastructure-exception
  namespace: azds
spec:
  PodLabels:
    azds.io/uses-cluster-identity: "true"
```

Výše uvedený soubor vytvoří objekt *AzurePodIdentityException* pro azds- *injektor – Webhook*. K nasazení tohoto objektu použijte `kubectl` :

```cmd
kubectl apply -f webhookException.yaml
```

Pokud chcete aktualizovat lusky instrumentované pomocí Azure Dev Spaces pro přístup ke spravované identitě, aktualizujte *obor názvů* v níže uvedené definici YAML a použijte `kubectl` ho k použití pro každé vývojové místo.

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: azds-infrastructure-exception
  namespace: myNamespace
spec:
  PodLabels:
    azds.io/instrumented: "true"
```

Alternativně můžete vytvořit objekty *AzureIdentity* a *AzureIdentityBinding* a aktualizovat popisky pod pro úlohy spuštěné v prostorech instrumentované Azure dev Spaces pro přístup ke spravované identitě vytvořené clusterem AKS.

Chcete-li zobrazit podrobnosti o spravované identitě, spusťte následující příkaz pro cluster AKS:

```azurecli
az aks show -g <resourcegroup> -n <cluster> -o json --query "{clientId: identityProfile.kubeletidentity.clientId, resourceId: identityProfile.kubeletidentity.resourceId}"
```

Výše uvedený příkaz vypíše *ClientID* a *ResourceID* pro spravovanou identitu. Příklad:

```json
{
  "clientId": "<clientId>",
  "resourceId": "/subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>"
}
```

Pokud chcete vytvořit objekt *AzureIdentity* , vytvořte soubor s názvem *clusteridentity. yaml* a použijte následující definici YAML s podrobnostmi o spravované identitě z předchozího příkazu:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentity
metadata:
  name: my-cluster-mi
spec:
  type: 0
  ResourceID: /subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>
  ClientID: <clientId>
```

Chcete-li vytvořit objekt *AzureIdentityBinding* , vytvořte soubor s názvem *clusteridentitybinding. yaml* a použijte následující definici YAML:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: my-cluster-mi-binding
spec:
  AzureIdentity: my-cluster-mi
  Selector: my-label-value
```

Chcete-li nasadit objekty *AzureIdentity* a *AzureIdentityBinding* , použijte `kubectl` :

```cmd
kubectl apply -f clusteridentity.yaml
kubectl apply -f clusteridentitybinding.yaml
```

Až nasadíte objekty *AzureIdentity* a *AzureIdentityBinding* , budou mít všechny úlohy s jmenovkou *aadpodidbinding: My-Label-Value* přístup ke spravované identitě clusteru. Přidejte tento popisek a znovu nasaďte všechny úlohy spuštěné v jakémkoli vývojovém prostoru. Příklad:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: sample
        aadpodidbinding: my-label-value
    spec:
      [...]
```

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>Běžné problémy s používáním sady Visual Studio a Visual Studio Code s Azure Dev Spaces

### <a name="error-required-tools-and-configurations-are-missing"></a>Chyba: chybí požadované nástroje a konfigurace.

K této chybě může dojít při spuštění VS Code: "[Azure Dev Spaces] požadované nástroje a konfigurace pro sestavení a ladění [název projektu] chybí."
Chyba znamená, že azds.exe není v proměnné prostředí PATH, jak je vidět v VS Code.

Zkuste spustit VS Code z příkazového řádku, kde je správně nastavená proměnná prostředí PATH.

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Chyba: požadované nástroje pro sestavení a ladění ProjectName jsou zastaralé.

Tato chyba se zobrazí v Visual Studio Code, pokud máte novější verzi rozšíření VS Code pro Azure Dev Spaces, ale starší verzi Azure Dev Spaces CLI.

Zkuste stáhnout a nainstalovat nejnovější verzi rozhraní příkazového řádku Azure Dev Spaces:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>Chyba: nepovedlo se najít rozšíření ladicího programu pro typ: CoreCLR.

Tato chyba se může zobrazit při spuštění ladicího programu Visual Studio Code. Na vašem vývojovém počítači možná nemáte nainstalovanou příponu VS Code pro C#. Rozšíření C# zahrnuje podporu ladění pro .NET Core (CoreCLR).

Chcete-li tento problém vyřešit, nainstalujte [rozšíření vs Code pro jazyk C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>Chyba "nakonfigurovaný typ ladění" CoreCLR "není podporován"

Tato chyba se může zobrazit při spuštění ladicího programu Visual Studio Code. Možná nemáte rozšíření VS Code pro Azure Dev Spaces ve vývojovém počítači nainstalované.

Chcete-li tento problém vyřešit, nainstalujte rozšíření VS Code pro Azure Dev Spaces.

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Chyba "neplatná" hodnota "CWD"/src ". Systém nemůže najít zadaný soubor. nebo "Launch: program"/src/[cesta k binárnímu souboru projektu] "neexistuje"

Tato chyba se může zobrazit při spuštění ladicího programu Visual Studio Code. Ve výchozím nastavení používá rozšíření VS Code `src` jako pracovní adresář pro projekt na kontejneru. Pokud jste aktualizovali `Dockerfile` , abyste určili jiný pracovní adresář, může se zobrazit tato chyba.

Chcete-li tento problém vyřešit, aktualizujte `launch.json` soubor v `.vscode` podadresáři složky projektu. Změňte `configurations->cwd` direktivu tak, aby odkazovala na stejný adresář jako `WORKDIR` definovaný v projektu `Dockerfile` . Také je možné, že budete muset taky aktualizovat `configurations->program` direktivu.

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Chyba "program kanálu ' azds ' se neočekávaně ukončil s kódem 126."

Tato chyba se může zobrazit při spuštění ladicího programu Visual Studio Code.

Chcete-li tento problém vyřešit, zavřete a znovu otevřete Visual Studio Code. Restartujte ladicí program.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Chyba "vnitřní kukátko se nezdařilo: Sledujte ENOSPC" při připojování ladění k aplikaci Node.js

K této chybě dojde, když uzel, na kterém je spuštěný, s aplikací Node.js, ke které se pokoušíte připojit pomocí ladicího programu, překročil hodnotu *FS. inotify. max_user_watches* . V některých případech [může být výchozí hodnota *FS. inotify. max_user_watches* příliš malá, aby bylo možné manipulovat s připojením ladicího programu přímo k poli pod](https://github.com/Azure/AKS/issues/772).

Dočasným řešením tohoto problému je zvýšit hodnotu *FS. inotify. max_user_watches* v každém uzlu v clusteru a restartovat tento uzel, aby se změny projevily.

## <a name="other-common-issues"></a>Další běžné problémy

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Chyba "azds" není rozpoznána jako interní nebo externí příkaz, spustitelný program nebo dávkový soubor.

K této chybě může dojít `azds.exe` , pokud není správně nainstalovaná nebo nakonfigurovaná.

Pokud chcete tento problém vyřešit:

1. Ověřte umístění% ProgramFiles%/Microsoft SDKs\Azure\Azure dev Spaces CLI pro `azds.exe` . Pokud tam tam je, přidejte toto umístění do proměnné prostředí PATH.
2. Pokud `azds.exe` není nainstalován, spusťte následující příkaz:

    ```azurecli
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>Chyba autorizace "Microsoft. DevSpaces/Register/Action"

Ke správě Azure Dev Spaces potřebujete ve svém předplatném Azure přístup *vlastníka* nebo *přispěvatele* . Pokud se pokoušíte spravovat vývojové prostory a nemáte oprávnění *vlastníka* nebo *přispěvatele* k přidruženému předplatnému Azure, může se zobrazit chyba autorizace. Příklad:

```output
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

Pokud chcete tento problém vyřešit, pomocí účtu s přístupem *vlastníka* nebo *přispěvatele* k předplatnému Azure ručně zaregistrujte `Microsoft.DevSpaces` obor názvů:

```azurecli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>Nová Luska se nespouští.

Inicializátor Kubernetes nemůže použít PodSpec pro nové lusky z důvodu změn oprávnění RBAC do role *Správce clusteru* v clusteru. Nový objekt pod může mít také neplatnou PodSpec, například účet služby spojený s objektem pod již neexistuje. Chcete-li zobrazit lusky, které jsou ve stavu *čekání* v důsledku problému s inicializátorem, použijte `kubectl get pods` příkaz:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Tento problém může mít vliv na lusky ve *všech oborech názvů* v clusteru včetně oborů názvů, kde není povolený Azure dev Spaces.

Pokud chcete tento problém vyřešit, [aktualizujte vývojové prostory CLI na nejnovější verzi](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) a pak odstraňte *azds InitializerConfiguration* z kontroleru Azure dev Spaces:

```azurecli
az aks get-credentials --resource-group <resource group name> --name <cluster name>
```

```bash
kubectl delete InitializerConfiguration azds
```

Po odebrání *Azds InitializerConfiguration* z kontroleru Azure dev Spaces použijte `kubectl delete` k odebrání všech lusků ve stavu *čekání na vyřízení* . Po odebrání všech probíhajících lusků znovu nasaďte své lusky.

Pokud se nové lusky po opětovném nasazení stále zablokují ve stavu *čekání* , použijte `kubectl delete` k odebrání všech lusků ve stavu *čekání na vyřízení* . Po odebrání všech probíhajících lusků odstraňte řadič z clusteru a znovu ho nainstalujte:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Po opětovné instalaci kontroleru znovu nasaďte své lusky.

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Nesprávná oprávnění RBAC pro volání řadiče pro vývoj prostorů a rozhraní API

Uživatel, který přistupuje k řadiči Azure Dev Spaces, musí mít přístup, aby mohl číst *kubeconfig* správce v clusteru AKS. Toto oprávnění je například k dispozici v [předdefinované roli Správce clusteru služby Azure Kubernetes](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions). Uživatel, který přistupuje k řadiči Azure Dev Spaces, musí mít také roli *Přispěvatel* nebo *Owner* role Azure pro řadič. Další podrobnosti o aktualizaci oprávnění uživatele pro cluster AKS jsou k dispozici [zde](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group).

Aktualizace role uživatele Azure pro kontroler:

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

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Překlad názvů DNS se nezdařil pro veřejnou adresu URL přidruženou ke službě dev Spaces.

Můžete nakonfigurovat koncový bod veřejné adresy URL pro vaši službu zadáním `--enable-ingress` přepínače na `azds prep` příkaz nebo zaškrtnutím `Publicly Accessible` políčka v aplikaci Visual Studio. Veřejný název DNS se automaticky zaregistruje při spuštění služby ve vývojových prostorech. Pokud tento název DNS není zaregistrovaný, zobrazí se ve webovém prohlížeči při připojování k veřejné adrese URL *Stránka nemůžete zobrazit* nebo *se k webu nedá dostat* chyba.

Pokud chcete tento problém vyřešit:

* Ověřte stav všech adres URL přidružených k vašim službám dev Spaces:

  ```console
  azds list-uris
  ```

* Pokud je adresa URL ve stavu *čekání na vyřízení* , vývojové prostory stále čekají na dokončení registrace DNS. Dokončení registrace může někdy trvat několik minut. Vývojové prostory také otevřou tunel localhost pro každou službu, kterou můžete použít při čekání na registraci DNS.
* Pokud adresa URL zůstane v *nevyřízeném* stavu po dobu delší než 5 minut, může to znamenat problém s externím DNS pod tím, že vytvoří veřejný koncový bod nebo Nginx příchozí řadič domény pod tím, který získá veřejný koncový bod. Pomocí následujících příkazů odstraňte tyto lusky a umožněte AKS jejich automatickému opětovnému vytvoření:
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Chyba "Chyba nadřazeného připojení nebo odpojení/resetování před záhlavími"

Tato chyba se může zobrazit při pokusu o přístup ke službě. Například když v prohlížeči přejdete na adresu URL služby. Tato chyba znamená, že port kontejneru není k dispozici. To může být z následujících důvodů:

* Kontejner se stále vytváří a nasazuje. K tomuto problému může dojít, pokud spustíte `azds up` nebo spustíte ladicí program a potom se pokusíte o přístup k kontejneru předtím, než se úspěšně nasadí.
* Konfigurace portů není konzistentní v rámci _souboru Dockerfile_, grafu Helm a libovolného kódu serveru, který otevírá port.

Pokud chcete tento problém vyřešit:

1. Pokud je kontejner v procesu sestavení/nasazení, můžete počkat 2-3 sekund a pokusit se o přístup ke službě znovu. 
1. Ověřte konfiguraci portů v následujících zdrojích:
    * ** [Graf Helm](https://docs.helm.sh):** Určené `service.port` `deployment.containerPort` hodnotami a v hodnotách. yaml vygenerované pomocí `azds prep` příkazu.
    * Jakékoli porty, které jsou otevřeny v kódu aplikace, například v Node.js: `var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>Název typu nebo oboru názvů "MyLibrary" se nenašel.

Projekt knihovny, který používáte, se nenašel. Pomocí vývojových prostorů je kontext buildu ve výchozím nastavení na úrovni projektu nebo služby.  

Pokud chcete tento problém vyřešit:

1. Upravte `azds.yaml` soubor a nastavte kontext sestavení na úroveň řešení.
2. Upravte `Dockerfile` soubory a `Dockerfile.develop` tak, aby odkazovaly na soubory projektu, například `.csproj` správně vzhledem k novému kontextu sestavení.
3. Přidejte do `.dockerignore` stejného adresáře jako `.sln` soubor.
4. Aktualizujte podle `.dockerignore` potřeby další položky.

Příklad najdete [tady](https://github.com/sgreenmsft/buildcontextsample).

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>Automatické škálování pod automatickým škálováním nefunguje ve vývojovém prostoru

Když službu spustíte ve vývojovém prostoru, je tato služba [vložená s dalšími kontejnery pro instrumentaci](how-dev-spaces-works-cluster-setup.md#prepare-your-aks-cluster) a všechny kontejnery v rámci musí mít omezení prostředků a požadavky nastavené na automatické škálování vodorovně pod.

Chcete-li tento problém vyřešit, použijte požadavek prostředku a omezte na vložené kontejnery pro vývoj prostorů. Požadavky na prostředky a omezení lze použít pro vložený kontejner (devspaces-proxy) přidáním `azds.io/proxy-resources` poznámky k specifikaci pod. Hodnota by měla být nastavena na objekt JSON, který představuje oddíl prostředků specifikace kontejneru pro proxy server.

Níže je uveden příklad anotace prostředků proxy, která se má použít pro specifikaci pod.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>Povolit Azure Dev Spaces v existujícím oboru názvů s běžícími lusky

Je možné, že máte existující cluster AKS a obor názvů se spuštěnými lusky, kde chcete povolit Azure Dev Spaces.

Pokud chcete povolit Azure Dev Spaces v existujícím oboru názvů v clusteru AKS, spusťte `use-dev-spaces` a použijte `kubectl` k restartování všech lusků v daném oboru názvů.

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
```

```console
kubectl -n my-namespace delete pod --all
```

Po restartování lusků můžete začít používat stávající obor názvů s Azure Dev Spaces.

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>Povolení Azure Dev Spaces v clusteru AKS s omezeným provozem odchozích dat pro uzly clusteru

Pokud chcete povolit Azure Dev Spaces v clusteru AKS, pro který je omezený provoz z uzlů clusteru, budete muset povolit tyto plně kvalifikované názvy domén:

| FQDN                                    | Port      | Použití      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS: 443 | Vyžádat si Linux Alpine a jiné Azure Dev Spaces image |
| gcr.io | HTTP: 443 | Načtení imagí Helm/překladen|
| storage.googleapis.com | HTTP: 443 | Načtení imagí Helm/překladen|

Aktualizujte bránu firewall nebo konfiguraci zabezpečení tak, aby povolovaly síťový provoz do a ze všech výše uvedených plně kvalifikovaných názvů domény a [služby Azure dev Spaces infrastruktury](../dev-spaces/configure-networking.md#virtual-network-or-subnet-configurations).

### <a name="error-could-not-find-the-cluster-cluster-in-subscription-subscriptionid"></a>Chyba: nepovedlo se najít cluster \<cluster\> v předplatném. \<subscriptionId\>

Tato chyba se může zobrazit v případě, že váš soubor kubeconfig cílí na jiný cluster nebo předplatné, než se snažíte použít s Azure Dev Spaces nástrojů na straně klienta. Nástroje Azure Dev Spaces na straně klienta replikují chování *kubectl*, které používá [jeden nebo více souborů kubeconfig](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/) k výběru a komunikaci s clusterem.

Pokud chcete tento problém vyřešit:

* Použijte `az aks use-dev-spaces -g <resource group name> -n <cluster name>` k aktualizaci aktuálního kontextu. Tento příkaz také umožňuje Azure Dev Spaces v clusteru AKS, pokud ještě není povolený. Alternativně můžete použít `kubectl config use-context <cluster name>` k aktualizaci aktuálního kontextu.
* Použijte `az account show` k zobrazení aktuálního předplatného Azure, které cílíte, a ověřte, jestli je to správné. Předplatné, které cílíte, můžete změnit pomocí `az account set` .

### <a name="error-using-dev-spaces-after-rotating-aks-certificates"></a>Chyba při použití vývojových prostorů po otočení AKS certifikátů

Po [otočení certifikátů v clusteru AKS](../aks/certificate-rotation.md)se některé operace, například `azds space list` a, `azds up` nezdaří. Po otočení certifikátů v clusteru je také potřeba aktualizovat certifikáty na řadiči Azure Dev Spaces.

Pokud chcete tento problém vyřešit, ujistěte se, že vaše *kubeconfig* má aktualizované certifikáty, a `az aks get-credentials` pak `azds controller refresh-credentials` příkaz spusťte. Příklad:

```azurecli
az aks get-credentials -g <resource group name> -n <cluster name>
```

```console
azds controller refresh-credentials -g <resource group name> -n <cluster name>
```
