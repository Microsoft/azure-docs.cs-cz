---
title: Přehled kotev objektů Azure
description: Přečtěte si, jak kotvy objektů Azure pomáhají detekovat objekty ve fyzickém světě.
author: craigktreasure
manager: vriveras
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: b4284411fbe09a0d0ce8412e6e5a5df464f35564
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462860"
---
# <a name="azure-object-anchors-overview"></a>Přehled kotev objektů Azure

Kotvy objektů Azure umožňují aplikaci detekovat objekt ve fyzickém světě pomocí 3D model a odhadnout jeho 6DoF pozici. 6DoF (6 stupňů volnosti) je definováno jako rotace a překlad mezi 3D model a jeho fyzickým protějškem, skutečným objektem.

Kotvy objektů Azure se skládají ze služby pro převod modelů a běhové klientské sady SDK pro HoloLens. Služba přijme model 3D Object a vytvoří výstup modelu kotev objektů Azure. Model ukotvení objektů Azure se používá společně s běhovou sadou SDK a umožňuje aplikaci HoloLens načíst objektový model, detekovat a sledovat instance daného modelu ve fyzickém světě.

:::image type="content" source="./media/aoa-overview.jpg" alt-text="Kotvy objektů Azure v akci":::

## <a name="examples"></a>Příklady

Mezi příklady případů použití povolené kotvami objektů Azure patří:

- **Trénování:** Vytvářejte hybridní prostředí pro práci s realitami pro vaše pracovní procesy, aniž byste museli umísťovat značky nebo čas strávený ruční úpravou hologramového zarovnání. Pokud chcete rozšířit vaše smíšená školicí prostředí pro realitu díky automatizovanému zjišťování a sledování, ingestujte model do služby kotvy objektů Azure a budete mít jeden krok blíž k možnosti bez označení.

- **Pokyny pro úlohy**. Vystavení zaměstnanců prostřednictvím sady úkolů může být při použití smíšené reality výrazně jednodušší. Překrytí digitálních instrukcí a osvědčených postupů na fyzických objektech – to znamená, že se jedná o zařízení v továrně nebo v kavárně v týmu – může značně snížit problémy při dokončování sady úkolů. Spuštění těchto zkušeností obvykle vyžaduje určitou formu značky nebo ručního zarovnání, ale s kotvami objektů Azure můžete vytvořit prostředí, které automaticky detekuje objekt související s úlohou. Pak plynule přesměrujte prostřednictvím doprovodných pokynů pro hybridní realitu bez značek nebo ručního zarovnání.

- **Hledání assetu**. Pokud už máte 3D model nějakého objektu ve fyzickém prostoru, kotvy objektů Azure vám umožní vyhledat a sledovat instance daného objektu ve svém fyzickém prostředí.

## <a name="next-steps"></a>Další kroky

V následujících částech najdete informace o tom, jak začít používat a sestavovat aplikace pomocí kotev objektů Azure.

> [!div class="nextstepaction"]
> [Přijímání modelů](quickstarts/get-started-model-conversion.md)

> [!div class="nextstepaction"]
> [Unity HoloLens](quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [HoloLens v Unity s MRTK](quickstarts/get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [HoloLens DirectX](quickstarts/get-started-hololens-directx.md)
