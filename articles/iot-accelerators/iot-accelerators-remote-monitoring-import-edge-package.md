---
title: Balíček pro import hraničních řešení vzdáleného monitorování – Azure | Microsoft Docs
description: Tento článek popisuje, jak importovat balíček IoT Edge do akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/10/2018
ms.topic: conceptual
ms.openlocfilehash: 34222f396ed3c43932371aa9f64a459bb2a5dd0e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012284"
---
# <a name="import-an-iot-edge-package-into-your-remote-monitoring-solution-accelerator"></a>Import balíčku IoT Edge do akcelerátoru řešení vzdáleného monitorování

Manifest nasazení definuje moduly, které mají být nasazeny do zařízení IoT Edge. Tento článek předpokládá, že vývojář ve vaší organizaci už vytvořil manifest nasazení. Další informace o tom, jak Vývojář vytvoří manifest, najdete v některém z následujících článků o postupech IoT Edge:

- [Nasazení Azure IoT Edgech modulů z Azure Portal](../iot-edge/how-to-deploy-modules-portal.md)
- [Nasazení modulů Azure IoT Edge pomocí Azure CLI](../iot-edge/how-to-deploy-modules-cli.md)
- [Nasazení Azure IoT Edgech modulů z Visual Studio Code](../iot-edge/how-to-deploy-modules-vscode.md)

Vývojář vytvoří a otestuje manifest nasazení ve vývojovém prostředí. Až budete připraveni, můžete manifest importovat do akcelerátoru řešení vzdáleného monitorování.

## <a name="export-a-manifest"></a>Exportovat manifest

Pomocí Azure Portal exportujte manifest nasazení z vývojového prostředí:

1. V Azure Portal přejděte do služby IoT Hub, kterou používáte pro vývoj a testování vašich IoT Edgech zařízení. Klikněte na **IoT Edge** a pak **IoT Edge nasazení**: ![ IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/iotedge.png)

1. Klikněte na nasazení, které má konfiguraci nasazení, kterou chcete použít. Zobrazí se stránka s **podrobnostmi o nasazení** : ![ informace o nasazení IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/deploymentdetails.png)

1. Klikněte na **stáhnout IoT Edge manifest**:  ![ Stáhnout manifest nasazení](media/iot-accelerators-remote-monitoring-import-edge-package/download.png)

1. Uložte soubor JSON jako místní soubor s názvem **deploymentmanifest.jsv**.

Nyní máte soubor, který obsahuje manifest nasazení. V další části importujete tento manifest jako balíček do řešení vzdáleného monitorování.

## <a name="import-a-package"></a>Importovat balíček

Pomocí následujících kroků importujte manifest nasazení Edge jako balíček do vašeho řešení:

1. Přejděte na stránku **balíčky** ve webovém uživatelském rozhraní vzdáleného monitorování:  ![ balíčky.](media/iot-accelerators-remote-monitoring-import-edge-package/packagespage.png)

1. Klikněte na **+ nový balíček**, jako typ balíčku zvolte **manifest Edge** a klikněte na **Procházet** a vyberte **deploymentmanifest.jspro** soubor, který jste uložili v předchozí části:  ![ Vyberte manifest.](media/iot-accelerators-remote-monitoring-import-edge-package/selectmanifest.png)

1. Kliknutím na **nahrát** přidejte balíček do řešení vzdáleného monitorování:  ![ nahraný balíček](media/iot-accelerators-remote-monitoring-import-edge-package/uploadedpackage.png)

Nyní jste nahráli manifest nasazení IoT Edge jako balíček. Na stránce **nasazení** můžete tento balíček nasadit do připojených zařízení IoT Edge.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak nasadit moduly do zařízení IoT Edge z řešení vzdáleného monitorování, je dalším krokem Další informace o [IoT Edge](../iot-edge/about-iot-edge.md).
