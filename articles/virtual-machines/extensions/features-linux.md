---
title: Azure rozšíření virtuálního počítače a funkce pro Linux | Microsoft Docs
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
ms.date: 03/30/2018
ms.author: danis
ms.openlocfilehash: 760f832bc12bccbf1cce77db25bf60413ad9a36b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "33942827"
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Rozšíření virtuálního počítače a funkce pro Linux

Rozšíření virtuálních počítačů (VM) Azure jsou malých aplikacích, které poskytují konfiguraci a automatizaci úloh po nasazení na virtuálních počítačích Azure. Například, pokud virtuální počítač vyžaduje instalace softwaru, antivirové ochrany, nebo spusťte skript uvnitř této můžete použít rozšíření virtuálního počítače. Rozšíření virtuálního počítače Azure můžete spustit pomocí rozhraní příkazového řádku Azure, prostředí PowerShell, šablony Azure Resource Manager a portálu Azure. Rozšíření můžete dodávat s nové nasazení virtuálního počítače nebo spouštění všechny existující systém.

Tento článek obsahuje přehled rozšíření virtuálních počítačů, požadavků na používání rozšíření virtuálního počítače Azure, a pokyny o tom, jak zjistit, spravovat a odeberte rozšíření virtuálního počítače. Tento článek obsahuje zobecněný informace, protože mnoho rozšíření virtuálního počítače nejsou k dispozici, každý s konfigurací potenciálně jedinečný. Podrobnosti o konkrétní rozšíření najdete v každý dokument specifické pro jednotlivé rozšíření.

## <a name="use-cases-and-samples"></a>Případy použití a ukázky

Jsou k dispozici několik různých rozšíření virtuálního počítače Azure, každý s konkrétní případ použití. Možné příklady:

- Použít PowerShell požadovaná stavu konfigurace pro virtuální počítač s příponou DSC pro Linux. Další informace najdete v tématu [stavu Azure požadovaná konfigurace rozšíření](https://github.com/Azure/azure-linux-extensions/tree/master/DSC).
- Nakonfigurujte monitorování virtuálního počítače pomocí rozšíření Microsoft Monitoring Agent virtuálního počítače. Další informace najdete v tématu [postupy k monitorování virtuálního počítače s Linuxem](../linux/tutorial-monitoring.md).
- Konfigurace sledování infrastruktury Azure s příponou Chef nebo Datadog. Další informace najdete v tématu [Chef dokumentace](https://docs.chef.io/azure_portal.html) nebo [Datadog blog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).

Kromě rozšíření specifické pro proces rozšíření vlastních skriptů je k dispozici pro virtuální počítače Windows a Linux. Rozšíření vlastních skriptů pro Linux umožňuje všech skriptů Bash ke spuštění na virtuálním počítači. Vlastní skripty jsou užitečné pro návrh Azure nasazení, které vyžadují konfiguraci nad rámec jaké nativní Azure nástrojů může poskytnout. Další informace najdete v tématu [rozšíření skriptů vlastní virtuálních počítačů Linux](custom-script-linux.md).

## <a name="prerequisites"></a>Požadavky

Pro zpracování rozšíření ve virtuálním počítači, potřebujete Azure Linux Agent nainstalovaný. Některé jednotlivých rozšíření mít požadavky, jako je přístup k prostředkům nebo závislosti.

### <a name="azure-vm-agent"></a>Agent virtuálního počítače Azure

Agent virtuálního počítače Azure spravuje interakce mezi virtuální počítač Azure a kontroleru prostředků infrastruktury Azure. Agent virtuálního počítače je zodpovědná za funkční aspekty nasazení a správa virtuálních počítačích Azure, včetně spuštění rozšíření virtuálního počítače. Agent virtuálního počítače Azure je předinstalován v Azure Marketplace bitové kopie a může být nainstalován ručně na podporovaných operačních systémů. Agent virtuálního počítače Azure pro Linux se označuje jako agenta systému Linux.

Informace o podporovaných operačních systémů a pokyny k instalaci najdete v tématu [agent virtuálního počítače Azure](agent-linux.md).

#### <a name="supported-agent-versions"></a>Verze podporovaného agenta

Chcete-li poskytovat nejlepší možný výkon, jsou minimální verze agenta. Další informace najdete v [tomto článku](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Podporované operační systémy

Agenta systému Linux se spouští na několika operační systémy, ale rozhraní rozšíření má omezení pro operační systémy tohoto rozšíření. Další informace najdete v tématu [v tomto článku] (https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems ).

Některá rozšíření nejsou podporovány v rámci všechny operační systémy a může vysílat *51 kódu chyby, nepodporovaný operační systém*. Zkontrolujte dokumentaci jednotlivých rozšíření možnosti podpory.

#### <a name="network-access"></a>Síťový přístup

Rozšiřující balíčky budou staženy z rozšíření úložiště Azure Storage a nahrávání stav rozšíření jsou odeslány do služby Azure Storage. Pokud používáte [podporované](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) verze agentů, není nutné pro povolení přístupu k Azure Storage v oblasti virtuálních počítačů, jak můžete použít agenta pro přesměrování komunikace na kontroler prostředků infrastruktury Azure pro komunikaci agenta. Pokud jste v nepodporované verzi agenta, budete muset povolit odchozí přístup k úložišti Azure v této oblasti z virtuálního počítače.

> [!IMPORTANT]
> Pokud mají blokovaný přístup k *168.63.129.1* použití brány firewall hosta, potom rozšíření nezdaří bez ohledu na výše.

Agenti slouží pouze ke stažení rozšíření balíčky a vytváření sestav stavu. Například pokud instalaci rozšíření je nutné stáhnout skript z webu GitHub (vlastní skript) nebo potřebují přístup ke službě Azure Storage (Azure Backup), pak další brány firewall nebo síťových zabezpečení muset otevřít porty skupiny. Různá rozšíření mají různé požadavky, protože jsou aplikace samy. Pro rozšíření, které vyžadují přístup k úložišti Azure, můžete povolit přístup pomocí značek služby Azure skupina NSG pro [úložiště](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview#service-tags).

Přesměrovat provoz požadavky agenta, má agenta systému Linux podpora proxy serveru. Tato podpora proxy serveru, ale nevztahuje rozšíření. Je nutné nakonfigurovat každé jednotlivé rozšíření pro práci s proxy server.

## <a name="discover-vm-extensions"></a>Zjistit rozšíření virtuálního počítače

Mnoho různých rozšíření virtuálního počítače jsou k dispozici pro použití s virtuálními počítači Azure. Pokud chcete zobrazit úplný seznam, použijte [seznamu obrázků rozšíření virtuálního počítače az](/cli/azure/vm/extension/image#az-vm-extension-image-list). Následující příklad uvádí všechny dostupné rozšíření v *westus* umístění:

```azurecli
az vm extension image list --location westus --output table
```

## <a name="run-vm-extensions"></a>Spuštění rozšíření virtuálního počítače

Rozšíření virtuálního počítače Azure spustit na existující virtuální počítače, což je užitečné, když potřebujete udělat změny konfigurace nebo obnovit připojení již nasazené virtuálního počítače. Rozšíření virtuálního počítače můžete také dodávat s nasazení šablony Azure Resource Manager. Virtuální počítače Azure můžete pomocí rozšíření šablony Resource Manageru, nasazení a nakonfigurování bez zásahu po nasazení.

Tyto metody slouží ke spuštění rozšíření stávajícího virtuálního počítače.

### <a name="azure-cli-20"></a>Azure CLI 2.0

Rozšíření virtuálního počítače Azure můžete spustit na existující virtuální počítač s [nastavení rozšíření virtuálního az](/cli/azure/vm/extension#az-vm-extension-set) příkaz. Následující příklad spustí rozšíření vlastních skriptů na virtuálním počítači s názvem *Můjvp* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/me/project/hello.sh"],"commandToExecute": "./hello.sh"}'
```

Když rozšíření běží správně, výstup se podobá v následujícím příkladu:

```bash
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Azure Portal

Rozšíření virtuálního počítače je použít pro existující virtuální počítač prostřednictvím portálu Azure. Vyberte virtuální počítač na portálu, zvolte **rozšíření**, pak vyberte **přidat**. Zvolte rozšíření, ze seznamu dostupných rozšíření a postupujte podle pokynů v průvodci.

Následující obrázek znázorňuje instalaci rozšíření Linux vlastních skriptů na portálu Azure:

![Instalace rozšíření vlastních skriptů](./media/features-linux/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru

Rozšíření virtuálních počítačů můžete přidat do šablony Azure Resource Manager a provést při nasazení šablony. Když nasadíte rozšíření pomocí šablony, můžete vytvořit plně nakonfigurované Azure nasazení. Například následující kód JSON je převzat ze šablony Resource Manageru, která nasadí sadu virtuálních počítačů s vyrovnáváním zatížení a Azure SQL database, a poté nainstaluje aplikace .NET Core na každý virtuální počítač. Rozšíření virtuálního počítače má na starosti instalace softwaru.

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

Další informace o vytváření šablon Resource Manageru najdete v tématu [šablon pro tvorbu Azure Resource Manageru](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Zabezpečení dat rozšíření virtuálního počítače

Když spustíte rozšíření virtuálního počítače, může být nutné zahrnovat citlivé informace, jako je například přihlašovací údaje, názvy účtů úložiště a přístupových klíčů k účtu úložiště. Mnoho rozšíření virtuálního počítače zahrnují chráněné konfigurace, která data šifruje a dešifruje ji pouze v cílovém virtuálním počítači. Každé rozšíření má schéma konkrétní chráněné konfigurace a každý je podrobně popsaná v dokumentaci konkrétní rozšíření.

Následující příklad ukazuje instanci rozšíření vlastních skriptů pro Linux. Příkaz k provedení zahrnuje sadu přihlašovacích údajů. V tomto příkladu není šifrován spuštění příkazu:

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

Přesun **příkaz k provedení** vlastnost, která má **chráněné** konfigurace zabezpečuje provádění řetězec, jak je znázorněno v následujícím příkladu:

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

### <a name="how-do-agents-and-extensions-get-updated"></a>Jak aktualizovat agenty a rozšíření?

Agenti a rozšíření sdílet stejný mechanismus aktualizace. Některé aktualizace se nevyžadují žádné další brány firewall pravidla.

Aktualizace je dostupná, pouze instalován na virtuálním počítači když dojde ke změně rozšíření a jiné změny modelu virtuálních počítačů, jako:

- Datové disky
- Rozšíření
- Spouštění diagnostiky kontejneru
- Tajné klíče hostovaného operačního systému
- Velikost virtuálního počítače
- Profil sítě

Vydavatelé zpřístupnění aktualizací pro oblasti v různou dobu, takže je možné, že může mít virtuální počítače v různých oblastech v různých verzích.

#### <a name="agent-updates"></a>Aktualizace agenta

Obsahuje agenta virtuálního počítače Linux *zřizování kód agenta* a *zpracování rozšíření kódu* v jeden balíček, který nelze rozdělit. Můžete zakázat *zřizování agenta* Pokud chcete zřídit v Azure pomocí init cloudu. Chcete-li to provést, přečtěte si téma [pomocí cloudu init](../linux/using-cloud-init.md).

Podporované verze agentů můžete použít automatické aktualizace. Pouze kód, který může být aktualizován, se *zpracování rozšíření kódu*, není kód zřizování. *Zřizování agenta kódu* je jedno spuštění kódu.

*Zpracování rozšíření kódu* zodpovídá za komunikaci s prostředky infrastruktury Azure a zpracování operace rozšíření virtuálního počítače, jako nainstaluje, zprávy o stavu, aktualizaci jednotlivých rozšíření a jejich odebrání. Aktualizace obsahují opravy zabezpečení, oprav chyb a vylepšení *zpracování rozšíření kódu*.

Když je agent nainstalován, vytvoří se démon nadřazené. Tento nadřazený poté vytvoří podřízený proces, který slouží ke zpracování rozšíření. Pokud aktualizace je dostupná pro agenta, je stažen, nadřazený zastaví podřízeného procesu, upgraduje se a potom ho restartuje. Mělo by být problém s aktualizací, proces nadřazené vrátí zpět na předchozí verzi podřízené.

Nadřazený proces nelze aktualizovat automaticky. Nadřazený lze aktualizovat pouze pomocí distro aktualizace balíčku.

Chcete-li zkontrolovat, jaká verze, kterou používáte, zkontrolujte `waagent` následujícím způsobem:

```bash
waagent --version
```

Výstup se podobá následujícímu příkladu:

```bash
WALinuxAgent-2.2.17 running on ubuntu 16.04
Python: 3.5.2
Goal state agent: 2.2.18
```

V předchozí příklad výstupu, k nadřazené nebo 'balíček nasadit verze' je *WALinuxAgent 2.2.17*

Agent stavu cílem je verze aktualizace automaticky.

Důrazně doporučujeme, abyste měli vždy automatickou aktualizaci pro agenta, [AutoUpdate.Enabled=y](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/update-agent). Není s to znamená povoleno, je třeba zachovat ručně aktualizovat agenta a ne získali opravy chyb a zabezpečení.

#### <a name="extension-updates"></a>Rozšíření aktualizací

Když je dostupná aktualizace rozšíření, bude Linux Agent stáhne a upgraduje rozšíření. Aktualizace automatické rozšíření jsou buď *menší* nebo *opravu Hotfix*. Můžete vyjádřit výslovný souhlas nebo vyjádření výslovného nesouhlasu rozšíření *menší* aktualizace při zřizování rozšíření. Následující příklad ukazuje, jak lze automaticky upgradovat podverze v šabloně Resource Manager s *autoUpgradeMinorVersion ": true, se*:

```json
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
```

Chcete-li získat nejnovější opravy chyb vedlejší verzi, důrazně doporučujeme vždy vyberte automatickou aktualizaci v nasazeních rozšíření. Aktualizace oprav hotfix, které provedení oprav chyb zabezpečení nebo klíč nemůže být zakázána.

### <a name="how-to-identify-extension-updates"></a>Zjištění aktualizací rozšíření

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identifikace, pokud je rozšíření s verzí autoUpgradeMinorVersion nastavená na virtuálním počítači

Uvidíte z modelu virtuálních počítačů, pokud rozšíření byla zřízena 'autoUpgradeMinorVersion'. Chcete-li zkontrolovat, použijte [az virtuálních počítačů zobrazit](/cli/azure/vm#az-vm-show) a zadejte skupinu prostředků a virtuálních počítačů název následujícím způsobem:

```azurecli
az vm show --resource-group myResourceGroup --name myVM
```

Zobrazuje následující příklad výstupu, který *autoUpgradeMinorVersion* je nastaven na *true*:

```json
  "resources": [
    {
      "autoUpgradeMinorVersion": true,
      "forceUpdateTag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/CustomScriptExtension",
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identifikace, když došlo autoUpgradeMinorVersion

Najdete při aktualizaci rozšíření došlo k chybě, projděte si agenta protokolů na virtuální počítač na */var/log/waagent.log*.

V následujícím příkladu je virtuální počítač měl *Microsoft.OSTCExtensions.LinuxDiagnostic 2.3.9025* nainstalována. Opravu hotfix je k dispozici pro *Microsoft.OSTCExtensions.LinuxDiagnostic 2.3.9027*:

```bash
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Expected handler state: enabled
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Decide which version to use
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Use version: 2.3.9027
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Current handler state is: NotInstalled
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Download extension package
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Unpack extension package
INFO Event: name=Microsoft.OSTCExtensions.LinuxDiagnostic, op=Download, message=Download succeeded
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Initialize extension directory
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Update settings file: 0.settings
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025] Disable extension.
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025] Launch command:diagnostic.py -disable
...
INFO Event: name=Microsoft.OSTCExtensions.LinuxDiagnostic, op=Disable, message=Launch command succeeded: diagnostic.py -disable
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Update extension.
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Launch command:diagnostic.py -update
2017/08/14 20:21:57 LinuxAzureDiagnostic started to handle.
```

## <a name="agent-permissions"></a>Oprávnění agenta

K provádění úloh, je potřeba agenta spustit jako *kořenové*.

## <a name="troubleshoot-vm-extensions"></a>Řešení potíží s rozšířeními virtuálního počítače

Každé rozšíření virtuálního počítače může mít při řešení potíží konkrétní rozšíření. Například pokud použijete rozšíření vlastních skriptů, podrobnosti provádění skriptu najdete místně na virtuálním počítači, kde byla rozšíření spustit. Kroky řešení potíží konkrétní rozšíření jsou podrobně popsané v dokumentaci k konkrétní rozšíření.

Následující kroky řešení potíží použít na všechny přípony virtuálních počítačů.

1. Zkontrolujte protokol agenta systému Linux, podívejte se na aktivity při rozšíření byla se zřídí v */var/log/waagent.log*

2. Další podrobnosti v protokolech o skutečné rozšíření   */var/protokolu/azure /<extensionName>*

3. Podívejte se do dokumentace konkrétní rozšíření řešení potíží s oddíly pro kódy chyb, známé problémy atd.

3. Podívejte se na protokol systému. Zkontrolujte, zda jiné operace, které může mít brání rozšíření, jako je třeba dlouhotrvající instalace jiné aplikace, která vyžaduje výhradní balíček manager přístup.

### <a name="common-reasons-for-extension-failures"></a>Z možných příčin selhání rozšíření

1. Rozšíření mít 20 minut ke spuštění (výjimky jsou rozšíření CustomScript, Chef a DSC, které mají 90 minut). Pokud nasazení překročí tuto dobu, je označena jako vypršení časového limitu. Příčinou může být z důvodu nedostatku prostředků virtuálních počítačů, dalších virtuálních počítačů konfigurace nebo po spuštění úlohy využívání vysoké objemy prostředků a zároveň rozšíření se pokouší o zřízení.

2. Nebyly splněny minimální předpoklady. Některá rozšíření jsou závislé na SKU virtuálních počítačů, například bitové kopie prostředí HPC. Rozšíření může vyžadovat určité síťové požadavky přístup, jako je například komunikaci s Azure Storage nebo veřejné služby. Další příklady může být přístup k balíčku úložišť dostatek místa na disku nebo omezení zabezpečení.

3. Balíček výhradní přístup správce. V některých případech se můžete setkat s dlouhotrvající konfigurace virtuálního počítače a instalace rozšíření konfliktní, kde se oba musí výhradní přístup k Správce balíčků.

### <a name="view-extension-status"></a>Zobrazit stav rozšíření

Po spuštění rozšíření virtuálního počítače pro virtuální počítač, použijte [az virtuálních počítačů get--zobrazení instance](/cli/azure/vm#az-vm-get-instance-view) vrátit stav rozšíření následujícím způsobem:

```azurecli
az vm get-instance-view \
    --resource-group rgName \
    --name myVM \
    --query "instanceView.extensions"
```

Výstup je podobné výstupu v následujícím příkladu:

```bash
  {
    "name": "customScript",
    "statuses": [
      {
        "code": "ProvisioningState/failed/0",
        "displayStatus": "Provisioning failed",
        "level": "Error",
        "message": "Enable failed: failed to execute command: command terminated with exit status=127\n[stdout]\n\n[stderr]\n/bin/sh: 1: ech: not found\n",
        "time": null
      }
    ],
    "substatuses": null,
    "type": "Microsoft.Azure.Extensions.customScript",
    "typeHandlerVersion": "2.0.6"
  }
```

Stav spuštění rozšíření možné také najít na portálu Azure. Chcete-li zobrazit stav rozšíření, vyberte virtuální počítač, zvolte **rozšíření**, pak vyberte požadované rozšíření.

### <a name="rerun-a-vm-extension"></a>Znovu spustit, rozšíření virtuálního počítače

Můžou nastat případy, ve kterých musí být znovu rozšíření virtuálního počítače. Rozšíření může znovu odebrat, a pak znovu spustit rozšíření s metodu provádění podle svého výběru. Chcete-li odebrat rozšíření, použijte [odstranit rozšíření virtuálního počítače az](/cli/azure/vm/extension#az-vm-extension-delete) následujícím způsobem:

```azurecli
az vm extension delete \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name customScript
```

Můžete také odebrat rozšíření na portálu Azure následujícím způsobem:

1. Vyberte virtuální počítač.
2. Zvolte **rozšíření**.
3. Vyberte požadované rozšíření.
4. Zvolte **odinstalovat**.

## <a name="common-vm-extension-reference"></a>Běžné odkaz na rozšíření virtuálního počítače

| Název rozšíření | Popis | Další informace |
| --- | --- | --- |
| Rozšíření vlastních skriptů pro Linux |Spouštění skriptů na virtuálním počítači Azure |[Rozšíření vlastních skriptů pro Linux](custom-script-linux.md) |
| Rozšíření přístupu virtuálních počítačů |Znovu získat přístup do virtuálního počítače Azure |[Rozšíření přístupu virtuálních počítačů](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Rozšíření Azure Diagnostics |Správa Azure Diagnostics |[Rozšíření Azure Diagnostics](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Rozšíření přístup virtuálních počítačů Azure |Spravovat uživatele a přihlašovací údaje |[Rozšíření pro přístup virtuálních počítačů pro Linux](https://azure.microsoft.com/en-us/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Další postup

Další informace o rozšíření virtuálního počítače najdete v tématu [virtuální počítač Azure rozšíření a funkce přehled](overview.md).
