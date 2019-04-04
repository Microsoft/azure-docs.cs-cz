---
title: Jak vytvořit a vyhledejte ukotvení pomocí prostorových kotvy Azure v jazyce C + +/ WinRT | Dokumentace Microsoftu
description: Podrobnější vysvětlení toho, jak vytvořit a vyhledejte ukotvení pomocí prostorových kotvy Azure v jazyce C + +/ WinRT.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: ramonarguelles
ms.date: 02/24/2019
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 966a8bd5990f955a3ec66ae465d14a0369d0cdf9
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2019
ms.locfileid: "58915304"
---
# <a name="how-to-create-and-locate-anchors-using-azure-spatial-anchors-in-cwinrt"></a>Jak vytvořit a vyhledejte ukotvení pomocí prostorových kotvy Azure v jazyce C + +/ WinRT

> [!div  class="op_single_selector"]
> * [Unity](create-locate-anchors-unity.md)
> * [Objective-C](create-locate-anchors-objc.md)
> * [Swift](create-locate-anchors-swift.md)
> * [Android Java](create-locate-anchors-java.md)
> * [C + +/ NDK](create-locate-anchors-cpp-ndk.md)
> * [C + +/ WinRT](create-locate-anchors-cpp-winrt.md)

Azure prostorových kotvy umožňují sdílet kotvy na světě mezi různými zařízeními. Podporuje několik různých vývojových prostředích. V tomto článku začneme budete zabývat způsob použití sady SDK Azure prostorových ukotvení v jazyce C + +/ WinRT, do:

- Správně nastavit a spravovat relaci prostorových kotvy Azure.
- Vytvořte a nastavte vlastnosti na místní ukotvení.
- Jejich nahrávání do cloudu.
- Vyhledejte a odstraňte prostorových Cloudová ukotvení.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto průvodce, ujistěte se, že máte:

- Přečtěte si [přehled Azure prostorových kotvy](../overview.md).
- Dokončit jeden z [zprovoznění během 5 minut](../index.yml).
- Základní znalosti jazyka C++ a <a href="https://docs.microsoft.com/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt" target="_blank">rozhraní API Windows Runtime</a>.

[!INCLUDE [Start](../../../includes/spatial-anchors-create-locate-anchors-start.md)]

Další informace o [CloudSpatialAnchorSession](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession) třídy.

```cpp
    SpatialAnchorsFactory m_asafactory{ nullptr };
    CloudSpatialAnchorSession m_cloudSession{ nullptr };
    winrt::com_ptr<::IUnknown> unk;
    winrt::check_hresult(ASACreateFactory(unk.put()));
    m_asafactory = unk.as<SpatialAnchorsFactory>();
    m_cloudSession = m_asafactory.CreateCloudSpatialAnchorSession();
```

[!INCLUDE [Account Keys](../../../includes/spatial-anchors-create-locate-anchors-account-keys.md)]

Další informace o [SessionConfiguration](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/sessionconfiguration) třídy.

```cpp
    auto configuration = m_cloudSession.Configuration();
    configuration.AccountKey(LR"(MyAccountKey)");
```

[!INCLUDE [Access Tokens](../../../includes/spatial-anchors-create-locate-anchors-access-tokens.md)]

```cpp
    auto configuration = m_cloudSession.Configuration();
    configuration.AccessToken(LR"(MyAccessToken)");
```

[!INCLUDE [Access Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-access-tokens-event.md)]

Další informace o [TokenRequiredDelegate](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/tokenrequireddelegate) delegovat.

```cpp
    m_accessTokenRequiredToken = m_cloudSession.TokenRequired(winrt::auto_revoke, [](auto&&, auto&& args) {
        args.AccessToken(LR"(MyAccessToken)");
    });
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```cpp
    m_accessTokenRequiredToken = m_cloudSession.TokenRequired(winrt::auto_revoke, [this](auto&&, auto&& args) {
        auto deferral = args.GetDeferral();
        MyGetTokenAsync([&deferral, &args](winrt::hstring const& myToken) {
            if (!myToken.empty()) args.AccessToken(myToken);
            deferral.Complete();
        });
    });
```

[!INCLUDE [Azure AD Tokens](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens.md)]

```cpp
    auto configuration = m_cloudSession.Configuration();
    configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

[!INCLUDE [Azure AD Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens-event.md)]

