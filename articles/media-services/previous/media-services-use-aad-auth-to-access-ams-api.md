---
title: Přístup k rozhraní API Azure Media Services s ověřováním Azure Active Directory | Microsoft Docs
description: Přečtěte si o konceptech a krocích, které je potřeba použít Azure Active Directory (Azure AD) k ověření přístupu k rozhraní Azure Media Services API.
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
ms.openlocfilehash: 9dae3b1adc1f7fecf6a34fc51d2a35bf4c98ea65
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92425911"
---
# <a name="access-the-azure-media-services-api-with-azure-ad-authentication"></a>Přístup k rozhraní API služby Azure Media Services s využitím ověřování Azure AD 

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi [Media Services V3](../latest/index.yml). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-from-v2-to-v3.md) .

Rozhraní Azure Media Services API je rozhraní API pro RESTful. Můžete ji použít k provádění operací s prostředky médií pomocí REST API nebo pomocí dostupných klientských sad SDK. Azure Media Services nabízí Media Services klientskou sadu SDK pro Microsoft .NET. Abyste mohli získat autorizaci pro přístup k prostředkům a rozhraní API služby Media Services, musíte se nejprve ověřit. 

Media Services podporuje [ověřování založené na Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md). Služba Azure Media REST vyžaduje, aby uživatel nebo aplikace, která vytváří REST API žádosti, měla roli **Přispěvatel** nebo **vlastník** pro přístup k prostředkům. Další informace najdete v tématu [co je řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/overview.md).  

Tento dokument poskytuje přehled o tom, jak přistupovat k rozhraní Media Services API pomocí rozhraní REST API nebo rozhraní .NET API.

> [!NOTE]
> Autorizace Access Control byla zastaralá od 1. června 2018.

## <a name="access-control"></a>Řízení přístupu

Aby požadavek na médium Azure byl úspěšný, volající uživatel musí mít roli přispěvatel nebo vlastník pro účet Media Services, ke kterému se pokouší získat přístup.  
Přístup k prostředkům médií (účtu) pro nové uživatele nebo aplikace může udělit jenom uživatel s rolí vlastníka. Role přispěvatele má přístup pouze k mediálnímu zdroji.
Neautorizované požadavky selžou se stavovým kódem 401. Pokud se zobrazí tento kód chyby, zkontrolujte, jestli má uživatel přiřazenou roli přispěvatel nebo vlastník pro účet Media Services uživatele. Můžete to vrátit se změnami Azure Portal. Vyhledejte svůj mediální účet a potom klikněte na kartu **řízení přístupu** . 

![Karta řízení přístupu](./media/media-services-use-aad-auth-to-access-ams-api/media-services-access-control.png)

## <a name="types-of-authentication"></a>Typy ověřování 
 
Pokud používáte ověřování Azure AD s Azure Media Services, máte dvě možnosti ověřování:

- **Ověřování uživatelů**. Ověřte osobu, která aplikaci používá k interakci s Media Servicesmi prostředky. Interaktivní aplikace by nejdřív měla uživatele vyzvat k zadání přihlašovacích údajů uživatele. Příkladem je aplikace konzoly pro správu, kterou autorizovaní uživatelé používají k monitorování úloh kódování nebo živého streamování. 
- **Ověřování instančního objektu**. Ověří službu. Mezi aplikace, které běžně využívají tuto metodu ověřování, patří aplikace spouštějící služby démonů, služby střední úrovně nebo naplánované úlohy. Příklady jsou webové aplikace, aplikace Function App, Logic Apps, API a mikroslužby.

### <a name="user-authentication"></a>Ověřování uživatelů 

Aplikace, které by měly používat metodu ověřování uživatele, jsou nativní aplikace pro správu nebo monitorování: mobilní aplikace, aplikace pro Windows a konzolové aplikace. Tento typ řešení je užitečný v případě, že chcete lidské interakce se službou v jednom z následujících scénářů:

- Řídicí panel monitorování pro vaše úlohy kódování.
- Řídicí panel monitorování pro vaše živé streamy.
- Aplikace pro správu pro stolní nebo mobilní uživatele, kteří spravují prostředky v účtu Media Services.

> [!NOTE]
> Tato metoda ověřování by se neměla používat pro aplikace s přístupem uživatelů. 

Nativní aplikace musí nejdřív získat přístupový token z Azure AD a pak ho použít při provádění požadavků HTTP na Media Services REST API. Přidejte přístupový token do hlavičky žádosti. 

Následující diagram znázorňuje typický tok interaktivního ověřování aplikací: 

![Diagram nativních aplikací](./media/media-services-use-aad-auth-to-access-ams-api/media-services-native-aad-app1.png)

V předchozím diagramu čísla reprezentují tok požadavků v chronologickém pořadí.

> [!NOTE]
> Při použití metody ověřování uživatele budou všechny aplikace sdílet stejné (výchozí) ID klienta nativní aplikace a identifikátor URI přesměrování nativní aplikace. 

1. Vyzvat uživatele k zadání přihlašovacích údajů.
2. Vyžádejte přístupový token Azure AD s následujícími parametry:  

   * Koncový bod tenanta Azure AD.

       Informace o tenantovi lze získat z Azure Portal. Umístěte ukazatel myši na jméno přihlášeného uživatele v pravém horním rohu.
   * Media Services identifikátor URI prostředku. 

       Tento identifikátor URI je stejný pro účty Media Services, které jsou ve stejném prostředí Azure (například https: \/ /REST.Media.Azure.NET).

   * ID klienta aplikace Media Services (nativní).
   * Identifikátor URI přesměrování aplikace Media Services (nativní).
   * Identifikátor URI prostředku pro Media Services REST
        
       Identifikátor URI představuje koncový bod REST API (například https://test03.restv2.westus.media.azure.net/api/) .

     Pokud chcete získat hodnoty těchto parametrů, přečtěte si téma [použití Azure Portal pro přístup k nastavení ověřování Azure AD](media-services-portal-get-started-with-aad.md) pomocí možnosti ověřování uživatelů.

3. Přístupový token Azure AD se pošle klientovi.
4. Klient odešle požadavek na Azure Media REST API pomocí přístupového tokenu Azure AD.
5. Klient získá zpět data z Media Services.

Informace o tom, jak pomocí ověřování Azure AD komunikovat s požadavky REST pomocí klientské sady SDK Media Services .NET, najdete v tématu [použití ověřování Azure AD pro přístup k rozhraní API Media Services pomocí .NET](media-services-dotnet-get-started-with-aad.md). 

Pokud nepoužíváte sadu SDK klienta Media Services .NET, musíte ručně vytvořit žádost o přístupový token Azure AD pomocí parametrů popsaných v kroku 2. Další informace najdete v tématu [jak pomocí knihovny ověřování Azure AD získat token Azure AD](../../active-directory/azuread-dev/active-directory-authentication-libraries.md).

### <a name="service-principal-authentication"></a>Ověřování instančních objektů

Aplikace, které běžně používají tuto metodu ověřování, jsou aplikace, které spouštějí služby střední vrstvy a naplánované úlohy: webové aplikace, aplikace Function App, Logic Apps, rozhraní API a mikroslužby. Tato metoda ověřování je také vhodná pro interaktivní aplikace, ve kterých byste mohli chtít použít účet služby ke správě prostředků.

Když použijete metodu ověřování instančního objektu k vytváření zákaznických scénářů, ověřování se obvykle zpracovává uprostřed (přes některé rozhraní API), nikoli přímo v mobilní nebo desktopové aplikaci. 

Chcete-li použít tuto metodu, vytvořte aplikaci a instanční objekt služby Azure AD ve vlastním tenantovi. Po vytvoření aplikace udělte přispěvateli aplikace nebo roli vlastníka přístup k účtu Media Services. To můžete provést v Azure Portal, pomocí rozhraní příkazového řádku Azure nebo pomocí skriptu PowerShellu. Můžete také použít existující aplikaci Azure AD. [V Azure Portal](media-services-portal-get-started-with-aad.md)můžete zaregistrovat a spravovat svoji aplikaci a instanční objekt služby Azure AD. Můžete to také provést pomocí [Azure CLI](media-services-use-aad-auth-to-access-ams-api.md) nebo [PowerShellu](media-services-powershell-create-and-configure-aad-app.md). 

![Aplikace střední vrstvy](./media/media-services-use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

Po vytvoření aplikace Azure AD získáte hodnoty pro následující nastavení. Pro ověřování potřebujete tyto hodnoty:

- ID klienta 
- Tajný klíč klienta 

Na předchozím obrázku čísla reprezentují tok požadavků v chronologickém pořadí:
    
1. Aplikace střední vrstvy (webové rozhraní API nebo webová aplikace) žádá o přístupový token Azure AD s následujícími parametry:  

   * Koncový bod tenanta Azure AD.

       Informace o tenantovi lze získat z Azure Portal. Umístěte ukazatel myši na jméno přihlášeného uživatele v pravém horním rohu.
   * Media Services identifikátor URI prostředku. 

       Tento identifikátor URI je stejný pro účty Media Services, které se nacházejí ve stejném prostředí Azure (například https: \/ /REST.Media.Azure.NET).

   * Identifikátor URI prostředku pro Media Services REST

       Identifikátor URI představuje koncový bod REST API (například https://test03.restv2.westus.media.azure.net/api/) .

   * Hodnoty aplikace Azure AD: ID klienta a tajný kód klienta.
    
     Pokud chcete získat hodnoty těchto parametrů, přečtěte si téma [použití Azure Portal pro přístup k nastavení ověřování Azure AD](media-services-portal-get-started-with-aad.md) pomocí možnosti ověřování instančního objektu.

2. Přístupový token Azure AD se pošle do střední úrovně.
4. Střední vrstva odesílá požadavek do Azure Media REST API s tokenem Azure AD.
5. Střední vrstva vrátí data z Media Services.

Další informace o použití ověřování Azure AD ke komunikaci s požadavky REST pomocí klientské sady SDK Media Services .NET najdete v tématu [použití ověřování Azure AD pro přístup k rozhraní API Azure Media Services pomocí .NET](media-services-dotnet-get-started-with-aad.md). 

Pokud nepoužíváte sadu SDK klienta Media Services .NET, musíte ručně vytvořit žádost o token Azure AD pomocí parametrů popsaných v kroku 1. Další informace najdete v tématu [jak pomocí knihovny ověřování Azure AD získat token Azure AD](../../active-directory/azuread-dev/active-directory-authentication-libraries.md).

## <a name="troubleshooting"></a>Řešení potíží

Výjimka: vzdálený server vrátil chybu: (401) Neautorizováno.

Řešení: aby byla žádost o Media Services REST úspěšná, volající uživatel musí být rolí přispěvatel nebo Owner v účtu Media Services, ke kterému se pokouší získat přístup. Další informace najdete v části [řízení přístupu](media-services-use-aad-auth-to-access-ams-api.md#access-control) .

## <a name="resources"></a>Zdroje a prostředky

Následující články jsou přehledem konceptů ověřování Azure AD: 

- [Scénáře ověřování řešené službou Azure AD](../../active-directory/develop/authentication-vs-authorization.md)
- [Přidání, aktualizace nebo odebrání aplikace v Azure AD](../../active-directory/develop/quickstart-register-app.md)
- [Přidání nebo odebrání přiřazení rolí v Azure pomocí Azure PowerShellu](../../role-based-access-control/role-assignments-powershell.md)

## <a name="next-steps"></a>Další kroky

* Pro [přístup k ověřování Azure AD použijte Azure Portal pro využívání rozhraní API Azure Media Services](media-services-portal-get-started-with-aad.md).
* Použijte ověřování Azure AD pro [přístup k rozhraní API Azure Media Services pomocí .NET](media-services-dotnet-get-started-with-aad.md).
