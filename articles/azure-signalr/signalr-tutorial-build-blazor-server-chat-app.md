---
title: 'Kurz: Vytvoření aplikace Blazor Server chat – signál Azure'
description: V tomto kurzu se naučíte vytvářet a upravovat aplikace serveru Blazor pomocí služby Azure Signal Service.
author: JialinXin
ms.service: signalr
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jixin
ms.openlocfilehash: b0059e986b9a6ba8152a1a61f8d696f1caa4646a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97755901"
---
# <a name="tutorial-build-a-blazor-server-chat-app"></a>Kurz: Vytvoření aplikace chat serveru Blazor

V tomto kurzu se dozvíte, jak vytvořit a upravit aplikaci Blazor serveru. Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Sestavte jednoduchou chatovací místnost pomocí aplikace Blazor Server.
> * Upravte komponenty Razor.
> * Použijte zpracování událostí a datové vazby v součástech.
> * Rychlé nasazení Azure App Service v aplikaci Visual Studio.
> * Migrujte místní signál do služby Azure Signal.

## <a name="prerequisites"></a>Předpoklady
* Nainstalovat [sadu .NET Core 3,0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) (verze >= 3.0.100)
* Instalace sady [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) (verze >= 16,3)
> Verze Visual Studio 2019 Preview funguje i pro vydání nejnovější šablony aplikace Blazor serveru, která cílí na novější verzi .Net Core.

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/asrs/qsblazor)

## <a name="build-a-local-chat-room-in-blazor-server-app"></a>Sestavení místní konverzační místnosti v aplikaci Blazor Server

V rámci sady Visual Studio 2019 verze 16.2.0 je služba signalizace Azure v procesu publikování webové aplikace a Správa závislostí mezi webovou aplikací a službou Signal by byla mnohem pohodlnější. Můžete pracovat na místním signálu v prostředí vývojového prostředí a pracovat na službě Azure Signal Service pro Azure App Service ve stejnou dobu bez jakýchkoli změn kódu.

