---
title: Řízení přístupu účtu úložiště pro SQL na vyžádání (Preview)
description: Popisuje, jak SQL na vyžádání (Preview) přistupuje k Azure Storage a jak můžete řídit přístup k úložišti pro SQL na vyžádání v Azure synapse Analytics.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0d2683091898e9c84457b3b538776f0e6b0469d4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424031"
---
# <a name="control-storage-account-access-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Řízení přístupu účtu úložiště pro SQL na vyžádání (Preview) ve službě Azure synapse Analytics

Dotaz SQL na vyžádání (ve verzi Preview) čte soubory přímo z Azure Storage. Vzhledem k tomu, že účet úložiště je objekt, který je externě pro prostředek SQL na vyžádání, jsou vyžadovány příslušné přihlašovací údaje. Uživatel potřebuje příslušná oprávnění udělená pro použití požadovaných přihlašovacích údajů. Tento článek popisuje typy přihlašovacích údajů, které můžete použít, a informace o tom, jak jsou pro uživatele SQL a Azure AD vyhledány přihlašovací údaje.

## <a name="supported-storage-authorization-types"></a>Podporované typy autorizace úložiště

Uživatel, který se přihlásil k prostředku na vyžádání SQL, musí mít oprávnění pro přístup k souborům v Azure Storage a dotazování na ně. Podporují se tři typy autorizace:

- [Sdílený přístupový podpis](#shared-access-signature)
- [Spravovaná identita](#managed-identity)
- [Identita uživatele](#user-identity)

> [!NOTE]
> [Předávací služba Azure AD](#force-azure-ad-pass-through) je výchozím chováním při vytváření pracovního prostoru. Pokud ho použijete, nemusíte vytvářet přihlašovací údaje pro každý účet úložiště, ke kterému jste přistupovali pomocí přihlašovacích údajů služby AD. [Toto chování můžete zakázat](#disable-forcing-azure-ad-pass-through).

V následující tabulce najdete různé typy autorizací, které jsou buď podporovány, nebo budou brzy podporovány.

| Typ autorizace                    | *Uživatel SQL*    | *Uživatel Azure AD*     |
| ------------------------------------- | ------------- | -----------    |
| [VEDE](#shared-access-signature)       | Podporuje se     | Podporuje se      |
| [Spravovaná identita](#managed-identity) | Nepodporuje se | Nepodporuje se  |
| [Identita uživatele](#user-identity)       | Nepodporuje se | Podporuje se      |

### <a name="shared-access-signature"></a>Sdílený přístupový podpis

**Sdílený přístupový podpis (SAS)** poskytuje delegovaný přístup k prostředkům v účtu úložiště. Pomocí SAS může zákazník udělit klientům přístup k prostředkům v účtu úložiště bez použití klíčů účtu pro sdílení. SAS vám poskytuje podrobnější kontrolu nad typem přístupu, který udělíte klientům, kteří mají SAS, včetně intervalu platnosti, udělených oprávnění, přijatelného rozsahu IP adres a přijatelného protokolu (HTTPS/HTTP).

Token SAS můžete získat tak, že přejdete na **účet úložiště > Azure Portal – > sdílený přístup – > konfigurace oprávnění – > generovat SAS a připojovací řetězec.**

> [!IMPORTANT]
> Při vygenerování tokenu SAS obsahuje znak otazníku (?) na začátku tokenu. Pokud chcete použít token v SQL na vyžádání, musíte při vytváření přihlašovacích údajů odebrat otazník (?). Příklad:
>
> Token SAS:? sv = 2018-03-28&SS = bfqt&SRT aplikace = SCO&SP = rwdlacup&se = 2019-04-18T20:42:12Z&St = 2019-04-18T12:42:12Z&spr = https&SIG = lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78% 3D

### <a name="user-identity"></a>Identita uživatele

**Identita uživatele**, označovaná také jako "průchozí", je autorizační typ, ve kterém se k autorizaci přístupu k datům používá identita uživatele služby Azure AD, který se přihlásil k SQL na vyžádání. Před přístupem k datům musí správce Azure Storage udělit oprávnění k uživateli Azure AD. Jak je uvedeno v tabulce výše, není podporováno pro typ uživatele SQL.

> [!NOTE]
> Pokud používáte [předávací službu Azure AD](#force-azure-ad-pass-through) , nemusíte vytvářet přihlašovací údaje pro každý účet úložiště, ke kterému jste přistupovali pomocí přihlášení AD.

> [!IMPORTANT]
> Abyste mohli používat vaši identitu pro přístup k datům, musíte mít roli vlastníka dat objektu BLOB úložiště/Přispěvatel/čtenář.
> I v případě, že jste vlastníkem účtu úložiště, je stále nutné přidat sami sebe do jedné z rolí dat objektu BLOB úložiště.
>
> Další informace o řízení přístupu v Azure Data Lake Store Gen2 naleznete [v tématu řízení přístupu v Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-access-control.md) článku.
>

### <a name="managed-identity"></a>Spravovaná identita

**Spravovaná identita** se také označuje jako MSI. Je to funkce Azure Active Directory (Azure AD), která poskytuje služby Azure pro SQL na vyžádání. Také nasadí automaticky spravovanou identitu ve službě Azure AD. Tato identita se dá použít k autorizaci žádosti o přístup k datům v Azure Storage.

Před přístupem k datům musí správce Azure Storage udělit oprávnění ke spravované identitě pro přístup k datům. Udělení oprávnění pro spravovanou identitu se provádí stejným způsobem jako udělení oprávnění jinému uživateli Azure AD.

## <a name="create-credentials"></a>Vytvořit pověření

Pokud chcete zadat dotaz na soubor umístěný v Azure Storage, vyžaduje koncový bod SQL na vyžádání přihlašovací údaje na úrovni serveru, které obsahují ověřovací údaje. Přihlašovací údaje se přidají spuštěním [Vytvoření přihlašovacích údajů](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Budete muset zadat argument název PŘIHLAŠOVACÍch údajů. Musí odpovídat buď části cesty, nebo celé cestě k datům v úložišti (viz níže).

> [!NOTE]
> Argument pro zprostředkovatele KRYPTOGRAFICKÝch služeb není podporován.

U všech podporovaných typů autorizace můžou přihlašovací údaje ukazovat na účet, kontejner, libovolný adresář (nekořen) nebo jeden soubor.

PŘIHLAŠOVACÍ jméno musí odpovídat celé cestě ke kontejneru, složce nebo souboru v následujícím formátu:`<prefix>://<storage_account_path>/<storage_path>`

| Externí zdroj dat       | Předpona | Cesta k účtu úložiště                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | HTTPS  | <storage_account>. blob.core.windows.net             |
| Azure Data Lake Storage Gen1 | HTTPS  | <storage_account>. azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | HTTPS  | <storage_account>. dfs.core.windows.net              |

 ' <storage_path> ' je cesta v rámci úložiště, která odkazuje na složku nebo soubor, který chcete číst.

> [!NOTE]
> K dispozici je speciální `UserIdentity` přihlašovací jméno, které [vynucuje předávací službu Azure AD](#force-azure-ad-pass-through). Přečtěte si o tom, jaký vliv má při provádění dotazů na [vyhledávání přihlašovacích údajů](#credential-lookup) .

Pokud chcete, aby uživatel mohl vytvořit nebo vyřadit přihlašovací údaje, může správce udělit nebo odepřít změnu oprávnění pro všechny přihlašovací údaje pro uživatele:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

### <a name="supported-storages-and-authorization-types"></a>Podporované typy úložišť a autorizace

Můžete použít následující kombinace autorizačních a Azure Storagech typů:

|                     | Blob Storage   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| *VEDE*               | Podporuje se      | Nepodporováno   | Podporuje se     |
| *Spravovaná identita* | Nepodporuje se  | Nepodporuje se    | Nepodporuje se |
| *Identita uživatele*    | Podporuje se      | Podporuje se        | Podporuje se     |

### <a name="examples"></a>Příklady

V závislosti na [typu ověřování](#supported-storage-authorization-types)můžete přihlašovací údaje vytvořit pomocí níže uvedené syntaxe T-SQL.

**Sdílený přístupový podpis a Blob Storage**

Exchange <*mystorageaccountname*> s vaším skutečným názvem účtu úložiště a> <*mystorageaccountcontainername* s aktuálním názvem kontejneru:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

**Identita uživatele a Azure Data Lake Storage Gen1**

Exchange <*mystorageaccountname*> s vaším skutečným názvem účtu úložiště a> <*mystorageaccountcontainername* s aktuálním názvem kontejneru:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.azuredatalakestore.net/webhdfs/v1/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

**Identita uživatele a Azure Data Lake Storage Gen2**

Exchange <*mystorageaccountname*> s vaším skutečným názvem účtu úložiště a> <*mystorageaccountcontainername* s aktuálním názvem kontejneru:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.dfs.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

## <a name="force-azure-ad-pass-through"></a>Vynutit předávací službu Azure AD

Vynucení předávacího průchodu Azure AD je výchozím chováním, které dosáhlo speciálního `UserIdentity`názvu PŘIHLAŠOVACÍho údaje, který se vytvoří automaticky během zřizování pracovních prostorů Azure synapse. Vynucuje použití předávacího průchodu Azure AD pro každý dotaz každého přihlášení služby Azure AD, ke kterému dojde bez ohledu na existenci jiných přihlašovacích údajů.

> [!NOTE]
> Předávací služba Azure AD je výchozí chování. Nemusíte vytvářet přihlašovací údaje pro každý účet úložiště, ke kterému přistupovali prostřednictvím přihlášení AD.

Pokud jste [zakázali vynucení předávacího procesu Azure AD pro každý dotaz](#disable-forcing-azure-ad-pass-through)a chcete ho znovu povolit, spusťte:

```sql
CREATE CREDENTIAL [UserIdentity]
WITH IDENTITY = 'User Identity';
```

Pokud chcete povolit vynucení předávacího průchodu Azure AD pro konkrétního uživatele, můžete tomuto konkrétnímu uživateli `UserIdentity` udělit referenční oprávnění k přihlašovacím údajům. Následující příklad umožňuje vynucení předávacího průchodu Azure AD pro user_name:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

Další informace o tom, jak SQL na vyžádání vyhledá přihlašovací údaje k použití, najdete v tématu [vyhledání přihlašovacích údajů](#credential-lookup).

## <a name="disable-forcing-azure-ad-pass-through"></a>Zakázat vynucení předávacího Azure AD

[Pro každý dotaz můžete zakázat vynucení předávacího Azure AD](#force-azure-ad-pass-through). Pokud ho chcete zakázat, vyřaďte `Userdentity` přihlašovací údaje pomocí:

```sql
DROP CREDENTIAL [UserIdentity];
```

Pokud ho chcete znovu povolit, přečtěte si část [vynucení předávacího rozhraní Azure AD](#force-azure-ad-pass-through) .

Chcete-li zakázat vynucení předávacího průchodu Azure AD pro konkrétního uživatele, můžete odepřít oprávnění REFERENCE `UserIdentity` pro přihlašovací údaje konkrétního uživatele. Následující příklad zakáže vynucené předávání Azure AD pro user_name:

```sql
DENY REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

Další informace o tom, jak SQL na vyžádání vyhledává pověření k použití, najdete v tématu [vyhledání přihlašovacích údajů](#credential-lookup).

## <a name="grant-permissions-to-use-credential"></a>Udělení oprávnění k použití přihlašovacích údajů

Aby uživatel mohl používat přihlašovací údaje, musí mít pro konkrétní přihlašovací údaje oprávnění reference. Chcete-li udělit oprávnění k ODKAZům na storage_credential pro specific_user, proveďte následující:

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

Aby bylo zajištěno bezproblémové předávací prostředí Azure AD, budou mít všichni uživatelé ve výchozím nastavení právo používat `UserIdentity` přihlašovací údaje. Toho je dosaženo automatickým provedením následujícího příkazu při zřizování pracovního prostoru Azure synapse:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO [public];
```

## <a name="credential-lookup"></a>Vyhledávání přihlašovacích údajů

Při autorizaci dotazů se vyhledávání přihlašovacích údajů používá pro přístup k účtu úložiště a je založené na následujících pravidlech:

- Uživatel je přihlášený jako přihlášení ke službě Azure AD.

  - Pokud existuje přihlašovací údaj UserIdentity a uživatel má na něm oprávnění s odkazem, použije se předávací služba Azure AD, jinak se vyhledá [přihlašovací údaje podle cesty](#lookup-credential-by-path) .

- Uživatel je přihlášený jako přihlašovací jméno SQL.

  - Použít [vyhledávací přihlašovací údaje podle cesty](#lookup-credential-by-path)

### <a name="lookup-credential-by-path"></a>Vyhledat přihlašovací údaje podle cesty

Pokud je vynucené předávání Azure AD zakázané, vyhledávání přihlašovacích údajů bude založené na cestě k úložišti (nejprve hloubka) a existenci oprávnění odkazů na konkrétní přihlašovací údaje. Pokud existuje více přihlašovacích údajů, které lze použít pro přístup ke stejnému souboru, bude SQL na vyžádání používat nejvíce konkrétního.  

Níže je příklad dotazu v následující cestě k souboru: *account.DFS.Core.Windows.NET/FileSystem/Folder1/.../folderN/fileX.ext*

Vyhledávání přihlašovacích údajů se dokončí v tomto pořadí:

```
account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX
account.dfs.core.windows.net/filesystem/folder1/.../folderN
account.dfs.core.windows.net/filesystem/folder1
account.dfs.core.windows.net/filesystem
account.dfs.core.windows.net
```

Pokud uživatel nemá žádné odkazy na přihlašovací údaje na čísle 5, SQL na vyžádání zkontroluje, jestli má uživatel odkaz na oprávnění u přihlašovacích údajů, která je o jednu úroveň výš, dokud nenalezne přihlašovací údaje, na kterých má uživatel oprávnění. Pokud takové oprávnění nenajde, vrátí se chybová zpráva.

### <a name="credential-and-path-level"></a>Úroveň přihlašovacích údajů a cest

V závislosti na požadovaném tvaru cesty jsou pro spouštění dotazů k disviset následující požadavky:

- Pokud je dotaz cílen na více souborů (složky se zástupnými kartami nebo bez nich), musí mít uživatel přístup k přihlašovacím údajům na úrovni kořenového adresáře (na úrovni kontejneru). Tato úroveň přístupu je potřeba, protože výpisy souborů jsou relativní od kořenového adresáře (Azure Storage omezení).
- Pokud je dotaz cílen na jeden soubor, musí mít uživatel přístup k přihlašovacím údajům na jakékoli úrovni, protože SQL na vyžádání přistupuje k souboru přímo, tedy bez výpisu složek.

|                  | *Účet* | *Kořenový adresář* | *Libovolný další adresář* | *Soubor*        |
| ---------------- | --------- | ---------------- | --------------------- | ------------- |
| *Jeden soubor*    | Podporuje se | Podporuje se        | Podporuje se             | Podporuje se     |
| *Více souborů* | Podporuje se | Podporuje se        | Nepodporuje se         | Nepodporuje se |

## <a name="next-steps"></a>Další kroky

Níže uvedené články vám pomůžou zjistit, jak zadávat dotazy na různé typy složek, typy souborů a vytváření a používání zobrazení:

- [Dotaz na jeden soubor CSV](query-single-csv-file.md)
- [Složky dotazů a více souborů CSV](query-folders-multiple-csv-files.md)
- [Dotazování konkrétních souborů](query-specific-files.md)
- [Dotazování souborů Parquet](query-parquet-files.md)
- [Vytváření a používání zobrazení](create-use-views.md)
- [Dotazování souborů JSON](query-json-files.md)
- [Dotazování vnořených typů Parquet](query-parquet-nested-types.md)
