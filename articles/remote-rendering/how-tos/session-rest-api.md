---
title: REST API správy relací
description: Popisuje, jak spravovat relace.
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 4323884b2dee3eeccfe71ec7817d92467450e88e
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950021"
---
# <a name="use-the-session-management-rest-api"></a>Použití rozhraní REST API pro správu relací

Pokud chcete používat funkci vzdáleného vykreslování Azure, musíte vytvořit *relaci*. Každá relace odpovídá serveru přidělovanému v Azure, ke kterému se může klientské zařízení připojit. Když se zařízení připojí, server vykreslí požadovaná data a zachová výsledek jako datový proud videa. Během vytváření relace jste zvolili [druh serveru](../reference/vm-sizes.md) , na kterém chcete běžet, což určuje ceny. Jakmile už relace není potřebná, měla by se zastavit. Pokud se nezastaví ručně, automaticky se ukončí, až vyprší *doba zapůjčení* relace.

## <a name="rest-api-reference"></a>REST API – referenční informace

Odkaz na [REST API najdete tady a definice](/rest/api/mixedreality/2021-01-01preview/remoterendering) Swagger [tady](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality).
Ve složce *Scripts* poskytujeme skript prostředí PowerShell *RenderingSession.ps1*, který ukazuje použití naší služby v [úložišti ukázek ARR](https://github.com/Azure/azure-remote-rendering) . Skript a jeho konfigurace jsou popsané tady: [Příklady skriptů PowerShellu](../samples/powershell-example-scripts.md).
Poskytujeme také sady SDK pro [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/remoterendering/Azure.MixedReality.RemoteRendering/README.md) a [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/remoterendering/azure-mixedreality-remoterendering/README.md).

> [!IMPORTANT]
> Latence je důležitým faktorem při použití vzdáleného vykreslování. Nejlepších výsledků dosáhnete, když vytvoříte relace v oblasti, která je pro vás nejblíže. [Test latence Azure](https://www.azurespeed.com/Azure/Latency) se dá použít k určení, které oblasti jsou pro vás nejblíže.

> [!IMPORTANT]
> Sada SDK modulu runtime ARR je nutná pro připojení klientského zařízení k relaci vykreslování. Tyto sady SDK jsou k dispozici v [rozhraní .NET](/dotnet/api/microsoft.azure.remoterendering) a [C++](/cpp/api/remote-rendering/). Kromě připojení ke službě můžete tyto sady SDK také použít ke spouštění a zastavování relací.

## <a name="next-steps"></a>Další kroky

* [Použití rozhraní API Azure front-endu k ověřování](frontend-apis.md)
* [Ukázkové skripty PowerShellu](../samples/powershell-example-scripts.md)