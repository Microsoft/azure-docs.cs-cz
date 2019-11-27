---
title: Automatické zřizování zařízení se systémem Linux pomocí DPS-Azure IoT Edge | Microsoft Docs
description: Použít čip TPM simulované na virtuální počítač s Linuxem k otestování služby Azure Device Provisioning Service pro Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 292ae570d4f2ddd0c09e667860ee2ba01b9fc6b8
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457167"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Vytvoření a zřízení zařízení IoT Edge s virtuálním čipem TPM na virtuálním počítači se systémem Linux

Zařízení Azure IoT Edge se dají automaticky zřídit pomocí [služby Device Provisioning](../iot-dps/index.yml). Pokud nejste obeznámeni s procesem automatického zřizování, před pokračováním zkontrolujte [Koncepty automatického zřizování](../iot-dps/concepts-auto-provisioning.md) .

V tomto článku se dozvíte, jak otestovat Automatické zřizování na simulovaném IoT Edge zařízení pomocí následujících kroků:

* Vytvoření virtuálního počítače (VM) s Linuxem v technologii Hyper-V s Simulovaná Trusted Platform Module (TPM) pro zabezpečení hardwaru.
* Vytvoření instance z IoT Hubu zařízení zřizování služby (DPS).
* Vytvořit jednotlivou registraci pro zařízení
* Nainstalujte modul runtime IoT Edge a připojení zařízení k centru IoT

> [!NOTE]
> ČIP TPM 2,0 se vyžaduje při použití ověření identity pomocí čipu TPM s DPS a dá se použít jenom k vytvoření individuálních, neskupinových a registrací certifikátů.

