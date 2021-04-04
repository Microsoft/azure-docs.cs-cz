---
title: Nejčastější dotazy ke spouštění integrovaných kontejnerů
description: V Azure App Service najdete odpovědi na nejčastější dotazy týkající se integrovaných kontejnerů pro Linux.
keywords: Azure App Service, Web App, nejčastější dotazy, Linux, OSS, Web App for Containers, multi-Container, více kontejnerů
author: msangapu-msft
ms.topic: article
ms.date: 10/30/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 6faec27bf368b3eb45e05a91307df6027bda93b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100093994"
---
# <a name="azure-app-service-on-linux-faq"></a>Nejčastější dotazy k Azure App Service v Linuxu

S vydáním App Service v systému Linux pracujeme na přidávání funkcí a vylepšování naší platformy. Tento článek obsahuje odpovědi na otázky, které naši zákazníci v poslední době požádali o nás.

Pokud máte otázku, přidejte komentář k tomuto článku.

## <a name="built-in-images"></a>Integrované image

**Chci rozvětvit vestavěné kontejnery Docker, které poskytuje platforma. Kde najdu tyto soubory?**

Všechny soubory Docker můžete najít na [GitHubu](https://github.com/azure-app-service). Všechny kontejnery Docker můžete najít v [Docker Hub](https://hub.docker.com/u/appsvc/).

<a id="#startup-file"></a>

**Jaké jsou očekávané hodnoty oddílu spouštěcí soubor při konfiguraci zásobníku modulu runtime?**

| Zásobník           | Očekávaná hodnota                                                                         |
|-----------------|----------------------------------------------------------------------------------------|
| Java SE         | příkaz pro spuštění aplikace JAR (například `java -jar /home/site/wwwroot/app.jar --server.port=80` ) |
| Tomcat          | umístění skriptu pro provedení potřebných konfigurací (například `/home/site/deployments/tools/startup_script.sh` )          |
| Node.js         | konfigurační soubor konfiguračního PM2 nebo soubor skriptu                                |
| .NET Core       | název zkompilované knihovny DLL jako `dotnet <myapp>.dll`                                 |
| Ruby            | skript Ruby, se kterým chcete aplikaci inicializovat                     |

Tyto příkazy nebo skripty se spustí po spuštění předdefinovaného kontejneru Docker, ale před spuštěním kódu aplikace.

## <a name="management"></a>Správa

**Co se stane po stisknutí tlačítka restartovat v Azure Portal?**

Tato akce je stejná jako restartování Docker.

**Můžu použít Secure Shell (SSH) pro připojení k virtuálnímu počítači kontejneru aplikace?**

Ano, můžete to provést prostřednictvím lokality správy zdrojového kódu (SCM).

> [!NOTE]
> Ke kontejneru aplikace se můžete připojit také přímo z místního vývojového počítače pomocí SSH, SFTP nebo Visual Studio Code (pro živé ladění aplikací Node.js). Další informace najdete v tématu [Vzdálené ladění a SSH ve službě App Service v Linuxu](https://azure.github.io/AppService/2018/05/07/New-SSH-Experience-and-Remote-Debugging-for-Linux-Web-Apps.html).
>

**Jak mohu vytvořit plán App Service pro Linux prostřednictvím sady SDK nebo šablony Azure Resource Manager?**

Nastavte **vyhrazené** pole služby App Service na *hodnotu true*.

## <a name="continuous-integration-and-deployment"></a>Průběžná integrace a nasazování

**Moje webová aplikace stále používá starou image kontejneru Docker po aktualizaci image v Docker Hub. Podporujete průběžnou integraci a nasazování vlastních kontejnerů?**

Ano, pokud chcete nastavit průběžnou integraci a nasazování pro Azure Container Registry nebo Dockerhubu pomocí [průběžného nasazování s Web App for Containers](./deploy-ci-cd-custom-container.md). U privátních registrů můžete kontejner aktualizovat tím, že zastavíte a pak spustíte svou webovou aplikaci. Nebo můžete změnit nebo přidat nastavení fiktivní aplikace k vynucení aktualizace kontejneru.

**Podporujete pracovní prostředí?**

Ano.

**Můžu k nasazení webové aplikace použít *WebDeploy/MSDeploy* ?**

Ano, je třeba nastavit nastavení aplikace `WEBSITE_WEBDEPLOY_USE_SCM` na *hodnotu NEPRAVDA*.

**Nasazení Git moje aplikace se při použití webové aplikace pro Linux nezdařilo. Jak můžu tento problém obejít?**

Pokud se nasazení Git nepovede do vaší webové aplikace pro Linux, vyberte jednu z následujících možností pro nasazení kódu aplikace:

- Použití funkce průběžné doručování (Preview): zdrojový kód vaší aplikace můžete uložit v úložišti Git Azure DevOps nebo v úložišti GitHubu, abyste mohli používat průběžné doručování Azure. Další informace najdete v tématu [Konfigurace průběžného doručování pro webovou aplikaci pro Linux](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/).

- Použijte [rozhraní API pro nasazení zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file): pro použití tohoto rozhraní API, [SSH do vaší webové aplikace](configure-linux-open-ssh-session.md) a přechod do složky, do které chcete kód nasadit. Spusťte následující kód:

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Pokud se zobrazí chyba, že `curl` příkaz nebyl nalezen, ujistěte se, že jste nainstalovali kudrlinkou pomocí `apt-get install curl` před spuštěním předchozího `curl` příkazu.

## <a name="language-support"></a>Podpora jazyků

**Chci v aplikaci Node.js použít webové sokety, všechna speciální nastavení nebo konfigurace, které se mají nastavit?**

Ano, zakázat `perMessageDeflate` kód Node.js na straně serveru. Například pokud používáte socket.io, použijte následující kód:

```nodejs
const io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Podporujete nekompilované aplikace .NET Core?**

Ano.

**Podporujete skladatele jako správce závislostí pro aplikace PHP?**

Ano, Kudu by během nasazování Gitu měli zjistit, že nasazujete aplikaci PHP (s tím, že zadáváte soubor skladatele. lock), a Kudu potom spustí instalaci skladatele.

## <a name="custom-containers"></a>Vlastní kontejnery

**Používám vlastní kontejner. Chci, aby platforma připojovat sdílenou složku SMB k `/home/` adresáři.**

Pokud `WEBSITES_ENABLE_APP_SERVICE_STORAGE` nastavení **neurčíte** nebo nastavíte na *false*, nebude se `/home/` adresář **sdílet** mezi instancemi škálování a zapsané soubory **se nebudou uchovávat** v restartování. Při explicitním nastavení `WEBSITES_ENABLE_APP_SERVICE_STORAGE` na *hodnotu true* se připojení povolí.

**Spuštění vlastního kontejneru trvá dlouhou dobu a platforma restartuje kontejner před tím, než se začne dokončí.**

Můžete nakonfigurovat dobu, po kterou bude platforma čekat, než restartuje váš kontejner. Provedete to tak, že nastavíte `WEBSITES_CONTAINER_START_TIME_LIMIT` nastavení aplikace na požadovanou hodnotu. Výchozí hodnota je 230 sekund a maximální hodnota je 1800 sekund.

**Jaký je formát adresy URL serveru privátního registru?**

Zadejte úplnou adresu URL registru, včetně `http://` nebo `https://` .

**Jaký je formát názvu bitové kopie v možnosti privátního registru?**

Přidejte úplný název bitové kopie včetně adresy URL privátního registru (například myacr.azurecr.io/dotnet:latest). Názvy obrázků, které používají vlastní port, [nelze zadat prostřednictvím portálu](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). K nastavení `docker-custom-image-name` použijte [ `az` Nástroj příkazového řádku](/cli/azure/webapp/config/container#az-webapp-config-container-set).

**Můžu na vlastní imagi kontejneru zobrazit víc než jeden port?**

Nepodporujeme vystavení více než jednoho portu.

**Můžu přinášet vlastní úložiště?**

Ano, [Přineste si vlastní úložiště](./configure-connect-to-azure-storage.md) ve verzi Preview.

**Proč nemůžu procházet systém souborů vlastního kontejneru nebo spuštěné procesy z webu SCM?**

Web SCM se spouští v samostatném kontejneru. Nemůžete kontrolovat systém souborů ani spouštět procesy kontejneru aplikace.

**Můj vlastní kontejner naslouchá na jiném portu než port 80. Jak můžu nakonfigurovat aplikaci, aby směrovala požadavky na tento port?**

Automatické zjišťování portů jsme nastavili. Můžete také zadat nastavení aplikace s názvem *WEBSITES_PORT* a přidělit jí hodnotu očekávaného čísla portu. Dřív používala nastavení aplikace *portu* na platformě. Plánujeme, aby toto nastavení aplikace bylo Zastaralé a používalo výhradně *WEBSITES_PORT* .

**Potřebuji v mém vlastním kontejneru implementovat protokol HTTPS?**

Ne, platforma zpracovává ukončení HTTPS na sdílených front-endy.

## <a name="multi-container-with-docker-compose"></a>Více kontejnerů s Docker Compose

**Návody nakonfigurovat Azure Container Registry (ACR) pro použití s více kontejnery?**

Aby bylo možné používat ACR s více kontejnery, je nutné, aby **všechny image kontejneru** byly hostovány na stejném serveru registru ACR. Až budou na stejném serveru registru, budete muset vytvořit nastavení aplikace a pak aktualizovat konfigurační soubor Docker Compose tak, aby zahrnoval název bitové kopie ACR.

Vytvořte následující nastavení aplikace:

- DOCKER_REGISTRY_SERVER_USERNAME
- DOCKER_REGISTRY_SERVER_URL (úplná adresa URL, např. `https://<server-name>.azurecr.io` )
- DOCKER_REGISTRY_SERVER_PASSWORD (povolit přístup správce v nastavení ACR)

V konfiguračním souboru, odkazujte na obrázek ACR jako v následujícím příkladu:

```yaml
image: <server-name>.azurecr.io/<image-name>:<tag>
```

**Návody víte, který kontejner je dostupný k Internetu?**

- Pro přístup se dá otevřít jenom jeden kontejner.
- K dispozici jsou pouze porty 80 a 8080 (vystavené porty).

Tady jsou pravidla pro určení kontejneru, který je přístupný – v pořadí podle priority:

- Nastavení aplikace `WEBSITES_WEB_CONTAINER_NAME` je nastaveno na název kontejneru
- První kontejner pro definování portu 80 nebo 8080
- Pokud žádná z výše uvedených možností není pravdivá, bude první kontejner definovaný v souboru přístupný (vystaveno).


## <a name="web-sockets"></a>Webové sokety

Webové sokety jsou podporovány v aplikacích pro Linux.

> [!IMPORTANT]
> Webové sokety se v současnosti nepodporují pro aplikace pro Linux v Free App Servicech plánech. Pracujeme na odebrání tohoto omezení a naplánujte podporu až 5 připojení webového soketu v Free App Servicech plánech.

## <a name="pricing-and-sla"></a>Ceny a smlouvy SLA

**Jaké jsou ceny, teď je služba všeobecně dostupná?**

Ceny se liší podle SKU a oblasti, ale na naší stránce s cenami můžete zobrazit další podrobnosti: [App Service ceny](https://azure.microsoft.com/pricing/details/app-service/linux/).

## <a name="other-questions"></a>Další dotazy

**Co znamená, že požadovaná funkce není ve skupině prostředků k dispozici?**

Tato zpráva se může zobrazit při vytváření webové aplikace pomocí Azure Resource Manager (ARM). Na základě současného omezení pro stejnou skupinu prostředků nemůžete kombinovat aplikace pro Windows a Linux ve stejné oblasti.

**Jaké jsou podporované znaky v názvech nastavení aplikace?**

Pro nastavení aplikace lze použít pouze písmena (A-Z, A-z), čísla (0-9) a podtržítko (_).

**Kde můžu požádat o nové funkce?**

Svůj nápad můžete odeslat na [fóru Web Apps Feedback](https://aka.ms/webapps-uservoice). Přidejte "[Linux]" do titulu vaší myšlenky.

## <a name="next-steps"></a>Další kroky

- [Co je Azure App Service v systému Linux?](overview.md#app-service-on-linux)
- [Nastavení přípravných prostředí ve službě Azure App Service](deploy-staging-slots.md)
- [Průběžné nasazování pomocí Web App for Containers](./deploy-ci-cd-custom-container.md)
