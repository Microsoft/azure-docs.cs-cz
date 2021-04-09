---
title: Příprava virtuálních počítačů VMware pro zajištění ochrany a navrácení služeb po obnovení pomocí Azure Site Recovery
description: Příprava na vrácení back-VM virtuálních počítačů VMware po převzetí služeb při selhání pomocí Azure Site Recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: f3e352577d65bda021f886d6612c8c0101922003
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727502"
---
# <a name="prepare-for-reprotection-and-failback-of-vmware-vms"></a>Příprava na znovunastavení ochrany a navrácení služeb po obnovení virtuálních počítačů VMware

Po [převzetí služeb při selhání](site-recovery-failover.md) místních virtuálních počítačů VMware nebo fyzických serverů do Azure můžete znovu chránit virtuální počítače Azure vytvořené po převzetí služeb při selhání, aby se prováděly zpět do místní lokality. Díky replikaci z Azure do místního prostředí můžete po dokončení převzetí služeb při selhání z Azure do místního prostředí navrátit.

Než budete pokračovat, získáte rychlý přehled s tímto videem o tom, jak navrátit služby po obnovení z Azure do místní lokality.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

## <a name="reprotectionfailback-components"></a>Součásti pro znovu navýšení nebo obnovení

Než začnete znovu chránit a navracet služby po obnovení z Azure, budete potřebovat několik součástí a nastavení.

| **Komponenta**| **Podrobnosti** |
| --- | --- |
| **Lokální konfigurační server** | Místní konfigurační server musí být spuštěný a připojený k Azure.<br/><br/> Virtuální počítač, na který se nedaří přejít, musí existovat v databázi konfiguračního serveru. Pokud by havárie měla vliv na konfigurační server, obnovte ji se stejnou IP adresou, abyste zajistili, že navrácení služeb po obnovení funguje.<br/><br/>  Pokud se IP adresy replikovaných počítačů zachovaly při převzetí služeb při selhání, je třeba vytvořit připojení typu Site-to-Site (nebo připojení ExpressRoute) mezi virtuálními počítači Azure a síťovou kartou pro navrácení služeb po obnovení konfiguračního serveru. Pro zachované IP adresy konfigurační server potřebuje dvě síťové karty – jeden pro připojení ke zdrojovému počítači a jeden pro připojení k Azure navrácení služeb po obnovení. Tím předejdete překrytí rozsahů adres podsítí pro zdroj a virtuální počítače při selhání. |
| **Procesový Server v Azure** | Před navrácením služeb po obnovení do místní lokality budete potřebovat procesový Server v Azure.<br/><br/> Procesový server přijímá data z chráněného virtuálního počítače Azure a odesílá je do místní lokality.<br/><br/> Mezi procesovým serverem a chráněným virtuálním počítačem budete potřebovat síť s nízkou latencí, proto doporučujeme nasadit procesový Server v Azure pro vyšší výkon replikace.<br/><br/> Pro účely ověření konceptu můžete použít místní procesový Server a ExpressRoute se soukromým partnerským vztahem.<br/><br/> Procesový Server by měl být v síti Azure, ve které se nachází virtuální počítač, u kterého došlo k převzetí služeb při selhání. Procesový Server musí být také schopný komunikovat s místním konfiguračním serverem a hlavním cílovým serverem. |
| **Samostatný hlavní cílový server** | Hlavní cílový server obdrží data navrácení služeb po obnovení a ve výchozím nastavení běží hlavní cílový server Windows na místním konfiguračním serveru.<br/><br/> K hlavnímu cílovému serveru může být připojený až 60 disků. Virtuální počítače, které se nedaří zálohovat, mají více než společný celkový počet 60 disků, nebo pokud selháváte velké objemy přenosů, vytvořte samostatný hlavní cílový server pro navrácení služeb po obnovení.<br/><br/> Pokud jsou počítače shromážděny do replikační skupiny pro konzistenci s více virtuálními počítači, musí být virtuální počítače v systému Windows nebo musí být všechny Linux. Proč? Protože všechny virtuální počítače v replikační skupině musí používat stejný hlavní cílový server a hlavní cílový server musí mít stejný operační systém (se stejnou nebo vyšší verzí) než u replikovaných počítačů.<br/><br/> Hlavní cílový server by neměl mít žádné snímky na svých discích, jinak se ochrana nevrátí a navrácení služeb po obnovení nebude fungovat.<br/><br/> Hlavní cíl nemůže mít řadič SCSI Paravirtual. Kontroler může být jenom řadič LSI Logic. Bez kontroleru adaptéru LSI Logic se chyba ochrany nezdařila. |
| **Zásada replikace pro navrácení služeb po obnovení** | Pokud chcete provést replikaci zpátky na místní lokalitu, budete potřebovat zásadu navrácení služeb po obnovení. Tato zásada se automaticky vytvoří při vytváření zásad replikace do Azure.<br/><br/> Tato zásada se automaticky přidruží ke konfiguračnímu serveru. Je nastavená na prahovou hodnotu cíle bodu obnovení (15 minut), uchovávání bodů obnovení po dobu 24 hodin a frekvence snímků konzistentní vzhledem k aplikacím je 60 minut. Zásady se nedají upravit. |
| **Privátní partnerské vztahy typu Site-to-Site VPN/ExpressRoute** | Opětovné zabezpečení a navrácení služeb po obnovení vyžaduje připojení VPN typu Site-to-site nebo soukromý partnerský vztah ExpressRoute k replikaci dat. |


