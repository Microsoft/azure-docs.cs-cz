---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: 72e9334b4df58b1b90288cb7363a7d94bb531661
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80658689"
---
Chcete-li vytvořit projekt sady Visual Studio pro vývoj plochy jazyka C++, je třeba nastavit možnosti vývoje sady Visual Studio, vytvořit projekt, vybrat cílovou architekturu a nainstalovat sadu Speech SDK.

### <a name="set-up-visual-studio-development-options"></a>Nastavení možností vývoje sady Visual Studio

Chcete-li začít, ujistěte se, že jste správně nastaveni ve Visual Studiu pro vývoj plochy v Jazyce C++:

1. Otevřete Visual Studio 2019 pro zobrazení okna **Start.**

   ![Počáteční okno – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-start-window.png)

1. Chcete-li přejít na ide sady Visual Studio, vyberte **pokračovat bez kódu.**

1. Na řádku nabídek Sady Visual Studio vyberte **Nástroje** > **získat nástroje a funkce,** chcete-li otevřít Instalační službu sady Visual Studio a zobrazit dialogové okno **Úpravy.**

   ![Karta Úlohy, dialogové okno Úpravy, Instalační služba Sady Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

1. Na kartě **Úlohy** v části **Windows**najděte vývoj plochy s úlohami **jazyka C++.** Pokud políčko vedle tohoto pracovního vytížení ještě není zaškrtnuté, vyberte ho.

1. Na kartě **Jednotlivé součásti** najděte zaškrtávací políčko **Správce balíčků Nuget.** Pokud toto políčko ještě není zaškrtnuté, vyberte ho.

1. Vyberte tlačítko v rohu **označeném** Zavřít nebo **Změnit**. (Název tlačítka se liší v závislosti na tom, zda jste pro instalaci vybrali nějaké funkce.) Pokud vyberete **Změnit**, začne instalace, což může chvíli trvat.

1. Zavřete Instalační program sady Visual Studio.

### <a name="create-the-project-and-select-the-target-architecture"></a>Vytvoření projektu a výběr cílové architektury

Dále vytvořte projekt:

1. Na řádku nabídek Sady Visual Studio zvolte **Soubor** > **nového** > **projektu,** **chcete-li** zobrazit okno Vytvořit nový projekt.

   ![Vytvoření nového projektu, C++ - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Najít a vybrat **konzolovou aplikaci**. Ujistěte se, že vyberete verzi c++ tohoto typu projektu (na rozdíl od Jazyka C# nebo Visual Basic).

1. Výběrem **možnosti Další** zobrazíte obrazovku **Konfigurovat nový projekt.**

   ![Konfigurace nového projektu, C++ - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-configure-your-new-project.png)

1. Do pole Název `helloworld` **projektu**zadejte .

1. V **části Umístění**přejděte na složku, do které chcete projekt uložit, a vytvořte ji.

Nyní vyberte architekturu cílové platformy. Na panelu nástrojů Sady Visual Studio vyhledejte rozevírací okno **Platformy řešení.** (Pokud ji nevidíte, zvolte **Zobrazit** > **panely nástrojů** > **Standard,** chcete-li zobrazit panel nástrojů obsahující **platformy řešení**.) Pokud používáte 64bitový Systém Windows, zvolte v rozevíracím poli **x64.** 64bitový systém Windows může také spouštět 32bitové aplikace, takže si můžete vybrat **x86,** pokud chcete.

### <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Nakonec nainstalujte [balíček Speech SDK NuGet](https://aka.ms/csspeech/nuget)a odkazujte na sadu Speech SDK v projektu:

1. V **Průzkumníku řešení**klikněte pravým tlačítkem myši na vaše řešení a zvolte **Spravovat balíčky NuGet pro řešení** a přejděte do okna **Nuget – řešení.**

1. Vyberte **Procházet**.

   ![NuGet – karta Řešení, Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. Ve **zdroji balíčků**zvolte **nuget.org**.

1. Do pole **Hledat** `Microsoft.CognitiveServices.Speech`zadejte a poté zvolte tento balíček, jakmile se zobrazí ve výsledcích hledání.

   ![Instalace balíčku Microsoft.CognitiveServices.Speech C++ - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

1. V podokně stavu balíčku vedle výsledků hledání vyberte projekt **Helloworld.**

1. Vyberte **Install** (Nainstalovat).

1. V dialogovém **okně Náhled změn** vyberte **OK**.

1. V dialogovém okně **Přijetí licence** zobrazte licenci a vyberte **Možnost Přijmout**. Instalace balíčku začíná a po dokončení instalace se v podokně **Výstup** zobrazí `Successfully installed 'Microsoft.CognitiveServices.Speech 1.11.0' to helloworld`zpráva podobná následujícímu textu: .
