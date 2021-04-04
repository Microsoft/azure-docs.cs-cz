---
title: Kurz upgradu aplikace Service Fabric
description: Tento článek vás provede nasazením aplikace Service Fabric, změnou kódu a zavedením upgradu pomocí sady Visual Studio.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: acde2f4e51bee29d2eefb0d5fbb54fbe421a41f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996232"
---
# <a name="service-fabric-application-upgrade-tutorial-using-visual-studio"></a>Service Fabric kurz upgradu aplikací pomocí sady Visual Studio
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Azure Service Fabric zjednodušuje proces upgradu cloudových aplikací tím, že zajišťuje, že se upgradují jenom změněné služby a že stav aplikace se monitoruje během procesu upgradu. Při výskytu problémů se také automaticky vrátí aplikace na předchozí verzi. Service Fabric upgrady aplikací nejsou žádné *výpadky*, protože aplikaci lze upgradovat bez výpadků. Tento kurz popisuje, jak dokončit postupný upgrade ze sady Visual Studio.

## <a name="step-1-build-and-publish-the-visual-objects-sample"></a>Krok 1: sestavování a publikování ukázky vizuálních objektů
Nejdřív Stáhněte aplikaci [Visual Objects](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Actors/VisualObjects) z GitHubu. Poté sestavte a publikujte aplikaci kliknutím pravým tlačítkem na projekt aplikace, **VisualObjects** a výběrem příkazu **publikovat** v položce nabídky Service Fabric.

![Místní nabídka pro aplikaci Service Fabric][image1]

Když vyberete **publikovat** , zobrazí se místní nabídka a můžete nastavit **cílový profil** na **PublishProfiles\Local.xml**. Před kliknutím na tlačítko **publikovat** by okno mělo vypadat takto.

![Publikování aplikace Service Fabric][image2]

Nyní můžete v dialogovém okně kliknout na **publikovat** . [K zobrazení clusteru a aplikace](service-fabric-visualizing-your-cluster.md)můžete použít Service Fabric Explorer. Aplikace Visual Objects obsahuje webovou službu, na kterou můžete přejít zadáním `http://localhost:8081/visualobjects/` do adresního řádku v prohlížeči.  Mělo by se zobrazit 10 plovoucích vizuálních objektů, které se pohybují na obrazovce.

**Poznámka:** Pokud se nasazuje do `Cloud.xml` profilu (Azure Service Fabric), měla by být aplikace dostupná na adrese **http://{ServiceFabricName}. { Region}. cloudapp. Azure. com: 8081/visualobjects/**. Ujistěte se, že jste `8081/TCP` nakonfigurovali v Load Balancer (najděte Load Balancer ve stejné skupině prostředků jako instance Service Fabric).

## <a name="step-2-update-the-visual-objects-sample"></a>Krok 2: aktualizace ukázky vizuálních objektů
Můžete si všimnout, že ve verzi, která byla nasazena v kroku 1, se neotáčí vizuální objekty. Pojďme tuto aplikaci upgradovat na jednu, kde se také otočí vizuální objekty.

V řešení VisualObjects vyberte projekt VisualObjects. ActorService a otevřete soubor **VisualObjectActor. cs** . V tomto souboru přejdete do metody `MoveObject` , nakomentovat `visualObject.Move(false)` a odkomentujte `visualObject.Move(true)` . Tato změna kódu otočí objekty po upgradu služby.  **Nyní můžete sestavit (ne znovu sestavit) řešení**, které vytvoří upravené projekty. Pokud vyberete možnost *znovu sestavit vše*, je nutné aktualizovat verze pro všechny projekty.

Musíme také poznáte verzi naší aplikace. Chcete-li provést změny verze po kliknutí pravým tlačítkem na projekt **VisualObjects** , můžete použít možnost **Upravit verze manifestu** aplikace Visual Studio. Když vyberete tuto možnost, zobrazí se dialogové okno pro verze edice následujícím způsobem:

![Správa verzí – dialogové okno][image3]

Aktualizujte verze upravených projektů a jejich balíčků kódu společně s aplikací na verzi 2.0.0. Po provedení změn by měl manifest vypadat jako následující (tučné části zobrazují změny):

![Aktualizace verzí][image4]

Nástroje sady Visual Studio mohou provádět automatické souhrny verzí při výběru **automaticky aktualizovat verze aplikací a služeb**. Použijete-li [SemVer](http://www.semver.org), je nutné aktualizovat pouze verzi balíčku Code nebo Configuration, pokud je tato možnost vybrána.

Uložte změny a teď zaškrtněte políčko **upgradovat aplikaci** .

## <a name="step-3--upgrade-your-application"></a>Krok 3: upgrade aplikace
Seznamte se s [parametry upgradu aplikace](service-fabric-application-upgrade-parameters.md) a [procesem upgradu](service-fabric-application-upgrade.md) , abyste získali dobré znalosti o různých parametrech upgradu, časových limitech a kritériu stavu, které lze použít. V tomto návodu je kritérium vyhodnocení stavu služby nastavené na výchozí (nemonitorovaný režim). Tato nastavení můžete nakonfigurovat výběrem možnosti **Konfigurovat nastavení upgradu** a úpravou parametrů podle potřeby.

Nyní je vše nastaveno na spuštění upgradu aplikace výběrem možnosti **publikovat**. Tato možnost upgraduje aplikaci na verzi 2.0.0, ve které se objekty otáčí. Service Fabric upgraduje jednu aktualizační doménu současně (některé objekty se aktualizují jako první, za kterými následuje ostatní) a služba zůstane během upgradu dostupná. Přístup ke službě se dá zkontrolovat přes klienta (prohlížeč).  

Teď, když upgrade aplikace pokračuje, můžete ho monitorovat pomocí Service Fabric Explorer, a to pomocí karty **probíhající upgrady** v části aplikace.

Během několika minut by měly být upgradovány všechny aktualizační domény (dokončeny) a okno výstup sady Visual Studio by mělo také mít za následek dokončení upgradu. A měli byste najít, že *všechny* vizuální objekty v okně prohlížeče jsou teď rotující!

Možná budete chtít zkusit změnit verze a přesunout se z verze 2.0.0 na verzi 3.0.0 jako cvičení nebo dokonce z verze 2.0.0 zpátky na verzi 1.0.0. Můžete hrát s časovými limity a zásadami stavu, abyste s nimi mohli s nimi dobře pracovat. Při nasazování do clusteru Azure na rozdíl od místního clusteru se můžou použité parametry lišit. Doporučujeme, abyste nastavili časový limit uvážlivě.

## <a name="next-steps"></a>Další kroky
[Upgrade aplikace pomocí PowerShellu](service-fabric-application-upgrade-tutorial-powershell.md) vás provede upgradem aplikace pomocí PowerShellu.

Pomocí [parametrů upgradu](service-fabric-application-upgrade-parameters.md)lze řídit, jak je aplikace upgradována.

Vyjistěte, aby byly upgrady aplikací kompatibilní, pomocí učení, jak používat [serializaci dat](service-fabric-application-upgrade-data-serialization.md).

Naučte se používat pokročilé funkce při upgradu vaší aplikace, které odkazují na [Pokročilá témata](service-fabric-application-upgrade-advanced.md).

Pomocí postupu v části [řešení potíží s upgrady aplikací](service-fabric-application-upgrade-troubleshooting.md)můžete opravit běžné problémy s upgrady aplikací.

[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png
