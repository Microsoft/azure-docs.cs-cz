---
title: Osvědčené postupy pro podniky Přesun do Azure | Microsoft Docs
description: Popisuje zobrazení vygenerovaného uživatelského rozhraní, které podniky můžete použít k zajištění zabezpečeného a spravovat prostředí.
services: azure-resource-manager
documentationcenter: na
author: rdendtler
manager: timlt
editor: tysonn
ms.assetid: 8692f37e-4d33-4100-b472-a8da37ce628f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2017
ms.author: rodend;karlku;tomfitz
ms.openlocfilehash: 02553bb59cb37bab21d53adafc42fddcf08795a7
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="azure-enterprise-scaffold---prescriptive-subscription-governance"></a>Azure enterprise vygenerované uživatelské rozhraní – zásady správného řízení doporučený předplatného
Podniky jsou stále přijetí veřejného cloudu pro jeho její agilnost a flexibilitu. Budou se využívá v cloudu síly ke generování výnosy nebo optimalizovat prostředky pro firmy. Microsoft Azure poskytuje různé služby, aby podniky můžete sestavit jako stavební bloky k řešení široké škály úlohy a aplikace. 

Ale zároveň budete vědět, kde začít je často složité. Jakmile se rozhodnete používat Azure, běžně nastat několik otázek:

* "Jak mám splňují naše právní požadavky pro suverenity dat v některých zemích?"
* "Jak zjistím, že někdo nezmění nechtěně důležitých systémových?"
* "Jak poznám, co každý prostředků se nepodporuje, a proto I můžete účet pro ho a faktury přesně zpátky?"

Potenciálního předplatné prázdný s žádná které ochrana je jednoduché. Tento prázdný prostor může zabránit spuštění vaší přesunout do Azure.

Tento článek obsahuje výchozí bod pro technické profesionály, které adres nutnost zásad správného řízení a vyvážit s potřebu flexibility. Zavádí koncepci enterprise vygenerované uživatelské rozhraní, která provede organizace v implementaci a správu svých předplatných Azure. 

## <a name="need-for-governance"></a>Nutnost zásad správného řízení
Při přesunu do Azure, musíte vyřešit, v tématu Zásady správného řízení již v rané fázi k zajištění úspěšné využití cloudu v rámci podniku. Bohužel byrokracie vytváření systému komplexní zásad správného řízení a čas znamená, že některé obchodní skupiny přejít přímo na dodavateli bez zásahu podnikovém IT. Tuto metodu můžete ponechat podniku otevřete na slabá místa zabezpečení Pokud nejsou správně spravované prostředky. Charakteristika veřejný cloud – flexibility, flexibilitu a na základě spotřeby ceny - jsou důležité pro obchodní skupiny, které je potřeba rychle splňují požadavky zákazníků (interních i externích). Ale podnikovém IT potřebuje k zajištění efektivní ochrany dat a systémy.

Ve skutečnosti generování uživatelského rozhraní slouží k vytvoření základ struktury. Vygenerované uživatelské rozhraní provede obecný postup a poskytuje ukotvení body pro více trvalých systémy chcete připojit. Vygenerované uživatelské rozhraní enterprise je stejný: Sada flexibilní ovládací prvky a možnosti Azure, které poskytují strukturu prostředí a kotvy pro služby založený na veřejném cloudu. Poskytuje tvůrce (IT a obchodních skupin) foundation a vytvořte nové služby.

Vygenerované uživatelské rozhraní je založena na postupy, které jsme shromáždili z mnoha závazky s klienty různých velikostí. Tyto klienty v rozsahu od malých organizací vývoj řešení v cloudu a 500 žádnou podniky a nezávislé dodavatele softwaru, kteří jsou migrace a vývoji řešení v cloudu. Vygenerované uživatelské rozhraní enterprise je "vytvořeného pro tento účel" flexibilně pro podporu tradičních IT úlohy a agilní zatížení; například vývojáři, kteří vytvářejí aplikace software jako služba (SaaS) podle možnosti Azure.

Vygenerované uživatelské rozhraní enterprise slouží jako základ pro každý nový odběr v rámci Azure. Umožňuje správcům zajistit, aby úlohy splňovat požadavky zásad správného řízení minimální organizace bez brání rychle splňuje vlastní cíle obchodních skupin a vývojářů.

