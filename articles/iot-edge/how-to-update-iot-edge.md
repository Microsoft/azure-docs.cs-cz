---
title: Aktualizace verze IoT Edge na zařízeních – Azure IoT Edge | Dokumenty společnosti Microsoft
description: Jak aktualizovat zařízení IoT Edge tak, aby spouštěla nejnovější verze daemonu zabezpečení a runtime IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/07/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4a7c27beeb7208efcf6687e49193c8d3b68f5300
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77186515"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Aktualizace modulu runtime a procesu démon zabezpečení IoT Edge

Vzhledem k tomu, že služba IoT Edge vydává nové verze, budete chtít aktualizovat zařízení IoT Edge pro nejnovější funkce a vylepšení zabezpečení. Tento článek obsahuje informace o tom, jak aktualizovat zařízení IoT Edge, když je k dispozici nová verze.

Dvě součásti zařízení IoT Edge je třeba aktualizovat, pokud chcete přejít na novější verzi. Prvním z nich je bezpečnostní daemon, který běží na zařízení a spustí moduly runtime při spuštění zařízení. V současné době může být bezpečnostní daemon aktualizován pouze ze samotného zařízení. Druhou komponentou je modul runtime, který se skládá z modulů agenta IoT Edge hub a IoT Edge. V závislosti na tom, jak strukturujete nasazení, lze za běhu aktualizovat ze zařízení nebo vzdáleně.

