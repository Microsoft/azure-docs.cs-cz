---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 308ee2ef121648cb45152948926c5fd7fb934744
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68362473"
---
1. Otevřete Visual Studio 2019.

1. V okně Start vyberte **vytvořit nový projekt**. 

1. Vyberte **Konzolová aplikace (.NET Framework)** a pak vyberte **Další**.

1. Do **název projektu**zadejte `helloworld`a pak vyberte **vytvořit**.

1. Z panelu nabídek v aplikaci Visual Studio vyberte **nástroje** > **získat nástroje a funkce**a ověřte, zda je k dispozici úloha **vývoj pro desktopy .NET** . Pokud se úloha nenainstalovala, označte zaškrtávací políčko a kliknutím na tlačítko **změnit** spusťte instalaci. Stažení a instalace může trvat několik minut.

   Pokud je vybráno zaškrtávací políčko vedle funkce **vývoj desktopových** aplikací pro .NET, můžete nyní dialogové okno zavřít.

   ![Povolení Vývoje desktopových aplikací pomocí .NET](../articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

Dalším krokem je instalace [balíčku NuGet sady Speech SDK](https://aka.ms/csspeech/nuget), abyste na něj mohli odkazovat v kódu.

1. V Průzkumník řešení klikněte `helloworld`pravým tlačítkem myši a potom vyberte **Spravovat balíčky NuGet** , abyste zobrazili správce balíčků NuGet.

   ![Správce balíčků NuGet](../articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. V pravém horním rohu Najděte rozevírací seznam **zdroj balíčku** a ujistěte se, že je vybraná možnost **NuGet.org** .

1. V levém horním rohu vyberte **Procházet**.

1. Do vyhledávacího pole zadejte `Microsoft.CognitiveServices.Speech` Package a stiskněte klávesu ENTER.

1. Vyberte `Microsoft.CognitiveServices.Speech`a pak vyberte **instalovat** a nainstalujte nejnovější stabilní verzi.

   ![Nainstalovat balíček NuGet Microsoft. Cognitiveservices Account. Speech](../articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Přijetím všech smluv a licencí spusťte instalaci.

   Po instalaci balíčku se v okně **konzoly Správce balíčků** zobrazí potvrzení.

Chcete-li nyní sestavit a spustit konzolovou aplikaci, vytvořte konfiguraci platformy, která odpovídá architektuře vašeho počítače.

1. V řádku nabídek vyberte **sestavení** > **Configuration Manager**. **Nástroje Configuration Manager** zobrazí se dialogové okno.

   ![Configuration Manager – dialogové okno](../articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. V rozevíracím seznamu **Aktivní platforma řešení** vyberte **Nový**. Zobrazí se dialogové okno **Nová platforma řešení** .

1. V rozevíracím seznamu **Typ vyberte možnost Nová platforma** :
   - Pokud používáte 64-bitové Windows, vyberte **x64**.
   - Pokud používáte 32-bit Windows, vyberte **x86**.

1. Vyberte **OK** a pak **Zavřít**.
