---
title: Co je Azure Backup?
description: Poskytuje přehled služby Azure Backup a jak ji nasadit jako součást kontinuity podnikových procesů a po havárii (BCDR) strategii zotavení.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 01/09/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 298c9fabca9d1994e0b952fdf8b48b70370c3ec2
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490665"
---
# <a name="what-is-azure-backup"></a>Co je Azure Backup?

Služba Azure Backup zálohuje data do cloudu Microsoft Azure. Můžete zálohovat na místních počítačích a úlohy a Azure virtual machines (VM).


## <a name="why-use-azure-backup"></a>Proč používat Azure Backup?

Azure Backup nabízí tyto klíčové výhody:

- **Zpracování úloh zálohování v místním**: Azure Backup nabízí jednoduché řešení pro zálohování místních prostředků do cloudu. Získejte krátkodobého a dlouhodobého zálohování, aniž by museli nasazovat komplexní místní řešení pro zálohování. Eliminuje nutnost pásky nebo zálohování dat mimo pracoviště.
- **Zálohování virtuálních počítačů Azure IaaS**: Azure Backup poskytuje zálohy nezávislou a izolovanou pro ochranu proti náhodnému odstranění původní data. Zálohy jsou uložené v trezoru služby Recovery Services s integrovanými spravované bodů obnovení. Konfigurace a škálovatelnosti je jednoduché, jsou optimalizované zálohování a můžete je snadno obnovit podle potřeby.
- **Jednoduše škálujte** – Azure Backup používá základní výkon a neomezený rozsah cloudu Azure k zajištění vysoké dostupnosti – s žádné údržbou nebo monitorováním režijní náklady. Můžete nastavit výstrahy pro poskytování informací o událostech, ale nemusíte se starat o vysokou dostupnost svých dat v cloudu.
- **Získejte neomezený přenos dat.** – Azure Backup neomezuje množství příchozích nebo odchozích datových přenosů. V rámci Azure Backup se přenesená data neúčtují. Pokud ale použijete službu importu/exportu v Azure k importu velkého množství dat, účtují se náklady související s příchozími daty. Další informace o těchto nákladech najdete v části [Pracovní postup zálohování offline v Azure Backup](backup-azure-backup-import-export.md). Odchozí data označují data přenášená z trezoru služby Recovery Services během operace obnovení.
- **Zabezpečení dat**: Šifrování dat umožňuje bezpečný přenos a ukládání vašich dat ve veřejném cloudu. Šifrovací heslo máte uložené v místním úložišti a do Azure se nikdy nepřenáší ani se tam neukládá. Pokud je nutné obnovit některá data, máte šifrovací heslo (klíč) k dispozici jen vy.
- **Získejte konzistentní zálohování**: Konzistentní zálohování znamená, že bod obnovení obsahuje všechna požadovaná data pro obnovení záložní kopie. Azure Backup poskytuje zálohy konzistentní s aplikací, které zajišťují, že pro obnovení dat se nevyžadují další opravy. Obnovování dat konzistentních s aplikací zkracuje čas obnovení, což vám umožní rychle se vrátit do funkčního stavu.
- **Získat krátkodobé a dlouhodobé uchovávání**: **Dlouhodobé uchovávání** – Trezory služby Recovery Services můžete použít ke krátkodobému i dlouhodobému uchovávání dat. Azure neomezuje dobu, po kterou data můžou zůstat v trezoru služby Recovery Services. Data můžete v trezoru ponechat, jak dlouho chcete. Služba Azure Backup má omezení 9999 bodů obnovení na chráněnou instanci. Přečtěte si oddíl [Zálohování a uchovávání](backup-introduction-to-azure-backup.md#backup-and-retention) v tomto článku, kde najdete vysvětlení, jak může toto omezení ovlivnit vaše potřeby zálohování.
- **Automatická správa úložiště** – Hybridní prostředí často vyžadují heterogenní úložiště – některá místní a některá cloudová. Azure Backup neznamená žádné náklady na používání místních zařízení úložiště. Azure Backup automaticky přiděluje a spravuje úložiště záloh a používá model založený na průběžných platbách. Průběžné platby znamenají, že platíte jen za úložný prostor, který spotřebujete. Další informace najdete v [článku o cenách Azure](https://azure.microsoft.com/pricing/details/backup).
- **Více možností úložiště** – Jedním z aspektů vysoké dostupnosti je replikace úložiště. Azure Backup nabízí dva typy replikace: [místně redundantní úložiště](../storage/common/storage-redundancy-lrs.md) a [geograficky redundantní úložiště](../storage/common/storage-redundancy-grs.md). Možnost úložiště pro zálohování zvolte podle potřeby:
    - Místně redundantní úložiště (LRS) replikuje vaše data třikrát (vytváří tři kopie dat) v jednotce škálování úložiště v datacentru. Všechny kopie dat existují ve stejné oblasti. Místně redundantní úložiště nabízí cenově úsporný způsob ochrany dat před selháním místního hardwaru.
    - Geograficky redundantní úložiště (GRS) je výchozí a doporučená možnost replikace. GRS replikuje vaše data do sekundární oblasti (vzdálené stovky kilometrů od primárního umístění zdrojových dat). Geograficky redundantní úložiště je nákladnější než místně redundantní úložiště, ale nabízí vyšší úroveň odolnosti dat i v případě regionálního výpadku.


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
**Místní počítače (fyzické nebo virtuální)** |  Můžete zálohovat jednotlivé na místních počítačích.<br/><br/>Můžete zálohovat na místních počítačích, které jsou chráněné službou System Center Data Protection Manager (DPM)<br/><br/> Můžete zálohovat místní počítače chráněné službou Azure Backup Server (MABS)...
**Virtuální počítače Azure** | Můžete zálohovat jednotlivé virtuální počítače Azure.<br/><br/> Můžete zálohovat virtuální počítače Azure, které jsou chráněné službou DPM nebo MABS.

### <a name="back-up-servers"></a>Zálohování serverů

Můžete zálohovat na místních serverech a úlohy, nebo virtuální počítače Azure a jejich pracovní vytížení, nejprve na záložní server a potom na služby Recovery Services trezoru. 

**Zálohování serveru** | **Podrobnosti**
--- | ---
**System Center Data Protection Manager (DPM)** | Azure Backup můžete zálohovat data chráněná aplikací DPM:<br/><br/> – DPM může běžet na místním (fyzický nebo virtuální) nebo v Azure.<br/><br/> -Můžete chránit různé typy dat systémem místních počítačů a virtuálních počítačů Azure pomocí zálohování dat do serveru DPM.<br/><br/> Pak DPM server můžete zálohovat do trezoru služby Recovery Services pomocí služby Azure Backup.<br/><br/> DPM server a na počítače, které chrání musí být ve stejné síti. Místní server DPM může být chráněn pouze na místních počítačích. Podobně virtuální počítače Azure můžete chránit pouze aplikace DPM běžící na Azure.
**Server Microsoft Azure Backup (MABS)** | Azure Backup můžete použít k zálohování dat, který je chráněn MABS:<br/><br/> -MABS může být spuštěn v místním (fyzický nebo virtuální) nebo v Azure.<br/><br/> – Můžete chránit různé typy dat, zálohování dat až po MABS spuštěný na místních počítačů a virtuálních počítačů Azure.<br/><br/> -Zase MABS lze zálohovat do trezoru služby Recovery Services pomocí služby Azure Backup.<br/><br/> -MABS poskytuje podobné funkce aplikace DPM, s tím rozdílem, že nemůžete zálohovat na pásku pomocí MABS. MABS nevyžaduje licenci produktu System Center.<br/><br/> Stejně jako aplikace DPM na místních počítačích pouze se dá chránit v místním MABS. Virtuální počítače Azure se dá chránit jenom MABS v Azure.

Výhody zálohování první do DPM nebo MABS a pak do trezoru se následujícím způsobem:

- Zálohování aplikace DPM/MAB poskytuje s ohledem na aplikace zálohy optimalizovaných pro běžné aplikace, jako je například SQL Server, Exchange a SharePoint, v další soubor/složka/svazek zálohy a zálohy stavu počítač (holý, stav systému).
- Není nutné k instalaci agenta Azure Backup na každém počítači, který chcete zálohovat. Každý počítače běží agent ochrany aplikace DPM nebo MABS a agenta služeb zotavení Azure zálohování Microsoft Azure poběží DPM server/MABS pouze.
- Máte větší flexibilitu a podrobné možnosti plánování pro zálohování.
- Můžete spravovat zálohy pro víc počítačů, které shromáždíte do skupiny ochrany na jediné konzoly. To je zvlášť užitečné, když proběhne aplikací přes víc počítačů a chcete zálohovat společně.

## <a name="what-can-be-backed-up"></a>Co je možné zálohovat?

**Počítač** | **Zálohování serveru** | **Zálohování**
--- | --- | ---
Místní virtuální počítače Windows | Nejsou zálohovány do DPM nebo MABS | Zálohování souborů, složek, stav systému.
Virtuální počítače Azure (Windows nebo Linuxem) | Nejsou zálohovány do DPM nebo MABS | Zálohování souborů, složek, stav systému.<br/><br/> Zálohy jsou aplikace s ohledem na u počítačů s Windows a soubor s ohledem na pro počítače s Linuxem.
Místní virtuální počítače virtuální počítače Azure | Chránit pomocí DPM | Zálohování cokoli, co je chráněná pomocí DPM včetně souborů/složek/sdílené složky nebo svazky a data specifická pro aplikaci. [Přečtěte si](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807) co aplikace DPM může zálohovat.
Místní virtuální počítače virtuální počítače Azure | Chráněné službou MABS | Zálohování cokoli, co je chráněn MABS včetně souborů/složek/sdílené složky nebo svazky a data specifická pro aplikaci. [Přečtěte si](backup-mabs-protection-matrix.md) co může zálohovat MABS.

## <a name="what-backup-agents-do-i-need"></a>Jaké agenty služby backup budu potřebovat?
**Scénář** | **Agent** | **Podrobnosti**
--- | --- | ---
Místní počítače (žádné záložního serveru) | Agent Microsoft Azure Recovery Services (MARS) běží na počítači s Windows. | Stáhněte a nainstalujte agenta MARS během nasazování Azure Backup.<br/><br/> Podpora pro pouze počítače Windows.
Virtuální počítače Azure (bez záložního serveru) | Žádný explicitní agent. Rozšíření Azure VM pro zálohování se spustí na virtuálním počítači Azure. | Rozšíření je nainstalované při spuštění první zálohování virtuálních počítačů Azure.<br/><br/> Podpora pro podporu Windows a Linuxu.
Místní počítače nebo Azure virtuální počítače chráněné pomocí DPM. | MARS agent běží na serveru DPM. | Není nutné agenta MARS na jednotlivých počítačích.<br/><br/> Při nasazování DPM je nainstalován agent ochrany DPM na každém počítači, který chráníte. 
Zálohování místních počítačů a virtuálních počítačů Azure, které jsou chráněné službou MABS | Agenta MARS poběží MABS. | Není nutné agenta MARS na jednotlivých počítačích.<br/><br/> Když nasadíte MABS, je nainstalován agent ochrany MABS na každém počítači, který chráníte. 


## <a name="which-backup-agent-should-i-use"></a>Které agenta zálohování mám použít?

**Backup** | **Řešení** | **Omezení**
--- | --- | ---
Chci, aby pro zálohování místních Windows počítačů. Počítače nejsou chráněné pomocí DPM nebo MABS | Nainstalujte agenta MARS na počítači. | Můžete zálohovat soubory, složky a stavu systému do Azure. Zálohování se s ohledem na aplikace.
Chci, aby pro zálohování místních počítačů s Linuxem. Počítače nejsou chráněné pomocí DPM nebo MABS. | Potřebujete k nasazení aplikace DPM nebo MABS zálohování do Azure.
Chci, aby zálohování aplikace běžící na místních počítačích Windows | Pro aplikace používající zálohování počítačů s Windows musí být chráněn DPM nebo MABS.
Chci, aby k zálohování virtuálních počítačů Azure | Spusťte zálohu pomocí Azure Backup. Rozšíření zálohování se automaticky nakonfigurují na virtuální počítač Azure s Linuxem nebo Windows. | Se zálohují disky virtuálních počítačů.<br/><br/> Pro virtuální počítače s Windows je konzistentní zálohování. pro Linux je konzistentní zálohování. Pokud potřebujete s ohledem na aplikace, budete muset nastavit tuto konfiguraci pomocí vlastních skriptů.
Chci zálohování virtuálních počítačů Azure s využitím flexibility podrobné nastavení zálohování a obnovení | Ochrana virtuálních počítačů Azure pomocí DPM nebo MABS běžící v Azure pro další flexibilitu při plánování zálohování a úplnou flexibilitu pro ochranu a obnovení souborů, složek, svazků, aplikace a stavu systému.


## <a name="next-steps"></a>Další postup

- [Kontrola](backup-architecture.md) architektura a komponenty pro různé scénáře zálohování.
- [Ověřte](backup-support-matrix.md) podporované funkce a nastavení pro zálohování.

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png

