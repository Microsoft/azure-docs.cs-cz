---
title: 'Kurz: implementace CI/CD pomocí GitOps s využitím clusterů Kubernetes s podporou ARC Azure'
description: Tento kurz vás provede nastavením řešení CI/CD pomocí GitOps s povolenými clustery Kubernetes s podporou ARC Azure. Pro koncepční použití tohoto pracovního postupu si přečtěte článek pracovní postup CI/CD s použitím GitOps-Kubernetesed on Azure ARC.
author: tcare
ms.author: tcare
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/03/2021
ms.custom: template-tutorial, devx-track-azurecli
ms.openlocfilehash: 6fb8802dd92e6f9bd55a96772abe3cef5150ac30
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478382"
---
# <a name="tutorial-implement-cicd-with-gitops-using-azure-arc-enabled-kubernetes-clusters"></a>Kurz: implementace CI/CD pomocí GitOps s využitím clusterů Kubernetes s podporou ARC Azure


V tomto kurzu nastavíte řešení CI/CD pomocí GitOps s povolenými clustery Kubernetes s podporou ARC Azure. Pomocí ukázkové aplikace pro hlasování v Azure:

> [!div class="checklist"]
> * Vytvořte cluster Kubernetes s povoleným ARC Azure.
> * Připojte k Azure Repos úložiště aplikace a GitOps.
> * Importujte kanály CI/CD.
> * Připojte své Azure Container Registry (ACR) k Azure DevOps a Kubernetes.
> * Vytvořte skupiny proměnných prostředí.
> * Nasaďte `dev` `stage` prostředí a.
> * Otestujte prostředí aplikace.

Pokud nepodporují™ t, abyste měli předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="before-you-begin"></a>Než začnete

V tomto kurzu se seznámíte s Azure DevOps, Azure Reposmi a kanály a Azure CLI.

