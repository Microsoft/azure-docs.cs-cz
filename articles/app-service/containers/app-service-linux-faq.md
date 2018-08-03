---
title: Azure App Service v Linuxu – nejčastější dotazy | Dokumentace Microsoftu
description: Azure App Service v Linuxu – nejčastější dotazy.
keywords: služby Azure app service, web Apps, nejčastější dotazy, linux, oss, služby web app for containers, více kontejnerů, multicontainer
services: app-service
documentationCenter: ''
author: yili
manager: apurvajo
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: yili
ms.openlocfilehash: cd107ddc5ac443daee592c40bbed451c913a8a3b
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444585"
---
# <a name="azure-app-service-on-linux-faq"></a>Azure App Service v Linuxu – nejčastější dotazy

S vydáním služby App Service v Linuxu pracujeme na přidání funkcí a vylepšovat k naší platformě. Tento článek obsahuje odpovědi na otázky, aby naši zákazníci už dlouho žádali nám nedávno.

Pokud máte dotaz, přidejte komentář v tomto článku.

## <a name="built-in-images"></a>Integrované Image

**Chci vytvořit fork předdefinované kontejnery Dockeru, které platforma nabízí. Kde najdu tyto soubory?**

Všechny soubory Docker můžete najít na [Githubu](https://github.com/azure-app-service). Všechny kontejnery Dockeru můžete najít na [Docker Hubu](https://hub.docker.com/u/appsvc/).

**Jaké jsou očekávané hodnoty pro oddíl spouštěcí soubor, pro které nakonfiguruji zásobník modulu runtime?**

Pro Node.js zadáte konfiguračního souboru PM2 nebo souboru skriptu. .NET Core, zadejte název zkompilované knihovny DLL jako `dotnet <myapp>.dll`. Pro Ruby můžete zadat skript Ruby, který chcete inicializovat aplikace s využitím.

## <a name="management"></a>Správa

**Co se stane při stisknutí tlačítka restartování na webu Azure Portal?**

Tato akce je stejná jako Docker restartování.

**Můžete použít Secure Shell (SSH) pro připojení k aplikaci kontejner virtuálních počítačů (VM)?**

Ano, můžete to udělat prostřednictvím management (SCM) ovládací prvek zdrojové lokality.

> [!NOTE]
> Ke kontejneru aplikace se můžete připojit také přímo z místního vývojového počítače pomocí SSH, SFTP nebo Visual Studio Code (pro živé ladění aplikací Node.js). Další informace najdete v tématu [Vzdálené ladění a SSH ve službě App Service v Linuxu](https://aka.ms/linux-debug).
>

**Jak můžete vytvořit plán služby App Service pro Linux pomocí sady SDK nebo šablony Azure Resource Manageru?**

Byste měli nastavit **vyhrazené** pole app service a *true*.

## <a name="continuous-integration-and-deployment"></a>Průběžná integrace a nasazování

**Moje webová aplikace stále používá starý image kontejneru Dockeru po aktualizovali jsme image v Docker Hubu. Podporujete průběžnou integraci a nasazování vlastních kontejnerů?**

Ano, jak nastavit průběžnou integraci a nasazování pro Azure Container Registry nebo Dockerhubu, následující [průběžné nasazování se službou Web App for Containers](./app-service-linux-ci-cd.md). Privátní Registry můžete aktualizovat kontejner zastavení a spuštění webové aplikace. Nebo můžete změnit nebo přidat nastavení fiktivní aplikace můžete vynutit aktualizaci vašeho kontejneru.

**Podporuje pracovní prostředí?**

Ano.

**Můžu použít *WebDeploy/MSDeploy* nasadit svou webovou aplikaci?**

Ano, je nutné nastavit aplikaci názvem `WEBSITE_WEBDEPLOY_USE_SCM` k *false*.

**Nasazení z Gitu mojí aplikace selže při použití Linuxovou webovou aplikaci. Jak je můžete problém vyřešit?**

Pokud selže nasazení z Gitu do webové aplikace v Linuxu, zvolte jednu z následujících možností pro nasazení kódu aplikace:

- Použít funkci průběžné doručování (Preview): zdrojový kód vaší aplikace můžete uložit v úložišti Git sady Team Services nebo úložiště GitHub do pomocí nepřetržitého doručování Azure. Další informace najdete v tématu [konfigurace průběžného doručování pro Linuxovou webovou aplikaci](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/).

- Použít [ZIP nasaďte API](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file): použití tohoto rozhraní API [SSH do své webové aplikace](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-ssh-support#making-a-client-connection) a přejděte do složky, kam chcete svůj kód můžete nasadit. Spusťte následující kód:

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Pokud dojde k chybě `curl` příkaz nebyl nalezen, ujistěte se, že nainstalujete pomocí curl `apt-get install curl` předtím, než spustíte předchozí `curl` příkazu.

## <a name="language-support"></a>Podpora jazyků

**Chci používat webové sokety v aplikaci Node.js, jakékoli speciální nastavení nebo konfigurace nastavení?**

Ano, zakažte `perMessageDeflate` ve vašem kódu Node.js na straně serveru. Pokud používáte socket.io, použijte následující kód:

```nodejs
var io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Podporujete nezkompilované aplikace .NET Core?**

Ano.

**Podporujete Composer jako správce závislostí pro aplikace v PHP?**

Během nasazení Git, by měl Ano, Kudu rozpoznají, že nasazujete aplikaci PHP (díky přítomnost souboru composer.lock) a Kudu pak spustí instalaci autora.

## <a name="custom-containers"></a>Vlastní kontejnery

**Používám vlastní vlastní kontejner. Chci, aby platformu, pro připojení sdílené složky SMB do `/home/` adresáře.**

Můžete to udělat tak, že nastavíte `WEBSITES_ENABLE_APP_SERVICE_STORAGE` nastavení aplikace nastavte na *true*. Uvědomte si, že to způsobí, že kontejner se restartuje po úložiště platformy prochází změnu.

>[!NOTE]
>Pokud `WEBSITES_ENABLE_APP_SERVICE_STORAGE` nastavení je tento parametr zadán, nebo nastavte *false*, `/home/` adresáře nebude sdílet mezi instancí škálovací a soubory, které jsou zapsány existuje nebude zachován po restartování.

**Moje vlastní kontejner trvá dlouhou dobu spuštění a platformu restartuje kontejner před dokončením spouštění.**

Můžete nakonfigurovat množství času, které platformy bude čekat před restartování vašeho kontejneru. Chcete-li to provést, nastavte `WEBSITES_CONTAINER_START_TIME_LIMIT` nastavení aplikace nastavte na hodnotu, kterou chcete. Výchozí hodnota je 230 sekund a maximální hodnota je 1 800 sekund.

**Co je formát pro adresu URL serveru privátního registru?**

Zadejte adresu URL úplné registru, včetně `http://` nebo `https://`.

**Co je formát pro název image v privátním registru možnost?**

Přidáte název úplnou bitovou kopii, včetně adresy URL privátního registru (například myacr.azurecr.io/dotnet:latest). Obrázek názvy, které používají vlastní port [nelze zadat prostřednictvím portálu](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Chcete-li nastavit `docker-custom-image-name`, použijte [ `az` nástroj příkazového řádku](https://docs.microsoft.com/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set).

**Můžete zpřístupnit více než jeden port ve své vlastní image kontejneru?**

Aktuálně nepodporujeme, vystavení více než jeden port.

**Můžete použít vlastní úložiště?**

Aktuálně nepodporujeme, přináší vlastní úložiště.

**Proč nelze procházet procesy systému nebo spuštění souboru Moje vlastní kontejner na webu Správce řízení služeb?**

Web SCM běží v samostatných kontejneru. Nelze zkontrolovat soubor systému nebo spuštěné procesy kontejneru aplikace.

**Moje vlastní kontejner naslouchá na port než 80. Jak lze nakonfigurovat aplikace směrovat požadavky na daný port?**

Máme Automatická detekce portu. Můžete také určit aplikaci názvem *WEBSITES_PORT* a přiřaďte jí hodnotu číslo portu očekávané. Dříve, platformu používá *PORT* nastavení aplikace. Plánujeme přestat používat nastavení aplikace a používat *WEBSITES_PORT* exkluzivně.

**Je potřeba implementovat HTTPS ve své vlastní kontejner?**

Ne, se stará platforma a ukončení HTTPS na sdílené front-endů.

## <a name="multi-container-with-docker-compose-and-kubernetes"></a>Více kontejnerů pomocí Docker Compose a Kubernetes

**Jak konfigurovat Azure Container Registry (ACR) pro použití s více kontejnerů?**

Chcete-li použít služby ACR s více kontejnerů, **všech imagí kontejneru** musí být hostované na stejném serveru registru ACR. Poté, co jsou na stejném serveru registru, je potřeba vytvořit nastavení aplikace a pak aktualizovat konfigurační soubor Docker Compose nebo Kubernetes pro vložení názvu image ACR.

Vytvořte následující nastavení aplikace:

- DOCKER_REGISTRY_SERVER_USERNAME
- DOCKER_REGISTRY_SERVER_URL (úplná adresa URL, třeba: https://<server-name>.azurecr.io)
- DOCKER_REGISTRY_SERVER_PASSWORD (Povolit přístup správce v nastavení služby ACR)

V konfiguračním souboru referenční image ACR jako v následujícím příkladu:

```yaml
image: <server-name>.azurecr.io/<image-name>:<tag>
```

**Jak zjistím, které kontejner je přístup k Internetu?**

- Pouze jeden kontejner může být otevřený pro přístup k
- Jenom port 80 a 8080 je přístupná (vystavené porty)

Tady jsou pravidla pro určování, které kontejner je přístupný – v pořadí podle priority:

- Nastavení aplikace `WEBSITES_WEB_CONTAINER_NAME` nastavte název kontejneru
- První kontejner k definování portu 80 nebo 8080
- Pokud ani jeden z výše uvedených má hodnotu true, první kontejner definované v souboru budou přístupné (vystavit)

## <a name="pricing-and-sla"></a>Ceny a smlouva SLA

**Jaké jsou ceny, teď, když je tato služba obecně dostupná?**

Bude se vám účtovat normální ceny služby Azure App Service pro počet hodin, které vaše aplikace běží.

## <a name="other-questions"></a>Další otázky

**Jaké jsou podporované znaky v názvech nastavení aplikace?**

Pro nastavení aplikace můžete použít pouze písmena (A-Z, a – z), číslice (0-9) a podtržítko (_).

**Kde můžou požádat o nové funkce?**

Odešlete svůj nápad na [fóru pro zpětnou vazbu Web Apps](https://aka.ms/webapps-uservoice). Přidáte "[Linux]" title svůj nápad.

## <a name="next-steps"></a>Další postup

- [Co je Azure App Service v Linuxu?](app-service-linux-intro.md)
- [Nastavení přípravných prostředí ve službě Azure App Service](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Průběžné nasazování pomocí služby Web App for Containers](./app-service-linux-ci-cd.md)
