---
title: Co je Azure Backup?
description: Poskytuje přehled služby Azure Backup a jejich podíl na strategii pro provozní kontinuitu a zotavení po havárii (BCDR).
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 04/24/2019
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: a4fbfeb96d2316ce6af100cb16fcbf0d13f230f2
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737121"
---
# <a name="what-is-the-azure-backup-service"></a>Co je služba Azure Backup?

Služba Azure Backup Zálohuje data do cloudu Microsoft Azure. Můžete zálohovat místní počítače a úlohy a virtuální počítače Azure.


## <a name="why-use-azure-backup"></a>Proč používat Azure Backup?

Azure Backup přináší tyto klíčové výhody:

- **Přesměrování místního zálohování**: Azure Backup nabízí jednoduché řešení pro zálohování místních prostředků do cloudu. Získejte krátkodobé a dlouhodobé zálohování bez nutnosti nasazovat složitá místní řešení pro zálohování.
- **Zálohování virtuálních počítačů Azure IaaS**: Azure Backup poskytuje nezávislé a izolované zálohy pro ochranu proti náhodnému zničení původních dat. Zálohy jsou uloženy v Recovery Services trezoru s integrovanými spravovanými body obnovení. Konfigurace a škálovatelnost jsou jednoduché, zálohy jsou optimalizované a můžete je snadno obnovit podle potřeby.
- **Snadné škálování** – Azure Backup využívá základní výkon a neomezené škálování cloudu Azure pro zajištění vysoké dostupnosti bez režie údržby a monitorování.
- **Získání neomezeného přenosu dat**: Azure Backup neomezuje množství příchozích a odchozích dat, která přenášíte, nebo poplatky za přenášená data.
    - Odchozí data označují data přenášená z trezoru služby Recovery Services během operace obnovení.
    - Pokud provádíte offline prvotní zálohování pomocí služby Azure import/export k importu velkých objemů dat, účtují se vám náklady spojené s příchozími daty.  [Další informace](backup-azure-backup-import-export.md).
