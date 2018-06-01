---
title: Monitorování virtuálního počítače s Linuxem pomocí rozšíření virtuálního počítače | Microsoft Docs
description: Další informace o použití rozšíření diagnostiky Linux k monitorování výkonu a diagnostických dat virtuálního počítače s Linuxem v Azure.
services: virtual-machines-linux
author: NingKuang
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: f54a11c5-5a0e-40ff-af6c-e60bd464058b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: Ning
ms.openlocfilehash: f1415e2cfbe48b287db5851bb8ebef1ff9251280
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32193022"
---
# <a name="use-the-linux-diagnostic-extension-to-monitor-the-performance-and-diagnostic-data-of-a-linux-vm"></a>Použití diagnostického rozšíření Linuxu pro monitorování údajů o výkonu a diagnostických dat virtuálního počítače s Linuxem

Tento dokument popisuje 2.3 verzi rozšíření diagnostiky Linux.

> [!IMPORTANT]
> Tato verze je zastaralá a může být publikování kdykoli po 30. června 2018. Nahradila ji verze 3.0. Další informace najdete v tématu [dokumentace pro verzi 3.0 rozšíření diagnostiky Linux](../diagnostic-extension.md).

## <a name="introduction"></a>Úvod

(**Poznámka**: rozšíření diagnostiky Linux je open source na [Githubu](https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic) kde nejaktuálnější informace o rozšíření prvním publikování. Můžete chtít zkontrolovat [GitHub stránce](https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic) první.)

Rozšíření diagnostiky Linux pomáhá uživatele monitorování virtuálních počítačů Linux, které jsou spuštěné v Microsoft Azure. Má následující funkce:

* Shromažďuje a odesílá informace o výkonu systému z virtuálního počítače s Linuxem do tabulky úložiště uživatele, včetně informací o diagnostiky a syslog.
* Umožňuje uživatelům umožnit přizpůsobení metriky dat, které bude shromážděna a nahrát.
* Umožňuje uživatelům odesílat zadané soubory protokolu do tabulky určené úložiště.

V aktuální verzi 2.3 data obsahují:

