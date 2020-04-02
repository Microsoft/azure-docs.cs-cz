---
title: Připojení virtuálního počítače s Windows k Azure AD DS pomocí šablony | Dokumenty společnosti Microsoft
description: Přečtěte si, jak pomocí šablon Azure Resource Managerpřipojit nový nebo stávající virtuální počítač se systémem Windows Server ke spravované doméně služby Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 19e4033b462cda9511ee45420bd4c1a76cc36b92
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80518959"
---
# <a name="join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain-using-a-resource-manager-template"></a>Připojení virtuálního počítače se systémem Windows Server ke spravované doméně služby Azure Active Directory Domain Services pomocí šablony Správce prostředků

K automatizaci nasazení a konfigurace virtuálních počítačů Azure můžete použít šablonu Správce prostředků. Tyto šablony umožňují vytvářet konzistentní nasazení pokaždé. Rozšíření mohou být také zahrnuty v šablonách automaticky konfigurovat virtuální ho virtuálního počítače jako součást nasazení. Jedno užitečné rozšíření spojuje virtuální počítače s doménou, kterou lze použít se spravovanými doménami Služby Azure Active Directory Domain Services (Azure AD DS).

Tento článek ukazuje, jak vytvořit a připojit virtuální počítač windows serveru ke spravované doméně Azure AD DS pomocí šablon Správce prostředků. Dozvíte se také, jak připojit existující virtuální počítač se systémem Windows Server k doméně Azure AD DS.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte si účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, synchronizovaný s místním adresářem nebo s adresářem pouze pro cloud.
    * V případě potřeby [vytvořte klienta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure ke svému účtu][associate-azure-ad-tenant].
