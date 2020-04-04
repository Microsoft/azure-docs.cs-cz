---
title: Změnit cenovou úroveň oboru názvů Centra oznámení | Dokumenty společnosti Microsoft
description: Přečtěte si, jak změnit cenovou úroveň oboru názvů Centra oznámení Azure.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/02/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/28/2019
ms.openlocfilehash: 855a050afa14144f8963f24398c6b7b3939ef562
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656472"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>Změna cenové úrovně oboru názvů center oznamovacích center Azure

Centra oznámení jsou nabízena ve třech úrovních: **zdarma**, **základní**a **standardní**. Tento článek ukazuje, jak změnit cenovou úroveň pro obor názvů Centra oznámení Azure.

## <a name="overview"></a>Přehled

V centru oznámení Azure je centrum nejmenší prostředek nebo entita. Obecně se mapuje na jednu aplikaci a může mít jeden certifikát pro každý systém oznamování platformy (PNS), který podporujeme pro aplikaci. Aplikace může být hybridní nebo nativní a aplikace pro různé platformy.

**Obor názvů** je kolekce center oznámení. Každý obor názvů se obvykle skládá z rozbočovačů, které spolu souvisejí a používají se k určitému účelu. Můžete mít například tři různé obory názvů pro účely vývoje, testování a výroby.

Obor názvů můžete přidružit k **bezplatným**, **základním**nebo **standardním** cenovým úrovním. Vrstvu můžete použít pro obor názvů, který vyhovuje vašim požadavkům. V následujících částech se ukazuje, jak změnit cenovou úroveň oboru názvů Centra oznámení.

## <a name="use-azure-portal"></a>Použití webu Azure Portal

Při použití portálu Azure můžete změnit cenovou úroveň pro obor názvů na stránce oboru názvů nebo na centrální stránce. Když ji změníte na centrální stránce, ve skutečnosti ji změníte na úrovni oboru názvů. Změní cenovou úroveň pro obor názvů a všechny rozbočovače v oboru názvů.

### <a name="change-tier-on-the-namespace-page"></a>Změna úrovně na stránce oboru názvů

Následující postup ukazuje, jak změnit cenovou úroveň pro obor názvů na stránce oboru názvů. Když změníte úroveň pro obor názvů, platí pro všechny rozbočovače v tomto oboru názvů.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V levé nabídce vyberte **Všechny služby.**
3. V části **Internet věcí** vyberte **Obory názvů centra oznámení.** Pokud vyberete hvězdičku`*`( ) vedle textu, přidá se na levý navigační panel pod **položkou OBLÍBENÉ POLOŽKY**. To vám pomůže získat přístup k jmenovky stránky rychleji do budoucna. Po přidání do seznamu OBLÍBENÉ POLOŽKY vyberte **položku Obory názvů centra oznámení**.

    ![Všechny služby -> Obory názvů centra oznámení](./media/change-pricing-tier/all-services-nhub.png)

4. Na stránce **Obory názvů centra oznámení** vyberte obor názvů, pro který chcete změnit cenovou úroveň.
5. Na stránce **Obor názvů centra oznámení** pro obor názvů se v části **Essentials** zobrazí aktuální cenová úroveň oboru názvů. Na následujícím obrázku uvidíte, že cenová úroveň oboru názvů je **Zdarma**.

    ![Aktuální cenová úroveň na stránce oboru názvů](./media/change-pricing-tier/pricing-tier-before.png)

6. Na stránce **Obor názvů centra oznámení** pro obor názvů vyberte v části Spravovat v části **Spravovat** **možnost Cenová úroveň.**

    ![Výběr cenové úrovně na stránce oboru názvů](./media/change-pricing-tier/namespace-select-pricing-menu.png)

7. Změňte cenovou úroveň a klikněte na tlačítko **Vybrat.**
8. Stav akce změny vrstvy můžete zobrazit ve **výstrahách**.
9. Přepněte na stránku **Přehled.** Zkontrolujte, zda se nová úroveň zobrazí pro pole **Cenová úroveň** v části **Základy.**
10. Tento krok je volitelný. Vyberte libovolné centrum v oboru názvů. Potvrďte, že se v části **Essentials** zobrazuje stejná cenová úroveň. Měli byste vidět stejnou cenovou úroveň pro všechny rozbočovače v oboru názvů.

### <a name="change-tier-on-the-hub-page"></a>Změna úrovně na stránce centra

Následující postup ukazuje, jak změnit cenovou úroveň pro obor názvů na stránce centra. I když tyto kroky provedete od stránky centra, ve skutečnosti změníte cenovou úroveň pro obor názvů a všechny rozbočovače v oboru názvů.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V levé nabídce vyberte **Všechny služby.**
3. V části Internet **věcí** vyberte **Centra oznámení.**
4. Vyberte **centrum**oznámení .
5. V levé nabídce vyberte **Cenovou úroveň.**
6. Změňte cenovou úroveň a klikněte na tlačítko **Vybrat.** Tato akce změní nastavení cenové úrovně pro obor názvů, který obsahuje centrum. Takže se zobrazí nová cenová úroveň na stránce oboru názvů a na všech stránkách rozbočovače.

> [!NOTE]
> Všechny změny cenové úrovně jsou účinné okamžitě.

## <a name="use-rest-api"></a>Použití rozhraní REST API

Můžete použít následující zprostředkovatele prostředků REST API získat aktuální cenovou úroveň a aktualizovat ji.

### <a name="get-current-pricing-tier-for-a-namespace"></a>Získání aktuální cenové úrovně pro obor názvů

Chcete-li získat aktuální úroveň oboru názvů, odešlete příkaz GET, jak je znázorněno v následujícím příkladu:

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>Aktualizace cenové úrovně pro obor názvů

Chcete-li aktualizovat úroveň oboru názvů, odešlete příkaz PUT, jak je znázorněno v následujícím příkladu:

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```

## <a name="next-steps"></a>Další kroky

Další informace o těchto úrovních a cenách najdete v [tématu Ceny centra oznámení](https://azure.microsoft.com/pricing/details/notification-hubs/).
