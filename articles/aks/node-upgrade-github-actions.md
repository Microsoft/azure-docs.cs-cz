---
title: Zpracování upgradů uzlů AKS pomocí akcí GitHubu
titleSuffix: Azure Kubernetes Service
description: Naučte se aktualizovat uzly AKS pomocí akcí GitHubu.
services: container-service
ms.topic: article
ms.date: 11/27/2020
ms.openlocfilehash: 7a24911fd771663c7edbbdf0c8d2d763a74fc586
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96535186"
---
# <a name="apply-security-updates-to-azure-kubernetes-service-aks-nodes-automatically-using-github-actions"></a>Použití aktualizací zabezpečení u uzlů AKS (Azure Kubernetes Service) automaticky pomocí akcí GitHubu

Aktualizace zabezpečení jsou klíčovou součástí údržby zabezpečení clusteru AKS a dodržování předpisů pomocí nejnovějších oprav pro základní operační systém. Tyto aktualizace zahrnují opravy zabezpečení operačního systému nebo aktualizace jádra. Některé aktualizace vyžadují dokončení procesu restartováním uzlu.

Spuštění `az aks upgrade` přináší nezpůsobení nečinnosti při použití aktualizací. Příkaz zpracuje nejnovější aktualizace na všech uzlech clusteru, cordoningí a vyprázdnění provozu do uzlů a restartuje uzly a pak umožní provoz do aktualizovaných uzlů. Pokud uzly aktualizujete pomocí jiné metody, AKS nebude automaticky restartovat vaše uzly.

> [!NOTE]
> Hlavní rozdíl mezi `az aks upgrade` při použití s `--node-image-only` příznakem je, že při jeho použití budou upgradovány pouze image uzlů. Je-li tento parametr vynechán, bude upgradována jak image uzlů, tak verze řídicí roviny ovládacího prvku Kubernetes. Podrobnější informace najdete v dokumentech [pro spravované upgrady na uzlech][managed-node-upgrades-article] a v [dokumentaci pro upgrady clusteru][cluster-upgrades-article] .

Všechny uzly Kubernetes běží na standardním virtuálním počítači Azure (VM). Tyto virtuální počítače mohou být založené na systému Windows nebo Linux. Virtuální počítače se systémem Linux používají image Ubuntu s operačním systémem nakonfigurovaným k automatickému vyhledávání aktualizací v každé noci.

Když použijete `az aks upgrade` příkaz, Azure CLI vytvoří nárůst počtu nových uzlů s nejnovějšími aktualizacemi zabezpečení a jádra, tyto uzly se zpočátku uzavřené, aby se zabránilo jejich naplánování na tyto aplikace, dokud se aktualizace nedokončí. Po dokončení Azure cordons (zpřístupní uzel pro plánování nových úloh) a vyprázdní (přesune existující úlohy do jiného uzlu) starší uzly a uncordon nové a efektivně přenáší všechny naplánované aplikace do nových uzlů.

Tento proces je lepší než ruční aktualizace jader na bázi systému Linux, protože Linux vyžaduje restart při instalaci nové aktualizace jádra. Pokud ručně aktualizujete operační systém, musíte restartovat virtuální počítač, ručně cordoning a vyprázdnit všechny aplikace.

V tomto článku se dozvíte, jak můžete automatizovat proces aktualizace uzlů AKS. Pomocí akcí GitHubu a Azure CLI vytvoříte úlohu aktualizace na základě toho `cron` , že se spustí automaticky.

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].

Potřebujete také nainstalované a nakonfigurované rozhraní Azure CLI verze 2.0.59 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

Tento článek také předpokládá, že máte účet [GitHub][github] , ve kterém můžete vytvářet vaše akce.

## <a name="create-a-timed-github-action"></a>Vytvoření časované akce GitHubu

`cron` je nástroj, který umožňuje spustit sadu příkazů nebo úlohy podle automatizovaného plánu. K vytvoření úlohy pro aktualizaci uzlů AKS na automatizovaném plánu budete potřebovat úložiště, abyste mohli své akce hostovat. Akce GitHubu se obvykle konfigurují ve stejném úložišti jako vaše aplikace, ale můžete použít libovolné úložiště. V tomto článku budeme používat vaše [úložiště profilů][profile-repository]. Pokud ho ještě nemáte, vytvořte nové úložiště se stejným názvem, jako má vaše uživatelské jméno GitHubu.

