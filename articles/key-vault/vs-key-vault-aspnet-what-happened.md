---
title: Změny provedené v ASP.NET projektu při připojo do služby Azure Key Vault | Dokumentace Microsoftu
description: Popisuje, co se stane projektu ASP.NET, když se připojíte pomocí služeb Visual Studio připojené toKey trezoru.
services: key-vault
author: ghogen
manager: douge
tags: azure-resource-manager
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: a15f9c41c5af8803bfb230b19cbbf2f1bdbc2686
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44050685"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-key-vault-connected-service"></a>Co se stalo s mým projektem ASP.NET (Visual Studio Key Vault připojená služba)?

> [!div class="op_single_selector"]
> - [Začínáme](vs-key-vault-aspnet-get-started.md)
> - [Co se přihodilo](vs-key-vault-aspnet-what-happened.md)

Tento článek identifikuje přesný změny provedené v projektu ASP.NET při přidávání [service pomocí sady Visual Studio připojené služby Key Vault](vs-key-vault-add-connected-service.md).

Informace o práci s připojenou službu, najdete v části [Začínáme](vs-key-vault-aspnet-get-started.md).

## <a name="added-references"></a>Přidání odkazů

Má vliv na odkazy na soubory *.NET projektu a `packages.config` (odkazy na NuGet).

| Typ | Referenční informace |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

## <a name="added-files"></a>Přidání souborů

- Soubor ConnectedService.json přidali, která zaznamenává některé informace o poskytovateli připojenou službu, verzi a odkaz na dokumentaci.

## <a name="project-file-changes"></a>Změny v souboru projektu

- Přidání připojené služby ItemGroup a ConnectedServices.json souboru.
- Odkazy na sestavení .NET je popsáno v [přidali odkazy](#added-references) oddílu.

## <a name="webconfig-or-appconfig-changes"></a>změny v souboru Web.config nebo app.config

- Přidá následující položky konfigurace:

    ```xml
    <configSections>
      <section
           name="configBuilders"
           type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" 
           restartOnExternalChanges="false"
           requirePermission="false" />
    </configSections>
    <configBuilders>
      <builders>
        <add 
             name="AzureKeyVault"
             vaultName="vaultname"
             type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=1.0.0.0, Culture=neutral" 
             vaultUri="https://vaultname.vault.azure.net" />
      </builders>
    </configBuilders>
    ```

## <a name="changes-on-azure"></a>Změny v Azure

- Vytvoří skupinu prostředků (nebo použít existující).
- V zadané skupině prostředků vytvořili službu Key Vault.