> [!IMPORTANT]
> Zásady správného řízení je rozhodující pro úspěch Azure. Tento článek zaměřuje na technickou implementaci vygenerované uživatelské rozhraní enterprise, ale pouze dotykem na širší proces a vztahy mezi součástmi. Zásady správného řízení zásad toků shora dolů a je dáno firmy chce zajistit. Samozřejmě vytvoření modelu zásad správného řízení pro Azure zahrnuje zástupců z oddělení IT, ale ještě důležitější musí mít silné reprezentace z žebříčky obchodní skupiny a zabezpečení a řízení rizik. V části end vygenerované uživatelské rozhraní enterprise je zmírňování rizika obchodní k usnadnění organizace zvláště a cílů.
> 
> 

Na následujícím obrázku jsou popsány součásti vygenerované uživatelské rozhraní. Základ spoléhá na plnou plán pro oddělení, účty a odběry. Pilíře obsahovat zásady Resource Manager a silné standardy pro vytváření názvů. Zbytek vygenerované uživatelské rozhraní pochází z základní možnosti Azure a že povolení funkce zabezpečení a spravovat prostředí.

![součásti vygenerované uživatelské rozhraní](./media/resource-manager-subscription-governance/components.png)

> [!NOTE]
> Azure se zvětšil rychle od jeho uvedení v 2008. Tento nárůst vyžaduje Microsoft vývojové týmy přehodnotit jejich přístup pro správu a nasazení služeb. Azure Resource Manager modelu byla zavedena v 2014 a nahradí modelu nasazení classic. Resource Manager umožňuje organizacím snadno nasadit, uspořádání a řízení prostředků Azure. Správce prostředků obsahuje paralelizace při vytváření prostředků pro rychlejší nasazení řešení komplexní, vzájemně souvisí. Zahrnuje také řízení granulární přístupu a možnost Značka prostředky s metadaty. Společnost Microsoft doporučuje, abyste vytvořili všechny prostředky pomocí modelu Resource Manager. Vygenerované uživatelské rozhraní enterprise je explicitně určená pro model Resource Manager.
> 
> 

## <a name="define-your-hierarchy"></a>Definovat hierarchii
Základ pro vygenerované uživatelské rozhraní je podnikového zápisu Azure (a podnikovém portálu). Podnikového zápisu definuje tvar a využívají služby Azure v rámci společnosti a struktura zásad správného řízení jádra. V rámci smlouvy enterprise je možné dále rozdělit prostředí do oddělení, účty a nakonec odběry zákazníků. Předplatné Azure je základní jednotkou, kde jsou všechny prostředky obsažené. Definuje také několik omezení v rámci Azure, například na počtu jader, zdroje atd.

![Hierarchie](./media/resource-manager-subscription-governance/agreement.png)

Každý enterprise se liší a hierarchie v předchozí obrázek umožňuje významné flexibilitu v uspořádání Azure v rámci společnosti. Před implementací pokyny obsažené v tomto dokumentu, by měl modelu vaší hierarchii a pochopit vliv na fakturace, přístup k prostředkům a složitost.

Jsou tři obecné vzory pro registraci v Azure:

* **Funkční** vzor
  
    ![funkční](./media/resource-manager-subscription-governance/functional.png)
* **Organizační jednotky** vzor 
  
    ![nejdůležitější.](./media/resource-manager-subscription-governance/business.png)
* **Geografické** vzor
  
    ![zeměpisná](./media/resource-manager-subscription-governance/geographic.png)

Můžete použít vygenerované uživatelské rozhraní na úrovni předplatného rozšířit zásad správného řízení požadavky podniku do předplatného.

## <a name="naming-standards"></a>Standardy pro vytváření názvů
První pilíře vygenerované uživatelské rozhraní je pojmenování standardů. Dobře navrženou standardy pro vytváření názvů umožňují identifikovat prostředky na portálu, faktury a v rámci skriptů. S největší pravděpodobností už máte pojmenování standardy pro místní infrastrukturu. Při přidávání Azure pro vaše prostředí, by měl rozšířit tyto standardy pro vytváření názvů vašich prostředků Azure. Pojmenování standard usnadnění efektivnější správy prostředí na všech úrovních.

> [!TIP]
> Pro zásady vytváření názvů:
> * Zkontrolujte a přijmout, kde je to možné [Patterns and Practices pokyny](../guidance/guidance-naming-conventions.md). Tyto pokyny vám pomůže zjistit, na smysluplný standardní pojmenování.
> * Použijte camelCasing pro názvy prostředků (například myResourceGroup a vnetNetworkName). Poznámka: Existují některé prostředky, například účty úložiště, kde je jedinou možností používat malá písmena (a žádné speciální znaky).
> * Zvažte použití zásad Azure Resource Manager (popsané v další části) vynutit standardy pro vytváření názvů.
> 
> Předchozí tipy vám pomůžou při implementaci konzistentní zásady vytváření názvů.

