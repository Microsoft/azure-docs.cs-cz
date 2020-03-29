---
title: Jak aktualizovat cloudovou službu | Dokumenty společnosti Microsoft
description: Přečtěte si, jak aktualizovat cloudové služby v Azure. Zjistěte, jak aktualizace cloudové služby pokračuje, aby byla zajištěna dostupnost.
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: 731f4e8cc8a93f33d6887f44fc8d09585e92a75a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75360340"
---
# <a name="how-to-update-a-cloud-service"></a>Jak aktualizovat cloudovou službu

Aktualizace cloudové služby, včetně jejích rolí a hostovaného operačního systému, je třístupňový proces. Nejprve je nutné nahrát binární soubory a konfigurační soubory pro novou cloudovou službu nebo verzi operačního systému. V dalším případě Azure rezervuje výpočetní a síťové prostředky pro cloudovou službu na základě požadavků nové verze cloudové služby. Nakonec Azure provede postupný upgrade postupně aktualizovat klienta na novou verzi nebo hostovaného operačního systému při zachování dostupnosti. Tento článek popisuje podrobnosti tohoto posledního kroku – postupné inovace.

## <a name="update-an-azure-service"></a>Aktualizace služby Azure
Azure organizuje instance vaší role do logických seskupení nazývaných upgradovací domény (UD). Upgradovací domény (UD) jsou logické sady instancí rolí, které jsou aktualizovány jako skupina.  Azure aktualizuje cloudovou službu po jednom ud, což umožňuje instancím v jiných uds nadále obsluhovat provoz.

