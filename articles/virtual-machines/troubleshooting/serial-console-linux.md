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
ms.date: 09/11/2018
ms.author: harijay
ms.openlocfilehash: ce799f4201a560077c5bb1b943a9e587a71806f2
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48856339"
---
# <a name="virtual-machine-serial-console"></a>Konzola sériového portu virtuálního počítače


Konzole sériového portu virtuálního počítače v Azure poskytuje přístup ke konzole založený na textu pro virtuální počítače s Linuxem. Toto sériové připojení je COM1 sériového portu virtuálního počítače, poskytování přístupu k virtuálnímu počítači, který je nezávislý na síti nebo stav operačního systému virtuálního počítače. Přístup ke konzole sériového portu pro virtuální počítač momentálně můžete pouze to udělat pomocí webu Azure portal a je povolená jenom pro uživatele, kteří mají Přispěvatel virtuálních počítačů nebo vyšší než přístup k virtuálnímu počítači. 

Pro dokumentaci ke konzole sériového portu pro virtuální počítače s Windows [kliknutím sem](../windows/serial-console.md).

> [!Note] 
> Konzola sériového portu u virtuálních počítačů je obecně dostupná v globálními oblastmi Azure. V tomto okamžiku konzoly sériového portu ještě není k dispozici v cloudu Azure Government nebo Azure China.


## <a name="prerequisites"></a>Požadavky 

* Musí používat model nasazení správy prostředků. Klasická nasazení nejsou podporovány. 
* Virtuální počítač musí mít [Diagnostika spouštění](boot-diagnostics.md) povoleny – snímek najdete níže.

    ![](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

* Musíte mít účet Azure, pomocí konzoly sériového portu [role Přispěvatel](../../role-based-access-control/built-in-roles.md) pro virtuální počítač a [Diagnostika spouštění](boot-diagnostics.md) účtu úložiště. 
* Virtuální počítač, u kterého jste přístup k pracovnímu konzoly sériového portu musí mít také účet založené na heslech. Můžete si ho vytvořit pomocí [resetovat heslo](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) funkce rozšíření přístupu virtuálních počítačů – snímek najdete níže.

    ![](./media/virtual-machines-serial-console/virtual-machine-serial-console-reset-password.png)

* Nastavení specifická pro distribuce Linuxu najdete v části [přístup ke konzole sériového portu pro Linux](#Serial-Console-Linux-distro-availability)



## <a name="get-started-with-serial-console"></a>Začínáme s konzoly sériového portu
Konzola sériového portu pro virtuální počítače je k dispozici pouze prostřednictvím [webu Azure portal](https://portal.azure.com). Ujistěte se, že jste splnili [požadavky](#prerequisites) výše. Níže je uvedený postup pro přístup ke konzole sériového portu pro virtuální počítače prostřednictvím portálu:

  1. Otevřete na webu Azure portal
  1. (Přeskočte to, pokud má virtuální počítač jako uživatel, který používá ověřování pomocí hesla) Kliknutím na okno "Resetovat heslo" Přidání uživatele s ověřováním
  1. V nabídce vlevo vyberte virtuální počítače.
  1. Klikněte na virtuální počítač v seznamu. Otevře se stránka s přehledem pro virtuální počítač.
  1. Posuňte se dolů části Podpora a řešení potíží s části a klikněte na možnost "Konzoly sériového portu". Otevře se nové podokno s sériové konzoly a spustit připojení.

![](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### 

> [!NOTE] 
> Konzola sériového portu vyžaduje místního uživatele s heslem nakonfigurovaným. V tuto chvíli nebudou moct přihlásit ke konzole sériového portu virtuální počítače nakonfigurované pouze veřejný klíč SSH. Chcete-li vytvořit místní uživatele s heslem, použijte [rozšíření přístupu virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), která je dostupná na portálu klikněte na "Resetovat heslo" na portálu a vytvořte místní uživatele s heslem.
> Můžou také resetovat heslo správce v účtu podle [pomocí GRUB vyřadit do režimu jednoho uživatele](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distro-availability"></a>Sériový dostupnosti distribuce Linuxu konzoly
Aby konzoly sériového portu, aby správně fungoval hostovaného operačního systému nastavené pro čtení a zápis zpráv konzoly sériového portu. Většina [Linuxových distribucí doporučených pro Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) mají ve výchozím nastavení nakonfigurované konzoly sériového portu. Jednoduše kliknutím v části konzoly sériového portu, na webu Azure Portal bude poskytovat přístup ke konzole. 

Distribuce      | Sériový přístup ke konzole
:-----------|:---------------------
Red Hat Enterprise Linux    | Red Hat Enterprise Linux Imagí dostupných v Azure máte přístup ke konzole ve výchozím nastavení povolená. 
CentOS      | Imagí centOS dostupných v Azure máte přístup ke konzole ve výchozím nastavení povolená. 
Ubuntu      | Imagemi Ubuntu v Azure k dispozici máte přístup ke konzole ve výchozím nastavení povolená.
CoreOS      | CoreOS imagí dostupných v Azure mají přístup ke konzole ve výchozím nastavení povolená.
SUSE        | Novější imagí SLES dostupných v Azure máte přístup ke konzole ve výchozím nastavení povolená. Pokud používáte starší verze SLES (10 nebo pod) v Azure, postupujte [článku znalostní BÁZE](https://www.novell.com/support/kb/doc.php?id=3456486) umožňující konzoly sériového portu. 
Oracle Linux        | Linuxové Image Oracle v Azure k dispozici máte přístup ke konzole ve výchozím nastavení povolená.
Vlastní Linuxové Image     | Pokud chcete povolit konzoly sériového portu pro vaši vlastní image virtuálního počítače s Linuxem, povolte přístup ke konzole v `/etc/inittab` ke spuštění v terminálu `ttyS0`. Tady je příklad, který to přidejte do souboru inittab: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Další informace o správně vytváření vlastních imagí najdete v části [vytvoření a nahrání VHD s Linuxem v Azure](https://aka.ms/createuploadvhd). Pokud vytváříte vlastní jádra, jsou některé jádra příznaky, které byste měli zvážit povolení `CONFIG_SERIAL_8250=y` a `CONFIG_MAGIC_SYSRQ_SERIAL=y`. Konfigurační soubor je často přidávaném /boot/ pro další zkoumání.

## <a name="common-scenarios-for-accessing-serial-console"></a>Časté scénáře pro přístup ke konzole sériového portu 
Scénář          | Akce v konzole sériového portu                
:------------------|:-----------------------------------------
Poškozený soubor FSTAB | `Enter` klíč si pokračovat a opravte soubor fstab pomocí textového editoru. Budete muset být v jednouživatelském režimu to. Zobrazit [k vyřešení potíží se souborem fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) a [pomocí konzoly sériového portu pro přístup k GRUB a Jednouživatelský režim](serial-console-grub-single-user-mode.md) začít.
Pravidla brány firewall na nesprávný | Přístup ke konzole sériového portu a iptables vyřešit. 
Poškození systému souborů a vrácení | Přístup ke konzole sériového portu a obnovení systému souborů. 
Problémy s konfigurací SSH nebo RDP | Přístup ke konzole sériového portu a změňte nastavení. 
Uzamknutí sítě v systému| Konzoly sériového portu přístup prostřednictvím portálu pro správu systému. 
Interakce s zaváděcího programu pro spouštění | GRUB přístup prostřednictvím konzoly sériového portu. Přejděte na [pomocí konzoly sériového portu pro přístup k GRUB a Jednouživatelský režim](serial-console-grub-single-user-mode.md) začít. 

## <a name="disable-serial-console"></a>Zakázat konzoly sériového portu
Všechna předplatná mají ve výchozím přístupem ke konzole sériového portu pro všechny virtuální počítače. Konzola sériového portu na úrovni předplatného nebo na úrovni virtuálního počítače můžete kdykoli deaktivovat.

> [!Note] 
> Pokud chcete povolit nebo zakázat konzoly sériového portu k předplatnému, musíte mít oprávnění k zápisu do předplatného. To zahrnuje, ale není omezena pouze na role správce nebo vlastníka. Vlastní role může mít také oprávnění k zápisu.

### <a name="subscription-level-disable"></a>Zakázat úroveň předplatného
Konzola sériového portu se dají zakázat pro celé předplatné podle prostřednictvím [volání rozhraní API REST zakázat konzoly](https://aka.ms/disableserialconsoleapi). "Vyzkoušet" funkce k dispozici na stránce dokumentace k rozhraní API můžete samozřejmě využít zakázání a povolení konzoly sériového portu pro odběr. Zadejte vaše `subscriptionId`, "Výchozí" v `default` pole a klikněte na tlačítko spustit. Příkazy Azure CLI ještě nejsou k dispozici a budou doručeny později. [Zkuste volání rozhraní REST API zde](https://aka.ms/disableserialconsoleapi).

![](./media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

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

## <a name="accessibility"></a>Přístupnost
Klíče se pro Azure konzoly sériového portu se usnadnění přístupu. Za tímto účelem jsme zajistíte, že je přístupný pro ty, které mají vizuál a poškozením sluchu, jakož i uživatelů, kteří nebudou moct používat myš konzole sériového portu.

### <a name="keyboard-navigation"></a>Procházení pomocí klávesnice
Použití `tab` kláves na klávesnici pro navigaci v konzole sériového portu rozhraní na webu Azure portal. Vaše poloha budou zvýrazněny na obrazovce. Pokud chcete nechat fokus z okna konzoly sériového portu, stiskněte klávesu `Ctrl + F6` na klávesnici.

### <a name="use-serial-console-with-a-screen-reader"></a>Použití konzoly sériového portu se čtečkou obrazovky
Konzola sériového portu se dodává s integrované podpoře čtečky obrazovky. Navigace pomocí čtečky obrazovky zapnuté vám umožní alternativní text pro aktuálně vybrané tlačítko nahlas číst čtečka obrazovky.

## <a name="errors"></a>Chyby
Většina chyb jsou přechodné ze své podstaty a opakování pokusu o připojení konzoly sériového portu často tyto adresy. Následující tabulka uvádí seznam chyb a způsoby zmírnění rizik

Chyba                            |   Omezení rizik 
:---------------------------------|:--------------------------------------------|
Nepovedlo se načíst nastavení diagnostiky spouštění pro "<VMNAME>". Použití konzole sériového portu, zajistěte, že Diagnostika spouštění je povolená pro tento virtuální počítač. | Ujistěte se, že má virtuální počítač [Diagnostika spouštění](boot-diagnostics.md) povolena. 
Virtuální počítač je v zastaveném stavu Uvolněno. Spusťte virtuální počítač a pokus o připojení konzoly sériového portu. | Virtuální počítač musí být ve spuštěném stavu pro přístup ke konzole sériového portu
Nemáte požadovaná oprávnění ke konzole sériového portu tento virtuální počítač použít. Ujistěte se, máte alespoň oprávnění role Přispěvatel virtuálních počítačů.| Přístup ke konzole sériového portu vyžaduje určitá oprávnění pro přístup. Zobrazit [požadavky na přístup](#prerequisites) podrobnosti
Nepovedlo se určit skupinu prostředků pro účet úložiště diagnostiky spouštění '<STORAGEACCOUNTNAME>". Ověřte, že Diagnostika spouštění je povolená pro tento virtuální počítač a máte přístup k tomuto účtu úložiště. | Přístup ke konzole sériového portu vyžaduje určitá oprávnění pro přístup. Zobrazit [požadavky na přístup](#prerequisites) podrobnosti
Webové sokety je uzavřený nebo nelze otevřít. | Možná budete muset povolit `*.console.azure.com`. Podrobnější ale delší přístup je na seznamu povolených IP adres [rozsahy IP adres Datacentra Microsoft Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653), které mění poměrně.
Při přístupu k tomuto virtuálnímu počítači účet úložiště diagnostiky spouštění došlo k odpovědi "Zakázáno". | Zajistěte, aby že tuto diagnostiku spouštění nemá žádné brány firewall účtu. Účet úložiště diagnostiky dostupné spouštěcí je nezbytné pro konzoly sériového portu funkce.

## <a name="known-issues"></a>Známé problémy 
Víme o některé problémy s konzole sériového portu. Tady je seznam těchto problémů a kroky pro omezení rizik.

Problém                           |   Omezení rizik 
:---------------------------------|:--------------------------------------------|
Dosažení zadejte po banner připojení není uveden do protokolu v řádku | Podrobnosti najdete na této stránce: [Hitting zadejte nemá žádný účinek,](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). K tomu může dojít, pokud používáte vlastní virtuální počítač, Posílená zařízení nebo konfigurace GRUB, který způsobí, že Linux selhání správně připojení do sériového portu.
Text konzoly sériového portu potrvá jenom část na velikost obrazovky (často po pomocí textového editoru) | Konzoly sériového portu nepodporují vyjednávání o velikost okna ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)), což znamená, že bude bez signálu SIGWINCH odesílat aktualizace velikosti obrazovky a virtuální počítač bude nemají žádné informace o velikosti svého terminálu. Doporučujeme, abyste instaling xterm nebo některé podobné nástroj, který obsahuje příkaz "Změna velikosti". Spuštění "Změna velikosti" opravu provedete.
Vkládání velmi dlouhých řetězců nefunguje | Konzola sériového portu omezení délky řetězce do terminálu na 2 048 znaků. Toto je zabránit zahlcení šířky pásma sériového portu.


## <a name="frequently-asked-questions"></a>Nejčastější dotazy 
**Q. Jak můžu poslat svůj názor?**

A. Poskytnout zpětnou vazbu jako problém tak, že přejdete do https://aka.ms/serialconsolefeedback. Můžete také (menší upřednostňované), odeslat zpětnou vazbu prostřednictvím azserialhelp@microsoft.com nebo v kategorii virtuálního počítače http://feedback.azure.com

**Q. Podporuje konzoly sériového portu, kopírování a vkládání?**

A. Ano, co dělá. Zkopírujte a vložte do terminálu pomocí kombinace kláves Ctrl + Shift + C a Ctrl + Shift + V.

**Q. Můžete použít konzoly sériového portu místo připojení SSH?**

A. Když to může zdát, že je to technicky možné, konzoly sériového portu je určena pro použití především jako nástroje pro odstraňování potíží v situacích, kdy není možné připojení pomocí protokolu SSH. Nedoporučujeme použití konzoly sériového portu jako náhrady SSH dvou důvodů:

1. Konzola sériového portu nemá tak velkou šířku pásma jako SSH - je připojení pouze text, takže další interakce náročná na výkon grafické uživatelské rozhraní bude obtížné v konzole sériového portu.
1. Přístup ke konzole sériového portu je aktuálně pouze uživatelské jméno a heslo. Klíče SSH jsou mnohem bezpečnější než kombinace uživatelského jména a hesla, tak z hlediska zabezpečení přihlášení doporučujeme prostřednictvím konzoly sériového portu SSH.

**Q. Kdo může povolit nebo zakázat konzoly sériového portu pro Moje předplatné?**

A. Pokud chcete povolit nebo zakázat konzoly sériového portu na úrovni celé předplatné, musí mít oprávnění k zápisu do předplatného. Role, které mají oprávnění k zápisu patří, ale nejsou omezeny rolí správce nebo vlastníka. Vlastní role může mít také oprávnění k zápisu.

**Q. Kdo má přístup k konzoly sériového portu pro virtuální počítač?**

A. Musíte mít přístup úrovně Přispěvatel nebo vyšší do virtuálního počítače pro přístup ke konzole sériového portu Virtuálního počítače. 

**Q. Moje konzoly sériového portu se nezobrazuje nic, co mám dělat?**

A. Vaše image je pravděpodobně nesprávně nakonfigurované pro přístup ke konzole sériového portu. Zobrazit [konzoly sériového portu přístup pro Linux](#Access-Serial-Console-for-Linux) podrobné informace o konfiguraci bitové kopie umožňují konzoly sériového portu.

**Q. Je k dispozici konzoly sériového portu pro Virtual Machine Scale Sets?**

A. V současné době se nepodporuje přístup ke konzole sériového portu pro instance škálovací sady virtuálních počítačů.

**Q. Nastavit virtuální počítač pomocí jenom ověřování pomocí klíče SSH, můžu dál používat konzoly sériového portu pro připojení k virtuálnímu počítači?** Odpověď: Ano. Konzola sériového portu nevyžaduje klíče SSH, takže všechno, co musíte udělat zajišťuje kombinace uživatelského jména a hesla. To lze provést pomocí okna "Resetovat heslo" na portálu a pomocí těchto přihlašovacích údajů pro přihlášení ke konzole sériového portu.

## <a name="next-steps"></a>Další postup
* Použití konzoly sériového portu k [spustí v GRUB a do režimu jednoho uživatele](serial-console-grub-single-user-mode.md)
* Použití konzoly sériového portu pro [NMI a SysRq volání](serial-console-nmi-sysrq.md)
* Je taky dostupná ke konzole sériového portu [Windows](../windows/serial-console.md) virtuálních počítačů
* Další informace o [Diagnostika spouštění](boot-diagnostics.md)