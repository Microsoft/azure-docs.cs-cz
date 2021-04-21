---
title: Pochopení Synchronizace souborů Azure vrstvení cloudu | Microsoft Docs
description: Princip vrstvení cloudu, volitelné funkce Synchronizace souborů Azure. Často používané soubory jsou ukládány do mezipaměti místně na serveru. ostatní jsou vrstveny na soubory Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1f58aa4e2aa4637dfb9fc8b29c52209975e3e367
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796807"
---
# <a name="cloud-tiering-overview"></a>Přehled vrstvení cloudu
Vrstvení cloudu, volitelná funkce Synchronizace souborů Azure, snižuje množství potřebných místních úložišť a zároveň udržuje výkon místního souborového serveru.

Když je tato funkce povolená, uloží na místním serveru jenom často používané (horké) soubory. Zřídka používané (studené) soubory jsou rozděleny do oboru názvů (struktura souborů a složek) a obsahu souboru. Obor názvů je uložený lokálně a obsah souboru uložený ve sdílené složce Azure v cloudu. 

Když uživatel otevře vrstvený soubor, Synchronizace souborů Azure bez problémů znovu zavolá data souborů ze sdílené složky v Azure.

## <a name="how-cloud-tiering-works"></a>Jak funguje vrstva cloudu

### <a name="cloud-tiering-policies"></a>Zásady vrstvení cloudu
Když povolíte vrstvení cloudu, můžete nastavit, aby se při použití vrstvy na úrovni cloudu informovaly Synchronizace souborů Azure, kdy je potřeba, abyste měli na paměti zásady pro **volné místo svazku** a **data**. 

#### <a name="volume-free-space-policy"></a>Zásada pro volné místo svazku
**Zásada pro volné místo svazku** oznamuje synchronizace souborů Azure, aby se na váš místní disk zavedlo určité množství místa, aby se do cloudu převzaly studené soubory. 

Pokud je například kapacita vašeho místního disku 200 GB a chcete, aby kapacita místního disku minimálně 40 GB měla být vždy volná, měli byste nastavit zásadu volného místa svazku na 20%. Volné místo na svazku se vztahuje na úrovni svazku, nikoli na úrovni jednotlivých adresářů nebo koncových bodů serveru. 

