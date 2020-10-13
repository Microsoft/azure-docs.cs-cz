---
title: Konfigurace vlastního kontejneru
description: Přečtěte si, jak nakonfigurovat vlastní kontejner v Azure App Service. Tento článek ukazuje nejběžnější konfigurační úlohy.
ms.topic: article
ms.date: 09/22/2020
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 5b1bf9b205fc1eb90c6eeae3a101def764381213
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91264571"
---
# <a name="configure-a-custom-container-for-azure-app-service"></a>Konfigurace vlastního kontejneru pro Azure App Service

V tomto článku se dozvíte, jak nakonfigurovat vlastní kontejner pro spuštění v Azure App Service.

::: zone pivot="container-windows"

Tato příručka poskytuje klíčové koncepty a pokyny pro kontejnerování aplikací pro Windows v App Service. Pokud jste nikdy Azure App Service nepoužili, postupujte jako první v prvním [rychlém startu vlastního kontejneru](quickstart-custom-container.md) a [kurzu](tutorial-custom-container.md) .

::: zone-end

::: zone pivot="container-linux"

Tato příručka poskytuje klíčové koncepty a pokyny pro kontejnerování aplikací pro Linux v App Service. Pokud jste nikdy Azure App Service nepoužili, postupujte jako první v prvním [rychlém startu vlastního kontejneru](quickstart-custom-container.md) a [kurzu](tutorial-custom-container.md) . K dispozici je také rychlý Start a [kurz](tutorial-multi-container-app.md) [aplikace pro více kontejnerů](quickstart-multi-container.md) .

::: zone-end

::: zone pivot="container-windows"

## <a name="supported-parent-images"></a>Podporované nadřazené image

