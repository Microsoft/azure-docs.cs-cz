---
title: Přidat místní aplikace – Proxy aplikace ve službě Azure Active Directory | Dokumentace Microsoftu
description: Azure Active Directory (Azure AD) obsahuje Proxy aplikace služby, který umožňuje uživatelům přistupovat k místním aplikacím, když se přihlásíte pomocí svého účtu Azure AD. V tomto kurzu se dozvíte, jak připravit prostředí pro použití se službou Proxy aplikací a potom pomocí webu Azure portal přidat místní aplikace do svého tenanta Azure AD.
services: active-directory
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: barbkess
ms.reviewer: japere
ms.openlocfilehash: 444fb5576ed6886e5919202cf7f22ef14e1255b5
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53321405"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Kurz: Přidat místní aplikace pro vzdálený přístup prostřednictvím Proxy aplikací v Azure Active Directory

Azure Active Directory (Azure AD) obsahuje Proxy aplikace služby, který umožňuje uživatelům přistupovat k místním aplikacím, když se přihlásíte pomocí svého účtu Azure AD. V tomto kurzu připraví vaše prostředí pro použití s Proxy aplikací. Jakmile je prostředí připravené, použijete k přidání místní aplikace do svého tenanta Azure AD na webu Azure portal.

V tomto kurzu:

> [!div class="checklist"]
> * Otevře porty pro odchozí provoz a umožňuje přístup ke konkrétní adresy URL
> * Konektor se nainstaluje na Windows serveru a zaregistruje ho s Proxy aplikací
> * Ověří konektor nainstalován a správně zaregistrován
> * Přidá místní aplikace do svého tenanta Azure AD
> * Ověřuje, že testovací uživatel může přihlásit k aplikaci pomocí účtu služby Azure AD.

## <a name="before-you-begin"></a>Před zahájením

K přidání aplikace do tenanta budete potřebovat:

