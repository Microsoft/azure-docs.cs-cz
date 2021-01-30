---
title: Oracle Database v Azure Virtual Machines strategie zálohování
description: Možnosti zálohování databází Oracle v prostředí virtuálního počítače Azure Linux.
author: cro27
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: adb455e7a49ead1e8ba8e9845a4e56542528d0b1
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063855"
---
# <a name="oracle-database-in-azure-linux-vm-backup-strategies"></a>Oracle Database ve strategiích zálohování virtuálních počítačů Azure Linux

Zálohování databáze chrání databázi před ztrátou dat kvůli selhání komponenty úložiště a selhání datového centra. Můžou to být také prostředky obnovení z lidské chyby a způsob klonování databáze pro účely vývoje nebo testování. 

V Azure platí, že pokud je úložiště vysoce redundantní a ztráta jednoho nebo více disků nevede k výpadku databáze, zálohy se nejčastěji používají k ochraně proti lidské chybě, k klonování operací nebo ochraně dat pro účely zákonování. Také se jedná o způsob ochrany před oblastním výpadkem, kdy se technologie zotavení po havárii, jako je třeba DataGuard, nepoužívá. V takovém případě musí být zálohy uložené v různých oblastech Azure pomocí geograficky redundantní replikace, takže budou dostupné mimo oblast primární databáze.

## <a name="azure-storage"></a>Azure Storage 

[Služba Azure Storage Services](../../../storage/common/storage-introduction.md) jsou řešení cloudového úložiště Microsoftu pro scénáře moderních úložišť dat. Azure Storage nabízí řadu služeb, které se dají použít k připojení externího úložiště k virtuálnímu počítači Azure Linux, který je vhodný jako záložní médium pro databáze Oracle. K zahájení operace zálohování nebo obnovení a zkopírování zálohy do/ze Azure Storage služby se vyžaduje nástroj pro zálohování, jako je Oracle RMAN.
 
Služba Azure Storage Services nabízí následující výhody:

-  Odolné a vysoce dostupné: Redundance zajišťuje, že vaše data budou v případě krátkodobého selhání hardwaru v bezpečí. Všechna úložiště jsou ve výchozím nastavení tři zrcadlová. Můžete se také rozhodnout pro replikaci dat mezi datovými centry nebo geografickými oblastmi, aby se mohla zvýšit ochrana z místního pohromě nebo přírodní havárie. Data replikovaná tímto způsobem zůstávají v případě nečekaného výpadku vysoce dostupná.

-  Požadavk. Všechna data zapsaná do účtu služby Azure Storage jsou službou šifrována. Azure Storage poskytuje jemně odstupňované řízení přístupu k datům.

-  Měřítk. Služba Azure Storage je navržená pro širokou škálovatelnost, aby splňovala požadavky dnešních aplikací na datové úložiště a výkon.

-  Starosti. Azure zpracovává údržbu, aktualizace a kritické problémy s hardwarem.

-  Přístupné: Data ve službě Azure Storage jsou přístupná prostřednictvím protokolu HTTP nebo HTTPS odkudkoli na světě. Microsoft poskytuje klientské knihovny pro Azure Storage v různých jazycích, včetně .NET, Java, Node.js, Pythonu, PHP, Ruby, přechodu a dalších, a také na vyspělé REST API. Azure Storage podporuje skriptování v Azure PowerShellu nebo Azure CLI. A web Azure Portal a Průzkumník služby Azure Storage nabízí snadná vizuální řešení pro práci s daty.

Platforma Azure Storage zahrnuje následující datové služby, které jsou vhodné pro použití jako záložní média pro databázi Oracle:

-  Objekty blob Azure: široce škálovatelné úložiště objektů pro textová a binární data Zahrnuje také podporu pro analýzy velkých objemů dat prostřednictvím Data Lake Storage Gen2.

-  Soubory Azure: spravované sdílené složky pro cloudová nebo místní nasazení.

