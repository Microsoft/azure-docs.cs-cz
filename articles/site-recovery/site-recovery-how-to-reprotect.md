---
title: "Znovu nastavte ochranu z Azure na místní web | Microsoft Docs"
description: "Po převzetí služeb při selhání virtuálních počítačů do Azure můžete zahájit navrácení služeb po obnovení a dovést tak virtuální počítače zpět do místní. Naučte se znovu nastavte ochranu před navrácení služeb po obnovení."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/06/2018
ms.author: rajanaki
ms.openlocfilehash: c336966f9a785707e76bc6a10c4a9283d797d064
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2018
---
# <a name="reprotect-from-azure-to-an-on-premises-site"></a>Znovu nastavte ochranu z Azure do místního serveru



## <a name="overview"></a>Přehled
Tento článek popisuje, jak k nastavení opětné Azure virtuálních počítačů z Azure do místního webu. Postupujte podle pokynů v tomto článku, až budete připraveni k selhání zpět vaše virtuální počítače VMware nebo Windows nebo Linuxem fyzických serverů po jste při selhání z místní lokality do Azure (jak je popsáno v [VMware replikaci virtuálních počítačů a fyzických serverů do Azure s Azure Site Recovery](site-recovery-failover.md)).

> [!WARNING]
> Až budete mít buď není možné navrácení služeb po obnovení [dokončit migraci](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration), přesunout virtuální počítač do jiné skupiny prostředků nebo odstranit virtuální počítač Azure. Pokud zakážete ochrany virtuálního počítače, nemůžete navrácení služeb po obnovení. Pokud je virtuální počítač vytvořený nejprve v Azure (narodili v cloudu) pak můžete nelze nastavte ji znovu zpět na místní. Tento počítač by měl mít byla původně chráněném místní a převzetí služeb při selhání do Azure před opětovné ochrany.


Po dokončení vytvoření a jsou replikace chráněných virtuálních počítačů, můžete zahájit navrácení služeb po obnovení ve virtuálních počítačích, aby byly k místní lokalitě.

> [!NOTE]
> Je možné pouze navrácení služeb po obnovení do hostitele ESXi a opětovné ochrany. Nelze vytvořit virtuální počítač na hostitele Hyper-v, pracovních stanic VMware nebo jiných virtualizační platforma navrácení služeb po obnovení.

