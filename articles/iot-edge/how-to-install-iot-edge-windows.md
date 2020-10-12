---
title: Instalace Azure IoT Edge ve Windows | Microsoft Docs
description: Pokyny k instalaci Azure IoT Edge v systému Windows 10, Windows Server a Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: kgremban
ms.openlocfilehash: ba3e8b9d7649d56d1639f7f608d85a2da04ff74a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84465554"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Instalace modulu runtime Azure IoT Edge ve Windows

Azure IoT Edge modul runtime je tím, že zařízení přepíná do IoT Edge zařízení. Modul runtime se dá na zařízeních nasadit tak, jak malý, jako např. v/v jako průmyslový Server. Jakmile v zařízení nakonfigurujete modul runtime IoT Edge, můžete do něj z cloudu začít nasazovat obchodní logiku.

Další informace o modulu runtime IoT Edge najdete v tématu [pochopení Azure IoT Edge runtime a jeho architektury](iot-edge-runtime.md).

V tomto článku jsou uvedené kroky pro instalaci modulu runtime Azure IoT Edge v systému Windows x64 (AMD/Intel) pomocí kontejnerů Windows.

> [!NOTE]
> Známý problém s operačním systémem Windows brání přechodu do režimu spánku a hibernace, když IoT Edge moduly (kontejnery Windows nano serveru izolované na procesu). Tento problém má vliv na výdrž baterie zařízení.
>
> Jako alternativní řešení pomocí příkazu `Stop-Service iotedge` zastavte všechny běžící IoT Edge moduly předtím, než tyto stavy napájení využijete.

Použití kontejnerů Linux v systémech Windows není doporučená nebo podporovaná konfigurace produkčního prostředí pro Azure IoT Edge. Dá se ale použít pro účely vývoje a testování. Další informace najdete v tématu [použití IoT Edge ve Windows ke spouštění kontejnerů Linux](how-to-install-iot-edge-windows-with-linux.md).

