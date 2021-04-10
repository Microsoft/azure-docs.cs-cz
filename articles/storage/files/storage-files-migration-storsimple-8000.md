---
title: Migrace řady StorSimple 8000 na Synchronizace souborů Azure
description: Přečtěte si, jak migrovat zařízení s StorSimple 8100 nebo 8600 do Synchronizace souborů Azure.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 10/16/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 814a2f7e32f173111e45fff02f00c3e4d2a9a670
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103601079"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>Migrace StorSimple 8100 a 8600 do Synchronizace souborů Azure

Série StorSimple 8000 je reprezentovaná buď na 8100, 8600 nebo na místních zařízeních a na součástech jejich cloudových služeb. Je možné migrovat data z některého z těchto zařízení do Synchronizace souborů Azureho prostředí. Synchronizace souborů Azure je výchozí a strategická dlouhodobá služba Azure, na kterou je možné StorSimple zařízení migrovat.

Série StorSimple 8000 dosáhne [konce životnosti](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) v prosinci 2022. Je důležité zahájit plánování migrace co nejrychleji. Tento článek popisuje nezbytné kroky pro znalosti a migraci na pozadí pro úspěšnou migraci na Synchronizace souborů Azure.

## <a name="phase-1-prepare-for-migration"></a>Fáze 1: Příprava na migraci

Tato část obsahuje kroky, které byste měli provést na začátku migrace ze svazků StorSimple do sdílených složek Azure.

### <a name="inventory"></a>inventář

Když zahájíte plánování migrace, nejdříve Identifikujte všechna zařízení StorSimple a svazky, které potřebujete migrovat. Až to uděláte, můžete se rozhodnout pro vás na nejlepší cestu migrace.

* StorSimple fyzická zařízení (8000 series) se používají v této příručce k migraci.
* Virtuální zařízení, [řada StorSimple 1200, používají jiný Průvodce migrací](storage-files-migration-storsimple-1200.md).

### <a name="migration-cost-summary"></a>Souhrn nákladů na migraci

Migrace do sdílených složek Azure ze svazků StorSimple prostřednictvím úloh migrace v prostředku StorSimple Data Manager jsou bezplatné. Další náklady mohou být účtovány během a po migraci:

* Odchozí přenos dat z **sítě:** Vaše soubory StorSimple jsou v účtu úložiště v konkrétní oblasti Azure v reálném čase. Pokud zřídíte sdílené složky Azure, které migrujete do účtu úložiště, který je umístěný ve stejné oblasti Azure, nebudou se vám vyskytnout žádné náklady na výstup. V rámci této migrace můžete soubory přesunout do účtu úložiště v jiné oblasti. V takovém případě vám budou platit náklady na výstup.
* **Transakce sdílené složky Azure:** Po zkopírování souborů do sdílené složky Azure (jako součást migrace nebo mimo jednu) se účtují náklady na transakce při psaní souborů a metadat. Osvědčeným postupem je během migrace spustit sdílenou složku Azure na úrovni optimalizované pro transakci. Po dokončení migrace přepněte na požadovanou úroveň. Následující fáze budou vyvolány v příslušném bodě.
* **Změňte úroveň sdílené složky Azure:** Změna úrovně transakcí s cenami za Azure File Share. Ve většině případů bude cenově výhodnější postupovat podle pokynů z předchozího bodu.
* **Náklady na úložiště:** Když tato migrace začne kopírovat soubory do sdílené složky Azure, bude se používat a Fakturovatelné úložiště souborů Azure. Migrované zálohy se stanou [snímky sdílené složky Azure](storage-snapshots-files.md). Snímky sdílené složky využívají pouze kapacitu úložiště pro rozdíly, které obsahují.
* **StorSimple:** Dokud nebudete mít možnost zrušit zřízení zařízení StorSimple a účtů úložiště, bude docházet k StorSimple nákladům na úložiště, zálohy a zařízení.

### <a name="direct-share-access-vs-azure-file-sync"></a>Přímý přístup pro sdílení a Synchronizace souborů Azure

Sdílené složky Azure otevírají celou řadu příležitostí pro vytvoření struktury nasazení souborové služby. Sdílená složka Azure je jenom sdílená složka SMB v cloudu, kterou můžete nastavit tak, aby uživatelé měli přístup přímo přes protokol SMB se známým ověřováním protokolu Kerberos a stávajícími oprávněními NTFS (seznamy řízení přístupu (ACL) souborů a složek) pracující nativně. Přečtěte si další informace o [přístupu na základě identity ke sdíleným složkám Azure](storage-files-active-directory-overview.md).

Alternativou k přímému přístupu je [synchronizace souborů Azure](./storage-sync-files-planning.md). Synchronizace souborů Azure je přímým analogem pro schopnost StorSimple ukládat často používané soubory do mezipaměti v místním prostředí.

Synchronizace souborů Azure je cloudová služba Microsoftu založená na dvou hlavních součástech:

* Synchronizace souborů a vrstvení cloudu pro vytvoření mezipaměti pro přístup k výkonu na jakémkoli Windows serveru.
* Sdílené složky jako nativní úložiště v Azure, které je možné používat v různých protokolech jako SMB a REST souborů.

Sdílené složky Azure uchovávají důležité aspekty souborů na uložených souborech, jako jsou atributy, oprávnění a časová razítka. Se sdílenými složkami Azure už nepotřebujete, aby aplikace ani služba dokázala interpretovat soubory a složky uložené v cloudu. K těmto datům můžete nativně přistupovat prostřednictvím známých protokolů a klientů, jako je Průzkumník souborů systému Windows. Sdílené složky Azure umožňují ukládat data souborového serveru pro obecné účely a data aplikací v cloudu. Zálohování sdílené složky Azure je integrovaná funkce a dá se dál rozšířit o Azure Backup.

Tento článek se zaměřuje na kroky migrace. Pokud chcete získat další informace o Synchronizace souborů Azure před migrací, přečtěte si následující články:

* [Přehled Synchronizace souborů Azure](./storage-sync-files-planning.md "Přehled")
* [Průvodce nasazením Synchronizace souborů Azure](storage-sync-files-deployment-guide.md)

### <a name="storsimple-service-data-encryption-key"></a>Šifrovací klíč dat služby StorSimple

Při prvním nastavení zařízení StorSimple vygenerovala "šifrovací klíč dat služby" a vydá pokyn k bezpečnému uložení klíče. Tento klíč se používá k šifrování všech dat v přidruženém účtu služby Azure Storage, kde zařízení StorSimple ukládá vaše soubory.

Pro úspěšnou migraci je nutný šifrovací klíč dat služby. Teď je vhodný čas načíst tento klíč ze svých záznamů, jednu pro každé zařízení v inventáři.

Pokud klíče ve svých záznamech nemůžete najít, můžete ho načíst ze zařízení. Každé zařízení má jedinečný šifrovací klíč. Načtení klíče:

