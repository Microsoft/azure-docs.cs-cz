---
title: Camera
description: Popisuje nastavení kamery a případy použití.
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.openlocfilehash: 5ad39ad9f5e585029cff5d573a026702f259607e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90564812"
---
# <a name="camera"></a>Camera

Aby bylo zajištěno, že místně a vzdáleně vykreslený obsah může být bez problémů složený ze všech vlastností kamery, musí mezi serverem a klientem zůstat synchronizované různé vlastnosti kamery. Hlavně pro transformaci a projekci kamery. Místně vykreslený obsah například musí používat stejnou transformaci a projekci kamery, pomocí kterých byl vygenerován nejnovější vzdálený rámec.

![Místní a vzdálený fotoaparát](./media/camera.png)

V imagi výše se místní fotoaparát přesunul ve srovnání se vzdáleným snímkem odeslaném serverem. V důsledku toho je potřeba vykreslit místní obsah ze stejné perspektivy, jako je vzdálený rámec, a nakonec se výsledný obraz znovu promítnout do místního prostoru kamery, který je podrobně vysvětlen v článku o [reprojekci v pozdní fázi](late-stage-reprojection.md).

Zpoždění mezi vzdáleným a místním vykreslováním znamená, že jakákoli změna těchto nastavení se projeví v případě zpoždění. Proto se žádná nová hodnota nedá použít hned stejný rámec.

U nastavení kamery je možné nastavit vzdálenosti blízko a daleko roviny. Na HoloLens 2 je zbývající data transformace a projekce definována hardwarem. Při použití [simulace plochy](../../concepts/graphics-bindings.md)se nastavují prostřednictvím vstupu jeho `Update` funkce.

Změněné hodnoty lze použít místně hned po doručení prvního vzdáleného rámce, který byl vykreslen s nimi. Na HoloLens 2 se data, která se mají použít pro vykreslování, poskytují *HolographicFrame* stejně jako v jakékoli jiné holografické aplikaci. V [simulaci plochy](../../concepts/graphics-bindings.md)jsou načítány prostřednictvím výstupu `Update` funkce.

## <a name="camera-settings"></a>Nastavení kamery

V nastaveních kamery lze změnit následující vlastnosti:

**Blízko a daleko rovina:**

Aby se zajistilo, že není možné nastavit žádné neplatné rozsahy, vlastnosti **NearPlane** a **FarPlane** jsou jen pro čtení a pro změnu rozsahu existují samostatné funkce **SetNearAndFarPlane** . Tato data budou odeslána na konec rámce na server.

> [!IMPORTANT]
> V Unity se to při změně hlavní kamery blízko i daleko řídí automaticky.

**EnableDepth**:

Někdy je vhodné zakázat zápis hloubky do vyrovnávací paměti pro účely ladění vzdálené image. Při deaktivaci se taky zastaví Server, aby odesílal data hloubky, což snižuje šířku pásma.

> [!TIP]
> V Unity je k dispozici součást ladění s názvem **EnableDepthComponent** , která se dá použít k přepínání této funkce v uživatelském rozhraní editoru.

Změna nastavení kamery může probíhat takto:

```cs
void ChangeCameraSetting(AzureSession session)
{
    CameraSettings settings = session.Actions.CameraSettings;

    settings.SetNearAndFarPlane(0.1f, 20.0f);
    settings.EnableDepth = false;
}
```

```cpp
void ChangeStageSpace(ApiHandle<AzureSession> session)
{
    ApiHandle<CameraSettings> settings = session->Actions()->GetCameraSettings();

    settings->SetNearAndFarPlane(0.1f, 20.0f);
    settings->SetEnableDepth(false);
}
```

## <a name="api-documentation"></a>Dokumentace k rozhraní API

* [C# CameraSettings](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.camerasettings)
* [CameraSettings C++](https://docs.microsoft.com/cpp/api/remote-rendering/camerasettings)
* [Funkce C# GraphicsBindingSimD3d11. Update](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11.update)
* [C++ GraphicsBindingSimD3d11:: Update – funkce](https://docs.microsoft.com/cpp/api/remote-rendering/graphicsbindingsimd3d11#update)

## <a name="next-steps"></a>Další kroky

* [Grafika – vazba](../../concepts/graphics-bindings.md)
* [Reprojekce pozdní fáze](late-stage-reprojection.md)
