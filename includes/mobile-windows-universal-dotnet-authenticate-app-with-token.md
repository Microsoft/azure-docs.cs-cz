
1. V souboru projektu MainPage.xaml.cs, přidejte následující **pomocí** příkazy:
   
        using System.Linq;        
        using Windows.Security.Credentials;
2. Nahraďte **AuthenticateAsync** metoda následujícím kódem:
   
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
                // expired, as shown in this post: http://aka.ms/jww5vp.
   
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
   
    V této verzi **AuthenticateAsync**, aplikace se pokusí použít přihlašovací údaje uložené v **PasswordVault** přístup ke službě. Regulární přihlášení je také tehdy, pokud žádné uložených přihlašovacích údajů.
   
   > [!NOTE]
   > Token v mezipaměti jeho platnost vypršela a vypršení platnosti tokenu může také dojít po ověření, když aplikace je používána. Zjistěte, jak určit, pokud vypršela platnost tokenu, najdete v tématu [zkontrolujte vypršela platnost ověřování tokenů](http://aka.ms/jww5vp). Řešení pro zpracování chyb autorizace související s tokeny kterým vyprší platnost, najdete v příspěvku [SDK ke správě ukládání do mezipaměti a zpracování vypršení platnosti tokenů v Azure Mobile Services](http://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx). 
   > 
   > 
3. Restartujte aplikaci dvakrát.
   
    Všimněte si, že na první spuštění, přihlaste se pomocí zprostředkovatele vyžádáním znovu. Ale na druhém restartu se používají přihlašovací údaje v mezipaměti a přihlašování bude přeskočeno. 

