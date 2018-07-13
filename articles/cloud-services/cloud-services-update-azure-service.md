---
title: Postup aktualizace cloudové služby | Dokumentace Microsoftu
description: Zjistěte, jak aktualizovat cloudové služby v Azure. Zjistěte, jak pokračuje aktualizace v cloudové službě k zajištění dostupnosti.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: c6a8b5e6-5c99-454c-9911-5c7ae8d1af63
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: jeconnoc
ms.openlocfilehash: 2f5a82fac18ab34bfa9d6b46f553227ed44a994a
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008089"
---
# <a name="how-to-update-a-cloud-service"></a>Postup aktualizace cloudové služby

Aktualizace cloudové služby, včetně jeho role a hostovaný operační systém, je o třech krocích. Nejprve musí se nahrát binární a konfigurační soubory pro novou cloudovou službu nebo verze operačního systému. Azure si dále vyhrazuje výpočetní a síťové prostředky pro cloudové služby na základě požadavků novou cloudovou verzi služby. Nakonec Azure provádí upgradu se zajištěním provozu přírůstkově aktualizovat tenanta na novou verzi nebo hostovaný operační systém, při zachování vašich dostupnosti. Tento článek popisuje podrobnosti o tomto posledním kroku – upgradu se zajištěním provozu.

## <a name="update-an-azure-service"></a>Aktualizace služby Azure
Azure organizuje vaše instance rolí do logických seskupení volá upgradovací domény (UD). Upgradovací domény (UD) jsou logické sady instancí role, které se aktualizují jako skupinu.  Aktualizace Azure a cloudových služeb jeden UD současně, což umožňuje instance v jiných aktualizačními doménami nadále obsluhuje provoz.

