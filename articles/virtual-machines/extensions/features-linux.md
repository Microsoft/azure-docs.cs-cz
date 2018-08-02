---
title: Azure VM funkcí a rozšíření pro Linux | Dokumentace Microsoftu
description: Zjistěte, jaká rozšíření jsou dostupná pro Azure virtual machines, seskupené podle co, zadejte nebo zvýšit.
services: virtual-machines-linux
documentationcenter: ''
author: zroiy
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
ms.author: roiyz
ms.openlocfilehash: e172b9d6a59f14f741a09450d31602b0d2d9ee5a
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412623"
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Rozšíření virtuálních počítačů a funkce pro Linux

Rozšíření virtuálních počítačů (VM) Azure jsou malých aplikací, které poskytují konfiguraci a automatizaci úloh po nasazení na virtuálních počítačích Azure. Například, pokud virtuální počítač vyžaduje instalace softwaru, antivirové ochrany, nebo spusťte skript uvnitř této můžete použít rozšíření virtuálního počítače. Rozšíření virtuálního počítače Azure je možné spustit pomocí rozhraní příkazového řádku Azure, Powershellu, šablon Azure Resource Manageru a webu Azure portal. Rozšíření můžete dodávat s nasazením nového virtuálního počítače nebo spouštět všechny stávající systém.

Tento článek obsahuje přehled rozšíření virtuálních počítačů, požadavků na používání rozšíření virtuálního počítače Azure, a pokyny o tom, jak zjišťovat, spravovat a odebrání rozšíření virtuálních počítačů. Tento článek obsahuje obecné informace, protože mnoho rozšíření virtuálního počítače jsou k dispozici, každý s potenciálně jedinečnou konfiguraci. Podrobnosti o konkrétní rozšíření najdete v jednotlivých dokumentech specifických pro jednotlivá rozšíření.

## <a name="use-cases-and-samples"></a>Případy použití a ukázky

Několik různých rozšíření virtuálního počítače Azure jsou k dispozici, každý s konkrétním případu použití. Možné příklady:

