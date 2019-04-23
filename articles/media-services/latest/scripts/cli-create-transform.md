---
title: Ukázkový skript Azure CLI – Vytvoření transformace | Microsoft Docs
description: K vytvoření transformace použijte skript Azure CLI.
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
ms.openlocfilehash: 028aceac240afd62619e2d7be4f6ced9522fc4de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60308817"
---
# <a name="cli-example-create-a-transform"></a>Příklad rozhraní příkazového řádku: Vytvoření transformace

Skript Azure CLI v tomto článku znázorňuje, jak vytvořit transformaci. Transformace popisují jednoduchý pracovní postup úloh pro zpracování videosouborů nebo zvukových souborů a často se označují jako předpisy. Vždy byste měli zkontrolovat, zda už neexistuje transformace s požadovaným názvem a předpisem. Pokud existuje, měli byste ji použít.

## <a name="prerequisites"></a>Požadavky 

[Vytvoření účtu Media Services](../create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>Další postup

Další příklady najdete v tématu [Ukázky Azure CLI](../cli-samples.md).
