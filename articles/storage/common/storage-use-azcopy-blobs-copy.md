---
title: Kopírování objektů BLOB mezi účty úložiště Azure pomocí AzCopy v10 za účelem | Microsoft Docs
description: Tento článek obsahuje kolekci AzCopy ukázkových příkazů, které vám pomůžou zkopírovat objekty blob mezi účty úložiště.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 524e3f70f9588cfae2c739722fc1a44e683f9a7f
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617285"
---
# <a name="copy-blobs-between-azure-storage-accounts-by-using-azcopy-v10"></a>Kopírování objektů BLOB mezi účty úložiště Azure pomocí AzCopy v10 za účelem

Objekty blob, adresáře a kontejnery mezi účty úložiště můžete kopírovat pomocí nástroje příkazového řádku AzCopy v10 za účelem. 

Chcete-li zobrazit příklady pro jiné typy úloh, jako je například nahrávání souborů, stahování objektů BLOB a synchronizace s úložištěm objektů blob, přečtěte si odkazy v části [Další kroky](#next-steps) v tomto článku.

AzCopy používá [](/rest/api/storageservices/put-block-from-url) [rozhraní API](/rest/api/storageservices/put-page-from-url)pro servery, takže se data zkopírují přímo mezi úložnými servery. Tyto operace kopírování nepoužívají šířku pásma sítě vašeho počítače.

Pokud si chcete stáhnout AzCopy a získat informace o způsobech poskytování přihlašovacích údajů k autorizaci službě úložiště, přečtěte si téma Začínáme [s AzCopy](storage-use-azcopy-v10.md). 

## <a name="guidelines"></a>Pokyny

Pro příkazy AzCopy použijte následující pokyny. 

- Váš klient musí mít síťový přístup ke zdrojovému i cílovému účtu úložiště. Informace o tom, jak nakonfigurovat nastavení sítě pro každý účet úložiště, najdete v tématu [konfigurace Azure Storage bran firewall a virtuálních sítí](storage-network-security.md?toc=/azure/storage/blobs/toc.json).

- Připojí token SAS ke každé zdrojové adrese URL. 

  Pokud přihlašovací údaje pro autorizaci zadáte pomocí Azure Active Directory (Azure AD), můžete token SAS vynechat jenom z cílové adresy URL. Ujistěte se, že jste ve svém cílovém účtu nastavili správné role. Viz [možnost 1: použijte Azure Active Directory](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory). 

  V příkladech v tomto článku se předpokládá, že jste ověřili svoji identitu pomocí Azure AD, takže příklady vynechávají tokeny SAS z cílové adresy URL.

-  Pokud kopírujete účet úložiště objektů blob bloku Premium, vynechejte úroveň přístupu objektu BLOB z operace kopírování nastavením `s2s-preserve-access-tier` na `false` (například: `--s2s-preserve-access-tier=false` ). Účty úložiště blob bloku úrovně Premium nepodporují úrovně přístupu. 

- Pokud kopírujete do nebo z účtu, který má hierarchický obor názvů, použijte `blob.core.windows.net` místo `dfs.core.windows.net` v syntaxi URL. [Přístup k více protokolům na data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) umožňuje použít a jedná se `blob.core.windows.net` o jedinou podporovanou syntaxi pro účty pro scénáře kopírování účtů. 

- Propustnost operací kopírování můžete zvýšit nastavením hodnoty `AZCOPY_CONCURRENCY_VALUE` proměnné prostředí. Další informace najdete v tématu [optimalizace propustnosti](storage-use-azcopy-configure.md#optimize-throughput). 

- Pokud mají zdrojové objekty blob značky indexu a chcete je zachovat, budete je muset znovu použít v cílových objektech blob. Informace o tom, jak nastavit značky indexu, najdete v části [kopírování objektů blob do jiného účtu úložiště pomocí značek indexu](#copy-between-accounts-and-add-index-tags) v tomto článku.

## <a name="copy-a-blob"></a>Kopírování objektu BLOB

Zkopírujte objekt blob do jiného účtu úložiště pomocí příkazu [AzCopy Copy](storage-ref-azcopy-copy.md) . 

> [!TIP]
> Tento příklad uzavře argumenty cesty s jednoduchými uvozovkami (' '). Použijte jednoduché uvozovky ve všech příkazových prostředích s výjimkou příkazového prostředí systému Windows (cmd.exe). Pokud používáte příkazové prostředí systému Windows (cmd.exe), uzavřete argumenty cesty pomocí dvojitých uvozovek ("") místo jednoduchých uvozovek (' ').

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Příklad** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

Operace kopírování je synchronní, takže když příkaz vrátí výsledek, znamená to, že se zkopírovaly všechny soubory. 

## <a name="copy-a-directory"></a>Kopírování adresáře

Zkopírujte adresář do jiného účtu úložiště pomocí příkazu [AzCopy Copy](storage-ref-azcopy-copy.md) . 

> [!TIP]
> Tento příklad uzavře argumenty cesty s jednoduchými uvozovkami (' '). Použijte jednoduché uvozovky ve všech příkazových prostředích s výjimkou příkazového prostředí systému Windows (cmd.exe). Pokud používáte příkazové prostředí systému Windows (cmd.exe), uzavřete argumenty cesty pomocí dvojitých uvozovek ("") místo jednoduchých uvozovek (' ').

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Příklad** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

Operace kopírování je synchronní, takže když příkaz vrátí výsledek, znamená to, že se zkopírovaly všechny soubory.

## <a name="copy-a-container"></a>Kopírování kontejneru

Zkopírujte kontejner do jiného účtu úložiště pomocí příkazu [AzCopy Copy](storage-ref-azcopy-copy.md) .

> [!TIP]
> Tento příklad uzavře argumenty cesty s jednoduchými uvozovkami (' '). Použijte jednoduché uvozovky ve všech příkazových prostředích s výjimkou příkazového prostředí systému Windows (cmd.exe). Pokud používáte příkazové prostředí systému Windows (cmd.exe), uzavřete argumenty cesty pomocí dvojitých uvozovek ("") místo jednoduchých uvozovek (' ').

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Příklad** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

Operace kopírování je synchronní, takže když příkaz vrátí výsledek, znamená to, že se zkopírovaly všechny soubory.

## <a name="copy-containers-directories-and-blobs"></a>Kopírování kontejnerů, adresářů a objektů BLOB

Zkopírujte všechny kontejnery, adresáře a objekty blob do jiného účtu úložiště pomocí příkazu [AzCopy Copy](storage-ref-azcopy-copy.md) .

> [!TIP]
> Tento příklad uzavře argumenty cesty s jednoduchými uvozovkami (' '). Použijte jednoduché uvozovky ve všech příkazových prostředích s výjimkou příkazového prostředí systému Windows (cmd.exe). Pokud používáte příkazové prostředí systému Windows (cmd.exe), uzavřete argumenty cesty pomocí dvojitých uvozovek ("") místo jednoduchých uvozovek (' ').

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Příklad** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

Operace kopírování je synchronní, takže když příkaz vrátí výsledek, znamená to, že se zkopírovaly všechny soubory.

<a id="copy-between-accounts-and-add-index-tags"></a>

## <a name="copy-blobs-and-add-index-tags"></a>Kopírování objektů BLOB a přidání rejstříkových značek

Zkopírujte objekty blob do jiného účtu úložiště a přidejte do cílového objektu BLOB [značky indexu objektů BLOB (Preview)](../blobs/storage-manage-find-blobs.md) .

Pokud používáte autorizaci Azure AD, musí být k objektu zabezpečení přiřazená role [vlastníka dat objektu BLOB úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) nebo musí mít udělené oprávnění k `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [operaci poskytovatele prostředků Azure](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) prostřednictvím vlastní role Azure. Pokud používáte token sdíleného přístupového podpisu (SAS), musí tento token poskytovat přístup ke značkám objektu BLOB prostřednictvím `t` oprávnění SAS.

Pokud chcete přidat značky, použijte `--blob-tags` možnost spolu s dvojicí klíč-hodnota s kódováním URL. 

Chcete-li například přidat klíč `my tag` a hodnotu `my tag value` , přidejte `--blob-tags='my%20tag=my%20tag%20value'` do cílového parametru. 

Více indexových značek oddělte pomocí ampersandu ( `&` ).  Například pokud chcete přidat klíč `my second tag` a hodnotu `my second tag value` , úplný řetězec možnosti by byl `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` .

Následující příklady ukazují, jak použít `--blob-tags` možnost.

> [!TIP]
> Tyto příklady uzavírají argumenty cesty s jednoduchými uvozovkami (' '). Použijte jednoduché uvozovky ve všech příkazových prostředích s výjimkou příkazového prostředí systému Windows (cmd.exe). Pokud používáte příkazové prostředí systému Windows (cmd.exe), uzavřete argumenty cesty pomocí dvojitých uvozovek ("") místo jednoduchých uvozovek (' ').

|    |     |
|--------|-----------|
| **Objekt blob** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Adresář** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Kontejner** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Účet** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |

Operace kopírování je synchronní, takže když příkaz vrátí výsledek, znamená to, že se zkopírovaly všechny soubory.

> [!NOTE]
> Pokud pro zdroj určíte adresář, kontejner nebo účet, všechny objekty blob, které se zkopírují do cíle, budou mít stejné značky, které zadáte v příkazu.

## <a name="copy-with-optional-flags"></a>Kopírovat s volitelnými příznaky

Operaci kopírování můžete upravit pomocí volitelných příznaků. Tady je několik příkladů.

|Scénář|Příznak|
|---|---|
|Zkopírujte objekty BLOB jako blok, stránku nebo doplňovací objekty blob.|**--BLOB-typ** = \[ BlockBlob \| PageBlob \| AppendBlob\]|
|Zkopírujte do konkrétní úrovně přístupu (například do archivní vrstvy).|**--Block-– úroveň** = \[ objektu BLOB Žádný \| horká \| studená \| archiv\]|
|Automatické dekomprimace souborů|**--dekomprese** = \[ GZIP – \| uprostřed\]|

Úplný seznam najdete v tématu [Možnosti](storage-ref-azcopy-copy.md#options). 

## <a name="next-steps"></a>Další kroky

Další příklady najdete v těchto článcích:

- [Příklady: Odeslat](storage-use-azcopy-blobs-upload.md)
- [Příklady: Stáhnout](storage-use-azcopy-blobs-download.md)
- [Příklady: Synchronizace](storage-use-azcopy-blobs-synchronize.md)
- [Příklady: Kbelíky Amazon S3](storage-use-azcopy-s3.md)
- [Příklady: soubory Azure](storage-use-azcopy-files.md)
- [Kurz: Migrace místních dat do cloudového úložiště pomocí AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Konfigurace, optimalizace a řešení potíží s AzCopy](storage-use-azcopy-configure.md)