---
title: Principy Azure File Sync vrstvení cloudu | Microsoft Docs
description: Přečtěte si o vrstvení cloudu, volitelné funkci Azure File Sync. Často používané soubory jsou ukládány do mezipaměti místně na serveru. ostatní jsou vrstveny na soubory Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 5ca65a428af02eaf5ae6ac461006c720da4461bd
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91538176"
---
# <a name="cloud-tiering-overview"></a>Přehled vrstvení cloudu
Vrstvení cloudu je volitelná funkce Azure File Sync, ve které jsou často používané soubory ukládány do mezipaměti místně na serveru, zatímco všechny ostatní soubory jsou vrstveny do souborů Azure na základě nastavení zásad. Když je soubor vrstvený, Azure File Sync filtr systému souborů (StorageSync.sys) nahradí soubor místně s ukazatelem nebo bodem rozboru. Bod rozboru představuje adresu URL souboru ve službě soubory Azure. Vrstvený soubor má atribut offline i atribut FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS nastavený v systému souborů NTFS, aby aplikace třetích stran mohli bezpečně identifikovat vrstvené soubory.
 
Když uživatel otevře vrstvený soubor, Azure File Sync hladce znovu volá data souborů ze souborů Azure bez toho, abyste věděli, že soubor je uložený v Azure. 
 
 > [!Important]  
 > Ve svazku systému Windows není podporováno vrstvení cloudu.
    
 > [!Important]  
 > Aby bylo možné odvolat soubory, které byly vrstveny, Šířka pásma sítě by měla být minimálně 1 MB/s. Pokud je šířka pásma sítě menší než 1 MB/s, nemusí se soubory po vypršení časového limitu navrátit.

## <a name="cloud-tiering-faq"></a>Nejčastější dotazy k vrstvám cloudu

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>Jak funguje vrstva cloudu?
Filtr systému Azure File Sync vytvoří pro každý koncový bod serveru "heatmapu" svého oboru názvů. Monitoruje v průběhu času přístup (operace čtení a zápisu) a pak na základě četnosti i recency přístupu přiřadí ke každému souboru zahřívání. Často používaný soubor, který se nedávno otevřel, se považuje za horkou, zatímco soubor, který je zlomek a který se v určitou dobu neotevřel, se považuje za studenou. Když svazek souboru na serveru překročí prahovou hodnotu pro volné místo, kterou jste nastavili, bude tyto soubory navrstvit do služby soubory Azure, dokud nedosáhnete procenta volného místa.

Kromě toho můžete zadat zásadu data na každý koncový bod serveru, ke kterému se budou navrstvit všechny soubory nedostupné během zadaného počtu dnů bez ohledu na dostupnou kapacitu místního úložiště. To je dobrá volba k proaktivnímu uvolnění místa na místním disku, pokud víte, že soubory v tomto koncovém bodu serveru není nutné uchovávat místně po určitém stáří. Ta uvolní cennou kapacitu místního disku pro jiné koncové body na stejném svazku, aby bylo možné ukládat do mezipaměti více souborů.

Heatmapu vrstev cloudu je v podstatě uspořádaný seznam všech souborů, které se synchronizují a jsou v umístění s povoleným vrstvou cloudu. Aby bylo možné určit relativní pozici jednotlivého souboru v tomto heatmapu, používá systém maximum kteréhokoli z následujících časových razítek v tomto pořadí: MAX (čas posledního přístupu, čas poslední změny, čas vytvoření). Čas posledního přístupu se obvykle sleduje a je dostupný. Když se ale vytvoří nový koncový bod serveru s povoleným vytvářením vrstev cloudu, pak se zpočátku nedostal dostatek času na sledování přístupu k souboru. Při nedostatku času posledního přístupu se k vyhodnocení relativní pozice v heatmapu použije čas poslední změny. Stejná záloha se vztahuje na zásady data. Bez času posledního přístupu bude zásada data fungovat s časem poslední změny. Neměl by být k dispozici, bude vrácen na čas vytvoření souboru. V průběhu času bude systém sledovat více a více požadavků na přístup k souborům a překlopit se, aby bylo možné předem sledovat čas posledního přístupu na základě sebe.