Informace o tom, co je zahrnuto v nejnovější verzi IoT Edge, najdete v tématu [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Požadavky

Pomocí této části můžete zkontrolovat, jestli zařízení s Windows podporuje IoT Edge, a připravit ho pro modul kontejneru před instalací.

### <a name="supported-windows-versions"></a>Podporované verze systému Windows

IoT Edge pro Windows vyžaduje Windows verze 1809/Build 17763, což je nejnovější [sestavení pro dlouhodobé podpory Windows](https://docs.microsoft.com/windows/release-information/). Podporu Windows SKU najdete v tématu Co je podporováno na základě toho, jestli připravujete pro produkční scénáře nebo vývojové a testovací scénáře:

* **Produkce**: nejnovější informace o tom, které operační systémy se v produkčních scénářích aktuálně podporují, najdete v tématu [Azure IoT Edge podporovaných systémech](support.md#operating-systems).
* **Vývoj a testování**: pro vývojové a testovací scénáře lze Azure IoT Edge s kontejnery Windows nainstalovat do libovolné SKU (pro, Enterprise, server atd.) windows Build 17763, který podporuje funkci Containers.

Zařízení IoT Core musí zahrnovat volitelnou funkci kontejnerů Windows IoT Core pro podporu IoT Edge runtime. Pomocí následujícího příkazu ve [vzdálené relaci PowerShellu](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell) ověřte, že jsou kontejnery Windows na vašem zařízení podporované:

```powershell
Get-Service vmcompute
```

Pokud je služba k dispozici, měli byste získat úspěšnou odpověď se stavem služby uvedeným jako **spuštěno**. Pokud se `vmcompute` služba nenajde, zařízení nesplňuje požadavky na IoT Edge. Zeptejte se svého poskytovatele hardwaru o podpoře této funkce.

### <a name="prepare-for-a-container-engine"></a>Příprava na modul kontejneru

Azure IoT Edge spoléhá na modul kontejneru, který je [kompatibilní s OCI](https://www.opencontainers.org/) . V produkčních scénářích použijte modul Moby, který je součástí instalačního skriptu, ke spuštění kontejnerů Windows na zařízení s Windows.

## <a name="install-iot-edge-on-a-new-device"></a>Instalace IoT Edge na nové zařízení

>[!NOTE]
>Azure IoT Edge softwarové balíčky podléhají licenčním podmínkám, které jsou umístěny v balíčcích (v adresáři licencí). Přečtěte si licenční smlouvy před použitím tohoto balíčku. Vaše instalace a používání balíčku znamená přijetí těchto podmínek. Pokud s licenčními podmínkami nesouhlasíte, nepoužívejte balíček.

PowerShellový skript stáhne a nainstaluje démona zabezpečení Azure IoT Edge. Démon zabezpečení potom spustí první ze dvou běhových modulů a Agent IoT Edge, který umožňuje vzdálené nasazení jiných modulů.

>[!TIP]
>Pro zařízení IoT Core doporučujeme spouštět instalační příkazy pomocí relace RemotePowerShell. Další informace najdete v tématu [použití PowerShellu pro Windows IoT](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell).

Když na zařízení poprvé nainstalujete IoT Edge runtime, bude potřeba zřídit zařízení s identitou ze služby IoT Hub. Jedno zařízení IoT Edge lze zřídit ručně pomocí připojovacího řetězce zařízení, který poskytuje IoT Hub. Nebo můžete použít službu Device Provisioning (DPS) k automatickému zřízení zařízení, což je užitečné v případě, že máte spoustu zařízení nastavených. V závislosti na výběru zřizování zvolte vhodný instalační skript.

Následující části popisují běžné případy použití a parametry IoT Edge instalačního skriptu na novém zařízení.

### <a name="option-1-install-and-manually-provision"></a>Možnost 1: instalace a ruční zřízení

V této první možnosti zadáte **připojovací řetězec zařízení** generovaný IoT Hub pro zřízení zařízení.

Tento příklad ukazuje ruční instalaci pomocí kontejnerů Windows:

1. Pokud jste to ještě neudělali, zaregistrujte nové zařízení IoT Edge a načtěte **připojovací řetězec zařízení**. Zkopírujte připojovací řetězec pro pozdější použití v této části. Tento krok můžete provést pomocí následujících nástrojů:

   * [Azure Portal](how-to-register-device.md#register-in-the-azure-portal)
   * [Azure CLI](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio Code](how-to-register-device.md#register-with-visual-studio-code)

2. Spusťte PowerShell jako správce.

   >[!NOTE]
   >K instalaci IoT Edge, nikoli PowerShell (x86) použijte relaci AMD64 prostředí PowerShell. Pokud si nejste jistí, který typ relace používáte, spusťte následující příkaz:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. Příkaz **Deploy-IoTEdge** zkontroluje, jestli má počítač s Windows podporovanou verzi, zapne funkci Containers a pak stáhne modul runtime Moby a modul runtime IoT Edge. Příkaz ve výchozím nastavení používá kontejnery Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. V tuto chvíli se zařízení IoT Core můžou restartovat automaticky. Jiná zařízení s Windows 10 nebo Windows Server vás můžou vyzvat k restartování. Pokud ano, restartujte zařízení nyní. Až bude zařízení připravené, spusťte PowerShell jako správce znovu.

5. Příkaz **Initialize-IoTEdge** nakonfiguruje IoT Edge modul runtime na vašem počítači. Příkaz je standardně nastaven na ruční zřizování pomocí kontejnerů Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. Po zobrazení výzvy zadejte připojovací řetězec zařízení, který jste získali v kroku 1. Připojovací řetězec zařízení přidružuje fyzické zařízení k ID zařízení v IoT Hub.

   Připojovací řetězec zařízení má následující formát a nesmí obsahovat uvozovky: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. Pomocí postupu v části [ověření úspěšné instalace](#verify-successful-installation) zkontrolujte stav IoT Edge na vašem zařízení.

Při ruční instalaci a zřízení zařízení můžete pomocí dalších parametrů upravit instalaci, včetně:

* Směrování provozu pomocí proxy server
* Nasměrování instalační služby na offline adresář
* Deklarace konkrétní image kontejneru agenta a zadání přihlašovacích údajů, pokud je v privátním registru

Další informace o těchto možnostech instalace najdete v informacích o [všech parametrech instalace](#all-installation-parameters).

### <a name="option-2-install-and-automatically-provision"></a>Možnost 2: instalace a Automatické zřizování

V této druhé možnosti zřídíte zařízení pomocí IoT Hub Device Provisioning Service. Zadejte **ID oboru** z instance služby Device Provisioning spolu s dalšími informacemi, které jsou specifické pro váš preferovaný [mechanismus ověřování](../iot-dps/concepts-security.md#attestation-mechanism):

* [Vytvoření a zřízení simulovaného IoT Edge zařízení s virtuálním čipem TPM ve Windows](how-to-auto-provision-simulated-device-windows.md)
* [Vytvoření a zřízení simulovaného zařízení IoT Edge pomocí certifikátů X. 509](how-to-auto-provision-x509-certs.md)
* [Vytvoření a zřízení zařízení IoT Edge pomocí ověřování symetrického klíče](how-to-auto-provision-symmetric-keys.md)

Když instalujete a zřídíte zařízení automaticky, můžete pomocí dalších parametrů upravit instalaci, včetně:

* Směrování provozu pomocí proxy server
* Nasměrování instalační služby na offline adresář
* Deklarace konkrétní image kontejneru agenta a zadání přihlašovacích údajů, pokud je v privátním registru

Další informace o těchto možnostech instalace najdete v článku Přečtěte si tento článek nebo si přečtěte informace o [všech parametrech instalace](#all-installation-parameters).

## <a name="offline-or-specific-version-installation"></a>Instalace offline nebo specifické verze

Během instalace se stáhnou tři soubory:

* Skript prostředí PowerShell, který obsahuje pokyny k instalaci
* Microsoft Azure IoT Edge soubor CAB obsahující rozhraní Moby (IoT Edge Security Daemon), modul kontejnerů Moby a CLI
* Instalační program pro Visual C++ Distribuovatelný balíček (VC Runtime)

Pokud bude vaše zařízení během instalace offline nebo pokud chcete nainstalovat určitou verzi IoT Edge, můžete tyto soubory stáhnout na zařízení předem. Až bude čas na instalaci, najeďte instalační skript v adresáři, který obsahuje stažené soubory. Instalační program nejprve zkontroluje adresář a pak stáhne pouze součásti, které nebyly nalezeny. Pokud jsou všechny soubory k dispozici v režimu offline, můžete nainstalovat bez připojení k Internetu.

K aktualizaci IoT Edge můžete použít taky parametr cesty k instalaci offline. Další informace najdete v tématu [aktualizace démona zabezpečení IoT Edge a modulu runtime](how-to-update-iot-edge.md).

1. Nejnovější instalační soubory IoT Edge spolu s předchozími verzemi najdete v tématu [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases).

2. Vyhledejte verzi, kterou chcete nainstalovat, a Stáhněte si následující soubory z části **assets (prostředky** ) poznámky k verzi do zařízení IoT:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab z verzí 1.0.9 nebo novější, nebo Microsoft-Azure-IoTEdge.cab z verzí 1.0.8 a starších.

   Microsoft-Azure-IotEdge-arm32.cab je k dispozici také pro účely testování, od 1.0.9. IoT Edge se v současné době nepodporují na zařízeních s Windows ARM32.

   Je důležité použít skript prostředí PowerShell ze stejné verze, jako je soubor. cab, který používáte, protože funkce se mění tak, aby podporovaly funkce v jednotlivých vydaných verzích.

3. Pokud má soubor. cab, který jste stáhli, příponu architektury, přejmenujte soubor na pouze **Microsoft-Azure-IoTEdge.cab**.

4. Volitelně můžete stáhnout instalační program pro Visual C++ Redistributable. Například skript PowerShellu používá tuto verzi: [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe). Uložte instalační program do stejné složky v zařízení IoT jako soubory IoT Edge.

5. Chcete [-li nainstalovat](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing) nástroje s offline komponentami, poznamenejte si místní kopii skriptu PowerShellu. Pak použijte `-OfflineInstallationPath` parametr jako součást `Deploy-IoTEdge` příkazu a zadejte absolutní cestu k adresáři souborů. Příklad:

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   Příkaz pro nasazení bude používat všechny součásti, které jsou k dispozici v adresáři místního souboru. Pokud chybí soubor. cab nebo instalační program Visual C++, pokusí se ho stáhnout.

6. Spuštěním `Initialize-IoTEdge` příkazu zřídíte zařízení s identitou v IoT Hub. Buď zadejte připojovací řetězec zařízení pro ruční zřizování, nebo vyberte jednu z metod popsaných v předchozím oddílu věnovaném [automatickému zřizování](#option-2-install-and-automatically-provision) .

   Pokud se vaše zařízení restartovalo po spuštění `Deploy-IoTEdge` , napřed spusťte skript PowerShellu znovu `Initialize-IoTEdge` .

Pokud chcete získat další informace o možnosti offline instalace, přeskočte si přečtěte si další informace o [všech parametrech instalace](#all-installation-parameters).

## <a name="verify-successful-installation"></a>Ověření úspěšné instalace

Zkontrolujte stav služby IoT Edge. Měl by být uveden jako spuštěný.  

```powershell
Get-Service iotedge
```

Prověřte protokoly služby za posledních 5 minut. Pokud jste právě dokončili instalaci IoT Edge runtime, může se zobrazit seznam chyb od času mezi spuštěním **Deploy-IoTEdge** a **Initialize-IoTEdge**. Tyto chyby se očekávají, protože se služba pokouší spustit před tím, než se nakonfiguruje.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Spusťte [Nástroj pro řešení potíží](troubleshoot.md#run-the-check-command) , ve kterém zjistíte nejběžnější chyby konfigurace a sítě.

```powershell
iotedge check
```

Až nasadíte svůj první modul, abyste IoT Edgei na zařízení, modul **$edgeHub** systému nebude na zařízení nasazený. V důsledku toho automatizovaná kontroly vrátí chybu pro `Edge Hub can bind to ports on host` kontrolu připojení. Tuto chybu je možné ignorovat, pokud se neobjeví po nasazení modulu do zařízení.

Nakonec seznam spuštěných modulů:

```powershell
iotedge list
```

Jediným modulem, který by měl být spuštěný, se po nové instalaci zobrazí **edgeAgent**. Po prvním [nasazení IoT Edge moduly](how-to-deploy-modules-portal.md) se na zařízení spustí i druhý systémový modul **edgeHub**.

## <a name="manage-module-containers"></a>Správa kontejnerů modulů

Služba IoT Edge vyžaduje, aby na vašem zařízení běžel kontejnerový modul. Když nasadíte modul na zařízení, modul runtime IoT Edge používá modul kontejnerů k vyžádání image kontejneru z registru v cloudu. Služba IoT Edge umožňuje interakci s moduly a načítání protokolů, někdy ale můžete chtít použít modul kontejnerů k interakci s kontejnerem.

Další informace o konceptech modulů najdete v tématu [principy Azure IoT Edgech modulů](iot-edge-modules.md).

Pokud na zařízení s Windows IoT Edge používáte kontejnery Windows, součástí instalace IoT Edge je modul kontejneru Moby. Modul Moby byl založen na stejných standardech jako Docker a byl navržený tak, aby běžel paralelně na stejném počítači jako Docker Desktop. Z tohoto důvodu, pokud chcete cílit na kontejnery spravované modulem Moby, je třeba cílit na tento modul místo Docker.

Pokud například chcete zobrazit seznam všech imagí Docker, použijte následující příkaz:

```powershell
docker images
```

Pokud chcete zobrazit seznam všech imagí Moby, upravte stejný příkaz s ukazatelem na modul Moby:

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

Identifikátor URI modulu je uveden ve výstupu instalačního skriptu, nebo jej můžete najít v oddílu nastavení modulu runtime kontejneru pro soubor config. yaml.

![moby_runtime identifikátor URI v souboru config. yaml](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

Další informace o příkazech, které můžete použít k interakci s kontejnery a obrázky běžícími na vašem zařízení, najdete v tématu [rozhraní příkazového řádku Docker](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="uninstall-iot-edge"></a>Odinstalace IoT Edge

Pokud chcete odebrat instalaci IoT Edge ze zařízení s Windows, použijte následující příkaz z okna pro správu prostředí PowerShell. Tento příkaz odebere modul runtime IoT Edge společně se stávající konfigurací a daty modulu Moby.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Příkaz Uninstall-IoTEdge nefunguje v systému Windows IoT Core. Chcete-li odebrat IoT Edge ze zařízení s Windows IoT Core, je nutné znovu nasadit bitovou kopii Windows IoT Core.

Další informace o možnostech odinstalace získáte pomocí příkazu `Get-Help Uninstall-IoTEdge -full` .

## <a name="verify-installation-script"></a>Ověřit instalační skript

Instalační příkazy uvedené v tomto článku používají rutinu Invoke-WebRequest k vyžádání instalačního skriptu z `aka.ms/iotedge-win` . Tento odkaz odkazuje na `IoTEdgeSecurityDaemon.ps1` skript z poslední [vydané verze IoT Edge](https://github.com/Azure/azure-iotedge/releases). Můžete si také stáhnout tento skript nebo verzi skriptu z konkrétní verze a spustit instalační příkazy na zařízení IoT Edge.

Zadaný skript je podepsaný ke zvýšení zabezpečení. Podpis můžete ověřit stažením skriptu na zařízení a následným spuštěním následujícího příkazu PowerShellu:

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

Stav výstupu je **platný** , pokud je podpis ověřen.

## <a name="all-installation-parameters"></a>Všechny parametry instalace

Předchozí části představily běžné scénáře instalace s příklady použití parametrů pro úpravu instalačního skriptu. V této části najdete referenční tabulky se společnými parametry, které se používají k instalaci, aktualizaci nebo odinstalaci IoT Edge.

### <a name="deploy-iotedge"></a>Deploy-IoTEdge

Příkaz Deploy-IoTEdge stáhne a nasadí proces démona zabezpečení IoT Edge a jeho závislosti. Příkaz nasazení akceptuje tyto společné parametry, mimo jiné. Úplný seznam získáte pomocí příkazu `Get-Help Deploy-IoTEdge -full` .  

| Parametr | Přípustné hodnoty | Komentáře |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** nebo **Linux** | Pokud není zadaný žádný operační systém kontejneru, výchozí hodnota je Windows.<br><br>V případě kontejnerů Windows používá IoT Edge modul kontejnerů Moby, který je součástí instalace. V případě kontejnerů Linux je před zahájením instalace nutné nainstalovat kontejnerový modul. |
| **Proxy server** | Adresa URL proxy serveru | Tento parametr uveďte, pokud vaše zařízení potřebuje k připojení k Internetu projít proxy server. Další informace najdete v tématu [Konfigurace zařízení IoT Edge pro komunikaci prostřednictvím proxy server](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Cesta k adresáři | Pokud je tento parametr zahrnutý, instalační program zkontroluje uvedený adresář pro soubory IoT Edge CAB a runtime VC, které jsou potřebné pro instalaci. Všechny soubory, které nebyly nalezeny v adresáři, se stáhnou. Pokud jsou oba soubory v adresáři, můžete nainstalovat IoT Edge bez připojení k Internetu. Tento parametr můžete použít také k použití konkrétní verze. |
| **InvokeWebRequestParameters** | Zatřiďovací tabulka parametrů a hodnot | Během instalace se provedou několik webových požadavků. Pomocí tohoto pole můžete nastavit parametry pro tyto webové požadavky. Tento parametr je vhodný pro konfiguraci přihlašovacích údajů pro proxy servery. Další informace najdete v tématu [Konfigurace zařízení IoT Edge pro komunikaci prostřednictvím proxy server](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | žádné | Tento příznak umožňuje skriptu pro nasazení restartovat počítač bez výzvy, pokud je to nutné. |

### <a name="initialize-iotedge"></a>Initialize-IoTEdge

Příkaz Initialize-IoTEdge nakonfiguruje IoT Edge s připojovacím řetězcem zařízení a provozními podrobnostmi. Většinu informací generovaných tímto příkazem je pak Uloženo v souboru iotedge\config.yaml. Inicializační příkaz akceptuje tyto společné parametry, mimo jiné. Úplný seznam získáte pomocí příkazu `Get-Help Initialize-IoTEdge -full` .

| Parametr | Přípustné hodnoty | Komentáře |
| --------- | --------------- | -------- |
| **Ruční** | Žádné | **Parametr Switch** Pokud není zadán žádný typ zřizování, je výchozí hodnota ruční.<br><br>Deklaruje, že budete zadat připojovací řetězec zařízení pro ruční zřízení zařízení. |
| **DPS** | Žádné | **Parametr Switch** Pokud není zadán žádný typ zřizování, je výchozí hodnota ruční.<br><br>Deklarujete, že zadáte ID oboru služby Device Provisioning Service (DPS) a ID registrace vašeho zařízení, které se bude zřizovat prostřednictvím DPS.  |
| **DeviceConnectionString** | Připojovací řetězec ze zařízení IoT Edge zaregistrovaného v IoT Hub v jednoduchých uvozovkách | **Vyžaduje** se pro ruční zřizování. Pokud v parametrech skriptu nezadáte připojovací řetězec, zobrazí se výzva k zadání. |
| **Objekt ScopeId** | ID oboru z instance služby Device Provisioning přidružené k vašemu IoT Hub. | **Vyžaduje** se pro zřizování DPS. Pokud nezadáte ID oboru v parametrech skriptu, budete vyzváni k jeho zadání. |
| **RegistrationId** | ID registrace generované vaším zařízením | **Vyžaduje** se pro zřizování DPS, pokud používáte ověřování pomocí čipu TPM nebo symetrického klíče. **Nepovinné** , pokud se používá ověřování certifikátů X. 509. |
| **X509IdentityCertificate** | Cesta URI k certifikátu identity zařízení X. 509 na zařízení. | **Vyžaduje** se pro zřizování DPS, pokud se používá ověření certifikátu X. 509. |
| **X509IdentityPrivateKey** | Cesta URI k klíči certifikátu identity zařízení X. 509 na zařízení. | **Vyžaduje** se pro zřizování DPS, pokud se používá ověření certifikátu X. 509. |
| **SymmetricKey** | Symetrický klíč, který slouží k zřízení identity IoT Edge zařízení při použití DPS | **Vyžaduje** se pro zřizování DPS, pokud používá ověření identity symetrického klíče. |
| **ContainerOs** | **Windows** nebo **Linux** | Pokud není zadaný žádný operační systém kontejneru, výchozí hodnota je Windows.<br><br>V případě kontejnerů Windows používá IoT Edge modul kontejnerů Moby, který je součástí instalace. V případě kontejnerů Linux je před zahájením instalace nutné nainstalovat kontejnerový modul. |
| **InvokeWebRequestParameters** | Zatřiďovací tabulka parametrů a hodnot | Během instalace se provedou několik webových požadavků. Pomocí tohoto pole můžete nastavit parametry pro tyto webové požadavky. Tento parametr je vhodný pro konfiguraci přihlašovacích údajů pro proxy servery. Další informace najdete v tématu [Konfigurace zařízení IoT Edge pro komunikaci prostřednictvím proxy server](how-to-configure-proxy-support.md). |
| **AgentImage** | Identifikátor URI image agenta IoT Edge | Ve výchozím nastavení používá nová instalace IoT Edge nejnovější značku pro Image agenta IoT Edge. Tento parametr použijte k nastavení konkrétní značky pro verzi obrázku nebo k poskytnutí vlastní image agenta. Další informace najdete v tématu [Vysvětlení značek IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Uživatelské jméno** | Uživatelské jméno registru kontejneru | Tento parametr použijte pouze v případě, že nastavíte parametr-AgentImage na kontejner v privátním registru. Zadejte uživatelské jméno s přístupem k registru. |
| **Heslo** | Řetězec zabezpečeného hesla | Tento parametr použijte pouze v případě, že nastavíte parametr-AgentImage na kontejner v privátním registru. Zadejte heslo pro přístup do registru. |

### <a name="update-iotedge"></a>Update-IoTEdge

| Parametr | Přípustné hodnoty | Komentáře |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** nebo **Linux** | Pokud není zadaný žádný kontejnerový operační systém, je Windows výchozí hodnota. V případě kontejnerů Windows se v instalaci zahrne modul kontejnerů. V případě kontejnerů Linux je před zahájením instalace nutné nainstalovat kontejnerový modul. |
| **Proxy server** | Adresa URL proxy serveru | Tento parametr uveďte, pokud vaše zařízení potřebuje k připojení k Internetu projít proxy server. Další informace najdete v tématu [Konfigurace zařízení IoT Edge pro komunikaci prostřednictvím proxy server](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Zatřiďovací tabulka parametrů a hodnot | Během instalace se provedou několik webových požadavků. Pomocí tohoto pole můžete nastavit parametry pro tyto webové požadavky. Tento parametr je vhodný pro konfiguraci přihlašovacích údajů pro proxy servery. Další informace najdete v tématu [Konfigurace zařízení IoT Edge pro komunikaci prostřednictvím proxy server](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Cesta k adresáři | Pokud je tento parametr zahrnutý, instalační program zkontroluje uvedený adresář pro soubory IoT Edge CAB a runtime VC, které jsou potřebné pro instalaci. Všechny soubory, které nebyly nalezeny v adresáři, se stáhnou. Pokud jsou oba soubory v adresáři, můžete nainstalovat IoT Edge bez připojení k Internetu. Tento parametr můžete použít také k použití konkrétní verze. |
| **RestartIfNeeded** | žádné | Tento příznak umožňuje skriptu pro nasazení restartovat počítač bez výzvy, pokud je to nutné. |

### <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| Parametr | Přípustné hodnoty | Komentáře |
| --------- | --------------- | -------- |
| **Ode** | žádné | Tento příznak vynutí odinstalaci v případě neúspěchu předchozího pokusu o odinstalaci.
| **RestartIfNeeded** | žádné | Tento příznak umožňuje, aby skript pro odinstalaci restartoval počítač bez výzvy, pokud je to nutné. |

## <a name="next-steps"></a>Další kroky

Teď, když máte nainstalovanou IoT Edge zařízení s nainstalovaným modulem runtime, můžete [nasadit IoT Edge moduly](how-to-deploy-modules-portal.md).

Pokud máte potíže s instalací IoT Edge správně, podívejte se na stránku [Poradce při potížích](troubleshoot.md) .

Chcete-li aktualizovat existující instalaci na nejnovější verzi IoT Edge, přečtěte si téma [aktualizace procesu démona zabezpečení IoT Edge a modulu runtime](how-to-update-iot-edge.md).