* Spravovaná doména Služby Azure Active Directory Domain Services povolená a nakonfigurovaná ve vašem tenantovi Azure AD.
    * V případě potřeby první kurz [vytvoří a nakonfiguruje instanci služby Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Uživatelský účet, který je součástí spravované domény Azure AD DS.

## <a name="azure-resource-manager-template-overview"></a>Přehled šablon Azure Resource Manageru

Šablony Správce prostředků umožňují definovat infrastrukturu Azure v kódu. Požadované prostředky, síťová připojení nebo konfigurace virtuálních počítačů lze definovat v šabloně. Tyto šablony vytvořit konzistentní, reprodukovatelné nasazení pokaždé a může být verzí při provádění změn. Další informace najdete v tématu [Přehled šablon Azure Resource Manager .][template-overview]

Každý prostředek je definován v šabloně pomocí JavaScript unotace objektu (JSON). Následující příklad JSON používá typ prostředku *Microsoft.Compute/virtualMachines/extensions* k instalaci rozšíření připojení domény služby Active Directory. Parametry se používají, které zadáte v době nasazení. Když se rozšíření nasadí, virtuální počítač se připojí k zadané spravované doméně Azure AD DS.

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

Toto rozšíření virtuálního počítače můžete nasadit i v případě, že nevytvoříte virtuální ho ve stejné šabloně. Příklady v tomto článku ukazují oba následující přístupy:

* [Vytvoření virtuálního virtuálního vstupu systému Windows Server a připojení ke spravované doméně](#create-a-windows-server-vm-and-join-to-a-managed-domain)
* [Připojení existujícího virtuálního virtuálního virtuálního ms systému Windows Server ke spravované doméně](#join-an-existing-windows-server-vm-to-a-managed-domain)

## <a name="create-a-windows-server-vm-and-join-to-a-managed-domain"></a>Vytvoření virtuálního virtuálního vstupu systému Windows Server a připojení ke spravované doméně

Pokud potřebujete virtuální virtuální počítače se systémem Windows Server, můžete ho vytvořit a nakonfigurovat pomocí šablony Správce prostředků. Když se virtuální počítač nasadí, namontuje se rozšíření, které připojí virtuální počítač ke spravované doméně Azure AD DS. Pokud už máte virtuální počítač, ke kterým se chcete připojit ke spravované doméně Azure AD DS, přeskočte na [Připojit se k existujícímu virtuálnímu počítači windows serveru ke spravované doméně](#join-an-existing-windows-server-vm-to-a-managed-domain).

Chcete-li vytvořit virtuální počítač se systémem Windows Server a připojit ho ke spravované doméně Služby Azure AD DS, proveďte následující kroky:

1. Přejděte k [šabloně rychlého startu](https://azure.microsoft.com/resources/templates/201-vm-domain-join/). Vyberte možnost **nasazení do Azure**.
1. Na stránce **Vlastní nasazení** zadejte následující informace pro vytvoření a připojení virtuálního počítače se systémem Windows Server ke spravované doméně Azure AD DS:

    | Nastavení                   | Hodnota |
    |---------------------------|-------|
    | Předplatné              | Vyberte stejné předplatné Azure, ve kterém jste povolili služby Azure AD Domain Services. |
    | Skupina prostředků            | Vyberte skupinu prostředků pro váš virtuální počítač. |
    | Umístění                  | Vyberte umístění virtuálního počítače. |
    | Existující název virtuální sítě        | Název existující virtuální sítě pro připojení virtuálního počítače, jako je například *myVnet*. |
    | Název existující podsítě      | Název existující podsítě virtuální sítě, například *Úlohy*. |
    | Předpona popisku DNS          | Zadejte název DNS, který se má použít pro virtuální hod, například *myvm*. |
    | Velikost virtuálního počítače                   | Zadejte velikost virtuálního počítače, například *Standard_DS2_v2*. |
    | Doména, ke které se chcete připojit            | Název DNS spravované domény služby Azure AD DS, například *aaddscontoso.com*. |
    | Uživatelské jméno domény           | Uživatelský účet ve spravované doméně Azure AD DS, který by se měl `contosoadmin@aaddscontoso.com`použít k připojení virtuálního počítače ke spravované doméně, například . Tento účet musí být součástí spravované domény Azure AD DS. |
    | Heslo domény           | Heslo pro uživatelský účet zadané v předchozím nastavení. |
    | Volitelná cesta ou          | Vlastní ou, ve kterém chcete přidat virtuální ho. Pokud nezadáte hodnotu pro tento parametr, virtuální počítač se přidá do výchozí ou *položky počítače Řadič eAD DC.* |
    | Uživatelské jméno správce virtuálního uživatele virtuálního uživatele         | Zadejte účet místního správce, který chcete vytvořit na virtuálním počítači. |
    | Heslo správce virtuálního počítače         | Zadejte heslo místního správce pro virtuální počítače. Vytvořte silné heslo místního správce, které chrání před útoky hrubou silou hesla. |

1. Přečtěte si podmínky a zaškrtávací políčko, protože **souhlasím s výše uvedenými podmínkami**. Až budete připraveni, vyberte **Nákup** a vytvořte a připojte virtuální počítač ke spravované doméně Azure AD DS.

> [!WARNING]
> **Hesla zacházejte opatrně.**
> Soubor parametru šablony požaduje heslo pro uživatelský účet, který je součástí spravované domény Azure AD DS. Nezadávejte do tohoto souboru ručně hodnoty a ponechte je přístupné ve sdílených složek nebo v jiných sdílených umístěních.

Dokončení nasazení trvá několik minut. Po dokončení virtuálního počítače s Windows se vytvoří a připojí ke spravované doméně Azure AD DS. Virtuální ho virtuálního počítačů se dá spravovat nebo přiživovat pomocí účtů domény.

## <a name="join-an-existing-windows-server-vm-to-a-managed-domain"></a>Připojení existujícího virtuálního virtuálního virtuálního ms systému Windows Server ke spravované doméně

Pokud máte existující virtuální počítač nebo skupinu virtuálních počítačů, které chcete připojit ke spravované doméně Azure AD DS, můžete použít šablonu Správce prostředků k nasazení rozšíření virtuálního počítače.

Chcete-li připojit existující virtuální počítač se systémem Windows Server ke spravované doméně Azure AD DS, proveďte následující kroky:

1. Přejděte k [šabloně rychlého startu](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/). Vyberte možnost **nasazení do Azure**.
1. Na stránce **Vlastní nasazení** zadejte následující informace pro připojení virtuálního počítače ke spravované doméně Azure AD DS:

    | Nastavení                   | Hodnota |
    |---------------------------|-------|
    | Předplatné              | Vyberte stejné předplatné Azure, ve kterém jste povolili služby Azure AD Domain Services. |
    | Skupina prostředků            | Zvolte skupinu prostředků s existujícím virtuálním počítačem. |
    | Umístění                  | Vyberte umístění existujícího virtuálního počítače. |
    | Seznam virtuálních mís                   | Zadejte seznam existujících virtuálních počítače oddělených čárkami, abyste se připojili ke spravované doméně Azure AD DS, jako je *myVM1,myVM2*. |
    | Uživatelské jméno připojení k doméně     | Uživatelský účet ve spravované doméně Azure AD DS, který by se měl `contosoadmin@aaddscontoso.com`použít k připojení virtuálního počítače ke spravované doméně, například . Tento účet musí být součástí spravované domény Azure AD DS. |
    | Heslo uživatele připojení k doméně | Heslo pro uživatelský účet zadané v předchozím nastavení. |
    | Volitelná cesta ou          | Vlastní ou, ve kterém chcete přidat virtuální ho. Pokud nezadáte hodnotu pro tento parametr, virtuální počítač se přidá do výchozí ou *položky počítače Řadič eAD DC.* |

1. Přečtěte si podmínky a zaškrtávací políčko, protože **souhlasím s výše uvedenými podmínkami**. Až budete připraveni, vyberte **Nákup,** chcete-li připojit virtuální počítač ke spravované doméně Azure AD DS.

> [!WARNING]
> **Hesla zacházejte opatrně.**
> Soubor parametru šablony požaduje heslo pro uživatelský účet, který je součástí spravované domény Azure AD DS. Nezadávejte do tohoto souboru ručně hodnoty a ponechte je přístupné ve sdílených složek nebo v jiných sdílených umístěních.

Trvá několik okamžiků pro nasazení úspěšně dokončit. Po dokončení zadaných virtuálních počítačů s Windows jsou připojeny ke spravované doméně Azure AD DS a lze spravovat nebo přihlášeni pomocí účtů domény.

## <a name="next-steps"></a>Další kroky

V tomto článku jste použili portál Azure ke konfiguraci a nasazení prostředků pomocí šablon. Prostředky můžete nasadit také pomocí šablon Správce prostředků pomocí [Azure PowerShellu][deploy-powershell] nebo [azure cli][deploy-cli].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[template-overview]: ../azure-resource-manager/templates/overview.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