1. Přechod do úložiště na GitHubu
1. Klikněte na kartu **Akce** v horní části stránky.
1. Pokud jste již v tomto úložišti nastavili pracovní postup, budete přesměrováni na seznam dokončených spuštění. v tomto případě klikněte na tlačítko **nový pracovní postup** . Pokud se jedná o váš první pracovní postup v úložišti, GitHub vám nabídne některé šablony projektu, klikněte na odkaz **nastavit pracovní postup sami** pod textem popisu.
1. Změňte pracovní postup `name` a `on` značky podobně jako na následujícím obrázku. Akce GitHubu používají stejnou [syntaxi POSIX cron][cron-syntax] jako jakýkoli systém na platformě Linux. V tomto plánu oznamujeme, že pracovní postup se spouští každých 15 dnů na 3am.

    ```yml
    name: Upgrade cluster node images
    on:
      schedule:
        - cron: '0 3 */15 * *'
    ```

1. Pomocí následujícího příkladu vytvořte novou úlohu. Tato úloha se jmenuje `upgrade-node` , běží na agentovi Ubuntu a připojí se k vašemu účtu Azure CLI, aby provedl potřebný postup k upgradu uzlů.

    ```yml
    name: Upgrade cluster node images

    on:
      schedule:
        - cron: '0 3 */15 * *'

    jobs:
      upgrade-node:
        runs-on: ubuntu-latest
    ```

## <a name="set-up-the-azure-cli-in-the-workflow"></a>Nastavení rozhraní příkazového řádku Azure CLI v pracovním postupu

V `steps` klíči definujte veškerou práci, kterou pracovní postup spustí pro upgrade uzlů.

Stáhněte si a přihlaste se do Azure CLI.

1. Na pravé straně obrazovky akce GitHubu Najděte *panel hledání na webu Marketplace* a zadejte **"přihlášení do Azure"**.
1. V důsledku toho se zobrazí akce nazvané **přihlášení Azure** publikované **v Azure**:

      :::image type="content" source="media/node-upgrade-github-actions/azure-login-search.png" alt-text="Výsledky hledání zobrazující dva řádky: první akce se nazývá Azure login a druhá ' Azure Container Registry Login":::

1. Klikněte na **přihlášení Azure**. Na další obrazovce klikněte v pravém horním rohu ukázka kódu na **ikonu Kopírovat** .

    :::image type="content" source="media/node-upgrade-github-actions/azure-login.png" alt-text="Podokno výsledků akce přihlášení do Azure s ukázkou kódu níže, červený čtverec kolem ikony kopírování zvýrazní místo kliknutí.":::

1. Do klíče vložte následující `steps` :

      ```yml
      name: Upgrade cluster node images

      on:
        schedule:
          - cron: '0 3 */15 * *'

      jobs:
        upgrade-node:
          runs-on: ubuntu-latest

          steps:
            - name: Azure Login
              uses: Azure/login@v1.1
              with:
                creds: ${{ secrets.AZURE_CREDENTIALS }}
      ```

