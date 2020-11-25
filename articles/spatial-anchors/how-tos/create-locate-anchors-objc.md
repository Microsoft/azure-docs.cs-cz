---
title: Vytvoření & vyhledání kotev v cíli – C
description: Podrobné vysvětlení způsobu vytváření a hledání kotev pomocí prostorových kotev Azure v cíli – C.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 6bb156544400ecd820fcb3e8c38d25dc7d83d442
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2020
ms.locfileid: "96019544"
---
# <a name="how-to-create-and-locate-anchors-using-azure-spatial-anchors-in-objective-c"></a>Jak vytvořit a najít kotvy pomocí prostorových kotev Azure v cíli – C

> [!div  class="op_single_selector"]
> * [Unity](create-locate-anchors-unity.md)
> * [Objective-C](create-locate-anchors-objc.md)
> * [Swift](create-locate-anchors-swift.md)
> * [Android Java](create-locate-anchors-java.md)
> * [/NDK C++](create-locate-anchors-cpp-ndk.md)
> * [C++/WinRT](create-locate-anchors-cpp-winrt.md)

Prostorové kotvy Azure umožňují sdílet kotvy na světě mezi různými zařízeními. Podporuje několik různých vývojových prostředí. V tomto článku se podrobněme, jak používat sadu SDK prostorových kotev Azure v cíli-C k těmto akcím:

- Správně nastavte a spravujte relaci prostorových kotev Azure.
- Vytváření a nastavování vlastností místních ukotvení.
- Nahrajte je do cloudu.
- Najděte a odstraňte cloudové kotvy.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto průvodce se ujistěte, že máte následující:

- Přečtěte si [Přehled prostorových kotev Azure](../overview.md).
- Bylo dokončeno jedno z [5 minut rychlých startů](../index.yml).
- Základní znalosti o cíli – C.
- Základní znalosti o <a href="https://developer.apple.com/arkit/" target="_blank">ARKit</a>

[!INCLUDE [Start](../../../includes/spatial-anchors-create-locate-anchors-start.md)]

Přečtěte si další informace o třídě [ASACloudSpatialAnchorSession](/objectivec/api/spatial-anchors/asacloudspatialanchorsession) .

```objc
    ASACloudSpatialAnchorSession *_cloudSession;
    // In your view handler
    _cloudSession = [[ASACloudSpatialAnchorSession alloc] init];
```

[!INCLUDE [Account Keys](../../../includes/spatial-anchors-create-locate-anchors-account-keys.md)]

Přečtěte si další informace o třídě [ASASessionConfiguration](/objectivec/api/spatial-anchors/asasessionconfiguration) .

```objc
    _cloudSession.configuration.accountKey = @"MyAccountKey";
```

[!INCLUDE [Access Tokens](../../../includes/spatial-anchors-create-locate-anchors-access-tokens.md)]

```objc
    _cloudSession.configuration.accessToken = @"MyAccessToken";
```

[!INCLUDE [Access Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-access-tokens-event.md)]

