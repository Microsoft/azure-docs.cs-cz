---
title: Kurz – spuštění image sestavení buildu pomocí soukromé základní image aktualizace
description: V tomto kurzu nakonfigurujete úlohu Azure Container Registry pro automatické spouštění sestavení imagí kontejneru v cloudu, když se aktualizuje základní image v jiném privátním registru služby Azure Container Registry.
ms.topic: tutorial
ms.date: 11/20/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 27ab7c3fc0f04023c32cfac181d8f8650de23560
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772356"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-another-private-container-registry"></a>Kurz: automatizace sestavení imagí kontejneru při aktualizaci základní image v jiném privátním registru kontejneru 

[ACR úlohy](container-registry-tasks-overview.md) podporují automatizované sestavení imagí při [aktualizaci základní image](container-registry-tasks-base-images.md)kontejneru, například při opravě operačního systému nebo rozhraní aplikace v jedné z vašich základních imagí. 

V tomto kurzu se naučíte, jak vytvořit úlohu ACR, která aktivuje sestavení v cloudu, když se základní image kontejneru odešle do jiného služby Azure Container Registry. Můžete také vyzkoušet kurz vytvoření úlohy ACR, která spustí sestavení image, když se do [stejného registru kontejnerů Azure](container-registry-tutorial-base-image-update.md)vloží základní image.

V tomto kurzu:

> [!div class="checklist"]
> * Sestavení základní image v základním registru
> * Vytvoření úlohy sestavení aplikace v jiném registru pro sledování základní image 
> * Aktualizovat základní image tak, aby aktivovala úlohu image aplikace
> * Zobrazit aktivovanou úlohu
> * Ověřit aktualizovanou image aplikace

## <a name="prerequisites"></a>Požadavky

### <a name="complete-the-previous-tutorials"></a>Dokončení předchozích kurzů

V tomto kurzu se předpokládá, že jste už nakonfigurovali své prostředí a dokončili jste postup v prvním dvou kurzech v řadě, ve kterých jste:

* Vytvoření registru kontejneru Azure
* Vytvoření forku ukázkového úložiště
* Klonování ukázkového úložiště
* Vytvoření tokenu PAT GitHubu

Pokud jste to ještě neudělali, před pokračováním dokončete následující kurzy:

[Sestavení imagí kontejnerů v cloudu pomocí Azure Container Registry Tasks](container-registry-tutorial-quick-task.md)

[Automatizace sestavení imagí kontejnerů pomocí Azure Container Registry Tasks](container-registry-tutorial-build-task.md)

Kromě registru kontejneru vytvořeného pro předchozí kurzy je třeba vytvořit registr pro uložení základních imagí. Pokud chcete, vytvořte druhý registr v jiném umístění než původní registr.

### <a name="configure-the-environment"></a>Konfigurace prostředí

Tyto proměnné prostředí naplňte hodnotami vhodnými pro vaše prostředí. Tento krok není nezbytně nutný, ale usnadní provádění víceřádkových příkazů Azure CLI v tomto kurzu. Pokud tyto proměnné prostředí neplníte, je nutné ručně nahradit každou hodnotu, pokud se zobrazí v ukázkových příkazech.

```azurecli
BASE_ACR=<base-registry-name>   # The name of your Azure container registry for base images
ACR_NAME=<registry-name>        # The name of your Azure container registry for application images
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```

### <a name="base-image-update-scenario"></a>Scénář aktualizace základní image

Tento kurz vás provede scénářem aktualizace základní image. Tento scénář odráží pracovní postup vývoje pro správu základních imagí v rámci společného soukromého registru kontejnerů při vytváření imagí aplikace v jiných registrech. Základní image mohou určovat běžné operační systémy a architektury používané týmem nebo dokonce i běžné součásti služby.

Například vývojáři, kteří vyvíjí image aplikace v jejich vlastních registrech, mají přístup k sadě základních imagí udržovaných v rámci společného základního registru. Základní registr může být v jiné oblasti nebo i geograficky replikovaný.

[Vzorový kód][code-sample] zahrnuje dva soubory Dockerfile: image aplikace a image, na které je založená. V následujících částech vytvoříte úlohu ACR, která automaticky aktivuje sestavení image aplikace, když je nová verze základní image vložena do jiného služby Azure Container Registry.

