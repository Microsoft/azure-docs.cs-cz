---
title: Principy Azure File Sync vrstvení cloudu | Microsoft Docs
description: Další informace o funkcích clouding funkcí Azure File Sync
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: fea9cebc5199fc7c1fc5c081aa45f08044c21e44
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268090"
---
# <a name="cloud-tiering-overview"></a>Přehled vrstvení cloudu
Vrstvení cloudu je volitelná funkce Azure File Sync, ve které jsou často používané soubory ukládány do mezipaměti místně na serveru, zatímco všechny ostatní soubory jsou vrstveny do souborů Azure na základě nastavení zásad. Při vrstvení souboru Azure File Sync filtr systému souborů (StorageSync. sys) místně nahradí soubor objektem ukazatele nebo bodem rozboru. Bod rozboru představuje adresu URL souboru ve službě soubory Azure. Vrstvený soubor má atribut offline i atribut FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS nastavený v systému souborů NTFS, aby aplikace třetích stran mohli bezpečně identifikovat vrstvené soubory.
 
Když uživatel otevře vrstvený soubor, Azure File Sync hladce znovu volá data souborů ze souborů Azure bez toho, abyste věděli, že soubor je uložený v Azure. 
 
 > [!Important]  
 > Pro koncové body serveru na svazcích se systémem Windows není podporováno vrstvení cloudu a velikost pouze souborů vyšších než 64 KiB může být vrstvena do souborů Azure.
    
Azure File Sync nepodporuje vrstvení souborů menších než 64 KiB, protože nároky na výkon při vrstvení a vracení těchto malých souborů by převažují za úspory místa.

 > [!Important]  
 > Aby bylo možné odvolat soubory, které byly vrstveny, Šířka pásma sítě by měla být minimálně 1 MB/s. Pokud je šířka pásma sítě menší než 1 MB/s, nemusí se soubory po vypršení časového limitu navrátit.

## <a name="cloud-tiering-faq"></a>Nejčastější dotazy k vrstvám cloudu

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>Jak funguje vrstva cloudu?
Filtr systému Azure File Sync vytvoří pro každý koncový bod serveru "heatmapu" svého oboru názvů. Monitoruje v průběhu času přístup (operace čtení a zápisu) a pak na základě četnosti i recency přístupu přiřadí ke každému souboru zahřívání. Často používaný soubor, který se nedávno otevřel, se považuje za horkou, zatímco soubor, který je zlomek a který se v určitou dobu neotevřel, se považuje za studenou. Když svazek souboru na serveru překročí prahovou hodnotu pro volné místo, kterou jste nastavili, bude tyto soubory navrstvit do služby soubory Azure, dokud nedosáhnete procenta volného místa.

Ve verzích 4,0 a vyšších Azure File Sync agenta můžete také zadat zásadu data na každém koncovém bodu serveru, ke kterému se budou navrstvit všechny soubory, ke kterým během zadaného počtu dnů nepřijdete nebo se nemění.

<a id="afs-volume-free-space"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work"></a>Jak fungují zásady vrstvení volného místa na svazku?
Volné místo svazku je množství volného místa, které chcete rezervovat na svazku, na kterém je umístěný koncový bod serveru. Pokud je například volné místo na svazku nastavené na 20% na svazku, který má jeden koncový bod serveru, až 80% místa na svazku bude pracovat s naposledy použitými soubory, všechny zbývající soubory, které se do tohoto místa nevejdou na Azure. Volné místo na svazku se vztahuje na úrovni svazku, nikoli na úrovni jednotlivých adresářů nebo skupin synchronizace. 

<a id="volume-free-space-fastdr"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work-with-regards-to-new-server-endpoints"></a>Jak fungují zásady vrstev volného místa na svazku v souvislosti s novými koncovými body serveru?
Když je koncový bod serveru nově zřízený a připojený ke sdílené složce Azure, server nejdřív tento obor názvů odvolá a potom načte skutečné soubory, dokud nedosáhne prahové hodnoty pro volné místo na svazku. Tento proces se označuje také jako rychlé zotavení po havárii nebo rychlé obnovení oboru názvů.

