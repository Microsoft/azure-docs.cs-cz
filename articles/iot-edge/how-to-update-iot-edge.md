---
title: Aktualizace verze IoT Edge na zařízení – Azure IoT Edge | Dokumentace Microsoftu
description: Jak aktualizovat zařízení IoT Edge spustit nejnovější verze démona zabezpečení a modul runtime IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f31dec1d654124f9071c01c0e5d033db4b65a1d3
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76509661"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Aktualizace zabezpečení démon IoT Edge a modulu runtime

Jelikož služba IoT Edge uvolní nové verze, budete chtít aktualizovat IoT Edge zařízení o nejnovější funkce a vylepšení zabezpečení. Tento článek obsahuje informace o tom, jak aktualizovat zařízení IoT Edge, když je dostupná nová verze.

Dvě součásti zařízení IoT Edge je potřeba aktualizovat, pokud chcete přejít na novější verzi. První je démon zabezpečení, který běží na zařízení a spouští moduly runtime při spuštění zařízení. Proces démon zabezpečení v současné době je možné pouze aktualizovat ze samotného zařízení. Druhá součást je modul runtime, který se skládá z modulů IoT Edge hub a agentů IoT Edge. V závislosti na tom, jak strukturovat vaše nasazení modul runtime aktualizovat ze zařízení nebo vzdáleně.

Nejnovější verzi služby Azure IoT Edge najdete v tématu [Azure IoT Edge uvolní](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>Aktualizace zabezpečení démona

Démon zabezpečení IoT Edge je nativní součást, kterou je potřeba aktualizovat pomocí Správce balíčků na zařízení IoT Edge.

Zkontrolujte verzi démona zabezpečení běžícího ve vašem zařízení s použitím příkazu `iotedge version`.

### <a name="linux-devices"></a>Linux zařízení

V zařízeních se systémem Linux x64 pomocí apt-get nebo odpovídajícího správce balíčků aktualizujte démona zabezpečení.

```bash
apt-get update
apt-get install libiothsm iotedge
```

Na zařízeních se systémem Linux ARM32 nainstalujte nejnovější verzi démona zabezpečení pomocí postupu v části [Instalace modulu runtime Azure IoT Edge v systému Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) .

### <a name="windows-devices"></a>Zařízení Windows

Na zařízeních s Windows aktualizujte démona zabezpečení pomocí skriptu PowerShellu. Skript automaticky vyžádá nejnovější verzi démona zabezpečení.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

Spuštění příkazu Update-IoTEdge odebere a aktualizuje démona zabezpečení ze zařízení spolu se dvěma bitovými kopiemi kontejnerů modulu runtime. Soubor config. yaml se uchovává v zařízení a také data z modulu kontejneru Moby (Pokud používáte kontejnery Windows). Udržování informací o konfiguraci znamená, že v průběhu procesu aktualizace nemusíte znovu zadávat informace o připojovacím řetězci nebo službě Device Provisioning pro vaše zařízení.

Pokud chcete nainstalovat určitou verzi démona zabezpečení, Stáhněte si příslušný soubor Microsoft-Azure-IoTEdge. cab z [IoT Edge vydání](https://github.com/Azure/azure-iotedge/releases). Pak použijte parametr `-OfflineInstallationPath`, aby odkazoval na umístění souboru. Další informace najdete v tématu [instalace offline](how-to-install-iot-edge-windows.md#offline-installation).

## <a name="update-the-runtime-containers"></a>Aktualizovat kontejnerů modulu runtime

Způsob aktualizace IoT Edge agentů a kontejnerů centra IoT Edge závisí na tom, zda používáte ve svém nasazení valení značky (například 1,0) nebo konkrétní značky (například 1.0.7).

Pomocí příkazů `iotedge logs edgeAgent` nebo `iotedge logs edgeHub`vyhledejte verzi IoT Edge agenta a IoT Edgech modulů centra, které jsou aktuálně na vašem zařízení.

  ![Kontejner verzi naleznete v protokolech](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Vysvětlení značky IoT Edge

IoT Edge agent a image centra IoT Edge jsou označené verzí IoT Edge, ke které jsou přidružené. Existují dva různé způsoby použití značky pomocí bitové kopie modulu runtime:

* **Značky se zajištěním provozu** – pouze první dvě hodnoty čísla verze můžete získat nejnovější image, která odpovídá těchto číslic. 1\.0 je třeba aktualizovat vždycky, když je nová verze tak, aby odkazoval na nejnovější verzi 1.0.x. Pokud modul runtime kontejneru na vašem zařízení IoT Edge znovu stáhne bitovou kopii, moduly runtime jsou aktualizovány na nejnovější verzi. Tento přístup je určeno pro účely vývoje. Nasazení z portálu Azure výchozí značky se zajištěním provozu.

* **Konkrétními značkami** -použít všechny tři hodnoty čísla verze explicitně nastavit verzi image. Například 1.0.7 se po počáteční verzi nemění. Jakmile budete připraveni k aktualizaci je možné deklarovat nové číslo verze v manifestu nasazení. Tento přístup doporučuje pro produkční účely.

### <a name="update-a-rolling-tag-image"></a>Aktualizovat bitovou kopii postupné značky

Pokud používáte kumulativní značky ve vašem nasazení (například mcr.microsoft.com/azureiotedge-hub:**1.0**) budete muset vynutit modul runtime kontejneru na vašem zařízení o přijetí změn nejnovější verzi image.

Odstraňte místní verzi image ze zařízení IoT Edge. V počítačích se systémem Windows odinstalování démona zabezpečení také odstraní image za běhu, takže tento krok nemusíte znovu provádět.

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Budete možná muset použít síla `-f` příznak odebrat bitové kopie.

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

Azure IoT Edge pravidelně uvolňuje nové verze služby IoT Edge. Před každou stabilní verzí je k dispozici nejméně jedna verze Release Candidate (RC). Verze RC obsahují všechny plánované funkce pro vydání, ale stále procházejí procesy testování a ověření vyžadované pro stabilní vydání. Pokud chcete novou funkci otestovat brzy, můžete nainstalovat verzi RC a poskytnout zpětnou vazbu prostřednictvím GitHubu.

Verze kandidáta Release mají stejnou konvenci číslování verzí, ale mají **-RC** plus přírůstkové číslo připojené ke konci. Kandidáty na vydání verze si můžete prohlédnout ve stejném seznamu [Azure IoT Edge vydání](https://github.com/Azure/azure-iotedge/releases) jako stabilní verze. Například vyhledejte **1.0.7-RC1** a **1.0.7-RC2**, které byly vydány před **1.0.7**. Můžete si také prohlédnout, že verze RC jsou označeny pomocí popisků **před vydáním** .

Ve verzi Preview jsou Release Candidate verze nezahrnuté jako nejnovější verze, které jsou určené pro běžné instalační programy. Místo toho je nutné ručně cílit prostředky na verzi RC, kterou chcete testovat. V závislosti na operačním systému IoT Edge zařízení aktualizujte IoT Edge na konkrétní verzi pomocí následujících částí:

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-installation)

## <a name="next-steps"></a>Další kroky

Zobrazit nejnovější [Azure IoT Edge uvolní](https://github.com/Azure/azure-iotedge/releases).

Udržujte si přehled o nejnovějších aktualizacích a oznámeních na [blogu Internet věcí](https://azure.microsoft.com/blog/topics/internet-of-things/)
