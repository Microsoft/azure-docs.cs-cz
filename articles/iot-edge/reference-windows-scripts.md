---
title: Skripty pro Azure IoT Edge s kontejnery Windows | Microsoft Docs
description: Referenční informace o IoT Edge skriptů PowerShellu pro instalaci, odinstalaci nebo aktualizaci na zařízeních s Windows
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/06/2020
ms.topic: reference
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: a919238e4a62ae8954e101cb21a2fd4943191f6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103489520"
---
# <a name="powershell-scripts-for-iot-edge-with-windows-containers"></a>PowerShellové skripty pro IoT Edge s kontejnery Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Pochopení skriptů PowerShellu, které instalují, aktualizují nebo odinstalují IoT Edge na zařízeních s Windows.

Příkazy popsané v tomto článku jsou ze souboru, který se vydává v `IoTEdgeSecurityDaemon.ps1` každé [IoT Edge vydané verzi](https://github.com/Azure/azure-iotedge/releases). Nejnovější verze skriptu je vždy k dispozici na adrese aka.ms/iotedge-win.

K získání přístupu k nejnovější verzi skriptu můžete spustit libovolný příkaz pomocí `Invoke-WebRequest` rutiny. Například:

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge
```

Můžete si také stáhnout tento skript nebo verzi skriptu z konkrétní verze a spustit příkazy. Například:

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Deploy-IoTEdge
```

Zadaný skript je podepsaný ke zvýšení zabezpečení. Podpis můžete ověřit stažením skriptu na zařízení a následným spuštěním následujícího příkazu PowerShellu:

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

Stav výstupu je **platný** , pokud je podpis ověřen.

## <a name="deploy-iotedge"></a>Deploy-IoTEdge

Příkaz Deploy-IoTEdge stáhne a nasadí proces démona zabezpečení IoT Edge a jeho závislosti. Příkaz nasazení akceptuje tyto společné parametry, mimo jiné. Úplný seznam získáte pomocí příkazu `Get-Help Deploy-IoTEdge -full` .  

| Parametr | Přípustné hodnoty | Komentáře |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** nebo **Linux** | Pokud není zadaný žádný operační systém kontejneru, výchozí hodnota je Windows.<br><br>V případě kontejnerů Windows používá IoT Edge modul kontejnerů Moby, který je součástí instalace. V případě kontejnerů Linux je před zahájením instalace nutné nainstalovat kontejnerový modul. |
| **Proxy server** | Adresa URL proxy serveru | Tento parametr uveďte, pokud vaše zařízení potřebuje k připojení k Internetu projít proxy server. Další informace najdete v tématu [Konfigurace zařízení IoT Edge pro komunikaci prostřednictvím proxy server](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Cesta k adresáři | Pokud je tento parametr zahrnutý, instalační program zkontroluje uvedený adresář pro soubory IoT Edge CAB a runtime VC, které jsou potřebné pro instalaci. Všechny soubory, které nebyly nalezeny v adresáři, se stáhnou. Pokud jsou oba soubory v adresáři, můžete nainstalovat IoT Edge bez připojení k Internetu. Tento parametr můžete použít také k použití konkrétní verze. |
| **InvokeWebRequestParameters** | Zatřiďovací tabulka parametrů a hodnot | Během instalace se provedou několik webových požadavků. Pomocí tohoto pole můžete nastavit parametry pro tyto webové požadavky. Tento parametr je vhodný pro konfiguraci přihlašovacích údajů pro proxy servery. Další informace najdete v tématu [Konfigurace zařízení IoT Edge pro komunikaci prostřednictvím proxy server](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | žádné | Tento příznak umožňuje skriptu pro nasazení restartovat počítač bez výzvy, pokud je to nutné. |

## <a name="initialize-iotedge"></a>Initialize-IoTEdge

Příkaz Initialize-IoTEdge nakonfiguruje IoT Edge s připojovacím řetězcem zařízení a provozními podrobnostmi. Většinu informací generovaných tímto příkazem je pak Uloženo v souboru iotedge\config.yaml. Inicializační příkaz akceptuje tyto společné parametry, mimo jiné. Úplný seznam získáte pomocí příkazu `Get-Help Initialize-IoTEdge -full` .

| Parametr | Přípustné hodnoty | Komentáře |
| --------- | --------------- | -------- |
| **ManualConnectionString** | Žádné | **Parametr Switch** **Výchozí hodnota**. Pokud není zadaný žádný typ zřizování, je výchozí hodnotou ruční zřízení s připojovacím řetězcem.<br><br>Deklaruje, že budete zadat připojovací řetězec zařízení pro ruční zřízení zařízení. |
| **ManualX509** | Žádné | **Parametr Switch** Pokud není zadaný žádný typ zřizování, je výchozí hodnotou ruční zřízení s připojovacím řetězcem.<br><br>Deklaruje, že poskytnete certifikát identity a privátní klíč pro ruční zřízení zařízení.
| **DpsTpm** | Žádné | **Parametr Switch** Pokud není zadaný žádný typ zřizování, je výchozí hodnotou ruční zřízení s připojovacím řetězcem.<br><br>Deklarujete, že zadáte ID oboru služby Device Provisioning Service (DPS) a ID registrace vašeho zařízení, které se bude zřizovat prostřednictvím DPS.  |
| **DpsSymmetricKey** | Žádné | **Parametr Switch** Pokud není zadaný žádný typ zřizování, je výchozí hodnotou ruční zřízení s připojovacím řetězcem.<br><br>Deklaruje, že poskytnete ID oboru služby Device Provisioning Service (DPS) a ID registrace vašeho zařízení, které se bude zřizovat prostřednictvím DPS, společně s symetrickým klíčem pro ověření identity. |
| **DpsX509** | Žádné | **Parametr Switch** Pokud není zadaný žádný typ zřizování, je výchozí hodnotou ruční zřízení s připojovacím řetězcem.<br><br>Deklaruje, že poskytnete ID oboru služby Device Provisioning Service (DPS) a ID registrace vašeho zařízení, které se bude zřizovat prostřednictvím DPS, společně s certifikátem identity X. 509 a privátním klíčem pro ověření identity.  |
| **DeviceConnectionString** | Připojovací řetězec ze zařízení IoT Edge zaregistrovaného v IoT Hub v jednoduchých uvozovkách | **Vyžaduje** se pro ruční zřizování s připojovacím řetězcem. Pokud v parametrech skriptu nezadáte připojovací řetězec, zobrazí se výzva k zadání. |
| **IotHubHostName** | Název hostitele IoT Hub, ke kterému se zařízení připojuje | **Vyžaduje** se pro ruční zřizování pomocí certifikátů X. 509. Má formát *{hub Name}. Azure-Devices.NET*. |
| **DeviceId** | ID zařízení z registrované identity zařízení v IoT Hub. | **Vyžaduje** se pro ruční zřizování pomocí certifikátů X. 509. |
| **Objekt ScopeId** | ID oboru z instance služby Device Provisioning přidružené k vašemu IoT Hub. | **Vyžaduje** se pro zřizování DPS. Pokud nezadáte ID oboru v parametrech skriptu, budete vyzváni k jeho zadání. |
| **RegistrationId** | ID registrace generované vaším zařízením | **Vyžaduje** se pro zřizování DPS, pokud používáte ověřování pomocí čipu TPM nebo symetrického klíče. **Nepovinné** , pokud se používá ověřování certifikátů X. 509. |
| **X509IdentityCertificate** | Cesta URI k certifikátu identity zařízení X. 509 na zařízení. | **Vyžaduje** se při zřizování ručních nebo DPS, pokud se používá ověření certifikátu X. 509. |
| **X509IdentityPrivateKey** | Cesta URI k klíči certifikátu identity zařízení X. 509 na zařízení. | **Vyžaduje** se při zřizování ručních nebo DPS, pokud se používá ověření certifikátu X. 509. |
| **SymmetricKey** | Symetrický klíč, který slouží k zřízení identity IoT Edge zařízení při použití DPS | **Vyžaduje** se pro zřizování DPS, pokud používá ověření identity symetrického klíče. |
| **ContainerOs** | **Windows** nebo **Linux** | Pokud není zadaný žádný operační systém kontejneru, výchozí hodnota je Windows.<br><br>V případě kontejnerů Windows používá IoT Edge modul kontejnerů Moby, který je součástí instalace. V případě kontejnerů Linux je před zahájením instalace nutné nainstalovat kontejnerový modul. |
| **DeviceCACertificate** | Cesta URI k certifikátu certifikační autority zařízení X. 509 v zařízení. | Lze také nakonfigurovat v `C:\ProgramData\iotedge\config.yaml` souboru. Další informace najdete v tématu [Správa certifikátů na zařízení IoT Edge](how-to-manage-device-certificates.md). |
| **DeviceCAPrivateKey** | Cesta k identifikátoru URI k privátnímu klíči certifikační autority zařízení X. 509 na zařízení. | Lze také nakonfigurovat v `C:\ProgramData\iotedge\config.yaml` souboru. Další informace najdete v tématu [Správa certifikátů na zařízení IoT Edge](how-to-manage-device-certificates.md). |
| **InvokeWebRequestParameters** | Zatřiďovací tabulka parametrů a hodnot | Během instalace se provedou několik webových požadavků. Pomocí tohoto pole můžete nastavit parametry pro tyto webové požadavky. Tento parametr je vhodný pro konfiguraci přihlašovacích údajů pro proxy servery. Další informace najdete v tématu [Konfigurace zařízení IoT Edge pro komunikaci prostřednictvím proxy server](how-to-configure-proxy-support.md). |
| **AgentImage** | Identifikátor URI image agenta IoT Edge | Ve výchozím nastavení používá nová instalace IoT Edge nejnovější značku pro Image agenta IoT Edge. Tento parametr použijte k nastavení konkrétní značky pro verzi obrázku nebo k poskytnutí vlastní image agenta. Další informace najdete v tématu [Vysvětlení značek IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Uživatelské jméno** | Uživatelské jméno registru kontejneru | Tento parametr použijte pouze v případě, že nastavíte parametr-AgentImage na kontejner v privátním registru. Zadejte uživatelské jméno s přístupem k registru. |
| **Heslo** | Řetězec zabezpečeného hesla | Tento parametr použijte pouze v případě, že nastavíte parametr-AgentImage na kontejner v privátním registru. Zadejte heslo pro přístup do registru. |

## <a name="update-iotedge"></a>Update-IoTEdge

| Parametr | Přípustné hodnoty | Komentáře |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** nebo **Linux** | Pokud není zadaný žádný kontejnerový operační systém, je Windows výchozí hodnota. V případě kontejnerů Windows se v instalaci zahrne modul kontejnerů. V případě kontejnerů Linux je před zahájením instalace nutné nainstalovat kontejnerový modul. |
| **Proxy server** | Adresa URL proxy serveru | Tento parametr uveďte, pokud vaše zařízení potřebuje k připojení k Internetu projít proxy server. Další informace najdete v tématu [Konfigurace zařízení IoT Edge pro komunikaci prostřednictvím proxy server](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Zatřiďovací tabulka parametrů a hodnot | Během instalace se provedou několik webových požadavků. Pomocí tohoto pole můžete nastavit parametry pro tyto webové požadavky. Tento parametr je vhodný pro konfiguraci přihlašovacích údajů pro proxy servery. Další informace najdete v tématu [Konfigurace zařízení IoT Edge pro komunikaci prostřednictvím proxy server](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Cesta k adresáři | Pokud je tento parametr zahrnutý, instalační program zkontroluje uvedený adresář pro soubory IoT Edge CAB a runtime VC, které jsou potřebné pro instalaci. Všechny soubory, které nebyly nalezeny v adresáři, se stáhnou. Pokud jsou oba soubory v adresáři, můžete nainstalovat IoT Edge bez připojení k Internetu. Tento parametr můžete použít také k použití konkrétní verze. |
| **RestartIfNeeded** | žádné | Tento příznak umožňuje skriptu pro nasazení restartovat počítač bez výzvy, pokud je to nutné. |

## <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| Parametr | Přípustné hodnoty | Komentáře |
| --------- | --------------- | -------- |
| **Force** | žádné | Tento příznak vynutí odinstalaci v případě neúspěchu předchozího pokusu o odinstalaci.
| **RestartIfNeeded** | žádné | Tento příznak umožňuje, aby skript pro odinstalaci restartoval počítač bez výzvy, pokud je to nutné. |

## <a name="next-steps"></a>Další kroky

Naučte se používat tyto příkazy v následujícím článku:

* [Instalace nebo odinstalace Azure IoT Edge pro Windows](how-to-install-iot-edge-windows-on-windows.md)
