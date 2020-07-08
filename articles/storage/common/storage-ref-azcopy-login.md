---
title: přihlášení AzCopy | Microsoft Docs
description: Tento článek poskytuje referenční informace pro příkaz AzCopy Login.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8d2adca661882ea11d04ebe55afe25f7f9c2ef4e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84219964"
---
# <a name="azcopy-login"></a>azcopy login

Přihlásí se k Azure Active Directory pro přístup k prostředkům Azure Storage.

## <a name="synopsis"></a>Stručný obsah

Přihlaste se k Azure Active Directory pro přístup k prostředkům Azure Storage.

Aby bylo možné účet Azure Storage, je nutné přiřadit roli **Přispěvatel dat objektů BLOB úložiště** k vašemu uživatelskému účtu v kontextu účtu úložiště, nadřazené skupiny prostředků nebo nadřazeného předplatného.

Tento příkaz uloží do mezipaměti šifrované přihlašovací informace pro aktuálního uživatele pomocí integrovaných mechanismů operačního systému.

Další informace najdete v příkladech.

> [!IMPORTANT]
> Pokud nastavíte proměnnou prostředí pomocí příkazového řádku, bude tato proměnná čitelná v historii příkazového řádku. Zvažte vymazání proměnných, které obsahují pověření z historie příkazového řádku. Chcete-li zabránit zobrazování proměnných ve vaší historii, můžete použít skript, který uživateli vyzve k zadání přihlašovacích údajů a k nastavení proměnné prostředí.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>Související koncepční články

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)
- [Přenos dat pomocí AzCopy a BLOB Storage](storage-use-azcopy-blobs.md)
- [Přenos dat pomocí AzCopy a úložiště souborů](storage-use-azcopy-files.md)
- [Konfigurace, optimalizace a řešení potíží s AzCopy](storage-use-azcopy-configure.md)

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

Přihlaste se jako instanční objekt pomocí tajného klíče klienta. Nastavte proměnnou prostředí AZCOPY_SPA_CLIENT_SECRET na tajný klíč klienta pro ověřování instančního objektu založeného na tajnosti.

```azcopy
azcopy login --service-principal
```

Přihlaste se jako instanční objekt pomocí certifikátu a hesla. Nastavte proměnnou prostředí AZCOPY_SPA_CERT_PASSWORD na heslo certifikátu pro autorizaci objektu služby na základě certifikátu.

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert
```

Zajistěte, aby se/path/to/my/CERT považovala za cestu k souboru PEM nebo PKCS12. AzCopy se nedostane do úložiště certifikátů systému, aby mohl získat váš certifikát.

--certifikát-cesta je při ověřování instančního objektu na základě certifikátu povinná.

## <a name="options"></a>Možnosti

|Možnost|Description|
|--|--|
|--AAD-koncový bod|Koncový bod Azure Active Directory, který se má použít. Výchozí hodnota ( `https://login.microsoftonline.com` ) je správná pro veřejný cloud Azure. Nastavte tento parametr při ověřování v národním cloudu. Viz [koncové body ověřování Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).
Tento příznak není pro Identita spravované služby potřebný.|
|--ID aplikace – řetězec|ID aplikace identity přiřazené uživatelem Vyžaduje se pro ověřování instančního objektu.|
|--Certificate-cesta – řetězec|Cesta k certifikátu pro ověřování hlavního názvu služby (SPN) Vyžaduje se pro ověřování instančního objektu založeného na certifikátech.|
|-h,--help|Zobrazit obsah nápovědu pro příkaz pro přihlášení|
|--identity|Přihlaste se pomocí identity virtuálního počítače, označovaného taky jako identita spravované služby (MSI).|
|--identity-Client-ID řetězec|ID klienta identity přiřazené uživatelem.|
|--identity-Object-ID řetězec|ID objektu identity přiřazené uživatelem.|
|--identity-Resource-ID řetězec|ID prostředku identity přiřazené uživatelem.|
|--Service-Principal|Přihlaste se přes hlavní název služby (SPN) pomocí certifikátu nebo tajného klíče. Tajný klíč klienta nebo heslo certifikátu musí být umístěn v příslušné proměnné prostředí. Zadáním `AzCopy env` zobrazíte názvy a popisy proměnných prostředí.|
|--tenant-ID řetězec| ID tenanta Azure Active Directory, které se má použít pro interaktivní přihlášení zařízení OAuth.|

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

|Možnost|Description|
|---|---|
|--Cap – Mbps|Velká rychlost přenosu v megabajtech za sekundu. Okamžitá propustnost se může mírně lišit od Cap. Pokud je tato možnost nastavená na hodnotu nula nebo je vynechána, propustnost nebude omezené.|
|--výstupní řetězec typu|Formát výstupu příkazu Mezi možnosti patří: text, JSON. Výchozí hodnota je "text".|
|--Trusted – řetězec Microsoft-přípony   |Určuje další přípony domén, kde se můžou odesílat přihlašovací tokeny Azure Active Directory.  Výchozí hodnota je *. Core.Windows.NET;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Zde uvedené jsou přidány do výchozího nastavení. Z důvodu zabezpečení byste měli sem umístit jenom Microsoft Azure domény. Více položek oddělte středníkem.|

## <a name="see-also"></a>Viz také

- [AzCopy](storage-ref-azcopy.md)
