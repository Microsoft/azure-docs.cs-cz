---
title: Znovu nastavte ochranu virtuálních počítačů VMware nebo fyzických serverů na místní lokalitu pomocí Azure Site Recovery
description: Po převzetí služeb při selhání do Azure během zotavení po havárii virtuálních počítačů VMware a fyzických serverů se dozvíte, jak navrátit služby po obnovení z Azure do místní lokality.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: mayg
ms.openlocfilehash: f3d5f38d940b99c6a74d784f174c91d4127353dc
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961344"
---
# <a name="reprotect-and-fail-back-machines-to-an-on-premises-site-after-failover-to-azure"></a>Po převzetí služeb při selhání do Azure znovu nastavit ochranu a navrácení počítačů po obnovení do místní lokality

Po [převzetí služeb při selhání](site-recovery-failover.md) místních virtuálních počítačů VMware nebo fyzických serverů do Azure je prvním krokem při selhání zpět na místní lokalitu, aby se znovu chránily virtuální počítače Azure vytvořené během převzetí služeb při selhání. Tento článek popisuje, jak to provést. 

Rychlý přehled najdete v následujícím videu o tom, jak převzít služby při selhání z Azure do místní lokality.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="before-you-begin"></a>Než začnete

Pokud jste pro vytváření virtuálních počítačů použili šablonu, ujistěte se, že každý virtuální počítač má vlastní UUID pro tyto disky. Pokud je identifikátor UUID místního virtuálního počítače v konfliktu s identifikátorem UUID hlavního cíle, protože oba byly vytvořeny ze stejné šablony, změna ochrany se nezdařila. Nasaďte další hlavní cíl, který se nevytvořil ze stejné šablony. Všimněte si následujících informací:
- Pokud se pokoušíte navrátit služby po obnovení na alternativní vCenter, ujistěte se, že je nový Server vCenter a hlavní cílový server zjištěny. Typickým příznakem je, že úložiště dat nejsou přístupná nebo nejsou viditelná v dialogovém okně opětovné **ochrany** .
- K replikaci zpátky do místního prostředí budete potřebovat zásadu navrácení služeb po obnovení. Tato zásada se vytvoří automaticky při vytvoření zásady pro směr směrování. Všimněte si následujících informací:
    - Tato zásada je během vytváření automaticky přidružená ke konfiguračnímu serveru.
    - Tato zásada není upravitelná.
    - Nastavené hodnoty zásad jsou (prahová hodnota cíle RPO = 15 minut, uchování bodu obnovení = 24 hodin, frekvence snímků konzistence aplikací = 60 minut).
