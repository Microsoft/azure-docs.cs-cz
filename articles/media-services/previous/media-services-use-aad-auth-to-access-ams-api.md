---
title: Přístup k rozhraní API služby Azure Media Services pomocí ověřování Azure Active Directory | Microsoft Docs
description: Další informace o konceptech a kroky pro použití služby Azure Active Directory (Azure AD) k ověření přístupu k rozhraní API služby Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.openlocfilehash: a7f20c22b39458134d3dcd42b7e13860c03bad58
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790402"
---
# <a name="access-the-azure-media-services-api-with-azure-ad-authentication"></a>Přístup k Azure Media Services API pomocí ověřování Azure AD
 
Rozhraní API služby Azure Media Services je rozhraní RESTful API. Můžete ji použít k provádění operací na zdrojů médií pomocí rozhraní REST API nebo pomocí sady SDK k dispozici klienta. Azure Media Services nabízí klienta Media Services SDK pro rozhraní Microsoft .NET. K autorizaci pro přístup k prostředkům Media Services a rozhraní API služby Media Services, je nutné nejprve ověřit. 

Služba Media Services podporuje [Azure Active Directory (Azure AD)-ověřování na základě](../../active-directory/active-directory-whatis.md). Azure Media REST služba vyžaduje, aby uživatel nebo aplikace, která umožňuje rozhraní API REST požádá o mít buď **Přispěvatel** nebo **vlastníka** role pro přístup k prostředkům. Další informace najdete v tématu [Začínáme s řízením přístupu na základě rolí na portálu Azure](../../role-based-access-control/overview.md).  

> [!IMPORTANT]
> V současné době Media Services podporuje model řízení přístupu Azure služby ověřování. Řízení přístupu autorizace však bude na 1. června 2018 zastaralá. Doporučujeme, abyste na model ověřování Azure AD migrovali co nejdříve.

Tento dokument poskytuje přehled toho, jak získat přístup k rozhraní API služby Media Services pomocí rozhraní API .NET nebo REST.

## <a name="access-control"></a>Řízení přístupu

Pro úspěšné požadavku Azure Media REST volání uživatel musí mít roli Přispěvatel nebo vlastníka účtu Media Services, který se pokouší o přístup.  
Pouze uživatele k roli vlastníka můžete udělit přístup k prostředkům (účet) média pro nové uživatele nebo aplikace. Role Přispěvatel má přístup pouze k prostředku média.
Neoprávněné žádosti nezdaří s stavový kód 401. Pokud se tento kód chyby, zkontrolujte, zda váš uživatel má roli Přispěvatel nebo vlastníka přiřazená k účtu Media Services uživatele. Zkontrolovat to můžete na portálu Azure. Najděte pro svůj účet mediálních a potom klikněte **řízení přístupu** kartě. 

![Karta řízení přístupu](./media/media-services-use-aad-auth-to-access-ams-api/media-services-access-control.png)

## <a name="types-of-authentication"></a>Typy ověřování 
 
Při použití ověřování Azure AD pomocí služby Azure Media Services, máte dvě možnosti ověřování:

- **Ověření uživatele**. Ověření osoba, která používá aplikace komunikovat s prostředky Media Services. Interaktivní aplikace by měla nejprve vyzvou uživatele k zadání přihlašovacích údajů uživatele. Příkladem je aplikace konzoly správy Autorizovaní uživatelé používají ke sledování úloh kódování nebo živé streamování. 
- **Objekt zabezpečení ověřování služby**. Ověření služby. Aplikace, které běžně používají tuto metodu ověřování jsou aplikace, které běží služby démon, střední vrstvy služby nebo naplánované úlohy. Příklady jsou webové aplikace, funkce aplikací, aplikace logiky, rozhraní API a mikroslužeb.

### <a name="user-authentication"></a>Ověřování uživatelů 

Jsou aplikace, které by měl použít metodu ověřování uživatele pro správu nebo monitorování nativní aplikace: mobilní aplikace, aplikace pro Windows a konzolové aplikace. Tohoto typu řešení je užitečné, když chcete zásahem ze strany se službou v jednom z následujících scénářů:

