---
title: Zásady správného řízení v Azure | Dokumentace Microsoftu
description: Přečtěte si o cloudové výpočetní služby, které je možné škálovat nahoru nebo dolů podle potřeb vaší aplikace nebo enterprise.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: 579e900ee6616af8fd197e501364acd8e18d3e37
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970519"
---
# <a name="governance-in-azure"></a>Zásady správného řízení v Azure

Azure nabízí řadu možností zabezpečení a možnosti jejich řízení tak, že splňujete jedinečným požadavkům vaší organizace nasazení.

Zásady správného řízení cloudu Azure označuje rozhodovacích procesech, kritéria a zásady, které jsou součástí plánování, architektury, získání, nasazení, operace a správa cloud computingu. Zásady správného řízení cloudu Azure poskytuje integrované auditu a konzultační přístupu k revizi a radí organizacím na využití platformy Azure. 

Vytvořte plán pro zásady správného řízení cloudu Azure, budete muset provést podrobný pohled na lidi, procesy a technologie, které jsou nyní na místě. Potom můžete vytvářet architektury, které usnadňují IT na podporu konzistentně obchodní potřeby a zároveň uživatelům poskytuje flexibilitu používat funkce Azure.

## <a name="implementation-of-policies-processes-and-standards"></a>Implementace zásady, procesy a norem 

Správa stal role a zodpovědnosti získejte přehled o implementaci zásad zabezpečení informací a provozní kontinuity podnikových procesů v Azure. Správa Azure zodpovídá za dohled zabezpečení a postupy kontinuity podnikových procesů v rámci svých odpovídajících týmů (včetně třetích stran). Zajišťuje taky dodržování zásad zabezpečení, procesy a standardy.

### <a name="account-provisioning"></a>Zřizování účtů

Definování hierarchie účtu je velkým krokem k použití a struktury služeb Azure v rámci společnosti. Je základní struktura zásad správného řízení. Zákazníci, kteří mají smlouvu Enterprise (EA) můžou rozdělit prostředí do oddělení, účtů a předplatných.

![Zřizování účtů](./media/governance-in-azure/security-governance-in-azure-fig1.png)

