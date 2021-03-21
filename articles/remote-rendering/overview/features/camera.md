---
title: Camera
description: Popisuje nastavení kamery a případy použití.
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.openlocfilehash: dbe86313054706af974ccb324a39e942e9b5ca44
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594125"
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

Aby se zajistilo, že není možné nastavit žádné neplatné rozsahy, vlastnosti **NearPlane** a **FarPlane** jsou jen pro čtení a pro změnu rozsahu existují samostatné funkce **SetNearAndFarPlane** . Tato data budou odeslána na konec rámce na server. Při nastavování těchto hodnot musí být **NearPlane** menší než **FarPlane**. V opačném případě dojde k chybě.

> [!IMPORTANT]
> V Unity se to při změně hlavní kamery blízko i daleko řídí automaticky.

**EnableDepth**:

Někdy je vhodné zakázat zápis hloubky do vyrovnávací paměti pro účely ladění vzdálené image. Při deaktivaci se taky zastaví Server, aby odesílal data hloubky, což snižuje šířku pásma.

> [!TIP]
> V Unity je k dispozici součást ladění s názvem **EnableDepthComponent** , která se dá použít k přepínání této funkce v uživatelském rozhraní editoru.

**InverseDepth**:

> [!NOTE]
> Toto nastavení je důležité pouze v případě `EnableDepth` , že je nastavena na `true` . V opačném případě toto nastavení nemá žádný vliv.

Vyrovnávací paměti hloubky obvykle zaznamenávají hodnoty z v rozsahu s plovoucí desetinnou čárkou (0; 1], přičemž 0 označuje blízkou rovinu a 1 označuje hloubku nejvyšší roviny. Je také možné Invertovat tento rozsah a hodnoty hloubky záznamu v rozsahu [1; 0], to znamená, že blízko roviny je 1 a hloubka nejvyšší roviny bude 0. Obecně to znamená, že druhá vylepšuje distribuci přesnosti s plovoucí desetinnou čárkou v nelineárním rozsahu z.

> [!WARNING]
> Běžný přístup navertuje hodnoty téměř roviny a daleko v objektech fotoaparátu. Tato chyba se při vzdáleném vykreslování Azure nezdaří s chybou při pokusu o provedení této akce v `CameraSettings` .

Rozhraní API pro vzdálené vykreslování Azure potřebuje informace o tom, jak se má hloubka vyrovnávací paměti pro váš místní zobrazovací jednotku správně poznat do vyrovnávací paměti místní hloubky. Pokud je rozsah vyrovnávací paměti hloubky [0; 1], nechejte tento příznak jako `false` . Použijete-li vyrovnávací paměť s obrácenou hloubkou s rozsahem [1; 0], nastavte `InverseDepth` příznak na `true` .

> [!NOTE]
> V případě Unity je správné nastavení již použito, takže není `RenderingConnection` nutné provádět ruční zásah.

Změna nastavení kamery může probíhat takto:

```cs
void ChangeCameraSetting(RenderingSession session)
{
    CameraSettings settings = session.Connection.CameraSettings;

    settings.SetNearAndFarPlane(0.1f, 20.0f);
    settings.EnableDepth = false;
    settings.InverseDepth = false;
}
```

```cpp
void ChangeCameraSetting(ApiHandle<RenderingSession> session)
{
    ApiHandle<CameraSettings> settings = session->Connection()->GetCameraSettings();

    settings->SetNearAndFarPlane(0.1f, 20.0f);
    settings->SetEnableDepth(false);
    settings->SetInverseDepth(false);
}
```

## <a name="api-documentation"></a>Dokumentace k rozhraní API

* [C# CameraSettings](/dotnet/api/microsoft.azure.remoterendering.camerasettings)
* [CameraSettings C++](/cpp/api/remote-rendering/camerasettings)
* [Funkce C# GraphicsBindingSimD3d11. Update](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11.update)
* [C++ GraphicsBindingSimD3d11:: Update – funkce](/cpp/api/remote-rendering/graphicsbindingsimd3d11#update)

## <a name="next-steps"></a>Další kroky

* [Grafika – vazba](../../concepts/graphics-bindings.md)
* [Reprojekce pozdní fáze](late-stage-reprojection.md)