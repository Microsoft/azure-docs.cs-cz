---
title: Upgrade aplikace Service Fabric | Dokumentace Microsoftu
description: Tento článek obsahuje úvod k upgradu aplikace Service Fabric, včetně zvolíte možnost upgradu režimech a provádění kontroly stavu.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: 803c9c63-373a-4d6a-8ef2-ea97e16e88dd
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 79408c9936000aa18dba9347b8a10fa7dcd8e8ee
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35759554"
---
# <a name="service-fabric-application-upgrade"></a>Upgrade aplikace Service Fabric
Aplikace Azure Service Fabric je kolekce služeb. Během upgradu, porovná Service Fabric nové [manifest aplikace](service-fabric-application-and-service-manifests.md) s předchozí verzí a určuje, které služby vyžadovat aktualizace aplikace. Service Fabric porovnává verzi čísla ve službě manifesty pomocí čísla verze v předchozí verzi. Pokud služba se nezměnila, není upgradována danou službu.

## <a name="rolling-upgrades-overview"></a>Přehled upgradu se zajištěním provozu
V aplikaci se zajištěním upgradu provádí ve fázích. V každé fázi upgradu použít pro dílčí sadu uzlů v clusteru, s názvem aktualizační doména. V důsledku toho zůstane k dispozici v rámci upgradu aplikace. Během upgradu clusteru může obsahovat kombinaci staré a nové verze.

Z tohoto důvodu musí být dvě verze dopředné a zpětné kompatibilní. Pokud nejsou kompatibilní, správce aplikace je zodpovědná za pracovní více fází upgradu dostupnost. V případě více fází upgradu je prvním krokem upgraduje na zprostředkující verzi aplikace, která je kompatibilní s předchozí verzí. Druhým krokem je upgrade finální verze přeruší kompatibilitu s verzí před aktualizací, která je kompatibilní s verzí zprostředkující.

Aktualizační domény jsou určené v manifestu clusteru, při konfiguraci clusteru. Aktualizační domény nebudou přijímat aktualizace v určitém pořadí. Aktualizační doména je logická jednotka nasazení pro aplikaci. Aktualizační domény povolit služby, které mají zůstat na vysokou dostupnost během upgradu.

Zajištěním bez je možné v případě upgrade se použije na všechny uzly v clusteru, což je případ, kdy má jenom jednu aktualizační doménu aplikace. Tento přístup není doporučen, protože služba přestane fungovat a není k dispozici během upgradu. Kromě toho Azure neposkytuje žádné záruky při cluster má nastavenou jenom jedna aktualizační doména.

Po dokončení upgradu všech služeb a replicas(instances) by zůstat ve stejné verzi – to znamená, pokud upgradu úspěšná, bude aktualizován na novou verzi; Když se upgrade nezdaří a je vrácena zpět, že by se vrátit zpět na předchozí verzi aplikace.

## <a name="health-checks-during-upgrades"></a>Doplněk pro kontroly stavu během upgradu
Pro účely upgradu musí být nastaveny zásady stavu (nebo mohou být použity výchozí hodnoty). Upgrade se nazývá úspěšné při upgradu všech aktualizačních domén v rámci zadané vypršení časových limitů a všech aktualizačních domén se považují za v pořádku.  V pořádku aktualizační doména znamená, že aktualizační doména předány kontroly stavu uveden v zásadách stavu. Například může zásady stavu stanoví, že všechny služby v rámci instancí aplikace musí být *v pořádku*, je dle stavu Service Fabric.

Zásady stavu a kontrol během upgradu v Service Fabric jsou nezávislá na služby a aplikace. To znamená jsou prováděny žádné testy specifické pro služby.  Třeba vaše služba může mít požadavek na propustnost, ale Service Fabric nemá žádné informace ke kontrole propustnost. Odkazovat [stavu články](service-fabric-health-introduction.md) kontroly, které se provádí. Kontroly, ke kterým dochází při upgradu zahrnout testy pro Určuje, zda byl balíček aplikace zkopíroval správně, zda byla spuštěna instance a tak dále.

Připravenost aplikace není agregaci podřízené entity aplikace. Stručně řečeno Service Fabric vyhodnocuje stav aplikací prostřednictvím služby health, která se použije v hlášení v aplikaci. Také vyhodnotí stav všech služeb pro aplikaci tímto způsobem. Service Fabric další vyhodnocuje stav aplikačních služeb na základě agregace stav jejich podřízené prvky, jako je například repliku služby. Po ověření zásady stavu aplikace upgradu pokračovat. Pokud porušení zásad stavu, nezdaří se upgrade aplikace.

## <a name="upgrade-modes"></a>Upgrade režimy
Režim, který doporučujeme pro upgrade aplikace je monitorovaných režimu, který je běžně používané režimem. Monitorované režimu provede upgrade na jednu aktualizační doménu a je-li doplněk pro kontroly stavu všech pass (podle zásad určených) přejde k další aktualizační domény automaticky.  Selhání kontroly stavu a/nebo časové limity se dosáhne, upgrade je buď vrátit zpět pro aktualizační doména, zda je režim změnit na nemonitorovaný ruční. Můžete nakonfigurovat upgradu zvolte jednu z těchto dvou režimů selhání upgradu na verzi. 

