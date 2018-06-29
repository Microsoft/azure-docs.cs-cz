---
title: Integrovat Azure MFA VPN pomocí rozšíření Network Policy Server | Microsoft Docs
description: Pomocí rozšíření Network Policy Server pro Microsoft Azure integrate Azure MFA infrastrukturu sítě VPN.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 08/15/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: dfd28c5abea668e704fb5ee8b02d85d552e9ae77
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098868"
---
# <a name="integrate-your-vpn-infrastructure-with-azure-mfa-by-using-the-network-policy-server-extension-for-azure"></a>Integrovat Azure MFA infrastrukturu sítě VPN pomocí rozšíření Network Policy Server pro Azure.

## <a name="overview"></a>Přehled

Rozšíření serveru NPS (Network Policy Server) pro Azure umožňuje organizacím zabezpečit pomocí cloudového ověřování klientů RADIUS Remote Authentication Dial-In User Service () [Azure Multi-Factor Authentication (MFA)](howto-mfaserver-nps-rdg.md), který nabízí dvoustupňové ověřování.

Tento článek obsahuje pokyny k integraci infrastruktury serveru NPS pomocí vícefaktorového ověřování pomocí serveru NPS rozšíření pro Azure. Tento proces umožní bezpečné dvoustupňové ověřování pro uživatele, kteří se pokoušejí připojit k síti pomocí sítě VPN. 

Služba Síťové zásady a přístup poskytuje organizacím umožňuje:

* Přiřaďte centrálního umístění pro správu a řízení požadavků sítě k určení:

    * Kdo se může připojit 
    
    * Jaké doby připojení den jsou povoleny. 
    
    * Dobu trvání připojení
    
    * Úroveň zabezpečení, které musí klienti používat pro připojení

    Místo zadat zásady na každém serveru sítě VPN nebo brány vzdálené plochy, tak učinit po jsou v centrálním umístění. Protokol RADIUS slouží k centralizované ověřování, autorizace a monitorování (AAA). 

* Stanovit a vynutit zásady stavu klienta Network Access Protection (NAP), které určují, jestli zařízení mají neomezeného nebo omezeného přístupu k síťovým prostředkům.

* Zadejte způsob, jak vynucení ověřování a autorizace pro přístup k protokolu 802. 1 x podporující bezdrátové přístupové body a přepínače sítě Ethernet.   
Další informace najdete v tématu [Network Policy Server](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top). 

Pokud chcete zvýšit zabezpečení a zajistit vysokou úroveň dodržování předpisů, organizace může integrovat NPS Azure Multi-Factor Authentication zajistit, aby uživatelé používali dvoustupňové ověřování pro připojení k virtuálnímu portu na serveru VPN. Uživatelům udělen přístup musí zadat svoje uživatelské jméno a kombinace hesla a další informace, které budou řídit. Tyto informace musí být důvěryhodný a nebude jednoduché duplicitní. Může obsahovat číslo mobilního telefonu, číslo pevné nebo aplikace v mobilním zařízení.

Před dostupnost serveru NPS rozšíření pro Azure zákazníky, kteří chtěli implementovat dvoustupňové ověřování pro integraci serveru NPS a MFA prostředí museli konfigurovat a spravovat samostatný server MFA v místním prostředí. Tento typ ověřování se nabízí Brána vzdálené plochy a pomocí protokolu RADIUS serveru Azure Multi-Factor Authentication Server.

S příponou NPS pro Azure můžete zabezpečit organizace nasazením řešení MFA s místní nebo cloudové řešení vícefaktorového ověřování, ověřování klienta protokolu RADIUS.
 
## <a name="authentication-flow"></a>Tok ověřování
Když se uživatelé připojí na virtuální port na serveru VPN, musí je nejdřív ověřit pomocí různých protokolů. Protokoly povolit použití kombinace uživatelského jména a hesla a metody ověřování založené na certifikátu. 

Kromě ověřování a ověření identity, uživatelé musí mít příslušná oprávnění. V jednoduché implementace jsou nastavena dial-in oprávnění, které umožňují přístup přímo na objekty uživatele služby Active Directory. 

![Uživatelské vlastnosti](./media/howto-mfa-nps-extension-vpn/image1.png)

Každý server sítě VPN v jednoduché implementace, uděluje nebo odmítne přístup na základě zásad, které jsou definovány na každém serveru místní VPN.

