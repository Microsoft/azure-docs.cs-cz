---
title: Uspořádání prostředků pomocí skupin pro správu – zásady správného řízení Azure
description: Další informace o skupinách pro správu, fungování jejich oprávnění a způsobu jejich využití
ms.date: 09/22/2020
ms.topic: overview
ms.custom: contperfq1
ms.openlocfilehash: e3bc3ee34227fd23ea9f56070f8ea7776a10a134
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91533801"
---
# <a name="what-are-azure-management-groups"></a>Co jsou skupiny pro správu Azure?

Pokud má vaše organizace mnoho předplatných, možná budete potřebovat způsob, jak efektivně spravovat přístup, zásady a dodržování předpisů pro tato předplatná. Skupiny pro správu Azure poskytují úroveň oboru nad předplatnými. Předplatná uspořádáte do kontejnerů označovaných jako skupiny pro správu a na tyto skupiny pro správu použijete své zásady správného řízení. Všechna předplatná v rámci skupiny pro správu automaticky dědí podmínky, které se na příslušnou skupinu pro správu vztahují. Skupiny pro správu poskytují správu na podnikové úrovni ve velkém měřítku bez ohledu na to, jaké typy předplatného případně máte.
Všechna předplatná v rámci jedné skupiny pro správu musí důvěřovat stejnému tenantovi Azure Active Directory.

Zásady můžete například použít pro skupinu pro správu, která omezuje oblasti dostupné pro vytváření virtuálních počítačů (VM). Tyto zásady se použijí pro všechny skupiny pro správu, předplatná a prostředky v rámci této skupiny a umožní vytváření virtuálních počítačů jenom v příslušné oblasti.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Hierarchie skupin pro správu a předplatných

Můžete vytvořit flexibilní strukturu skupin pro správu a předplatných a uspořádat tak prostředky do hierarchie umožňující využít jednotné zásady a správu přístupu. Následující diagram ukazuje příklad vytvoření hierarchie pro zásady správného řízení s využitím skupin pro správu.

:::image type="complex" source="./media/tree.png" alt-text="Diagram ukázkové hierarchie skupin pro správu" border="false":::
   Diagram kořenové skupiny pro správu, který má skupiny pro správu i odběry. Některé podřízené skupiny pro správu uchovávají skupiny pro správu, některá předplatná a některá se uchovávají. Jedním z příkladů v ukázkové hierarchii jsou čtyři úrovně skupin pro správu, které mají úroveň podřízenosti všechna předplatná.
:::image-end:::

Můžete vytvořit hierarchii, která aplikuje zásadu, například omezení umístění virtuálních počítačů na oblast USA – západ ve skupině s názvem Produkce. Tato zásada se bude dědit do všech předplatných se smlouvou Enterprise, která jsou následníky dané skupiny pro správu, a bude se vztahovat na všechny virtuální počítače v rámci těchto předplatných. Tuto zásadu zabezpečení nemůže změnit vlastník prostředku ani předplatného. Výsledkem je vylepšení zásad správného řízení.

Dalším scénářem, kde by se skupiny pro správu použily, je poskytnutí uživatelského přístupu k několika předplatným. Přesunutím více předplatných v rámci této skupiny pro správu můžete vytvořit jedno [přiřazení role Azure](../../role-based-access-control/overview.md) ve skupině pro správu, které zdědí tento přístup ke všem předplatným. Jedno přiřazení ve skupině pro správu může uživatelům umožnit přístup ke všemu, co potřebují, místo skriptování Azure RBAC přes různá předplatná.

### <a name="important-facts-about-management-groups"></a>Důležitá fakta týkající se skupin pro správu

- Jeden adresář podporuje až 10 000 skupin pro správu.
- Strom skupin pro správu může mít až šest úrovní vnoření.
  - Toto omezení nezahrnuje kořenovou úroveň ani úroveň předplatného.
