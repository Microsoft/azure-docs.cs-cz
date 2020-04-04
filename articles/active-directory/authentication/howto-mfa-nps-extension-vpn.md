---
title: VPN s Azure MFA pomocí rozšíření NPS – Azure Active Directory
description: Integrujte infrastrukturu VPN s Azure MFA pomocí rozšíření Network Policy Server pro Microsoft Azure.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec8d5b66c71c558e56f3d1f48cec96d7cc487552
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654131"
---
# <a name="integrate-your-vpn-infrastructure-with-azure-mfa-by-using-the-network-policy-server-extension-for-azure"></a>Integrace infrastruktury VPN s Azure MFA pomocí rozšíření Network Policy Server pro Azure

Rozšíření server NPS (Network Policy Server) pro Azure umožňuje organizacím chránit ověřování klientů služby RADIUS (Remote Authentication Dial-In User Service) pomocí cloudového [vícefaktorového ověřování Azure (MFA),](howto-mfaserver-nps-rdg.md)které poskytuje dvoustupňové ověření.

Tento článek obsahuje pokyny pro integraci infrastruktury NPS s vícefaktorové pomocí rozšíření NPS pro Azure. Tento proces umožňuje zabezpečené dvoustupňové ověření pro uživatele, kteří se pokoušejí připojit k síti pomocí sítě VPN.

Služby zásad a přístupu k síti poskytují organizacím možnost:

* Přiřaďte centrální umístění pro správu a řízení síťových požadavků, které určí:

  * Kdo se může připojit

  * Jaké denní časy jsou povoleny

  * Doba trvání připojení

  * Úroveň zabezpečení, kterou musí klienti použít k připojení

    Místo aby určovali zásady na každém serveru VPN nebo Služby vzdálené služby, učiňte tak poté, co se nacházejí v centrálním umístění. Protokol RADIUS se používá k poskytování centralizovaného ověřování, autorizace a účtování (AAA).

* Vytvořte a vynucujte zásady stavu klientů ochrany přístupu k síti (NAP), které určují, zda je zařízením udělen neomezený nebo omezený přístup k síťovým prostředkům.

* Poskytněte způsob, jak vynutit ověřování a autorizaci pro přístup k bezdrátovým přístupovým bodům 802.1x a přepínačům Ethernet.
  Další informace naleznete v [tématu Network Policy Server](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top).

Pro zvýšení zabezpečení a zajištění vysoké úrovně dodržování předpisů mohou organizace integrovat server NPS s vícefaktorovým ověřováním Azure, aby zajistily, že uživatelé používají dvoustupňové ověřování pro připojení k virtuálnímu portu na serveru VPN. Aby byl uživatelům udělen přístup, musí poskytnout kombinaci uživatelského jména a hesla a další informace, které řídí. Tyto informace musí být důvěryhodné a nesmí být snadno duplikovány. Může obsahovat číslo mobilního telefonu, číslo pevné linky nebo aplikaci na mobilním zařízení.

Před dostupností rozšíření NPS pro Azure museli zákazníci, kteří chtěli implementovat dvoustupňové ověření pro integrovaná prostředí NPS a MFA, nakonfigurovat a udržovat samostatný server MFA v místním prostředí. Tento typ ověřování nabízí brána vzdálené plochy a vícefaktorový ověřovací server Azure pomocí protokolu RADIUS.

S rozšířením NPS pro Azure mohou organizace zabezpečit ověřování klientů RADIUS nasazením místního řešení MFA nebo cloudového řešení MFA.

## <a name="authentication-flow"></a>Tok ověřování

Když se uživatelé připojí k virtuálnímu portu na serveru VPN, musí se nejprve ověřit pomocí různých protokolů. Protokoly umožňují použití kombinace uživatelského jména a hesla a metod ověřování založených na certifikátech.

Kromě ověřování a ověřování své identity musí mít uživatelé příslušná oprávnění k telefonickému připojení. V jednoduchých implementacích jsou oprávnění pro telefonické připojení, která umožňují přístup, nastavena přímo na objektech uživatelů služby Active Directory.

![Karta Vytáčené připojení ve vlastnostech uživatele uživatelů a počítačů služby Active Directory](./media/howto-mfa-nps-extension-vpn/image1.png)

V jednoduchých implementacích každý server VPN uděluje nebo zakazuje přístup na základě zásad, které jsou definovány na každém místním serveru VPN.

