---
title: Změna cenové úrovně oboru názvů Notification Hubs | Microsoft Docs
description: Naučte se, jak změnit cenovou úroveň oboru názvů Azure Notification Hubs.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/03/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/28/2019
ms.openlocfilehash: 1455259bc42aea9d506a9a2a19d725cac3d643f8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87562765"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>Změna cenové úrovně oboru názvů centra oznámení Azure

Notification Hubs se nabízí na třech úrovních: **Free**, **Basic** a **Standard**. V tomto článku se dozvíte, jak změnit cenovou úroveň pro obor názvů Azure Notification Hubs.

## <a name="overview"></a>Přehled

V Azure Notification Hubs je *Centrum oznámení* nejmenším prostředkem/entitou. Obecně se mapuje na jednu aplikaci a může obsahovat jeden certifikát pro každý systém oznámení platformy (PNS), který podporujeme pro aplikaci. Aplikace může být hybridní nebo nativní a aplikace pro více platforem.

*Obor názvů* je kolekce Center oznámení. Každý obor názvů se obvykle skládá z rozbočovačů, které se vztahují k určitému účelu a používají je. Například můžete mít tři různé obory názvů pro vývoj, testování a produkční účely, v uvedeném pořadí.

Obor názvů můžete přidružit k cenovým úrovním **Free**, **Basic** nebo **Standard** . Pro libovolný obor názvů můžete použít vrstvu, která vyhovuje vašim požadavkům. V následujících částech se dozvíte, jak změnit cenovou úroveň oboru názvů Notification Hubs.

## <a name="use-azure-portal"></a>Použití webu Azure Portal

Při použití Azure Portal můžete změnit cenovou úroveň oboru názvů na stránce oboru názvů nebo na stránce centra. Když ho změníte na stránce centra, ve skutečnosti ho změníte na úrovni oboru názvů. Změní cenovou úroveň pro obor názvů a všechna centra v oboru názvů.

### <a name="change-tier-on-the-namespace-page"></a>Změna úrovně na stránce oboru názvů

Následující postup ukazuje, jak změnit cenovou úroveň oboru názvů na stránce oboru názvů. Když změníte úroveň oboru názvů, bude platit pro všechna centra v daném oboru názvů.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo vyberte **všechny služby** .
3. V části **Internet věcí** vyberte **obory názvů centra oznámení** . Pokud vyberete hvězdičku ( `*` ) vedle textu, přidá se do levého navigačního panelu v části **Oblíbené**. To vám pomůže rychleji přistupovat na stránku oborů názvů. Po přidání do **oblíbených položek** vyberte **obory názvů centra oznámení**.

    ![Všechny služby – > obory názvů centra oznámení](./media/change-pricing-tier/all-services-nhub.png)

4. Na stránce **obory názvů centra oznámení** vyberte obor názvů, pro který chcete změnit cenovou úroveň.
5. Na stránce **oboru názvů centra oznámení** pro váš obor názvů uvidíte aktuální cenovou úroveň pro obor názvů v části **základy** . Na následujícím obrázku můžete vidět, že cenová úroveň oboru názvů je **zdarma**.

    ![Aktuální cenová úroveň na stránce oboru názvů](./media/change-pricing-tier/pricing-tier-before.png)

6. Na stránce **oboru názvů centra oznámení** pro svůj obor názvů vyberte **cenová úroveň** v části **Správa** .

    ![Výběr cenové úrovně na stránce oboru názvů](./media/change-pricing-tier/namespace-select-pricing-menu.png)

7. Změňte cenovou úroveň a pak klikněte na tlačítko **Vybrat** .
8. V **upozorněních** můžete zobrazit stav akce změny vrstvy.
9. Přepněte na stránku **Přehled** . Potvrďte, že se nová úroveň zobrazuje v poli **cenová úroveň** v části **základy** .
10. Tento krok je volitelný. Vyberte libovolné centrum v oboru názvů. Ověřte, že se v části **základy** zobrazuje stejná cenová úroveň. Měli byste vidět stejnou cenovou úroveň pro všechna centra v oboru názvů.

### <a name="change-tier-on-the-hub-page"></a>Změnit úroveň na stránce centra

Pomocí těchto kroků můžete změnit cenovou úroveň oboru názvů na stránce centra. I když provedete tyto kroky počínaje na stránce centra, změníte cenovou úroveň pro obor názvů a všechna centra v oboru názvů:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo vyberte **všechny služby** .
3. V části **Internet věcí** vyberte **Notification Hubs** .
4. Vyberte **centrum** oznámení.
5. V nabídce vlevo vyberte **cenovou úroveň** .
6. Změňte cenovou úroveň a klikněte na tlačítko **Vybrat** . Tato akce změní nastavení cenové úrovně pro obor názvů, který obsahuje centrum. Proto uvidíte novou cenovou úroveň na stránce obor názvů a na všech stránkách centra.

> [!NOTE]
> Všechny změny cenové úrovně jsou okamžitě platné.

## <a name="use-rest-api"></a>Použití rozhraní REST API

K získání aktuální cenové úrovně a její aktualizaci můžete použít následující poskytovatele prostředků REST API.

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

Další informace o těchto úrovních a cenách najdete v tématu [Notification Hubs ceny](https://azure.microsoft.com/pricing/details/notification-hubs/).
