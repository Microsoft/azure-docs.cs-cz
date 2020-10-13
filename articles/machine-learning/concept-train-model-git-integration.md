---
title: Integrace Gitu pro Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Přečtěte si, jak Azure Machine Learning integrovat s místním úložištěm Git. Při odesílání školicích běhů z místního adresáře, který je úložištěm Git, se jako součást běhu sledují informace o úložišti, větvi a aktuálním potvrzení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.openlocfilehash: 71ac7793fe5226215c5d4eab98f84dba356b114c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91275961"
---
# <a name="git-integration-for-azure-machine-learning"></a>Integrace Gitu pro Azure Machine Learning

[Git](https://git-scm.com/) je oblíbený systém pro správu verzí, který umožňuje sdílení a spolupráci na vašich projektech. 

Azure Machine Learning plně podporuje úložiště Git pro sledování práce – úložiště můžete klonovat přímo do sdíleného systému souborů pracovních prostorů, použít Git na místní pracovní stanici nebo použít git z kanálu CI/CD.

Při odesílání úlohy do Azure Machine Learning, pokud jsou zdrojové soubory uložené v místním úložišti Git, informace o úložišti se sledují jako součást procesu školení.

Vzhledem k tomu, že Azure Machine Learning sleduje informace z místního úložiště Git, není svázán s žádným konkrétním centrálním úložištěm. Vaše úložiště je možné klonovat z GitHubu, GitLab, BitBucket, Azure DevOps nebo jakékoli jiné služby kompatibilní s Git.

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Klonování úložišť Git do systému souborů vlastního pracovního prostoru
Azure Machine Learning poskytuje sdílený systém souborů pro všechny uživatele v pracovním prostoru.
K naklonování úložiště Git do této sdílené složky doporučujeme vytvořit výpočetní instanci & otevřete terminál.
Po otevření terminálu máte přístup k plnému klientovi Git a budete moct pomocí prostředí Git CLI klonovat a pracovat s ním.

Doporučujeme, abyste naklonoval úložiště do adresáře uživatelů, aby ostatní nedošlo k kolizím přímo ve vaší pracovní větvi.

Můžete klonovat jakékoli úložiště Git, na které můžete ověřovat (GitHub, Azure Repos, BitBucket atd.).

Další informace o klonování najdete v příručce k použití rozhraní příkazového [řádku Git](https://guides.github.com/introduction/git-handbook/).

## <a name="authenticate-your-git-account-with-ssh"></a>Ověření účtu Git pomocí SSH
### <a name="generate-a-new-ssh-key"></a>Vygenerovat nový klíč SSH
1) [Otevřete okno terminálu](https://docs.microsoft.com/azure/machine-learning/how-to-run-jupyter-notebooks#terminal) na kartě Poznámkový blok Azure Machine Learning.

2) Níže vložte text a nahraďte svou e-mailovou adresu.

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

Tím se vytvoří nový klíč SSH s použitím poskytnutého e-mailu jako popisku.

```
> Generating public/private rsa key pair.
```

3) Po zobrazení výzvy k zadání souboru, ve kterém se má klíč uložit, stiskněte klávesu ENTER. Tím se přijme výchozí umístění souboru.

4) Ověřte, zda je výchozí umístění "/Home/azureuser/.ssh" a stiskněte klávesu ENTER. V opačném případě zadejte umístění '/Home/azureuser/.ssh '.

> [!TIP]
> Ujistěte se, že je klíč SSH uložený v '/Home/azureuser/.ssh '. Tento soubor je uložený na výpočetní instanci je dostupný jenom vlastníkem výpočetní instance.

```
> Enter a file in which to save the key (/home/azureuser/.ssh/id_rsa): [Press enter]
```

5) Na příkazovém řádku zadejte zabezpečené heslo. Pro zvýšení zabezpečení doporučujeme pro svůj klíč SSH přidat přístupové heslo.

```
> Enter passphrase (empty for no passphrase): [Type a passphrase]
> Enter same passphrase again: [Type passphrase again]
```

### <a name="add-the-public-key-to-git-account"></a>Přidání veřejného klíče do účtu Git
1) V okně terminálu zkopírujte obsah souboru veřejného klíče. Pokud jste přejmenovali klíč, nahraďte id_rsa. pub názvem souboru veřejného klíče.

```bash
cat ~/.ssh/id_rsa.pub
```
> [!TIP]
> **Kopírování a vkládání v terminálu**
> * Windows: `Ctrl-Insert` kopírování a používání `Ctrl-Shift-v` nebo `Shift-Insert` vložení.
> * Mac OS: `Cmd-c` pro kopírování a `Cmd-v` vložení.
> * Aplikace FireFox/IE nemusí správně podporovat oprávnění schránky.

2) Vyberte a zkopírujte výstup klíče ve schránce.

