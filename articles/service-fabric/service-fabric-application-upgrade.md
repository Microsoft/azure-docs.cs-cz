---
title: Upgrade aplikace Service Fabric
description: Tento článek obsahuje úvod do inovace aplikace Service Fabric, včetně výběru režimů upgradu a provádění kontrol stavu.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 2dc484b49c5250510e5f018cbbc2da107573d452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259042"
---
# <a name="service-fabric-application-upgrade"></a>Upgrade aplikace Service Fabric
Aplikace Azure Service Fabric je kolekce služeb. Během upgradu Service Fabric porovná nový [manifest aplikace](service-fabric-application-and-service-manifests.md) s předchozí verzí a určuje, které služby v aplikaci vyžadují aktualizace. Service Fabric porovnává čísla verzí v manifestech služby s čísly verzí v předchozí verzi. Pokud se služba nezměnila, nebude upgradována.

## <a name="rolling-upgrades-overview"></a>Přehled postupných upgradů
Při postupném upgradu aplikace se upgrade provádí postupně. V každé fázi je upgrade použit na podmnožinu uzlů v clusteru, která se nazývá aktualizační doména. V důsledku toho aplikace zůstane k dispozici po celou dobu upgradu. Během upgradu může cluster obsahovat kombinaci staré a nové verze.

Z tohoto důvodu musí být obě verze kompatibilní dopředu a dozadu. Pokud nejsou kompatibilní, správce aplikace je zodpovědný za přípravu vícefázového upgradu k udržení dostupnosti. V upgradu více fází je prvním krokem upgrade na zprostředkující verzi aplikace, která je kompatibilní s předchozí verzí. Druhým krokem je upgrade konečné verze, která přeruší kompatibilitu s předaktualizací, ale je kompatibilní s zprostředkující verzí.

Aktualizační domény jsou určeny v manifestu clusteru při konfiguraci clusteru. Aktualizační domény nedostávají aktualizace v určitém pořadí. Aktualizační doména je logická jednotka nasazení pro aplikaci. Aktualizační domény umožňují, aby služby zůstaly během upgradu na vysoké dostupnosti.

Nepostupné upgrady jsou možné, pokud je upgrade použit na všechny uzly v clusteru, což je případ, kdy má aplikace pouze jednu aktualizační doménu. Tento přístup se nedoporučuje, protože služba přejde dolů a není k dispozici v době upgradu. Azure navíc neposkytuje žádné záruky, když je cluster nastavený pouze s jednou doménou aktualizace.

Po dokončení upgradu všechny služby a repliky (instance) zůstanou ve stejné verzi, tj. Pokud se upgrade nezdaří a je vrácena zpět, by být vrácena zpět do staré verze.

## <a name="health-checks-during-upgrades"></a>Kontroly stavu během upgradů
Pro upgrade musí být nastaveny zásady stavu (nebo mohou být použity výchozí hodnoty). Upgrade je označován jako úspěšný, pokud jsou všechny aktualizační domény upgradovány v rámci zadaných časových období a všechny aktualizační domény jsou považovány za v pořádku.  V pořádku aktualizovat doménu znamená, že aktualizační domény prošel všechny kontroly stavu zadané v zásadách stavu. Zásady stavu může například nařídit, že všechny služby v rámci instance aplikace musí být *v pořádku*, jako stav je definován Service Fabric.

Zásady stavu a kontroly během upgradu service fabric jsou služby a aplikace agnostik. To znamená, že nejsou prováděny žádné testy specifické pro službu.  Vaše služba může mít například požadavek na propustnost, ale Service Fabric nemá informace ke kontrole propustnost. Naleznete na [stav články](service-fabric-health-introduction.md) pro kontroly, které jsou prováděny. Kontroly, ke kterým dojde během upgradu, zahrnují testy, zda byl balíček aplikace zkopírován správně, zda byla instance spuštěna a tak dále.

Stav aplikace je agregace podřízených entit aplikace. Stručně řečeno Service Fabric vyhodnotí stav aplikace prostřednictvím stavu, který je hlášen v aplikaci. Také vyhodnocuje stav všech služeb pro aplikaci tímto způsobem. Service Fabric dále vyhodnocuje stav aplikačních služeb agregací stavu jejich podřízených, jako je například replika služby. Jakmile je splněna zásada stavu aplikace, upgrade může pokračovat. Pokud je porušena zásada stavu, upgrade aplikace se nezdaří.

## <a name="upgrade-modes"></a>Režimy upgradu
Režim, který doporučujeme pro upgrade aplikace je sledovaný režim, což je běžně používaný režim. Sledovaný režim provádí upgrade na jedné doméně aktualizace a pokud všechny kontroly stavu projít (podle zadané zásady), přesune na další aktualizaci domény automaticky.  Pokud se nezdaří kontroly stavu a/nebo jsou dosaženy časové nedochody, upgrade je vrácena zpět pro aktualizační doménu nebo režim se změní na nemonitorované ruční. Můžete nakonfigurovat upgrade zvolit jeden z těchto dvou režimů pro neúspěšné upgrady. 

