---
title: Zřízení zařízení s virtuálním čipem TPM na platformě Linux – Azure IoT Edge
description: Použití simulovaného čipu TPM na virtuálním počítači se systémem Linux k otestování služby Azure Device Provisioning pro Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 95dc5b70174cd738104260aac2e175c0657d9c90
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91966198"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-tpm-on-linux"></a>Vytvoření a zřízení zařízení IoT Edge s čipem TPM v systému Linux

Tento článek ukazuje, jak otestovat Automatické zřizování na zařízení se systémem Linux IoT Edge pomocí čipu TPM (Trusted Platform Module). Zařízení Azure IoT Edge se [službou Device Provisioning](../iot-dps/index.yml)můžete automaticky zřizovat. Pokud neznáte proces automatického zřizování, přečtěte si přehled [zřizování](../iot-dps/about-iot-dps.md#provisioning-process) a teprve potom pokračujte.

Úkoly jsou následující:

1. Vytvořte virtuální počítač se systémem Linux v technologii Hyper-V s simulovaným čipem TPM (Trusted Platform Module) pro hardwarové zabezpečení.
1. Vytvořte instanci IoT Hub Device Provisioning Service (DPS).
1. Vytvořte jednotlivou registraci zařízení.
1. Nainstalujte modul runtime IoT Edge a připojte zařízení k IoT Hub.

> [!TIP]
> Tento článek popisuje, jak otestovat zřizování DPS pomocí simulátoru TPM, ale většina z nich se vztahuje na fyzický hardware TPM, jako je například [ &trade; čip TPM Infineon OPTIGA](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), zařízení Azure Certified for IoT.
>
> Pokud používáte fyzické zařízení, můžete přeskočit k [informacím o načtení zřizování z fyzického zařízení](#retrieve-provisioning-information-from-a-physical-device) v tomto článku.

## <a name="prerequisites"></a>Předpoklady

* Vývojový počítač s Windows, který má [povolenou technologii Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). Tento článek používá Windows 10, na kterém běží virtuální počítač s Ubuntu serverem.
* Aktivní IoT Hub.

> [!NOTE]
> ČIP TPM 2,0 se vyžaduje při použití ověření identity pomocí čipu TPM s DPS a dá se použít jenom k vytvoření individuálních, neskupinových a registrací certifikátů.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Vytvoření virtuálního počítače se systémem Linux s virtuálním čipem TPM

V této části vytvoříte nový virtuální počítač se systémem Linux v systému Hyper-V. Tento virtuální počítač nakonfigurujete simulovaným čipem TPM pro testování, jak Automatické zřizování funguje s IoT Edge.

### <a name="create-a-virtual-switch"></a>Vytvoření virtuálního přepínače

Virtuální přepínač umožňuje virtuálnímu počítači připojit se k fyzické síti.

1. Na počítači s Windows otevřete Správce technologie Hyper-V.

2. V nabídce **Akce** vyberte **Správce virtuálního přepínače**.

3. Zvolte **externí** virtuální přepínač a pak vyberte **vytvořit virtuální přepínač**.

4. Dejte novému virtuálnímu přepínači název, například **EdgeSwitch**. Ujistěte se, že typ připojení je nastavený na **externí síť**, a pak vyberte **OK**.

5. Automaticky otevírané okno vás upozorní, že může dojít k přerušení připojení k síti. Vyberte **Ano**, abyste mohli pokračovat.

Pokud se při vytváření nového virtuálního přepínače zobrazí chyby, zajistěte, aby žádné jiné přepínače nepoužívaly adaptér sítě Ethernet a aby žádné jiné přepínače nepoužívaly stejný název.

### <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

1. Stáhněte si soubor image disku, který se má použít pro virtuální počítač, a uložte ho místně. Například [Ubuntu server 18,04](http://releases.ubuntu.com/18.04/). Informace o podporovaných operačních systémech pro zařízení IoT Edge najdete v článku [Azure IoT Edge podporované systémy](support.md).

2. Ve Správci technologie Hyper-V znovu vyberte **Akce**  >  **Nový**  >  **virtuální počítač** v nabídce **Akce** .

3. Dokončete **Průvodce novým virtuálním počítačem** s následujícími konkrétními konfiguracemi:

   1. **Zadejte generaci**: vyberte **generaci 2**. Virtuální počítače generace 2 mají povolenou vnořenou virtualizaci, která je nutná ke spuštění IoT Edge na virtuálním počítači.
   2. **Konfigurace sítě**: nastavte hodnotu **připojení** k virtuálnímu přepínači, který jste vytvořili v předchozí části.
   3. **Možnosti instalace**: vyberte možnost **instalovat operační systém ze souboru spouštěcí bitové kopie** a vyhledejte soubor bitové kopie disku, který jste uložili místně.

4. V průvodci vyberte **Dokončit** a vytvořte virtuální počítač.

Vytvoření nového virtuálního počítače může trvat několik minut.

### <a name="enable-virtual-tpm"></a>Povolit virtuální čip TPM

Po vytvoření virtuálního počítače otevřete jeho nastavení, abyste povolili čip TPM (Virtual Trusted Platform Module), který umožňuje automatické zřízení zařízení.

1. Ve Správci technologie Hyper-V klikněte pravým tlačítkem na virtuální počítač a vyberte **Nastavení**.

2. Přejděte na **zabezpečení**.

3. Zrušte kontrolu **povolení zabezpečeného spouštění**.

4. Zaškrtněte **Povolit čip Trusted Platform Module**.

5. Klikněte na **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Spustit virtuální počítač a shromažďovat data TPM

Na virtuálním počítači Sestavte nástroj, který můžete použít k načtení **ID registrace** a **ověřovacího klíče**zařízení.

1. Ve Správci technologie Hyper-V spusťte virtuální počítač a připojte se k němu.

1. Podle pokynů v rámci virtuálního počítače dokončete proces instalace a restartujte počítač.

1. Přihlaste se k VIRTUÁLNÍmu počítači a pak postupujte podle kroků uvedených v části [Nastavení vývojového prostředí pro Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) pro instalaci a sestavení sady SDK pro zařízení Azure IoT pro jazyk C.

   >[!TIP]
   >V průběhu tohoto článku zkopírujete a vložíte na virtuální počítač, který není snadný prostřednictvím aplikace připojení správce technologie Hyper-V. Pokud chcete získat IP adresu, můžete se k virtuálnímu počítači připojit pomocí Správce technologie Hyper-V. Nejprve spusťte `sudo apt install net-tools` a potom `hostname -I` . Pak můžete použít IP adresu pro připojení prostřednictvím SSH: `ssh <username>@<ipaddress>` .

1. Spuštěním následujících příkazů Sestavte nástroj SDK, který načte informace o zřizování zařízení z čipu TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. V okně výstup se zobrazí **ID registrace** a **ověřovací klíč**zařízení. Zkopírujte tyto hodnoty pro pozdější použití při vytváření jednotlivých zápisů pro vaše zařízení.

Jakmile budete mít registrační kód a ověřovací klíč, pokračujte k části [nastavení IoT Hub Device Provisioning Service](#set-up-the-iot-hub-device-provisioning-service)

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Načíst informace o zřizování z fyzického zařízení

Pokud místo virtuálního počítače používáte fyzické IoT Edge zařízení, vytvořte nástroj, který můžete použít k načtení informací o zřizování zařízení.

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
> V rozhraní příkazového řádku Azure můžete vytvořit [registraci](/cli/azure/ext/azure-iot/iot/dps/enrollment) a pomocí příznaku s **povoleným okrajem** určit, že se jedná o zařízení IoT Edge.

1. V [Azure Portal](https://portal.azure.com)přejděte do vaší instance IoT Hub Device Provisioning Service.

2. V části **Nastavení**vyberte **spravovat registrace**.

3. Vyberte **přidat jednotlivou registraci** a potom proveďte následující kroky, abyste nakonfigurovali registraci:  

   1. V případě **mechanismu**vyberte **TPM**.

   2. Zadejte **ověřovací klíč** a **ID registrace** , které jste zkopírovali z virtuálního počítače.

      > [!TIP]
      > Pokud používáte fyzické zařízení TPM, je nutné určit **ověřovací klíč**, který je jedinečný pro každý čip TPM a získá se od výrobce čipu TPM, který je k němu přidružený. Jedinečné **ID registrace** pro vaše zařízení TPM můžete odvodit, například vytvořením hodnoty hash SHA-256 ověřovacího klíče.

   3. Pokud chcete, zadejte ID zařízení. Pokud ID zařízení nezadáte, použije se ID registrace.

   4. Výběrem **hodnoty true** deklarujete, že tento virtuální počítač je IoT Edge zařízení.

   5. Zvolte propojený IoT Hub, ke kterému chcete zařízení připojit, nebo vyberte **odkaz na nový IoT Hub**. Můžete zvolit více rozbočovačů a zařízení bude přiřazeno k jednomu z nich v závislosti na zvolených zásadách přiřazení.

   6. Pokud chcete, přidejte hodnotu značky do **počátečního stavového vlákna zařízení** . Pomocí značek můžete cílit skupiny zařízení na nasazení modulů. Další informace najdete v tématu věnovaném [nasazení IoT Edgech modulů ve velkém měřítku](how-to-deploy-at-scale.md).

   7. Vyberte **Uložit**.

Teď, když pro toto zařízení existuje registrace, IoT Edge modul runtime může zařízení během instalace automaticky zřídit.

## <a name="install-the-iot-edge-runtime"></a>Instalace modulu runtime IoT Edge

Modul runtime IoT Edge se nasadí na všechna zařízení IoT Edge. Jeho komponenty se spouštějí v kontejnerech a umožňují na zařízení nasadit další kontejnery, abyste mohli spustit kód na hraničních zařízeních. Na virtuální počítač nainstalujte modul runtime IoT Edge.

Postupujte podle kroků v části [Instalace modulu runtime Azure IoT Edge](how-to-install-iot-edge.md)a pak se vraťte k tomuto článku a zřiďte zařízení.

## <a name="configure-the-device-with-provisioning-information"></a>Konfigurace zařízení pomocí informací o zřizování

Po nainstalování modulu runtime do zařízení nakonfigurujte zařízení s informacemi, které používá pro připojení ke službě Device Provisioning a IoT Hub.

1. Zjistěte svůj **Rozsah ID** DPS a **ID registrace** zařízení, které byly shromážděny v předchozích částech.

1. Otevřete konfigurační soubor na zařízení IoT Edge.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. V souboru vyhledejte část konfigurace zřizování. Odkomentujte řádky pro zřizování čipu TPM a zajistěte, aby byly všechny další řádky pro zřizování zakomentovány.

   `provisioning:`Řádek by neměl mít žádné předchozí prázdné znaky a vnořené položky by měly být odsazeny dvěma mezerami.

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "tpm"
       registration_id: "<REGISTRATION_ID>"
   ```

1. Aktualizujte hodnoty `scope_id` a `registration_id` pomocí informací DPS a Device.

## <a name="give-iot-edge-access-to-the-tpm"></a>Udělení IoT Edge k čipu TPM

Modul runtime IoT Edge potřebuje k automatickému zřízení zařízení přístup k čipu TPM.

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
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="iotedge", MODE="0600"
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

   Úspěšný výstup se zobrazí takto:

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

Proces registrace DPS vám umožní nastavit ID zařízení a nedokončené značky zařízení současně, když zřizujete nové zařízení. Tyto hodnoty můžete použít k zaměření jednotlivých zařízení nebo skupin zařízení pomocí automatické správy zařízení. Naučte se, jak [nasadit a monitorovat IoT Edge moduly ve velkém měřítku pomocí Azure Portal](how-to-deploy-at-scale.md) nebo [pomocí Azure CLI](how-to-deploy-cli-at-scale.md).
