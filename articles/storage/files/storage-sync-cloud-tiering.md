---
title: Principy Azure File Sync vrstvení cloudu | Dokumentace Microsoftu
description: Další informace o Azure File Sync funkce vrstvení cloudu
services: storage
author: sikoo
ms.service: storage
ms.topic: article
ms.date: 09/21/2018
ms.author: sikoo
ms.subservice: files
ms.openlocfilehash: e73a11d7849d6e304be0844a55ddad46e6966f6e
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55470446"
---
# <a name="cloud-tiering-overview"></a>Přehled vrstvení cloudu
Cloud ovládání datových vrstev je volitelná funkce služby Azure File Sync, ve kterém často používaných souborů jsou uložené v mezipaměti místně na serveru při další souborů proběhne do služby soubory Azure na základě nastavení zásad. Pokud se vrstvený soubor, filtr systému souborů Azure File Sync (StorageSync.sys) nahradí soubor místně ukazatel nebo spojovacím bodem. Bod rozboru představuje adresu URL k souboru ve službě soubory Azure. Vrstvený soubor má atribut "offline" a FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS atributu nastavit v systému souborů NTFS tak, aby aplikace třetích stran můžou bezpečně identifikovat vrstvené soubory.
 
Když uživatel otevře vrstvených sdílených, Azure File Sync bezproblémově vrátí data souboru z soubory Azure bez uživatele nepotřebuje vědět, že je soubor uložený ve skutečnosti v Azure. 
 
 > [!Important]  
    > Důležité: Cloud ovládání datových vrstev se nepodporuje pro koncové body serveru u svazků systému Windows a pouze soubory, které jsou větší než 64 KiB velikosti může být rozvrstvena do služby soubory Azure.
    
Azure File Sync nepodporuje ani soubory menší než 64 KiB by ovládání datových vrstev a vrací tyto soubory malé nároky na výkon převažují nad úspory místa na disku.

## <a name="cloud-tiering-faq"></a>Nejčastější dotazy týkající se vrstvení cloudu

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>Jak práce vrstvení cloudu?
Filtr systému Azure File Sync sestavení "heatmapu" váš obor názvů na každém koncovém bodu serveru. Monitoruje přístupy (operací čtení a zápisu) v čase a pak podle frekvence a vlastnost přístup, přiřadí a heat skóre. pro každý soubor. Častým soubor, který byl naposledy otevřen se budou považovat za horké, že soubor, který je i neziskovky dokázal a nepřistupovalo nechystáte nějakou dobu se budou považovat za nástrojů. Když svazek souboru na serveru je větší než prahová hodnota volného místa svazku, které jste nastavili, ji budou vrstvy nejatraktivnější soubory do služby soubory Azure až do splnění vašich procento volného místa.

Do verze 4.0 a vyšší než agenta Azure File Sync, můžete navíc určit datum zásady na každém koncovém bodu serveru, který bude úroveň všechny soubory nejsou otevřeny nebo upraveny v rámci zadaného počtu dnů.

<a id="afs-volume-free-space"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work"></a>Jak funguje zásad vrstvení svazku volné místo?
Volné místo na svazku se množství volného místa, které chcete rezervovat na svazku, na kterém je umístěn koncový bod serveru. Například pokud volné místo na svazku je nastaven na 20 % na svazku, který má jeden koncový bod serveru, až 80 % místa na disku, bude pravděpodobně obsazena naposledy použitých souborů s všechny zbývající soubory, které se nehodí do tohoto prostoru vrstveného do Azure. Volné místo na svazku se vztahuje na úrovni svazku, nikoli na úrovni jednotlivých adresářů nebo skupiny synchronizace. 

<a id="volume-free-space-fastdr"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work-with-regards-to-new-server-endpoints"></a>Jak funguje zásady tvorby vrstev volné místo svazku s ohledem na nové koncové body serveru?
Pokud koncový bod serveru je nově zřízený a připojené sdílené složky Azure, server se nejprve stáhne obor názvů a potom stáhne skutečné souborech dokud nedosáhne prahové hodnoty volného místa na svazku. Tento proces je také rychlé zotavení po havárii nebo obor názvů rychlé obnovení.

