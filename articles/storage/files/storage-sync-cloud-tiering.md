---
title: Principy azure file sync cloudtiering | Dokumenty společnosti Microsoft
description: Další informace o funkci Azure File Sync cloudtiering
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 11f9097fc4875f0a4300ac56dafe7af9a0b00c97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454614"
---
# <a name="cloud-tiering-overview"></a>Cloudvrstving – přehled
Vrstvení cloudu je volitelná funkce Azure File Sync, ve které se často přistupují k souborům místně na serveru, zatímco všechny ostatní soubory jsou vrstvené na soubory Azure na základě nastavení zásad. Když je soubor vrstvený, filtr systému souborů Azure File Sync (StorageSync.sys) nahradí soubor místně ukazatelem nebo změní analýzu bodu. Bod závažnosti představuje adresu URL souboru v Azure Files. Vrstvený soubor má atribut "offline" i atribut FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS nastavený v systému souborů NTFS, takže aplikace třetích stran mohou bezpečně identifikovat vrstvené soubory.
 
Když uživatel otevře vrstvený soubor, Azure File Sync bez problémů navolá data souboru ze souborů Azure, aniž by uživatel potřeboval vědět, že soubor je uložený v Azure. 
 
 > [!Important]  
 > Vrstvení cloudu není na systémovém svazku Windows podporováno.
    
 > [!Important]  
 > Chcete-li vyvolat soubory, které byly vrstvené, šířka pásma sítě by měla být alespoň 1 Mbps. Pokud šířka pásma sítě je menší než 1 Mb/s, může dojít k odvolání souborů s chybou časového omezení.

## <a name="cloud-tiering-faq"></a>Nejčastější dotazy k vrstvení cloudu

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>Jak funguje vrstvení cloudu?
Filtr systému Azure File Sync vytvoří "heatmap" vašeho oboru názvů na každém koncovém bodu serveru. Monitoruje přístupy (operace čtení a zápisu) v průběhu času a pak, na základě frekvence a aktuálnosti přístupu, přiřadí skóre tepla do každého souboru. Často přistupovaný soubor, který byl nedávno otevřen, bude považován za horký, zatímco soubor, který se sotva dotkl a nebyl po určitou dobu přístupný, bude považován za chladný. Pokud svazek souborů na serveru překročí nastavenou prahovou hodnotu volného místa na svazku, bude vrstvit nejlepší soubory do souborů Azure, dokud nebude splněno procento volného místa.

Ve verzích 4.0 a vyšší agent azure file sync agenta můžete navíc zadat zásady data na každém koncovém bodu serveru, který bude vrstvy všechny soubory, které nejsou přístupné nebo změněny v rámci zadaného počtu dní.

<a id="tiering-minimum-file-size"></a>
### <a name="what-is-the-minimum-file-size-for-a-file-to-tier"></a>Jaká je minimální velikost souboru pro soubor na úroveň?
U verzí agenta 9.x a novějších je minimální velikost souboru na úroveň založena na velikosti clusteru systému souborů (dvojnásobek velikosti clusteru systému souborů). Pokud je například velikost clusteru systému souborů NTFS 4 KB, je výsledná minimální velikost souboru na úroveň 8 KB. Pro agenta verze 8.x a starší, minimální velikost souboru pro vrstvu je 64 KB.

<a id="afs-volume-free-space"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work"></a>Jak fungují zásady vrstvení volného místa svazku?
Volné místo na svazku je množství volného místa, které chcete rezervovat na svazku, na kterém je umístěn koncový bod serveru. Pokud je například volné místo na svazku nastaveno na 20 % na svazku, který má jeden koncový bod serveru, bude až 80 % místa na svazku obsazeno naposledy přistupovanými soubory, přičemž všechny zbývající soubory se nevejdou do tohoto prostoru vrstveného do Azure. Volné místo na svazku platí na úrovni hlasitosti, nikoli na úrovni jednotlivých adresářů nebo synchronizačních skupin. 

<a id="volume-free-space-fastdr"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work-with-regards-to-new-server-endpoints"></a>Jak fungují zásady vrstvení volného místa svazku s ohledem na nové koncové body serveru?
Když koncový bod serveru je nově zřízena a připojené ke sdílené složce Azure, server nejprve stáhne obor názvů a potom bude stahovat skutečné soubory, dokud nedosáhne prahové hodnoty volného místa svazku. Tento proces se také označuje jako rychlé zotavení po havárii nebo rychlé obnovení oboru názvů.

