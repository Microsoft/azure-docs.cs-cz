---
title: VPN s MFA pomocí Azure AD s využitím rozšíření NPS – Azure Active Directory
description: Integrujte svoji infrastrukturu VPN pomocí Azure AD MFA pomocí rozšíření serveru NPS (Network Policy Server) pro Microsoft Azure.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c4c5c4e632943ebbe68003f663aebbeaab9ebaf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96743441"
---
# <a name="integrate-your-vpn-infrastructure-with-azure-ad-mfa-by-using-the-network-policy-server-extension-for-azure"></a>Integrace vaší infrastruktury VPN pomocí Azure AD MFA pomocí rozšíření serveru NPS (Network Policy Server) pro Azure

Rozšíření serveru NPS (Network Policy Server) pro Azure umožňuje organizacím chránit ověřování klientů protokol RADIUS (Remote Authentication Dial-In User Service) (RADIUS) pomocí cloudové [Multi-Factor Authentication Azure AD (MFA)](howto-mfaserver-nps-rdg.md), která poskytuje dvoustupňové ověřování.

Tento článek poskytuje pokyny pro integraci infrastruktury serveru NPS s MFA pomocí rozšíření NPS pro Azure. Tento proces umožňuje zabezpečené Dvoustupňové ověřování pro uživatele, kteří se pokoušejí připojit k síti pomocí sítě VPN.

Služba Síťové zásady a přístup poskytuje organizacím možnost:

* Přiřaďte centrální umístění pro správu a řízení síťových požadavků a určete:

  * Kdo se může připojit

  * Jaká je doba připojení ke dni, která jsou povolena

  * Doba trvání připojení

  * Úroveň zabezpečení, kterou klienti musí používat pro připojení

    Místo zadání zásad na každém serveru VPN nebo Brána vzdálené plochy tak, aby byly v centrálním umístění. Protokol RADIUS slouží k poskytování centralizovaného ověřování, autorizace a monitorování účtů (AAA).

* Vytvořte a vyvynuťte zásady stavu klienta architektury NAP (Network Access Protection), které určují, jestli mají zařízení udělený neomezený nebo omezený přístup k síťovým prostředkům.

* Poskytněte způsob, jak vymáhat ověřování a autorizaci pro přístup k bezdrátovým přístupovým bodům 802.1 x a přepínačům sítě Ethernet.
  Další informace najdete v tématu [Server NPS (Network Policy Server](/windows-server/networking/technologies/nps/nps-top)).

Z důvodu zvýšení zabezpečení a zajištění vysoké úrovně dodržování předpisů mohou organizace integrovat server NPS se službou Azure AD Multi-Factor Authentication a zajistit tak, že uživatelé použijí dvoustupňové ověřování pro připojení k virtuálnímu portu na serveru VPN. Aby mohli uživatelé udělit přístup, musí poskytnout kombinaci uživatelského jména a hesla a další informace, které řídí. Tyto informace musí být důvěryhodné a nelze je snadno duplikovat. Může obsahovat číslo mobilního telefonu, pevné číslo nebo aplikaci na mobilním zařízení.

Před dostupností rozšíření serveru NPS pro Azure museli zákazníci, kteří chtěli implementovat dvoustupňové ověřování pro integrovaná prostředí NPS a MFA, nakonfigurovat a udržovat samostatný server MFA v místním prostředí. Tento typ ověřování nabízí Brána vzdálené plochy a Azure Multi-Factor Authentication Server pomocí protokolu RADIUS.

Díky rozšíření serveru NPS pro Azure můžou organizace zabezpečit ověřování klientů RADIUS nasazením řešení MFA založeného na místním prostředí nebo cloudového řešení MFA.

## <a name="authentication-flow"></a>Tok ověřování

Když se uživatelé připojí k virtuálnímu portu na serveru VPN, musí se nejdřív ověřit pomocí různých protokolů. Protokoly umožňují použití kombinace uživatelského jména a hesla a metod ověřování na základě certifikátů.

Kromě ověřování a ověření identity musí mít uživatelé příslušná oprávnění k telefonickému přihlášení. V jednoduchých implementacích se oprávnění k telefonickému přístupu, která povolují přístup, nastaví přímo na objekty uživatele služby Active Directory.

![Karta Telefonické připojení ve vlastnostech uživatelů a počítačů služby Active Directory](./media/howto-mfa-nps-extension-vpn/image1.png)

V jednoduchých implementacích každý server VPN uděluje nebo odmítá přístup na základě zásad, které jsou definovány na jednotlivých místních serverech VPN.