Ve větších a škálovatelných implementacích jsou zásady, které udělují nebo odepírají přístup vpn, centralizovány na serverech RADIUS. V těchto případech server VPN funguje jako server pro přístup (klient RADIUS), který předává požadavky na připojení a zprávy o účtu serveru RADIUS. Chcete-li se připojit k virtuálnímu portu na serveru VPN, musí být uživatelé ověřeni a splňovat podmínky, které jsou definovány centrálně na serverech RADIUS.

Když je rozšíření NPS pro Azure integrované s serverem NPS, výsledkem úspěšného toku ověřování jsou následující:

1. Server VPN obdrží požadavek na ověření od uživatele sítě VPN, který obsahuje uživatelské jméno a heslo pro připojení k prostředku, například relaci vzdálené plochy.
2. Server VPN, který funguje jako klient RADIUS, převede požadavek na zprávu *RADIUS Access-Request* a odešle ji (se šifrovaným heslem) na server RADIUS, na kterém je nainstalováno rozšíření NPS.
3. Kombinace uživatelského jména a hesla je ověřena ve službě Active Directory. Pokud je uživatelské jméno nebo heslo nesprávné, server RADIUS odešle zprávu *o odmítnutí přístupu.*
4. Pokud jsou splněny všechny podmínky, jak je uvedeno v požadavku na připojení nps a zásady sítě (například denní doba nebo omezení členství ve skupině), rozšíření NPS aktivuje požadavek na sekundární ověřování pomocí azure multi-factor authentication.
5. Azure Multi-Factor Authentication komunikuje s Azure Active Directory, načte podrobnosti o uživateli a provádí sekundární ověřování pomocí metody, která je nakonfigurovaná uživatelem (mobilní telefon volání, textové zprávy nebo mobilní aplikace).
6. Když je výzva mfa úspěšná, Azure Multi-Factor Authentication sdělí výsledek rozšíření NPS.
7. Po ověření i autorizaci pokusu o připojení odešle server NPS, kde je rozšíření nainstalováno, serveru VPN (klient RADIUS) zprávu *RADIUS Access-Accept.*
8. Uživateli je udělen přístup k virtuálnímu portu na serveru VPN a vytvoří šifrovaný tunel VPN.

## <a name="prerequisites"></a>Požadavky

Tato část podrobně popisuje požadavky, které musí být dokončeny před integrací vícefaktorové ověřování s VPN. Než začnete, musíte mít následující požadavky na místě:

* Infrastruktura VPN
* Role zásad a přístupových služeb sítě
* Licence azure multifaktorové ověřování
* Software Windows Server
* Knihovny
* Azure Active Directory (Azure AD) synchronizované s místní službou Active Directory
* Azure Active Directory GUID ID

### <a name="vpn-infrastructure"></a>Infrastruktura VPN

Tento článek předpokládá, že máte funkční infrastrukturu VPN, která používá Microsoft Windows Server 2016 a že váš server VPN není aktuálně nakonfigurován tak, aby přesměroval požadavky na připojení na server RADIUS. V článku nakonfigurujete infrastrukturu VPN tak, aby používala centrální server RADIUS.

Pokud nemáte funkční infrastrukturu VPN na místě, můžete ji rychle vytvořit podle pokynů v mnoha kurzech nastavení VPN, které můžete najít na stránkách společnosti Microsoft a třetích stran.

### <a name="the-network-policy-and-access-services-role"></a>Role Zásady sítě a přístupové služby

Služba Zásady sítě a přístup poskytuje server RADIUS a funkce klienta. Tento článek předpokládá, že jste nainstalovali roli Síťové zásady a přístupové služby na členský server nebo řadič domény ve vašem prostředí. V této příručce nakonfigurujete radius pro konfiguraci sítě VPN. Nainstalujte roli Síťové zásady a služby Access Services na jiný server *než* server VPN.

