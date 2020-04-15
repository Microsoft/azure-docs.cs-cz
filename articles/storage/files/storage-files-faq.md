---
title: Nejčastější dotazy (nejčastější dotazy) pro soubory Azure | Dokumenty společnosti Microsoft
description: Najděte odpovědi na nejčastější dotazy týkající se souborů Azure.
author: roygara
ms.service: storage
ms.date: 02/23/2020
ms.author: rogarana
ms.subservice: files
ms.topic: conceptual
ms.openlocfilehash: 82c516eeac6d3e88ca7b6ac1c97ebb638ba27979
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383911"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Nejčastější dotazy ke službě Azure Files
[Azure Files](storage-files-introduction.md) nabízí plně spravované sdílené složky v cloudu, které jsou přístupné prostřednictvím standardního [protokolu Server Message Block (SMB).](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) Sdílené složky Azure můžete připevnit souběžně v cloudových nebo místních nasazeních Windows, Linuxu a macOS. Sdílené složky Azure můžete také ukládat do mezipaměti na počítačích s Windows Server pomocí Azure File Sync pro rychlý přístup v blízkosti místa, kde se data používají.

Tento článek odpovídá na běžné otázky týkající se funkcí a funkcí Souborů Azure, včetně použití Azure File Sync se soubory Azure. Pokud odpověď na vaši otázku nevidíte, můžete nás kontaktovat prostřednictvím následujících kanálů (v eskalujícím pořadí):

