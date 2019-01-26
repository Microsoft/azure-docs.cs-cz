---
title: Vzdálená plocha brány integrace s rozšíření Azure MFA NPS | Dokumentace Microsoftu
description: Integrace infrastruktury služby Brána vzdálené plochy s Azure MFA pomocí rozšíření serveru Network Policy Server pro Microsoft Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.openlocfilehash: 3a6ede4447ff6ab11ed8b51f18d4ae6f04c7d191
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55077518"
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>Integrace služby Brána vzdálené plochy infrastrukturu s použitím rozšíření serveru NPS (Network Policy Server) a Azure AD

Tento článek obsahuje podrobnosti pro integraci s Azure Multi-Factor Authentication (MFA) infrastrukturu služby Brána vzdálené plochy pomocí rozšíření serveru NPS (Network Policy Server) pro Microsoft Azure.

Rozšíření serveru NPS (Network Policy Server) pro Azure umožňuje zákazníkům, kteří k zajištění ověření klienta RADIUS Remote Authentication telefonického uživatele Service () pomocí Azure je založené na cloudu [Multi-Factor Authentication (MFA)](multi-factor-authentication.md). Toto řešení zajišťuje dvoustupňové ověření pro přidání druhou vrstvu zabezpečení uživatelská přihlášení a transakce.

Tento článek obsahuje podrobné pokyny pro integraci s Azure MFA NPS server infrastruktury pomocí rozšíření NPS pro Azure. To umožňuje zabezpečené ověřování pro uživatele pokoušející se přihlásit do služby Brána vzdálené plochy.

> [!NOTE]
> Tento článek se nemá používat s MFA Server nasazení a by měla sloužit pouze s nasazeními Azure MFA (Cloud-based).

Síťové zásady a přístup k službám (NPS) poskytuje organizacím možnost postupujte takto:

* Definujte centrální umístění pro řízení a kontrolu požadavků sítě tak, že určíte, kdo se může připojit, dobu připojení den jsou povolené a dobu trvání připojení a úroveň zabezpečení, které musí klienti používat pro připojení a tak dále. Místo zadání těchto zásad na každém serveru sítě VPN nebo Brána vzdálené plochy (RD), tyto zásady se dá nastavit jednou v centrálním umístění. Protokol RADIUS poskytuje centralizované ověřování, autorizaci a monitorování (AAA). 
* Vytvořit a vynucovat zásady stavu klienta ochrany NAP (Network Access), které určují, jestli zařízení jsou udělena neomezeného nebo omezeného přístupu k síťovým prostředkům.
* Poskytují způsob vynucení ověřování a autorizace pro přístup k protokolu 802. 1 x podporuje bezdrátových přístupových bodů a přepínače sítě Ethernet.

Obvykle zásady organizace použijte RADIUS serveru NPS () pro zjednodušení a centralizovat správu sítě VPN. Řada organizací ale používat taky NPS zjednodušují a centralizovat správu zásad autorizace připojení ke vzdálené ploše Desktop (CAP k vzdálené ploše).