<a id="afs-effective-vfs"></a>
### <a name="how-is-volume-free-space-interpreted-when-i-have-multiple-server-endpoints-on-a-volume"></a>Volné místo na svazku je interpretován jak když mám několik koncových bodů serveru na svazku
Pokud existuje více než jeden koncový bod serveru na svazku, prahová hodnota volného místa efektivní svazku je největší volné místo svazku zadat napříč libovolný koncový bod serveru na tomto svazku. Soubory se vrstvený podle jejich bez ohledu na to, které koncový bod serveru, ke kterému patří vzorce používání. Například pokud máte dva koncové body serveru na svazku, koncovém bodě 1 a Endpoint2, kde koncovém bodě 1 má prahová hodnota volného místa svazek o 25 % Endpoint2 má prahová hodnota volného místa na svazku 50 %, prahová hodnota volného místa na svazku pro oba koncové body serveru to 50 %. 

<a id="date-tiering-policy"></a>
### <a name="how-does-the-date-tiering-policy-work-in-conjunction-with-the-volume-free-space-tiering-policy"></a>Jak zásady tvorby vrstev datum funguje ve spojení s volné místo svazku ovládání datových vrstev na zásady? 
Při povolení na koncovém bodu serveru vrstvení cloudu, můžete nastavit zásady volné místo svazku. Vždy má přednost před všechny ostatní zásady, včetně zásad datum. Volitelně můžete povolit zásady pro každý koncový bod serveru na, že svazek, což znamená, že přistupovat pouze soubory (které je číst nebo zapisovat do) v rozsahu dní, po které tato zásada popisuje zůstanou zachovaná, místní, se všechny soubory staler vrstvené datu. Mějte na paměti, že vždy má přednost zásada na volné místo na svazku a pokud není k dispozici dostatek volného místa na svazku pro zachování tolik několikadenní souborů, jak je popsáno v zásadách datum, Azure File Sync bude pokračovat ovládání datových vrstev na Nejchladnější souborů až do svazku zdarma procento prostoru je splněna.

Řekněme například, že máte 60 dnů Snazším vrstvení zásady a zásady 20 % volného místa svazku. Pokud po použití zásad pro data, je menší než 20 % volného místa na svazku, budou zásady volné místo svazku rozjíždí a přepsat zásady datum. Výsledkem bude více souborů se vrstvený, tak, že objem dat, které jsou uloženy na serveru může být kratší než 60 dnů od data do 45 dnů. Naopak tato zásada vynutí, ovládání datových vrstev soubory, které spadají mimo časový rozsah, i v případě, že prahová hodnota volného místa – nebyly přístupů, tak soubor, který je 61 dnů se vrstvený, i v případě, že svazek je prázdný.

<a id="volume-free-space-guidelines"></a>
### <a name="how-do-i-determine-the-appropriate-amount-of-volume-free-space"></a>Jak zjistím odpovídající množství volné místo na svazku?
Objem dat byste měli mít místní je určeno několik faktorů: šířky pásma, vzor přístupu k vaší datové sady a vašeho rozpočtu. Pokud máte připojení s malou šířkou pásma, můžete chtít zachovat informace z vašich dat místní Ujistěte se, že je minimální prodleva pro vaše uživatele. V opačném případě můžete založit ho na četnost změn dat během daného období. Například pokud vědět, že přibližně 10 % 1 TB datovou sadu změn nebo přistupuje aktivně každý měsíc, pak můžete chtít zachovat 100 GB místní tak můžete nejsou navrácení často souborů. Pokud je 2TB svazku, pak budete chtít zachovat 5 % (nebo 100 GB) je místní, což znamená, zbývající 95 % vaše Procento volné místo svazku. Doporučujeme však, že přidáte do vyrovnávací paměti pro období vyšší četnost změn – jinými slovy, počínaje nižší procento volného místa na svazku a upravit ho v případě potřeby později. 