Nemonitorované ruční režim vyžaduje ruční zásah po každém upgradu na aktualizační doména, aktivovala aktualizaci na další aktualizační domény. Jsou prováděny žádné kontroly stavu Service Fabric. Správce provádí kontroly stavu nebo stavu před spuštěním upgradu v další aktualizační domény.

## <a name="upgrade-default-services"></a>Aktualizace výchozí služby
Některé výchozí služby parametry definované v [manifest aplikace](service-fabric-application-and-service-manifests.md) můžete také upgradovat, protože při upgradu aplikace. Pouze služba parametry, které podporují mění prostřednictvím [aktualizace ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) lze změnit jako součást upgradu. Chování Změna výchozích služeb během upgradu aplikace vypadá takto:

1. Vytvářejí se výchozí služby nový manifest aplikace, které už neexistují v clusteru.
2. Aktualizují se výchozí služby, které existují v manifestech předchozí a nové aplikace. Parametry výchozí služba v nový manifest aplikace přepsat parametry existující služby. Upgrade aplikace vrátí zpět automaticky, pokud se aktualizace výchozí služby nezdaří.
3. Pokud existují v clusteru, odstraní se výchozí služby, které neexistují v nový manifest aplikace. **Všimněte si, že odstraňuje se služba výchozí způsobí odstranění všech funkcí, které služba stavu a nesmí být vrátit zpět.**

Při upgradu aplikace se vrátí zpět, výchozí parametry služby jsou vráceny zpět na původní hodnoty, než upgrade spustil, ale odstraněné services nemůže být znovu vytvořena s jejich starý stav.

> [!TIP]
> Tím [EnableDefaultServicesUpgrade](service-fabric-cluster-fabric-settings.md) nastavení konfigurace clusteru musí být *true* povolit pravidla 2) a 3) nad (výchozí služby aktualizace a odstranění). Tato funkce je podporované počínaje Service Fabric verze 5.5.

## <a name="upgrading-multiple-applications-with-https-endpoints"></a>Upgrade více aplikací pomocí koncových bodů HTTPS
Budete muset dejte pozor, abyste používat **stejný port** pro různé instance stejné aplikace při používání protokolu HTTP**S**. Důvodem je, že Service Fabric nebudou moct upgradovat certifikátu pro jeden z instancí aplikace. Pokud například aplikace 1 nebo aplikace 2 oba chtějí upgradovat jejich cert 1 na certifikátu. 2. Při upgradu dojde, Service Fabric může vyčistili registrační certifikát 1 se souborem http.sys i v případě, že ji stále používá jiná aplikace. Chcete-li tomu zabránit, Service Fabric zjistí, že je již jiná instance aplikace zaregistrované na portu s certifikátem (z důvodu http.sys) a operace se nezdaří.

Proto Service Fabric nepodporuje upgrade dvou různých služeb pomocí **stejný port** v různé instance aplikace. Jinými slovy nelze použít stejný certifikát na různé služby na stejném portu. Pokud je potřeba mít sdílené certifikát na stejném portu, je potřeba zajistit, že služby jsou umístěné na různých počítačích pomocí omezení umístění. Nebo zvažte použití dynamické porty Service Fabric, pokud je to možné pro každou službu v každé instanci aplikace. 

Pokud se zobrazí upgradu selhání pomocí protokolu https, chybu upozornění oznamující "Rozhraní API Windows HTTP serveru nepodporuje více certifikátů pro aplikace, které sdílejí portu."

## <a name="application-upgrade-flowchart"></a>Vývojový diagram upgradu aplikace
Vývojový diagram za tímto odstavcem pomůže vám porozumět procesu upgradu aplikace Service Fabric. Konkrétně se tok popisuje jak časové limity, včetně *HealthCheckStableDuration*, *HealthCheckRetryTimeout*, a *UpgradeHealthCheckInterval*, Nápověda ovládací prvek při upgradu v jedné aktualizační doméně se považuje za úspěch nebo selhání.

![Proces upgradu aplikace Service Fabric][image]

## <a name="next-steps"></a>Další postup
[Upgrade aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md) vás provede upgrade aplikace pomocí sady Visual Studio.

[Upgrade aplikace pomocí Powershellu](service-fabric-application-upgrade-tutorial-powershell.md) vás provede upgrade aplikace pomocí Powershellu.

Řídí, jak vaše aplikace upgradovala pomocí [parametry upgradu](service-fabric-application-upgrade-parameters.md).

Díky upgradů aplikace kompatibilní učit, jak používat [serializaci dat](service-fabric-application-upgrade-data-serialization.md).

Zjistěte, jak používat pokročilé funkce při upgradu aplikace rekapitulací [Pokročilá témata](service-fabric-application-upgrade-advanced.md).

Vyřešit běžné problémy v upgradech aplikací odkazující na kroky v [řešení potíží s upgrady aplikací](service-fabric-application-upgrade-troubleshooting.md).

[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
