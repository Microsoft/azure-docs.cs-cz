---
title: Vytváření konfigurací DSC v konfiguraci stavu automatizace Azure pomocí složených prostředků
description: Zjistěte, jak vytvořit konfigurace pomocí složených prostředků v konfiguraci stavu azure automatizace.
keywords: powershell dsc, požadovaná konfigurace stavu, powershell dsc azure, složené zdroje
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: 7e7625ae95d5355ae1122a16ea4828eed5f78c73
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682927"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-using-composite-resources"></a>Vytváření konfigurací DSC v konfiguraci stavu automatizace Azure pomocí složených prostředků

Pokud potřebujete spravovat prostředek s více než jednou konfigurací požadovaného stavu (DSC), nejlepší cestou je použití [složených prostředků](/powershell/scripting/dsc/resources/authoringresourcecomposite). Složený prostředek je vnořená a parametrizovaná konfigurace používaná jako prostředek DSC v rámci jiné konfigurace. Použití složených prostředků umožňuje vytvářet složité konfigurace a současně umožňovat individuální spravovanou a sestavěnou souženou a sebudovávanou základní složené prostředky.

Azure Automation umožňuje [import a kompilaci složených prostředků](automation-dsc-compile.md). Po importu složených prostředků do účtu Automation můžete použít konfiguraci stavu automatizace Azure pomocí **funkce Konfigurace stavu (DSC** na portálu Azure.

## <a name="composing-a-configuration-from-composite-resources"></a>Vytvoření konfigurace ze složených prostředků

Než budete moci přiřadit konfiguraci vyrobenou ze složených prostředků na webu Azure Portal, musíte ji vytvořit. Složení používá **konfiguraci compose** na stránce Konfigurace stavu (DSC) na **kartě Konfigurace** nebo **Kompilované konfigurace.**

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **Všechny prostředky** a potom na název účtu Automation.
1. Na stránce Účet automatizace vyberte **konfiguraci stavu (DSC)** v části **Správa konfigurace**.
1. Na stránce Konfigurace stavu (DSC) klikněte na kartu **Konfigurace** nebo **Zkompilované konfigurace** a v nabídce v horní části stránky klikněte na **Příkazka konfigurace.**
1. V kroku **Základy** zadejte nový název konfigurace (povinné) a klikněte na libovolné místo na řádku každého složeného prostředku, který chcete zahrnout do nové konfigurace, a potom klikněte na **další** nebo klikněte na krok **zdrojového kódu.** Pro následující kroky jsme `PSExecutionPolicy` `RenameAndDomainJoin` vybrali a složené zdroje.
   ![Snímek obrazovky s krokem základů konfigurační stránky compose](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. Krok **zdrojového kódu** ukazuje, jak vypadá složená konfigurace vybraných složených prostředků. Můžete vidět sloučení všech parametrů a způsob jejich předání složenému prostředku. Po dokončení kontroly nového zdrojového kódu klikněte na **další** nebo klikněte na krok **Parametry.**
   ![Snímek obrazovky s krokem zdrojového kódu na konfigurační stránce compose](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. V kroku Parametry je parametr pro každý složený prostředek vystaven tak, aby **mohly** být poskytnuty hodnoty. Pokud má parametr popis, zobrazí se vedle pole parametru. Pokud je parametr `PSCredential` typu, rozevírací seznam obsahuje seznam objektů **pověření** v aktuálním účtu automatizace. A **+ Přidat pověření** možnost je také k dispozici. Po zadání všech požadovaných parametrů klepněte na tlačítko **Uložit a zkompilovat**.
   ![Snímek obrazovky s krokem parametrů na konfigurační stránce compose](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

Jakmile je nová konfigurace uložena, je odeslána k kompilaci. Stav úlohy kompilace lze zobrazit jako libovolnou importovnou konfiguraci. Další informace naleznete [v tématu Zobrazení úlohy kompilace](automation-dsc-getting-started.md#viewing-a-compilation-job).

Po úspěšném dokončení kompilace se nová konfigurace zobrazí na kartě **Kompilované konfigurace.** Potom můžete přiřadit konfiguraci spravovanému uzlu pomocí kroků v [části Přeřazení uzlu do jiné konfigurace uzlu](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration).

## <a name="next-steps"></a>Další kroky

- Další informace najdete [v tématu Začínáme s azure automation state configuration](automation-dsc-getting-started.md).
- Další informace o tom, jak napalubě uzly, najdete [v tématu onboarding počítače pro správu pomocí konfigurace stavu automatizace Azure](automation-dsc-onboarding.md).
- Další informace o kompilaci konfigurací DSC, abyste je mohli přiřadit k cílovým uzlům, najdete [v tématu Kompilace konfigurací v konfiguraci stavu automatizace Azure](automation-dsc-compile.md).
- Odkaz na rutinu prostředí PowerShell najdete [v tématu Rutiny konfigurace stavu azure automatizace](/powershell/module/azurerm.automation/#automation).
- Informace o cenách najdete v [tématu Ceny konfigurace stavu Azure Automation](https://azure.microsoft.com/pricing/details/automation/).
- Příklad použití konfigurace stavu azure automatizace v kanálu průběžného nasazení najdete v [tématu průběžné nasazení pomocí konfigurace stavu automatizace Azure a Chocolatey](automation-dsc-cd-chocolatey.md).
