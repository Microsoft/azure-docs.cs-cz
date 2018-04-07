---
title: Zásady správného řízení v Azure | Microsoft Docs
description: Další informace o cloudové výpočetní služby, které zahrnují široký výběr výpočetních instancích & služby, které je možné škálovat nahoru či dolů automaticky podle potřeb vaší aplikace nebo enterprise.
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: 04d9e6152c87a49a5f1b1b1a29c16d80de00f4e9
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="governance-in-azure"></a>Zásady správného řízení v Azure

Víme, že je zabezpečení úlohy, jeden v cloudu a jak důležité je, že zjistíte přesné a aktuální informace o zabezpečení Azure. Jedním z nejlepší důvodů, proč používat Azure pro vaše aplikace a služby je využívat jeho širokou škálu zabezpečení nástroje a možnosti. Tyto nástroje a možnosti pomáhat ji možné vytvořit zabezpečený řešení na zabezpečené platformy Azure.

Vám pomůžou lépe pochopit pole zásad správného řízení je zapsán ovládací prvky implementované v rámci Microsoft Azure z odběratele a Microsoft operations perspektivy, v tomto článku "Zásad správného řízení v Azure", k poskytování komplexní pohled na zásad správného řízení Funkce dostupné v Microsoft Azure.

## <a name="azure-platform"></a>Platforma Azure

Azure je platforma služby veřejného cloudu, která podporuje široký výběr operačních systémů, programovací jazyky, rozhraní, nástroje, databází a zařízení. Může probíhat Linux kontejnery s integrací Dockers; vývoj aplikací pomocí jazyka JavaScript, Python, .NET, PHP, Java a Node.js; sestavení back EndY pro iOS, Android a Windows zařízení. Služby veřejného cloudu Azure podporují stejné technologie miliony vývojářů a IT profesionály již spoléhají na a vztah důvěryhodnosti.

Sestavení nebo migrovat aktiva IT, poskytovatele služeb veřejného cloudu se spoléhat na dané organizace dalo k ochraně vašich aplikací a dat se službami a ovládací prvky slouží ke správě zabezpečení vaše cloudové prostředky.

Infrastruktury Azure a k aplikacím pro hostování miliony zákazníků současně slouží ze zařízení a poskytuje trustworthy foundation, na kterém může podnikům splňovat požadavky jejich zabezpečení. Kromě toho Azure poskytuje mnoho možností zabezpečení a umožňuje řídit tak, aby zabezpečení ke splnění jedinečným požadavkům vaší organizace nasazení můžete přizpůsobit.

Tento dokument vám pomůže pochopit, jak možnosti zásad správného řízení Azure vám mohou pomoci splnit tyto požadavky.

## <a name="abstract"></a>Abstraktní

Zásady správného řízení cloudu Microsoft Azure poskytuje integrované auditu a konzultační přístup pro kontrolu a poradenství organizace na jejich využití platformy Azure. Microsoft Azure cloud zásad správného řízení odkazuje na rozhodovacích procesy, kritéria a zásady plánují, architektura, získávání, nasazení, operace a správy cloudu computing.

Chcete-li vytvořit plán pro Microsoft Azure cloud zásad správného řízení, je potřeba provést hlubší pohled na osoby, procesy a technologie aktuálně na místě a následně vytvořit rozhraní, které usnadňují tak konzistentně podporu obchodních potřeb současně nabízí koncovým uživatelům možnost používat výkonné funkce Microsoft Azure.

Tento dokument popisuje, jak můžete dosáhnout zvýšené úrovni zásad správného řízení prostředků vaší IT v Microsoft Azure. Tento dokument vám může pomoct pochopit funkce zabezpečení a zásad správného řízení, které jsou součástí do služby Microsoft Azure.

Tady jsou hlavní zásad správného řízení problémy popsané v tomto dokumentu:

- Implementace zásady, procesy a postupy podle cíle organizace.

- Zabezpečení a průběžné dodržování standardů organizace

- Monitorování a výstrahy

## <a name="implementation-of-policies-processes-and-procedures"></a>Implementace zásady, procesy a postupy 

Správa navázal rolích a zodpovědnostech k dohlížejí implementace zásady zabezpečení informací a provozní kontinuitu v Azure. Správa Microsoft Azure zodpovídá za dohled nad zabezpečením a postupy kontinuity v rámci svých příslušných týmů (včetně třetích stran) a usnadnit dodržování zásad zabezpečení, procesy a standardy.

Zde jsou vyvinuly faktory:

- Zřizování účtů

- Ovládací prvky předplatného

- Řízení přístupu na základě role

- Správa prostředků

- Sledování prostředku

- Ovládací prvek kritické prostředku

- Rozhraní API pro přístup k informacím fakturace

- Ovládací prvky sítě

## <a name="account-provisioning"></a>Zřizování účtů

Definování hierarchie účtu je hlavní krok struktury Azure a používat služby v rámci společnosti a je základní struktura zásad správného řízení. V případě zákazníků s smlouvy enterprise můžete rozdělit zákazníkům další prostředí do oddělení, účty a nakonec odběry.

![Zřizování účtů](./media/governance-in-azure/security-governance-in-azure-fig1.png)

