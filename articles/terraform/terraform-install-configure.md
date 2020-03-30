---
title: Úvodní příručka – instalace a konfigurace Terraformu pro zřizování prostředků Azure
description: V tomto quicstart nainstalujete a nakonfigurujete Terraform k vytvoření prostředků Azure
keywords: konfigurace instalace terraform azure devops
ms.topic: quickstart
ms.date: 03/09/2020
ms.openlocfilehash: 82635f59ec8165add2046a230a040b06f89d9898
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78943507"
---
# <a name="quickstart-install-and-configure-terraform-to-provision-azure-resources"></a>Úvodní příručka: Instalace a konfigurace Terraformu pro zřizování prostředků Azure
 
Terraform poskytuje snadný způsob, jak definovat, zobrazit náhled a nasadit cloudovou infrastrukturu pomocí [jednoduchého šablonovacího jazyka](https://www.terraform.io/docs/configuration/syntax.html). Tento článek popisuje nezbytné kroky k použití Terraform zřídit prostředky v Azure.

Další informace o používání Terraformu s Azure najdete v [centru Terraform Hub](/azure/terraform).
> [!NOTE]
> Pro podporu specifickou pro Terraform se prosím obraťte na Terraform přímo pomocí jednoho z jejich komunitních kanálů:
>
>    * [Část Terraform](https://discuss.hashicorp.com/c/terraform-core) na komunitním portálu obsahuje otázky, případy použití a užitečné vzory.
>
>    * Otázky týkající se poskytovatele naleznete v části [Terraform Providers](https://discuss.hashicorp.com/c/terraform-providers) na komunitním portálu.



[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Terraform je ve výchozím nastavení nainstalován v [prostředí Cloud Shell](/azure/terraform/terraform-cloud-shell). Pokud se rozhodnete terraform nainstalovat místně, dokončete další krok, jinak pokračujte [v nastavení přístupu Terraform k Azure](#set-up-terraform-access-to-azure).

## <a name="install-terraform"></a>Instalace Terraform

Chcete-li nainstalovat Terraform, [stáhněte](https://www.terraform.io/downloads.html) příslušný balíček pro operační systém do samostatného instalačního adresáře. Stahování obsahuje jeden spustitelný soubor, pro který byste měli také definovat globální cestu. Pokyny k nastavení cesty na Linuxu a Macu najdete na [této webové stránce](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Pokyny k nastavení cesty v systému Windows naleznete na [této webové stránce](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows).

Ověřte konfiguraci `terraform` cesty pomocí příkazu. Zobrazí se seznam dostupných možností Terraform, jako v následujícím příkladu výstupu:

```console
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Nastavení přístupu Terraform do Azure

Chcete-li terraform zřídit prostředky do Azure, vytvořte [instanční objekt služby Azure AD](/cli/azure/create-an-azure-service-principal-azure-cli). Hlavní servisní uděluje skripty Terraform pro zřizování prostředků ve vašem předplatném Azure.

Pokud máte více předplatných Azure, nejprve se dotazujte na svůj účet se [seznamem účtů az,](/cli/azure/account#az-account-list) abyste získali seznam id předplatného a hodnot ID klienta:

```azurecli-interactive
az account list --query "[].{name:name, subscriptionId:id, tenantId:tenantId}"
```

Chcete-li použít vybrané předplatné, nastavte předplatné pro tuto relaci se [sadou účtu az](/cli/azure/account#az-account-set). Nastavte `SUBSCRIPTION_ID` proměnnou prostředí tak, aby `id` uchovávala hodnotu vráceného pole z předplatného, které chcete použít:

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Nyní můžete vytvořit instanční objekt pro použití s Terraform. Použijte [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac)a nastavte *obor* pro vaše předplatné takto:

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

Vaše `appId` `password`, `sp_name`, `tenant` a jsou vráceny. Poznamenejte `appId` si `password`a .

## <a name="configure-terraform-environment-variables"></a>Konfigurace proměnných prostředí Terraform

Chcete-li nakonfigurovat Terraform tak, aby používal váš instanční objekt služby Azure AD, nastavte následující proměnné prostředí, které pak používají [moduly Azure Terraform](https://registry.terraform.io/modules/Azure). Prostředí můžete nastavit taky, pokud pracujete s jiným cloudem Azure, než je Azure Public.

- `ARM_SUBSCRIPTION_ID`
- `ARM_CLIENT_ID`
- `ARM_CLIENT_SECRET`
- `ARM_TENANT_ID`
- `ARM_ENVIRONMENT`

Tyto proměnné můžete nastavit pomocí následujícího ukázkového skriptu prostředí:

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id

# Not needed for public, required for usgovernment, german, china
export ARM_ENVIRONMENT=public
```

## <a name="run-a-sample-script"></a>Spuštění ukázkového skriptu

Vytvořte `test.tf` soubor v prázdném adresáři a vložte do následujícího skriptu.

```hcl
provider "azurerm" {
  # The "feature" block is required for AzureRM provider 2.x. 
  # If you are using version 1.x, the "features" block is not allowed.
  version = "~>2.0"
  features {}
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Uložte soubor a potom inicializovat nasazení Terraform. Tento krok stáhne moduly Azure potřebné k vytvoření skupiny prostředků Azure.

```bash
terraform init
```

Výstup se podobá následujícímu příkladu:

```console
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Můžete zobrazit náhled akcí, které mají být `terraform plan`dokončeny skriptem Terraform s . Až budete připraveni vytvořit skupinu prostředků, použijte plán Terraform následujícím způsobem:

```bash
terraform apply
```

Výstup se podobá následujícímu příkladu:

```console
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste nainstalovali Terraform nebo pomocí Cloud Shell ukonfigurace přihlašovacích údajů Azure a začít vytvářet prostředky ve vašem předplatném Azure. Pokud chcete vytvořit úplnější nasazení Terraform v Azure, přečtěte si následující článek:

> [!div class="nextstepaction"]
> [Vytvoření virtuálního počítače Azure s terraformem](terraform-create-complete-vm.md)