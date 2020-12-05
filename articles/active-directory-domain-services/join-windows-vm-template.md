---
title: Použití šablony k připojení virtuálního počítače s Windows k Azure služba AD DS | Microsoft Docs
description: Naučte se používat šablony Azure Resource Manager k připojení k novému nebo existujícímu virtuálnímu počítači s Windows serverem k spravované doméně Azure Active Directory Domain Services.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: e7245e8e468ea051ee095d97cc250ad303aa80a5
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619432"
---
# <a name="join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain-using-a-resource-manager-template"></a>Připojení virtuálního počítače s Windows serverem k Azure Active Directory Domain Services spravované doméně pomocí šablony Správce prostředků

K automatizaci nasazení a konfigurace virtuálních počítačů Azure můžete použít šablonu Správce prostředků. Tyto šablony umožňují kdykoli vytvořit konzistentní nasazení. V šablonách je taky možné zahrnout rozšíření a automaticky tak nakonfigurovat virtuální počítač jako součást nasazení. Jedna z užitečných rozšíření spojuje virtuální počítače s doménou, které se dají používat se spravovanými doménami Azure Active Directory Domain Services (Azure služba AD DS).

V tomto článku se dozvíte, jak vytvořit virtuální počítač s Windows serverem a připojit ho k spravované doméně Azure služba AD DS pomocí šablon Správce prostředků. Naučíte se také, jak připojit stávající virtuální počítač s Windows serverem k doméně Azure služba AD DS.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Musíte mít aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Ve vašem tenantovi Azure AD je povolená a nakonfigurovaná spravovaná doména Azure Active Directory Domain Services.
    * V případě potřeby se v prvním kurzu [vytvoří a nakonfiguruje Azure Active Directory Domain Services spravovaná doména][create-azure-ad-ds-instance].
* Uživatelský účet, který je součástí spravované domény.

## <a name="azure-resource-manager-template-overview"></a>Přehled šablony Azure Resource Manager

Správce prostředků šablony vám umožní definovat infrastrukturu Azure v kódu. Požadované prostředky, síťová připojení nebo konfigurace virtuálních počítačů je možné definovat v šabloně. Tyto šablony vytvářejí konzistentní a reprodukovatelná nasazení pokaždé a můžou být ve stejném znění jako při provádění změn. Další informace najdete v tématu [Přehled šablon Azure Resource Manager][template-overview].

Každý prostředek je definován v šabloně pomocí JavaScript Object Notation (JSON). Následující příklad JSON používá typ prostředku *Microsoft. COMPUTE/virtualMachines/Extensions* k instalaci rozšíření připojení k doméně služby Active Directory. Používají se parametry, které zadáte v době nasazení. Po nasazení rozšíření je virtuální počítač připojený k zadané spravované doméně.

```json
 {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('dnsLabelPrefix'),'/joindomain')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('dnsLabelPrefix'))]"
      ],
      "properties": {
        "publisher": "Microsoft.Compute",
        "type": "JsonADDomainExtension",
        "typeHandlerVersion": "1.3",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "Name": "[parameters('domainToJoin')]",
          "OUPath": "[parameters('ouPath')]",
          "User": "[concat(parameters('domainToJoin'), '\\', parameters('domainUsername'))]",
          "Restart": "true",
          "Options": "[parameters('domainJoinOptions')]"
        },
        "protectedSettings": {
          "Password": "[parameters('domainPassword')]"
        }
      }
    }
```

Toto rozšíření virtuálního počítače se dá nasadit i v případě, že virtuální počítač nevytvoříte ve stejné šabloně. Příklady v tomto článku znázorňují oba následující přístupy:

* [Vytvoření virtuálního počítače s Windows serverem a připojení ke spravované doméně](#create-a-windows-server-vm-and-join-to-a-managed-domain)
* [Připojit stávající virtuální počítač s Windows serverem ke spravované doméně](#join-an-existing-windows-server-vm-to-a-managed-domain)

## <a name="create-a-windows-server-vm-and-join-to-a-managed-domain"></a>Vytvoření virtuálního počítače s Windows serverem a připojení ke spravované doméně

Pokud potřebujete virtuální počítač s Windows serverem, můžete ho vytvořit a nakonfigurovat pomocí šablony Správce prostředků. Po nasazení virtuálního počítače se nainstaluje rozšíření, které se připojí k virtuálnímu počítači do spravované domény. Pokud už máte virtuální počítač, který chcete připojit ke spravované doméně, přejděte k [virtuálnímu počítači s Windows serverem a připojte se ke spravované doméně](#join-an-existing-windows-server-vm-to-a-managed-domain).

Pokud chcete vytvořit virtuální počítač s Windows serverem, připojte ho ke spravované doméně a proveďte následující kroky:

1. Přejděte k [šabloně pro rychlé](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)zprovoznění. Vyberte možnost **nasazení do Azure**.
1. Na stránce **vlastní nasazení** zadejte následující informace, chcete-li vytvořit virtuální počítač s Windows serverem a připojit ho k spravované doméně:

    | Nastavení                   | Hodnota |
    |---------------------------|-------|
    | Předplatné              | Vyberte stejné předplatné Azure, ve kterém jste povolili Azure AD Domain Services. |
    | Skupina prostředků            | Vyberte skupinu prostředků pro váš virtuální počítač. |
    | Umístění                  | Vyberte umístění pro váš virtuální počítač. |
    | Název existující virtuální sítě        | Název existující virtuální sítě, ke které se má virtuální počítač připojit, například *myVnet*. |
    | Existující název podsítě      | Název existující podsítě virtuální sítě, jako jsou například *úlohy*. |
    | Předpona popisku DNS          | Zadejte název DNS, který chcete pro virtuální počítač použít, například *myvm*. |
    | Velikost virtuálního počítače                   | Zadejte velikost virtuálního počítače, například *Standard_DS2_v2*. |
    | Doména pro připojení            | Název DNS spravované domény, například *aaddscontoso.com*. |
    | Uživatelské jméno domény           | Uživatelský účet ve spravované doméně, který se má použít k připojení virtuálního počítače ke spravované doméně, například `contosoadmin@aaddscontoso.com` . Tento účet musí být součástí spravované domény. |
    | Heslo domény           | Heslo pro uživatelský účet zadané v předchozím nastavení |
    | Volitelná cesta organizační jednotky          | Vlastní organizační jednotka, do které se má virtuální počítač přidat Pokud nezadáte hodnotu pro tento parametr, virtuální počítač se přidá do výchozí organizační jednotky *řadiče domény AAD* . |
    | Uživatelské jméno správce virtuálního počítače         | Zadejte účet místního správce, který se má vytvořit na virtuálním počítači. |
    | Heslo správce virtuálního počítače         | Zadejte heslo místního správce pro virtuální počítač. Vytvořte silné heslo místního správce pro ochranu proti útokům hrubou silou hesla. |

1. Přečtěte si podmínky a ujednání a potom zaškrtněte políčko pro souhlasím **s podmínkami a ujednáními uvedenými nahoře**. Až budete připraveni, vyberte **koupit** , aby se virtuální počítač vytvořil a připojil k spravované doméně.

> [!WARNING]
> **Pořídí hesla s opatrností.**
> Soubor parametrů šablony požaduje heslo pro uživatelský účet, který je součástí spravované domény. Do tohoto souboru neměňte ručně hodnoty a nechte ho přístupný pro sdílené složky nebo jiná sdílená umístění.

Úspěšné dokončení nasazení trvá několik minut. Po dokončení se virtuální počítač s Windows vytvoří a připojí se ke spravované doméně. Virtuální počítač se dá spravovat nebo přihlásit k používání doménových účtů.

## <a name="join-an-existing-windows-server-vm-to-a-managed-domain"></a>Připojit stávající virtuální počítač s Windows serverem ke spravované doméně

Pokud máte existující virtuální počítač nebo skupinu virtuálních počítačů, které chcete připojit ke spravované doméně, můžete rozšíření virtuálního počítače nasadit jenom pomocí šablony Správce prostředků.

Pokud se chcete připojit k existujícímu virtuálnímu počítači s Windows serverem ke spravované doméně, proveďte následující kroky:

1. Přejděte k [šabloně pro rychlé](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)zprovoznění. Vyberte možnost **nasazení do Azure**.
1. Na stránce **vlastní nasazení** zadejte následující informace, které se připojí k virtuálnímu počítači ke spravované doméně:

    | Nastavení                   | Hodnota |
    |---------------------------|-------|
    | Předplatné              | Vyberte stejné předplatné Azure, ve kterém jste povolili Azure AD Domain Services. |
    | Skupina prostředků            | Vyberte skupinu prostředků s existujícím VIRTUÁLNÍm počítačem. |
    | Umístění                  | Vyberte umístění existujícího virtuálního počítače. |
    | Seznam virtuálních počítačů                   | Zadejte čárkami oddělený seznam existujících virtuálních počítačů, které se mají připojit ke spravované doméně, například *myVM1, myVM2*. |
    | Uživatelské jméno pro připojení k doméně     | Uživatelský účet ve spravované doméně, který se má použít k připojení virtuálního počítače ke spravované doméně, například `contosoadmin@aaddscontoso.com` . Tento účet musí být součástí spravované domény. |
    | Uživatelské heslo pro připojení k doméně | Heslo pro uživatelský účet zadané v předchozím nastavení |
    | Volitelná cesta organizační jednotky          | Vlastní organizační jednotka, do které se má virtuální počítač přidat Pokud nezadáte hodnotu pro tento parametr, virtuální počítač se přidá do výchozí organizační jednotky *řadiče domény AAD* . |

1. Přečtěte si podmínky a ujednání a potom zaškrtněte políčko pro souhlasím **s podmínkami a ujednáními uvedenými nahoře**. Až budete připraveni, vyberte **koupit** a připojte se k virtuálnímu počítači ke spravované doméně.

> [!WARNING]
> **Pořídí hesla s opatrností.**
> Soubor parametrů šablony požaduje heslo pro uživatelský účet, který je součástí spravované domény. Do tohoto souboru neměňte ručně hodnoty a nechte ho přístupný pro sdílené složky nebo jiná sdílená umístění.

Úspěšné dokončení nasazení může chvíli trvat. Po dokončení jsou zadané virtuální počítače s Windows připojené ke spravované doméně a dají se spravovat nebo přihlašování k používání doménových účtů.

## <a name="next-steps"></a>Další kroky

V tomto článku jste použili Azure Portal ke konfiguraci a nasazení prostředků pomocí šablon. Prostředky můžete také nasadit pomocí Správce prostředků šablon pomocí [Azure PowerShell][deploy-powershell] nebo rozhraní příkazového [řádku Azure CLI][deploy-cli].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[template-overview]: ../azure-resource-manager/templates/overview.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
