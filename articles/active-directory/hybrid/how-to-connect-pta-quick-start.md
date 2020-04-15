---
title: Předávací ověřování Azure AD – úvodní příručka | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak začít s předávacím ověřováním služby Azure Active Directory (Azure AD).
services: active-directory
keywords: Předávací ověřování Azure AD Connect, instalace služby Active Directory, požadované součásti pro Azure AD, Jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/13/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b84e972584562be741919c7dccb6bdfe1bdea628
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312861"
---
# <a name="azure-active-directory-pass-through-authentication-quickstart"></a>Předávací ověřování služby Azure Active Directory: Úvodní příručka

## <a name="deploy-azure-ad-pass-through-authentication"></a>Nasazení předávacího ověřování Azure AD

Předávací ověřování Azure Active Directory (Azure AD) umožňuje uživatelům přihlásit se k místním i cloudovým aplikacím pomocí stejných hesel. Předávací ověřování připisuje uživatele ověřením jejich hesel přímo proti místnímu službě Active Directory.

>[!IMPORTANT]
>Pokud migrujete ze služby AD FS (nebo jiných federačních technologií) na předávací ověřování, důrazně doporučujeme, abyste se řídili naším podrobným průvodcem nasazením, který byl zveřejněn [zde](https://aka.ms/adfstoPTADPDownload).

Podle těchto pokynů nasaďte předávací ověřování ve svém tenantovi:

## <a name="step-1-check-the-prerequisites"></a>Krok 1: Kontrola předpokladů

Ujistěte se, že jsou na místě následující požadavky.

### <a name="in-the-azure-active-directory-admin-center"></a>V Centru pro správu Služby Azure Active Directory

1. Vytvořte účet globálního správce pouze pro cloud v tenantovi Azure AD. Tímto způsobem můžete spravovat konfiguraci vašeho tenanta, pokud vaše místní služby selžou nebo se stanou nedostupnými. Přečtěte si [o přidání účtu globálního správce pouze pro cloud](../active-directory-users-create-azure-portal.md). Dokončení tohoto kroku je důležité zajistit, že se nedostanete uzamčenz vašeho tenanta.
2. Přidejte do klienta Azure AD jeden nebo více [vlastních názvů domén.](../active-directory-domains-add-azure-portal.md) Uživatelé se mohou přihlásit pomocí jednoho z těchto názvů domén.

### <a name="in-your-on-premises-environment"></a>V místním prostředí

1. Identifikujte server se systémem Windows Server 2012 R2 nebo novějším pro spuštění služby Azure AD Connect. Pokud již není povoleno, [povolte tls 1.2 na serveru](./how-to-connect-install-prerequisites.md#enable-tls-12-for-azure-ad-connect). Přidejte server do stejné doménové struktury služby Active Directory jako uživatelé, jejichž hesla je třeba ověřit.
2. Nainstalujte [nejnovější verzi Služby Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) na server identifikovaný v předchozím kroku. Pokud už máte Azure AD Connect spuštěna, ujistěte se, že verze je 1.1.750.0 nebo novější.

    >[!NOTE]
    >Azure AD Connect verze 1.1.557.0, 1.1.558.0, 1.1.561.0 a 1.1.614.0 mají problém související se synchronizací hash hesel. Pokud _nemáte_ v úmyslu používat synchronizaci hodnot hash hesel ve spojení s předávacím ověřováním, přečtěte si [poznámky k verzi Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#116470).

3. Identifikujte jeden nebo více dalších serverů (se systémem Windows Server 2012 R2 nebo novějším s povoleným protokolem TLS 1.2), na kterých můžete spustit samostatné agenty ověřování. Tyto další servery jsou potřebné k zajištění vysoké dostupnosti požadavků na přihlášení. Přidejte servery do stejné doménové struktury služby Active Directory jako uživatelé, jejichž hesla je třeba ověřit.

    >[!IMPORTANT]
    >V produkčním prostředí doporučujeme, abyste ve vašem tenantovi běželi minimálně 3 agenti ověřování. Existuje systémový limit 40 agentů ověřování na klienta. A jako osvědčený postup, zacházet se všemi servery se systémem Autentizační agenti jako tier 0 systémy (viz [odkaz](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).

4. Pokud je brána firewall mezi servery a Azure AD, nakonfigurujte následující položky:
   - Ujistěte se, že agenti ověřování můžou do Služby Azure AD pořazovat *odchozí* požadavky na následující porty:

     | Číslo portu | Jak se používá |
     | --- | --- |
     | **80** | Stáhne seznamy odvolaných certifikátů (CRL) při ověřování certifikátu TLS/SSL. |
     | **443** | Zpracovává veškerou odchozí komunikaci se službou. |
     | **8080** (volitelně) | Agenti ověřování hlásí svůj stav každých deset minut přes port 8080, pokud port 443 není k dispozici. Tento stav se zobrazí na portálu Azure AD. Port 8080 se _nepoužívá_ pro přihlášení uživatele. |
     
     Pokud brána firewall vynucuje pravidla podle původních uživatelů, otevřete tyto porty pro přenosy ze služeb systému Windows, které běží jako síťová služba.
   - Pokud brána firewall nebo proxy server umožňuje seznam povolených dns, přidejte připojení k ** \*možnostem .msappproxy.net** a ** \*.servicebus.windows.net**. Pokud ne, povolte přístup k [rozsahům IP adres datového centra Azure](https://www.microsoft.com/download/details.aspx?id=41653), které se aktualizují každý týden.
   - Vaši agenti pro ověřování potřebují přístup k **login.windows.net** a **login.microsoftonline.com** pro počáteční registraci. Otevřete také bránu firewall pro tyto adresy URL.
   - Pro ověření certifikátu odblokujte následující adresy URL: **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80**a **www\.microsoft.com:80**. Vzhledem k tomu, že tyto adresy URL se používají pro ověření certifikátu s jinými produkty společnosti Microsoft, je možné, že tyto adresy URL již byly odblokovány.

### <a name="azure-government-cloud-prerequisite"></a>Předpoklad cloudu Azure Government
Před povolením předávacího ověřování prostřednictvím Azure AD Connect s krokem 2 stáhněte nejnovější verzi agenta PTA z webu Azure Portal.  Musíte se ujistit, že váš agent je verze **x.x.xxx.x** nebo novější.  Informace o ověření agenta naleznete v [tématu Upgrade ověřovacích agentů](how-to-connect-pta-upgrade-preview-authentication-agents.md)

Po stažení nejnovější verze agenta postupujte podle níže uvedených pokynů ke konfiguraci předávacího ověřování prostřednictvím služby Azure AD Connect.

## <a name="step-2-enable-the-feature"></a>Krok 2: Povolení funkce

Povolte předávací ověřování prostřednictvím [služby Azure AD Connect](whatis-hybrid-identity.md).

>[!IMPORTANT]
>Předávací ověřování můžete povolit na primárním nebo pracovním serveru Azure AD Connect. Důrazně doporučujeme povolit z primárního serveru. Pokud v budoucnu nastavujete pracovní server Služby Azure AD Connect, **musíte** jako možnost přihlášení dál vybírat předávací ověřování. výběrem jiné možnosti **zakážete** předávací ověřování v tenantovi a přepíšete nastavení na primárním serveru.

Pokud instalujete Azure AD Connect poprvé, zvolte [vlastní instalační cestu](how-to-connect-install-custom.md). Na přihlašovací stránce **uživatele** zvolte **Předávací ověřování** jako **metodu Přihlášení**. Po úspěšném dokončení předávací agent ověřování je nainstalován na stejném serveru jako Azure AD Connect. Kromě toho je ve vašem tenantovi povolena předávací ověřování.

![Azure AD Connect: Přihlášení uživatele](./media/how-to-connect-pta-quick-start/sso3.png)

Pokud jste už nainstalovali Azure AD Connect pomocí [expresní instalace](how-to-connect-install-express.md) nebo [vlastní instalační](how-to-connect-install-custom.md) cesty, vyberte změnit úlohu **přihlášení uživatele** ve službě Azure AD Connect a pak vyberte **Další**. Pak vyberte **předávací ověřování** jako metodu přihlášení. Po úspěšném dokončení předávací ověřování agent je nainstalován na stejném serveru jako Azure AD Connect a funkce je povolena ve vašem tenantovi.

![Azure AD Connect: Změna přihlášení uživatele](./media/how-to-connect-pta-quick-start/changeusersignin.png)

>[!IMPORTANT]
>Předávací ověřování je funkce na úrovni klienta. Zapnutí ovlivňuje přihlášení uživatelů ve _všech_ spravovaných doménách ve vašem tenantovi. Pokud přecházíte ze služby AD FS (AD FS) na předávací ověřování, měli byste počkat alespoň 12 hodin před vypnutím infrastruktury služby AD FS. Tato čekací doba je zajistit, že uživatelé mohou udržet přihlášení k Exchange ActiveSync během přechodu. Další pomoc při migraci ze služby AD FS na předávací ověřování najdete v našem podrobném plánu nasazení, který je zveřejněn [zde](https://aka.ms/adfstoptadpdownload).

## <a name="step-3-test-the-feature"></a>Krok 3: Testování funkce

Podle těchto pokynů ověřte, zda jste správně povolili předávací ověřování:

1. Přihlaste se do [Centra pro správu Služby Azure Active Directory](https://aad.portal.azure.com) pomocí přihlašovacích údajů globálního správce pro vašeho tenanta.
2. V levém podokně vyberte **Službu Azure Active Directory.**
3. Vyberte **Azure AD Connect**.
4. Ověřte, zda se funkce **předávacího ověřování** zobrazuje jako **povolená**.
5. Vyberte **možnost Předávací ověřování**. V podokně **předávacího ověřování** jsou uvedeny servery, na kterých jsou nainstalováni agenti ověřování.

![Centrum pro správu Azure Active Directory: Podokno Azure AD Connect](./media/how-to-connect-pta-quick-start/pta7.png)

![Centrum pro správu Služby Azure Active Directory: Podokno předávacího ověřování](./media/how-to-connect-pta-quick-start/pta8.png)

V této fázi se mohou uživatelé ze všech spravovaných domén ve vašem tenantovi přihlásit pomocí předávacího ověřování. Uživatelé z federovaných domén se však nadále přihlašují pomocí služby AD FS nebo jiného poskytovatele federace, který jste dříve nakonfigurovali. Pokud převedete doménu z federované na spravovanou, všichni uživatelé z této domény se automaticky začnou přihlašovat pomocí předávacího ověřování. Funkce předávacího ověřování nemá vliv na uživatele pouze cloud.

## <a name="step-4-ensure-high-availability"></a>Krok 4: Zajištění vysoké dostupnosti

Pokud plánujete nasadit předávací ověřování v produkčním prostředí, měli byste nainstalovat další samostatné agenty ověřování. Nainstalujte tyto agenty ověřování na _jiné_ servery, než je ten, který používá Azure AD Connect. Toto nastavení poskytuje vysokou dostupnost pro požadavky na přihlášení uživatele.

>[!IMPORTANT]
>V produkčním prostředí doporučujeme, abyste ve vašem tenantovi běželi minimálně 3 agenti ověřování. Existuje systémový limit 40 agentů ověřování na klienta. A jako osvědčený postup, zacházet se všemi servery se systémem Autentizační agenti jako tier 0 systémy (viz [odkaz](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).

Instalace více předávacích agentů ověřování zajišťuje vysokou dostupnost, ale není deterministické vyrovnávání zatížení mezi agenty ověřování. Chcete-li zjistit, kolik agentů ověřování potřebujete pro vašeho tenanta, zvažte špičku a průměrné zatížení žádostí o přihlášení, které očekáváte, že se zobrazí na vašem tenantovi. Jako měřítko může jeden agent ověřování zpracovat 300 až 400 ověřování za sekundu na standardním 4jádrovém procesoru, 16 GB RAM serveru.

Chcete-li odhadnout síťový provoz, použijte následující pokyny pro velikost:
- Každý požadavek má velikost datové části (0.5K + 1K * num_of_agents) bajtů, to znamená data z Azure AD na agenta ověřování. Zde "num_of_agents" označuje počet agentů ověřování registrovaných ve vašem tenantovi.
- Každá odpověď má velikost datové části 1 kB bajtů, to znamená data z agenta ověřování do Azure AD.

Pro většinu zákazníků jsou pro vysokou dostupnost a kapacitu dostačující celkem tři agenti ověřování. Agenti pro ověřování byste měli nainstalovat v blízkosti řadičů domény, abyste zlepšili latenci přihlášení.

Chcete-li začít, stáhněte si software agenta ověřování podle těchto pokynů:

1. Chcete-li stáhnout nejnovější verzi agenta ověřování (verze 1.5.193.0 nebo novější), přihlaste se do [Centra pro správu služby Azure Active Directory](https://aad.portal.azure.com) pomocí přihlašovacích údajů globálního správce vašeho tenanta.
2. V levém podokně vyberte **Službu Azure Active Directory.**
3. Vyberte **Azure AD Connect**, vyberte **Předávací ověřování**a pak vyberte Zprostředkovatel **stahování**.
4. Vyberte tlačítko **Přijmout podmínky & stažení.**

![Centrum pro správu Služby Azure Active Directory: Tlačítko Stáhnout agenta ověřování](./media/how-to-connect-pta-quick-start/pta9.png)

![Centrum pro správu služby Azure Active Directory: Podokno Agent pro stahování](./media/how-to-connect-pta-quick-start/pta10.png)

>[!NOTE]
>Můžete také přímo [stáhnout software Authentication Agent](https://aka.ms/getauthagent). _Před_ instalací zkontrolujte a přijměte [smluvní podmínky](https://aka.ms/authagenteula) agenta pro ověřování.

Existují dva způsoby nasazení samostatného agenta ověřování:

Nejprve to můžete udělat interaktivně pouhým spuštěním spuštěného spustitelného souboru staženého agenta ověřování a poskytnutím pověření globálního správce klienta po zobrazení výzvy.

Za druhé můžete vytvořit a spustit skript bezobslužného nasazení. To je užitečné, pokud chcete nasadit více agentů ověřování najednou nebo nainstalovat agenty ověřování na servery systému Windows, které nemají povolené uživatelské rozhraní nebo ke kterým nemáte přístup pomocí vzdálené plochy. Zde jsou pokyny, jak používat tento přístup:

1. Chcete-li nainstalovat agenta ověřování, spusťte následující příkaz: `AADConnectAuthAgentSetup.exe REGISTERCONNECTOR="false" /q`.
2. Agenta ověřování můžete zaregistrovat u naší služby pomocí prostředí Windows PowerShell. Vytvořte objekt `$cred` pověření prostředí PowerShell, který obsahuje uživatelské jméno a heslo globálního správce pro vašeho klienta. Spusťte následující příkaz, který nahradí * \<uživatelské jméno\> * a * \<heslo\>*:

        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $User, $SecurePassword
3. Přejděte na **C:\Program Files\Microsoft Azure AD Connect** Authentication `$cred` Agent a spusťte následující skript pomocí vytvořeného objektu:

        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\" -moduleName "PassthroughAuthPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature PassthroughAuthentication

>[!IMPORTANT]
>Pokud je agent ověřování nainstalovaný ve virtuálním počítači, nemůžete naklonovat virtuální počítač a nastavit jiného agenta ověřování. Tato metoda není **podporována**.

## <a name="step-5-configure-smart-lockout-capability"></a>Krok 5: Konfigurace funkce inteligentního uzamčení

Smart Lockout pomáhá při zamykání špatných herců, kteří se pokoušejí uhodnout hesla uživatelů nebo pomocí metod hrubé síly, aby se dostali dovnitř. Konfigurací nastavení smart lockout u Azure AD a /nebo vhodné nastavení uzamčení v místním active directory, útoky lze odfiltrovat dříve, než se dostanou do služby Active Directory. V [tomto článku](../authentication/howto-password-smart-lockout.md) se dozvíte další informace o tom, jak nakonfigurovat nastavení inteligentního uzamčení ve vašem tenantovi za účelem ochrany uživatelských účtů.

## <a name="next-steps"></a>Další kroky
- [Migrace ze služby AD FS do předávacího ověřování](https://aka.ms/adfstoptadp) – podrobný průvodce migrací ze služby AD FS (nebo jiných federačních technologií) do předávacího ověřování.
- [Inteligentní uzamčení](../authentication/howto-password-smart-lockout.md): Přečtěte si, jak nakonfigurovat funkci Inteligentní uzamčení ve vašem tenantovi za účelem ochrany uživatelských účtů.
- [Aktuální omezení](how-to-connect-pta-current-limitations.md): Zjistěte, které scénáře jsou podporovány s předávacím ověřováním a které nejsou.
- [Technické podrobné informace](how-to-connect-pta-how-it-works.md): Zjistěte, jak funguje funkce předávacího ověřování.
- [Nejčastější dotazy](how-to-connect-pta-faq.md): Najděte odpovědi na často kladené otázky.
- [Poradce při potížích](tshoot-connect-pass-through-authentication.md): Přečtěte si, jak vyřešit běžné problémy s funkcí Předávací ověřování.
- [Podrobné informace o zabezpečení](how-to-connect-pta-security-deep-dive.md): Získejte technické informace o funkci předávacího ověřování.
- [Azure AD bezproblémové jednotné přihlašovací](how-to-connect-sso.md)služby : Další informace o této doplňkové funkce.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Fórum Azure Active Directory můžete zasít nové požadavky na funkce.
