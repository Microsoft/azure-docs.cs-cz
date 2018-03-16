---
title: "Sadách škálování virtuálních počítačů zkontrolujte dostupné v zásobníku Azure | Microsoft Docs"
description: "Zjistěte, jak přidat škálování virtuálních počítačů do Azure Marketplace zásobníku operátor cloudu"
services: azure-stack
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.topic: article
ms.date: 03/13/2018
ms.author: brenduns
ms.reviewer: anajod
keywords: 
ms.openlocfilehash: a4c854bdd659a05f032f5ee232074bc38ff677ef
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>Zpřístupnit sady škálování virtuálního počítače v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Sady škálování virtuálního počítače jsou výpočetní prostředek Azure zásobníku. Můžete je používat k nasazení a správě sadu identickými virtuálními počítači. S všechny virtuální počítače nakonfigurované stejně, nevyžadují předem zřizování virtuálních počítačů sady škálování. Aby bylo jednodušší vytváření rozsáhlých služeb, které se zaměřují velkých výpočetních, velké objemy dat a kontejnerizované úlohy.

Tento článek vás provede procesem vytvoření sady škálování dostupné v Azure Marketplace zásobníku. Po dokončení tohoto postupu můžete přidat uživatele sady škálování virtuálních počítačů do svých předplatných.

Sady škálování virtuálního počítače v Azure zásobníku jsou stejné jako sady škálování virtuálního počítače na platformě Azure. Další informace najdete na následující videa:
* [Mark Russinovich hovoří o škálovacích sadách Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [Guy Bowerman provádí škálovacími sadami virtuálních počítačů](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

Sady škálování virtuálního počítače v Azure zásobníku nepodporují automatické škálování. Můžete přidat více instancí škálování, nastavit pomocí portálu Azure zásobníku, šablony Resource Manageru nebo prostředí PowerShell.

## <a name="prerequisites"></a>Požadavky
* **Prostředí PowerShell a nástroje**

   Instalace a nakonfigurované prostředí PowerShell pro Azure zásobníku a nástroje Azure zásobníku. V tématu [zprovoznění pomocí prostředí PowerShell v zásobníku Azure](azure-stack-powershell-configure-quickstart.md).

   Po instalaci nástroje Azure zásobníku, ujistěte se, importujte modul PowerShell následující (cesta vzhledem k. \ComputeAdmin složky ve složce AzureStack. nástroje pro hlavní):

        Import-Module .\AzureStack.ComputeAdmin.psm1

* **Bitové kopie operačního systému**

   Nepřidali jste image operačního systému vaší Azure Marketplace zásobníku, najdete v části [přidat bitovou kopii virtuálního počítače Windows serveru 2016 do zásobníku Azure marketplace](azure-stack-add-default-image.md).

   Podpora systému Linux, stáhněte Ubuntu Server 16.04 a přidejte ho pomocí ```Add-AzsVMImage``` s následujícími parametry: ```-publisher "Canonical" -offer "UbuntuServer" -sku "16.04-LTS"```.

## <a name="add-the-virtual-machine-scale-set"></a>Přidat sady škálování virtuálního počítače

Upravte následující skript prostředí PowerShell pro vaše prostředí a spusťte jej přidejte škálování virtuálních počítačů, nastavte na vaše Azure Marketplace zásobníku. 

``$User`` je účet, který používáte pro připojení správce portálu. Například, serviceadmin@contoso.onmicrosoft.com.

```
$Arm = "https://adminmanagement.local.azurestack.external"
$Location = "local"

Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint $Arm

$Password = ConvertTo-SecureString -AsPlainText -Force "<your Azure Stack administrator password>"

$User = "<your Azure Stack service administrator user name>"

$Creds =  New-Object System.Management.Automation.PSCredential $User, $Password

$AzsEnv = Get-AzureRmEnvironment AzureStackAdmin
$AzsEnvContext = Add-AzureRmAccount -Environment $AzsEnv -Credential $Creds

Select-AzureRmSubscription -SubscriptionName "Default Provider Subscription"

Add-AzsVMSSGalleryItem -Location $Location
```

## <a name="remove-a-virtual-machine-scale-set"></a>Odstranit škálovací sadu virtuálních počítačů

Odebrat virtuální počítač škálování položky galerie sady, spusťte následující příkaz prostředí PowerShell:

    Remove-AzsVMSSGalleryItem

> [!NOTE]
> Položky galerie nemusí být okamžitě odstraněna. Noční potřebujete aktualizujte portál několikrát, než položka ukazuje, jak odebrat z Marketplace.


## <a name="next-steps"></a>Další postup
[Nejčastější dotazy pro Azure zásobníku](azure-stack-faq.md)