Pro vlastní image Windows musíte zvolit správnou [nadřazenou Image (základní image)](https://docs.docker.com/develop/develop-images/baseimages/) pro požadované rozhraní:

- Pokud chcete nasadit aplikace .NET Framework, použijte nadřazenou image založenou na vydaných verzích základního kanálu pro Windows Server [(LTSC)](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) . 
- Pokud chcete nasadit aplikace .NET Core, použijte nadřazenou bitovou kopii založenou na verzi Windows Server nano [pro kanál pro údržbu (SAC)](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) . 

Stažení nadřazené image při spuštění aplikace nějakou dobu trvá. Čas spuštění však můžete zkrátit použitím některé z následujících nadřazených imagí, které jsou již uložené v mezipaměti ve službě Azure App Service:

- [MCR.Microsoft.com/Windows/ServerCore](https://hub.docker.com/_/microsoft-windows-servercore): 2004
- [MCR.Microsoft.com/Windows/ServerCore](https://hub.docker.com/_/microsoft-windows-servercore): ltsc2019
- [MCR.Microsoft.com/DotNET/Framework/ASPNET](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/): 4,8-windowsservercore-2004
- [MCR.Microsoft.com/DotNET/Framework/ASPNET](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/): 4,8-windowsservercore-ltsc2019
- [MCR.Microsoft.com/dotnet/Core/Runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3,1-nanoserver-2004
- [MCR.Microsoft.com/dotnet/Core/Runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3,1-nanoserver-1909
- [MCR.Microsoft.com/dotnet/Core/Runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3,1-nanoserver-1903
- [MCR.Microsoft.com/dotnet/Core/Runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3,1-nanoserver-1809
- [MCR.Microsoft.com/dotnet/Core/ASPNET](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3,1-nanoserver-2004
- [MCR.Microsoft.com/dotnet/Core/ASPNET](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3,1-nanoserver-1909
- [MCR.Microsoft.com/dotnet/Core/ASPNET](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3,1-nanoserver-1903
- [MCR.Microsoft.com/dotnet/Core/ASPNET](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3,1-nanoserver-1809

::: zone-end

## <a name="change-the-docker-image-of-a-custom-container"></a>Změna image Docker vlastního kontejneru

Chcete-li změnit stávající vlastní aplikaci kontejneru z aktuální image Docker na novou bitovou kopii, použijte následující příkaz:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <docker-hub-repo>/<image>
```

## <a name="use-an-image-from-a-private-registry"></a>Použití image z privátního registru

Pokud chcete použít image z privátního registru, například Azure Container Registry, spusťte následující příkaz:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

V případě *\<username>* a *\<password>* Zadejte přihlašovací údaje pro váš účet privátního registru.

## <a name="i-dont-see-the-updated-container"></a>Nevidím aktualizovaný kontejner

Pokud změníte nastavení kontejneru Docker tak, aby odkazovalo na nový kontejner, může trvat několik minut, než aplikace zachová požadavky HTTP z nového kontejneru. I když je nový kontejner načítán a spuštěný, App Service nadále obsluhovat požadavky z původního kontejneru. Jenom v případě, že je nový kontejner spuštěný a připravený k přijímání požadavků, App Service začít odesílat požadavky do něj.

## <a name="how-container-images-are-stored"></a>Způsob ukládání imagí kontejneru

Při prvním spuštění vlastní image Docker v App Service App Service provede `docker pull` a vyžádá všechny vrstvy imagí. Tyto vrstvy jsou uložené na disku, jako kdybyste používali Docker v místním prostředí. Pokaždé, když se aplikace restartuje, App Service provede `docker pull` , ale pouze přebírá vrstvy, které se změnily. Pokud se žádné změny nezměnily, App Service použije existující vrstvy na místním disku.

Pokud aplikace změní výpočetní instance z jakéhokoli důvodu, například horizontální navýšení nebo snížení úrovně cenové úrovně, App Service musí všechny vrstvy znovu vyčítat. Totéž platí, pokud nahorizontální navýšení kapacity pro přidání dalších instancí. Existují také výjimečné případy, kdy se instance aplikace mohou měnit bez operace škálování.

## <a name="configure-port-number"></a>Konfigurace čísla portu

Ve výchozím nastavení App Service předpokládá, že váš vlastní kontejner naslouchá na portu 80. Pokud Váš kontejner naslouchá na jiném portu, nastavte `WEBSITES_PORT` v aplikaci App Service nastavení aplikace. Můžete ho nastavit přes [Cloud Shell](https://shell.azure.com). V bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

V PowerShellu:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_PORT"="8000"}
```

App Service v současné době umožňuje vašemu kontejneru vystavovat pouze jeden port pro požadavky HTTP. 

## <a name="configure-environment-variables"></a>Konfigurace proměnných prostředí

Vlastní kontejner může používat proměnné prostředí, které je třeba zadat externě. Můžete je předat prostřednictvím [Cloud Shell](https://shell.azure.com). V bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings DB_HOST="myownserver.mysql.database.azure.com"
```

V PowerShellu:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"DB_HOST"="myownserver.mysql.database.azure.com"}
```

Když je vaše aplikace spuštěná, nastavení App Service aplikace se vloží do procesu automaticky jako proměnné prostředí. 

::: zone pivot="container-windows"
V případě kontejnerů založených na službě IIS nebo .NET Framework (4,0 nebo vyšší) jsou vloženy do `System.ConfigurationManager` nastavení aplikace .NET a připojovací řetězce automaticky pomocí App Service. Pro všechny ostatní jazyky nebo rozhraní jsou k dispozici jako proměnné prostředí pro proces s jedním z následujících odpovídajících předpon:

- `APPSETTING_`
- `SQLCONTR_`
- `MYSQLCONTR_`
- `SQLAZURECOSTR_`
- `POSTGRESQLCONTR_`
- `CUSTOMCONNSTR_`

::: zone-end

::: zone pivot="container-linux"

Tato metoda funguje jak pro aplikace s jedním kontejnerem, tak pro aplikace s více kontejnery, kde jsou proměnné prostředí určeny v souboru *Docker-Compose. yml* .

::: zone-end

## <a name="use-persistent-shared-storage"></a>Použití trvalého sdíleného úložiště

::: zone pivot="container-windows"

Pomocí adresáře *C:\home* v systému souborů vaší aplikace můžete uchovávat soubory mezi restarty a sdílet je mezi instancemi. K `C:\home` dispozici je ve vaší aplikaci, aby mohla vaše aplikace kontejneru přistupovat k trvalému úložišti.

Pokud je trvalé úložiště zakázané, zápisy do `C:\home` adresáře se nezachovají. [Protokoly hostitelů Docker a protokoly kontejnerů](#access-diagnostic-logs) se ukládají do výchozího trvalého sdíleného úložiště, které není připojené ke kontejneru. Pokud je povolené trvalé úložiště, všechny zápisy do `C:\home` adresáře jsou trvalé a můžou k němu přistupovat všechny instance aplikace s možností horizontálního rozšíření kapacity a protokol je dostupný na adrese `C:\home\LogFiles` .

Ve výchozím nastavení je trvalé úložiště *zakázané* a nastavení se v nastavení aplikace nezveřejňuje. Pokud ho chcete povolit, nastavte `WEBSITES_ENABLE_APP_SERVICE_STORAGE` nastavení aplikace přes [Cloud Shell](https://shell.azure.com). V bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
```

V PowerShellu:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_ENABLE_APP_SERVICE_STORAGE"=true}
```

::: zone-end

::: zone pivot="container-linux"

Pomocí adresáře */Home* v systému souborů vaší aplikace můžete uchovávat soubory mezi restarty a sdílet je mezi instancemi. K `/home` dispozici je ve vaší aplikaci, aby mohla vaše aplikace kontejneru přistupovat k trvalému úložišti.

Když je trvalé úložiště zakázané, pak se zápisy do `/home` adresáře neukládají mezi restarty aplikace nebo mezi několika instancemi. Jedinou výjimkou je `/home/LogFiles` adresář, který se používá k uložení protokolů Docker a kontejner. Když je povolené trvalé úložiště, všechny zápisy do tohoto `/home` adresáře jsou trvalé a můžou k němu mít pøístup všechny instance aplikace s možností horizontálního rozšíření kapacity.

Ve výchozím nastavení je trvalé úložiště *povolené* a nastavení se v nastavení aplikace nezveřejňuje. Pokud ho chcete zakázat, nastavte `WEBSITES_ENABLE_APP_SERVICE_STORAGE` nastavení aplikace přes [Cloud Shell](https://shell.azure.com). V bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

V PowerShellu:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_ENABLE_APP_SERVICE_STORAGE"=false}
```

::: zone-end

> [!NOTE]
> Můžete také [nakonfigurovat vlastní trvalé úložiště](configure-connect-to-azure-storage.md).

## <a name="detect-https-session"></a>Zjistit relaci HTTPS

App Service ukončí protokol TLS/SSL na front-endy. To znamená, že žádosti TLS/SSL se do vaší aplikace nikdy nezískají. Nemusíte a neměli byste do své aplikace implementovat žádnou podporu TLS/SSL. 

Front-endy se nachází v datových centrech Azure. Pokud s vaší aplikací používáte protokol TLS/SSL, váš provoz přes Internet bude vždycky bezpečně zašifrovaný.

::: zone pivot="container-windows"

## <a name="customize-aspnet-machine-key-injection"></a>Přizpůsobení vkládání klíče ASP.NET počítače

 Během spuštění kontejneru se automaticky vygenerované klíče vloží do kontejneru jako klíče počítače pro kryptografické rutiny ASP.NET. [Tyto klíče najdete v kontejneru](#connect-to-the-container) , a to tak, že vyhledáte následující proměnné prostředí: `MACHINEKEY_Decryption` , `MACHINEKEY_DecryptionKey` , `MACHINEKEY_ValidationKey` , `MACHINEKEY_Validation` . 

Nové klíče při každém restartování můžou resetovat ověřování ASP.NET Forms a stav zobrazení, pokud vaše aplikace na nich závisí. Pokud chcete zabránit automatickému obnovení klíčů, [nastavte je ručně jako App Service nastavení aplikace](#configure-environment-variables). 

## <a name="connect-to-the-container"></a>Připojit ke kontejneru

Můžete se připojit k kontejneru Windows přímo pro úlohy diagnostiky tak, že přejdete na `https://<app-name>.scm.azurewebsites.net/DebugConsole` . Funguje to následovně:

- Konzola ladění umožňuje spustit interaktivní příkazy, jako je spouštění relací PowerShellu, kontrola klíčů registru a procházení celého systému souborů kontejnerů.
- Funguje samostatně z grafického prohlížeče nad ním, který zobrazuje jenom soubory ve [sdíleném úložišti](#use-persistent-shared-storage).
- V aplikaci s možností horizontálního rozšíření je konzola ladění připojená k jedné z instancí kontejneru. Můžete vybrat jinou instanci z rozevíracího seznamu **instance** v horní nabídce.
- Všechny změny, které provedete v kontejneru z konzoly, *se* neuloží při restartování vaší aplikace (s výjimkou změn ve sdíleném úložišti), protože nejsou součástí image Docker. Chcete-li zachovat změny, jako je například nastavení registru a instalace softwaru, zajistěte jejich součást souboru Dockerfile.

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

App Service protokoluje akce hostitele Docker a také aktivity z kontejneru. Protokoly z hostitele Docker (protokoly platforem) jsou dodávány ve výchozím nastavení, ale protokoly aplikací nebo protokoly webového serveru z kontejneru je potřeba povolit ručně. Další informace najdete v tématech [Povolení protokolování aplikace](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer) a [Povolení protokolování webového serveru](troubleshoot-diagnostic-logs.md#enable-web-server-logging). 

Existuje několik způsobů, jak získat přístup k protokolům Docker:

- [V Azure Portal](#in-azure-portal)
- [Z konzoly Kudu](#from-the-kudu-console)
- [S rozhraním API Kudu](#with-the-kudu-api)
- [Odeslat protokoly do Azure monitoru](troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview)

### <a name="in-azure-portal"></a>V Azure Portal

Protokoly Docker se zobrazují na portálu na stránce **nastavení kontejneru** v aplikaci. Protokoly jsou zkráceny, ale můžete stáhnout všechny protokoly kliknutím na tlačítko **Stáhnout**. 

### <a name="from-the-kudu-console"></a>Z konzoly Kudu

Přejděte na `https://<app-name>.scm.azurewebsites.net/DebugConsole` složku soubory protokolů **LogFiles** a kliknutím na ni Zobrazte jednotlivé soubory protokolů. Chcete-li stáhnout celý adresář souborů **protokolů** , klikněte na ikonu **Stáhnout** nalevo od názvu adresáře. K této složce můžete přistupovat také pomocí klienta FTP.

V terminálu konzoly nemůžete `C:\home\LogFiles` ve výchozím nastavení přistupovat ke složce, protože trvalé sdílené úložiště není povolené. Pokud chcete toto chování povolit v terminálu konzoly, [Povolte trvalé sdílené úložiště](#use-persistent-shared-storage).

Pokud se pokusíte stáhnout protokol Docker, který se aktuálně používá pomocí klienta FTP, může se při uzamčení souboru zobrazit chyba.

### <a name="with-the-kudu-api"></a>S rozhraním API Kudu

Přejděte přímo na, `https://<app-name>.scm.azurewebsites.net/api/logs/docker` abyste viděli metadata pro protokoly Docker. V seznamu se může zobrazit více než jeden soubor protokolu a `href` vlastnost umožňuje stáhnout soubor protokolu přímo. 

Chcete-li stáhnout všechny protokoly společně v jednom souboru ZIP, přístup `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip` .

## <a name="customize-container-memory"></a>Přizpůsobení paměti kontejneru

Ve výchozím nastavení jsou všechny kontejnery Windows nasazené v Azure App Service omezené na 1 GB RAM. Tuto hodnotu můžete změnit zadáním `WEBSITE_MEMORY_LIMIT_MB` nastavení aplikace prostřednictvím [Cloud Shell](https://shell.azure.com). V bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_MEMORY_LIMIT_MB=2000
```

V PowerShellu:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_MEMORY_LIMIT_MB"=2000}
```

Hodnota je definována v MB a musí být menší a rovna celkové fyzické paměti hostitele. Například v plánu App Service s 8 GB paměti RAM nesmí kumulativní součet `WEBSITE_MEMORY_LIMIT_MB` pro všechny aplikace překročit 8 GB. Informace o tom, kolik paměti je k dispozici pro jednotlivé cenové úrovně, najdete v tématu [App Service ceny](https://azure.microsoft.com/pricing/details/app-service/windows/)v části **Plán Premium (Windows) Plan** .

## <a name="customize-the-number-of-compute-cores"></a>Přizpůsobení počtu výpočetních jader

Ve výchozím nastavení se kontejner Windows spouští se všemi dostupnými jádry vaší zvolené cenové úrovně. Možná budete chtít snížit počet jader, které používá pracovní slot, například. Chcete-li snížit počet jader používaných kontejnerem, nastavte `WEBSITE_CPU_CORES_LIMIT` nastavení aplikace na preferovaný počet jader. Můžete ho nastavit přes [Cloud Shell](https://shell.azure.com). V bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --slot staging --settings WEBSITE_CPU_CORES_LIMIT=1
```

V PowerShellu:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_CPU_CORES_LIMIT"=1}
```

> [!NOTE]
> Aktualizace nastavení aplikace aktivuje automatické restartování, což způsobuje minimální výpadky. V případě produkční aplikace byste ji měli zvážit v příchodu do přípravného slotu, změnit nastavení aplikace v přípravné patici a pak ji znovu přepnout do produkčního prostředí.

Přizpůsobené číslo si můžete ověřit tak, že na konzoli Kudu ( `https://<app-name>.scm.azurewebsites.net` ) a zadáte následující příkazy pomocí PowerShellu. Každý příkaz vypíše číslo.

```PowerShell
Get-ComputerInfo | ft CsNumberOfLogicalProcessors # Total number of enabled logical processors. Disabled processors are excluded.
Get-ComputerInfo | ft CsNumberOfProcessors # Number of physical processors.
```

Procesory můžou být vícejádrovými procesory nebo procesory s vlákny. Informace o tom, kolik jader je k dispozici pro jednotlivé cenové úrovně, najdete v tématu [App Service ceny](https://azure.microsoft.com/pricing/details/app-service/windows/)v části **Plán Premium (Windows) Plan** .

## <a name="customize-health-ping-behavior"></a>Přizpůsobení chování při testu stavu

App Service předpokládá, že kontejner bude úspěšně spuštěn při spuštění kontejneru a reaguje na příkazy HTTP HTTP. Požadavek na test stavu, který je hlavičkou, je kontejnerem `User-Agent= "App Service Hyper-V Container Availability Check"` . Pokud se kontejner spustí, ale po uplynutí určité doby nereaguje na příkaz if, App Service zaznamená událost do protokolu Docker, že se kontejner nespustil. 

Pokud je vaše aplikace náročná na prostředky, kontejner nemusí v čase reagovat na příkaz HTTP test. Chcete-li řídit akce při selhání příkazového testu HTTP, nastavte `CONTAINER_AVAILABILITY_CHECK_MODE` nastavení aplikace. Můžete ho nastavit přes [Cloud Shell](https://shell.azure.com). V bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings CONTAINER_AVAILABILITY_CHECK_MODE="ReportOnly"
```

V PowerShellu:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"CONTAINER_AVAILABILITY_CHECK_MODE"="ReportOnly"}
```

Následující tabulka uvádí možné hodnoty:

| Hodnota | Označení |
| - | - |
| **Opravdu** | Restartování kontejneru po třech po sobě jdoucích kontrolách dostupnosti |
| **ReportOnly** | Výchozí hodnota. Po třech po sobě jdoucích kontrol dostupnosti nerestartuje kontejner, ale v protokolech Docker pro kontejner. |
| **Vypnuto** | Nekontrolovat dostupnost. |

## <a name="support-for-group-managed-service-accounts"></a>Podpora skupinových účtů spravované služby

Skupinové účty spravované služby (účty gMSA) se v současnosti nepodporují v kontejnerech Windows v App Service.

::: zone-end

::: zone pivot="container-linux"

## <a name="enable-ssh"></a>Povolit SSH

SSH umožňuje zabezpečenou komunikaci mezi kontejnerem a klientem. Aby mohl vlastní kontejner podporovat SSH, musíte ho přidat do samotného souboru dockerfileu.

> [!TIP]
> Všechny integrované kontejnery Linux přidaly instrukce SSH do úložišť imagí. Pomocí následujících pokynů můžete v [ úložištiNode.js 10,14](https://github.com/Azure-App-Service/node/blob/master/10.14) zjistit, jak je tato funkce povolená.

- Použijte instrukci [Run](https://docs.docker.com/engine/reference/builder/#run) k instalaci serveru SSH a nastavte heslo pro kořenový účet na `"Docker!"` . Například pro Image založenou na systému [Alpine Linux](https://hub.docker.com/_/alpine)budete potřebovat následující příkazy:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Tato konfigurace neumožňuje externí připojení ke kontejneru. SSH je k dispozici pouze prostřednictvím `https://<app-name>.scm.azurewebsites.net` a ověřeno s přihlašovacími údaji pro publikování.

- Přidejte [Tento soubor sshd_config](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) do úložiště imagí a pomocí instrukce [copy](https://docs.docker.com/engine/reference/builder/#copy) zkopírujte soubor do adresáře */etc/ssh/* . Další informace o *sshd_config* souborů najdete v [dokumentaci k OpenBSD](https://man.openbsd.org/sshd_config).

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Soubor *sshd_config* musí obsahovat následující položky:
    > - `Ciphers` musí obsahovat alespoň jednu položku v tomto seznamu: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs` musí obsahovat alespoň jednu položku v tomto seznamu: `hmac-sha1,hmac-sha1-96`.

- K otevření portu 2222 v kontejneru použijte instrukci [vystavení](https://docs.docker.com/engine/reference/builder/#expose) . I když je známé heslo ke kořenovému adresáři, není port 2222 přístupný z Internetu. Je přístupná pouze kontejnery v rámci mostu sítě privátní virtuální sítě.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- V spouštěcím skriptu pro svůj kontejner spusťte server SSH.

    ```bash
    /usr/sbin/sshd
    ```

    Příklad najdete v tématu Jak výchozí [ kontejnerNode.js 10,14](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) SPUSTÍ Server SSH.

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="configure-multi-container-apps"></a>Konfigurace aplikací s více kontejnery

- [Použít trvalé úložiště v Docker Compose](#use-persistent-storage-in-docker-compose)
- [Omezení verze Preview](#preview-limitations)
- [Možnosti Docker Compose](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Použít trvalé úložiště v Docker Compose

Aplikace s více kontejnery, jako je WordPress, potřebují pro správné fungování trvalé úložiště. Pokud ho chcete povolit, vaše konfigurace Docker Compose musí odkazovat na umístění úložiště *mimo* váš kontejner. Umístění úložiště ve vašem kontejneru neukládají změny po restartování aplikace.

Nastavením `WEBSITES_ENABLE_APP_SERVICE_STORAGE` nastavení aplikace pomocí příkazu [AZ WebApp config appSettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) v [Cloud Shell](https://shell.azure.com)povolte trvalé úložiště.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

V souboru *Docker-Compose. yml* namapujte `volumes` možnost na `${WEBAPP_STORAGE_HOME}` . 

`WEBAPP_STORAGE_HOME` je proměnná prostředí ve službě App Service, která je namapovaná na trvalé úložiště vaší aplikace. Například:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>Omezení verze Preview

Vícenásobný kontejner je aktuálně ve verzi Preview. Následující funkce App Service platformy nejsou podporovány:

- Ověřování/autorizace
- Spravované identity
- CORS

### <a name="docker-compose-options"></a>Možnosti Docker Compose

Následující seznamy obsahují podporované a nepodporované možnosti konfigurace Docker Compose:

#### <a name="supported-options"></a>Podporované možnosti

- command
- entrypoint
- environment
- image
- ports
- restart
- services
- volumes

#### <a name="unsupported-options"></a>Nepodporované možnosti

- build (nepovoleno)
- depends_on (ignorováno)
- networks (ignorováno)
- secrets (ignorováno)
- jiné porty než 80 a 8080 (ignorováno)

> [!NOTE]
> Všechny další možnosti, které nejsou explicitně vyvolány, jsou v Public Preview ignorovány.

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: migrace vlastního softwaru na Azure App Service pomocí vlastního kontejneru](tutorial-custom-container.md)

::: zone pivot="container-linux"

> [!div class="nextstepaction"]
> [Kurz: aplikace pro více kontejnerů WordPress](tutorial-multi-container-app.md)

::: zone-end

Nebo si přečtěte další zdroje informací:

[Načíst certifikát v kontejnerech Windows/Linux](configure-ssl-certificate-in-code.md#load-certificate-in-linuxwindows-containers)