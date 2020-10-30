---
title: Mapování struktury složek na Azure File Sync topologii
description: Namapujte existující strukturu souborů a složek na sdílené složky Azure pro použití s Azure File Sync. Společný textový blok sdílený v rámci migračních dokumentů.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 441632ea33195ff8bcb6da5f4fb2298c337a6c97
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043158"
---
V tomto kroku si vyhodnocujete, kolik sdílených složek Azure potřebujete. Jedna instance nebo cluster Windows serveru může synchronizovat až 30 sdílených složek Azure.

Můžete mít více složek na svazcích, které aktuálně sdílíte místně jako sdílené složky SMB pro uživatele a aplikace. Nejjednodušším způsobem, jak tento scénář naformátovat, je předpostupovat místní sdílenou složku, která mapuje 1:1 na sdílenou složku Azure. Pokud máte pro jednu instanci Windows serveru malý dostatečný počet, doporučujeme vám mapování 1:1.

Pokud máte více sdílených složek než 30, často není nutné namapovat místní sdílenou složku 1:1 na sdílenou složku Azure. Vezměte v úvahu následující možnosti.

#### <a name="share-grouping"></a>Sdílet seskupení

Například pokud má oddělení lidských zdrojů (HR) celkem 15 sdílených složek, můžete zvážit ukládání všech údajů o lidských zdrojích do jedné sdílené složky Azure. Uložení více místních sdílených složek v jedné sdílené složce Azure vám nebrání v vytváření obvyklých sdílených složek SMB v místní instanci Windows serveru. To znamená, že uspořádáte kořenové složky těchto 15 sdílených složek jako podsložek v rámci společné složky. Pak tuto společnou složku synchronizujete se sdílenou složkou Azure. Tímto způsobem je pro tuto skupinu místních sdílených složek potřeba jenom jedna sdílená složka Azure v cloudu.

#### <a name="volume-sync"></a>Synchronizace svazků

Azure File Sync podporuje synchronizaci kořene svazku se sdílenou složkou Azure. Pokud synchronizujete kořenovou složku, všechny podsložky a soubory budou přejít do stejné sdílené složky Azure.

Synchronizace kořene svazku není vždycky nejlepší odpověď. Synchronizace více umístění přináší výhody. Například v takovém případě pomáhá udržet počet položek snížený na rozsah synchronizace. I když testujeme sdílené složky Azure a Azure File Sync s 100 000 000 položkami (soubory a složkami) na jednu sdílenou složku, osvědčeným postupem je zkusit zachovat číslo nižší než 20 000 000 nebo 30 000 000 v jedné sdílené složce. Nastavení Azure File Sync s menším počtem položek není pro synchronizaci souborů výhodné. Nižší počet položek také přináší podobné scénáře:

* Počáteční prohledávání cloudového obsahu před tím, než se může začít zobrazovat na serveru s povolenou Azure File Sync může být rychlejší.
* Obnovení na straně cloudu ze snímku sdílené složky Azure bude rychlejší.
* Zotavení po havárii místního serveru se může významně zrychlit.
* Změny provedené přímo ve sdílené složce Azure (mimo synchronizaci) se dají detekovat a synchronizovat rychleji.

> [!TIP]
> Pokud si nejste jistí, kolik souborů a složek máte, Projděte si nástroj TreeSize od zaseknutí softwaru GmbH.

#### <a name="a-structured-approach-to-a-deployment-map"></a>Strukturovaný přístup k mapě nasazení

Před nasazením cloudového úložiště v pozdějším kroku je důležité vytvořit mapu mezi místními složkami a sdílenými soubory Azure. Toto mapování pak bude informovat, kolik a které Azure File Sync *synchronizovat prostředky skupiny* , které se budou zřizovat. Skupina synchronizace přiřazuje sdílenou složku Azure a složku na serveru společně a naváže připojení synchronizace.

Pokud chcete učinit rozhodnutí o tom, kolik sdílených složek Azure potřebujete, Projděte si následující omezení a osvědčené postupy. To vám pomůže optimalizovat mapu.

* Server s nainstalovaným agentem Azure File Sync se může synchronizovat s až 30 sdílenými složkami Azure.
* Sdílená složka Azure je nasazená v rámci účtu úložiště. Díky tomu je účet úložiště cílem škálování pro počty výkonu, jako jsou IOPS a propustnost.

  Dvě standardní (ne Premium) sdílené složky Azure můžou teoreticky zvýšit maximální výkon, který může účet úložiště doručovat. Pokud máte v úmyslu k těmto sdíleným složkám připojit jenom Azure File Sync, nevytvoří se v seskupení několika sdílených složek Azure do stejného účtu úložiště problém. Projděte si cíle výkonu sdílené složky Azure, kde najdete hlubší přehled o relevantních metrikách, které byste měli zvážit.

  Pokud plánujete vyzvednutí aplikace do Azure, která bude používat sdílenou složku Azure nativně, možná budete potřebovat více výkonu ze sdílené složky Azure. Pokud je tento typ použití možnost i v budoucnu, je nejlepší mapování sdílené složky Azure na vlastní účet úložiště.