Vrstvení cloudu nezávisí na funkci systému souborů NTFS ke sledování času posledního přístupu. Tato funkce systému souborů NTFS je ve výchozím nastavení vypnutá a kvůli důležitým informacím o výkonu nedoporučujeme tuto funkci ručně povolit. Vrstvení cloudu sleduje čas posledního přístupu samostatně a velmi efektivně.

<a id="tiering-minimum-file-size"></a>
### <a name="what-is-the-minimum-file-size-for-a-file-to-tier"></a>Jaká je minimální velikost souboru pro soubor do vrstvy?

U agentů verze 9 a novějších je minimální velikost souboru na vrstvu založená na velikosti clusteru systému souborů. Minimální velikost souboru způsobilá pro vytváření vrstev cloudu se počítá podle dvojnásobku velikosti clusteru a minimálně 8 KB. Následující tabulka ilustruje minimální velikosti souborů, které je možné rozvrstvit, na základě velikosti clusteru svazku:

|Velikost clusteru svazků (bajty) |Soubory této velikosti nebo větší lze převrstveny  |
|----------------------------|---------|
|4 KB nebo menší (4096)      | 8 kB    |
|8 KB (8192)                 | 16 kB   |
|16 KB (16384)               | 32 KB   |
|32 KB (32768)               | 64 kB   |
|64 KB (65536) a větší    | 128 kB  |

S Windows serverem 2019 a agentem Azure File Sync verze 12 (budoucí verze agenta) se podporuje i velikost clusteru až do velikosti 2 MB a vrstvení na tyto větší velikosti clusterů funguje stejným způsobem. Starší verze operačních systémů nebo agentů podporují velikosti clusterů až do 64 KB, ale i přes tuto úroveň nefungují.

Všechny systémy souborů používané systémem Windows organizují pevný disk na základě velikosti clusteru (označované také jako velikost alokační jednotky). Velikost clusteru představuje nejmenší množství místa na disku, které lze použít k uložení souboru. Když velikosti souborů nejdou na sudý násobek velikosti clusteru, je potřeba použít další místo pro uložení souboru do další násobky velikosti clusteru.

Azure File Sync se podporuje na svazcích NTFS s Windows Serverem 2012 R2 a novějším. Následující tabulka popisuje výchozí velikosti clusterů při vytváření nového svazku NTFS. 

|Velikost svazku    |Windows server 2012R2 a novější |
|---------------|---------------|
|7 MB – 16 TB   | 4 kB          |
|16TB – 32 TB   | 8 kB          |
|32 TB – 64 TB   | 16 kB         |
|64 TB – 128 TB  | 32 KB         |
|128TB – 256 TB | 64 kB         |
|> 256 TB       | Nepodporováno |

