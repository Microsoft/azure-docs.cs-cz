---
title: Migrace řady StorSimple 8000 do synchronizace souborů Azure
description: Zjistěte, jak migrovat zařízení StorSimple 8100 nebo 8600 do Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 7e5f70d0323aa5c502491ab99db303fde31ade83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528621"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>Migrace StorSimple 8100 a 8600 do Azure File Sync

Řada StorSimple 8000 je reprezentována fyzickými místními zařízeními 8100 nebo 8600 a jejich součástmi cloudových služeb. Je možné migrovat data z některého z těchto zařízení do prostředí Azure File Sync. Azure File Sync je výchozí a strategická dlouhodobá služba Azure, do které lze migrovat zařízení StorSimple.

Série StorSimple 8000 dosáhne [konce životnosti](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) v prosinci 2022. Je důležité začít plánovat migraci co nejdříve. Tento článek obsahuje potřebné znalosti na pozadí a kroky migrace pro úspěšnou migraci do Azure File Sync. 

## <a name="azure-file-sync"></a>Synchronizace souborů Azure

> [!IMPORTANT]
> Společnost Microsoft se zavazuje pomáhat zákazníkům při jejich migraci. E-mail AzureFilesMigration@microsoft .com pro vlastní plán migrace, stejně jako pomoc během migrace.

Azure File Sync je cloudová služba Microsoftu založená na dvou hlavních součástech:

* Synchronizace souborů a vrstvení cloudu.
* Sdílené složky jako nativní úložiště v Azure, které lze přistupovat přes více protokolů, jako je SMB a soubor REST. Sdílená složka Azure je srovnatelná se sdílenou složkou na Windows Serveru, kterou můžete nativně připojit jako síťovou jednotku. Podporuje důležité aspekty věrnosti souborů, jako jsou atributy, oprávnění a časová razítka. S Azure sdílení souborů, již není potřeba aplikace nebo služby interpretovat soubory a složky uložené v cloudu. Můžete k nim přistupovat nativně přes známé protokoly a klienty, jako je Průzkumník souborů systému Windows. Díky tomu je sdílení souborů Azure ideální a nejflexibilnější přístup k ukládání dat souborového serveru pro obecné účely i některých dat aplikací v cloudu.

Tento článek se zaměřuje na kroky migrace. Pokud se před migrací chcete dozvědět více o Azure File Sync, doporučujeme následující články:

* [Azure File Sync – přehled](https://aka.ms/AFS "Přehled")
* [Průvodce synchronizací souborů Azure – nasazením](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Cíle migrace

Cílem je zaručit integritu výrobních dat a také zajistit dostupnost. Ten vyžaduje udržení prostojů na minimu, aby se veešel do pravidelná okna údržby nebo je jen mírně překračoval.

## <a name="storsimple-8000-series-migration-path-to-azure-file-sync"></a>Cesta migrace řady StorSimple 8000 do synchronizace souborů Azure

Ke spuštění agenta Azure File Sync je potřeba místní Windows Server. Windows Server může být minimálně server 2012R2, ale v ideálním případě je Windows Server 2019.

Existuje mnoho alternativních cest migrace a vytvořit příliš dlouhý článek dokumentovat všechny z nich a ilustrovat, proč nesou riziko nebo nevýhody přes trasu doporučujeme jako osvědčený postup v tomto článku.

![Přehled fází migrace řady StorSimple 8000](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-overview.png "Trasa migrace řady StorSimple 8000 přehled fází dále níže v tomto článku.")

Předchozí obrázek znázorňuje fáze, které odpovídají oddíly v tomto článku.
Používáme migraci na straně cloudu, abychom se vyhnuli zbytečnému stahování souborů do místního zařízení StorSimple. Tento přístup zabraňuje ovlivnění chování místního ukládání do mezipaměti nebo využití šířky pásma sítě, které mohou ovlivnit vaše produkční úlohy.
Migrace na straně cloudu pracuje na snímku (klon svazku) dat. Takže vaše produkční data jsou izolována od tohoto procesu - až do přerušení na konci migrace. Práce z toho, co je v podstatě záloha, je migrace bezpečná a snadno opakovatelná, pokud narazíte na nějaké potíže.

## <a name="considerations-around-existing-storsimple-backups"></a>Důležité informace o existujících zálohách StorSimple

StorSimple umožňuje zálohovat ve formě klonů svazku. Tento článek používá k migraci živých souborů nový klon svazku.
Pokud potřebujete migrovat zálohy kromě živých dat, pak všechny pokyny v tomto článku stále platí. Jediným rozdílem je, že namísto spuštění s novým klonem svazku začnete s nejstarším klonem svazku zálohy, který potřebujete migrovat.

Pořadí je následující:

* Určete minimální sadu klonů svazků, které je třeba migrovat. Doporučujeme, aby tento seznam byl pokud možno na minimu, protože čím více záloh budete migrovat, tím déle bude celý proces migrace trvat.
* Při procházení procesu migrace začněte s nejstarším klonem svazku, který chcete migrovat, a při každé další migraci použijte další nejstarší.
* Po dokončení migrace klonování svazku je nutné pořizovat snímek sdílené složky Azure. [Snímky sdílené složky Azure](storage-snapshots-files.md) jsou způsob, jakým udržujete zálohy souborů a struktury složek pro sdílené složky Azure. Tyto snímky budete potřebovat po dokončení migrace, abyste zajistili, že máte při přechodu migrace zachované verze jednotlivých klonů svazku.
* Ujistěte se, že pořizovat snímky sdílené složky Azure pro všechny sdílené složky Azure, které jsou obsluhovány stejným svazkem StorSimple. Klony svazků jsou na úrovni svazku, snímky sdílené složky Azure jsou na úrovni sdílené složky. Po dokončení migrace klonování svazku je nutné pořizovat snímek sdílené složky (v každé sdílené složce Azure).
* Opakujte proces migrace pro klon svazku a pořizování snímků sdílené složky po každém klonování svazku, dokud nezískáte snímek živých dat. Proces migrace klonování svazku je popsán v následujících fázích. 

Pokud nepotřebujete přesunout zálohy vůbec a můžete spustit nový řetězec záloh na straně sdílení souborů Azure po migraci pouze živá data se provádí, pak je výhodné snížit složitost migrace a množství času migrace bude trvat. Můžete se rozhodnout, zda chcete přesunout zálohy a kolik pro každý svazek (ne každou sdílenou složku) máte v Programu StorSimple.

## <a name="phase-1-get-ready"></a>Fáze 1: Připravte se

:::row:::
    :::column:::
        ![Obrázek ilustrující část dřívějšího obrázku přehledu, který pomáhá zaměřit tuto podsekci článku.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-1.png)
    :::column-end:::
    :::column:::
        Základem migrace je klon svazku a virtuální cloudové zařízení, nazývané StorSimple 8020.
Tato fáze se zaměřuje na nasazení těchto prostředků v Azure.
    :::column-end:::
:::row-end:::

### <a name="deploy-a-storsimple-8020-virtual-appliance"></a>Nasazení virtuálního zařízení StorSimple 8020

Nasazení cloudového zařízení je proces, který vyžaduje zabezpečení, sítě a několik dalších aspektů.

> [!IMPORTANT]
> Následující příručka obsahuje některé nepotřebné oddíly. Přečtěte si a postupujte podle článku od začátku až do "Kroku 3". Pak se vraťte k tomuto článku. V této příručce není třeba v této příručce v tomto okamžiku vyplňovat "Krok 3" ani nic jiného.

[Nasazení virtuálního zařízení StorSimple 8020](../../storsimple/storsimple-8000-cloud-appliance-u2.md)

### <a name="determine-a-volume-clone-to-use"></a>Určení klonování svazku, který se má použít

Když jste připraveni zahájit migraci, prvním krokem je provést nový klon svazku - stejně jako pro zálohování - který zachycuje aktuální stav vašeho cloudového úložiště StorSimple. Vezměte klon pro každý z svazků StorSimple máte.
Pokud potřebujete přesunout zálohy, pak první klon svazku, který používáte, není nově vytvořený klon, ale nejstarší klon svazku (nejstarší záloha), který potřebujete migrovat.
Podrobné pokyny naleznete v části ["Důležité informace o existujících zálohách StorSimple".](#considerations-around-existing-storsimple-backups)

> [!IMPORTANT]
> Následující příručka obsahuje některé nepotřebné oddíly. Přečtěte si a postupujte pouze podle kroků uvedených v části propojená. Pak se vraťte k tomuto článku. Není nutné postupovat podle části Další kroky.

[Vytvoření klonu svazku](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)

### <a name="use-the-volume-clone"></a>Použití klonování svazku

Poslední fáze fáze 1 je zpřístupnit vybraný klon svazku na virtuálním zařízení 8020 v Azure.

> [!IMPORTANT]
> Následující příručka obsahuje potřebné kroky, ale také - na konci - instrukce pro formátování svazku. **NEFORMÁTUJTE SVAZEK** Přečtěte si a postupujte podle propojeného "oddílu 7" od začátku až do instrukce: "10. Chcete-li formátovat jednoduchý svazek, ..."  Zastavte před provedením tohoto kroku a vraťte se k tomuto článku.

[Připojení klonu svazku na virtuální zařízení 8020 v Azure](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-1-summary"></a>Shrnutí fáze 1

Nyní, když jste dokončili fázi 1, budete muset provést následující:

* Nasazení virtuálního zařízení StorSimple 8020 v Azure.
* Určení, se kterým klonem svazku zahájíte migraci.
* Připojení klonů svazku (jeden pro každý živý svazek) do virtuálního zařízení StorSimple v Azure, s jeho daty k dispozici pro další použití.

## <a name="phase-2-cloud-vm"></a>Fáze 2: Virtuální virtuální měsících automatů v cloudu

:::row:::
    :::column:::
        ![Obrázek ilustrující část dřívějšího obrázku přehledu, který pomáhá zaměřit tuto podsekci článku.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-2.png)
    :::column-end:::
    :::column:::
        Poté, co váš počáteční klon je k dispozici na virtuálnízařízení StorSimple 8020 v Azure, je teď čas zřídit virtuální počítač a vystavit klon svazku (nebo více) na tento virtuální počítač přes iSCSI.
    :::column-end:::
:::row-end:::

### <a name="deploy-an-azure-vm"></a>Nasazení virtuálního počítače Azure

Virtuální počítač se systémem Windows Server v Azure je stejně jako StorSimple 8020, dočasné část infrastruktury, která je nezbytná jenom během migrace.
Konfigurace nasazeného virtuálního počítače závisí především na počtu položek (souborů a složek), které budete synchronizovat. Pokud máte nějaké obavy, doporučujeme použít konfiguraci s vyšším výkonem.

Jeden Systém Windows Server může synchronizovat až 30 sdílených složek Azure.
Specifikace, o kterých se rozhodnete, musí zahrnovat každou sdílenou nebo cestu nebo kořen svazku StorSimple a počítat položky (soubory a složky).

Celková velikost dat je menší překážkou – je to počet položek, které potřebujete přizpůsobit specifikace stroje.

* [Přečtěte si, jak velikost systému Windows Server na základě počtu položek (souborů a složek), které potřebujete synchronizovat.](storage-sync-files-planning.md#recommended-system-resources)

    **Upozorňujeme:** Dříve propojený článek představuje tabulku s rozsahem pro paměť serveru (RAM). Orientujte se směrem k velkému počtu pro virtuální počítač Azure. Můžete se orientovat směrem k menšímu číslu pro místní počítač.

* [Přečtěte si, jak nasadit virtuální virtuální ms Se systémem Windows Sever.](../../virtual-machines/windows/quick-create-portal.md)

> [!IMPORTANT]
> Ujistěte se, že virtuální počítač je nasazený ve stejné oblasti Azure jako virtuální zařízení StorSimple 8020. Pokud v rámci této migrace, budete také muset změnit oblast dat cloudu z oblasti, ve které je uložena v dnes, můžete to udělat později, když zřídíte sdílené složky Azure.

> [!IMPORTANT]
> Chcete-li optimalizovat výkon, nasaďte **velmi rychlý disk operačního systému** pro váš cloudový virtuální počítač. Synchronizační databázi uložíte na disk operačního systému pro všechny datové svazky. Dále se ujistěte, že vytvoříte **velký disk operačního systému**. V závislosti na počtu položek (souborů a složek) na svazcích StorSimple může disk operačního systému potřebovat **několik set gib** místa pro synchronizaci databáze.

### <a name="expose-the-storsimple-8020-volumes-to-the-azure-vm"></a>Vystavit svazky StorSimple 8020 virtuálnímu počítači Azure

V této fázi připojujete jeden nebo několik svazků StorSimple z virtuálního zařízení 8020 přes iSCSI k zřízenému virtuálnímu počítači se systémem Windows Server.

> [!IMPORTANT]
> Následující články vyplňte pouze **získat privátní IP pro cloudové zařízení** a připojit přes **iSCSI** části a vraťte se k tomuto článku.

1. [Získání privátní IP adresy pro cloudové zařízení](../../storsimple/storsimple-8000-cloud-appliance-u2.md#get-private-ip-for-the-cloud-appliance)
2. [Připojení přes iSCSI](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-2-summary"></a>Shrnutí fáze 2

Nyní, když jste dokončili fázi 2, máte: 

* Zřízení virtuálního počítače se systémem Windows Server ve stejné oblasti jako virtuální zařízení StorSimple 8020
* Vystavené všechny příslušné svazky z 8020 na Windows Server VM přes iSCSI.
* Nyní byste měli vidět obsah souborů a složek při použití Průzkumníka souborů na virtuálním počítači serveru na připojených svazcích.

Pokračujte do fáze 3 pouze po dokončení těchto kroků pro všechny svazky, které vyžadují migraci.

## <a name="phase-3-set-up-azure-file-shares-and-get-ready-for-azure-file-sync"></a>Fáze 3: Nastavení sdílených složek Azure a příprava na Azure File Sync

:::row:::
    :::column:::
        ![Obrázek ilustrující část dřívějšího obrázku přehledu, který pomáhá zaměřit tuto podsekci článku.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-3.png)
    :::column-end:::
    :::column:::
        V této fázi budete určovat a zřizujeněkolik sdílených složek Azure, vytvoření Windows Server místně jako náhrada zařízení StorSimple a nakonfigurovat tento server pro Azure File Sync. 
    :::column-end:::
:::row-end:::

### <a name="map-your-existing-namespaces-to-azure-file-shares"></a>Mapování stávajících oborů názvů na sdílené složky Azure

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="deploy-azure-file-shares"></a>Nasazení sdílených složek Azure

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

> [!TIP]
> Pokud potřebujete změnit oblast Azure z aktuální oblasti, ve které se nacházejí vaše data StorSimple, pak zřídíte sdílené složky Azure v nové oblasti, kterou chcete použít. Oblast určíte tak, že ji vyberete při zřizování účtů úložiště, které drží vaše sdílené složky Azure. Ujistěte se, že také prostředek Azure File Sync, který zřídíte níže, je ve stejné, nové oblasti.

### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Nasazení cloudového prostředku Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Pokud potřebujete změnit oblast Azure z aktuální oblasti, ve které se nacházejí vaše data StorSimple, pak jste zřídíte účty úložiště pro sdílené složky Azure v nové oblasti. Ujistěte se, že jste vybrali stejnou oblast při nasazení této služby synchronizace úložiště.

### <a name="deploy-an-on-premises-windows-server"></a>Nasazení místního Windows Serveru

* Vytvořte Windows Server 2019 – minimálně 2012R2 – jako virtuální počítač nebo fyzický server. Je také podporován cluster s podporou převzetí služeb při selhání systému Windows Server. Nepoužívejte znovu server, který můžete mít fronting StorSimple 8100 nebo 8600.
* Zřízení nebo přidání přímo připojeného úložiště (DAS ve srovnání s NAS, který není podporován).

Je vhodné poskytnout novému systému Windows Server stejné nebo větší množství úložiště, než má zařízení StorSimple 8100 nebo 8600 místně dostupné pro ukládání do mezipaměti. Budete používat Windows Server stejným způsobem, jakým jste použili zařízení StorSimple, pokud má stejné množství úložiště jako zařízení, pak ukládání do mezipaměti by mělo být podobné, ne-li stejné.
Úložiště můžete ze serveru Windows Server přidávat nebo odebírat podle svých vůle. To umožňuje škálovat velikost místního svazku a velikost místního úložiště, které je k dispozici pro ukládání do mezipaměti.

### <a name="prepare-the-windows-server-for-file-sync"></a>Příprava systému Windows Server na synchronizaci souborů

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="configure-azure-file-sync-on-the-windows-server"></a>Konfigurace synchronizace souborů Azure na Windows Serveru

Pro tento proces musí být registrovaný místní systém Windows Server připraven a připojen k Internetu.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Nezapomeňte zapnout vrstvení cloudu!** Vrstvení cloudu je funkce AFS, která umožňuje místnímu serveru mít menší kapacitu úložiště, než je uloženo v cloudu, ale mají k dispozici celý obor názvů. Místně zajímavá data jsou také uložena do mezipaměti místně pro rychlý, místní přístup výkon. Dalším důvodem pro zapnutí vrstvení cloudu v tomto kroku je, že v této fázi nechceme synchronizovat obsah souboru, pouze obor názvů by se měl v tuto chvíli pohybovat.

## <a name="phase-4-configure-the-azure-vm-for-sync"></a>Fáze 4: Konfigurace virtuálního počítače Azure pro synchronizaci

:::row:::
    :::column:::
        ![Obrázek ilustrující část dřívějšího obrázku přehledu, který pomáhá zaměřit tuto podsekci článku.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-4.png)
    :::column-end:::
    :::column:::
        Tato fáze se týká vašeho virtuálního počítače Azure s připojeným iSCSI, klony prvního svazku. Během této fáze získáte virtuální počítač připojený prostřednictvím Azure File Sync a spustit první kolo přesunutí souborů z vašeho storsimple svazku klony.
        
    :::column-end:::
:::row-end:::

Už jste nakonfigurovali místní server, který nahradí vaše zařízení StorSimple 8100 nebo 8600 pro Azure File Sync. 

Konfigurace virtuálního počítače Azure je téměř identický proces s jedním dalším krokem. Následující kroky vás provedou procesem.

> [!IMPORTANT]
> Je důležité, aby virtuální počítač Azure **nebyl nakonfigurovaný s povoleným vrstvením v cloudu!** Během migrace budete vyměňovat objem tohoto serveru s novějšími klony svazků. Vrstvení cloudu nemá žádné výhody a režii na využití procesoru, které byste se měli vyhnout.

1. [Nasaďte agenta AFS. (viz předchozí část)](#prepare-the-windows-server-for-file-sync)
2. [Příprava virtuálního počítače pro Azure File Sync.](#get-the-vm-ready-for-azure-file-sync)
3. [Konfigurace synchronizace](#configure-azure-file-sync-on-the-azure-vm)

### <a name="get-the-vm-ready-for-azure-file-sync"></a>Příprava virtuálního počítače na Azure File Sync

Azure File Sync se používá k přesunutí souborů z připojených svazků iSCSI StorSimple do cílových sdílených složek azure.
Během tohoto procesu migrace připojíte několik klonů svazku k virtuálnímu počítači pod stejným písmenem jednotky. Azure File Sync musí být nakonfigurovaná tak, aby viděla další klon svazku, který jste připojili jako novější verzi souborů a složek, a aktualizovat sdílené složky Azure připojené prostřednictvím Azure File Sync. 

> [!IMPORTANT]
> Aby to fungovalo, musí být klíč registru nastaven na serveru před nakonfigurovanou synchronizací souborů Azure.

1. Vytvořte nový adresář na systémové jednotce virtuálního počítači. Informace o synchronizaci souborů Azure bude nutné zachovat tam místo na připojené svazek klony. Příklad: `"C:\syncmetadata"`
2. Otevřete regedit a vyhledejte následující podregistr registru:`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync`
3. Vytvoření nového klíče typu String s ***názvem: MetadataRootPath***
4. Nastavte úplnou cestu k adresáři, který jste vytvořili na systémovém svazku, například:`C:\syncmetadata"`

### <a name="configure-azure-file-sync-on-the-azure-vm"></a>Konfigurace synchronizace souborů Azure na virtuálním počítači Azure

Tento krok je podobný předchozí části, která popisuje, jak nakonfigurovat AFS na místním serveru.

Rozdíl je, že nesmíte povolit vrstvení cloudu na tomto serveru a že je třeba se ujistit, že správné složky jsou připojeny ke správným sdíleným složkám Azure. V opačném případě se vaše pojmenování sdílených složek Azure a datového obsahu nebude shodovat a neexistuje žádný způsob, jak přejmenovat cloudové prostředky nebo místní složky bez změny konfigurace synchronizace.

V [předchozí části se podívejte na postup konfigurace Azure File Sync na Windows Serveru](#configure-azure-file-sync-on-the-windows-server).

### <a name="step-4-summary"></a>Shrnutí kroku 4

V tomto okamžiku budete úspěšně nakonfigurovánazure file sync na virtuálním počítači Azure jste připojeni váš storSimple svazku klony prostřednictvím iSCSI.
Data teď proudí z virtuálního počítače Azure do různých sdílených složek Azure a odtud se na místním Windows Serveru zobrazí plně unavený obor názvů.

> [!IMPORTANT]
> Ujistěte se, že v současné době nejsou k systému Windows Server uděleny žádné změny ani přístup uživatelů.

Počáteční data klonování svazku přes virtuální počítač Azure do sdílených složek Azure může trvat dlouhou dobu, potenciálně týdny. Odhad času, který to bude trvat, je ošidný a závisí na mnoha faktorech. Zejména rychlost, jakou má virtuální počítač Azure přístup k souborům na svazcích StorSimple a jak rychle může Azure File Sync zpracovávat soubory a složky, které je potřeba synchronizovat. 

Ze zkušenosti můžeme předpokládat, že šířka pásma - tedy skutečná velikost dat - hraje podřízenou roli. Doba, po kterou bude toto nebo jakékoli následující kolo migrace trvat, závisí většinou na počtu položek, které lze zpracovat za sekundu. Takže například 1 TiB se 100 000 soubory a složkami bude s největší pravděpodobností dokončen pomaleji než 1 TiB s pouze 50 000 soubory a složkami.

## <a name="phase-5-iterate-through-multiple-volume-clones"></a>Fáze 5: Iterate přes více svazkových klonů

:::row:::
    :::column:::
        ![Obrázek ilustrující část dřívějšího obrázku přehledu, který pomáhá zaměřit tuto podsekci článku.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-5.png)
    :::column-end:::
    :::column:::
        Jak je popsáno v předchozí fázi, počáteční synchronizace může trvat dlouhou dobu. Vaši uživatelé a aplikace stále přistupují k místnímu zařízení StorSimple 8100 nebo 8600. To znamená, že se hromadí změny a každý den větší rozdíl mezi živými daty a počátečním klonem svazku, právě se jedná o migraci, formuláře. V této části se dozvíte, jak minimalizovat prostoje pomocí více klonů svazků a sdělování, kdy je synchronizace hotová.
    :::column-end:::
:::row-end:::

Bohužel, proces migrace není okamžitý. To znamená, že výše uvedená delta k živým datům je nevyhnutelným důsledkem. Dobrou zprávou je, že můžete opakovat proces montáže nových svazkových klonů. Delta každého klonu svazku bude postupně menší. Synchronizace se tedy nakonec dokončí v době, kterou považujete za přijatelnou pro převedení uživatelů a aplikací do režimu offline a přepnutí na místní server Windows.

Opakujte následující kroky, dokud se synchronizace nedokončí dostatečně rychle, abyste se cítili pohodlně, když jste uživatele a aplikace přepínali do režimu offline:

1. [Určete, že synchronizace je pro daný klon svazku dokončena.](#determine-when-sync-is-done)
2. [Vezměte nový klon (y) svazku a namontujte jej na virtuální zařízení 8020.](#the-next-volume-clones)
3. [Určete, kdy je synchronizace provedena.](#determine-when-sync-is-done)
4. [Strategie přeškrtání](#cut-over-strategy)

### <a name="the-next-volume-clones"></a>Další klon (klony) dalšího svazku

Diskutovali jsme s svazek klony dříve v tomto článku.
Tato fáze má dvě akce:

1. [Vzít klon svazku](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)
2. [Připojení tohoto klonu svazku (viz výše)](#use-the-volume-clone)

### <a name="determine-when-sync-is-done"></a>Určení doby synchronizace

Po dokončení synchronizace můžete zastavit měření času a zjistit, zda je třeba opakovat proces pořízení klonu svazku a jeho montáže nebo zda byla synchronizace času s posledním klonem svazku dostatečně malá.

Chcete-li určit, synchronizace je dokončena:

1. Otevření Prohlížeče událostí a přechod na **aplikace a služby**
2. Navigace a otevření **microsoft\FileSync\Agent\Telemetrie**
3. Podívejte se na nejnovější **událost 9102**, která odpovídá dokončené relaci synchronizace
4. Vyberte **Podrobnosti** a zkontrolujte, zda je hodnota **SyncDirection** **Nahráno.**
5. Zkontrolujte **HResult** a potvrďte, že ukazuje **0**. To znamená, že relace synchronizace byla úspěšná. Pokud HResult je nenulová hodnota, došlo k chybě během synchronizace. Pokud **je PerItemErrorCount** větší než 0, některé soubory nebo složky se nesynchronizovaly správně. Je možné mít HResult 0, ale PerItemErrorCount, který je větší než 0. V tomto okamžiku se nemusíte starat o PerItemErrorCount. Tyhle složky chytíme později. Pokud je tento počet chyb významný, tisíce položek, obraťte se na zákaznickou podporu a požádejte o připojení ke skupině produktů Azure File Sync, abyste se k ní připojili k přímému návodu k dalším fázím.
6. Zkontrolujte, zda chcete zobrazit více 9102 událostí s HResult 0 v řadě. To znamená, že synchronizace je pro tento klon svazku dokončena.

### <a name="cut-over-strategy"></a>Strategie přeškrtání

1. Zjistěte, zda je synchronizace z klonování svazku dostatečně rychlá. (Delta dostatečně malá.)
2. Přístroj StorSimple přepnete do úřádku.
3. Konečná RoboKopie.

Změřte čas a zjistěte, zda synchronizace z nedávného klonu svazku může být dokončena v časovém okně dostatečně malém, který si můžete dovolit jako prostoje v systému.

Nyní je čas zakázat přístup uživatele k zařízení StorSimple. Už žádné změny. Prostoje začaly.
Přístroj musíte nechat online a připojený, ale nyní musíte zabránit změnám.

Ve fázi 6 doženete všechny delta v živých datech od posledního klonu svazku.

## <a name="phase-6-a-final-robocopy"></a>Fáze 6: Závěrečná RoboCopy

V tomto okamžiku existují dva rozdíly mezi místním systémem Windows Server a zařízením StorSimple 8100 nebo 8600:

1. Mohou existovat soubory, které nebyly synchronizovány (viz **PerItemErrors** z protokolu událostí výše)
2. Zařízení StorSimple má naplněnou mezipaměť vs. Windows Server pouze obor názvů, kde není v tuto chvíli uložen žádný obsah souboru.

![Obrázek ilustrující část dřívějšího obrázku přehledu, který pomáhá zaměřit tuto podsekci článku.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-6.png)

Můžeme přinést cache Systému Windows Server do stavu zařízení a zajistit, aby žádný soubor zůstal pozadu s konečnou RoboCopy.

> [!CAUTION]
> Je nezbytně nutné, aby příkaz RoboCopy, který sledujete, byl přesně tak, jak je popsáno níže. Chceme pouze kopírovat soubory, které jsou místní a soubory, které nebyly přesunuty prostřednictvím svazku klon + synchronizace přístup dříve. Můžeme vyřešit problémy, proč se nesynchronizovaly později, po dokončení migrace. (Viz [Řešení potíží se synchronizací souborů Azure](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing). Je to s největší pravděpodobností netisknutelné znaky v názvech souborů, které vám při odstranění neuniknou.)

RoboCopy, příkaz:

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Pozadí:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Umožňuje RoboCopy spustit vícevláknové. Výchozí hodnota je 8, max je 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Výstupy stav log souboru jako UNICODE (přepíše existující protokol).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Výstupy do okna konzoly. Používá se ve spojení s výstupem do souboru protokolu.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Spustí RoboCopy ve stejném režimu, který by použila záložní aplikace. Umožňuje RoboCopy přesunout soubory, ke kterým současný uživatel nemá oprávnění.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Umožňuje RoboCopy pouze zvážit rozdíly mezi zdrojem (StorSimple zařízení) a cíl (Windows Server adresář).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY:příznak copy[s]
   :::column-end:::
   :::column span="1":::
      věrnost kopie souboru (výchozí hodnota je /COPY:DAT), příznaky kopírování: D=Data, A=Atributy, T=Timestamps, S=Security=NTFS ACL, O=Owner info, U=aUditing info
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      KOPÍROVAT všechny informace o souboru (ekvivalentní /COPY:DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY:příznak copy[s]
   :::column-end:::
   :::column span="1":::
      věrnost pro kopii adresářů (výchozí hodnota je /DCOPY:DA), příznaky kopírování: D=Data, A=Atributy, T=Časová razítka
   :::column-end:::
:::row-end:::

Tento příkaz RoboCopy byste měli spustit pro každý adresář v systému Windows Server jako cíl, který jste nakonfigurovali se synchronizací souborů se souborem Azure.

Můžete spustit více těchto příkazů paralelně.
Jakmile je tento krok RoboCopy dokončen, můžete uživatelům a aplikacím umožnit přístup k systému Windows Server, jako tomu bylo dříve v zařízení StorSimple.

Podívejte se do souborů protokolu robocopy a zjistěte, zda byly soubory zanechány. Pokud by problémy měly existovat, ve většině případů je můžete vyřešit po dokončení migrace a uživatelům a aplikacím, kteří byli znovu převedeni na windows server. Pokud potřebujete opravit nějaké problémy, uvažte před fází 7.

Je pravděpodobně potřeba vytvořit sdílené složky SMB na serveru Windows Server, které jste měli na datech StorSimple dříve. Můžete front-load tento krok a to dříve, aby zde neztrácel čas, ale musíte zajistit, že před tímto bodem, žádné změny souborů dojít na serveru Windows.

Pokud máte nasazení DFS-N, můžete nasměrovat DFN-Namespaces na nová umístění složek serveru. Pokud nemáte nasazení DFS-N a zařízení 8100 8600 jste místně zadali pomocí systému Windows Server, můžete tento server z domény smést a připojit se k novému systému Windows Server s afs emitovanému do domény, dát mu stejný název serveru jako starý server. a stejné názvy sdílení, pak přestřihnutí na nový server zůstane pro uživatele, zásady skupiny nebo skripty transparentní.

## <a name="phase-7-deprovision"></a>Fáze 7: Zrušení poskytování

Během poslední fáze jste iterovali prostřednictvím více svazkových klonů a nakonec se vám podařilo snížit přístup uživatelů k novému systému Windows Server poté, co jste zařízení StorSimple převedli do provozu.

Nyní můžete začít odpojit nepotřebné prostředky.
Než začnete, je osvědčeným postupem sledovat vaše nové nasazení Azure File Sync v produkčním prostředí, na chvíli. To vám dává možnosti, jak opravit všechny problémy, se kterými se můžete setkat.

Jakmile jste spokojeni a jste sledovali nasazení AFS po dobu nejméně několika dní, můžete začít odjit prostředky v tomto pořadí:

1. Vypněte virtuální počítač Azure, který jsme použili k přesunutí dat z klonů svazku do sdílených složek Azure prostřednictvím synchronizace souborů.
2. Přejděte na prostředek služby Synchronizace úložiště v Azure a odregistrujte virtuální počítač Azure. Tím se odebere ze všech skupin synchronizace.

    > [!WARNING]
    > **Ujistěte se, že si vyberete ten správný stroj.** Vypnuli jste cloudový virtuální mísu, to znamená, že by se měl zobrazovat jako jediný offline server v seznamu registrovaných serverů. V tomto kroku nesmíte vybírat místní systém Windows Server, pokud tak učiníte, zrušíte jeho registraci.

3. Odstraňte virtuální počítač Azure a jeho prostředky.
4. Zakažte virtuální zařízení StorSimple 8020.
5. Zrušení zřízení všech prostředků StorSimple v Azure.
6. Odpojte fyzické zařízení StorSimple od datového centra.

Migrace je dokončena.

## <a name="next-steps"></a>Další kroky

Seznamte se s Azure File Sync. Zejména s flexibilitou zásad odstupňování cloudu.

Pokud vidíte na webu Azure Portal nebo z předchozích událostí, že některé soubory se trvale nesynchronizují, přečtěte si průvodce pro řešení potíží, kde najdete postup řešení těchto problémů.

* [Azure File Sync – přehled: aka.ms/AFS](https://aka.ms/AFS)
* [Vrstvení cloudu](storage-sync-cloud-tiering.md) 
* [Průvodce odstraňováním potíží se synchronizací souborů Azure](storage-sync-files-troubleshoot.md)
