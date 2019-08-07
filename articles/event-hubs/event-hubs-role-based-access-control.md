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
ms.date: 05/21/2019
ms.author: shvija
ms.openlocfilehash: 117b7d4adb508628ee768bb9531d0bbc52f61121
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816057"
---
# <a name="active-directory-role-based-access-control-preview"></a>Aktivní Directory Role-Based řízení přístupu (preview)

Microsoft Azure poskytuje správu řízení integrované přístupu k prostředkům a aplikace založené na Azure Active Directory (Azure AD). S Azure AD můžete buď spravovat uživatelské účty a aplikace konkrétně pro aplikace založené na Azure, nebo může provést federaci stávající infrastrukturu služby Active Directory s Azure AD pro pořádaného microsoftem single-sign-on, která také zahrnuje prostředky Azure a aplikace hostované v Azure. Aby bylo možné udělit přístup k prostředkům Azure pak můžete přiřadit tyto identity uživatelů a aplikací Azure AD pro globální a specifické pro služby role.

Pro Azure Event Hubs, Správa oborů názvů a všech souvisejících prostředků na webu Azure portal a rozhraní API pro správu prostředků Azure už je chráněný pomocí *řízení přístupu na základě rolí* modelu (RBAC). RBAC pro modul runtime operace funkce je teď ve verzi public preview. 

Aplikace, která používá Azure AD RBAC není potřeba zpracovat pravidla SAS a klíče nebo jiné přístupové tokeny specifické pro službu Event Hubs. Klientská aplikace komunikuje se službou Azure AD k navázání kontext ověřování a získá přístupový token pro službu Event Hubs. S uživatelskými účty domény, které vyžadují interaktivního přihlášení aplikace nikdy zpracovává všechny přihlašovací údaje přímo.

## <a name="event-hubs-roles-and-permissions"></a>Event Hubs role a oprávnění
Azure poskytuje následující předdefinované role RBAC pro autorizaci přístupu k oboru názvů Event Hubs:

Role [vlastníka dat Event Hubs Azure (Preview)](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner-preview) umožňuje přístup k datům z oboru názvů Event Hubs a jeho entit (fronty, témata, odběry a filtry).

>[!IMPORTANT]
> Dříve jsme podporovali přidání spravované identity do role **vlastníka** nebo **přispěvatele** . Oprávnění pro přístup k datům pro **vlastníka** a roli přispěvatele se ale už neuplatňují. Pokud používáte roli **vlastníka** nebo **Přispěvatel** , přepněte se na použití role **vlastníka dat Event Hubs Azure (Preview)** .


## <a name="use-event-hubs-with-an-azure-ad-domain-user-account"></a>Uživatelský účet domény Azure AD pomocí služby Event Hubs

Následující část popisuje kroky potřebné k vytvoření a spuštění ukázkové aplikace, který zobrazí výzvu k zadání interaktivní Azure AD přihlášení uživatele, jak udělit přístup k Event Hubs s daným uživatelským účtem a tom, jak použít tuto identitu pro přístup k Event Hubs. 

Tento Úvod popisuje jednoduchou konzolovou aplikaci, [jejíž kód je na GitHubu](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK) .

### <a name="create-an-active-directory-user-account"></a>Vytvoření účtu uživatele služby Active Directory

Tento první krok je volitelný. Každé předplatné služby Azure se automaticky páruje s oblastí tenanta služby Azure Active Directory a pokud máte přístup k předplatnému Azure, váš uživatelský účet je už zaregistrovaný. To znamená, že používáte na váš účet. 

Pokud chcete vytvořit zvláštní účet pro tento scénář [postupujte podle těchto kroků](../automation/automation-create-aduser-account.md). Musíte mít oprávnění k vytváření účtů v tenantovi Azure Active Directory, která nemusí být v případě větší podnikové scénáře.

### <a name="create-an-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs

Dále [vytvořte obor názvů Event Hubs](event-hubs-create.md). 