* V jedné oblasti Azure je omezení 250 účtů úložiště na předplatné.

> [!TIP]
> Na základě těchto informací je často nutné seskupit více složek nejvyšší úrovně na svazcích do společného a nového kořenového adresáře. Potom tento nový kořenový adresář a všechny složky, které do něj seskupíte, synchronizujte do jedné sdílené složky Azure. Tento postup vám umožní zůstat v rámci limitu 30 synchronizovaných sdílených složek Azure na jeden server.
>
> Toto seskupení v rámci společného kořene nemá žádný vliv na přístup k vašim datům. Seznamy ACL zůstanou tak, jak jsou. Je třeba upravit pouze jakékoli cesty ke sdílené složce (například sdílené složky SMB nebo NFS), které se změnily na společné kořenové složky serveru. Nic jiného nemění.

> [!IMPORTANT]
> Nejdůležitějším vektorem škálování pro Azure File Sync je počet položek (soubory a složky), které je třeba synchronizovat.

Azure File Sync podporuje synchronizaci až 100 000 000 položek s jednou sdílenou složkou Azure. Toto omezení může být překročeno a zobrazuje pouze informace o tom, co tým Azure File Sync pravidelně testuje.

Osvědčeným postupem je udržovat počet položek na rozsah synchronizace nízké. To je důležitý faktor, který je potřeba vzít v úvahu při mapování složek na sdílené složky Azure. I když testujeme sdílené složky Azure a Azure File Sync s 100 000 000 položkami (soubory a složkami) na jednu sdílenou složku, osvědčeným postupem je zkusit zachovat číslo nižší než 20 000 000 nebo 30 000 000 v jedné sdílené složce. Pokud začnete přesáhnout tato čísla, rozdělte obor názvů na více sdílených složek. Pokud máte zhruba pod těmito čísly, můžete do stejné sdílené složky Azure dál seskupovat více místních sdílených složek. Tento postup vám poskytne prostor pro růst.

V případě potřeby je možné, že sada složek může logicky synchronizovat se stejnou sdílenou složkou Azure (pomocí nového přístupu ke společné kořenové složce zmíněného dříve). Stále ale může být lepší oddělit složky tak, aby se synchronizovaly na dvě místo jedné sdílené složky Azure. Tento přístup můžete použít k udržení počtu souborů a složek na sdílení souborů napříč serverem.

#### <a name="create-a-mapping-table"></a>Vytvoření tabulky mapování

:::row:::
    :::column:::
        [![Příklad tabulky mapování. Stáhněte si následující soubor pro práci a použijte obsah tohoto obrázku.](media/storage-files-migration-namespace-mapping/namespace-mapping.png)](media/storage-files-migration-namespace-mapping/namespace-mapping-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        Použijte kombinaci předchozích konceptů, které vám pomůžou určit, kolik sdílených složek Azure potřebujete, a které části vašich stávajících dat budou mít za následek sdílení souborů Azure.
        
        Vytvořte tabulku, která bude zaznamená vaše myšlenky, abyste na ni mohli v případě potřeby odkazovat. Uspořádání je důležité, protože při zřizování mnoha prostředků Azure v jednom okamžiku může být snadné přijít na podrobnosti o plánu mapování. Chcete-li vytvořit úplné mapování, můžete stáhnout soubor aplikace Microsoft Excel jako šablonu.

[//]: # (HTML se zobrazí jako jediný způsob, jak dosáhnout Přidání vnořené tabulky se dvěma sloupci s analýzou pracovní image a textu nebo hypertextového odkazu na stejný řádek.)

<br>
<table>
    <tr>
        <td>
            <img src="media/storage-files-migration-namespace-mapping/excel.png" alt="Microsoft Excel file icon that helps to set the context for the type of file download for the link next to it.">
        </td>
        <td>
            <a href="https://download.microsoft.com/download/1/8/D/18DC8184-E7E2-45EF-823F-F8A36B9FF240/Azure File Sync - Namespace Mapping.xlsx">Stáhněte šablonu mapování oboru názvů.</a>
        </td>
    </tr>
</table>
    :::column-end:::
:::row-end:::