* Všechny Linux Rsyslog protokoly, včetně systému, zabezpečení a protokoly aplikací.
* Všechna data systému, které je zadáno v [webu řešení System Center křížové platformy](https://scx.codeplex.com/wikipage?title=xplatproviders).
* Soubory protokolu definované uživatelem.

Toto rozšíření pracuje s classic i modelech nasazení Resource Manager.

### <a name="current-version-of-the-extension-and-deprecation-of-old-versions"></a>Aktuální verze rozšíření a vyřazení staré verze

Nejnovější verze rozšíření je **2.3**, a **všechny starší verze (2.0, 2.1 a 2.2) se již nepoužívá a Nepublikováno konce tohoto roku (2017)**. Pokud jste nainstalovali rozšíření diagnostiky Linux automatické podverze upgradu zakázána, doporučujeme odinstalovat rozšíření a znovu ji nainstalujte automatické podverze upgradu povoleno. Na klasické virtuální počítače (ASM) můžete tím dosáhnout zadáním '2.*, jako je verze při instalaci rozšíření prostřednictvím příkazového řádku Azure XPLAT nebo Powershellu. Na virtuálních počítačů ARM, lze dosáhnout zahrnutím ' "autoUpgradeMinorVersion": true, v nasazení šablony virtuálního počítače. Všechny nové instalace rozšíření by měl mít také, podverze automatického upgradu zapnuta možnost.

## <a name="enable-the-extension"></a>Povolení rozšíření

Toto rozšíření můžete povolit pomocí [portál Azure](https://portal.azure.com/#), prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure skripty.

Chcete-li zobrazit a nakonfigurovat výkon systému a data přímo z portálu Azure, postupujte podle [na Azure blog tyto kroky](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/).

Tento článek se zaměřuje na tom, jak povolit a konfigurovat rozšíření pomocí rozhraní příkazového řádku Azure. To umožňuje číst a zobrazovat data přímo z tabulky úložiště.

Všimněte si, že konfigurace metody, které jsou zde popsané nebudou fungovat pro portál Azure. Pokud chcete zobrazit a konfigurovat výkon systému a data přímo z portálu Azure, musí být povolena rozšíření prostřednictvím portálu.

## <a name="prerequisites"></a>Požadavky

* **Azure Linux Agent verze 2.0.6 nebo novější**.

  Všimněte si, že většina Galerie Image virtuálních počítačů Linux Azure zahrnují verze 2.0.6 nebo novější. Můžete spustit **příkaz WAAgent-verze** k potvrzení, která verze je nainstalovaná ve virtuálním počítači. Pokud je virtuální počítač spuštěný na verzi, která je starší než 2.0.6, můžete podle [tyto pokyny na Githubu](https://github.com/Azure/WALinuxAgent "pokyny") jej aktualizovat.
* **Azure CLI**. Postupujte podle [tyto pokyny pro instalaci rozhraní příkazového řádku](../../../cli-install-nodejs.md) nastavení prostředí příkazového řádku Azure CLI na váš počítač. Po instalaci rozhraní příkazového řádku Azure, můžete použít **azure** příkaz vaše rozhraní příkazového řádku (Bash, Terminálové nebo příkazového řádku) pro přístup k příkazy rozhraní příkazového řádku Azure. Příklad:

  * Spustit **sadu rozšíření virtuálního počítače azure – Nápověda** podrobnou nápovědu informace.
  * Spustit **přihlášení k azure** pro přihlášení k Azure.
  * Spustit **seznamu virtuálních počítačů azure** seznam všech virtuálních počítačů, které máte v Azure.
* Účet úložiště pro ukládání dat. Budete potřebovat název účtu úložiště, který byl vytvořen dříve a přístupový klíč chcete nahrát data do úložiště.

## <a name="use-the-azure-cli-command-to-enable-the-linux-diagnostic-extension"></a>Povolit rozšíření diagnostiky Linux pomocí příkazu příkazového řádku Azure CLI

### <a name="scenario-1-enable-the-extension-with-the-default-data-set"></a>Scénář 1. Povolit rozšíření s výchozí sadou dat

Ve verzi 2.3 nebo novější výchozí data, která se budou shromažďovat zahrnují:

* Všechny informace Rsyslog (včetně systému, zabezpečení a protokoly aplikací).  
* Základní sady dat základ systému. Všimněte si, že úplné datové sady je popsáno na [řešení System Center křížové platformy lokality](https://scx.codeplex.com/wikipage?title=xplatproviders).
  Pokud chcete povolit doplňující data, pokračujte kroky v scénáře 2 a 3.

Krok 1. Vytvořte soubor s názvem PrivateConfig.json s následujícím obsahem:

    {
        "storageAccountName" : "the storage account to receive data",
        "storageAccountKey" : "the key of the account"
    }

Krok 2. Spustit \* *vm_name LinuxDiagnostic Microsoft.OSTCExtensions 2 nastavit rozšíření virtuálních počítačů azure.* --PrivateConfig.json** privátní config-path.

### <a name="scenario-2-customize-the-performance-monitor-metrics"></a>Scénář 2. Přizpůsobení metriky sledování výkonu

Tato část popisuje, jak přizpůsobit výkonu a diagnostických dat tabulky.

Krok 1. Vytvořte soubor s názvem PrivateConfig.json s obsahem, který je popsáno ve scénáři 1. Vytvořte také soubor s názvem PublicConfig.json. Zadejte konkrétní data, která chcete shromažďovat.

Pro všechny podporované zprostředkovatele a proměnné odkazují [řešení System Center křížové platformy lokality](https://scx.codeplex.com/wikipage?title=xplatproviders). Můžete mít více dotazů a uložit je do více tabulek přidáním další dotazy do skriptu.

Ve výchozím nastavení je vždy shromažďují Rsyslog data.

    {
          "perfCfg":
          [
              {
                  "query" : "SELECT PercentAvailableMemory, AvailableMemory, UsedMemory ,PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
                  "table" : "LinuxMemory"
              }
          ]
    }


Krok 2. Spustit **rozšíření virtuálního počítače azure nastavit vm_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' – privátní config-path PrivateConfig.json – veřejné config-path PublicConfig.json**.

### <a name="scenario-3-upload-your-own-log-files"></a>Scénář 3. Odeslání souborů protokolu

Tato část popisuje, jak ke sběru a odeslat konkrétní soubory do svého účtu úložiště. Je třeba zadat cestu k souboru protokolu a název tabulky, kam chcete uložit protokolu. Přidáním více položek souboru nebo tabulky do skriptu můžete vytvořit více souborů protokolu.

Krok 1. Vytvořte soubor s názvem PrivateConfig.json s obsahem, který je popsáno ve scénáři 1. Pak vytvořte jiný soubor s názvem PublicConfig.json s následujícím obsahem:

```json
{
    "fileCfg" :
    [
        {
            "file" : "/var/log/mysql.err",
            "table" : "mysqlerr"
            }
    ]
}
```

Krok 2. Spusťte `azure vm extension set vm_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json`.

Všimněte si, že s tímto nastavením na rozšíření verze starší než 2.3 všechny protokoly zapisují do `/var/log/mysql.err` může být duplicitní k `/var/log/syslog` (nebo `/var/log/messages` v závislosti na Linux distro) také. Pokud chcete, aby se zabránilo duplicitním protokolování, můžete je vyloučit protokolování `local6` protokolů zařízení ve vaší konfiguraci rsyslog. Závisí na Linux distro, ale u systému Ubuntu 14.04, je soubor k úpravě `/etc/rsyslog.d/50-default.conf` a můžete nahradit řádek `*.*;auth,authpriv.none -/var/log/syslog` k `*.*;auth,authpriv,local6.none -/var/log/syslog`. Tento problém vyřešen v nejnovější opravy hotfix verzi 2.3 (2.3.9007), takže pokud máte verzi rozšíření 2.3, tento problém došlo k neočekávané chybě. Pokud k tomu ještě i po restartování virtuálního počítače, kontaktujte nás a Pomozte nám Poradce při potížích se není automaticky nainstalovaná nejnovější verze opravy hotfix.

### <a name="scenario-4-stop-the-extension-from-collecting-any-logs"></a>Scénář 4. Zastavit rozšíření z shromažďování žádné protokoly

Tato část popisuje postup zastavení rozšíření z shromažďování protokolů. Všimněte si, že proces agenta monitorování bude dál spuštěný a funkční i přes tuto změnu konfigurace. Pokud chcete úplně zastavit proces agenta monitorování, můžete tak učinit zakázáním rozšíření. Příkaz rozšíření zakázat je `azure vm extension set --disable <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions '2.*'`.

Krok 1. Vytvořte soubor s názvem PrivateConfig.json s obsahem, který je popsáno ve scénáři 1. Vytvořte jiný soubor s názvem PublicConfig.json s následujícím obsahem:

    {
        "perfCfg" : [],
        "enableSyslog" : "false"
    }


Krok 2. Spustit **rozšíření virtuálního počítače azure nastavit vm_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' – privátní config-path PrivateConfig.json – veřejné config-path PublicConfig.json**.

## <a name="review-your-data"></a>Zkontrolujte vaše data

Výkon a diagnostických dat jsou uložené v tabulce Azure Storage. Zkontrolujte [jak používat Azure Table Storage z Ruby](../../../cosmos-db/table-storage-how-to-use-ruby.md) se dozvíte, jak k přístupu k datům v tabulce úložiště pomocí rozhraní příkazového řádku Azure skriptů.

Kromě toho můžete tyto nástroje uživatelského rozhraní pro přístup k datům:

1. Průzkumníka serveru Visual Studio. Přejděte na svůj účet úložiště. Po dobu asi 5 minut spuštění virtuálního počítače, uvidíte čtyři výchozí tabulky: "LinuxCpu", "LinuxDisk", "LinuxMemory" a "Linuxsyslog". Dvakrát klikněte na názvy tabulek, které chcete zobrazit data.
1. [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/ "Azure Storage Explorer").

![Bitové kopie](./media/diagnostic-extension/no1.png)

Pokud jste povolili fileCfg nebo perfCfg (jak je popsáno v scénáře 2 a 3), můžete použít Průzkumníka serveru Visual Studia a Azure Storage Explorer pro zobrazení dat jiné než výchozí.

## <a name="known-issues"></a>Známé problémy

* Rsyslog informace a zákazník zadaný soubor protokolu můžete přistupovat pouze pomocí skriptů.