Nesledovaný manuální režim vyžaduje ruční zásah po každém upgradu na aktualizační doméně, aby se upgrade na další aktualizační doménu. Nejsou prováděny žádné kontroly stavu service fabric. Správce provede kontroly stavu nebo stavu před zahájením inovace v další doméně aktualizace.

## <a name="upgrade-default-services"></a>Upgrade výchozích služeb
Některé výchozí parametry služby definované v [manifestu aplikace](service-fabric-application-and-service-manifests.md) lze také upgradovat jako součást upgradu aplikace. V rámci upgradu lze změnit pouze parametry služby, které podporují změny prostřednictvím [služby Update-ServiceFabricService.](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) Chování změny výchozích služeb během upgradu aplikace je následující:

1. Výchozí služby v novém manifestu aplikace, které ještě neexistují v clusteru jsou vytvořeny.
2. Výchozí služby, které existují v předchozích i nových manifestech aplikace jsou aktualizovány. Parametry výchozí služby v novém manifestu aplikace přepíší parametry existující služby. Upgrade aplikace se automaticky vrátí zpět, pokud se aktualizace výchozí služby nezdaří.
3. Výchozí služby, které neexistují v novém manifestu aplikace, budou odstraněny, pokud existují v clusteru. **Všimněte si, že odstranění výchozí služby bude mít za následek odstranění stavu této služby a nelze vrátit zpět.**

Při vrácení upgradu aplikace zpět, výchozí parametry služby jsou vráceny zpět na své staré hodnoty před upgradem spuštěna, ale odstraněné služby nelze znovu vytvořit s jejich starý majestát.

> [!TIP]
> Aby bylo možné povolit pravidla 2) a 3) vyšší (výchozí aktualizace a odstranění služby), musí být konfigurační nastavení clusteru [EnableDefaultServicesUpgrade](service-fabric-cluster-fabric-settings.md) *pravdivé.* Tato funkce je podporována spuštěním service fabric verze 5.5.

## <a name="upgrading-multiple-applications-with-https-endpoints"></a>Inovace více aplikací pomocí koncových bodů HTTPS
Při použití protokolu HTTP**S**je třeba dávat pozor, abyste nepoužívali **stejný port** pro různé instance stejné aplikace. Důvodem je, že Service Fabric nebude moci upgradovat certifikát pro jednu z instancí aplikace. Například pokud aplikace 1 nebo aplikace 2 oba chtějí upgradovat svůj certifikát 1 na certifikát 2. Když dojde k upgradu, Service Fabric může mít vyčistit certifikát 1 registrace s http.sys i v případě, že druhá aplikace je stále používá. Chcete-li tomu zabránit, Service Fabric zjistí, že již existuje jiná instance aplikace registrované na portu s certifikátem (vzhledem http.sys) a nezdaří operace.

Proto Service Fabric nepodporuje upgrade dvou různých služeb pomocí **stejného portu** v různých instancích aplikace. Jinými slovy nelze použít stejný certifikát na různých službách na stejném portu. Pokud potřebujete mít sdílený certifikát na stejném portu, musíte zajistit, aby služby byly umístěny na různých počítačích s omezeními umístění. Nebo zvažte použití service fabric dynamické porty, pokud je to možné pro každou službu v každé instanci aplikace. 

Pokud se zobrazí selhání upgradu s https, upozornění o chybě "Rozhraní API http serveru systému Windows nepodporuje více certifikátů pro aplikace, které sdílejí port."

## <a name="application-upgrade-flowchart"></a>Vývojový diagram upgradu aplikace
Vývojový diagram následující tento odstavec vám může pomoci pochopit proces upgradu aplikace Service Fabric. Zejména tok popisuje, jak časové toky, včetně *HealthCheckStableDuration*, *HealthCheckRetryTimeout*a *UpgradeHealthCheckInterval*, pomáhají řídit, kdy je upgrade v jedné doméně aktualizace považován za úspěšný nebo neúspěšný.

![Proces upgradu pro aplikaci Service Fabric][image]

## <a name="next-steps"></a>Další kroky
[Inovace aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md) vás provede upgradem aplikace pomocí sady Visual Studio.

[Upgrade aplikace pomocí PowerShellu](service-fabric-application-upgrade-tutorial-powershell.md) vás provede upgradem aplikace pomocí PowerShellu.

Pomocí funkce [Parametry upgradu](service-fabric-application-upgrade-parameters.md)můžete řídit způsob upgradu aplikace .

Pomocí aplikace můžete upgrady aplikací provést tak, že se naučíte používat [serializaci dat](service-fabric-application-upgrade-data-serialization.md).

Naučte se používat pokročilé funkce při inovaci aplikace odkazem na [upřesnit témata](service-fabric-application-upgrade-advanced.md).

Běžné problémy při upgradu aplikací opravíte odkazem na kroky při [řešení potíží s inovacemi aplikací](service-fabric-application-upgrade-troubleshooting.md).

[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