V implementacích větší a větší škálovatelnost jsou zásady, které udělit nebo odepřít přístup k síti VPN centralizované na servery RADIUS. V těchto případech serveru VPN funguje jako server služby přístupu (klienta RADIUS), který předává žádosti o připojení a zprávy účtu na serveru RADIUS. Pro připojení k virtuálnímu portu na serveru VPN, uživatelé musí být ověřeny a splňují podmínky, které jsou definované centrálně na servery RADIUS. 

Když NPS rozšíření pro Azure je integrována se na server NPS, výsledků toku úspěšné ověřování, následujícím způsobem:

1. Server sítě VPN obdrží požadavek na ověření od uživatele sítě VPN, která obsahuje uživatelské jméno a heslo pro připojení k prostředku, jako je například relaci vzdálené plochy. 

2. Funguje jako klienta protokolu RADIUS, VPN server převede žádost protokolem RADIUS *žádost o přístup* zprávy a odešle ho (se zašifrovaným heslem) se serverem RADIUS nainstalovanou rozšíření serveru NPS. 

3. Kombinace uživatelského jména a hesla je ověřit ve službě Active Directory. Pokud uživatelské jméno nebo heslo nejsou správné, odešle RADIUS Server *odepření přístupu* zprávy. 

4. Pokud jsou splněny všechny podmínky zadané v žádosti o připojení serveru NPS a zásady sítě (například čas, den nebo skupiny omezeními pro členství vyplývajícími), NPS rozšíření aktivuje žádost o sekundární ověření službou Azure Multi-Factor Authentication. 

5. Azure Multi-Factor Authentication komunikuje se službou Azure Active Directory, načte podrobnosti uživatele a provádí sekundární ověřování pomocí metody, která je nakonfigurovaná uživatelem (buňky telefonní hovor, textová zpráva nebo mobilní aplikace). 

6. Po úspěšné výzvy vícefaktorového ověřování Azure Multi-Factor Authentication komunikuje výsledek, který má rozšíření serveru NPS.

7. Po ověření i autorizace na server NPS, kde je nainstalován rozšíření odešle RADIUS *povolení přístupu* zpráva k serveru sítě VPN (klienta RADIUS).

8. Uživateli je udělen přístup k virtuálnímu portu na serveru VPN a vytvoří šifrované tunelové propojení VPN.

## <a name="prerequisites"></a>Požadavky
Tato část podrobně požadavky, které je třeba dokončit před MFA může integrovat služby Brána vzdálené plochy. Než začnete, musíte mít následující požadavky na místě:

* Infrastruktura sítě VPN.
* Role služby Síťové zásady a přístup
* Licence Azure Multi-Factor Authentication
* Software Windows serveru
* Knihovny
* Azure Active Directory (Azure AD) synchronizované s místní služby Active Directory 
* ID identifikátor GUID služby Azure Active Directory

### <a name="vpn-infrastructure"></a>Infrastruktura sítě VPN.
Tento článek předpokládá, že máte infrastrukturu sítě VPN práci, kterou využívá Microsoft Windows Server 2016 a že VPN server není nyní nakonfigurován, aby předával požadavky na RADIUS server připojení. V článku můžete nakonfigurovat infrastrukturu sítě VPN na použití centrálního serveru RADIUS.

Pokud nemáte infrastrukturu sítě VPN práci na místě, můžete rychle vytvořit pomocí následujících kroků uvedených v řada návodů nastavení VPN, které můžete najít na společnosti Microsoft a weby třetích stran. 
            
### <a name="the-network-policy-and-access-services-role"></a>Role služby Síťové zásady a přístup

Služba Síťové zásady a přístup poskytuje funkci serveru a klienta RADIUS. Tento článek předpokládá, že jste nainstalovali roli služby Síťové zásady a přístup na členský server nebo řadič domény ve vašem prostředí. V této příručce konfigurace RADIUS pro konfiguraci sítě VPN. Nainstalujte roli služby Síťové zásady a přístup na serveru *jiné než* VPN server.