* [Dockerfile-app][dockerfile-app]: Jedná se o malou webovou aplikaci Node.js, která vykreslí statickou webovou stránku zobrazující verzi Node.js, na které je založena. Řetězec verze je simulovaný: zobrazuje obsah proměnné prostředí `NODE_VERSION`, která je definovaná v základní imagi.

* [Dockerfile-base][dockerfile-base]: Image, kterou `Dockerfile-app` specifikuje jako svou základní image. Je založená na imagi [Node][base-node] a zahrnuje proměnnou prostředí `NODE_VERSION`.

V následujících částech vytvoříte úlohu, aktualizujete hodnotu `NODE_VERSION` v souboru Dockerfile základní image a potom použijete ACR Tasks k sestavení základní image. Když úloha ACR odešle do registru novou základní image, automaticky aktivuje sestavení image aplikace. Volitelně můžete spustit image kontejneru aplikace místně, abyste se mohli podívat na různé řetězce verze v sestavených imagích.

V tomto kurzu vaše úloha ACR sestaví a nahraje image kontejneru aplikace zadanou v souboru Dockerfile. ACR úlohy mohou také spouštět [úlohy s více kroky](container-registry-tasks-multi-step.md), pomocí souboru YAML k definování kroků pro sestavení, vložení a volitelně testování více kontejnerů.

## <a name="build-the-base-image"></a>Sestavit základní image

Začněte vytvořením základní Image pomocí *rychlého úkolu* ACR Tasks pomocí [AZ ACR Build][az-acr-build]. Jak je popsáno v [prvním kurzu](container-registry-tutorial-quick-task.md) série, tímto postupem se nejen sestaví image, ale v případě úspěšného sestavení se odešle do registru kontejneru. V tomto příkladu je obrázek přesunut do registru základní image.

```azurecli
az acr build --registry $BASE_ACR --image baseimages/node:15-alpine --file Dockerfile-base .
```

## <a name="create-a-task-to-track-the-private-base-image"></a>Vytvoření úlohy ke sledování privátní základní image

V dalším kroku vytvořte úlohu v registru imagí aplikace pomocí [AZ ACR Task Create][az-acr-task-create]a povolte [spravovanou identitu](container-registry-tasks-authentication-managed-identity.md). Spravovaná identita se používá v pozdějších krocích, takže se úkol ověřuje pomocí registru základní image. 

V tomto příkladu se používá identita přiřazená systémem, ale můžete pro určité scénáře vytvořit a povolit spravovanou identitu přiřazenou uživatelem. Podrobnosti najdete v tématu [ověřování mezi registry v úloze ACR pomocí identity spravované službou Azure](container-registry-tasks-cross-registry-authentication.md).

```azurecli
az acr task create \
    --registry $ACR_NAME \
    --name baseexample2 \
    --image helloworld:{{.Run.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git#main \
    --file Dockerfile-app \
    --git-access-token $GIT_PAT \
    --arg REGISTRY_NAME=$BASE_ACR.azurecr.io \
    --assign-identity
```

Tato úloha se podobá úkolu vytvořenému v [předchozím kurzu](container-registry-tutorial-build-task.md). Dává službě ACR Tasks pokyn aktivovat sestavení image, když se do úložiště určeného parametrem `--context` odešlou potvrzení. Zatímco souboru Dockerfile použitý k sestavení image v předchozím kurzu určuje veřejnou základní Image ( `FROM node:15-alpine` ), souboru Dockerfile v této úloze, [souboru Dockerfile-App][dockerfile-app], určí základní image v registru základní Image:

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:15-alpine
```

Tato konfigurace usnadňuje simulaci opravy architektury v základní imagi později v tomto kurzu.

## <a name="give-identity-pull-permissions-to-base-registry"></a>Udělení oprávnění pro získání identity základnímu registru

Pokud chcete, aby spravovaná oprávnění této úlohy vyčetla image z registru základní image, nejdřív spusťte [AZ ACR Task show][az-acr-task-show] a Získejte ID objektu služby identity. Pak spusťte příkaz [AZ ACR show][az-acr-show] , který získá ID prostředku základního registru:

```azurecli
# Get service principal ID of the task
principalID=$(az acr task show --name baseexample2 --registry $ACR_NAME --query identity.principalId --output tsv) 

