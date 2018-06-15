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
ms.date: 04/23/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 381c1c37b0675d97adc058979a5d9b5c4fd2cc8b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32155152"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Rychlý úvod: vytvoření virtuálního počítače Windows serveru pomocí rozhraní příkazového řádku Azure v zásobníku Azure

‎*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Virtuální počítač systému Windows Server 2016 můžete vytvořit pomocí rozhraní příkazového řádku Azure. Postupujte podle kroků v tomto článku vytváření a používání virtuálního počítače. Tento článek také poskytuje následující kroky:

* Připojte k virtuálnímu počítači pomocí vzdáleného klienta.
* Nainstalovat webový server IIS a zobrazit výchozí domovskou stránku.
* Vyčistěte vašich prostředků.

## <a name="prerequisites"></a>Požadavky

* Ujistěte se, že vaše zásobník Azure operátor přidán **systému Windows Server 2016** bitovou kopii do zásobníku Azure marketplace.

* Azure zásobníku vyžaduje konkrétní verzi rozhraní příkazového řádku Azure k vytváření a správě prostředků. Pokud nemáte nakonfigurován pro Azure zásobníku rozhraní příkazového řádku Azure, postupujte podle kroků pro [instalace a konfigurace rozhraní příkazového řádku Azure](azure-stack-version-profiles-azurecli2.md).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, kde můžete nasadit a spravovat prostředky Azure zásobníku. Z prostředí zásobníku Azure spustit [vytvořit skupinu az](/cli/azure/group#az_group_create) příkazu vytvořte skupinu prostředků.

>[!NOTE]
 Hodnoty se přiřazují pro všechny proměnné v příklady kódu. Pokud chcete, můžete však přiřadit nové hodnoty.

Následující příklad vytvoří skupinu prostředků s názvem myResourceGroup do místního umístění.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvoření virtuálního počítače (VM) pomocí [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create) příkaz. Následující příklad vytvoří virtuální počítač s názvem můjvp přesměrovat. Tento příklad používá Demouser pro název správce a Demouser@123 jako heslo uživatele. Tyto hodnoty změňte na něco, co je vhodné pro vaše prostředí.

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

Když je vytvořen virtuální počítač, **PublicIPAddress** parametr ve výstupu obsahuje veřejnou IP adresu pro virtuální počítač. Poznamenejte si tuto adresu, protože je nutné ho pro přístup k virtuálnímu počítači.

## <a name="open-port-80-for-web-traffic"></a>Otevření portu 80 pro webový provoz

Protože tento virtuální počítač bude běžet webovém serveru IIS, budete muset otevřít port 80 pro přenosy z Internetu.

Použití [az virtuálních počítačů open-port](/cli/azure/vm#open-port) příkaz pro otevření portu 80.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>Připojení k virtuálnímu počítači

Použijte další příkaz k vytvoření připojení vzdálené plochy k virtuálnímu počítači. Nahraďte adresu IP virtuálního počítače "Veřejná IP adresa". Po zobrazení výzvy zadejte uživatelské jméno a heslo, které jste použili pro virtuální počítač.

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>Instalace služby IIS pomocí PowerShellu

Teď, když jste přihlášení k virtuálnímu počítači, můžete použít PowerShell k instalaci služby IIS. Spusťte prostředí PowerShell na virtuálním počítači a spusťte následující příkaz:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Zobrazení úvodní stránky služby IIS

Chcete-li zobrazit výchozí úvodní stránka služby IIS, můžete použít webový prohlížeč podle svého výběru. Navštivte stránku výchozího použijte veřejnou IP adresu, které jsou popsané v předchozí části.

![Výchozí web služby IIS](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Vyčistěte prostředky, které už nepotřebujete. Použití [odstranění skupiny az](/cli/azure/group#az_group_delete) příkaz, který má odeberte skupinu zdrojů, virtuální počítač, a všechny související prostředky.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další postup

V tento rychlý start jste nasadili základní virtuální počítač Windows serveru. Další informace o virtuálních počítačích Azure zásobníku, nadále [důležité informace pro virtuální počítače v Azure zásobníku](azure-stack-vm-considerations.md).