- Zajištění **zabezpečení dat**: Azure Backup poskytuje řešení pro zabezpečení dat při přenosu a v klidovém provozu.
- **Získání záloh konzistentních vzhledem k aplikacím**: Zálohování konzistentní vzhledem k aplikacím znamená, že bod obnovení obsahuje všechna požadovaná data pro obnovení záložní kopie. Azure Backup poskytuje zálohy konzistentní s aplikací, které zajišťují, že pro obnovení dat se nevyžadují další opravy. Obnovování dat konzistentních s aplikací zkracuje čas obnovení, což vám umožní rychle se vrátit do funkčního stavu.
- **Uchování krátkodobých a dlouhodobých dat**: Trezory Recovery Services můžete použít k krátkodobému a dlouhodobému uchovávání dat. Azure neomezuje dobu, po kterou data můžou zůstat v trezoru služby Recovery Services. Můžete ho uchovávat tak dlouho, jak budete chtít. Služba Azure Backup má omezení 9999 bodů obnovení na chráněnou instanci. 
- **Automatická správa úložiště** – Hybridní prostředí často vyžadují heterogenní úložiště – některá místní a některá cloudová. Azure Backup neznamená žádné náklady na používání místních zařízení úložiště. Azure Backup automaticky přiděluje a spravuje úložiště záloh a používá model průběžných plateb, takže platíte jenom za využité úložiště. [Přečtěte si další informace](https://azure.microsoft.com/pricing/details/backup) o cenách.
- **Více možností úložiště** – Azure Backup nabízí dva typy replikace k zajištění vysoké dostupnosti úložiště/dat.
    - [Místně redundantní úložiště (LRS)](../storage/common/storage-redundancy-lrs.md) replikuje vaše data třikrát (vytváří tři kopie dat) v jednotce škálování úložiště v datacentru. Všechny kopie dat existují ve stejné oblasti. Místně redundantní úložiště nabízí cenově úsporný způsob ochrany dat před selháním místního hardwaru.
    - [Geograficky redundantní úložiště (GRS)](../storage/common/storage-redundancy-grs.md) je výchozí a doporučená možnost replikace. GRS replikuje vaše data do sekundární oblasti (vzdálené stovky kilometrů od primárního umístění zdrojových dat). Geograficky redundantní úložiště je nákladnější než místně redundantní úložiště, ale nabízí vyšší úroveň odolnosti dat i v případě regionálního výpadku.


## <a name="whats-the-difference-between-azure-backup-and-azure-site-recovery"></a>Jaký je rozdíl mezi Azure Backup a Azure Site Recovery?

Azure Backup i Azure Site Recovery služby přispívají k strategii pro provozní kontinuitu a zotavení po havárii (BCDR) ve vaší firmě. BCDR se skládá ze dvou hlavních cílů:

- Zajistěte, aby vaše obchodní data byla bezpečná a obnovitelná, když dojde k výpadku.
- Udržujte aplikace a úlohy v provozu a během plánovaných a neplánovaných výpadků.

Obě služby poskytují doplňkové, ale různé funkce.

- **Azure Site Recovery**: Site Recovery poskytuje řešení zotavení po havárii pro místní počítače a pro virtuální počítače Azure. Počítače se replikují z primárního umístění do sekundárního. Když dojde k výpadku, dojde k selhání počítačů v sekundárním umístění a získáte k nim přístup. Pokud je vše v chodu a normálně spuštěné, dojde k selhání počítačů zpět k jejich obnovení v primární lokalitě.
- **Azure Backup**: Služba Azure Backup Zálohuje data z místních počítačů a virtuálních počítačů Azure. Data je možné zálohovat a obnovovat na detailní úrovni, včetně zálohování souborů, složek, stavu počítače a zálohování dat s ohledem na aplikace. Azure Backup zpracovává data na podrobnější úrovni než Site Recovery. Například pokud se prezentace na přenosném počítači stala poškozená, můžete k jejímu obnovení použít Azure Backup. Pokud chcete zajistit, aby byla konfigurace a data virtuálních počítačů bezpečná a dostupná, můžete použít Site Recovery.  

Použijte body tabulky, které vám pomůžou BCDR potřeby.

**Cíl** | **Podrobnosti** | **Porovnání**
--- | --- | ---
**Zálohování a uchovávání dat** | Záložní data se můžou uchovávat a ukládat po dnech, měsících nebo dokonce i rocích v perspektivě dodržování předpisů. | Řešení pro zálohování, jako je Azure Backup, umožňují přesně vybírat data, která chcete zálohovat, a jemně vyladit zásady zálohování a uchovávání informací.<br/><br/> Site Recovery neumožňuje stejné jemné ladění.
**Cíl bodu obnovení (RPO)** | Přijatelná míra ztráty dat v případě potřeby provedení obnovení. | Zálohy mají více proměnných RPO.<br/><br/> Zálohy virtuálních počítačů mají obvykle plánovaný bod obnovení dne, zatímco zálohy databáze byly RPO po dobu 15 minut.<br/><br/> Site Recovery poskytuje nižší cíl bodu obnovení, protože replikace je nepřetržitá nebo častá, takže rozdíl mezi zdrojem a kopií repliky je malý.
**Cíl doby obnovení (RTO)** |Množství času potřebného k dokončení obnovení nebo zotavení. | Z důvodu vyššího RPO je množství dat, které musí řešení zálohování zpracovat, typicky mnohem vyšší, což vede k delším RTO. Například obnovení dat z pásků může trvat i dny v závislosti na tom, jak dlouho trvá přenos pásku z odlehlého umístění.

## <a name="what-backup-scenarios-are-supported"></a>Jaké scénáře zálohování se podporují?

Azure Backup můžou zálohovat místní počítače a virtuální počítače Azure.

**Počítačové** | **Scénář zálohování**
--- | ---
**Místní zálohování** |  1) spusťte agenta Azure Backup Microsoft Azure Recovery Services (MARS) na místních počítačích s Windows pro zálohování jednotlivých souborů a stavu systému. <br/><br/>2) Zálohujte místní počítače na záložní server (System Center Data Protection Manager (DPM) nebo server Microsoft Azure Backup (MABS)) a pak nakonfigurujte záložní server tak, aby byl v Azure zálohovaný na Azure Backup Recovery Servicesový trezor.
**Virtuální počítače Azure** | 1) Povolte zálohování pro jednotlivé virtuální počítače Azure. Když zapnete zálohování, Azure Backup nainstaluje rozšíření agenta virtuálního počítače Azure, který běží na virtuálním počítači. Agent zálohuje celý virtuální počítač.<br/><br/> 2) spusťte agenta MARS na virtuálním počítači Azure. To je užitečné, pokud chcete zálohovat jednotlivé soubory a složky na virtuálním počítači.<br/><br/> 3) zálohujte virtuální počítač Azure na server DPM nebo MABS spuštěný v Azure. Pak zálohujte server DPM nebo MABS do trezoru pomocí Azure Backup.


