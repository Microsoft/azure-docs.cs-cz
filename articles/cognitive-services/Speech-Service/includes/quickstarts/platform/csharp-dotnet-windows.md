---
title: 'Rychlý Start: sada Speech SDK pro .NET Framework (Windows) Platform Setup – služba pro rozpoznávání řeči'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto průvodce můžete nastavit platformu pro C# Windows v části .NET Framework pro Windows se sadou Speech Services SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/10/2019
ms.author: erhopf
ms.openlocfilehash: 2caa5124144146e2286b102bf6ded9c5d120f904
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502510"
---
V této příručce se dozvíte, jak nainstalovat [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro .NET Framework (Windows).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Vytvořit projekt sady Visual Studio a nainstalovat sadu Speech SDK

Budete muset nainstalovat [balíček NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) , abyste na něj mohli odkazovat v kódu. K tomu může být nutné nejprve vytvořit projekt **HelloWorld** . Pokud už máte projekt s dostupnými úlohou **vývoj desktopových** aplikací pro .NET, můžete použít tento projekt a přeskočit k [používání Správce balíčků NuGet k instalaci sady Speech SDK](#use-nuget-package-manager-to-install-the-speech-sdk).

### <a name="create-helloworld-project"></a>Vytvořit projekt HelloWorld

1. Otevřete Visual Studio 2019.

1. V okně Start vyberte **vytvořit nový projekt**. 

1. V okně **vytvořit nový projekt** zvolte **Konzolová aplikace (.NET Framework)** a pak vyberte **Další**.

1. V okně **Konfigurovat nový projekt** zadejte *HelloWorld* do pole **název projektu**, zvolte nebo vytvořte cestu k adresáři v **umístění**a pak vyberte **vytvořit**.

1. V řádku nabídek sady Visual Studio vyberte **nástroje** > **získat nástroje a funkce**, které se otevřou instalační program pro Visual Studio a zobrazí se dialogové okno **změny** .

1. Ověřte, zda je k dispozici úloha **vývoj desktopových** aplikací pro .NET. Pokud se úloha nenainstalovala, zaškrtněte políčko vedle ní a pak výběrem **změnit** spusťte instalaci. Stažení a instalace může trvat několik minut.

   Pokud je zaškrtnuté políčko pro **vývoj desktopových** aplikací pro .NET je už vybrané, kliknutím na **Zavřít zavřete** dialogové okno.

   ![Povolení Vývoje desktopových aplikací pomocí .NET](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Zavřete Instalační program pro Visual Studio.

### <a name="use-nuget-package-manager-to-install-the-speech-sdk"></a>Použití Správce balíčků NuGet k instalaci sady Speech SDK

1. V Průzkumník řešení klikněte pravým tlačítkem myši na projekt **HelloWorld** a pak výběrem **možnosti spravovat balíčky NuGet** Zobrazte správce balíčků NuGet.

   ![Správce balíčků NuGet](~/articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. V pravém horním rohu Najděte rozevírací seznam **zdroj balíčku** a ujistěte se, že je vybraná možnost **`nuget.org`** .

1. V levém horním rohu vyberte **Procházet**.

1. Do vyhledávacího pole zadejte *Microsoft. cognitiveservices Account. Speech* a vyberte **ENTER**.

1. Ve výsledcích hledání vyberte balíček **Microsoft. cognitiveservices Account. Speech** a pak vyberte **instalovat** a nainstalujte nejnovější stabilní verzi.

   ![Nainstalovat balíček NuGet Microsoft. Cognitiveservices Account. Speech](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Přijetím všech smluv a licencí spusťte instalaci.

   Po instalaci balíčku se v okně **konzoly Správce balíčků** zobrazí potvrzení.

Nyní můžete přejít na [Další kroky](#next-steps) níže.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [windows](../quickstart-list.md)]
