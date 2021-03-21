---
title: Nahrání obsahu do příkazového řádku assetu
description: Skript Azure CLI v tomto tématu znázorňuje, jak vytvořit asset služby Azure Media Services, do kterého je možné nahrát obsah.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/16/2021
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3923394e76d5ce4001d3652ae9cbc263df08e4e2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100545611"
---
# <a name="create-an-asset"></a>Vytvoření prostředku

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Tento článek ukazuje, jak vytvořit prostředek Media Services.  K uchování mediálního obsahu pro kódování a streamování použijete Asset.  Další informace o Media Services assety najdete [v článku Azure Media Services V3](assets-concept.md) .

## <a name="prerequisites"></a>Předpoklady

Postupujte podle kroků v části [Vytvoření účtu Media Services](./create-account-howto.md) a vytvořte potřebný Media Services účet a skupinu prostředků pro vytvoření assetu.

## <a name="methods"></a>Metody

## <a name="cli"></a>[Rozhraní příkazového řádku](#tab/cli/)

[!INCLUDE [Create an asset with CLI](./includes/task-create-asset-cli.md)]

## <a name="example-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

## <a name="rest"></a>[REST](#tab/rest/)

### <a name="using-rest"></a>S využitím rozhraní REST

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-rest.md)]

### <a name="using-curl"></a>S využitím cURL

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-curl.md)]

## <a name="net"></a>[.NET](#tab/net/)

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-dotnet.md)]

---

## <a name="next-steps"></a>Další kroky

[Přehled Media Services](media-services-overview.md)