<a id="afs-effective-vfs"></a>
### <a name="how-is-volume-free-space-interpreted-when-i-have-multiple-server-endpoints-on-a-volume"></a>Jak se interpretuje volné místo na svazku, když mám na svazku více koncových bodů serveru?
Pokud je na svazku více než jeden koncový bod serveru, je prahová hodnota platného volného místa svazku největší volné místo, které je zadáno v libovolném koncovém bodu serveru na daném svazku. Soubory budou vrstveny podle jejich vzorů použití bez ohledu na to, který koncový bod serveru patří. Například pokud máte dva koncové body serveru na svazku, Endpoint1 a pro endpoint2 u, kde Endpoint1 má prahovou hodnotu pro volné místo 25% a pro endpoint2 u má prahovou hodnotu pro volné místo na svazku 50%, bude prahová hodnota volného místa svazku pro oba koncové body serveru 50%. 

<a id="date-tiering-policy"></a>
### <a name="how-does-the-date-tiering-policy-work-in-conjunction-with-the-volume-free-space-tiering-policy"></a>Jak zásady vrstvení dat fungují v kombinaci se zásadami pro vrstvení volného místa na svazku? 
Při povolování vrstvení cloudu na koncovém bodu serveru se nastavuje zásada pro volné místo svazku. Vždycky má přednost před všemi ostatními zásadami, včetně zásad data. Volitelně můžete pro každý koncový bod serveru na tomto svazku povolit zásady pro data, což znamená, že v rámci rozsahu dnů se tato zásada bude uchovávat jenom v případě, že se tyto zásady postarují jako místní, přičemž všechny zastaralé soubory jsou vrstvené. Mějte na paměti, že zásady volného místa na svazku vždy mají přednost, a když na svazku není dost volného místa, abyste zachovali tolik dní, kolik se bude považovat za soubor, který je popsaný v zásadách data, Azure File Sync bude pokračovat ve vrstvení souborů coldest, dokud se svazek neuvolní. je splněno procento místa.

Řekněme například, že máte zásady vrstvení založené na datu 60 dnů a zásady volného místa svazku o 20%. Pokud po použití zásad data je na svazku méně než 20% volného místa, zásada pro volné místo svazku se zahájí a přepíše zásadu data. Výsledkem bude, že se zaměří víc souborů, takže objem dat uchovávaných na serveru se může snížit z 60 dnů od data do 45 dnů. Naopak tato zásada vynutí vrstvení souborů, které spadají mimo váš časový rozsah, a to i v případě, že jste nedosáhli prahové hodnoty pro volné místo, takže soubor, který je 61 dnů staré, bude vrstvený i v případě, že je svazek prázdný.

<a id="volume-free-space-guidelines"></a>
### <a name="how-do-i-determine-the-appropriate-amount-of-volume-free-space"></a>Návody určit odpovídající množství volného místa na svazku?
Množství dat, které byste měli zachovat v místním prostředí, je určené několika faktory: vaše šířka pásma, vzor přístupu k datové sadě a váš rozpočet. Pokud máte připojení s malou šířkou pásma, možná budete chtít zachovat místní data a zajistit tak minimální prodlevu pro uživatele. V opačném případě je můžete v daném období založit na míru četnosti změn. Pokud například víte, že přibližně 10% z 1 TB změn datových sad nebo se k němu aktivně přistupovalo, budete možná chtít zachovat 100 GB místní, abyste nemuseli často odvolat soubory. Pokud je svazek 2 TB, budete chtít zachovat 5% (nebo 100 GB) místní, což znamená, že zbývající 95% je procento volného místa na svazku. Doporučujeme však přidat vyrovnávací paměť pro účty s vyššími změnami – jinými slovy, počínaje nižším procentem volného místa na svazku a následně upravit v případě potřeby později. 

Udržování většího množství dat znamená nižší náklady na výstup, protože méně souborů bude vráceno z Azure, ale také vyžaduje, abyste zachovali větší množství místních úložišť, která se týkají vlastní ceny. Jakmile máte nanasazenou instanci Azure File Sync, můžete si prohlédnout výstup svého účtu úložiště a zhruba tak posoudit, jestli jsou nastavení volného místa na svazku vhodná pro vaše využití. Za předpokladu, že účet úložiště obsahuje jenom váš koncový bod cloudu Azure File Sync (tj. vaše synchronizační sdílená složka), pak vysoký výstup znamená, že se z cloudu odvolají mnoho souborů, a měli byste zvážit zvýšení místní mezipaměti.

<a id="how-long-until-my-files-tier"></a>
### <a name="ive-added-a-new-server-endpoint-how-long-until-my-files-on-this-server-tier"></a>Přidal (a) jsem nový koncový bod serveru. Jak dlouho do má být moje soubory na této vrstvě serveru?
Ve verzích 4,0 a vyšších od agenta Azure File Sync se po nahrání souborů do sdílené složky Azure tyto soubory vrství podle vašich zásad, jakmile se spustí další relace vrstvení, ke které dojde jednou za hodinu. Na starších agentech může zpracování vrstev trvat až 24 hodin.

