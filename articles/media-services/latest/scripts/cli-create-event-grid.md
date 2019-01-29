---
title: Ukázkový skript Azure CLI – Vytvoření předplatného služby Azure Event Grid | Microsoft Docs
description: Skript Azure CLI v tomto tématu znázorňuje, jak vytvořit předplatné služby Event Grid na úrovni účtu pro změny stavu úlohy.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/25/2019
ms.author: juliako
ms.openlocfilehash: 2fcacd559db0ab143ffa7ed49bbd546785765154
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55098901"
---
# <a name="cli-example-create-an-azure-event-grid-subscription"></a>Příklad rozhraní příkazového řádku: Vytvořit odběr služby Azure Event Grid 

Skript Azure CLI v tomto článku znázorňuje, jak vytvořit předplatné služby Event Grid na úrovni účtu pro změny stavu úlohy.

## <a name="prerequisites"></a>Požadavky 

[Vytvoření účtu Media Services](../create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-event-grid/Create-EventGrid.sh "Create an EventGrid subscription")]

## <a name="next-steps"></a>Další postup

Další příklady najdete v tématu [Ukázky Azure CLI](../cli-samples.md).
