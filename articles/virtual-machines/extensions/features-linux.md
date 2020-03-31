---
title: Rozšíření a funkce virtuálních počítačů Azure pro Linux
description: Zjistěte, jaká rozšíření jsou dostupná pro virtuální počítače Azure seskupená podle toho, co poskytují nebo vylepšují.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 52f5d0ec-8f75-49e7-9e15-88d46b420e63
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: akjosh
ms.openlocfilehash: 67df46742be52b03bd91af19654fbfac5df29646
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250514"
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Rozšíření a funkce virtuálních strojů pro Linux

Rozšíření virtuálních počítačů Azure jsou malé aplikace, které na virtuálních počítačích Azure umožňují provádět úlohy konfigurace a automatizace po nasazení. Pokud virtuální počítač vyžaduje například instalaci softwaru, antivirovou ochranu nebo spuštění interního skriptu, je možné pro tento účel použít rozšíření virtuálního počítače. Rozšíření virtuálních počítačů Azure je možné spustit přes Azure CLI, PowerShell, šablony Azure Resource Manageru a Azure Portal. Rozšíření lze spojit s novým nasazením virtuálního počítače, případně spustit v jakémkoli existujícím systému.

Tento článek obsahuje přehled rozšíření virtuálních počítačů, předpoklady pro použití rozšíření virtuálních zařízení Azure a pokyny, jak zjistit, spravovat a odebírat rozšíření virtuálních počítačových společností. Tento článek poskytuje zobecněné informace, protože mnoho rozšíření virtuálních počítačů jsou k dispozici, každý s potenciálně jedinečnou konfiguraci. Podrobnosti specifické pro rozšíření lze nalézt v každém dokumentu specifické pro jednotlivé rozšíření.

## <a name="use-cases-and-samples"></a>Případy použití a vzorky

K dispozici je několik různých rozšíření virtuálních počítačů Azure, z nichž každé má konkrétní případ použití. Možné příklady:

