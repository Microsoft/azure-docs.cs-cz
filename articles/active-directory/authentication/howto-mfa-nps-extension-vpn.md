---
title: Integrace pomocí rozšíření serveru Network Policy Server VPN s Azure MFA | Dokumentace Microsoftu
description: Pomocí rozšíření serveru Network Policy Server pro Microsoft Azure integrate vaše infrastruktura sítě VPN s Azure MFA.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: f08bf6a65a93c31d76c5b336cc6ef433153d71c4
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52423113"
---
# <a name="integrate-your-vpn-infrastructure-with-azure-mfa-by-using-the-network-policy-server-extension-for-azure"></a>Integrovat vaše infrastruktura sítě VPN s Azure MFA pomocí rozšíření Server NPS pro Azure

## <a name="overview"></a>Přehled

Rozšíření serveru NPS (Network Policy Server) pro Azure umožňuje organizacím chránit pomocí cloudového ověřování klientů RADIUS Remote Authentication telefonického uživatele Service () [Azure Multi-Factor Authentication (MFA)](howto-mfaserver-nps-rdg.md), která zajišťuje dvoustupňové ověření.

Tento článek obsahuje pokyny k integraci infrastruktury NPS se používá služba MFA pomocí rozšíření NPS pro Azure. Tento proces umožní zabezpečený dvoustupňové ověřování pro uživatele, kteří se pokoušejí připojit k síti pomocí sítě VPN. 

Službu síťové zásady a přístup poskytuje organizacím možnost:

* Přiřaďte centrální umístění pro správu a řízení síťových požadavků k určení:

    * Kdo se může připojit 
    
    * Dobu připojení den jsou povoleny. 
    
    * Doba připojení
    
    * Úroveň zabezpečení, které musí klienti používat pro připojení

    Místo určení zásad na každém serveru sítě VPN nebo Brána vzdálené plochy, tak učinit po jejich v centrálním umístění. Protokol RADIUS slouží k poskytování centralizované ověřování, autorizaci a monitorování (AAA). 

* Vytvořit a vynucovat zásady stavu klienta ochrany NAP (Network Access), které určují, jestli zařízení jsou udělena neomezeného nebo omezeného přístupu k síťovým prostředkům.

* Poskytují způsob k vynucení ověřování a autorizace pro přístup k protokolu 802. 1 x podporuje bezdrátových přístupových bodů a přepínače sítě Ethernet.   
Další informace najdete v tématu [Network Policy Server](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top). 

Umožňuje zvýšit zabezpečení a poskytovat vysokou úroveň dodržování předpisů, organizace může integrovat NPS ověřování Azure Multi-Factor Authentication k zajištění, že uživatelé použít dvoustupňové ověřování pro připojení k virtuálnímu portu na serveru VPN. Pro uživatele, který má být udělen přístup musíte zadat svoje uživatelské jméno a hesla a další informace, které řídí. Tyto informace musí být důvěryhodné a není lehké. Může obsahovat číslo mobilního telefonu, číslo linky nebo aplikace na mobilním zařízení.

Před dostupnost rozšíření NPS pro Azure zákazníci, kteří chtěli implementace dvoustupňové ověřování pro integrovaný režim serveru NPS a prostředí MFA museli konfigurovat a spravovat samostatný server MFA v místním prostředí. Tento typ ověřování se nabízí tak, že Brána vzdálené plochy a Azure Multi-Factor Authentication Server pomocí protokolu RADIUS.

Pomocí rozšíření NPS pro Azure můžete organizací zabezpečit ověření klienta RADIUS nasazením řešení vícefaktorového ověřování na základě v místním nebo cloudovým řešením vícefaktorové ověřování.
 
## <a name="authentication-flow"></a>Tok ověřování
Když se uživatelé připojí na virtuální port na serveru VPN, musí nejdřív ověřit pomocí různých protokolů. Protokoly povolit použití kombinace uživatelského jména a hesla a metody ověřování založené na certifikátu. 

Kromě ověřování a ověření identity, uživatelé musí mít příslušná oprávnění. V jednoduché implementace telefonického připojení, které umožňují přístup jsou oprávnění přímo na objekty uživatele služby Active Directory. 

![Uživatelské vlastnosti](./media/howto-mfa-nps-extension-vpn/image1.png)

V jednoduché implementace každý server VPN udělí nebo odepře přístup na základě zásad, které jsou definovány pro každý místní server VPN.