Výchozí počet upgradovacích domén je 5. Můžete zadat jiný počet upgradovacích domén zahrnutím atributu upgradeDomainCount do definičního souboru služby (.csdef). Další informace o atributu upgradeDomainCount najdete v tématu Schéma [definice azure cloudových služeb (soubor.csdef).](https://docs.microsoft.com/azure/cloud-services/schema-csdef-file)

Když provedete místní aktualizaci jedné nebo více rolí ve vaší službě, Azure aktualizuje sady instancí rolí podle upgradu domény, do které patří. Azure aktualizuje všechny instance v dané doméně upgradu – zastaví je, aktualizuje je a přenese je zpět do úponku – a pak se přesune do další domény. Zastavením pouze instance spuštěné v aktuální doméně upgradu Azure zajišťuje, že dojde k aktualizaci s co nejmenším dopadem na spuštěnou službu. Další informace naleznete [v tématu Jak aktualizace pokračuje](#howanupgradeproceeds) dále v tomto článku.

> [!NOTE]
> Zatímco termíny **aktualizace** a **upgrade** mají mírně odlišný význam v kontextu Azure, mohou být použity zaměnitelně pro procesy a popisy funkcí v tomto dokumentu.
>
>

Vaše služba musí definovat alespoň dvě instance role pro tuto roli, která má být aktualizována na místě bez prostojů. Pokud se služba skládá pouze z jedné instance jedné role, nebude vaše služba k dispozici, dokud nebude dokončena aktualizace na místě.

Toto téma popisuje následující informace o aktualizacích Azure:

* [Povolené změny služby během aktualizace](#AllowedChanges)
* [Jak upgrade pokračuje](#howanupgradeproceeds)
* [Vrácení aktualizace zpět](#RollbackofanUpdate)
* [Zahájení více operací mutování v probíhajícím nasazení](#multiplemutatingoperations)
* [Rozdělení rolí mezi upgradovací domény](#distributiondfroles)

<a name="AllowedChanges"></a>

## <a name="allowed-service-changes-during-an-update"></a>Povolené změny služby během aktualizace
V následující tabulce jsou uvedeny povolené změny služby během aktualizace:

| Změny povolené pro hostování, služby a role | Aktualizace na místě | Inscenované (VIP swap) | Odstranění a opětovné nasazení |
| --- | --- | --- | --- |
| Verze operačního systému |Ano |Ano |Ano |
| Úroveň důvěryhodnosti rozhraní .NET |Ano |Ano |Ano |
| Velikost virtuálního počítače<sup>1</sup> |Ano<sup>2</sup> |Ano |Ano |
| Nastavení místního úložiště |Zvýšit pouze<sup>2</sup> |Ano |Ano |
| Přidání nebo odebrání rolí ve službě |Ano |Ano |Ano |
| Počet případů konkrétní role |Ano |Ano |Ano |
| Počet nebo typ koncových bodů pro službu |Ano<sup>2</sup> |Ne |Ano |
| Názvy a hodnoty nastavení konfigurace |Ano |Ano |Ano |
| Hodnoty (ale ne názvy) nastavení konfigurace |Ano |Ano |Ano |
| Přidání nových certifikátů |Ano |Ano |Ano |
| Změna existujících certifikátů |Ano |Ano |Ano |
| Nasazení nového kódu |Ano |Ano |Ano |

<sup>1</sup> Změna velikosti omezená na podmnožinu velikostí dostupných pro cloudovou službu.

<sup>2</sup> Vyžaduje Azure SDK 1.5 nebo novější verze.

> [!WARNING]
> Změna velikosti virtuálního počítače zničí místní data.
>
>

Následující položky nejsou během aktualizace podporovány:

* Změna názvu role. Odeberte a přidejte roli s novým názvem.
* Změna počtu domény upgradu.
* Zmenšení velikosti místních prostředků.

Pokud provádíte další aktualizace definice služby, například zmenšení velikosti místního prostředku, musíte místo toho provést aktualizaci výměny VIP. Další informace naleznete v [tématu Swap Deployment](/previous-versions/azure/reference/ee460814(v=azure.100)).

<a name="howanupgradeproceeds"></a>

## <a name="how-an-upgrade-proceeds"></a>Jak upgrade pokračuje
Můžete se rozhodnout, zda chcete aktualizovat všechny role ve službě nebo jednu roli ve službě. V obou případech jsou všechny instance každé role, která je upgradována a patří do první domény upgradu, zastaveny, upgradovány a převedeny zpět do režimu online. Jakmile jsou opět online, instance v druhé doméně upgradu jsou zastaveny, upgradovány a přeneseny zpět do režimu online. Cloudová služba může mít aktivní maximálně jeden upgrade najednou. Upgrade se vždy provádí proti nejnovější verzi cloudové služby.

Následující diagram znázorňuje, jak pokračuje upgrade, pokud upgradujete všechny role ve službě:

![Služba upgradu](media/cloud-services-update-azure-service/IC345879.png "Služba upgradu")

Tento další diagram znázorňuje, jak aktualizace pokračuje, pokud upgradujete pouze jednu roli:

![Role upgradu](media/cloud-services-update-azure-service/IC345880.png "Role upgradu")  

Během automatické aktualizace řadič prostředků infrastruktury Azure pravidelně vyhodnocuje stav cloudové služby k určení, kdy je bezpečné chodit další UD. Toto hodnocení stavu se provádí na základě pro-role a bere v úvahu pouze instance v nejnovější verzi (tj. instance z UD, které již byly procházeny). Ověřuje, že minimální počet instancí rolí pro každou roli dosáhl uspokojivého stavu terminálu.

### <a name="role-instance-start-timeout"></a>Časový výtok spuštění instance role
Řadič prostředků infrastruktury bude čekat 30 minut pro každou instanci role k dosažení stavu Spuštěno. Pokud uplyne doba trvání časového limitu, řadič prostředků infrastruktury bude pokračovat v chůzi na další instanci role.

### <a name="impact-to-drive-data-during-cloud-service-upgrades"></a>Dopad na zvýšení dat během upgradů cloudové služby

Při upgradu služby z jedné instance na více instancí se vaše služba při upgradu sníží, zatímco se upgrade provádí kvůli způsobu upgradu služeb Azure. Smlouva o úrovni služeb zaručující dostupnost služeb se vztahuje pouze na služby, které jsou nasazeny s více než jednou instancí. Následující seznam popisuje, jak jsou data na každé jednotce ovlivněna jednotlivými scénáři upgradu služby Azure:

|Scénář|C Pohon|D Jednotka|E pohon|
|--------|-------|-------|-------|
|Restartování virtuálního počítače|Zachovány|Zachovány|Zachovány|
|Restartování portálu|Zachovány|Zachovány|Zničeny|
|Obrázek portálu|Zachovány|Zničeny|Zničeny|
|Upgrade na místě|Zachovány|Zachovány|Zničeny|
|Migrace uzlů|Zničeny|Zničeny|Zničeny|

Všimněte si, že ve výše uvedeném seznamu jednotka E: představuje kořenovou jednotku role a neměla by být pevně zakódována. Místo toho použijte proměnnou prostředí **%RoleRoot%** k reprezentaci jednotky.

Chcete-li minimalizovat prostoje při upgradu služby jedné instance, nasaďte novou službu s více instancemi na pracovní server a proveďte prohození vip.

<a name="RollbackofanUpdate"></a>

## <a name="rollback-of-an-update"></a>Vrácení aktualizace zpět
Azure poskytuje flexibilitu při správě služeb během aktualizace tím, že vám umožní zahájit další operace ve službě, po přijetí počáteční ho požadavku na aktualizaci řadičem Azure Fabric. Vrácení zpět lze provést pouze v případě, že aktualizace (změna konfigurace) nebo upgrade je ve stavu **probíhá** na nasazení. Aktualizace nebo upgrade se považuje za probíhající, pokud existuje alespoň jedna instance služby, která ještě nebyla aktualizována na novou verzi. Chcete-li otestovat, zda je povoleno vrácení zpět, zkontrolujte hodnotu příznaku RollbackAllowed vrácenou [operacemi Get Deployment](/previous-versions/azure/reference/ee460804(v=azure.100)) a [Get Cloud Service Properties](/previous-versions/azure/reference/ee460806(v=azure.100)) je nastavena na hodnotu true.

> [!NOTE]
> Má smysl volat vrácení zpět na **vlastní** aktualizaci nebo upgrade, protože upgrady odkládacího programu VIP zahrnují nahrazení jedné celé spuštěné instance vaší služby jinou.
>
>

Vrácení aktualizace probíhající má následující účinky na nasazení:

* Všechny instance role, které ještě nebyly aktualizovány nebo upgradovány na novou verzi, nejsou aktualizovány ani upgradovány, protože tyto instance již používají cílovou verzi služby.
* Všechny instance role, které již byly aktualizovány nebo upgradovány\*na novou verzi souboru balíčku\*služby (.cspkg) nebo soubor konfigurace služby (.cscfg) (nebo oba soubory), se vrátí do verze těchto souborů před upgradem.

To je funkčně zajištěno následujícími funkcemi:

* Operace [vrácení zpět aktualizace nebo upgrade,](/previous-versions/azure/reference/hh403977(v=azure.100)) které lze volat na aktualizaci konfigurace (spouštěné voláním změnit [konfiguraci nasazení)](/previous-versions/azure/reference/ee460809(v=azure.100))nebo upgrade (spuštěno voláním [nasazení upgradu)](/previous-versions/azure/reference/ee460793(v=azure.100))tak dlouho, dokud je alespoň jedna instance ve službě, která ještě nebyla aktualizována na novou verzi.
* Locked element a RollbackAllowed element, které jsou vráceny jako součást těla odezvy [získat nasazení](/previous-versions/azure/reference/ee460804(v=azure.100)) a [získat vlastnosti cloudové služby](/previous-versions/azure/reference/ee460806(v=azure.100)) operace:

  1. Prvek Locked umožňuje zjistit, kdy lze v daném nasazení vyvolat operaci mutování.
  2. Prvek RollbackAllowed umožňuje zjistit, kdy lze při daném nasazení volat operaci [aktualizace nebo upgradu vrácení zpět.](/previous-versions/azure/reference/hh403977(v=azure.100))

  Chcete-li provést vrácení zpět, není třeba zkontrolovat locked a RollbackAllowed prvky. Postačí potvrdit, že RollbackAllowed je nastavena na hodnotu true. Tyto prvky jsou vráceny pouze v případě, že tyto metody jsou vyvolány pomocí hlavičky požadavku nastavena na "x-ms-version: 2011-10-01" nebo novější verzi. Další informace o záhlavích správy verzí naleznete v [tématu Service Management Versioning](/previous-versions/azure/gg592580(v=azure.100)).

Existují některé situace, kdy vrácení aktualizace nebo upgradu není podporováno, jsou následující:

* Snížení místních prostředků – Pokud aktualizace zvyšuje místní prostředky pro roli platformy Azure neumožňuje vrácení zpět.
* Omezení kvót – Pokud se jednalo o operaci škálování, nemusí být k dokončení operace vrácení zpět již dostatečná výpočetní kvóta. Ke každému předplatnému Azure je přidružena kvóta, která určuje maximální počet jader, které můžou spotřebovat všechny hostované služby, které patří do tohoto předplatného. Pokud provádění vrácení dané aktualizace by umístit vaše předplatné přes kvótu pak, že vrácení zpět nebude povoleno.
* Spor – pokud byla dokončena počáteční aktualizace, vrácení zpět není možné.

Příkladem, kdy vrácení aktualizace může být užitečné, je, pokud používáte operaci [nasazení upgradu](/previous-versions/azure/reference/ee460793(v=azure.100)) v ručním režimu k řízení rychlosti, jakou je zaveden hlavní upgrade na místo hostované služby Azure.

Během zavádění upgradu zavoláte [nasazení upgradu](/previous-versions/azure/reference/ee460793(v=azure.100)) v ručním režimu a začnete procházet upgradovací domény. Pokud v určitém okamžiku, při sledování upgradu, zjistíte, že některé instance rolí v prvních doménách upgradu, které jste zkoumali, přestaly reagovat, můžete volat operaci [vrácení zpět aktualizace nebo upgradu](/previous-versions/azure/reference/hh403977(v=azure.100)) v nasazení, která ponechá nedotčené instance, které ještě nebyly upgradovány, a instance vrácení zpět, které byly upgradovány na předchozí balíček služeb a konfiguraci.

<a name="multiplemutatingoperations"></a>

## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Zahájení více operací mutování v probíhajícím nasazení
V některých případech můžete chtít zahájit více souběžných operací mutování na probíhající nasazení. Můžete například provést aktualizaci služby a během této aktualizace, která se zavádí v rámci celé služby, chcete provést některé změny, například vrátit aktualizaci zpět, použít jinou aktualizaci nebo dokonce odstranit nasazení. Případ, ve kterém to může být nezbytné, je, pokud upgrade služby obsahuje kód buggy, který způsobí, že inovované instance role opakovaně havaruje. V takovém případě řadič Prostředků infrastruktury Azure nebude moci dosáhnout pokroku v použití tohoto upgradu, protože nedostatečný počet instancí v upgradované doméně jsou v pořádku. Tento stav se označuje jako *zablokované nasazení*. Nasazení můžete zrušit vrácením aktualizace zpět nebo použitím nové aktualizace nad horní část selhání.

Jakmile počáteční požadavek na aktualizaci nebo upgrade služby byla přijata řadičem Azure Fabric, můžete spustit následné operace mutování. To znamená, že není třeba čekat na dokončení počáteční operace před spuštěním další operace mutování.

Zahájení druhé operace aktualizace, zatímco první aktualizace probíhá bude provádět podobně jako operace vrácení zpět. Pokud je druhá aktualizace v automatickém režimu, první upgradovací doména bude okamžitě upgradována, což může vést k tomu, že instance z více upgradovacích domén jsou v režimu offline ve stejném okamžiku.

Zmutující operace jsou následující: [Změna konfigurace nasazení](/previous-versions/azure/reference/ee460809(v=azure.100)), Nasazení [upgradu](/previous-versions/azure/reference/ee460793(v=azure.100)), [Stav nasazení aktualizace](/previous-versions/azure/reference/ee460808(v=azure.100)), Odstranění [nasazení](/previous-versions/azure/reference/ee460815(v=azure.100))a Aktualizace nebo inovace [vrácení zpět](/previous-versions/azure/reference/hh403977(v=azure.100)).

Dvě [operace, Get Deployment](/previous-versions/azure/reference/ee460804(v=azure.100)) a [Get Vlastnosti cloudové služby](/previous-versions/azure/reference/ee460806(v=azure.100)), vrátí příznak Uzamčeno, který lze zkontrolován, aby se zjistilo, zda lze při daném nasazení vyvolat operaci mutování.

Chcete-li volat verzi těchto metod, která vrací příznak Uzamčeno, je nutné nastavit hlavičku požadavku na "x-ms-version: 2011-10-01" nebo novější. Další informace o záhlavích správy verzí naleznete v [tématu Service Management Versioning](/previous-versions/azure/gg592580(v=azure.100)).

<a name="distributiondfroles"></a>

## <a name="distribution-of-roles-across-upgrade-domains"></a>Rozdělení rolí mezi upgradovací domény
Azure distribuuje instance role rovnoměrně přes nastavený počet upgradovacích domén, které lze nakonfigurovat jako součást souboru definice služby (.csdef). Maximální počet upgradovacích domén je 20 a výchozí hodnota je 5. Další informace o tom, jak upravit soubor definice služby, naleznete v tématu [Schéma definice služby Azure (.csdef File)](cloud-services-model-and-package.md#csdef).

Pokud má například vaše role deset instancí, ve výchozím nastavení obsahuje každá upgradovací doména dvě instance. Pokud vaše role obsahuje 14 instancí, pak čtyři z upgradu domény obsahují tři instance a páté domény obsahuje dvě.

Upgradovací domény jsou označeny indexem založeným na nule: první upgradovací doména má ID 0 a druhá upgradovací doména má ID 1 a tak dále.

Následující diagram znázorňuje, jak jsou distribuovány služby, než obsahuje dvě role, když služba definuje dvě domény upgradu. Služba je spuštěna osm instancí webové role a devět instancí role pracovního procesu.

![Distribuce upgradovacích domén](media/cloud-services-update-azure-service/IC345533.png "Distribuce upgradovacích domén")

> [!NOTE]
> Všimněte si, že Azure řídí, jak jsou instance přidělovány napříč upgradovacími doménami. Není možné určit, které instance jsou přiděleny které doméně.
>
>

## <a name="next-steps"></a>Další kroky
[Jak spravovat cloudové služby](cloud-services-how-to-manage-portal.md)  
[Jak sledovat cloudové služby](cloud-services-how-to-monitor.md)  
[Jak konfigurovat Cloud Services](cloud-services-how-to-configure-portal.md)  



