---
title: Nainstalujte Windows Azure IoT Edge | Dokumentace Microsoftu
description: Instalační pokyny pro Azure IoT Edge ve Windows 10, Windows Server a Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: c280410816bfb48f21c68fe5d57b6ae18af0e855
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53970657"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Nainstalovat modul runtime Azure IoT Edge ve Windows

Modul runtime Azure IoT Edge je co se změní na zařízení do zařízení IoT Edge. Modul runtime můžete nasadit na zařízení jako Raspberry Pi malé nebo velké průmyslové serveru. Jakmile je zařízení nakonfigurovaná s modulem runtime IoT Edge, můžete začít nasazovat obchodní logiky k němu z cloudu. 

Další informace o modulu runtime IoT Edge najdete v tématu [pochopení runtime Azure IoT Edge a jeho architektura](iot-edge-runtime.md).

V tomto článku jsou uvedené kroky k instalaci modulu runtime Azure IoT Edge na vaše Windows x64 (AMD nebo Intel) systému. Podpora Windows je aktuálně ve verzi Preview.

>[!NOTE]
Pomocí kontejnerů Linuxu na systémy Windows není produkční doporučené nebo podporované konfigurace pro Azure IoT Edge. To však lze použít pro účely vývoje a testování.

## <a name="prerequisites"></a>Požadavky

V této části použijte ke kontrole, jestli zařízení s Windows může podporovat IoT Edge a aby byl připravený k modul container před instalací. 

### <a name="supported-windows-versions"></a>Podporované verze Windows

Azure IoT Edge podporuje různé verze Windows, v závislosti na tom, jestli používáte kontejnery Windows nebo kontejnery Linuxu. 

Nejnovější verzi služby Azure IoT Edge s kontejnery Windows můžete spustit na následující verze systému Windows:
* Windows 10 nebo IoT Core s října 2018 update (build 17763)
* Windows Server 2019 (build 17763)

V následujících verzích Windows můžete spustit na nejnovější verzi služby Azure IoT Edge pomocí kontejnerů Linuxu: 
* Windows 10 Anniversary update (sestavení 14393) nebo novější
* Windows Server 2016 nebo novější

