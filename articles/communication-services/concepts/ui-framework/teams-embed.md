---
title: Vložení týmů architektury uživatelského rozhraní
titleSuffix: An Azure Communication Services quickstart
description: V tomto dokumentu se dozvíte, jak se dají možnosti vložení týmů rozhraní .NET Communication Services UI použít k sestavování klíč volání.
author: tophpalmer
ms.author: chpalm
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: e55cfb1a4dff7bfda2323e68777d6f50514b1608
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105930230"
---
# <a name="teams-embed"></a>Týmy vkládají

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]


Vložení týmů je funkce komunikačních služeb Azure zaměřené na vzájemné interakce mezi uživatelem a obchodním voláním. Jádrem systému pro vložení týmů je [video a hlasové volání](../voice-video-calling/calling-sdk-features.md), ale týmy vkládají systémová sestavení v voláních primitiv Azure, aby poskytovaly ucelené uživatelské prostředí na základě schůzek Microsoft Teams.

Týmy vkládají sady SDK jsou uzavřené a zpřístupňují je v klíč složeném formátu. Odřadíte týmy do plátna vaší aplikace a sada SDK vytvoří kompletní uživatelské prostředí. Vzhledem k tomu, že se toto uživatelské prostředí velmi podobá schůzkám Microsoft teams, můžete využít tyto možnosti:

- Zkrácená doba vývoje a složitost strojírenství
- Znalost koncových uživatelů s týmy
- Možnost opakovaně používat týmy pro [školicí obsah koncových uživatelů](https://support.microsoft.com/office/meetings-in-teams-e0b0ae21-53ee-4462-a50d-ca9b9e217b67)

Vložení týmů poskytuje většinu funkcí, které jsou v týmu schůzky podporované, včetně:

- Prostředí pro předběžnou schůzku, kde uživatel nakonfiguruje zvuková zařízení a videosoubory
- Prostředí v průběhu schůzky pro konfiguraci zvukových a obrazových zařízení
- [Video pozadí](https://support.microsoft.com/office/change-your-background-for-a-teams-meeting-f77a2381-443a-499d-825e-509a140f4780): umožňuje účastníkům Rozostřit nebo nahradit jejich pozadí.
- [Několik možností pro](https://support.microsoft.com/office/using-video-in-microsoft-teams-3647fc29-7b92-4c26-8c2d-8a596904cdae) rozsáhlou galerii video galerie, dohromady režim, fokus, připnutí a Spotlight
- [Sdílení obsahu](https://support.microsoft.comoffice/share-content-in-a-meeting-in-teams-fcc2bf59-aecd-4481-8f99-ce55dd836ce8#ID0EABAAA=Mobile): umožnění účastníků sdílet svoji obrazovku

Další informace o tomto uživatelském rozhraní v porovnání s jinými sadami SDK pro komunikaci Azure najdete v tématu [Úvod do konceptu sady SDK uživatelského rozhraní](ui-sdk-overview.md). 