Udržování víc dat místní znamená nižší poplatky za výchozí přenos dat, jako třeba připomenout menší počet souborů z Azure, ale také vyžaduje, abyste udržovat větší velikost místního úložiště, který se dodává své vlastní poplatků. Jakmile budete mít instanci služby Azure File Sync nasadili, můžete si prohlédnout výchozí přenos vašeho účtu úložiště do zhruba odhadnout, jestli jsou vhodné pro vaše použití nastavení volné místo svazku. Za předpokladu, že účet úložiště obsahuje pouze Azure souboru synchronizace cloudu koncový bod služby (to znamená, že vaše synchronizační sdílenou složku) a potom vysokou odchozího přenosu dat znamená, že mnoho souborů jsou navrácení z cloudu a měli byste zvážit zvýšení místní mezipaměti.

<a id="how-long-until-my-files-tier"></a>
### <a name="ive-added-a-new-server-endpoint-how-long-until-my-files-on-this-server-tier"></a>Přidali jsme nový koncový bod serveru. Jak dlouho až do své soubory na tuto úroveň serveru?
Do verze 4.0 a nad agenta Azure File Sync, jakmile se vaše soubory nahrály do sdílené složky Azure, jejich bude vrstvený podle vašich zásad co nejdříve další vrstvení spuštění relace, které se stane, jednou za hodinu. Na starší agenti ovládání datových vrstev může trvat až 24 hodin, které se provedou.

