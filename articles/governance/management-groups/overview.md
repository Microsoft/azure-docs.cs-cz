---
title: Uspořádání prostředků pomocí skupin pro správu – zásady správného řízení Azure
description: Seznamte se se skupinami pro správu, jak jejich oprávnění fungují a jak se používají.
author: rthorn17
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: governance
ms.date: 04/22/2019
ms.author: rithorn
ms.topic: overview
ms.openlocfilehash: aa4c5a7bfe7333c02fe79612b828df5680b83b26
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254732"
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Uspořádání prostředků pomocí skupin pro správu Azure

Pokud má vaše organizace mnoho předplatných, budete možná potřebovat způsob, jak efektivně spravovat přístup, zásady a dodržování předpisů pro tyto odběry. Skupiny pro správu Azure poskytují úroveň rozsahu nad odběry. Předplatná uspořádáte do kontejnerů označovaných jako "skupiny pro správu" a podmínky zásad správného řízení se použijí pro skupiny pro správu. Všechna předplatná v rámci skupiny pro správu automaticky přebírají podmínky použité pro skupinu pro správu. Skupiny pro správu poskytují správu na podnikové úrovni ve velkém měřítku bez ohledu na to, jaký typ předplatných máte. Všechna předplatná v rámci jedné skupiny pro správu musí důvěřovat stejnému Azure Active Directory tenantovi.

Můžete například použít zásady pro skupinu pro správu, která omezuje dostupné oblasti pro vytváření virtuálních počítačů. Tato zásada se použije u všech skupin pro správu, odběrů a prostředků v této skupině pro správu tím, že povoluje vytváření virtuálních počítačů pouze v této oblasti.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Hierarchie skupin pro správu a předplatných

Můžete sestavit flexibilní strukturu skupin a předplatných pro správu, které slouží k uspořádání prostředků do hierarchie pro sjednocenou správu zásad a přístupu. Následující diagram znázorňuje příklad vytvoření hierarchie pro zásady správného řízení pomocí skupin pro správu.

![Příklad stromu hierarchie skupiny pro správu](./media/tree.png)

Můžete vytvořit hierarchii, která aplikuje zásadu, například omezení umístění virtuálních počítačů na oblast USA – západ ve skupině s názvem "produkční". Tyto zásady zdědí obě předplatná EA v této skupině pro správu a vztahují se na všechny virtuální počítače v těchto předplatných. Tuto zásadu zabezpečení nelze změnit vlastníkem prostředku nebo předplatného, což umožňuje lepší řízení.

Dalším scénářem, kdy byste měli používat skupiny pro správu, je poskytnout uživatelům přístup k několika předplatným. Přesunutím více předplatných v rámci této skupiny pro správu můžete ve skupině pro správu vytvořit jedno přiřazení [řízení přístupu na základě role](../../role-based-access-control/overview.md) (RBAC), které zdědí přístup ke všem předplatným.
Jedno přiřazení ve skupině pro správu může uživatelům umožnit přístup k všechno, co potřebují, místo skriptování RBAC v různých předplatných.

### <a name="important-facts-about-management-groups"></a>Důležitá fakta týkající se skupin pro správu

- skupiny pro správu 10 000 se můžou podporovat v jednom adresáři.
- Strom skupiny pro správu může podporovat až šest úrovní hloubky.
  - Toto omezení nezahrnuje kořenovou úroveň ani úroveň předplatného.
