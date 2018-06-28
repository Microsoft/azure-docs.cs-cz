---
title: Azure AD předávací ověřování – rychlý start | Microsoft Docs
description: Tento článek popisuje, jak začít pracovat s Azure Active Directory (Azure AD) předávací ověřování.
services: active-directory
keywords: Azure AD Connect předávací ověřování, instalace služby Active Directory, požadované součásti pro Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 365d9cde29292b40847ec2a0ac6f7d4011222f2c
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37028927"
---
# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Azure předávací ověřování služby Active Directory: Rychlý start

## <a name="deploy-azure-ad-pass-through-authentication"></a>Nasadit Azure AD předávací ověřování

Předávací ověřování Azure Active Directory (Azure AD) umožňuje uživatelům přihlásit se k místní i cloudové aplikace pomocí stejnými hesly. Předávací ověřování přihlášení uživatele pomocí ověřování hesla přímo na místní služby Active Directory.

>[!IMPORTANT]
>Používáte-li tuto funkci pomocí verze preview, ujistěte se, že upgrade verze preview agentů ověřování pomocí pokynů uvedených v [ověřování Azure Active Directory průchozí: Upgrade preview Ověřování agentů](./active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md).

Postupujte podle těchto pokynů můžete nasadit předávací ověřování:

## <a name="step-1-check-the-prerequisites"></a>Krok 1: Kontrola předpokladů

Ujistěte se, že jsou splněné následující požadavky.

### <a name="in-the-azure-active-directory-admin-center"></a>V Centru správy služby Azure Active Directory

1. Vytvořte účet globálního správce jenom pro cloud v klientovi Azure AD. Tímto způsobem konfigurace vašeho klienta můžete spravovat, by měla místní služby nezdaří, nebo k dispozici. Další informace o [přidání účtu globálního správce jenom pro cloud](../active-directory-users-create-azure-portal.md). Dokončení tohoto kroku je důležité zajistit, že nezůstanete z vašeho klienta.
2. Přidejte jednu nebo více [vlastních názvů domén](../active-directory-domains-add-azure-portal.md) ke klientovi Azure AD. Vaši uživatelé mohou přihlásit pomocí jeden z těchto názvů domény.

### <a name="in-your-on-premises-environment"></a>V místním prostředí

1. Identifikujte a server se systémem Windows Server 2012 R2 nebo novější, aby se spustila Azure AD Connect. Přidejte server do stejné doménové struktuře služby Active Directory jako uživatele, jejichž hesla je třeba provést ověření.
2. Nainstalujte [nejnovější verzi služby Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) na serveru identifikovat v předchozím kroku. Pokud je již spuštěna Azure AD Connect, ujistěte se, zda je verze 1.1.644.0 nebo novější.

    >[!NOTE]
    >Azure AD Connect verze 1.1.557.0, 1.1.558.0, 1.1.561.0 a 1.1.614.0 mít problém související s synchronizaci hodnoty hash hesla. Pokud jste _nemáte_ v úmyslu použít synchronizaci hodnoty hash hesla ve spojení se předávací ověřování, přečtěte si [poznámky k verzi Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470).

