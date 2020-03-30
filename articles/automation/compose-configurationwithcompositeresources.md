---
title: Vytváření konfigurací DSC v konfiguraci stavu automatizace Azure (DSC) pomocí složených prostředků
description: Zjistěte, jak vytvořit konfigurace pomocí složených prostředků v konfiguraci stavu automatizace Azure (DSC)
keywords: powershell dsc, požadovaná konfigurace stavu, powershell dsc azure, složené zdroje
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: e5083ec55ee0a57cd7defd466f5baf1704336320
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77370669"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-dsc-using-composite-resources"></a>Vytváření konfigurací DSC v konfiguraci stavu automatizace Azure (DSC) pomocí složených prostředků

Pokud je třeba prostředek spravovat s více než jednou konfigurací konfigurace požadovaného stavu (DSC), je nejlepší cestou použití [složených prostředků](/powershell/scripting/dsc/resources/authoringresourcecomposite). Složený prostředek je vnořená a parametrizovaná konfigurace používaná jako prostředek DSC v rámci jiné konfigurace. To umožňuje vytváření složitých konfigurací a zároveň umožňuje základní složené prostředky (parametrizované konfigurace) být individuálně spravovány a sestaveny.

Azure Automation umožňuje [import a kompilaci složených prostředků](automation-dsc-compile.md).
Po importu složených prostředků do účtu automatizace můžete použít prostředí **konfigurace compose** na stránce **Konfigurace stavu (DSC).**

## <a name="composing-a-configuration-from-composite-resources"></a>Vytvoření konfigurace ze složených prostředků

Než budete moci přiřadit konfiguraci vyrobenou ze složených prostředků na webu Azure Portal, musíte ji vytvořit. To lze provést pomocí **konfigurace compose** na stránce **Konfigurace stavu (DSC)** na kartách **Konfigurace** nebo **Kompilované konfigurace.**

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Na levé straně klikněte na **Všechny prostředky** a potom na název účtu Automation.
1. Na stránce **Účet automatizace** vyberte **konfiguraci stavu (DSC)** v části **Správa konfigurace**.
1. Na stránce **Konfigurace stavu (DSC)** klikněte na kartu **Konfigurace** nebo **Zkompilované konfigurace** a potom v nabídce v horní části stránky klikněte na **Příkazka konfigurace.**
1. V kroku **Základy** zadejte nový název konfigurace (povinné) a klikněte na libovolné místo na řádku každého složeného prostředku, který chcete zahrnout do nové konfigurace, a potom klikněte na **další** nebo klikněte na krok **zdrojového kódu.** Pro následující kroky jsme vybrali **PSExecutionPolicy** a **RenameAndDomainJoin** složené prostředky.
   ![Snímek obrazovky s krokem základů konfigurační stránky compose](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. Krok **zdrojového kódu** ukazuje, jak vypadá složená konfigurace vybraných složených prostředků. Můžete vidět sloučení všech parametrů a způsob jejich předání složenému prostředku. Po dokončení kontroly nového zdrojového kódu klikněte na **další** nebo klikněte na krok **Parametry.**
   ![Snímek obrazovky s krokem zdrojového kódu na konfigurační stránce compose](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. V **kroku Parametry** je parametr, který má každý složený prostředek, vystaven tak, aby mohly být poskytnuty. Pokud má parametr popis, zobrazí se vedle pole parametru. Pokud je pole parametrem typu **PSCredential,** rozevírací seznam pro konfiguraci poskytuje seznam objektů **pověření** v aktuálním účtu automatizace. A **+ Přidat pověření** možnost je také k dispozici. Po zadání všech požadovaných parametrů klepněte na tlačítko **Uložit a zkompilovat**.
   ![Snímek obrazovky s krokem parametrů na konfigurační stránce compose](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

Jakmile je nová konfigurace uložena, je odeslána k kompilaci. Stav úlohy kompilace lze zobrazit jako libovolnou importovnou konfiguraci. Další informace naleznete [v tématu Zobrazení úlohy kompilace](automation-dsc-getting-started.md#viewing-a-compilation-job).

Po úspěšném dokončení kompilace se nová konfigurace zobrazí na kartě **Kompilované konfigurace.** Jakmile je na této kartě viditelná, lze ji přiřadit spravovanému uzlu pomocí kroků v části [Přeřazování uzlu do jiné konfigurace uzlu](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration).

## <a name="next-steps"></a>Další kroky

- Další informace najdete v tématu [Začínáme s konfigurací stavu automatizace Azure](automation-dsc-getting-started.md)
- Informace o tom, jak napalubě uzlů, najdete [v tématu Onboarding ové počítače pro správu pomocí konfigurace stavu automatizace Azure](automation-dsc-onboarding.md)
- Další informace o kompilaci konfigurací DSC, abyste je mohli přiřadit k cílovým uzlům, najdete [v tématu Kompilace konfigurací v konfiguraci stavu automatizace Azure](automation-dsc-compile.md)
- Informace o odkazu na rutinu prostředí PowerShell najdete v tématu [Rutiny konfigurace stavu automatizace Azure](/powershell/module/azurerm.automation/#automation)
- Informace o cenách najdete v [tématu Ceny konfigurace stavu Azure Automation](https://azure.microsoft.com/pricing/details/automation/)
- Příklad použití konfigurace stavu azure automatizace v kanálu průběžného nasazení najdete v [tématu Průběžné nasazení pomocí konfigurace stavu automatizace Azure a Chocolatey](automation-dsc-cd-chocolatey.md)
