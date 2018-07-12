---
title: Kurz služby Azure Container Registry – Nahrání aktualizované image do regionálních nasazení
description: Nahrajte upravenou image Dockeru do vlastního geograficky replikovaného registru kontejneru Azure a změny se automaticky nasadí do webových aplikací běžících v několika oblastech. Třetí částí z třídílné série.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 04/30/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 8edb35b91327bde1fa824ec456b8a98962adb7ce
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38634083"
---
# <a name="tutorial-push-an-updated-image-to-regional-deployments"></a>Kurz: Nahrání aktualizované image do regionálních nasazení

Toto je třetí část z třídílné série kurzů. V [předchozím kurzu](container-registry-tutorial-deploy-app.md) se geografická replikace konfigurovala pro dvě různá nasazení regionální webové aplikace. V tomto kurzu nejprve aplikaci upravíte a potom sestavíte novou image kontejneru a nahrajete ji do geograficky replikovaného registru. Nakonec si zobrazíte změnu, která v obou instancích webové aplikace proběhla automaticky díky webhookům služby Azure Container Registry.

V poslední části série tohoto kurzu se naučíte:

> [!div class="checklist"]
> * Upravit webovou aplikaci protokolu HTML
> * Sestavit a označit image Dockeru
> * Nahrát změnu do služby Azure Container Registry
> * Zobrazit aktualizovanou aplikaci ve dvou různých oblastech

Pokud jste ještě dvě regionální nasazení služby *Web App for Containers* nenakonfigurovali, přejděte na předchozí kurz v této sérii: [Nasazení webové aplikace ze služby Azure Container Registry](container-registry-tutorial-deploy-app.md).

## <a name="modify-the-web-application"></a>Úprava webové aplikace

V tomto kroku provedete změnu webové aplikace, která bude po nahrání aktualizované image kontejneru do služby Azure Container Registry snadno rozpoznatelná.

