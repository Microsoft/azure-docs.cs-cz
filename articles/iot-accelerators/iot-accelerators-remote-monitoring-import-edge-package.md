---
title: Řešení vzdáleného monitorování importovat balíček Edge – Azure | Dokumentace Microsoftu
description: Tento článek popisuje, jak importovat balíček IoT Edge do akcelerátoru řešení vzdáleného monitorování
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/10/2018
ms.topic: conceptual
ms.openlocfilehash: 34222f396ed3c43932371aa9f64a459bb2a5dd0e
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2018
ms.locfileid: "51828192"
---
# <a name="import-an-iot-edge-package-into-your-remote-monitoring-solution-accelerator"></a>Importovat balíček IoT Edge do akcelerátor řešení vzdálené monitorování

Manifest nasazení definuje moduly, které chcete nasadit do zařízení IoT Edge. Tento článek předpokládá, že vývojáři ve vaší organizaci již vytvořeno manifestu nasazení. Další informace o tom, jak vývojáři vytvoří manifest, naleznete v následujících článcích s postupy IoT Edge:

- [Nasadit moduly Azure IoT Edge z portálu Azure portal](../iot-edge/how-to-deploy-modules-portal.md)
- [Nasadit moduly Azure IoT Edge pomocí Azure CLI](../iot-edge/how-to-deploy-modules-cli.md)
- [Nasadit moduly Azure IoT Edge z Visual Studio Code](../iot-edge/how-to-deploy-modules-vscode.md)

Vývojář vytváří a testuje manifest nasazení ve vývojovém prostředí. Jakmile budete připraveni, můžete importovat do manifestu do akcelerátor řešení vzdálené monitorování.

## <a name="export-a-manifest"></a>Export manifestu

Export manifest nasazení z vývojového prostředí pomocí webu Azure portal:

1. Na webu Azure Portal přejděte k centru IoT, které používáte pro vývoj a testování vašeho zařízení IoT Edge. Klikněte na tlačítko **IoT Edge** a potom **nasazení IoT Edge**: ![IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/iotedge.png)

1. Klikněte na nasazení, jehož konfigurace nasazení, který chcete použít. **Podrobnosti o nasazení** zobrazí se stránka: ![podrobnosti o nasazení IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/deploymentdetails.png)

1. Klikněte na tlačítko **IoT Edge stáhnout manifest**: ![stáhnout manifest nasazení](media/iot-accelerators-remote-monitoring-import-edge-package/download.png)

1. Uložení souboru JSON jako místní soubor s názvem **deploymentmanifest.json**.

Nyní máte soubor obsahující manifest nasazení. V další části importu manifestu jako balíček do řešení vzdáleného monitorování.

## <a name="import-a-package"></a>Importovat balíček

Podle následujících pokynů k importu manifestu nasazení Edge jako balíček do vašeho řešení:

1. Přejděte na **balíčky** stránky ve vzdálené monitorování webového uživatelského rozhraní: ![balíčky stránky](media/iot-accelerators-remote-monitoring-import-edge-package/packagespage.png)

1. Klikněte na tlačítko **+ nový balíček pro**, zvolte **Edge Manifest** typ balíčku, a klikněte na **Procházet** vyberte **deploymentmanifest.json** souboru jste si uložili v předchozí části: ![manifestu vyberte](media/iot-accelerators-remote-monitoring-import-edge-package/selectmanifest.png)

1. Klikněte na tlačítko **nahrát** přidání balíčku do vašeho řešení vzdáleného monitorování: ![nahrané balíčku](media/iot-accelerators-remote-monitoring-import-edge-package/uploadedpackage.png)

Teď když jste odeslali manifest nasazení IoT Edge jako balíček. Na **nasazení** stránce tento balíček můžete nasadit do připojených zařízení IoT Edge.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak nasadit moduly do zařízení IoT Edge z řešení vzdáleného monitorování, dalším krokem je další informace o [IoT Edge](../iot-edge/about-iot-edge.md).
