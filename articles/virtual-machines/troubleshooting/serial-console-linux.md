---
title: Sériová konzola Azure pro Linux | Microsoft Docs
description: Obousměrná sériová Konzola pro Azure Virtual Machines a Virtual Machine Scale Sets.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: 0eda9fe0e16a945dcb9f9a1b686afcd2aebe6306
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854392"
---
# <a name="azure-serial-console-for-linux"></a>Sériová konzola Azure pro Linux

Konzola sériového portu v Azure Portal poskytuje přístup k textové konzole pro virtuální počítače se systémem Linux a instance sady škálování virtuálních počítačů. Toto sériové připojení se připojuje k sériovému portu ttyS0 virtuálního počítače nebo instance sady škálování virtuálních počítačů a poskytuje přístup k němu nezávisle na stavu sítě nebo operačního systému. Ke konzole sériového přístupu se dá přistupovat jenom pomocí Azure Portal a je povolená jenom pro uživatele, kteří mají roli přístupu přispěvatel nebo vyšší, k virtuálnímu počítači nebo sadě škálování virtuálních počítačů.

Sériová konzola funguje stejným způsobem pro virtuální počítače a instance sady škálování virtuálních počítačů. V tomto dokumentu budou všechny zmínky k virtuálním počítačům implicitně zahrnovat instance sady škálování virtuálních počítačů, pokud není uvedeno jinak.

Dokumentaci k sériové konzole pro Windows najdete v tématu [sériová Konzola pro Windows](../windows/serial-console.md).

> [!NOTE]
> Konzola sériového portu je všeobecně dostupná v globálních oblastech Azure. Zatím není k dispozici v Azure government nebo Azure China cloudy.


## <a name="prerequisites"></a>Požadavky

- Vaše virtuální počítač nebo instance sady škálování virtuálního počítače musí používat model nasazení správy prostředků. Klasická nasazení nejsou podporovány.

- Váš účet, který používá sériová konzola, musí mít [roli Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) pro virtuální počítač a účet úložiště [diagnostiky spouštění](boot-diagnostics.md) .

- Vaše virtuální počítač nebo instance sady škálování virtuálního počítače musí mít uživatele založené na heslech. Můžete si ho vytvořit pomocí [resetovat heslo](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) funkce rozšíření přístupu virtuálních počítačů. Vyberte **resetovat heslo** z **podpora a řešení potíží** oddílu.

