---
title: Zřizovací zařízení s virtuálním čipem TPM na virtuálním počítači s Linuxem – Azure IoT Edge
description: Použití simulovaného čipu TPM na virtuálním počítači s Linuxem k testování služby Azure Device Provisioning Service pro Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6bb1282212ccff45f179b8750e3ed8aec27d129e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511055"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Vytvoření a zřízení zařízení IoT Edge s virtuálním čipem TPM na virtuálním počítači s Linuxem

Zařízení Azure IoT Edge se dá automaticky zřídit pomocí [služby Device Provisioning Service](../iot-dps/index.yml). Pokud nejste obeznámeni s procesem automatického zřizování, zkontrolujte [koncepty automatického zřizování](../iot-dps/concepts-auto-provisioning.md) před pokračováním.

Tento článek ukazuje, jak otestovat automatické zřizování na simulovaném zařízení IoT Edge pomocí následujících kroků:

* Vytvořte virtuální počítač (VM) linuxového počítače v hyper-V se simulovaným modulem Důvěryhodné platformy (TPM) pro zabezpečení hardwaru.
* Vytvořte instanci služby Zřizování zařízení služby IoT Hub (DPS).
* Vytvoření individuální registrace pro zařízení
* Instalace runtime IoT Edge a připojení zařízení k centru IoT Hub

