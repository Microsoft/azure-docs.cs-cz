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
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: 00a776131321500386b507f45ea84817b08147f7
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37867857"
---
# <a name="virtual-machine-serial-console-preview"></a>Virtuální počítač sériová konzola (preview) 


Konzole sériového portu virtuálního počítače v Azure poskytuje přístup ke konzole založený na textu pro virtuální počítače s Linuxem a Windows. Toto sériové připojení je COM1 sériového portu virtuálního počítače a poskytuje přístup k virtuálnímu počítači a nesouvisí se síť virtuálních počítačů / provozní stav systému. Přístup ke konzole sériového portu pro virtuální počítač můžete provést jenom prostřednictvím portálu Azure portal momentálně a povolený jenom pro ty, kteří mají Přispěvatel virtuálních počítačů nebo vyšší přístup k virtuálnímu počítači. 

> [!Note] 
> Verze Preview jsou dostupné pro vás, za předpokladu, že budete souhlasit s podmínkami použití. Další informace najdete v tématu [Microsoft Azure dodatečných podmínkách použití systémů Microsoft Azure Preview.] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Aktuálně je tato služba v **ve verzi public preview** a přístup ke konzole sériového portu pro virtuální počítače je k dispozici globálními oblastmi Azure. Konzola sériového portu v tuto chvíli není k dispozici cloudu Azure Government, Azure Germany a Azure China.


## <a name="prerequisites"></a>Požadavky 

