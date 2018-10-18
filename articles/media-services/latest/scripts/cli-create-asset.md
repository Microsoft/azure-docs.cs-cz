---
title: Ukázkový skript Azure CLI – Vytvoření účtu prostředku Azure Media Services| Microsoft Docs
description: Skript Azure CLI v tomto tématu znázorňuje, jak vytvořit asset služby Azure Media Services, do kterého je možné nahrát obsah.
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
ms.date: 10/09/2018
ms.author: juliako
ms.openlocfilehash: d2fe1718999bcdc33fcde6891fa891373a9ea5cb
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49069348"
---
# <a name="cli-example-create-an-asset"></a>Příklad rozhraní příkazového řádku: vytvoření prostředku

Skript Azure CLI v tomto článku znázorňuje, jak vytvořit asset služby Azure Media Services, do kterého je možné nahrát obsah.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0.20 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

## <a name="next-steps"></a>Další kroky

Další příklady najdete v tématu [Ukázky Azure CLI](../cli-samples.md).
