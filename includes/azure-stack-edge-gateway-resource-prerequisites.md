---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 354811b1db31731913f2a99287b0e4a71b2aff61
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96468204"
---
Než začnete, ujistěte se, že:

* Ve vašem předplatném Microsoft Azure je povolený prostředek Azure Stack Edge. Ujistěte se, že jste použili podporované předplatné, jako je například [Microsoft smlouva Enterprise (EA)](https://azure.microsoft.com/overview/sales-number/), [Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp)nebo [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).
* Máte přístup vlastníka nebo přispěvatele na úrovni skupiny prostředků pro Azure Stackch hraničních zařízení/Azure Storage bránu, IoT Hub a prostředků Azure Storage.

  * Pokud chcete vytvořit Azure Stack hraničního prostředku, měli byste mít oprávnění jako Přispěvatel (nebo vyšší) vymezená na úrovni skupiny prostředků. Musíte se také ujistit, že `Microsoft.DataBoxEdge` je poskytovatel zaregistrován. Informace o registraci najdete v tématu [Registrace poskytovatele prostředků](../articles/databox-online/azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Pokud chcete vytvořit libovolný prostředek IoT Hub, ujistěte se, že je poskytovatel Microsoft. Devices zaregistrovaný. Informace o registraci najdete v tématu [Registrace poskytovatele prostředků](../articles/databox-online/azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Pokud chcete vytvořit prostředek účtu úložiště, musíte pro něj znovu použít rozsah přístupu přispěvatele nebo vyšší úrovně. Azure Storage je ve výchozím nastavení registrovaný poskytovatel prostředků.
* K Graph API Azure Active Directory máte oprávnění správce nebo uživatel. Další informace najdete v tématu [Azure Active Directory Graph API](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
* Máte účet služby Microsoft Azure Storage a přihlašovací údaje účtu.

