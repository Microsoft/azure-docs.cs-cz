---
title: 'Rychlý Start: Vytvoření aplikace pro HoloLens pomocí Unity'
description: V tomto rychlém startu se dozvíte, jak vytvořit aplikaci pro HoloLens v Unity pomocí kotev objektů.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 4f85a258042430d58690ef578db6d21a6c831d50
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102044645"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-unity"></a>Rychlý Start: Vytvoření aplikace HoloLens pomocí kotev objektů Azure v Unity

V tomto rychlém startu vytvoříte aplikaci Unity HoloLens, která používá [kotvy objektů Azure](../overview.md). Kotvy objektů Azure je spravovaná cloudová služba, která převádí 3D prostředky na modely AI, které umožňují objektově pracujícím prostředí HoloLens. Až budete hotovi, budete mít aplikaci HoloLens vytvořenou v Unity, která dokáže detekovat objekty ve fyzickém světě.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Připravte nastavení sestavení Unity.
> * Exportujte projekt HoloLens sady Visual Studio.
> * Nasaďte aplikaci a spusťte ji na zařízení HoloLens 2.

[!INCLUDE [Unity quickstart prerequisites](../../../includes/object-anchors-quickstart-unity-prerequisites.md)]

[!INCLUDE [Unity device setup](../../../includes/object-anchors-quickstart-unity-device-setup.md)]

## <a name="open-the-sample-project"></a>Otevřete vzorový projekt

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

[!INCLUDE [Download Unity Package](../../../includes/object-anchors-quickstart-unity-download-package.md)]

V Unity otevřete `quickstarts/apps/unity/basic` projekt.

[!INCLUDE [Import Unity Package](../../../includes/object-anchors-quickstart-unity-import-package.md)]

[!INCLUDE [Unity build sample scene 1](../../../includes/object-anchors-quickstart-unity-build-sample-scene-1.md)]

[!INCLUDE [Unity build sample scene 2](../../../includes/object-anchors-quickstart-unity-build-sample-scene-2.md)]

[!INCLUDE [Unity build and deploy](../../../includes/object-anchors-quickstart-unity-build-deploy.md)]

### <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

Zapněte zařízení, vyberte **všechny aplikace** a pak vyhledejte a spusťte aplikaci. Po úvodní obrazovce Unity se zobrazí zpráva oznamující, že došlo k inicializaci pozorovatele objektu. Do aplikace ale budete muset přidat svůj model.

[!INCLUDE [Unity setup Windows Device Portal](../../../includes/object-anchors-quickstart-unity-setup-device-portal.md)]

[!INCLUDE [Unity upload your model](../../../includes/object-anchors-quickstart-unity-upload-model.md)]

Aplikace vyhledá objekty v aktuálním poli zobrazení a poté je po zjištění zjištěna. Instance bude odebrána, když je 6 metrů z umístění uživatele. Text ladění zobrazí podrobnosti o instanci, jako je ID, aktualizované časové razítko a poměr pokrytí povrchu.

[!INCLUDE [Unity troubleshooting](../../../includes/object-anchors-quickstart-unity-troubleshooting.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [HoloLens v Unity s MRTK](get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [Koncepty: Přehled sady SDK](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [Nejčastější dotazy](../faq.md)
