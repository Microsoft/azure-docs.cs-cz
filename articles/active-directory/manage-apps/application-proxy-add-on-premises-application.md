---
title: Kurz – přidání místní aplikace – proxy aplikace ve službě Azure AD
description: Azure Active Directory (Azure AD) má službu Proxy aplikace, která umožňuje uživatelům přístup k místním aplikacím po přihlášení pomocí svého účtu Azure AD. Tento kurz ukazuje, jak připravit prostředí pro použití s proxy aplikací. Potom použije portál Azure k přidání místní aplikace do vašeho klienta Azure AD.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73aa01ea08c8bab1395516c31bb46dbfd88045db
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79481411"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Kurz: Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace ve službě Azure Active Directory

Azure Active Directory (Azure AD) má službu Proxy aplikace, která umožňuje uživatelům přístup k místním aplikacím po přihlášení pomocí svého účtu Azure AD. Tento kurz připraví vaše prostředí pro použití s proxy aplikací. Jakmile bude vaše prostředí připravené, použijete portál Azure k přidání místní aplikace do vašeho klienta Azure AD.

V tomto kurzu:

> [!div class="checklist"]
> * Otevírá porty pro odchozí provoz a umožňuje přístup ke konkrétním adresám URL.
> * Nainstaluje konektor na server windows a zaregistruje jej pomocí proxy aplikace.
> * Ověří správně nainstalovaný a registrovaný konektor
> * Přidá místní aplikaci do vašeho klienta Azure AD.
> * Ověří testovací uživatel můžete přihlásit k aplikaci pomocí účtu Azure AD

## <a name="before-you-begin"></a>Než začnete

Chcete-li přidat místní aplikaci do služby Azure AD, potřebujete:

