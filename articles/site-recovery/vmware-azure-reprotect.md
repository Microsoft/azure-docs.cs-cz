---
title: Znovunastavení ochrany virtuálních počítačů z Azure do místní lokality | Dokumentace Microsoftu
description: Po převzetí služeb při selhání virtuálních počítačů do Azure můžete zahájit navrácení služeb po obnovení přenést virtuální počítače zpět do místní. Zjistěte, jak znovu nastavit ochranu před navrácení služeb po obnovení.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: rajanaki
ms.openlocfilehash: 04cb658da6a22643e21104f5673a3f211f48b7fc
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37916475"
---
# <a name="reprotect-machines-from-azure-to-an-on-premises-site"></a>Znovunastavení ochrany počítačů z Azure do místní lokality

Po [převzetí služeb při selhání](site-recovery-failover.md) místních virtuálních počítačů VMware nebo fyzických serverů do Azure, je prvním krokem při selhání, zpět do místní lokality k znovunastavení ochrany virtuálních počítačů Azure, které se vytvořily během převzetí služeb při selhání. Tento článek popisuje, jak to provést. 

Získejte rychlý přehled zhlédnout následující video o tom, jak převzetí služeb při selhání z Azure do místní lokality.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="before-you-begin"></a>Než začnete

Pokud jste použili k vytvoření virtuálních počítačů šablony, ujistěte se, že každý virtuální počítač má svůj vlastní identifikátor UUID disků. Pokud UUID na místním virtuálním počítači konfliktu s UUID hlavního cíle, protože oba byly vytvořeny ze stejné šablony, opětovného nastavování ochrany se nezdaří. Nasazení jiný hlavní cíl, který nebyl vytvořen ze stejné šablony. Všimněte si následujících informací:
- Pokud se pokoušíte navrácení služeb po obnovení alternativního serveru vCenter, ujistěte se, že se zjistí nové vCenter a hlavní cílový server. Typické symptomem je, že nejsou dostupné úložiště dat, nebo nejsou viditelné v **znovunastavení ochrany** dialogové okno.
- K replikaci zpět do místního, budete potřebovat zásady navrácení služeb po obnovení. Tato zásada se vytvoří automaticky při vytváření zásad směru dopředu. Všimněte si následujících informací:
    - Tato zásada je automaticky během vytváření přidružené s konfiguračním serverem.
    - Tato zásada nelze ho upravovat.
    - Nastavte hodnoty zásady jsou (prahová hodnota cíle bodu obnovení = 15 minut, uchování bodu obnovení = 24 hodin, frekvence pořizování snímků konzistence App = 60 minut).
- Během opětovného nastavování ochrany a navrácení služeb po obnovení musí být místní konfigurační server spuštěn a připojen.
- Pokud vCenter server spravuje virtuální počítače, na které je budete navrácení služeb po obnovení, ujistěte se, že máte [požadovaná oprávnění](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) pro zjišťování virtuálních počítačů na vCenter servery.
- Předtím, než můžete znovu nastavit ochranu, odstraňte snímky na hlavním cílovém serveru. Pokud nejsou k dispozici na na hlavním cíli v místním nebo na virtuální počítač snímky, opětovného nastavování ochrany se nezdaří. Snímky na virtuálním počítači se sloučit automaticky během úlohu znovunastavení ochrany.
- Všechny virtuální počítače skupiny replikace musí být stejného typu operačního systému (všechny Windows nebo Linuxem všechny). Replikační skupina s smíšené operačních systémů aktuálně není podporována pro operace opětovného zapnutí ochrany a navrácení služeb po obnovení do místní. Je to proto, že se hlavní cíl musí mít stejný operační systém jako virtuální počítač. Všechny virtuální počítače skupiny replikace musí mít stejný hlavní cíl. 
- Konfigurační server je požadovaná místní, při navrácení služeb po obnovení. Během navrácení služeb po obnovení musí existovat virtuální počítač v databázi konfiguračního serveru. Jinak navrácení služeb po obnovení neúspěšné. Ujistěte se, že budete pravidelně naplánovaných záloh konfiguračního serveru. Pokud dojde k havárii, obnovení tak, aby funguje navrácení služeb po obnovení serveru se stejnou IP adresou.
- Síť site-to-site (S2S) VPN k replikaci dat vyžadují, opětovného nastavování ochrany a navrácení služeb po obnovení. Zadejte síť tak, aby převzetím služeb při selhání virtuálních počítačů v Azure můžete získat přístup (ping) místní konfiguraci serveru. Můžete také nasadit procesový server v síti Azure převzetím služeb při selhání virtuálního počítače. Tento procesový server musí být také schopné komunikovat s místní konfigurační server.
- Ujistěte se, že otevřít následující porty pro převzetí služeb při selhání a navrácení služeb po obnovení:

    ![Porty pro převzetí služeb při selhání a navrácení služeb po obnovení](./media/vmware-azure-reprotect/failover-failback.png)

