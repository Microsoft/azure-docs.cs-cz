---
title: Použití klíčů SSH pro připojení k virtuálním počítačům se systémem Linux
description: Naučte se generovat a používat klíče SSH z počítače s Windows pro připojení k virtuálnímu počítači se systémem Linux v Azure.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.date: 10/31/2020
ms.topic: how-to
ms.author: cynthn
ms.openlocfilehash: 167ce63931155f5142ed34b41f857505699bc0a6
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102552775"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Použití klíčů SSH s Windows v Azure

Tento článek je určen pro uživatele systému Windows, kteří chtějí [vytvářet](#create-an-ssh-key-pair) a používat klíče *Secure Shell* (SSH) pro [připojení](#connect-to-your-vm) k virtuálním počítačům se systémem Linux v Azure. [V Azure Portal můžete také vygenerovat a ukládat klíče SSH](../ssh-keys-portal.md) , které se použijí při vytváření virtuálních počítačů na portálu.


Postup použití klíčů SSH z klienta se systémem Linux nebo macOS naleznete v tématu [rychlé kroky](mac-create-ssh-keys.md). Podrobnější přehled služby SSH najdete v tématu [podrobný postup: vytváření a Správa klíčů ssh pro ověřování na virtuálním počítači Linux v Azure](create-ssh-keys-detailed.md).

## <a name="overview-of-ssh-and-keys"></a>Přehled SSH a klíčů

[SSH](https://www.ssh.com/ssh/) je zašifrovaný protokol připojení, který umožňuje zabezpečená přihlášení přes nezabezpečená připojení. SSH je výchozí protokol připojení pro virtuální počítače se systémem Linux hostované v Azure. I když SSH sám poskytuje šifrované připojení, používá hesla s SSH i nadále virtuální počítač zranitelný proti útokům hrubou silou. Doporučujeme připojit se k virtuálnímu počítači přes protokol SSH pomocí páru klíčů veřejného a soukromého klíče, označovaného také jako *klíče SSH*. 

Pár klíčů veřejného a soukromého klíče je podobný jako zámek na vašich frontách. Zámek **je zveřejněný, kdokoli**, kdo má správný klíč, může otevřít dveře. Klíč je **soukromý** a je určený jenom lidem, kterým důvěřujete, protože ho můžete použít k odemknutí dveří. 

- *Veřejný klíč* se při vytváření virtuálního počítače umístí do virtuálního počítače se systémem Linux. 

- *Privátní klíč* zůstane v místním systému. Chraňte tento privátní klíč. Nesdílejte ho.

Když se připojíte k VIRTUÁLNÍmu počítači se systémem Linux, virtuální počítač otestuje klienta SSH, aby se ujistil, že má správný privátní klíč. Pokud má klient privátní klíč, získá přístup k virtuálnímu počítači. 

V závislosti na zásadách zabezpečení vaší organizace můžete použít jeden pár klíčů pro přístup k několika virtuálním počítačům a službám Azure. Pro každý virtuální počítač nepotřebujete samostatné dvojice klíčů. 

Veřejný klíč se dá sdílet s kýmkoli, ale k privátnímu klíči by měl mít přístup jenom vy (nebo vaše místní infrastruktura zabezpečení).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-clients"></a>Klienti SSH

Poslední verze Windows 10 zahrnují [příkazy klienta OpenSSH](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) k vytvoření a použití klíčů ssh a vytvoření připojení SSH z PowerShellu nebo příkazového řádku. Toto je nejjednodušší způsob, jak vytvořit připojení SSH k VIRTUÁLNÍmu počítači Linux z počítače se systémem Windows. 

K připojení k VIRTUÁLNÍmu počítači můžete použít také Bash v [Azure Cloud Shell](../../cloud-shell/overview.md) . Cloud Shell můžete použít ve [webovém prohlížeči](https://shell.azure.com/bash), v [Azure Portal](https://portal.azure.com)nebo jako terminál v Visual Studio Code pomocí [rozšíření účtu Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

[Podsystém Windows pro Linux](/windows/wsl/about) můžete také nainstalovat pro připojení k virtuálnímu počítači přes SSH a použít jiné nativní nástroje pro Linux v prostředí bash shell.

## <a name="create-an-ssh-key-pair"></a>Vytvoření páru klíčů SSH

Pomocí příkazu vytvořte dvojici klíčů SSH `ssh-keygen` . Zadejte název souboru nebo použijte výchozí hodnotu zobrazenou v závorkách (například `C:\Users\username/.ssh/id_rsa` ).  Zadejte pro tento soubor přístupové heslo, nebo pokud nechcete použít přístupové heslo, ponechte toto heslo prázdné. 

```powershell
ssh-keygen -m PEM -t rsa -b 4096
```

## <a name="create-a-vm-using-your-key"></a>Vytvoření virtuálního počítače pomocí klíče

Pokud chcete vytvořit virtuální počítač se systémem Linux, který používá klíče SSH k ověřování, zadejte svůj veřejný klíč SSH při vytváření virtuálního počítače.

Pomocí rozhraní příkazového řádku Azure můžete zadat cestu a název souboru pro veřejný klíč pomocí `az vm create` parametru a `--ssh-key-value` .

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVM \
   --image UbuntuLTS\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

Pomocí prostředí PowerShell použijte `New-AzVM` a přidejte klíč SSH do konfigurace virtuálního počítače pomocí. Příklad najdete v tématu [rychlý Start: Vytvoření virtuálního počítače se systémem Linux v Azure pomocí PowerShellu](quick-create-powershell.md).

Pokud máte na portálu spoustu nasazení, možná budete chtít odeslat veřejný klíč do Azure, kde ho můžete snadno vybrat při vytváření virtuálního počítače z portálu. Další informace najdete v tématu [nahrání klíče SSH](../ssh-keys-portal.md#upload-an-ssh-key).


## <a name="connect-to-your-vm"></a>Připojení k virtuálnímu počítači

S veřejným klíčem nasazeným na VIRTUÁLNÍm počítači Azure a privátním klíčem v místním systému se k vašemu VIRTUÁLNÍmu počítači přihlaste přes SSH pomocí IP adresy nebo názvu DNS vašeho virtuálního počítače. V následujícím příkazu nahraďte *azureuser* a *10.111.12.123* uživatelským jménem správce, IP adresou (nebo plně kvalifikovaným názvem domény) a cestou k vašemu privátnímu klíči:

```bash
ssh -i ~/.ssh/id_rsa.pub azureuser@10.111.12.123
```

Pokud jste při vytváření páru klíčů nakonfigurovali heslo, po zobrazení výzvy zadejte heslo.

Pokud virtuální počítač používá zásady přístupu za běhu, musíte požádat o přístup, abyste se mohli připojit k virtuálnímu počítači. Další informace o zásadách za běhu najdete v tématu [Správa přístupu k virtuálním počítačům pomocí zásad podle časových údajů](../../security-center/security-center-just-in-time.md).


## <a name="next-steps"></a>Další kroky

- Informace o klíčích SSH v Azure Portal najdete v tématu [generování a ukládání klíčů ssh v Azure Portal](../ssh-keys-portal.md) pro použití při vytváření virtuálních počítačů na portálu.

- Podrobný postup, možnosti a rozšířené příklady práce s klíči SSH najdete v tématu [podrobný postup vytvoření párů klíčů ssh](create-ssh-keys-detailed.md).

- PowerShell můžete také použít v Azure Cloud Shell k vygenerování klíčů SSH a k vytvoření připojení SSH k virtuálním počítačům se systémem Linux. Další informace najdete v tématu [rychlý Start prostředí PowerShell](../../cloud-shell/quickstart-powershell.md#ssh).

- Pokud máte potíže s použitím protokolu SSH pro připojení k virtuálním počítačům se systémem Linux, přečtěte si téma [řešení potíží s připojením SSH k virtuálnímu počítači Azure Linux](../troubleshooting/troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json).
