---
title: Monitorování virtuálního počítače s Linuxem pomocí rozšíření virtuálního počítače | Dokumentace Microsoftu
description: Další informace o použití diagnostického rozšíření Linuxu pro monitorování výkonu a diagnostických dat virtuálního počítače s Linuxem v Azure.
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
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "32193022"
---
# <a name="use-the-linux-diagnostic-extension-to-monitor-the-performance-and-diagnostic-data-of-a-linux-vm"></a>Použití diagnostického rozšíření Linuxu pro monitorování údajů o výkonu a diagnostických dat virtuálního počítače s Linuxem

Tento dokument popisuje verzi 2.3 diagnostického rozšíření Linuxu.

> [!IMPORTANT]
> Tato verze se už nepoužívá a může nepublikovaných kdykoli po 30. června 2018. Nahradila ji verze 3.0. Další informace najdete v tématu [dokumentaci diagnostického rozšíření Linux verze 3.0](../diagnostic-extension.md).

## <a name="introduction"></a>Úvod

(**Poznámka**: diagnostického rozšíření Linuxu je open source na [Githubu](https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic) kde aktuální informace o rozšíření je publikován. Můžete chtít zkontrolovat [stránku Githubu](https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic) první.)

Diagnostické rozšíření Linux pomáhá uživateli monitorování virtuálních počítačů s Linuxem, který běží v Microsoft Azure. Má následující možnosti:

* Shromažďuje a odesílá informace o výkonu systému z virtuálního počítače s Linuxem do tabulky úložiště uživatele, včetně informace diagnostiky a syslog.
* Umožňuje uživatelům přizpůsobení, který bude shromážděna a nahráli data metriky.
* Umožňuje uživatelům odeslat zadané soubory protokolu do tabulky úložiště určené.

V aktuální verzi 2.3 data obsahují:

* Všechny Linux Rsyslog protokoly, včetně systému, zabezpečení a protokolů aplikací.
* Všechna data systému, které je zadáno v [lokality řešení pro System Center pro různé platformy](https://scx.codeplex.com/wikipage?title=xplatproviders).
* Soubory protokolu zadané uživatelem.

Toto rozšíření funguje s i klasické modely nasazení a modely nasazení Resource Manager.

### <a name="current-version-of-the-extension-and-deprecation-of-old-versions"></a>Aktuální verzi rozšíření a vyřazení staré verze

Nejnovější verze tohoto rozšíření je **2.3**, a **všechny starší verze (2.0, 2.1 nebo 2.2) bude zastaralé a Nepublikováno konce tohoto roku (2017)**. Pokud jste nainstalovali rozšíření Linux Diagnostic vedlejší verze aktualizace automatického upgradu zakázán, doporučujeme odinstalovat rozšíření a znovu ji nainstalujte vedlejší verze aktualizace automatického upgradu povolené. Na virtuální počítače classic (ASM) lze toho dosáhnout tak, že zadáte "2.*" jako verze při instalaci rozšíření prostřednictvím Azure XPLAT CLI nebo Powershellu. Na virtuálních počítačích s ARM, můžete dosáhnout zahrnutím ""verzi autoUpgradeMinorVersion": true" v nasazení šablony virtuálního počítače. Všechny nové instalace rozšíření by měl mít také vedlejší verze automaticky zapnuta možnost upgradovat.

## <a name="enable-the-extension"></a>Povolení rozšíření

Toto rozšíření můžete povolit pomocí [webu Azure portal](https://portal.azure.com/#), Azure Powershellu nebo skriptů rozhraní příkazového řádku Azure.

Pokud chcete zobrazit a konfigurovat výkon systému a data přímo z webu Azure portal, postupujte [tyto kroky na blogu Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/).

Tento článek se zaměřuje na tom, jak povolit a konfigurovat rozšíření pomocí příkazů rozhraní příkazového řádku Azure. To umožňuje čtení a zobrazení dat přímo z tabulky úložiště.

Všimněte si, že metody konfigurace, které jsou popsány zde nebude fungovat pro na webu Azure portal. K zobrazení a konfiguraci výkon systému a data přímo z webu Azure portal, musí být povoleno rozšíření na portálu.

## <a name="prerequisites"></a>Požadavky

* **Agent Azure Linux verze 2.0.6 nebo novější**.

  Chtěli bychom upozornit, že většina Image z Galerie virtuálních počítačů Azure s Linuxem zahrnují verze 2.0.6 nebo novější. Můžete spustit **WAAgent-verze** pro potvrzení, která verze je nainstalovaná na virtuálním počítači. Pokud virtuální počítač běží na verzi, která je starší než 2.0.6, můžete postupovat podle [tyto pokyny na Githubu](https://github.com/Azure/WALinuxAgent "pokyny") ji aktualizovat.
* **Azure CLI**. Postupujte podle [tento návod pro instalaci rozhraní příkazového řádku](../../../cli-install-nodejs.md) k nastavení prostředí rozhraní příkazového řádku Azure na svém počítači. Po instalaci rozhraní příkazového řádku Azure, můžete použít **azure** z rozhraní příkazového řádku (Bash, terminál nebo příkazový řádek) pro přístup k příkazům rozhraní příkazového řádku Azure. Příklad:

  * Spustit **sada rozšíření virtuálního počítače azure – Nápověda** podrobnou nápovědu informace.
  * Spustit **přihlášení k azure** pro přihlášení k Azure.
  * Spustit **seznamu virtuálních počítačů azure** k výpisu všech virtuálních počítačů, které máte v Azure.
* Účet úložiště pro uložení data. Budete potřebovat název účtu úložiště, který byl vytvořen dříve a přístupový klíč k nahrání dat do úložiště.

## <a name="use-the-azure-cli-command-to-enable-the-linux-diagnostic-extension"></a>Povolení diagnostického rozšíření Linux pomocí příkazového řádku Azure

### <a name="scenario-1-enable-the-extension-with-the-default-data-set"></a>Scénář 1. Povolení rozšíření s výchozí sadu dat

Ve verzi 2.3 nebo novější obsahuje výchozí data, která se budou shromažďovat:

* Všechny informace Rsyslog (včetně systému, zabezpečení a protokolů aplikací).  
* Základní sada dat systému základ. Všimněte si, že úplná sada dat je popsaný na [řešení pro System Center pro různé platformy lokality](https://scx.codeplex.com/wikipage?title=xplatproviders).
  Pokud chcete povolit další data, pokračujte v krocích v scénáře 2 a 3.

Krok 1. Vytvořte soubor s názvem PrivateConfig.json s následujícím obsahem:

    {
        "storageAccountName" : "the storage account to receive data",
        "storageAccountKey" : "the key of the account"
    }

Krok 2. Spuštění **vm_name LinuxDiagnostic Microsoft.OSTCExtensions 2 nastavit rozšíření virtuálního počítače azure.* --private-config-path PrivateConfig.json**.

### <a name="scenario-2-customize-the-performance-monitor-metrics"></a>Scénář 2. Přizpůsobení metriky monitorování výkonu

Tato část popisuje, jak přizpůsobit výkonu a diagnostických dat tabulky.

Krok 1. Vytvořte soubor s názvem PrivateConfig.json s obsahem, který byl popsaný ve scénáři 1. Vytvořte také soubor s názvem PublicConfig.json. Zadejte konkrétní data, která chcete shromažďovat.

Pro všechny podporované poskytovatele a proměnné odkazovat [řešení pro System Center pro různé platformy lokality](https://scx.codeplex.com/wikipage?title=xplatproviders). Můžete mít více dotazů a uložit je do několika tabulek přidáním více dotazů do skriptu.

Ve výchozím nastavení, Rsyslog data jsou vždy shromažďována.

    {
          "perfCfg":
          [
              {
                  "query" : "SELECT PercentAvailableMemory, AvailableMemory, UsedMemory ,PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
                  "table" : "LinuxMemory"
              }
          ]
    }


Krok 2. Spustit **rozšíření virtuálního počítače azure nastavit vm_name LinuxDiagnostic Microsoft.OSTCExtensions "2.*'--private-config-path PrivateConfig.json--public-config-path PublicConfig.json**.

### <a name="scenario-3-upload-your-own-log-files"></a>Scénář 3. Odeslání souborů protokolu

Tato část popisuje, jak shromáždit a nahrát do účtu úložiště konkrétní soubory. Je třeba zadat cestu k souboru protokolu a název tabulky, ve které chcete uložit protokol. Více souborů protokolu můžete vytvořit přidáním více položek souboru/tabulky do skriptu.

Krok 1. Vytvořte soubor s názvem PrivateConfig.json s obsahem, který byl popsaný ve scénáři 1. Potom vytvořte jiný soubor s názvem PublicConfig.json s následujícím obsahem:

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

Všimněte si, že se toto nastavení v rozšíření verze starší než 2.3 všechny protokoly zapisují do `/var/log/mysql.err` mohou být duplicitní k `/var/log/syslog` (nebo `/var/log/messages` v závislosti na distribuce Linuxu) i. Pokud chcete, aby se zabránilo duplicitní protokolování, můžete vyloučit protokolování `local6` protokoly zařízení ve vaší konfiguraci rsyslog. Závisí na distribuce Linuxu, ale v systému Ubuntu 14.04, je soubor, který má upravit `/etc/rsyslog.d/50-default.conf` a nahradíte řádek `*.*;auth,authpriv.none -/var/log/syslog` k `*.*;auth,authpriv,local6.none -/var/log/syslog`. Tento problém je vyřešen v nejnovější opravy hotfix verzi 2.3 (2.3.9007), takže pokud máte rozšíření verze 2.3 Tento problém nemělo stát. Pokud je stále využívá i po restartování vašeho virtuálního počítače, kontaktujte nás a pomáhají řešit potíže způsobující není automaticky nainstalovaná nejnovější verze opravy hotfix.

### <a name="scenario-4-stop-the-extension-from-collecting-any-logs"></a>Scénář 4. Zastavit rozšíření nemusí shromažďovat všechny protokoly

Tato část popisuje, jak zastavit rozšíření shromažďování protokolů. Všimněte si, že proces agenta monitorování bude stále v provozu i při použití této změny konfigurace. Pokud chcete úplně zastavit proces agenta monitorování, můžete k tomu při zakázání rozšíření. Pomocí příkazu zakažte rozšíření `azure vm extension set --disable <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions '2.*'`.

Krok 1. Vytvořte soubor s názvem PrivateConfig.json s obsahem, který byl popsaný ve scénáři 1. Vytvořte jiný soubor s názvem PublicConfig.json s následujícím obsahem:

    {
        "perfCfg" : [],
        "enableSyslog" : "false"
    }


Krok 2. Spustit **rozšíření virtuálního počítače azure nastavit vm_name LinuxDiagnostic Microsoft.OSTCExtensions "2.*'--private-config-path PrivateConfig.json--public-config-path PublicConfig.json**.

## <a name="review-your-data"></a>Zkontrolujte data

Výkon a diagnostických dat jsou uložené v tabulce Azure Storage. Kontrola [použití úložiště Azure Table z Ruby](../../../cosmos-db/table-storage-how-to-use-ruby.md) Další informace o přístupu k datům v tabulce úložiště s použitím skriptů příkazového řádku Azure.

Kromě toho můžete tyto nástroje uživatelského rozhraní pro přístup k datům:

1. Průzkumníka serveru Visual Studio. Přejděte do účtu úložiště. Po spuštění virtuálního počítače pro přibližně během pěti minut, uvidíte čtyři výchozí tabulky: "LinuxCpu", "LinuxDisk", "LinuxMemory" a "Linuxsyslog". Dvakrát klikněte na názvy tabulek, chcete-li zobrazit data.
1. [Průzkumník služby Azure Storage](https://azurestorageexplorer.codeplex.com/ "Průzkumníka služby Azure Storage").

![image](./media/diagnostic-extension/no1.png)

Pokud jste povolili fileCfg nebo perfCfg (jak je popsáno v scénáře 2 a 3), můžete použít Průzkumníka serveru Visual Studia a Průzkumníka služby Azure Storage k zobrazení dat jiné než výchozí.

## <a name="known-issues"></a>Známé problémy

* Informace o Rsyslog a zákazníkem zadaný soubor protokolu je přístupný pouze pomocí skriptování.