## <a name="ports-for-reprotectionfailback"></a>Porty pro zajištění ochrany nebo navrácení služeb po obnovení

Pro novou ochranu nebo navrácení služeb po obnovení musí být otevřen určitý počet portů. Následující obrázek znázorňuje porty a znovu chrání nebo navrácení služeb po obnovení.

![Porty pro převzetí služeb při selhání a obnovení](./media/vmware-azure-reprotect/failover-failback.png)


## <a name="deploy-a-process-server-in-azure"></a>Nasazení procesového serveru v Azure

1. [Nastavení procesového serveru](vmware-azure-set-up-process-server-azure.md) v Azure pro navrácení služeb po obnovení.
2. Ujistěte se, že virtuální počítače Azure se můžou spojit s procesovým serverem. 
3. Ujistěte se, že připojení VPN typu Site-to-site nebo privátní partnerské síť ExpressRoute mají dostatečnou šířku pásma pro odesílání dat z procesového serveru do místní lokality.

## <a name="deploy-a-separate-master-target-server"></a>Nasazení samostatného hlavního cílového serveru

1. Poznamenejte si [požadavky a omezení](#reprotectionfailback-components)hlavního cílového serveru.
2. Vytvořte hlavní cílový server se [systémem Windows](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers) nebo [Linux](vmware-azure-install-linux-master-target.md) , aby odpovídal operačnímu systému virtuálních počítačů, které chcete znovu chránit, a navrácení služeb po obnovení.
3. Ujistěte se, že nepoužíváte vMotion úložiště pro hlavní cílový server, nebo navrácení služeb po obnovení může selhat. Počítač virtuálního počítače nelze spustit, protože disky nejsou k dispozici.
    - Pokud tomu chcete zabránit, vylučte hlavní cílový server ze seznamu vMotion.
    - Pokud hlavní cíl po reochraně doprovází úlohu vMotion úložiště, budou chráněné disky virtuálních počítačů připojené k hlavnímu cílovému serveru migrovány do cíle úlohy vMotion. Pokud se po této akci pokusíte o navrácení služeb po obnovení, odpojování disku selže, protože disky nebyly nalezeny. Pak je obtížné najít disky ve vašich účtech úložiště. Pokud k tomu dojde, Najděte je ručně a připojte je k virtuálnímu počítači. Potom můžete místní virtuální počítač spustit znovu.

4. Přidejte jednotku pro uchování do stávajícího hlavního cílového serveru Windows. Přidejte nový disk a naformátujte jednotku. Jednotka pro uchovávání dat se používá k zastavení bodů v čase, kdy se virtuální počítač replikuje zpátky do místní lokality. Všimněte si těchto kritérií. Pokud nejsou splněné, jednotka není uvedená pro hlavní cílový server:
    - Svazek se nepoužívá pro žádný jiný účel, jako je například cíl replikace, a není v režimu zámku.
    - Svazek není svazkem mezipaměti. Vlastní instalační svazek pro procesový Server a hlavní cíl nemá nárok na svazek pro uchovávání dat. Pokud jsou procesový Server a hlavní cíl nainstalované na svazku, je svazek svazek mezipaměti hlavního cíle.
    - Typ systému souborů svazku není FAT ani FAT32.
    - Kapacita svazku je nenulová.
    - Výchozím svazkem uchování pro Windows je svazek R.
    - Výchozí svazek pro uchovávání dat pro Linux je svazek/mnt/retention.

5. Pokud používáte existující procesový Server, přidejte jednotku. Nová jednotka musí splňovat požadavky v posledním kroku. Pokud jednotka pro uchovávání dat není k dispozici, nezobrazí se v rozevíracím seznamu výběr na portálu. Po přidání jednotky do místního hlavního cíle může trvat až 15 minut, než se jednotka objeví ve výběru na portálu. Konfigurační server můžete aktualizovat, pokud se jednotka nezobrazí po 15 minutách.
6. Na hlavním cílovém serveru nainstalujte nástroje VMware nebo otevřít-VM-Tools. Bez nástrojů nelze zjistit úložiště dat na hostiteli ESXi hlavního cíle.
7. Nastavte disk. EnableUUID = true nastavení v parametrech konfigurace hlavního cílového virtuálního počítače ve VMware. Pokud tento řádek neexistuje, přidejte jej. Toto nastavení je nutné k poskytnutí konzistentního identifikátoru UUID pro VMDK, aby bylo správně připojeno.
8. Ověřit požadavky na přístup vCenter Server:
    - Pokud se virtuální počítač, na který se vrátíte zpátky, nachází na hostiteli ESXi spravovaném VMware vCenter Server, potřebuje hlavní cílový server přístup k místnímu souboru virtuálního počítače (VMDK), aby se mohla zapsat replikovaná data na disky virtuálního počítače. Ujistěte se, že místní úložiště dat virtuálního počítače je připojené k hlavnímu cílovému hostiteli s přístupem pro čtení a zápis.
    - Pokud virtuální počítač není na hostiteli ESXi spravovaném vCenter Server VMware, Site Recovery vytvoří nový virtuální počítač během opětovné ochrany. Tento virtuální počítač se vytvoří na hostiteli ESXi, na kterém vytvoříte virtuální počítač hlavního cílového serveru. Pokud chcete vytvořit virtuální počítač na hostiteli, který chcete, vyberte pečlivě hostitele ESXi. Pevný disk virtuálního počítače musí být v úložišti dat přístupném pro hostitele, na kterém běží hlavní cílový server.
    - Další možnost – Pokud už místní virtuální počítač existuje pro navrácení služeb po obnovení, je potřeba ho před navrácením služeb po obnovení odstranit. Navrácení služeb po obnovení pak vytvoří nový virtuální počítač na stejném hostiteli jako hlavní cílový hostitel ESXi. Po navrácení služeb po obnovení do alternativního umístění se data obnoví do stejného úložiště dat a do stejného hostitele ESXi, jaký používá místní hlavní cílový server.
9. U fyzických počítačů, které selžou zpátky na virtuální počítače VMware, byste měli dokončit zjišťování hostitele, na kterém běží hlavní cílový server, než budete moct počítač znovu chránit.
10. Ověřte, jestli má hostitel ESXi, na kterém má hlavní cílový virtuální počítač k němu připojené aspoň jeden úložiště dat systému souborů VMFS (Virtual Machine File System). Pokud nejsou k dispozici žádné úložiště dat VMFS, vstup do úložiště dat v nastavení ochrany je prázdný a nemůžete pokračovat.


## <a name="next-steps"></a>Další kroky

Znovu nastavte [ochranu](vmware-azure-reprotect.md) virtuálního počítače.
