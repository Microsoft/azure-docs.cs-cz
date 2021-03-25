---
title: Zobrazení vzdáleně vykresleného modelu
description: Kurz "Hello World vzdáleného vykreslování Azure" ukazuje, jak zobrazit model vygenerovaný vzdáleně pomocí Azure
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: dd3596dd7b2cd01e80d9cf4db9bbc179048e7fcf
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105035961"
---
# <a name="tutorial-viewing-a-remotely-rendered-model"></a>Kurz: zobrazení vzdáleného vykresleného modelu

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Zřízení instance služby Azure Remote rendering (ARR)
> * Vytvoření a zastavení relace vykreslování
> * Znovu použít existující relaci vykreslování
> * Připojení a odpojení od relací
> * Načtení modelů do relace vykreslování

## <a name="prerequisites"></a>Požadavky

Pro tento kurz potřebujete:

* Aktivní předplatné Azure s průběžnými platbami podle aktuálního využití pro [vytváření účtů](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)
* Windows SDK 10.0.18362.0 [(Stáhnout)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* Nejnovější verzi sady Visual Studio 2019 [(Stáhnout)](https://visualstudio.microsoft.com/vs/older-downloads/)
* GIT [(Stáhnout)](https://git-scm.com/downloads)
* Unity (viz [požadavky na systém](../../../overview/system-requirements.md#unity) pro podporované verze)
* Mezilehlé znalosti Unity a jazyka C# (například vytváření skriptů a objektů, použití prefabs, konfigurace událostí Unity atd.)

## <a name="provision-an-azure-remote-rendering-arr-instance"></a>Zřízení instance služby Azure Remote rendering (ARR)

Pokud chcete získat přístup ke službě vzdáleného vykreslování Azure, musíte nejdřív [vytvořit účet](../../../how-tos/create-an-account.md#create-an-account).

## <a name="create-a-new-unity-project"></a>Vytvoření nového projektu Unity

> [!TIP]
> [Úložiště ukázek ARR](https://github.com/Azure/azure-remote-rendering) obsahuje projekt se všemi dokončenými kurzy, který je možné použít jako referenci. Prohlédněte si *Unity\Tutorial-Complete* pro úplný projekt Unity.

V centru Unity vytvořte nový projekt.
V tomto příkladu budeme předpokládat, že se projekt vytváří ve složce s názvem **RemoteRendering**.

:::image type="content" source="./media/unity-new-project.PNG" alt-text="Nový projekt Unity":::

## <a name="include-the-azure-remote-rendering-package"></a>Zahrnutí balíčku vzdáleného vykreslování Azure

[Postupujte podle pokynů](../../../how-tos/unity/install-remote-rendering-unity-package.md) , jak přidat balíček vzdáleného vykreslování Azure do projektu Unity.


## <a name="configure-the-camera"></a>Konfigurace kamery

1. Vyberte **hlavní uzel kamery** .

1. Kliknutím pravým tlačítkem na součást *transformace* otevřete místní nabídku a vyberte možnost **resetovat** :

    ![resetovat transformaci kamery](./media/camera-reset-transform.png)

1. Nastavit **clear Flags** na *plnou barvu*

1. Nastavit **pozadí** na *černou* (#000000) s plně transparentní (0) alfa (A)

    ![Barevné kolo](./media/color-wheel-black.png)

1. Nastavte **ořezové roviny** na *blízko = 0,3* a *daleko = 20*. To znamená, že vykreslování bude oříznout geometrii, která je větší než 30 cm nebo větší než 20 metrů.

    ![Vlastnosti kamery Unity](./media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>Úprava nastavení projektu

1. Otevřít *úpravy > nastavení projektu...*
1. Vybrat **kvalitu** z nabídky vlevo v seznamu
1. Změňte **výchozí úroveň kvality** všech platforem na *hodnotu Nízká*. Toto nastavení umožní efektivnější vykreslování místního obsahu a nebude mít vliv na kvalitu vzdáleného vykresleného obsahu.

    ![změnit nastavení kvality projektu](./media/settings-quality.png)

1. Výběr **grafiky** z nabídky vlevo v seznamu
1. Změňte nastavení **kanálu vykreslování pomocí skriptů** na *HybridRenderingPipeline*. \
    ![Snímek obrazovky, který ukazuje, kde změníte nastavení kanálu vykreslování pomocí skriptů na HybridRenderingPipeline.](./media/settings-graphics-render-pipeline.png)\
    V některých případech uživatelské rozhraní nenačítá seznam dostupných typů kanálů z balíčků. Pokud k tomu dojde, musí se prostředek *HybridRenderingPipeline* do pole přetáhnout ručně: \
    ![Změna nastavení grafiky projektu](./media/hybrid-rendering-pipeline.png)

    > [!NOTE]
    > Pokud nemůžete přetáhnout Asset *HybridRenderingPipeline* do pole Asset kanálu vykreslování (pravděpodobně proto, že pole neexistuje!), ujistěte se, že konfigurace balíčku obsahuje `com.unity.render-pipelines.universal` balíček.

1. V nabídce vlevo v seznamu vyberte **přehrávač** .
1. Vyberte kartu **nastavení Univerzální platforma Windows** reprezentovanou jako ikona Windows.
1. Změňte **Nastavení XR** tak, aby podporovalo Windows Mixed reality, jak je znázorněno níže:
    1. Povolit **podporu virtuální realitu**
    1. Stiskněte tlačítko + a přidejte **Windows Mixed reality** .
    1. Nastavit **Formát hloubky** na *16bitovou hloubku*
    1. Zajistěte, aby bylo zapnuté **sdílení vyrovnávací paměti** .
    1. Nastavit **režim vykreslování stereo** na *instanci Single Passed*

    ![nastavení přehrávače](./media/xr-player-settings.png)

1. Ve stejném okně, nad **XR nastavení**, rozbalte **Nastavení publikování** .
1. Přejděte dolů na **Možnosti** a vyberte:
    * **InternetClient**
    * **InternetClientServer**
    * **SpatialPerception**
    * **PrivateNetworkClientServer** (*volitelné*). Tuto možnost vyberte, pokud chcete ke svému zařízení připojit vzdálený ladicí program Unity.

1. V části **podporované rodiny zařízení** povolit **holografické** a **desktopové**
1. Zavřít nebo ukotvit panel **nastavení projektu**
1. Otevřít *soubor->nastavení sestavení*
1. Vyberte **Univerzální platforma Windows**
1. Nakonfigurujte nastavení tak, aby odpovídalo následujícím funkcím.
1. Stiskněte tlačítko **platformy přepínače** . \
![nastavení sestavení](./media/build-settings.png)
1. Po změně platforem v Unity zavřete panel sestavení.

## <a name="validate-project-setup"></a>Ověřit nastavení projektu

Provedením následujících kroků ověříte, zda je nastavení projektu správné.

1. V nabídce **RemoteRendering** na panelu nástrojů editoru Unity vyberte položku **ValidateProject** .
1. V okně **ValidateProject** Zkontrolujte chyby a opravte nastavení projektu tam, kde je to potřeba.

    ![Ověření projektu editoru Unity](./media/remote-render-unity-validation.png)

## <a name="create-a-script-to-coordinate-azure-remote-rendering-connection-and-state"></a>Vytvoření skriptu pro koordinaci připojení a stavu vzdáleného vykreslování Azure

Existují čtyři základní fáze pro zobrazení vzdáleně vygenerovaných modelů, které jsou popsány v následujícím vývojovém diagramu. Každá fáze musí být provedena v daném pořadí. Dalším krokem je vytvoření skriptu, který bude spravovat stav aplikace a bude pokračovat v každé požadované fázi.

![Zásobník ARR 0](./media/remote-render-stack-0.png)

1. V podokně *projekt* v části **assety** vytvořte novou složku s názvem *RemoteRenderingCore*. Pak uvnitř *RemoteRenderingCore* vytvořte další složku s názvem *skripty*.

1. Vytvořte [Nový skript jazyka C#](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) s názvem **RemoteRenderingCoordinator**.
Váš projekt by měl vypadat takto:

    ![Hierarchie projektu](./media/project-structure.png)

    Tento skript koordinátora bude sledovat a spravovat stav vzdáleného vykreslování. Poznámka: některé z těchto kódů se používají pro zachování stavu, vystavení funkcí jiným komponentám, aktivaci událostí a ukládání dat specifických pro aplikaci, která *přímo* nesouvisejí se vzdáleným vykreslováním Azure. Jako výchozí bod použijte kód uvedený níže a na později v tomto kurzu budeme implementovat konkrétní kód vzdáleného vykreslování Azure.

1. Otevřete **RemoteRenderingCoordinator** v editoru kódu a nahraďte jeho celý obsah následujícím kódem:

```cs
// Copyright (c) Microsoft Corporation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System;
using System.Linq;
using System.Threading.Tasks;
using UnityEngine;
using UnityEngine.Events;

#if UNITY_WSA
using UnityEngine.XR.WSA;
#endif

/// <summary>
/// Remote Rendering Coordinator is the controller for all Remote Rendering operations.
/// </summary>

// Require the GameObject with a RemoteRenderingCoordinator to also have the ARRServiceUnity component
[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRenderingCoordinator : MonoBehaviour
{
    public enum RemoteRenderingState
    {
        NotSet,
        NotInitialized,
        NotAuthorized,
        NoSession,
        ConnectingToExistingRemoteSession,
        ConnectingToNewRemoteSession,
        RemoteSessionReady,
        ConnectingToRuntime,
        RuntimeConnected
    }

    public static RemoteRenderingCoordinator instance;

    // AccountDomain must be '<region>.mixedreality.azure.com' - if no '<region>' is specified, connections will fail
    // The list of regions is available at https://docs.microsoft.com/azure/remote-rendering/reference/regions
    [SerializeField]
    private string accountDomain = "westus2.mixedreality.azure.com";
    public string AccountDomain
    {
        get => accountDomain.Trim();
        set => accountDomain = value;
    }

    [Header("Development Account Credentials")]
    [SerializeField]
    private string accountId = "<enter your account id here>";
    public string AccountId {
        get => accountId.Trim();
        set => accountId = value;
    }

    [SerializeField]
    private string accountAuthenticationDomain = "<enter your account authentication domain here>";
    public string AccountAuthenticationDomain
    {
        get => accountAuthenticationDomain.Trim();
        set => accountAuthenticationDomain = value;
    }   

    [SerializeField]
    private string accountKey = "<enter your account key here>";
    public string AccountKey {
        get => accountKey.Trim();
        set => accountKey = value;
    }

    // These settings are important. All three should be set as low as possible, while maintaining a good user experience
    // See the documentation around session management and the technical differences in session VM size
    [Header("New Session Defaults")]

    public RenderingSessionVmSize renderingSessionVmSize = RenderingSessionVmSize.Standard;
    public uint maxLeaseHours = 0;
    public uint maxLeaseMinutes = 20;

    [Header("Other Configuration")]

    [Tooltip("If you have a known active SessionID, you can fill it in here before connecting")]
    [SerializeField]
    private string sessionIDOverride;
    public string SessionIDOverride {
        get => sessionIDOverride.Trim();
        set => sessionIDOverride = value;
    }

    // When Automatic Mode is true, the coordinator will attempt to automatically proceed through the process of connecting and loading a model
    public bool automaticMode = true;

    public event Action RequestingAuthorization;
    public UnityEvent OnRequestingAuthorization = new UnityEvent();

    public event Action AuthorizedChanged;
    public UnityEvent OnAuthorizationChanged = new UnityEvent();
    private bool authorized;
    public bool Authorized
    {
        get => authorized;
        set
        {
            if (value == true) //This is a one-way value, once we're authorized it lasts until the app is shutdown.
            {
                authorized = value;
                AuthorizedChanged?.Invoke();
            }
        }
    }

    public delegate Task<SessionConfiguration> AccountInfoGetter();

    public static AccountInfoGetter ARRCredentialGetter
    {
        private get;
        set;
    }

    private RemoteRenderingState currentCoordinatorState = RemoteRenderingState.NotSet;
    public RemoteRenderingState CurrentCoordinatorState
    {
        get => currentCoordinatorState;
        private set
        {
            if (currentCoordinatorState != value)
            {
                currentCoordinatorState = value;
                Debug.Log($"State changed to: {currentCoordinatorState}");
                CoordinatorStateChange?.Invoke(currentCoordinatorState);
            }
        }
    }

    public static event Action<RemoteRenderingState> CoordinatorStateChange;

    public static RenderingSession CurrentSession => instance?.ARRSessionService?.CurrentActiveSession;

    private ARRServiceUnity arrSessionService;

    private ARRServiceUnity ARRSessionService
    {
        get
        {
            if (arrSessionService == null)
                arrSessionService = GetComponent<ARRServiceUnity>();
            return arrSessionService;
        }
    }

    private async Task<SessionConfiguration> GetDevelopmentCredentials()
    {
        Debug.LogWarning("Using development credentials! Not recommended for production.");
        return await Task.FromResult(new SessionConfiguration(AccountAuthenticationDomain, AccountDomain, AccountId, AccountKey));
    }

    /// <summary>
    /// Keep the last used SessionID, when launching, connect to this session if its available
    /// </summary>
    private string LastUsedSessionID
    {
        get
        {
            if (!string.IsNullOrEmpty(SessionIDOverride))
                return SessionIDOverride;

            if (PlayerPrefs.HasKey("LastUsedSessionID"))
                return PlayerPrefs.GetString("LastUsedSessionID");
            else
                return null;
        }
        set
        {
            PlayerPrefs.SetString("LastUsedSessionID", value);
        }
    }

    public void Awake()
    {
        //Forward events to Unity events
        RequestingAuthorization += () => OnRequestingAuthorization?.Invoke();
        AuthorizedChanged += () => OnAuthorizationChanged?.Invoke();

        //Attach to event
        AuthorizedChanged += RemoteRenderingCoordinator_AuthorizedChanged;

        if (instance == null)
            instance = this;
        else
            Destroy(this);

        CoordinatorStateChange += AutomaticMode;

        CurrentCoordinatorState = RemoteRenderingState.NotInitialized;
    }

    private void RemoteRenderingCoordinator_AuthorizedChanged()
    {
        if (CurrentCoordinatorState != RemoteRenderingState.NotAuthorized)
            return; //This isn't valid from any other state than NotAuthorized


        //We just became authorized to connect to Azure
        InitializeSessionService();
    }

    /// <summary>
    /// Automatic mode attempts to automatically progress through the connection and loading steps. Doesn't handle error states.
    /// </summary>
    /// <param name="currentState">The current state</param>
    private async void AutomaticMode(RemoteRenderingState currentState)
    {
        if (!automaticMode)
            return;

        //Add a small delay for visual effect
        await Task.Delay(1500);
        switch (currentState)
        {
            case RemoteRenderingState.NotInitialized:
                InitializeARR();
                break;
            case RemoteRenderingState.NotAuthorized:
                RequestAuthorization();
                break;
            case RemoteRenderingState.NoSession:
                JoinRemoteSession();
                break;
            case RemoteRenderingState.RemoteSessionReady:
                ConnectRuntimeToRemoteSession();
                break;
        }
    }

    /// <summary>
    /// Initializes ARR, associating the main camera
    /// Note: This must be called on the main Unity thread
    /// </summary>
    public void InitializeARR()
    {
        //Implement me
    }

    /// <summary>
    /// Create a new remote session manager
    /// If the ARRCredentialGetter is set, use it as it, otherwise use the development credentials 
    /// </summary>
    public async void InitializeSessionService()
    {
        //Implement me
    }

    /// <summary>
    /// Trigger the event for checking authorization, respond to this event by prompting the user for authentication
    /// If authorized, set Authorized = true
    /// </summary>
    public void RequestAuthorization()
    {
        RequestingAuthorization?.Invoke();
    }

    public void BypassAuthorization()
    {
        Authorized = true;
    }

    /// <summary>
    /// Attempts to join an existing session or start a new session
    /// </summary>
    public async void JoinRemoteSession()
    {
        //Implement me
    }

    public void StopRemoteSession()
    {
        //Implement me
    }

    private async Task<bool> IsSessionAvailable(string sessionID)
    {
        var allSessions = await ARRSessionService.Client.GetCurrentRenderingSessionsAsync();
        return allSessions.SessionProperties.Any(x => x.Id == sessionID && (x.Status == RenderingSessionStatus.Ready || x.Status == RenderingSessionStatus.Starting));
    }

    /// <summary>
    /// Connects the local runtime to the current active session, if there's a session available
    /// </summary>
    public void ConnectRuntimeToRemoteSession()
    {
        //Implement me
    }

    public void DisconnectRuntimeFromRemoteSession()
    {
        //Implement me
    }

    /// <summary>
    /// The session must have its runtime pump updated.
    /// The Connection.Update() will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
    /// </summary>
    private void LateUpdate()
    {
        ARRSessionService?.CurrentActiveSession?.Connection?.Update();
    }

    /// <summary>
    /// Loads a model into the remote session for rendering
    /// </summary>
    /// <param name="modelPath">The model's path</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <returns>An awaitable Remote Rendering Entity</returns>
    public async Task<Entity> LoadModel(string modelPath, Transform parent = null, Action<float> progress = null)
    {
        //Implement me
        return null;
    }

    private async void OnRemoteSessionStatusChanged(ARRServiceUnity caller, RenderingSession session)
    {
        var properties = await session.GetPropertiesAsync();

        switch (properties.SessionProperties.Status)
        {
            case RenderingSessionStatus.Error:
            case RenderingSessionStatus.Expired:
            case RenderingSessionStatus.Stopped:
            case RenderingSessionStatus.Unknown:
                CurrentCoordinatorState = RemoteRenderingState.NoSession;
                break;
            case RenderingSessionStatus.Starting:
                CurrentCoordinatorState = RemoteRenderingState.ConnectingToNewRemoteSession;
                break;
            case RenderingSessionStatus.Ready:
                CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
                break;
        }
    }

    private void OnLocalRuntimeStatusChanged(ConnectionStatus status, Result error)
    {
        switch (status)
        {
            case ConnectionStatus.Connected:
                CurrentCoordinatorState = RemoteRenderingState.RuntimeConnected;
                break;
            case ConnectionStatus.Connecting:
                CurrentCoordinatorState = RemoteRenderingState.ConnectingToRuntime;
                break;
            case ConnectionStatus.Disconnected:
                CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
                break;
        }
    }
}
```

## <a name="create-the-azure-remote-rendering-gameobject"></a>Vytvoření GameObject vzdáleného vykreslování Azure

Koordinátor vzdáleného vykreslování a jeho požadovaný skript (*ARRServiceUnity*) jsou třídy MonoBehaviour, které musí být připojeny k GameObject na scéně. *ARRServiceUnity* skript je poskytován pomocí funkce ARR a zpřístupňuje většinu funkcí ARR pro připojení ke vzdáleným relacím a jejich správu.

1. Vytvořte ve scéně novou GameObject (Ctrl + Shift + N nebo *GameObject->vytvořit prázdné*) a pojmenujte ji **RemoteRenderingCoordinator**.
1. Přidejte skript *RemoteRenderingCoordinator* do **RemoteRenderingCoordinator** GameObject. \
![Přidat komponentu RemoteRenderingCoordinator](./media/add-coordinator-script.png)
1. Potvrďte, že se skript *ARRServiceUnity* , který se zobrazuje jako *Služba* v inspektoru, automaticky přidá do GameObject. V případě, že se zajímáte, je to výsledek, který se nachází `[RequireComponent(typeof(ARRServiceUnity))]` v horní části **RemoteRenderingCoordinator** skriptu.
1. Přidejte přihlašovací údaje pro vzdálené vykreslování Azure, doménu ověřování účtu a doménu účtu do skriptu koordinátora: \
![Přidání přihlašovacích údajů](./media/configure-coordinator-script.png)

## <a name="initialize-azure-remote-rendering"></a>Inicializovat vzdálené vykreslování Azure

Teď, když máme rozhraní pro náš koordinátor, budeme implementovat každou ze čtyř fází počínaje **inicializací vzdáleného vykreslování**.

![1. zásobník pro ARR](./media/remote-render-stack-1.png)

**Inicializace** oznamuje vzdálené vykreslování Azure, který objekt Camera použije pro vykreslování a průběh stavového počítače do **NotAuthorized**. To znamená, že se inicializuje, ale ještě nemáte autorizaci pro připojení k relaci. Vzhledem k tomu, že spuštění relace ARR vede k nákladům, musíme potvrdit, že uživatel chce pokračovat.

Při zadávání stavu **NotAuthorized** se volá **CheckAuthorization** , který vyvolá událost **RequestingAuthorization** a určuje, které přihlašovací údaje účtu se mají použít (**AccountInfo** je definována v horní části třídy a používá přihlašovací údaje, které jste definovali v rámci kontroly Unity v kroku výše).

   > [!NOTE]
   > Opětovná kompilace modulu runtime není u ARR podporována. Úprava skriptu a jeho uložení v době, kdy je aktivní režim přehrávání, může způsobit zamrznutí Unity a nutnost vynutit vypnutí prostřednictvím Správce úloh. Před úpravou skriptů vždy zajistěte, abyste zastavili režim přehrávání.

1. Nahraďte obsah hodnot **InitializeARR** a **InitializeSessionService** následujícím kódem:

 ```cs
/// <summary>
/// Initializes ARR, associating the main camera
/// Note: This must be called on the main Unity thread
/// </summary>
public void InitializeARR()
{
    RemoteManagerUnity.InitializeManager(new RemoteUnityClientInit(Camera.main));

    CurrentCoordinatorState = RemoteRenderingState.NotAuthorized;
}

/// <summary>
/// Create a new remote session manager
/// If the ARRCredentialGetter is set, use it as it, otherwise use the development credentials 
/// </summary>
public async void InitializeSessionService()
{
    if (ARRCredentialGetter == null)
        ARRCredentialGetter = GetDevelopmentCredentials;

    var accountInfo = await ARRCredentialGetter.Invoke();

    ARRSessionService.OnSessionStatusChanged += OnRemoteSessionStatusChanged;

    ARRSessionService.Initialize(accountInfo);

    CurrentCoordinatorState = RemoteRenderingState.NoSession;
}
```

Aby bylo možné postupovat od **NotAuthorized** po **relaci**, obvykle k uživateli prezentuje modální dialog, aby si ho mohli vybrat (a my to budeme dělat jenom v jiné kapitole). Prozatím automaticky obejít kontrolu autorizace voláním **ByPassAuthentication** , jakmile se aktivuje událost **RequestingAuthorization** .

1. Vyberte **RemoteRenderingCoordinator** GameObject a vyhledejte událost Unity **OnRequestingAuthorization** v Inspektoru komponenty **RemoteRenderingCoordinator** .

1. Kliknutím na + v pravém dolním rohu přidejte novou událost.
1. Přetáhněte komponentu na vlastní událost, aby odkazovala na sebe sama. \
![Obejít ověřování](./media/bypass-authorization-add-event.png)\
1. V rozevíracím seznamu vyberte **RemoteRenderingCoordinator-> BypassAuthorization**. \
![Snímek obrazovky zobrazující vybranou možnost RemoteRenderingCoordinator. BypassAuthorization](./media/bypass-authorization-event.png)

## <a name="create-or-join-a-remote-session"></a>Vytvoření nebo připojení vzdálené relace

Druhá fáze je vytvořit nebo připojit relaci vzdáleného vykreslování (Další informace najdete v tématu [relace vzdáleného vykreslování](../../../concepts/sessions.md) ).

![Zásobník ARR 2](./media/remote-render-stack-2.png)

Vzdálená relace je místo, kde se budou modely vykreslovat. Metoda **JoinRemoteSession ()** se pokusí připojit k existující relaci, která je sledována pomocí vlastnosti **LastUsedSessionID** , nebo pokud je v **SessionIDOverride** přiřazeno ID aktivní relace. **SessionIDOverride** je určena pouze pro účely ladění a mělo by se používat pouze tehdy, když víte, že relace existuje a chcete se k ní explicitně připojit.

Pokud nejsou k dispozici žádné relace, vytvoří se nová relace. Vytvoření nové relace je však časově náročná operace. Proto byste se měli pokusit vytvořit relace pouze v případě potřeby a znovu je použít (viz [komerční Příprava: sdružování relací, plánování a osvědčené postupy](../commercial-ready/commercial-ready.md#fast-startup-time-strategies) pro další informace o správě relací).

> [!TIP]
> **StopRemoteSession ()** ukončí aktivní relaci. Aby nedocházelo k zbytečným poplatkům, měli byste vždycky zastavit relace, pokud už je nepotřebujete.

Stavový počítač teď bude postupovat buď **ConnectingToNewRemoteSession** nebo **ConnectingToExistingRemoteSession**, v závislosti na dostupných relacích. Otevřením existující relace nebo vytvořením nové relace dojde k aktivaci události **ARRSessionService. OnSessionStatusChanged** , která spouští naši metodu **OnRemoteSessionStatusChanged** . V ideálním případě to způsobí posunutí stavového počítače na **RemoteSessionReady**.

1. Chcete-li se připojit k nové relaci, upravte kód tak, aby nahradil metody **JoinRemoteSession ()** a **StopRemoteSession ()** pomocí níže uvedených příkladů:

```cs
/// <summary>
/// Attempts to join an existing session or start a new session
/// </summary>
public async void JoinRemoteSession()
{
    //If there's a session available that previously belonged to us, and it's ready, use it. Otherwise start a new session.
    RenderingSessionProperties joinResult;
    if (await IsSessionAvailable(LastUsedSessionID))
    {
        CurrentCoordinatorState = RemoteRenderingState.ConnectingToExistingRemoteSession;
        joinResult = await ARRSessionService.OpenSession(LastUsedSessionID);
    }
    else
    {
        CurrentCoordinatorState = RemoteRenderingState.ConnectingToNewRemoteSession;
        joinResult = await ARRSessionService.StartSession(new RenderingSessionCreationOptions(renderingSessionVmSize, (int)maxLeaseHours, (int)maxLeaseMinutes));
    }

    if (joinResult.Status == RenderingSessionStatus.Ready || joinResult.Status == RenderingSessionStatus.Starting)
    {
        LastUsedSessionID = joinResult.Id;
    }
    else
    {
        //The session should be ready or starting, if it's not, something went wrong
        await ARRSessionService.StopSession();
        if(LastUsedSessionID == SessionIDOverride)
            SessionIDOverride = "";
        CurrentCoordinatorState = RemoteRenderingState.NoSession;
    }
}

public void StopRemoteSession()
{
    if (ARRSessionService.CurrentActiveSession != null)
    {
        ARRSessionService.CurrentActiveSession.StopAsync();
    }
}
```

Pokud chcete ušetřit čas pomocí opětovného použití relací, nezapomeňte deaktivovat možnost **automaticky zastavit relaci** v součásti *ARRServiceUnity* . Mějte na paměti, že tato akce opustí spuštěné relace, a to i v případě, že k nim nikdo není připojen. Vaše relace může běžet po dobu, po kterou se *MaxLeaseTime* před vypnutím serverem (hodnota *MaxLeaseTime* se dá upravit ve službě Koordinátor vzdáleného vykreslování v části *nové výchozí relace*). Na druhé straně, pokud při odpojení automaticky vypnete každou relaci, budete muset počkat na spuštění nové relace pokaždé, což může být trochu zdlouhavý proces.

> [!NOTE]
> Zastavení relace se projeví okamžitě a nedá se vrátit zpátky. Po zastavení budete muset vytvořit novou relaci se stejnou režií při spuštění.

## <a name="connect-the-local-runtime-to-the-remote-session"></a>Připojit místní modul runtime ke vzdálené relaci

V dalším kroku aplikace potřebuje připojit svůj místní modul runtime ke vzdálené relaci.

![ŠIPKA 1 – zásobník 3](./media/remote-render-stack-3.png)

Aplikace také musí naslouchat událostem souvisejícím s připojením mezi modulem runtime a aktuální relací. Tyto změny stavu jsou zpracovávány v **OnLocalRuntimeStatusChanged**. Tento kód bude náš stav **ConnectingToRuntime**. Po připojení v **OnLocalRuntimeStatusChanged** bude stav přejít na **RuntimeConnected**. Připojení k modulu runtime je poslední stav, ve kterém se koordinátor týká, což znamená, že je aplikace prováděná se všemi běžnými konfiguracemi a je připravená začít pracovat s modelem načítání a vykreslování specifických pro relaci.

 1. Nahraďte metody **ConnectRuntimeToRemoteSession ()** a **DisconnectRuntimeFromRemoteSession ()** s dokončenými verzemi níže.
 1. Je důležité poznamenat **volá** metodu Unity a aktualizovat aktuální aktivní relaci. Tím umožníte, aby aktuální relace odesílala a přijímala zprávy a aktualizovala vyrovnávací paměť snímků pomocí rámců přijatých ze vzdálené relace. Funkčnost pochodu na ni je velmi důležitá.

```cs
/// <summary>
/// Connects the local runtime to the current active session, if there's a session available
/// </summary>
public void ConnectRuntimeToRemoteSession()
{
    if (ARRSessionService == null || ARRSessionService.CurrentActiveSession == null)
    {
        Debug.LogError("Not ready to connect runtime");
        return;
    }

    //Connect the local runtime to the currently connected session
    //This session is set when connecting to a new or existing session

    ARRSessionService.CurrentActiveSession.ConnectionStatusChanged += OnLocalRuntimeStatusChanged;
    ARRSessionService.CurrentActiveSession.ConnectAsync(new RendererInitOptions());
    CurrentCoordinatorState = RemoteRenderingState.ConnectingToRuntime;
}

public void DisconnectRuntimeFromRemoteSession()
{
    if (ARRSessionService == null || ARRSessionService.CurrentActiveSession == null || ARRSessionService.CurrentActiveSession.ConnectionStatus != ConnectionStatus.Connected)
    {
        Debug.LogError("Runtime not connected!");
        return;
    }

    ARRSessionService.CurrentActiveSession.Disconnect();
    ARRSessionService.CurrentActiveSession.ConnectionStatusChanged -= OnLocalRuntimeStatusChanged;
    CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
}

/// <summary>
/// The session must have its runtime pump updated.
/// The Connection.Update() will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
/// </summary>
private void LateUpdate()
{
    ARRSessionService?.CurrentActiveSession?.Connection?.Update();
}
```

> [!NOTE]
> Připojení místního modulu runtime ke vzdálené relaci závisí na tom, jaká **aktualizace** je volána v aktuálně aktivní relaci. Pokud zjistíte, že aplikace nikdy nepokračuje po **ConnectingToRuntime** stavu, ujistěte se, že na aktivní relaci budete pravidelně volat **aktualizaci** .

## <a name="load-a-model"></a>Načtení modelu

V případě, že je na místě požadovaná základní, jste připraveni načíst model do vzdálené relace a začít přijímat rámce.

![Diagram znázorňující tok procesu pro přípravu na načtení a zobrazení modelu.](./media/remote-render-stack-4.png)

Metoda **LoadModel** je navržena tak, aby přijímala cestu modelu, obslužnou rutinu průběhu a nadřazenou transformaci. Tyto argumenty budou použity k načtení modelu do vzdálené relace, aktualizaci uživatele při načítání a orientaci vzdáleně vykresleného modelu na základě nadřazené transformace.

1. Nahraďte metodu **LoadModel** zcela následujícím kódem:

    ```cs
    /// <summary>
    /// Loads a model into the remote session for rendering
    /// </summary>
    /// <param name="modelName">The model's path</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <returns>An awaitable Remote Rendering Entity</returns>
    public async Task<Entity> LoadModel(string modelPath, Transform parent = null, Action<float> progress = null)
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
        var loadModelParams = new LoadModelFromSasOptions(modelPath, modelEntity);
        var loadModelAsync = ARRSessionService.CurrentActiveSession.Connection.LoadModelFromSasAsync(loadModelParams, progress);
        var result = await loadModelAsync;
        return modelEntity;
    }
    ```

Výše uvedený kód provádí následující kroky:

1. Vytvořte [vzdálenou entitu](../../../concepts/entities.md).
1. Vytvořte místní GameObject, který bude představovat vzdálenou entitu.
1. Nakonfigurujte místní GameObject tak, aby synchronizoval svůj stav (tj. transformovat) ke vzdálené entitě každý snímek.
1. Nastavte název a přidejte [**WorldAnchor**](https://docs.unity3d.com/ScriptReference/XR.WSA.WorldAnchor.html) pro pomoc stabilizaci.
1. Načte data modelu z Blob Storage do vzdálené entity.
1. Vrátí nadřazenou entitu pro pozdější referenci.

## <a name="view-the-test-model"></a>Zobrazit model testu

Teď máme veškerý kód potřebný k zobrazení vzdáleně vykresleného modelu, ale všechny čtyři z požadovaných fází pro vzdálené vykreslování jsou dokončené. Nyní musíme přidat malý kód pro spuštění procesu načítání modelu.

![Šipka pro ARR 4](./media/remote-render-stack-5.png)

1. Přidejte následující kód do třídy **RemoteRenderingCoordinator** , těsně pod metodou **LoadModel** , je to v pořádku:

    ```cs
    private bool loadingTestModel = false;
    [ContextMenu("Load Test Model")]
    public async void LoadTestModel()
    {
        if(CurrentCoordinatorState != RemoteRenderingState.RuntimeConnected)
        {
            Debug.LogError("Please wait for the runtime to connect before loading the test model. Try again later.");
            return;
        }
        if(loadingTestModel)
        {
            Debug.Log("Test model already loading or loaded!");
            return;
        }
        loadingTestModel = true;
    
        // Create a parent object to use for positioning
        GameObject testParent = new GameObject("TestModelParent");
        testParent.transform.position = new Vector3(0f, 0f, 3f);
    
        // The 'built in engine path' is a special path that references a test model built into Azure Remote Rendering.
        await LoadModel("builtin://Engine", testParent.transform, (progressValue) => Debug.Log($"Loading Test Model progress: {Math.Round(progressValue * 100, 2)}%"));
    }
    ```
    
    Tento kód vytvoří GameObject, který bude sloužit jako nadřazený model testu. Pak zavolá metodu **LoadModel** pro načtení modelu "Builtin://Engine", který je prostředkem integrovaným do vzdáleného vykreslování Azure pro účely testování vykreslování.

1. Uložte kód.
1. Stisknutím tlačítka Přehrát v editoru Unity zahajte proces připojení ke vzdálenému vykreslování Azure a vytvoření nové relace.
1. V zobrazení herních souborů se nezobrazuje moc, ale v konzole se zobrazí stav změny aplikace. Bude pravděpodobně postupovat `ConnectingToNewRemoteSession` a může zůstat až pět minut.
1. Vyberte GameObject **RemoteRenderingCoordinator** , aby se zobrazily připojené skripty v inspektoru. Sledujte aktualizaci komponenty **služby** , protože se její průběh provede inicializací a postupem připojení.
1. Monitorování výstupu konzoly – čeká se, až se stav změní na **RuntimeConnected**.
1. Jakmile je modul runtime připojen, klikněte pravým tlačítkem na **RemoteRenderingCoordinator** v inspektoru a zpřístupněte kontextovou nabídku. Pak klikněte na možnost **model zátěžového testu** v místní nabídce, kterou jste přidali `[ContextMenu("Load Test Model")]` částí našeho kódu výše.

    ![Načíst z kontextové nabídky](./media/load-test-model.png)

1. Podívejte se na konzolu s výstupem **ProgressHandler** , který jsme předali do metody **LoadModel** .
1. Podívejte se na vzdáleně vykreslený model!

> [!NOTE]
> Vzdálený model nebude nikdy viditelný v zobrazení scény, pouze v herním zobrazení. Je to proto, že šipka myši vykresluje snímky vzdáleně konkrétně pro perspektivu kamery herního zobrazení a neví se o tom, jak kamera editoru (používá se k vykreslování zobrazení scény).

## <a name="next-steps"></a>Další kroky

![Model načten](./media/test-model-rendered.png)

Gratulujeme! Vytvořili jste základní aplikaci, která umožňuje zobrazit vzdáleně vykreslené modely pomocí vzdáleného vykreslování Azure. V dalším kurzu budeme integrovat MRTK a importovat naše vlastní modely.

> [!div class="nextstepaction"]
> [Další: rozhraní a vlastní modely](../custom-models/custom-models.md)
