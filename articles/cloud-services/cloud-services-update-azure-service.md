---
title: Jak aktualizovat Cloudová služba | Microsoft Docs
description: Zjistěte, jak aktualizovat cloudové služby v Azure. Zjistěte, jak se aktualizace v cloudové službě pokračuje k zajištění dostupnosti.
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
ms.assetid: c6a8b5e6-5c99-454c-9911-5c7ae8d1af63
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: adegeo
ms.openlocfilehash: 36d4ee9dabd39f4103d17455e47521b378af6ebb
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2017
ms.locfileid: "24860667"
---
# <a name="how-to-update-a-cloud-service"></a>Postup aktualizace cloudové služby

Aktualizace cloudové služby, včetně jeho role a hostovaný operační systém, je proces tři krok. První musí se nahrát binární soubory a konfigurační soubory pro novou cloudovou službu nebo verze operačního systému. V dalším kroku Azure si vyhrazuje výpočetní a síťové prostředky pro cloudové služby na základě požadavků na nové cloudovou verzi služby. Nakonec Azure provádí přírůstkové aktualizace klienta na novou verzi nebo hostovaný operační systém, při zachování dostupnosti vaší postupného upgradu. Tento článek popisuje podrobnosti o tomto posledním kroku – postupného upgradu.

## <a name="update-an-azure-service"></a>Aktualizace služby Azure
Azure umožňuje instance role uspořádat do logických skupin názvem domén upgradu (UD). Upgradu domény (UD) jsou logické skupiny instancí role, které jsou aktualizované jako skupina.  Azure aktualizace a cloudové služby jeden UD současně, což umožňuje instancí v jiných UDs pokračujte s provozem.

