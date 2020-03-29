---
title: Vzdálené monitorování řešení importu balíčku Edge – Azure | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak importovat balíček IoT Edge do akcelerátoru řešení vzdáleného monitorování
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/10/2018
ms.topic: conceptual
ms.openlocfilehash: 34222f396ed3c43932371aa9f64a459bb2a5dd0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61442935"
---
# <a name="import-an-iot-edge-package-into-your-remote-monitoring-solution-accelerator"></a>Import balíčku IoT Edge do akcelerátoru řešení vzdáleného monitorování

Manifest nasazení definuje moduly, které se mají nasadit do zařízení IoT Edge. Tento článek předpokládá, že vývojář ve vaší organizaci již vytvořil manifest nasazení. Informace o tom, jak vývojář vytvoří manifest, najdete v jednom z následujících článků s postupy pro IoT Edge:

- [Nasazení modulů Azure IoT Edge z webu Azure Portal](../iot-edge/how-to-deploy-modules-portal.md)
- [Nasazení modulů Azure IoT Edge pomocí rozhraní příkazového příkazového příkazu Azure](../iot-edge/how-to-deploy-modules-cli.md)
- [Nasazení modulů Azure IoT Edge z kódu Visual Studia](../iot-edge/how-to-deploy-modules-vscode.md)

Vývojář vytvoří a otestuje manifest nasazení ve vývojovém prostředí. Až budete připraveni, můžete importovat manifest do akcelerátoru řešení vzdáleného monitorování.

## <a name="export-a-manifest"></a>Export manifestu

Pomocí portálu Azure exportujte manifest nasazení z vývojového prostředí:

1. Na webu Azure Portal přejděte do centra IoT, které používáte k vývoji a testování zařízení IoT Edge. Klikněte na **IoT Edge** a potom ![na nasazení **IoT Edge**: IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/iotedge.png)

1. Klikněte na nasazení, které má konfiguraci nasazení, kterou chcete použít. Zobrazí se stránka ![ **Podrobnosti o nasazení:** Podrobnosti o nasazení IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/deploymentdetails.png)

1. Klikněte na **Stáhnout manifest IoT Edge**: ![Manifest nasazení download.](media/iot-accelerators-remote-monitoring-import-edge-package/download.png)

1. Uložte soubor JSON jako místní soubor s názvem **deploymentmanifest.json**.

Nyní máte soubor, který obsahuje manifest nasazení. V další části importujete tento manifest jako balíček do řešení vzdáleného monitorování.

## <a name="import-a-package"></a>Import balíčku

Podle následujících kroků importujte manifest nasazení Edge jako balíček do vašeho řešení:

1. Přejděte na stránku **Balíčky** na ![webovém uživatelském uživatelském mši vzdáleného monitorování: Balíčky](media/iot-accelerators-remote-monitoring-import-edge-package/packagespage.png)

1. Klepněte na **+ Nový balíček**, jako typ balíčku zvolte **Edge Manifest** a klepnutím na tlačítko **Procházet** vyberte soubor **deploymentmanifest.json,** který jste uložili v předchozí části: ![Vyberte manifest.](media/iot-accelerators-remote-monitoring-import-edge-package/selectmanifest.png)

1. Kliknutím na **Nahrát** přidáte balíček ![do řešení vzdáleného monitorování: Nahraný balíček](media/iot-accelerators-remote-monitoring-import-edge-package/uploadedpackage.png)

Teď jste nahráli manifest nasazení IoT Edge jako balíček. Na stránce **Nasazení** můžete tento balíček nasadit do připojených zařízení IoT Edge.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili nasazovat moduly do zařízení IoT Edge z řešení vzdáleného monitorování, je dalším krokem další informace o [IoT Edge](../iot-edge/about-iot-edge.md).
