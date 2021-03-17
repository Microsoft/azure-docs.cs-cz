---
title: Glosář Azure Backup
description: Tento článek popisuje výrazy užitečné pro použití s Azure Backup.
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: fb46415c8bdb463556d57004e37d741c1b9a9b57
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102502021"
---
# <a name="azure-backup-glossary"></a>Glosář Azure Backup

Tento Glosář termínů může být užitečný při použití Azure Backup.

>[!NOTE]
>
> - Podmínky označené předponou (termín specifický pro pracovní zatížení) odkazují na tyto podmínky, které jsou relevantní pouze v kontextu konkrétní podmnožiny úloh, které Azure Backup podporuje.
> - Výrazy, které se běžně používají v dokumentaci Azure Backup, ale odkazují na další služby Azure, poskytuje přímý odkaz na dokumentaci relevantní služby Azure.

## <a name="afs-azure-file-shares"></a>AFS (sdílené složky Azure)

Informace najdete v [dokumentaci ke službě soubory Azure](../storage/files/storage-files-introduction.md).

## <a name="alternate-location-recovery"></a>Obnovení alternativního umístění

Obnovení provedené z bodu obnovení do jiného umístění, než je původní umístění, ve kterém byly zálohy provedeny. Při použití zálohování virtuálních počítačů Azure to by znamenalo, že se virtuální počítač obnovuje na jiný server, než na původní server, na kterém se zálohují zálohy. Při použití zálohování sdílené složky Azure to by znamenalo obnovení dat do sdílené složky, která se liší od zálohovaných sdílených souborů.

## <a name="application-consistent-backup"></a>Zálohování konzistentní s aplikací

(Termín specifický pro úlohy)

