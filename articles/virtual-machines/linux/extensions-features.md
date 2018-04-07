---
title: Rozšíření virtuálního počítače a funkce pro Linux | Microsoft Docs
description: Zjistěte, jaká rozšíření jsou k dispozici pro virtuální počítače Azure, seskupené podle co se poskytují nebo zvýšit.
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 52f5d0ec-8f75-49e7-9e15-88d46b420e63
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: danis
ms.openlocfilehash: 2a53e34ed6f1eef3e54585587ddadd544b1ae728
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Rozšíření virtuálního počítače a funkce pro Linux

Rozšíření virtuálního počítače Azure se malých aplikacích, které poskytují konfiguraci a automatizaci úloh po nasazení na virtuálních počítačích Azure. Například pokud virtuální počítač vyžaduje instalace softwaru, ochrana proti virům nebo Docker konfigurace, rozšíření virtuálního počítače můžete použít k dokončení těchto úloh. Rozšíření virtuálního počítače Azure můžete spustit pomocí rozhraní příkazového řádku Azure, prostředí PowerShell, šablony Azure Resource Manager a portálu Azure. Rozšíření můžete dodávat s nové nasazení virtuálního počítače nebo spouštění všechny existující systém.

Tento dokument obsahuje přehled rozšíření virtuálních počítačů, požadavků na používání rozšíření virtuálního počítače Azure, a pokyny o tom, jak zjistit, spravovat a odeberte rozšíření virtuálního počítače. Tento dokument obsahuje zobecněný informace, protože mnoho rozšíření virtuálního počítače nejsou k dispozici, každý s konfigurací potenciálně jedinečný. Podrobnosti o konkrétní rozšíření najdete v každý dokument specifické pro jednotlivé rozšíření.

## <a name="use-cases-and-samples"></a>Případy použití a ukázky

Jsou k dispozici několik různých rozšíření virtuálního počítače Azure, každý s konkrétní případ použití. Tady je několik příkladů:

- Použít PowerShell požadovaná stav konfigurace virtuálního počítače pomocí rozšíření DSC pro Linux. Další informace najdete v tématu [stavu Azure požadovaná konfigurace rozšíření](https://github.com/Azure/azure-linux-extensions/tree/master/DSC).
- Konfigurace monitorování virtuálního počítače pomocí rozšíření Microsoft Monitoring Agent virtuálního počítače. Další informace najdete v tématu [postupy k monitorování virtuálního počítače s Linuxem](tutorial-monitoring.md).
- Konfigurace sledování infrastruktury Azure s příponou Datadog. Další informace najdete v tématu [Datadog blog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).
- Konfigurace hostitele Docker na virtuální počítač Azure pomocí rozšíření Docker virtuálního počítače. Další informace najdete v tématu [rozšíření virtuálního počítače Docker](dockerextension.md).

Kromě rozšíření specifické pro proces rozšíření vlastních skriptů je k dispozici pro virtuální počítače Windows a Linux. Rozšíření vlastních skriptů pro Linux umožňuje všech skriptů Bash ke spuštění na virtuálním počítači. Vlastní skripty jsou užitečné pro návrh Azure nasazení, které vyžadují konfiguraci nad rámec jaké nativní Azure nástrojů může poskytnout. Další informace najdete v tématu [rozšíření skriptů vlastní virtuálních počítačů Linux](extensions-customscript.md).


## <a name="prerequisites"></a>Požadavky

Každé rozšíření virtuálního počítače může mít vlastní sadu požadavků. Například rozšíření virtuálního počítače Docker má předpokladem podporované distribuce systému Linux. Požadavky jednotlivých rozšíření jsou podrobně popsané v dokumentaci k konkrétní rozšíření.

### <a name="azure-vm-agent"></a>Agent virtuálního počítače Azure

Agent virtuálního počítače Azure spravuje interakce mezi virtuální počítač Azure a kontroleru prostředků infrastruktury Azure. Agent virtuálního počítače je zodpovědná za funkční aspekty nasazení a správa virtuálních počítačích Azure, včetně spuštění rozšíření virtuálního počítače. Agent virtuálního počítače Azure je předinstalován v Azure Marketplace bitové kopie a může být nainstalován ručně na podporovaných operačních systémů.

Informace o podporovaných operačních systémů a pokyny k instalaci najdete v tématu [agent virtuálního počítače Azure](agent-user-guide.md).

## <a name="discover-vm-extensions"></a>Zjistit rozšíření virtuálního počítače

Mnoho různých rozšíření virtuálního počítače jsou k dispozici pro použití s virtuálními počítači Azure. Pokud chcete zobrazit úplný seznam, spusťte následující příkaz pomocí Azure CLI, nahraďte umístění Příklad umístění podle vaší volby.

```azurecli
az vm extension image list --location westus -o table
```

## <a name="run-vm-extensions"></a>Spuštění rozšíření virtuálního počítače

Rozšíření virtuálního počítače Azure můžete spustit na existující virtuální počítače, které jsou užitečné, když potřebujete udělat změny konfigurace nebo obnovit připojení již nasazené virtuálního počítače. Rozšíření virtuálního počítače můžete také dodávat s nasazení šablony Azure Resource Manager. Virtuální počítače Azure můžete pomocí rozšíření šablony Resource Manageru, nasazení a nakonfigurování bez zásahu po nasazení.

Tyto metody slouží ke spuštění rozšíření stávajícího virtuálního počítače.

### <a name="azure-cli"></a>Azure CLI

Pro existující virtuální počítač můžete spouštět rozšíření virtuálního počítače Azure pomocí `az vm extension set` příkaz. Tento příklad spouští rozšíření vlastních skriptů virtuálním počítači.

```azurecli
az vm extension set `
  --resource-group exttest `
  --vm-name exttest `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],"commandToExecute": "./hello.sh"}'