Informace o instalaci služby role Zásady sítě a služby Access Services windows server 2012 nebo novější naleznete [v tématu Instalace serveru zásad stavu navštovu](https://technet.microsoft.com/library/dd296890.aspx). Nap je v systému Windows Server 2016 již zastaralé. Popis doporučených postupů pro server NPS, včetně doporučení k instalaci serveru NPS do řadiče domény, naleznete v [tématu Doporučené postupy pro server NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-mfa-license"></a>Licence Azure MFA

Licence je vyžadována pro azure multi-factor authentication a je dostupná prostřednictvím Azure AD Premium, Enterprise Mobility + Security nebo samostatná licence vícefaktorového ověřování. Licence založené na spotřebě pro Azure MFA, například pro uživatele nebo ověřování licencí nejsou kompatibilní s rozšířením NPS. Další informace najdete v tématu [Jak získat Azure Multi-Factor Authentication](concept-mfa-licensing.md). Pro účely testování můžete použít zkušební předplatné.

### <a name="windows-server-software"></a>Software Windows Server

Rozšíření NPS vyžaduje aktualizaci Windows Server 2008 R2 SP1 nebo novější s nainstalovanou rolí Network Policy and Access Services. Všechny kroky v této příručce byly provedeny se systémem Windows Server 2016.

### <a name="libraries"></a>Knihovny

S rozšířením NPS se automaticky instalují následující knihovny:

-    [Redistribuovatelné balíčky Visual C++ pro Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-    [Modul Microsoft Azure Active Directory pro Prostředí Windows PowerShell verze 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

Pokud modul Prostředí Microsoft Azure Active Directory PowerShell ještě není k dispozici, nainstaluje se pomocí konfiguračního skriptu, který spustíte v rámci procesu instalace. Není třeba modul instalovat předem, pokud již není nainstalován.

### <a name="azure-active-directory-synced-with-on-premises-active-directory"></a>Služba Azure Active Directory synchronizovaná s místní službou Active Directory

Chcete-li použít rozšíření NPS, místní uživatelé musí být synchronizovány s Azure Active Directory a povolena pro vícefaktorové informace. Tato příručka předpokládá, že místní uživatelé jsou synchronizovány s Azure Active Directory prostřednictvím Azure AD Connect. Pokyny pro povolení uživatelů pro vícefaktorové informace jsou uvedeny níže.

Informace o Azure AD Connect najdete [v tématu Integrace místních adresářů s Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory GUID ID

Chcete-li nainstalovat rozšíření NPS, musíte znát identifikátor GUID služby Azure Active Directory. Pokyny pro nalezení identifikátoru GUID služby Azure Active Directory jsou uvedeny v další části.

## <a name="configure-radius-for-vpn-connections"></a>Konfigurace protokolu RADIUS pro připojení VPN

Pokud jste nainstalovali roli NPS na členský server, je třeba ji nakonfigurovat tak, aby ověřovala a autorizovala klienta VPN, který požaduje připojení VPN. 

Tato část předpokládá, že jste nainstalovali roli Síťové zásady a přístupové služby, ale nenakonfigurovali jste ji pro použití v infrastruktuře.

> [!NOTE]
> Pokud již máte funkční server VPN, který používá centralizovaný server RADIUS pro ověřování, můžete tuto část přeskočit.
>

### <a name="register-server-in-active-directory"></a>Registrace serveru ve službě Active Directory

Chcete-li v tomto scénáři správně fungovat, musí být server NPS zaregistrován ve službě Active Directory.

1. Spusťte Správce serveru.

2. Ve Správci serveru vyberte **nástroje**a potom vyberte **server zásad sítě**.

3. V konzole Server zásad síťových zásad klepněte pravým tlačítkem myši na položku **NPS (Local)** a potom vyberte **příkaz Registrovat server ve službě Active Directory**. Vyberte **OK** dvakrát.

    ![Možnost Zaregistrovat server v nabídce služby Active Directory](./media/howto-mfa-nps-extension-vpn/image2.png)

4. Nechte konzolu otevřenou pro další postup.

### <a name="use-wizard-to-configure-the-radius-server"></a>Konfigurace serveru RADIUS pomocí průvodce

Ke konfiguraci serveru RADIUS můžete použít standardní (založenou na průvodci) nebo upřesňující konfiguraci. Tato část předpokládá, že používáte možnost standardní konfigurace založené na průvodci.

1. V konzole Server síťových zásad vyberte **možnost NPS (Local).**

2. V části **Standardní konfigurace**vyberte možnost RADIUS Server **pro telefonické připojení nebo připojení VPN**a pak vyberte Konfigurovat síť VPN nebo **telefonické připojení**.

    ![Konfigurace serveru RADIUS pro telefonická nebo síť VPN](./media/howto-mfa-nps-extension-vpn/image3.png)

3. V okně **Vybrat telefonická nebo virtuální privátní síťová připojení** vyberte **položku Připojení virtuální privátní sítě**a pak vyberte **Další**.

    ![Konfigurace připojení k virtuální privátní síti](./media/howto-mfa-nps-extension-vpn/image4.png)

4. V okně **Zadat telefonické připojení nebo server VPN** vyberte **přidat**.

5. V okně **Klient a klient Nový radius** zadejte popisný název, zadejte název nebo adresu IP serveru VPN a zadejte sdílené tajné heslo. Nastavení sdíleného tajného hesla pro dlouhé a složité. Zaznamenejte to, protože budete potřebovat v další části.

    ![Vytvoření nového okna klienta RADIUS](./media/howto-mfa-nps-extension-vpn/image5.png)

6. Vyberte **OK**a pak vyberte **Další**.

7. V okně **Konfigurovat metody ověřování** přijměte výchozí výběr **(Microsoft Encrypted Authentication verze 2 [MS-CHAPv2])** nebo zvolte jinou možnost a vyberte **další**.

    > [!NOTE]
    > Pokud nakonfigurujete protokol EAP (ExTENSIBLE Authentication Protocol), musíte použít protokol CHAPv2 (ChAPv2) (ExTENSIBLE AUTHENTICATION PROTOCOL) společnosti Microsoft Challenge-Handshake nebo protected extensible authentication Protocol (PEAP). Žádný jiný eAP je podporován.

8. V okně **Určit skupiny uživatelů** vyberte **Přidat**a pak vyberte příslušnou skupinu. Pokud žádná skupina neexistuje, ponechejte výběr prázdný, aby byl přístup k přístupu všem uživatelům.

    ![Chcete-li povolit nebo odepřít přístup, chcete-li povolit nebo odepřít přístup, zadat okno Skupiny uživatelů.](./media/howto-mfa-nps-extension-vpn/image7.png)

9. Vyberte **další**.

10. V okně **Zadat filtry IP** vyberte **Další**.

11. V okně **Zadat nastavení šifrování** přijměte výchozí nastavení a pak vyberte **Další**.

    ![Okno Zadat nastavení šifrování](./media/howto-mfa-nps-extension-vpn/image8.png)

12. V okně **Zadejte název sféry** ponechte název sféry prázdný, přijměte výchozí nastavení a pak vyberte **Další**.

    ![Okno Zadat název sféry](./media/howto-mfa-nps-extension-vpn/image9.png)

13. V okně **Dokončení nových telefonických připojení nebo připojení virtuální privátní sítě a klientů RADIUS** vyberte možnost **Dokončit**.

    ![Dokončené konfigurační okno](./media/howto-mfa-nps-extension-vpn/image10.png)

### <a name="verify-the-radius-configuration"></a>Ověření konfigurace RADIUS

V této části je podrobně uveden název konfigurace, kterou jste vytvořili pomocí průvodce.

1. Na serveru zásad sítě v konzole NPS (local) rozbalte **klienty RADIUS**a vyberte **klienty RADIUS**.

2. V podokně podrobností klepněte pravým tlačítkem myši na klienta RADIUS, který jste vytvořili, a vyberte příkaz **Vlastnosti**. Vlastnosti klienta RADIUS (server VPN) by měly být podobné těm, které jsou zde uvedeny:

    ![Ověření vlastností a konfigurace sítě VPN](./media/howto-mfa-nps-extension-vpn/image11.png)

3. Vyberte **Zrušit**.

4. Na serveru zásad sítě v konzole NPS (místní) rozbalte **položku Zásady**a vyberte možnost **Zásady požadavků na připojení**. Zásady připojení VPN se zobrazí tak, jak je znázorněno na následujícím obrázku:

    ![Zásady požadavku na připojení zobrazující zásady připojení VPN](./media/howto-mfa-nps-extension-vpn/image12.png)

5. V části **Zásady**vyberte **položku Zásady sítě**. Měli byste vidět zásady připojení virtuální privátní sítě (VPN), která se podobá zásadám zobrazeným na následujícím obrázku:

    ![Zásady sítě zobrazující zásady připojení virtuální privátní sítě](./media/howto-mfa-nps-extension-vpn/image13.png)

## <a name="configure-your-vpn-server-to-use-radius-authentication"></a>Konfigurace serveru VPN pro použití ověřování RADIUS

V této části nakonfigurujete server VPN tak, aby používal ověřování RADIUS. Pokyny předpokládají, že máte funkční konfiguraci serveru VPN, ale nenakonfigurovali jej tak, aby používal ověřování RADIUS. Po konfiguraci serveru VPN zkontrolujte, zda konfigurace funguje podle očekávání.

> [!NOTE]
> Pokud již máte funkční konfiguraci serveru VPN, která používá ověřování RADIUS, můžete tuto část přeskočit.
>

### <a name="configure-authentication-provider"></a>Konfigurace zprostředkovatele ověřování

1. Na serveru VPN otevřete Správce serveru.

2. Ve Správci serveru vyberte **Nástroje**a pak vyberte **Možnost Směrování a vzdálený přístup**.

3. V okně **Směrování a vzdálený přístup** klepněte pravým tlačítkem myši na ** \<název serveru> (místní)** a potom vyberte příkaz **Vlastnosti**.

4. V okně ** \<název serveru> (místní) vlastnosti** vyberte kartu **Zabezpečení.**

5. Na kartě **Zabezpečení** vyberte v části **Zprostředkovatel ověřování**možnost **Radius Authentication**a potom vyberte **Konfigurovat**.

    ![Konfigurace zprostředkovatele ověřování RADIUS](./media/howto-mfa-nps-extension-vpn/image15.png)

6. V okně **Ověřování RADIUS** vyberte **Přidat**.

7. V okně **Přidat server RADIUS** postupujte takto:

    a. Do pole **Název serveru** zadejte název nebo adresu IP serveru RADIUS, který jste nakonfigurovali v předchozí části.

    b. U **sdíleného tajného klíče**vyberte **změnit**a zadejte sdílené tajné heslo, které jste vytvořili a zaznamenali dříve.

    c. Do pole **Časový výstraž (v sekundách)** zadejte hodnotu **30**.  
    Hodnota časového času je nezbytná k tomu, aby byl dostatek času k dokončení druhého faktoru ověřování.

    ![Přidání okna serveru RADIUS, které konfiguruje časový výtok](./media/howto-mfa-nps-extension-vpn/image16.png)

8. Vyberte **OK**.

### <a name="test-vpn-connectivity"></a>Testování připojení VPN

V této části potvrzujete, že klient VPN je ověřen a autorizován serverem RADIUS při pokusu o připojení k virtuálnímu portu VPN. Pokyny předpokládají, že používáte systém Windows 10 jako klienta VPN.

> [!NOTE]
> Pokud jste již nakonfigurovali klienta VPN pro připojení k serveru VPN a uložili nastavení, můžete přeskočit kroky související s konfigurací a uložením objektu připojení VPN.
>

1. V klientském počítači VPN vyberte tlačítko **Start** a pak vyberte tlačítko **Nastavení.**

2. V okně **Nastavení systému Windows** vyberte **položku Síťová & Internet**.

3. Vyberte **možnost VPN**.

4. Vyberte **Přidat připojení VPN**.

5. V okně **Přidat připojení VPN** vyberte v poli zprostředkovatel sítě **VPN** systém **Windows (vestavěný),** vyplňte zbývající pole podle potřeby a pak vyberte **Uložit**.

    ![Okno "Přidat připojení VPN"](./media/howto-mfa-nps-extension-vpn/image17.png)

6. Přejděte do **Ovládacích panelů**a vyberte **Centrum sítě a sdílení**.

7. Vyberte **Změnit nastavení adaptéru**.

    ![Centrum síťových připojení a sdílení – změna nastavení adaptéru](./media/howto-mfa-nps-extension-vpn/image18.png)

8. Klepněte pravým tlačítkem myši na síťové připojení VPN a vyberte příkaz **Vlastnosti**.

9. V okně vlastností VPN vyberte kartu **Zabezpečení.**

10. Na kartě **Zabezpečení** zkontrolujte, zda je vybrána pouze možnost **Microsoft CHAP verze 2 (MS-CHAP v2),** a pak vyberte **ok**.

    ![Možnost "Povolit tyto protokoly"](./media/howto-mfa-nps-extension-vpn/image20.png)

11. Klikněte pravým tlačítkem myši na připojení VPN a potom vyberte **připojit**.

12. V okně **Nastavení** vyberte **Připojit**.  
    Úspěšné připojení se zobrazí v protokolu zabezpečení na serveru RADIUS jako ID události 6272, jak je znázorněno zde:

    ![Okno Vlastnosti události zobrazující úspěšné připojení](./media/howto-mfa-nps-extension-vpn/image21.png)

## <a name="troubleshooting-radius"></a>Poradce při potížích radius

Předpokládejme, že vaše konfigurace sítě VPN fungovala před konfigurací serveru VPN tak, aby používal centralizovaný server RADIUS pro ověřování a autorizaci. Pokud konfigurace fungovala, je pravděpodobné, že problém je způsoben chybnou konfigurací serveru RADIUS nebo použitím neplatného uživatelského jména nebo hesla. Pokud například použijete alternativní příponu hlavního názvu uživatele v uživatelském jménu, může se nezdaří pokus o přihlášení. Pro dosažení nejlepších výsledků použijte stejný název účtu.

Chcete-li tyto problémy vyřešit, je ideálním místem pro spuštění prozkoumání protokolů událostí zabezpečení na serveru RADIUS. Chcete-li ušetřit čas při hledání událostí, můžete použít vlastní zobrazení zásad y sítě a přístupového serveru v Prohlížeči událostí, jak je znázorněno zde. "ID události 6273" označuje události, kdy server NPS odepřel uživateli přístup.

![Prohlížeč událostí zobrazující události NPAS](./media/howto-mfa-nps-extension-vpn/image22.png)

## <a name="configure-multi-factor-authentication"></a>Konfigurace vícefaktorového ověřování

Pomoc s konfigurací uživatelů pro vícefaktorové ověřování najdete v článcích [Plánování nasazení vícefaktorového ověřování Azure](howto-mfa-getstarted.md#create-conditional-access-policy) na základě cloudu a Nastavení účtu pro [dvoustupňové ověřování.](../user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="install-and-configure-the-nps-extension"></a>Instalace a konfigurace rozšíření serveru NPS

Tato část obsahuje pokyny pro konfiguraci sítě VPN pro použití vícefaktorové ověřování pro ověřování klientů se serverem VPN.

Po instalaci a konfiguraci rozšíření nps je pro použití vícefaktorového přístupu vyžadováno veškeré ověřování klientů založené na protokolu RADIUS, které je zpracováno tímto serverem. Pokud všichni vaši uživatelé VPN nejsou zaregistrovaní v Azure Multi-Factor Authentication, můžete udělat jednu z následujících akcí:

* Nastavte jiný server RADIUS pro ověřování uživatelů, kteří nejsou nakonfigurováni pro použití vícefaktorového ověřování.

* Vytvořte položku registru, která umožňuje postiženým uživatelům poskytnout druhý faktor ověřování, pokud jsou zaregistrovaní v Azure Multi-Factor Authentication.

Vytvořte novou hodnotu řetězce s názvem _REQUIRE_USER_MATCH v HKLM\SOFTWARE\Microsoft\AzureMfa_a nastavte hodnotu na *Hodnotu True* nebo *False*.

![Nastavení Vyžadovat shodu uživatelů](./media/howto-mfa-nps-extension-vpn/image34.png)

Pokud je hodnota nastavena na *hodnotu True* nebo je prázdná, všechny požadavky na ověření jsou předmětem výzvy vícefaktorové ověřování. Pokud je hodnota nastavena na *False*, výzvy vícefaktorového ověřování jsou vydávány pouze uživatelům, kteří jsou zaregistrovaní v Azure Multi-Factor Authentication. Nastavení *False* používejte pouze v testovacím prostředí nebo v produkčním prostředí během doby registrace.

### <a name="obtain-the-azure-active-directory-guid-id"></a>Získání Identifikátoru GUID služby Azure Active Directory

Jako součást konfigurace rozšíření NPS je nutné zadat přihlašovací údaje správce a ID vašeho klienta Azure AD. Id získáte následujícím způsobem:

1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako globální správce klienta Azure.

2. V nabídce Portál Azure vyberte **Azure Active Directory**nebo vyhledejte a vyberte Azure Active **Directory** z libovolné stránky.

3. Vyberte **vlastnosti**.

4. Pokud chcete zkopírovat ID Azure AD, vyberte tlačítko **Kopírovat.**

    ![ID adresáře Azure AD na webu Azure Portal](./media/howto-mfa-nps-extension-vpn/azure-active-directory-id-in-azure-portal.png)

### <a name="install-the-nps-extension"></a>Instalace rozšíření NPS

Rozšíření serveru NPS musí být nainstalováno na serveru, který má nainstalovanou roli Síťové zásady a přístupová služba a který funguje jako server RADIUS v návrhu. Neinstalujte rozšíření NPS na server VPN. *not*

1. Stáhněte si rozšíření serveru NPS ze [služby Stažení softwaru](https://aka.ms/npsmfa).

2. Zkopírujte spustitelný soubor instalace (*NpsExtnForAzureMfaInstaller.exe*) na server NPS.

3. Na serveru NPS poklepejte na **položku NpsExtnForAzureMfaInstaller.exe** a po zobrazení výzvy **vyberte**spustit .

4. V okně **Nastavení služby Azure MFA rozšíření serveru NPS** zkontrolujte licenční podmínky softwaru, zaškrtněte políčko **Souhlasím s licenčními podmínkami** a pak vyberte **Instalovat**.

    ![Okno "Rozšíření NPS pro nastavení Azure MFA"](./media/howto-mfa-nps-extension-vpn/image36.png)

5. V okně **Nastavení rozšíření serveru NPS pro Azure MFA** vyberte **zavřít**.  

    ![Potvrzovací okno Instalace byla úspěšná.](./media/howto-mfa-nps-extension-vpn/image37.png)

### <a name="configure-certificates-for-use-with-the-nps-extension-by-using-a-powershell-script"></a>Konfigurace certifikátů pro použití s rozšířením NPS pomocí skriptu prostředí PowerShell

Chcete-li zajistit zabezpečenou komunikaci a jistotu, nakonfigurujte certifikáty pro použití v rozšíření serveru NPS. Součásti serveru NPS obsahují skript prostředí Windows PowerShell, který konfiguruje certifikát podepsaný svým držitelem pro použití se serverem NPS.

Skript provádí následující akce:

* Vytvoří certifikát podepsaný svým držitelem.
* Přidruží veřejný klíč certifikátu k instančnímu objektu ve službě Azure AD.
* Uloží certifikát v místním úložišti počítače.
* Uděluje síťovému uživateli přístup k soukromému klíči certifikátu.
* Restartuje službu NPS.

Pokud chcete použít vlastní certifikáty, musíte přidružit veřejný klíč certifikátu k instančnímu objektu ve službě Azure AD a tak dále.

Pokud chcete použít skript, zadejte rozšíření s pověřeními pro správu Služby Azure Active Directory a ID klienta Služby Azure Active Directory, které jste zkopírovali dříve. Spusťte skript na každém serveru NPS, na kterém nainstalujete rozšíření NPS.

1. Spusťte prostředí Windows PowerShell jako správce.

2. Na příkazovém řádku prostředí PowerShell zadejte **disk CD "c:\Program Files\Microsoft\AzureMfa\Config"** a pak vyberte Enter.

3. Na dalším příkazovém řádku zadejte **.\AzureMfaNpsExtnConfigSetup.ps1**a pak vyberte Enter. Skript zkontroluje, jestli je nainstalovaný modul Azure AD PowerShell. Pokud není nainstalován, skript nainstaluje modul za vás.

    ![Spuštění konfiguračního skriptu AzureMfsNpsExtnConfigSetup.ps1](./media/howto-mfa-nps-extension-vpn/image38.png)

    Poté, co skript ověří instalaci modulu PowerShell, zobrazí přihlašovací okno modulu Azure Active Directory PowerShell.

4. Zadejte přihlašovací údaje a heslo správce Azure AD a pak **vyberte Přihlásit se**.

    ![Ověření na Azure AD PowerShell](./media/howto-mfa-nps-extension-vpn/image39.png)

5. Na příkazovém řádku vložte ID klienta, které jste zkopírovali dříve, a pak vyberte Enter.

    ![Zadejte ID adresáře Azure AD zkopírované před](./media/howto-mfa-nps-extension-vpn/image40.png)

    Skript vytvoří certifikát podepsaný svým držitelem a provede další změny konfigurace. Výstup je jako na následujícím obrázku:

    ![Okno PowerShellu s certifikátem podepsaným svým držitelem](./media/howto-mfa-nps-extension-vpn/image41.png)

6. Restartujte server.

### <a name="verify-the-configuration"></a>Ověření konfigurace

Chcete-li ověřit konfiguraci, musíte vytvořit nové připojení VPN se serverem VPN. Po úspěšném zadání pověření pro primární ověřování čeká připojení VPN na úspěšné sekundární ověřování, jak je uvedeno níže.

![Okno VPN nastavení systému Windows](./media/howto-mfa-nps-extension-vpn/image42.png)

Pokud jste úspěšně ověřit pomocí sekundární metody ověření, které jste dříve nakonfigurovali v Azure MFA, jste připojeni k prostředku. Pokud je však sekundární ověřování neúspěšné, bude vám odepřen přístup k prostředku.

V následujícím příkladu poskytuje aplikace Microsoft Authenticator na Windows Phone sekundární ověřování:

![Příklad výzvy vícefaktorové faktori ve Windows Phone](./media/howto-mfa-nps-extension-vpn/image43.png)

Po úspěšném ověření pomocí sekundární metody vám bude udělen přístup k virtuálnímu portu na serveru VPN. Vzhledem k tomu, že jste museli používat metodu sekundárního ověřování pomocí mobilní aplikace na důvěryhodném zařízení, je proces přihlášení bezpečnější, než kdyby používal pouze kombinaci uživatelského jména a hesla.

### <a name="view-event-viewer-logs-for-successful-sign-in-events"></a>Zobrazit protokoly Prohlížeče událostí pro úspěšné události přihlášení

Chcete-li zobrazit úspěšné události přihlášení v prohlížeči událostí systému Windows, zaznamenáte dotaz do protokolu zabezpečení systému Windows na serveru NPS zadáním následujícího příkazu prostředí PowerShell:

    `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`

![Prohlížeč událostí zabezpečení Prostředí PowerShell](./media/howto-mfa-nps-extension-vpn/image44.png)

Můžete také zobrazit protokol zabezpečení nebo vlastní zobrazení Služby síťových zásad a přístupových služeb, jak je znázorněno zde:

![Ukázkový protokol serveru síťových zásad](./media/howto-mfa-nps-extension-vpn/image45.png)

Na serveru, na kterém jste nainstalovali rozšíření NPS pro azure multi-factor authentication, můžete najít protokoly aplikací Prohlížeče událostí, které jsou specifické pro rozšíření na *protokolech aplikací a služeb\Microsoft\AzureMfa*.

    `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`

![Podokno protokolů AuthZ prohlížeče událostí](./media/howto-mfa-nps-extension-vpn/image46.png)

## <a name="troubleshooting-guide"></a>Průvodce odstraňováním potíží

Pokud konfigurace nefunguje podle očekávání, začněte řešit potíže ověřením, že uživatel je nakonfigurován pro použití vícefaktorové ověřování. Ponětit uživatele připojit k [portálu Azure](https://portal.azure.com). Pokud je uživatel vyzván k sekundárnímu ověřování a může se úspěšně ověřit, můžete odstranit nesprávnou konfiguraci vícefaktorové ověřování jako problém.

Pokud vícefaktorové ověřování pracuje pro uživatele, zkontrolujte příslušné protokoly prohlížeče událostí. Protokoly zahrnují událost zabezpečení, provozní bránu a protokoly Azure Multi-Factor Authentication, které jsou popsány v předchozí části.

Příklad protokolu zabezpečení, který zobrazuje událost přihlášení se nezdařilo (ID události 6273) je zde zobrazen:

![Protokol zabezpečení zobrazující událost přihlašování se nezdařilo](./media/howto-mfa-nps-extension-vpn/image47.png)

Tady se zobrazí související událost z protokolu vícefaktorového ověřování Azure:

![Protokoly azure vícefaktorového ověřování](./media/howto-mfa-nps-extension-vpn/image48.png)

Chcete-li provést řešení potíží pro pokročilé servery, obraťte se na soubory protokolu formátu databáze serveru NPS, kde je nainstalována služba NPS. Soubory protokolu jsou vytvořeny ve složce _%SystemRoot%\System32\Logs_ jako textové soubory oddělené čárkami. Popis souborů protokolu naleznete v [tématu Interpretovat soubory protokolu ve formátu databáze NPS](https://technet.microsoft.com/library/cc771748.aspx).

Položky v těchto souborech protokolu je obtížné interpretovat, pokud je neexportujete do tabulky nebo databáze. Můžete najít mnoho nástrojů pro analýzu služby Internet Authentication Service (IAS) online, které vám pomohou při interpretaci souborů protokolu. Výstup jedné takové ke stažení [shareware aplikace](https://www.deepsoftware.com/iasviewer) je zobrazen zde:

![Ukázkový analyzátor aplikace Shareware IAS](./media/howto-mfa-nps-extension-vpn/image49.png)

Chcete-li provést další řešení potíží, můžete použít analyzátor protokolů, například Wireshark nebo [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx). Následující obrázek z Wireshark zobrazuje zprávy RADIUS mezi serverem VPN a NPS.

![Analyzátor zpráv společnosti Microsoft zobrazující filtrovaný provoz](./media/howto-mfa-nps-extension-vpn/image50.png)

Další informace najdete [v tématu Integrace stávající infrastruktury serveru NPS s azure multi-factor authentication](howto-mfa-nps-extension.md).

## <a name="next-steps"></a>Další kroky

[Získání azure vícefaktorového ověřování](concept-mfa-licensing.md)

[Brána vzdálené plochy Azure Multi-Factor Authentication Server pomocí protokolu RADIUS](howto-mfaserver-nps-rdg.md)

[Integrace místních adresářů do služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
