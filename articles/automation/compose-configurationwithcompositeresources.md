---
title: Vytvoření konfigurace DSC v Azure Automation stavu Configuration (DSC) pomocí složené prostředky
description: Zjistěte, jak vytvořit konfigurace pomocí složené prostředky v Azure Automation stavu Configuration (DSC)
keywords: PowerShell dsc, konfigurace požadovaného stavu prostředí powershell dsc azure složené prostředky
services: automation
ms.service: automation
ms.component: dsc
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/21/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a1297b6cd84a012ff54f7d902c92687b2e284fe2
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45607777"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-dsc-using-composite-resources"></a>Vytvoření konfigurace DSC v Azure Automation stavu Configuration (DSC) pomocí složené prostředky

Pokud prostředek potřebuje pro správu ve více než jeden požadovaný stav konfigurace (DSC) konfigurace, je použít nejlepší cestu [složené prostředky](/powershell/dsc/authoringresourcecomposite). Složený prostředek je používán jako prostředek DSC v rámci jiné konfigurace vnořené a parametry konfigurace. To umožňuje vytvoření složitých konfigurací zároveň umožní základní složené prostředky (parametry konfigurace) samostatně spravovat a integrované.

Azure Automation umožňuje [importu a kompilace složené prostředky](automation-dsc-compile.md#composite-resources). Složené prostředky po naimportování do účtu Automation, budete moct používat **psaní konfigurace** prostředí v **konfigurace stavu (DSC)** stránky.

## <a name="composing-a-configuration-from-composite-resources"></a>Vytváření konfiguraci z složené prostředky

Předtím, než můžete přiřadit konfiguraci z složené prostředky na webu Azure Portal, musíte ji vytvořit. To lze provést pomocí **psaní konfigurace** na **konfigurace stavu (DSC)** stránky na buď **konfigurace** nebo **zkompilované konfigurace** karty.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na tlačítko **všechny prostředky** a potom na název vašeho účtu Automation.
1. Na **účtu Automation** stránce **konfigurace stavu (DSC)** pod **Configuration Management**.
1. Na **konfigurace stavu (DSC)** klikněte buďto **konfigurace** nebo **kompilaci konfigurace** kartu a potom klikněte na **psaní konfigurace**  v nabídce v horní části stránky.
1. Na **Základy** kroku, zadejte nový název konfigurace (povinné) a klikněte na libovolné místo v řadě jednotlivé složené prostředky, které chcete zahrnout do nové konfigurace a pak klikněte na **Další** nebo klikněte **Zdrojový kód** kroku. Následující postup, jsme vybrali **PSExecutionPolicy** a **RenameAndDomainJoin** složené prostředky.
   ![Snímek obrazovky krok základní informace o nové konfigurační stránce](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. **Zdrojový kód** krok ukazuje, jak vypadá složené konfiguraci vybraných složené prostředky. Můžete zobrazit sloučení všech parametrů a jak se předávají do složeného prostředku. Po dokončení revizi nového zdrojového kódu, klikněte na tlačítko **Další** nebo klikněte na tlačítko **parametry** kroku.
   ![Snímek obrazovky krok zdrojový kód nové konfigurační stránce](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. Na **parametry** kroku parametr, který má každý složený prostředek je přístupný tak, že je možné poskytnout. Pokud parametr obsahuje popis, zobrazí se vedle pole parametrů. Pokud je pole **PSCredential** parametr typu, rozevíracího seznamu konfigurace obsahuje seznam **přihlašovacích údajů** objekty v aktuálním účtu Automation. A **+ přidat přihlašovací údaj** možnost je také k dispozici. Jakmile se všechny požadované parametry byly zadány, klikněte na tlačítko **uložte a zkompilujte**.
   ![Snímek obrazovky se parametry kroku konfigurační stránce compose](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

Po uložení nové konfigurace se odešle ke kompilaci. Stejně jako všechny importované konfigurace si můžete zobrazit stav úlohy kompilace. Další informace najdete v tématu [zobrazení úloha kompilace](automation-dsc-getting-started.md#viewing-a-compilation-job).

Při kompilaci byla úspěšně dokončena, aby se nová konfigurace se zobrazí v **kompilaci konfigurace** kartu. Jakmile je viditelný na této kartě, může být přiřazena k uzlu spravované pomocí postupu v [přiřazení uzlu do jiného uzlu Konfigurace](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration).

## <a name="next-steps"></a>Další postup

- Abyste mohli začít, najdete v článku [Začínáme s Azure Automation stavu konfigurace](automation-dsc-getting-started.md)
- Další informace jak připojit uzlů najdete v článku [připojování počítačů pro správu podle konfigurace stavu služby Azure Automation](automation-dsc-onboarding.md)
- Další informace o kompilaci konfigurace DSC, takže můžete je přiřadit k cílové uzly, naleznete v tématu [kompilace konfigurací v konfiguraci stavu služby Azure Automation](automation-dsc-compile.md)
- Reference k rutinám Powershellu najdete v části [rutiny Azure Automation stavu konfigurace](/powershell/module/azurerm.automation/#automation)
- Informace o cenách najdete v tématu [ceny konfigurace stavu služby Azure Automation](https://azure.microsoft.com/pricing/details/automation/)
- Příklad použití Azure Automation stav konfigurace v kanálu průběžného nasazování najdete v tématu [nepřetržité nasazení pomocí Azure Automation konfigurace stavu a Chocolatey](automation-dsc-cd-chocolatey.md)