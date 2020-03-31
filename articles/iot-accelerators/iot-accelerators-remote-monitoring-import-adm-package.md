---
title: Balíček importu řešení vzdáleného monitorování – Azure | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak importovat balíček automatické správy zařízení do akcelerátoru řešení vzdáleného monitorování
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 8100914e9a1d1489cb80de55a689e17f6d28a941
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61443328"
---
# <a name="import-an-automatic-device-management-package-into-your-remote-monitoring-solution-accelerator"></a>Import balíčku automatické správy zařízení do akcelerátoru řešení vzdáleného monitorování

Konfigurace automatické správy zařízení definuje změny konfigurace, které se mají nasadit do skupiny zařízení. Tento článek předpokládá, že vývojář ve vaší organizaci již vytvořil automatickou konfiguraci správy zařízení. Informace o tom, jak vývojář vytvoří konfiguraci, najdete v jednom z následujících článků s postupy služby IoT Hub:

- [Konfigurace a monitorování zařízení IoT ve velkém měřítku pomocí portálu Azure](../iot-hub/iot-hub-auto-device-config.md)
- [Konfigurace a monitorování zařízení IoT ve velkém měřítku pomocí rozhraní příkazového příkazu Azure](../iot-hub/iot-hub-auto-device-config-cli.md)

Vývojář vytvoří a testuje konfiguraci automatické správy zařízení ve vývojovém prostředí. Až budete připraveni, můžete importovat konfiguraci do akcelerátoru řešení vzdáleného monitorování.

## <a name="export-a-configuration"></a>Export konfigurace

Pomocí portálu Azure exportujte konfiguraci automatické správy zařízení z vývojového prostředí:

1. Na webu Azure Portal přejděte do centra IoT, které používáte k vývoji a testování zařízení IoT. Klikněte na **konfiguraci zařízení IoT**:

    [![Konfigurace zařízení IoT](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-expanded.png#lightbox)

1. Klikněte na konfiguraci, kterou chcete použít. Stránka **Podrobnosti konfigurace zařízení** zobrazuje:

    [![Detail konfigurace zařízení IoT](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-expanded.png#lightbox)
1. Klikněte na **Stáhnout konfigurační soubor**:

    [![Stáhnout konfigurační soubor](./media/iot-accelerators-remote-monitoring-import-adm-package/download-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/download-expanded.png#lightbox)

1. Uložte soubor JSON jako místní soubor nazvaný **configuration.json**.

Nyní máte soubor, který obsahuje automatickou konfiguraci správy zařízení. V další části importujete tuto konfiguraci jako balíček do řešení vzdáleného monitorování.

## <a name="import-a-package"></a>Import balíčku

Podle následujících kroků importujte do řešení automatickou konfiguraci správy zařízení jako balíček:

1. Přejděte na stránku **Balíčky** na ![webovém uživatelském uživatelském mši vzdáleného monitorování: Balíčky](media/iot-accelerators-remote-monitoring-import-adm-package/packagepage.png)

1. Klepněte na **+ Nový balíček**, zvolte **Konfigurace** jako typ balíčku a klepnutím na **tlačítko Procházet** vyberte soubor **configuration.json,** který jste uložili v předchozí části:

    ![Vybrat konfiguraci](media/iot-accelerators-remote-monitoring-import-adm-package/uploadpackage.png)

1. Kliknutím na **Nahrát** přidáte balíček do řešení vzdáleného monitorování:

    ![Nahraný balíček](media/iot-accelerators-remote-monitoring-import-adm-package/uploadedpackage.png)

Nyní jste jako balíček nahráli automatickou konfiguraci správy zařízení. Na stránce **Nasazení** můžete tento balíček nasadit do připojených zařízení.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak vytvořit konfigurační balíček a importovat ho z řešení vzdáleného monitorování, je dalším krokem naučit se [spravovat zařízení připojená ke vzdálenému monitorování hromadně](iot-accelerators-remote-monitoring-bulk-configuration-update.md).
