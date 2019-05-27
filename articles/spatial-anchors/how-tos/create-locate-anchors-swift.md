---
title: Jak vytvořit a vyhledejte ukotvení pomocí prostorových kotvy Azure ve Swiftu | Dokumentace Microsoftu
description: Podrobnější vysvětlení toho, jak vytvořit a vyhledejte ukotvení pomocí prostorových kotvy Azure ve Swiftu.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: f0078aac093a19ae0d02da4e7ba07c112d563971
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65964823"
---
# <a name="how-to-create-and-locate-anchors-using-azure-spatial-anchors-in-swift"></a>Jak vytvořit a vyhledejte ukotvení pomocí prostorových kotvy Azure ve Swiftu

> [!div  class="op_single_selector"]
> * [Unity](create-locate-anchors-unity.md)
> * [Objective-C](create-locate-anchors-objc.md)
> * [Swift](create-locate-anchors-swift.md)
> * [Android Java](create-locate-anchors-java.md)
> * [C++/ SADA NDK](create-locate-anchors-cpp-ndk.md)
> * [C++/ WinRT](create-locate-anchors-cpp-winrt.md)

Azure prostorových kotvy umožňují sdílet kotvy na světě mezi různými zařízeními. Podporuje několik různých vývojových prostředích. V tomto článku začneme budete zabývat jak pomocí nástroje Azure prostorových kotvy SDK ve Swiftu:

- Správně nastavit a spravovat relaci prostorových kotvy Azure.
- Vytvořte a nastavte vlastnosti na místní ukotvení.
- Jejich nahrávání do cloudu.
- Vyhledejte a odstraňte prostorových Cloudová ukotvení.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto průvodce, ujistěte se, že máte:

- Přečtěte si [přehled Azure prostorových kotvy](../overview.md).
- Dokončit jeden z [zprovoznění během 5 minut](../index.yml).
- Základní znalosti Swift.
- Základní znalosti <a href="https://developer.apple.com/arkit/" target="_blank">ARKit</a>.

[!INCLUDE [Start](../../../includes/spatial-anchors-create-locate-anchors-start.md)]

Další informace o [ASACloudSpatialAnchorSession](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession) třídy.

```swift
    var _cloudSession : ASACloudSpatialAnchorSession? = nil
    // In your view handler
    _cloudSession = ASACloudSpatialAnchorSession()
```

[!INCLUDE [Account Keys](../../../includes/spatial-anchors-create-locate-anchors-account-keys.md)]