Při vytváření svazku je možné ručně naformátovat svazek s jinou velikostí clusteru. Pokud svazek vychází ze starší verze Windows, můžou se výchozí velikosti clusterů lišit také. [Tento článek obsahuje další podrobnosti o výchozích velikostech clusteru.](https://support.microsoft.com/help/140365/default-cluster-size-for-ntfs-fat-and-exfat) I v případě, že zvolíte velikost clusteru menší než 4 KB, limit 8 KB jako nejmenší velikost souboru, který může být vrstvený, bude stále platit. (I v případě, že by technicky 2x velikost clusteru byla rovna méně než 8 KB)

Důvod absolutního minima se nachází v způsobu, jakým systém souborů NTFS ukládá extrémně malé soubory – 1 KB až 4 soubory velikosti KB. V závislosti na dalších parametrech svazku je možné, že malé soubory nejsou uloženy v clusteru na disku vůbec. Je možné, že budete chtít ukládat takové soubory přímo do hlavní tabulky souborů svazku nebo do záznamu MFT. Bod rozboru vrstvy cloudu se vždycky ukládá na disk a zabírá přesně jeden cluster. Vrstvení takových malých souborů může skončit bez úspory místa. V extrémních případech může dokonce dokončit větší místo s povoleným vytvářením vrstev cloudu. Aby se předešlo ochraně, nejmenší velikost souboru, na který se vrstva cloudu bude nacházet, je 8 KB až o 4 KB nebo menší velikost clusteru.

<a id="afs-volume-free-space"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work"></a>Jak fungují zásady vrstvení volného místa svazku?
Volné místo svazku je množství volného místa, které chcete rezervovat na svazku, na kterém je umístěný koncový bod serveru. Pokud je například volné místo na svazku nastavené na 20% na svazku, který má jeden koncový bod serveru, až 80% místa na svazku bude pracovat s naposledy použitými soubory, všechny zbývající soubory, které se do tohoto místa nevejdou na Azure. Volné místo na svazku se vztahuje na úrovni svazku, nikoli na úrovni jednotlivých adresářů nebo skupin synchronizace. 

<a id="volume-free-space-fastdr"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work-with-regards-to-new-server-endpoints"></a>Jak fungují zásady vrstvení volného místa svazku s ohledem na nové koncové body serveru?
Když je koncový bod serveru nově zřízený a připojený ke sdílené složce Azure, server nejdřív tento obor názvů odvolá a potom načte skutečné soubory, dokud nedosáhne prahové hodnoty pro volné místo na svazku. Tento proces se označuje také jako rychlé zotavení po havárii nebo rychlé obnovení oboru názvů.

<a id="afs-effective-vfs"></a>
### <a name="how-is-volume-free-space-interpreted-when-i-have-multiple-server-endpoints-on-a-volume"></a>Jak se interpretuje volné místo svazku, pokud je na něm několik koncových bodů serveru?
Pokud je na svazku více než jeden koncový bod serveru, je prahová hodnota platného volného místa svazku největší volné místo, které je zadáno v libovolném koncovém bodu serveru na daném svazku. Soubory budou vrstveny podle jejich vzorů použití bez ohledu na to, který koncový bod serveru patří. Například pokud máte dva koncové body serveru na svazku, Endpoint1 a pro endpoint2 u, kde Endpoint1 má prahovou hodnotu pro volné místo 25% a pro endpoint2 u má prahovou hodnotu pro volné místo na svazku 50%, bude prahová hodnota volného místa svazku pro oba koncové body serveru 50%. 

<a id="date-tiering-policy"></a>
### <a name="how-does-the-date-tiering-policy-work-in-conjunction-with-the-volume-free-space-tiering-policy"></a>Jak fungují zásady vrstvení podle data ve spojení se zásadami vrstvení volného místa svazku? 
Při povolování vrstvení cloudu na koncovém bodu serveru se nastavuje zásada pro volné místo svazku. Vždycky má přednost před všemi ostatními zásadami, včetně zásad data. Volitelně můžete pro každý koncový bod serveru na tomto svazku povolit zásady pro data. Tato zásada spravuje, že v rámci rozsahu dnů se budou tyto zásady uchovávat jenom v případě, že jsou k dispozici pouze soubory (které jsou, přečteny nebo zapsány do). Soubory, které nejsou v zadaném počtu dnů k dispozici, budou vrstveny. 

Vrstvení cloudu využívá čas posledního přístupu k určení, které soubory by měly být vrstveny. Ovladač filtru vrstvy cloudu (storagesync.sys) sleduje čas posledního přístupu a zapisuje informace do úložiště s navýšenou úrovní cloudu. Můžete načíst tepelný obchod a uložit ho do souboru CSV pomocí rutiny prostředí PowerShell pro místní server.

```powershell
# There is a single heat store for files on a volume / server endpoint / individual file.
# The heat store can get very large. If you only need to retrieve the "coolest" number of items, use -Limit and a number

# Import the PS module:
Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'

# VOLUME FREE SPACE: To get the order in which files will be tiered using the volume free space policy:
Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName LastAccessTimeWithSyncAndTieringOrder

# DATE POLICY: To get the order in which files will be tiered using the date policy:
Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName LastAccessTimeWithSyncAndTieringOrderV2

# Find the heat store information for a particular file:
Get-StorageSyncHeatStoreInformation -FilePath '<PathToSpecificFile>'
```

> [!IMPORTANT]
> Poslední přístup – časové razítko není vlastností sledovanými systémem souborů NTFS, takže ve výchozím nastavení není v Průzkumníkovi souborů viditelné. Pro soubor nepoužívejte časové razítko Naposledy změněno-a ověřte, zda zásada data funguje podle očekávání. Toto časové razítko sleduje pouze zápisy, nikoli čtení. Použijte rutinu zobrazenou k získání posledního přístupového časového razítka pro toto vyhodnocení.

> [!WARNING]
> Nezapněte funkci NTFS pro sledování posledního použití časového razítka pro soubory a složky. Tato funkce je ve výchozím nastavení vypnutá, protože má velký dopad na výkon. Azure File Sync bude sledovat časy posledního otevření automaticky a velmi efektivně a tuto funkci NTFS nevyužívá.

Mějte na paměti, že zásady volného místa na svazku vždy mají přednost a když na svazku není dost volného místa, abyste zachovali tolik dní, kolik se bude považovat za soubor, který je popsaný v zásadách data, Azure File Sync bude pokračovat ve vrstvení souborů coldest, dokud nedosáhne procento volného místa na svazku.

Řekněme například, že máte zásady vrstvení založené na datu 60 dnů a zásady volného místa svazku o 20%. Pokud se po použití zásady data na tomto svazku nachází méně než 20% volného místa, zásada pro volné místo svazku se zahájí a přepíše zásadu data. Výsledkem bude, že se zaměří víc souborů, takže objem dat uchovávaných na serveru se může snížit z 60 dnů od data do 45 dnů. Naopak tato zásada vynutí vrstvení souborů, které spadají mimo váš časový rozsah, a to i v případě, že jste nedosáhli prahové hodnoty pro volné místo, takže soubor, který je 61 dnů staré, bude vrstvený i v případě, že je svazek prázdný.

<a id="volume-free-space-guidelines"></a>
### <a name="how-do-i-determine-the-appropriate-amount-of-volume-free-space"></a>Jak určím vhodnou velikost volného místa svazku?
Množství dat, které byste měli zachovat v místním prostředí, je určené několika faktory: vaše šířka pásma, vzor přístupu k datové sadě a váš rozpočet. Pokud máte připojení s malou šířkou pásma, možná budete chtít zachovat místní data a zajistit tak minimální prodlevu pro uživatele. V opačném případě je můžete v daném období založit na míru četnosti změn. Pokud například víte, že přibližně 10% z 1 TB změn datových sad nebo se k němu aktivně přistupovalo, budete možná chtít zachovat 100 GB místní, abyste nemuseli často odvolat soubory. Pokud je svazek 2 TB, budete chtít zachovat 5% (nebo 100 GB) místní, což znamená, že zbývající 95% je procento volného místa na svazku. Doporučujeme však přidat vyrovnávací paměť pro účty s vyššími změnami – jinými slovy, počínaje nižším procentem volného místa na svazku a následně upravit v případě potřeby později. 

Udržování většího množství dat znamená nižší náklady na výstup, protože méně souborů bude vráceno z Azure, ale také vyžaduje, abyste zachovali větší množství místních úložišť, která se týkají vlastní ceny. Jakmile máte nanasazenou instanci Azure File Sync, můžete si prohlédnout výstup svého účtu úložiště a zhruba tak posoudit, jestli jsou nastavení volného místa na svazku vhodná pro vaše využití. Za předpokladu, že účet úložiště obsahuje jenom váš koncový bod cloudu Azure File Sync (tj. vaše synchronizační sdílená složka), pak vysoký výstup znamená, že se z cloudu odvolají mnoho souborů, a měli byste zvážit zvýšení místní mezipaměti.

<a id="how-long-until-my-files-tier"></a>
### <a name="ive-added-a-new-server-endpoint-how-long-until-my-files-on-this-server-tier"></a>Přidal (a) jsem nový koncový bod serveru. Jak dlouho do má být moje soubory na této vrstvě serveru?

Bez ohledu na to, jestli je potřeba soubory rozvrstvit na nastavené zásady, se vyhodnotí jednou za hodinu. Při vytvoření nového koncového bodu serveru můžete narazit na dvě situace:

1. Když přidáte nový koncový bod serveru, pak často existují soubory v tomto umístění serveru. Před zahájením vrstvení cloudu je potřeba nejdřív nahrávat. Zásada volného místa svazku nezahájí svou práci, dokud se nedokončí počáteční nahrávání všech souborů. Volitelné zásady kalendářních dat ale začnou pracovat na jednotlivých souborech, jakmile se soubor nahraje. V tomto případě platí také interval hodin. 
2. Když přidáte nový koncový bod serveru, je možné připojit prázdné umístění serveru ke sdílené složce Azure s Vašimi daty. Určuje, jestli je to pro druhý server nebo v případě zotavení po havárii. Pokud se rozhodnete stáhnout obor názvů a odvolat obsah během počátečního stahování na váš server, pak se soubory znovu vrátí na základě posledního upravovaného časového razítka. V rámci zásad pro volné místo svazku a volitelné zásady data budou znovu zavolána pouze tolik souborů.

<a id="is-my-file-tiered"></a>
### <a name="how-can-i-tell-whether-a-file-has-been-tiered"></a>Jak zjistím, jestli byl soubor vrstvený?
Existuje několik způsobů, jak ověřit, zda byl soubor vrstven do sdílené složky Azure:
    
   *  **Ověřte atributy souboru v souboru.**
     Klikněte pravým tlačítkem na soubor, přejděte na **Podrobnosti**a přejděte dolů k vlastnosti **atributy** . Vrstvený soubor má následující sady atributů:     
        
        | Písmeno atributu | Atribut | Definice |
        |:----------------:|-----------|------------|
        | A | Archiv | Indikuje, že by měl být soubor zálohovaný zálohovacím softwarem. Tento atribut je vždy nastaven bez ohledu na to, zda je soubor na disku povrstvený nebo uložený jako plný. |
        | P | Zhuštěný soubor | Označuje, že se jedná o zhuštěný soubor. Zhuštěný soubor je specializovaný typ souboru, který systém souborů NTFS nabízí pro efektivní použití v případě, že je soubor na diskovém streamu většinou prázdný. Azure File Sync používá zhuštěné soubory, protože soubor je buď úplně vrstven, nebo částečně odvolán. V plně vrstveném souboru je datový proud souboru uložený v cloudu. V částečně vráceném souboru je tato část souboru již na disku. Pokud je soubor zcela znovu volán na disk, Azure File Sync jej převede ze zhuštěného souboru do normálního souboru. Tento atribut je nastaven pouze v systémech Windows Server 2016 a starších.|
        | Ú | Odvolat při přístupu k datům | Indikuje, že data souboru nejsou plně přítomná v místním úložišti. Při čtení souboru dojde k tomu, že se alespoň část obsahu souboru načte ze sdílené složky Azure, ke které je připojený koncový bod serveru. Tento atribut je nastaven pouze v systému Windows Server 2019. |
        | L | Spojovací bod | Označuje, že soubor obsahuje bod rozboru. Bod rozboru je speciální ukazatel pro použití filtrem systému souborů. Azure File Sync používá spojovací body k definování do Azure File Syncho filtru systému souborů (StorageSync.sys) umístění v cloudu, kde je soubor uložený. To podporuje bezproblémový přístup. Uživatelé nebudou muset znát, že se používá Azure File Sync nebo jak získat přístup k souboru ve sdílené složce Azure. Když je soubor zcela znovu vyvolán, Azure File Sync odebere bod rozboru ze souboru. |
        | O | Offline | Indikuje, že některé nebo všechny obsahy souboru nejsou uložené na disku. Když je soubor zcela znovu vyvolán, Azure File Sync tento atribut odstraní. |

        ![Dialogové okno Vlastnosti souboru s vybranou kartou podrobnosti](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Atributy pro všechny soubory ve složce můžete zobrazit tak, že přidáte pole **atributy** do zobrazení tabulky v Průzkumníkovi souborů. Provedete to tak, že kliknete pravým tlačítkem na existující sloupec (například **Velikost**), vyberete **Další**a v rozevíracím seznamu vyberete **atributy** .
        
   * **Slouží `fsutil` ke kontrole bodů rozboru v souboru.**
       Jak je popsáno v předchozí možnosti, vrstvený soubor má vždy nastaven bod rozboru. Ukazatel rozboru je speciální ukazatel pro Azure File Sync filtr systému souborů (StorageSync.sys). Pokud chcete zjistit, jestli má soubor bod rozboru, v příkazovém řádku se zvýšenými oprávněními nebo v okně PowerShellu spusťte `fsutil` Nástroj:
    
        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```

        Pokud má soubor bod rozboru, můžete očekávat, že se zobrazí **hodnota značky rozboru: 0x8000001E**. Tato hexadecimální hodnota je hodnota bodu rozboru, která je vlastněna Azure File Sync. Výstup také obsahuje data opětovného zpracování, která představují cestu k souboru ve sdílené složce Azure.

        > [!WARNING]  
        > `fsutil reparsepoint`Příkaz nástroje má také možnost odstranit spojovací bod. Nespouštějte tento příkaz, pokud vám Azure File Sync technický tým nežádá. Spuštění tohoto příkazu může způsobit ztrátu dat. 

<a id="afs-recall-file"></a>
### <a name="a-file-i-want-to-use-has-been-tiered-how-can-i-recall-the-file-to-disk-to-use-it-locally"></a>Soubor, který chcete použít, byl vrstven. Jak mohu soubor odvolat na disk a použít ho místně?
Nejjednodušší způsob, jak na disk navrátit soubor, je otevřít soubor. Azure File Sync filtr systému souborů (StorageSync.sys) bezproblémově stáhne soubor ze sdílené složky Azure, aniž by byla na vaší straně potřeba žádná práce. Pro typy souborů, ze kterých se dá částečně číst, například multimédia nebo soubory zip, otevření souboru nestáhne celý soubor.

Můžete také použít PowerShell k vynucení souboru, který se má odvolat. Tato možnost může být užitečná, pokud chcete odvolat více souborů najednou, například všechny soubory ve složce. Otevřete relaci PowerShellu pro uzel serveru, na kterém je nainstalovaná Azure File Sync, a pak spusťte následující příkazy PowerShellu:
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
Volitelné parametry:
* `-Order CloudTieringPolicy` Nejdřív odvolá naposledy změněné nebo dostupné soubory a povolí aktuální zásady vrstvení. 
    * Pokud je nakonfigurovaná zásada volného místa svazku, budou se soubory volat, dokud nedosáhnete nastavení zásad pro volné místo svazku. Pokud je například nastavení zásad Free o svazku 20%, bude odvolání zastaveno, jakmile volné místo na svazku dosáhne 20%.  
    * Pokud je nakonfigurované volné místo na svazku a data zásad, budou se soubory volat, dokud nedosáhnete nastavení pro volné místo na svazku nebo pro datum. Pokud má například nastavení zásad Free hodnotu 20% a zásada data je 7 dní, pak se odvolání zastaví, jakmile velikost volného místa na svazku dosáhne 20% nebo jsou všechny soubory, ke kterým se přistupovalo nebo upraveno, do 7 dnů, místní.
* `-ThreadCount` Určuje, kolik souborů lze paralelně volat.
* `-PerFileRetryCount`Určuje, jak často se bude opakovat pokus o odvolání souboru, který je aktuálně blokován.
* `-PerFileRetryDelaySeconds`Určuje dobu v sekundách mezi opakovanými pokusy o odvolání a měla by být vždy použita v kombinaci s předchozím parametrem.

Příklad:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -ThreadCount 8 -Order CloudTieringPolicy -PerFileRetryCount 3 -PerFileRetryDelaySeconds 10
``` 

> [!Note]  
> - Rutinu Invoke-StorageSyncFileRecall lze také použít ke zlepšení výkonu stahování souborů při přidávání nového koncového bodu serveru do existující skupiny synchronizace.  
>- Pokud místní svazek, který hostuje server, nemá dost volného místa pro odvolání všech vrstvených dat, `Invoke-StorageSyncFileRecall` rutina se nezdařila.  

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>Proč velikost vlastnosti *disku* pro soubor neodpovídá vlastnosti *size* po použití Azure File Sync? 
Průzkumník souborů systému Windows zpřístupňuje dvě vlastnosti, které reprezentují velikost souboru: **Velikost** a **Velikost na disku**. Tyto vlastnosti se v takovém smyslu liší. **Velikost** představuje úplnou velikost souboru. **Velikost na disku** představuje velikost datového proudu souboru uloženého na disku. Hodnoty těchto vlastností se mohou lišit z nejrůznějších důvodů, jako je komprese, použití odstranění duplicitních dat nebo vrstvení cloudu s Azure File Sync. Pokud je soubor vrstvený ve sdílené složce Azure, velikost na disku je nula, protože datový proud souboru je uložený ve sdílené složce Azure, a ne na disku. Je také možné, že se soubor částečně vrstvený (nebo částečně odvolal). V částečně vrstveném souboru je část souboru na disku. K tomu může dojít, když jsou soubory částečně čteny aplikacemi, jako jsou multimediální přehrávače nebo nástroje zip. 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>Návody vynutit vrstvení souboru nebo adresáře?

> [!NOTE]
> Když vyberete adresář, který má být vrstven, budou vrstveny pouze soubory, které jsou nyní v adresáři. Všechny soubory vytvořené po této době nejsou automaticky vrstveny.

Když je povolená funkce vrstvení cloudu, vrstva cloudu automaticky rozchází soubory na základě posledního přístupu a doby úprav, aby se dosáhlo procenta volného místa na svazku, které je zadané na koncovém bodu cloudu. Někdy ale můžete chtít ručně vynutit soubor do vrstvy. To může být užitečné, pokud uložíte velký soubor, který nechcete znovu použít po dlouhou dobu, a chcete, aby volné místo na svazku bylo teď k dispozici pro jiné soubory a složky. Vrstvení můžete vynutit pomocí následujících příkazů PowerShellu:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

<a id="afs-image-thumbnail"></a>
### <a name="why-are-my-tiered-files-not-showing-thumbnails-or-previews-in-windows-explorer"></a>Proč mé vrstvené soubory neobsahují miniatury nebo náhledy v Průzkumníkovi Windows?
V případě vrstvených souborů se miniatury a verze Preview nebudou zobrazovat na koncovém bodu serveru. Toto chování je očekávané, protože funkce mezipaměti miniatur ve Windows záměrně přeskočí čtení souborů s atributem offline. Díky povoleným vrstvám cloudu by čtení přes vrstvených souborů způsobilo stažení (vráceno).

Toto chování není specifické pro Azure File Sync, Průzkumník Windows zobrazuje "šedou X" pro všechny soubory, které mají nastaven atribut offline. Při přístupu k souborům přes SMB se zobrazí ikona X. Podrobné vysvětlení tohoto chování najdete v tématu. [https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105](https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105)

<a id="afs-tiering-disabled"></a>
### <a name="i-have-cloud-tiering-disabled-why-are-there-tiered-files-in-the-server-endpoint-location"></a>Mám zakázanou vrstvu cloudu, proč jsou v umístění koncového bodu serveru umístěny soubory?

Existují dva důvody, proč v umístění koncového bodu serveru mohou existovat vrstvené soubory:

- Při přidávání nového koncového bodu serveru do existující skupiny synchronizace se metadata nejprve synchronizují se serverem a soubory se pak stáhnou do serveru na pozadí. Soubory se budou zobrazovat tak, jak budou vrstveny, dokud se nestáhnou místně. Chcete-li zlepšit výkon stahování souborů při přidávání nového serveru do skupiny synchronizace, použijte rutinu [Invoke-StorageSyncFileRecall](storage-sync-cloud-tiering.md#afs-recall-file) .

- Pokud byla na koncovém bodu serveru povolená vrstva cloudu a pak se zakázala, soubory zůstanou vrstveny, dokud nebudou přístupné.


## <a name="next-steps"></a>Další kroky
* [Plánování nasazení Azure File Sync](storage-sync-files-planning.md)
