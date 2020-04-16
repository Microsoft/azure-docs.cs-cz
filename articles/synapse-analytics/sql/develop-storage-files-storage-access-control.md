---
title: Řízení přístupu účtu úložiště pro SQL na vyžádání (preview)
description: Popisuje, jak SQL na vyžádání (preview) přistupuje k Azure Storage a jak můžete řídit přístup k úložišti pro SQL na vyžádání v Azure Synapse Analytics.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0d2683091898e9c84457b3b538776f0e6b0469d4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424031"
---
# <a name="control-storage-account-access-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Řízení přístupu k účtu úložiště pro SQL na vyžádání (preview) v Azure Synapse Analytics

Dotaz SQL na vyžádání (preview) čte soubory přímo z Azure Storage. Vzhledem k tomu, že účet úložiště je objekt, který je externí prostředek NA vyžádání, jsou vyžadována příslušná pověření. Uživatel potřebuje příslušná oprávnění udělená k použití požadovaných pověření. Tento článek popisuje typy přihlašovacích údajů, které můžete použít, a jak je uzákoněno vyhledávání pověření pro uživatele SQL a Azure AD.

## <a name="supported-storage-authorization-types"></a>Podporované typy autorizace úložiště

Uživatel, který se přihlásil k prostředku NA vyžádání SQL, musí mít oprávnění k přístupu k souborům ve službě Azure Storage a k jejich dotazování. Podporovány jsou tři typy autorizace:

