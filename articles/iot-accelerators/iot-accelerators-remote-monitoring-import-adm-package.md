---
title: Import balíčku řešení vzdáleného monitorování – Azure | Microsoft Docs
description: Tento článek popisuje, jak importovat automatický balíček pro správu zařízení do akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 8100914e9a1d1489cb80de55a689e17f6d28a941
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "61443328"
---
# <a name="import-an-automatic-device-management-package-into-your-remote-monitoring-solution-accelerator"></a>Import automatického balíčku pro správu zařízení do akcelerátoru řešení vzdáleného monitorování

Automatická konfigurace správy zařízení definuje změny konfigurace, které se mají nasadit do skupiny zařízení. V tomto článku se předpokládá, že vývojář ve vaší organizaci už vytvořil automatickou konfiguraci správy zařízení. Další informace o tom, jak Vývojář vytvoří konfiguraci, najdete v některém z následujících článků s postupy IoT Hub:

- [Nakonfigurujte a monitorujte zařízení IoT ve velkém měřítku pomocí Azure Portal](../iot-hub/iot-hub-auto-device-config.md)
- [Konfigurace a monitorování škálování zařízení IoT pomocí Azure CLI](../iot-hub/iot-hub-auto-device-config-cli.md)

Vývojář vytvoří a testuje automatickou konfiguraci správy zařízení ve vývojovém prostředí. Až budete připraveni, můžete importovat konfiguraci do akcelerátoru řešení vzdáleného monitorování.

## <a name="export-a-configuration"></a>Export konfigurace

Pomocí Azure Portal exportujte automatickou konfiguraci správy zařízení z vašeho vývojového prostředí:

1. V Azure Portal přejděte do služby IoT Hub, kterou používáte pro vývoj a testování zařízení IoT. Klikněte na **Konfigurace zařízení IoT**:

    [![Konfigurace zařízení IoT](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-expanded.png#lightbox)

1. Klikněte na konfiguraci, kterou chcete použít. Zobrazí se stránka **Podrobnosti konfigurace zařízení** :

    [![Podrobnosti konfigurace zařízení IoT](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-expanded.png#lightbox)
1. Klikněte na **Stáhnout konfigurační soubor**:

    [![Stažení konfiguračního souboru](./media/iot-accelerators-remote-monitoring-import-adm-package/download-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/download-expanded.png#lightbox)

1. Uložte soubor JSON jako místní soubor s názvem **configuration.jsv**.

Nyní máte soubor, který obsahuje automatickou konfiguraci správy zařízení. V další části naimportujete tuto konfiguraci jako balíček do řešení vzdáleného monitorování.

## <a name="import-a-package"></a>Importovat balíček

Pomocí následujícího postupu naimportujte automatickou konfiguraci správy zařízení jako balíček do svého řešení:

1. Přejděte na stránku **balíčky** ve webovém uživatelském rozhraní vzdáleného monitorování:  ![ balíčky.](media/iot-accelerators-remote-monitoring-import-adm-package/packagepage.png)

1. Klikněte na **+ nový balíček**, zvolte **Konfigurace** jako typ balíčku a klikněte na **Procházet** a vyberte **configuration.jspro** soubor, který jste uložili v předchozí části:

    ![Vybrat konfiguraci](media/iot-accelerators-remote-monitoring-import-adm-package/uploadpackage.png)

1. Kliknutím na **nahrát** přidejte balíček do řešení vzdáleného monitorování:

    ![Nahraný balíček](media/iot-accelerators-remote-monitoring-import-adm-package/uploadedpackage.png)

Nyní jste nahráli automatickou konfiguraci správy zařízení jako balíček. Na stránce **nasazení** můžete tento balíček nasadit do připojených zařízení.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak vytvořit konfigurační balíček a importovat ho do nástroje pro vzdálené monitorování, je dalším krokem informace o tom, jak [spravovat zařízení připojená ke vzdálenému monitorování hromadně](iot-accelerators-remote-monitoring-bulk-configuration-update.md).
