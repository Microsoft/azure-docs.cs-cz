---
title: Nahrání obsahu do Azure Media Services prostředku pomocí Azure CLI
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
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7b362ba30baf13211dc41a6043dcedd05bd506ea
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585406"
---
# <a name="create-an-asset"></a>Vytvoření prostředku

Tento článek ukazuje, jak vytvořit prostředek Media Services.  K uchování mediálního obsahu pro kódování a streamování použijete Asset.  Další informace o Media Services assety najdete [v článku Azure Media Services V3](assets-concept.md) .

## <a name="prerequisites"></a>Požadavky

Postupujte podle kroků v části [Vytvoření účtu Media Services](./create-account-howto.md) a vytvořte potřebný Media Services účet a skupinu prostředků pro vytvoření assetu.

## <a name="methods"></a>Metody

## <a name="cli"></a>[Rozhraní příkazového řádku](#tab/cli/)

[!INCLUDE [Create an asset with CLI](./includes/task-create-asset-cli.md)]

## <a name="cli-shell"></a>[Prostředí CLI](#tab/clishell/)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

---

## <a name="next-steps"></a>Další kroky

[Správa prostředků](manage-asset-concept.md)
