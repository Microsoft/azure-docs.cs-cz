---
title: Řešení potíží s převzetím služeb při selhání v Azure | Microsoft Docs
description: Tento článek popisuje způsoby řešení běžných chyb při selhání služby v Azure.
author: ponatara
manager: abhemraj
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 01/08/2020
ms.author: mayg
ms.openlocfilehash: 8fe21ce5b92d672a2e025e0b45b8cbaea5951a8b
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043899"
---
# <a name="troubleshoot-errors-when-failing-over-vmware-vm-or-physical-machine-to-azure"></a>Řešení chyb při přebírání služeb při selhání fyzického počítače nebo virtuálního počítače VMware do Azure

Při převzetí služeb při selhání virtuálního počítače do Azure se může zobrazit jedna z následujících chyb. K řešení potíží použijte popsané kroky pro jednotlivé chybové podmínky.

## <a name="failover-failed-with-error-id-28031"></a>Převzetí služeb při selhání se nezdařilo s ID chyby 28031

Site Recovery se nepovedlo vytvořit virtuální počítač s převzetím služeb při selhání v Azure. K tomu může dojít z některého z následujících důvodů:

* Pro vytvoření virtuálního počítače není k dispozici dostatečná kvóta. dostupná kvóta se dá ověřit tak, že přejdete na předplatné-> využití a kvóty. Můžete otevřít [novou žádost o podporu](https://aka.ms/getazuresupport) a zvýšit tak kvótu.

* Pokoušíte se převzetí služeb při selhání virtuálních počítačů různých skupin velikostí ve stejné skupině dostupnosti. Ujistěte se, že jste vybrali stejnou rodinu velikostí pro všechny virtuální počítače ve stejné skupině dostupnosti. Přejděte na nastavení výpočty a síť virtuálního počítače a pak zkuste převzetí služeb při selhání zopakovat.

* U předplatného je k dispozici zásada, která brání vytvoření virtuálního počítače. Změňte zásadu tak, aby povolovala vytváření virtuálního počítače, a pak zkuste převzetí služeb při selhání zopakovat.

## <a name="failover-failed-with-error-id-28092"></a>Převzetí služeb při selhání se nezdařilo s ID chyby 28092

Site Recovery nebylo možné vytvořit síťové rozhraní pro virtuální počítač, u kterého došlo k převzetí služeb při selhání. Ujistěte se, že máte k dispozici dostatečnou kvótu pro vytváření síťových rozhraní v rámci předplatného. Dostupnou kvótu můžete ověřit tak, že přejdete na předplatné-> využití a kvóty. Můžete otevřít [novou žádost o podporu](https://aka.ms/getazuresupport) a zvýšit tak kvótu. Pokud máte dostatečnou kvótu, může to být přechodný problém, zkuste operaci zopakovat. Pokud problém přetrvává i po opakování, pak na konci tohoto dokumentu ponechte komentář.  

## <a name="failover-failed-with-error-id-70038"></a>Převzetí služeb při selhání se nezdařilo s ID chyby 70038

Site Recovery se nepovedlo vytvořit klasický virtuální počítač v Azure. K tomu může dojít z těchto důvodů:

* Jeden z prostředků, jako je třeba virtuální síť, která je nutná pro vytvoření virtuálního počítače, neexistuje. Vytvořte virtuální síť, která je k dispozici v nastavení výpočty a síť virtuálního počítače, nebo změňte nastavení na virtuální síť, která už existuje, a pak zkuste převzetí služeb při selhání zopakovat.

## <a name="failover-failed-with-error-id-170010"></a>Převzetí služeb při selhání se nezdařilo s ID chyby 170010

Site Recovery se nepovedlo vytvořit virtuální počítač s převzetím služeb při selhání v Azure. Může to být způsobeno tím, že se pro místní virtuální počítač nezdařila interní aktivita pro vysazování.

Aby bylo možné v Azure vyvolat libovolný počítač, prostředí Azure vyžaduje, aby některé z ovladačů byly ve stavu spuštění spouštění a aby služby jako DHCP byly ve stavu autostart. V době převzetí služeb při selhání pak činnost pro vystavení převede typ spouštění pro **ovladače ATAPI, Intelide, storflt, VMBus a storvsc** na spouštění po spuštění. Také převede typ spouštění několika služeb, jako je DHCP, na automatické spuštění. Tato aktivita může selhat z důvodu problémů specifických pro prostředí. 

Chcete-li ručně změnit typ spouštění ovladačů pro **hostovaný operační systém Windows**, postupujte podle následujících kroků:

1. [Stáhněte](https://download.microsoft.com/download/5/D/6/5D60E67C-2B4F-4C51-B291-A97732F92369/Script-no-hydration.ps1) si skript pro No-vysazování a spusťte ho následujícím způsobem. Tento skript zkontroluje, jestli virtuální počítač vyžaduje službu pro vysazování.

    `.\Script-no-hydration.ps1`

    Pokud je požadováno vysazování, poskytuje následující výsledek:

    ```output
    REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

    This system doesn't meet no-hydration requirement.
    ```

    V případě, že virtuální počítač splňuje požadavek bez nutnosti vysazování, skript poskytne výsledek "Tento systém splní požadavek bez nutnosti vysazování". V takovém případě jsou všechny ovladače a služby ve stavu, jak to vyžaduje Azure a kdy se na virtuálním počítači nepožaduje aplikace pro vysazování.

2. Pokud virtuální počítač nesplňuje požadavek na použití bez nutnosti vystavování, spusťte skript pro ne-vysazování.

    `.\Script-no-hydration.ps1 -set`
    
    Tato akce převede typ spouštění ovladačů a poskytne výsledek podobný tomuto:

    ```output
    REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

    Updating registry:  REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc   start =  0

    This system is now no-hydration compatible.
    ```

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>Nepovedlo se připojit/RDP/SSH k virtuálnímu počítači převzetí služeb při selhání kvůli šedě připojenému tlačítku připojení na virtuálním počítači.

Podrobné pokyny pro řešení potíží s protokolem RDP najdete v naší [dokumentaci.](/troubleshoot/azure/virtual-machines/troubleshoot-rdp-connection)

Podrobné pokyny k odstraňování potíží s SSH najdete v [naší dokumentaci.](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection)

Pokud se tlačítko **připojit** na virtuálním počítači služby převzít služby při selhání v Azure zobrazuje šedě a nejste k Azure připojeni přes Express Route nebo připojení VPN typu Site-to-site,

1. Přejděte na **síť virtuálních počítačů**  >  , klikněte na název požadovaného síťového rozhraní.  ![Snímek obrazovky ukazuje stránku sítě pro virtuální počítač se zvoleným názvem síťového rozhraní.](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. Přejděte do části **Konfigurace protokolu IP** a potom klikněte na pole název požadované konfigurace protokolu IP. ![Snímek obrazovky se zobrazí stránka Konfigurace I P pro síťové rozhraní s vybraným názvem konfigurace I P.](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. Pokud chcete povolit veřejnou IP adresu, klikněte na **Povolit**. ![Povolit IP adresu](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. Klikněte na **konfigurovat požadovaná nastavení**  >  **vytvořit nový**. ![Vytvořit nový](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. Zadejte název veřejné adresy, zvolte výchozí možnosti pro položku **SKU** a **přiřazení** a pak klikněte na **OK**.
6. Nyní uložte provedené změny kliknutím na **Uložit**.
7. Zavřete panely a přejděte do části **Přehled** virtuálního počítače pro připojení/RDP.

## <a name="unable-to-connectrdpssh---vm-connect-button-available"></a>Připojení/RDP/SSH – tlačítko připojit k virtuálnímu počítači není k dispozici

Pokud je tlačítko **připojit** na virtuálním počítači služby při selhání v Azure k dispozici (není zobrazeno šedě), zkontrolujte **diagnostiku spouštění** na virtuálním počítači a zkontrolujte chyby, jak je uvedeno v [tomto článku](/troubleshoot/azure/virtual-machines/boot-diagnostics).

1. Pokud se virtuální počítač nespustil, zkuste provést převzetí služeb při selhání do staršího bodu obnovení.
2. Pokud aplikace není ve virtuálním počítači funkční, zkuste provést převzetí služeb při selhání do bodu obnovení konzistentního vzhledem k aplikacím.
3. Pokud je virtuální počítač připojený k doméně, ujistěte se, že řadič domény správně funguje. Můžete to udělat pomocí následujících kroků:

    a. Vytvořte nový virtuální počítač ve stejné síti.

    b.  Zajistěte, aby se mohl připojit ke stejné doméně, na které se očekává, že se má virtuální počítač převzetí služeb při selhání nacházet.

    c. Pokud řadič domény **nepracuje správně** , zkuste se přihlásit k virtuálnímu počítači s převzetím služeb při selhání pomocí místního účtu správce.
4. Pokud používáte vlastní server DNS, ujistěte se, že je dostupný. Můžete to udělat pomocí následujících kroků:

    a. Vytvořit nový virtuální počítač ve stejné síti a

    b. Ověřte, jestli virtuální počítač dokáže překládat IP adresy pomocí vlastního serveru DNS.

>[!Note]
>Povolení jakéhokoli jiného nastavení než diagnostiky spouštění vyžaduje, aby byl na virtuálním počítači před převzetím služeb při selhání nainstalovaný agent virtuálního počítače Azure.

## <a name="unable-to-open-serial-console-after-failover-of-a-uefi-based-machine-into-azure"></a>Po převzetí služeb při selhání počítače založeného na rozhraní UEFI do Azure se nedá otevřít sériová konzola.

Pokud se k počítači můžete připojit pomocí protokolu RDP, ale nemůžete spustit sériovou konzolu, postupujte podle následujících kroků:

* Pokud je operační systém v systému Red Hat nebo Oracle Linux 7. */8.0, spusťte na virtuálním počítači Azure s podporou převzetí služeb při selhání následující příkaz s oprávněními root. Restartujte virtuální počítač za příkazem.

  ```console
  grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg
  ```

* Pokud je operační systém počítače CentOS 7. *, spusťte na VIRTUÁLNÍm počítači Azure s podporou převzetí služeb při selhání následující příkaz s oprávněními root. Restartujte virtuální počítač za příkazem.

  ```console
  grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg
  ```

## <a name="unexpected-shutdown-message-event-id-6008"></a>Neočekávaná zpráva vypnutí (ID události 6008)

Pokud při spuštění virtuálního počítače s Windows po převzetí služeb při selhání obdržíte neočekávanou zprávu o vypnutí v obnoveném virtuálním počítači, znamená to, že v bodu obnovení používaném pro převzetí služeb při selhání nebyl zachycen stav vypínání virtuálního počítače. K tomu dojde při obnovení do bodu, kdy se virtuální počítač zcela neukončil.

To obvykle není příčinou obav a je možné je obvykle ignorovat pro neplánované převzetí služeb při selhání. Pokud je naplánovaná převzetí služeb při selhání, ujistěte se, že je virtuální počítač před převzetím služeb při selhání řádně vypnutý, a poskytněte dostatek času pro odeslání místně vyřízených dat pro replikaci do Azure. Pak na [obrazovce převzetí služeb při selhání](site-recovery-failover.md#run-a-failover) použijte **nejnovější** možnost, aby se všechna nevyřízená data v Azure zpracovala do bodu obnovení, který se pak používá pro převzetí služeb virtuálního počítače při selhání.

## <a name="unable-to-select-the-datastore"></a>Nepovedlo se vybrat úložiště dat.

Tento problém je uveden, pokud se nemůžete podívat na úložiště dat v Azure portálu při pokusu o opětovné zapnutí ochrany virtuálního počítače, u kterého došlo k převzetí služeb při selhání. Důvodem je to, že hlavní cíl není rozpoznán jako virtuální počítač v servery vCenter přidaný do Azure Site Recovery.

Další informace o opětovném zapnutí ochrany virtuálního počítače najdete v tématu o [opětovném zapnutí a navrácení služeb po obnovení do Azure do místní lokality](vmware-azure-reprotect.md).

Řešení tohoto problému:

Ručně vytvořte hlavní cíl v vCenter, který spravuje zdrojový počítač. Úložiště dat bude k dispozici po dalších operacích zjišťování vCenter a aktualizaci prostředků infrastruktury.

> [!Note]
> 
> Dokončení operací zjišťování a aktualizace prostředků infrastruktury může trvat až 30 minut. 

## <a name="linux-master-target-registration-with-cs-fails-with-a-tls-error-35"></a>Registrace hlavní cílové platformy Linux s CS se nezdařila s chybou TLS 35 

Registrace hlavního cíle Azure Site Recovery se konfiguračním serverem se nezdařila z důvodu povolení ověřeného proxy serveru na hlavním cíli. 
 
Tato chyba je uvedena v protokolu instalace v následujících řetězcích: 

```
RegisterHostStaticInfo encountered exception config/talwrapper.cpp(107)[post] CurlWrapper Post failed : server : 10.38.229.221, port : 443, phpUrl : request_handler.php, secure : true, ignoreCurlPartialError : false with error: [at curlwrapperlib/curlwrapper.cpp:processCurlResponse:231]   failed to post request: (35) - SSL connect error. 
```

Řešení tohoto problému:
 
1. Na virtuálním počítači konfiguračního serveru otevřete příkazový řádek a ověřte nastavení proxy pomocí následujících příkazů:

    Cat/etc/Environment echo $http _proxy echo $https _proxy 

2. Pokud výstup předchozích příkazů ukazuje, že jsou definovaná nastavení http_proxy nebo https_proxy, pomocí jedné z následujících metod odblokujte komunikaci hlavního cíle pomocí konfiguračního serveru:
   
   - Stáhněte si [Nástroj PsExec](/sysinternals/downloads/psexec).
   - Použijte nástroj pro přístup k systémovému kontextu uživatele a určete, jestli je adresa proxy serveru nakonfigurovaná. 
   - Pokud je proxy server nakonfigurován, otevřete aplikaci Internet Explorer v kontextu uživatele systému pomocí nástroje PsExec.
  
     **PsExec-s-i "%programfiles%\Internet Explorer\iexplore.exe"**

   - Chcete-li zajistit, aby hlavní cílový server mohl komunikovat s konfiguračním serverem:
  
     - Upravte nastavení proxy serveru v Internet Exploreru, aby se IP adresa hlavního cílového serveru nepoužívala prostřednictvím proxy serveru.   
     Nebo
     - Zakažte proxy server na hlavním cílovém serveru. 


## <a name="next-steps"></a>Další kroky
- Řešení potíží s [připojením RDP k virtuálnímu počítači s Windows](/troubleshoot/azure/virtual-machines/troubleshoot-rdp-connection)
- Řešení potíží s [připojením SSH k virtuálnímu počítači se systémem Linux](/troubleshoot/azure/virtual-machines/detailed-troubleshoot-ssh-connection)

Pokud potřebujete další nápovědu, odešlete dotaz do souboru [Microsoft Q&stránku s otázkou pro Site Recovery](/answers/topics/azure-site-recovery.html) nebo ponechte komentář na konci tohoto dokumentu. Máme aktivní komunitu, která vám může pomoct.