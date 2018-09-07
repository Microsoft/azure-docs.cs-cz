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
ms.openlocfilehash: c5d61da61f6ec98a1cac37ce9b12b28019ce2ae1
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "44058382"
---
## <a name="set-up-your-project"></a>Nastavení projektu

V této části vytvoříte nový projekt na ukazují, jak integrovat aplikace Windows Desktop .NET (XAML) s *ve službě Microsoft* tak, aby aplikace může dotazovat webové rozhraní API, která vyžaduje token.

Aplikace, kterou vytvoříte v této příručce se zobrazí tlačítko, které se používá k volání grafu, oblast, kterou chcete zobrazit výsledky na obrazovce a tlačítkem pro odhlášení.

> [!NOTE]
> Stáhněte si tento ukázkový projekt sady Visual Studio místo toho raději? [Stáhnete projekt](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/master.zip)a pokračujte [potřeba provádět krok konfigurace](#register-your-application) konfigurace ukázkového kódu před spuštěním.
>

Jak vytvořit aplikaci, postupujte takto:
1. V sadě Visual Studio, vyberte **souboru** > **nový** > **projektu**.
2. V části **šablony**vyberte **Visual C#**.
3. Vyberte **aplikace WPF** nebo **aplikace WPF**, v závislosti na verzi verze sady Visual Studio, kterou používáte.

## <a name="add-msal-to-your-project"></a>Do projektu přidejte MSAL
1. V sadě Visual Studio, vyberte **nástroje** > **Správce balíčků NuGet**> **Konzola správce balíčků**.
2. V okně konzoly Správce balíčků vložte následující příkaz prostředí Azure PowerShell:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre -Version 1.1.4-preview0002
    ```

    > [!NOTE] 
    > Tento příkaz nainstaluje knihovny Microsoft Authentication Library. Knihovna MSAL zpracovává načítání, ukládání do mezipaměti a aktualizaci tokeny uživatele, které se používají pro přístup k rozhraní API, která jsou chráněné službou Azure Active Directory v2.
    >

    > [!NOTE]
    > V tomto rychlém startu tak není, ale použijte nejnovější verzi MSAL.NET, ale pracujeme na jeho aktualizace
    > 

## <a name="add-the-code-to-initialize-msal"></a>Přidejte kód pro inicializaci MSAL
V tomto kroku vytvoříte třídu pro zpracování interakci s MSAL, jako je zpracování tokenů.

1. Otevřít *App.xaml.cs* soubor a přidejte odkaz na MSAL do třídy:

    ```csharp
    using Microsoft.Identity.Client;
    ```
<!-- Workaround for Docs conversion bug -->

2. Aktualizujte třídu aplikace takto:

    ```csharp
    public partial class App : Application
    {
        //Below is the clientId of your app registration. 
        //You have to replace the below with the Application Id for your app registration
        private static string ClientId = "your_client_id_here";

        public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);

    }
    ```

## <a name="create-the-application-ui"></a>Vytvoření uživatelského rozhraní aplikace

Tato část ukazuje, jak aplikace může dotazovat chráněného serveru back-end, jako je například Microsoft Graphu. 

A *souboru MainWindow.xaml* soubor by měl být automaticky vytvoří jako součást šablony projektu. Otevřete tento soubor a potom nahraďte vaší aplikace  *\<mřížky >* uzel s následujícím kódem:

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