<a id="is-my-file-tiered"></a>
### <a name="how-can-i-tell-whether-a-file-has-been-tiered"></a>Jak zjistit, jestli se vrstvený soubor?
Existuje několik způsobů, jak zkontrolovat, zda byl soubor Vrstvená do sdílené složky Azure:
    
   *  **Zkontrolujte soubor atributů souboru.**
     Klikněte pravým tlačítkem na soubor, přejděte na **podrobnosti**a potom přejděte dolů k položce **atributy** vlastnost. Vrstvený soubor má následující atributy nastavit:     
        
        | Atribut písmeno | Atribut | Definice |
        |:----------------:|-----------|------------|
        | A | Archiv | Označuje, že soubor by měl být zálohovány zálohovací software. Tento atribut je vždycky nastavený, bez ohledu na to, zda je soubor vrstvené nebo plně uložená na disku. |
        | P | Chování řídkého souboru | Označuje, zda je soubor zhuštěného souboru. Chování řídkého souboru je speciální typ souboru, který nabízí systému souborů NTFS pro efektivní využití v datovém proudu disku se většinou prázdný. Azure File Sync používá zhuštěných souborů, protože plně vrstvené nebo částečně připomenout souboru. V souboru plně vrstvené datový proud souboru uložená v cloudu. V částečně odvolaného souboru, který je součástí souboru již na disku. Pokud je soubor plně připomenout, na disk, Azure File Sync převede z zhuštěného souboru na regulárním souborem. |
        | L | Spojovací bod | Označuje, že soubor obsahuje bod rozboru. Bod opakování analýzy je speciální ukazatele pro použití ve filtru systému souborů. Azure File Sync používá spojovací body k definování filtru systému souborů Azure File Sync (StorageSync.sys) cloudových umístění, ve které je uložený soubor. Tento atribut podporuje bezproblémový přístup. Uživatelé nebudou muset vědět, jestli se používá Azure File Sync nebo jak získat přístup k souboru ve sdílené složky Azure. Když souboru je plně připomenout, Azure File Sync odebere ze souboru spojovacím bodem. |
        | O | Offline | Označuje, že některé nebo všechny obsah souboru není uložený na disk. Azure File Sync souboru je plně připomenout, odebere tento atribut. |

        ![Dialogové okno Vlastnosti souboru, s vybranou kartou podrobnosti](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Atributy pro všechny soubory ve složce můžete zobrazit tak, že přidáte **atributy** do zobrazení tabulky v Průzkumníku souborů. Chcete-li to provést, klikněte pravým tlačítkem na existující sloupec (třeba **velikost**) vyberte **Další**a pak vyberte **atributy** z rozevíracího seznamu.
        
   * **Použití `fsutil` ke kontrole spojovacích bodů u souboru.**
       Jak je popsáno v předchozí možnosti, vrstvený soubor má vždy spojovacím bodu sady. Ukazatel rozboru je speciální ukazatel pro filtr systému souborů Azure File Sync (StorageSync.sys). Chcete-li zkontrolovat, zda má soubor spojovacím bodem, v okně příkazového řádku nebo Powershellu se zvýšenými oprávněními, spusťte `fsutil` nástroje:
    
        ```PowerShell
        fsutil reparsepoint query <your-file-name>
        ```

        Pokud má soubor spojovacím bodem, můžete očekávat zobrazíte **rozboru hodnota značky: 0x8000001e**. Tato šestnáctková hodnota je hodnota spojovací bod, který je vlastněn Azure File Sync. Výstup obsahuje také rozboru data, která představuje cestu k souboru na sdílené složky Azure.

        > [!WARNING]  
        > `fsutil reparsepoint` Nástroj příkaz má také možnost Odstranit bod rozboru. Pokud technického týmu Azure File Sync vás vyzve k není provedení tohoto příkazu. Spuštění tohoto příkazu může dojít ke ztrátě. 

<a id="afs-recall-file"></a>

### <a name="a-file-i-want-to-use-has-been-tiered-how-can-i-recall-the-file-to-disk-to-use-it-locally"></a>Soubor, který chcete použít zřízeny vrstvené. Jak můžete odvolat soubor na disk používat místně?
Nejjednodušší způsob, jak si možná Vzpomínáte soubor na disk se k otevření souboru. Soubor filtru systému souborů Azure File Sync (StorageSync.sys) bezproblémově stáhne ze sdílené složky Azure bez nutnosti dělat něco z vaší strany. Pro typy souborů, které může být částečně není pro čtení, jako jsou například soubory multimediálních souborů nebo ZIP, otevřete soubor stáhnout celý soubor.

Prostředí PowerShell můžete použít také k vynucení soubor, který chcete zrušit. Tato možnost může být užitečné, pokud chcete odvolat více souborů najednou, jako je například všechny soubory ve složce. Otevřete relaci Powershellu k uzlu serveru, kde je nainstalovaný Azure File Sync a pak spusťte následující příkazy Powershellu:
    
    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
    ```

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>Proč není *velikost na disku* vlastnost souboru shody *velikost* vlastnost po použití Azure File Sync? 
Průzkumníka souborů Windows poskytuje dvě vlastnosti k reprezentaci velikosti souboru: **Velikost** a **velikost na disku**. Tyto vlastnosti se mírně liší v význam. **Velikost** představuje dokončení velikost souboru. **Velikost na disku** představuje velikost souboru datového proudu, který je uložený na disku. Hodnoty těchto vlastností můžete se liší z různých důvodů, jako je komprese, použít odstranění duplicitních dat nebo pomocí služby Azure File Sync vrstvení cloudu. Pokud je soubor Vrstvená do sdílené složky Azure, velikost na disku je nula, protože datový proud souboru je uložené v adresáři sdílené složky Azure a nikoli na disk. Je také možné pro soubor, který chcete být částečně vrstvené (nebo částečně odvolaného). V částečně vrstvený soubor je část souboru na disku. Tato situace může nastat, když jsou částečně číst pomocí aplikací, jako je multimediálního přehrávače nebo zip nástroje souborů. 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>Jak vynutit soubor nebo adresář vrstvený?
Pokud je povolena funkce výběru vrstvy na cloud, vrstvení automaticky soubory úrovních cloudu založený na poslední přístup a upravit dobu k dosažení Procento volné místo svazku na koncový bod cloudu. V některých případech však můžete chtít ručně vynutit soubor k vrstvě. To může být užitečné, pokud uložíte s velkými soubory, které nechcete použít znovu po dlouhou dobu a požadované volné místo na svazku teď má použít pro jiné soubory a složky. Můžete vynutit ovládání datových vrstev na následující příkazy Powershellu:

    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
    ```

## <a name="next-steps"></a>Další kroky
* [Plánování nasazení služby Azure File Sync](storage-sync-files-planning.md)