Přečtěte si další informace o metodě protokolu [tokenRequired](/objectivec/api/spatial-anchors/asacloudspatialanchorsessiondelegate#tokenrequired) .

```objc
    - (void)tokenRequired:(ASACloudSpatialAnchorSession *)cloudSession :(ASATokenRequiredEventArgs *)args {
        args.accessToken = @"MyAccessToken";
    }
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```objc
    - (void)tokenRequired:(ASACloudSpatialAnchorSession *)cloudSession :(ASATokenRequiredEventArgs *)args {
        ASACloudSpatialAnchorSessionDeferral *deferral = [args getDeferral];
        [myGetTokenAsync callback:^(NSString *myToken) {
            if (myToken) args.accessToken = myToken;
            [deferral complete];
        }];
    }
```

[!INCLUDE [Azure AD Tokens](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens.md)]

```objc
    _cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

[!INCLUDE [Azure AD Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens-event.md)]

```objc
    - (void)tokenRequired:(ASACloudSpatialAnchorSession *)cloudSession :(ASATokenRequiredEventArgs *)args {
        args.authenticationToken = @"MyAuthenticationToken";
    }
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```objc
    - (void)tokenRequired:(ASACloudSpatialAnchorSession *)cloudSession :(ASATokenRequiredEventArgs *)args {
        ASACloudSpatialAnchorSessionDeferral *deferral = [args getDeferral];
        [myGetTokenAsync callback:^(NSString *myToken) {
            if (myToken) args.authenticationToken = myToken;
            [deferral complete];
        }];
    }
```

[!INCLUDE [Setup](../../../includes/spatial-anchors-create-locate-anchors-setup-ios.md)]

Přečtěte si další informace o metodě [Start](/objectivec/api/spatial-anchors/asacloudspatialanchorsession#start) .

```objc0
    _cloudSession.session = self.sceneView.session;
    _cloudSession.delegate = self;
    [_cloudSession start];
```

[!INCLUDE [Frames](../../../includes/spatial-anchors-create-locate-anchors-frames.md)]

Další informace o metodě [processFrame](/objectivec/api/spatial-anchors/asacloudspatialanchorsession#processframe)

```objc
    [_cloudSession processFrame:_sceneView.session.currentFrame];
```

[!INCLUDE [Feedback](../../../includes/spatial-anchors-create-locate-anchors-feedback.md)]

Přečtěte si další informace o metodě protokolu [sessionUpdated](/objectivec/api/spatial-anchors/asacloudspatialanchorsessiondelegate#sessionupdated) .

```objc
    - (void)sessionUpdated:(ASACloudSpatialAnchorSession *)cloudSession :(ASASessionUpdatedEventArgs *)args {
        ASASessionStatus *status = [args status];
        if (status.userFeedback == ASASessionUserFeedbackNone) return;
        _feedback = [NSString
            stringWithFormat:@"Feedback: %@ - Recommend Create=%.0f%%",
            FeedbackToString(status.userFeedback),
            status.recommendedForCreateProgress * 100.f];
    }
```

[!INCLUDE [Creating](../../../includes/spatial-anchors-create-locate-anchors-creating.md)]

Přečtěte si další informace o třídě [ASACloudSpatialAnchor](/objectivec/api/spatial-anchors/asacloudspatialanchor) .

```objc
    // Create a local anchor, perhaps by hit-testing and creating an ARAnchor
    NSArray<ARHitTestResult *> *hits = [_sceneView.session.currentFrame hitTest:CGPointMake(0.5, 0.5) types:ARHitTestResultTypeEstimatedHorizontalPlane];
    if ([hits count] == 0) return;
    // The hitTest method sorts the resulting list by increasing distance from the camera
    // The first hit result will usually be the most relevant when responding to user input
    ARAnchor *localAnchor = [[ARAnchor alloc] initWithTransform:hits[0].worldTransform];
    [_sceneView.session addAnchor:localAnchor];

    // If the user is placing some application content in their environment,
    // you might show content at this anchor for a while, then save when
    // the user confirms placement.
    ASACloudSpatialAnchor *cloudAnchor = [[ASACloudSpatialAnchor alloc] init];
    cloudAnchor.localAnchor = localAnchor;
    [_cloudSession createAnchor:cloudAnchor withCompletionHandler:^(NSError *error) {
        if (error) {
            _feedback = [NSString stringWithFormat:@"Save Failed:%@", error.localizedDescription];
            return;
        }
        _feedback = [NSString stringWithFormat:@"Created a cloud anchor with ID=%@", cloudAnchor.identifier];
    }];
```

[!INCLUDE [Session Status](../../../includes/spatial-anchors-create-locate-anchors-session-status.md)]

Další informace o metodě [getSessionStatusWithCompletionHandler](/objectivec/api/spatial-anchors/asacloudspatialanchorsession#getsessionstatus)

```objc
    [_cloudSession getSessionStatusWithCompletionHandler:^(ASASessionStatus *value, NSError *error) {
        if (error) {
            _feedback = [NSString stringWithFormat:@"Session status error:%@", error.localizedDescription];
            return;
        }
        if (value.recommendedForCreateProgress < 1.0f) return;
        // Issue the creation request ...
    }];
```

[!INCLUDE [Setting Properties](../../../includes/spatial-anchors-create-locate-anchors-setting-properties.md)]

Přečtěte si další informace o vlastnosti [appProperties](/objectivec/api/spatial-anchors/asacloudspatialanchor#appproperties) .

```objc
    ASACloudSpatialAnchor *cloudAnchor = [[ASACloudSpatialAnchor alloc] init];
    cloudAnchor.localAnchor = localAnchor;
    cloudAnchor.appProperties = @{ @"model-type" : @"frame", @"label" : @"my latest picture" };
    [_cloudSession createAnchor:cloudAnchor withCompletionHandler:^(NSError *error) {
        // ...
    });
```

[!INCLUDE [Update Anchor Properties](../../../includes/spatial-anchors-create-locate-anchors-updating-properties.md)]

Další informace o metodě [updateAnchorProperties](/objectivec/api/spatial-anchors/asacloudspatialanchorsession#updateanchorproperties)

```objc
    ASACloudSpatialAnchor *anchor = /* locate your anchor */;
    [anchor.appProperties setValue:@"just now" forKey:@"last-user-access"];
    [_cloudSession updateAnchorProperties:anchor withCompletionHandler:^(NSError *error) {
        if (error) _feedback = [NSString stringWithFormat:@"Updating Properties Failed:%@", error.localizedDescription];
    }];
```

[!INCLUDE [Getting Properties](../../../includes/spatial-anchors-create-locate-anchors-getting-properties.md)]

Další informace o metodě [getAnchorProperties](/objectivec/api/spatial-anchors/asacloudspatialanchorsession#getanchorproperties)

```objc
    [_cloudSession getAnchorProperties:@"anchorId" withCompletionHandler:^(SCCCloudSpatialAnchor *anchor, NSError *error) {
        if (error) {
            _feedback = [NSString stringWithFormat:@"Getting Properties Failed:%@", error.localizedDescription];
            return;
        }
        if (anchor) {
            [anchor.appProperties setValue:@"just now" forKey:@"last-user-access"];
            [_cloudSession updateAnchorProperties:anchor withCompletionHandler:^(NSError *error) {
                // ...
            }];
        }
    }];
```

[!INCLUDE [Expiration](../../../includes/spatial-anchors-create-locate-anchors-expiration.md)]

Přečtěte si další informace o vlastnosti [vypršení platnosti](/objectivec/api/spatial-anchors/asacloudspatialanchor#expiration) .

```objc
    int secondsInAWeek = 60 * 60 * 24 * 7;
    NSDate *oneWeekFromNow = [[NSDate alloc] initWithTimeIntervalSinceNow: (NSTimeInterval) secondsInAWeek];
    cloudAnchor.expiration = oneWeekFromNow;
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating.md)]

