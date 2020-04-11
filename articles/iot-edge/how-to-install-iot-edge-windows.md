---
title: Instalace Azure IoT Edge do Windows | Dokumenty společnosti Microsoft
description: Pokyny k instalaci Azure IoT Edge ve Windows 10, Windows Serveru a Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: kgremban
ms.openlocfilehash: 61b382f1c286209a12d0be39a81e6817806d3251
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113459"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Instalace modulu runtime Azure IoT Edge ve Windows

Azure IoT Edge runtime je to, co změní zařízení na zařízení IoT Edge. Runtime lze nasadit na zařízení tak malá jako Raspberry Pi nebo velká jako průmyslový server. Jakmile je zařízení nakonfigurované s runtime IoT Edge, můžete do něj začít nasazovat obchodní logiku z cloudu.

Další informace o runtime IoT Edge najdete [v tématu Principy runtime Azure IoT Edge a jeho architektury](iot-edge-runtime.md).

Tento článek uvádí postup instalace runtime Azure IoT Edge do systému Windows x64 (AMD/Intel) pomocí kontejnerů Windows.

> [!NOTE]
> Známý problém operačního systému Windows zabraňuje přechodu do režimu spánku a hibernaci stavu napájení při spuštění modulů IoT Edge (kontejnerů Windows Nano Server izolovaných procesem). Tento problém má vliv na životnost baterie v zařízení.
>
> Jako řešení, pomocí příkazu `Stop-Service iotedge` zastavit všechny spuštěné moduly IoT Edge před použitím těchto stavů napájení.

Použití linuxových kontejnerů v systémech Windows není doporučená nebo podporovaná produkční konfigurace pro Azure IoT Edge. Nicméně, to může být použit pro účely vývoje a testování. Další informace najdete [v tématu Použití IoT Edge ve Windows ke spuštění linuxových kontejnerů](how-to-install-iot-edge-windows-with-linux.md).