Nejnovější verzi Azure IoT Edge najdete v [tématu Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>Aktualizace daemonu zabezpečení

Dém zabezpečení IoT Edge je nativní součást, kterou je třeba aktualizovat pomocí správce balíčků na zařízení IoT Edge.

Pomocí příkazu `iotedge version`zkontrolujte verzi bezpečnostního daemonu spuštěnou v zařízení .

### <a name="linux-devices"></a>Linuxová zařízení

Na zařízeních S II Pro Linux x64 aktualizujte bezpečnostního daemona na nejnovější verzi pomocí apt-get nebo příslušného správce balíčků.

```bash
apt-get update
apt-get install libiothsm iotedge
```

Pokud chcete aktualizovat na konkrétní verzi demonu zabezpečení, najděte verzi, na kterou chcete cílit z [vydání IoT Edge](https://github.com/Azure/azure-iotedge/releases). V této verzi vyhledejte příslušné soubory **libiothsm-std** a **iotedge** pro vaše zařízení. U každého souboru klepněte pravým tlačítkem myši na odkaz na soubor a zkopírujte adresu odkazu. Pomocí adresy odkazu nainstalujte konkrétní verze těchto součástí:

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
```

### <a name="windows-devices"></a>Zařízení s Windows

Na zařízeních se systémem Windows použijte skript Prostředí PowerShell k aktualizaci daemonu zabezpečení. Skript automaticky vytáhne nejnovější verzi bezpečnostního daemonu.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

Spuštění příkazu Update-IoTEdge odebere a aktualizuje daemon zabezpečení ze zařízení spolu se dvěma bitovými kopiemi kontejnerů runtime. Soubor config.yaml je uložen v zařízení, stejně jako data z modulu kontejneru Moby (pokud používáte kontejnery Windows). Uchovávání informací o konfiguraci znamená, že během procesu aktualizace není třeba znovu zadávat informace o připojovacím řetězci nebo službě Device Provisioning Service pro vaše zařízení.

Pokud chcete aktualizovat na konkrétní verzi demonu zabezpečení, najděte verzi, na kterou chcete cílit z [vydání IoT Edge](https://github.com/Azure/azure-iotedge/releases). V této verzi stáhněte soubor **Microsoft-Azure-IoTEdge.cab.** Potom použijte `-OfflineInstallationPath` parametr a přejděte na místní umístění souboru. Například:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux> -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>Parametr `-OfflineInstallationPath` hledá soubor s názvem **Microsoft-Azure-IoTEdge.cab** v adresáři k dispozici. Počínaje IoT Edge verze 1.0.9-rc4, existují dva .cab soubory k dispozici pro použití, jeden pro zařízení AMD64 a jeden pro ARM32. Stáhněte si správný soubor pro vaše zařízení a přejmenujte soubor a odeberte příponu architektury.

Další informace o možnostech aktualizace `Get-Help Update-IoTEdge -full` získáte pomocí příkazu nebo v části [Všechny parametry instalace](how-to-install-iot-edge-windows.md#all-installation-parameters).

## <a name="update-the-runtime-containers"></a>Aktualizace kontejnerů runtime

Způsob aktualizace agenta IoT Edge a kontejnerů centra IoT Edge závisí na tom, jestli ve vašem nasazení používáte postupné značky (například 1.0) nebo konkrétní značky (například 1.0.7).

Zkontrolujte verzi modulů ioT Edge agenta a centra IoT Edge, `iotedge logs edgeAgent` `iotedge logs edgeHub`které jsou aktuálně v zařízení, pomocí příkazů nebo .

  ![Najít verzi kontejneru v protokolech](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Principy značek IoT Edge

IoT Edge agent a ioT edge hub image jsou označeny verze IoT Edge, které jsou přidruženy. Existují dva různé způsoby použití značek s bitovými kopiemi za běhu:

* **Postupné značky** – Použijte pouze první dvě hodnoty čísla verze k získání nejnovějšího obrázku, který odpovídá těmto číslicím. Například 1.0 je aktualizován vždy, když je nová verze přejděte na nejnovější verzi 1.0.x. Pokud modul runtime kontejneru na zařízení IoT Edge znovu vytáhne image, moduly runtime se aktualizují na nejnovější verzi. Tento přístup je navržen pro účely vývoje. Nasazení z portálu Azure výchozí rolovací značky.

* **Konkrétní značky** – Pomocí všech tří hodnot čísla verze explicitně nastavte verzi obrázku. Například 1.0.7 se po počátečním vydání nezmění. Můžete deklarovat nové číslo verze v manifestu nasazení, když jste připraveni k aktualizaci. Tento přístup je navržen pro výrobní účely.

### <a name="update-a-rolling-tag-image"></a>Aktualizace obrázku svisáka značky

Pokud používáte postupné značky ve vašem nasazení (například mcr.microsoft.com/azureiotedge-hub:**1.0),** pak budete muset vynutit runtime kontejneru na vašem zařízení vytáhnout nejnovější verzi bitové kopie.

Odstraňte místní verzi obrázku ze zařízení IoT Edge. V počítačích se systémem Windows odinstalování medaidonu zabezpečení také odebere bitové kopie za běhu, takže tento krok nemusíte opakovat.

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

K odstranění obrázků může `-f` být nutné použít příznak síly.

Služba IoT Edge vytáhne nejnovější verze bitových kopií za běhu a znovu je automaticky spustí v zařízení.

### <a name="update-a-specific-tag-image"></a>Aktualizace konkrétního obrázku značky

Pokud ve svém nasazení používáte určité značky (například mcr.microsoft.com/azureiotedge-hub:**1.0.8),** stačí aktualizovat značku v manifestu nasazení a použít změny v zařízení.

1. V centru IoT Hub na webu Azure Portal vyberte zařízení IoT Edge a vyberte **Nastavit moduly**.

1. V části **Moduly okrajů IoT** vyberte **Nastavení modulu runtime**.

   ![Konfigurace nastavení běhu](./media/how-to-update-iot-edge/configure-runtime.png)

1. V **nastavení běhu**aktualizujte hodnotu **Image** pro Edge **Hub** požadovanou verzí. Ještě nevybírejte **Uložit.**

   ![Aktualizovat verzi bitové kopie centra Edge Hub](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. Sbalte nastavení **edge hubu** nebo posuňte se dolů a aktualizujte hodnotu **Image** pro **agenta edge** stejnou požadovanou verzí.

   ![Aktualizovat verzi agenta edge hubu](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. Vyberte **Uložit**.

1. Vyberte **Zkontrolovat + vytvořit**, zkontrolujte nasazení a vyberte **Vytvořit**.

## <a name="update-to-a-release-candidate-version"></a>Aktualizace na verzi kandidáta verze

Azure IoT Edge pravidelně vydává nové verze služby IoT Edge. Před každým stabilním vydáním existuje jedna nebo více verzí release candidate (RC). RC verze obsahují všechny plánované funkce pro vydání, ale stále procházejí testováním a ověřováním. Pokud chcete otestovat novou funkci brzy, můžete nainstalovat RC verzi a poskytnout zpětnou vazbu prostřednictvím GitHubu.

Verze kandidátů verze postupujte podle stejné číslování konvence vydání, ale mají **-rc** plus přírůstkové číslo připojené na konec. Kandidáti na vydání můžete zobrazit ve stejném seznamu [verzí Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases) jako stabilní verze. Například najít **1.0.7-rc1** a **1.0.7-rc2**, dva release kandidáty, které byly před **1.0.7**. Můžete také vidět, že RC verze jsou označeny **předběžnými popisky.**

Moduly agenta a rozbočovače IoT Edge mají verze RC, které jsou označeny stejnou konvencí. Například **mcr.microsoft.com/azureiotedge-hub:1.0.7-rc2**.

Jako náhledy nejsou verze pro kandidáty k verzi zahrnuty jako nejnovější verze, na kterou se zaměřují běžné instalační programy. Místo toho je třeba ručně cílit prostředky pro verzi RC, kterou chcete testovat. Z větší části je instalace nebo aktualizace na RC verzi stejná jako cílení na jakoukoli jinou konkrétní verzi IoT Edge s výjimkou jednoho rozdílu pro zařízení se systémem Windows. 

V případě kandidáta na vydání může mít skript prostředí PowerShell, který umožňuje instalaci a správu demonu zabezpečení IoT Edge na zařízení se systémem Windows, mít jiné funkce než nejnovější obecně dostupná verze. Kromě stažení souboru IoT Edge .cab pro RC si také stáhněte skript **IotEdgeSecurityDaemon.ps1.** Ke spuštění staženého skriptu v aktuálním zdroji použijte [dot sourcing.](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing) Například: 

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Update-IoTEdge -OfflineInstallationPath <path>
```

V částech tohoto článku se dozvíte, jak aktualizovat zařízení IoT Edge na konkrétní verzi modulů daemonu zabezpečení nebo modulů runtime.

Pokud instalujete IoT Edge do nového počítače, použijte následující odkazy, které se dozvíte, jak nainstalovat konkrétní verzi v závislosti na operačním systému zařízení:

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-or-specific-version-installation)

## <a name="next-steps"></a>Další kroky

Zobrazení nejnovějších [verzí Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

Buďte v obraze díky nejnovějším aktualizacím a oznámením v [blogu Internet věcí](https://azure.microsoft.com/blog/topics/internet-of-things/)
