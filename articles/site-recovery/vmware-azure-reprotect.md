---
title: Znovu nastavte ochranu virtuálních počítačů z Azure na místní web | Microsoft Docs
description: Po převzetí služeb při selhání virtuálních počítačů do Azure můžete zahájit navrácení služeb po obnovení a dovést tak virtuální počítače zpět do místní. Naučte se znovu nastavte ochranu před navrácení služeb po obnovení.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: rajanaki
ms.openlocfilehash: aec87625cd00b11b41c248eecf7af650ae57036f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="reprotect-machines-from-azure-to-an-on-premises-site"></a>Znovu nastavte ochranu počítačů z Azure do místního serveru

Po [převzetí služeb při selhání](site-recovery-failover.md) místní virtuální počítače VMware nebo fyzických serverů do Azure, je prvním krokem při selhání zpátky do místního serveru k nastavení opětné virtuálních počítačích Azure, který vytvoří během převzetí služeb při selhání. Tento článek popisuje, jak to provést. 

Pro rychlý přehled najdete v následujícím videu o tom, jak převzetí služeb při selhání z Azure na místní lokalitu.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="before-you-start"></a>Než začnete

Pokud jste použili šablonu pro vytvoření virtuálního počítače, ujistěte se, že každý virtuální počítač má svou vlastní UUID pro disky. Pokud UUID místní virtuální počítač je v konfliktu s hlavního cíle, protože obě byly vytvořeny ze stejné šablony, nové provedení ochrany se nezdaří. Nasaďte další hlavní cíl, který ještě nebyl vytvořen ze stejné šablony.
- Pokud se pokoušíte navrácení služeb po obnovení alternativní vCenter, ujistěte se, že budou zjišťována vaší nové vCenter a hlavní cílový server. Typické symptomem je, že datastores nejsou přístupné nebo viditelné v **znovu nastavte ochranu** dialogové okno.
- K replikaci zpět na místní, budete potřebovat zásadu navrácení služeb po obnovení. Tato zásada získat automaticky vytvoří při vytváření zásady směru vpřed. Poznámky:
    - Tato zásada získá automaticky během vytváření přidružené k konfigurační server.
    - Tato zásada se nedá upravovat.
    - Jsou nastavené hodnoty zásad (RPO prahová hodnota = 15 minut, uchování bodu obnovení = 24 hodin, frekvence snímků konzistence aplikace = 60 minut)
- Při vytvoření a navrácení služeb po obnovení místní konfigurační server by měl být spuštěn a připojen.
- Pokud vCenter server spravuje virtuální počítače, na kterém jste budete navrácení služeb po obnovení Ujistěte se, abyste měli [požadovaná oprávnění](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) zjišťování virtuálních počítačů na servery vCenter server.
- Odstraňte snímky na hlavním cílovém serveru, než můžete znovu nastavte ochranu. Pokud snímky se nacházejí na místní hlavní cílový nebo virtuální počítač, nové provedení ochrany se nezdaří. Snímky virtuálního počítače automaticky sloučí během úloh opětovné ochrany.
- Všechny virtuální počítače skupiny replikace by měla být stejný typ operačního systému (všechny verze Windows nebo Linux všechny). Replikační skupina s smíšený operačních systémů se aktuálně nepodporuje pro opětovné ochrany a navrácení služeb po obnovení místně. Je to proto, že se hlavní cíl by měl být stejný operační systém jako virtuální počítač a všechny virtuální počítače replikační skupiny musí mít stejný hlavní cíl. 
- Konfigurační server je místní, když jste navrácení služeb po obnovení. Virtuální počítač musí během navrácení služeb po obnovení, neexistuje v databázi konfigurací serveru. Jinak navrácení služeb po obnovení neúspěšné. Zajistěte, aby podniknout pravidelně naplánovaných záloh konfigurace serveru. Pokud dojde k havárii, obnovení tak, aby funguje navrácení služeb po obnovení serveru se stejnou IP adresu.
- Vytvoření a navrácení služeb po obnovení vyžadují site-to-site VPN chcete replikovat data. Zadejte síť tak, aby virtuální počítače při selhání v Azure může kontaktovat (ping) konfigurace místní server. Můžete také nasadit procesový server v Azure sítě virtuálního počítače při selhání. Tento proces server by měl být také schopné komunikovat s místní konfigurační server.
- Ujistěte se, že otevřít následující porty pro převzetí služeb při selhání a navrácení služeb po obnovení.

    ![Porty pro převzetí služeb při selhání a navrácení služeb po obnovení](./media/vmware-azure-reprotect/failover-failback.png)