```cpp
    m_accessTokenRequiredToken = m_cloudSession.TokenRequired(winrt::auto_revoke, [](auto&&, auto&& args) {
        args.AuthenticationToken(LR"(MyAuthenticationToken)");
    });
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```cpp
    m_accessTokenRequiredToken = m_cloudSession.TokenRequired(winrt::auto_revoke, [this](auto&&, auto&& args) {
        auto deferral = args.GetDeferral();
        MyGetTokenAsync([&deferral, &args](winrt::hstring const& myToken) {
            if (!myToken.empty()) args.AuthenticationToken(myToken);
            deferral.Complete();
        });
    });
```

[!INCLUDE [Setup](../../../includes/spatial-anchors-create-locate-anchors-setup-non-ios.md)]

Další informace o [Start](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession#start) metody.

```cpp
    m_cloudSession.Start();
```

[!INCLUDE [Frames](../../../includes/spatial-anchors-create-locate-anchors-frames.md)]

Další informace o [ProcessFrame](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession) metody.

```cpp
    m_cloudSession->ProcessFrame(ar_frame_);
```

[!INCLUDE [Feedback](../../../includes/spatial-anchors-create-locate-anchors-feedback.md)]

Další informace o [SessionUpdatedDelegate](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/sessionupdateddelegate) delegovat.

```cpp
    m_sessionUpdatedToken = m_cloudSession.SessionUpdated(winrt::auto_revoke, [this](auto&&, auto&& args)
    {
        auto status = args.Status();
        if (status.UserFeedback() == SessionUserFeedback::None) return;
        m_feedback = LR"(Feedback: )" + FeedbackToString(status.UserFeedback()) + LR"( -)" +
            LR"( Recommend Create=)" + FormatPercent(status.RecommendedForCreateProgress() * 100.f);
    });
```

[!INCLUDE [Creating](../../../includes/spatial-anchors-create-locate-anchors-creating.md)]

Další informace o [CloudSpatialAnchor](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchor) třídy.

```cpp
    // Initialization
    SpatialStationaryFrameOfReference m_stationaryReferenceFrame = nullptr;
    HolographicDisplay defaultHolographicDisplay = HolographicDisplay::GetDefault();
    SpatialLocator spatialLocator = defaultHolographicDisplay.SpatialLocator();
    m_stationaryReferenceFrame = spatialLocator.CreateStationaryFrameOfReferenceAtCurrentLocation();

    // Create a local anchor, perhaps by positioning a SpatialAnchor a few meters in front of the user
    SpatialAnchor localAnchor{ nullptr };
    PerceptionTimestamp timestamp = PerceptionTimestampHelper::FromHistoricalTargetTime(DateTime::clock::now());
    SpatialCoordinateSystem currentCoordinateSystem = m_attachedReferenceFrame.GetStationaryCoordinateSystemAtTimestamp(timestamp);
    SpatialPointerPose pose = SpatialPointerPose::TryGetAtTimestamp(currentCoordinateSystem, timestamp);

    // Get the gaze direction relative to the given coordinate system.
    const float3 headPosition = pose.Head().Position();
    const float3 headDirection = pose.Head().ForwardDirection();

    // The anchor is positioned two meter(s) along the user's gaze direction.
    constexpr float distanceFromUser = 2.0f; // meters
    const float3 gazeAtTwoMeters = headPosition + (distanceFromUser * headDirection);

    localAnchor = SpatialAnchor::TryCreateRelativeTo(currentCoordinateSystem, gazeAtTwoMeters);

    // If the user is placing some application content in their environment,
    // you might show content at this anchor for a while, then save when
    // the user confirms placement.
    CloudSpatialAnchor cloudAnchor = m_asafactory.CreateCloudSpatialAnchor();
    cloudAnchor.LocalAnchor(localAnchor);
    co_await m_cloudSession.CreateAnchorAsync(cloudAnchor);
    m_feedback = LR"(Created a cloud anchor with ID=)" + cloudAnchor.Identifier();
```

[!INCLUDE [Session Status](../../../includes/spatial-anchors-create-locate-anchors-session-status.md)]

Další informace o [GetSessionStatusAsync](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession#getsessionstatusasync) metody.

```cpp
    SessionStatus status = co_await m_cloudSession.GetSessionStatusAsync();
    if (value.RecommendedForCreateProgress() < 1.0f) return;
    // Issue the creation request ...