* Musí používat model nasazení správy prostředků. Klasická nasazení nejsou podporovány. 
* Virtuální počítač musí mít [Diagnostika spouštění](boot-diagnostics.md) povoleno 
* Účet, pomocí konzoly sériového portu, musí mít [role Přispěvatel](../../role-based-access-control/built-in-roles.md) pro virtuální počítač a [Diagnostika spouštění](boot-diagnostics.md) účtu úložiště. 
* Nastavení specifická pro distribuce Linuxu najdete v části [přístup ke konzole sériového portu pro Linux](#accessing-serial-console-for-linux)


## <a name="open-the-serial-console"></a>Otevřete konzoly sériového portu
Konzola sériového portu pro virtuální počítače je k dispozici pouze prostřednictvím [webu Azure portal](https://portal.azure.com). Níže je uvedený postup pro přístup ke konzole sériového portu pro virtuální počítače prostřednictvím portálu 

  1. Otevřete na webu Azure portal
  2. V nabídce vlevo vyberte virtuální počítače.
  3. Klikněte na virtuální počítač v seznamu. Otevře se stránka s přehledem pro virtuální počítač.
  4. Posuňte se dolů části Podpora a řešení potíží s části a klikněte na možnost sériová konzola (Preview). Otevře se nové podokno s sériové konzoly a spustit připojení.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)


> [!NOTE] 
> Konzola sériového portu vyžaduje místního uživatele s heslem nakonfigurovaným. V tuto chvíli virtuální počítače nakonfigurované pouze veřejný klíč SSH, nebudete mít přístup ke konzole sériového portu. Chcete-li vytvořit místní uživatele s heslem, postupujte podle [rozšíření přístupu virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension) a vytvořte místní uživatele s heslem.

### <a name="disable-feature"></a>Zakázat funkci
Funkce konzoly sériového portu lze deaktivovat pro konkrétní virtuální počítače tím, že zakážete nastavení diagnostiky spouštění Virtuálního počítače.

## <a name="serial-console-security"></a>Zabezpečení konzoly sériového portu 

### <a name="access-security"></a>Zabezpečení přístupu 
Přístup ke konzole sériového portu je omezená na uživatele, kteří mají [VM přispěvatelé](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) nebo vyšší než přístup k virtuálnímu počítači. Pokud váš tenant AAD vyžaduje Vícefaktorové ověřování, přístup ke konzole sériového portu bude také nutné MFA, jeho přístup je prostřednictvím [webu Azure portal](https://portal.azure.com).

### <a name="channel-security"></a>Zabezpečení kanálu
Všechna data se pošle zpět a vpřed je šifrovaná.

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
Poškozený soubor FSTAB | Zadejte klíč pokračovat a opravte soubor fstab pomocí textového editoru. Zobrazit [k vyřešení potíží se souborem fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) | Linux 
Pravidla brány firewall na nesprávný | Přístup ke konzole sériového portu a opravte iptables nebo pravidla brány firewall na Windows | Linux/Windows 
Poškození systému souborů a vrácení | Přístup ke konzole sériového portu a obnovení systému souborů | Linux/Windows 
Problémy s konfigurací SSH nebo RDP | Přístup ke konzole sériového portu a změnit nastavení | Linux/Windows 
Uzamknutí sítě v systému| Konzola sériového portu přístup prostřednictvím portálu pro správu systému | Linux/Windows 
Interakce s zaváděcího programu pro spouštění | Přístup GRUB/BCD prostřednictvím konzoly sériového portu | Linux/Windows 

## <a name="accessing-serial-console-for-linux"></a>Přístup ke konzole sériového portu pro Linux
Aby konzoly sériového portu, aby správně fungoval hostovaného operačního systému nastavené pro čtení a zápis zpráv konzoly sériového portu. Většina [Linuxových distribucí doporučených pro Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) mají ve výchozím nastavení nakonfigurované konzoly sériového portu. Jednoduše kliknutím na portálu v části konzoly sériového portu bude poskytovat přístup ke konzole. 

### <a name="access-for-red-hat"></a>Přístup k systému Red Hat 
Red Hat Imagí dostupných v Azure máte přístup ke konzole ve výchozím nastavení povolená. Režim jednoho uživatele v Red Hat vyžaduje kořenového uživatele povoleno, která je ve výchozím nastavení zakázaná. Pokud máte třeba povolit režim jednoho uživatele, použijte následující pokyny:

1. Přihlaste se k systému Red Hat pomocí protokolu SSH
2. Povolit heslo uživatele root 
 * `passwd root` (nastavte silné kořenové heslo)
3. Ujistěte se, že uživatel root mohou přihlašovat pouze prostřednictvím ttyS0
 * `edit /etc/ssh/sshd_config` a ujistěte se, že PermitRootLog v je nastavena na no
 * `edit /etc/securetty file` Povolit přihlášení prostřednictvím ttyS0 jenom 

Teď Pokud systému se spustí do režimu jednoho uživatele můžete přihlásit pomocí hesla kořenového.

Případně zobrazí výzvu režimu jednoho uživatele RHEL 7.4 + nebo 6.9 + můžete povolit v GRUB, naleznete v tématu pokyny [zde](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)

### <a name="access-for-ubuntu"></a>Přístup k Ubuntu 
Imagemi Ubuntu v Azure k dispozici máte přístup ke konzole ve výchozím nastavení povolená. Pokud systém ochránil před vytvořením Jednouživatelský režim můžete přistupovat bez další přihlašovací údaje. 

### <a name="access-for-coreos"></a>Přístup pro CoreOS
CoreOS imagí dostupných v Azure mají přístup ke konzole ve výchozím nastavení povolená. Pokud šlo nezbytné systém do režimu jednoho uživatele prostřednictvím změna GRUB parametry a přidání `coreos.autologin=ttyS0` umožní uživateli jádra protokolu v a k dispozici v konzole sériového portu. 

### <a name="access-for-suse"></a>Přístup pro SUSE
Imagí SLES dostupných v Azure máte přístup ke konzole ve výchozím nastavení povolená. Pokud používáte starší verze SLES v Azure, postupujte [článku znalostní BÁZE](https://www.novell.com/support/kb/doc.php?id=3456486) umožňující konzoly sériového portu. Novější Imagí z SLES 12 SP3 + také umožňuje přístup prostřednictvím konzoly sériového portu v případě, že systém se spustí v nouzovém režimu.

### <a name="access-for-centos"></a>Přístup pro CentOS
Imagí centOS dostupných v Azure máte přístup ke konzole ve výchozím nastavení povolená. Jednouživatelský režim postupujte podle pokynů, které jsou podobné Image Red Hat, výše. 

### <a name="access-for-oracle-linux"></a>Přístup pro Oracle Linux
Linuxové Image Oracle v Azure k dispozici máte přístup ke konzole ve výchozím nastavení povolená. Jednouživatelský režim postupujte podle pokynů, které jsou podobné Image Red Hat, výše.

### <a name="access-for-custom-linux-image"></a>Přístup pro vlastní image Linuxu
Pokud chcete povolit konzoly sériového portu pro vaši vlastní image virtuálního počítače s Linuxem, povolte přístup ke konzole v /etc/inittab spouštět ttyS0 terminálu. Tady je příklad, přidáte do souboru inittab 

`S0:12345:respawn:/sbin/agetty -L 115200 console vt102` 

## <a name="errors"></a>Chyby
Většina chyb jsou přechodné povahy a opakováním tyto adresy připojení. Následující tabulka obsahuje seznam chyb a zmírnění distribuovaných útoků 

Chyba                            |   Omezení rizik 
:---------------------------------|:--------------------------------------------|
Nepovedlo se načíst nastavení diagnostiky spouštění pro "<VMNAME>". Použití konzole sériového portu, zajistěte, že Diagnostika spouštění je povolená pro tento virtuální počítač. | Ujistěte se, že má virtuální počítač [Diagnostika spouštění](boot-diagnostics.md) povolena. 
Virtuální počítač je v zastaveném stavu Uvolněno. Spusťte virtuální počítač a pokus o připojení konzoly sériového portu. | Virtuální počítač musí být ve spuštěném stavu pro přístup ke konzole sériového portu
Nemáte požadovaná oprávnění ke konzole sériového portu tento virtuální počítač použít. Ujistěte se, máte alespoň oprávnění role Přispěvatel virtuálních počítačů.| Přístup ke konzole sériového portu vyžaduje určitá oprávnění pro přístup. Zobrazit [požadavky na přístup](#prerequisites) podrobnosti
Nepovedlo se určit skupinu prostředků pro účet úložiště diagnostiky spouštění '<STORAGEACCOUNTNAME>". Ověřte, že Diagnostika spouštění je povolená pro tento virtuální počítač a máte přístup k tomuto účtu úložiště. | Přístup ke konzole sériového portu vyžaduje určitá oprávnění pro přístup. Zobrazit [požadavky na přístup](#prerequisites) podrobnosti

## <a name="known-issues"></a>Známé problémy 
Jak jsme jsou stále ve verzi preview fázích pro přístup ke konzole sériového portu, jsme práci prostřednictvím některým známým problémům, níže je seznam těchto prvků s možná řešení 

Problém                           |   Omezení rizik 
:---------------------------------|:--------------------------------------------|
Neexistuje žádná možnost pomocí virtuálního počítače škálovací sady instance sériové konzoly |  V období preview se nepodporuje přístup ke konzole sériového portu pro instance škálovací sady virtuálních počítačů.
Dosažení zadejte po banner připojení není uveden do protokolu v řádku | [Dosažení zadejte neprovede žádnou akci](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)


## <a name="frequently-asked-questions"></a>Nejčastější dotazy 
**Q. Jak můžu poslat svůj názor?**

A. Poskytnout zpětnou vazbu jako problém tak, že přejdete do https://aka.ms/serialconsolefeedback. Můžete také menší (upřednostňované) pošlete zpětnou vazbu prostřednictvím azserialhelp@microsoft.com nebo v kategorii virtuálního počítače http://feedback.azure.com

**Q.I dojde k chybě "existující Konzola má konfliktní typ operačního systému"Windows"s požadovaný typ operačního systému Linux?**

A. Toto je známý problém, chcete-li tento problém vyřešit, stačí jednoduše otevřít [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) v prostředí bash režim a zkuste to znovu.

**Q. Nejde mi pro přístup ke konzole sériového portu, kde můžete soubor případ podpory?**

A. Tato funkce ve verzi preview se vztahuje prostřednictvím podmínky verze Preview služby Azure. Podpora pro tuto potíže nejlépe vyřeší prostřednictvím kanálů uvedených výše. 

## <a name="next-steps"></a>Další postup
* Je taky dostupná ke konzole sériového portu [Windows](../windows/serial-console.md) virtuálních počítačů
* Další informace o [bootdiagnostics](boot-diagnostics.md)