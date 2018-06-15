---
title: Sestavení příkazového řádku Azure | Microsoft Docs
description: Sestavení příkazového řádku pro Azure.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 94b35d0d-0d35-48b6-b48b-3641377867fd
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/05/2017
ms.author: ghogen
ms.openlocfilehash: 7d0138abb07aea46ad8d0069c87964b393347dcf
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
ms.locfileid: "31791422"
---
# <a name="building-azure-projects-from-the-command-line"></a>Sestavení projektů Azure z příkazového řádku
Microsoft Build Engine (MSBuild) můžete vytvořit produkty v sestavení testovacích prostředích, kde není nainstalovaná sada Visual Studio. MSBuild používá formátu XML pro soubory projektu, které je rozšiřitelný a plně podporované společností Microsoft. Pomocí nástroje MSBuild formát souboru, můžete popisují, co položky musí být vytvořené pro jednu nebo více platforem a konfigurace.

Můžete také spouštět nástroje MSBuild v příkazovém řádku a toto téma popisuje tento přístup. Nastavením vlastnosti na příkazovém řádku, můžete vytvořit konkrétní konfigurace projektu. Podobně můžete také definovat cíle, které MSBuild sestavení. Další informace o parametry příkazového řádku a nástroje MSBuild najdete v tématu [Reference k příkazovému řádku MSBuild](https://msdn.microsoft.com/library/ms164311.aspx).

## <a name="msbuild-parameters"></a>Parametry nástroje MSBuild
Nejjednodušší způsob, jak vytvořit balíček je spuštění MSBuild s parametrem `/t:Publish` možnost. Ve výchozím nastavení, tento příkaz vytvoří adresář ve vztahu k kořenové složce projektu, například `<ProjectDirectory>\bin\Configuration\app.publish\`. Při sestavování projektu Azure jsou generovány dva soubory: soubor balíčku samostatně a doprovodné konfigurační soubor:

* Soubor balíčku (`project.cspkg`)
* Konfigurační soubor (`ServiceConfiguration.TargetProfile.cscfg`)

Každý projekt, Azure ve výchozím nastavení, obsahuje jeden soubor konfigurace služby pro místní sestavení (ladění) a druhou pro sestavení cloudu (pracovním nebo produkčním). Můžete ale přidat nebo odebrat soubory konfigurace služby podle potřeby. Když vytvoříte balíček Visual Studia, zobrazí se výzva které souboru konfigurace služby společně se balíček. Když vytvoříte balíček pomocí nástroje MSBuild, místní služby konfigurační soubor je zahrnuté ve výchozím nastavení. Chcete-li zahrnout jiný soubor konfigurace služby, nastavte `TargetProfile` vlastnosti nástroje MSBuild příkazu (`MSBuild /t:Publish /p:TargetProfile=ProfileName`).

Pokud chcete používat adresář alternativní uložené balíčku a konfigurační soubory, nastavte cestu pomocí `/p:PublishDir=Directory\` možnost, včetně koncové oddělovače zpětné lomítko.

## <a name="next-steps"></a>Další postup
Jakmile je balíček vytvořen, můžete ho nasadit do Azure.