# Get resource ID of the base registry
baseregID=$(az acr show --name $BASE_ACR --query id --output tsv) 
```
 
Přiřaďte k registru oprávnění vyžádané replikace spravované identity spuštěním funkce [AZ role Assignment Create][az-role-assignment-create]:

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID --role acrpull 
```

## <a name="add-target-registry-credentials-to-the-task"></a>Přidat do úlohy přihlašovací údaje cílového registru

Spuštěním [AZ ACR Task Credential Add][az-acr-task-credential-add] přidejte do úlohy přihlašovací údaje. Předáním `--use-identity [system]` parametru označíte, že spravovaná identita přiřazená systémem úkolu má přístup k přihlašovacím údajům.

```azurecli
az acr task credential add \
  --name baseexample2 \
  --registry $ACR_NAME \
  --login-server $BASE_ACR.azurecr.io \
  --use-identity [system] 
```

## <a name="manually-run-the-task"></a>Ručně spustit úlohu

K ruční aktivaci úlohy a sestavení image aplikace použijte [příkaz AZ ACR Task Run][az-acr-task-run] . Tento krok je potřeba, aby úkol sledovat závislost image aplikace na základní imagi.

```azurecli
az acr task run --registry $ACR_NAME --name baseexample2
```

Jakmile se úloha dokončí, poznamenejte si **ID spuštění** (například „da6“), pokud chcete provést následující volitelný krok.

### <a name="optional-run-application-container-locally"></a>Volitelné: Spuštění kontejneru aplikace místně

Pokud pracujete místně (nejste v Cloud Shellu) a máte nainstalovaný Docker, spusťte kontejner, abyste zobrazili aplikaci vykreslenou ve webovém prohlížeči dříve, než opětovně sestavíte její základní image. Pokud Cloud Shell používáte, tuto část přeskočte (Cloud Shell nepodporuje `az acr login` ani `docker run`).

Nejprve proveďte ověření ve vašem registru kontejneru pomocí [AZ ACR Login][az-acr-login]:

```azurecli
az acr login --name $ACR_NAME
```

Teď pomocí `docker run` spusťte kontejner místně. Nahraďte **\<run-id\>** ID spuštění, které najdete ve výstupu z předchozího kroku (například "DA6"). Tento příklad pojmenuje kontejner `myapp` a obsahuje `--rm` parametr pro odebrání kontejneru při jeho zastavení.

```bash
docker run -d -p 8080:80 --name myapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

V prohlížeči přejděte na `http://localhost:8080`. Měli byste vidět číslo verze Node.js vykreslené na webové stránce. Mělo by vypadat takto. V dalším kroku změníte verzi tak, že do řetězce verze přidáte znak „a“.

:::image type="content" source="media/container-registry-tutorial-base-image-update/base-update-01.png" alt-text="Snímek obrazovky ukázkové aplikace v prohlížeči":::

Chcete-li zastavit a odebrat kontejner, spusťte následující příkaz:

```bash
docker stop myapp
```

## <a name="list-the-builds"></a>Výpis sestavení

Dále pomocí příkazu [az acr task list-runs][az-acr-task-list-runs] vypište spuštění úloh, která služba ACR Tasks dokončila pro váš registr:

```azurecli
az acr task list-runs --registry $ACR_NAME --output table
```

Pokud jste dokončili předchozí kurz (a nevymazali jste registr), měli byste vidět výstup podobný následujícímu. Poznamenejte si počet spuštění úloh a ID nejnovějšího spuštění, abyste je mohli porovnat s výstupem po aktualizaci základní image, kterou provedete v další části.

```console
$ az acr task list-runs --registry $ACR_NAME --output table

UN ID    TASK            PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
ca12      baseexample2    linux       Succeeded  Manual        2020-11-21T00:00:56Z  00:00:36
ca11      baseexample1    linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:34
ca10      taskhelloworld  linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:24
cay                       linux       Succeeded  Manual        2020-11-20T23:38:08Z  00:00:22
cax       baseexample1    linux       Succeeded  Manual        2020-11-20T23:33:12Z  00:00:30
caw       taskhelloworld  linux       Succeeded  Commit        2020-11-20T23:16:07Z  00:00:29
```

## <a name="update-the-base-image"></a>Aktualizace základní image