Najděte ve zdroji aplikace soubor `AcrHelloworld/Views/Home/Index.cshtml`, který jste v předchozím kurzu [naklonovali z GitHubu](container-registry-tutorial-prepare-registry.md#get-application-code) a otevřete ho ve svém oblíbeném textovém editoru. Pod řádek `<h1>` přidejte následující řádek:

```html
<h1>MODIFIED</h1>
```

Upravený soubor `Index.cshtml` by měl vypadat nějak takto:

```html
@{
    ViewData["Title"] = "Azure Container Registry :: Geo-replication";
}
<style>
    body {
        background-image: url('images/azure-regions.png');
        background-size: cover;
    }
    .footer {
        position: fixed;
        bottom: 0px;
        width: 100%;
    }
</style>

<h1 style="text-align:center;color:blue">Hello World from:  @ViewData["REGION"]</h1>
<h1>MODIFIED</h1>
<div class="footer">
    <ul>
        <li>Registry URL: @ViewData["REGISTRYURL"]</li>
        <li>Registry IP: @ViewData["REGISTRYIP"]</li>
        <li>Registry Region: @ViewData["REGION"]</li>
    </ul>
</div>
```

## <a name="rebuild-the-image"></a>Opětovné sestavení image

Nyní, když jste webovou aplikaci aktualizovali, sestavte znovu její image kontejneru. Stejně jako dříve použijte plně kvalifikovaný název image včetně plně kvalifikovaného názvu domény přihlašovacího serveru pro značku:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

## <a name="push-image-to-azure-container-registry"></a>Nahrání image do služby Azure Container Registry

Pak aktualizovanou image kontejneru *acr-helloworld* nahrajte do svého geograficky replikovaného registru. Zde provedete jediný příkaz `docker push`, kterým nasadíte aktualizovanou image do replik registru v obou oblastech (*West US* a *East US*).

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

Výstup příkazu `docker push` by měl vypadat přibližně takto:

```console
$ docker push uniqueregistryname.azurecr.io/acr-helloworld:v1
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
5b9454e91555: Pushed
d6803756744a: Layer already exists
b7b1f3a15779: Layer already exists
a89567dff12d: Layer already exists
59c7b561ff56: Layer already exists
9a2f9413d9e4: Layer already exists
a75caa09eb1f: Layer already exists
v1: digest: sha256:4c3f2211569346fbe2d1006c18cbea2a4a9dcc1eb3a078608cef70d3a186ec7a size: 1792
```

## <a name="view-the-webhook-logs"></a>Zobrazení webhookových protokolů

Během procesu replikace image si můžete všimnout, že dochází k aktivaci webhooků služby Azure Container Registry.

Pokud si chcete zobrazit regionální webhooky, které se vytvořily v předchozím kurzu po nasazení kontejneru do služby *Web Apps for Containers*, přejděte do svého registru kontejneru na portálu Azure Portal a v části **SLUŽBY** vyberte **Webhooky**.

![Webhooky v registru kontejnerů na portálu Azure Portal][tutorial-portal-01]

Historii volání a odpovědí webhooku uvidíte po kliknutí na jeden z nich. V protokolech obou webhooků byste měli vidět řadu pro akci **nahrání**. Protokol webhooku umístěného v oblasti *West US* zobrazuje aktivaci akce **nahrání** příkazem `docker push` z předchozího kroku:

![Protokol webhooku v registru kontejnerů na portálu Azure Portal (West US)][tutorial-portal-02]

## <a name="view-the-updated-web-app"></a>Zobrazení aktualizované webové aplikace

Webhooky upozorňují webové aplikace, že byla do registru nahrávána nová image, která automaticky nasadí aktualizovaný kontejner do dvou regionálních webových aplikací.

Přejděte ve svém prohlížeči na obě nasazení regionální webové aplikace a ověřte, že v obou nasazeních už byla aplikace aktualizována. Připomínáme vám, že adresu URL nasazené webové aplikace najdete v pravém horním rohu každé karty přehledu služby App Service.

![Přehled služby App Service na portálu Azure Portal][tutorial-portal-03]

Pokud si chcete aktualizovanou aplikaci zobrazit, vyberte odkaz v přehledu služby App Service. Zde je příklad zobrazení aplikace běžící v oblasti *West US*:

![Zobrazení prohlížeče s upravenou webovou aplikací běžící v oblasti West US][deployed-app-westus-modified]

Ověřte si zobrazením v prohlížeči, že aktualizovaná image kontejneru byla nasazená také v oblasti *East US*.

![Zobrazení prohlížeče s upravenou webovou aplikací běžící v oblasti East US][deployed-app-eastus-modified]

Jediným příkazem `docker push` jste automaticky aktualizovali webovou aplikaci běžící v obou nasazeních regionální webové aplikace. A služba Azure Container Registry poskytla image kontejnerů z úložišť, která jsou umístěná nejblíže k jednotlivým nasazením.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste aktualizovali a nasadili novou verzi kontejneru webové aplikace do svého geograficky replikovaného registru. Webhooky ve službě Azure Container Registry zaslaly oznámení o aktualizaci službě Web App for Containers, která aktivovala načtení z nejbližší repliky registru.

### <a name="acr-build-automated-image-build-and-patch"></a>ACR Build: Automatické sestavení a oprava image

Vedle geografické replikace je ACR Build další funkcí služby Azure Container Registry, která vám pomůže při optimalizaci kanálu nasazení kontejneru. Začněte přehledem ACR Build, abyste získali představu o možnostech této funkce:

[Automatizace oprav operačního systému a architektury pomocí funkce ACR Build](container-registry-build-overview.md)

<!-- IMAGES -->
[deployed-app-eastus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-eastus-modified.png
[deployed-app-westus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-westus-modified.png
[local-container-01]: ./media/container-registry-tutorial-deploy-update/local-container-01.png
[tutorial-portal-01]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-01.png
[tutorial-portal-02]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-02.png
[tutorial-portal-03]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-03.png