* A [základní nebo prémiové předplatné služby Microsoft Azure AD](https://azure.microsoft.com/pricing/details/active-directory). 
* Účet správce aplikace.

### <a name="windows-server"></a>Windows server
Protože aplikace, kterou přidáváte je místně, musíte Windows serveru se systémem Windows Server 2012 R2 nebo novějším na kterých je nainstalován konektor Proxy aplikací. Tento konektor server potřebuje pro připojení ke službám Proxy aplikací v Azure a místních aplikací, které chcete publikovat.

Pro zajištění vysoké dostupnosti v produkčním prostředí doporučujeme mít více než jeden server Windows.  Pro účely tohoto kurzu je dostačující jeden Windows server.

**Doporučení pro server konektoru**

1. Fyzicky najděte server konektoru blízko aplikačních serverů za účelem optimalizace výkonu mezi konektoru a aplikací. Další informace najdete v tématu [aspekty topologie sítě](application-proxy-network-topology.md).

2. Server konektoru a webové servery aplikace by měly patřit do stejné domény služby Active Directory. Servery ve stejné doméně, je požadavek pro jednotné přihlašování (SSO) pomocí integrovaného ověřování Windows (IWA) a Kerberos omezené delegování (KCD). Pokud jsou server konektoru a serverů webových aplikací v různých doménách služby Active Directory, budete muset použít pro jednotné přihlašování založené na prostředcích delegování. Další informace najdete v tématu [KCD pro jednotné přihlašování s Proxy aplikací](application-proxy-configure-single-sign-on-with-kcd.md).

**Požadavky na software**

Windows server konektoru musí mít TLS 1.2 zapnutý předtím, než nainstalujete konektor Proxy aplikací. Existující konektory se starším než 1.5.612.0 budou nadále fungovat na dřívější verze TLS až do odvolání. 

Povolení protokolu TLS 1.2:

1. Nastavte následující klíče registru:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

2. Restartujte server

  
## <a name="prepare-your-on-premises-environment"></a>Příprava místního prostředí
Příprava prostředí pro Azure AD Application Proxy, musíte nejprve povolit komunikaci s datovými centry Azure. Pokud je v cestě brána firewall, ujistěte se, že je otevřený, takže se v konektoru požadavky prostřednictvím protokolu HTTPS (TCP) na Proxy aplikací.

### <a name="open-ports"></a>Otevřít porty

Otevřít následující porty **odchozí** provoz. 

   | Číslo portu | Jak se používá |
   | --- | --- |
   | 80 | Stahuje se odvolání certifikátu seznamy (CRL) při ověřování certifikátu SSL |
   | 443 | Veškerá odchozí komunikace se službou Proxy aplikací |

Pokud vaše brána firewall vynucuje přenos v závislosti na zdroji uživatelů, taky otevřete porty 80 a 443 pro přenosy služby Windows, na kterých běží jako síťové služby.

Pokud už používáte Proxy aplikací, bude pravděpodobně starší verzi nainstalovaný konektor.  V tomto kurzu nainstalujte nejnovější verzi konektoru. Verze starší než 1.5.132.0 také vyžadovat že otevřít následující porty: 5671, 8080, 9350 9090-9091 9352, 10100 – 10120. 

### <a name="allow-access-to-urls"></a>Povolit přístup k adresám URL

Povolit přístup k následujícím adresám URL:

| zprostředkovatele identity | Jak se používá |
| --- | --- |
| \*. msappproxy.net<br>servicebus.windows.net | Komunikace mezi konektoru a cloudovou službou Proxy aplikací |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Tyto adresy URL Azure používá k ověření certifikátů |
| login.windows.net<br>login.microsoftonline.com | Konektor používá tyto adresy URL během procesu registrace. |

Pokud vaše brána firewall nebo proxy server umožňuje přidávání na seznam povolených DNS, můžete seznam povolených připojení k msappproxy.net a servicebus.windows.net. Pokud ne, je potřeba povolit přístup k [rozsahy IP adres Azure DataCenter](https://www.microsoft.com/download/details.aspx?id=41653), které se aktualizují každý týden.

## <a name="install-and-register-a-connector"></a>Instalace a registrace konektoru
Pokud chcete použít Proxy aplikace, budete muset nainstalovat konektor na každém Windows serveru, kterou chcete použít se službou Proxy aplikací. Konektor je agent, který spravuje odchozí připojení z aplikačních serverů s místními Proxy aplikace ve službě Azure AD. Konektor můžete nainstalovat na servery, které také obsahují další ověřování agenty nainstalované jako je například Azure AD Connect.

Instalace konektoru:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) jako správce adresáře, který používá aplikace Proxy aplikací. Například pokud doména tenanta je contoso.com, Správce by měl být admin@contoso.com nebo jakýkoli jiný alias správce v této doméně.
2. V rámci své uživatelské jméno v pravém horním rohu se zobrazí váš aktuální adresář. Ověřte, že jste přihlášeni do adresáře, který používá Proxy aplikací. Pokud potřebujete změnit adresář, vybráním této ikony.
3. V levém okně klikněte na tlačítko **Azure Active Directory**a potom **proxy aplikací**.
4. Klikněte na tlačítko **stáhnout službu konektoru**.
5. Přečtěte si podmínky služby.  Jakmile budete připraveni, klikněte na tlačítko **přijmout podmínky a stáhnout**.
6. V dolní části okna, zobrazí se vám zobrazí výzva ke stažení **AADApplicationProxyConnectorInstaller.exe**. Klikněte na tlačítko **spustit** k instalaci konektoru. Otevře se Průvodce instalací. 
7. Nainstalujte podle pokynů instalačního průvodce. Po zobrazení výzvy k registraci konektoru Proxy aplikace pro vašeho tenanta Azure AD, zadejte svoje přihlašovací údaje správce aplikace.
    - Pro Internet Explorer (IE), pokud **konfigurace rozšířeného zabezpečení Internet Exploreru** je nastavena na **na**, nemusí se zobrazí na obrazovce registrace. Chcete-li získat přístup, postupujte podle pokynů v chybové zprávě. Ujistěte se, že rozšířeného zabezpečení aplikace Internet Explorer je nastavena na **vypnout**.

### <a name="general-remarks"></a>Obecné poznámky

Pokud jste dříve nainstalovali konektor, znovu nainstalujte nejnovější verzi.

Pokud budete chtít mít více než jeden server Windows pro místní aplikace, budete potřebovat k instalaci a registraci konektoru na každém serveru. Konektory můžete uspořádat do skupiny konektorů. Další informace najdete v tématu [skupiny konektorů](application-proxy-connector-groups.md). 

Pokud vaše organizace používá proxy servery pro připojení k Internetu, musíte je nakonfigurovat pro Proxy aplikací.  Další informace najdete v tématu [práce s existující místní proxy servery](application-proxy-configure-connectors-with-proxy-servers.md). 

Informace o konektory, plánování kapacity a jak zůstanou aktuální najdete v tématu [pochopit Azure AD Application Proxy konektory](application-proxy-connectors.md). 

Pokud vaše aplikace používá objekty Websocket pro připojení, ujistěte se, že jste nainstalovali nejnovější konektoru.  Objekty Websocket jsou podporovány verze konektoru 1.5.612.0 nebo novější.


## <a name="verify-the-connector-installed-and-registered-correctly"></a>Ověřte konektor nainstalován a správně zaregistrován

Na webu Azure portal nebo Windows server můžete použít k potvrzení, že nový konektor nainstalován správně.

### <a name="verify---azure-portal"></a>Ověřte – Azure portal
Potvrďte konektor nainstalován a registrován správně:

1. Přihlaste se do adresáře v tenantovi [webu Azure portal](https://portal.azure.com).
2. Klikněte na tlačítko **Azure Active Directory** a potom **Proxy aplikací**. Všechny konektory a skupiny konektorů se zobrazí na této stránce. 
3. Vyberte konektor ověřit její podrobnosti. Aktivní zelený popisek označuje, že váš konektor může připojit ke službě. Ale i když je popisek zelené, potíže se sítí může stále blokovat konektoru příjem zprávy. 

    ![Konektory Proxy aplikací Azure AD](./media/application-proxy-connectors/app-proxy-connectors.png)

Další nápovědu k instalaci konektoru najdete v tématu [potíže s instalací konektoru Proxy aplikace](application-proxy-connector-installation-problem.md).

### <a name="verify---windows-server"></a>Ověřte – Windows server
Potvrďte konektor nainstalován a registrován správně:

1. Otevřete Správce služby Windows kliknutím **Windows** klíč a zadáním *services.msc*.
2. Zkontrolujte, jestli je stav pro následující dvě služby **systémem**.
   - **Microsoft AAD Application Proxy Connector** umožňuje propojení
   - **Microsoft AAD Application Proxy Connector Updater** je služba pro automatickou aktualizaci. Aktualizátoru kontroluje nové verze konektoru a aktualizuje konektoru podle potřeby.

    ![Služby konektoru proxy aplikace – snímek obrazovky](./media/application-proxy-enable/app_proxy_services.png)

3. Pokud není stav služby **systémem**, klikněte pravým tlačítkem na jednotlivé služby a zvolte **start**. 

## <a name="add-an-on-premises-app-to-azure-ad"></a>Přidat místní aplikace do služby Azure AD

Teď, když jste připravit vaše prostředí a nainstalujete konektor, jste připraveni přidat místní aplikace do služby Azure AD.  

1. Přihlaste se jako správce [webu Azure portal](https://portal.azure.com/).
2. Vyberte **Azure Active Directory** > **podnikové aplikace** > **novou aplikaci**.

    ![Přidat podniková aplikace](./media/application-proxy-publish-azure-portal/add-app.png)

3. Vyberte **všechny**a pak vyberte **On-premises application**.  

    ![Přidat svoji vlastní aplikaci](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. Zadejte následující informace o vaší aplikaci:

    ![Konfigurace aplikace](./media/application-proxy-publish-azure-portal/configure-app.png)

    | Pole | Popis |
    | :---- | :---------- |
    | **Název** | Název aplikace, které se zobrazí na přístupovém panelu a na webu Azure Portal. |
    | **Interní adresa URL** | Adresa URL pro přístup k aplikaci uvnitř vaší privátní sítě. Můžete zadat konkrétní cestu na beck-endovém serveru, kterou chcete publikovat, zatímco zbytek serveru publikovaný nebude. Tímto způsobem můžete publikovat různé stránky na stejném serveru jako různé aplikace a použít každý z nich vlastní název a pravidla přístupu.<br><br>Pokud publikujete cestu, ujistěte se, že zahrnuje všechny nezbytné obrázky, skripty a šablony stylů pro vaši aplikaci. Například, pokud je vaše aplikace na https://yourapp/app a používá obrázky nacházející se v https://yourapp/media, pak by měli zveřejnit https://yourapp/ jako cestu. Toto interní adresa URL nemusí být na úvodní stránku, kterou uživatelé uvidí. Další informace najdete v tématu [nastavit vlastní domovskou stránku pro publikované aplikace](application-proxy-configure-custom-home-page.md). |
    | **Externí adresa URL** | Adresa pro uživatele pro přístup k aplikaci mimo vaši síť. Pokud už nechcete používat výchozí doménu Proxy aplikací, přečtěte si informace o [vlastních domén v Azure AD Application Proxy](application-proxy-configure-custom-domain.md).|
    | **Předběžné ověření** | Jak Proxy aplikace ověřuje uživatele, než jim poskytne přístup k aplikaci.<br><br>**Azure Active Directory** – Proxy aplikace přesměruje uživatele na stránku přihlášení Azure AD, která ověří jejich oprávnění k adresáři a aplikaci. Doporučujeme tuto možnost jako výchozí, takže můžete využít výhod funkcí zabezpečení Azure AD, jako je podmíněný přístup a ověřování službou Multi-Factor Authentication.<br><br>**Průchod** – uživatelé nemají k ověřování na základě Azure Active Directory pro přístup k aplikaci. Stále můžete nastavit požadavky na ověřování na back-endu. |
    | **Skupina konektorů.** | Konektory zpracování vzdálený přístup do vaší aplikace a skupiny konektorů můžete uspořádat konektory a aplikace podle oblasti, sítě nebo účel. Pokud nemáte žádné skupiny konektorů nemáte ještě vytvořený, vaše aplikace se přiřadí **výchozí**.<br><br>Pokud vaše aplikace používá objekty Websocket pro připojení, všechny konektory ve skupině musí být ve verzi 1.5.612.0 nebo novější.|

5. V případě potřeby nakonfigurujte další nastavení. Pro většinu aplikací byste měli mít tato nastavení na jejich výchozí stavy. 

    ![Konfigurace aplikace](./media/application-proxy-publish-azure-portal/additional-settings.png)

    | Pole | Popis |
    | :---- | :---------- |
    | **Časový limit pro back-endu aplikace** | Nastavte tuto hodnotu na **dlouhé** pouze v případě, že vaše aplikace se ověřili a připojili. |
    | **Použít soubor Cookie jenom pro protokol HTTP** | Nastavte tuto hodnotu na **Ano** Pokud chcete, aby Proxy aplikací služby soubory cookie, zahrňte příznak HTTPOnly v hlavičce odpovědi protokolu HTTP. Pokud pomocí služby Vzdálená plocha, nastavte ji na **ne**.|
    | **Použití zabezpečeného souboru Cookie**| Nastavte tuto hodnotu na **Ano** zajistit soubory cookie jenom přenosu přes zabezpečený kanál například šifrované požadavek HTTPS.
    | **Překládat adresy URL v hlavičkách** | Ponechte tuto hodnotu jako **Ano** Pokud vaše aplikace vyžaduje hlavičku původního hostitele v žádosti o ověření. |
    | **Překládat adresy URL v těle žádosti** | Ponechte tuto hodnotu jako **ne** Pokud nemáte pevně zakódované HTML odkazy na další místní aplikace a nebude se používat vlastní domény. Další informace najdete v tématu [propojit překlad textů pomocí Proxy aplikací](application-proxy-configure-hard-coded-link-translation.md). |
   


6. Vyberte **Přidat**.

## <a name="test-the-application"></a>Testování aplikace

K otestování, zda vaše aplikace byl přidán správně, budete do aplikace přidat uživatelský účet a zkuste se přihlásit. 

### <a name="add-a-user-for-testing"></a>Přidání uživatele pro účely testování
Před přidáním uživatele do aplikace, ověřte, že uživatelský účet už má oprávnění pro přístup k aplikaci uvnitř podnikové sítě.

Chcete-li přidat testovací uživatel:

1. Zpět na **úvodní** okně vyberte **přiřadit uživatele pro testování**.

    ![Přiřadit uživatele pro testování](./media/application-proxy-publish-azure-portal/assign-user.png)

2. Na **uživatelů a skupin** okně vyberte **přidat uživatele**.

    ![Přidat uživatele nebo skupinu](./media/application-proxy-publish-azure-portal/add-user.png)

3. Na **přidat přiřazení** okně vyberte **uživatelů a skupin**a pak vyberte účet, který chcete přidat. 
4. Vyberte **Přiřadit**.

### <a name="test-the-sign-on"></a>Otestovat přihlašování

Chcete-li otestovat přihlašování k aplikaci:

1. V prohlížeči přejděte na externí adresu URL, kterou jste nakonfigurovali během kroku publikovat. 
2. By měl zobrazit na obrazovce start a bude moct přihlásit se pomocí testovacího účtu, který nastavíte.

    ![Testování publikované aplikace](./media/application-proxy-publish-azure-portal/test-app.png)

Řešení potíží, najdete v části [Poradce při potížích s Proxy aplikací problémy a chybové zprávy](application-proxy-troubleshoot.md).

## <a name="next-steps"></a>Další postup
V tomto kurzu jste připravili v místním prostředí pro práci s Proxy aplikací a pak nainstalován a registrován konektor Proxy aplikací. V dalším kroku přidání aplikace do svého tenanta Azure AD a ověřit, že to šlo o podepisování v aplikaci pomocí účtu služby Azure AD.

Provedli jste tyto akce:
> [!div class="checklist"]
> * Otevřené porty pro odchozí provoz a povolený přístup pro konkrétní adresy URL
> * Na serveru pro Windows nainstalovaný konektor a zaregistrované v Proxy aplikací
> * Ověřit konektor nainstalován a správně zaregistrován
> * Přidat místní aplikace do svého tenanta Azure AD
> * Ověřit, že testovací uživatel může přihlásit k aplikaci pomocí účtu služby Azure AD.

Teď jste připraveni nakonfigurovat pro aplikaci jednotné přihlašování. Několika způsoby jednotné přihlašování a Výběr nejvhodnější způsob závisí na způsob, jakým vaše aplikace se ověřuje. Následující odkaz vám pomůže najít odpovídající jednotné přihlašování výukového programu pro vaši aplikaci.

> [!div class="nextstepaction"]
>[Konfigurace jednotného přihlašování](what-is-single-sign-on.md#choosing-a-single-sign-on-method)





