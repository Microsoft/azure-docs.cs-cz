---
title: Změny provedené v ASP.NET Core projektu, když jste připojení ke službě do Azure Key Vault | Microsoft Docs
description: Popisuje, co se stane projektu ASP.NET Core, jakmile se připojíte pomocí sady Visual Studio připojené služby toKey trezoru.
services: key-vault
author: ghogen
manager: douge
tags: azure-resource-manager
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: 8b6c590344db2997c1a987da14cabba76cdca83b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="what-happened-to-my-aspnet-core-project-visual-studio-key-vault-connected-service"></a>Co se stalo s ASP.NET Core projektu (Visual Studio Key Vault připojeno service)?

> [!div class="op_single_selector"]
> - [Začínáme](vs-key-vault-aspnet-core-get-started.md)
> - [Co se přihodilo](vs-key-vault-aspnet-core-what-happened.md)

Tento článek identifikuje přesný změny provedené v projektu ASP.NET při přidávání [Key Vault připojení služby pomocí sady Visual Studio](vs-key-vault-add-connected-service.md).

Informace o práci s připojené služby najdete v tématu [Začínáme](vs-key-vault-aspnet-core-get-started.md).

## <a name="added-references"></a>Přidání odkazů

Ovlivňuje odkazů *.NET souboru projektu a odkazy na balíček NuGet.

| Typ | Referenční informace |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

## <a name="added-files"></a>Přidání souborů

- ConnectedService.json přidali, který zaznamenává některé informace o poskytovateli připojené služby, verzi a odkaz na dokumentaci.

## <a name="project-file-changes"></a>Změny souborů projektu

- Přidat připojení služby ItemGroup a ConnectedServices.json souboru.

## <a name="launchsettingsjson-changes"></a>launchsettings.JSON změny

- Následující položky proměnné prostředí přidat do profilu služby IIS Express a profil, který odpovídá název webového projektu:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

## <a name="changes-on-azure"></a>Změny v Azure

- Vytvořit skupinu prostředků (nebo použít existující šablonu).
- Key Vault vytvoří v zadaná skupina prostředků.

