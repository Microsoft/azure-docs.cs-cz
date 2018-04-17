---
title: Virtuální počítač Azure konzoly sériového portu | Microsoft Docs
description: Obousměrná sériové konzoly pro virtuální počítače Azure.
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
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: 69f5e29be77f25d649ce357dae6e3905ab2bf6b8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="virtual-machine-serial-console-preview"></a>Virtuální počítač konzoly sériového portu (preview) 


Konzole sériového portu virtuálního počítače na platformě Azure poskytuje přístup k založený na textu konzoly pro virtuální počítače Linux a Windows. Toto sériové připojení je COM1 sériového portu virtuálního počítače a poskytuje přístup k virtuálnímu počítači a nesouvisí se síť virtuálních počítačů / operačního stavu systému. Přístup ke konzole sériového portu pro virtuální počítač, můžete provést pouze prostřednictvím portálu Azure aktuálně a povoleny pouze pro uživatele, kteří mají Přispěvatel virtuálních počítačů nebo vyšší přístup k virtuálnímu počítači. 

> [!Note] 
> Verze Preview jsou k dispozici pro vás, za předpokladu, že souhlasíte s podmínkami použití. Další informace najdete v tématu [Microsoft Azure doplňkové podmínky použití pro Microsoft Azure Preview.] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Aktuálně tato služba je v **verzi public preview** a přístup ke konzole sériového portu pro virtuální počítače je k dispozici pro globální oblastech Azure. V tomto okamžiku konzoly sériového portu, není k dispozici cloudu Azure Government, Azure v Německu a Azure China.


## <a name="prerequisites"></a>Požadavky 