- Můžete si přečíst všechny požadavky na porty a adresy URL povolených [sem](vmware-azure-deploy-configuration-server.md#prerequisites)

## <a name="deploy-a-process-server-in-azure"></a>Nasadit procesový server v Azure

Můžete potřebovat procesový server v Azure předtím, než se vrátí zpátky na místní lokalitu:
- Procesový server] přijímá data z chráněného virtuálního počítače v Azure a odesílá data do místního webu.
- Síť s nízkou latencí je vyžadován mezi procesovým serverem a chráněného virtuálního počítače. Obecně platí je potřeba zvážit latence při rozhodování, zda je nutné procesní server v Azure:
    - Pokud máte nastavení připojení ExpressRoute, můžete k odesílání dat, protože je nízkou latenci mezi virtuálním počítačem a serverem proces serveru místní proces.
    - Pokud máte pouze S2S VPN, doporučujeme však nasazení procesní server v Azure.
    - Doporučujeme používat využitím Azure procesového serveru během navrácení služeb po obnovení. Replikace výkon je vyšší, pokud je procesový server blíže k replikaci virtuálního počítače (počítače při selhání v Azure). Ověření – koncepce můžete na místním serveru proces společně s ExpressRoute s soukromého partnerského vztahu.

Nasaďte takto:

1. Pokud potřebujete nasadit procesový server v Azure, postupujte podle [tyto pokyny](vmware-azure-set-up-process-server-azure.md)
2. Virtuální počítače Azure bude posílat data replikace na procesní server. Konfigurace sítě tak, aby virtuální počítače Azure můžete připojit k procesu serveru.
3. Mějte na paměti, že replikace z Azure do místní může dojít pouze přes S2S VPN nebo přes privátní partnerský vztah ExpressRoute sítě. Ujistěte se, že dostatečná šířka pásma je k dispozici v daném kanálu sítě.

## <a name="deploy-a-separate-master-target-server"></a>Nasazení samostatné hlavní cílový server

