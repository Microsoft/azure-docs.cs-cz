---
title: azkopie přihlášení | Dokumenty společnosti Microsoft
description: Tento článek obsahuje referenční informace pro příkaz azcopy přihlášení.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d07d1a706635a7f269a9a51769ae6f8bbf57df3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295408"
---
# <a name="azcopy-login"></a>azcopy login

Přihlašuje se do Služby Azure Active Directory pro přístup k prostředkům azure storage.

## <a name="synopsis"></a>Synopse

Přihlaste se ke službě Azure Active Directory a získejte přístup k prostředkům úložiště Azure.

Chcete-li být autorizováni k účtu služby Azure Storage, musíte přiřadit roli **přispěvatele dat objektů blob úložiště** k vašemu uživatelskému účtu v kontextu účtu úložiště, nadřazené skupiny prostředků nebo nadřazeného předplatného.

Tento příkaz uloží do mezipaměti zašifrované přihlašovací údaje pro aktuálního uživatele pomocí integrovaných mechanismů operačního systému.

Další informace naleznete v příkladech.

> [!IMPORTANT]
> Pokud nastavíte proměnnou prostředí pomocí příkazového řádku, bude tato proměnná čitelná v historii příkazového řádku. Zvažte vymazání proměnných, které obsahují pověření z historie příkazového řádku. Chcete-li zabránit zobrazení proměnných v historii, můžete pomocí skriptu vyzvat uživatele k zadání pověření a nastavit proměnnou prostředí.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>Související koncepční články

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)
- [Přenos dat pomocí úložiště AzCopy a Blob](storage-use-azcopy-blobs.md)
- [Přenos dat pomocí AzCopy a ukládání souborů](storage-use-azcopy-files.md)
- [Konfigurace, optimalizace a řešení potíží s azcopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Příklady

Přihlaste se interaktivně s výchozím ID klienta AAD nastaveným na běžné:

```azcopy
azcopy login
```

Přihlaste se interaktivně pomocí zadaného ID klienta:

```azcopy
azcopy login --tenant-id "[TenantID]"
```

Přihlaste se pomocí systémem přiřazené identity virtuálního počítače (VM):

```azcopy
azcopy login --identity
```

Přihlaste se pomocí uživatelem přiřazené identity virtuálního uživatele a ID klienta identity služby:

```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```

Přihlaste se pomocí uživatelem přiřazené identity virtuálního uživatele a ID objektu identity služby:

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

Přihlaste se pomocí uživatelem přiřazené identity virtuálního uživatele a ID prostředku identity služby:

```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

Přihlaste se jako instanční objekt pomocí tajného klíče klienta. Nastavte proměnnou prostředí AZCOPY_SPA_CLIENT_SECRET na tajný klíč klienta pro zabezpečení na základě zabezpečení instančního objektu.

```azcopy
azcopy login --service-principal
```

Přihlaste se jako instanční objekt pomocí certifikátu a hesla. Nastavte proměnnou prostředí AZCOPY_SPA_CERT_PASSWORD na heslo certifikátu pro autorizaci instančního objektu založeného na certifikátu.

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert
```

Ujistěte se, že považovat /path/to/my/cert jako cestu k souboru PEM nebo PKCS12. AzCopy nedosahuje do úložiště certifikátů systému získat certifikát.

--cesta k certifikátu je povinná při ověřování služby na základě certifikátu.

## <a name="options"></a>Možnosti

|Možnost|Popis|
|--|--|
|--aad-endpoint|Koncový bod služby Azure Active Directory, který se má použít. Výchozí hodnota`https://login.microsoftonline.com`( ) je správná pro veřejný cloud Azure. Nastavte tento parametr při ověřování v národním cloudu. Viz [koncové body ověřování Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).
Tento příznak není potřeba pro identitu spravované služby.|
|--řetězec id aplikace|ID aplikace uživatelem přiřazené identity. Vyžadováno pro ustanční objekt.|
|--řetězec cesty k certifikátu|Cesta k certifikátu pro ověřování spn. Vyžadováno pro ověřování služby založené na certifikátu.|
|-h, --pomoc|Zobrazit obsah nápovědy pro příkaz přihlášení.|
|--identita|přihlaste se pomocí identity virtuálního počítače, označované také jako identita spravované služby (MSI).|
|--identity-client-id řetězec|ID klienta uživatelem přiřazené identity.|
|--identity-object-id string --identity-object-id string --identity-object-id string --identit|ID objektu uživatelem přiřazené identity.|
|--identity-resource-id řetězec|ID prostředku uživatelem přiřazené identity.|
|--service-in-in|Přihlaste se pomocí hlavního názvu služby (název služby) pomocí certifikátu nebo tajného klíče. Heslo klientského tajného klíče nebo certifikátu musí být umístěno do příslušné proměnné prostředí. Chcete-li zobrazit názvy a popisy proměnných prostředí, zadejte: `AzCopy env`|
|--tenant-id řetězec| ID klienta služby Azure active directory, které se má použít pro interaktivní přihlášení zařízení OAuth.|

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

|Možnost|Popis|
|---|---|
|--cap-mbps uint32|Završuje přenosovou rychlost v megabitech za sekundu. Propustnost se může mírně lišit od víčka. Pokud je tato možnost nastavena na nulu nebo je vynechána, propustnost není omezena.|
|--řetězec výstupního typu|Formát výstupu příkazu. Volby zahrnují: text, json. Výchozí hodnota je "text".|

## <a name="see-also"></a>Viz také

- [azkopie](storage-ref-azcopy.md)
