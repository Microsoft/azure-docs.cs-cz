---
title: Připojení MXCHIP AZ3166 k Azure IoT Central rychlý Start
description: Použijte integrovaný software Azure RTO k připojení zařízení AZ3166 MXCHIP k Azure IoT a posílání telemetrie.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 03/17/2021
ms.openlocfilehash: 160797367e2daf0cb6fe708d626cbf217c9992c8
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448592"
---
# <a name="quickstart-connect-an-mxchip-az3166-devkit-to-iot-central"></a>Rychlý Start: připojení MXCHIP AZ3166 DevKit k IoT Central

**Platí pro**: [vývoj integrovaných zařízení](about-iot-develop.md#embedded-device-development)<br>
**Celkový čas dokončení**: 30 minut

[![Procházet kód](media/common/browse-code.svg)](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)

V tomto kurzu použijete Azure RTO k připojení MXCHIP AZ3166 IoT DevKit (dále MXCHIP DevKit) do Azure IoT. Tento článek je součástí série [Začínáme s vývojem zařízení Azure IoT Embedded](quickstart-device-development.md). Série přináší vývojářům zařízení do Azure RTO a ukazuje, jak propojit několik sad pro vyhodnocení zařízení s Azure IoT.

Provedete následující úkoly:

* Instalace sady integrovaných vývojářských nástrojů pro programování MXCHIP DevKit v jazyce C
* Sestavte image a zablikají ji do MXCHIP DevKit
* Použití Azure IoT Central k vytvoření cloudových komponent, zobrazení vlastností, zobrazení telemetrie zařízení a volání přímých příkazů

## <a name="prerequisites"></a>Požadavky

* POČÍTAČ se systémem Microsoft Windows 10
* [Git](https://git-scm.com/downloads) pro klonování úložiště
* Hardware

    > * [MXCHIP AZ3166 IoT DevKit](https://aka.ms/iot-devkit) (MXCHIP DevKit)
    > * Wi-Fi 2,4 GHz
    > * USB 2,0 A samci a Micro USB samčí kabel

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

K nastavení vývojového prostředí nejdřív naklonujte úložiště GitHub, které obsahuje všechny prostředky, které pro tento kurz potřebujete. Potom nainstalujete sadu programovacích nástrojů.

### <a name="clone-the-repo-for-the-tutorial"></a>Naklonujte úložiště pro kurz.

Naklonujte následující úložiště pro stažení všech ukázkových kódů zařízení, instalačních skriptů a offline verzí dokumentace. Pokud jste toto úložiště předtím naklonováni jiným kurzem, nemusíte to dělat znovu.

K naklonování úložiště spusťte následující příkaz:

```shell
git clone --recursive https://github.com/azure-rtos/getting-started.git
```

### <a name="install-the-tools"></a>Instalace nástrojů

Klonovaný úložiště obsahuje skript instalačního programu, který nainstaluje a nakonfiguruje požadované nástroje. Pokud jste tyto nástroje nainstalovali v jiném kurzu příručky Začínáme, nemusíte to dělat znovu.

> [!NOTE]
> Instalační skript nainstaluje následující nástroje:
> * [Cmake](https://cmake.org): sestavení
> * [ARM RSZ](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm): kompilovat
> * [Termite](https://www.compuphase.com/software_termite.htm): monitorovat výstup sériového portu pro připojená zařízení

Postup při instalaci nástrojů:

1. V Průzkumníku souborů přejděte k následující cestě v úložišti a spusťte instalační skript s názvem *get-toolchain.bat*:

    > *getting-started\tools\get-toolchain.bat*

1. Po instalaci otevřete nové okno konzoly pro rozpoznávání změn konfigurace provedených instalačním skriptem. Tuto konzolu použijte k dokončení zbývajících programovacích úloh v tomto kurzu. Pro Windows můžete použít Windows CMD, PowerShell nebo Git bash.
1. Spusťte následující kód a potvrďte, že je nainstalována CMake verze 3,14 nebo novější.

    ```shell
    cmake --version
    ```

## <a name="create-the-cloud-components"></a>Vytvoření cloudových součástí

### <a name="create-the-iot-central-application"></a>Vytvoření aplikace IoT Central

Existuje několik způsobů, jak připojit zařízení k Azure IoT. V této části se dozvíte, jak připojit zařízení pomocí služby Azure IoT Central. IoT Central je aplikační platforma IoT, která snižuje náklady a složitost vytváření a správy řešení IoT.

Vytvoření nové aplikace:
1. Na [portálu Azure IoT Central](https://apps.azureiotcentral.com/)vyberte **Moje aplikace** v navigační nabídce vedle sebe.
1. Vyberte **+ Nová aplikace**.
1. Vyberte **Vlastní aplikace**.
1. Přidejte název aplikace a adresu URL.
1. Pokud chcete aktivovat 7 dní zkušební verzi, vyberte **bezplatný** cenový plán.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-create-custom.png" alt-text="Vytvoření vlastní aplikace v Azure IoT Central":::

1. Vyberte **Vytvořit**.

    Jakmile IoT Central zřídí aplikaci, automaticky se přesměruje na nový řídicí panel aplikace.

    > [!NOTE]
    > Pokud máte existující aplikaci IoT Central, můžete ji použít k dokončení kroků v tomto článku, ale nemusíte vytvářet novou aplikaci.

### <a name="create-a-new-device"></a>Vytvoření nového zařízení

V této části použijete řídicí panel aplikace IoT Central k vytvoření nového zařízení. Informace o připojení nově vytvořeného zařízení budete používat k zabezpečenému připojení fyzického zařízení v pozdější části.

Postup vytvoření zařízení:
1. Z řídicího panelu aplikace v navigační nabídce vedle sebe vyberte **zařízení** .
1. Výběrem **+ Nová** otevřete okno **vytvořit nové zařízení** .
1. Ponechte šablonu zařízení **nepřiřazenou**.
1. Zadejte požadovaný název zařízení a ID zařízení.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-create-device.png" alt-text="Vytvoření zařízení v Azure IoT Central":::

1. Vyberte tlačítko **Vytvořit**.
1. Nově vytvořené zařízení se zobrazí v seznamu **všechna zařízení** .  Chcete-li zobrazit podrobnosti, vyberte možnost název zařízení.
1. V pravém horním panelu nabídek vyberte **připojit** a zobrazte informace o připojení, které se použijí ke konfiguraci zařízení v další části.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-connection-info.png" alt-text="Zobrazit podrobnosti o připojení zařízení":::

1. Všimněte si hodnot připojení pro následující parametry připojovacího řetězce, které se zobrazují v dialogovém okně **připojit** . Tyto hodnoty přidáte do konfiguračního souboru v dalším kroku:

    > * `ID scope`
    > * `Device ID`
    > * `Primary key`

## <a name="prepare-the-device"></a>Příprava zařízení

Pokud chcete připojit MXCHIP DevKit k Azure, upravíte konfigurační soubor pro Wi-Fi a nastavení Azure IoT, znovu sestavíte image a zablikáte image do zařízení.

### <a name="add-configuration"></a>Přidání konfigurace

1. V textovém editoru otevřete následující soubor:

    > *getting-started\MXChip\AZ3166\app\ azure_config. h*

1. Nastavte Wi-Fi konstanty na následující hodnoty z vašeho místního prostředí.

    |Název konstanty|Hodnota|
    |-------------|-----|
    |`WIFI_SSID` |{*Vaše Wi-Fi SSID*}|
    |`WIFI_PASSWORD` |{*Heslo pro Wi-Fi*}|
    |`WIFI_MODE` |{*Jedna z hodnot režimu výčtu Wi-Fi v souboru*}|

1. Nastavte konstanty informací o zařízení Azure IoT na hodnoty, které jste uložili po vytvoření prostředků Azure.

    |Název konstanty|Hodnota|
    |-------------|-----|
    |`IOT_DPS_ID_SCOPE` |{*Vaše hodnota oboru ID*}|
    |`IOT_DPS_REGISTRATION_ID` |{*Vaše hodnota ID zařízení*}|
    |`IOT_DEVICE_SAS_KEY` |{*Vaše hodnota primárního klíče*}|

1. Uložte soubor a zavřete ho.

### <a name="build-the-image"></a>Sestavení image

V konzole nebo v Průzkumníku souborů spusťte skript *rebuild.bat* v následující cestě k sestavení Image:

> *getting-started\MXChip\AZ3166\tools\rebuild.bat*

Po dokončení sestavení ověřte, zda byl binární soubor vytvořen v následující cestě:

> *getting-started\MXChip\AZ3166\build\app\ mxchip_azure_iot. bin*

### <a name="flash-the-image"></a>Blikání obrázku

1. Na DevKit MXCHIP najděte tlačítko **obnovit** a port micro USB. Tyto komponenty použijete v následujících krocích. Obě jsou zvýrazněna na následujícím obrázku:

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/mxchip-iot-devkit.png" alt-text="Najít klíčové komponenty na panelu MXChip DevKit":::

1. Připojte kabel Micro USB k portu Micro USB na MXCHIP DevKit a pak ho připojte k počítači.
1. V Průzkumníku souborů Najděte binární soubor, který jste vytvořili v předchozí části.
1. Zkopírujte binární soubor *mxchip_azure_iot. bin*.
1. V Průzkumníku souborů Najděte zařízení MXCHIP DevKit připojené k vašemu počítači. Zařízení se zobrazí jako jednotka v systému se jmenovkou jednotky **AZ3166**.
1. Vložte binární soubor do kořenové složky MXCHIP DevKit. Blikání se spustí automaticky a dokončí za pár sekund.

    > [!NOTE]
    > Během procesu blikání se zelený indikátor LED přepíná na MXCHIP DevKit.

### <a name="confirm-device-connection-details"></a>Potvrdit podrobnosti připojení zařízení

Pomocí aplikace **Termite** můžete monitorovat komunikaci a ověřit, jestli je zařízení správně nastavené.

1. Spusťte **Termite**.
    > [!TIP]
    > Pokud se Termite k DevKit nemůžete připojit, nainstalujte [ovladač St-Link](https://my.st.com/content/ccc/resource/technical/software/driver/files/stsw-link009.zip) a zkuste to znovu. Další postup najdete v tématu  [řešení potíží](https://github.com/azure-rtos/getting-started/blob/master/docs/troubleshooting.md) .
1. Vyberte **Nastavení**.
1. V dialogovém okně **nastavení sériového portu** ověřte následující nastavení a v případě potřeby aktualizujte:
    * **Přenosová rychlost**: 115 200
    * **Port**: port, ke kterému je připojen MXCHIP DevKit. Pokud je v rozevírací nabídce více možností portů, můžete najít správný port, který chcete použít. Otevřete Windows **Správce zařízení** a zobrazte **porty** a určete, který port se má použít.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/termite-settings.png" alt-text="Potvrzení nastavení v aplikaci Termite":::

1. Vyberte OK.
1. Stiskněte tlačítko **obnovit** na zařízení. Tlačítko je označené na zařízení a nachází se poblíž konektoru Micro USB.
1. V aplikaci **Termite** zkontrolujte následující hodnoty kontrolního bodu, abyste se ujistili, že je zařízení inicializované a připojené ke službě Azure IoT.

    ```output
    Starting Azure thread

    Initializing WiFi
        Connecting to SSID 'iot'
    SUCCESS: WiFi connected to iot

    Initializing DHCP
        IP address: 10.0.0.123
        Mask: 255.255.255.0
        Gateway: 10.0.0.1
    SUCCESS: DHCP initialized

    Initializing DNS client
        DNS address: 10.0.0.1
    SUCCESS: DNS client initialized

    Initializing SNTP client
        SNTP server 0.pool.ntp.org
        SNTP IP address: 185.242.56.3
        SNTP time update: Nov 16, 2020 23:47:35.385 UTC 
    SUCCESS: SNTP initialized

    Initializing Azure IoT DPS client
        DPS endpoint: global.azure-devices-provisioning.net
        DPS ID scope: ***
        Registration ID: ***
    SUCCESS: Azure IoT DPS client initialized

    Initializing Azure IoT Hub client
        Hub hostname: ***
        Device id: ***
        Model id: dtmi:azurertos:devkit:gsgmxchip;1
    Connected to IoTHub
    SUCCESS: Azure IoT Hub client initialized

    Starting Main loop
    ```

Nechejte Termite otevřené pro monitorování výstupu zařízení v následujících krocích.

## <a name="verify-the-device-status"></a>Ověření stavu zařízení

Zobrazení stavu zařízení na portálu IoT Central:
1. Z řídicího panelu aplikace v navigační nabídce vedle sebe vyberte **zařízení** .
1. Potvrďte, že je **stav zařízení** aktualizovaný na **zřízený**.
1. Ověřte, že se **Šablona zařízení** aktualizovala na **Začínáme Průvodce**.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-view-status.png" alt-text="Zobrazit stav zařízení v IoT Central":::

## <a name="view-telemetry"></a>Zobrazení telemetrických dat

Pomocí IoT Central můžete zobrazit tok telemetrie ze zařízení do cloudu.

Zobrazení telemetrie na portálu IoT Central:

1. Z řídicího panelu aplikace v navigační nabídce vedle sebe vyberte **zařízení** .
1. Vyberte zařízení ze seznamu zařízení.
1. Podívejte se na telemetrii, protože zařízení odesílá zprávy do cloudu na kartě **Přehled** .

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-telemetry.png" alt-text="Zobrazení telemetrie zařízení v IoT Central":::

    > [!NOTE]
    > Telemetrii ze zařízení můžete také monitorovat pomocí aplikace Termite.

## <a name="call-a-direct-method-on-the-device"></a>Volání přímé metody v zařízení

K volání přímé metody, kterou jste v zařízení implementovali, můžete použít taky IoT Central. Přímé metody mají název a můžou volitelně mít datovou část JSON, konfigurovatelné připojení a časový limit metody. V této části zavoláte metodu, která vám umožní zapnout nebo vypnout indikátor LED.

Volání metody na portálu IoT Central:

1. Na stránce zařízení vyberte kartu **příkaz** .
1. Vyberte **stav** a vyberte **Spustit**.  Indikátor LED by měl zapnout.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-invoke-method.png" alt-text="Volání přímé metody na zařízení":::

1. Zrušte výběr **stavu** a vyberte **Spustit**. Indikátor LED by se měl vypnout.

## <a name="view-device-information"></a>Zobrazit informace o zařízení

Informace o zařízení můžete zobrazit z IoT Central.

Na stránce zařízení vyberte **o** kartě.

:::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-about.png" alt-text="Zobrazit informace o zařízení v IoT Central":::

## <a name="debugging"></a>Ladění

Ladění aplikace naleznete v tématu [ladění pomocí Visual Studio Code](https://github.com/azure-rtos/getting-started/blob/master/docs/debugging.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už prostředky Azure vytvořené v tomto kurzu nepotřebujete, můžete je odstranit z portálu IoT Central. Pokud budete chtít v této Začínáme průvodci pokračovat dalším kurzem, můžete zachovat již vytvořené prostředky a znovu je použít.

Pokud chcete odebrat celou ukázkovou aplikaci Azure IoT Central a všechna její zařízení a prostředky:
1. Vyberte možnost **Správa**  >  **aplikace**.
1. Vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili vlastní image, která obsahuje vzorový kód Azure RTO, a pak obrázek Flash na zařízení MXCHIP DevKit. Použili jste také portál IoT Central k vytváření prostředků Azure, k bezpečnému připojení MXCHIP DevKit k Azure, zobrazení telemetrie a posílání zpráv.

* V případě vývojářů zařízení je doporučeným dalším krokem zobrazení dalších kurzů v řadě [Začínáme s vývojem zařízení Azure IoT Embedded](quickstart-device-development.md).
* Pokud máte problémy s tím, že se vaše zařízení inicializuje nebo připojí po provedení kroků v této příručce, přečtěte si téma [řešení potíží](https://github.com/azure-rtos/getting-started/blob/master/docs/troubleshooting.md).
* Další informace o tom, jak se v ukázkovém kódu pro tento kurz používají komponenty Azure RTO, najdete v tématu [použití Azure RTO v příručce Začínáme](https://github.com/azure-rtos/getting-started/blob/master/docs/using-azure-rtos.md).

    > [!IMPORTANT]
    > Azure RTO poskytuje výrobcům OEM komponenty pro zabezpečení komunikace a vytváření kódu a izolace dat pomocí základních mechanismů hardwarové ochrany MCU/MPU. Každý výrobce OEM je však nakonec zodpovědný za to, že jeho zařízení splňuje vývoj požadavků na zabezpečení.

