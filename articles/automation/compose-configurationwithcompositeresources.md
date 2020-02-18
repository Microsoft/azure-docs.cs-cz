---
title: Sestavování konfigurací DSC v konfiguraci stavu Azure Automation (DSC) pomocí složených prostředků
description: Naučte se vytvářet konfigurace pomocí složených prostředků v konfiguraci stavu Azure Automation (DSC).
keywords: PowerShell DSC, konfigurace požadovaného stavu, PowerShell DSC Azure, složené prostředky
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: e5083ec55ee0a57cd7defd466f5baf1704336320
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2020
ms.locfileid: "77370669"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-dsc-using-composite-resources"></a>Sestavování konfigurací DSC v konfiguraci stavu Azure Automation (DSC) pomocí složených prostředků

Pokud je potřeba prostředek spravovat s více než jednou konfigurací požadovaného stavu konfigurace (DSC), nejlepším řešením je použití [složených prostředků](/powershell/scripting/dsc/resources/authoringresourcecomposite). Složený prostředek je vnořená a Parametrizovaná konfigurace, která se používá jako prostředek DSC v rámci jiné konfigurace. To umožňuje vytváření složitých konfigurací a zároveň umožňuje, aby byly základní složené prostředky (parametrizované konfigurace) jednotlivě spravovány a sestaveny.

Azure Automation umožňuje [Import a kompilaci složených prostředků](automation-dsc-compile.md).
Po importu složených prostředků do účtu Automation můžete na stránce **Konfigurace stavu (DSC)** použít prostředí pro **vytváření konfigurace** .

## <a name="composing-a-configuration-from-composite-resources"></a>Sestavení konfigurace ze složených prostředků

Než budete moct přiřadit konfiguraci provedenou ze složených prostředků v Azure Portal, je nutné ji vytvořit. To lze provést pomocí **Konfigurace psaní** na stránce **Konfigurace stavu (DSC)** , a to na kartách **Konfigurace** nebo **zkompilované konfigurace** .

1. Přihlaste se k webu [Portál Azure](https://portal.azure.com).
1. Na levé straně klikněte na **všechny prostředky** a pak na název svého účtu Automation.
1. Na stránce **účet Automation** vyberte v části **Správa konfigurace**možnost **Konfigurace stavu (DSC)** .
1. Na stránce **Konfigurace stavu (DSC)** klikněte na kartu **Konfigurace** nebo **zkompilované konfigurace** a pak v nabídce v horní části stránky klikněte na **vytvořit konfiguraci** .
1. V kroku **základy** zadejte nový název konfigurace (požadováno) a klikněte kamkoli na řádek každého složeného prostředku, který chcete zahrnout do nové konfigurace, a potom klikněte na **Další** nebo klikněte na krok **zdrojového kódu** . Pro následující kroky jsme vybrali složené prostředky **PSExecutionPolicy** a **RenameAndDomainJoin** .
   ![snímku kroku základy stránky konfigurace pro psaní](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. Krok **zdrojového kódu** ukazuje, jak vypadá složená konfigurace vybraných složených prostředků. Můžete zobrazit sloučení všech parametrů a jejich předání do složeného prostředku. Po dokončení kontroly nového zdrojového kódu klikněte na tlačítko **Další** nebo klikněte na krok **parametry** .
   ![snímku kroku zdrojového kódu stránky konfigurace pro psaní](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. V kroku **Parameters** je parametr, který má každý složený prostředek vystavený, aby mohl být poskytnut. Pokud má parametr popis, zobrazí se vedle pole parametru. Pokud je pole parametr typu **PSCredential** , rozevírací seznam, který se má nakonfigurovat, poskytne seznam objektů **přihlašovacích údajů** v aktuálním účtu Automation. K dispozici je také možnost **+ Přidat pověření** . Po zadání všech požadovaných parametrů klikněte na **Uložit a zkompilovat**.
   ![snímku kroku s parametry stránky konfigurace pro psaní](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

Po uložení nové konfigurace je odeslána pro kompilaci. Stav úlohy kompilace můžete zobrazit stejně jako všechny importované konfigurace. Další informace naleznete v tématu [zobrazení úlohy kompilace](automation-dsc-getting-started.md#viewing-a-compilation-job).

Po úspěšném dokončení kompilace se nová konfigurace zobrazí na kartě **zkompilované konfigurace** . Jakmile je tato karta viditelná, může být přiřazena ke spravovanému uzlu pomocí kroků v části [Změna přiřazení uzlu k jiné konfiguraci uzlu](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration).

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak začít, najdete v tématu [Začínáme s konfigurací stavu Azure Automation](automation-dsc-getting-started.md) .
- Další informace o připojování uzlů najdete v tématu věnovaném [připojování počítačů ke správě podle konfigurace stavu Azure Automation](automation-dsc-onboarding.md) .
- Další informace o kompilaci konfigurací DSC, abyste je mohli přiřadit cílovým uzlům, najdete v tématu [kompilace konfigurací v konfiguraci stavu Azure Automation](automation-dsc-compile.md)
- Referenční informace k rutinám PowerShellu najdete v tématu [rutiny konfigurace stavu Azure Automation](/powershell/module/azurerm.automation/#automation) .
- Informace o cenách najdete v tématu [Azure Automation ceny konfigurace stavu](https://azure.microsoft.com/pricing/details/automation/) .
- Příklad použití konfigurace stavu Azure Automation v kanálu průběžného nasazování najdete v tématu [průběžné nasazování pomocí Azure Automation konfigurace stavu a čokolády](automation-dsc-cd-chocolatey.md) .
