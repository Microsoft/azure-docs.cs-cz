---
title: Plánování nasazení služby soubory Azure | Microsoft Docs
description: Vysvětlení plánování nasazení služby soubory Azure Můžete buď přímo připojit sdílenou složku Azure, nebo Uložit do mezipaměti sdílenou složku Azure místně pomocí Synchronizace souborů Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 267b68fbdae6d894acc3222a8d74a8e15e865dbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023516"
---
# <a name="planning-for-an-azure-files-deployment"></a>Plánování nasazení služby Soubory Azure
[Soubory Azure](storage-files-introduction.md) se dají nasadit dvěma hlavními způsoby: přímým připojením sdílených složek Azure bez serveru nebo ukládáním do mezipaměti sdílených složek Azure v místním prostředí pomocí synchronizace souborů Azure. Kterou možnost nasazení zvolíte, změní se to, co je potřeba vzít v úvahu při plánování nasazení. 

- **Přímé připojení sdílené složky Azure**: vzhledem k tomu, že soubory Azure poskytují přístup přes protokol SMB (Server Message Block) nebo systém souborů NFS (Network File System), můžete sdílené složky Azure připojit místně nebo v cloudu pomocí standardních klientů SMB nebo NFS, které jsou k dispozici v operačním systému. Vzhledem k tomu, že sdílené složky Azure jsou bez serveru, nasazení pro produkční scénáře nevyžaduje správu souborového serveru nebo zařízení NAS. To znamená, že nemusíte instalovat softwarové opravy ani odpínat fyzické disky. 

- **Ukládání sdílené složky Azure do mezipaměti v místním prostředí pomocí synchronizace souborů Azure**: synchronizace souborů Azure umožňuje centralizovat sdílené složky ve vaší organizaci ve službě soubory Azure a zachovat tak flexibilitu, výkon a kompatibilitu místního souborového serveru. Synchronizace souborů Azure transformuje místní (nebo cloudový) Windows Server na rychlou mezipaměť sdílené složky Azure SMB. 

Tento článek primárně řeší požadavky nasazení na nasazení sdílené složky Azure, aby je bylo možné přímo připojit k místnímu nebo cloudovém klientovi. Chcete-li naplánovat nasazení Synchronizace souborů Azure, přečtěte si téma [Plánování nasazení synchronizace souborů Azure](storage-sync-files-planning.md).

## <a name="available-protocols"></a>Dostupné protokoly

Soubory Azure nabízí dva protokoly, které se dají použít při připojování sdílených složek, SMB a systému souborů NFS (Network File System). Podrobnosti o těchto protokolech najdete v tématu [protokoly sdílení souborů Azure](storage-files-compare-protocols.md).

> [!IMPORTANT]
> Většina obsahu tohoto článku platí pouze pro sdílené složky SMB. Cokoli, co platí pro sdílené složky systému souborů NFS, bude konkrétně platit pro tento stav.

## <a name="management-concepts"></a>Koncepty správy
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Při nasazování sdílených složek Azure do účtů úložiště doporučujeme:

- Do účtů úložiště s jinými sdílenými složkami Azure nasadíte jenom sdílené složky Azure. I když účty úložiště GPv2 umožňují používat účty úložiště pro smíšené účely, protože prostředky úložiště, jako jsou sdílené složky Azure a kontejnery objektů blob, sdílejí omezení účtu úložiště, může být obtížnější řešit problémy s výkonem později. 

- Při nasazování sdílených složek Azure věnujte pozornost omezením IOPS účtu úložiště. V ideálním případě byste namapovali sdílené složky 1:1 s účty úložiště. to ale nemusí být vždycky možné kvůli různým omezením a omezením, a to jak z vaší organizace, tak z Azure. Pokud není možné mít v jednom účtu úložiště nasazenou jenom jednu sdílenou složku, zvažte, které sdílené složky budou vysoce aktivní a které akcie budou méně aktivní, aby se zajistilo, že sdílené složky nejžhavějších se nebudou ukládat do stejného účtu úložiště společně.

- Nasaďte GPv2 a účty úložiště a upgradujte GPv1 a klasické účty úložiště, když je najdete ve svém prostředí. 