Výchozí počet domén upgradu je 5. Je-li zadat jiný počet upgradovacích domén, včetně upgradeDomainCount atributu v souboru definice služby (.csdef). Další informace o atributu upgradeDomainCount najdete v tématu [WebRole schéma](https://msdn.microsoft.com/library/azure/gg557553.aspx) nebo [WorkerRole schéma](https://msdn.microsoft.com/library/azure/gg557552.aspx).

Při provádění v místě aktualizace jeden nebo více rolí ve službě Azure aktualizuje sadu instancí role podle upgradovací domény, ke kterému patří. Aktualizace Azure, které všechny instance v dané doméně upgradu – zastavení, aktualizuje, uvede zpět online – pak přesune na další doménu. Zastavením pouze instance spuštěné v aktuální upgradovací doméně Azure zajišťuje, že aktualizace dochází s nejmenší dopad na spuštěnou službu. Další informace najdete v tématu [jak pokračuje aktualizace](#howanupgradeproceeds) dále v tomto článku.

> [!NOTE]
> Zatímco podmínky **aktualizovat** a **upgradovat** mají mírně odlišný význam v rámci Azure, mohou být použity Zaměnitelně pro procesy a popis jednotlivých funkcí v tomto dokumentu.
>
>

Vaše služba musí definovat aspoň dvě instance role pro danou roli aktualizace na místě bez jakýchkoli prostojů. Pokud služba obsahuje jenom jeden výskyt jedné role, vaše služba nebude k dispozici, až do dokončení aktualizace na místě.

Toto téma obsahuje následující informace o Azure aktualizace:

* [Během aktualizace povoleny změny služby](#AllowedChanges)
* [Jak pokračuje upgradu](#howanupgradeproceeds)
* [Vrácení zpět aktualizace](#RollbackofanUpdate)
* [Zahájení více mutující operací na probíhající nasazení](#multiplemutatingoperations)
* [Distribuce rolí napříč upgradovací domény](#distributiondfroles)

<a name="AllowedChanges"></a>

## <a name="allowed-service-changes-during-an-update"></a>Během aktualizace povoleny změny služby
V následující tabulce jsou uvedeny povolené změny do služby během aktualizace:

| Povoleny změny pro hostování, služby a role | Aktualizace na místě | Dvoufázové instalace (prohození virtuálních IP adres) | Odstranit a znovu nasadit |
| --- | --- | --- | --- |
| Verze operačního systému |Ano |Ano |Ano |
| Úrovně důvěryhodnosti .NET |Ano |Ano |Ano |
| Velikost virtuálního počítače<sup>1</sup> |Ano<sup>2</sup> |Ano |Ano |
| Nastavení místního úložiště |Pouze zvýšit<sup>2</sup> |Ano |Ano |
| Přidání nebo odebrání role ve službě |Ano |Ano |Ano |
| Počet instancí konkrétní roli |Ano |Ano |Ano |
| Číslo nebo typ koncových bodů služby |Ano<sup>2</sup> |Ne |Ano |
| Názvy a hodnoty nastavení konfigurace |Ano |Ano |Ano |
| Hodnoty (ale ne názvy) nastavení konfigurace |Ano |Ano |Ano |
| Přidat nové certifikáty |Ano |Ano |Ano |
| Změna existujících certifikátů |Ano |Ano |Ano |
| Nasazení nového kódu |Ano |Ano |Ano |

<sup>1</sup> velikost změnu omezené na podmnožinu dostupných pro cloudovou službu.

<sup>2</sup> vyžaduje Azure SDK 1.5 nebo novější verze.

> [!WARNING]
> Změna velikosti virtuálního počítače způsobí zničení místní data.
>
>

Během aktualizace se nepodporují následující položky:

* Změna názvu role. Odebrat a potom přidejte roli s novým názvem.
* Změnit počet domén upgradu.
* Zmenšit velikost místních prostředků.

Pokud provádíte jiné aktualizace definice vaší služby, jako je například zmenšit velikost místního prostředku, je nutné provést aktualizaci prohození virtuálních IP adres. Další informace najdete v tématu [Prohodit nasazení](https://msdn.microsoft.com/library/azure/ee460814.aspx).

<a name="howanupgradeproceeds"></a>

## <a name="how-an-upgrade-proceeds"></a>Jak pokračuje upgradu
Můžete se rozhodnout, jestli chcete aktualizovat všechny role ve službě nebo jedné role ve službě. V obou případech jsou všechny instance každé role, která se upgraduje a patří do první upgradovací doména zastavena, upgradovat a opět nepřipojí online. Poté, co jsou zpátky do online režimu, instance v druhé upgradovací domény jsou zastavena, upgradovat a opět nepřipojí online. Cloudové služby může mít maximálně jeden upgrade v každém okamžiku aktivní. Upgrade se vždy provádí na nejnovější verzi cloudovou službu.

Následující diagram znázorňuje, jak pokračuje v upgradu při upgradu všech rolí ve službě:

![Upgradovat službu](media/cloud-services-update-azure-service/IC345879.png "Upgrade služby")

Tento další diagram znázorňuje, jak aktualizace pokračuje, pokud provádíte upgrade jedné role:

![Upgrade role](media/cloud-services-update-azure-service/IC345880.png "upgradu role")  

Kontroler prostředků infrastruktury Azure během automatických aktualizací, pravidelně vyhodnocuje stav cloudové služby můžete zjistit, kdy je bezpečné provedou další UD. Toto vyhodnocení stavu se provádí na základě specifických pro konkrétní role a bere v úvahu pouze instance v nejnovější verzi (tj. instance ze aktualizačními doménami, které již byly šel). Ověřuje, že minimální počet instancí role, pro každou roli dosáhli uspokojivé konečného stavu.

### <a name="role-instance-start-timeout"></a>Časový limit spuštění Instance role
Kontroler prostředků infrastruktury se Počkejte 30 minut pro každou instanci role dosáhne stavu spuštěno. Dobu trvání časového limitu uplyne, kontroler prostředků infrastruktury budou procházení k dalším instance role.

### <a name="impact-to-drive-data-during-cloud-service-upgrades"></a>Upgraduje dopadu na jednotce dat během cloudové služby

Při upgradu služby z jedné instance na několik instancí služby vznášet během upgradu je provést z důvodu služeb Azure upgrady způsobem. Záruční služby dostupnost smlouvu o úrovni služeb se vztahuje pouze na služby, které se nasazují s více než jednu instanci. Následující seznam popisuje, jak data na každou jednotku je ovlivněna každý scénář upgradu služby Azure:

|Scénář|Jednotce C|Jednotky D|Jednotce E:|
|--------|-------|-------|-------|
|Restartování virtuálního počítače|Zachovány|Zachovány|Zachovány|
|Restartování portálu|Zachovány|Zachovány|Zničení|
|Portálu obnovení z Image|Zachovány|Zničení|Zničení|
|Místní Upgrade|Zachovány|Zachovány|Zničení|
|Přenesení uzlu|Zničení|Zničení|Zničení|

Mějte na paměti, že v seznamu výše jednotky představuje role kořenové jednotce a by neměl být pevně zakódované. Místo toho použijte **RoleRoot %** proměnnou prostředí k reprezentaci jednotce.

Chcete-li minimalizovat prostoje při upgradu služby jednou instancí, nasazení nové služby s více instancemi na testovacím serveru a provést prohození virtuálních IP adres.

<a name="RollbackofanUpdate"></a>

## <a name="rollback-of-an-update"></a>Vrácení zpět aktualizace
Azure poskytuje flexibilitu při správě služby během aktualizace umožňují zahájit další operace v rámci služby, po přijetí žádosti počáteční aktualizace podle kontroler prostředků infrastruktury Azure. Vrácení zpět lze provést pouze v případě aktualizace (Změna konfigurace) nebo upgrade **probíhá** stavu na nasazení. Aktualizaci nebo upgradu se považuje za probíhá, dokud existuje alespoň jedna instance služby, která ještě neaktualizoval na novou verzi. K otestování, jestli smí vrácení zpět, zkontrolujte hodnotu příznaku RollbackAllowed, vrácený [získat nasazení](https://msdn.microsoft.com/library/azure/ee460804.aspx) a [získat vlastnosti cloudové služby](https://msdn.microsoft.com/library/azure/ee460806.aspx) operace, je nastavena na hodnotu true.

> [!NOTE]
> Je vhodné volat vrácení zpět **místní** aktualizovat nebo upgradovat, protože upgrady prohození virtuálních IP adres zahrnují nahrazení celého jednu běžící instanci služby s jiným.
>
>

Vrátit zpět změny v průběhu aktualizace má následující důsledky při nasazení:

* Všechny instance rolí, které bylo ještě nebyly aktualizovány nebo upgradovat na novou verzi se aktualizovat nebo upgradovat, protože tyto instance jsou již spuštěny cílovou verzi služby.
* Všechny instance rolí, které již byly aktualizovat nebo upgradovat na novou verzi balíčku služby (\*.cspkg) souboru nebo konfigurace služby (\*.cscfg) soubor (nebo oba soubory) jsou vráceny do před upgradem verze těchto souborů.

Tato funkce poskytuje následující funkce:

* [Vrácení zpět aktualizace nebo upgradu](https://msdn.microsoft.com/library/azure/hh403977.aspx) operace, které je možné vyvolat v aktualizaci konfigurace (aktivuje voláním [změna konfigurace nasazení](https://msdn.microsoft.com/library/azure/ee460809.aspx)) nebo upgradu (aktivuje voláním [ Upgrade nasazení](https://msdn.microsoft.com/library/azure/ee460793.aspx)), dokud je alespoň jednou instancí služby, které ještě neaktualizoval na novou verzi.
* Element uzamčené a RollbackAllowed elementu, které jsou následně vráceny jako součást text odpovědi o [získat nasazení](https://msdn.microsoft.com/library/azure/ee460804.aspx) a [získat vlastnosti cloudové služby](https://msdn.microsoft.com/library/azure/ee460806.aspx) operace:

  1. Element uzamčené umožňuje rozpoznat, kdy mutující operace může být vyvolána v zadaném nasazení.
  2. RollbackAllowed element slouží ke zjištění, kdy [vrácení zpět aktualizace nebo upgradu](https://msdn.microsoft.com/library/azure/hh403977.aspx) operace lze volat pro konkrétní nasazení.

  Aby bylo možné provést vrácení zpět, není nutné uzamčeno a RollbackAllowed elementy. Stačí potvrdit, že RollbackAllowed nastavený na hodnotu true. Tyto prvky jsou vráceny pouze, pokud tyto metody jsou vyvolány pomocí hlavičky požadavku nastavte na "x-ms-version: 2011-10-01" nebo novější. Další informace o hlavičkách správy verzí, naleznete v tématu [Správa verzí služby správy](https://msdn.microsoft.com/library/azure/gg592580.aspx).

Existují určité situace, kdy vrácení zpět aktualizace nebo upgrade se nepodporuje, ty jsou následující:

* Snížení místní prostředky – aktualizace se zvyšuje místních prostředků pro role na platformě Azure neumožňuje vrácení zpět.
* Omezení kvóty – Pokud aktualizace byl vertikálního snižování kapacity operace, kterou jste už může mít dostatečná kvóta výpočetních prostředků k dokončení operace vrácení zpět. Každé předplatné Azure má kvótu, která určuje maximální počet jader, které mohou být spotřebovány všechny hostované služby, které patří k tomuto předplatnému. Je-li provést vrácení dané aktualizace vložili předplatné přes kvótu pak, která nepovolí vrácení zpět.
* Konflikt časování - li počáteční aktualizace byla dokončena, vrácení zpět není možný.

Je například při vrácení zpět aktualizace může být užitečné, pokud používáte [nasazení upgradu](https://msdn.microsoft.com/library/azure/ee460793.aspx) operace v režimu ručního řídit rychlost, jakou hlavní místní upgrade na Azure hostovaná služba nasazení.

Během zavádění upgradu zavoláte [nasazení upgradu](https://msdn.microsoft.com/library/azure/ee460793.aspx) v režimu ručního a začít procházet upgradovacích domén. Pokud v určitém okamžiku, jak můžete monitorovat upgrade, jste si některé instance rolí v první upgradovacích domén, které můžete zkontrolovat staly reagovat, můžete volat [vrácení zpět aktualizace nebo upgradu](https://msdn.microsoft.com/library/azure/hh403977.aspx) operaci s nasazením, zůstane zůstanou instance, které nebyly dosud nebyly upgradovány a vrácení zpět instance, které upgradovali na předchozí balíček služby a konfiguraci.

<a name="multiplemutatingoperations"></a>

## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Zahájení více mutující operací na probíhající nasazení
V některých případech můžete chtít spustit více souběžných operací mutující průběžné nasazení. Například může provádět aktualizace služby a tuto aktualizaci se zavádějí napříč vaší služby, ale chcete třeba provést některé změny k vrácení zpět aktualizace, použití různých aktualizace nebo dokonce odstranit nasazení. Případ, ve kterém to může být nezbytné je-li upgrade služby obsahuje chybový kód, což způsobí, že upgradovaná role instance opakovaně k chybě. V takovém případě kontroler prostředků infrastruktury Azure nebude možné pokroku při uplatňování, upgradovat, protože dostatečný počet instancí v upgradované domény jsou v pořádku. Tento stav se označuje jako *zablokované nasazení*. Nasazení můžete unstick vrácení zpět aktualizace nebo použití funkce aktualizace v horní části neúspěšného jeden.

Po počáteční požadavek na aktualizaci nebo upgradu služby obdržel kontroler prostředků infrastruktury Azure, můžete spustit následující mutující operací. To znamená, že nemáte čekat pro počáteční operaci dokončit před zahájením mutující jiná operace.

Inicializace druhou operaci aktualizace, zatímco probíhá první aktualizací provede podobný operaci vrácení zpět. Pokud je druhý aktualizace v automatickém režimu, první upgradovací doména se upgradují okamžitě, by mohl vést k instancím z více upgradovacích doménách budou offline stejného bodu v čase.

Mutující operace jsou následující: [změna konfigurace nasazení](https://msdn.microsoft.com/library/azure/ee460809.aspx), [nasazení upgradu](https://msdn.microsoft.com/library/azure/ee460793.aspx), [stav nasazení aktualizace](https://msdn.microsoft.com/library/azure/ee460808.aspx), [odstranit nasazení ](https://msdn.microsoft.com/library/azure/ee460815.aspx), a [vrácení zpět aktualizace nebo upgradu](https://msdn.microsoft.com/library/azure/hh403977.aspx).

Dvě operace [získat nasazení](https://msdn.microsoft.com/library/azure/ee460804.aspx) a [získat vlastnosti cloudové služby](https://msdn.microsoft.com/library/azure/ee460806.aspx), vrátí uzamčené příznak, který můžete prověřit, abyste zjistili, jestli mutující operaci lze vyvolat u dané nasazení.

Volání verzi tyto metody, která vrátí příznak uzamknout, je nutné nastavit hlavičku požadavku na "x-ms-version: 2011-10-01" nebo pozdější. Další informace o hlavičkách správy verzí, naleznete v tématu [Správa verzí služby správy](https://msdn.microsoft.com/library/azure/gg592580.aspx).

<a name="distributiondfroles"></a>

## <a name="distribution-of-roles-across-upgrade-domains"></a>Distribuce rolí napříč upgradovací domény
Instance rolí Azure rovnoměrně distribuuje mezi sadu počet upgradovacích domén, které lze konfigurovat jako součást souboru definičních (.csdef) služby. Výchozí hodnota je 5 maximální počet domén upgradu je 20. Další informace o tom, jak upravit soubor definice služby najdete v tématu [Azure schématu definice služby (.csdef souboru)](cloud-services-model-and-package.md#csdef).

Například pokud má vaše role deseti instancí, ve výchozím nastavení každý upgradovací doména obsahuje dvě instance. Pokud vaše role má 14 instancí, pak čtyři upgradovacích doménách budou obsahovat tři instance a pátý domény obsahuje dva.

Upgradovací domény jsou označeny index založený na nule: první upgradovací doména má ID 0 a druhý upgradovací doména má ID 1 a tak dále.

Následující diagram znázorňuje, jak službu než obsahuje dvě role jsou distribuovány v případě služby definuje dvě upgradovacích domén. Se službou osmi instancí webové role a devět instance role pracovního procesu.

![Distribuce Upgradovacích doménách](media/cloud-services-update-azure-service/IC345533.png "distribuci Upgradovacích domén")

> [!NOTE]
> Všimněte si, že Azure řídí, jak přidělovat instance napříč upgradovací domény. Není možné určit, která instance jsou přiděleny které domény.
>
>

## <a name="next-steps"></a>Další postup
[Jak spravovat Cloud Services](cloud-services-how-to-manage-portal.md)  
[Jak monitorovat Cloud Services](cloud-services-how-to-monitor.md)  
[Jak konfigurovat Cloud Services](cloud-services-how-to-configure-portal.md)  