<a id="afs-effective-vfs"></a>
### <a name="how-is-volume-free-space-interpreted-when-i-have-multiple-server-endpoints-on-a-volume"></a>Jak se interpretuje volné místo svazku, pokud je na něm několik koncových bodů serveru?
Pokud je na svazku více než jeden koncový bod serveru, efektivní prahová hodnota volného místa na svazku je největší volné místo svazku určené v libovolném koncovém bodě serveru na tomto svazku. Soubory budou vrstvené podle jejich vzory použití bez ohledu na to, který koncový bod serveru, ke kterému patří. Pokud máte například dva koncové body serveru na svazku, Endpoint1 a Endpoint2, kde endpoint1 má prahovou hodnotu volného místa na svazku 25 % a koncový bod2 má prahovou hodnotu volného místa na svazku 50 %, bude prahová hodnota volného místa na svazku pro oba koncové body serveru 50 %. 

<a id="date-tiering-policy"></a>
### <a name="how-does-the-date-tiering-policy-work-in-conjunction-with-the-volume-free-space-tiering-policy"></a>Jak fungují zásady vrstvení podle data ve spojení se zásadami vrstvení volného místa svazku? 
Při povolení vrstvení cloudu na koncovém bodu serveru nastavíte zásady volného místa svazku. Vždy má přednost před všemi ostatními zásadami, včetně zásad data. Volitelně můžete povolit zásady data pro každý koncový bod serveru na tomto svazku, což znamená, že pouze soubory přístupné (to znamená, čtení nebo zápis) v rozsahu dnů, které tato zásada popisuje, budou zachovány místní, přičemž všechny soubory staler budou vrstvené. Mějte na paměti, že zásady volného místa na svazku mají vždy přednost, a když na svazku není dostatek volného místa, aby bylo zachováno tolik dní souborů, jak je popsáno v zásadách data, Azure File Sync bude pokračovat v vrstvení nejchladnějších souborů, dokud nebude svazek volný. je splněno procento prostoru.

Řekněme například, že máte zásady vrstvení založené na datu 60 dní a zásady volného místa svazku 20 %. Pokud po použití zásady data je na svazku méně než 20 % volného místa, spustí se zásada volného místa svazku a přepíše zásady data. To bude mít za následek další soubory jsou vrstvené, tak, že množství dat uchovávaných na serveru může být snížena z 60 dnů dat na 45 dnů. Naopak tato zásada vynutí vrstvení souborů, které spadají mimo časový rozsah, i když jste nedosáhli prahové hodnoty volného místa – takže soubor, který je starý 61 dní, bude vrstvený i v případě, že je váš svazek prázdný.

<a id="volume-free-space-guidelines"></a>
### <a name="how-do-i-determine-the-appropriate-amount-of-volume-free-space"></a>Jak určím vhodnou velikost volného místa svazku?
Množství dat, která byste měli uchovávat jako místní, je určeno několika faktory: šířkou pásma, způsobem přístupu datové sady a rozpočtem. Pokud máte připojení s malou šířkou pásma, můžete chtít zachovat více místních dat, abyste zajistili minimální zpoždění pro uživatele. V opačném případě jej můžete založit na rychlosti konve během daného období. Pokud například víte, že přibližně 10 % datové sady 1 TB se mění nebo je aktivně přístupné každý měsíc, můžete chtít zachovat místní 100 GB, abyste často nevolali soubory. Pokud je váš svazek 2 TB, budete chtít zachovat 5 % (nebo 100 GB) místní, což znamená, že zbývajících 95 % je procento volného místa na svazku. Doporučujeme však přidat vyrovnávací paměť pro účet pro období vyšší změny – jinými slovy, počínaje nižší procento volného místa svazku a v případě potřeby upravit později. 

Zachování více místních dat znamená nižší náklady na odchozí přenos, protože z Azure bude odvoláno méně souborů, ale také vyžaduje, abyste udržovali větší množství místního úložiště, které je k dispozici na vlastní náklady. Jakmile máte nasazenou instanci Azure File Sync, můžete se podívat na odchozí přenos vašeho účtu úložiště a zhruba odhadnout, jestli je nastavení volného místa na svazku vhodné pro vaše využití. Za předpokladu, že účet úložiště obsahuje jenom váš koncový bod cloudu synchronizace souborů Azure (tj. vaše sdílená složka synchronizace), pak vysoký odchozí přenos znamená, že mnoho souborů se svolává z cloudu a měli byste zvážit zvýšení místní mezipaměti.

