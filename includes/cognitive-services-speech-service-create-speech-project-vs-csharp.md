---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 08/30/2019
ms.author: wolfma
ms.openlocfilehash: c7332177795a45331749225e0f3dc3856e28a6a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "71327060"
---
Chcete-li vytvořit projekt sady Visual Studio pro vývoj systému Windows, musíte vytvořit projekt, nastavit visual studio pro vývoj plochy .NET, nainstalovat sadu Speech SDK a zvolit cílovou architekturu.

### <a name="create-the-project-and-add-the-workload"></a>Vytvoření projektu a přidání pracovního vytížení

Chcete-li začít, vytvořte projekt v sadě Visual Studio a ujistěte se, že visual studio je nastaveno pro vývoj desktopu .NET:

1. Otevřete Visual Studio 2019.

1. V okně Start vyberte **Vytvořit nový projekt**. 

1. V okně **Vytvořit nový projekt** zvolte Console App **(.NET Framework)** a pak vyberte **Další**.

1. V okně **Konfigurovat nový projekt** zadejte *helloworld* v **názvu projektu**, zvolte nebo vytvořte cestu k adresáři v **lokaci**a pak vyberte **Vytvořit**.

1. Na řádku nabídek Sady Visual Studio vyberte **Nástroje** > **získat nástroje a funkce**, který otevře Instalační službu sady Visual Studio a zobrazí dialogové okno **Úpravy.**

1. Zkontrolujte, zda je k dispozici **úloha vývoje plochy .NET.** Pokud pracovní vytížení nebylo nainstalováno, zaškrtněte políčko vedle něj a pak vyberte **Změnit** a spusťte instalaci. Stažení a instalace může trvat několik minut.

   Pokud je políčko vedle **vývoje plochy rozhraní .NET** zaškrtnuté, vyberte **Zavřít** a ukončete dialogové okno.

   ![Povolení Vývoje desktopových aplikací pomocí .NET](../articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Zavřete Instalační program sady Visual Studio.

### <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Dalším krokem je instalace [balíčku Speech SDK NuGet](https://aka.ms/csspeech/nuget), takže na něj můžete odkazovat v kódu.

1. V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt **helloworld** a pak vyberte **spravovat balíčky NuGet,** chcete-li zobrazit Správce balíčků NuGet.

   ![Správce balíčků NuGet](../articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. V pravém horním rohu vyhledejte rozevírací pole **Zdroj balíčku** a ujistěte se, že je vybraná **nuget.org.**

1. V levém horním rohu vyberte **Procházet**.

1. Do vyhledávacího pole zadejte *Microsoft.CognitiveServices.Speech* a vyberte **Enter**.

1. Ve výsledcích hledání vyberte balíček **Microsoft.CognitiveServices.Speech** a pak vyberte **Instalovat** a nainstalujte nejnovější stabilní verzi.

   ![Instalace balíčku Microsoft.CognitiveServices.Speech NuGet](../articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Přijměte všechny smlouvy a licence k zahájení instalace.

   Po instalaci balíčku se v okně **konzoly Správce balíčků** zobrazí potvrzení.

### <a name="choose-the-target-architecture"></a>Zvolte cílovou architekturu

Chcete-li nyní vytvořit a spustit konzolovou aplikaci, vytvořte konfiguraci platformy odpovídající architektuře počítače.

1. Na řádku nabídek vyberte **Build** > **Configuration Manager**. Zobrazí se dialogové okno **Správce konfigurace.**

   ![Dialogové okno Správce konfigurace](../articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. V rozevíracím poli **Aktivní platforma řešení** vyberte **Nový**. Zobrazí se dialogové okno **Nová platforma řešení.**

1. V rozevíracím poli **Typ nebo vyberte novou platformu:**
   - Pokud používáte 64bitový systém Windows, vyberte **x64**.
   - Pokud používáte 32bitový systém Windows, vyberte **x86**.

1. Vyberte **OK** a potom **zavřete**.
