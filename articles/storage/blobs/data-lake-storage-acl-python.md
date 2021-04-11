---
title: Použití Pythonu ke správě seznamů ACL v Azure Data Lake Storage Gen2
description: Použijte Python spravovat seznamy řízení přístupu (ACL) v účtech úložiště, které mají povolený hierarchický obor názvů (HNS).
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: ba864aa1aa2462f21e05ab5e779c8e715d6bb973
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100654090"
---
# <a name="use-python-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Použití Pythonu ke správě seznamů ACL v Azure Data Lake Storage Gen2

V tomto článku se dozvíte, jak používat Python k získání, nastavení a aktualizaci seznamů řízení přístupu adresářů a souborů. 

Dědičnost seznamů ACL je již k dispozici pro nové podřízené položky, které jsou vytvořeny v nadřazeném adresáři. Můžete ale také přidat, aktualizovat a odebrat seznamy ACL pro existující podřízené položky nadřazeného adresáře, aniž by bylo nutné provádět tyto změny jednotlivě pro každou podřízenou položku. 

[Balíček (index balíčku Pythonu)](https://pypi.org/project/azure-storage-file-datalake/)  |  [Ukázky](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)  |  [Rekurzivní ukázky](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)  |  seznamů ACL Reference k rozhraní [API](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)  |  Mapování Gen1 na [Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Sdělte nám svůj názor](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

- Účet úložiště, který má povolený hierarchický obor názvů (HNS). Pokud ho chcete vytvořit, postupujte podle [těchto](create-data-lake-storage-account.md) pokynů.

- Verze Azure CLI `2.6.0` nebo vyšší

- Jedno z následujících oprávnění zabezpečení:

  - Zřízeným [objektem zabezpečení](../../role-based-access-control/overview.md#security-principal) služby Azure Active Directory (AD), kterému byla přiřazena role [vlastníka dat objektů BLOB úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) v oboru cílového kontejneru, nadřazené skupiny prostředků nebo předplatného.  

  - Vlastnící uživatel cílového kontejneru nebo adresáře, pro který plánujete použít nastavení seznamu ACL. Chcete-li nastavit seznamy ACL rekurzivně, zahrnuje všechny podřízené položky v cílovém kontejneru nebo adresáři.
  
  - Klíč účtu úložiště

## <a name="set-up-your-project"></a>Nastavení projektu

Nainstalujte klientskou knihovnu Azure Data Lake Storage pro Python pomocí [PIP](https://pypi.org/project/pip/).

```
pip install azure-storage-file-datalake
```

Přidejte tyto příkazy pro import do horní části souboru kódu.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>Připojit k účtu

Pokud chcete používat fragmenty kódu v tomto článku, budete muset vytvořit instanci **DataLakeServiceClient** , která představuje účet úložiště. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Připojení pomocí Azure Active Directory (AD)

> [!NOTE]
> Pokud k autorizaci přístupu používáte Azure Active Directory (Azure AD), ujistěte se, že je vašemu objektu zabezpečení přiřazená [role vlastníka dat objektu BLOB úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Další informace o tom, jak se používají oprávnění seznamu řízení přístupu a důsledky jejich změny, najdete  [v tématu model řízení přístupu v Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md).

K ověření vaší aplikace v Azure AD můžete použít [klientskou knihovnu Azure identity pro Python](https://pypi.org/project/azure-identity/) .

Získejte ID klienta, tajný klíč klienta a ID tenanta. Pokud to chcete provést, přečtěte si téma [získání tokenu z Azure AD pro autorizaci žádostí z klientské aplikace](../common/storage-auth-aad-app.md). V rámci tohoto procesu budete muset k objektu zabezpečení přiřadit jednu z následujících rolí [řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/overview.md) . 

|Role|Funkce nastavení seznamu ACL|
|--|--|
|[Vlastník dat v objektech blob služby Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Všechny adresáře a soubory v účtu.|
|[Přispěvatel dat v objektech blob služby Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Pouze adresáře a soubory vlastněné objektem zabezpečení.|

Tento příklad vytvoří instanci **DataLakeServiceClient** pomocí ID klienta, tajného klíče klienta a ID tenanta.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Další příklady najdete v dokumentaci ke [klientské knihovně Azure identity pro Python](https://pypi.org/project/azure-identity/) .

### <a name="connect-by-using-an-account-key"></a>Připojení pomocí klíče účtu

Toto je nejjednodušší způsob, jak se připojit k účtu.

Tento příklad vytvoří instanci **DataLakeServiceClient** pomocí klíče účtu.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::
 
- Nahraďte `storage_account_name` hodnotu zástupného symbolu názvem vašeho účtu úložiště.

- Nahraďte `storage_account_key` hodnotu zástupného symbolu vaším klíčem pro přístup k účtu úložiště.

## <a name="set-acls"></a>Nastavení seznamů ACL

Když *nastavíte* seznam řízení přístupu (ACL), **NAHRADÍTE** celý seznam ACL včetně všech jeho položek. Pokud chcete změnit úroveň oprávnění objektu zabezpečení nebo přidat nový objekt zabezpečení do seznamu ACL, aniž by to ovlivnilo jiné existující položky, měli byste místo toho *aktualizovat* seznam ACL. Chcete-li aktualizovat seznam řízení přístupu (ACL) místo jeho nahrazení, přečtěte si část [aktualizace seznamů ACL](#update-acls-recursively) tohoto článku.  

V této části se dozvíte, jak:

- Nastavení seznamu ACL adresáře
- Nastavení seznamu ACL souboru

### <a name="set-the-acl-of-a-directory"></a>Nastavení seznamu ACL adresáře

Získejte seznam řízení přístupu (ACL) adresáře voláním metody **DataLakeDirectoryClient.get_access_control** a nastavte seznam řízení přístupu voláním metody **DataLakeDirectoryClient.set_access_control** .

Tento příklad načte a nastaví seznam řízení přístupu k adresáři s názvem `my-directory` . Řetězec přiřadí `rwxr-xrw-` vlastnícímu uživateli oprávnění ke čtení, zápisu a spouštění, dává vlastnící skupině pouze oprávnění číst a spouštět a poskytuje všem ostatním oprávnění ke čtení a zápisu.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ACLDirectory":::

Můžete také získat a nastavit seznam ACL kořenového adresáře kontejneru. Chcete-li získat kořenový adresář, zavolejte metodu **FileSystemClient._get_root_directory_client** .

### <a name="set-the-acl-of-a-file"></a>Nastavení seznamu ACL souboru

Získání seznamu řízení přístupu (ACL) souboru voláním metody **DataLakeFileClient.get_access_control** a nastavením seznamu ACL voláním metody **DataLakeFileClient.set_access_control** .

Tento příklad získá a nastaví seznam řízení přístupu k souboru s názvem `my-file.txt` . Řetězec přiřadí `rwxr-xrw-` vlastnícímu uživateli oprávnění ke čtení, zápisu a spouštění, dává vlastnící skupině pouze oprávnění číst a spouštět a poskytuje všem ostatním oprávnění ke čtení a zápisu.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_FileACL":::

## <a name="set-acls-recursively"></a>Rekurzivní nastavení seznamů ACL

Když *nastavíte* seznam řízení přístupu (ACL), **NAHRADÍTE** celý seznam ACL včetně všech jeho položek. Pokud chcete změnit úroveň oprávnění objektu zabezpečení nebo přidat nový objekt zabezpečení do seznamu ACL, aniž by to ovlivnilo jiné existující položky, měli byste místo toho *aktualizovat* seznam ACL. Chcete-li aktualizovat seznam řízení přístupu (ACL) místo jeho nahrazení, přečtěte si část [aktualizace seznamů ACL](#update-acls-recursively) v tomto článku jako rekurzivní.

Nastavení seznamů ACL rekurzivně voláním metody **DataLakeDirectoryClient.set_access_control_recursive** .

Pokud chcete nastavit **výchozí** položku seznamu řízení přístupu (ACL), přidejte řetězec `default:` na začátek každého řetězce položky seznamu ACL.

Tento příklad nastaví seznam řízení přístupu k adresáři s názvem `my-parent-directory` .

Tato metoda přijímá logický parametr s názvem `is_default_scope` , který určuje, zda se má nastavit výchozí seznam řízení přístupu. Pokud je tento parametr `True` , seznam položek seznamu ACL předchází řetězci `default:` .

Položky seznamu řízení přístupu poskytují oprávnění ke čtení, zápisu a spouštění vlastnícího uživatele, přidělí skupině jenom oprávnění ke čtení a spouštění a všem ostatním uživatelům se neudělí žádný přístup. Poslední položka seznamu ACL v tomto příkladu poskytuje konkrétního uživatele s ID objektu "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" oprávnění číst a spustit. Tyto záznamy poskytují oprávnění ke čtení, zápisu a spouštění vlastnícího uživatele, přidělí skupině pouze oprávnění ke čtení a spouštění a všem ostatním uživatelům nezíská přístup. Poslední položka seznamu ACL v tomto příkladu poskytuje konkrétního uživatele s ID objektu "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" oprávnění číst a spustit.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_SetACLRecursively":::

Příklad, který zpracovává seznam ACL rekurzivně v dávkách zadáním velikosti dávky, najdete v [ukázce](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)Pythonu.

## <a name="update-acls-recursively"></a>Rekurzivní aktualizace seznamů ACL

Když *aktualizujete* seznam řízení přístupu (ACL), místo nahrazení seznamu ACL ho upravte. Do seznamu ACL můžete například přidat nový objekt zabezpečení, aniž by to ovlivnilo jiné objekty zabezpečení uvedené v seznamu ACL.  Chcete-li nahradit seznam ACL místo aktualizace, přečtěte si část [set ACL](#set-acls) v tomto článku.

Chcete-li seznam ACL aktualizovat rekurzivně, vytvořte nový objekt ACL s položkou seznamu ACL, kterou chcete aktualizovat, a pak tento objekt použijte v operaci aktualizovat seznam ACL. Nezískejte existující seznam ACL, stačí poskytnout položky seznamu ACL, které se mají aktualizovat. Aktualizujte seznam ACL rekurzivně voláním metody **DataLakeDirectoryClient.update_access_control_recursive** . Pokud chcete aktualizovat **výchozí** položku seznamu řízení přístupu (ACL), přidejte řetězec `default:` na začátek každého řetězce položky seznamu ACL.

Tento příklad aktualizuje položku seznamu řízení přístupu (ACL) s oprávněním k zápisu.

Tento příklad nastaví seznam řízení přístupu k adresáři s názvem `my-parent-directory` . Tato metoda přijímá logický parametr s názvem `is_default_scope` , který určuje, zda se má aktualizovat výchozí seznam ACL. Pokud je tento parametr `True` , aktualizovaná položka seznamu ACL předá řetězec `default:` .  

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_UpdateACLsRecursively":::

Příklad, který zpracovává seznam ACL rekurzivně v dávkách zadáním velikosti dávky, najdete v [ukázce](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)Pythonu.

## <a name="remove-acl-entries-recursively"></a>Rekurzivní odebrání položek seznamu ACL

Můžete odebrat jednu nebo více položek seznamu řízení přístupu. Chcete-li odebrat položky seznamu ACL rekurzivně, vytvořte nový objekt ACL pro položku seznamu ACL, která se má odebrat, a pak tento objekt použijte v operaci odebrat seznam ACL. Nezískávat existující seznam ACL, stačí zadat položky seznamu ACL, které se mají odebrat. 

Odeberte položky seznamu ACL voláním metody **DataLakeDirectoryClient.remove_access_control_recursive** . Pokud chcete odebrat **výchozí** položku seznamu řízení přístupu (ACL), přidejte řetězec `default:` na začátek řetězce položky seznamu řízení přístupu. 

Tento příklad odebere položku seznamu řízení přístupu z seznamu řízení přístupu (ACL) adresáře s názvem `my-parent-directory` . Tato metoda přijímá logický parametr s názvem `is_default_scope` , který určuje, zda má být položka odebrána z výchozího seznamu ACL. Pokud je tento parametr `True` , aktualizovaná položka seznamu ACL předá řetězec `default:` . 

```python
def remove_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

Příklad, který zpracovává seznam ACL rekurzivně v dávkách zadáním velikosti dávky, najdete v [ukázce](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)Pythonu.

## <a name="recover-from-failures"></a>Obnovit po selhání

Může dojít k chybám za běhu nebo oprávnění. V případě chyb běhového prostředí restartujte proces od začátku. K chybám oprávnění může dojít v případě, že objekt zabezpečení nemá dostatečná oprávnění pro úpravu seznamu ACL adresáře nebo souboru, který je v upravované hierarchii adresáře. Vyřešte problém s oprávněním a pak zvolte, že chcete proces obnovit z bodu selhání pomocí tokenu pro pokračování, nebo spusťte proces znovu od začátku. Nemusíte používat token pro pokračování, pokud dáváte přednost restartování od začátku. Položky ACL můžete znovu použít bez jakýchkoli negativních dopadů.

Tento příklad vrátí token pro pokračování v případě selhání. Aplikace může tuto ukázkovou metodu zavolat znovu poté, co byla tato chyba adresována, a předejte token pro pokračování. Pokud je tato ukázková metoda volána poprvé, může aplikace předat hodnotu ``None`` parametru tokenu pokračování.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ResumeContinuationToken":::

Příklad, který zpracovává seznam ACL rekurzivně v dávkách zadáním velikosti dávky, najdete v [ukázce](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)Pythonu.

Pokud chcete, aby byl proces dokončen bez přerušení pomocí chyb oprávnění, můžete zadat.

Chcete-li zajistit, že proces bude dokončen bez přerušení, nepředejte token pro pokračování do metody **DataLakeDirectoryClient.set_access_control_recursive** .

Tento příklad nastavuje rekurzivní položky seznamů ACL. Pokud tento kód narazí na chybu oprávnění, zaznamená toto selhání a pokračuje v provádění. Tento příklad vytiskne počet selhání konzoly.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ContinueOnFailure":::

Příklad, který zpracovává seznam ACL rekurzivně v dávkách zadáním velikosti dávky, najdete v [ukázce](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)Pythonu.

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Viz také

- [Referenční dokumentace k rozhraní API](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
- [Balíček (index balíčku Pythonu)](https://pypi.org/project/azure-storage-file-datalake/)
- [ukázky](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
- [Mapování Gen1 na Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Známé problémy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Sdělte nám svůj názor](https://github.com/Azure/azure-sdk-for-python/issues)
- [Model řízení přístupu v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Seznamy řízení přístupu (ACL) v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)