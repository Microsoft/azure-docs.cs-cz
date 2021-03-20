---
title: Sestavování konfigurací DSC
description: Tento článek popisuje, jak vytvářet konfigurace pomocí složených prostředků v konfiguraci stavu Azure Automation.
keywords: PowerShell DSC, konfigurace požadovaného stavu, PowerShell DSC Azure, složené prostředky
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: 1b1bbb12412deec6ecac8cf1ffd47a00f778862e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98894724"
---
# <a name="compose-dsc-configurations"></a>Sestavování konfigurací DSC

Pokud potřebujete spravovat prostředek s více než jednou konfigurací požadovaného stavu (DSC), nejlepším postupem je použití [složených prostředků](/powershell/scripting/dsc/resources/authoringresourcecomposite). Složený prostředek je vnořená a Parametrizovaná konfigurace, která se používá jako prostředek DSC v rámci jiné konfigurace. Použití složených prostředků vám umožní vytvořit složitou konfiguraci a zároveň umožnit, aby se základní složené prostředky jednotlivě spravovaly a vytvořily.

Azure Automation umožňuje [Import a kompilaci složených prostředků](automation-dsc-compile.md). Po importu složených prostředků do svého účtu Automation můžete použít konfiguraci Azure Automation stav prostřednictvím funkce **Konfigurace stavu (DSC)** v Azure Portal.

## <a name="compose-a-configuration"></a>Vytvoření konfigurace

Než budete moct přiřadit konfiguraci provedenou ze složených prostředků v Azure Portal, musíte vytvořit konfiguraci. Složení používá **konfiguraci vytváření** na stránce Konfigurace stavu (DSC), a to na kartě **Konfigurace** nebo **zkompilované konfigurace** .

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Na levé straně klikněte na **všechny prostředky** a pak na název svého účtu Automation.
1. Na stránce účet Automation vyberte v části **Správa konfigurace** možnost **Konfigurace stavu (DSC)** .
1. Na stránce Konfigurace stavu (DSC) klikněte na kartu **Konfigurace** nebo **zkompilované konfigurace** a pak v nabídce v horní části stránky klikněte na **vytvořit konfiguraci** .
1. V kroku **základy** zadejte nový název konfigurace (požadováno) a klikněte kamkoli na řádek každého složeného prostředku, který chcete zahrnout do nové konfigurace, a potom klikněte na **Další** nebo klikněte na krok **zdrojového kódu** . V následujících krocích jsme vybrali `PSExecutionPolicy` a `RenameAndDomainJoin` složené prostředky.
   ![Snímek obrazovky kroku základy stránky konfigurace pro psaní](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. Krok **zdrojového kódu** ukazuje, jak vypadá složená konfigurace vybraných složených prostředků. Můžete zobrazit sloučení všech parametrů a jejich předání do složeného prostředku. Po dokončení kontroly nového zdrojového kódu klikněte na tlačítko **Další** nebo klikněte na krok **parametry** .
   ![Snímek obrazovky kroku zdrojového kódu stránky konfigurace pro psaní](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. V kroku **Parameters** je k dispozici parametr pro každý složený prostředek, aby bylo možné zadat hodnoty. Pokud má parametr popis, zobrazí se vedle pole parametru. Pokud je parametr `PSCredential` typu, zobrazuje rozevírací seznam objekty **přihlašovacích údajů** v aktuálním účtu Automation. K dispozici je také možnost **+ Přidat pověření** . Po zadání všech požadovaných parametrů klikněte na **Uložit a zkompilovat**.
   ![Snímek obrazovky s krokem parametrů na stránce konfigurace pro psaní](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

## <a name="submit-the-configuration-for-compilation"></a>Odeslat konfiguraci pro kompilaci

Po uložení nové konfigurace je odeslána pro kompilaci. Stav úlohy kompilace můžete zobrazit stejně jako u všech importovaných konfigurací. Další informace naleznete v tématu [zobrazení úlohy kompilace](automation-dsc-getting-started.md#view-a-compilation-job).

Po úspěšném dokončení kompilace se nová konfigurace zobrazí na kartě **zkompilované konfigurace** . Pak můžete konfiguraci přiřadit ke spravovanému uzlu pomocí kroků v části [Změna přiřazení uzlu k jiné konfiguraci uzlu](automation-dsc-getting-started.md#reassign-a-node-to-a-different-node-configuration).

## <a name="next-steps"></a>Další kroky

- Další informace o povolení uzlů najdete v tématu [povolení konfigurace stavu Azure Automation](automation-dsc-onboarding.md).
- Další informace o kompilaci konfigurací DSC, abyste je mohli přiřadit cílovým uzlům, najdete v tématu [kompilace konfigurací DSC v konfiguraci stavu Azure Automation](automation-dsc-compile.md).
- Příklad použití konfigurace stavu Azure Automation v kanálu nepřetržitého nasazení najdete v tématu [Nastavení průběžného nasazování s čokoládou](automation-dsc-cd-chocolatey.md).
- Informace o cenách najdete v tématu [Azure Automation ceny konfigurace stavu](https://azure.microsoft.com/pricing/details/automation/).
- Referenční informace k rutinám PowerShellu najdete v tématu [AZ. Automation](/powershell/module/az.automation).
