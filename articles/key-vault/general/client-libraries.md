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
ms.openlocfilehash: 34321ac9baf3d3cb43dace33b574cb582c2d72a0
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378946"
---
# <a name="client-libraries-for-azure-key-vault"></a>Klientské knihovny pro Azure Key Vault

Klientské knihovny pro Azure Key Vault umožňují programový přístup k Key Vault funkcím z nejrůznějších jazyků, včetně .NET, Pythonu, Java a JavaScriptu.

## <a name="client-libraries-per-language-and-object"></a>Klientské knihovny na jazyk a objekt

Každá sada SDK obsahuje samostatné klientské knihovny pro tajné klíče, klíče a certifikáty podle níže uvedené tabulky.

| Jazyk | Tajné kódy | Klíče | Certifikáty |
|--|--|--|--|
| .NET | - [Reference k rozhraní API](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet)<br>- [Balíček NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)<br>- [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Secrets)<br>- [Rychlý Start](../secrets/quick-create-net.md) | - [Reference k rozhraní API](/dotnet/api/azure.security.keyvault.keys?view=azure-dotnet)<br>- [Balíček NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Keys/)<br>- [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Keys) | - [Reference k rozhraní API](/dotnet/api/azure.security.keyvault.certificates?view=azure-dotnet)<br>- [Balíček NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)<br>- [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Certificates) |
| Python| - [Reference k rozhraní API](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python)<br>- [Balíček PyPi](https://pypi.org/project/azure-keyvault-secrets/)<br>- [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)<br>- [Rychlý Start](../secrets/quick-create-python.md) |- [Reference k rozhraní API](/python/api/overview/azure/keyvault-keys-readme?view=azure-python)<br>- [Balíček PyPi](https://pypi.org/project/azure-keyvault-keys/)<br>- [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)<br>- [Rychlý Start](../keys/quick-create-python.md) | - [Reference k rozhraní API](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python)<br>- [Balíček PyPi](https://pypi.org/project/azure-keyvault-certificates/)<br>- [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)<br>- [Rychlý Start](../certificates/quick-create-python.md) |
| Java | - [Reference k rozhraní API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>- [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)<br>- [Rychlý Start](../secrets/quick-create-java.md) |- [Reference k rozhraní API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>- [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys) | - [Reference k rozhraní API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>- [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates) |
| Node.js | - [Reference k rozhraní API](/javascript/api/@azure/keyvault-secrets/?view=azure-node-latest)<br>- [balíček npm](https://www.npmjs.com/package/@azure/keyvault-secrest)<br>- [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-secrets)<br>- [Rychlý Start](../secrets/quick-create-node.md) |- [Reference k rozhraní API](/javascript/api/@azure/keyvault-keys/?view=azure-node-latest)<br>- [balíček npm](https://www.npmjs.com/package/@azure/keyvault-keys)<br>- [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-keys)| - [Reference k rozhraní API](/javascript/api/@azure/keyvault-certificates/?view=azure-node-latest)<br>- [balíček npm](https://www.npmjs.com/package/@azure/keyvault-certificates)<br>- [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-certificates) |

## <a name="next-steps"></a>Další kroky

- Projděte si [příručku pro vývojáře Azure Key Vault](developers-guide.md)
- Přečtěte si další informace o [ověřování do trezoru klíčů](authentication.md) .
