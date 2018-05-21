---
title: Rychlý start Azure – Konfigurace virtuálního počítače s využitím DSC | Dokumentace Microsoftu
description: Konfigurace sady LAMP na virtuálním počítači s Linuxem s využitím konfigurace požadovaného stavu
services: automation
ms.service: automation
ms.component: dsc
keywords: dsc, configuration, automation
author: KrisBash
ms.author: krbash
ms.date: 12/17/2017
ms.topic: quickstart
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 959171963bcdc721c81823fcf4f9769174b32636
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="configure-a-linux-virtual-machine-with-desired-state-configuration"></a>Konfigurace virtuálního počítače s Linuxem s využitím konfigurace požadovaného stavu

Povolení konfigurace požadovaného stavu (DSC) vám umožní spravovat a monitorovat konfigurace vašich serverů s Windows a Linuxem. Konfigurace, které se odchylují o požadované konfigurace, je možné identifikovat nebo automaticky opravit. Tento rychlý start prochází jednotlivé kroky k připojení virtuálního počítače s Linuxem a nasazení sady LAMP s využitím DSC.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu je potřeba:

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/).
* Účet Azure Automation. Pokyny k vytvoření účtu Azure Automation Spustit jako najdete v tématu [Účet Spustit jako pro Azure](automation-sec-configure-azure-runas-account.md).
* Virtuální počítač Azure Resource Manageru (ne Classic) se systémem Red Hat Enterprise Linux, CentOS nebo Oracle Linux. Pokyny k vytvoření virtuálního počítače najdete v tématu [Vytvoření prvního virtuálního počítače s Linuxem na webu Azure Portal](../virtual-machines/linux/quick-create-portal.md).

## <a name="log-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k Azure na adrese https://portal.azure.com.

## <a name="onboard-a-virtual-machine"></a>Připojení virtuálního počítače
Existuje celá řada různých metod, jak připojit počítač a povolit konfiguraci požadovaného stavu. Tento rychlý start popisuje připojení přes účet Automation. Další informace o různých metodách připojení počítačů ke konfiguraci požadovaného stavu najdete v článku o [připojování](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding).

