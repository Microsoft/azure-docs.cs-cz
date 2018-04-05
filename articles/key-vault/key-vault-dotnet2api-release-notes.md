---
title: Klíč trezoru .NET 2.x API poznámky k verzi | Microsoft Docs
description: Vývojáři rozhraní .NET bude používat toto rozhraní API do kódu pro Azure Key Vault
services: key-vault
author: lleonard-msft
manager: mbaldwin
editor: alleonar
ms.assetid: 1cccf21b-5be9-4a49-8145-483b695124ba
ms.service: key-vault
ms.devlang: CSharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/02/2017
ms.author: alleonar
ms.openlocfilehash: a7735f8c1c4332bf2472bc83c0c37baf49019004
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Azure Key Vault rozhraní .NET 2.0 – poznámky k verzi a Průvodci migrací
Následující informace pomáhají migrace na verzi 2.0 knihovny Azure Key Vault pro C# a rozhraní .NET.  Aplikací napsaných pro starší verze je třeba, že se aktualizuje na podporu nejnovější verze.  Tyto změny jsou potřeba pro úplnou podporu nových a vylepšených funkcích, jako například **Key Vault certifikáty**.

## <a name="key-vault-certificates"></a>Certifikáty Key Vault

Spravovat certifikáty Key Vault x509 certifikáty a podporuje následující chování:  

* Vytvořte certifikáty procesem vytvoření Key Vault nebo importujte existující certifikát. To zahrnuje i podepsaného svým držitelem a vygeneruje certifikáty certifikační autority (CA).
* Bezpečně ukládat a spravovat x509 certifikátu úložiště bez zásahu pomocí materiál privátního klíče.  
* Definujte zásady, které budou řídit Key Vault pro správu životního cyklu certifikátu.  
* Zadejte kontaktní informace pro události životního cyklu, jako je například upozornění na vypršení platnosti a obnovení oznámení.  
* Automatické obnovení certifikátů pomocí vybrané vystavitelů (zprostředkovatelů Key Vault partnera X509 certifikátů a certifikačních autorit). * podporu certifikát z alternativní (bez partnera) poskytuje a certifikační úřady (nepodporuje automatické obnovení).  

## <a name="net-support"></a>Podpora v rozhraní .NET

* **Rozhraní .NET 4.0** nepodporuje 2.0 verzi knihovny Azure Key Vault .NET
* **Rozhraní .NET framework 4.5.2** podporuje verze 2.0 knihovny Azure Key Vault .NET
* **.NET standardní 1.4** podporuje verze 2.0 knihovny Azure Key Vault .NET

## <a name="namespaces"></a>Názvové prostory

* Obor názvů pro **modely** změněn z **Microsoft.Azure.KeyVault** k **Microsoft.Azure.KeyVault.Models**.
* **Microsoft.Azure.KeyVault.Internal** obor názvů se ukončí.
* Mít následujících oborů názvů závislosti sady Azure SDK 

    - **Hyak.Common** je nyní **Microsoft.Rest**.
    - **Hyak.Common.Internals** je nyní **Microsoft.Rest.Serialization**.

## <a name="type-changes"></a>Typ změny

* *Tajný klíč* změnit tak, aby *SecretBundle*
* *Slovník* změnit tak, aby *IDictionary*
* *Seznam<T>, string []* změnit tak, aby *rozhraní IList.<T>*
* *NextList* změnit tak, aby *NextPageLink*

## <a name="return-types"></a>Návratové typy

* **KeyList** a **SecretList** nyní vrátí *IPage<T>*  místo *ListKeysResponseMessage*
* Generovaný objekt **BackupKeyAsync** nyní vrátí *BackupKeyResult*, který obsahuje *hodnotu* (zálohování objektů blob). Metoda byla dříve, zabalená a vrátil pouze hodnotu.

## <a name="exceptions"></a>Výjimky

* *KeyVaultClientException* se změní na *KeyVaultErrorException*
* Chyba služby se změnil z *výjimka. Chyba* k *výjimka. Body.Error.Message*.
* Další informace o odeberou v chybové zprávě **[JsonExtensionData]**.

## <a name="constructors"></a>Konstruktory

* Místo přijetí *HttpClient* jako argument konstruktoru, konstruktor přijímá pouze *HttpClientHandler* nebo *[DelegatingHandler]*.

## <a name="downloaded-packages"></a>Stažených balíčků

Když klient zpracovává závislost Key Vault, se stáhnou následujících balíčků:

### <a name="previous-package-list"></a>Předchozí seznam balíčků

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

## <a name="class-changes"></a>Změny – třída

* **UnixEpoch** třída byla odebrána.
* **Base64UrlConverter** třída je přejmenován na **Base64UrlJsonConverter**.

## <a name="other-changes"></a>Další změny

* Na tuto verzi rozhraní API byla přidána podpora pro konfiguraci zásady opakování operace KV na přechodných chyb.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* Pro operace, které vrátil *trezoru*, návratový typ byl třídu, která obsahovala **trezoru** vlastnost. Návratový typ je nyní *trezoru*.
* *PermissionsToKeys* a *PermissionsToSecrets* jsou nyní *Permissions.Keys* a *Permissions.Secrets*
* Některé změny návratové typy platí pro ovládací prvek roviny také.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* Balíček je porušený než **Microsoft.Azure.KeyVault.Extensions** a **Microsoft.Azure.KeyVault.Cryptography** pro operace šifrování.