- [Sdílený přístupový podpis](#shared-access-signature)
- [Spravovaná identita](#managed-identity)
- [Identita uživatele](#user-identity)

> [!NOTE]
> [Předávací řešení Azure AD](#force-azure-ad-pass-through) je výchozí chování při vytváření pracovního prostoru. Pokud ho používáte, nemusíte vytvářet přihlašovací údaje pro každý účet úložiště, ke které se přistupuje pomocí přihlášení služby AD. [Toto chování](#disable-forcing-azure-ad-pass-through)můžete zakázat .

V následující tabulce najdete různé typy autorizací, které jsou buď podporovány, nebo budou brzy podporovány.

| Typ autorizace                    | *Uživatel SQL*    | *Uživatel Azure AD*     |
| ------------------------------------- | ------------- | -----------    |
| [Sas](#shared-access-signature)       | Podporuje se     | Podporuje se      |
| [Spravovaná identita](#managed-identity) | Nepodporuje se | Nepodporuje se  |
| [Identita uživatele](#user-identity)       | Nepodporuje se | Podporuje se      |

### <a name="shared-access-signature"></a>Sdílený přístupový podpis

**Sdílený přístupový podpis (SAS)** poskytuje delegovaný přístup k prostředkům v účtu úložiště. S SAS může zákazník udělit klientům přístup k prostředkům v účtu úložiště bez sdílení klíčů účtu. SAS poskytuje podrobnou kontrolu nad typem přístupu, který udělujete klientům, kteří mají Interval platnosti, včetně intervalu platnosti, udělených oprávnění, rozsahu přijatelných IP adres a přijatelného protokolu (https/http).

Token SAS můžete získat tak, že přejdete na **portál Azure -> účet úložiště -> podpis u sdíleného přístupu -> Konfigurovat oprávnění -> generovat SAS a připojovací řetězec.**

> [!IMPORTANT]
> Při generování tokenu SAS obsahuje otazník ('?') na začátku tokenu. Chcete-li použít token v SQL na vyžádání, musíte odebrat otazník ('?') při vytváření pověření. Příklad:
>
> SAS token: ?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2 019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSmANd0ef9BrIPBNJ3VYEIq78%3D

### <a name="user-identity"></a>Identita uživatele

**Identita uživatele**, označovaná také jako "předávací", je typ autorizace, kde se identita uživatele Azure AD, který se přihlásil k SQL na vyžádání, používá k autorizaci přístupu k datům. Před přístupem k datům musí správce Azure Storage udělit oprávnění uživateli Azure AD. Jak je uvedeno v tabulce výše, není podporovánpro typ uživatele SQL.

> [!NOTE]
> Pokud používáte [předávací zařízení Azure AD,](#force-azure-ad-pass-through) nemusíte vytvářet přihlašovací údaje pro každý účet úložiště, ke kterým se přistupuje pomocí přihlášení služby AD.

> [!IMPORTANT]
> Chcete-li k přístupu k datům použít svou identitu, musíte mít roli Vlastník/Přispěvatel/Čtenář objektu blob úložiště.
> I v případě, že jste vlastníkem účtu úložiště, stále musíte přidat sami do jedné z rolí dat objektů blob úložiště.
>
> Další informace o řízení přístupu v Azure Data Lake Store Gen2, přečtěte si řízení přístupu v článku [Azure Data Lake Storage Gen2.](../../storage/blobs/data-lake-storage-access-control.md)
>

### <a name="managed-identity"></a>Spravovaná identita

**Spravovaná identita** je také známá jako MSI. Je to funkce Azure Active Directory (Azure AD), která poskytuje služby Azure pro SQL na vyžádání. Také nasadí automaticky spravované identity ve službě Azure AD. Tuto identitu můžete použít k autorizaci požadavku na přístup k datům ve službě Azure Storage.

Před přístupem k datům musí správce Azure Storage udělit oprávnění spravované identitě pro přístup k datům. Udělení oprávnění spravované identitě se provádí stejným způsobem jako udělení oprávnění jakémukoli jinému uživateli Azure AD.

## <a name="create-credentials"></a>Vytvoření přihlašovacích údajů

Chcete-li dotaz ovat soubor umístěný ve službě Azure Storage, koncový bod SQL na vyžádání potřebuje pověření na úrovni serveru, které obsahuje ověřovací informace. Pověření je přidáno spuštěním [create credential](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Budete muset zadat argument NÁZEV POVĚŘENÍ. Musí odpovídat buď část cesty nebo celou cestu k datům v úložišti (viz níže).

> [!NOTE]
> Argument FOR CRYPTOGRAPHIC PROVIDER není podporován.

U všech podporovaných typů autorizací mohou pověření překážet na účet, kontejner, libovolný adresář (nekořenový) nebo jeden soubor.

Název pověření musí odpovídat úplné cestě ke kontejneru, složce nebo souboru v následujícím formátu:`<prefix>://<storage_account_path>/<storage_path>`

| Externí zdroj dat       | Předpona | Cesta k účtu úložiště                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | HTTPS  | <storage_account>.blob.core.windows.net             |
| Azure Data Lake Storage Gen1 | HTTPS  | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | HTTPS  | <storage_account>.dfs.core.windows.net              |

 "<storage_path>" je cesta v úložišti, která odkazuje na složku nebo soubor, který chcete číst.

> [!NOTE]
> Existuje speciální název `UserIdentity` pověření, který [vynutí předávací překlad azure ad](#force-azure-ad-pass-through). Přečtěte si prosím o vlivu, který má na [vyhledávání pověření](#credential-lookup) při provádění dotazů.

Volitelně chcete-li uživateli povolit vytvoření nebo přetažení pověření, může správce udělit nebo odepřít oprávnění ZMĚNIT všechna pověření uživateli:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

### <a name="supported-storages-and-authorization-types"></a>Podporovaná úložiště a typy autorizací

Můžete použít následující kombinace autorizace a azure storage typy:

|                     | Blob Storage   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| *Sas*               | Podporuje se      | Není podporováno   | Podporuje se     |
| *Spravovaná identita* | Nepodporuje se  | Nepodporuje se    | Nepodporuje se |
| *Identita uživatele*    | Podporuje se      | Podporuje se        | Podporuje se     |

### <a name="examples"></a>Příklady

V závislosti na [typu autorizace](#supported-storage-authorization-types)můžete vytvořit pověření pomocí níže uvedené syntaxe T-SQL.

**Sdílený přístupový podpis a úložiště objektů blob**

Exchange <*mystorageaccountname*> s názvem skutečného účtu úložiště a <*mystorageaccountcontainername*> s názvem skutečného kontejneru:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

**Identita uživatele a úložiště datových jezer Azure Gen1**

Exchange <*mystorageaccountname*> s názvem skutečného účtu úložiště a <*mystorageaccountcontainername*> s názvem skutečného kontejneru:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.azuredatalakestore.net/webhdfs/v1/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

**Identita uživatele a úložiště datových jezer Azure Gen2**

Exchange <*mystorageaccountname*> s názvem skutečného účtu úložiště a <*mystorageaccountcontainername*> s názvem skutečného kontejneru:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.dfs.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

## <a name="force-azure-ad-pass-through"></a>Vynucení průchodu Azure AD

Vynucení předávací ho řešení Azure AD je `UserIdentity`výchozí chování dosažené speciálním názvem pověření , který se vytvoří automaticky během zřizování pracovního prostoru Azure Synapse. Vynutí využití předávací ho azure ad pro každý dotaz každého přihlášení Azure AD, ke kterému dojde i přes existenci jiných pověření.

> [!NOTE]
> Předávací nastavení Azure AD je výchozí chování. Nemusíte vytvářet přihlašovací údaje pro každý účet úložiště, ke kterým přistupuje přihlášení služby AD.

V případě, že jste [zakázali vynucení předávacího řízení služby Azure AD pro každý dotaz](#disable-forcing-azure-ad-pass-through)a chcete jej znovu povolit, spusťte:

```sql
CREATE CREDENTIAL [UserIdentity]
WITH IDENTITY = 'User Identity';
```

Chcete-li povolit vynucení předávací ho azure ad `UserIdentity` pro konkrétního uživatele, můžete udělit oprávnění REFERENCE na pověření pro konkrétního uživatele. Následující příklad umožňuje vynucení předávací zařízení Azure AD pro user_name:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

Další informace o tom, jak sql na vyžádání najde pověření použít, naleznete v [tématu vyhledávání pověření](#credential-lookup).

## <a name="disable-forcing-azure-ad-pass-through"></a>Zakázat vynucení předávacího průchodu služby Azure AD

Můžete zakázat [vynucení předávací služby Azure AD pro každý dotaz](#force-azure-ad-pass-through). Chcete-li jej `Userdentity` zakázat, přetáhněte pověření pomocí:

```sql
DROP CREDENTIAL [UserIdentity];
```

Pokud chcete znovu povolit, najdete v platnost [Azure AD předávací](#force-azure-ad-pass-through) části.

Chcete-li zakázat vynucení předávací ho služby Azure `UserIdentity` AD pro konkrétního uživatele, můžete odepřít oprávnění REFERENCE na přihlašovací údaje pro konkrétního uživatele. Následující příklad zakáže vynucení předávací služby Azure AD pro user_name:

```sql
DENY REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

Další informace o tom, jak sql na vyžádání vyhledá pověření použít, naleznete v [tématu vyhledávání pověření](#credential-lookup).

## <a name="grant-permissions-to-use-credential"></a>Udělení oprávnění k použití pověření

Chcete-li použít pověření, musí mít uživatel oprávnění REFERENCES na konkrétní pověření. Chcete-li udělit storage_credential odkazy na specific_user, proveďte:

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

Chcete-li zajistit hladké předávací prostředí Azure AD, všichni uživatelé budou mít ve výchozím nastavení právo používat `UserIdentity` přihlašovací údaje. Toho je dosaženo automatickým spuštěním následujícího příkazu při zřizování pracovního prostoru Azure Synapse:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO [public];
```

## <a name="credential-lookup"></a>Vyhledávání pověření

Při autorizaci dotazů se vyhledávání přihlašovacích údajů používá pro přístup k účtu úložiště a je založeno na následujících pravidlech:

- Uživatel je přihlášen jako přihlášení azure ad

  - Pokud existuje pověření UserIdentity a uživatel má referenční oprávnění na to, bude použit předávací azure ad, jinak [vyhledávání pověření podle cesty](#lookup-credential-by-path)

- Uživatel je přihlášen jako přihlášení SQL

  - Použití [vyhledávacího pověření podle cesty](#lookup-credential-by-path)

### <a name="lookup-credential-by-path"></a>Vyhledávací pověření podle cesty

Pokud vynucení předávací ho služby Azure AD je zakázáno, vyhledávání pověření bude založena na cestě úložiště (hloubka jako první) a existence oprávnění REFERENCES na konkrétní pověření. Pokud existuje více pověření, které lze použít pro přístup ke stejnému souboru, SQL na vyžádání bude používat nejkonkrétnější jeden.  

Níže je uveden příklad dotazu na následující cestě k souboru: *account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX.ext*

Vyhledávání pověření bude dokončeno v tomto pořadí:

```
account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX
account.dfs.core.windows.net/filesystem/folder1/.../folderN
account.dfs.core.windows.net/filesystem/folder1
account.dfs.core.windows.net/filesystem
account.dfs.core.windows.net
```

Pokud uživatel nemá oprávnění REFERENCES na pověření číslo 5, SQL na vyžádání zkontroluje, zda má oprávnění REFERENCES na pověření, které je o jednu úroveň vyšší, dokud nenajde pověření, na které má uživatel oprávnění REFERENCES. Pokud není nalezeno žádné takové oprávnění, bude vrácena chybová zpráva.

### <a name="credential-and-path-level"></a>Úroveň pověření a cesty

V závislosti na požadovaném obrazci cesty jsou pro spouštění dotazů zavedeny následující požadavky:

- Pokud dotaz cílí na více souborů (složky, s zástupnými znaky nebo bez ní), musí mít uživatel přístup k pověření alespoň na úrovni kořenového adresáře (úroveň kontejneru). Tato úroveň přístupu je potřebná, protože výpis souborů je relativní z kořenového adresáře (omezení úložiště Azure)
- Pokud dotaz cílí na jeden soubor, uživatel musí mít přístup k pověření na libovolné úrovni jako SQL on-demand přistupuje k souboru přímo, to znamená bez výpisu složek.

|                  | *Účet* | *Kořenový adresář* | *Jakýkoli jiný adresář* | *Soubor*        |
| ---------------- | --------- | ---------------- | --------------------- | ------------- |
| *Jeden soubor*    | Podporuje se | Podporuje se        | Podporuje se             | Podporuje se     |
| *Více souborů* | Podporuje se | Podporuje se        | Nepodporuje se         | Nepodporuje se |

## <a name="next-steps"></a>Další kroky

Níže uvedené články vám pomohou zjistit, jak se dotazovat různých typů složek, typů souborů a vytváření a používání zobrazení:

- [Dotaz na jeden soubor CSV](query-single-csv-file.md)
- [Složky dotazů a více souborů CSV](query-folders-multiple-csv-files.md)
- [Soubory specifické pro dotaz](query-specific-files.md)
- [Soubory parket dotazu](query-parquet-files.md)
- [Vytváření a používání zobrazení](create-use-views.md)
- [Dotaz na soubory JSON](query-json-files.md)
- [Typy vnořených par dotazů](query-parquet-nested-types.md)
