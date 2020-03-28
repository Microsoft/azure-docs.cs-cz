---
title: 'Úvodní příručka: Sada Speech SDK pro nastavení platformy rozhraní .NET Framework (Windows) – služba Rozpoznávání řeči'
titleSuffix: Azure Cognitive Services
description: Tato příručka slouží k nastavení platformy pro c# v rozhraní .NET Framework pro Windows pomocí sady SDK služby Rozpoznávání řeči.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/10/2019
ms.author: erhopf
ms.openlocfilehash: a09b969ee3e11aeb04f338cf035b21b5da9bd952
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925808"
---
Tato příručka ukazuje, jak nainstalovat [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro rozhraní .NET Framework (Windows). Pokud chcete, aby název balíčku začít na `Install-Package Microsoft.CognitiveServices.Speech` vlastní pěst, spusťte v konzoli NuGet.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Vytvoření projektu sady Visual Studio a instalace sady Speech SDK

Budete muset nainstalovat [balíček Speech SDK NuGet,](https://aka.ms/csspeech/nuget) abyste na něj mohli odkazovat ve vašem kódu. Chcete-li to provést, možná budete muset nejprve vytvořit projekt **helloworld.** Pokud již máte projekt s **úlohou vývoje plochy .NET** k dispozici, můžete použít tento projekt a přeskočit [na použití Správce balíčků NuGet k instalaci sady Speech SDK](#use-nuget-package-manager-to-install-the-speech-sdk).

### <a name="create-helloworld-project"></a>Vytvořte projekt helloworld

1. Otevřete Visual Studio 2019.

1. V okně Start vyberte **Vytvořit nový projekt**. 

1. V okně **Vytvořit nový projekt** zvolte Console App **(.NET Framework)** a pak vyberte **Další**.

1. V okně **Konfigurovat nový projekt** zadejte *helloworld* v **názvu projektu**, zvolte nebo vytvořte cestu k adresáři v **lokaci**a pak vyberte **Vytvořit**.

1. Na řádku nabídek Sady Visual Studio vyberte **Nástroje** > **získat nástroje a funkce**, který otevře Instalační službu sady Visual Studio a zobrazí dialogové okno **Úpravy.**

1. Zkontrolujte, zda je k dispozici **úloha vývoje plochy .NET.** Pokud pracovní vytížení nebylo nainstalováno, zaškrtněte políčko vedle něj a pak vyberte **Změnit** a spusťte instalaci. Stažení a instalace může trvat několik minut.

   Pokud je políčko vedle **vývoje plochy rozhraní .NET** zaškrtnuté, vyberte **Zavřít** a ukončete dialogové okno.

   ![Povolení Vývoje desktopových aplikací pomocí .NET](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Zavřete Instalační program sady Visual Studio.

### <a name="use-nuget-package-manager-to-install-the-speech-sdk"></a>Instalace sady Speech SDK pomocí Správce balíčků NuGet

1. V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt **helloworld** a pak vyberte **spravovat balíčky NuGet,** chcete-li zobrazit Správce balíčků NuGet.

   ![Správce balíčků NuGet](~/articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. V pravém horním rohu vyhledejte rozevírací pole **Zdroj** balíčku **`nuget.org`** a ujistěte se, že je vybrána.

1. V levém horním rohu vyberte **Procházet**.

1. Do vyhledávacího pole zadejte *Microsoft.CognitiveServices.Speech* a vyberte **Enter**.

1. Ve výsledcích hledání vyberte balíček **Microsoft.CognitiveServices.Speech** a pak vyberte **Instalovat** a nainstalujte nejnovější stabilní verzi.

   ![Instalace balíčku Microsoft.CognitiveServices.Speech NuGet](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Přijměte všechny smlouvy a licence k zahájení instalace.

   Po instalaci balíčku se v okně **konzoly Správce balíčků** zobrazí potvrzení.

### <a name="choose-target-architecture"></a>Zvolte cílovou architekturu

Chcete-li vytvořit a spustit konzolovou aplikaci, vytvořte konfiguraci platformy odpovídající architektuře počítače.

1. Na řádku nabídek vyberte **Build** > **Configuration Manager**. Zobrazí se dialogové okno **Správce konfigurace.**

   ![Dialogové okno Správce konfigurace](~/articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. V rozevíracím poli **Aktivní platforma řešení** vyberte **Nový**. Zobrazí se dialogové okno **Nová platforma řešení.**

1. V rozevíracím poli **Typ nebo vyberte novou platformu:**
   - Pokud používáte 64bitový systém Windows, vyberte **x64**.
   - Pokud používáte 32bitový systém Windows, vyberte **x86**.

1. Vyberte **OK** a potom **zavřete**.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [windows](../quickstart-list.md)]