- Použijte PowerShell Desired State konfigurace virtuálního počítače pomocí rozšíření DSC pro Linux. Další informace najdete v tématu [rozšíření Azure Desired State configuration](https://github.com/Azure/azure-linux-extensions/tree/master/DSC).
- Konfigurace monitorování virtuálního počítače pomocí rozšíření Microsoft Monitoring Agent virtuálního počítače. Další informace najdete v tématu [monitorování virtuálního počítače s Linuxem](../linux/tutorial-monitoring.md).
- Konfigurace sledování infrastruktury Azure pomocí Chefu nebo služby Datadog rozšíření. Další informace najdete v tématu [Chef dokumentace](https://docs.chef.io/azure_portal.html) nebo [blog služby Datadog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).

Kromě rozšíření specifické pro proces rozšíření vlastních skriptů je k dispozici pro virtuální počítače s Windows a Linux. Rozšíření vlastních skriptů pro Linux umožňuje libovolného skriptu Bash ke spuštění na virtuálním počítači. Vlastní skripty jsou užitečné pro návrh nasazení Azure, které vyžadují konfiguraci nad rámec může poskytnout co nativních nástrojů Azure. Další informace najdete v tématu [rozšíření vlastních skriptů virtuálního počítače Linux](custom-script-linux.md).

## <a name="prerequisites"></a>Požadavky

Pro zpracování rozšíření na virtuálním počítači, musíte nainstalovat agenta Azure Linux. Některé konkrétní rozšíření jsou splněné požadavky, jako je například přístup k prostředkům nebo závislosti.

### <a name="azure-vm-agent"></a>Agent virtuálního počítače Azure

Agent virtuálního počítače Azure slouží ke správě interakcí mezi Virtuálním počítači Azure a kontroler prostředků infrastruktury Azure. Agent virtuálního počítače je zodpovědná za funkční aspekty nasazení a správa virtuálních počítačů Azure, včetně spouštění rozšíření virtuálních počítačů. Agent virtuálního počítače Azure je jako předinstalované na imagích Azure Marketplace a můžete nainstalovat ručně do podporovaných operačních systémů. Agent virtuálního počítače Azure pro Linux se označuje jako agenta pro Linux.

Informace o podporovaných operačních systémů a pokyny k instalaci najdete v tématu [agent virtuálního počítače Azure](agent-linux.md).

#### <a name="supported-agent-versions"></a>Podporovaný agent verze

K zajištění nejlepšího možného prostředí, jsou minimální verze agenta. Další informace najdete v [tomto článku](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Podporované operační systémy

Agenta pro Linux se spouští na více operačních systémů, ale rozhraní rozšíření má limit pro operačních systémech tohoto rozšíření. Další informace najdete v tématu [tohoto článku] (https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems ).

Některá rozšíření nejsou podporovány ve všech operačních systémů a může vysílat *51 kód chyby, nepodporovaný operační systém*. Možnosti podpory v dokumentaci jednotlivých rozšíření.

#### <a name="network-access"></a>Síťový přístup

Balíčky rozšíření se stáhnou z rozšíření úložiště Azure Storage a stav nahrávání rozšíření jsou odeslány do služby Azure Storage. Pokud používáte [podporované](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) verze agentů, není nutné pro povolení přístupu ke službě Azure Storage v oblasti virtuálních počítačů, můžete k používání agenta pro přesměrování komunikace na kontroler prostředků infrastruktury Azure pro komunikaci agenta. Pokud jste v nepodporované verzi agenta, budete muset povolit odchozí přístup k Azure storage v dané oblasti z virtuálního počítače.

> [!IMPORTANT]
> Pokud budete mít zablokovaný přístup k *168.63.129.1* pomocí brány firewall hosta, pak rozšíření nezdaří bez ohledu na to, výše uvedené.

Agenti jde použít jenom chcete stáhnout balíčky rozšíření a vykazování stavu. Například, pokud instalaci rozšíření je potřeba stáhnout skript z Githubu (Custom Script) nebo je nutné získat přístup ke službě Azure Storage (Azure Backup), pak další brány firewall nebo sítě zabezpečení musejí být otevřeny porty skupiny. Různá rozšíření mají různé požadavky, protože jsou aplikace samy. Pro rozšíření, které vyžadují přístup ke službě Azure Storage, můžete povolit přístup pomocí značky služeb Azure NSG pro [úložiště](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview#service-tags).

Pro přesměrování požadavků provoz agenta, má agenta pro Linux podpora proxy serveru. Tato podpora proxy serveru, ale nevztahuje rozšíření. Je nutné nakonfigurovat každé jednotlivé rozšíření pro práci s proxy server.

## <a name="discover-vm-extensions"></a>Zjistit rozšíření virtuálních počítačů

Mnoho různých rozšíření virtuálního počítače jsou k dispozici pro použití s virtuálními počítači Azure. Pokud chcete zobrazit úplný seznam, použijte [seznamu image virtuálního počítače rozšíření az](/cli/azure/vm/extension/image#az-vm-extension-image-list). Následující příklad zobrazí seznam všech dostupných rozšíření v *westus* umístění:

```azurecli
az vm extension image list --location westus --output table
```

## <a name="run-vm-extensions"></a>Spuštění rozšíření virtuálních počítačů

Rozšíření Azure VM spustit na stávajících virtuálních počítačů, což je užitečné, když budete chtít provést změny konfigurace nebo obnovit připojení na již nasazených virtuálních počítačů. Rozšíření virtuálních počítačů může být součástí nasazení šablon Azure Resource Manageru. Pomocí rozšíření pomocí šablon Resource Manageru, je možné nasadit a nakonfigurovat bez zásahu po nasazení virtuálních počítačů Azure.

Následující metody můžete použít ke spuštění rozšíření existujícího virtuálního počítače.

### <a name="azure-cli-20"></a>Azure CLI 2.0

Rozšíření virtuálního počítače Azure dají spustit pro existující virtuální počítač s [sada rozšíření az vm](/cli/azure/vm/extension#az-vm-extension-set) příkazu. Následující příklad spustí rozšíření vlastních skriptů na virtuálním počítači s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/me/project/hello.sh"],"commandToExecute": "./hello.sh"}'
```

Když se spustí rozšíření správně, výstup se podobá následujícímu příkladu:

```bash
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>portál Azure

Rozšíření virtuálních počítačů můžete použít pro existující virtuální počítač na webu Azure portal. Vyberte virtuální počítač na portálu, zvolte **rozšíření**a pak vyberte **přidat**. Vyberte rozšíření ze seznamu dostupných rozšíření a postupujte podle pokynů v průvodci.

Následující obrázek ukazuje instalaci rozšíření vlastních skriptů Linuxu na webu Azure Portal:

![Instalace rozšíření vlastních skriptů](./media/features-linux/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru

Rozšíření virtuálních počítačů lze přidat do šablony Azure Resource Manageru a spustit při nasazení šablony. Když nasadíte rozšíření pomocí šablony, můžete vytvořit plně nakonfigurovaného nasazení Azure. Například následující kód JSON je převzata z šablony Resource Manageru, které nasadí sadu virtuálních počítačů s vyrovnáváním zatížení a Azure SQL database, a poté nainstaluje aplikaci .NET Core na každém virtuálním počítači. Rozšíření virtuálního počítače se postará o instalaci softwaru.

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

Další informace o vytváření šablon Resource Manageru najdete v tématu [šablon pro vytváření Azure Resource Manageru](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Zabezpečení dat rozšíření virtuálního počítače

Při spuštění rozšíření virtuálního počítače může být nutné zahrnout citlivé informace, jako je například přihlašovací údaje, názvy účtů úložiště a přístupové klíče účtu úložiště. Mnoho rozšíření virtuálního počítače zahrnují chráněné konfigurace, která šifruje data a dešifruje ji pouze v cílovém virtuálním počítači. Každé rozšíření má specifickou konfiguraci chráněných schéma a každý je podrobně popsán v dokumentaci pro konkrétní rozšíření.

Následující příklad ukazuje instanci rozšíření vlastních skriptů pro Linux. Příkaz provedený zahrnuje sadu přihlašovacích údajů. V tomto příkladu není šifrovaná příkaz pro spuštění:

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

Přechod **příkaz k provedení** vlastnost **chráněné** zabezpečuje konfigurace spuštění řetězce, jak je znázorněno v následujícím příkladu:

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

### <a name="how-do-agents-and-extensions-get-updated"></a>Jak aktualizaci agentů a rozšíření?

Agenti a rozšíření sdílet stejný mechanismus aktualizace. Některé aktualizace se nevyžadují žádné další pravidla brány firewall.

Pokud je k dispozici aktualizace, je pouze nainstalovaná na virtuálním počítači při dojde ke změně rozšíření a jiné změny modelu virtuálního počítače, jako:

- Datové disky
- Rozšíření
- Spouštění diagnostiky kontejnerů
- Tajné kódy hostovaného operačního systému
- Velikost virtuálního počítače
- Profil sítě

Vydavatelé zpřístupnění aktualizací pro regiony v různých časech, takže je možné, že může mít virtuální počítače v různých oblastech na různé verze.

#### <a name="agent-updates"></a>Aktualizace agenta

Obsahuje agenta virtuálního počítače s Linuxem *zřizování agenta kód* a *zpracování rozšíření kódu* v rámci jednoho balíčku, který nelze rozdělit. Můžete zakázat *zřizování agenta* Pokud chcete zřídit v Azure pomocí cloud-init. Chcete-li to provést, najdete v článku [použití cloud-init](../linux/using-cloud-init.md).

Podporované verze agentů, můžete použít automatické aktualizace. Je pouze kód, který je možné aktualizovat *zpracování rozšíření kódu*, není kód zřizování. *Kód zřizování agenta* je jedno spuštění kódu.

*Zpracování rozšíření kódu* zodpovídá za komunikaci s prostředky infrastruktury Azure a zpracování operace rozšíření virtuálních počítačů, jako nainstaluje, hlásí stav jednotlivých rozšíření a aktualizace je odebrali. Aktualizace obsahují opravy zabezpečení, opravy chyb a vylepšení *zpracování rozšíření kódu*.

Při instalaci agenta je vytvořen nadřazený démona. Tento nadřazený poté vytvoří podřízený proces, který se používá ke zpracování rozšíření. Pokud je k dispozici je aktualizace pro agenta, se stáhne, nadřazené podřízený proces se zastaví, upgraduje je a potom ho restartuje. Mělo by být potíže s aktualizací, nadřazený proces vrátí zpět na předchozí verzi podřízené.

Nadřazený proces nemůže být automaticky aktualizován. Nadřazené lze aktualizovat pouze aktualizace balíčku distribuce.

Pokud chcete zkontrolovat verzi používáte, zkontrolujte, `waagent` následujícím způsobem:

```bash
waagent --version
```

Výstup se podobá následujícímu příkladu:

```bash
WALinuxAgent-2.2.17 running on ubuntu 16.04
Python: 3.5.2
Goal state agent: 2.2.18
```

V předchozím příkladu výstupu k nadřazené nebo "balíček nasazen verze" je *WALinuxAgent 2.2.17*

Cílem agenta stavu je automaticky aktualizovanou verzi.

Důrazně doporučujeme mít vždy Automatická aktualizace pro agenta, [AutoUpdate.Enabled=y](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/update-agent). Není to povoleno znamená, že je potřeba nechat ruční aktualizace agenta s a nedostanou opravy chyb a zabezpečení.

#### <a name="extension-updates"></a>Aktualizace rozšíření

Při aktualizaci rozšíření je k dispozici, soubory ke stažení agenta pro Linux a upgraduje rozšíření. Aktualizace automatické rozšíření jsou buď *menší* nebo *opravu Hotfix*. Můžete vyjádřit výslovný souhlas nebo vyjádřit výslovný nesouhlas rozšíření *menší* aktualizuje při zřizování rozšíření. Následující příklad ukazuje, jak automaticky aktualizovat dílčí verze v šabloně Resource Manageru s *autoUpgradeMinorVersion ": true,"*:

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

Pokud chcete získat nejnovější podverzi opravy, důrazně doporučujeme vždy vybrat automatické aktualizace v nasazení vašeho rozšíření. Aktualizace hotfix, které zajišťují opravy zabezpečení nebo klíč nemůže být zakázána.

### <a name="how-to-identify-extension-updates"></a>Zjištění aktualizace rozšíření

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identifikace, pokud je rozšíření nastavená možnost autoupgrademinorversion na virtuálním počítači

Je vidět z modelu virtuálního počítače, pokud rozšíření a byla opatřena "verzi autoUpgradeMinorVersion". Chcete-li zkontrolovat, použijte [az vm show](/cli/azure/vm#az-vm-show) a zadejte skupinu prostředků a virtuální počítač pojmenujte následujícím způsobem:

```azurecli
az vm show --resource-group myResourceGroup --name myVM
```

Následující příklad výstupu ukazuje, že *autoUpgradeMinorVersion* je nastavena na *true*:

```json
  "resources": [
    {
      "autoUpgradeMinorVersion": true,
      "forceUpdateTag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/CustomScriptExtension",
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Určení, kdy došlo k chybě verzí autoUpgradeMinorVersion

Chcete-li zobrazit, kdy došlo k aktualizaci rozšíření, zkontrolujte agenta protokoly na virtuálním počítači na */var/log/waagent.log*.

V následujícím příkladu virtuální počítač měl *Microsoft.OSTCExtensions.LinuxDiagnostic 2.3.9025* nainstalované. Byla k dispozici oprava hotfix *Microsoft.OSTCExtensions.LinuxDiagnostic 2.3.9027*:

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

Umožní provádět úkoly, je potřeba agenta spustit jako *kořenové*.

## <a name="troubleshoot-vm-extensions"></a>Řešení potíží s rozšířeními virtuálních počítačů

Každé rozšíření virtuálního počítače může mít řešení potíží s konkrétní kroky pro rozšíření. Například při použití rozšíření vlastních skriptů podrobnosti spuštění skriptu můžete najít místně na virtuálním počítači, kde byla spuštěna rozšíření. Všechny kroky pro řešení problémů s konkrétní rozšíření jsou podrobně popsány v rozšíření příslušnou část dokumentace.

Následující postup platí pro všechna rozšíření virtuálních počítačů.

1. Zkontrolujte protokol agenta systému Linux, podívejte se na aktivity při rozšíření se zřídí */var/log/waagent.log*

2. Pro další informace naleznete v protokolech skutečné rozšíření   */var/protokolu/azure /<extensionName>*

3. Zkontrolujte rozšíření příslušnou část dokumentace Poradce při potížích s oddíly pro kódy chyb, známé problémy atd.

3. Podívejte se na protokoly systému. Kontrola pro jiné operace, které mohou mít brání navigací příponu, třeba dlouho probíhající instalace jiné aplikace, která vyžaduje výhradní balíček správce přístup.

### <a name="common-reasons-for-extension-failures"></a>Běžné důvody selhání rozšíření

1. Máte 20 minut. Chcete-li spustit rozšíření (výjimky jsou rozšíření CustomScript, Chef a DSC, které mají 90 minut). Pokud nasazení překročí tuto dobu, je označena jako vypršení časového limitu. Příčin této chyby může být z důvodu nedostatku prostředků virtuálních počítačů a dalších virtuálních počítačů konfigurace/úloh při spuštění spotřebovává vysoké množství prostředků, zatímco rozšíření se pokouší o zřízení.

2. Nebyly splněny minimální předpoklady. Některá rozšíření jsou závislé na skladové položky virtuálních počítačů, jako jsou obrázky prostředí HPC. Rozšíření můžou vyžadovat některé síťové požadavky na přístup, jako je například komunikace a veřejné služby Azure Storage. Další příklady může být přístup k úložištím balíčku, nemá dostatek místa na disku nebo omezení zabezpečení.

3. Exkluzivní balíček přístup správce. V některých případech se můžete setkat dlouho probíhající konfigurace virtuálního počítače a instalace rozšíření konfliktní, kde obě potřebovat výhradní přístup k Správce balíčků.

### <a name="view-extension-status"></a>Zobrazit stav rozšíření

Po spuštění rozšíření virtuálního počítače na virtuálním počítači použít [az vm get-instance-view](/cli/azure/vm#az-vm-get-instance-view) vrátit stav rozšíření následujícím způsobem:

```azurecli
az vm get-instance-view \
    --resource-group rgName \
    --name myVM \
    --query "instanceView.extensions"
```

Výstup se podobá následující příklad výstupu:

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

Stav spuštění rozšíření najdete také na webu Azure Portal. Chcete-li zobrazit stav rozšíření, vyberte virtuální počítač, zvolte **rozšíření**, pak vyberte požadovaného rozšíření.

### <a name="rerun-a-vm-extension"></a>Znovu spusťte rozšíření virtuálního počítače

Můžou nastat případy, ve kterých je rozšíření virtuálního počítače potřeba znovu spustit. Rozšíření můžete znovu spustit ho odebrat, a potom znovu spustit rozšíření s metodou spuštění podle vašeho výběru. Chcete-li odebrat rozšíření, použijte [odstranění rozšíření az vm](/cli/azure/vm/extension#az-vm-extension-delete) následujícím způsobem:

```azurecli
az vm extension delete \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name customScript
```

Můžete také odebrat rozšíření na webu Azure Portal následujícím způsobem:

1. Vyberte virtuální počítač.
2. Zvolte **rozšíření**.
3. Vyberte požadované rozšíření.
4. Zvolte **odinstalovat**.

## <a name="common-vm-extension-reference"></a>Běžné odkaz na rozšíření virtuálního počítače

| Název rozšíření | Popis | Další informace |
| --- | --- | --- |
| Rozšíření vlastních skriptů pro Linux |Spouštění skriptů pro virtuální počítač Azure |[Rozšíření vlastních skriptů pro Linux](custom-script-linux.md) |
| Rozšíření přístupu virtuálních počítačů |Jak opět získat přístup k virtuálnímu počítači Azure |[Rozšíření přístupu virtuálních počítačů](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Rozšíření Azure Diagnostics |Správa Azure Diagnostics |[Rozšíření Azure Diagnostics](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Rozšíření přístupu virtuálních počítačů Azure |Správa uživatelů a přihlašovacích údajů |[Rozšíření přístupu virtuálních počítačů pro Linux](https://azure.microsoft.com/en-us/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Další postup

Další informace o rozšíření virtuálních počítačů najdete v tématu [přehled funkcí a rozšíření virtuálních počítačů Azure](overview.md).