3. Určit další server (systém Windows Server 2012 R2 nebo novější) kde můžete spouštět samostatný Agent ověřování. Verze agenta ověřování musí být 1.5.193.0 nebo novější. Tento další server je potřeba zajistit vysokou dostupnost žádostí o přihlášení. Přidejte server do stejné doménové struktuře služby Active Directory jako uživatele, jejichž hesla je třeba provést ověření.
4. Pokud je brána firewall mezi servery a Azure AD, nakonfigurujte následující položky:
   - Ujistěte se, že ověřování agentů provádět *odchozí* požadavky do Azure AD přes následující porty:
   
    | Číslo portu | Jak se používají |
    | --- | --- |
    | **80** | Soubory ke stažení seznamů odvolaných certifikátů (CRL) při ověřování certifikátu SSL |
    | **443** | Zpracovává všechny odchozí komunikace se službou |
   
    Pokud brána firewall vynucuje pravidla podle uživatelů, původní, otevřete tyto porty pro přenos ze služby Windows, které běží jako síťové služby.
   - Pokud je brána firewall nebo proxy server umožňuje povolených DNS, povolených připojení k  **\*. msappproxy.net** a  **\*. servicebus.windows.net**. Pokud ne, povolit přístup k [rozsahy IP adres Azure datacenter](https://www.microsoft.com/download/details.aspx?id=41653), se aktualizují každý týden.
   - Ověřování agenty potřebují přístup k **login.windows.net** a **login.microsoftonline.com** pro počáteční registraci. Otevřete bránu firewall pro tyto adresy URL také.
   - Pro ověření certifikátu, odblokovat následující adresy URL: **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80**, a  **www.microsoft.com:80**. Tyto adresy URL se používají k ověření certifikátu s dalšími produkty Microsoftu. Již můžete mít tyto adresy URL odblokováno.

## <a name="step-2-enable-exchange-activesync-support-optional"></a>Krok 2: Povolit podporu protokolu Exchange ActiveSync (volitelné)

Postupujte podle těchto pokynů můžete povolit podporu protokolu Exchange ActiveSync:

1. Použití [Exchange PowerShell](https://technet.microsoft.com/library/mt587043(v=exchg.150).aspx) spusťte následující příkaz:
```
Get-OrganizationConfig | fl per*
```

2. Zkontrolujte hodnotu `PerTenantSwitchToESTSEnabled` nastavení. Pokud je hodnota **true**, váš klient je správně nakonfigurována. Obvykle se jedná o případ pro většinu zákazníků. Pokud je hodnota **false**, spusťte následující příkaz:
```
Set-OrganizationConfig -PerTenantSwitchToESTSEnabled:$true
```

3. Ověřte, že hodnota `PerTenantSwitchToESTSEnabled` nastavení je teď nastavená na **true**. Počkejte, než jednu hodinu, než budete pokračovat k dalšímu kroku.

Pokud jste v tomto kroku musí čelit všechny problémy, podívejte se [Průvodce odstraňováním potíží s](active-directory-aadconnect-troubleshoot-pass-through-authentication.md#exchange-activesync-configuration-issues).

## <a name="step-3-enable-the-feature"></a>Krok 3: Povolení funkce

Povolit předávací ověřování prostřednictvím [Azure AD Connect](active-directory-aadconnect.md).

>[!IMPORTANT]
>Na serveru primární nebo pracovní Azure AD Connect můžete povolit předávací ověřování. Měli byste povolit ji z primárního serveru.

Pokud Azure AD Connect instalujete poprvé, vyberte [vlastní cesta](active-directory-aadconnect-get-started-custom.md). Na **přihlášení uživatele** vyberte **předávací ověřování** jako **metoda přihlašování**. Při úspěšném dokončení agenta předávací ověřování nainstalovaný na stejném serveru jako Azure AD Connect. Kromě toho že předávací ověřování je povolena funkce na klientovi.

![Azure AD Connect: Přihlášení uživatele](./media/active-directory-aadconnect-sso/sso3.png)

Pokud jste už nainstalovali Azure AD Connect s použitím [Expresní instalace](active-directory-aadconnect-get-started-express.md) nebo [vlastní instalaci](active-directory-aadconnect-get-started-custom.md) cestu, vyberte **změnit přihlášení uživatele** úloh na Azure AD Připojit a potom vyberte **Další**. Potom vyberte **předávací ověřování** jako metoda přihlašování. Při úspěšném dokončení je Agent předávací ověřování nainstalovaný na stejném serveru jako Azure AD Connect a tato funkce je povolena na klientovi.

![Azure AD Connect: Změnit přihlášení uživatele](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>Předávací ověřování je funkce úrovni klienta. Zapnutí ovlivňuje přihlášení pro uživatele mezi _všechny_ spravované domény ve vašem klientovi. Pokud přepínáte ze služby Active Directory Federation Services (AD FS) na předávací ověřování, byste měli počkat alespoň 12 hodin před ukončením infrastruktury služby AD FS. Tato čekací doba je zajistit, že uživatelé můžete zachovat přihlášení k protokolu Exchange ActiveSync během přechodu.

## <a name="step-4-test-the-feature"></a>Krok 4: Testování funkce

Postupujte podle těchto pokynů k ověření, že jste povolili ověřování průchozí správně:

1. Přihlaste se k [centra pro správu Azure Active Directory](https://aad.portal.azure.com) pomocí přihlašovacích údajů globálního správce pro vašeho klienta.
2. Vyberte **Azure Active Directory** v levém podokně.
3. Vyberte **Azure AD Connect**.
4. Ověřte, zda **předávací ověřování** funkce se zobrazuje jako **povoleno**.
5. Vyberte **předávací ověřování**. **Předávací ověřování** podokně zobrazí seznam serverů, na kterém jsou nainstalované agenty ověřování.

![Centrum pro správu Azure Active Directory: podokně Azure AD Connect](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Centrum pro správu Azure Active Directory: podokně předávací ověřování](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

V této fázi můžete uživatele ze všech spravovaných domén ve vašem klientovi přihlásit pomocí předávacího ověřování. Uživatelé z federovaných domén se však nadále přihlašování pomocí služby AD FS nebo jiného zprostředkovatele federace, který jste dříve nakonfigurovali. Pokud převedete k doméně z federovaných spravovat, všichni uživatelé z této domény, automaticky spustí, přihlášení pomocí předávacího ověřování. Předávací ověřování funkce nemá vliv na uživatele jenom pro cloud.

## <a name="step-5-ensure-high-availability"></a>Krok 5: Zajištění vysoké dostupnosti

Pokud plánujete nasadit předávací ověřování v produkčním prostředí, musíte nainstalovat alespoň jeden další samostatné Agent ověřování. Instalace těchto agentů ověřování na servery _jiných_ než jedna spuštěná Azure AD Connect. Tato instalace vám poskytuje vysokou dostupnost pro požadavky přihlášení uživatele.

Postupujte podle těchto pokynů ke stažení softwaru agenta ověřování:

1. Chcete-li stáhnout nejnovější verzi agenta ověřování (verze 1.5.193.0 nebo novější), přihlaste se k [centra pro správu Azure Active Directory](https://aad.portal.azure.com) pomocí přihlašovacích údajů globálního správce vašeho klienta.
2. Vyberte **Azure Active Directory** v levém podokně.
3. Vyberte **Azure AD Connect**, vyberte **předávací ověřování**a potom vyberte **stáhnout agenta**.
4. Vyberte **přijmout podmínky a stahovat** tlačítko.

![Centrum pro správu Azure Active Directory: tlačítko Stáhnout agenta ověřování](./media/active-directory-aadconnect-pass-through-authentication/pta9.png)

![Centrum pro správu Azure Active Directory: podokně stáhnout agenta](./media/active-directory-aadconnect-pass-through-authentication/pta10.png)

>[!NOTE]
>Můžete také přímo stáhnout software agenta ověřování [zde](https://aka.ms/getauthagent). Přečtěte si a přijměte Agent ověřování [podmínkami služby](https://aka.ms/authagenteula) _před_ jeho instalaci.

Existují dva způsoby, jak nasadit samostatnou Agent ověřování:

První můžete provést interaktivně právě spuštěním spustitelný soubor stažený ověřování agenta a poskytnout pověření pro globálního správce vašeho klienta po zobrazení výzvy.

Druhý můžete vytvořit a spustit skript bezobslužné nasazení. To je užitečné, pokud chcete nasadit více ověřování agentů najednou, nebo nainstalovat agenty ověřování na serverech Windows, který není k dispozici uživatelské rozhraní, které jsou povolené nebo, ke kterému nelze přistupovat pomocí vzdálené plochy. Zde jsou pokyny pro tento postup:

1. Spusťte následující příkaz pro instalaci agenta ověřování: `AADConnectAuthAgentSetup.exe REGISTERCONNECTOR="false" /q`.
2. Agent ověřování můžete zaregistrovat v naší službě pomocí prostředí Windows PowerShell. Vytvořit objekt pověření prostředí PowerShell `$cred` obsahující heslo a uživatelské jméno globálního správce pro vašeho klienta. Spusťte následující příkaz, nahraďte *\<uživatelské jméno\>* a  *\<heslo\>*:
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
3. Přejděte na **Agent C:\Program Files\Microsoft Azure AD Connect ověřování** a spusťte následující skript pomocí `$cred` objekt, který jste vytvořili:
   
        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature PassthroughAuthentication

## <a name="next-steps"></a>Další postup
- [Inteligentní uzamčení](../authentication/howto-password-smart-lockout.md): Zjistěte, jak nakonfigurovat možnosti inteligentního uzamčení na vašeho klienta k ochraně uživatelské účty.
- [Aktuální omezení](active-directory-aadconnect-pass-through-authentication-current-limitations.md): Zjistěte, jaké scénáře jsou podporovány pomocí předávacího ověřování a ty, které nejsou.
- [Podrobné technické informace](active-directory-aadconnect-pass-through-authentication-how-it-works.md): pochopit, jak funguje funkci předávací ověřování.
- [Nejčastější dotazy](active-directory-aadconnect-pass-through-authentication-faq.md): Vyhledejte odpovědi na nejčastější dotazy.
- [Řešení potíží s](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Přečtěte si o řešení běžných problémů s funkcí předávací ověřování.
- [Podrobné informace zabezpečení](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): technické informace o funkci předávací ověřování.
- [Azure AD bezproblémové SSO](active-directory-aadconnect-sso.md): Další informace o této funkci vzájemně doplňují.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): použijte fóru Azure Active Directory do souboru žádosti o nové funkce.