Výchozí počet domén upgradu je 5. Můžete zadat jiný počet domén upgradu včetně atribut upgradeDomainCount v souboru definice služby (.csdef). Další informace o atributu upgradeDomainCount najdete v tématu [WebRole schématu](https://msdn.microsoft.com/library/azure/gg557553.aspx) nebo [WorkerRole schématu](https://msdn.microsoft.com/library/azure/gg557552.aspx).

Když provedete aktualizaci na místě jednu nebo více rolí ve službě, aktualizuje Azure sady instancí role podle upgradovací domény, do které patří. Azure aktualizací, které se všechny instance v dané doméně upgradu –, zastavení, aktualizuje, uvede zpět online – pak se posouvá do další domény. Tím zastavíte pouze instance, které jsou spuštěné v aktuální upgradovací doméně, je zajištěno, že aktualizace vyskytuje v nejmenší dopad na spuštěnou službu Azure. Další informace najdete v tématu [jak aktualizace pokračuje](#howanupgradeproceeds) dále v tomto článku.

> [!NOTE]
> Při podmínky **aktualizace** a **upgrade** mají mírně jiný význam v rámci Azure, mohou být použity zcela zaměnitelným významem pro procesy a popis jednotlivých funkcí v tomto dokumentu.
>
>

Služby musí definovat aspoň dvě instance role pro tuto roli aktualizovat na místě bez výpadků. Pokud služba obsahuje pouze jednu instanci jedné role, služby nebude k dispozici, dokud nedokončí aktualizace na místě.

Toto téma obsahuje následující informace o Azure aktualizace:

* [Během aktualizace povoleny změny služby](#AllowedChanges)
* [Jak pokračuje upgradu](#howanupgradeproceeds)
* [Vrácení zpět aktualizace](#RollbackofanUpdate)
* [Spouštění více mutating operací na probíhající nasazení](#multiplemutatingoperations)
* [Distribuce rolí napříč doménami upgradu](#distributiondfroles)

<a name="AllowedChanges"></a>

## <a name="allowed-service-changes-during-an-update"></a>Během aktualizace povoleny změny služby
Následující tabulka ukazuje povolené změny služby během aktualizace:

| Změny umožnit hostování, služeb a rolí | Aktualizace na místě | Dvoufázové instalace (prohození virtuálních IP adres) | Odstranit a znovu nasadit. |
| --- | --- | --- | --- |
| Verze operačního systému |Ano |Ano |Ano |
| Úrovně důvěryhodnosti .NET |Ano |Ano |Ano |
| Velikost virtuálního počítače<sup>1</sup> |Ano<sup>2</sup> |Ano |Ano |
| Nastavení místního úložiště |Pouze zvýšit<sup>2</sup> |Ano |Ano |
| Přidání nebo odebrání rolí ve službě |Ano |Ano |Ano |
| Počet instancí určité role |Ano |Ano |Ano |
| Číslo nebo typ koncových bodů pro služby |Ano<sup>2</sup> |Ne |Ano |
| Názvy a hodnoty nastavení konfigurace |Ano |Ano |Ano |
| Hodnoty (ale ne názvy) nastavení konfigurace |Ano |Ano |Ano |
| Přidat nové certifikáty |Ano |Ano |Ano |
| Změna existující certifikátů |Ano |Ano |Ano |
| Nasazování nového kódu |Ano |Ano |Ano |

<sup>1</sup> velikost změnu omezit na podmnožinu dostupných pro cloudovou službu.

<sup>2</sup> vyžaduje Azure SDK 1.5 nebo novější verze.

> [!WARNING]
> Změna velikosti virtuálního počítače způsobí zničení místní data.
>
>

Během aktualizace nejsou podporovány následující položky:

* Změna názvu role. Odeberte a pak přidejte roli s novým názvem.
* Změna počtu upgradu domény.
* Snížení velikosti místních prostředků.

Pokud provádíte jiné aktualizace definice služby, například snížení velikosti místní prostředek, je nutné provést o aktualizaci prohození virtuální IP adresy. Další informace najdete v tématu [Prohodit nasazení](https://msdn.microsoft.com/library/azure/ee460814.aspx).

<a name="howanupgradeproceeds"></a>

## <a name="how-an-upgrade-proceeds"></a>Jak pokračuje upgradu
Můžete se rozhodnout, jestli chcete aktualizovat všechny role ve službě nebo jedné role v rámci služby. V obou případech jsou všechny instance jednotlivých rolí, která se upgraduje a musí patřit do první upgradu domény byla zastavena, upgradovat a vrátí do režimu online. Jakmile jsou zpět do režimu online, jsou instance v druhé doméně upgradu byla zastavena, upgradovat a vrátí do režimu online. Cloudové služby může mít maximálně jeden upgradu v každém okamžiku aktivní. Je proveden upgrade na nejnovější verzi cloudové služby.

Následující diagram znázorňuje, jak upgradu při upgradu všech rolí v rámci služby:

![Upgrade služby](media/cloud-services-update-azure-service/IC345879.png "Upgrade služby")

Tento další diagram znázorňuje, jak bude pokračovat aktualizace, pokud provádíte upgrade jedné role:

![Upgrade role](media/cloud-services-update-azure-service/IC345880.png "upgradu role")  

Během automatické aktualizace Kontroleru prostředků infrastruktury Azure pravidelně vyhodnocuje stav cloudové služby za účelem určení, kdy je bezpečné provede další UD. Tato vyhodnocení stavu se provádí na základě podle rolí a považuje pouze instance v nejnovější verzi (tj. instance z UDs, které již byly šel). Ověřuje, že minimální počet instancí role, pro každou roli, jste dosáhli uspokojivé stavu terminálu.

### <a name="role-instance-start-timeout"></a>Časový limit spuštění Instance role
Kontroleru prostředků infrastruktury počká 30 minut pro každou instanci role dosáhne stavu spuštěno. Časový limit uplynutím, Kontroleru prostředků infrastruktury bude proti pro další instance role.

### <a name="impact-to-drive-data-during-cloud-service-upgrades"></a>Upgraduje dopad na jednotce data během cloudové služby

Při upgradu služby z jediné instance na více instancí služby bude uveden do během upgradu je provést z důvodu služby Azure upgrady způsobem. Záruční služby dostupnost smlouvu o úrovni služeb se vztahuje pouze na služby, které se nasadí s více než jednu instanci. Následující seznam popisuje, jak jsou data na každém disku ovlivněná každý scénář upgradu služby Azure:

|Scénář|Jednotka C|Diskovou jednotku d|Jednotce E:|
|--------|-------|-------|-------|
|Restartování virtuálního počítače|Zachovají|Zachovají|Zachovají|
|Restartování portálu|Zachovají|Zachovají|Zničení|
|Obnovení z Image portálu|Zachovají|Zničení|Zničení|
|Místní Upgrade|Zachovají|Zachovají|Zničení|
|Migrace uzlu|Zničení|Zničení|Zničení|

Upozorňujeme, že, v seznamu nahoře jednotce E: představuje role kořenové jednotce a nesmí být pevně. Místo toho použijte **RoleRoot %** proměnné prostředí, které představují jednotky.

Chcete-li minimalizovat výpadek při upgradu jedné instance služby, nasazení nové služby s více instancemi pracovní server a proveďte prohození virtuálních IP adres.

<a name="RollbackofanUpdate"></a>

## <a name="rollback-of-an-update"></a>Vrácení zpět aktualizace
Azure poskytuje flexibilitu při správě služby během aktualizace tím, že umožňuje zahájit další operace ve službě, po počáteční aktualizace žádost přijme Kontroleru prostředků infrastruktury Azure. Vrácení zpět lze provést pouze, pokud by aktualizace (Změna konfigurace) nebo upgrade **v průběhu** stavu na nasazení. Aktualizaci nebo upgradu se považuje za probíhá, pokud existuje alespoň jedna instance služby, který ještě neaktualizoval na novou verzi. K ověření, zda je povoleno vrácení zpět, zkontrolujte hodnotu příznak RollbackAllowed vrácený [získat nasazení](https://msdn.microsoft.com/library/azure/ee460804.aspx) a [získat vlastnosti cloudové služby](https://msdn.microsoft.com/library/azure/ee460806.aspx) operace, je nastaven na hodnotu true.

> [!NOTE]
> Pouze má smysl volat vrácení zpět **na místě** aktualizovat nebo upgradovat, protože upgrady prohození virtuální IP adresy zahrnují nahrazení jednu celý spuštěné instanci služby s jinou.
>
>

Vrácení změn v průběhu aktualizace má následující důsledky pro nasazení:

* Nejsou žádné instance rolí, které měl ještě nebyly aktualizovat nebo upgradovat na novou verzi aktualizovat nebo upgradovat, protože tato instance je již spuštěn cílový verzi služby.
* Všechny instance rolí, které měl již byla aktualizovat nebo upgradovat na novou verzi balíčku služby (\*.cspkg) souboru nebo konfigurace služby (\*.cscfg) souboru (nebo oba soubory) se vrátit zpět na před upgradem verze těchto souborů.

To funkčně poskytuje následující funkce:

* [Vrácení zpět aktualizovat nebo upgradovat](https://msdn.microsoft.com/library/azure/hh403977.aspx) operace, které může zavolat na aktualizaci konfigurace (aktivovány volání [změna konfigurace nasazení](https://msdn.microsoft.com/library/azure/ee460809.aspx)) nebo upgradu (aktivovány volání [nasazení upgradu](https://msdn.microsoft.com/library/azure/ee460793.aspx)), dokud existuje alespoň jedna instance v rámci služby, která nebyla aktualizována na novou verzi.
* Element uzamčen a RollbackAllowed elementu, které jsou vraceny jako součást text odpovědi [získat nasazení](https://msdn.microsoft.com/library/azure/ee460804.aspx) a [získat vlastnosti cloudové služby](https://msdn.microsoft.com/library/azure/ee460806.aspx) operace:

  1. Element uzamčen umožňuje rozpoznat, kdy mutating operace může být volána v zadaném nasazení.
  2. RollbackAllowed element umožňuje zjistit, kdy [vrácení zpět aktualizace nebo upgradu](https://msdn.microsoft.com/library/azure/hh403977.aspx) operaci nelze volat u dané nasazení.

  Chcete-li provést vrácení zpět, nemáte zkontrolujte uzamčen a RollbackAllowed elementy. Stačí potvrďte, že RollbackAllowed je nastavená na hodnotu true. Tyto prvky jsou vrácena pouze v případě, že tyto metody jsou vyvolány pomocí hlavičky žádosti nastavit na "x-ms-version: 2011-10-01" nebo novější. Další informace o hlavičkách verze najdete v tématu [Správa služby správy verzí](https://msdn.microsoft.com/library/azure/gg592580.aspx).

Existují některé situace, kde vrácení zpět aktualizace nebo není upgrade podporován, jedná se o následujícím způsobem:

* Snížení místní prostředky - aktualizace se zvyšuje místních prostředků pro roli platformy Azure neumožňuje vrácení zpět.
* Omezení kvóty – Pokud aktualizace byla vertikálně operace, které jste se už nebude mít dostatečnou kvótu výpočetní k dokončení operace vrácení zpět. Každé předplatné Azure má přidruženo kvótu, která určuje maximální počet jader, které mohou být spotřebovávána všechny hostované služby, které patří k tomuto předplatnému. Je-li provést vrácení dané aktualizace by měla být umístěna vaše předplatné přes kvóty a která nebude povolen vrácení zpět.
* Soupeření podmínku – Pokud počáteční aktualizace dokončena, vrácení zpět. není možné.

Je například při vrácení zpět aktualizace mohou být užitečné, pokud používáte [nasazení upgradu](https://msdn.microsoft.com/library/azure/ee460793.aspx) operace v ručním režimu řídí rychlost, jakou hlavní místního upgradu vaší Azure hostovaná služba se nasazuje.

Při zavedení upgradu zavoláte [nasazení upgradu](https://msdn.microsoft.com/library/azure/ee460793.aspx) v ručním režimu a začít provede upgradovacích domén. Pokud v určitém okamžiku při sledování upgradu můžete Všimněte si některé instance rolí v prvním upgradu domén, které byste zkontrolovat mít přestat reagovat, můžete zavolat [vrácení zpět aktualizace nebo upgradu](https://msdn.microsoft.com/library/azure/hh403977.aspx) operaci s nasazením, což ponechá nezměněné instance, které nebyly dosud nebyly upgradovány a vrácení instance, které upgradovali na předchozí balíček služby a konfiguraci.

<a name="multiplemutatingoperations"></a>

## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Spouštění více mutating operací na probíhající nasazení
V některých případech můžete chtít zahájit více souběžných mutating operací na probíhající nasazení. Například může provádět aktualizaci služby a že aktualizace je se nasazuje přes služby, ale chcete provedení některých změn, například vrácení zpět aktualizace, použít jinou aktualizaci nebo odstranění i nasazení. Případ, kdy to může být potřeba je, zda aktualizace služby obsahuje buggy kódu, což způsobí, že upgradovaná role instance na opakovaně k chybě. V takovém případě nebude Kontroleru prostředků infrastruktury Azure nemůže pokračovat v použití, která upgradovat, protože dostatečný počet instancí v upgradované domény jsou v pořádku. Tento stav se označuje jako *zablokované nasazení*. Nasazení můžete unstick vrácení zpět aktualizace nebo novou aktualizaci nad horní neúspěšného jeden.

Po počáteční požadavek na aktualizaci nebo upgradu služby byla přijata adaptérem prostředků infrastruktury Azure, můžete začít následných mutace operací. To znamená, že nemáte počkejte na dokončení před zahájením jiná operace mutating počáteční operace.

Při první aktualizaci stále probíhá inicializace druhá operace aktualizace provede podobná operaci vrácení zpět. Pokud je druhá aktualizace v automatickém režimu, první doména upgradu se upgradují okamžitě, by mohl vést k instancí z několika domén upgradu je offline ze stejného bodu v čase.

Mutating operace jsou následující: [změna konfigurace nasazení](https://msdn.microsoft.com/library/azure/ee460809.aspx), [nasazení upgradu](https://msdn.microsoft.com/library/azure/ee460793.aspx), [stav nasazení aktualizace](https://msdn.microsoft.com/library/azure/ee460808.aspx), [odstranit nasazení](https://msdn.microsoft.com/library/azure/ee460815.aspx), a [vrácení zpět aktualizace nebo upgradu](https://msdn.microsoft.com/library/azure/hh403977.aspx).

Dvě operace [získat nasazení](https://msdn.microsoft.com/library/azure/ee460804.aspx) a [získat vlastnosti cloudové služby](https://msdn.microsoft.com/library/azure/ee460806.aspx), vrátí uzamčen příznak, který můžete prověřit, abyste zjistili, zda mutating operace může být volána na dané nasazení.

Volání verze těchto metod, které vrátí příznak uzamčen, je nutné nastavit hlavička požadavku na "x-ms-version: 2011-10-01" nebo pozdější. Další informace o hlavičkách verze najdete v tématu [Správa služby správy verzí](https://msdn.microsoft.com/library/azure/gg592580.aspx).

<a name="distributiondfroles"></a>

## <a name="distribution-of-roles-across-upgrade-domains"></a>Distribuce rolí napříč doménami upgradu
Instance role Azure rovnoměrně distribuuje mezi se stanoveným počtem upgradovacích domén, které lze konfigurovat jako součást souboru definice (.csdef) služby. Maximální počet domén upgradu je 20 a výchozí hodnota je 5. Další informace o tom, jak upravit soubor definice služby najdete v tématu [Azure schématu definice služby (.csdef souboru)](cloud-services-model-and-package.md#csdef).

Například pokud vaše role má deset instancí, ve výchozím nastavení každé upgradované domény obsahuje dvě instance. Pokud vaše role má 14 instancí, potom čtyři upgradu domén obsahovat tři instance a páté domény obsahuje dva.

Domén upgradu jsou označeny index počítaný od nuly: první doména upgradu má ID 0, a druhé upgradu domény ID 1 a tak dále.

Následující diagram znázorňuje, jak služba než obsahuje dvě role při distribuují službu definuje dvě domény upgradu. Služba běží osm instancí webovou roli a devět instancí role pracovního procesu.

![Distribuce domén upgradu](media/cloud-services-update-azure-service/IC345533.png "distribuce domén upgradu")

> [!NOTE]
> Všimněte si, že Azure řídit přidělování instancí napříč doménami upgradu. Není možné určit, které instance jsou přiděleny do které domény.
>
>

## <a name="next-steps"></a>Další kroky
[Jak spravovat Cloud Services](cloud-services-how-to-manage-portal.md)  
[Postup sledování cloudové služby](cloud-services-how-to-monitor.md)  
[Jak konfigurovat Cloud Services](cloud-services-how-to-configure-portal.md)  
