---
title: Aplikační služba Azure v systému Linux – nejčastější dotazy | Microsoft Docs
description: Aplikační služba Azure v systému Linux – nejčastější dotazy.
keywords: služby Azure app service, webové aplikace, – nejčastější dotazy, linux, operačních systémů, webové aplikace pro kontejnery, více kontejneru, multicontainer
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
ms.openlocfilehash: a35f3d428674c3398497cd43465e0bd501f5c3fc
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131488"
---
# <a name="azure-app-service-on-linux-faq"></a>V systému Linux nejčastější dotazy týkající se služby Azure App Service

S vydáním služby App Service v systému Linux pracujeme na přidání funkcí a vylepšení naše platforma provádění. Tento článek obsahuje odpovědi na otázky, aby naše zákazníky mít byla zprávu s požadavkem nedávno.

Pokud máte dotazy, komentář v tomto článku.

## <a name="built-in-images"></a>Předdefinované bitové kopie

**Chci rozvětvit předdefinované Docker kontejnerů, které poskytuje platformu. Kde najdu tyto soubory?**

Všechny soubory Docker můžete najít na [Githubu](https://github.com/azure-app-service). Můžete najít všechny kontejnery Docker na [úložiště Docker Hub](https://hub.docker.com/u/appsvc/).

**Co jsou očekávané hodnoty pro oddíl spuštění souboru, pro které je možné nakonfigurovat zásobník runtime?**

Pro Node.js je třeba zadat konfigurační soubor PM2 nebo souboru skriptu. Pro .NET Core, zadejte název vaší zkompilované knihovny DLL jako `dotnet <myapp>.dll`. Pro Ruby můžete zadat Ruby skript, který chcete inicializovat vaší aplikace pomocí.

## <a name="management"></a>Správa

**Co se stane při stisknutí tlačítka restartování na portálu Azure?**

Tato akce je stejná jako Docker restartování.

**Můžete použít Secure Shell (SSH) pro připojení k aplikaci kontejneru virtuální počítač (VM)?**

Ano, můžete to udělat pomocí zdrojové lokality řízení správy (SCM).

> [!NOTE]
> Ke kontejneru aplikace se můžete připojit také přímo z místního vývojového počítače pomocí SSH, SFTP nebo Visual Studio Code (pro živé ladění aplikací Node.js). Další informace najdete v tématu [Vzdálené ladění a SSH ve službě App Service v Linuxu](https://aka.ms/linux-debug).
>

**Jak můžete vytvořit plán služby App Service Linux prostřednictvím sady SDK nebo šablonu Azure Resource Manager?**

Měli byste nastavit **vyhrazené** pole aplikace služby k *true*.

## <a name="continuous-integration-and-deployment"></a>Průběžná integrace a nasazování

**Webová aplikace se pořád používají staré obrázek kontejner Docker po aktualizovali bitovou kopii na úložiště Docker Hub. Podporujete průběžnou integraci a nasazení vlastních kontejnerů?**

Ano, nastavte průběžnou integraci a nasazení pro Azure kontejneru registru nebo DockerHub, tak následující [průběžné nasazování s webovou aplikaci pro kontejnery](./app-service-linux-ci-cd.md). Pro privátní registrech můžete aktualizovat kontejneru zastavení a spuštění webové aplikace. Nebo můžete změnit nebo přidat nastavení fiktivní aplikace můžete vynutit aktualizaci vašeho kontejneru.

**Podporujete pracovní prostředí?**

Ano.

**Je možné používat *nasazení webu* nasazení webová aplikace?**

Ano, je nutné nastavit aplikaci názvem `WEBSITE_WEBDEPLOY_USE_SCM` k *false*.

**Nasazení Git Moje aplikace nezdaří, při použití Linux webové aplikace. Jak je můžete problém vyřešit?**

Pokud nasazení Git webové aplikace v systému Linux, vyberte jednu z následujících možností, které nasadíte tak svůj kód aplikace:

- Použít funkci nastavené průběžné doručování (Preview): můžete ukládat zdrojového kódu vaší aplikace do úložiště Team Services Git nebo úložiště GitHub používat Azure nastavené průběžné doručování. Další informace najdete v tématu [postup konfigurace nastavené průběžné doručování pro webovou aplikaci Linux](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/).

- Použít [ZIP nasadit rozhraní API](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file): používat toto rozhraní API [SSH do vaší webové aplikace](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-ssh-support#making-a-client-connection) a přejděte do složky, ve které chcete nasadit kód. Spusťte následující kód:

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Pokud dojde k chybě `curl` příkaz nebyl nalezen, je nutné nainstalovat curl pomocí `apt-get install curl` předtím, než spustíte předchozí `curl` příkaz.

## <a name="language-support"></a>Podpora jazyků

**Chcete použít objekty websocket v mé aplikaci Node.js, jakékoli speciální nastavení nebo konfigurace nastavení?**

Ano, zakažte `perMessageDeflate` ve vašem kódu Node.js na straně serveru. Například pokud používáte socket.io, použijte následující kód:

```nodejs
var io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Podporujete nezkompilované aplikace .NET Core?**

Ano.

**Podporujete autora jako správce závislostí pro aplikace PHP?**

Během nasazení Git, by měl Ano, Kudu zjistí, že nasazujete aplikace PHP (díky přítomnost souboru composer.lock), a Kudu poté spustí instalaci autora.

## <a name="custom-containers"></a>Vlastní kontejnery

**Používám vlastní vlastní kontejner. Požadovaná platforma připojit k serveru SMB pro sdílení `/home/` adresáře.**

Můžete to udělat nastavením `WEBSITES_ENABLE_APP_SERVICE_STORAGE` nastavení aplikace nastavte na *true*. Mějte na paměti, které jsou restartuje kontejner to může způsobit, pokud úložiště platformy prochází změnu.

>[!NOTE]
>Pokud `WEBSITES_ENABLE_APP_SERVICE_STORAGE` nastavení je tento parametr nezadáte, nebo nastavte *false*, `/home/` directory nebude sdílen napříč instancemi škálování a soubory, které jsou zapsány existuje nebude zachován po restartování.

**Mé vlastní kontejner trvá dlouhou dobu spuštění a platformou restartuje kontejner před dokončením spuštění.**

Můžete nakonfigurovat množství času, kterou platformu bude čekat před jeho restartování vašeho kontejneru. Chcete-li tak učinit, nastavte `WEBSITES_CONTAINER_START_TIME_LIMIT` nastavení aplikace nastavte na hodnotu, kterou chcete. Výchozí hodnota je 230 sekund a maximální hodnota je 1 800 sekund.

**Co je formát pro adresu URL serveru privátní registru?**

Zadejte adresu URL úplné registru včetně `http://` nebo `https://`.

**Co je formát pro název bitové kopie v možnosti privátní registru?**

Přidejte název úplnou bitovou kopii, včetně privátního registru adresa URL (například myacr.azurecr.io/dotnet:latest). Obrázek názvy, které používají vlastní port [nelze zadat, prostřednictvím portálu](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Chcete-li nastavit `docker-custom-image-name`, použijte [ `az` nástroj příkazového řádku](https://docs.microsoft.com/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set).

**Můžou zpřístupnit více než jeden port na Moje obrázek vlastní kontejner?**

Aktuálně nepodporujeme, vystavení více než jeden port.

**Můžete zahrnout vlastní úložiště?**

Aktuálně nepodporujeme, přináší vlastního úložiště.

**Proč nelze procházet procesy systému nebo spuštění souboru Moje vlastní kontejner z webu SCM?**

SCM lokality běží ve zvláštním kontejneru. Nelze zkontrolovat soubor systému nebo spuštění procesů kontejneru aplikace.

**Mé vlastní kontejner naslouchá na jiný port než port 80. Konfigurování aplikace my směrovat požadavky k tomuto portu**

Máme Automatická detekce portu. Můžete také určit aplikaci názvem *WEBSITES_PORT* a jako hodnotu číslo portu očekávané. Předtím, platformu použít *PORT* nastavení aplikace. Jsme plánování přestat používat toto nastavení aplikace a používat *WEBSITES_PORT* výhradně.

**Je nutné implementovat HTTPS v mé vlastní kontejner?**

Ne, platformu zpracovává ukončení protokolu HTTPS na sdílené elementy front end.

## <a name="multi-container-with-docker-compose-and-kubernetes"></a>Více kontejnerů pomocí Docker Compose a Kubernetes

**Konfigurování Azure kontejneru registru (ACR) pro použití s více kontejneru**

Abyste mohli používat ACR s více kontejneru **všechny Image kontejneru** musí být hostované na stejném serveru registru ACR. Jakmile jsou na stejném serveru registru, musíte se k vytvoření nastavení aplikace a potom aktualizovat konfigurační soubor Docker Compose nebo Kubernetes, zahrnout název ACR bitové kopie.

Vytvořte následující nastavení aplikace:

- DOCKER_REGISTRY_SERVER_USERNAME
- DOCKER_REGISTRY_SERVER_URL (úplná adresa URL, například: https://<server-name>.azurecr.io)
- DOCKER_REGISTRY_SERVER_PASSWORD (Povolit přístup správce v nastavení ACR)

V rámci konfiguračního souboru referenční bitové kopie ACR jako v následujícím příkladu:

```yaml
image: <server-name>.azurecr.io/<image-name>:<tag>
```

**Jak poznám, které kontejner je dostupný na Internetu?**

- Pouze jeden kontejner může být otevřené pro přístup
- Pouze port 80 a 8080 je přístupný (zveřejněné porty)

Zde jsou pravidla pro určování, které kontejneru je přístupná – v pořadí podle priority:

- Nastavení aplikace `WEBSITES_WEB_CONTAINER_NAME` nastavena na název kontejneru
- První kontejner, aby definovat port 80 nebo 8080
- Pokud žádná z výše uvedeného není true, budou přístupné kontejneru první definované v souboru (vystavený)

## <a name="pricing-and-sla"></a>Ceny a smlouva SLA

**Co je, ceny, teď, když je všeobecně dostupná služba?**

Budou se účtovat normální cenách služby Azure App Service pro počet hodin, které vaše aplikace běží.

## <a name="other-questions"></a>Další otázky

**Jaké jsou podporovanými znaky v názvech nastavení aplikace?**

Pro nastavení aplikace můžete použít pouze písmena (A-Z, a – z), čísla (0-9) a podtržítko (_).

**Kde můžete požádat o nové funkce?**

Můžete odeslat si na [fóru pro zpětnou vazbu webové aplikace](https://aka.ms/webapps-uservoice). Přidejte "[Linux]" na název vaší představu.

## <a name="next-steps"></a>Další postup

- [Co je Azure App Service v systému Linux?](app-service-linux-intro.md)
- [Nastavení přípravných prostředí ve službě Azure App Service](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Průběžné nasazování pomocí webové aplikace pro kontejnery](./app-service-linux-ci-cd.md)
