---
title: Pomocí rozhraní příkazového příkazu Azure vytvořte aplikaci Azure AD a nakonfigurujte ji pro přístup k rozhraní API Azure Media Services | Dokumenty společnosti Microsoft
description: Toto téma ukazuje, jak pomocí rozhraní api Azure vytvořit aplikaci Azure AD a nakonfigurovat ji pro přístup k rozhraní API Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2019
ms.author: juliako
ms.openlocfilehash: f136fb666e93adc0fe92aee014e3da9a37bbd6aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70035800"
---
# <a name="use-azure-cli-to-create-an-azure-ad-app-and-configure-it-to-access-media-services-api"></a>Použití rozhraní příkazového příkazu Azure k vytvoření aplikace Azure AD a její konfiguraci pro přístup k rozhraní API mediálních služeb 

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Viz také [pokyny k migraci z v2 na v3](../latest/migrate-from-v2-to-v3.md)

Toto téma ukazuje, jak pomocí rozhraní příkazového příkazu Azure vytvořit aplikaci azure active directory (Azure AD) a instancí pro přístup k prostředkům Azure Media Services. 

## <a name="prerequisites"></a>Požadavky

- Účet Azure. Podrobnosti najdete v [tématu bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Účet Media Services. Další informace najdete [v tématu Vytvoření účtu Mediální chod Azure pomocí portálu Azure](media-services-portal-create-account.md).

## <a name="use-the-azure-cloud-shell"></a>Použití cloudového prostředí Azure

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Spusťte prostředí Cloud Shell z horního navigačního podokna portálu.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

Další informace najdete v [tématu Přehled Azure Cloud Shell](../../cloud-shell/overview.md).

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-azure-cli"></a>Vytvoření aplikace Azure AD a konfigurace přístupu k účtu médií pomocí rozhraní příkazového příkazu Azure
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> 
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Například:

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

V tomto příkladu je **obor** úplnou cestou prostředku pro účet mediálních služeb. **Obor** však může být na libovolné úrovni.

Může to být například jedna z následujících úrovní:
 
* Úroveň **předplatného.**
* Úroveň **skupiny prostředků.**
* Úroveň **prostředků** (například účet Média).

Další informace najdete [v tématu Vytvoření hlavního povinného poskytování služeb Azure pomocí příkazového příkazu k příkazu Konzumu Azure](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)

Viz také [správa řízení přístupu na základě rolí pomocí rozhraní příkazového řádku Azure](../../role-based-access-control/role-assignments-cli.md). 

## <a name="next-steps"></a>Další kroky

Začínáme s [nahráváním souborů do svého účtu](media-services-portal-upload-files.md).
