---
title: Sestavení z příkazového řádku Azure | Dokumentace Microsoftu
description: Sestavení z příkazového řádku Azure
services: visual-studio-online
author: ghogen
manager: douge
assetId: 94b35d0d-0d35-48b6-b48b-3641377867fd
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/05/2017
ms.author: ghogen
ms.openlocfilehash: c47da12feeef2a1536cdbfbe0187fe8991b3257d
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42055448"
---
# <a name="building-azure-projects-from-the-command-line"></a>Sestavování projektů Azure z příkazového řádku
Microsoft Build Engine (MSBuild) můžete vytvářet produkty v prostředích sestavení testovacího prostředí, kde není nainstalovaná sada Visual Studio. Nástroj MSBuild používá formát XML pro soubory projektu, které je rozšiřitelný a plně podporovaný microsoftem. Pomocí nástroje MSBuild formát souboru, můžete popsat, co položky musí být vytvořená pro jeden nebo více platformách a konfiguracích.

Můžete také spustit nástroj MSBuild na příkazovém řádku a v tomto tématu popisuje tento přístup. Nastavením vlastnosti na příkazovém řádku můžete vytvářet konkrétní konfigurace projektu. Podobně můžete také definovat cíle, které sestavení nástroje MSBuild. Další informace o parametrech příkazového řádku a nástroje MSBuild naleznete v tématu [MSBuild Reference k příkazovému řádku](https://msdn.microsoft.com/library/ms164311.aspx).

## <a name="msbuild-parameters"></a>Parametry nástroje MSBuild
Chcete-li spustit nástroj MSBuild s je nejjednodušší způsob, jak vytvořit balíček `/t:Publish` možnost. Ve výchozím nastavení, tento příkaz vytvoří adresář ve vztahu ke kořenové složce projektu, jako například `<ProjectDirectory>\bin\Configuration\app.publish\`. Když vytvoříte projekt Azure, jsou generovány dva soubory: balíček samotný soubor a související konfigurační soubor:

* Soubor balíčku (`project.cspkg`)
* Konfigurační soubor (`ServiceConfiguration.TargetProfile.cscfg`)

Ve výchozím nastavení obsahuje každý projekt Azure jeden soubor konfigurace služby pro místní sestavení (ladění) a druhý pro sestavení můžou v cloudu (přípravné nebo produkční). Můžete však přidat nebo odebrat soubory konfigurace služby podle potřeby. Při vytváření balíčku v rámci sady Visual Studio, zobrazí se výzva který soubor konfigurace služby zahrnout spolu s balíček. Při vytváření balíčku pomocí nástroje MSBuild je standardní součástí místní služby konfigurační soubor. Chcete-li zahrnout jiný soubor konfigurace služby, nastavte `TargetProfile` vlastnost příkaz MSBuild (`MSBuild /t:Publish /p:TargetProfile=ProfileName`).

Pokud chcete použít jiný adresář pro uložené balíček a konfigurační soubory, nastavte cestu s použitím `/p:PublishDir=Directory\` možností, včetně koncové oddělovače zpětné lomítko.

## <a name="next-steps"></a>Další postup
Jakmile je balíček vytvořen, můžete ji nasadit do Azure.