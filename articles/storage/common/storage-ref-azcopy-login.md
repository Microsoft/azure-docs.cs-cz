---
title: přihlášení AzCopy | Microsoft Docs
description: Tento článek poskytuje referenční informace pro příkaz AzCopy Login.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: e4861749d66e466bc3302553af8b3ed4919a72e0
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503231"
---
# <a name="azcopy-login"></a>azcopy login

Přihlásí se k Azure Active Directory pro přístup k prostředkům Azure Storage.

## <a name="synopsis"></a>Stručný obsah

Přihlaste se k Azure Active Directory pro přístup k prostředkům Azure Storage.

Aby bylo možné účet Azure Storage, je nutné přiřadit roli **Přispěvatel dat objektů BLOB úložiště** k vašemu uživatelskému účtu v kontextu buď účtu úložiště, nadřazené skupiny prostředků nebo nadřazeného předplatného.

Tento příkaz uloží do mezipaměti šifrované přihlašovací informace pro aktuálního uživatele pomocí integrovaných mechanismů operačního systému.

> [!IMPORTANT]
> Pokud nastavíte proměnnou prostředí pomocí příkazového řádku, bude tato proměnná čitelná v historii příkazového řádku. Zvažte vymazání proměnných, které obsahují pověření z historie příkazového řádku. Chcete-li zabránit zobrazování proměnných ve vaší historii, můžete použít skript, který uživateli vyzve k zadání přihlašovacích údajů a k nastavení proměnné prostředí.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>Související koncepční články

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)
- [Kurz: migrace místních dat do cloudového úložiště pomocí AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Přenos dat pomocí AzCopy a BLOB Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Přenos dat s použitím AzCopy a úložiště souborů](storage-use-azcopy-files.md)

## <a name="examples"></a>Příklady

Přihlaste se interaktivně s výchozím ID tenanta AAD nastaveným na společné:

```azcopy
azcopy login
```

Přihlaste se interaktivně pomocí zadaného ID tenanta:

```azcopy
azcopy login --tenant-id "[TenantID]"
```

Přihlaste se pomocí identity určené systémem pro virtuální počítač (VM):

```azcopy
azcopy login --identity
```

Přihlaste se pomocí uživatelsky přiřazené identity virtuálního počítače a ID klienta identity služby:
  
```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```
 
Přihlaste se pomocí uživatelsky přiřazené identity virtuálního počítače a ID objektu identity služby:

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

Přihlaste se pomocí uživatelsky přiřazené identity virtuálního počítače a ID prostředku identity služby:
 
```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

Přihlaste se jako instanční objekt pomocí tajného klíče klienta: nastavte proměnnou prostředí AZCOPY_SPA_CLIENT_SECRET na tajný klíč klienta pro ověřování instančního objektu založeného na tajnosti.

```azcopy
azcopy login --service-principal --application-id <your service principal's application ID>
```

Přihlaste se jako instanční objekt pomocí certifikátu a jeho heslem:

Nastavte proměnnou prostředí AZCOPY_SPA_CERT_PASSWORD na heslo certifikátu pro ověřování instančního objektu na základě certifikátu:

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert --application-id <your service principal's application ID>
```

Považovat `/path/to/my/cert` za cestu k souboru PEM nebo PKCS12. AzCopy se nedostane do úložiště certifikátů systému, aby mohl získat váš certifikát.

`--certificate-path` je při ověřování instančního objektu na základě certifikátu povinné.

## <a name="options"></a>Možnosti

**--AAD-koncový bod** řetězec Azure Active Directory koncový bod, který se má použít. Výchozí hodnota ( https://login.microsoftonline.com) je správná pro globální cloud Azure. Nastavte tento parametr při ověřování v národním cloudu. Není nutné pro Identita spravované služby.

**--ID** aplikace řetězec aplikace ID identity přiřazené uživatelem. Vyžaduje se pro ověřování instančního objektu.

**--** cesta k řetězci cesty k certifikátu pro ověřování hlavního názvu služby (SPN). Vyžaduje se pro ověřování instančního objektu založeného na certifikátech.

**–**   nápovědu k `azcopy login` příkazu.

**--identity**   Přihlaste se pomocí identity virtuálního počítače, označovaného taky jako identita spravované služby (MSI).

**--identity-Client-ID** řetězec ID klienta identity přiřazené uživatelem.

**--identity-Object-** ID objektu řetězce identity přiřazené uživatelem.

**--identity-** ID prostředku řetězce ID identity přiřazené uživatelem.

**--Service-Principal**   Přihlaste se přes hlavní název služby (SPN) pomocí certifikátu nebo tajného klíče. Tajný klíč klienta nebo heslo certifikátu musí být umístěn v příslušné proměnné prostředí. Zadejte AzCopy ENV pro zobrazení názvů a popisů proměnných prostředí.

**--ID klienta –** řetězec Azure Active Directory ID tenanta, které se má použít pro interaktivní přihlášení zařízení OAuth.

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

|Možnost|Popis|
|---|---|
|--Cap – MB/s float|Velká rychlost přenosu v megabajtech za sekundu. Okamžitá propustnost se může mírně lišit od Cap. Pokud je tato možnost nastavená na hodnotu nula nebo je vynechána, propustnost nebude omezené.|
|--výstupní řetězec typu|Formát výstupu příkazu Mezi možnosti patří: text, JSON. Výchozí hodnota je "text".|
|--Trusted – řetězec Microsoft-přípony   |Určuje další přípony domén, kde se můžou odesílat přihlašovací tokeny Azure Active Directory.  Výchozí hodnota je *. Core.Windows.NET;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Zde uvedené jsou přidány do výchozího nastavení. Z důvodu zabezpečení byste měli sem umístit jenom Microsoft Azure domény. Více položek oddělte středníkem.|

## <a name="see-also"></a>Viz také

- [AzCopy](storage-ref-azcopy.md)
