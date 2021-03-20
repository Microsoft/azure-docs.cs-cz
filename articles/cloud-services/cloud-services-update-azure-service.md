---
title: Jak aktualizovat cloudovou službu (Classic) | Microsoft Docs
description: Naučte se aktualizovat cloudové služby v Azure. Zjistěte, jak aktualizace cloudové služby pokračuje, aby se zajistila dostupnost.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 5d85003ca7b4307c308914484502ae03269f66ac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98741107"
---
# <a name="how-to-update-an-azure-cloud-service-classic"></a>Jak aktualizovat cloudovou službu Azure (Classic)

> [!IMPORTANT]
> [Azure Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md) je nový model nasazení založený na Azure Resource Manager pro produkt Azure Cloud Services.V důsledku této změny se Azure Cloud Services běžící na modelu nasazení založeném na Azure Service Manager přejmenovala jako Cloud Services (Classic) a všechna nová nasazení by měla používat [Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md).

Aktualizace cloudové služby včetně jejích rolí a hostovaného operačního systému je proces tří kroků. Nejdřív je potřeba nahrát binární soubory a konfigurační soubory pro novou cloudovou službu nebo verzi operačního systému. V dalším kroku Azure rezervuje výpočetní a síťové prostředky pro cloudovou službu na základě požadavků nové verze cloudové služby. Nakonec Azure provede postupný upgrade, který klienta postupně aktualizuje na novou verzi nebo hostovaný operační systém a zároveň zachovává vaši dostupnost. Tento článek popisuje podrobnosti tohoto posledního kroku – postupná inovace.

## <a name="update-an-azure-service"></a>Aktualizace služby Azure
Azure uspořádá instance rolí do logických seskupení označovaných jako upgradovací domény (UD). Upgradovací domény (UD) jsou logické sady instancí rolí, které se aktualizují jako skupina.  Azure aktualizuje cloudovou službu po jednom UD, což umožňuje instancím v jiné UDs dál obsluhovat provoz.

Výchozí počet domén upgradu je 5. Můžete zadat jiný počet domén upgradu zahrnutím atributu upgradeDomainCount do souboru definice služby (. csdef). Další informace o atributu upgradeDomainCount naleznete v tématu [schéma definice Azure Cloud Services (soubor. csdef)](./schema-csdef-file.md).