1. Vytvoření aplikace chat Blazor
   
   V aplikaci Visual Studio vyberte možnost vytvořit nový projekt – > aplikace Blazor-> (pojmenujte aplikaci a vyberte složku) – > aplikace Blazor serveru. Ujistěte se, že jste už nainstalovali .NET Core SDK 3.0 +, abyste aplikaci Visual Studio správně rozpoznali cílovou architekturu.

   [![V části vytvořit nový projekt jsou vybrány šablony aplikací Blazor. ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png#lightbox)
   
   Nebo spusťte příkaz cmd
   ```dotnetcli
   dotnet new blazorserver -o BlazorChat
   ```
   
1. Přidejte `BlazorChatSampleHub.cs` soubor, který se má implementovat `Hub` pro chat.
   
   ```cs
   using System;
   using System.Threading.Tasks;
   using Microsoft.AspNetCore.SignalR;
   
   namespace BlazorChat
   {
       public class BlazorChatSampleHub : Hub
       {
           public const string HubUrl = "/chat";
   
           public async Task Broadcast(string username, string message)
           {
               await Clients.All.SendAsync("Broadcast", username, message);
           }
   
           public override Task OnConnectedAsync()
           {
               Console.WriteLine($"{Context.ConnectionId} connected");
               return base.OnConnectedAsync();
           }
   
           public override async Task OnDisconnectedAsync(Exception e)
           {
               Console.WriteLine($"Disconnected {e?.Message} {Context.ConnectionId}");
               await base.OnDisconnectedAsync(e);
           }
       }
   }
   ```
   
1. Přidejte koncový bod pro centrum v `Startup.Configure()` .
   
   ```cs
   app.UseEndpoints(endpoints =>
   {
       endpoints.MapBlazorHub();
       endpoints.MapFallbackToPage("/_Host");
       endpoints.MapHub<BlazorChatSampleHub>(BlazorChatSampleHub.HubUrl);
   });
   ```
   
1. Nainstalujte `Microsoft.AspNetCore.SignalR.Client` balíček pro použití klienta signalizace.

   ```dotnetcli
   dotnet add package Microsoft.AspNetCore.SignalR.Client --version 3.1.7
   ```

1. Vytvořte `ChatRoom.razor` v části `Pages` Složka pro implementaci klienta signalizace. Postupujte podle následujících kroků nebo jednoduše zkopírujte [ChatRoom. Razor](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BlazorChat/Pages/ChatRoom.razor).

   1. Přidejte odkaz a odkaz na stránku.
      
      ```razor
      @page "/chatroom"
      @inject NavigationManager navigationManager
      @using Microsoft.AspNetCore.SignalR.Client;
      ```

   1. Přidejte kód do nového klienta signalizace pro odesílání a příjem zpráv.
      
      ```razor
      @code {
          // flag to indicate chat status
          private bool _isChatting = false;
          
          // name of the user who will be chatting
          private string _username;
      
          // on-screen message
          private string _message;
          
          // new message input
          private string _newMessage;
          
          // list of messages in chat
          private List<Message> _messages = new List<Message>();
          
          private string _hubUrl;
          private HubConnection _hubConnection;
      
          public async Task Chat()
          {
              // check username is valid
              if (string.IsNullOrWhiteSpace(_username))
              {
                  _message = "Please enter a name";
                  return;
              };
      
              try
              {
                  // Start chatting and force refresh UI.
                  _isChatting = true;
                  await Task.Delay(1);

                  // remove old messages if any
                  _messages.Clear();
         
                  // Create the chat client
                  string baseUrl = navigationManager.BaseUri;
      
                  _hubUrl = baseUrl.TrimEnd('/') + BlazorChatSampleHub.HubUrl;
      
                  _hubConnection = new HubConnectionBuilder()
                      .WithUrl(_hubUrl)
                      .Build();
      
                  _hubConnection.On<string, string>("Broadcast", BroadcastMessage);
      
                  await _hubConnection.StartAsync();
      
                  await SendAsync($"[Notice] {_username} joined chat room.");
              }
              catch (Exception e)
              {
                  _message = $"ERROR: Failed to start chat client: {e.Message}";
                  _isChatting = false;
              }
          }
      
          private void BroadcastMessage(string name, string message)
          {
              bool isMine = name.Equals(_username, StringComparison.OrdinalIgnoreCase);
      
              _messages.Add(new Message(name, message, isMine));
      
              // Inform blazor the UI needs updating
              StateHasChanged();
          }
      
          private async Task DisconnectAsync()
          {
              if (_isChatting)
              {
                  await SendAsync($"[Notice] {_username} left chat room.");
      
                  await _hubConnection.StopAsync();
                  await _hubConnection.DisposeAsync();
      
                  _hubConnection = null;
                  _isChatting = false;
              }
          }
      
          private async Task SendAsync(string message)
          {
              if (_isChatting && !string.IsNullOrWhiteSpace(message))
              {
                  await _hubConnection.SendAsync("Broadcast", _username, message);
      
                  _newMessage = string.Empty;
              }
          }
      
          private class Message
          {
              public Message(string username, string body, bool mine)
              {
                  Username = username;
                  Body = body;
                  Mine = mine;
              }
      
              public string Username { get; set; }
              public string Body { get; set; }
              public bool Mine { get; set; }
      
              public bool IsNotice => Body.StartsWith("[Notice]");
      
              public string CSS => Mine ? "sent" : "received";
          }
      }
      ```

   1. Přidejte část vykreslování předtím, než `@code` bude uživatelské rozhraní pracovat s klientem signalizace.
      
      ```razor
      <h1>Blazor SignalR Chat Sample</h1>
      <hr />
      
      @if (!_isChatting)
      {
          <p>
              Enter your name to start chatting:
          </p>
      
          <input type="text" maxlength="32" @bind="@_username" />
          <button type="button" @onclick="@Chat"><span class="oi oi-chat" aria-hidden="true"></span> Chat!</button>
      
          // Error messages
          @if (_message != null)
          {
              <div class="invalid-feedback">@_message</div>
              <small id="emailHelp" class="form-text text-muted">@_message</small>
          }
      }
      else
      {
          // banner to show current user
          <div class="alert alert-secondary mt-4" role="alert">
              <span class="oi oi-person mr-2" aria-hidden="true"></span>
              <span>You are connected as <b>@_username</b></span>
              <button class="btn btn-sm btn-warning ml-md-auto" @onclick="@DisconnectAsync">Disconnect</button>
          </div>
          // display messages
          <div id="scrollbox">
              @foreach (var item in _messages)
              {
                  @if (item.IsNotice)
                  {
                      <div class="alert alert-info">@item.Body</div>
                  }
                  else
                  {
                      <div class="@item.CSS">
                          <div class="user">@item.Username</div>
                          <div class="msg">@item.Body</div>
                      </div>
                  }
              }
              <hr />
              <textarea class="input-lg" placeholder="enter your comment" @bind="@_newMessage"></textarea>
              <button class="btn btn-default" @onclick="@(() => SendAsync(_newMessage))">Send</button>
          </div>
      }
      ```

1. Aktualizace `NavMenu.razor` pro vložení nabídky položky pro chatovací místnost v nabídce `NavMenuCssClass` jako REST.

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="chatroom">
           <span class="oi oi-chat" aria-hidden="true"></span> Chat room
       </NavLink>
   </li>
   ```
   
1. Aktualizace `site.css` k optimalizaci pro zobrazení bublin v oblasti chatu Přidejte pod kód na konci.

   ```css
   /* improved for chat text box */
   textarea {
       border: 1px dashed #888;
       border-radius: 5px;
       width: 80%;
       overflow: auto;
       background: #f7f7f7
   }
   
   /* improved for speech bubbles */
   .received, .sent {
       position: relative;
       font-family: arial;
       font-size: 1.1em;
       border-radius: 10px;
       padding: 20px;
       margin-bottom: 20px;
   }
   
   .received:after, .sent:after {
       content: '';
       border: 20px solid transparent;
       position: absolute;
       margin-top: -30px;
   }
   
   .sent {
       background: #03a9f4;
       color: #fff;
       margin-left: 10%;
       top: 50%;
       text-align: right;
   }
   
   .received {
       background: #4CAF50;
       color: #fff;
       margin-left: 10px;
       margin-right: 10%;
   }
   
   .sent:after {
       border-left-color: #03a9f4;
       border-right: 0;
       right: -20px;
   }
   
   .received:after {
       border-right-color: #4CAF50;
       border-left: 0;
       left: -20px;
   }
   
   /* div within bubble for name */
   .user {
       font-size: 0.8em;
       font-weight: bold;
       color: #000;
   }
   
   .msg {
       /*display: inline;*/
   }
   ```

1. Kliknutím na <kbd>F5</kbd> spusťte aplikaci. Budete moci chatovat, jak je znázorněno níže.

   [![Zobrazuje se animovaná konverzace mezi Bobem a Alicí. Alice říká Hello, Bob říká Dobrý den. ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif#lightbox)
   
[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/asrs/qsblazor)

## <a name="publish-to-azure"></a>Publikování do Azure

   V současné době aplikace Blazor pracuje na místním signálu a při nasazení do Azure App Service je navržena pro použití [služby signalizace Azure](/aspnet/core/signalr/scale?view=aspnetcore-3.1#azure-signalr-service) , která umožňuje škálovat aplikaci serveru Blazor na velký počet souběžných připojení k signalizaci. Kromě toho globální dosah a vysoce výkonná datová centra služby signalizace významně pomáhají při snižování latence kvůli geografickým zeměpisům.

> [!IMPORTANT]
> V aplikaci Blazor Server jsou stavy uživatelského rozhraní udržovány na straně serveru, což znamená, že v tomto případě je vyžadován server v rychlém stavu. Pokud je k dispozici jeden aplikační server, je server v rychlém designu zajištěný. Pokud ale existuje několik aplikačních serverů, může vyjednávání klienta a připojení přejít na jiné servery a vede k chybám uživatelského rozhraní v aplikaci Blazor. Proto je třeba povolit server v rychlém příkladu níže v `appsettings.json` :
> ```json
> "Azure:SignalR:ServerStickyMode": "Required"
> ```

1. Klikněte na projekt pravým tlačítkem myši a přejděte na `Publish` .

   * Cíl: Azure
   * Konkrétní cíl: jsou podporovány všechny typy **Azure App Service** .
   * App Service: Vytvořte novou nebo vyberte existující službu App Service.

   [![Animace zobrazuje výběr Azure jako cíle a pak Azure App manifestu jako konkrétní cíl. ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif#lightbox)

1. Přidat závislost služby signalizace Azure

   Po vytvoření profilu publikování se v části **závislosti služby** zobrazí doporučená zpráva. Kliknutím na **Konfigurovat** vytvořte novou nebo vyberte existující službu Azure Signal Service na panelu.

   [![Při publikování je odkaz na konfiguraci zvýrazněný. ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png#lightbox)

   Tato závislost služby provede následující akce, které umožní aplikaci automaticky přepnout na službu Azure Signal Service v Azure.

   * Aktualizujte [`HostingStartupAssembly`](/aspnet/core/fundamentals/host/platform-specific-configuration?view=aspnetcore-3.1) , aby používala službu Azure Signal Service.
   * Přidejte odkaz na balíček NuGet služby pro signály Azure.
   * Aktualizujte vlastnosti profilu a uložte nastavení závislostí.
   * Konfigurace úložiště tajných klíčů závisí na vaší volbě.
   * Přidejte `appsettings` konfiguraci pro výběr cíle vaší aplikace na službu Azure Signal.

   [![Při souhrnu změn se k výběru všech závislostí použijí zaškrtávací políčka. ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png#lightbox)

1. Publikování aplikace

   Nyní je připraven k publikování. A po dokončení publikování bude stránka automaticky procházet. 
   > [!NOTE]
   > Nemusí okamžitě fungovat při první návštěvě stránky z důvodu latence spuštění nasazení Azure App Service a zkuste stránku aktualizovat, aby bylo jisté zpoždění.
   > Kromě toho můžete použít režim ladicího programu prohlížeče s nástrojem <kbd>F12</kbd> k ověření, že provoz již přesměruje na službu Azure Signal Service.

   [![Ukázka chatu pro signál Blazor obsahuje textové pole pro vaše jméno a tlačítko chat! pro spuštění chatu. ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png#lightbox)
   
[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/asrs/qsblazor)

## <a name="further-topic-enable-azure-signalr-service-in-local-development"></a>Další téma: povolení služby signalizace Azure v místním vývojovém prostředí

1. Přidat odkaz na sadu SDK pro službu Azure Signal

   ```dotnetcli
   dotnet add package Microsoft.Azure.SignalR --version 1.5.1
   ```

1. Přidejte do služby Azure Signal Service volání `Startup.ConfigureServices()` .

   ```cs
   public void ConfigureServices(IServiceCollection services)
   {
       ...
       services.AddSignalR().AddAzureSignalR();
       ...
   }
   ```

1. Konfigurace služby signalizace Azure `ConnectionString` buď v `appsetting.json` nástroji, nebo pomocí nástroje [správce tajných klíčů](/aspnet/core/security/app-secrets?tabs=visual-studio&view=aspnetcore-3.1#secret-manager)

> [!NOTE]
> Krok 2 se dá nahradit pomocí [`HostingStartupAssembly`](/aspnet/core/fundamentals/host/platform-specific-configuration?view=aspnetcore-3.1) do sady Signal SDK.
> 
> 1. Přidání konfigurace pro zapnutí služby signalizace Azure v `appsetting.json`
>    ```js
>    "Azure": {
>      "SignalR": {
>        "Enabled": true,
>        "ConnectionString": <your-connection-string>
>      }
>    }
>    ```
> 
> 1. Přiřaďte hostování spouštěcího sestavení pro použití sady Azure Signal SDK. Upravte `launchSettings.json` a přidejte konfiguraci podobnou níže `environmentVariables` .
>    ```js
>    "environmentVariables": {
>        ...,
>        "ASPNETCORE_HOSTINGSTARTUPASSEMBLIES": "Microsoft.Azure.SignalR"
>      }
>    ```

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/asrs/qsblazor)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky vytvořené v tomto kurzu, pomocí webu Azure Portal odstraňte skupinu prostředků.

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Sestavte jednoduchou chatovací místnost pomocí aplikace Blazor Server.
> * Upravte komponenty Razor.
> * Použijte zpracování událostí a datové vazby v součástech.
> * Rychlé nasazení Azure App Service v aplikaci Visual Studio.
> * Migrujte místní signál do služby Azure Signal.

Přečtěte si další informace o vysoké dostupnosti.
> [!div class="nextstepaction"]
> [Odolnost a zotavení po havárii](signalr-concept-disaster-recovery.md)

## <a name="additional-resources"></a>Další zdroje informací

* [ASP.NET Core Blazor](/aspnet/core/blazor)
