---
title: Rozšíření a funkce virtuálních počítačů Azure pro Linux
description: Zjistěte, jaká rozšíření jsou k dispozici pro virtuální počítače Azure v systému Linux, seskupená podle toho, co poskytují nebo zlepšují.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 03/30/2018
ms.openlocfilehash: b6a834ede381ee319d3ff7678f49febd11e34a5d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559763"
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Rozšíření a funkce virtuálních počítačů pro Linux

Rozšíření virtuálních počítačů Azure jsou malé aplikace, které na virtuálních počítačích Azure umožňují provádět úlohy konfigurace a automatizace po nasazení. Pokud virtuální počítač vyžaduje například instalaci softwaru, antivirovou ochranu nebo spuštění interního skriptu, je možné pro tento účel použít rozšíření virtuálního počítače. Rozšíření virtuálních počítačů Azure je možné spustit přes Azure CLI, PowerShell, šablony Azure Resource Manageru a Azure Portal. Rozšíření lze spojit s novým nasazením virtuálního počítače, případně spustit v jakémkoli existujícím systému.

Tento článek poskytuje přehled rozšíření virtuálních počítačů, předpoklady pro používání rozšíření virtuálních počítačů Azure a pokyny k tomu, jak detekovat, spravovat a odebírat rozšíření virtuálních počítačů. Tento článek poskytuje generalizované informace, protože je k dispozici mnoho rozšíření virtuálních počítačů, z nichž každá má potenciálně jedinečnou konfiguraci. Podrobnosti o rozšíření se dají najít v každém dokumentu specifickém pro jednotlivé rozšíření.

## <a name="use-cases-and-samples"></a>Případy použití a ukázky

K dispozici je několik různých rozšíření virtuálních počítačů Azure, z nichž každý má konkrétní případ použití. Možné příklady:

