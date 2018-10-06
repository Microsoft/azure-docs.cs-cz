---
title: Nejčastější dotazy (FAQ) pro Azure Files | Dokumentace Microsoftu
description: Najděte odpovědi na nejčastější dotazy o službě soubory Azure.
services: storage
author: RenaShahMSFT
ms.service: storage
ms.date: 10/04/2018
ms.author: renash
ms.component: files
ms.openlocfilehash: 725aa5ff0d5ac0d6760656fc596fe44ca8661d1f
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816119"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>O službě soubory Azure – nejčastější dotazy (FAQ)
[Služba soubory Azure](storage-files-introduction.md) nabízí plně spravované sdílené složky v cloudu, které jsou přístupné prostřednictvím standardních průmyslových [zprávy bloku SMB (Server) protokol](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx). Sdílené složky Azure je možné připojit současně v cloudových i místních nasazení systémů Windows, Linux a macOS. Také můžete ukládat do mezipaměti sdílených složek Azure v počítačích s Windows serverem pomocí Azure File Sync pro rychlý přístup blízko, ve kterém jsou využívány.

Tento článek obsahuje odpovědi na běžné otázky o Azure Files funkce a funkce, včetně použití služby Azure File Sync s Azure Files. Pokud nevidíte odpověď na svoji otázku, kontaktujte nás prostřednictvím následujících kanálů (v neustále rostoucích pořadí):

