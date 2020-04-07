---
title: Nastavení projektu Unity od nuly
description: Vysvětluje, jak nakonfigurovat prázdný projekt Unity pro použití s Azure Remote Rendering.
author: florianborn71
ms.author: flborn
ms.date: 01/30/2020
ms.topic: tutorial
ms.openlocfilehash: 33801316e4c0446865169560bb42f98052acba70
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679594"
---
# <a name="tutorial-setting-up-a-unity-project-from-scratch"></a>Kurz: Nastavení projektu Unity od nuly

V tomto kurzu se dozvíte:

> [!div class="checklist"]
>
> * Konfigurace scratch Unity projektu pro ARR.
> * Vytváření a zastavení relací vykreslování.
> * Opětovné použití existujících relací.
> * Připojení a odpojení od relací.
> * Načítání modelů do relace vykreslování.
> * Zobrazení statistik připojení.

## <a name="prerequisites"></a>Požadavky

Pro tento výukový program potřebujete:

* Informace o vašem účtu (ID účtu, klíč účtu, ID předplatného). Pokud účet nemáte, [vytvořte si účet](../../how-tos/create-an-account.md).
* Sada Windows SDK 10.0.18362.0 [(stáhnout)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* Nejnovější verze Visual Studia 2019 [(ke stažení)](https://visualstudio.microsoft.com/vs/older-downloads/)
* GIT [(stáhnout)](https://git-scm.com/downloads)
* Unity 2019.3.1 [(ke stažení)](https://unity3d.com/get-unity/download)
  * Nainstalujte tyto moduly v Unity:
    * **UPWP** – podpora sestavení univerzální platformy Windows
    * **IL2CPP** - Podpora sestavení systému Windows (IL2CPP)

> [!TIP]
> [Úložiště vzorků ARR](https://github.com/Azure/azure-remote-rendering) obsahuje připravené projekty Unity pro všechny kurzy. Tyto projekty můžete použít jako odkaz.

## <a name="create-a-new-unity-project"></a>Vytvoření nového projektu Unity

Z Unity Hub vytvořte nový projekt.
V tomto příkladu budeme předpokládat, že projekt `RemoteRendering`je vytvářen ve složce s názvem .

![nové okno projektu](media/new-project.png)

## <a name="configure-the-projects-manifest"></a>Konfigurace manifestu projektu

Je třeba upravit `Packages/manifest.json` soubor, který je umístěn ve složce projektu Unity. Otevřete soubor v textovém editoru a připojit řádky uvedené níže:

```json
{
  "scopedRegistries": [
    {
      "name": "Azure Mixed Reality Services",
      "url": "https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM/",
      "scopes": ["com.microsoft.azure"]
    }
   ],
  "dependencies": {
    "com.microsoft.azure.remote-rendering": "0.1.11",
    "com.unity.render-pipelines.universal": "7.2.1",
    ...existing dependencies...
  }
}
```

Univerzální vykreslovat potrubí balíček je volitelné, ale doporučuje se z důvodů výkonu.
Po úpravě a uložení manifestu se Unity automaticky aktualizuje. Potvrďte, že balíčky byly načteny v okně *Projektu:*

![potvrdit import balíčků](media/confirm-packages.png)

## <a name="ensure-you-have-the-latest-version-of-the-package"></a>Ujistěte se, že máte nejnovější verzi balíčku

Následující kroky zajistí, že váš projekt používá nejnovější verzi balíčku vzdáleného vykreslování.
1. Vyberte balíček v okně Projekt a ![klikněte na ikonu balíčku: Výběr ikony balíčku](media/package-icons.png)
1. V inspektoru klikněte na "Zobrazit ![ve Správci balíčků": inspektor balíčku](media/package-properties.png)
1. Na stránce správce balíčků pro balíček vzdáleného vykreslování zjistěte, zda je k dispozici tlačítko aktualizace. Pokud ano, pak kliknutím na něj aktualizujete balíček na nejnovější dostupnou verzi: ![Balíček ARR ve správci balíčků](media/package-manager.png)
1. Někdy aktualizace balíčku může vést k chybám v konzole. Pokud k tomu dojde, zkuste zavřít a znovu otevřít projekt.

## <a name="configure-the-camera"></a>Konfigurace fotoaparátu

Vyberte uzel **hlavního fotoaparátu.**

1. Obnovit jeho *transformace*:

    ![resetovat transformaci kamery](media/camera-reset-transform.png)

1. Nastavit **příznaky Vymazat** na plná *barva*

1. Nastavit **pozadí** na *černou*

1. Nastavte **ořezové roviny** na *Near = 0,3* a *Daleko = 20*. To znamená, že vykreslování ořízne geometrii, která je blíže než 30 cm nebo dále než 20 metrů.

    ![Vlastnosti kamery Unity](media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>Úprava nastavení projektu

1. Otevřít *upravit > nastavení projektu...*
1. V seznamu vlevo vyberte Kvalita.
1. Změna **výchozí úrovně kvality** na *nízkou*

    ![změna nastavení kvality projektu](media/settings-quality.png)

1. Vlevo vyberte **Grafika.**
1. Změňte nastavení **Skriptovatelného kanálu vykreslování** na *HybridRenderingPipeline*. Tento krok přeskočte, pokud není použit kanál univerzálního vykreslení.

    ![změna nastavení](media/settings-graphics-lwrp.png) grafiky projektu Někdy rozhraní nenaplní seznam dostupných typů kanálu z balíčků, v takovém případě musí být ![prostředek *HybridRenderingPipeline* přetažen do pole ručně: změna nastavení grafiky projektu](media/hybrid-rendering-pipeline.png)
1. Vlevo vyberte **Přehrávač.**
1. Výběr karty **Nastavení univerzální platformy Windows**
1. Změna **nastavení XR** pro podporu ![windows smíšené reality: nastavení přehrávače](media/xr-player-settings.png)
1. Vyberte nastavení jako na obrázku výše:
    1. Podpora **povolení virtuální reality**
    1. Nastavit **formát hloubky** na *16bitovou hloubku*
    1. Povolit **sdílení hloubkové vyrovnávací paměti**
    1. Nastavení **režimu stereofonního vykreslování** na *jednoprůchodovou instanci*

1. Ve stejném okně nad *nastavením XR* **rozbalte položku Nastavení publikování**
1. Posuňte se dolů na **Možnosti** a vyberte:
    * **Internetový klient**
    * **Server InternetClientServer**
    * **Prostorové vnímání**
    * Volitelné pro vývoj: **PrivateNetworkClientServer**

      Tato možnost je potřeba, pokud chcete připojit unity vzdálené ladicí program k zařízení.

1. V **části Podporované rodiny zařízení**povolte **holografické** a **desktopové**

1. Chcete-li použít sadu nástrojů pro smíšenou realitu, přečtěte si [dokumentaci k sadě MRTK](https://docs.microsoft.com/windows/mixed-reality/unity-development-overview), kde naleznete další informace o doporučených nastaveních a možnostech.

## <a name="validate-project-setup"></a>Ověřit nastavení projektu

Proveďte následující kroky k ověření, zda jsou nastavení projektu správná.

1. Zvolte položku ValidateProject z nabídky RemoteRendering v pruhu nástrojů editoru Unity.
1. Okno ValidateProject slouží ke kontrole a v případě potřeby k opravě nastavení projektu.

    ![Ověření projektu editoru unity](media/arr-unity-validation.png)

## <a name="create-a-script-to-initialize-azure-remote-rendering"></a>Vytvoření skriptu pro inicializaci vzdáleného vykreslování Azure

Vytvořte [nový skript](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) a pojmenujte jej **RemoteRendering**. Otevřete soubor skriptu a nahraďte celý jeho obsah níže uvedenou kódem:

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

#if UNITY_WSA
    using UnityEngine.XR.WSA;
#endif

// ask Unity to automatically append an ARRServiceUnity component when a RemoteRendering script is attached
[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRendering : MonoBehaviour
{
    // fill out the variables below with your account details

    // AccountDomain must be '<region>.mixedreality.azure.com' - if no '<region>' is specified, connections will fail
    // See the documentation for the list of available regions.
    public string AccountDomain = "westus2.mixedreality.azure.com";
    public string AccountId = "<enter your account id here>";
    public string AccountKey = "<enter your account key here>";

    public uint MaxLeaseTimeHours = 0;
    public uint MaxLeaseTimeMinutes = 10;
    public RenderingSessionVmSize VMSize = RenderingSessionVmSize.Standard;

    private ARRServiceUnity arrService = null;

    private void Awake()
    {
        // initialize Azure Remote Rendering for use in Unity:
        // it needs to know which camera is used for rendering the scene
        RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
        RemoteManagerUnity.InitializeManager(clientInit);

        // lookup the ARRServiceUnity component and subscribe to session events
        arrService = GetComponent<ARRServiceUnity>();
        arrService.OnSessionErrorEncountered += ARRService_OnSessionErrorEncountered;
        arrService.OnSessionStatusChanged += ARRService_OnSessionStatusChanged;
    }

#if !UNITY_EDITOR
    private void Start()
    {
        StartCoroutine(AutoStartSessionAsync());
    }
#endif

    private void OnDestroy()
    {
        arrService.OnSessionErrorEncountered -= ARRService_OnSessionErrorEncountered;
        arrService.OnSessionStatusChanged -= ARRService_OnSessionStatusChanged;

        RemoteManagerStatic.ShutdownRemoteRendering();
    }

    private void CreateFrontend()
    {
        if (arrService.Frontend != null)
        {
            // early out if the front-end has been created before
            return;
        }

        // initialize the ARR service with our account details
        AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
        accountInfo.AccountKey = AccountKey;
        accountInfo.AccountId = AccountId;
        accountInfo.AccountDomain = AccountDomain;

        arrService.Initialize(accountInfo);
    }

    public void CreateSession()
    {
        CreateFrontend();

        // StartSession will call ARRService_OnSessionStarted once the session becomes available
        arrService.StartSession(new RenderingSessionCreationParams(VMSize, MaxLeaseTimeHours, MaxLeaseTimeMinutes));
    }

    public void StopSession()
    {
        arrService.StopSession();
    }

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);
    }

    private void ARRService_OnSessionErrorEncountered(ARRServiceUnity caller, SessionGeneralContext context)
    {
        Debug.LogError($"Session error encountered. Context: {context.ErrorMessage}. Request Cv: {context.RequestCorrelationVector}. Response Cv: {context.ResponseCorrelationVector}");
    }

    private async void LogSessionStatus(AzureSession session)
    {
        if (session != null)
        {
            var sessionProperties = await session.GetPropertiesAsync().AsTask();
            LogSessionStatus(sessionProperties);
        }
        else
        {
            var sessionProperties = arrService.LastProperties;
            Debug.Log($"Session ended: Id={sessionProperties.Id}");
        }
    }

    private void LogSessionStatus(RenderingSessionProperties sessionProperties)
    {
        Debug.Log($"Session '{sessionProperties.Id}' is {sessionProperties.Status}. Size={sessionProperties.Size}" +
            (!string.IsNullOrEmpty(sessionProperties.Hostname) ? $", Hostname='{sessionProperties.Hostname}'" : "") +
            (!string.IsNullOrEmpty(sessionProperties.Message) ? $", Message='{sessionProperties.Message}'" : ""));
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
    }
#endif
}
```

Tento skript inicializuje azure vzdálené vykreslování, sdělte mu, který objekt kamery použít pro vykreslování a umístí tlačítko **Vytvořit relaci** do výřezu, když je aktivován *režim přehrávání.*

> [!CAUTION]
> Úprava skriptu a jeho uložení v době, kdy je aktivní režim přehrávání v Unity, může vést k zmrazení jednoty a budete nuceni jej vypnout prostřednictvím správce úloh. Proto vždy zastavit režim přehrávání před úpravou *RemoteRendering* skript.

## <a name="test-azure-remote-rendering-session-creation"></a>Testování vytvoření relace vzdáleného vykreslování Azure

Vytvořte nový GameObject ve scéně a přidejte *remoterendering* komponenty do něj. Vyplňte příslušnou *doménu účtu*, *Id účtu*a klíč *účtu* pro vzdálený vykreslovací účet:

![Vlastnosti součásti vzdáleného vykreslování](media/remote-rendering-component.png)

Spusťte aplikaci v editoru **(stiskněte tlačítko Přehrát** nebo CTRL+P). Ve výřezu by se mělo zobrazit tlačítko **Vytvořit relaci.** Kliknutím na něj zahájíte první relaci ARR:

![Vytvoření první relace](media/test-create.png)

Pokud se to nezdaří, ujistěte se, že jste správně zadali podrobnosti o účtu do vlastností komponenty RemoteRendering. V opačném případě se v okně konzoly zobrazí zpráva s ID relace, které vám bylo přiřazeno, a oznamující, že relace je aktuálně ve stavu *Spuštění:*

![Výstup spuštění relace](media/create-session-output.png)

V tomto okamžiku Azure zřizoval server pro vás a spouštění vzdáleného vykreslovacího virtuálního počítače. To obvykle **trvá 3 až 5 minut**. Když je virtuální jazyk připraven, spustí `OnSessionStatusChanged` se zpětné volání našeho skriptu Unity a vytiskne se stav nové relace:

![Výstup připravený k relaci](media/create-session-output-2.png)

To je ono! Prozatím se nic víc nestane. Chcete-li zabránit poplatkům, měli byste vždy zastavit relace, když již nejsou potřeba. V této ukázce to můžete provést klepnutím na tlačítko **Zastavit relaci** nebo zastavením simulace Unity. Vzhledem k vlastnosti **Auto-Stop Session** na komponentě *ARRServiceUnity,* která je ve výchozím nastavení zapnutá, bude relace automaticky zastavena za vás. Pokud vše selže, z důvodu selhání nebo problémy s připojením, vaše relace může běžet tak dlouho, jak *maxLeaseTime* před vypnutím serverem.

> [!NOTE]
> Zastavení relace se projeví okamžitě a nelze ji vrátit zpět. Po zastavení je třeba vytvořit novou relaci se stejnou režijní režií při spuštění.

## <a name="reusing-sessions"></a>Opakované použití relací

Vytvoření nové relace je bohužel časově náročná operace. Proto by se člověk měl snažit vytvářet relace zřídka a znovu je používat, kdykoli je to možné.

Vložte následující kód do skriptu *RemoteRendering* a odeberte staré verze duplicitních funkcí:

```csharp
    public string SessionId = null;

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);

        var status = caller.LastProperties.Status;

        // Capture the session id for the starting/ready session so it can be reused
        if (status == RenderingSessionStatus.Ready || status == RenderingSessionStatus.Starting)
        {
            SessionId = session.SessionUUID;
        }
        // Our session is stopped, expired, or in an error state.
        else
        {
            SessionId = null;
        }
    }

    public async void QueryActiveSessions()
    {
        CreateFrontend();

        var allSessionsProperties = await arrService.Frontend.GetCurrentRenderingSessionsAsync().AsTask();

        Debug.Log($"Number of active sessions: {allSessionsProperties.Length}");

        foreach (var sessionProperties in allSessionsProperties)
        {
            if (string.IsNullOrEmpty(SessionId))
            {
                Debug.Log($"Number of active sessions: {allSessionsProperties.Length}");
                SessionId = sessionProperties.Id;
            }

            LogSessionStatus(sessionProperties);
        }
    }

    public void UseExistingSession()
    {
        CreateFrontend();

        // OpenSession will call ARRService_OnSessionStarted once the session becomes available
        arrService.OpenSession(SessionId);
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
    }
#endif
```

> [!CAUTION]
> Před spuštěním tohoto kódu nezapomeňte deaktivovat možnost **Automatické zastavení relace** v součásti RemoteRendering. V opačném případě bude každá relace, kterou vytvoříte, automaticky zastavena, když zastavíte simulaci, a pokus o její opětovné použití se nezdaří.

Když stisknete *tlačítko Přehrát*, zobrazí se ve výřezu tři tlačítka: **Vytvořit relaci**, Aktivní **relace dotazu**a **Použít existující relaci**. První tlačítko vždy vytvoří novou relaci. Druhé tlačítko se dotazuje, které *aktivní* relace existují. Pokud jste ručně neurčili ID relace, které chcete zkusit použít, tato akce automaticky vybere toto ID relace pro budoucí použití. Třetí tlačítko se pokusí připojit k existující relaci. Buď ten, který jste zadali ručně prostřednictvím *vlastnosti komponenty Id relace,* nebo jeden nalezený *aktivními relacemi dotazu*.

Funkce **AutoStartSessionAsync** slouží k simulaci stisknutí tlačítka mimo editor.

> [!TIP]
> Je možné otevřít relace, které byly zastaveny, vypršela nebo jsou v chybovém stavu. Zatímco již nelze použít pro vykreslování, můžete dotazovat jejich podrobnosti, jakmile otevřete neaktivní relaci. Výše uvedený kód zkontroluje stav `ARRService_OnSessionStarted`relace v aplikace , aby se automaticky zastavil, když se relace stala nepoužitelnou.

Pomocí této funkce můžete nyní vytvářet a opakovaně používat relace, které by měly výrazně zlepšit pracovní postup vývoje.

Obvykle vytvoření relace by se aktivovalo mimo klientskou aplikaci z důvodu času potřebného ke spuštění serveru.

## <a name="connect-to-an-active-session"></a>Připojení k aktivní relaci

Zatím jsme vytvořili nebo otevřeli relace. Dalším krokem je *připojení* k relaci. Po připojení bude vykreslovací server produkovat obrázky a odesílat video stream do naší aplikace.

Vložte následující kód do skriptu *RemoteRendering* a odeberte staré verze duplicitních funkcí:

```csharp
    private bool isConnected = false;

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);

        var status = caller.LastProperties.Status;

        // Capture the session id for the starting/ready session so it can be reused
        if (status == RenderingSessionStatus.Ready || status == RenderingSessionStatus.Starting)
        {
            SessionId = session.SessionUUID;

            // If our session properties are 'Ready' we are ready to start connecting to the runtime of the service.
            if (status == RenderingSessionStatus.Ready)
            {
                session.ConnectionStatusChanged += AzureSession_OnConnectionStatusChanged;
            }
        }
        // Our session is stopped, expired, or in an error state.
        else
        {
            SessionId = null;
            session.ConnectionStatusChanged -= AzureSession_OnConnectionStatusChanged;
        }
    }

    private void AzureSession_OnConnectionStatusChanged(ConnectionStatus status, Result result)
    {
        Debug.Log($"Connection status: '{status}', result: '{result}'");
        isConnected = (status == ConnectionStatus.Connected);
    }

    public void ConnectSession()
    {
        arrService.CurrentActiveSession?.ConnectToRuntime(new ConnectToRuntimeParams());
    }

    public void DisconnectSession()
    {
        if (isConnected)
        {
            arrService.CurrentActiveSession?.DisconnectFromRuntime();
        }
    }

    private void LateUpdate()
    {
        // The session must have its runtime pump updated.
        // The update will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
        arrService.CurrentActiveSession?.Actions.Update();
    }

    private void OnDisable()
    {
        DisconnectSession();
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }

            if (arrService.LastProperties.Status == RenderingSessionStatus.Ready)
            {
                if (!isConnected)
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Connect"))
                    {
                        ConnectSession();
                    }
                }
                else
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Disconnect"))
                    {
                        DisconnectSession();
                    }
                }
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
        while (arrService.CurrentActiveSession == null)
        {
            yield return null;
        }
        ConnectSession();
    }