## <a name="policies-and-auditing"></a>Zásady a auditování
Druhého pilíře vygenerované uživatelské rozhraní zahrnuje vytvoření [Azure zásady](../azure-policy/azure-policy-introduction.md) a [auditování protokolu činnosti](resource-group-audit.md). Správce prostředků zásady vám poskytují možnost spravovat rizika v Azure. Můžete definovat zásady, které zajišťují suverenity dat. omezení, vynucování nebo auditování určité akce. 

* Zásady je výchozí **povolit** systému. Akce řídit definice a přiřazení zásad na prostředky, které odepřít nebo auditovat akce na prostředky.
* Zásady jsou popsané podle definice zásady v jazyce definice zásady (Pokud potom podmínky).
* Můžete vytvořit zásady formátované soubory s JSON (Javascript Object Notation). Po definování zásad, můžete ho přiřadit na konkrétní rozsah: předplatné, skupinu prostředků nebo prostředek.

Zásady mají různé akce, které umožňují podrobných způsob pro vaše scénáře. Akce, které jsou:

* **Odepřít**: blokovat žádost o prostředku
* **Audit**: umožňuje požadavek, ale přidá řádek do protokolu činnosti (který může být použit k poskytování výstrahy a pro spuštění sady runbook)
* **Připojit**: informace o zadaném přidává k prostředku. Například pokud na prostředku není značku "CostCenter", přidejte tuto značku výchozí hodnotu.

### <a name="common-uses-of-resource-manager-policies"></a>Běžná použití služby Správce prostředků zásady
Azure Resource Manager zásady jsou výkonný nástroj v sadě nástrojů Azure. Ty umožňují vyhnout neočekávané nákladům, k identifikaci nákladové středisko pro prostředkům prostřednictvím označování a ujistěte se, zda jsou splněny požadavky na dodržování předpisů. Zásady jsou v kombinaci s integrované funkce auditování, můžete dotazování komplexní a flexibilní řešení. Zásady umožňují společnosti, které poskytují ovládací prvky pro úlohy "Tradičních IT" a "Agile" zatížení; například vývoj aplikací zákazníka. Většina běžných vzorů, které vidíte pro zásady jsou:

* **Geograficky nebo data dodržování předpisů suverenity** -Azure poskytuje oblastech po celém světě. Podniky se často chcete řídit, kdy jsou prostředky vytvořeny (ať už zajistit suverenity dat nebo jenom zajistit, že prostředky jsou vytvořeny blízko koncoví uživatelé prostředků).
* **Správa nákladů** -předplatné služby Azure může obsahovat prostředky mnoho typů a škálování. Společnosti často chcete zajistit, že standardní odběry nepoužívejte zbytečně velké prostředků, které může náklady stovky dolarů za měsíc nebo více.
* **Výchozí zásady správného řízení prostřednictvím požadované značky** -vyžadování značek je jedním nejběžnější a vysoce požadované funkce. Pomocí Azure Resource Manager zásad podniky dokážou zajistit, že je prostředek správně označené. Nejběžnější značky jsou: oddělení, vlastník prostředku a prostředí typu (například - provozní, testovací, vývoj)

**Příklady**

"Tradičních IT" předplatné pro-obchodní aplikace

* Vynuťte oddělení a vlastník značek u všech zdrojů
* Omezit vytvoření prostředku pro oblast Severní Americe
* Omezit schopnost vytvářet virtuální počítače G-Series a clustery HDInsight

"Agile" prostředí pro organizační jednotku vytváření cloudových aplikací

* Splnění suverenity dat., povolit vytváření prostředků pouze v určité oblasti.
* Vynuťte značky prostředí na všechny prostředky. Pokud prostředek je vytvořen bez značku, připojte **prostředí: Neznámý** značky k prostředku.
* Audit při prostředky se vytvoří mimo Severní Ameriku, ale nebudou bránit.
* Audit při vytvoření vysoce náklady na prostředky.

