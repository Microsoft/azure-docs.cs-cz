---
title: Azure Site Recovery pro odstraňování potíží v problémy s probíhající replikací Azure do Azure | Dokumentace Microsoftu
description: Řešení potíží s chybami a problémů při replikaci virtuálních počítačů Azure pro zotavení po havárii
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 9ff756270c368d39b7ef78d7c1046f7c91169668
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56299594"
---
# <a name="troubleshoot-ongoing-problems-in-azure-to-azure-vm-replication"></a>Průběžné řešení problémů s v replikaci virtuálních počítačů Azure do Azure

Tento článek popisuje běžné problémy ve službě Azure Site Recovery při replikaci a obnovení virtuálních počítačů Azure z jedné oblasti do jiné oblasti. Také vysvětluje, jak jejich řešení. Další informace o podporovaných konfiguracích najdete v tématu [matice podpory pro replikaci virtuálních počítačů Azure](site-recovery-support-matrix-azure-to-azure.md).

Azure Site Recovery konzistentně replikuje data ze zdrojové oblasti do oblasti pro zotavení po havárii a vytváří bod obnovení konzistentní při selhání každých 5 minut. Pokud Site Recovery nemůže vytvořit body obnovení po dobu 60 minut, upozorní vás pomocí těchto informací:

Chybová zpráva: "Při selhání obnovení konzistentní vzhledem k k dispozici žádný bod virtuálního počítače za posledních 60 minut."</br>
ID chyby: 153007 </br>

Následující části popisují příčiny a řešení.

## <a name="high-data-change-rate-on-the-source-virtal-machine"></a>Četnost změn dat vysoké na zdrojovém virtuálním počítači
Azure Site Recovery vydá událost v případě četnost změn dat na zdrojovém virtuálním počítači je vyšší než podporované limity. Zkontrolujte, jestli je problém z důvodu vysoké četnosti změn, přejděte na **replikované položky** > **VM** > **události – posledních 72 hodin**.
Měli byste vidět události "Frekvence změny dat přesahuje podporované limity":

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Pokud vyberete událost, se měly zobrazit informace přesné disku:

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


### <a name="azure-site-recovery-limits"></a>Omezení Azure Site Recovery
Následující tabulka obsahuje omezení služby Azure Site Recovery. Tato omezení se zakládají na našich testováních, nemůžou však pokrýt všechny možné vstupně-výstupních operací kombinace aplikace. Skutečné výsledky se můžou lišit v závislosti na kombinaci vstupně-výstupních operací vaší aplikace. 

Existují dvě omezení ke zvážení, četnost změn dat na disk a četnost změn dat jednotlivých virtuálních počítačů. Například Podívejme se na disk úrovně Premium P20 v následující tabulce. Site Recovery dokáže zpracovat 5 MB/s pro četností změn na disk s maximálně pěti těmito disky na virtuální počítač, z důvodu omezení 25 MB/s z celkové četnosti změn virtuálního počítače.

**Cíl ukládání replikace** | **Průměrná velikost vstupně-výstupních operací zdrojového disku** |**Průměrná četnost změn dat zdrojového disku** | **Celková četnost změn dat za den pro zdrojový datový disk**
---|---|---|---
Storage úrovně Standard | 8 kB | 2 MB/s | 168 GB na disk
Disk úrovně Premium P10 nebo P15 | 8 kB  | 2 MB/s | 168 GB na disk
Disk úrovně Premium P10 nebo P15 | 16 kB | 4 MB/s |  336 GB na disk
Disk úrovně Premium P10 nebo P15 | 32 kB nebo větší | 8 MB/s | 672 GB na disk
Disk úrovně Premium P20 nebo P30 nebo P40 nebo P50 | 8 kB    | 5 MB/s | 421 GB na disk
Disk úrovně Premium P20 nebo P30 nebo P40 nebo P50 | 16 kB nebo větší |10 MB/s | 842 GB na disk

### <a name="solution"></a>Řešení
Azure Site Recovery má omezení na frekvenci změn dat na základě typu disku. Vědět, pokud tento problém je opakované nebo momentální, najdete že data změnit počet ovlivněných virtuálního počítače. Přejít na zdrojovém virtuálním počítači, vyhledejte metrik v části **monitorování**a přidat metriky, jak je znázorněno na tomto snímku obrazovky:

![Tří kroků pro vyhledání četnost změn dat](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Vyberte **přidat metriku**a přidejte **OS zápis bajtů disku/s** a **Data zapsat bajty disku/s**.
2. Sledování zásobníku, jak je znázorněno na snímku obrazovky.
3. Zobrazení celkové zápisu operace děje napříč disky s operačním systémem a všechny datové disky v kombinaci. Tyto metriky nemusí poskytnout informace na úrovni jednotlivých disků, ale indikuje celkový počet vzor četnosti změn dat.

Pokud prudký nárůst je ze občasné data dávek a data změnit míra je větší než 10 MB/s (pro Premium) a 2 MB/s (pro úroveň Standard) u některých čas a se vrátí, replikace se dohnat. Při provozu i nad rámec podporované, ale ve většině případů omezit, zvažte jednu z těchto možností, pokud je to možné:

* **Vyloučit disk, který je příčinou mírou změn dat vysoké**: Můžete vyloučit disk pomocí [Powershellu](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine).
* **Změna úrovně úložiště disku pro zotavení po havárii**: Tato možnost je možný jenom v případě, že je četnost změn dat disku je menší než 10 MB/s. Řekněme, že virtuální počítač pomocí disku P10 dochází k danému četnost změn dat větší než 8 MB/s, ale menší než 10 MB/s. Pokud zákazník můžete použít P30 disku pro úložiště v cíli během ochrany, můžete problém vyřešen.

## <a name="Network-connectivity-problem"></a>Problémy s připojením k síti

### <a name="network-latency-to-a-cache-storage-account"></a>Latence sítě na účet úložiště mezipaměti
Site Recovery odešle replikovaná data účet úložiště mezipaměti. Může se zobrazit, že latence sítě, pokud chcete nahrát data z virtuálního počítače do účtu úložiště mezipaměti je pomalejší než 4 MB v 3 sekundy. 

Chcete-li zkontrolovat pro problém související s latencí, použijte [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) k odesílání dat z virtuálního počítače do účtu úložiště mezipaměti. Pokud je vysoká latence, zkontrolujte, pokud používáte síťové virtuální zařízení (NVA) k řízení odchozího síťového provozu z virtuálních počítačů. Když se všechny replikace provoz prochází přes síťové virtuální zařízení může získat omezí na zařízení. 

Doporučujeme vytvořit koncový bod služby sítě ve vaší virtuální síti pro "Úložiště" tak, aby provoz replikace nelze přejít na síťové virtuální zařízení. Další informace najdete v tématu [konfiguraci síťového virtuálního zařízení](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration).

### <a name="network-connectivity"></a>Připojení k síti
U replikace Site Recovery pro práci, odchozí připojení ke konkrétní adresy URL nebo IP rozsahy se vyžaduje z virtuálního počítače. Pokud se váš virtuální počítač nachází za bránou firewall nebo používá síť pravidla skupiny zabezpečení (NSG) k řízení odchozího připojení, může setkat některý z těchto problémů. Ujistěte se, že všechny adresy URL připojení, najdete v článku [odchozí připojení k adresám URL služby Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges). 