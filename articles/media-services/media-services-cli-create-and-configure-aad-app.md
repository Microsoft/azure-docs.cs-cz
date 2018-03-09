---
title: "Pro vytvoření aplikace Azure AD a nakonfigurujte ho pro přístup k rozhraní API služby Azure Media Services použít 2.0 rozhraní příkazového řádku | Microsoft Docs"
description: "Toto téma ukazuje, jak používat 2.0 rozhraní příkazového řádku k vytvoření aplikace Azure AD a nakonfigurovat ji pro přístup k rozhraní API služby Azure Media Services."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.openlocfilehash: 205e554fa42b1c28ed78add3e84c046855875872
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="use-cli-20-to-create-an-aad-app-and-configure-it-to-access-azure-media-services-api"></a>Pomocí rozhraní příkazového řádku 2.0 vytvořit aplikaci AAD a konfigurovat ho pro přístup k rozhraní API služby Azure Media Services

Toto téma ukazuje, jak vytvořit aplikaci Azure Active Directory (Azure AD) a objektu zabezpečení pro přístup k prostředkům Azure Media Services pomocí rozhraní příkazového řádku 2.0. 

## <a name="prerequisites"></a>Požadavky

- Účet Azure. Podrobnosti najdete na stránce [bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Účet Media Services. Další informace najdete v tématu [vytvoření účtu Azure Media Services pomocí webu Azure portal](media-services-portal-create-account.md).

## <a name="use-the-azure-cloud-shell"></a>Použití prostředí cloudu Azure

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Spusťte prostředí cloudu v horním navigačním podokně portálu.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

Další informace najdete v tématu [Přehled prostředí cloudu Azure](../cloud-shell/overview.md).

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-cli-20"></a>Vytvoření aplikace Azure AD a konfigurace přístupu k účtu media s 2.0 rozhraní příkazového řádku
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> --password <strong password>
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Příklad:

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

V tomto příkladu **oboru** je cesta úplné prostředku pro médium účet služby. Ale **oboru** může být na všech úrovních.

Například je může použít jeden z následujících úrovní:
 
* **Předplatné** úroveň.
* **Skupiny prostředků** úroveň.
* **Prostředků** úrovně (například účet Media).

Další informace najdete v tématu [vytvořit objekt služby Azure pomocí Azure CLI 2.0](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)

Viz také [Manage Role-Based řízení přístupu pomocí rozhraní příkazového řádku Azure](../active-directory/role-based-access-control-manage-access-azure-cli.md). 

## <a name="next-steps"></a>Další postup

Začínáme s [nahrávání souborů do účtu](media-services-portal-upload-files.md).
