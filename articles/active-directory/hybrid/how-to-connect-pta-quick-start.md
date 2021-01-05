---
title: Předávací ověřování Azure AD – rychlý Start | Microsoft Docs
description: Tento článek popisuje, jak začít s předávacím ověřováním Azure Active Directory (Azure AD).
services: active-directory
keywords: Azure AD Connect předávací ověřování, instalace služby Active Directory, požadované součásti pro Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/13/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5394a2829af4b0cd7a1c817f6aad4ca5451cc4bc
ms.sourcegitcommit: 00aa5afaa9fac91f1059cfed3d8dbc954caaabe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2020
ms.locfileid: "97792428"
---
# <a name="azure-active-directory-pass-through-authentication-quickstart"></a>Azure Active Directory předávací ověřování: rychlý Start

## <a name="deploy-azure-ad-pass-through-authentication"></a>Nasazení předávacího ověřování Azure AD

Předávací ověřování Azure Active Directory (Azure AD) umožňuje uživatelům přihlašovat se pomocí stejných hesel k místním i cloudovým aplikacím. Předávací ověřování podepisuje uživatele v nástroji tím, že ověřuje hesla přímo v místní službě Active Directory.

>[!IMPORTANT]
>Pokud provádíte migraci z AD FS (nebo jiných federačních technologií) na předávací ověřování, důrazně doporučujeme, abyste provedli náš podrobný průvodce nasazením, který jste publikovali [tady](https://aka.ms/adfstoPTADPDownload).

>[!NOTE]
>Pokud provedete předávací ověřování pomocí Azure Governmentho cloudu, podívejte se na [Azure Government o hybridní identitě](./reference-connect-government-cloud.md).

Pomocí těchto pokynů nasaďte předávací ověřování ve vašem tenantovi:

## <a name="step-1-check-the-prerequisites"></a>Krok 1: ověření požadavků

Ujistěte se, že jsou splněné následující požadavky.

>[!IMPORTANT]
>Z hlediska zabezpečení by správci měli zacházet se serverem, na kterém běží agent PTA, jako by šlo o řadič domény.  Servery agenta PTA by měly být posílené na stejném řádku, jak je uvedeno v části [zabezpečení řadičů domény před útoky](/windows-server/identity/ad-ds/plan/security-best-practices/securing-domain-controllers-against-attack) .

### <a name="in-the-azure-active-directory-admin-center"></a>V centru pro správu Azure Active Directory

1. Vytvořte v tenantovi Azure AD jenom cloudový účet globálního správce. Tímto způsobem můžete spravovat konfiguraci vašeho tenanta, pokud vaše místní služby selžou nebo nebudou k dispozici. Seznamte [se s přidáním účtu globálního správce jenom pro Cloud](../fundamentals/add-users-azure-active-directory.md). Dokončení tohoto kroku je důležité, aby se zajistilo, že nebudete mít uzamčený přístup k vašemu tenantovi.
2. Přidejte jeden nebo více [vlastních názvů domén](../fundamentals/add-custom-domain.md) do svého tenanta služby Azure AD. Uživatelé se můžou přihlásit pomocí některého z těchto názvů domén.

### <a name="in-your-on-premises-environment"></a>V místním prostředí

1. Identifikujte Server se systémem Windows Server 2012 R2 nebo novějším, aby bylo možné spustit Azure AD Connect. Pokud ještě není povolená, [Povolte na serveru protokol TLS 1,2](./how-to-connect-install-prerequisites.md#enable-tls-12-for-azure-ad-connect). Přidejte server do stejné doménové struktury služby Active Directory jako uživatelé, jejichž hesla potřebujete ověřit. Je potřeba poznamenat, že instalace ověřovacího agenta Pass-Through v základních verzích Windows serveru není podporovaná. 
2. Nainstalujte [nejnovější verzi Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) na server identifikovaný v předchozím kroku. Pokud už máte Azure AD Connect spuštěnou, ujistěte se, že je verze 1.1.750.0 nebo novější.

    >[!NOTE]
    >Azure AD Connect verze 1.1.557.0, 1.1.558.0, 1.1.561.0 a 1.1.614.0 mají problém týkající se synchronizace hodnot hash hesel. Pokud _nehodláte_ použít synchronizaci hodnot hash hesel ve spojení s předávacím ověřováním, přečtěte si [poznámky k verzi Azure AD Connect](./reference-connect-version-history.md).

3. Identifikujte jeden nebo více dalších serverů (se systémem Windows Server 2012 R2 nebo novějším s povoleným protokolem TLS 1,2), kde můžete spouštět samostatné ověřovací agenty. Tyto další servery jsou potřebné k zajištění vysoké dostupnosti žádostí o přihlášení. Přidejte servery do stejné doménové struktury služby Active Directory jako uživatelé, jejichž hesla potřebujete ověřit.

    >[!IMPORTANT]
    >V produkčních prostředích doporučujeme, abyste ve svém tenantovi spustili minimálně 3 agenty ověřování. Omezení počtu 40 ověřovacích agentů na každého tenanta je systému. A osvědčeným postupem je považovat všechny servery, na kterých běží agenti ověřování, jako systémy vrstvy 0 (viz [referenční](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)informace).

4. Pokud je mezi vašimi servery a službou Azure AD brána firewall, nakonfigurujte následující položky:
   - Zajistěte, aby ověřovací agenti mohli vytvářet *odchozí* požadavky na službu Azure AD prostřednictvím následujících portů:

     | Číslo portu | Jak se používá |
     | --- | --- |
     | **80** | Stahuje seznamy odvolaných certifikátů (CRL) při ověřování certifikátu TLS/SSL. |
     | **443** | Zpracovává veškerou odchozí komunikaci se službou. |
     | **8080** (volitelné) | Agenti ověřování hlásí svůj stav každých deset minut přes port 8080, pokud není k dispozici port 443. Tento stav se zobrazuje na portálu Azure AD. Port _8080 se nepoužívá pro_ přihlášení uživatelů. |
     
     Pokud brána firewall vynutila pravidla podle prvotních uživatelů, otevřete tyto porty pro provoz ze služeb systému Windows, které jsou spuštěny jako síťová služba.
   - Pokud vaše brána firewall nebo proxy server umožňuje přidat záznamy DNS do povolených, přidejte připojení do souborů **\* . msappproxy.NET** a **\* . ServiceBus.Windows.NET**. V takovém případě povolte přístup k [rozsahům IP adres datacentra Azure](https://www.microsoft.com/download/details.aspx?id=41653), které se aktualizují týdně.
   - Vaše agenti ověřování potřebují pro počáteční registraci přístup k **Login.Windows.NET** a **Login.microsoftonline.com** . Otevřete taky bránu firewall pro tyto adresy URL.
    - Pro ověření certifikátu Odblokujte následující adresy URL: **crl3.DigiCert.com:80**, **crl4.DigiCert.com:80**, **ocsp.digicert.com:80**, **www \. d-trust.net:80**, **root-C3-CA2-2009.OCSP.d-Trust.NET:80**, **CRL.Microsoft.com:80**, **oneocsp.Microsoft.com:80** a **OCSP.msocsp.com:80**. Vzhledem k tomu, že se tyto adresy URL používají pro ověřování certifikátů s jinými produkty Microsoftu, tyto adresy URL už možná máte odblokované.

### <a name="azure-government-cloud-prerequisite"></a>Cloudová součást Azure Government
Před povolením předávacího ověřování prostřednictvím Azure AD Connect v kroku 2 si stáhněte nejnovější verzi agenta PTA z Azure Portal.  Musíte zajistit, aby byl váš Agent 1.5.1742.0 verze **.** nebo novější.  Postup ověření agenta najdete v tématu [Upgrade agentů ověřování](how-to-connect-pta-upgrade-preview-authentication-agents.md) .

Po stažení nejnovější verze agenta pokračujte níže uvedenými pokyny ke konfiguraci ověřování Pass-Through prostřednictvím Azure AD Connect.

## <a name="step-2-enable-the-feature"></a>Krok 2: povolení funkce

Povolení předávacího ověřování prostřednictvím [Azure AD Connect](whatis-hybrid-identity.md).

>[!IMPORTANT]
>Předávací ověřování můžete povolit na Azure AD Connect primárním nebo přípravném serveru. Důrazně doporučujeme povolit ho z primárního serveru. Pokud v budoucnu nastavujete Azure AD Connect přípravný Server, **musíte** pro možnost přihlášení dál zvolit předávací ověřování. Když vyberete jinou možnost, **zakážete** předávací ověřování u tenanta a přepíšete nastavení na primárním serveru.

Pokud Azure AD Connect instalujete poprvé, vyberte [vlastní cestu instalace](how-to-connect-install-custom.md). Na **přihlašovací stránce uživatele** jako **způsob přihlašování** vyberte **předávací ověřování** . Po úspěšném dokončení se do stejného serveru, jako je Azure AD Connect, nainstaluje agent předávacího ověřování. Kromě toho je ve vašem tenantovi povolená funkce předávacího ověřování.

![Azure AD Connect: přihlášení uživatele](./media/how-to-connect-pta-quick-start/sso3.png)

Pokud jste už Azure AD Connect nainstalovali pomocí [Expresní instalace](how-to-connect-install-express.md) nebo [vlastní instalační](how-to-connect-install-custom.md) cesty, vyberte v Azure AD Connect úkol **změnit přihlašování uživatelů** a pak vyberte **Další**. Pak jako metodu přihlašování vyberte **předávací ověřování** . Po úspěšném dokončení se do stejného serveru nainstaluje agent s průchozím ověřováním, který je Azure AD Connect a ve vašem tenantovi je povolená funkce.

![Azure AD Connect: Změna přihlášení uživatele](./media/how-to-connect-pta-quick-start/changeusersignin.png)

>[!IMPORTANT]
>Předávací ověřování je funkce na úrovni tenanta. Zapnutí této funkce má vliv na přihlášení uživatelů napříč _všemi_ spravovanými doménami ve vašem tenantovi. Pokud přecházíte z Active Directory Federation Services (AD FS) (AD FS) na předávací ověřování, měli byste před vypnutím infrastruktury AD FS počkat aspoň 12 hodin. Tato čekací doba slouží k tomu, aby se uživatelé mohli během přechodu přihlašovat k Exchangi ActiveSync. Další nápovědu k migraci z AD FS na předávací ověřování najdete v našem podrobném plánu nasazení, který jsme publikovali [tady](https://aka.ms/adfstoptadpdownload).

## <a name="step-3-test-the-feature"></a>Krok 3: testování funkce

Postupujte podle těchto pokynů a ověřte, zda jste správně povolili předávací ověřování:

1. Přihlaste se k [centru pro správu Azure Active Directory](https://aad.portal.azure.com) pomocí přihlašovacích údajů globálního správce vašeho tenanta.
2. V levém podokně vyberte **Azure Active Directory** .
3. Vyberte **Azure AD Connect**.
4. Ověřte, že je funkce **předávacího ověřování** zobrazená jako **povolená**.
5. Vyberte **předávací ověřování**. V podokně **předávací ověřování** se zobrazí seznam serverů, na kterých jsou nainstalované vaše agenty ověřování.

![Centrum pro správu Azure Active Directory: Azure AD Connect podokno](./media/how-to-connect-pta-quick-start/pta7.png)

![Centrum pro správu Azure Active Directory: podokno předávací ověřování](./media/how-to-connect-pta-quick-start/pta8.png)

V této fázi se uživatelé ze všech spravovaných domén ve vašem tenantovi můžou přihlásit pomocí předávacího ověřování. Uživatelé z federovaných domén se však nadále přihlásí pomocí AD FS nebo jiného zprostředkovatele federace, který jste nakonfigurovali dříve. Pokud převedete doménu ze federované na spravovanou, všichni uživatelé z této domény se automaticky začnou přihlašovat pomocí předávacího ověřování. Funkce předávacího ověřování nemá vliv jenom na cloudové uživatele.

## <a name="step-4-ensure-high-availability"></a>Krok 4: zajištění vysoké dostupnosti

Pokud plánujete nasazení předávacího ověřování v produkčním prostředí, měli byste nainstalovat další samostatné ověřovací agenty. Nainstalovat tyto ověřovací agenty na servery _jiné_ než ta, ve které běží Azure AD Connect. Tato instalace poskytuje vysokou dostupnost pro žádosti o přihlášení uživatele.

>[!IMPORTANT]
>V produkčních prostředích doporučujeme, abyste ve svém tenantovi spustili minimálně 3 agenty ověřování. Omezení počtu 40 ověřovacích agentů na každého tenanta je systému. A osvědčeným postupem je považovat všechny servery, na kterých běží agenti ověřování, jako systémy vrstvy 0 (viz [referenční](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)informace).

Instalace více agentů předávacího ověřování zajišťuje vysokou dostupnost, ale nedeterministické vyrovnávání zatížení mezi ověřovacími agenty. Pokud chcete zjistit, kolik agentů pro ověřování potřebujete pro vašeho tenanta, vezměte v úvahu nejvyšší a průměrné zatížení žádostí o přihlášení, které očekáváte ve vašem tenantovi. Jako srovnávací test může jeden agent ověřování zpracovat 300 až 400 ověřování za sekundu na standardním procesoru se 4 jádry, 16 GB paměti RAM.

K odhadu provozu v síti použijte následující pokyny k určení velikosti:
- Každá žádost má velikost datové části (0,5 K + 1 tisíc * num_of_agents) bajtů, tj. data z Azure AD do ověřovacího agenta. Zde "num_of_agents" označuje počet agentů ověřování registrovaných ve vašem tenantovi.
- Každá odpověď má velikost datové části 1 tisíc bajtů, tj. data z ověřovacího agenta do služby Azure AD.

U většiny zákazníků jsou celkem tři agenti ověřování dostačující pro vysokou dostupnost a kapacitu. Měli byste nainstalovat agenty ověřování blízko k řadičům domény, aby se zlepšila latence přihlášení.

Chcete-li začít, postupujte podle těchto pokynů a stáhněte software ověřovacího agenta:

1. Pokud si chcete stáhnout nejnovější verzi ověřovacího agenta (verze 1.5.193.0 nebo novější), přihlaste se do [centra pro správu Azure Active Directory](https://aad.portal.azure.com) s přihlašovacími údaji globálního správce vašeho tenanta.
2. V levém podokně vyberte **Azure Active Directory** .
3. Vyberte **Azure AD Connect**, vyberte **předávací ověřování** a pak vyberte **Stáhnout agenta**.
4. Vyberte tlačítko **přijmout podmínky & stáhnout** .

![Centrum pro správu Azure Active Directory: tlačítko Stáhnout ověřovacího agenta](./media/how-to-connect-pta-quick-start/pta9.png)

![Centrum pro správu Azure Active Directory: podokno stáhnout agenta](./media/how-to-connect-pta-quick-start/pta10.png)

>[!NOTE]
>Můžete také přímo [Stáhnout software ověřovacího agenta](https://aka.ms/getauthagent). _Před_ instalací si přečtěte a přijměte [podmínky služby](https://aka.ms/authagenteula) ověřovacího agenta.

Existují dva způsoby, jak nasadit samostatného ověřovacího agenta:

Nejdřív to můžete provést interaktivně pouhým spuštěním staženého spustitelného agenta pro ověření a zadáním přihlašovacích údajů globálního správce klienta po zobrazení výzvy.

Za druhé můžete vytvořit a spustit skript bezobslužného nasazení. To je užitečné, pokud chcete nasadit více agentů ověřování najednou nebo nainstalovat ověřovací agenty na servery Windows, které nemají povolené uživatelské rozhraní nebo že nemáte přístup ke vzdálené ploše. V této části najdete pokyny k použití tohoto přístupu:

1. Spusťte následující příkaz pro instalaci ověřovacího agenta: `AADConnectAuthAgentSetup.exe REGISTERCONNECTOR="false" /q` .
2. Ověřovacího agenta můžete v rámci služby zaregistrovat pomocí prostředí Windows PowerShell. Vytvořte objekt přihlašovacích údajů prostředí PowerShell `$cred` , který obsahuje uživatelské jméno a heslo globálního správce pro vašeho tenanta. Spusťte následující příkaz a nahraďte *\<username\>* a *\<password\>* :

  ```powershell
  $User = "<username>"
  $PlainPassword = '<password>'
  $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
  $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $User, $SecurePassword
  ```
3. Do složky **C:\Program Files\Microsoft Azure AD Connect Authentication agent** a spusťte následující skript pomocí `$cred` objektu, který jste vytvořili:

  ```powershell
  RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\" -moduleName "PassthroughAuthPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature PassthroughAuthentication
  ```

>[!IMPORTANT]
>Pokud je na virtuálním počítači nainstalovaný agent ověřování, nemůžete naklonovat virtuální počítač a nastavit jiného ověřovacího agenta. Tato metoda není **podporována**.

## <a name="step-5-configure-smart-lockout-capability"></a>Krok 5: Konfigurace funkce inteligentního uzamčení

Inteligentní zamykání pomáhá zamykat chybné aktéry, které se pokoušejí uhodnout hesla uživatelů, nebo využívají metody hrubou silou, které se mají v systému získat. Když nakonfigurujete nastavení inteligentního uzamčení v Azure AD nebo příslušné nastavení uzamčení v místní službě Active Directory, můžete před dosažením služby Active Directory odfiltrovat útoky. Přečtěte si [Tento článek](../authentication/howto-password-smart-lockout.md) , kde najdete další informace o tom, jak nakonfigurovat nastavení inteligentního zamykání ve vašem tenantovi, aby se chránily vaše uživatelské účty.

## <a name="next-steps"></a>Další kroky
- [Migrace z AD FS na předávací ověřování](https://aka.ms/adfstoptadp) – podrobný průvodce migrací z AD FS (nebo jiných federačních technologií) na předávací ověřování.
- [Inteligentní uzamčení](../authentication/howto-password-smart-lockout.md): Naučte se konfigurovat funkci inteligentního uzamknutí pro vašeho tenanta pro ochranu uživatelských účtů.
- [Aktuální omezení](how-to-connect-pta-current-limitations.md): Zjistěte, které scénáře jsou podporovány při předávacím ověřování a které nejsou.
- [Technický hluboký podrobně](how-to-connect-pta-how-it-works.md): Zjistěte, jak funguje funkce předávacího ověřování.
- [Nejčastější dotazy](how-to-connect-pta-faq.md): Přečtěte si odpovědi na nejčastější dotazy.
- [Řešení potíží](tshoot-connect-pass-through-authentication.md): Naučte se řešit běžné problémy s funkcí předávacího ověřování.
- [Hloubkové podrobně zabezpečení](how-to-connect-pta-security-deep-dive.md): získání technických informací o funkci předávacího ověřování.
- [Bezproblémové jednotné přihlašování Azure AD](how-to-connect-sso.md): Přečtěte si další informace o této doplňkové funkci.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): použijte Fórum Azure Active Directory k započetí nových požadavků na funkce.