-  Disky Azure: svazky úložiště na úrovni bloku pro virtuální počítače Azure.

### <a name="cross-regional-storage-mounting"></a>Připojení k více oblastem úložiště

Možnost přístupu k úložišti zálohování mezi oblastmi je důležitým aspektem BCDR a užitečnou pro klonování databází ze záloh do různých geografických oblastí. Cloudové úložiště Azure poskytuje pět úrovní [redundance](../../../storage/common/storage-redundancy.md) .
- Místně redundantní úložiště [(LRS)](../../../storage/common/storage-redundancy.md#locally-redundant-storage) , kde jsou vaše data replikována třikrát v jednom fyzickém umístění v primární oblasti.  
- Redundantní úložiště zóny [(ZRS)](../../../storage/common/storage-redundancy.md#zone-redundant-storage) , ve kterém jsou vaše data chráněná LRS v primární oblasti a replikována synchronně v rámci tří zón dostupnosti (AZ) v primární oblasti. Každý AZ je LRS chráněný. 
- Geo-Redundant Storage [(GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) , kde jsou vaše data chráněná LRS v primární oblasti a replikována asynchronně do sekundární oblasti, kde je taky chráněná LRS.
- Geograficky redundantní úložiště [(GZRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) kopíruje data synchronně v rámci tří zón dostupnosti Azure v primární oblasti pomocí ZRS. Pak data kopíruje asynchronně do jednoho fyzického umístění v sekundární oblasti. Ve všech umístěních jsou data chráněná pomocí LRS. 
- Přístup pro čtení Geo-Redundant Storage [(RA-GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) a geografické úložiště s přístupem pro čtení [(RA-GZRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) umožňuje kdykoli přístup k datům replikovaným do sekundární oblasti jenom pro čtení. 

#### <a name="blob-storage-and-file-storage"></a>Úložiště objektů BLOB a úložiště souborů

Při použití souborů Azure s protokoly SMB nebo NFS 4,1 (Preview) pro připojení jako úložiště záloh si všimněte, že soubory Azure nepodporují geograficky redundantní úložiště s přístupem pro čtení (RA-GRS) a geograficky redundantní úložiště s přístupem pro čtení (RA-GZRS). 

Kromě toho, pokud je požadavek na úložiště zálohování větší než 5 TiB soubory Azure vyžaduje povolenou [funkci velké sdílené složky](../../../storage/files/storage-files-planning.md) , která nepodporuje GRS nebo GZRS redundanci, je podporováno pouze LRS. 

Služba Azure Blob připojená pomocí systému souborů NFS 3,0 (Preview) v současné době podporuje jenom redundanci LRS a ZRS.  

Objekt blob Azure nakonfigurovaný s možností redundance se dá připojit pomocí Blobfuse.

#### <a name="recovery-services-vault"></a>Trezor služby Recovery Services 

Trezor služby Recovery Services je správní entitou, která ukládá body obnovení vytvořené v průběhu času a nabízí rozhraní, pomocí kterého je možné provádět operace související se zálohováním. Mezi ně patří zálohování na vyžádání, provádění obnovení a vytváření zásad zálohování.
Azure Backup automaticky zpracovává úložiště pro trezor. Musíte určit, jak se má úložiště replikovat při vytváření. Po ochraně položek v trezoru ho nelze změnit. V případě regionální redundance vyberte nastavení geograficky redundantní.

Pokud máte v úmyslu obnovení do sekundární služby, můžete použít funkci [obnovení mezi oblastmi](../../../backup/backup-create-rs-vault.md) v rámci [Azure spárované oblasti](../../../best-practices-availability-paired-regions.md) . Pokud je povoleno obnovení mezi oblastmi, je úložiště zálohování přesunuto z GRS do geograficky redundantního úložiště s přístupem pro čtení (RA-GRS). 

### <a name="azure-blob-storage"></a>Azure Blob Storage

Azure Blob Storage je vysoce nákladná, trvalá a zabezpečená cloudová služba úložiště, která se používá k ukládání velkých objemů nestrukturovaných dat a je ideálním médiem, které slouží k Oracle Database zálohování. Úložiště objektů BLOB v Azure se dá připojit k virtuálnímu počítači Azure Linux pomocí protokolu NFS v 3.0 nebo Blobfuse (pojistka Linux).

#### <a name="azure-blob-blobfuse"></a>Blobfuse objektů BLOB v Azure

[Blobfuse](../../../storage/blobs/storage-how-to-mount-container-linux.md) je open source projekt vyvinutý tak, aby poskytoval virtuální systém zálohovaný úložištěm objektů BLOB v Azure. Používá open source knihovnu libfuse ke komunikaci s modulem jádra pro Linux a implementuje operace systému souborů pomocí rozhraní REST API Azure Storage Blob. Blobfuse je aktuálně k dispozici pro distribuce Ubuntu a CentOS/RedHat. K dispozici je také pro Kubernetes pomocí [ovladače CSI](https://github.com/kubernetes-sigs/blob-csi-driver). 

I když všudypřítomný napříč všemi oblastmi Azure a funguje se všemi typy účtů úložiště, včetně obecného účelu v1/v2 a Azure Data Lake Storech Gen2, výkon nabízený Blobfuse se ukázal jako na méně než alternativních protokolech, jako je SMB nebo NFS. Pro vhodnost jako záložní média databáze doporučujeme použít protokoly SMB nebo [NFS](../../../storage/blobs/storage-how-to-mount-container-linux.md) k připojení úložiště objektů BLOB v Azure. 

#### <a name="azure-blob-nfs-v30-preview"></a>Azure Blob NFS v 3.0 (Preview)

Podpora Azure pro systém souborů NFS (Network File System) v 3.0 je teď ve verzi Preview. Podpora [systému souborů NFS](../../../storage/blobs/network-file-system-protocol-support.md) umožňuje klientům se systémem Windows a Linux připojit kontejner úložiště objektů BLOB k virtuálnímu počítači Azure. 

Systém souborů NFS 3,0 Public Preview podporuje účty úložiště GPV2 s výkonem úrovně Standard v následujících oblastech: 
- Austrálie – východ
- Jižní Korea – střed
- Střed USA – jih. 

Aby se zajistilo zabezpečení sítě, musí být účet úložiště, který se používá pro připojení NFS, obsažený v rámci virtuální sítě. Zabezpečení Azure Active Directory (AD) a seznamy řízení přístupu (ACL) ještě nejsou podporované v účtech s povolenou podporou protokolu NFS 3,0.

### <a name="azure-files"></a>Azure Files

[Soubory Azure](../../../storage/files/storage-files-introduction.md) jsou cloudové a plně spravované distribuované systémy souborů, které je možné připojit k místním nebo cloudovým klientům Windows, Linux nebo MacOS.

Soubory Azure nabízí plně spravované sdílené složky pro různé platformy v cloudu, které jsou přístupné prostřednictvím protokolu SMB (Server Message Block) a protokolu NFS (Network File System) (verze Preview). Služba soubory Azure v současné době nepodporuje přístup s více protokoly, takže sdílená složka může být pouze sdílená složka systému souborů NFS nebo sdílená složka protokolu SMB. Před vytvořením sdílených složek Azure doporučujeme určit, který protokol nejlépe vyhovuje vašim potřebám.

Sdílené složky Azure je také možné chránit prostřednictvím Azure Backup do trezoru služby Recovery Services a poskytuje další úroveň ochrany pro zálohování Oracle RMAN.

#### <a name="azure-files-nfs-v41-preview"></a>Soubory Azure NFS v 4.1 (Preview)

Sdílené složky Azure je možné připojit v distribucích systému Linux pomocí protokolu NFS (Network File System) v 4.1. Ve verzi Preview existuje několik omezení podporovaných funkcí, které jsou popsány [zde](../../../storage/files/storage-files-how-to-mount-nfs-shares.md). 

I když ve verzi Preview jsou soubory NFS v systému souborů NFS i v 4.1 omezené i na tyto [oblasti](../../../storage/files/storage-files-how-to-mount-nfs-shares.md):
- Východní USA (LRS a ZRS)
- USA – východ 2
- Západní USA 2
- West Europe
- Southeast Asia
- Spojené království – jih
- Austrálie – východ

#### <a name="azure-files-smb-30"></a>Soubory Azure SMB 3,0

Sdílené složky Azure je možné připojit v rámci distribucí systému Linux pomocí klienta jádra protokolu SMB. Protokol CIFS (Common Internet File System), který je k dispozici v distribucích systému Linux, je dialektem protokolu SMB. Při připojování Azure Files SMB k virtuálním počítačům se systémem Linux je tato funkce připojena jako typ systému souborů CIFS a balíček CIFS musí být nainstalován. 

Služba Azure Files SMB je všeobecně dostupná ve všech oblastech Azure a zobrazuje stejné charakteristiky výkonu jako protokoly NFS v 3.0 a v, a proto je v současné době doporučená metoda pro poskytování záložních úložných médií do virtuálních počítačů Azure Linux.  

K dispozici jsou dvě podporované verze SMB, SMB 2,1 a SMB 3,0 a druhá se doporučuje, protože podporuje šifrování při přenosu. Různé verze jader pro Linux ale mají různou podporu pro SMB 2,1 a 3,0 a měli byste [tady](../../../storage/files/storage-how-to-use-files-linux.md) zkontrolovat tabulku, abyste zajistili, že aplikace podporuje protokol SMB 3,0. 

Vzhledem k tomu, že služba soubory Azure je navržená tak, aby byla službou Sdílení souborů více uživateli, měli byste vyladit určité charakteristiky, aby lépe vyhovovaly jako záložní paměťové médium. Doporučuje se vypnutí ukládání do mezipaměti a nastavení ID uživatelů a skupin pro vytvořené soubory.

## <a name="azure-netapp-files"></a>Azure NetApp Files

Služba [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-solution-architectures.md) je kompletní řešení úložiště pro databáze Oracle ve virtuálních počítačích Azure. Vysoce výkonné a měřené úložiště souborů na podnikové úrovni, které podporuje libovolný typ úlohy a ve výchozím nastavení je vysoce dostupné. Společně s ovladačem systému Oracle Direct NFS (dNFS) poskytuje Azure NetApp Files pro Oracle Database vysoce optimalizovanou vrstvu úložiště.

Azure NetApp Files poskytuje efektivní kopie snímků na bázi úložiště v podkladovém systému úložiště, který používá přesměrování na mechanismu zápisu (řádek). I když jsou kopie snímků extrémně rychlé, aby je bylo možné převzít a obnovovat, slouží pouze jako první linie obrany, což může představovat rozsáhlou většinu požadovaných operací obnovení jakékoli dané organizace, což je často obnovení z lidské chyby. Kopie snímků však nejsou kompletním zálohováním. Aby se pokryly všechny požadavky na zálohování a obnovení, je potřeba, aby se externí repliky snímků a/nebo jiné záložní kopie vytvořily ve vzdálené geografické oblasti pro ochranu před oblastním výpadkem. Pokud chcete získat další informace o používání souborů NetApp pro databáze Oracle v Azure, přečtěte si tuto [sestavu](https://www.netapp.com/pdf.html?item=/media/17105-tr4780pdf.pdf).

## <a name="azure-backup-service"></a>Služba Azure Backup

[Azure Backup](../../../backup/backup-overview.md) je plně spravovaná PaaS, která poskytuje jednoduchá, bezpečná a nákladově efektivní řešení pro zálohování vašich dat a obnovení z Microsoft Azure cloudu. Azure Backup můžou zálohovat a obnovovat místní klienty, sdílené složky Azure ve sdílených složkách a také databáze SQL Server, Oracle, MySQL, PostreSQL a SAP HANA na virtuálních počítačích Azure. 

Azure Backup poskytuje nezávislé a izolované zálohy pro ochranu proti náhodnému zničení původních dat. Zálohy jsou uloženy v [Recovery Services trezoru](../../../backup/backup-azure-recovery-services-vault-overview.md) s integrovanou správou bodů obnovení. Konfigurace a škálovatelnost jsou jednoduché, zálohy jsou optimalizované a můžete je snadno obnovit podle potřeby. Využívá základní napájení a neomezené škálování cloudu Azure k zajištění vysoké dostupnosti bez režie údržby a monitorování. Azure Backup neomezuje množství příchozích a odchozích dat, která přenášíte, nebo poplatky za přenášená data a data jsou zabezpečená při přenosu a v klidovém provozu. 

Pro zajištění odolnosti Azure Backup nabízí více typů replikace, aby byla data záloh vysoce dostupná.

- Místně redundantní úložiště [(LRS)](../../../storage/common/storage-redundancy.md#locally-redundant-storage) replikuje vaše data třikrát (vytváří tři kopie dat) v jednotce škálování úložiště v datovém centru.
- Geograficky redundantní úložiště [(GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) je výchozí a doporučená možnost replikace. GRS replikuje vaše data do sekundární oblasti (vzdálené stovky kilometrů od primárního umístění zdrojových dat).

Trezor vytvořený s redundancí GRS zahrnuje možnost konfigurace funkce [obnovení mezi oblastmi](../../../backup/backup-create-rs-vault.md#set-storage-redundancy) , která umožňuje obnovení dat v sekundární oblasti, která je spárována se službou Azure.

Služba Azure Backup poskytuje [rozhraní](../../../backup/backup-azure-linux-app-consistent.md) pro zajištění konzistence aplikací během zálohování virtuálních počítačů s Windows a Linux pro různé aplikace, jako je Oracle, MySQL, Mongo DB, SAP Hana a PostGreSQL, nazývané snímky konzistentní vzhledem k aplikacím. To zahrnuje vyvolání předzálohovacího skriptu (k nečinnosti aplikací) před pořizováním snímku disků a voláním následného skriptu (příkazy pro uvolnění aplikací) po dokončení snímku, aby se aplikace vracely do normálního režimu. I když je na GitHubu k dispozici ukázka předzálohovacích a potištěných skriptů, je vytváření a údržba těchto skriptů vaše zodpovědnost. V případě Oracle musí být databáze v režimu archivelog, aby bylo možné zálohovat online, a příslušné příkazy pro začátek a ukončení databáze jsou spouštěny v předzálohovacích a následných skriptech, které je třeba vytvořit a udržovat. 

Azure Backup teď nabízí [vylepšené rozhraní předzálohovacího a pozálohovacího skriptu](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts), které je teď ve verzi Preview, kde služba Azure Backup poskytuje zabalené předběžné skripty a následné skripty pro vybrané aplikace. Azure Backup uživatelé pouze potřebují aplikaci pojmenovat a pak zálohování virtuálních počítačů Azure automaticky vyvolá příslušné předběžné skripty a následné skripty. Zabalené předzálohovací skripty a post-Script budou spravovány Azure Backup týmem, aby uživatelé mohli zajistit podporu, vlastnictví a platnost těchto skriptů. V současné době jsou podporované aplikace pro vylepšené rozhraní Oracle a MySQL a v budoucnu jsou očekávané další typy aplikací. Snímek bude úplnou kopií úložiště, a ne přírůstkovým nebo kopírovacím snímkem zápisu, takže se jedná o efektivní médium pro obnovení databáze z.

## <a name="next-steps"></a>Další kroky

- [Rychlé vytvoření Oracle Database](oracle-database-quick-create.md)
- [Zálohování Oracle Database do souborů Azure](oracle-database-backup-azure-storage.md)
- [Zálohování Oracle Database pomocí služby Azure Backup](oracle-database-backup-azure-backup.md)