1. V sekci komentáře v tomto článku.
2. [Fórum služby Azure Storage](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
3. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. podporu Microsoftu. Chcete-li vytvořit novou žádost o podporu, na webu Azure Portal, na **pomáhají** kartu, vyberte možnost **Nápověda a podpora** tlačítko a pak vyberte **nová žádost o podporu**.

## <a name="general"></a>Obecné
* <a id="why-files-useful"></a>
**Jak se soubory Azure užitečná?**  
   Soubory Azure můžete použít k vytvoření sdílené složky v cloudu, aniž by musel pracovat zodpovědného za správu nároky na fyzickém serveru, zařízení nebo zařízení. Monotónní práce uděláme za vás, včetně použití aktualizací operačního systému a nahrazení chybných disků. Další informace o scénářích, které vám pomáhají soubory Azure najdete v tématu [proč soubory Azure je užitečné](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>
**Jaké jsou různé způsoby, jak přistupovat k souborům ve službě soubory Azure?**  
    Sdílené složky na místním počítači můžete připojit pomocí protokolu SMB 3.0, nebo můžete použít nástroje, jako je [Průzkumníka služby Storage](http://storageexplorer.com/) při přístupu k souborům ve sdílené složce. Z vaší aplikace můžete použít klientských knihoven pro úložiště, rozhraní REST API, Powershellu nebo rozhraní příkazového řádku Azure pro přístup k souborům ve sdílené složce Azure file.

* <a id="what-is-afs"></a>
**Co je Azure File Sync?**  
    Azure File Sync můžete centralizovat sdílené složky organizace ve službě soubory Azure, při zachování flexibility, výkonu a kompatibility s místními souborového serveru. Azure File Sync transformuje počítačů s Windows serverem na rychlou mezipaměť sdílené složky Azure. Můžete použít jakýkoli protokol dostupný ve Windows serveru pro přístup k datům místně, včetně SMB, systém souborů NFS a soubor přenos protokolu služby (FTPS). Můžete mít libovolný počet mezipamětí po celém světě potřebujete.

* <a id="files-versus-blobs"></a>
**Proč používat sdílené složky Azure a Azure Blob storage pro moje data?**  
    Služba soubory Azure a Azure Blob storage obě úložiště poskytují způsoby, jak ukládat velké objemy dat v cloudu, ale jsou užitečné pro účely trochu jinak. 
    
    Azure Blob storage je užitečné pro masivní škálování, nativních cloudových aplikací, které potřebují ukládat Nestrukturovaná data. Úložiště objektů Blob v Azure k zajištění maximálního výkonu a škálování, je jednodušší abstrakce úložiště než systém souborů na hodnotu true. Úložiště objektů Blob v Azure můžete přistupovat pouze prostřednictvím knihoven klienta založené na protokolu REST (nebo přímo prostřednictvím protokolu založené na protokolu REST).

    Služba soubory Azure je konkrétně systém souborů. Služba soubory Azure má všechny souboru přehledů, které znáte a máte rádi ze mnohaleté práce s místním operačních systémů. Jako úložiště objektů Blob v Azure Azure Files nabízí rozhraní REST a ostatní klientské knihovny založené na REST. Na rozdíl od úložiště objektů Blob v Azure soubory Azure nabízí SMB přístup ke sdílené složky Azure. Pomocí protokolu SMB, můžete připojit sdílené složky Azure přímo na Windows, Linux nebo macOS, ať už místní nebo v cloudu virtuálních počítačů, bez psaní kódu nebo všechny speciální ovladače se připojuje k systému souborů. Také můžete ukládat do mezipaměti sdílených složek Azure na místní souborové servery s využitím Azure File Sync pro rychlý přístup blízko, ve kterém jsou využívány. 
   
    Podrobnější popis o rozdílech mezi Azure Files a úložiště objektů Blob v Azure najdete v tématu [rozhodování, jestli použít úložiště objektů Blob v Azure, soubory Azure nebo Azure Disks](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Další informace o službě Azure Blob storage najdete v tématu [seznámení se službou Blob storage](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**Proč používat sdílené složky Azure místo Azure Disks?**  
    Disk v Azure Disks je jednoduše disk. K získání hodnoty z Azure Disks, musí připojení disku k virtuálnímu počítači, na kterém běží v Azure. Disky Azure je možné pro všechno, co by použít disk pro na místním serveru. Můžete ho použít jako disk s operačním systémem systémem, jako odkládací prostor pro operační systém nebo jako vyhrazeného úložiště pro aplikaci. K vytvoření souborového serveru v cloudu a použít ve stejných míst, kde můžete použít sdílené složky Azure je zajímavé použití pro disky Azure. Nasazení souborového serveru ve službě Azure Virtual Machines je vysoce výkonné způsob, jak získat úložiště souborů v Azure, když budete vyžadovat, aby možnostech nasazení, které nejsou v současnosti podporované ve službě soubory Azure (jako je například systém souborů NFS protokol podpory úrovně premium storage nebo). 

    Zprovoznění souborového serveru s Azure Disks jako back endové úložné obvykle je však mnohem nákladnější než použití sdílené složky Azure, z několika důvodů. Nejprve kromě placení za úložiště na disku, musíte platíte také za výdajů spuštění jedné nebo více virtuálních počítačů Azure. Za druhé také musíte spravovat virtuální počítače, které se používají ke spuštění souborový server. Například zodpovídáte za upgrady operačního systému. Nakonec pokud požadujete nakonec data uložená v mezipaměti v místním, je můžete nastavit a spravovat replikaci technologie, jako je například distribuované systému službu replikace souborů (DFSR), chcete-li docílit.

    Jedním z přístupů k získání to nejlepší z Azure Files a souborový server, která je hostována ve službě Azure Virtual Machines (navíc k použití jako back endové úložné disky Azure) je instalace Azure File Sync na souborovém serveru, který je hostovaný v cloudu virtuálního počítače. Pokud sdílená složka Azure je ve stejné oblasti jako váš souborový server, můžete povolit cloudu ovládání datových vrstev a nastavte objemu procento volného místa na maximum (99 %). Tím se zajistí minimální duplicitní data. Také můžete použít všechny aplikace, které chcete s souborovými servery, jako jsou aplikace, které vyžadují protokol NFS podporují.

    Informace o možnost pro vytvoření výkonné a vysoce dostupný souborový server v Azure najdete v tématu [clusterů hostů nasazení virtuálních počítačů IaaS v Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). Podrobnější popis rozdílů mezi soubory Azure a Azure Disks najdete v tématu [rozhodování, jestli použít úložiště objektů Blob v Azure, soubory Azure nebo Azure Disks](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Další informace o Azure Disks najdete v tématu [Přehled služby Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).

* <a id="get-started"></a>
**Jak můžu začít používat soubory Azure?**  
   Začínáme se službou soubory Azure je snadné. Nejprve je potřeba [vytvoření sdílené složky](storage-how-to-create-file-share.md)a pak připojte upřednostňované operačního systému: 

    * [Připojit k Windows](storage-how-to-use-files-windows.md)
    * [Připojení v Linuxu](storage-how-to-use-files-linux.md)
    * [Připojení v systému macOS](storage-how-to-use-files-mac.md)

   Podrobnější informace o nasazení sdílené složky Azure k nahrazení produkční sdílených složek ve vaší organizaci, najdete v článku [plánování nasazení služby soubory Azure](storage-files-planning.md).

* <a id="redundancy-options"></a>
**Jaké možnosti redundance úložiště podporují soubory Azure?**  
    Soubory Azure v současné době podporuje místně redundantní úložiště (LRS), zónově redundantní úložiště (ZRS) a geograficky redundantní úložiště (GRS). Plánujeme v budoucnu podporu jen pro čtení geograficky redundantní úložiště (RA-GRS), ale časové osy pro sdílení v tuto chvíli nemáme.

* <a id="tier-options"></a>
**Jaké úroveň úložiště jsou podporovány ve službě soubory Azure?**  
    Soubory Azure v současné době podporuje pouze na úrovni standard storage. Časové osy pro sdílení pro storage úrovně premium a studeného úložiště podporují v tuto chvíli nemáme. 
    
    > [!NOTE]
    > Nelze vytvořit sdílené složky Azure, z účtů úložiště pouze objektů blob nebo účty služby premium storage.

* <a id="give-us-feedback"></a>
**Chci zobrazit konkrétní funkci přidali do služby soubory Azure. Můžete ho přidat?**  
    Tým Azure Files zajímají vaše všechny názory, které máte s našimi službami. Prosím hlasovat pro požadavky na funkce na [UserVoice soubory Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files)! Právě se díváme vpřed na okouzlí vám s mnoha novými funkcemi.

## <a name="azure-file-sync"></a>Synchronizace souborů Azure

* <a id="afs-region-availability"></a>
**Jaké oblasti jsou podporovány pro Azure File Sync?**  
    Seznam dostupných oblastí najdete na [dostupnosti oblast](storage-sync-files-planning.md#region-availability) příručka k části Plánování Azure File Sync. Průběžně přidáme podporu dalších oblastech, včetně oblastí non-Public.

* <a id="cross-domain-sync"></a>
**Může mít servery připojené k doméně a není připojené k doméně ve stejné skupině synchronizace?**  
    Ano. Skupina synchronizace může obsahovat koncové body serveru, které mají různé ve skupinách služby Active Directory, i když nejsou připojené k doméně. I když technicky tato konfigurace funguje, nedoporučujeme to jako Typická konfigurace protože seznamy řízení přístupu (ACL), které jsou definovány pro soubory a složky na jednom serveru nemusí být možné vynutit jinými servery ve skupině synchronizace. Nejlepších výsledků doporučujeme, abyste synchronizaci mezi servery, které jsou ve stejné doménové struktuře služby Active Directory, mezi servery, které jsou v různých doménových strukturách služby Active Directory, ale které mají mezi sebou vytvořené vztahy důvěryhodnosti nebo mezi servery, které nejsou v doméně. Doporučujeme, abyste je velmi riskantní používat kombinaci těchto konfigurací.

* <a id="afs-change-detection"></a>
**Můžu vytvořit soubor přímo v mé sdílené složky Azure pomocí protokolu SMB nebo prostřednictvím portálu. Jak dlouho trvá synchronizaci pro servery ve skupině synchronizace souboru?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**Pokud je stejný soubor změněn na dva servery na přibližně ve stejnou dobu, co se stane?**  
    Azure File Sync používá strategii jednoduché řešení konfliktů: uchováváme obě změny na soubory, které jsou změněny na dva servery ve stejnou dobu. Nedávno písemné změnu zachová původní název souboru. Starší soubor má počítač "zdroj" a číslo konflikt připojeným k názvu. Splňuje tato taxonomie: 
   
    \<FileNameWithoutExtension\>-\<MachineName\>\[-#\].\< ext, přípona\>  

    Například první konflikt CompanyReport.docx by se mohla stát CompanyReport CentralServer.docx při CentralServer, kde došlo k starší zápisu. Druhý konflikt by se pojmenoval CompanyReport. CentralServer 1.docx.

* <a id="afs-storage-redundancy"></a>
**Je geograficky redundantní úložiště nepodporuje pro Azure File Sync?**  
    Ano, soubory Azure podporuje místně redundantní úložiště (LRS) a geograficky redundantní úložiště (GRS). Pokud dojde k selhání GRS mezi spárovaných oblastí, doporučujeme považovat nové oblasti jako záložní kopie pouze data. Azure File Sync nezačne automaticky synchronizuje s novou primární oblasti. 

* <a id="sizeondisk-versus-size"></a>
**Proč není *velikost na disku* vlastnost souboru shody *velikost* vlastnost po použití Azure File Sync?**  
 Zobrazit [vrstvení cloudu Principy](storage-sync-cloud-tiering.md#sizeondisk-versus-size).

* <a id="is-my-file-tiered"></a>
**Jak zjistit, jestli se vrstvený soubor?**  
 Zobrazit [vrstvení cloudu Principy](storage-sync-cloud-tiering.md#is-my-file-tiered).

* <a id="afs-recall-file"></a>**Soubor, který chcete použít zřízeny vrstvené. Jak můžete odvolat soubor na disk používat místně?**  
 Zobrazit [vrstvení cloudu Principy](storage-sync-cloud-tiering.md#afs-recall-file).


* <a id="afs-force-tiering"></a>
**Jak vynutit soubor nebo adresář vrstvený?**  
 Zobrazit [vrstvení cloudu Principy](storage-sync-cloud-tiering.md#afs-force-tiering).

* <a id="afs-effective-vfs"></a>
**Jak je *volné místo na svazku* interpretovat při mám několik koncových bodů serveru na svazku?**  
 Zobrazit [vrstvení cloudu Principy](storage-sync-cloud-tiering.md#afs-effective-vfs).

* <a id="afs-files-excluded"></a>
**Které soubory nebo složky jsou automaticky vyloučeny Azure File Sync?**  
    Azure File Sync ve výchozím nastavení, vyloučí následující soubory:
    * Desktop.ini
    * Thumbs.DB
    * ehthumbs.DB
    * ~$\*.\*
    * \*.laccdb
    * \*TMP
    * 635D02A9D91C401B97884B82B3BCDAEA.\*

    Ve výchozím nastavení jsou vyloučeni také následující složky:

    * \System volume Information.
    * \$RECYKLACE. KOŠ
    * \SyncShareState

* <a id="afs-os-support"></a>
**Můžete použít Azure File Sync s Windows serverem 2008 R2, Linux nebo zařízení úložiště připojeného k síti (NAS)?**  
    Azure File Sync v současné době podporuje pouze systém Windows Server 2016 a Windows Server 2012 R2. V tuto chvíli nemáme jakékoli jiné plány, které společnost Microsoft může sdílet, ale jsme otevření podporuje další platformy založené na požadavcích zákazníků. Dejte nám vědět v [UserVoice soubory Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files) jaké platformy který byste nám chtěli na podporu.

* <a id="afs-tiered-files-out-of-endpoint"></a>
**Proč vrstvené soubory existují mimo obor názvů koncový bod serveru?**  
    Azure File Sync starší než verze agenta Azure File Sync 3, zablokuje přesunutí vrstvených souborů mimo koncový bod serveru, ale na stejném svazku jako koncový bod serveru. Operace kopírování, přesune mimo vrstvené soubory a přesune z vrstveného na jiných svazků byla poškozena. Důvod pro toto chování je implicitní předpoklad, které mají Průzkumníka souborů a jiných rozhraní Windows API, operace na stejném svazku jsou (téměř), že operace přejmenování instanenous se přesunou. To znamená, že přejde způsobí, že Průzkumníka souborů nebo jiné metody move (například příkazového řádku nebo Powershellu) zobrazí reagovat, zatímco Azure File Sync vrátí data z cloudu. Počínaje [verze agenta Azure File Sync 3.0.12.0](storage-files-release-notes.md#agent-version-30120), Azure File Sync vám umožní přesunout vrstvených sdílených mimo koncový bod serveru. Můžeme vyhnout negativní důsledky už jsme zmínili, což vrstvených sdílených existovat jako vrstvených sdílených mimo koncový bod serveru a pak vrací soubor na pozadí. To znamená, že, který přesune na stejném svazku se instaneous a jsme udělal všechnu práci vrátit soubor na disk po dokončení přesunutí. 

* <a id="afs-do-not-delete-server-endpoint"></a>
**Mám potíže s Azure File Sync na serveru (synchronizace, cloudových vrstev, etc). By měla odebrat a znovu vytvořte koncový bod pro tento server?**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
**Můžete přesunout službu synchronizace úložiště a/nebo účtu úložiště do jiné skupiny prostředků nebo předplatného?**  
   Ano, služba synchronizace úložiště a/nebo účtu úložiště lze přesunout do jiné skupiny prostředků nebo předplatného. Pokud účet úložiště se přesune, budete muset poskytnout přístup hybridní služby File Sync k účtu úložiště (viz [zajistit Azure File Sync má přístup k účtu úložiště](https://docs.microsoft.com/en-us/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

* <a id="afs-ntfs-acls"></a>
**Azure File Sync se zachovat adresářů a souborů úrovni systému souborů NTFS seznamy řízení přístupu spolu s daty uloženými v Azure Files?**

    Seznamy ACL systému souborů NTFS provádět z místní soubor, který servery jsou zachované v Azure File Sync jako metadata. Služba soubory Azure nepodporuje ověřování pomocí přihlašovacích údajů Azure AD pro přístup ke sdíleným složkám, které jsou spravované službou Azure File Sync.
    
## <a name="security-authentication-and-access-control"></a>Zabezpečení, ověřování a řízení přístupu
* <a id="ad-support"></a>
**Je ověřování pomocí služby Active Directory a access control nepodporuje soubory Azure?**  
    
    Ano, podporuje soubory Azure založené na identitě ověřování a řízení přístupu se službou Azure Active Directory (Azure AD) (Preview). Azure AD authentication přes protokol SMB pro soubory Azure využívá Azure Active Directory Domain Services povolit připojených k doméně virtuální počítače pro přístup k sdílených složek, adresářů a souborů pomocí přihlašovacích údajů Azure AD. Další podrobnosti najdete v tématu [přehled o Azure Active Directory authentication přes protokol SMB pro soubory Azure (Preview)](storage-files-active-directory-overview.md). 

    Služba soubory Azure nabízí dva další způsoby, jak spravovat řízení přístupu:

    - Sdílené přístupové podpisy (SAS) můžete použít ke generování tokenů, které mají konkrétní oprávnění, a které jsou platné pro zadaný časový interval. Můžete například vygenerovat token s přístupem jen pro čtení ke konkrétnímu souboru, který má platnost během 10 minut. Každý, kdo má token, zatímco je token platný má přístup jen pro čtení pro daný soubor pro těchto 10 minut. V současné době klíče sdíleného přístupového podpisu jsou podporovány pouze přes rozhraní REST API nebo klientských knihoven. Sdílené složky Azure přes protokol SMB je nutné připojit s použitím klíče účtu úložiště.

    - Azure File Sync chrání a replikuje všechny volitelné seznamy řízení přístupu nebo volitelné seznamy řízení přístupu, (ať už prostřednictvím služby Active Directory nebo místní) pro všechny koncové body serveru, které se synchronizuje. Vzhledem k tomu, že systém Windows Server můžete už ověřování pomocí Active Directory, Azure File Sync je efektivní možnost stop mezery do plnou podporu pro ověřování pomocí služby Active Directory a přijde podpora seznam ACL.

* <a id="ad-support-regions"></a>
**Je ve verzi preview služby Azure AD prostřednictvím protokolu SMB pro soubory Azure k dispozici ve všech oblastech Azure?**

    Verzi preview je k dispozici ve všech veřejných oblastech s výjimkou: západní USA, střed USA – Jih, střed USA, západní Evropa, Severní Evropa.

* <a id="ad-support-on-premises"></a>
**Ověřování pomocí Azure AD z místní počítače podporuje ověřování Azure AD prostřednictvím protokolu SMB pro soubory Azure (Preview)?**

    Ne, Azure Files nepodporuje ověřování pomocí Azure AD z místních počítačů ve verzi preview.

* <a id="ad-support-devices"></a>
**Fakturuje se u služby Azure AD authentication přes protokol SMB pro soubory Azure (Preview) podporu protokolu SMB přístup pomocí přihlašovacích údajů Azure AD ze zařízení připojená k nebo zaregistrované v Azure AD?**

    Ne, tento scénář není podporován.

* <a id="ad-support-rest-apis"></a>
**Dochází k rozhraní REST API pro podporu Get/Set/kopírování adresářů a souborů NTFS seznamy ACL?**

    Verzi preview nepodporuje rozhraní REST API k získání, nastavení, nebo zkopírujte NTFS seznamy ACL pro soubory nebo adresáře.

* <a id="ad-vm-subscription"></a>
**Mohu přistupovat pomocí přihlašovacích údajů Azure AD z virtuálního počítače v rámci jiného předplatného Azure Files?**

    Pokud předplatné, ve kterém je nasazená sdílené složky je spojen se stejným tenantem Azure AD jako nasazení služby Azure AD Domain Services, ke které je virtuální počítač připojený k doméně a pak budete moct soubory Azure pomocí stejných přihlašovacích údajů Azure AD. Omezení je nastaveno z důvodu není v předplatném, ale na související služby Azure AD tenanta.    
    
* <a id="ad-support-subscription"></a>
**Můžu povolit ověřování Azure AD prostřednictvím protokolu SMB pro soubory Azure s tenantem Azure AD, která se liší od primární tenant se kterým je sdílená složka file assoicated?**

    Ne, soubory Azure podporuje pouze integraci Azure AD s tenantem Azure AD, který se nachází ve stejném předplatném jako sdílené. Jenom jedno předplatné, může být přidružen k tenantovi Azure AD.

* <a id="ad-linux-vms"></a>
**Podporuje ověřování Azure AD prostřednictvím protokolu SMB pro soubory Azure (Preview) virtuálních počítačů s Linuxem?**

    Ne, není podporován ověřování z virtuálních počítačů s Linuxem ve verzi preview.

* <a id="ad-aad-smb-afs"></a>
**Můžete využít ověřování Azure AD prostřednictvím funkce protokolu SMB ve sdílených složkách, které spravuje Azure File Sync?**

    Ne, Azure Files nepodporuje zachování seznamů ACL systému souborů NTFS ve sdílených složkách, které spravuje Azure File Sync. Soubor, který seznamy řízení přístupu provedených v místní souborové servery jsou zachované v Azure File Sync. Ve službě Azure File Sync se přepíšou všechny seznamy řízení přístupu systému souborů NTFS nativně nakonfigurované pro soubory Azure. Soubory Azure navíc nepodporuje ověřování pomocí přihlašovacích údajů Azure AD pro přístup ke sdíleným složkám, které jsou spravované službou Azure File Sync.

* <a id="encryption-at-rest"></a>
**Jak můžete zajistit, že Moje sdílené složky Azure se šifrují při nečinnosti**  

    Šifrování služby Azure Storage se právě probíhá povolená ve výchozím nastavení ve všech oblastech. Pro tyto oblasti nemusíte provádět žádné akce Povolit šifrování. Pro ostatní oblasti, naleznete v tématu [šifrování na straně serveru](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>
**Jak umožním přístup ke konkrétnímu souboru pomocí webového prohlížeče?**  

    Sdílené přístupové podpisy lze použít ke generování tokenů, které mají konkrétní oprávnění, a které jsou platné pro zadaný časový interval. Můžete například vygenerovat token, který poskytuje přístup jen pro čtení ke konkrétnímu souboru po nastavenou dobu. Zatímco je token platný každý, kdo má adresu URL můžete přístup k souboru přímo z libovolného webového prohlížeče. Lze snadno generovat sdílený přístupový klíč podpisu z uživatelského rozhraní, jako je Průzkumník úložišť.

* <a id="file-level-permissions"></a>
**Je možné zadat jen pro čtení nebo jen pro zápis oprávnění pro složky ve sdílené složce?**  

    Připojení sdílené složky pomocí protokolu SMB, nemáte složku úroveň kontroly nad oprávněními. Pokud vytvoříte sdíleného přístupového podpisu pomocí rozhraní REST API nebo klientských knihoven, můžete však zadat oprávnění jen pro čtení nebo jen pro zápis pro složky ve sdílené složce.

* <a id="ip-restrictions"></a>
**Můžete implementovat omezení IP adres pro sdílené složky Azure?**  

    Ano. Přístup ke sdílené složky Azure mohou být omezeny na úrovni účtu úložiště. Další informace najdete v tématu [virtuálních sítí a bran firewall nakonfigurovat Storage Azure](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>
**Zásady dodržování předpisů dat nepodporuje soubory Azure?**  

   Služba soubory Azure se spouští nad rámec stejné architektury úložiště, který se používá v dalších službách úložiště ve službě Azure Storage. Služba soubory Azure platí stejné zásady dodržování předpisů dat, které se používají v jiných služeb úložiště Azure. Další informace o dodržování předpisů pro data služby Azure Storage najdete [nabídek dodržování předpisů služby Azure Storage](https://docs.microsoft.com/en-us/azure/storage/common/storage-compliance-offerings)a přejděte [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx).

## <a name="on-premises-access"></a>Místní přístup
* <a id="expressroute-not-required"></a>
**Máte připojení do služby soubory Azure pomocí Azure ExpressRoute nebo použít Azure File Sync v místním?**  

    Ne. ExpressRoute není vyžadován pro přístup k sdílené složky Azure. Pokud se připojujete sdílené složky Azure přímo v místním, všechny, které je nutné je máte port 445 (odchozí TCP) otevřete pro přístup k Internetu (to je port, který komunikuje pomocí protokolu SMB). Pokud používáte Azure File Sync, všechny, které je nutné je port 443 (odchozí TCP) pro přístup k protokolu HTTPS (bez protokolu SMB povinné). Však můžete *můžete* ExpressRoute pomocí některé z těchto možností přístupu.

* <a id="mount-locally"></a>
**Jak můžu připojit sdílené složky Azure na místním počítači?**  

    Připojení sdílené složky pomocí protokolu SMB, pokud je otevřený port 445 (odchozí TCP) a váš klient podporuje protokol SMB 3.0 (například pokud používáte Windows 10 nebo Windows Server 2016). Pokud je port 445 blokovaný podle zásad vaší organizace nebo svého poskytovatele internetových služeb, můžete použít Azure File Sync pro přístup k vaší sdílené složky Azure.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**Jak se dá zálohování Azure soubor sdílet?**  
    Můžete použít pravidelné [sdílet snímky](storage-snapshots-files.md) pro ochranu před náhodným odstraněním. Můžete také můžete použít AzCopy, Robocopy nebo zálohování nástroj třetí strany, který můžete zálohovat připojené sdílené složky. Azure Backup poskytuje zálohování souborů Azure. Další informace o [zálohování Azure sdílených složek pomocí Azure Backup](https://docs.microsoft.com/en-us/azure/backup/backup-azure-files).

## <a name="share-snapshots"></a>Snímky sdílené složky

### <a name="share-snapshots-general"></a>Sdílet snímky: Obecné
* <a id="what-are-snaphots"></a>
**Co jsou snímky sdílené složky?**  
    Snímky sdílené složky Azure můžete vytvořit verzi této sdílené složky jen pro čtení. Soubory Azure můžete použít také ke zkopírování starší verzi sady obsahu zpět do stejné sdílené složce, do jiného umístění v Azure nebo místně pro víc úpravy. Další informace o snímků sdílené složky, najdete v článku [sdílet snímek přehled](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>
**Kde jsou uloženy Moje snímků sdílené složky?**  
    Snímky sdílené složky se ukládají v rámci stejného účtu úložiště jako sdílené.

* <a id="snapshot-perf-impact"></a>
**Existují jakékoli dopad výkonu pomocí snímků sdílené složky?**  
    Snímky sdílené složky nemají žádné nároky na výkon.

* <a id="snapshot-consistency"></a>
**Snímky sdílené složky jsou konzistentní s aplikací?**  
    Ne, snímků sdílené složky nejsou konzistentní s aplikací. Uživatel musí vyprázdnění zápisy z aplikace do sdílené složky, před provedením snímku sdílené složky.

* <a id="snapshot-limits"></a>
**Existují omezení počtu snímků sdílené složky, které můžete použít?**  
    Ano. Služba soubory Azure uchovávat maximálně 200 sdílet snímky. Snímky sdílené složky se nepočítají do kvóta pro sdílenou složku, takže není nijak celkové místo, které používají všechny snímky sdílené složky za sdílené složky. Stále platí omezení účtu úložiště. Po snímků 200 sdílených složek je nutné odstranit starší snímky k vytvoření nových snímků sdílené složky.

* <a id="snapshot-cost"></a>
**Kolik sdílí náklady na snímky?**  
    Standardní transakce a náklady na úložiště úrovně standard se bude vztahovat na snímek. Snímky se přičítají ze své podstaty. Je základní snímek sdílené složky, samotného. Všechny další snímky se přičítají a uloží pouze rozdíl od předchozího snímku. To znamená, že rozdílové změny, které se zobrazí ve vyúčtování bude minimální, pokud je minimální vaše klidové vytížení. Zobrazit [stránce s cenami](https://azure.microsoft.com/pricing/details/storage/files/) pro standardní informace o cenách službě soubory Azure. V dnešní době je způsob, jak se podívat na velikosti spotřebované snímku sdílené složky porovnáním billed kapacitu se kapacita využitá. Pracujeme na nástroje k vylepšení vytváření sestav.

* <a id="ntfs-acls-snaphsots"></a>
**Jsou seznamy ACL systému souborů NTFS na adresáře a soubory jako trvalý v snímků sdílené složky?**
    Seznamy ACL systému souborů NTFS na adresářů a souborů se uchovávají v snímků sdílené složky.

### <a name="create-share-snapshots"></a>Vytvoření snímků sdílené složky
* <a id="file-snaphsots"></a>
**Můžete vytvořit snímek sdílené složky z jednotlivých souborů?**  
    Snímky sdílené složky se vytvářejí na úrovni sdílené složky. Můžete jednotlivé soubory obnovit ze snímku sdílené složky souboru, ale nemůžete vytvářet snímky sdílené složky souborů. Nicméně, pokud jste provedli snímek sdílené složky úrovni sdílené složky a chcete zobrazit seznam snímků sdílené složky se změnou konkrétní soubor, můžete udělat v rámci **předchozí verze** do sdílené složky připojené Windows. 
    
    Pokud potřebujete funkce snímku souborů, dejte nám vědět v [UserVoice soubory Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encypted-snapshots"></a>
**Můžete vytvořit snímky sdílené složky nebo do sdílené složky souborů EFS?**  
    Udělat snímek sdílené složky ze sdílených složek Azure, které mají šifrování v klidovém stavu povolená. Soubory můžete obnovit ze snímku sdílené složky do sdílené složky zašifrované. Pokud je šifrovaný s ve sdílené složce vaší snímku sdílené složky jsou zašifrovaná, i.

* <a id="geo-redundant-snaphsots"></a>
**Moje snímků sdílené složky jsou geograficky redundantní?**  
    Snímky sdílené složky mají stejnou redundanci jako sdílenou složku Azure file, pro kterou byly provedeny. Pokud jste vybrali geograficky redundantní úložiště pro svůj účet, vaše sdílená složka snímků taky ukládána v párované oblasti dodatečně.

### <a name="manage-share-snapshots"></a>Správa snímků sdílené složky
* <a id="browse-snapshots-linux"></a>
**Můžete procházet Moje snímků sdílené složky z Linuxu?**  
    Vytváření, výpisu, procházení a obnovení snímků sdílené složky v systému Linux, můžete použít rozhraní příkazového řádku Azure.

* <a id="copy-snapshots-to-other-storage-account"></a>
**Můžete zkopírovat snímky sdílené složky do jiného účtu úložiště?**  
    Můžete zkopírovat soubory ze snímků sdílené složky do jiného umístění, ale nelze zkopírovat snímky sdílené složky sami.

### <a name="restore-data-from-share-snapshots"></a>Data obnovit ze snímků sdílené složky
* <a id="promote-share-snapshot"></a>
**Můžete zvýšit úroveň snímku sdílené složky na základní sdílenou složku?**  
    Kopírovat data ze snímku sdílené složky do libovolného cíle. Nelze zvýšit úroveň snímku sdílené složky na základní sdílenou složku.

* <a id="restore-snapshotted-file-to-other-share"></a>
**Můžu provést obnovení dat z mé snímku sdílené složky do jiného účtu úložiště?**  
    Ano. Soubory ze snímku sdílené složky můžete zkopírovat do původního umístění nebo do alternativního umístění, která obsahuje stejný účet úložiště nebo jiný účet úložiště, ve stejné oblasti nebo v různých oblastech. Také můžete zkopírovat soubory do umístění v místním nebo do jakéhokoli cloudu.    
  
### <a name="clean-up-share-snapshots"></a>Vyčištění snímků sdílené složky
* <a id="delete-share-keep-snapshots"></a>
**Můžete odstranit mé sdílenou složku ale nikoli odstraňovat Moje snímků sdílené složky?**  
    Pokud máte aktivní sdílené složce snímků ve vaší sdílené složce, do sdílené složky nelze odstranit. Rozhraní API můžete použít k odstranění snímků sdílené složky, spolu se sdílenou složku. Můžete také odstranit snímky sdílené složky a sdílené složky na webu Azure Portal.

* <a id="delete-share-with-snapshots"></a>
**Co se stane Moje snímků sdílené složky, když odstraním svůj účet úložiště?**  
    Při odstranění účtu úložiště, se odstraní také snímky sdílené složky.

## <a name="billing-and-pricing"></a>K fakturaci a cenách
* <a id="vm-file-share-network-traffic"></a>
**Sítě mezi Virtuálním počítači Azure a sdílenou složkou soubory Azure provoz jako externí datové přenosy, které jsou zpoplatněné nad rámec předplatného?**  
    Pokud jsou sdílenou složku a virtuálních počítačů ve stejné oblasti Azure, neexistuje žádné další poplatky za přenos dat mezi sdílené složky a virtuálního počítače. Pokud sdílené složky a virtuální počítač jsou v různých oblastech, provoz mezi nimi jsou účtován jako externí datové.

* <a id="share-snapshot-price"></a>
**Kolik sdílí náklady na snímky?**  
     Ve verzi preview se neúčtuje za kapacita snímku sdílené složky. Náklady na odchozí transakce a transakce služby storage úrovně standard se vztahují. Po obecné dostupnosti předplatné bude účtovat kapacita a transakce na snímky sdílené složky.
     
     Snímky sdílené složky se přičítají ze své podstaty. Snímek základní sdílená složka je sdílená složka, samotného. Přístup ke všem snímkům další sdílené složky se přičítají a ukládat jenom rozdíl oproti předchozím snímku sdílené složky. Bude vám Účtovaná pouze změněný obsah. Pokud máte sdílenou složku se 100 GB dat, ale jenom 5 GB se změnil od vaší poslední snímek sdílené složky, využívá jenom 5 dalších GiB snímku sdílené složky a se vám účtuje 105 GB. Další informace o transakci a poplatky za odchozí přenos standard najdete v článku [stránce s cenami](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="scale-and-performance"></a>Škálování a výkon
* <a id="files-scale-limits"></a>
**Jaké jsou limity škálování Azure Files?**  
    Informace o škálovatelnost a výkonnostní cíle pro soubory Azure najdete v tématu [škálovatelnost a výkonnostní cíle Azure Files](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>
**Potřebuji větší sdílení souborů než souborů Azure v současné době nabízí. Můžete zvýšit velikost Moje sdílené složky Azure?**  
    Ne. Maximální velikost sdílené složky Azure je 5 TiB. V současné době je pevný limit, který jsme nelze upravit. Pracujeme na řešení, které zvýšit velikost sdílené složky na 100 TB, ale časové osy pro sdílení v tuto chvíli nemáme.

* <a id="open-handles-quota"></a>
**Kolik klientů může souběžně přístup ke stejnému souboru?**   
    Používá se kvóta 2 000 otevřenými popisovači v jednom souboru. Až budete mít 2 000 otevřené popisovače, se zobrazí chybová zpráva s upozorněním, že je dosaženo kvóty.

* <a id="zip-slow-performance"></a>
**Když mám rozbalte soubory ve službě soubory Azure je pomalé Moje výkon. Co bych měl/a dělat?**  
    Přenést do služby soubory Azure velký počet souborů, doporučujeme použít AzCopy (pro Windows, ve verzi preview pro Linux a UNIX) nebo Azure Powershellu. Tyto nástroje jsou optimalizované pro přenos v síti.

* <a id="slow-perf-windows-81-2012r2"></a>
**Proč je můj pomalého výkonu po můžu připojit své sdílené složky Azure v systému Windows Server 2012 R2 nebo Windows 8.1?**  
    Při připojení sdílené složky Azure v systému Windows Server 2012 R2 a Windows 8.1 je známý problém. Byl tento problém opravit v kumulativní aktualizaci. dubna 2014 pro Windows 8.1 a Windows Server 2012 R2. Pro optimální výkon zajistěte, aby tato oprava použít všechny instance Windows Server 2012 R2 a Windows 8.1. (Vždy byste měli obdržet oprav Windows prostřednictvím Windows Update.) Další informace najdete v článku znalostní báze Microsoft Knowledge Base [pomalý výkon při přístupu k Azure Files z Windows 8.1 nebo Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Funkce a vzájemná funkční spolupráce s jinými službami
* <a id="cluster-witness"></a>
**Můžete použít své sdílené složky Azure jako *určující sdílenou složku* pro Cluster převzetí služeb při selhání Windows serveru?**  
    Tato konfigurace není v současné době nepodporuje pro sdílené složky Azure. Další informace o tom, jak nastavit službu Azure Blob storage najdete v tématu [nasazení cloudové kopie clusteru pro převzetí služeb při selhání clusteru](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>
**Můžete připojit sdílené složky Azure na instanci kontejneru Azure?**  
    Ano, sdílených složek Azure jsou dobrou volbou, pokud chcete zachovat informace za dobu života instance kontejneru. Další informace najdete v tématu [připojení sdílené složky Azure pomocí služby Azure Container instances](../../container-instances/container-instances-mounting-azure-files-volume.md).

* <a id="rest-rename"></a>
**Je k dispozici v rozhraní REST API operaci přejmenovat?**  
    V tuto chvíli to není možné.

* <a id="nested-shares"></a>
**Můžete nastavit vnořené sdílené složky? Jinými slovy sdílenou složku ve sdílené složce?**  
    Ne. Sdílené složky souborů *je* virtuální jednotka, kterou můžete připojit, takže vnořené sdílené složky se nepodporují.

* <a id="ibm-mq"></a>
**Jak používat soubory Azure s IBM MQ?**  
    Společnost IBM vydala dokument, který pomáhá zákazníkům IBM MQ nakonfigurovat IBM služby soubory Azure. Další informace najdete v tématu [jak nastavit správce fronty více instancí IBM MQ se službou Microsoft Azure Files](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).

## <a name="see-also"></a>Další informace najdete v tématech
* [Řešení problémů se soubory Azure ve Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Řešení problémů se soubory Azure v Linuxu](storage-troubleshoot-linux-file-connection-problems.md)
* [Řešení potíží s Azure File Sync](storage-sync-files-troubleshoot.md)