Při provádění místní aktualizace jedné nebo více rolí ve službě Azure aktualizuje sady instancí rolí v závislosti na upgradovací doméně, do které patří. Azure aktualizuje všechny instance v dané upgradovací doméně – zastavuje je, aktualizuje je, přenáší je online a pak se přesune na další doménu. Když zastavíte jenom instance spuštěné v aktuální upgradovací doméně, Azure zajistí, že dojde k aktualizaci s nejmenším možným dopadem na běžící službu. Další informace najdete v tématu [jak aktualizace pokračuje](#howanupgradeproceeds) dále v tomto článku.

> [!NOTE]
> I když se tyto výrazy **aktualizují** a **upgradují** mírně odlišným významem v kontextu Azure, dají se pro procesy a popisy funkcí v tomto dokumentu použít zaměnitelné.
>
>

Vaše služba musí definovat aspoň dvě instance role, aby se tato role místně aktualizovala bez výpadků. Pokud se služba skládá jenom z jedné instance jedné role, bude služba nedostupná, dokud se nedokončí aktualizace na pracovišti.

Toto téma obsahuje následující informace o aktualizacích Azure:

* [Povolená Změna služby během aktualizace](#AllowedChanges)
* [Jak upgrade pokračuje](#howanupgradeproceeds)
* [Vrácení aktualizace zpět](#RollbackofanUpdate)
* [Inicializace více operací s pokračujícím nasazením](#multiplemutatingoperations)
* [Distribuce rolí napříč doménami upgradu](#distributiondfroles)

<a name="AllowedChanges"></a>

## <a name="allowed-service-changes-during-an-update"></a>Povolená Změna služby během aktualizace
V následující tabulce jsou uvedeny povolené změny služby během aktualizace:

| Změny povolené pro hostování, služby a role | Místní aktualizace | Připraveno (prohození VIP) | Odstranit a znovu nasadit |
| --- | --- | --- | --- |
| Verze operačního systému |Yes |Yes |Yes |
| Úroveň důvěryhodnosti .NET |Yes |Yes |Yes |
| Velikost virtuálního počítače<sup>1</sup> |Ano<sup>2</sup> |Yes |Yes |
| Nastavení místního úložiště |Zvýšit jenom<sup>2</sup> |Yes |Yes |
| Přidání nebo odebrání rolí ve službě |Yes |Yes |Yes |
| Počet instancí konkrétní role |Yes |Yes |Yes |
| Počet nebo typ koncových bodů pro službu |Ano<sup>2</sup> |No |Yes |
| Názvy a hodnoty nastavení konfigurace |Yes |Yes |Yes |
| Hodnoty (ale ne názvy) nastavení konfigurace |Yes |Yes |Yes |
| Přidat nové certifikáty |Yes |Yes |Yes |
| Změna existujících certifikátů |Yes |Yes |Yes |
| Nasadit nový kód |Yes |Yes |Yes |

<sup>1</sup> Změna velikosti omezená na podmnožinu velikostí dostupných pro cloudovou službu.

<sup>2</sup> vyžaduje sadu Azure SDK 1,5 nebo novější verze.

> [!WARNING]
> Změna velikosti virtuálního počítače způsobí zničení místních dat.
>
>

Během aktualizace nejsou podporovány následující položky:

* Změna názvu role. Odeberte a pak přidejte roli s novým názvem.
* Probíhá změna počtu upgradovacích domén.
* Zmenšení velikosti místních prostředků.

Pokud provádíte jiné aktualizace definice služby, jako je třeba zmenšení velikosti místního prostředku, musíte místo toho provést aktualizaci VIP swap. Další informace najdete v tématu [prohození nasazení](/previous-versions/azure/reference/ee460814(v=azure.100)).

<a name="howanupgradeproceeds"></a>

## <a name="how-an-upgrade-proceeds"></a>Jak upgrade pokračuje
Můžete se rozhodnout, jestli chcete ve službě aktualizovat všechny role ve vaší službě, nebo jednu roli. V obou případech se všechny instance všech rolí, které se upgradují a patří do první upgradovací domény, zastaví, upgradují a vrátí se zpátky do online režimu. Až budou zpátky online, instance v druhé upgradovací doméně se zastaví, upgradují a vrátí se zpátky do online režimu. Cloudová služba může mít po dobu maximálně jeden aktivní upgrade. Upgrade se vždy provádí na nejnovější verzi cloudové služby.

Následující diagram znázorňuje, jak upgrade pokračuje, pokud upgradujete všechny role ve službě:

![Upgradovat službu](media/cloud-services-update-azure-service/IC345879.png "Upgradovat službu")

Tento další diagram znázorňuje, jak aktualizace pokračuje, pokud upgradujete jenom jednu roli:

![Role upgradu](media/cloud-services-update-azure-service/IC345880.png "Role upgradu")  

Během automatické aktualizace kontroler prostředků infrastruktury Azure pravidelně vyhodnocuje stav cloudové služby, aby zjistil, kdy je bezpečné projít další UD. Toto hodnocení stavu se provádí na základě jednotlivých rolí a zvažuje jenom instance v nejnovější verzi (tj. instance z UDs, které už jsou vás provedl). Ověřuje, že pro každou roli dosáhlo minimálního počtu instancí rolí, který dosáhl uspokojivý stav terminálu.

### <a name="role-instance-start-timeout"></a>Časový limit spuštění instance role
Kontroler prostředků infrastruktury počká 30 minut, než se každá instance role dorazí na počáteční stav. Pokud doba trvání vypršela, kontroler prostředků infrastruktury bude pokračovat v procházení k další instanci role.

### <a name="impact-to-drive-data-during-cloud-service-upgrades"></a>Dopad na data na disku během upgradu cloudové služby

Při upgradování služby z jedné instance na více instancí bude služba zavedena v době, kdy se upgrade provede v důsledku upgradu služeb Azure. Smlouva o úrovni služeb garantuje dostupnost služby pouze pro služby, které jsou nasazeny s více než jednou instancí. Následující seznam popisuje, jak jsou data na jednotlivých jednotkách ovlivněná jednotlivými scénáři upgradu služby Azure:

|Scenario|Jednotka C|Jednotka D|Jednotka E|
|--------|-------|-------|-------|
|Restartování virtuálního počítače|Konzervován|Konzervován|Konzervován|
|Restart portálu|Konzervován|Konzervován|Zneškodněn|
|Přeinstalace portálu|Konzervován|Zneškodněn|Zneškodněn|
|In-Place upgrade|Konzervován|Konzervován|Zneškodněn|
|Migrace uzlů|Zneškodněn|Zneškodněn|Zneškodněn|

Všimněte si, že ve výše uvedeném seznamu představuje jednotka E: kořenovou jednotku role a neměl by být pevně zakódovaný. Místo toho použijte proměnnou prostředí **% RoleRoot%** k reprezentaci jednotky.

Abyste minimalizovali prostoje při upgradování jedné instance služby, nasaďte na pracovní server novou službu s více instancemi a proveďte prohození virtuálních IP adres.

<a name="RollbackofanUpdate"></a>

## <a name="rollback-of-an-update"></a>Vrácení aktualizace zpět
Azure poskytuje flexibilitu při správě služeb během aktualizace tím, že vám umožní zahájit u služby další operace až po přijetí počáteční žádosti o aktualizaci řadičem prostředků infrastruktury Azure. Vrácení zpět se dá provést jenom v případě, že se aktualizace (Změna konfigurace) nebo upgrade nachází v **probíhajícím** stavu nasazení. Aktualizace nebo upgrade se považuje za probíhající, pokud existuje aspoň jedna instance služby, která ještě není aktualizovaná na novou verzi. Pokud chcete otestovat, jestli je vrácení zpět povolené, zkontrolujte hodnotu příznaku RollbackAllowed, který vrátí operace [získat nasazení](/previous-versions/azure/reference/ee460804(v=azure.100)) a [získat vlastnosti cloudové služby](/previous-versions/azure/reference/ee460806(v=azure.100)) , na hodnotu true.

> [!NOTE]
> Je vhodné volat vrácení zpět v rámci **místní** aktualizace nebo upgradu, protože virtuální IP adresy prohození zahrnují nahrazení jedné spuštěné instance služby jinou instancí.
>
>

Vrácení probíhající aktualizace má na nasazení následující důsledky:

* Jakékoli instance rolí, které ještě nebyly aktualizovány nebo upgradovány na novou verzi, nejsou aktualizovány ani upgradovány, protože tyto instance jsou již spuštěny cílovou verzí služby.
* Všechny instance rolí, které již byly aktualizovány nebo upgradovány na novou verzi souboru balíčku služby ( \* . cspkg) nebo soubor konfigurace služby ( \* . cscfg), jsou vráceny na verzi předběžného upgradu těchto souborů.

Tato funkce je poskytována následujícími funkcemi:

* Operace [vrácení aktualizace nebo upgradu](/previous-versions/azure/reference/hh403977(v=azure.100)) , která může být volána v aktualizaci konfigurace (spouštěné voláním [Konfigurace nasazení změn](/previous-versions/azure/reference/ee460809(v=azure.100))), nebo při upgradu (aktivované voláním [nasazení upgradu](/previous-versions/azure/reference/ee460793(v=azure.100))), pokud ve službě existuje alespoň jedna instance, která ještě nebyla aktualizována na novou verzi.
* Uzamčený element a element RollbackAllowed, které jsou vráceny jako součást textu odpovědi v rámci operace [získat nasazení](/previous-versions/azure/reference/ee460804(v=azure.100)) a [získat vlastnosti cloudové služby](/previous-versions/azure/reference/ee460806(v=azure.100)) :

  1. Uzamčený element umožňuje rozpoznat, kdy může být v daném nasazení vyvolána případná operace.
  2. Element RollbackAllowed umožňuje rozpoznat, kdy může být operace [vrácení aktualizace nebo upgradu](/previous-versions/azure/reference/hh403977(v=azure.100)) volána v daném nasazení.

  Aby bylo možné provést vrácení zpět, není nutné kontrolovat uzamčené i RollbackAllowed prvky. Postačuje, abyste potvrdili, že je RollbackAllowed nastavené na true. Tyto prvky jsou vráceny pouze v případě, že jsou tyto metody vyvolány pomocí hlavičky Request nastavenou na "x-MS-Version: 2011-10-01" nebo na novější verzi. Další informace o hlavičkách verzí najdete v tématu [Správa verzí správy služeb](/previous-versions/azure/gg592580(v=azure.100)).

V některých situacích se nepodporují vrácení aktualizace nebo upgradu, které jsou následující:

* Snížení místních prostředků – Pokud aktualizace zvyšuje místní prostředky pro roli, platforma Azure nepovoluje vracení zpět.
* Omezení kvóty – Pokud byla aktualizace operací horizontálního snížení kapacity, možná už nebudete mít k dokončení operace vrácení zpět dostatečnou kvótu výpočtů. Každé předplatné Azure má přiřazenou kvótu, která určuje maximální počet jader, které mohou být spotřebovány všemi hostovanými službami, které patří k tomuto předplatnému. Pokud by došlo k vrácení zpět dané aktualizace, bude vaše předplatné přes kvótu zavedeno a vrácení zpět nebude povoleno.
* Konflikt časování – Pokud se počáteční aktualizace dokončí, vrácení zpět není možné.

Příkladem, kdy může být odvolání aktualizace užitečné, je, pokud používáte operaci [nasazení upgradu](/previous-versions/azure/reference/ee460793(v=azure.100)) v ručním režimu k řízení četnosti, s jakou je nasazený hlavní upgrade na službu Azure Hosted.

Během zavedení upgradu zavolejte [nasazení upgradu](/previous-versions/azure/reference/ee460793(v=azure.100)) v ručním režimu a začněte procházet domény upgradu. Pokud v některém okamžiku sledujete upgrade, Všimněte si, že některé instance role v první upgradovací doméně, kterou prohlížíte, přestanou reagovat, můžete na nasazení zavolat operaci [vrácení aktualizace nebo upgradu](/previous-versions/azure/reference/hh403977(v=azure.100)) , která zůstane bez vylepšení instancí, které ještě nebyly upgradovány, a vrátit zpět instance, které byly upgradovány na předchozí balíček a konfiguraci služby.

<a name="multiplemutatingoperations"></a>

## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Inicializace více operací s pokračujícím nasazením
V některých případech můžete chtít zahájit více souběžných operací probíhajícího nasazení. Například můžete provést aktualizaci služby a, zatímco tato aktualizace je v rámci služby zaváděna, chcete provést nějaké změny, například vrátit zpět aktualizaci, použít jinou aktualizaci nebo dokonce odstranit nasazení. Případ, kdy to může být nutné, je, že pokud upgrade služby obsahuje ladicí kód, který způsobí opakované zhroucení instance role. V takovém případě nebude mít řadič prostředků infrastruktury Azure během provádění tohoto upgradu žádný pokrok, protože v upgradovaných doménách je v pořádku nedostatečný počet instancí. Tento stav se označuje jako *zablokované nasazení*. Nasazení můžete odvýšit vrácením aktualizace nebo použitím nové aktualizace nad selháním jedné z nich.

Jakmile kontroler prostředků infrastruktury Azure přijme počáteční požadavek na aktualizaci nebo upgrade služby, můžete začít s dalšími následnými operacemi. To znamená, že nemusíte čekat na dokončení počáteční operace, než budete moci spustit jinou operaci.

Zahájení druhé operace aktualizace při probíhající první aktualizaci bude fungovat podobně jako operace vrácení zpět. Pokud je druhá aktualizace v automatickém režimu, upgraduje se hned první upgradovací doména, což může vést k neaktivnímu navýšení instancí z více domén upgradu v jednom okamžiku.

Jedná se o následující operace: [Změna konfigurace nasazení](/previous-versions/azure/reference/ee460809(v=azure.100)), [nasazení upgradu](/previous-versions/azure/reference/ee460793(v=azure.100)), [stav nasazení aktualizace](/previous-versions/azure/reference/ee460808(v=azure.100)), [odstranění nasazení](/previous-versions/azure/reference/ee460815(v=azure.100))a [aktualizace pro vrácení zpět nebo upgrade](/previous-versions/azure/reference/hh403977(v=azure.100)).

Dvě operace, [získat vlastnosti nasazení](/previous-versions/azure/reference/ee460804(v=azure.100)) a [získat vlastnosti cloudové služby](/previous-versions/azure/reference/ee460806(v=azure.100)), vrátí příznak uzamčení, který se dá prozkoumat, aby se zjistilo, jestli se dá v daném nasazení vyvolat případnou operaci.

Pro volání verze těchto metod, které vrací příznak uzamknut, je nutné nastavit hlavičku požadavku na "x-MS-Version: 2011-10-01" nebo na pozdější. Další informace o hlavičkách verzí najdete v tématu [Správa verzí správy služeb](/previous-versions/azure/gg592580(v=azure.100)).

<a name="distributiondfroles"></a>

## <a name="distribution-of-roles-across-upgrade-domains"></a>Distribuce rolí napříč doménami upgradu
Azure distribuuje instance role rovnoměrně v rámci nastaveného počtu domén upgradu, které je možné nakonfigurovat jako součást souboru definice služby (. csdef). Maximální počet domén upgradu je 20 a výchozí hodnota je 5. Další informace o tom, jak upravit definiční soubor služby, najdete v tématu [schéma definice služby Azure (soubor. csdef)](cloud-services-model-and-package.md#csdef).

Například pokud má vaše role deset instancí, ve výchozím nastavení každá upgradovací doména obsahuje dvě instance. Pokud má vaše role 14 instancí, pak čtyři domény upgradu obsahují tři instance a pátá doména obsahuje dvě.

Domény upgradu jsou označeny indexem založeným na nule: první upgradovací doména má ID 0 a druhá upgradovací doména má ID 1 atd.

Následující diagram znázorňuje, jak je služba, která obsahuje dvě role, distribuována v případě, že služba definuje dvě domény upgradu. Služba spouští osm instancí webové role a devět instancí role pracovního procesu.

![Distribuce domén upgradu](media/cloud-services-update-azure-service/IC345533.png "Distribuce domén upgradu")

> [!NOTE]
> Všimněte si, že Azure řídí přiřazování instancí mezi upgradovací domény. Není možné určit, které instance jsou přiděleny k této doméně.
>
>

## <a name="next-steps"></a>Další kroky
[Správa Cloud Services](cloud-services-how-to-manage-portal.md)  
[Jak monitorovat Cloud Services](cloud-services-how-to-monitor.md)  
[Jak konfigurovat Cloud Services](cloud-services-how-to-configure-portal.md)