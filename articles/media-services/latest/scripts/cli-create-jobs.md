---
title: Ukázkový skript Azure CLI – Vytvoření a spuštění úlohy | Microsoft Docs
description: Skript Azure CLI v tomto tématu znázorňuje, jak odeslat úlohu k transformaci s jednoduchým kódováním pomocí adresy URL s protokolem HTTPs.
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
ms.date: 11/11/2018
ms.author: juliako
ms.openlocfilehash: 11e148181568f0c550b5eb094055c21c47c00cfc
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51615819"
---
# <a name="cli-example-create-and-submit-a-job"></a>Ukázka CLI: Vytvoření a odeslání úlohy

Skript Azure CLI v tomto článku znázorňuje, jak vytvořit a odeslat úlohu k transformaci s jednoduchým kódováním pomocí adresy URL s protokolem HTTPs.

## <a name="prerequisites"></a>Požadavky 

- Nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 

    V současné době všechny [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) příkazy fungují ve službě Azure Cloud Shell. Doporučujeme používat rozhraní příkazového řádku místně.

- [Vytvoření účtu Media Services](../create-account-cli-how-to.md).

## <a name="example-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-jobs/Create-Jobs.sh "Create and submit jobs")]

## <a name="next-steps"></a>Další postup

Další příklady najdete v tématu [Ukázky Azure CLI](../cli-samples.md).