#endif
```

Chcete-li tuto funkci otestovat:

1. Stiskněte **tlačítko Přehrát** v jednotě.
1. Otevření relace:
    1. Pokud již máte relaci, stiskněte **dotaz na aktivní relace** a potom použijte existující **relaci**.
    1. V opačném případě stiskněte **klávesu Create Session**.
1. Stiskněte **klávesu Connect**.
1. Po několika sekundách by měl výstup konzoly vytisknout, že jste připojeni.
1. Prozatím by se nic jiného nemělo stát.
1. Stiskněte **tlačítko Odpojit** nebo zastavit režim přehrávání Unity.

>[!NOTE]
> Více uživatelů může *otevřít* relaci za účelem dotazování na její informace, ale k relaci může být *současně připojen* pouze jeden uživatel. Pokud je již připojen jiný uživatel, připojení se nezdaří s **chybou handshake**.

## <a name="load-a-model"></a>Načtení modelu

Vložte následující kód do skriptu *RemoteRendering* a odeberte staré verze duplicitních funkcí:

```csharp

    public string ModelName = "builtin://Engine";

    private Entity modelEntity = null;
    private GameObject modelEntityGO = null;

#if UNITY_WSA
    private WorldAnchor modelWorldAnchor = null;
#endif

    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();

        // get the game object representation of this entity
        modelEntityGO = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        // ensure the entity will sync translations with the server
        var sync = modelEntityGO.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        // set position to an arbitrary distance from the parent
        modelEntityGO.transform.position = Camera.main.transform.position + Camera.main.transform.forward * 2;
        modelEntityGO.transform.localScale = Vector3.one;

