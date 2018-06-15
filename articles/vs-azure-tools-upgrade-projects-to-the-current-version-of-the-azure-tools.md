---
title: Postup upgradu projektů na aktuální verze nástroje Azure | Microsoft Docs
description: Zjistěte, jak provést upgrade projektu Azure v sadě Visual Studio na aktuální verzi nástroje Azure
services: visual-studio-online
author: ghogen
manager: douge
assetId: 1d64070a-078d-468a-87f4-e6715de6475f
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/18/2016
ms.author: ghogen
ms.openlocfilehash: 738c9d12beed6ba73872d520cb16bd3a6c813a35
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
ms.locfileid: "31791388"
---
# <a name="how-to-upgrade-projects-to-the-current-version-of-the-azure-tools-for-visual-studio"></a>Postup upgradu projektů na aktuální verze nástroje Azure pro sadu Visual Studio
## <a name="overview"></a>Přehled
Po instalaci aktuální verze nástroje Azure (nebo předchozí verze, která je novější než 1.6), všechny projekty, které byly vytvořeny pomocí nástroje Azure verzi před 1.6 (Listopad 2011) budou automaticky aktualizovány při jejich otevření. Pokud jste vytvořili projektů pomocí 1.6 (Listopad 2011) verzi těchto nástrojů a stále máte tuto verzi nainstalovat, můžete otevřít ve starší verzi tyto projekty a rozhodnout později jestli se má provést aktualizaci.

## <a name="how-your-project-changes-when-you-upgrade-it"></a>Jak projektu změní, když ho upgradovat
Pokud je automaticky upgradován na projekt nebo jste zadali, chcete-li ho upgradovat, úpravě projektu pro práci s aktuálními verzemi určité sestavení a některé vlastnosti jsou také změnit, protože tato část popisuje. Pokud váš projekt vyžaduje další změny, aby byl kompatibilní s novější verzí nástroje, je nutné ručně provést tyto změny.

* Chcete-li novější verzi Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitoirTraceListener.dll jsou aktualizovány souboru web.config pro webové role a souboru app.config u rolí pracovního procesu.
* Sestavení Microsoft.WindowsAzure.StorageClient.dll, Microsoft.WindowsAzure.Diagnostics.dll a Microsoft.WindowsAzure.ServiceRuntime.dll upgradují na nové verze.
* Profily publikování, které byly uloženy v souboru projektu Azure (.ccproj) přesunou do samostatného souboru s příponou .azurePubXml, v **publikovat** podadresáři.
* Pro podporu nové a změněné funkce jsou aktualizovány některé vlastnosti v profilu publikování. **AllowUpgrade** je nahrazena **DeploymentReplacementMethod** protože nasazené cloudovou službu můžete aktualizovat současně nebo postupně.
* Vlastnost **UseIISExpressByDefault** přidat a nastavit na hodnotu false, aby webový server, který se používá pro ladění se automaticky nezmění z Internetové informační služby (IIS) do služby IIS Express. Služba IIS Express je výchozí webový server pro projekty, které jsou vytvořeny pomocí novější verze nástroje.
* Pokud je ukládání do mezipaměti Azure hostovaná v jednom nebo více rolí vašeho projektu, se některé vlastnosti v konfiguraci služby (soubor .cscfg) a definice služby (soubor .csdef) změnit při upgradu na projekt. Pokud projekt používá balíček NuGet ukládání do mezipaměti Azure, je projekt upgradován na nejnovější verzi balíčku. Měli byste otevřete soubor web.config a ověřit, že byla správně zachová konfiguraci klienta během procesu upgradu. Pokud jste přidali odkazy na sestavení klienta ukládání do mezipaměti Azure bez použití balíčku NuGet, nebude aktualizovat tyto sestavení; je nutné ručně aktualizovat tyto odkazy na nové verze.

> [!IMPORTANT]
> Pro F # projekty musíte ručně aktualizovat odkazy na sestavení Azure tak, aby se odkazovat novější verze těchto sestavení.
> 
> 

### <a name="how-to-upgrade-an-azure-project-to-the-current-release"></a>Postup upgradu projektu Azure na aktuální verzi
1. Instalace aktuální verze nástroje Azure do instalace sady Visual Studio, který chcete použít pro upgradovaném projektu a pak otevřete projekt, který chcete upgradovat. Pokud projekt byla vytvořena pomocí nástroje Azure verzi před 1.6 (Listopad 2011), projekt je automaticky upgradován na aktuální verzi. Vytvoření projektu s Listopad 2011 verze a této verze je stále nainstalován, projekt se otevře v této verzi.
2. V Průzkumníku řešení otevřete místní nabídce uzlu projektu, zvolte **vlastnosti**a potom zvolte **aplikace** kartě zobrazeném dialogu.
   
    **Aplikace** kartě se zobrazují verze nástrojů, který je přidružený k projektu. Pokud se zobrazí aktuální verze nástroje Azure, projekt již byla upgradována. Pokud jste nainstalovali novější verzi nástroje, než jaké kartě se zobrazují, **Upgrade** se zobrazí tlačítko.
3. Vyberte **Upgrade** tlačítko Aktualizovat na aktuální verzi nástroje pro projekt.
4. Sestavte projekt a potom vyřešte všechny chyby, které jsou výsledkem změny rozhraní API. Informace o tom, jak upravit kód pro novou verzi najdete v dokumentaci pro konkrétní rozhraní API.

