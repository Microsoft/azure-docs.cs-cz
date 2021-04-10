---
title: Rychlý Start – připojení týmů na schůzku z aplikace pro iOS
description: V tomto kurzu se naučíte připojit týmy na schůzku pomocí komunikačních služeb Azure, které volají sadu SDK pro iOS.
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 8ff85dc5571f549027e208409db3f4e80c68b205
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108168"
---
V tomto rychlém startu se dozvíte, jak připojit týmy s využitím komunikačních služeb Azure, které volají sadu SDK pro iOS.

## <a name="prerequisites"></a>Požadavky

- Funkční [komunikační služby, které volají aplikaci pro iOS](../getting-started-with-calling.md).
- [Nasazení týmů](/deployoffice/teams-install).


## <a name="add-the-teams-ui-controls-and-enable-the-teams-ui-controls"></a>Přidat ovládací prvky uživatelského rozhraní týmů a povolit ovládací prvky uživatelského rozhraní týmů

Nahraďte kód v ContentView. SWIFT následujícím fragmentem kódu. Textové pole se použije k zadání kontextu schůzky týmů a tlačítko se použije k připojení k zadané schůzce:

```swift

import SwiftUI
import AzureCommunicationCalling
import AVFoundation

struct ContentView: View {
    @State var meetingLink: String = ""
    @State var callStatus: String = ""
    @State var message: String = ""
    @State var recordingStatus: String = ""
    @State var callClient: CallClient?
    @State var callAgent: CallAgent?
    @State var call: Call?
    @State var callObserver: CallObserver?

    var body: some View {
        NavigationView {
            Form {
                Section {
                    TextField("Teams meeting link", text: $meetingLink)
                    Button(action: joinTeamsMeeting) {
                        Text("Join Teams Meeting")
                    }.disabled(callAgent == nil)
                    Button(action: leaveMeeting) {
                        Text("Leave Meeting")
                    }.disabled(call == nil)
                    Text(callStatus)
                    Text(message)
                    Text(recordingStatus)
                }
            }
            .navigationBarTitle("Calling Quickstart")
        }.onAppear {
            // Initialize call agent
            var userCredential: CommunicationTokenCredential?
            do {
                userCredential = try CommunicationTokenCredential(token: "<USER ACCESS TOKEN>")
            } catch {
                print("ERROR: It was not possible to create user credential.")
                self.message = "Please enter your token in source code"
                return
            }

            self.callClient = CallClient()

            // Creates the call agent
            self.callClient?.createCallAgent(userCredential: userCredential) { (agent, error) in
                if error == nil {
                    guard let agent = agent else {
                        self.message = "Failed to create CallAgent"
                        return
                    }

                    self.callAgent = agent
                    self.message = "Call agent successfully created."
                } else {
                    self.message = "Failed to create CallAgent with error"
                }
            }
        }
    }

    func joinTeamsMeeting() {
        // Ask permissions
        AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
            if granted {
                let joinCallOptions = JoinCallOptions()
                let teamsMeetingLinkLocator = TeamsMeetingLinkLocator(meetingLink: self.meetingLink);
                guard let call = self.callAgent?.join(with: teamsMeetingLinkLocator, joinCallOptions: joinCallOptions) else {
                    self.message = "Failed to join Teams meeting"
                    return
                }

                self.call = call
                self.callObserver = CallObserver(self)
                self.call!.delegate = self.callObserver
                self.message = "Teams meeting joined successfully"
            }
        }
    }

    func leaveMeeting() {
        if let call = call {
            call.hangup(options: nil, completionHandler: { (error) in
                if error == nil {
                    self.message = "Leaving Teams meeting was successful"
                } else {
                    self.message = "Leaving Teams meeting failed"
                }
            })
        } else {
            self.message = "No active call to hanup"
        }
    }
}

class CallObserver : NSObject, CallDelegate {
    private var owner:ContentView
    init(_ view:ContentView) {
        owner = view
    }

    public func onCallStateChanged(_ call: Call!,
                                   args: PropertyChangedEventArgs!) {
        owner.callStatus = CallObserver.callStateToString(state: call.state)
        if call.state == .disconnected {
            owner.call = nil
            owner.message = "Left Meeting"
        } else if call.state == .inLobby {
            owner.message = "Waiting in lobby !!"
        } else if call.state == .connected {
            owner.message = "Joined Meeting !!"
        }
    }
    
    public func onIsRecordingActiveChanged(_ call: Call!, args: PropertyChangedEventArgs!) {
        if (call.isRecordingActive == true) {
            owner.recordingStatus = "This call is being recorded"
        }
        else {
            owner.recordingStatus = ""
        }
    }

    private static func callStateToString(state: CallState) -> String {
        switch state {
        case .connected: return "Connected"
        case .connecting: return "Connecting"
        case .disconnected: return "Disconnected"
        case .disconnecting: return "Disconnecting"
        case .earlyMedia: return "EarlyMedia"
        case .hold: return "Hold"
        case .incoming: return "Incoming"
        case .none: return "None"
        case .ringing: return "Ringing"
        case .inLobby: return "InLobby"
        default: return "Unknown"
        }
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}

```

## <a name="get-the-teams-meeting-link"></a>Získat odkaz na schůzku týmů

Odkaz na schůzku týmů lze načíst pomocí rozhraní Graph API. Tato část je podrobně popsána v [dokumentaci ke grafům](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta).
Komunikační služby, které volá sadu SDK, přijímají úplný tým odkaz na schůzku. Tento odkaz se vrátí jako součást `onlineMeeting` prostředku, který je přístupný v rámci [ `joinWebUrl` vlastnosti](/graph/api/resources/onlinemeeting?view=graph-rest-beta). Požadované informace o schůzce můžete také získat z adresy URL **připojení ke schůzce** v rámci pozvánky na schůzku.

## <a name="launch-the-app-and-join-teams-meeting"></a>Spusťte aplikaci a připojte se k Teams Meeting.

Můžete sestavit a spustit aplikaci v simulátoru iOS tak, že vyberete možnost  >  **spuštění** produktu nebo pomocí klávesové zkratky (&#8984;-R).

:::image type="content" source="../media/ios/acs-join-teams-meeting-quickstart.png" alt-text="Snímek obrazovky znázorňující dokončenou aplikaci":::

Do textového pole vložte kontext týmů a stiskněte *připojit týmy schůzky* , aby se připojili k týmům, které se nacházejí v rámci vaší aplikace komunikační služby.
