---
title: Změny provedené v ASP.NET Core projektu při připojo do služby Azure Key Vault | Dokumentace Microsoftu
description: Popisuje, co se stane projektu ASP.NET Core, když připojení toKey trezor s použitím sady Visual Studio připojené služby.
services: key-vault
author: ghogen
manager: douge
tags: azure-resource-manager
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: b7cbe55fa3a524965e0ebc16c5ff350a60d6e440
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42060096"
---
# <a name="what-happened-to-my-aspnet-core-project-visual-studio-key-vault-connected-service"></a>Co se stalo s mým projektem ASP.NET Core (Visual Studio Key Vault připojená služba)?

> [!div class="op_single_selector"]
> - [Začínáme](vs-key-vault-aspnet-core-get-started.md)
> - [Co se přihodilo](vs-key-vault-aspnet-core-what-happened.md)

Tento článek identifikuje přesný změny provedené v projektu ASP.NET při přidávání [service pomocí sady Visual Studio připojené služby Key Vault](vs-key-vault-add-connected-service.md).

Informace o práci s připojenou službu, najdete v části [Začínáme](vs-key-vault-aspnet-core-get-started.md).

## <a name="added-references"></a>Přidání odkazů

Ovlivňuje odkazy *.NET soubor projektu a odkazy na balíčky NuGet.

| Typ | Referenční informace |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

## <a name="added-files"></a>Přidání souborů

- Soubor ConnectedService.json přidali, která zaznamenává některé informace o poskytovateli připojenou službu, verzi a odkaz na dokumentaci.

## <a name="project-file-changes"></a>Změny v souboru projektu

- Přidání připojené služby ItemGroup a ConnectedServices.json souboru.

## <a name="launchsettingsjson-changes"></a>launchsettings.JSON změny

- Následující položky proměnná prostředí přidat do profilu služby IIS Express a profil, který odpovídá názvu vašeho webového projektu:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

## <a name="changes-on-azure"></a>Změny v Azure

- Vytvoří skupinu prostředků (nebo použít existující).
- V zadané skupině prostředků vytvořili službu Key Vault.

