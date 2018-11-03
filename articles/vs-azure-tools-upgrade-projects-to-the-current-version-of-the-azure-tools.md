---
title: Postup při upgradu projektů na aktuální verzi nástroje Azure | Dokumentace Microsoftu
description: Zjistěte, jak upgradovat projekt Azure v sadě Visual Studio na aktuální verzi nástroje Azure
services: visual-studio-online
author: ghogen
manager: douge
assetId: 1d64070a-078d-468a-87f4-e6715de6475f
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/18/2016
ms.author: ghogen
ms.openlocfilehash: a8b62bd261fb2b39f3e16549d2b531e442dc7b38
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969318"
---
# <a name="how-to-upgrade-projects-to-the-current-version-of-the-azure-tools-for-visual-studio"></a>Postup při upgradu projektů na aktuální verzi nástroje Azure pro sadu Visual Studio
## <a name="overview"></a>Přehled
Po instalaci aktuální verze sady nástrojů Azure (nebo předchozí verze, která je novější než 1.6) verze všechny projekty, které byly vytvořeny pomocí nástroje Azure před 1.6 (Listopad 2011) se automaticky upgraduje poté, co je otevřete. Pokud jste vytvořili projekty pomocí 1.6 (Listopad 2011) verzi těchto nástrojů a ještě tuto verzi nainstalovat, můžete tyto projekty otevřít ve starší verzi a můžete rozhodnout později, jestli se má provést upgrade.

## <a name="how-your-project-changes-when-you-upgrade-it"></a>Jak projektu změní, když ho upgradovat
Pokud dojde k automatickému upgradu projektu nebo určíte, že chcete ho upgradovat, váš projekt je upravit tak, aby pracovat s aktuálními verzemi určité sestavení a některé vlastnosti jsou také změnit, protože tato část popisuje. Pokud váš projekt vyžaduje jiné změny, aby byly kompatibilní s touto novou verzí nástroje, musí tyto změny provést ručně.

* Soubor web.config pro webové role a souboru app.config u rolí pracovního procesu jsou aktualizovány tak, aby odkazovaly novější verzi Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitoirTraceListener.dll.
* Sestavení Microsoft.WindowsAzure.StorageClient.dll Microsoft.WindowsAzure.Diagnostics.dll a Microsoft.WindowsAzure.ServiceRuntime.dll upgradují na novou verzi.
* Profily publikování, které byly uloženy v souboru projektu Azure (ccproj) přesunou do samostatného souboru, s .azurePubXml rozšíření v **publikovat** podadresáře.
* Některé vlastnosti v profilu publikování jsou aktualizována o podporu nové a změněné funkce. **AllowUpgrade** nahrazuje **DeploymentReplacementMethod** protože nasazené cloudové službě můžete současně nebo přírůstkově aktualizovat.
* Vlastnost **UseIISExpressByDefault** přidán a nastavit na hodnotu false, aby webový server, který slouží k ladění se nezmění automaticky z Internetové informační služby (IIS) ve službě IIS Express. Služba IIS Express je výchozí webový server pro projekty, které jsou vytvořeny pomocí novější verze nástrojů.
* Pokud ukládání do mezipaměti Azure je hostované v jedné nebo více rolí váš projekt, se některé vlastnosti v konfiguraci služby (soubor .cscfg) a definice služby (soubor .csdef) změnit při upgradu projektu. Pokud projekt používá balíček NuGet pro ukládání do mezipaměti Azure, projekt upgradovat na nejnovější verzi balíčku. By měla otevřít soubor web.config a ověřte, že byla správně Udržovat konfiguraci klienta během procesu upgradu. Pokud jste přidali odkazy na sestavení klienta ukládání do mezipaměti Azure bez použití balíčku NuGet, neaktualizuje se tato sestavení; je nutné ručně aktualizovat tyto odkazy na nové verze.

> [!IMPORTANT]
> Pro projekty F # je nutné ručně aktualizovat odkazy na sestavení v Azure, takže odkazují novější verze tato sestavení.
> 
> 

### <a name="how-to-upgrade-an-azure-project-to-the-current-release"></a>Jak upgradovat projekt Azure na aktuální verzi
1. Nainstalujte aktuální verzi nástroje Azure do instalace sady Visual Studio, kterou chcete použít pro aktualizovaný projekt a pak otevřete projekt, který chcete upgradovat. Pokud byl projekt vytvořen pomocí nástroje Azure verze před 1.6 (Listopad 2011), projekt se automaticky upgradovány na aktuální verzi. Pokud byl projekt vytvořen Listopad 2011 verze a tuto verzi je nainstalovaná, projekt se otevře v této verzi.
2. V Průzkumníku řešení otevřete místní nabídku pro uzel projektu, zvolte **vlastnosti**a klikněte na tlačítko **aplikace** dialogovém okně, které se zobrazí na kartě.
   
    **Aplikace** karta zobrazuje verze nástrojů, který je spojen s projektem. Pokud se zobrazí aktuální verze nástrojů Azure, projekt již byla upgradována. Pokud si nainstalujete novější verzi nástroje, než jaké kartě se zobrazí, **upgradovat** se zobrazí tlačítko.
3. Zvolte **upgradovat** tlačítko Upgradovat projekt na aktuální verzi nástroje.
4. Sestavte projekt a pak řešit všechny chyby, které jsou výsledkem změny rozhraní API. Informace o tom, jak upravit kód pro novou verzi naleznete v dokumentaci pro konkrétní rozhraní API.