- Každá skupina pro správu a předplatné můžou podporovat jenom jednu nadřazenou položku.
- Každá skupina pro správu může mít mnoho podřízených objektů.
- Všechna předplatná a skupiny pro správu jsou v rámci jedné hierarchie v jednotlivých adresářích. Podívejte [se na důležité skutečnosti týkající se kořenové skupiny pro správu](#important-facts-about-the-root-management-group).

## <a name="root-management-group-for-each-directory"></a>Kořenová skupina pro správu pro každý adresář

Každému adresáři je přiřazena jedna skupina pro správu nejvyšší úrovně s názvem kořenová skupina pro správu.
Tato kořenová skupina pro správu je integrována do hierarchie nástroje tak, aby byly všechny skupiny pro správu a odběry přeloženy do této hierarchie. Tato kořenová skupina pro správu umožňuje použití globálních zásad a přiřazení RBAC na úrovni adresáře. [Globální správce Azure AD musí nejdřív zvýšit úroveň](../../role-based-access-control/elevate-access-global-admin.md) na roli správce přístupu uživatele této kořenové skupiny. Po zvýšení oprávnění může správce přiřadit hierarchii libovolné role RBAC ostatním uživatelům nebo skupinám adresáře a spravovat hierarchii. Jako správce můžete přiřadit vlastní účet jako vlastníka kořenové skupiny pro správu.

### <a name="important-facts-about-the-root-management-group"></a>Důležitá fakta týkající se kořenové skupiny pro správu

- Ve výchozím nastavení je zobrazovaný název kořenové skupiny pro správu kořenovou **skupinou tenanta**. ID je ID Azure Active Directory.
- Chcete-li změnit zobrazované jméno, musí mít váš účet přiřazenou roli vlastníka nebo přispěvatele pro kořenovou skupinu pro správu. Postup pro změnu názvu najdete v tématu [Změna názvu skupiny pro správu](manage.md#change-the-name-of-a-management-group).
- Kořenovou skupinu pro správu nelze přesunout ani odstranit, na rozdíl od jiných skupin pro správu.  
- Všechna předplatná a skupiny pro správu se přeloží až do jedné kořenové skupiny pro správu v rámci adresáře.
  - Všechny prostředky v adresáři jsou přeloženy do kořenové skupiny pro správu pro globální správu.
  - Nové odběry jsou při vytvoření automaticky nastavené na kořenovou skupinu pro správu.
- Všichni zákazníci Azure můžou zobrazit kořenovou skupinu pro správu, ale ne všichni zákazníci mají přístup ke správě této kořenové skupiny pro správu.
  - Každý, kdo má přístup k předplatnému, uvidí kontext, kde se toto předplatné nachází v hierarchii.  
  - K žádnému výchozímu přístupu ke kořenové skupině pro správu se neudělí nikdo jiný. Globální Správci služby Azure AD jsou jedinými uživateli, kteří se můžou k získání přístupu zvýšit.  Až budou mít přístup ke kořenové skupině pro správu, můžou globální správci přiřadit roli RBAC ostatním uživatelům a spravovat je.  

> [!IMPORTANT]
> Jakékoli přiřazení přístupu uživatele nebo přiřazení zásad v kořenové skupině pro správu **se vztahuje na všechny prostředky v adresáři**.
> Z tohoto důvodu by všichni zákazníci měli vyhodnotit nutnost mít v tomto oboru definované položky.
> Přiřazení přístupu uživatele a zásad by mělo být v tomto oboru pouze "musí být".  

## <a name="initial-setup-of-management-groups"></a>Počáteční nastavení skupin pro správu

Když některý z uživatelů začne používat skupiny pro správu, je k dispozici počáteční proces nastavení, ke kterému dojde. Prvním krokem je vytvoření kořenové skupiny pro správu v adresáři. Po vytvoření této skupiny se všechna existující předplatná, která existují v adresáři, převedou na podřízené skupiny pro správu root. Důvodem tohoto procesu je zajistit, aby existovala pouze jedna hierarchie skupiny pro správu v rámci adresáře. Jediná hierarchie v adresáři umožňuje administrativním zákazníkům použít globální přístup a zásady, které nemůžou nepoužívat jiní zákazníci v adresáři. Cokoli přiřazené k kořenovému adresáři bude platit pro celou hierarchii, která zahrnuje všechny skupiny pro správu, předplatná, skupiny prostředků a prostředky v rámci tohoto tenanta Azure AD.

## <a name="trouble-seeing-all-subscriptions"></a>Problémy se zobrazením všech předplatných

Několik adresářů, které začaly používat skupiny pro správu na začátku ve verzi Preview před červenou 25 2018, by mohlo dojít k problému, kdy v rámci hierarchie nebyly všechna předplatná. Proces, který má mít všechna předplatná v hierarchii, se umístí po provedení přiřazení role nebo zásady v kořenové skupině pro správu v adresáři. 

### <a name="how-to-resolve-the-issue"></a>Jak tento problém vyřešit

Tento problém můžete vyřešit dvěma způsoby.

1. Odebrat všechna přiřazení rolí a zásad z kořenové skupiny pro správu
   1. Odebráním všech přiřazení zásad a rolí z kořenové skupiny pro správu bude služba zpětně naplnit všechna předplatná do hierarchie v nejbližším noci cyklu.  Tento proces je proto, že se nejedná o žádné náhodné přístupu nebo přiřazení zásad ke všem předplatným tenantů.
   1. Nejlepším způsobem, jak tento proces provést, aniž by to ovlivnilo vaše služby, je použití role nebo přiřazení zásady o jednu úroveň pod kořenovou skupinou pro správu. Pak můžete odebrat všechna přiřazení z kořenového oboru.
1. Volání rozhraní API přímo za účelem spuštění procesu naplnění
   1. Každý zákazník v adresáři může volat rozhraní API *TenantBackfillStatusRequest* nebo *StartTenantBackfillRequest* . Když se zavolá rozhraní StartTenantBackfillRequest API, zahájí se proces prvotního nastavení přesunu všech předplatných do hierarchie. Tento proces také spustí vynucení celého nového předplatného, které je podřízenou položkou kořenové skupiny pro správu. Tento postup je možné provést bez změny přiřazení na kořenové úrovni. Voláním rozhraní API zjistíte, že je to v pořádku, že jakékoli zásady nebo přiřazení přístupu v kořenovém adresáři lze použít pro všechna předplatná.

Pokud máte dotazy k tomuto procesu opětovného naplnění, obraťte se na: managementgroups@microsoft.com  
  
## <a name="management-group-access"></a>Přístup ke skupině pro správu

Skupiny pro správu Azure podporují [Access Control na základě rolí Azure (RBAC)](../../role-based-access-control/overview.md) pro všechny přístupy k prostředkům a definice rolí.
Tato oprávnění se dědí do podřízených prostředků, které v hierarchii existují. Do skupiny pro správu, která zdědí hierarchii na prostředky, se dá přiřadit jakákoli předdefinovaná role RBAC.
Například přispěvateli virtuálních počítačů role RBAC se dá přiřadit ke skupině pro správu. Tato role nemá ve skupině pro správu žádnou akci, ale zdědí na všechny virtuální počítače v této skupině pro správu.

Následující graf znázorňuje seznam rolí a podporované akce pro skupiny pro správu.

| Název role RBAC             | Create | Přejmenovat | Přesunout * * | Odstranit | Přiřadit přístup | Přiřadit zásady | Číst  |
|:-------------------------- |:------:|:------:|:------:|:------:|:-------------:| :------------:|:-----:|
|Vlastník                       | X      | X      | X      | X      | X             | X             | X     |
|Skupinou                 | X      | X      | X      | X      |               |               | X     |
|G Přispěvatel *             | X      | X      | X      | X      |               |               | X     |
|Modulu                      |        |        |        |        |               |               | X     |
|Čtečka MG *                  |        |        |        |        |               |               | X     |
|Přispěvatel zásad prostředků |        |        |        |        |               | X             |       |
|Správce přístupu uživatelů   |        |        |        |        | X             | X             |       |

*: MG čtenář a g Reader umožňují uživatelům provádět tyto akce pouze v oboru skupiny pro správu.  
\* *: Přiřazení rolí pro kořenovou skupinu pro správu se nevyžadují k přesunu předplatného nebo skupiny pro správu do a z ní.  Podrobnosti o přesouvání položek v hierarchii najdete v tématu [Správa prostředků pomocí skupin pro správu](manage.md) .

### <a name="custom-rbac-role-definition-and-assignment"></a>Vlastní definice role RBAC a přiřazení

Vlastní role RBAC se v tuto chvíli nepodporují u skupin pro správu. Pokud chcete zobrazit stav této položky, Projděte si [fórum pro názory na skupinu pro správu](https://aka.ms/mgfeedback) .

## <a name="audit-management-groups-using-activity-logs"></a>Auditovat skupiny pro správu pomocí protokolů aktivit

Skupiny pro správu jsou podporovány v [protokolu aktivit Azure](../../azure-monitor/platform/activity-logs-overview.md). Všechny události, ke kterým dochází ve skupině pro správu, můžete vyhledat ve stejném centrálním umístění jako ostatní prostředky Azure.  Můžete například zobrazit všechna přiřazení rolí nebo změny přiřazení zásad provedené v konkrétní skupině pro správu.

![Protokoly aktivit s Skupiny pro správu](media/al-mg.png)

Když hledáte dotaz na Skupiny pro správu mimo Azure Portal, cílový rozsah pro skupiny pro správu vypadá jako **"/Providers/Microsoft.Management/managementGroups/{yourMgID}"** .

## <a name="next-steps"></a>Další kroky

Další informace o skupinách pro správu najdete v těchto tématech:

- [Vytvoření skupin pro správu pro uspořádání prostředků Azure](create.md)
- [Jak změnit, odstranit nebo spravovat skupiny pro správu](manage.md)
- [Kontrola skupin pro správu v modulu Azure PowerShellch prostředků](/powershell/module/az.resources#resources)
- [Kontrola skupin pro správu v REST API](/rest/api/resources/managementgroups)
- [Kontrola skupin pro správu v Azure CLI](/cli/azure/account/management-group)
