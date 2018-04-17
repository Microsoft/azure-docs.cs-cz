---
title: Pomocí image Azure Marketplace můžete vytvořit virtuální počítač s Linuxem Terraform s identita spravované služby
description: Použijte bitovou kopii Marketplace k vytvoření virtuálního počítače Terraform Linux s identita spravované služby a vzdálenou správu stavu snadno nasadit prostředky do Azure.
keywords: terraform, devops, MSI, virtuální počítač, vzdálené stav, azure
author: VaijanathB
manager: rloutlaw
ms.author: tarcher
ms.date: 3/12/2018
ms.topic: article
ms.openlocfilehash: 5f0ee2904c1072a5ad8c5f7ae1c90e649cc4813c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="use-an-azure-marketplace-image-to-create-a-terraform-linux-virtual-machine-with-managed-service-identity"></a>Pomocí image Azure Marketplace můžete vytvořit virtuální počítač s Linuxem Terraform s identita spravované služby

V tomto článku se dozvíte, jak používat [image Terraform Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview) k vytvoření virtuálního počítače s Ubuntu Linux (16.04 LTS) s nejnovější [Terraform](https://www.terraform.io/intro/index.html) verze nainstalovaný a nakonfigurovaný pomocí [spravované Služba Identity (MSI)](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Tuto bitovou kopii nakonfiguruje taky vzdálené back-end, chcete-li povolit [vzdáleného stavu](https://www.terraform.io/docs/state/remote.html) správu pomocí Terraform. 

Bitovou kopii Terraform Marketplace snadno začít používat Terraform v Azure, aniž by bylo nutné nainstalovat a nakonfigurovat Terraform ručně. 

Neexistují žádné poplatky softwaru pro tuto bitovou kopii Terraform virtuálních počítačů. Platíte jenom využití poplatků Azure hardwaru, které jsou hodnotí na základě velikosti virtuálního počítače, který je zřízený. Další informace o poplatcích výpočetní najdete v tématu [stránce s cenami virtuální počítače s Linuxem](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="prerequisites"></a>Požadavky
Před vytvořením Linux Terraform virtuálního počítače, musí mít předplatné Azure. Pokud jste již nemáte, přečtěte si téma [vytvořit účet Azure zdarma Dnes](https://azure.microsoft.com/free/).  

## <a name="create-your-terraform-virtual-machine"></a>Vytvoření virtuálního počítače Terraform 

Tady jsou kroky k vytvoření instance virtuálního počítače Linux Terraform: 

1. V portálu Azure přejděte do [vytvořit prostředek](https://ms.portal.azure.com/#create/hub) výpis.

2. V **vyhledávání na webu Marketplace** panelu vyhledávání, hledání **Terraform**. Vyberte **Terraform** šablony. 

3. Na kartě Podrobnosti Terraform vpravo dole vyberte **vytvořit** tlačítko.

    ![Vytvoření virtuálního počítače Terraform](media\terraformmsi.png)

4. Vstupy pro všechny kroky v průvodci vytvořit virtuální počítač Terraform Linux v následujících částech. V následující části jsou uvedeny vstupních hodnot, které jsou potřeba ke konfiguraci jednotlivých kroků.

## <a name="details-on-the-create-terraform-tab"></a>Podrobnosti v kartě vytvořit Terraform

Zadejte následující podrobnosti **vytvořit Terraform** karty:

1. **Základy**
    
   * **Název**: název Terraform virtuálního počítače.
   * **Uživatelské jméno**: první účet přihlásit ID.
   * **Heslo**: první heslo účtu. (Veřejný klíč SSH můžete místo hesla.)
   * **Předplatné**: předplatné, na kterém se tento počítač je vytvořen a účtují. Musíte mít oprávnění vytvoření prostředku pro toto předplatné.
   * **Skupina prostředků**: skupinu nový nebo existující prostředek.
   * **Umístění**: datacenter, která je nejvhodnější. Obvykle je datacenter, která obsahuje většinu dat nebo ten, který je nejblíže vašemu fyzické umístění pro nejrychlejší přístup k síti.

2. **Další nastavení**

   * **Velikost**: velikost virtuálního počítače. 
   * **Typ disku virtuálního počítače**: SSD nebo pevný disk.

3. **Souhrn Terraform**

   * Ověřte, že veškeré informace, které jste zadali správný. 

4. **Kupte si**

   * Chcete-li zahájit proces zřizování, vyberte **koupit**. Je k dispozici odkaz na podmínky transakce. Virtuální počítač nemá žádné další poplatky za výpočetní pro velikost serveru, který jste si zvolili v kroku velikost.

Image virtuálního počítače Terraform provede následující kroky:

* Vytvoří virtuální počítač s identitou systému přiřazen, založený na bitovou kopii Ubuntu 16.04 LTS.
* Nainstaluje příponou MSI ve virtuálním počítači povolit tokenů OAuth pro vystavování pro prostředky Azure.
* Přiřadí oprávnění RBAC spravované identitu, udělení oprávnění vlastníka pro skupinu prostředků.
* Vytvoří složku šablony Terraform (tfTemplate).
* Předem nakonfiguruje Terraform vzdáleného stavu s Azure back end.

## <a name="access-and-configure-a-linux-terraform-virtual-machine"></a>Přístup a konfigurace virtuálního počítače Linux Terraform

Po vytvoření virtuálního počítače, se můžete přihlásit se pomocí protokolu SSH. Pomocí pověření účtu, které jste vytvořili v části "Základy" krok 3 pro rozhraní prostředí text. V systému Windows, si můžete stáhnout nástroj pro klienta na SSH jako [Putty](http://www.putty.org/).

Po připojení k virtuálnímu počítači pomocí protokolu SSH, budete muset dát oprávnění Přispěvatel pro celé předplatné identita spravované služby ve virtuálním počítači. 

Oprávnění přispěvatele pomáhá MSI ve virtuálním počítači pomocí Terraform vytvářet prostředky mimo skupinu prostředků virtuálních počítačů. Tato akce můžete snadno dosáhnout jednou spuštěním skriptu. Použijte následující příkaz:

`. ~/tfEnv.sh`

Předchozí skript používá [AZ rozhraní příkazového řádku v 2.0 interaktivní přihlašování](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest#interactive-log-in) mechanismus pro ověření pomocí Azure a přiřaďte oprávnění přispěvatele identita spravované služby v celé předplatné virtuálního počítače. 

 Virtuální počítač má Terraform vzdáleného stavu back-end. K jeho povolení pro vaše nasazení Terraform, zkopírujte soubor remoteState.tf z adresáře tfTemplate do kořenového adresáře Terraform skripty.  

 `cp  ~/tfTemplate/remoteState.tf .`

 Další informace o vzdálené správě stavu najdete v tématu [tuto stránku týkající se stavu vzdáleného Terraform](https://www.terraform.io/docs/state/remote.html). Přístupový klíč úložiště je vystaven v tomto souboru a je potřeba vyloučit před potvrzení Terraform konfigurační soubory do správy zdrojového kódu.

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak nastavit virtuální počítač s Linuxem Terraform v Azure. Zde jsou některé další zdroje, které obsahují další informace o Terraform v Azure: 

 [Centrum Terraform v doméně Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Dokumentaci k Terraform Azure zprostředkovatele](http://aka.ms/terraform)  
 [Zprostředkovatel zdroje Terraform Azure](http://aka.ms/tfgit)  
 [Moduly Terraform Azure](http://aka.ms/tfmodules)
 

