Další informace o [ASASessionConfiguration](https://docs.microsoft.com/objectivec/api/spatial-anchors/asasessionconfiguration) třídy.

```swift
    _cloudSession!.configuration.accountKey = "MyAccountKey"
```

[!INCLUDE [Access Tokens](../../../includes/spatial-anchors-create-locate-anchors-access-tokens.md)]

```swift
    _cloudSession!.configuration.accessToken = "MyAccessToken"
```

[!INCLUDE [Access Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-access-tokens-event.md)]

Další informace o [tokenRequired](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsessiondelegate#tokenrequired) protokol metody.

```swift
    internal func tokenRequired(_ cloudSession:ASACloudSpatialAnchorSession!, _ args:ASATokenRequiredEventArgs!) {
        args.accessToken = "MyAccessToken"
    }
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```swift
    internal func tokenRequired(_ cloudSession:ASACloudSpatialAnchorSession!, _ args:ASATokenRequiredEventArgs!) {
        let deferral = args.getDeferral()
        myGetTokenAsync( withCompletionHandler: { (myToken: String?) in
            if (myToken != nil) {
                args.accessToken = myToken
            }
            deferral?.complete()
        })
    }

```

[!INCLUDE [Azure AD Tokens](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens.md)]

```swift
    _cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

[!INCLUDE [Azure AD Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens-event.md)]

```swift
    internal func tokenRequired(_ cloudSession:ASACloudSpatialAnchorSession!, _ args:ASATokenRequiredEventArgs!) {
        args.authenticationToken = "MyAuthenticationToken"
    }
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```swift
    internal func tokenRequired(_ cloudSession:ASACloudSpatialAnchorSession!, _ args:ASATokenRequiredEventArgs!) {
        let deferral = args.getDeferral()
        myGetTokenAsync( withCompletionHandler: { (myToken: String?) in
            if (myToken != nil) {
                args.authenticationToken = myToken
            }
            deferral?.complete()
        })
    }

```

[!INCLUDE [Setup](../../../includes/spatial-anchors-create-locate-anchors-setup-ios.md)]

Další informace o [start](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#start) metody.

```swift
    _cloudSession!.session = self.sceneView.session;
    _cloudSession!.delegate = self;
    _cloudSession!.start()
```

[!INCLUDE [Frames](../../../includes/spatial-anchors-create-locate-anchors-frames.md)]

Další informace o [processFrame](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#processframe) metody.

```swift
    _cloudSession?.processFrame(self.sceneView.session.currentFrame)
```

[!INCLUDE [Feedback](../../../includes/spatial-anchors-create-locate-anchors-feedback.md)]

Další informace o [sessionUpdated](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsessiondelegate#sessionupdated) protokol metody.

```swift
    internal func sessionUpdated(_ cloudSession:ASACloudSpatialAnchorSession!, _ args:ASASessionUpdatedEventArgs!) {
        let status = args.status!
        if (status.userFeedback.isEmpty) {
            return
        }
        _feedback = "Feedback: \(FeedbackToString(userFeedback:status.userFeedback)) - Recommend Create=\(status.recommendedForCreateProgress * 100)"
    }
```

[!INCLUDE [Creating](../../../includes/spatial-anchors-create-locate-anchors-creating.md)]

Další informace o [ASACloudSpatialAnchor](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchor) třídy.

```swift
    // Create a local anchor, perhaps by hit-testing and creating an ARAnchor
    var localAnchor : ARAnchor? = nil
    let hits = self.sceneView.session.currentFrame?.hitTest(CGPoint(x:0.5, y:0.5), types: ARHitTestResult.ResultType.estimatedHorizontalPlane)
    if (hits!.count == 0) return
    // The hitTest method sorts the resulting list by distance from the camera, increasing
    // The first hit result will usually be the most relevant when responding to user input
    localAnchor = ARAnchor(transform:hits![0].worldTransform)
    self.sceneView.session.add(anchor: _localAnchor!)

    // If the user is placing some application content in their environment,
    // you might show content at this anchor for a while, then save when
    // the user confirms placement.
    var cloudAnchor : ASACloudSpatialAnchor? = nil
    cloudAnchor = ASACloudSpatialAnchor()
    cloudAnchor!.localAnchor = localAnchor
    _cloudSession?.createAnchor(cloudAnchor!, withCompletionHandler: { (error: Error?) in
        if (error != nil) {
            _feedback = "Save Failed:\(error!.localizedDescription)"
            return
        }
        _feedback = "Created a cloud anchor with ID=\(cloudAnchor!.identifier!)"
    })
```

[!INCLUDE [Session Status](../../../includes/spatial-anchors-create-locate-anchors-session-status.md)]

Další informace o [getStatusWithCompletionHandler](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#getsessionstatus) metody.

```swift
    _cloudSession?.getStatusWithCompletionHandler( { (value:ASASessionStatus, error:Error?) in
        if (error != nil) {
            _feedback = "Session status error:\(error!.localizedDescription)"
            return
        }
        if (value!.recommendedForCreateProgress <> 1.0) {
            return
        }
        // Issue the creation request ...
    })
```

[!INCLUDE [Setting Properties](../../../includes/spatial-anchors-create-locate-anchors-setting-properties.md)]

Další informace o [objekt appProperties](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchor#appproperties) vlastnost.

```swift
    var cloudAnchor : ASACloudSpatialAnchor? = nil
    cloudAnchor = ASACloudSpatialAnchor()
    cloudAnchor!.localAnchor = localAnchor
    cloudAnchor!.appProperties = [ "model-type" : "frame", "label" : "my latest picture" ]
    _cloudSession?.createAnchor(cloudAnchor!, withCompletionHandler: { (error: Error?) in
        // ...
    })
```

[!INCLUDE [Update Anchor Properties](../../../includes/spatial-anchors-create-locate-anchors-updating-properties.md)]

Další informace o [updateAnchorProperties](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#updateanchorproperties) metody.

```swift
    var anchor : ASACloudSpatialAnchor? = /* locate your anchor */;
    anchor!.appProperties["last-user-access"] = "just now"
    _cloudSession?.updateAnchorProperties(anchor!, withCompletionHandler: { (error:Error?) in
        if (error != nil) {
            _feedback = "Updating Properties Failed:\(error!.localizedDescription)"
        }
    })
```

[!INCLUDE [Getting Properties](../../../includes/spatial-anchors-create-locate-anchors-getting-properties.md)]

Další informace o [getAnchorProperties](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#getanchorproperties) metody.

```swift
    _cloudSession?.getAnchorProperties("anchorId", withCompletionHandler: { (anchor:SCCCloudSpatialAnchor?, error:Error?) in
        if (error != nil) {
            _feedback = "Getting Properties Failed:\(error!.localizedDescription)"
        }
        if (anchor != nil) {
            anchor!.appProperties["last-user-access"] = "just now"
            _cloudSession?.updateAnchorProperties(anchor!, withCompletionHandler: { (error:Error?) in
                // ...
            })
        }
    })

```

[!INCLUDE [Expiration](../../../includes/spatial-anchors-create-locate-anchors-expiration.md)]

Další informace o [vypršení platnosti](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchor#expiration) vlastnost.

```swift
    let secondsInAWeek = 60.0 * 60.0 * 24.0 * 7.0
    let oneWeekFromNow = Date(timeIntervalSinceNow: secondsInAWeek)
    cloudAnchor!.expiration = oneWeekFromNow
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating.md)]

