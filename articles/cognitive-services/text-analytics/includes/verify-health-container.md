---
title: Ověření instance kontejneru stavů
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak ověřit instanci kontejneru stavů.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 83a9eeb7644d107a808494ad06a8bef91d471fe1
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779346"
---
### <a name="verify-that-a-container-is-running"></a>Ověření, že je kontejner spuštěný

1. Vyberte kartu **Přehled** a zkopírujte IP adresu.
1. Otevřete novou kartu prohlížeče a zadejte IP adresu. Zadejte například `http://<IP-address>:5000 (http://55.55.55.55:5000` ). Zobrazí se Domovská stránka kontejneru, která vám umožní zjistit, že je kontejner spuštěný.

    ![Zobrazte domovskou stránku kontejneru a ověřte, že je spuštěný.](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Kliknutím na odkaz **Popis rozhraní API služby** přejdete na stránku Swagger kontejneru.

1. Zvolte kterékoli rozhraní API pro **post** a vyberte **vyzkoušet**. Zobrazí se parametry, které obsahují příklad vstupu.

K dispozici je několik adres URL, které můžete použít také k ověření, že je kontejner spuštěný.

|Žádost|Účel|
|--|--|
|`http://localhost:5000/`|Kontejner poskytuje domovskou stránku.|
|`http://localhost:5000/ready`|Požadováno pomocí GET, poskytuje ověření, že je kontejner připraven přijmout dotaz na model. Tento požadavek se dá použít k Kubernetesi [živých a připravených sond](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/).|
|`http://localhost:5000/status`|Požadavek s GET, jako koncový bod/Ready, ověří, že je kontejner spuštěný, aniž by se musel dotazovat na model. Tento požadavek se dá použít k Kubernetesi [živých a připravených sond](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/).|
|`http://localhost:5000/swagger`|Prostřednictvím této adresy URL kontejner poskytuje úplnou sadu dokumentace pro koncové body a `Try it now` funkci. Pomocí této funkce můžete zadat nastavení do webového formuláře HTML a vytvořit dotaz bez nutnosti psát jakýkoli kód. Jakmile se dotaz vrátí, je k dispozici vzorový příkaz SLOŽENÉho příkazu, který předvádí hlavičku protokolu HTTP a požadovaný formát textu. |
|`http://localhost:5000/demo`| Tato funkce, která se požaduje prostřednictvím prohlížeče, poskytuje interaktivní vizualizaci výsledků dotazů na ukázky vstupních textů nebo tu, kterou zadáte.  |

Pomocí této adresy URL žádosti můžete `http://localhost:5000/text/analytics/v3.2-preview.1/entities/health` Odeslat dotaz do kontejneru.
