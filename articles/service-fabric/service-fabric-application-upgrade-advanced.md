---
title: Rozšířené témata týkající se upgradu aplikace | Microsoft Docs
description: Tento článek se zabývá některá Pokročilá témata týkající se upgradu aplikace Service Fabric.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: e29585ff-e96f-46f4-a07f-6682bbe63281
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 168d944f72c1409b5b69c9ab7c07f7fcfa04c7c8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212319"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Upgrade aplikace Service Fabric: advanced témata
## <a name="adding-or-removing-service-types-during-an-application-upgrade"></a>Přidání nebo odebrání typů služeb během upgradu aplikace
Pokud přidáte nový typ služby k publikované aplikaci jako součást upgradu, nový typ služby se přidá do nasazené aplikace. Takové upgrade neovlivní žádné instance služby, které již byly součástí aplikace, ale musíte vytvořit instanci typu služby, která byla přidána pro nový typ služby jako aktivní (viz [New-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)).

Podobně můžete z aplikace jako součást upgradu odebrat typů služeb. Však musí být před pokračováním v upgradu odebrány všechny instance služby typu k-be-odebrat služby (viz [odebrat ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps)).

## <a name="manual-upgrade-mode"></a>Režim manuálního upgradu
> [!NOTE]
> *Monitorované* režimu upgradu se doporučuje pro všechny upgradů Service Fabric.
> *UnmonitoredManual* režimu upgradu pouze považovat za událost pro selhání nebo pozastavený upgrady. 
>
>

V *monitorované* režimu, Service Fabric se vztahují zásady stavu a ujistěte se, že aplikace je v pořádku v průběhu upgradu. Pokud jsou došlo k porušení zásady stavu, pak upgradu je pozastaven nebo automaticky vrátila zpět v závislosti na zadaný *FailureAction*.

V *UnmonitoredManual* režimu, Správce aplikací má plně pod kontrolou nad průběh upgradu. Tento režim je užitečné při použití zásad vyhodnocení stavu vlastní nebo provádění jiných konvenční upgrady obejít úplně sledování stavu (aplikace je již dojít ke ztrátě dat.). V tomto režimu upgradu bude po dokončení každé UD pozastavit sám a musí být explicitně byl obnoven pomocí [Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps). Při upgradu je pozastavené a jste připraveni pokračovat uživatelem, stav upgradu se zobrazí *RollforwardPending* (viz [UpgradeState](https://docs.microsoft.com/dotnet/api/system.fabric.applicationupgradestate?view=azure-dotnet)).

Nakonec *UnmonitoredAuto* režim je užitečné pro provádění rychlé upgradu iterací během služby vývoj nebo testování vzhledem k tomu, že žádný uživatelský vstup je povinná a vyhodnocují se žádné zásady stavu aplikace.

## <a name="upgrade-with-a-diff-package"></a>Upgrade s balíčkem rozdílů
Místo zřizování balíčku aplikace dokončena, můžete upgrade provést také pomocí zřizování rozdílové balíčky, které obsahují jenom aktualizované balíčky kód/config/dat společně s manifest aplikace dokončena a dokončení manifesty služby. Balíčky dokončení aplikací jsou pouze požadované pro počáteční instalaci aplikace do clusteru. Následující upgrady mohou být buď z hotové aplikace balíčků nebo rozdílové.  

Všechny odkazy v manifestu aplikace nebo služby manifesty rozdílové balíčku, který nebyl nalezen v balíčku aplikace se automaticky nahradí aktuálně zřízené verze.

Scénáře pro pomocí balíčku rozdílové jsou:

* Pokud máte velké aplikace balíček, který odkazuje na několik souborů manifestu služby nebo několik balíčků kódu, konfigurace balíčky nebo balíčky data.
* Pokud máte nasazení systému, který generuje rozložení sestavení přímo z vaší aplikace proces sestavení. V tomto případě to i v případě, že kód se nezměnila, nově vytvořený sestavení získat různé kontrolního součtu. Pomocí balíčku pro celou aplikaci by vyžadují aktualizaci verze na všechny balíčky kódu. Pomocí balíčku rozdílů, je jenom zadat soubory, které změnily a souborů manifestu nichž došlo ke změně verze.

Při upgradu aplikace pomocí sady Visual Studio rozdílové balíček je automaticky publikován. Vytvoření balíčku rozdílové ručně, musí být aktualizované manifest aplikace a služby manifesty, ale pouze změněné balíčky by měl být součástí balíčku poslední aplikace.

Například začneme následující aplikace (čísla verzí zadaná pro snadné pochopení):

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Předpokládejme, že chcete aktualizovat pouze kód balíček service1 pomocí rozdílové balíčku. Aktualizovaná aplikace obsahuje následující změny verze:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

V takovém případě je aktualizovat manifest aplikace 2.0.0 a service manifest pro service1 tak, aby odrážela aktualizace balíčku kódu. Složku balíčku aplikace by mít následující strukturu:

```text
app1/
  service1/
    code/
```

Jinými slovy obvykle vytvořit balíček dokončení aplikace a potom odeberte všechny složky balíček kódu/config/dat, u kterých nebylo změněno na verzi.

## <a name="rolling-back-application-upgrades"></a>Vrácení upgradu aplikace

Při upgradu můžete v jednom ze tří režimů posunuta dopředu (*monitorované*, *UnmonitoredAuto*, nebo *UnmonitoredManual*), budou pouze možné vrátit zpět v obou *UnmonitoredAuto* nebo *UnmonitoredManual* režimu. Postupné zpět v *UnmonitoredAuto* režimu funguje stejným způsobem jako vrácení dál s tím rozdílem, který na výchozí hodnotu *UpgradeReplicaSetCheckTimeout* se liší - najdete v části [aplikace Upgrade parametry](service-fabric-application-upgrade-parameters.md). Postupné zpět v *UnmonitoredManual* režimu funguje stejným způsobem jako vrácení dál – vrácení zpět se pozastavit sám po dokončení každé UD a musí být explicitně byl obnoven pomocí [ Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) pokračujte s vrácení zpět.

Odvolání můžete spustit automaticky při zásady stavu upgradu v *monitorované* režimu se *FailureAction* z *vrácení zpět* jsou došlo k porušení (viz [Parametry upgradu aplikace](service-fabric-application-upgrade-parameters.md)) nebo explicitně pomocí [Start-ServiceFabricApplicationRollback](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps).

Během vracení zpět hodnotu *UpgradeReplicaSetCheckTimeout* a režim stále lze změnit současně pomocí [aktualizace ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps).

## <a name="next-steps"></a>Další postup
[Upgrade vaší aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md) vás provede upgrade aplikace pomocí sady Visual Studio.

[Upgrade vaší aplikace pomocí prostředí Powershell](service-fabric-application-upgrade-tutorial-powershell.md) vás provede upgrade aplikace pomocí prostředí PowerShell.

Řídí, jak vaše aplikace upgraduje pomocí [Upgrade parametry](service-fabric-application-upgrade-parameters.md).

Zkontrolujte upgradů aplikace kompatibilní podle naučit se používat [serializace dat](service-fabric-application-upgrade-data-serialization.md).

Řešení běžných potíží v upgradů aplikací podle kroků v části [řešení potíží s aplikací upgrady](service-fabric-application-upgrade-troubleshooting.md).