Další informace o [createWatcher](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#createwatcher) metody.

```swift
    let criteria = ASAAnchorLocateCriteria()!
    criteria.identifiers = [ "id1", "id2", "id3" ]
    _cloudSession!.createWatcher(criteria)
```

[!INCLUDE [Locate Events](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

Další informace o [anchorLocated](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsessiondelegate#anchorlocated) protokol metody.

```swift
    internal func anchorLocated(_ cloudSession: ASACloudSpatialAnchorSession!, _ args: ASAAnchorLocatedEventArgs!) {
        let status = args.status
        switch (status) {
        case ASALocateAnchorStatus.located:
            let foundAnchor = args.anchor
            // Go add your anchor to the scene...
            break
        case ASALocateAnchorStatus.alreadyTracked:
            // This anchor has already been reported and is being tracked
            break
        case ASALocateAnchorStatus.notLocatedAnchorDoesNotExist:
            // The anchor was deleted or never existed in the first place
            // Drop it, or show UI to ask user to anchor the content anew
            break
        case ASALocateAnchorStatus.notLocated:
            // The anchor hasn't been found given the location data
            // The user might in the wrong location, or maybe more data will help
            // Show UI to tell user to keep looking around
            break
        }
    }
```

[!INCLUDE [Deleting](../../../includes/spatial-anchors-create-locate-anchors-deleting.md)]

Další informace o [odstranit](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#deleteanchor) metody.

```swift
    _cloudSession?.delete(cloudAnchor!, withCompletionHandler: { (error: Error?) in
        // Perform any processing you may want when delete finishes
    })
```

[!INCLUDE [Stopping](../../../includes/spatial-anchors-create-locate-anchors-stopping.md)]

Další informace o [Zastavit](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#stop) metody.

```swift
    _cloudSession!.stop()
```

[!INCLUDE [Resetting](../../../includes/spatial-anchors-create-locate-anchors-resetting.md)]

Další informace o [resetování](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#reset) metody.

```swift
    _cloudSession!.reset()
```

[!INCLUDE [Cleanup](../../../includes/spatial-anchors-create-locate-anchors-cleanup-others.md)]

```swift
    _cloudSession = nil
```

[!INCLUDE [Next Steps](../../../includes/spatial-anchors-create-locate-anchors-next-steps.md)]
