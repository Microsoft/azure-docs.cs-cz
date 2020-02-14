---
title: Aktualizace verze IoT Edge na zařízení – Azure IoT Edge | Dokumentace Microsoftu
description: Jak aktualizovat zařízení IoT Edge spustit nejnovější verze démona zabezpečení a modul runtime IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/07/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4a7c27beeb7208efcf6687e49193c8d3b68f5300
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186515"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Aktualizace zabezpečení démon IoT Edge a modulu runtime

Jelikož služba IoT Edge uvolní nové verze, budete chtít aktualizovat IoT Edge zařízení o nejnovější funkce a vylepšení zabezpečení. Tento článek obsahuje informace o tom, jak aktualizovat zařízení IoT Edge, když je dostupná nová verze.

Dvě součásti zařízení IoT Edge je potřeba aktualizovat, pokud chcete přejít na novější verzi. První je démon zabezpečení, který běží na zařízení a spouští moduly runtime při spuštění zařízení. Proces démon zabezpečení v současné době je možné pouze aktualizovat ze samotného zařízení. Druhá součást je modul runtime, který se skládá z modulů IoT Edge hub a agentů IoT Edge. V závislosti na tom, jak strukturovat vaše nasazení modul runtime aktualizovat ze zařízení nebo vzdáleně.

