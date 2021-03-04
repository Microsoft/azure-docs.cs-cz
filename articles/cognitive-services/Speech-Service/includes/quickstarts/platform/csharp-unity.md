---
title: 'Rychlý Start: sada Speech SDK pro C# Unity Platform Setup – Speech Service'
titleSuffix: Azure Cognitive Services
description: Tato příručka slouží k nastavení platformy pro Unity v jazyce C# se sadou Speech Service SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 895943b00ef2c2541b7de6cc81648877c9a73c0d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750932"
---
V této příručce se dozvíte, jak nainstalovat [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro [Unity](https://unity3d.com/).

> [!NOTE]
> Sada Speech SDK for Unity podporuje Desktop Windows (x86 a x64) nebo Univerzální platforma Windows (x86, x64, ARM/ARM64), Android (x86, ARM32/64) a iOS (simulátor x64 a ARM64).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

- V systému Windows potřebujete [Microsoft Visual C++ distribuovatelné součásti pro Visual Studio 2019](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0) pro vaši platformu. První instalace může vyžadovat restartování.
- [Unity 2018,3 nebo novější](https://store.unity.com/) s [Unity 2019,1 Přidání podpory pro UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal)
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Také je přijatelné verze 15,9 nebo vyšší ze sady Visual Studio 2017.
- Pro podporu Windows ARM64 nainstalujte [volitelné nástroje sestavení pro ARM64 a sadu Windows 10 SDK pro ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).

## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Pokud chcete sadu Speech SDK for Unity nainstalovat, postupujte takto:

1. Stáhněte si a otevřete [sadu Speech SDK for Unity](https://aka.ms/csspeech/unitypackage), která je zabalená jako balíček assetů Unity (. unitypackage), a měla by už být přidružená k Unity. Po otevření balíčku assetu se zobrazí dialogové okno **importovat balíček Unity** . Aby tento krok fungoval, možná budete muset vytvořit a otevřít prázdný projekt.

   [![Dialogové okno Import balíčku Unity v editoru Unity](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png#lightbox)

1. Zajistěte, aby byly vybrány všechny soubory, a vyberte **importovat**. Po chvíli se balíček assetů Unity naimportuje do vašeho projektu.

Další informace o importu balíčků assetů do Unity najdete v [dokumentaci k Unity](https://docs.unity3d.com/Manual/AssetPackages.html).

Nyní můžete přejít na [Další kroky](#next-steps) níže.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [windows](../quickstart-list.md)]