1. V levém podokně webu Azure Portal vyberte **Účty Automation**. Pokud tuto možnost v levém podokně nevidíte, klikněte na **Všechny služby** a vyhledejte ji ve výsledném zobrazení.
1. V seznamu vyberte účet Automation.
1. V levém podokně účtu Automation vyberte **Uzly DSC**.
1. Klikněte na možnost nabídky **Přidat virtuální počítač Azure**.
1. Vyhledejte virtuální počítače, pro který chcete povolit DSC. K vyhledání konkrétního virtuálního počítače můžete použít vyhledávací pole a možnosti filtru.
1. Klikněte na vybraný virtuální počítač a vyberte **Připojit**.
1. Vyberte vhodné nastavení DSC pro tento virtuální počítač. Pokud již máte připravenou konfiguraci, můžete ji zadat jako *Název konfigurace uzlu*. Pokud chcete řídit chování konfigurace počítače, můžete nastavit [režim konfigurace](https://docs.microsoft.com/powershell/dsc/metaconfig).
1. Klikněte na tlačítko **OK**.

![Připojení virtuálního počítače Azure k DSC](./media/automation-quickstart-dsc-configuration/dsc-onboard-azure-vm.png)

Zatímco se rozšíření konfigurace požadovaného stavu nasazuje do virtuální počítače, zobrazuje se stav *Připojování*.

## <a name="import-modules"></a>Import modulů

Moduly obsahují prostředky DSC a řadu jich můžete najít v [Galerii prostředí PowerShell](https://www.powershellgallery.com). Všechny prostředky, které se používají ve vašich konfiguracích, se před zkompilováním musí importovat do účtu Automation. Pro účely tohoto kurzu se vyžaduje modul **nx**.

1. V levém podokně účtu Automation vyberte **Galerie modulů** (v části Sdílené prostředky).
1. Vyhledejte modul, který chcete importovat, zadáním části jeho názvu: *nx*
1. Klikněte na modul, který chcete importovat.
1. Klikněte na **Importovat**.

![Importování modulu DSC](./media/automation-quickstart-dsc-configuration/dsc-import-module-nx.png)

## <a name="import-the-configuration"></a>Import konfigurace

Tento rychlý start využívá konfiguraci DSC, která na počítači konfiguruje Apache HTTP Server, MySQL a PHP.

Informace o konfiguracích DSC najdete v tématu [Konfigurace DSC](https://docs.microsoft.com/powershell/dsc/configurations).

V textovém editoru zadejte následující a soubor místně uložte jako `LAMPServer.ps1`.

```powershell-interactive
configuration LAMPServer {
   Import-DSCResource -module "nx"

   Node localhost {

        $requiredPackages = @("httpd","mod_ssl","php","php-mysql","mariadb","mariadb-server")
        $enabledServices = @("httpd","mariadb")

        #Ensure packages are installed
        ForEach ($package in $requiredPackages){
            nxPackage $Package{
                Ensure = "Present"
                Name = $Package
                PackageManager = "yum"
            }
        }

        #Ensure daemons are enabled
        ForEach ($service in $enabledServices){
            nxService $service{
                Enabled = $true
                Name = $service
                Controller = "SystemD"
                State = "running"
            }
        }
   }
}
```

Import konfigurace:

1. V levém podokně účtu Automation vyberte **Konfigurace DSC**.
1. Klikněte na možnost nabídky **Přidat konfiguraci**.
1. Vyberte *Konfigurační soubor*, který jste uložili před tímto krokem.
1. Klikněte na tlačítko **OK**.

## <a name="compile-a-configuration"></a>Kompilace konfigurace

Konfigurace DSC se musí před přiřazením k uzlu zkompilovat do konfigurace uzlu (dokument MOF). Kompilace ověří konfiguraci a umožňuje zadat hodnoty parametrů. Další informace o kompilaci konfigurace najdete v tématu [Kompilace konfigurací v Azure Automation DSC](https://docs.microsoft.com/azure/automation/automation-dsc-compile).

Kompilace konfigurace:

1. V levém podokně účtu Automation vyberte **Konfigurace DSC**.
1. Vyberte konfiguraci LAMPServer, kterou jste naimportovali v dřívějším kroku.
1. V možnostech nabídky klikněte na **Zkompilovat** a pak na **Ano**.
1. V zobrazení Konfigurace se teď ve frontě zobrazí nová *Úloha kompilace*. Až se úloha úspěšně dokončí, budete připraveni přejít k dalšímu kroku. Pokud dojde k nějakým chybám, kliknutím na úlohu kompilace můžete zobrazit podrobnosti.

![Stav úlohy kompilace](./media/automation-quickstart-dsc-configuration/dsc-compilationjob.png)

## <a name="assign-a-node-configuration"></a>Přiřazení konfigurace uzlu

Zkompilovanou *konfiguraci uzlu* už je možné přiřadit uzlům DSC. Přiřazení aplikuje konfiguraci na počítač a monitoruje (nebo automaticky opravuje) případné odchylky od této konfigurace.

1. V levém podokně účtu Automation vyberte **Uzly DSC**.
1. Vyberte uzel, ke kterému chcete přiřadit konfiguraci.
1. Klikněte na **Přiřadit konfiguraci uzlu**
1. Vyberte *konfiguraci uzlu* - **LAMPServer.localhost**, která se přiřadí, a klikněte na **OK**
1. Zkompilovaná konfigurace se teď přiřazuje k uzlu a stav uzlu se mění na *Probíhá*. Při další pravidelné kontrole si uzel tuto konfiguraci načte, použije ji a ohlásí zpět stav. Načtení konfigurace může uzlu trvat až 30 minut v závislosti na nastavení uzlu. Pokud chcete vynutit okamžitou kontrolu, můžete spustit následující příkaz místně na virtuálním počítači s Linuxem:`sudo /opt/microsoft/dsc/Scripts/PerformRequiredConfigurationChecks.py`

![Přiřazení konfigurace uzlu](./media/automation-quickstart-dsc-configuration/dsc-assign-node-configuration.png)

## <a name="viewing-node-status"></a>Zobrazení stavu uzlu

Stav všech spravovaných uzlů najdete v zobrazení **Uzly DCS** účtu Automation. Zobrazení můžete filtrovat podle stavu, konfigurace uzlu nebo hledání názvu. 

![Stav uzlu DSC](./media/automation-quickstart-dsc-configuration/dsc-node-status.png)

## <a name="next-steps"></a>Další kroky

V rámci tohoto rychlého startu jste připojili virtuální počítač s Linuxem k DSC, vytvořili konfiguraci sady LAMP a nasadili ji do virtuálního počítače. Informace o tom, jak pomocí Automation DSC umožnit průběžné nasazování, najdete v následujícím článku:

> [!div class="nextstepaction"]
> [Průběžné nasazování do virtuálního počítače pomocí DSC a Chocolatey](./automation-dsc-cd-chocolatey.md)

* Další informace o konfiguraci požadovaného stavu PowerShellu najdete v tématu [Přehled konfigurace požadovaného stavu PowerShellu](https://docs.microsoft.com/powershell/dsc/overview).
* Další informace o správě Automation DSC z PowerShellu najdete v tématu [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.automation/?view=azurermps-5.0.0).
* Informace o přesměrování sestav DSC do Log Analytics za účelem generování sestav a upozorňování najdete v tématu [Přesměrování generování sestav DSC do Log Analytics](https://docs.microsoft.com/azure/automation/automation-dsc-diagnostics). 
