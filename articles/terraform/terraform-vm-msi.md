---
title: "Pomocí image Azure Marketplace můžete vytvořit virtuální počítač s Linuxem Terraform s identita spravované služby"
description: "Použijte bitovou kopii Marketplace k vytvoření Terraform Linux virtuálního počítače s identita spravované služby a stav vzdáleného řízení snadno nasadit prostředky do Azure."
keywords: "terraform, devops, MSI, virtuální počítač, vzdálené stav, azure"
author: VaijanathB
manager: rloutlaw
ms.author: tarcher
ms.date: 3/12/2018
ms.topic: article
ms.openlocfilehash: ce8a3e8b813bf4224a1fd77d60ec30f2f8e080a7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="use-an-azure-marketplace-image-to-create-a-terraform-linux-virtual-machine-with-managed-service-identity"></a>Pomocí image Azure Marketplace můžete vytvořit virtuální počítač s Linuxem Terraform s identita spravované služby

V tomto článku se dozvíte, jak používat [image Terraform Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview) k vytvoření `Ubuntu Linux VM (16.04 LTS)` nejnovější [Terraform](https://www.terraform.io/intro/index.html) verze nainstalovaný a nakonfigurovaný pomocí [(identita spravované služby MSI)](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Tuto bitovou kopii nakonfiguruje taky povolit vzdálené back-end [stav vzdáleného](https://www.terraform.io/docs/state/remote.html) správu pomocí Terraform. Bitovou kopii Terraform Marketplace snadno začít používat Terraform v Azure v minutách, aniž by museli Terraform nainstalovat a nakonfigurovat ověřování ručně. 

Neexistují žádné poplatky softwaru pro tuto bitovou kopii Terraform virtuálních počítačů. Platíte jenom využití poplatků Azure hardwaru, které jsou hodnotí na základě velikosti virtuálního počítače zřízené. Další podrobnosti o poplatky za výpočetní naleznete na [stránce ceny virtuálních počítačů Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="prerequisites"></a>Požadavky
Než bude možné vytvořit virtuální počítač s Linuxem Terraform, musí mít předplatné Azure. Pokud není již nemáte, přečtěte si téma [vytvořit účet Azure zdarma Dnes](https://azure.microsoft.com/free/).  

## <a name="create-your-terraform-virtual-machine"></a>Vytvoření virtuálního počítače Terraform 

Tady jsou kroky k vytvoření instance systému Linux Terraform virtuálního počítače. 

1. Přejděte na [vytvořit prostředek](https://ms.portal.azure.com/#create/hub) výpis na portálu Azure.
2. Vyhledejte `Terraform` v `Search the Marketplace` panelu vyhledávání. Vyberte `Terraform` šablony. Vyberte **vytvořit** tlačítka na kartě s podrobnostmi Terraform vpravo dole.
![alternativní text](media\terraformmsi.png)
3. Následující části obsahují vstupy pro všechny kroky v průvodci (**uvedené na pravé straně**) k vytvoření Terraform Linux virtuálního počítače.  Tady jsou potřeba ke konfiguraci jednotlivých kroků vstupní hodnoty

## <a name="details-in-create-terraform-tab"></a>Vytvořit podrobnosti v kartě Terraform

Zde jsou informace, které je nutné zadat na kartě Terraform vytvořit.

a. **Základy**
    
* **Název**: název Terraform virtuálního počítače.
* **Uživatelské jméno**: první účet přihlásit ID.
* **Heslo**: první heslo účtu (veřejný klíč SSH můžete použít místo hesla).
* **Předplatné**: Pokud máte více než jedno předplatné, vyberte ten, na kterém se tento počítač je vytvořen a účtují. Musíte mít oprávnění vytvoření prostředku pro toto předplatné.
* **Skupina prostředků**: můžete vytvořit novou nebo použít stávající skupinu.
* **Umístění**: Vyberte datové centrum, která je nejvhodnější. Obvykle je datové centrum, které má většina vašich dat nebo je nejblíže vašemu fyzické umístění pro nejrychlejší přístup k síti.

b. **Další nastavení**

* Velikost: Velikost virtuálního počítače.
* Typ disku virtuálního počítače: zvolit SSD a HDD

c. **Souhrn Terraform**

* Ověřte, že všechny informace, které jste zadali správné. 

d. **Kupte si**

* Pokud chcete spustit, zřizování, klikněte na tlačítko Koupit. Je k dispozici odkaz na podmínky transakce. Virtuální počítač nemá žádné další poplatky za výpočetní pro velikost serveru, které jste zvolili v kroku velikost.

Image virtuálního počítače Terraform provede následující kroky

* Vytvoří virtuální počítač s systému přiřazené identity na základě bitové kopie Ubuntu 16.04 LTS
* Instalaci rozšíření MSI ve virtuálním počítači povolit tokenů OAuth pro vystavování pro prostředky Azure
* Přiřazení oprávnění RBAC k identitě spravované, udělení oprávnění vlastníka pro skupinu prostředků
* Vytvoří složku šablony Terraform (tfTemplate)
* Předem nakonfiguruje Terraform vzdáleného stavu s Azure back-end

## <a name="how-to-access-and-configure-linux-terraform-virtual-machine"></a>Přístup a konfiguraci Linux Terraform virtuálního počítače

Po vytvoření virtuálního počítače se můžete přihlásit se pomocí protokolu SSH. Pomocí pověření účtu, které jste vytvořili v části základní informace o kroku 3 pro rozhraní prostředí text. V systému Windows, si můžete stáhnout nástroj pro klienta na SSH jako [Putty](http://www.putty.org/)

Jakmile jste použili `SSH` Pokud chcete připojit k virtuálnímu počítači, je potřeba dát oprávnění Přispěvatel pro celé předplatné spravované identita služby ve virtuálním počítači. Oprávnění přispěvatele pomáhá MSI ve virtuálním počítači pomocí Terraform vytvářet prostředky mimo skupinu prostředků virtuálních počítačů. Tato akce můžete snadno dosáhnout jednou spuštěním skriptu. Zde je příkaz to udělat.

`. ~/tfEnv.sh`

Předchozí skript používá [AZ rozhraní příkazového řádku v 2.0 interaktivní přihlašování](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest#interactive-log-in) mechanismus pro ověření pomocí Azure a přiřaďte oprávnění přispěvatele identita spravované služby virtuálního počítače na celé předplatné. 

 Virtuální počítač má stav vzdáleného Terraform back-end vytvořen a k jeho povolení pro vaše nasazení Terraform, potřebujete remoteState.tf soubor zkopírovat z adresáře tfTemplate do kořenového adresáře Terraform skripty.  

 `cp  ~/tfTemplate/remoteState.tf .`

 Další informace o vzdálené správě stavu [zde](https://www.terraform.io/docs/state/remote.html). Přístupový klíč úložiště je vystaven v tomto souboru a musí být pečlivě změnami do správy zdrojového kódu.  

## <a name="next-steps"></a>Další kroky
V tomto článku jste zjistili, jak nastavit až Terraform virtuální počítač s Linuxem v Azure. Tady jsou některé další zdroje, které další informace o Terraform v Azure. 

 [Centrum Terraform v doméně Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Dokumentaci k Terraform Azure zprostředkovatele](http://aka.ms/terraform)  
 [Zprostředkovatel zdroje Terraform Azure](http://aka.ms/tfgit)  
 [Moduly Terraform Azure](http://aka.ms/tfmodules)
 

















