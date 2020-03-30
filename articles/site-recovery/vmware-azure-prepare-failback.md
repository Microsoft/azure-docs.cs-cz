---
title: Příprava virtuálních počítačů VMware na opětovné zabezpečení a navrácení služeb po obnovení pomocí azure site recovery
description: Příprava na vrácení virtuálních stránek VMware na vrácení služeb po převzetí služeb při selhání pomocí azure site recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: 5a330f8cba31640d0116ca3d5ccab352ce5b3509
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257183"
---
# <a name="prepare-for-reprotection-and-failback-of-vmware-vms"></a>Příprava na opětovnou ochranu a navrácení služeb po obnovení virtuálních počítačích VMware

Po [převzetí služeb při selhání](site-recovery-failover.md) místních virtuálních počítačích VMware nebo fyzických serverů do Azure znovu protecteor virtuálních počítačích Azure vytvořených po převzetí služeb při selhání tak, aby replikovat zpět do místního webu. S replikací z Azure na místní místě, pak můžete zpět pomocí služby po obnovení spuštěním převzetí služeb při selhání z Azure do místního, když jste připraveni.

Než budete pokračovat, získejte rychlý přehled s tímto videem o tom, jak vrátit z Azure na místní web.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

## <a name="reprotectionfailback-components"></a>Součásti opětovné ochrany/navrácení služeb po obnovení

Před opětovnou ochranou a obnovením služby Azure potřebujete několik součástí a nastavení.

**Komponenta**| **Podrobnosti**
--- | ---
**Lokální konfigurační server** | Místní konfigurační server musí být spuštěný a připojený k Azure.<br/><br/> Virtuální počítače, na který se vracíte, musí existovat v databázi konfiguračního serveru. Pokud dojde k havárii konfiguračního serveru, obnovte jej se stejnou adresou IP, abyste zajistili, že zpětné převzetí služeb po obnovení bude fungovat.<br/><br/>  Pokud ip adresy replikovaných počítačů byly zachovány při převzetí služeb při selhání, připojení mezi lokalitami (nebo připojení ExpressRoute) by měla být vytvořena mezi počítači virtuálních počítačů Azure a nic na vrácení služeb po selhání konfiguračního serveru. U uchovávaných IP adres potřebuje konfigurační server dvě síťové karty – jednu pro připojení zdrojového počítače a jednu pro připojení azure na vrácení po selhání. Tím se zabrání překrytí rozsahů adres podsítě pro zdroj a převzetí selhání přes virtuální chod.
**Procesní server v Azure** | Než budete moci vrátit do místního webu, potřebujete v Azure procesní server.<br/><br/> Procesní server přijímá data z chráněného virtuálního počítače Azure a odesílá je do místního webu.<br/><br/> Potřebujete síť s nízkou latencí mezi procesním serverem a chráněným virtuálním počítačem, takže doporučujeme nasadit procesní server v Azure pro vyšší výkon replikace.<br/><br/> Pro proof-of-concept můžete použít místní procesový server a ExpressRoute s privátním partnerským vztahem.<br/><br/> Procesní server by měl být v síti Azure, ve které se nachází virtuální počítač s převzetím počítače. Procesní server musí být také schopen komunikovat s místním konfiguračním serverem a hlavním cílovým serverem.
**Samostatný hlavní cílový server** | Hlavní cílový server přijímá data navrácení služeb po selhání a ve výchozím nastavení je hlavní cílový server systému Windows spuštěn na místním konfiguračním serveru.<br/><br/> Hlavní cílový server může mít k němu připojeno až 60 disků. Selhání virtuálních počítačů má více než celkový součet 60 disků nebo pokud vracíte zpět velké objemy provozu, vytvořte samostatný hlavní cílový server pro navrácení služeb po obnovení.<br/><br/> Pokud jsou počítače shromážděny do replikační skupiny pro konzistenci více virtuálních počítačů, virtuální počítače musí být všechny Windows nebo všechny musí být Linux. Proč? Vzhledem k tomu, že všechny virtuální počítače v replikační skupině musí používat stejný hlavní cílový server a hlavní cílový server musí mít stejný operační systém (se stejnou nebo vyšší verzí) než u replikovaných počítačů.<br/><br/> Hlavní cílový server by neměl mít na svých discích žádné snímky, jinak nebude fungovat opětovné protekce a navrácení služeb po obnovení.<br/><br/> Hlavní cíl nemůže mít paravirtuální řadič SCSI. Řadič může být pouze řadič EmI Logic. Bez řadiče Logika LSI se reprotection nezdaří.
**Zásady replikace navrácení služeb po selhání** | Chcete-li replikovat zpět do místního webu, potřebujete zásady navrácení služeb po selhání. Tato zásada se automaticky vytvoří při vytvoření zásady replikace do Azure.<br/><br/> Tato zásada se automaticky přidruží ke konfiguračnímu serveru. Je nastavena na prahovou hodnotu RPO 15 minut, bod obnovení uchovávání 24 hodin a aplikace konzistentní snímek frekvence je 60 minut. Zásadu nelze upravit. 
**Privátní partnerský vztah VPN/ExpressRoute mezi lokalitami** | Opětovné protekce a navrácení služeb po obnovení potřebují připojení VPN mezi lokalitami nebo soukromý partnerský vztah ExpressRoute k replikaci dat. 