- Použijte konfiguraci požadovaného stavu PowerShellu na virtuální počítač s rozšířením DSC pro Linux. Další informace najdete v tématu [rozšíření konfigurace požadovaného stavu Azure](https://github.com/Azure/azure-linux-extensions/tree/master/DSC).
- Nakonfigurujte monitorování virtuálního počítače pomocí rozšíření virtuálního počítače Microsoft Monitoring Agent. Další informace najdete v tématu [monitorování virtuálního počítače se systémem Linux](/previous-versions/azure/virtual-machines/linux/tutorial-monitor).
- Nakonfigurujte monitorování infrastruktury Azure pomocí rozšíření pro začínající nebo služby Datadog. Další informace najdete v dokumentaci k [docs](https://docs.chef.io/azure_portal.html) nebo na [blogu služby Datadog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).

Kromě rozšíření specifických pro procesy je k dispozici rozšíření vlastních skriptů pro virtuální počítače s Windows i Linux. Rozšíření vlastních skriptů pro Linux umožňuje spustit libovolný skript bash na virtuálním počítači. Vlastní skripty jsou užitečné pro navrhování nasazení Azure, která vyžadují konfiguraci, a to nad rámec toho, co můžou využít nativní nástroje Azure. Další informace najdete v tématu [rozšíření vlastních skriptů pro virtuální počítače Linux](custom-script-linux.md).

## <a name="prerequisites"></a>Předpoklady

Pro zpracování rozšíření na virtuálním počítači potřebujete nainstalovaného agenta Azure Linux. Některá jednotlivá rozšíření mají požadavky, jako je například přístup k prostředkům nebo závislostem.

### <a name="azure-vm-agent"></a>Agent virtuálního počítače Azure

Agent virtuálního počítače Azure spravuje interakce mezi virtuálním počítačem Azure a řadičem prostředků infrastruktury Azure. Agent virtuálního počítače zodpovídá za mnoho funkčních aspektů nasazení a správy virtuálních počítačů Azure, včetně spuštění rozšíření virtuálních počítačů. Agent virtuálního počítače Azure je předinstalovaný na Azure Marketplace imagí a dá se nainstalovat ručně v podporovaných operačních systémech. Agent virtuálního počítače Azure pro Linux je známý jako agent pro Linux.

Informace o podporovaných operačních systémech a pokyny k instalaci najdete v tématu [Agent virtuálního počítače Azure](agent-linux.md).

#### <a name="supported-agent-versions"></a>Podporované verze agentů

Aby se zajistilo nejlepší možné prostředí, existují minimální verze agenta. Další informace najdete v [tomto článku](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Podporované operačních systémech

Agent pro Linux běží na více operačních systémech, ale rozhraní rozšíření má omezení pro operačních systémech rozšíření. Další informace najdete v [tomto článku](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems
).

Některá rozšíření nejsou v rámci všech operačních systémech podporovaná a můžou generovat *kód chyby 51, nepodporovaný operační systém*. Projděte si dokumentaci k jednotlivým příponám, abyste mohli podporu.

#### <a name="network-access"></a>Síťový přístup

Balíčky rozšíření se stáhnou z úložiště rozšíření Azure Storage a nahrávání stavu rozšíření se publikují do Azure Storage. Pokud používáte [podporovanou](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) verzi agentů, nemusíte v oblasti virtuálního počítače povolit přístup k Azure Storage, protože agent může pomocí agenta přesměrovat komunikaci do řadiče Azure Fabric pro komunikaci agenta. Pokud jste v nepodporované verzi agenta, musíte z virtuálního počítače v této oblasti povolený odchozí přístup ke službě Azure Storage.

> [!IMPORTANT]
> Pokud jste zablokovali přístup k *168.63.129.16* pomocí brány firewall hostů, rozšíření selžou bez ohledu na výše uvedené.

Agenty lze použít pouze ke stažení balíčků rozšíření a stavu hlášení. Pokud třeba instalace rozšíření potřebuje stáhnout skript z GitHubu (vlastní skript) nebo potřebuje přístup k Azure Storage (Azure Backup), musí se otevřít další porty skupiny zabezpečení brány firewall/sítě. Různá rozšíření mají různé požadavky, protože se jedná o aplikace v pravém. U rozšíření, která vyžadují přístup k Azure Storage můžete přístup pomocí značek služeb Azure NSG pro [úložiště](../../virtual-network/network-security-groups-overview.md#service-tags)zpřístupnit.

Pro přesměrování požadavků na přenos agenta má agent pro Linux proxy server podporu. Tato proxy server podpora však nepoužívá rozšíření. Je nutné nakonfigurovat každé individuální rozšíření tak, aby fungovalo s proxy serverem.

## <a name="discover-vm-extensions"></a>Zjistit rozšíření virtuálních počítačů

Pro použití s virtuálními počítači Azure je k dispozici řada různých rozšíření virtuálních počítačů. Pokud chcete zobrazit úplný seznam, použijte příkaz [AZ VM Extension image list](/cli/azure/vm/extension/image#az-vm-extension-image-list). V následujícím příkladu jsou uvedena všechna dostupná rozšíření v umístění *westus* :

```azurecli
az vm extension image list --location westus --output table
```

## <a name="run-vm-extensions"></a>Spustit rozšíření virtuálních počítačů

Rozšíření virtuálních počítačů Azure běží na stávajících virtuálních počítačích, což je užitečné v případě, že potřebujete udělat změny konfigurace nebo obnovit připojení na už nasazeném virtuálním počítači. Rozšíření virtuálních počítačů je také možné seskupit pomocí Azure Resource Managerch nasazení šablon. Pomocí rozšíření se šablonami Správce prostředků můžou být virtuální počítače Azure nasazené a nakonfigurované bez zásahu po nasazení.

Pomocí následujících metod lze spustit rozšíření pro existující virtuální počítač.

### <a name="azure-cli"></a>Azure CLI

Rozšíření virtuálních počítačů Azure je možné spustit s existujícím virtuálním počítačem pomocí příkazu [AZ VM Extension set](/cli/azure/vm/extension#az-vm-extension-set) . Následující příklad spustí rozšíření vlastních skriptů na virtuálním počítači s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*. Nahraďte ukázkový název skupiny prostředků, název virtuálního počítače a skript, který se má spustit (https: \/ /raw.githubusercontent.com/me/Project/Hello.sh), s vašimi vlastními informacemi. 

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/me/project/hello.sh"],"commandToExecute": "./hello.sh"}'
```

Pokud se rozšíření spustí správně, výstup je podobný následujícímu příkladu:

```bash
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>portál Azure

Rozšíření virtuálních počítačů je možné použít pro existující virtuální počítač prostřednictvím Azure Portal. Vyberte virtuální počítač na portálu, klikněte na **rozšíření** a pak vyberte **Přidat**. V seznamu dostupných rozšíření vyberte požadované rozšíření a postupujte podle pokynů v průvodci.

Na následujícím obrázku vidíte instalaci rozšíření vlastních skriptů pro Linux z Azure Portal:

![Nainstalovat rozšíření vlastních skriptů](./media/features-linux/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru

Rozšíření virtuálních počítačů lze přidat do šablony Azure Resource Manager a spustit s nasazením šablony. Když nasadíte rozšíření s šablonou, můžete vytvořit plně nakonfigurovaná nasazení Azure. Například následující JSON se převezme z Správce prostředků šablony, která nasadí sadu virtuálních počítačů s vyrovnáváním zatížení a Azure SQL Database a potom do každého virtuálního počítače nainstaluje aplikaci .NET Core. Rozšíření virtuálního počítače má za starosti instalaci softwaru.

Další informace najdete v tématu úplná [Správce prostředků šablona](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

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

Další informace o vytváření šablon Správce prostředků naleznete v tématu [authoring Azure Resource Manager Templates](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Data rozšíření zabezpečeného virtuálního počítače

Když spustíte rozšíření virtuálního počítače, může být nutné zahrnout citlivé informace, jako jsou přihlašovací údaje, názvy účtů úložiště a přístupové klíče účtu úložiště. Mnoho rozšíření virtuálních počítačů zahrnuje chráněnou konfiguraci, která šifruje data a jenom dešifruje v cílovém virtuálním počítači. Každé rozšíření má specifické schéma chráněné konfigurace a každá je podrobně popsána v dokumentaci ke konkrétnímu rozšíření.

Následující příklad ukazuje instanci rozšíření vlastních skriptů pro Linux. Příkaz, který se má provést, zahrnuje sadu přihlašovacích údajů. V tomto příkladu není příkaz ke spuštění zašifrovaný:

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

Přesunutí **příkazu k provedení** vlastnosti do **chráněné** konfigurace zabezpečuje řetězec spuštění, jak je znázorněno v následujícím příkladu:

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

### <a name="how-do-agents-and-extensions-get-updated"></a>Jak se aktualizují agenti a rozšíření?

Agenti a rozšíření mají stejný mechanismus aktualizace. Některé aktualizace nevyžadují další pravidla brány firewall.

Pokud je k dispozici aktualizace, nainstaluje se jenom na virtuální počítač, pokud dojde ke změně rozšíření a dalším změnám modelu virtuálního počítače, jako třeba:

- Datové disky
- Rozšíření
- Kontejner diagnostiky spouštění
- Tajné kódy operačního systému hosta
- Velikost virtuálního počítače
- Profil sítě

Vydavatelé zpřístupňují aktualizace oblastí v různých časech, takže je možné mít virtuální počítače v různých oblastech v různých verzích.

#### <a name="agent-updates"></a>Aktualizace agenta

Agent virtuálního počítače se systémem Linux obsahuje kód *agenta zřizování* a *rozšíření pro zpracování*  kódu v jednom balíčku, který nelze oddělit. *Agent zřizování* můžete zakázat, pokud chcete zřídit Azure pomocí Cloud-init. Postup najdete v tématu [použití Cloud-init](../linux/using-cloud-init.md).

Podporované verze agentů můžou používat automatické aktualizace. Jediný kód, který lze aktualizovat, je *kód pro zpracování rozšíření*, nikoli kód zřizování. *Kód zřizovacího agenta* je kód spustit jako.

*Kód pro manipulaci s rozšířeními* zodpovídá za komunikaci s prostředky infrastruktury Azure a zpracovává operace rozšíření virtuálních počítačů, jako jsou instalace, stav generování sestav, aktualizace jednotlivých rozšíření a jejich odebrání. Aktualizace obsahují opravy zabezpečení, opravy chyb a vylepšení *kódu pro zpracování rozšíření*.

Při instalaci agenta se vytvoří nadřazený démon. Tato nadřazená položka potom vytvoří podřízený proces, který se používá ke zpracování rozšíření. Pokud je pro agenta k dispozici aktualizace, bude stažena, Nadřazená aktivita zastaví podřízený proces, provede upgrade a pak ji restartuje. Pokud by došlo k potížím s aktualizací, nadřazený proces se vrátí zpět na předchozí podřízenou verzi.

Nadřazený proces nelze automaticky aktualizovat. Nadřazenou položku lze aktualizovat pouze pomocí aktualizace balíčku distribuce.

Pokud chcete zjistit, jakou verzi máte spuštěnou, podívejte se na `waagent` následující:

```bash
waagent --version
```

Výstup se podobá následujícímu příkladu:

```bash
WALinuxAgent-2.2.17 running on ubuntu 16.04
Python: 3.5.2
Goal state agent: 2.2.18
```

V předchozím příkladu je výstup nadřazeného objektu nebo balíčku nasazená verze *WALinuxAgent-2.2.17* .

"Agent stavu cíle" je verze automatického aktualizace.

Důrazně doporučujeme, abyste pro agenta vždy měli automatické aktualizace, [AutoUpdate. Enabled = y](./update-linux-agent.md). Tato možnost není povolená znamená, že je potřeba aktualizovat agenta ručně a Nezískávat opravy chyb a zabezpečení.

#### <a name="extension-updates"></a>Aktualizace rozšíření

Pokud je k dispozici aktualizace rozšíření, agent pro Linux stáhne a upgraduje rozšíření. Automatické aktualizace rozšíření jsou buď *drobné* , nebo *hotfix*. Při zřizování rozšíření můžete povolit nebo odhlásit rozšíření *dílčí* aktualizace. Následující příklad ukazuje, jak automaticky upgradovat dílčí verze v Správce prostředků šabloně pomocí *autoUpgradeMinorVersion ": true,"*:

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

Chcete-li získat nejnovější drobné opravy chyb vydaných verzí, důrazně doporučujeme, abyste vždy vybrali možnost automaticky aktualizovat v nasazeních rozšíření. Aktualizace oprav hotfix, které obsahují opravy zabezpečení nebo chyby klíčů, nelze odhlásit.

### <a name="how-to-identify-extension-updates"></a>Jak identifikovat aktualizace rozšíření

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Určení, jestli se rozšíření nastavilo pomocí autoUpgradeMinorVersion na virtuálním počítači

V případě, že se rozšíření zřídilo pomocí ' autoUpgradeMinorVersion ', můžete vidět z modelu virtuálního počítače. Pokud chcete kontrolu ověřit, použijte příkaz [AZ VM show](/cli/azure/vm#az-vm-show) a zadejte skupinu prostředků a název virtuálního počítače následujícím způsobem:

```azurecli
az vm show --resource-group myResourceGroup --name myVM
```

Následující příklad výstupu ukazuje, že *autoUpgradeMinorVersion* je nastavená na *hodnotu true*:

```json
  "resources": [
    {
      "autoUpgradeMinorVersion": true,
      "forceUpdateTag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/CustomScriptExtension",
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identifikace, kdy došlo k autoUpgradeMinorVersion

Pokud chcete zjistit, kdy došlo k aktualizaci rozšíření, zkontrolujte protokoly agenta na virtuálním počítači na adrese */var/log/waagent.log*.

V následujícím příkladu má virtuální počítač nainstalovanou *aplikaci Microsoft. OSTCExtensions. LinuxDiagnostic-2.3.9025* . Byla k dispozici oprava hotfix pro *Microsoft. OSTCExtensions. LinuxDiagnostic-2.3.9027*:

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

Aby bylo možné provádět jeho úkoly, musí být agent spuštěn jako *kořenový*.

## <a name="troubleshoot-vm-extensions"></a>Řešení potíží s rozšířeními virtuálních počítačů

Každé rozšíření virtuálního počítače může mít postup řešení potíží specifický pro toto rozšíření. Pokud například použijete rozšíření vlastních skriptů, podrobnosti spuštění skriptu můžete najít místně na virtuálním počítači, kde bylo rozšíření spuštěno. Jakékoli kroky při odstraňování potíží specifických pro rozšíření jsou podrobně popsané v dokumentaci ke konkrétnímu rozšíření.

Následující kroky pro řešení potíží se vztahují na všechna rozšíření virtuálních počítačů.

1. Pokud chcete zkontrolovat protokol agenta pro Linux, podívejte se na aktivitu při zřizování rozšíření v */var/log/waagent.log* .

2. Další podrobnosti najdete v protokolech aktuálních rozšíření */var/log/Azure/ \<extensionName>* .

3. Podívejte se na oddíly řešení potíží v dokumentaci ke konkrétnímu rozšíření pro kódy chyb, známé problémy atd.

3. Podívejte se na systémové protokoly. Vyhledejte další operace, které mohly být v konfliktu s rozšířením, například dlouhodobě běžící instalace jiné aplikace, která vyžadovala výhradní přístup správce balíčků.

### <a name="common-reasons-for-extension-failures"></a>Běžné důvody selhání rozšíření

1. Spuštění rozšíření má 20 minut (výjimky jsou rozšířeními CustomScript, a DSC a DSC, která mají 90 minut). Pokud nasazení překročí tuto dobu, bude označeno jako časový limit. Příčinou může být to, že virtuální počítače s nízkou spotřebou, další konfigurace virtuálních počítačů nebo úlohy spouštějí úlohy s vysokými objemy prostředků, zatímco se rozšíření pokouší zřídit.

2. Minimální požadavky nejsou splněné. Některá rozšíření mají závislosti na SKU virtuálních počítačů, jako jsou například Image HPC. Rozšíření můžou vyžadovat určité požadavky na přístup k síti, jako je například komunikace s Azure Storage nebo veřejnými službami. Dalším příkladem může být přístup k úložištím balíčků, nedostatek místa na disku nebo omezení zabezpečení.

3. Výhradní přístup správce balíčků. V některých případech se může vyskytnout dlouho běžící konfigurace virtuálního počítače a instalace rozšíření, kde obě potřebují exkluzivní přístup ke Správci balíčků.

### <a name="view-extension-status"></a>Zobrazit stav rozšíření

Po spuštění rozšíření virtuálního počítače s virtuálním počítačem pomocí příkazu [AZ VM Get-instance-View](/cli/azure/vm#az-vm-get-instance-view) můžete stav rozšíření vrátit následujícím způsobem:

```azurecli
az vm get-instance-view \
    --resource-group rgName \
    --name myVM \
    --query "instanceView.extensions"
```

Výstup se podobá následujícímu příkladu výstupu:

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

Stav spuštění rozšíření lze také najít v Azure Portal. Pokud chcete zobrazit stav rozšíření, vyberte virtuální počítač, zvolte **rozšíření** a pak vyberte požadované rozšíření.

### <a name="rerun-a-vm-extension"></a>Opětovné spuštění rozšíření virtuálního počítače

Můžou nastat případy, kdy je potřeba znovu spustit rozšíření virtuálního počítače. Rozšíření můžete znovu spustit tak, že ho odeberete a pak znovu spustíte rozšíření s vámi zvolenou metodou spuštění. Pokud chcete odebrat rozšíření, použijte příkaz [AZ VM Extension Delete](/cli/azure/vm/extension#az-vm-extension-delete) následujícím způsobem:

```azurecli
az vm extension delete \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name customScript
```

Můžete také odebrat rozšíření v Azure Portal následujícím způsobem:

1. Vyberte virtuální počítač.
2. Vyberte **rozšíření**.
3. Vyberte požadované rozšíření.
4. Vyberte možnost **odinstalovat**.

## <a name="common-vm-extension-reference"></a>Obecné odkazy na rozšíření virtuálních počítačů

| Název rozšíření | Description | Další informace |
| --- | --- | --- |
| Rozšíření vlastních skriptů pro Linux |Spouštění skriptů na virtuálním počítači Azure |[Rozšíření vlastních skriptů pro Linux](custom-script-linux.md) |
| Rozšíření přístupu virtuálních počítačů |Opětovné získání přístupu k virtuálnímu počítači Azure |[Rozšíření přístupu virtuálních počítačů](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Rozšíření Azure Diagnostics |Správa Azure Diagnostics |[Rozšíření Azure Diagnostics](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Rozšíření přístupu k virtuálnímu počítači Azure |Správa uživatelů a přihlašovacích údajů |[Rozšíření přístupu virtuálních počítačů pro Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Další kroky

Další informace o rozšíření virtuálních počítačů najdete v tématu [Přehled rozšíření a funkcí virtuálních počítačů Azure](overview.md).