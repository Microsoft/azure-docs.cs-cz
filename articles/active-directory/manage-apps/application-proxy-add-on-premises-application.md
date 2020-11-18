---
title: Kurz – Přidání místní aplikace proxy aplikací do služby Azure AD
description: Azure Active Directory (Azure AD) obsahuje službu proxy aplikací, která uživatelům umožňuje přístup k místním aplikacím přihlášením pomocí svého účtu Azure AD. V tomto kurzu se dozvíte, jak připravit prostředí pro použití s proxy aplikací. Pak používá Azure Portal k přidání místní aplikace do tenanta služby Azure AD.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 41955475f32fe674bcb3ef2d1b6e59c71a008b6b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656441"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Kurz: Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace v Azure Active Directory

Azure Active Directory (Azure AD) obsahuje službu proxy aplikací, která uživatelům umožňuje přístup k místním aplikacím přihlášením pomocí svého účtu Azure AD. Tento kurz připraví vaše prostředí pro použití s proxy aplikací. Až bude vaše prostředí připravené, pomocí Azure Portal přidejte místní aplikaci do svého tenanta Azure AD.

V tomto kurzu:

> [!div class="checklist"]
> * Otevře porty pro odchozí provoz a povolí přístup ke konkrétním adresám URL.
> * Nainstaluje konektor na Windows Server a zaregistruje ho u proxy aplikací.
> * Ověří, jestli je nainstalovaný a správně zaregistrovaný konektor.
> * Přidá místní aplikaci do tenanta služby Azure AD.
> * Ověří, jestli se testovací uživatel může přihlásit k aplikaci pomocí účtu Azure AD.

## <a name="prerequisites"></a>Požadavky

K přidání místní aplikace do služby Azure AD potřebujete:

* [Předplatné Microsoft Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory)
* Účet správce aplikace
* Identity uživatelů se musí synchronizovat z místního adresáře nebo vytvářet přímo v klientech Azure AD. Synchronizace identity umožňuje službě Azure AD předem ověřit uživatele předtím, než jim udělí přístup k aplikacím publikovaným v proxy aplikaci, a bude mít potřebné informace o uživatelském identifikátoru k provedení jednotného přihlašování (SSO).

### <a name="windows-server"></a>Windows Server

Pokud chcete použít proxy aplikace, potřebujete Windows Server se systémem Windows Server 2012 R2 nebo novějším. Nainstalujete konektor proxy aplikace na server. Tento server konektoru se musí připojit k aplikačním proxy službám v Azure a místním aplikacím, které plánujete publikovat.

Pro zajištění vysoké dostupnosti v produkčním prostředí doporučujeme mít více než jeden Windows Server. Pro tento kurz stačí jeden Windows Server.

> [!IMPORTANT]
> Pokud instalujete konektor v systému Windows Server 2019, je nutné zakázat podporu protokolu HTTP2 v součásti WinHttp pro vynucené delegování protokolu Kerberos do správné práce. V dřívějších verzích podporovaných operačních systémů je to ve výchozím nastavení zakázané. Přidáním následujícího klíče registru a restartováním serveru ho zakážete ve Windows serveru 2019. Všimněte si, že se jedná o klíč registru na úrovni počítače.
>
> ```
> Windows Registry Editor Version 5.00
> 
> [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp] "EnableDefaultHttp2"=dword:00000000
> ```
>

#### <a name="recommendations-for-the-connector-server"></a>Doporučení pro server konektoru

1. Fyzicky Najděte Server konektoru blízko aplikačním serverům, abyste mohli optimalizovat výkon mezi konektorem a aplikací. Další informace najdete v tématu [požadavky na topologii sítě](application-proxy-network-topology.md).
1. Server konektoru a servery webových aplikací by měly patřit do stejné domény služby Active Directory nebo do rozsahu důvěřujících domén. Použití serverů ve stejné doméně nebo důvěřujících doménách je požadavek na použití jednotného přihlašování (SSO) s integrovaným ověřováním systému Windows (IWA) a vynuceným delegováním protokolu Kerberos (KCD). Pokud jsou servery konektoru a webové aplikace v různých doménách služby Active Directory, musíte pro jednotné přihlašování použít delegování založené na prostředcích. Další informace najdete v tématu [KCD pro jednotné přihlašování pomocí proxy aplikací](application-proxy-configure-single-sign-on-with-kcd.md).

> [!WARNING]
> Pokud jste nasadili proxy ochranu heslem služby Azure AD, neinstalujte do stejného počítače službu Azure Proxy aplikací služby AD ani proxy ochrany heslem služby Azure AD. Služba Azure Proxy aplikací služby AD a proxy ochrany heslem Azure AD instalují různé verze služby Azure AD Connect agenta pro aktualizace. Tyto různé verze jsou při instalaci společně na stejném počítači nekompatibilní.