V tomto kroku budete simulovat opravu architektury v základní imagi. Upravte **Dockerfile-base** a za číslo verze definované v `NODE_VERSION` přidejte znak „a“:

```Dockerfile
ENV NODE_VERSION 15.2.1a
```

Spusťte rychlou úlohu, která sestaví upravenou základní image. Poznamenejte si **ID spuštění** uvedené ve výstupu.

```azurecli
az acr build --registry $BASE_ACR --image baseimages/node:15-alpine --file Dockerfile-base .
```

Jakmile se sestavení dokončí a úloha ACR odešle novou základní image do registru, aktivuje se sestavení image aplikace. Než úloha, kterou jste vytvořili dříve, aktivuje sestavení image aplikace, může to chvíli trvat, protože musí zjistit nově sestavenou a odeslanou základní image.

## <a name="list-updated-build"></a>Výpis aktualizovaného sestavení

Teď, když jste aktualizovali základní image, znovu zobrazte seznam spuštění úloh a porovnejte ho s předchozím seznamem. Pokud se napoprvé výstup neliší, opakovaně spouštějte příkaz, aby se nové spuštění úlohy v seznamu zobrazilo.

```azurecli
az acr task list-runs --registry $ACR_NAME --output table
```

Výstup je podobný tomuto. Triggerem pro poslední spuštěné sestavení by měl být „Image Update“ značící, že se úloha zahájila rychlou úlohou základní image.

```console
$ az acr task list-runs --registry $ACR_NAME --output table

         PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
ca13      baseexample2    linux       Succeeded  Image Update  2020-11-21T00:06:00Z  00:00:43
ca12      baseexample2    linux       Succeeded  Manual        2020-11-21T00:00:56Z  00:00:36
ca11      baseexample1    linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:34
ca10      taskhelloworld  linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:24
cay                       linux       Succeeded  Manual        2020-11-20T23:38:08Z  00:00:22
cax       baseexample1    linux       Succeeded  Manual        2020-11-20T23:33:12Z  00:00:30
caw       taskhelloworld  linux       Succeeded  Commit        2020-11-20T23:16:07Z  00:00:29
```

Chcete-li provést následující volitelný krok spuštění nově vytvořeného kontejneru, aby bylo možné zobrazit aktualizované číslo verze, poznamenejte si hodnotu **ID spuštění** pro sestavení aktivované aktualizací Image (v předchozím výstupu je "CA13").

### <a name="optional-run-newly-built-image"></a>Volitelné: Spuštění nově sestavené image

Pokud pracujete místně (nejste v Cloud Shellu) a máte nainstalovaný Docker, spusťte novou image aplikace, jakmile se její sestavení dokončí. `<run-id>` nahraďte hodnotou ID spuštění, kterou jste získali v předchozím kroku. Pokud Cloud Shell používáte, tuto část přeskočte (Cloud Shell nepodporuje `docker run`).

```bash
docker run -d -p 8081:80 --name updatedapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

V prohlížeči přejděte na http://localhost:8081. Měli byste vidět aktualizované číslo verze Node.js (se znakem „a“) na webové stránce:

:::image type="content" source="media/container-registry-tutorial-base-image-update/base-update-02.png" alt-text="Snímek obrazovky aktualizované ukázkové aplikace v prohlížeči":::

Důležité je uvědomit si, že jste aktualizovali **základní** image s novým číslem verze, ale novou verzi zobrazuje poslední sestavená image **aplikace**. Služba ACR Tasks převzala změnu základní image a automaticky znovu sestavila image aplikace.

Chcete-li zastavit a odebrat kontejner, spusťte následující příkaz:

```bash
docker stop updatedapp
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak pomocí úlohy automaticky aktivovat sestavení imagí kontejnerů při aktualizaci základní image. Teď přejděte k dalšímu kurzu, kde se dozvíte, jak aktivovat úlohy podle definovaného plánu.

> [!div class="nextstepaction"]
> [Spuštění úlohy podle plánu](container-registry-tasks-scheduled.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[code-sample]: https://github.com/Azure-Samples/acr-build-helloworld-node
[dockerfile-app]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-app
[dockerfile-base]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-base

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-update]: /cli/azure/acr/task#az_acr_task_update
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-show]: /cli/azure/acr/task#az_acr_task_show
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az_acr_task_credential_add
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-acr-task]: /cli/azure/acr#az_acr_task
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
