---
title: Předpoklady pro Xamarin Android (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o konkrétních doporučeních pro použití Xamarin Androidu s knihovnou Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 54df91d38541fbe17a28c9ae083ae0e7d0c9d88d
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063658"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Doporučení pro Xamarin Android týkající se MSAL.NET
Tento článek popisuje konkrétní informace týkající se použití Xamarin Androidu s knihovnou Microsoft Authentication Library pro .NET (MSAL.NET).

## <a name="set-the-parent-activity"></a>Nastavení nadřazené aktivity

V Xamarin. Android je nutné nastavit nadřazenou aktivitu tak, aby se token znovu vrátil, jakmile k interakci dojde.

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```
Můžete ji také nastavit na úrovni PublicClientApplication (v MSAL 4.2 +) prostřednictvím zpětného volání.

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Doporučením je [použít CurrentActivityPlugin.](https://github.com/jamesmontemagno/CurrentActivityPlugin)  Váš kód tvůrce PublicClientApplication by pak vypadal takto:

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```


## <a name="ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends"></a>Zajistěte, aby se řízení vrátilo zpátky na MSAL, jakmile skončí interaktivní část toku ověřování.
V systému Android je nutné přepsat metodu `OnActivityResult` `Activity` a volat metodu SetAuthenticationContinuationEventArgs třídy MSAL AuthenticationContinuationHelper.

```csharp
protected override void OnActivityResult(int requestCode, 
                                         Result resultCode, Intent data)
{
 base.OnActivityResult(requestCode, resultCode, data);
 AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                         resultCode,
                                                                         data);
}

```
Tento řádek zajistí, že se ovládací prvek vrátí zpět na MSAL, jakmile se dokončí interaktivní část toku ověřování.

## <a name="update-the-android-manifest"></a>Aktualizace manifestu pro Android
`AndroidManifest.xml` by měl obsahovat následující hodnoty:
```xml
<activity android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="msauth"
              android:host="Enter_the_Package_Name"
              android:path="/Enter_the_Signature_Hash"/>
         </intent-filter>
</activity>
```
Pro `android:host=` hodnotu nahraďte název balíčku, který jste zaregistrovali v Azure Portal. Hodnotu hash klíče, kterou jste zaregistrovali v Azure Portal, nahraďte hodnotou `android:path=`. Hodnota hash **podpisu by neměla být kódovaná** v adrese URL. Zajistěte, aby na začátku hodnoty hash podpisu existovala úvodní `/`.

Nebo můžete [aktivitu vytvořit v kódu](https://docs.microsoft.com/xamarin/android/platform/android-manifest#the-basics) a nemusíte je ručně upravovat `AndroidManifest.xml`. V takovém případě je nutné vytvořit třídu, která má atribut `Activity` a `IntentFilter`. Třída, která představuje stejné hodnoty výše uvedeného XML, by byla:

```csharp
  [Activity]
  [IntentFilter(new[] { Intent.ActionView },
        Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
        DataHost = "auth",
        DataScheme = "msal{client_id}")]
  public class MsalActivity : BrowserTabActivity
  {
  }
```

### <a name="xamarinforms-43x-manifest"></a>XamarinForms 4.3. X – manifest

Kód vygenerovaný XamarinForms 4.3. x nastaví atribut `package` tak, aby `com.companyname.{appName}` v `AndroidManifest.xml`. Pokud používáte `DataScheme` jako `msal{client_id}`, můžete změnit hodnotu na stejné jako obor názvů `MainActivity.cs`.

## <a name="use-the-embedded-web-view-optional"></a>Použít vložené webové zobrazení (volitelné)

Ve výchozím nastavení MSAL.NET používá webový prohlížeč systému, který umožňuje získat jednotné přihlašování s webovými aplikacemi a dalšími aplikacemi. V některých vzácných případech může být vhodné určit, že chcete použít vložené webové zobrazení. Další informace najdete v tématu [MSAL.NET používá webový prohlížeč](msal-net-web-browsers.md) a [prohlížeč systému Android](msal-net-system-browser-android-considerations.md).

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

## <a name="troubleshooting"></a>Řešení potíží
Pokud vytvoříte novou aplikaci Xamarin. Forms a přidáte do ní odkaz na balíček NuGet MSAL.Net, bude to jenom fungovat.
Nicméně pokud chcete upgradovat existující aplikaci Xamarin. Forms na MSAL.NET Preview 1.1.2 nebo novější, může dojít k problémům se sestavením.

Chcete-li tyto problémy vyřešit, měli byste:
- Aktualizace existujícího balíčku NuGet MSAL.NET na verzi MSAL.NET verze 1.1.2 nebo novější
- Ověřte, že se Xamarin. Forms automaticky aktualizovala na verzi 2.5.0.122203 (Pokud ne, aktualizujte na tuto verzi).
- Ověřte, jestli se Xamarin. Android. support. v4 automaticky aktualizoval na verzi 25.4.0.2 (Pokud ne, aktualizujte na tuto verzi).
- Všechny balíčky Xamarin. Android. support by měly být cíleny na verzi 25.4.0.2
- Vyčistit/znovu sestavit
- Zkuste nastavit maximální paralelní projekt sestavení na 1 v aplikaci Visual Studio (Možnosti-> projekty a řešení – > sestavování a spouštění-> maximální počet paralelně sestavovaných projektů)
- Případně, pokud vytváříte z příkazového řádku, zkuste z příkazu odebrat/m, pokud ho používáte.


### <a name="error-the-name-authenticationcontinuationhelper-does-not-exist-in-the-current-context"></a>Chyba: název AuthenticationContinuationHelper neexistuje v aktuálním kontextu.

To je pravděpodobně proto, že Visual Studio neaktualizovalo správně soubor Android. csproj *. Někdy **\<cestu >** FilePath nesprávně obsahuje netstandard13 namísto **monoandroid90**.

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Další kroky

Další podrobnosti a ukázky najdete v článku o [specifických informacích pro Android](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations) v následujícím souboru Readme.MD ukázky:

| Ukázka | Platforma | Popis |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Jednoduchá aplikace Xamarin Forms předvádí, jak pomocí MSAL ověřovat MSA a Azure AD prostřednictvím koncového bodu AADD v 2.0 a přistupovat k Microsoft Graph s výsledným tokenem. <br>![Topologie](media/msal-net-xamarin-android-considerations/topology.png) |