#if UNITY_WSA
        // anchor the model in the world
        modelWorldAnchor = modelEntityGO.AddComponent<WorldAnchor>();
#endif

        // load a model that will be parented to the entity
        // We are using the 'from SAS' flavor because that variant can load the built-in model
        var loadModelParams = new LoadModelFromSASParams(ModelName, modelEntity);
        var async = arrService.CurrentActiveSession.Actions.LoadModelFromSASAsync(loadModelParams);
        async.ProgressUpdated += (float progress) =>
        {
            Debug.Log($"Loading: {progress * 100.0f}%");
        };

        await async.AsTask();
    }

    public void DestroyModel()
    {
        if (modelEntity == null)
        {
            return;
        }

#if UNITY_WSA
        DestroyImmediate(modelWorldAnchor);
#endif

        modelEntity.Destroy();
        modelEntity = null;

        DestroyImmediate(modelEntityGO);
    }

    public void DisconnectSession()
    {
        if (isConnected)
        {
            arrService.CurrentActiveSession?.DisconnectFromRuntime();
        }

        DestroyModel();
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }

            if (arrService.LastProperties.Status == RenderingSessionStatus.Ready)
            {
                if (!isConnected)
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Connect"))
                    {
                        ConnectSession();
                    }
                }
                else
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Disconnect"))
                    {
                        DisconnectSession();
                    }

                    if (modelEntity == null)
                    {
                        if (GUI.Button(new Rect(10, 90, 175, 30), "Load Model"))
                        {
                            LoadModel();
                        }
                    }
                    else
                    {
                        if (GUI.Button(new Rect(10, 90, 175, 30), "Destroy Model"))
                        {
                            DestroyModel();
                        }
                    }
                }
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
        while (arrService.CurrentActiveSession == null)
        {
            yield return null;
        }
        ConnectSession();
        while (!isConnected)
        {
            yield return null;
        }
        LoadModel();
    }
