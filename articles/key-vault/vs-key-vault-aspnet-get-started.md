---
title: Začínáme s klíč trezoru připojené služby v sadě Visual Studio (projekty ASP.NET) | Microsoft Docs
description: Pomocí tohoto kurzu můžete informace o přidání podpory Key Vault k webové aplikaci ASP.NET nebo ASP.NET Core.
services: key-vault
author: ghogen
manager: douge
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: cd305801f10c899682aa6d751e48f30b6e8303fa
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787399"
---
# <a name="get-started-with-key-vault-connected-service-in-visual-studio-aspnet-projects"></a>Začínáme s klíč trezoru připojené služby v sadě Visual Studio (projekty ASP.NET)

> [!div class="op_single_selector"]
> - [Začínáme](vs-key-vault-aspnet-get-started.md)
> - [Co se přihodilo](vs-key-vault-aspnet-what-happened.md)

Tento článek obsahuje další pokyny po přidání do projektu ASP.NET MVC pomocí Key Vault **přidat připojení služby** příkazu v sadě Visual Studio. Pokud jste již přidali službu do projektu, můžete tak učinit kdykoli podle pokynů v [Key Vault přidat k vaší webové aplikaci pomocí Visual Studio připojené Services](vs-key-vault-add-connected-service.md).

V tématu [co se stalo s Moje projekt ASP.NET?](vs-key-vault-aspnet-core-what-happened.md) pro změny provedené při přidání připojené služby v projektu.

## <a name="after-you-connect"></a>Po připojení

1. V v Azure Key Vault přidáte tajný klíč. Abyste se dostali na správném místě na portálu, klikněte na odkaz pro **spravovat tajné klíče uložené v tomto trezoru klíč**. Pokud jste zavřeli stránce nebo projektu, můžete přejít na ho [portál Azure](https://portal.azure.com) výběrem **všechny služby**v části **zabezpečení**, zvolte **Key Vault**, zvolte Key Vault, kterou jste právě vytvořili.

   ![Navigace na portálu](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. V části Key Vault pro klíč trezoru, kterou jste vytvořili, zvolte **tajné klíče**, pak **generovat a Import**.

   ![Generování a Import tajný klíč](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Zadejte tajný klíč, například **MySecret**a dejte mu libovolnou hodnotu řetězce jako testu, a potom vyberte **vytvořit** tlačítko.

   ![Vytvoření tajného klíče](media/vs-key-vault-add-connected-service/create-a-secret.jpg)
 
1. (volitelné) Zadejte další sdílený tajný klíč, ale tentokrát ho přesuňte do kategorie pojmenováním ho **tajné klíče--MySecret**. Tuto syntaxi Určuje kategorii **tajné klíče** tajný klíč, který obsahuje **MySecret**.

1. Upravte soubor web.config následujícím způsobem. U klíčů se zástupné symboly, které budou nahrazeny AzureKeyVault ConfigurationBuilder hodnotami tajných klíčů v Key Vault.

   ```xml
     <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="MySecret" value="dummy1"/>
       <add key="Secrets--MySecret" value="dummy2"/>
     </appSettings>
   ```

1. V HomeController, v metodě o kontroleru přidejte následující řádky můžete načíst tajný klíč a uložit ho do objekt ViewBag.
 
   ```csharp
            var secret = ConfigurationManager.AppSettings["MySecret"];
            var secret2 = ConfigurationManager.AppSettings["Secrets--MySecret"];
            ViewBag.Secret = $"Secret: {secret}";
            ViewBag.Secret2 = $"Secret2: {secret2}";
   ```

1. V zobrazení About.cshtml přidejte následující zobrazit hodnotu tajný klíč (pro pouze zkušební režim).

   ```csharp
      <h3>@ViewBag.Secret</h3>
      <h3>@ViewBag.Secret2</h3>
   ```

Blahopřejeme, nyní jste povolili vaší webové aplikace používat Key Vault pro přístup k bezpečně uloženým tajných klíčů.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, odstraňte skupinu prostředků. To odstraní Key Vault a související prostředky. Odstranění skupiny prostředků přes portál:

1. Do pole Hledat v horní části portálu zadejte název vaší skupiny prostředků. Až se ve výsledcích hledání zobrazí skupina prostředků použitá v tomto rychlém startu, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. Do pole **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ:** zadejte název vaší skupiny prostředků a vyberte **Odstranit**.

# <a name="next-steps"></a>Další postup

Další informace o vývoji s v Key Vault [Příručka vývojáře Key Vault](key-vault-developers-guide.md)