Jakmile se vytvoří obor názvů, přejděte do jeho **řízení přístupu (IAM)** stránky na portálu a potom klikněte na **přidat přiřazení role** přidání do role vlastníka účtu uživatele Azure AD. Pokud používáte vlastní uživatelský účet a vytvořili obor názvů, jste již v roli vlastník. Pokud chcete přidat jiný účet k roli, vyhledejte název webové aplikace v **přidat oprávnění** panel **vyberte** pole a potom klikněte na položku. Potom klikněte na **Uložit**. Uživatelský účet má teď přístup k oboru názvů služby Event Hubs a do centra událostí, kterou jste vytvořili dřív.
 
### <a name="register-the-application"></a>Zaregistrovat aplikaci

Předtím, než spustíte ukázkovou aplikaci zaregistrovat ve službě Azure AD a schválit výzva k povolení spuštění, který umožňuje aplikaci přístup k Event Hubs svým jménem. 

Protože ukázková aplikace je konzolová aplikace, musí registrovat nativní aplikaci a přidejte oprávnění k rozhraní API pro **Microsoft.EventHub** do sady "požadováno oprávnění". Nativní aplikace také potřebují **redirect-URI** ve službě Azure AD, který slouží jako identifikátor URI není potřeba se cílové sítě. Použití `https://eventhubs.microsoft.com` v tomto příkladu vzhledem k tomu, že ukázkový kód již používá tento identifikátor URI.

Podrobné registrační kroky jsou vysvětlené v [v tomto kurzu](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Postupujte podle pokynů pro registraci **nativní** aplikace a pak postupujte podle pokynů aktualizace a přidejte **Microsoft.EventHub** rozhraní API požadovaná oprávnění. Jak budete postupovat podle kroků, poznamenejte si **TenantId** a **ApplicationId**, protože ji budete potřebovat tyto hodnoty ke spuštění aplikace.

### <a name="run-the-app"></a>Spuštění aplikace

Před spuštěním ukázky, upravte soubor App.config a v závislosti na scénáři, nastavte následující hodnoty:

- `tenantId`: Nastavte na hodnotu **TenantId** .
- `clientId`: Nastavte na hodnotu **ApplicationId** . 
- `clientSecret`: Pokud se chcete přihlásit pomocí tajného kódu klienta, vytvořte ho v Azure AD. Také můžete použijte webovou aplikaci nebo API namísto nativní aplikaci. Přidejte také aplikaci v rámci **řízení přístupu (IAM)** v oboru názvů, kterou jste vytvořili dřív.
- `eventHubNamespaceFQDN`: Nastavte na plně kvalifikovaný název DNS nově vytvořeného oboru názvů Event Hubs; například `example.servicebus.windows.net`.
- `eventHubName`: Nastavte na název centra událostí, které jste vytvořili.
- Identifikátor URI pro přesměrování, který jste zadali ve vaší aplikaci v předchozích krocích.
 
Když spustíte aplikaci konzoly, zobrazí se výzva k výběru scénáře; Klikněte na tlačítko **interaktivní přihlášení uživatele** zadáním příslušného čísla a stisknutím klávesy ENTER. Aplikace zobrazí okno přihlášení, vyzve k zadání vašeho svolení pro přístup k Event Hubs a potom použije službu pro spuštění prostřednictvím scénář posílání a přijímání pomocí identity přihlášení.

Aplikace používá `ServiceAudience.EventHubsAudience` jako cílovou skupinu tokenů. Při použití jiných jazyků nebo sad SDK, kde cílová skupina není k dispozici jako konstanta, je `https://eventhubs.azure.net/`nejvhodnější hodnota pro použití.

## <a name="next-steps"></a>Další kroky

Další informace o službě Event Hubs naleznete pod těmito odkazy:

* Úvodní [Kurz služby Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
* [Podrobnosti o cenách služby Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Ukázkové aplikace, které používají službu Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)
