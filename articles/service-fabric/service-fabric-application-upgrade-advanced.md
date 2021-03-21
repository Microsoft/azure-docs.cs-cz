---
title: Témata rozšířeného upgradu aplikací
description: Tento článek se věnuje několika pokročilým tématům, která se týkají upgradu aplikace Service Fabric.
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 6604300328f2d243077ba341a9028221438dce9d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98792044"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Upgrade Service Fabric aplikace: Pokročilá témata

## <a name="add-or-remove-service-types-during-an-application-upgrade"></a>Přidání nebo odebrání typů služeb během upgradu aplikace

Pokud se do publikované aplikace v rámci upgradu přidá nový typ služby, do nasazené aplikace se přidá nový typ služby. Takový upgrade nemá vliv na žádné instance služby, které již jsou součástí aplikace, ale je nutné vytvořit instanci přidaného typu, aby byl nový typ služby aktivní (viz [New-ServiceFabricService](/powershell/module/servicefabric/new-servicefabricservice)).

Podobně lze typy služeb odebrat z aplikace jako součást upgradu. Před pokračováním v upgradu je ale nutné odebrat všechny instance služby, které mají být odebrány. (viz [Remove-ServiceFabricService](/powershell/module/servicefabric/remove-servicefabricservice)).

## <a name="avoid-connection-drops-during-stateless-service-planned-downtime"></a>Vyhněte se výpadkům připojení během plánovaného výpadku služby

U plánovaných výpadků nestavových instancí, jako je například upgrade aplikace nebo clusteru nebo deaktivace uzlu, je připojení možné vyřadit, protože po ukončení instance dojde k odebrání vystaveného koncového bodu, což vede k vynucení uzavírání připojení.

Pokud tomu chcete předejít, nakonfigurujte funkci *RequestDrain* přidáním *instance doba zpoždění uzavření* v konfiguraci služby, aby mohly existující požadavky v rámci clusteru vyprázdnit z vystavených koncových bodů. K tomu je potřeba, aby byl koncový bod inzerovaný nestavovou instancí odebrán před tím, *než* se zahájí zpoždění před uzavřením instance. Tato prodleva umožní řádný odtok stávajících požadavků předtím, než se instance skutečně ukončí. Klienti jsou upozorněni na změnu koncového bodu pomocí funkce zpětného volání v době spuštění zpoždění, aby mohli znovu přeložit koncový bod a vyhnout se odesílání nových požadavků do instance, která se chystá. Tyto požadavky můžou pocházet z klientů pomocí [reverzního proxy serveru](./service-fabric-reverseproxy.md) nebo pomocí rozhraní API pro překlad koncového bodu služby s modelem oznámení ([ServiceNotificationFilterDescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription)) pro aktualizaci koncových bodů.

### <a name="service-configuration"></a>Konfigurace služeb

Existuje několik způsobů, jak nakonfigurovat zpoždění na straně služby.

 * **Při vytváření nové služby** zadejte `-InstanceCloseDelayDuration` :

    ```powershell
    New-ServiceFabricService -Stateless [-ServiceName] <Uri> -InstanceCloseDelayDuration <TimeSpan>
    ```

 * **Při definování služby v části výchozí v manifestu aplikace** přiřaďte `InstanceCloseDelayDurationSeconds` vlastnost:

    ```xml
          <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]" InstanceCloseDelayDurationSeconds="15">
              <SingletonPartition />
          </StatelessService>
    ```

 * **Při aktualizaci existující služby** zadejte `-InstanceCloseDelayDuration` :

    ```powershell
    Update-ServiceFabricService [-Stateless] [-ServiceName] <Uri> [-InstanceCloseDelayDuration <TimeSpan>]`
    ```

 * **Při vytváření nebo aktualizaci existující služby prostřednictvím šablony ARM** zadejte `InstanceCloseDelayDuration` hodnotu (minimální podporovaná verze rozhraní API: 2019-11-01-Preview):

    ```ARM template to define InstanceCloseDelayDuration of 30seconds
    {
      "apiVersion": "2019-11-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications/services",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
      "location": "[variables('clusterLocation')]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
      ],
      "properties": {
        "provisioningState": "Default",
        "serviceKind": "Stateless",
        "serviceTypeName": "[parameters('serviceTypeName')]",
        "instanceCount": "-1",
        "partitionDescription": {
          "partitionScheme": "Singleton"
        },
        "serviceLoadMetrics": [],
        "servicePlacementPolicies": [],
        "defaultMoveCost": "",
        "instanceCloseDelayDuration": "00:00:30.0"
      }
    }
    ```

### <a name="client-configuration"></a>Konfigurace klienta

Pokud chcete dostávat oznámení, když se změní koncový bod, klienti by měli zaregistrovat zpětné volání, viz [ServiceNotificationFilterDescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription).
Oznámení o změně je indikaci, že se koncové body změnily, klient by měl znovu přeložit koncové body a nesmí používat koncové body, které již nejsou inzerované, protože budou brzy pokračovat.

### <a name="optional-upgrade-overrides"></a>Volitelné přepsání upgradu

Kromě nastavení výchozích dob trvání zpoždění na službu můžete také přepsat zpoždění během upgradu aplikace nebo clusteru pomocí stejné `InstanceCloseDelayDurationSec` Možnosti ():

```powershell
Start-ServiceFabricApplicationUpgrade [-ApplicationName] <Uri> [-ApplicationTypeVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]

