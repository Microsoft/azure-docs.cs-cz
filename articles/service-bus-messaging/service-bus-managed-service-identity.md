---
title: "Identita spravované služby s verzí preview služby Azure Service Bus | Microsoft Docs"
description: "Identita spravované služby pomocí služby Azure Service Bus"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 7b9901ee3478cb193c808b65d2dbbcf8b596a3c1
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="managed-service-identity-preview"></a>Identita spravované služby (preview)

A spravovaná služba Identity (MSI) je funkce mezi Azure, která umožňuje vytvořit bezpečné identitu přidruženou k nasazení, pod kterým běží kódu aplikace. Potom můžete tuto identitu přidružit role řízení přístupu, které udělují vlastní oprávnění pro přístup ke konkrétním prostředkům Azure, které aplikace potřebuje.

Pomocí Instalační služby MSI spravuje platformy Azure tuto identitu modulu runtime. Není potřeba ukládat a chránit přístupové klíče v kódu aplikace nebo konfigurace, pro identitu, sám sebe nebo pro prostředky, které potřebujete získat přístup. Pro zpracování pravidel SAS a klíče nebo jiných přístupových tokenů nemusí klientskou aplikaci služby Service Bus s povolená podpora MSI v aplikaci Azure App Service nebo ve virtuálním počítači. Klientská aplikace potřebuje pouze adresa koncového bodu oboru názvů zasílání zpráv Service Bus. Když se aplikace připojí, vytvoří vazbu Service Bus kontext MSI klienta v operaci, která se zobrazí v příklad později v tomto článku. 

Jakmile je přidružen identita spravované služby, Service Bus klient všem oprávněným operace lze provádět. Autorizace udělují přidružení souboru MSI s rolí služby Service Bus. 

## <a name="service-bus-roles-and-permissions"></a>Service Bus rolí a oprávnění

U verze public preview počáteční je identita spravované služby přidat pouze rolím "Vlastník" nebo "Přispěvatel" oboru názvů Service Bus, která udělují oprávnění Úplné řízení identity na všechny entity v oboru názvů. Však management operace, které změní topologie obor názvů se původně podporovány pouze, když správce prostředků Azure a ne prostřednictvím rozhraní pro správu nativní REST pro Service Bus. Tato podpora také znamená, že nelze použít klienta rozhraní .NET Framework [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) objekt v rámci identity spravované služby.

## <a name="use-service-bus-with-a-managed-service-identity"></a>Použití služby Service Bus s identitou, spravované služby

Následující část popisuje kroky potřebné k vytvoření a nasazení ukázkové aplikace, která běží v části Identita spravované služby, udělení tohoto přístupu identity do oboru názvů, zasílání zpráv Service Bus a jak aplikace komunikuje se službou Service Bus Pomocí této identity entity.

Tento úvod popisuje webové aplikace hostované na [Azure App Service](https://azure.microsoft.com/services/app-service/). Kroky potřebné pro aplikaci hostovaných virtuálních počítačů jsou podobné.

### <a name="create-an-app-service-web-application"></a>Vytvořit webovou aplikaci služby App Service

Prvním krokem je vytvoření aplikace ASP.NET aplikace služby. Pokud si nejste obeznámeni s jak to provést v Azure, postupujte podle [tento postup průvodce](../app-service/app-service-web-get-started-dotnet-framework.md). Místo vytváření aplikace MVC, jak je znázorněno v tomto kurzu, vytvořte aplikace webových formulářů.

### <a name="set-up-the-managed-service-identity"></a>Nastavení identity spravované služby

Jakmile vytvoříte aplikaci, přejděte na nově vytvořenou webovou aplikaci na portálu Azure (také znázorněné postupy) a potom přejděte na **identita spravované služby** stránky a povolení této funkce: 

![](./media/service-bus-managed-service-identity/msi1.png)

Po povolení funkce, je novou identitu služby vytvořené v Azure Active Directory a nakonfigurovat do hostitele služby App Service.

### <a name="create-a-new-service-bus-messaging-namespace"></a>Vytvořit nový obor názvů zasílání zpráv Service Bus

Dále [vytvoření oboru názvů zasílání zpráv Service Bus](service-bus-create-namespace-portal.md) v jedné oblasti Azure, které mají preview podporu pro RBAC: **USA – východ**, **USA – východ 2**, nebo **západní Evropa** . 

Přejděte do oboru názvů **řízení přístupu (IAM)** na portálu a pak klikněte na tlačítko **přidat** přidat identita spravované služby k **vlastníka** role. Chcete-li to provést, vyhledejte název webové aplikace v **přidat oprávnění** panelu **vyberte** pole a pak klikněte na položku. Potom klikněte na **Uložit**.

![](./media/service-bus-managed-service-identity/msi2.png)
 
Identita spravované služby webové aplikace teď má přístup k oboru názvů Service Bus a do fronty jste předtím vytvořili. 

### <a name="run-the-app"></a>Spuštění aplikace

Nyní upravte výchozí stránku aplikace ASP.NET, kterou jste vytvořili. Můžete taky kódu webové aplikace z [toto úložiště GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ManagedServiceIdentity).

Stránka Default.aspx je cílová stránka. Kód naleznete v souboru Default.aspx.cs. Výsledkem je minimální webové aplikace s několika vstupní pole a se **odeslat** a **přijímat** tlačítka, která připojení k Service Bus odesílat nebo přijímat zprávy.

Poznámka: Jak [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) objekt je inicializován. Místo použití zprostředkovatele tokenu sdíleného přístupového tokenu (SAS), kód vytvoří zprostředkovatele tokenu pro identitu spravované služby pomocí `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.ServiceBusAudience)` volání. Jako takový neexistují žádné tajné klíče uchovat a použít. Tok kontext identity spravované služby Service Bus a autorizace metody handshake se automaticky požádat o token poskytovatele, který je model jednodušší než pomocí SAS.

Po provedení těchto změn, publikování a spuštění aplikace. Snadný způsob, jak získat správné publikování dat je ke stažení a pak import profilu publikování v sadě Visual Studio:

![](./media/service-bus-managed-service-identity/msi3.png)
 
Pokud chcete odesílat nebo přijímat zprávy, zadejte název oboru názvů a název entity, která jste vytvořili a pak klikněte buď na **odeslat** nebo **přijímat**.
 
Všimněte si, že identita spravované služby je funkční pouze v prostředí Azure a pouze v nasazení služby App Service, do které jste nakonfigurovali. Všimněte si také, že identita spravované služby nebudou fungovat s slotů nasazení služby App Service v tuto chvíli.

## <a name="next-steps"></a>Další postup

Pokud se o přenosu zpráv přes Service Bus chcete dozvědět víc, pročtěte si následující témata.

* [Základy služby Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)