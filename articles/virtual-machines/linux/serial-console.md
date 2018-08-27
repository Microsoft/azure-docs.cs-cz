---
title: Konzola sériového portu virtuálního počítače Azure | Dokumentace Microsoftu
description: Obousměrné sériové konzoly pro virtuální počítače Azure.
services: virtual-machines-linux
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/07/2018
ms.author: harijay
ms.openlocfilehash: 0951b0ee8a1b92f94dd06bfad831b3dd9a9e967c
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918213"
---
# <a name="virtual-machine-serial-console-preview"></a>Virtuální počítač sériová konzola (preview) 


Konzole sériového portu virtuálního počítače v Azure poskytuje přístup ke konzole založený na textu pro virtuální počítače s Linuxem a Windows. Toto sériové připojení je COM1 sériového portu virtuálního počítače a poskytuje přístup k virtuálnímu počítači a nesouvisí se síť virtuálních počítačů / provozní stav systému. Přístup ke konzole sériového portu pro virtuální počítač můžete provést jenom prostřednictvím portálu Azure portal momentálně a povolený jenom pro ty, kteří mají Přispěvatel virtuálních počítačů nebo vyšší přístup k virtuálnímu počítači. 

Pro dokumentaci ke konzole sériového portu pro virtuální počítače s Windows [kliknutím sem](../windows/serial-console.md).

> [!Note] 
> Verze Preview jsou dostupné pro vás, za předpokladu, že budete souhlasit s podmínkami použití. Další informace najdete v tématu [Microsoft Azure dodatečných podmínkách použití systémů Microsoft Azure Preview.] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Aktuálně je tato služba v **ve verzi public preview** a přístup ke konzole sériového portu pro virtuální počítače je k dispozici globálními oblastmi Azure. Konzola sériového portu v tuto chvíli není k dispozici cloudu Azure Government, Azure Germany a Azure China.


## <a name="prerequisites"></a>Požadavky 

* Musí používat model nasazení správy prostředků. Klasická nasazení nejsou podporovány. 
* Virtuální počítač musí mít [Diagnostika spouštění](boot-diagnostics.md) povoleno   ![](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)
* Účet, pomocí konzoly sériového portu, musí mít [role Přispěvatel](../../role-based-access-control/built-in-roles.md) pro virtuální počítač a [Diagnostika spouštění](boot-diagnostics.md) účtu úložiště. 
* Nastavení specifická pro distribuce Linuxu najdete v části [přístup ke konzole sériového portu pro Linux](#access-serial-console-for-linux)



## <a name="open-the-serial-console"></a>Otevřete konzoly sériového portu
Konzola sériového portu pro virtuální počítače je k dispozici pouze prostřednictvím [webu Azure portal](https://portal.azure.com). Níže je uvedený postup pro přístup ke konzole sériového portu pro virtuální počítače prostřednictvím portálu 

  1. Otevřete na webu Azure portal
  2. V nabídce vlevo vyberte virtuální počítače.
  3. Klikněte na virtuální počítač v seznamu. Otevře se stránka s přehledem pro virtuální počítač.
  4. Posuňte se dolů části Podpora a řešení potíží s části a klikněte na možnost sériová konzola (Preview). Otevře se nové podokno s sériové konzoly a spustit připojení.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)


> [!NOTE] 
> Konzola sériového portu vyžaduje místního uživatele s heslem nakonfigurovaným. V tuto chvíli virtuální počítače nakonfigurované pouze veřejný klíč SSH, nebudete mít přístup ke konzole sériového portu. Chcete-li vytvořit místní uživatele s heslem, použijte [rozšíření přístupu virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension) (k dispozici na portálu klikněte na "Resetovat heslo") a vytvořte místní uživatele s heslem.

## <a name="disable-serial-console"></a>Zakázat konzoly sériového portu
Všechna předplatná mají ve výchozím přístupem ke konzole sériového portu pro všechny virtuální počítače. Konzola sériového portu na úrovni předplatného nebo na úrovni virtuálního počítače můžete kdykoli deaktivovat.

### <a name="subscription-level-disable"></a>Zakázat úroveň předplatného
Konzola sériového portu se dají zakázat pro celé předplatné podle prostřednictvím [volání rozhraní API REST zakázat konzoly](https://aka.ms/disableserialconsoleapi). "Vyzkoušet" funkce k dispozici na stránce dokumentace k rozhraní API můžete samozřejmě využít zakázání a povolení konzoly sériového portu pro odběr. Zadejte vaše `subscriptionId`, "Výchozí" v `default` pole a klikněte na tlačítko spustit. Příkazy Azure CLI ještě nejsou k dispozici a budou doručeny později. [Zkuste volání rozhraní REST API zde](https://aka.ms/disableserialconsoleapi).

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

Alternativně můžete použít sadu příkazů níže ve službě Cloud Shell (uvedené příkazy bash) zakázání, povolení a zobrazení nezabezpečenou stav konzoly sériového portu pro odběr. 

* Pokud chcete získat zakázané konzoly sériového portu pro předplatné:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s | jq .properties
    ```
* Chcete-li zakázat konzoly sériového portu pro předplatné:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/disableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```
* Pokud chcete povolit konzoly sériového portu pro předplatné:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/enableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```

### <a name="vm-level-disable"></a>Zakázat úrovni virtuálního počítače
Konzoly sériového portu je možné zakázat konkrétní virtuální počítače tím, že zakážete nastavení diagnostiky spouštění Virtuálního počítače. Stačí vypnout nastavení diagnostiky spouštění na webu Azure Portal a konzoly sériového portu se deaktivuje pro virtuální počítač.

## <a name="serial-console-security"></a>Sériové konzoly zabezpečení 

### <a name="access-security"></a>Zabezpečení přístupu 
Přístup ke konzole sériového portu je omezená na uživatele, kteří mají [VM přispěvatelé](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) nebo vyšší než přístup k virtuálnímu počítači. Pokud váš tenant AAD vyžaduje Vícefaktorové ověřování, přístup ke konzole sériového portu bude také nutné MFA, jeho přístup je prostřednictvím [webu Azure portal](https://portal.azure.com).

### <a name="channel-security"></a>Zabezpečení kanálu
Přenosu se šifrují všechna data, která se odešle vpřed a zpět.

### <a name="audit-logs"></a>Protokoly auditu
Veškerý přístup ke konzole sériového portu je aktuálně přihlášen [Diagnostika spouštění](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) protokoly virtuálního počítače. Přístup k tyto protokoly jsou vlastněné a řídí správce virtuálních počítačů Azure.  

>[!CAUTION] 
Při přihlášení bez hesla přístup pro konzolu, pokud obsahují příkazy se spouští v rámci konzoly nebo výstup hesla, tajné kódy, uživatelská jména nebo jakoukoli jinou formou z identifikovatelné osobní údaje (PII), ty se zapíšou do Diagnostika spouštění virtuálních počítačů protokoly, spolu s všechny ostatní viditelného textu, jako součást provádění funkce scrollback konzole sériového portu. Tyto protokoly jsou cyklické a pouze uživatelé, kteří mají oprávnění ke čtení pro účet úložiště diagnostiky k nim měli přístup, ale doporučujeme osvědčený postup používání konzoly SSH pro všechno, co, která může zahrnovat tajné kódy a/nebo identifikovatelné osobní údaje. 

### <a name="concurrent-usage"></a>Souběžné používání
Pokud je uživatel připojený k sériové konzoly a jiný uživatel úspěšně požaduje přístup k tomuto virtuálnímu počítači stejný, bude první uživatel odpojen a druhý uživatel se připojil způsobem podobají první uživatel, sestavení a opuštění fyzické konzole a nový uživatel sedí.

>[!CAUTION] 
To znamená, že uživatel, který odpojí nebude odhlášeni! Schopnost Vynutit odhlášení při odpojení (prostřednictvím SIGHUP nebo mechanismus podobný) je stále v se plánuje. Pro Windows automatické časový limit v SAC povolené ale pro Linux můžete nakonfigurovat nastavení terminálu vypršení časového limitu. Stačí jednoduše přidat `export TMOUT=600` .bash_profile nebo .profile pro uživatele přihlášení v konzole, vypršení časového limitu relace po 10 minutách.

### <a name="disable-feature"></a>Zakázat funkci
Funkce konzoly sériového portu lze deaktivovat pro konkrétní virtuální počítače tím, že zakážete nastavení diagnostiky spouštění Virtuálního počítače.

## <a name="common-scenarios-for-accessing-serial-console"></a>Časté scénáře pro přístup ke konzole sériového portu 
Scénář          | Akce v konzole sériového portu                |  Použitelnost operačního systému 
:------------------|:-----------------------------------------|:------------------
Poškozený soubor FSTAB | `Enter` klíč si pokračovat a opravte soubor fstab pomocí textového editoru. Budete muset být v jednouživatelském režimu to. Zobrazit [k vyřešení potíží se souborem fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) a [pomocí konzoly sériového portu pro přístup k GRUB a Jednouživatelský režim](serial-console-grub-single-user-mode.md) začít. | Linux 
Pravidla brány firewall na nesprávný | Přístup ke konzole sériového portu a opravte iptables nebo pravidla brány firewall na Windows. | Linux/Windows 
Poškození systému souborů a vrácení | Přístup ke konzole sériového portu a obnovení systému souborů. | Linux/Windows 
Problémy s konfigurací SSH nebo RDP | Přístup ke konzole sériového portu a změňte nastavení. | Linux/Windows 
Uzamknutí sítě v systému| Konzoly sériového portu přístup prostřednictvím portálu pro správu systému. | Linux/Windows 
Interakce s zaváděcího programu pro spouštění | Přístup GRUB/BCD prostřednictvím konzoly sériového portu. Přejděte na [pomocí konzoly sériového portu pro přístup k GRUB a Jednouživatelský režim](serial-console-grub-single-user-mode.md) začít. | Linux/Windows 

## <a name="access-serial-console-for-linux"></a>Konzoly sériového portu přístup pro Linux
Aby konzoly sériového portu, aby správně fungoval hostovaného operačního systému nastavené pro čtení a zápis zpráv konzoly sériového portu. Většina [Linuxových distribucí doporučených pro Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) mají ve výchozím nastavení nakonfigurované konzoly sériového portu. Jednoduše kliknutím v části konzoly sériového portu, na webu Azure Portal bude poskytovat přístup ke konzole. 

Distribuce      | Sériový přístup ke konzole
:-----------|:---------------------
Red Hat Enterprise Linux    | Red Hat Enterprise Linux Imagí dostupných v Azure máte přístup ke konzole ve výchozím nastavení povolená. 
CentOS      | Imagí centOS dostupných v Azure máte přístup ke konzole ve výchozím nastavení povolená. 
Ubuntu      | Imagemi Ubuntu v Azure k dispozici máte přístup ke konzole ve výchozím nastavení povolená.
CoreOS      | CoreOS imagí dostupných v Azure mají přístup ke konzole ve výchozím nastavení povolená.
SUSE        | Novější imagí SLES dostupných v Azure máte přístup ke konzole ve výchozím nastavení povolená. Pokud používáte starší verze SLES (10 nebo pod) v Azure, postupujte [článku znalostní BÁZE](https://www.novell.com/support/kb/doc.php?id=3456486) umožňující konzoly sériového portu. 
Oracle Linux        | Linuxové Image Oracle v Azure k dispozici máte přístup ke konzole ve výchozím nastavení povolená.
Vlastní Linuxové Image     | Pokud chcete povolit konzoly sériového portu pro vaši vlastní image virtuálního počítače s Linuxem, povolte přístup ke konzole v /etc/inittab spouštět ttyS0 terminálu. Tady je příklad, který to přidejte do souboru inittab: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Další informace o správně vytváření vlastních imagí najdete v části [vytvoření a nahrání VHD s Linuxem v Azure](https://aka.ms/createuploadvhd).

## <a name="errors"></a>Chyby
Většina chyb jsou přechodné ze své podstaty a opakování pokusu o připojení konzoly sériového portu často tyto adresy. Následující tabulka uvádí seznam chyb a způsoby zmírnění rizik

Chyba                            |   Omezení rizik 
:---------------------------------|:--------------------------------------------|
Nepovedlo se načíst nastavení diagnostiky spouštění pro "<VMNAME>". Použití konzole sériového portu, zajistěte, že Diagnostika spouštění je povolená pro tento virtuální počítač. | Ujistěte se, že má virtuální počítač [Diagnostika spouštění](boot-diagnostics.md) povolena. 
Virtuální počítač je v zastaveném stavu Uvolněno. Spusťte virtuální počítač a pokus o připojení konzoly sériového portu. | Virtuální počítač musí být ve spuštěném stavu pro přístup ke konzole sériového portu
Nemáte požadovaná oprávnění ke konzole sériového portu tento virtuální počítač použít. Ujistěte se, máte alespoň oprávnění role Přispěvatel virtuálních počítačů.| Přístup ke konzole sériového portu vyžaduje určitá oprávnění pro přístup. Zobrazit [požadavky na přístup](#prerequisites) podrobnosti
Nepovedlo se určit skupinu prostředků pro účet úložiště diagnostiky spouštění '<STORAGEACCOUNTNAME>". Ověřte, že Diagnostika spouštění je povolená pro tento virtuální počítač a máte přístup k tomuto účtu úložiště. | Přístup ke konzole sériového portu vyžaduje určitá oprávnění pro přístup. Zobrazit [požadavky na přístup](#prerequisites) podrobnosti
Webové sokety je uzavřený nebo nelze otevřít. | Možná budete muset povolit `*.console.azure.com`. Podrobnější ale delší přístup je na seznamu povolených IP adres [rozsahy IP adres Datacentra Microsoft Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653), které mění poměrně.
## <a name="known-issues"></a>Známé problémy 
Jak jsme jsou stále ve verzi preview fázích pro přístup ke konzole sériového portu, pracujeme na tom prostřednictvím některým známým problémům. Níže je seznam těchto prvků s možná řešení:

Problém                           |   Omezení rizik 
:---------------------------------|:--------------------------------------------|
Neexistuje žádná možnost pomocí virtuálního počítače škálovací sady instance sériové konzoly |  V období preview se nepodporuje přístup ke konzole sériového portu pro instance škálovací sady virtuálních počítačů.
Dosažení zadejte po banner připojení není uveden do protokolu v řádku | Podrobnosti najdete na této stránce: [Hitting zadejte nemá žádný účinek,](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). K tomu může dojít, pokud používáte vlastní virtuální počítač, Posílená zařízení nebo konfigurace GRUB, který způsobí, že Linux selhání správně připojení do sériového portu.
Při přístupu k tomuto virtuálnímu počítači účet úložiště diagnostiky spouštění došlo k odpovědi "Zakázáno". | Zajistěte, aby že tuto diagnostiku spouštění nemá žádné brány firewall účtu. Účet úložiště diagnostiky dostupné spouštěcí je nezbytné pro konzoly sériového portu funkce.


## <a name="frequently-asked-questions"></a>Nejčastější dotazy 
**Q. Jak můžu poslat svůj názor?**

A. Poskytnout zpětnou vazbu jako problém tak, že přejdete do https://aka.ms/serialconsolefeedback. Můžete také menší (upřednostňované) pošlete zpětnou vazbu prostřednictvím azserialhelp@microsoft.com nebo v kategorii virtuálního počítače http://feedback.azure.com

**Q. Nejde mi pro přístup ke konzole sériového portu, kde můžete soubor případ podpory?**

A. Tato funkce ve verzi preview se vztahuje prostřednictvím podmínky verze Preview služby Azure. Podpora pro tuto potíže nejlépe vyřeší prostřednictvím kanálů uvedených výše. 

## <a name="next-steps"></a>Další postup
* Použití konzoly sériového portu k [spustí v GRUB a do režimu jednoho uživatele](serial-console-grub-single-user-mode.md)
* Použití konzoly sériového portu pro [NMI a SysRq volání](serial-console-nmi-sysrq.md)
* Je taky dostupná ke konzole sériového portu [Windows](../windows/serial-console.md) virtuálních počítačů
* Další informace o [Diagnostika spouštění](boot-diagnostics.md)