Start-ServiceFabricClusterUpgrade [-CodePackageVersion] <String> [-ClusterManifestVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]
```

Přepsané trvání prodlevy platí pouze pro vyvolanou instanci upgradu a jinak nemění konfigurace zpoždění jednotlivých služeb. Můžete například použít k určení zpoždění `0` pro přeskočení všech předkonfigurovaných zpoždění upgradu.

> [!NOTE]
> * Nastavení vyprázdnit požadavky nebude moci zabránit nástroji pro vyrovnávání zatížení Azure posílat nové žádosti do koncových bodů, které procházejí vyprázdněním.
> * Mechanismus řešení založený na stížnostech nevede k řádnému vyprázdnění požadavků, protože aktivuje řešení služby po selhání. Jak bylo popsáno výše, mělo by se místo toho rozšířit, aby se přihlásil k odběru oznámení změn koncových bodů pomocí [ServiceNotificationFilterDescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription).
> * Tato nastavení se neuplatňují, pokud upgrade neovlivní nějaký význam. v případě, že repliky nebudou během upgradu zavedeny.
>
>

> [!NOTE]
> Tato funkce se dá nakonfigurovat v existujících službách pomocí rutiny Update-ServiceFabricService nebo šablony ARM, jak je uvedeno výše, pokud je verze kódu clusteru 7.1.XXX nebo vyšší.
>
>

## <a name="manual-upgrade-mode"></a>Režim ručního upgradu

> [!NOTE]
> Režim *monitorovaného* upgradu je doporučen pro všechny upgrady Service Fabric.
> Režim upgradu *UnmonitoredManual* by se měl považovat jenom za neúspěšné nebo pozastavené upgrady. 
>
>

V *monitorovaném* režimu Service Fabric používá zásady stavu, aby se zajistilo, že bude aplikace v pořádku, jak probíhá upgrade. Pokud dojde k porušení zásad stavu, upgrade se buď pozastaví, nebo se automaticky vrátí zpátky v závislosti na zadaném *FailureAction*.

V režimu *UnmonitoredManual* má správce aplikace celkovou kontrolu nad průběhem upgradu. Tento režim je užitečný při použití vlastních zásad hodnocení stavu nebo provádění nekonvenčních upgradů, které mají zcela obejít monitorování stavu (například aplikace je již v případě ztráty dat). Upgrade běžící v tomto režimu se sám po dokončení každého UD zastaví a musí se explicitně obnovit pomocí operace [Resume-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/resume-servicefabricapplicationupgrade). V případě, že je upgrade pozastaven a je připraven k obnovení uživatelem, zobrazí se stav upgradu *RollforwardPending* (viz [UpgradeState](/dotnet/api/system.fabric.applicationupgradestate)).

*UnmonitoredAuto* režim je vhodný pro provádění iterací rychlého upgradu během vývoje nebo testování služby, protože není nutný žádný vstup uživatele a nejsou vyhodnoceny žádné zásady stavu aplikace.

## <a name="upgrade-with-a-diff-package"></a>Upgrade s rozdílovým balíčkem

Místo zřízení kompletního balíčku aplikace je možné upgrady provádět taky pomocí zřizovacích balíčků, které obsahují jenom aktualizované balíčky Code/config/data, a to společně s úplným manifestem aplikace a kompletními manifesty služby. Kompletní balíčky aplikací jsou nutné pouze pro počáteční instalaci aplikace do clusteru. Další upgrady můžou být buď z kompletních balíčků aplikací, nebo rozdílných balíčků.  

Jakýkoli odkaz v manifestu aplikace nebo manifestech služby rozdílového balíčku, který se nedá najít v balíčku aplikace, se automaticky nahradí aktuálně zřízenou verzí.

Scénáře použití rozdílového balíčku:

* Máte-li velký balíček aplikace, který odkazuje na několik souborů manifestu služby a/nebo několik balíčků kódu, konfigurační balíčky nebo datové balíčky.
* Pokud máte systém nasazení, který generuje rozložení sestavení přímo z procesu sestavení vaší aplikace. V tomto případě, i když se kód nezměnil, nově vytvořená sestavení získají jiný kontrolní součet. Použití úplného balíčku aplikace by vyžadovalo aktualizaci verze u všech balíčků kódu. Pomocí rozdílového balíčku zadejte jenom soubory, které se změnily, a soubory manifestu, ve kterých se verze změnila.

Při upgradu aplikace pomocí sady Visual Studio je automaticky publikován rozdílový balíček. Chcete-li vytvořit rozdílový balíček ručně, musí být aktualizován manifest aplikace a manifesty služby, ale do finálního balíčku aplikace by měly být zahrnuty pouze změněné balíčky.

Řekněme například, že začneme s následující aplikací (čísla verzí, která jsou poskytována pro snazší porozumění):

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Předpokládejme, že jste chtěli aktualizovat pouze balíček kódu Service1 pomocí rozdílového balíčku. Aktualizovaná aplikace má následující změny verze:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

V tomto případě aktualizujete manifest aplikace na 2.0.0 a manifest služby pro Service1 tak, aby odrážel aktualizaci balíčku kódu. Složka pro váš balíček aplikace by měla mít následující strukturu:

```text
app1/
  service1/
    code/
