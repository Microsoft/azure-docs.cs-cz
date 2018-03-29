---
title: Upgrade aplikace Service Fabric | Microsoft Docs
description: Tento článek obsahuje úvod do upgradu aplikace Service Fabric, včetně výběrem možnosti upgradu režimy a provádění kontroly stavu.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: 803c9c63-373a-4d6a-8ef2-ea97e16e88dd
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 60bbd75496b6e835a76edb4251aac6ea249187b3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="service-fabric-application-upgrade"></a>Upgrade aplikace Service Fabric
Aplikace Azure Service Fabric je kolekce služeb. Během upgradu, porovná Service Fabric nové [manifest aplikace](service-fabric-application-and-service-manifests.md) v předchozí verzi a určuje, které služby v uzlu aktualizace vyžadovat aplikace. Service Fabric porovnává verze čísla ve službě manifesty s čísla verze v předchozí verzi. Je-li služba se nezměnila, není aktualizován dané služby.

## <a name="rolling-upgrades-overview"></a>Vrácení upgradu – přehled
V postupného upgradu aplikace upgradu provádí ve fázích. V každé fázi upgradu použijí pro dílčí sadu uzlů v clusteru, názvem domény služby aktualizace. V důsledku toho zůstává k dispozici v rámci upgradu aplikace. Během upgradu clusteru může obsahovat kombinací staré a nové verze.

Z tohoto důvodu musí být dvě verze dopředného a zpětně kompatibilní. Pokud nejsou kompatibilní, Správce aplikací je zodpovědná za pracovní více fází upgradu údržbu dostupnosti. V případě více fází upgradu prvním krokem je upgrade zprostředkující verzi aplikace, která je kompatibilní s předchozí verzí. Druhým krokem je upgrade finální verzi, která dělí kompatibilitu s verzí před aktualizací, ale je kompatibilní s verzí zprostředkující.

Aktualizace domény jsou určené v manifestu clusteru při konfiguraci clusteru. Aktualizace domén neobdrží aktualizace v určitém pořadí. Doména aktualizace je logická jednotka nasazení pro aplikaci. Aktualizace domén povolit služby, aby zůstaly během upgradu na vysokou dostupnost.

Bez vrácení upgrady je možné, pokud je na všech uzlech v clusteru, což je případ, kdy aplikace má pouze jednu aktualizační doménu upgradu. Tento přístup se nedoporučuje, protože služba přestane fungovat a není k dispozici při upgradu. Kromě toho Azure neposkytuje žádné záruky, při nastavení clusteru s doménou pouze jednu aktualizaci.

Po dokončení upgradu, všechny služby a replicas(instances) by zůstat ve stejné verzi – tj, jestliže upgrade úspěšné, bude aktualizován na novou verzi; Pokud upgrade selže, je vrácena, že by se vrátit zpět na předchozí verzi aplikace.

## <a name="health-checks-during-upgrades"></a>Kontroly stavu během upgradu
Pro upgrade musí být nastaveny zásady stavu (nebo mohou být použity výchozí hodnoty). Upgrade se říká úspěšné, pokud jsou všechny aktualizace domény upgradován během zadaného vypršení časových limitů a pokud všechny domény aktualizace se považují za v pořádku.  Domény v pořádku aktualizace znamená, že aktualizace domény předány všechny kontroly stavu určená v zásadách stavu. Například může zásady stavu vyžádá, musí být všechny služby v rámci instance aplikace *pořádku*, jak je definováno stavu Service Fabric.

Zásady stavu a kontroly během upgradu pomocí Service Fabric se bez ohledu na služby a aplikace. To znamená, že se provádějí žádné testy specifickou pro službu.  Například služby může mít požadavek propustnost, ale Service Fabric nemá informace ke kontrole propustnost. Odkazovat [stavu články](service-fabric-health-introduction.md) pro kontroly, které se provádí. Kontroly, které dojít během upgradu zahrnout testy pro jestli byl balíček aplikace správně zkopírován, zda byla spuštěna instance a tak dále.

Stav aplikace je agregaci podřízených entit aplikace. Stručně řečeno Service Fabric vyhodnocuje stav aplikace prostřednictvím stavu, který je hlášen na aplikaci. Také vyhodnocuje stav všech služeb pro aplikace tímto způsobem. Service Fabric další vyhodnocuje stav aplikačních služeb agregací stav jejich podřízené položky, jako je například služba repliky. Po splňují zásady stavu aplikace, můžete pokračovat v upgradu. Pokud je porušení zásad stavu, upgradu aplikace se nezdaří.

## <a name="upgrade-modes"></a>Upgrade režimy
Režim, který doporučujeme pro upgradu aplikace je monitorovaných režimu, který je běžně používané režimem. Monitorovaných režimu provede upgrade na jednu aktualizační doménu a pokud kontroluje všechny stavy průchodu (podle zásad určených), přejde k další domény aktualizace automaticky.  Pokud selhání kontroly stavu a/nebo se dosáhne vypršení časových limitů, upgrade je pro doménu aktualizace buď vrácena nebo režim se změní na není sledována ručně. Můžete nakonfigurovat upgrade na vyberte jednu z těchto dvou režimů pro upgrade se nezdařilo. 