<a id="is-my-file-tiered"></a>
### <a name="how-can-i-tell-whether-a-file-has-been-tiered"></a>Jak zjistím, jestli byl soubor vrstvený?
Existuje několik způsobů, jak ověřit, zda byl soubor vrstven do sdílené složky Azure:
    
   *  **Ověřte atributy souboru v souboru.**
     Klikněte pravým tlačítkem na soubor, přejděte na **Podrobnosti**a přejděte dolů k vlastnosti **atributy** . Vrstvený soubor má následující sady atributů:     
        
        | Písmeno atributu | Atribut | Definice |
        |:----------------:|-----------|------------|
        | A | Archiv | Indikuje, že by měl být soubor zálohovaný zálohovacím softwarem. Tento atribut je vždy nastaven bez ohledu na to, zda je soubor na disku povrstvený nebo uložený jako plný. |
        | P | Zhuštěný soubor | Označuje, že se jedná o zhuštěný soubor. Zhuštěný soubor je specializovaný typ souboru, který systém souborů NTFS nabízí pro efektivní použití v případě, že je soubor na diskovém streamu většinou prázdný. Azure File Sync používá zhuštěné soubory, protože soubor je buď úplně vrstven, nebo částečně odvolán. V plně vrstveném souboru je datový proud souboru uložený v cloudu. V částečně vráceném souboru je tato část souboru již na disku. Pokud je soubor zcela znovu volán na disk, Azure File Sync jej převede ze zhuštěného souboru do normálního souboru. Tento atribut je nastaven pouze v systémech Windows Server 2016 a starších.|
        | M | Odvolat při přístupu k datům | Indikuje, že data souboru nejsou plně přítomná v místním úložišti. Při čtení souboru dojde k tomu, že se alespoň část obsahu souboru načte ze sdílené složky Azure, ke které je připojený koncový bod serveru. Tento atribut je nastaven pouze v systému Windows Server 2019. |
        | L | Bod rozboru | Označuje, že soubor obsahuje bod rozboru. Bod rozboru je speciální ukazatel pro použití filtrem systému souborů. Azure File Sync používá spojovací body pro definování do filtru souborů Azure File Sync systému souborů (StorageSync. sys), kde se nachází v cloudu, kde je soubor uložený. To podporuje bezproblémový přístup. Uživatelé nebudou muset znát, že se používá Azure File Sync nebo jak získat přístup k souboru ve sdílené složce Azure. Když je soubor zcela znovu vyvolán, Azure File Sync odebere bod rozboru ze souboru. |
        | O | V režimu offline | Indikuje, že některé nebo všechny obsahy souboru nejsou uložené na disku. Když je soubor zcela znovu vyvolán, Azure File Sync tento atribut odstraní. |

        ![Dialogové okno Vlastnosti souboru s vybranou kartou podrobnosti](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Atributy pro všechny soubory ve složce můžete zobrazit tak, že přidáte pole **atributy** do zobrazení tabulky v Průzkumníkovi souborů. Provedete to tak, že kliknete pravým tlačítkem na existující sloupec (například **Velikost**), vyberete **Další**a v rozevíracím seznamu vyberete **atributy** .
        
   * **Použijte `fsutil` ke kontrole bodů rozboru v souboru.**
       Jak je popsáno v předchozí možnosti, vrstvený soubor má vždy nastaven bod rozboru. Ukazatel rozboru je speciální ukazatel pro Azure File Sync filtr systému souborů (StorageSync. sys). Pokud chcete zjistit, jestli má soubor bod rozboru, v příkazovém řádku se zvýšenými oprávněními nebo v okně PowerShellu spusťte nástroj `fsutil`:
    
        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```

        Pokud má soubor bod rozboru, můžete očekávat, že se zobrazí **hodnota značky rozboru: 0x8000001E**. Tato hexadecimální hodnota je hodnota bodu rozboru, která je vlastněna Azure File Sync. Výstup také obsahuje data opětovného zpracování, která představují cestu k souboru ve sdílené složce Azure.

        > [!WARNING]  
        > Příkaz `fsutil reparsepoint` Utility má také možnost odstranit spojovací bod. Nespouštějte tento příkaz, pokud vám Azure File Sync technický tým nežádá. Spuštění tohoto příkazu může způsobit ztrátu dat. 

<a id="afs-recall-file"></a>

### <a name="a-file-i-want-to-use-has-been-tiered-how-can-i-recall-the-file-to-disk-to-use-it-locally"></a>Soubor, který chcete použít, byl vrstven. Jak mohu soubor odvolat na disk a použít ho místně?
Nejjednodušší způsob, jak na disk navrátit soubor, je otevřít soubor. Azure File Sync filtr systému souborů (StorageSync. sys) bezproblémově stáhne soubor ze sdílené složky Azure, aniž by byla na vaší straně potřeba žádná práce. Pro typy souborů, ze kterých se dá částečně číst, například multimédia nebo soubory zip, otevření souboru nestáhne celý soubor.

Můžete také použít PowerShell k vynucení souboru, který se má odvolat. Tato možnost může být užitečná, pokud chcete odvolat více souborů najednou, například všechny soubory ve složce. Otevřete relaci PowerShellu pro uzel serveru, na kterém je nainstalovaná Azure File Sync, a pak spusťte následující příkazy PowerShellu:
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
Volitelné parametry:
* `-Order CloudTieringPolicy` nejprve odvolá naposledy změněné soubory.  
* `-ThreadCount` určuje, kolik souborů lze paralelně volat.
* `-PerFileRetryCount`určuje, jak často se bude opakovat pokus o odvolání souboru, který je aktuálně blokován.
* `-PerFileRetryDelaySeconds`určuje dobu v sekundách mezi opakovanými pokusy o odvolání a měla by být vždy použita v kombinaci s předchozím parametrem.

> [!Note]  
> Pokud místní svazek, který hostuje server, nemá dost volného místa pro odvolání všech vrstvených dat, rutina `Invoke-StorageSyncFileRecall` se nezdařila.  

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>Proč velikost vlastnosti *disku* pro soubor neodpovídá vlastnosti *size* po použití Azure File Sync? 
Průzkumník souborů systému Windows zpřístupňuje dvě vlastnosti, které reprezentují velikost souboru: **Velikost** a **Velikost na disku**. Tyto vlastnosti se v takovém smyslu liší. **Velikost** představuje úplnou velikost souboru. **Velikost na disku** představuje velikost datového proudu souboru uloženého na disku. Hodnoty těchto vlastností se mohou lišit z nejrůznějších důvodů, jako je komprese, použití odstranění duplicitních dat nebo vrstvení cloudu s Azure File Sync. Pokud je soubor vrstvený ve sdílené složce Azure, velikost na disku je nula, protože datový proud souboru je uložený ve sdílené složce Azure, a ne na disku. Je také možné, že se soubor částečně vrstvený (nebo částečně odvolal). V částečně vrstveném souboru je část souboru na disku. K tomu může dojít, když jsou soubory částečně čteny aplikacemi, jako jsou multimediální přehrávače nebo nástroje zip. 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>Návody vynutit vrstvení souboru nebo adresáře?
Když je povolená funkce vrstvení cloudu, vrstva cloudu automaticky rozchází soubory na základě posledního přístupu a doby úprav, aby se dosáhlo procenta volného místa na svazku, které je zadané na koncovém bodu cloudu. Někdy ale můžete chtít ručně vynutit soubor do vrstvy. To může být užitečné, pokud uložíte velký soubor, který nechcete znovu použít po dlouhou dobu, a chcete, aby volné místo na svazku bylo teď k dispozici pro jiné soubory a složky. Vrstvení můžete vynutit pomocí následujících příkazů PowerShellu:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

<a id="afs-image-thumbnail"></a>
### <a name="why-are-my-tiered-files-not-showing-thumbnails-or-previews-in-windows-explorer"></a>Proč mé vrstvené soubory neobsahují miniatury nebo náhledy v Průzkumníkovi Windows?
V případě vrstvených souborů se miniatury a verze Preview nebudou zobrazovat na koncovém bodu serveru. Toto chování je očekávané, protože funkce mezipaměti miniatur ve Windows záměrně přeskočí čtení souborů s atributem offline. Díky povoleným vrstvám cloudu by čtení přes vrstvených souborů způsobilo stažení (vráceno).

Toto chování není specifické pro Azure File Sync, Průzkumník Windows zobrazuje "šedou X" pro všechny soubory, které mají nastaven atribut offline. Při přístupu k souborům přes SMB se zobrazí ikona X. Podrobné vysvětlení tohoto chování najdete v tématu [https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105](https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105)


## <a name="next-steps"></a>Další kroky
* [Plánování nasazení Azure File Sync](storage-sync-files-planning.md)