## <a name="ports-for-reprotectionfailback"></a>Porty pro opětovné protekci/navrácení služeb po obnovení

Pro opětovné navrácení služeb po obnovení musí být otevřeno několik portů. Následující obrázek znázorňuje porty a znovu chránit/obnovit tok.

![Porty pro převzetí služeb při selhání a navrácení služeb po selhání](./media/vmware-azure-reprotect/failover-failback.png)


## <a name="deploy-a-process-server-in-azure"></a>Nasazení procesního serveru v Azure

1. [Nastavte procesní server](vmware-azure-set-up-process-server-azure.md) v Azure pro navrácení služeb po službě navrácení služeb po službě.
2. Ujistěte se, že virtuální počítače Azure můžou dosáhnout procesního serveru. 
3. Ujistěte se, že připojení VPN mezi lokalitami nebo síť soukromého partnerského vztahu ExpressRoute má dostatečnou šířku pásma pro odesílání dat z procesního serveru do místní lokality.

## <a name="deploy-a-separate-master-target-server"></a>Nasazení samostatného hlavního cílového serveru

1. Poznamenejte si [požadavky a omezení](#reprotectionfailback-components)hlavního cílového serveru .
2. Vytvořte hlavní cílový server [Systému Windows](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers) nebo [Linuxu,](vmware-azure-install-linux-master-target.md) který bude odpovídat operačnímu systému virtuálních počítačů, které chcete znovu chránit, a navrácení můe ní.
3. Ujistěte se, že nepoužíváte storage vMotion pro hlavní cílový server, nebo může selhat navrácení služeb po selhání. Počítač s virtuálním počítačem nelze spustit, protože disky nejsou k dispozici.
    - Chcete-li tomu zabránit, vylučte hlavní cílový server ze seznamu vMotion.
    - Pokud hlavní cíl prochází úlohou Storage vMotion po opětovné protekci, chráněné disky virtuálního počítače připojené k hlavnímu cílovému serveru migrují do cíle úlohy vMotion. Pokud se pokusíte po tomto selhání, odpojení disku se nezdaří, protože disky nebyly nalezeny. Je pak těžké najít disky v účtech úložiště. Pokud k tomu dojde, najděte je ručně a připojte je k virtuálnímu virtuálnímu mísu. Za to, že místní virtuální počítač lze spustit.

4. Přidejte retenční jednotku na existující hlavní cílový server systému Windows. Přidejte nový disk a naformátujte jednotku. Retenční jednotka se používá k zastavení bodů v čase, kdy virtuální počítač replikuje zpět do místní lokality. Poznamenejte si tato kritéria. Pokud nejsou splněny, jednotka není uvedena pro hlavní cílový server:
    - Svazek se nepoužívá k žádnému jinému účelu, například k cíli replikace, a není v režimu uzamčení.
    - Svazek není svazek mezipaměti. Vlastní svazek instalace pro procesový server a hlavní cíl není způsobilý pro svazek uchovávání informací. Pokud jsou na svazku nainstalován y procesní server a hlavní cíl, je svazek svazku hlavního cíle mezipaměti.
    - Typ systému souborů svazku není FAT nebo FAT32.
    - Kapacita svazku je nenulová.
    - Výchozí retenční svazek pro systém Windows je svazek R.
    - Výchozí svazek uchovávání informací pro Linux je /mnt/retention.

5. Pokud používáte existující procesní server, přidejte jednotku. Nový pohon musí splňovat požadavky v posledním kroku. Pokud jednotka uchovávání informací není k dispozici, nezobrazí se v rozevíracím seznamu výběru na portálu. Po přidání jednotky do místního hlavního cíle trvá až 15 minut, než se jednotka zobrazí ve výběru na portálu. Konfigurační server můžete aktualizovat, pokud se jednotka nezobrazí po 15 minutách.
6. Nainstalujte nástroje VMware nebo nástroje pro otevření vm na hlavní cílový server. Bez nástrojů nelze zjistit úložiště dat na hostiteli ESXi hlavního cíle.
7. Nastavte disk. EnableUUID=true nastavení v konfiguračních parametrech hlavního cílového virtuálního počítače ve VMware. Pokud tento řádek neexistuje, přidejte jej. Toto nastavení je nutné poskytnout konzistentní UUID v VMDK tak, aby se připojí správně.
8. Zkontrolujte požadavky na přístup k serveru vCenter:
    - Pokud je virtuální počítač, ke kterému se vracíte zpět, na hostiteli ESXi spravovaném serverem VMware vCenter Server, hlavní cílový server potřebuje přístup k místnímu souboru virtuálního počítače Virtuální počítač (VMDK), aby mohl zapisovat replikovaná data na disky virtuálního počítače. Ujistěte se, že místní úložiště dat virtuálního počítače je připojené na hlavním cílovém hostiteli s přístupem pro čtení a zápis.
    - Pokud virtuální počítač není na hostitele ESXi spravovanévCenter Serveru VMware, site recovery vytvoří nový virtuální počítač během opětovné ochrany. Tento virtuální počítač se vytvoří na hostiteli ESXi, na kterém vytvoříte hlavní cílový server virtuálního počítači. Pečlivě zvolte hostitele ESXi a vytvořte virtuální počítač na hostiteli, který chcete. Pevný disk virtuálního počítače musí být v úložišti dat přístupném pro hostitele, na kterém běží hlavní cílový server.
    - Další možností, pokud místní virtuální počítač již existuje pro navrácení služeb po službě " znovu, je odstranit jej před navrácením po službě napo ji. Navrácení služeb po obnovení pak vytvoří nový virtuální počítač na stejném hostiteli jako hlavní cíl ESXi hostitele. Při obnovení do alternativního umístění jsou data obnovena do stejného úložiště dat a stejného hostitele ESXi, který používá místní hlavní cílový server.
9. Pro fyzické počítače selhání zpět do virtuálních počítačů VMware, měli byste dokončit zjišťování hostitele, na kterém je spuštěn hlavní cílový server, před opětovnou ochranu počítače.
10. Zkontrolujte, zda je k němu připojen o hostiteles ESXi, na kterém má hlavní cílový virtuální počítač alespoň jedno úložiště dat systému souborů virtuálního počítače (VMFS). Pokud nejsou připojena žádná úložiště dat VMFS, vstup úložiště dat v nastavení opětovné ochrany je prázdný a nelze pokračovat.


## <a name="next-steps"></a>Další kroky

[Znovu přizamknete](vmware-azure-reprotect.md) virtuální ho.