- Řídicí panel pro kódování úlohy monitorování.
- Řídicí panel pro vaše živé datové proudy monitorování.
- Správa aplikací pro desktop či mobile uživatelům spravovat prostředky v účtu Media Services.

> [!NOTE]
> Tato metoda ověřování není používat u určených aplikací. 

Nativní aplikace musíte nejprve získat přístupový token ze služby Azure AD a pak použít při provádění požadavků HTTP ke službě Media Services REST API. Přidání přístupového tokenu do hlaviček požadavku. 

Následující diagram znázorňuje tok ověřování Typická interaktivní aplikace: 

![Diagram nativní aplikace](./media/media-services-use-aad-auth-to-access-ams-api/media-services-native-aad-app1.png)

Na předchozím obrázku čísla, která představují tok žádostí v chronologickém pořadí.

> [!NOTE]
> Při použití metody ověřování uživatelů, všechny aplikace sdílejí stejné ID klienta nativní aplikace (výchozí) a identifikátor URI přesměrování nativní aplikace. 

1. Vyzvat uživatele k zadání přihlašovacích údajů.
2. Žádosti o token přístupu Azure AD s následujícími parametry:  

    * Koncový bod pro klienta Azure AD.

        Informace o klienta můžete načíst z portálu Azure. Umístěte kurzor na název přihlášeného uživatele v horním pravém rohu.
    * Identifikátor URI prostředku Media Services. 

        Tento identifikátor URI je stejný pro účty služby Media Services, které jsou ve stejném prostředí Azure (například https://rest.media.azure.net).

    * ID klienta aplikace Media Services (nativní).
    * Identifikátor URI pro přesměrování služby Media Services (nativní) aplikace.
    * Identifikátor URI pro REST Media Services.
        
        Identifikátor URI představuje koncový bod REST API (například https://test03.restv2.westus.media.azure.net/api/).

    Chcete-li získat hodnoty pro tyto parametry, přečtěte si téma [používat portál Azure pro přístup k nastavení ověřování Azure AD](media-services-portal-get-started-with-aad.md) pomocí možnosti ověřování uživatele.

3. Přístupový token služby Azure AD se může odeslat klientovi.
4. Klient odešle požadavek na rozhraní API služby REST Azure média s tímto tokenem přístupu Azure AD.
5. Klient získá zpět data ze služby Media Services.

Informace o tom, jak používat Azure AD ověřování ke komunikaci s požadavky REST pomocí klienta Media Services .NET SDK najdete v tématu [ověřování pomocí služby Azure AD pro přístup k rozhraní API Media Services pomocí rozhraní .NET](media-services-dotnet-get-started-with-aad.md). 

Pokud nepoužíváte klienta Media Services .NET SDK, musíte ručně vytvořit požadavek tokenu přístupu Azure AD pomocí parametrů popsané v kroku 2. Další informace najdete v tématu [jak používat knihovnu ověřování služby Azure AD k získání tokenu Azure AD](../../active-directory/develop/active-directory-authentication-libraries.md).

### <a name="service-principal-authentication"></a>Ověřování instančních objektů

Aplikace, které běžně používají tuto metodu ověřování, jsou aplikace, které běží služby střední vrstvy a naplánované úlohy: webové aplikace, funkce aplikací, aplikace logiky, rozhraní API a mikroslužeb. Tato metoda ověřování je také vhodná pro interaktivní aplikace, ve kterých můžete chtít použít účet služby ke správě prostředků.

Když použijete metodu ověření objektu služby k sestavení spotřebitelské scénáře, ověřování obvykle používá ve střední vrstvě (prostřednictvím některé rozhraní API) a ne přímo v mobilních nebo desktopových aplikací. 

Chcete-li použít tuto metodu, vytvořte aplikaci Azure AD a služby hlavní v jeho vlastní klienta. Po vytvoření aplikace, udělte aplikace přístup k roli Přispěvatel nebo vlastníka účtu Media Services. Musíte na portálu Azure pomocí rozhraní příkazového řádku Azure nebo pomocí skriptu prostředí PowerShell. Můžete taky použít existující aplikaci Azure AD. Můžete zaregistrovat a spravovat aplikaci Azure AD a instanční objekt [na portálu Azure](media-services-portal-get-started-with-aad.md). Také to provedete pomocí [Azure CLI 2.0](media-services-use-aad-auth-to-access-ams-api.md) nebo [prostředí PowerShell](media-services-powershell-create-and-configure-aad-app.md). 

![Vícevrstvé aplikace](./media/media-services-use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

Po vytvoření aplikace Azure AD, získat hodnoty pro následující nastavení. Je třeba tyto hodnoty pro ověřování:

- ID klienta 
- Tajný klíč klienta 

Na předchozím obrázku čísla, která představují tok žádostí v chronologickém pořadí:
    
1. Vícevrstvé aplikace (webového rozhraní API nebo webové aplikace) požaduje přístupový token služby Azure AD, který má následující parametry:  

    * Koncový bod pro klienta Azure AD.

        Informace o klienta můžete načíst z portálu Azure. Umístěte kurzor na název přihlášeného uživatele v horním pravém rohu.
    * Identifikátor URI prostředku Media Services. 

        Tento identifikátor URI je stejný pro účty služby Media Services, které se nacházejí ve stejném prostředí Azure (například https://rest.media.azure.net).

    * Identifikátor URI pro REST Media Services.

        Identifikátor URI představuje koncový bod REST API (například https://test03.restv2.westus.media.azure.net/api/).

    * Hodnoty aplikace Azure AD: ID klienta a tajný klíč klienta.
    
    Chcete-li získat hodnoty pro tyto parametry, přečtěte si téma [používat portál Azure pro přístup k nastavení ověřování Azure AD](media-services-portal-get-started-with-aad.md) pomocí možnosti ověření objektu služby.

2. Přístupový token služby Azure AD se odesílají do střední vrstvy.
4. Střední vrstva odešle požadavek na REST API pro Azure Media s tokenem Azure AD.
5. Střední vrstva získá zpět data ze služby Media Services.

Další informace o tom, jak používat Azure AD ověřování ke komunikaci s požadavky REST pomocí klienta Media Services .NET SDK najdete v tématu [ověřování pomocí služby Azure AD pro přístup k rozhraní API služby Azure Media Services pomocí rozhraní .NET](media-services-dotnet-get-started-with-aad.md). 

Pokud nepoužíváte klienta Media Services .NET SDK, musíte ručně vytvořit požadavek tokenu Azure AD pomocí parametrů, které jsou popsané v kroku 1. Další informace najdete v tématu [jak používat knihovnu ověřování služby Azure AD k získání tokenu Azure AD](../../active-directory/develop/active-directory-authentication-libraries.md).

## <a name="troubleshooting"></a>Řešení potíží

Výjimka: "vzdálený server vrátil chybu: (401) Neautorizováno."

Řešení: K úspěšné žádosti média služby REST, volání uživatel musí být Přispěvatel nebo vlastníka role, které se pokouší získat přístup k účtu Media Services. Další informace najdete v tématu [řízení přístupu](media-services-use-aad-auth-to-access-ams-api.md#access-control) části.

## <a name="resources"></a>Zdroje a prostředky

V následujících článcích jsou Přehled konceptů ověřování Azure AD: 

- [Scénáře ověřování řešené pomocí Azure AD](../../active-directory/develop/active-directory-authentication-scenarios.md#basics-of-authentication-in-azure-ad)
- [Přidání, aktualizace nebo odebrání aplikace ve službě Azure AD](../../active-directory/develop/active-directory-integrating-applications.md)
- [Konfigurace a správa řízení přístupu na základě Role pomocí prostředí PowerShell](../../role-based-access-control/role-assignments-powershell.md)

## <a name="next-steps"></a>Další postup

* Použití portálu Azure [ověřování přístupu k Azure AD, které využívají rozhraní API služby Azure Media Services](media-services-portal-get-started-with-aad.md).
* Ověřování pomocí služby Azure AD [přístup k rozhraní API služby Azure Media Services s .NET](media-services-dotnet-get-started-with-aad.md).