Pokud nemáte smlouvu Enterprise Agreement, zvažte použití [Azure značky](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) na úrovni předplatného k definování v hierarchii. Předplatné Azure je základní jednotkou, která obsahuje všechny prostředky. Definuje také několik omezení v rámci Azure, jako je počet jader a prostředky. Předplatné může obsahovat [skupiny prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), který může obsahovat prostředky. [Řízení přístupu na základě role (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) zásady se vztahují na tyto tři úrovně.

Každá společnost se liší. Pro společnosti mimo kategorii enterprise hierarchii pomocí značky Azure umožňuje flexibilitu při uspořádání Azure. Dříve, než nasadíte prostředky v Azure, by měl modelů hierarchie a porozumět dopadům na fakturace, přístup k prostředkům a složitosti.

### <a name="subscription-controls"></a>Ovládací prvky předplatného

Předplatná určit, jak fakturovat se hlásí využití prostředků. Můžete nastavit předplatné pro samostatné fakturace a plateb. Jeden účet Azure může obsahovat víc předplatných. Předplatná je možné určit využití prostředků Azure z několika oddělení v rámci podniku.

Například společnost má IT, HR, a oddělení marketingu a tyto oblasti jsou spuštěné jiné projekty. Společnost můžete založit jeho fakturace na každé oddělení využití prostředků Azure, jako jsou virtuální počítače. Společnost můžete pak řídit finance každé oddělení.

Předplatná Azure vytvořit tři parametry:

- ID jedinečné předplatitele

- Umístění fakturace

- Sada dostupných prostředků

Pro jednotlivce tyto parametry zahrnují ID pro jeden účet Microsoft, číslo platební karty a kompletní sadu služeb Azure. Microsoft vynucuje omezení využití, v závislosti na typu předplatného.

Registrace v Azure hierarchie definovat strukturování služby v rámci smlouvy Enterprise. Smlouvy Enterprise portal umožňuje zákazníkům k rozdělení přístupu k prostředkům Azure, které jsou spojené se smlouvou Enterprise, který je založen na flexibilním hierarchie, které lze přizpůsobit podle potřeb vaší organizace. Vzor hierarchie by měl odpovídat správy a geografické strukturu pro související fakturace a přístup k prostředkům vaší organizace.

Tyto tři vzory se dají vysoké úrovně hierarchie jsou funkční, obchodní jednotku a geografické. Oddělení jsou administrativní konstrukce pro seskupení účtu. V rámci každé oddělení můžete účty přiřazené předplatné, které vytvoření sila pro fakturaci a několik klíčových omezení v Azure (třeba počet virtuálních počítačů a účty úložiště).

![Ovládací prvky předplatného](./media/governance-in-azure/security-governance-in-azure-fig2.png)


Organizace se smlouvou Enterprise předplatná Azure, postupujte podle čtyři úrovně hierarchie:

1. Registrace podnikového správce.

2. Oddělení správce

3. Vlastník účtu

4. Správce služeb

Tato hierarchie se řídí tímto:

- Fakturační vztah.

- Správa účtu.

- Přístup k prostředkům pomocí RBAC.

- Hranice:

  - Využití a fakturace (sazebník podle čísla nabídka)

  - Omezení

  - Virtuální síť

- Přílohy do Azure Active Directory (Azure AD). Jedna instance služby Azure AD může být přidružený k více předplatným.

- Přidružení s účtem organizace registrace.

### <a name="role-based-access-control"></a>Řízení přístupu na základě role

[RBAC](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) umožňuje správu přístupu podrobné prostředků v Azure. Pomocí RBAC můžete udělit pouze takovou úroveň přístupu, že uživatelé potřebují k provádění svých úloh. Společnosti byste se zaměřit na poskytuje zaměstnancům konkrétní oprávnění, které potřebují. Příliš mnoho oprávnění zpřístupňují účet tak, aby útočníci. Příliš málo oprávnění znamená, že zaměstnanci nelze práci efektivně. 

Ne všichni poskytuje neomezená oprávnění v předplatném Azure nebo prostředky, můžete povolit pouze určité akce. Například můžete RBAC nechat jednoho zaměstnance spravovat virtuální počítače v předplatném, zatímco jiné zaměstnance spravuje databáze SQL ve stejném předplatném.

Pokud chcete udělit přístup, přiřazení rolí uživatelům, skupinám nebo aplikacím v určitém rozsahu. Obor přiřazení role může být předplatné, skupinu prostředků nebo jediný prostředek. Role přiřazená na nadřazeném oboru uděluje přístup také podřízené objekty jsou v něm obsaženy. Například uživatel s přístupem ke skupině prostředků můžete spravovat všechny prostředky, které obsahuje, jako jsou weby, virtuální počítače a podsítě. V rámci každého předplatného můžete vytvořit až 2000 přiřazení rolí.

Role je kolekce oprávnění a má několik předdefinovaných rolí RBAC. Následující předdefinované role se vztahují na všechny typy prostředků:

- **Vlastník** má úplný přístup ke všem prostředkům, včetně práva na delegovat přístup ostatním uživatelům.

- **Přispěvatel** můžete vytvářet a spravovat všechny typy prostředků Azure, ale nemůže udělovat přístup ostatním uživatelům.

- **Čtečka** můžete zobrazit všechny prostředky Azure.

Zbytek předdefinované role v Azure umožňují správu konkrétních prostředků Azure. Třeba role Přispěvatel virtuálních počítačů umožňuje uživatelům vytvářet a spravovat virtuální počítače. Seznam předdefinovaných rolí a jejich operací najdete v tématu [předdefinované role RBAC](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

RBAC podporuje operace správy prostředků Azure v rozhraní API Azure Resource Manageru a webu Azure portal. Ve většině případů nejde autorizovat RBAC dat na úrovni operace pro prostředky Azure. Informace o tom, jak se rozšiřuje RBAC pro operace s daty, najdete v části [pochopení definic rolí](https://docs.microsoft.com/azure/role-based-access-control/role-definitions).

Pokud předdefinované role nevyhovují vašim potřebám specifický přístup, můžete si [vytvořit vlastní roli](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). Stejně jako předdefinované role je možné vlastní role přiřadit uživatelům, skupinám a aplikacím na předplatné, skupinu prostředků a prostředků oboru. Můžete vytvořit vlastní role pomocí [prostředí Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)a [rozhraní REST API](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

### <a name="resource-management"></a>Správa prostředků

Azure nabízí dva modely nasazení: [classic](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) a správce prostředků Azure.

V případě klasického modelu každý prostředek existuje nezávisle na sobě. Neexistuje žádný způsob, jak seskupit související prostředky. Budete muset manuálně sledovat prostředků, které tvoří řešení nebo aplikace a nezapomeňte k jejich správě koordinovat přístup. Je základní jednotkou správy předplatného. Je těžké rozdělit prostředky v rámci předplatného, což vede k vytvoření velkého počtu předplatných.

Model nasazení Resource Manager zahrnuje koncept [skupiny prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Skupina prostředků je kontejner pro prostředky, které sdílejí společný životní cyklus. To může zahrnovat všechny prostředky pro řešení nebo jenom prostředky, které chcete spravovat jako skupinu. Na základě toho, co je pro vaši organizaci nejvhodnější, rozhodnete, jakým způsobem se mají prostředky přidělovat do skupin prostředků.

Model nasazení Resource Manager poskytuje několik výhod:

- Všechny služby pro vaše řešení můžete nasadit, spravovat a monitorovat jako skupinu a nemusíte je zpracovávat jednotlivě.

- Můžete opakovaně nasazovat vaše řešení v průběhu životního cyklu a mít přitom jistotu, že se prostředky nasadí konzistentně.

- Můžete využít řízení přístupu ke všem prostředkům ve vaší skupině prostředků. Tyto zásady automaticky použije při přidávání nových prostředků do skupiny prostředků.

- Můžete označit prostředky pomocí značek a logicky tak uspořádat všechny prostředky ve svém předplatném.

- K definování infrastruktury řešení můžete použít formát JSON (JavaScript Object Notation). Soubor JSON se označuje jako šablona Resource Manageru.

- Můžete definovat závislosti mezi prostředky, takže se nasadí ve správném pořadí.

![Resource Manager](./media/governance-in-azure/security-governance-in-azure-fig4.png)

Další doporučení k šablonám najdete v tématu [Osvědčené postupy pro vytváření šablon Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices).

Azure Resource Manager analyzuje závislosti a pomáhají zajistit, že se prostředky vytvoří ve správném pořadí. Pokud jeden prostředek závisí na hodnotě z jiného prostředku (například virtuální počítač potřebuje účet úložiště pro disky), které [nastavit závislost](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies) v šabloně.

Šablony můžete také využít pro aktualizace infrastruktury. Můžete například ke svému řešení přidat prostředek a konfigurační pravidla pro prostředky, které jsou už nasazené. Pokud šablona specifikuje vytvoření prostředku, ale tento prostředek již existuje, Resource Manager provede aktualizaci místo vytvoření nového assetu. Její data zahrnují doby odezvy, závislosti, výjimky, trasování, ladění snímků a profily spouštění.

Poskytuje nástroje pro analýzu všechna tato telemetrie si můžete usnadnit ladění aplikace i vám pomohou pochopit, jak uživatelé pracují s ním.

### <a name="resource-tracking"></a>Můžete zjistit, zda nárůst doby odezvy je z důvodu něco v aplikaci nebo některé externí pravděpodobně problém.

Pokud používáte Visual Studio a aplikace se při selhání, můžete přejít přímo na problém řádek kódu, můžete to napravit. Log [Analytics](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) je pro uživatele, kteří potřebují k vyladění výkonu a plánování údržby na aplikace běžící v produkčním prostředí. Shromažďuje a agreguje data z mnoha zdrojů, s trvat 10 až 15 minut. Poskytuje holistické řešení pro správu IT pro Azure, místní a cloudové infrastruktury třetí strany (například Amazon Web Services).

Poskytuje nástroje pro analýzu dat napříč zdroji, umožňuje složitých dotazů ve všech protokolů a můžete proaktivně upozorní na zadané podmínky. Můžete dokonce shromáždění vlastních dat v centrálním úložišti a následně dotazovat a vizualizovat tato data.

System Center Operations Manager je pro správu a monitorování velkých cloudových zařízení. Možná už znáte jako nástroj pro správu pro místní Windows Server a Hyper-V na základě cloudy, ale můžete také integrovat a Správa aplikací pro Azure. Mimo jiné ho můžete nainstalovat na existující živé aplikace Application Insights. Označení může také hrát důležitou roli při omezení zbytečných nákladů ve vašem předplatném.

Pokud aplikace přestane fungovat, Operations Manageru zjistíte v řádu sekund. Příklady implementace zásad správného řízení předplatného Azure

Microsoft Azure Government Můžete vytvořit zásady k zajištění, že prostředky jsou označené odpovídajícími hodnotami.

Označené prostředky můžete také zobrazit přes Azure Portal. [Sestavu využití](https://docs.microsoft.com/azure/billing/billing-understand-your-bill) pro vaše předplatné obsahuje názvy a hodnoty značek, takže je lze rozdělit náklady podle značek.

Další informace o značkách najdete v tématu [iniciativa zásad fakturačních značek](../azure-policy/scripts/billing-tags-policy-init.md).

Na značky se vztahují následující omezení:

- Každý prostředek nebo skupina prostředků může mít maximálně 15 párů klíč/hodnota značek. Toto omezení se vztahuje pouze na značky použité přímo na prostředek nebo skupinu prostředků. Skupina prostředků může obsahovat řadu prostředků nichž každý má 15 párů klíč/hodnota značek.

- Název značky je omezen na 512 znaků.

- Hodnota značky je omezena na 256 znaků.

- Prostředky ve skupině prostředků nedědí značky použité na danou skupinu prostředků.

Pokud k prostředku potřebujete přidružit více než 15 hodnot, použijte jako hodnotu značky řetězec JSON. Řetězec JSON může obsahovat mnoho hodnot, které se použijí ke klíči jedné značky.

#### <a name="tags-for-billing"></a>Značky pro fakturaci

Značky umožňují seskupit fakturačních údajů. Například pokud používáte více virtuálních počítačů pro jiné organizace, použití značek na použití skupiny podle nákladových středisek. Značky lze použít také ke kategorizaci náklady podle prostředí modulu runtime, jako je například fakturovaného využití pro virtuální počítače spuštěné v provozním prostředí.

Můžete načíst informace o značkách prostřednictvím [využití prostředků Azure a RateCard API](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview) nebo použití souboru hodnot oddělených čárkami (CSV). Stáhněte si soubor využití z [účty Azure portal](https://account.windowsazure.com/) nebo [portál EA](https://ea.azure.com/).

Další informace o programový přístup k fakturačním údajům najdete v tématu [získání přehledů o spotřebě prostředků Microsoft Azure](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview). Operace rozhraní REST API najdete v části [Azure Billing Reference k REST API](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Když si stáhnete použití sdíleného svazku clusteru pro služby, které podporují značky s využitím fakturace, značky se zobrazí ve sloupci značky.

### <a name="critical-resource-controls"></a>Ovládací prvky důležitých prostředků

Jak vaše organizace přidá základních služeb pro předplatné, bude další důležité zajistit, že tyto služby jsou k dispozici, aby se zabránilo narušení činnosti firmy. [Zámky prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) umožňují omezení týkající se prostředků vysoké hodnoty, jejichž změně nebo odstranění by mohlo mít významný dopad na vaše aplikace nebo cloudové infrastruktury. Zámky můžete použít pro předplatné, skupinu prostředků nebo prostředek. Obvykle použijete zámky na základní prostředky, jako jsou virtuální sítě, bran a účty úložiště.

Zámky prostředků aktuálně podporují dvě hodnoty: **CanNotDelete** a **jen pro čtení**. **CanNotDelete** znamená, že se můžete stále uživatelů (s odpovídající práva) čtení nebo upravení prostředku ale nelze ji odstranit. **Jen pro čtení** znamená, že oprávnění uživatele nejde odstranit ani změnit prostředek.

Zámky prostředků platí pouze pro operace, ke kterým dochází v rovině správy, který se skládá z operací odesílat <https://management.azure.com>. Zámky Neomezovat, jak prostředky provádět vlastní funkce. Změn prostředků jsou omezené, ale operace prostředků nejsou omezené. Například **jen pro čtení** brání zámku v databázi SQL, můžete odstranit nebo upravit databázi, ale přesto můžete ve vytváření, aktualizaci nebo odstraňování dat v databázi.

Použití **jen pro čtení** může vést k neočekávaným výsledkům, protože některé operace, které se zdají být přečíst operace vyžadují další akce. Například, že umístíte **jen pro čtení** výpis klíčů všem uživatelům zabrání zámek účtu úložiště. Operace výpis klíčů probíhá prostřednictvím funkce požadavek POST, protože vrácený klíče jsou k dispozici pro operace zápisu.

![Ovládací prvky důležitých prostředků](./media/governance-in-azure/security-governance-in-azure-fig5.png)

Další příklad umístění **jen pro čtení** zámek na prostředek služby Azure App Service brání Průzkumníka serveru Visual Studia v zobrazení souborů pro prostředek, protože danou interakci vyžaduje oprávnění k zápisu.

Na rozdíl od řízení přístupu podle role pomocí zámky pro správu platí omezení na všech uživatelů a rolí. Další informace o nastavení oprávnění najdete v tématu [správě přístupu pomocí RBAC a webu Azure portal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

Při použití zámku v nadřazeném oboru dědí všechny prostředky v daném oboru stejný zámku. I prostředky, které přidáte později dědit Zámek z nadřazeného objektu. Nejvíc omezující zámku v dědičnosti má přednost.

Pokud chcete vytvořit nebo odstranit zámky pro správu, musí mít přístup k Microsoft.Authorization/ nebo Microsoft.Authorization/locks/ akcí. Z předdefinovaných rolí jsou udělena pouze vlastník a správce uživatelských přístupů těchto akcí.

### <a name="api-access-to-billing-information"></a>Přístup přes rozhraní API pro fakturační údaje

Pomocí Azure API pro fakturaci. data o využití a prostředků o přijetí změn do nástroje pro vaše preferované datové analýzy. Využití prostředků Azure a RateCard API můžete přesně předpovídat a spravovat svoje náklady. Rozhraní API se implementují jako poskytovatele prostředků a součástí rodiny API pomocí Azure Resource Manageru.

#### <a name="resource-usage-api-preview"></a>Využití prostředků rozhraní API (Preview)

Použití Azure [rozhraní API využití prostředků](https://msdn.microsoft.com/library/azure/mt219003) k přesunu dat Odhadované využití Azure. Toto rozhraní API zahrnuje:

- **RBAC**: Nakonfigurujte zásady přístupu na [webu Azure portal](https://portal.azure.com/) nebo prostřednictvím [rutin prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) k určení, které uživatele nebo aplikace můžete získat přístup k datům využití předplatného. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volajícího do role Čtenář fakturace, čtenář, vlastník nebo Přispěvatel získat přístup k datům využití pro konkrétní předplatné Azure.

- **Hodinových nebo denních agregací**: volající můžete určit, zda chtějí svá data využití Azure, v přírůstcích po hodinových nebo denních. Výchozí hodnota je denně.

- **Instance metadata (včetně značky prostředku)**: získání podrobností na úrovni instance jako plně kvalifikovaný identifikátor URI (/subscriptions/ {id předplatného} /..), informace o skupinách prostředků a značky prostředku. Tato metadata vám deterministicky a programově přidělit využití podle klíčových slov pro případy užití jako různé účtování.

- **Resource metadata**: Podrobnosti o prostředku, například název měřiče, kategorie měřiče, podkategorie měřiče, jednotky a oblasti dejte volající lépe pochopit, co spotřebovával. Spolupracujeme také zarovnat terminologie resource metadata ve na webu Azure portal, Azure použití sdíleného svazku clusteru, EA fakturace sdíleného svazku clusteru a další veřejné webové prostředí, můžete korelovat data napříč prostředím.

- **Nabízí využití pro všechny typy**: data o využití je k dispozici pro všechny typy, včetně průběžných plateb, MSDN, peněžního závazku, peněžní kredit ve výši a EA nabízejí.

#### <a name="resource-ratecard-api"></a>Prostředek RateCard API

Můžete získat seznam dostupných prostředků Azure a odhadovaných informace o cenách pro každou Azure Resource RateCard API. Toto rozhraní API zahrnuje:

- **RBAC**: Konfigurace zásad přístupu na portálu Azure portal nebo pomocí rutin prostředí Azure PowerShell k určení, které uživatele nebo aplikace můžete získat přístup k datům RateCard. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volajícího do role Čtenář, přispěvatele nebo vlastníka získat přístup k datům využití ke konkrétnímu předplatnému Azure.

- **Podporu pro průběžné platby, MSDN, peněžního závazku využívání služeb a peněžní kredit ve výši nabídky (ale ne EA)**: Toto rozhraní API poskytuje informace sazeb Azure úrovně nabídky. Volající toto rozhraní API musíte předat informace o nabídce zobrazíte podrobnosti o prostředku a kurzy. EA se aktuálně nepodporuje, protože nabídky EA upravili sazby za registraci. 

#### <a name="scenarios"></a>Scénáře

Kombinace využití a RateCard API umožňuje tyto scénáře:

- **Principy Azure výdajů za daný měsíc**: použijte kombinaci využití a RateCard API získat lepší přehled o své cloudové výdaje za daný měsíc. Můžete analyzovat každou hodinu a každý den odhadne využití a poplatků.

- **Nastavení výstrah**: využít k získání odhadovaných cloudové využití a poplatky za a nastavit výstrahy na základě prostředků nebo peněžní na využití a RateCard API.

- **Předpověď vyúčtování**: Get Odhadované využití a cloudové výdaje a použití algoritmů strojového učení k předpovědi, co bude na faktuře na konci fakturačního cyklu.

- **Provést před spotřeby analýza nákladů**: použijte RateCard API předpovědět, kolik vaše faktura se pro očekávané využití při přesun úloh do Azure. Pokud máte existující úlohy v jiných cloudech nebo privátních cloudů, využití s Azure můžete také namapovat sazby získat lepší odhad Azure výdajů. Tento odhad dává možnost pro zaměření na nabídky a porovnání mezi typy jinou nabídku nad rámec s průběžnými platbami, jako jsou prostředky peněžních závazků a peněžního kreditu, který.

- **Provedení analýzy what-if**: můžete určit, zda je cenově výhodnější ke spouštění úloh v jiné oblasti nebo na jinou konfiguraci prostředků Azure. Náklady na prostředky Azure může lišit v závislosti na oblasti Azure, kterou používáte. Můžete také určit, pokud jiný typ nabídky Azure poskytuje lepší rychlost v prostředku Azure.

### <a name="networking-controls"></a>Ovládací prvky sítě

Přístup k prostředkům může být vnitřní (uvnitř podnikové sítě) nebo externí (přes internet). Je snadné uživatelé ve vaší organizaci můžou neúmyslně vložit prostředky na nesprávné místo a potenciálně škodlivým přístupem otevírat. Stejně jako u místních zařízení, musíte přidat podniky patřičných opatření na Ujistěte se, že uživatelé Azure dělat správná rozhodnutí.

![Ovládací prvky sítě](./media/governance-in-azure/security-governance-in-azure-fig6.png)

U předplatného zásad správného řízení zadejte následující základní prostředky základní řízení přístupu.

#### <a name="network-connectivity"></a>Připojení k síti

[Virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) jsou objekty typu kontejner pro podsítě. Když to není nezbytně nutné, virtuální síť se často používá pro připojení aplikací k interním firemním prostředkům. Služba Azure Virtual Network umožňuje zabezpečené propojení prostředků Azure mezi sebou s virtuálními sítěmi.

Virtuální síť je reprezentace vaší vlastní sítě v cloudu. Virtuální síť se o logickou izolaci cloudu Azure, které jsou vyhrazené pro vaše předplatné. Můžete také propojit virtuální sítě k místní síti.

Možnosti pro virtuální sítě Azure jsou následující:

- **Izolace**: virtuální sítě jsou od sebe. Můžete vytvořit samostatné virtuální sítě pro vývoj, testování a produkci, které používají stejné bloky adres CIDR. Naopak můžete vytvořit více virtuálních sítí, které používají různé bloky adres CIDR a propojit sítě. Virtuální síť můžete rozdělit do několika podsítí. Azure poskytuje interní překlad adres pro virtuální počítače a služby Azure Cloud Services instance rolí, které jsou připojené k virtuální síti. Volitelně můžete nakonfigurovat virtuální síť používat vlastní servery DNS, namísto použití Azure interní překlad adres.

- **Připojení k Internetu**: všechny Azure virtual machines a role instancí Cloud Services, které jsou připojené k virtuální síti mají přístup k Internetu, ve výchozím nastavení. Podle potřeby můžete také povolit příchozí přístup ke konkrétním prostředkům.

- **Připojení prostředků Azure**: prostředky Azure, jako jsou Cloud Services a virtuální počítače, můžete připojit ke stejné virtuální síti. Prostředky můžete připojit k sobě navzájem pomocí privátních IP adres, i když jsou v různých podsítích. Azure poskytuje výchozí směrování mezi podsítěmi, virtuálních sítích a sítích na pracovišti, takže není nutné konfigurovat a spravovat směrování.

- **Připojení k virtuální síti**: můžete propojit virtuální sítě k sobě navzájem. Prostředky, které jsou připojené k žádné virtuální sítě může komunikovat s jakýmikoli prostředky v jiné virtuální síti.

- **Místní připojení**: můžete propojit virtuální sítě do místní sítě prostřednictvím připojení k privátní síti mezi vaší sítí a Azure, nebo virtuální privátní sítě site-to-site (VPN) připojení přes internet.

- **Filtrování provozu**: můžete filtrovat síťového provozu (příchozí a odchozí) pro virtuální počítače a Cloud Services podle zdrojové IP adresy a portu, cílové IP adresy a portu a protokolu.

- **Směrování**: Volitelně můžete přepsat výchozí směrování Azure tím, že nakonfigurujete vlastní trasy, nebo pomocí trasy protokolu BGP přes bránu sítě.

#### <a name="network-access-controls"></a>Řízení přístupu k síti

[Skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) jsou (Nsg), jako jsou brány firewall a poskytovat pravidla pro způsob prostředku můžete "mluvit" v síti. Poskytují kontrolu nad jak podsítě (nebo virtuálním počítači) můžou připojit k Internetu nebo jiné podsítě ve stejné virtuální síti.

Skupina zabezpečení sítě obsahuje seznam pravidel zabezpečení, která povolují nebo odpírají síťový provoz prostředků připojených k virtuálním sítím Azure. Skupiny Nsg můžou být spojené s podsítí, jednotlivé virtuální počítače (classic) nebo jednotlivým síťovým rozhraním (NIC) připojených k virtuálním počítačům (Resource Manager).

Pokud skupinu zabezpečení sítě přidružená k podsíti, pravidla se vztahují na všechny prostředky, které jsou připojené k podsíti. Můžete dále omezit přenosy tím, že přidružíte skupinu NSG k virtuálnímu počítači nebo síťové kartě.

## <a name="security-and-compliance-with-organizational-standards"></a>Zabezpečení a dodržování předpisů se standardy organizace

Všechny firmy má různé potřeby a budou těžit z cloudových řešení nesporné výhody. Zákazníci všech typů ještě stejné základní nemuseli dělat starosti o přesun do cloudu. Co zákazníci chtějí od poskytovatelů cloudových služeb je:

- **Naše data zabezpečení**: lídři v oboru IT na vědomí, že cloud může poskytovat vyšší dat zabezpečení a administrativní řízení. Ale jsou to stále obavy, že migrace do cloudu se být je vystavena většímu nebezpečí hackery než jejich aktuální interních řešení.

- **Zajištění privátnosti našich dat**: Cloud services přinášejí výzvy ochrany osobních údajů. Jak společnosti do cloudu Šetřete náklady na infrastrukturu a vylepšit jejich flexibilitě, jsou také starosti neztratili kontrolu nad ukládat svoje data, kdo je používá a jak se používá.

- **Sdělte nám ovládací prvek**: I když společnosti využít výhod cloudu a nasazovat další inovativní řešení, musí zabývat neztratili kontrolu nad jejich data. Poslední zveřejňování státní úřady, přístup k datům zákazníků, právní a mimosoudních způsobem, zkontrolujte některé vedoucí opatrně ukládání svých dat v cloudu.

- **Transparentnost**: obchodní uživatelé s rozhodovací pravomocí pochopení důležitosti zabezpečení, ochrany osobních údajů a řízení. Můžete ale také chtějí možnost nezávisle ověřit, jak jejich data ukládají, k němu přistupovat a zabezpečené.

- **Udržovat kompatibilitu**: jako společnosti rozšířit jejich používání cloudových technologií, i nadále vyvíjí složitosti a rozsahu standardů a nařízení. Společnosti potřebují vědět, že budou splněny jejich standardy pro dodržování předpisů.

## <a name="security-configuration-for-monitoring-logging-and-auditing"></a>Konfigurace zabezpečení pro monitorování, protokolování a auditování

Předplatitelé Azure mohou svoje Cloudová prostředí spravovat z více zařízení. Tato zařízení patří pracovní stanice pro správu, počítače vývojářů a dokonce i Privilegovaná zařízení koncových uživatelů, které mají oprávnění ke konkrétním úlohám. 

V některých případech se funkce správy provádějí prostřednictvím webových konzol, například na webu Azure portal. V ostatních případech mohou existovat přímé připojení k Azure z místních systémů prostřednictvím sítí VPN, terminálových služeb, protokolů klientských aplikací nebo (prostřednictvím kódu programu) rozhraní API pro správu služby Azure (SMAPI). Kromě toho lze koncové body klienta buď domény připojený nebo izolované a nespravované, jako jsou tablety nebo smartphony.

Může tato variabilita významné riziko nasazení cloudu. Může být obtížné spravovat, sledovat a auditovat akce správy. Tato variabilita může zároveň způsobit ohrožení zabezpečení prostřednictvím neregulovaného přístupu ke koncovým bodům klientů, které se používají pro správu cloudových služeb. Používání obecných nebo osobních pracovních stanic k vývoji a správě infrastruktury otevírá možnosti útoků z nečekaných směrů, například prohlížení webu (např. útok typu watering hole) nebo e-mailu (např. sociální inženýrství a phishing).

Sledování, protokolování a auditování poskytují základ pro sledování a pochopení aktivit správy. Auditování všech akcí podrobné nemusí být vždy proveditelné kvůli množství dat, které jsou generovány. Ale auditování účinnosti zásad správy je to osvědčený postup.

Zásady správného řízení zabezpečení služby Azure z objektů zásad skupiny Azure Active Directory Domain Services (AD DS) můžete ovládací prvek rozhraní Windows pro všechny správce, například sdílení souborů. Zahrňte pracovní stanice pro správu sledování, protokolování a auditování procesů. Sledujte všechny přístupy a chování správců a vývojářů.

### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) poskytuje centralizovaný pohled na stav zabezpečení prostředků v rámci předplatných. Poskytuje doporučení, která pomáhá zabránit ohrožených prostředků. Ji můžete povolit podrobné zásady pro – například uplatňovat zásady na určitých skupinách prostředků, které umožňují enterprise přizpůsobit jejich stav na riziko, které při adresování.

![Azure Security Center](./media/governance-in-azure/security-governance-in-azure-fig7.png)

Security Center poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, které jinak nevšimli a spolupracuje s řadou řešení zabezpečení. Po povolení [zásady zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-policies) pro prostředky předplatného, Security Center analyzuje zabezpečení vašich prostředků, aby identifikovala potenciální ohrožení zabezpečení. Informace o konfiguraci vaší sítě jsou k dispozici okamžitě.

Azure Security Center představuje kombinaci vzorů osvědčených postupů analýzy a zabezpečení Správa zásad pro všechny prostředky v rámci předplatného Azure. Umožňuje týmu zabezpečení a rizika vedoucí pracovníci pověření ochranou zabránit, detekovat a reagovat na hrozby zabezpečení tak, jak automaticky shromažďuje a analyzuje data zabezpečení z vašich prostředků Azure, sítě a řešení partnerů, jako jsou antimalwarové programy a brány firewall.

Kromě toho Azure Security Center používá pokročilou analýzu, včetně machine learningu a analýzy chování. Využívá globální analýzu hrozeb z produktů a služeb Microsoft Digital Crimes Unit (DCU), Microsoft Security Response Center (MSRC), Microsoftu a externích informačních kanálů. Můžete použít [zásad správného řízení zabezpečení](https://www.credera.com/blog/credera-site/azure-governance-part-4-other-tools-in-the-toolbox/) široce na úrovni předplatného. Nebo můžete zúžit na konkrétní požadavky a použít je u jednotlivých prostředků prostřednictvím definic zásad.

A konečně Azure Security Center analyzuje stav zabezpečení prostředků na základě těchto zásad a používá tyto informace poskytnout přehledné řídicí panely a upozorňování pro události, například zjištění malwaru nebo připojení IP se zlými úmysly pokusy. Další informace o tom, jak používat doporučení, najdete v části [implementace doporučení zabezpečení v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

Azure Security Center monitoruje prostředky Azure následující:

- Virtuální počítače (VM) (včetně cloudových služeb)

- Virtuální sítě

- Databáze SQL

- Partnerských řešení integrovaných ve vašem předplatném Azure, jako je například firewallu webových aplikací na virtuálních počítačích a na [služby App Service Environment](https://docs.microsoft.com/azure/app-service/app-service-app-service-environments-readme)

Při prvním přístupu ke službě Security Center, je povolené shromažďování dat u všech virtuálních počítačů ve vašem předplatném. Doporučujeme zachovat povolené shromažďování dat ale můžete [zakázat](https://docs.microsoft.com/azure/security-center/security-center-faq) v zásadách centra zabezpečení.

### <a name="log-analytics"></a>Log Analytics

Azure Log Analytics software development a služba týmu zabezpečení informací a [zásad správného řízení programu](https://github.com/Microsoft/azure-docs/blob/master/articles/log-analytics/log-analytics-security.md) podporuje jeho obchodní požadavky. Dodržuje zákony a nařízeními, jak je popsáno v [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) a [Microsoft Trust Center dodržování předpisů](https://www.microsoft.com/TrustCenter/Compliance/default.aspx). Jak Log Analytics zjistí požadavky na zabezpečení, identifikuje kontrolních mechanismů pro zabezpečení a spravuje a monitorování rizik je také popsáno zde. Ročně tým kontroly zásad, standardů, postupy a pokyny.

Každý člen týmu vývoje Log Analytics přijímá školení o zabezpečení formální aplikace. Systém správy verzí k ochraně každý softwarový projekt ve vývoji.

Microsoft má tým zabezpečení a dodržování předpisů, který dohlíží a vyhodnocuje všechny služby v Microsoftu. Informace o zabezpečení vedoucí pracovníci pověření ochranou tvoří tým a nejsou spojené s technické oddělení, které vývoj Log Analytics. Pracovníci zabezpečení mají své vlastní řetězec správy. Jejich chování nezávislé hodnocení produktů a služeb k zajištění zabezpečení a dodržování předpisů.

Základní funkce služby Log Analytics poskytuje sadu služeb, které běží v Azure. Každá služba poskytuje specifickou funkci správy a kombinováním služeb je možné dosahovat různých scénářů správy.

![Služby Azure pro správu](./media/governance-in-azure/security-governance-in-azure-fig9.JPG)

Tyto služby pro správu byly navržené pro cloud. Se už výhradně hostované v Azure, proto není zahrnují nasazení a správy místních prostředků. Konfigurace je minimální a může být zprovoznění v řádu minut.

Umístěte agenta na libovolném počítači Windows nebo Linux ve vašem datovém centru a bude odesílat data do Log Analytics. Existuje mohou být Analyzovaná spolu s dalšími daty shromážděnými z cloudu nebo místních služeb. Využijte výhod cloudu pro zálohování a vysokou dostupnost místních prostředků pomocí Azure Backup a Azure Site Recovery.

![Služby správy na řídicím panelu Azure](./media/governance-in-azure/security-governance-in-azure-fig8.png)

Runbooky v cloudu nemohou běžně přistupovat k místním prostředkům, ale můžete nainstalovat agenta na jeden nebo více počítačů, které budou hostovat runbooky v datovém centru. Při spuštění sady runbook, určíte, zda chcete spustit v cloudu nebo v místním pracovním procesu.

Jiná řešení jsou k dispozici od Microsoftu a partnerů můžete přidat do předplatného Azure ke zvýšení hodnoty investice do Log Analytics. Například Azure nabízí [řešení pro správu](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions)– balené sady logik, které implementují scénář správy pomocí jedné nebo více služeb správy.

![Galerie řešení pro správu v Azure](./media/governance-in-azure/security-governance-in-azure-fig10.png)

Jako partner můžete vytvořit svoje vlastní řešení pro podporu vašich aplikací a služeb a poskytnout je uživatelům prostřednictvím šablon Azure Marketplace nebo rychlý start.

## <a name="performance-alerting-and-monitoring"></a>Výstrahy výkonu a monitorování

### <a name="alerting"></a>Zobrazení výstrah

Oznámení představují způsob monitorování prostředků Azure metriky, události nebo protokoly. Oznámí vám při splnění podmínky, které jste zadali.

Výstrahy jsou dostupné v rámci služeb, včetně:

- **Azure Application Insights**: povolí webové výstrahy o metrikách a test. Další informace najdete v tématu [nastavit výstrahy ve službě Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) a [monitorovat dostupnost a odezvu libovolné webové stránky](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability).

- **Log Analytics**: umožňuje směrování aktivity a diagnostických protokolů do Log Analytics. To umožňuje metrik, protokolů a ostatní typy výstrah. Další informace najdete v tématu [upozornění v Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts).

- **Azure Monitor**: povolí upozornění na základě hodnoty metriky a události protokolu aktivit. Můžete použít [REST API služby Azure Monitor](https://msdn.microsoft.com/library/dn931943.aspx) správě těchto výstrah. Další informace najdete v tématu [pomocí webu Azure portal, Powershellu nebo rozhraní příkazového řádku k vytvoření výstrahy](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal).

### <a name="monitoring"></a>Monitorování

Problémy s výkonem v cloudové aplikaci může ovlivnit vaše podnikání. S několika propojených součástí a časté vydané verze může dojít, snížení výkonnosti v každém okamžiku. A pokud vyvíjíte aplikaci, vaši uživatelé obvykle zjišťovat problémy, které jste nenašli při testování. By měl vědět o tyto problémy okamžitě a mají nástroje pro diagnostiku a jejich implementace vyžadovala.

Existuje celou řadu nástrojů pro monitorování aplikace a služby Azure. Některé z jejich funkcí se překrývají. Toto je částečně kvůli rozostření mezi vývojem a používání aplikace.

Tady jsou základní nástroje:

- **Azure Monitor** je základní nástroj pro monitorování služby spuštěné v Azure. Poskytuje data na úrovni infrastruktury o propustnosti služeb a okolního prostředí. Pokud spravujete všechny vaše aplikace v Azure a rozhodování, jestli chcete vertikálně navýšit nebo snížit kapacitu prostředků, Azure Monitor vám umožňují spustit.

- **Application Insights** lze použít pro vývoj a jako produkční řešení pro monitorování. Funguje díky instalaci balíčku ve vaší aplikaci, takže ho vám ukáže, co se děje, více interní. Její data zahrnují doby odezvy, závislosti, výjimky, trasování, ladění snímků a profily spouštění. Poskytuje nástroje pro analýzu všechna tato telemetrie si můžete usnadnit ladění aplikace i vám pomohou pochopit, jak uživatelé pracují s ním. Můžete zjistit, zda nárůst doby odezvy je z důvodu něco v aplikaci nebo některé externí pravděpodobně problém. Pokud používáte Visual Studio a aplikace se při selhání, můžete přejít přímo na problém řádek kódu, můžete to napravit.

- **Log Analytics** je pro uživatele, kteří potřebují k vyladění výkonu a plánování údržby na aplikace běžící v produkčním prostředí. Shromažďuje a agreguje data z mnoha zdrojů, s trvat 10 až 15 minut. Poskytuje holistické řešení pro správu IT pro Azure, místní a cloudové infrastruktury třetí strany (například Amazon Web Services). Poskytuje nástroje pro analýzu dat napříč zdroji, umožňuje složitých dotazů ve všech protokolů a můžete proaktivně upozorní na zadané podmínky. Můžete dokonce shromáždění vlastních dat v centrálním úložišti a následně dotazovat a vizualizovat tato data.

- **System Center Operations Manager** je pro správu a monitorování velkých cloudových zařízení. Možná už znáte jako nástroj pro správu pro místní Windows Server a Hyper-V na základě cloudy, ale můžete také integrovat a Správa aplikací pro Azure. Mimo jiné ho můžete nainstalovat na existující živé aplikace Application Insights. Pokud aplikace přestane fungovat, Operations Manageru zjistíte v řádu sekund.


## <a name="next-steps"></a>Další postup

- [Osvědčené postupy pro vytváření šablon Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices)

- [Příklady implementace zásad správného řízení předplatného Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-subscription-examples)

- [Microsoft Azure Government](https://docs.microsoft.com/azure/azure-government/)
