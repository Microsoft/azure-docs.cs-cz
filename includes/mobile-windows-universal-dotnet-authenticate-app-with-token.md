---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: d71d52257b6e8cfa243207c9bfdb5c7de7d3dd37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175688"
---
1. Do souboru projektu MainPage.xaml.cs přidejte následující **příkazy:**
   
        using System.Linq;        
        using Windows.Security.Credentials;
2. Nahraďte metodu **AuthenticateAsync** následujícím kódem:
   
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
   
            // This sample uses the Facebook provider.
            var provider = MobileServiceAuthenticationProvider.Facebook;
   
            // Use the PasswordVault to securely store and access credentials.
            PasswordVault vault = new PasswordVault();
            PasswordCredential credential = null;
   
            try
            {
                // Try to get an existing credential from the vault.
                credential = vault.FindAllByResource(provider.ToString()).FirstOrDefault();
            }
            catch (Exception)
            {
                // When there is no matching resource an error occurs, which we ignore.
            }
   
            if (credential != null)
            {
                // Create a user from the stored credentials.
                user = new MobileServiceUser(credential.UserName);
                credential.RetrievePassword();
                user.MobileServiceAuthenticationToken = credential.Password;
   
                // Set the user from the stored credentials.
                App.MobileService.CurrentUser = user;
   
                // Consider adding a check to determine if the token is 
                // expired, as shown in this post: https://aka.ms/jww5vp.
   
                success = true;
                message = string.Format("Cached credentials for user - {0}", user.UserId);
            }
            else
            {
                try
                {
                    // Sign in with the identity provider.
                    user = await App.MobileService
                        .LoginAsync(provider, "{url_scheme_of_your_app}");
   
                    // Create and store the user credentials.
                    credential = new PasswordCredential(provider.ToString(),
                        user.UserId, user.MobileServiceAuthenticationToken);
                    vault.Add(credential);
   
                    success = true;
                    message = string.Format("You are now signed in - {0}", user.UserId);
                }
                catch (MobileServiceInvalidOperationException)
                {
                    message = "You must sign in. Sign-In Required";
                }
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
   
            return success;
        }
   
    V této verzi **authenticateasync**aplikace pokusí použít pověření uložená v **PasswordVault** pro přístup ke službě. Pravidelné přihlášení se provádí také v případě, že není uložena pověření.
   
   > [!NOTE]
   > Platnost tokenu uloženého v mezipaměti může vypršet a vypršení platnosti tokenu může také dojít po ověření, když je aplikace používána. Informace o tom, jak zjistit, jestli vypršela platnost tokenu, najdete v [tématu Kontrola ověřovacích tokenů s ukončenou platností](https://aka.ms/jww5vp). Řešení pro zpracování chyb autorizace souvisejících s vypršením platnosti tokenů najdete v tématu ukládání [do mezipaměti a zpracování tokenů s prošlou platností v Azure Mobile Services spravované sdk](https://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx). 
   > 
   > 
3. Restartujte aplikaci dvakrát.
   
    Všimněte si, že při prvním spuštění je opět vyžadováno přihlášení u zprostředkovatele. Při druhém restartování se však používají přihlašovací údaje uložené v mezipaměti a přihlášení je vynecháno. 