> [!TIP]
> Nejběžnější použití zásad Resource Manager, v organizacích je ovládací prvek *kde* je možné prostředky vytvořit a *co* lze vytvořit typy prostředků. Kromě poskytování ovládacích prvků na *kde* a *co*, mnoho podniků pomocí zásad zajistit prostředky příslušná metadata k vyúčtování zpět pro používání. Doporučujeme uplatňovat zásady na úrovni předplatného pro:
> 
> * Suverenity geograficky nebo data dodržování předpisů
> * Správa nákladů
> * Požadované značky (ROZHODNUTY podle potřeby podniku, jako je například BillTo, majitel aplikace)
> 
> Můžete použít další zásady na nižších úrovních oboru.
> 
> 

### <a name="audit---what-happened"></a>Audit - co se stalo?
Pokud chcete zobrazit, jak funguje prostředí, budete muset audit aktivity uživatelů. Většina typů prostředků v rámci Azure vytvořit diagnostických protokolů, které můžete analyzovat pomocí některého nástroje, protokolu nebo v Azure Log Analytics. Můžete shromáždit protokoly aktivity mezi více předplatných k poskytování oddělení nebo enterprise zobrazení. Záznamy auditu jsou důležité diagnostický nástroj i zásadní mechanismus pro aktivační události v prostředí Azure.

Protokoly aktivity z nasazení Resource Manager umožňují určit **operations** která trvala místní a kdo je provedl. Protokoly aktivity se můžou shromažďovat a agregovat pomocí nástroje, například analýzy protokolů.

## <a name="resource-tags"></a>Značky prostředku
Jak uživatelé ve vaší organizaci přidat prostředky do odběru, stane se stále důležité prostředky přidružit příslušné oddělení, zákazníků a prostředí. Metadata můžete připojit k prostředkům prostřednictvím [značky](resource-group-using-tags.md). Použití značek k zadání informací o prostředku nebo vlastník. Značky umožňují nejen agregovat a skupiny prostředků v různých způsobů, ale tato data použít pro účely vracení peněz. Můžete označit prostředky s až 15 páry klíč: hodnota. 

Značky prostředku jsou flexibilní a by měl být připojen nejvíce zdrojů. Příklady běžných značky prostředku jsou:

* BillTo
* Oddělení (nebo organizační jednotka)
* Prostředí (vývoj pro produkční fázi)
* Úroveň (webová vrstva, aplikační vrstvě)
* Majitel aplikace
* ProjectName

![tags](./media/resource-manager-subscription-governance/resource-group-tagging.png)

Další příklady značek najdete v tématu [doporučená zásady vytváření názvů pro prostředky Azure](../guidance/guidance-naming-conventions.md).

> [!TIP]
> Mějte na paměti, což zásadu, která vyžaduje označování pro:
> 
> * Skupiny prostředků
> * Úložiště
> * Virtuální počítače
> * Aplikační služby prostředí nebo webové servery
> 
> Tato strategie označování identifikuje ve vašich předplatných, jaká metadata je potřeba pro firmy, finance, zabezpečení, řízení rizik a celkové správy prostředí. 

## <a name="resource-group"></a>Skupina prostředků
Resource Manager umožňuje umístit prostředky do smysluplný skupin pro správu, fakturace nebo přírodní spřažení. Jak už bylo zmíněno dříve, Azure má dva modely nasazení. V dřívějších klasického modelu se základní jednotkou správy předplatného. Bylo obtížné rozdělení prostředků v rámci předplatného, která vedla k vytvoření velkého počtu předplatných. Pomocí modelu Resource Manager jsme viděli zavedení skupiny prostředků. Skupiny prostředků jsou kontejnery prostředky, které mají společné životního cyklu nebo sdílet atribut jako je například "všechny SQL servery" nebo "Aplikaci A".

Skupiny prostředků, nemůžou být obsažené v sobě navzájem a zdroje můžete pouze patřit do jedné skupiny prostředků. Některé akce můžete použít na všechny prostředky ve skupině prostředků. Odstranění skupiny prostředků, například odebere všechny prostředky v rámci skupiny prostředků. Obvykle umístěte celou aplikaci nebo související systémová ve stejné skupině prostředků. Třívrstvá aplikace s názvem Contoso webová aplikace by například obsahovat webový server, aplikační server a SQL server ve stejné skupině prostředků.

> [!TIP]
> Jak uspořádat vaší skupiny prostředků může lišit úlohy "Tradičních IT" na "Agilní IT" úlohy:
> 
> * "Tradičních IT" úlohy jsou nejčastěji seskupené podle položky v rámci stejný životní cyklus, jako je například aplikace. Umožňuje seskupení aplikací pro správu jednotlivých aplikací.
> * "Agilní IT" úlohy jsou obvykle umožňuje zaměřit se na externí zákazníkem cloudové aplikace. Skupiny prostředků musí být stejný vrstvy nasazení (například webovou vrstvu, vrstvy aplikace) a správu.
> 
> Principy vaše úlohy usnadňuje vývoj strategie pro skupinu prostředků.

