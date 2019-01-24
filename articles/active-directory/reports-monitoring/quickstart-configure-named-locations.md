---
title: Konfigurace pojmenovaných umístění ve službě Azure Active Directory | Microsoft Docs
description: Přečtěte si, jak konfigurovat pojmenovaná umístění.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.component: report-monitor
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 45d3cebf9b410cb51106ac9255ff3ad461ea47d3
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54818043"
---
# <a name="quickstart-configure-named-locations-in-azure-active-directory"></a>Rychlý start: Konfigurace pojmenovaných umístění ve službě Azure Active Directory

Pomocí pojmenovaných umístění můžete označit důvěryhodné rozsahy IP adres ve vaší organizaci. Azure AD používá pojmenovaná umístění ke:
- Zjištění falešně pozitivních výsledků v [rizikových událostech](concept-risk-events.md). Přihlášení z důvěryhodného umístění snižuje riziko při přihlášení uživatele.   
- Konfiguraci [podmíněného přístupu na základě umístění](../conditional-access/location-condition.md).

V tomto rychlém startu se dozvíte, jak nakonfigurovat pojmenovaná umístění ve vašem prostředí.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu je potřeba:

* Tenanta Azure AD. Zaregistrujte se k [bezplatné zkušební verzi](https://azure.microsoft.com/trial/get-started-active-directory/). 
* Uživatel, který je globální správce pro daného tenanta.
* Rozsah IP adres, který je zřízený a důvěryhodný ve vaší organizaci. Rozsah IP adres musí být ve formátu **CIDR (Classless Interdomain Routing)** formátu.

## <a name="configure-named-locations"></a>Konfigurace pojmenovaných umístění

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. V levém podokně vyberte **Azure Active Directory** a pak v části **Zabezpečení** vyberte **Podmíněný přístup**.

    ![Karta Podmíněný přístup](./media/quickstart-configure-named-locations/entrypoint.png)

3. Na stránce **Podmíněný přístup** vyberte **Pojmenovaná umístění** a pak vyberte **Nové umístění**.

    ![Pojmenované umístění](./media/quickstart-configure-named-locations/namedlocation.png)

6. Vyplňte formulář na nové stránce. 

    * Do pole **Název** zadejte název pro pojmenované umístění.
    * Do pole **Rozsahy IP adres** zadejte rozsah IP adres ve formátu CIDR.  
    * Klikněte na možnost **Vytvořit**.
    
    ![Nové okno](./media/quickstart-configure-named-locations/61.png)

## <a name="next-steps"></a>Další postup

Další informace naleznete v tématu:

- [Přístup ke službě Azure AD podmíněné](../active-directory-conditional-access-azure-portal.md).
- [Podmínky umístění v podmíněném přístupu Azure AD](../conditional-access/location-condition.md)
- [Sestavy rizikových přihlášení](concept-risky-sign-ins.md).  