## <a name="why-use-a-backup-server"></a>Proč používat záložní server?
Výhody zálohování počítačů a aplikací do úložiště MABS/DPM a pak zálohování úložiště DPM/MABS do trezoru jsou následující:

- Zálohování do MABS/DPM poskytuje zálohování s podporou aplikací optimalizované pro běžné aplikace, jako jsou SQL Server, Exchange a SharePoint, navíc k zálohování souborů, složek a svazků a zálohování stavu počítače (holý počítač, stav systému).
- U místních počítačů nemusíte instalovat agenta MARS do každého počítače, který chcete zálohovat. Každý počítač spustí agenta ochrany aplikace DPM nebo MABS a Agent MARS běží pouze na MABS/DPM.
- Máte větší flexibilitu a podrobné možnosti plánování pro spouštění záloh.
- Zálohy pro více počítačů, které shromáždíte do skupin ochrany, můžete spravovat v jediné konzole. To je užitečné hlavně v případě, že jsou aplikace vrstveny na více počítačích a chcete je zálohovat dohromady.

Přečtěte si další informace o [tom, jak zálohování funguje](backup-architecture.md#architecture-back-up-to-dpmmabs) při používání záložního serveru, a [požadavky na podporu](backup-support-matrix-mabs-dpm.md) pro záložní servery.

## <a name="what-can-i-back-up"></a>Co můžu zálohovat?

**Počítačové** | **Backup – metoda** | **Zálohování**
--- | --- | ---
**Místní virtuální počítače s Windows** | Spustit agenta MARS | Zálohování souborů, složek, stavu systému.<br/><br/> Počítače se systémem Linux nejsou podporovány.
**Místní počítače** | Zálohování do DPM/MABS | Zálohujte všechno, co je chráněné aplikací [DPM](backup-support-matrix-mabs-dpm.md#supported-backups-to-dpm) nebo [MABS](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs), včetně souborů, složek, sdílených složek a svazků a dat specifických pro aplikaci.
**Virtuální počítače Azure** | Spustit záložní rozšíření agenta virtuálního počítače Azure | Zálohování celého virtuálního počítače
**Virtuální počítače Azure** | Spustit agenta MARS | Zálohování souborů, složek, stavu systému.<br/><br/> Počítače se systémem Linux nejsou podporovány.
**Virtuální počítače Azure** | Zálohování na MABS/DPM běžící v Azure | Zálohujte všechno, co je chráněné aplikací [MABS](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs) nebo [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807) , včetně souborů, složek, sdílených složek a svazků a dat specifických pro aplikaci.

## <a name="what-backup-agents-do-i-need"></a>Jakou zálohovací agenti potřebuji?

**Scénář** | **Agent**
--- | ---
**Zálohování virtuálních počítačů Azure** | Není nutné žádného agenta. Rozšíření Azure VM pro zálohování je nainstalovaná na virtuálním počítači Azure při spuštění první zálohy virtuálního počítače Azure.<br/><br/> Podpora pro podporu systému Windows a Linux.
**Zálohování místních počítačů s Windows** | Stáhněte, nainstalujte a spusťte agenta MARS přímo v počítači.
**Zálohování virtuálních počítačů Azure pomocí agenta MARS** | Stáhněte, nainstalujte a spusťte agenta MARS přímo v počítači. Agent MARS může běžet společně s rozšířením zálohování.
**Zálohování místních počítačů a virtuálních počítačů Azure do DPM/MABS** | Agent ochrany DPM nebo MABS je spuštěn na počítačích, které chcete chránit. Agent MARS běží na serveru DPM nebo MABS pro zálohování do Azure.

## <a name="which-backup-agent-should-i-use"></a>Který Agent zálohování mám použít?

**Backup** | **Řešení** | **Omezené**
--- | --- | ---
**Chci zálohovat celý virtuální počítač Azure** | Povolte zálohování pro virtuální počítač. Rozšíření zálohování se automaticky nakonfiguruje na virtuálním počítači s Windows nebo Linuxem Azure. | Celý virtuální počítač je zálohovaný <br/><br/> U virtuálních počítačů s Windows je záloha konzistentní vzhledem k aplikacím. pro Linux je záloha konzistentní soubor. Pokud potřebujete aplikace pro virtuální počítače se systémem Linux, je nutné nakonfigurovat tuto konfiguraci pomocí vlastních skriptů.
**Chci zálohovat konkrétní soubory nebo složky na virtuálním počítači Azure** | Nasaďte agenta MARS na virtuálním počítači.
**Chci přímo Back-premises počítačů s Windows** | Na počítač nainstalujte agenta MARS. | Do Azure můžete zálohovat soubory, složky a stav systému. Zálohy nepodporují aplikace.
**Chci přímo zálohovat místní počítače se systémem Linux** | Abyste mohli zálohovat do Azure, musíte nasadit DPM nebo MABS. | Zálohování hostitele se systémem Linux není podporováno, stačí zálohovat hostovaný počítač se systémem Linux hostovaný na technologii Hyper-V nebo VMWare.
**Chci zálohovat aplikace běžící místně** | Pro záložní počítače s podporou aplikací musí být aplikace DPM nebo MABS chráněny.
**Chci podrobné a flexibilní nastavení zálohování a obnovení pro virtuální počítače Azure** | Chraňte virtuální počítače Azure pomocí MABS/DPM běžícího v Azure, abyste měli větší flexibilitu při plánování zálohování a celou flexibilitu při ochraně a obnovování souborů, složek, svazků, aplikací a stavu systému.

## <a name="backup-and-retention"></a>Zálohování a uchovávání

Azure Backup má limit 9999 bodů obnovení (označovaných také jako záložní kopie nebo snímky) na jednu *chráněnou instanci*.

- Chráněná instance je počítač, server (fyzický nebo virtuální) nebo úloha nakonfigurovaná pro zálohování dat do Azure. Instance je chráněná, jakmile se uloží záložní kopie dat.
- Ochranou je právě tato záložní kopie dat. Pokud dojde ke ztrátě nebo poškození zdrojových dat, je možné je obnovit ze záložní kopie.

Následující tabulka uvádí maximální četnost zálohování pro jednotlivé komponenty. Vaše konfigurace zásad zálohování určuje, jak rychle budete využívat body obnovení. Pokud například vytváříte bod obnovení každý den, můžete zachovat body obnovení 27 let, teprve potom vám dojdou. Pokud vytváříte bod obnovení každý měsíc, můžete zachovat body obnovení 833 let, teprve potom vám dojdou. Služba Backup pro body obnovení nenastavuje omezení času vypršení platnosti.

|  | Agent Azure Backup | System Center DPM | Server Azure Backup | Zálohování virtuálních počítačů Azure IaaS |
| --- | --- | --- | --- | --- |
| Frekvence zálohování<br/> (do trezoru služby Recovery Services) |Tři zálohy za den |Dvě zálohy za den |Dvě zálohy za den |Jedna záloha za den |
| Frekvence zálohování<br/> (na disk) |Neuvedeno |Každých 15 minut pro SQL Server<br/><br/> Každou hodinu pro ostatní úlohy |Každých 15 minut pro SQL Server<br/><br/> Každou hodinu pro ostatní úlohy |Nelze použít |
| Možnosti uchovávání |Denně, týdně, měsíčně, ročně |Denně, týdně, měsíčně, ročně |Denně, týdně, měsíčně, ročně |Denně, týdně, měsíčně, ročně |
| Maximální počet bodů obnovení na chráněnou instanci |9999|9999|9999|9999|
| Maximální doba uchovávání |Závisí na četnosti zálohování |Závisí na četnosti zálohování |Závisí na četnosti zálohování |Závisí na četnosti zálohování |
| Body obnovení na místním disku |Nelze použít | 64 pro souborové servery<br/><br/> 448 pro aplikační servery | 64 pro souborové servery<br/><br/> 448 pro aplikační servery |Nelze použít |
| Body obnovení na pásku |Nelze použít |Unlimited |Nelze použít |Nelze použít |

## <a name="how-does-azure-backup-work-with-encryption"></a>Jak Azure Backup funguje se šifrováním?

**Šifrování** | **Zálohování místně** | **Zálohování virtuálních počítačů Azure** | **Zálohování SQL na virtuálních počítačích Azure**
--- | --- | --- | ---
Šifrování v klidovém stavu<br/> (Šifrování dat, kde se chovají/ukládají) | K šifrování dat se používá heslo zadané zákazníkem. | K šifrování dat uložených v trezoru se používá Azure [šifrování služby Storage (SSE)](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) .<br/><br/> Zálohování automaticky šifruje data před jejich uložením. Před načtením Azure Storage data dešifruje. Použití klíčů spravovaných zákazníkem pro SSE není aktuálně podporováno.<br/><br/> Můžete zálohovat virtuální počítače, které používají [Azure Disk Encryption (ADE)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) k šifrování operačních systémů a datových disků. Azure Backup podporuje virtuální počítače zašifrované pouze klíče bek a klíče bek i [KEK](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/). Zkontrolujte [omezení](backup-azure-vms-encryption.md#encryption-support). | Azure Backup podporuje zálohování databází SQL Server nebo serveru s povoleným [TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) . Zálohování podporuje TDE pomocí klíčů spravovaných pomocí Azure nebo klíčů spravovaných zákazníkem (BYOK).<br/><br/> V rámci procesu zálohování neprovede zálohování žádné šifrování SQL.
Šifrování při přenosu<br/> (Šifrování dat se přesouvá z jednoho umístění do druhého) | Data se šifrují pomocí AES256 a odesílají do trezoru v Azure přes HTTPS. | V rámci Azure je data mezi službou Azure Storage a trezorem chráněná protokolem HTTPS. Tato data zůstávají v páteřní síti Azure.<br/><br/> V případě obnovení souborů chrání iSCSI data přenášená mezi trezorem a virtuálním počítačem Azure. Zabezpečené tunelování chrání kanál iSCSI. | V rámci Azure je data mezi službou Azure Storage a trezorem chráněná protokolem HTTPS.<br/><br/> Obnovení souborů není relevantní pro SQL.

## <a name="next-steps"></a>Další kroky

- [Projděte si](backup-architecture.md) architekturu a součásti pro různé scénáře zálohování.
- [Ověřte](backup-support-matrix.md) požadavky na podporu a omezení pro zálohování a [zálohování virtuálních počítačů Azure](backup-support-matrix-iaas.md).

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png