## <a name="identity"></a>Identita
Aby bylo možné získat přístup ke sdílené složce Azure, musí být uživatel sdílené složky ověřený a mít autorizaci pro přístup ke sdílené složce. To se provádí na základě identity uživatele, který přistupuje ke sdílené složce souborů. Soubory Azure se integrují se třemi hlavními zprostředkovateli identity:
- Místní **Active Directory Domain Services (služba AD DS nebo místní služba AD DS)**: účty Azure Storage můžou být připojené k doméně, která patří k zákazníkovi Active Directory Domain Services, stejně jako souborový server Windows nebo zařízení NAS. Můžete nasadit řadič domény místně, na virtuálním počítači Azure nebo dokonce jako virtuální počítač v jiném poskytovateli cloudu. Soubory Azure se nezávislá na místo, kde je hostovaný váš řadič domény. Jakmile je účet úložiště připojený k doméně, koncový uživatel může připojit sdílenou složku s uživatelským účtem, pomocí kterého se přihlásili ke svému počítači. Ověřování založené na AD používá ověřovací protokol Kerberos.
- **Azure Active Directory Domain Services (azure služba AD DS)**: Azure služba AD DS poskytuje řadič domény spravovaný Microsoftem, který se dá použít pro prostředky Azure. Doména, která se připojuje k vašemu účtu úložiště k Azure služba AD DS poskytuje podobné výhody pro doménu, která se připojuje ke službě Active Directory vlastněné zákazníkem. Tato možnost nasazení je nejužitečnější pro scénáře použití a posunutí aplikace, které vyžadují oprávnění založené na službě AD. Vzhledem k tomu, že Azure služba AD DS poskytuje ověřování založené na AD, používá tato možnost také ověřovací protokol Kerberos.
- **Klíč účtu úložiště Azure**: sdílené složky Azure můžou být taky připojené pomocí klíče účtu úložiště Azure. Chcete-li připojit sdílenou složku tímto způsobem, je název účtu úložiště použit jako uživatelské jméno a klíč účtu úložiště se používá jako heslo. Použití klíče účtu úložiště pro připojení sdílené složky Azure je efektivně operace správce, protože připojená sdílená složka bude mít úplná oprávnění ke všem souborům a složkám ve sdílené složce, a to i v případě, že mají seznamy řízení přístupu (ACL). Při použití klíče účtu úložiště k připojení přes protokol SMB se používá ověřovací protokol NTLMv2.

Pro zákazníky, kteří migrují z místních souborových serverů nebo vytváří nové sdílené složky ve službě soubory Azure, které se chovají jako souborové servery Windows nebo zařízení NAS, je doporučenou možností připojit se k vašemu účtu úložiště ke **službě Active Directory vlastněné zákazníkem** . Další informace o tom, jak se doména připojuje k vašemu účtu úložiště ke službě Active Directory vlastněné zákazníkem, najdete v tématu [Přehled služby Azure Files Active Directory](storage-files-active-directory-overview.md).

