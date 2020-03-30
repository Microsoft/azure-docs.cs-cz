---
title: Mapování struktury složek na topologii Synchronizace souborů Azure
description: Mapování existující struktury souborů a složek na sdílené složky Azure pro použití s Azure File Sync. Běžný textový blok sdílený mezi dokumenty migrace.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 25c333aae49bff8d0596d4f5403c18576bf198b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124728"
---
V tomto kroku vyhodnocujete, kolik sdílených složek Azure potřebujete. Jeden Windows Server (nebo cluster) může synchronizovat až 30 sdílených složek Azure.

Na svazcích, které aktuálně sdílíte jako sdílené položky SMB uživatelům a aplikacím, můžete mít více složek. Nejjednodušší by bylo představit si místní sdílenou složku, která by mapovala 1:1 na sdílenou složku Azure. Pokud máte dostatečně malé číslo, pod 30 pro jeden systém Windows Server, doporučujeme mapování 1:1.

Pokud máte více sdílených složek než 30, je často zbytečné mapovat místní sdílenou složku 1:1 na sdílenou složku Azure.
Zvažte následující možnosti:

#### <a name="share-grouping"></a>Seskupení sdílení

Pokud má například vaše oddělení lidských zdrojů celkem 15 sdílených složek, můžete zvážit uložení všech dat hr do jedné sdílené složky Azure. Ukládání více místních sdílených složek v jedné sdílené složce Azure nezabrání ve vytváření obvyklých 15 sdílených složek SMB na místním Windows Serveru. To znamená pouze, že uspořádáte kořenové složky těchto 15 sdílených složek jako podsložky ve společné složce. Potom synchronizovat tuto společnou složku do sdílené složky Azure. Tímto způsobem je pro tuto skupinu místních sdílených složek potřeba jenom jedna sdílená složka Azure v cloudu.

#### <a name="volume-sync"></a>Synchronizace svazku

Azure File Sync podporuje synchronizaci kořenového adresáře svazku do sdílené složky Azure.
Pokud synchronizujete kořenovou složku, všechny podsložky a soubory skončí ve stejné sdílené složce Azure.

Synchronizace kořenového adresáře svazku nebude vždy nejlepší odpovědí. Synchronizace více umístění přináší výhody, takže to pomáhá udržovat nižší počet položek na obor synchronizace. Nastavení Azure File Sync s nižším počtem položek není výhodné jen pro synchronizaci souborů. Nižší počet položek také výhody jiné scénáře, jako jsou:

* obnovení na straně cloudu ze snímku sdílené složky Azure pořízeného jako záloha
* zotavení po havárii místního serveru může výrazně urychlit
* změny provedené přímo ve sdílené složce Azure (mimo synchronizaci) lze zjistit a synchronizovat rychleji

#### <a name="a-structured-approach-to-a-deployment-map"></a>Strukturovaný přístup k mapě nasazení

Před nasazením cloudového úložiště v následujícím kroku je důležité vytvořit mapu mezi místními složkami a sdílenými složkami Azure. Toto mapování pak bude informovat, kolik a které Azure File Sync "sync group" prostředky, které zřídíte. Skupina synchronizace vazby sdílené složky Azure a složky na serveru dohromady a naváže synchronizační připojení.

Chcete-li se rozhodnout, kolik sdílených složek Azure potřebujete, zkontrolujte následující limity a doporučené postupy. To vám pomůže optimalizovat mapu:

* Server s nainstalovaným agentem Azure File Sync se dá synchronizovat až s 30 sdílenými složkami Azure.
* Sdílená složka Azure se nasadí uvnitř účtu úložiště. Díky tomu je účet úložiště cíl škálování pro čísla výkonu, jako jsou vstupně-operace a propustnost. Dvě standardní (ne prémiové) sdílené složky Azure by teoreticky mohly nasytit maximální výkon, který může účet úložiště poskytnout. Pokud máte v plánu připojit jenom Azure File Sync k těmto sdíleným složekm souborů, pak seskupení několika sdílených složek Azure do stejného účtu úložiště nevytváří problém. Zkontrolujte cíle výkonu sdílení souborů Azure pro hlubší přehled o relevantních metrikách, které je třeba zvážit. Pokud máte v plánu na zrušení aplikace do Azure, který bude používat sdílenou složku Azure nativně, pak budete potřebovat větší výkon ze sdílené složky Azure. Pokud je to možné, i v budoucnu, pak mapování sdílené složky Azure na vlastní účet úložiště je nejlepší.
* V jedné oblasti Azure je limit 250 účtů úložiště na jedno předplatné.

> [!TIP]
> S ohledem na tyto informace je často nutné seskupit více složek nejvyšší úrovně na svazcích do společného nového kořenového adresáře. Potom synchronizujete tento nový kořenový adresář a všechny složky, které jste do něj seskupili, do jedné sdílené složky Azure.                                                    

Tato technika umožňuje zůstat v rámci 30 Azure limit synchronizace souborů na server.
Toto seskupení pod společným kořenem nemá žádný vliv na přístup k vašim datům. Vaše akly zůstanou tak, jak jsou, stačí upravit všechny cesty sdílení (například sdílené složky SMB nebo NFS), které můžete mít ve složkách serveru, které jste nyní změnili na společný kořen. Nic jiného se nemění.

Dalším důležitým aspektem Azure File Sync a vyvážený výkon a prostředí je pochopení škálování faktorů pro výkon Azure File Sync. Je zřejmé, že když jsou soubory synchronizovány přes internet, větší soubory trvat déle a šířku pásma pro synchronizaci.

> [!IMPORTANT]
> Nejdůležitější škálovací vektor pro Azure File Sync je počet položek (souborů a složek), které je potřeba synchronizovat.

Azure File Sync podporuje synchronizaci až 100 000 položek do jedné sdílené složky Azure. Toto omezení může být překročena a zobrazuje pouze co azure file sync tým testuje pravidelně.

Je osvědčeným postupem ponechat nízký počet položek na obor synchronizace. Tento aspekt je důležitým faktorem, který je třeba vzít v úvahu při mapování složek na sdílené složky Azure.

I v případě, že ve vaší situaci sada složek můžete logicky synchronizovat do stejné sdílené složky Azure (pomocí nové, společné kořenové složky přístup shora), může být stále lepší přeskupit složky tak, aby synchronizovat do dvou namísto jedné sdílené složky Azure. Tento přístup lze použít k udržení počtu souborů a složek na sdílené složky vyvážený na serveru.

#### <a name="create-a-mapping-table"></a>Vytvoření tabulky mapování

:::row:::
    :::column:::
        [![](media/storage-files-migration-namespace-mapping/namespace-mapping.png "An example of a mapping table. Download the file below to experience and use the content of this image.")](media/storage-files-migration-namespace-mapping/namespace-mapping-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        Pomocí kombinace předchozích konceptů můžete určit, kolik sdílených složek Azure potřebujete a které části vašich existujících dat skončí ve sdílené složce Azure.
        
        Vytvořte tabulku, která zaznamená vaše myšlenky, abyste na ně mohli odkazovat v dalším kroku. Udržování uspořádanosti je důležité, protože při zřizování mnoha prostředků Azure najednou může být snadné ztratit podrobnosti o plánu mapování. Chcete-li vytvořit úplné mapování, můžete si stáhnout soubor aplikace Microsoft Excel jako šablonu.

[//]: # (HTML se zobrazí jako jediný způsob, jak dosáhnout přidání vnořené tabulky se dvěma sloupci s analýzou pracovního obrazu a textem/hypertextovým odkazem na stejném řádku.)

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