POST dotazy nebo připomínky můžete na konci tohoto článku nebo na [fóru Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

Pro rychlý přehled najdete v následujícím videu o tom, jak převzetí služeb při selhání z Azure na místní lokalitu.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="prerequisites"></a>Požadavky

> [!IMPORTANT]
> Během převzetí služeb při selhání do Azure, nemusí být dostupný na místní lokalitu a proto konfigurační server může být buď zrušení k dispozici nebo vypnutí. Během opětovné ochrany a navrácení služeb po obnovení by měla být na místním serveru configuration spuštěná a v připojeném stavu OK.


Při přípravě k nastavení opětné virtuálních počítačů trvat nebo vzít v úvahu následující požadované akce:

* Pokud vCenter server spravuje virtuální počítače, které chcete navrácení služeb po obnovení, ujistěte se, že máte [požadovaná oprávnění](site-recovery-vmware-to-azure-classic.md) zjišťování virtuálních počítačů na servery vCenter server.

  > [!WARNING]
  > Pokud snímky se nacházejí na místní hlavní cílový nebo virtuální počítač, nové provedení ochrany se nezdaří. Než budete pokračovat k nastavení opětné můžete odstranit snímky na hlavním cíli. Snímky virtuálního počítače automaticky sloučí během úloh opětovné ochrany.

* Předtím, než jste navrácení služeb po obnovení, vytvořte dva další součásti:

  * **Procesový server**: [procesový server](site-recovery-vmware-setup-azure-ps-resource-manager.md) přijímá data z chráněného virtuálního počítače v Azure a odesílá data do místního webu. Síť s nízkou latencí je vyžadován mezi procesovým serverem a chráněného virtuálního počítače. Pokud používáte připojení k Azure ExpressRoute nebo využitím Azure procesového serveru a sítě VPN, proto může obsahovat serveru místní proces.
  
  * **Hlavní cílový server**: hlavní cílový server přijímá data navrácení služeb po obnovení. Hlavní cílový server nainstalované ve výchozím nastavení má server pro správu místní, kterou jste vytvořili. V závislosti na objem provozu zpět se nezdařilo, však může potřebovat vytvořit samostatné hlavní cílový server navrácení služeb po obnovení.
    * [Virtuální počítač s Linuxem musí hlavní cílový server Linux](site-recovery-how-to-install-linux-master-target.md).
    * Virtuální počítač Windows musí hlavní cílový server systému Windows. Místní proces server a hlavní cílových počítačů můžete použít znovu.
    * Hlavní cíl má jiné požadavky, které jsou uvedeny v [běžné co je potřeba zkontrolovat na hlavním cíli před opětovné ochrany](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server).

> [!NOTE]
> Všechny virtuální počítače skupiny replikace by měla být stejný typ operačního systému (všechny verze Windows nebo Linux všechny). Replikační skupina s smíšený operačních systémů se aktuálně nepodporuje pro opětovné ochrany a navrácení služeb po obnovení místně. Je to proto, že se hlavní cíl by měl být stejný operační systém jako virtuální počítač a všechny virtuální počítače replikační skupiny musí mít stejný hlavní cíl. 

    

* Konfigurační server je místní, když jste navrácení služeb po obnovení. Virtuální počítač musí během navrácení služeb po obnovení, neexistuje v databázi konfigurací serveru. Jinak navrácení služeb po obnovení neúspěšné. 

> [!IMPORTANT]
> Zajistěte, aby podniknout pravidelně naplánovaných záloh konfigurace serveru. Pokud dojde k havárii, obnovení tak, aby funguje navrácení služeb po obnovení serveru se stejnou IP adresu.

> [!WARNING]
> Replikační skupinu by měla mít pouze virtuální počítače Windows nebo virtuální počítače s Linuxem a není kombinaci obou vzhledem k tomu, že všechny virtuální počítače ve skupině replictaion používá stejnou hlavní cílový server a virtuální počítač s Linuxem vyžaduje server hlavního cíle Linuxu a, jako jsou vhodné pro virtuální počítač s Windows.

* Nastavte `disk.EnableUUID=true` nastavení v konfigurační parametry hlavního cílového virtuálního počítače v prostředí VMware. Pokud tento řádek neexistuje, přidejte ji. Toto nastavení je nutné k zajištění konzistentní UUID disku virtuálního počítače (VMDK) tak, aby ji připojí správně.

* *Na hlavním cílovém serveru nemůžete použít řešení Storage vMotion*. To může způsobit navrácení služeb po obnovení nezdaří. Virtuální počítač nelze spustit, protože disky nejsou dostupné. K tomuto problému předešli, vylučte ze seznamu řešení vMotion hlavních cílových serverů.

* Přidat nový disk do hlavního cílového serveru: jednotka pro uchování. Přidejte nový disk a formátování disku.


### <a name="frequently-asked-questions"></a>Nejčastější dotazy

#### <a name="why-do-i-need-a-s2s-vpn-or-an-expressroute-connection-to-replicate-data-back-to-the-on-premises-site"></a>Proč musím S2S VPN nebo ExpressRoute připojení k replikaci dat zpět na místní lokalitu?
Zatímco prostřednictvím Internetu nebo připojení typu ExpressRoute, který má veřejného partnerského vztahu, vytvoření a vyžadují site-to-site (S2S) VPN k replikaci dat navrácení služeb po obnovení může dojít replikace z místního do Azure. Zadejte síť tak, aby virtuální počítače při selhání v Azure může kontaktovat (ping) konfigurace místní server. Můžete také nasadit procesový server v Azure sítě virtuálního počítače při selhání. Tento proces server by měl být také schopné komunikovat s místní konfigurační server.

#### <a name="when-should-i-install-a-process-server-in-azure"></a>Při instalaci procesový server v Azure?


Virtuální počítače na Azure, který chcete znovu nastavte ochranu odesílat data replikace na procesní server. Nastavení sítě tak, aby virtuální počítače na platformě Azure můžete připojit k procesu serveru.

Můžete nasadit procesový server v Azure nebo použít existující server proces, který jste použili při převzetí služeb při selhání. Důležité vzít v úvahu je latence odeslat data z virtuálních počítačů v Azure na procesní server.

Pokud máte nastavení připojení ExpressRoute, můžete k odesílání dat, protože je nízkou latenci mezi virtuálním počítačem a serverem proces serveru místní proces.

 ![Diagram architektury pro ExpressRoute](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)



Pokud máte pouze S2S VPN, doporučujeme však nasazení procesní server v Azure.

 ![Diagram architektury pro síť VPN](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)


Mějte na paměti, že replikace z Azure do místní může dojít pouze přes síť VPN S2S nebo soukromého partnerského vztahu ExpressRoute sítě. Ujistěte se, že dostatečná šířka pásma je k dispozici v daném kanálu sítě.

Informace o instalaci serveru pro postup založené na Azure najdete v tématu [spravovat procesový server běží v Azure](site-recovery-vmware-setup-azure-ps-resource-manager.md).

> [!TIP]
> Doporučujeme používat využitím Azure procesového serveru během navrácení služeb po obnovení. Replikace výkon je vyšší, pokud je procesový server blíže k replikaci virtuálního počítače (počítače při selhání v Azure). Však během testování konceptu (POC) nebo ukázku, můžete použít na místním serveru proces společně s ExpressRoute s soukromého partnerského vztahu k dokončení POC rychlejší.

> [!NOTE]
> Replikace z místního do Azure může dojít pouze přes internet nebo ExpressRoute s veřejného partnerského vztahu. Replikace z Azure do místní může dojít pouze přes S2S VPN nebo ExpressRoute s soukromého partnerského vztahu


#### <a name="what-ports-should-i-open-on-different-components-so-that-reprotection-can-work"></a>Jaké porty by měla otevřít na různých komponent, aby mohl pracovat nové provedení ochrany?

![Porty pro převzetí služeb při selhání a navrácení služeb po obnovení](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

#### <a name="which-master-target-server-should-i-use-for-reprotection"></a>Které hlavní cílový server by měl použít pro nové provedení ochrany?
Místní hlavní cílový server je potřeba přijímat data z procesového serveru a pak zapsat do VMDK na místním virtuálním počítači. Pokud chráníte virtuální počítače s Windows, potřebujete hlavní cílový server systému Windows. Můžete opakovaně použít cílový server a hlavní proces místní<!-- !todo component -->. Pro virtuální počítače s Linuxem musíte nastavit dalších místních hlavního cíle Linuxu.


Informace o instalaci hlavní cílový server najdete v tématu:

* [Postup instalace Windows hlavní cílový server](site-recovery-vmware-to-azure.md)
* [Postup instalace Linux hlavní cílový server](site-recovery-how-to-install-linux-master-target.md)


#### <a name="what-datastore-types-are-supported-on-the-on-premises-esxi-host-during-failback"></a>Jaké typy úložiště jsou podporovány na hostiteli ESXi místní během navrácení služeb po obnovení?

V současné době podporuje Azure Site Recovery selhání zpět jenom na systém souborů virtuálního počítače (VMFS) nebo sítě vSAN úložiště. Úložiště dat systému souborů NFS se nepodporuje. Kvůli tomuto omezení zadejte výběr úložiště dat na opětovné ochrany obrazovky je prázdný v případě systému souborů NFS datastores nebo ukazuje s úložištěm síť vSAN, ale v průběhu úlohy nezdaří. Pokud máte v úmyslu navrácení služeb po obnovení, můžete vytvořit místní úložiště VMFS a navrácení služeb po obnovení je. Úplné stažení VMDK způsobí, že tento navrácení služeb po obnovení.

### <a name="common-things-to-check-after-completing-installation-of-the-master-target-server"></a>Běžné co je potřeba zkontrolovat po dokončení instalace hlavní cílový server

* Pokud je virtuální počítač existuje místně na serveru vCenter server, hlavní cílový server potřebuje přístup k VMDK na místním virtuálním počítači. Je nutný přístup k zápisu replikovaná data na disky virtuálního počítače. Zajistěte, aby je připojené úložiště na místním virtuálním počítači na hostiteli se hlavní cíl s přístupem pro čtení a zápis.

* Pokud virtuální počítač není přítomen místně na serveru vCenter server, je potřeba vytvořit nový virtuální počítač při vytvoření služby Site Recovery. Tento virtuální počítač se vytvoří na hostiteli ESX, ve kterém vytvoříte na hlavním cíli. Vyberte hostitele ESX pečlivě, tak, aby na hostiteli, kterého chcete, aby se vytvoří navrácení služeb po obnovení virtuálního počítače.

* *Řešení Storage vMotion nelze použít pro hlavní cílový server*. To může způsobit navrácení služeb po obnovení nezdaří. Virtuální počítač nelze spustit, protože disky nejsou dostupné.

  > [!WARNING]
  > Pokud hlavní cíl zde nevyskytlo úlohu řešení vMotion úložiště po vytvoření, disky chráněných virtuálních počítačů, které jsou připojené k hlavnímu cíli migrovat do cílové řešení vMotion úlohy. Pokud se pokusíte zpět po selhat, odpojení disku nezdaří, protože nebyly nalezeny disky. Pak stává vyhledání disky v účty úložiště. Musíte je ručně vyhledat a připojte je k virtuálnímu počítači. Potom lze spustit na místním virtuálním počítači.

* Přidáním jednotky uchovávání dat na existující hlavní cílový server systému Windows. Přidejte nový disk a formátování disku. Jednotky pro uchovávání dat se používá k zastavení body v čase, kdy se mají replikovat virtuální počítač zpět do místního webu. Toto jsou některé kritéria jednotka pro uchování. Bez těchto kritérií jednotku nebude v seznamu uveden pro hlavní cílový server.

   * Svazek není použít k žádnému jinému účelu, jako je cílem replikace.

   * Svazek není v režimu zamknutí.

   * Svazek není svazek mezipaměti. Na tomto svazku by neměla být instalace hlavní cíl. Vlastní instalace svazek pro cílový server a hlavní proces nejsou vhodné pro svazek pro uchovávání. Když procesový server a hlavní cíl jsou nainstalovány na svazek, svazek je svazek mezipaměti hlavního cíle.

   * Typ systému souborů svazku není FAT nebo FAT32.

   * Kapacita svazku je nenulové hodnoty.

   * Svazek pro uchovávání dat výchozí pro Windows je svazek R.

   * Výchozí svazek pro uchovávání dat pro Linux je /mnt/retention.

  > [!IMPORTANT]
  > Je nutné přidat na nový disk, pokud používáte existující počítač proces serveru nebo konfigurační server nebo škálování nebo proces serveru/hlavní cílový server počítač. Nový disk by měl splňovat požadavky na předchozí. Pokud jednotka pro uchování není dostupná, nezobrazí se v rozevíracím seznamu výběru na portálu. Po přidání k hlavnímu cíli místní jednotku, bude trvat až 15 minut na jednotce, než se objeví ve výběru na portálu. Můžete taky aktualizovat konfigurační server, pokud jednotka nezobrazí po 15 minutách.

* Virtuální počítač s Linuxem při selhání vyžaduje hlavní cílový server Linux. Při selhání virtuálního počítače s Windows vyžaduje hlavní cílový server systému Windows.

* Nainstalujte nástroje VMware na hlavním cílovém serveru. Bez nástroje VMware nelze zjistit datastores na hostiteli ESXi na hlavním cíli.

* Povolit `disk.EnableUUID=true` parametr na hlavním cílovém virtuálním počítači pomocí vlastností vCenter. <!-- !todo Needs link. -->

* Hlavní cíl musí mít alespoň jeden VMFS úložiště připojené. Pokud je none, **úložiště** vstup na stránce opětovné ochrany bude prázdný a nemůže pokračovat.

* Hlavní cílový server nemůže mít snímky na discích. Pokud existují snímky, vytvoření a navrácení služeb po obnovení nezdaří.

* Na hlavním cíli nemůže mít řadič Paravirtual SCSI. Kontroler může být pouze řadič LSI Logic. Bez řadič LSI Logic nové provedení ochrany se nezdaří.

* V dané instanci může mít hlavního cíle atmst 60 disky připojené k němu. Pokud počet virtuálních počítačů se znovu k hlavnímu cíli místní součet celkový počet víc než 60 disky a pak reprotects k hlavnímu cíli začne selhání. Ujistěte se, že máte dostatek hlavní cíl sloty disku nebo nasadit další hlavních cílových serverů.

<!--
### Failback policy
To replicate back to on-premises, you will need a failback policy. This policy get automatically created when you create a forward direction policy. Note that

1. This policy gets auto associated with the configuration server during creation.
2. This policy is not editable.
3. The set values of the policy are (RPO Threshold = 15 Mins, Recovery Point Retention = 24 Hours, App Consistency Snapshot Frequency = 60 Mins)
   ![](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

-->

## <a name="steps-to-reprotect"></a>Kroky k nastavení opětné

> [!NOTE]
> Po spuštění virtuálního počítače v Azure, bude trvat nějakou dobu agenta k registraci zpět na konfigurační server (až 15 minut). Během této doby došlo selže a chybová zpráva označuje, že agent není nainstalovaný. Počkejte několik minut a opakujte akci vytvoření.



1. V **trezoru** > **replikované položky**, klikněte pravým tlačítkem na virtuální počítač, který při selhání a pak vyberte **znovu nastavit ochranu**. Můžete také klikněte na počítač a vyberte **znovu nastavit ochranu** z příkazová tlačítka.
2. V okně, Všimněte si, že směr ochrany, **Azure k místnímu**, je již vybrána.
3. V **hlavní cílový Server** a **procesový Server**, vyberte místní hlavní cílový server a procesový server.
4. Pro **úložiště**, vyberte úložiště dat, ke které chcete obnovit disky na místě. Tato možnost se používá při odstranění virtuálního počítače na místě, a budete muset vytvořit nové disky. Tato možnost je ignorována, pokud disky již existují, ale stále je třeba zadat hodnotu.
5. Vyberte jednotky pro uchovávání dat.
6. Zásady navrácení služeb po obnovení je automaticky vybrán.
7. Klikněte na tlačítko **OK** zahájíte nové provedení ochrany. Úlohu začne replikovat virtuální počítač z Azure do místní lokality. Průběh můžete sledovat na **úlohy** kartě.

Pokud chcete obnovit do alternativního umístění (při odstranění virtuálního počítače místní), vyberte jednotka pro uchování a úložiště dat, které jsou nakonfigurované pro hlavní cílový server. Pokud žádnou zpět na místní lokalitu, virtuální počítače VMware v plánu ochrany navrácení služeb po obnovení použití stejné úložiště jako hlavní cílový server. Nový virtuální počítač se pak vytvoří v vCenter.

Pokud chcete obnovit virtuální počítač na platformě Azure k existující místní virtuální počítač, připojte datastores místní virtuální počítač s přístupem pro čtení a zápis na hostiteli ESXi hlavní cílový server.
    ![Dialogové okno znovu nastavit ochranu](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

Znovu nastavte ochranu můžete také na úrovni plánu obnovení. Replikační skupiny můžete reprotected pouze prostřednictvím plán obnovení. Pokud jste znovu nastavte ochranu pomocí plán obnovení, budete muset zadat hodnoty pro každý chráněný počítač.

> [!NOTE]
> Použijte stejný hlavní cílový server k nastavení opětné replikační skupiny. Pokud používáte jiný hlavní cílový server k nastavení opětné skupiny replikace, nemůže server poskytovat společný bod v čase.

> [!NOTE]
> Místní virtuální počítač je vypnutý během vytvoření. To pomáhá zajistit konzistenci dat během replikace. Nezapínejte virtuálního počítače, po dokončení vytvoření.

Poté, co vytvoření úspěšné, bude virtuální počítač zadejte chráněném stavu.

## <a name="common-issues"></a>Běžné problémy

* Pokud jste použili šablonu pro vytvoření virtuálního počítače, ujistěte se, že každý virtuální počítač má svou vlastní UUID pro disky. Pokud UUID místní virtuální počítač je v konfliktu s hlavního cíle, protože obě byly vytvořeny ze stejné šablony, nové provedení ochrany se nezdaří. Nasaďte další hlavní cíl, který ještě nebyl vytvořen ze stejné šablony.

* Pokud provedení zjišťování vCenter uživatele jen pro čtení a ochrana virtuálních počítačů, je úspěšné ochrany a funguje převzetí služeb při selhání. Při vytvoření převzetí služeb při selhání se nezdaří, protože datastores nemůže být zjištěny. Příznakem je, že nejsou uvedené datastores během vytvoření. Chcete-li vyřešit tento problém, můžete aktualizovat pověření vCenter odpovídajícímu účtu, který má oprávnění a opakujte úlohu. Další informace najdete v tématu [VMware replikaci virtuálních počítačů a fyzických serverů do Azure s Azure Site Recovery](site-recovery-vmware-to-azure-classic.md).

* Při navrácení služeb po obnovení virtuální počítač s Linuxem a spusťte ho místně, uvidíte, že byl balíček správce sítě odinstalován z počítače. Tato odinstalace se stane, protože balíček správce sítě odebrán, pokud je virtuální počítač obnovit v Azure.

* Když virtuální počítač s Linuxem je nakonfigurovaný se statickou IP adresou a při selhání do Azure, je ze serveru DHCP získali IP adresu. Při selhání do místní virtuální počítač dál používat službu DHCP získat IP adresu. Ručně Přihlaste se k počítači a nastavit IP adresu zpět na statickou adresu v případě potřeby. Virtuálního počítače s Windows můžete znovu získat jeho statickou IP adresu.

* Pokud používáte edice free ESXi 5.5 nebo edice free Hypervisor vSphere 6, převzetí služeb při selhání úspěšné, ale navrácení služeb po obnovení nebude úspěšná. Chcete-li povolit navrácení služeb po obnovení, upgradujte na buď program zkušební licence.

* Pokud nelze kontaktovat konfigurační server z procesového serveru, použijte Telnet zkontrolujte připojení k serveru configuration na portu 443. Můžete také zkusit na příkaz ping konfigurační server z procesového serveru. Procesový server musí být také prezenční signál při připojení k serveru configuration.

* Pokud se pokoušíte navrácení služeb po obnovení alternativní vCenter, ujistěte se, že budou zjišťována vaší nové vCenter a hlavní cílový server. Typické symptomem je, že datastores nejsou přístupné nebo viditelné v **znovu nastavte ochranu** dialogové okno.

* Server Windows Server 2008 R2 SP1, který je chráněn jako fyzický místní server nemůže být zpět z Azure místní webového serveru se nezdařil.


## <a name="next-steps"></a>Další postup

Po zadání chráněném stavu virtuálního počítače můžete [zahájení navrácení služeb po obnovení](site-recovery-how-to-failback-azure-to-vmware.md#steps-to-fail-back). 

Navrácení služeb po obnovení se vypnout virtuální počítač v Azure a místní virtuální počítač spustit. Očekávají menšímu výpadku pro aplikaci. Vyberte čas pro navrácení služeb po obnovení, pokud aplikace může tolerovat výpadku.