+ [GitHub](https://docs.github.com/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account)

+ [GitLab](https://docs.gitlab.com/ee/ssh/#adding-an-ssh-key-to-your-gitlab-account)

+ [DevOps Azure](https://docs.microsoft.com/azure/devops/repos/git/use-ssh-keys-to-authenticate?view=azure-devops#step-2--add-the-public-key-to-azure-devops-servicestfs)  Začněte v **kroku 2**.

+ [Bitbucket](https://support.atlassian.com/bitbucket-cloud/docs/set-up-an-ssh-key/#SetupanSSHkey-ssh2). Začněte v **kroku 4**.

### <a name="clone-the-git-repository-with-ssh"></a>Klonování úložiště Git pomocí SSH

1) Zkopírujte adresu URL klonu SSH git z úložiště Git.

2) Vložte adresu URL do `git clone` následujícího příkazu, aby bylo možné použít adresu URL úložiště Git SSH. To bude vypadat přibližně takto:

```bash
git clone git@example.com:GitUser/azureml-example.git
Cloning into 'azureml-example'...
```

Zobrazí se odpověď, třeba:

```bash
The authenticity of host 'example.com (192.30.255.112)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,192.30.255.112' (RSA) to the list of known hosts.
```

SSH může zobrazit otisk prstu serveru SSH a požádat ho o ověření. Měli byste ověřit, že zobrazený otisk prstu odpovídá jednomu otisku prstu na stránce veřejných klíčů SSH.

SSH zobrazí tento otisk prstu, když se připojí k neznámému hostiteli, aby vás chránil před [útoky](https://technet.microsoft.com/library/cc959354.aspx)prostředníkem. Až přijměte otisk prstu hostitele, SSH se znovu nezobrazí, pokud se otisk prstu nezmění.

3) Až se zobrazí dotaz, jestli chcete pokračovat v připojování, zadejte `yes` . Git naklonuje úložiště a nastaví počáteční vzdálenou aplikaci pro připojení k SSH pro budoucí příkazy Gitu.

## <a name="track-code-that-comes-from-git-repositories"></a>Sledovat kód, který pochází z úložišť Git

Když odešlete školicí běh ze sady Python SDK nebo Machine Learning CLI, soubory potřebné pro výuku modelu se nahrají do vašeho pracovního prostoru. Pokud `git` je příkaz k dispozici ve vašem vývojovém prostředí, proces nahrávání je používá ke kontrole, zda jsou soubory uloženy v úložišti Git. V takovém případě jsou informace z úložiště Git také nahrány v rámci školicího běhu. Tyto informace jsou uloženy v následujících vlastnostech pro školicí běh:

| Vlastnost | Příkaz git použitý k získání hodnoty | Description |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | Identifikátor URI, ze kterého bylo úložiště naklonováno. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | Identifikátor URI, ze kterého bylo úložiště naklonováno. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | Aktivní větev při odeslání běhu. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | Aktivní větev při odeslání běhu. |
| `azureml.git.commit` | `git rev-parse HEAD` | Hodnota hash potvrzení kódu, který byl odeslán pro spuštění. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | Hodnota hash potvrzení kódu, který byl odeslán pro spuštění. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`, pokud je větev/potvrzení nečistá; v opačném případě `false` . |

Tyto informace se odesílají pro běhy, které používají Estimator, kanál strojového učení nebo spuštění skriptu.

Pokud vaše školicí soubory nejsou umístěny v úložišti Git ve vašem vývojovém prostředí nebo `git` příkaz není k dispozici, nebudou sledovány žádné informace týkající se Gitu.

> [!TIP]
> Pokud chcete zjistit, jestli je příkaz git ve vývojovém prostředí dostupný, otevřete relaci prostředí, příkazový řádek, PowerShell nebo jiné rozhraní příkazového řádku a zadejte tento příkaz:
>
> ```
> git --version
> ```
>
> V případě instalace a v cestě obdržíte odpověď podobnou této `git version 2.4.1` . Další informace o instalaci Gitu do vývojového prostředí najdete na [webu Git](https://git-scm.com/).

## <a name="view-the-logged-information"></a>Zobrazit protokolované informace

Informace Git se ukládají do vlastností pro školicí běh. Tyto informace můžete zobrazit pomocí Azure Portal, sady Python SDK a rozhraní příkazového řádku. 

### <a name="azure-portal"></a>portál Azure

1. Na [portálu Studio](https://ml.azure.com)vyberte svůj pracovní prostor.
1. Vyberte __experimenty__a pak vyberte jeden z experimentů.
1. Vyberte jedno z běhů ze sloupce __číslo běhu__ .
1. Vyberte možnost __výstupy + protokoly__a potom rozbalte položku __protokoly__ a položky __AzureML__ . Vyberte odkaz, který začíná na __ ### \_ Azure__.

Protokolované informace obsahují text podobný následujícímu formátu JSON:

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>Python SDK

Po odeslání školicího běhu se vrátí objekt [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true) . `properties`Atribut tohoto objektu obsahuje protokolované informace Gitu. Například následující kód načte hodnotu hash potvrzení:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>Rozhraní příkazového řádku

`az ml run`Příkaz CLI lze použít k načtení vlastností z běhu. Například následující příkaz vrátí vlastnosti posledního spuštění v experimentu s názvem `train-on-amlcompute` :

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Další informace najdete v referenční dokumentaci [AZ ml Run](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest&preserve-view=true) reference.

## <a name="next-steps"></a>Další kroky

* [Použití výpočetních cílů pro školení modelů](how-to-set-up-training-targets.md)
