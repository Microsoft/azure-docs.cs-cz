---
title: Zásady správného řízení v Azure | Microsoft Docs
description: Další informace o cloudové výpočetní služby, které je možné škálovat nahoru a dolů, aby vyhovovaly vaší aplikace nebo enterprise.
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
ms.openlocfilehash: 9c6509f25be7fe520a427e17ca1206e10f296fea
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110758"
---
# <a name="governance-in-azure"></a>Zásady správného řízení v Azure

Azure poskytuje mnoho možností zabezpečení a umožňuje řídit tak, že splňujete jedinečným požadavkům vaší organizace nasazení.

Zásady správného řízení cloudu Azure označuje rozhodovacích procesů, kritéria a zásady účastnící se plánování, architektura, získávání, nasazení, operace a správa cloud computing. Zásady správného řízení cloudu Azure poskytuje integrované auditu a konzultační přístup pro kontrolu a poradenství organizace na jejich využití platformy Azure. 

Pokud chcete vytvořit plán pro řízení cloudu Azure, budete muset provést hlubší pohled na osoby, procesy a technologie, které jsou nyní v místě. Potom může vytvořit rozhraní, které usnadňují tak konzistentně podporu obchodních potřeb současně nabízí uživatelům možnost používat funkce Azure.

## <a name="implementation-of-policies-processes-and-standards"></a>Implementace zásady, procesy a standardy 

Správa navázal rolích a zodpovědnostech k dohlížejí implementace zásady zabezpečení informací a provozní kontinuitu v Azure. Správa Azure zodpovídá za dohled nad zabezpečením a postupy kontinuity v rámci jeho odpovídající týmy (včetně třetích stran). Zajišťuje také dodržování zásad zabezpečení, procesy a standardy.

### <a name="account-provisioning"></a>Zřizování účtů

Definování hierarchie účtu je hlavní krok použít a struktury služby Azure v rámci společnosti. Je základní struktura zásad správného řízení. Zákazníci, kteří mají Enterprise Agreement (EA) můžete rozdělit prostředí do oddělení, účty a odběry.

![Zřizování účtů](./media/governance-in-azure/security-governance-in-azure-fig1.png)