```

[!INCLUDE [Setting Properties](../../../includes/spatial-anchors-create-locate-anchors-setting-properties.md)]

Další informace o [objekt AppProperties](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchor#appproperties) metody.

```cpp
    CloudSpatialAnchor cloudAnchor = m_asafactory.CreateCloudSpatialAnchor();
    cloudAnchor.LocalAnchor(localAnchor);
    auto properties = m_cloudAnchor.AppProperties();
    properties.Insert(LR"(model-type)", LR"(frame)");
    properties.Insert(LR"(label)", LR"(my latest picture)");
    co_await m_cloudSession.CreateAnchorAsync(cloudAnchor);
```

[!INCLUDE [Update Anchor Properties](../../../includes/spatial-anchors-create-locate-anchors-updating-properties.md)]

Další informace o [UpdateAnchorPropertiesAsync](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession#updateanchorpropertiesasync) metody.

```cpp
    CloudSpatialAnchor anchor = /* locate your anchor */;
    anchor.AppProperties().Insert(LR"(last-user-access)", LR"(just now)");
    co_await m_cloudSession.UpdateAnchorPropertiesAsync(anchor);
```

[!INCLUDE [Getting Properties](../../../includes/spatial-anchors-create-locate-anchors-getting-properties.md)]

Další informace o [GetAnchorPropertiesAsync](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession#getanchorpropertiesasync) metody.

```cpp
    CloudSpatialAnchor anchor = co_await m_cloudSession.GetAnchorPropertiesAsync(LR"(anchorId)");
    if (anchor != nullptr)
    {
        anchor.AppProperties().Insert(LR"(last-user-access)", LR"(just now)");
        co_await m_cloudSession.UpdateAnchorPropertiesAsync(anchor);
    }
```

[!INCLUDE [Expiration](../../../includes/spatial-anchors-create-locate-anchors-expiration.md)]

Další informace o [vypršení platnosti](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchor#expiration) metody.

```cpp
    const int64_t oneWeekFromNowInHours = 7 * 24;
    const DateTime oneWeekFromNow = DateTime::clock::now() + std::chrono::hours(oneWeekFromNowInHours);
    cloudAnchor.Expiration(oneWeekFromNow);
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating.md)]

Další informace o [CreateWatcher](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession#createwatcher) metody.

```cpp
    AnchorLocateCriteria criteria = m_asafactory.CreateAnchorLocateCriteria();
    criteria.Identifiers({ LR"(id1)", LR"(id2)", LR"(id3)" });
    auto cloudSpatialAnchorWatcher = m_cloudSession.CreateWatcher(criteria);
```

[!INCLUDE [Locate Events](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

Další informace o [AnchorLocatedDelegate](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/anchorlocateddelegate) delegovat.

```cpp
    m_anchorLocatedToken = m_cloudSession.AnchorLocated(winrt::auto_revoke, [this](auto&&, auto&& args)
    {
        switch (args.Status())
        {
            case LocateAnchorStatus::Located:
            {
                CloudSpatialAnchor foundAnchor = args.Anchor();
            }
                break;
            case LocateAnchorStatus::AlreadyTracked:
                // This anchor has already been reported and is being tracked
                break;
            case LocateAnchorStatus::NotLocatedAnchorDoesNotExist:
                // The anchor was deleted or never existed in the first place
                // Drop it, or show UI to ask user to anchor the content anew
                break;
            case LocateAnchorStatus::NotLocated:
                // The anchor hasn't been found given the location data
                // The user might in the wrong location, or maybe more data will help
                // Show UI to tell user to keep looking around
                break;
        }
    });
```

[!INCLUDE [Deleting](../../../includes/spatial-anchors-create-locate-anchors-deleting.md)]

Další informace o [DeleteAnchorAsync](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession#deleteanchorasync) metody.

```cpp
    co_await m_cloudSession.DeleteAnchorAsync(cloudAnchor);
    // Perform any processing you may want when delete finishes
```

[!INCLUDE [Stopping](../../../includes/spatial-anchors-create-locate-anchors-stopping.md)]

Další informace o [Zastavit](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession#stop) metody.

```cpp
    m_cloudSession.Stop();
```

[!INCLUDE [Resetting](../../../includes/spatial-anchors-create-locate-anchors-resetting.md)]

Další informace o [resetování](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession#reset) metody.

```cpp
    m_cloudSession.Reset();
```

[!INCLUDE [Cleanup](../../../includes/spatial-anchors-create-locate-anchors-cleanup-others.md)]

```cpp
    m_cloudSession = nullptr;
```

[!INCLUDE [Next Steps](../../../includes/spatial-anchors-create-locate-anchors-next-steps.md)]
