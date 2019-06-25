---
title: Nainstalujte Windows Azure IoT Edge | Dokumentace Microsoftu
description: Instalační pokyny pro Azure IoT Edge ve Windows 10, Windows Server a Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: f67f24cab907c3fe9998704e0a0a85d5b29f60a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808858"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Nainstalovat modul runtime Azure IoT Edge ve Windows

Modul runtime Azure IoT Edge je co se změní na zařízení do zařízení IoT Edge. Modul runtime můžete nasadit na zařízení jako Raspberry Pi malé nebo velké průmyslové serveru. Jakmile je zařízení nakonfigurovaná s modulem runtime IoT Edge, můžete začít nasazovat obchodní logiky k němu z cloudu. 

Další informace o modulu runtime IoT Edge najdete v tématu [pochopení runtime Azure IoT Edge a jeho architektura](iot-edge-runtime.md).

V tomto článku jsou uvedené kroky k instalaci modulu runtime Azure IoT Edge na vaše Windows x64 (AMD nebo Intel) systému pomocí kontejnerů Windows.

> [!NOTE]
> Známý problém operační systém Windows zabrání přechodem na Přejít do režimu spánku a hibernace stavy napájení při spouštění moduly IoT Edge (izolovaný režim procesu kontejnery Windows Nano Server). Tento problém má vliv na výdrž baterie zařízení.
>
> Jako alternativní řešení, použijte příkaz `Stop-Service iotedge` zastavit všechny spuštěné moduly IoT Edge před použitím tyto stavy napájení. 

<!--
> [!NOTE]
> Using Linux containers on Windows systems is not a recommended or supported production configuration for Azure IoT Edge. However, it can be used for development and testing purposes.
-->

Použití kontejnerů Linuxu v systémech Windows není produkční doporučené nebo podporované konfigurace pro Azure IoT Edge. To však lze použít pro účely vývoje a testování. Další informace najdete v tématu [pomocí IoT Edge ve Windows pro spouštění kontejnerů Linuxu](how-to-install-iot-edge-windows-with-linux.md).

