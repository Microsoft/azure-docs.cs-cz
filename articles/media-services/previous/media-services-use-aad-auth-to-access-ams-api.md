---
title: Přístup k rozhraní AZURE Media Services API s ověřováním Azure Active Directory | Dokumenty společnosti Microsoft
description: Přečtěte si o konceptech a krocích, které je třeba podniknout při ověřování přístupu k rozhraní API Azure Media Services pomocí služby Azure Active Directory (Azure AD).
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 8e1aeaf105ce371e965b433ac78e2b257f4bc18b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682042"
---
# <a name="access-the-azure-media-services-api-with-azure-ad-authentication"></a>Přístup k rozhraní API služby Azure Media Services s využitím ověřování Azure AD  

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Viz také [pokyny k migraci z v2 na v3](../latest/migrate-from-v2-to-v3.md)

Rozhraní API Mediálních služeb Azure je rozhraní API RESTful. Můžete ji použít k provádění operací s prostředky médií pomocí rozhraní REST API nebo pomocí dostupných sad SDK klienta. Azure Media Services nabízí sadu SDK klienta Media Services pro Microsoft .NET. Chcete-li získat oprávnění k přístupu k prostředkům mediálních služeb a rozhraní API mediálních služeb, musíte být nejprve ověřeni. 

Mediální služby podporují [ověřování na základě azure active directory (Azure AD).](../../active-directory/fundamentals/active-directory-whatis.md) Služba Azure Media REST vyžaduje, aby uživatel nebo aplikace, která vytváří požadavky rozhraní REST API, měla roli **přispěvatele** nebo **vlastníka** pro přístup k prostředkům. Další informace najdete [v tématu Začínáme s řízením přístupu na základě rolí na webu Azure Portal](../../role-based-access-control/overview.md).  

Tento dokument poskytuje přehled o tom, jak získat přístup k rozhraní API mediálních služeb pomocí rozhraní REST nebo .NET API.

> [!NOTE]
> 1. června 2018 byla zastaralá autorizace řízení přístupu.

## <a name="access-control"></a>Řízení přístupu

Aby byl požadavek Azure Media REST úspěšný, musí mít volající uživatel roli přispěvatele nebo vlastníka pro účet Mediální ch služeb, ke které se pokouší získat přístup.  
Pouze uživatel s rolí Vlastník může poskytnout mediální prostředek (účet) přístup novým uživatelům nebo aplikacím. Role přispěvatele může přistupovat pouze k mediálnímu prostředku.
Neoprávněné požadavky se nezdaří, se stavovým kódem 401. Pokud se zobrazí tento kód chyby, zkontrolujte, zda má uživatel přiřazenou roli přispěvatele nebo vlastníka pro účet mediálních služeb uživatele. Můžete to zkontrolovat na webu Azure Portal. Vyhledejte svůj mediální účet a klikněte na kartu **Řízení přístupu.** 

![Karta Řízení přístupu](./media/media-services-use-aad-auth-to-access-ams-api/media-services-access-control.png)

## <a name="types-of-authentication"></a>Typy ověřování 
 
Při použití ověřování Azure AD s Azure Media Services, máte dvě možnosti ověřování:

- **Ověření uživatele**. Ověřte osobu, která používá aplikaci k interakci s prostředky Mediálních služeb. Interaktivní aplikace by měla nejprve vyzvat uživatele k zadání pověření uživatele. Příkladem je aplikace konzoly pro správu používaná oprávněnými uživateli ke sledování úloh kódování nebo živého streamování. 
- **Ověřování instančního objektu**. Ověřte službu. Aplikace, které běžně používají tuto metodu ověřování, jsou aplikace, které spouštějí služby daemon, služby střední vrstvy nebo naplánované úlohy. Příklady jsou webové aplikace, aplikace funkcí, aplikace logiky, rozhraní API a mikroslužeb.

### <a name="user-authentication"></a>Ověřování uživatelů 

Aplikace, které by měly používat metodu ověřování uživatelů, jsou nativní aplikace pro správu nebo monitorování: mobilní aplikace, aplikace pro Windows a konzolové aplikace. Tento typ řešení je užitečný, pokud chcete, aby byla lidská interakce se službou v jednom z následujících scénářů:

- Monitorování řídicího panelu pro úlohy kódování.
- Monitorování řídicího panelu pro vaše živé přenosy.
- Aplikace pro správu pro uživatele pro stolní počítače nebo mobilní zařízení pro správu prostředků v účtu Mediální služby.

> [!NOTE]
> Tato metoda ověřování by neměla být použita pro aplikace orientované na spotřebitele. 

Nativní aplikace musí nejprve získat přístupový token z Azure AD a potom použít při provádění požadavků HTTP rozhraní API Media Services REST. Přidejte přístupový token do hlavičky požadavku. 

Následující diagram znázorňuje typický tok interaktivního ověřování aplikací: 

![Diagram nativních aplikací](./media/media-services-use-aad-auth-to-access-ams-api/media-services-native-aad-app1.png)

V předchozím diagramu čísla představují tok požadavků v chronologickém pořadí.

> [!NOTE]
> Při použití metody ověřování uživatele sdílejí všechny aplikace stejné (výchozí) nativní ID klienta aplikace a identifikátor URI přesměrování nativní aplikace. 

1. Vyzvat uživatele k zadání pověření.
2. Požádejte o přístupový token Azure AD s následujícími parametry:  

   * Koncový bod klienta Azure AD.

       Informace o tenantovi se můžou načítat z webu Azure Portal. Umístěte kurzor na jméno přihlášeného uživatele v pravém horním rohu.
   * Identifikátor URI prostředku mediálních služeb. 

       Tento identifikátor URI je stejný pro účty mediálních služeb, které\/jsou ve stejném prostředí Azure (například https: /rest.media.azure.net).

   * Media Services (nativní) ID klienta aplikace.
   * Identifikátor URI přesměrovává aplikace Media Services (nativní)
   * Identifikátor URI prostředků pro službu REST Media Services.
        
       Identifikátor URI představuje koncový bod rozhraní https://test03.restv2.westus.media.azure.net/api/)REST API (například .

     Pokud chcete získat hodnoty pro tyto parametry, [přečtěte si informace o použití portálu Azure Portal pro přístup k nastavení ověřování Azure AD](media-services-portal-get-started-with-aad.md) pomocí možnosti ověřování uživatelů.

3. Přístupový token Služby Azure AD se odesílá klientovi.
4. Klient odešle požadavek do rozhraní AZURE Media REST API s přístupovým tokenem Azure AD.
5. Klient získá zpět data z Media Services.

Informace o použití ověřování Azure AD ke komunikaci s požadavky REST pomocí sady Media Services .NET client SDK najdete v [tématu Použití ověřování Azure AD pro přístup k rozhraní API mediálních služeb pomocí rozhraní .NET](media-services-dotnet-get-started-with-aad.md). 

Pokud nepoužíváte sadku SDK klienta Media Services .NET, musíte ručně vytvořit požadavek na přístupový token Azure AD pomocí parametrů popsaných v kroku 2. Další informace najdete v [tématu Jak pomocí knihovny ověřování Azure AD získat token Azure AD](../../active-directory/azuread-dev/active-directory-authentication-libraries.md).

### <a name="service-principal-authentication"></a>Ověřování instančních objektů

Aplikace, které běžně používají tuto metodu ověřování jsou aplikace, které spouštějí služby střední vrstvy a naplánované úlohy: webové aplikace, aplikace funkcí, aplikace logiky, api a mikroslužby. Tato metoda ověřování je také vhodná pro interaktivní aplikace, ve kterých můžete chtít použít účet služby ke správě prostředků.

Při použití metody ověřování instančního objektu k vytváření spotřebitelských scénářů je ověřování obvykle zpracováno ve střední vrstvě (prostřednictvím některého rozhraní API) a nikoli přímo v mobilní nebo desktopové aplikaci. 