## <a name="role-based-access-control"></a>Řízení přístupu na základě role
Budete pravděpodobně žádáme sami "kdo má mít přístup k prostředkům?" a "jak lze řídit tento přístup?" Je důležitá umožňuje nebo zakazuje přístup k portálu Azure a řízení přístupu k prostředkům na portálu. 

Pokud Azure byla původně, řízení přístupu k odběru byly základní: správce nebo spolusprávce. Přístup k odběru v klasickém modelu implicitní přístup k všechny prostředky v portálu. Kvůli chybějící jemně odstupňovanou kontrolu vedla k rozšiřování odběry zajistit úroveň řízení přiměřené přístupu pro Azure zápisu.

Tento, jak narůstá počet odběrů již není potřeba. Pomocí řízení přístupu na základě rolí můžete přiřadit uživatele k standardní role (jako je například běžné "čtení" a "zápis" typy rolí). Můžete také definovat vlastní role.

> [!TIP]
> Implementovat řízení přístupu na základě rolí:
> * Připojte vaše podnikové identitě úložiště (nejčastěji Active Directory) do Azure Active Directory pomocí nástroje AD Connect.
> * Řízení správce nebo Spolusprávce předplatného pomocí spravovaného identity. **Nemáte** správce nebo spolusprávce přiřadit nový vlastník předplatného. Místo toho použijte role RBAC zajistit **vlastníka** práv na skupinu nebo jednotlivé.
> * Azure uživatele přidáte do skupiny (například X vlastníci aplikace) ve službě Active Directory. Pomocí synchronizoval skupiny lze zadat členy skupiny příslušná oprávnění ke správě skupinu prostředků obsahující aplikaci.
> * Postupujte podle Princip přidělování **nejnižší oprávnění** potřeba očekávané práci. Příklad:
>   * Nasazení skupiny: Skupina, která je možné instalovat prostředky.
>   * Správa virtuálních počítačů: Skupiny, která je možné spustit virtuální počítače (pro operace)
> 
> Tyto tipy můžete spravovat přístup uživatelů v rámci vašeho předplatného.

## <a name="azure-resource-locks"></a>Zámky prostředků Azure.
Jak vaše organizace přidá základní služby pro předplatné, bude stále důležité zajistit, že tyto služby jsou k dispozici chcete zabránit přerušení obchodní. [Uzamčení prostředků](resource-group-lock-resources.md) vám umožní omezit operací s prostředky vysoké hodnoty, jejichž změně nebo odstranění by mohlo mít významný dopad na vaší aplikace nebo cloudové infrastruktury. Zámky můžete použít pro předplatné, skupinu prostředků nebo prostředek. Zámky obvykle použijete pro základní prostředkům, například virtuální sítě, bran a účty úložiště. 

Uzamčení prostředků v současné době podporují dvou hodnot: CanNotDelete a jen pro čtení. CanNotDelete znamená, že uživatelé (s odpovídající práva) můžete stále číst nebo upravit prostředek, ale nelze ho proto odstranit. Jen pro čtení znamená, že oprávnění uživatelé nejde odstranit ani změnit prostředku.

Vytvořit nebo odstranit zámky správy, musíte mít přístup k `Microsoft.Authorization/*` nebo `Microsoft.Authorization/locks/*` akce.
Z předdefinovaných rolí pouze vlastník a správce přístupu uživatelů mají tyto akce.

> [!TIP]
> Základní možnosti sítě by měly být chráněné s zámky. Nechtěnému odstranění brány, site-to-site VPN by katastrofální k předplatnému Azure. Azure vám odstranit virtuální síť, která je používána neumožňuje, ale použijí další omezení, která je užitečné opatření. 
> 
> * Virtual Network: CanNotDelete
> * Skupina zabezpečení sítě: CanNotDelete
> * Zásady: CanNotDelete
> 
> Zásady jsou také zásadní význam pro údržbu příslušných ovládacích prvků. Doporučujeme, abyste použili **CanNotDelete** zámek pro zásady, které jsou používány.