Informace o tom, co je zahrnuté v nejnovější verzi služby IoT Edge, najdete v části [Azure IoT Edge uvolní](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Požadavky

V této části použijte ke kontrole, jestli zařízení s Windows může podporovat IoT Edge a aby byl připravený k modul container před instalací. 

### <a name="supported-windows-versions"></a>Podporované verze Windows

Pro scénáře vývoje a testování Azure IoT Edge s kontejnery Windows nainstalujete na kteroukoli verzi Windows 10 nebo Windows Server. 2019 (build 17763), který podporuje funkci containers. Informace o tom, které operační systémy jsou aktuálně podporovány pro produkční scénáře, naleznete v tématu [Azure IoT Edge podporované systémy](support.md#operating-systems). 

### <a name="prepare-for-a-container-engine"></a>Připravit pro modul container 

Azure IoT Edge se může spolehnout [OCI kompatibilní](https://www.opencontainers.org/) modul kontejneru. Pro produkční scénáře použití modulu Moby součástí instalační skript pro spouštění kontejnerů Windows na zařízení s Windows. 

## <a name="install-iot-edge-on-a-new-device"></a>Nainstalovat nové zařízení IoT Edge

>[!NOTE]
>Azure IoT Edge softwarové balíčky jsou souladu s licenčními podmínkami umístěný v balíčcích (v adresáři licencí). Přečtěte si licenční podmínky před použitím balíčku. Instalace a použití balíčku se považuje za svůj souhlas s těmito podmínkami. Pokud s licenčními podmínkami nesouhlasíte, nepoužívejte balíček.

Powershellový skript se stáhne a nainstaluje démona zabezpečení Azure IoT Edge. Zabezpečení proces démon potom spustí první ze dvou modulů runtime, agenta IoT Edge, které umožňuje vzdálené nasazení dalších modulů. 

Při instalaci modulu runtime IoT Edge na zařízení poprvé, budete muset zřizovat zařízení s identitou ze služby IoT hub. Jedno zařízení IoT Edge se dá zřídit ručně pomocí připojovacího řetězce zařízení k dispozici ve službě IoT Hub. Nebo můžete do služby Device Provisioning k automatickému zřízení zařízení, což je užitečné, když máte velký počet zařízení k nastavení. Podle svého výběru: zřizování zvolte příslušný instalační skript. 

V následujících částech se běžné případy použití a parametry instalačního skriptu IoT Edge na nová zařízení. 

### <a name="option-1-install-and-manually-provision"></a>Option 1: Instalace a ruční zřizování

V této první možnost poskytnout **připojovací řetězec zařízení** vygenerované službou IoT Hub, zřídit zařízení. 

Tento příklad ukazuje ruční instalace s kontejnery Windows:

1. Pokud jste to ještě neudělali, zaregistrujte nové zařízení IoT Edge a načíst **připojovací řetězec zařízení**. Zkopírujte připojovací řetězec pro pozdější použití v této části. Dokončení tohoto kroku pomocí následujících nástrojů:

   * [Azure Portal](how-to-register-device-portal.md)
   * [Azure CLI](how-to-register-device-cli.md)
   * [Visual Studio Code](how-to-register-device-vscode.md)

2. Spusťte PowerShell jako správce.

   >[!NOTE]
   >Instalace IoT Edge, PowerShell (x86) používáte AMD64 relaci powershellu. Pokud si nejste jistí, který typ relace používáte, spusťte následující příkaz:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. **Nasadit IoTEdge** příkaz kontroluje, zda váš počítač Windows na podporovanou verzi, se změní na funkci kontejnery a potom stáhne moby runtime a modul runtime IoT Edge. Příkaz ve výchozím nastavení používá kontejnery Windows. 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. V tomto okamžiku může automaticky restartovat zařízení IoT Core. Ostatní zařízení Windows 10 nebo Windows Server můžete být vyzváni k restartování. Pokud ano, restartujte zařízení. Jakmile se vaše zařízení je připravené, znovu spusťte PowerShell jako správce.

5. **Inicializace IoTEdge** příkaz nakonfiguruje modul runtime IoT Edge na svém počítači. Příkaz výchozí hodnota je ruční zřizování s kontejnery Windows. 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. Po zobrazení výzvy zadejte připojovací řetězec zařízení, které jste získali v kroku 1. Připojovací řetězec zařízení přidružuje fyzického zařízení s ID zařízení ve službě IoT Hub. 

   Připojovací řetězec zařízení má následující formát a neměla by obsahovat uvozovky: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. Postupujte podle kroků v [ověření úspěšné instalace](#verify-successful-installation) a zkontrolujte stav služby IoT Edge na zařízení. 

Při instalaci a zřízení zařízení ručně, můžete upravit, včetně instalace dalších parametrů:
* Chcete-li přejít přes proxy server směrovat přenos dat
* Přejděte na adresář služby offline instalační program
* Deklarujte image kontejneru určitého agenta a zadejte přihlašovací údaje, pokud je v privátním registru

Další informace o těchto možnostech instalace, přeskočte k části Další informace o [všechny parametry instalace](#all-installation-parameters).

### <a name="option-2-install-and-automatically-provision"></a>Option 2: Instalace a automaticky zřizovat

V této druhé možnosti zřizování zařízení pomocí IoT Hub Device Provisioning Service. Zadejte **ID oboru** z instance služby Device Provisioning a **ID registrace** z vašeho zařízení.

Následující příklad ukazuje automatické instalace s kontejnery Windows:

1. Postupujte podle kroků v [vytvoření a zřízení simulovaného zařízení TPM IoT Edge ve Windows](how-to-auto-provision-simulated-device-windows.md) nastavení služby Device Provisioning a získání jeho **ID oboru**, simulace zařízení TPM a načíst jeho **ID registrace**, pak vytvoření jednotlivé registrace. Jakmile je vaše zařízení zaregistrované ve službě IoT hub, pokračujte postupem instalace.  

   >[!TIP]
   >Nechte okno, na kterém běží simulátor TPM otevřených během instalace a testování. 

2. Spusťte PowerShell jako správce.

   >[!NOTE]
   >Instalace IoT Edge, PowerShell (x86) používáte AMD64 relaci powershellu. Pokud si nejste jistí, který typ relace používáte, spusťte následující příkaz:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. **Nasadit IoTEdge** příkaz kontroluje, zda váš počítač Windows na podporovanou verzi, se změní na funkci kontejnery a potom stáhne moby runtime a modul runtime IoT Edge. Příkaz ve výchozím nastavení používá kontejnery Windows. 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. V tomto okamžiku může automaticky restartovat zařízení IoT Core. Ostatní zařízení Windows 10 nebo Windows Server můžete být vyzváni k restartování. Pokud ano, restartujte zařízení. Jakmile se vaše zařízení je připravené, znovu spusťte PowerShell jako správce.

6. **Inicializace IoTEdge** příkaz nakonfiguruje modul runtime IoT Edge na svém počítači. Příkaz výchozí hodnota je ruční zřizování s kontejnery Windows. Použít `-Dps` příznak pro použití služby Device Provisioning místo ručního zřizování.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps
   ```

7. Po zobrazení výzvy zadejte ID oboru z vaší služby Device Provisioning a ID registrace ze zařízení, které by jste získali v kroku 1.

8. Postupujte podle kroků v [ověření úspěšné instalace](#verify-successful-installation) a zkontrolujte stav služby IoT Edge na zařízení. 

Při instalaci a zřízení zařízení ručně, můžete upravit, včetně instalace dalších parametrů:
* Chcete-li přejít přes proxy server směrovat přenos dat
* Přejděte na adresář služby offline instalační program
* Deklarujte image kontejneru určitého agenta a zadejte přihlašovací údaje, pokud je v privátním registru

Další informace o těchto možnostech instalace, pokračujte ve čtení tohoto článku nebo přejděte k další informace o [všechny parametry instalace](#all-installation-parameters).

## <a name="offline-installation"></a>Offline instalace

Během instalace se stáhnou dva soubory: 
* Microsoft Azure IoT Edge cab, který obsahuje démon zabezpečení IoT Edge (iotedged), modul container Moby a Moby rozhraní příkazového řádku.
* Visual C++ redistributable package (modulu runtime jazyka VC) msi

Můžete stáhnout jeden nebo oba z těchto souborů předem do zařízení a pak přejděte do adresáře, který obsahuje soubory instalační skript. Instalační program nejprve zkontroluje adresáři a stáhne jenom součásti, které nebyly nalezeny. Pokud všechny soubory jsou k dispozici v režimu offline, můžete nainstalovat pomocí bez připojení k Internetu. Tuto funkci můžete také nainstalovat určitou verzi komponenty.  

Nejnovější instalační soubory IoT Edge spolu s předchozími verzemi, naleznete v tématu [Azure IoT Edge uvolní](https://github.com/Azure/azure-iotedge/releases).

K instalaci součástí offline, použijte `-OfflineInstallationPath` parametr jako součást nasazení – IoTEdge příkaz a zadejte absolutní cestu k adresáři souboru. Například:

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

Můžete také použít parametr cesty v režimu offline instalace pomocí příkazu Update-IoTEdge zavedené dále v tomto článku. 

## <a name="verify-successful-installation"></a>Ověření úspěšné instalace

Zkontrolujte stav služby IoT Edge. By měly být uvedeny jako spuštění.  

```powershell
Get-Service iotedge
```

Zkontrolujte protokoly služby z posledních 5 minut. Pokud právě jste dokončili, instalace modulu runtime IoT Edge, může se zobrazit seznam chyb od času mezi spuštění **nasadit IoTEdge** a **inicializace IoTEdge**. Tyto chyby jsou očekávané chování, protože služba se pokouší spustit před konfigurován. 

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Seznam s moduly. Po dokončení nové instalace pouze modulu byste měli vidět spuštění **edgeAgent**. Poté co [nasadit moduly IoT Edge](how-to-deploy-modules-portal.md), zobrazí se ostatním. 

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>Správa kontejnerů modulu

Služba IoT Edge vyžaduje, aby modul container běžícího ve vašem zařízení. Při nasazení modulu do zařízení, modul runtime IoT Edge používá modul kontejneru pro vyžádejte si image kontejneru z registru v cloudu. Služba IoT Edge umožňuje pracovat s moduly a protokoly načíst, ale v některých případech můžete chtít použít modul kontejneru pro interakci s kontejner sám o sobě. 

Další informace o modulu koncepty, najdete v části [moduly pochopit Azure IoT Edge](iot-edge-modules.md). 

Pokud používáte kontejnery Windows na zařízení s Windows IoT Edge, IoT Edge instalace zahrnuté modul Moby kontejneru. Modul Moby byl založen na stejné standardy jako Docker a nebyl navržený pro běh paralelně na stejném počítači jako Docker Desktop. Z tohoto důvodu Pokud chcete cíl kontejnery spravuje Moby modul, budete muset specificky cílí tento motor místo Dockeru. 

Například seznam všech imagí Dockeru, použijte následující příkaz:

```powershell
docker images
```

Chcete-li seznam všech imagí Moby, upravte stejný příkaz pomocí ukazatele na modul Moby: 

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

Modul identifikátoru URI je uvedená ve výstupu skriptu instalace nebo najdete ho v části nastavení modulu runtime kontejneru config.yaml souboru. 

![identifikátor uri moby_runtime v config.yaml](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

Další informace o příkazech, která vám umožní pracovat s kontejnery a imagí běžící na vašem zařízení, najdete v části [rozhraní příkazového řádku Dockeru](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="update-an-existing-installation"></a>Aktualizace stávající instalace

Pokud jste již nainstalován modul runtime IoT Edge na zařízení, než a zřízené s identitou ze služby IoT Hub, můžete aktualizovat modul runtime bez nutnosti znovu zadat údaje o zařízení. 

Další informace najdete v tématu [aktualizovat démon zabezpečení IoT Edge a modulu runtime](how-to-update-iot-edge.md).

Tento příklad ukazuje instalaci, která odkazuje na existující konfigurační soubor a nepoužívá kontejnery Windows: 

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Update-IoTEdge
```

Když aktualizujete IoT Edge, můžete použít další parametry upravit aktualizace, včetně:
* Směrování provozu do přejít přes proxy server, nebo
* Přejděte na adresář služby offline instalační program 
* V případě potřeby restartování bez výzvy

Image kontejneru agenta IoT Edge s parametry skriptu nelze deklarovat, protože tyto informace je již nastavena v konfiguračním souboru z předchozí instalace. Pokud chcete změnit agenta image kontejneru, můžete tak učinit v souboru config.yaml. 

Další informace o těchto možnostech aktualizace, použijte příkaz `Get-Help Update-IoTEdge -full` nebo si přečtěte [všechny parametry instalace](#all-installation-parameters).

## <a name="uninstall-iot-edge"></a>Odinstalujte IoT Edge

Pokud chcete odebrat ze zařízení s Windows instalaci IoT Edge, použijte následující příkaz z okna prostředí PowerShell pro správu. Tento příkaz odebere modul runtime IoT Edge, spolu s vaší existující konfigurace a data Moby motoru. 

```powershell
Uninstall-IoTEdge
```

Příkaz Odinstalovat IoTEdge nefunguje na Windows IoT Core. Chcete-li odebrat ze zařízení s Windows IoT Core IoT Edge, budete muset znovu nasadit svou image Windows IoT Core. 

Další informace o možnostech odinstalace, použijte příkaz `Get-Help Uninstall-IoTEdge -full`. 

## <a name="all-installation-parameters"></a>Všechny parametry instalace

V předchozích částech zavedené běžné scénáře instalace s příklady použití parametrů a upravte skript instalace. Tato část obsahuje referenční tabulky společné parametry pro instalaci, aktualizaci nebo odinstalaci IoT Edge. 

### <a name="deploy-iotedge"></a>Deploy-IoTEdge

Příkaz nasadit IoTEdge stáhne a nasadí démon zabezpečení IoT Edge a jeho závislosti. Nasazení příkazu přijímá tyto společné parametry, mimo jiné. Úplný seznam, použijte příkaz `Get-Help Deploy-IoTEdge -full`.  

| Parametr | Přípustné hodnoty | Komentáře |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** nebo **Linux** | Pokud není zadán žádný kontejner operační systém, je Windows výchozí hodnota.<br><br>IoT Edge pro kontejnery Windows, používá modul kontejneru moby součástí instalace. Pro kontejnery Linuxu musíte nainstalovat modul container před zahájením instalace. |
| **Proxy** | Adresa URL proxy serveru | Zahrňte tento parametr, pokud vaše zařízení je potřeba přejít přes proxy server pro přístup k Internetu. Další informace najdete v tématu [nakonfigurovat nastavení zařízení IoT Edge pro komunikaci přes proxy server](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Cesta k adresáři | Pokud tento parametr je součástí, instalační program zkontroluje uvedené adresář pro soubor cab IoT Edge a MSI modulu Runtime VC soubory potřebné k instalaci. Všechny soubory v adresáři nenašel se stáhnou. Pokud jsou oba soubory v adresáři, můžete nainstalovat IoT Edge bez připojení k Internetu. Tento parametr můžete také použít konkrétní verzi. |
| **InvokeWebRequestParameters** | Zatřiďovací tabulka parametrů a hodnot | Při instalaci několika webových požadavků probíhají. Použijte toto pole můžete nastavit parametry pro tyto webové žádosti. Tento parametr je vhodné nakonfigurovat přihlašovací údaje pro proxy servery. Další informace najdete v tématu [nakonfigurovat nastavení zařízení IoT Edge pro komunikaci přes proxy server](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | None | Tento příznak umožňuje restartovat počítač bez výzvy, skript nasazení v případě potřeby. |

### <a name="initialize-iotedge"></a>Initialize-IoTEdge

Inicializace IoTEdge příkaz nakonfiguruje IoT Edge se připojovací řetězec zařízení a provozní údaje. Velká část informace generované tímto příkazem je uložena v souboru iotedge\config.yaml. Inicializace příkazu přijímá tyto společné parametry, mimo jiné. Úplný seznam, použijte příkaz `Get-Help Initialize-IoTEdge -full`. 

| Parametr | Přípustné hodnoty | Komentáře |
| --------- | --------------- | -------- |
| **Ruční** | Žádný | **Přepnout parametr**. Pokud není zadán žádný typ zřizování, ruční je výchozí hodnota.<br><br>Deklaruje, že zadáte připojovací řetězec zařízení pro zřízení zařízení ručně |
| **DPS** | Žádný | **Přepnout parametr**. Pokud není zadán žádný typ zřizování, ruční je výchozí hodnota.<br><br>Deklaruje, že zadáte zařízení zřizování služby (DPS) ID oboru a ID registrace vašeho zařízení pro zřízení prostřednictvím distribučních bodů.  |
| **DeviceConnectionString** | Řetězec připojení ze zařízení IoT Edge zaregistrovaný ve službě IoT Hub, v jednoduchých uvozovkách | **Vyžaduje** pro ruční instalaci. Pokud nezadáte připojovací řetězec do pole Parametry skriptu, zobrazí se výzva k zadání jeden během instalace. |
| **Objekt ScopeId** | ID oboru z instance služby Device Provisioning související s centrem IoT. | **Vyžaduje** pro instalaci distribučních bodů. Pokud nezadáte ID oboru v parametry skriptu, zobrazí se výzva k zadání jednoho během instalace. |
| **RegistrationId** | ID registrace generovaná vaším zařízením | **Vyžaduje** pro instalaci distribučních bodů. Pokud nezadáte ID registrace v parametry skriptu, zobrazí se výzva k zadání jednoho během instalace. |
| **ContainerOs** | **Windows** nebo **Linux** | Pokud není zadán žádný kontejner operační systém, je Windows výchozí hodnota.<br><br>IoT Edge pro kontejnery Windows, používá modul kontejneru moby součástí instalace. Pro kontejnery Linuxu musíte nainstalovat modul container před zahájením instalace. |
| **InvokeWebRequestParameters** | Zatřiďovací tabulka parametrů a hodnot | Při instalaci několika webových požadavků probíhají. Použijte toto pole můžete nastavit parametry pro tyto webové žádosti. Tento parametr je vhodné nakonfigurovat přihlašovací údaje pro proxy servery. Další informace najdete v tématu [nakonfigurovat nastavení zařízení IoT Edge pro komunikaci přes proxy server](how-to-configure-proxy-support.md). |
| **AgentImage** | Identifikátor URI image agenta IoT Edge | Ve výchozím nastavení používá nové instalace IoT Edge na nejnovější kumulativní značku pro bitovou kopii agenta IoT Edge. Tento parametr použijte k nastavení s konkrétní značkou pro verze image nebo poskytnout vlastní image agenta. Další informace najdete v tématu [značky pochopit IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Uživatelské jméno** | Uživatelské jméno registru kontejneru | Tento parametr použijte jenom v případě, že nastavíte parametr - AgentImage do kontejneru v privátním registru. Zadejte uživatelské jméno, přístup k registru. |
| **Heslo** | Zabezpečené heslo řetězec | Tento parametr použijte jenom v případě, že nastavíte parametr - AgentImage do kontejneru v privátním registru. Zadejte heslo pro přístup k registru. | 

### <a name="update-iotedge"></a>Update-IoTEdge

| Parametr | Přípustné hodnoty | Komentáře |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** nebo **Linux** | Pokud se kontejner zadaný operační systém, je Windows výchozí hodnota. Pro kontejnery Windows modul container součástí instalace. Pro kontejnery Linuxu musíte nainstalovat modul container před zahájením instalace. |
| **Proxy** | Adresa URL proxy serveru | Zahrňte tento parametr, pokud vaše zařízení je potřeba přejít přes proxy server pro přístup k Internetu. Další informace najdete v tématu [nakonfigurovat nastavení zařízení IoT Edge pro komunikaci přes proxy server](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Zatřiďovací tabulka parametrů a hodnot | Při instalaci několika webových požadavků probíhají. Použijte toto pole můžete nastavit parametry pro tyto webové žádosti. Tento parametr je vhodné nakonfigurovat přihlašovací údaje pro proxy servery. Další informace najdete v tématu [nakonfigurovat nastavení zařízení IoT Edge pro komunikaci přes proxy server](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Cesta k adresáři | Pokud tento parametr je součástí, instalační program zkontroluje uvedené adresář pro soubor cab IoT Edge a MSI modulu Runtime VC soubory potřebné k instalaci. Všechny soubory v adresáři nenašel se stáhnou. Pokud jsou oba soubory v adresáři, můžete nainstalovat IoT Edge bez připojení k Internetu. Tento parametr můžete také použít konkrétní verzi. |
| **RestartIfNeeded** | None | Tento příznak umožňuje restartovat počítač bez výzvy, skript nasazení v případě potřeby. |


### <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| Parametr | Přípustné hodnoty | Komentáře |
| --------- | --------------- | -------- |
| **Platnost** | None | Tento příznak vynutí odinstalování v případě, že předchozí pokus o odinstalaci nebylo úspěšné. 
| **RestartIfNeeded** | None | Tento příznak umožňuje odinstalačního skriptu, který chcete restartovat počítač bez výzvy, v případě potřeby. |


## <a name="next-steps"></a>Další postup

Teď, když máte zařízení IoT Edge zřízené s modulem runtime nainstalovaný, je možné [nasadit moduly IoT Edge](how-to-deploy-modules-portal.md).

Pokud máte potíže s instalací IoT Edge správně, podívejte se [řešení potíží s](troubleshoot.md) stránky.

Aktualizace stávající instalace na nejnovější verzi služby IoT Edge, najdete v článku [aktualizovat démon zabezpečení IoT Edge a modulu runtime](how-to-update-iot-edge.md).