Chcete-li použít tuto metodu, vytvořte aplikaci Azure AD a instanční objekt ve vlastním tenantovi. Po vytvoření aplikace udělit aplikaci přispěvatel nebo vlastník role přístup k účtu Mediální služby. Můžete to provést na webu Azure Portal, pomocí azure cli nebo pomocí skriptu PowerShell. Můžete také použít existující aplikaci Azure AD. Můžete zaregistrovat a spravovat své aplikace Azure AD a instanční objekt [na webu Azure Portal](media-services-portal-get-started-with-aad.md). Můžete to udělat také pomocí [Azure CLI](media-services-use-aad-auth-to-access-ams-api.md) nebo [PowerShell](media-services-powershell-create-and-configure-aad-app.md). 

![Aplikace střední úrovně](./media/media-services-use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

Po vytvoření aplikace Azure AD získáte hodnoty pro následující nastavení. Pro ověřování potřebujete tyto hodnoty:

- ID klienta 
- Tajný klíč klienta 

Na předchozím obrázku představují čísla tok požadavků v chronologickém pořadí:
    
1. Aplikace střední vrstvy (webové rozhraní API nebo webová aplikace) požaduje přístupový token Azure AD, který má následující parametry:  

   * Koncový bod klienta Azure AD.

       Informace o tenantovi se můžou načítat z webu Azure Portal. Umístěte kurzor na jméno přihlášeného uživatele v pravém horním rohu.
   * Identifikátor URI prostředku mediálních služeb. 

       Tento identifikátor URI je stejný pro účty mediálních služeb, které jsou\/umístěny ve stejném prostředí Azure (například https: /rest.media.azure.net).

   * Identifikátor URI prostředků pro službu REST Media Services.

       Identifikátor URI představuje koncový bod rozhraní https://test03.restv2.westus.media.azure.net/api/)REST API (například .

   * Hodnoty aplikace Azure AD: ID klienta a tajný klíč klienta.
    
     Pokud chcete získat hodnoty pro tyto parametry, [přečtěte si informace o použití portálu Azure Portal pro přístup k nastavení ověřování Azure AD](media-services-portal-get-started-with-aad.md) pomocí možnosti ověřování na uvěčňovacího objektu služby.

2. Přístupový token Azure AD se odesílá do střední vrstvy.
4. Střední vrstva odesílá požadavek do rozhraní AZURE Media REST API s tokenem Azure AD.
5. Střední vrstva získá zpět data z Media Services.

Další informace o tom, jak používat ověřování Azure AD ke komunikaci s požadavky REST pomocí sady Media Services .NET client SDK, najdete v [tématu Použití ověřování Azure AD pro přístup k rozhraní API Azure Media Services s rozhraním .NET](media-services-dotnet-get-started-with-aad.md). 

Pokud nepoužíváte sadku SDK klienta Media Services .NET, musíte ručně vytvořit požadavek na token Azure AD pomocí parametrů popsaných v kroku 1. Další informace najdete v [tématu Jak pomocí knihovny ověřování Azure AD získat token Azure AD](../../active-directory/azuread-dev/active-directory-authentication-libraries.md).

## <a name="troubleshooting"></a>Poradce při potížích

Výjimka: "Vzdálený server vrátil chybu: (401) Neautorizováno."

Řešení: Aby byl požadavek služby Media Services REST úspěšný, musí být volající uživatel rolí přispěvatele nebo vlastníka v účtu Mediálních služeb, ke které se pokouší získat přístup. Další informace naleznete v části [Řízení přístupu.](media-services-use-aad-auth-to-access-ams-api.md#access-control)

## <a name="resources"></a>Prostředky

Následující články jsou přehledy konceptů ověřování Azure AD: 

- [Scénáře ověřování adresované službou Azure AD](../../active-directory/develop/authentication-scenarios.md)
- [Přidání, aktualizace nebo odebrání aplikace ve službě Azure AD](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)
- [Konfigurace a správa řízení přístupu na základě rolí pomocí Prostředí PowerShell](../../role-based-access-control/role-assignments-powershell.md)

## <a name="next-steps"></a>Další kroky

* Na webu Azure Portal [můžete přistupovat k ověřování Azure AD ke spotřebovávat rozhraní API Azure Media Services](media-services-portal-get-started-with-aad.md).
* Pomocí ověřování Azure AD můžete přistupovat k [rozhraní API Azure Media Services pomocí rozhraní .NET](media-services-dotnet-get-started-with-aad.md).

