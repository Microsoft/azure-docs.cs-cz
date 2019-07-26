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
ms.custom: seodec18
ms.openlocfilehash: b48455b6ea9c1cd74e94c10d8f9f938c20512c02
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414570"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Vytvoření a zřízení zařízení IoT Edge s virtuálním čipem TPM na virtuálním počítači se systémem Linux

Zařízení Azure IoT Edge se dají automaticky zřídit pomocí [služby Device](../iot-dps/index.yml)Provisioning. Pokud neznáte proces autoprovisioning, přečtěte si [autoprovisioning koncepty](../iot-dps/concepts-auto-provisioning.md) než budete pokračovat. 

V tomto článku se dozvíte, jak otestovat autozřizování na simulovaném IoT Edge zařízení pomocí následujících kroků: 

* Vytvoření virtuálního počítače (VM) s Linuxem v technologii Hyper-V s Simulovaná Trusted Platform Module (TPM) pro zabezpečení hardwaru.
* Vytvoření instance z IoT Hubu zařízení zřizování služby (DPS).
* Vytvořit jednotlivou registraci pro zařízení
* Nainstalujte modul runtime IoT Edge a připojení zařízení k centru IoT

Kroky v tomto článku jsou určená pro účely testování.

## <a name="prerequisites"></a>Požadavky

* Vývojový počítač s Windows s [Hyper-V povolené](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). Tento článek používá Windows 10, spuštění virtuálního počítače s Ubuntu Server. 
* Aktivním centrem IoT. 

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Vytvoření virtuálního počítače s Linuxem pomocí virtuální čip TPM

V této části vytvoříte nový virtuální počítač se systémem Linux v systému Hyper-V. Tento virtuální počítač jste nakonfigurovali simulovaným čipem TPM, abyste ho mohli použít k testování, jak Automatické zřizování spolupracuje s IoT Edge. 

### <a name="create-a-virtual-switch"></a>Vytvoření virtuálního přepínače

Virtuální přepínač umožňuje ve virtuálním počítači pro připojení k fyzické síti.

1. Na počítači s Windows otevřete Správce technologie Hyper-V. 

2. V **akce** nabídce vyberte možnost **Správce virtuálních přepínačů**. 

3. Zvolte **externí** virtuální přepínače a potom vyberte **vytvořit virtuální přepínač**. 

4. Pojmenujte nový virtuální přepínač, například **EdgeSwitch**. Ujistěte se, že typ připojení je nastaven na **externí síť**a pak vyberte **Ok**.

5. Automaticky otevírané okno vás upozorní, že může přerušit připojení k síti. Vyberte **Ano** pokračujte. 

Pokud se zobrazí chyby při vytváření nového virtuálního přepínače, ujistěte se, žádné přepínače používáte adaptér sítě ethernet a že žádné další přepínače použijte stejný název. 

### <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