- Použijte konfigurace požadovaného stavu prostředí PowerShell u virtuálního počítače s rozšířením DSC pro Linux. Další informace naleznete v [tématu Rozšíření konfigurace Požadované ho stavu Azure](https://github.com/Azure/azure-linux-extensions/tree/master/DSC).
- Konfigurace monitorování virtuálního počítače s rozšířením virtuálního počítače Microsoft Monitoring Agent. Další informace najdete [v tématu Jak sledovat virtuální počítač s Linuxem](../linux/tutorial-monitoring.md).
- Nakonfigurujte monitorování infrastruktury Azure pomocí rozšíření Chef nebo Datadog. Další informace naleznete v blogu [Chef docs](https://docs.chef.io/azure_portal.html) nebo [Datadog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).

Kromě rozšíření specifických pro proces je k dispozici rozšíření Custom Script pro virtuální počítače s Windows i Linuxem. Rozšíření vlastní skript pro Linux umožňuje spuštění libovolného skriptu Bash na virtuálním počítači. Vlastní skripty jsou užitečné pro navrhování nasazení Azure, které vyžadují konfiguraci nad rámec toho, co mohou poskytovat nativní nástroje Azure. Další informace naleznete v [tématu Rozšíření vlastního skriptu virtuálního počítače v Linuxu](custom-script-linux.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li zpracovat rozšíření na virtuálním počítači, budete potřebovat nainstalovaný agent Azure Linux. Některá jednotlivá rozšíření mají požadavky, například přístup k prostředkům nebo závislostem.

### <a name="azure-vm-agent"></a>Agent virtuálního počítače Azure

Agent virtuálního počítače Azure spravuje interakce mezi virtuálním počítačem Azure a řadičem infrastruktury Azure. Agent virtuálního počítače je zodpovědný za mnoho funkčních aspektů nasazení a správy virtuálních počítačích Azure, včetně spuštění rozšíření virtuálních počítačů. Agent virtuálního počítače Azure je předinstalovaný na ibi Azure Marketplace a dá se nainstalovat ručně do podporovaných operačních systémů. Agent virtuálního počítače Azure pro Linux se označuje jako agent Linuxu.

Informace o podporovaných operačních systémech a pokynech k instalaci najdete v [tématu Azure virtual machine agent](agent-linux.md).

#### <a name="supported-agent-versions"></a>Podporované verze agentů

Chcete-li poskytnout nejlepší možné zkušenosti, existují minimální verze agenta. Další informace najdete v [tomto článku](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Podporované operační systémy

Agent Linuxu běží na více operačních systémem, ale rozšíření rozhraní má limit pro operační systémy, které rozšíření. Další informace najdete v [tomto článku](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems
).

Některá rozšíření nejsou podporována ve všech operačních systémech a mohou vyzařovat *kód chyby 51, "Nepodporovaný operační systém"*. Zkontrolujte, zda je v dokumentaci k jednotlivým rozšířením k podpoře.

#### <a name="network-access"></a>Síťový přístup

Balíčky rozšíření se stáhnou z úložiště rozšíření Azure Storage a odeslání stavu rozšíření se zaúčtují do Služby Azure Storage. Pokud používáte [podporovanou](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) verzi agentů, nemusíte povolit přístup k Azure Storage v oblasti virtuálních zařízení, jako agent přesměrovat komunikaci do řadiče infrastruktury Azure pro komunikaci agenta. Pokud jste na nepodporovanou verzi agenta, musíte povolit odchozí přístup k úložišti Azure v této oblasti z virtuálního počítače.

> [!IMPORTANT]
> Pokud jste zablokovali přístup k *168.63.129.16* pomocí brány firewall pro hosty, rozšíření se nezdaří bez ohledu na výše uvedené.

Agenti lze použít pouze ke stažení balíčků rozšíření a stavu vykazování. Pokud například instalace rozšíření potřebuje stáhnout skript z GitHubu (vlastní skript) nebo potřebuje přístup k Úložišti Azure Storage (Azure Backup), je třeba otevřít další porty firewallu/skupiny zabezpečení sítě. Různá rozšíření mají různé požadavky, protože se jedná o aplikace samy o sobě. U rozšíření, která vyžadují přístup k Azure Storage, můžete povolit přístup pomocí značek služby Azure NSG service pro [úložiště](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Chcete-li přesměrovat požadavky na přenos agentů, má agent Linuxu podporu proxy serveru. Tato podpora serveru proxy však nepoužije rozšíření. Každé jednotlivé rozšíření je nutné nakonfigurovat tak, aby fungovalo s proxy serverem.

## <a name="discover-vm-extensions"></a>Objevte rozšíření virtuálních míchačů

Pro použití s virtuálními počítači Azure je k dispozici řada různých rozšíření virtuálních počítačů. Chcete-li zobrazit úplný seznam, použijte [seznam obrázků rozšíření az vm](/cli/azure/vm/extension/image#az-vm-extension-image-list). V následujícím příkladu jsou uvedena všechna dostupná rozšíření v umístění *westus:*

```azurecli
az vm extension image list --location westus --output table
```

## <a name="run-vm-extensions"></a>Spuštění rozšíření virtuálních míchačů

Rozšíření virtuálních počítačů Azure běží na stávajících virtuálních počítačích, což je užitečné, když potřebujete provést změny konfigurace nebo obnovit připojení na již nasazený virtuální počítač. Rozšíření virtuálních aplikací můžou být taky svázaná s nasazeními šablon Azure Resource Manageru. Pomocí rozšíření se šablonami Správce prostředků lze virtuální počítače Azure nasadit a nakonfigurovat bez zásahu po nasazení.

Následující metody lze použít ke spuštění rozšíření proti existující virtuální hod.

### <a name="azure-cli"></a>Azure CLI

Rozšíření virtuálních počítačů Azure lze spustit proti existující virtuální počítač s příkazem [az vm rozšíření sady.](/cli/azure/vm/extension#az-vm-extension-set) Následující příklad spustí rozšíření Vlastní skript proti virtuálnímu virtuálnímu uživateli s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*. Nahraďte název skupiny prostředků příkladu, název\/virtuálního počítače a skript, který chcete spustit (https: /raw.githubusercontent.com/me/project/hello.sh) vlastními informacemi. 

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/me/project/hello.sh"],"commandToExecute": "./hello.sh"}'
```

Při správném spuštění rozšíření je výstup podobný následujícímu příkladu:

```bash
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>portál Azure

Rozšíření virtuálních počítače se dá použít na existující virtuální počítač prostřednictvím portálu Azure. Vyberte virtuální virtuální ms na portálu, zvolte **Rozšíření**a pak vyberte **Přidat**. Ze seznamu dostupných rozšíření vyberte požadované rozšíření a postupujte podle pokynů průvodce.

Následující obrázek znázorňuje instalaci rozšíření Linux Custom Script z webu Azure Portal:

![Instalace rozšíření vlastního skriptu](./media/features-linux/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru

Rozšíření virtuálních počítače lze přidat do šablony Azure Resource Manager a spustit s nasazením šablony. Když nasadíte rozšíření se šablonou, můžete vytvořit plně nakonfigurovaná nasazení Azure. Například následující JSON se přebere ze šablony Správce prostředků, která nasazuje sadu virtuálních počítačích s vyrovnáváním zatížení a databázi Azure SQL a pak nainstaluje aplikaci .NET Core na každý virtuální počítač. Rozšíření virtuálního počítače se postará o instalaci softwaru.

Další informace naleznete v úplné [šabloně Správce prostředků](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

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

Další informace o vytváření šablon Správce prostředků najdete v [tématu Vytváření šablon Azure Resource Manageru](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Zabezpečená data rozšíření virtuálního aplikace

Při spuštění rozšíření virtuálního počítačů může být nutné zahrnout citlivé informace, jako jsou přihlašovací údaje, názvy účtů úložiště a přístupové klíče účtu úložiště. Mnoho rozšíření virtuálních počítačů obsahuje chráněnou konfiguraci, která šifruje data a dešifruje je pouze uvnitř cílového virtuálního počítače. Každé rozšíření má specifické chráněné schéma konfigurace a každý je podrobně popsán v dokumentaci specifické pro rozšíření.

Následující příklad ukazuje instanci rozšíření Custom Script pro Linux. Příkaz, který má být proveden, obsahuje sadu pověření. V tomto příkladu není příkaz, který má být proveden, šifrován:

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

Přesunutím **příkazu ke spuštění** vlastnosti do **chráněné** konfigurace je zabezpečen řetězec spuštění, jak je znázorněno v následujícím příkladu:

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

### <a name="how-do-agents-and-extensions-get-updated"></a>Jak se agenti a rozšíření aktualizují?

Agenti a rozšíření sdílejí stejný mechanismus aktualizace. Některé aktualizace nevyžadují další pravidla brány firewall.

Když je k dispozici aktualizace, je nainstalovaný na virtuálním počítači, když dojde ke změně rozšíření a další změny modelu virtuálního počítače, jako jsou:

- Datové disky
- Rozšíření
- Kontejner diagnostiky spouštění
- Tajné kódy hostovaného operačního es
- Velikost virtuálního počítače
- Profil sítě

Vydavatelé zpřístupňují aktualizace do oblastí v různých časech, takže je možné, že virtuální počítači můžete mít v různých oblastech v různých verzích.

#### <a name="agent-updates"></a>Aktualizace agentů

Agent virtuálního počítače Linux obsahuje *kód zprostředkovatele zřizování* a *kód zpracování rozšíření* v jednom balíčku, který nelze oddělit. *Agenta zřizování* můžete zakázat, pokud chcete zřídit v Azure pomocí cloud-init. Chcete-li to provést, naleznete [v tématu pomocí cloud-init](../linux/using-cloud-init.md).

Podporované verze agentů mohou používat automatické aktualizace. Jediný kód, který lze aktualizovat, je *kód zpracování rozšíření*, nikoli kód zřizování. *Kód agenta zřizování* je kód spustit jednou.

*Kód zpracování rozšíření* je zodpovědný za komunikaci s prostředků infrastruktury Azure a zpracování operací rozšíření virtuálních počítače, jako jsou instalace, stav vykazování, aktualizace jednotlivých rozšíření a jejich odebrání. Aktualizace obsahují opravy zabezpečení, opravy chyb a vylepšení *kódu zpracování rozšíření*.

Při instalaci agenta je vytvořen nadřazený daemon. Tento nadřazený potom spouští podřízený proces, který se používá ke zpracování rozšíření. Pokud je pro agenta k dispozici aktualizace, je stažena, nadřazený zastaví podřízený proces, inovuje jej a restartuje jej. Pokud by došlo k potížím s aktualizací, nadřazený proces se vrátí k předchozí podřízené verzi.

Nadřazený proces nelze automaticky aktualizovat. Nadřazený objekt lze aktualizovat pouze aktualizací distro balíčku.

Chcete-li zkontrolovat, jakou `waagent` verzi používáte, zkontrolujte následující:

```bash
waagent --version
```

Výstup se podobá následujícímu příkladu:

```bash
WALinuxAgent-2.2.17 running on ubuntu 16.04
Python: 3.5.2
Goal state agent: 2.2.18
```

V předchozím příkladu výstupu je nadřazená nebo "nasazená verze balíčku" *WALinuxAgent-2.2.17*

"Agent stavu cíle" je verze automatické aktualizace.

Důrazně doporučujeme, abyste měli vždy automatickou aktualizaci [agenta, AutoUpdate.Enabled=y](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent). Pokud to toto povolení není povoleno, je třeba ručně aktualizovat agenta a nezískat opravy chyb a zabezpečení.

#### <a name="extension-updates"></a>Aktualizace rozšíření

Když je k dispozici aktualizace rozšíření, Linux Agent stáhne a upgraduje rozšíření. Automatické aktualizace rozšíření jsou *menší* nebo *oprava hotfix*. Při zřizování rozšíření se můžete odhlásit nebo odhlásit z *rozšíření.* Následující příklad ukazuje, jak automaticky upgradovat dílčí verze v šabloně Správce prostředků s *autoUpgradeMinorVersion": true,'*:

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

Chcete-li získat nejnovější drobné opravy chyb vydání, důrazně doporučujeme vždy vybrat automatickou aktualizaci v nasazení rozšíření. Aktualizace opravy hotfix, které provádějí opravy zabezpečení nebo opravy chyb klíčů, nelze odhlášení.

### <a name="how-to-identify-extension-updates"></a>Jak identifikovat aktualizace rozšíření

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identifikace, pokud je rozšíření nastaveno pomocí automatického upgraduMinorVersion na virtuálním počítači

Můžete vidět z modelu virtuálního soudu, pokud rozšíření bylo zřízeno s "autoUpgradeMinorVersion". Chcete-li zkontrolovat, použijte [az vm show](/cli/azure/vm#az-vm-show) a zadejte název skupiny prostředků a virtuálního soudu takto:

```azurecli
az vm show --resource-group myResourceGroup --name myVM
```

Následující příklad výstupu ukazuje, že *autoUpgradeMinorVersion* je nastavena na *hodnotu true*:

```json
  "resources": [
    {
      "autoUpgradeMinorVersion": true,
      "forceUpdateTag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/CustomScriptExtension",
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identifikace, kdy došlo k automatickému upgraduMinorVersion

Chcete-li zjistit, kdy došlo k aktualizaci rozšíření, zkontrolujte protokoly agenta na virtuálním počítači na */var/log/waagent.log*.

V níže uvedeném příkladu měl virtuální počítač nainstalovanou *microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025.* Oprava hotfix byla k dispozici pro *microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027*:

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

Chcete-li provádět své úkoly, musí agent spustit jako *root*.

## <a name="troubleshoot-vm-extensions"></a>Poradce při potížích s rozšířeními virtuálních míchačů

Každé rozšíření virtuálního virtuálního virtuálního montovny může mít kroky řešení potíží specifické pro rozšíření. Například při použití rozšíření vlastní skript, podrobnosti spuštění skriptu lze nalézt místně na virtuálním počítači, kde byla spuštěna rozšíření. Všechny kroky řešení potíží specifické pro rozšíření jsou podrobně popsány v dokumentaci specifické pro rozšíření.

Následující kroky řešení potíží platí pro všechna rozšíření virtuálních mit.

1. Chcete-li zkontrolovat protokol agenta Linuxu, podívejte se na aktivitu, když bylo vaše rozšíření zřizováno v */var/log/waagent.log*

2. Zkontrolujte skutečné protokoly rozšíření pro další podrobnosti v */var/log/azure/\<extensionName>*

3. Zkontrolujte, zda v částech s řešením potíží s dokumentací pro konkrétní rozšíření nenajdete kódy chyb, známé problémy atd.

3. Podívejte se na systémové protokoly. Zkontrolujte další operace, které mohly narušit rozšíření, jako je například dlouhotrvající instalace jiné aplikace, která vyžadovala výhradní přístup správce balíčků.

### <a name="common-reasons-for-extension-failures"></a>Běžné důvody selhání rozšíření

1. Rozšíření mají 20 minut ke spuštění (výjimkou jsou rozšíření CustomScript, Chef a DSC, které mají 90 minut). Pokud vaše nasazení překročí tuto dobu, je označen jako časový limit. Příčinou může být nedostatek prostředků virtuálních počítačů, jiné konfigurace virtuálních počítačů/spuštění úlohy spotřebovávají vysoké množství prostředků, zatímco rozšíření se pokouší zřídit.

2. Minimální předpoklady nejsou splněny. Některá rozšíření mají závislosti na virtuálních aplikacích SKU, jako jsou například image HPC. Rozšíření mohou vyžadovat určité požadavky na přístup k síti, jako je například komunikace s Azure Storage nebo veřejnými službami. Dalšími příklady mohou být přístup k úložištím balíčků, nedostatek místa na disku nebo omezení zabezpečení.

3. Exkluzivní přístup správce balíčků. V některých případech může dojít k dlouho běžící konfiguraci virtuálního počítače a rozšíření instalace konfliktní, kde oba potřebují výhradní přístup ke správci balíčků.

### <a name="view-extension-status"></a>Zobrazit stav rozšíření

Po rozšíření virtuálního virtuálního provozu byla spuštěna proti virtuálnímu virtuálnímu provozu, použijte [az vm get-instance zobrazení](/cli/azure/vm#az-vm-get-instance-view) vrátit stav rozšíření takto:

```azurecli
az vm get-instance-view \
    --resource-group rgName \
    --name myVM \
    --query "instanceView.extensions"
```

Výstup je podobný následujícímu příkladu výstupu:

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

Stav spuštění rozšíření se také nachází na webu Azure Portal. Pokud chcete zobrazit stav rozšíření, vyberte virtuální honový virtuální ms, zvolte **Rozšíření**, pak vyberte požadované rozšíření.

### <a name="rerun-a-vm-extension"></a>Opětovné spuštění rozšíření virtuálního mísa

Mohou existovat případy, ve kterých je třeba znovu spustit rozšíření virtuálního provozu. Rozšíření můžete znovu spustit odebráním a opětovným spuštěním rozšíření s metodou spuštění podle vašeho výběru. Chcete-li rozšíření odebrat, použijte [odstranění rozšíření az vm](/cli/azure/vm/extension#az-vm-extension-delete) následujícím způsobem:

```azurecli
az vm extension delete \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name customScript
```

Rozšíření na webu Azure Portal můžete taky odebrat takto:

1. Vyberte virtuální hod.
2. Zvolte **Rozšíření**.
3. Vyberte požadované rozšíření.
4. Zvolte **Odinstalovat**.

## <a name="common-vm-extension-reference"></a>Odkaz na rozšíření běžného virtuálního virtuálního vztahu

| Název rozšíření | Popis | Další informace |
| --- | --- | --- |
| Rozšíření vlastního skriptu pro Linux |Spouštění skriptů proti virtuálnímu počítači Azure |[Rozšíření vlastního skriptu pro Linux](custom-script-linux.md) |
| Rozšíření přístupu virtuálních počítačů |Opětovné získání přístupu k virtuálnímu počítači Azure |[Rozšíření přístupu virtuálních počítačů](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Rozšíření Azure Diagnostics |Správa diagnostiky Azure |[Rozšíření Diagnostiky Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Rozšíření Přístupu virtuálních počítačů Azure |Správa uživatelů a přihlašovacích údajů |[Rozšíření Přístupu k virtuálním montovně pro Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Další kroky

Další informace o rozšířeních virtuálních virtuálních zařízení najdete v [tématu Rozšíření virtuálních zařízení Azure a přehled funkcí](overview.md).
