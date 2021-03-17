---
title: Konfigurace pojmenovaných umístění ve službě Azure Active Directory | Microsoft Docs
description: Přečtěte si, jak konfigurovat pojmenovaná umístění.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.subservice: report-monitor
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94f4d17596936dd9d0ebbdae3c351cac9ed2a570
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89299860"
---
# <a name="quickstart-configure-named-locations-in-azure-active-directory"></a>Rychlý start: Konfigurace pojmenovaných umístění ve službě Azure Active Directory

Pomocí pojmenovaných umístění můžete označit důvěryhodné rozsahy IP adres ve vaší organizaci. Azure AD používá pojmenovaná umístění ke:
- Detekuje falešně pozitivní výsledky v [detekcích rizik](../identity-protection/overview-identity-protection.md). Přihlášení z důvěryhodného umístění snižuje riziko při přihlášení uživatele.   
- Nakonfigurujte [podmíněný přístup na základě umístění](../conditional-access/location-condition.md).

V tomto rychlém startu se dozvíte, jak nakonfigurovat pojmenovaná umístění ve vašem prostředí.

## <a name="prerequisites"></a>Předpoklady

Co budete potřebovat k dokončení tohoto rychlého startu:

* Tenanta Azure AD. Zaregistrujte si [bezplatnou zkušební verzi](https://azure.microsoft.com/trial/get-started-active-directory/). 
* Uživatel, který je globální správce pro daného tenanta.
* Rozsah IP adres, který je zřízený a důvěryhodný ve vaší organizaci. Rozsah IP adres musí být ve formátu **CIDR (Classless Interdomain Routing)** formátu.

## <a name="configure-named-locations"></a>Konfigurace pojmenovaných umístění

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).

2. V levém podokně vyberte **Azure Active Directory**a potom v části **zabezpečení** vyberte **podmíněný přístup** .

    ![Karta podmíněný přístup](./media/quickstart-configure-named-locations/entrypoint.png)

3. Na stránce **Podmíněný přístup** vyberte **Pojmenovaná umístění** a pak vyberte **Nové umístění**.

    ![Pojmenované umístění](./media/quickstart-configure-named-locations/namedlocation.png)

6. Vyplňte formulář na nové stránce. 

   * Do pole **Název** zadejte název pro pojmenované umístění.
   * Do pole **Rozsahy IP adres** zadejte rozsah IP adres ve formátu CIDR.  
   * Klikněte na **Create** (Vytvořit).
    
     ![Nové okno](./media/quickstart-configure-named-locations/61.png)

## <a name="next-steps"></a>Další kroky

Další informace naleznete v tématech:

- [Umístění jako podmínka v podmíněném přístupu](../conditional-access/concept-conditional-access-conditions.md#locations).
- [Sestava rizikových přihlášení](../identity-protection/overview-identity-protection.md).