Pokud chcete najít nejnovější verzi Azure IoT Edge, přečtěte si téma [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>Aktualizace zabezpečení démona

Démon zabezpečení IoT Edge je nativní součást, kterou je potřeba aktualizovat pomocí Správce balíčků na zařízení IoT Edge.

Pomocí příkazu `iotedge version`ověřte verzi démona zabezpečení spuštěnou na vašem zařízení.

### <a name="linux-devices"></a>Linux zařízení

Na zařízeních se systémem Linux x64 pomocí apt-get nebo odpovídajícího správce balíčků aktualizujte démona zabezpečení na nejnovější verzi.

```bash
apt-get update
apt-get install libiothsm iotedge
```

Pokud chcete aktualizovat na konkrétní verzi démona zabezpečení, vyhledejte verzi, kterou chcete cílit z [IoT Edge verzí](https://github.com/Azure/azure-iotedge/releases). V této verzi vyhledejte příslušné soubory **libiothsm-STD** a **iotedge** pro vaše zařízení. Pro každý soubor klikněte pravým tlačítkem na odkaz na soubor a zkopírujte adresu odkazu. Pomocí adresy odkaz nainstalujte konkrétní verze těchto součástí:

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
```

### <a name="windows-devices"></a>Zařízení Windows

Na zařízeních s Windows aktualizujte démona zabezpečení pomocí skriptu PowerShellu. Skript automaticky vyžádá nejnovější verzi démona zabezpečení.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

Spuštění příkazu Update-IoTEdge odebere a aktualizuje démona zabezpečení ze zařízení spolu se dvěma bitovými kopiemi kontejnerů modulu runtime. Soubor config. yaml se uchovává v zařízení a také data z modulu kontejneru Moby (Pokud používáte kontejnery Windows). Udržování informací o konfiguraci znamená, že v průběhu procesu aktualizace nemusíte znovu zadávat informace o připojovacím řetězci nebo službě Device Provisioning pro vaše zařízení.

Pokud chcete aktualizovat na konkrétní verzi démona zabezpečení, vyhledejte verzi, kterou chcete cílit z [IoT Edge verzí](https://github.com/Azure/azure-iotedge/releases). V této verzi Stáhněte soubor **Microsoft-Azure-IoTEdge. cab** . Pak použijte parametr `-OfflineInstallationPath`, aby odkazoval na umístění místního souboru. Například:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux> -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>Parametr `-OfflineInstallationPath` v zadaném adresáři vyhledá soubor s názvem **Microsoft-Azure-IoTEdge. cab** . Počínaje IoT Edge verzí 1.0.9-RC4 jsou k dispozici dva soubory. cab, jeden pro zařízení AMD64 a jeden pro ARM32. Stáhněte si správný soubor pro vaše zařízení a pak přejmenujte soubor, abyste odebrali příponu architektury.

Pokud chcete získat další informace o možnostech aktualizace, použijte příkaz `Get-Help Update-IoTEdge -full` nebo se podívejte na [všechny parametry instalace](how-to-install-iot-edge-windows.md#all-installation-parameters).

## <a name="update-the-runtime-containers"></a>Aktualizovat kontejnerů modulu runtime

Způsob aktualizace IoT Edge agentů a kontejnerů centra IoT Edge závisí na tom, zda používáte ve svém nasazení valení značky (například 1,0) nebo konkrétní značky (například 1.0.7).

Pomocí příkazů `iotedge logs edgeAgent` nebo `iotedge logs edgeHub`vyhledejte verzi IoT Edge agenta a IoT Edgech modulů centra, které jsou aktuálně na vašem zařízení.

  ![Kontejner verzi naleznete v protokolech](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Vysvětlení značky IoT Edge

IoT Edge agent a image centra IoT Edge jsou označené verzí IoT Edge, ke které jsou přidružené. Existují dva různé způsoby použití značky pomocí bitové kopie modulu runtime:

* **Válcování značek** – použijte pouze první dvě hodnoty čísla verze, abyste získali nejnovější obrázek, který se shoduje s těmito číslicemi. 1\.0 je třeba aktualizovat vždycky, když je nová verze tak, aby odkazoval na nejnovější verzi 1.0.x. Pokud modul runtime kontejneru na vašem zařízení IoT Edge znovu stáhne bitovou kopii, moduly runtime jsou aktualizovány na nejnovější verzi. Tento přístup je určeno pro účely vývoje. Nasazení z portálu Azure výchozí značky se zajištěním provozu.

* **Konkrétní značky** – k explicitnímu nastavení verze image použijte všechny tři hodnoty čísla verze. Například 1.0.7 se po počáteční verzi nemění. Jakmile budete připraveni k aktualizaci je možné deklarovat nové číslo verze v manifestu nasazení. Tento přístup doporučuje pro produkční účely.

### <a name="update-a-rolling-tag-image"></a>Aktualizovat bitovou kopii postupné značky

Pokud ve svém nasazení používáte přístupné značky (například mcr.microsoft.com/azureiotedge-hub:**1,0**), musíte vynutit modul runtime kontejneru v zařízení, aby využíval nejnovější verzi image.

Odstraňte místní verzi image ze zařízení IoT Edge. V počítačích se systémem Windows odinstalování démona zabezpečení také odstraní image za běhu, takže tento krok nemusíte znovu provádět.

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

K odebrání imagí možná budete muset použít příznak vynucení `-f`.

Služba IoT Edge o přijetí změn nejnovější verze modulu runtime obrázků a automaticky je znovu spusťte ve vašem zařízení.

### <a name="update-a-specific-tag-image"></a>Aktualizace image s konkrétní značkou

Použijete-li v nasazení konkrétní značky (například mcr.microsoft.com/azureiotedge-hub:**1.0.8**), je nutné provést aktualizaci značky v manifestu nasazení a použít změny v zařízení.

1. V IoT Hub v Azure Portal vyberte zařízení IoT Edge a vyberte možnost **nastavit moduly**.

1. V části **IoT Edge moduly** vyberte **nastavení modulu runtime**.

   ![Konfigurace nastavení modulu runtime](./media/how-to-update-iot-edge/configure-runtime.png)

1. V **nastavení modulu runtime**aktualizujte hodnotu **Image** pro **centrum Edge** s požadovanou verzí. Nevybírejte možnost **Uložit** pouze zatím.

   ![Aktualizovat verzi obrázku centra hran](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. Sbalte nastavení **hraničního centra** nebo přejděte dolů a aktualizujte hodnotu **Image** pro **agenta Edge** se stejnou požadovanou verzí.

   ![Aktualizace verze agenta centra hran](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. Vyberte **Save** (Uložit).

1. Vyberte **zkontrolovat + vytvořit**, zkontrolujte nasazení a vyberte **vytvořit**.

## <a name="update-to-a-release-candidate-version"></a>Aktualizace na verzi Release Candidate

Azure IoT Edge pravidelně uvolňuje nové verze služby IoT Edge. Před každou stabilní verzí je k dispozici nejméně jedna verze Release Candidate (RC). Verze RC zahrnují všechny plánované funkce pro tuto verzi, ale stále procházejí testováním a ověřováním. Pokud chcete otestovat novou funkci v brzkém případě, můžete nainstalovat verzi RC a poskytnout zpětnou vazbu prostřednictvím GitHubu.

Verze kandidáta Release mají stejnou konvenci číslování verzí, ale mají **-RC** plus přírůstkové číslo připojené ke konci. Kandidáty na vydání verze si můžete prohlédnout ve stejném seznamu [Azure IoT Edge vydání](https://github.com/Azure/azure-iotedge/releases) jako stabilní verze. Například vyhledejte **1.0.7-RC1** a **1.0.7-RC2**, které byly vydány před **1.0.7**. Můžete si také prohlédnout, že verze RC jsou označeny pomocí popisků **před vydáním** .

Agenti IoT Edge a moduly rozbočovače mají verze RC, které jsou označené stejnou konvencí. Například **MCR.Microsoft.com/azureiotedge-hub:1.0.7-RC2**.

Ve verzi Preview jsou Release Candidate verze nezahrnuté jako nejnovější verze, které jsou určené pro běžné instalační programy. Místo toho je nutné ručně cílit prostředky na verzi RC, kterou chcete testovat. Ve většině případů je instalace nebo aktualizace verze RC stejná jako u libovolné jiné konkrétní verze IoT Edge s výjimkou jednoho rozdílu pro zařízení s Windows. 

V Release Candidate skript PowerShellu, který vám umožní nainstalovat a spravovat funkci démona zabezpečení IoT Edge na zařízení s Windows, může mít různé funkce než nejnovější všeobecně dostupná verze. Kromě stažení souboru IoT Edge. cab pro RC si také Stáhněte skript **IotEdgeSecurityDaemon. ps1** . Ke spuštění staženého skriptu v aktuálním zdroji použijte [koncový bod původu](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing) . Například: 

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Update-IoTEdge -OfflineInstallationPath <path>
```

V částech v tomto článku se dozvíte, jak aktualizovat zařízení IoT Edge na určitou verzi modulů pro démona zabezpečení nebo modul runtime.

Pokud instalujete IoT Edge do nového počítače, pomocí následujících odkazů se dozvíte, jak nainstalovat konkrétní verzi v závislosti na operačním systému zařízení:

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-or-specific-version-installation)

## <a name="next-steps"></a>Další kroky

Prohlédněte si nejnovější [verze Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

Udržujte si přehled o nejnovějších aktualizacích a oznámeních na [blogu Internet věcí](https://azure.microsoft.com/blog/topics/internet-of-things/)