V rámci větších a více škálovatelných implementací jsou zásady, které udělují nebo zamítají přístup k síti VPN, centralizované na serverech RADIUS. V těchto případech server VPN funguje jako server pro přístup (klient RADIUS), který přepošle požadavky na připojení a zprávy účtů na server RADIUS. Chcete-li se připojit k virtuálnímu portu na serveru VPN, musí být uživatelé ověřeni a splňovat podmínky definované centrálně na serverech RADIUS.

Když je rozšíření NPS pro Azure integrované s NPS, výsledky toku úspěšného ověření jsou následující:

1. Server VPN obdrží požadavek na ověření od uživatele sítě VPN, který obsahuje uživatelské jméno a heslo pro připojení k prostředku, jako je například relace vzdálené plochy.
2. Vystupuje jako klient RADIUS, server VPN převede požadavek na zprávu s *požadavkem na přístup* k protokolu RADIUS a pošle ji (se zašifrovaným heslem) na server RADIUS, na kterém je nainstalované rozšíření NPS.
3. Kombinace uživatelského jména a hesla se ověřuje ve službě Active Directory. Pokud uživatelské jméno nebo heslo nejsou správné, server RADIUS pošle zprávu o odepření *přístupu* .
4. Pokud jsou splněné všechny podmínky, jak je uvedeno v žádosti o připojení NPS a zásady sítě (například v případě omezení počtu dnů nebo členství ve skupinách), rozšíření serveru NPS vyvolá požadavek na sekundární ověřování pomocí Multi-Factor Authentication Azure AD.
5. Azure AD Multi-Factor Authentication komunikuje s Azure Active Directory, načte podrobnosti o uživateli a provede sekundární ověřování pomocí metody, která je nakonfigurovaná uživatelem (volání mobilního telefonu, textové zprávy nebo mobilní aplikace).
6. Když je výzva MFA úspěšná, Azure AD Multi-Factor Authentication komunikuje s rozšířením serveru NPS.
7. Jakmile je pokus o připojení ověřen i autorizován, server NPS, ve kterém je rozšíření nainstalováno, odesílá zprávu RADIUS *Access-Accept* na server VPN (klient RADIUS).
8. Uživateli je udělen přístup k virtuálnímu portu na serveru VPN a vytvoří šifrované tunelové propojení VPN.

## <a name="prerequisites"></a>Předpoklady

Tato část podrobně popisuje požadavky, které je třeba provést před integrací vícefaktorového ověřování pomocí sítě VPN. Než začnete, musíte mít splněné následující předpoklady:

* Infrastruktura sítě VPN
* Role služby síťové zásady a přístup
* Licence k Azure AD Multi-Factor Authentication
* Software Windows serveru
* Knihovny
* Služba Azure Active Directory (Azure AD) synchronizovaná s místní službou Active Directory
* ID GUID Azure Active Directory

### <a name="vpn-infrastructure"></a>Infrastruktura sítě VPN

V tomto článku se předpokládá, že máte funkční infrastrukturu VPN, která používá Microsoft Windows Server 2016 a že váš server VPN není momentálně nakonfigurovaný pro přeposílání požadavků na připojení na server RADIUS. V tomto článku nakonfigurujete infrastrukturu VPN tak, aby používala centrální server RADIUS.

Pokud nemáte funkční infrastrukturu sítě VPN, můžete ji rychle vytvořit podle pokynů v části spousta kurzů pro nastavení sítě VPN, které najdete na webech Microsoftu a třetích stran.

### <a name="the-network-policy-and-access-services-role"></a>Role služby síťové zásady a přístup

Služba Síťové zásady a přístup poskytuje funkce serveru a klienta protokolu RADIUS. V tomto článku se předpokládá, že jste nainstalovali roli služby síťové zásady a přístup na členský server nebo řadič domény ve vašem prostředí. V této příručce nakonfigurujete protokol RADIUS pro konfiguraci sítě VPN. Nainstalujte roli služby síťové zásady a přístup na jiném serveru, *než* je server VPN.

Informace o instalaci služby role Síťové zásady a přístup k Windows Serveru 2012 nebo novějšímu najdete v tématu [instalace serveru zásad stavu NAP](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd296890(v=ws.10)). Architektura NAP je v systému Windows Server 2016 zastaralá. Popis osvědčených postupů pro server NPS, včetně doporučení k instalaci NPS na řadič domény, najdete v tématu [osvědčené postupy pro server NPS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771746(v=ws.10)).

### <a name="azure-ad-mfa-license"></a>Licence Azure AD MFA

Pro Azure AD Multi-Factor Authentication se vyžaduje licence, která je dostupná prostřednictvím Azure AD Premium, Enterprise Mobility + Security nebo Multi-Factor Authentication samostatnou licenci. Licence založené na spotřebě pro Azure AD MFA, jako jsou licence vázané na uživatele nebo na ověřování, nejsou kompatibilní s rozšířením NPS. Další informace najdete v tématu [Jak získat Multi-Factor Authentication služby Azure AD](concept-mfa-licensing.md). Pro účely testování můžete použít zkušební předplatné.