> [!TIP]
> Tento článek popisuje, jak otestovat zřizování DPS pomocí simulátoru Čipu TPM, ale velká část se vztahuje na fyzický hardware čipu TPM, jako je [infineon&trade; OPTIGA TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), zařízení s certifikací Azure pro IoT.
>
> Pokud používáte fyzické zařízení, můžete přeskočit na [načíst informace z zřizování z fyzického zařízení](#retrieve-provisioning-information-from-a-physical-device) části v tomto článku.

## <a name="prerequisites"></a>Požadavky

* Vývojový počítač systému Windows s [povolenou technologií Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). Tento článek používá Windows 10 se systémem Ubuntu Server VM.
* Aktivní služba IoT Hub.
* Pokud používáte simulovaný čip TPM, [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 nebo novější s povoleným [zatížením "Vývoj plochy s C++".](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/)

> [!NOTE]
> Čip TPM 2.0 je vyžadován při použití atestace čipu TPM s DPS a lze jej použít pouze k vytvoření individuálních, nikoli skupinových zápisů.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Vytvoření virtuálního počítače s Linuxem s virtuálním čipem TPM

V této části vytvoříte nový virtuální počítač Linux u technologie Hyper-V. Tento virtuální počítač jste nakonfigurovali se simulovaným čipem TPM, abyste ho mohli použít k testování toho, jak funguje automatické zřizování s IoT Edge.

### <a name="create-a-virtual-switch"></a>Vytvoření virtuálního přepínače

Virtuální přepínač umožňuje virtuálnímu počítači připojit se k fyzické síti.

1. Otevřete správce technologie Hyper-V na počítači se systémem Windows.

2. V nabídce **Akce** vyberte **Správce virtuálních přepínačů**.

3. Zvolte **externí** virtuální přepínač a pak vyberte **Vytvořit virtuální přepínač**.

4. Pojmenujte nový virtuální přepínač, například **EdgeSwitch**. Zkontrolujte, zda je typ připojení nastaven na **externí síť**, a pak vyberte **ok**.

5. Automaticky otevírané okno vás upozorní, že připojení k síti může být přerušeno. Chcete-li pokračovat, vyberte **možnost Ano.**

Pokud se při vytváření nového virtuálního přepínače zobrazí chyby, ujistěte se, že adaptér ethernet nepoužívají žádné jiné přepínače a že žádné jiné přepínače nepoužívají stejný název.

### <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

1. Stáhněte si soubor s bitovou kopií disku, který chcete použít pro svůj virtuální počítač, a uložte ho místně. [Například, Ubuntu server](https://www.ubuntu.com/download/server).

2. Ve Správci technologie Hyper-V znovu vyberte **nový** > **virtuální počítač** v nabídce **Akce.**

3. Dokončete **Průvodce novým virtuálním počítačem** pomocí následujících specifických konfigurací:

   1. **Určit generování**: Vyberte **generaci 2**. Virtuální počítače generace 2 mají povolenou vnořenou virtualizaci, která je nutná ke spuštění IoT Edge na virtuálním počítači.
   2. **Konfigurace sítě**: Nastavte hodnotu **připojení** k virtuálnímu přepínači, který jste vytvořili v předchozí části.
   3. **Možnosti instalace**: Vyberte **nainstalovat operační systém ze spouštěcího souboru bitové kopie** a přejděte do souboru bitové kopie disku, který jste uložili místně.

4. **Chcete-li** vytvořit virtuální počítač, vyberte v průvodci dokončit.

Vytvoření nového virtuálního virtuálního movitého virtuálního movitého virtuálního<e. může trvat několik minut.

### <a name="enable-virtual-tpm"></a>Povolení virtuálního čipu TPM

Po vytvoření virtuálního počítače otevřete jeho nastavení a povolte modul virtuální důvěryhodné platformy (TPM), který vám umožní automaticky zřídit zařízení.

1. Vyberte virtuální počítač a otevřete jeho **Nastavení**.

2. Přejděte na **zabezpečení**.

3. Zrušit zaškrtnutí **políčka Povolit zabezpečené spouštění**.

4. Zaškrtněte **políčko Povolit modul důvěryhodné platformy**.

5. Klikněte na tlačítko **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Spuštění virtuálního počítače a shromažďování dat čipu TPM

Ve virtuálním počítači vytvořte nástroj, který můžete použít k načtení **Registračního ID** a **ověřovacího klíče**zařízení .

1. Spusťte virtuální počítač a připojte se k němu.

1. Postupujte podle pokynů ve virtuálním počítači k dokončení procesu instalace a restartování počítače.

1. Přihlaste se k virtuálnímu počítači a pak postupujte podle kroků v [části Nastavení vývojového prostředí Linuxu](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) k instalaci a sestavení sady Azure IoT device SDK pro C.

   >[!TIP]
   >V průběhu tohoto článku budete kopírovat a vložit z virtuálního počítače, což není snadné prostřednictvím aplikace připojení Správce Hyper-V. Chcete-li načíst jeho IP adresu, můžete se k virtuálnímu počítači připojit prostřednictvím správce Technologie Hyper-V: `ifconfig`. Potom můžete použít IP adresu pro připojení `ssh <username>@<ipaddress>`přes SSH: .

1. Spusťte následující příkazy k vytvoření nástroje SDK, který načte informace o zřizování zařízení ze simulátoru čipu TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Z příkazového okna přejděte do `azure-iot-sdk-c` adresáře a spusťte simulátor čipu TPM. Ten naslouchá přes soket na portech 2321 a 2322. Nezavírejte toto příkazové okno. budete muset udržet tento simulátor spuštěn.

   V `azure-iot-sdk-c` adresáři spusťte simulátor následujícím příkazem:

   ```bash
   ./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe
   ```

1. Pomocí sady Visual Studio otevřete `cmake` řešení `azure_iot_sdks.sln`generované v adresáři s názvem a vytvořte ho pomocí příkazu **Sestavit řešení** v nabídce **Sestavení.**

1. V podokně **Průzkumník řešení** v sadě Visual Studio přejděte do složky **Provision\_Tools**. Klikněte pravým tlačítkem na projekt **tpm_device_provision** a vyberte **Nastavit jako spouštěný projekt**.

1. Spusťte řešení pomocí některého z příkazů **Start** v nabídce **Ladění.** Ve výstupním okně se zobrazí **ID registrace** simulátoru čipu TPM a **ověřovací klíč**, který byste měli později zkopírovat, až vytvoříte individuální zápis pro vaše zařízení: Toto okno můžete zavřít (s ID registrace a ověřovacím klíčem), ale ponechat okno simulátoru čipu TPM spuštěné.

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Načtení informací o zřizování z fyzického zařízení

Na zařízení vytvořte nástroj, který můžete použít k načtení informací o zřizování zařízení.

1. Postupujte podle kroků v [části Nastavení vývojového prostředí Linuxu](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) k instalaci a sestavení sady Azure IoT device SDK pro C.

1. Spusťte následující příkazy k vytvoření nástroje SDK, který načte informace o zřizování zařízení ze zařízení TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Zkopírujte hodnoty pro **ID registrace** a **ověřovací klíč**. Tyto hodnoty slouží k vytvoření individuální registrace pro vaše zařízení v DPS.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Nastavení služby zřizování zařízení služby IoT Hub

Vytvořte novou instanci služby Zřizování zařízení služby IoT Hub v Azure a propojte ji s vaším centrem IoT hub. Podle pokynů můžete [postupovat v části Nastavení DPS centra IoT](../iot-dps/quick-setup-auto-provision.md)Hub .

Po spuštění služby Device Provisioning Service zkopírujte hodnotu **oboru ID** ze stránky s přehledem. Tuto hodnotu použijete při konfiguraci běhu IoT Edge.

## <a name="create-a-dps-enrollment"></a>Vytvoření registrace DPS

Načtěte informace o zřizování z vašeho virtuálního počítače a použijte je k vytvoření individuální registrace ve službě Device Provisioning Service.

Při vytváření registrace v DPS máte možnost deklarovat **počáteční stav dvojčete zařízení**. V dvojčeti zařízení můžete nastavit značky pro seskupení zařízení podle libovolné metriky, kterou potřebujete ve vašem řešení, jako je oblast, prostředí, umístění nebo typ zařízení. Tyto značky se používají k vytvoření [automatických nasazení](how-to-deploy-monitor.md).

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na instanci služby Zřizování zařízení služby IoT Hub.

2. V části **Nastavení**vyberte **Spravovat registrace**.

3. Vyberte **Přidat individuální zápis** a proveďte následující kroky konfigurace registrace:  

   1. V **případě mechanismu**vyberte možnost **TPM**.

   2. Zadejte **klíč potvrzení** a **ID registrace,** které jste zkopírovali z virtuálního počítače.

      > [!TIP]
      > Pokud používáte fyzické zařízení TPM, musíte určit **klíč potvrzení**, který je jedinečný pro každý čip Čip TPM a je získán od výrobce čipu TPM, který je k němu přidružen. Jedinečné **ID registrace** pro zařízení TPM můžete odvodit například vytvořením hash SHA-256 ověřovacího klíče.

   3. Vyberte **True,** chcete-li deklarovat, že tento virtuální počítač je zařízení IoT Edge.

   4. Vyberte propojené **služby IoT Hub,** ke kterému chcete zařízení připojit. Můžete zvolit více rozbočovačů a zařízení bude přiřazeno jednomu z nich podle vybraných zásad přidělení.

   5. Pokud chcete, zadejte id zařízení. ID zařízení můžete použít k cílení na jednotlivé zařízení pro nasazení modulu. Pokud ID zařízení nezadáte, použije se ID registrace.

   6. Pokud chcete, přidejte hodnotu značky do **počátečního stavu dvojčete zařízení.** Značky můžete použít k cílení skupin zařízení pro nasazení modulu. Například:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   7. Vyberte **Uložit**.

Teď, když pro toto zařízení existuje registrace, může runtime IoT Edge automaticky zřídit zařízení během instalace.

## <a name="install-the-iot-edge-runtime"></a>Instalace runtime IoT Edge

Modul runtime IoT Edge se nasadí na všechna zařízení IoT Edge. Jeho součásti spustit v kontejnerech a umožňují nasadit další kontejnery do zařízení, takže můžete spustit kód na okraji. Nainstalujte runtime IoT Edge do virtuálního počítače.

Před zahájením článku, který odpovídá typu vašeho zařízení, znáte **obor DPS ID** a **ID registrace** zařízení. Pokud jste nainstalovali příklad serveru Ubuntu, použijte pokyny **x64.** Ujistěte se, že konfigurace ioT Edge runtime pro automatické, ne ruční zřizování.

[Instalace runtime Azure IoT Edge na Linux](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>Poskytnutí přístupu k čipu TPM ioT Edge

Aby runtime IoT Edge automaticky zřizovat vaše zařízení, potřebuje přístup k čipu TPM.

Čip TPM můžete udělit přístup k runtime IoT Edge přepsáním nastavení systemd tak, aby služba **iotedge** má oprávnění root. Pokud nechcete zvýšit oprávnění služby, můžete také použít následující kroky k ručnímu poskytnutí přístupu čipu TPM.

1. Najděte cestu k hardwarovému modulu TPM v zařízení a uložte ji jako místní proměnnou.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Vytvořte nové pravidlo, které umožní runtime přístup ioT Edge k tpm0.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Otevřete soubor pravidel.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Zkopírujte následující informace o přístupu do souboru pravidel.

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", GROUP="iotedge", MODE="0660"
   ```

5. Uložte a ukončete soubor.

6. Aktivujte systém udev k vyhodnocení nového pravidla.

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Ověřte, zda bylo pravidlo úspěšně použito.

   ```bash
   ls -l /dev/tpm0
   ```

   Úspěšný výstup vypadá takto:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Pokud nevidíte, že byla použita správná oprávnění, zkuste restartovat počítač a aktualizovat udev.

## <a name="restart-the-iot-edge-runtime"></a>Restartujte runtime IoT Edge

Restartujte runtime IoT Edge tak, aby zachytil všechny změny konfigurace, které jste provedli v zařízení.

   ```bash
   sudo systemctl restart iotedge
   ```

Zkontrolujte, zda běží runtime IoT Edge.

   ```bash
   sudo systemctl status iotedge
   ```

Pokud se zobrazí chyby zřizování, může se zobrazit, že změny konfigurace se ještě neprojevily. Zkuste znovu restartovat daemon IoT Edge.

   ```bash
   sudo systemctl daemon-reload
   ```

Nebo zkuste restartovat virtuální počítač a zjistěte, jestli se změny projeví při novém začátku.

## <a name="verify-successful-installation"></a>Ověření úspěšné instalace

Pokud se runtime úspěšně spustil, můžete přejít do svého ioT hubu a zjistit, že se vaše nové zařízení automaticky zřídí. Teď je vaše zařízení připravené ke spuštění modulů IoT Edge.

Zkontrolujte stav daemonu IoT Edge.

```cmd/sh
systemctl status iotedge
```

Zkontrolujte protokoly daemonů.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Seznam spuštěných modulů.

```cmd/sh
iotedge list
```

Můžete ověřit, že byla použita individuální registrace, kterou jste vytvořili ve službě Device Provisioning Service. Přejděte na instanci služby zřizování zařízení na webu Azure Portal. Otevřete podrobnosti o registraci pro jednotlivé registrace, které jste vytvořili. Všimněte si, že je **přiřazen** stav registrace a id zařízení je uveden.

## <a name="next-steps"></a>Další kroky

Proces registrace služby Device Provisioning Service umožňuje nastavit ID zařízení a značky dvojčete zařízení současně s zřízením nového zařízení. Tyto hodnoty můžete použít k cílení na jednotlivá zařízení nebo skupiny zařízení pomocí automatické správy zařízení. Zjistěte, jak [nasadit a monitorovat moduly IoT Edge ve velkém měřítku pomocí portálu Azure](how-to-deploy-monitor.md) nebo [pomocí rozhraní příkazového příkazového příkazu Azure](how-to-deploy-monitor-cli.md).