#endif
```

Když nyní stisknete tlačítko Přehrát, otevřete relaci a připojíte se k ní, zobrazí se tlačítko **Načíst model.** Po kliknutí na něj se na výstupu konzoly zobrazí průběh načítání a když dosáhne 100%, měli byste vidět model motoru:

![Model načtený v editoru](media/model-loaded-replace-me.png)

[WorldAnchor](https://docs.unity3d.com/ScriptReference/XR.WSA.WorldAnchor.html) je důležitou součástí používanou pro [stabilitu hologramu](https://docs.microsoft.com/windows/mixed-reality/hologram-stability). Však bude mít vliv pouze při nasazení na zařízení se smíšenou realitou.

> [!TIP]
> Pokud jste postupovali podle [úvodního panelu: Převést model pro vykreslování](../../quickstarts/convert-model.md), už víte, jak převést své vlastní modely. Vše, co nyní musíte udělat, abyste jej vykreslovali, je umístit identifikátor URI do převedeného modelu do vlastnosti *Název modelu.*

## <a name="display-frame-statistics"></a>Zobrazit statistiku snímků

Azure Remote Rendering sleduje různé informace o kvalitě připojení. Chcete-li zobrazit tyto informace, postupujte takto:

Vytvořte [nový skript](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) a dát mu název **RemoteFrameStats**. Otevřete soubor skriptu a nahraďte celý jeho obsah níže uvedenou kódem:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class RemoteFrameStats : MonoBehaviour
{
    public Text FrameStats = null;

    ARRServiceStats arrServiceStats = null;

#if UNITY_EDITOR
    private void OnEnable()
    {
        arrServiceStats = new ARRServiceStats();
    }

    void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            FrameStats.text = string.Empty;
            return;
        }

        arrServiceStats.Update(RemoteManagerUnity.CurrentSession);

        if (FrameStats != null)
        {
            FrameStats.text = arrServiceStats.GetStatsString();
        }
    }
#endif
}
```