* [Předplatné Microsoft Azure AD premium](https://azure.microsoft.com/pricing/details/active-directory)
* Účet správce aplikace
* Identity uživatelů musí být synchronizovány z místního adresáře nebo vytvořeny přímo v rámci klienta Azure AD. Synchronizace identit umožňuje Azure AD předem ověřit uživatele před udělením přístupu k aplikacím publikované proxy aplikace a mít potřebné informace o identifikátoru uživatele k provedení jednotného přihlašování (SSO).

### <a name="windows-server"></a>Server Windows

Chcete-li používat proxy aplikace, potřebujete server se systémem Windows Server 2012 R2 nebo novějším. Na server nainstalujete konektor Proxy aplikace. Tento konektorový server se musí připojit ke službám proxy aplikací v Azure a k místním aplikacím, které chcete publikovat.

Pro vysokou dostupnost ve vašem produkčním prostředí doporučujeme mít více než jeden server Windows. Pro účely tohoto kurzu stačí jeden server windows.

> [!IMPORTANT]
> Pokud instalujete konektor v systému Windows Server 2019, existuje omezení HTTP2. Řešení pro použití konektoru v této verzi je přidání následujícího klíče registru a restartování serveru. Všimněte si, že se jedná o klíč pro celý registr počítače. 
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp\EnableDefaultHttp2 (DWORD) Value: 0 
    ```

#### <a name="recommendations-for-the-connector-server"></a>Doporučení pro konektorový server

1. Fyzicky vyhledejte konektorový server v blízkosti aplikačních serverů pro optimalizaci výkonu mezi konektorem a aplikací. Další informace naleznete v [tématu Aspekty topologie sítě](application-proxy-network-topology.md).
1. Server konektorů a servery webových aplikací by měly patřit do stejné domény služby Active Directory nebo do důvěryhodných domén služby Active Directory. Mít servery ve stejné doméně nebo důvěřující domény je požadavek pro použití jednotného přihlašování (SSO) s integrovaným ověřováním systému Windows (IWA) a Kerberos Constrained Delegování (KCD). Pokud jsou servery konektorů a webové aplikační servery v různých doménách služby Active Directory, je třeba pro jednotné přihlašování použít delegování založené na prostředcích. Další informace naleznete [v tématu KCD pro jednotné přihlášení pomocí proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md).

> [!WARNING]
> Pokud jste nasadili proxy server ochrany heslem Azure AD, neinstalujte proxy aplikace Azure AD a proxy server ochrany heslem Azure AD společně do stejného počítače. Proxy aplikace Azure AD a proxy server ochrany heslem Azure AD nainstalují různé verze služby Azure AD Connect Agent Updater. Tyto různé verze jsou nekompatibilní při instalaci společně na stejném počítači.

#### <a name="tls-requirements"></a>Požadavky na TLS

Před instalací konektoru proxy aplikace musí mít server Konektor systému Windows povolenou technologii TLS 1.2.

Povolení TLS 1.2:

1. Nastavte následující klíče registru:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Restartujte server.

> [!IMPORTANT]
> Chcete-li našim zákazníkům poskytovat nejlepší šifrování ve své třídě, služba Proxy aplikací omezuje přístup pouze na protokoly TLS 1.2. Tyto změny byly postupně zaváděny a účinné od 31. srpna 2019. Ujistěte se, že všechny kombinace klient-server a prohlížeč-server jsou aktualizovány tak, aby používaly tls 1.2 pro zachování připojení ke službě Proxy aplikace. Patří mezi ně klienti, které uživatelé používají pro přístup k aplikacím publikovaným prostřednictvím proxy aplikace. Užitečné odkazy a prostředky najdete v článku Příprava na [TLS 1.2 v Office 365.](https://support.microsoft.com/help/4057306/preparing-for-tls-1-2-in-office-365)

## <a name="prepare-your-on-premises-environment"></a>Příprava místního prostředí

Začněte povolením komunikace s datovými centry Azure a připravte si prostředí pro proxy aplikací Azure AD. Pokud je v cestě brána firewall, ujistěte se, že je otevřená. Otevřená brána firewall umožňuje konektoru provádět požadavky PROTOKOLU HTTPS (TCP) na proxy aplikace.

### <a name="open-ports"></a>Otevřené porty

Otevřete následující porty pro **odchozí** provoz.

   | Číslo portu | Jak se používá |
   | --- | --- |
   | 80 | Stahování seznamů odvolaných certifikátů (CRL) při ověřování certifikátu TLS/SSL |
   | 443 | Veškerá odchozí komunikace se službou Proxy aplikace |

Pokud brána firewall vynucuje přenosy podle původních uživatelů, otevřete také porty 80 a 443 pro přenosy ze služeb systému Windows, které běží jako síťová služba.

### <a name="allow-access-to-urls"></a>Povolit přístup k adresám URL

Povolit přístup k následujícím adresám URL:

| zprostředkovatele identity | Jak se používá |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Komunikace mezi konektorem a cloudovou službou Proxy aplikace |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Konektor používá tyto adresy URL k ověření certifikátů. |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>\*.microsoftonline.com<br>\*.microsoftonline-p.com<br>\*.msauth.net<br>\*.msauthimages.net<br>\*.msecnd.net<br>\*.msftauth.net<br>\*.msftauthimages.net<br>\*.phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctdl.windowsupdate.com:80 | Konektor používá tyto adresy URL během procesu registrace. |

Připojení k \*rozhraní .msappproxy.net \*a .servicebus.windows.net můžete povolit, pokud brána firewall nebo proxy server umožňují konfigurovat seznamy povolených služeb DNS. Pokud ne, musíte povolit přístup k [rozsahům IP adres Azure a značkám služeb – Veřejný cloud](https://www.microsoft.com/download/details.aspx?id=56519). Rozsahy IP adres jsou aktualizovány každý týden.

## <a name="install-and-register-a-connector"></a>Instalace a registrace konektoru

Chcete-li použít proxy aplikace, nainstalujte konektor na každý server windows, který používáte, se službou Proxy aplikace. Konektor je agent, který spravuje odchozí připojení z místních aplikačních serverů na proxy aplikace ve službě Azure AD. Konektor můžete nainstalovat na servery, které mají také další agenty ověřování nainstalované, jako je Azure AD Connect.

Instalace konektoru:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) jako správce aplikace adresáře, který používá proxy aplikace. Pokud je například doména klienta contoso.com, měl by být admin@contoso.com správce nebo jakýkoli jiný alias správce v této doméně.
1. V pravém horním rohu vyberte své uživatelské jméno. Ověřte, zda jste přihlášeni k adresáři, který používá proxy aplikace. Pokud potřebujete změnit adresáře, vyberte **Přepnout adresář** a zvolte adresář, který používá proxy aplikace.
1. V levém navigačním panelu vyberte **Azure Active Directory**.
1. V části **Manage**vyberte **Proxy aplikace**.
1. Vyberte **službu Stáhnout konektor .**

    ![Stáhněte si konektor služby vidět podmínky služby](./media/application-proxy-add-on-premises-application/application-proxy-download-connector-service.png)

1. Přečtěte si smluvní podmínky. Až budete připraveni, vyberte **Přijmout podmínky & stáhnout**.
1. V dolní části okna vyberte **Spustit,** chcete-li nainstalovat konektor. Otevře se průvodce instalací.
1. Podle pokynů průvodce nainstalujte službu. Až budete vyzváni k registraci konektoru s proxy aplikací pro vašeho klienta Azure AD, zadejte pověření správce aplikace.
    - Pokud je v aplikaci Internet Explorer (IE) **nastavena konfigurace rozšířeného zabezpečení aplikace Internet** Explorer **na zapnuto**, nemusí se zobrazit obrazovka registrace. Chcete-li získat přístup, postupujte podle pokynů v chybové zprávě. Zkontrolujte, zda je **rozšířená konfigurace zabezpečení aplikace Internet Explorer** nastavena na **vypnuto**.

### <a name="general-remarks"></a>Obecné poznámky

Pokud jste dříve nainstalovali konektor, přeinstalujte a získejte nejnovější verzi. Informace o dříve vydaných verzích a o jejich změnách naleznete [v tématu Proxy aplikace: Historie verzí](application-proxy-release-version-history.md).

Pokud se rozhodnete mít více než jeden server Windows pro místní aplikace, budete muset nainstalovat a zaregistrovat konektor na každém serveru. Konektory můžete uspořádat do skupin konektorů. Další informace naleznete v tématu [Skupiny konektorů](application-proxy-connector-groups.md).

Pokud vaše organizace používá proxy servery pro připojení k Internetu, je třeba je nakonfigurovat pro proxy aplikace.  Další informace naleznete v [tématu Práce s existujícími místními proxy servery](application-proxy-configure-connectors-with-proxy-servers.md). 

Informace o konektorech, plánování kapacity a jejich dostupnosti naleznete v [tématu Understand Azure AD Application Proxy connectors](application-proxy-connectors.md).

## <a name="verify-the-connector-installed-and-registered-correctly"></a>Ověření správně nainstalovaného a registrovaného konektoru

Na webu Azure Portal nebo serveru Windows můžete ověřit, že je správně nainstalován nový konektor.

### <a name="verify-the-installation-through-azure-portal"></a>Ověření instalace prostřednictvím portálu Azure

Chcete-li správně ověřit nainstalovaný a registrovaný konektor:

1. Přihlaste se k adresáři klienta na [webu Azure Portal](https://portal.azure.com).
1. V levém navigačním panelu vyberte **Azure Active Directory**a v části **Správa** vyberte **Proxy aplikace.** Na této stránce se zobrazí všechny konektory a skupiny konektorů.
1. Zobrazit konektor k ověření jeho podrobnosti. Konektory by měly být ve výchozím nastavení rozbaleny. Pokud spojnice, kterou chcete zobrazit, není rozbalená, rozbalte spojnici a zobrazte podrobnosti. Aktivní zelený popisek označuje, že se konektor může připojit ke službě. Však i v případě, že popisek je zelená, problém se sítí může stále blokovat konektor u přijímání zpráv.

    ![Konektory proxy aplikací Azure AD](./media/application-proxy-add-on-premises-application/app-proxy-connectors.png)

Další pomoc s instalací konektoru naleznete v [tématu Problém s instalací konektoru proxy aplikace](application-proxy-connector-installation-problem.md).

### <a name="verify-the-installation-through-your-windows-server"></a>Ověření instalace prostřednictvím serveru Windows

Chcete-li správně ověřit nainstalovaný a registrovaný konektor:

1. Sem otevřete Správce služeb systému Windows klepnutím na klíč **systému Windows** a zadáním *souboru services.msc*.
1. Zkontrolujte, zda je stav následujících dvou služeb **spuštěn .**
   - **Microsoft AAD Application Proxy Connector** umožňuje připojení.
   - **Microsoft AAD Application Proxy Connector Updater** je služba automatické aktualizace. Aktualizační služba zkontroluje nové verze konektoru a podle potřeby aktualizuje konektor.

     ![Služby konektoru proxy aplikace – snímek obrazovky](./media/application-proxy-add-on-premises-application/app_proxy_services.png)

1. Pokud stav služeb není **spuštěn**, klepněte pravým tlačítkem myši na výběr jednotlivých služeb a zvolte **Start**.

## <a name="add-an-on-premises-app-to-azure-ad"></a>Přidání místní aplikace do Azure AD

Teď, když jste připravili prostředí a nainstalovali konektor, jste připraveni přidat místní aplikace do Azure AD.  

1. Přihlaste se jako správce na [webu Azure Portal](https://portal.azure.com/).
2. V levém navigačním panelu vyberte **Azure Active Directory**.
3. Vyberte **podnikové aplikace**a potom vyberte **Nová aplikace**.
4. V části **Místní aplikace** vyberte Přidat **místní aplikaci**.
5. V části **Přidat vlastní místní aplikaci** zadejte následující informace o vaší aplikaci:

    | Pole | Popis |
    | :---- | :---------- |
    | **Název** | Název aplikace, která se zobrazí na přístupovém panelu a na webu Azure Portal. |
    | **Interní adresa URL** | Adresa URL pro přístup k aplikaci z vlastní privátní sítě. Můžete zadat konkrétní cestu na beck-endovém serveru, kterou chcete publikovat, zatímco zbytek serveru publikovaný nebude. Tímto způsobem můžete publikovat různé weby na stejném serveru jako různé aplikace a dát každému z nich svůj vlastní název a pravidla přístupu.<br><br>Pokud publikujete cestu, ujistěte se, že zahrnuje všechny nezbytné obrázky, skripty a šablony stylů pro vaši aplikaci. Například pokud je vaše aplikace\/na https: /yourapp/app\/a používá obrázky umístěné na https: /yourapp/media, měli byste publikovat https:\//yourapp/ jako cestu. Tato interní adresa URL nemusí být vstupní stránkou, kterou uživatelé uvidí. Další informace najdete [v tématu Nastavení vlastní domovské stránky pro publikované aplikace](application-proxy-configure-custom-home-page.md). |
    | **Externí adresa URL** | Adresa pro přístup uživatelů k aplikaci mimo vaši síť. Pokud nechcete používat výchozí doménu proxy aplikace, přečtěte si o [vlastních doménách v proxy aplikací Azure AD](application-proxy-configure-custom-domain.md).|
    | **Předběžné ověření** | Jak proxy aplikace ověří uživatele před tím, než jim přístup k vaší aplikaci.<br><br>**Azure Active Directory** – proxy aplikace přesměruje uživatele k přihlášení pomocí služby Azure AD, která ověřuje jejich oprávnění pro adresář a aplikaci. Doporučujeme zachovat tuto možnost jako výchozí, abyste mohli využívat funkce zabezpečení Azure AD, jako je podmíněný přístup a vícefaktorové ověřování. **Služba Azure Active Directory** je vyžadována pro monitorování aplikace pomocí zabezpečení microsoftcloudových aplikací.<br><br>**Průchod** – uživatelé nemusí ověřování proti Azure AD pro přístup k aplikaci. Stále můžete nastavit požadavky na ověřování v back-endu. |
    | **Skupina konektorů** | Konektory zpracovávají vzdálený přístup k vaší aplikaci a skupiny konektorů vám pomohou uspořádat konektory a aplikace podle oblasti, sítě nebo účelu. Pokud ještě nemáte žádné skupiny konektorů, je vaše aplikace přiřazena k **nastavení Výchozí**.<br><br>Pokud vaše aplikace používá WebSockets pro připojení, všechny konektory ve skupině musí být verze 1.5.612.0 nebo novější.|

6. V případě potřeby nakonfigurujte **další nastavení**. U většiny aplikací byste měli tato nastavení ponechat ve výchozím stavu. 

    | Pole | Popis |
    | :---- | :---------- |
    | **Časový rozsah aplikace back-endu** | Tuto hodnotu nastavte na **hodnotu Long** pouze v případě, že se aplikace pomalu ověřuje a připojuje. Ve výchozím nastavení časový limit aplikace back-end uběhne délku 85 sekund. Pokud je nastavena na dlouhou, časový rozsah back-endu se zvýší na 180 sekund. |
    | **Použití souboru cookie pouze pro protokol HTTP** | Nastavte tuto hodnotu na **Ano,** aby soubory cookie proxy aplikace obsahovaly příznak HTTPOnly v hlavičce odpovědi HTTP. Pokud používáte službu Vzdálená plocha, nastavte tuto hodnotu na **hodnotu Ne**.|
    | **Použití zabezpečeného souboru cookie**| Nastavte tuto hodnotu na **Ano** pro přenos souborů cookie přes zabezpečený kanál, jako je například šifrovaný požadavek HTTPS.
    | **Použít trvalý soubor cookie**| Ponechejte tuto hodnotu nastavenou na **hodnotu Ne**. Toto nastavení používejte pouze pro aplikace, které nemohou sdílet soubory cookie mezi procesy. Další informace o nastavení souborů cookie najdete v [tématu Nastavení souborů cookie pro přístup k místním aplikacím ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).
    | **Překlad adres URL v záhlaví** | Ponechte tuto hodnotu jako **Ano,** pokud vaše aplikace v žádosti o ověření nevyžadovala původní hlavičku hostitele. |
    | **Přeložit adresy URL v těle aplikace** | Ponechte tuto hodnotu jako **Ne,** pokud nemáte pevně zakódované odkazy HTML na jiné místní aplikace a nepoužíváte vlastní domény. Další informace naleznete v tématu [Link translation with Application Proxy](application-proxy-configure-hard-coded-link-translation.md).<br><br>Pokud chcete tuto aplikaci monitorovat pomocí služby Microsoft Cloud App Security (MCAS), nastavte tuto hodnotu na **Hodnotu Ano.** Další informace najdete [v tématu Konfigurace monitorování přístupu k aplikacím](application-proxy-integrate-with-microsoft-cloud-application-security.md)v reálném čase pomocí Microsoft Cloud App Security a Azure Active Directory . |

7. Vyberte **Přidat**.

## <a name="test-the-application"></a>Testování aplikace

Jste připraveni otestovat, že aplikace je přidána správně. V následujících krocích přidáte do aplikace uživatelský účet a pokusíte se přihlásit.

### <a name="add-a-user-for-testing"></a>Přidání uživatele k testování

Před přidáním uživatele do aplikace ověřte, zda uživatelský účet již má oprávnění k přístupu k aplikaci z podnikové sítě.

Přidání testovacího uživatele:

1. Vyberte **podnikové aplikace**a pak vyberte aplikaci, kterou chcete testovat.
2. Vyberte **Možnost Začínáme**a pak vyberte **Přiřadit uživatele k testování**.
3. V části **Uživatelé a skupiny**vyberte **Přidat uživatele**.
4. V části **Přidat přiřazení**vyberte **Možnost Uživatelé a skupiny**. Zobrazí se oddíl **Uživatel a skupiny.**
5. Vyberte účet, který chcete přidat.
6. Zvolte **Vybrat**a pak vyberte **Přiřadit**.

### <a name="test-the-sign-on"></a>Otestujte přihlášení

Chcete-li otestovat přihlášení k aplikaci:

1. V aplikaci, kterou chcete testovat, vyberte **proxy aplikace**.
2. V horní části stránky vyberte **Testovat aplikaci** spustit test v aplikaci a zkontrolujte všechny problémy s konfigurací.
3. Ujistěte se, že nejprve spustit aplikaci otestovat přihlášení do aplikace, pak stáhnout diagnostickou sestavu zkontrolovat pokyny k řešení pro všechny zjištěné problémy.

Řešení potíží naleznete v [tématu Poradce při potížích s proxy aplikací a chybových zprávách](application-proxy-troubleshoot.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste připravili místní prostředí pro práci s proxy aplikací a pak jste nainstalovali a zaregistrovali konektor Proxy aplikace. Dále jste přidali aplikaci do vašeho klienta Azure AD. Ověřili jste, že uživatel můžete přihlásit k aplikaci pomocí účtu Azure AD.

Provedli jste tyto akce:
> [!div class="checklist"]
> * Otevřené porty pro odchozí provoz a povolený přístup ke konkrétním adresám URL
> * Nainstaluje konektor na server se systémem Windows a zaregistruje jej pomocí proxy aplikace
> * Ověřeno správně nainstalované a registrované konektor
> * Do klienta Azure AD byla přidána místní aplikace.
> * Ověřený testovací uživatel se může přihlásit k aplikaci pomocí účtu Azure AD

Jste připraveni nakonfigurovat aplikaci pro jednotné přihlašování. Pomocí následujícího odkazu zvolte metodu jednotného přihlášení a vyhledejte kurzy jednotného přihlášení.

> [!div class="nextstepaction"]
> [Konfigurace jednotného přihlašování](what-is-single-sign-on.md#choosing-a-single-sign-on-method)
