---
title: Vytváření konfigurací DSC v konfiguraci stavu Azure Automation pomocí složených prostředků
description: Naučte se vytvářet konfigurace pomocí složených prostředků v konfiguraci stavu Azure Automation.
keywords: PowerShell DSC, konfigurace požadovaného stavu, PowerShell DSC Azure, složené prostředky
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: 7e7625ae95d5355ae1122a16ea4828eed5f78c73
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81682927"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-using-composite-resources"></a>Vytváření konfigurací DSC v konfiguraci stavu Azure Automation pomocí složených prostředků

Pokud potřebujete spravovat prostředek s více než jednou konfigurací požadovaného stavu (DSC), nejlepším postupem je použití [složených prostředků](/powershell/scripting/dsc/resources/authoringresourcecomposite). Složený prostředek je vnořená a Parametrizovaná konfigurace, která se používá jako prostředek DSC v rámci jiné konfigurace. Použití složených prostředků vám umožní vytvořit složitou konfiguraci a zároveň umožnit, aby se základní složené prostředky jednotlivě spravovaly a vytvořily.

Azure Automation umožňuje [Import a kompilaci složených prostředků](automation-dsc-compile.md). Po importu složených prostředků do svého účtu Automation můžete použít konfiguraci Azure Automation stav prostřednictvím funkce **Konfigurace stavu (DSC** v Azure Portal.

## <a name="composing-a-configuration-from-composite-resources"></a>Sestavení konfigurace ze složených prostředků

Než budete moct přiřadit konfiguraci provedenou ze složených prostředků v Azure Portal, musíte vytvořit konfiguraci. Složení používá **konfiguraci vytváření** na stránce Konfigurace stavu (DSC), a to na kartě **Konfigurace** nebo **zkompilované konfigurace** .

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **všechny prostředky** a pak na název svého účtu Automation.
1. Na stránce účet Automation vyberte v části **Správa konfigurace**možnost **Konfigurace stavu (DSC)** .
1. Na stránce Konfigurace stavu (DSC) klikněte na kartu **Konfigurace** nebo **zkompilované konfigurace** a pak v nabídce v horní části stránky klikněte na **vytvořit konfiguraci** .
1. V kroku **základy** zadejte nový název konfigurace (požadováno) a klikněte kamkoli na řádek každého složeného prostředku, který chcete zahrnout do nové konfigurace, a potom klikněte na **Další** nebo klikněte na krok **zdrojového kódu** . V následujících krocích jsme vybrali `PSExecutionPolicy` a `RenameAndDomainJoin` složené prostředky.
   ![Snímek obrazovky kroku základy stránky konfigurace pro psaní](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. Krok **zdrojového kódu** ukazuje, jak vypadá složená konfigurace vybraných složených prostředků. Můžete zobrazit sloučení všech parametrů a jejich předání do složeného prostředku. Po dokončení kontroly nového zdrojového kódu klikněte na tlačítko **Další** nebo klikněte na krok **parametry** .
   ![Snímek obrazovky kroku zdrojového kódu stránky konfigurace pro psaní](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. V kroku **Parameters** je k dispozici parametr pro každý složený prostředek, aby bylo možné zadat hodnoty. Pokud má parametr popis, zobrazí se vedle pole parametru. Pokud je parametr `PSCredential` typu, zobrazuje rozevírací seznam objekty **přihlašovacích údajů** v aktuálním účtu Automation. K dispozici je také možnost **+ Přidat pověření** . Po zadání všech požadovaných parametrů klikněte na **Uložit a zkompilovat**.
   ![Snímek obrazovky s krokem parametrů na stránce konfigurace pro psaní](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

Po uložení nové konfigurace je odeslána pro kompilaci. Stav úlohy kompilace můžete zobrazit stejně jako všechny importované konfigurace. Další informace naleznete v tématu [zobrazení úlohy kompilace](automation-dsc-getting-started.md#viewing-a-compilation-job).

Po úspěšném dokončení kompilace se nová konfigurace zobrazí na kartě **zkompilované konfigurace** . Pak můžete konfiguraci přiřadit ke spravovanému uzlu pomocí kroků v části [Změna přiřazení uzlu k jiné konfiguraci uzlu](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration).

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak začít, najdete v tématu [Začínáme s konfigurací stavu Azure Automation](automation-dsc-getting-started.md).
- Další informace o připojování uzlů najdete v tématu věnovaném [připojování počítačů ke správě podle konfigurace stavu Azure Automation](automation-dsc-onboarding.md).
- Další informace o kompilaci konfigurací DSC, abyste je mohli přiřadit cílovým uzlům, najdete v tématu [kompilace konfigurací v konfiguraci stavu Azure Automation](automation-dsc-compile.md).
- Referenční informace k rutinám PowerShellu najdete v tématu [rutiny konfigurace stavu Azure Automation](/powershell/module/azurerm.automation/#automation).
- Informace o cenách najdete v tématu [Azure Automation ceny konfigurace stavu](https://azure.microsoft.com/pricing/details/automation/).
- Příklad použití konfigurace stavu Azure Automation v kanálu průběžného nasazování najdete v tématu [průběžné nasazování pomocí konfigurace Azure Automation stavu a čokolády](automation-dsc-cd-chocolatey.md).
