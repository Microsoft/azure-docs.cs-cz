---
title: Změna cenové úrovně oboru názvů Notification Hubs | Dokumentace Microsoftu
description: Zjistěte, jak změnit cenovou úroveň obor názvů služby Azure Notification Hubs.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 01/28/2019
ms.author: jowargo
ms.openlocfilehash: c572f64bdcb8846b5f73a0fee34f11c9729ee45d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55885051"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>Změnit cenovou úroveň obor názvů služby Azure notification hubs
Notification Hubs se nabízí ve třech úrovních: **bezplatné**, **základní**, a **standardní**. Tento článek ukazuje, jak změnit cenovou úroveň pro obor názvů služby Azure Notification Hubs. 

## <a name="overview"></a>Přehled
Ve službě Azure Notification Hubs **centra** je nejmenší prostředků na entitu. To obecně mapuje na jednu aplikaci a může obsahovat jeden certifikát u každého systému oznámení platformy jsme podporu pro aplikaci. Aplikace může být hybridní nebo nativní a multiplatformní aplikace.

A **obor názvů** je kolekce služby notification hubs. Každý obor názvů se obvykle skládá z rozbočovačů, které mají související a používat pro konkrétní účel. Například můžete mít tři různé obory názvů pro vývoj, testování a produkční účely v uvedeném pořadí. 

Můžete přiřadit cenovou úroveň na úrovni oboru názvů. Notification Hubs podporuje tři úrovně: **bezplatné**, **základní**, a **standardní**. Na úrovni můžete použít pro obor názvů, který vyhovuje vašim požadavkům. Následující části ukazují, jak změnit cenovou úroveň oboru názvů Notification Hubs. 

## <a name="use-azure-portal"></a>Použití webu Azure Portal 
Při použití webu Azure portal, můžete změnit cenovou úroveň pro obor názvů na stránce obor názvů nebo stránka centra.  Když se změní na stránce centra, ve skutečnosti jej změníte na úrovni oboru názvů. Změní cenovou úroveň pro obor názvů a všechna centra v oboru názvů. 

### <a name="change-tier-on-the-namespace-page"></a>Změna úrovně na stránce obor názvů
Následující postup obsahuje kroky, chcete-li změnit cenovou úroveň pro obor názvů na stránce obor názvů. Pokud změníte úroveň pro obor názvů, platí pro všechna centra v oboru názvů.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** v nabídce vlevo. 
3. Vyberte **názvové prostory centra oznámení** v **Internet of Things** oddílu. Pokud vyberte hvězdičku (`*`) vedle textu, získá přidá do levého navigačního panelu v části **Oblíbené**. Pomůže vám s přístupem ke stránce obory názvů rychlejší při příštím a vyšší. Po přidání do oblíbených položek, vyberte **názvové prostory centra oznámení**. 

    ![Všechny služby -> názvové prostory centra oznámení](./media/change-pricing-tier/all-services-nhub.png)
1. Na **názvové prostory centra oznámení** stránky, vyberte obor názvů, pro kterou chcete změnit cenovou úroveň. 
2. Na **Namespace centra oznámení** stránky pro váš obor názvů, zobrazí se aktuální cenová úroveň pro obor názvů v **Essentials** oddílu. Na následujícím obrázku vidíte, že cenové úrovně oboru názvů je **Free**. 

    ![Aktuální cenová úroveň na stránce obor názvů](./media/change-pricing-tier/pricing-tier-before.png)
1. Na **Namespace centra oznámení** stránky pro váš obor názvů, vyberte **cenová úroveň** pod **spravovat** oddílu. 

    ![Vyberte cenovou úroveň na stránce obor názvů](./media/change-pricing-tier/namespace-select-pricing-menu.png)
6. Změnit svou cenovou úroveň a klikněte na tlačítko **vyberte** tlačítko.    
7. Zobrazí stav úrovně, změnit v akci **výstrahy**. 
8. Přepněte **přehled** stránky. Potvrďte, že se zobrazí nová úroveň pro **cenová úroveň** pole **Essentials** oddílu.     
1. Tento krok je volitelný. Vyberte libovolné centrum v oboru názvů. Zkontrolujte, jestli se stejnou cenovou úroveň v **Essentials** oddílu. Měli byste vidět stejnou cenovou úroveň pro všechna centra v oboru názvů. 

### <a name="change-tier-on-the-hub-page"></a>Změna úrovně na stránce centra
Následující postup obsahuje kroky, chcete-li změnit cenovou úroveň pro obor názvů na stránce centra. I když neprovedete tyto kroky od stránka centra, ve skutečnosti změnit cenovou úroveň pro obor názvů a všechna centra v oboru názvů. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** v nabídce vlevo.
3. Vyberte **Notification Hubs** v **Internet of Things** oddílu. 
4. Vyberte oznámení **centra**. 
5. Vyberte **cenová úroveň** v nabídce vlevo. 
6. Změnit cenovou úroveň a klikněte na tlačítko **vyberte** tlačítko. Tato akce změní nastavení cenové úrovně pro obor názvů obsahující rozbočovače. Proto se zobrazí novou cenovou úroveň na stránce obor názvů a všech stránkách centra. 

## <a name="use-rest-api"></a>Použití rozhraní REST API
Následující REST API poskytovatele prostředků můžete použít k získání aktuální cenová úroveň a aktualizujte ji. 

### <a name="get-current-pricing-tier-for-a-namespace"></a>Získat aktuální cenová úroveň pro obor názvů
Chcete-li získat **aktuální úroveň oboru názvů**, odeslat příkaz GET, jak je znázorněno v následujícím příkladu: 

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>Aktualizace cenové úrovně pro obor názvů
K **aktualizovat na úrovni oboru názvů**, poslat příkaz, PUT, jak je znázorněno v následujícím příkladu: 

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```



## <a name="next-steps"></a>Další postup
Další informace o těchto úrovních a cenách najdete v tématu [Notification Hubs ceny](https://azure.microsoft.com/pricing/details/notification-hubs/).
