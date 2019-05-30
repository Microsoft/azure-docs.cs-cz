---
title: Kopírování nebo přesun dat do služby Azure Storage pomocí AzCopy v10 | Dokumentace Microsoftu
description: AzCopy je nástroj příkazového řádku, který můžete použít ke zkopírování dat na, z nebo mezi účty úložiště. Tento článek pomůže stáhnout AzCopy, připojte se k účtu úložiště a pak přenos souborů.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: cc65d6d3f7e7dcc08ea29ecc8a299b556563135b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236321"
---
# <a name="get-started-with-azcopy"></a>Začínáme s AzCopy

AzCopy je nástroj příkazového řádku, který vám pomůže zkopírovat objekty BLOB nebo souborů do nebo z účtu úložiště. Tento článek pomůže stáhnout AzCopy, připojte se k účtu úložiště a pak přenos souborů.

> [!NOTE]
> AzCopy **V10** je aktuálně podporované verze nástroje AzCopy.
>
> Pokud je potřeba pomocí nástroje AzCopy **v8.1**, najdete v článku [v předchozí verzi AzCopy](#previous-version) části tohoto článku.

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>Stáhněte si nástroje AzCopy

Nejprve stáhněte spustitelný soubor AzCopy V10 do libovolné složky ve vašem počítači. Pro usnadnění zvažte přidání umístění složky AzCopy do systémové cesty pro snadné použití.

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (cíl)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

> [!NOTE]
> Pokud chcete zkopírovat data do a z vašich [Azure Table storage](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) služby, potom nainstalovat [AzCopy verzi 7.3](https://aka.ms/downloadazcopynet).

## <a name="run-azcopy"></a>Spuštění nástroje AzCopy

Z příkazového řádku přejděte do adresáře, kam jste stáhli soubor.

Chcete-li zobrazit seznam příkazů AzCopy, zadejte `azCopy`a potom stiskněte klávesu ENTER.

Další informace o konkrétním příkazu, zadejte `azCopy` za nímž následuje název příkazu.

Například pro další informace o `copy` příkazu, zadejte `azcopy copy`a potom stiskněte klávesu ENTER.

Předtím, než můžete dělat nic smysluplné pomocí nástroje AzCopy, musíte se rozhodnout, jak vám poskytnout přihlašovací údaje pro ověření do služby storage.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Zvolte, jak vám poskytnout přihlašovací údaje pro autorizaci

Můžete zadat přihlašovací údaje pro ověření pomocí Azure Active Directory (AD) nebo pomocí tokenu sdíleného přístupového podpisu (SAS).

Tuto tabulku použijte jako vodítko:

| Typ úložiště | Aktuálně podporované metody ověřování |
|--|--|
|**Blob Storage** | Azure AD & SAS |
|**Úložiště objektů BLOB (hierarchické oboru názvů)** | Pouze Azure AD |
|**Úložiště souborů** | Pouze SAS |

### <a name="option-1-use-azure-ad"></a>Option 1: Pomocí služby Azure AD

Úroveň ověřování, který je třeba vychází Určuje, zda chcete nahrát soubory nebo je právě stáhnout.

#### <a name="authorization-to-upload-files"></a>Oprávnění k nahrání souborů

Ověřte, že jednu z těchto rolí se přiřadila k vaší identitě:

- [Storage Blob Data Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Vlastník dat úložiště objektů Blob](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Tyto role je možné přiřadit k vaší identity v některém z těchto oborů:

- Kontejner (systém souborů)
- Účet úložiště
- Skupina prostředků
- Předplatné

Zjistěte, jak ověřit a přiřazení rolí, najdete v článku [udělit přístup k Azure data objektů blob a fronty pomocí RBAC na webu Azure Portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Není nutné mít jednu z těchto rolí přiřadit k vaší identity, pokud vaši identitu se přidá do seznamu řízení přístupu (ACL) cílový kontejner nebo složky. V seznamu ACL potřebuje vaši identitu v cílové složce oprávnění k zápisu a oprávnění spouštět v kontejneru a každé nadřazené složky.

Další informace najdete v tématu [řízení přístupu v Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authorization-to-download-files"></a>Povolení ke stahování souborů

Ověřte, že jednu z těchto rolí se přiřadila k vaší identitě:

- [Čtenář dat objektu Blob služby Storage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)
- [Storage Blob Data Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Vlastník dat úložiště objektů Blob](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Tyto role je možné přiřadit k vaší identity v některém z těchto oborů:

- Kontejner (systém souborů)
- Účet úložiště
- Skupina prostředků
- Předplatné

Zjistěte, jak ověřit a přiřazení rolí, najdete v článku [udělit přístup k Azure data objektů blob a fronty pomocí RBAC na webu Azure Portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Není nutné mít jednu z těchto rolí přiřadit k vaší identity, pokud vaši identitu se přidá do seznamu řízení přístupu (ACL) cílový kontejner nebo složky. V seznamu ACL musí vaši identitu v cílové složce oprávnění ke čtení a provádění oprávnění na kontejner a všechny nadřazené složky.

Další informace najdete v tématu [řízení přístupu v Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authenticate-your-identity"></a>Ověření vaší identity

Po ověření, že vaši identitu nebyla zadána potřebnou úroveň oprávnění, otevřete příkazový řádek, zadejte následující příkaz a stiskněte klávesu ENTER.

```azcopy
azcopy login
```

Tento příkaz vrátí ověřovacího kódu a adresu URL webu. Otevřít web, zadejte kód a pak zvolte **Další** tlačítko.

![Vytvoření kontejneru](media/storage-use-azcopy-v10/azcopy-login.png)

Zobrazí se okno přihlášení. V tomto okně přihlaste ke svému účtu Azure pomocí přihlašovacích údajů k účtu Azure. Po úspěšném jste přihlášení, můžete zavřít okno prohlížeče a začít používat AzCopy.

### <a name="option-2-use-a-sas-token"></a>Option 2: Pomocí tokenu SAS

SAS token můžete připojit k každý zdroj nebo cíl URL, kterou používají vaše příkazů AzCopy.

Tento příklad příkazu rekurzivně kopíruje data z místního adresáře do kontejneru objektů blob. Fiktivní token SAS je připojen na konec objektu adresy URL kontejneru.

```azcopy
azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Další informace o tokeny SAS a jak ho získat, najdete v článku [použití sdílených přístupových podpisů (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="transfer-files"></a>Přenos souborů

Když si ověřit vaši identitu a získat SAS token, můžete začít přenos souborů.

Příkazy v příkladu najdete v tématu některého z těchto článků.

- [Přenos dat pomocí AzCopy a blob storage](storage-use-azcopy-blobs.md)

- [Přenos dat pomocí AzCopy a file storage](storage-use-azcopy-files.md)

- [Přenos dat pomocí AzCopy a Amazon S3 intervalů](storage-use-azcopy-s3.md)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>Konfigurace, optimalizovat a řešení potíží s AzCopy

Zobrazit [konfigurovat, optimalizovat a řešení potíží s AzCopy](storage-use-azcopy-configure.md)

## <a name="use-azcopy-in-storage-explorer"></a>V Průzkumníku služby Storage pomocí AzCopy

Pokud chcete využít výhody výkonu AzCopy, ale budete chtít pracovat se soubory pomocí Průzkumníka služby Storage namísto příkazového řádku, povolte AzCopy v Průzkumníku služby Storage.

V Průzkumníku služby Storage vyberte **ve verzi Preview**->**použijte AzCopy k vylepšení nahrát objekt Blob a stažení**.

![Povolte AzCopy jako modul přenosu v Průzkumníku služby Storage](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

> [!NOTE]
> Není nutné toto nastavení povolte, pokud jste povolili hierarchického oboru názvů na vašem účtu úložiště. Který je, protože Průzkumníka služby Storage automaticky pomocí nástroje AzCopy pro účty úložiště, které mají hierarchického oboru názvů.  

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>V předchozí verzi AzCopy

Pokud potřebujete předchozí verzi AzCopy (AzCopy v8.1), naleznete v některé z následujících odkazů:

- [AzCopy ve Windows (v8:)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)
- [AzCopy v Linuxu (v8:)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="next-steps"></a>Další postup

Pokud máte dotazy, problémy nebo obecnou zpětnou, odešlete je [na Githubu](https://github.com/Azure/azure-storage-azcopy) stránky.