Pokud nemáte smlouvu enterprise agreement, zvažte použití [Azure značky](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) na úrovni předplatného a definovat hierarchie. Předplatné Azure je základní jednotkou, kde jsou všechny prostředky obsažené. Definuje také několik omezení v rámci Azure, například na počtu jader, zdroje atd. Může obsahovat odběry [skupiny prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), která může obsahovat prostředky. [RBAC](https://docs.microsoft.com/azure/api-management/api-management-role-based-access-control) zásady se vztahují na tyto tři úrovně.

Každých enterprise se liší a hierarchii pomocí Azure značky v případě zákazníků mimo organizaci umožní významnou flexibilitu uspořádání Azure v rámci společnosti. Před nasazením prostředky v Microsoft Azure, by měl modelu hierarchie a pochopit vliv na fakturace, přístup k prostředkům a složitost.

## <a name="subscription-controls"></a>Ovládací prvky předplatného

Předplatné Určuje, jak hlášené a účtují využití prostředků. Odběry, může být instalační program pro samostatné fakturace a platby. Jako uvedených starší v rámci jednoho účtu Azure jsme může mít několik odběrů. Odběry slouží k určení využití prostředků Azure více oddělení v rámci podniku.

Například, pokud má společnosti oddělení IT oddělení lidských zdrojů a uvádění na trh a tyto oddělení mají různé projekty systémem. Na základě využití prostředků Azure jako virtuální počítače každé oddělení, se můžou účtovat odpovídajícím způsobem. To jsme můžete řídit finance jednotlivých oddělení.

Předplatná Azure vytvořit tři parametry:

- ID jedinečný odběratele

- fakturace umístění

- Sadu dostupných prostředků

Pro jednotlivce, která bude zahrnovat jeden ID pro účet Microsoft, číslo platební karty a úplná sada služeb Azure – i když Microsoft vynucuje omezení spotřeby, v závislosti na typu předplatného.

Azure registrace hierarchií definovat, jak jsou strukturovaná služby v rámci smlouvy Enterprise. Podnikový portál umožňuje zákazníkům k rozdělení přístupu k Azure prostředky přidružené k smlouvu Enterprise Agreement podle flexibilní hierarchií přizpůsobitelné jedinečný potřebám organizace. Vzor hierarchie by měl odpovídat tak, aby přidružené k fakturaci a prostředku přístup, může být přesně účtována pro správu a geografické struktura organizace.

Tři vzory vysoké úrovně jsou funkční, obchodní jednotky a geografické, pomocí oddělení jako pro správu konstrukce pro účet seskupení. V rámci každé oddělení můžete účty přiřazené odběry, které vytvoření sila pro fakturaci a několik klíčů omezení v Azure (například počet virtuální počítače, účty služby storage atd.).

![Ovládací prvky předplatného](./media/governance-in-azure/security-governance-in-azure-fig2.png)


Pro organizace s smlouvu Enterprise Agreement předplatná Azure, postupujte podle čtyři úrovně hierarchie:

- registrace podnikového správce.

- Správce oddělení

- vlastníka účtu

- Správce služeb

Tato hierarchie řídí následující:

- Vztah fakturace

- Účet správy

- Na základě řízení přístupu role (RBAC) na artefakty

- Hranice nebo omezení

- Hranice

  - Využití a fakturace (sazebník podle čísla nabídka)

  - Omezení

  - Virtual Network

- Připojen 1 aad (1 AAD být přidružen mnoho odběrů)

- Přidružené k účtu podnikové registrace

## <a name="role-based-access-controls"></a>Řízení přístupu na základě rolí

Pokud Azure byla původně, řízení přístupu k odběru byly základní: správce nebo spolusprávce. Přístup k odběru v klasickém modelu implicitní přístup k všechny prostředky v portálu. Kvůli chybějící jemně odstupňovanou kontrolu vedla k rozšiřování odběry zajistit úroveň řízení přiměřené přístupu pro Azure zápisu.

![Řízení přístupu na základě rolí](./media/governance-in-azure/security-governance-in-azure-fig3.png)

Tento, jak narůstá počet odběrů již není potřeba. Pomocí řízení přístupu na základě rolí můžete přiřadit uživatele k standardní role (jako je například běžné "čtení" a "zápis" typy rolí). Můžete také definovat vlastní role.

[Azure na základě rolí řízení přístupu (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) umožňuje vyladění správy přístupu pro Azure. Pomocí řízení přístupu na základě role v Azure můžete uživatelům poskytnout pouze takovou úroveň přístupu, kterou potřebují k provádění svých úloh. Zaměřené na zabezpečení společnosti by měla soustředit na poskytnutí zaměstnanci přesný oprávnění, které potřebují. Příliš mnoho oprávnění vystavit účet, který útočníkům. Příliš málo oprávnění znamená, že zaměstnanci nelze práci efektivně. Azure na základě rolí řízení přístupu (RBAC) pomáhá vyřešit tento problém tak, že nabídka vyladění správy přístupu pro Azure. RBAC vám pomůže povinností v rámci týmu oddělit a poskytnout pouze takovou úroveň přístupu pro uživatele, kteří potřebují k provádění svých úloh. Namísto udělení každý uživatel neomezený oprávnění v vašeho předplatného Azure nebo prostředky, můžete povolit jenom určité akce.

Například použijte funkci RBAC umožníte jednoho zaměstnance spravovat virtuální počítače v předplatném, zatímco jiné můžete spravovat databází SQL v rámci stejného předplatného.

Azure RBAC má tři základní role, které platí pro všechny typy prostředků:

- **Vlastník** má úplný přístup ke všem prostředkům, včetně právo delegovat přístup k ostatním.

- **Přispěvatel** můžete vytvářet a spravovat všechny typy prostředků Azure, ale nelze udělit přístup ostatním uživatelům.

- **Čtečka** můžete zobrazit stávající prostředky Azure.

Zbytek role RBAC v Azure povolit správu konkrétních prostředků Azure. Například role Přispěvatel virtuálních počítačů umožňuje uživatelům vytvářet a spravovat virtuální počítače. Nedává je přístup k virtuální síť nebo podsíť, které se virtuální počítač připojí k.

[Předdefinované role RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) uvádí role, které jsou k dispozici v Azure. Určuje operace a rozsah, který každé předdefinované role uděluje uživatelům.

Udělit přístup přiřazením příslušné role RBAC uživatelům, skupinám a aplikace v určité oboru. Předplatné, skupinu prostředků nebo jediný zdroj, může být oboru přiřazení role. Role přiřazené v nadřazeném oboru podřízené objekty jsou v něm obsažena také uděluje přístup.

Například uživatel s přístupem do skupiny prostředků můžete spravovat všechny prostředky, které obsahuje, jako jsou weby, virtuální počítače a podsítě.

Azure RBAC podporuje pouze operace správy prostředků Azure v portálu Azure a rozhraní API Správce Azure Resource Manager. Všechny operace úrovně dat pro prostředky Azure se nejde autorizovat. Například může autorizovat někdo chcete spravovat účty pro úložiště, ale není pro objekty BLOB nebo tabulky v rámci účtu úložiště nemůžete. Podobně databáze SQL je možné spravovat, ale nikoli tabulky v něm.

Další informace o tom, jak vám řízení přístupu na základě role v Azure pomůže spravovat přístup uživatelů najdete v článku [Co je řízení přístupu na základě role](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is).

Můžete také [vytvořit vlastní roli](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) v řízení řízení přístupu (RBAC), pokud žádná z předdefinovaných rolí splňují konkrétní přístup potřebuje. Můžete vytvořit vlastní role pomocí [prostředí Azure PowerShell](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell), [rozhraní příkazového řádku Azure (CLI)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-azure-cli)a [REST API](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-rest). Stejně jako předdefinovaných rolí vlastní role jde přiřadit uživatelům, skupinám a aplikacím na předplatné, skupinu prostředků a prostředků obory.

V rámci každého předplatného můžete udělit až 2000 přiřazení rolí.

## <a name="resource-management"></a>Správa prostředků

Azure původně zadat pouze v modelu nasazení classic. V tomto modelu všechny prostředky existovaly nezávisle; došlo k dispozici žádný způsob, jak seskupit související prostředky. Místo toho musíte ručně sledovat prostředky, ke kterým skládá řešení nebo aplikace a nezapomeňte spravovat je v koordinovaný přístup.

Nasazení řešení, jste měli k vytvoření každého prostředku jednotlivě prostřednictvím portálu Azure, nebo vytvořit skript, který nasadit všechny prostředky ve správném pořadí. Pokud chcete odstranit řešení, jste museli odstranit jednotlivé prostředky. Není snadno můžete použít a aktualizovat zásady řízení přístupu pro související prostředky. Nakonec nelze aplikovat značek k prostředkům a označte je podmínky, které vám pomůžou sledovat vaše prostředky a spravovat fakturace.

V roce 2014 si uvedla Azure Resource Manager, která přidá koncept skupinu prostředků. Skupina prostředků je kontejner pro prostředky, které sdílejí společné životního cyklu. Model nasazení Resource Manager poskytuje několik výhod:

- Můžete nasadit, spravovat a monitorovat všechny služby pro vaše řešení jako se skupinou, nikoli zpracování těchto služeb jednotlivě.

- Můžete opakovaně nasadit řešení v průběhu životního cyklu a mít přitom jistotu, že vaše prostředky jsou nasazeny v konzistentním stavu.

- Řízení přístupu můžete použít na všechny prostředky ve vaší skupině prostředků a tyto zásady budou automaticky použita při přidávání nových prostředků do skupiny prostředků.

- Můžete označit prostředky pomocí značek a logicky tak uspořádat všechny prostředky ve svém předplatném.

- JavaScript Object Notation (JSON) můžete použít k definování infrastrukturu pro vaše řešení. Soubor JSON se označuje jako šablony Resource Manageru.

- Můžete definovat závislosti mezi prostředky, takže se nasadí ve správném pořadí.

![Správa prostředků](./media/governance-in-azure/security-governance-in-azure-fig4.png)

Resource Manager umožňuje umístit prostředky do smysluplný skupin pro správu, fakturace nebo přírodní spřažení. Jak už bylo zmíněno dříve, Azure má dva modely nasazení. V dříve [modelu Classic](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model), základní jednotkou správy byl odběr. Bylo obtížné rozdělení prostředků v rámci předplatného, která vedla k vytvoření velkého počtu předplatných. Pomocí modelu Resource Manager jsme viděli zavedení skupiny prostředků.

Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. [Skupina prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) může zahrnovat všechny prostředky pro řešení, nebo jenom prostředky, které chcete spravovat jako skupinu. Na základě toho, co je pro vaši organizaci nejvhodnější, rozhodnete, jakým způsobem se mají prostředky přidělovat do skupin prostředků.

Další doporučení k šablonám najdete v tématu [Osvědčené postupy pro vytváření šablon Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices).

Azure Resource Manager analyzuje závislosti a zajistí, že se prostředky vytvoří ve správném pořadí. Pokud jeden prostředek závisí na hodnotě z jiného prostředku (například virtuální počítač potřebuje účet úložiště pro disky), nastavíte závislost.

>[!Note]
>Další informace najdete v tématu [Definování závislostí v šablonách Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies).

Šablony můžete také využít pro aktualizace infrastruktury. Můžete například ke svému řešení přidat prostředek a konfigurační pravidla pro prostředky, které jsou už nasazené. Pokud šablona specifikuje vytvoření nového prostředku, ale tento prostředek již existuje, Azure Resource Manager místo vytvoření nového assetu provede jeho aktualizaci. Azure Resource Manager aktualizuje stávající asset do stejného stavu, jako kdyby byl nový.

Resource Manager poskytuje rozšíření pro scénáře, pokud potřebujete další operace, například při instalaci softwaru, který není součástí instalace.

## <a name="resource-tracking"></a>Sledování prostředku

Jak uživatelé ve vaší organizaci přidat prostředky do odběru, stane se stále důležité prostředky přidružit příslušné oddělení, zákazníků a prostředí. Metadata můžete připojit k prostředkům prostřednictvím značky. Používáte [značky](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) k zadání informací o prostředku nebo vlastník. Značky umožňují nejen agregovat a skupiny prostředků několika způsoby, ale tato data použít pro účely vracení peněz.

Značky použijte v případě, že máte komplexní kolekci prostředků a skupin prostředků a potřebujete tyto assety vizualizovat co nejsmysluplnějším způsobem. Můžete například označit prostředky, které mají v rámci organizace podobnou roli nebo které patří do stejného oddělení.

Uživatelé ve vaší organizaci mohou vytvořit různé prostředky, které se bez použití značek budou později těžko identifikovat a spravovat. Například můžete chtít odstranit všechny prostředky pro projekt. Pokud tyto prostředky nejsou označeny pro projekt, musíte je ručně vyhledat. Označení může také hrát důležitou roli při omezení zbytečných nákladů ve vašem předplatném.

Prostředky se stejnou značkou nemusí být umístěné ve stejné skupině prostředků. Můžete vytvořit vlastní taxonomii značek a zajistit tak, že všichni uživatelé ve vaší organizaci budou využívat společné značky a nebudou neúmyslně zavádět vlastní (třeba odd. místo oddělení).

Zásady prostředků umožňují vytvořit standardní pravidla pro vaši organizaci. Můžete vytvořit zásady, které zajišťují, že prostředky jsou označené odpovídající hodnoty.

> [!Note]
> Další informace najdete v tématu [fakturace značky zásad initiative](../azure-policy/scripts/billing-tags-policy-init.md).

Označené prostředky můžete také zobrazit přes Azure Portal.

[Sestava využití](https://docs.microsoft.com/azure/billing/billing-understand-your-bill) pro vaše předplatné obsahuje názvy a hodnoty značek, což umožňuje rozdělit náklady podle značek.

> [!Note]
> Další informace o značkách najdete v tématu [fakturace značky zásad initiative](../azure-policy/scripts/billing-tags-policy-init.md).

Na značky se vztahují následující omezení:

- Každý prostředek nebo skupina prostředků může mít maximálně 15 páry klíč – hodnota značky. Toto omezení se vztahuje pouze na základě značek použité přímo do skupiny prostředků nebo prostředek. Skupiny prostředků může obsahovat mnoho prostředků nichž každá má 15 páry klíč – hodnota značky.

- Název značky je omezen na 512 znaků.

- Hodnota značky je omezena na 256 znaků.

- Prostředky ve skupině prostředků nedědí značky použité na danou skupinu prostředků.

Pokud k prostředku potřebujete přidružit více než 15 hodnot, použijte jako hodnotu značky řetězec JSON. Řetězce formátu JSON může obsahovat mnoho hodnot, které se použijí pro klíč jedinou značku.

### <a name="tags-and-billing"></a>Značky a fakturace

Značky umožňují skupiny fakturační údaje. Například pokud používáte víc virtuálních počítačů pro jiné organizace, použití značek k použití skupiny podle nákladové středisko. Značky lze použít také ke kategorizaci náklady modulu runtime prostředí; například fakturace využití pro virtuální počítače spuštěné v provozním prostředí.

Můžete načíst informace o značkách prostřednictvím [využití prostředků Azure a rozhraní API RateCard](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview) nebo využití soubor hodnot oddělených čárkami (CSV). Stáhnout soubor využití z [portál účtů Azure](https://account.windowsazure.com/) nebo [EA portál](https://ea.azure.com/).

>[!Note]
> Další informace o programový přístup k fakturační informace najdete v tématu [proniknout do vaší spotřeby prostředků Microsoft Azure](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview). Operace REST API, najdete v části [referenční dokumentace rozhraní API Azure fakturace REST](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Při stahování použití sdíleného svazku clusteru pro služby, které podporují značky s fakturace, značky se zobrazí ve sloupci značky.

## <a name="critical-resource-controls"></a>Ovládací prvky kritické prostředků

Jak vaše organizace přidá základní služby pro předplatné, bude stále důležité zajistit, že tyto služby jsou k dispozici chcete zabránit přerušení obchodní. [Uzamčení prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) vám umožní omezit operací s prostředky vysoké hodnoty, jejichž změně nebo odstranění by mohlo mít významný dopad na vaší aplikace nebo cloudové infrastruktury. Zámky můžete použít pro předplatné, skupinu prostředků nebo prostředek. Zámky obvykle použijete pro základní prostředkům, například virtuální sítě, bran a účty úložiště.

Uzamčení prostředků v současné době podporují dvou hodnot: CanNotDelete a jen pro čtení. CanNotDelete znamená, že uživatelé (s odpovídající práva) můžete stále číst nebo upravit prostředek, ale nelze ho proto odstranit. Jen pro čtení znamená, že oprávnění uživatelé nejde odstranit ani změnit prostředku.

Zámky správce prostředků se vztahují pouze na operace, které dojít v rovině řízení, sestávající operací posílá <https://management.azure.com>. Zámky neomezují jak prostředky provádět vlastní funkce. Změny prostředku jsou s omezeným přístupem, ale operace prostředků nejsou s omezeným přístupem. Například jen pro čtení zámku v databázi SQL zabráníte odstranění nebo úprava databázi, ale nezabrání můžete z vytvoření, aktualizace a odstraňování dat v databázi.

Použití **jen pro čtení** může vést k neočekávaným výsledkům, protože některé operace, které vypadají podobně jako pro čtení, operace vyžadují další akce. Například umístění **jen pro čtení** výpis klíčů všem uživatelům zabrání zámku na účet úložiště. Seznam klíčů operaci je zpracováván prostřednictvím požadavek POST, protože vrácený klíče jsou k dispozici pro operace zápisu.

![Ovládací prvky kritické prostředků](./media/governance-in-azure/security-governance-in-azure-fig5.png)

Další příklad uvedení zámek jen pro čtení na prostředek aplikace služby zabrání Průzkumníka serveru Visual Studia zobrazení souborů pro daný prostředek, protože interakce vyžaduje oprávnění k zápisu.

Na rozdíl od řízení přístupu na základě rolí použít zámky správy pro aplikaci omezení ve všech uživatelů a rolí. Další informace o nastavení oprávnění pro uživatele a rolí najdete v tématu [řízení přístupu na základě Role v Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

Když použijete zámku v nadřazeném oboru, zdědí všechny prostředky v rámci tohoto oboru stejné zámek. I prostředky, které přidáte později zámek dědí z nadřazeného objektu. Nejvíc omezující zámek v dědičnosti přednost.

Vytvořit nebo odstranit zámky správy, musíte mít přístup k Microsoft.Authorization/ _nebo Microsoft.Authorization/locks/_ akce. Z předdefinovaných rolí pouze **vlastníka** a **správce přístupu uživatelů** mají tyto akce.

## <a name="api-access-to-billing-information"></a>Rozhraní API pro přístup k fakturační informace

Používáte rozhraní API Správce Azure fakturace k získání dat využití a prostředků do vašeho nástrojů pro analýzu dat upřednostňované. Rozhraní API RateCard a využití prostředků Azure vám může pomoct přesně předpovědět a náklady na správu. Rozhraní API jsou implementovány jako poskytovatel prostředků a součástí řady rozhraní API vystavené pomocí Správce prostředků Azure.

### <a name="azure-resource-usage-api-preview"></a>Rozhraní API (Preview) pro využití prostředků Azure.

Použití Azure [API využití prostředků](https://msdn.microsoft.com/library/azure/mt219003) načíst vaše data Odhadované využití platformy Azure. Rozhraní API obsahuje:

- **Řízení přístupu Azure na základě rolí** -konfigurovat zásady přístupu na [portál Azure](https://portal.azure.com/) nebo pomocí [rutin prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) k určení, které uživatele nebo aplikace můžete získat přístup k data o využití předplatného. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volající do čtečky fakturace, čtečky, vlastníka nebo přispěvatele role získat přístup k dat o využití pro konkrétní předplatné Azure.

- **Hodinové nebo denní agregace** – volající můžete určit, jestli chtějí jejich Azure využití dat každou hodinu intervalů nebo denních intervalů. Výchozí hodnota je denně.

- **Instance metadat (zahrnuje značky prostředku)** – získání podrobností na úrovni instance jako identifikátor uri prostředku plně kvalifikovaný (/subscriptions/ {id předplatného} /..), informace o skupině prostředků a značky prostředku. Tato metadata vám deterministicky a prostřednictvím kódu programu přidělit využití podle značky, pomůže pro případy použití mezi poplatků.

- **Metadata prostředků** – prostředek podrobnosti například měření název, kategorie měření, měřicí dílčí kategorie, jednotky a oblast poskytnout volající lépe porozumět tomu, co se spotřebovala. Pracujeme také zarovnat prostředků metadata terminologie přes portál Azure, Azure použití sdíleného svazku clusteru, EA fakturace CSV a dalších činnostech veřejné umožňují vazbu mezi data v prostředí.

- **Použití všech nabízet typy** – data o využití je k dispozici pro všechny typy jako průběžné platby, MSDN, peněžních závazků, peněžního kreditu, který a EA nabízejí.

**Prostředek Azure API RateCard (Preview)**

Použijte rozhraní API RateCard prostředků Azure k získání seznamu dostupných prostředků Azure a odhadovanou informace o cenách pro jednotlivé. Rozhraní API obsahuje:

- **Řízení přístupu Azure na základě rolí** – konfigurace zásad přístupu na portálu Azure nebo pomocí rutin prostředí Azure PowerShell k určení uživatelů, kteří nebo aplikace mohou získat přístup k datům RateCard. Volající musí používat standardní tokeny služby Azure Active Directory pro ověřování. Přidání volající do čtečky, vlastníka nebo přispěvatele role získat přístup k dat o využití pro konkrétní předplatné Azure.

- **Podpora pro průběžné platby, MSDN, peněžních závazků a peněžního kreditu, který nabízí (EA není podporován)** – tato rozhraní API poskytuje Azure míra nabídka úroveň informace. Volající toto rozhraní API musí projít informace nabídka získat podrobnosti zdroje a sazby. Nemohli jsme aktuálně zajistit EA sazby, protože nabízí EA přizpůsobili sazby za registraci. Tady jsou některé scénáře, které lze vytvořit pomocí kombinace využití a rozhraní API RateCard:

- **Azure tráví v měsíci** – pomocí kombinace využití serveru a rozhraní API RateCard získat lepší přehled o vašem cloudu tráví v měsíci. Můžete analyzovat kbelíků hodinové a denní využití a poplatků odhad.

- **Nastavení výstrah** – použijte využití a rozhraní API RateCard získat odhadovaný cloudu využívání a poplatky, a nastavte založené na prostředcích nebo peněžní na základě výstrahy.

- **Předpovídat faktury** – Get odhadované spotřeby a cloud tráví a použít algoritmy strojového učení k předvídání co kusovníku bude na konci fakturačního cyklu.

- **Předběžné spotřeba analýza nákladů** – použít rozhraní API RateCard odhadnout, kolik vaše faktura by byl očekávané využití při přesunutí úlohy do Azure. Pokud máte existující úlohy v ostatních cloudů nebo privátní cloudy, můžete namapovat vaše použití s Azure tráví sazby získat lepší odhad Azure. Tento odhad vám dává možnost otáčení na nabídku a porovnání mezi typy různých nabídka nad rámec průběžné platby, jako peněžních závazků a peněžního kreditu. Rozhraní API také vám umožní zobrazit náklady rozdíly podle oblasti a umožňuje provádět analýzy citlivostních náklady který vám pomůže provádět rozhodnutí o nasazení.

- **Analýz** -můžete určit, zda je cenově výhodnější spuštění úloh v jiné oblasti nebo na jinou konfiguraci prostředků Azure. Náklady na prostředků Azure se můžou lišit podle na oblast Azure, kterou používáte.

- Můžete také určit, pokud jiný typ nabídky Azure poskytuje lepší rychlost na prostředek služby Azure.

## <a name="networking-controls"></a>Ovládací prvky sítě

Přístup k prostředkům může být vnitřní (v rámci podnikové sítě) nebo externí (prostřednictvím Internetu). Je snadné pro uživatele ve vaší organizaci nechtěně uvést prostředky do nesprávného místě a potenciálně škodlivým přístupem otevírat. Stejně jako u místní / zařízení, musíte přidat podniky příslušných ovládacích prvků k zajištění, že Azure uživatelé provádět správné rozhodnutí, která.

![Ovládací prvky sítě](./media/governance-in-azure/security-governance-in-azure-fig6.png)

Pro řízení předplatné jsme identifikovat core prostředky, které poskytují základní řízení přístupu. Prostředky jader skládá z:

### <a name="network-connectivity"></a>Připojení k síti

[Virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) jsou objekty kontejneru pro podsítě. Když je to nezbytně nutné, často se používá při připojování aplikace k interním firemním prostředkům. Služba Azure Virtual Network umožňuje bezpečně se vzájemně připojovat prostředky Azure s virtuálními sítěmi (virtuální sítě).

Virtuální síť je reprezentace vlastní sítě v cloudu. Virtuální síť je to logická izolace cloudu Azure vyhrazeného pro vaše předplatné. Virtuální sítě můžete také připojit k místní síti.

Následují možnosti pro virtuální sítě Azure:

- **Izolace**: virtuální sítě jsou izolované od sebe navzájem. Můžete vytvořit samostatné virtuálních sítí patřících pro vývoj, testování a produkci použít stejné bloky adres CIDR. Naopak můžete vytvořit více virtuálních sítí, použít jiné bloky adres CIDR a společně připojení sítě. Virtuální síť můžete rozdělit do několika podsítí. Azure poskytuje interní překlad adres pro virtuální počítače a cloudové služby instance rolí, které jsou připojené k virtuální síti. Volitelně můžete nakonfigurovat virtuální síť použít vlastní servery DNS, místo použití Azure interní překlad adres.

- **Připojení k Internetu**: cloudových služeb pro všechny virtuální počítače Azure (VM) a instancí rolí, které jsou připojené k virtuální síti mají přístup k Internetu, ve výchozím nastavení. Podle potřeby můžete také povolit příchozí přístup ke konkrétním prostředkům.

- **Připojení prostředků Azure**: prostředky Azure, jako je například cloudových služeb a virtuálních počítačů může být připojen k stejnou virtuální síť. Prostředky můžete připojit k sobě navzájem pomocí privátních IP adres, i když jsou v různých podsítích. Azure poskytuje výchozí směrování mezi podsítěmi, virtuálními sítěmi a místními sítěmi, takže není nutné konfigurovat a spravovat trasy.

- **Připojení k virtuální síti**: virtuální sítě může být připojena k sobě navzájem, povolení prostředky připojenými k žádné virtuální sítě pro komunikaci s jakýmikoli prostředky na ostatní virtuální sítě.

- **Místní připojení**: virtuální sítě může být připojen k místní sítě prostřednictvím privátní sítě připojení mezi vaší sítí a Azure, nebo připojení site-to-site VPN přes Internet.

- **Filtrování provozu**: virtuálního počítače a cloudové služby role instance síťového provozu je možné filtrovat příchozí a odchozí zdrojové IP adresy a portu, cílové IP adresy a portu a protokolu.

- **Směrování**: Volitelně můžete přepsat výchozí Azure směrování konfiguraci vlastních tras, nebo pomocí trasy protokolu BGP prostřednictvím brány sítě.

## <a name="network-access-controls"></a>Ovládací prvky pro přístup k síti

[Skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) jsou například Brána firewall a poskytují pravidla pro jak prostředku může "kontaktovat" přes síť. Poskytují podrobnou kontrolu nad jak / pokud podsíť (nebo virtuální počítač) lze připojit k Internetu nebo jiných podsítí ve stejné virtuální síti.

Skupina zabezpečení sítě (NSG) obsahuje seznam pravidel zabezpečení, která prostředkům připojeným k virtuálním sítím Azure povolují nebo odpírají síťový provoz. Skupiny zabezpečení sítě můžou být přidružené k podsítím, jednotlivým virtuálním počítačům (klasický model) nebo jednotlivým síťovým rozhraním (síťovým kartám) připojeným k virtuálním počítačům (Resource Manager).

Pokud je skupina zabezpečení sítě přidružená k podsíti, pravidla se vztahují na všechny prostředky, které jsou připojené k příslušné podsíti. Provoz se dá dále omezit přidružením skupiny zabezpečení sítě k virtuálnímu počítači nebo síťové kartě.

## <a name="security-and-continuous-compliance-with-organizational-standards"></a>Zabezpečení a průběžné dodržování standardů organizace

Každý firmy má různé požadavky a každou obchodní bude vytěžit maximum odlišné výhody z cloudové řešení. Zákazníci nejrůznějších ještě stejné základní pochybnostmi přechodu do cloudu. Mohou chtít zachovat kontrolu nad svoje data a chtějí mít dat, které budou zachovány zabezpečení a privátní, všechny při zachování průhlednost a dodržování předpisů.

Zákazníci chcete od poskytovatelů cloudu je:

- **Zabezpečení našich dat** při to v úvahu, že může poskytovat cloudu vyšší zabezpečení dat a administrativní řízení, IT žebříčky jsou stále problémem, migraci do cloudu ponechá je zranitelnější vůči hackery než jeho aktuální interní řešení.

- **Ponechat naše data** soukromé cloudové služby vyvolat výzvy jedinečný ochrany osobních údajů pro firmy. Jak společností, vypadat do cloudu uložte na náklady na infrastrukturu a zvýšit jejich flexibilitě, budou také obávat ztráty řízení toho, kde mají uložená data, který je k ní přistupují a jak získá používají.

- **Sdělte nám řízení** to i v případě jejich využít výhod cloudu nasadit další inovativní řešení, jsou velmi zajímá neztratili kontrolu nad svá data společnosti. Nejnovější prohlášení o přístup k datům zákazníka prostřednictvím znamená, právních i velmi právní, organizace státní správy zkontrolujte některé ředitelé informačních technologií opatrní ukládání svých dat v cloudu.

- **Zvýšit úroveň průhlednosti** zabezpečení, ochrany osobních údajů a řízení jsou důležité pro obchodní rozhodnutí ve firmě, chtějí nezávisle ověřit jak jejich data ukládají, přístupu a zabezpečené.

- **Udržovat kompatibilitu** jako společností rozšířit jejich používání cloudových technologií, složitost a obor standardů a nařízení pokračovat ve vývoji. Společnosti potřebují vědět, že budou splněny jejich standardů dodržování předpisů a že dodržování předpisů bude momentální jako předpisy změn v průběhu času.

## <a name="security-configuration-monitoring-and-alerting"></a>Konfigurace zabezpečení, monitorování a výstrahy

Předplatitelé služby Azure mohou svoje cloudová prostředí spravovat z více zařízení. Můžou k tomu využívat pracovní stanice, počítače vývojářů a dokonce i privilegovaná zařízení koncových uživatelů, která mají oprávnění ke konkrétním úlohám. V některých případech se funkce správy provádějí prostřednictvím webových konzol, například na portálu Azure. V ostatních případech můžou existovat přímá připojení k Azure z místních systémů prostřednictvím virtuálních privátních sítí (VPN), terminálových služeb, protokolů klientských aplikací nebo (v kódu) rozhraní API pro správu služby Azure (SMAPI). Kromě toho můžou být koncové body klienta buď připojené k doménám nebo izolované a nespravované, jako například tablety nebo smartphony.

Přestože funkce správy a více přístupů nabízejí bohatou sadu možností, může tato variabilita výrazně zvýšit rizika při nasazení cloudu. Může být obtížné spravovat, sledovat a auditovat akce správy. Tato variabilita může také zvýšit ohrožení bezpečnosti prostřednictvím neregulovaného přístupu ke koncovým bodům klientů, které se používají pro správu cloudových služeb. Používání obecných nebo osobních pracovních stanic k vývoji a správě infrastruktury otevírá možnosti útoků z nečekaných směrů, například prohlížení webu (např. útok typu watering hole) nebo e-mailu (např. sociální inženýrství a phishing).

Sledování, protokolování a auditování poskytují základ pro sledování a pochopení aktivit správy. Podrobné auditování všech akcí nemusí být vždy proveditelné vzhledem k množství dat, která jsou při tom vytvářena. Přesto za osvědčený postup považujeme auditování účinnosti zásad správy.

Zabezpečení Azure zásad správného řízení z GPO v AD DS k řízení všech správce Windows rozhraní, například sdílení souborů. Zahrňte pracovní stanice pro správu do procesů auditování, sledování a protokolování. Sledujte všechny přístupy a chování správců a vývojářů.

### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) poskytuje centrální zobrazení stavu zabezpečení prostředků ve předplatných a poskytuje doporučení, které pomáhají zabránit ohroženými prostředky. Podrobnější zásady (například používá zásady skupiny konkrétní prostředků, které povolí enterprise podle jejich postavení riziku, které budou adresování) ji můžete povolit.

![Azure Security Center](./media/governance-in-azure/security-governance-in-azure-fig7.png)

Security Center poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, které byste jinak nevšimli a spolupracuje s řadou řešení zabezpečení. Po povolení [zásady zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-policies) pro prostředky předplatného, Security Center analyzuje zabezpečení vašich prostředků, aby identifikovala potenciální ohrožení zabezpečení. Informace o konfiguraci vaší sítě jsou k dispozici okamžitě.

Azure Security Center představuje kombinaci osvědčených postupů analýzy a zabezpečení správy zásad pro všechny prostředky v rámci předplatného Azure. Tento efektivní a snadno použitelný nástroj umožňuje týmy zabezpečení a rizika osob zabránit, zjistit a reagovat na hrozby zabezpečení tak, jak automaticky shromažďuje a analyzuje data zabezpečení z vašich prostředků Azure, sítě a řešení partnerů, jako jsou antimalwarové programy a brány firewall.

Kromě toho Azure Security Center používá pokročilou analýzu, včetně machine learningu a analýzy chování při využívání globální analýzou hrozeb z produktů společnosti Microsoft a služby, Microsoft digitální činů jednotky (přicházejí týmu DCU), Microsoft Security Response Center (MSRC) a externích informačních kanálů. [Zásady správného řízení zabezpečení](https://www.credera.com/blog/credera-site/azure-governance-part-4-other-tools-in-the-toolbox/) můžete použít široce na úrovni předplatného nebo co nejlépe určen konkrétní, podrobné požadavky u jednotlivých prostředků prostřednictvím definic zásad.

Nakonec Azure Security Center analyzuje stav zabezpečení prostředků na základě těchto zásad a to se používá k poskytnutí pronikavého řídicí panely a výstrahy pro události, například zjištění malwaru nebo škodlivý připojení IP pokusy.

>[!Note]
> Další informace o tom, jak používat doporučení, najdete v článku [Implementace doporučení zabezpečení v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

Security Center shromažďuje data z virtuálních počítačů k vyhodnocení jejich stavu zabezpečení, poskytování doporučení zabezpečení a výstrahy na hrozeb. Pokud nejprve přístup k Security Center, shromažďování dat je povolené na všechny virtuální počítače ve vašem předplatném. Doporučuje se shromažďování dat ale můžete můžete odhlásit pomocí [zakázání shromažďování dat](https://docs.microsoft.com/azure/security-center/security-center-faq) v zásadách Security Center.

Nakonec Azure Security Center je otevřená platforma umožňující vytvoření software, který se připojuje k Azure Security Center ke zvýšení jeho funkce pro partnery společnosti Microsoft a nezávislí dodavatelé softwaru.

Azure Security Center monitoruje následující prostředky Azure:

- Virtuální počítače (VM) (včetně cloudové služby)

- Virtuální sítě Azure

- Služba Azure SQL

- Partnerských řešení integrovaných ve vašem předplatném Azure například brány firewall webových aplikací na virtuálních počítačích a na [App Service Environment](https://docs.microsoft.com/azure/app-service/app-service-app-service-environments-readme).

### <a name="log-analytics"></a>Log Analytics

Zabezpečení informací analýzy protokolů softwaru vývoj a služby týmu a [zásad správného řízení programu](https://github.com/Microsoft/azure-docs/blob/master/articles/log-analytics/log-analytics-security.md) podporuje požadavky na jeho firmy a dodržuje zákonů a nařízení, jak je popsáno v [důvěřovat Microsoft Azure Center](https://azure.microsoft.com/support/trust-center/) a [dodržování předpisů Centrum zabezpečení Microsoft](https://www.microsoft.com/TrustCenter/Compliance/default.aspx). Jak zřídit požadavky na zabezpečení analýzy protokolů, identifikuje ovládací prvky zabezpečení, spravuje a monitoruje rizika jsou také popsány existuje. Ročně, jsme zkontrolujte zásady, standardy, postupy a pokyny.

Každý člen týmu vývoj analýzy protokolů obdrží formální aplikace bezpečnostního školení. Interně používáme systém správy verzí pro vývoj softwaru. Každý projekt softwaru je chráněn systém správy verzí.

Společnost Microsoft nemá tým zabezpečení a dodržování předpisů, který dohlíží a vyhodnocuje všechny služby ve službě Microsoft. Informace o zabezpečení osob tvoří tým a nejsou přidruženy engineering oddělení, které vyvíjet analýzy protokolů. Zabezpečení osoby mít vlastní řetězec správy a provedení nezávislé posuzování produktů a služeb k zajištění zabezpečení a dodržování předpisů.

Azure poskytuje kolekci služeb pro správu, které byly navrženy v cloudu od začátku. Namísto nasazení a správa na místní prostředky, jsou tyto součásti zcela hostované v Azure. Konfigurace je minimální a během několika minut můžete začít pracovat.

![Sada Operations Manager](./media/governance-in-azure/security-governance-in-azure-fig8.png)

Právě, protože služby běží v cloudu analýzy protokolů neznamená, že to není možné efektivně spravujte v místním prostředí.

Uvedené agenta na všechny Windows nebo počítač se systémem Linux v datovém centru a bude posílat data do analýzy protokolů, které mohou být analyzovány spolu se všemi ostatními daty, které jsou shromážděny z cloudu nebo na místní služby. Využít cloudu pro zálohování a vysokou dostupnost pro místní prostředky pomocí Azure Backup a Azure Site Recovery.

Runbooky v cloudu nemohou běžně přistupovat k místním prostředkům, ale můžete nainstalovat agenta na jeden nebo více počítačů, které budou hostovat runbooky v datovém centru. Když spustíte runbook, stačí určit, jestli ho chcete spustit v cloudu, nebo na místním pracovním procesu.

Sada služeb, které běží v Azure poskytuje základní funkce služby analýzy protokolů. Každá služba poskytuje specifickou funkci správy a kombinováním služeb je možné dosahovat různých scénářů správy.

![Sada Operations Manager](./media/governance-in-azure/security-governance-in-azure-fig9.JPG)

Operace systému Azure manager rozšiřuje jeho funkce tím, že poskytuje řešení pro správu. [Řešení pro správu](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions) jsou hotových sady logiky, které implementují scénáři správy využívá jednu nebo více služeb správy.

![Spravovat operace systému Azure](./media/governance-in-azure/security-governance-in-azure-fig10.png)

Různá řešení jsou dostupné od Microsoftu a partnerů můžete snadno přidat do vašeho předplatného Azure k zvýšit hodnotu svou investici do analýzy protokolů.

Jako partner můžete vytvořit vlastní řešení pro podporu aplikací a služeb a poskytněte uživatelům prostřednictvím Azure Marketplace nebo šablony rychlý Start.

## <a name="performance-alerting-and-monitoring"></a>Monitorování a výstrahy výkonu

### <a name="alerting"></a>Zobrazení výstrah

Metoda monitorování metriky prostředků Azure, události nebo protokoly se výstrahy a oznámení při zadávání podmínku splníte.

**Výstrahy v různých služeb Azure**

Výstrahy jsou k dispozici mezi různé služby, včetně:

- Application Insights: Umožňuje test webu a metriky výstrahy.

>[!Note]
> V tématu [nastavit výstrahy ve službě Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) a [sledování dostupnosti a odezvy žádné webu](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability).

- Analýzy protokolů: Umožňuje směrování aktivity a diagnostických protokolů k analýze protokolů a povoluje metrika, log a ostatní typy výstrah.

>[!Note]
> Další informace najdete v tématu výstrahy v [analýzy protokolů](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts).

- Azure monitorování: Umožňuje výstrahy na základě hodnoty metriky a aktivity protokolu události. Můžete použít [REST API služby Azure monitorování](https://msdn.microsoft.com/library/dn931943.aspx) ke správě výstrah.

>[!Note]
> Další informace najdete v tématu [pomocí portálu Azure, PowerShell nebo rozhraní příkazového řádku k vytvoření výstrahy](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal).

### <a name="monitoring"></a>Monitorování

Problémy s výkonem v cloudové aplikace může mít vliv na vaši firmu. S více vzájemně propojena součástmi a často verzích může dojít, degradations kdykoli. A pokud vyvíjíte aplikace, uživatelé obvykle zjistit problémy, které nebyl nalezen v testování. Měli vědět o tyto problémy okamžitě a mít nástroje pro diagnostiku a řešení problémů. Microsoft Azure obsahuje řadu nástrojů pro identifikaci těchto problémů.

**Jak se monitorování Moje Azure cloudových aplikací?**

Není řadu nástrojů pro monitorování aplikací Azure a služby. Některé z jejich funkcí překrývat. Toto je částečně historických důvodů a částečně kvůli stírá mezi vývojovým týmem a operace aplikace.

Zde jsou hlavní nástroje:

- **Azure monitorování** je základní nástroj pro monitorování služby spuštěné v Azure. Nabízí data na úrovni infrastruktury o propustnost služby a okolního prostředí. Pokud spravujete své aplikace v Azure, rozhodování o škálování směrem nahoru nebo dolů prostředky, pak monitorování Azure vám dává používáte ke spuštění.

- **Application Insights** lze použít pro vývoj a jako výrobní řešení monitorování. Funguje tak, že instalaci balíčku do vaší aplikace a proto nabízí více interní zobrazení co se děje. Jeho data zahrnují odezvy závislostí, výjimek trasování, ladění snímky, profily spuštění. Poskytuje výkonné inteligentní nástroje pro analýzu všech tuto telemetrii do vám pomůže při ladění aplikace a které vám pomohou pochopit, co uživatelé dělají s ním. Můžete zjistit, zda Špička při odezvy je z důvodu něco v aplikaci nebo některé externí resourcing problém. Pokud používáte Visual Studio a aplikace se na chyby, vám může přesměrováni vpravo na problém řádky kódu, můžete ji opravit.

- **Analýza protokolu** je pro uživatele, kteří potřebují k optimalizaci výkonu a plán údržby na aplikace běžící v produkčním prostředí. Pracuje v Azure. Shromažďuje a agreguje data z mnoha zdrojů, i když s zpožděním 10 až 15 minut. Poskytuje komplexní řešení pro správu IT pro Azure, místní a cloudové infrastruktuře jiných výrobců (například Amazon Web Services). Poskytuje bohatší nástroje k analýze dat napříč více zdrojů, umožňuje komplexní dotazy napříč všechny protokoly a může aktivně upozornit na zadaných podmínek. Můžete dokonce shromáždění vlastních dat do své centrální úložiště, takže se můžete dotazovat a vizualizovat ho.

- **System Center Operations Manager (SCOM)** je pro správu a monitorování instalace velké cloudu. Je již obeznámeni s ním jako nástroj pro správu pro místní server systému Windows a na základě technologie Hyper-V-cloudy, ale můžete také integrovat a spravovat aplikace Azure. Kromě jiných věcí ho můžete nainstalovat na existující živé aplikace Application Insights. Pokud aplikace přestane fungovat, zde zjistíte v sekundách.


## <a name="next-steps"></a>Další postup

- [Osvědčené postupy pro vytváření šablon Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices).

- [Příklady implementace zásad správného řízení předplatného Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-subscription-examples).

- [Microsoft Azure Government](https://docs.microsoft.com/azure/azure-government/).