- Přečíst všechny [požadavky na porty a adresy URL na seznam povolených](vmware-azure-deploy-configuration-server.md#prerequisites).

## <a name="deploy-a-process-server-in-azure"></a>Nasazení procesového serveru v Azure

Procesový server v Azure může být nutné před převezmete služby zpět do místní lokality:
- Procesový server přijímá data z chráněného virtuálního počítače v Azure a potom odešle data do místní lokality.
- Síť s nízkou latencí je vyžadován mezi procesovým serverem a chráněného virtuálního počítače. Obecně platí je potřeba zvážit latence při rozhodování o tom, jestli potřebujete procesový server v Azure:
    - Pokud máte nastavení připojení Azure ExpressRoute, můžete použít místní procesový server k odesílání dat, protože je s nízkou latencí mezi virtuálním počítačem a procesovým serverem.
    - Nicméně pokud máte pouze S2S VPN, doporučujeme nasadit procesový server v Azure.
    - Doporučujeme používat založené na Azure procesový server během navrácení služeb po obnovení. Výkon replikace je vyšší, pokud je procesový server je blíž ke replikující se virtuální počítač (počítače převzetím služeb při selhání v Azure). Pro testování konceptu můžete použít místní procesový server a ExpressRoute pomocí soukromého partnerského vztahu.

Nasazení procesového serveru v Azure:

1. Pokud potřebujete nasadit procesový server v Azure, přečtěte si téma [nastavit procesový server v Azure pro navrácení služeb po obnovení](vmware-azure-set-up-process-server-azure.md).
2. Virtuální počítače Azure odesílají data do procesového serveru. Konfigurovat sítě tak, aby virtuální počítače Azure můžete oslovit na procesovém serveru.
3. Nezapomeňte, že replikace z Azure do místního může dojít pouze přes síť VPN typu S2S nebo přes privátní partnerský vztah ExpressRoute sítě. Ujistěte se, že je dostatečná šířka pásma dostupná přes tento síťový kanál.

## <a name="deploy-a-separate-master-target-server"></a>Nasadit samostatný hlavní cílový server

Hlavní cílový server přijímá data navrácení služeb po obnovení. Ve výchozím nastavení běží hlavní cílový server na místním konfiguračním serveru. V závislosti na tom, jaký objem přenosů se nezdařilo zpět, potřebujete ale vytvořit samostatný hlavní cílový server navrácení služeb po obnovení. Tady je postup pro vytvoření:

* [Vytvoření hlavního cílového serveru s Linuxem](vmware-azure-install-linux-master-target.md) navrácení služeb po obnovení virtuálních počítačů s Linuxem. Toto je povinné.
* Volitelně můžete vytvořte samostatný hlavní cílový server navrácení služeb po obnovení virtuálního počítače Windows. K tomuto účelu sjednocenou instalaci spusťte znovu a vyberte, chcete-li vytvořit hlavní cílový server. [Další informace](physical-azure-set-up-source.md#run-azure-site-recovery-unified-setup).

Jakmile vytvoříte hlavní cílový server, proveďte následující úkoly:

- Pokud je virtuální počítač k dispozici místně na serveru vCenter, hlavní cílový server potřebuje přístup k souboru na místním virtuálním počítači disku virtuálního počítače (VMDK). Zapisovat replikovaná data na disky virtuálního počítače je nutné mít přístup. Ujistěte se, že úložiště dat na místním virtuálním počítači je připojený na hlavním cílovém hostiteli s přístupem pro čtení a zápisu.
- Pokud virtuální počítač není k dispozici místně na serveru vCenter, služba Site Recovery je potřeba vytvořit nový virtuální počítač během opětovného nastavování ochrany. Tento virtuální počítač se vytvoří na hostiteli ESX, na kterém vytvoříte hlavní cíl. Pečlivě hostitele ESX, tak, aby na hostiteli, který chcete, aby se vytvoří navrácení služeb po obnovení virtuálního počítače.
- U hlavního cílového serveru nelze používat funkci Storage vMotion. Pomocí řešení Storage vMotion pro hlavní cílový server může způsobit, že navrácení služeb po obnovení selže. Virtuální počítač nelze spustit, protože disky nejsou k dispozici. Chcete-li tomu zabránit, vylučte hlavní cílové servery ze svého seznamu vMotion.
- Pokud hlavní cíl podroben úlohu Storage vMotion po opětovného nastavování ochrany, chráněný virtuální počítač disky připojené k hlavnímu cíli migrovat na cílový úkol vMotion. Pokud se pokusíte selhání zpět po odpojení disku se nezdaří, protože disky nebyly nalezeny. Pak bude obtížné najít disky v účtech úložiště. Musíte ručně vyhledat a připojit k virtuálnímu počítači. Potom můžete spustit na místním virtuálním počítači.
- Přidání jednotky pro uchovávání dat do vaší stávající hlavní cílový server Windows. Přidejte nový disk a formátování disku. Jednotka pro uchovávání dat slouží k zastavení body v čase, když se virtuální počítač replikuje zpět do místní lokality. Toto jsou některá kritéria jednotky pro uchovávání dat. Pokud se nesplní tyto kritéria, jednotka není uvedena v seznamu pro hlavní cílový server:
    - Svazek se nepoužívá k žádnému jinému účelu, jako je cílem replikace.
    - Svazek není v režimu uzamčení.
    - Svazek se svazkem mezipaměti. Instalace hlavního cílového nemůže existovat na tomto svazku. Svazek vlastní instalace pro cílový server a hlavní proces nemá nárok svazek pro uchovávání. Pokud procesový server a hlavní cílový jsou nainstalované na svazek, svazek je svazkem mezipaměti serveru na hlavním cíli.
    - Typ systému souborů svazku není systému souborů FAT nebo FAT32.
    - Kapacita svazku není nenulová.
    - Výchozí svazek pro uchovávání dat pro Windows je svazek r.
    - Výchozí svazek pro uchovávání dat pro Linux je /mnt/retention.
- Pokud používáte existující počítač proces serveru nebo konfigurační server nebo škálování nebo proces server nebo hlavní cílový server počítač je nutné přidat novou jednotku. Nová jednotka musí splňovat požadavky na předchozí. Pokud jednotka pro uchovávání dat není k dispozici, nezobrazí se v rozevíracím seznamu pro výběr na portálu. Po přidání disku do místní hlavní cílový trvá až 15 minut pro disk se zobrazí ve výběru na portálu. Pokud na jednotce není zobrazená po 15 minutách můžete taky aktualizovat konfigurační server.
- Nainstalujte nástroje VMware nebo nástroje open virtuální počítače na hlavním cílovém serveru. Bez nástrojů nelze zjistit úložiště na hostiteli ESXi hlavního cíle.
- Nastavte `disk.EnableUUID=true` nastavení v parametry konfigurace hlavního cílového virtuálního počítače v prostředí VMware. Pokud tento řádek neexistuje, přidejte ji. Toto nastavení je potřeba poskytnout konzistentní UUID vmdk tak, aby jej připojí správně.
- Hostiteli ESX, ke kterému se vytvoří na hlavním cíli musí mít alespoň jeden virtuální počítač souboru systému (VMFS) úložiště dat k němu připojená. Pokud jsou připojeny žádné VMFS úložišť, **Datastore** vstup na stránce Operace opětovného zapnutí ochrany je prázdný a nemůže pokračovat.
- Hlavní cílový server nemůže mít snímků na discích. Pokud existují snímky, opětovného nastavování ochrany a navrácení služeb po obnovení nezdaří.
- Na hlavním cíli nemůže mít řadiče Paravirtual SCSI. Kontroler může být pouze řadič Adaptér LSI Logic. Bez řadič Adaptér LSI Logic opětovného nastavování ochrany se nezdaří.
- Pro všechny instance se hlavní cíl může mít maximálně 60 disky připojené k němu. Pokud počet virtuálních počítačů, které se znovunastavení ochrany k hlavnímu cíli místní má větší než celkový počet 60 disky reprotects k hlavnímu cíli začnou být neúspěšné. Ujistěte se, že máte dostatek hlavní cíl sloty disku, nebo nasadit další hlavní cílové servery.
    

## <a name="enable-reprotection"></a>Povolit opětovného nastavování ochrany

Po spuštění virtuálního počítače v Azure, trvá nějakou dobu agenta k registraci zpět do konfiguračního serveru (až 15 minut). Během této doby nebude možné znovunastavení ochrany a chybová zpráva znamená, že agent není nainstalovaný. Pokud k tomu dojde, počkejte pár minut a pak zkuste znovu opětovného nastavování ochrany:


1. Vyberte **trezor** > **replikované položky**. Klikněte pravým tlačítkem na virtuální počítač, převzetí služeb při selhání a pak vyberte **znovu nastavit ochranu**. Nebo, z příkazového tlačítka, vyberte počítač a pak vyberte **znovu nastavit ochranu**.
2. Ověřte, že **Azure k místnímu** vybraný směr ochrany.
3. V **hlavního cílového serveru** a **procesový Server**, vyberte místní hlavní cílový server a procesový server.  
4. Pro **Datastore**, vyberte úložiště dat, ke kterému chcete obnovit disky v místním. Tato možnost se používá, když na místním virtuálním počítači se odstraní a budete muset vytvořit nové disky. Tato možnost se ignoruje, pokud již disky existují. Stále je nutné zadat hodnotu.
5. Vyberte jednotky pro uchovávání dat.
6. Automaticky se vyberou zásady navrácení služeb po obnovení.
7. Vyberte **OK** zahajte opětovné nastavování ochrany. Úloha začne replikovat virtuální počítač z Azure do místní lokality. Průběh můžete sledovat na kartě **Úlohy**. Po úspěšném opětovného nastavování ochrany virtuální počítač dostane chráněném stavu.

Všimněte si následujících informací:
- Pokud chcete provést obnovení do alternativního umístění (když je odstraněn virtuální počítač v místním prostředí), vyberte jednotky pro uchovávání dat a úložiště dat, které jsou nakonfigurované pro hlavní cílový server. Když převezmete služby zpět do místní lokality, virtuální počítače VMware v plánu ochrany navrácení služeb po obnovení pomocí stejného úložiště dat jako hlavní cílový server. Vytvoření nového virtuálního počítače se pak vytvoří v systému vCenter.
- Pokud chcete obnovit virtuální počítač v Azure do existující místní virtuální počítač, připojte úložiště na místním virtuálním počítači s přístupem pro čtení a zápis na hostiteli ESXi hlavního cílového serveru.

    ![Znovunastavení ochrany dialogové okno](./media/vmware-azure-reprotect/reprotectinputs.png)

- Také možné znovunastavení ochrany na úrovni plánu obnovení. Replikační skupinu. možné znovunastavení ochrany pouze prostřednictvím plánu obnovení. Když můžete znovu nastavit ochranu s použitím plánu obnovení, je nutné zadat hodnoty pro každý chráněný počítač.
- K znovunastavení ochrany replikační skupiny použijte stejný hlavní cílový server. Pokud k znovunastavení ochrany replikační skupiny použijete jiný hlavní cílový server, nemůže server zajistit společný bod v čase.
- Na místním virtuálním počítači se během opětovného nastavování ochrany vypne. To pomáhá zajistit konzistenci dat během replikace. Nikdy nezapínat na virtuálním počítači po dokončení opětovného nastavování ochrany.



## <a name="common-issues"></a>Běžné problémy

- Site Recovery v současné době podporuje navrácením služeb po obnovení pouze do úložiště dat VMFS nebo síti vSAN. Systému souborů NFS datastore není podporován. Kvůli tomuto omezení vstup výběr úložiště dat na obrazovce operace opětovného zapnutí ochrany je prázdný pro systém souborů NFS úložišť, nebo zobrazuje sítě vSAN úložiště dat, ale selže v průběhu úlohy. Pokud máte v úmyslu navrácení služeb po obnovení, můžete vytvořit místní úložiště dat VMFS a navrácení služeb po obnovení se. Toto navrácení služeb po obnovení způsobí, že úplné stažení VMDK.
- Je-li provést zjišťování serveru vCenter uživatele jen pro čtení a ochrana virtuálních počítačů, úspěšné ochrany a funguje převzetí služeb při selhání. Během opětovného nastavování ochrany převzetí služeb při selhání se nezdaří, protože úložiště dat nelze zjistit. Příznakem je, že úložiště dat nejsou uvedené během opětovného nastavování ochrany. Chcete-li vyřešit tento problém, můžete aktualizovat přihlašovací údaje vCenter se příslušný účet, který má oprávnění a poté úlohu opakujte. 
- Při navrácení služeb po obnovení virtuálního počítače s Linuxem a spustíte ji v místním, uvidíte, že byl balíček správce sítě odinstalován z počítače. Tato odinstalace dochází, protože je odebrán balíček správce sítě, při obnovení virtuálního počítače v Azure.
- Když virtuální počítač s Linuxem je nakonfigurovaný se statickou IP adresou a se převzaly při selhání do Azure, je ze serveru DHCP získali IP adresu. Když převzetí služeb při selhání do místního, bude virtuální počítač dál pro používání protokolu DHCP k získání IP adresy. Ručně se přihlaste k počítači a pak nastavit IP adresu zpět na statickou adresu v případě potřeby. Virtuální počítače s Windows můžete znovu získat jeho statickou IP adresu.
- Pokud používáte edici free ESXi 5.5 nebo na edici free Hypervisor vSphere 6, proběhne převzetí služeb při selhání úspěšně, ale neproběhne úspěšně navrácení služeb po obnovení. Pokud chcete povolit navrácení služeb po obnovení, upgrade na buď program zkušební licence.
- Pokud se nelze připojit konfigurační server z procesového serveru, aby se ověřilo připojení ke konfiguračnímu serveru na portu 443 použijte službu Telnet. Můžete také zkuste příkaz ping konfiguračního serveru z procesového serveru. Procesový server musí být také prezenční signál při připojení ke konfiguračnímu serveru.
- Server Windows Server 2008 R2 SP1, který je chráněn jako fyzický na místním serveru nelze provést obnovení z Azure do místní lokality.
- Nelze provést navrácení služeb zpět v následujících případech:
    - Migraci počítače do Azure. [Další informace](migrate-overview.md#what-do-we-mean-by-migration).
    - Přesunout virtuální počítač do jiné skupiny prostředků.
    - Odstraníte virtuální počítač Azure.
    - Jste zakázali ochrany virtuálního počítače.
    - V Azure vytvoříte virtuální počítač ručně. Počítač musí mít byl zpočátku chráněné místní a převzetí služeb při selhání do Azure před opětovného nastavování ochrany.
    - Můžete předat pouze na hostitele ESXi. Nemůžete navrácení služeb po obnovení virtuálních počítačů VMware nebo fyzických serverů do hostitele Hyper-V, fyzických počítačů nebo pracovních stanic VMware.


## <a name="next-steps"></a>Další postup

Až se virtuální počítač se dostala do chráněném stavu, můžete provést [zahájit navrácení služeb po obnovení](vmware-azure-failback.md). Navrácení služeb po obnovení vypne virtuální počítač v Azure a místní virtuální počítač se spustí. Počítejte s výpadky, některé aplikace. Vyberte čas navrácení služeb po obnovení, když aplikace může tolerovat možnost, výpadky.


