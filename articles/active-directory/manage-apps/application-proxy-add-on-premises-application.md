---
title: Přidat místní aplikace – Proxy aplikace ve službě Azure Active Directory | Dokumentace Microsoftu
description: Azure Active Directory (Azure AD) obsahuje Proxy aplikace služby, který umožňuje uživatelům přistupovat k místním aplikacím, když se přihlásíte pomocí svého účtu Azure AD. V tomto kurzu se dozvíte, jak připravit prostředí pro použití se službou Proxy aplikací. Potom použije na webu Azure portal přidat místní aplikace do svého tenanta Azure AD.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecff60d1a1f808c4021476d136fe014175451672
ms.sourcegitcommit: 0ebc62257be0ab52f524235f8d8ef3353fdaf89e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723963"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Kurz: Přidat místní aplikace pro vzdálený přístup prostřednictvím Proxy aplikací v Azure Active Directory

Azure Active Directory (Azure AD) obsahuje Proxy aplikace služby, který umožňuje uživatelům přistupovat k místním aplikacím, když se přihlásíte pomocí svého účtu Azure AD. V tomto kurzu připraví vaše prostředí pro použití s Proxy aplikací. Jakmile je prostředí připravené, použijete k přidání místní aplikace do svého tenanta Azure AD na webu Azure portal.

V tomto kurzu:

> [!div class="checklist"]
> * Otevře porty pro odchozí provoz a umožňuje přístup ke konkrétní adresy URL
> * Konektor se nainstaluje na Windows serveru a zaregistruje ho s Proxy aplikací
> * Ověří konektor nainstalován a správně zaregistrován
> * Přidá místní aplikace do svého tenanta Azure AD
> * Ověřuje, že testovací uživatel může přihlásit k aplikaci pomocí účtu služby Azure AD

## <a name="before-you-begin"></a>Než začnete

Pokud chcete přidat místní aplikace do služby Azure AD, budete potřebovat:

