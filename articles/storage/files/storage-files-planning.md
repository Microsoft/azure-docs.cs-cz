---
title: Plánování nasazení služby soubory Azure | Microsoft Docs
description: Zjistěte, co je potřeba vzít v úvahu při plánování nasazení souborů Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9b71c4a5c0f245d9da97dc8f096d15c5386bf919
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368617"
---
# <a name="planning-for-an-azure-files-deployment"></a>Plánování nasazení služby Soubory Azure

[Soubory Azure](storage-files-introduction.md) nabízí plně spravované sdílené složky v cloudu, které jsou přístupné přes standardní průmyslový protokol SMB. Vzhledem k tomu, že soubory Azure jsou plně spravované, jejich nasazení v produkčních scénářích je mnohem jednodušší než nasazení a Správa souborového serveru nebo zařízení NAS. Tento článek popisuje témata, která je potřeba vzít v úvahu při nasazení sdílené složky Azure pro produkční použití v rámci vaší organizace.

## <a name="management-concepts"></a>Koncepty správy

 Následující diagram znázorňuje konstrukce správy souborů Azure:

![Struktura souborů](./media/storage-files-introduction/files-concepts.png)

* **Účet služby Storage:** Veškerý přístup ke službě Azure Storage se provádí prostřednictvím účtu úložiště. Podrobnosti o kapacitě účtu úložiště najdete v tématu [škálovatelnost a výkonnostní cíle pro účty úložiště úrovně Standard](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) .

* **Sdílená složka:** Sdílená složka služby File Storage představuje sdílenou složku protokolu SMB v Azure. Všechny adresáře a soubory musí být vytvořeny v nadřazené sdílené složce. Účet může obsahovat neomezený počet sdílených složek a sdílená složka může ukládat neomezený počet souborů až do celkové kapacity sdílené složky. Celková kapacita pro sdílené složky úrovně Premium a Standard je 100 TiB.

* **Adresář:** Volitelná hierarchie adresářů.

* **Soubor:** Soubor ve sdílené složce. Soubor může mít velikost až 1 TiB.

