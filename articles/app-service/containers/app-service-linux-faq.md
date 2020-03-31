---
title: Časté otázky ke spuštění vestavěných kontejnerů
description: Najděte odpovědi na nejčastější dotazy týkající se integrovaných kontejnerů Linuxu ve službě Azure App Service.
keywords: azure app service, webová aplikace, faq, linux, oss, webová aplikace pro kontejnery, multikontejner, multicontainer
author: msangapu-msft
ms.topic: article
ms.date: 10/30/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: f0a8b1758571a9473402d11a4d5141a11f76504d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245816"
---
# <a name="azure-app-service-on-linux-faq"></a>Nejčastější dotazy k Azure App Service v Linuxu

S vydáním App Service na Linuxu pracujeme na přidávání funkcí a vylepšování naší platformy. Tento článek obsahuje odpovědi na otázky, které nám naši zákazníci v poslední době kladou.

Pokud máte dotaz, komentář k tomuto článku.

## <a name="built-in-images"></a>Vestavěné obrázky

**Chci rozčlenit integrované kontejnery Dockeru, které poskytuje platforma. Kde najdu ty soubory?**

Všechny soubory Dockeru najdete na [GitHubu](https://github.com/azure-app-service). Všechny kontejnery Dockeru najdete v [Docker Hubu](https://hub.docker.com/u/appsvc/).

<a id="#startup-file"></a>

**Jaké jsou očekávané hodnoty pro část Spouštěcí soubor při konfiguraci zásobníku runtime?**

| Zásobník           | Očekávaná hodnota                                                                         |
|-----------------|----------------------------------------------------------------------------------------|
| Java SE         | příkaz pro spuštění aplikace JAR `java -jar /home/site/wwwroot/app.jar --server.port=80`(například) |
| Tomcat          | umístění skriptu pro provedení nezbytných konfigurací `/home/site/deployments/tools/startup_script.sh`(například)          |
| Node.js         | konfigurační soubor PM2 nebo soubor skriptu                                |
| .NET Core       | zkompilovaný název DLL jako`dotnet <myapp>.dll`                                 |
| Ruby            | rubínový skript, pomocí kterého chcete aplikaci inicializovat                     |

Tyto příkazy nebo skripty jsou spouštěny po spuštění integrovaného kontejneru Dockeru, ale před spuštěním kódu aplikace.

## <a name="management"></a>Správa

**Co se stane, když stisknu tlačítko restart na webu Azure Portal?**

Tato akce je stejná jako restartování Dockeru.

**Můžu použít Secure Shell (SSH) pro připojení k virtuálnímu počítači kontejneru aplikace (VM)?**

Ano, můžete to udělat prostřednictvím webu správy správy zdrojového kódu (SCM).

> [!NOTE]
> Ke kontejneru aplikace se můžete připojit také přímo z místního vývojového počítače pomocí SSH, SFTP nebo Visual Studio Code (pro živé ladění aplikací Node.js). Další informace najdete v tématu [Vzdálené ladění a SSH ve službě App Service v Linuxu](https://azure.github.io/AppService/2018/05/07/New-SSH-Experience-and-Remote-Debugging-for-Linux-Web-Apps.html).
>

**Jak můžu vytvořit plán linuxové služby app service prostřednictvím sady SDK nebo šablony Azure Resource Manager?**

Nastavte **vyhrazené** pole služby aplikace na *hodnotu true*.

## <a name="continuous-integration-and-deployment"></a>Průběžná integrace a nasazování

**Moje webová aplikace stále používá starou image kontejneru Dockeru poté, co jsem aktualizoval image v Docker Hubu. Podporujete průběžnou integraci a nasazování vlastních kontejnerů?**

Ano, chcete-li nastavit průběžnou integraci/nasazení pro Azure Container Registry nebo DockerHub, podle [sledování průběžného nasazení s webovou aplikací pro kontejnery](./app-service-linux-ci-cd.md). U soukromých registrů můžete kontejner aktualizovat zastavením a spuštěním webové aplikace. Nebo můžete změnit nebo přidat fiktivní nastavení aplikace vynutit aktualizaci kontejneru.

**Podporujete pracovní prostředí?**

Ano.

**Můžu k nasazení webové aplikace použít *WebDeploy/MSDeploy?***

Ano, nastavení aplikace s názvem `WEBSITE_WEBDEPLOY_USE_SCM` *false*.

**Nasazení aplikace Git se nezdaří při použití webové aplikace Linux. Jak mohu problém vyřešit?**

Pokud se nasazení Gitu nezdaří do vaší webové aplikace pro Linux, zvolte jednu z následujících možností nasazení kódu aplikace:

- Použití funkce Průběžné doručování (Preview): Zdrojový kód aplikace můžete uložit do úložiště Azure DevOps Git nebo úložiště GitHub u použití azure nepřetržitého doručování. Další informace najdete v tématu [Konfigurace webové aplikace průběžného doručování pro Linux](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/).

- Použití [rozhraní API nasazení ZIP](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file): Chcete-li použít toto rozhraní API, [SSH do webové aplikace](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-ssh-support) a přejděte do složky, do které chcete nasadit kód. Spusťte následující kód:

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Pokud se zobrazí chyba, že `curl` příkaz nebyl nalezen, ujistěte se, že nainstalujete curl pomocí `apt-get install curl` před spuštěním předchozího `curl` příkazu.

## <a name="language-support"></a>Podpora jazyků

**Chcete použít webové sokety v aplikaci Node.js, všechna zvláštní nastavení nebo konfigurace, které chcete nastavit?**

Ano, `perMessageDeflate` zakázat v kódu Node.js na straně serveru. Pokud například používáte socket.io, použijte následující kód:

```nodejs
const io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Podporujete nezkompilované aplikace .NET Core?**

Ano.

**Podporujete Composer jako správce závislostí pro aplikace PHP?**

Ano, během nasazení Gitu by měl Kudu zjistit, že nasazujete aplikaci PHP (díky přítomnosti souboru composer.lock) a Kudu pak spustí instalaci skladatele.

## <a name="custom-containers"></a>Vlastní kontejnery

**Používám vlastní kontejner. Chci, aby platforma připojit smb `/home/` sdílené složky do adresáře.**

Pokud `WEBSITES_ENABLE_APP_SERVICE_STORAGE` je nastavení **neurčeno** nebo `/home/` je nastaveno na *hodnotu true*, bude adresář **sdílen** mezi instancemi škálování a zapsané soubory **budou přetrvávat** v rámci restartování. Explicitně `WEBSITES_ENABLE_APP_SERVICE_STORAGE` nastavení *na false* zakáže připojení.

**Spuštění vlastního kontejneru trvá dlouho a platforma restartuje kontejner před dokončením spuštění.**

Můžete nakonfigurovat dobu, po kterou bude platforma čekat, než restartuje kontejner. Chcete-li tak `WEBSITES_CONTAINER_START_TIME_LIMIT` učinit, nastavte nastavení aplikace na požadovanou hodnotu. Výchozí hodnota je 230 sekund a maximální hodnota je 1800 sekund.

**Jaký je formát adresy URL soukromého serveru registru?**

Zadejte úplnou adresu `http://` `https://`URL registru, včetně nebo .

**Jaký je formát názvu obrázku v možnosti soukromého registru?**

Přidejte úplný název obrázku, včetně adresy URL soukromého registru (například myacr.azurecr.io/dotnet:latest). Názvy obrázků, které používají vlastní [port, nelze zadat prostřednictvím portálu](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Chcete-li nastavit `docker-custom-image-name`, použijte nástroj [ `az` příkazového řádku](https://docs.microsoft.com/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set).

**Mohu na vlastní image kontejneru vystavit více než jeden port?**

Nepodporujeme odhalení více než jednoho portu.

**Mohu si přinést vlastní úložný prostor?**

Ano, [přineste si vlastní úložiště](https://docs.microsoft.com/azure/app-service/containers/how-to-serve-content-from-azure-storage) je ve verzi preview.

**Proč nemohu procházet systém souborů vlastního kontejneru nebo spouštět procesy z webu SCM?**

Web SCM běží v samostatném kontejneru. Nelze zkontrolovat systém souborů nebo spuštěné procesy kontejneru aplikace.

**Můj vlastní kontejner naslouchá jinému portu než portu 80. Jak mohu nakonfigurovat aplikaci tak, aby směrovala požadavky na tento port?**

Máme automatickou detekci portů. Můžete také zadat nastavení aplikace s názvem *WEBSITES_PORT* a zadat hodnotu očekávaného čísla portu. Dříve platforma používala nastavení aplikace *PORT.* Plánujeme toto nastavení aplikace zamezit a používat výhradně *WEBSITES_PORT.*

**Musím implementovat https ve vlastním kontejneru?**

Ne, platforma zpracovává ukončení HTTPS na sdílených front-endech.

## <a name="multi-container-with-docker-compose"></a>Vícekontejnerů s dockerovým komponem

**Jak nakonfiguruji Azure Container Registry (ACR) pro použití s více kontejnery?**

Aby bylo možné používat ACR s více kontejnery, všechny **image kontejneru** musí být hostovány na stejném serveru registru ACR. Jakmile jsou na stejném serveru registru, budete muset vytvořit nastavení aplikace a potom aktualizovat konfigurační soubor Docker Compose tak, aby zahrnoval název bitové kopie ACR.

Vytvořte následující nastavení aplikace:

- DOCKER_REGISTRY_SERVER_USERNAME
- DOCKER_REGISTRY_SERVER_URL (úplná adresa `https://<server-name>.azurecr.io`URL, například: )
- DOCKER_REGISTRY_SERVER_PASSWORD (povolit přístup správce v nastavení ACR)

V konfiguračním souboru na obrázek ACR nalezněte jako následující příklad:

```yaml
image: <server-name>.azurecr.io/<image-name>:<tag>
```

**Jak poznám, který kontejner je přístupný z internetu?**

- Pro přístup může být otevřen pouze jeden kontejner
- Přístupný je pouze port 80 a 8080 (exponované porty)

Zde jsou pravidla pro určení, který kontejner je přístupný - v pořadí podle priority:

- Nastavení `WEBSITES_WEB_CONTAINER_NAME` aplikace nastavené na název kontejneru
- První kontejner definuji port 80 nebo 8080
- Pokud ani jeden z výše uvedených není pravda, první kontejner definovaný v souboru bude přístupný (vystavený)


## <a name="web-sockets"></a>Webové sokety

Webové sokety jsou podporovány v aplikacích pro Linux.

> [!IMPORTANT]
> Webové sokety nejsou aktuálně podporovány pro linuxové aplikace v plánech bezplatných služeb aplikací. Pracujeme na odstranění tohoto omezení a plánujeme podporovat až 5 připojení webových soketů v plánech služby Free App Service.

## <a name="pricing-and-sla"></a>Ceny a SLA

**Jaké jsou ceny, nyní, když je služba obecně dostupná?**

Ceny se liší podle skladové položky a oblasti, ale další podrobnosti můžete zobrazit na naší stránce s cenami: [Ceny služeb aplikace](https://azure.microsoft.com/pricing/details/app-service/linux/).

## <a name="other-questions"></a>Další otázky

**Co znamená "Požadovaná funkce není k dispozici ve skupině prostředků"?**

Tato zpráva se může zobrazit při vytváření webové aplikace pomocí Správce prostředků Azure (ARM). Na základě aktuálního omezení pro stejnou skupinu prostředků nelze kombinovat aplikace pro Windows a Linux ve stejné oblasti.

**Jaké jsou podporované znaky v názvech nastavení aplikace?**

Pro nastavení aplikace můžete použít pouze písmena (A-Z, a-z), čísla (0-9) a znak podtržítka (_).

**Kde mohu požádat o nové funkce?**

Svůj nápad můžete odeslat na fóru pro [zpětnou vazbu webových aplikací](https://aka.ms/webapps-uservoice). Přidejte "[Linux]" k názvu vašeho nápadu.

## <a name="next-steps"></a>Další kroky

- [Co je služba Azure App Service na Linuxu?](app-service-linux-intro.md)
- [Nastavení přípravných prostředí ve službě Azure App Service](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Průběžné nasazování pomocí webové aplikace pro kontejnery](./app-service-linux-ci-cd.md)
