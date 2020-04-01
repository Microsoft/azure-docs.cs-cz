---
title: Rozšířená témata upgradu aplikací
description: Tento článek popisuje některá pokročilá témata týkající se upgradu aplikace Service Fabric.
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 182ab6dc1663e160561b8941ebf3a36b5af3d950
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422810"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Upgrade aplikace Service Fabric: Pokročilá témata

## <a name="add-or-remove-service-types-during-an-application-upgrade"></a>Přidání nebo odebrání typů služeb během upgradu aplikace

Pokud je nový typ služby přidán do publikované aplikace jako součást upgradu, bude nový typ služby přidán do nasazené aplikace. Takový upgrade nemá vliv na žádné instance služby, které již byly součástí aplikace, ale instance typu služby, která byla přidána, musí být vytvořena, aby byl nový typ služby aktivní (viz [New-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)).

Podobně typy služeb lze odebrat z aplikace jako součást upgradu. Všechny instance služby typu služby, které mají být odebrány, však musí být před pokračováním v upgradu odebrány (viz [Odebrání služby ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps)).

## <a name="avoid-connection-drops-during-stateless-service-planned-downtime-preview"></a>Vyhněte se výpadkům připojení během plánovaných výpadků služby bez stavů (náhled)

Pro plánované prostojů bezstavové instance, jako je například upgrade aplikace/clusteru nebo deaktivace uzlu, může být připojení zrušena z důvodu vyřazení exponovaného koncového bodu po přejdeme instance, což má za následek vynucené uzavření připojení.

Chcete-li tomu zabránit, nakonfigurujte funkci *RequestDrain* (preview) přidáním *doby trvání zpoždění zavření instance* v konfiguraci služby, abyste povolili vypouštění při přijímání požadavků z jiných služeb v rámci clusteru a používají reverzní proxy server nebo používají rozhraní API pro řešení s modelem oznámení pro aktualizaci koncových bodů. Tím je zajištěno, že koncový bod inzerovaný bezstavovou instancí je odebrán *před* začátkem zpoždění před zavřením instance. Toto zpoždění umožňuje existující požadavky řádně vyprázdnit před instance skutečně přejde dolů. Klienti jsou upozorněni na změnu koncového bodu funkcí zpětného volání v době spuštění zpoždění, aby mohli znovu vyřešit koncový bod a vyhnout se odesílání nových požadavků do instance, která se chystá dolů.

### <a name="service-configuration"></a>Konfigurace služby

Existuje několik způsobů, jak nakonfigurovat zpoždění na straně služby.

 * **Při vytváření nové služby**zadejte `-InstanceCloseDelayDuration`:

    ```powershell
    New-ServiceFabricService -Stateless [-ServiceName] <Uri> -InstanceCloseDelayDuration <TimeSpan>`
    ```

 * **Při definování služby v části výchozí v manifestu aplikace**přiřaďte `InstanceCloseDelayDurationSeconds` vlastnost:

    ```xml
          <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]" InstanceCloseDelayDurationSeconds="15">
              <SingletonPartition />
          </StatelessService>
    ```

 * **Při aktualizaci existující služby**zadejte `-InstanceCloseDelayDuration`:

    ```powershell
    Update-ServiceFabricService [-Stateless] [-ServiceName] <Uri> [-InstanceCloseDelayDuration <TimeSpan>]`
    ```

### <a name="client-configuration"></a>Konfigurace klienta

Chcete-li dostávat oznámení při změně koncového bodu, klienti by měli zaregistrovat zpětné volání viz [ServiceNotificationFilterDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.servicenotificationfilterdescription).
Oznámení o změně je známkou toho, že se změnily koncové body, klient by měl znovu vyřešit koncové body a nepoužívat koncové body, které již nejsou inzerovány, protože brzy přejdou dolů.

### <a name="optional-upgrade-overrides"></a>Volitelná přepsání upgradu

Kromě nastavení výchozí doby zpoždění na službu můžete také přepsat zpoždění během upgradu`InstanceCloseDelayDurationSec`aplikace/clusteru pomocí stejné možnosti ( ) :