Informace o tom, jak zásady volného místa svazku ovlivňují soubory, které se zpočátku stáhly při přidání nového koncového bodu serveru (viz [zásady synchronizace ovlivňující vrstvy cloudu](#sync-policies-that-affect-cloud-tiering)).

#### <a name="date-policy"></a>Zásady data
V případě **zásad data** jsou studené soubory vrstveny do cloudu, pokud k nim nebyly přičteny (tj., přečteny nebo zapsány do) po dobu x počtu dnů. Pokud jste si například všimli, že soubory, které jste prošli více než 15 dny a nejsou k dispozici, jsou obvykle archivované soubory, měli byste nastavit vaše zásady data na 15 dní. 

Další příklady, jak zásady data a volného místa ve svazku spolupracují, najdete v tématu [výběr synchronizace souborů Azure zásad clouding](file-sync-choose-cloud-tiering-policies.md).

### <a name="windows-server-data-deduplication"></a>Odstranění duplicitních dat Windows serveru
Odstranění duplicitních dat je podporováno na svazcích s povoleným vytvářením vrstev cloudu počínaje systémem Windows Server 2016. Další podrobnosti najdete v tématu [Plánování nasazení synchronizace souborů Azure](file-sync-planning.md#data-deduplication).

### <a name="cloud-tiering-heatmap"></a>Heatmapuování vrstev cloudu
Synchronizace souborů Azure monitoruje přístup k souborům (operace čtení a zápisu) v průběhu času a podle toho, jak často a nedávno je přístup, přiřadí ke každému souboru skóre tepla. Pomocí těchto výsledků sestaví "heatmapu" svého oboru názvů na každém koncovém bodu serveru. Tento heatmapu je seznam všech synchronizovaných souborů v umístění s povoleným vrstvou cloudu, seřazené podle jejich skóre. Často používané soubory, které se nedávno otevřely, se považují za horkou, zatímco soubory, které se zlomek a nebyly k dispozici, se považují za studené. 

K určení relativní pozice jednotlivého souboru v tomto heatmapu systému se používá maximální délka svých časových razítek v tomto pořadí: MAX (čas posledního přístupu, čas poslední změny, čas vytvoření). 

Čas posledního přístupu se obvykle sleduje a je dostupný. Když se ale vytvoří nový koncový bod serveru s povoleným vrstvou cloudu, nedost času se předává za účelem sledování přístupu k souboru. Pokud není k dispozici žádný platný čas posledního přístupu, použije se místo toho čas poslední změny k vyhodnocení relativní pozice v heatmapu.  

Zásada data funguje stejným způsobem. Bez času posledního přístupu bude zásada data fungovat s časem poslední změny. Pokud není k dispozici, přejde zpět na čas vytvoření souboru. V průběhu času bude systém sledovat více a více požadavků na přístup k souborům a automaticky začít používat samostatně sledovaný čas posledního přístupu.

> [!Note]
> Vrstvení cloudu nezávisí na funkci systému souborů NTFS ke sledování času posledního přístupu. Tato funkce systému souborů NTFS je ve výchozím nastavení vypnutá a kvůli důležitým informacím o výkonu nedoporučujeme tuto funkci ručně povolit. Vrstvení cloudu sleduje čas posledního přístupu samostatně.

### <a name="sync-policies-that-affect-cloud-tiering"></a>Zásady synchronizace ovlivňující vrstvení cloudu

V případě agenta Synchronizace souborů Azure verze 11 existují dva další Synchronizace souborů Azure zásady, které můžete nastavit tak, aby to mělo vliv na vrstvení cloudu: **počáteční stahování** a **proaktivní odvolání**.

#### <a name="initial-download"></a>Prvotní stažení

Když se server připojuje ke sdílené složce Azure se soubory, můžete se teď rozhodnout, jak má server nejdřív stahovat data sdílené složky. Když je zapnutá vrstva cloudu, máte dvě možnosti. 

![Snímek obrazovky s prvotním stažením při povolení vrstvení cloudu](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-3.png)  

První možností je nejprve odvolat obor názvů a potom odvolat obsah souboru podle poslední upravené časové razítko, dokud se nedosáhne kapacity místního disku. Pokud máte dostatek místa na disku a víte, že soubory, které se naposledy změnily, by měly být uloženy do mezipaměti místně, je tato možnost ideální. Soubory, které nelze ukládat místně z důvodu nedostatku místa na disku, budou vrstveny.        

Druhou možností je prvotní odvolání pouze oboru názvů a navrácení obsahu souboru pouze v případě, že je k dispozici. Tato možnost je nejlepší, pokud chcete minimalizovat kapacitu použitou na místním disku a chcete, aby se uživatelé rozhodli, které soubory by se měly ukládat do mezipaměti místně. Všechny soubory se spustí jako vrstvené soubory s touto možností.

![Snímek obrazovky s prvotním stažením při zakázaném vrstvení cloudu](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-4.png)

Když je vrstva cloudu zakázaná, máte třetí možnost, což znamená, že se soubory vrstvenými dohromady neseskupují dohromady. V takovém případě se soubory na serveru zobrazí až po jejich úplném stažení. Pokud máte aplikace, které vyžadují, aby byly k dispozici úplné soubory, a nemohou tolerovat vrstvené soubory ve svém oboru názvů, je to ideální.      

#### <a name="proactive-recalling"></a>Proaktivní odvolání

Při vytvoření nebo úpravě souboru můžete proaktivním odvolat soubor na servery, které zadáte. To umožňuje, aby byl nový nebo změněný soubor snadno dostupný pro použití na jednotlivých určených serverech. 

Například globálně distribuovaná společnost obsahuje pobočky v USA a v Indii. V dopoledne (čas USA) si pracovní procesy vytvoří novou složku a nové soubory pro úplně nový projekt a budou na něm fungovat celý den. Synchronizace souborů Azure bude synchronizovat složku a soubory do sdílené složky Azure (koncový bod cloudu). Informační pracovníci v Indii budou v tomto časovém pásmu pokračovat v práci na projektu. Po doručení do ráno musí místní server Synchronizace souborů Azure, v Indii, mít tyto nové soubory místně k dispozici, aby tým Indie mohl efektivně pracovat s místní mezipamětí. Povolením tohoto režimu zabráníte, aby byl počáteční přístup k souborům pomalejší z důvodu odvolání na vyžádání a aby server mohl znovu aktivovat soubory ihned po změně nebo vytvoření ve sdílené složce Azure.

Pokud se soubory, které jsou na serveru znovu navolají, nejsou ve skutečnosti potřeba v místním prostředí, pak nepotřebné odvolání může zvýšit váš výstup a náklady. Proto povolte proaktivní opětovné volání jenom v případě, že víte, že předem naplnění mezipaměti serveru s posledními změnami z cloudu bude mít kladný vliv na uživatele nebo aplikace, které používají soubory na tomto serveru. 

Povolení proaktivní opětovného volání může také vést k vyššímu využití šířky pásma na serveru a může způsobit, že se na místním serveru bude agresivní i jiný poměrně nový obsah, protože se zvýší množství souborů, které se odvolají. To může vést k většímu zavolání, pokud jsou soubory vrstveny, které servery považují za horkou. 

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="Obrázek znázorňující chování stahování sdílené složky Azure pro aktuálně platný koncový bod serveru a tlačítko pro otevření nabídky, která umožňuje ji změnit.":::

Další podrobnosti o proaktivním opětovném volání najdete v tématu [nasazení synchronizace souborů Azure](file-sync-deployment-guide.md#proactively-recall-new-and-changed-files-from-an-azure-file-share).

## <a name="tiered-vs-locally-cached-file-behavior"></a>Chování souborů ve vrstvách a v místní mezipaměti

Vrstvení cloudu je oddělení mezi oborem názvů (hierarchií souborů a složek a také vlastnostmi souboru) a obsahem souboru. 

#### <a name="tiered-file"></a>Vrstvený soubor

V případě vrstvených souborů je velikost na disku nulová, protože obsah samotného souboru není uložen místně. Když je soubor vrstvený, Synchronizace souborů Azure filtr systému souborů (StorageSync.sys) nahradí soubor místně pomocí ukazatele (bod rozboru). Bod rozboru představuje adresu URL souboru ve sdílené složce Azure. Vrstvený soubor má atribut offline i atribut FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS nastavený v systému souborů NTFS, aby aplikace třetích stran mohli bezpečně identifikovat vrstvené soubory.   

![Snímek vlastností souboru, když je jenom vrstvený – obor názvů.](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-2.png)    

#### <a name="locally-cached-file"></a>Soubor v místní mezipaměti

Na druhé straně se u souboru uloženého na místním souborovém serveru velikost na disku rovná velikosti logické velikosti souboru, protože celý soubor (atributy souboru + obsah souboru) je uložený místně.     

![Snímek vlastností souboru, když se nejedná o vrstvený obor názvů a obsah souboru.](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-1.png) 

Je také možné, že se soubor částečně vrstvený (nebo částečně odvolal). V částečně vrstveném souboru je část souboru na disku. K tomu může dojít, když jsou soubory částečně čteny aplikacemi, jako jsou multimediální přehrávače nebo nástroje zip, které podporují streamování přístupu k souborům. Synchronizace souborů Azure je inteligentní a znovu volá pouze požadované informace z připojené sdílené složky Azure.

> [!NOTE]
> Velikost představuje logickou velikost souboru. Velikost na disku představuje fyzickou velikost datového proudu souboru uloženého na disku.

## <a name="next-steps"></a>Další kroky

* [Zvolit Synchronizace souborů Azure zásady cloudové vrstvy](file-sync-choose-cloud-tiering-policies.md)
* [Plánování nasazení Synchronizace souborů Azure](file-sync-planning.md)