- Během opětovné ochrany a navrácení služeb po obnovení musí být místní konfigurační server spuštěný a připojený.
- Pokud Server vCenter spravuje virtuální počítače, na které se vrátíte navrácení služeb po obnovení, ujistěte se, že máte [požadovaná oprávnění](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) pro zjišťování virtuálních počítačů na serverech vCenter.
- Než znovu nastavíte ochranu, odstraňte snímky na hlavním cílovém serveru. Pokud se snímky nacházejí na místním hlavním cíli nebo na virtuálním počítači, znovu se ochrana nezdařila. Snímky na virtuálním počítači se automaticky sloučí během úlohy opětovného zapnutí ochrany.
- Všechny virtuální počítače skupiny replikace musí být stejného typu operačního systému (buď všechny systémy Windows, nebo všechny systémy Linux). Replikační skupina se smíšenými operačními systémy aktuálně není podporovaná pro opětovné zapnutí ochrany a navrácení služeb po obnovení do místního prostředí. Důvodem je to, že hlavní cíl musí být stejného operačního systému jako virtuální počítač. Všechny virtuální počítače skupiny replikace musí mít stejný hlavní cíl. 
- Hlavní cíl musí mít stejnou nebo vyšší verzi operačního systému, než je verze operačního systému replikovaných položek.
- Při navrácení služeb po obnovení je konfigurační server vyžadován v místním prostředí. Během navrácení služeb po obnovení musí virtuální počítač existovat v databázi konfiguračního serveru. V opačném případě je navrácení služeb po obnovení neúspěšné. Ujistěte se, že provádíte pravidelné naplánování záloh konfiguračního serveru. Pokud dojde k havárii, obnovte server se stejnou IP adresou, aby navrácení služeb po obnovení fungovalo. 
- Opětovné navýšení a navrácení služeb po obnovení vyžaduje pro replikaci dat privátní partnerské vztahy typu Site-to-Site (S2S) VPN nebo ExpressRoute. Poskytněte síť, aby se virtuální počítače s podporou převzetí služeb při selhání v Azure dostaly k místnímu konfiguračnímu serveru (otestujete ho). Je potřeba nasadit procesový Server v síti Azure u virtuálních počítačů, které selhaly při převzetí služeb při selhání. Tento procesový Server musí být také schopný komunikovat s místním konfiguračním serverem a hlavním cílovým serverem.
- V případě, že se IP adresy replikovaných položek zachovaly při převzetí služeb při selhání, je třeba vytvořit připojení S2S nebo ExpressRoute mezi virtuálními počítači Azure a síťovou kartou pro navrácení služeb po obnovení pro konfigurační server. Mějte na paměti, že uchovávání IP adres vyžaduje, aby konfigurační server měl dvě síťové karty – jeden pro připojení ke zdrojovým počítačům a jeden pro připojení k Azure navrácení služeb K tomu je potřeba zabránit překrytí rozsahů adres podsítí ve zdroji a při převzetí služeb při selhání virtuálních počítačů.
- Ujistěte se, že otevřete následující porty pro převzetí služeb při selhání a navrácení služeb po obnovení:

    ![Porty pro převzetí služeb při selhání a obnovení](./media/vmware-azure-reprotect/failover-failback.png)