Pokud nemáte smlouvu Enterprise Agreement, zvažte použití [Azure značky](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) na úrovni předplatného můžete definovat v hierarchii. Předplatné Azure je základní jednotkou, která obsahuje všechny prostředky. Definuje také několik omezení v rámci Azure, například na počtu jader a prostředky. Může obsahovat odběry [skupiny prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), která může obsahovat prostředky. [Řízení přístupu na základě role (RBAC)](https://docs.microsoft.com/en-us/azure/role-based-access-control/overview) zásady se vztahují na tyto tři úrovně.

Každý enterprise se liší. Pro společnosti, mimo organizaci hierarchii pomocí Azure značek umožňuje flexibilitu v tom, jak je organizovaná Azure. Před nasazením prostředky v Azure, by měl modelu hierarchie a pochopit vliv na fakturace, přístup k prostředkům a složitost.

### <a name="subscription-controls"></a>Ovládací prvky předplatného

Odběry určit, jak hlášené a účtují využití prostředků. Můžete nastavit odběry pro samostatné fakturace a platby. Jeden účet Azure může mít několik odběrů. Odběry slouží k určení využití prostředků Azure více oddělení v rámci podniku.

Například společnost má IT oddělení lidských zdrojů, a oddělení marketingu a tyto oddělení běží na různých projektů. Společnost můžete základní jeho fakturace na každé oddělení využití prostředků Azure, stejně jako virtuální počítače. Společnost můžete pak řídit finance jednotlivých oddělení.

Předplatná Azure vytvořit tři parametry:

- ID jedinečný odběratele

- Umístění fakturace

- Sadu dostupných prostředků

Pro jednotlivce tyto parametry patří jeden ID pro účet Microsoft, číslo platební karty a úplná sada služeb Azure. Microsoft vynucuje omezení spotřeby, v závislosti na typu předplatného.

Azure registrace hierarchií definovat, jak jsou strukturovaná služby v rámci smlouvy Enterprise. Smlouva Enterprise Agreement portál umožňuje zákazníkům k rozdělení přístupu k prostředkům Azure přidružené smlouvu Enterprise Agreement podle flexibilní hierarchie, které můžete přizpůsobit potřebám organizace. Vzor hierarchie by měl odpovídat správy a k účtu pro přidružené k fakturaci a prostředku přístup geografické strukturu organizace.

Jsou tři vzory vysoké úrovně hierarchie funkční, obchodní jednotky a geografické. Oddělení jsou pro správu konstrukce pro účet seskupení. V rámci každé oddělení můžete účty přiřazené odběry, které vytvoření sila pro fakturaci a několik klíčů omezení v Azure (například počet virtuálních počítačů a účty úložiště).

![Ovládací prvky předplatného](./media/governance-in-azure/security-governance-in-azure-fig2.png)


Pro organizace s smlouvu Enterprise Agreement předplatná Azure, postupujte podle čtyři úrovně hierarchie:

1. registrace podnikového správce.

2. Správce oddělení

3. vlastníka účtu

4. Správce služeb

Tato hierarchie řídí následující:

- Vztah fakturace.

- Správa účtu.

- Přístup k prostředkům prostřednictvím RBAC.

- Hranice:

  - Využití a fakturace (sazebník podle čísla nabídka)

  - Omezení

  - Virtuální síť

- Připojení k Azure Active Directory (Azure AD). Jedna instance služby Azure AD může být přidružen mnoho odběrů.

- Přidružení účet pro zápis podnikové sítě.

### <a name="role-based-access-control"></a>Řízení přístupu na základě role

[RBAC](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) umožňuje správu podrobné přístupu prostředků v Azure. Pomocí RBAC můžete poskytnout pouze takovou úroveň přístupu, aby uživatelé potřebují k provádění svých úloh. Společnosti by měla soustředit na poskytnutí zaměstnanci přesný oprávnění, která potřebují. Příliš mnoho oprávnění vystavit účet, který útočníkům. Příliš málo oprávnění znamená, že zaměstnanci nelze práci efektivně. 

Namísto udělení každý uživatel neomezený oprávnění v vašeho předplatného Azure nebo prostředky, můžete povolit jenom určité akce. Například můžete RBAC umožníte jednoho zaměstnance spravovat virtuální počítače v předplatném, zatímco jiným zaměstnancem spravuje databází SQL v rámci stejného předplatného.

Pokud chcete udělit přístup, je přiřadit role uživatele, skupiny nebo aplikace v určité oboru. Předplatné, skupinu prostředků nebo jediný zdroj, může být oboru přiřazení role. Role přiřazené v nadřazeném oboru podřízené objekty jsou v něm obsažena také uděluje přístup. Například uživatel s přístupem do skupiny prostředků můžete spravovat všechny prostředky, které obsahuje, jako jsou weby, virtuální počítače a podsítě. V rámci každé předplatné můžete vytvořit až 2 000 přiřazení rolí.

Role je kolekce oprávnění a RBAC má několik předdefinovaných rolí. Následující předdefinované role platí pro všechny typy prostředků:

- **Vlastník** má úplný přístup ke všem prostředkům, včetně právo delegovat přístup k ostatním.

- **Přispěvatel** můžete vytvářet a spravovat všechny typy prostředků Azure, ale nelze udělit přístup ostatním uživatelům.

- **Čtečka** můžete zobrazit všechny prostředky Azure.

Ostatní předdefinované role v Azure povolit správu konkrétních prostředků Azure. Například role Přispěvatel virtuálních počítačů umožňuje uživatelům vytvářet a spravovat virtuální počítače. Seznam předdefinovaných rolí a jejich operací najdete v tématu [předdefinované role RBAC](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

RBAC podporuje operace správy prostředků Azure v portálu Azure a rozhraní API Správce Azure Resource Manager. Ve většině případů nejde autorizovat RBAC dat na úrovni operace pro prostředky Azure. Informace o tom, jak RBAC rozšiřuje pro operace dat najdete v tématu [pochopit definice role](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-definitions).

Pokud předdefinované role nevyhovují vašim potřebám konkrétní přístup, můžete [vytvořit vlastní roli](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). Stejně jako předdefinované role můžete přiřadit vlastní role pro uživatele, skupiny a aplikace v předplatné, skupinu prostředků a prostředků oboru. Můžete vytvořit vlastní role pomocí [prostředí Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)a [REST API](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

### <a name="resource-management"></a>Správa prostředků

Azure poskytuje dva modely nasazení: [classic](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) a Azure Resource Manager.

V klasickém modelu všechny prostředky existuje nezávisle. Neexistuje žádný způsob, jak seskupit související prostředky. Budete muset ručně sledovat prostředky, ke kterým tvoří řešení nebo aplikace a nezapomeňte spravovat je v koordinovaný přístup. Základní jednotkou správy je předplatné. Je obtížné rozdělení prostředků v rámci předplatného, což vede k vytvoření velkého počtu předplatných.

Model nasazení Resource Manager zahrnuje koncept [skupiny prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Skupina prostředků je kontejner pro prostředky, které sdílejí společný životní cyklus. Může zahrnovat všechny prostředky pro řešení, nebo jenom prostředky, které chcete spravovat jako skupinu. Na základě toho, co je pro vaši organizaci nejvhodnější, rozhodnete, jakým způsobem se mají prostředky přidělovat do skupin prostředků.

Model nasazení Resource Manager poskytuje několik výhod:

- Všechny služby pro vaše řešení můžete nasadit, spravovat a monitorovat jako skupinu a nemusíte je zpracovávat jednotlivě.

- Můžete opakovaně nasadit řešení v průběhu životního cyklu a mít jistotu, že jsou vaše prostředky nasazené v konzistentním stavu.

- Řízení přístupu můžete použít na všechny prostředky ve vaší skupině prostředků. Tyto zásady budou automaticky použita při přidávání nových prostředků do skupiny prostředků.

- Můžete označit prostředky pomocí značek a logicky tak uspořádat všechny prostředky ve svém předplatném.

- K definování infrastruktury řešení můžete použít formát JSON (JavaScript Object Notation). Soubor JSON se označuje jako šablona Resource Manageru.

- Můžete definovat závislosti mezi prostředky, takže se nasadí ve správném pořadí.

![Resource Manager](./media/governance-in-azure/security-governance-in-azure-fig4.png)

Další doporučení k šablonám najdete v tématu [Osvědčené postupy pro vytváření šablon Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices).

Azure Resource Manager analyzuje závislosti k zajištění, že prostředky jsou vytvořeny ve správném pořadí. Pokud jeden prostředek spoléhá na hodnotu z jiného prostředku (například virtuální počítač, která potřebuje účet úložiště pro disky), můžete [nastavení závislosti](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies) v šabloně.

Šablony můžete také využít pro aktualizace infrastruktury. Můžete například ke svému řešení přidat prostředek a konfigurační pravidla pro prostředky, které jsou už nasazené. Pokud šablona specifikuje vytvoření prostředku, ale tento prostředek již existuje, Resource Manager provede aktualizaci místo vytvoření nového prostředku. Resource Manager aktualizuje stávající prostředek do stejného stavu, jako kdyby byl nový.

Resource Manager poskytuje rozšíření pro scénáře, pokud potřebujete další operace, například při instalaci softwaru, který není součástí instalace.

### <a name="resource-tracking"></a>Sledování prostředku

Jak uživatelé ve vaší organizaci přidat prostředky do odběru, stane se důležitější přidružovat prostředky k příslušné oddělení, zákazníků a prostředí. Metadata můžete připojit k prostředkům prostřednictvím [značky](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags). Použití značek k zadání informací o prostředku nebo vlastník. Značky umožňují nejen agregovat a skupiny prostředků několika způsoby, ale také použít data pro účely vracení peněz.

Používat značky, pokud máte komplexní kolekci skupin prostředků a prostředků a budete muset vizualizovat způsobem, který je pro vás nejvhodnější. Můžete například označit prostředky, které ve vaší organizaci poskytovat podobnou roli nebo které patří do stejného oddělení.

Bez použití značek uživatelé ve vaší organizaci můžete vytvořit různé prostředky, které může být obtížné později identifikovat a spravovat. Například můžete chtít odstranit všechny prostředky pro projekt. Pokud tyto prostředky nejsou označeny pro projekt, musíte je ručně vyhledat. Označení může také hrát důležitou roli při omezení zbytečných nákladů ve vašem předplatném.

Není nutné prostředky jsou umístěny ve stejné skupině prostředků se stejnou značkou. Můžete vytvořit vlastní taxonomii značek a zajistit, že všichni uživatelé ve vaší organizaci používat společné značky spíše než nechtěně použití mírně odlišné značky (třeba odd"místo"oddělení").

Zásady prostředků umožňují vytvořit standardní pravidla pro vaši organizaci. Můžete vytvořit zásady, abyste zajistili, že jsou prostředky označené odpovídající hodnoty.

Označené prostředky můžete také zobrazit přes Azure Portal. [Sestav využití](https://docs.microsoft.com/azure/billing/billing-understand-your-bill) pro vaše předplatné zahrnuje značka názvy a hodnoty, takže je můžete přerušení náklady podle značky.

Další informace o značkách najdete v tématu [fakturace značky zásad initiative](../azure-policy/scripts/billing-tags-policy-init.md).

Na značky se vztahují následující omezení:

- Každý prostředek nebo skupina prostředků může mít maximálně 15 páry klíč – hodnota značky. Toto omezení se vztahuje pouze na značky použité přímo na prostředek nebo skupinu prostředků. Skupiny prostředků může obsahovat mnoho prostředků nichž každá má 15 páry klíč – hodnota značky.

- Název značky je omezen na 512 znaků.

- Hodnota značky je omezena na 256 znaků.

- Prostředky ve skupině prostředků nedědí značky použité na danou skupinu prostředků.

Pokud k prostředku potřebujete přidružit více než 15 hodnot, použijte jako hodnotu značky řetězec JSON. Řetězce formátu JSON může obsahovat mnoho hodnot, které se použijí pro klíč jedinou značku.

#### <a name="tags-for-billing"></a>Značky pro fakturaci

Značky umožňují skupiny fakturační údaje. Například pokud spouštíte víc virtuálních počítačů pro jiné organizace, použijte značek k použití skupiny podle nákladové středisko. Značky lze použít také ke kategorizaci náklady prostředí runtime, jako je fakturace využití pro virtuální počítače spuštěné v provozním prostředí.

Můžete načíst informace o značkách prostřednictvím [využití prostředků Azure a rozhraní API RateCard](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview) nebo využití soubor hodnot oddělených čárkami (CSV). Stáhnout soubor využití z [portál účtů Azure](https://account.windowsazure.com/) nebo [EA portál](https://ea.azure.com/).

Další informace o programový přístup k fakturační informace najdete v tématu [proniknout do vaší spotřeby prostředků Microsoft Azure](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview). Operace REST API, najdete v části [referenční dokumentace rozhraní API Azure fakturace REST](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Při stahování použití sdíleného svazku clusteru pro služby, které podporují značky s fakturace, značky se zobrazí ve sloupci značky.

### <a name="critical-resource-controls"></a>Ovládací prvky kritické prostředků

Jak vaše organizace přidá základní služby pro předplatné, bude další důležité zajistit, že tyto služby jsou k dispozici chcete zabránit přerušení obchodní. [Uzamčení prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) vám umožní omezit operací s prostředky vysoké hodnoty, jejichž změně nebo odstranění by mohlo mít významný dopad na vaší aplikace nebo cloudové infrastruktury. Zámky můžete použít pro předplatné, skupinu prostředků nebo prostředek. Zámky obvykle použijete pro základní prostředkům, například virtuální sítě, bran a účty úložiště.

Uzamčení prostředků v současné době podporují dvou hodnot: **CanNotDelete** a **jen pro čtení**. **CanNotDelete** znamená, že uživatelé (s odpovídající práva) můžete stále číst nebo upravit prostředek ale nelze ho proto odstranit. **Jen pro čtení** znamená, že na autorizované uživatele nejde odstranit ani změnit prostředku.

Uzamčení prostředků se vztahují pouze na operace, které dojít v rovině řízení, sestávající operací posílá <https://management.azure.com>. Zámky nemáte omezit, jak provádět prostředky vlastní funkce. Změny prostředku jsou s omezeným přístupem, ale operace prostředků nejsou s omezeným přístupem. Například **jen pro čtení** zámku v databázi SQL zabráníte odstranění nebo úprava databázi, ale nezabrání vytváření, aktualizaci nebo odstranění dat v databázi.

Použití **jen pro čtení** může vést k neočekávaným výsledkům, protože některé operace, které vypadají podobně jako pro čtení, operace vyžadují další akce. Například umístění **jen pro čtení** výpis klíčů všem uživatelům zabrání zámku na účet úložiště. Operace uvedení klíče je prováděna pomocí požadavek POST, protože vrácený klíče jsou k dispozici pro operace zápisu.

![Ovládací prvky kritické prostředků](./media/governance-in-azure/security-governance-in-azure-fig5.png)

Další příklad umístění **jen pro čtení** zámku na prostředek služby Azure App Service zabrání zobrazení souborů pro daný prostředek, protože interakce vyžaduje oprávnění k zápisu Průzkumníka serveru Visual Studia.

Na rozdíl od řízení přístupu na základě rolí použít zámky správy pro aplikaci omezení ve všech uživatelů a rolí. Další informace o nastavení oprávnění najdete v tématu [Správa přístupu pomocí RBAC a webu Azure portal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

Když použijete zámku v nadřazeném oboru, zdědí všechny prostředky v rámci tohoto oboru stejné zámek. I prostředky, které můžete přidat později zámek dědí z nadřazeného objektu. Nejvíc omezující zámek v dědičnosti přednost.

Vytvořit nebo odstranit zámky správy, musí mít přístup k Microsoft.Authorization/ nebo Microsoft.Authorization/locks/ akcí. Z předdefinovaných rolí pouze vlastník a správce přístupu uživatelů mají tyto akce.

### <a name="api-access-to-billing-information"></a>Rozhraní API pro přístup k fakturační informace

Používáte rozhraní API Správce Azure fakturace k získání dat využití a prostředků do vašeho nástrojů pro analýzu dat upřednostňované. Rozhraní API RateCard a využití prostředků Azure vám může pomoct přesně předpovědět a náklady na správu. Rozhraní API jsou implementovány jako poskytovatel prostředků a součástí řady rozhraní API vystavené pomocí Správce prostředků Azure.

#### <a name="resource-usage-api-preview"></a>Využití prostředků rozhraní API (Preview)

Použití Azure [API využití prostředků](https://msdn.microsoft.com/library/azure/mt219003) načíst vaše data Odhadované využití platformy Azure. Rozhraní API obsahuje:

- **RBAC**: Nakonfigurujte zásady přístupu na [portál Azure](https://portal.azure.com/) nebo pomocí [rutin prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) k určení, které uživatele nebo aplikace můžete získat přístup k datům použití odběru. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volající do čtečky fakturace, čtečky, vlastníka nebo přispěvatele role získat přístup k dat o využití pro konkrétní předplatné Azure.

- **Hodinových nebo denních agregací**: volající můžete určit, zda chtějí jejich Azure využití dat v hodinových nebo denních přírůstcích. Výchozí hodnota je denně.

- **Instance metadat (zahrnuje značky prostředku)**: získání podrobností na úrovni instance jako plně kvalifikovaný identifikátor URI (/subscriptions/ {id předplatného} /..), informace o skupině prostředků a značky prostředku. Tato metadata pomáhá deterministicky a prostřednictvím kódu programu přidělit využití podle značky, pro případy použití jako mezi poplatků.

- **Metadata prostředků**: Podrobnosti prostředku, jako je například název měření, měřicí kategorie, podkategorie měření, jednotky a oblast poskytnout volající lépe porozumět tomu, co se spotřebovala. Pracujeme také zarovnat prostředků metadata terminologie přes portál Azure, Azure použití sdíleného svazku clusteru, EA fakturace sdíleného svazku clusteru a dalších činnostech veřejné, které vám pomůžou vazbu mezi data v prostředí.

- **Použití všech nabízet typy**: data o využití je k dispozici pro všechny typy, včetně průběžné platby, MSDN, peněžních závazků, peněžního kreditu, který a EA nabízejí.

#### <a name="resource-ratecard-api"></a>Prostředek RateCard rozhraní API

Použijte rozhraní API RateCard prostředků Azure k získání seznamu dostupných prostředků Azure a odhadovanou informace o cenách pro jednotlivé. Rozhraní API obsahuje:

- **RBAC**: Konfigurace zásad přístupu na portálu Azure nebo pomocí rutin prostředí Azure PowerShell k určení, které uživatele nebo aplikace můžete získat přístup k datům RateCard. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Volající se přidal k roli čtečky, vlastníka nebo přispěvatele získat přístup k dat o využití pro konkrétní předplatné Azure.

- **Podpora pro průběžné platby, MSDN, peněžních závazků a peněžního kreditu, který nabízí (ale ne EA)**: Toto rozhraní API poskytuje Azure míra nabídka úroveň informace. Volající toto rozhraní API musí projít informace nabídka získat podrobnosti zdroje a sazby. EA není aktuálně podporována, protože nabízí EA přizpůsobili sazby za registraci. 

#### <a name="scenarios"></a>Scénáře

Kombinace využití a RateCard rozhraní API umožňuje tyto scénáře:

- **Pochopení Azure tráví v měsíci**: pomocí kombinace využití serveru a rozhraní API RateCard získat lepší přehled o své cloudové náklady v měsíci. Můžete analyzovat každou hodinu a každodenní odhady využití a poplatků.

- **Nastavení výstrah**: použití rozhraní API RateCard a využití získat odhadovaný cloudu využívání a poplatky, a nastavte založené na prostředcích nebo peněžní na základě výstrahy.

- **Předpovídat faktury**: Get odhadované spotřeby a cloud tráví a použít algoritmy strojového učení k předvídání co kusovníku bude na konci fakturačního cyklu.

- **Provést předběžné spotřeba analýza nákladů**: použití rozhraní API RateCard odhadnout, kolik vaše faktura by byl očekávané využití při přesunutí úlohy do Azure. Pokud máte existující úlohy v ostatních cloudů nebo privátní cloudy, můžete namapovat vaše použití s Azure tráví sazby získat lepší odhad Azure. Tento odhad vám dává možnost otáčení na nabídku a porovnání mezi typy různých nabídka nad rámec průběžné platby, jako je peněžních závazků a peněžního kreditu.

- **Provedení analýzy citlivostních**: můžete určit, zda je cenově výhodnější pro spuštění úlohy v jiné oblasti nebo na jinou konfiguraci prostředků Azure. Náklady na prostředků Azure se můžou lišit podle na oblast Azure, kterou používáte. Můžete také určit, pokud jiný typ nabídky Azure poskytuje lepší rychlost na prostředek služby Azure.

### <a name="networking-controls"></a>Ovládací prvky sítě

Přístup k prostředkům může být vnitřní (v rámci podnikové sítě) nebo externí (prostřednictvím Internetu). Je snadné pro uživatele ve vaší organizaci nechtěně uvést prostředky do nesprávného místě a potenciálně škodlivým přístupem otevírat. Stejně jako u místní zařízení, musíte přidat podniky příslušných ovládacích prvků k zajištění, že Azure uživatelé provádět správné rozhodnutí.

![Ovládací prvky sítě](./media/governance-in-azure/security-governance-in-azure-fig6.png)

Následující prostředky jádra pro řízení předplatné, poskytují základní řízení přístupu.

#### <a name="network-connectivity"></a>Připojení k síti

[Virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) jsou objekty kontejneru pro podsítě. Když je to nezbytně nutné, virtuální sítě se často používá pro připojení aplikace k interním firemním prostředkům. Služba Azure Virtual Network umožňuje bezpečně se vzájemně připojovat prostředky Azure s virtuálními sítěmi.

Virtuální síť je reprezentace vlastní sítě v cloudu. Virtuální síť je to logická izolace cloudu Azure vyhrazeného pro vaše předplatné. Virtuální sítě můžete také připojit k místní síti.

Následují možnosti pro virtuální sítě Azure:

- **Izolace**: virtuální sítě jsou izolované od sebe navzájem. Můžete vytvořit samostatné virtuální sítě pro vývoj, testování a produkci, které používají stejné bloky adres CIDR. Naopak můžete vytvořit několik virtuálních sítí, které používají jiné bloky adres CIDR a společně připojení sítě. Virtuální síť můžete rozdělit do několika podsítí. Azure poskytuje interní překlad adres pro virtuální počítače a Azure Cloud Services instance rolí, které jsou připojené k virtuální síti. Volitelně můžete nakonfigurovat virtuální sítě pro použití vlastní servery DNS, místo použití Azure interní překlad adres.

- **Připojení k Internetu**: Azure všechny virtuální počítače a instance rolí cloudové služby, které jsou připojené k virtuální síti mají přístup k Internetu, ve výchozím nastavení. Podle potřeby můžete také povolit příchozí přístup ke konkrétním prostředkům.

- **Připojení prostředků Azure**: prostředky Azure, například cloudové služby a virtuální počítače, můžete připojit ke stejné virtuální síti. Prostředky můžete připojit k sobě navzájem prostřednictvím privátních IP adres, i když jsou v různých podsítích. Azure poskytuje výchozí směrování mezi podsítěmi, virtuální sítě a místní sítě, takže není nutné konfigurovat a spravovat trasy.

- **Připojení k virtuální síti**: virtuální sítě můžete připojit k sobě navzájem. Prostředky, které jsou připojené k žádné virtuální síti se pak může komunikovat s jakýmikoli prostředky na jiné virtuální síti.

- **Místní připojení**: virtuální sítě můžete připojit k místní sítě prostřednictvím privátní sítě připojení mezi vaší sítí a Azure, nebo připojení site-to-site virtuální privátní sítě (VPN) přes internet.

- **Filtrování provozu**: můžete filtrovat síťového provozu (příchozí a odchozí) pro virtuální počítače a cloudové služby zdrojové IP adresy a portu, cílové IP adresy a portu a protokolu.

- **Směrování**: Volitelně můžete přepsat výchozí Azure směrování pouze vlastní trasy, nebo pomocí trasy protokolu BGP prostřednictvím brány sítě.

#### <a name="network-access-controls"></a>Ovládací prvky pro přístup k síti

[Skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (Nsg) jsou jako brána firewall a poskytují pravidla pro jak prostředku může "kontaktovat" přes síť. Poskytují kontrolu nad jak podsíť (nebo virtuálním počítači) připojit k Internetu nebo jiných podsítí ve stejné virtuální síti.

Skupina zabezpečení sítě obsahuje seznam pravidel zabezpečení, která povolují nebo odpírají síťový provoz prostředky připojenými k virtuálních sítí Azure. Skupiny Nsg můžou být spojené s podsítí, jednotlivé virtuální počítače (klasické) nebo jednotlivým síťovým rozhraním (síťovým kartám) připojených k virtuálním počítačům (Resource Manager).

Pokud je skupina NSG přidružená k podsíti, pravidla se vztahují na všechny prostředky, které jsou připojené k podsíti. Provoz můžete dál omezit tím, že přidružíte skupinu NSG k virtuálního počítače nebo síťový adaptér.

## <a name="security-and-compliance-with-organizational-standards"></a>Zabezpečení a dodržování předpisů s organizační standardy

Každé obchodní má jiné požadavky a bude vytěžit maximum odlišné výhody z cloudové řešení. Zákazníci nejrůznějších ještě stejné základní pochybnostmi přechodu do cloudu. Zákazníci chcete od poskytovatelů cloudu je:

- **Zabezpečení našich dat**: žebříčky IT na vědomí, že může poskytovat cloudu, vyšší zabezpečení a administrativní řízení. Jsou ale stále obavy, že migrace do cloudu ponechá je zranitelnější vůči hackery než jeho aktuální interní řešení.

- **Naše data zachovat privátní**: cloudové služby vyvolat výzvy jedinečný ochrany osobních údajů. Jak společností, vypadat do cloudu uložte na náklady na infrastrukturu a zvýšit jejich flexibilitě, budou také obávat ztráty řízení toho, kde mají uložená data, který je k ní přistupují a jak získá používají.

- **Sdělte nám řízení**: to i v případě společnosti využít výhod cloudu nasadit další inovativní řešení, jsou zajímá neztratili kontrolu nad svá data. Nejnovější prohlášení o přístup k datům zákazníka, právních i mimosoudních způsoby, organizace státní správy zkontrolujte některé ředitelé informačních technologií opatrní ukládání svých dat v cloudu.

- **Zvýšit úroveň průhlednosti**: obchodní rozhodnutí ve firmě pochopit význam zabezpečení, ochrany osobních údajů a řízení. Můžete ale také kvůli nezávisle ověřit jak jejich data ukládají, přístupu a zabezpečené.

- **Udržovat kompatibilitu**: společností rozšířit jejich používání cloudových technologií, složitost a obor standardů a nařízení dál momentální. Společnosti potřebovat znát splnění standardů jejich dodržování předpisů.

## <a name="security-configuration-for-monitoring-logging-and-auditing"></a>Konfigurace zabezpečení pro sledování, protokolování a auditování

Předplatitelé služby Azure mohou svoje Cloudová prostředí spravovat z více zařízení. Tato zařízení mohou zahrnovat pracovní stanice pro správu, počítače vývojářů a dokonce i Privilegovaná zařízení koncových uživatelů, které mají oprávnění ke konkrétním úlohám. 

V některých případech se funkce správy provádějí prostřednictvím webových konzol, například na portálu Azure. V ostatních případech mohou existovat přímá připojení k Azure z místních systémů prostřednictvím sítě VPN, terminálových služeb, protokolů klientských aplikací nebo (v kódu) rozhraní API pro správu služby Azure (SMAPI). Kromě toho lze koncové body klienta buď domény připojený nebo izolované a nespravované, jako jsou tablety nebo smartphony.

Tato variabilita zvýšit rizika při nasazení cloudu. Může být obtížné spravovat, sledování a audit akcí správy. Tato variabilita může také zvýšit ohrožení bezpečnosti prostřednictvím neregulovaného přístupu ke koncovým bodům klientů, které se používají pro správu cloudových služeb. Používání obecných nebo osobních pracovních stanic k vývoji a správě infrastruktury otevírá možnosti útoků z nečekaných směrů, například prohlížení webu (např. útok typu watering hole) nebo e-mailu (např. sociální inženýrství a phishing).

Sledování, protokolování a auditování poskytují základ pro sledování a pochopení aktivit správy. Všechny akce podrobné auditování nemusí být vždy proveditelné kvůli množství dat vygenerována. Ale auditování účinnosti zásad správy je nejvhodnější.

Zásady správného řízení zabezpečení Azure z objektů zásad skupiny Azure Active Directory Domain Services (AD DS) můžete řídit rozhraní Windows pro všechny správce, například sdílení souborů. Zahrňte pracovní stanice pro správu do sledování, protokolování a auditování procesů. Sledujte všechny přístupy a chování správců a vývojářů.

### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) poskytuje centrální zobrazení stavu zabezpečení prostředků v rámci předplatných. Poskytuje doporučení, které pomáhají zabránit ohroženými prostředky. Ho zásady můžete povolit podrobnější – například uplatňovat zásady na určitých skupinách prostředků umožňujících enterprise podle jejich postavení riziku, které jste jejich vyřešení.

![Azure Security Center](./media/governance-in-azure/security-governance-in-azure-fig7.png)

Security Center poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, které byste jinak nevšimli a spolupracuje s řadou řešení zabezpečení. Po povolení [zásady zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-policies) pro prostředky předplatného, Security Center analyzuje zabezpečení vašich prostředků, aby identifikovala potenciální ohrožení zabezpečení. Informace o konfiguraci vaší sítě jsou k dispozici okamžitě.

Azure Security Center představuje kombinaci osvědčených postupů analýzy a zabezpečení správy zásad pro všechny prostředky v rámci předplatného Azure. Umožňuje týmy zabezpečení a rizika osob zabránit, zjistit a reagovat na hrozby zabezpečení tak, jak automaticky shromažďuje a analyzuje data zabezpečení z vašich prostředků Azure, sítě a řešení partnerů, jako jsou antimalwarové programy a brány firewall.

Kromě toho Azure Security Center používá pokročilou analýzu, včetně machine learningu a analýzy chování. Využívá globální analýzou hrozeb z produktů a služeb společnosti Microsoft digitální činů jednotky (přicházejí týmu DCU), Microsoft Security Response Center (MSRC), a externí kanály. Můžete použít [zásad správného řízení zabezpečení](https://www.credera.com/blog/credera-site/azure-governance-part-4-other-tools-in-the-toolbox/) široce na úrovni předplatného. Nebo můžete zúžit dolů specifické požadavky a použít je k jednotlivým prostředkům prostřednictvím definic zásad.

Nakonec Azure Security Center analyzuje stav zabezpečení prostředků na základě těchto zásad a používá tyto informace k zajištění pronikavého řídicí panely a výstrahy pro události, například zjištění malwaru nebo škodlivý připojení IP pokusy. Další informace o tom, jak používat doporučení najdete v tématu [implementace doporučení zabezpečení v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

Azure Security Center monitoruje následující prostředky Azure:

- Virtuální počítače (VM) (včetně cloudové služby)

- Virtuální sítě

- Databáze SQL

- Partnerských řešení integrovaných ve vašem předplatném Azure, jako je například brány firewall webových aplikací na virtuálních počítačích a na [App Service Environment](https://docs.microsoft.com/azure/app-service/app-service-app-service-environments-readme)

Pokud nejprve přístup k Security Center, shromažďování dat je povolené na všechny virtuální počítače ve vašem předplatném. Doporučujeme ponechat shromažďování dat, které jsou povolené, ale můžete [zakázat](https://docs.microsoft.com/azure/security-center/security-center-faq) v zásadách Security Center.

### <a name="log-analytics"></a>Log Analytics

Informace o zabezpečení Azure Log Analytics softwaru vývoj a služby týmu a [zásad správného řízení programu](https://github.com/Microsoft/azure-docs/blob/master/articles/log-analytics/log-analytics-security.md) podporuje požadavky na jeho firmy. Dodržuje zákonů a nařízení, jak je popsáno v [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) a [dodržování předpisů Center důvěřovat Microsoft](https://www.microsoft.com/TrustCenter/Compliance/default.aspx). Jak analýzy protokolů vytváří požadavky na zabezpečení, identifikuje kontrolních mechanismů pro zabezpečení a spravuje a rizika monitorování je také popsáno existuje. Ročně zkontroluje tým zásad, standardů, postupy a pokyny.

Každý člen týmu vývoj analýzy protokolů obdrží formální aplikace bezpečnostního školení. Systém správy verzí pomáhá chránit každý projekt softwaru ve vývoji.

Společnost Microsoft nemá tým zabezpečení a dodržování předpisů, který dohlíží a vyhodnocuje všechny služby ve službě Microsoft. Informace o zabezpečení osob tvoří tým a nejsou přidruženy engineering oddělení, které vyvíjet analýzy protokolů. Zabezpečení osoby mít vlastní řetězec správy. Provádění svých nezávislé posuzování produktů a služeb k zajištění zabezpečení a dodržování předpisů.

Sada služeb, které běží v Azure poskytuje základní funkce služby analýzy protokolů. Každá služba poskytuje specifickou funkci správy a kombinováním služeb je možné dosahovat různých scénářů správy.

![Azure services pro správu](./media/governance-in-azure/security-governance-in-azure-fig9.JPG)

Tyto služby pro správu byly navrženy v cloudu. Budou se zcela hostované v Azure, takže nemáte zahrnují nasazení a správě místních prostředků. Konfigurace je minimální, a může být spuštěná v řádu minut.

Vložit agenta na libovolném počítači Windows nebo Linux ve vašem datovém centru a bude posílat data do analýzy protokolů. Existuje může být analyzována spolu se všechna data shromážděná z cloudu, nebo místní služby. Využívat výhody cloudu pro zálohování a vysoké dostupnosti pro místních prostředků pomocí Azure Backup a Azure Site Recovery.

![Služby správy na řídicím panelu Azure](./media/governance-in-azure/security-governance-in-azure-fig8.png)

Sady Runbook v cloudu nelze obvykle přístup k prostředkům místně, ale můžete nainstalovat agenta na jeden nebo více počítačů, které budou hostiteli sady runbook ve vašem datovém centru. Při spuštění sady runbook, zadejte, zda chcete spustit v cloudu nebo na místní pracovní.

Různá řešení jsou dostupné od Microsoftu a partnerů, můžete přidat do vašeho předplatného Azure k zvýšit hodnotu svou investici do analýzy protokolů. Například Azure nabízí [řešení pro správu](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions)– balené nastaví logiku, které implementují scénář správy pomocí služby pro jeden nebo více.

![Galerie řešení pro správu v Azure](./media/governance-in-azure/security-governance-in-azure-fig10.png)

Jako partner můžete vytvořit vlastní řešení pro podporu aplikací a služeb a poskytněte uživatelům prostřednictvím Azure Marketplace nebo rychlý start šablon.

## <a name="performance-alerting-and-monitoring"></a>Monitorování a výstrahy výkonu

### <a name="alerting"></a>Zobrazení výstrah

Výstrahy jsou metoda monitorování metriky prostředků Azure, události nebo protokoly. Jejich upozornit je, když je splněna podmínka, který jste zadali.

Výstrahy jsou k dispozici v rámci služeb, včetně:

- **Azure Application Insights**: umožňuje webů test a metrika výstrahy. Další informace najdete v tématu [nastavit výstrahy ve službě Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) a [sledování dostupnosti a odezvy žádné webu](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability).

- **Analýza protokolu**: umožňuje směrování aktivity a diagnostických protokolů k analýze protokolů. Umožňuje metrika, log a ostatní typy výstrah. Další informace najdete v tématu [výstrahy v analýzy protokolů](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts).

- **Azure monitorování**: umožňuje výstrahy na základě hodnoty metriky a aktivity protokolu události. Můžete použít [REST API služby Azure monitorování](https://msdn.microsoft.com/library/dn931943.aspx) ke správě výstrah. Další informace najdete v tématu [pomocí portálu Azure, PowerShell nebo rozhraní příkazového řádku k vytvoření výstrahy](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal).

### <a name="monitoring"></a>Monitorování

Problémy s výkonem v cloudové aplikace může ovlivnit vaší firmy. S více vzájemně propojena součástmi a často verzích může dojít, degradations kdykoli. A pokud vyvíjíte aplikace, uživatelé obvykle zjistit problémy, které nebyl nalezen v testování. Měli vědět o tyto problémy okamžitě a mít nástrojů pro diagnostiku a jejich oprava.

Není řadu nástrojů pro monitorování aplikací Azure a služby. Některé z jejich funkcí překrývat. Toto je částečně kvůli stírá mezi vývojovým týmem a operace aplikace.

Zde jsou hlavní nástroje:

- **Azure monitorování** je základní nástroj pro monitorování služby spuštěné v Azure. Nabízí data na úrovni infrastruktury o propustnost služby a okolního prostředí. Pokud spravujete všechny aplikace v Azure a rozhodování o škálování směrem nahoru nebo dolů prostředků, můžete pomocí monitorování Azure spustit.

- **Application Insights** lze použít pro vývoj a jako výrobní řešení monitorování. Funguje díky instalaci balíčku v aplikaci, takže nabízí více interní zobrazení co se děje. Jeho data zahrnují odezvy závislostí, výjimek trasování, ladění, snímky a profilů spuštění. Poskytuje nástroje pro analýzu všech tuto telemetrii do vám pomůže při ladění aplikace a které vám pomohou pochopit, co uživatelé dělají s ním. Můžete zjistit, zda Špička při odezvy je z důvodu něco v aplikaci nebo některé externí resourcing problém. Pokud používáte Visual Studio a aplikace, je při selhání, můžete přejít přímo na problém řádek kódu, můžete ji opravit.

- **Analýza protokolu** je pro uživatele, kteří potřebují k optimalizaci výkonu a plán údržby na aplikace běžící v produkčním prostředí. Shromažďuje a agreguje data z mnoha zdrojů, s zpožděním 10 až 15 minut. Poskytuje komplexní řešení pro správu IT pro Azure, místní a cloudové infrastruktuře jiných výrobců (například Amazon Web Services). Poskytuje nástroje k analýze dat napříč zdrojů, umožňuje komplexní dotazy napříč všechny protokoly a může aktivně upozornit na zadaných podmínek. Můžete dokonce shromáždění vlastních dat v centrálním úložišti a potom budete dotazovat a vizualizovat data.

- **System Center Operations Manager** je pro správu a monitorování instalace velké cloudu. Je již znáte jako nástroj pro správu pro místní systém Windows Server a Hyper-V na základě cloudy, ale můžete také integrovat a spravovat aplikace Azure. Kromě jiných věcí ho můžete nainstalovat na existující živé aplikace Application Insights. Pokud aplikace přestane fungovat, Operations Manager se dozvíte v sekundách.


## <a name="next-steps"></a>Další postup

- [Osvědčené postupy pro vytváření šablon Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices)

- [Příklady implementace zásad správného řízení předplatného Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-subscription-examples)

- [Microsoft Azure Government.](https://docs.microsoft.com/azure/azure-government/)