```powershell
Start-ServiceFabricApplicationUpgrade [-ApplicationName] <Uri> [-ApplicationTypeVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]

Start-ServiceFabricClusterUpgrade [-CodePackageVersion] <String> [-ClusterManifestVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]
```

Doba trvání zpoždění se vztahuje pouze na instanci vyvoláné upgradu a jinak nezmění jednotlivé konfigurace zpoždění služby. Můžete ji například použít k určení `0` zpoždění, aby bylo možné přeskočit všechna předkonfigurovaná zpoždění upgradu.

> [!NOTE]
> Nastavení vyprázdnění požadavků není dodrženo pro požadavky z Azure BalanceR. Nastavení není dodrženo, pokud volající služba používá řešení založené na stížnostech.
>
>

> [!NOTE]
> Tuto funkci lze nakonfigurovat v existujících službách pomocí rutiny Update-ServiceFabricService, jak je uvedeno výše, pokud je verze kódu clusteru 7.1.XXX nebo vyšší.
>
>

## <a name="manual-upgrade-mode"></a>Režim ručního upgradu

> [!NOTE]
> *Režim sledovaného* upgradu je doporučen pro všechny upgrady service fabric.
> Režim upgradu *UnmonitoredManual* by měl být považován pouze za neúspěšné nebo pozastavené inovace. 
>
>

Ve *sledovaném* režimu Service Fabric použije zásady stavu k zajištění, že aplikace je v pořádku, jak probíhá upgrade. Pokud jsou porušeny zásady stavu, upgrade je pozastavena nebo automaticky vrácena zpět v závislosti na zadané *akce Selhání*.

