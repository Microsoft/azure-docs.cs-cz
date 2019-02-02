---
title: Vytvoření virtuálního počítače s Windows ve službě Azure Stack pomocí rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit virtuální počítač s Windows ve službě Azure Stack pomocí Azure CLI
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/14/2019
ms.author: mabrigg
ms.custom: mvc
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: d2e29e183ab898635f550290b6ebd6eddd42c4d2
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55659197"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Rychlý start: vytvoření virtuálního počítače s Windows serverem pomocí Azure CLI ve službě Azure Stack

‎*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Virtuální počítač Windows Server 2016 můžete vytvořit pomocí rozhraní příkazového řádku Azure. Postupujte podle kroků v tomto článku, jak vytvořit a používat virtuální počítač. Tento článek také obsahuje následující kroky:

* Připojení k virtuálnímu počítači pomocí vzdáleného klienta.
* Instalace webového serveru služby IIS a zobrazit výchozí domovskou stránku.
* Vyčištění prostředků.

## <a name="prerequisites"></a>Požadavky

* Ujistěte se, že operátor Azure stacku přidána **Windows serveru 2016** image na marketplace služby Azure Stack.

* Azure Stack vyžaduje určitou verzi rozhraní příkazového řádku Azure můžete vytvořit a spravovat prostředky. Pokud nemáte nakonfigurované pro službu Azure Stack rozhraní příkazového řádku Azure, postupujte podle kroků pro [instalace a konfigurace rozhraní příkazového řádku Azure](azure-stack-version-profiles-azurecli2.md).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, ve kterém můžete nasadit a spravovat prostředky služby Azure Stack. Z vašeho prostředí Azure Stack, spusťte [vytvořit skupiny az](/cli/azure/group#az-group-create) příkazu vytvořte skupinu prostředků.

>[!NOTE]
 Hodnoty jsou přiřazeny pro všechny proměnné v příkladech kódu. Pokud chcete, ale můžete přiřadit nové hodnoty.

Následující příklad vytvoří skupinu prostředků myResourceGroup v místním umístění.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvoření virtuálního počítače (VM) s použitím [az vm vytvořit](/cli/azure/vm#az-vm-create) příkazu. Následující příklad vytvoří virtuální počítač s názvem můjvp přesměrovat. Tento příklad používá Demouser pro uživatelské jméno správce a Demouser@123 jako heslo uživatele. Tyto hodnoty změňte na něco, co je pro vaše prostředí vhodný.

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

Když se vytvoří virtuální počítač, **PublicIPAddress** parametr ve výstupu obsahuje veřejnou IP adresu pro virtuální počítač. Tato adresa zapište, protože mu umožní přístup k virtuálnímu počítači potřebujete.

## <a name="open-port-80-for-web-traffic"></a>Otevření portu 80 pro webový provoz

Protože tento virtuální počítač bude spouštět na webovém serveru IIS, budete muset otevřít port 80 pro přenosy z Internetu.

Použití [az vm open-port](/cli/azure/vm) příkaz pro otevření portu 80.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>Připojení k virtuálnímu počítači

Pomocí následujícího příkazu k vytvoření připojení vzdálené plochy k virtuálnímu počítači. Nahraďte "Veřejné IP adresy" s IP adresou vašeho virtuálního počítače. Po zobrazení výzvy zadejte uživatelské jméno a heslo, které jste použili pro virtuální počítač.

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>Instalace služby IIS pomocí PowerShellu

Teď, když jste přihlášení k virtuálnímu počítači, můžete použít PowerShell k instalaci služby IIS. Spusťte prostředí PowerShell na virtuálním počítači a spusťte následující příkaz:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Zobrazení úvodní stránky služby IIS

Chcete-li zobrazit výchozí úvodní stránka IIS můžete použít webový prohlížeč podle vašeho výběru. Pro návštěvu výchozí stránky použijte veřejnou IP adresu, které jsou popsané v předchozí části.

![Výchozí web služby IIS](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Vyčistěte prostředky, které už nepotřebujete. Použití [odstranění skupiny az](/cli/azure/group#az-group-delete) příkazu k odebrání skupiny prostředků, virtuálního počítače a všech souvisejících prostředků.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste nasadili základní virtuální počítač Windows Server. Další informace o virtuálních počítačích Azure Stack, [důležité informace týkající se virtuálních počítačů ve službě Azure Stack](azure-stack-vm-considerations.md).
