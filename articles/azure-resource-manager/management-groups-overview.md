---
title: Uspořádání prostředků se skupinami pro správu Azure | Microsoft Docs
description: Další informace o skupin pro správu a jejich použití.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/28/2018
ms.author: rithorn
ms.openlocfilehash: 611faef7e4b94b1734896fb64ca29540b12bc057
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102215"
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Uspořádání prostředků se skupinami pro správu Azure

Pokud má vaše organizace mnoho odběrů, musíte způsob, jak efektivně spravovat přístup, zásady a dodržování předpisů pro tyto odběry. Skupiny pro správu Azure zadejte úroveň oboru výše odběry. Uspořádání odběrů do kontejnerů názvem "skupin pro správu" a použít vaše podmínky zásad správného řízení ke skupinám pro správu. Všechny odběry v rámci skupiny pro správu automaticky převezmou podmínky pro skupinu pro správu. Skupiny pro správu poskytují podnikové úrovni správy ve velkém měřítku bez ohledu na to, jaký typ předplatné můžete mít.

Funkce skupiny správy je dostupná ve verzi public preview. K použití skupin pro správu, přihlaste se k [portál Azure](https://portal.azure.com) a vyhledejte **skupin pro správu** v **všechny služby** části.

Například můžete používat zásady do skupiny pro správu, který omezuje oblastí, které jsou k dispozici pro vytvoření virtuálního počítače (VM). Tato zásada by bylo možné provést pro všechny skupiny pro správu, odběry a prostředky v této skupině pro správu tím, že se jenom virtuální počítače vytvořené v této oblasti.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Hierarchie skupin pro správu a odběry

Můžete vytvořit flexibilní strukturu skupin pro správu a odběry, které slouží k uspořádání prostředků do hierarchie jednotná zásad a správu přístupu.
Následující diagram znázorňuje příklad hierarchie, která se skládá ze skupiny pro správu a odběry uspořádané podle oddělení.

![Strom](media/management-groups/MG_overview.png)

Vytvořením hierarchie, která je seskupené podle oddělení, můžete přiřadit [řízení řízení přístupu (RBAC)](../role-based-access-control/overview.md) role, *dědění* k oddělení pod tuto skupinu pro správu. Pomocí skupin pro správu, můžete snížit vaše úlohy a snižuje riziko chyby tak, že pouze jednou přiřadit role.

### <a name="important-facts-about-management-groups"></a>Důležité informace o skupinách správy

- 10 000 skupin pro správu může být podporováno v jednom adresáři.
- Větev skupiny správy může podporovat až šest úrovní hloubka.
  - Tento limit neobsahuje úrovni kořenového adresáře nebo na úrovni předplatného.
- Každou skupinu pro správu a předplatné podporuje pouze jednu nadřazenou položku.
- Každou skupinu pro správu může mít více podřízených položek.
- Všechny odběry a skupin pro správu jsou obsaženy v jedné hierarchii v každý adresář. V tématu [důležitých faktů o skupině pro správu kořenového](#important-facts-about-the-root-management-group) pro výjimky ve verzi Preview.

### <a name="preview-subscription-visibility-limitation"></a>Omezení viditelnosti předplatné Preview

Není aktuálně ve verzi preview, kde nejsou zobrazit předplatná, která mají zděděná přístup k omezení. Zdědí přístup k předplatnému, ale není moct respektovat přístup dědičnosti ještě Azure Resource Manager.  

Pomocí rozhraní REST API informace o odběru vrátí podrobnosti, jak máte přístup, ale v rámci portálu Azure a prostředí Azure Powershell předplatná nezobrazovat.

Tato položka je pracuje a bude se vyřešit před správy skupin jsou oznámeno jako "Obecné dostupnosti."  

### <a name="cloud-solution-provider-csp-limitation-during-preview"></a>Cloud Solution Provider (CSP) omezení během Preview

Neexistuje aktuální omezení pro partnery Cloud Solution Provider (CSP), kde nejsou vytvářet a spravovat skupiny pro správu jejich zákazníka v adresáři zákazníka.  
Tato položka je pracuje a bude se vyřešit před správy skupin jsou oznámeno jako "Obecné dostupnosti."

## <a name="root-management-group-for-each-directory"></a>Skupina pro správu kořenového pro každý adresář

Každý adresář dostane nejvyšší úrovně správy skupinu s názvem "Root" skupina pro správu. Tuto skupinu pro správu kořenového je integrovaná do hierarchie, které chcete mít všechny skupiny pro správu a odběry přeložte do ní. Tuto skupinu pro správu kořenového umožňuje globální zásady a přiřazení RBAC má být použita na úrovni adresáře. [Directory správce potřebuje ke zvýšení oprávnění sami](../role-based-access-control/elevate-access-global-admin.md) jako vlastník této kořenové skupiny původně. Jakmile správce vlastník skupiny, budou všechny role RBAC přiřadit jiné adresáře uživatele nebo skupiny pro správu v hierarchii.  

### <a name="important-facts-about-the-root-management-group"></a>Důležité skutečnosti o skupině pro správu kořenové

- Název a ID skupiny pro správu kořenového jsou uvedeny ve výchozím nastavení. Zobrazovaný název lze aktualizovat kdykoli zobrazit jiné v rámci portálu Azure.
  - Název bude "Skupina kořenové klienta".
  - ID bude ID Azure Active Directory.
- Skupina pro správu kořenového se nedá přesunout ani odstranit, na rozdíl od jiných skupin pro správu.  
- Všechny odběry a skupin pro správu ukončit do skupiny pro správu jeden kořenový v adresáři.
  - Všechny prostředky v adresáři ukončit do skupiny pro správu kořenového pro globální správu.
  - Nová předplatná se automaticky uvedena do výchozího stavu skupiny pro správu kořenového při vytvoření.
- Všechny Azure zákazníci mohou zobrazit skupinu root management, ale ne všechny zákazníci mají přístup ke správě tuto skupinu pro správu kořenové.
  - Každý, kdo má přístup k odběru můžete zobrazit kontextu kde toto předplatné je v hierarchii.  
  - Výchozí úroveň přístupu nikdo je uveden do kořenové skupiny správy. Globální správce adresáře jsou pouze uživatelé, kteří může zvýšit nároky sami získat přístup.  Jakmile budou mít přístup, můžete správci directory přiřadit všechny role RBAC ostatním uživatelům ke správě.  

>[!NOTE]
>Pokud adresáře spustili pomocí služby skupiny pro správu před 6/25/2018, nemusí být nastavit adresáře se všechny odběry v hierarchii. Tým správy skupiny zpětně aktualizuje každý adresář, který pomocí skupin pro správu ve verzi Public Preview před toto datum v rámci července 2018 spuštěna. Všechny odběry v adresáři, bude se podřízené objekty v rámci skupiny pro správu kořenového předchozí.  
>
>Pokud máte dotazy na tento proces zpětnou, obraťte se na: managementgroups@microsoft.com  
  
## <a name="initial-setup-of-management-groups"></a>Počáteční nastavení skupin pro správu

Pokud každý uživatel, spustí při použití skupin pro správu, je počáteční nastavení proces, který se stane. Prvním krokem je že vytvoření skupiny pro správu kořenového v adresáři. Po vytvoření této skupiny jsou vytvářeny všechny existující odběry, které existují v adresáři podřízené kořenové skupiny pro správu.  Z důvodu pro tento proces je a ujistěte se, že existuje jenom jedna hierarchie skupiny správy v rámci adresáře.  Jedné hierarchii v adresáři umožňuje správu zákazníkům použít globální přístup a zásady, které se zákazníci navzájem v adresáři nedá obejít. NIC přiřazené v kořenovém uplatní na všech skupin pro správu, odběry, skupiny prostředků a prostředky v rámci adresáře tak, že jedna hierarchie v adresáři.  

> [!IMPORTANT]
> Všechny přiřazení přístupu nebo zásady přiřazení uživatelů na skupinu root management **se vztahuje na všechny prostředky v rámci adresáře**. Z tohoto důvodu by měly vyhodnotit všem zákazníkům nutné, abyste měli položky definované v tomto rozsahu.  Přiřazení přístupu a zásady uživatele by měla být "Musí mít" pouze na tento obor.  
  
## <a name="management-group-access"></a>Přístup skupiny pro správu

Skupin pro správu Azure podporu [řízení řízení přístupu (RBAC)](../role-based-access-control/overview.md) pro všechny přístupy prostředků a definice rolí. Tato oprávnění zdědí na podřízené prostředky, které existují v hierarchii. Žádné předdefinované role RBAC lze přiřadit ke skupině pro správu, který zdědí dolů hierarchii k prostředkům.  Například Přispěvatel RBAC role virtuálních počítačů lze přiřadit ke skupině pro správu. Tato role nemá žádnou akci skupiny pro správu, ale bude dědit do všech virtuálních počítačů v rámci této skupiny pro správu.  

Následující graf zobrazuje seznam rolí a podporovaných akcí na skupiny pro správu.

| Název Role RBAC             | Vytvořit | Přejmenovat | Přesunout | Odstranění | Přiřadit přístup pro | Přiřadit zásady | Čtení  |
|:-------------------------- |:------:|:------:|:----:|:------:|:-------------:| :------------:|:-----:|
|Vlastník                       | X      | X      | X    | X      | X             |               | X     |
|Přispěvatel                 | X      | X      | X    | X      |               |               | X     |
|Čtenář                      |        |        |      |        |               |               | X     |
|Přispěvatel zásad prostředků |        |        |      |        |               | X             |       |
|Správce přístupu uživatelů   |        |        |      |        | X             |               |       |

### <a name="custom-rbac-role-definition-and-assignment"></a>Definice Role vlastní RBAC a přiřazení

Vlastní role RBAC nepodporuje skupiny pro správu v tuto chvíli.  Najdete v článku [fóru pro zpětnou vazbu skupiny správy](https://aka.ms/mgfeedback) zobrazíte stav této položky.

## <a name="next-steps"></a>Další postup

Další informace o skupinách správy najdete v tématu:

- [Vytvoření skupiny pro správu k uspořádání prostředků Azure](management-groups-create.md)
- [Jak změnit, odstranit nebo spravovat skupiny pro správu](management-groups-manage.md)
- [Instalace modulu Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Zkontrolujte specifikace rozhraní API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)
- [Nainstalujte rozšíření rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)