Sledována ruční režim musí ruční zásah po každém upgradu v doméně služby aktualizace, chcete-li ji upgradu na další aktualizaci domény. Budou provedeny žádné kontroly stavu Service Fabric. Správce provádí kontroly stavu nebo stavu před spuštěním upgradu v další aktualizaci domény.

## <a name="upgrade-default-services"></a>Upgradujte výchozí služby
Některé výchozí služby parametry definované v [manifest aplikace](service-fabric-application-and-service-manifests.md) lze také upgradovat v rámci upgradu aplikace. Pouze služba parametry, které podporují mění prostřednictvím [aktualizace ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) lze změnit jako součást upgradu. Chování Změna výchozích služeb během upgradu aplikace je následující:

1. Výchozí služby v nové manifestu aplikace, které už neexistují v clusteru se vytvoří.
2. Výchozí služby, které existují v manifestech předchozí a nové aplikace jsou aktualizované. Parametry výchozí služba v nové manifest aplikace přepsat parametry existující službu. Upgradu aplikace bude vrácení zpět automaticky, pokud se nezdaří aktualizace výchozí služby.
3. Výchozí služby, které nejsou k dispozici v nové manifest aplikace jsou odstraněny, pokud existují v clusteru. **Všimněte si, že odstraněním služby výchozí způsobí odstranění všechno, co služba stavu a nesmí být vrátit zpět.**

Při upgradu aplikace je vrácena, parametry výchozí služby se vrátit zpět na původní hodnoty před upgrade spustit ale odstraněné služby nelze znovu vytvořit s jejich původním stavu.

> [!TIP]
> [EnableDefaultServicesUpgrade](service-fabric-cluster-fabric-settings.md) nastavení konfigurace clusteru musí být *true* a povolte tak pravidla 2) a 3) nad (výchozí služby aktualizace a odstranění). Tato funkce je podporovaná počínaje Service Fabric verzi 5.5.

## <a name="upgrading-multiple-applications-with-https-endpoints"></a>Upgrade více aplikací s koncovými body HTTPS
Budete muset pečlivě nepoužívat **stejný port** pro různé instance stejné aplikace při používání protokolu HTTP**S**. Důvodem je, že Service Fabric, nebude možné upgradovat certifikátu pro jednu z instancí aplikace. Například pokud aplikace 1 nebo aplikace 2 obou chcete upgradovat jejich cert 1 cert 2. Při upgradu se stane, Service Fabric může mít vyčistit cert 1 registrace pomocí ovladače http.sys i když se stále používá jiná aplikace. Chcete-li tomu zabránit, Service Fabric zjistí, že se už používá jiná instance aplikace zaregistrované na portu s certifikátem (z důvodu http.sys) a operaci se nezdaří.

Proto Service Fabric nepodporuje upgrade dvě různé služby pomocí **stejný port** v případech jinou aplikaci. Jinými slovy nelze použít stejný certifikát na různé služby na stejném portu. Pokud potřebujete mít sdílené certifikát na stejném portu, je třeba zajistit, že služby jsou umístěny na různých počítačích s omezeními umístění. Nebo zvažte, pokud je to možné pomocí Service Fabric dynamické porty pro každou službu v každé instanci aplikace. 

Pokud se zobrazí upgradu služeb při selhání s protokolem https, chybu upozornění oznamující "Rozhraní API systému Windows HTTP serveru nepodporuje víc certifikátů pro aplikace, které sdílejí port."

## <a name="application-upgrade-flowchart"></a>Vývojový diagram upgradu aplikace
Vývojový diagram níže vám může pomoct pochopit proces upgradu aplikace Service Fabric. Konkrétně toku popisuje jak vypršení časových limitů, včetně *HealthCheckStableDuration*, *HealthCheckRetryTimeout*, a *UpgradeHealthCheckInterval*, pomůže ovládací prvek při upgradu v jedné aktualizační doméně se považuje za úspěch nebo selhání.

![Proces upgradu pro Fabric aplikaci služby][image]

## <a name="next-steps"></a>Další postup
[Upgrade vaší aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md) vás provede upgrade aplikace pomocí sady Visual Studio.

[Upgrade vaší aplikace pomocí prostředí Powershell](service-fabric-application-upgrade-tutorial-powershell.md) vás provede upgrade aplikace pomocí prostředí PowerShell.

Řídí, jak vaše aplikace upgraduje pomocí [Upgrade parametry](service-fabric-application-upgrade-parameters.md).

Zkontrolujte upgradů aplikace kompatibilní podle naučit se používat [serializace dat](service-fabric-application-upgrade-data-serialization.md).

Další informace o použití pokročilých funkcí při upgradu vaší aplikace tím, že odkazuje na [Pokročilá témata](service-fabric-application-upgrade-advanced.md).

Řešení běžných potíží v upgradů aplikací podle kroků v části [řešení potíží s aplikací upgrady](service-fabric-application-upgrade-troubleshooting.md).

[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