#### <a name="tls-requirements"></a>Požadavky TLS

Než nainstalujete konektor proxy aplikace, musí mít server Windows Connector povolený protokol TLS 1,2.

Povolení TLS 1,2:

1. Nastavte následující klíče registru:
    
   ```
   Windows Registry Editor Version 5.00

   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
   "DisabledByDefault"=dword:00000000
   "Enabled"=dword:00000001
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server]
   "DisabledByDefault"=dword:00000000
   "Enabled"=dword:00000001
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
   "SchUseStrongCrypto"=dword:00000001
   ```

1. Restartujte server.

## <a name="prepare-your-on-premises-environment"></a>Příprava místního prostředí

Začněte tím, že povolíte komunikaci s datovými centry Azure a připravíte své prostředí pro Azure Proxy aplikací služby AD. Pokud je v cestě brána firewall, ujistěte se, že je otevřená. Otevřená brána firewall umožňuje konektoru předávat žádosti pomocí protokolu HTTPS (TCP) na proxy aplikace.

> [!IMPORTANT]
> Pokud instalujete konektor pro Azure Government Cloud, postupujte podle [požadavků](../hybrid/reference-connect-government-cloud.md#allow-access-to-urls) a [pokynů k instalaci](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud). K tomu je potřeba povolit přístup k jiné sadě adres URL a další parametr ke spuštění instalace.

### <a name="open-ports"></a>Otevřené porty

Otevřete následující porty pro **odchozí** provoz.

   | Číslo portu | Jak se používá |
   | --- | --- |
   | 80 | Stahování seznamů odvolaných certifikátů (CRL) při ověřování certifikátu TLS/SSL |
   | 443 | Veškerá odchozí komunikace se službou proxy aplikace |

Pokud brána firewall vynutila provoz na základě pocházejících uživatelů, otevřete také porty 80 a 443 pro provoz ze služeb systému Windows, které běží jako síťová služba.

### <a name="allow-access-to-urls"></a>Povolení přístupu k adresám URL

Povolte přístup k následujícím adresám URL:

| URL | Port | Jak se používá |
| --- | --- | --- |
| &ast;. msappproxy.net<br>&ast;. servicebus.windows.net | 443/HTTPS | Komunikace mezi konektorem a cloudovou službou proxy aplikací |
| crl3.digicert.com<br>crl4.digicert.com<br>ocsp.digicert.com<br>crl.microsoft.com<br>oneocsp.microsoft.com<br>ocsp.msocsp.com<br> | 80/HTTP |Konektor používá tyto adresy URL k ověření certifikátů. |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>&ast;.microsoftonline.com<br>&ast;. microsoftonline-p.com<br>&ast;. msauth.net<br>&ast;. msauthimages.net<br>&ast;. msecnd.net<br>&ast;. msftauth.net<br>&ast;. msftauthimages.net<br>&ast;. phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctldl.windowsupdate.com | 443/HTTPS |Konektor tyto adresy URL používá během procesu registrace. |
| ctldl.windowsupdate.com | 80/HTTP |Konektor používá tuto adresu URL během procesu registrace. |

&ast; &ast; V případě, že vaše brána firewall nebo proxy server umožní nakonfigurovat seznamy povolených serverů DNS, můžete pro připojení k. msappproxy.NET,. ServiceBus.Windows.NET a dalším adresám URL. V takovém případě je potřeba zpřístupnit přístup k [rozsahům IP adres Azure a veřejným cloudům](https://www.microsoft.com/download/details.aspx?id=56519). Rozsahy IP adres se aktualizují každý týden.

## <a name="install-and-register-a-connector"></a>Instalace a registrace konektoru

Chcete-li použít proxy aplikace, nainstalujte konektor na každý server s Windows, který používáte se službou proxy aplikací. Konektor je agent, který spravuje odchozí připojení z místních aplikačních serverů k proxy aplikací v Azure AD. Konektor můžete nainstalovat na servery, na kterých jsou taky nainstalované další ověřovací agenty, jako je Azure AD Connect.


Postup instalace konektoru:

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) jako správce aplikace adresáře, který používá proxy aplikace. Pokud je například doména tenanta contoso.com, správce by měl být admin@contoso.com v této doméně nebo jiný alias správce.
1. V pravém horním rohu vyberte své uživatelské jméno. Ověřte, že jste přihlášeni k adresáři, který používá proxy aplikace. Pokud potřebujete změnit adresáře, vyberte **Přepnout adresář** a zvolte adresář, který používá proxy aplikace.
1. V levém navigačním panelu vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **proxy aplikace**.
1. Vyberte **stáhnout službu konektoru**.

    ![Stažení služby konektoru, aby se zobrazila podmínka služby](./media/application-proxy-add-on-premises-application/application-proxy-download-connector-service.png)

1. Přečtěte si podmínek služby. Až budete připraveni, vyberte **přijmout podmínky & stáhnout**.
1. V dolní části okna vyberte **Spustit** pro instalaci konektoru. Otevře se Průvodce instalací aplikace.
1. Podle pokynů v průvodci nainstalujte službu. Až budete vyzváni k registraci konektoru u proxy aplikace pro vašeho tenanta Azure AD, zadejte přihlašovací údaje správce aplikace.
    - V případě aplikace Internet Explorer (IE), pokud je **Konfigurace rozšířeného zabezpečení aplikace Internet** Explorer nastavena **na zapnuto**, se nemusí zobrazit registrační obrazovka. Chcete-li získat přístup, postupujte podle pokynů v chybové zprávě. Ujistěte se, že je **Konfigurace rozšířeného zabezpečení aplikace Internet Explorer** nastavena na **vypnuto**.

### <a name="general-remarks"></a>Obecné poznámky

Pokud jste dříve nainstalovali konektor, nainstalujte ho znovu, abyste získali nejnovější verzi. Informace o dříve vydaných verzích a o tom, jaké změny zahrnují, najdete v tématu [proxy aplikace: historie](application-proxy-release-version-history.md)verzí.

Pokud se rozhodnete, že budete mít více než jeden Windows Server pro místní aplikace, budete muset konektor nainstalovat a zaregistrovat na každém serveru. Konektory můžete uspořádat do skupin konektorů. Další informace najdete v tématu [skupiny konektorů](application-proxy-connector-groups.md).

Pokud vaše organizace používá pro připojení k Internetu proxy servery, musíte je nakonfigurovat pro proxy aplikací.  Další informace najdete v tématu [práce se stávajícími místními proxy servery](application-proxy-configure-connectors-with-proxy-servers.md). 

Informace o konektorech, plánování kapacity a o tom, jak jsou stále aktuální, najdete v tématu [vysvětlení konektorů služby Azure proxy aplikací služby AD](application-proxy-connectors.md).

## <a name="verify-the-connector-installed-and-registered-correctly"></a>Ověřte, jestli je konektor nainstalovaný a správně zaregistrovaný.

Pomocí Azure Portal nebo Windows serveru můžete potvrdit, že se nový konektor správně nainstaloval.

### <a name="verify-the-installation-through-azure-portal"></a>Ověření instalace prostřednictvím Azure Portal

Chcete-li ověřit, zda byl konektor nainstalován a správně zaregistrován:

1. Přihlaste se ke svému adresáři tenanta v [Azure Portal](https://portal.azure.com).
1. V levém navigačním panelu vyberte **Azure Active Directory** a potom v části **Spravovat** vyberte **proxy aplikace** . Na této stránce se zobrazí všechny vaše konektory a skupiny konektorů.
1. Zobrazením konektoru ověříte jeho podrobnosti. Konektory by měly být ve výchozím nastavení rozbalené. Pokud se konektor, který chcete zobrazit, nerozšíří, rozbalte konektor a zobrazte podrobnosti. Aktivní zelený popisek označuje, že se váš konektor může připojit ke službě. I když je však popisek zelený, může problém se sítí stále blokovat příjem zpráv.

    ![Konektory Azure Proxy aplikací služby AD](./media/application-proxy-add-on-premises-application/app-proxy-connectors.png)

Další nápovědu k instalaci konektoru najdete v tématu [problém instalace konektoru proxy aplikací](application-proxy-connector-installation-problem.md).

### <a name="verify-the-installation-through-your-windows-server"></a>Ověření instalace prostřednictvím Windows serveru

Chcete-li ověřit, zda byl konektor nainstalován a správně zaregistrován:

1. Spusťte Správce služeb systému Windows kliknutím na klíč **systému Windows** a zadáním příkazu *Services. msc*.
1. Zkontrolujte, jestli je **spuštěný** stav těchto dvou služeb.
   - **Microsoft AAD Application proxy Connector** umožňuje připojení.
   - **Aktualizátor konektorů proxy aplikace služby Microsoft AAD** je služba automatizované aktualizace. Nástroj aktualizace kontroluje nové verze konektoru a podle potřeby aktualizuje konektor.

     ![Služby konektoru proxy aplikace – snímek obrazovky](./media/application-proxy-add-on-premises-application/app_proxy_services.png)

1. Pokud stav služeb není **spuštěný**, klikněte pravým tlačítkem myši a vyberte každou službu a zvolte **Spustit**.

## <a name="add-an-on-premises-app-to-azure-ad"></a>Přidání místní aplikace do Azure AD

Teď, když jste připravili prostředí a nainstalovali konektor, jste připraveni přidat místní aplikace do Azure AD.  

1. Přihlaste se jako správce v [Azure Portal](https://portal.azure.com/).
2. V levém navigačním panelu vyberte **Azure Active Directory**.
3. Vyberte **podnikové aplikace** a pak vyberte **Nová aplikace**.
4. V části místní **aplikace** vyberte **Přidat místní aplikaci**.
5. V části **Přidat vlastní místní aplikaci** zadejte následující informace o vaší aplikaci:

    | Pole | Popis |
    | :---- | :---------- |
    | **Název** | Název aplikace, která se zobrazí v okně moje aplikace a v Azure Portal. |
    | **Interní adresa URL** | Adresa URL pro přístup k aplikaci zevnitř vaší privátní sítě. Můžete zadat konkrétní cestu na beck-endovém serveru, kterou chcete publikovat, zatímco zbytek serveru publikovaný nebude. Tímto způsobem můžete publikovat různé weby na stejném serveru jako různé aplikace a dát každému z nich vlastní název a pravidla přístupu.<br><br>Pokud publikujete cestu, ujistěte se, že zahrnuje všechny nezbytné obrázky, skripty a šablony stylů pro vaši aplikaci. Pokud je vaše aplikace například na https: \/ /yourapp/App a používá Image umístěné v protokolu https: \/ /yourapp/Media, měli byste jako cestu publikovat https: \/ /yourapp/. Tato interní adresa URL nemusí být cílovou stránkou, kterou uživatelé uvidí. Další informace najdete v tématu [nastavení vlastní domovské stránky pro publikované aplikace](application-proxy-configure-custom-home-page.md). |
    | **Externí adresa URL** | Adresa pro uživatele, kteří budou mít přístup k aplikaci mimo vaši síť. Pokud nechcete používat výchozí doménu proxy aplikace, přečtěte si o [vlastních doménách v Azure proxy aplikací služby AD](application-proxy-configure-custom-domain.md).|
    | **Předběžné ověření** | Jak proxy aplikace ověřuje uživatele před tím, než jim poskytne přístup k vaší aplikaci.<br><br>**Azure Active Directory** – proxy aplikací přesměruje uživatele na přihlášení pomocí služby Azure AD, která ověřuje jejich oprávnění pro adresář a aplikaci. Tuto možnost doporučujeme ponechat jako výchozí, abyste mohli využívat funkce zabezpečení Azure AD, jako je podmíněný přístup a Multi-Factor Authentication. **Azure Active Directory** se vyžaduje pro monitorování aplikace s Microsoft Cloud zabezpečení aplikací.<br><br>**Passthrough** – uživatelé nemusejí pro přístup k aplikaci ověřovat přes Azure AD. Požadavky na ověřování můžete nastavit i pro back-end. |
    | **Skupina konektorů** | Konektory zpracovávají vzdálený přístup k vaší aplikaci a skupiny konektorů vám pomůžou organizovat konektory a aplikace podle oblastí, sítě nebo účelu. Pokud ještě nemáte vytvořené žádné skupiny konektorů, vaše aplikace se přiřadí **výchozímu**.<br><br>Pokud vaše aplikace používá k připojení objekty WebSockets, musí být všechny konektory ve skupině verze 1.5.612.0 nebo novější.|

6. V případě potřeby nakonfigurujte **Další nastavení**. U většiny aplikací byste měli tato nastavení zachovat ve svých výchozích stavech. 

    | Pole | Popis |
    | :---- | :---------- |
    | **Časový limit aplikace back-endu** | Nastavte tuto hodnotu na **Long** , jenom pokud se vaše aplikace pomalu ověřuje a připojuje. Ve výchozím nastavení má časový limit aplikace back-end délku 85 sekund. Když se nastaví na Long, časový limit pro back-end se zvýší na 180 sekund. |
    | **Použít soubor cookie HTTP-Only** | Nastavte tuto hodnotu na **Ano** , pokud chcete, aby soubory cookie proxy aplikací zahrnovaly příznak HttpOnly v hlavičce HTTP Response. Pokud používáte službu Vzdálená plocha, nastavte tuto hodnotu na **ne**.|
    | **Použít zabezpečený soubor cookie**| Nastavte tuto hodnotu na **Ano** , pokud chcete přenášet soubory cookie přes zabezpečený kanál, jako je třeba ŠIFROVANÝ požadavek https.
    | **Použít trvalý soubor cookie**| Nechte tuto hodnotu nastavenou na **ne**. Toto nastavení použijte jenom pro aplikace, které nemůžou sdílet soubory cookie mezi procesy. Další informace o nastavení souborů cookie najdete v tématu [nastavení souborů cookie pro přístup k místním aplikacím v Azure Active Directory](./application-proxy-configure-cookie-settings.md).
    | **Přeložit adresy URL v hlavičkách** | Tuto hodnotu nechte jako **Ano** , pokud vaše aplikace nevyžadovala v žádosti o ověření hlavičku původního hostitele. |
    | **Přeložit adresy URL v těle aplikace** | Tuto hodnotu nechte jako **ne** , pokud jste nepevně zakódovanéi odkazy HTML na jiné místní aplikace a nepoužíváte vlastní domény. Další informace najdete v tématu [Překlad propojení pomocí proxy aplikace](application-proxy-configure-hard-coded-link-translation.md).<br><br>Tuto hodnotu nastavte na **Ano** , pokud plánujete monitorovat tuto aplikaci pomocí Microsoft Cloud App Security (MCAS). Další informace najdete v tématu [Konfigurace monitorování přístupu aplikace v reálném čase pomocí Microsoft Cloud App Security a Azure Active Directory](application-proxy-integrate-with-microsoft-cloud-application-security.md). |

7. Vyberte možnost **Přidat**.

## <a name="test-the-application"></a>Testování aplikace

Jste připraveni k otestování aplikace, aby byla správně přidána. V následujících krocích přidáte uživatelský účet do aplikace a zkusíte se přihlásit.

### <a name="add-a-user-for-testing"></a>Přidat uživatele pro testování

Před přidáním uživatele do aplikace ověřte, zda uživatelský účet již má oprávnění k přístupu k aplikaci v rámci podnikové sítě.

Chcete-li přidat testovacího uživatele:

1. Vyberte **podnikové aplikace** a pak vyberte aplikaci, kterou chcete testovat.
2. Vyberte **Začínáme** a pak vyberte **přiřadit uživatele pro testování**.
3. V části **Uživatelé a skupiny** vyberte **Přidat uživatele**.
4. V části **Přidat přiřazení** vyberte **Uživatelé a skupiny**. Zobrazí se část **uživatel a skupiny** .
5. Vyberte účet, který chcete přidat.
6. Zvolte **Vybrat** a pak vybrat **přiřadit**.

### <a name="test-the-sign-on"></a>Otestování přihlášení

Otestování přihlášení k aplikaci:

1. Z aplikace, kterou chcete testovat, vyberte **proxy aplikace**.
2. V horní části stránky vyberte **test aplikace** , aby se spustila test aplikace a kontrolovaly se případné problémy s konfigurací.
3. Nezapomeňte nejdřív spustit aplikaci, abyste otestovali přihlášení k aplikaci, a pak si stáhněte diagnostickou zprávu, kde najdete pokyny k vyřešení všech zjištěných problémů.

Informace o řešení potíží najdete v tématu [řešení potíží se službou Application proxy a chybovými zprávami](application-proxy-troubleshoot.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte prostředky, které jste vytvořili v tomto kurzu.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste připravili své místní prostředí pro práci s proxy aplikací a potom jste nainstalovali a zaregistrovali konektor proxy aplikací. Pak jste do svého tenanta služby Azure AD přidali aplikaci. Ověřili jste, že se uživatel může přihlásit k aplikaci pomocí účtu Azure AD.

Provedli jste tyto akce:
> [!div class="checklist"]
> * Otevřené porty pro odchozí provoz a povolený přístup ke konkrétním adresám URL
> * Nainstalovali jste konektor na Windows Server a zaregistrovali ho u proxy aplikací.
> * Ověření, že je konektor nainstalovaný a správně zaregistrován
> * Do tenanta Azure AD se přidala místní aplikace.
> * Ověřeno, že testovací uživatel se může přihlásit k aplikaci pomocí účtu Azure AD

Jste připraveni nakonfigurovat aplikaci pro jednotné přihlašování. Pomocí následujícího odkazu můžete vybrat metodu jednotného přihlašování a vyhledat kurzy jednotného přihlašování.

> [!div class="nextstepaction"]
> [Konfigurace jednotného přihlašování](sso-options.md#choosing-a-single-sign-on-method)