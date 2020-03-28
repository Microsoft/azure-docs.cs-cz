---
title: 'Úvodní příručka: Sada Speech SDK pro nastavení platformy C# Unity – služba rozpoznávání řeči'
titleSuffix: Azure Cognitive Services
description: Tato příručka slouží k nastavení platformy pro C# Unity se službou Speech SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/10/2019
ms.author: erhopf
ms.openlocfilehash: 8d9e05b5d8105cce8355d217cc6922c25bbe1730
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75468025"
---
Tato příručka ukazuje, jak nainstalovat [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro [jednotu](https://unity3d.com/).

> [!NOTE]
> Sada Speech SDK for Unity podporuje plochu Systému Windows (x86 a x64) nebo univerzální platformu Windows (x86, x64, ARM/ARM64), Android (x86, ARM32/64) a iOS (simulátor x64, ARM32 a ARM64)

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

- [Unity 2018.3 nebo novější](https://store.unity.com/) s [Unity 2019.1 přidání podpory pro UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal).
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Verze 15.9 nebo vyšší Visual Studio 2017 je také přijatelné.
- Pro podporu Windows ARM64 nainstalujte [volitelné nástroje sestavení pro ARM64 a Windows 10 SDK pro ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).

## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Chcete-li nainstalovat sadu Speech SDK for Unity, postupujte takto:

1. Stáhněte a otevřete [sadu Speech SDK for Unity](https://aka.ms/csspeech/unitypackage), která je zabalena jako balíček unity asset (.unitypackage) a již by měla být přidružena k Unity. Po otevření balíčku datových zdrojů se zobrazí dialogové okno **Import ovat balíček jednoty.** Možná budete muset vytvořit a otevřít prázdný projekt pro tento krok do práce.

   [![Dialogové okno Importovat balíček Unity v Editoru jednoty](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png#lightbox)

1. Zkontrolujte, zda jsou vybrány všechny soubory, a vyberte **importovat**. Po několika okamžicích unity datový chod balíček je importován do projektu.

Další informace o importu balíčků datových zdrojů do Unity naleznete v [dokumentaci Unity](https://docs.unity3d.com/Manual/AssetPackages.html).

Nyní můžete přejít na [další kroky](#next-steps) níže.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [windows](../quickstart-list.md)]