- Vaše virtuální počítač nebo instance sady škálování virtuálního počítače musí mít povolenou [diagnostiku spouštění](boot-diagnostics.md) .

    ![Nastavení diagnostiky spouštění](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Nastavení specifická pro distribuce systému Linux najdete v tématu [dostupnost distribuce sériová konzola Linux](#serial-console-linux-distribution-availability).

- U virtuálního počítače nebo instance sady škálování virtuálních počítačů musí být nakonfigurovaná pro sériové `ttys0`výstup. Toto je výchozí nastavení pro Image Azure, ale u vlastních imagí je budete chtít dvakrát ověřit. Podrobnosti [níže](#custom-linux-images).


## <a name="get-started-with-the-serial-console"></a>Začínáme s konzolou sériového portu
Konzola sériového portu pro virtuální počítače a sadu škálování virtuálního počítače je dostupná jenom prostřednictvím Azure Portal:

### <a name="serial-console-for-virtual-machines"></a>Sériová Konzola pro Virtual Machines
Sériová Konzola pro virtuální počítače je stejně jednoduchá jako při kliknutí na **sériová konzola** v části **Podpora a řešení potíží** v Azure Portal.
  1. Otevřete web [Azure Portal](https://portal.azure.com).

  1. Přejděte na **všechny prostředky** a vyberte virtuální počítač. Otevře se stránka s přehledem pro virtuální počítač.

  1. Přejděte dolů k položce **podpora a řešení potíží** a vyberte **konzoly sériového portu**. Nové podokno v konzole sériového portu se otevře a aktivuje připojení.

     ![Okno sériové konzoly pro Linux](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Sériová Konzola pro Virtual Machine Scale Sets
Sériová konzola je k dispozici na základě jednotlivých instancí pro sady škálování virtuálních počítačů. Před zobrazením tlačítka **sériová konzola** budete muset přejít na jednotlivé instance sady škálování virtuálního počítače. Pokud vaše sada škálování virtuálního počítače nemá zapnutou diagnostiku spouštění, ujistěte se, že jste aktualizovali model sady škálování virtuálních počítačů, aby se aktivovala spouštění diagnostiky, a pak upgradovat všechny instance na nový model, aby bylo možné získat přístup ke konzole sériového portu.
  1. Otevřete web [Azure Portal](https://portal.azure.com).

  1. Přejděte na **všechny prostředky** a vyberte sadu škálování virtuálního počítače. Otevře se stránka s přehledem pro sadu škálování virtuálního počítače.

  1. Přejít k **instancím**

  1. Vyberte instanci sady škálování virtuálního počítače.

  1. V části **Podpora a řešení potíží** vyberte **sériová konzola**. Nové podokno v konzole sériového portu se otevře a aktivuje připojení.

     ![Sériová Konzola pro sadu škálování virtuálního počítače se systémem Linux](./media/virtual-machines-serial-console/vmss-start-console.gif)


> [!NOTE]
> Konzola sériového portu vyžaduje, aby místní uživatel s nakonfigurovaným heslem. Virtuální počítače nebo sady škálování virtuálních počítačů, které jsou nakonfigurované jenom pomocí veřejného klíče SSH, se nebudou moct přihlásit ke konzole sériového portu. Chcete-li vytvořit místního uživatele s heslem, použijte [rozšíření VMAccess](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), které je k dispozici na portálu, a vyberte možnost **resetovat heslo** v Azure Portal a vytvořte místního uživatele s heslem.
> Můžete také resetovat heslo správce v účtu [pomocí grub ke spuštění do režimu jednoho uživatele](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distribution-availability"></a>Dostupnost distribuce systému Linux v sériové konzole
Aby mohla konzola sériového fungování fungovat správně, musí být hostovaný operační systém nakonfigurovaný na čtení a zápis zpráv konzoly do sériového portu. U nejdůležitějších [distribucí systému Azure Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) je ve výchozím nastavení nakonfigurována sériová konzola. Výběr **sériová konzola** v části **Podpora a řešení potíží** v Azure Portal poskytuje přístup ke konzole sériového portu.

> [!NOTE]
> Pokud nevidíte cokoli v konzole sériového portu, ujistěte se, že je na vašem VIRTUÁLNÍm počítači povolená Diagnostika spouštění. Při stisknutí této akce budou často opravovat problémy, které se v konzole sériového umístění nezobrazí.

Distribuce      | Přístup k sériové konzole
:-----------|:---------------------
Red Hat Enterprise Linux    | Ve výchozím nastavení je povolený přístup Sériová konzola.
CentOS      | Ve výchozím nastavení je povolený přístup Sériová konzola.
Ubuntu      | Ve výchozím nastavení je povolený přístup Sériová konzola.
CoreOS      | Ve výchozím nastavení je povolený přístup Sériová konzola.
SUSE        | Novější image SLES dostupné v Azure mají ve výchozím nastavení povolený přístup pomocí sériové konzoly. Pokud používáte starší verze (10 nebo starší) služby SLES v Azure, přečtěte si [článek znalostní báze](https://www.novell.com/support/kb/doc.php?id=3456486) , ve kterém můžete povolit sériovou konzolu.
Oracle Linux        | Ve výchozím nastavení je povolený přístup Sériová konzola.

### <a name="custom-linux-images"></a>Vlastní image Linux
Pokud chcete pro vlastní image virtuálního počítače se systémem Linux povolit konzolu sériového prostředí, povolte přístup k konzole v souboru/etc/inittab `ttyS0`pro spuštění terminálu. Například: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`.

Budete také chtít přidat ttyS0 jako cíl pro sériový výstup. Další informace o konfiguraci vlastní image pro práci s konzolou sériového rozhraní najdete v tématu Obecné požadavky na systém při [vytváření a nahrání virtuálního pevného disku pro Linux v Azure](https://aka.ms/createuploadvhd#general-linux-system-requirements).

Pokud vytváříte vlastní jádro, zvažte povolení těchto příznaků jádra: `CONFIG_SERIAL_8250=y` a. `CONFIG_MAGIC_SYSRQ_SERIAL=y` Konfigurační soubor se obvykle nachází v cestě */boot/* .

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Běžné scénáře přístupu ke konzole sériového portu

Scénář          | Akce v konzole sériového portu
:------------------|:-----------------------------------------
Poškozený soubor *FSTAB* | Pokračujte stisknutím klávesy **ENTER** a opravte soubor *FSTAB* pomocí textového editoru. Je možné, že budete muset být v režimu jednoho uživatele. Další informace najdete v části "sériová konzola" o [tom, jak opravit problémy fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) a [pomocí konzoly sériového přístupu získat přístup k grub a jednomu uživatelskému režimu](serial-console-grub-single-user-mode.md).
Pravidla brány firewall na nesprávný |  Pokud jste nakonfigurovali softwaru iptables k blokování připojení SSH, můžete pomocí sériové konzoly pracovat s VIRTUÁLNÍm počítačem bez nutnosti SSH. Další podrobnosti najdete na [stránce softwaru iptables Man](https://linux.die.net/man/8/iptables).<br>Podobně pokud brána firewall blokuje přístup přes SSH, můžete k virtuálnímu počítači přistupovat pomocí sériové konzoly a znovu nakonfigurovat bránu firewall. Další podrobnosti najdete v [dokumentaci k bráně firewall](https://firewalld.org/documentation/).
Poškození systému souborů a vrácení | Další informace o řešení potíží s poškozenými systémy souborů pomocí sériové konzoly najdete v části věnované sériovým konzolám [virtuálního počítače Azure Linux](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) .
Problémy s konfigurací SSH | Přístup ke konzole sériového portu a změnit nastavení. Sériová konzola lze použít bez ohledu na konfiguraci SSH virtuálního počítače, protože nevyžaduje, aby virtuální počítač fungoval s připojením k síti. Průvodce odstraňováním potíží je k dispozici v tématu [řešení potíží s připojením SSH k virtuálnímu počítači Azure Linux, u kterého došlo](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection)k chybě, nebo je zamítnutá. Další podrobnosti najdete v podrobnějších [postupech při řešení potíží s nástrojem SSH pro problémy s připojením k virtuálnímu počítači Linux v Azure](./detailed-troubleshoot-ssh-connection.md) .
Interakce s zaváděcího programu pro spouštění | Restartujte virtuální počítač v okně sériové konzoly, abyste měli přístup k GRUB na svém VIRTUÁLNÍm počítači se systémem Linux. Další podrobnosti a informace týkající se distribuce najdete v tématu [použití sériové konzoly pro přístup k grub a jednomu uživatelskému režimu](serial-console-grub-single-user-mode.md).

## <a name="disable-the-serial-console"></a>Zakázání sériové konzoly
Ve výchozím nastavení mají všechny odběry povolený přístup pomocí sériové konzoly. Službu sériového portu můžete zakázat buď na úrovni předplatného, nebo na úrovni sady virtuálních počítačů nebo virtuálních počítačů. Aby mohla konzola sériového fungování fungovat, musí být na virtuálním počítači povolená Diagnostika spouštění.

### <a name="vmvirtual-machine-scale-set-level-disable"></a>Virtuální počítač/sada škálování virtuálního počítače – zakázat na úrovni sady
Sériová konzola se dá zakázat pro konkrétní virtuální počítač nebo sadu škálování virtuálního počítače zakázáním nastavení diagnostiky spouštění. Vypnutím diagnostiky spouštění z Azure Portal zakážete sériovou konzoli pro virtuální počítač nebo sadu škálování virtuálního počítače. Pokud v sadě škálování virtuálního počítače používáte sériovou konzolu, ujistěte se, že upgradujete instance sady škálování virtuálních počítačů na nejnovější model.

> [!NOTE]
> K povolení nebo zakázání konzole sériového portu k předplatnému, musíte mít oprávnění k zápisu do předplatného. Mezi tato oprávnění patří role správce nebo vlastník. Vlastní role můžete také mít oprávnění k zápisu.

### <a name="subscription-level-disable"></a>Zakázat úroveň předplatného
Konzole sériového portu se dají zakázat pro celé předplatné prostřednictvím [volání rozhraní API REST zakázat konzoly](/rest/api/serialconsole/console/disableconsole). Tato akce vyžaduje pro předplatné přístup na úrovni přispěvatele nebo vyšší. Můžete použít **vyzkoušet** funkce, které jsou k dispozici na této stránce dokumentace k rozhraní API zakázání a povolení konzole sériového portu pro odběr. Zadejte ID předplatného pro **SubscriptionId**, zadejte **výchozí** hodnotu **výchozí**a potom vyberte **Spustit**. Příkazy Azure CLI ještě nejsou k dispozici.

Pokud chcete znovu povolit sériovou konzolu pro předplatné, použijte [REST API volání Enable konzoly](/rest/api/serialconsole/console/enableconsole).

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
> To znamená, že uživatel, který je odpojen nebude odhlášeni. Možnost vymáhat odhlášení po odpojení (pomocí SIGHUP nebo podobného mechanismu) je stále v plánu. Pro systém Windows je povolen automatický časový limit ve speciální konzole pro správu (SAC); pro Linux ale můžete nakonfigurovat nastavení časový limit terminálu. Provedete to tak `export TMOUT=600` , že přidáte do souboru *. bash_profile* nebo *. profil* pro uživatele, kterého použijete k přihlášení do konzoly. Toto nastavení vyprší časový limit relace po 10 minutách.

## <a name="accessibility"></a>Usnadnění
Přístupnost je klíčovým fokusem pro konzolu sériového rozhraní Azure. Za tímto účelem jsme zajistili, že sériová konzola je plně přístupná.

### <a name="keyboard-navigation"></a>Procházení pomocí klávesnice
Použití **kartu** kláves na klávesnici a přejděte v konzole sériového portu rozhraní na webu Azure Portal. Vaše poloha budou zvýrazněny na obrazovce. Pokud chcete nechat fokus z okna konzoly sériového portu, stiskněte klávesu **Ctrl**+**F6** na klávesnici.

### <a name="use-serial-console-with-a-screen-reader"></a>Použití sériové konzoly se čtečkou obrazovky
Konzole sériového portu je integrované podpoře čtečky obrazovky. Navigace pomocí čtečky obrazovky zapnuté vám umožní alternativní text pro aktuálně vybrané tlačítko nahlas číst čtečka obrazovky.

## <a name="errors"></a>Chyby
Protože většina chyb jsou přechodné, opakování pokusu o připojení je často opravit. V následující tabulce najdete seznam chyb a způsoby zmírnění rizik. Tyto chyby a omezení se týkají jak virtuálních počítačů, tak instancí sady škálování virtuálních počítačů.

Chyba                            |   Omezení rizik
:---------------------------------|:--------------------------------------------|
Nepovedlo se načíst nastavení diagnostiky spouštění pro  *&lt;VMNAME&gt;* . Použití konzole sériového portu, zajistěte, že Diagnostika spouštění je povolená pro tento virtuální počítač. | Ujistěte se, že má virtuální počítač [Diagnostika spouštění](boot-diagnostics.md) povolena.
Virtuální počítač je v zastaveném stavu Uvolněno. Spusťte virtuální počítač a pokus o připojení konzoly sériového portu. | Pro přístup ke konzole sériového portu musí být virtuální počítač v spuštěném stavu.
Nemáte potřebná oprávnění k použití tohoto virtuálního počítače se sériovou konzolou. Ujistěte se, máte alespoň oprávnění role Přispěvatel virtuálních počítačů.| Přístup ke konzole sériového portu vyžaduje určitá oprávnění. Další informace najdete v tématu [požadavky](#prerequisites).
Nepovedlo se určit skupinu prostředků pro účet úložiště diagnostiky spouštění  *&lt;STORAGEACCOUNTNAME&gt;* . Ověřte, že Diagnostika spouštění je povolená pro tento virtuální počítač a máte přístup k tomuto účtu úložiště. | Přístup ke konzole sériového portu vyžaduje určitá oprávnění. Další informace najdete v tématu [požadavky](#prerequisites).
Webové sokety je uzavřený nebo nelze otevřít. | Možná budete muset povolit `*.console.azure.com`. Podrobnější ale delší přístup je na seznamu povolených IP adres [rozsahy IP adres Datacentra Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653), které mění poměrně.
Při přístupu k tomuto virtuálnímu počítači účet úložiště diagnostiky spouštění došlo k odpovědi "Zakázáno". | Ujistěte se, že diagnostika spouštění nemá bránu firewall účtu. Účet úložiště diagnostiky dostupné spouštěcí je nezbytné pro konzole sériového portu na funkci.

## <a name="known-issues"></a>Známé problémy
Jsme si vědomi některé problémy s konzole sériového portu. Tady je seznam těchto problémů a kroky pro omezení rizik. Tyto problémy a omezení rizik platí pro instance virtuálních počítačů i instancí sady škálování virtuálních počítačů.

Problém                           |   Omezení rizik
:---------------------------------|:--------------------------------------------|
Stisknutím klávesy **Enter** po připojení banner nezpůsobí výzvě přihlášení má být zobrazen. | Další informace najdete v tématu [Hitting zadejte nemá žádný účinek,](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). K tomuto problému může dojít, pokud máte spuštěný vlastní virtuální počítač, zesílené zařízení nebo GRUB config, které způsobí, že se Linux nepřipojí k sériovému portu.
Text Sériová konzola zabírá pouze část velikosti obrazovky (často po použití textového editoru). | Sériové konzoly nepodporují vyjednávání o velikosti okna ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)), což znamená, že nebude k dispozici žádný signál SIGWINCH k aktualizaci velikosti obrazovky a virtuální počítač nebude mít žádné znalosti o velikosti terminálu. Nainstalujte xterm nebo podobný nástroj, který vám `resize` poskytne příkaz, a pak spusťte. `resize`
Vkládání dlouhé řetězce nebude fungovat. | Konzole sériového portu omezení délky řetězce do terminálu na 2 048 znaků, aby se zabránilo přetížení šířky pásma sériového portu.
Sériová konzola nefunguje s bránou firewall účtu úložiště. | Sériová konzola podle návrhu nemůžou pracovat s povolenými branami firewall účtu úložiště v účtu úložiště diagnostiky spouštění.
Sériová konzola nepracuje s účtem úložiště pomocí Azure Data Lake Storage Gen2 s hierarchickými obory názvů. | Jedná se o známý problém s hierarchickými obory názvů. Pokud chcete zmírnit, ujistěte se, že účet úložiště diagnostiky spouštění virtuálního počítače není vytvořený pomocí Azure Data Lake Storage Gen2. Tuto možnost lze nastavit pouze při vytváření účtu úložiště. Je možné, že budete muset vytvořit samostatný účet úložiště diagnostiky spouštění bez Azure Data Lake Storage Gen2 povoleného pro zmírnění tohoto problému.
Nestabilní vstup klávesnice v obrázcích SLES BYOS. Vstup z klávesnice je jenom zřídka rozpoznaný. | Jedná se o problém s balíčkem Plymouth. Plymouth by se nemělo spouštět v Azure, protože nepotřebujete úvodní obrazovku a Plymouth brání možnosti platformy používat sériovou konzolu. Odeberte Plymouth pomocí `sudo zypper remove plymouth` a pak restartujte počítač. Případně můžete upravit řádek jádra vaší konfigurace grub připojením `plymouth.enable=0` ke konci řádku. To můžete provést úpravou [spouštěcí položky při spuštění](https://aka.ms/serialconsolegrub#single-user-mode-in-suse-sles)nebo úpravou GRUB_CMDLINE_LINUX řádku v `/etc/default/grub`, opětovným sestavením grub `grub2-mkconfig -o /boot/grub2/grub.cfg`a následným restartováním.


## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Q. Jak můžu poslat svůj názor?**

A. Poskytněte zpětnou vazbu vytvořením problému GitHubu na adrese https://aka.ms/serialconsolefeedback. Můžete také (méně upřednostňované), můžete odeslat zpětnou vazbu prostřednictvím azserialhelp@microsoft.com nebo v kategorii virtuální počítač https://feedback.azure.com.

**Q. Podporuje konzole sériového portu, kopírování a vkládání?**

A. Ano. Použití **Ctrl**+**Shift**+**C** a **Ctrl**+**Shift** + **V** zkopírovat a vložit do terminálu.

**Q. Můžu místo připojení SSH použít sériovou konzolu?**

A. I když se může zdát, že by toto použití mohlo být technicky možné, má se tato konzola používat primárně jako nástroj pro řešení potíží v situacích, kdy připojení přes SSH není možné. K použití konzoly sériového rozhraní jako náhrady SSH doporučujeme z následujících důvodů:

- Konzola sériového portu nemá jako SSH větší šířku pásma. Vzhledem k tomu, že se jedná o textové připojení, je obtížné pracovat s velkým grafickým rozhraním.
- Přístup k Sériová konzola je aktuálně možný jenom pomocí uživatelského jména a hesla. Vzhledem k tomu, že klíče SSH jsou mnohem bezpečnější než kombinace uživatelského jména a hesla, z hlediska zabezpečení přihlášení doporučujeme protokol SSH přes sériovou konzolu.

**Q. Kdo může povolit nebo zakázat sériovou konzolu pro moje předplatné?**

A. K povolení nebo zakázání konzole sériového portu na úrovni celé předplatné, musí mít oprávnění k zápisu do předplatného. Role, které mají oprávnění k zápisu zahrnují role správce nebo vlastníka. Vlastní role můžete také mít oprávnění k zápisu.

**Q. Kdo má přístup ke konzole sériového portu pro virtuální počítač/sadu škálování virtuálního počítače?**

A. Pro přístup ke konzole sériového portu musíte mít roli Přispěvatel virtuálních počítačů nebo vyšší pro virtuální počítač nebo sadu škálování virtuálního počítače.

**Q. Moje konzoly sériového portu se nezobrazuje nic, co mám dělat?**

A. Vaše image je pravděpodobně nesprávně nakonfigurované pro přístup ke konzole sériového portu. Informace o konfiguraci image pro povolení služby sériového prostředí najdete v tématu [dostupnost distribuce sériová konzola Linux](#serial-console-linux-distribution-availability).

**Q. Konzole sériového portu je dostupný pro škálovací sady virtuálních počítačů?**

A. Ano, je! Další informace najdete v tématu [sériová Konzola pro Virtual Machine Scale Sets](#serial-console-for-virtual-machine-scale-sets)

**Q. Když nastavil (a) jsem virtuální počítač nebo sadu škálování virtuálního počítače jenom pomocí ověřování pomocí klíče SSH, můžu stále používat sériovou konzolu pro připojení k virtuálnímu počítači nebo instanci sady škálování virtuálního počítače?**

A. Ano. Vzhledem k tomu, že konzola sériového portu nevyžaduje klíče SSH, stačí nastavit kombinaci uživatelského jména a hesla. Můžete to udělat tak, že v Azure Portal vyberete **resetovat heslo** a pomocí těchto přihlašovacích údajů se přihlásíte ke konzole sériového portu.

## <a name="next-steps"></a>Další kroky
* Použijte konzolu sériového [přístupu pro přístup k grub a jednomu uživatelskému režimu](serial-console-grub-single-user-mode.md).
* Použijte konzolu sériového rozhraní pro [volání NMI a SysRq](serial-console-nmi-sysrq.md).
* Naučte se používat konzolu sériového portu k [Povolení GRUB v různých distribuce](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/).
* Pro [virtuální počítače s Windows](../windows/serial-console.md)je dostupná taky konzola sériového portu.
* Další informace o [Diagnostika spouštění](boot-diagnostics.md).