- Přečtěte si všechny [požadavky na porty a seznam povolených adres URL](vmware-azure-deploy-configuration-server.md#prerequisites).

## <a name="deploy-a-process-server-in-azure"></a>Nasazení procesového serveru v Azure

Před navrácením služeb po obnovení do místní lokality budete potřebovat procesový Server v Azure:

- Procesový server přijímá data z chráněných virtuálních počítačů v Azure a poté odesílá data do místní lokality.
- Mezi procesovým serverem a chráněným virtuálním počítačem se vyžaduje síť s nízkou latencí. Proto se doporučuje nasadit procesový Server v Azure. Výkon replikace je vyšší, pokud je procesový Server přiblížný k replikačnímu virtuálnímu počítači (počítač s podporou převzetí služeb při selhání v Azure). 
- Pro zkoušku konceptu můžete použít místní procesový Server a ExpressRoute se soukromým partnerským vztahem.

Postup nasazení procesového serveru v Azure:

1. Pokud potřebujete nasadit procesový Server v Azure, přečtěte si téma [Nastavení procesového serveru v Azure pro navrácení služeb po obnovení](vmware-azure-set-up-process-server-azure.md).
2. Virtuální počítače Azure odesílají data replikace na procesový Server. Nakonfigurujte sítě tak, aby se virtuální počítače Azure mohly dostat na procesový Server.
3. Pamatujte, že replikace z Azure do místního prostředí se může provádět jenom přes S2S VPN nebo přes privátní partnerský vztah vaší ExpressRoute sítě. Ujistěte se, že je přes tento síťový kanál k dispozici dostatečná šířka pásma.

## <a name="deploy-a-separate-master-target-server"></a>Nasazení samostatného hlavního cílového serveru

Hlavní cílový server přijímá data navrácení služeb po obnovení. Ve výchozím nastavení běží hlavní cílový server na místním konfiguračním serveru. V závislosti na objemu neúspěšných přenosů ale možná budete muset vytvořit samostatný hlavní cílový server pro navrácení služeb po obnovení. Tady je postup, jak ho vytvořit:

* [Vytvořte hlavní cílový server se systémem Linux](vmware-azure-install-linux-master-target.md) pro navrácení služeb po obnovení virtuálních počítačů se systémem Linux. To je povinné. Všimněte si, že hlavní cílový server na LVM není podporován.
* Volitelně můžete vytvořit samostatný hlavní cílový server pro navrácení služeb po obnovení virtuálního počítače s Windows. Provedete to tak, že znovu spustíte Unified Setup a vyberete vytvořit hlavní cílový server. [Další informace](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers) 

Po vytvoření hlavního cílového serveru proveďte následující úlohy:

- Pokud je virtuální počítač přítomen místně na serveru vCenter, hlavní cílový server potřebuje přístup k souboru virtuálního počítače (VMDK) místního virtuálního počítače. K zápisu replikovaných dat na disky virtuálního počítače je vyžadován přístup. Ujistěte se, že úložiště dat místního virtuálního počítače je připojené k hostiteli hlavního cíle s přístupem pro čtení a zápis.
- Pokud virtuální počítač není na serveru vCenter místně přítomen, musí služba Site Recovery při opětovné ochraně vytvořit nový virtuální počítač. Tento virtuální počítač se vytvoří na hostiteli ESX, na kterém jste vytvořili hlavní cíl. Vyberte hostitele ESX pečlivě, aby bylo možné virtuální počítač pro navrácení služeb po obnovení vytvořit na hostiteli, který chcete.
- VMotion úložiště nemůžete použít pro hlavní cílový server. Použití úložiště vMotion pro hlavní cílový server může způsobit selhání navrácení služeb po obnovení. Virtuální počítač nelze spustit, protože disky nejsou k dispozici. Aby k tomu nedošlo, vylučte hlavní cílové servery ze seznamu vMotion.
- Pokud hlavní cíl po reochraně doprovází úlohu vMotion úložiště, budou chráněné disky virtuálních počítačů, které jsou připojené k hlavnímu cíli, migrovány do cíle úlohy vMotion. Pokud se po této akci pokusíte o navrácení služeb po obnovení, odpojení disku se nezdaří, protože disky nebyly nalezeny. Pak je obtížné najít disky ve vašich účtech úložiště. Je nutné je vyhledat ručně a připojit je k virtuálnímu počítači. Potom můžete místní virtuální počítač spustit znovu.
- Přidejte jednotku pro uchování do stávajícího hlavního cílového serveru Windows. Přidejte nový disk a naformátujte jednotku. Jednotka pro uchovávání dat se používá k zastavení bodů v čase, kdy se virtuální počítač replikuje zpátky na místní lokalitu. Níže jsou uvedená některá kritéria jednotky pro uchovávání dat. Pokud tato kritéria nejsou splněná, jednotka není uvedená pro hlavní cílový server:
    - Svazek se nepoužívá pro žádný jiný účel, jako je například cíl replikace.
    - Svazek není v režimu zámku.
    - Svazek není svazkem mezipaměti. Instalace hlavního cíle nemůže na tomto svazku existovat. Vlastní instalační svazek pro procesový Server a hlavní cíl nemá nárok na svazek pro uchovávání dat. Pokud jsou procesový Server a hlavní cíl nainstalované na svazku, je svazek svazek mezipaměti hlavního cíle.
    - Typ systému souborů svazku není FAT ani FAT32.
    - Kapacita svazku je nenulová.
    - Výchozím svazkem uchování pro Windows je svazek R.
    - Výchozí svazek pro uchovávání dat pro Linux je svazek/mnt/retention.
- Pokud používáte existující počítač procesového serveru/konfiguračního serveru nebo počítač se serverem nebo hlavním cílovým serverem, je nutné přidat novou jednotku. Nová jednotka musí splňovat předchozí požadavky. Pokud jednotka pro uchovávání dat není k dispozici, nezobrazí se v rozevíracím seznamu výběr na portálu. Po přidání jednotky do místního hlavního cíle může trvat až 15 minut, než se jednotka objeví ve výběru na portálu. Konfigurační server můžete aktualizovat i v případě, že se jednotka nezobrazí po 15 minutách.
- Na hlavním cílovém serveru nainstalujte nástroje VMware nebo otevřít-VM-Tools. Bez nástrojů nelze zjistit úložiště dat na hostiteli ESXi hlavního cíle.
- Nastavte `disk.EnableUUID=true` nastavení v parametrech konfigurace hlavního cílového počítače ve VMware. Pokud tento řádek neexistuje, přidejte jej. Toto nastavení je nutné k poskytnutí konzistentního identifikátoru UUID pro VMDK, aby bylo správně připojeno.
- Hostitel ESX, na kterém je vytvořen hlavní cíl, musí mít připojen alespoň jeden úložiště dat systému souborů VMFS (Virtual Machine File System). Pokud nejsou k dispozici žádná úložiště VMFS DataStore, vstup **úložiště dat** na stránce pro opětovné provedení ochrany je prázdný a nemůžete pokračovat.
- Hlavní cílový server nemůže obsahovat snímky na discích. Pokud jsou k dispozici snímky, dojde k selhání ochrany a navrácení služeb po obnovení.
- Hlavní cíl nemůže mít řadič SCSI Paravirtual. Kontroler může být jenom řadič LSI Logic. Bez kontroleru adaptéru LSI Logic se chyba ochrany nezdařila.
- V případě jakékoli instance může mít hlavní cíl nejvýše 60 disků připojených k němu. Pokud počet virtuálních počítačů, které mají být znovu chráněny na místní hlavní cíl, má více než celkový počet 60 disků, znovu se zapojte k hlavnímu cílovému serveru a zahajte selhání. Ujistěte se, že máte dostatek pozic pro hlavní cílový disk nebo nasaďte další hlavní cílové servery.
    

## <a name="enable-reprotection"></a>Povolení ochrany

Až se virtuální počítač spustí v Azure, může se agent nějaký čas zaregistrovat zpátky na konfigurační server (až 15 minut). Během této doby nebudete moci znovu zapnout ochranu a chybová zpráva indikuje, že agent není nainstalován. Pokud k tomu dojde, počkejte několik minut a pak zkuste znovu zapnout ochranu:


1. Vyberte **trezor** > **replikované položky**. Klikněte pravým tlačítkem na virtuální počítač, u kterého došlo k převzetí služeb při selhání, a pak vyberte **znovu zapnout ochranu**. Případně můžete z příkazových tlačítek vybrat počítač a pak vybrat možnost **znovu zapnout ochranu**.
2. Ověřte, že je vybraná možnost **Azure do místního** směru ochrany.
3. Na **hlavním cílovém serveru** a **procesovém serveru**vyberte místní hlavní cílový server a procesový Server.  
4. V části **úložiště dat**vyberte úložiště dat, do kterého chcete místně obnovit disky. Tato možnost se používá, když se místní virtuální počítač odstraní a potřebujete vytvořit nové disky. Tato možnost je ignorována, pokud již disky existují. Stále musíte zadat hodnotu.
5. Vyberte jednotku pro uchovávání.
6. Automaticky se vyberou zásady navrácení služeb po obnovení.
7. Kliknutím na **tlačítko OK** zahajte znovu ochranu. Úloha začne replikovat virtuální počítač z Azure do místní lokality. Průběh můžete sledovat na kartě **úlohy** . Po úspěšném provedení ochrany se virtuální počítač dostane do chráněného stavu.

Všimněte si následujících informací:
- Pokud chcete provést obnovení do alternativního umístění (když se místní virtuální počítač odstraní), vyberte jednotku pro uchovávání dat a úložiště dat, které jsou nakonfigurované pro hlavní cílový server. Po navrácení služeb po obnovení do místní lokality používá virtuální počítače VMware v plánu ochrany navrácení služeb po obnovení stejné úložiště dat jako hlavní cílový server. V vCenter se pak vytvoří nový virtuální počítač.
- Pokud chcete obnovit virtuální počítač v Azure na stávající místní virtuální počítač, připojte úložiště dat místního virtuálního počítače s přístupem pro čtení a zápis na hostiteli ESXi hlavního cílového serveru.

    ![Dialogové okno opětovného zapnutí ochrany](./media/vmware-azure-reprotect/reprotectinputs.png)

- Můžete také znovu zapnout ochranu na úrovni plánu obnovení. Replikační skupina se dá znovu chránit jenom prostřednictvím plánu obnovení. Při opětovném zapnutí ochrany pomocí plánu obnovení je nutné zadat hodnoty pro každý chráněný počítač.
- K znovunastavení ochrany replikační skupiny použijte stejný hlavní cílový server. Pokud k opětovné ochraně replikační skupiny použijete jiný hlavní cílový server, server nemůže poskytnout společný bod v čase.
- Místní virtuální počítač je během opětovné ochrany vypnutý. To pomáhá zajistit konzistenci dat během replikace. Po dokončení ochrany neměňte virtuální počítač.



## <a name="common-issues"></a>Běžné problémy

- Pokud provádíte zjišťování vCenter uživatele, který je jen pro čtení, a ochranu virtuálních počítačů, ochrana bude úspěšná a funguje převzetí služeb při selhání. Během ochrany dojde k selhání, protože úložiště dat nelze zjistit. Příznakem je, že úložiště dat nejsou uvedena během opakované ochrany. Chcete-li tento problém vyřešit, můžete aktualizovat přihlašovací údaje pro vCenter pomocí odpovídajícího účtu, který má oprávnění, a potom úlohu opakovat. 
- Při navrácení služeb po obnovení virtuálního počítače se systémem Linux a jeho spuštění v místním prostředí můžete zjistit, že byl balíček správce sítě z tohoto počítače odinstalován. Tato odinstalace nastane, protože balíček správce sítě se odebere, když se virtuální počítač obnoví v Azure.
- Když je virtuální počítač se systémem Linux nakonfigurovaný se statickou IP adresou a převezme služby při selhání do Azure, IP adresa se získá z protokolu DHCP. Když převezmete služby při selhání do místní sítě, virtuální počítač bude nadále používat protokol DHCP k získání IP adresy. Ručně se přihlaste k počítači a v případě potřeby nastavte IP adresu zpět na statickou adresu. Virtuální počítač s Windows může znovu získat svoji statickou IP adresu.
- Pokud používáte buď bezplatnou edici ESXi 5,5 nebo edici vSphere 6 hypervisoru, převzetí služeb při selhání proběhne úspěšně, ale navrácení služeb po obnovení nebude úspěšné. Pokud chcete povolit navrácení služeb po obnovení, upgradujte na buď zkušební licenci programu.
- Pokud se nemůžete připojit ke konfiguračnímu serveru z procesového serveru, pomocí programu Telnet ověřte připojení ke konfiguračnímu serveru na portu 443. Můžete se také pokusit testovat konfigurační server z procesového serveru z procesu. Procesový Server by měl mít také prezenční signál, pokud je připojený ke konfiguračnímu serveru.
- Server s Windows Serverem 2008 R2 SP1, který je chráněný jako fyzický místní server, se nedá vrátit z Azure do místní lokality.
- Nemůžete navrátit služby po obnovení v následujících případech:
    - Migrovali jste počítače do Azure. [Další informace](migrate-overview.md#what-do-we-mean-by-migration)
    - Přesunuli jste virtuální počítač do jiné skupiny prostředků.
    - Odstranili jste virtuální počítač Azure.
    - Zakázali jste ochranu virtuálního počítače.
    - Virtuální počítač jste vytvořili ručně v Azure. Počítač by měl být zpočátku chráněný místně a před provedením ochrany se převzal do Azure.
    - Selhání můžete provést pouze pro hostitele ESXi. Nemůžete navrácení služeb po obnovení virtuálních počítačů VMware nebo fyzických serverů do hostitelů Hyper-V, fyzických počítačů nebo pracovních stanic VMware.


## <a name="next-steps"></a>Další kroky

Jakmile virtuální počítač zadáte chráněný stav, můžete [zahájit navrácení služeb po obnovení](vmware-azure-failback.md). Navrácení služeb po obnovení vypne virtuální počítač v Azure a spustí místní virtuální počítač. Pro aplikaci můžete očekávat nějaké výpadky. Vyberte si čas pro navrácení služeb po obnovení, když aplikace může tolerovat výpadky.