Další informace o tom, které jsou aktuálně podporované operační systémy, najdete v části [podpory Azure IoT Edge](support.md#operating-systems). 

Další informace o co je zahrnuté v nejnovější verzi služby IoT Edge najdete v tématu [Azure IoT Edge uvolní](https://github.com/Azure/azure-iotedge/releases).

### <a name="prepare-for-a-container-engine"></a>Připravit pro modul container 

Azure IoT Edge se může spolehnout [OCI kompatibilní](https://www.opencontainers.org/) modul kontejneru. Pro produkční scénáře použití modulu Moby součástí instalační skript pro spouštění kontejnerů Windows na zařízení s Windows. Pro vývoj a testování, kontejnery Linuxu můžete spustit na zařízení s Windows, ale je potřeba nainstalovat a nakonfigurovat modul container před instalací IoT Edge. Obou scénářích najdete v následující části pro požadavky na přípravu vašeho zařízení. 

#### <a name="moby-engine-for-windows-containers"></a>Modul Moby pro kontejnery Windows

Pro zařízení Windows s IoT Edge v produkčních scénářích je Moby pouze modul oficiálně podporovaných kontejneru. Instalační skript automaticky nainstaluje modul Moby na vašem zařízení před instalací IoT Edge. Připravte zařízení zapnutím funkce kontejnery. 

1. Na panelu spuštění vyhledat **Windows zapnout nebo vypnout funkce** a otevřít programu v Ovládacích panelech.
2. Vyhledejte a vyberte **kontejnery**.
3. Vyberte **OK**. 

#### <a name="docker-for-linux-containers"></a>Docker pro kontejnery Linuxu

Pokud používáte Windows můžete vyvíjet a testovat kontejnery pro Linux zařízení, můžete použít [Docker pro Windows](https://www.docker.com/docker-windows) jako modul kontejneru. Docker může být nakonfigurován k [používat kontejnery Linuxu](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers). Budete muset Docker nainstalujte a nakonfigurujte jej před instalací IoT Edge. Na zařízeních s Windows v produkčním prostředí nejsou podporovány kontejnery Linux. 

Pokud zařízení IoT Edge je počítač Windows, zkontrolujte, zda splňuje [požadavky na systém](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) pro Hyper-V. Pokud je virtuální počítač, povolte [vnořená virtualizace](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization) a vyhradit alespoň 2 GB paměti.

## <a name="install-iot-edge-on-a-new-device"></a>Nainstalovat nové zařízení IoT Edge

>[!NOTE]
>Azure IoT Edge softwarové balíčky jsou souladu s licenčními podmínkami umístěný v balíčcích (v adresáři licencí). Přečtěte si licenční podmínky před použitím balíčku. Instalace a použití balíčku se považuje za svůj souhlas s těmito podmínkami. Pokud s licenčními podmínkami nesouhlasíte, nepoužívejte balíček.

Powershellový skript se stáhne a nainstaluje démona zabezpečení Azure IoT Edge. Zabezpečení proces démon potom spustí první ze dvou modulů runtime, agenta IoT Edge, které umožňuje vzdálené nasazení dalších modulů. 

Při instalaci modulu runtime IoT Edge na zařízení poprvé, budete muset zřizovat zařízení s identitou ze služby IoT hub. Jedno zařízení IoT Edge se dá zřídit ručně pomocí řetězce připojení zařízení k dispozici ve službě IoT Hub. Nebo můžete do služby Device Provisioning k automatickému zřízení zařízení, což je užitečné, když máte velký počet zařízení k nastavení. Podle svého výběru: zřizování zvolte příslušný instalační skript. 

V následujících částech se běžné případy použití a parametry instalačního skriptu IoT Edge na nová zařízení. 

### <a name="option-1-install-and-manually-provision"></a>Možnost 1: Instalace a ruční zřizování

V první možnost můžete zadat připojovací řetězec zařízení vygenerované službou IoT Hub, zřídit zařízení. 

Postupujte podle kroků v [zaregistrovat nová zařízení Azure IoT Edge](how-to-register-device-portal.md) načíst připojovací řetězec zařízení a zaregistrovat své zařízení. 

Tento příklad ukazuje ruční instalace s kontejnery Windows:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -ContainerOs Windows -DeviceConnectionString '<connection-string>'
```

Při instalaci a zřízení zařízení ručně, můžete upravit, včetně instalace dalších parametrů:
* Chcete-li přejít přes proxy server směrovat přenos dat
* Přejděte na adresář služby offline instalační program
* Deklarujte image kontejneru určitého agenta a zadejte přihlašovací údaje, pokud je v privátním registru
* Instalace rozhraní příkazového řádku Moby přeskočí

Další informace o těchto možnostech instalace, pokračujte ve čtení tohoto článku nebo přejděte k další informace o [všechny parametry instalace](#all-installation-parameters).

### <a name="option-2-install-and-automatically-provision"></a>Možnost 2: Instalace a automaticky zřizovat

V této druhé možnosti zřizování zařízení pomocí IoT Hub Device Provisioning Service. Zadejte **ID oboru** z instance služby Device Provisioning a **ID registrace** z vašeho zařízení.

Postupujte podle kroků v [vytvoření a zřízení simulovaného zařízení TPM Edge ve Windows](how-to-auto-provision-simulated-device-windows.md) nastavení služby Device Provisioning a získání jeho **ID oboru**, simulace zařízení TPM a načíst jeho  **ID registrace**, pak vytvoření jednotlivé registrace. Jakmile je vaše zařízení zaregistrované ve službě IoT Hub, pokračujte v instalaci.  

   >[!TIP]
   >Nechte okno, na kterém běží simulátor TPM otevřených během instalace a testování. 

Následující příklad ukazuje automatické instalace s kontejnery Windows:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Dps -ContainerOs Windows -ScopeId <DPS scope ID> -RegistrationId <device registration ID>
```

Při instalaci a zřízení zařízení ručně, můžete upravit, včetně instalace dalších parametrů:
* Chcete-li přejít přes proxy server směrovat přenos dat
* Přejděte na adresář služby offline instalační program
* Deklarujte image kontejneru určitého agenta a zadejte přihlašovací údaje, pokud je v privátním registru
* Instalace rozhraní příkazového řádku Moby přeskočí

Další informace o těchto možnostech instalace, pokračujte ve čtení tohoto článku nebo přejděte k další informace o [všechny parametry instalace](#all-installation-parameters).

## <a name="update-an-existing-installation"></a>Aktualizace stávající instalace

Pokud jste již nainstalován modul runtime IoT Edge na zařízení, než a zřízené s identitou ze služby IoT Hub, můžete použít zjednodušenou instalační skript. Příznak `-ExistingConfig` deklaruje, že konfigurační soubor IoT Edge na zařízení už existuje. Konfigurační soubor obsahuje informace o nastavení identity stejně jako certifikáty a síťových zařízení. Tato možnost instalace můžete použít, ať už ručně nebo automaticky se původně zřídilo vaše zařízení. 

Další informace najdete v tématu [aktualizovat démon zabezpečení IoT Edge a modulu runtime](how-to-update-iot-edge.md).

Tento příklad ukazuje instalaci, která odkazuje na existující konfigurační soubor a nepoužívá kontejnery Windows: 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -ExistingConfig -ContainerOs Windows
```

Při instalaci IoT Edge z existující konfigurační soubor, můžete použít další parametry upravte instalaci, včetně:
* Chcete-li přejít přes proxy server směrovat přenos dat
* Přejděte na adresář služby offline instalační program nebo 
* Instalace rozhraní příkazového řádku Moby přeskočí. 

Image kontejneru agenta IoT Edge s parametry skriptu nelze deklarovat, protože tyto informace je již nastavena v konfiguračním souboru z předchozí instalace. Pokud chcete změnit agenta image kontejneru, můžete tak učinit v souboru config.yaml. 

Další informace o těchto možnostech instalace, pokračujte ve čtení tohoto článku nebo přejděte k další informace o [všechny parametry instalace](#all-installation-parameters).

## <a name="offline-installation"></a>Offline instalace

Během instalace se stáhnou čtyři soubory: 
* Zip démon (iotedgd) zabezpečení IoT Edge 
* Moby modulu zip
* Rozhraní příkazového řádku Moby zip
* Visual C++ redistributable package (modulu runtime jazyka VC) msi

Můžete stáhnout jeden nebo všechny tyto soubory předem do zařízení a pak přejděte do adresáře, který obsahuje soubory instalační skript. Instalační program nejprve zkontroluje adresáři a stáhne jenom součásti, které nebyly nalezeny. Pokud všechny čtyři soubory jsou k dispozici v režimu offline, můžete nainstalovat pomocí bez připojení k Internetu. Tuto funkci můžete použít také k přepsání online verze jednoho nebo více komponent.  

Nejnovější instalační soubory spolu s předchozími verzemi najdete v části [uvolní Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)

K instalaci součástí offline, použijte `-OfflineInstallationPath` parametr a zadejte absolutní cestu k adresáři souboru. Například:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -DeviceConnectionString '<connection-string>' -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

## <a name="all-installation-parameters"></a>Všechny parametry instalace

V předchozích částech zavedené běžné scénáře instalace s příklady použití parametrů a upravte skript instalace. Tato část obsahuje referenční tabulku platných parametrů, které můžete použít k instalaci IoT Edge. Další informace získáte spuštěním `get-help Install-SecurityDaemon -full` v okně Powershellu. 

K instalaci IoT Edge pomocí existující konfigurace, instalační příkaz můžete použít tyto běžné parametry: 

| Parametr | Přípustné hodnoty | Komentáře |
| --------- | --------------- | -------- |
| **Ruční** | Žádný | **Přepnout parametr**. Každé instalaci musí být deklarován buď ručně, distribučních bodů, nebo existingconfig.<br><br>Deklaruje, že zadáte připojovací řetězec zařízení pro zřízení zařízení ručně |
| **DPS** | Žádný | **Přepnout parametr**. Každé instalaci musí být deklarován buď ručně, distribučních bodů, nebo existingconfig.<br><br>Deklaruje, že zadáte zařízení zřizování služby (DPS) ID oboru a ID registrace vašeho zařízení pro zřízení prostřednictvím distribučních bodů.  |
| **ExistingConfig** | Žádný | **Přepnout parametr**. Každé instalaci musí být deklarován buď ručně, distribučních bodů, nebo existingconfig.<br><br>Deklaruje, že už existuje soubor config.yaml na zařízení s jeho informace o zřizování. |
| **DeviceConnectionString** | Řetězec připojení ze zařízení IoT Edge zaregistrovaný ve službě IoT Hub, v jednoduchých uvozovkách | **Vyžaduje** pro ruční instalaci. Pokud nezadáte připojovací řetězec do pole Parametry skriptu, zobrazí se výzva k zadání jeden během instalace. |
| **Objekt ScopeId** | ID oboru z instance služby Device Provisioning související s centrem IoT. | **Vyžaduje** pro instalaci distribučních bodů. Pokud nezadáte ID oboru v parametry skriptu, zobrazí se výzva k zadání jednoho během instalace. |
| **RegistrationId** | ID registrace generovaná vaším zařízením | **Vyžaduje** pro instalaci distribučních bodů. Pokud nezadáte ID registrace v parametry skriptu, zobrazí se výzva k zadání jednoho během instalace. |
| **ContainerOs** | **Windows** nebo **Linux** | Pokud se kontejner zadaný operační systém, Linux je výchozí hodnota. Pro kontejnery Windows modul container součástí instalace. Pro kontejnery Linuxu musíte nainstalovat modul container před zahájením instalace. Kontejnery Linuxu a systémem Windows je užitečné vývojové scénáře, ale není podporován v produkčním prostředí. |
| **Proxy server** | Adresa URL proxy serveru | Zahrňte tento parametr, pokud vaše zařízení je potřeba přejít přes proxy server pro přístup k Internetu. Další informace najdete v tématu [nakonfigurovat nastavení zařízení IoT Edge pro komunikaci přes proxy server](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Zatřiďovací tabulka parametrů a hodnot | Při instalaci několika webových požadavků probíhají. Použijte toto pole můžete nastavit parametry pro tyto webové žádosti. Tento parametr je vhodné nakonfigurovat přihlašovací údaje pro proxy servery. Další informace najdete v tématu [nakonfigurovat nastavení zařízení IoT Edge pro komunikaci přes proxy server](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Cesta k adresáři | Pokud tento parametr je součástí, instalační program zkontroluje adresář pro iotedged zip, Moby modulu zip, rozhraní příkazového řádku Moby zip a MSI modulu Runtime VC soubory potřebné k instalaci. Pokud jsou všechny čtyři soubory v adresáři, můžete nainstalovat IoT Edge při offline. Tento parametr můžete použít také k přepsání online verzi konkrétní součást. |
| **AgentImage** | Identifikátor URI image agenta IoT Edge | Ve výchozím nastavení používá nové instalace IoT Edge na nejnovější kumulativní značku pro bitovou kopii agenta IoT Edge. Tento parametr použijte k nastavení s konkrétní značkou pro verze image nebo poskytnout vlastní image agenta. Další informace najdete v tématu [značky pochopit IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Uživatelské jméno** | Uživatelské jméno registru kontejneru | Tento parametr použijte jenom v případě, že nastavíte parametr - AgentImage do kontejneru v privátním registru. Zadejte uživatelské jméno, přístup k registru. |
| **Heslo** | Zabezpečené heslo řetězec | Tento parametr použijte jenom v případě, že nastavíte parametr - AgentImage do kontejneru v privátním registru. Zadejte heslo pro přístup k registru. | 
| **SkipMobyCli** | Žádný | Platí pouze pokud - ContainerOS nastavená na Windows. Neinstalujte na $MobyInstallDirectory Moby rozhraní příkazového řádku (docker.exe). |

## <a name="verify-successful-installation"></a>Ověření úspěšné instalace

Můžete zkontrolovat stav této služby IoT Edge: 

```powershell
Get-Service iotedge
```

Zkontrolujte protokoly služby z posledních 5 minut pomocí:

```powershell

# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

A seznam s moduly:

```powershell
iotedge list
```

## <a name="uninstall-iot-edge"></a>Odinstalujte IoT Edge

Pokud chcete odebrat ze zařízení s Windows instalaci IoT Edge, použijte následující příkaz z okna prostředí PowerShell pro správu. Tento příkaz odebere modul runtime IoT Edge, spolu s vaší existující konfigurace a data Moby motoru. 

```PowerShell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-SecurityDaemon -DeleteConfig -DeleteMobyDataRoot
```

Pokud chcete znovu nainstalovat IoT Edge na zařízení, vynechte `-DeleteConfig` a `-DeleteMobyDataRoot` parametry tak, aby přeinstalujete démona zabezpečení později pomocí stávající informace o konfiguraci. 

## <a name="next-steps"></a>Další postup

Teď, když máte zařízení IoT Edge zřízené s modulem runtime nainstalovaný, je možné [nasadit moduly IoT Edge](how-to-deploy-modules-portal.md).

Pokud máte potíže s instalací IoT Edge správně, podívejte se [řešení potíží s](troubleshoot.md) stránky.

Aktualizace stávající instalace na nejnovější verzi služby IoT Edge, najdete v článku [aktualizovat démon zabezpečení IoT Edge a modulu runtime](how-to-update-iot-edge.md).
