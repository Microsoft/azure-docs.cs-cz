---
title: Použití image z Azure Marketplace k vytvoření virtuálního počítače Terraform s Linuxem pomocí spravované identity
description: Použijte image z webu Marketplace k vytvoření virtuálního počítače Terraform s Linuxem pomocí spravované identity a vzdálené správy stavu ke snadnému nasazení prostředků do Azure.
services: terraform
ms.service: terraform
keywords: terraform, devops, MSI, virtuální počítač, vzdálený stav, azure
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 3/12/2018
ms.openlocfilehash: 6c9bef108c2f272c678879124ae2cd4f9ae093ba
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54076213"
---
# <a name="use-an-azure-marketplace-image-to-create-a-terraform-linux-virtual-machine-with-managed-identities-for-azure-resources"></a>Použití image z Azure Marketplace k vytvoření virtuálního počítače Terraform s Linuxem pomocí spravovaných identit pro prostředky Azure

V tomto článku se dozvíte, jak pomocí [image Terraformu z webu Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview) vytvořit virtuální počítač s Ubuntu Linuxem (16.04 LTS) a nainstalovanou nejnovější verzí [Terraformu](https://www.terraform.io/intro/index.html) nakonfigurovanou s využitím [spravovaných identit pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Tato image nakonfiguruje i vzdálený back-end, aby prostřednictvím Terraformu umožnil správu [vzdáleného stavu](https://www.terraform.io/docs/state/remote.html). 

Image Terraformu z webu Marketplace usnadňuje začátky používání Terraformu v Azure bez nutnosti manuální instalace a konfigurace Terraformu. 

Za tuto image virtuálního počítače Terraform se neúčtují žádné poplatky. Platíte pouze poplatky za použití hardwaru Azure, které se počítají podle velikosti zřízeného virtuálního počítače. Další informace o poplatcích za výpočetní prostředky najdete na [stránce s cenami virtuálních počítačů s Linuxem](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="prerequisites"></a>Požadavky
Než vytvoříte virtuální počítač Terraform s Linuxem, musíte mít předplatné Azure. Pokud ho ještě nemáte, přejděte na stránku [Vytvořte si bezplatný účet Azure ještě dnes](https://azure.microsoft.com/free/).  

## <a name="create-your-terraform-virtual-machine"></a>Vytvoření virtuálního počítače Terraform 

Tady jsou kroky k vytvoření instance virtuálního počítače Terraform s Linuxem: 

1. Na webu Azure Portal přejděte na záznam [Vytvořit prostředek](https://ms.portal.azure.com/#create/hub).

2. Do vyhledávacího pole **Hledat na Marketplace** zadejte **Terraform**. Vyberte šablonu **Terraform**. 

3. Na kartě podrobností Terraformu vyberte v pravém dolním rohu tlačítko **Vytvořit**.

    ![Vytvoření virtuálního počítače Terraform](media/terraformmsi.png)

4. Následující oddíly obsahují vstupy ke každému z kroků v průvodci vytvořením virtuálního počítače Terraform s Linuxem. Následující část uvádí vstupy potřebné ke konfiguraci každého kroku.

## <a name="details-on-the-create-terraform-tab"></a>Podrobnosti na kartě Vytvořit Terraform

Na kartě **Vytvořit Terraform** zadejte následující podrobnosti:

1. **Základy**
    
   * **Název**: Název virtuálního počítače Terraform.
   * **Uživatelské jméno**: ID první účet přihlášení.
   * **Heslo**: První heslo účtu. (Místo hesla můžete použít veřejný klíč SSH.)
   * **Předplatné**: Předplatné, ve které je vytvořené a fakturuje počítač. Toto předplatné musí mít oprávnění vytvářet prostředky.
   * **Skupina prostředků**: Nové nebo existující skupinu prostředků.
   * **Umístění**: Datacentrum, který nejlépe vyhovuje. (Obvykle jde o datacentrum s většinou dat nebo nejbližší datacentrum kvůli co nejrychlejšímu síťovému přístupu.)

2. **Další nastavení**

   * **Velikost**: Velikost virtuálního počítače. 
   * **Typ disku virtuálního počítače**: SSD nebo pevný disk.

3. **Souhrn Terraformu**

   * Ověřte správnost všech zadaných informací. 

4. **Koupě**

   * Pokud chcete proces zřizování spustit, vyberte **Koupit**. Zobrazí se odkaz na podmínky transakce. Virtuální počítač nebude kromě poplatků za výpočetní prostředky (podle velikosti serveru, kterou jste zvolili v kroku velikost) účtovat žádné další poplatky.

Image virtuálního počítače Terraform provede následující kroky:

* Vytvoří virtuální počítač s identitou přiřazenou systémem, která je založena na imagi Ubuntu 16.04 LTS.
* Nainstaluje na virtuální počítač rozšíření MSI, aby se prostředkům Azure mohly vydávat tokeny OAuth.
* Přiřadí spravované identitě oprávnění RBAC, která udělí skupině prostředků oprávnění vlastníka.
* Vytvoří složku pro šablonu Terraformu (tfTemplate).
* Předem nakonfiguruje vzdálený stav Terraformu s back-endem Azure.

## <a name="access-and-configure-a-linux-terraform-virtual-machine"></a>Přístup a konfigurace virtuálního počítače Terraform s Linuxem

Po vytvoření virtuálního počítače se k němu můžete pomocí SSH přihlásit. V rozhraní textového prostředí použijte přihlašovací údaje, které jste vytvořili v kroku 3 v části „Základy“. Ve Windows můžete stáhnout klientský nástroj SSH, jako je [Putty](http://www.putty.org/).

Až se pomocí SSH k virtuálnímu počítači připojíte, budete muset spravovaným identitám pro prostředky Azure na virtuálním počítači udělit oprávnění přispěvatele pro celé předplatné. 

Oprávnění přispěvatele pomůže identitě spravované služby na virtuálním počítači používat Terraform k vytváření prostředků mimo skupinu prostředků virtuálního počítače. Stačí k tomu jednou spustit skript. Použijte následující příkaz:

`. ~/tfEnv.sh`

Předchozí skript používá k ověření v Azure a přiřazení oprávnění přispěvatele pro celé předplatné spravované identitě na virtuálním počítači mechanismus [interaktivního přihlášení v Azure CLI 2.0](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest#interactive-log-in). 

 Virtuální počítač má back-end vzdáleného stavu Terraformu. Pokud ho chcete povolit při nasazení Terraformu, zkopírujte soubor remoteState.tf z adresáře tfTemplate do kořenového adresáře skriptů Terraformu.  

 `cp  ~/tfTemplate/remoteState.tf .`

 Další informace o vzdálené správě stavu najdete na [této stránce týkající se vzdáleného stavu Terraformu](https://www.terraform.io/docs/state/remote.html). Přístupový klíč úložiště je v tomto souboru zveřejněný a před zápisem konfiguračních souborů Terraformu do správy zdrojového kódu je potřeba ho vyloučit.

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak v Azure nastavit virtuální počítač Terraform s Linuxem. Pokud chcete o nástroji Terraform v Azure získat více informací, můžou vám pomoct následující prostředky: 

 [Dokumentace k Terraformu v Azure](https://docs.microsoft.com/azure/terraform/)  
 [Dokumentace k Terraformu zprostředkovatele Azure](https://aka.ms/terraform)  
 [Zdrojová data k Terraformu zprostředkovatele Azure](https://aka.ms/tfgit)  
 [Moduly Terraformu pro Azure](https://aka.ms/tfmodules)
 

