1. Stažení souboru bitové kopie disku pro virtuální počítač a uloží do místního prostředí. Například [Ubuntu server](https://www.ubuntu.com/download/server). 

2. Ve Správci technologie Hyper-V znovu vyberte **Nový** > **virtuální počítač** v nabídce **Akce** .

3. Dokončení **Průvodce novým virtuálním počítačem** s následující konkrétní konfigurace:

   1. **Zadat generaci**: Vyberte **generaci 2**. Virtuální počítače generace 2 mají povolenou vnořenou virtualizaci, která je nutná ke spuštění IoT Edge na virtuálním počítači.
   2. **Konfigurace sítě**: Nastavte hodnotu **připojení** k virtuálnímu přepínači, který jste vytvořili v předchozí části. 
   3. **Možnosti instalace**: Vyberte možnost **instalovat operační systém ze souboru spouštěcí bitové kopie** a přejděte k souboru bitové kopie disku, který jste uložili místně.

4. V průvodci vyberte **Dokončit** a vytvořte virtuální počítač.

Může trvat několik minut pro vytvoření nového virtuálního počítače. 

### <a name="enable-virtual-tpm"></a>Povolit virtuální čip TPM

Po vytvoření virtuálního počítače otevřete jeho nastavení, abyste povolili čip TPM (Virtual Trusted Platform Module), který vám umožní zařízení povolit. 

1. Vyberte virtuální počítač a pak otevřete jeho **Nastavení**.

2. Přejděte do **zabezpečení**. 

3. Zrušte zaškrtnutí políčka **povolení zabezpečeného spouštění**.

4. Zkontrolujte **povolit Trusted Platform Module**. 

5. Klikněte na **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Spustit virtuální počítač a shromažďovat TPM data

Ve virtuálním počítači sestavení SDK pro jazyk C nástroj, který můžete použít k načtení zařízení **ID registrace** a **ověřovací klíč**. 

1. Spusťte virtuální počítač a připojte se k němu.

2. Podle pokynů v rámci virtuálního počítače dokončete proces instalace a restartujte počítač. 

3. Přihlaste se k VIRTUÁLNÍmu počítači a pak postupujte podle kroků uvedených v části [Nastavení vývojového prostředí pro Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) pro instalaci a sestavení sady SDK pro zařízení Azure IoT pro jazyk C. 

   >[!TIP]
   >V průběhu tohoto článku budete kopírovat a vkládat z virtuálního počítače, který není snadný prostřednictvím aplikace připojení správce technologie Hyper-V. K virtuálnímu počítači se můžete připojit pomocí Správce technologie Hyper-V, když načtete jeho IP adresu `ifconfig`:. Pak můžete použít IP adresu pro připojení prostřednictvím SSH: `ssh <username>@<ipaddress>`.

4. Spusťte následující příkazy k sestavení SDK pro jazyk C nástroj, který načítá informace o zřizování vašeho zařízení. 

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```
   >[!TIP]
   >Pokud testujete pomocí simulátoru TPM, budete muset před povolením vložit další parametr `-Duse_tpm_simulator:BOOL=ON` . Úplný příkaz bude `cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..`.

5. Zkopírujte hodnoty **ID registrace** a **ověřovací klíč**. Tyto hodnoty použijete k vytvoření jednotlivé registrace pro zařízení do služby Device Provisioning. 

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Nastavte si IoT Hub Device Provisioning Service

Vytvořit novou instanci IoT Hub Device Provisioning Service v Azure a propojit jej do služby IoT hub. Můžete podle pokynů v [nastavení IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).

Jakmile budete mít spuštěné služby Device Provisioning Service, zkopírujte hodnotu **rozsah ID** na stránce Přehled. Tuto hodnotu použijete při konfiguraci modulu runtime IoT Edge. 

## <a name="create-a-dps-enrollment"></a>Vytvoření registrace distribučních bodů

Načíst informace o zajištění z vašeho virtuálního počítače a použít k vytvoření jednotlivé registrace do služby Device Provisioning. 

Až vytvořit registraci ve službě Device Provisioning, budete mít příležitost k deklaraci **počáteční stav Dvojčete zařízení**. Ve dvojčeti zařízení můžete nastavit značky k seskupení zařízení podle libovolné metriky, které potřebujete ve vašem řešení, jako je oblast, prostředí, umístění nebo zařízení typu. Tyto značky se používají k vytváření [automatické nasazení](how-to-deploy-monitor.md). 

1. V [Azure Portal](https://portal.azure.com)přejděte do vaší instance IoT Hub Device Provisioning Service. 

2. V části **nastavení**vyberte **Správa registrací**. 

3. Vyberte **přidat jednotlivou registraci** pak dokončete následující postup pro konfiguraci registrace:  

   1. Pro **mechanismus**vyberte **TPM**. 
   
   2. Zadejte **ověřovací klíč** a **ID registrace** , které jste zkopírovali z virtuálního počítače.
   
   3. Výběrem **hodnoty true** deklarujete, že tento virtuální počítač je IoT Edge zařízení. 
   
   4. Zvolte propojený **služby IoT Hub** , že chcete připojení k zařízení. Můžete zvolit více rozbočovačů a zařízení bude přiřazeno k jednomu z nich podle vybrané zásady přidělování. 
   
   5. Pokud chcete, zadejte ID pro vaše zařízení. ID zařízení můžete cílit na jednotlivá zařízení pro nasazení modulu. Pokud ID zařízení nezadáte, použije se ID registrace.
   
   6. Přidat hodnotu značky k **počáteční stav Dvojčete zařízení** Pokud byste o ni. Značky na cílové skupiny zařízení můžete použít pro nasazení modulu. Příklad: 

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

Vědět, službě Device Provisioning **rozsah ID** a zařízení **ID registrace** před zahájením článek, který odpovídá typu vašeho zařízení. Pokud jste nainstalovali server se systémem Ubuntu v příkladu, použijte **x64** pokyny. Ujistěte se, že konfigurace modulu runtime IoT Edge není ruční, automatické zřizování. 

[Instalace modulu runtime Azure IoT Edge v systému Linux](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>Poskytnout přístup IoT Edge do čipu TPM

Aby modul runtime IoT Edge k automatickému zřízení zařízení potřebuje přístup do čipu TPM. 

Můžete poskytnout přístup TPM na modul runtime IoT Edge tak, že přepíšete systemd nastavení tak, aby **iotedge** služby má oprávnění root. Pokud nechcete, aby ke zvýšení oprávnění služby, můžete použít následující kroky také ručně poskytnout přístup čipu TPM. 

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

8. Otevřete soubor přepíše modul runtime IoT Edge. 

   ```bash
   sudo systemctl edit iotedge.service
   ```

9. Přidejte následující kód k vytvoření proměnné prostředí čipu TPM.

   ```input
   [Service]
   Environment=IOTEDGE_USE_TPM_DEVICE=ON
   ```

10. Uložte a zavřete soubor.

11. Ověřte, že toto přepsání bylo úspěšné.

    ```bash
    sudo systemctl cat iotedge.service
    ```

    Úspěšný výstup zobrazuje **iotedge** výchozí služby proměnné a poté zobrazí proměnné prostředí, že jste nastavili **override.conf**. 

12. Znovu načte nastavení.

    ```bash
    sudo systemctl daemon-reload
    ```

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

Můžete ověřit, že se použil jednotlivý zápis, který jste vytvořili v rámci služby Device Provisioning. V Azure Portal přejděte na instanci služby Device Provisioning. Otevřete podrobnosti registrace pro jednotlivou registraci, kterou jste vytvořili. Všimněte si, že je přiřazený stav  registrace a že je uvedené ID zařízení. 

## <a name="next-steps"></a>Další postup

Proces registrace služby Device Provisioning umožňuje nastavit ID zařízení a značky dvojčat zařízení ve stejnou dobu, jak zřídit nové zařízení. Tyto hodnoty můžete cílit na jednotlivá zařízení nebo skupin pomocí automatické správy zařízení. Zjistěte, jak [nasazení a monitorování modulů při škálování na portálu Azure IoT Edge](how-to-deploy-monitor.md) nebo [pomocí Azure CLI](how-to-deploy-monitor-cli.md).
