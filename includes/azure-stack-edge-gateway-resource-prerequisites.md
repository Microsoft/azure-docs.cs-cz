---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: c29c7abf0964edb7a5427d8c2cfe62e066d13bfb
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900841"
---
Než začnete, ujistěte se, že:

* Ve vašem předplatném Microsoft Azure je povolený prostředek Azure Stack Edge. Ujistěte se, že jste použili podporované předplatné, jako je například [Microsoft smlouva Enterprise (EA)](https://azure.microsoft.com/overview/sales-number/), [Cloud Solution Provider (CSP)](/partner-center/azure-plan-lp)nebo [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).
* Máte přístup vlastníka nebo přispěvatele na úrovni skupiny prostředků pro Azure Stackch hraničních zařízení/Azure Storage bránu, IoT Hub a prostředků Azure Storage.

  * Pokud chcete vytvořit Azure Stack hraničního prostředku, měli byste mít oprávnění jako Přispěvatel (nebo vyšší) vymezená na úrovni skupiny prostředků. Musíte se také ujistit, že `Microsoft.DataBoxEdge` je poskytovatel zaregistrován. Informace o registraci najdete v tématu [Registrace poskytovatele prostředků](../articles/databox-online/azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Pokud chcete vytvořit libovolný prostředek IoT Hub, ujistěte se, že je poskytovatel Microsoft. Devices zaregistrovaný. Informace o registraci najdete v tématu [Registrace poskytovatele prostředků](../articles/databox-online/azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Pokud chcete vytvořit prostředek účtu úložiště, musíte pro něj znovu použít rozsah přístupu přispěvatele nebo vyšší úrovně. Azure Storage je ve výchozím nastavení registrovaný poskytovatel prostředků.
* K Graph API Azure Active Directory máte oprávnění správce nebo uživatel. Další informace najdete v tématu [Azure Active Directory Graph API](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
* Máte účet služby Microsoft Azure Storage a přihlašovací údaje účtu.