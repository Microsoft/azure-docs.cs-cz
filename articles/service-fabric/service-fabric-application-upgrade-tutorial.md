---
title: Kurz upgradu aplikací Service Fabric | Dokumentace Microsoftu
description: Tento článek vás provedl možnostmi, které nasazení aplikace Service Fabric, změny kódu a zavádí upgrade pomocí sady Visual Studio.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: a3181a7a-9ab1-4216-b07a-05b79bd826a4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 8fe0bf9c8827b7248195f89377176fd834845e32
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663668"
---
# <a name="service-fabric-application-upgrade-tutorial-using-visual-studio"></a>Kurz upgradu Service Fabric aplikace pomocí sady Visual Studio
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Azure Service Fabric zjednodušuje proces upgradu cloudové aplikace díky zajištění, že budou upgradovány pouze změněné služby a, že se v rámci procesu upgradu monitoruje stav aplikací. Je také automaticky vrátí zpět aplikaci na předchozí verzi při zjištění problémů. Upgrady aplikací Service Fabric se *nulové výpadky*, protože je možné upgradovat aplikace bez výpadků. Tento kurz se zaměřuje na dokončení upgradu se zajištěním provozu ze sady Visual Studio.

## <a name="step-1-build-and-publish-the-visual-objects-sample"></a>Krok 1: Vytvářejte a publikujte ukázka vizuálních objektů
Nejdřív stáhněte [vizuální objekty](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Actors/VisualObjects) aplikace z Githubu. Potom sestavení a publikování aplikace kliknutím pravým tlačítkem na projekt aplikace **VisualObjects**a výběr **publikovat** v položce nabídky Service Fabric.

![Místní nabídka pro aplikace Service Fabric][image1]

Výběr **publikovat** zobrazí místní okno, a můžete nastavit **cílový profil** k **PublishProfiles\Local.xml**. V okně by měl vypadat nějak takto před kliknutím na **publikovat**.

![Publikování aplikace Service Fabric][image2]

Nyní můžete kliknout na **publikovat** v dialogovém okně. Můžete použít [Service Fabric Explorer zobrazíte cluster a aplikace](service-fabric-visualizing-your-cluster.md). Vizuální objekty aplikace má webovou službu, můžete přejít na zadáním [ http://localhost:8081/visualobjects/ ](http://localhost:8081/visualobjects/) do adresního řádku prohlížeče.  Měli byste vidět 10 s plovoucí desetinnou čárkou vizuální objekty přesouvat na obrazovce.

**POZNÁMKA:** Pokud nasazení `Cloud.xml` profilu (Azure Service Fabric), aplikace pak měla být k dispozici na **http://{ServiceFabricName}. { Region}.cloudapp.Azure.com:8081/visualobjects/**. Ujistěte se, že máte `8081/TCP` nakonfigurovaná v nástroji pro vyrovnávání zatížení (najít nástroj pro vyrovnávání zatížení ve stejné skupině prostředků jako instance Service Fabric).

## <a name="step-2-update-the-visual-objects-sample"></a>Krok 2: Ukázku aktualizovat, a vizuálních objektů
Můžete si všimnout, že s verzí, který je nasazený v kroku 1, ne otočit vizuální objekty. Umožňuje upgradovat tuto aplikaci do jednoho kde také otočit vizuální objekty.

Vyberte projekt VisualObjects.ActorService v rámci řešení VisualObjects a otevřete **VisualObjectActor.cs** souboru. V rámci tohoto souboru, přejděte k metodě `MoveObject`, okomentujte `visualObject.Move(false)`a zrušte komentář u `visualObject.Move(true)`. Tato změna kódu otočí objekty po upgradu služby.  **(Sestavení) můžete vytvořit řešení**, která sestavení upravené projekty. Pokud vyberete *sestavit vše znovu*, budete muset aktualizovat verze pro všechny projekty.

Musíme také verze našich aplikací. Změnit verzi, po kliknutí pravým tlačítkem na **VisualObjects** projektu, můžete použít Visual Studio **upravit verze manifestu** možnost. Výběrem této možnosti se vyvolá dialogové okno pro vydání verze následujícím způsobem:

![Dialogové okno správy verzí][image3]

Aktualizace verze pro upravené projekty a jejich balíčky kódu, společně s aplikací na verzi 2.0.0. Po provedení změny v manifestu by měl vypadat nějak takto (tučné části se změny zobrazily):

![Aktualizace verze][image4]

Nástroje sady Visual Studio můžete provést automatické kumulativní verzí při výběru **automaticky aktualizovat verze aplikací a služeb**. Pokud používáte [SemVer](http://www.semver.org), je potřeba aktualizovat kód a/nebo je vybraná samostatně, pokud tuto možnost konfigurace balíčku verze.

Uložte změny a teď zkontrolujte, **upgradovat aplikaci** pole.

## <a name="step-3--upgrade-your-application"></a>Krok 3:  Upgrade aplikace
Seznamte se s [parametry upgradu aplikace](service-fabric-application-upgrade-parameters.md) a [procesu upgradu](service-fabric-application-upgrade.md) získat dobrý přehled o různé parametry upgradu, vypršení časových limitů a stavu kritérium, které může být použít. V tomto návodu kritéria hodnocení stavu služby nastavený na výchozí hodnotu (nemonitorované režim). Tato nastavení můžete konfigurovat tak, že vyberete **konfigurace nastavení upgradu** a následnou úpravou parametrů podle potřeby.

Teď máme nastavené a můžete začít upgrade aplikace tak, že vyberete **publikovat**. Tato možnost provede upgrade aplikace na verzi 2.0.0, ve kterém objekty otočit. Service Fabric se upgraduje jednu aktualizační doménu najednou (některé objekty se aktualizují jako první, za nímž následuje ostatní) a služby zůstávají dostupná během upgradu. Přístup ke službě mohou být zkontrolována prostřednictvím vašeho klienta (prohlížeč).  

Teď, když aplikace upgrade bude pokračovat, ji můžete sledovat pomocí Service Fabric Exploreru pomocí **probíhající upgrady** na kartě aplikace.

Za pár minut nutné upgradovat všech aktualizačních domén (dokončení) a v okně výstupu sady Visual Studio by měl také uvést dokončení upgradu. A měli byste najít, který *všechny* vizuálních objektů v okně prohlížeče jsou nyní otáčení!

Můžete chtít, zkuste změnit verze a přesun z verze 2.0.0 verze 3.0.0 jako cvičení, nebo dokonce z verze 2.0.0 zpět do verze 1.0.0. Pohrajte si s vypršení časových limitů a zásady stavu proveďte vlastní zkušenosti s nimi. Při nasazování do clusteru, na rozdíl od místního clusteru služby Azure, může mít parametry použité dokumenty budou lišit. Doporučujeme nastavit konzervativní časových limitů.

## <a name="next-steps"></a>Další postup
[Upgrade vaší aplikace pomocí Powershellu](service-fabric-application-upgrade-tutorial-powershell.md) vás provede upgrade aplikace pomocí Powershellu.

Řídí, jak se aplikace upgradovat pomocí [parametry upgradu](service-fabric-application-upgrade-parameters.md).

Díky upgradů aplikace kompatibilní učit, jak používat [serializaci dat](service-fabric-application-upgrade-data-serialization.md).

Zjistěte, jak používat pokročilé funkce při upgradu aplikace rekapitulací [Pokročilá témata](service-fabric-application-upgrade-advanced.md).

Vyřešit běžné problémy v upgradech aplikací odkazující na kroky v [řešení potíží s upgrady aplikací](service-fabric-application-upgrade-troubleshooting.md).

[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png
