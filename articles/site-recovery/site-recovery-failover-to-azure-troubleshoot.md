---
title: Poradce při potížích s převzetím služeb při selhání Azure | Dokumenty společnosti Microsoft
description: Tento článek popisuje způsoby řešení běžných chyb při převzetí služby při selhání do Azure
author: ponatara
manager: abhemraj
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 01/08/2020
ms.author: mayg
ms.openlocfilehash: 6de37daa0b9e0ebc711a5dacbdce352e3675a3db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257742"
---
# <a name="troubleshoot-errors-when-failing-over-vmware-vm-or-physical-machine-to-azure"></a>Poradce při potížích s chybami při selhání virtuálního počítače VMware nebo fyzického počítače do Azure

Při převzetí služeb při selhání virtuálního počítače do Azure se může zobrazit jedna z následujících chyb. Chcete-li vyřešit potíže, použijte popsané kroky pro každý chybový stav.

## <a name="failover-failed-with-error-id-28031"></a>Převzetí služeb při selhání se nezdařilo s chybou ID 28031

Obnovení webu se nepodařilo vytvořit selhání virtuálního počítače v Azure. Může se to stát z jednoho z následujících důvodů:

* K vytvoření virtuálního počítače není k dispozici dostatečná kvóta: Dostupnou kvótu můžete zkontrolovat tak, že přejdete na předplatné -> využití + kvóty. Můžete otevřít [novou žádost o podporu](https://aka.ms/getazuresupport) pro zvýšení kvóty.

* Pokoušíte se převzetí služeb při selhání virtuální počítače různých velikostí rodin y ve stejné sadě dostupnosti. Ujistěte se, že jste zvolili stejnou rodinu velikostí pro všechny virtuální počítače ve stejné sadě dostupnosti. Změňte velikost tak, že přejdete na Nastavení výpočetních prostředků a sítě virtuálního počítače a pak zkuste převzetí služeb při selhání.

* Existuje zásada na předplatné, která brání vytvoření virtuálního počítače. Změňte zásadu tak, aby bylo možné vytvořit virtuální počítač a pak opakovat převzetí služeb při selhání.

## <a name="failover-failed-with-error-id-28092"></a>Převzetí služeb při selhání se nezdařilo s chybou ID 28092

Obnovení sítě nebylo možné vytvořit síťové rozhraní pro selhání virtuálního počítače. Ujistěte se, že máte k dispozici dostatečnou kvótu pro vytvoření síťových rozhraní v předplatném. Dostupnou kvótu můžete zkontrolovat tak, že přejdete na předplatné -> využití + kvóty. Můžete otevřít [novou žádost o podporu](https://aka.ms/getazuresupport) pro zvýšení kvóty. Pokud máte dostatečnou kvótu, může se jedná o občasný problém, zkuste operaci znovu. Pokud problém přetrvává i po opakování, zanechte komentář na konci tohoto dokumentu.  

## <a name="failover-failed-with-error-id-70038"></a>Převzetí služeb při selhání se nezdařilo s chybou ID 70038

Obnovení webu se nepodařilo vytvořit selhání než klasický virtuální počítač v Azure. Mohlo by se to stát, protože:

* Jeden z prostředků, jako je například virtuální síť, která je vyžadována pro vytvoření virtuálního počítače neexistuje. Vytvořte virtuální síť, jak je uvedeno v části Výpočetní a síťové nastavení virtuálního počítače nebo upravit nastavení na virtuální síť, která již existuje, a pak zkuste převzetí služeb při selhání.

## <a name="failover-failed-with-error-id-170010"></a>Převzetí služeb při selhání se nezdařilo s chybou ID 170010

Obnovení webu se nepodařilo vytvořit selhání virtuálního počítače v Azure. Může k tomu dojít, protože interní aktivita hydratace se nezdařila pro místní virtuální počítač.

Chcete-li vyvolat jakýkoli počítač v Azure, prostředí Azure vyžaduje, aby některé ovladače byly ve stavu spuštění spouštění a služby, jako je DHCP, byly ve stavu automatického spuštění. Proto aktivita hydratace v době převzetí služeb při selhání převádí typ spuštění **atapi, intelide, storflt, vmbus a storvsc ovladače** pro spuštění. Převádí také typ spuštění několika služeb, jako je DHCP, na automatické spuštění. Tato aktivita může selhat z důvodu problémů specifické pro prostředí. 

Chcete-li ručně změnit typ spuštění ovladačů pro **hostovaný operační systém Windows**, postupujte podle následujících kroků:

1. [Stáhněte si](https://download.microsoft.com/download/5/D/6/5D60E67C-2B4F-4C51-B291-A97732F92369/Script-no-hydration.ps1) skript bez hydratace a spusťte jej následujícím způsobem. Tento skript zkontroluje, jestli virtuální počítač vyžaduje hydrataci.

    `.\Script-no-hydration.ps1`

    To dává následující výsledek, pokud je nutná hydratace:

        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

        This system doesn't meet no-hydration requirement.

    V případě, že virtuální počítač splňuje požadavek na nehydrataci, skript poskytne výsledek "Tento systém splňuje požadavek na nehydrataci". V takovém případě jsou všechny ovladače a služby ve stavu, jak to vyžaduje Azure a není vyžadována hydratace na virtuálním počítači.

2. Spusťte skript bez hydratace nastavit takto, pokud virtuální počítač nesplňuje požadavek na bez hydratace.

    `.\Script-no-hydration.ps1 -set`
    
    Tím se převede typ spuštění ovladačů a dá výsledek jako níže:
    
        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0 

        Updating registry:  REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc   start =  0 

        This system is now no-hydration compatible. 

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>Nelze připojit/RDP/SSH k selhání nad virtuálním počítačem z důvodu šedého tlačítka Připojit na virtuálním počítači

Pokud je tlačítko **Připojit** na serveru Microsoft Microsoft Over VM v Azure zašedlé a nejste připojeni k Azure prostřednictvím expresní trasy nebo připojení VPN site-to-site, pak,

1. Přejděte na **Virtual machine** > **Networking**, klikněte na název požadovaného síťového rozhraní.  ![síťové rozhraní](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. Přejděte na **Ip Konfigurace**a klikněte na pole názvu požadované konfigurace IP. ![Konfigurace IP](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. Chcete-li povolit veřejnou IP adresu, klikněte na **povolit**. ![Povolit IP adresu](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. Klikněte na **Konfigurovat požadovaná nastavení** > **Vytvořit nový**. ![Vytvořit nový](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. Zadejte název veřejné adresy, zvolte výchozí možnosti **skladové položky** a **přiřazení**a klepněte na tlačítko **OK**.
6. Chcete-li provedené změny uložit, klepněte na tlačítko **Uložit**.
7. Zavřete panely a přejděte do části **Přehled** virtuálního počítače pro připojení/ PRV.

## <a name="unable-to-connectrdpssh---vm-connect-button-available"></a>Nelze se připojit/RDP/SSH - tlačítko Připojení virtuálního měsícího virtuálního připojení je k dispozici.

Pokud je tlačítko **Připojit** na virtuálním počítači s převzetím služby microsoft em it v Azure dostupné (není zašedlé), zkontrolujte **diagnostiku spouštění** ve virtuálním počítači a zkontrolujte chyby uvedené v [tomto článku](../virtual-machines/windows/boot-diagnostics.md).

1. Pokud se virtuální počítač nespustil, zkuste převzetí služby při selhání do staršího bodu obnovení.
2. Pokud aplikace uvnitř virtuálního počítače není nahoru, zkuste převzetí při selhání k bodu obnovení konzistentní aplikace.
3. Pokud je virtuální počítač připojen k doméně, ujistěte se, že řadič domény funguje přesně. To lze provést pomocí následujících kroků:

    a. Vytvořte nový virtuální počítač ve stejné síti.

    b.  Ujistěte se, že je možné připojit ke stejné doméně, na kterém se očekává, že převzetí služby ve selhání přes virtuální počítač přijít.

    c. Pokud řadič domény **nefunguje** přesně, zkuste se přihlásit k převzetí počítače s tím, že se jedná o virtuální počítač, který se nezdařil, pomocí účtu místního správce.
4. Pokud používáte vlastní server DNS, ujistěte se, že je dostupný. To lze provést pomocí následujících kroků:

    a. Vytvořte nový virtuální počítač ve stejné síti a

    b. Zkontrolujte, jestli je virtuální počítač schopný provést překlad názvů pomocí vlastního serveru DNS

>[!Note]
>Povolení jakéhokoli jiného nastavení než diagnostiky spouštění by vyžadovalo, aby se agent virtuálního počítače Azure nainstaloval do virtuálního počítače před převzetím služeb při selhání.

## <a name="unable-to-open-serial-console-after-failover-of-a-uefi-based-machine-into-azure"></a>Nelze otevřít sériovou konzolu po převzetí služeb při selhání počítače založeného na UEFI do Azure

Pokud se můžete připojit k počítači pomocí rdp, ale nemůžete otevřít konzolu sériového serveru, postupujte podle následujících kroků:

* Pokud je operační systém počítače Red Hat nebo Oracle Linux 7.*/8.0, spusťte následující příkaz na převzetí služeb při selhání virtuálního počítače Azure s oprávněními root. Restartujte virtuální počítač po příkazu.

        grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg

* Pokud je operační systém počítače CentOS 7.*, spusťte následující příkaz na převzetí služeb při selhání virtuálního počítače Azure s oprávněními root. Restartujte virtuální počítač po příkazu.

        grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg

## <a name="unexpected-shutdown-message-event-id-6008"></a>Zpráva o neočekávaném vypnutí (ID události 6008)

Při spouštění převzetí služeb při selhání virtuálního počítače systému Windows, pokud se zobrazí neočekávaná zpráva o vypnutí na obnoveném virtuálním počítači, znamená to, že stav vypnutí virtuálního počítače nebyl zachycen v bodě obnovení používaném pro převzetí služeb při selhání. K tomu dochází, když se obnovíte do bodu, kdy virtuální ho disponiplu nebyl úplně vypnutý.

To obvykle není důvodem k obavám a může být obvykle ignorována pro neplánované převzetí služeb při selhání. Pokud se plánuje převzetí služeb při selhání, ujistěte se, že virtuální počítač je správně vypnout před převzetí služeb při selhání a poskytnout dostatek času pro čekající replikace data v místním prostředí, které mají být odeslány do Azure. Pak použijte **možnost Nejnovější** na obrazovce převzetí služeb při [selhání](site-recovery-failover.md#run-a-failover) tak, aby všechna čekající data v Azure se zpracovala do bodu obnovení, který se pak používá pro převzetí služeb při selhání virtuálního počítače.

## <a name="unable-to-select-the-datastore"></a>Nelze vybrat úložiště dat.

Tento problém je určen, když se vám nedaří zobrazit úložiště dat v Azure portálu při pokusu o opětovné nahrazení virtuálního počítače, u kterého došlo k převzetí služeb při selhání. Důvodem je, že hlavní cíl není rozpoznán jako virtuální počítač v rámci vCenters přidané do Azure Site Recovery.

Další informace o opětovné ochraně vituálního počítače najdete v tématu [Reprotect a fail back počítače na místní web po převzetí služeb při selhání do Azure](vmware-azure-reprotect.md).

Chcete-li tento problém vyřešit:

Ručně vytvořte hlavní cíl v centru, který spravuje zdrojový počítač. Úložiště dat bude k dispozici po další operace zjišťování vCenter a aktualizace prostředků infrastruktury.

> [!Note]
> 
> Operace zjišťování a aktualizace prostředků infrastruktury může trvat až 30 minut. 

## <a name="linux-master-target-registration-with-cs-fails-with-an-ssl-error-35"></a>Registrace hlavního cíle Linuxu s CS se nezdaří s chybou SSL 35 

Registrace cíle hlavního webu Azure site recovery s konfiguračním serverem se nezdaří z důvodu povolení ověřeného proxy serveru v hlavním cíli. 
 
Tato chyba je označena následujícími řetězci v protokolu instalace: 

```
RegisterHostStaticInfo encountered exception config/talwrapper.cpp(107)[post] CurlWrapper Post failed : server : 10.38.229.221, port : 443, phpUrl : request_handler.php, secure : true, ignoreCurlPartialError : false with error: [at curlwrapperlib/curlwrapper.cpp:processCurlResponse:231]   failed to post request: (35) - SSL connect error. 
```

Chcete-li tento problém vyřešit:
 
1. Na virtuálním počítači konfiguračního serveru otevřete příkazový řádek a ověřte nastavení serveru proxy pomocí následujících příkazů:

    kočka /etc/prostředí echo $http_proxy echo $https_proxy 

2. Pokud výstup předchozích příkazů ukazuje, že jsou definovány nastavení http_proxy nebo https_proxy, odblokujte komunikaci hlavního cíle s konfiguračním serverem jedním z následujících způsobů:
   
   - Stáhněte si [nástroj PsExec](https://aka.ms/PsExec).
   - Pomocí nástroje můžete přistupovat k kontextu systémového uživatele a určit, zda je nakonfigurována adresa proxy. 
   - Pokud je proxy server nakonfigurován, otevřete aplikaci IE v kontextu systémového uživatele pomocí nástroje PsExec.
  
     **psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"**

   - Chcete-li zajistit, aby hlavní cílový server mohl komunikovat s konfiguračním serverem:
  
     - Upravte nastavení serveru proxy v aplikaci Internet Explorer tak, aby obejití ip adresy hlavního cílového serveru prostřednictvím serveru proxy.   
     Nebo
     - Zakažte proxy server na serveru Hlavní cíl. 


## <a name="next-steps"></a>Další kroky
- Poradce při potížích s [připojením RDP k virtuálnímu montovně systému Windows](../virtual-machines/windows/troubleshoot-rdp-connection.md)
- Poradce při potížích s [připojením SSH k virtuálnímu počítači SSH](../virtual-machines/linux/detailed-troubleshoot-ssh-connection.md)

Pokud potřebujete další pomoc, pak po váš dotaz na [fóru site recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) nebo zanechat komentář na konci tohoto dokumentu. Máme aktivní komunitu, která by vám měla být schopna pomoci.