* A [základní nebo prémiové předplatné služby Microsoft Azure AD](https://azure.microsoft.com/pricing/details/active-directory)
* Účet správce aplikace
* Identity uživatelů musí být synchronizované z místního adresáře nebo přímo v rámci vašich tenantů Azure AD. Umožňuje synchronizaci identit Azure AD pro předběžné ověření uživatelů před udělením přístupu k Proxy aplikace publikované aplikace a mít informace potřebné uživatelské identifikátor provádět jednotné přihlašování (SSO).

### <a name="windows-server"></a>Windows server

Pokud chcete použít Proxy aplikace, je třeba Windows serveru se systémem Windows Server 2012 R2 nebo novější. Na serveru nainstalujete konektor Proxy aplikací. Tento konektor server potřebuje pro připojení ke službám Proxy aplikací v Azure a místních aplikací, které chcete publikovat.

Pro zajištění vysoké dostupnosti v produkčním prostředí doporučujeme mít více než jeden server Windows. Pro účely tohoto kurzu je dostačující jeden Windows server.

#### <a name="recommendations-for-the-connector-server"></a>Doporučení pro server konektoru

1. Fyzicky najděte server konektoru blízko aplikačních serverů za účelem optimalizace výkonu mezi konektoru a aplikací. Další informace najdete v tématu [aspekty topologie sítě](application-proxy-network-topology.md).
1. Server konektoru a webové servery aplikace by měly patřit do stejné domény služby Active Directory nebo span důvěryhodné domény. Mají servery ve stejné doméně nebo důvěřující domény představuje požadavek pro jednotné přihlašování (SSO) pomocí integrovaného ověřování Windows (IWA) a Kerberos omezené delegování (KCD). Pokud jsou server konektoru a serverů webových aplikací v různých doménách služby Active Directory, budete muset použít pro jednotné přihlašování založené na prostředcích delegování. Další informace najdete v tématu [KCD pro jednotné přihlašování s Proxy aplikací](application-proxy-configure-single-sign-on-with-kcd.md).

#### <a name="tls-requirements"></a>Požadavky TLS

Windows server konektoru musí mít TLS 1.2 zapnutý předtím, než nainstalujete konektor Proxy aplikací.

Povolení protokolu TLS 1.2:

1. Nastavte následující klíče registru:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Restartujte server.

> [!IMPORTANT]
> Ve své třídě nejlepší šifrování poskytovat našim zákazníkům, provádíme aktualizací ve službě Proxy aplikací a omezit tak přístup na pouze protokoly TLS 1.2. Podle připravenosti zákazníka, které změny budou postupně zavádět zákazníkům, kteří používají pouze protokoly TLS 1.2 a žádný vliv tato změna se nezobrazí. Protokol TLS 1.0 a 1.1 vyřazení dokončí v srpnu 31. května 2019 a zákazníci získají předem, chcete-li tuto změnu připravit. Přípravy na tuto změnu zkontrolujte že, která jsou všechny klient server a server prohlížeče kombinace aktualizované na používání protokolu TLS 1.2 pro zachování připojení k Proxy aplikace služby. Patří mezi ně klientů, které uživatelé používají pro přístup k aplikacím, které jsou publikované prostřednictvím Proxy aplikací. V tématu Příprava pro [TLS 1.2 v Office 365](https://support.microsoft.com/help/4057306/preparing-for-tls-1-2-in-office-365) pro užitečné odkazy a prostředky.

## <a name="prepare-your-on-premises-environment"></a>Příprava místního prostředí

Začněte tím, že povolení komunikace do datových center Azure přípravy prostředí pro Azure AD Application Proxy. Pokud je v cestě brána firewall, ujistěte se, že je otevřený. Otevřít brána firewall povoluje konektor tak, aby Proxy aplikace posílat požadavky HTTPS (TCP).

### <a name="open-ports"></a>Otevření portů

Otevřít následující porty **odchozí** provoz.

   | Číslo portu | Jak se používá |
   | --- | --- |
   | 80 | Stahuje se odvolání certifikátu seznamy (CRL) při ověřování certifikátu SSL |
   | 443 | Veškerá odchozí komunikace se službou Proxy aplikací |

Pokud vaše brána firewall vynucuje přenos v závislosti na zdroji uživatelů, taky otevřete porty 80 a 443 pro přenosy služby Windows, na kterých běží jako síťové služby.

Bude pravděpodobně starší verzi konektor nainstalován, pokud už používáte Proxy aplikací. V tomto kurzu nainstalujte nejnovější verzi konektoru. Verze starší než 1.5.132.0 také vyžadovat že otevřít následující porty: 5671, 8080, 9090-9091, 9350, 9352, 10100–10120.

### <a name="allow-access-to-urls"></a>Povolit přístup k adresám URL

Povolit přístup k následujícím adresám URL:

| URL | Jak se používá |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Komunikace mezi konektoru a cloudovou službou Proxy aplikací |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Tyto adresy URL Azure používá k ověření certifikátů. |
| login.windows.net<br>login.microsoftonline.com<br>secure.aadcdn.microsoftonline-p.com  | Konektor používá tyto adresy URL během procesu registrace. |

Můžete povolit připojení k \*. msappproxy.net a \*. servicebus.windows.net Pokud brána firewall nebo proxy serveru vám umožní nakonfigurovat DNS povolit seznamy. Pokud ne, je potřeba povolit přístup k [rozsahy IP adres Azure DataCenter](https://www.microsoft.com/download/details.aspx?id=41653). Rozsahy IP adres se aktualizují každý týden.

## <a name="install-and-register-a-connector"></a>Instalace a registrace konektoru

Pokud chcete použít Proxy aplikace, nainstalujte konektor na každém Windows serveru, které používáte se službou Proxy aplikací. Konektor je agent, který spravuje odchozí připojení z aplikačních serverů s místními Proxy aplikace ve službě Azure AD. Konektor můžete nainstalovat na servery, které také obsahují další ověřování agenty nainstalované jako je například Azure AD Connect.

Instalace konektoru:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) jako správce adresáře, který používá aplikace Proxy aplikací. Například pokud doména tenanta je contoso.com, Správce by měl být admin@contoso.com nebo jakýkoli jiný alias správce v této doméně.
1. V pravém horním rohu vyberte své uživatelské jméno. Ověřte, že jste přihlášeni do adresáře, který používá Proxy aplikací. Pokud potřebujete změnit adresář, vyberte **přepnout adresář** a vyberte adresář, který používá Proxy aplikací.
1. V levém navigačním panelu vyberte **Azure Active Directory**.
1. V části **spravovat**vyberte **proxy aplikací**.
1. Vyberte **stáhnout službu konektoru**.

    ![Stáhnout službu konektoru zobrazíte podmínky služby](./media/application-proxy-add-on-premises-application/application-proxy-download-connector-service.png)

1. Přečtěte si podmínky služby. Jakmile budete připraveni, vyberte **přijmout podmínky a stáhnout**.
1. V dolní části okna vyberte **spustit** k instalaci konektoru. Otevře se Průvodce instalací.
1. Postupujte podle pokynů v průvodci pro instalaci služby. Po zobrazení výzvy k registraci konektoru Proxy aplikace pro vašeho tenanta Azure AD, zadejte svoje přihlašovací údaje správce aplikace.
    - Pro Internet Explorer (IE), pokud **konfigurace rozšířeného zabezpečení Internet Exploreru** je nastavena na **na**, nemusí se zobrazí na obrazovce registrace. Chcete-li získat přístup, postupujte podle pokynů v chybové zprávě. Ujistěte se, že **konfigurace rozšířeného zabezpečení aplikace Internet Explorer** je nastavena na **vypnout**.

### <a name="general-remarks"></a>Obecné poznámky

Pokud jste dříve nainstalovali konektor, znovu nainstalujte nejnovější verzi. Pokud chcete zobrazit informace o dřív vydaných verzí a změny, které jsou patří, naleznete v tématu [Proxy aplikací: Historie vydaných verzí](application-proxy-release-version-history.md).

Pokud budete chtít mít více než jeden server Windows pro místní aplikace, budete potřebovat k instalaci a registraci konektoru na každém serveru. Konektory můžete uspořádat do skupiny konektorů. Další informace najdete v tématu [skupiny konektorů](application-proxy-connector-groups.md).

Pokud vaše organizace používá proxy servery pro připojení k Internetu, musíte je nakonfigurovat pro Proxy aplikací.  Další informace najdete v tématu [práce s existující místní proxy servery](application-proxy-configure-connectors-with-proxy-servers.md). 

Informace o konektory, plánování kapacity a jak zůstanou aktuální najdete v tématu [pochopit Azure AD Application Proxy konektory](application-proxy-connectors.md).

## <a name="verify-the-connector-installed-and-registered-correctly"></a>Ověřte konektor nainstalován a správně zaregistrován

Na webu Azure portal nebo Windows server můžete použít k potvrzení, že nový konektor nainstalován správně.

### <a name="verify-the-installation-through-azure-portal"></a>Ověření instalace portálu Azure portal

Potvrďte konektor nainstalován a registrován správně:

1. Přihlaste se do adresáře v tenantovi [webu Azure portal](https://portal.azure.com).
1. V levém navigačním panelu vyberte **Azure Active Directory**a pak vyberte **Proxy aplikací** pod **spravovat** oddílu. Všechny konektory a skupiny konektorů se zobrazí na této stránce.
1. Zobrazte konektoru k ověření jeho podrobnosti. Ve výchozím nastavení by měly být rozbalené konektory. Pokud není rozbalen konektor, který chcete zobrazit, rozbalte konektoru zobrazíte podrobnosti. Aktivní zelený popisek označuje, že váš konektor může připojit ke službě. Ale i když je popisek zelené, potíže se sítí může stále blokovat konektoru příjem zprávy.

    ![Konektory Proxy aplikací Azure AD](./media/application-proxy-connectors/app-proxy-connectors.png)

Další nápovědu k instalaci konektoru najdete v tématu [problému s instalací konektoru Proxy aplikace](application-proxy-connector-installation-problem.md).

### <a name="verify-the-installation-through-your-windows-server"></a>Ověření instalace přes Windows serveru

Potvrďte konektor nainstalován a registrován správně:

1. Otevřete Správce služby Windows kliknutím **Windows** klíč a zadáním *services.msc*.
1. Zkontrolujte, jestli je stav pro následující dvě služby **systémem**.
   - **Microsoft AAD Application Proxy Connector** umožňuje připojení.
   - **Microsoft AAD Application Proxy Connector Updater** je služba pro automatickou aktualizaci. Aktualizátoru kontroluje nové verze konektoru a aktualizuje konektoru podle potřeby.

     ![Služby konektoru proxy aplikace – snímek obrazovky](./media/application-proxy-enable/app_proxy_services.png)

1. Pokud není stav služby **systémem**, klikněte pravým tlačítkem a vyberte jednotlivé služby a zvolte **Start**.

## <a name="add-an-on-premises-app-to-azure-ad"></a>Přidat místní aplikace do služby Azure AD

Teď, když jste připravit vaše prostředí a nainstalujete konektor, jste připraveni přidat místní aplikace do služby Azure AD.  

1. Přihlaste se jako správce [webu Azure portal](https://portal.azure.com/).
1. V levém navigačním panelu vyberte **Azure Active Directory**.
1. Vyberte **podnikové aplikace**a pak vyberte **novou aplikaci**.
1. Vyberte **On-premises application**.  
1. V **přidat vlastní místní aplikaci** části, zadejte následující informace o vaší aplikaci:

    | Pole | Popis |
    | :---- | :---------- |
    | **Název** | Název aplikace, které se zobrazí na přístupovém panelu a na webu Azure Portal. |
    | **Interní adresa URL** | Adresa URL pro přístup k aplikaci uvnitř vaší privátní sítě. Můžete zadat konkrétní cestu na beck-endovém serveru, kterou chcete publikovat, zatímco zbytek serveru publikovaný nebude. Tímto způsobem můžete publikovat různé stránky na stejném serveru jako různé aplikace a použít každý z nich vlastní název a pravidla přístupu.<br><br>Pokud publikujete cestu, ujistěte se, že zahrnuje všechny nezbytné obrázky, skripty a šablony stylů pro vaši aplikaci. Například, pokud je vaše aplikace v protokolu https:\//yourapp/aplikace a použití Image nachází v protokolu https:\//yourapp/médium a pak by měli zveřejnit informace protokolu https:\//yourapp/ jako cestu. Toto interní adresa URL nemusí být na úvodní stránku, kterou uživatelé uvidí. Další informace najdete v tématu [nastavit vlastní domovskou stránku pro publikované aplikace](application-proxy-configure-custom-home-page.md). |
    | **Externí adresa URL** | Adresa pro uživatele pro přístup k aplikaci mimo vaši síť. Pokud už nechcete používat výchozí doménu Proxy aplikací, přečtěte si informace o [vlastních domén v Azure AD Application Proxy](application-proxy-configure-custom-domain.md).|
    | **Předběžné ověření** | Jak Proxy aplikace ověřuje uživatele, než jim poskytne přístup k aplikaci.<br><br>**Azure Active Directory** – Proxy aplikace přesměruje uživatele na stránku přihlášení Azure AD, která ověří jejich oprávnění k adresáři a aplikaci. Doporučujeme tuto možnost jako výchozí, takže můžete využít výhod funkcí zabezpečení Azure AD, jako je podmíněný přístup a ověřování službou Multi-Factor Authentication. **Azure Active Directory** je vyžadován pro monitorování aplikací pomocí Microsoft Cloud Application Security.<br><br>**Průchod** – uživatelé nemají k ověřování vůči službě Azure AD pro přístup k aplikaci. Stále můžete nastavit požadavky na ověřování na back-endu. |
    | **Skupina konektorů.** | Konektory zpracování vzdálený přístup do vaší aplikace a skupiny konektorů můžete uspořádat konektory a aplikace podle oblasti, sítě nebo účel. Pokud nemáte žádné skupiny konektorů nemáte ještě vytvořený, vaše aplikace se přiřadí **výchozí**.<br><br>Pokud vaše aplikace používá objekty Websocket pro připojení, všechny konektory ve skupině musí být ve verzi 1.5.612.0 nebo novější.|

1. V případě potřeby nakonfigurujte **další nastavení**. Pro většinu aplikací byste měli mít tato nastavení na jejich výchozí stavy. 

    | Pole | Popis |
    | :---- | :---------- |
    | **Časový limit pro back-endu aplikace** | Nastavte tuto hodnotu na **dlouhé** pouze v případě, že vaše aplikace se ověřili a připojili. |
    | **Použít soubor Cookie jenom pro protokol HTTP** | Nastavte tuto hodnotu na **Ano** Pokud chcete, aby Proxy aplikací služby soubory cookie, zahrňte příznak HTTPOnly v hlavičce odpovědi protokolu HTTP. Pokud pomocí služby Vzdálená plocha, nastavte tuto hodnotu na **ne**.|
    | **Použití zabezpečeného souboru Cookie**| Nastavte tuto hodnotu na **Ano** k přenosu přes zabezpečený kanál například požadavek HTTPS šifrované soubory cookie.
    | **Použít trvalého souboru Cookie**| Ponechte tuto hodnotu nastavit na **ne**. Toto nastavení používejte pouze pro aplikace, které nelze sdílení souborů cookie mezi procesy. Další informace o nastavení souborů cookie najdete v tématu [nastavení souborů Cookie pro přístup k místním aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).
    | **Překládat adresy URL v hlavičkách** | Ponechte tuto hodnotu jako **Ano** Pokud vaše aplikace vyžaduje hlavičku původního hostitele v žádosti o ověření. |
    | **Překládat adresy URL v těle žádosti** | Ponechte tuto hodnotu jako **ne** Pokud nemáte pevně zakódované HTML odkazy na další místní aplikace a nebude se používat vlastní domény. Další informace najdete v tématu [propojit překlad textů pomocí Proxy aplikací](application-proxy-configure-hard-coded-link-translation.md).<br><br>Nastavte tuto hodnotu na **Ano** Pokud máte v plánu pro monitorování této aplikace s Microsoft Cloud App Security (MCAS). Další informace najdete v tématu [konfigurace aplikací v reálném čase přístup k monitorování pomocí Microsoft Cloud App Security a Azure Active Directory](application-proxy-integrate-with-microsoft-cloud-application-security.md). |

1. Vyberte **Přidat**.

## <a name="test-the-application"></a>Testování aplikace

Jste připraveni k testování aplikace je přidán správně. V následujícím postupu budete do aplikace přidat uživatelský účet a zkuste se přihlásit.

### <a name="add-a-user-for-testing"></a>Přidání uživatele pro účely testování

Před přidáním uživatele do aplikace, ověřte, že uživatelský účet už má oprávnění pro přístup k aplikaci uvnitř podnikové sítě.

Chcete-li přidat testovací uživatel:

1. Vyberte **podnikové aplikace**a pak vyberte aplikaci, kterou potřebujete otestovat.
1. Vyberte **Začínáme**a pak vyberte **přiřadit uživatele pro testování**.
1. V části **uživatelů a skupin**vyberte **přidat uživatele**.
1. V části **přidat přiřazení**vyberte **uživatelů a skupin**. **Uživatele a skupiny** se zobrazí část.
1. Vyberte účet, který chcete přidat.
1. Zvolte **vyberte**a pak vyberte **přiřadit**.

### <a name="test-the-sign-on"></a>Otestovat přihlašování

Chcete-li otestovat přihlašování k aplikaci:

1. V prohlížeči přejděte na externí adresu URL, kterou jste nakonfigurovali během kroku publikovat. Měli byste vidět na obrazovce start.
1. Přihlaste se jako uživatel, který jste vytvořili v předchozí části.

Řešení potíží, najdete v části [Poradce při potížích s Proxy aplikací problémy a chybové zprávy](application-proxy-troubleshoot.md).

## <a name="next-steps"></a>Další postup

V tomto kurzu jste připravili v místním prostředí pro práci s Proxy aplikací a pak nainstalován a registrován konektor Proxy aplikací. V dalším kroku přidáte aplikaci do svého tenanta Azure AD. Můžete ověřit, že uživatel může přihlásit k aplikaci pomocí účtu služby Azure AD.

Provedli jste tyto akce:
> [!div class="checklist"]
> * Otevřené porty pro odchozí provoz a povolený přístup pro konkrétní adresy URL
> * Na serveru pro Windows nainstalovaný konektor a zaregistrované v Proxy aplikací
> * Ověřit konektor nainstalován a správně zaregistrován
> * Přidat místní aplikace do svého tenanta Azure AD
> * Ověřit, že testovací uživatel může přihlásit k aplikaci pomocí účtu služby Azure AD

Jste připraveni nakonfigurovat aplikaci pro jednotné přihlašování. Chcete-li zvolit metodu jednotné přihlašování a jednotné přihlašování – kurzy, použijte tento odkaz.

> [!div class="nextstepaction"]
> [Konfigurace jednotného přihlašování](what-is-single-sign-on.md#choosing-a-single-sign-on-method)
