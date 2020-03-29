---
title: Kurz upgradu aplikace Service Fabric
description: Tento článek vás provede prostředím nasazení aplikace Service Fabric, změny kódu a zavedení upgradu pomocí sady Visual Studio.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: db814b972db1aee56be0858c9ff5d1c382640642
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464818"
---
# <a name="service-fabric-application-upgrade-tutorial-using-visual-studio"></a>Kurz upgradu aplikace Service Fabric pomocí sady Visual Studio
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Azure Service Fabric zjednodušuje proces upgradu cloudových aplikací tím, že zajišťuje, že jsou upgradovány pouze změněné služby a že stav aplikace je sledován v průběhu celého procesu upgradu. Také automaticky vrátí aplikaci na předchozí verzi při výskytu problémů. Upgrady aplikací Service Fabric jsou *nulové prostoje*, protože aplikace může být upgradována bez prostojů. Tento kurz popisuje, jak dokončit postupnou inovaci z visual studia.

## <a name="step-1-build-and-publish-the-visual-objects-sample"></a>Krok 1: Sestavení a publikování ukázky vizuálních objektů
Nejprve stáhněte aplikaci [Visual Objects](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Actors/VisualObjects) z GitHubu. Potom vytvořte a publikujte aplikaci kliknutím pravým tlačítkem myši na projekt aplikace **VisualObjects**a výběrem příkazu **Publikovat** v položce nabídky Service Fabric.

![Místní nabídka pro aplikaci Service Fabric][image1]

Výběrem **možnosti Publikovat** vyvoláte vyskakovací okno a můžete nastavit **cílový profil** na **PublishProfiles\Local.xml**. Okno by mělo vypadat takto, než klepnete na **tlačítko Publikovat**.

![Publikování aplikace Service Fabric][image2]

Nyní můžete v dialogovém okně kliknout na **Publikovat.** Pomocí aplikace Service Fabric Explorer můžete [zobrazit cluster a aplikaci](service-fabric-visualizing-your-cluster.md). Aplikace Visual Objects má webovou službu, na [http://localhost:8081/visualobjects/](http://localhost:8081/visualobjects/) kterou můžete přejít zadáním do adresního řádku prohlížeče.  Měli byste vidět 10 plovoucí vizuální objekty pohybující se na obrazovce.

**POZNÁMKA:** Pokud se `Cloud.xml` nasazujete do profilu (Azure Service Fabric), aplikace by pak měla být k dispozici na **adrese http://{ServiceFabricName}.{ Region}.cloudapp.azure.com:8081/visualobjects/**. Ujistěte se, `8081/TCP` že máte nakonfigurované v balancer (najít vyrovnávání zatížení ve stejné skupině prostředků jako service fabric instance).

## <a name="step-2-update-the-visual-objects-sample"></a>Krok 2: Aktualizace ukázky vizuálních objektů
Můžete si všimnout, že s verzí, která byla nasazena v kroku 1, vizuální objekty neotáčejí. Pojďme upgradovat tuto aplikaci na ten, kde vizuální objekty také otáčet.

Vyberte projekt VisualObjects.ActorService v rámci řešení VisualObjects a otevřete **soubor VisualObjectActor.cs.** V rámci tohoto souboru `MoveObject`přejděte `visualObject.Move(false)`na metodu , komentář a odkomentujte `visualObject.Move(true)`. Tato změna kódu otočí objekty po upgradu služby.  **Nyní můžete vytvořit (ne znovu sestavit) řešení**, který vytváří upravené projekty. Pokud vyberete *znovu sestavit vše*, budete muset aktualizovat verze pro všechny projekty.

Musíme také verzi naší aplikace. Chcete-li provést změny verze po kliknutí pravým tlačítkem myši na projekt **VisualObjects,** můžete použít možnost **Upravit verze manifestu** sady Visual Studio. Výběrem této volby se zobrazí dialogové okno pro verze edice následujícím způsobem:

![Dialogové okno Správa verzí][image3]

Aktualizujte verze pro upravené projekty a jejich balíčky kódu spolu s aplikací na verzi 2.0.0. Po provedení změn by měl manifest vypadat takto (tučné části zobrazují změny):

![Aktualizace verzí][image4]

Nástroje sady Visual Studio mohou provést automatické souhrny verzí po výběru **možnosti Automaticky aktualizovat verze aplikací a služeb**. Pokud používáte [SemVer](http://www.semver.org), je třeba aktualizovat kód a/nebo verzi balíčku konfigurace samostatně, pokud je tato možnost vybrána.

Uložte změny a teď zaškrtněte **políčko Upgradovat aplikaci.**

## <a name="step-3--upgrade-your-application"></a>Krok 3: Upgrade aplikace
Seznamte se s [parametry upgradu aplikace](service-fabric-application-upgrade-parameters.md) a [proces upgradu](service-fabric-application-upgrade.md) získat dobré znalosti o různých parametrech upgradu, časové rozlišení a kritérium stavu, které lze použít. V tomto návodu je kritérium vyhodnocení stavu služby nastaveno na výchozí (nesledovaný režim). Tato nastavení můžete nakonfigurovat tak, že vyberete **Konfigurovat nastavení upgradu** a pak podle potřeby změníte parametry.

Nyní jsme všichni nastaveni na spuštění upgradu aplikace výběrem **publikovat**. Tato možnost inovuje aplikaci na verzi 2.0.0, ve které se objekty otáčejí. Service Fabric upgraduje jednu aktualizační doménu najednou (některé objekty jsou aktualizovány jako první, následuje jiné) a služba zůstane přístupná během upgradu. Přístup ke službě lze zkontrolovat prostřednictvím vašeho klienta (prohlížeče).  

Nyní, jak pokračuje upgrade aplikace, můžete sledovat pomocí Service Fabric Explorer, pomocí **upgrady v průběhu** karty pod aplikacemi.

Během několika minut by měly být upgradovány všechny aktualizační domény (dokončeno) a ve výstupním okně sady Visual Studio by také mělo být uvedeno, že upgrade je dokončen. A měli byste zjistit, že *všechny* vizuální objekty v okně prohlížeče jsou nyní rotující!

Můžete zkusit změnit verze a přechod z verze 2.0.0 na verzi 3.0.0 jako cvičení, nebo dokonce z verze 2.0.0 zpět na verzi 1.0.0. Hrajte si s časovými výčasy a zásadami stavu, abyste se s nimi seznámili. Při nasazování do clusteru Azure na rozdíl od místního clusteru, použité parametry se mohou lišit. Doporučujeme nastavit časové osy konzervativně.

## <a name="next-steps"></a>Další kroky
[Upgrade aplikace pomocí PowerShellu](service-fabric-application-upgrade-tutorial-powershell.md) vás provede upgradem aplikace pomocí PowerShellu.

Pomocí [parametrů upgradu](service-fabric-application-upgrade-parameters.md)můžete určit, jak bude aplikace upgradována.

Pomocí aplikace upgrady kompatibilní tím, že se naučíte používat [serializaci dat](service-fabric-application-upgrade-data-serialization.md).

Naučte se používat pokročilé funkce při inovaci aplikace odkazem na [rozšířená témata](service-fabric-application-upgrade-advanced.md).

Běžné problémy při upgradu aplikací opravíte odkazem na kroky [při řešení potíží s inovacemi aplikací](service-fabric-application-upgrade-troubleshooting.md).

[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png