* Přihlaste se [Azure DevOps Services](https://dev.azure.com/).
* Dokončete [předchozí kurz](./tutorial-use-gitops-connected-cluster.md) , kde se dozvíte, jak nasadit GitOps pro prostředí CI/CD.
* Pochopte [výhody a architektury](./conceptual-configurations.md) této funkce.
* Ověřte, že máte následující:
  * [Připojený cluster Kubernetes s povoleným ARC Azure](./quickstart-connect-cluster.md#connect-an-existing-kubernetes-cluster) pojmenovaný **oblouk-cicd-cluster**.
  * Připojená Azure Container Registry (ACR) s [AKS Integration](../../aks/cluster-container-registry-integration.md) nebo [bez AKSho ověřování clusteru](../../container-registry/container-registry-auth-kubernetes.md).
  * Oprávnění "správce sestavení" a "projektový správce" pro [Azure Repos](/azure/devops/repos/get-started/what-is-repos) a [Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started).
* Nainstalujte následující Kubernetes rozšíření CLI s povoleným rozšířením Azure ARC verze >= 1.0.0:

  ```azurecli
  az extension add --name connectedk8s
  az extension add --name k8s-configuration
  ```
  * Chcete-li aktualizovat Tato rozšíření na nejnovější verzi, spusťte následující příkazy:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-configuration
    ```

## <a name="import-application-and-gitops-repos-into-azure-repos"></a>Import aplikací a úložišť GitOps do Azure Repos

Importujte [úložiště aplikace](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-ci-cd#application-repo) a [úložiště GitOps](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-ci-cd#gitops-repo) do Azure Repos. Pro tento kurz použijte následující příklad úložišť:

* **oblouk – cicd – demo –** úložiště aplikace src
   * Adresa URL https://github.com/Azure/arc-cicd-demo-src
   * Obsahuje ukázkovou aplikaci pro hlasování v Azure, kterou nasadíte pomocí GitOps.
* **oblouk – cicd-demo-gitops** Úložiště GitOps
   * Adresa URL https://github.com/Azure/arc-cicd-demo-gitops
   * Funguje jako základ pro prostředky clusteru, které jsou součástí aplikace hlasování Azure.

Přečtěte si další informace o [importu úložišť Git](/azure/devops/repos/git/import-git-repository).

>[!NOTE]
> Import a použití dvou samostatných úložišť pro úložiště aplikací a GitOps může zlepšit zabezpečení a jednoduchost. Oprávnění a viditelnost aplikace a GitOps úložiště je možné vyladit individuálně.
> Správce clusteru například nemusí najít změny v kódu aplikace, které jsou relevantní pro požadovaný stav clusteru. V opačném případě nemusí vývojář aplikace znát konkrétní parametry pro každé prostředí – sada testovacích hodnot, které zajišťují pokrytí parametrů, mohou být dostačující.

## <a name="connect-the-gitops-repo"></a>Připojení úložiště GitOps

Pokud chcete aplikaci průběžně nasadit, připojte úložiště aplikace k vašemu clusteru pomocí GitOps. Vaše **cicd úložiště ARC-gitops** gitops obsahuje základní prostředky pro zprovoznění vaší aplikace v clusteru **ARC-cicd-cluster** .

Počáteční úložiště GitOps obsahuje pouze [manifest](https://github.com/Azure/arc-cicd-demo-gitops/blob/master/arc-cicd-cluster/manifests/namespaces.yml) , který vytváří obory názvů pro **vývoj** a **přípravu** odpovídající prostředím nasazení.

Připojení GitOps, které vytvoříte, bude automaticky:
* Synchronizujte manifesty v adresáři manifestu.
* Aktualizujte stav clusteru.

Pracovní postup CI/CD naplní adresář manifestu dalšími manifesty k nasazení aplikace.


1. [Vytvořte nové připojení GitOps](./tutorial-use-gitops-connected-cluster.md) k nově importovanému úložišti **cicd-demo-GitOps** ve službě Azure Repos.

   ```azurecli
   az k8sconfiguration create \
      --name cluster-config \
      --cluster-name arc-cicd-cluster \
      --resource-group myResourceGroup \
      --operator-instance-name cluster-config \
      --operator-namespace cluster-config \
      --repository-url https://dev.azure.com/<Your organization>/arc-cicd-demo-gitops \
      --https-user <Azure Repos username> \
      --https-key <Azure Repos PAT token> \
      --scope cluster \
      --cluster-type connectedClusters \
      --operator-params='--git-readonly --git-path=arc-cicd-cluster/manifests'
   ```

1. Zajistěte  , aby tok používal `arc-cicd-cluster/manifests` jako základní cestu pouze adresář. Zadejte cestu pomocí následujícího parametru operátoru:

   `--git-path=arc-cicd-cluster/manifests`

   > [!NOTE]
   > Pokud používáte připojovací řetězec HTTPS a máte problémy s připojením, ujistěte se, že jste v adrese URL vynechi předponu uživatelského jména. Například `https://alice@dev.azure.com/contoso/arc-cicd-demo-gitops` je třeba `alice@` Odebrat. `--https-user`Místo toho Určuje uživatele, například `--https-user alice` .

1. Ověřte stav nasazení v Azure Portal.
   * V případě úspěchu se `dev` `stage` ve vašem clusteru zobrazí oba i obory názvů.

## <a name="import-the-cicd-pipelines"></a>Import kanálů CI/CD

Teď, když jste prosynchronizoval připojení GitOps, budete muset importovat kanály CI/CD, které vytvářejí manifesty.

Úložiště aplikace obsahuje `.pipeline` složku s kanály, které budete používat pro PR, CI a CD. Importujte a přejmenujte tři kanály poskytované v ukázkovém úložišti:

| Název souboru kanálu | Popis |
| ------------- | ------------- |
| [`.pipelines/az-vote-pr-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-pr-pipeline.yaml)  | Kanál PR aplikace s názvem **oblouk-cicd-demo-src PR** |
| [`.pipelines/az-vote-ci-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-ci-pipeline.yaml) | Kanál CI aplikace s názvem **oblouk-cicd-demo-src CI** |
| [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml) | Kanál disku CD aplikace s názvem **oblouk-cicd-demo-src CD** |



## <a name="connect-your-acr"></a>Připojení ACR
Oba kanály i cluster budou využívat ACR k ukládání a načítání imagí Docker.

### <a name="connect-acr-to-azure-devops"></a>Připojení ACR k Azure DevOps
Během procesu CI nasadíte kontejnery aplikace do registru. Začněte vytvořením připojení služby Azure:

1. V Azure DevOps otevřete stránku **připojení služby** ze stránky nastavení projektu. V sadě TFS otevřete stránku **služby** z ikony **Nastavení** v horním řádku nabídek.
2. Zvolte **+ nové připojení služby** a vyberte typ připojení služby, které potřebujete.
3. Zadejte parametry pro připojení služby. Pro tento kurz:
   * Pojmenujte ARC připojení služby **– demo-ACR**. 
   * Jako skupinu prostředků vyberte **myResourceGroup** .
4. Vyberte **oprávnění udělit přístup ke všem kanálům**. 
   * Tato možnost autorizuje soubory kanálu YAML pro připojení služby. 
5. Kliknutím na **tlačítko OK** vytvořte připojení.

### <a name="connect-acr-to-kubernetes"></a>Připojení ACR k Kubernetes
Umožněte clusteru Kubernetes vyžádat si image z vaší ACR. Pokud je privátní, bude vyžadováno ověřování.

#### <a name="connect-acr-to-existing-aks-clusters"></a>Připojení ACR k existujícím clusterům AKS

Integrujte stávající ACR s existujícími clustery AKS pomocí následujícího příkazu:

```azurecli
az aks update -n arc-cicd-cluster -g myResourceGroup --attach-acr arc-demo-acr
```

#### <a name="create-an-image-pull-secret"></a>Vytvoření bitové kopie pro vyžádání obsahu

Pokud chcete k ACR připojit jiné než AKS a místní clustery, vytvořte tajný klíč pro vyžádání image. Kubernetes používá k ukládání informací potřebných k ověření vašeho registru tajné klíče pro vyžádání obrazu.

Pomocí následujícího příkazu vytvořte tajný klíč pro vyžádání image `kubectl` . Opakujte pro `dev` `stage` obory názvů a.
```console
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<container-registry-name>.azurecr.io \
    --docker-username=<service-principal-ID> \
    --docker-password=<service-principal-password>
```

> [!TIP]
> Abyste se vyhnuli nutnosti nastavovat imagePullSecret pro všechny pod, zvažte přidání imagePullSecret k účtu služby v `dev` `stage` oborech názvů a. Další informace najdete v [kurzu Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#add-imagepullsecrets-to-a-service-account) .

## <a name="create-environment-variable-groups"></a>Vytvoření skupin proměnných prostředí

### <a name="app-repo-variable-group"></a>Skupina proměnných úložiště aplikace
[Vytvořte skupinu proměnných](/azure/devops/pipelines/library/variable-groups) s názvem **AZ-Hlasujte-App-dev**. Nastavte následující hodnoty:

| Proměnná | Hodnota |
| -------- | ----- |
| AZ_ACR_NAME | (například instance ACR. azurearctest.azurecr.io) |
| AZURE_SUBSCRIPTION | (vaše připojení služby Azure, které by mělo být **ARC – demo-ACR** z výše uvedeného kurzu) |
| AZURE_VOTE_IMAGE_REPO | Úplná cesta k úložišti hlasovacích aplikací Azure, například azurearctest.azurecr.io/azvote |
| ENVIRONMENT_NAME | Vývoj |
| MANIFESTS_BRANCH | `master` |
| MANIFESTS_REPO | Připojovací řetězec Git pro úložiště GitOps |
| PAT | [Vytvořený token Pat](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate#create-a-pat) s oprávněním ke čtení/zápisu ve zdroji. Uložte ho pro pozdější použití při vytváření `stage` skupiny proměnných. |
| SRC_FOLDER | `azure-vote` | 
| TARGET_CLUSTER | `arc-cicd-cluster` |
| TARGET_NAMESPACE | `dev` |

> [!IMPORTANT]
> Označte svou PAT jako tajný typ. V aplikacích zvažte propojení tajných kódů z [trezoru klíčů Azure](/azure/devops/pipelines/library/variable-groups#link-secrets-from-an-azure-key-vault).
>
### <a name="stage-environment-variable-group"></a>Skupina proměnných prostředí fáze

1. Naklonujte skupinu proměnných **AZ-Hlasujte-App-dev** .
1. Změňte název na **AZ-Hlasujte-App-Stage**.
1. Zajistěte, aby byly pro příslušné proměnné následující hodnoty:

| Proměnná | Hodnota |
| -------- | ----- |
| ENVIRONMENT_NAME | Fáze |
| TARGET_NAMESPACE | `stage` |

Teď jste připraveni nasadit do `dev` `stage` prostředí a.

## <a name="deploy-the-dev-environment-for-the-first-time"></a>První nasazení vývojového prostředí
Když jste vytvořili kanály CI a CD, spusťte při prvním nasazení aplikace kanál CI.

### <a name="ci-pipeline"></a>Kanál CI

Během spuštění počátečního kanálu CI se při čtení názvu připojení služby může zobrazit chyba autorizace prostředku.
1. Ověřte, zda je přistupovaná proměnná AZURE_SUBSCRIPTION.
1. Autorizovat použití.
1. Znovu spusťte kanál.

Kanál CI:
* Zajistí, aby změna aplikace prošla všemi automatizovanými kontrolami kvality pro nasazení.
* Provede jakékoli dodatečné ověření, které nebylo možné dokončit v kanálu žádosti o přijetí změn.
    * V rámci GitOps kanál také zveřejňuje artefakty pro potvrzení, které bude nasazeno kanálem CD.
* Ověří, že image Docker se změnila a že je vložen nový obrázek.

### <a name="cd-pipeline"></a>Kanál CD
Úspěšné spuštění kanálu CI aktivuje kanál CD, aby se dokončil proces nasazení. Nasadíte do každého prostředí přírůstkově.

> [!TIP]
> Pokud kanál CD automaticky neaktivuje:
> 1. Ověřte, že se název shoduje s triggerem větve v. [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml)
>    * Měla by mít hodnotu `arc-cicd-demo-src CI`.
> 1. Znovu spusťte kanál CI.

Jakmile vygenerujete šablonu a manifest do úložiště GitOps, kanál CD vytvoří potvrzení, nabídne ho a vytvoří žádost o přijetí změn ke schválení.
1. Otevřete odkaz na žádost o přijetí změn uvedený ve `Create PR` výstupu úlohy.
1. Ověřte změny v úložišti GitOps. Měli byste vidět tohle:
   * Změny šablony Helm na vysoké úrovni.
   * Kubernetes manifesty nízké úrovně, které zobrazují příslušné změny v požadovaném stavu. Tok nasadí tyto manifesty.
1. Pokud vše vypadá dobře, schvalte a dokončete TRVALOu žádost o přijetí změn.

1. Po několika minutách se tok změní a spustí se nasazení.
1. Předejte port místně pomocí `kubectl` a ujistěte se, že aplikace funguje správně pomocí:

   `kubectl port-forward -n dev svc/azure-vote-front 8080:80`

1. Podívejte se do aplikace pro hlasování Azure v prohlížeči na adrese `http://localhost:8080/` .

1. Hlasujte pro vaše oblíbené a připravte se na provedení některých změn v aplikaci.

## <a name="set-up-environment-approvals"></a>Nastavení schválení prostředí
Při nasazení aplikace nemůžete provádět změny pouze v kódu nebo šablonách, ale můžete také neúmyslně uvést cluster do špatného stavu.

Pokud vývojové prostředí odhalí přerušení po nasazení, ponechte ho v prostředí s použitím schválení prostředí.

1. V projektu Azure DevOps otevřete prostředí, které je potřeba chránit.
1. Přejděte ke **schválení a kontrolám** prostředku.
1. Vyberte **Vytvořit**.
1. Zadejte schvalovatele a volitelnou zprávu.
1. Vyberte **vytvořit** znovu a dokončete přidání kontroly ručního schválení.

Další podrobnosti najdete v kurzu [Definování schválení a kontroly](/azure/devops/pipelines/process/approvals) .

Při příštím spuštění kanálu CD se kanál pozastaví po vytvoření žádosti o přijetí změn (GitOps). Ověřte, že se změna správně synchronizovaná a předává základní funkce. Schvalte kontrolu z kanálu, aby se změna pokračovala v dalším prostředí.

## <a name="make-an-application-change"></a>Provedení změny aplikace

Pomocí této základní sady šablon a manifestů, které představují stav clusteru, provedete v aplikaci malou změnu.

1. V souboru **ARC-cicd-demo-src** úložiště upravte [`azure-vote/src/azure-vote-front/config_file.cfg`](https://github.com/Azure/arc-cicd-demo-src/blob/master/azure-vote/src/azure-vote-front/config_file.cfg) soubor.

2. Vzhledem k tomu, že "Kočky vs psi" nedosahují dostatečné hlasy, změňte ji na "tabulátory vs Spaces", aby bylo možné řídit počet hlasů.

3. Potvrďte změnu v nové větvi, dodejte ji a vytvořte žádost o přijetí změn.
   * Toto je typický tok pro vývojáře, který spustí životní cyklus CI/CD.

## <a name="pr-validation-pipeline"></a>Kanál ověření žádosti o přijetí změn

Kanál žádosti o přijetí změn je první linií obrany proti vadné změně. Běžné kontroly kvality kódu aplikace zahrnují linting a statickou analýzu. V perspektivě GitOps je také potřeba zajistit, aby se nasadila stejná kvalita pro výslednou infrastrukturu.

Grafy souboru Dockerfile a Helm aplikace mohou používat linting podobným způsobem jako aplikace.

Chyby nalezené během linting rozsahu:
* Nesprávně naformátované soubory YAML na
* Návrhy osvědčených postupů, jako je nastavení omezení procesoru a paměti pro vaši aplikaci.

> [!NOTE]
> Abyste dosáhli nejlepšího pokrytí z Helm linting v reálné aplikaci, budete muset nahradit hodnoty, které se přiměřeně podobají těm, které se používaly ve skutečném prostředí.

Chyby nalezené během spuštění kanálu se zobrazí v části výsledky testu v běhu. Odsud máte tyto možnosti:
* Sledujte užitečné statistiky typů chyb.
* Najde první potvrzení, na kterém byly zjištěny.
* Styl trasování zásobníku odkazuje na části kódu, které způsobily chybu.

Po dokončení běhu kanálu jste si jisti kvalitou kódu aplikace a šablony, která ji nasadí. Nyní můžete schválit a dokončit žádost o přijetí změn. CI se znovu spustí a znovu vygeneruje šablony a manifesty, než se aktivuje kanál CD.

> [!TIP]
> V reálném prostředí nezapomeňte nastavit zásady pro větev, aby bylo zajištěno, že žádost o přijetí změn projde vaše kontroly kvality. Další informace najdete v článku [nastavení zásad větví](/azure/devops/repos/git/branch-policies) .

## <a name="cd-process-approvals"></a>Schválení procesu CD

Úspěšné spuštění kanálu CI aktivuje kanál CD, aby se dokončil proces nasazení. Podobně jako při prvním pokusu o kanál CD-ROM můžete nasadit do každého prostředí přírůstkově. V tomto okamžiku kanál vyžaduje, abyste schválili každé prostředí nasazení.

1. Schvalte nasazení do `dev` prostředí.
1. Jakmile vygenerujete šablonu a manifest do úložiště GitOps, kanál CD vytvoří potvrzení, nabídne ho a vytvoří žádost o přijetí změn ke schválení.
1. Otevřete odkaz na žádost o přijetí změn uvedený v úkolu.
1. Ověřte změny v úložišti GitOps. Měli byste vidět tohle:
   * Změny šablony Helm na vysoké úrovni.
   * Kubernetes manifesty nízké úrovně, které zobrazují příslušné změny v požadovaném stavu.
1. Pokud vše vypadá dobře, schvalte a dokončete TRVALOu žádost o přijetí změn.
1. Počkejte, až se nasazení dokončí.
1. Jako základní test kouře přejděte na stránku aplikace a ověřte, že hlasovací aplikace teď zobrazuje tabulátory vs.
   * Předejte port místně pomocí `kubectl` a ujistěte se, že aplikace funguje správně pomocí: `kubectl port-forward -n dev svc/azure-vote-front 8080:80`
   * V prohlížeči si můžete zobrazit aplikaci pro hlasování Azure http://localhost:8080/ a ověřit, jestli se možnosti hlasování změnily na tabulátory a mezery. 
1. Opakujte kroky 1-7 pro `stage` prostředí.

Vaše nasazení je nyní dokončeno. Tím se ukončí pracovní postup CI/CD.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte všechny prostředky pomocí následujících kroků:

1. Odstraňte připojení konfigurace GitOps ARC Azure:
   ```azurecli
   az k8sconfiguration delete \
   --name cluster-config \
   --cluster-name arc-cicd-cluster \
   --resource-group myResourceGroup \
   --cluster-type connectedClusters
   ```

2. Odebrat `dev` obor názvů:
   * `kubectl delete namespace dev`

3. Odebrat `stage` obor názvů:
   * `kubectl delete namespace stage`

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nastavili kompletní pracovní postup CI/CD, který implementuje DevOps z vývoje aplikací prostřednictvím nasazení. Změny aplikace automaticky aktivují ověřování a nasazení, které jsou ověřované ručním schválením.

V našem koncepčním článku se dozvíte víc o GitOps a konfiguracích s povoleným Kubernetes ARC Azure.

> [!div class="nextstepaction"]
> [Pracovní postup CI/CD pomocí GitOps – Kubernetes s podporou ARC Azure](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-ci-cd)