Vytvořte GameObject a pojmenujte jej *FrameStats*. Připojte jej jako podřízený uzel k objektu *hlavní kamery* a nastavte jeho polohu na **x = 0, y = 0, z = 0,325**. Přidejte do objektu komponentu **RemoteFrameStats.**

Přidejte podřízený objekt **> základního rozhraní** do objektu *FrameStats* a nastavte jeho vlastnosti takto:

![vlastnosti plátna](media/framestats-canvas.png)

Přidejte objekt **> textu ui** jako podřízený objekt plátna a nastavte jeho vlastnosti takto:

![vlastnosti textu](media/framestats-text.png)

Vyberte objekt *FrameStats* a naplňte **pole FrameStats** kliknutím na ikonu kruhu a **výběrem** textového objektu:

![nastavení vlastnosti textu](media/framestats-set-text.png)

Nyní, když jste připojeni ke vzdálené relaci, měl by text zobrazit statistiky streamování:

![výstup statistik snímků](media/framestats-output.png)

Kód zakáže aktualizaci statistik mimo editor, protože textové pole s uzamčeným hlavou by bylo rušivé. V projektu [Rychlého startu](../../quickstarts/render-model.md) se nachází složitější implementace.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili všechny kroky nezbytné k provedení prázdného projektu Unity a jeho práci s vzdáleným vykreslováním Azure. V dalším kurzu se blíže podíváme na to, jak pracovat se vzdálenými entitami.

> [!div class="nextstepaction"]
> [Kurz: Práce se vzdálenými entitami v Unity](working-with-remote-entities.md)
