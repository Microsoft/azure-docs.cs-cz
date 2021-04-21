---
title: Zabezpečení služby Securing Azure Remote Rendering a úložiště modelů
description: Posílení zabezpečení pro vzdálenou aplikaci pro vykreslování při zabezpečení obsahu
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: d30ab051e58573daefd16f178feb4fc94f2ec83f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835465"
---
# <a name="tutorial-securing-azure-remote-rendering-and-model-storage"></a>Kurz: zabezpečení vzdáleného vykreslování a úložiště modelu Azure

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Zabezpečení Azure Blob Storage obsahující modely vzdáleného vykreslování Azure
> * Ověření ve službě Azure AD pro přístup k instanci vzdáleného vykreslování Azure
> * Použití přihlašovacích údajů Azure pro ověřování vzdáleného vykreslování Azure

## <a name="prerequisites"></a>Požadavky

* Tento kurz sestaví [kurz: rafinace materiálů, osvětlení a efektů](..\materials-lighting-effects\materials-lighting-effects.md).

## <a name="why-additional-security-is-needed"></a>Proč je potřeba další zabezpečení

Aktuální stav aplikace a její přístup k prostředkům Azure vypadá takto:

![Počáteční zabezpečení](./media/security-one.png)

ID účtu + AccountKey i adresa URL + token SAS v podstatě ukládají uživatelské jméno a heslo společně. Pokud jste například vystavili "ID účtu + AccountKey", musel by být triviální útočník, který použije vaše prostředky ARR bez vašeho svolení na vaše náklady.

## <a name="securing-your-content-in-azure-blob-storage"></a>Zabezpečení obsahu v Azure Blob Storage

