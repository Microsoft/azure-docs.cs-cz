---
title: Kurz – vytvoření virtuálního počítače se systémem Linux se spravovanou identitou z Azure Marketplace Image pomocí Terraformu
description: Vytvoření virtuálního počítače s Terraformu Linux se spravovanou identitou a správou vzdáleného stavu pomocí Azure Marketplace image
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: e00b674df35516da559339af8028c1ca1845b0db
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969788"
---
# <a name="tutorial-create-a-linux-vm-with-a-managed-identity-from-the-azure-marketplace-image-using-terraform"></a>Kurz: Vytvoření virtuálního počítače se systémem Linux se spravovanou identitou z Azure Marketplace Image pomocí Terraformu

V tomto článku se dozvíte, jak pomocí [image Terraformu z webu Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview) vytvořit virtuální počítač s Ubuntu Linuxem (16.04 LTS) a nainstalovanou nejnovější verzí [Terraformu](https://www.terraform.io/intro/index.html) nakonfigurovanou s využitím [spravovaných identit pro prostředky Azure](/azure/active-directory/managed-service-identity/overview). Tato image nakonfiguruje i vzdálený back-end, aby prostřednictvím Terraformu umožnil správu [vzdáleného stavu](https://www.terraform.io/docs/state/remote.html). 

Image Terraformu z webu Marketplace usnadňuje začátky používání Terraformu v Azure bez nutnosti manuální instalace a konfigurace Terraformu. 

Za tuto image virtuálního počítače Terraform se neúčtují žádné poplatky. Platíte jenom poplatky za využití hardwaru Azure na základě velikosti zřízeného virtuálního počítače. 

Další informace o výpočetních poplatcích najdete na [stránce s cenami za virtuální počítače Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="prerequisites"></a>Předpoklady
Než budete moct vytvořit virtuální počítač se systémem Linux Terraformu, musíte mít předplatné Azure. Pokud ho ještě nemáte, přejděte na stránku [Vytvořte si bezplatný účet Azure ještě dnes](https://azure.microsoft.com/free/).  

## <a name="create-your-terraform-vm"></a>Vytvoření virtuálního počítače s Terraformu 

Tady je postup vytvoření instance virtuálního počítače se systémem Linux Terraformu: 

1. Na webu Azure Portal přejděte na záznam [Vytvořit prostředek](https://ms.portal.azure.com/#create/hub).

1. Do vyhledávacího pole **Hledat na Marketplace** zadejte **Terraform**. 

1. Vyberte **vytvořit**. 

1. V následujících částech jsou uvedeny vstupy pro každý z kroků v Průvodci vytvořením virtuálního počítače s Terraformu Linux. Následující část uvádí vstupy potřebné ke konfiguraci každého kroku.

## <a name="details-on-the-create-terraform-tab"></a>Podrobnosti na kartě Vytvořit Terraform

Na kartě **Vytvořit Terraform** zadejte následující podrobnosti:

1. **Základy**
    
   * **Name (název**): název vašeho virtuálního počítače s terraformu.
   * **Uživatelské jméno**: přihlašovací ID prvního účtu
   * **Heslo**: heslo první účtu (Místo hesla můžete použít veřejný klíč SSH.)
   * **Předplatné**: předplatné, ve kterém se má počítač vytvořit a kterému se bude fakturovat. Toto předplatné musí mít oprávnění vytvářet prostředky.
   * **Skupina prostředků**: nová nebo existující skupina prostředků
   * **Umístění**: nejvhodnější datacentrum (Obvykle jde o datacentrum s většinou dat nebo nejbližší datacentrum kvůli co nejrychlejšímu síťovému přístupu.)

2. **Další nastavení**

   * **Velikost**: velikost virtuálního počítače. 
   * **Typ disku virtuálního počítače**: SSD nebo HDD

3. **Souhrn Terraformu**

   * Ověřte správnost všech zadaných informací. 

4. **Koupě**

   * Pokud chcete proces zřizování spustit, vyberte **Koupit**. Zobrazí se odkaz na podmínky transakce. Tento virtuální počítač nemá žádné další poplatky nad výpočetní kapacitu pro velikost serveru, kterou jste zvolili v kroku velikost.

Image virtuálního počítače s Terraformu provádí následující kroky:

* Vytvoří virtuální počítač s identitou přiřazenou systémem, která je založena na imagi Ubuntu 16.04 LTS.
* Nainstaluje na virtuální počítač rozšíření spravované identity pro prostředky Azure, aby bylo možné vystavovat tokeny OAuth pro prostředky Azure.
* Přiřadí spravované identitě oprávnění RBAC, která udělí skupině prostředků oprávnění vlastníka.
* Vytvoří složku pro šablonu Terraformu (tfTemplate).
* Předem nakonfiguruje vzdálený stav Terraformu s back-endem Azure.

## <a name="access-and-configure-a-linux-terraform-vm"></a>Přístup k virtuálnímu počítači s Terraformu Linux a jeho konfigurace

Po vytvoření virtuálního počítače proveďte následující kroky:

1. Přihlaste se k virtuálnímu počítači pomocí SSH. Použijte přihlašovací údaje účtu, které jste vytvořili v předchozí části. Ve Windows můžete stáhnout klientský nástroj SSH, jako je [Putty](https://www.putty.org/).

1. Udělte oprávnění přispěvatele pro celé předplatné spravovaným identitám pro prostředky Azure na virtuálním počítači. 

    Oprávnění Přispěvatel pomáhá spravovaným identitám prostředků Azure na virtuálním počítači používat Terraformu k vytváření prostředků mimo skupinu prostředků virtuálních počítačů. Tuto akci proveďte spuštěním následujícího skriptu: 
    
    ```bash
    . ~/tfEnv.sh
    ```

    Tento skript k ověřování pomocí Azure používá mechanismus [interaktivního přihlášení Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest#sign-in-interactively) . Tento proces přiřadí [oprávnění přispěvatele spravované identity](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) pro celé předplatné. 

1. Virtuální počítač má back-end vzdáleného stavu Terraformu. Pokud ho chcete povolit v nasazení Terraformu, musíte zkopírovat soubor `remoteState.tf` do kořenového adresáře skriptů Terraformu.

    ```bash
    cp  ~/tfTemplate/remoteState.tf .
    ```

    Další informace o správě vzdálených stavů najdete v tématu [terraformu Remote State](https://www.terraform.io/docs/state/remote.html). Přístupový klíč k úložišti je zveřejněný v tomto souboru. Vylučte před potvrzením konfiguračních souborů Terraformu do správy zdrojového kódu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Terraformu v Azure](/azure/ansible/)