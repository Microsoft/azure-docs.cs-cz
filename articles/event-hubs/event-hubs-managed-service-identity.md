---
title: Identita spravované služby s verzí preview služby Azure Event Hubs | Dokumentace Microsoftu
description: Identity spravovaných služeb pomocí Azure Event Hubs
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: sethm
ms.openlocfilehash: 7c8f7fff5e3cf7334ce30a3fa90ae950f841662c
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37865293"
---
# <a name="managed-service-identity-preview"></a>Identita spravované služby (Preview)

Identity spravované služby (MSI) je funkce napříč Azure, která umožňuje vytvořit zabezpečené identitu přidruženou k nasazení, pod kterým běží kód vaší aplikace. Potom můžete tuto identitu přidružit role řízení přístupu, které vlastní oprávnění pro přístup ke konkrétním prostředkům Azure, které vaše aplikace potřebuje. 

Na platformě Azure pomocí MSI, spravuje tuto identitu modulu runtime. Není potřeba ukládat a chránit přístupových klíčů v kódu aplikace nebo konfigurace pro identitu, samotné nebo pro prostředky, které potřebujete získat přístup. Event Hubs klientské aplikaci spuštěnou v aplikaci Azure App Service nebo na virtuálním počítači povolená podpora MSI nemusí zpracovávat pravidla SAS a klíče nebo jiné přístupové tokeny. Klientská aplikace potřebuje pouze adresu koncového bodu z oboru názvů Event Hubs. Když se aplikace připojí, Event Hubs váže kontextu MSI do klienta v operaci, která se zobrazí v příklad dále v tomto článku.

Až bude související s využitím identity spravované služby, klient služby Event Hubs můžete provádět všem oprávněným operace. Povolení uděleno tím, že přidružíte MSI s rolí služby Event Hubs. 

## <a name="event-hubs-roles-and-permissions"></a>Event Hubs role a oprávnění

Počáteční verze public preview verzi pouze přidáte identitu spravované služby "Vlastník" nebo "Přispěvatel" rolí obor názvů služby Event Hubs, která udělují oprávnění Úplné řízení identit na všechny entity v oboru názvů. Však nepodporuje správu operací, které se mění topologii obor názvů jsou zpočátku pouze když Azure Resource Manageru a ne přes rozhraní nativní Správa Event Hubs REST. Tato podpora také znamená, že nelze použít klienta rozhraní .NET Framework [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) objekt v rámci identity spravované služby. 
 
## <a name="use-event-hubs-with-a-managed-service-identity"></a>Použití služby Event Hubs s využitím Identity spravované služby

Následující část popisuje kroky potřebné k vytvoření a nasazení ukázkové aplikace, na kterém běží v části Identita spravované služby, jak udělit přístup této identity na obor názvů služby Event Hubs a jak aplikace komunikuje s event hubs, která pomocí Identita.

Tento úvod popisuje webové aplikaci hostované v [služby Azure App Service](https://azure.microsoft.com/services/app-service/). Kroky potřebné pro aplikace hostované na virtuálním počítači jsou podobné.

### <a name="create-an-app-service-web-application"></a>Vytvořit webovou aplikaci služby App Service

Prvním krokem je vytvoření aplikace App Service ASP.NET. Pokud nejste obeznámeni s jak to udělat v Azure, postupujte podle [Tato příručka](../app-service/app-service-web-get-started-dotnet-framework.md). Místo vytváření aplikace MVC, jak je znázorněno v tomto kurzu, vytvořte aplikaci webových formulářů.

### <a name="set-up-the-managed-service-identity"></a>Nastavení identity spravované služby

Jakmile vytvoříte aplikaci, přejděte na nově vytvořenou webovou aplikaci na webu Azure Portal (také je znázorněno postupy) a pak přejděte na **identita spravované služby** stránce a povolení této funkce: 

![](./media/event-hubs-managed-service-identity/msi1.png)
 
Po povolení funkce, je novou identitu služby vytvořené ve službě Azure Active Directory a nakonfigurováno v hostiteli služby App Service.

### <a name="create-a-new-event-hubs-namespace"></a>Vytvoření nového oboru názvů služby Event Hubs

Dále [vytvořte obor názvů služby Event Hubs](event-hubs-create.md) v jedné oblasti Azure, které nabízí podporu verze preview pro MSI: **oblasti USA – východ**, **USA – východ 2**, nebo **západní Evropa**. 

Přejděte do oboru názvů **řízení přístupu (IAM)** stránky na portálu a potom klikněte na tlačítko **přidat** přidáte identitu spravované služby pro **vlastníka** role. Uděláte to tak, vyhledejte název webové aplikace v **přidat oprávnění** panel **vyberte** pole a potom klikněte na příslušnou položku. Potom klikněte na **Uložit**.

![](./media/event-hubs-managed-service-identity/msi2.png)
 
Identita spravované služby pro webové aplikace teď má přístup k oboru názvů Event Hubs a do centra událostí, kterou jste vytvořili dřív. 

### <a name="run-the-app"></a>Spuštění aplikace

Nyní můžete upravte výchozí stránku, kterou jste vytvořili aplikaci ASP.NET. Můžete také použít kódu webové aplikace z [úložiště GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/MSI/EventHubsMSIDemoWebApp). 

Po spuštění aplikace, přejděte v prohlížeči EventHubsMSIDemo.aspx. Alternativně nastavte ji jako úvodní stránku. Kód můžete najít v souboru EventHubsMSIDemo.aspx.cs. Výsledkem je minimální webové aplikace s několika polí pro zadávání a **odeslat** a **přijímat** tlačítka, která připojení do služby Event Hubs k odesílání nebo příjem událostí. 

Poznámka: Jak [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) objekt je inicializován. Namísto použití zprostředkovatele tokenu sdíleného přístupového tokenu (SAS), kód vytvoří poskytovatel tokenů pro identity spravované služby u `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.EventHubAudience)` volání. V důsledku toho nejsou žádné tajných kódů a zachovat. Tok kontext identity spravované služby Event Hubs a povolení handshake jsou automaticky zpracovány od poskytovatele tokenu, tedy modelem jednodušší než při použití SAS.

Po provedení těchto změn, publikování a spuštění aplikace. Ke stažení a pak importovat profil publikování v sadě Visual Studio je snadný způsob, jak získat správný publikování dat:

![](./media/event-hubs-managed-service-identity/msi3.png)
 
Odesílat nebo přijímat zprávy, zadejte název oboru názvů a názvem entity, které jste vytvořili a pak klikněte na možnost **odeslat** nebo **přijímat**. 
 
Všimněte si, že služby managed service identity funguje jenom v prostředí Azure a pouze v nasazení služby App Service, ve kterém jste je nakonfigurovali. Všimněte si také, že identity spravovaných služeb nefungují s sloty nasazení služby App Service v tuto chvíli.

## <a name="next-steps"></a>Další postup

Další informace o službě Event Hubs naleznete pod těmito odkazy:

* Úvodní [Kurz služby Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
* [Podrobnosti o cenách služby Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Ukázkové aplikace, které používají službu Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)