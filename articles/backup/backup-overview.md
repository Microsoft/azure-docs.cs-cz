---
title: Co je Azure Backup?
description: Poskytuje přehled služby Azure Backup a jak ji nasadit jako součást kontinuity podnikových procesů a po havárii (BCDR) strategii zotavení.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 02/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 79a53c1b11a1bac0ea83afdd1333423aff4d6aa6
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56428462"
---
# <a name="what-is-azure-backup"></a>Co je Azure Backup?

Služba Azure Backup zálohuje data do cloudu Microsoft Azure. Můžete zálohovat na místních počítačích a úlohy a Azure virtual machines (VM).


## <a name="why-use-azure-backup"></a>Proč používat Azure Backup?

Azure Backup nabízí tyto klíčové výhody:

- **Zpracování úloh zálohování v místním**: Azure Backup nabízí jednoduché řešení pro zálohování místních prostředků do cloudu. Získejte krátkodobého a dlouhodobého zálohování, aniž by museli nasazovat komplexní místní řešení pro zálohování. 
- **Zálohování virtuálních počítačů Azure IaaS**: Azure Backup poskytuje zálohy nezávislou a izolovanou pro ochranu proti náhodnému odstranění původní data. Zálohy jsou uložené v trezoru služby Recovery Services s integrovanými spravované bodů obnovení. Konfigurace a škálovatelnosti je jednoduché, jsou optimalizované zálohování a můžete je snadno obnovit podle potřeby.
- **Jednoduše škálujte** – Azure Backup používá základní výkon a neomezený rozsah cloudu Azure k zajištění vysoké dostupnosti s žádné údržbou nebo monitorováním režijní náklady. 
- **Získejte neomezený přenos dat.** – Azure Backup neomezuje množství příchozích nebo odchozích dat přenosu, nebo za data přenesená.
    - Odchozí data označují data přenášená z trezoru služby Recovery Services během operace obnovení.
    - Pokud provedete import velkých objemů dat pomocí služby Azure Import/Export počáteční offline zálohu, je náklady související s příchozími daty.  [Další informace](backup-azure-backup-import-export.md). 