* Soubor a žádost o podporu se Microsoft Azure prostřednictvím Azure Portal. Požadavek by měl obsahovat sériové číslo (y) zařízení StorSimple a požadavek na načtení šifrovacího klíče dat služby.
* Pracovník podpory StorSimple vás bude kontaktovat s požadavkem na virtuální schůzku.
* Ujistěte se, že před zahájením schůzky se k zařízení StorSimple připojujete [přes sériovou konzolu](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) nebo prostřednictvím [vzdálené relace PowerShellu](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple).

> [!CAUTION]
> Při rozhodování o tom, jak se připojit k zařízení StorSimple, zvažte následující:
>
> * Připojení prostřednictvím relace HTTPS je nejbezpečnější a doporučuje se možnost.
> * Přímé připojení ke konzole sériového portu zařízení je zabezpečené, ale připojení ke konzole sériového portu přes síťové přepínače není.
> * Připojení relace HTTP je možnost, ale nejsou *šifrovaná*. Nedoporučují se, pokud se nepoužívají v uzavřené a důvěryhodné síti.

### <a name="storsimple-volume-backups"></a>Zálohy svazků StorSimple

StorSimple nabízí rozdílové zálohy na úrovni svazku. Sdílené složky Azure mají taky tuto schopnost, která se nazývá snímky sdílené složky.
Úlohy migrace můžou přesunout jenom zálohy, ne data z živého svazku. Proto by poslední záloha měla být vždy na seznamu záloh přesunutých v rámci migrace.

Rozhodněte, jestli během migrace potřebujete přesunout starší zálohy.
Osvědčeným postupem je udržovat tento seznam co nejmenším možným způsobem, aby se úlohy migrace dokončily rychleji.

Chcete-li identifikovat kritické zálohy, které je třeba migrovat, vytvořte kontrolní seznam zásad zálohování. Například:
* Poslední záloha. (Poznámka: poslední záloha by měla být vždy součástí tohoto seznamu.)
* Jeden měsíc zálohy na 12 měsíců.
* Jedna záloha za rok na tři roky. 

Když později vytvoříte úlohy migrace, můžete pomocí tohoto seznamu identifikovat přesné zálohy svazků StorSimple, které se musí migrovat, aby splňovaly požadavky na váš seznam.

> [!CAUTION]
> Výběr více než **50** zálohování svazku StorSimple není podporováno.
> Úlohy migrace můžou přesunout jenom zálohy, ne data z živého svazku. Proto je nejnovější záloha nejblíže živým datům, takže by měla být vždy součástí seznamu záloh, které se mají přesunout do migrace.

### <a name="map-your-existing-storsimple-volumes-to-azure-file-shares"></a>Namapujte stávající StorSimple svazky na sdílené složky Azure.

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="number-of-storage-accounts"></a>Počet účtů úložiště

Vaše migrace bude nejspíš těžit z nasazení víc účtů úložiště, které každý z nich drží menší počet sdílených složek Azure.

Pokud jsou vaše sdílené soubory vysoce aktivní (využívá mnoho uživatelů nebo aplikací), můžou se na limit výkonu vašeho účtu úložiště dostat dvě sdílené složky Azure. Z tohoto důvodu je osvědčeným postupem migrace do několika účtů úložiště, z nichž každý má vlastní jednotlivé sdílené složky, a obvykle není více než dvě nebo tři sdílené složky na jeden účet úložiště.

Osvědčeným postupem je nasazování účtů úložiště s jednou sdílenou složkou. Pokud máte archivované sdílené složky, můžete do stejného účtu úložiště seskupit více sdílených složek Azure.

