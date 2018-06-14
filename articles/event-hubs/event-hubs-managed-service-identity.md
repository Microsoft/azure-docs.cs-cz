---
title: Identita spravované služby s Azure Event Hubs ve verzi preview | Microsoft Docs
description: Identita spravované služby pomocí služby Azure Event Hubs
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/18/2017
ms.author: sethm
ms.openlocfilehash: dd50e4f6ebc5fdf5496a5127fde20bd052087b59
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2017
ms.locfileid: "26783409"
---
# <a name="managed-service-identity-preview"></a>Identita spravované služby (preview)

A spravovaná služba Identity (MSI) je funkce mezi Azure, která umožňuje vytvořit bezpečné identitu přidruženou k nasazení, pod kterým běží kódu aplikace. Potom můžete tuto identitu přidružit role řízení přístupu, které udělují vlastní oprávnění pro přístup ke konkrétním prostředkům Azure, které aplikace potřebuje. 

Pomocí Instalační služby MSI spravuje platformy Azure tuto identitu modulu runtime. Není potřeba ukládat a chránit přístupové klíče v kódu aplikace nebo konfigurace, pro identitu, sám sebe nebo pro prostředky, které potřebujete získat přístup. Není nutné aplikaci klienta služby Event Hubs v aplikaci Azure App Service nebo ve virtuálním počítači s povolená podpora MSI zpracování pravidel SAS a klíče nebo jiných přístupových tokenů. Klientská aplikace potřebuje pouze adresa koncového bodu služby Event Hubs oboru názvů. Když se aplikace připojí, Event Hubs váže kontext MSI do klienta v operaci, která se zobrazí v příklad později v tomto článku.

Jakmile je přidružen identita spravované služby, můžete provádět klientem služby Event Hubs všem oprávněným operace. Autorizace udělují přidružení souboru MSI s rolí služby Event Hubs. 

## <a name="event-hubs-roles-and-permissions"></a>Event Hubs role a oprávnění

U verze public preview počáteční je identita spravované služby přidat pouze rolím "Vlastník" nebo "Přispěvatel" oboru názvů služby Event Hubs, která udělují oprávnění Úplné řízení identity na všechny entity v oboru názvů. Však management operace, které změní topologie obor názvů se původně podporovány pouze, když správce prostředků Azure a ne prostřednictvím rozhraní pro správu nativní REST centra událostí. Tato podpora také znamená, že nelze použít klienta rozhraní .NET Framework [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) objekt v rámci identity spravované služby. 
 
## <a name="use-event-hubs-with-a-managed-service-identity"></a>Použití centra událostí s identitou, spravované služby

Následující část popisuje kroky potřebné k vytvoření a nasazení ukázkové aplikace, která běží v části Identita spravované služby, udělení tohoto přístupu identity na obor názvů služby Event Hubs a jak aplikace komunikuje se službou event hubs, pomocí které Identita.

Tento úvod popisuje webové aplikace hostované na [Azure App Service](https://azure.microsoft.com/services/app-service/). Kroky potřebné pro aplikaci hostovaných virtuálních počítačů jsou podobné.

### <a name="create-an-app-service-web-application"></a>Vytvořit webovou aplikaci služby App Service

Prvním krokem je vytvoření aplikace ASP.NET aplikace služby. Pokud si nejste obeznámeni s jak to provést v Azure, postupujte podle [tento postup průvodce](../app-service/app-service-web-get-started-dotnet-framework.md). Místo vytváření aplikace MVC, jak je znázorněno v tomto kurzu, vytvořte aplikace webových formulářů.

### <a name="set-up-the-managed-service-identity"></a>Nastavení identity spravované služby

Jakmile vytvoříte aplikaci, přejděte na nově vytvořenou webovou aplikaci na portálu Azure (také znázorněné postupy) a potom přejděte na **identita spravované služby** stránky a povolení této funkce: 

![](./media/event-hubs-managed-service-identity/msi1.png)
 
Po povolení funkce, je novou identitu služby vytvořené v Azure Active Directory a nakonfigurovat do hostitele služby App Service.

### <a name="create-a-new-event-hubs-namespace"></a>Vytvořit nový obor názvů služby Event Hubs

Dále [vytvoření oboru názvů Event Hubs](event-hubs-create.md) v jedné oblasti Azure, které mají preview podporu pro MSI: **USA – východ**, **USA – východ 2**, nebo **západní Evropa**. 

Přejděte do oboru názvů **řízení přístupu (IAM)** na portálu a pak klikněte na tlačítko **přidat** přidat identita spravované služby k **vlastníka** role. Chcete-li to provést, vyhledejte název webové aplikace v **přidat oprávnění** panelu **vyberte** pole a pak klikněte na položku. Potom klikněte na **Uložit**.

![](./media/event-hubs-managed-service-identity/msi2.png)
 
Identita spravované služby webové aplikace teď má přístup k oboru názvů služby Event Hubs a do centra událostí jste předtím vytvořili. 

### <a name="run-the-app"></a>Spuštění aplikace

Nyní upravte výchozí stránku aplikace ASP.NET, kterou jste vytvořili. Můžete taky kódu webové aplikace z [toto úložiště GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/MSI/EventHubsMSIDemoWebApp). 

Po spuštění aplikace, přejděte v prohlížeči do EventHubsMSIDemo.aspx. Alternativně nastavte ji jako úvodní stránky. Kód naleznete v souboru EventHubsMSIDemo.aspx.cs. Výsledkem je minimální webové aplikace s několika vstupní pole a se **odeslat** a **přijímat** tlačítka, který je připojen k Event Hubs odesílat nebo přijímat zprávy. 

Poznámka: Jak [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) objekt je inicializován. Místo použití zprostředkovatele tokenu sdíleného přístupového tokenu (SAS), kód vytvoří zprostředkovatele tokenu pro identitu spravované služby pomocí `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.EventHubAudience)` volání. Jako takový neexistují žádné tajné klíče uchovat a použít. Tok kontext identity spravované služby Event Hubs a autorizace metody handshake se automaticky požádat o token poskytovatele, který je model jednodušší než pomocí SAS.

Po provedení těchto změn, publikování a spuštění aplikace. Snadný způsob, jak získat správné publikování dat je ke stažení a pak import profilu publikování v sadě Visual Studio:

![](./media/event-hubs-managed-service-identity/msi3.png)
 
Pokud chcete odesílat nebo přijímat zprávy, zadejte název oboru názvů a název entity, která jste vytvořili a pak klikněte buď na **odeslat** nebo **přijímat**. 
 
Všimněte si, že identita spravované služby je funkční pouze v prostředí Azure a pouze v nasazení služby App Service, do které jste nakonfigurovali. Všimněte si také, že identita spravované služby nebudou fungovat s slotů nasazení služby App Service v tuto chvíli.

## <a name="next-steps"></a>Další kroky

Další informace o službě Event Hubs naleznete pod těmito odkazy:

* Úvodní [Kurz služby Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
* [Podrobnosti o cenách služby Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Ukázkové aplikace, které používají službu Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)