Zálohování konzistentní s aplikací zaznamenává obsah paměti a probíhající vstupně-výstupní operace. Snímky konzistentní vzhledem k aplikacím používají [zapisovač VSS VSS](#vss-windows-volume-shadow-copy-service) (nebo předzálohovací skripty pro Linux), aby se zajistila konzistence dat aplikací před tím, než dojde k zálohování. [Další informace](backup-azure-vms-introduction.md).

## <a name="azure-resource-manager-arm-templates"></a>Šablony Azure Resource Manageru (ARM)

Informace najdete v [dokumentaci k šablonám ARM](../azure-resource-manager/templates/overview.md).

## <a name="autoprotection-for-databases"></a>AutoProtection (pro databáze)

(Termín specifický pro úlohy)

Automatická ochrana je možnost, která umožňuje automaticky chránit všechny databáze v samostatné instanci SQL Server nebo skupině dostupnosti Always On SQL Server. Nejen umožňuje zálohování pro existující databáze, ale také chrání všechny databáze, které můžete v budoucnu přidat.

## <a name="availability-storage-replication-types"></a>Dostupnost (typy replikace úložiště)

Azure Backup nabízí tři typy replikace k zajištění vysoké dostupnosti úložiště a dat:

### <a name="lrs"></a>LRS

[Místně redundantní úložiště (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) replikuje zálohovaná data třikrát (vytváří tři kopie zálohovaných dat) v jednotce škálování úložiště v datacentru. Všechny kopie zálohovaných dat existují ve stejné oblasti. LRS je cenová možnost pro ochranu zálohovaných dat před selháním místního hardwaru.

### <a name="grs"></a>GRS

[Geograficky redundantní úložiště (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage) je výchozí a doporučená možnost replikace. GRS replikuje data záloh do sekundární oblasti a stovky kilometrů od primárního umístění zdrojových dat. GRS náklady jsou větší než LRS, ale GRS poskytuje vyšší úroveň trvanlivosti pro data záloh, i když dojde k výpadku v regionu.

>[!NOTE]
>U trezorů GRS, které mají povolenou funkci obnovení mezi oblastmi, se úložiště zálohování upgraduje z GRS na RA-GRS (úložiště pro přístup pro čtení Geo-Redundant).

### <a name="zrs"></a>ZRS

[Zóna – redundantní úložiště (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage) replikuje vaše Zálohovaná data do [zón dostupnosti](../availability-zones/az-overview.md#availability-zones)a zaručuje jejich započet a odolnost záložních dat ve stejné oblasti. Důležité úlohy, které vyžadují zastavování [dat](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) , se dají zálohovat v ZRS.

## <a name="azure-command-line-interface-cli"></a>Rozhraní příkazového řádku Azure (CLI)

Informace najdete v dokumentaci k rozhraní příkazového [řádku Azure](/cli/azure/what-is-azure-cli).

## <a name="azure-policy"></a>Azure Policy

Informace o [Azure Policy](../governance/policy/overview.md)najdete v dokumentaci.

## <a name="azure-powershell"></a>Azure PowerShell

Informace o [Azure PowerShell](/powershell/azure/)najdete v dokumentaci.

## <a name="azure-resource-manager-arm"></a>Azure Resource Manager (ARM)

Informace o [Azure Resource Manager](../azure-resource-manager/management/overview.md)najdete v dokumentaci.

## <a name="azure-disk-encryption-ade"></a>Azure Disk Encryption (ADE)

Informace o [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md)najdete v dokumentaci.

## <a name="backend-storage--cloud-storage--backup-storage"></a>Úložiště back-endu/cloudové úložiště/úložiště zálohování

Skutečné úložiště používané instancí zálohy. Zahrnuje velikost všech bodů uchovávání dat, které existují pro instanci zálohování (jak jsou definovány v zásadách zálohování a uchovávání informací).

## <a name="bare-metal-backup"></a>Úplné zálohování systému

Zálohuje soubory operačního systému a všechna data na kritických svazcích, s výjimkou uživatelských dat. V rámci definice zahrnuje úplné zálohování systému zálohu stavu systému. Poskytuje ochranu v případě, že se počítač nespustí a vy budete muset všechno obnovit. [Další informace](backup-mabs-system-state-and-bmr.md).

## <a name="backup-extensions--vm-extensions"></a>Rozšíření pro zálohování/rozšíření virtuálních počítačů

(Specifické pro typ úlohy virtuálních počítačů Azure)

Rozšíření virtuálních počítačů Azure jsou malé aplikace, které na virtuálních počítačích Azure umožňují provádět úlohy konfigurace a automatizace po nasazení. Azure Backup zálohuje virtuální počítače Azure tím, že nainstaluje rozšíření na agenta virtuálního počítače Azure, který běží na počítači. Azure Backup tato rozšíření automaticky spravuje a uživatelé nemusejí tato rozšíření aktualizovat ručně.

## <a name="backup-instance--backup-item"></a>Instance zálohy/zálohovaná položka

Zdroj dat zálohovaný do trezoru s konkrétní zásadou zálohování a uchovávání představuje instanci zálohy nebo zálohovanou položku.

## <a name="backup-rule--backup-policy"></a>Pravidlo zálohování/zásady zálohování

Pravidlo zálohování je uživatelsky definované pravidlo, které určuje, kdy a jak často se mají zálohy provádět na zdrojovém zdroji dat. U některých typů úloh poskytuje zásada zálohování také způsob, jak určit metodu snímku, která se má použít pro zdroj dat (úplná, přírůstková, rozdílová). Zásady zálohování se často vytvářejí jako kombinace pravidel zálohování a pravidel uchovávání.

## <a name="backup-vault"></a>Trezor služby Backup

Azure Resource Manager prostředku typu *Microsoft. DataProtection/BackupVaults*. V současné době se trezory služby Backup používají k zálohování databází Azure pro server PostgreSQL. [Přečtěte si další informace o úložištích služby Backup](backup-azure-recovery-services-vault-overview.md).

## <a name="bcdr-business-continuity-and-disaster-recovery"></a>BCDR (provozní kontinuita a zotavení po havárii)

BCDR zahrnuje sadu procesů, které musí organizace přijmout, aby se zajistilo, že aplikace a úlohy budou spuštěné během plánovaných i neplánovaných služeb nebo výpadků Azure s minimálním dopadem na chod firmy. [Další informace](https://azure.microsoft.com/solutions/backup-and-disaster-recovery/#overview) o různých službách, které Azure nabízí, vám pomůžou vytvořit BCDR strategii pro zvukové služby.

## <a name="churn"></a>Mísení

Procentuální hodnota změny v zálohovaných datech mezi dvěma po sobě jdoucími zálohami. To může být způsobeno přidáním nových dat nebo úpravou nebo odstraněním stávajících dat.

## <a name="crash-consistent-backup"></a>Zálohování konzistentní se selháním

(Termín specifický pro úlohy)

K snímkům konzistentním se selháním obvykle dochází v případě, že se virtuální počítač Azure vypíná v době zálohování. Budou zachycena a zálohována pouze data, která na disku již existují v době zálohování. [Další informace](backup-azure-vms-introduction.md#snapshot-consistency).

## <a name="cross-region-restore-crr"></a>Obnovení mezi oblastmi (CRR)

Jedna z [možností obnovení](backup-azure-arm-restore-vms.md#restore-options)(crr) vám umožňuje obnovit zálohované položky v sekundární oblasti, která je [spárována se službou Azure](../best-practices-availability-paired-regions.md#what-are-paired-regions).

## <a name="data-box"></a>Data box

Informace najdete v [dokumentaci k data box](../databox/data-box-overview.md).

## <a name="datasource"></a>Zdroj dat

Prostředek (prostředek Azure, prostředek proxy serveru nebo místní prostředek), který je kandidátem pro zálohování. Například virtuální počítač Azure nebo sdílená složka Azure.

## <a name="dpm-data-protection-manager"></a>DPM (Data Protection Manager)

(Termín specifický pro úlohy)

Informace najdete v [dokumentaci k aplikaci DPM](/system-center/dpm/dpm-overview).

## <a name="expressroute"></a>ExpressRoute

Přečtěte si [dokumentaci k ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="file-system-consistent-backup"></a>Zálohování konzistentní se systémem souborů

(Termín specifický pro úlohy)

Zálohy konzistentní se systémem souborů poskytují konzistenci tím, že pořizuje všechny soubory ve stejnou dobu. [Další informace](backup-azure-vms-introduction.md#snapshot-consistency).

## <a name="frontend-storage--source-size"></a>Front-endové úložiště/zdrojová velikost

Velikost dat, která mají být zálohována pro zdroj dat. Velikost front-endu objektu DataSource určuje jeho počet [chráněných instancí](#protected-instance) .

## <a name="full-backup"></a>Úplné zálohování

V úplném zálohování je kopie celého zdroje dat uložena pro každou zálohu.

## <a name="gfs-backup-policy"></a>Zásady zálohování GFS

Zásada zálohování GFS (dědečka-otce-syn) je ta, která umožňuje kromě denního plánu zálohování definovat týdenní, měsíční a roční plány zálohování. Týdenní zálohy jsou známé jako "Sons", měsíční zálohy se označují jako "otců" a roční zálohy se označují jako "grandfathers". Každou z těchto sad záložních kopií je možné nakonfigurovat tak, aby se zachovaly po různých časových obdobích, a umožňují další přizpůsobení možností uchování záložních kopií. Zásady GFS jsou užitečné pro uchovávání záloh po dlouhou dobu, a to s větším efektivním způsobem úložiště.

## <a name="iaas-vms--azure-vms"></a>Virtuální počítače s IaaS nebo virtuální počítače Azure

Přečtěte si [dokumentaci k virtuálnímu počítači Azure](../virtual-machines/index.yml).

## <a name="incremental-backup"></a>Přírůstkové zálohování

Přírůstkové zálohy ukládají pouze bloky, které se od předchozí zálohy změnily.

## <a name="instant-restore"></a>Okamžité obnovení

(Termín specifický pro úlohy) Rychlé obnovení zahrnuje obnovení počítače přímo z jeho záložního snímku, nikoli z kopie snímku v trezoru. Rychlé obnovení jsou rychlejší než obnovení z trezoru. Počet dostupných bodů okamžitého obnovení závisí na době uchování nastavené pro snímky. Aktuálně platí jenom pro zálohování virtuálních počítačů Azure.

## <a name="iops"></a>IOPS

Vstupně-výstupní operace za sekundu.

## <a name="item-level-restore"></a>Obnovení na úrovni položek

(Termín specifický pro úlohy)

Obnovení jednotlivých souborů nebo složek v počítači z bodu obnovení.

## <a name="job"></a>Úloha

Úkol související se zálohou, který je vytvořen uživatelem nebo Azure Backup službou. Úlohy mohou být buď naplánované, nebo na vyžádání (ad hoc). Existují různé typy úloh – zálohování, obnovení, konfigurace ochrany atd. [Přečtěte si další informace o úlohách](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault).

## <a name="mabs--azure-backup-server"></a>MABS/Azure Backup Server

(Termín specifický pro úlohy)

Pomocí Azure Backup Server můžete chránit pracovní zatížení aplikací, jako jsou například virtuální počítače Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange a klienti Windows z jediné konzoly. Z aplikace DPM zdědí většinu funkcí zálohování úloh, ale několik rozdílů. [Další informace](backup-azure-microsoft-azure-backup.md)

## <a name="managed-disks"></a>Spravované disky

Informace najdete v [dokumentaci ke službě Managed disks](../virtual-machines/managed-disks-overview.md).

## <a name="mars-agent"></a>Agent MARS

(Termín specifický pro úlohy)

Označuje se taky jako agent **Azure Backup agenta** nebo agent **Recovery Services**, Azure Backup k zálohování dat z místních počítačů a virtuálních počítačů Azure do záložního Recovery Services trezoru v Azure. [Další informace](backup-support-matrix-mars-agent.md).

## <a name="nsg-network-security-group"></a>NSG (skupina zabezpečení sítě)

Přečtěte si [dokumentaci k NSG](../virtual-network/network-security-groups-overview.md).

## <a name="offline-seeding"></a>Offline osazení

Dosazení offline se týká procesu přenosu počáteční (úplné) zálohy offline bez použití šířky pásma sítě. Poskytuje mechanismus pro kopírování zálohovaných dat do fyzických úložných zařízení, která se pak odesílají do nejbližšího datového centra Azure a nahrála se do úložiště Recovery Services. [Další informace](offline-backup-overview.md).

## <a name="on-demand-backup--ad-hoc-backup"></a>Zálohování na vyžádání/zálohování ad hoc

Úloha zálohování, která se aktivuje uživatelem v jednorázovém časovém intervalu, a ne na základě plánu zálohování (zásady), který je pro prostředek nakonfigurovaný.

## <a name="original-location-recovery-olr"></a>Obnovení původního umístění (OLR)

Obnovení provedené z bodu obnovení do zdrojového umístění, ze kterého byly zálohy pořízeny, a jejich nahrazení stavem uloženým v bodu obnovení. Při použití zálohování virtuálních počítačů Azure to by znamenalo obnovení virtuálního počítače na původní server, na kterém se prováděly zálohy. Při použití zálohování sdílené složky Azure to by znamenalo obnovení dat do zálohované sdílené složky.

## <a name="passphrase"></a>Hesel

(Termín specifický pro úlohy)

K šifrování a dešifrování dat se používá přístupové heslo při zálohování nebo obnovení místního nebo místního počítače pomocí agenta MARS do systému nebo z Azure.

## <a name="private-endpoint"></a>Privátní koncový bod

Přečtěte si [dokumentaci k privátnímu koncovému bodu](../private-link/private-endpoint-overview.md).

## <a name="protected-instance"></a>Chráněná instance

Chráněná instance odkazuje na počítač, fyzický nebo virtuální server, který používáte ke konfiguraci zálohování na Azure.  Z **hlediska fakturace** je počet chráněných instancí pro počítač funkcí jeho velikosti front-endu. Jedna instance zálohy (například virtuální počítač zálohovaný do Azure) může odpovídat několika chráněným instancím v závislosti na velikosti front-endu. [Další informace](https://azure.microsoft.com/pricing/details/backup/).

## <a name="rbac-role-based-access-control"></a>RBAC (řízení přístupu na základě role)

Informace najdete v [dokumentaci RBAC](../role-based-access-control/overview.md).

## <a name="recovery-point-restore-point-retention-point--point-in-time-pit"></a>Bod obnovení/bod obnovení/bod uchování/v čase (PECKY)

Kopie původních zálohovaných dat. Bod uchovávání informací je přidružený k časovému razítku, takže ho můžete použít k obnovení položky do určitého bodu v čase.

## <a name="recovery-services-vault"></a>Trezor služby Recovery Services

Azure Resource Manager prostředku typu *Microsoft. RecoveryServices/trezory*. V současné době se Recovery Services trezory používají k zálohování následujících úloh: virtuální počítače Azure, SQL ve virtuálních počítačích Azure, SAP HANA ve virtuálních počítačích Azure a sdílené složky Azure. Používá se také k zálohování místních úloh pomocí MARS, Azure Backup Server (MABS) a softwaru System Center DPM. [Přečtěte si další informace o úložištích Recovery Services](backup-azure-recovery-services-vault-overview.md).

## <a name="resource-group"></a>Skupina prostředků

Informace najdete v [dokumentaci k Azure Resource Manager](../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group).

## <a name="rest-api"></a>REST API

Informace najdete v [dokumentaci k Azure REST API](/rest/api/azure/).

## <a name="retention-rule"></a>Pravidlo uchovávání informací

Uživatelsky definované pravidlo, které určuje, jak dlouho se mají uchovávat zálohy.

## <a name="rpo-recovery-point-objective"></a>RPO (cíl bodu obnovení)

RPO označuje maximální ztrátu dat, která je možná ve scénáři ztráty dat. Tato služba je určena četností zálohování.

## <a name="rto-recovery-time-objective"></a>RTO (cíl doby obnovení)

RTO označuje maximální možnou dobu, během které mohou být data obnovena k poslednímu dostupnému bodu v čase po ztrátě dat.

## <a name="scheduled-backup"></a>Naplánované zálohování

Úloha zálohování, která se automaticky aktivuje zásadami zálohování nakonfigurovanými pro danou položku.

## <a name="secondary-region--paired-region"></a>Sekundární oblast/spárovaná oblast

Regionální pár se skládá ze dvou oblastí ve stejné zeměpisné oblasti. Jedna je primární oblastí a druhá je sekundární oblastí. Spárované oblasti jsou používány některými službami Azure (včetně Azure Backup s nastaveními GRS), aby se zajistila Kontinuita podnikových aplikací a chránila před ztrátou dat. [Další informace](../best-practices-availability-paired-regions.md).

## <a name="soft-delete"></a>Obnovitelné odstranění

Obnovitelné odstranění je funkce, která pomáhá chránit před náhodným odstraněním zálohovaných dat. Pomocí obnovitelného odstranění, i když škodlivý objekt actor odstraní zálohu (nebo se zálohovaná data nechtěně odstraní), zálohovaná data se uchovávají po delší dobu, což umožňuje obnovení této zálohované položky bez ztráty dat. [Další informace](backup-azure-security-feature-cloud.md).

## <a name="snapshot"></a>Snímek

Snímek je úplná kopie virtuálního pevného disku (VHD), která je jen pro čtení, nebo sdílená složka Azure. Další informace o [snímcích snímků disku](../virtual-machines/windows/snapshot-copy-managed-disk.md) a [snímcích souborů](../storage/files/storage-snapshots-files.md).

## <a name="storage-account"></a>Účet úložiště

Informace najdete v [dokumentaci k účtu úložiště](../storage/common/storage-account-overview.md).

## <a name="subscription"></a>Předplatné

Předplatné Azure je logický kontejner, který se používá ke zřizování prostředků v Azure. Obsahuje podrobné informace o všech vašich prostředcích, jako jsou virtuální počítače, databáze a další.

## <a name="system-state-backup"></a>Zálohování stavu systému

(Termín specifický pro úlohy)

Zálohuje soubory operačního systému. Tato záloha umožňuje obnovení při spuštění počítače, ale systémové soubory a Registry jsou ztraceny. [Další informace](backup-mabs-system-state-and-bmr.md).

## <a name="tenant"></a>Tenant

Klient představuje organizaci. Jedná se o vyhrazenou instanci Azure AD, kterou organizace nebo vývojář aplikací obdrží při vytvoření partnerství s Microsoftem, například registrací do Azure, Microsoft Intune nebo Microsoft 365.

## <a name="tier"></a>Úroveň

V současné době Azure Backup podporuje následující úrovně úložiště zálohování:

### <a name="snapshot-tier"></a>Vrstva snímku

(Termín specifický pro úlohy) V první fázi zálohování virtuálního počítače se pořízené snímky ukládají společně s diskem. Tato forma úložiště se označuje jako vrstva snímku. Obnovení vrstvy snímků je rychlejší (než obnovení z trezoru), protože eliminují dobu čekání na zkopírování snímků z trezoru před aktivací operace obnovení.

### <a name="vault-standard-tier"></a>Vault-Standard úroveň

Data záloh pro všechny úlohy, které Azure Backup podporuje, se ukládají do trezorů, které uchovávají úložiště pro zálohování, automatické škálování sady účtů úložiště, které spravuje Azure Backup. Vault-Standard vrstva je online vrstva úložiště, která umožňuje uložit izolovanou kopii zálohovaných dat do spravovaného tenanta společnosti Microsoft a vytvořit tak další úroveň ochrany. Pro úlohy, kde je podporována vrstva snímků, je kopie zálohovaných dat jak v úrovni snímku, tak i v trezoru úrovně Standard. Trezor – úroveň Standard zajišťuje dostupnost zálohovaných dat i v případě, že je odstraněn nebo ohrožen zálohovaný zdroj dat.

## <a name="unmanaged-disk"></a>Nespravovaný disk

Informace najdete v [dokumentaci k nespravovaným diskům](../storage/common/storage-disaster-recovery-guidance.md#azure-unmanaged-disks).

## <a name="vault"></a>Trezor

Entita úložiště v Azure, která slouží k zálohování dat. Je to také jednotka RBAC a fakturace. V současné době existují dva typy trezorů – trezor Recovery Services a úložiště záloh.

## <a name="vault-credentials"></a>Přihlašovací údaje trezoru

Soubor s přihlašovacími údaji trezoru je certifikát vygenerovaný portálem pro každý trezor. Tento postup se používá při registraci místního serveru do trezoru. [Další informace](backup-azure-dpm-introduction.md).

## <a name="vnet-virtual-network"></a>Virtuální síť (Virtual Network)

Přečtěte si [dokumentaci k virtuální](../virtual-network/virtual-networks-overview.md)síti.

## <a name="vss-windows-volume-shadow-copy-service"></a>VSS (Windows služba Stínová kopie svazku)

Informace najdete v [dokumentaci](/windows-server/storage/file-server/volume-shadow-copy-service)ke službě VSS.

## <a name="next-steps"></a>Další kroky

- [Přehled Azure Backup](backup-overview.md)
- [Architektura Azure Backup a součásti](backup-architecture.md)