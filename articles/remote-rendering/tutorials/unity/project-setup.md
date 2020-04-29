---
title: Nastavení projektu Unity od začátku
description: Vysvětluje, jak nakonfigurovat prázdný projekt Unity pro použití se vzdáleným vykreslováním Azure.
author: florianborn71
ms.author: flborn
ms.date: 01/30/2020
ms.topic: tutorial
ms.openlocfilehash: 33801316e4c0446865169560bb42f98052acba70
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80679594"
---
# <a name="tutorial-setting-up-a-unity-project-from-scratch"></a>Kurz: vytvoření projektu Unity od začátku

V tomto kurzu se dozvíte:

> [!div class="checklist"]
>
> * Konfigurace nového projektu Unity pro ARR
> * Vytváření a zastavování relací vykreslování.
> * Znovu se používají existující relace.
> * Připojení a odpojení od relací.
> * Načítají se modely do relace vykreslování.
> * Zobrazují se statistiky připojení.

## <a name="prerequisites"></a>Požadavky

Pro tento kurz potřebujete:

* Informace o vašem účtu (ID účtu, klíč účtu, ID předplatného). Pokud účet nemáte, [vytvořte účet](../../how-tos/create-an-account.md).
* Windows SDK 10.0.18362.0 [(Stáhnout)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* Nejnovější verzi sady Visual Studio 2019 [(Stáhnout)](https://visualstudio.microsoft.com/vs/older-downloads/)
* GIT [(Stáhnout)](https://git-scm.com/downloads)
* Unity 2019.3.1 [(Stáhnout)](https://unity3d.com/get-unity/download)
  * Nainstalujte tyto moduly v Unity:
    * Podpora **UWP** – Univerzální platforma Windows sestavení
    * **IL2CPP** – podpora sestavení pro Windows (IL2CPP)

> [!TIP]
> [Úložiště ukázek ARR](https://github.com/Azure/azure-remote-rendering) obsahuje připravené projekty Unity pro všechny kurzy. Tyto projekty můžete použít jako referenci.

## <a name="create-a-new-unity-project"></a>Vytvořit nový projekt Unity

V centru Unity vytvořte nový projekt.
V tomto příkladu budeme předpokládat, že projekt se vytváří ve složce s názvem `RemoteRendering`.

![nové okno projektu](media/new-project.png)

## <a name="configure-the-projects-manifest"></a>Konfigurace manifestu projektu

Je potřeba upravit soubor `Packages/manifest.json` , který je umístěný ve složce projektu Unity. Otevřete soubor v textovém editoru a přidejte řádky uvedené níže:

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

Balíček kanálu univerzálního vykreslování je volitelný, ale doporučuje se z důvodů výkonu.
Po úpravě a uložení manifestu se Unity automaticky aktualizuje. Potvrďte, že jsou balíčky načteny v okně *projektu* :

![potvrdit importy balíčků](media/confirm-packages.png)

## <a name="ensure-you-have-the-latest-version-of-the-package"></a>Ujistěte se, že máte nejnovější verzi balíčku.

Následující kroky zajišťují, aby projekt používal nejnovější verzi balíčku vzdáleného vykreslování.
1. V okně projektu vyberte balíček a klikněte na ikonu balíčku: ![výběr ikony balíčku](media/package-icons.png)
1. V inspektoru klikněte na zobrazit ve Správci balíčků: Inspector balíčku ![.](media/package-properties.png)
1. Na stránce Správce balíčků pro balíček vzdáleného vykreslování zkontrolujte, zda je k dispozici tlačítko Aktualizovat. Pokud je, pak se kliknutím na něj aktualizuje balíček na nejnovější dostupnou verzi: balíček ARR ![ve Správci balíčků.](media/package-manager.png)
1. Aktualizace balíčku někdy může vést k chybám v konzole nástroje. Pokud k tomu dojde, zkuste projekt zavřít a znovu otevřít.

## <a name="configure-the-camera"></a>Konfigurace kamery

Vyberte **hlavní uzel kamery** .

1. Resetovat *transformaci*:

    ![resetovat transformaci kamery](media/camera-reset-transform.png)

1. Nastavit **clear Flags** na *plnou barvu*

1. Nastavit **pozadí** na *černou*

1. Nastavte **roviny ořezu** na *poblíž = 0,3* a *daleko = 20*. To znamená, že vykreslování bude oříznout geometrii, která je větší než 30 cm nebo větší než 20 metrů.

    ![Vlastnosti kamery Unity](media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>Úprava nastavení projektu

1. Otevřít *úpravy > nastavení projektu...*
1. V seznamu na levé straně vyberte kvalitu.
1. Změnit **výchozí úroveň kvality** na *Nízká*

    ![změnit nastavení kvality projektu](media/settings-quality.png)

1. Vyberte **grafiku** vlevo.
1. Změňte nastavení **kanálu vykreslování pomocí skriptů** na *HybridRenderingPipeline*. Tento krok přeskočte, pokud není použit kanál univerzálního vykreslování.

    ![Změna nastavení](media/settings-graphics-lwrp.png) grafiky projektu někdy v uživatelském rozhraní nenačítá seznam dostupných typů kanálů z balíčků. v takovém případě musí být prostředek *HybridRenderingPipeline* přetažen do pole ručně: Změna nastavení grafiky projektu ![.](media/hybrid-rendering-pipeline.png)
1. Na levé straně vyberte **Player** .
1. Vyberte kartu **nastavení Univerzální platforma Windows**
1. Změna **Nastavení XR** pro podporu Windows Mixed reality: ![nastavení přehrávače](media/xr-player-settings.png)
1. Vyberte nastavení jako na snímku obrazovky výše:
    1. Povolit **podporu virtuální realitu**
    1. Nastavit **Formát hloubky** na *16bitovou hloubku*
    1. Povolit **sdílení vyrovnávací paměti pro hloubku**
    1. Nastavit **režim vykreslování stereo** na *instanci Single Passed*

1. Ve stejném okně, nad *XR nastavení*, rozbalte **Nastavení publikování** .
1. Přejděte dolů na **Možnosti** a vyberte:
    * **InternetClient**
    * **InternetClientServer**
    * **SpatialPerception**
    * Volitelné pro vývoj: **PrivateNetworkClientServer**

      Tato možnost je nutná, pokud chcete ke svému zařízení připojit vzdálený ladicí program Unity.

1. V **podporovaných rodinách zařízení**povolit **holografické** a **desktopové**

1. Pokud chcete používat sadu nástrojů Mixed reality, přečtěte si [dokumentaci k MRTK](https://docs.microsoft.com/windows/mixed-reality/unity-development-overview), kde najdete další informace o doporučeném nastavení a možnostech.

## <a name="validate-project-setup"></a>Ověřit nastavení projektu

Provedením následujících kroků ověříte, zda je nastavení projektu správné.

1. V nabídce RemoteRendering na panelu nástrojů editoru Unity vyberte položku ValidateProject.
1. Pomocí okna ValidateProject můžete podle potřeby vyhledat a opravit nastavení projektu.

    ![Ověření projektu editoru Unity](media/arr-unity-validation.png)

## <a name="create-a-script-to-initialize-azure-remote-rendering"></a>Vytvoření skriptu pro inicializaci vzdáleného vykreslování Azure

Vytvořte [Nový skript](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) a pojmenujte ho **RemoteRendering**. Otevřete soubor skriptu a nahraďte jeho celý obsah následujícím kódem:

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

Tento skript inicializuje vzdálené vykreslování Azure, informuje o tom, který objekt kamery se má použít pro vykreslování, *a když se* aktivuje tlačítko pro vytvoření relace, umístí se do zobrazení tlačítko **vytvořit relaci** .

> [!CAUTION]
> Úprava skriptu a jeho uložení v případě, že je aktivní režim přehrávání v Unity, může způsobit zamrznutí Unity a vy budete nuceni ho vypnout pomocí Správce úloh. Proto před úpravou skriptu *RemoteRendering* vždy zastavte režim přehrávání.

## <a name="test-azure-remote-rendering-session-creation"></a>Test vytvoření relace vzdáleného vykreslování Azure

Vytvořte ve scéně novou GameObject a přidejte do ní komponentu *RemoteRendering* . Vyplňte příslušnou *doménu účtu*, *ID účtu*a *klíč účtu* pro účet vzdáleného vykreslování:

![Vlastnosti komponenty vzdáleného vykreslování](media/remote-rendering-component.png)

Spusťte aplikaci v editoru (**stiskněte tlačítko Přehrát** nebo CTRL + P). V zobrazení se zobrazí tlačítko **vytvořit relaci** . Kliknutím na něj spustíte svou první relaci ARR:

![Vytvoření první relace](media/test-create.png)

Pokud se to nepovede, ujistěte se, že jste správně zadali podrobnosti o účtu do vlastností komponenty RemoteRendering. V opačném případě se v okně konzoly zobrazí zpráva s ID relace, které jste přiřadili, a oznámení o tom, že je relace aktuálně ve *výchozím* stavu:

![Spuštění výstupu relace](media/create-session-output.png)

V tomto okamžiku Azure zřídí server pro vás a spouští se virtuální počítač pro vzdálené vykreslování. To obvykle **trvá 3 až 5 minut**. Až bude virtuální počítač připravený, provede se `OnSessionStatusChanged` zpětné volání skriptu Unity a vytiskne se nový stav relace:

![Výstup připravený pro relaci](media/create-session-output-2.png)

To je! V době, kdy se nic nestane víc. Aby se předešlo poplatkům, měli byste vždycky zastavit relace, když už nejsou potřeba. V této ukázce to můžete udělat tak, že kliknete na tlačítko **zastavit relaci** nebo zastavíte simulaci Unity. Kvůli **automatickému zastavení vlastnosti relace** v součásti *ARRServiceUnity* , která je ve výchozím nastavení zapnutá, se relace automaticky zastaví. Pokud dojde k selhání z důvodu chyby nebo problémů s připojením, může se vaše relace spustit po dobu, po kterou se *MaxLeaseTime* před vypnutím serverem.

> [!NOTE]
> Zastavení relace se projeví okamžitě a nedá se vrátit zpátky. Po zastavení budete muset vytvořit novou relaci se stejnou režií při spuštění.

## <a name="reusing-sessions"></a>Znovu se používají relace

Vytvoření nové relace je, ale časově náročná operace bohužel. Proto by se jedna měla pokusit vytvořit relace zřídka a znovu je použít, kdykoli to bude možné.

Do skriptu *RemoteRendering* vložte následující kód a odstraňte staré verze duplicitních funkcí:

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
> Před spuštěním tohoto kódu nezapomeňte deaktivovat možnost **automaticky zastavit relaci** v součásti RemoteRendering. V opačném případě se každá vytvořená relace automaticky zastaví, když simulaci zastavíte a pokus o opakované použití selže.

Po stisknutí tlačítka *Přehrát*se teď v zobrazení zobrazí tři tlačítka: **vytvořit relaci**, **dotazovat aktivní relace**a **použít existující relaci**. První tlačítko vždy vytvoří novou relaci. Druhé tlačítko vyžádá dotaz, které *aktivní* relace existují. Pokud jste ručně nezadali ID relace, které se má použít, tato akce automaticky vybere toto ID relace pro budoucí použití. Třetí tlačítko se pokusí připojit k existující relaci. Jednu, kterou jste zadali ručně prostřednictvím vlastnosti komponenty s *ID relace* , nebo jednu, kterou najde *dotaz na aktivní relace*.

Funkce **AutoStartSessionAsync** slouží k simulaci stisknutí tlačítek mimo editor.

> [!TIP]
> Je možné otevřít relace, které byly zastaveny, jejichž platnost vypršela, nebo jsou v chybovém stavu. I když již nelze použít pro vykreslování, můžete po otevření neaktivní relace zadat dotaz na jejich podrobnosti. Výše uvedený kód kontroluje stav relace v `ARRService_OnSessionStarted`, aby se automaticky zastavily, když se relace stane nepoužitelnou.

Díky této funkci teď můžete vytvářet a opakovaně používat relace, které by měly významně zlepšit vývoj pracovního postupu.

Vytvoření relace se obvykle spustí mimo klientskou aplikaci kvůli době potřebné k tomu, aby se server vyvolal.

## <a name="connect-to-an-active-session"></a>Připojení k aktivní relaci

Zatím jsme vytvořili nebo otevřeli relace. Dalším krokem je *připojení* k relaci. Po připojení vygeneruje Server vykreslování image a pošle Stream videa do naší aplikace.

Do skriptu *RemoteRendering* vložte následující kód a odstraňte staré verze duplicitních funkcí:

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

K otestování této funkce:

1. Stiskněte **Přehrát** v Unity.
1. Otevřete relaci:
    1. Pokud už máte relaci, stiskněte **dotaz na aktivní relace** a pak **použijte existující relaci**.
    1. V opačném případě stiskněte **vytvořit relaci**.
1. Stiskněte **připojit**.
1. Po několika sekundách by měl výstup konzoly vytisknout, ke kterému jste se připojili.
1. V současné době by nemělo dojít k žádnému dalšímu.
1. Stiskněte tlačítko **Odpojit** nebo zastavit režim přehrávání Unity.

>[!NOTE]
> Několik uživatelů může *otevřít* relaci pro dotazování na příslušné informace, ale v jednom okamžiku může být k relaci *připojen* pouze jeden uživatel. Pokud je již připojen jiný uživatel, připojení selže a dojde k **chybě handshake**.

## <a name="load-a-model"></a>Načtení modelu

Do skriptu *RemoteRendering* vložte následující kód a odstraňte staré verze duplicitních funkcí:

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

Když nyní stisknete tlačítko Přehrát, otevřete relaci a připojíte se k ní, zobrazí se tlačítko **model zatížení** . Po kliknutí na výstup konzoly se zobrazí průběh načítání a když dosáhne 100%, měl by se zobrazit model motoru:

![Model načtený v editoru](media/model-loaded-replace-me.png)

[WorldAnchor](https://docs.unity3d.com/ScriptReference/XR.WSA.WorldAnchor.html) je důležitou součástí, která se používá pro [stabilitu hologramů](https://docs.microsoft.com/windows/mixed-reality/hologram-stability). Projeví se to ale jenom v případě, že se nasadí na zařízení se smíšeným realitou.

> [!TIP]
> Pokud jste sledovali [rychlý Start: převod modelu pro vykreslování](../../quickstarts/convert-model.md), již víte, jak převést vlastní modely. Vše, co je potřeba udělat, abyste ho mohli vykreslit, je vložit identifikátor URI do převedeného modelu do vlastnosti *název modelu* .

## <a name="display-frame-statistics"></a>Zobrazit statistiku snímků

Vzdálené vykreslování Azure sleduje různé informace o kvalitě připojení. Rychlý způsob zobrazení těchto informací:

Vytvořte [Nový skript](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) a pojmenujte ho **RemoteFrameStats**. Otevřete soubor skriptu a nahraďte jeho celý obsah následujícím kódem:

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

Vytvořte GameObject a pojmenujte ho *FrameStats*. Připojte ho jako podřízený uzel k objektu *hlavního fotoaparátu* a nastavte jeho polohu na **x = 0, y = 0, z = 0,325**. Přidejte komponentu **RemoteFrameStats** do objektu.

Do objektu *FrameStats* přidejte podřízený objekt **> uživatelského rozhraní** a nastavte jeho vlastnosti takto:

![vlastnosti plátna](media/framestats-canvas.png)

Přidejte **uživatelské rozhraní > textový** objekt jako podřízený prvek plátna a nastavte jeho vlastnosti takto:

![vlastnosti textu](media/framestats-text.png)

Vyberte objekt *FrameStats* a naplňte **pole FrameStats** kliknutím na ikonu kruhu a výběrem **textového** objektu:

![nastavení vlastnosti text](media/framestats-set-text.png)

Teď, když se připojíte ke vzdálené relaci, text by měl zobrazovat statistiky streamování:

![výstup statistik snímků](media/framestats-output.png)

Kód zakáže aktualizaci statistiky mimo editor, protože textové pole zamčené záhlaví by bylo odvoláno. Propracovanější implementace je k dispozici v projektu [rychlý Start](../../quickstarts/render-model.md) .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o všech krocích nutných k vytvoření prázdného projektu Unity a k jeho fungování při vzdáleném vykreslování Azure. V dalším kurzu se podíváme na to, jak pracovat se vzdálenými entitami.

> [!div class="nextstepaction"]
> [Kurz: práce se vzdálenými entitami v Unity](working-with-remote-entities.md)
