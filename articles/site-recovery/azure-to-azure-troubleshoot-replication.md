---
title: Azure Site Recovery pro odstraňování potíží v potíže s replikací z Azure do Azure a chyby | Dokumentace Microsoftu
description: Řešení potíží s chyb a problémů při replikaci virtuálních počítačů Azure pro zotavení po havárii
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/30/2018
ms.author: asgang
ms.openlocfilehash: 22ea3d955fe2910dc99ab4015165008da899d48e
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52312846"
---
# <a name="troubleshoot-azure-to-azure-vm-ongoing-replication-issues"></a>Řešení potíží s probíhající replikaci virtuálních počítačů Azure do Azure

Tento článek popisuje běžné problémy ve službě Azure Site Recovery při replikaci a obnovení virtuálních počítačů Azure z jedné oblasti do jiné oblasti a vysvětluje, jak jejich řešení. Další informace o podporovaných konfiguracích najdete v tématu [matice podpory pro replikaci virtuálních počítačů Azure](site-recovery-support-matrix-azure-to-azure.md).


## <a name="recovery-points-not-getting-generated"></a>Body obnovení aplikace vygenerované

CHYBOVÁ zpráva: Selhání obnovení konzistentní vzhledem k k dispozici žádný bod virtuálního počítače za posledních 60 minut.</br>
ID CHYBY: 153007 </br>

Azure Site Recovery konzistentně replikuje data ze zdrojové oblasti do oblasti pro zotavení po havárii a vytváří konzistentní pro případ chyby bodu každých 5 minut. Pokud se Site Recovery je schopna vytvářet body obnovení po dobu 60 minut, upozorní uživatele. V následující tabulce jsou příčiny, které by mohly vést k této chybě:

**1. příčina: [vysoké údaje změnit rychlost na zdrojovém virtuálním počítači](#high-data-change-rate-on-the-source-virtal-machine)**    
**2. příčina: [problém se sítí ](#Network-connectivity-issue)**

## <a name="causes-and-solutions"></a>Příčiny a řešení

### <a name="high-data-change-rate-on-the-source-virtal-machine"></a>Vysoké údaje změnit rychlost v zdrojového virtuálního počítače.
Azure Site Recovery vydá událost v případě četnost změn dat na zdrojovém virtuálním počítači je vyšší než podporované limity. Pokud chcete zkontrolovat, pokud je problém způsobený vysokou četností změn dat, přejděte na replikované položky > virtuální počítač > klikněte na "události – posledních 72 hodin".
Rychlost těchto událostí"změny dat přesahuje podporované limity" měli byste vidět, jak je znázorněno v následujícím snímku obrazovky

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Pokud kliknete na událost, informace o přesnou disku měli vidět, jak je znázorněno v následujícím snímku obrazovky

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


#### <a name="azure-site-recovery-limits"></a>Omezení Azure Site Recovery
Následující tabulka obsahuje omezení služby Azure Site Recovery. Tato omezení se zakládají na našich testováních, nemůžou však pokrýt všechny možné kombinace vstupně-výstupních operací aplikace. Skutečné výsledky se můžou lišit v závislosti na kombinaci vstupně-výstupních operací vaší aplikace. Můžeme také nezapomeňte přitom, že existují dvě omezení ke zvážení na disk, četnost změn dat a za virtuální počítač data churn.
Například, když se podíváte na disk úrovně Premium P20 v níže uvedená tabulka, může Site Recovery pro zpracování změn 5 MB/s na disk s maximálně pěti těmito disky na virtuální počítač kvůli omezení 25 MB/s celková četnost změn na virtuální počítač.

**Cíl ukládání replikace** | **Průměrná velikost vstupně-výstupních operací zdrojového disku** |**Průměrná četnost změn dat zdrojového disku** | **Celková denní četnost změn dat zdrojového disku**
---|---|---|---
Storage úrovně Standard | 8 kB | 2 MB/s | 168 GB na disk
Disk úrovně Premium P10 nebo P15 | 8 kB  | 2 MB/s | 168 GB na disk
Disk úrovně Premium P10 nebo P15 | 16 kB | 4 MB/s |  336 GB na disk
Disk úrovně Premium P10 nebo P15 | 32 kB nebo větší | 8 MB/s | 672 GB na disk
Disk úrovně Premium P20 nebo P30 nebo P40 nebo P50 | 8 kB    | 5 MB/s | 421 GB na disk
Disk úrovně Premium P20 nebo P30 nebo P40 nebo P50 | 16 kB nebo větší |10 MB/s | 842 GB na disk

### <a name="solution"></a>Řešení
Musíte rozumí tomu, že Azure Site Recovery obsahuje data změnit šířku pásma na základě typu disku. Pokud chcete vědět, pokud se tento problém opakuje, nebo okamžik, je potřeba najít změn dat míra vzor ovlivněných virtuálních počítačů.
Najít data měnit počet ovlivněných virtuálního počítače. Přejděte na zdrojovém virtuálním počítači > metrik v části monitorování a přidat metriky, jak je znázorněno níže.

![high_data_change_rate](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Klikněte na "Add metric" a přidejte "Disk s operačním systémem zapsat bajty/s" a "Datový Disk zapsat bajty/s".
2. Sledování zásobníku, jak je znázorněno na snímku obrazovky.
3. Zobrazí se, že se že celkový počet zapíše operace děje napříč disk s operačním systémem a všechny datové disky v kombinaci. Nyní tyto metriky nemusí informace na úrovni informace o disku ale jasně ukazuje celkové množství dat vzor četnosti změn.

V případech, jako je vyšší než pokud je občasnému dat shluků a četnost změn dat je větší než 10 MB/s (pro Premium) a 2 MB/s (pro úroveň Standard) nechystáte nějakou dobu se vrátí, replikace se dohnat. Ale pokud provozu i nad rámec podporovaný limit je ve většině případů, pak zvažte jednu z níže možnost, pokud je to možné:

**Možnost 1:** vyloučit disk, který je příčinou četnost změn dat vysoké: </br>
Aktuálně můžete vyloučit disk pomocí [Powershellu pro Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine)

**Možnost 2:** změnit úroveň úložiště disku pro zotavení po havárii: </br>
Tato možnost je možné, pouze pokud četnost změn dat disku je menší než 10 MB/s. Umožní Řekněme, že virtuální počítač s P10 disk má četnost změn dat větší než 8 MB/s, ale menší než 10 MB/s. Pokud zákazník můžete použít P30 disku pro úložiště v cíli během ochrany, můžete problém vyřešit.

### <a name="Network-connectivity-issue"></a>Problém síťového připojení

#### <a name="network-latency-to-cache-storage-account-"></a>Latence sítě do účtu úložiště mezipaměti:
 Site Recovery odešle replikovaná data účet úložiště mezipaměti a problému může dojít, pokud nahrávání dat z virtuálního počítače do účtu úložiště mezipaměti je pomalejší, že 4 MB v 3 sekundy. Zkontrolujte, jestli je jakýkoli problém související s latencí použití [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) k odesílání dat z virtuálního počítače do účtu úložiště mezipaměti.<br>
Pokud je vysoká latence, zkontrolujte, pokud používáte síťových virtuálních zařízení k řízení odchozího síťového provozu z virtuálních počítačů. Když se všechny replikace provoz prochází přes síťové virtuální zařízení může získat omezí na zařízení. Doporučujeme vytvořit koncový bod služby sítě ve vaší virtuální síti pro "Úložiště" tak, aby provoz replikace se nepřenáší na síťové virtuální zařízení. Přečtěte si [konfiguraci síťového virtuálního zařízení](https://docs.microsoft.com/en-us/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration)

#### <a name="network-connectivity"></a>Připojení k síti
U replikace Site Recovery pro práci, odchozí připojení ke konkrétní adresy URL nebo IP rozsahy se vyžaduje z virtuálního počítače. Pokud se váš virtuální počítač nachází za bránou firewall nebo používá síť pravidla skupiny zabezpečení (NSG) k řízení odchozího připojení, může setkat jednu z těchto problémů.</br>
Odkazovat na [odchozí připojení k adresám URL služby Site Recovery](https://docs.microsoft.com/en-us/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) k Ujistěte se, že všechny adresy URL jsou připojené. 