> [!TIP]
> Tento článek popisuje, jak otestovat zřizování DPS pomocí simulátoru TPM, ale většina z nich se vztahuje na fyzický hardware TPM, jako je [INFINEON OPTIGA&trade; TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), zařízení Azure Certified for IoT.
>
> Pokud používáte fyzické zařízení, můžete přeskočit k [informacím o načtení zřizování z fyzického zařízení](#retrieve-provisioning-information-from-a-physical-device) v tomto článku.

## <a name="prerequisites"></a>Požadavky

* Vývojový počítač s Windows, který má [povolenou technologii Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). Tento článek používá Windows 10, spuštění virtuálního počítače s Ubuntu Server.
* Aktivním centrem IoT.
* Pokud používáte simulovaný čip TPM, [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 nebo novější s povoleným pracovním vytížením pro [desktopový vývoj C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) .

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Vytvoření virtuálního počítače s Linuxem pomocí virtuální čip TPM

V této části vytvoříte nový virtuální počítač se systémem Linux v systému Hyper-V. Tento virtuální počítač jste nakonfigurovali simulovaným čipem TPM, abyste ho mohli použít k testování, jak Automatické zřizování spolupracuje s IoT Edge. 

### <a name="create-a-virtual-switch"></a>Vytvoření virtuálního přepínače

Virtuální přepínač umožňuje ve virtuálním počítači pro připojení k fyzické síti.

1. Na počítači s Windows otevřete Správce technologie Hyper-V. 

2. V nabídce **Akce** vyberte **Správce virtuálního přepínače**. 

3. Zvolte **externí** virtuální přepínač a pak vyberte **vytvořit virtuální přepínač**. 

4. Dejte novému virtuálnímu přepínači název, například **EdgeSwitch**. Ujistěte se, že typ připojení je nastavený na **externí síť**, a pak vyberte **OK**.

5. Automaticky otevírané okno vás upozorní, že může přerušit připojení k síti. Pokračujte výběrem **Ano** . 

Pokud se zobrazí chyby při vytváření nového virtuálního přepínače, ujistěte se, žádné přepínače používáte adaptér sítě ethernet a že žádné další přepínače použijte stejný název. 

### <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

1. Stažení souboru bitové kopie disku pro virtuální počítač a uloží do místního prostředí. Například [Server Ubuntu](https://www.ubuntu.com/download/server). 

2. Ve Správci technologie Hyper-V znovu vyberte **nový** > **virtuální počítač** v nabídce **Akce** .

3. Dokončete **Průvodce novým virtuálním počítačem** s následujícími konkrétními konfiguracemi:

   1. **Zadejte generaci**: vyberte **generaci 2**. Virtuální počítače generace 2 mají povolenou vnořenou virtualizaci, která je nutná ke spuštění IoT Edge na virtuálním počítači.
   2. **Konfigurace sítě**: nastavte hodnotu **připojení** k virtuálnímu přepínači, který jste vytvořili v předchozí části. 
   3. **Možnosti instalace**: vyberte možnost **instalovat operační systém ze souboru spouštěcí bitové kopie** a vyhledejte soubor bitové kopie disku, který jste uložili místně.

4. V průvodci vyberte **Dokončit** a vytvořte virtuální počítač.

Může trvat několik minut pro vytvoření nového virtuálního počítače. 

### <a name="enable-virtual-tpm"></a>Povolit virtuální čip TPM

Po vytvoření virtuálního počítače otevřete jeho nastavení, abyste povolili čip TPM (Virtual Trusted Platform Module), který umožňuje automatické zřízení zařízení.

1. Vyberte virtuální počítač a pak otevřete jeho **Nastavení**.

2. Přejděte na **zabezpečení**. 

3. Zrušte kontrolu **povolení zabezpečeného spouštění**.

4. Zaškrtněte **Povolit čip Trusted Platform Module**. 

5. Klikněte na tlačítko **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Spustit virtuální počítač a shromažďovat TPM data

Na virtuálním počítači Sestavte nástroj, který můžete použít k načtení **ID registrace** a **ověřovacího klíče**zařízení.

1. Spusťte virtuální počítač a připojte se k němu.

1. Podle pokynů v rámci virtuálního počítače dokončete proces instalace a restartujte počítač.

1. Přihlaste se k VIRTUÁLNÍmu počítači a pak postupujte podle kroků uvedených v části [Nastavení vývojového prostředí pro Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) pro instalaci a sestavení sady SDK pro zařízení Azure IoT pro jazyk C.

   >[!TIP]
   >V průběhu tohoto článku budete kopírovat a vkládat z virtuálního počítače, který není snadný prostřednictvím aplikace připojení správce technologie Hyper-V. Když se chcete připojit k virtuálnímu počítači přes Správce technologie Hyper-V, můžete se hned pokusit získat jeho IP adresu: `ifconfig`. Pak můžete použít IP adresu pro připojení prostřednictvím SSH: `ssh <username>@<ipaddress>`.

1. Spuštěním následujících příkazů Sestavte nástroj SDK, který načte informace o zřizování zařízení z simulátoru TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Z příkazového okna přejděte do adresáře `azure-iot-sdk-c` a spusťte simulátor TPM. Ten naslouchá přes soket na portech 2321 a 2322. Toto příkazové okno nezavírejte; je nutné, aby byl tento simulátor spuštěný.

   V adresáři `azure-iot-sdk-c` spusťte následující příkaz, který spustí simulátor:

   ```bash
   ./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe
   ```

1. Pomocí sady Visual Studio otevřete řešení vygenerované v adresáři `cmake` s názvem `azure_iot_sdks.sln`a sestavte jej pomocí příkazu **Sestavit řešení** v nabídce **sestavení** .

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

1. Zkopírujte hodnoty pro **ID registrace** a **ověřovací klíč**. Tyto hodnoty použijete k vytvoření jednotlivé registrace pro zařízení do služby Device Provisioning.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Nastavte si IoT Hub Device Provisioning Service

Vytvořit novou instanci IoT Hub Device Provisioning Service v Azure a propojit jej do služby IoT hub. Můžete postupovat podle pokynů v tématu [nastavení IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).

Po spuštění služby Device Provisioning zkopírujte na stránce Přehled hodnotu **Rozsah ID** . Tuto hodnotu použijete při konfiguraci modulu runtime IoT Edge. 

## <a name="create-a-dps-enrollment"></a>Vytvoření registrace distribučních bodů

Načíst informace o zajištění z vašeho virtuálního počítače a použít k vytvoření jednotlivé registrace do služby Device Provisioning. 

Když vytvoříte registraci v DPS, budete mít možnost deklarovat **počáteční stav**dopředných zařízení. Ve dvojčeti zařízení můžete nastavit značky k seskupení zařízení podle libovolné metriky, které potřebujete ve vašem řešení, jako je oblast, prostředí, umístění nebo zařízení typu. Tyto značky slouží k vytváření [automatických nasazení](how-to-deploy-monitor.md). 

1. V [Azure Portal](https://portal.azure.com)přejděte do vaší instance IoT Hub Device Provisioning Service. 

2. V části **Nastavení**vyberte **spravovat registrace**. 

3. Vyberte **přidat jednotlivou registraci** a potom proveďte následující kroky, abyste nakonfigurovali registraci:  

   1. V případě **mechanismu**vyberte **TPM**. 

   2. Zadejte **ověřovací klíč** a **ID registrace** , které jste zkopírovali z virtuálního počítače.

      > [!TIP]
      > Pokud používáte fyzické zařízení TPM, je nutné určit **ověřovací klíč**, který je jedinečný pro každý čip TPM a získá se od výrobce čipu TPM, který je k němu přidružený. Jedinečné **ID registrace** pro vaše zařízení TPM můžete odvodit, například vytvořením hodnoty hash SHA-256 ověřovacího klíče.

   3. Výběrem **hodnoty true** deklarujete, že tento virtuální počítač je IoT Edge zařízení. 

   4. Vyberte propojené **IoT Hub** , ke kterým chcete zařízení připojit. Můžete zvolit více rozbočovačů a zařízení bude přiřazeno k jednomu z nich podle vybrané zásady přidělování. 

   5. Pokud chcete, zadejte ID pro vaše zařízení. ID zařízení můžete cílit na jednotlivá zařízení pro nasazení modulu. Pokud ID zařízení nezadáte, použije se ID registrace.

   6. Pokud chcete, přidejte hodnotu značky do **počátečního stavového vlákna zařízení** . Značky na cílové skupiny zařízení můžete použít pro nasazení modulu. Příklad: 

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

## <a name="install-the-iot-edge-runtime"></a>Nainstalovat modul runtime IoT Edge

Modul runtime IoT Edge se nasadí na všechna zařízení IoT Edge. Jeho součástí spouštění v kontejnerech a můžete nasadit další kontejnery do zařízení tak, aby kód můžete spustit na hraničních zařízeních. Nainstalujte modul runtime IoT Edge ve vašem virtuálním počítači. 

Před zahájením článku, který odpovídá vašemu typu zařízení, si poznáte **Rozsah ID** DPS a **registraci** zařízení. Pokud jste nainstalovali vzorový Server Ubuntu, postupujte podle pokynů pro **platformu x64** . Ujistěte se, že konfigurace modulu runtime IoT Edge není ruční, automatické zřizování. 

[Instalace modulu runtime Azure IoT Edge v systému Linux](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>Poskytnout přístup IoT Edge do čipu TPM

Aby modul runtime IoT Edge k automatickému zřízení zařízení potřebuje přístup do čipu TPM. 

Přístup čipu TPM k modulu runtime IoT Edge můžete poskytnout přepsáním nastavení systému tak, aby služba **iotedge** měla kořenová oprávnění. Pokud nechcete, aby ke zvýšení oprávnění služby, můžete použít následující kroky také ručně poskytnout přístup čipu TPM. 

1. Najít cestu k modulu hardwarového TPM na vašem zařízení a uložte ho jako lokální proměnné. 

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Vytvořte nové pravidlo, které vám poskytne IoT Edge, modul runtime přístup k tpm0. 

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Otevřete soubor pravidel. 

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Zkopírujte následující informace o přístup do souboru pravidel. 

   ```input 
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", GROUP="iotedge", MODE="0660"
   ```

5. Uložte a zavřete soubor. 

6. Aktivace procesu udev systému k vyhodnocení nové pravidlo. 

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Ověřte, že bylo úspěšně aplikováno pravidlo.

   ```bash
   ls -l /dev/tpm0
   ```

   Úspěšný výstup by měl vypadat nějak takto:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Pokud se nezobrazí, zda byla použita správná oprávnění, zkuste restartovat systém aktualizovat udev. 

## <a name="restart-the-iot-edge-runtime"></a>Restartujte modul runtime IoT Edge

Restartujte modul runtime IoT Edge, aby použila všechny změny konfigurace, které jste provedli v zařízení. 

   ```bash
   sudo systemctl restart iotedge
   ```

Zkontrolujte, zda je spuštěn modul runtime IoT Edge. 

   ```bash
   sudo systemctl status iotedge
   ```

Pokud vidíte chyby zřizování, může být, že změny konfigurace ještě nevstoupilo v platnost. Restartujte démona IoT Edge znovu. 

   ```bash
   sudo systemctl daemon-reload
   ```
   
Nebo, zkuste restartovat virtuální počítač zobrazíte, pokud se změny projeví na začít znovu. 

## <a name="verify-successful-installation"></a>Ověření úspěšné instalace

Pokud modul runtime byl úspěšně spuštěn, můžete přejít do služby IoT Hub a najdete v článku, že byl automaticky zřídit nové zařízení. Vaše zařízení je nyní připraven ke spuštění moduly IoT Edge. 

Kontrola stavu démona IoT Edge.

```cmd/sh
systemctl status iotedge
```

Zkontrolujte démon protokoly.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Seznam s moduly.

```cmd/sh
iotedge list
```

Můžete ověřit, že se použil jednotlivý zápis, který jste vytvořili v rámci služby Device Provisioning. V Azure Portal přejděte na instanci služby Device Provisioning. Otevřete podrobnosti registrace pro jednotlivou registraci, kterou jste vytvořili. Všimněte si, že je **přiřazený** stav registrace a že je uvedené ID zařízení. 

## <a name="next-steps"></a>Další kroky

Proces registrace služby Device Provisioning umožňuje nastavit ID zařízení a značky dvojčat zařízení ve stejnou dobu, jak zřídit nové zařízení. Tyto hodnoty můžete cílit na jednotlivá zařízení nebo skupin pomocí automatické správy zařízení. Naučte se, jak [nasadit a monitorovat IoT Edge moduly ve velkém měřítku pomocí Azure Portal](how-to-deploy-monitor.md) nebo [pomocí Azure CLI](how-to-deploy-monitor-cli.md).