V implementacích větší a větší škálovatelnost zásadách, které udělit nebo odepřít přístup k síti VPN jsou centralizované na servery RADIUS. V těchto případech se serveru VPN funguje jako server access (klienta protokolu RADIUS), který předá žádosti o připojení a účet zprávy na RADIUS server. Pro připojení k virtuálnímu portu na serveru VPN, uživatelé musí být ověřené a splňují podmínky, které jsou definované centrálně na servery RADIUS. 

Když rozšíření NPS pro Azure je integrovaná se serverem NPS výsledků toku úspěšné ověřování, následujícím způsobem:

1. Server sítě VPN obdrží požadavek na ověření od uživatele sítě VPN, která obsahuje uživatelské jméno a heslo pro připojení k prostředku, jako je například relaci vzdálené plochy. 

2. Funguje jako klienta protokolu RADIUS, VPN server převede požadavek na POLOMĚR *žádost o přístup* zprávy a odešle ji (s heslem zašifrovaných) na server RADIUS, instalaci rozšíření serveru NPS. 

3. Kombinace uživatelského jména a hesla je ověřit ve službě Active Directory. Pokud zadané uživatelské jméno nebo heslo nejsou správné, odešle RADIUS Server *odepření přístupu* zprávy. 

4. Pokud jsou splněny všechny podmínky, jak je uvedeno v žádosti o připojení serveru NPS a zásady sítě (například čas, den nebo skupiny omezeními pro členství vyplývajícími), aktivuje rozšíření NPS žádost pro sekundární ověřování pomocí Azure Multi-Factor Authentication. 

5. Azure Multi-Factor Authentication komunikuje se službou Azure Active Directory, načte podrobnosti daného uživatele a provádí sekundární ověření pomocí metody, která je nakonfigurovaná uživatelem (buňky telefonní hovor, textová zpráva nebo mobilní aplikace). 

6. Po úspěšné vícefaktorové ověřování Azure Multi-Factor Authentication komunikuje výsledek, který má rozšíření serveru NPS.

7. Po ověření i autorizace instalaci rozšíření serveru NPS odešle POLOMĚR *přijmout přístup* zprávy serveru sítě VPN (klienta protokolu RADIUS).

8. Uživateli je udělen přístup k virtuálnímu portu na serveru VPN a vytváří šifrovaného tunelu VPN.

## <a name="prerequisites"></a>Požadavky
Tato část podrobně popisuje předpoklady, které musíte splnit, předtím, než je možné integrovat MFA s Brána vzdálené plochy. Než začnete, musíte mít splněné následující požadavky na místě:

* Infrastrukturu sítě VPN
* Síťové zásady a přístup ke službám rolí
* Licence Azure Multi-Factor Authentication
* Software Windows serveru
* Knihovny
* Azure Active Directory (Azure AD) synchronizované s místní služby Active Directory 
* Identifikátor GUID služby Azure Active Directory

### <a name="vpn-infrastructure"></a>Infrastrukturu sítě VPN
Tento článek předpokládá, že máte vytvořit fungující infrastrukturu sítě VPN, který používá Microsoft Windows Server 2016 a že VPN server není aktuálně nakonfigurované přesměrování požadavků na připojení k serveru RADIUS. V následujícím článku je nakonfigurovat infrastrukturu sítě VPN pro použití centrálního serveru RADIUS.

Pokud není nutné vytvořit fungující infrastrukturu sítě VPN v místě, můžete rychle vytvořit podle pokynů v kurzech mnoho nastavení sítě VPN, které najdete na Microsoft a weby třetích stran. 
            
### <a name="the-network-policy-and-access-services-role"></a>Role služby Síťové zásady a přístup

Službu síťové zásady a přístup poskytuje funkce pro server a klienta RADIUS. Tento článek předpokládá, že je nainstalován síťové zásady a přístup ke službám role na členský server nebo řadič domény ve vašem prostředí. V této příručce konfigurace RADIUS pro konfiguraci sítě VPN. Nainstalujte roli služby Síťové zásady a přístup na server *jiné než* serveru VPN.