V režimu *UnmonitoredManual* má správce aplikace úplnou kontrolu nad průběhem upgradu. Tento režim je užitečný při použití vlastních zásad hodnocení stavu nebo provádění nekonvenčních upgradů, aby se úplně obešla monitorování stavu (např. aplikace je již ve ztrátě dat). Upgrade spuštěný v tomto režimu se po dokončení každého ud pozastaví a musí být explicitně obnoven pomocí [resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps). Pokud je upgrade pozastaven a připraven k obnovení uživatelem, jeho stav upgradu se zobrazí *RollforwardPending* (viz [UpgradeState](https://docs.microsoft.com/dotnet/api/system.fabric.applicationupgradestate?view=azure-dotnet)).

Nakonec je režim *UnmonitoredAuto* užitečný pro provádění iterací rychlého upgradu během vývoje nebo testování služby, protože není vyžadován žádný vstup uživatele a nejsou vyhodnocovány žádné zásady stavu aplikace.

## <a name="upgrade-with-a-diff-package"></a>Upgrade pomocí rozdílového balíčku

Namísto zřizování kompletní balíček aplikace upgrady lze také provést zřizováním rozdílové balíčky, které obsahují pouze aktualizovaný kód/config/data balíčky spolu s kompletní manifest aplikace a kompletní služby manifesty. Kompletní balíčky aplikací jsou vyžadovány pouze pro počáteční instalaci aplikace do clusteru. Následné inovace mohou být buď z kompletní balíčky aplikací nebo diff balíčky.  

Jakýkoli odkaz v manifestu aplikace nebo manifesty služby rozdílový balíček, který nelze nalézt v balíčku aplikace je automaticky nahrazen aktuálně zřízenou verzi.

Scénáře pro použití rozdílového balíčku jsou:

* Pokud máte velký balíček aplikace, který odkazuje na několik souborů manifestu služby nebo několik balíčků kódu, balíčky konfigurace nebo datové balíčky.
* Pokud máte systém nasazení, který generuje rozložení sestavení přímo z procesu sestavení aplikace. V tomto případě, i když se kód nezměnil, nově vytvořená sestavení získají jiný kontrolní součet. Použití úplného balíčku aplikace by vyžadovalo aktualizaci verze na všech balíčcích kódu. Pomocí balíčku diff poskytujete pouze soubory, které se změnily, a soubory manifestu, kde byla změněna verze.

Při upgradu aplikace pomocí sady Visual Studio je automaticky publikován balíček rozdíl. Chcete-li vytvořit diff balíček ručně, manifest aplikace a manifesty služby musí být aktualizovány, ale pouze změněné balíčky by měly být zahrnuty v konečné balíčku aplikace.

Začněme například s následující aplikací (čísla verzí jsou k dispozici pro snadné pochopení):

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Předpokládejme, že jste chtěli aktualizovat pouze balíček kódu service1 pomocí balíčku diff. Aktualizovaná aplikace má následující změny verze:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

V takovém případě aktualizujete manifest aplikace na 2.0.0 a manifest služby pro service1 tak, aby odrážely aktualizaci balíčku kódu. Složka pro váš balíček aplikace bude mít následující strukturu:

```text
app1/
  service1/
    code/
```

Jinými slovy, vytvořte kompletní balíček aplikace normálně, pak odeberte všechny složky code/config/data package, u kterých se verze nezměnila.

## <a name="upgrade-application-parameters-independently-of-version"></a>Upgrade parametrů aplikace nezávisle na verzi

Někdy je žádoucí změnit parametry aplikace Service Fabric beze změny verze manifestu. To lze provést pohodlně pomocí příznaku **-ApplicationParameter** s rutinou **Start-ServiceFabricApplicationUpgrade** Azure Service Fabric PowerShell. Předpokládejme aplikaci Service Fabric s následujícími vlastnostmi:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "1"; "NewParameter" = "testBefore" }
```

Nyní upgradujte aplikaci pomocí rutiny **Start-ServiceFabricApplicationUpgrade.** Tento příklad ukazuje monitorovaný upgrade, ale lze také použít nemonitorovaný upgrade. Úplný popis příznaků přijatých touto rutinou najdete v [tématu odkaz na modul PowerShell azure service fabric](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps#parameters)

```PowerShell
PS C:\> $appParams = @{ "ImportantParameter" = "2"; "NewParameter" = "testAfter"}

PS C:\> Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/Application1 -ApplicationTypeVers
ion 1.0.0 -ApplicationParameter $appParams -Monitored

```

Po upgradu zkontrolujte, zda má aplikace aktualizované parametry a stejnou verzi:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "2"; "NewParameter" = "testAfter" }
```

## <a name="roll-back-application-upgrades"></a>Vrácení upgradů aplikací zpět

Zatímco upgrady lze vrátit vpřed v jednom ze tří režimů *(Monitored*, *UnmonitoredAuto*, nebo *UnmonitoredManual),* mohou být vráceny zpět pouze v režimu *UnmonitoredAuto* nebo *UnmonitoredManual.* Vrácení zpět v režimu *UnmonitoredAuto* funguje stejným způsobem jako postupvpřed s výjimkou, že výchozí hodnota *UpgradeReplicaSetCheckTimeout* se liší - viz [Parametry upgradu aplikace](service-fabric-application-upgrade-parameters.md). Vrácení zpět v režimu *UnmonitoredManual* funguje stejným způsobem jako postupné vpřed - vrácení zpět se pozastaví po dokončení každého UD a musí být explicitně obnovena pomocí [Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) pokračovat s vrácení zpět.

Vrácení zpět lze spustit automaticky, když jsou porušeny zásady stavu upgradu ve *sledovaném* režimu s *akcí failureaction* *vrácení zpět* (viz [Parametry upgradu aplikace](service-fabric-application-upgrade-parameters.md)) nebo explicitně pomocí [Start-ServiceFabricApplicationRollback](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps).

Během vrácení zpět, hodnota *UpgradeReplicaSetCheckTimeout* a režim lze kdykoli změnit pomocí [Update-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps).

## <a name="next-steps"></a>Další kroky
[Inovace aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md) vás provede upgradem aplikace pomocí sady Visual Studio.

[Upgrade aplikace pomocí powershellu](service-fabric-application-upgrade-tutorial-powershell.md) vás provede upgradem aplikace pomocí PowerShellu.

Pomocí funkce [Parametry upgradu](service-fabric-application-upgrade-parameters.md)můžete řídit způsob upgradu aplikace .

Pomocí aplikace můžete upgrady aplikací provést tak, že se naučíte používat [serializaci dat](service-fabric-application-upgrade-data-serialization.md).

Běžné problémy při upgradu aplikací opravíte odkazem na kroky při [řešení potíží s inovacemi aplikací](service-fabric-application-upgrade-troubleshooting.md).