Informace o instalaci role Síťové zásady a přístup ke službě služby Windows Server 2012 nebo novější, najdete na [nainstalovat Server zásad stavu NAP](https://technet.microsoft.com/library/dd296890.aspx). Architektura NAP je zastaralá v Windows Server 2016. Popis osvědčené postupy pro server NPS, včetně doporučení k instalaci NPS na řadiči domény najdete v části [osvědčené postupy pro server NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-mfa-license"></a>Licence Azure MFA

Licence je vyžadována pro Azure Multi-Factor Authentication, a je k dispozici prostřednictvím Azure AD Premium, Enterprise Mobility + Security nebo samostatné licence služby Multi-Factor Authentication. Na základě spotřeby licencí Azure MFA jako ověřování licencí vázaných na uživatele nebo nejsou kompatibilní s příponou serveru NPS. Další informace najdete v tématu [jak získat Azure Multi-Factor Authentication](concept-mfa-licensing.md). Pro účely testování můžete použít zkušební verzi předplatného.

### <a name="windows-server-software"></a>Software Windows serveru

Rozšíření serveru NPS vyžaduje Windows Server 2008 R2 SP1 nebo novější s rolí Služba Síťové zásady a přístup nainstalovat. Se systémem Windows Server 2016 nebyly provedeny všechny kroky v této příručce.

### <a name="libraries"></a>Knihovny

S příponou NPS se automaticky nainstalují následující knihovny:

-   [Distribuovatelné balíčky Visual C++ pro Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-   [Microsoft Azure Active Directory modul pro prostředí Windows PowerShell verze 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

Pokud modul Microsoft Azure Active Directory PowerShell již není přítomna, se instaluje s konfigurační skript, který spouštíte jako součást procesu instalace. Není nutné nainstalovat modul předem, pokud již není nainstalována.

### <a name="azure-active-directory-synced-with-on-premises-active-directory"></a>Azure Active Directory synchronizované s místní služby Active Directory 

Použití rozšíření serveru NPS, musí být místních uživatelů se synchronizují s Azure Active Directory a povolené pro MFA. Tato příručka předpokládá, že místních uživatelů se synchronizují s Azure Active Directory přes Azure AD Connect. Pokyny k povolení uživatelů pro MFA jsou uvedeny níže.

Informace o Azure AD Connect najdete v tématu [integraci místních adresářů se službou Azure Active Directory](../connect/active-directory-aadconnect.md). 

### <a name="azure-active-directory-guid-id"></a>ID identifikátor GUID služby Azure Active Directory 

Pokud chcete nainstalovat rozšíření serveru NPS, musíte znát identifikátor GUID služby Azure Active Directory. Pokyny pro hledání identifikátor GUID služby Azure Active Directory najdete v další části.

## <a name="configure-radius-for-vpn-connections"></a>Konfigurace protokolu RADIUS pro připojení k síti VPN

Pokud jste nainstalovali NPS role na členský server, budete muset nakonfigurovat ji k ověřování a autorizaci klienta VPN této žádosti o připojení k síti VPN. 

V této části se předpokládá, že máte nainstalovanou roli služby Síťové zásady a přístup, ale nenakonfigurovali ho pro použití ve vaší infrastruktuře.

> [!NOTE]
> Pokud už máte pracovní serveru VPN, který používá centralizovaný server RADIUS pro ověřování, můžete tuto část přeskočit.
>

### <a name="register-server-in-active-directory"></a>Registrace serveru ve službě Active Directory
V tomto scénáři, fungovat správně, musí se na server NPS zaregistrovat ve službě Active Directory.

1. Otevřete správce serveru.

2. Ve Správci serveru vyberte **nástroje**a potom vyberte **Network Policy Server**. 

3. V konzole serveru NPS klikněte pravým tlačítkem na **server NPS (místní)** a potom vyberte **server zaregistrovat ve službě Active Directory**. Vyberte **OK** dvakrát.

    ![Server NPS](./media/howto-mfa-nps-extension-vpn/image2.png)

4. Ponechte konzole otevřené pro následující postup.

### <a name="use-wizard-to-configure-the-radius-server"></a>Použití Průvodce konfigurací serveru RADIUS
Můžete použít standardní (založené na průvodci) nebo rozšířené možnosti konfigurace pro konfigurace serveru RADIUS. Této části se předpokládá, že používáte možnost založené na průvodci standardní konfigurace.

1. V konzole serveru zásad sítě vyberte **server NPS (místní)**.

2. V části **standardní konfiguraci**, vyberte **Server RADIUS pro vytáčená nebo VPN připojení**a potom vyberte **konfigurace sítě VPN nebo telefonické**.

    ![Konfigurace sítě VPN](./media/howto-mfa-nps-extension-vpn/image3.png)

3. V **vyberte telefonicky nebo typ síťového připojení virtuální privátní** okně vyberte **připojení k virtuální privátní síti**a potom vyberte **Další**.

    ![Virtuální privátní sítě](./media/howto-mfa-nps-extension-vpn/image4.png)

4. V **zadejte vytáčená nebo VPN serveru** vyberte **přidat**.

5. V **klienta RADIUS nové** okno, zadejte popisný název, zadejte rozlišitelný název nebo IP adresu serveru VPN a pak zadejte sdílený tajný heslo. Ujistěte se, sdílené tajné heslo dlouhá a složitá. Záznam, protože ho budete potřebovat v další části.

    ![Nový klient protokolu RADIUS](./media/howto-mfa-nps-extension-vpn/image5.png)

6. Vyberte **OK**a potom vyberte **Další**.

7. V **konfigurovat metody ověřování** okno, přijměte výchozí hodnotu (**šifrované ověření 2 [MS-CHAPv2])** nebo zvolte jinou možnost a vyberte **další** .

    > [!NOTE]
    > Při konfiguraci protokolu EAP (Extensible Authentication), musíte použít Microsoft Challenge Handshake Authentication Protocol (CHAPv2) nebo Protected Extensible Authentication Protocol (PEAP). Žádné jiné EAP je podporována.
 
8. V **zadat skupiny uživatelů** vyberte **přidat**a potom vyberte příslušné skupiny. Pokud žádná skupina existuje, nezadáte výběr udělit přístup všem uživatelům.

    ![Okno zadat skupiny uživatelů](./media/howto-mfa-nps-extension-vpn/image7.png)

9. Vyberte **Next** (Další).

10. V **zadejte filtry IP adres** vyberte **Další**.

11. V **zadejte nastavení šifrování** oken, přijměte výchozí nastavení a potom vyberte **Další**.

    ![Zadejte nastavení šifrování okna](./media/howto-mfa-nps-extension-vpn/image8.png)

12. V **zadejte název sféry** název sféry ponechat prázdné, přijměte výchozí nastavení a pak vyberte **Další**.

    ![Zadejte název sféry okna](./media/howto-mfa-nps-extension-vpn/image9.png)

13. V **dokončení nové telefonické připojení nebo připojení k virtuální privátní síti a RADIUS klienty** vyberte **Dokončit**.

    ![Okno "dokončení nové telefonické nebo připojení virtuální privátní sítě a RADIUS klienti"](./media/howto-mfa-nps-extension-vpn/image10.png)

### <a name="verify-the-radius-configuration"></a>Ověření konfigurace RADIUS
Tato část Podrobnosti o konfiguraci, kterou jste vytvořili pomocí průvodce.

1. Na serveru Network Policy Server, v konzole serveru NPS (místní) rozbalte **klientů RADIUS**a potom vyberte **klientů RADIUS**.

2. V podokně podrobností klikněte pravým tlačítkem na klienta RADIUS, který jste vytvořili a potom vyberte **vlastnosti**. Vlastnosti vašeho klienta protokolu RADIUS (VPN server) by měl vypadat jako uvedených zde:

    ![Vlastnosti sítě VPN](./media/howto-mfa-nps-extension-vpn/image11.png)

3. Vyberte **zrušit**.

4. Na serveru Network Policy Server, v konzole serveru NPS (místní) rozbalte **zásady**a potom vyberte **zásady vyžádání nového připojení**. Zásada připojení k síti VPN se zobrazí, jak je znázorněno na následujícím obrázku:

    ![Žádosti o připojení](./media/howto-mfa-nps-extension-vpn/image12.png)

5. V části **zásady**, vyberte **zásady sítě**. Měli byste vidět zásadu připojení virtuální privátní sítě (VPN), která vypadá přibližně takto: zásady vidět na následujícím obrázku:

    ![Zásady sítě](./media/howto-mfa-nps-extension-vpn/image13.png)

## <a name="configure-your-vpn-server-to-use-radius-authentication"></a>Nakonfigurujte server VPN ověřování pomocí protokolu RADIUS
V této části nakonfigurujete server VPN ověřování pomocí protokolu RADIUS. Pokyny předpokládají, že máte funkční konfigurace serveru VPN, ale nebyly nakonfigurované na používání ověřování pomocí protokolu RADIUS. Po dokončení konfigurace serveru VPN, potvrďte, že vaše konfigurace funguje podle očekávání.

> [!NOTE]
> Pokud již máte funkční konfigurace serveru VPN, který používá ověřování pomocí protokolu RADIUS, můžete tuto část přeskočit.
>

### <a name="configure-authentication-provider"></a>Konfigurace zprostředkovatele ověřování
1. Na serveru VPN spusťte Správce serveru.

2. Ve Správci serveru vyberte **nástroje**a potom vyberte **směrování a vzdálený přístup**.

3. V **směrování a vzdálený přístup** okna, klikněte pravým tlačítkem na  **\<název serveru > (místní)** a potom vyberte **vlastnosti**.

    ![Okno Směrování a vzdálený přístup](./media/howto-mfa-nps-extension-vpn/image14.png)
 
4. V  **\<název serveru > vlastnosti (místní)** vyberte **zabezpečení** kartě. 

5. Na **zabezpečení** v části **zprostředkovatele ověřování**, vyberte **ověřování pomocí protokolu RADIUS**a potom vyberte **konfigurace**.

    ![Ověřování RADIUS](./media/howto-mfa-nps-extension-vpn/image15.png)
 
6. V **ověřování pomocí protokolu RADIUS** vyberte **přidat**.

7. V **přidat Server RADIUS** okno, postupujte takto:

    a. V **název serveru** , zadejte název nebo IP adresu serveru RADIUS, který jste nakonfigurovali v předchozím oddílu.

    b. Pro **sdílený tajný klíč**, vyberte **změnu**a pak zadejte sdílený tajný heslo, které jste vytvořili a poznamenali dříve.

    c. V **časový limit (sekundy)** , vyberte hodnotu od **30** prostřednictvím **60**.  
    Hodnota časového limitu je nutné povolit dostatek času k dokončení druhý ověřovací faktor.
 
    ![Okno Přidat Server RADIUS](./media/howto-mfa-nps-extension-vpn/image16.png)
 
8. Vyberte **OK**.

### <a name="test-vpn-connectivity"></a>Test připojení VPN
V této části potvrdíte, že klient VPN ověří a autorizuje server RADIUS, při pokusu o připojení k virtuálnímu portu sítě VPN. Pokyny předpokládají, že používáte Windows 10 jako klienta VPN. 

> [!NOTE]
> Pokud jste již nakonfigurovali klienta VPN pro připojení k serveru VPN a uložení nastavení, můžete přeskočit kroky týkající se konfigurace a uložení objekt připojení VPN.
>

1. Na klientském počítači sítě VPN, vyberte **spustit** tlačítko a pak vyberte **nastavení** tlačítko.

2. V **nastavení systému Windows** vyberte **síť a Internet**.

3. Vyberte **VPN**.

4. Vyberte **přidat připojení VPN**.

5. V **přidat připojení VPN** okno v **poskytovatel VPN** vyberte **systému Windows (předdefinovaná)** dokončete zbývající pole, podle potřeby a potom vyberte **Uložit**. 

    ![Okno "Přidat připojení VPN."](./media/howto-mfa-nps-extension-vpn/image17.png)
 
6. Přejděte na **ovládací panely**a potom vyberte **Centrum sítí a sdílení**.

7. Vyberte **změnit nastavení adaptéru**.

    ![Změnit nastavení adaptéru](./media/howto-mfa-nps-extension-vpn/image18.png)

8. Klikněte pravým tlačítkem na připojení sítě VPN a pak vyberte **vlastnosti**. 

    ![Vlastnosti sítě VPN](./media/howto-mfa-nps-extension-vpn/image19.png)

9. V okně Vlastnosti sítě VPN, vyberte **zabezpečení** kartě. 

10. Na **zabezpečení** zkontrolujte pouze **Microsoft CHAP Version 2 (MS-CHAP v2)** vybrané, a pak vyberte možnost **OK**.

    ![Možnost "Povolit tyto protokoly"](./media/howto-mfa-nps-extension-vpn/image20.png)

11. Klikněte pravým tlačítkem na připojení k síti VPN a pak vyberte **Connect**.

12. V **nastavení** vyberte **Connect**.  
    Úspěšné připojení zobrazí se v protokolu zabezpečení na serveru RADIUS jako události 6272 ID, jak je vidět tady:

    ![Okno vlastností událostí](./media/howto-mfa-nps-extension-vpn/image21.png)

## <a name="troubleshooting-radius"></a>Řešení potíží s protokolu RADIUS

Předpokládejme, že konfiguraci VPN fungovala předtím, než jste nakonfigurovali server VPN používat centralizovaný server RADIUS pro ověřování a autorizaci. Pokud byla konfigurace funguje, je pravděpodobné, že tento problém je způsoben nesprávnou konfiguraci serveru RADIUS nebo použití neplatného uživatelského jména nebo hesla. Například pokud použijete alternativní přípona UPN v uživatelské jméno, pokus o přihlášení může selhat. Použijte stejný název účtu pro dosažení co nejlepších výsledků. 

Chcete-li vyřešit tyto problémy, je ideální místo spuštění Zkontrolujte protokoly událostí zabezpečení na serveru RADIUS. Pokud chcete uložit, čas vyhledat události, můžete na základě rolí síťové zásady a přístup k serveru vlastní zobrazení v prohlížeči událostí, jak je vidět tady. "ID události 6273" Určuje události, které na server NPS odepřen přístup na uživatele. 

![Prohlížeč událostí](./media/howto-mfa-nps-extension-vpn/image22.png)
 
## <a name="configure-multi-factor-authentication"></a>Konfigurace služby Multi-Factor Authentication

Pomoc při konfiguraci uživatelů pro službu Multi-Factor Authentication najdete v článcích [jak vyžadovat dvoustupňové ověřování pro uživatele nebo skupinu](howto-mfa-userstates.md) a [nastavit účtu pro dvoustupňové ověření](end-user/current/multi-factor-authentication-end-user-first-time.md)

## <a name="install-and-configure-the-nps-extension"></a>Instalace a konfigurace serveru NPS rozšíření

Tato část obsahuje pokyny ke konfiguraci sítě VPN pro použití vícefaktorového ověřování pro ověřování klientů se serverem VPN.

Po dokončení instalace a konfigurace serveru NPS rozšíření, všechny ověřování klienta na základě protokolu RADIUS, které zpracovává tento server je potřeba použít vícefaktorové ověřování. Pokud všichni uživatelé sítě VPN nejsou zaregistrovaná v Azure Multi-Factor Authentication, můžete provést jeden z následujících akcí:

* Nastavte jiný server RADIUS pro ověřování uživatelů, kteří nejsou nakonfigurovaná pro použití vícefaktorového ověřování. 

* Vytvořte položku registru, který umožňuje němuž uživatelům poskytnout druhý ověřovací faktor, pokud jsou zaregistrovaná ve službě Azure Multi-Factor Authentication. 

Vytvořte novou řetězcovou hodnotu s názvem _REQUIRE_USER_MATCH v HKLM\SOFTWARE\Microsoft\AzureMfa_a nastavte hodnotu na *True* nebo *False*. 

![Nastavení "Vyžadovat shodu uživatele"](./media/howto-mfa-nps-extension-vpn/image34.png)
 
Pokud je hodnota nastavená *True* nebo je prázdné, všechny požadavky na ověření se vztahují ověřovací test MFA. Pokud je hodnota nastavená *False*, vícefaktorového ověřování výzvy vydávají jen uživatelům, kteří jsou zaregistrované v Azure Multi-Factor Authentication. Použití *False* nastavení pouze v testování nebo v produkčním prostředí během období registrace.

### <a name="obtain-the-azure-active-directory-guid-id"></a>Získat Identifikátor GUID služby Azure Active Directory

Jako součást konfigurace serveru NPS rozšíření je třeba zadat pověření správce a ID klienta služby Azure AD. Získejte ID následujícím způsobem:

1. Přihlaste se k [portál Azure](https://portal.azure.com) jako globální správce Azure klienta.

2. V levém podokně, vyberte **Azure Active Directory** tlačítko.

3. Vyberte **vlastnosti**.

4. Pokud chcete zkopírovat vaše ID Azure AD, vyberte **kopie** tlačítko.
 
    ![ID služby Azure AD](./media/howto-mfa-nps-extension-vpn/image35.png)

### <a name="install-the-nps-extension"></a>Nainstalujte rozšíření serveru NPS
Rozšíření serveru NPS musí být nainstalován na server, který má síťové zásady a nainstalovanou rolí služby pro přístup a která slouží jako server RADIUS při návrhu. Proveďte *není* nainstalovat rozšíření serveru NPS na serveru vzdálené plochy.

1. Stáhněte si rozšíření serveru NPS ze [Microsoft Download Center](https://aka.ms/npsmfa). 

2. Zkopírujte instalační spustitelný soubor (*NpsExtnForAzureMfaInstaller.exe*) na server NPS.

3. Na serveru NPS, dvakrát klikněte na **NpsExtnForAzureMfaInstaller.exe** a pokud se zobrazí výzva, vyberte **spustit**.

4. V **NPS rozšíření pro Azure MFA instalace** okno, zkontrolujte licenční podmínky pro software, vyberte **souhlasím s licenční podmínky a ujednání** zaškrtněte políčko a potom vyberte **nainstalovat**.

    ![Okno "NPS rozšíření pro Azure MFA nastavení"](./media/howto-mfa-nps-extension-vpn/image36.png)
 
5. V **NPS rozšíření pro Azure MFA instalace** vyberte **Zavřít**.  

    !["Instalační program úspěšně" okno pro potvrzení](./media/howto-mfa-nps-extension-vpn/image37.png) 
 
### <a name="configure-certificates-for-use-with-the-nps-extension-by-using-a-powershell-script"></a>Konfigurace certifikátů pro použití s příponou NPS pomocí skriptu prostředí PowerShell
Zabezpečení komunikace a záruku, konfiguraci certifikátů pro použití rozšíření serveru NPS. Součásti serveru NPS zahrnují skript prostředí Windows PowerShell, který konfiguruje certifikát podepsaný svým držitelem pro použití se serverem NPS. 

Skript provede následující akce:

* Vytvoří certifikát podepsaný svým držitelem.
* Přidruží veřejný klíč certifikátu pro službu objektu zabezpečení v Azure AD.
* Ukládá certifikát v úložišti místního počítače.
* Uděluje přístup uživatelů sítě k privátní klíč certifikátu.
* Restartuje službu NPS.

Pokud chcete používat vlastní certifikáty, musí veřejný klíč certifikátu přidružit objektu služby v Azure AD a tak dále.

Chcete-li použít skript, poskytnout rozšíření vaše pověření pro správu Azure Active Directory a ID klienta Azure Active Directory, který jste zkopírovali dříve. Spuštění skriptu na každý server NPS, který jste nainstalovali rozšíření serveru NPS.

1. Spusťte prostředí Windows PowerShell jako správce.

2. Na příkazovém řádku prostředí PowerShell zadejte **cd "c:\Program Files\Microsoft\AzureMfa\Config"** a potom vyberte Enter.

3. Zadejte na příkazovém řádku další **.\AzureMfsNpsExtnConfigSetup.ps1**a potom vyberte Enter. Skript zkontroluje, zda je nainstalován modul Azure AD PowerShell. Pokud není nainstalována, skript nainstaluje modul pro vás.
 
    ![PowerShell](./media/howto-mfa-nps-extension-vpn/image38.png)
 
    Po skript ověřuje instalace modulu prostředí PowerShell, zobrazí okno přihlášení Azure Active Directory PowerShell modulu. 

4. Zadejte svoje přihlašovací údaje Správce služby Azure AD a heslo a potom vyberte **přihlášení**. 
 
    ![Přihlášení okno prostředí PowerShell](./media/howto-mfa-nps-extension-vpn/image39.png)
 
5. Na příkazovém řádku vložte ID klienta, který jste zkopírovali dříve a pak vyberte Enter. 

    ![ID tenanta](./media/howto-mfa-nps-extension-vpn/image40.png)

    Tento skript vytvoří certifikát podepsaný svým držitelem a provede další změny v konfiguraci. Výstup je jako je například na následujícím obrázku:

    ![Certifikát podepsaný svým držitelem](./media/howto-mfa-nps-extension-vpn/image41.png)

6. Restartujte server.

### <a name="verify-the-configuration"></a>Zkontrolujte konfiguraci
Pokud chcete ověřit konfiguraci, je potřeba vytvořit nové připojení VPN se serverem VPN. Po úspěšně jste zadali svoje přihlašovací údaje pro primární ověřování, čeká na připojení k síti VPN pro sekundární ověření úspěšné před připojení, jak je uvedeno níže. 

![Okno nastavení sítě VPN ve Windows](./media/howto-mfa-nps-extension-vpn/image42.png)

Pokud jste úspěšně ověřit pomocí metody sekundární ověření, který jste dříve nakonfigurovali v Azure MFA, jste připojení k prostředku. Pokud sekundárního ověřování neproběhne úspěšně, ale je odepřen přístup k prostředku. 

V následujícím příkladu poskytuje Microsoft Authenticator aplikace na Windows Phone sekundárního ověřování:

![Ověření účtu.](./media/howto-mfa-nps-extension-vpn/image43.png)

Poté, co jste úspěšně ověřen pomocí metody sekundární, je uděleno oprávnění na virtuální port na serveru VPN. Protože jste museli používat metodu sekundární ověřování pomocí mobilní aplikace na důvěryhodném zařízení, proces přihlášení je bezpečnější než pokud používali pouze kombinace uživatelského jména a hesla.

### <a name="view-event-viewer-logs-for-successful-sign-in-events"></a>Zobrazit protokoly Prohlížeče událostí pro úspěšné události přihlášení
Chcete-li zobrazit úspěšné události přihlášení v protokolech prohlížeče událostí systému Windows, protokolu dotazu zabezpečení systému Windows na serveru NPS zadáním následujícího příkazu Powershellu:

    _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL 

![Prostředí PowerShell zabezpečení Prohlížeč událostí](./media/howto-mfa-nps-extension-vpn/image44.png)
 
Také můžete zobrazit protokol zabezpečení nebo vlastní zobrazení služby Síťové zásady a přístup, jak je vidět tady:

![Protokol serveru zásad sítě](./media/howto-mfa-nps-extension-vpn/image45.png)

Na serveru, kam jste nainstalovali server NPS rozšíření pro Azure Multi-Factor Authentication, můžete najít protokoly Prohlížeče událostí aplikace, které jsou specifické pro rozšíření na *aplikace a služby Logs\Microsoft\AzureMfa*. 

    _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL

![Prohlížeč událostí "Počet událostí" podokno](./media/howto-mfa-nps-extension-vpn/image46.png)

## <a name="troubleshooting-guide"></a>Průvodce odstraňováním potíží
Pokud konfigurace nefungují podle očekávání, začnou, řešení potíží pomocí ověření, že je správce nakonfigurován na použití vícefaktorového ověřování. Uživatel připojit k [portál Azure](https://portal.azure.com). Pokud je uživatel vyzván k zadání sekundárního ověřování a můžete úspěšně ověřit, že můžete eliminovat nesprávné konfiguraci vícefaktorového ověřování jako problém.

Pokud MFA funguje pro uživatele, přečtěte si relevantní protokoly Prohlížeče událostí. Protokoly obsahovat událostí zabezpečení, brány provozní a protokoly pro ověřování Azure Multi-Factor Authentication, které jsou popsané v předchozí části. 

Příklad protokolu zabezpečení, která zobrazuje neúspěšné přihlášení událostí (událost ID 6273) je uveden zde:

![Zobrazení událostí neúspěšných přihlášení protokolu zabezpečení](./media/howto-mfa-nps-extension-vpn/image47.png)

Související události z protokolu Azure Multi-Factor Authentication je znázorněno zde:

![Protokoly služby Azure Multi-Factor Authentication](./media/howto-mfa-nps-extension-vpn/image48.png)

Uděláte to pokročilá řešení problémů, zkontrolujte soubory protokolů formátu databáze NPS nainstalovanou službu NPS. Soubory protokolu se vytvoří v _%SystemRoot%\System32\Logs_ složky jako soubory text oddělený čárkami. Popis souborů protokolu najdete v tématu [interpretovat NPS formátu soubory protokolů databáze](https://technet.microsoft.com/library/cc771748.aspx). 

Položky v těchto protokolových souborech obtížně interpretovat Pokud exportovat je do tabulkového procesoru nebo databázi. Můžete najít mnoho služby IAS (Internet Authentication) analýza nástroje online vám pomůže při interpretaci souborů protokolu. Výstup jednoho takové ke stažení [shareware aplikace](http://www.deepsoftware.com/iasviewer) je znázorněno zde: 

![Shareware aplikace](./media/howto-mfa-nps-extension-vpn/image49.png)

Pokud chcete provést další řešení potíží, můžete pomocí analyzátoru protokolu například Wireshark nebo [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx). Na následujícím obrázku z Wireshark zobrazí zprávy protokolu RADIUS mezi serverem VPN a na server NPS.

![Microsoft Message Analyzer](./media/howto-mfa-nps-extension-vpn/image50.png)

Další informace najdete v tématu [vaší stávající infrastruktury pro server NPS integrovat Azure Multi-Factor Authentication](howto-mfa-nps-extension.md). 

## <a name="next-steps"></a>Další postup
[Získání Azure Multi-Factor Authentication](concept-mfa-licensing.md)

[Brána vzdálené plochy Azure Multi-Factor Authentication Server pomocí protokolu RADIUS](howto-mfaserver-nps-rdg.md)

[Integrace místních adresářů do služby Azure Active Directory](../connect/active-directory-aadconnect.md)