* **Formát adresy URL**: pro požadavky na sdílenou složku Azure vytvořené pomocí protokolu REST je možné soubory adresovat pomocí následujícího formátu adresy URL:

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/<file>
    ```

## <a name="data-access-method"></a>Metoda přístupu k datům

Soubory Azure nabízí dva, vestavěné a pohodlný metody přístupu k datům, které můžete použít samostatně, nebo v kombinaci s ostatními, pro přístup k datům:

1. **Přímý přístup do cloudu**: jakákoli sdílená složka Azure může být připojená do [systému Windows](storage-how-to-use-files-windows.md), [MacOS](storage-how-to-use-files-mac.md)nebo [Linux](storage-how-to-use-files-linux.md) s použitím standardního protokolu SMB (Server Message Block) nebo prostřednictvím souboru REST API. Pomocí protokolu SMB se čtení a zápisy do souborů ve sdílené složce provádějí přímo ve sdílené složce v Azure. Aby klient SMB v operačním systému mohl připojit virtuální počítač v systému Azure, musí podporovat minimálně protokol SMB 2,1. Pro místní připojení, jako je například pracovní stanice uživatele, musí klient SMB podporovaný pracovní stanicí podporovat minimálně protokol SMB 3,0 (s šifrováním). Kromě protokolu SMB můžou nové aplikace nebo služby získat přímý přístup ke sdílené složce přes soubor REST, který poskytuje jednoduché a škálovatelné programovací rozhraní aplikace pro vývoj softwaru.
2. **Azure File Sync**: u Azure File Sync se sdílené složky dají replikovat na místní Windows servery nebo do Azure. Vaši uživatelé mají přístup ke sdílené složce přes Windows Server, jako je například přes sdílenou složku SMB nebo NFS. To je užitečné ve scénářích, kdy k datům budou mít data v datovém centru Azure, například ve scénáři firemní pobočky, a to daleko beze změny. Data je možné replikovat mezi několika koncovými body Windows serveru, například mezi více pobočkami. Nakonec můžou být data vrstvená do souborů Azure, takže všechna data jsou stále přístupná prostřednictvím serveru, ale server nemá úplnou kopii dat. Místo toho jsou data bez problémů znovu volána, když je uživatel otevřel.

Následující tabulka ukazuje, jak můžou uživatelé a aplikace získat přístup ke sdílené složce Azure:

| | Přímý přístup do cloudu | Synchronizace souborů Azure |
|------------------------|------------|-----------------|
| Jaké protokoly potřebujete použít? | Soubory Azure podporují SMB 2,1, SMB 3,0 a REST API souborů. | Přístup ke sdílené složce Azure prostřednictvím libovolného podporovaného protokolu na Windows serveru (SMB, NFS, FTPS atd.) |  
| Kde máte spuštěnou úlohu? | **V Azure**nabízí Azure Files přímý přístup k vašim datům. | Místní **s pomalými sítěmi**: klienti Windows, Linux a MacOS můžou připojit místní sdílenou složku Windows jako rychlou mezipaměť sdílené složky Azure. |
| Jakou úroveň seznamů ACL potřebujete? | Sdílená složka a úroveň souboru. | Úroveň sdílení, souboru a uživatele. |

## <a name="data-security"></a>Zabezpečení dat

Soubory Azure mají několik předdefinovaných možností pro zajištění zabezpečení dat:

* Podpora šifrování v obou přenosových protokolech: šifrování SMB 3,0 a soubor REST přes HTTPS. Ve výchozím nastavení: 
    * Klienti, kteří podporují šifrování SMB 3,0, odesílají a přijímají data prostřednictvím šifrovaného kanálu.
    * Klienti, kteří nepodporují protokol SMB 3,0 se šifrováním, můžou komunikovat v rámci datových center přes SMB 2,1 nebo SMB 3,0 bez šifrování. Klienti SMB nemůžou komunikovat mezi datovými centru přes SMB 2,1 nebo SMB 3,0 bez šifrování.
    * Klienti můžou komunikovat přes soubor REST buď pomocí protokolu HTTP, nebo HTTPS.
* Šifrování v klidovém prostředí ([Azure Storage šifrování služby](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)): šifrování služby Storage (SSE) je povolené pro všechny účty úložiště. Data v klidovém stavu jsou šifrovaná pomocí plně spravovaných klíčů. Šifrování v klidovém případě nezvyšuje náklady na úložiště ani nesnižuje výkon. 
* Volitelný požadavek šifrovaných dat při přenosu: když je tato možnost vybraná, soubory Azure odmítnou přístup k datům přes nešifrované kanály. Pouze připojení HTTPS a SMB 3,0 s připojením šifrování je povoleno.

    > [!Important]  
    > Vyžadování zabezpečeného přenosu dat způsobí, že starší klienti protokolu SMB nebudou schopni komunikovat s protokolem SMB 3,0 se šifrováním, aby nedošlo k chybě. Další informace najdete v tématech [připojení ve Windows](storage-how-to-use-files-windows.md), [připojení k systému Linux](storage-how-to-use-files-linux.md)a [připojení k MacOS](storage-how-to-use-files-mac.md).

Z důvodu maximálního zabezpečení důrazně doporučujeme, abyste vždycky povolili šifrování v klidovém režimu a povolili šifrování přenášených dat, kdykoli pro přístup k datům používáte moderní klienty. Pokud třeba budete potřebovat připojit sdílenou složku na virtuálním počítači s Windows Serverem 2008 R2, který podporuje jenom SMB 2,1, musíte do svého účtu úložiště povolený nešifrovaný provoz, protože SMB 2,1 nepodporuje šifrování.

Pokud používáte Azure File Sync pro přístup ke sdílené složce Azure, vždy použijeme protokol HTTPS a SMB 3,0 s šifrováním k synchronizaci vašich dat na serverech Windows, a to bez ohledu na to, jestli budete vyžadovat šifrování neaktivních dat.

## <a name="file-share-performance-tiers"></a>Úrovně výkonu sdílení souborů

Soubory Azure nabízí dvě úrovně výkonu: Standard a Premium.

### <a name="standard-file-shares"></a>Standardní sdílené složky

Standardní sdílené složky se zálohují na jednotky pevného disku (HDD). Standardní sdílené složky poskytují spolehlivý výkon pro vstupně-výstupní úlohy, které jsou méně citlivé na variabilitu výkonu, jako jsou například sdílené složky pro obecné účely a vývoj a testovací prostředí. Standardní sdílené složky jsou dostupné jenom v modelu fakturace s průběžnými platbami.

> [!IMPORTANT]
> Pokud chcete používat sdílené složky větší než 5 TiB, Projděte si část připojení [k větším sdíleným složkám (úroveň Standard)](#onboard-to-larger-file-shares-standard-tier) , kde najdete kroky pro připojení a také regionální dostupnost a omezení.

### <a name="premium-file-shares"></a>Soubory ke sdílení souborů úrovně Premium

Soubory úrovně Premium jsou zajištěny jednotkami SSD (Solid-State Drive) (SSD). Soubory úrovně Premium poskytují pro úlohy náročné na vstupně-výstupní operace konzistentní vysoký výkon a nízkou latenci v rámci jedné číslice milisekund pro většinu vstupně-výstupních operací. Díky tomu jsou vhodné pro širokou škálu úloh, jako jsou databáze, hostování webů a vývojová prostředí. Sdílené složky Premium jsou dostupné jenom v modelu zřízené fakturace. Soubory úrovně Premium používají model nasazení oddělený od standardních sdílených složek.

Azure Backup je k dispozici pro sdílené složky Premium a služba Azure Kubernetes podporuje prémiové sdílené složky ve verzi 1,13 a vyšší.

Pokud se chcete dozvědět, jak vytvořit prémiovou sdílenou složku, přečtěte si náš článek na předmětu: [jak vytvořit účet úložiště Azure Premium](storage-how-to-create-premium-fileshare.md).

V současné době nemůžete přímo převádět mezi standardní sdílenou složkou souborů a prémiovou sdílenou složkou. Pokud byste chtěli přepnout na jednu vrstvu, musíte v této vrstvě vytvořit novou sdílenou složku a ručně zkopírovat data z původní sdílené složky do nové sdílené složky, kterou jste vytvořili. Můžete to provést pomocí kteréhokoli z podporovaných nástrojů pro kopírování souborů Azure, jako je například Robocopy nebo AzCopy.

> [!IMPORTANT]
> Soubory úrovně Premium jsou dostupné ve většině oblastí, které nabízí účty úložiště a ZRS v menší podmnožině oblastí. Pokud chcete zjistit, jestli jsou v současnosti ve vaší oblasti dostupné sdílené složky Premium, přečtěte si stránku [Dostupné produkty podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=storage) pro Azure. Informace o oblastech, které podporují ZRS, najdete v tématu [Azure Storage redundance](../common/storage-redundancy.md).
>
> Abychom vám pomohli upřednostnit nové oblasti a funkce úrovně Premium, vyplňte prosím tento [průzkum](https://aka.ms/pfsfeedback).

#### <a name="provisioned-shares"></a>Zřízené sdílené složky

Soubory úrovně Premium se zřídí na základě pevného poměru propustnosti GiB/IOPS/. Pro každé zřízené GiB se pro sdílenou složku vystaví jedna propustnost a 0,1 MiB/s až do maximálního počtu na jednu sdílenou složku. Minimální povolené zřizování je 100 GiB s minimálním IOPS/propustností.

Na základě optimálního úsilí se všechny sdílené složky můžou rozdělovat až na tři IOPS za GiB zřízeného úložiště po dobu 60 minut nebo déle v závislosti na velikosti sdílené složky. Nové sdílené složky začínají úplným kreditem na základě zřízené kapacity.

Sdílené složky musí být zřízené v 1 přírůstcích GiB. Minimální velikost je 100 GiB, příští velikost je 101 GiB atd.

> [!TIP]
> Směrného plánu IOPS = 1 * zřízené GiB. (Až do maximálního počtu 100 000 IOPS).
>
> Limit shlukování = 3 * základní IOPS (Až do maximálního počtu 100 000 IOPS).
>
> míra odchozích dat = 60 MiB/s + 0,06 * zřízené GiB
>
> rychlost příchozího přenosu dat = 40 MiB/s + 0,04 * zřízené GiB

Velikost zřízené sdílené složky je určena kvótou sdílené složky. Kvótu sdílení můžete kdykoli prodloužit, ale můžete ji snížit až 24 hodin od posledního zvýšení. Po čekání na 24 hodin bez zvýšení kvóty můžete kvótu sdílení snížit tolikrát, kolikrát chcete, a to až do jejich opětovného zvýšení. Změny v rozsahu IOPS/propustnosti budou platit během několika minut od změny velikosti.

Velikost zřízené sdílené složky můžete snížit pod použitou GiB. Pokud to uděláte, neztratíte data, ale bude se vám stále účtovat využitá velikost a bude se vám účtovat výkon (počet vstupně-výstupních operací za sekundu, propustnost a shlukové IOPS) zřízené sdílené složky, nikoli použitou velikost.

Následující tabulka ilustruje několik příkladů těchto vzorců pro zřízené velikosti sdílených složek:

|Kapacita (GiB) | Směrný IOPS | Shlukový IOPS | Výstup (MiB/s) | Příchozí přenosy (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | Až 300     | 66   | 44   |
|500         | 500     | Až 1 500   | 90   | 60   |
|1,024       | 1,024   | Až 3 072   | 122   | 81   |
|5 120       | 5 120   | Až 15 360  | 368   | 245   |
|10 240      | 10 240  | Až 30 720  | 675 | 450   |
|33 792      | 33 792  | Až 100 000 | 2 088 | 1 392   |
|51 200      | 51 200  | Až 100 000 | 3 132 | 2 088   |
|102 400     | 100 000 | Až 100 000 | 6 204 | 4 136   |

> [!NOTE]
> Výkon sdílených složek závisí na omezeních sítě počítačů, dostupné šířce pásma sítě, velikosti v/v, paralelních operacích, mezi mnoha dalšími faktory. Například na základě interního testování s 8 KiB velikostí vstupně-výstupních operací čtení a zápisu může být jeden virtuální počítač s Windows, *Standard F16s_v2*připojený ke sdílené složce Premium přes SMB, mohl dosáhnout 20 tisíc čtení IOPS a 15 000 IOPS. S 512 velikostí čtení/zápisu v/v souboru MiB může stejný virtuální počítač dosáhnout propustnosti 1,1 GiB/s a 370 propustnosti příchozího přenosu dat MiB/s. Chcete-li dosáhnout maximálního rozsahu výkonu, rozšíříte zatížení napříč více virtuálními počítači. Některé běžné problémy s výkonem a alternativní řešení najdete v [Průvodci odstraňováním potíží](storage-troubleshooting-files-performance.md) .

#### <a name="bursting"></a>Shlukování

Soubory úrovně Premium můžou zvýšit zatížení za IOPS až do násobku tří. Shlukování je automatizované a funguje na základě úvěrového systému. Shluking funguje na nejvyšší úrovni a limit shluku není zárukou, sdílené složky mohou *zvýšit úroveň až do* limitu.

Kredity se sčítají v rámci shlukového přenosu, kdykoli je přenos pro sdílenou složku pod směrným intervalem IOPS. Například sdílená složka 100 GiB má 100 směrný IOPS. Pokud byl skutečný provoz na sdílené složce 40 vstupně-výstupních operací pro určitý interval 1 sekund, pak je 60 nepoužitelné IOPS v kreditu do intervalu shlukování. Tyto kredity se pak použijí později, pokud by operace překročily směrný IOPs.

> [!TIP]
> Velikost shlukového intervalu = směrné IOPS * 2 * 3600.

Pokaždé, když sdílená složka přesáhne základní IOPS a má kredity v rámci shlukového intervalu, dojde k nárůstu zatížení. Sdílené složky můžou pokračovat v roztržení, dokud budou kredity nižší, i když sdílené složky menší než 50 TiB budou zůstat až po hodinu. Sdílené složky, které jsou větší než 50 TiB, mohou technicky překročit tento limit hodin, až dvě hodiny, ale na základě počtu kumulovaných kreditů. Každý v/v za rámec standardních hodnot IOPS spotřebovává jeden kredit a jakmile se všechny kredity spotřebují, vrátí se do směrného plánu IOPS.

Sdílené kredity mají tři stavy:

- Probíhá nabíhání, Pokud sdílená složka používá méně než směrný IOPS.
- Odmítnutí, když se sdílená složka rozpíná.
- Zbývající konstanta, pokud nejsou k dispozici žádné kredity nebo se základní hodnota IOPS používá.

Nové sdílené složky začínají úplným počtem kreditů v rámci svého shlukového intervalu. Kredity shluku se neúčtují, pokud se za vstupně-výstupní operace klesne pod základnu IOPS, a to kvůli omezení serveru.

## <a name="file-share-redundancy"></a>Redundance sdílení souborů

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Pokud se rozhodnete pro geograficky redundantní úložiště s přístupem pro čtení (RA-GRS), měli byste se seznámit s tím, že Azure File v tuto chvíli nepodporuje geograficky redundantní úložiště s přístupem pro čtení (RA-GRS). Sdílené složky v účtu úložiště RA-GRS fungují stejně jako v účtech GRS a účtují se za GRS ceny.

> [!Warning]  
> Pokud používáte sdílenou složku Azure jako koncový bod cloudu v účtu úložiště GRS, neměli byste iniciovat převzetí služeb při selhání účtu úložiště. Pokud to uděláte, synchronizace přestane fungovat a v případě nově vrstvených souborů může dojít i k neočekávané ztrátě dat. V případě ztráty oblasti Azure spustí Microsoft převzetí služeb při selhání účtu úložiště způsobem, který je kompatibilní s Azure File Sync.

Sdílené složky Azure Files Premium podporují LRS i ZRS, ale ZRS jsou momentálně dostupné v menší podmnožině oblastí.

## <a name="onboard-to-larger-file-shares-standard-tier"></a>Připojování k větším sdíleným složkám (úroveň Standard)

Tato část se vztahuje pouze na standardní sdílené složky. Všechny sdílené složky Premium jsou dostupné s kapacitou 100 TiB.

### <a name="restrictions"></a>Omezení

- Převod účtu LRS/ZRS na GRS/GZRS nebude možné použít pro žádný účet úložiště s povolenými velkými sdílenými složkami.

### <a name="regional-availability"></a>Regionální dostupnost

Standardní sdílené složky s 100 TiB limit kapacity jsou k dispozici globálně ve všech oblastech Azure –

- LRS: všechny oblasti kromě jar (Jižní Afrika – sever a Jižní Afrika – západ)
- ZRS: všechny oblasti kromě Japonska – východ, Severní Evropa, Jižní Afrika – sever.
- GRS/GZRS: není podporováno.

### <a name="enable-and-create-larger-file-shares"></a>Povolit a vytvořit větší sdílené složky

Pokud chcete začít používat větší sdílené složky, přečtěte si náš článek [Jak povolit a vytvořit velké sdílené složky souborů](storage-files-how-to-create-large-file-share.md).

## <a name="data-growth-pattern"></a>Model růstu dat

V současné době je maximální velikost sdílené složky Azure 100 TiB. Z důvodu tohoto současného omezení musíte při nasazování sdílené složky Azure vzít v úvahu očekávaný nárůst dat.

Je možné synchronizovat více sdílených složek Azure na jeden souborový server s Windows pomocí Azure File Sync. Díky tomu můžete zajistit, aby se starší, velké sdílené složky, které máte v místním prostředí, přenesly do Azure File Sync. Další informace najdete v tématu [Plánování nasazení Azure File Sync](storage-files-planning.md).

## <a name="data-transfer-method"></a>Metoda přenosu dat

Existuje mnoho jednoduchých možností, jak hromadně přenášet data z existující sdílené složky, jako je místní sdílená složka, do souborů Azure. Mezi několik oblíbených patří (nevyčerpávající seznam):

* **[Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning)** : jako součást první synchronizace mezi sdílenou složkou Azure (koncovým bodem cloudu) a oborem názvů adresáře Windows (koncový bod serveru) Azure File Sync replikuje všechna data z existující sdílené složky do souborů Azure.
* **[Import/export v Azure](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** : Služba Azure import/export umožňuje bezpečně přenášet velké objemy dat do sdílené složky Azure tím, že je přenáší jednotky pevného disku do datacentra Azure. 
* **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)** : Robocopy je dobře známý nástroj pro kopírování, který je dodáván s Windows a Windows serverem. Pomocí nástroje Robocopy můžete přenášet data do souborů Azure, a to tak, že sdílenou složku připojíte místně a potom v příkazu Robocopy použijete připojené umístění jako cíl.
* **[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** : AzCopy je nástroj příkazového řádku určený ke kopírování dat do a ze souborů Azure a také pro úložiště objektů BLOB v Azure pomocí jednoduchých příkazů s optimálním výkonem.

## <a name="next-steps"></a>Další kroky
* [Plánování nasazení Azure File Sync](storage-sync-files-planning.md)
* [Nasazení souborů Azure](storage-files-deployment-guide.md)
* [Nasazení Azure File Sync](storage-sync-files-deployment-guide.md)