## <a name="core-networking-resources"></a>Základní síťové prostředky
Přístup k prostředkům může být vnitřní (v rámci podnikové sítě) nebo externí (prostřednictvím Internetu). Je snadné pro uživatele ve vaší organizaci nechtěně uvést prostředky do nesprávného místě a potenciálně škodlivým přístupem otevírat. Stejně jako u místní zařízení, musíte přidat podniky příslušných ovládacích prvků k zajištění, že Azure uživatelé provádět správné rozhodnutí. Pro řízení předplatné jsme identifikovat core prostředky, které poskytují základní řízení přístupu. Prostředky jader skládá z:

* **Virtuální sítě** jsou objekty kontejneru pro podsítě. Když je to nezbytně nutné, často se používá při připojování aplikace k interním firemním prostředkům.
* **Skupin zabezpečení sítě** jsou podobná bránou firewall a poskytují pravidla pro jak prostředku může "kontaktovat" přes síť. Poskytují podrobnou kontrolu nad jak / pokud podsíť (nebo virtuální počítač) lze připojit k Internetu nebo jiných podsítí ve stejné virtuální síti.

![Základní síťové služby](./media/resource-manager-subscription-governance/core-network.png)

> [!TIP]
> Pro sítě:
> * Vytvoření virtuální sítě, které jsou vyhrazené pro externího úlohy a úlohy interní přístupem. Tento přístup snižuje riziko nechtěně umístění virtuálních počítačů, které jsou určené pro interní úlohy v externí přístupných prostoru.
> * Konfigurace skupin zabezpečení sítě pro omezení přístupu. Minimálně blokovat přístup k Internetu z interní virtuální sítě a blokovat přístup k podnikové síti z externí virtuální sítě.
> 
> Tyto tipy vám pomůžou při implementaci zabezpečení síťových prostředků.

### <a name="automation"></a>Automation
Správa prostředků jednotlivě není obě časově náročná a potenciálně chyby, které jsou náchylné k chybám pro určité operace. Azure poskytuje různé možnosti automatizace včetně Azure Automation, Logic Apps a Azure Functions. [Služby Azure Automation](../automation/automation-intro.md) správcům umožňuje vytvořit a definovat sady runbook pro zpracování běžné úkoly při správě prostředků. Vytváření runbooků pomocí editoru kódu prostředí PowerShell nebo grafický editor. Můžete vytvořit komplexní fáze více pracovních postupů. Služby Azure Automation se často používá ke zpracování běžné úkoly jako vypíná nepoužité zdroje nebo vytváření prostředků v reakci na konkrétní aktivační událost bez nutnosti lidského zásahu.

> [!TIP]
> Pro automatizaci:
> * Vytvoření účtu Azure Automation a zkontrolujte dostupné sady runbook (grafickém uživatelském rozhraní nebo příkaz řádku) k dispozici v [Galerie Runbooků](../automation/automation-runbook-gallery.md).
> * Import a přizpůsobit klíče sady runbook pro vlastní použití.
> 
> Běžný scénář je možnost spuštění nebo vypnutí virtuální počítače v plánu. Existují příklad sady runbook, které jsou k dispozici v galerii které u tohoto scénáře i můžete naučit, jak ho rozbalte.
> 
> 

## <a name="azure-security-center"></a>Azure Security Center
Jeden z největších blokování do cloudu přijetí možná byl, aby si nemuseli dělat starosti nad zabezpečením. Riziko správci IT oddělením a oddělením zabezpečení potřeba zajistit, aby byly zabezpečené prostředky v Azure. 

[Azure Security Center](../security-center/security-center-intro.md) poskytuje centrální zobrazení stavu zabezpečení prostředků ve předplatných a poskytuje doporučení, které pomáhají zabránit ohroženými prostředky. Podrobnější zásady (například používá zásady skupiny konkrétní prostředků, které povolí enterprise podle jejich postavení riziku, které budou adresování) ji můžete povolit. Nakonec Azure Security Center je otevřená platforma umožňující vytvoření software, který se připojuje k Azure Security Center ke zvýšení jeho funkce pro partnery společnosti Microsoft a nezávislí dodavatelé softwaru. 

> [!TIP]
> Azure Security Center je povolená ve výchozím nastavení v každém předplatném. Shromažďování dat z virtuálních počítačů umožňující Azure Security Center nainstalovat jeho agenta a spustíte shromažďování dat, ale musíte povolit.
> 
> ![Shromažďování dat](./media/resource-manager-subscription-governance/data-collection.png)
> 
> 

## <a name="next-steps"></a>Další postup
* Teď, když jste se naučili o zásad správného řízení předplatné, je čas zobrazíte tato doporučení v praxi. V tématu [příklady implementace zásad správného řízení předplatného Azure](resource-manager-subscription-examples.md).

