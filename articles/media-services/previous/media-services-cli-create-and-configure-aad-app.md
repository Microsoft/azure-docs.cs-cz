---
title: Použití Azure CLI k vytvoření aplikace Azure AD a její konfiguraci pro přístup k rozhraní Azure Media Services API | Microsoft Docs
description: V tomto tématu se dozvíte, jak pomocí Azure CLI vytvořit aplikaci Azure AD a nakonfigurovat ji pro přístup k rozhraní Azure Media Services API.
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
ms.openlocfilehash: ceb01c97ec50f4e2a7aa146448c3da662d4314ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89258346"
---
# <a name="use-azure-cli-to-create-an-azure-ad-app-and-configure-it-to-access-media-services-api"></a>Použití Azure CLI k vytvoření aplikace Azure AD a její konfiguraci pro přístup k rozhraní Media Services API

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi [Media Services V3](../latest/index.yml). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-from-v2-to-v3.md) .

V tomto tématu se dozvíte, jak pomocí Azure CLI vytvořit aplikaci Azure Active Directory (Azure AD) a instančního objektu pro přístup k prostředkům Azure Media Services. 

## <a name="prerequisites"></a>Požadavky

- Účet Azure: Podrobnosti najdete v článku [bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Účet Media Services. Další informace najdete v tématu [Vytvoření účtu Azure Media Services pomocí Azure Portal](media-services-portal-create-account.md).

## <a name="use-the-azure-cloud-shell"></a>Použití Azure Cloud Shell

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).
2. Spusťte Cloud Shell v horním navigačním podokně portálu.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

Další informace najdete v tématu [přehled Azure Cloud Shell](../../cloud-shell/overview.md).

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-azure-cli"></a>Vytvoření aplikace Azure AD a konfigurace přístupu k účtu média pomocí Azure CLI
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> 
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Například:

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

V tomto příkladu je **oborem** úplná cesta prostředku pro účet Media Services. **Rozsah** ale může být na libovolné úrovni.

Může to být například jedna z následujících úrovní:
 
* Úroveň **předplatného** .
* Úroveň **skupiny prostředků** .
* Úroveň **prostředku** (například účet média).

Další informace najdete v tématu [Vytvoření instančního objektu Azure pomocí Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli) .

Další informace najdete také v tématu [správa Role-Based Access Control s rozhraním příkazového řádku Azure](../../role-based-access-control/role-assignments-cli.md). 

## <a name="next-steps"></a>Další kroky

Začněte s [nahráváním souborů na svůj účet](media-services-portal-upload-files.md).