Vzdálené vykreslování Azure může bezpečně přistupovat k obsahu vašeho Blob Storage Azure se správnou konfigurací. Viz [Postupy: propojení účtů úložiště](../../../how-tos/create-an-account.md#link-storage-accounts) a konfigurace instance vzdáleného vykreslování Azure pomocí účtů úložiště BLOB.

Při použití propojeného úložiště objektů BLOB použijete mírně různé metody načítání modelů:

```cs
var loadModelParams = new LoadModelFromSasOptions(modelPath, modelEntity);
var task = ARRSessionService.CurrentActiveSession.Connection.LoadModelFromSasAsync(loadModelParams);
```

Výše uvedené řádky používají `FromSas` verzi parametrů a akci relace. Je nutné je převést na jiné verze než SAS:

```cs
var loadModelParams = new LoadModelOptions(storageAccountPath, blobContainerName, modelPath, modelEntity);
var task = ARRSessionService.CurrentActiveSession.Connection.LoadModelAsync(loadModelParams);
```

Pojďme upravit **RemoteRenderingCoordinator** , aby se načetl vlastní model z propojeného účtu úložiště BLOB.

1. Pokud jste to ještě neudělali, dokončete [Postup: propojení účtů úložiště](../../../how-tos/create-an-account.md#link-storage-accounts) a udělte vaší instanci ARR oprávnění k přístupu k vaší instanci BLOB Storage.
1. Přidejte následující upravenou metodu **LoadModel** pro **RemoteRenderingCoordinator** hned pod aktuální metodu **LoadModel** :

    ```cs
    /// <summary>
    /// Loads a model from blob storage that has been linked to the ARR instance
    /// </summary>
    /// <param name="storageAccountName">The storage account name, this contains the blob containers </param>
    /// <param name="blobContainerName">The blob container name, i.e. arroutput</param>
    /// <param name="modelPath">The relative path inside the container to the model, i.e. test/MyCustomModel.arrAsset</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <returns></returns>
    public async Task<Entity> LoadModel(string storageAccountName, string blobContainerName, string modelPath, Transform parent = null, Action<float> progress = null)
    {
        //Create a root object to parent a loaded model to
        var modelEntity = ARRSessionService.CurrentActiveSession.Connection.CreateEntity();

        //Get the game object representation of this entity
        var modelGameObject = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        //Ensure the entity will sync its transform with the server
        var sync = modelGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        //Parent the new object under the defined parent
        if (parent != null)
        {
            modelGameObject.transform.SetParent(parent, false);
            modelGameObject.name = parent.name + "_Entity";
        }

    #if UNITY_WSA
        //Anchor the model in the world, prefer anchoring parent if there is one
        if (parent != null)
        {
            parent.gameObject.AddComponent<WorldAnchor>();
        }
        else
        {
            modelGameObject.AddComponent<WorldAnchor>();
        }
    #endif

        //Load a model that will be parented to the entity
        var loadModelParams = new LoadModelOptions($"{storageAccountName}.blob.core.windows.net", blobContainerName, modelPath, modelEntity);
        var loadModelAsync = ARRSessionService.CurrentActiveSession.Connection.LoadModelAsync(loadModelParams, progress);
        var result = await loadModelAsync;
        return modelEntity;
    }
    ```

    Ve většině případů je tento kód totožný s původní `LoadModel` metodou, ale nahradili jsme verzi SAS voláním metody s verzemi, které nejsou SAS.

    Další vstupy `storageAccountName` a `blobContainerName` byly přidány také k argumentům. Tuto novou metodu **LoadModel** zavoláme z jiné metody podobné metodě **LoadTestModel** , kterou jsme vytvořili v prvním kurzu.

1. Přidejte následující metodu, která se **RemoteRenderingCoordinator** hned za **LoadTestModel**

    ```cs
    private bool loadingLinkedCustomModel = false;

    [SerializeField]
    private string storageAccountName;
    public string StorageAccountName {
        get => storageAccountName.Trim();
        set => storageAccountName = value;
    }

    [SerializeField]
    private string blobContainerName;
    public string BlobContainerName {
        get => blobContainerName.Trim();
        set => blobContainerName = value;
    }

    [SerializeField]
    private string modelPath;
    public string ModelPath {
        get => modelPath.Trim();
        set => modelPath = value;
    }

    [ContextMenu("Load Linked Custom Model")]
    public async void LoadLinkedCustomModel()
    {
        if (CurrentCoordinatorState != RemoteRenderingState.RuntimeConnected)
        {
            Debug.LogError("Please wait for the runtime to connect before loading the test model. Try again later.");
            return;
        }
        if (loadingLinkedCustomModel)
        {
            Debug.Log("Linked Test model already loading or loaded!");
            return;
        }
        loadingLinkedCustomModel = true;

        // Create a parent object to use for positioning
        GameObject testParent = new GameObject("LinkedCustomModel");
        testParent.transform.position = new Vector3(0f, 0f, 3f);

        await LoadModel(StorageAccountName, BlobContainerName, ModelPath, testParent.transform, (progressValue) => Debug.Log($"Loading Test Model progress: {Math.Round(progressValue * 100, 2)}%"));
    }
    ```

    Tento kód přidá do komponenty **RemoteRenderingCoordinator** tři další řetězcové proměnné.
    ![Snímek obrazovky, který zvýrazní název účtu úložiště, název kontejneru objektů BLOB a cestu k modelu komponenty RemoteRenderingCoordinator](./media/storage-account-linked-model.png)

1. Přidejte hodnoty do komponenty **RemoteRenderingCoordinator** . Po převedení [modelu na převod](../../../quickstarts/convert-model.md), vaše hodnoty by měly být:

    * **Název účtu úložiště**: název účtu úložiště, globálně jedinečný název, který si zvolíte pro svůj účet úložiště. V rychlém startu to bylo *arrtutorialstorage*, vaše hodnota se bude lišit.
    * **Název kontejneru objektů BLOB**: Arroutput, kontejner BLOB Storage
    * **Cesta k modelu**: kombinace "outputFolderPath" a "outputAssetFileName" definovaná v *arrconfig.jsv* souboru. V tomto rychlém startu se jednalo o "outputFolderPath": "převedený/robot", "outputAssetFileName": "robot. arrAsset". Výsledkem bude, že hodnota cesty k modelu "Konvertovaný/robot/robot. arrAsset", vaše hodnota se bude lišit.

    >[!TIP]
    > Pokud [spustíte skript **Conversion.ps1**](../../../quickstarts/convert-model.md#run-the-conversion) bez argumentu "-UseContainerSas", skript vypíše všechny výše uvedené hodnoty pro místo tokenu SAS. ![Propojený model](./media/converted-output.png)
1. V době, kdy je to potřeba, odeberte nebo zakažte GameObject **TestModel**, abyste uvolnili prostor pro načtení vlastního modelu.
1. Nahrajte scénu a připojte se ke vzdálené relaci.
1. Klikněte pravým tlačítkem na **RemoteRenderingCoordinator** a vyberte **načíst propojený vlastní model**.
    ![Načíst propojený model](./media/load-linked-model.png)

Tyto kroky zvýšily zabezpečení aplikace odebráním tokenu SAS z místní aplikace.

Aktuální stav aplikace a její přístup k prostředkům Azure teď vypadá takto:

![Lepší zabezpečení](./media/security-two.png)

Pro odebrání z místní aplikace máme ještě jedno "heslo", AccountKey. To se dá udělat pomocí ověřování Azure Active Directory (AAD).

## <a name="azure-active-directory-azure-ad-authentication"></a>Ověřování Azure Active Directory (Azure AD)

Ověřování AAD vám umožní lépe řízený způsob, jakým jednotlivci nebo skupiny používají ARR. ŠIPKA na základě této vlastnosti podporovala příjem [přístupových tokenů](../../../../active-directory/develop/access-tokens.md) namísto použití klíče účtu. Přístupové tokeny si můžete představit jako uživatelsky omezený klíč specifický uživatelem, který pouze odemkne určité části konkrétního prostředku, pro který byl vyžádán.

Skript **RemoteRenderingCoordinator** má delegáta s názvem **ARRCredentialGetter**, který obsahuje metodu, která vrací objekt **SessionConfiguration** , který se používá ke konfiguraci vzdálené správy relací. K **ARRCredentialGetter** můžeme přiřadit jinou metodu. díky tomu můžeme použít tok přihlášení Azure, který vygeneruje objekt **SessionConfiguration** , který obsahuje přístupový token Azure. Tento přístupový token bude specifický pro uživatele, který se přihlašuje.

1. Postupujte podle pokynů v tématu [Postupy: Konfigurace ověřování pro nasazené aplikace](../../../how-tos/authentication.md#authentication-for-deployed-applications), konkrétně budete postupovat podle pokynů uvedených v dokumentaci k Azure AD anchorch kotev v dokumentaci k [ověřování uživatelů Azure AD](../../../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-user-authentication). Zahrnuje registraci nové aplikace Azure Active Directory a konfiguraci přístupu k instanci ARR.
1. Po nakonfigurování nové aplikace AAD ověřte, že vaše aplikace AAD vypadá jako na následujících obrázcích:

    **Aplikace AAD – ověřování >** ![ Ověřování aplikací](./media/app-authentication-public.png)

    **Aplikace AAD – > oprávnění API** ![ Rozhraní API pro aplikace](./media/request-api-permissions-step-five.png)

1. Po nakonfigurování účtu vzdáleného vykreslování ověřte, že vaše konfigurace vypadá jako na následujícím obrázku:

    **AAR-> AccessControl (IAM)** ![ Role ARR](./media/azure-remote-rendering-role-assignment-complete.png)

    >[!NOTE]
    > Role *vlastníka* není dostatečná ke správě relací prostřednictvím klientské aplikace. Pro každého uživatele, kterému chcete udělit možnost Spravovat relace, musíte poskytnout **klienta vzdáleného vykreslování** role. Pro každého uživatele, který chcete spravovat relace a převod modelů, je nutné zadat **Správce vzdáleného vykreslování** role.

Když je služba Azure na místě, je teď potřeba změnit způsob připojení kódu ke službě AAR. Provedeme to implementací instance **BaseARRAuthentication**, která vrátí nový objekt **SessionConfiguration** . V takovém případě se informace o účtu nakonfigurují pomocí přístupového tokenu Azure.

1. Vytvořte nový skript s názvem **AADAuthentication** a nahraďte jeho kód následujícím kódem:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.
    
    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Identity.Client;
    using System;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using UnityEngine;
    
    public class AADAuthentication : BaseARRAuthentication
    {
        [SerializeField]
        private string activeDirectoryApplicationClientID;
        public string ActiveDirectoryApplicationClientID
        {
            get => activeDirectoryApplicationClientID.Trim();
            set => activeDirectoryApplicationClientID = value;
        }
    
        [SerializeField]
        private string azureTenantID;
        public string AzureTenantID
        {
            get => azureTenantID.Trim();
            set => azureTenantID = value;
        }
    
        [SerializeField]
        private string azureRemoteRenderingDomain;
        public string AzureRemoteRenderingDomain
        {
            get => azureRemoteRenderingDomain.Trim();
            set => azureRemoteRenderingDomain = value;
        }
    
        [SerializeField]
        private string azureRemoteRenderingAccountID;
        public string AzureRemoteRenderingAccountID
        {
            get => azureRemoteRenderingAccountID.Trim();
            set => azureRemoteRenderingAccountID = value;
        }
    
        [SerializeField]
        private string azureRemoteRenderingAccountDomain;
        public string AzureRemoteRenderingAccountDomain
        {
            get => azureRemoteRenderingAccountDomain.Trim();
            set => azureRemoteRenderingAccountDomain = value;
        }    
    
        public override event Action<string> AuthenticationInstructions;
    
        string authority => "https://login.microsoftonline.com/" + AzureTenantID;
    
        string redirect_uri = "https://login.microsoftonline.com/common/oauth2/nativeclient";
    
        string[] scopes => new string[] { "https://sts." + AzureRemoteRenderingAccountDomain + "/mixedreality.signin" };
    
        public void OnEnable()
        {
            RemoteRenderingCoordinator.ARRCredentialGetter = GetAARCredentials;
            this.gameObject.AddComponent<ExecuteOnUnityThread>();
        }
    
        public async override Task<SessionConfiguration> GetAARCredentials()
        {
            var result = await TryLogin();
            if (result != null)
            {
                Debug.Log("Account signin successful " + result.Account.Username);
    
                var AD_Token = result.AccessToken;
    
                return await Task.FromResult(new SessionConfiguration(AzureRemoteRenderingAccountDomain, AzureRemoteRenderingDomain, AzureRemoteRenderingAccountID, "", AD_Token, ""));
            }
            else
            {
                Debug.LogError("Error logging in");
            }
            return default;
        }
    
        private Task DeviceCodeReturned(DeviceCodeResult deviceCodeDetails)
        {
            //Since everything in this task can happen on a different thread, invoke responses on the main Unity thread
            ExecuteOnUnityThread.Enqueue(() =>
            {
                // Display instructions to the user for how to authenticate in the browser
                Debug.Log(deviceCodeDetails.Message);
                AuthenticationInstructions?.Invoke(deviceCodeDetails.Message);
            });
    
            return Task.FromResult(0);
        }
    
        public override async Task<AuthenticationResult> TryLogin()
        {
            var clientApplication = PublicClientApplicationBuilder.Create(ActiveDirectoryApplicationClientID).WithAuthority(authority).WithRedirectUri(redirect_uri).Build();
            AuthenticationResult result = null;
            try
            {
                var accounts = await clientApplication.GetAccountsAsync();
    
                if (accounts.Any())
                {
                    result = await clientApplication.AcquireTokenSilent(scopes, accounts.First()).ExecuteAsync();
    
                    return result;
                }
                else
                {
                    try
                    {
                        result = await clientApplication.AcquireTokenWithDeviceCode(scopes, DeviceCodeReturned).ExecuteAsync(CancellationToken.None);
                        return result;
                    }
                    catch (MsalUiRequiredException ex)
                    {
                        Debug.LogError("MsalUiRequiredException");
                        Debug.LogException(ex);
                    }
                    catch (MsalServiceException ex)
                    {
                        Debug.LogError("MsalServiceException");
                        Debug.LogException(ex);
                    }
                    catch (MsalClientException ex)
                    {
                        Debug.LogError("MsalClientException");
                        Debug.LogException(ex);
                        // Mitigation: Use interactive authentication
                    }
                    catch (Exception ex)
                    {
                        Debug.LogError("Exception");
                        Debug.LogException(ex);
                    }
                }
            }
            catch (Exception ex)
            {
                Debug.LogError("GetAccountsAsync");
                Debug.LogException(ex);
            }
    
            return null;
        }
    }
    ```

>[!NOTE]
> Tento kód není úplný a není připravený pro komerční aplikaci. Například minimálně budete pravděpodobně chtít přidat možnost Odhlásit se. To lze provést pomocí `Task RemoveAsync(IAccount account)` metody poskytované klientskou aplikací. Tento kód je určený jenom pro použití v kurzu, vaše implementace bude specifická pro vaši aplikaci.

Kód se nejprve pokusí získat token tiše pomocí **AquireTokenSilent**. Pokud uživatel tuto aplikaci dřív ověřil, bude tato akce úspěšná. Pokud to neproběhne úspěšně, přejděte k další strategii, kterou uživatel zahrnoval.

Pro tento kód používáme [tok kódu zařízení](../../../../active-directory/develop/v2-oauth2-device-code.md) k získání přístupového tokenu. Tento tok umožňuje uživateli přihlásit se ke svému účtu Azure na počítači nebo mobilním zařízení a nechat si výsledný token odeslat zpátky do aplikace HoloLens.

Nejdůležitější část této třídy z perspektivy ARR je tento řádek:

```cs
return await Task.FromResult(new SessionConfiguration(AzureRemoteRenderingAccountDomain, AzureRemoteRenderingDomain, AzureRemoteRenderingAccountID, "", AD_Token, ""));
```

Tady vytvoříte nový objekt **SessionConfiguration** pomocí domény vzdáleného vykreslování, ID účtu, domény účtu a přístupového tokenu. Tento token je pak používán službou ARR k dotazování, vytvoření a připojení vzdálených relací vykreslování, pokud je uživatel autorizován na základě oprávnění na základě rolí nakonfigurovaných dříve.

V důsledku této změny bude aktuální stav aplikace a její přístup k prostředkům Azure vypadat takto:

![Ještě lepší zabezpečení](./media/security-three.png)

Vzhledem k tomu, že přihlašovací údaje uživatele nejsou uložené v zařízení (nebo v tomto případě i v zařízení), je riziko jejich ohrožení velmi nízké. Zařízení teď pro přístup k druhé straně používá uživatelský a omezený přístupový token, který pro přístup k Blob Storage používá řízení přístupu (IAM). Tyto dva kroky úplně odebraly "hesla" ze zdrojového kódu a významně zvyšují zabezpečení. Nejedná se ale o nejpravděpodobnější zabezpečení, takže přesunutí modelu a správy relace do webové služby zvýší zabezpečení. Další požadavky na zabezpečení jsou popsány v části [komerční připravenost](../commercial-ready/commercial-ready.md) .

### <a name="testing-aad-auth"></a>Testování ověřování AAD

Pokud je v editoru Unity aktivní ověřování AAD, budete se muset ověřit při každém spuštění aplikace. Na zařízení se spustí krok ověření poprvé a bude se vyžadovat jeho opětovné schválení, nebo zrušení platnosti tokenu.

1. Přidejte komponentu **AADAuthentication** do **RemoteRenderingCoordinator** GameObject.

    ![Součást ověřování AAD](./media/azure-active-directory-auth-component.png)

1. Zadejte hodnoty pro ID klienta a ID tenanta. Tyto hodnoty najdete na stránce Přehled registrace vaší aplikace:

    * **ID klienta aplikace služby Active Directory** je *ID aplikace (klienta)* nalezené v registraci aplikace AAD (viz obrázek níže).
    * **ID tenanta Azure** je *ID adresáře (tenant)* , které najdete v registraci aplikace AAD (viz obrázek níže).
    * **Doména vzdáleného vykreslování Azure** je stejná jako doména, kterou jste používali v doméně vzdáleného vykreslování **RemoteRenderingCoordinator**.
    * **ID účtu vzdáleného vykreslování Azure** je stejné **ID účtu** , které jste používali pro **RemoteRenderingCoordinator**.
    * **Doména účtu vzdáleného vykreslování Azure** je stejná **Doména účtu** , kterou jste používali v **RemoteRenderingCoordinator**.

    ![Snímek obrazovky, který zvýrazňuje ID aplikace (klienta) a ID adresáře (tenanta).](./media/app-overview-data.png)

1. Stiskněte Přehrát v editoru Unity a vyjádření souhlasu s spuštěním relace.
    Vzhledem k tomu, že komponenta **AADAuthentication** má kontroler zobrazení, automaticky se připojovat k zobrazení výzvy po modálním panelu autorizace relace.
1. Postupujte podle pokynů uvedených na panelu napravo od **AppMenu**.
    Mělo by se zobrazit něco podobného jako v tomto ![ příkladu, který zobrazuje panel instrukcí, který se zobrazí napravo od AppMenu.](./media/device-flow-instructions.png)
    
    Po zadání poskytnutého kódu na sekundárním zařízení (nebo v prohlížeči na stejném zařízení) a přihlášení pomocí přihlašovacích údajů se přístupový token vrátí do žádající aplikace, v tomto případě v editoru Unity.

Po tomto okamžiku by všechno v aplikaci mělo normálně pokračovat. Pokud nebudete postupovat podle očekávaných fází, podívejte se na konzolu Unity s případnými chybami.

## <a name="build-to-device"></a>Sestavit do zařízení

Pokud vytváříte aplikaci s použitím MSAL k zařízení, budete muset zahrnout soubor do složky **assets** projektu. To pomůže kompilátoru sestavit aplikaci správně pomocí *Microsoft.Identity.Client.dll* zahrnutých v **výukovém** programu.

1. Přidat nový soubor do **assetů** s názvem **link.xml**
1. Do souboru přidejte následující:

    ```xml
    <linker>
        <assembly fullname="Microsoft.Identity.Client" preserve="all"/>
        <assembly fullname="System.Runtime.Serialization" preserve="all"/>
        <assembly fullname="System.Core">
            <type fullname="System.Linq.Expressions.Interpreter.LightLambda" preserve="all" />
        </assembly>
    </linker>
    ```

1. Uložte změny.

Postupujte podle kroků v části [rychlý Start: nasazení ukázky Unity do HoloLens – Sestavte vzorový projekt](../../../quickstarts/deploy-to-hololens.md#build-the-sample-project)pro sestavení na HoloLens.

## <a name="next-steps"></a>Další kroky

Zbývající část této sady kurzů obsahuje koncepční témata pro vytvoření aplikace připravené pro produkční prostředí, která používá vzdálené vykreslování Azure.

> [!div class="nextstepaction"]
> [Další: komerční připravenost](../commercial-ready/commercial-ready.md)