* Virtuální počítač musí mít [spouštění diagnostiky](boot-diagnostics.md) povoleno 
* Účet, pomocí konzoly sériového portu, musí mít [role Přispěvatel](../../role-based-access-control/built-in-roles.md) pro virtuální počítač a [spouštění diagnostiky](boot-diagnostics.md) účet úložiště. 
* Nastavení specifická pro Linux distro, najdete v části [přístup ke konzole sériového portu pro Linux](#accessing-serial-console-for-linux)


## <a name="open-the-serial-console"></a>Otevření konzole sériového portu
konzoly sériového portu pro virtuální počítače je k dispozici pouze prostřednictvím [portál Azure](https://portal.azure.com). Níže jsou uvedené kroky pro přístup k konzoly sériového portu pro virtuální počítače prostřednictvím portálu 

  1. Otevřete portál Azure
  2. V nabídce vlevo vyberte virtuální počítače.
  3. Klikněte na virtuální počítač v seznamu. Otevře se stránka Přehled pro virtuální počítač.
  4. Posuňte se na podporu + řešení potíží s části a klikněte na možnost konzoly sériového portu (Preview). Nové podokno s konzole sériového portu se jim otevře a spuštění připojení.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)


> [!NOTE] 
> Konzoly sériového portu vyžaduje místního uživatele s heslem nakonfigurované. V tomto okamžiku virtuálních počítačů nakonfigurovat jenom pomocí veřejného klíče SSH nebudete mít přístup ke konzole sériového portu. Při vytváření místního uživatele s heslem, postupujte podle [rozšíření virtuálního počítače přístup](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension) a vytvoření místního uživatele s heslem.

### <a name="disable-feature"></a>Zakázat funkci
Funkce konzoly sériového portu můžete deaktivovat pro konkrétní virtuální počítače zakázáním nastavení diagnostiky spouštění tohoto virtuálního počítače.

## <a name="serial-console-security"></a>Zabezpečení konzoly sériového portu 

### <a name="access-security"></a>Zabezpečení přístupu 
Přístup k konzoly sériového portu je omezená na uživatele, kteří mají [virtuálních počítačů přispěvatelé](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) nebo vyšší přístup k virtuálnímu počítači. Pokud klienta služby AAD vyžaduje Vícefaktorové ověřování, tak přístup ke konzole sériového portu bude také nutné vícefaktorového ověřování, jako je přístup přes [portál Azure](https://portal.azure.com).

### <a name="channel-security"></a>Zabezpečení kanálu
Všechna data odeslána zpět a stanovilo se šifrují v drátové síti.

### <a name="audit-logs"></a>Protokoly auditu
Veškerý přístup ke konzole sériového portu je právě přihlášen [spouštění diagnostiky](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) protokoly virtuálního počítače. Přístup k tyto protokoly jsou ve vlastnictví a řídí správce virtuálního počítače Azure.  

>[!CAUTION] 
Při přihlášení žádná hesla přístup pro konzolu, pokud příkazy spuštění konzoly obsahovat nebo uložení hesla, tajné klíče, uživatelská jména nebo jakoukoli jinou formou z identifikovatelné osobní informace (PII), ty se zapíše do Diagnostika spouštění virtuálního počítače zaznamená, společně s všechny ostatní viditelné text, v rámci implementace funkce scrollback konzole sériového portu. Tyto protokoly jsou cyklické a pouze uživatelé, kteří mají oprávnění ke čtení pro účet úložiště diagnostiky mít přístup k nim, ale doporučujeme následující osvědčený postup pomocí konzole SSH pro všechno, co, která může zahrnovat tajných klíčů nebo PII. 

### <a name="concurrent-usage"></a>Souběžné používání
Pokud je uživatel připojený k konzoly sériového portu a jiný uživatel úspěšně požaduje přístup k tomuto virtuálnímu počítači stejný, bude první uživatel odpojen a druhý uživatel připojen způsobem podobají první uživatel stálého a nechá fyzické konzoly a nový uživatel uložený.

>[!CAUTION] 
To znamená, že uživatel, který odpojí nebude odhlásit! Schopnost Vynutit odhlášení při odpojení (prostřednictvím SIGHUP nebo podobné mechanismus) je stále v plánovaná. Pro Windows není automatické vypršení časového limitu povolené ve Speciální konzoly pro správu ale pro Linux můžete nakonfigurovat nastavení limitu terminálu. Stačí jednoduše přidat `export TMOUT=600` v .bash_profile nebo .profile pro uživatele přihlásit v konzole, časový limit relace po 10 minutách.

### <a name="disable-feature"></a>Zakázat funkci
Funkce konzoly sériového portu můžete deaktivovat pro konkrétní virtuální počítače zakázáním nastavení diagnostiky spouštění tohoto virtuálního počítače.

## <a name="common-scenarios-for-accessing-serial-console"></a>Běžné scénáře pro přístup k konzoly sériového portu 
Scénář          | Akce v konzoly sériového portu                |  Použitelnosti operačního systému 
:------------------|:-----------------------------------------|:------------------
Poškozený soubor FSTAB | Zadejte klíč můžete pokračovat, opravte fstab soubor pomocí textového editoru. V tématu [informace o vyřešení problémů fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) | Linux 
Pravidla brány firewall nesprávný | Přístup ke konzole sériového portu a opravte iptables nebo pravidla brány firewall systému Windows | Linux/Windows 
Poškození systému souborů nebo kontrola | Přístup ke konzole sériového portu a obnovení systému souborů | Linux/Windows 
Problémy s konfigurací protokolu RDP/SSH | Přístup ke konzole sériového portu a změnit nastavení | Linux/Windows 
Sítě uzamčení systému| Konzoly sériového portu přístup prostřednictvím portálu pro správu systému | Linux/Windows 
Interakci s zaváděcí program pro spouštění | Přístup GRUB/BCD prostřednictvím konzole sériového portu | Linux/Windows 

## <a name="accessing-serial-console-for-linux"></a>Přístup k konzoly sériového portu pro Linux
Aby konzoly sériového portu fungovat správně je třeba číst a zapisovat zprávy konzoly sériového portu hostovaného operačního systému. Většina [schváleného Azure Linuxových distribucích](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) mít konzoly sériového portu, který je ve výchozím nastavení nakonfigurované. Jednoduše kliknutím na portálu v části konzoly sériového portu, bude poskytovat přístup ke konzole. 

### <a name="access-for-redhat"></a>Přístup pro RedHat 
RedHat Imagím v Azure mají přístup ke konzole ve výchozím nastavení povolené. Režimu jednoho uživatele v Red Hat vyžaduje kořenový uživatel povolit, který je ve výchozím nastavení zakázaný. Pokud máte třeba povolit režimu jednoho uživatele, postupujte podle následujících pokynů:

1. Přihlaste se k systému Red Hat prostřednictvím SSH.
2. Povolit heslo pro kořenového uživatele 
 * `passwd root` (nastavení silné kořenového hesla)
3. Zajistěte, aby byl kořenový uživatel může pouze přihlásit přes ttyS0
 * `edit /etc/ssh/sshd_config` a ujistěte se, že PermitRootLog v je nastaven na žádný
 * `edit /etc/securetty file` možnost Povolit jenom přihlášení prostřednictvím ttyS0 

Teď Pokud systému se spustí v režimu jednoho uživatele je můžete přihlásit pomocí hesla kořenového.

Případně režimu jednoho uživatele RHEL 7.4 + nebo 6.9 + můžete povolit v GRUB vyzve, najdete v části pokyny [sem](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)

### <a name="access-for-ubuntu"></a>Přístup pro Ubuntu 
Ubuntu imagím v Azure mají přístup ke konzole ve výchozím nastavení povolené. Pokud v systému se spustí do režimu jednoho uživatele můžete přistupovat bez další přihlašovací údaje. 

### <a name="access-for-coreos"></a>Přístup pro jádro operačního systému
CoreOS imagím v Azure mají přístup ke konzole ve výchozím nastavení povolené. Pokud potřebné systému lze spustit na Jednouživatelský režim prostřednictvím změna GRUB parametry a přidání `coreos.autologin=ttyS0` by povolit uživateli jádra protokolu v a k dispozici v konzoly sériového portu. 

### <a name="access-for-suse"></a>Přístup pro SUSE
SLES imagím v Azure mají přístup ke konzole ve výchozím nastavení povolené. Pokud používáte starší verze SLES v Azure, postupujte podle kroků [článek znalostní báze KB](https://www.novell.com/support/kb/doc.php?id=3456486) povolit konzoly sériového portu. Novější bitové kopie z SLES 12 SP3 + také umožňuje přístup prostřednictvím konzole sériového portu v případě, že systém se spustí v nouzovém režimu.

### <a name="access-for-centos"></a>Přístup pro CentOS
CentOS imagím v Azure mají přístup ke konzole ve výchozím nastavení povolené. Jednouživatelský režim postupujte podle pokynů, které jsou podobné Red Hat Image výše. 

### <a name="access-for-oracle-linux"></a>Přístup pro Oracle Linux
Oracle Linux imagím v Azure mají přístup ke konzole ve výchozím nastavení povolené. Jednouživatelský režim postupujte podle pokynů, které jsou podobné Red Hat Image výše.

### <a name="access-for-custom-linux-image"></a>Přístup pro vlastní obrázek Linux
Pokud chcete povolit konzoly sériového portu pro vlastní bitovou kopii virtuálního počítače s Linuxem, povolte přístup ke konzole v /etc/inittab na ttyS0 spouštět terminál. Dole je příklad to přidat v souboru inittab 

`S0:12345:respawn:/sbin/agetty -L 115200 console vt102` 

## <a name="errors"></a>Chyby
Nejčastější chyby jsou přechodné ve své podstatě a opakování pokusu o připojení k adrese tyto. Níže uvedená tabulka obsahuje seznam chyb a zmírnění 

Chyba                            |   Omezení rizik 
:---------------------------------|:--------------------------------------------|
Nelze načíst nastavení diagnostiky spouštění pro '<VMNAME>'. Pokud chcete používat konzole sériového portu, zkontrolujte, zda že tento Diagnostika spouštění je povolený pro tento virtuální počítač. | Zkontrolujte, zda má virtuální počítač [spouštění diagnostiky](boot-diagnostics.md) povolena. 
Virtuální počítač je v zastaveném stavu deallocated. Spusťte virtuální počítač a opakujte pokus o připojení konzoly sériového portu. | Virtuální počítač musí být ve stavu spuštěna přístup ke konzole sériového portu
Nemáte potřebná oprávnění k použití tohoto virtuálního počítače, konzole sériového portu. Zajistěte, abyste měli aspoň oprávnění role Přispěvatel virtuálních počítačů.| Přístup ke konzole sériového portu vyžaduje určitá oprávnění pro přístup. V tématu [přístup k požadavkům](#prerequisites) podrobnosti
Nelze určit skupinu prostředků pro účet úložiště diagnostiky spouštění se<STORAGEACCOUNTNAME>'. Ověřte, zda je povoleno spuštění diagnostiky pro tento virtuální počítač a máte přístup k tomuto účtu úložiště. | Přístup ke konzole sériového portu vyžaduje určitá oprávnění pro přístup. V tématu [přístup k požadavkům](#prerequisites) podrobnosti

## <a name="known-issues"></a>Známé problémy 
Jak jsme jsou stále ve fázích preview pro přístup ke konzole sériového portu, jsme pracují prostřednictvím některé známé problémy, níže je seznam těchto s možných řešení 

Problém                           |   Omezení rizik 
:---------------------------------|:--------------------------------------------|
Neexistuje žádná možnost pomocí konzoly sériového portu sady instance škálování pro virtuální počítač |  V době preview přístup ke konzole sériového portu pro instancí sady škálování virtuálního počítače není podporován.
Stiskne zadejte po Banner informující o připojení nezobrazuje protokolu v řádku | [Stiskne zadejte se nic nestane.](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)


## <a name="frequently-asked-questions"></a>Nejčastější dotazy 
**Q. Jak lze odeslat zpětnou vazbu?**

A. Poskytnutí zpětné vazby jako problém přechodem na https://aka.ms/serialconsolefeedback. Případně méně (upřednostňované) odeslat zpětnou vazbu prostřednictvím azserialhelp@microsoft.com nebo v kategorii virtuálního počítače http://feedback.azure.com

**Q.I dojde k chybě "existující konzoly má konfliktní typ operačního systému"Systém Windows"pomocí požadovaný typ operačního systému Linux?**

A. Jedná se o známý problém tento problém lze vyřešit jednoduše otevřít [prostředí cloudu Azure](https://docs.microsoft.com/azure/cloud-shell/overview) v režimu bash a zkuste to znovu.

**Q. Nejste schopni přistupovat konzole sériového portu, kde můžete soubor případu podpory?**

A. Tato funkce preview se zabývá prostřednictvím Azure Preview podmínky. Podpora pro tento nejlépe vyřeší prostřednictvím kanálů uvedených výše. 

## <a name="next-steps"></a>Další postup
* Konzole sériového portu je také k dispozici pro [Windows](../windows/serial-console.md) virtuální počítače
* Další informace o [bootdiagnostics](boot-diagnostics.md)