1. Komentáře v tomto článku.
2. [Fórum úložišť Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
3. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Podpora společnosti Microsoft. Pokud chcete vytvořit novou žádost o podporu, na webu Azure portal na kartě **Nápověda** vyberte tlačítko **Nápověda + podpora** a pak vyberte **Nová žádost o podporu**.

## <a name="general"></a>Obecné
* <a id="why-files-useful"></a>
  **Jak jsou soubory Azure užitečné?**  
   Soubory Azure můžete použít k vytvoření sdílených složek v cloudu, aniž byste měli odpovědnost za správu režie fyzického serveru, zařízení nebo zařízení. Děláme monotónní práci za vás, včetně použití aktualizací operačního systému a nahrazení špatných disků. Další informace o scénářích, se kterými vám můžou pomoct Soubory Azure, najdete v článku [Proč jsou azure files užitečné](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>
  **Jaké jsou různé způsoby přístupu k souborům v Azure Files?**  
    Sdílenou složku můžete připojit k místnímu počítači pomocí protokolu SMB 3.0 nebo můžete použít nástroje, jako je [Průzkumník úložiště,](https://storageexplorer.com/) pro přístup k souborům ve sdílené složce. Z vaší aplikace můžete použít knihovny klienta úložiště, REST API, PowerShell nebo Azure CLI pro přístup k souborům ve sdílené složce Azure.

* <a id="what-is-afs"></a>
  **Co je synchronizace souborů Azure?**  
    Azure File Sync můžete použít k centralizaci sdílených složek vaší organizace v souborech Azure a současně zachovat flexibilitu, výkon a kompatibilitu místního souborového serveru. Azure File Sync transformuje vaše počítače s Windows Serverem do rychlé mezipaměti sdílené složky Azure. K místnímu přístupu k datům můžete použít libovolný protokol, který je k dispozici v systému Windows Server, včetně protokolu SMB, síťového systému souborů (NFS) a služby FTPS (File Transfer Protocol Service). Můžete mít tolik cache, kolik potřebujete po celém světě.

* <a id="files-versus-blobs"></a>
  **Proč bych pro svá data používal sdílenou složku Azure versus úložiště objektů blob Azure?**  
    Azure Files a azure blob úložiště obě nabízejí způsoby, jak ukládat velké množství dat v cloudu, ale jsou užitečné pro mírně odlišné účely. 
    
    Úložiště objektů blob Azure je užitečné pro aplikace v masčově nativní pro cloud, které potřebují ukládat nestrukturovaná data. Chcete-li maximalizovat výkon a škálování, azure blob úložiště je jednodušší abstrakce úložiště než skutečný systém souborů. K úložišti objektů Blob Azure můžete přistupovat jenom prostřednictvím klientských knihoven založených na REST (nebo přímo prostřednictvím protokolu založeného na rest).

    Azure Files je konkrétně souborový systém. Azure Files obsahuje všechny abstrakty souborů, které znáte a milujete z let práce s místními operačními systémy. Stejně jako úložiště objektů blob Azure nabízí Azure Files rozhraní REST a klientské knihovny založené na REST. Na rozdíl od úložiště objektů blob Azure nabízí Azure Files přístup s mb ke sdíleným složkám Azure. Pomocí SMB můžete připojit sdílenou složku Azure přímo ve Windows, Linuxu nebo macOS, ať už místně nebo v cloudových virtuálních počítačích, bez psaní kódu nebo připojení speciálních ovladačů k systému souborů. Sdílené složky Azure můžete také ukládat do mezipaměti na místních souborových serverech pomocí Azure File Sync pro rychlý přístup, blízko místa, kde se data používají. 
   
    Podrobnější popis rozdílů mezi soubory Azure a úložištěm objektů blob Azure najdete v [tématu Úvod do základních služeb Azure Storage](../common/storage-introduction.md). Další informace o úložišti objektů Blob Azure najdete [v tématu Úvod do úložiště objektů Blob](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**Proč bych měl použít sdílenou složku Azure místo Azure Disks?**  
    Disk v Azure Disks je jednoduše disk. Chcete-li získat hodnotu z Disky Azure, musíte připojit disk k virtuálnímu počítači, který běží v Azure. Disky Azure lze použít pro všechno, co byste použít disk pro na místním serveru. Můžete jej použít jako systémový disk operačního systému, jako odkládací místo pro operační systém nebo jako vyhrazené úložiště pro aplikaci. Zajímavé použití pro Azure Disks je vytvořit souborový server v cloudu pro použití na stejných místech, kde můžete použít sdílenou složku Azure. Nasazení souborového serveru ve virtuálních počítačích Azure je vysoce výkonný způsob, jak získat úložiště souborů v Azure, když potřebujete možnosti nasazení, které aktuálně nejsou podporované soubory Azure (například podpora protokolu nfs nebo úložiště premium). 

    Spuštění souborového serveru s Disky Azure jako back-endové úložiště je však obvykle mnohem dražší než použití sdílené složky Azure, a to z několika důvodů. Za prvé, kromě placení za diskové úložiště, musíte také zaplatit za náklady na spuštění jednoho nebo více virtuálních počítačů Azure. Za druhé je také nutné spravovat virtuální chod, které se používají ke spuštění souborového serveru. Například jste zodpovědní za upgrady operačního systému. A konečně, pokud nakonec budete vyžadovat data, která mají být uložena do mezipaměti v místním prostředí, je na vás nastavit a spravovat replikační technologie, jako je například replikace distribuovaného systému souborů (DFSR), aby se tak stalo.

    Jedním z přístupů k získání toho nejlepšího ze souborů Azure a souborového serveru, který je hostovaný ve virtuálních počítačích Azure (kromě použití Azure Disks jako back-endového úložiště), je instalace Azure File Sync na souborový server, který je hostovaný na cloudovém virtuálním počítači. Pokud je sdílená složka Azure ve stejné oblasti jako souborový server, můžete povolit vrstvení cloudu a nastavit objem volného místa na maximum (99 %). Tím je zajištěno minimální duplikace dat. Se souborovými servery můžete také použít libovolné aplikace, jako jsou aplikace, které vyžadují podporu protokolu NFS.

    Informace o možnosti nastavení vysoce výkonného a vysoce dostupného souborového serveru v Azure najdete v [tématu Nasazení clusterů hosta virtuálního počítače IaaS v Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). Podrobnější popis rozdílů mezi soubory Azure a disky Azure najdete v [tématu Úvod do základních služeb Azure Storage](../common/storage-introduction.md). Další informace o Azure Disky najdete v [tématu Přehled spravovaných disků Azure](../../virtual-machines/windows/managed-disks-overview.md).

* <a id="get-started"></a>
  **Jak můžu začít používat Soubory Azure?**  
   Začít s Azure Files je snadné. Nejprve [vytvořte sdílenou složku](storage-how-to-create-file-share.md)a pak ji připojte do upřednostňovaného operačního systému: 

  * [Připojení v systému Windows](storage-how-to-use-files-windows.md)
  * [Montáž v Linuxu](storage-how-to-use-files-linux.md)
  * [Montáž v macOS](storage-how-to-use-files-mac.md)

    Podrobnější průvodce nasazením sdílené složky Azure k nahrazení sdílených složek produkčních souborů ve vaší organizaci najdete v [tématu Plánování nasazení souborů Azure](storage-files-planning.md).

* <a id="redundancy-options"></a>
  **Jaké možnosti redundance úložiště jsou podporované soubory Azure?**  
    V současné době Azure Files podporuje místně redundantní úložiště (LRS), zónově redundantní úložiště (ZRS), geograficky redundantní úložiště (GRS) a geograficky zónově redundantní úložiště (GZRS) (preview). V budoucnu plánujeme podporovat geograficky redundantní úložiště pro přístup ke čtení (RA-GRS), ale v tuto chvíli nemáme časové osy, které bychom mohli sdílet.

* <a id="tier-options"></a>
  **Jaké vrstvy úložiště jsou podporované v Azure Files?**  
    Azure Files podporuje dvě úrovně úložiště: premium a standard. Standardní sdílené složky jsou vytvářeny v obecném smyslu (GPv1 nebo GPv2) účty úložiště a sdílené složky premium jsou vytvořeny v účtech úložiště FileStorage. Přečtěte si další informace o vytváření [standardních sdílených složek](storage-how-to-create-file-share.md) a [prémiových sdílených složek](storage-how-to-create-premium-fileshare.md). 
    
    > [!NOTE]
    > Sdílené složky Azure nelze vytvořit z účtů úložiště objektů Blob nebo z účtů úložiště *pro obecné* účely (GPv1 nebo GPv2). Standardní sdílené složky Azure musí být vytvořeny pouze ve *standardních* účtech pro obecné účely a prémiové sdílené složky Azure se musí vytvářet jenom v účtech úložiště Úložiště souborů. Účty úložiště pro účely *obecného* použití (GPv1 a GPv2) jsou pouze pro objekty BLOB stránky premium. 

* <a id="give-us-feedback"></a>
  **Opravdu chci vidět konkrétní funkci přidanou do souborů Azure. Můžeš to přidat?**  
    Tým Azure Files má zájem o všechny názory, které máte o naší službě. Hlasujte o žádostech o funkce na [webu Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)! Těšíme se, že vás potěšíme mnoha novými funkcemi.

  **Podporuje soubory Azure zamykání souborů?**  
    Ano, Soubory Azure plně podporuje uzamčení souborů ve stylu SMB/Windows, [viz podrobnosti](https://docs.microsoft.com/rest/api/storageservices/managing-file-locks). 
    
## <a name="azure-file-sync"></a>Synchronizace souborů Azure

* <a id="afs-region-availability"></a>
  **Jaké oblasti jsou podporované pro Azure File Sync?**  
    Seznam dostupných oblastí najdete v části [Dostupnost oblasti](storage-sync-files-planning.md#azure-file-sync-region-availability) v průvodci plánováním synchronizace souborů Azure. Budeme průběžně přidávat podporu pro další regiony, včetně neveřejných regionů.

* <a id="cross-domain-sync"></a>
  **Mohu mít servery spojené s doménou a servery, které nejsou připojeny k doméně, ve stejné skupině synchronizace?**  
    Ano. Skupina synchronizace může obsahovat koncové body serveru, které mají různá členství ve službě Active Directory, i když nejsou připojeny k doméně. Přestože tato konfigurace technicky funguje, nedoporučujeme to jako typickou konfiguraci, protože seznamy řízení přístupu (ACLs), které jsou definovány pro soubory a složky na jednom serveru, nemusí být vynuceny jinými servery ve skupině synchronizace. Nejlepších výsledků dosáhnete, doporučujeme synchronizaci mezi servery, které jsou ve stejné doménové struktuře služby Active Directory, mezi servery, které jsou v různých doménových strukturách služby Active Directory, ale které vytvořily vztahy důvěryhodnosti, nebo mezi servery, které nejsou v doméně. Doporučujeme, abyste se vyhýbali použití kombinace těchto konfigurací.

* <a id="afs-change-detection"></a>
  **Vytvořil jsem soubor přímo ve sdílené složce Azure pomocí SMB nebo na portálu. Jak dlouho trvá, než se soubor synchronizuje se servery ve skupině synchronizace?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**Pokud se stejný soubor změní na dvou serverech přibližně ve stejnou dobu, co se stane?**  
    Azure File Sync používá jednoduchou strategii řešení konfliktů: uchováme obě změny souborů, které se mění na dvou serverech současně. Poslední písemná změna zachová původní název souboru. Starší soubor má "zdrojový" počítač a číslo konfliktu připojené k názvu. Vyplývá to z této taxonomie: 
   
    \<Název_souboruBez\>-\<rozšíření Název_počítače\>\[-#\]. \<ext\>  

    Například první konflikt CompanyReport.docx by se stal CompanyReport-CentralServer.docx, pokud CentralServer je místo, kde došlo k staršímu zápisu. Druhý konflikt by se jmenoval CompanyReport-CentralServer-1.docx. Azure File Sync podporuje 100 souborů konfliktů na soubor. Po dosažení maximálního počtu souborů konfliktů se soubor nepodaří synchronizovat, dokud nebude počet souborů konfliktů menší než 100.

* <a id="afs-storage-redundancy"></a>
  **Je geograficky redundantní úložiště podporované pro Azure File Sync?**  
    Ano, Soubory Azure podporuje místně redundantní úložiště (LRS) i geograficky redundantní úložiště (GRS). Pokud zahájíte převzetí služeb při selhání mezi spárovanými oblastmi z účtu nakonfigurovaného pro GRS, společnost Microsoft doporučuje považovat novou oblast pouze jako zálohu dat. Azure File Sync se automaticky nezačne synchronizovat s novou primární oblastí. 

* <a id="sizeondisk-versus-size"></a>
  **Proč vlastnost *Velikost na disku* pro soubor neodpovídá vlastnosti *Size* po použití Azure File Sync?**  
  Viz [Principy vrstvení cloudu](storage-sync-cloud-tiering.md#sizeondisk-versus-size).

* <a id="is-my-file-tiered"></a>
  **Jak poznám, jestli byl soubor vrstvený?**  
  Viz [Principy vrstvení cloudu](storage-sync-cloud-tiering.md#is-my-file-tiered).

* <a id="afs-recall-file"></a>**Soubor, který chci použít, byl vrstvený. Jak lze odvolat soubor na disk, aby jej použít místně?**  
  Viz [Principy vrstvení cloudu](storage-sync-cloud-tiering.md#afs-recall-file).

* <a id="afs-force-tiering"></a>
  **Jak vynutím vrstvení souboru nebo adresáře?**  
  Viz [Principy vrstvení cloudu](storage-sync-cloud-tiering.md#afs-force-tiering).

* <a id="afs-effective-vfs"></a>
  **Jak je *volné místo svazku* interpretováno, když mám na svazku více koncových bodů serveru?**  
  Viz [Principy vrstvení cloudu](storage-sync-cloud-tiering.md#afs-effective-vfs).

* <a id="afs-files-excluded"></a>
  **Které soubory nebo složky jsou službou Azure File Sync automaticky vyloučeny?**  
  Viz [Soubory přeskočeny](storage-sync-files-planning.md#files-skipped).

* <a id="afs-os-support"></a>
  **Můžu používat Azure File Sync se systémem Windows Server 2008 R2, Linuxem nebo se zařízením úložiště připojeném k síti (NAS)?**  
    Azure File Sync v současné době podporuje jenom Windows Server 2019, Windows Server 2016 a Windows Server 2012 R2. V tuto chvíli nemáme žádné jiné plány, které můžeme sdílet, ale jsme otevřeni podpoře dalších platforem na základě poptávky zákazníků. Dejte nám vědět na [Azure Files UserVoice,](https://feedback.azure.com/forums/217298-storage/category/180670-files) jaké platformy byste chtěli, abychom podporovali.

* <a id="afs-tiered-files-out-of-endpoint"></a>
  **Proč existují vrstvené soubory mimo obor názvů koncového bodu serveru?**  
    Před Agent Azure File Sync verze 3 Azure File Sync zablokoval přesunutí vrstvené soubory mimo koncový bod serveru, ale na stejném svazku jako koncový bod serveru. Operace kopírování, přesuny souborů bez vrstvených a přesuny vrstvených na jiné svazky nebyly ovlivněny. Důvodem pro toto chování byl implicitní předpoklad, že Průzkumník souborů a další rozhraní API systému Windows mají, že operace přesunutí na stejném svazku jsou (téměř) okamžité přejmenování operací. To znamená, že přesunutí způsobí, že Průzkumník souborů nebo jiné metody přesunutí (například příkazový řádek nebo PowerShell) budou vypadat nereagují, zatímco Azure File Sync vyvolá data z cloudu. Počínaje [agentem Azure File Sync verze 3.0.12.0](storage-files-release-notes.md#supported-versions)vám Azure File Sync umožní přesunout vrstvený soubor mimo koncový bod serveru. Vyhneme se dříve uvedeným negativním účinkům tím, že povolíme, aby vrstvený soubor existoval jako vrstvený soubor mimo koncový bod serveru, a potom soubor na pozadí svoláme. To znamená, že přesuny na stejném svazku jsou okamžité a my děláme veškerou práci, abychom soubor po dokončení přesunu stáhli na disk. 

* <a id="afs-do-not-delete-server-endpoint"></a>
  **Mám problém se synchronizací souborů Azure na serveru (synchronizace, vrstvení v cloudu atd.). Mám odebrat a znovu vytvořit koncový bod serveru?**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
  **Můžu přesunout službu synchronizace úložiště nebo účet úložiště do jiné skupiny prostředků nebo předplatného?**  
   Ano, účet synchronizace úložiště a účet úložiště lze přesunout do jiné skupiny prostředků nebo předplatného v rámci existujícího klienta Azure AD. Pokud se účet úložiště přesune, musíte službě hybrid File Sync Service udělit přístup k účtu úložiště (viz [Zajištění azure file sync má přístup k účtu úložiště).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > Azure File Sync nepodporuje přesunutí předplatného do jiného klienta Azure AD.
    
* <a id="afs-ntfs-acls"></a>
  **Zachová Azure File Sync seznamy ACL na úrovni adresářů nebo souborů spolu s daty uloženými v souborech Azure?**

    února 2020 budou nové a existující seznamy ACL vrstvené podle synchronizace souborů Azure trvalé ve formátu NTFS a změny ACL provedené přímo ve sdílené složce Azure se synchronizují se všemi servery ve skupině synchronizace. Všechny změny v seznamech ACL provedené v souborech Azure se synchronizují prostřednictvím synchronizace souborů Azure. Při kopírování dat do souborů Azure se ujistěte, že používáte SMB pro přístup ke sdílené složce a zachování vašich seznamů ACL. Existující nástroje založené na REST, jako je například AzCopy nebo Průzkumník úložiště, nepřetrvávají akly.

    Pokud jste povolili Azure Backup ve sdílených slučovaných souborech synchronizace souborů, mohou se akly souborů nadále obnovovat jako součást pracovního postupu obnovení zálohování. To to funguje buď pro celou sdílenou složku nebo jednotlivé soubory / adresáře.

    Pokud používáte snímky jako součást řešení zálohování s vlastním správou pro sdílené složky spravované synchronizací souborů, nemusí být vaše počet alokací správně obnoven do seznamu ACL ntfs, pokud byly snímky pořízeny před 24. Pokud k tomu dojde, zvažte kontaktování podpory Azure.
    
## <a name="security-authentication-and-access-control"></a>Zabezpečení, ověřování a řízení přístupu
* <a id="ad-support"></a>
**Jsou soubory Azure podporované ověřováním a řízením přístupu na základě identity?**  
    
    Ano, Soubory Azure podporují ověřování a řízení přístupu založené na identitách. Můžete zvolit jeden ze dvou způsobů použití řízení přístupu založeného na identitě: Active Directory (AD) (preview) nebo Azure Active Directory Domain Services (Azure AD DS) (GA). Služba AD podporuje ověřování pomocí počítačů spojených s doménou Služby AD, ať už místně nebo v Azure, pro přístup ke sdíleným položkám Azure přes SMB. Ověřování Azure AD DS přes SMB pro soubory Azure umožňuje virtuálním počítačem Windows s připojením k doméně Azure AD DS přístup ke sdíleným složkám, adresářům a souborům pomocí přihlašovacích údajů Azure AD. Další podrobnosti najdete [v tématu Přehled podpory ověřování na základě identity souborů Azure pro přístup SMB](storage-files-active-directory-overview.md). 

    Soubory Azure nabízí dva další způsoby správy řízení přístupu:

    - Sdílené přístupové podpisy (SAS) můžete použít ke generování tokenů, které mají určitá oprávnění a které jsou platné pro zadaný časový interval. Můžete například vygenerovat token s přístupem jen pro čtení k určitému souboru, který má 10minutovou vypršení platnosti. Každý, kdo má token, zatímco token je platný má přístup jen pro čtení k tomuto souboru pro těchto 10 minut. Klíče podpisu sdíleného přístupu jsou podporovány pouze prostřednictvím rozhraní REST API nebo v klientských knihovnách. Sdílenou složku Azure je nutné připojit přes SMB pomocí klíčů účtu úložiště.

    - Azure File Sync zachová a replikuje všechny volitelné seznamy ACL nebo seznamy DACL (ať už na základě služby Active Directory nebo místní) do všech koncových bodů serveru, se kterými se synchronizuje. Vzhledem k tomu, že systém Windows Server se již může ověřit pomocí služby Active Directory, je synchronizace souborů Azure účinnou možností zastavení, dokud nedorazí úplná podpora ověřování na základě služby Active Directory a podpory seznamu ACL.
    
    Můžete odkazovat na [autorizaci přístupu k Úložišti Azure](https://docs.microsoft.com/azure/storage/common/storage-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) pro komplexní reprezentaci všech protokolů podporovaných ve službách Azure Storage. 

* <a id="ad-support-devices"></a>
**Podporuje ověřování Azure Files Azure Active Directory Domain Services (Azure AD DS) přístup SMB pomocí přihlašovacích údajů Azure AD ze zařízení spojených nebo registrovaných ve službě Azure AD?**

    Ne, tento scénář není podporován.

* <a id="ad-support-rest-apis"></a>
**Existují rozhraní API REST pro podporu seznamů ACL pro získání/set/kopírování adresáře/souboru NTFS?**

    Ano, podporujeme rozhraní REST API, která při použití rozhraní REST API [2019-07-07](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#version-2019-07-07) (nebo novější) REST API získají, nastaví nebo zkopírují seznamy AcL ntfs pro adresáře nebo soubory.

* <a id="ad-vm-subscription"></a>
**Můžu přistupovat k souborům Azure pomocí přihlašovacích údajů Azure AD z virtuálního počítače v rámci jiného předplatného?**

    Pokud je předplatné, pod kterým se nasadí sdílená složka, přidružené ke stejnému tenantovi Azure AD jako nasazení služby Azure AD Domain Services, ke kterému je virtuální počítač připojen k virtuálnímu počítači, pak můžete přistupovat k souborům Azure pomocí stejných přihlašovacích údajů Azure AD. Omezení je uložena není na předplatné, ale na přidružené azure ad tenanta.
    
* <a id="ad-support-subscription"></a>
**Můžu povolit azure files Azure AD DS nebo ověřování služby AD s klientem Azure AD, který se liší od primárního klienta, ke kterému je sdílená složka přidružena?**

    Ne, Azure Files podporuje jenom azure ad ds nebo ad integrace s tenantem Azure AD, který se nachází ve stejném předplatném jako sdílená složka. K tenantovi Azure AD lze přidružit jenom jedno předplatné. Toto omezení platí pro metody ověřování Azure AD DS i AD. Při použití služby AD pro ověřování musí být pověření služby AD synchronizováno se službou Azure AD, ke které je účet úložiště přidružen.

* <a id="ad-linux-vms"></a>
**Podporuje Azure Files Azure AD DS nebo Ověřování služby AD virtuální počítače s Linuxem?**

    Ne, ověřování z virtuálních počítačů s Linuxem není podporované.

* <a id="ad-aad-smb-afs"></a>
**Můžu využít azure files azure ad ds ověřování nebo ověřování ve službě Active Directory (AD) ve sdílených složkách spravovaných službou Azure File Sync?**

    Ano, můžete povolit ověřování Azure AD DS nebo AD ve sdílené složce spravované synchronizací souborů Azure. Změny seznamů ACL adresáře nebo souboru NTFS na místních souborových serverech budou vrstvené na soubory Azure a naopak.

* <a id="ad-aad-smb-files"></a>
**Jak můžu zkontrolovat, jestli jsem povolil ad ověřování v účtu úložiště a v informacích o doméně služby AD?**

    Můžete odkazovat na pokyny [zde](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-enable#1-enable-ad-authentication-for-your-account) uvedené ověřit, pokud Azure Files AD Ověřování je povolena na vašem účtu úložiště a načíst informace o doméně služby AD.

* <a id="encryption-at-rest"></a>
**Jak zajistím, aby byla moje sdílená složka Azure zašifrovaná v klidovém stavu?**  

    Ano. Další informace najdete [v tématu Azure Storage Service Encryption](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>
**Jak mohu poskytnout přístup k určitému souboru pomocí webového prohlížeče?**  

    Sdílené přístupové podpisy můžete použít ke generování tokenů, které mají určitá oprávnění a které jsou platné pro zadaný časový interval. Můžete například vygenerovat token, který poskytuje přístup jen pro čtení k určitému souboru po nastavené časové období. Každý, kdo má adresu URL, může přistupovat k souboru přímo z libovolného webového prohlížeče, když je token platný. Můžete snadno vygenerovat klíč podpisu sdíleného přístupu z průzkumníka úložiště, jako je rozhraní.

* <a id="file-level-permissions"></a>
**Je možné zadat oprávnění jen pro čtení nebo jen pro zápis ve složkách ve sdílené složce?**  

    Pokud připojíte sdílenou složku pomocí protokolu SMB, nemáte kontrolu nad oprávněními na úrovni složky. Pokud však vytvoříte sdílený přístupový podpis pomocí rozhraní REST API nebo klientských knihoven, můžete zadat oprávnění pouze pro čtení nebo jen pro zápis ve složkách ve sdílené složce.

* <a id="ip-restrictions"></a>
**Můžu implementovat omezení IP adres pro sdílenou složku Azure?**  

    Ano. Přístup ke sdílené složce Azure může být omezen na úrovni účtu úložiště. Další informace najdete [v tématu Konfigurace bran firewall úložišť Azure a virtuálních sítí](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>
**Jaké zásady dodržování předpisů pro data podporují soubory Azure?**  

   Azure Files běží nad stejnou architekturou úložiště, která se používá v jiných službách úložiště ve službě Azure Storage. Azure Files používá stejné zásady dodržování předpisů dat, které se používají v jiných službách úložiště Azure. Další informace o dodržování předpisů azure storage dat, můžete odkazovat na [azure storage dodržování předpisů nabídky](https://docs.microsoft.com/azure/storage/common/storage-compliance-offerings)a přejděte na Microsoft Trust [Center](https://microsoft.com/trustcenter/default.aspx).
   
### <a name="ad-authentication"></a>Ověřování ve službě AD
* <a id=""></a>
**Podporuje ověřování Azure AD Azure virtuální počítače s Linuxem?**

    Ne, ověřování z virtuálních počítačů s Linuxem není podporované.

* <a id="ad-multiple-forest"></a>
**Podporuje ověřování Azure Files AD integraci s prostředím služby AD pomocí více doménových struktur?**    

    Ověřování Azure Files AD se integruje jenom s doménovou doménou služby AD, na kterou je účet úložiště registrovaný. Pro podporu ověřování z jiné doménové struktury služby AD musí být vaše prostředí správně nakonfigurováno. Způsob registrace souborů Azure do služby domény služby AD je většinou stejný jako běžný souborový server, kde ve službě AD vytvoří identitu (přihlašovací účet počítače nebo služby) pro ověřování. Jediným rozdílem je, že registrovaný hlavní název služby účtu úložiště končí "file.core.windows.net", který neodpovídá příponě domény. Obraťte se na správce domény a zjistěte, zda je nutná nějaká aktualizace zásad směrování DNS, která by umožnila ověřování více doménových domén z důvodu jiné přípony domény.

* <a id=""></a>
**Jaké oblasti jsou dostupné pro ověřování azure soubory ad (preview)?**

    Podrobnosti najdete [v místní dostupnosti služby AD.](storage-files-identity-auth-active-directory-enable.md#regional-availability)

* <a id="ad-aad-smb-afs"></a>
**Můžu využít ověřování azure souborů ad (AD) ve sdílených složkách spravovaných službou Azure File Sync?**

    Ano, ověřování ad ve sdílené složce spravované synchronizací souborů Azure můžete povolit. Změny seznamů ACL adresáře nebo souboru NTFS na místních souborových serverech budou vrstvené na soubory Azure a naopak.

* <a id="ad-aad-smb-files"></a>
**Jak můžu zkontrolovat, jestli jsem povolil ad ověřování v účtu úložiště a v informacích o doméně služby AD?**

    Můžete odkazovat na pokyny [zde](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-enable#enable-ad-authentication-for-your-account) uvedené ověřit, pokud Azure Files AD Ověřování je povolena na vašem účtu úložiště a načíst informace o doméně služby AD.

* <a id="ad-aad-smb-files"></a>
**Je nějaký rozdíl ve vytvoření účtu počítače nebo přihlašovacího účtu služby, který představuje můj účet úložiště ve službě AD?**

    Vytvoření [účtu počítače](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (výchozí) nebo přihlašovací [účet služby](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) nemá žádný rozdíl v tom, jak by ověřování fungovalo se soubory Azure. Můžete si sami vybrat, jak reprezentovat účet úložiště jako identitu v prostředí služby AD. Výchozí domainaccounttype nastavená v rutině Join-AzStorageAccountForAuth je účet počítače. Stáří vypršení platnosti hesla nakonfigurované v prostředí služby AD se však může lišit pro přihlašovací účet počítače nebo služby a je třeba to vzít v úvahu při [aktualizaci hesla identity účtu úložiště ve službě AD](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-enable#5-update-ad-account-password).

## <a name="on-premises-access"></a>Místní přístup

* <a id="port-445-blocked"></a>
**Můj ISP nebo IT blokuje Port 445, který se lhaní Azure Files připojit. Co mám dělat?**

    Můžete se [dozvědět o různých způsobech řešení blokovanéport 445 zde](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems#cause-1-port-445-is-blocked). Soubory Azure umožňuje jenom připojení pomocí SMB 3.0 (s podporou šifrování) z mimo oblast nebo datové centrum. Protokol SMB 3.0 zavedl mnoho bezpečnostních funkcí, včetně šifrování kanálů, které je velmi bezpečné pro použití přes internet. Nicméně jeho možné, že port 445 byl zablokován z historických důvodů zranitelnosti nalezených v nižších verzích SMB. V ideálním případě by měl být port blokován pouze pro přenosy SMB 1.0 a SMB 1.0 by měl být vypnut ý všechny klienty.

* <a id="expressroute-not-required"></a>
**Musím použít Azure ExpressRoute pro připojení k souborům Azure nebo k místnímu použití Azure File Sync?**  

    Ne. ExpressRoute není nutné pro přístup ke sdílené složce Azure. Pokud intekujete sdílenou složku Azure přímo místně, vše, co je potřeba, je mít port 445 (TCP odchozí) otevřený pro přístup k internetu (to je port, který používá SMB ke komunikaci). Pokud používáte Azure File Sync, vše, co je potřeba, je port 443 (TCP odchozí) pro přístup HTTPS (není vyžadováno smb). Můžete však *použít* ExpressRoute s některou z těchto možností přístupu.

* <a id="mount-locally"></a>
**Jak můžu připojit sdílenou složku Azure v místním počítači?**  

    Sdílenou složku můžete připojit pomocí protokolu SMB, pokud je port 445 (odchozí TCP) otevřený a váš klient podporuje protokol SMB 3.0 (například pokud používáte Windows 10 nebo Windows Server 2016). Pokud je port 445 blokován zásadami vaší organizace nebo vaším isp, můžete použít Azure File Sync pro přístup ke sdílené složce Azure.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**Jak můžu zálohovat sdílenou složku Azure?**  
    [Snímky periodické sdílené složky](storage-snapshots-files.md) můžete použít k ochraně proti náhodnému odstranění. Můžete také použít AzCopy, Robocopy nebo nástroj pro zálohování jiného výrobce, který může zálohovat připojenou sdílenou složku. Azure Backup nabízí zálohování souborů Azure. Další informace o [zálohování sdílených složek Azure pomocí Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-files).

## <a name="share-snapshots"></a>Sdílení snímků

### <a name="share-snapshots-general"></a>Sdílení snímků: Obecné
* <a id="what-are-snaphots"></a>
**Co jsou snímky sdílené složky?**  
    Snímky sdílené složky Azure můžete použít k vytvoření verze sdílených složek jen pro čtení. Soubory Azure můžete také použít ke zkopírování starší verze obsahu zpět do stejné sdílené složky, do alternativního umístění v Azure nebo místně pro další úpravy. Další informace o snímcích sdílení najdete v tématu [Přehled snímků sdílení](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>
**Kde jsou uloženy snímky sdílené složky?**  
    Snímky sdílené složky jsou uloženy ve stejném účtu úložiště jako sdílená složka.

* <a id="snapshot-consistency"></a>
**Jsou snímky sdílené složky konzistentní s aplikací?**  
    Ne, snímky sdílené složky nejsou konzistentní s aplikací. Uživatel musí vyprázdnění zápisy z aplikace do sdílené složky před přijetím snímek sdílené složky.

* <a id="snapshot-limits"></a>
**Existují omezení počtu snímků sdílené složky, které mohu použít?**  
    Ano. Soubory Azure můžete zachovat maximálně 200 snímků sdílené složky. Snímky sdílené složky se do kvóty sdílené složky nezapočítávají, takže neexistuje žádný limit na sdílenou složku na celkovém prostoru, který používají všechny snímky sdílené složky. Limity účtu úložiště stále platí. Po 200 snímků sdílené složky je nutné odstranit starší snímky a vytvořit tak nové snímky sdílené složky.

* <a id="snapshot-cost"></a>
**Kolik stojí snímky sdílení?**  
    Standardní transakce a standardní náklady na úložiště se budou vztahovat na snímek. Snímky jsou přírůstkové povahy. Základní snímek je sdílená sdílená složky samotné. Všechny následující snímky jsou přírůstkové a bude ukládat pouze rozdíl z předchozí snímek. To znamená, že rozdílové změny, které se zobrazí ve vyúčtování bude minimální, pokud vaše pracovní vytížení konve je minimální. Viz [Stránka Ceny](https://azure.microsoft.com/pricing/details/storage/files/) pro informace o cenách standardních souborů Azure. Dnes je způsob, jak se podívat na velikost spotřebované snímek sdílené složky je porovnáním fakturované kapacity s využitou kapacitou. Pracujeme na nástrojech pro zlepšení podávání zpráv.

* <a id="ntfs-acls-snaphsots"></a>
**Jsou seznamy ACL systému souborů NTFS v adresářích a souborech trvalé ve snímcích sdílené složky?**  
    Seznamy ACL ntfs na adresáře a soubory jsou trvalé ve snímcích sdílené složky.

### <a name="create-share-snapshots"></a>Vytvoření snímků sdílené složky
* <a id="file-snaphsots"></a>
**Mohu vytvořit snímek sdílené složky jednotlivých souborů?**  
    Snímky sdílené složky jsou vytvářeny na úrovni sdílené složky. Můžete obnovit jednotlivé soubory ze snímku sdílené složky, ale nelze vytvořit snímky sdílené složky na úrovni souboru. Pokud jste však pořídili snímek sdílené složky na úrovni sdílené složky a chcete vypsat snímky sdílené složky, ve **kterých** se změnil určitý soubor, můžete tak učinit v části Předchozí verze ve sdílené složce připojené k systému Windows. 
    
    Pokud potřebujete funkci snímek souboru, dejte nám vědět na [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encrypted-snapshots"></a>
**Je možné vytvořit snímky sdílené složky šifrované sdílené složky?**  
    Můžete pořizovat snímek sdílené složky Azure, které mají šifrování v klidovém stavu povoleno. Soubory ze snímku sdílené složky můžete obnovit do šifrované sdílené složky. Pokud je sdílená sdílená složky zašifrovaná, je zašifrovaný také snímek sdílené složky.

* <a id="geo-redundant-snaphsots"></a>
**Jsou snímky sdílené složky geograficky redundantní?**  
    Snímky sdílené složky mají stejnou redundanci jako sdílená složka Azure, pro kterou byly pořízeny. Pokud jste pro svůj účet vybrali geograficky redundantní úložiště, snímek sdílené složky se také redundantně uloží ve spárované oblasti.

### <a name="manage-share-snapshots"></a>Správa snímků sdílené složky
* <a id="browse-snapshots-linux"></a>
**Můžu procházet snímky sdílené složky z Linuxu?**  
    Azure CLI můžete použít k vytváření, seznam, procházení a obnovení sdílení snímků v Linuxu.

* <a id="copy-snapshots-to-other-storage-account"></a>
**Můžu snímky sdílené složky zkopírovat do jiného účtu úložiště?**  
    Soubory ze snímků sdílené složky můžete kopírovat do jiného umístění, ale nemůžete zkopírovat snímky sdílené složky sami.

### <a name="restore-data-from-share-snapshots"></a>Obnovení dat ze snímků sdílené složky
* <a id="promote-share-snapshot"></a>
**Můžu povýšit snímek sdílené složky na základní sdílenou složku?**  
    Data ze snímku sdílené složky můžete zkopírovat do libovolného jiného cíle. Snímek sdílené složky nelze povýšit na základní sdílenou složku.

* <a id="restore-snapshotted-file-to-other-share"></a>
**Můžu obnovit data ze snímku sdílené složky do jiného účtu úložiště?**  
    Ano. Soubory ze snímku sdílené složky lze zkopírovat do původního umístění nebo do alternativního umístění, které zahrnuje buď stejný účet úložiště, nebo jiný účet úložiště, ve stejné oblasti nebo v různých oblastech. Můžete také kopírovat soubory do místního umístění nebo do jiného cloudu.    
  
### <a name="clean-up-share-snapshots"></a>Vyčistit snímky sdílené složky
* <a id="delete-share-keep-snapshots"></a>
**Můžu sdílet sdílenou složku, ale neodstranit snímky sdílené složky?**  
    Pokud máte ve sdílené složce aktivní snímky sdílené složky, nemůžete sdílenou složku odstranit. Pomocí rozhraní API můžete odstranit snímky sdílené složky spolu se sdílenou spojí. Můžete také odstranit snímky sdílené složky a sdílené složky na webu Azure Portal.

* <a id="delete-share-with-snapshots"></a>
**Co se stane s mými snímky sdílené složky, když smažu svůj účet úložiště?**  
    Pokud odstraníte účet úložiště, odstraní se také snímky sdílené složky.

## <a name="billing-and-pricing"></a>Fakturace a ceny
* <a id="vm-file-share-network-traffic"></a>
**Počítá se síťový provoz mezi virtuálním počítačem Azure a sdílenou složkou Azure jako externí šířka pásma, která se účtuje z předplatného?**  
    Pokud sdílené složky a virtuální počítač jsou ve stejné oblasti Azure, neexistuje žádný další poplatek za provoz mezi sdílené složky a virtuálním počítačem. Pokud sdílené složky a virtuální hod jsou v různých oblastech, provoz mezi nimi se účtuje jako externí šířku pásma.

* <a id="share-snapshot-price"></a>
**Kolik stojí snímky sdílení?**  
     Během náhledu se neúčtuje žádný poplatek za kapacitu snímku sdílené složky. Platí standardní odchozí úložiště a transakční náklady. Po obecné dostupnosti se předplatné bude účtovat za kapacitu a transakce na snímcích sdílené složky.
     
     Sdílené snímky jsou přírůstkové povahy. Snímek základní sdílené složky je samotná sdílená složky. Všechny následné snímky sdílené složky jsou přírůstkové a ukládají pouze rozdíl od předchozího snímku sdílené složky. Bude se vám účtovat pouze změněný obsah. Pokud máte sdílenou složku se 100 GiB dat, ale od posledního snímku sdílené složky se změnilo pouze 5 GiB, snímek sdílené složky spotřebovává pouze 5 dalších GiB a bude se vám účtovat 105 GiB. Další informace o transakčních a standardních výstupních poplatcích naleznete na [stránce Ceny](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="scale-and-performance"></a>Škálování a výkon
* <a id="files-scale-limits"></a>
**Jaké jsou omezení škálování souborů Azure?**  
    Informace o škálovatelnosti a výkonnostních cílech pro soubory Azure najdete v [tématu Škálovatelnost souborů Azure a cíle výkonu](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>
**Jaké velikosti jsou dostupné pro sdílené složky Azure?**  
    Velikost sdílené složky Azure (premium a standard) můžete škálovat až na 100 TiB. Podívejte se [na část Onboarding to larger file shares (standard tier)](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) v průvodci plánováním pro pokyny k připojení k větším sdíleným souborům pro standardní úroveň.

* <a id="lfs-performance-impact"></a>
**Má rozšíření kvóty sdílení souborů vliv na moje úlohy nebo Azure File Sync?**
    
    Ne. Rozšíření kvóty nebude mít vliv na vaše úlohy nebo Azure File Sync.

* <a id="open-handles-quota"></a>
**Kolik klientů může přistupovat ke stejnému souboru současně?**   
    V jednom souboru je kvóta 2 000 otevřených popisovačů. Pokud máte 2 000 otevřených popisovačů, zobrazí se chybová zpráva, která říká, že je dosaženo kvóty.

* <a id="zip-slow-performance"></a>
**Můj výkon je pomalý, když rozbalím soubory v Azure Files. Co mám dělat?**  
    Chcete-li přenést velký počet souborů do souborů Azure, doporučujeme použít AzCopy (pro Windows; ve verzi preview pro Linux a UNIX) nebo Azure PowerShell. Tyto nástroje byly optimalizovány pro síťový přenos.

* <a id="slow-perf-windows-81-2012r2"></a>
**Proč je můj výkon pomalý po připojení sdílené složky Azure na Windows Server 2012 R2 nebo Windows 8.1?**  
    Při připojovací sdílené složce Azure na Windows Server 2012 R2 a Windows 8.1 je známý problém. Problém byl opraven v kumulativní aktualizaci z dubna 2014 pro Windows 8.1 a Windows Server 2012 R2. Pro optimální výkon, ujistěte se, že všechny instance Windows Server 2012 R2 a Windows 8.1 mít tuto opravu použít. (Opravy systému Windows byste měli vždy dostávat prostřednictvím služby Windows Update.) Další informace naleznete v souvisejícím článku znalostní báze Microsoft Knowledge Base [Pomalý výkon při přístupu k souborům Azure z Windows 8.1 nebo Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Funkce a interoperabilita s dalšími službami
* <a id="cluster-witness"></a>
**Můžu použít sdílenou složku Azure jako důkaz *sdílení souborů* pro cluster s podporou převzetí služeb při selhání windows serveru?**  
    V současné době tato konfigurace není podporována pro sdílenou složku Azure. Další informace o tom, jak to nastavit pro úložiště objektů blob Azure, najdete [v tématu nasazení cloudu Důkaz pro cluster s podporou převzetí služeb při selhání](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>
**Můžu připojit sdílenou složku Azure na instanci Azure Container?**  
    Ano, sdílené složky Azure jsou dobrou volbou, pokud chcete zachovat informace po dobu platnosti instance kontejneru. Další informace najdete [v tématu připojení sdílené složky Azure s instancemi Azure Container](../../container-instances/container-instances-mounting-azure-files-volume.md).

* <a id="rest-rename"></a>
**Existuje operace přejmenování v rozhraní REST API?**  
    V tuto chvíli to není možné.

* <a id="nested-shares"></a>
**Mohu nastavit vnořené sdílené složky? Jinými slovy, podíl v rámci podílu?**  
    Ne. Sdílená složka *je* virtuální ovladač, který můžete připojit, takže vnořené sdílené složky nejsou podporovány.

* <a id="ibm-mq"></a>
**Jak se používají soubory Azure s IBM MQ?**  
    IBM vydala dokument, který pomáhá zákazníkům IBM MQ konfigurovat soubory Azure pomocí služby IBM. Další informace najdete [v tématu Jak nastavit správce front ibm MQ s více instancemi pomocí služby Microsoft Azure Files](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).

## <a name="see-also"></a>Viz také
* [Poradce při potížích se soubory Azure ve Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Poradce při potížích se soubory Azure v Linuxu](storage-troubleshoot-linux-file-connection-problems.md)
* [Řešení problémů se Synchronizací souborů Azure](storage-sync-files-troubleshoot.md)
