---
title: Klientské knihovny pro Azure Key Vault | Microsoft Docs
description: Klientské knihovny pro Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/14/2020
ms.author: mbaldwin
ms.openlocfilehash: 2c48917e7205bca03380267c1c97469acc245fa6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91628023"
---
# <a name="client-libraries-for-azure-key-vault"></a>Klientské knihovny pro Azure Key Vault

Klientské knihovny pro Azure Key Vault umožňují programový přístup k Key Vault funkcím z nejrůznějších jazyků, včetně .NET, Pythonu, Java a JavaScriptu.

## <a name="client-libraries-per-language-and-object"></a>Klientské knihovny na jazyk a objekt

Každá sada SDK obsahuje samostatné klientské knihovny pro Trezor klíčů, tajné klíče, klíče a certifikáty, a to podle následující tabulky.

| Jazyk | Tajné kódy | Klíče | Certifikáty | Key Vault (rovina správy)
|--|--|--|--|
| .NET | - [Reference k rozhraní API](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet)<br>- [Balíček NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)<br>- [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Secrets)<br>- [Rychlý Start](../secrets/quick-create-net.md) | - [Reference k rozhraní API](/dotnet/api/azure.security.keyvault.keys?view=azure-dotnet)<br>- [Balíček NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Keys/)<br>- [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Keys) | - [Reference k rozhraní API](/dotnet/api/azure.security.keyvault.certificates?view=azure-dotnet)<br>- [Balíček NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)<br>- [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Certificates) | - [Reference k rozhraní API](/dotnet/api/microsoft.azure.management.keyvault?view=azure-dotnet)<br>- [Balíček NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.KeyVault/)<br> - [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Microsoft.Azure.Management.KeyVault)|
| Python| - [Reference k rozhraní API](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python)<br>- [Balíček PyPi](https://pypi.org/project/azure-keyvault-secrets/)<br>- [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)<br>- [Rychlý Start](../secrets/quick-create-python.md) |- [Reference k rozhraní API](/python/api/overview/azure/keyvault-keys-readme?view=azure-python)<br>- [Balíček PyPi](https://pypi.org/project/azure-keyvault-keys/)<br>- [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)<br>- [Rychlý Start](../keys/quick-create-python.md) | - [Reference k rozhraní API](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python)<br>- [Balíček PyPi](https://pypi.org/project/azure-keyvault-certificates/)<br>- [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)<br>- [Rychlý Start](../certificates/quick-create-python.md) | - [Reference k rozhraní API](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault?view=azure-python)<br> - [Balíček PyPi](https://pypi.org/project/azure-mgmt-keyvault/)<br> - [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-mgmt-keyvault)|
| Java | - [Reference k rozhraní API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>- [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)<br>- [Rychlý Start](../secrets/quick-create-java.md) |- [Reference k rozhraní API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>- [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys) | - [Reference k rozhraní API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>- [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates) |- [Reference k rozhraní API](/java/api/com.microsoft.azure.management.keyvault?view=azure-java-stable)<br>- [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/mgmt-v2016_10_01)|
| Node.js | - [Reference k rozhraní API](/javascript/api/@azure/keyvault-secrets/?view=azure-node-latest)<br>- [balíček npm](https://www.npmjs.com/package/@azure/keyvault-secrest)<br>- [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-secrets)<br>- [Rychlý Start](../secrets/quick-create-node.md) |- [Reference k rozhraní API](/javascript/api/@azure/keyvault-keys/?view=azure-node-latest)<br>- [balíček npm](https://www.npmjs.com/package/@azure/keyvault-keys)<br>- [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-keys)| - [Reference k rozhraní API](/javascript/api/@azure/keyvault-certificates/?view=azure-node-latest)<br>- [balíček npm](https://www.npmjs.com/package/@azure/keyvault-certificates)<br>- [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-certificates) |  - [Reference k rozhraní API](/javascript/api/@azure/arm-keyvault/?view=azure-node-latest)<br>- [balíček npm](https://www.npmjs.com/package/@azure/arm-keyvault)<br>- [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/arm-keyvault)

## <a name="next-steps"></a>Další kroky

- Projděte si [příručku pro vývojáře Azure Key Vault](developers-guide.md)
- Přečtěte si další informace o [ověřování k trezoru klíčů](authentication.md) .
- Přečtěte si další informace o [zabezpečení přístupu k Key Vault](secure-your-key-vault.md) .
