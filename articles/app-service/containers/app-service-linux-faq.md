---
title: Aplikační služba Azure v systému Linux – nejčastější dotazy | Microsoft Docs
description: Aplikační služba Azure v systému Linux – nejčastější dotazy.
keywords: služby Azure app service, webové aplikace, – nejčastější dotazy, linux, operačních systémů
services: app-service
documentationCenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: aelnably;wesmc
ms.openlocfilehash: 8d25c70a0e5db92bca6f3970049a2e1325fe124b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="azure-app-service-on-linux-faq"></a>V systému Linux nejčastější dotazy týkající se služby Azure App Service

S vydáním služby App Service v systému Linux pracujeme na přidání funkcí a vylepšení naše platforma provádění. Tento článek obsahuje odpovědi na otázky, aby naše zákazníky mít byla zprávu s požadavkem nedávno.

Pokud máte dotazy, komentáře na článek a jsme budete nejdříve hovor.

## <a name="built-in-images"></a>Předdefinované bitové kopie

**Chci rozvětvit předdefinované Docker kontejnerů, které poskytuje platformu. Kde najdu tyto soubory?**

Všechny soubory Docker můžete najít na [Githubu](https://github.com/azure-app-service). Můžete najít všechny kontejnery Docker na [úložiště Docker Hub](https://hub.docker.com/u/appsvc/).

**Co jsou očekávané hodnoty pro oddíl spuštění souboru, pro které je možné nakonfigurovat zásobník runtime?**

Pro Node.js je třeba zadat konfigurační soubor PM2 nebo souboru skriptu. .NET Core zadejte název vaší zkompilované knihovny DLL. Pro Ruby můžete zadat Ruby skript, který chcete inicializovat vaší aplikace pomocí.

## <a name="management"></a>Správa

**Co se stane při stisknutí tlačítka restartování na portálu Azure?**

Tato akce je stejná jako Docker restartování.

**Můžete použít Secure Shell (SSH) pro připojení k aplikaci kontejneru virtuální počítač (VM)?**

Ano, můžete to udělat pomocí zdrojové lokality řízení správy (SCM).

> [!NOTE] 
> Můžete také připojit ke kontejneru aplikaci přímo z místní vývojovém počítači pomocí protokolu SSH, SFTP nebo Visual Studio Code (pro živé ladění aplikace Node.js). Další informace najdete v tématu [vzdálené ladění a SSH ve službě App Service v systému Linux](https://aka.ms/linux-debug).
>

**Jak můžete vytvořit plán služby App Service Linux prostřednictvím sady SDK nebo šablonu Azure Resource Manager?**

Je nutné nastavit **vyhrazené** pole aplikace služby k *true*.

## <a name="continuous-integration-and-deployment"></a>Průběžná integrace a nasazování

**Webová aplikace se pořád používají staré obrázek kontejner Docker po aktualizovali bitovou kopii na úložiště Docker Hub. Podporujete průběžnou integraci a nasazení vlastních kontejnerů?**

Nastavit průběžnou integraci a nasazení pro Azure kontejneru registru nebo DockerHub Image kontrolou v následujícím článku [průběžné nasazování s webovou aplikaci pro kontejnery](./app-service-linux-ci-cd.md). Pro privátní registrech můžete aktualizovat kontejneru zastavení a spuštění webové aplikace. Nebo můžete změnit nebo přidat nastavení fiktivní aplikace můžete vynutit aktualizaci vašeho kontejneru.

**Podporujete pracovní prostředí?**

Ano.

**Je možné používat *nasazení webu* nasazení webová aplikace?**

Ano, je nutné nastavit aplikaci názvem `WEBSITE_WEBDEPLOY_USE_SCM` k *false*.

**Nasazení Git Moje aplikace nezdaří, při použití Linux webové aplikace. Jak můžu alternativní řešení problému?**

Pokud nasazení Git webové aplikace v systému Linux, je možné následující alternativní možnosti nasazení kódu aplikace:

- Použít funkci nastavené průběžné doručování (Preview): můžete ukládat zdrojového kódu vaší aplikace do úložiště Team Services Git nebo úložiště GitHub používat Azure nastavené průběžné doručování. Další podrobnosti najdete v tématu [postup konfigurace nastavené průběžné doručování pro webovou aplikaci Linux](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/).

- Použít [ZIP nasadit rozhraní API](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file): používat toto rozhraní API [SSH do vaší webové aplikace](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-ssh-support#making-a-client-connection) a přejděte do složky, ve které chcete nasadit kód. Spusťte následující příkazy:

   ```
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Pokud dojde k chybě `curl` příkaz nebyl nalezen, je nutné nainstalovat curl pomocí `apt-get install curl` předtím, než spustíte předchozí `curl` příkaz.

## <a name="language-support"></a>Podpora jazyků

**Chci používat objekty websockets v mé aplikaci Node.js, jakékoli speciální nastavení nebo konfigurace nastavení?**

Ano, zakažte `perMessageDeflate` v kódu Node.js straně serveru. Například pokud používáte socket.io, postupujte takto:
```
var io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Podporujete nezkompilované aplikace .NET Core?**

Ano.

**Podporujete autora jako správce závislostí pro aplikace PHP?**

Ano. Během nasazení Git Kudu by měl zjistit, že nasazujete aplikace PHP (díky přítomnost souboru composer.lock), a Kudu poté aktivuje autora instalace pro vás.

## <a name="custom-containers"></a>Vlastní kontejnery

**Používám vlastní vlastní kontejner. Požadovaná platforma připojit k serveru SMB pro sdílení `/home/` adresáře.**

Můžete to udělat nastavením `WEBSITES_ENABLE_APP_SERVICE_STORAGE` nastavení aplikace nastavte na *true* nebo zcela odeberete aplikaci nastavení. Mějte na paměti, že to způsobí, že kontejner restartování při úložiště platformy prochází změnu. 

>[!NOTE]
>Pokud `WEBSITES_ENABLE_APP_SERVICE_STORAGE` nastavení je *false*, `/home/` directory nebude sdílen napříč instancemi škálování a soubory, které jsou zapsány existuje nebude zachován po restartování.

**Mé vlastní kontejner trvá dlouhou dobu spuštění a platformou restartuje kontejner před dokončením spuštění.**

Můžete nakonfigurovat množství času, kterou platformu bude čekat před jeho restartování vašeho kontejneru. Chcete-li tak učinit, nastavte `WEBSITES_CONTAINER_START_TIME_LIMIT` nastavení aplikace nastavte na hodnotu, kterou chcete. Výchozí hodnota je 230 sekund a maximální hodnota je 600 sekund.

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

## <a name="pricing-and-sla"></a>Ceny a smlouva SLA

**Co je, ceny, teď, když je všeobecně dostupná služba?**

Budou se účtovat normální cenách služby Azure App Service pro počet hodin, které vaše aplikace běží.

## <a name="other-questions"></a>Další otázky

**Jaké jsou podporovanými znaky v názvech nastavení aplikace?**

Pro nastavení aplikace můžete použít pouze písmena (A-Z, a – z), čísla (0-9) a podtržítko (_).

**Kde můžete požádat o nové funkce?**

Můžete odeslat si na [fóru pro zpětnou vazbu webové aplikace](https://aka.ms/webapps-uservoice). Přidejte "[Linux]" na název vaší představu.

## <a name="next-steps"></a>Další postup

* [Co je Azure App Service v systému Linux?](app-service-linux-intro.md)
* [Nastavení přípravných prostředí ve službě Azure App Service](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Průběžné nasazování pomocí webové aplikace pro kontejnery](./app-service-linux-ci-cd.md)
