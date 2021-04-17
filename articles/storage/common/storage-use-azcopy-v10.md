---
title: Kopírování nebo přesun dat do Azure Storage pomocí AzCopy v10 za účelem | Microsoft Docs
description: AzCopy je nástroj příkazového řádku, který můžete použít ke kopírování dat do, z nebo mezi účty úložiště. Tento článek vám pomůže se stažením nástroje AzCopy, připojením k vašemu účtu úložiště a následným přenosem souborů.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.custom: contperf-fy21q2
ms.openlocfilehash: 34d3bd45d2c0bf0260a4f8524cff6f8ac03b746c
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501712"
---
# <a name="get-started-with-azcopy"></a>Začínáme s nástrojem AzCopy

AzCopy je nástroj příkazového řádku, pomocí kterého můžete kopírovat objekty blob nebo soubory do nebo z účtu úložiště. Tento článek vám pomůže se stažením nástroje AzCopy, připojením k vašemu účtu úložiště a následným přenosem souborů.

> [!NOTE]
> AzCopy **v10 za účelem** je aktuálně podporovaná verze AzCopy.
>
> Pokud potřebujete použít předchozí verzi AzCopy, přečtěte si část [Použití předchozí verze AzCopy](#previous-version) tohoto článku.

<a id="download-and-install-azcopy"></a>

## <a name="download-azcopy"></a>Stažení nástroje AzCopy

Nejdřív Stáhněte spustitelný soubor AzCopy v10 za účelem do libovolného adresáře v počítači. AzCopy v10 za účelem je jenom spustitelný soubor, takže nemusíte nic instalovat.

- [Windows 64 – bit](https://aka.ms/downloadazcopy-v10-windows) (ZIP)
- [Windows 32 – bit](https://aka.ms/downloadazcopy-v10-windows-32bit) (ZIP)
- [Linux X86-64](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (ZIP)

Tyto soubory se komprimují jako soubor zip (Windows a Mac) nebo jako soubor s tar (Linux). Chcete-li stáhnout a dekomprimovat soubor tar v systému Linux, přečtěte si dokumentaci k distribuci systému Linux.

> [!NOTE]
> Pokud chcete kopírovat data do služby [Azure Table Storage](../tables/table-storage-overview.md) a z ní, nainstalujte [AzCopy verze 7,3](https://aka.ms/downloadazcopynet).

## <a name="run-azcopy"></a>Spuštění nástroje AzCopy

Pro usnadnění práce zvažte přidání umístění adresáře spustitelného souboru AzCopy do systémové cesty. Tímto způsobem můžete zadat `azcopy` libovolný adresář v systému.

Pokud se rozhodnete Nepřidávat adresář AzCopy do cesty, budete muset změnit adresáře na umístění spustitelného souboru AzCopy a typu `azcopy` nebo `.\azcopy` v příkazovém řádku prostředí Windows PowerShell.

Jako vlastník účtu Azure Storage nemáte automaticky přiřazená oprávnění pro přístup k datům. Než budete moct s AzCopy dělat něco smysluplného, budete se muset rozhodnout, jak zadáte přihlašovací údaje pro autorizaci službě úložiště. 

<a id="choose-how-youll-provide-authorization-credentials"></a>

## <a name="authorize-azcopy"></a>Autorizovat AzCopy

Autorizační přihlašovací údaje můžete zadat pomocí Azure Active Directory (AD) nebo pomocí tokenu sdíleného přístupového podpisu (SAS).

Tuto tabulku použijte jako vodítko:

| Typ úložiště | Aktuálně podporovaná metoda autorizace |
|--|--|
|**Blob Storage** | Azure AD a SAS |
|**Úložiště objektů BLOB (hierarchický obor názvů)** | Azure AD a SAS |
|**File Storage** | Pouze SAS |

#### <a name="option-1-use-azure-active-directory"></a>Možnost 1: použití Azure Active Directory

Tato možnost je k dispozici pouze pro úložiště objektů BLOB. Pomocí Azure Active Directory můžete zadat přihlašovací údaje místo toho, abyste museli k jednotlivým příkazům připojit token SAS.  

> [!NOTE]
> Pokud v aktuální verzi plánujete zkopírovat objekty blob mezi účty úložiště, budete muset ke každé zdrojové adrese URL připojit token SAS. Token SAS můžete vynechat pouze z cílové adresy URL. Příklady najdete v tématu [kopírování objektů BLOB mezi účty úložiště](#transfer-data).

Pokud chcete autorizovat přístup pomocí Azure AD, přečtěte si téma [autorizace přístupu k objektům blob pomocí AzCopy a Azure Active Directory (Azure AD)](storage-use-azcopy-authorize-azure-active-directory.md).

#### <a name="option-2-use-a-sas-token"></a>Možnost 2: použití tokenu SAS

Token SAS můžete připojit ke každé zdrojové nebo cílové adrese URL, která se používá v příkazech AzCopy.

Tento ukázkový příkaz rekurzivně kopíruje data z místního adresáře do kontejneru objektů BLOB. Fiktivní token SAS je připojen ke konci adresy URL kontejneru.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Další informace o tokenech SAS a o tom, jak je získat, najdete v tématu [použití sdílených přístupových podpisů (SAS)](./storage-sas-overview.md).

> [!NOTE]
> Nastavení [vyžadováno zabezpečeným přenosem](storage-require-secure-transfer.md) účtu úložiště Určuje, jestli je připojení k účtu úložiště zabezpečené pomocí protokolu TLS (Transport Layer Security). Standardně je toto nastavení povolené.   

<a id="transfer-data"></a>

## <a name="transfer-data"></a>Přenos dat

Po ověření identity nebo získání tokenu SAS můžete začít přenášet data.

Ukázkové příkazy najdete v některém z těchto článků.

| Služba | Článek |
|--------|-----------|
|Azure Blob Storage|[Nahrání souborů do Azure Blob Storage](storage-use-azcopy-blobs-upload.md) |
|Azure Blob Storage|[Stažení objektů BLOB z Azure Blob Storage](storage-use-azcopy-blobs-download.md)|
|Azure Blob Storage|[Kopírování objektů BLOB mezi účty úložiště Azure](storage-use-azcopy-blobs-copy.md)|
|Azure Blob Storage|[Synchronizace s Azure Blob Storage](storage-use-azcopy-blobs-synchronize.md)|
|Azure Files |[Přenos dat s použitím AzCopy a úložiště souborů](storage-use-azcopy-files.md)|
|Amazon S3|[Kopírovat data z Amazon S3 do Azure Storage](storage-use-azcopy-s3.md)|
|Cloudové úložiště Googlu|[Kopírování dat z Google Cloud Storage do Azure Storage (Preview)](storage-use-azcopy-google-cloud.md)|
|Azure Stack úložiště|[Přenos dat pomocí úložiště AzCopy a Azure Stack](/azure-stack/user/azure-stack-storage-transfer#azcopy)|

## <a name="get-command-help"></a>Získat nápovědu k příkazu

Pokud chcete zobrazit seznam příkazů, zadejte `azcopy -h` a potom stiskněte klávesu ENTER.

Chcete-li získat informace o konkrétním příkazu, stačí zahrnout název příkazu (například: `azcopy list -h` ).

> [!div class="mx-imgBorder"]
> ![Vložená Help](media/storage-use-azcopy-v10/azcopy-inline-help.png)

### <a name="list-of-commands"></a>Seznam příkazů

V následující tabulce jsou uvedeny všechny příkazy AzCopy v10 za účelem. Každý příkaz odkazuje na odkazový článek. 

|Příkaz|Popis|
|---|---|
|[azcopy bench](storage-ref-azcopy-bench.md?toc=/azure/storage/blobs/toc.json)|Spustí srovnávací test výkonu odesláním nebo stažením testovacích dat do nebo ze zadaného umístění.|
|[azcopy copy](storage-ref-azcopy-copy.md?toc=/azure/storage/blobs/toc.json)|Zkopíruje zdrojová data do cílového umístění.|
|[azcopy doc](storage-ref-azcopy-doc.md?toc=/azure/storage/blobs/toc.json)|Vygeneruje dokumentaci pro nástroj ve formátu Markdownu.|
|[azcopy env](storage-ref-azcopy-env.md?toc=/azure/storage/blobs/toc.json)|Zobrazuje proměnné prostředí, které můžou konfigurovat chování AzCopy.|
|[azcopy jobs](storage-ref-azcopy-jobs.md?toc=/azure/storage/blobs/toc.json)|Dílčí příkazy související se správou úloh.|
|[azcopy jobs clean](storage-ref-azcopy-jobs-clean.md?toc=/azure/storage/blobs/toc.json)|Odeberte všechny soubory protokolu a souboru s plánem pro všechny úlohy.|
|[azcopy jobs list](storage-ref-azcopy-jobs-list.md?toc=/azure/storage/blobs/toc.json)|Zobrazí informace o všech úlohách.|
|[azcopy jobs remove](storage-ref-azcopy-jobs-remove.md?toc=/azure/storage/blobs/toc.json)|Odebere všechny soubory přidružené k danému ID úlohy.|
|[azcopy jobs resume](storage-ref-azcopy-jobs-resume.md?toc=/azure/storage/blobs/toc.json)|Obnoví existující úlohu s daným ID úlohy.|
|[azcopy jobs show](storage-ref-azcopy-jobs-show.md?toc=/azure/storage/blobs/toc.json)|Zobrazí podrobné informace o daném ID úlohy.|
|[azcopy load](storage-ref-azcopy-load.md)|Dílčí příkazy související s přenosem dat v konkrétních formátech.|
|[azcopy load clfs](storage-ref-azcopy-load-avere-cloud-file-system.md?toc=/azure/storage/blobs/toc.json)|Převede místní data do kontejneru a uloží ho do formátu CLFS (avere Cloud FileSystem) Microsoftu.|
|[azcopy list](storage-ref-azcopy-list.md?toc=/azure/storage/blobs/toc.json)|Zobrazí seznam entit v daném prostředku.|
|[azcopy login](storage-ref-azcopy-login.md?toc=/azure/storage/blobs/toc.json)|Přihlásí se k Azure Active Directory pro přístup k prostředkům Azure Storage.|
|[azcopy logout](storage-ref-azcopy-logout.md?toc=/azure/storage/blobs/toc.json)|Zaznamená uživatele a ukončí přístup k prostředkům Azure Storage.|
|[azcopy make](storage-ref-azcopy-make.md?toc=/azure/storage/blobs/toc.json)|Vytvoří kontejner nebo sdílenou složku souborů.|
|[azcopy remove](storage-ref-azcopy-remove.md?toc=/azure/storage/blobs/toc.json)|Odstraňte objekty blob nebo soubory z účtu služby Azure Storage.|
|[azcopy sync](storage-ref-azcopy-sync.md?toc=/azure/storage/blobs/toc.json)|Replikuje zdrojové umístění do cílového umístění.|

## <a name="use-in-a-script"></a>Použití ve skriptu

#### <a name="obtain-a-static-download-link"></a>Získat odkaz na statický ke stažení

V průběhu času bude [odkaz ke stažení](#download-and-install-azcopy) AzCopy ukazovat na nové verze AzCopy. Pokud váš skript stahuje AzCopy, může skript přestat fungovat, pokud novější verze AzCopy upraví funkce, na kterých váš skript závisí.

Chcete-li se těmto problémům vyhnout, Získejte statický (nezměněný) odkaz na aktuální verzi AzCopy. Díky tomu váš skript při každém spuštění stáhne stejnou přesnou verzi AzCopy.

Chcete-li získat odkaz, spusťte tento příkaz:

| Operační systém  | Příkaz |
|--------|-----------|
| **Linux** | `curl -s -D- https://aka.ms/downloadazcopy-v10-linux | grep ^Location` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).headers.location` |

> [!NOTE]
> Pro Linux `--strip-components=1` `tar` příkaz v příkazu odebere složku na nejvyšší úrovni, která obsahuje název verze, a místo toho extrahuje binární soubor přímo do aktuální složky. Tím umožníte, aby se skript aktualizoval pomocí nové verze nástroje `azcopy` , a to tak, že aktualizuje `wget` adresu URL.

Adresa URL se zobrazí ve výstupu tohoto příkazu. Skript pak může stáhnout AzCopy pomocí této adresy URL.

| Operační systém  | Příkaz |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

#### <a name="escape-special-characters-in-sas-tokens"></a>Sekvence speciálních znaků v tokenech SAS

V dávkových souborech, které mají `.cmd` rozšíření, je nutné řídicí znaky, `%` které se zobrazí v tokenech SAS. To lze provést přidáním dalšího `%` znaku vedle existujících `%` znaků v řetězci tokenu SAS.

#### <a name="run-scripts-by-using-jenkins"></a>Spouštění skriptů pomocí Jenkinse

Pokud máte v úmyslu spouštět skripty pomocí [Jenkinse](https://jenkins.io/) , nezapomeňte na začátek skriptu umístit následující příkaz.

```
/usr/bin/keyctl new_session
```

## <a name="use-in-azure-storage-explorer"></a>Použít v Průzkumník služby Azure Storage

[Průzkumník služby Storage](https://azure.microsoft.com/features/storage-explorer/) používá AzCopy k provádění všech operací přenosu dat. [Průzkumník služby Storage](https://azure.microsoft.com/features/storage-explorer/) můžete použít, pokud chcete využívat výhody výkonu AzCopy, ale dáváte přednost použití grafického uživatelského rozhraní, nikoli příkazového řádku pro interakci se soubory.

Průzkumník služby Storage používá k provádění operací klíč účtu, takže po přihlášení k Průzkumník služby Storage nebudete muset zadávat další přihlašovací údaje pro autorizaci.

<a id="previous-version"></a>

## <a name="configure-optimize-and-fix"></a>Konfigurace, optimalizace a oprava

Podívejte se na některý z následujících zdrojů:

- [Nastavení konfigurace AzCopy](storage-ref-azcopy-configuration-settings.md)

- [Optimalizace výkonu AzCopy](storage-use-azcopy-optimize.md)

- [Řešení potíží s AzCopy v10 za účelem v Azure Storage pomocí souborů protokolu](storage-use-azcopy-configure.md)

## <a name="use-a-previous-version"></a>Použít předchozí verzi

Pokud potřebujete použít předchozí verzi AzCopy, podívejte se na některý z následujících odkazů:

- [AzCopy ve Windows (v8)](/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy on Linux (v7)](/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="next-steps"></a>Další kroky

Pokud máte dotazy, problémy nebo obecné názory, odešlete je [na stránku GitHub](https://github.com/Azure/azure-storage-azcopy) .
