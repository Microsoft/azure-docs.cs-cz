---
title: Vytvoření virtuálního počítače s Windows v zásobníku Azure pomocí rozhraní příkazového řádku Azure | Microsoft Docs
description: Naučte se vytvořit virtuální počítač s Windows v zásobníku Azure pomocí rozhraní příkazového řádku Azure
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: E26B246E-811D-44C9-9BA6-2B3CE5B62E83
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/19/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 5665af14b9b0d0705b68c8a27c593b19c31b053e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="quickstart-create-a-windows-virtual-machine-in-azure-stack-using-azure-cli"></a>Rychlý úvod: vytvoření virtuálního počítače s Windows v zásobníku Azure pomocí rozhraní příkazového řádku Azure

Rozhraní příkazového řádku Azure slouží k vytváření a správě prostředků Azure zásobníku z příkazového řádku. Tento článek ukazuje, jak používat rozhraní příkazového řádku Azure k vytváření a přístup k systému Windows Server 2016 virtuálního počítače v Azure zásobníku.

## <a name="prerequisites"></a>Požadavky

* Ujistěte se, že vaše operátor zásobník Azure přidal bitovou kopii "Windows Server 2016" do zásobníku Azure marketplace.

* Azure zásobníku vyžaduje konkrétní verzi rozhraní příkazového řádku Azure k vytváření a správě prostředků. Pokud nemáte nakonfigurován pro Azure zásobníku rozhraní příkazového řádku Azure, postupujte podle kroků pro [instalace a konfigurace rozhraní příkazového řádku Azure](azure-stack-version-profiles-azurecli2.md).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, do které zásobník Azure jsou nasadit a spravovat prostředky. Z vaší development kit nebo zásobník Azure integrovaný systém, [vytvořit skupinu az](/cli/azure/group#az_group_create) příkazu vytvořte skupinu prostředků. Hodnoty se přiřazují pro všechny proměnné v tomto dokumentu, můžete použít tyto hodnoty, nebo přiřadit nové hodnoty. Následující příklad vytvoří skupinu prostředků s názvem myResourceGroup do místního umístění.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvoření virtuálního počítače pomocí [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create) příkaz. Následující příklad vytvoří virtuální počítač s názvem můjvp přesměrovat. Tento příklad používá Demouser pro název správce a Demouser@123 jako heslo. Aktualizujte tyto hodnoty na nějaké vhodné pro vaše prostředí. Tyto hodnoty je třeba při připojení k virtuálnímu počítači.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "Win2016Datacenter" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

Když je vytvořen virtuální počítač, *PublicIPAddress* je výstupní parametr. Poznamenejte si tuto adresu, protože to přístup k virtuálnímu počítači potřebujete.

## <a name="open-port-80-for-web-traffic"></a>Otevření portu 80 pro webový provoz

Ve výchozím nastavení je povoleno pouze připojení RDP virtuálního počítače s Windows nasazené v Azure zásobníku. Pokud bude tento virtuální počítač webovým serverem, budete muset otevřít port 80 z internetu. Požadovaný port otevřete pomocí příkazu [az vm open-port](/cli/azure/vm#open-port).

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>Připojení k virtuálnímu počítači

Pomocí následujícího příkazu vytvořte s virtuálním počítačem relaci vzdálené plochy. IP adresu nahraďte veřejnou IP adresou vašeho virtuálního počítače. Po zobrazení výzvy zadejte přihlašovací údaje, které jste použili při vytváření virtuálního počítače.

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>Instalace služby IIS pomocí PowerShellu

Po přihlášení k virtuálnímu počítači Azure, můžete použít jeden řádek prostředí PowerShell k instalaci IIS a aktivace pravidla místní brány firewall pro povolení webový provoz. Otevřete příkazový řádek PowerShellu a spusťte následující příkaz:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Zobrazení úvodní stránky služby IIS

Chcete-li zobrazit výchozí úvodní stránka služby IIS, můžete použít webový prohlížeč podle svého výběru. Navštivte stránku výchozího použijte veřejnou IP adresu, které jsou popsané v předchozí části.

![Výchozí web služby IIS](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, můžete použít [odstranění skupiny az](/cli/azure/group#az_group_delete) příkaz, který má odeberte skupinu zdrojů, virtuální počítač, a všechny související prostředky.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další postup

V tento rychlý start jste nasadili jednoduchého virtuálního počítače Windows. Další informace o virtuálních počítačích Azure zásobníku, nadále [důležité informace pro virtuální počítače v Azure zásobníku](azure-stack-vm-considerations.md).
