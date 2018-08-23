---
title: Začínáme s Key Vault připojenou službu v sadě Visual Studio (projekty ASP.NET) | Dokumentace Microsoftu
description: Pomocí tohoto kurzu vám pomůžou získat informace tom, jak přidat podporu služby Key Vault k webové aplikaci ASP.NET nebo ASP.NET Core.
services: key-vault
author: ghogen
manager: douge
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: 3ca62d47d8e7682c80985bf5409b8540382fbf45
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42061305"
---
# <a name="get-started-with-key-vault-connected-service-in-visual-studio-aspnet-projects"></a>Začínáme s Key Vault připojenou službu v sadě Visual Studio (projekty ASP.NET)

> [!div class="op_single_selector"]
> - [Začínáme](vs-key-vault-aspnet-get-started.md)
> - [Co se přihodilo](vs-key-vault-aspnet-what-happened.md)

Tento článek obsahuje další doprovodné materiály po přidání do projektu aplikace ASP.NET MVC pomocí služby Key Vault **přidání připojené služby** příkaz v sadě Visual Studio. Pokud ještě není přidáte službu do projektu, lze provést kdykoli podle pokynů v [přidat služby Key Vault do vaší webové aplikace pomocí připojených služeb sady Visual Studio](vs-key-vault-add-connected-service.md).

Zobrazit [co se stalo s mým projektem ASP.NET?](vs-key-vault-aspnet-core-what-happened.md) pro změny do svého projektu při přidání připojené služby.

## <a name="after-you-connect"></a>Po připojení

1. Přidání tajného klíče v Key Vault v Azure. Chcete-li získat na správném místě na portálu, klikněte na odkaz pro **spravovat tajné kódy uložené v této službě Key Vault**. Pokud jste zavřeli, na stránce nebo projekt, můžete přejít k tomu [webu Azure portal](https://portal.azure.com) výběrem **všechny služby**v části **zabezpečení**, zvolte **služby Key Vault**, klikněte na tlačítko Key Vault, které jste právě vytvořili.

   ![Přejděte na portálu](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. V části Key Vault pro klíč trezoru, kterou jste vytvořili, vyberte **tajných kódů**, pak **vygenerovat/importovat**.

   ![Vygenerovat/importovat tajného kódu](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Zadejte tajný klíč, jako například **MySecret**a přiřaďte jí libovolnou hodnotou řetězce jako test a pak zvolte **vytvořit** tlačítko.

   ![Vytvoření tajného klíče](media/vs-key-vault-add-connected-service/create-a-secret.jpg)
 
1. (volitelné) Zadejte další tajný kód, ale tentokrát umístil do kategorie podle jeho pojmenování **tajných kódů – MySecret**. Následující syntaxe Určuje kategorii **tajných kódů** tajný klíč, který obsahuje **MySecret**.

1. Upravte soubor web.config následujícím způsobem. Klíče jsou zástupné symboly, které budou nahrazeny AzureKeyVault ConfigurationBuilder s hodnotami tajných klíčů ve službě Key Vault.

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

1. V HomeController v metodě o kontroleru přidejte následující řádky k načtení tajný klíč a uložte ho objekt ViewBag.
 
   ```csharp
            var secret = ConfigurationManager.AppSettings["MySecret"];
            var secret2 = ConfigurationManager.AppSettings["Secrets--MySecret"];
            ViewBag.Secret = $"Secret: {secret}";
            ViewBag.Secret2 = $"Secret2: {secret2}";
   ```

1. V zobrazení About.cshtml přidejte následující k zobrazení hodnoty tajný kód (pouze pro testování).

   ```csharp
      <h3>@ViewBag.Secret</h3>
      <h3>@ViewBag.Secret2</h3>
   ```

Blahopřejeme, nyní povolíte vaší webové aplikace pomocí služby Key Vault přístup k tajným klíčům bezpečně uložené.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků. Tím se odstraní služby Key Vault a související prostředky. Odstranění skupiny prostředků přes portál:

1. Do pole Hledat v horní části portálu zadejte název vaší skupiny prostředků. Až se ve výsledcích hledání zobrazí skupina prostředků použitá v tomto rychlém startu, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. Do pole **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ:** zadejte název vaší skupiny prostředků a vyberte **Odstranit**.

# <a name="next-steps"></a>Další postup

Další informace o vývoji s využitím služby Key Vault v [– Příručka vývojáře pro Key Vault](key-vault-developers-guide.md)