```

Tento skript vytvoří výstup podobný následujícímu:

```azurecli
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Azure Portal

Rozšíření virtuálního počítače je použít pro existující virtuální počítač prostřednictvím portálu Azure. To pokud chcete udělat, vyberte virtuální počítač, vyberte **rozšíření**a klikněte na tlačítko **přidat**. Vyberte požadované rozšíření ze seznamu dostupných rozšíření a postupujte podle pokynů v průvodci.

Následující obrázek znázorňuje instalaci rozšíření Linux vlastních skriptů na portálu Azure.

![Instalace rozšíření vlastních skriptů](./media/extensions-features/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru

Rozšíření virtuálních počítačů můžete přidat do šablony Azure Resource Manager a provést při nasazení šablony. Když nasadíte rozšíření pomocí šablony, můžete vytvořit plně nakonfigurované Azure nasazení. Například následující kód JSON je převzat ze šablony Resource Manageru. Šablona nasadí sadu Vyrovnávání zatížení sítě virtuálních počítačů a Azure SQL database a potom nainstaluje aplikace .NET Core na každý virtuální počítač. Rozšíření virtuálního počítače má na starosti instalace softwaru.

Další informace najdete v tématu kompletní [šablony Resource Manageru](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
    }
}
```

Další informace najdete v tématu [šablon pro tvorbu Azure Resource Manageru](../windows/template-description.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#extensions).

## <a name="secure-vm-extension-data"></a>Zabezpečení dat rozšíření virtuálního počítače

Když používáte rozšíření virtuálního počítače, může být nutné zahrnovat citlivé informace, jako je například přihlašovací údaje, názvy účtů úložiště a přístupových klíčů k účtu úložiště. Mnoho rozšíření virtuálního počítače zahrnují chráněné konfigurace, která data šifruje a dešifruje ji pouze uvnitř cílového virtuálního počítače. Každé rozšíření má schéma konkrétní chráněné konfigurace a každý je podrobně popsaná v dokumentaci konkrétní rozšíření.

Následující příklad ukazuje instanci rozšíření vlastních skriptů pro Linux. Všimněte si, že příkaz k provedení obsahuje sadu přihlašovacích údajů. V tomto příkladu spuštění příkazu se šifrovat nebude.


```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ],
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

Přesun **příkaz k provedení** vlastnost, která má **chráněné** konfigurace zabezpečuje provádění řetězec.

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

## <a name="troubleshoot-vm-extensions"></a>Řešení potíží s rozšířeními virtuálního počítače

Každé rozšíření virtuálního počítače může mít při řešení potíží konkrétní rozšíření. Například pokud používáte rozšíření vlastních skriptů, podrobnosti provádění skriptu najdete místně na virtuálním počítači, na kterém byl rozšíření spustit. Kroky řešení potíží konkrétní rozšíření jsou podrobně popsané v dokumentaci k konkrétní rozšíření.

Následující kroky řešení potíží použít na všechny přípony virtuálního počítače.

### <a name="view-extension-status"></a>Zobrazit stav rozšíření

Po virtuálního počítače rozšíření spustit na virtuálním počítači, pomocí následujícího příkazu příkazového řádku Azure CLI vrátí stav rozšíření. Názvy parametrů příkladu nahraďte vlastními hodnotami.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Výstup vypadá následující text:

```azurecli
AutoUpgradeMinorVersion    Location    Name          ProvisioningState    Publisher                   ResourceGroup      TypeHandlerVersion  VirtualMachineExtensionType
-------------------------  ----------  ------------  -------------------  --------------------------  ---------------  --------------------  -----------------------------
True                       westus      customScript  Succeeded            Microsoft.Azure.Extensions  exttest                             2  customScript
```

Stav spuštění rozšíření možné také najít na portálu Azure. Chcete-li zobrazit stav rozšíření, vyberte virtuální počítač, zvolte **rozšíření**a vyberte požadované rozšíření.

### <a name="rerun-a-vm-extension"></a>Znovu spustit, rozšíření virtuálního počítače

Můžou nastat případy, ve kterých musí být znovu rozšíření virtuálního počítače. Rozšíření může znovu odebrat, a pak znovu spustit rozšíření s metodu provádění podle svého výběru. Chcete-li odebrat rozšíření, spusťte následující příkaz pomocí Azure CLI. Názvy parametrů příkladu nahraďte vlastními hodnotami.

```azurecli
az vm extension delete --name customScript --resource-group myResourceGroup --vm-name myVM
```

Rozšíření můžete odebrat pomocí následujících kroků na portálu Azure:

1. Vyberte virtuální počítač.
2. Zvolte **rozšíření**.
3. Vyberte požadované rozšíření.
4. Zvolte **odinstalovat**.

## <a name="common-vm-extension-reference"></a>Běžné odkaz na rozšíření virtuálního počítače
| Název rozšíření | Popis | Další informace |
| --- | --- | --- |
| Rozšíření vlastních skriptů pro Linux |Spouštění skriptů na virtuálním počítači Azure |[Rozšíření vlastních skriptů pro Linux](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Rozšíření docker |Instalace démona Docker pro podporu vzdálených příkazů Docker. |[Rozšíření Docker VM](dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Rozšíření přístupu virtuálních počítačů |Znovu získat přístup do virtuálního počítače Azure |[Rozšíření přístupu virtuálních počítačů](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Rozšíření Azure Diagnostics |Správa Azure Diagnostics |[Rozšíření Azure Diagnostics](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Rozšíření přístup virtuálních počítačů Azure |Spravovat uživatele a přihlašovací údaje |[Rozšíření pro přístup virtuálních počítačů pro Linux](https://azure.microsoft.com/en-us/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |
