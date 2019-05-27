---
title: Azure konzoly sériového portu pro Linux | Dokumentace Microsoftu
description: Obousměrné sériové konzoly pro virtuální počítače Azure a Škálovací sady virtuálních počítačů.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: fe08569937dc29ecbc66da1cb2c431cca11a8580
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65835106"
---
# <a name="azure-serial-console-for-linux"></a>Azure konzoly sériového portu pro Linux

Konzole sériového portu na webu Azure Portal poskytuje přístup ke konzole založený na textu pro Linux virtual machines (VM) a instance virtuálního počítače škálovací sadě. Toto sériové připojení připojí k COM1 sériového portu virtuálního počítače nebo instanci virtuálního počítače škálovací sady, díky přístupu k němu nezávisle na stav sítě nebo operačního systému. Konzole sériového portu je přístupný pouze pomocí webu Azure portal a je povolený jenom pro uživatele, kteří mají roli přístup Přispěvatel nebo vyšší do škálovací sady virtuálního počítače nebo virtuálního počítače.

Konzola sériového portu funguje stejným způsobem pro virtuální počítače a škálovací sady virtuálních počítačů instancí. V tomto dokumentu bude všech zmínky k virtuálním počítačům implicitně obsahovat instancí škálovací sady virtuálních počítačů, pokud není uvedeno jinak.

Konzola sériového portu dokumentaci pro Windows najdete v tématu [sériové konzoly pro Windows](../windows/serial-console.md).

> [!NOTE]
> Konzole sériového portu je obecně dostupná v globálními oblastmi Azure. Zatím není k dispozici v Azure government nebo Azure China cloudy.


## <a name="prerequisites"></a>Požadavky

- Váš virtuální počítač nebo virtuální počítač instanci škálovací sady používaly model nasazení správy prostředků. Klasická nasazení nejsou podporovány.

- Musí mít váš účet, který používá konzoly sériového portu [role Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) pro virtuální počítač a [Diagnostika spouštění](boot-diagnostics.md) účtu úložiště

- Váš virtuální počítač nebo virtuální počítač instance škálovací sadě musí mít uživatel založené na heslech. Můžete si ho vytvořit pomocí [resetovat heslo](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) funkce rozšíření přístupu virtuálních počítačů. Vyberte **resetovat heslo** z **podpora a řešení potíží** oddílu.

- Musíte mít instanci vašeho virtuálního počítače nebo virtuálního počítače sady škálování [Diagnostika spouštění](boot-diagnostics.md) povolena.

    ![Nastavení diagnostiky spouštění](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Nastavení specifická pro Linuxové distribuce, naleznete v tématu [konzoly sériového portu dostupnost distribuce Linuxu](#serial-console-linux-distribution-availability).



## <a name="get-started-with-the-serial-console"></a>Začínáme s konzole sériového portu
Konzole sériového portu pro virtuální počítače a škálovací sady virtuálních počítačů je přístupný pouze prostřednictvím webu Azure portal:

### <a name="serial-console-for-virtual-machines"></a>Konzola sériového portu pro virtuální počítače
Konzola sériového portu pro virtuální počítače je stejně jednoduché jako při kliknutí na **konzoly sériového portu** v rámci **podpora a řešení potíží** části webu Azure Portal.
  1. Otevřete web [Azure Portal](https://portal.azure.com).

  1. Přejděte do **všechny prostředky** a vyberte virtuální počítač. Otevře se stránka s přehledem pro virtuální počítač.

  1. Přejděte dolů k položce **podpora a řešení potíží** a vyberte **konzoly sériového portu**. Nové podokno v konzole sériového portu se otevře a aktivuje připojení.

     ![Okno konzoly sériového portu v Linuxu](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Konzola sériového portu pro Škálovací sady virtuálních počítačů
Konzola sériového portu je k dispozici na základě jednotlivé instance pro škálovací sady virtuálních počítačů. Budete muset přejít na jednotlivé instance škálovací sady virtuálních počítačů, než **konzoly sériového portu** tlačítko. Pokud vaše škálovací sada virtuálních počítačů nemá povolenou diagnostikou spuštění, zajistěte, aby že aktualizace vašeho modelu škálovací sady virtuálního počítače povolte diagnostiku spouštění, a pak upgradovat všechny instance na nový model pro přístup ke konzole sériového portu.
  1. Otevřete web [Azure Portal](https://portal.azure.com).

  1. Přejděte do **všechny prostředky** a vyberte Škálovací sady virtuálních počítačů. Stránka s přehledem pro škálování virtuálního počítače nastavte otevře.

  1. Přejděte na **instancí**

  1. Vyberte instanci virtuálního počítače škálovací sady

  1. Z **podpora a řešení potíží** vyberte **konzoly sériového portu**. Nové podokno v konzole sériového portu se otevře a aktivuje připojení.

     ![Linux škálovací sady virtuálních počítačů konzoly sériového portu](./media/virtual-machines-serial-console/vmss-start-console.gif)


> [!NOTE]
> Konzole sériového portu vyžaduje místního uživatele s nakonfigurovaným heslem. Virtuální počítače nebo škálovací sady virtuálních počítačů nakonfigurovanou pouze veřejný klíč SSH, nebudou moct přihlásit ke konzole sériového portu. K vytvoření místního uživatele s heslem, použijte [rozšíření VMAccess](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), která je k dispozici na portálu tak, že vyberete **resetovat heslo** na webu Azure Portal a vytvořte místní uživatele s heslem.
> Můžete také resetovat heslo správce v účtu podle [pomocí GRUB ke spuštění do režimu jednoho uživatele](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distribution-availability"></a>Sériové konzoly Linux distribuční dostupnosti
Konzole sériového portu správně fungovala musí být hostovaný operační systém nakonfigurované pro čtení a zápis zpráv konzoly sériového portu. Většina [distribucí Linuxu schválených pro Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) mají ve výchozím nastavení nakonfigurované konzoly sériového portu. Výběr **konzoly sériového portu** v **podpora a řešení potíží** části webu Azure portal poskytuje přístup ke konzole sériového portu.

Distribuce      | Přístup k sériové konzole
:-----------|:---------------------
Red Hat Enterprise Linux    | Přístup ke konzole sériového portu ve výchozím nastavení povolená.
CentOS      | Přístup ke konzole sériového portu ve výchozím nastavení povolená.
Ubuntu      | Přístup ke konzole sériového portu ve výchozím nastavení povolená.
CoreOS      | Přístup ke konzole sériového portu ve výchozím nastavení povolená.
SUSE        | Novější imagí SLES dostupných v Azure měli konzoly sériového portu ve výchozím nastavení povolená. Pokud používáte starší verze SLES (10 nebo starší) v Azure, najdete v článku [článku znalostní BÁZE](https://www.novell.com/support/kb/doc.php?id=3456486) umožňující konzoly sériového portu.
Oracle Linux        | Přístup ke konzole sériového portu ve výchozím nastavení povolená.
Vlastní Linuxové Image     | Pokud chcete povolit konzole sériového portu pro vaši vlastní image virtuálního počítače s Linuxem, povolte přístup ke konzole v souboru */etc/inittab* ke spuštění v terminálu `ttyS0`. Například: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Další informace o správně vytváření vlastních imagí najdete v tématu [vytvoření a nahrání VHD s Linuxem v Azure](https://aka.ms/createuploadvhd). Pokud vytváříte vlastní jádra, zvažte povolení tyto příznaky jádra: `CONFIG_SERIAL_8250=y` a `CONFIG_MAGIC_SYSRQ_SERIAL=y`. Konfigurační soubor se obvykle nachází v */boot/* cestu.

> [!NOTE]
> Pokud se nezobrazují v konzole sériového portu nic, ujistěte se, že Diagnostika spouštění je povolená na virtuálním počítači. Dosažení **Enter** se často opravit problémy kde nic, zobrazují v konzole sériového portu.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Časté scénáře pro přístup ke konzole sériového portu

Scénář          | Akce v konzole sériového portu
:------------------|:-----------------------------------------
Nefunkční *FSTAB* souboru | Stisknutím klávesy **Enter** klíč pomocí textového editoru opravit a pokračovat *FSTAB* souboru. Musíte může být v jednouživatelském režimu Uděláte to tak. Další informace najdete v části konzoly sériového portu v [k vyřešení potíží se souborem fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) a [konzoly sériového portu používá pro přístup k GRUB a režimu jednoho uživatele](serial-console-grub-single-user-mode.md).
Pravidla brány firewall na nesprávný |  Pokud jste nakonfigurovali iptables blokovat připojení SSH, můžete k interakci s virtuálním Počítačem bez nutnosti SSH konzoly sériového portu. Další podrobnosti najdete v [iptables člověk stránky](https://linux.die.net/man/8/iptables).<br>Podobně pokud vaše firewalld blokuje přístup přes SSH, můžete přístup k virtuálnímu počítači prostřednictvím konzoly sériového portu a překonfigurujte firewalld. Další podrobnosti najdete v [firewalld dokumentaci](https://firewalld.org/documentation/).
Poškození systému souborů a vrácení | Podrobnosti najdete v části konzoly sériového portu [Linuxový virtuální počítač Azure nejde spustit kvůli chybám systémových souborů](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) další podrobnosti o řešení potíží s poškozený soubor systémů pomocí konzoly sériového portu.
Problémy s konfigurací SSH | Přístup ke konzole sériového portu a změnit nastavení. Konzola sériového portu je možné bez ohledu na to se konfigurace SSH z virtuálního počítače nevyžaduje virtuální počítač má síťové připojení k práci. Průvodce odstraňováním potíží je k dispozici na [řešení potíží s připojení SSH k virtuálnímu počítači Azure Linux, který selže, chyby, nebo bylo odmítnuto](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection). Další podrobnosti najdete na adrese [podrobné SSH řešení potíží pro problémy s připojením k virtuálnímu počítači s Linuxem v Azure](./detailed-troubleshoot-ssh-connection.md)
Interakce s zaváděcího programu pro spouštění | Restartujte virtuální počítač z v rámci okna konzoly sériového portu pro přístup k GRUB na virtuální počítač s Linuxem. Další podrobnosti a informace specifické pro distribuce, naleznete v tématu [konzoly sériového portu používá pro přístup k GRUB a režimu jednoho uživatele](serial-console-grub-single-user-mode.md).

## <a name="disable-the-serial-console"></a>Zakázat konzole sériového portu
Všechna předplatná mají ve výchozím přístupem ke konzole sériového portu. Můžete zakázat konzole sériového portu na úrovni předplatného nebo na virtuální počítač nebo virtuální počítače škálovací sady úroveň. Všimněte si, že Diagnostika spouštění, musí být povolené na virtuálním počítači v konzole sériového portu, aby pro práci.

### <a name="vmvirtual-machine-scale-set-level-disable"></a>Zakázat úrovni sady škálování virtuálního počítače nebo virtuální počítač
Konzole sériového portu je možné zakázat pro konkrétní virtuální počítač nebo virtuální počítač škálovací sady s tím, že zakážete nastavení diagnostiky spouštění. Vypnutí diagnostiky spouštění na webu Azure Portal se zakázat konzole sériového portu pro virtuální počítač nebo škálovací sady virtuálních počítačů. Pokud používáte konzoly sériového portu na škálovací sadu virtuálních počítačů, zajistěte, aby že vaše instance škálovací sady virtuálních počítačů upgradujete na nejnovější model.

> [!NOTE]
> K povolení nebo zakázání konzole sériového portu k předplatnému, musíte mít oprávnění k zápisu do předplatného. Tato oprávnění zahrnují role správce nebo vlastníka. Vlastní role můžete také mít oprávnění k zápisu.

### <a name="subscription-level-disable"></a>Zakázat úroveň předplatného
Konzole sériového portu se dají zakázat pro celé předplatné prostřednictvím [volání rozhraní API REST zakázat konzoly](/rest/api/serialconsole/console/disableconsole). Můžete použít **vyzkoušet** funkce, které jsou k dispozici na této stránce dokumentace k rozhraní API zakázání a povolení konzole sériového portu pro odběr. Zadejte ID svého předplatného pro **subscriptionId**, zadejte **výchozí** pro **výchozí**a pak vyberte **spustit**. Příkazy Azure CLI ještě nejsou k dispozici.

![Vyzkoušet rozhraní REST API](./media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

Alternativně můžete použít následující sady příkazů prostředí bash ve službě Cloud Shell k zakázání, povolení a zobrazení zakázané konzole sériového portu pro předplatné:

* Pokud chcete získat zakázané konzole sériového portu pro předplatné:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s | jq .properties
    ```
* Chcete-li zakázat konzole sériového portu pro předplatné:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/disableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```
* Pokud chcete povolit konzole sériového portu pro předplatné:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/enableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```

## <a name="serial-console-security"></a>Zabezpečení konzoly sériového portu

### <a name="access-security"></a>Zabezpečení přístupu
Přístup ke konzole sériového portu je omezená na uživatele, kteří mají roli přístup z [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) nebo vyšší, k virtuálnímu počítači. Pokud váš tenant Azure Active Directory vyžaduje vícefaktorové ověřování (MFA), je přístup ke konzole sériového portu bude také nutné vícefaktorové ověřování, protože přístup ke konzole sériového portu, je prostřednictvím [webu Azure portal](https://portal.azure.com).

### <a name="channel-security"></a>Zabezpečení kanálu
Přenosu se šifrují všechna data, která se odešle vpřed a zpět.

### <a name="audit-logs"></a>Protokoly auditu
Veškerý přístup ke konzole sériového portu je aktuálně přihlášen [Diagnostika spouštění](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) protokoly virtuálního počítače. Přístup k tyto protokoly jsou vlastněné a řídí správce virtuálních počítačů Azure.

> [!CAUTION]
> Žádná hesla přístup pro konzolu jsou protokolovány. Nicméně pokud příkazy se spouští v rámci konzoly obsahovat nebo výstup hesla, tajné kódy, uživatelská jména nebo jakoukoli jinou formu identifikovatelné osobní údaje (PII), ty se zapíšou do protokolů diagnostiky spouštění virtuálního počítače. Se zapíšou spolu s všechny ostatní viditelného textu, jako součást provádění konzoly sériového portu přejděte zpět funkce. Tyto protokoly jsou cyklické a přístup k nim mají pouze uživatelé, kteří mají oprávnění ke čtení pro účet úložiště diagnostiky. Však doporučujeme osvědčený postup pomocí vzdálené plochy pro všechno, co, která může zahrnovat tajné kódy a/nebo identifikovatelné osobní údaje.

### <a name="concurrent-usage"></a>Souběžné používání
Pokud je uživatel připojen ke konzole sériového portu a jiný uživatel úspěšně požaduje přístup k tomuto virtuálnímu počítači stejný, bude první uživatel odpojen a druhý uživatel se připojil do stejné relace.

> [!CAUTION]
> To znamená, že uživatel, který je odpojen nebude odhlášeni. Schopnost Vynutit odhlášení při odpojení (pomocí SIGHUP nebo mechanismus podobný) je stále v se plánuje. Pro Windows se automatické vypršení časového limitu povolené ve speciální správy konzoly (SAC); ale pro Linux můžete nakonfigurovat nastavení terminálu vypršení časového limitu. Chcete-li to provést, přidejte `export TMOUT=600` ve vaší *.bash_profile* nebo *.profile* souboru pro uživatele, který používáte k přihlášení do konzoly. Toto nastavení vyprší časový limit relace po 10 minutách.

## <a name="accessibility"></a>Usnadnění
Klíče se pro sériové konzoly Azure se pro usnadnění. Za tímto účelem jsme zajistili, že je plně přístupné konzole sériového portu.

### <a name="keyboard-navigation"></a>Procházení pomocí klávesnice
Použití **kartu** kláves na klávesnici a přejděte v konzole sériového portu rozhraní na webu Azure Portal. Vaše poloha budou zvýrazněny na obrazovce. Pokud chcete nechat fokus z okna konzoly sériového portu, stiskněte klávesu **Ctrl**+**F6** na klávesnici.

### <a name="use-serial-console-with-a-screen-reader"></a>Použití konzoly sériového portu se čtečkou obrazovky
Konzole sériového portu je integrované podpoře čtečky obrazovky. Navigace pomocí čtečky obrazovky zapnuté vám umožní alternativní text pro aktuálně vybrané tlačítko nahlas číst čtečka obrazovky.

## <a name="errors"></a>Chyby
Protože většina chyb jsou přechodné, opakování pokusu o připojení je často opravit. V následující tabulce najdete seznam chyb a způsoby zmírnění rizik. Tyto chyby a jejich zmírnění platí pro oba virtuální počítače a škálovací sady virtuálních počítačů instancí.

Chyba                            |   Omezení rizik
:---------------------------------|:--------------------------------------------|
Nepovedlo se načíst nastavení diagnostiky spouštění pro  *&lt;VMNAME&gt;*. Použití konzole sériového portu, zajistěte, že Diagnostika spouštění je povolená pro tento virtuální počítač. | Ujistěte se, že má virtuální počítač [Diagnostika spouštění](boot-diagnostics.md) povolena.
Virtuální počítač je v zastaveném stavu Uvolněno. Spusťte virtuální počítač a pokus o připojení konzoly sériového portu. | Virtuální počítač musí být ve spuštěném stavu pro přístup ke konzole sériového portu.
Nemáte požadovaná oprávnění pro tento virtuální počítač pomocí konzoly sériového portu. Ujistěte se, máte alespoň oprávnění role Přispěvatel virtuálních počítačů.| Konzola sériového portu přístup vyžaduje určitá oprávnění. Další informace najdete v tématu [požadavky](#prerequisites).
Nepovedlo se určit skupinu prostředků pro účet úložiště diagnostiky spouštění  *&lt;STORAGEACCOUNTNAME&gt;*. Ověřte, že Diagnostika spouštění je povolená pro tento virtuální počítač a máte přístup k tomuto účtu úložiště. | Konzola sériového portu přístup vyžaduje určitá oprávnění. Další informace najdete v tématu [požadavky](#prerequisites).
Webové sokety je uzavřený nebo nelze otevřít. | Možná budete muset povolit `*.console.azure.com`. Podrobnější ale delší přístup je na seznamu povolených IP adres [rozsahy IP adres Datacentra Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653), které mění poměrně.
Při přístupu k tomuto virtuálnímu počítači účet úložiště diagnostiky spouštění došlo k odpovědi "Zakázáno". | Zajistěte, aby že tuto diagnostiku spouštění nemá firewall k účtu. Účet úložiště diagnostiky dostupné spouštěcí je nezbytné pro konzole sériového portu na funkci.

## <a name="known-issues"></a>Známé problémy
Jsme si vědomi některé problémy s konzole sériového portu. Tady je seznam těchto problémů a kroky pro omezení rizik. Tyto problémy a jejich zmírnění platí pro oba virtuální počítače a škálovací sady virtuálních počítačů instancí.

Problém                           |   Omezení rizik
:---------------------------------|:--------------------------------------------|
Stisknutím klávesy **Enter** po připojení banner nezpůsobí výzvě přihlášení má být zobrazen. | Další informace najdete v tématu [Hitting zadejte nemá žádný účinek,](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Tomuto problému může dojít, pokud používáte vlastní virtuální počítač, Posílená zařízení nebo konfigurace GRUB, který způsobí, že Linux se nejde připojit k sériového portu.
Text konzoly sériového portu zabere jenom část na velikost obrazovky (často po pomocí textového editoru). | Konzoly sériového portu nepodporují vyjednávání o velikost okna ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)), což znamená, že bude bez signálu SIGWINCH odesílat aktualizace velikosti obrazovky a virtuální počítač bude nemají žádné informace o velikosti svého terminálu. Nainstalovat xterm nebo podobného nástroje, abyste měli `resize` příkaz a poté spusťte `resize`.
Vkládání dlouhé řetězce nebude fungovat. | Konzole sériového portu omezení délky řetězce do terminálu na 2 048 znaků, aby se zabránilo přetížení šířky pásma sériového portu.
Konzola sériového portu nefunguje s bránou firewall účtu úložiště. | Konzoly sériového portu záměrné nemůže pracovat s branami firewall účet úložiště na účet úložiště diagnostiky spouštění povolené.


## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Q. Jak můžu poslat svůj názor?**

A. Poskytnout zpětnou vazbu tak, že vytvoříte problém Githubu v https://aka.ms/serialconsolefeedback. Můžete také (méně upřednostňované), můžete odeslat zpětnou vazbu prostřednictvím azserialhelp@microsoft.com nebo v kategorii virtuální počítač https://feedback.azure.com.

**Q. Podporuje konzole sériového portu, kopírování a vkládání?**

A. Ano. Použití **Ctrl**+**Shift**+**C** a **Ctrl**+**Shift** + **V** zkopírovat a vložit do terminálu.

**Q. Můžete použít konzoly sériového portu místo připojení SSH?**

A. Při použití těchto se může zdát technicky možné, konzole sériového portu je určena pro použití především jako nástroje pro odstraňování potíží v situacích, kdy není možné připojení pomocí protokolu SSH. Doporučujeme, abyste před použitím konzole sériového portu jako náhrady SSH z následujících důvodů:

- Konzole sériového portu nemá tak velkou šířku pásma jako SSH. Protože se jedná textovém připojení, další interakce náročná na výkon grafickým uživatelským rozhraním je obtížné.
- Přístup ke konzole sériového portu je aktuálně možné pouze pomocí uživatelského jména a hesla. Vzhledem k tomu, že klíče SSH jsou mnohem bezpečnější než kombinace uživatelského jména a hesla z pohledu zabezpečení přihlášení, doporučujeme prostřednictvím konzoly sériového portu SSH.

**Q. Kdo může povolit nebo zakázat konzoly sériového portu pro Moje předplatné?**

A. K povolení nebo zakázání konzole sériového portu na úrovni celé předplatné, musí mít oprávnění k zápisu do předplatného. Role, které mají oprávnění k zápisu zahrnují role správce nebo vlastníka. Vlastní role můžete také mít oprávnění k zápisu.

**Q. Kdo má přístup k konzole sériového portu pro mé škálovací sady virtuálního počítače nebo virtuální počítač?**

A. Musíte mít roli Přispěvatel virtuálních počítačů nebo vyšší pro virtuální počítač nebo virtuální počítač škálovací sadu pro přístup ke konzole sériového portu.

**Q. Moje konzoly sériového portu se nezobrazuje nic, co mám dělat?**

A. Vaše image je pravděpodobně nesprávně nakonfigurované pro přístup ke konzole sériového portu. Informace o konfiguraci bitové kopie umožňují konzole sériového portu najdete v tématu [konzoly sériového portu dostupnost distribuce Linuxu](#serial-console-linux-distribution-availability).

**Q. Konzole sériového portu je dostupný pro škálovací sady virtuálních počítačů?**

A. Ano, je! Zobrazit [konzoly sériového portu pro Škálovací sady virtuálních počítačů](#serial-console-for-virtual-machine-scale-sets)

**Q. Je-li nastavit tento virtuální počítač nebo virtuální počítač škálovací sady s použitím pouze ověřování pomocí klíče SSH, můžu dál používat konzole sériového portu pro připojení k Můj virtuální počítač nebo virtuální instanci počítače škálovací sady?**

A. Ano. Protože konzole sériového portu nevyžaduje klíče SSH, stačí nastavit kombinace uživatelského jména a hesla. Můžete tak učinit tak, že vyberete **resetovat heslo** webu Azure portal a pomocí těchto přihlašovacích údajů pro přihlášení ke konzole sériového portu.

## <a name="next-steps"></a>Další postup
* Použití konzole sériového portu [přístup GRUB a režimu jednoho uživatele](serial-console-grub-single-user-mode.md).
* Použití konzole sériového portu pro [volání NMI a SysRq](serial-console-nmi-sysrq.md).
* Další informace o použití konzoly sériového portu k [povolit GRUB v různých distribucích](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/).
* Je taky dostupná ke konzole sériového portu [virtuální počítače s Windows](../windows/serial-console.md).
* Další informace o [Diagnostika spouštění](boot-diagnostics.md).

