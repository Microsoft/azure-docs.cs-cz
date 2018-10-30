---
title: Přístup k rozhraní API služby Azure Media Services pomocí ověřování Azure Active Directory | Dokumentace Microsoftu
description: Další informace o konceptech a kroky pro použití služby Azure Active Directory (Azure AD) k ověření přístupu k rozhraní API služby Azure Media Services.
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
ms.date: 10/29/2018
ms.author: juliako
ms.openlocfilehash: 320afdb1e4f71150680e9bed6a4c1c9955c99936
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230620"
---
# <a name="access-the-azure-media-services-api-with-azure-ad-authentication"></a>Přístup k rozhraní API Azure Media Services pomocí ověřování Azure AD
 
Rozhraní API služby Azure Media Services je rozhraní RESTful API. Slouží k provádění operací s prostředky media pomocí rozhraní REST API nebo pomocí sady SDK klienta k dispozici. Azure Media Services nabízí Media Services Klientská sada SDK pro rozhraní Microsoft .NET. K autorizaci pro přístup k prostředkům služby Media Services a rozhraní API služby Media Services, musíte nejprve být ověřeni. 

Služba Media Services podporuje [Azure Active Directory (Azure AD) – ověřování na základě](../../active-directory/fundamentals/active-directory-whatis.md). Vyžaduje službu Azure Media REST, že uživatel nebo aplikace, která bude rozhraní REST API vyžaduje mít **Přispěvatel** nebo **vlastníka** rolí pro přístup k prostředkům. Další informace najdete v tématu [Začínáme s řízením přístupu na základě rolí na portálu Azure portal](../../role-based-access-control/overview.md).  

> [!IMPORTANT]
> V současné době služba Media Services podporuje model ověřování Azure Access Control service. Ale autorizaci na řízení přístupu se přestanou používat 1. června 2018. Doporučujeme, abyste na model ověřování Azure AD migrovali co nejdříve.

Tento dokument poskytuje přehled o tom, jak získat přístup k rozhraní API služby Media Services pomocí rozhraní API .NET nebo REST.

## <a name="access-control"></a>Řízení přístupu

Azure Media REST žádosti úspěšné musí mít volajícího uživatele roli Přispěvatel nebo vlastník účtu Media Services, který se pokouší o přístup.  
Pouze uživatele s rolí vlastník můžete poskytnout přístup k prostředkům (účet) media nových uživatelů nebo aplikací. Role Přispěvatel přístup pouze mediální zdroj.
Neoprávněné požadavky nesplní se stavovým kódem 401. Pokud se zobrazí tento kód chyby, zkontrolujte, zda má uživatel roli Přispěvatel nebo vlastník přiřazené k uživatelskému účtu Media Services. Můžete zkontrolovat na portálu Azure portal. Najděte pro váš účet media a potom klikněte **řízení přístupu** kartu. 

![Kartě pro řízení přístupu](./media/media-services-use-aad-auth-to-access-ams-api/media-services-access-control.png)

## <a name="types-of-authentication"></a>Typy ověřování 
 
Pokud používáte ověřování Azure AD pomocí služby Azure Media Services, máte dvě možnosti ověřování:

- **Ověřování uživatelů**. Ověření osoba, která používá aplikace k interakci s prostředky služby Media Services. Interaktivní aplikace by měly nejprve vyzve uživatele k přihlašovacím údajům uživatele. Příkladem je aplikace konzoly správy Autorizovaní uživatelé používají ke sledování úloh kódování nebo živého streamování. 
- **Ověřování instančních objektů**. Ověření služby. Aplikace, které se běžně používají tuto metodu ověřování jsou aplikace, na kterých běží služby démonů, střední vrstvy služby nebo podle plánu. Příklady jsou webové aplikace, aplikace function App, logic apps, rozhraní API a mikroslužeb.

### <a name="user-authentication"></a>Ověřování uživatelů 

Jsou aplikace, které by měl používat metodu ověřování uživatelů správy a monitorování nativních aplikací: mobilní aplikace, aplikace pro Windows a konzolových aplikací. Tento typ řešení je užitečné, když chcete, aby lidské interakce se službou v jednom z následujících scénářů:

- Monitorování řídicího panelu pro své kódovací úlohy.
- Monitorování řídicího panelu pro živé streamy.
- Aplikace pro správu pro počítače nebo mobilního uživatelům ke správě prostředků v účtu Azure Media Services.

> [!NOTE]
> Tato metoda ověřování by neměly používat pro zákaznické aplikace. 

Nativní aplikaci musí nejprve získání přístupového tokenu z Azure AD a pak použít při provádění požadavků protokolu HTTP rozhraní REST API pro Media Services. Přidání přístupového tokenu do hlavičky žádosti. 

Následující diagram znázorňuje tok ověřování Typická interaktivní aplikace: 

![Diagram nativní aplikace](./media/media-services-use-aad-auth-to-access-ams-api/media-services-native-aad-app1.png)

Čísla udávají na předchozím obrázku, tok žádostí v chronologickém pořadí.

> [!NOTE]
> Při použití metody ověřování uživatele, všechny aplikace sdílejí stejné ID klienta nativní aplikace (výchozí) a identifikátor URI přesměrování nativní aplikace. 

1. Vyzvat uživatele k zadání přihlašovacích údajů.
2. Žádost o přístupový token Azure AD s následujícími parametry:  

    * Koncový bod pro tenanta Azure AD.

        Informace o tenantovi můžete získat z webu Azure portal. Umístěte ukazatel myši přes jméno přihlášeného uživatele v horním pravém rohu.
    * Identifikátor URI prostředku služby Media Services. 

        Pomocí tohoto identifikátoru URI je stejný pro účty Media Services, které jsou ve stejném prostředí Azure (například https://rest.media.azure.net).

    * ID klienta aplikace služby Media Services (nativní).
    * Identifikátor URI pro přesměrování aplikace služby Media Services (nativní).
    * Identifikátor URI pro REST Media Services.
        
        Identifikátor URI představuje koncový bod rozhraní REST API (například https://test03.restv2.westus.media.azure.net/api/).

    K získání hodnot těchto parametrů, najdete v článku [přístup k nastavení ověřování Azure AD pomocí webu Azure portal](media-services-portal-get-started-with-aad.md) pomocí možnosti ověřování uživatele.

3. Přístupový token Azure AD posílá do klienta.
4. Klient odešle žádost na rozhraní REST API Azure Media pomocí přístupového tokenu Azure AD.
5. Klient získá zpět data ze služby Media Services.

Informace o tom, jak pomocí ověřování Azure AD můžete komunikovat s požadavky REST pomocí klienta Media Services .NET SDK najdete v tématu [ověřování pomocí služby Azure AD pro přístup k rozhraní API služby Media Services s .NET](media-services-dotnet-get-started-with-aad.md). 

Pokud nepoužíváte klienta Media Services .NET SDK, musíte ručně vytvořit požadavek na token přístup Azure AD pomocí parametrů, které jsou uvedené v kroku 2. Další informace najdete v tématu [jak používat Azure AD Authentication Library pro získání tokenu Azure AD](../../active-directory/develop/active-directory-authentication-libraries.md).

### <a name="service-principal-authentication"></a>Ověřování instančních objektů

Aplikace, které se běžně používají tuto metodu ověřování, jsou aplikace, které běží na střední vrstvy služeb a naplánované úlohy: webové aplikace, aplikace function App, logic apps, rozhraní API a mikroslužeb. Tato metoda ověřování je také vhodný pro interaktivní aplikace, ve kterých můžete chtít použít účet služby pro správu prostředků.

Při použití metody ověřování instančních objektů služby vytvářet scénáře ověřování obvykle probíhá ve střední vrstvě (přes několik rozhraní API) a ne přímo v desktopové nebo mobilní aplikaci. 

Při použití této metody, vytvořte aplikaci Azure AD a služby instanční objekt v tenantovi svou vlastní. Po vytvoření aplikace, poskytují přístup k roli Přispěvatel nebo vlastník aplikace k účtu Media Services. Můžete provést na webu Azure Portal, pomocí rozhraní příkazového řádku Azure nebo pomocí skriptu prostředí PowerShell. Můžete také použít existující aplikaci Azure AD. Můžete registrovat a spravovat vaše aplikace Azure AD a instanční objekt [na webu Azure Portal](media-services-portal-get-started-with-aad.md). Můžete také můžete to provést pomocí [rozhraní příkazového řádku Azure](media-services-use-aad-auth-to-access-ams-api.md) nebo [Powershellu](media-services-powershell-create-and-configure-aad-app.md). 

![Aplikace střední vrstvy](./media/media-services-use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

Po vytvoření aplikace Azure AD, zobrazí se hodnoty pro následující nastavení. Tyto hodnoty budete potřebovat pro ověřování:

- ID klienta 
- Tajný klíč klienta 

Čísla udávají na předchozím obrázku, tok žádostí v chronologickém pořadí:
    
1. Aplikace střední vrstvy (webového rozhraní API nebo webové aplikace) požaduje přístupový token Azure AD, který má následující parametry:  

    * Koncový bod pro tenanta Azure AD.

        Informace o tenantovi můžete získat z webu Azure portal. Umístěte ukazatel myši přes jméno přihlášeného uživatele v horním pravém rohu.
    * Identifikátor URI prostředku služby Media Services. 

        Pomocí tohoto identifikátoru URI je stejný pro účtů Media Services, které se nacházejí ve stejné prostředí Azure (například https://rest.media.azure.net).

    * Identifikátor URI pro REST Media Services.

        Identifikátor URI představuje koncový bod rozhraní REST API (například https://test03.restv2.westus.media.azure.net/api/).

    * Hodnoty aplikace Azure AD: ID klienta a tajný kód klienta.
    
    K získání hodnot těchto parametrů, najdete v článku [přístup k nastavení ověřování Azure AD pomocí webu Azure portal](media-services-portal-get-started-with-aad.md) s využitím ověřování instančních objektů možnost služby.

2. Přístupový token Azure AD se odešle do střední vrstvy.
4. Střední vrstva odešle požadavek REST API služby Azure Media s tokenem Azure AD.
5. Střední vrstva získá zpět data ze služby Media Services.

Další informace o tom, jak pomocí ověřování Azure AD můžete komunikovat s požadavky REST pomocí klienta Media Services .NET SDK najdete v tématu [ověřování pomocí služby Azure AD pro přístup k rozhraní API služby Azure Media Services s .NET](media-services-dotnet-get-started-with-aad.md). 

Pokud nepoužíváte klienta Media Services .NET SDK, musíte ručně vytvořit požadavek tokenu Azure AD s použitím parametrů jsou uvedené v kroku 1. Další informace najdete v tématu [jak používat Azure AD Authentication Library pro získání tokenu Azure AD](../../active-directory/develop/active-directory-authentication-libraries.md).

## <a name="troubleshooting"></a>Řešení potíží

Výjimka: "vzdálený server vrátil chybu: (401) Neoprávněno."

Řešení: Media Services REST žádost proběhla úspěšně, volajícího uživatele musí být roli Přispěvatel nebo vlastník účtu Media Services, který se pokouší o přístup. Další informace najdete v tématu [řízení přístupu](media-services-use-aad-auth-to-access-ams-api.md#access-control) oddílu.

## <a name="resources"></a>Zdroje a prostředky

Následující články jsou přehledy konceptů ověřování Azure AD: 

- [Scénáře ověřování zákazníky a vyřešené ve službě Azure AD](../../active-directory/develop/authentication-scenarios.md)
- [Přidání, aktualizace nebo odebrání aplikace ve službě Azure AD](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)
- [Konfigurace a správa řízení přístupu na základě Role pomocí prostředí PowerShell](../../role-based-access-control/role-assignments-powershell.md)

## <a name="next-steps"></a>Další postup

* Pomocí webu Azure portal k [ověřování přístupu Azure AD používat rozhraní API služby Azure Media Services](media-services-portal-get-started-with-aad.md).
* Ověřování pomocí služby Azure AD [přístup k rozhraní API služby Azure Media Services s .NET](media-services-dotnet-get-started-with-aad.md).