- Každá skupina pro správu a předplatné může mít jenom jeden nadřazený prvek.
- Každá skupina pro správu může mít mnoho podřízených položek.
- Všechny skupiny pro správu a předplatná jsou v rámci adresáře v jedné hierarchii. [Důležité informace o kořenových skupinách pro správu](#important-facts-about-the-root-management-group)

## <a name="root-management-group-for-each-directory"></a>Kořenová skupina pro správu pro jednotlivé adresáře

Každý adresář obdrží jednu skupinu pro správu nejvyšší úrovně, která se označuje jako kořenová skupina pro správu. Tato kořenová skupina pro správu je integrovaná do hierarchie tak, aby pod ní spadaly všechny skupiny pro správu a všechna předplatná. Tato kořenová skupina pro správu umožňuje použití globálních zásad a přiřazení rolí Azure na úrovni adresáře. [Globální správce Azure AD musí sám sobě zvýšit oprávnění](../../role-based-access-control/elevate-access-global-admin.md), aby v počátečním nastavení měl pro tuto kořenovou skupinu roli správce uživatelského přístupu. Po zvýšení úrovně přístupu může správce přiřadit libovolnou roli Azure ostatním uživatelům adresáře nebo skupinám pro správu hierarchie. Jako správce můžete jako vlastníka kořenové skupiny pro správu nastavit svůj vlastní účet.

### <a name="important-facts-about-the-root-management-group"></a>Důležité informace o kořenových skupinách pro správu

- Ve výchozím nastavení je zobrazovaný název kořenové skupiny pro správu **Kořenová skupina tenanta**. ID je ID služby Azure Active Directory.
- Pokud chcete tento zobrazovaný název změnit, musí váš účet mít pro příslušnou kořenovou skupinu pro správu roli Vlastník nebo Přispěvatel. Viz [Změna názvu skupiny pro správu](manage.md#change-the-name-of-a-management-group) , aby se aktualizoval název skupiny pro správu.
- Kořenová skupina pro správu se na rozdíl od ostatních skupin pro správu nedá přesunout ani odstranit.  
- Všechna předplatná a skupiny pro správu v rámci adresáře spadají do jedné kořenové skupina pro správu.
  - Všechny prostředky v adresáři spadají do kořenové skupiny pro správu, která umožňuje globální správu.
  - Nová předplatná při vytvoření ve výchozím nastavení automaticky spadají do kořenové skupiny pro správu.
- Kořenovou složku pro správu sice vidí všichni zákazníci Azure, ale ne všichni mají přístup ke správě této skupiny.
  - Každý, kdo má přístup k předplatnému, vidí kontext tohoto předplatného v rámci hierarchie.  
  - Ke kořenová skupina pro správu nikdo nemá výchozí přístup. Globální správci Azure AD jsou jedinými uživateli, kteří si sami sobě mohou zvýšit oprávnění a získat tak přístup. Až budou mít přístup do kořenové skupiny pro správu, můžou globální správci přiřazovat role Azure ostatním uživatelům ke správě.  
    její.
- V sadě SDK je kořenová skupina pro správu nebo "klient" root "provozována jako skupina pro správu.

> [!IMPORTANT]
> Všechna přiřazení uživatelského přístupu nebo zásad v kořenové skupině pro správu **se použijí pro všechny prostředky v rámci příslušného adresáře**. Vzhledem k tomu by uživatelé měli vyhodnotit, jestli je potřeba mít prostředky definované v tomto rozsahu. Přiřazení přístupu uživatele a zásad by mělo být pouze v tomto  
> oboru.

## <a name="initial-setup-of-management-groups"></a>Počáteční nastavení skupin pro správu

Když libovolný uživatel začne využívat skupiny pro správu, musí proběhnout úvodní proces nastavení. Prvním krokem je, že se v adresáři vytvoří kořenová skupina pro správu. Po vytvoření této skupiny se všechna existující předplatná, která v příslušném adresáři existují, nastaví jako podřízené prvky kořenové skupiny pro správu.
Cílem tohoto procesu je zajistit, aby v rámci adresáře existovala jenom jedna hierarchie skupin pro správu. Jedna hierarchie v adresáři umožňuje zákazníkům využít globální přístup a zásady, které ostatní zákazníci v tomto adresáři nemohou obejít. Všechno, co se přiřadí na kořenové úrovni, se použije napříč celou hierarchií, to znamená všemi skupinami pro správu, předplatnými, skupinami prostředků a prostředky v rámci příslušného tenanta Azure AD.

## <a name="trouble-seeing-all-subscriptions"></a>Potíže se zobrazením všech předplatných

U několika adresářů, které začaly využívat skupiny pro správu v rané fázi verze Preview před 25. červnem 2018, může docházet k chybě, kdy v hierarchii nejsou všechna předplatná. Proces pro zařazení předplatných do hierarchie se implementoval až po přiřazení zásad nebo role pro kořenovou skupinu pro správu v příslušném adresáři.

### <a name="how-to-resolve-the-issue"></a>Jak tyto potíže vyřešit

Tento problém můžete vyřešit dvěma způsoby.

- Odebrání všech přiřazení rolí a zásad z kořenové skupiny pro správu
  - Odebráním všech přiřazení zásad a rolí z kořenové skupiny pro správu služba vyplní všechna předplatná do hierarchie dalším jednodenním cyklem. Smyslem tohoto procesu je zajistit, že se žádnému z klientských předplatných neposkytne náhodný přístup nebo přiřazení zásad.
  - Nejlepší způsob, jak to provést bez dopadu na vaše služby, je použít přiřazení zásad nebo rolí o jednu úroveň pod kořenovou skupinu pro správu. Potom můžete všechna přiřazení z kořenového oboru odebrat.
- Přímé volání rozhraní API pro zahájení procesu obnovení
  - Libovolný uživatel adresáře může volat rozhraní API _TenantBackfillStatusRequest_ nebo _StartTenantBackfillRequest_. Rozhraní API StartTenantBackfillRequest po zavolání zahájí proces nastavení přesunu všech předplatných do hierarchie. Tento proces také začne vynucovat, aby se všechna nová předplatná stala podřízeným elementem kořenové skupiny pro správu.
    Tento postup se dá udělat beze změny přiřazení na kořenové úrovni. Voláním rozhraní API říkáte, že je v pořádku, když se přiřazení zásad nebo přístupu na kořenové úrovni použije pro všechna předplatná.

Pokud máte k tomuto procesu obnovení nějaké dotazy, obraťte se na `managementgroups@microsoft.com`.
  
## <a name="management-group-access"></a>Přístup ke skupinám pro správu

Skupiny pro správu Azure podporují [řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/overview.md) pro všechny přístupy k prostředkům a definice rolí.
Tato oprávnění se dědí do podřízených prostředků, které v hierarchii existují. Všechny role Azure je možné přiřadit ke skupině pro správu, která bude dědit hierarchii na prostředky. Například přispěvatele virtuálních počítačů role Azure se dá přiřadit ke skupině pro správu. Tato role nemá ve skupině pro správu žádnou akci, ale zdědí se do všech virtuálních počítačů v rámci této skupiny.

Následující diagram ukazuje role a podporované akce pro skupiny pro správu.

| Název role Azure             | Vytvořit | přejmenování | Pøesunout\*\* | Odstranit | Přiřazení přístupu | Přiřazení zásad | Číst  |
|:-------------------------- |:------:|:------:|:--------:|:------:|:-------------:| :------------:|:-----:|
|Vlastník                       | X      | X      | X        | X      | X             | X             | X     |
|Přispěvatel                 | X      | X      | X        | X      |               |               | X     |
|G Přispěvatel\*            | X      | X      | X        | X      |               |               | X     |
|Čtenář                      |        |        |          |        |               |               | X     |
|Čtečka MG\*                 |        |        |          |        |               |               | X     |
|Přispěvatel zásad prostředků |        |        |          |        |               | X             |       |
|Správce uživatelských přístupů   |        |        |          |        | X             | X             |       |

\*: MG čtenář a g Reader umožňují uživatelům provádět tyto akce pouze v oboru skupiny pro správu.  
\*\*: Přiřazení rolí u kořenové skupiny pro správu se nevyžadují k přesunu předplatného nebo skupiny pro správu do a z ní. Další informace o přesunu položek v rámci hierarchie najdete v tématu věnovaném [správě prostředků s využitím skupin pro správu](manage.md).

## <a name="azure-custom-role-definition-and-assignment"></a>Definice a přiřazení vlastní role Azure

Podpora vlastních rolí Azure pro skupiny pro správu je momentálně ve verzi Preview s některými [omezeními](#limitations). Obor skupiny pro správu můžete definovat v oboru, který se dá přiřadit k definici role. Tato vlastní role Azure pak bude k dispozici pro přiřazení v této skupině pro správu a skupině pro správu, předplatného, skupině prostředků nebo prostředku pod ní. Tato vlastní role se bude dědit v rámci hierarchie jako všechny předdefinované role.  

### <a name="example-definition"></a>Příklad definice

[Definování a vytvoření vlastní role](../../role-based-access-control/custom-roles.md) se nemění se zahrnutím skupin pro správu. Pomocí úplné cesty definujte skupinu pro správu **/providers/Microsoft.Management/managementgroups/{GroupID}**.

Použijte ID skupiny pro správu, nikoli zobrazovaný název skupiny pro správu. K této běžné chybě dochází, protože při vytváření skupiny pro správu jsou obě pole definovaná vlastním polem.

```json
...
{
  "Name": "MG Test Custom Role",
  "Id": "id", 
  "IsCustom": true,
  "Description": "This role provides members understand custom roles.",
  "Actions": [
    "Microsoft.Management/managementgroups/delete",
    "Microsoft.Management/managementgroups/read",
    "Microsoft.Management/managementgroup/write",
    "Microsoft.Management/managementgroup/subscriptions/delete",
    "Microsoft.Management/managementgroup/subscriptions/write",
    "Microsoft.resources/subscriptions/read",
    "Microsoft.Authorization/policyAssignments/*",
    "Microsoft.Authorization/policyDefinitions/*",
    "Microsoft.Authorization/policySetDefinitions/*",
    "Microsoft.PolicyInsights/*",
    "Microsoft.Authorization/roleAssignments/*",
    "Microsoft.Authorization/roledefinitions/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
        "/providers/microsoft.management/managementGroups/ContosoCorporate"
  ]
}
...
```

### <a name="issues-with-breaking-the-role-definition-and-assignment-hierarchy-path"></a>Problémy s přerušením cesty k definici rolí a hierarchii přiřazení

Definice rolí se přiřadí oboru kdekoli v rámci hierarchie skupiny pro správu. Definici role můžete definovat pro nadřazenou skupinu pro správu, zatímco vlastní přiřazení role existuje v podřízeném předplatném. Vzhledem k tomu, že mezi dvěma položkami existuje vztah, při pokusu o oddělení přiřazení z jeho definice se zobrazí chyba.

Řekněme například, že se podíváme na malou část hierarchie pro vizuál.

:::image type="complex" source="./media/subtree.png" alt-text="Diagram ukázkové hierarchie skupin pro správu" border="false":::
   Diagram se zaměřuje na kořenovou skupinu pro správu s podřízenými a marketingovými skupinami pro správu. Skupina pro správu I T má jednu podřízenou skupinu pro správu s názvem produkční, zatímco skupina pro správu marketingu má dvě bezplatné zkušební verze předplatného.
:::image-end:::

Řekněme, že ve skupině pro správu marketingu je definovaná vlastní role. Tato vlastní role se pak přiřadí ke dvěma bezplatným zkušebním předplatným.  

Pokud se pokusíme jeden z těchto předplatných přesunout jako podřízenou skupinu pro správu produkčního prostředí, tento přesun by přerušil cestu od přiřazení role předplatného k definici role skupiny pro správu marketingu. V tomto scénáři se zobrazí chyba oznamující, že přesun není povolený, protože tento vztah přeruší.  

Tento scénář můžete vyřešit několika různými možnostmi:
- Před přesunutím předplatného na nový nadřazený prvek MG odeberte přiřazení role z předplatného.
- Přidejte předplatné do oboru přiřazení definice role.
- Změňte rozsah přiřazení v rámci definice role. V předchozím příkladu můžete aktualizovat obory přiřazení z marketingu na kořenovou skupinu pro správu, aby definice mohla být dostupná jak v obou větvích hierarchie.  
- Vytvořte další vlastní roli, která bude definovaná ve druhé větvi. Tato nová role bude vyžadovat, aby se u předplatného změnilo i přiřazení role.  

### <a name="limitations"></a>Omezení  

Existují určitá omezení, která existují při použití vlastních rolí ve skupinách pro správu. 

 - V oborech přiřazení nové role můžete definovat jenom jednu skupinu pro správu. Toto omezení je zavedeno, aby se snížil počet situací, kdy se odpojí definice rolí a přiřazení rolí. K této situaci dochází, když se předplatné nebo skupina pro správu s přiřazením role přesune na jiný nadřazený prvek, který nemá definici role.  
 - Akce roviny dat poskytovatele prostředků nelze definovat ve vlastních rolích skupiny pro správu. Toto omezení je zavedeno, protože došlo k potížím s latencí při aktualizaci poskytovatelů prostředků datové roviny.
   Tato latence se právě zpracovává a tyto akce budou z definice role zakázané, aby se snížila rizika.
 - Azure Resource Manager neověřuje existenci skupiny pro správu v oboru přiřazení definice role. Pokud je v seznamu uvedeno překlep nebo nesprávné ID skupiny pro správu, bude definice role stále vytvořena.  

## <a name="moving-management-groups-and-subscriptions"></a>Přesun skupin pro správu a předplatných 

Chcete-li přesunout skupinu pro správu nebo odběr jako podřízenou položku jiné skupiny pro správu, je třeba vyhodnotit tři pravidla jako true.

Pokud provádíte akci přesunutí, budete potřebovat: 

- Oprávnění pro zápis a přiřazení role pro skupinu pro správu pro podřízené předplatné nebo skupinu pro správu.
  - Předdefinovaný příklad **vlastníka** role
- Přístup k zápisu skupiny pro správu v cílové nadřazené skupině pro správu.
  - Předdefinovaný příklad role: **vlastník**, **Přispěvatel**, **Přispěvatel skupiny pro správu**
- Přístup k zápisu skupiny pro správu v existující nadřazené skupině pro správu.
  - Předdefinovaný příklad role: **vlastník**, **Přispěvatel**, **Přispěvatel skupiny pro správu**

**Výjimka**: Pokud je cílová nebo existující nadřazená skupina pro správu kořenovou skupinou pro správu, požadavky na oprávnění se nepoužijí. Vzhledem k tomu, že kořenová skupina pro správu je výchozím cílovým bodem pro všechny nové skupiny pro správu a odběry, nepotřebujete pro ni oprávnění k přesunutí položky.

Pokud je role vlastníka v předplatném zděděná z aktuální skupiny pro správu, cíle přesunutí jsou omezené. Předplatné můžete přesunout jenom do jiné skupiny pro správu, kde máte roli vlastníka. Nemůžete ho přesunout do skupiny pro správu, kde jste přispěvatel, protože byste ztratili vlastnictví tohoto předplatného. Pokud jste přiřazeni přímo k roli vlastníka pro předplatné (nedědí se ze skupiny pro správu), můžete ho přesunout do jakékoli skupiny pro správu, kde jste přispěvatelem.

## <a name="audit-management-groups-using-activity-logs"></a>Audit skupin pro správu s využitím protokolů aktivit

Skupiny pro správu se podporují v rámci [protokolu aktivit Azure](../../azure-monitor/platform/platform-logs-overview.md). Můžete hledat všechny události, ke kterým dochází ve skupině pro správu ve stejném centrálním umístění jako ostatní prostředky Azure. Pro konkrétní skupinu pro správu si můžete si zobrazit všechny změny přiřazení zásad nebo přiřazení rolí.

:::image type="content" source="./media/al-mg.png" alt-text="Diagram ukázkové hierarchie skupin pro správu" border="false":::

Pokud se chcete na skupiny pro správu dotazovat mimo Azure Portal, cílový obor pro skupiny pro správu vypadá takto: **"/providers/Microsoft.Management/managementGroups/{yourMgID}"**.

## <a name="next-steps"></a>Další kroky

Další informace o řešeních pro správu najdete v následujících tématech:

- [Vytváření skupin pro správu pro organizaci prostředků Azure](./create.md)
- [Jak měnit, odstraňovat nebo spravovat skupiny pro správu](./manage.md)
- Další informace najdete v tématu možnosti [ochrany vaší hierarchie prostředků](./how-to/protect-resource-hierarchy.md) .