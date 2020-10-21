---
title: Migrace řady StorSimple 8000 na Azure File Sync
description: Přečtěte si, jak migrovat zařízení s StorSimple 8100 nebo 8600 do Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 10/16/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: dcb690829aaed1d5a256f53766f05c922f8b2a9a
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331072"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>Migrace StorSimple 8100 a 8600 do Azure File Sync

Série StorSimple 8000 je představována buď 8100, 8600 nebo s fyzickými, místními zařízeními a jejich součástmi cloudové služby. Data z některého z těchto zařízení je možné migrovat do Azure File Syncho prostředí. Azure File Sync je výchozí a strategická dlouhodobá služba Azure, na kterou je možné StorSimple zařízení migrovat.

Série StorSimple 8000 se [dokončí do konce životnosti](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) v prosinci 2022. Je důležité zahájit plánování migrace co nejrychleji. Tento článek popisuje nezbytné kroky pro znalosti a migrace na pozadí pro úspěšnou migraci na Azure File Sync. 

## <a name="phase-1-prepare-for-migration"></a>Fáze 1: Příprava na migraci

Tato část obsahuje kroky, které byste měli provést na začátku migrace ze svazků StorSimple do sdílených složek Azure.

### <a name="inventory"></a>inventář
Když zahájíte plánování migrace, nejdříve Identifikujte všechna zařízení StorSimple a svazky, které potřebujete migrovat. Až to uděláte, můžete se rozhodnout pro vás na nejlepší cestu migrace.

* StorSimple fyzická zařízení (8000 series) se používají v této příručce k migraci. 
* Virtuální zařízení, [řada StorSimple 1200, používají jiný Průvodce migrací](storage-files-migration-storsimple-1200.md).

### <a name="migration-cost-summary"></a>Souhrn nákladů na migraci
Migrace do sdílených složek Azure ze svazků StorSimple prostřednictvím úloh transformace dat v prostředku StorSimple Data Manager jsou bezplatné. Existují však i další náklady, které mohou být účtovány během a po migraci:

* Odchozí přenos dat z **sítě:** Vaše soubory StorSimple jsou v účtu úložiště v konkrétní oblasti Azure v reálném čase. Pokud zřídíte sdílené složky Azure, které migrujete do v účtu úložiště, který je umístěný ve stejné oblasti Azure, nebudou se vám vyskytnout žádné náklady na výstup. V rámci této migrace můžete soubory přesunout do účtu úložiště v jiné oblasti. V takovém případě vám budou platit náklady na výstup.
* **Transakce sdílené složky Azure:** Po zkopírování souborů do sdílené složky Azure (jako součást migrace nebo mimo jednu) se účtují poplatky za transakce při psaní souborů a metadat. Osvědčeným postupem je během migrace spustit sdílenou složku Azure na úrovni optimalizované pro transakci. Po dokončení migrace přepněte na požadovanou úroveň. Následující fáze budou volat v příslušném bodě.
* **Mění se úroveň sdílené složky Azure:** Změna úrovně transakcí s cenami za Azure File Share. Ve většině případů bude cenově výhodnější postupovat podle pokynů z předchozího bodu.
* **Náklady na úložiště:** Když tato migrace začne kopírovat soubory do sdílené složky Azure, bude se používat a Fakturovatelné úložiště souborů Azure. 
* **StorSimple:** Dokud nebudete mít možnost zrušit zřízení zařízení StorSimple a účtů úložiště, bude docházet k StorSimple nákladům na úložiště, zálohy a zařízení.

### <a name="direct-share-access-vs-azure-file-sync"></a>Přímý přístup ke sdílené složce vs. Azure File Sync
Sdílené složky Azure otevírají celou řadu příležitostí pro vytvoření struktury nasazení souborové služby. Sdílená složka Azure je jenom sdílená složka SMB v cloudu, kterou můžete nastavit tak, aby uživatelé měli přístup přímo přes protokol SMB se známým ověřováním protokolu Kerberos a stávajícími oprávněními NTFS (seznamy řízení přístupu (ACL) souborů a složek) pracující nativně. [Přečtěte si další informace o přístupu na základě identity ke sdíleným složkám Azure](storage-files-active-directory-overview.md).