Tyto požadavky se vztahují i na [přímý přístup do cloudu](#direct-share-access-vs-azure-file-sync) (přes virtuální počítač nebo službu Azure), než na synchronizace souborů Azure. Pokud máte v úmyslu použít pro tyto sdílené složky výhradně Synchronizace souborů Azure, je seskupení několika do jednoho účtu úložiště Azure přesné. V budoucnu možná budete chtít aplikaci přezvednout a přesunout do cloudu, která by pak měla přímý přístup ke sdílené složce. Tento scénář by měl mít vyšší počet vstupně-výstupních operací a propustnosti. Nebo můžete začít používat službu v Azure, která by mohla mít navíc větší počet vstupně-výstupních operací a propustnosti.

Pokud jste vytvořili seznam sdílených složek, namapujte každou sdílenou složku na účet úložiště, kde se bude nacházet.

> [!IMPORTANT]
> Rozhodněte se v oblasti Azure a zajistěte, aby každý účet úložiště a Synchronizace souborů Azure prostředku odpovídaly vybrané oblasti.
> Nekonfigurujte síť a nastavení brány firewall pro účty úložiště nyní. V tomto okamžiku by tyto konfigurace nemohly být migrace možné. Po dokončení migrace tato nastavení úložiště Azure nakonfigurujte.

### <a name="phase-1-summary"></a>Souhrn fáze 1

Na konci fáze 1:

* Máte dobrý přehled o zařízeních a svazcích StorSimple.
* Služba Data Manager je připravená k přístupu ke svazkům StorSimple v cloudu, protože jste načetli šifrovací klíč dat služby pro každé zařízení StorSimple.
* Máte plán, ve kterém se musí migrovat svazky a zálohy (pokud jsou nějaké mimo poslední).
* Víte, jak namapovat svazky na příslušný počet sdílených složek Azure a účtů úložiště.

## <a name="phase-2-deploy-azure-storage-and-migration-resources"></a>Fáze 2: nasazení úložiště Azure a prostředků migrace

Tato část popisuje požadavky týkající se nasazení různých typů prostředků, které jsou potřeba v Azure. Některé budou uchovávat data po migraci a některé jsou potřeba jenom pro migraci. Nespouštějte nasazování prostředků, dokud nedokončíte plán nasazení. Je obtížné, někdy nemožné, změnit některé aspekty vašich prostředků Azure po jejich nasazení.

### <a name="deploy-storage-accounts"></a>Nasazení účtů úložiště

Pravděpodobně budete potřebovat nasadit několik účtů úložiště Azure. Každý z nich bude obsahovat menší počet sdílených složek Azure podle plánu nasazení, který je v předchozí části tohoto článku hotový. Pokud chcete [nasadit svoje plánované účty úložiště](../common/storage-account-create.md#create-a-storage-account), otevřete Azure Portal. Vezměte v úvahu následující základní nastavení pro všechny nové účty úložiště.

> [!IMPORTANT]
> Nekonfigurujte nyní nastavení sítě a brány firewall pro vaše účty úložiště. Díky tomu by tyto konfigurace v tuto chvíli nevedly k migraci. Po dokončení migrace tato nastavení úložiště Azure nakonfigurujte.

#### <a name="subscription"></a>Předplatné

Můžete použít stejné předplatné, jaké jste použili pro nasazení StorSimple, nebo jiné. Jediným omezením je, že vaše předplatné musí být ve stejném Azure Active Directory tenant jako předplatné StorSimple. Než začnete s migrací, zvažte přesunutí předplatného StorSimple do příslušného tenanta. Můžete přesunout jenom celé předplatné, jednotlivé prostředky StorSimple se nedají přesunout do jiného tenanta nebo předplatného.

#### <a name="resource-group"></a>Skupina prostředků

Skupiny prostředků pomáhají s organizací prostředků a oprávněními pro správu správců. Přečtěte si další informace o [skupinách prostředků v Azure](../../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group).

#### <a name="storage-account-name"></a>Název účtu úložiště

Název účtu úložiště se stane součástí adresy URL a má určitá omezení znaků. V případě zásad vytváření názvů zvažte, že názvy účtů úložiště musí být v celém světě jedinečné, povolit jenom malá písmena a čísla, vyžadovat délku mezi 3 a 24 znaky a nepovolujte speciální znaky, jako jsou pomlčky nebo podtržítka. Další informace najdete v tématu [pravidla vytváření názvů prostředků Azure Storage](../../azure-resource-manager/management/resource-name-rules.md#microsoftstorage).

#### <a name="location"></a>Umístění

Umístění nebo oblast Azure účtu úložiště je velmi důležité. Pokud používáte Synchronizace souborů Azure, všechny vaše účty úložiště musí být ve stejné oblasti jako prostředek služby synchronizace úložiště. Oblast Azure, kterou vyberete, by měla být pro místní servery a uživatele blízkoná nebo centrální. Po nasazení prostředku nemůžete změnit jeho oblast.

Můžete vybrat jinou oblast, ze které se aktuálně nacházejí data StorSimple (účet úložiště).

> [!IMPORTANT]
> Pokud z aktuálního umístění účtu úložiště StorSimple vyberete jinou oblast, budou se během migrace [platit poplatky za výstup](https://azure.microsoft.com/pricing/details/bandwidth) . Data ponechají oblast StorSimple a zadají novou oblast účtu úložiště. Pokud zůstanete ve stejné oblasti Azure, nemusíte platit žádné poplatky za šířku pásma.

#### <a name="performance"></a>Výkon

Máte možnost vybrat Storage úrovně Premium (SSD) pro sdílené složky Azure nebo úložiště Standard. Standardní úložiště obsahuje [několik vrstev pro sdílenou složku](storage-how-to-create-file-share.md#changing-the-tier-of-an-azure-file-share). Storage úrovně Standard je správnou možností pro většinu zákazníků, kteří migrují z StorSimple.

Pořád si nejste jisti?

* Pokud potřebujete [výkon služby Azure File Share Premium](understanding-billing.md#provisioned-model), vyberte Premium Storage.
* Pro úlohy souborového serveru pro obecné účely vyberte úložiště Standard, které zahrnuje aktivní data a Archivovaná data. Pokud se Synchronizace souborů Azure jedinou úlohou sdílené složky v cloudu, vyberte možnost standardní úložiště.

#### <a name="account-kind"></a>Druh účtu

* Pro úložiště Standard klikněte na *StorageV2 (obecné účely v2)*.
* V případě sdílených složek Premium vyberte *úložiště* souborů.

#### <a name="replication"></a>Replikace

K dispozici je několik nastavení replikace. Přečtěte si další informace o různých typech replikace.

Vyberte jenom jednu z následujících dvou možností:

* *Místně redundantní úložiště (LRS)*.
* *Redundantní úložiště zóny (ZRS)*, které není k dispozici ve všech oblastech Azure.

> [!NOTE]
> Pouze LRS a ZRS typy redundance jsou kompatibilní s velkými 100-TiB sdílenými sdílenými složkami Azure.

Geograficky redundantní úložiště (GRS) ve všech variacích se momentálně nepodporuje. Typ redundance můžete přepnout později a přejít na GRS, když se na něj podpora dorazí v Azure.

#### <a name="enable-100-tib-capacity-file-shares"></a>Povolit 100 – TiB sdílené složky kapacity

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png" alt-text="Obrázek znázorňující kartu Upřesnit v Azure Portal pro vytvoření účtu úložiště.":::
    :::column-end:::
    :::column:::
        V části **Upřesnit** v Průvodci vytvořením nového účtu úložiště v Azure Portal můžete povolit podporu **velkých sdílených složek** v tomto účtu úložiště. Pokud tato možnost není pro vás dostupná, pravděpodobně jste vybrali špatný typ redundance. Ujistěte se, že jste vybrali možnost LRS nebo ZRS, aby tato možnost byla k dispozici.
    :::column-end:::
:::row-end:::

Zamezit pro velké 100y sdílené složky s kapacitou TiB má několik výhod:

* Výkon se výrazně zvyšuje ve srovnání s menšími TiB sdílenými složkami souborů (například desetkrát za IOPS).
* Vaše migrace se dokončí významně rychleji.
* Ujistěte se, že sdílená složka bude mít dostatečnou kapacitu pro uchovávání všech dat, která do ní migrujete, včetně požadavků na rozdílovou zálohu kapacity úložiště.
* Pojednává o budoucí nárůst.

### <a name="azure-file-shares"></a>Sdílené složky Azure

Po vytvoření účtů úložiště přejdete do části **sdílení souborů** v účtu úložiště a nasadíte příslušný počet sdílených složek Azure podle plánu migrace z fáze 1. Zvažte dodržování následujících základních nastavení pro nové sdílené složky v Azure.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-share.png" alt-text="Azure Portal snímek obrazovky s novým uživatelským rozhraním pro sdílení souborů.":::
    :::column-end:::
    :::column:::
        </br>**Název**</br>Podporují se malá písmena, číslice a spojovníky.</br></br>**Kvóta**</br>Kvóta je tady srovnatelná s vynucenou kvótou SMB v instanci Windows serveru. Osvědčeným postupem je nenastavit kvótu, protože migrace a další služby selžou po dosažení kvóty.</br></br>**Úrovně**</br>Vyberte **transakci optimalizovanou** pro novou sdílenou složku. Během migrace proběhne spousta transakcí. Cenově výhodnější je později změnit úroveň na úroveň, která nejlépe vyhovuje vašim úlohám.
    :::column-end:::
:::row-end:::

### <a name="storsimple-data-manager"></a>Správce dat StorSimple

Prostředek Azure, který bude obsahovat vaše úlohy migrace, se nazývá **StorSimple data Manager**. Vyberte **nový prostředek** a vyhledejte ho. Potom vyberte **Vytvořit**.

Tento dočasný prostředek se používá pro orchestraci. Až se migrace dokončí, můžete ji zrušit. Měl by se nasadit do stejného předplatného, skupiny prostředků a oblasti jako účet úložiště StorSimple.

### <a name="azure-file-sync"></a>Synchronizace souborů Azure

Pomocí Synchronizace souborů Azure můžete přidat místní ukládání nejčastěji používaných souborů do mezipaměti. Podobně jako při ukládání do mezipaměti Synchronizace souborů Azure StorSimple funkce vrstev cloudu nabízí latenci místního přístupu v kombinaci s lepší kontrolou dostupné kapacity mezipaměti v instanci Windows serveru a synchronizaci s více lokalitami. Pokud je místní mezipaměť vaším cílem, potom v místní síti Připravte virtuální počítač s Windows serverem (jsou podporované taky fyzické servery a clustery s podporou převzetí služeb při selhání) s dostatečnou úložnou kapacitou, která je přímo připojená.

> [!IMPORTANT]
> Zatím neinstalujte Synchronizace souborů Azure. Doporučuje se nastavit Synchronizace souborů Azure po dokončení migrace vaší sdílené složky. Nasazení Synchronizace souborů Azure by nemělo začínat před fází 4 migrace.

### <a name="phase-2-summary"></a>Souhrn fáze 2

Na konci fáze 2 jste nasadili účty úložiště a všechny sdílené složky Azure v nich. Budete mít také prostředek StorSimple Data Manager. Při konfiguraci úloh migrace budete používat druhý postup ve fázi 3.

## <a name="phase-3-create-and-run-a-migration-job"></a>Fáze 3: vytvoření a spuštění úlohy migrace

Tato část popisuje, jak nastavit úlohu migrace a pečlivě namapovat adresáře na svazku StorSimple, které by se měly zkopírovat do cílové sdílené složky Azure, kterou vyberete. Začněte tím, že přejdete do StorSimple Data Manager, v nabídce vyhledáte **Definice úloh** a vyberete **+ definice úlohy**. Správný cílový typ úložiště je výchozí hodnota: **sdílená složka Azure**.

![Typy úloh migrace řady StorSimple 8000](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job-type.png "Snímek obrazovky s definicemi úloh Azure Portal s otevřeným dialogovým oknem definice úlohy, který se zeptá na typ úlohy: kopírování do sdílené složky nebo do kontejneru objektů BLOB.")

:::row:::
    :::column:::
        ![Úloha migrace řady StorSimple 8000](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job.png "Snímek obrazovky nového formuláře pro vytvoření úlohy pro úlohu migrace.")
    :::column-end:::
    :::column:::
        **Název definice úlohy**</br>Tento název by měl označovat sadu souborů, které přesouváte. Dobrým zvykem je poskytnout podobný název jako sdílená složka Azure. </br></br>**Umístění, kde se úloha spouští**</br>Když vybíráte určitou oblast, musíte vybrat stejnou oblast jako účet úložiště StorSimple, nebo pokud tato oblast není k dispozici, a pak ji zavřít. </br></br><h3>Zdroj</h3>**Zdrojové předplatné**</br>Vyberte předplatné, do kterého uložíte StorSimple Správce zařízení prostředku. </br></br>**Prostředek StorSimple**</br>Vyberte StorSimple Správce zařízení vaše zařízení je zaregistrované ve službě. </br></br>**Šifrovací klíč dat služby**</br>V tomto [článku v tomto článku najdete předchozí část](#storsimple-service-data-encryption-key) , v případě, že klíč nemůžete najít ve svých záznamech. </br></br>**Zařízení**</br>Vyberte zařízení StorSimple, které obsahuje svazek, na který chcete provést migraci. </br></br>**Svazek**</br>Vyberte zdrojový svazek. Později se můžete rozhodnout, jestli chcete migrovat celý svazek nebo podadresáře do cílové sdílené složky Azure.</br></br> **Zálohy svazků**</br>Výběrem *možnosti vybrat zálohy svazku* můžete vybrat konkrétní zálohy, které chcete přesunout jako součást této úlohy. Nadcházející, [vyhrazená část tohoto článku](#selecting-volume-backups-to-migrate) podrobně popisuje proces.</br></br><h3>Cíl</h3>Jako cíl této úlohy migrace vyberte předplatné, účet úložiště a sdílenou složku Azure.</br></br><h3>Mapování adresáře</h3>[Vyhrazená část tohoto článku](#directory-mapping)se zabývá všemi relevantními podrobnostmi.
    :::column-end:::
:::row-end:::

### <a name="selecting-volume-backups-to-migrate"></a>Výběr záloh svazku k migraci

Existují důležité aspekty týkající se výběru záloh, které je potřeba migrovat:

- Úlohy migrace můžou přesunout jenom zálohy, ne data z živého svazku. Takže nejaktuálnější záloha je nejblíže živým datům a měla by být vždy na seznamu záloh přesunutých v rámci migrace.
- Ujistěte se, že nejnovější záloha je nedávná, takže rozdíl na živém sdílení zůstane co nejmenším možným způsobem. Před vytvořením úlohy migrace může dojít k ručnímu spuštění a dokončení další zálohy svazku. Malé rozdíly v živém sdílení budou zlepšit možnosti migrace. Pokud tato rozdílová hodnota může být nula = žádné další změny StorSimpleho svazku se neprojevily po pořízení poslední zálohy v seznamu, fáze 5: převzetí uživatele bude drasticky jednodušší a urychlit.
- Zálohy je nutné přehrává zpátky do sdílené složky Azure **od nejstarších po nejnovější**. Po spuštění úlohy migrace nejde na seznam záloh ve sdílené složce Azure seřadit starší zálohy. Proto je *před* vytvořením úlohy nutné zajistit, aby byl seznam zálohování dokončen. 
- Tento seznam záloh v úloze nelze po vytvoření úlohy změnit – i v případě, že úloha nebyla nikdy spuštěna. 

:::row:::
    :::column:::        
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups.png" alt-text="Snímek obrazovky nového formuláře pro vytvoření úlohy, který popisuje část, kde jsou StorSimple zálohy vybrány pro migraci." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-expanded.png":::
    :::column-end:::
    :::column:::
        Pokud chcete pro úlohu migrace vybrat zálohy StorSimple svazku, vyberte ve formuláři pro vytvoření úlohy *možnost vybrat zálohy svazku* .
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-annotated.png" alt-text="Obrázek znázorňující, že horní polovina okna pro výběr zálohování obsahuje seznam všech dostupných záloh. Vybraná záloha se v tomto seznamu zobrazí šedě a přidá se do druhého seznamu v dolní polovině okna. Můžete ji také odstranit znovu." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-annotated.png":::
    :::column-end:::
    :::column:::
        Když se otevře okno Výběr zálohy, rozdělí se na dva seznamy. V prvním seznamu se zobrazí všechny dostupné zálohy. Můžete rozšířit a zúžit sadu výsledků filtrováním pro určitý časový rozsah. (viz další oddíl) </br></br>Vybraná záloha se zobrazí jako šedá a v dolní polovině okna se přidá do druhého seznamu. V druhém seznamu se zobrazí všechny zálohy vybrané k migraci. Je také možné odebrat zálohu vybranou v chybě.
        > [!CAUTION]
        > Musíte vybrat **všechny** zálohy, které chcete migrovat. Starší zálohy nemůžete přidat později. Když se úloha vytvoří, nemůžete úlohu změnit, aby se změnil výběr.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-time.png" alt-text="Snímek obrazovky znázorňující výběr časového rozsahu okna pro výběr zálohování" lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-time-expanded.png":::
    :::column-end:::
    :::column:::
        Ve výchozím nastavení je seznam filtrovaný tak, aby v posledních sedmi dnech zobrazoval zálohy svazků StorSimple a usnadnil tak výběr nejaktuálnějšího zálohování. Pro zálohování dále v minulosti použijte filtr časového rozsahu v horní části okna. Můžete buď vybrat z existujícího filtru, nebo nastavit vlastní časový rozsah, který bude filtrovat pouze zálohy provedené během této doby.
    :::column-end:::
:::row-end:::

> [!CAUTION]
> Výběr více než 50 zálohování svazku StorSimple není podporováno. Úlohy s velkým počtem záloh můžou selhat.

### <a name="directory-mapping"></a>Mapování adresáře

Mapování adresáře je pro vaši úlohu migrace volitelné. Pokud necháte oddíl prázdnou, *všechny* soubory a složky v kořenovém adresáři StorSimple svazku se přesunou do kořenového adresáře vaší cílové sdílené složky Azure. Ve většině případů není nejlepší přístup uložení obsahu celého svazku ve sdílené složce Azure. Je často lepší rozdělit obsah svazku mezi několik sdílených složek v Azure. Pokud jste ještě plán ještě nevytvořili, přečtěte si nejdřív téma [mapování svazku StorSimple na sdílené složky Azure](#map-your-existing-storsimple-volumes-to-azure-file-shares) .

V rámci vašeho plánu migrace se možná rozhodli, že složky na svazku StorSimple musí být rozdělené mezi několik sdílených složek Azure. Pokud je to tento případ, můžete provést rozdělení podle:

1. Definování více úloh pro migraci složek na jednom svazku. Každá z nich bude mít stejný zdroj StorSimple svazků, ale jako cíl bude jiná sdílená složka Azure.
1. Zadáním přesně těch složek ze svazku StorSimple se musí migrovat do zadané sdílené složky pomocí oddílu **mapování adresáře** ve formuláři pro vytvoření úlohy a podle konkrétní [sémantiky mapování](#semantic-elements).

> [!IMPORTANT]
> Výrazy Path a Mapping v tomto formuláři nelze ověřit při odeslání formuláře. Pokud jsou mapování nesprávně zadána, úloha může buď zcela selhat, nebo způsobit nežádoucí výsledek. V takovém případě je obvykle nejlepší odstranit sdílenou složku Azure, vytvořit ji znovu a pak opravit příkazy mapování v nové úloze migrace pro sdílenou složku. Spuštění nové úlohy s příkazy s pevným mapováním může opravit vynechané složky a přenést je do existující sdílené složky. Tímto způsobem však lze vyřešit pouze složky, které byly vynechány z důvodu chybného pravopisu v cestě.

#### <a name="semantic-elements"></a>Sémantické elementy

Mapování je vyjádřeno zleva doprava: [\Source cesta] \> [cesta \target].

|Sémantický znak          | Význam  |
|:---------------------------|:---------|
| **\\**                     | Indikátor kořenové úrovně       |
| **\>**                     | [Source] a [target-Mapping] operátor.     |
|**\|** nebo RETURN (nový řádek) | Oddělovač dvou instrukcí pro mapování složky </br>Alternativně můžete tento znak vynechat a vybrat **ENTER** a získat tak další mapovací výraz na svém vlastním řádku.        |

### <a name="examples"></a>Příklady
Přesune obsah *uživatelských dat* složky do kořenového adresáře cílové sdílené složky:
``` console
\User data > \
```
Přesune celý obsah svazku do nové cesty na cílové sdílené složce:
``` console
\ > \Apps\HR tracker
```
Přesune obsah zdrojové složky do nové cesty na cílové sdílené složce:
``` console
\HR resumes-Backup > \Backups\HR\resumes
```
Seřadí několik zdrojových umístění do nové adresářové struktury:
``` console
\HR\Candidate Tracker\v1.0 > \Apps\Candidate tracker
\HR\Candidates\Resumes > \HR\Candidates\New
\Archive\HR\Old Resumes > \HR\Candidates\Archived
```

### <a name="semantic-rules"></a>Sémantická pravidla

* Vždy zadejte cesty ke složkám relativně k kořenové úrovni.
* Zahajte každou cestu ke složce se indikátorem kořenové úrovně " \\ ".
* Nepoužívejte písmena jednotek.
* Při zadávání více cest se zdrojové nebo cílové cesty nesmí překrývat:</br>
   Neplatný příklad překrytí zdrojové cesty:</br>
    *\\folder\1 > \\ složku*</br>
    *\\Složka \\ 1 \\ 2 > \\ složky*</br>
   Neplatný příklad překrytí cílové cesty:</br>
   *\\> složky \\*</br>
   *\\složky > \\*</br>
* Zdrojové složky, které neexistují, budou ignorovány.
* Vytvoří se struktury složek, které v cíli neexistují.
* Podobně jako u oken se v názvech složek nerozlišují velká a malá písmena.

> [!NOTE]
> Úloha migrace nebude kopírovat obsah složky *informací o svazku \System* a *$recycle. bin* na svazku StorSimple.

### <a name="run-a-migration-job"></a>Spuštění úlohy migrace

Úlohy migrace jsou uvedené v části *Definice úloh* v prostředku data Manager, který jste nasadili do skupiny prostředků.
V seznamu definice úlohy vyberte úlohu, kterou chcete spustit.

V okně úlohy, které se otevře, můžete zobrazit spuštění úlohy v dolním seznamu. Zpočátku bude tento seznam prázdný. V horní části okna se nachází příkaz s názvem *Spustit úlohu*. Tento příkaz tuto úlohu hned nespustí, otevře se okno **spuštění úlohy** :

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-run-job.png" alt-text="Obrázek znázorňující okno spuštění úlohy s otevřeným ovládacím prvkem, který zobrazuje vybrané zálohy k migraci. Nejstarší záloha je zvýrazněna, je nutné ji nejprve vybrat." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-run-job-expanded.png":::
    :::column-end:::
    :::column:::
        V této verzi je potřeba každou úlohu spustit několikrát. </br></br>**Musíte začít s nejstarší zálohou ze seznamu záloh, které chcete migrovat.** (zvýrazněné v obrázku)</br></br>Úlohu znovu spustíte, kolikrát jste vybrali zálohování, pokaždé v průběhu pozdějšího zálohování.
        </br></br>
        > [!CAUTION]
        > Je nutné, abyste spustili úlohu migrace s nejdříve vybranou nejstarší zálohou a pak znovu a pokaždé, když se pokusíte o postupné pozdější zálohování. Pořadí záloh je vždycky nutné udržovat ručně – od nejstarší po nejnovější.
    :::column-end:::
:::row-end:::

### <a name="phase-3-summary"></a>Souhrn fáze 3

Na konci fáze 3 budete spouštět aspoň jednu z úloh migrace ze StorSimple svazků do sdílených složek Azure. Stejnou úlohu migrace budete spouštět několikrát, od nejstarší po nejnovější zálohy, kterou je třeba migrovat. Nyní se můžete zaměřit na buď nastavení Synchronizace souborů Azure pro sdílenou složku (po dokončení úloh migrace pro sdílenou složku), nebo nasměrováním přístupu ke sdílení pro vaše informační pracovníky a aplikace do sdílené složky Azure.

## <a name="phase-4-access-your-azure-file-shares"></a>Fáze 4: přístup ke sdíleným složkám Azure

K dispozici jsou dvě hlavní strategie pro přístup ke sdíleným složkám Azure:

* **Synchronizace souborů Azure**: [Nasaďte synchronizace souborů Azure](#deploy-azure-file-sync) na místní instanci Windows serveru. Synchronizace souborů Azure má všechny výhody místní mezipaměti, podobně jako StorSimple.
* **Přímý přístup ke sdílení**: [nasazení přímého přístupu ke sdílení](#deploy-direct-share-access). Tuto strategii použijte v případě, že váš scénář přístupu pro danou sdílenou složku Azure nebude využívat místní ukládání do mezipaměti, nebo už nebudete mít možnost hostovat místní instanci Windows serveru. Tady budou uživatelé a aplikace dál přistupovat ke sdíleným složkám SMB přes protokol SMB. Tyto sdílené složky už nejsou na místním serveru, ale přímo v cloudu.

Měli byste už rozhodnout, která možnost je pro vás nejvhodnější ve [fázi 1](#phase-1-prepare-for-migration) této příručky.

Zbývající část této části se zaměřuje na pokyny k nasazení.

### <a name="deploy-azure-file-sync"></a>Nasazení Synchronizace souborů Azure

Je čas nasadit součást Synchronizace souborů Azure.

1. Vytvořte prostředek cloudu Synchronizace souborů Azure.
1. Nasaďte agenta Synchronizace souborů Azure na místním serveru.
1. Zaregistrujte server u cloudového prostředku.

Ještě nevytvářejte žádné skupiny synchronizace. Nastavení synchronizace se sdílenou složkou Azure by se mělo provádět až po dokončení úloh migrace do sdílené složky Azure. Pokud jste před dokončením migrace začali používat Synchronizace souborů Azure, bude migrace zbytečně nenáročná, protože se nám nepovedlo dát vědět, kdy jste se mohli rozhodnout, kdy by bylo možné provést vyjmutí.

#### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Nasazení cloudového prostředku Synchronizace souborů Azure

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Pokud chcete změnit oblast Azure, ve které se data nachází po dokončení migrace, nasaďte službu synchronizace úložiště ve stejné oblasti jako cílové účty úložiště pro tuto migraci.

#### <a name="deploy-an-on-premises-windows-server-instance"></a>Nasazení místní instance Windows serveru

* Vytvořte Windows Server 2019 (minimálně 2012R2) jako virtuální počítač nebo fyzický server. Podporuje se i cluster s podporou převzetí služeb při selhání se systémem Windows Server. Nepoužívejte znovu server front-StorSimple 8100 nebo 8600.
* Zřídit nebo přidat přímo připojené úložiště. Úložiště připojené k síti není podporované.

Osvědčeným postupem je poskytnout nové instanci Windows serveru stejné nebo větší velikosti úložiště než zařízení StorSimple 8100 nebo 8600, které je místně dostupné pro ukládání do mezipaměti. Instanci Windows serveru použijete stejným způsobem jako zařízení StorSimple. Pokud má stejné množství úložiště jako zařízení, prostředí pro ukládání do mezipaměti by mělo být podobné, pokud není stejné. Můžete přidat nebo odebrat úložiště z instance Windows serveru na. Tato funkce umožňuje škálovat místní velikost svazku a velikost místního úložiště dostupného pro ukládání do mezipaměti.

#### <a name="prepare-the-windows-server-instance-for-file-sync"></a>Příprava instance Windows serveru na synchronizaci souborů

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

#### <a name="configure-azure-file-sync-on-the-windows-server-instance"></a>Konfigurace Synchronizace souborů Azure v instanci Windows serveru

Zaregistrovaná místní instance Windows serveru musí být pro tento proces připravená a připojená k Internetu.

> [!IMPORTANT]
> Před pokračováním musí být migrace souborů a složek do sdílené složky Azure dokončená StorSimple. Ujistěte se, že sdílená složka neprovádí žádné změny.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Nezapomeňte zapnout vrstvení cloudu. Vrstvení cloudu je funkce Synchronizace souborů Azure, která umožňuje místnímu serveru mít méně úložnou kapacitu, než je uložený v cloudu, ale má úplný obor názvů k dispozici. Místně zajímavá data se ukládají také místně do mezipaměti pro zajištění rychlého přístupu v místním prostředí. Dalším důvodem pro zapnutí vrstvení cloudu v tomto kroku je, že v této fázi nechcete synchronizovat obsah souboru. V tuto chvíli by se měl přesunout jenom obor názvů.

### <a name="deploy-direct-share-access"></a>Nasazení přímého přístupu pro sdílení

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        Toto video je průvodce a ukázkou, jak bezpečně vystavit sdílené složky Azure přímo pro informační pracovníky a aplikace v pěti jednoduchých krocích.</br>
        Video odkazuje na vyhrazenou dokumentaci pro některá témata:

* [Přehled identity](storage-files-active-directory-overview.md)
* [Jak připojit k doméně účet úložiště](storage-files-identity-auth-active-directory-enable.md)
* [Přehled sítí pro sdílené složky Azure](storage-files-networking-overview.md)
* [Postup konfigurace veřejných a privátních koncových bodů](storage-files-networking-endpoints.md)
* [Jak nakonfigurovat S2S VPN](storage-files-configure-s2s-vpn.md)
* [Jak nakonfigurovat Windows P2S VPN](storage-files-configure-p2s-vpn-windows.md)
* [Jak nakonfigurovat P2S síť VPN pro Linux](storage-files-configure-p2s-vpn-linux.md)
* [Jak nakonfigurovat předávání DNS](storage-files-networking-dns.md)
* [Konfigurace DFS – N](/windows-server/storage/dfs-namespaces/dfs-overview)
   :::column-end:::
:::row-end:::

### <a name="phase-4-summary"></a>Souhrn fáze 4

V této fázi jste v StorSimple Data Manager vytvořili a spustili více úloh migrace. Tyto úlohy migrovali soubory a složky do sdílených složek Azure. Nasadili jste taky Synchronizace souborů Azure nebo připravil vaše síťové a úložné účty pro přímý přístup do sdílené složky.

## <a name="phase-5-user-cut-over"></a>Fáze 5: vyjmutí uživatele z převzetí

Tato fáze je vše o zabalení migrace:

* Naplánujte výpadky.
* Všechny změny, které uživatelé a aplikace vygenerovali na StorSimple straně, zatímco úlohy migrace ve fázi 3 byly spuštěné.
* Převzetí služeb při selhání uživatelů na novou instanci Windows serveru s Synchronizace souborů Azure nebo sdílenými složkami Azure prostřednictvím přímého přístupu ke sdílení.

### <a name="plan-your-downtime"></a>Plánování výpadků

Tento přístup k migraci vyžaduje pro uživatele a aplikace nějaké výpadky. Cílem je udržet výpadky na minimum. Následující okolnosti můžou pomáhat:

* Udržujte dostupné svazky StorSimple při spouštění úloh migrace.
* Až skončíte s úlohami migrace dat pro sdílenou složku, je čas odebrat přístup uživatelů (alespoň přístup pro zápis) ze StorSimple svazků nebo sdílených složek. Finální prostředí Robocopy bude zachytilo sdílenou složku Azure. Potom můžete uživatele vyjmout. Pokud spouštíte příkaz Robocopy, záleží na tom, jestli jste se rozhodli použít Synchronizace souborů Azure nebo přímý přístup ke sdílení. Část nadcházející v nástroji Robocopy pokrývá tento předmět.
* Po dokončení funkce Robocopy pro vás budete připraveni zpřístupnit nové umístění uživatelům pomocí sdílené složky Azure přímo nebo sdílené složky protokolu SMB v instanci systému Windows Server s Synchronizace souborů Azure. Nasazení DFS-N se často může rychleji a efektivně dosáhnout. Zachová se tak, aby vaše stávající sdílené adresy byly konzistentní a odkazovaly na nové umístění, které obsahuje migrované soubory a složky.

### <a name="determine-when-your-namespace-has-fully-synced-to-your-server"></a>Určení, kdy se obor názvů plně synchronizuje s vaším serverem

Když pro sdílenou složku Azure použijete Synchronizace souborů Azure, je důležité, abyste *před* spuštěním jakékoli místní složky Robocopy zjistili, že jste dokončili stahování celého oboru názvů na server. Doba potřebná ke stažení vašeho oboru názvů závisí na počtu položek ve sdílené složce Azure. Existují dva způsoby, jak určit, zda byl váš obor názvů plně doručen na server.

#### <a name="azure-portal"></a>portál Azure

Pomocí Azure Portal můžete zobrazit, kdy váš obor názvů plně dorazil.

* Přihlaste se k Azure Portal a přejít do skupiny synchronizace. Ověřte stav synchronizace pro skupinu synchronizace a koncový bod serveru.
* Zajímavý směr se stáhne. Pokud je koncový bod serveru nově zřízený, bude se zobrazovat **počáteční synchronizace**, která indikuje, že obor názvů je stále mimo provoz.
Po provedení změn na cokoli, ale na **počáteční synchronizaci**, se Váš obor názvů na serveru plně naplní. Nyní můžete pokračovat pomocí místní nástroje Robocopy.

#### <a name="windows-server-event-viewer"></a>Prohlížeč událostí Windows serveru

Můžete také použít Prohlížeč událostí v instanci systému Windows Server k oznámení, že obor názvů byl zcela doručen.

1. Otevřete **Prohlížeč událostí** a pokračujte na **aplikace a služby**.
1. Přejít na a otevřít **Microsoft\FileSync\Agent\Telemetry**.
1. Vyhledejte nejnovější **událost 9102**, která odpovídá dokončené relaci synchronizace.
1. Vyberte **Podrobnosti** a potvrďte, že se díváte na událost, kde je hodnota **SyncDirection** **stažena**.
1. V době, kdy byl váš obor názvů dokončen ke stažení na server, bude existovat jediná událost se **scénářem**, hodnotou **FullGhostedSync** a **HRESULT**  =  **0**.
1. Pokud jste tuto událost nezjistili, můžete také vyhledat další **události 9102** s **SyncDirection**  =  **stažením** a **scénářem**  =  **"RegularSync"**. Hledání jedné z těchto událostí také znamená, že obor názvů se dokončil stahování a synchronizace v pravidelných relacích synchronizace, ať už v tuto chvíli existuje cokoli k synchronizaci.

### <a name="a-final-robocopy"></a>Poslední příkaz Robocopy

V tomto okamžiku existují rozdíly mezi místní instancí Windows serveru a zařízením StorSimple 8100 nebo 8600.

1. Musíte zachytit změny, které uživatelé nebo aplikace vytvářely na StorSimple straně, zatímco migrace probíhala průběžně.
1. V případech, kdy používáte Synchronizace souborů Azure: zařízení StorSimple má naplněnou mezipaměť a instanci Windows serveru, která má pouze obor názvů, který v současné době ukládá místně uložený obsah souboru. Výsledná služba Robocopy může přispět k postupnému navýšení místní mezipaměti Synchronizace souborů Azure tím, že se k nim dá získat obsah souborů uložených v mezipaměti, který je dostupný a vejde se na Synchronizace souborů Azure Server.
1. V důsledku neplatných znaků mohly být některé soubory ponechány na pozadí úlohy migrace. Pokud ano, zkopírujte je do instance Windows serveru s povolenou Synchronizace souborů Azure. Později je můžete upravit tak, aby se synchronizovaly. Pokud nepoužíváte Synchronizace souborů Azure pro konkrétní sdílenou složku, budete lépe přejmenovává soubory o neplatné znaky na svazku StorSimple. Pak spusťte příkaz Robocopy přímo se sdílenou složkou Azure.

> [!WARNING]
> Robocopy v systému Windows Server 2019 v současné době dochází k problému, který způsobí, že soubory vytvořené Synchronizace souborů Azure na cílovém serveru budou překopírovány ze zdroje a znovu nahrány do Azure při použití funkce/MIR nástroje Robocopy. Je nutné použít příkaz Robocopy na jiném serveru Windows než 2019. Upřednostňovanou volbou je Windows Server 2016. Tato poznámka se aktualizuje, pokud se problém vyřeší prostřednictvím web Windows Update.

> [!WARNING]
> Než bude server mít úplný obor názvů pro sdílenou složku Azure stažený, *nesmíte spustit příkaz* Robocopy. Další informace najdete v tématu [určení, kdy se Váš obor názvů úplně stáhl na váš server](#determine-when-your-namespace-has-fully-synced-to-your-server).

 Chcete kopírovat jenom soubory, které se změnily po posledním spuštění úlohy migrace, a soubory, které se před těmito úlohami nepřesunuly. Problém můžete vyřešit tak, že se po dokončení migrace později nepřesunul na server. Další informace najdete v tématu [řešení potíží s synchronizace souborů Azure](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

Příkaz Robocopy obsahuje několik parametrů. Následující příklad prezentuje dokončený příkaz a seznam důvodů pro výběr těchto parametrů.

```console
Robocopy /MT:16 /UNILOG:<file name> /TEE /NP /B /MIR /IT /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Pozadí

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Umožňuje nástroji Robocopy spustit vícevláknové procesy. Výchozí hodnota je 8 a maximální hodnota je 128.
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
      /NP
   :::column-end:::
   :::column span="1":::
      Vynechá protokolování průběhu, aby se protokol mohl přečíst.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Spustí příkaz Robocopy ve stejném režimu, který používá zálohovací aplikace. Umožňuje nástroji Robocopy přesunout soubory, ke kterým nemá aktuální uživatel oprávnění.
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
      /IT
   :::column-end:::
   :::column span="1":::
      Zajistí, aby se v některých scénářích zrcadlení zachovala přesnost.</br>Příklad: mezi dvěma prostředími Robocopy se spustí soubor se změnou seznamu ACL a aktualizace atributu, například jako *skrytý*. Bez příkazu/IT může být Změna seznamu ACL vynechána, takže nebude přenesena do cílového umístění.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      Přesnost kopírování souborů (výchozí je/COPY: DAT), příznaky kopírování: D = data, A = atributy, T = časová razítka, S = Security = NTFS ACL, O = informace o vlastníkovi, U = informace o auditování.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      Kopírovat všechny informace o souborech (ekvivalentní k/COPY: DATSOU).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      Věrnost pro kopii adresářů (výchozí hodnota je/DCOPY: DA), příznaky kopírování: D = data, A = atributy, T = časová razítka.
   :::column-end:::
:::row-end:::

Když nakonfigurujete zdrojové a cílové umístění příkazu Robocopy, ujistěte se, že jste provedli kontrolu struktury zdroje a cíle, abyste se ujistili, že se shodují. Pokud jste použili funkci mapování adresáře úlohy migrace, struktura kořenového adresáře se může lišit od struktury StorSimple svazku. V takovém případě budete možná potřebovat více úloh Robocopy, jednu pro každý podadresář. Pokud si nejste jistí, jestli příkaz bude fungovat podle očekávání, můžete použít parametr */l* , který bude simulovat příkaz, aniž by ve skutečnosti prováděla žádné změny.

Tento příkaz Robocopy používá/MIR, takže nebude přesouvat soubory, které jsou stejné (vrstvené soubory, např.). Pokud ale nechybí zdrojovou a cílovou cestu,/MIR také vyprázdní adresářové struktury ve vaší instanci Windows serveru nebo sdílené složce Azure, které nejsou přítomné ve zdrojové cestě StorSimple. Musí přesně odpovídat úloze Robocopy, aby dosáhli zamýšleného cíle aktualizace migrovaného obsahu s nejnovějšími změnami provedenými v době, kdy migrace probíhá.

Podívejte se do souboru protokolu Robocopy, jestli byly soubory ponechány na pozadí. Pokud existují problémy, opravte je a znovu spusťte příkaz Robocopy. Před opravou nezpracovaných problémů se soubory nebo složkami, které vás zajímají, nezajistěte žádné prostředky StorSimple.

Pokud nepoužíváte Synchronizace souborů Azure k ukládání konkrétní sdílené složky Azure do mezipaměti, ale místo toho jste zvolili přístup k přímým sdílením:

1. [Připojte sdílenou složku Azure](storage-how-to-use-files-windows.md#mount-the-azure-file-share) jako síťovou jednotku k místnímu počítači s Windows.
1. Proveďte příkaz Robocopy mezi StorSimple a připojenou sdílenou složkou Azure. Pokud se soubory nekopírují, opravte jejich názvy na straně StorSimple a odeberte neplatné znaky. Pak opakujte příkaz Robocopy. Výše uvedený příkaz Robocopy je možné spustit několikrát, aniž by to způsobilo zbytečné odvolání StorSimple.

### <a name="user-cut-over"></a>Vyjmuté uživatelem

Pokud používáte Synchronizace souborů Azure, pravděpodobně budete muset vytvořit sdílené složky SMB na této instanci Windows serveru s povolenou Synchronizace souborů Azure, která odpovídá sdíleným složkám, které jste měli na svazcích StorSimple. Tento krok můžete provést ještě před tím, než se tady neztratí čas. Je však třeba zajistit, aby před tímto bodem nikdo nezpůsobil změny v instanci Windows serveru.

Pokud máte nasazení DFS-N, můžete DFN-Namespaces nasměrovat na nová umístění složky na serveru. Pokud nemáte nasazení DFS-N a vaše zařízení 8100 nebo 8600 jste nastavili místně s instancí Windows serveru, můžete tento server převzít mimo doménu. Pak se doména připojí k nové instanci Windows serveru s povolenou Synchronizace souborů Azure. Během tohoto procesu dejte serveru stejný název serveru a sdílejte názvy jako starý server, aby oříznutí zůstalo transparentní pro uživatele, zásady skupiny a skripty.

Přečtěte si další informace o [systému souborů DFS-N](/windows-server/storage/dfs-namespaces/dfs-overview).

## <a name="deprovision"></a>Zrušení zřízení

Při zrušení zřízení prostředku ztratíte přístup ke konfiguraci tohoto prostředku a jeho dat. Zrušení zřízení nelze vrátit zpět. Nepokračujte, dokud nepotvrdíte, že:

* Migrace je hotová.
* Neexistují žádné závislosti na StorSimple souborech, složkách nebo zálohách svazků, se kterými se chystáte zrušit zřízení.

Než začnete, je osvědčeným postupem, jak v průběhu provozu sledovat nové nasazení Synchronizace souborů Azure v produkčním prostředí. Tato doba vám dává možnost opravit případné problémy, se kterými se můžete setkat. Po dokončení nasazení Synchronizace souborů Azure po dobu nejméně několika dnů můžete začít v tomto pořadí zrušit zřízení prostředků:

1. Zrušení zřízení StorSimple Data Managerho prostředku prostřednictvím Azure Portal. Budou odstraněny všechny úlohy služby DTS. Protokoly kopírování nebudete moct snadno načíst. Pokud jsou důležité pro vaše záznamy, načtěte je před zrušením zřízení.
1. Ujistěte se, že jsou vaše fyzická zařízení StorSimple migrována, a pak je zrušte. Pokud si nejste jistí, že se migrují, nepokračujte. Pokud zrušíte zřízení těchto prostředků, i když jsou pořád potřebná, nebudete moct data obnovit nebo jejich konfiguraci.<br>Volitelně můžete zrušit zřízení prostředku svazku StorSimple, který vymaže data na zařízení. Tato **Akce** může trvat několik dní a nebude Forensically data na zařízení vynulovat. Pokud je to pro vás důležité, zpracujte na disku nezávisle na zrušení zřízení prostředku a podle vašich zásad.
1. Pokud se v StorSimple Správce zařízení neodešlou žádná další registrovaná zařízení, můžete pokračovat v odebírání tohoto Správce zařízení prostředku.
1. Nyní je čas odstranit StorSimple účet úložiště v Azure. Znovu zastavte a potvrďte, že je migrace dokončená a že žádná a žádná z nich nezávisí na těchto datech, než budete pokračovat.
1. Odpojte fyzické zařízení StorSimple z datového centra.
1. Pokud zařízení StorSimple vlastníte, nebudete moct počítač recyklovat. Pokud je zařízení zapůjčené, obraťte se na něj a podle potřeby zařízení vraťte.

Migrace je hotová.

> [!NOTE]
> Pořád máte otázky nebo se u nich vyskytují nějaké problémy?</br>
> Jsme tu, abychom vám pomohli AzureFilesMigration@microsoft.com .

## <a name="next-steps"></a>Další kroky

* Získejte více znalostí [synchronizace souborů Azure: aka.MS/AFS](./storage-sync-files-planning.md).
* Seznamte se s flexibilitou zásad [cloudových vrstev](storage-sync-cloud-tiering-overview.md) .
* [Povolením Azure Backup](../../backup/backup-afs.md#configure-backup-from-the-file-share-pane) ve sdílených složkách Azure naplánujete snímky a definujete plány uchovávání záloh.
* Pokud se zobrazí v Azure Portal, že některé soubory se trvale nesynchronizují, přečtěte si [Průvodce řešením potíží](storage-sync-files-troubleshoot.md) , kde najdete postup řešení těchto problémů.