Další informace o metodě [createWatcher](/objectivec/api/spatial-anchors/asacloudspatialanchorsession#createwatcher)

```objc
    ASAAnchorLocateCriteria *criteria = [ASAAnchorLocateCriteria new];
    criteria.identifiers = @[ @"id1", @"id2", @"id3" ];
    [_cloudSession createWatcher:criteria];
```

[!INCLUDE [Locate Events](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

Přečtěte si další informace o metodě protokolu [anchorLocated](/objectivec/api/spatial-anchors/asacloudspatialanchorsessiondelegate#anchorlocated) .

```objc
    - (void)anchorLocated:(ASACloudSpatialAnchorSession *)cloudSession :(ASAAnchorLocatedEventArgs *)args {
        ASALocateAnchorStatus status = [args status];
        switch (status) {
        case ASALocateAnchorStatusLocated: {
            ASACloudSpatialAnchor *foundAnchor = [args anchor];
            // Go add your anchor to the scene...
        }
            break;
        case ASALocateAnchorStatusAlreadyTracked:
            // This anchor has already been reported and is being tracked
            break;
        case ASALocateAnchorStatusNotLocatedAnchorDoesNotExist:
            // The anchor was deleted or never existed in the first place
            // Drop it, or show UI to ask user to anchor the content anew
            break;
        case ASALocateAnchorStatusNotLocated:
            // The anchor hasn't been found given the location data
            // The user might in the wrong location, or maybe more data will help
            // Show UI to tell user to keep looking around
            break;
    }
```

[!INCLUDE [Deleting](../../../includes/spatial-anchors-create-locate-anchors-deleting.md)]

Další informace o metodě [deleteAnchor](/objectivec/api/spatial-anchors/asacloudspatialanchorsession#deleteanchor)

```objc
    [_cloudSession deleteAnchor:cloudAnchor withCompletionHandler:^(NSError *error) {
        // Perform any processing you may want when delete finishes
    }];
```

[!INCLUDE [Stopping](../../../includes/spatial-anchors-create-locate-anchors-stopping.md)]

Přečtěte si další informace o metodě [stop](/objectivec/api/spatial-anchors/asacloudspatialanchorsession#stop) .

```objc
    [_cloudSession stop];
```

[!INCLUDE [Resetting](../../../includes/spatial-anchors-create-locate-anchors-resetting.md)]

Přečtěte si další informace o metodě [resetování](/objectivec/api/spatial-anchors/asacloudspatialanchorsession#reset) .

```objc
    [_cloudSession reset];
```

[!INCLUDE [Cleanup](../../../includes/spatial-anchors-create-locate-anchors-cleanup-others.md)]

```objc
    _cloudSession = NULL;
```

[!INCLUDE [Next Steps](../../../includes/spatial-anchors-create-locate-anchors-next-steps.md)]