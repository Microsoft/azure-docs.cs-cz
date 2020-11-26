---
title: 'Rychlý Start: sada Speech SDK pro .NET Framework (Windows) Platform Setup – služba pro rozpoznávání řeči'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto průvodce můžete nastavit platformu pro C# v části .NET Framework pro Windows se sadou Speech Service SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.custom: devx-track-dotnet
ms.openlocfilehash: f63c97fd23d56ff508de8d5953eefac6b15ae1aa
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188199"
---
V této příručce se dozvíte, jak nainstalovat [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro .NET Framework (Windows). Pokud chcete, aby název balíčku začal vlastním způsobem, spusťte `Install-Package Microsoft.CognitiveServices.Speech` v konzole NuGet.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Předpoklady

K tomuto rychlému startu potřebujete:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Vytvořit projekt sady Visual Studio a nainstalovat sadu Speech SDK

Budete muset nainstalovat [balíček NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) , abyste na něj mohli odkazovat v kódu. K tomu může být nutné nejprve vytvořit projekt **HelloWorld** . Pokud už máte projekt s dostupnými úlohou **vývoj desktopových** aplikací pro .NET, můžete použít tento projekt a přeskočit k [používání Správce balíčků NuGet k instalaci sady Speech SDK](#use-nuget-package-manager-to-install-the-speech-sdk).

### <a name="create-helloworld-project"></a>Vytvořit projekt HelloWorld

1. Otevřete Visual Studio 2019.

1. V okně Start vyberte **vytvořit nový projekt**. 

1. V okně **vytvořit nový projekt** zvolte **Konzolová aplikace (.NET Framework)** a pak vyberte **Další**.

1. V okně **Konfigurovat nový projekt** zadejte *HelloWorld* do pole **název projektu**, zvolte nebo vytvořte cestu k adresáři v **umístění** a pak vyberte **vytvořit**.

1. V řádku nabídek sady Visual Studio vyberte **nástroje**  >  **získat nástroje a funkce**, které se otevře instalační program pro Visual Studio a zobrazí se dialogové okno **změny** .

1. Ověřte, zda je k dispozici úloha **vývoj desktopových** aplikací pro .NET. Pokud se úloha nenainstalovala, zaškrtněte políčko vedle ní a pak výběrem **změnit** spusťte instalaci. Stažení a instalace může trvat několik minut.

   Pokud je zaškrtnuté políčko pro **vývoj desktopových** aplikací pro .NET je už vybrané, kliknutím na **Zavřít zavřete** dialogové okno.

   ![Povolení Vývoje desktopových aplikací pomocí .NET](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Zavřete Instalační program pro Visual Studio.

### <a name="use-nuget-package-manager-to-install-the-speech-sdk"></a>Použití Správce balíčků NuGet k instalaci sady Speech SDK

1. V Průzkumník řešení klikněte pravým tlačítkem myši na projekt **HelloWorld** a pak výběrem **možnosti spravovat balíčky NuGet** Zobrazte správce balíčků NuGet.

   ![Správce balíčků NuGet](~/articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. V pravém horním rohu Najděte rozevírací seznam **zdroj balíčku** a ujistěte se, že **`nuget.org`** je vybraná možnost.

1. V levém horním rohu vyberte **Procházet**.

1. Do vyhledávacího pole zadejte *Microsoft. cognitiveservices Account. Speech* a vyberte **ENTER**.

1. Ve výsledcích hledání vyberte balíček **Microsoft. cognitiveservices Account. Speech** a pak vyberte **instalovat** a nainstalujte nejnovější stabilní verzi.

   ![Nainstalovat balíček NuGet Microsoft. Cognitiveservices Account. Speech](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Přijetím všech smluv a licencí spusťte instalaci.

   Po instalaci balíčku se v okně **konzoly Správce balíčků** zobrazí potvrzení.

### <a name="choose-target-architecture"></a>Zvolit cílovou architekturu

Chcete-li sestavit a spustit konzolovou aplikaci, vytvořte konfiguraci platformy, která odpovídá architektuře vašeho počítače.

1. V řádku nabídek vyberte **sestavení**  >  **Configuration Manager**. Zobrazí se dialogové okno **Configuration Manager** .

   ![Configuration Manager – dialogové okno](~/articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. V rozevíracím seznamu **Aktivní platforma řešení** vyberte **Nový**. Zobrazí se dialogové okno **Nová platforma řešení** .

1. V rozevíracím seznamu **Typ vyberte možnost Nová platforma** :
   - Pokud používáte 64-bitové Windows, vyberte **x64**.
   - Pokud používáte 32-bit Windows, vyberte **x86**.

1. Vyberte **OK** a pak **Zavřít**.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [windows](../quickstart-list.md)]