<a id="how-long-until-my-files-tier"></a>
### <a name="ive-added-a-new-server-endpoint-how-long-until-my-files-on-this-server-tier"></a>Přidal jsem nový koncový bod serveru. Jak dlouho potrvá moje soubory na této úrovni serveru?
Ve verzích 4.0 a vyšší agent Azure File Sync, jakmile vaše soubory byly nahrány do sdílené složky Azure, budou vrstvené podle vašich zásad, jakmile se spustí další relace vrstvení, ke kterému dojde jednou za hodinu. U starších agentů může stupňování trvat až 24 hodin.

<a id="is-my-file-tiered"></a>
### <a name="how-can-i-tell-whether-a-file-has-been-tiered"></a>Jak poznám, jestli byl soubor vrstvený?
Existuje několik způsobů, jak zkontrolovat, jestli byl soubor vrstvený do sdílené složky Azure:
    
   *  **Zkontrolujte atributy souboru v souboru.**
     Klikněte pravým tlačítkem myši na soubor, přejděte na **Podrobnosti**a pak přejděte dolů na vlastnost **Atributy.** Vrstvený soubor má nastaveny následující atributy:     
        
        | Písmeno atributu | Atribut | Definice |
        |:----------------:|-----------|------------|
        | A | Archiv | Označuje, že soubor by měl být zálohován zálohovacím softwarem. Tento atribut je vždy nastaven, bez ohledu na to, zda je soubor vrstvený nebo plně uložen na disku. |
        | P | Řídký soubor | Označuje, že soubor je řídké souboru. Řídký soubor je specializovaný typ souboru, který ntfs nabízí pro efektivní použití, když je soubor v datovém proudu disku většinou prázdný. Azure File Sync používá řídké soubory, protože soubor je buď plně vrstvené nebo částečně odvolán. V plně vrstvené souboru datový proud souboru je uložen v cloudu. V částečně odvolaný soubor, že část souboru je již na disku. Pokud je soubor plně odvolán na disk, Azure File Sync jej převede z řídkého souboru na běžný soubor. Tento atribut je nastaven pouze v systému Windows Server 2016 a starší.|
        | M | Odvolání při přístupu k datům | Označuje, že data souboru není plně k dispozici v místním úložišti. Čtení souboru způsobí, že alespoň část obsahu souboru, které mají být načteny ze sdílené složky Azure, ke kterému je připojen koncový bod serveru. Tento atribut je nastaven pouze v systému Windows Server 2019. |
        | L | Spojovací bod | Označuje, že soubor má bod obnovení. Bod pro analýzu je speciální ukazatel pro použití filtrem systému souborů. Azure File Sync používá body reparse k definování filtru systému souborů Azure File Sync (StorageSync.sys) do cloudového umístění, kde je soubor uložený. To podporuje bezproblémový přístup. Uživatelé nebudou potřebovat vědět, že azure file sync se používá nebo jak získat přístup k souboru ve sdílené složce Azure. Když je soubor plně odvolán, Azure File Sync odebere bod závažnosti ze souboru. |
        | O | Offline | Označuje, že část nebo veškerý obsah souboru není uložen na disku. Když je soubor plně odvolán, Azure File Sync odebere tento atribut. |

        ![Dialogové okno Vlastnosti souboru s vybranou kartou Podrobnosti](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Atributy všech souborů ve složce můžete zobrazit přidáním pole **Atributy** do zobrazení tabulky Průzkumníka souborů. Chcete-li to provést, klepněte pravým tlačítkem myši na existující sloupec (například **Velikost**), vyberte **Další**a pak v rozevíracím seznamu vyberte **Atributy.**
        
   * **Slouží `fsutil` ke kontrole bodů pro analýzu v souboru.**
       Jak je popsáno v předchozí možnosti, vrstvený soubor má vždy sadu bodů pro analýzu. Ukazatel pro analýzu je speciální ukazatel pro filtr systému souborů Azure File Sync (StorageSync.sys). Chcete-li zkontrolovat, zda soubor má bod změna řezu, spusťte `fsutil` nástroj v okně příkazového řádku se zvýšenými oprávněními nebo v prostředí PowerShell:
    
        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```

        Pokud má soubor bod doprotivny, můžete očekávat **hodnotu značky Reparse: 0x8000001e**. Tato šestnáctková hodnota je hodnota bodu reparse, která je vlastněna azure file sync. Výstup také obsahuje data změny zpracování, která představují cestu k souboru ve sdílené složce Azure.

        > [!WARNING]  
        > Příkaz `fsutil reparsepoint` nástroje má také možnost odstranit bod zlomu. Nespouštějte tento příkaz, pokud vás o to nepožádá technický tým Azure File Sync. Spuštění tohoto příkazu může vést ke ztrátě dat. 

<a id="afs-recall-file"></a>

### <a name="a-file-i-want-to-use-has-been-tiered-how-can-i-recall-the-file-to-disk-to-use-it-locally"></a>Soubor, který chci použít, byl vrstvený. Jak lze odvolat soubor na disk, aby jej použít místně?
Nejjednodušší způsob, jak vyvolat soubor na disk, je otevřít soubor. Filtr systému souborů Azure File Sync (StorageSync.sys) bez problémů stáhne soubor ze sdílené složky Azure bez práce z vaší části. U typů souborů, ze kterých lze částečně číst, například z multimediálních souborů nebo souborů ZIP, se otevřením souboru nestáhne celý soubor.

Můžete také použít Prostředí PowerShell vynutit soubor, který má být odvolán. Tato možnost může být užitečná, pokud chcete vyvolat více souborů najednou, například všechny soubory ve složce. Otevřete relaci Prostředí PowerShell do uzlu serveru, kde je nainstalovaná Azure File Sync, a spusťte následující příkazy Prostředí PowerShell:
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
Volitelné parametry:
* `-Order CloudTieringPolicy`nejdříve stáhne naposledy upravené soubory.  
* `-ThreadCount`určuje, kolik souborů lze souběžně svolat.
* `-PerFileRetryCount`určuje, jak často se bude o odvolání souboru, který je aktuálně blokován, pokusit.
* `-PerFileRetryDelaySeconds`určuje čas v sekundách mezi opakováním pokusů o odvolání a měl by být vždy použit v kombinaci s předchozím parametrem.

> [!Note]  
> Pokud místní svazek hostující server nemá dostatek volného místa pro vyvolání všech vrstvených dat, `Invoke-StorageSyncFileRecall` rutina se nezdaří.  

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>Proč vlastnost *Velikost na disku* pro soubor neodpovídá vlastnosti *Size* po použití Azure File Sync? 
Průzkumník souborů systému Windows zpřístupňuje dvě vlastnosti představující velikost souboru: **Velikost** a **velikost na disku**. Tyto vlastnosti se mírně liší ve významu. **Velikost** představuje úplnou velikost souboru. **Velikost na disku** představuje velikost datového proudu souboru, který je uložen na disku. Hodnoty pro tyto vlastnosti se mohou lišit z různých důvodů, jako je komprese, použití odstranění duplicitdat nebo vrstvení cloudu pomocí Azure File Sync. Pokud je soubor vrstvený na sdílenou složku Azure, velikost na disku je nulová, protože datový proud souboru je uložený ve sdílené složce Azure a ne na disku. Je také možné, že soubor může být částečně vrstvený (nebo částečně odvolán). V částečně vrstvené souboru část souboru je na disku. K tomu může dojít, pokud jsou soubory částečně čteny aplikacemi, jako jsou multimediální přehrávače nebo nástroje zip. 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>Jak vynutím vrstvení souboru nebo adresáře?
Když je povolena funkce vrstvení cloudu, vrstvení cloudu automaticky vrství soubory na základě posledního přístupu a upravte časy, aby bylo dosaženo procenta volného místa na svazku určeného v koncovém bodě cloudu. Někdy však můžete chtít ručně vynutit soubor na úroveň. To může být užitečné, pokud uložíte velký soubor, který nechcete dlouho znovu použít, a chcete, aby volné místo na svazku bylo nyní využito pro jiné soubory a složky. Vrstvení můžete vynutit pomocí následujících příkazů Prostředí PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

<a id="afs-image-thumbnail"></a>
### <a name="why-are-my-tiered-files-not-showing-thumbnails-or-previews-in-windows-explorer"></a>Proč se v průzkumníku Windows nezobrazují vrstvené soubory miniaturnebo náhledů?
U vrstvených souborů se miniatury a náhledy nebudou v koncovém bodě serveru zobrazovat. Toto chování se očekává, protože funkce mezipaměti miniatur v systému Windows záměrně přeskočí čtení souborů s atributem offline. S povoleným vrstvením cloudu by čtení přes vrstvené soubory způsobilo jejich stažení (vyvoláno).

Toto chování není specifické pro Azure File Sync, Průzkumník Windows zobrazí "šedé X" pro všechny soubory, které mají nastaven atribut offline. Při přístupu k souborům přes SMB se zobrazí ikona X. Podrobné vysvětlení tohoto chování naleznete v části[https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105](https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105)


## <a name="next-steps"></a>Další kroky
* [Plánování nasazení synchronizace souborů Azure](storage-sync-files-planning.md)