Informace o instalaci role Síťové zásady a přístup ke službám služby systému Windows Server 2012 nebo novější, najdete v článku [nainstalovat Server zásad stavu NAP](https://technet.microsoft.com/library/dd296890.aspx). Ve Windows serveru 2016 je architektura NAP zastaralá. Popis osvědčených postupů pro server NPS, včetně doporučení pro instalaci serveru NPS na řadiči domény, najdete v části [osvědčené postupy pro server NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-mfa-license"></a>Licence Azure MFA

Licence se vyžaduje pro ověřování Azure Multi-Factor Authentication, a je dostupná prostřednictvím Azure AD Premium, Enterprise Mobility + Security nebo samostatné licence služby Multi-Factor Authentication. Založenou na skutečné spotřebě licencí Azure MFA jako ověřování licencí vázaných na uživatele nebo nejsou kompatibilní s rozšíření serveru NPS. Další informace najdete v tématu [jak získat Azure Multi-Factor Authentication](concept-mfa-licensing.md). Pro účely testování můžete použít zkušební verzi předplatného.

### <a name="windows-server-software"></a>Software Windows serveru

Rozšíření NPS server vyžaduje Windows Server 2008 R2 SP1 nebo novější, s rolí služby Síťové zásady a přístup nainstalovat. Ve Windows serveru 2016 byly provedeny všechny kroky v této příručce.

### <a name="libraries"></a>Knihovny

Následující knihovny se automaticky instalují s rozšíření serveru NPS:

-   [Distribuovatelné balíčky Visual C++ pro Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-   [Microsoft Azure Active Directory modulu pro Windows PowerShell verze 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

Pokud se modul Microsoft Azure Active Directory PowerShell ještě není k dispozici, se instaluje s konfigurační skript, který spustí jako součást procesu instalace. Není nutné k instalaci modulu předem, pokud ještě není nainstalovaná.

### <a name="azure-active-directory-synced-with-on-premises-active-directory"></a>Azure Active Directory synchronizované s místní služby Active Directory 

Místní uživatelé musí použít rozšíření serveru NPS, synchronizaci se službou Azure Active Directory a povoleným vícefaktorovým Ověřováním. Tento průvodce to předpokládá, že místních uživatelů se synchronizují se službou Azure Active Directory prostřednictvím služby Azure AD Connect. Níže jsou uvedené pokyny pro povolení uživatelů pro MFA.

Informace o Azure AD Connect najdete v tématu [integrace místních adresářů se službou Azure Active Directory](../hybrid/whatis-hybrid-identity.md). 

### <a name="azure-active-directory-guid-id"></a>Identifikátor GUID služby Azure Active Directory 

Pokud chcete nainstalovat rozšíření serveru NPS, je potřeba vědět identifikátor GUID služby Azure Active Directory. V další části jsou k dispozici pokyny, jak najít identifikátor GUID služby Azure Active Directory.

## <a name="configure-radius-for-vpn-connections"></a>Konfigurace RADIUS pro připojení k síti VPN

Pokud jste nainstalovali NPS role na členský server, budete ho muset nakonfigurovat k ověřování a autorizaci klienta VPN této žádosti o připojení k síti VPN. 

V této části se předpokládá, že máte nainstalovanou síťové zásady a přístup ke službám rolí, ale nebyly konfigurován pro použití ve vaší infrastruktuře.

> [!NOTE]
> Pokud už máte pracovní serveru VPN, který používá centralizované server RADIUS pro ověřování, můžete tuto část přeskočit.
>

### <a name="register-server-in-active-directory"></a>Registrace serveru ve službě Active Directory
V tomto scénáři, fungovat správně, NPS server musí být zaregistrovaný ve službě Active Directory.

1. Otevřete správce serveru.

2. Ve Správci serveru vyberte **nástroje**a pak vyberte **Network Policy Server**. 

3. V konzole serveru Network Policy Server klikněte pravým tlačítkem na **server NPS (místní)** a pak vyberte **zaregistrovat server ve službě Active Directory**. Vyberte **OK** dvakrát.

    ![Network Policy Server](./media/howto-mfa-nps-extension-vpn/image2.png)

4. Nechte konzolu otevřený pro další postup.

### <a name="use-wizard-to-configure-the-radius-server"></a>Pomocí průvodce můžete nakonfigurovat server protokolu RADIUS
Můžete použít standard (založené na průvodci) nebo rozšířené možnosti konfigurace konfigurace serveru RADIUS. V této části se předpokládá, že používáte možnost založené na průvodci standardní konfigurace.

1. V konzole serveru Network Policy Server vyberte **server NPS (místní)**.

2. V části **standardní konfigurace**vyberte **Server RADIUS pro vytáčená nebo VPN připojení**a pak vyberte **konfigurace sítě VPN nebo telefonické**.

    ![Konfigurace sítě VPN](./media/howto-mfa-nps-extension-vpn/image3.png)

3. V **vyberte vytáčená nebo virtuální privátní typ síťového připojení** okna, vyberte **připojení virtuální privátní síti**a pak vyberte **Další**.

    ![Virtuální privátní sítě](./media/howto-mfa-nps-extension-vpn/image4.png)

4. V **zadejte telefonického připojení nebo VPN serveru** okně **přidat**.

5. V **klienta RADIUS nové** zadejte popisný název, zadejte rozlišitelný název nebo IP adresu serveru VPN a pak zadejte sdílený tajný. Ujistěte se, sdílený tajný klíč do dlouhá a složitá. Poznamenejte si ho, protože ho budete potřebovat v další části.

    ![Nový klient protokolu RADIUS](./media/howto-mfa-nps-extension-vpn/image5.png)

6. Vyberte **OK**a pak vyberte **Další**.

7. V **konfigurovat metody ověřování** okna, přijměte výchozí hodnotu (**šifrované ověření verze 2 [MS-CHAPv2])** nebo zvolte jinou možnost a vyberte **další** .

    > [!NOTE]
    > Při konfiguraci protokolu EAP (Extensible Authentication), musíte použít Microsoft Challenge Handshake Authentication Protocol (CHAPv2) nebo Protected Extensible Authentication Protocol (PEAP). Žádné EAP se nepodporuje.
 
8. V **určete skupiny uživatelů** okně **přidat**a pak vyberte příslušné skupiny. Pokud neexistuje žádná skupina, ponechte výběr prázdné, pokud chcete udělit přístup všem uživatelům.

    ![V okně určete skupiny uživatelů](./media/howto-mfa-nps-extension-vpn/image7.png)

9. Vyberte **Další**.

10. V **zadat filtry IP adres** okně **Další**.

11. V **zadat nastavení šifrování** okna, přijměte výchozí nastavení a pak vyberte **Další**.

    ![V okně zadejte nastavení šifrování](./media/howto-mfa-nps-extension-vpn/image8.png)

12. V **zadejte název sféry** název sféry ponechejte prázdné, přijměte výchozí nastavení a pak vyberte **Další**.

    ![Zadejte název sféry okna](./media/howto-mfa-nps-extension-vpn/image9.png)

13. V **klienty dokončení nové vytáčené připojení nebo připojení k virtuální privátní síti a protokolu RADIUS** okně **Dokončit**.

    ![V okně "dokončení nové vytáčené připojení nebo připojení virtuální privátní sítě a pomocí protokolu RADIUS klienti"](./media/howto-mfa-nps-extension-vpn/image10.png)

### <a name="verify-the-radius-configuration"></a>Ověření konfigurace RADIUS
Tato část obsahuje podrobnosti o konfiguraci, kterou jste vytvořili pomocí průvodce.

1. Na serveru zásad sítě, v konzole serveru NPS (místní) rozbalte **klientů RADIUS**a pak vyberte **klientů RADIUS**.

2. V podokně podrobností klikněte pravým tlačítkem na klienta RADIUS, který jste vytvořili a pak vyberte **vlastnosti**. Vlastnosti vašeho klienta protokolu RADIUS (VPN server) by měl být podobné těm je vidět tady:

    ![Vlastnosti sítě VPN](./media/howto-mfa-nps-extension-vpn/image11.png)

3. Vyberte **zrušit**.

4. Na serveru zásad sítě, v konzole serveru NPS (místní) rozbalte **zásady**a pak vyberte **zásady vyžádání nového připojení**. Připojení k síti VPN zásada se zobrazí, jak je znázorněno na následujícím obrázku:

    ![Požadavky na připojení](./media/howto-mfa-nps-extension-vpn/image12.png)

5. V části **zásady**vyberte **zásady sítě**. Měli byste vidět zásadu připojení virtuální privátní sítě (VPN), která vypadá podobně jako zásada je znázorněno na následujícím obrázku:

    ![Zásady sítě](./media/howto-mfa-nps-extension-vpn/image13.png)

## <a name="configure-your-vpn-server-to-use-radius-authentication"></a>Konfigurace sítě VPN serveru na použití ověřování pomocí protokolu RADIUS
V této části můžete nakonfigurovat VPN serveru na použití ověřování pomocí protokolu RADIUS. Pokyny předpokládají, že máte funkční konfigurace serveru VPN, ale nebyly nakonfigurované na používání ověřování pomocí protokolu RADIUS. Po dokončení konfigurace serveru VPN, potvrďte, že konfigurace funguje podle očekávání.

> [!NOTE]
> Pokud už máte funkční konfigurace serveru VPN, která používá ověřování pomocí protokolu RADIUS, můžete tuto část přeskočit.
>

### <a name="configure-authentication-provider"></a>Konfigurace zprostředkovatele ověřování
1. Na serveru VPN otevřete Správce serveru.

2. Ve Správci serveru vyberte **nástroje**a pak vyberte **směrování a vzdálený přístup**.

3. V **směrování a vzdálený přístup** okna, klikněte pravým tlačítkem na  **\<název serveru > (místní)** a pak vyberte **vlastnosti**.

    ![Okno Směrování a vzdálený přístup](./media/howto-mfa-nps-extension-vpn/image14.png)
 
4. V  **\<název serveru > (místní) vlastnosti** okna, vyberte **zabezpečení** kartu. 

5. Na **zabezpečení** ve skupině **zprostředkovatele ověřování**vyberte **ověřování pomocí protokolu RADIUS**a pak vyberte **konfigurovat**.

    ![Ověřování RADIUS](./media/howto-mfa-nps-extension-vpn/image15.png)
 
6. V **ověřování pomocí protokolu RADIUS** okně **přidat**.

7. V **přidat Server protokolu RADIUS** okno, postupujte takto:

    a. V **název serveru** , zadejte název nebo IP adresa serveru RADIUS, který jste nakonfigurovali v předchozím oddílu.

    b. Pro **sdílený tajný klíč**vyberte **změnu**a pak zadejte sdílený tajný, který jste vytvořili a poznamenali dříve.

    c. V **časový limit (sekundy)** , vyberte hodnotu z **30** prostřednictvím **60**.  
    Hodnota časového limitu je nutné vyčkat dostatečně dlouho na dokončení druhý ověřovací faktor.
 
    ![V okně Přidat Server protokolu RADIUS](./media/howto-mfa-nps-extension-vpn/image16.png)
 
8. Vyberte **OK**.

### <a name="test-vpn-connectivity"></a>Test připojení k síti VPN
V této části si ověřit, že klient VPN ověří a autorizuje serverem RADIUS, při pokusu o připojení k virtuálnímu portu sítě VPN. Pokyny předpokládají, že používáte Windows 10 jako klienta VPN. 

> [!NOTE]
> Pokud jste už nakonfigurovali klienta VPN pro připojení k serveru VPN a uložení nastavení, můžete přeskočit kroky týkající se konfigurace a ukládá se objekt připojení VPN.
>

1. Na klientském počítači VPN, vyberte **Start** tlačítko a pak vyberte **nastavení** tlačítko.

2. V **nastavení Windows** okně **síť a Internet**.

3. Vyberte **VPN**.

4. Vyberte **přidat připojení k síti VPN**.

5. V **přidat připojení k síti VPN** okno v **poskytovatele připojení VPN** vyberte **Windows (vestavěné)**, vyplňte zbývající pole, podle potřeby a pak vyberte **Uložit**. 

    ![V okně "Přidat připojení VPN."](./media/howto-mfa-nps-extension-vpn/image17.png)
 
6. Přejděte na **ovládací panely**a pak vyberte **Centrum sítí a sdílení**.

7. Vyberte **změnit nastavení adaptéru**.

    ![Změnit nastavení adaptéru](./media/howto-mfa-nps-extension-vpn/image18.png)

8. Klikněte pravým tlačítkem na připojení k síti VPN a pak vyberte **vlastnosti**. 

    ![Vlastnosti sítě VPN](./media/howto-mfa-nps-extension-vpn/image19.png)

9. V okně Vlastnosti sítě VPN, vyberte **zabezpečení** kartu. 

10. Na **zabezpečení** kartu, ujistěte se, že pouze **2 Version Microsoft CHAP (MS-CHAP v2)** je vybrané a pak vyberte **OK**.

    ![Možnost "Povolit tyto protokoly"](./media/howto-mfa-nps-extension-vpn/image20.png)

11. Klikněte pravým tlačítkem na připojení k síti VPN a pak vyberte **připojit**.

12. V **nastavení** okně **připojit**.  
    V případě úspěšného připojení zobrazí v protokolu zabezpečení na serveru RADIUS jako události 6272 ID, jak je znázorněno zde:

    ![V okně Vlastnosti události](./media/howto-mfa-nps-extension-vpn/image21.png)

## <a name="troubleshooting-radius"></a>Řešení potíží pomocí protokolu RADIUS

Předpokládejme, že konfigurace VPN fungovala předtím, než jste nakonfigurovali server sítě VPN určený centralizované server RADIUS pro ověřování a autorizaci. Pokud byla konfigurace funguje, je pravděpodobné, že tento problém je způsoben nesprávnou konfiguraci serveru RADIUS nebo použití neplatného uživatelského jména nebo hesla. Například pokud používáte alternativní přípona UPN v uživatelské jméno, pokus o přihlášení může selhat. Použijte stejný název účtu pro dosažení co nejlepších výsledků. 

Při řešení těchto problémů, je ideální místo, kde začít Zkontrolujte protokoly událostí zabezpečení na serveru RADIUS. Pokud chcete uložit, časem při hledání události, můžete na základě rolí síťové zásady a přístup k serveru vlastní zobrazení v prohlížeči událostí, jak je znázorněno zde. "ID události 6273" Určuje události, kde na server NPS byl odepřen přístup uživateli. 

![Prohlížeč událostí](./media/howto-mfa-nps-extension-vpn/image22.png)
 
## <a name="configure-multi-factor-authentication"></a>Konfigurace ověřování službou Multi-Factor Authentication

Pomoc uživatelům konfigurace ověřování službou Multi-Factor Authentication najdete v článcích [vyžadování dvoustupňového ověřování pro uživatele nebo skupinu](howto-mfa-userstates.md) a [nastavit účtu pro dvoustupňové ověřování.](../user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="install-and-configure-the-nps-extension"></a>Instalace a konfigurace rozšíření serveru NPS

Tato část obsahuje pokyny ke konfiguraci sítě VPN pro použít vícefaktorové ověřování pro ověření klienta se serverem VPN.

Po instalaci a konfiguraci rozšíření serveru NPS, je potřeba použít vícefaktorové ověřování při všech ověření klienta založené na protokolu RADIUS, který je tento server zpracovávat. Pokud všechny uživatele sítě VPN nejsou zaregistrované ve službě Azure Multi-Factor Authentication, můžete provést jeden z následujících akcí:

* Nastavení jiný server RADIUS pro ověřování uživatelů, kteří nejsou nakonfigurovány k používání vícefaktorového ověřování. 

* Vytvořte položku registru, který umožňuje němuž uživatelům poskytnout druhý ověřovací faktor, pokud jsou zaregistrované v Azure Multi-Factor Authentication. 

Vytvořte novou řetězcovou hodnotu s názvem _REQUIRE_USER_MATCH v HKLM\SOFTWARE\Microsoft\AzureMfa_a nastavte hodnotu na *True* nebo *False*. 

![Nastavení "Vyžadovat porovnání uživatele u služby"](./media/howto-mfa-nps-extension-vpn/image34.png)
 
Pokud je hodnota nastavena na *True* nebo je prázdný, všechny požadavky na ověření se vztahují ověřovacím testem MFA. Pokud je hodnota nastavena na *False*, vícefaktorové ověřování výzvy vydávají jenom na uživatele, kteří jsou zaregistrované v Azure Multi-Factor Authentication. Použití *False* během připojování období nastavení pouze při testování nebo v produkčním prostředí.

### <a name="obtain-the-azure-active-directory-guid-id"></a>Získat Identifikátor GUID služby Azure Active Directory

Jako součást konfigurace rozšíření serveru NPS je třeba zadat přihlašovací údaje správce a ID vašeho tenanta Azure AD. Získejte ID následujícím způsobem:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) jako globální správce tenanta Azure.

2. V levém podokně, vyberte **Azure Active Directory** tlačítko.

3. Vyberte **vlastnosti**.

4. Pokud chcete zkopírovat Azure AD ID, vyberte **kopírování** tlačítko.
 
    ![ID služby Azure AD](./media/howto-mfa-nps-extension-vpn/image35.png)

### <a name="install-the-nps-extension"></a>Instalace rozšíření serveru NPS
Rozšíření NPS musí nainstalovat na server, který má síťové zásady a nainstalovanou rolí služby pro přístup a, který funguje jako server protokolu RADIUS v návrhu. Proveďte *není* instalace rozšíření serveru NPS na serveru vzdálené plochy.

1. Stáhněte si rozšíření NPS z [Microsoft Download Center](https://aka.ms/npsmfa). 

2. Zkopírujte spustitelný soubor instalace (*NpsExtnForAzureMfaInstaller.exe*) na server NPS.

3. Na serveru NPS, klikněte dvakrát na **NpsExtnForAzureMfaInstaller.exe** a pokud se zobrazí výzva, vyberte **spustit**.

4. V **rozšíření NPS pro Azure MFA nastavení** okna, zkontrolujte licenční podmínky pro software, vyberte **vyjadřuji souhlas s licenčními podmínkami a ujednáními** zaškrtněte políčko a potom vyberte **nainstalovat**.

    ![V okně "NPS pro Azure MFA nastavení rozšíření"](./media/howto-mfa-nps-extension-vpn/image36.png)
 
5. V **rozšíření NPS pro Azure MFA nastavení** okně **Zavřít**.  

    ![Okno "Nastavení úspěšné" potvrzení](./media/howto-mfa-nps-extension-vpn/image37.png) 
 
### <a name="configure-certificates-for-use-with-the-nps-extension-by-using-a-powershell-script"></a>Konfigurace certifikátů pro použití s rozšíření NPS pomocí skriptu prostředí PowerShell
K zajištění zabezpečené komunikace a záruky, konfigurace certifikátů pro použití rozšíření serveru NPS. Server NPS součásti zahrnují skript prostředí Windows PowerShell, který se nakonfiguruje certifikát podepsaný svým držitelem pro použití se serverem NPS. 

Skript provede následující akce:

* Vytvoří certifikát podepsaný svým držitelem.
* Přidruží veřejný klíč certifikátu pro instanční objekt v Azure AD.
* Ukládá certifikát v úložišti místního počítače.
* Udělí uživateli přístup k privátnímu klíči certifikátu sítě.
* Restartuje službu NPS.

Pokud chcete použít vlastní certifikáty, musíte přidružit veřejný klíč certifikátu instančního objektu v Azure AD a tak dále.

Pokud chcete použít skript, poskytnout rozšíření správce přihlašovacích údajů Azure Active Directory a ID tenanta Azure Active Directory, který jste si zkopírovali dříve. Spuštění skriptu na každý server NPS, ve kterém nainstalujete rozšíření NPS.

1. Spusťte prostředí Windows PowerShell jako správce.

2. Příkazovém řádku prostředí PowerShell, zadejte **cd "c:\Program Files\Microsoft\AzureMfa\Config"** a pak stiskněte Enter.

3. Další příkazového řádku, zadejte **.\AzureMfsNpsExtnConfigSetup.ps1**a pak stiskněte Enter. Skript zkontroluje, zda je nainstalován modul Azure AD PowerShell. Pokud není nainstalovaná, skript nainstaluje modul pro vás.
 
    ![PowerShell](./media/howto-mfa-nps-extension-vpn/image38.png)
 
    Jakmile skript ověří instalace modulu prostředí PowerShell, zobrazí modulu přihlašovací okno Powershellu pro Azure Active Directory. 

4. Zadejte svoje přihlašovací údaje správce Azure AD a heslo a pak vyberte **přihlášení**. 
 
    ![Přihlašovací okno prostředí PowerShell](./media/howto-mfa-nps-extension-vpn/image39.png)
 
5. Na příkazovém řádku vložte ID tenanta, který jste si zkopírovali dříve a pak stiskněte Enter. 

    ![ID tenanta](./media/howto-mfa-nps-extension-vpn/image40.png)

    Tento skript vytvoří certifikát podepsaný svým držitelem a provede další změny v konfiguraci. Výstup je jako na následujícím obrázku:

    ![Certifikát podepsaný svým držitelem](./media/howto-mfa-nps-extension-vpn/image41.png)

6. Restartujte server.

### <a name="verify-the-configuration"></a>Ověření konfigurace
Pokud chcete ověřit konfiguraci, je potřeba vytvořit nové připojení VPN se serverem VPN. Po zadání svých přihlašovacích údajů úspěšně pro primární ověřování, počká, připojení k síti VPN pro sekundární ověřování úspěšné předtím, než se naváže připojení, jak je znázorněno níže. 

![V okně Windows Nastavení VPN](./media/howto-mfa-nps-extension-vpn/image42.png)

Pokud jste úspěšně ověřit pomocí sekundární ověřovací metody, kterou jste dříve nakonfigurovali v Azure MFA, jste připojení k prostředku. Pokud sekundární ověřování neproběhne úspěšně, ale je odepřen přístup k prostředku. 

Aplikace Microsoft Authenticator na Windows Phone v následujícím příkladu obsahuje sekundární ověřování:

![Ověření účtu](./media/howto-mfa-nps-extension-vpn/image43.png)

Poté, co byli jste úspěšně ověřeni pomocí sekundární metody, je udělen přístup k virtuální port na serveru VPN. Vzhledem k tomu, že se budete muset použít metodu sekundární ověření pomocí mobilní aplikace na důvěryhodné zařízení, je bezpečnější než pokud používali jenom kombinace uživatelského jména a hesla proces přihlašování.

### <a name="view-event-viewer-logs-for-successful-sign-in-events"></a>Zobrazit protokoly Prohlížeče událostí pro události úspěšného přihlášení
Pokud chcete zobrazit události úspěšného přihlášení v protokolech prohlížeče událostí Windows, protokolu dotazu zabezpečení Windows na serveru NPS zadáním následujícího příkazu Powershellu:

    _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL 

![Zabezpečení prostředí PowerShell, Prohlížeč událostí](./media/howto-mfa-nps-extension-vpn/image44.png)
 
Můžete také zobrazit v protokolu zabezpečení nebo vlastní zobrazení síťové zásady a přístup ke službám, jak je znázorněno zde:

![Protokol serveru zásad sítě](./media/howto-mfa-nps-extension-vpn/image45.png)

Na serveru, kam jste nainstalovali rozšíření NPS pro Azure Multi-Factor Authentication, můžete najít protokoly Prohlížeče událostí aplikace, které jsou specifické pro rozšíření na *aplikace a služby Logs\Microsoft\AzureMfa*. 

    _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL

![Prohlížeč událostí "Počet událostí" podokno](./media/howto-mfa-nps-extension-vpn/image46.png)

## <a name="troubleshooting-guide"></a>Průvodce odstraňováním potíží
Pokud konfiguraci nefunguje podle očekávání, začněte řešit potíže tak, že ověříte, že je nakonfigurován na použití vícefaktorového ověřování. Požádejte uživatele, připojte se k [webu Azure portal](https://portal.azure.com). Pokud uživatel vyzván k zadání sekundární ověřování a můžete úspěšně ověřit, můžete eliminovat nesprávnou konfiguraci vícefaktorového ověřování jako chyby.

Pokud vícefaktorové ověřování funguje pro uživatele, přečtěte si relevantní protokoly Prohlížeče událostí. Protokoly obsahovat zabezpečení, provozní brány, protokoly událostí a ověřování Azure Multi-Factor Authentication, které jsou popsané v předchozí části. 

Zde je uveden příklad protokolu zabezpečení, který se zobrazí neúspěšných přihlášení událostí (událost ID 6273):

![Protokol zabezpečení znázorňující neúspěšné událostí přihlášení](./media/howto-mfa-nps-extension-vpn/image47.png)

Související události z protokolu ověřování Azure Multi-Factor Authentication je znázorněna zde:

![Protokoly služby Azure Multi-Factor Authentication](./media/howto-mfa-nps-extension-vpn/image48.png)

Postup řešení potíží pro pokročilé zkontrolujte soubory protokolu formátu databáze serveru NPS ve kterém je nainstalovaná služba NPS. Soubory protokolu se vytvoří v _%SystemRoot%\System32\Logs_ složky jako soubory text oddělený čárkami. Popis souborů protokolu najdete v tématu [interpretovat NPS formátu soubory protokolů databáze](https://technet.microsoft.com/library/cc771748.aspx). 

Položky v těchto souborech protokolu jsou obtížně interpretovat, ledaže byste je vyexportovali pro tabulku nebo databázi. Můžete najít mnoho služby IAS (Internet Authentication) analýza online nástroje pro pomoc při interpretaci souborů protokolu. Výstup těchto ke stažení [shareware aplikace](https://www.deepsoftware.com/iasviewer) je znázorněna zde: 

![Shareware aplikace](./media/howto-mfa-nps-extension-vpn/image49.png)

Chcete-li provést další řešení potíží, můžete pomocí analyzátoru protokolu například Wireshark nebo [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx). Na následujícím obrázku z Wireshark zobrazuje zprávy pomocí protokolu RADIUS mezi serverem VPN a na server NPS.

![Microsoft Message Analyzer](./media/howto-mfa-nps-extension-vpn/image50.png)

Další informace najdete v tématu [integrace vaší stávající infrastruktury NPS pomocí ověřování Azure Multi-Factor Authentication](howto-mfa-nps-extension.md). 

## <a name="next-steps"></a>Další postup
[Získat Azure Multi-Factor Authentication](concept-mfa-licensing.md)

[Brána vzdálené plochy Azure Multi-Factor Authentication Server pomocí protokolu RADIUS](howto-mfaserver-nps-rdg.md)

[Integrace místních adresářů do služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md)

