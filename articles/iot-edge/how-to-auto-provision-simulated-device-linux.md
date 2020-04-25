---
title: Zřízení zařízení s virtuálním čipem TPM na platformě Linux – Azure IoT Edge
description: Použití simulovaného čipu TPM na virtuálním počítači se systémem Linux k otestování služby Azure Device Provisioning pro Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/2/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 82bdc71a123a263fffd842a04f4837b34aaa8685
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82131065"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Vytvoření a zřízení zařízení IoT Edge s virtuálním čipem TPM na virtuálním počítači se systémem Linux

Zařízení Azure IoT Edge se dají automaticky zřídit pomocí [služby Device Provisioning](../iot-dps/index.yml). Pokud nejste obeznámeni s procesem automatického zřizování, před pokračováním zkontrolujte [Koncepty automatického zřizování](../iot-dps/concepts-auto-provisioning.md) .

V tomto článku se dozvíte, jak otestovat Automatické zřizování na simulovaném IoT Edge zařízení pomocí následujících kroků:

* Vytvořte virtuální počítač se systémem Linux v technologii Hyper-V s simulovaným čipem TPM (Trusted Platform Module) pro hardwarové zabezpečení.
* Vytvořte instanci IoT Hub Device Provisioning Service (DPS).
* Vytvořit jednotlivou registraci pro zařízení
* Instalace modulu runtime IoT Edge a připojení zařízení k IoT Hub

