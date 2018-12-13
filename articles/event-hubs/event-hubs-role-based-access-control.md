---
title: Náhled řízení přístupu na základě rolí – Azure Event Hubs | Dokumentace Microsoftu
description: Tento článek obsahuje informace o řízení přístupu na základě rolí pro službu Azure Event Hubs.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: c7d38538b3876ae91c0ae3794e14ab11f08993c6
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53162881"
---
# <a name="active-directory-role-based-access-control-preview"></a>Aktivní Directory Role-Based řízení přístupu (preview)

Microsoft Azure poskytuje správu řízení integrované přístupu k prostředkům a aplikace založené na Azure Active Directory (Azure AD). S Azure AD můžete buď spravovat uživatelské účty a aplikace konkrétně pro aplikace založené na Azure, nebo může provést federaci stávající infrastrukturu služby Active Directory s Azure AD pro pořádaného microsoftem single-sign-on, která také zahrnuje prostředky Azure a aplikace hostované v Azure. Aby bylo možné udělit přístup k prostředkům Azure pak můžete přiřadit tyto identity uživatelů a aplikací Azure AD pro globální a specifické pro služby role.

Pro Azure Event Hubs, Správa oborů názvů a všech souvisejících prostředků na webu Azure portal a rozhraní API pro správu prostředků Azure už je chráněný pomocí *řízení přístupu na základě rolí* modelu (RBAC). RBAC pro modul runtime operace funkce je teď ve verzi public preview. 

Aplikace, která používá Azure AD RBAC není potřeba zpracovat pravidla SAS a klíče nebo jiné přístupové tokeny specifické pro službu Event Hubs. Klientská aplikace komunikuje se službou Azure AD k navázání kontext ověřování a získá přístupový token pro službu Event Hubs. S uživatelskými účty domény, které vyžadují interaktivního přihlášení aplikace nikdy zpracovává všechny přihlašovací údaje přímo.

## <a name="event-hubs-roles-and-permissions"></a>Event Hubs role a oprávnění

Počáteční verze public Preview můžete pouze přidat účty služby Azure AD a instanční objekty "Vlastník" nebo "Přispěvatel" rolí obor názvů služby Event Hubs. Tato operace udělují oprávnění Úplné řízení všech entit v oboru názvů identity. Operace správy, které se mění topologii obor názvů jsou zpočátku pouze podporované i když Azure resource management a ne přes rozhraní nativní Správa Event Hubs REST. Tato podpora také znamená, že rozhraní .NET Framework client [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) objekt nelze použít s účtem služby Azure AD.  

## <a name="use-event-hubs-with-an-azure-ad-domain-user-account"></a>Uživatelský účet domény Azure AD pomocí služby Event Hubs

Následující část popisuje kroky potřebné k vytvoření a spuštění ukázkové aplikace, který zobrazí výzvu k zadání interaktivní Azure AD přihlášení uživatele, jak udělit přístup k Event Hubs s daným uživatelským účtem a tom, jak použít tuto identitu pro přístup k Event Hubs. 

Tento úvod popisuje jednoduchou konzolovou aplikaci, [kód, pro který je na Githubu](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Rbac/EventHubsSenderReceiverRbac/)

### <a name="create-an-active-directory-user-account"></a>Vytvoření účtu uživatele služby Active Directory

Tento první krok je volitelný. Každé předplatné služby Azure se automaticky páruje s oblastí tenanta služby Azure Active Directory a pokud máte přístup k předplatnému Azure, váš uživatelský účet je už zaregistrovaný. To znamená, že používáte na váš účet. 

Pokud chcete vytvořit zvláštní účet pro tento scénář [postupujte podle těchto kroků](../automation/automation-create-aduser-account.md). Musíte mít oprávnění k vytváření účtů v tenantovi Azure Active Directory, která nemusí být v případě větší podnikové scénáře.

### <a name="create-an-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs

Dále [vytvořte obor názvů služby Event Hubs](event-hubs-create.md) v jedné oblasti Azure, které podporují službu Event Hubs ve verzi preview pro RBAC: **USA – východ**, **USA – východ 2**, nebo **západní Evropa**. 

Jakmile se vytvoří obor názvů, přejděte do jeho **řízení přístupu (IAM)** stránky na portálu a potom klikněte na **přidat přiřazení role** přidání do role vlastníka účtu uživatele Azure AD. Pokud používáte vlastní uživatelský účet a vytvořili obor názvů, jste již v roli vlastník. Pokud chcete přidat jiný účet k roli, vyhledejte název webové aplikace v **přidat oprávnění** panel **vyberte** pole a potom klikněte na položku. Potom klikněte na **Uložit**. Uživatelský účet má teď přístup k oboru názvů služby Event Hubs a do centra událostí, kterou jste vytvořili dřív.
 
### <a name="register-the-application"></a>Zaregistrovat aplikaci

Předtím, než spustíte ukázkovou aplikaci zaregistrovat ve službě Azure AD a schválit výzva k povolení spuštění, který umožňuje aplikaci přístup k Event Hubs svým jménem. 

Protože ukázková aplikace je konzolová aplikace, musí registrovat nativní aplikaci a přidejte oprávnění k rozhraní API pro **Microsoft.EventHub** do sady "požadováno oprávnění". Nativní aplikace také potřebují **redirect-URI** ve službě Azure AD, který slouží jako identifikátor URI není potřeba se cílové sítě. Použití `http://eventhubs.microsoft.com` v tomto příkladu vzhledem k tomu, že ukázkový kód již používá tento identifikátor URI.

Podrobné registrační kroky jsou vysvětlené v [v tomto kurzu](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Postupujte podle pokynů pro registraci **nativní** aplikace a pak postupujte podle pokynů aktualizace a přidejte **Microsoft.EventHub** rozhraní API požadovaná oprávnění. Jak budete postupovat podle kroků, poznamenejte si **TenantId** a **ApplicationId**, protože ji budete potřebovat tyto hodnoty ke spuštění aplikace.

### <a name="run-the-app"></a>Spuštění aplikace

Před spuštěním ukázky, upravte soubor App.config a v závislosti na scénáři, nastavte následující hodnoty:

- `tenantId`: Nastavte na **TenantId** hodnotu.
- `clientId`: Nastavte na **ApplicationId** hodnotu. 
- `clientSecret`: Pokud chcete k přihlášení pomocí tajného klíče klienta, vytvořte ho ve službě Azure AD. Také můžete použijte webovou aplikaci nebo API namísto nativní aplikaci. Přidejte také aplikaci v rámci **řízení přístupu (IAM)** v oboru názvů, kterou jste vytvořili dřív.
- `eventHubNamespaceFQDN`: Nastavte na plně kvalifikovaný název DNS vašeho nově vytvořeného oboru názvů služby Event Hubs; například `example.servicebus.windows.net`.
- `eventHubName`: Nastavte na název centra událostí, které jste vytvořili.
- Identifikátor URI pro přesměrování, který jste zadali ve vaší aplikaci v předchozích krocích.
 
Když spustíte aplikaci konzoly, zobrazí se výzva k výběru scénáře; Klikněte na tlačítko **interaktivní přihlášení uživatele** zadáním příslušného čísla a stisknutím klávesy ENTER. Aplikace zobrazí okno přihlášení, vyzve k zadání vašeho svolení pro přístup k Event Hubs a potom použije službu pro spuštění prostřednictvím scénář posílání a přijímání pomocí identity přihlášení.

## <a name="next-steps"></a>Další postup

Další informace o službě Event Hubs naleznete pod těmito odkazy:

* Úvodní [Kurz služby Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
* [Podrobnosti o cenách služby Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Ukázkové aplikace, které používají službu Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)