Pokud máte v úmyslu používat pro přístup ke sdíleným složkám Azure klíč účtu úložiště, doporučujeme používat koncové body služby, jak je popsáno v části [síť](#networking) .

## <a name="networking"></a>Sítě
Sdílené složky Azure jsou přístupné odkudkoli prostřednictvím veřejného koncového bodu účtu úložiště. To znamená, že ověřené požadavky, jako jsou požadavky autorizované identitou přihlašování uživatele, můžou pocházet z interního nebo mimo Azure. V mnoha zákaznických prostředích se počáteční připojení sdílené složky Azure na místní pracovní stanici nezdaří, i když připojení z virtuálních počítačů Azure bude úspěšné. Důvodem je to, že mnoho organizací a poskytovatelů internetových služeb (ISP) blokuje port, který protokol SMB používá ke komunikaci, port 445. Souhrn poskytovatelů internetových služeb, kteří umožňují nebo neumožňují přístup z portu 445, najdete na webu [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Pokud chcete odblokovat přístup ke sdílené složce Azure, máte dvě hlavní možnosti:

- Odblokujte port 445 pro místní síť vaší organizace. Sdílené složky Azure je možné externě přistupovat prostřednictvím veřejného koncového bodu pomocí internetových bezpečných protokolů, jako je SMB 3,0 a rozhraní REST API. Toto je nejjednodušší způsob, jak získat přístup ke sdílené složce Azure z místního prostředí, protože nevyžaduje rozšířenou konfiguraci sítě nad rámec změny odchozích pravidel portů vaší organizace. Doporučujeme ale odebrat starší verze a zastaralé verze protokolu SMB, konkrétně SMB 1,0. Další informace o tom, jak to udělat, najdete v tématu [zabezpečení Windows/Windows serveru](storage-how-to-use-files-windows.md#securing-windowswindows-server) a [zabezpečení systému Linux](storage-how-to-use-files-linux.md#securing-linux).

- Přístup ke sdíleným složkám Azure přes ExpressRoute nebo připojení k síti VPN. Když ke sdílené složce Azure přistupujete prostřednictvím síťového tunelového propojení, budete moci připojit sdílenou složku Azure jako místní sdílenou složku, protože přenosy SMB nepřecházejí do vaší organizační hranice.   

I když z technického hlediska je podstatně jednodušší připojit sdílené složky Azure přes Veřejný koncový bod, očekáváme, že většina zákazníků bude chtít připojit své sdílené složky Azure přes ExpressRoute nebo připojení k síti VPN. Připojení k těmto možnostem je možné u sdílených složek SMB i NFS. K tomu budete muset pro svoje prostředí nakonfigurovat následující:  

- **Síťové tunelové propojení pomocí ExpressRoute, Site-to-site nebo VPN typu Point-to-site**: tunelování do virtuální sítě umožňuje přístup ke sdíleným složkám Azure z místního prostředí, i když je port 445 zablokovaný.
- **Privátní koncové body**: privátní koncové body přidávají účtu úložiště vyhrazenou IP adresu z adresního prostoru virtuální sítě. To umožňuje síťové tunelové propojení bez nutnosti otevírat místní sítě až do všech rozsahů IP adres, které vlastní clustery Azure Storage. 
- **Předávání DNS**: Nakonfigurujte místní službu DNS tak, aby přeložila název vašeho účtu úložiště ( `storageaccount.file.core.windows.net` pro oblasti veřejného cloudu), aby se přeložila na IP adresu vašich privátních koncových bodů.

Informace o plánování sítě přidružených k nasazení sdílené složky Azure najdete v tématu věnovaném [důležitým informacím o sítích Azure Files](storage-files-networking-overview.md).

## <a name="encryption"></a>Šifrování
Soubory Azure podporují dva různé typy šifrování: šifrování při přenosu, které souvisí s šifrováním používaným při připojování/přístupu ke sdílené složce Azure, a šifrování v klidovém stavu, které souvisí s tím, jak se data při ukládání na disk šifrují. 

### <a name="encryption-in-transit"></a>Šifrování během přenosu

> [!IMPORTANT]
> Tato část popisuje šifrování v podrobnostech o přenosu pro sdílené složky SMB. Podrobnosti o šifrování při přenosu s sdílenými složkami NFS najdete v tématu [zabezpečení](storage-files-compare-protocols.md#security).

Ve výchozím nastavení mají všechny účty úložiště Azure povolený šifrování při přenosu. To znamená, že když připojíte sdílenou složku přes protokol SMB nebo k ní přistupujete prostřednictvím protokolu REST (například prostřednictvím Azure Portal, PowerShellu nebo rozhraní Azure SDK), budou soubory Azure umožňovat připojení jenom v případě, že se jedná o protokol SMB 3.0 + s šifrováním nebo HTTPS. Klienti, kteří nepodporují protokol SMB 3,0 nebo klienti, kteří podporují protokol SMB 3,0, ale nemají šifrování protokolu SMB, nebudou moci připojit sdílenou složku Azure, pokud je zapnuté šifrování při přenosu. Další informace o tom, které operační systémy podporují protokol SMB 3,0 se šifrováním, najdete v naší podrobné dokumentaci pro [Windows](storage-how-to-use-files-windows.md), [MacOS](storage-how-to-use-files-mac.md)a [Linux](storage-how-to-use-files-linux.md). Všechny aktuální verze PowerShellu, CLI a sad SDK podporují protokol HTTPS.  

Šifrování můžete zakázat při přenosu pro účet služby Azure Storage. Když je šifrování zakázané, budou soubory Azure taky umožňovat SMB 2,1, SMB 3,0 bez šifrování a nešifrované volání rozhraní REST API přes HTTP. Hlavním důvodem Zakázání šifrování při přenosu je podpora starší verze aplikace, kterou je třeba spustit ve starším operačním systému, jako je Windows Server 2008 R2 nebo starší distribuce systému Linux. Soubory Azure v rámci stejné oblasti Azure jako sdílená složka Azure povolují jenom připojení SMB 2,1. Klient SMB 2,1 mimo oblast Azure sdílené složky Azure, například v místním prostředí nebo v jiné oblasti Azure, nebude mít přístup ke sdílené složce souborů.

Důrazně doporučujeme, abyste zajistili, že je povolené šifrování dat při přenosu.

Další informace o šifrování v přenosu najdete v tématu [vyžadování zabezpečeného přenosu ve službě Azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="encryption-at-rest"></a>Šifrování v klidovém stavu
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="data-protection"></a>Ochrana dat
Soubory Azure mají Vícevrstvý přístup k zajištění zálohovaných dat, jejich obnovení a ochrany před bezpečnostními hrozbami.

### <a name="soft-delete"></a>Obnovitelné odstranění
Obnovitelné odstranění pro sdílené složky (Preview) je nastavení na úrovni účtu úložiště, které umožňuje obnovit sdílenou složku, když ji omylem odstraníte. Když se sdílená složka odstraní, přejde se do stavu bez trvalého odstranění, takže se neodstraní. Můžete nakonfigurovat dobu, po kterou se Odstraněná data odstraněná mají obnovit, než se trvale odstraní, a během této doby uchování zrušit její sdílení kdykoli. 

Pro většinu sdílených složek doporučujeme zapnout obnovitelné odstranění. Pokud máte pracovní postup, ve kterém je odstraňování sdílené složky běžné a očekávané, můžete se rozhodnout, že budete mít krátké období uchovávání nebo nemáte povolené obnovitelné odstranění.

Další informace o obnovitelném odstranění najdete v tématu [prevence náhodného odstranění dat](./storage-files-prevent-file-share-deletion.md).

### <a name="backup"></a>Backup
Sdílenou složku Azure můžete zálohovat pomocí [snímků sdílené složky](./storage-snapshots-files.md), které jsou určené jen pro čtení, jako kopie vaší sdílené složky v časovém okamžiku. Snímky jsou přírůstkové, což znamená, že obsahují pouze tolik dat, kolik bylo od předchozího snímku změněno. Můžete mít až 200 snímků na sdílenou složku a uchovávat je až po dobu 10 let. Tyto snímky můžete buď ručně vzít v Azure Portal, prostřednictvím PowerShellu nebo rozhraní příkazového řádku (CLI), nebo můžete použít [Azure Backup](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Snímky se ukládají v rámci sdílené složky, což znamená, že pokud odstraníte sdílenou složku, vaše snímky se taky odstraní. Pokud chcete ochránit zálohy snímků před náhodným odstraněním, ujistěte se, že je pro vaši sdílenou složku povolené obnovitelné odstranění.

[Azure Backup pro sdílené složky Azure](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) zpracovává plánování a uchovávání snímků. Funkce dědečka-otce-syn (GFS) znamenají, že můžete provádět denní, týdenní, měsíční a roční snímky, z nichž každá má svou vlastní jedinečnou dobu uchování. Azure Backup také orchestruje povolení obnovitelného odstranění a provede zámek proti odstranění na účtu úložiště, jakmile bude pro zálohování nakonfigurované všechny sdílené soubory. A konečně Azure Backup poskytuje určité klíčové možnosti monitorování a upozorňování, které zákazníkům umožňují získat konsolidované zobrazení jejich záložního vlastnictví.

V Azure Portal můžete pomocí Azure Backup provádět obnovení na úrovni položek i sdílené složky. Stačí vybrat bod obnovení (konkrétní snímek), konkrétní soubor nebo adresář, pokud je to relevantní, a pak umístění (originál nebo alternativní), na které chcete provést obnovení. Služba Backup zpracovává kopírování dat snímku a zobrazuje průběh obnovení na portálu.

Další informace o zálohování najdete v tématu [informace o zálohování sdílených složek Azure](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="azure-defender-for-azure-files"></a>Azure Defender pro soubory Azure 
Azure Defender pro Azure Storage (dříve Rozšířená ochrana před internetovými útoky pro Azure Storage) poskytuje další úroveň inteligentního zabezpečení, která poskytuje výstrahy, když detekuje neobvyklé aktivitu na vašem účtu úložiště, například nezvyklé pokusy o přístup. Také spustí analýzu reputace hodnoty hash malwaru a upozorní na známý malware. Azure Defender můžete nakonfigurovat na úrovni předplatného nebo účtu úložiště prostřednictvím Azure Security Center. 

Další informace najdete v tématu [Úvod do služby Azure Defender pro úložiště](../../security-center/defender-for-storage-introduction.md).

## <a name="storage-tiers"></a>Úrovně úložiště
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Povolit standardní sdílené složky pro rozsah až 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="limitations"></a>Omezení
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>Redundance
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>Migrace
V mnoha případech nebudete pro vaši organizaci vytvářet novou sdílenou složku a místo toho migrujete existující sdílenou složku z místního souborového serveru nebo ze zařízení NAS do služby soubory Azure. Vybírání správné strategie a nástroje migrace pro váš scénář je důležité pro úspěch migrace. 

[Článek Přehled migrace](storage-files-migration-overview.md) stručně popisuje základy a obsahuje tabulku, která vás zavede k migračním průvodcům, které se pravděpodobně týkají vašeho scénáře.

## <a name="next-steps"></a>Další kroky
* [Plánování nasazení Synchronizace souborů Azure](storage-sync-files-planning.md)
* [Nasazení souborů Azure](./storage-how-to-create-file-share.md)
* [Nasazení Synchronizace souborů Azure](storage-sync-files-deployment-guide.md)
* [Podívejte se na článek Přehled migrace, kde najdete Průvodce migrací pro váš scénář.](storage-files-migration-overview.md)