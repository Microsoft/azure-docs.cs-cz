---
title: Zpráva k vydání verze .NET 2.x API trezoru klíčů | Dokumentace Microsoftu
description: Vývojáři na platformě .NET bude pomocí tohoto rozhraní API do kódu pro Azure Key Vault
services: key-vault
author: bryanla
manager: barbkess
editor: bryanla
ms.assetid: 1cccf21b-5be9-4a49-8145-483b695124ba
ms.service: key-vault
ms.devlang: CSharp
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/02/2017
ms.author: bryanla
ms.openlocfilehash: 486f31e79210aa67c8545d6bd56f6bedd9297e9c
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56105929"
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Azure Key Vault .NET 2.0 – zpráva k vydání verze a Průvodce migrací
Následující informace pomáhají, migrace do verze 2.0 této knihovny služby Azure Key Vault pro C# a .NET.  Aplikace napsané pro starší verze muset aktualizovat podporovat nejnovější verzi.  Tyto změny jsou potřeba pro úplnou podporu nové a vylepšené funkce, jako například **Key Vault certificates**.

## <a name="key-vault-certificates"></a>Certifikáty služby Key Vault

Certifikáty služby Key Vault spravovat x509 certifikáty a podporuje následující chování:  

* Vytvoření certifikátů procesem vytvoření služby Key Vault nebo naimportujte existující certifikát. To zahrnuje i podepsaný svým držitelem a vygenerovat certifikáty certifikační autority (CA).
* Bezpečně ukládat a spravovat x509 certifikátu úložiště bez zásahu pomocí materiál privátního klíče.  
* Můžete Definujte zásady, které budou řídit Key Vault a správa životního cyklu certifikátu.  
* Zadejte kontaktní údaje pro události životního cyklu, jako je například upozornění na vypršení platnosti a oznámení o prodloužení platnosti.  
* Automaticky prodloužit platnost certifikátů s vybranou vystavitelů (zprostředkovatelů služby Key Vault partnera X509 certifikátů a certifikační autority). * podpora certifikát z alternativní (partneři) poskytuje a certifikační autority (nepodporuje automatické obnovení).  

## <a name="net-support"></a>Podpora platformy .NET

* **Rozhraní .NET 4.0** nepodporuje verzi knihovny Azure Key Vault .NET 2.0
* **Rozhraní .NET framework 4.5.2** podporuje verzi knihovny Azure Key Vault .NET 2.0
* **.NET standard 1.4** podporuje verzi knihovny Azure Key Vault .NET 2.0

## <a name="namespaces"></a>Obory názvů

* Obor názvů pro **modely** se změnil z **Microsoft.Azure.KeyVault** k **Microsoft.Azure.KeyVault.Models**.
* **Microsoft.Azure.KeyVault.Internal** obor názvů se zahodí.
* Mají následující obory názvů závislosti sady Azure SDK 

    - **Hyak.Common** je nyní **Microsoft.Rest**.
    - **Hyak.Common.Internals** je nyní **Microsoft.Rest.Serialization**.

## <a name="type-changes"></a>Typ změny

* *Tajný kód* změněn na *SecretBundle*
* *Slovník* změněn na *IDictionary*
* *Seznam<T>, string []* změněn na *IList<T>*
* *NextList* změněn na *NextPageLink*

## <a name="return-types"></a>Návratové typy

* **KeyList** a **SecretList** nyní vrací *IPage<T>*  místo *ListKeysResponseMessage*
* Vygenerovaný **BackupKeyAsync** nyní vrací *BackupKeyResult*, který obsahuje *hodnotu* (zálohování blob). Metoda dříve, byla zabalena a vrátí jen hodnota.

## <a name="exceptions"></a>Výjimky

* *KeyVaultClientException* se změní na *KeyVaultErrorException*
* Chyba služby se změnil z *výjimky. Chyba* k *výjimky. Body.Error.Message*.
* Odebrat z chybové zprávy pro další informace o **[JsonExtensionData]**.

## <a name="constructors"></a>Konstruktory

* Místo přijetí *HttpClient* jako argument konstruktoru konstruktoru přijímá pouze *HttpClientHandler* nebo *DelegatingHandler []*.

## <a name="downloaded-packages"></a>Stažených balíčků

Když klient zpracovává závislostí služby Key Vault, se stáhnou následujících balíčků:

### <a name="previous-package-list"></a>Předchozí seznamu balíčků

* `package id="Hyak.Common" version="1.0.2" targetFramework="net45"`
* `package id="Microsoft.Azure.Common" version="2.0.4" targetFramework="net45"`
* `package id="Microsoft.Azure.Common.Dependencies" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Azure.KeyVault" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Bcl" version="1.1.9" targetFramework="net45"`
* `package id="Microsoft.Bcl.Async" version="1.0.168" targetFramework="net45"`
* `package id="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"`
* `package id="Microsoft.Net.Http" version="2.2.22" targetFramework="net45"`

### <a name="current-package-list"></a>Aktuální seznam balíčků

* `package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"`

## <a name="class-changes"></a>Třída změny

* **UnixEpoch** třída odebrala.
* **Base64UrlConverter** třídy bylo přejmenováno na **Base64UrlJsonConverter**.

## <a name="other-changes"></a>Další změny

* Na tuto verzi rozhraní API byla přidána podpora pro konfiguraci zásady opakování operace KV na přechodné chyby.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* Pro operace, které vrátily *trezor*, návratový typ je třída, která obsahovala **trezor** vlastnost. Návratový typ je nyní *trezor*.
* *PermissionsToKeys* a *PermissionsToSecrets* jsou nyní *Permissions.Keys* a *Permissions.Secrets*
* Některé typy vrácených hodnot změny se aplikují na roviny řízení i.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* Balíček je porušený až **Microsoft.Azure.KeyVault.Extensions** a **Microsoft.Azure.KeyVault.Cryptography** pro kryptografické operace.