Alternativou k přímému přístupu je [Azure File Sync](https://aka.ms/AFS). Azure File Sync je přímým analogem pro schopnost StorSimple ukládat často používané soubory do mezipaměti. 

Azure File Sync je cloudová služba Microsoftu založená na dvou hlavních součástech:

* Synchronizace souborů a vrstvení cloudu.
* Sdílené složky jako nativní úložiště v Azure, které je možné používat v různých protokolech jako SMB a REST souborů. 

Sdílené složky Azure uchovávají důležité aspekty souborů na uložených souborech, jako jsou atributy, oprávnění a časová razítka. Se sdílenými složkami Azure už není potřeba, aby aplikace ani služba dokázala interpretovat soubory a složky uložené v cloudu. K těmto datům můžete nativně přistupovat prostřednictvím známých protokolů a klientů, jako je Průzkumník souborů systému Windows. Sdílené složky Azure umožňují ukládat data souborového serveru pro obecné účely a data aplikací v cloudu. Zálohování sdílené složky Azure je integrovaná funkce a dá se dál rozšířit o Azure Backup.

Tento článek se zaměřuje na kroky migrace. Pokud před migrací chcete získat další informace o Azure File Sync, doporučujeme následující články:

* [Azure File Sync – přehled](https://aka.ms/AFS "Přehled")
* [Průvodce nasazením Azure File Sync](storage-sync-files-deployment-guide.md)

### <a name="storsimple-service-data-encryption-key"></a>Šifrovací klíč dat služby StorSimple
Při prvním nastavení zařízení StorSimple vygenerovala*klíč šifrování dat služby*a vydá pokyn k bezpečnému uložení klíče.
Tento klíč se používá k šifrování všech dat v přidruženém účtu služby Azure Storage, který zařízení StorSimple ukládá do souborů.

Pro úspěšnou migraci je nutný šifrovací klíč dat služby. Teď je vhodný čas načíst tento klíč ze svých záznamů, a to pro každé zařízení v inventáři.

Pokud v záznamech nemůžete najít klíče, můžete si z zařízení načíst klíč. Každé zařízení má jedinečný šifrovací klíč.

* Soubor a žádost o podporu se Microsoft Azure prostřednictvím Azure Portal. Obsah požadavku by měl mít sériová čísla zařízení StorSimple a požadavek na načtení šifrovacího klíče dat služby. 
* Pracovník podpory StorSimple vás bude kontaktovat s požadavkem na schůzku sdílení obrazovky.
* Ujistěte se, že před zahájením schůzky se k zařízení StorSimple připojujete [přes sériovou konzolu](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) nebo prostřednictvím [vzdálené relace PowerShellu](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple). 

> [!CAUTION]
> Při rozhodování o tom, jak se připojit k zařízení StorSimple, vezměte v úvahu následující skutečnosti:
> * Připojení prostřednictvím relace HTTPS je nejbezpečnější a doporučuje se možnost.
> * Přímé připojení ke konzole sériového portu zařízení je zabezpečené, ale připojení ke konzole sériového portu přes síťové přepínače není. 
> * Připojení relace HTTP jsou možností, ale nejsou **šifrovaná** a proto se nedoporučují, pokud se nepoužívají v uzavřené, důvěryhodné síti.

### <a name="storsimple-volume-backups"></a>Zálohy svazků StorSimple
StorSimple nabízí rozdílové zálohy na úrovni svazku. Sdílené složky Azure mají taky tuto schopnost, která se nazývá snímky sdílené složky.

Rozhodněte se, jestli je v rámci migrace k dispozici také povinnost přesunout jakékoli zálohy.

> [!CAUTION]
> Zastavte se sem, pokud potřebujete migrovat zálohy ze svazků StorSimple.
>
> V současné době můžete migrovat jenom poslední zálohu svazku. Podpora migrace zálohy přijde na konec 2020.
> Pokud teď začít, nemůžete své zálohy znovu zapnout.
> V nadcházející verzi musí být zálohy přehrávají se do sdílených složek Azure od nejstarší po nejnovější, přičemž se snímky sdílené složky Azure provedou mezi.

Pokud chcete migrovat jenom živá data a nemáte žádné požadavky na zálohování, můžete pokračovat v tomto průvodci.
Pokud máte jenom velmi krátkodobý požadavek na uchovávání záloh, který představuje měsíc nebo dva, pak se můžete rozhodnout pokračovat v migraci hned a jenom zrušit zřízení prostředků StorSimple po uplynutí této doby. Tento přístup vám umožní vytvořit tolik historie zálohování na straně sdílené složky Azure, jak potřebujete. V době, kdy udržujete oba systémy, platí další náklady, což nevede k tomu, že byste měli vzít v úvahu, že budete potřebovat více než velmi krátkodobou dobu uchovávání záloh.

### <a name="map-your-existing-storsimple-volumes-to-azure-file-shares"></a>Namapujte stávající StorSimple svazky na sdílené složky Azure.
[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="number-of-storage-accounts"></a>Počet účtů úložiště
Vaše migrace bude nejspíš těžit z nasazení víc účtů úložiště, takže každý z nich bude mít menší počet sdílených složek Azure.

Pokud jsou sdílené složky vysoce aktivní (využívá mnoho uživatelů nebo aplikací), může se stát, že pouze dvě sdílené složky Azure dosáhnou limitu výkonu vašeho účtu úložiště. Z tohoto důvodu je osvědčeným postupem migrace na více účtů úložiště, z nichž každý má vlastní jednotlivé sdílené složky a obvykle není více než dvě nebo tři sdílené složky na jeden účet úložiště.

Osvědčeným postupem je nasazování účtů úložiště s jednou sdílenou složkou. Můžete seskupit více sdílených složek Azure do stejného účtu úložiště pro případ, že v nich máte archivované sdílené složky.

Tyto požadavky se vztahují i na [přímý přístup do cloudu](#direct-share-access-vs-azure-file-sync) (přes virtuální počítač nebo službu Azure), než na Azure File Sync. Pokud plánujete použít Azure File Sync jenom na těchto sdílených složkách, je v pořádku seskupení několika do jednoho účtu Azure Storage. Zvažte také, že budete chtít aplikaci najímat a přesunout do cloudu, která by pak měla přímý přístup ke sdílené složce souborů. Nebo můžete začít používat službu v Azure, která by mohla mít k dispozici vyšší počet vstupně-výstupních operací a propustnosti. 

Pokud jste vytvořili seznam sdílených složek, měli byste namapovat každou sdílenou složku na účet úložiště, ve kterém se bude nacházet.

> [!IMPORTANT]
> Ujistěte se, že jste se rozhodli v oblasti Azure, a pak každý účet úložiště a Azure File Sync prostředku odpovídají stejné oblasti, kterou jste vybrali.

### <a name="phase-1-summary"></a>Souhrn fáze 1
Na konci fáze 1:
* Máte dobrý přehled o zařízeních a svazcích StorSimple.
* Služba transformace dat je připravena k přístupu ke svazkům StorSimple v cloudu, protože jste načetli šifrovací klíč dat služby pro každé zařízení StorSimple.
* Máte plán, ve kterém se nedají migrovat svazky, ale taky to, jak namapovat svazky na příslušný počet sdílených složek Azure a účtů úložiště.

> [!CAUTION]
> Pokud potřebujete migrovat zálohy ze StorSimple svazků, **zastavte se sem**.
>
> Tento postup migrace spoléhá na nové funkce služby transformace dat, které v současné době nemůžou migrovat zálohy. Podpora migrace zálohy přijde na konec 2020.
> Teď můžete migrovat jenom živá data. Pokud teď začít, nemůžete své zálohy znovu zapnout.
> Zálohy musí být přehrávají se ke sdíleným složkám Azure od nejstarší po nejnovější a k živým datům, a to pomocí snímků sdílené složky Azure mezi.

Pokud chcete migrovat jenom živá data a nemáte žádné požadavky na zálohování, můžete pokračovat v tomto průvodci.

## <a name="phase-2-deploying-azure-storage-and-migration-resources"></a>Fáze 2: nasazení úložiště Azure a prostředků migrace

Tato část popisuje požadavky týkající se nasazení různých typů prostředků, které jsou potřeba v Azure. Některé budou uchovávat data po migraci a některé jsou nutné pouze pro migraci. Nespouštějte nasazování prostředků, dokud nedokončíte plán nasazení. Je obtížné, někdy nemožné, změnit některé aspekty vašich prostředků Azure po jejich nasazení.

### <a name="deploy-storage-accounts"></a>Nasazení účtů úložiště
Pravděpodobně budete potřebovat nasadit několik účtů úložiště Azure. Každý z nich bude obsahovat menší počet sdílených složek Azure podle plánu nasazení, který je v předchozí části tohoto článku hotový. Azure Portal k [nasazení vašich plánovaných účtů úložiště](../common/storage-account-create.md#create-a-storage-account). Vezměte v úvahu následující základní nastavení pro každý nový účet úložiště:

##### <a name="subscription"></a>Předplatné
Můžete použít stejné předplatné, jaké jste použili pro nasazení StorSimple, nebo jiné. Jediným omezením je, že vaše předplatné musí být ve stejném tenantovi služby Azure AD jako StorSimple předplatné. Před zahájením migrace zvažte možnost přesunout si předplatné StorSimple do správného tenanta. Můžete přesunout jenom celé předplatné, jednotlivé prostředky StorSimple nejde přesunout do jiného tenanta nebo předplatného.

##### <a name="resource-group"></a>Skupina prostředků
Skupiny prostředků pomáhají s organizací prostředků a oprávněními pro správu správců. Přečtěte si další informace o [skupinách prostředků v Azure](../../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group).

##### <a name="storage-account-name"></a>Název účtu úložiště
Název účtu úložiště se stane součástí adresy URL a má určitá omezení znaků. V případě zásad vytváření názvů zvažte, že názvy účtů úložiště musí být v celém světě jedinečné, povolit jenom malá písmena a čísla, vyžadovat délku 3 až 24 znaků a nepovolujte speciální znaky, jako jsou pomlčky nebo podtržítka. Viz také: [pravidla pro pojmenovávání prostředků v Azure Storage](../../azure-resource-manager/management/resource-name-rules.md#microsoftstorage).

##### <a name="location"></a>Umístění
Lokalita "umístění" nebo oblast Azure v účtu úložiště je velmi důležitá. Pokud používáte Azure File Sync, všechny vaše účty úložiště musí být ve stejné oblasti jako prostředek služby synchronizace úložiště. Oblast Azure, kterou si vyberete, by měla být pro místní servery nebo uživatele blízko nebo po středu. Po nasazení prostředku nemůžete změnit jeho oblast.

Můžete vybrat jinou oblast, ze které se aktuálně nacházejí data StorSimple (účet úložiště).

> [!IMPORTANT]
> Pokud z aktuálního umístění účtu úložiště StorSimple vyberete jinou oblast, budou se během migrace [platit poplatky za výstup](https://azure.microsoft.com/pricing/details/bandwidth) . Data ponechají oblast StorSimple a zadají novou oblast účtu úložiště. Pokud zůstanete ve stejné oblasti Azure, nemusíte platit žádné poplatky za šířku pásma.

##### <a name="performance"></a>Výkon
Máte možnost vybrat Storage úrovně Premium (SSD) pro sdílené složky Azure nebo úložiště Standard. Standardní úložiště obsahuje [několik vrstev pro sdílenou složku](storage-how-to-create-file-share.md#changing-the-tier-of-an-azure-file-share). Storage úrovně Standard je správnou možností pro většinu zákazníků, kteří migrují z StorSimple.

Pořád si nejste jisti?
* Pokud potřebujete [výkon služby Azure File Share Premium](storage-files-planning.md#understanding-provisioning-for-premium-file-shares), vyberte Premium Storage.
* Pro úlohy souborového serveru pro obecné účely, včetně aktivních dat a archivovaných dat, vyberte Storage úrovně Standard. Pokud se Azure File Sync jedinou úlohou sdílené složky v cloudu, vyberte možnost standardní úložiště.

##### <a name="account-kind"></a>Druh účtu
* Pro úložiště Standard vyberte: *StorageV2 (obecné účely v2)*
* V případě sdílených složek Premium vyberte: File *Storage* .

##### <a name="replication"></a>Replikace
K dispozici je několik nastavení replikace. Přečtěte si další informace o různých typech replikace.

Vyberte jenom jednu z následujících dvou možností:
* *Místně redundantní úložiště (LRS)*
* *Redundantní úložiště zóny (ZRS)* – to není k dispozici ve všech oblastech Azure.

> [!NOTE]
> Pouze LRS a ZRS typy redundance jsou kompatibilní s velkými sdílenými složkami Azure pro 100TiB kapacitu.

Globálně redundantní úložiště (všechny variace) se v tuto chvíli nepodporuje. Svůj typ redundance můžete přepínat později a GRS, když se na něj podpora dorazí v Azure.

##### <a name="enable-100tib-capacity-file-shares"></a>Povolit sdílené složky 100TiB kapacity

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png" alt-text="Obrázek znázorňující kartu Upřesnit v Azure Portal pro vytvoření účtu úložiště.":::
    :::column-end:::
    :::column:::
        V části *Upřesnit* v Průvodci vytvořením nového účtu úložiště v Azure Portal můžete povolit podporu *velkých sdílených složek* v tomto účtu úložiště. Pokud tato možnost není k dispozici, pravděpodobně jste vybrali špatný typ redundance. Ujistěte se, že jste vybrali možnost LRS nebo ZRS, aby tato možnost byla k dispozici.
    :::column-end:::
:::row-end:::

U rozsáhlých sdílených složek s 100TiB kapacitou má několik výhod:
* Výkon se výrazně zvyšuje ve srovnání s menšími 5TiB sdílenými složkami. (příklad: 10 opakování IOPS)
* Vaše migrace se dokončí významně rychleji.
* Ujistěte se, že sdílená složka bude mít dostatečnou kapacitu pro uložení všech dat, která do něj migrujete.
* Pojednává o budoucí nárůst.

### <a name="azure-file-shares"></a>Sdílené složky Azure
Po vytvoření účtů úložiště můžete přejít do části *"sdílení souborů"* v účtu úložiště a nasadit příslušný počet sdílených složek Azure podle plánu migrace z fáze 1. Vezměte v úvahu následující základní nastavení pro nové sdílené složky v Azure:

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-share.png" alt-text="Obrázek znázorňující kartu Upřesnit v Azure Portal pro vytvoření účtu úložiště.":::
    :::column-end:::
    :::column:::
        </br>**Název**</br>Jsou podporována malá písmena, číslice a spojovníky.</br></br>**Kvóta**</br>Kvóta je tady srovnatelná s vynucenou kvótou SMB na Windows serveru. Osvědčeným postupem je nenastavit kvótu na tomto místě, protože migrace a další služby selžou po dosažení kvóty.</br></br>**Úroveň**</br>Vyberte *transakci optimalizovanou* pro novou sdílenou složku. Během migrace bude provedeno mnoho transakcí a cenově výhodnější je později změnit úroveň na úroveň, která je nejvhodnější pro vaše zatížení.
    :::column-end:::
:::row-end:::

### <a name="storsimple-data-manager"></a>Správce dat StorSimple
Prostředek Azure, který bude obsahovat úlohy migrace, se nazývá *"StorSimple data Manager"*. Klikněte na nový prostředek a vyhledejte ho a pak klikněte na *vytvořit*.

Jedná se o dočasný prostředek, který se používá k orchestraci, a až se vaše migrace dokončí, zrušíte jeho zřízení. Měl by se nasadit do stejného předplatného, skupiny prostředků a oblasti jako účet úložiště StorSimple.

### <a name="azure-file-sync"></a>Synchronizace souborů Azure
Azure File Sync (AFS) umožňuje přidat místní ukládání nejčastěji používaných souborů do mezipaměti. Podobně jako možnosti ukládání do mezipaměti StorSimple nabízí funkce vrstvení cloudu AFS latenci místního přístupu s lepší kontrolou dostupné kapacity mezipaměti na Windows serveru a v synchronizaci s více lokalitami. Pokud je místní mezipaměť vaším cílem, potom v místní síti Připravte virtuální počítač s Windows serverem (jsou podporované taky fyzické servery a clustery s podporou převzetí služeb při selhání) s dostatečnou kapacitou přímo připojeného úložiště (DAS). 

> [!IMPORTANT]
> Ještě neinstalujte Azure File Sync, je nejlepší nastavit Azure File Sync po dokončení migrace vaší sdílené složky. Nasazení AFS by nemělo být spuštěno před fází 4 migrace.

### <a name="phase-2-summary"></a>Souhrn fáze 2
Na konci fáze 2 budete mít nasazené účty úložiště, všechny sdílené složky Azure ve všech těchto sdílených složkách a také prostředky StorSimple Data Manager. Když ve skutečnosti nakonfigurujete úlohy migrace, použijete ji ve fázi 3.

## <a name="phase-3-create-and-run-a-migration-job"></a>Fáze 3: vytvoření a spuštění úlohy migrace
Tato část popisuje, jak nastavit úlohu migrace a pečlivě namapovat adresáře na svazku StorSimple, které by se měly zkopírovat do cílové sdílené složky Azure, kterou vyberete. Začněte tím, že přejdete na StorSimple Data Manager, v nabídce vyhledáte **Definice úloh** a vyberete **+ definice úlohy**. Výchozím typem cílového úložiště je: *sdílená složka Azure*.

![Typy úloh migrace řady StorSimple 8000](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job-type.png "Snímek obrazovky s definicemi úloh Azure Portal s otevřeným dialogovým oknem definice úlohy, který se zeptá na typ úlohy: kopírování do sdílené složky nebo do kontejneru objektů BLOB.")

> [!IMPORTANT]
> Před spuštěním jakékoli úlohy migrace zastavte všechna automaticky naplánovaná zálohování svazků StorSimple.

:::row:::
    :::column:::
        ![Úloha migrace řady StorSimple 8000](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job.png "Snímek obrazovky nového formuláře pro vytvoření úlohy pro úlohu transformace dat.")
    :::column-end:::
    :::column:::
        **Název definice úlohy**</br>Tento název by měl být indikativní pro sadu souborů, které přesouváte. Dobrým zvykem je poskytnout podobný název jako sdílená složka Azure. </br></br>**Umístění, kde se úloha spouští**</br>Když vybíráte určitou oblast, musíte vybrat stejnou oblast jako účet úložiště StorSimple, nebo pokud tato oblast není k dispozici, a pak ji zavřít. </br></br><h3>Zdroj</h3>**Zdrojové předplatné**</br>Vyberte předplatné, ve kterém uložíte StorSimple Device Manager prostředku. </br></br>**Prostředek StorSimple**</br>Vyberte StorSimple Device Manager vaše zařízení je zaregistrované ve službě. </br></br>**Datový šifrovací klíč služby**</br>V tomto [článku najdete předchozí část](#storsimple-service-data-encryption-key), pokud nemůžete najít klíč ve svých záznamech. </br></br>**Zařízení**</br>Vyberte zařízení StorSimple, které obsahuje svazek, na který chcete migrovat. </br></br>**Svazek**</br>Vyberte zdrojový svazek. Později se můžete rozhodnout, jestli chcete migrovat celý svazek nebo podadresáře do cílové sdílené složky Azure. </br></br><h3>Cíl</h3>Jako cíl této úlohy migrace vyberte předplatné, účet úložiště a sdílenou složku Azure.
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> K provedení migrace se použije poslední záloha svazku. Zajistěte, aby byla k dispozici minimálně jedna záloha svazku nebo aby se úloha nezdařila. Také se ujistěte, že nejnovější záloha je poměrně nedávná, takže rozdíl v živém sdílení zůstane co nejmenším možným způsobem. **Před** spuštěním úlohy, kterou jste právě vytvořili, to může být ruční aktivace a dokončení další zálohy svazku.

### <a name="directory-mapping"></a>Mapování adresáře
To je pro úlohu migrace volitelné. Pokud pole necháte prázdné, **všechny** soubory a složky v kořenovém adresáři StorSimple svazku se přesunou do kořenového adresáře vaší cílové sdílené složky Azure. Ve většině případů není při ukládání obsahu celého svazku do sdílené složky Azure nejlepší přístup. Je často lepší rozdělit obsah svazku mezi několik sdílených složek v Azure. Pokud jste ještě nevytvořili plán, podívejte se nejprve do této části: [namapujte svazek StorSimple na sdílené složky Azure](#map-your-existing-storsimple-volumes-to-azure-file-shares) .

V rámci vašeho plánu migrace jste se rozhodli, že složky na StorSimple svazku musí být rozdělené mezi několik sdílených složek Azure. Pokud je to tento případ, můžete provést rozdělení podle:
1. Při definování více úloh pro migraci složek na jednom svazku bude mít každý z nich stejný StorSimple zdroj svazku, ale jako cíl bude jiná sdílená složka Azure.
1. Zadáním přesně toho, které složky ze svazku StorSimple je potřeba migrovat do zadané sdílené složky, pomocí oddílu *mapování adresáře* ve formuláři pro vytvoření úlohy a podle konkrétní [sémantiky mapování](#semantic-elements).

> [!IMPORTANT]
> Výrazy Path a Mapping v tomto formuláři nelze ověřit při odeslání formuláře. Pokud jsou mapování nesprávně zadána, úloha může buď zcela selhat, nebo způsobit nežádoucí výsledek. V takovém případě je obvykle nejlepší odstranit sdílenou složku Azure, vytvořit ji znovu a pak opravit příkazy mapování v nové úloze migrace pro sdílenou složku. Spuštění nové úlohy s pevnými příkazy mapování může opravit vynechané složky a přenést je do existující sdílené složky. Tímto způsobem však lze vyřešit pouze složky, které byly vynechány z důvodu chybného pravopisu v cestě.

#### <a name="semantic-elements"></a>Sémantické elementy
Mapování je vyjádřeno zleva doprava: [\Source cesta] \> [cesta \target].

|Sémantický znak          | Význam  |
|:---------------------------|:---------|
| **\\**                     | indikátor na kořenové úrovni        |
| **\>**                     | [Source] a [target] – operátor mapování        |
|**\|** nebo RETURN (nový řádek) | oddělovač dvou instrukcí pro mapování složky </br>Alternativně můžete tento znak vynechat a stisknutím klávesy ENTER získat další výraz mapování na svém vlastním řádku.        |

### <a name="examples"></a>Příklady
Přesune obsah složky "uživatelská data" do kořenového adresáře cílové sdílené složky:
``` console
\User data > \\
```
Přesune celý obsah svazku do nové cesty na cílové sdílené složce:
``` console
\ \> \Apps\HR tracker
```
Přesune obsah zdrojové složky do nové cesty na cílové sdílené složce:
``` console
\HR resumes-Backup \> \Backups\HR\resumes
```
Seřadí několik zdrojových umístění do nové adresářové struktury:
``` console
\HR\Candidate Tracker\v1.0 > \Apps\Candidate tracker
\HR\Candidates\Resumes > \HR\Candidates\New
\Archive\HR\Old Resumes > \HR\Candidates\Archived
```

### <a name="semantic-rules"></a>Sémantická pravidla
* Vždy zadejte cesty ke složkám relativně k kořenové úrovni. 
* Zahajte každou cestu ke složce se indikátorem kořenové úrovně " \" . 
* Nezahrnujte písmena jednotek. 
* Při zadávání více cest se zdrojové nebo cílové cesty nesmí překrývat:</br>
   Neplatný příklad překrytí zdrojové cesty:</br>
    *\\folder\1 > \\ složku*</br>
    *\\Složka \\ 1 \\ 2 > \\ složky*</br>
   Neplatný příklad překrytí cílové cesty:</br>
   *\\> složky \\*</br>
   *\\složky > \\*</br>
* Zdrojové složky, které neexistují, budou ignorovány. 
* Vytvoří se struktury složek, které v cíli neexistují. 
* Podobně jako u Windows: názvy složek rozlišují malá a velká písmena, ale zachovává se.

> [!NOTE]
> úloha transformace nebude kopírovat obsah složky "*\System Volume Information*" a "*$recycle. bin*" na svazku StorSimple.

### <a name="phase-3-summary"></a>Souhrn fáze 3
Na konci fáze 3 budete spouštět úlohy transformace dat ze svazků StorSimple do sdílených složek Azure. Nyní se můžete soustředit na nastavení Azure File Sync pro sdílenou složku (po dokončení úloh migrace pro sdílenou složku) nebo přímý přístup ke sdílení pro vaše informační pracovníky a aplikace do sdílené složky Azure.

## <a name="phase-4-accessing-your-azure-file-shares"></a>Fáze 4: přístup ke sdíleným složkám Azure

K dispozici jsou dvě hlavní strategie pro přístup ke sdíleným složkám Azure:

* **Azure File Sync:** [Jak nasadit Azure File Sync](#deploy-azure-file-sync) na místní Windows Server. AFS má všechny výhody místní mezipaměti, podobně jako StorSimple.
* **Přímý přístup ke sdílení:** [Jak nasadit přímý přístup do sdílené složky](#deploy-direct-share-access). Tuto strategii použijte v případě, že váš scénář přístupu pro danou sdílenou složku Azure nebude využívat místní ukládání do mezipaměti, nebo už nebudete mít možnost hostovat místní Windows Server. Tady budou uživatelé a aplikace dál přistupovat ke sdíleným složkám SMB přes protokol SMB, ale tyto sdílené složky už nejsou na místním serveru, ale přímo v cloudu.

Již jste se rozhodli, která možnost je pro vás nejvhodnější, ve [fázi 1](#phase-1-prepare-for-migration) této příručky.

Zbývající část této části se zaměřuje na pokyny k nasazení.

### <a name="deploy-azure-file-sync"></a>Nasazení Synchronizace souborů Azure

Je čas nasadit součást Azure File Sync:
1. Vytvořte prostředek cloudu Azure File Sync.
1. Na Prem Server nasaďte agenta Azure File Sync.
1. Registrace serveru u cloudového prostředku

Ještě nevytvářejte žádné skupiny synchronizace. Nastavení synchronizace se sdílenou složkou Azure by se mělo provést jenom v případě, že se dokončily úlohy migrace do sdílené složky Azure. Pokud jste museli začít používat Synchronizace souborů před dokončením migrace, bude migrace nenutně složitá, protože nemůžete jednoduše zjistit, kdy je čas iniciovat vyjmutí.

#### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Nasazení cloudového prostředku Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Pokud chcete změnit, ve které oblasti Azure se vaše data nacházejí, v době, kdy se migrace provede, nasaďte službu synchronizace úložiště ve stejné oblasti jako cílové účty úložiště pro tuto migraci.

#### <a name="deploy-an-on-premises-windows-server"></a>Nasazení místního Windows serveru

* Vytvořte Windows Server 2019 – minimálně 2012R2 jako virtuální počítač nebo fyzický server. Podporuje se taky cluster Windows serveru s podporou převzetí služeb při selhání. Nepoužívejte znovu server front-StorSimple 8100 nebo 8600.
* Zřídit nebo přidat přímo připojené úložiště (DAS ve srovnání se serverem NAS, který není podporován).

Osvědčeným postupem je poskytnout novému systému Windows Server stejné nebo větší množství úložiště než zařízení StorSimple 8100 nebo 8600, které je místně k dispozici pro ukládání do mezipaměti. Windows Server budete používat stejným způsobem jako zařízení StorSimple, pokud má stejné množství úložiště jako zařízení, pak by prostředí pro ukládání do mezipaměti mělo být podobné, pokud není stejné.
Úložiště můžete přidat nebo odebrat z Windows serveru. To vám umožní škálovat velikost místního svazku a velikost místního úložiště dostupného pro ukládání do mezipaměti.

#### <a name="prepare-the-windows-server-for-file-sync"></a>Příprava Windows serveru na synchronizaci souborů

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

#### <a name="configure-azure-file-sync-on-the-windows-server"></a>Konfigurace Azure File Sync na Windows serveru

Váš registrovaný místní Windows Server musí být pro tento proces připravený a připojený k Internetu.

> [!IMPORTANT]
> Před pokračováním musí být migrace souborů a složek do sdílené složky Azure dokončená StorSimple. Ujistěte se, že sdílená složka neprovádí žádné změny.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Nezapomeňte zapnout vrstvení cloudu! Vrstvení cloudu je funkce Azure File Sync, která umožňuje místnímu serveru mít méně úložnou kapacitu, než je uložený v cloudu, ale má úplný obor názvů k dispozici. Místně zajímavá data se ukládají také místně do mezipaměti pro zajištění rychlého přístupu v místním prostředí. Další příčinou toho, že v tomto kroku zapnete vrstvení cloudu, je, že v této fázi nechcete synchronizovat obsah souboru. v tuto chvíli by se teď měl posunout jenom obor názvů.

### <a name="deploy-direct-share-access"></a>Nasazení přímého přístupu pro sdílení

:::row:::
    :::column:::
        [![Podrobný průvodce a ukázka, jak bezpečně vystavit sdílené složky Azure přímo pro informační pracovníky a aplikace – kliknutím zahrajete.](./media/storage-files-migration-storsimple-8000/azure-files-direct-access-video-placeholder.png)](https://youtu.be/KG0OX0RgytI)
    :::column-end:::
    :::column:::
        Toto video je průvodce a ukázkou, jak bezpečně vystavit sdílené složky Azure přímo pro informační pracovníky a aplikace v pěti, jednoduchých krocích.</br>
        Video odkazuje na vyhrazenou dokumentaci pro některá témata:
* [Přehled identity](storage-files-active-directory-overview.md)
* [Jak připojit k doméně účet úložiště](storage-files-identity-auth-active-directory-enable.md)
* [Přehled sítí pro sdílené složky Azure](storage-files-networking-overview.md)
* [Postup konfigurace veřejných a privátních koncových bodů](storage-files-networking-endpoints.md)
* [Jak nakonfigurovat S2S VPN](storage-files-configure-s2s-vpn.md)
* [Jak nakonfigurovat Windows P2S VPN](storage-files-configure-p2s-vpn-windows.md)
* [Jak nakonfigurovat P2S síť VPN pro Linux](storage-files-configure-p2s-vpn-linux.md)
* [Jak nakonfigurovat předávání DNS](storage-files-networking-dns.md)
* [Konfigurace DFS – N](https://aka.ms/AzureFiles/Namespaces)
   :::column-end:::
:::row-end:::

### <a name="phase-4-summary"></a>Souhrn fáze 4
V této fázi jste vytvořili a spustili více úloh *služby transformace dat* (DTS) ve vašem *StorSimple data Manager*. Tyto úlohy migrovali soubory a složky do sdílených složek Azure. Kromě toho jste nasadili Azure File Sync nebo připravili vaše síťové a úložné účty pro přímý přístup do sdílené složky.

## <a name="phase-5-user-cut-over"></a>Fáze 5: vyjmutí uživatele z převzetí
Tato fáze je vše o zabalení migrace:
* Naplánujte výpadky.
* Zachytit se změnami, které uživatelé a aplikace vytvořili na StorSimple straně, zatímco úlohy transformace dat ve fázi 3 byly spuštěny. 
* Převzetí služeb při selhání uživatelů na nový Windows Server s Azure File Sync nebo sdílenými složkami Azure prostřednictvím přímého přístupu ke sdílení.

### <a name="plan-your-downtime"></a>Plánování výpadků
Tento přístup k migraci vyžaduje pro uživatele a aplikace nějaké výpadky. Cílem je udržet výpadky na minimum a může vám to mít za cíl:

* Udržujte dostupné svazky StorSimple při spouštění úloh transformace dat.
* Až skončíte s úlohami migrace dat pro sdílenou složku, je čas odebrat přístup uživatelů (alespoň přístup pro zápis) ze StorSimple svazků nebo sdílených složek. Poslední příkaz Robocopy si zachytí vaši sdílenou složku Azure a pak můžete uživatele vyjmout. Pokud spouštíte příkaz Robocopy, záleží na tom, jestli jste se rozhodli použít Azure File Sync nebo přímý přístup ke sdílení. V části věnované nástroji Robocopy se zabýváme.
* Po dokončení zpracování nástroje Robocopy budete připraveni k vystavení nového umístění uživatelům. Sdílenou složku Azure buď přímo, nebo sdílenou složku protokolu SMB na Windows serveru s nástrojem AFS. Nasazení DFS-N se často může rychleji a efektivně vyjímat. Zachová se tak, aby vaše stávající sdílené adresy byly konzistentní a odkazovaly na nové umístění, které obsahuje migrované soubory a složky.

### <a name="determine-when-your-namespace-has-fully-synced-to-your-server"></a>Určení, kdy se obor názvů plně synchronizuje s vaším serverem

Když pro sdílenou složku Azure použijete Azure File Sync, je důležité, abyste před spuštěním jakékoli místní složky Robocopy zjistili, že jste dokončili stahování celého oboru názvů na server. Doba potřebná ke stažení vašeho oboru názvů závisí na počtu položek ve sdílené složce Azure. Existují dva způsoby, jak určit, zda je váš obor názvů plně doručen na server:

#### <a name="azure-portal"></a>Azure Portal
Pomocí Azure Portal můžete zobrazit, kdy váš obor názvů plně dorazil.
* Přihlaste se k Azure Portal, přejděte do skupiny synchronizace a ověřte stav synchronizace pro skupinu synchronizace a koncový bod serveru. 
* Zajímavý směr je stažený: Pokud je koncový bod serveru nově zřízený, bude se zobrazovat **počáteční synchronizace** , která indikuje, že se obor názvů stále připravuje.
Jakmile se změní na cokoli, ale na *počáteční synchronizaci*, váš obor názvů se na serveru plně naplní a budete dobrým postupem pokračovat v místní složce Robocopy.

#### <a name="windows-server-event-viewer"></a>Prohlížeč událostí Windows serveru
organizační jednotka může také pomocí Prohlížeč událostí na serveru Windows sdělit, kdy byl obor názvů plně doručen.

1. Otevřete **Prohlížeč událostí** a přejděte do části **aplikace a služby**.
1. Přejděte na **Microsoft\FileSync\Agent\Telemetry**a otevřete ji.
1. Vyhledejte nejnovější **událost 9102**, která odpovídá dokončené relaci synchronizace.
1. Vyberte **Podrobnosti** a potvrďte, že se díváte na událost, kde **SyncDirection** je hodnota SyncDirection **stažena**.
1. V době, kdy byl váš obor názvů dokončen ke stažení na server, bude existovat jediná událost se **scénářem**, hodnotou **FullGhostedSync** a **HRESULT**  =  **0**.
1. V případě, že jste tuto událost vyhledali, můžete také vyhledat další **události 9102** s **SyncDirection**  =  **stažením** a **scénářem**  =  **"RegularSync"**. Hledání jedné z těchto událostí také znamená, že obor názvů se dokončil stahování a synchronizace v pravidelných relacích synchronizace, ať už v tuto chvíli existuje cokoli k synchronizaci.

### <a name="a-final-robocopy"></a>Poslední příkaz Robocopy
V tomto okamžiku existují rozdíly mezi místním Windows serverem a zařízením StorSimple 8100 nebo 8600:

1. Musíte zachytit změny, které uživatelé nebo aplikace vytvořili na StorSimple straně, zatímco migrace probíhala průběžně.
1. V případech, kdy používáte Azure File Sync: zařízení StorSimple má naplněnou mezipaměť vs. v tomto okamžiku se systémem Windows Server nachází pouze obor názvů bez uloženého obsahu souboru. Proto může výsledná služba Robocopy přispět k tomu, že vaše místní mezipaměť AFS můžete pořídit načtením obsahu souboru uloženého místně v mezipaměti, který je dostupný a vejde se na server AFS.
1. V důsledku neplatných znaků mohly být některé soubory ponechány na pozadí úlohy transformace dat. V takovém případě je zkopírujte do Windows serveru s povoleným Azure File Sync. Později je můžete upravit tak, aby se synchronizovaly. Pokud pro konkrétní sdílenou složku nepoužíváte Azure File Sync, je vhodnější jejich přejmenování na svazku StorSimple s neplatnými znaky a pak spustit Robocopy přímo proti sdílené složce Azure. 

> [!WARNING]
> Než bude server mít úplný obor názvů pro sdílenou složku Azure, nemusíte spouštět příkaz Robocopy.
> Rezervujte: "[určení, kdy se Váš obor názvů úplně stáhl na váš server](#determine-when-your-namespace-has-fully-synced-to-your-server)"

 Chcete kopírovat pouze soubory, které byly změněny po posledním spuštění úlohy migrace, a soubory, které nebyly během těchto úloh přesunuty. Až se migrace dokončí, můžete vyřešit problémy, proč se na serveru nepřesunuly později. Přečtěte si téma [řešení potíží s Azure File Sync](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

Příkaz Robocopy obsahuje několik parametrů. Následující ukázka předvádí dokončený příkaz a seznam důvodů pro výběr těchto parametrů:

```console
Robocopy /MT:16 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
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

Při konfiguraci zdrojového a cílového umístění příkazu Robocopy se ujistěte, že provedete kontrolu struktury zdroje a cíle, abyste se ujistili, že se shodují. Pokud jste použili funkci mapování adresáře úlohy migrace, struktura kořenového adresáře se může lišit od struktury StorSimple svazku. V takovém případě budete možná potřebovat více úloh Robocopy, jednu pro každý podadresář. Vzhledem k tomu, že tento příkaz Robocopy používá/MIR, nepřesunou se soubory, které jsou stejné (vrstvené soubory pro instanci), ale/MIR taky vyprázdní zdrojové a cílové sdílené složky, které se ve službě Windows Server nebo ve sdílené složce Azure nenacházejí ve zdrojové cestě StorSimple. Proto se musí přesně shodovat s úlohou Robocopy, aby se dosáhlo zamýšleného cíle jenom k aktualizaci migrovaného obsahu s nejnovějšími změnami provedenými při probíhající migraci. 

Podívejte se do souboru protokolu Robocopy, jestli byly soubory ponechány na pozadí. Pokud existují problémy, opravte je a znovu spusťte příkaz Robocopy. Neodstraňujte žádné prostředky StorSimple, než opravíte nedokončené problémy se soubory nebo složkami, které vás zajímají.

Pokud nepoužíváte Azure File Sync k ukládání konkrétní sdílené složky Azure do mezipaměti, ale místo toho je potřeba pro přímý přístup ke sdílení:
1. [Připojte sdílenou složku Azure](storage-how-to-use-files-windows.md#mount-the-azure-file-share) jako síťovou jednotku k místnímu počítači s Windows.
1. Proveďte příkaz Robocopy mezi StorSimple a připojenou sdílenou složkou Azure. Pokud se soubory nekopírují, opravte jejich názvy na straně StorSimple a odeberte neplatné znaky a potom spusťte příkaz Robocopy. Výše uvedený příkaz Robocopy je možné spustit několikrát, což způsobí nepotřebné odvolání StorSimple.

### <a name="user-cut-over"></a>Vyjmuté uživatelem

Pokud používáte Azure File Sync, pravděpodobně budete muset vytvořit sdílené složky SMB na tomto serveru s povoleným Windows serverem, který odpovídá sdíleným složkám, které jste měli na svazcích StorSimple. Tento krok můžete provést ještě před tím, než se sem zařadí čas, ale před tímto bodem je potřeba zajistit, aby nikdo neměl přístup k příčině změn v systému Windows Server.

Pokud máte nasazení DFS-N, můžete DFN-Namespaces nasměrovat na nová umístění složky na serveru. Pokud nemáte nasazení DFS-N a vaše zařízení 8100/8600 jste zapnuli v místním počítači s Windows serverem, můžete tento server převzít mimo doménu. Pak k novému připojení k doméně Azure File Sync povolit Windows Server. Během tohoto procesu dejte serveru stejný název serveru a sdílejte názvy jako starý server, aby oříznutí bylo pro uživatele, zásady skupiny a skripty transparentní.

[Další informace o systému souborů DFS-N](https://aka.ms/AzureFiles/Namespaces)

## <a name="deprovision"></a>Zrušení zřízení
Když zrušíte zřízení prostředku, ztratíte přístup ke konfiguraci tohoto prostředku i jeho datům. Zrušení zřízení nelze vrátit zpět, takže nepokračujte, dokud nepotvrdíte dokončení migrace a neexistují žádné závislosti na StorSimple souborech, složkách nebo zálohách svazků, které se chystáte zrušit.

Než začnete, je osvědčeným postupem, jak sledovat nové nasazení Azure File Sync v produkčním prostředí, a to pro určitý bit. Díky tomu máte možnost opravit případné problémy, se kterými se můžete setkat.
Jakmile provedete nasazení Azure File Sync nejméně na několik dní, můžete začít v tomto pořadí zrušit zřízení prostředků:

1. Zrušení zřízení StorSimple Data Managerho prostředku prostřednictvím Azure Portal.
   Budou odstraněny všechny úlohy služby DTS. Protokoly kopírování nebudete moct snadno načíst. Pokud jsou důležité pro vaše záznamy, načtěte je před zrušením zřízení.
1. Ujistěte se, že jsou vaše fyzická zařízení StorSimple migrována, a pak je zrušte.
   Pokud si nejste jistí, jestli jsou migrace dokončená, nepokračujte. Pokud zrušíte zřízení těchto prostředků, i když jsou pořád potřebná, nebudete moct data obnovit nebo jejich konfiguraci.
1. Pokud se v StorSimple Device Manager neodešlou žádná další registrovaná zařízení, můžete pokračovat v odebírání tohoto Device Manager prostředku.
1. Nyní je čas odstranit StorSimple účet úložiště v Azure. Znovu zastavte a potvrďte, že je migrace dokončená a že žádná z nich nezávisí na těchto datech, než budete pokračovat.
1. Odpojte fyzické zařízení StorSimple z datového centra.
1. Pokud zařízení StorSimple vlastníte, můžete ho zdarma recyklovat. 
   Pokud je zařízení zapůjčené, obraťte se na něj a podle potřeby zařízení vraťte.

Migrace je hotová.

> [!NOTE]
> Pořád máte nějaké otázky nebo se vyskytly nějaké problémy?</br>
> Jsme tu, abychom vám pomohli: AzureFilesMigration@microsoft . com


## <a name="next-steps"></a>Další kroky

* Získejte více znalostí [Azure File Sync: aka.MS/AFS](https://aka.ms/AFS).
* Seznamte se s flexibilitou zásad [cloudových vrstev](storage-sync-cloud-tiering.md) .
* [Povolením Azure Backup](../../backup/backup-afs.md#configure-backup-from-the-file-share-pane) ve sdílených složkách Azure naplánujete snímky a definujete plány uchovávání záloh.
* Pokud se zobrazí v Azure Portal, že některé soubory se trvale nesynchronizují, přečtěte si [Průvodce řešením potíží](storage-sync-files-troubleshoot.md) , kde najdete postup řešení těchto problémů.