- **Zabezpečení dat**: Šifrování dat umožňuje bezpečný přenos a ukládání vašich dat ve veřejném cloudu. Šifrovací heslo máte uložené v místním úložišti a do Azure se nikdy nepřenáší ani se tam neukládá. Pokud je nutné obnovit některá data, máte šifrovací heslo (klíč) k dispozici jen vy.
- **Získejte konzistentní zálohování**: Konzistentní zálohování znamená, že bod obnovení obsahuje všechna požadovaná data pro obnovení záložní kopie. Azure Backup poskytuje zálohy konzistentní s aplikací, které zajišťují, že pro obnovení dat se nevyžadují další opravy. Obnovování dat konzistentních s aplikací zkracuje čas obnovení, což vám umožní rychle se vrátit do funkčního stavu.
- **Zachovat data krátkodobé a dlouhodobé**: Trezory služby Recovery Services můžete použít pro uchovávání dat v krátkodobém a dlouhodobém horizontu. Azure neomezuje dobu, po kterou data můžou zůstat v trezoru služby Recovery Services. Abyste mohli pro dlouho budete chtít. Služba Azure Backup má omezení 9999 bodů obnovení na chráněnou instanci. [Další informace](backup-introduction-to-azure-backup.md#backup-and-retention)o tom, jak tento limit ovlivní vaše potřeby zálohování.
- **Automatická správa úložiště** – Hybridní prostředí často vyžadují heterogenní úložiště – některá místní a některá cloudová. Azure Backup neznamená žádné náklady na používání místních zařízení úložiště. Azure Backup automaticky přiděluje a spravuje úložiště záloh a používá model plateb jako využití, takže platíte jenom za úložiště, které skutečně využijete. [Další informace](https://azure.microsoft.com/pricing/details/backup) informace o cenách.
- **Více možností úložiště** – Azure Backup nabízí dva typy replikace pro vaše úložiště/dat s vysokou dostupností.
    - [Místně redundantní úložiště (LRS)](../storage/common/storage-redundancy-lrs.md) replikuje vaše data třikrát (vytváří tři kopie dat) v jednotce škálování úložiště v datovém centru. Všechny kopie dat existují ve stejné oblasti. Místně redundantní úložiště nabízí cenově úsporný způsob ochrany dat před selháním místního hardwaru.
    - [Geograficky redundantní úložiště (GRS)](../storage/common/storage-redundancy-grs.md) je výchozí a doporučená možnost replikace. GRS replikuje vaše data do sekundární oblasti (vzdálené stovky kilometrů od primárního umístění zdrojových dat). Geograficky redundantní úložiště je nákladnější než místně redundantní úložiště, ale nabízí vyšší úroveň odolnosti dat i v případě regionálního výpadku.


## <a name="whats-the-difference-between-azure-backup-and-azure-site-recovery"></a>Jaký je rozdíl mezi Azure Backup a Azure Site Recovery?

Jak Azure Backup a Azure Site Recovery services přispívá ke provozní kontinuitu a strategii (BCDR) zotavení po havárii ve vašem podniku. BCDR se skládá ze dvou různé cíle:

- Udržujte svá firemní data bezpečnost a obnovitelnost, když dojde k výpadku.
- Vaše aplikace a úlohy zůstaly rychle zprovoznit během plánované a neplánované výpadky.

Obě služby poskytují doplňkové, ale jiné funkce.

- **Azure Site Recovery**: Site Recovery poskytuje řešení pro zotavení po havárii pro místní počítače a pro virtuální počítače Azure. Můžete replikovat počítače z primární umístění do sekundární. V případě náhlé havárie selhání počítačů do sekundárního umístění a přistupovat k nim tam. Pokud vše, co je zprovozněný obvykle znovu, můžete počítače selhání zpět a obnovit v primární lokalitě.
- **Azure Backup**: Služba Azure Backup zálohuje data z místních počítačů a virtuálních počítačů Azure. Data můžete zálohovat a obnovit na velmi podrobné úrovni, včetně zálohování souborů, složek, stav systému počítače a zálohování dat s ohledem na aplikace. Azure Backup zpracovává data na podrobnější úrovni než Site Recovery. Například pokud poškozením prezentace na svém přenosném počítači, můžete použít Azure Backup můžete tuto prezentaci obnovit. Pokud chcete zachovat konfiguraci virtuálního počítače a data, bezpečné a přístupné, použijete Site Recovery.  

Pomocí body tabulku zjistit vašim potřebám BCDR. 

**Cíl** | **Podrobnosti** | **Porovnání**
--- | --- | --- | --- |
**Zálohování a uchovávání dat** | Zálohovaná data můžete uchovávat a ukládají dnů, měsíců nebo roků i v případě potřeby z hlediska dodržování předpisů. | Záložní řešení, jako je Azure Backup umožňuje jemně vyberte data, která chcete zálohovat a jemně ladit zásady zálohování a uchovávání.<br/><br/> Site Recovery neumožňuje stejné dolaďování.
**Cíl bodu obnovení (RPO)** | Přijatelná míra ztráty dat v případě potřeby provedení obnovení. | Zálohování mají další proměnnou cíle bodu obnovení.<br/><br/> Zálohy virtuálních počítačů obvykle mají RPO za den, zatímco zálohy databází mají rpo nižší, až 15 minut.<br/><br/> Site Recovery poskytuje nízkou hodnotou RPO, protože je replikace průběžné nebo časté, tak, aby je malý rozdíl mezi kopírovat zdroje a repliky.
**Plánovaná doba obnovení (RTO)** |Množství času potřebného k dokončení obnovení nebo zotavení. | Z důvodu vyššího RPO je množství dat, které musí řešení zálohování zpracovat, typicky mnohem vyšší, což vede k delším RTO. Například obnovení dat z pásků může trvat i dny v závislosti na tom, jak dlouho trvá přenos pásku z odlehlého umístění. | Řešení zotavení po havárii, jako je například Site Recovery mít s nízkou hodnotou RPO, protože replikace průběžné/časté obecně znamená, že se cíl více vysoce synchronizují se zdrojem. |

## <a name="what-backup-scenarios-are-supported"></a>Jaké scénáře zálohování se podporují?

Azure Backup můžete zálohovat místní počítače i virtuální počítače Azure.

**Počítač** | **Zálohování scénář**
--- | ---
**Místní zálohu** |  (1) spusťte agenta Azure Backup Microsoft Azure Recovery Services (MARS) na místním počítače Windows Zálohování jednotlivých souborů a stavu systému. <br/><br/>(2) zálohování místních počítačů k zálohování serveru (System Center Data Protection Manager (DPM) nebo Microsoft Azure Backup Server (MABS)) a potom nakonfigurovat záložní server pro zálohování do trezoru služby Azure Backup Recovery Services v Azure.
**Virtuální počítače Azure** | (1) povolte zálohování pro jednotlivé virtuální počítače Azure. Když povolíte zálohování, Azure Backup nainstaluje rozšíření do agenta virtuálního počítače Azure, na kterém běží na virtuálním počítači. Agent zálohuje celý virtuální počítač.<br/><br/> (2) spusťte agenta MARS na Virtuálním počítači Azure. To je užitečné, pokud chcete zálohování jednotlivých souborů a složek ve virtuálním počítači.<br/><br/> (3) zálohování virtuálního počítače Azure do serveru DPM nebo MABS běžící v Azure. Pak zálohujte server DPM nebo MABS do trezoru zálohování Azure. 


## <a name="why-use-a-backup-server"></a>Proč používat zálohování serveru?




Výhody zálohování počítačů a aplikací MABS/DPM úložiště a zálohování aplikace DPM nebo MABS úložiště do trezoru se následujícím způsobem:

- Zálohování MABS/DPM poskytuje s ohledem na aplikace zálohy optimalizovaných pro běžné aplikace, jako je například SQL Server, Exchange a SharePoint, v další soubor/složka/svazek zálohy a zálohy stavu počítač (holý, stav systému).
- Pro místní mahines není nutné instalovat agenta MARS na každém počítači, který chcete zálohovat. Spuštění agenta ochrany aplikace DPM nebo MABS jednotlivých počítačů a agenta MARS běží na MABS/DPM pouze.
- Máte větší flexibilitu a podrobné možnosti plánování pro zálohování.
- Můžete spravovat zálohy pro víc počítačů, které shromáždíte do skupiny ochrany na jediné konzoly. To je zvlášť užitečné, když proběhne aplikací přes víc počítačů a chcete zálohovat společně.

Další informace o [jak zálohování funguje](backup-architecture.md#architecture-back-up-to-dpmmabs) při používání zálohování serveru a [požadavky na podporu](backup-support-matrix-mabs-dpm.md) pro záložní servery.

## <a name="what-can-i-back-up"></a>Co můžu zálohovat?

**Počítač** | **Backup – metoda** | **Zálohování**
--- | --- | ---
**Místní virtuální počítače Windows** | Spuštění agenta MARS | Zálohování souborů, složek, stav systému.<br/><br/> Počítače s Linuxem není podporován.
**Místní počítače** | Zálohování do DPM nebo MABS | Proveďte zálohu cokoli, co je chráněn [DPM](backup-support-matrix-mabs-dpm.md#supported-backups-to-dpm) nebo [MABS](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs), včetně souborů/složek/sdílené složky nebo svazky a data specifická pro aplikaci. 
**Virtuální počítače Azure** | Spuštění rozšíření agenta zálohování virtuálních počítačů Azure | Zálohovat celý virtuální počítač
**Virtuální počítače Azure** | Spuštění agenta MARS | Zálohování souborů, složek, stav systému.<br/><br/> Počítače s Linuxem není podporován.
**Virtuální počítače Azure** | Zálohování do MABS/aplikace DPM běžící v Azure | Proveďte zálohu cokoli, co je chráněn [MABS](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs) nebo [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807) včetně dat soubory/složky/složky/svazky a konkrétní aplikace.

## <a name="what-backup-agents-do-i-need"></a>Jaké agenty služby backup budu potřebovat?

**Scénář** | **Agent** 
--- | --- 
**Zálohování virtuálních počítačů Azure** | Není potřeba žádný agent. Rozšíření virtuálního počítače Azure Backup je nainstalovaný na virtuálním počítači Azure při spuštění první zálohování virtuálních počítačů Azure.<br/><br/> Podpora pro podporu Windows a Linuxu.
**Zálohování místních Windows počítačů** | Stažení, instalace a spuštění agenta MARS přímo na počítači. 
**Zálohování virtuálních počítačů Azure pomocí agenta MARS** | Stažení, instalace a spuštění agenta MARS přímo na počítači. Agenta MARS lze spustit souběžně s linka záložního telefonu.
**Zálohování místních počítačů a virtuálních počítačů Azure do DPM nebo MABS** | Agent ochrany aplikace DPM nebo MABS běží na počítačích, které chcete chránit. MARS agent se spouští na serveru/MABS DPM pro zálohování do Azure.

## <a name="which-backup-agent-should-i-use"></a>Které agenta zálohování mám použít?

**Backup** | **Řešení** | **Omezení**
--- | --- | ---
**Chci zálohovat celý virtuální počítač Azure** | Povolte zálohování virtuálního počítače. Rozšíření zálohování se automaticky nakonfigurují na virtuální počítač Azure s Linuxem nebo Windows. | Je-li zálohovat celý virtuální počítač <br/><br/> Pro virtuální počítače s Windows je konzistentní zálohování. pro Linux je konzistentní zálohování. Pokud potřebujete s ohledem na aplikace pro virtuální počítače s Linuxem, budete muset nastavit tuto konfiguraci pomocí vlastních skriptů.
**Chci, aby pro zálohování konkrétní soubory nebo složky na virtuálním počítači Azure** | Nasazení agenta MARS na virtuálním počítači.
**Chci přímo zpět na místních počítačích Windows** | Nainstalujte agenta MARS na počítači. | Můžete zálohovat soubory, složky a stavu systému do Azure. Zálohování se s ohledem na aplikace.
**Chci přímo zálohování místních počítačů s Linuxem** | Potřebujete k nasazení aplikace DPM nebo MABS zálohování do Azure.
**Chci, aby zálohování aplikace běžící na místních** | S ohledem na aplikace Zálohování počítačů musí být chráněn DPM nebo MABS.
**Chci, aby podrobné a flexibilní nastavení zálohování a obnovení pro virtuální počítače Azure** | Ochrana virtuálních počítačů Azure pomocí MABS/aplikace DPM běžící na Azure pro větší flexibilita při plánování zálohování a úplnou flexibilitu pro ochranu a obnovení souborů, složek, svazků, aplikace a stavu systému.


## <a name="next-steps"></a>Další postup

- [Kontrola](backup-architecture.md) architektura a komponenty pro různé scénáře zálohování.
- [Ověřte](backup-support-matrix.md) podporované funkce a nastavení pro zálohování.

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png

