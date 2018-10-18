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
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: eae9947b7dcbd6f2c52fb0d4abc65375aed7766e
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378784"
---
# <a name="cli-example-create-an-azure-event-grid-subscription"></a>Ukázka CLI: Vytvoření předplatného služby Azure Event Grid 

Skript Azure CLI v tomto článku znázorňuje, jak vytvořit předplatné služby Event Grid na úrovni účtu pro změny stavu úlohy.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0.20 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-event-grid/Create-EventGrid.sh "Create an EventGrid subscription")]

## <a name="next-steps"></a>Další kroky

Další příklady najdete v tématu [Ukázky Azure CLI](../cli-samples.md).
