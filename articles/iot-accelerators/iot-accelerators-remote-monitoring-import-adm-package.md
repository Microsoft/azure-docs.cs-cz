---
title: Řešení vzdáleného monitorování importovat balíček – Azure | Dokumentace Microsoftu
description: Tento článek popisuje, jak importovat sady management package automatické zařízení do akcelerátoru řešení vzdáleného monitorování
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 8100914e9a1d1489cb80de55a689e17f6d28a941
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009848"
---
# <a name="import-an-automatic-device-management-package-into-your-remote-monitoring-solution-accelerator"></a>Naimportujte balíček správy automatické akcelerátor řešení vzdálené monitorování

Konfigurace správy automatické definuje změny konfigurace pro nasazení do skupiny zařízení. Tento článek předpokládá, že vývojáři ve vaší organizaci již vytvořena konfigurace správy automatické. Další informace o způsob, jak vytvořit vývojář na konfiguraci, naleznete v následující články s postupy služby IoT Hub:

- [Konfigurovat a monitorovat zařízení IoT ve velkém měřítku pomocí webu Azure portal](../iot-hub/iot-hub-auto-device-config.md)
- [Konfigurovat a monitorovat zařízení IoT ve velkém měřítku pomocí Azure CLI](../iot-hub/iot-hub-auto-device-config-cli.md)

Vývojář vytvoří a konfigurace správy automatické testy v prostředí pro vývoj. Jakmile budete připraveni, můžete importovat konfiguraci do akcelerátor řešení vzdálené monitorování.

## <a name="export-a-configuration"></a>Exportovat konfiguraci

Exportovat konfiguraci automatické správy z vývojového prostředí pomocí webu Azure portal:

1. Na webu Azure Portal přejděte k centru IoT, které používáte pro vývoj a testování zařízení IoT. Klikněte na tlačítko **konfigurace zařízení IoT**:

    [![IKonfigurace zařízení oT](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-expanded.png#lightbox)

1. Klikněte na položku konfigurace, které chcete použít. **Podrobnosti o konfiguraci zařízení** zobrazí se stránka:

    [![IPodrobnosti konfigurace zařízení oT](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-expanded.png#lightbox)
1. Klikněte na tlačítko **stažení konfiguračního souboru**:

    [![Dstáhn konfiguračního souboru](./media/iot-accelerators-remote-monitoring-import-adm-package/download-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/download-expanded.png#lightbox)

1. Uložení souboru JSON jako místní soubor s názvem **configuration.json**.

Nyní máte soubor, který obsahuje konfiguraci automatické zařízení pro správu. V další části je importovat tuto konfiguraci jako balíček do řešení vzdáleného monitorování.

## <a name="import-a-package"></a>Importovat balíček

Podle následujících pokynů k importu konfigurace správy automatické zařízení jako balíček do vašeho řešení:

1. Přejděte **balíčky** stránky ve vzdálené monitorování webového uživatelského rozhraní:  ![Stránka balíčky](media/iot-accelerators-remote-monitoring-import-adm-package/packagepage.png)

1. Klikněte na tlačítko **+ nový balíček pro**, zvolte **konfigurace** typ balíčku, a klikněte na **Procházet** vyberte **configuration.json** souboru uložili v předchozí části:

    ![Vyberte konfiguraci](media/iot-accelerators-remote-monitoring-import-adm-package/uploadpackage.png)

1. Klikněte na tlačítko **nahrát** přidání balíčku do vašeho řešení vzdáleného monitorování:

    ![Nahraný balíček](media/iot-accelerators-remote-monitoring-import-adm-package/uploadedpackage.png)

Nyní jste odeslali automatické konfigurace správy jako balíček. Na **nasazení** stránce tento balíček můžete nasadit do připojených zařízení.

## <a name="next-steps"></a>Další postup

Teď, když jsme zjistili, jak vytvořit balíček pro konfiguraci a importujte ho do řešení vzdáleného monitorování, dalším krokem je další způsob [spravovat zařízení připojená k vzdálené sledování hromadně](iot-accelerators-remote-monitoring-bulk-configuration-update.md).
