---
title: Key Vault poznámky k verzi rozhraní .NET 2. x API | Microsoft Docs
description: Naučte se aktualizovat aplikace napsané pro starší verze Azure Key Vault pro práci s verzí 2,0 knihovny Azure Key Vault pro C# a .NET.
services: key-vault
author: msmbaldwin
manager: rkarlin
editor: bryanla
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/02/2017
ms.author: mbaldwin
ms.openlocfilehash: 018570019b306dced76760fefa4441ee7d86ad2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88189844"
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Azure Key Vault .NET 2,0 – poznámky k verzi a Průvodce migrací
Následující informace pomáhají při migraci na verzi 2,0 knihovny Azure Key Vault pro C# a .NET.  Aplikace napsané pro starší verze se musí aktualizovat, aby podporovaly nejnovější verzi.  Tyto změny jsou potřebné k plnému podpoře nových a vylepšených funkcí, jako jsou **Key Vault certifikáty**.

## <a name="key-vault-certificates"></a>Key Vault certifikátů

Key Vault certifikáty spravují certifikáty x509 a podporují následující chování:  

* Vytvořte certifikáty prostřednictvím procesu vytváření Key Vault nebo importujte stávající certifikát. To zahrnuje certifikáty podepsané svým držitelem i certifikáty vygenerované certifikační autoritou (CA).
* Bezpečně ukládejte a spravujte úložiště certifikátů x509 bez interakce pomocí materiálu privátních klíčů.  
* Definujte zásady, které Key Vault pro správu životního cyklu certifikátů nasměrovat.  
* Poskytněte kontaktní informace pro události životního cyklu, například upozornění vypršení platnosti a oznámení o obnovení.  
* Automaticky obnovuje certifikáty s vybranými vystaviteli (Key Vault poskytovatelé certifikátů x509 a certifikačních autorit). * certifikát podpory od alternativních (nepartnerských) a certifikačních autorit (nepodporuje automatické obnovení).  

## <a name="net-support"></a>Podpora rozhraní .NET

* **Rozhraní .net 4,0** není podporované verzí 2,0 knihovny Azure Key Vault .NET.
* **.NET Framework 4.5.2** podporuje verze 2,0 knihovny Azure Key Vault .NET.
* Verze 2,0 Azure Key Vault knihovny .NET podporuje **.NET Standard 1,4** .

## <a name="namespaces"></a>Obory názvů

* Obor názvů pro **modely** se změnil z **Microsoft. Azure. webtrezoru** na **Microsoft. Azure. webtrezor. Models**.
* **Microsoft. Azure. webtrezor. Internal** obor názvů je vyřazený.
* Následující obory názvů pro závislosti sady Azure SDK mají 

    - **Hyak. Common** je teď **Microsoft. REST**.
    - **Hyak. Common. Internals** je nyní **Microsoft. REST. Serialization**.

## <a name="type-changes"></a>Změny typu

* *Tajný kód* se změnil na *SecretBundle*
* *Slovník* byl změněn na *IDictionary*
* *List \<T> , String []* změněno na *IList \<T> *
* *NextList* změněny na  *NextPageLink*

## <a name="return-types"></a>Návratové typy

* **Seznam** a **SecretList** nyní vrátí *IPage \<T> * namísto *ListKeysResponseMessage*
* Vygenerovaná **BackupKeyAsync** nyní vrátí *BackupKeyResult*, který obsahuje *hodnotu* (zálohovaný objekt BLOB). Dříve byla metoda zabalena a vrátila pouze hodnotu.

## <a name="exceptions"></a>Výjimky

* *KeyVaultClientException* se změní na *KeyVaultErrorException*
* Chyba služby se změnila z *výjimky. Chyba* na *výjimku. Tělo. Error. Message*.
* Odebraly se další informace z chybové zprávy pro: **[JsonExtensionData]**.

## <a name="constructors"></a>Konstruktory

* Namísto přijetí *HttpClient* jako argumentu konstruktoru akceptuje konstruktor pouze *HttpClientHandler* nebo *DelegatingHandler []*.

## <a name="downloaded-packages"></a>Stažené balíčky

Když klient zpracuje závislost Key Vault, stáhnou se tyto balíčky:

### <a name="previous-package-list"></a>Seznam předchozích balíčků

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

* Třída **UnixEpoch** byla odebrána.
* Třída **Base64UrlConverter** je přejmenována na **Base64UrlJsonConverter**.

## <a name="other-changes"></a>Další změny

* Do této verze rozhraní API se přidala podpora konfigurace zásad opakování operací KV pro přechodné selhání.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft. Azure. Management. NuGet – NuGet

* Pro operace, které vrátily *trezor*, byl návratový typ třída, která obsahuje vlastnost **trezoru** . Návratový typ je nyní *trezor*.
* *PermissionsToKeys* a *PermissionsToSecrets* jsou teď *oprávnění. klíče* a *oprávnění. tajné* klíče
* Některé změny návratových typů se vztahují i na rovinu ovládacího prvku.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>NuGet Microsoft. Azure. webtrezor. Extensions. Extensions

* Balíček je rozdělen do **Microsoft. Azure. webtrezor. Extensions** a **Microsoft. Azure. webtrezor. Cryptography** pro operace kryptografie.

