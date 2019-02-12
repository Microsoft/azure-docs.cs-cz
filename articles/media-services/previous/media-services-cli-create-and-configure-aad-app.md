---
title: Pomocí Azure CLI k vytvoření aplikace Azure AD a nakonfigurujte ho pro přístup k rozhraní API služby Azure Media Services | Dokumentace Microsoftu
description: Toto téma ukazuje, jak pomocí Azure CLI k vytvoření aplikace Azure AD a nakonfigurujte ho pro přístup k rozhraní API služby Azure Media Services.
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
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: 31a42e316dab3d751fffa3353200c0185c56ee9e
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55997118"
---
# <a name="use-azure-cli-to-create-an-azure-ad-app-and-configure-it-to-access-media-services-api"></a>Pomocí Azure CLI k vytvoření aplikace Azure AD a nakonfigurujte ho pro přístup k rozhraní API služby Media Services 

Toto téma ukazuje, jak pomocí Azure CLI pro vytvoření aplikace Azure Active Directory (Azure AD) a instanční objekt služby pro přístup k prostředkům Azure Media Services. 

## <a name="prerequisites"></a>Požadavky

- Účet Azure. Podrobnosti najdete na stránce [bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Účet Media Services. Další informace najdete v tématu [vytvoření účtu služby Azure Media Services pomocí webu Azure portal](media-services-portal-create-account.md).

## <a name="use-the-azure-cloud-shell"></a>Pomocí služby Azure Cloud Shell

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Spusťte Cloud Shell z horního navigačního podokna portálu.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

Další informace najdete v tématu [Přehled služby Azure Cloud Shell](../../cloud-shell/overview.md).

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-azure-cli"></a>Vytvoření aplikace Azure AD a konfigurovat přístup k účtu media pomocí Azure CLI
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> --password <strong password>
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Příklad:

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

V tomto příkladu **oboru** účet služby je cesta úplné prostředku pro médium. Ale **oboru** může být na libovolné úrovni.

Například to může být jeden z následujících úrovní:
 
* **Předplatné** úroveň.
* **Skupiny prostředků** úroveň.
* **Prostředků** úrovně (například účet média).

Další informace najdete v tématu [vytvoření instančního objektu Azure pomocí rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)

Viz také [Manage Role-Based řízení přístupu pomocí rozhraní příkazového řádku Azure](../../role-based-access-control/role-assignments-cli.md). 

## <a name="next-steps"></a>Další postup

Začínáme s [nahrávání souborů do účtu](media-services-portal-upload-files.md).
