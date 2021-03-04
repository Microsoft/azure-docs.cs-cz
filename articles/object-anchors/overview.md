---
title: Přehled kotev objektů Azure
description: Přečtěte si, jak kotvy objektů Azure pomáhají detekovat objekty ve fyzickém světě.
author: craigktreasure
manager: vriveras
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: cbe52004dddbe74aa02347c026028a8ffd4cf8d7
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034183"
---
# <a name="azure-object-anchors-overview"></a>Přehled kotev objektů Azure

Kotvy objektů Azure umožňují aplikaci detekovat objekt ve fyzickém světě pomocí 3D model a odhadnout jeho 6DoF pozici. 6DoF (6 stupňů volnosti) je definováno jako rotace a překlad mezi 3D model a jeho fyzickým protějškem, skutečným objektem. 

Kotvy objektů Azure se skládají ze spravované služby pro převod modelu a běhové klientské sady SDK pro HoloLens. Služba přijme model 3D Object a vytvoří výstup modelu kotev objektů Azure. Model ukotvení objektů Azure se používá společně s běhovou sadou SDK a umožňuje aplikaci HoloLens načíst objektový model, detekovat a sledovat instance daného modelu ve fyzickém světě.

:::image type="content" source="./media/aoa-overview.jpg" alt-text="Kotvy objektů Azure v akci":::

## <a name="examples"></a>Příklady

Mezi příklady případů použití povolené kotvami objektů Azure patří:

- **Trénování:** Vytvářejte hybridní prostředí pro práci s realitami pro vaše pracovní procesy, aniž byste museli umísťovat značky nebo čas strávený ruční úpravou hologramového zarovnání. Pokud chcete rozšířit vaše smíšené prostředí pro vzdělávání realit díky automatizovanému zjišťování a sledování, ingestujte model do služby kotvy objektů a budete mít jeden krok blíž k možnosti bez označení.

- **Pokyny pro úlohy**. Vystavení zaměstnanců prostřednictvím sady úkolů může být při použití smíšené reality výrazně jednodušší. Překrytí digitálních instrukcí a osvědčených postupů na fyzických objektech – to znamená, že se jedná o zařízení v továrně nebo v kavárně v týmu – může značně snížit problémy při dokončování sady úkolů. Spuštění těchto zkušeností obvykle vyžaduje určitou formu značky nebo ručního zarovnání, ale s kotvami objektů můžete vytvořit prostředí, které automaticky detekuje objekt související s úlohou. Pak plynule přesměrujte prostřednictvím doprovodných pokynů pro hybridní realitu bez značek nebo ručního zarovnání.

- **Hledání assetu**. Pokud již máte 3D model nějakého objektu ve fyzickém prostoru, kotvy objektů vám umožní vyhledat a sledovat instance daného objektu ve vašem fyzickém prostředí.

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