Hlavní cílový server přijímá data navrácení služeb po obnovení. Ve výchozím nastavení běží hlavní cílový server na místním konfiguračním serveru. V závislosti na objem provozu zpět se nezdařilo, však může potřebovat vytvořit samostatné hlavní cílový server navrácení služeb po obnovení. Chcete-li vytvořit:

    * [Vytvořit hlavní cílový server Linux](vmware-azure-install-linux-master-target.md) navrácení služeb po obnovení virtuálních počítačů Linux. To je potřeba.
    * Volitelně vytvořte samostatné hlavní cílový server navrácení služeb po obnovení virtuálního počítače s Windows. K tomu, znovu spusťte instalační program Unified a vytvořit hlavní cílový server. [Další informace](physical-azure-set-up-source.md#run-azure-site-recovery-unified-setup).

Po vytvoření hlavní cílový server, postupujte takto:

- Pokud je virtuální počítač existuje místně na serveru vCenter server, hlavní cílový server potřebuje přístup k VMDK na místním virtuálním počítači. Je nutný přístup k zápisu replikovaná data na disky virtuálního počítače. Zajistěte, aby je připojené úložiště na místním virtuálním počítači na hostiteli se hlavní cíl s přístupem pro čtení a zápis.
- Pokud virtuální počítač není přítomen místně na serveru vCenter server, je potřeba vytvořit nový virtuální počítač při vytvoření služby Site Recovery. Tento virtuální počítač se vytvoří na hostiteli ESX, ve kterém vytvoříte na hlavním cíli. Vyberte hostitele ESX pečlivě, tak, aby na hostiteli, kterého chcete, aby se vytvoří navrácení služeb po obnovení virtuálního počítače.
- Řešení Storage vMotion nelze použít pro hlavní cílový server *. To může způsobit navrácení služeb po obnovení nezdaří. Virtuální počítač nelze spustit, protože disky nejsou dostupné. K tomuto problému předešli, vylučte ze seznamu řešení vMotion hlavních cílových serverů.
- Pokud hlavní cíl zde nevyskytlo úlohu řešení vMotion úložiště po vytvoření, disky chráněných virtuálních počítačů, které jsou připojené k hlavnímu cíli migrovat do cílové řešení vMotion úlohy. Pokud se pokusíte zpět po selhat, odpojení disku nezdaří, protože nebyly nalezeny disky. Pak stává vyhledání disky v účty úložiště. Musíte je ručně vyhledat a připojte je k virtuálnímu počítači. Potom lze spustit na místním virtuálním počítači.
- Přidáním jednotky uchovávání dat na existující hlavní cílový server systému Windows. Přidejte nový disk a formátování disku. Jednotky pro uchovávání dat se používá k zastavení body v čase, kdy se mají replikovat virtuální počítač zpět do místního webu. Toto jsou některé kritéria jednotka pro uchování. Bez těchto kritérií jednotku nebude v seznamu uveden pro hlavní cílový server.
    - Svazek není použít k žádnému jinému účelu, jako je cílem replikace.
    - Svazek není v režimu zamknutí.
    - Svazek není svazek mezipaměti. Na tomto svazku by neměla být instalace hlavní cíl. Vlastní instalace svazek pro cílový server a hlavní proces nejsou vhodné pro svazek pro uchovávání. Když procesový server a hlavní cíl jsou nainstalovány na svazek, svazek je svazek mezipaměti hlavního cíle.
    - Typ systému souborů svazku není FAT nebo FAT32.
    - Kapacita svazku je nenulové hodnoty.
    - Svazek pro uchovávání dat výchozí pro Windows je svazek R.
    - Výchozí svazek pro uchovávání dat pro Linux je /mnt/retention.
- Je nutné přidat na nový disk, pokud používáte existující počítač proces serveru nebo konfigurační server nebo škálování nebo proces serveru/hlavní cílový server počítač. Nový disk by měl splňovat požadavky na předchozí. Pokud jednotka pro uchování není dostupná, nezobrazí se v rozevíracím seznamu výběru na portálu. Po přidání k hlavnímu cíli místní jednotku, bude trvat až 15 minut na jednotce, než se objeví ve výběru na portálu. Můžete taky aktualizovat konfigurační server, pokud jednotka nezobrazí po 15 minutách.
- Nainstalujte nástroje VMware na hlavním cílovém serveru. Bez nástroje VMware nelze zjistit datastores na hostiteli ESXi na hlavním cíli.
- Nastavte `disk.EnableUUID=true` nastavení v konfigurační parametry hlavního cílového virtuálního počítače v prostředí VMware. Pokud tento řádek neexistuje, přidejte ji. Toto nastavení je nutné k zajištění konzistentní UUID disku virtuálního počítače (VMDK) tak, aby ji připojí správně.
- Hostiteli ESX, ve které je vytvořena na hlavním cíli musí mít alespoň jeden VMFS úložiště k němu připojen. Pokud je none, **úložiště** vstup na stránce opětovné ochrany bude prázdný a nemůže pokračovat.
- Hlavní cílový server nemůže mít snímky na discích. Pokud existují snímky, vytvoření a navrácení služeb po obnovení nezdaří.
- Na hlavním cíli nemůže mít řadič Paravirtual SCSI. Kontroler může být pouze řadič LSI Logic. Bez řadič LSI Logic nové provedení ochrany se nezdaří.
- V dané instanci může mít hlavního cíle atmst 60 disky připojené k němu. Pokud počet virtuálních počítačů se znovu k hlavnímu cíli místní součet celkový počet víc než 60 disky a pak reprotects k hlavnímu cíli začne selhání. Ujistěte se, že máte dostatek hlavní cíl sloty disku nebo nasadit další hlavních cílových serverů.
    

## <a name="enable-reprotection"></a>Povolit nové provedení ochrany

Po spuštění virtuálního počítače v Azure, bude trvat nějakou dobu agenta k registraci zpět na konfigurační server (až 15 minut). Během této doby nebude moci znovu nastavte ochranu a chybová zpráva označuje, že agent není nainstalovaný. V takovém případě počkejte několik minut a opakujte akci vytvoření.


1. V **trezoru** > **replikované položky**, klikněte pravým tlačítkem na virtuální počítač, který při selhání a pak vyberte **znovu nastavit ochranu**. Můžete také klikněte na počítač a vyberte **znovu nastavit ochranu** z příkazová tlačítka.
2. Ověřte směr ochrany, **Azure k místnímu**, je již vybrána.
3. V **hlavní cílový Server** a **procesový Server**, vyberte místní hlavní cílový server a procesový server.  
4. Pro **úložiště**, vyberte úložiště dat, ke které chcete obnovit disky na místě. Tato možnost se používá při odstranění virtuálního počítače na místě, a budete muset vytvořit nové disky. Tato možnost je ignorována, pokud disky již existují, ale stále je třeba zadat hodnotu.
5. Vyberte jednotky pro uchovávání dat.
6. Automaticky se vyberou zásady navrácení služeb po obnovení.
7. Kliknutím na **OK** zahajte opětovné nastavování ochrany. Úloha začne replikovat virtuální počítač z Azure do místní lokality. Průběh můžete sledovat na kartě **Úlohy**. Poté, co vytvoření úspěšné, bude virtuální počítač zadejte chráněném stavu.

Poznámky:
- Pokud chcete obnovit do alternativního umístění (při odstranění virtuálního počítače místní), vyberte jednotka pro uchování a úložiště dat, které jsou nakonfigurované pro hlavní cílový server. Pokud žádnou zpět na místní lokalitu, virtuální počítače VMware v plánu ochrany navrácení služeb po obnovení použití stejné úložiště jako hlavní cílový server. Nový virtuální počítač se pak vytvoří v vCenter.
- Pokud chcete obnovit virtuální počítač na platformě Azure k existující místní virtuální počítač, připojte datastores místní virtuální počítač s přístupem pro čtení a zápis na hostiteli ESXi hlavní cílový server.
    ![Dialogové okno znovu nastavit ochranu](./media/vmware-azure-reprotect/reprotectinputs.png)

- Znovu nastavte ochranu můžete také na úrovni plánu obnovení. Replikační skupiny můžete reprotected pouze prostřednictvím plán obnovení. Pokud jste znovu nastavte ochranu pomocí plán obnovení, budete muset zadat hodnoty pro každý chráněný počítač.
- K znovunastavení ochrany replikační skupiny použijte stejný hlavní cílový server. Pokud k znovunastavení ochrany replikační skupiny použijete jiný hlavní cílový server, nemůže server zajistit společný bod v čase.
- Místní virtuální počítač je vypnutý během vytvoření. To pomáhá zajistit konzistenci dat během replikace. Nezapínejte virtuálního počítače, po dokončení vytvoření.



## <a name="common-issues"></a>Běžné problémy


- V současné době Site Recovery podporuje selhání zpět jenom na systém souborů virtuálního počítače (VMFS) nebo sítě vSAN úložiště. Úložiště dat systému souborů NFS se nepodporuje. Kvůli tomuto omezení zadejte výběr úložiště dat na opětovné ochrany obrazovky je prázdný v případě systému souborů NFS datastores nebo ukazuje s úložištěm síť vSAN, ale v průběhu úlohy nezdaří. Pokud máte v úmyslu navrácení služeb po obnovení, můžete vytvořit místní úložiště VMFS a navrácení služeb po obnovení je. Úplné stažení VMDK způsobí, že tento navrácení služeb po obnovení.
- Pokud provedení zjišťování vCenter uživatele jen pro čtení a ochrana virtuálních počítačů, je úspěšné ochrany a funguje převzetí služeb při selhání. Při vytvoření převzetí služeb při selhání se nezdaří, protože datastores nemůže být zjištěny. Příznakem je, že nejsou uvedené datastores během vytvoření. Chcete-li vyřešit tento problém, můžete aktualizovat pověření vCenter odpovídajícímu účtu, který má oprávnění a opakujte úlohu. 
- Při navrácení služeb po obnovení virtuální počítač s Linuxem a spusťte ho místně, uvidíte, že byl balíček správce sítě odinstalován z počítače. Tato odinstalace se stane, protože balíček správce sítě odebrán, pokud je virtuální počítač obnovit v Azure.
- Když virtuální počítač s Linuxem je nakonfigurovaný se statickou IP adresou a při selhání do Azure, je ze serveru DHCP získali IP adresu. Při selhání do místní virtuální počítač dál používat službu DHCP získat IP adresu. Ručně Přihlaste se k počítači a nastavit IP adresu zpět na statickou adresu v případě potřeby. Virtuálního počítače s Windows můžete znovu získat jeho statickou IP adresu.
- Pokud používáte edice free ESXi 5.5 nebo edice free Hypervisor vSphere 6, převzetí služeb při selhání úspěšné, ale navrácení služeb po obnovení nebude úspěšná. Chcete-li povolit navrácení služeb po obnovení, upgradujte na buď program zkušební licence.
- Pokud nelze kontaktovat konfigurační server z procesového serveru, použijte Telnet zkontrolujte připojení k serveru configuration na portu 443. Můžete také zkusit na příkaz ping konfigurační server z procesového serveru. Procesový server musí být také prezenční signál při připojení k serveru configuration.
- Server Windows Server 2008 R2 SP1, který je chráněn jako fyzický místní server nemůže být zpět z Azure místní webového serveru se nezdařil.
- Nelze označit zpět v následujících případech:
    - Počítače můžete migrovat na Azure. [Další informace](migrate-overview.md#what-do-we-mean-by-migration).
    - Virtuální počítač můžete přesunout do jiné skupiny prostředků.
    - Můžete odstranit virtuální počítač Azure.
    - Jste zakázali ochrany virtuálního počítače.
    - Virtuální počítač jste vytvořili ručně v Azure. Tento počítač by měl mít byla původně chráněném místní a převzetí služeb při selhání do Azure před opětovné ochrany.
    - Lze pouze neúspěšné do hostitele ESXi. Nemůžete navrácení služeb po obnovení virtuálních počítačů VMware nebo fyzických serverů na hostitele Hyper-V, fyzických počítačů a pracovních stanic VMware.


## <a name="next-steps"></a>Další postup

Po zadání chráněném stavu virtuálního počítače můžete [zahájení navrácení služeb po obnovení](vmware-azure-failback.md). Navrácení služeb po obnovení se vypnout virtuální počítač v Azure a místní virtuální počítač spustit. Očekávají menšímu výpadku pro aplikaci. Vyberte čas pro navrácení služeb po obnovení, pokud aplikace může tolerovat výpadku.