Informace o tom, co je součástí nejnovější verze IoT Edge, najdete v [tématu Azure IoT Edge verze](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Požadavky

V této části můžete zkontrolovat, jestli vaše zařízení s Windows podporuje IoT Edge, a připravit ho na modul kontejneru před instalací.

### <a name="supported-windows-versions"></a>Podporované verze systému Windows

IoT Edge pro Windows vyžaduje Windows verze 1809/build 17763, což je nejnovější [sestavení dlouhodobé podpory systému Windows](https://docs.microsoft.com/windows/release-information/). Podpora skladových položk pro windows, podívejte se, co je podporováno na základě toho, zda připravujete na scénáře výroby nebo scénáře vývoje a testování:

* **Produkční prostředí**: Nejnovější informace o tom, které operační systémy jsou aktuálně podporované pro produkční scénáře, najdete v [tématu Azure IoT Edge podporované systémy](support.md#operating-systems).
* **Vývoj a testování**: Pro scénáře vývoje a testování lze azure IoT Edge s kontejnery Windows nainstalovat do libovolné verze Windows 10 nebo Windows Serveru 2019, která podporuje funkci kontejnerů.

Zařízení IoT Core musí obsahovat volitelnou funkci kontejnerů Windows jádra IoT pro podporu runtime IoT Edge. Pomocí následujícího příkazu ve [vzdálené relaci prostředí PowerShell](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell) zkontrolujte, zda jsou v zařízení podporovány kontejnery Windows:

```powershell
Get-Service vmcompute
```

Pokud je služba k dispozici, měli byste získat úspěšnou odpověď se stavem služby, který je uveden jako **spuštěný**. Pokud `vmcompute` služba není nalezena, vaše zařízení nesplňuje požadavky na IoT Edge. Obraťte se na poskytovatele hardwaru a požádejte o podporu této funkce.

### <a name="prepare-for-a-container-engine"></a>Připravte se na kontejnerový motor

Azure IoT Edge spoléhá na modul kontejneru [kompatibilní s OCI.](https://www.opencontainers.org/) Pro produkční scénáře použijte modul Moby zahrnutý v instalačním skriptu ke spuštění kontejnerů systému Windows na zařízení se systémem Windows.

## <a name="install-iot-edge-on-a-new-device"></a>Instalace IoT Edge na nové zařízení

>[!NOTE]
>Softwarové balíčky Azure IoT Edge podléhají licenčním podmínkám umístěným v balíčcích (v adresáři LICENCE). Před použitím balíčku si přečtěte licenční podmínky. Vaše instalace a používání balíčku představuje váš souhlas s těmito podmínkami. Pokud nesouhlasíte s licenčními podmínkami, balíček nepoužívejte.

Skript Prostředí PowerShell stáhne a nainstaluje demon zabezpečení Azure IoT Edge. Daemon zabezpečení pak spustí první ze dvou modulů runtime, agenta IoT Edge, který umožňuje vzdálené nasazení jiných modulů.

>[!TIP]
>Pro zařízení IoT Core doporučujeme spustit instalační příkazy pomocí relace RemotePowerShell. Další informace naleznete [v tématu Using PowerShell for Windows IoT](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell).

Při první instalaci runtime IoT Edge na zařízení, je třeba zřídit zařízení s identitou z centra IoT. Jediné zařízení IoT Edge lze zřídit ručně pomocí připojovacího řetězce zařízení poskytovaného službou IoT Hub. Nebo můžete použít službu Zřizování zařízení (DPS) k automatickému zřizování zařízení, což je užitečné, když máte mnoho zařízení k nastavení. V závislosti na volbě zřizování zvolte příslušný instalační skript.

Následující části popisují běžné případy použití a parametry instalačního skriptu IoT Edge na novém zařízení.

### <a name="option-1-install-and-manually-provision"></a>Možnost 1: Instalace a ruční zřizování

V této první možnosti zadáte **připojovací řetězec zařízení** generovaný službai IoT Hub pro zřízení zařízení.

Tento příklad ukazuje ruční instalaci s kontejnery systému Windows:

1. Pokud jste tak ještě neučinili, zaregistrujte nové zařízení IoT Edge a načtěte **připojovací řetězec zařízení**. Zkopírujte připojovací řetězec, který chcete použít později v této části. Tento krok můžete provést pomocí následujících nástrojů:

   * [portál Azure](how-to-register-device.md#register-in-the-azure-portal)
   * [Azure CLI](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio Code](how-to-register-device.md#register-with-visual-studio-code)

2. Spusťte PowerShell jako správce.

   >[!NOTE]
   >K instalaci IoT Edge použijte relaci AMD64 v Prostředí PowerShell, ne powershellu (x86). Pokud si nejste jisti, který typ relace používáte, spusťte následující příkaz:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. Příkaz **Deploy-IoTEdge** zkontroluje, zda je váš počítač se systémem Windows v podporované verzi, zapne funkci kontejnerů a poté stáhne modul runtime moby a modul runtime IoT Edge. Příkaz je výchozí pro použití kontejnerů systému Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. V tomto okamžiku se zařízení IoT Core mohou automaticky restartovat. K restartování vás mohou vyzvat jiná zařízení s Windows 10 nebo Windows Server. Pokud ano, restartujte zařízení nyní. Jakmile je vaše zařízení připravené, spusťte PowerShell znovu jako správce.

5. Příkaz **Initialize-IoTEdge** konfiguruje runtime IoT Edge ve vašem počítači. Příkaz výchozí ruční zřizování s kontejnery systému Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. Po zobrazení výzvy zadejte připojovací řetězec zařízení, který jste načetli v kroku 1. Připojovací řetězec zařízení přidruží fyzické zařízení k ID zařízení v centru IoT Hub.

   Připojovací řetězec zařízení má následující formát a neměl by obsahovat uvozovky:`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. Pomocí kroků v [části Ověření úspěšné instalace](#verify-successful-installation) zkontrolujte stav IoT Edge na vašem zařízení.

Při ruční instalaci a zřizování zařízení můžete k úpravě instalace použít další parametry, včetně:

* Přímý provoz projít proxy serverem
* Nasměrovat instalační program do offline adresáře
* Deklarujte konkrétní bitovou kopii kontejneru agenta a zadejte pověření, pokud je v soukromém registru

Další informace o těchto možnostech instalace získáte přeskočením a dozvíte se o [všech parametrech instalace](#all-installation-parameters).

### <a name="option-2-install-and-automatically-provision"></a>Možnost 2: Instalace a automatické zřizování

V této druhé možnosti zřídíte zařízení pomocí služby zřizování zařízení služby IoT Hub. Zadejte **ID oboru** z instance služby device provisioning service spolu s dalšími informacemi specifickými pro preferovaný [mechanismus ověřování](../iot-dps/concepts-security.md#attestation-mechanism):

* [Vytvoření a zřízení simulovaného zařízení IoT Edge s virtuálním čipem TPM v systému Windows](how-to-auto-provision-simulated-device-windows.md)
* [Vytvoření a zřízení simulovaného zařízení IoT Edge pomocí certifikátů X.509](how-to-auto-provision-x509-certs.md)
* [Vytvoření a zřízení zařízení IoT Edge pomocí symetrického atestace klíče](how-to-auto-provision-symmetric-keys.md)

Při automatické instalaci a zřizování zařízení můžete k úpravě instalace použít další parametry, včetně:

* Přímý provoz projít proxy serverem
* Nasměrovat instalační program do offline adresáře
* Deklarujte konkrétní bitovou kopii kontejneru agenta a zadejte pověření, pokud je v soukromém registru

Další informace o těchto možnostech instalace získáte v tomto článku nebo přeskočíte, abyste se dozvěděli o [všech parametrech instalace](#all-installation-parameters).

## <a name="offline-or-specific-version-installation"></a>Instalace offline nebo konkrétní verze

Během instalace se stáhnou tři soubory:

* Skript prostředí PowerShell, který obsahuje pokyny k instalaci
* Kabina Microsoft Azure IoT Edge, která obsahuje daemon zabezpečení IoT Edge (iotedged), modul kontejneru Moby a moby cli
* Instalační program redistribuovatelného balíčku Visual C++ (VC runtime)

Pokud bude vaše zařízení během instalace offline nebo pokud chcete nainstalovat konkrétní verzi ioT Edge, můžete si tyto soubory stáhnout předem do zařízení. Když je čas na instalaci, namiřte instalační skript na adresář, který obsahuje stažené soubory. Instalační program nejprve zkontroluje tento adresář a potom stáhne pouze součásti, které nebyly nalezeny. Pokud jsou všechny soubory k dispozici offline, můžete nainstalovat bez připojení k internetu.

K aktualizaci ioT edge můžete také použít parametr cesty instalace offline. Další informace naleznete [v tématu Aktualizace daemonu zabezpečení IoT Edge a runtime](how-to-update-iot-edge.md).

1. Nejnovější instalační soubory IoT Edge spolu s předchozími verzemi najdete v [tématu Verze Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

2. Najděte verzi, kterou chcete nainstalovat, a stáhněte si následující soubory z části **Datové zdroje** v poznámkách k verzi do zařízení IoT:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab z verzí 1.0.9 nebo novější, nebo Microsoft-Azure-IoTEdge.cab z verzí 1.0.8 a starší.

   Microsoft-Azure-IotEdge-arm32.cab je k dispozici také od 1.0.9 pouze pro účely testování. IoT Edge není aktuálně podporovánna zařízeních s Windows ARM32.

   Je důležité použít skript prostředí PowerShell ze stejné verze jako soubor CAB, který používáte, protože funkce se mění tak, aby podporovaly funkce v každé verzi.

3. Pokud soubor CAB, který jste stáhli, obsahuje příponu architektury, přejmenujte soubor na jenom **Microsoft-Azure-IoTEdge.cab**.

4. Volitelně můžete stáhnout instalační program pro visual c++ redistribuovatelné. Skript prostředí PowerShell například používá tuto verzi: [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe). Uložte instalační program do stejné složky na zařízení IoT jako soubory IoT Edge.

5. Chcete-li instalovat s offline součástmi, [dot source](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing) místní kopii skriptu Prostředí PowerShell. Potom použijte `-OfflineInstallationPath` parametr jako součást `Deploy-IoTEdge` příkazu a zadejte absolutní cestu k adresáři souborů. Například:

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   Příkaz nasazení použije všechny součásti nalezené v místním adresáři souborů. Pokud chybí soubor CAB nebo instalační program Visual C++, pokusí se je stáhnout.

6. Spusťte `Initialize-IoTEdge` příkaz a zřídíte zařízení s identitou v centru IoT Hub. Buď zadejte připojovací řetězec zařízení pro ruční zřizování, nebo zvolte jednu z metod popsaných v předchozí části [automaticky zřizování.](#option-2-install-and-automatically-provision)

   Pokud se zařízení po `Deploy-IoTEdge`spuštění restartovalo , dot `Initialize-IoTEdge`source powershellový skript před spuštěním .

Další informace o možnosti instalace offline získáte přeskočením o [všech parametrech instalace](#all-installation-parameters).

## <a name="verify-successful-installation"></a>Ověření úspěšné instalace

Zkontrolujte stav služby IoT Edge. Měl by být uveden jako spuštěný.  

```powershell
Get-Service iotedge
```

Zkontrolujte protokoly služby z posledních 5 minut. Pokud jste právě dokončili instalaci runtime IoT Edge, může se zobrazit seznam chyb z doby mezi **spuštěním Deploy-IoTEdge** a **Initialize-IoTEdge**. Tyto chyby jsou očekávány, protože služba se pokouší spustit před konfigurací.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Spusťte automatickou kontrolu nejčastějších chyb konfigurace a sítě.

```powershell
iotedge check
```

Seznam spuštěných modulů. Po nové instalaci, jediný modul, který byste měli vidět běží je **edgeAgent**. Po [prvním nasazení modulů IoT Edge](how-to-deploy-modules-portal.md) se v zařízení spustí i druhý systémový modul **edgeHub**.

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>Správa kontejnerů modulů

Služba IoT Edge vyžaduje modul kontejneru spuštěný na vašem zařízení. Když nasadíte modul do zařízení, modul runtime IoT Edge používá modul kontejneru k vyprosit image kontejneru z registru v cloudu. Služba IoT Edge umožňuje interakci s moduly a načítat protokoly, ale někdy můžete chtít použít modul kontejneru k interakci s kontejneru samotného.

Další informace o konceptech modulů [najdete v tématu Principy modulů Azure IoT Edge](iot-edge-modules.md).

Pokud používáte kontejnery Windows na zařízení Windows IoT Edge, pak instalace IoT Edge zahrnovala modul kontejneru Moby. Motor Moby byl založen na stejných standardech jako Docker a byl navržen tak, aby běžel paralelně na stejném počítači jako Docker Desktop. Z tohoto důvodu pokud chcete cílit na kontejnery spravované motorem Moby, musíte konkrétně cílit na tento motor namísto Dockeru.

Chcete-li například vypsat všechny image Dockeru, použijte následující příkaz:

```powershell
docker images
```

Chcete-li vypsat všechny snímky Moby, upravte stejný příkaz s ukazatelem na motor Moby:

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

Identifikátor URI motoru je uveden ve výstupu instalačního skriptu nebo jej najdete v části nastavení modulu runtime kontejneru pro soubor config.yaml.

![moby_runtime uri v souboru config.yaml](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

Další informace o příkazech, které můžete použít k interakci s kontejnery a obrázky spuštěnými na vašem zařízení, naleznete v [tématu Rozhraní příkazového řádku Dockeru](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="uninstall-iot-edge"></a>Odinstalace ioT edge

Pokud chcete odebrat instalaci IoT Edge ze zařízení se systémem Windows, použijte následující příkaz z okna prostředí PowerShell pro správu. Tento příkaz odebere modul runtime IoT Edge spolu s vaší existující konfigurací a daty motoru Moby.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Příkaz Uninstall-IoTEdge nefunguje v systému Windows IoT Core. Chcete-li odebrat IoT Edge ze zařízení Windows IoT Core, musíte znovu nasadit bitovou kopii Windows IoT Core.

Další informace o možnostech odinstalace `Get-Help Uninstall-IoTEdge -full`získáte pomocí příkazu .

## <a name="verify-installation-script"></a>Ověření instalačního skriptu

Instalační příkazy uvedené v tomto článku používají rutinu Invoke-WebRequest k `aka.ms/iotedge-win`vyžádání instalačního skriptu od aplikace . Tento odkaz odkazuje`IoTEdgeSecurityDaemon.ps1` na skript z nejnovější [verze IoT Edge](https://github.com/Azure/azure-iotedge/releases). Můžete si také stáhnout tento skript nebo verzi skriptu z konkrétní verze a spustit instalační příkazy na zařízení IoT Edge.

Zadaný skript je podepsán pro zvýšení zabezpečení. Podpis můžete ověřit stažením skriptu do zařízení a následným spuštěním následujícího příkazu PowerShellu:

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

Stav výstupu je **Platný,** pokud je podpis ověřen.

## <a name="all-installation-parameters"></a>Všechny parametry instalace

V předchozích částech byly zavedeny běžné scénáře instalace s příklady použití parametrů k úpravě instalačního skriptu. Tato část obsahuje referenční tabulky běžných parametrů používaných k instalaci, aktualizaci nebo odinstalaci ioT Edge.

### <a name="deploy-iotedge"></a>Nasazení-IoTEdge

Příkaz Deploy-IoTEdge stáhne a nasadí daemon zabezpečení IoT Edge a jeho závislosti. Příkaz nasazení přijímá mimo jiné tyto běžné parametry. Úplný seznam použijte pomocí `Get-Help Deploy-IoTEdge -full`příkazu .  

| Parametr | Přípustné hodnoty | Komentáře |
| --------- | --------------- | -------- |
| **Kontejneros** | **Windows** nebo **Linux** | Pokud není zadán žádný operační systém kontejneru, windows je výchozí hodnota.<br><br>Pro kontejnery Windows IoT Edge používá modul kontejneru moby součástí instalace. Pro linuxové kontejnery je třeba nainstalovat modul kontejneru před zahájením instalace. |
| **Proxy server** | Proxy URL | Tento parametr zahrňte, pokud vaše zařízení potřebuje projít proxy serverem, aby se dostalo k internetu. Další informace naleznete [v tématu Konfigurace komunikace zařízení IoT Edge prostřednictvím serveru proxy](how-to-configure-proxy-support.md). |
| **OfflineInstalační program** | Cesta k adresáři | Pokud je tento parametr zahrnut, instalační program zkontroluje uvedený adresář pro cab IoT Edge a VC Runtime MSI soubory potřebné pro instalaci. Všechny soubory, které nebyly nalezeny v adresáři, budou staženy. Pokud jsou oba soubory v adresáři, můžete nainstalovat IoT Edge bez připojení k internetu. Tento parametr můžete také použít k použití konkrétní verze. |
| **InvokeWebRequestParameters** | Hashovatelné parametry a hodnoty | Během instalace je podáno několik webových požadavků. Toto pole slouží k nastavení parametrů pro tyto webové požadavky. Tento parametr je užitečný pro konfiguraci pověření pro proxy servery. Další informace naleznete [v tématu Konfigurace komunikace zařízení IoT Edge prostřednictvím serveru proxy](how-to-configure-proxy-support.md). |
| **RestartIfPotřebné** | Žádná | Tento příznak umožňuje skript nasazení restartovat počítač bez výzvy, v případě potřeby. |

### <a name="initialize-iotedge"></a>Inicializovat-IoTEdge

Příkaz Initialize-IoTEdge konfiguruje IoT Edge pomocí připojovacího řetězce zařízení a provozních podrobností. Většina informací generovaných tímto příkazem je pak uložena v souboru iotedge\config.yaml. Příkaz inicializace přijímá mimo jiné tyto běžné parametry. Úplný seznam použijte pomocí `Get-Help Initialize-IoTEdge -full`příkazu .

| Parametr | Přípustné hodnoty | Komentáře |
| --------- | --------------- | -------- |
| **Ruční** | Žádný | **Parametr přepínače**. Pokud není zadán žádný typ zřizování, ruční je výchozí hodnota.<br><br>Deklaruje, že poskytnete připojovací řetězec zařízení pro ruční zřízení zařízení |
| **Dps** | Žádný | **Parametr přepínače**. Pokud není zadán žádný typ zřizování, ruční je výchozí hodnota.<br><br>Prohlašuje, že budete poskytovat ID oboru služby Zřizování zařízení (DPS) a ID registrace vašeho zařízení, které se zřídí prostřednictvím DPS.  |
| **DeviceConnectionString** | Připojovací řetězec ze zařízení IoT Edge registrovaného v centru IoT Hub v jednoduchých uvozovkách | **Vyžadováno** pro ruční zřizování. Pokud nezadáte připojovací řetězec v parametrech skriptu, budete vyzváni k zadání. |
| **Scopeid** | ID oboru z instance služby Device Provisioning Service přidružené k vašemu centru IoT Hub. | **Požadováno** pro zřizování DPS. Pokud v parametrech skriptu nezadáte ID oboru, budete vyzváni k zadání. |
| **Id registrace** | ID registrace generované vaším zařízením | **Vyžadováno** pro zřizování DPS, pokud používáte čip TPM nebo symetrické atestace klíče. **Nepovinné,** pokud používáte osvědčení o certifikátu X.509. |
| **X509IdentityCertificate** | Cesta identifikátoru URI k certifikátu identity zařízení X.509 v zařízení. | **Vyžadováno** pro zřizování DPS, pokud používáte osvědčení Certifikát U X.509. |
| **X509IdentitySoukromý klíč** | Cesta identifikátoru URI ke klíči certifikátu identity zařízení X.509 v zařízení. | **Vyžadováno** pro zřizování DPS, pokud používáte osvědčení Certifikát U X.509. |
| **Symetrický klíč** | Symetrický klíč používaný k zajištění identity zařízení IoT Edge při použití DPS | **Vyžadováno** pro zřizování DPS, pokud používáte symetrické atestace klíče. |
| **Kontejneros** | **Windows** nebo **Linux** | Pokud není zadán žádný operační systém kontejneru, windows je výchozí hodnota.<br><br>Pro kontejnery Windows IoT Edge používá modul kontejneru moby součástí instalace. Pro linuxové kontejnery je třeba nainstalovat modul kontejneru před zahájením instalace. |
| **InvokeWebRequestParameters** | Hashovatelné parametry a hodnoty | Během instalace je podáno několik webových požadavků. Toto pole slouží k nastavení parametrů pro tyto webové požadavky. Tento parametr je užitečný pro konfiguraci pověření pro proxy servery. Další informace naleznete [v tématu Konfigurace komunikace zařízení IoT Edge prostřednictvím serveru proxy](how-to-configure-proxy-support.md). |
| **Obrázek agenta** | Identifikátor URI obrázku agenta IoT Edge | Ve výchozím nastavení používá nová instalace IoT Edge nejnovější postupnou značku pro bitovou kopii agenta IoT Edge. Tento parametr slouží k nastavení konkrétní značky pro verzi obrázku nebo k poskytnutí vlastní image agenta. Další informace naleznete [v tématu Understand IoT Edge tags](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Username** | Uživatelské jméno registru kontejneru | Tento parametr použijte pouze v případě, že nastavíte parametr -AgentImage na kontejner v soukromém registru. Zadejte uživatelské jméno s přístupem k registru. |
| **Heslo** | Řetězec bezpečného hesla | Tento parametr použijte pouze v případě, že nastavíte parametr -AgentImage na kontejner v soukromém registru. Zadejte heslo pro přístup k registru. |

### <a name="update-iotedge"></a>Aktualizace-IoTEdge

| Parametr | Přípustné hodnoty | Komentáře |
| --------- | --------------- | -------- |
| **Kontejneros** | **Windows** nebo **Linux** | Pokud není zadán žádný kontejner ový operační systém, výchozí hodnotou je systém Windows. Pro kontejnery systému Windows bude součástí instalace modul kontejneru. Pro linuxové kontejnery je třeba nainstalovat modul kontejneru před zahájením instalace. |
| **Proxy server** | Proxy URL | Tento parametr zahrňte, pokud vaše zařízení potřebuje projít proxy serverem, aby se dostalo k internetu. Další informace naleznete [v tématu Konfigurace komunikace zařízení IoT Edge prostřednictvím serveru proxy](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Hashovatelné parametry a hodnoty | Během instalace je podáno několik webových požadavků. Toto pole slouží k nastavení parametrů pro tyto webové požadavky. Tento parametr je užitečný pro konfiguraci pověření pro proxy servery. Další informace naleznete [v tématu Konfigurace komunikace zařízení IoT Edge prostřednictvím serveru proxy](how-to-configure-proxy-support.md). |
| **OfflineInstalační program** | Cesta k adresáři | Pokud je tento parametr zahrnut, instalační program zkontroluje uvedený adresář pro cab IoT Edge a VC Runtime MSI soubory potřebné pro instalaci. Všechny soubory, které nebyly nalezeny v adresáři, budou staženy. Pokud jsou oba soubory v adresáři, můžete nainstalovat IoT Edge bez připojení k internetu. Tento parametr můžete také použít k použití konkrétní verze. |
| **RestartIfPotřebné** | Žádná | Tento příznak umožňuje skript nasazení restartovat počítač bez výzvy, v případě potřeby. |

### <a name="uninstall-iotedge"></a>Odinstalovat-IoTEdge

| Parametr | Přípustné hodnoty | Komentáře |
| --------- | --------------- | -------- |
| **Síla** | Žádná | Tento příznak vynutí odinstalaci v případě, že předchozí pokus o odinstalaci byl neúspěšný.
| **RestartIfPotřebné** | Žádná | Tento příznak umožňuje odinstalovat skript restartovat počítač bez výzvy, v případě potřeby. |

## <a name="next-steps"></a>Další kroky

Teď, když máte zařízení IoT Edge zřízené s nainstalovaným modulem runtime, můžete [nasadit moduly IoT Edge](how-to-deploy-modules-portal.md).

Pokud máte problémy s instalací IoT Edge správně, podívejte se na stránku [řešení potíží.](troubleshoot.md)

Informace o aktualizaci existující instalace na nejnovější verzi technologie IoT Edge naleznete v [tématu Aktualizace daemonu zabezpečení IoT Edge a runtime](how-to-update-iot-edge.md).
