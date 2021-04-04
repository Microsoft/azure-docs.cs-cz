---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 08/30/2019
ms.author: wolfma
ms.openlocfilehash: c7332177795a45331749225e0f3dc3856e28a6a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "96001085"
---
Chcete-li vytvořit projekt sady Visual Studio pro vývoj pro systém Windows, je nutné vytvořit projekt, nastavit aplikaci Visual Studio pro vývoj desktopových aplikací .NET, nainstalovat sadu Speech SDK a vybrat cílovou architekturu.

### <a name="create-the-project-and-add-the-workload"></a>Vytvoření projektu a přidání úlohy

Pokud chcete začít, vytvořte projekt v sadě Visual Studio a ujistěte se, že je sada Visual Studio nastavená pro vývoj desktopových aplikací pro .NET:

1. Otevřete Visual Studio 2019.

1. V okně Start vyberte **vytvořit nový projekt**. 

1. V okně **vytvořit nový projekt** zvolte **Konzolová aplikace (.NET Framework)** a pak vyberte **Další**.

1. V okně **Konfigurovat nový projekt** zadejte *HelloWorld* do pole **název projektu**, zvolte nebo vytvořte cestu k adresáři v **umístění** a pak vyberte **vytvořit**.

1. V řádku nabídek sady Visual Studio vyberte **nástroje**  >  **získat nástroje a funkce**, které se otevře instalační program pro Visual Studio a zobrazí se dialogové okno **změny** .

1. Ověřte, zda je k dispozici úloha **vývoj desktopových** aplikací pro .NET. Pokud se úloha nenainstalovala, zaškrtněte políčko vedle ní a pak výběrem **změnit** spusťte instalaci. Stažení a instalace může trvat několik minut.

   Pokud je zaškrtnuté políčko pro **vývoj desktopových** aplikací pro .NET je už vybrané, kliknutím na **Zavřít zavřete** dialogové okno.

   ![Povolení Vývoje desktopových aplikací pomocí .NET](../articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Zavřete Instalační program pro Visual Studio.

### <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Dalším krokem je instalace [balíčku NuGet sady Speech SDK](https://aka.ms/csspeech/nuget), abyste na něj mohli odkazovat v kódu.

1. V Průzkumník řešení klikněte pravým tlačítkem myši na projekt **HelloWorld** a pak výběrem **možnosti spravovat balíčky NuGet** Zobrazte správce balíčků NuGet.

   ![Správce balíčků NuGet](../articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. V pravém horním rohu Najděte rozevírací seznam **zdroj balíčku** a ujistěte se, že je vybraná možnost **NuGet.org** .

1. V levém horním rohu vyberte **Procházet**.

1. Do vyhledávacího pole zadejte *Microsoft. cognitiveservices Account. Speech* a vyberte **ENTER**.

1. Ve výsledcích hledání vyberte balíček **Microsoft. cognitiveservices Account. Speech** a pak vyberte **instalovat** a nainstalujte nejnovější stabilní verzi.

   ![Nainstalovat balíček NuGet Microsoft. Cognitiveservices Account. Speech](../articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Přijetím všech smluv a licencí spusťte instalaci.

   Po instalaci balíčku se v okně **konzoly Správce balíčků** zobrazí potvrzení.

### <a name="choose-the-target-architecture"></a>Výběr cílové architektury

Chcete-li nyní sestavit a spustit konzolovou aplikaci, vytvořte konfiguraci platformy, která odpovídá architektuře vašeho počítače.

1. V řádku nabídek vyberte **sestavení**  >  **Configuration Manager**. Zobrazí se dialogové okno **Configuration Manager** .

   ![Configuration Manager – dialogové okno](../articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. V rozevíracím seznamu **Aktivní platforma řešení** vyberte **Nový**. Zobrazí se dialogové okno **Nová platforma řešení** .

1. V rozevíracím seznamu **Typ vyberte možnost Nová platforma** :
   - Pokud používáte 64-bitové Windows, vyberte **x64**.
   - Pokud používáte 32-bit Windows, vyberte **x86**.

1. Vyberte **OK** a pak **Zavřít**.
