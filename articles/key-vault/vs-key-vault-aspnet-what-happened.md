---
title: Změny provedené v technologie ASP.NET projektu, když jste připojení ke službě do Azure Key Vault | Microsoft Docs
description: Popisuje, co se stane projektu ASP.NET, jakmile se připojíte pomocí sady Visual Studio připojené služby toKey trezoru.
services: key-vault
author: ghogen
manager: douge
tags: azure-resource-manager
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: a43f893c7ee87ffb02179c06ea5786715547e93a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-key-vault-connected-service"></a>Co se stalo s Moje projekt ASP.NET (Visual Studio Key Vault připojeno service)?

> [!div class="op_single_selector"]
> - [Začínáme](vs-key-vault-aspnet-get-started.md)
> - [Co se přihodilo](vs-key-vault-aspnet-what-happened.md)

Tento článek identifikuje přesný změny provedené v projektu ASP.NET při přidávání [Key Vault připojení služby pomocí sady Visual Studio](vs-key-vault-add-connected-service.md).

Informace o práci s připojené služby najdete v tématu [Začínáme](vs-key-vault-aspnet-get-started.md).

## <a name="added-references"></a>Přidání odkazů

Ovlivňuje odkazy na projekt souboru *.NET a `packages.config` (odkazů NuGet).

| Typ | Referenční informace |
| --- | --- |
| ROZHRANÍ .NET; NuGet | Microsoft.Azure.KeyVault |
| ROZHRANÍ .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| ROZHRANÍ .NET; NuGet | Microsoft.Rest.ClientRuntime |
| ROZHRANÍ .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

## <a name="added-files"></a>Přidání souborů

- ConnectedService.json přidali, který zaznamenává některé informace o poskytovateli připojené služby, verzi a odkaz na dokumentaci.

## <a name="project-file-changes"></a>Změny souborů projektu

- Přidat připojení služby ItemGroup a ConnectedServices.json souboru.
- Odkazy na sestavení .NET, které jsou popsané v [přidány odkazy](#added-references) části.

## <a name="webconfig-or-appconfig-changes"></a>soubor Web.config nebo app.config změny

- Byla přidána následující položky konfigurace:

    ```xml
    <appSettings>
       <add key="vaultName" value="<your Key Vault name>" />
       <add key="vaultUri" value="<the URI to your Key Vault in Azure>" />
    </appSettings>
    ```

## <a name="changes-on-azure"></a>Změny v Azure

- Vytvořit skupinu prostředků (nebo použít existující šablonu).
- Key Vault vytvoří v zadaná skupina prostředků.

