---
title: Rychlý start Azure – Konfigurace virtuálního počítače s využitím DSC | Dokumentace Microsoftu
description: Konfigurace sady LAMP na virtuálním počítači s Linuxem s využitím konfigurace požadovaného stavu
services: automation
ms.subservice: dsc
keywords: dsc, configuration, automation
ms.date: 11/06/2018
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 1a146ab7c05d200b71a33a72fa6362c3cf62629a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457514"
---
# <a name="configure-a-virtual-machine-with-desired-state-configuration"></a>Konfigurace virtuálního počítače s konfigurací požadovaného stavu

Povolením konfigurace stavu azure automatizace můžete spravovat a monitorovat konfigurace serverů Windows a Linux pomocí konfigurace požadovaného stavu (DSC). Konfigurace, které se pohybují od požadované konfigurace, lze identifikovat nebo automaticky opravit. Tento rychlý start prochází jednotlivé kroky k připojení virtuálního počítače s Linuxem a nasazení sady LAMP s využitím DSC.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu je potřeba:

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/).
* Účet Azure Automation. Pokyny k vytvoření účtu Azure Automation Spustit jako najdete v tématu [Účet Spustit jako pro Azure](automation-sec-configure-azure-runas-account.md).
* Virtuální počítač Azure Resource Manageru (ne Classic) se systémem Red Hat Enterprise Linux, CentOS nebo Oracle Linux. Pokyny k vytvoření virtuálního počítače najdete v tématu [Vytvoření prvního virtuálního počítače s Linuxem na webu Azure Portal](../virtual-machines/linux/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se https://portal.azure.comk Azure na adrese .

## <a name="onboard-a-virtual-machine"></a>Připojení virtuálního počítače

Existuje mnoho různých metod pro napalubě stroje a povolit DSC. Tento rychlý start popisuje připojení přes účet Automation. Další informace o různých způsobech připojení počítačů do konfigurace stavu najdete v článku [o registraci.](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

1. V levém podokně webu Azure Portal vyberte **Účty Automation**. Pokud není v levém podokně viditelná, klikněte na **Všechny služby** a vyhledejte ve výsledném zobrazení.
1. V seznamu vyberte účet Automation.
1. V levém podokně účtu Automation vyberte **Konfigurace stavu (DSC)**.
2. Kliknutím na **Přidat** otevřete stránku pro výběr virtuálního počítače.
3. Najděte virtuální počítač, pro který chcete povolit DSC. K vyhledání konkrétního virtuálního počítače můžete použít vyhledávací pole a možnosti filtru.
4. Klikněte na virtuální počítač a potom klikněte na **Připojit.**
5. Vyberte vhodné nastavení DSC pro tento virtuální počítač. Pokud jste již připravili konfiguraci, `Node Configuration Name`můžete ji zadat jako . Pokud chcete řídit chování konfigurace počítače, můžete nastavit [režim konfigurace](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaConfig).
6. Klikněte na tlačítko **OK**. Při rozšíření DSC je nasazena do virtuálního počítače, stav se zobrazí jako `Connecting`.

![Připojení virtuálního počítače Azure k DSC](./media/automation-quickstart-dsc-configuration/dsc-onboard-azure-vm.png)

## <a name="import-modules"></a>Import modulů

Moduly obsahují prostředky DSC a mnoho z nich lze nalézt v [galerii prostředí PowerShell](https://www.powershellgallery.com). Všechny prostředky, které se používají ve vašich konfiguracích, musí být před kompilací importovány do účtu Automatizace. Pro účely tohoto kurzu se vyžaduje modul **nx**.

1. V levém podokně účtu Automatizace vyberte **Galerie modulů** v části **Sdílené prostředky**.
1. Vyhledejte modul, který chcete importovat zadáním části jeho názvu: `nx`.
1. Klikněte na modul pro import.
1. Klepněte na **tlačítko Importovat**.

![Importování modulu DSC](./media/automation-quickstart-dsc-configuration/dsc-import-module-nx.png)

## <a name="import-the-configuration"></a>Import konfigurace

Tento rychlý start využívá konfiguraci DSC, která na počítači konfiguruje Apache HTTP Server, MySQL a PHP. Viz [Konfigurace DSC](https://docs.microsoft.com/powershell/scripting/dsc/configurations/configurations).

Do textového editoru zadejte následující text a uložte jej místně jako **AMPServer.ps1**.

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

1. V levém podokně účtu Automation vyberte **Konfigurace stavu (DSC)** a pak klikněte na kartu **Konfigurace**.
2. Klikněte na tlačítko **+ Přidat**.
3. Vyberte konfigurační soubor, který jste uložili v předchozím kroku.
4. Klikněte na tlačítko **OK**.

## <a name="compile-a-configuration"></a>Kompilace konfigurace

Před přiřazením uzlu je nutné zkompilovat konfiguraci DSC do konfigurace uzlu (dokument MOF). Kompilace ověří konfiguraci a umožňuje zadat hodnoty parametrů. Další informace o kompilaci konfigurace naleznete v [tématu Kompilace konfigurací v konfiguraci stavu](automation-dsc-compile.md).

1. V levém podokně účtu Automatizace vyberte **Konfigurace stavu (DSC)** a klikněte na kartu **Konfigurace.**
1. Vyberte `LAMPServer`konfiguraci .
1. V nabídce možnosti vyberte **Kompilace** a klepněte na tlačítko **Ano**.
1. V zobrazení Konfigurace se zobrazí nová úloha kompilace ve frontě. Až se úloha úspěšně dokončí, budete připraveni přejít k dalšímu kroku. Pokud dojde k nějakým selháním, můžete kliknout na kompilaci úlohy pro podrobnosti.

## <a name="assign-a-node-configuration"></a>Přiřazení konfigurace uzlu

Konfiguraci kompilovaného uzlu můžete přiřadit k uzlu DSC. Přiřazení aplikuje konfiguraci na počítač a monitoruje nebo automaticky opravuje pro jakýkoli posun od této konfigurace.

1. V levém podokně účtu Automatizace vyberte **Konfigurace stavu (DSC)** a klikněte na kartu **Uzly.**
1. Vyberte uzel, ke kterému chcete přiřadit konfiguraci.
1. Klikněte na **Přiřadit konfiguraci uzlu**
1. Vyberte konfiguraci `LAMPServer.localhost` uzlu a klepněte na tlačítko **OK**. Konfigurace stavu nyní přiřadí kompilovanou konfiguraci uzlu a `Pending`stav uzlu se změní na . Při další pravidelné kontrole uzel načte konfiguraci, použije ji a hlásí stav. Může trvat až 30 minut, než uzel načte konfiguraci, v závislosti na nastavení uzlu. 
1. Pokud chcete vynutit okamžitou kontrolu, můžete spustit následující příkaz místně na virtuálním počítači s Linuxem:`sudo /opt/microsoft/dsc/Scripts/PerformRequiredConfigurationChecks.py`

![Přiřazení konfigurace uzlu](./media/automation-quickstart-dsc-configuration/dsc-assign-node-configuration.png)

## <a name="view-node-status"></a>Zobrazit stav uzlu

Můžete zobrazit stav všech uzlů spravovaných konfigurací stavu v účtu Automation. Informace se zobrazí tak, že zvolíte **konfiguraci stavu (DSC)** a kliknete na kartu **Uzly.** Zobrazení můžete filtrovat podle stavu, konfigurace uzlu nebo vyhledávání názvů.

![Stav uzlu DSC](./media/automation-quickstart-dsc-configuration/dsc-node-status.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zalomítli konfiguraci virtuálního počítače s Linuxem do stavu, vytvořili konfiguraci pro zásobník LAMP a nasadili konfiguraci do virtuálního počítače. Chcete-li zjistit, jak můžete pomocí konfigurace stavu azure automatizace povolit průběžné nasazení, pokračujte v článku:

> [!div class="nextstepaction"]
> [Průběžné nasazování do virtuálního počítače pomocí DSC a Chocolatey](./automation-dsc-cd-chocolatey.md)

* Další informace o powershellovém dsc najdete v [tématu Přehled konfigurace požadovaného stavu prostředí PowerShell](https://docs.microsoft.com/powershell/scripting/dsc/overview/overview).
* Další informace o správě konfigurace stavu z PowerShellu najdete v [tématu Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.automation/).
* Informace o tom, jak předávat sestavy DSC do protokolů Azure Monitor pro vytváření sestav a upozorňování, najdete [v tématu předávání sestav DSC do protokolů Azure Monitor](automation-dsc-diagnostics.md).