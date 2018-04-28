---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: d407b015699925a6a3ce6ef9108ace1e9900303f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
## <a name="set-up-your-project"></a>Nastavení projektu

V této části vytvoříte nový projekt na ukazují, jak integrovat aplikace Windows Desktop .NET (XAML) s *přihlášení se společností Microsoft* tak, aby aplikace můžete dotazovat webovým rozhraním API, které vyžadují token.

Aplikace, které vytvoříte v této příručce zobrazí tlačítko, které se používá k volání graf, oblast, kterou chcete zobrazit výsledky na obrazovce a odhlášení tlačítko.

> [!NOTE]
> Místo toho stáhněte projekt Visual Studio Tato ukázka dávají přednost? [Stažení projektu](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/master.zip)a pokračujte [krok konfigurace](#register-your-application) ke konfiguraci ukázka kódu, než ho provést.
>

Pokud chcete vytvořit aplikaci, postupujte takto:
1. V sadě Visual Studio, vyberte **soubor** > **nový** > **projektu**.
2. V části **šablony**, vyberte **Visual C#**.
3. Vyberte **aplikace WPF** nebo **aplikaci WPF**, v závislosti na verzi používáte verzi sady Visual Studio.

## <a name="add-msal-to-your-project"></a>Do projektu přidejte MSAL
1. V sadě Visual Studio, vyberte **nástroje** > **Správce balíčků NuGet**> **Konzola správce balíčků**.
2. V okně konzoly Správce balíčků vložte následující příkaz prostředí Azure PowerShell:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE] 
    > Tento příkaz nainstaluje knihovny pro ověřování Microsoft. MSAL zpracovává získávání, ukládání do mezipaměti a aktualizaci uživatele tokeny, které se používají pro přístup k rozhraní API, které jsou chráněné službou Azure Active Directory v2.
    >

## <a name="add-the-code-to-initialize-msal"></a>Přidat kód pro inicializaci MSAL
V tomto kroku vytvoříte třídu pro zpracování interakci s MSAL, jako je například zpracování tokenů.

1. Otevřete *App.xaml.cs* souboru a poté přidejte odkaz na pro MSAL pro třídu:

    ```csharp
    using Microsoft.Identity.Client;
    ```
<!-- Workaround for Docs conversion bug -->

2. Aktualizace třídy aplikace takto:

    ```csharp
    public partial class App : Application
    {
        //Below is the clientId of your app registration. 
        //You have to replace the below with the Application Id for your app registration
        private static string ClientId = "your_client_id_here";

        public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);

    }
    ```

## <a name="create-the-application-ui"></a>Vytvoření aplikace uživatelského rozhraní

Tato část uvádí, jak můžete aplikaci dotaz na chráněném serveru back-end, jako je například Microsoft Graph. 

A *MainWindow.xaml* soubor by měl být automaticky vytvoří jako součást vaše šablona projektu. Umožňuje otevřít tento soubor a potom můžete nahradit vaší aplikace  *\<mřížky >* uzlu s následujícím kódem:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

