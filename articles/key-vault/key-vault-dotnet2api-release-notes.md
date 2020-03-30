---
title: Key Vault .NET 2.x POZNÁMKY k verzi rozhraní API| Dokumenty společnosti Microsoft
description: Vývojáři rozhraní .NET budou používat toto rozhraní API ke kódu pro Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
editor: bryanla
ms.service: key-vault
ms.topic: conceptual
ms.date: 05/02/2017
ms.author: mbaldwin
ms.openlocfilehash: 07502b4c4487a7517e2fe73b62eae0a237a6f22b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70883265"
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Azure Key Vault .NET 2.0 – poznámky k verzi a průvodce migrací
Následující informace pomáhají migrovat na verzi 2.0 knihovny Azure Key Vault pro C# a .NET.  Aplikace napsané pro starší verze je třeba aktualizovat, aby podporovaly nejnovější verzi.  Tyto změny jsou potřebné k plné podpoře nových a vylepšených funkcí, jako jsou **například certifikáty trezoru klíčů**.

## <a name="key-vault-certificates"></a>Certifikáty trezoru klíčů

Certifikáty trezoru klíčů spravují certifikáty x509 a podporují následující chování:  

* Vytvořte certifikáty prostřednictvím procesu vytváření trezoru klíčů nebo importujte existující certifikát. To zahrnuje certifikáty podepsané svým držitelem i certifikáty (CERTIFIKAČNÍ AUTORITA).
* Bezpečně uklánějte a spravujte úložiště certifikátů x509 bez interakce pomocí materiálu soukromého klíče.  
* Definujte zásady, které směrují trezor klíčů ke správě životního cyklu certifikátu.  
* Zadejte kontaktní informace pro události životního cyklu, jako jsou upozornění na vypršení platnosti a oznámení o obnovení.  
* Automaticky obnovovat certifikáty s vybranými vystaviteli (poskytovatelé certifikátů Key Vault partner X509 a certifikační autority).* Certifikát podpory od náhradníka (jiného partnera) poskytuje a certifikační autority (nepodporuje automatické obnovení).  

## <a name="net-support"></a>Podpora rozhraní .NET

* **Rozhraní .NET 4.0** není podporováno verzí knihovny Azure Key Vault .NET 2.0
* **Rozhraní .NET Framework 4.5.2** je podporováno verzí knihovny Azure Key Vault .NET 2.0
* **Rozhraní .NET Standard 1.4** je podporováno verzí knihovny Azure Key Vault .NET 2.0

## <a name="namespaces"></a>Jmenné prostory

* Obor názvů **modelů** se změní z **Microsoft.Azure.KeyVault** na **Microsoft.Azure.KeyVault.Models**.
* Obor názvů **Microsoft.Azure.KeyVault.Internal** je vynechán.
* Následující obory názvů závislostí sady Azure SDK mají 

    - **Hyak.Common** je nyní **Microsoft.Rest**.
    - **Hyak.Common.Internals** je nyní **Microsoft.Rest.Serialization**.

## <a name="type-changes"></a>Změny typu

* *Tajný klíč* byl změněn na *SecretBundle*
* *Slovník* byl změněn na *IDictionary*
* *Seznam\<T>, řetězec []* změněn na *\<IList T>*
* *NextList* byl změněn na *NextPageLink.*

## <a name="return-types"></a>Návratové typy

* **KeyList** a **SecretList** nyní vrátí *IPage\<T>* namísto *ListKeysResponseMessage*
* Vygenerovaný **BackupKeyAsync** nyní vrátí *BackupKeyResult*, který obsahuje *Value* (back-up blob). Dříve byla metoda zabalena a vrácena pouze hodnotu.

## <a name="exceptions"></a>Výjimky

* *KeyVaultClientException* byl změněn na *KeyVaultErrorException*
* Chyba služby se změnila z *výjimky. Chyba* *výjimky. Body.Error.Message*.
* Z chybové zprávy **[JsonExtensionData]** byly odebrány další informace.

## <a name="constructors"></a>Konstruktory

* Namísto přijetí *httpclient* jako argument konstruktoru konstruktoru konstruktor přijímá pouze *HttpClientHandler* nebo *DelegatingHandler[]*.

## <a name="downloaded-packages"></a>Stažené balíčky

Když klient zpracuje závislost trezoru klíčů, stáhnou se následující balíčky:

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

## <a name="class-changes"></a>Změny třídy

* **Třída UnixEpoch** byla odebrána.
* **Třída Base64UrlConverter** je přejmenována na **Base64UrlJsonConverter**.

## <a name="other-changes"></a>Další změny

* Do této verze rozhraní API byla přidána podpora pro konfiguraci zásady opakování operace KV s přechodným selháním.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* Pro operace, které *vrátily úschovnu*, byl návratový typ třídou, která obsahovala vlastnost **Vault.** Návratový typ je nyní *Trezor*.
* *PermissionsToKeys* a *PermissionsToSecrets* jsou nyní *Permissions.Keys* a *Permissions.Secrets*
* Některé změny návratových typů platí také pro rovinu ovládacího prvku.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* Balíček je rozdělen na **Microsoft.Azure.KeyVault.Extensions** a **Microsoft.Azure.KeyVault.Cryptography** pro kryptografické operace.