> [!TIP]
> Tento článek popisuje, jak otestovat zřizování DPS pomocí simulátoru TPM, ale většina z nich se vztahuje na fyzický hardware TPM, jako je například [čip&trade; TPM Infineon OPTIGA](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), zařízení Azure Certified for IoT.
>
> Pokud používáte fyzické zařízení, můžete přeskočit k [informacím o načtení zřizování z fyzického zařízení](#retrieve-provisioning-information-from-a-physical-device) v tomto článku.

## <a name="prerequisites"></a>Požadavky

* Vývojový počítač s Windows, který má [povolenou technologii Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). Tento článek používá Windows 10, na kterém běží virtuální počítač s Ubuntu serverem.
* Aktivní IoT Hub.
* Pokud používáte simulovaný čip TPM, [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 nebo novější s povolenou úlohou [vývoj desktopových aplikací v jazyce C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) .

> [!NOTE]
> ČIP TPM 2,0 se vyžaduje při použití ověření identity pomocí čipu TPM s DPS a dá se použít jenom k vytvoření individuálních, neskupinových a registrací certifikátů.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Vytvoření virtuálního počítače se systémem Linux s virtuálním čipem TPM

V této části vytvoříte nový virtuální počítač se systémem Linux v systému Hyper-V. Tento virtuální počítač jste nakonfigurovali simulovaným čipem TPM, abyste ho mohli použít k testování, jak Automatické zřizování spolupracuje s IoT Edge.

### <a name="create-a-virtual-switch"></a>Vytvoření virtuálního přepínače

Virtuální přepínač umožňuje virtuálnímu počítači připojit se k fyzické síti.

1. Na počítači s Windows otevřete Správce technologie Hyper-V.

2. V nabídce **Akce** vyberte **Správce virtuálního přepínače**.

3. Zvolte **externí** virtuální přepínač a pak vyberte **vytvořit virtuální přepínač**.

4. Dejte novému virtuálnímu přepínači název, například **EdgeSwitch**. Ujistěte se, že typ připojení je nastavený na **externí síť**, a pak vyberte **OK**.

5. Automaticky otevírané okno vás upozorní, že může dojít k přerušení připojení k síti. Pokračujte výběrem **Ano** .

Pokud se při vytváření nového virtuálního přepínače zobrazí chyby, zajistěte, aby žádné jiné přepínače nepoužívaly adaptér sítě Ethernet a aby žádné jiné přepínače nepoužívaly stejný název.

### <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

1. Stáhněte si soubor image disku, který se má použít pro virtuální počítač, a uložte ho místně. Například [Server Ubuntu](https://www.ubuntu.com/download/server).

2. Ve Správci technologie Hyper-V znovu vyberte **Nový** > **virtuální počítač** v nabídce **Akce** .

3. Dokončete **Průvodce novým virtuálním počítačem** s následujícími konkrétními konfiguracemi:

   1. **Zadejte generaci**: vyberte **generaci 2**. Virtuální počítače generace 2 mají povolenou vnořenou virtualizaci, která je nutná ke spuštění IoT Edge na virtuálním počítači.
   2. **Konfigurace sítě**: nastavte hodnotu **připojení** k virtuálnímu přepínači, který jste vytvořili v předchozí části.
   3. **Možnosti instalace**: vyberte možnost **instalovat operační systém ze souboru spouštěcí bitové kopie** a vyhledejte soubor bitové kopie disku, který jste uložili místně.

4. V průvodci vyberte **Dokončit** a vytvořte virtuální počítač.

Vytvoření nového virtuálního počítače může trvat několik minut.

### <a name="enable-virtual-tpm"></a>Povolit virtuální čip TPM

Po vytvoření virtuálního počítače otevřete jeho nastavení, abyste povolili čip TPM (Virtual Trusted Platform Module), který umožňuje automatické zřízení zařízení.

1. Vyberte virtuální počítač a pak otevřete jeho **Nastavení**.

2. Přejděte na **zabezpečení**.

3. Zrušte kontrolu **povolení zabezpečeného spouštění**.

4. Zaškrtněte **Povolit čip Trusted Platform Module**.

5. Klikněte na tlačítko **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Spustit virtuální počítač a shromažďovat data TPM

Na virtuálním počítači Sestavte nástroj, který můžete použít k načtení **ID registrace** a **ověřovacího klíče**zařízení.

1. Spusťte virtuální počítač a připojte se k němu.

1. Podle pokynů v rámci virtuálního počítače dokončete proces instalace a restartujte počítač.

1. Přihlaste se k VIRTUÁLNÍmu počítači a pak postupujte podle kroků uvedených v části [Nastavení vývojového prostředí pro Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) pro instalaci a sestavení sady SDK pro zařízení Azure IoT pro jazyk C.

   >[!TIP]
   >V průběhu tohoto článku budete kopírovat a vkládat z virtuálního počítače, který není snadný prostřednictvím aplikace připojení správce technologie Hyper-V. Pokud chcete získat IP adresu, můžete se k virtuálnímu počítači připojit pomocí Správce technologie Hyper-V: `ifconfig`. Pak můžete použít IP adresu pro připojení prostřednictvím SSH: `ssh <username>@<ipaddress>`.

1. Spuštěním následujících příkazů Sestavte nástroj SDK, který načte informace o zřizování zařízení z simulátoru TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Z příkazového okna přejděte do `azure-iot-sdk-c` adresáře a spusťte simulátor TPM. Ten naslouchá přes soket na portech 2321 a 2322. Toto příkazové okno nezavírejte; je nutné, aby byl tento simulátor spuštěný.

   Z `azure-iot-sdk-c` adresáře spusťte následující příkaz, který spustí simulátor:

   ```bash
   ./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe
   ```

1. Pomocí sady Visual Studio `cmake` otevřete řešení vygenerované v adresáři s názvem `azure_iot_sdks.sln`a sestavte ho pomocí příkazu **Sestavit řešení** v nabídce **sestavení** .

1. V podokně **Průzkumník řešení** v sadě Visual Studio přejděte do složky **Provision\_Tools**. Klikněte pravým tlačítkem na projekt **tpm_device_provision** a vyberte **Nastavit jako spouštěný projekt**.

1. Spusťte řešení pomocí některého z příkazů **Start** v nabídce **ladění** . V okně výstup se zobrazí **ID registrace** simulátoru TPM a **ověřovací klíč**, který byste měli zkopírovat pro pozdější použití při vytváření individuální registrace zařízení v nástroji, můžete toto okno zavřít (s ID registrace a ověřovacím klíčem), ale nechat okno simulátoru TPM spuštěné.

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Načíst informace o zřizování z fyzického zařízení

V zařízení vytvořte nástroj, který můžete použít k načtení informací o zřizování zařízení.

1. Podle postupu v části [Nastavení vývojového prostředí pro Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) nainstalujte a Sestavte sadu SDK pro zařízení Azure IoT pro jazyk C.

1. Spuštěním následujících příkazů Sestavte nástroj SDK, který načte informace o zřizování zařízení ze zařízení TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Zkopírujte hodnoty pro **ID registrace** a **ověřovací klíč**. Tyto hodnoty použijete k vytvoření jednotlivé registrace zařízení v DPS.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Nastavení IoT Hub Device Provisioning Service

Vytvořte novou instanci IoT Hub Device Provisioning Service v Azure a propojte ji se službou IoT Hub. Můžete postupovat podle pokynů v tématu [nastavení IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).

Po spuštění služby Device Provisioning zkopírujte na stránce Přehled hodnotu **Rozsah ID** . Tuto hodnotu použijete při konfiguraci modulu runtime IoT Edge.

## <a name="create-a-dps-enrollment"></a>Vytvoření registrace DPS

Načtěte informace o zřizování z virtuálního počítače a pomocí tohoto prostředí vytvořte jednotlivé registrace ve službě Device Provisioning.

Když vytvoříte registraci v DPS, budete mít možnost deklarovat **počáteční stav**dopředných zařízení. V případě zařízení můžete nastavit značky pro seskupení zařízení podle libovolné metriky, kterou potřebujete ve vašem řešení, jako je oblast, prostředí, umístění nebo typ zařízení. Tyto značky slouží k vytváření [automatických nasazení](how-to-deploy-at-scale.md).

> [!TIP]
> V Azure CLI můžete vytvořit [registraci](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment) nebo [skupinu registrace](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment-group) a pomocí příznaku s **povoleným okrajem** určit, že zařízení nebo skupina zařízení je IoT Edge zařízení.

1. V [Azure Portal](https://portal.azure.com)přejděte do vaší instance IoT Hub Device Provisioning Service.

2. V části **Nastavení**vyberte **spravovat registrace**.

3. Vyberte **přidat jednotlivou registraci** a potom proveďte následující kroky, abyste nakonfigurovali registraci:  

   1. V případě **mechanismu**vyberte **TPM**.

   2. Zadejte **ověřovací klíč** a **ID registrace** , které jste zkopírovali z virtuálního počítače.

      > [!TIP]
      > Pokud používáte fyzické zařízení TPM, je nutné určit **ověřovací klíč**, který je jedinečný pro každý čip TPM a získá se od výrobce čipu TPM, který je k němu přidružený. Jedinečné **ID registrace** pro vaše zařízení TPM můžete odvodit, například vytvořením hodnoty hash SHA-256 ověřovacího klíče.

   3. Výběrem **hodnoty true** deklarujete, že tento virtuální počítač je IoT Edge zařízení.

   4. Vyberte propojené **IoT Hub** , ke kterým chcete zařízení připojit. Můžete zvolit více rozbočovačů a zařízení bude přiřazeno k jednomu z nich podle vybrané zásady přidělování.

   5. Pokud chcete, zadejte ID zařízení. Pomocí ID zařízení můžete cílit na jednotlivá zařízení pro nasazení modulů. Pokud ID zařízení nezadáte, použije se ID registrace.

   6. Pokud chcete, přidejte hodnotu značky do **počátečního stavového vlákna zařízení** . Pomocí značek můžete cílit skupiny zařízení na nasazení modulů. Příklad:

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

Teď, když pro toto zařízení existuje registrace, IoT Edge modul runtime může zařízení během instalace automaticky zřídit.

## <a name="install-the-iot-edge-runtime"></a>Instalace modulu runtime IoT Edge

Modul runtime IoT Edge se nasadí na všechna zařízení IoT Edge. Jeho komponenty se spouštějí v kontejnerech a umožňují na zařízení nasadit další kontejnery, abyste mohli spustit kód na hraničních zařízeních. Na virtuální počítač nainstalujte modul runtime IoT Edge.

Před zahájením článku, který odpovídá vašemu typu zařízení, si poznáte **Rozsah ID** DPS a **registraci** zařízení. Pokud jste nainstalovali vzorový Server Ubuntu, postupujte podle pokynů pro **platformu x64** . Nezapomeňte nakonfigurovat IoT Edge runtime pro automatické, ne ruční zřizování.

[Instalace modulu runtime Azure IoT Edge v systému Linux](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>Udělení IoT Edge k čipu TPM

Aby modul runtime IoT Edge automaticky zřídil vaše zařízení, potřebuje přístup k čipu TPM.

Přístup čipu TPM k modulu runtime IoT Edge můžete poskytnout přepsáním nastavení systému tak, aby `iotedge` služba měla kořenová oprávnění. Pokud nechcete oprávnění ke službě zvýšit, můžete k ručnímu poskytnutí přístupu k TPM použít taky následující postup.

1. Vyhledejte v zařízení cestu k hardwarovému modulu TPM a uložte ji jako místní proměnnou.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Vytvořte nové pravidlo, které udělí IoT Edge modulu runtime přístup k tpm0.

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

5. Soubor uložte a zavřete.

6. Aktivujte systém udev pro vyhodnocení nového pravidla.

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Ověřte, že pravidlo bylo úspěšně použito.

   ```bash
   ls -l /dev/tpm0
   ```

   Úspěšný výstup vypadá takto:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Pokud se vám nezobrazují správná oprávnění, zkuste restartovat počítač a obnovte udev.

## <a name="restart-the-iot-edge-runtime"></a>Restartování prostředí IoT Edge runtime

Restartujte modul runtime IoT Edge, aby provedl všechny změny konfigurace, které jste v zařízení provedli.

   ```bash
   sudo systemctl restart iotedge
   ```

Zkontrolujte, zda je spuštěn modul runtime IoT Edge.

   ```bash
   sudo systemctl status iotedge
   ```

Pokud se zobrazí chyby zřizování, může to být tím, že se ještě neprojevily změny konfigurace. Zkuste znovu spustit démona IoT Edge.

   ```bash
   sudo systemctl daemon-reload
   ```

Případně můžete restartovat virtuální počítač a zjistit, jestli se změny projeví při zahájení obnovení.

## <a name="verify-successful-installation"></a>Ověření úspěšné instalace

Pokud se modul runtime úspěšně spustil, můžete přejít do IoT Hub a podívat se, že se vaše nové zařízení automaticky zřídilo. Vaše zařízení je teď připravené spouštět IoT Edge moduly.

Ověřte stav procesu démona IoT Edge.

```cmd/sh
systemctl status iotedge
```

Prověřte protokoly démona.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Vypíše spuštěné moduly.

```cmd/sh
iotedge list
```

Můžete ověřit, že se použil jednotlivý zápis, který jste vytvořili v rámci služby Device Provisioning. V Azure Portal přejděte na instanci služby Device Provisioning. Otevřete podrobnosti registrace pro jednotlivou registraci, kterou jste vytvořili. Všimněte si, že je **přiřazený** stav registrace a že je uvedené ID zařízení.

## <a name="next-steps"></a>Další kroky

Proces registrace služby Device Provisioning umožňuje nastavit ID zařízení a nedokončené značky zařízení současně, když zřizujete nové zařízení. Tyto hodnoty můžete použít k zaměření jednotlivých zařízení nebo skupin zařízení pomocí automatické správy zařízení. Naučte se, jak [nasadit a monitorovat IoT Edge moduly ve velkém měřítku pomocí Azure Portal](how-to-deploy-at-scale.md) nebo [pomocí Azure CLI](how-to-deploy-cli-at-scale.md).
