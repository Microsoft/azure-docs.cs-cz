---
title: Pokročilá témata upgradu aplikace | Dokumentace Microsoftu
description: Tento článek se týká některá Pokročilá témata týkající se upgradu aplikace Service Fabric.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: e29585ff-e96f-46f4-a07f-6682bbe63281
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 3cdddac74552b56dfe3567adf30f1a05b6eb8e24
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663787"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Upgrade aplikace Service Fabric: Pokročilá témata
## <a name="adding-or-removing-service-types-during-an-application-upgrade"></a>Přidávání nebo odebírání typů služeb během upgradu aplikace
Pokud k publikované aplikaci jako součást upgradu je přidán nový typ služby, se přidá nový typ služby k nasazené aplikaci. Takové upgrade neovlivní žádné instance služby, které již byly součástí aplikace, ale musí vytvoří instanci daného typu služby, která byla přidána pro nový typ služby jako aktivní (viz [New-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)).

Typy služeb obdobně lze odebrat z aplikace jako součást upgradu. Však musí před pokračováním v upgradu odebrat všechny instance služby daného typu služby k-be-odebrat (viz [ServiceFabricService odebrat](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps)).

## <a name="manual-upgrade-mode"></a>Ruční režim upgradu
> [!NOTE]
> *Monitorované* režim upgradu se doporučuje pro všechny upgrady Service Fabric.
> *UnmonitoredManual* režim upgradu považovat jenom pro nezdařená nebo pozastavené upgrady. 
>
>

V *monitorované* režim, Service Fabric se týká stavu zásad pro zajištění, že aplikace je v dobrém stavu v průběhu upgradu. Pokud jsou došlo k porušení zásady stavu a pak upgrade pozastaví nebo automaticky vrátila zpátky v závislosti na zadané *FailureAction*.

V *UnmonitoredManual* režimu, správce aplikace má úplnou kontrolu nad jeho průběh upgradu. Tento režim je užitečný při uplatňování zásad pro vyhodnocení vlastních stavových nebo nekonvenční inovacích obejít zcela monitorování stavu (například aplikace je již ztrátu dat). V tomto režimu upgradu se pozastavit sám po dokončení každého UD a musí explicitně obnovit pomocí [Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps). Při upgradu je pozastavený a připravena obnoveno uživatelem, jeho stav upgradu se zobrazí *RollforwardPending* (viz [UpgradeState](https://docs.microsoft.com/dotnet/api/system.fabric.applicationupgradestate?view=azure-dotnet)).

Nakonec *UnmonitoredAuto* režim je užitečná při provádění rychlé inovace iterací během služby vývoj a testování, protože je vyžadován žádný vstup uživatele a žádné zásady stavu aplikace jsou vyhodnocovány.

## <a name="upgrade-with-a-diff-package"></a>Upgrade s balíčkem diff
Místo zřizovacího balíčku aplikace dokončena a, upgradů můžete také provést zřizovacích balíčků rozdílů, které obsahují pouze aktualizovaný kód/config/data balíčky spolu s manifest aplikace dokončena a a dokončete manifestů služby. Balíčky aplikace dokončena a jsou pouze pro počáteční instalaci aplikace do clusteru se vyžaduje. Následující upgrady může být buď z aplikace dokončena a balíčků nebo rozdíl.  

Každý odkaz v manifestu aplikace nebo služba manifesty diff balíčku, který nebyl nalezen v balíčku aplikace se automaticky nahradí aktuálně zřízené verze.

Scénáře použití sady změn jsou:

* Pokud máte velké aplikace balíček, který odkazuje na několik souborů manifestu služby a/nebo několik balíčků kódu, konfigurace balíčky nebo balíčky data.
* Pokud máte nasazení systému, který generuje rozložení sestavení přímo z vaší aplikace procesu sestavení. V takovém případě i v případě, že nedošlo ke změně kódu, nově vytvořených sestavení získat různé kontrolní součet. Pomocí balíčku pro celou aplikaci bude potřeba aktualizovat verze na všechny balíčky kódu. Pomocí balíčku rozdíl je pouze zadat, které se změnily soubory a soubory manifestu, kde byl změněn na verzi.

Při upgradu aplikace pomocí sady Visual Studio, sady změn je automaticky publikován. Vytvoření balíčku diff ručně, se musí aktualizovat manifesty služby a manifest aplikace, ale pouze změněné balíčky měly by být součástí balíčku poslední aplikace.

Například začneme následující aplikace (k dispozici pro snadnější pochopení čísla verzí):

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Předpokládejme, že chcete aktualizovat pouze kód balíček service1 pomocí balíčku pro rozdíl. Aktualizovaná aplikace obsahuje následující změny verze:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

V takovém případě je aktualizovat manifest aplikace na 2.0.0 a manifest služby pro service1 tak, aby odrážely aktualizace balíčku kódu. Složku balíčku aplikace by mají následující strukturu:

```text
app1/
  service1/
    code/
```

Jinými slovy obvykle vytvořit balíček aplikace dokončena a potom odeberte všechny složky balíčku pro kód/config/dat, u kterých nedošlo ke změně verze.

## <a name="rolling-back-application-upgrades"></a>Vrácení zpět upgrady aplikací

Při upgradu můžete v jednom ze tří režimů posunut dopředu (*monitorované*, *UnmonitoredAuto*, nebo *UnmonitoredManual*), jsou pouze možné vrátit zpět v obou *UnmonitoredAuto* nebo *UnmonitoredManual* režimu. Postupné zpět v *UnmonitoredAuto* režim funguje stejným způsobem jako s výjimkou Posunutí vpřed, který na výchozí hodnotu *UpgradeReplicaSetCheckTimeout* se liší - naleznete v tématu [aplikace Parametry upgradu](service-fabric-application-upgrade-parameters.md). Postupné zpět v *UnmonitoredManual* režim funguje stejným způsobem jako posunutí vpřed – vrácení změn se pozastavit sám po dokončení každého UD a musí být explicitně nedojde k přerušení pomocí [ Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) pokračujte vrácení změn.

Vrácení změn je možné spustit automaticky při zásady stavu upgradu v *monitorované pomocí monitorování* režimu s *FailureAction* z *vrácení zpět* je porušena (viz [Parametry upgradu aplikace](service-fabric-application-upgrade-parameters.md)) nebo explicitně pomocí [Start ServiceFabricApplicationRollback](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps).

Během vracení zpět hodnotu *UpgradeReplicaSetCheckTimeout* a režim změnit půjdou kdykoliv [Update-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps).

## <a name="next-steps"></a>Další postup
[Upgrade aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md) vás provede upgrade aplikace pomocí sady Visual Studio.

[Upgrade aplikace pomocí Powershellu](service-fabric-application-upgrade-tutorial-powershell.md) vás provede upgrade aplikace pomocí Powershellu.

Řídí, jak vaše aplikace upgradovala pomocí [parametry upgradu](service-fabric-application-upgrade-parameters.md).

Díky upgradů aplikace kompatibilní učit, jak používat [serializaci dat](service-fabric-application-upgrade-data-serialization.md).

Vyřešit běžné problémy v upgradech aplikací odkazující na kroky v [řešení potíží s upgrady aplikací](service-fabric-application-upgrade-troubleshooting.md).
