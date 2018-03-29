---
title: Nejčastější dotazy k Azure Files | Microsoft Docs
description: Odpovědi na časté otázky o Azure Files.
services: storage
documentationcenter: ''
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 12/04/2017
ms.author: renash
ms.openlocfilehash: cb44f1d456ec12b7fd21e397b749117942560f05
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="frequently-asked-questions-about-azure-files"></a>Časté otázky k Azure Files
[Soubory Azure](storage-files-introduction.md) nabízí plně spravované sdílené složky v cloudu, které jsou přístupné přes oborových standardů [zpráva bloku protokol Server (SMB)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) (také označovaný jako systém souborů běžné Internet nebo CIFS). Sdílené složky Azure můžete ke cloudu nebo na místní nasazení systému Windows, Linux a systému macOS připojit současně. Také můžete mezipaměti Azure sdílené složky na počítačích systému Windows Server pomocí synchronizace souboru Azure (preview) pro rychlý přístup blízko kde data se používají.

Tento článek obsahuje odpovědi na časté otázky týkající se Azure soubory funkcí a funkčnosti, včetně použití synchronizace souborů Azure s Azure Files. Pokud nevidíte odpověď na svoji otázku, kontaktujte nás prostřednictvím následující kanály (v narůstajícím pořadí):

1. Komentáře části tohoto článku.
2. [Fórum pro Azure Storage](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
3. [Soubory Azure UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Podporu společnosti Microsoft. K vytvoření nové žádosti o podporu, na portálu Azure na **pomoci** vyberte **Nápověda a podpora** tlačítko a potom vyberte **nová žádost o podporu**.

## <a name="general"></a>Obecné
* <a id="why-files-useful"></a>
**Jak je Azure Files užitečné?**  
   Soubory Azure můžete použít pro vytvoření sdílených složek v cloudu, aniž by musel být zodpovědní za správu nároky na fyzickém serveru, zařízení nebo zařízení. Provedeme monotónní práce pro vás, včetně použití aktualizací operačního systému a nahrazení chybných disků. Další informace o scénářích použití souborů Azure můžete s najdete v tématu [proč Azure soubory je užitečné](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>
**Jaké jsou různé způsoby, jak přistupovat k souborům v souborech Azure?**  
    Sdílené složce na místním počítači můžete připojit pomocí protokolu SMB 3.0, nebo můžete použít nástroje, například [Storage Explorer](http://storageexplorer.com/) přístup k souborům ve sdílené složce. Z vaší aplikace můžete použít knihovny klienta úložiště, rozhraní REST API, Powershellu nebo rozhraní příkazového řádku Azure pro přístup k souborům v Azure sdílené složky.

* <a id="what-is-afs"></a>
**Co je Azure souboru synchronizace?**  
    Synchronizace souboru Azure můžete centralizovat vaší organizace sdílené složky v souborech Azure, zatímco flexibilitu, výkonu a kompatibility pro místní souborový server. Synchronizace služby Azure souboru transformuje vašich počítačů Windows serveru na rychlé mezipaměti Azure sdílené složky. Můžete použít libovolný protokol, který je k dispozici v systému Windows Server pro přístup k datům místně, včetně protokolu SMB, systém souborů NFS a soubor přenos protokol služby FTPS (). Může mít libovolný počet mezipamětí, jako je třeba po celém světě.

* <a id="files-versus-blobs"></a>
**Proč používat sdílenou složku Azure a Azure Blob storage pro svá data?**  
    Soubory Azure a Azure Blob, úložiště i nabízet způsoby, jak ukládat velké objemy dat v cloudu, ale jsou užitečné pro účely mírně lišit. 
    
    Azure Blob storage je užitečné pro masivním měřítku, nativní cloudové aplikace, které potřebují pro ukládání nestrukturovaných dat. Pokud chcete maximalizovat výkon a škálování, úložiště objektů Blob Azure je jednodušší abstrakce úložiště než systém souborů na hodnotu true. Úložiště objektů Blob v Azure můžete přistupovat pouze prostřednictvím klientské knihovny založené na REST (nebo přímo přes protokol založené na REST).

    Soubory Azure je konkrétně systém souborů. Soubory Azure má všechny přehledů souborů, které znáte a rádi z let práce operační systémy na místě. Soubory Azure jako úložiště objektů Blob v Azure nabízí rozhraní REST a klientské knihovny založené na REST. Na rozdíl od úložiště objektů Azure Blob soubory Azure nabízí SMB přístup do sdílené složky Azure. Pomocí protokolu SMB, můžete připojit sdílenou složku Azure přímo v systému Windows, Linux nebo systému macOS, buď místní nebo v cloudu virtuálních počítačů, bez psaní jakéhokoli kódu nebo připojení k systému souborů žádné speciální ovladače. Také můžete mezipaměti Azure sdílených složek na souborových serverech místně pomocí synchronizace souboru Azure pro rychlý přístup blízko kde data se používají. 
   
    Podrobnější popis na rozdíly mezi soubory Azure a Azure Blob storage najdete v tématu [rozhodování, kdy používat úložiště objektů Azure Blob, soubory Azure nebo Azure disky](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Další informace o Azure Blob storage najdete v tématu [Úvod do úložiště objektů Blob](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**Proč používat sdílenou složku Azure místo disky Azure?**  
    Disk v Azure disků je jednoduše disk. Není moc užitečné samostatný disk samostatně. Chcete-li získat hodnotu z Azure disků, musí připojit disk k virtuálnímu počítači, který běží v Azure. Disky systému Azure lze použít pro všechno, co se využije disk na místní server. Můžete ho použít jako disk s operačním systémem systému, jako odkládacího prostoru pro operační systém nebo jako vyhrazeného úložiště pro aplikaci. Použití zajímavé pro disky Azure je vytvořit souborový server v cloudu používat stejné místech, kde můžete použít sdílenou složku Azure. Nasazení souborového serveru v Azure Virtual Machines je vysoce výkonné způsob, jak získat soubor úložiště v Azure, pokud požadujete možnosti nasazení, které aktuálně nepodporují Azure soubory (třeba podporu nebo premium úložiště serveru NFS protocol). 

    Obvykle spuštěna souborový server s disky, Azure jako back endové úložné je však mnohem dražší než pomocí sdílenou složku Azure, z několika důvodů. Nejprve kromě platícího ukládání na disk, také musí platíte za náklady spuštěných jeden nebo více virtuálních počítačů Azure. Druhý je také nutné spravovat virtuální počítače, které se používají ke spuštění souborový server. Například jste zodpovědní za upgrady operačního systému. Nakonec pokud požadujete nakonec data uložená v mezipaměti místní, je to na můžete nastavit a spravovat replikace technologie, jako jsou distribuované replikace systému souborů (DFSR), toho docílit.

    Jeden z přístupů k získání nejvhodnější soubory Azure a souborový server, který je hostován v Azure Virtual Machines (kromě používání Azure disky jako back endové úložné) je pro instalaci synchronizace Azure soubor na souborovém serveru, který je hostován v cloudu virtuálního počítače. Pokud sdílenou složkou Azure ve stejné oblasti jako souborový server, můžete povolit cloudu vrstvení a nastavte objem procento volného místa na maximální (99 %). Tím se zajistí minimální duplicitních dat. Můžete taky použít všech aplikací, které chcete s souborové servery, jako jsou aplikace, které vyžadují protokol NFS podporují.

    Informace o možnost pro nastavení vysoce výkonné a vysoce dostupný souborový server v Azure najdete v tématu [clusterů nasazení virtuálních počítačů IaaS hostovaného v Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). Podrobnější popis rozdílů mezi Azure soubory a disky Azure najdete v tématu [rozhodování, kdy používat úložiště objektů Azure Blob, soubory Azure nebo Azure disky](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Další informace o discích Azure najdete v tématu [přehled Azure spravované disky](../../virtual-machines/windows/managed-disks-overview.md).

* <a id="get-started"></a>
**Jak můžu začít pracovat pomocí souborů Azure?**  
   Začínáme s Azure Files je snadné. První, [vytvoření sdílené složky](storage-how-to-create-file-share.md)a pak připojte v operačním systému upřednostňované: 

    * [Připojení v systému Windows](storage-how-to-use-files-windows.md)
    * [Připojení v systému Linux](storage-how-to-use-files-linux.md)
    * [Připojení v systému macOS](storage-how-to-use-files-mac.md)

   Podrobnější informace o nasazení sdílenou složku Azure nahradit produkční sdílené složky ve vaší organizaci najdete v tématu [plánování nasazení Azure Files](storage-files-planning.md).

* <a id="redundancy-options"></a>
**Jaké možnosti redundance úložiště podporuje soubory Azure?**  
    Soubory Azure v současné době podporuje místně redundantní úložiště (LRS), zóny redundantní úložiště (ZRS) a geograficky redundantní úložiště (GRS). Plánujeme podporu geograficky redundantní úložiště (RA-GRS) přístup pro čtení v budoucnu, ale nemáme časové osy sdílet v tuto chvíli.

* <a id="tier-options"></a>
**Jaké vrstvami úložiště jsou podporovány v souborech Azure?**  
    Soubory Azure v současné době podporuje pouze úroveň standardní úložiště. Časové osy ke sdílení pro storage úrovně premium a studeného úložiště podporují v tuto chvíli nemáme. 
    
    > [!NOTE]
    > Sdílené složky Azure nelze vytvořit z jen objektu blob úložiště účty nebo účty úložiště premium.

* <a id="give-us-feedback"></a>
**Opravdu chcete najdete v části konkrétní funkce přidána do Azure Files. Můžete ho přidat?**  
    Tým služby Azure Files je zajímá konzultaci veškeré zpětnou vazbu, měli byste o naši službu. Prosím hlasovat o žádosti o funkce v [Azure soubory UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)! Nemůžeme se Těšíme se na delighting jste s mnoha nových funkcí.

## <a name="azure-file-sync"></a>Azure File Sync

* <a id="afs-region-availability"></a>
**Jaké oblasti jsou podporované pro synchronizaci souborů Azure (preview)?**  
    V současné době synchronizace souboru Azure je k dispozici v Austrálie – východ, Střední Kanada, východní USA, jihovýchodní Asie, Spojené království – Jih, západní Evropa a západní USA. Podpora pro více oblastí se přidat, protože jsme postupujte k obecné dostupnosti. Další informace najdete v tématu [dostupnost v oblastech](storage-sync-files-planning.md#region-availability).

* <a id="cross-domain-sync"></a>
**Může mít servery připojené k doméně a domény nepřipojená ve stejné skupině synchronizace?**  
    Ano. Synchronizace skupina může obsahovat koncové body serveru, které mají různé členství ve službě Active Directory, i když nejsou připojené k doméně. I když tato konfigurace technicky funguje, nedoporučujeme to jako typické konfigurace protože seznamy řízení přístupu (ACL), které jsou definovány pro soubory a složky na jednom serveru nemusí být možné vynutit jinými servery ve skupině synchronizace. Nejlepších výsledků dosáhnete doporučujeme, abyste synchronizaci mezi servery, které jsou ve stejné doménové struktuře služby Active Directory, mezi servery, které jsou v různých doménových strukturách služby Active Directory, ale které mají mezi sebou vytvořené vztahy důvěryhodnosti nebo mezi servery, které nejsou v doméně. Doporučujeme vyhnout pomocí kombinace těchto konfigurací.

* <a id="afs-change-detection"></a>
**Vytvoření souboru přímo v mé sdílenou složku Azure přes protokol SMB nebo na portálu. Jak dlouho trvá souboru pro synchronizaci servery ve skupině synchronizace?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**Pokud stejný soubor se změnil na dvou serverech přibližně ve stejnou dobu, co se stane?**  
    Synchronizace služby Azure soubor používá strategie simple řešení konfliktů: jsme zachovat oba změny u souborů, které se změnilo na dva servery ve stejnou dobu. Nedávno napsané změnu ponechá původní název souboru. Starší soubor má počítač "zdroj" a číslo konflikt připojeným k názvu. Postupuje tuto taxonomii: 
   
    \<FileNameWithoutExtension\>-\<MachineName\>\[-#\].\<ext\>  

    Například by první konflikt CompanyReport.docx jako CompanyReport CentralServer.docx, pokud CentralServer kde starší zápisu došlo k chybě. Druhý konflikt by se jmenovala CompanyReport. CentralServer 1.docx.

* <a id="afs-storage-redundancy"></a>
**Geograficky redundantní úložiště podporovány pro synchronizaci souborů Azure?**  
    Ano, podporuje Azure soubory místně redundantní úložiště (LRS) a geograficky redundantní úložiště (GRS). Pokud dojde k selhání GRS mezi oblastmi spárované, doporučujeme, abyste jako zálohu dat pouze zacházet s novou oblast. Synchronizace služby Azure souboru nezačíná automaticky synchronizuje s novou primární oblasti. 

* <a id="sizeondisk-versus-size"></a>
**Proč není *místo na disku* vlastnost pro odpovídající soubor *velikost* vlastnost po dokončení synchronizace souboru Azure?**  
    Průzkumníka souborů ve Windows zpřístupní dvě vlastnosti představují velikost souboru: **velikost** a **místo na disku**. Tyto vlastnosti se mírně liší v význam. **Velikost** představuje dokončení velikost souboru. **Místo na disku** představuje velikost datového proudu souboru, který je uložen na disku. Hodnoty pro tyto vlastnosti můžete pro celou řadu důvodů, například komprese se liší, použijte odstranění duplicitních dat nebo cloudu vrstvení se synchronizací souboru Azure. Pokud je soubor vrstvené na sdílenou složku Azure, bude velikost disku rovná nule, protože datový proud souboru je uložené v Azure sdílené složky a nikoli na disku. Je také možné pro soubor, který chcete být částečně vrstvené (nebo částečně odvolané). V souboru částečně vrstvené část souboru, je na disku. Tomu může dojít, když jsou aplikace, jako je přehrávačů multimédií částečně číst soubory nebo zip nástroje. 

* <a id="is-my-file-tiered"></a>
**Jak poznám, jestli byl vrstvené soubor?**  
    Zkontrolujte, zda soubor byly zřízeny vrstvené do sdílené složky Azure file několika způsoby:
    
   *  **Zkontrolujte atributy souborů v souboru.**
     Chcete-li to provést, klikněte pravým tlačítkem na soubor, přejděte na **podrobnosti**a poté přejděte dolů k **atributy** vlastnost. Soubor vrstvené má nastavit následující atributy:     
        
        | Atribut písmeno | Atribut | Definice |
        |:----------------:|-----------|------------|
        | A | Archiv | Označuje, že je nutné soubor zálohovat zálohování softwarem. Tento atribut je vždy nastaven, bez ohledu na to, zda je soubor vrstvené nebo plně uložená na disku. |
        | P | Chování řídkého souboru | Určuje, zda je soubor zhuštěného souboru. Chování řídkého souboru je speciálním typem souboru, který nabízí systému souborů NTFS pro efektivní použití Pokud soubor v datovém proudu disku je většinou prázdný. Synchronizace služby Azure soubor používá zhuštěných souborů, protože soubor je buď plně vrstvené nebo částečně zrušit. V souboru ve plně vrstvené datový proud souboru je uložené v cloudu. V částečně odvolané souboru, který je součástí souboru již na disku. Pokud je soubor plně připomenout na disk, synchronizace souboru Azure převede z zhuštěného souboru na běžných souborů. |
        | L | Spojovací bod | Určuje, že soubor má bod rozboru. Bod opakování analýzy je speciální ukazatel za účelem použití filtrem systému souborů. Synchronizace služby Azure soubor používá spojovací body umožňují definovat k Azure souboru Sync filtrem systému souborů (StorageSync.sys) cloudu umístění, kde jsou uložené souboru. To podporuje bezproblémový přístup. Uživatelé nebudou muset vědět, jestli se používá synchronizaci Azure soubor nebo jak získat přístup k souboru v Azure sdílené složky. Pokud soubor plně je třeba připomenout, synchronizace souboru Azure bodu rozboru odebere ze souboru. |
        | O | Offline | Označuje, že některé nebo všechny obsah souboru není uložen na disku. Pokud soubor plně je třeba připomenout, odebere synchronizace souboru Azure tento atribut. |

        ![Dialogové okno Vlastnosti souboru, se na kartě podrobnosti vybrané](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Zobrazí atributy pro všechny soubory ve složce přidáním **atributy** pole do tabulky zobrazení Průzkumníka souborů. Chcete-li to provést, klikněte pravým tlačítkem na existující sloupec (například **velikost**), vyberte **Další**a potom vyberte **atributy** z rozevíracího seznamu.
        
   * **Použití `fsutil` zkontrolujte spojovacích bodů na soubor.**
       Jak je popsáno v předchozí možnosti, vrstvené soubor má vždy spojovacích bodů sady. Ukazatel rozboru je speciální ukazatel pro Azure souboru Sync filtrem systému souborů (StorageSync.sys). Chcete-li zkontrolovat, zda má soubor bod rozboru v příkazovém řádku nebo prostředí PowerShell okno se zvýšenými oprávněními, spusťte `fsutil` nástroj:
    
        ```PowerShell
        fsutil reparsepoint query <your-file-name>
        ```

        Pokud má soubor bod rozboru, můžete očekávat zobrazíte **rozboru hodnota značky: 0x8000001e**. Tato hexadecimální hodnota je hodnota bodu rozboru, který je vlastněn synchronizace souboru Azure. Výstup obsahuje také rozboru data, která představuje cestu k souboru na Azure sdílené složky.

        > [!WARNING]  
        > `fsutil reparsepoint` Nástroj příkaz má také možnost Odstranit bod rozboru. Pokud technického týmu Azure souboru Sync požádá o není provedení tohoto příkazu. Spuštění tohoto příkazu může způsobit ztrátu dat. 

* <a id="afs-recall-file"></a>**Byly zřízeny vrstvené soubor, který chcete použít. Jak můžete odvolat soubor disk používat místně?**  
    Nejjednodušší způsob, jak odvolat soubor na disk se k otevření souboru. Synchronizace souboru Azure filtrem systému souborů (StorageSync.sys) bezproblémově stáhne soubor z Azure sdílené složky bez jakékoli úsilím na vaší straně. Pro typy souborů, které může být částečně pro čtení, jako jsou soubory multimédia nebo .zip, otevření souboru se nestahuje celý soubor.

    Také můžete použít PowerShell k vynucení soubor, který chcete zrušit. Tato možnost může být užitečné, pokud chcete odvolat několik souborů najednou, například všechny soubory ve složce. Otevřete relaci prostředí PowerShell na uzlu serveru, kde je nainstalován synchronizace souboru Azure a pak spusťte následující příkazy prostředí PowerShell:
    
    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
    ```

* <a id="afs-force-tiering"></a>
**Jak vynutit soubor nebo adresář, chcete-li být rozvrstvena?**  
    Pokud je povolena funkce vrstvení cloudu, cloudu vrstvení automaticky vrstev soubory založená na posledním přístupu a upravit dobu k dosažení Procento volné místo svazku, zadaný pro koncový bod cloudu. V některých případech ale můžete chtít ručně vynutit soubor k vrstvě. To může být užitečné, pokud uložíte velký soubor, který nemáte v úmyslu znovu použít po dlouhou dobu a chcete volné místo na svazku teď chcete použít pro další soubory a složky. Můžete vynutit vrstvení pomocí následujících příkazů prostředí PowerShell:

    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
    ```

* <a id="afs-effective-vfs"></a>
**Jak je *volné místo na svazku* interpretovat při ve svazku, je nutné několik koncových bodů serveru?**  
    Když je na svazku více než jeden koncový bod serveru, je prahová hodnota volného místa efektivní svazku největší volné místo svazku zadaný napříč žádný koncový bod serveru na tomto svazku. Soubory budou vrstvené podle způsobu jejich použití bez ohledu na to, které koncový bod serveru, do které patří. Například pokud máte dva koncové body serveru ve svazku, koncovém bodě 1 a Endpoint2, kde má prahová hodnota volného místa na svazku % 25 koncovém bodě 1 a Endpoint2 prahová hodnota volného místa na svazku, 50 % prahová hodnota volného místa na svazku pro oba koncové body serveru bude 50 %.

* <a id="afs-files-excluded"></a>
**Které soubory nebo složky se automaticky vyloučí pomocí synchronizace souboru Azure?**  
    Ve výchozím nastavení vyloučí synchronizace souboru Azure následující soubory:
    * desktop.ini
    * thumbs.db
    * ehthumbs.db
    * ~$\*.\*
    * \*.laccdb
    * \*TMP.
    * 635D02A9D91C401B97884B82B3BCDAEA.\*

    Ve výchozím nastavení jsou také vyloučit následující složky:

    * \System volume Information
    * \$RECYCLE.BIN
    * \SyncShareState

* <a id="afs-os-support"></a>
**Můžete použít Azure soubor synchronizaci s Windows Server 2008 R2, Linux nebo zařízení úložiště připojeného k síti (NAS)?**  
    Synchronizace souboru Azure v současné době podporuje pouze systém Windows Server 2016 a Windows Server 2012 R2. V tuto chvíli nemáme jiných plánů, které jsme můžete sdílet, ale nemohli jsme otevřít další platformy, na základě poptávky zákaznické podpory. Dejte nám vědět v [Azure soubory UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) jaké platformy který byste nám chtěli na podporu.

## <a name="security-authentication-and-access-control"></a>Zabezpečení, ověřování a řízení přístupu
* <a id="ad-support"></a>
**Je ověřování na základě služby Active Directory a řízení přístupu podporovanou soubory Azure?**  
    Soubory Azure nabízí dva způsoby, jak spravovat řízení přístupu:

    - Sdílené přístupové podpisy (SAS) můžete použít ke generování tokenů, které mají konkrétní oprávnění, a které jsou platné pro zadaný časový interval. Například můžete vygenerovat token s přístupem jen pro čtení pro konkrétní soubor, který má vypršení platnosti 10 minut. Každý, kdo má token, ale je platný token má přístup jen pro čtení k souboru pro těchto 10 minut. Sdílený přístupový podpis klíče v současné době jsou podporovány, jen přes REST API nebo knihovny klienta. Je nutné připojit sdílenou složkou Azure přes protokol SMB pomocí klíče účtu úložiště.

    - Synchronizace služby Azure souboru zachovává a replikuje všechny volitelné seznamy řízení přístupu nebo volitelné seznamy řízení přístupu, (na základě služby Active Directory nebo místní) na všechny koncové body serveru, které synchronizaci do. Protože Windows Server můžete již ověřit pomocí služby Active Directory, synchronizace souboru Azure je efektivní možnost zastavit mezera dokud plnou podporu pro ověřování na základě služby Active Directory a dorazí podporu seznamu ACL.

    V současné době soubory Azure Active Directory přímo nepodporuje.

* <a id="encryption-at-rest"></a>
**Jak můžete zajistit, že moje sdílenou složku Azure je zašifrovaná přinejmenším?**  
    Šifrování služby Azure Storage se právě probíhá povolené ve výchozím nastavení ve všech oblastech. Pro tyto oblasti nemusíte provádět žádné akce Povolit šifrování. Pro další oblasti, najdete v části [šifrování na straně serveru](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>
**Jak může poskytnout přístup k určitým souborem pomocí webového prohlížeče?**  
    Sdílené přístupové podpisy můžete použít ke generování tokenů, které mají konkrétní oprávnění, a které jsou platné pro zadaný časový interval. Například můžete vygenerovat token, který poskytuje přístup jen pro čtení k konkrétní soubor, na nastavenou dobu. Každý, kdo má adresu URL můžete přístup k souboru přímo z libovolného webového prohlížeče, ale token je platný. Můžete snadno vygenerovat sdílený přístupový podpis klíč z uživatelského rozhraní jako Průzkumník úložišť.

* <a id="file-level-permissions"></a>
**Je možné zadat jen pro čtení nebo jen pro zápis oprávnění pro složky ve sdílené složce?**  
    Pokud připojíte sdílenou složku přes protokol SMB, nemáte složku úroveň kontroly nad oprávněními. Pokud vytvoříte sdílený přístupový podpis pomocí rozhraní REST API nebo knihovny klienta, můžete však zadat oprávnění jen pro čtení nebo jen pro zápis pro složky ve sdílené složce.

* <a id="ip-restrictions"></a>
**Můžete implementovat omezení IP adres pro sdílenou složku Azure?**  
    Ano. Přístup k Azure sdílené složky lze omezit na úrovni účtu úložiště. Další informace najdete v tématu [brány firewall nakonfigurovat úložiště Azure a virtuální sítě](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>
**Jaké zásady dodržování předpisů dat podporuje soubory Azure?**  
   Soubory Azure běží nad stejnou architekturu úložiště, který se používá v jiné služby úložiště ve službě Azure Storage. Soubory Azure platí stejné zásady dodržování předpisů dat, které se používají v jiných služeb úložiště Azure. Další informace o dodržování předpisů dat úložiště Azure, můžete stáhnout a odkazovat [dokumentu Microsoft Azure Data Protection](http://go.microsoft.com/fwlink/?LinkID=398382&clcid=0x409)a přejděte na [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/default.aspx).

## <a name="on-premises-access"></a>Místní přístup
* <a id="expressroute-not-required"></a>
**Je nutné použít Azure ExpressRoute pro připojení k Azure Files nebo můžete používat synchronizaci Azure soubor místně?**  
    Ne. ExpressRoute není potřeba přístup Azure sdílené složky. Pokud jsou připojení Azure sdílená přímo na místě, všechny které je požadováno, je port 445 (odchozí TCP), otevřete pro přístup k Internetu (to je port, který používá ke komunikaci SMB). Pokud používáte Azure souboru Sync, všechny, které je nutné je port 443 (odchozí TCP) pro protokol HTTPS přístupu (požadované žádné SMB). Však můžete *můžete* pomocí ExpressRoute s některou z těchto možností přístupu.

* <a id="mount-locally"></a>
**Jak můžou připojit sdílenou složku Azure na můj místního počítače?**  
    Sdílené složky můžete připojit pomocí protokolu SMB, pokud je otevřený port 445 (odchozí TCP) a váš klient podporuje protokol SMB 3.0 (například pokud používáte Windows 10 nebo Windows Server 2016). Pokud port 445 zablokován podle zásad vaší organizace nebo podle vašeho poskytovatele internetových služeb, můžete pro přístup k Azure sdílené složky synchronizace souboru Azure.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**Jak zálohovat Moje Azure soubor sdílet?**  
    Můžete použít pravidelné [sdílet snímky](storage-how-to-use-files-snapshots.md) pro ochranu před náhodným odstraněním. Také můžete použít AzCopy, Robocopy nebo zálohování nástroj třetí strany, který můžete zálohovat připojené sdílené složky. 

## <a name="share-snapshots"></a>Sdílet snímky
### <a name="share-snapshots-general"></a>Sdílet snímky: Obecné
* <a id="what-are-snaphots"></a>
**Jaké jsou snímky sdílené složky souborů?**  
    Snímky sdílenou složku Azure file slouží k vytvoření verzi sdílené složky jen pro čtení. Soubory Azure můžete použít také ke zkopírování dřívější verzi obsahu zpět do stejné složky, do alternativního umístění v Azure nebo místním pro další úpravy. Další informace o sdílené složce snímků, najdete v článku [sdílet snímku přehled](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>
**Kde jsou uloženy snímky Moje sdílené složky?**  
    Sdílené složky snímků jsou uložené ve stejném účtu úložiště jako sdílené složky.

* <a id="snapshot-perf-impact"></a>
**Existují jakékoli ovlivnit výkon pro použití sdílené složky snímků?**  
    Sdílené složky snímky nemají žádné nároky na výkon.

* <a id="snapshot-consistency"></a>
**Jsou sdílené složky snímky konzistentní vzhledem k aplikacím?**  
    Ne, sdílené složky snímků nejsou konzistentní s aplikací. Uživatel musí k vyprázdnění zápisů z aplikace do sdílené složky před přepnutím sdílenou složku snímku.

* <a id="snapshot-limits"></a>
**Existují omezení počtu snímků sdílené složky, které mohu použít?**  
    Ano. Soubory Azure můžete uchovávat maximálně 200 sdílené složky snímků. Sdílené složky snímků nepočítá kvóta pro sdílenou složku, takže není žádné omezení za sdílené složky na celkové místo, které používají všechny snímky sdílené složky. Limity účtu úložiště se stále účtují. Po 200 sdílené složky snímků je nutné odstranit starší snímky k vytvoření nové sdílené složky snímků.
* <a id="snapshot-cost"></a>
**Kolik sdílet náklady na snímku?**  
    Standardní transakce a náklady na úložiště standard se uplatní na snímku. Snímky jsou ve své podstatě přírůstkové. Základní snímku je sdílená složka sám sebe. Všechny následné snímky jsou přírůstkové a uloží pouze rozdílový z předchozího snímku. To znamená, že rozdílové změny, které se zobrazí v kusovníku bude minimální, pokud je minimální, vaše klidové vytížení. V tématu [cenová stránky](https://azure.microsoft.com/en-us/pricing/details/storage/files/) pro standardní soubory Azure informace o cenách. V dnešní době způsob, jak můžete prohlížet velikost používané ve sdílené složce snímků je tak, že porovnáte fakturovaná kapacitu s použít kapacity. Pracujeme na nástrojů ke zlepšení generování sestav.


### <a name="create-share-snapshots"></a>Vytvoření sdílené složky snímků
* <a id="file-snaphsots"></a>
**Můžete vytvořit sdílenou složku snímku jednotlivých souborů?**  
    Sdílené složky snímků jsou vytvořené na úrovni sdílené složky souborů. Můžete obnovit jednotlivé soubory ze sdílené složky snímku souboru, ale nemůžete vytvářet snímky sdílené složky souborů. Ale pokud jste provedli snímku sdílené složky úrovni sdílené složky a chcete vypsat snímky sdílenou složku se změnou konkrétní soubor, můžete k tomu pod **předchozí verze** ve sdílené složce připojené systému Windows. 
    
    Pokud potřebujete funkci snímku souboru, dejte nám vědět, na [Azure soubory UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encypted-snapshots"></a>
**Můžete vytvořit sdílenou složku snímků sdílené složky souborů EFS?**  
    Může trvat snímek sdílené složky Azure sdílené složky, které mají šifrování v klidovém stavu povoleno. Můžete obnovit soubory ze sdílené složky snímku do šifrované sdílené složky. Pokud je vaše sdílená složka šifrovat, sdílenou složku snímku jsou šifrované.

* <a id="geo-redundant-snaphsots"></a>
**Jsou mé sdílené složky snímků geograficky redundantní?**  
    Sdílené složky snímků mít stejnou redundanci jako Azure sdílené složky, pro které byly provedeny. Pokud jste vybrali geograficky redundantní úložiště pro svůj účet, do sdílené složky snímku také ukládána v oblasti spárované dodatečně.

### <a name="manage-share-snapshots"></a>Spravovat sdílené složky snímků
* <a id="browse-snapshots-linux"></a>
**Můžete procházet Moje snímky sdílenou složku z Linux?**  
    Azure CLI 2.0 slouží k vytvoření, seznam, procházet a obnovení snímky sdílenou složku v systému Linux.

* <a id="copy-snapshots-to-other-storage-account"></a>
**Můžete zkopírovat snímky sdílenou složku na jiný účet úložiště?**  
    Můžete zkopírovat soubory ze sdílené složky snímků do jiného umístění, ale nelze zkopírovat snímky sdílené složky sami.

### <a name="restore-data-from-share-snapshots"></a>Obnovení dat ze sdílené složky snímků
* <a id="promote-share-snapshot"></a>
**Můžete zvýšit úroveň sdílenou složku snímku do základní složky?**  
    Může kopírovat data ze snímku sdílené složky do jiné cíle. Nelze zvýšit úroveň sdílenou složku snímku do základní složky.

* <a id="restore-snapshotted-file-to-other-share"></a>
**Je možné obnovit data z mé sdílenou složku snímku na jiný účet úložiště?**  
    Ano. Lze zkopírovat soubory ze snímku sdílenou složku, do původního umístění nebo do alternativního umístění, které obsahuje stejný účet úložiště nebo jiný účet úložiště, ve stejné oblasti nebo v různých oblastech. Také můžete zkopírovat soubory do místního umístění nebo do jiných cloudu.    
  
### <a name="clean-up-share-snapshots"></a>Vyčištění snímky sdílené složky
* <a id="delete-share-keep-snapshots"></a>
**Může odstranit mé sdílenou složku ale nikoli odstraňovat snímky Moje sdílené složky?**  
    Pokud máte aktivní sdílené složky snímků na sdílené složce, do sdílené složky nelze odstranit. Pokud chcete odstranit sdílenou složku snímky, společně s sdílené složky můžete použít rozhraní API. Můžete také odstranit snímky sdílené složky a sdílené složky v portálu Azure.

* <a id="delete-share-with-snapshots"></a>
**Co se stane Moje sdílené složky snímků, pokud svůj účet úložiště odstranit?**  
    Pokud váš účet úložiště odstraníte, budou odstraněny také snímky sdílené složky.

## <a name="billing-and-pricing"></a>Fakturaci a cenách
* <a id="vm-file-share-network-traffic"></a>
**Sítě provoz mezi virtuálním počítači Azure a Azure sdílenou složkou jako externí šířku pásma, které jsou zpoplatněné nad rámec předplatného?**  
    Pokud sdílená složka a virtuálních počítačů jsou ve stejné oblasti Azure, je pro provoz mezi sdílené složky a virtuální počítač bez dalších poplatků. Pokud jsou sdílené složky a virtuálního počítače v různých oblastech, provoz mezi nimi jsou účtován jako externí šířky pásma.

* <a id="share-snapshot-price"></a>
**Kolik sdílí náklady snímky?**  
     Verzi Preview není nijak zpoplatněn pro sdílenou složku snímku kapacity. Poplatky za odchozí data a transakci standardní úložiště použít. Po obecné dostupnosti si předplatné bude vám účtována kapacitu a transakce na sdílené složky snímků.
     
     Sdílené složky snímky jsou ve své podstatě přírůstkové. Snímek základní sdílená složka je sdílená složka sám sebe. Všechny následné sdílené složce snímky jsou přírůstkové a uložit jenom rozdíl oproti předchozím snímku sdílené složky. Fakturuje se pouze pro změněný obsah. Pokud máte sdílenou složku se 100 GiB dat, ale jenom 5 GiB se změnil od posledního sdílenou složku snímku, snímku sdílení využívá jenom 5 Další GiB a fakturuje se 105 GiB. Další informace o transakci a standardní nimi spojeným nákladům, najdete v článku [cenová stránky](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="scale-and-performance"></a>Rozsah a výkon
* <a id="files-scale-limits"></a>
**Jaké jsou limity škálování Azure souborů?**  
    Informace o škálovatelnosti a cílech výkonnosti pro soubory Azure najdete v tématu [Azure Files škálovatelnosti a cílech výkonnosti](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>
**Potřebuji větší sdílené složky než soubory Azure aktuálně nabízí. Můžete zvětšit velikost Moje sdílenou složku Azure?**  
    Ne. Maximální velikost sdílené složky Azure file je 5 TiB. Toto je v současné době pevný limit, který jsme nelze upravit. Pracujeme na řešení a zvyšte velikost sdílené složky na 100 TiB, ale nemáme časové osy sdílet v tuto chvíli.

* <a id="open-handles-quota"></a>
**Kolik klientů mohou přistupovat ke stejnému souboru současně?**   
    Není kvótu 2 000 otevřenými popisovači v jeden soubor. Až budete mít 2 000 otevřenými popisovači, se zobrazí chybová zpráva s upozorněním, že je dosaženo kvóty.

* <a id="zip-slow-performance"></a>
**Moje výkon je pomalý, když I rozbalit soubory v Azure Files. Co bych měl/a dělat?**  
    K Azure Files přenášet větší počet souborů, doporučujeme použít AzCopy (pro systém Windows; ve verzi preview pro Linux a UNIX) nebo Azure PowerShell. Tyto nástroje jsou optimalizované pro síťové přenosy.

* <a id="slow-perf-windows-81-2012r2"></a>
**Proč je můj výkon pomalé po můžu připojit sdílenou složku Moje Azure na Windows Server 2012 R2 nebo Windows 8.1?**  
    Při připojení Azure sdílené složky na Windows Server 2012 R2 a Windows 8.1 je známý problém. Byl problém opravit v kumulativní aktualizaci. dubna 2014 pro Windows 8.1 a Windows Server 2012 R2. Pro optimální výkon Ujistěte se, že všechny instance pro systém Windows Server 2012 R2 a Windows 8.1 mít tato oprava použít. (Vždy byste měli obdržet opravy Windows pomocí služby Windows Update.) Další informace najdete v souvisejícím článku znalostní báze Microsoft Knowledge Base [pomalý výkon při přístupu k Azure Files z Windows 8.1 nebo Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Funkce a vzájemná funkční spolupráce s jinými službami
* <a id="cluster-witness"></a>
**Můžete použít Moje sdílenou složku Azure jako *určující sdílenou složku* pro moje clusteru převzetí služeb při selhání Windows serveru?**  
    Tato konfigurace není v současné době podporována pro sdílenou složku Azure. Další informace o tom, jak nastavit službu Azure Blob storage najdete v tématu [nasazení cloudu určující pro Cluster s podporou převzetí služeb při selhání](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>
**Můžete v instanci kontejneru Azure připojit sdílenou složku Azure?**  
    Ano, Azure sdílené složky jsou vhodný, pokud chcete zachovat informace mimo dobu životnosti instance kontejneru. Další informace najdete v tématu [připojit sdílenou složku Azure s instancemi Azure kontejneru](../../container-instances/container-instances-mounting-azure-files-volume.md).

* <a id="rest-rename"></a>
**Je k dispozici v rozhraní REST API operaci přejmenovat?**  
    V tuto chvíli to není možné.

* <a id="nested-shares"></a>
**Můžete nastavit vnořené sdílené složky? Jinými slovy sdílenou složku ve sdílené složce?**  
    Ne. Sdílené složky *je* virtuální jednotka, kterou můžete připojit, takže vnořené sdílené složky se nepodporují.

* <a id="ibm-mq"></a>
**Jak používat Azure soubory s IBM MQ?**  
    Společnost IBM vydala dokument, který pomáhá zákazníkům IBM MQ nakonfigurovat službu IBM Azure Files. Další informace najdete v tématu [jak nastavit správce front pro více instancemi IBM MQ se službou Microsoft Azure Files](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).

## <a name="see-also"></a>Další informace najdete v tématech
* [Řešení potíží s Azure souborů v systému Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Řešení potíží s Azure soubory v systému Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Řešení potíží s synchronizace souboru Azure (preview)](storage-sync-files-troubleshoot.md)
