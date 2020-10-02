---
title: Migrace řady StorSimple 8000 na Azure File Sync
description: Přečtěte si, jak migrovat zařízení s StorSimple 8100 nebo 8600 do Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: be61a6e75c4aa9b5714ffbf3b4f19656b347c493
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653243"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>Migrace StorSimple 8100 a 8600 do Azure File Sync

Série StorSimple 8000 je představována buď 8100, 8600 nebo s fyzickými, místními zařízeními a jejich součástmi cloudové služby. Data z některého z těchto zařízení je možné migrovat do Azure File Syncho prostředí. Azure File Sync je výchozí a strategická dlouhodobá služba Azure, na kterou je možné StorSimple zařízení migrovat.

Série StorSimple 8000 se [dokončí do konce životnosti](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) v prosinci 2022. Je důležité zahájit plánování migrace co nejrychleji. Tento článek popisuje nezbytné kroky pro znalosti a migrace na pozadí pro úspěšnou migraci na Azure File Sync. 

## <a name="azure-file-sync"></a>Synchronizace souborů Azure

> [!IMPORTANT]
> Společnost Microsoft se zavazuje pomáhat zákazníkům při jejich migraci. E-mail AzureFilesMigration@microsoft . com pro vlastní plán migrace a pomoc při migraci.

Azure File Sync je cloudová služba Microsoftu založená na dvou hlavních součástech:

* Synchronizace souborů a vrstvení cloudu.
* Sdílené složky jako nativní úložiště v Azure, které je možné používat v různých protokolech jako SMB a REST souborů. Sdílená složka Azure je srovnatelná se sdílenou složkou na Windows serveru, kterou můžete nativně připojit jako síťovou jednotku. Podporuje důležité aspekty souborů, jako jsou atributy, oprávnění a časová razítka. Se sdílenými složkami Azure už není potřeba, aby aplikace ani služba dokázala interpretovat soubory a složky uložené v cloudu. K těmto datům můžete nativně přistupovat prostřednictvím známých protokolů a klientů, jako je Průzkumník souborů systému Windows. Díky tomu je Azure File sharely ideálním a nejpružnější přístupem k ukládání dat souborového serveru pro obecné účely a také k určitým datům aplikací v cloudu.

Tento článek se zaměřuje na kroky migrace. Pokud před migrací chcete získat další informace o Azure File Sync, doporučujeme následující články:

* [Azure File Sync – přehled](https://aka.ms/AFS "Přehled")
* [Průvodce nasazením Azure File Sync](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Cíle migrace

Cílem je zaručit integritu produkčních dat a zaručit dostupnost. Ta ta vyžaduje udržení minimálního výpadku, aby bylo možné se přizpůsobit nebo jen mírně překročit pravidelná časová období údržby.

## <a name="storsimple-8000-series-migration-path-to-azure-file-sync"></a>Azure File Sync cestu migrace řady StorSimple 8000

Pro spuštění agenta Azure File Sync je vyžadován místní Windows Server. Windows Server může mít minimálně server 2012R2, ale v ideálním případě je to Windows Server 2019.

Existuje spousta alternativních cest migrace a vytvoří se příliš dlouhý článek, který by měl dokument zdokumentovat a ilustruje, proč představují riziko nebo nevýhody v trase, které doporučujeme jako osvědčený postup v tomto článku.

![Přehled fází migrace řady StorSimple 8000](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-overview.png "Přehled postupu migrace řady StorSimple 8000 níže v tomto článku.")

Předchozí obrázek znázorňuje fáze, které odpovídají oddílům v tomto článku.
Používáme migraci na straně cloudu, aby nedocházelo k zbytečnému navrácení souborů do místního zařízení StorSimple. Tento přístup zabraňuje tomu, aby bylo možné ovlivnit ukládání do mezipaměti nebo využití šířky pásma sítě, a to z nich může mít vliv na vaše produkční úlohy.
Migrace na straně cloudu pracuje na snímku (klonování svazku) vašich dat. Proto jsou vaše produkční data izolovaná od tohoto procesu – dokud na konci migrace nedojde k jejich přerušení. Při práci na tom, co je v podstatě zálohování, je migrace zabezpečená a snadno opakovaná, takže byste měli řešit případné potíže.

## <a name="considerations-around-existing-storsimple-backups"></a>Otázky týkající se stávajících záloh StorSimple

StorSimple umožňuje vytvářet zálohy ve formě klonů svazků. V tomto článku se k migraci vašich živých souborů používá nový klon svazku.
Pokud potřebujete kromě svých živých dat migrovat zálohy, všechny pokyny v tomto článku jsou stále platné. Jediným rozdílem je, že místo začátku nové klonování svazku začnete s nejstarším klonem záložního svazku, který potřebujete migrovat.

Sekvence je následující:

* Určete minimální sadu klonů svazků, které je třeba migrovat. Pokud je to možné, doporučujeme tento seznam pokaždé, pokud je to možné, protože čím víc záloh migrujete, tím déle bude trvat celý proces migrace.
* Při procházení procesu migrace začněte s nejstarším klonem svazků, který máte v úmyslu migrovat, a při každé následné migraci použijte další nejstarší.
* Po dokončení migrace klonování svazků musíte převzít snímek sdílené složky Azure. [Snímky sdílené složky Azure](storage-snapshots-files.md) umožňují průběžné zálohování souborů a složek pro sdílené složky Azure v daném okamžiku. Tyto snímky budete potřebovat po dokončení migrace, abyste měli jistotu, že při migraci budete uchovávat verze všech klonů svazků.
* Ujistěte se, že používáte snímky sdílené složky Azure pro všechny sdílené složky Azure, které jsou obsluhovány stejným svazkem StorSimple. Klony svazků jsou na úrovni svazku, snímky sdílené složky Azure jsou na úrovni sdílené složky. Po dokončení migrace klonu svazku musíte pořídit snímek sdílené složky (ve všech sdílených souborech Azure).
* Opakujte proces migrace pro klon svazku a pořiďte snímky sdílené složky za každým klonem svazku, dokud se nedostanete do snímku živých dat. Proces migrace klonu svazku je popsaný v následujících fázích. 

Pokud nepotřebujete najednou přesunout zálohy a po dokončení migrace jenom živých dat zahájit nový řetěz záloh na straně sdílené složky Azure, je vhodné omezit složitost migrace a dobu, po kterou bude migrace trvat. Můžete rozhodnout, jestli chcete přesunout zálohy a kolik každého svazku (ne každé sdílené složky) v StorSimple.

## <a name="phase-1-get-ready"></a>Fáze 1: Příprava

:::row:::
    :::column:::
        ![Obrázek ilustrující část dřívějšího přehledu, který vám pomůže zaměřit se na tento pododdíl článku.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-1.png)
    :::column-end:::
    :::column:::
        Základem pro migraci je klonování svazku a virtuální cloudové zařízení, které se nazývá StorSimple 8020.
Tato fáze se zaměřuje na nasazení těchto prostředků v Azure.
    :::column-end:::
:::row-end:::

### <a name="deploy-a-storsimple-8020-virtual-appliance"></a>Nasazení virtuálního zařízení s StorSimple 8020

Nasazení cloudového zařízení je proces, který vyžaduje zabezpečení, sítě a další okolnosti.

> [!IMPORTANT]
> Následující příručka obsahuje některé nepotřebné oddíly. Přečtěte si článek od začátku až do kroku 3. Pak se vraťte k tomuto článku. V tuto chvíli nemusíte v tomto průvodci doplňovat "krok 3" nebo cokoli nad ním.

[Nasazení virtuálního zařízení s StorSimple 8020](../../storsimple/storsimple-8000-cloud-appliance-u2.md)

### <a name="determine-a-volume-clone-to-use"></a>Určení klonu svazku, který se má použít

Až budete připraveni zahájit migraci, prvním krokem je vytvoření nového klonu svazku, stejně jako při zálohování, který zachycuje aktuální stav cloudového úložiště StorSimple. Proveďte klon pro každý ze StorSimple svazků.
Pokud budete potřebovat přesunout zálohy, první klonovaná jednotka, kterou použijete, není nově vytvořeným klonem, ale nejstarší klonování svazku (nejstarší záloha), které je třeba migrovat.
Podrobné pokyny najdete v části ["informace o stávajících zálohách StorSimple"](#considerations-around-existing-storsimple-backups) .

> [!IMPORTANT]
> Následující příručka obsahuje některé nepotřebné oddíly. Přečtěte si a postupujte pouze podle kroků uvedených v části propojeno s. Pak se vraťte k tomuto článku. Nemusíte postupovat podle pokynů v části Další kroky.

[Vytvoření klonu svazku](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)

### <a name="use-the-volume-clone"></a>Použití klonu svazku

Poslední fází fáze 1 je vytvoření klonu svazku, který jste zvolili, k dispozici na virtuálním zařízení 8020 v Azure.

> [!IMPORTANT]
> Následující příručka obsahuje nezbytné kroky, ale také na konci – pokyny k naformátování svazku. **NEformátovat svazek** Přečtěte si část s odkazem na část 7 od začátku až do instrukce: 10. Formátování jednoduchého svazku,...  Zastavte se před tímto krokem a vraťte se k tomuto článku.

[Připojení klonu svazku na virtuálním zařízení 8020 v Azure](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-1-summary"></a>Souhrn fáze 1

Teď, když jste dokončili fázi 1, provedete následující:

* Nasadili jste virtuální zařízení s StorSimple 8020 v Azure.
* Určili jste, ke které klonování svazku se zahájí migrace.
* K virtuálnímu zařízení StorSimple v Azure jste připojili klony svazků (jeden pro každý živý svazek) a data jsou k dispozici pro další použití.

## <a name="phase-2-cloud-vm"></a>Fáze 2: cloudový virtuální počítač

:::row:::
    :::column:::
        ![Obrázek ukazuje, že teď je čas zřídit virtuální počítač a vystavit klon svazku (nebo několik) k tomuto virtuálnímu počítači přes iSCSI.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-2.png)
    :::column-end:::
    :::column:::
        Po dokončení počátečního klonu na virtuálním zařízení s StorSimple 8020 v Azure teď můžete zřídit virtuální počítač a vystavit klon svazku (nebo několik) k tomuto virtuálnímu počítači přes iSCSI.
    :::column-end:::
:::row-end:::

### <a name="deploy-an-azure-vm"></a>Nasazení virtuálního počítače Azure

Virtuální počítač s Windows serverem v Azure je stejný jako StorSimple 8020, což je dočasná infrastruktura, která je nezbytná jenom během migrace.
Konfigurace virtuálního počítače, který nasazujete, závisí hlavně na počtu položek (soubory a složky), které budete synchronizovat. Pokud máte nějaké obavy, doporučujeme, abyste se s vyšší konfigurací výkonu přečetli.

Jeden Windows Server může synchronizovat až 30 sdílených souborů Azure.
Specifikace, které se rozhodnete potřebovat, zahrnují každou sdílenou složku/cestu nebo kořen svazku StorSimple a počet položek (soubory a složky).

Celková velikost dat je menší než kritická hodnota – jedná se o počet položek, které potřebujete k přizpůsobení specifikací počítače.

* [Naučte se, jak nastavit systém Windows Server podle počtu položek (soubory a složky), které je třeba synchronizovat.](storage-sync-files-planning.md#recommended-system-resources)

    **Všimněte si prosím:** Dříve propojený článek prezentuje tabulku s rozsahem pro paměť serveru (RAM). Orientovat se k velkému počtu virtuálních počítačů Azure. Můžete orientovat směrem k menšímu počtu místních počítačů.

* [Přečtěte si, jak nasadit virtuální počítač se systémem Windows Server.](../../virtual-machines/windows/quick-create-portal.md)

> [!IMPORTANT]
> Ujistěte se, že je virtuální počítač nasazený ve stejné oblasti Azure jako virtuální zařízení StorSimple 8020. Pokud v rámci této migrace potřebujete také změnit oblast cloudových dat z oblasti, kterou už dnes ukládáte, můžete ji v pozdějším kroku zřídit při zřizování sdílených složek Azure.

> [!IMPORTANT]
> K místnímu zařízení StorSimple se často používá místní Windows Server. V takové konfiguraci je možné povolit funkci[odstranění duplicitních dat](https://docs.microsoft.com/windows-server/storage/data-deduplication/install-enable)na tomto Windows serveru. **Pokud jste pro data StorSimple použili odstranění duplicitních dat, ujistěte se, že na tomto virtuálním počítači Azure je taky povolená funkce odstranění duplicitních dat.** Nepleťte si tuto odstranění duplicitních dat na úrovni souborů pomocí StorSimples integrovaného odstranění duplicitních dat na úrovni bloku, pro které není nutná žádná akce.

> [!IMPORTANT]
> Pokud chcete optimalizovat výkon, nasaďte pro svůj cloudový virtuální počítač **Rychlý disk s operačním systémem** . Databázi synchronizace na disku s operačním systémem budete ukládat na všechny vaše datové svazky. Kromě toho se ujistěte, že jste vytvořili **velký disk s operačním systémem**. V závislosti na počtu položek (soubory a složky) na svazcích StorSimple může disk s operačním systémem vyžadovat **několik stovek GIB** místa, aby se synchronizovaná databáze mohla přizpůsobit.

### <a name="expose-the-storsimple-8020-volumes-to-the-azure-vm"></a>Zveřejňujte svazky StorSimple 8020 na virtuálním počítači Azure.

V této fázi připojujete jeden nebo několik StorSimple svazků z virtuálního zařízení 8020 přes iSCSI k virtuálnímu počítači s Windows serverem, který jste zřídili.

> [!IMPORTANT]
> V následujících článcích dokončete pouze část **získat soukromou IP adresu cloudového zařízení** a **Připojte se přes oddíly iSCSI** a vraťte se k tomuto článku.

1. [Získání privátní IP adresy pro cloudové zařízení](../../storsimple/storsimple-8000-cloud-appliance-u2.md#get-private-ip-for-the-cloud-appliance)
2. [Připojení přes iSCSI](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-2-summary"></a>Souhrn fáze 2

Teď, když jste dokončili fázi 2, máte následující: 

* Zřízení virtuálního počítače s Windows serverem ve stejné oblasti, jako je třeba zařízení s virtuálním StorSimple 8020
* Vystavte všechny použitelné svazky z 8020 k virtuálnímu počítači s Windows serverem přes iSCSI.
* Teď byste měli vidět obsah souborů a složek, když použijete Průzkumníka souborů na virtuálním počítači serveru na připojených svazcích.

Až dokončíte tyto kroky pro všechny svazky, které potřebují migraci, přejděte ke fázi 3.

## <a name="phase-3-set-up-azure-file-shares-and-get-ready-for-azure-file-sync"></a>Fáze 3: nastavte sdílené složky Azure a připravte se na Azure File Sync

:::row:::
    :::column:::
        ![Obrázek, který ukazuje nutnost určit a zřídit počet sdílených složek Azure a vytvořit místní Windows Server jako náhradu zařízení StorSimple.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-3.png)
    :::column-end:::
    :::column:::
        V této fázi určíte a zřídíte několik sdílených složek Azure, vytvoříte místní Windows Server jako náhradu zařízení StorSimple a nakonfigurujete tento server pro Azure File Sync. 
    :::column-end:::
:::row-end:::

### <a name="map-your-existing-namespaces-to-azure-file-shares"></a>Mapování stávajících oborů názvů ke sdíleným složkám Azure

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="deploy-azure-file-shares"></a>Nasazení sdílených složek Azure

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

> [!TIP]
> Pokud potřebujete změnit oblast Azure z aktuální oblasti, ve které jsou uložená data StorSimple, zajistěte, aby se sdílené složky Azure zřídily v nové oblasti, kterou chcete použít. Oblast se určíte tak, že ji vyberete při zřizování účtů úložiště, které uchovávají sdílené složky Azure. Ujistěte se také, že Azure File Sync prostředek, který budete dále zřizovat, je ve stejné, nové oblasti.

### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Nasazení cloudového prostředku Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Pokud potřebujete změnit oblast Azure z aktuální oblasti, ve které se data StorSimple nacházejí, pak jste v nové oblasti zřídili účty úložiště pro sdílené složky Azure. Když nasadíte tuto službu synchronizace úložiště, ujistěte se, že jste vybrali stejnou oblast.

### <a name="deploy-an-on-premises-windows-server"></a>Nasazení místního Windows serveru

* Vytvořte Windows Server 2019 – minimálně 2012R2 jako virtuální počítač nebo fyzický server. Podporuje se taky cluster Windows serveru s podporou převzetí služeb při selhání. Nepoužívejte Server, který jste pravděpodobně nastavili na StorSimple 8100 nebo 8600.
* Zřídit nebo přidat přímo připojené úložiště (DAS ve srovnání se serverem NAS, který není podporován).

Osvědčeným postupem je poskytnout novému systému Windows Server stejné nebo větší množství úložiště než zařízení StorSimple 8100 nebo 8600, které je místně k dispozici pro ukládání do mezipaměti. Windows Server budete používat stejným způsobem jako zařízení StorSimple, pokud má stejné množství úložiště jako zařízení, pak by prostředí pro ukládání do mezipaměti mělo být podobné, pokud není stejné.
Úložiště můžete přidat nebo odebrat z Windows serveru. To vám umožní škálovat velikost místního svazku a velikost místního úložiště dostupného pro ukládání do mezipaměti.

### <a name="prepare-the-windows-server-for-file-sync"></a>Příprava Windows serveru na synchronizaci souborů

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="configure-azure-file-sync-on-the-windows-server"></a>Konfigurace Azure File Sync na Windows serveru

Váš registrovaný místní Windows Server musí být pro tento proces připravený a připojený k Internetu.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Nezapomeňte zapnout vrstvení cloudu!** Vrstvení cloudu je funkce nástroje AFS, která umožňuje místnímu serveru mít menší kapacitu úložiště než je uložená v cloudu, ale má úplný obor názvů k dispozici. Místně zajímavá data se ukládají také místně do mezipaměti pro zajištění rychlého přístupu v místním prostředí. Další příčinou toho, že v tomto kroku zapnete vrstvení cloudu, je, že v této fázi nechcete synchronizovat obsah souboru. v tuto chvíli by se teď měl posunout jenom obor názvů.

## <a name="phase-4-configure-the-azure-vm-for-sync"></a>Fáze 4: konfigurace virtuálního počítače Azure pro synchronizaci

:::row:::
    :::column:::
        ![Obrázek, který ukazuje, jak získáte připojení k virtuálnímu počítači pomocí Azure File Sync a začátek prvního posunutí souborů z klonů svazků StorSimple.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-4.png)
    :::column-end:::
    :::column:::
        Tato fáze se týká vašeho virtuálního počítače Azure s připojeným iSCSI, první klony svazků. Během této fáze se virtuální počítač připojí přes Azure File Sync a spustí se první kolo přesunutí souborů z klonů svazků StorSimple.
        
    :::column-end:::
:::row-end:::

Již jste nakonfigurovali místní server, který nahradí vaše zařízení StorSimple 8100 nebo 8600 pro Azure File Sync. 

Konfigurace virtuálního počítače Azure je skoro stejný proces s jedním dalším krokem. Následující kroky vás provedou procesem.

> [!IMPORTANT]
> Je důležité, aby virtuální počítač Azure **nakonfigurovaný s povoleným vrstvou cloudu.** Během migrace budete vyměňovat svazek tohoto serveru s novějšími klony svazků. Vrstvení cloudu nemá žádné výhody a nároky na využití procesoru, měli byste se vyhnout.

1. [Nasaďte agenta AFS. (viz předchozí část)](#prepare-the-windows-server-for-file-sync)
2. [Příprava virtuálního počítače na Azure File Sync.](#get-the-vm-ready-for-azure-file-sync)
3. [Konfigurace synchronizace](#configure-azure-file-sync-on-the-azure-vm)

### <a name="get-the-vm-ready-for-azure-file-sync"></a>Příprava virtuálního počítače na Azure File Sync

Azure File Sync se používá k přesunutí souborů z připojených svazků iSCSI StorSimple do cílových sdílených složek Azure.
Během tohoto procesu migrace připojíte k VIRTUÁLNÍmu počítači několik klonů svazků za stejné písmeno jednotky. Azure File Sync musí být nakonfigurovaná tak, aby se zobrazila další klonování svazků, kterou jste připojili jako novější verzi souborů a složek, a aktualizovat sdílené složky Azure připojené přes Azure File Sync. 

> [!IMPORTANT]
> Aby to fungovalo, musí být na serveru před konfigurací Azure File Sync nastaven klíč registru.

1. Na systémové jednotce virtuálního počítače vytvořte nový adresář. Informace o Azure File Sync bude nutné zachovat místo v případě klonování připojených svazků. Příklad: `"C:\syncmetadata"`
2. Otevřete příkaz regedit a vyhledejte následující podregistr registru: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync`
3. Vytvořte nový klíč typu String s názvem: ***MetadataRootPath***
4. Nastavte úplnou cestu k adresáři, který jste vytvořili na systémovém svazku, například: `C:\syncmetadata"`

### <a name="configure-azure-file-sync-on-the-azure-vm"></a>Konfigurace Azure File Sync na virtuálním počítači Azure

Tento krok je podobný předchozímu oddílu, který popisuje, jak konfigurovat AFS na místním serveru.

Rozdíl je, že na tomto serveru nesmíte povolit vytváření vrstev cloudu a potřebujete zajistit, aby byly správné složky připojené ke správným sdíleným složkám Azure. Jinak se pojmenování sdílených složek Azure a obsah dat neshoduje a neexistuje žádný způsob, jak přejmenovat prostředky cloudu nebo místní složky, aniž by bylo nutné znovu konfigurovat synchronizaci.

Informace o [tom, jak nakonfigurovat Azure File Sync na Windows serveru](#configure-azure-file-sync-on-the-windows-server), najdete v předchozí části.

### <a name="step-4-summary"></a>Krok 4 – souhrn

V tuto chvíli jste úspěšně nakonfigurovali Azure File Sync na VIRTUÁLNÍm počítači Azure, které jste připojili k klonům svazků StorSimple přes iSCSI.
Data se teď přenášejí z virtuálního počítače Azure do různých sdílených složek Azure a ze svého místního Windows serveru se zobrazí plně už vás unavuje obor názvů.

> [!IMPORTANT]
> Ujistěte se, že v tuto chvíli nejsou k dispozici žádné změny, nebo uživatel neudělí přístup k Windows serveru.

U počátečních dat klonování svazku přes virtuální počítač Azure do sdílených složek Azure může trvat dlouhou dobu, potenciálně týdny. Odhad doby, kterou bude trvat, je obtížné a závisí na mnoha faktorech. Hlavně rychlost, s jakou má virtuální počítač Azure přístup k souborům na svazcích StorSimple a jak může rychlá Azure File Sync zpracovat soubory a složky, které potřebují synchronizovat. 

Na základě zkušeností můžeme předpokládat, že šířka pásma, tedy i skutečná velikost dat, hraje podřízenou roli. Doba, po kterou bude tento nebo jakýkoli následný kulatý přenos, bude většinou závislá na počtu položek, které lze zpracovat za sekundu. Takže například 1 TiB se soubory a složkami 100 000 bude pravděpodobně dokončeno pomalejší než 1 TiB pouze 50 000 souborů a složek.

## <a name="phase-5-iterate-through-multiple-volume-clones"></a>Fáze 5: iterace na více klonech svazků

:::row:::
    :::column:::
        ![Obrázek, který ukazuje, jak minimalizovat prostoje s využitím více klonů svazků a informace o tom, kdy se synchronizace provádí.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-5.png)
    :::column-end:::
    :::column:::
        Jak je popsáno v předchozí fázi, počáteční synchronizace může trvat dlouhou dobu. Uživatelé a aplikace stále přistupují k místnímu zařízení StorSimple 8100 nebo 8600. To znamená, že se změny načítají a každý den je větší rozdíl mezi živými daty a počáteční klonou svazku, a to v současné době migrace formulářů. V této části se dozvíte, jak minimalizovat prostoje s využitím více klonů svazků a o tom, kdy se synchronizace provádí.
    :::column-end:::
:::row-end:::

Omlouváme se, ale proces migrace není okamžitý. To znamená, že výše uvedená Delta k živým datům je nenevyhnutelný důsledek. Dobrá zpráva je, že můžete zopakovat proces připojování nových klonů svazků. Rozdílové rozdíly klonování svazků budou postupně menší. Nakonec se synchronizace dokončí po uplynutí doby, kterou považujete za přijatelné, aby se uživatelé a aplikace offline převzali na místní Windows Server.

Opakujte následující kroky, dokud nebude synchronizace dokončena rychle, ale budete mít k dispozici uživatele a aplikace offline:

1. [Určení synchronizace pro daný svazek svazku se dokončilo.](#determine-when-sync-is-done)
2. [Vezměte nové klony svazků a připojte je k virtuálnímu zařízení 8020.](#the-next-volume-clones)
3. [Určete, kdy se synchronizace provádí.](#determine-when-sync-is-done)
4. [Strategie oříznutí](#cut-over-strategy)

### <a name="the-next-volume-clones"></a>Další klony svazků

Provedli jsme pořizování klonů svazků dříve v tomto článku.
Tato fáze má dvě akce:

1. [Vytvořit klon svazku](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)
2. [Připojit tento klon svazku (viz výše)](#use-the-volume-clone)

### <a name="determine-when-sync-is-done"></a>Určení, kdy se synchronizace provede

Po dokončení synchronizace můžete zastavit měření času a zjistit, jestli je potřeba zopakovat proces pořizování klonování svazku a jeho připojení, nebo pokud trvala synchronizace času s poslední klonou svazku dostatečně malým.

Chcete-li určit synchronizaci, proveďte následující:

1. Otevřete Prohlížeč událostí a přejděte do části **aplikace a služby** .
2. Navigace a otevírání **Microsoft\FileSync\Agent\Telemetry**
3. Vyhledejte nejnovější **událost 9102**, která odpovídá dokončené relaci synchronizace.
4. Vyberte **Podrobnosti** a potvrďte, že je hodnota **SyncDirection** **nahrána** .
5. Zkontrolujte **HRESULT** a potvrďte, že se zobrazí **0**. To znamená, že relace synchronizace byla úspěšná. Pokud je HResult nenulová hodnota, při synchronizaci došlo k chybě. Pokud je **PerItemErrorCount** větší než 0, některé soubory nebo složky se nesynchronizují správně. Je možné mít hodnotu HResult 0, ale PerItemErrorCount, která je větší než 0. V tuto chvíli se nemusíte starat o PerItemErrorCount. Tyto soubory budeme zachytit později. Pokud je tento počet chyb značný, tisíce položek, obraťte se na zákaznickou podporu a požádejte ho o připojení k Azure File Sync skupině produktů pro přímé doprovodné materiály na nejlepší, další fáze.
6. Zkontrolujte, jestli se na řádku zobrazilo několik událostí 9102 s HResult 0. To znamená, že synchronizace je pro tento klon svazku dokončena.

### <a name="cut-over-strategy"></a>Strategie oříznutí

1. Zjistěte, jestli je synchronizace z klonu svazku teď dostatečně rychlá. (Malý rozdíl byl dostatečně malý.)
2. Převeďte zařízení StorSimple do režimu offline.
3. Poslední příkaz Robocopy.

Změřte čas a zjistěte, jestli se synchronizace z nedávného klonu svazků může dokončit v časovém intervalu dostatečně malým, což vám v systému umožní mít za následek výpadky.

Teď je čas zakázat uživateli přístup k zařízení StorSimple. Žádné další změny. Bylo zahájeno výpadky.
Je potřeba zařízení opustit online a připojit, ale teď ho musí zamezit.

Ve fázi 6 budete zachytit všechny rozdíly v živých datech od posledního klonu svazku.

## <a name="phase-6-a-final-robocopy"></a>Fáze 6: poslední příkaz Robocopy

V tomto okamžiku existují dva rozdíly mezi místním Windows serverem a zařízením StorSimple 8100 nebo 8600:

1. Mohou existovat soubory, které nebyly synchronizovány (viz **PerItemErrors** z protokolu událostí výše)
2. Zařízení StorSimple má naplněnou mezipaměť vs. systém Windows Server má v tuto chvíli uložený pouze obor názvů bez obsahu souboru uložený v místním prostředí.

![Obrázek, který ukazuje, jak byla mezipaměť systému Windows Server dokončena do stavu zařízení a zajišťují, že žádný soubor není v konečném souboru Robocopy.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-6.png)

Mezipaměť systému Windows Server můžeme převést do stavu zařízení a zajistěte, aby žádný soubor nezůstal za poslední součástí nástroje Robocopy.

> [!CAUTION]
> Je nezbytné, aby příkaz Robocopy, který sledujete, byl přesně popsaný níže. Chtěli bychom jenom zkopírovat soubory, které jsou místní a soubory, které se před ním nepřesunuly prostřednictvím postupu klonování svazku a synchronizace. Až se migrace dokončí, můžeme vyřešit problémy, proč se nesynchronizují později. (Další informace najdete v tématu [řešení potíží s Azure File Sync](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing). Jsou to pravděpodobně netisknutelné znaky v názvech souborů, které nebudete mít při odstranění.)

Příkaz Robocopy:

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Pozadí

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Umožňuje nástroji Robocopy spustit vícevláknové procesy. Výchozí hodnota je 8, maximální hodnota je 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Výstupuje stav do souboru protokolu jako UNICODE (přepíše existující protokol).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Provede výstup do okna konzoly. Používá se ve spojení s výstupem do souboru protokolu.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Spustí příkaz Robocopy ve stejném režimu, který používá zálohovací aplikace. Umožňuje nástroji Robocopy přesunout soubory, ke kterým aktuální uživatel nemá oprávnění.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Umožňuje, aby Robocopy braly jenom rozdíly mezi zdrojem (StorSimple zařízením) a cílem (adresář Windows serveru).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      věrnost kopírování souborů (výchozí je/COPY: DAT), příznaky kopírování: D = data, A = atributy, T = časová razítka, S = zabezpečení – seznamy řízení přístupu NTFS, O = informace o vlastníkovi, U = informace o auditování
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      Kopírovat všechny informace o souboru (ekvivalentní k/COPY: DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      věrnost pro kopii adresářů (výchozí hodnota je/DCOPY: DA), příznaky kopírování: D = data, A = atributy, T = časová razítka
   :::column-end:::
:::row-end:::

Tento příkaz Robocopy byste měli spustit pro každý adresář na Windows serveru jako cíl, který jste nakonfigurovali pomocí synchronizace souborů na soubor Azure.

Více těchto příkazů můžete spustit paralelně.
Po dokončení tohoto kroku Robocopy můžete uživatelům a aplikacím dovolit přístup k Windows serveru stejným způsobem, jako by StorSimple zařízení.

Informace o tom, jestli byly soubory ponechány, naleznete v souborech protokolu Robocopy. Pokud by měly existovat problémy, můžete je ve většině případů vyřešit po dokončení migrace a uživatelé a aplikace budou znovu připojeni k vašemu systému Windows Server. Pokud potřebujete opravit nějaké problémy, udělejte to před fází 7.

Je pravděpodobně potřeba vytvořit sdílené složky SMB na Windows serveru, které jste měli k StorSimple datům. Tento krok můžete provést ještě před tím, než se sem zařadí čas, ale je potřeba zajistit, aby před tímto bodem nedocházelo k žádným změnám souborů v systému Windows Server.

Pokud máte nasazení DFS-N, můžete ho nasměrovat na umístění DFN-Namespaces na nové serverové složky. Pokud nemáte nasazení DFS-N a vaše zařízení 8100 8600 jste nastavili místně s Windows serverem, můžete tento server převzít z domény a připojit se k novému serveru Windows Server s nástrojem AFS k doméně, dát mu stejný název serveru jako starý server a stejné názvy sdílených složek, takže rozmístění na nový server zůstane pro vaše uživatele transparentní. , zásady skupiny nebo skripty.

## <a name="phase-7-deprovision"></a>Fáze 7: zrušení zřízení

Během poslední fáze, kterou jste provedli v rámci více klonů svazků, a nakonec byly schopné získat přístup uživatelů k novému Windows serveru po StorSimple zařízení do offline režimu.

Teď můžete začít se zrušením zřízení zbytečných prostředků.
Než začnete, je osvědčeným postupem, jak sledovat nové nasazení Azure File Sync v produkčním prostředí, a to pro určitý bit. Díky tomu máte možnost opravit případné problémy, se kterými se můžete setkat.

Jakmile budete spokojeni s nasazením AFS a máte pozor aspoň několik dní, můžete začít v tomto pořadí zrušit zřízení těchto prostředků:

1. Vypněte virtuální počítač Azure, který jsme použili k přesunu dat z klonů svazků do sdílených složek Azure pomocí synchronizace souborů.
2. V Azure přejdete do prostředku služby synchronizace úložiště a zrušíte registraci virtuálního počítače Azure. Tím se odebere ze všech skupin synchronizace.

    > [!WARNING]
    > **Ujistěte se, že jste si vybrali správný počítač.** Cloudový virtuální počítač je vypnutý. to znamená, že by se měl v seznamu registrovaných serverů zobrazovat jako jediný offline server. V tomto kroku nesmíte vybrat místní Windows Server. tím zrušíte jeho registraci.

3. Odstraňte virtuální počítač Azure a jeho prostředky.
4. Zakažte zařízení s virtuálním StorSimple 8020.
5. Zrušení zřízení všech prostředků StorSimple v Azure.
6. Odpojte fyzické zařízení StorSimple z datového centra.

Migrace je hotová.

## <a name="next-steps"></a>Další kroky

Získejte více znalostí o Azure File Sync. Obzvláště díky flexibilitě zásad vytváření cloudových vrstev.

Pokud vidíte v Azure Portal nebo z předchozích událostí, že některé soubory se trvale nesynchronizují, přečtěte si průvodce řešením potíží, kde najdete postup řešení těchto problémů.

* [Přehled Azure File Sync: aka.ms/AFS](https://aka.ms/AFS)
* [Vrstvení cloudu](storage-sync-cloud-tiering.md) 
* [Průvodce odstraňováním potíží s Azure File Sync](storage-sync-files-troubleshoot.md)
