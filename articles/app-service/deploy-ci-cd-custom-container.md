---
title: CI/CD do vlastních kontejnerů
description: Nastavte průběžné nasazování na vlastní kontejner systému Windows nebo Linux v Azure App Service.
keywords: Azure App Service, Linux, Docker, ACR, OSS
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 03/12/2021
ms.author: msangapu
ms.custom: seodec18, devx-track-azurecli
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 6519f3fe7335ed41f4d5ef67771aaa738a33e4a8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782598"
---
# <a name="continuous-deployment-with-custom-containers-in-azure-app-service"></a>Průběžné nasazování s vlastními kontejnery v Azure App Service

V tomto kurzu nakonfigurujete průběžné nasazování pro vlastní image kontejneru ze spravovaných [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) úložišť nebo [Docker Hub](https://hub.docker.com).

## <a name="1-go-to-deployment-center"></a>1. přejít do centra nasazení

V [Azure Portal](https://portal.azure.com)přejděte na stránku pro správu vaší aplikace App Service.

V nabídce vlevo klikněte na nastavení **centra nasazení**  >  . 

::: zone pivot="container-linux"
## <a name="2-choose-deployment-source"></a>2. Vyberte zdroj nasazení.

**Výběr** zdroje nasazení závisí na vašem scénáři:
- **Registr kontejnerů** nastavuje CI/CD mezi registrem kontejneru a App Service.
- Možnost **Akce GitHubu** je určena pro vás, pokud udržujete zdrojový kód pro Image kontejneru v GitHubu. Aktivované novými potvrzeními změn do úložiště GitHubu můžete spustit akci nasadit `docker build` a `docker push` přímo do registru kontejneru a potom aktualizovat aplikaci App Service, aby se spustila nová image. Další informace najdete v tématu [Jak funguje CI/CD s akcemi GitHubu](#how-cicd-works-with-github-actions).
- Informace o nastavení služby CI/CD pomocí **Azure Pipelines** najdete v tématu [nasazení kontejneru webové aplikace Azure z Azure Pipelines](/azure/devops/pipelines/targets/webapp-on-container-linux).

> [!NOTE]
> V případě aplikace Docker Compose vyberte **Container Registry**.

Pokud zvolíte akce GitHubu, **klikněte na** **autorizovat** a postupujte podle výzev k autorizaci. Pokud jste už máte autorizaci s GitHubem, můžete ho nasadit z úložiště jiného uživatele kliknutím na **změnit účet**.

Po autorizaci účtu Azure pomocí GitHubu **Vyberte** **organizaci**, **úložiště** a **větev** , ze které se mají nasadit.
::: zone-end  

::: zone pivot="container-windows"
## <a name="2-configure-registry-settings"></a>2. Konfigurace nastavení registru
::: zone-end  
::: zone pivot="container-linux"
## <a name="3-configure-registry-settings"></a>3. Konfigurace nastavení registru

Pokud chcete nasadit aplikaci s více kontejnery (Docker Compose), **vyberte** **Docker Compose** v **typu kontejneru**.

Pokud nevidíte rozevírací seznam **typ kontejneru** , přejděte zpět na **zdroj** a **Vyberte** **Container Registry**.
::: zone-end

V případě **zdroje registru** **Vyberte** umístění registru kontejneru. Pokud není Azure Container Registry ani Docker Hub, **Vyberte** **privátní registr**.

::: zone pivot="container-linux"
> [!NOTE]
> Pokud vaše aplikace pro více kontejnerů (Docker Compose) používá více než jednu privátní bitovou kopii, ujistěte se, že jsou privátní obrázky ve stejném privátním registru a jsou přístupné se stejnými přihlašovacími údaji uživatele. Pokud vaše aplikace s více kontejnery používá jenom veřejné image, **Vyberte** **Docker Hub**, a to i v případě, že některé image nejsou v Docker Hub.
::: zone-end  

Postupujte podle dalších kroků a vyberte kartu, která odpovídá vaší volbě.

# <a name="azure-container-registry"></a>[Azure Container Registry](#tab/acr)

V rozevíracím seznamu **registr** se zobrazí Registry ve stejném předplatném jako vaše aplikace. **Vyberte** registr, který chcete.

> [!NOTE]
> Pokud chcete nasadit z registru v jiném předplatném, vyberte místo toho **možnost** **privátní registr** v rámci **zdroje registru** .

::: zone pivot="container-windows"
**Vyberte** **Image** a **značku** k nasazení. Pokud chcete, **Zadejte** do **spouštěcího souboru** příkaz Spustit jako. 
::: zone-end
::: zone pivot="container-linux"
Použijte následující krok v závislosti na **typu kontejneru**:
- Pro **Docker Compose** **Vyberte** registr pro privátní image. **Klikněte na** **zvolit soubor** a odešlete [soubor Docker Compose](https://docs.docker.com/compose/compose-file/)nebo stačí **Vložit** obsah Docker Compose souboru do **Konfigurace**.
- V případě **jednoho kontejneru** **Vyberte** **Image** a **značku** k nasazení. Pokud chcete, **Zadejte** do **spouštěcího souboru** příkaz Spustit jako. 
::: zone-end

App Service připojí řetězec ve **spouštěcím souboru** ke [konci `docker run` příkazu (jako `[COMMAND] [ARG...]` segment)](https://docs.docker.com/engine/reference/run/) při spuštění kontejneru.

# <a name="docker-hub"></a>[Docker Hub](#tab/dockerhub)

::: zone pivot="container-windows"
V části **přístup k úložišti** **Vyberte** , jestli je bitová kopie, která se má nasadit, veřejná nebo soukromá.
::: zone-end
::: zone pivot="container-linux"
V části **přístup k úložišti** **Vyberte** , jestli je bitová kopie, která se má nasadit, veřejná nebo soukromá. V případě aplikace Docker Compose s jedním nebo více privátními obrázky **Vyberte** **soukromé**.
::: zone-end

Pokud vyberete privátní bitovou kopii, **Zadejte** **přihlašovací** jméno a **heslo** k účtu Docker.

::: zone pivot="container-windows"
**Zadejte** název Image a značky do pole **název a značka úplného obrázku** oddělené znakem `:` (například `nginx:latest` ). Pokud chcete, **Zadejte** do **spouštěcího souboru** příkaz Spustit jako. 
::: zone-end
::: zone pivot="container-linux"
Použijte následující krok v závislosti na **typu kontejneru**:
- Pro **Docker Compose** **Vyberte** registr pro privátní image. **Klikněte na** **zvolit soubor** a odešlete [soubor Docker Compose](https://docs.docker.com/compose/compose-file/)nebo stačí **Vložit** obsah Docker Compose souboru do **Konfigurace**.
- V případě **jednoho kontejneru** **Zadejte** název Image a značky do pole **název a značka úplného obrázku** oddělené znakem `:` (například `nginx:latest` ). Pokud chcete, **Zadejte** do **spouštěcího souboru** příkaz Spustit jako. 
::: zone-end

App Service připojí řetězec ve **spouštěcím souboru** ke [konci `docker run` příkazu (jako `[COMMAND] [ARG...]` segment)](https://docs.docker.com/engine/reference/run/) při spuštění kontejneru.

# <a name="private-registry"></a>[Privátní registr](#tab/private)

Do pole **Adresa URL serveru** **Zadejte** adresu url serveru, počínaje **https://**.

Do **přihlašovacích** údajů  **Zadejte** přihlašovací údaje pro váš privátní registr a zadejte heslo.

::: zone pivot="container-windows"
**Zadejte** název Image a značky do pole **název a značka úplného obrázku** oddělené znakem `:` (například `nginx:latest` ). Pokud chcete, **Zadejte** do **spouštěcího souboru** příkaz Spustit jako. 
::: zone-end
::: zone pivot="container-linux"
Použijte následující krok v závislosti na **typu kontejneru**:
- Pro **Docker Compose** **Vyberte** registr pro privátní image. **Klikněte na** **zvolit soubor** a odešlete [soubor Docker Compose](https://docs.docker.com/compose/compose-file/)nebo stačí **Vložit** obsah Docker Compose souboru do **Konfigurace**.
- V případě **jednoho kontejneru** **Zadejte** název Image a značky do pole **název a značka úplného obrázku** oddělené znakem `:` (například `nginx:latest` ). Pokud chcete, **Zadejte** do **spouštěcího souboru** příkaz Spustit jako. 
::: zone-end

App Service připojí řetězec ve **spouštěcím souboru** ke [konci `docker run` příkazu (jako `[COMMAND] [ARG...]` segment)](https://docs.docker.com/engine/reference/run/) při spuštění kontejneru.

-----

::: zone pivot="container-windows"
## <a name="3-enable-cicd"></a>3. povolení CI/CD
::: zone-end
::: zone pivot="container-linux"
## <a name="4-enable-cicd"></a>4. povolení CI/CD
::: zone-end

App Service podporuje integraci CI/CD s Azure Container Registry a Docker Hub. Pokud ho chcete povolit, vyberte při **průběžném nasazování** **možnost** **zapnuto** .

::: zone pivot="container-linux"
> [!NOTE]
> Pokud vyberete možnost **GitHub Actions** ( **zdroj**), tato možnost se nezobrazuje, protože CI/CD se zpracovává přímo na základě akcí na GitHubu. Místo toho se zobrazí část **Konfigurace pracovního postupu** , kde můžete **kliknout na** **Náhled souboru** a zkontrolovat soubor pracovního postupu. Azure tento soubor potvrdí do vybraného zdrojového úložiště GitHub pro zpracování úloh sestavení a nasazení. Další informace najdete v tématu [Jak funguje CI/CD s akcemi GitHubu](#how-cicd-works-with-github-actions).
::: zone-end

Když tuto možnost povolíte, App Service do úložiště přidat Webhook v Azure Container Registry nebo Docker Hub. Vaše úložiště se publikuje do tohoto Webhooku, kdykoli se vaše vybraná image aktualizuje pomocí `docker push` . Webhook způsobí, že se vaše aplikace App Service restartuje a spustí `docker pull` se, aby se získala aktualizovaná image.

**U ostatních privátních registrů** se může váš Webhook vystavit ručně nebo jako krok v kanálu CI/CD. **Kliknutím** na tlačítko **Kopírovat** v **adrese URL WEBhooku** získáte adresu URL Webhooku.

::: zone pivot="container-linux"
> [!NOTE]
> Podpora aplikací pro více kontejnerů (Docker Compose) je omezená: 
> - V případě Azure Container Registry App Service vytvoří Webhook ve vybraném registru s registrem jako oborem. A `docker push` do jakéhokoli úložiště v registru (včetně těch, které neodkazuje soubor Docker Compose) spustí restart aplikace. Webhook možná budete chtít [změnit](../container-registry/container-registry-webhook.md) na užší rozsah.
> - Docker Hub nepodporuje Webhooky na úrovni registru. Webhooky musíte **Přidat** ručně do imagí zadaných v souboru Docker Compose.
::: zone-end

::: zone pivot="container-windows"
## <a name="4-save-your-settings"></a>4. Uložte nastavení.
::: zone-end
::: zone pivot="container-linux"
## <a name="5-save-your-settings"></a>5. Uložte nastavení.
::: zone-end

**Klikněte na** **Uložit**.

::: zone pivot="container-linux"

## <a name="how-cicd-works-with-github-actions"></a>Jak funguje CI/CD s akcemi GitHubu

Pokud zvolíte **Akce GitHubu** ve **zdroji** (viz téma [volba zdroje nasazení](#2-choose-deployment-source)), App Service nastaví CI/CD následujícími způsoby:

- Uloží soubor pracovního postupu akce GitHubu do úložiště GitHub, aby zpracovával úlohy sestavení a nasazení App Service.
- Přidá přihlašovací údaje k privátnímu registru jako tajné kódy GitHubu. Vygenerovaný soubor pracovního postupu spustí akci [Azure/Docker – přihlášení](https://github.com/Azure/docker-login) , aby se přihlásil ke svému privátnímu registru, a pak se spustí `docker push` a nasadí se na něj.
- Přidá profil publikování aplikace jako tajný klíč GitHubu. Vygenerovaný soubor pracovního postupu používá tento tajný klíč k ověření pomocí App Service a potom spustí akci [Azure/webapps-Deploy](https://github.com/Azure/webapps-deploy) ke konfiguraci aktualizované image, která spustí restart aplikace, který se má načíst v aktualizované imagi.
- Zachycuje informace z [pracovních postupů spuštění protokolů](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) a zobrazí je na kartě **protokoly** v **centru nasazení** vaší aplikace.

Poskytovatele sestavení akcí GitHubu můžete přizpůsobit následujícími způsoby:

- Až se soubor pracovního postupu vygeneruje v úložišti GitHubu, přizpůsobte ho. Další informace najdete v tématu [syntaxe pracovního postupu pro akce GitHubu](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions). Pokud chcete aktivovat restart aplikace, zajistěte, aby byl pracovní postup ukončený akcí [Azure/webapps-Deploy](https://github.com/Azure/webapps-deploy) .
- Pokud je vybraná větev chráněná, můžete si i nadále zobrazit náhled souboru pracovního postupu bez uložení konfigurace a pak ho přidat a požadované tajné kódy GitHubu do úložiště ručně. Tato metoda neposkytuje integraci protokolu s Azure Portal.
- Místo profilu publikování nasaďte pomocí [instančního objektu ve službě](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) Azure Active Directory.

#### <a name="authenticate-with-a-service-principal"></a>Ověřování pomocí instančního objektu

Tato volitelná konfigurace nahrazuje výchozí ověřování pomocí profilů publikování ve vygenerovaném souboru pracovního postupu.

**Vygenerujte** instanční objekt pomocí příkazu [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) v rozhraní příkazového [řádku Azure CLI](/cli/azure/). V následujícím příkladu nahraďte *\<subscription-id>* , *\<group-name>* a *\<app-name>* vlastními hodnotami. **Uložte** celý výstup JSON pro další krok, včetně nejvyšší úrovně `{}` .

```azurecli-interactive
az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

> [!IMPORTANT]
> V případě zabezpečení udělte minimálnímu požadovanému přístupu k instančnímu objektu. Obor v předchozím příkladu je omezený na konkrétní App Service aplikaci, a ne na celou skupinu prostředků.

V [GitHubu](https://github.com/) **přejděte** do úložiště a pak **Vyberte** **Nastavení > tajných kódů > přidat nový tajný kód**. Do pole hodnota tajného klíče **vložte** celý výstup JSON z příkazu Azure CLI. **Zadejte** název tajného kódu jako `AZURE_CREDENTIALS` .

V souboru pracovního postupu vygenerovaném **centrem nasazení** **upravte** `azure/webapps-deploy` krok s kódem podobným následujícímu příkladu:

```yaml
- name: Sign in to Azure 
# Use the GitHub secret you added
- uses: azure/login@v1
    with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
- name: Deploy to Azure Web App
# Remove publish-profile
- uses: azure/webapps-deploy@v2
    with:
    app-name: '<app-name>'
    slot-name: 'production'
    images: '<registry-server>/${{ secrets.AzureAppService_ContainerUsername_... }}/<image>:${{ github.sha }}'
    - name: Sign out of Azure
    run: |
    az logout
```

::: zone-end

## <a name="automate-with-cli"></a>Automatizace pomocí rozhraní příkazového řádku

Pokud chcete nakonfigurovat registr kontejneru a image Docker, **Spusťte** příkaz [AZ WebApp config Container set](/cli/azure/webapp/config/container#az_webapp_config_container_set).

# <a name="azure-container-registry"></a>[Azure Container Registry](#tab/acr)

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name '<image>:<tag>' --docker-registry-server-url 'https://<registry-name>.azurecr.io' --docker-registry-server-user '<username>' --docker-registry-server-password '<password>'
```

# <a name="docker-hub"></a>[Docker Hub](#tab/dockerhub)

```azurecli-interactive
# Public image
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name>

# Private image
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

# <a name="private-registry"></a>[Privátní registr](#tab/private)

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name '<image>:<tag>' --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

-----

::: zone pivot="container-linux"
Pokud chcete nakonfigurovat aplikaci s více kontejnery (Docker Compose), **Připravte** soubor Docker Compose místně a pak **Spusťte příkaz** [AZ WebApp config Container set](/cli/azure/webapp/config/container#az_webapp_config_container_set) s `--multicontainer-config-file` parametrem. Pokud soubor Docker Compose obsahuje soukromé image, **přidejte** `--docker-registry-server-*` parametry, jak je znázorněno v předchozím příkladu.

```azurecli-interactive
az webapp config container set --resource-group <group-name> --name <app-name> --multicontainer-config-file <docker-compose-file>
```
::: zone-end

Pokud chcete nakonfigurovat CI/CD z registru kontejneru do vaší aplikace, **Spusťte příkaz** [AZ WebApp Deployment Container config](/cli/azure/webapp/deployment/container#az_webapp_deployment-container-config) s `--enable-cd` parametrem. Příkaz vypíše adresu URL Webhooku, ale musíte Webhook vytvořit v registru ručně v samostatném kroku. Následující příklad povoluje CI/CD ve vaší aplikaci a pak pomocí adresy URL Webhooku ve výstupu vytvoří Webhook v Azure Container Registry.

```azurecli-interactive
ci_cd_url=$(az webapp deployment container config --name <app-name> --resource-group <group-name> --enable-cd true --query CI_CD_URL --output tsv)

az acr webhook create --name <webhook-name> --registry <registry-name> --resource-group <group-name> --actions push --uri $ci_cd_url --scope '<image>:<tag>'
```

## <a name="more-resources"></a>Další zdroje informací

* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Vytvoření webové aplikace .NET Core ve službě App Service v Linuxu](quickstart-dotnetcore.md)
* [Rychlý Start: spuštění vlastního kontejneru v App Service](quickstart-custom-container.md)
* [Nejčastější dotazy k App Service v Linuxu](faq-app-service-linux.md)
* [Konfigurace vlastních kontejnerů](configure-custom-container.md)
* [Akce pracovních postupů k nasazení do Azure](https://github.com/Azure/actions-workflow-samples)