```

Jinými slovy, vytvořit kompletní balíček aplikace normálně a pak odebrat všechny složky Code/config/data Package, pro které se verze nezměnila.

## <a name="upgrade-application-parameters-independently-of-version"></a>Upgradovat parametry aplikace nezávisle na verzi

V některých případech je žádoucí změnit parametry Service Fabric aplikace beze změny verze manifestu. To lze provést pohodlně pomocí příznaku **-ApplicationParameter** s rutinou **Start-ServiceFabricApplicationUpgrade** Azure Service Fabric PowerShellu. Předpokládejme Service Fabric aplikaci s následujícími vlastnostmi:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "1"; "NewParameter" = "testBefore" }
```

Nyní upgradujte aplikaci pomocí rutiny **Start-ServiceFabricApplicationUpgrade** . Tento příklad ukazuje monitorovaný upgrade, ale lze použít také nemonitorovaný upgrade. Úplný popis příznaků přijatých touto rutinou najdete v tématu [Přehled modulu Azure Service Fabric PowerShell](/powershell/module/servicefabric/start-servicefabricapplicationupgrade#parameters) .

```PowerShell
PS C:\> $appParams = @{ "ImportantParameter" = "2"; "NewParameter" = "testAfter"}

PS C:\> Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/Application1 -ApplicationTypeVers
ion 1.0.0 -ApplicationParameter $appParams -Monitored

```

Po upgradu ověřte, zda má aplikace aktualizované parametry a stejnou verzi:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "2"; "NewParameter" = "testAfter" }
```

## <a name="roll-back-application-upgrades"></a>Vrácení upgradů aplikací zpátky

I když se upgrady dají převádět v jednom ze tří režimů (*monitorované*, *UnmonitoredAuto* nebo *UnmonitoredManual*), dají se vrátit zpátky buď v režimu *UnmonitoredAuto* , nebo *UnmonitoredManual* . Vracení zpět v režimu *UnmonitoredAuto* funguje stejným způsobem jako při vracení s výjimkou, že výchozí hodnota *UpgradeReplicaSetCheckTimeout* je odlišná – viz [parametry upgradu aplikace](service-fabric-application-upgrade-parameters.md). Vracení zpět v režimu *UnmonitoredManual* funguje stejně jako postupné dopředné – vrácení zpět se po dokončení každého ud odblokuje a musí se explicitně obnovit pomocí funkce [Resume-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/resume-servicefabricapplicationupgrade) , aby bylo možné pokračovat v vracení zpět.

Vrácení zpět se dá spustit automaticky, když se naruší zásady stavu upgradu v *monitorovaném* režimu s *FailureActionem* *vrácení zpět* (viz [parametry upgradu aplikace](service-fabric-application-upgrade-parameters.md)) nebo explicitně pomocí [Start-ServiceFabricApplicationRollback](/powershell/module/servicefabric/start-servicefabricapplicationrollback).

Během vracení zpět lze hodnotu *UpgradeReplicaSetCheckTimeout* a režim kdykoli změnit pomocí funkce [Update-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/update-servicefabricapplicationupgrade).

## <a name="next-steps"></a>Další kroky
[Upgrade aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md) vás provede upgradem aplikace pomocí sady Visual Studio.

[Upgrade aplikace pomocí PowerShellu](service-fabric-application-upgrade-tutorial-powershell.md) vás provede upgradem aplikace pomocí PowerShellu.

Pomocí [parametrů upgradu](service-fabric-application-upgrade-parameters.md)lze řídit, jak se vaše aplikace upgradují.

Vyjistěte, aby byly upgrady aplikací kompatibilní, pomocí učení, jak používat [serializaci dat](service-fabric-application-upgrade-data-serialization.md).

Pomocí postupu v části [řešení potíží s upgrady aplikací](service-fabric-application-upgrade-troubleshooting.md)můžete opravit běžné problémy s upgrady aplikací.
