---
title: Nejčastější dotazy týkající se souborů Azure | Microsoft Docs
description: Přečtěte si odpovědi na nejčastější dotazy týkající se služby Azure Files.
author: roygara
ms.service: storage
ms.date: 07/30/2019
ms.author: rogarana
ms.subservice: files
ms.topic: conceptual
ms.openlocfilehash: 6fecd143055da2829ac49cee4f50d448a37a6e1b
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514887"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Nejčastější dotazy týkající se souborů Azure
[Soubory Azure](storage-files-introduction.md) nabízí plně spravované sdílené složky v cloudu, které jsou přístupné přes standardní [protokol SMB (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx). Sdílené složky Azure můžete připojit souběžně na cloudové nebo místní nasazení systémů Windows, Linux a macOS. Sdílené složky Azure můžete také ukládat do mezipaměti na počítačích s Windows serverem pomocí Azure File Sync pro rychlý přístup blízko místa, kde se data používají.

Tento článek obsahuje odpovědi na běžné dotazy týkající se funkcí a funkcí služby Azure Files, včetně použití Azure File Sync se soubory Azure. Pokud nevidíte odpověď na svoji otázku, můžete nás kontaktovat prostřednictvím následujících kanálů (v pořadí eskalace):

1. Část s poznámkami tohoto článku.
2. [Azure Storage Fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
3. [Soubory Azure ve službě UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Podpora Microsoftu. Chcete-li vytvořit novou žádost o podporu, v Azure Portal na kartě **help** klikněte na tlačítko **pomoc a podpora** a pak vyberte **Nová žádost o podporu**.

## <a name="general"></a>Obecné
* <a id="why-files-useful"></a>
  ,**jak se soubory Azure hodí?**  
   Pomocí služby soubory Azure můžete vytvářet sdílené složky v cloudu, aniž byste odpovědni za správu režie fyzického serveru, zařízení nebo zařízení. Monotonousme to pro vás, včetně použití aktualizací operačního systému a nahrazení chybných disků. Další informace o scénářích, které vám můžou pomoct soubory Azure, najdete v tématu [Proč jsou soubory Azure užitečné](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>
  **Jaké jsou různé způsoby přístupu k souborům ve službě soubory Azure?**  
    Sdílenou složku můžete připojit na svém místním počítači pomocí protokolu SMB 3,0, nebo můžete použít nástroje, jako je [Průzkumník služby Storage](https://storageexplorer.com/) k přístupu k souborům ve sdílené složce. Z vaší aplikace můžete k souborům ve sdílené složce Azure přistupovat pomocí klientských knihoven pro úložiště, rozhraní REST API, PowerShellu nebo Azure CLI.

* <a id="what-is-afs"></a>
  **co je Azure File Sync?**  
    Pomocí Azure File Sync můžete centralizovat sdílené složky ve vaší organizaci ve službě soubory Azure a zároveň udržet flexibilitu, výkon a kompatibilitu místního souborového serveru. Azure File Sync transformuje počítače s Windows serverem do rychlé mezipaměti sdílené složky Azure. Pro místní přístup k datům můžete použít jakýkoli protokol, který je dostupný na Windows serveru, včetně SMB, NFS (Network File System) a služby protokol FTP (File Transfer Protocol) (FTPS). Můžete mít tolik mezipamětí, kolik potřebujete po celém světě.

* <a id="files-versus-blobs"></a>
  **Proč se pro moje data používá sdílená složka Azure a Azure Blob Storage?**  
    Azure Files a Azure Blob Storage nabízí jak ukládat velké objemy dat v cloudu, ale jsou užitečné pro mírně odlišnou potřebu. 
    
    Úložiště objektů BLOB v Azure je užitečné pro rozsáhlé cloudové aplikace, které potřebují ukládat nestrukturovaná data. Pro maximalizaci výkonu a škálování je úložiště objektů BLOB v Azure jednodušší abstrakce úložiště než skutečný systém souborů. K úložišti objektů blob Azure se dostanete jenom prostřednictvím klientských knihoven založených na REST (nebo přímo prostřednictvím protokolu založeného na REST).

    Soubory Azure jsou konkrétně systémem souborů. Soubory Azure obsahují všechny abstrakce souborů, které znáte a máte rádi od let práce s místními operačními systémy. Podobně jako úložiště objektů BLOB v Azure nabízí služba soubory Azure rozhraní REST a klientské knihovny založené na REST. Na rozdíl od služby Azure Blob Storage nabízí Azure Files přístup SMB ke sdíleným složkám souborů Azure. Pomocí protokolu SMB můžete připojit sdílenou složku Azure přímo v systému Windows, Linux nebo macOS, a to buď místně, nebo na cloudových virtuálních počítačích, aniž byste museli psát žádný kód nebo připojovat žádné speciální ovladače k systému souborů. Sdílené složky Azure můžete také ukládat do mezipaměti na místních souborových serverech pomocí Azure File Sync pro rychlý přístup blízko místa, kde se data používají. 
   
    Podrobnější popis rozdílů mezi soubory Azure a úložištěm objektů BLOB v Azure najdete v tématu věnovaném [rozhodování, kdy používat úložiště objektů blob Azure, soubory Azure nebo disky Azure](../common/storage-decide-blobs-files-disks.md). Další informace o službě Azure Blob Storage najdete v tématu [Úvod do úložiště objektů BLOB](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**Proč místo disků Azure používám sdílenou složku Azure?**  
    Disk na discích Azure je prostě disk. Pokud chcete získat hodnotu z disků Azure, musíte připojit disk k virtuálnímu počítači, který běží v Azure. Disky Azure je možné použít pro všechno, co byste použili disk pro na místním serveru. Můžete ji použít jako systémový disk s operačním systémem, jako odkládací prostor pro operační systém nebo jako vyhrazené úložiště pro aplikaci. Zajímavou alternativou pro disky Azure je vytvoření souborového serveru v cloudu, který se bude používat na stejných místech, kde můžete použít sdílenou složku Azure. Nasazení souborového serveru v Azure Virtual Machines je vysoce výkonným způsobem, jak získat úložiště souborů v Azure, když budete potřebovat možnosti nasazení, které aktuálně nejsou podporované soubory Azure (například podpora protokolu NFS nebo Premium Storage). 

    Spuštění souborového serveru s disky Azure jako úložiště back-endu je ale většinou mnohem dražší než použití sdílené složky Azure, a to z několika důvodů. Nejdřív je potřeba platit i za to, že budete mít za následek, že za běhu jednoho nebo více virtuálních počítačů Azure platíte náklady. Za druhé musíte taky spravovat virtuální počítače, které se používají ke spuštění souborového serveru. Například zodpovídáte za upgrady operačního systému. Nakonec, pokud nakonec budete potřebovat ukládat data do místní mezipaměti, je až na to, abyste nastavili a spravovali replikační technologie, jako je třeba replikace systém souborů DFS (Distributed File System) (DFSR), aby k tomu mohlo dojít.

    Jedním ze způsobů, jak získat nejlepší z obou souborů Azure a souborového serveru, který je hostovaný v Azure Virtual Machines (kromě používání disků Azure jako back-endu úložiště) je instalace Azure File Sync na souborovém serveru, který je hostovaný na cloudovém virtuálním počítači. Pokud je sdílená složka Azure ve stejné oblasti jako souborový server, můžete povolit vrstvení cloudu a nastavit rozsah volného místa v procentech na maximum (99%). Tím se zajistí minimální duplicita dat. Můžete také použít libovolné aplikace, které požadujete u souborových serverů, jako třeba aplikace, které vyžadují podporu protokolu NFS.

    Informace o možnostech nastavení vysoce výkonného a vysoce dostupného souborového serveru v Azure najdete v tématu [nasazení clusterů hostů virtuálních počítačů s IaaS v Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). Podrobnější popis rozdílů mezi soubory Azure a disky Azure najdete v tématu věnovaném rozhodování, [kdy používat úložiště objektů blob Azure, soubory Azure nebo disky Azure](../common/storage-decide-blobs-files-disks.md). Další informace o discích Azure najdete v tématu [Přehled azure Managed disks](../../virtual-machines/windows/managed-disks-overview.md).

* <a id="get-started"></a>
  **návody začít používat soubory Azure?**  
   Začínáme se službou Azure Files je snadné. Nejdřív [Vytvořte sdílenou složku](storage-how-to-create-file-share.md)a potom ji připojte v preferovaném operačním systému: 

  * [Připojení v systému Windows](storage-how-to-use-files-windows.md)
  * [Připojení v systému Linux](storage-how-to-use-files-linux.md)
  * [Připojení v macOS](storage-how-to-use-files-mac.md)

    Podrobnější příručku k nasazení sdílené složky Azure, která nahrazuje produkční sdílené složky ve vaší organizaci, najdete v tématu [Plánování nasazení služby soubory Azure](storage-files-planning.md).

* <a id="redundancy-options"></a>
  **Jaké možnosti redundance úložiště podporují soubory Azure?**  
    Soubory Azure v současné době podporují místně redundantní úložiště (LRS), zónu redundantní úložiště (ZRS), geograficky redundantní úložiště (GRS) a geograficky-Zone redundantní úložiště (GZRS) (Preview). V budoucnu plánujeme podporovat geograficky redundantní úložiště s přístupem pro čtení (RA-GRS), ale v tuto chvíli nemáte časové osy ke sdílení.

* <a id="tier-options"></a>
  **Jaké úrovně úložiště se ve službě soubory Azure podporují?**  
    Soubory Azure podporují dvě úrovně úložiště: Premium a Standard. Standardní sdílené složky se vytvářejí v účtech úložiště pro obecné účely (GPv1 nebo GPv2) a sdílené složky Premium se vytvářejí v účtech úložiště souborů. Přečtěte si další informace o tom, jak vytvořit [standardní sdílené složky](storage-how-to-create-file-share.md) a [soubory úrovně Premium](storage-how-to-create-premium-fileshare.md). 
    
    > [!NOTE]
    > Nemůžete vytvářet sdílené složky Azure z účtů úložiště objektů BLOB nebo účtů úložiště úrovně *Premium* pro obecné účely (GPv1 nebo GPv2). Standardní sdílené složky Azure se musí vytvořit jenom ve *standardních* účtech pro obecné účely a sdílené složky Azure úrovně Premium se musí vytvořit jenom v účtech úložiště úložiště. Účty úložiště úrovně *Premium* pro obecné účely (GPv1 a GPv2) jsou jenom pro objekty blob stránky úrovně Premium. 

* <a id="give-us-feedback"></a>
  **ve skutečnosti chci zobrazit konkrétní funkci přidanou do služby soubory Azure. Můžete ho přidat?**  
    Tým souborů Azure vás zajímá o jakékoli názory a zpětnou vazbu týkající se naší služby. Hlasujte prosím na žádostech o funkce ve [službě Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)! Těšíme se, že vám bude dodána spousta nových funkcí.

## <a name="azure-file-sync"></a>Synchronizace souborů Azure

* <a id="afs-region-availability"></a>
  **Jaké oblasti jsou podporované pro Azure File Sync?**  
    Seznam oblastí, které jsou k dispozici, najdete v části [dostupnost oblasti](storage-sync-files-planning.md#region-availability) v příručce pro plánování Azure File Sync. Budeme průběžně přidávají podporu pro další oblasti, včetně neveřejných oblastí.

* <a id="cross-domain-sync"></a>
  **můžou být ve stejné skupině synchronizace servery připojené k doméně a jiné domény?**  
    Ano. Skupina synchronizace může obsahovat koncové body serveru, které mají jiné členství ve službě Active Directory, a to i v případě, že nejsou připojeny k doméně. I když tato konfigurace technicky funguje, nedoporučujeme ji používat jako typickou konfiguraci, protože seznamy řízení přístupu (ACL), které jsou definované pro soubory a složky na jednom serveru, nemusí být možné vyhovět jinými servery ve skupině synchronizace. Pro dosažení nejlepších výsledků doporučujeme synchronizovat mezi servery, které jsou ve stejné doménové struktuře služby Active Directory, mezi servery, které jsou v různých doménových strukturách služby Active Directory, ale mají vytvořené vztahy důvěryhodnosti, nebo mezi servery, které nejsou v doméně. Doporučujeme, abyste se vyhnuli používání kombinace těchto konfigurací.

* <a id="afs-change-detection"></a>
  **jsem soubor vytvořili přímo ve sdílené složce Azure pomocí protokolu SMB nebo na portálu. Jak dlouho trvá, než se soubor synchronizuje se servery ve skupině synchronizace?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**Pokud se stejný soubor na dvou serverech změní přibližně na stejný čas, co se stane?**  
    Azure File Sync používá jednoduchou strategii řešení konfliktů: obě změny souborů, které jsou na dvou serverech změněny současně. Poslední zapsaná změna zachovává původní název souboru. Starší soubor má "zdrojový" počítač a číslo konfliktu připojené k názvu. Sleduje tuto taxonomii: 
   
    \<FileNameWithoutExtension \> - \<MachineName \> \[ -# \]. \<ext \>  

    Například první konflikt CompanyReport. docx by se stal CompanyReport-CentralServer. docx, pokud CentralServer v místě, kde došlo k dřívějšímu zápisu. Druhý konflikt by byl pojmenovaný CompanyReport-CentralServer-1. docx. Azure File Sync podporuje soubory konfliktů 100 na jeden soubor. Po dosažení maximálního počtu souborů konfliktů se soubor nesynchronizuje, dokud nebude počet konfliktních souborů menší než 100.

* <a id="afs-storage-redundancy"></a>
  **je geograficky redundantní úložiště podporované pro Azure File Sync?**  
    Ano, soubory Azure podporují místně redundantní úložiště (LRS) i geograficky redundantní úložiště (GRS). Pokud zahájíte převzetí služeb při selhání účtu úložiště mezi spárované oblasti z účtu nakonfigurovaného pro GRS, společnost Microsoft doporučuje, abyste pocházeli s novou oblastí jako se zálohováním dat. Azure File Sync nezačne automaticky synchronizovat s novou primární oblastí. 

* <a id="sizeondisk-versus-size"></a>
  **Proč velikost vlastnosti *disku* pro soubor neodpovídá vlastnosti *Size* po použití Azure File Sync?**  
  Podívejte se na téma [Principy vrstvení cloudu](storage-sync-cloud-tiering.md#sizeondisk-versus-size).

* <a id="is-my-file-tiered"></a>
   **, jak zjistím, jestli byl soubor vrstvený?**  
  Podívejte se na téma [Principy vrstvení cloudu](storage-sync-cloud-tiering.md#is-my-file-tiered).

* <a id="afs-recall-file"></a>**Soubor, který chcete použít, byl vrstven. Jak mohu soubor odvolat na disk a použít ho místně?**  
  Podívejte se na téma [Principy vrstvení cloudu](storage-sync-cloud-tiering.md#afs-recall-file).

* <a id="afs-force-tiering"></a>
  **návody vynutit vrstvení souboru nebo adresáře?**  
  Podívejte se na téma [Principy vrstvení cloudu](storage-sync-cloud-tiering.md#afs-force-tiering).

* <a id="afs-effective-vfs"></a>
  ,**jak se interpretuje *volné místo na svazku* , když mám na svazku více koncových bodů serveru?**  
  Podívejte se na téma [Principy vrstvení cloudu](storage-sync-cloud-tiering.md#afs-effective-vfs).

* <a id="afs-files-excluded"></a>
  **které soubory nebo složky jsou automaticky vyloučeny pomocí Azure File Sync?**  
    Ve výchozím nastavení Azure File Sync vyloučí následující soubory:
  * Desktop. ini
  * miniatury. DB
  * ehThumbs. DB
  * ~$ \*. \*
  * \*. laccdb
  * \*. tmp
  * 635D02A9D91C401B97884B82B3BCDAEA. \*

    Ve výchozím nastavení jsou vyloučené také následující složky:

  * \System Volume Information
  * \$RECYCLE. BIN
  * \SyncShareState

* <a id="afs-os-support"></a>
   můžu**použít Azure File Sync se systémem Windows Server 2008 R2, Linux nebo moje zařízení úložiště připojené k síti (NAS)?**  
    V současné době Azure File Sync podporuje jenom Windows Server 2019, Windows Server 2016 a Windows Server 2012 R2. V tuto chvíli nemáme žádné jiné plány, které bychom mohli sdílet, ale otevřeli jsme podporu dalších platforem na základě poptávky zákazníků. Dejte nám prosím na [Azure soubory ve službě UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) , které vaše platformy chceme podporovat.

* <a id="afs-tiered-files-out-of-endpoint"></a>
  **Proč existují vrstvené soubory mimo obor názvů koncového bodu serveru?**  
    Před Azure File Sync agenta verze 3 Azure File Sync blokované přesun vrstvených souborů mimo koncový bod serveru, ale na stejném svazku jako koncový bod serveru. Operace kopírování, přesun nevrstvených souborů a přesuny vrstvených na jiné svazky nebyly ovlivněny. Důvodem tohoto chování je implicitní předpoklad, že Průzkumník souborů a další rozhraní Windows API mají operace přesunu na stejný svazek (skoro) okamžité operace přejmenování. To znamená, že Průzkumník souborů nebo jiné metody přesunu (například příkazový řádek nebo PowerShell) nereagují při Azure File Sync znovu zavolá data z cloudu. Počínaje [Azure File Sync agenta verze 3.0.12.0](storage-files-release-notes.md#supported-versions)Azure File Sync umožní přesunout vrstvený soubor mimo koncový bod serveru. Vyhnete se negativním dopadům, které jsme dříve uvedli, povolením vrstveného souboru jako vrstveného souboru mimo koncový bod serveru a následným vyvoláním souboru na pozadí. To znamená, že se okamžitě přesune na stejný svazek a my provedeme veškerou práci, která načte soubor na disk, až se přesun dokončí. 

* <a id="afs-do-not-delete-server-endpoint"></a>
   mám**problém s Azure File Sync na mém serveru (synchronizace, vrstvení cloudu atd.). Mám odebrat a znovu vytvořit koncový bod serveru?**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
   můžu**přesunout službu synchronizace úložiště nebo účet úložiště do jiné skupiny prostředků nebo předplatného?**  
   Ano, služba synchronizace úložiště nebo účet úložiště se můžou přesunout do jiné skupiny prostředků nebo předplatného v rámci stávajícího tenanta Azure AD. Pokud je účet úložiště přesunutý, musíte mu udělit přístup ke službě hybridní Synchronizace souborů k účtu úložiště (podívejte se, [jestli má Azure File Sync přístup k účtu úložiště](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync nepodporuje přesun předplatného na jiného tenanta Azure AD.
    
* <a id="afs-ntfs-acls"></a>
  **Azure File Sync zachovávají seznamy řízení přístupu NTFS adresáře nebo souborů společně s daty uloženými v souborech Azure?**

    Seznamy řízení přístupu systému souborů NTFS přenesené z místních souborových serverů jsou trvalé Azure File Sync jako metadata. Soubory Azure nepodporují ověřování pomocí přihlašovacích údajů Azure AD pro přístup ke sdíleným složkám spravovaným službou Azure File Sync.
    
## <a name="security-authentication-and-access-control"></a>Zabezpečení, ověřování a řízení přístupu
* <a id="ad-support"></a>
**je ověřování na základě identity a řízení přístupu podporované soubory Azure?**  
    
    Ano, soubory Azure podporují ověřování na základě identity a řízení přístupu využívajících službu Azure AD Domain Service (Azure služba AD DS). Služba Azure služba AD DS Authentication přes SMB pro soubory Azure umožňuje virtuálním počítačům Azure služba AD DS připojeným k doméně přistupovat ke sdíleným složkám, adresářům a souborům pomocí přihlašovacích údajů Azure AD. Další podrobnosti najdete v článku [Přehled podpory ověřování Azure Files Azure Active Directory Domain Service (azure služba AD DS) pro přístup přes protokol SMB](storage-files-active-directory-overview.md). 

    Azure Files nabízí dva další způsoby, jak spravovat řízení přístupu:

    - Pomocí sdílených přístupových podpisů (SAS) můžete vygenerovat tokeny, které mají určitá oprávnění a které jsou platné po zadaný časový interval. Můžete například vygenerovat token s přístupem jen pro čtení ke konkrétnímu souboru, který má vypršení platnosti 10 minut. Každý, kdo má token, zatímco je token platný, má k tomuto souboru přístup jen pro čtení po dobu 10 minut. Klíče sdíleného přístupového podpisu se v současné době podporují jenom prostřednictvím REST API nebo v klientských knihovnách. Sdílenou složku Azure je nutné připojit přes protokol SMB pomocí klíčů účtu úložiště.

    - Azure File Sync zachovává a replikuje všechny volitelné seznamy řízení přístupu (DACL) a seznamy DACL (ať už jsou založené na službě Active Directory nebo místní) na všechny koncové body serveru, na které se synchronizuje. Vzhledem k tomu, že se Windows Server už může ověřit pomocí služby Active Directory, Azure File Sync je efektivní možnost zarážky, dokud se nedosáhne plné podpory ověřování založeného na službě Active Directory a podpory seznamu ACL.
    
    V případě, že chcete mít k dispozici komplexní reprezentace všech protokolů podporovaných v Azure Storage Services, můžete použít odkaz pro [autorizaci Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) . 

* <a id="ad-support-devices"></a>
**Azure Files azure služba AD DS Authentication podporuje přístup přes protokol SMB pomocí přihlašovacích údajů Azure AD ze zařízení, která jsou připojená k Azure AD nebo zaregistrovaná?**

    Ne, tento scénář není podporován.

* <a id="ad-support-rest-apis"></a>
**jsou k dispozici rozhraní REST API pro podporu seznamů přístupových práv získat/nastavit/kopírovat adresář/soubor NTFS?**

    V současnosti nepodporujeme rozhraní REST API k získání, nastavení nebo kopírování seznamů řízení přístupu NTFS pro adresáře nebo soubory.

* <a id="ad-vm-subscription"></a>
 můžu**získat přístup k souborům Azure pomocí přihlašovacích údajů Azure AD z virtuálního počítače v jiném předplatném?**

    Pokud je předplatné, pod kterým je nainstalovaná sdílená složka, přidruženo ke stejnému tenantovi služby Azure AD jako Azure AD Domain Services nasazení, ke kterému je připojený virtuální počítač, pak můžete přistupovat k souborům Azure pomocí stejných přihlašovacích údajů Azure AD. Omezení je uloženo v předplatném, ale na přidruženém tenantovi služby Azure AD.    
    
* <a id="ad-support-subscription"></a>
 můžu**Povolit Azure Files azure služba AD DS ověřování pomocí tenanta Azure AD, který se liší od primárního tenanta, ke kterému je sdílená složka přidružená?**

    Ne, soubory Azure podporují integraci Azure služba AD DS jenom s klientem služby Azure AD, který se nachází ve stejném předplatném jako sdílená složka. K tenantovi služby Azure AD může být přidruženo pouze jedno předplatné.

* <a id="ad-linux-vms"></a>
 služby**Azure Files azure služba AD DS Authentication podporují virtuální počítače se systémem Linux?**

    Ne, ověřování z virtuálních počítačů se systémem Linux není podporováno.

* <a id="ad-aad-smb-afs"></a>
 můžu**využívat Azure Files azure služba AD DS ověřování u sdílených složek spravovaných pomocí Azure File Sync?**

    Ne, soubory Azure nepodporují zachování seznamů ACL systému souborů NTFS u sdílených složek spravovaných pomocí Azure File Sync. Seznamy ACL souborů přenesené z místních souborových serverů jsou trvalé Azure File Sync. Všechny seznamy ACL NTFS nakonfigurované nativně proti souborům Azure budou přepsány službou Azure File Sync. Soubory Azure navíc nepodporují ověřování pomocí přihlašovacích údajů Azure AD pro přístup ke sdíleným složkám spravovaným službou Azure File Sync.

* <a id="encryption-at-rest"></a>
 **, jak se dá zajistit, aby byla sdílená složka Azure v klidovém stavu šifrovaná?**  

    Ano. Další informace najdete v tématu [šifrování služby Azure Storage](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>
**způsob poskytnutí přístupu ke konkrétnímu souboru pomocí webového prohlížeče?**  

    Pomocí sdílených přístupových podpisů můžete vygenerovat tokeny, které mají určitá oprávnění a které jsou platné po zadaný časový interval. Můžete například vygenerovat token, který poskytuje přístup k určitému souboru jen pro čtení, a to po nastavenou dobu. Kdokoli, kdo má adresu URL, může k souboru přistupovat přímo z libovolného webového prohlížeče, pokud je token platný. Můžete snadno vygenerovat klíč sdíleného přístupového podpisu z uživatelského rozhraní, jako je Průzkumník služby Storage.

* <a id="file-level-permissions"></a>
**je možné pro složky ve sdílené složce určit oprávnění jen pro čtení nebo jen pro zápis?**  

    Pokud sdílenou složku připojíte pomocí protokolu SMB, nemáte pro oprávnění kontrolu na úrovni složek. Pokud ale vytvoříte sdílený přístupový podpis pomocí REST API nebo klientských knihoven, můžete pro složky v rámci sdílené složky zadat oprávnění jen pro čtení nebo jen pro zápis.

* <a id="ip-restrictions"></a>
 můžu**pro sdílenou složku Azure implementovat omezení IP adres?**  

    Ano. Přístup ke sdílené složce Azure se může omezit na úrovni účtu úložiště. Další informace najdete v tématu [konfigurace Azure Storage bran firewall a virtuálních sítí](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>
**Jaké zásady dodržování předpisů podporují soubory Azure?**  

   Soubory Azure běží nad stejnou architekturou úložiště, která se používá v jiných službách úložiště v Azure Storage. Služba soubory Azure používá stejné zásady dodržování předpisů, které se používají v jiných službách úložiště Azure. Další informace o kompatibilitě dat Azure Storage najdete v tématu [Azure Storage nabídek dodržování předpisů](https://docs.microsoft.com/azure/storage/common/storage-compliance-offerings)a na webu [Microsoft Trust Center](https://microsoft.com/trustcenter/default.aspx).

## <a name="on-premises-access"></a>Místní přístup

* <a id="port-445-blocked"></a>
**poskytovatel internetových služeb nebo zablokuje Port 445, který selhává při připojování souborů Azure. Co mám dělat?**

    Další informace o [různých způsobech blokovaného alternativního řešení najdete na portu 445](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems#cause-1-port-445-is-blocked). Soubory Azure umožňují připojení pomocí protokolu SMB 3,0 (s podporou šifrování) mimo oblast nebo Datacenter. Protokol SMB 3,0 zavedl mnoho funkcí zabezpečení, včetně šifrování kanálů, které je velmi bezpečné pro použití přes Internet. Je však možné, že port 445 byl zablokován z důvodu historických důvodů ohrožení zabezpečení zjištěných v nižších verzích protokolu SMB. V ideálním případě by měl být port zablokovaný jenom pro provoz SMB 1,0 a na všech klientech by měl být vypnutý protokol SMB 1,0.

* <a id="expressroute-not-required"></a>
 musím**použít Azure ExpressRoute k připojení k souborům Azure nebo k použití Azure File Sync v místním prostředí?**  

    Ne. ExpressRoute se nevyžaduje pro přístup ke sdílené složce Azure. Pokud připojujete sdílenou složku Azure přímo v místním prostředí, je nutné, aby byl pro přístup k Internetu otevřený port 445 (odchozí TCP) (Jedná se o port, který protokol SMB používá ke komunikaci). Pokud používáte Azure File Sync, vyžaduje se pro přístup HTTPS port 443 (odchozí TCP) (bez požadavku SMB). ExpressRoute ale *můžete* použít pro jednu z těchto možností přístupu.

* <a id="mount-locally"></a>
,**Jak můžu připojit sdílenou složku Azure na svém místním počítači?**  

    Sdílenou složku můžete připojit pomocí protokolu SMB, pokud je otevřený port 445 (odchozí TCP) a váš klient podporuje protokol SMB 3,0 (například pokud používáte Windows 10 nebo Windows Server 2016). Pokud je port 445 zablokovaný zásadami vaší organizace nebo vaším poskytovatelem internetových služeb, můžete k přístupu ke sdílené složce Azure použít Azure File Sync.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**návody zálohovat sdílenou složku Azure?**  
    Pro ochranu před náhodným odstraněním můžete použít periodické [snímky sdílené složky](storage-snapshots-files.md) . Můžete také použít nástroj pro zálohování AzCopy, Robocopy nebo třetí strany, který může zálohovat připojenou sdílenou složku. Azure Backup nabízí zálohu souborů Azure. Přečtěte si další informace o [Zálohování sdílených složek Azure pomocí Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-files).

## <a name="share-snapshots"></a>Sdílet snímky

### <a name="share-snapshots-general"></a>Snímky sdílené složky: Obecné
* <a id="what-are-snaphots"></a>
**co jsou snímky sdílení souborů?**  
    Pomocí snímků sdílené složky Azure můžete vytvořit verzi sdílených složek, která je jen pro čtení. Můžete také použít soubory Azure ke kopírování starší verze obsahu zpátky do stejné sdílené složky, do alternativního umístění v Azure nebo v místním prostředí pro další úpravy. Další informace o snímcích sdílených složek najdete v tématu [Přehled sdílení snímků](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>
**kde se ukládají snímky sdílené složky?**  
    Snímky sdílené složky se ukládají ve stejném účtu úložiště jako sdílená složka.

* <a id="snapshot-consistency"></a>
**sdílí aplikace snímky, je konzistentní?**  
    Ne, snímky sdílené složky nejsou konzistentní vzhledem k aplikacím. Před použitím snímku sdílené složky musí uživatel vyprázdnit zápisy z aplikace do sdílené složky.

* <a id="snapshot-limits"></a>
**se omezuje počet snímků sdílené složky, které můžu použít?**  
    Ano. Soubory Azure můžou uchovávat maximálně 200 snímků sdílené složky. Snímky sdílené složky se nepočítají směrem k kvótě sdílení, takže neexistují žádné limity pro sdílení na celkovém prostoru, který používají všechny snímky sdílené složky. Omezení účtu úložiště se pořád používají. Po 200 sdílení snímků musíte odstranit starší snímky pro vytvoření nových snímků sdílené složky.

* <a id="snapshot-cost"></a>
**Kolik stojí sdílení snímků?**  
    Náklady na standardní transakci a standardní úložiště se vztahují na snímek. Snímky jsou přírůstkové. Základní snímek je samotným sdílením. Všechny následné snímky jsou přírůstkové a uloží rozdíl jenom z předchozího snímku. To znamená, že rozdílové změny, které budou zobrazeny ve vyúčtování, budou minimální, pokud je vaše zatížení minimální. Informace o cenách standardních souborů Azure najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/storage/files/) . V současné době se vám podíváme na velikost spotřebované snímkem sdílení a porovnáte se s využitím účtované kapacity. Pracujeme na nástrojích, abychom vylepšili vytváření sestav.

* <a id="ntfs-acls-snaphsots"></a>
**jsou seznamy řízení přístupu systému souborů NTFS u adresářů a souborů trvale uložené ve sdílených snímcích?**  
    Seznamy řízení přístupu (ACL) NTFS u adresářů a souborů jsou uložené v snímcích sdílené složky.

### <a name="create-share-snapshots"></a>Vytváření snímků sdílené složky
* <a id="file-snaphsots"></a>
 můžu**vytvořit sdílený snímek jednotlivých souborů?**  
    Snímky sdílené složky se vytvářejí na úrovni sdílené složky. Jednotlivé soubory můžete obnovit ze snímku sdílené složky, ale nemůžete vytvářet snímky sdílené složky na úrovni souborů. Pokud jste ale nastavili snímek sdílené složky na úrovni sdílené složky a chcete vypsat snímky sdílené složky, ve kterých se konkrétní soubor změnil, můžete to provést v části **předchozí verze** ve sdílené složce připojené k Windows. 
    
    Pokud potřebujete funkci snímkování souborů, dejte nám prosím na [Azure soubory UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)informace.

* <a id="encrypted-snapshots"></a>
 můžu**vytvářet sdílené snímky pro zašifrované sdílení souborů?**  
    Můžete pořídit snímek sdílené složky Azure s povoleným šifrováním v klidovém formátu. Soubory můžete obnovit ze snímku sdílené složky do šifrované sdílené složky. Pokud je vaše sdílená složka šifrovaná, váš snímek sdílené složky je také zašifrovaný.

* <a id="geo-redundant-snaphsots"></a>
**jsou snímky sdílené složky geograficky redundantní?**  
    Snímky sdílené složky mají stejnou redundanci jako sdílená složka Azure, pro kterou byly pořízeny. Pokud jste pro svůj účet vybrali geograficky redundantní úložiště, váš snímek sdílené složky se také ukládá redundantním způsobem do spárované oblasti.

### <a name="manage-share-snapshots"></a>Správa snímků sdílené složky
* <a id="browse-snapshots-linux"></a>
**můžu Procházet snímky sdílené složky ze systému Linux?**  
    Pomocí rozhraní příkazového řádku Azure můžete vytvářet, vypisovat, Procházet a obnovovat snímky sdílené složky v systému Linux.

* <a id="copy-snapshots-to-other-storage-account"></a>
 můžu**zkopírovat snímky sdílené složky do jiného účtu úložiště?**  
    Soubory můžete kopírovat ze snímků sdílené složky do jiného umístění, ale snímky sdílené složky nemůžete kopírovat sami.

### <a name="restore-data-from-share-snapshots"></a>Obnovit data ze snímků sdílené složky
* <a id="promote-share-snapshot"></a>
 můžu**povýšit snímek sdílené složky na základní sdílenou složku?**  
    Data můžete kopírovat ze snímku sdílené složky do libovolného jiného cíle. Nepovedlo se zvýšit úroveň snímku sdílené složky na základní sdílenou složku.

* <a id="restore-snapshotted-file-to-other-share"></a>
 můžu**obnovit data ze snímku sdílené složky na jiný účet úložiště?**  
    Ano. Soubory ze snímku sdílené složky můžete zkopírovat do původního umístění nebo do alternativního umístění, které obsahuje buď stejný účet úložiště, nebo jiný účet úložiště, a to buď ve stejné oblasti, nebo v různých oblastech. Můžete také kopírovat soubory do místního umístění nebo do jakéhokoli jiného cloudu.    
  
### <a name="clean-up-share-snapshots"></a>Vyčištění snímků sdílených složek
* <a id="delete-share-keep-snapshots"></a>
**můžu odstranit sdílenou složku, ale ne odstranit snímky sdílené složky?**  
    Pokud máte na sdílené složce snímky aktivních složek, nemůžete sdílenou složku odstranit. Pomocí rozhraní API můžete odstranit snímky sdílené složky společně se sdílenou složkou. Můžete také odstranit snímky sdílené složky i sdílenou složku v Azure Portal.

* <a id="delete-share-with-snapshots"></a>
**co se stane s snímky sdílené složky, když odstraním svůj účet úložiště?**  
    Pokud odstraníte účet úložiště, odstraní se i snímky sdílené složky.

## <a name="billing-and-pricing"></a>Fakturace a ceny
* <a id="vm-file-share-network-traffic"></a>
 se**síťový provoz mezi virtuálním počítačem Azure a sdílenou složkou Azure počítá jako externí šířka pásma, která se účtuje do předplatného?**  
    Pokud je sdílená složka a virtuální počítač ve stejné oblasti Azure, za přenos mezi sdílenou složkou a virtuálním počítačem se neúčtují žádné další poplatky. Pokud je sdílená složka a virtuální počítač v různých oblastech, provoz mezi nimi se účtuje jako externí šířka pásma.

* <a id="share-snapshot-price"></a>
**Kolik stojí sdílení snímků?**  
     Během období Preview se neúčtují žádné poplatky za kapacitu snímku sdílené složky. Platí pro výstup služby Storage úrovně Standard a cena za transakce. Po obecné dostupnosti se předplatná účtují za kapacitu a transakce na snímcích sdílené složky.
     
     Snímky sdílené složky jsou přírůstkové. Základní snímek sdílené složky je samotný podíl. Všechny následné snímky sdílené složky jsou přírůstkové a ukládají pouze rozdíly z předchozího snímku sdílené složky. Účtuje se vám jenom změněný obsah. Pokud máte sdílenou složku s 100 GiB dat, ale od posledního snímku sdílené složky se změnila pouze 5 GiB, snímek sdílené složky spotřebovává pouze 5 dalších GiB a účtuje se vám 105 GiB. Další informace o cenách a za standardní výstupních operací najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="scale-and-performance"></a>Škálování a výkon
* <a id="files-scale-limits"></a>
**Jaké jsou limity škálování souborů Azure?**  
    Informace o škálovatelnosti a cílech výkonu pro soubory Azure najdete v tématu [škálovatelnost a cíle výkonnosti souborů Azure](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>
**jaké velikosti jsou k dispozici pro sdílené složky Azure?**  
    Velikosti sdílených složek Azure (Premium a Standard) se dají škálovat až na 100 TiB. Pokyny pro připojování k větším sdíleným složkám pro úroveň Standard najdete v části připojení [k větším sdíleným složkám (úroveň Standard)](storage-files-planning.md#onboard-to-larger-file-shares-standard-tier) Průvodce plánováním.

* <a id="lfs-performance-impact"></a>
**rozšiřuje svou kvótu své sdílené složky na moje úlohy nebo Azure File Sync?**
    
    Ne. Rozšiřování kvóty nebude mít vliv na vaše úlohy ani Azure File Sync.

* <a id="open-handles-quota"></a>
**kolik klientů má přístup ke stejnému souboru současně?**    
    V jednom souboru je kvóta 2 000 otevřených popisovačů. Když máte 2 000 otevřených popisovačů, zobrazí se chybová zpráva oznamující, že se dosáhlo kvóty.

* <a id="zip-slow-performance"></a>
 má**výkon při rozbalení souborů v souborech Azure pomalu. Co mám dělat?**  
    Pro přenos velkého počtu souborů do souborů Azure doporučujeme použít AzCopy (pro Windows, ve verzi Preview pro Linux a UNIX) nebo Azure PowerShell. Tyto nástroje jsou optimalizované pro přenos v síti.

* <a id="slow-perf-windows-81-2012r2"></a>
**Proč je po připojení sdílené složky Azure na Windows serveru 2012 R2 nebo Windows 8.1 pomalý výkon?**  
    Při připojování sdílené složky Azure v systému Windows Server 2012 R2 a Windows 8.1 došlo k známému problému. Tento problém byl opraven v kumulativní aktualizaci pro Windows 8.1 a Windows Server 2012 R2 v dubnu 2014. Pro zajištění optimálního výkonu zajistěte, aby byla tato oprava použita pro všechny instance systému Windows Server 2012 R2 a Windows 8.1. (Opravy systému Windows byste měli vždycky dostávat prostřednictvím web Windows Update.) Další informace najdete v článku o tom, jaký je přidružený článek znalostní báze Microsoft Knowledge Base s [pomalým výkonem při přístupu k souborům Azure z Windows 8.1 nebo serveru 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Funkce a interoperabilita s ostatními službami
* <a id="cluster-witness"></a>
**můžu použít sdílenou složku Azure jako *určující sdílenou složku* pro cluster s podporou převzetí služeb při selhání v systému Windows Server?**  
    V současné době není tato konfigurace pro sdílenou složku Azure podporována. Další informace o tom, jak nastavit tuto službu pro úložiště objektů BLOB v Azure, najdete v tématu [nasazení cloudového clusteru pro cluster s podporou převzetí služeb při selhání](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>
 můžu**připojit sdílenou složku Azure na instanci kontejneru Azure?**  
    Ano, sdílené složky Azure jsou vhodné, pokud chcete zachovat informace po dobu životnosti instance kontejneru. Další informace najdete v tématu [připojení sdílené složky Azure ke službě Azure Container Instances](../../container-instances/container-instances-mounting-azure-files-volume.md).

* <a id="rest-rename"></a>
**je v REST API operace přejmenování?**  
    V současnosti ne.

* <a id="nested-shares"></a>
**můžu nastavit vnořené sdílené složky? Jinými slovy, sdílená složka ve sdílené složce?**  
    Ne. Sdílená složka *je* virtuální ovladač, který se dá připojit, takže vnořené sdílené složky se nepodporují.

* <a id="ibm-mq"></a>
**návody používat soubory Azure s IBM MQ?**  
    Společnost IBM vydala dokument, který pomáhá zákazníkům IBM MQ nakonfigurovat soubory Azure pomocí služby IBM. Další informace najdete v tématu [jak nastavit správce front více instancí IBM MQ pomocí služby Microsoft Azure Files](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).

## <a name="see-also"></a>Další informace najdete v tématech
* [Řešení potíží se soubory Azure v systému Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Řešení potíží se soubory Azure v systému Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Řešení potíží se Synchronizací souborů Azure](storage-sync-files-troubleshoot.md)