Organizace také můžete integrovat server NPS s Azure MFA pro zvýšení zabezpečení a poskytovat vysokou úroveň dodržování předpisů. To pomáhá zajistit, že uživatelé vytvořit dvoustupňové ověření pro přihlášení k Brána vzdálené plochy. Pro uživatele, který má být udělen přístup musí zadat jejich kombinace uživatelského jména a hesla společně s informacemi, jestli nemá uživatel v jejich řízení. Tyto informace musí být důvěryhodné a duplicitní nejsou snadno, jako je například číslo mobilního telefonu, číslo linky, aplikace na mobilním zařízení a tak dále. Další informace o podporovaných metod ověřování najdete v části [zjistit, jaké metody ověřování uživatelům používat](howto-mfa-nps-extension.md#determine-which-authentication-methods-your-users-can-use).

Před dostupnost rozšíření NPS pro Azure zákazníci, kteří si nepřejí provádět dvoustupňové ověření integrované prostředí serveru NPS a Azure MFA museli konfigurovat a spravovat samostatný Server MFA v místním prostředí, jak je uvedeno v [ Vzdálená plocha brány a Server Azure Multi-Factor Authentication pomocí protokolu RADIUS](howto-mfaserver-nps-rdg.md).

Dostupnost rozšíření NPS pro Azure nyní poskytuje organizacím možnost nasazovat řešení vícefaktorového ověřování na základě v místním nebo cloudovým řešením vícefaktorové ověřování pro zabezpečené ověřování klienta protokolu RADIUS.

## <a name="authentication-flow"></a>Tok ověřování

Pro uživatele, kterým se má udělit přístup k síťovým prostředkům prostřednictvím služby Brána vzdálené plochy musí splňovat podmínky uvedené v jedné VP zásady autorizace připojení (CAP ke vzdálené ploše) a zásady pro prostředků autorizace jeden vzdálené ploše (VP pro autorizaci prostředků). CAP k vzdálené ploše zadejte, kdo je oprávnění pro připojení k VP brány. Autorizaci zadejte síťovým prostředkům, jako je vzdálené plochy nebo vzdálené aplikace, které uživatel může pro připojení prostřednictvím brány VP. 

Brány VP můžete nakonfigurovat pro použití zásady centrálního úložiště pro CAP k vzdálené ploše. Autorizaci nelze použít centrální zásady, jak se zpracovávají na bráně VP. Příklad Brána VP nakonfigurována pro použití zásady centrálního úložiště pro CAP k vzdálené ploše je klienta protokolu RADIUS na jiný server NPS, který slouží jako zásady centrálního úložiště.

Když rozšíření NPS pro Azure je integrována se serverem NPS a Brána vzdálené plochy, tok úspěšné ověřování vypadá takto:

1. Serveru služby Brána vzdálené plochy obdrží požadavek na ověření od uživatele vzdálené plochy pro připojení k prostředku, jako je například relaci vzdálené plochy. Funguje jako klienta protokolu RADIUS, serveru brány vzdálené plochy provede převod na zprávu pomocí protokolu RADIUS-žádost o přístup a odešle zprávu do serveru RADIUS (NPS), kde je nainstalovaná rozšíření NPS.
1. Kombinace uživatelského jména a hesla je ověřen ve službě Active Directory a ověření uživatele.
1. Pokud jsou splněny všechny podmínky uvedené v žádosti o připojení serveru NPS a zásady sítě (například čas, den nebo skupiny omezeními pro členství vyplývajícími), rozšíření NPS spustí požadavek pro sekundární ověřování s Azure MFA.
1. Azure MFA komunikuje se službou Azure AD, načte podrobnosti daného uživatele a provádí sekundární ověření pomocí podporované metody.
1. Po úspěšném nasazení z ověřovacím testem MFA Azure MFA komunikuje výsledek, který má rozšíření serveru NPS.
1. Server NPS, kde je nainstalovaná rozšíření, odešle zprávu přijmout přístup protokolu RADIUS pro zásady CAP ke vzdálené ploše na serveru brány vzdálené plochy.
1. Uživateli je udělen přístup k prostředku požadovaná síť prostřednictvím brány VP.

## <a name="prerequisites"></a>Požadavky

Tato část podrobně popisuje předpoklady nezbytné před integrace Azure MFA s bránou vzdálení plochy. Než začnete, musíte mít splněné následující požadavky na místě.  

* Vzdálené služby plocha (RDS) infrastruktury
* Licence Azure MFA
* Software Windows serveru
* Síťové zásady a přístup k službám (NPS)
* Azure Active Directory synchronizovat s místní služby Active Directory
* Identifikátor GUID služby Azure Active Directory

### <a name="remote-desktop-services-rds-infrastructure"></a>Vzdálené služby plocha (RDS) infrastruktury

Musíte mít funkční infrastrukturu služby Vzdálená plocha (RDS) na místě. Pokud ho nevidíte, pak můžete rychle vytvořit tuto infrastrukturu v Azure pomocí následující šablony rychlý start: [Vytvoření kolekce relací vzdálené plochy nasazení](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment). 

Pokud chcete ručně vytvořit místní infrastrukturu RDS rychle pro účely testování, postupujte podle kroků, které chcete nasadit některé. 
**Další informace**: [Nasazení vzdálené plochy s využitím Azure rychlý start](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-in-azure) a [základní VP k nasazení infrastruktury](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure). 

### <a name="azure-mfa-license"></a>Licence Azure MFA

Vyžaduje se licence pro Azure MFA, která je dostupná prostřednictvím Azure AD Premium nebo dalších sad, které ho obsahují. Založenou na skutečné spotřebě licence pro Azure MFA, například podle uživatele nebo na ověřování licencí, nejsou kompatibilní s rozšíření serveru NPS. Další informace najdete v tématu [jak získat Azure Multi-Factor Authentication](concept-mfa-licensing.md). Pro účely testování můžete použít zkušební verzi předplatného.

### <a name="windows-server-software"></a>Software Windows serveru

Rozšíření NPS server vyžaduje Windows Server 2008 R2 SP1 nebo novějším s nainstalovanou službou role NPS. Všechny kroky v této části byly provedeny pomocí Windows serveru 2016.

### <a name="network-policy-and-access-services-nps-role"></a>Síťové zásady a přístup k službám (NPS)

Službu role NPS poskytuje server protokolu RADIUS a klientské funkce, jakož i služba health service zásady přístupu k síti. Tato role musí být nainstalovaný na nejméně dva počítače ve vaší infrastruktuře: Brána vzdálené plochy a jiného členského serveru nebo řadiče domény. Ve výchozím nastavení role již existuje v počítač nakonfigurovaný jako Brána vzdálené plochy.  Také nainstalujte NPS role na alespoň na jiném počítači, jako jsou řadiče domény nebo členském serveru.

Informace o instalaci role Server NPS Windows Server 2012 nebo starší, podívejte se sem [nainstalovat Server zásad stavu NAP](https://technet.microsoft.com/library/dd296890.aspx). Popis osvědčených postupů pro server NPS, včetně doporučení pro instalaci serveru NPS na řadiči domény, najdete v části [osvědčené postupy pro server NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Azure Active Directory synchronizovat s místní služby Active Directory

Použití rozšíření serveru NPS, musí být místním uživatelům synchronizaci se službou Azure AD a povolené pro vícefaktorové ověřování. V této části se předpokládá, že jsou v místním uživatelům synchronizovat s Azure AD pomocí AD Connect. Informace o službě Azure AD connect najdete v tématu [integrace místních adresářů se službou Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>Identifikátor GUID služby Azure Active Directory

Pokud chcete nainstalovat rozšíření serveru NPS, je potřeba vědět identifikátor GUID služby Azure AD. Níže jsou uvedené pokyny, jak najít identifikátor GUID služby Azure AD.

## <a name="configure-multi-factor-authentication"></a>Konfigurace ověřování službou Multi-Factor Authentication 

Tato část obsahuje pokyny k integraci Azure MFA s bránou vzdálení plochy. Jako správce musíte nakonfigurovat službu Azure MFA, předtím, než mohou uživatelé registrovat svá zařízení služby Multi-Factor Authentication nebo aplikace.

Postupujte podle kroků v [Začínáme se službou Azure Multi-Factor Authentication v cloudu](howto-mfa-getstarted.md) povolte MFA pro uživatele Azure AD.

### <a name="configure-accounts-for-two-step-verification"></a>Konfigurace účtů pro dvoustupňové ověřování.

Jakmile účet povolen pro vícefaktorové ověřování, nemůžete se přihlásit k prostředkům řídí zásady vícefaktorového ověřování, dokud se úspěšně jste nakonfigurovali důvěryhodné zařízení pro druhý faktor ověřování a jste se ověřili pomocí dvoustupňového ověření.

Postupujte podle kroků v [Azure Multi-Factor Authentication význam pro mě nejlepší?](../user-help/multi-factor-authentication-end-user.md) k pochopení a správně nakonfigurovat zařízení pro vícefaktorové ověřování pomocí uživatelského účtu.

## <a name="install-and-configure-nps-extension"></a>Instalace a konfigurace rozšíření serveru NPS

Tato část obsahuje pokyny ke konfiguraci vzdálené plochy infrastruktury pro ověřování klienta s bránou vzdálení plochy pomocí Azure MFA.

### <a name="acquire-azure-active-directory-guid-id"></a>Získat Identifikátor GUID služby Azure Active Directory

Jako součást konfigurace rozšíření serveru NPS budete muset zadat přihlašovací údaje správce a Azure AD ID pro vašeho tenanta Azure AD. Následující kroky ukazují, jak získat ID tenanta.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) jako globální správce tenanta Azure.
1. V levém navigačním panelu vyberte **Azure Active Directory** ikonu.
1. Vyberte **vlastnosti**.
1. V okně Vlastnosti vedle ID adresáře, klikněte na tlačítko **kopírování** ikonu, jak je znázorněno níže, ID zkopírovat do schránky.

 ![Vlastnosti](./media/howto-mfa-nps-extension-rdg/image1.png)

### <a name="install-the-nps-extension"></a>Instalace rozšíření serveru NPS

Instalace rozšíření serveru NPS na serveru s nainstalovanou rolí síťové zásady a přístup k službám (NPS). Tato operace funguje jako server RADIUS pro návrh.

>[!Important]
> Ujistěte se, že nenainstalujete rozšíření NPS na serveru služby Brána vzdálené plochy.
>

1. Stáhněte si [rozšíření NPS](https://aka.ms/npsmfa). 
1. Zkopírujte spustitelný soubor instalace (NpsExtnForAzureMfaInstaller.exe) na server NPS.
1. Na serveru NPS, klikněte dvakrát na **NpsExtnForAzureMfaInstaller.exe**. Pokud se zobrazí výzva, klikněte na tlačítko **spustit**.
1. V dialogovém okně rozšíření NPS pro Azure MFA nastavení, přečtěte si licenční podmínky pro software, zkontrolujte **vyjadřuji souhlas s licenčními podmínkami a ujednáními**a klikněte na tlačítko **nainstalovat**.

  ![Nastavení Azure MFA](./media/howto-mfa-nps-extension-rdg/image2.png)

1. V dialogovém okně rozšíření NPS pro Azure MFA nastavení klikněte na tlačítko **Zavřít**.

  ![Rozšíření NPS pro Azure MFA](./media/howto-mfa-nps-extension-rdg/image3.png)

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Konfigurace certifikátů pro použití s rozšíření serveru NPS pomocí Powershellového skriptu

Dále je třeba nakonfigurovat certifikáty pro použití rozšíření NPS k zajištění zabezpečené komunikace a záruky. Server NPS součásti zahrnují skript prostředí Windows PowerShell, který se nakonfiguruje certifikát podepsaný svým držitelem pro použití se serverem NPS.

Skript provede následující akce:

* Vytvoří certifikát podepsaný svým držitelem
* Přidruží veřejný klíč certifikátu instančnímu objektu v Azure AD
* Ukládá certifikát v úložišti místního počítače
* Uděluje přístup k privátnímu klíči certifikátu do síťového uživatele
* Restartuje službu Network Policy Server

Pokud chcete použít vlastní certifikáty, musíte přidružit veřejný klíč certifikátu instančnímu objektu služby v Azure AD, a tak dále.

Pokud chcete použít skript, poskytují rozšíření pomocí svých přihlašovacích údajů správce Azure AD a ID tenanta Azure AD, který jste si zkopírovali dříve. Spusťte skript na každém serveru NPS, kam jste nainstalovali rozšíření serveru NPS. Potom udělejte následující:

1. Otevřete řádku prostředí Windows PowerShell pro správu.
1. Zadejte v příkazovém prostředí PowerShell `cd ‘c:\Program Files\Microsoft\AzureMfa\Config’`a stiskněte klávesu **ENTER**.
1. Typ `.\AzureMfaNpsExtnConfigSetup.ps1`a stiskněte klávesu **ENTER**. Skript zkontroluje, zda je nainstalován modul Powershellu pro Azure Active Directory. Pokud nainstalovaná není, skript nainstaluje modul pro vás.

  ![Prostředí PowerShell Azure AD](./media/howto-mfa-nps-extension-rdg/image4.png)
  
1. Jakmile skript ověří instalace modulu prostředí PowerShell, zobrazí dialogové okno modul Powershellu pro Azure Active Directory. V dialogovém okně zadejte svoje přihlašovací údaje správce Azure AD a heslo a klikněte na tlačítko **Sign In**.

  ![Otevřete prostředí Powershell účet](./media/howto-mfa-nps-extension-rdg/image5.png)

1. Po zobrazení výzvy, vložte ID tenanta, který jste si zkopírovali do schránky a stiskněte klávesu **ENTER**.

  ![Zadejte ID Tenanta](./media/howto-mfa-nps-extension-rdg/image6.png)

1. Tento skript vytvoří certifikát podepsaný svým držitelem a provede další změny v konfiguraci. Výstup by měl být podobně jako na následujícím obrázku.

  ![Certifikát podepsaný svým držitelem](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>Konfigurace součástí serveru NPS na Brána vzdálené plochy

V této části nakonfigurujete zásady autorizace připojení brány vzdálené plochy a další nastavení protokolu RADIUS.

Tok ověření vyžaduje, že zprávy protokolu RADIUS se vyměňují mezi Brána vzdálené plochy a na server NPS, kde je nainstalován NPS Server. To znamená, že nastavení klienta RADIUS musíte nakonfigurovat na Brána vzdálené plochy a instalaci rozšíření NPS server NPS.

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Nakonfigurujte zásady autorizace připojení brány vzdálené plochy pro použití centrální úložiště

Zásady autorizace připojení ke vzdálené ploše (CAP k vzdálené ploše) zadejte požadavky pro připojení k serveru služby Brána vzdálené plochy. CAP k vzdálené ploše můžete ukládat místně (výchozí) nebo může být uložený v centrální úložiště CAP ke vzdálené ploše, na kterém běží server NPS. Konfigurace integrace Azure MFA s vzdálené plochy, budete muset zadat příkaz use centrálního úložiště.

1. Na serveru služby Brána VP, otevřete **správce serveru**.
1. V nabídce klikněte na tlačítko **nástroje**, přejděte na **služby Vzdálená plocha**a potom klikněte na tlačítko **Správce brány vzdálené plochy**.

  ![Vzdálená plocha](./media/howto-mfa-nps-extension-rdg/image8.png)

1. V Správce brány VP klikněte pravým tlačítkem na  **\[název serveru\] (místní)** a klikněte na tlačítko **vlastnosti**.

  ![Název serveru](./media/howto-mfa-nps-extension-rdg/image9.png)

1. V dialogovém okně Vlastnosti vyberte **VP Zakončení Store** kartu.
1. Na kartě VP Zakončení Store vyberte **centrální server NPS**. 
1. V **zadejte název nebo IP adresu serveru NPS** zadejte IP adresu nebo server název serveru, kam jste nainstalovali rozšíření serveru NPS.

  ![Zadejte název nebo IP adresu](./media/howto-mfa-nps-extension-rdg/image10.png)
  
1. Klikněte na tlačítko **Add** (Přidat).
1. V **sdílený tajný klíč** dialogové okno, zadejte sdílený tajný klíč a potom klikněte na tlačítko **OK**. Zkontrolujte záznam sdílený tajný klíč a bezpečně uložit záznam.

 >[!NOTE]
 >Sdílený tajný klíč se používá k navázání vztahu důvěryhodnosti mezi klienty a servery RADIUS. Vytvoření tajného klíče dlouhá a složitá.
 >

 ![Sdílený tajný kód](./media/howto-mfa-nps-extension-rdg/image11.png)

1. Kliknutím na **OK** zavřete dialogové okno.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>Hodnota časového limitu protokolu RADIUS nakonfigurovat na NPS brány vzdálené plochy

Jak zajistit, že je čas na ověření přihlašovacích údajů uživatelů, provedení dvoustupňového ověřování, dostávat odpovědi a reagovat na zprávy pomocí protokolu RADIUS, je nutné upravit hodnoty časového limitu protokolu RADIUS.

1. Na serveru služby Brána VP otevřete Správce serveru. V nabídce klikněte na tlačítko **nástroje**a potom klikněte na tlačítko **Network Policy Server**. 
1. V **server NPS (místní)** rozbalte **klienti a servery RADIUS**a vyberte **vzdálený Server RADIUS**.

 ![Server Remote RADIUS](./media/howto-mfa-nps-extension-rdg/image12.png)

1. V podokně podrobností klikněte dvakrát na **TS GATEWAY SERVER GROUP**.

 >[!NOTE]
 >Tato skupina serveru RADIUS byla vytvořena při konfiguraci centrálního serveru NPS zásady. Brána VP přeposílá zprávy pomocí protokolu RADIUS na tento server nebo skupinu serverů, pokud více než jedna ve skupině.
 >

1. V **vlastností skupiny serverů brány TS** dialogového okna, vyberte IP adresu nebo název serveru NPS nakonfigurovat tak, aby úložiště CAP k vzdálené ploše a pak klikněte na tlačítko **upravit**. 

 ![Skupiny serverů brány TS](./media/howto-mfa-nps-extension-rdg/image13.png)

1. V **upravit Server protokolu RADIUS** dialogové okno, vyberte **Vyrovnávání zatížení** kartu.
1. V **Vyrovnávání zatížení** kartě **počet sekund bez odpovědi, než je žádost považována za zrušenou** pole, změňte výchozí hodnotu ze 3 na hodnotu 30 – 60 sekund.
1. V **počet sekund mezi požadavky, když je server identifikován jako nedostupný** pole, změňte výchozí hodnota 30 sekund na hodnotu, která je rovna nebo větší než hodnota zadaná v předchozím kroku.

 ![Upravit Server protokolu Radius](./media/howto-mfa-nps-extension-rdg/image14.png)

1. Klikněte na tlačítko **OK** dvakrát zavřete dialogová okna.

### <a name="verify-connection-request-policies"></a>Ověření zásad vyžádání nového připojení

Ve výchozím nastavení při konfiguraci brány VP pro použití zásady centrálního úložiště pro zásady autorizace připojení, je Brána VP nakonfigurované ke směrování žádostí Zakončení na server NPS. Server NPS pomocí rozšíření Azure MFA nainstalovali, zpracuje žádost o přístup protokolu RADIUS. Následující kroky ukazují, jak ověřit zásady vyžádání nového připojení výchozí.

1. Na bráně VP, v konzole serveru NPS (místní) rozbalte **zásady**a vyberte **zásady vyžádání nového připojení**.
1. Dvakrát klikněte na panel **TS GATEWAY AUTHORIZATION POLICY**.
1. V **TS GATEWAY AUTHORIZATION POLICY vlastnosti** dialogové okno, klikněte na tlačítko **nastavení** kartu.
1. Na **nastavení** klikněte na kartu podle předání požadavku na připojení **ověřování**. Klient protokolu RADIUS konfigurován pro směrování požadavků pro ověřování.

 ![Nastavení ověřování](./media/howto-mfa-nps-extension-rdg/image15.png)

1. Klikněte na tlačítko **zrušit**.

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>Konfigurace serveru NPS na serveru, kde je nainstalovaná rozšíření NPS

Server NPS, kde je nainstalovaná rozšíření NPS musí být schopen výměna zpráv pomocí protokolu RADIUS se serverem NPS na Brána vzdálené plochy. Pokud chcete povolit tento výměně zpráv, musíte nakonfigurovat komponenty NPS na serveru, ve kterém je nainstalovaná služba rozšíření NPS.

### <a name="register-server-in-active-directory"></a>Registrace serveru ve službě Active Directory

Fungovat správně v tomto scénáři, NPS server musí být zaregistrované ve službě Active Directory.

1. Na serveru NPS otevřete **správce serveru**.
1. Ve Správci serveru klikněte na tlačítko **nástroje**a potom klikněte na tlačítko **Network Policy Server**.
1. V konzole serveru Network Policy Server klikněte pravým tlačítkem na **server NPS (místní)** a potom klikněte na tlačítko **zaregistrovat server ve službě Active Directory**.
1. Klikněte na tlačítko **OK** dvakrát.

 ![Registrace serveru ve službě AD](./media/howto-mfa-nps-extension-rdg/image16.png)

1. Nechte konzolu otevřený pro další postup.

### <a name="create-and-configure-radius-client"></a>Vytvoření a konfigurace klienta protokolu RADIUS

Brána vzdálené plochy musí být nakonfigurován jako klienta RADIUS serveru NPS.

1. Na serveru NPS, kde NPS je rozšíření nainstalované, v **server NPS (místní)** konzoly, klikněte pravým tlačítkem na **klientů RADIUS** a klikněte na tlačítko **nový**.

 ![Noví klienti RADIUS](./media/howto-mfa-nps-extension-rdg/image17.png)

1. V **nový klient protokolu RADIUS** dialogového okna zadejte popisný název, jako například _brány_a IP adresu nebo název DNS serveru brány vzdálené plochy. 
1. V **sdílený tajný klíč** a **potvrzení sdíleného tajného klíče** pole, zadejte stejnou tajný klíč, který jste použili dříve.

 ![Název a adresu](./media/howto-mfa-nps-extension-rdg/image18.png)

1. Klikněte na tlačítko **OK** zavřete dialogové okno Nový klient protokolu RADIUS.

### <a name="configure-network-policy"></a>Konfigurovat zásady sítě

Připomínáme, že server NPS pomocí rozšíření Azure MFA je úložiště určené centrální zásady pro povolení zásad připojení (CAP). Proto budete muset implementovat LIMITU na serveru NPS k autorizaci požadavků na platné připojení.  

1. Na serveru NPS otevřete konzoly serveru NPS (místní počítač), rozbalte položku **zásady**a klikněte na tlačítko **zásady sítě**.
1. Klikněte pravým tlačítkem na **připojení k jiným serverům přístup**a klikněte na tlačítko **duplikovat zásadu**.

 ![Duplicitní zásad](./media/howto-mfa-nps-extension-rdg/image19.png)

1. Klikněte pravým tlačítkem na **kopírování připojení k jiným serverům přístup**a klikněte na tlačítko **vlastnosti**.

 ![Vlastnosti sítě](./media/howto-mfa-nps-extension-rdg/image20.png)

1. V **kopírování připojení k jiným serverům přístup** v dialogu **Název_zásady**, zadejte vhodný název, jako například _RDG_CAP_. Zkontrolujte **povolenou zásadou**a vyberte **udělit přístup**. Volitelně můžete v **typu server síťového přístupu**vyberte **Brána vzdálené plochy**, nebo můžete ponechat jako **nespecifikovaný**.

 ![Kopie připojení](./media/howto-mfa-nps-extension-rdg/image21.png)

1. Klikněte na tlačítko **omezení** kartě a zaškrtněte **povolit klientům připojit se bez vyjednávání metodu ověřování**.

 ![Povolit klientům připojit se](./media/howto-mfa-nps-extension-rdg/image22.png)

1. Případně můžete kliknout **podmínky** karta a přidat podmínky, které musí být splněny pro připojení k autorizaci, například členství v určité skupině Windows.

 ![Podmínky](./media/howto-mfa-nps-extension-rdg/image23.png)

1. Klikněte na **OK**. Po zobrazení výzvy k zobrazení příslušné téma nápovědy, klikněte na tlačítko **ne**.
1. Ujistěte se, že nové zásady se v horní části seznamu, že je zásada povolená, a že uděluje přístup.

 ![Zásady sítě](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Ověření konfigurace

Pokud chcete ověřit konfiguraci, budete muset přihlásit do služby Brána vzdálené plochy pomocí vhodné klienta protokolu RDP. Nezapomeňte použít účet, který povoluje vaše zásady autorizace připojení a je povolen pro Azure MFA.

Jak je vidět na obrázku níže, můžete použít **Remote Desktop Web Access** stránky.

![Remote Desktop Web Access](./media/howto-mfa-nps-extension-rdg/image25.png)

Po úspěšném zadání přihlašovacích údajů pro primární ověřování, dialogové okno připojení k vzdálené ploše zobrazí stav spuštění vzdáleného připojení, jak je znázorněno níže. 

Pokud jste úspěšně ověřit pomocí sekundární ověřování, které jste dříve nakonfigurovali v Azure MFA, jste připojení k prostředku. Pokud sekundární ověření není úspěšné, ale je odepřen přístup k prostředku. 

![Inicializace vzdáleného připojení](./media/howto-mfa-nps-extension-rdg/image26.png)

V následujícím příkladu se používá ověřovací aplikaci na Windows phone pro sekundární ověřování.

![Účty](./media/howto-mfa-nps-extension-rdg/image27.png)

Jakmile úspěšně jste se ověřili pomocí metody sekundární ověřování, jste přihlášení jako za normálních okolností Brána vzdálené plochy. Protože je potřeba použít metodu sekundární ověření pomocí mobilní aplikace na důvěryhodné zařízení, procesu přihlašování ale bezpečnější, než by jinak.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Zobrazit protokoly Prohlížeče událostí pro události úspěšného přihlášení

Zobrazení událostí úspěšného přihlášení v protokolech prohlížeče událostí Windows, můžete použít následující příkaz prostředí Windows PowerShell, který dotazování protokolů zabezpečení Windows a Windows Terminálové služby.

K dotazování událostí úspěšného přihlášení v provozních protokolech brány _(událost prohlížeč událostí\protokoly aplikací a služeb Logs\Microsoft\Windows\TerminalServices-Gateway\Operational)_, použijte následující příkazy Powershellu:

* _Get-WinEvent – název protokolu Microsoft-Windows-TerminalServices – brána/provozní_ | kde {$_.ID - eq "300"} | USA 
* Tento příkaz zobrazí události Windows, které uživatel splněny požadavky na zásady autorizace prostředků (VP pro autorizaci prostředků) a má umožněný přístup.

![Zobrazení prohlížeče událostí](./media/howto-mfa-nps-extension-rdg/image28.png)

* _Get-WinEvent – název protokolu Microsoft-Windows-TerminalServices – brána/provozní_ | kde {$_.ID - eq "200"} | USA
* Tento příkaz zobrazí události, které zobrazit, když uživatel splněny požadavky na zásady autorizace připojení.

![Ověření připojení](./media/howto-mfa-nps-extension-rdg/image29.png)

Můžete také zobrazit tento protokol a filtru na události s ID, 300 a 200. K dotazování událostí úspěšného přihlášení v protokolech prohlížeče událostí zabezpečení, použijte následující příkaz:

* _Get-WinEvent – název protokolu zabezpečení_ | kde {$_.ID - eq '6272'} | USA 
* Tento příkaz můžete spustit na centrální server NPS nebo Server brány VP. 

![Události úspěšného přihlášení](./media/howto-mfa-nps-extension-rdg/image30.png)

Můžete také zobrazit v protokolu zabezpečení nebo vlastní zobrazení síťové zásady a přístup ke službám, jak je znázorněno níže:

![Službu síťové zásady a přístup](./media/howto-mfa-nps-extension-rdg/image31.png)

Na serveru, kam jste nainstalovali rozšíření NPS pro Azure MFA, můžete najít protokoly Prohlížeče událostí aplikace specifické pro rozšíření na _aplikace a služby Logs\Microsoft\AzureMfa_. 

![Protokoly událostí pro aplikace prohlížeče](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Řešení potíží s Průvodce

Pokud konfiguraci nefunguje podle očekávání, je prvním místem, kde začít řešení potíží s Ověřte, že je správce nakonfigurován na použití Azure MFA. Požádejte uživatele, připojte se k [webu Azure portal](https://portal.azure.com). Pokud je uživatelé vyzváni k zadání sekundární ověřovací a můžete úspěšně ověřit, můžete eliminovat nesprávnou konfiguraci Azure MFA.

Pokud Azure MFA funguje pro uživatele, přečtěte si relevantní protokoly událostí. Patří mezi ně zabezpečení, provozní brány, protokoly událostí a Azure MFA, které jsou popsané v předchozí části. 

Níže je příklad výstupu protokolu zabezpečení znázorňující neúspěšné událostí (událost 6273 ID).

![Událost neúspěšných přihlášení](./media/howto-mfa-nps-extension-rdg/image33.png)

Níže je související události z protokolů AzureMFA:

![Protokolů Azure MFA](./media/howto-mfa-nps-extension-rdg/image34.png)

K provádění rozšířené možnosti řešení problémů, zkontrolujte soubory protokolu serveru NPS databáze v formát, ve kterém je nainstalovaná služba NPS. Tyto soubory protokolu se vytvoří v _%SystemRoot%\System32\Logs_ složky jako soubory text oddělený čárkami. 

Popis těchto souborů protokolu najdete v tématu [interpretovat NPS formátu soubory protokolů databáze](https://technet.microsoft.com/library/cc771748.aspx). Položky v těchto protokolových souborech může být obtížné pro interpretaci bez jejich importování do tabulky nebo databáze. Pomoc při interpretaci souborů protokolu můžete najít několik služby ověřování v Internetu analyzátory online. 

Následující obrázek ukazuje výstup těchto ke stažení [shareware aplikace](https://www.deepsoftware.com/iasviewer). 

![Shareware aplikace](./media/howto-mfa-nps-extension-rdg/image35.png)

Nakonec pro další řešení potíží s možností, můžete pomocí analyzátoru protokolu, takový [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx). 

Na obrázku níže z Microsoft Message Analyzer znázorňuje přenos v síti filtruje protokolem RADIUS, který obsahuje uživatelské jméno **CONTOSO\AliceC**.

![Microsoft Message Analyzer](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>Další postup

[Jak získat službu Azure Multi-Factor Authentication](concept-mfa-licensing.md)

[Brána vzdálené plochy Azure Multi-Factor Authentication Server pomocí protokolu RADIUS](howto-mfaserver-nps-rdg.md)

[Integrace místních adresářů do služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
