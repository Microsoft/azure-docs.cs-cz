---
title: Uspořádání prostředků s skupin pro správu Azure | Microsoft Docs
description: Další informace o skupin pro správu a jejich použití.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2018
ms.author: rithorn
ms.openlocfilehash: 55203a9a64184b43d0fac898180b43d4e504d969
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Uspořádání prostředků s skupin pro správu Azure 

Pokud má vaše organizace mnoho odběrů, musíte způsob, jak efektivně spravovat přístup, zásady a dodržování předpisů pro tyto odběry. Skupiny pro správu Azure poskytuje úroveň oboru výše odběry. Uspořádání odběrů do kontejnerů názvem "skupin pro správu" a použít vaše podmínky zásad správného řízení ke skupinám pro správu. Všechny odběry v rámci skupiny pro správu automaticky převezmou podmínky pro skupinu pro správu. Skupiny pro správu poskytují podnikové úrovni správy ve velkém měřítku bez ohledu na to, jaký typ předplatné můžete mít.

Funkce skupiny správy je dostupná ve verzi public preview. Chcete začít používat správu skupin, přihlášení, které [portál Azure](https://portal.azure.com) a vyhledejte **skupin pro správu** v **všechny služby** části. 

Jako příklad můžete používat zásady do skupiny pro správu, který omezuje oblastí, které jsou k dispozici pro vytvoření virtuálního počítače (VM). Tato zásada by bylo možné provést pro všechny skupiny pro správu, odběry a prostředky v této skupině pro správu tím, že se jenom virtuální počítače vytvořené v této oblasti.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Hierarchie skupin pro správu a odběry 

Můžete vytvořit flexibilní strukturu skupin pro správu a odběry, které slouží k uspořádání prostředků do hierarchie jednotná zásad a správu přístupu. Následující diagram znázorňuje příklad hierarchie, která se skládá ze skupiny pro správu a odběry uspořádané podle oddělení.    

![strom](media/management-groups/MG_overview.png)

Vytvořením hierarchie, která je seskupené podle oddělení, budete moci přiřadit [řízení řízení přístupu (RBAC)](../role-based-access-control/overview.md) role, *dědění* k oddělení pod tuto skupinu pro správu. Pomocí skupin pro správu, můžete snížit vaše úlohy a snižuje riziko chyby tak, že pouze jednou přiřadit role. 

### <a name="important-facts-about-management-groups"></a>Důležité informace o skupinách správy
- 10 000 skupin pro správu může být podporováno v jednom adresáři. 
- Větev skupiny správy může podporovat až šest úrovní hloubka.
    - Tento limit nezahrnuje úrovni kořenového adresáře nebo na úrovni předplatného.
- Každou skupinu pro správu podporuje pouze jednu nadřazenou položku.
- Každou skupinu pro správu může mít více podřízených položek. 

### <a name="preview-subscription-visibility-limitation"></a>Omezení viditelnosti předplatné Preview 
Není aktuálně ve verzi preview, kde nejsou zobrazit předplatná, která mají zděděná přístup k omezení. Zdědí přístup k předplatnému, ale není moct respektovat přístup dědičnosti ještě Azure Resource Manager.  

Pomocí rozhraní REST API informace o odběru vrátí podrobnosti, jak máte přístup, ale v rámci portálu Azure a prostředí Azure Powershell předplatná nezobrazovat. 

Tato položka je pracuje a vyřeší před skupin pro správu jsou oznámeno jako "Obecné dostupnosti."  

### <a name="cloud-solution-providercsp-limitation-during-preview"></a>Cloudové řešení Provider(CSP) omezení během Preview 
Neexistuje aktuální omezení pro cloudové řešení Provider(CSP) partnery, kde nejsou vytvářet a spravovat skupiny pro správu jejich zákazníka v adresáři zákazníka.  
Tato položka je pracuje a vyřeší před skupin pro správu jsou oznámeno jako "Obecné dostupnosti."


## <a name="root-management-group-for-each-directory"></a>Skupina pro správu kořenového pro každý adresář

Každý adresář dostane nejvyšší úrovně správy skupinu s názvem "Root" skupina pro správu. Tuto skupinu pro správu kořenového je integrovaná do hierarchie, které chcete mít všechny skupiny pro správu a odběry přeložte do ní. Tuto skupinu pro správu kořenového umožňuje globální zásady a přiřazení RBAC má být použita na úrovni adresáře. [Directory správce potřebuje ke zvýšení oprávnění sami](../role-based-access-control/elevate-access-global-admin.md) jako vlastník této kořenové skupiny původně. Jakmile správce vlastník skupiny, budou všechny role RBAC přiřadit jiné adresáře uživatele nebo skupiny pro správu v hierarchii.  

### <a name="important-facts-about-the-root-management-group"></a>Důležité skutečnosti o skupině pro správu kořenové
- Název a ID skupiny pro správu kořenového mají Azure Active Directory ID ve výchozím nastavení. Zobrazovaný název lze aktualizovat kdykoli zobrazit jiné v rámci portálu Azure. 
- Všechny odběry a skupin pro správu můžete ukončit do skupiny pro správu jeden kořenový v adresáři.  
    - Doporučuje se mít všechny položky v adresáři násobek až skupinu Root management pro globální správu.  
    - Ve veřejné verzi Preview nejsou všechny odběry v adresáři automaticky provedeny podřízené kořenové.   
    - Ve veřejné verzi Preview nová předplatná nejsou automaticky uvedena do výchozího stavu skupiny pro správu Root. 
- Skupina pro správu kořenového se nedá přesunout ani odstranit, na rozdíl od jiných skupin pro správu. 
  
## <a name="management-group-access"></a>Skupina pro správu přístupu

Podporuje Azure skupin pro správu [řízení řízení přístupu (RBAC)](../role-based-access-control/overview.md) pro všechny přístupy prostředků a definice rolí. Tato oprávnění zdědí na podřízené prostředky, které existují v hierarchii.   

Při žádné [předdefinovaná role RBAC](../role-based-access-control/overview.md#built-in-roles) lze přiřadit ke skupině pro správu, jsou čtyři role, které se běžně používají: 
- **Vlastník** má úplný přístup ke všem prostředkům, včetně právo delegovat přístup k ostatním. 
- **Přispěvatel** můžete vytvářet a spravovat všechny typy prostředků Azure, ale nelze udělit přístup ostatním uživatelům.
- **Přispěvatel zásad prostředků** můžete vytvořit a spravovat zásady v adresáři na prostředky.     
- **Čtečka** můžete zobrazit stávající prostředky Azure. 


## <a name="next-steps"></a>Další postup 
Další informace o skupinách správy najdete v tématu: 
- [Vytvoření skupiny pro správu k uspořádání prostředků Azure](management-groups-create.md)
- [Jak změnit, odstranit nebo spravovat skupiny pro správu](management-groups-manage.md)
- [Instalace modulu Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Zkontrolujte specifikace rozhraní API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)
- [Nainstalujte rozšíření rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)

