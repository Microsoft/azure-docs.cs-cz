---
title: Kurz upgradu aplikace Service Fabric | Microsoft Docs
description: Tento článek vás provede možností nasazení aplikace Service Fabric, změna kódu a zavádění upgrade pomocí sady Visual Studio.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: a3181a7a-9ab1-4216-b07a-05b79bd826a4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 0a94dc91308f21d9e2b440f9638829ee03a8b903
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-application-upgrade-tutorial-using-visual-studio"></a>Kurz upgradu Service Fabric aplikace pomocí sady Visual Studio
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Azure Service Fabric zjednodušuje proces upgradu cloudovým aplikacím tím, že zajistí, že budou upgradovány pouze změněné služby a zda je monitorovaný stavu aplikací během celého procesu upgradu. Je také automaticky vrátí zpět aplikace pro předchozí verze při zjištění problémy. Upgrade aplikace Service Fabric je *nula. výpadků*, protože aplikace lze upgradovat bez výpadků. Tento kurz popisuje postup tvorby postupného upgradu ze sady Visual Studio.

## <a name="step-1-build-and-publish-the-visual-objects-sample"></a>Krok 1: Vytvoření a publikování ukázkové vizuální objekty
Nejprve stáhnout [vizuální objekty](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Actors/VisualObjects) aplikace z webu GitHub. Potom sestavení a publikování aplikace kliknutím pravým tlačítkem na projekt aplikace **VisualObjects**a výběr **publikovat** v Service Fabric položky nabídky.

![Kontextové nabídky pro aplikace Service Fabric][image1]

Výběr **publikovat** otevře místní okno, a můžete nastavit **cíle profil** k **PublishProfiles\Local.xml**. Okno by měl vypadat jako následující před kliknutím na **publikovat**.

![Publikování aplikace Service Fabric][image2]

Teď můžete kliknout na **publikovat** v dialogovém okně. Můžete použít [Service Fabric Explorer zobrazíte cluster a aplikace](service-fabric-visualizing-your-cluster.md). Vizuální objekty aplikace má webová služba, která můžete přejít na zadáním [ http://localhost:8081/visualobjects/ ](http://localhost:8081/visualobjects/) na panelu Adresa prohlížeče.  Měli byste vidět 10 plovoucí vizuální objekty manipulaci se na obrazovce.

**Poznámka:** Pokud nasazení `Cloud.xml` profilu (Azure Service Fabric), aplikace by měl být k dispozici na **http://{ServiceFabricName}. { Region}.cloudapp.Azure.com:8081/visualobjects/**. Zajistěte, aby byla `8081/TCP` nakonfigurované nástroji pro vyrovnávání zatížení (najít nástroje pro vyrovnávání zatížení ve stejné skupině prostředků jako Service Fabric instance).

## <a name="step-2-update-the-visual-objects-sample"></a>Krok 2: Aktualizace Ukázka vizuální objekty
Můžete si všimnout, že s verzí, který byl nasazen v kroku 1, není otočit vizuální objekty. Umožňuje upgradovat tuto aplikaci do jednoho kde také otočit vizuální objekty.

Vyberte projekt VisualObjects.ActorService v rámci řešení VisualObjects a otevřete **VisualObjectActor.cs** souboru. V rámci tohoto souboru, přejděte k metodě `MoveObject`, komentář `visualObject.Move(false)`a zrušte komentář u `visualObject.Move(true)`. Tato změna kódu otočí objekty po upgradu služby.  **Nyní můžete vytvořit (ne opětovné sestavení) řešení**, která sestavení upravené projekty. Pokud vyberete *znovu vytvořit všechny*, budete muset aktualizovat verze pro všechny projekty.

Také je potřeba verze aplikace. Změnit verzi, po kliknutí pravým tlačítkem na **VisualObjects** projektu sady Visual Studio můžete použít **upravit verze manifestu** možnost. Výběrem této možnosti se vyvolá dialogové okno pro edition verze následujícím způsobem:

![Dialogové okno Správa verzí][image3]

Verze pro upravené projekty a jejich kód balíčky, společně s aplikací na verzi 2.0.0 aktualizací. Po provedení změn v manifestu by měl vypadat takto (tučné části zobrazit změny):

![Aktualizace verze][image4]

Nástroje sady Visual Studio můžete provést automatické kumulativní verzí při výběru **automatické aktualizaci aplikace a verze aktualizace service**. Pokud používáte [SemVer](http://www.semver.org), budete muset aktualizovat kód nebo konfigurace balíčku verze samostatně, pokud tuto možnost.

Uložte změny a nyní zkontrolujte **upgradu aplikace** pole.

## <a name="step-3--upgrade-your-application"></a>Krok 3: Upgradu vaší aplikace
Seznamte se s [parametry upgradu aplikace](service-fabric-application-upgrade-parameters.md) a [procesu upgradu](service-fabric-application-upgrade.md) získat dostatečné povědomí o různé parametry upgradu, vypršení časových limitů a kritérium stavu, který lze použít. V tomto návodu kritéria hodnocení stavu služby nastavena na výchozí hodnoty (sledována režim). Tato nastavení můžete nakonfigurovat tak, že vyberete **nakonfigurovat nastavení upgradu** a poté upravíte parametry podle potřeby.

Nyní jsme jsou nastaveny na spuštění upgradu aplikace tak, že vyberete **publikovat**. Tato možnost provede upgrade aplikace na verzi 2.0.0, ve kterém otočit objekty. Service Fabric upgraduje jednu aktualizační doménu najednou (některé objekty se aktualizují jako první, za nímž následuje jiné) a službu zůstávají dostupné během upgradu. Přístup ke službě lze zkontrolovat prostřednictvím vašeho klienta (prohlížeč).  

Nyní, jako aplikace upgradu bude pokračovat, je možné jej monitorovat pomocí Service Fabric Exploreru, pomocí **upgrady v průběhu** kartě v rámci aplikace.

Za několik minut všechny domény aktualizace je potřeba upgradovat (Dokončit) a okně výstupu sady Visual Studio by také stavu dokončení upgradu. A by měl zjistit, který *všechny* vizuální objekty v okně prohlížeče jsou nyní otáčení!

Můžete chtít zkuste změnit verze a přesun z verze 2.0.0 na verzi 3.0.0 jako cvičení, nebo dokonce z verze 2.0.0 zpět na verzi 1.0.0. Přehrání s vypršení časových limitů a zásad stavu proveďte vlastní obeznámeni s nimi. Při nasazení v clusteru služby Azure a místní cluster, použité parametry pravděpodobně nutné lišit. Doporučujeme vám, můžete nastavit vypršení časových limitů.

## <a name="next-steps"></a>Další postup
[Upgrade vaší aplikace pomocí prostředí PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) vás provede upgrade aplikace pomocí prostředí PowerShell.

Řídí, jak je upgrade vaší aplikace pomocí [upgrade parametry](service-fabric-application-upgrade-parameters.md).

Zkontrolujte upgradů aplikace kompatibilní podle naučit se používat [serializace dat](service-fabric-application-upgrade-data-serialization.md).

Další informace o použití pokročilých funkcí při upgradu vaší aplikace tím, že odkazuje na [Advanced témata](service-fabric-application-upgrade-advanced.md).

Řešení běžných potíží v upgradů aplikací podle kroků v části [řešení potíží s aplikací upgrady](service-fabric-application-upgrade-troubleshooting.md).

[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png