1. V Azure CLI spusťte následující příkaz, který vygeneruje nové uživatelské jméno a heslo.

    ```azurecli-interactive
    az ad sp create-for-rbac -o json
    ```

    Výstup by měl být podobný následujícímu formátu JSON:

    ```output
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-xxxx-xx-xx-xx-xx-xx",
      "name": "http://azure-cli-xxxx-xx-xx-xx-xx-xx",
      "password": "xXxXxXxXx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. **V novém okně prohlížeče** přejděte do úložiště GitHub a otevřete kartu **Nastavení** úložiště. Klikněte na **tajné klíče** a pak klikněte na **nový tajný klíč úložiště**.
1. Pro *název* použijte `AZURE_CREDENTIALS` .
1.  V poli *hodnota* přidejte celý obsah z výstupu předchozího kroku, kde jste vytvořili nové uživatelské jméno a heslo.

    :::image type="content" source="media/node-upgrade-github-actions/azure-credential-secret.png" alt-text="Formulář zobrazující AZURE_CREDENTIALS jako tajný kód a výstup spouštěného příkazu vloženého jako JSON":::

1. Klikněte na **Přidat tajný klíč**.

Rozhraní příkazového řádku, které vaše akce používá, se zaprotokoluje do vašeho účtu Azure a bude připravené ke spuštění příkazů.

K vytvoření kroků pro provedení příkazů rozhraní příkazového řádku Azure

1. Přejděte na **stránku vyhledávání** na *webu GitHub Marketplace* na pravé straně obrazovky a vyhledejte *akci Azure CLI*. Vyberte *akci Azure CLI podle Azure*.

    :::image type="content" source="media/node-upgrade-github-actions/azure-cli-action.png" alt-text="Výsledek hledání pro &quot;Azure CLI Action&quot; s prvním výsledkem zobrazeným v Azure":::

1. Klikněte na tlačítko Kopírovat na *webu GitHub Marketplace výsledek* a vložte obsah akce v hlavním editoru pod krok *přihlášení do Azure* , podobně jako v následujícím příkladu:

    ```yml
    name: Upgrade cluster node images

    on:
      schedule:
        - cron: '0 3 */15 * *'

    jobs:
      upgrade-node:
        runs-on: ubuntu-latest

        steps:
          - name: Azure Login
            uses: Azure/login@v1.1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
          - name: Upgrade node images
            uses: Azure/cli@v1.0.0
            with:
              inlineScript: az aks upgrade -g {resourceGroupName} -n {aksClusterName} --node-image-only
    ```

    > [!TIP]
    > Parametry a můžete oddělit `-g` pomocí `-n` příkazu jejich přidáním k tajným klíčům podobným předchozím krokům. `{resourceGroupName}`Zástupné symboly a nahraďte `{aksClusterName}` jejich tajnými protějšky, například `${{secrets.RESOURCE_GROUP_NAME}}` a`${{secrets.AKS_CLUSTER_NAME}}`

1. Přejmenujte soubor na `upgrade-node-images`.
1. Klikněte na tlačítko **Spustit potvrzení**, přidat název zprávy a uložte pracovní postup.

Po vytvoření potvrzení bude pracovní postup uložen a připraven ke spuštění.

> [!NOTE]
> Chcete-li upgradovat jeden fond uzlů místo všech fondů uzlů v clusteru, přidejte do `--name` příkazu parametr a `az aks nodepool upgrade` Zadejte název fondu uzlů. Příklad:
> ```
> inlineScript: az aks nodepool upgrade -g {resourceGroupName} --cluster-name {aksClusterName} --name {{nodePoolName}} --node-image-only
> ```

## <a name="run-the-github-action-manually"></a>Ruční spuštění akce GitHubu

Pracovní postup můžete spustit také ručně a naplánovaným spuštěním přidáním nové `on` triggeru `workflow_dispatch` . Hotový soubor by měl vypadat jako v následujícím YAML:

```yml
name: Upgrade cluster node images

on:
  schedule:
    - cron: '0 3 */15 * *'
  workflow_dispatch:

jobs:
  upgrade-node:
    runs-on: ubuntu-latest

    steps:
      - name: Azure Login
        uses: Azure/login@v1.1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      # Code for upgrading one or more node pools
```

## <a name="next-steps"></a>Další kroky

- Informace o nejnovějších imagích uzlů najdete v [poznámkách k vydání verze AKS](https://github.com/Azure/AKS/releases) .
- Přečtěte si, jak upgradovat verzi Kubernetes pomocí [upgradu clusteru AKS][cluster-upgrades-article].
- Přečtěte si další informace o více fondech uzlů a o tom, jak upgradovat fondy uzlů pomocí [vytváření a správy fondů více uzlů][use-multiple-node-pools].
- Další informace o [fondech uzlů systému][system-pools]
- Informace o tom, jak ušetřit náklady pomocí instancí s přímým odkazem, najdete v tématu [Přidání fondu bodových uzlů do AKS][spot-pools] .

<!-- LINKS - external -->
[github]: https://github.com
[profile-repository]: https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-github-profile/about-your-profile
[cron-syntax]: https://pubs.opengroup.org/onlinepubs/9699919799/utilities/crontab.html#tag_20_25_07

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[managed-node-upgrades-article]: node-image-upgrade.md
[cluster-upgrades-article]: upgrade-cluster.md
[system-pools]: use-system-pools.md
[spot-pools]: spot-node-pool.md
[use-multiple-node-pools]: use-multiple-node-pools.md