### <a name="windows-server-software"></a>Software Windows serveru

Rozšíření serveru NPS vyžaduje systém Windows Server 2008 R2 SP1 nebo novější s nainstalovanou rolí Network Policy and Access Services. Všechny kroky v této příručce byly provedeny s Windows serverem 2016.

### <a name="libraries"></a>Knihovny

Následující knihovny se instalují automaticky s rozšířením NPS:

-    [Visual C++ Distribuovatelné balíčky pro Visual Studio 2013 (x64)](https://www.microsoft.com/download/details.aspx?id=40784)
-    [1.1.166.0 verze Modul Microsoft Azure Active Directory pro Windows PowerShell](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

Pokud modul prostředí PowerShell Microsoft Azure Active Directory ještě neexistuje, nainstaluje se pomocí konfiguračního skriptu, který spustíte jako součást procesu instalace. Není potřeba modul instalovat předem, pokud ještě není nainstalovaný.

### <a name="azure-active-directory-synced-with-on-premises-active-directory"></a>Azure Active Directory synchronizované s místní službou Active Directory

Chcete-li použít rozšíření serveru NPS, musí být místní uživatelé synchronizováni s Azure Active Directory a povoleny pro MFA. V tomto průvodci se předpokládá, že místní uživatelé se synchronizují s Azure Active Directory přes Azure AD Connect. Pokyny pro povolení uživatelů MFA jsou uvedené níže.

Informace o Azure AD Connect najdete v tématu [Integrace místních adresářů s Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>ID GUID Azure Active Directory

Chcete-li nainstalovat rozšíření serveru NPS, je nutné znát identifikátor GUID Azure Active Directory. Pokyny pro vyhledání identifikátoru GUID Azure Active Directory jsou uvedené v další části.

## <a name="configure-radius-for-vpn-connections"></a>Konfigurace protokolu RADIUS pro připojení VPN

Pokud jste nainstalovali roli NPS na členský server, je nutné ji nakonfigurovat pro ověřování a autorizaci klienta VPN, který požaduje připojení VPN. 

V této části se předpokládá, že jste nainstalovali roli služby síťové zásady a přístup, ale nenakonfigurovali jste ji pro použití ve vaší infrastruktuře.

> [!NOTE]
> Pokud již máte funkční server VPN, který používá centralizovaný server RADIUS pro ověřování, můžete tuto část přeskočit.
>

### <a name="register-server-in-active-directory"></a>Zaregistrovat server ve službě Active Directory

Aby bylo možné v tomto scénáři správně fungovat, musí být server NPS zaregistrován ve službě Active Directory.

1. Spusťte Správce serveru.

2. V Správce serveru vyberte **nástroje** a pak vyberte **Server NPS (Network Policy Server**).

3. V konzole serveru NPS (Network Policy Server) klikněte pravým tlačítkem na **NPS (místní)** a pak vyberte **zaregistrovat server ve službě Active Directory**. Dvakrát vyberte **OK** .

    ![Možnost registrace serveru v nabídce služby Active Directory](./media/howto-mfa-nps-extension-vpn/image2.png)

4. Nechejte konzolu otevřenou pro další postup.

### <a name="use-wizard-to-configure-the-radius-server"></a>Použití Průvodce ke konfiguraci serveru RADIUS

Ke konfiguraci serveru RADIUS můžete použít standardní možnost (založenou na průvodci) nebo pokročilou konfiguraci. V této části se předpokládá, že používáte možnost standardní konfigurace na základě průvodce.

1. V konzole serveru NPS (Network Policy Server **) vyberte NPS (místní)**.

2. V části **standardní konfigurace** vyberte možnost **server RADIUS pro telefonické připojení nebo připojení VPN** a pak vyberte **Konfigurovat síť VPN nebo telefonické připojení**.

    ![Konfigurace serveru RADIUS pro vytáčená nebo VPN připojení](./media/howto-mfa-nps-extension-vpn/image3.png)

3. V okně **Vyberte telefonické připojení nebo typ připojení k virtuální privátní síti** vyberte připojení k **virtuální privátní síti** a pak vyberte **Další**.

    ![Konfigurace připojení virtuální privátní sítě](./media/howto-mfa-nps-extension-vpn/image4.png)

4. V okně **Zadejte server telefonického připojení nebo VPN** vyberte **Přidat**.

5. V okně **nové klientské okno protokolu RADIUS** zadejte popisný název, zadejte název nebo IP adresu serveru VPN, který chcete přeložit, a potom zadejte heslo sdíleného tajného klíče. Heslo sdíleného tajného klíče nastavte na dlouhé a složité. Poznamenejte si ho, protože ho budete potřebovat v další části.

    ![Vytvořit nové okno klienta protokolu RADIUS](./media/howto-mfa-nps-extension-vpn/image5.png)

6. Vyberte **OK** a pak vyberte **Další**.

7. V okně **konfigurovat metody ověřování** přijměte výchozí výběr (**Microsoft Encrypted Authentication Version 2 [MS-CHAPv2])** nebo zvolte jinou možnost a vyberte **Další**.

    > [!NOTE]
    > Pokud nakonfigurujete protokol EAP (Extensible Authentication Protocol), musíte použít protokol PEAP (Microsoft Challenge-Handshake Authentication Protocol) nebo protokol PEAP (Protected Extensible Authentication Protocol). Žádný jiný protokol EAP není podporován.

8. V okně **Zadejte skupiny uživatelů** vyberte **Přidat** a potom vyberte příslušnou skupinu. Pokud žádná skupina neexistuje, ponechte výběr prázdnou pro udělení přístupu všem uživatelům.

    ![Zadání okna skupin uživatelů pro povolení nebo odepření přístupu](./media/howto-mfa-nps-extension-vpn/image7.png)

9. Vyberte **Další**.

10. V okně **zadat filtry IP** vyberte **Další**.

11. V okně **zadat nastavení šifrování** přijměte výchozí nastavení a pak vyberte **Další**.

    ![Okno zadat nastavení šifrování](./media/howto-mfa-nps-extension-vpn/image8.png)

12. V okně **Zadejte název sféry** ponechejte pole název sféry prázdné, přijměte výchozí nastavení a pak vyberte **Další**.

    ![Okno zadat název sféry](./media/howto-mfa-nps-extension-vpn/image9.png)

13. V okně **dokončení nového telefonického připojení nebo připojení k virtuální privátní síti a klientů RADIUS** vyberte **Dokončit**.

    ![Okno Konfigurace dokončeno](./media/howto-mfa-nps-extension-vpn/image10.png)

### <a name="verify-the-radius-configuration"></a>Ověření konfigurace protokolu RADIUS

Tato část podrobně popisuje konfiguraci, kterou jste vytvořili pomocí průvodce.

1. Na serveru NPS (Network Policy Server) rozbalte v konzole NPS (místní) možnost **Klienti RADIUS** a pak vyberte **Klienti RADIUS**.

2. V podokně podrobností klikněte pravým tlačítkem na klienta RADIUS, který jste vytvořili, a pak vyberte **vlastnosti**. Vlastnosti vašeho klienta protokolu RADIUS (server VPN) by měly vypadat jako ty, které jsou tady uvedené:

    ![Ověření vlastností a konfigurace sítě VPN](./media/howto-mfa-nps-extension-vpn/image11.png)

3. Vyberte **Zrušit**.

4. Na serveru NPS (Network Policy Server) rozbalte v konzole NPS (místní) **zásady** a vyberte **zásady vyžádání nového připojení**. Zobrazí se zásada připojení VPN, jak je znázorněno na následujícím obrázku:

    ![Zásady vyžádání nového připojení, které zobrazují zásady připojení k síti VPN](./media/howto-mfa-nps-extension-vpn/image12.png)

5. V části **zásady** vyberte **zásady sítě**. Měla by se zobrazit zásada připojení virtuální privátní sítě (VPN), která se podobá zásadám uvedeným na následujícím obrázku:

    ![Zásady sítě ukazující zásadu připojení k virtuální privátní síti](./media/howto-mfa-nps-extension-vpn/image13.png)

## <a name="configure-your-vpn-server-to-use-radius-authentication"></a>Konfigurace serveru VPN pro použití ověřování RADIUS

V této části nakonfigurujete server VPN tak, aby používal ověřování pomocí protokolu RADIUS. V pokynech se předpokládá, že máte funkční konfiguraci serveru VPN, ale nenakonfigurovali jste ho, aby používal ověřování pomocí protokolu RADIUS. Po nakonfigurování serveru VPN ověřte, že konfigurace funguje podle očekávání.

> [!NOTE]
> Pokud už máte funkční konfiguraci serveru VPN, která používá ověřování pomocí protokolu RADIUS, můžete tuto část přeskočit.
>

### <a name="configure-authentication-provider"></a>Konfigurace zprostředkovatele ověřování

1. Na serveru VPN otevřete Správce serveru.

2. V Správce serveru vyberte **nástroje** a pak vyberte **Směrování a vzdálený přístup**.

3. V okně **Směrování a vzdálený přístup** klikněte pravým tlačítkem na **\<server name> (místní)** a pak vyberte **vlastnosti**.

4. V okně **\<server name> (místní) vlastnosti** vyberte kartu **zabezpečení** .

5. Na kartě **zabezpečení** v části **Zprostředkovatel ověřování** vyberte **ověřování RADIUS** a pak vyberte **Konfigurovat**.

    ![Konfigurace zprostředkovatele ověřování RADIUS](./media/howto-mfa-nps-extension-vpn/image15.png)

6. V okně **ověřování pomocí protokolu RADIUS** vyberte **Přidat**.

7. V okně **Přidat server protokolu RADIUS** proveďte tyto kroky:

    a. Do pole **název serveru** zadejte název nebo IP adresu serveru RADIUS, který jste nakonfigurovali v předchozí části.

    b. Pro **sdílený tajný klíč** vyberte **změnit** a potom zadejte heslo sdíleného tajného klíče, které jste vytvořili a nahráli dříve.

    c. V poli **časový limit (sekundy)** zadejte hodnotu **30**.  
    Hodnota časového limitu je nutná k tomu, aby byl dostatek času na dokončení druhého faktoru ověřování. Některé sítě VPN nebo oblasti vyžadují nastavení časového limitu delší než 30 sekund, aby uživatelé nemohli přijímat více telefonních hovorů. Pokud se tento problém vyskytne u uživatelů, zvyšte hodnotu **časového limitu (sekundy)** v přírůstcích po dobu 30 sekund, dokud nedojde k problému.

    ![Nastavení časového limitu pro přidání okna serveru RADIUS](./media/howto-mfa-nps-extension-vpn/image16.png) 

8. Vyberte **OK**.

### <a name="test-vpn-connectivity"></a>Testovat připojení VPN

V této části ověříte, že je klient VPN ověřený a autorizovaný serverem RADIUS při pokusu o připojení k virtuálnímu portu VPN. V těchto pokynech se předpokládá, že používáte Windows 10 jako klienta VPN.

> [!NOTE]
> Pokud jste už nakonfigurovali klienta VPN pro připojení k serveru VPN a uložili nastavení, můžete přeskočit postup týkající se konfigurace a uložení objektu připojení VPN.
>

1. V klientském počítači VPN vyberte tlačítko **Start** a pak klikněte na tlačítko **Nastavení** .

2. V okně **nastavení systému Windows** vyberte **síť & Internet**.

3. Vyberte **VPN**.

4. Vyberte **Přidat připojení VPN**.

5. V okně **Přidat připojení VPN** v poli **Poskytovatel sítě VPN** vyberte možnost **Windows (předdefinované)**, podle potřeby vyplňte zbývající pole a pak vyberte **Uložit**.

    ![Okno Přidat připojení k síti VPN](./media/howto-mfa-nps-extension-vpn/image17.png)

6. V **Ovládacích panelech** vyberte položku **Centrum síťových a sdílení**.

7. Vyberte **změnit nastavení adaptéru**.

    ![Centrum síťových a sdílení – Změna nastavení adaptéru](./media/howto-mfa-nps-extension-vpn/image18.png)

8. Klikněte pravým tlačítkem na připojení k síti VPN a pak vyberte **vlastnosti**.

9. V okně Vlastnosti sítě VPN vyberte kartu **zabezpečení** .

10. Na kartě **zabezpečení** zajistěte, aby byla vybrána pouze **Microsoft CHAP verze 2 (MS-CHAP v2)** , a pak vyberte **OK**.

    ![Možnost "Povolte tyto protokoly"](./media/howto-mfa-nps-extension-vpn/image20.png)

11. Klikněte pravým tlačítkem na připojení k síti VPN a pak vyberte **připojit**.

12. V okně **Nastavení** vyberte **připojit**.  
    Úspěšné připojení se zobrazí v protokolu zabezpečení na serveru RADIUS jako událost s ID 6272, jak je znázorněno zde:

    ![Událost okno Vlastnosti zobrazuje úspěšné připojení.](./media/howto-mfa-nps-extension-vpn/image21.png)

## <a name="troubleshooting-radius"></a>Řešení potíží s protokolem RADIUS

Předpokládejme, že vaše konfigurace sítě VPN fungovala předtím, než server VPN nakonfigurujete k použití centralizovaného serveru RADIUS pro ověřování a autorizaci. Pokud konfigurace fungovala, je pravděpodobně příčinou problému Chybná konfigurace serveru RADIUS nebo použití neplatného uživatelského jména nebo hesla. Pokud například použijete alternativní příponu UPN v uživatelském jménu, pokus o přihlášení může selhat. Pro dosažení nejlepších výsledků použijte stejný název účtu.

Aby bylo možné tyto problémy vyřešit, je ideálním místem, kde začít, je prozkoumávat protokoly událostí zabezpečení na serveru protokolu RADIUS. Pokud chcete ušetřit čas při hledání událostí, můžete použít vlastní zobrazení na serveru na základě rolí a přístup v Prohlížeč událostí, jak je znázorněno zde. "ID události 6273" označuje události, u kterých server NPS odepřel přístup k uživateli.

![Prohlížeč událostí zobrazení událostí NPAS](./media/howto-mfa-nps-extension-vpn/image22.png)

## <a name="configure-multi-factor-authentication"></a>Konfigurace Multi-Factor Authentication

Pomoc s konfigurací uživatelů pro Multi-Factor Authentication najdete v článcích [plánování cloudových Multi-Factor Authenticationch nasazení služby Azure AD](howto-mfa-getstarted.md#create-conditional-access-policy) a [Nastavení účtu pro dvoustupňové ověřování](../user-help/multi-factor-authentication-end-user-first-time.md) .

## <a name="install-and-configure-the-nps-extension"></a>Instalace a konfigurace rozšíření serveru NPS

V této části najdete pokyny pro konfiguraci sítě VPN pro použití VÍCEFAKTOROVÉHO ověřování klientů pomocí serveru VPN.

> [!NOTE]
> Klíč registru REQUIRE_USER_MATCH rozlišuje velká a malá písmena. Všechny hodnoty musí být nastaveny ve velkých malých písmenech.
>

Až nainstalujete a nakonfigurujete rozšíření serveru NPS, bude pro použití VÍCEFAKTOROVÉHO ověřování potřeba všechna ověření klienta na základě protokolu RADIUS, která jsou zpracována tímto serverem. Pokud všichni uživatelé VPN nejsou zaregistrovaní ve službě Azure AD Multi-Factor Authentication, můžete provést jednu z následujících akcí:

* Nastavte jiný server RADIUS pro ověřování uživatelů, kteří nejsou nakonfigurovaní pro použití vícefaktorového ověřování.

* Vytvořte položku registru, která umožní napadeným uživatelům poskytnout druhý faktor ověřování, pokud jsou zaregistrované ve službě Azure AD Multi-Factor Authentication.

Vytvořte novou řetězcovou hodnotu s názvem _REQUIRE_USER_MATCH v HKLM\SOFTWARE\Microsoft\AzureMfa_ a nastavte hodnotu na *true* nebo *false*.

![Nastavení "vyžadovat shod uživatele"](./media/howto-mfa-nps-extension-vpn/image34.png)

Pokud je hodnota nastavená na *true* nebo je prázdná, budou se všechny žádosti o ověření vztahovat na výzvu MFA. Pokud je hodnota nastavená na *false*, budou se problémy MFA vydávat jenom uživatelům, kteří jsou zaregistrovaní ve službě Azure AD Multi-Factor Authentication. Použijte nastavení *false* pouze při testování nebo v produkčním prostředí během období připojování.



### <a name="obtain-the-azure-active-directory-tenant-id"></a>Získat ID tenanta Azure Active Directory

V rámci konfigurace rozšíření serveru NPS musíte zadat přihlašovací údaje správce a ID vašeho tenanta Azure AD. Chcete-li získat ID tenanta, proveďte následující kroky:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako globální správce tenanta Azure.
1. V nabídce Azure Portal vyberte **Azure Active Directory** nebo vyhledejte a vyberte **Azure Active Directory** na libovolné stránce.
1. Na stránce **Přehled** se zobrazí *informace o tenantovi* . Vedle *ID tenanta* vyberte ikonu **kopírování** , jak je znázorněno na následujícím ukázkovém snímku obrazovky:

   ![Získává se ID tenanta z Azure Portal.](./media/howto-mfa-nps-extension-vpn/azure-active-directory-tenant-id-portal.png)

### <a name="install-the-nps-extension"></a>Instalace rozšíření serveru NPS

Rozšíření serveru NPS musí být nainstalováno na serveru, který má nainstalovanou roli síťové zásady a přístup a který funguje jako server RADIUS v návrhu. Neinstalujte *na* server VPN rozšíření serveru NPS.

1. Stáhněte si rozšíření serveru NPS z [webu Microsoft Download Center](https://aka.ms/npsmfa).

2. Zkopírujte spustitelný soubor instalačního programu (*NpsExtnForAzureMfaInstaller.exe*) na server NPS.

3. Na serveru NPS poklikejte na **NpsExtnForAzureMfaInstaller.exe** a pokud se zobrazí výzva, vyberte **Spustit**.

4. V okně **rozšíření serveru NPS pro Azure AD MFA** si přečtěte licenční podmínky pro software, zaškrtněte políčko Souhlasím **s licenčními podmínkami a ujednání** a pak vyberte **nainstalovat**.

    ![Okno pro nastavení rozšíření NPS pro Azure AD MFA](./media/howto-mfa-nps-extension-vpn/image36.png)

5. V okně **rozšíření NPS pro nastavení Azure AD MFA** vyberte **Zavřít**.  

    ![Potvrzovací okno "nastavení bylo úspěšné"](./media/howto-mfa-nps-extension-vpn/image37.png)

### <a name="configure-certificates-for-use-with-the-nps-extension-by-using-a-powershell-script"></a>Konfigurace certifikátů pro použití s rozšířením NPS pomocí skriptu PowerShellu

Aby bylo zajištěno zabezpečení komunikace a záruky, nakonfigurujte certifikáty pro použití v rozšíření serveru NPS. Komponenty serveru NPS obsahují skript prostředí Windows PowerShell, který konfiguruje certifikát podepsaný svým držitelem pro použití se serverem NPS.

Skript provede následující akce:

* Vytvoří certifikát podepsaný svým držitelem.
* Přidruží veřejný klíč certifikátu k instančnímu objektu ve službě Azure AD.
* Uloží certifikát do úložiště místního počítače.
* Udělí síťovému uživateli přístup k privátnímu klíči certifikátu.
* Restartuje službu NPS.

Pokud chcete používat vlastní certifikáty, musíte k instančnímu objektu ve službě Azure AD přidružit veřejný klíč certifikátu a tak dále.

Chcete-li použít skript, zadejte rozšíření s přihlašovacími údaji správce Azure Active Directory a Azure Active Directory ID tenanta, které jste zkopírovali dříve. Účet musí být ve stejném tenantovi Azure AD, pro který chcete rozšíření povolit. Spusťte skript na každém serveru NPS, na který nainstalujete rozšíření serveru NPS.

1. Spusťte prostředí Windows PowerShell jako správce.

2. Na příkazovém řádku PowerShellu zadejte **CD "C:\Program Files\Microsoft\AzureMfa\Config"** a pak vyberte Enter.

3. Na dalším příkazovém řádku zadejte **.\AzureMfaNpsExtnConfigSetup.ps1** a pak vyberte Enter. Skript zkontroluje, jestli je nainstalovaný modul Azure AD PowerShell. Pokud není nainstalovaný, skript nainstaluje modul za vás.

    ![Spuštění konfiguračního skriptu AzureMfsNpsExtnConfigSetup.ps1](./media/howto-mfa-nps-extension-vpn/image38.png)

    Pokud se v důsledku TLS zobrazí chyba zabezpečení, povolte TLS 1,2 pomocí `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` příkazu z příkazového řádku PowerShellu.
    
    Když skript ověří instalaci modulu PowerShellu, zobrazí se okno pro přihlášení modulu PowerShellu Azure Active Directory.

4. Zadejte svoje přihlašovací údaje a heslo správce Azure AD a pak vyberte **Přihlásit** se.

    ![Ověření ve službě Azure AD PowerShell](./media/howto-mfa-nps-extension-vpn/image39.png)

5. Na příkazovém řádku Vložte ID tenanta, které jste zkopírovali dříve, a pak vyberte zadat.

    ![Zadejte ID tenanta služby Azure AD, které jste zkopírovali dřív.](./media/howto-mfa-nps-extension-vpn/image40.png)

    Skript vytvoří certifikát podepsaný svým držitelem a provede další změny konfigurace. Výstup je podobný jako na následujícím obrázku:

    ![Okno PowerShellu zobrazující certifikát podepsaný svým držitelem](./media/howto-mfa-nps-extension-vpn/image41.png)

6. Restartujte server.

### <a name="verify-the-configuration"></a>Ověření konfigurace

Chcete-li ověřit konfiguraci, je nutné vytvořit nové připojení k síti VPN se serverem VPN. Po úspěšném zadání přihlašovacích údajů pro primární ověřování bude připojení VPN čekat, než se sekundární ověřování dokončí, než se naváže připojení, jak je znázorněno níže.

![Okno nastavení Windows VPN](./media/howto-mfa-nps-extension-vpn/image42.png)

Pokud úspěšně ověříte sekundární metodou ověření, kterou jste předtím nakonfigurovali v Azure AD MFA, budete připojeni k prostředku. Pokud se ale sekundární ověřování nezdařilo, budete mít přístup k prostředku odepřený.

V následujícím příkladu aplikace Microsoft Authenticator v Windows Phone poskytuje sekundární ověřování:

![Příklad výzvy MFA na Windows Phone](./media/howto-mfa-nps-extension-vpn/image43.png)

Po úspěšném ověření pomocí sekundární metody získáte přístup k virtuálnímu portu na serveru VPN. Vzhledem k tomu, že jste museli použít sekundární metodu ověřování pomocí mobilní aplikace na důvěryhodném zařízení, je proces přihlášení bezpečnější, než kdyby používal jenom kombinaci uživatelského jména a hesla.

### <a name="view-event-viewer-logs-for-successful-sign-in-events"></a>Zobrazit protokoly Prohlížeč událostí pro úspěšné události přihlášení

Pokud chcete zobrazit úspěšné události přihlášení v protokolech Windows Prohlížeč událostí, zadejte na server NPS dotaz na protokol zabezpečení systému Windows, a to tak, že zadáte následující příkaz prostředí PowerShell:

```powershell
Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL
```

![Prohlížeč událostí zabezpečení PowerShellu](./media/howto-mfa-nps-extension-vpn/image44.png)

Můžete také zobrazit protokol zabezpečení nebo vlastní zobrazení služby síťové zásady a přístup, jak je znázorněno zde:

![Příklad protokolu serveru NPS (Network Policy Server)](./media/howto-mfa-nps-extension-vpn/image45.png)

Na serveru, na který jste nainstalovali rozšíření serveru NPS pro Azure AD Multi-Factor Authentication, najdete Prohlížeč událostí protokoly aplikací, které jsou specifické pro rozšíření na stránce *Application and Services Logs\Microsoft\AzureMfa*.

```powershell
Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL
```

![Příklad Prohlížeč událostí podokně protokolu AuthZ](./media/howto-mfa-nps-extension-vpn/image46.png)

## <a name="troubleshooting-guide"></a>Průvodce odstraňováním potíží

Pokud konfigurace nefunguje podle očekávání, začněte s řešením potíží tím, že ověříte, jestli je uživatel nakonfigurovaný pro použití vícefaktorového ověřování. Připojte se k [Azure Portal](https://portal.azure.com)uživatele. Pokud se uživateli zobrazí výzva k zadání sekundárního ověřování a je možné ho úspěšně ověřit, můžete jako problém eliminovat neplatnou konfiguraci VÍCEFAKTOROVÉHO ověřování.

Pokud pro uživatele ověřování MFA funguje, zkontrolujte příslušné protokoly Prohlížeč událostí. Mezi protokoly patří události zabezpečení, protokoly provozu a služby Azure AD Multi-Factor Authentication, které jsou popsány v předchozí části.

Příklad protokolu zabezpečení, který zobrazuje neúspěšné události přihlášení (ID události 6273), je uveden zde:

![Protokol zabezpečení zobrazující událost neúspěšného přihlášení](./media/howto-mfa-nps-extension-vpn/image47.png)

V protokolu Multi-Factor Authentication služby Azure AD se zobrazí související událost:

![Protokoly Multi-Factor Authentication služby Azure AD](./media/howto-mfa-nps-extension-vpn/image48.png)

Chcete-li provést pokročilé řešení potíží, Projděte si soubory protokolu ve formátu databáze NPS, kde je nainstalována služba NPS. Soubory protokolu se vytvoří ve složce _%systemroot%\System32\Logs_ jako textové soubory oddělené čárkami. Popis souborů protokolu najdete v tématu [Interpretace souborů protokolu ve formátu databáze NPS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771748(v=ws.10)).

Položky v těchto souborech protokolu je obtížné interpretovat, pokud je neexportujete do tabulky nebo databáze. Mnoho analytických nástrojů služby ověřování v Internetu (IAS) můžete najít online, abyste vám pomohli interpretovat soubory protokolů. Výstup jedné takové aplikace ke stažení [shareware](https://www.deepsoftware.com/iasviewer) se zobrazí zde:

![Ukázka analyzátoru služby IAS pro shareware App](./media/howto-mfa-nps-extension-vpn/image49.png)

Chcete-li provést další řešení potíží, můžete použít analyzátor protokolů, například Wireshark nebo [Microsoft Message Analyzer](/message-analyzer/microsoft-message-analyzer-operating-guide). Následující obrázek z nástroje Wireshark zobrazuje zprávy protokolu RADIUS mezi serverem VPN a serverem NPS.

![Microsoft Message Analyzer zobrazující filtrovaný provoz](./media/howto-mfa-nps-extension-vpn/image50.png)

Další informace najdete v tématu [integrace stávající infrastruktury serveru NPS se službou Azure AD Multi-Factor Authentication](howto-mfa-nps-extension.md).

## <a name="next-steps"></a>Další kroky

[Získat Multi-Factor Authentication služby Azure AD](concept-mfa-licensing.md)

[Brána vzdálené plochy Azure Multi-Factor Authentication Server pomocí protokolu RADIUS](howto-mfaserver-nps-rdg.md)

[Integrace místních adresářů do služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md)