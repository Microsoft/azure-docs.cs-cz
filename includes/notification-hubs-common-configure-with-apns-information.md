---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 537c86512db4706077d0089a4f71ee945623c26b
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081374"
---
### <a name="configure-your-notification-hub-with-apns-information"></a>Konfigurace centra oznámení s použitím informací o službě APNS

V části **Notification Services**vyberte **Apple** a pak postupujte podle příslušných kroků na základě postupu, který jste zvolili dříve v části [Vytvoření certifikátu pro Notification Hubs](#creating-a-certificate-for-notification-hubs) .  

> [!NOTE]
> Pro **režim aplikace** použijte **pouze v případě, že chcete** odesílat nabízená oznámení uživatelům, kteří si zakoupili aplikaci ze Storu.

### <a name="option-1-using-a-p12-push-certificate"></a>MOŽNOST 1: použití nabízeného certifikátu. P12

1. Vyberte **Certifikát**.

1. Vyberte ikonu souboru.

1. Vyberte soubor. p12, který jste dříve exportovali, a pak vyberte **otevřít**.

1. V případě potřeby zadejte správné heslo.

1. Vyberte režim **Izolovaný prostor**.

1. Vyberte **Uložit**.

### <a name="option-2-using-token-based-authentication"></a>MOŽNOST 2: použití ověřování založeného na tokenech

1. Vyberte **token**.
1. Zadejte následující hodnoty, které jste získali dříve:

    - **ID klíče**
    - **ID sady prostředků**
    - **ID týmu**
    - **Token**

1. Vyberte možnost **izolovaný prostor**.
1. Vyberte **Uložit**.
