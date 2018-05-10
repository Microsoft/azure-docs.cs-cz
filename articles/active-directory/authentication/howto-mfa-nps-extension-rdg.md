---
title: Vzdálená plocha brány integrace s Azure MFA serveru NPS rozšíření | Microsoft Docs
description: Integrace infrastruktury Brána vzdálené plochy s Azure MFA pomocí Network Policy Server rozšíření pro Microsoft Azure
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 08/15/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: fbca491e845ad96ecafc6afb5f40d46b99c047bf
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>Integrace infrastruktury Brána vzdálené plochy pomocí rozšíření serveru NPS (Network Policy Server) a Azure AD

Tento článek obsahuje podrobné informace pro integraci infrastruktury Brána vzdálené plochy Azure Multi-Factor Authentication (MFA) pomocí serveru NPS (Network Policy Server) rozšíření pro Microsoft Azure. 

Rozšíření serveru NPS (Network Policy Server) pro Azure umožňuje zákazníkům, aby se předešlo ověření klienta RADIUS Remote Authentication Dial-In User Service () pomocí Azure je založená na cloudu [Multi-Factor Authentication (MFA)](multi-factor-authentication.md). Toto řešení poskytuje dvoustupňové ověřování pro přidání druhé vrstvy zabezpečení do uživatelská přihlášení a transakce.

Tento článek obsahuje podrobné pokyny k integraci s Azure MFA serveru NPS infrastruktury pomocí serveru NPS rozšíření pro Azure. To umožňuje zabezpečeného ověřování pro uživatele došlo k pokusu o přihlášení k Brána vzdálené plochy. 

Síťové zásady a přístup služby (NPS) poskytuje organizacím umožňuje postupujte takto:
* Definujte centrální umístění pro správu a řízení požadavků sítě tak, že zadáte, kdo se může připojit, jaké doby připojení den jsou povolené a dobu trvání připojení a úroveň zabezpečení, které musí klienti používat pro připojení a tak dále. Místo zadání těchto zásad na každý server sítě VPN nebo brány vzdálené plochy (RD), tyto zásady můžete zadat jednou v centrálním umístění. Protokol RADIUS poskytuje centralizované ověřování, autorizace a monitorování (AAA). 
* Stanovit a vynutit zásady stavu klienta Network Access Protection (NAP), které určují, jestli zařízení mají neomezeného nebo omezeného přístupu k síťovým prostředkům.
* Zadejte způsob k vynucení ověřování a autorizace pro přístup k protokolu 802. 1 x podporující bezdrátové přístupové body a přepínače sítě Ethernet.    

Obvykle zásady organizace použijte RADIUS serveru NPS () a zjednodušit centralizovat správu sítě VPN. Mnoho organizací však použít také NPS zjednodušují a centralizovat správu zásady autorizace připojení plochy vzdálené ploše (VP CAP). 

Organizace může také integrovat Azure MFA a zvýšit zabezpečení a zajistit vysokou úroveň dodržování předpisů serveru NPS. To pomáhá zajistit, že uživatelé vytvořit dvoustupňové ověřování pro přihlášení k služby Brána vzdálené plochy. Uživatelům udělen přístup musí zadat jejich kombinace uživatelského jména a hesla společně s informacemi, které má uživatel v jejich řízení. Tyto informace musí být důvěryhodný a duplikovaná není snadno, například číslo mobilního telefonu, číslo pevné, aplikace na mobilní zařízení a tak dále.

Před dostupnost serveru NPS rozšíření pro Azure, zákazníky, kteří si přáli implementovat dvoustupňové ověřování pro integrované prostředí NPS a Azure MFA museli konfigurovat a spravovat samostatný Server MFA v místním prostředí, jak je uvedeno v [Brána vzdálené plochy a pomocí protokolu RADIUS serveru Azure Multi-Factor Authentication Server](howto-mfaserver-nps-rdg.md).

Dostupnost serveru NPS rozšíření pro Azure teď poskytuje organizacím rozhodnout pro nasazení do řešení vícefaktorového ověřování pro místní nebo cloudové řešení vícefaktorového ověřování do zabezpečeného ověřování klienta protokolu RADIUS.

## <a name="authentication-flow"></a>Tok ověřování

Uživatelům udělen přístup k síťovým prostředkům prostřednictvím služby Brána vzdálené plochy musí splňovat podmínky uvedené v jedné VP zásady autorizace připojení (RD CAP) a zásady pro prostředek autorizace jeden vzdálené ploše (VP pro autorizaci prostředků). VP CAP k vzdálené ploše určit, kteří uživatelé mají oprávnění pro připojení k VP brány. Zásady VP pro autorizaci prostředků zadejte síťovým prostředkům, jako je vzdálené plochy nebo vzdálené aplikace, které uživatele dovoleno připojovat prostřednictvím brány VP. 

Bránu VP lze nakonfigurovat pro použití zásady centrálního úložiště pro VP CAP k vzdálené ploše. Zásady VP pro autorizaci prostředků nelze použít centrální zásady, jak se zpracovávají na bráně VP. Příklad Brána VP nakonfigurována pro použití zásady centrálního úložiště pro VP CAP k vzdálené ploše je klienta RADIUS na jiný server NPS, který slouží jako úložiště centrální zásady.

Když NPS rozšíření pro Azure je integrována se serverem NPS a Brána vzdálené plochy, tok úspěšné ověření vypadá takto:

1. Server služby Brána vzdálené plochy obdrží požadavek na ověření z uživatele vzdálené plochy pro připojení k prostředku, jako je například relaci vzdálené plochy. Funguje jako klienta protokolu RADIUS, serveru brány vzdálené plochy převede požadavek na zprávu protokolu RADIUS – žádost o přístup a odešle zprávu na server RADIUS (NPS), kde je nainstalován server NPS rozšíření. 
2. Kombinace uživatelského jména a hesla je ověřen ve službě Active Directory a ověření uživatele.
3. Pokud jsou splněny všechny podmínky zadané v žádosti o připojení serveru NPS a zásady sítě (například čas, den nebo skupiny omezeními pro členství vyplývajícími), NPS rozšíření aktivuje žádost o sekundárního ověřování s Azure MFA. 
4. Azure MFA komunikuje s Azure AD, načte podrobnosti uživatele a provádí sekundární ověřování metodou nakonfigurovaná uživatelem (textová zpráva, mobilní aplikace a tak dále). 
5. Po úspěšné ověřovací test MFA Azure MFA komunikuje výsledek, který má rozšíření serveru NPS.
6. Server NPS, kde je rozšíření nainstalovat, odešle zprávu RADIUS s potvrzením přístupu zásady CAP k vzdálené ploše na server služby Brána vzdálené plochy.
7. Uživateli je udělen přístup k požadované síti prostředku prostřednictvím brány VP.

## <a name="prerequisites"></a>Požadavky
Tato část podrobně předpoklady nezbytné před integrace Azure MFA s služby Brána vzdálené plochy. Než začnete, musí mít následující požadavky na místě.  

* Vzdálené infrastruktury služby plocha (RDS)
* Licence Azure MFA
* Software Windows serveru
* Role Síťové zásady a přístup služby NPS)
* Azure Active Directory synchronizována s místní služby Active Directory
* ID identifikátor GUID služby Azure Active Directory

### <a name="remote-desktop-services-rds-infrastructure"></a>Vzdálené infrastruktury služby plocha (RDS)
Musíte mít funkční infrastrukturu služby Vzdálená plocha (RDS) na místě. Pokud ho použít nechcete, pak můžete rychle vytvořit tuto infrastrukturu v Azure pomocí následující šablony rychlý start: [vytvoření kolekce relací vzdálené plochy nasazení](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment). 

Pokud chcete ručně vytvořit místní infrastruktury RDS rychle pro účely testování, postupujte podle kroků pro nasazení jednoho. 
**Další informace**: [nasazení vzdálené plochy s Azure rychlý start](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-in-azure) a [nasazení infrastruktury základní RDS](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure). 

### <a name="azure-mfa-license"></a>Licence Azure MFA
Vyžaduje se licenci pro Azure MFA, která je k dispozici prostřednictvím Azure AD Premium, Enterprise Mobility plus Security (EMS) nebo předplatné vícefaktorového ověřování. Na základě spotřeby licencí pro Azure MFA, například podle uživatele nebo na ověřování licence nejsou kompatibilní s příponou serveru NPS. Další informace najdete v tématu [jak získat Azure Multi-Factor Authentication](concept-mfa-licensing.md). Pro účely testování můžete použít zkušební verzi předplatného. 

### <a name="windows-server-software"></a>Software Windows serveru
Rozšíření serveru NPS vyžaduje Windows Server 2008 R2 SP1 nebo novějším s nainstalovanou službou role NPS. Všechny kroky v této části se provádí pomocí systému Windows Server 2016.

### <a name="network-policy-and-access-services-nps-role"></a>Role Síťové zásady a přístup služby NPS)
Služba role NPS poskytuje protokolu RADIUS serveru a klienta, funkce a také zásady přístupu k síti služby stavu. Musí být nainstalovaná tato role na alespoň dva počítače ve vaší infrastruktuře: Brána vzdálené plochy a druhý členský server nebo řadič domény. Ve výchozím nastavení role již existuje v počítač nakonfigurovaný jako Brána vzdálené plochy.  Také nainstalujte NPS role na alespoň na jiném počítači, jako jsou řadiče domény nebo členském serveru.

Informace o instalaci role serveru NPS služby Windows Server 2012 nebo starší, najdete v části [nainstalovat Server zásad stavu NAP](https://technet.microsoft.com/library/dd296890.aspx). Popis osvědčené postupy pro server NPS, včetně doporučení k instalaci NPS na řadiči domény najdete v části [osvědčené postupy pro server NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Azure Active Directory synchronizována s místní služby Active Directory
Použití rozšíření serveru NPS, musí být místních uživatelů se synchronizují s Azure AD a povolené pro MFA. V této části se předpokládá, že jsou synchronizována místních uživatelů s Azure AD pomocí služby AD Connect. Informace o Azure AD connect, najdete v části [integraci místních adresářů se službou Azure Active Directory](../connect/active-directory-aadconnect.md). 

### <a name="azure-active-directory-guid-id"></a>ID identifikátor GUID služby Azure Active Directory
Pokud chcete nainstalovat rozšíření serveru NPS, musíte znát identifikátor GUID služby Azure AD. Pokyny k nalezení GUID Azure AD jsou uvedeny níže.

## <a name="configure-multi-factor-authentication"></a>Konfigurace služby Multi-Factor Authentication 
Tato část obsahuje pokyny k integraci Azure MFA s služby Brána vzdálené plochy. Jako správce musíte konfigurovat službu Azure MFA, než mohou uživatelé registrovat svoje zařízení Multi-Factor a aplikace.

Postupujte podle kroků v [Začínáme s Azure Multi-Factor Authentication v cloudu](howto-mfa-getstarted.md) jak zapnout MFA pro uživatele Azure AD. 

### <a name="configure-accounts-for-two-step-verification"></a>Konfigurace účtů pro dvoustupňové ověření
Jakmile účet povolen pro MFA, nemůžete se přihlásit k prostředkům řídí podle zásad vícefaktorového ověřování, dokud úspěšně jste nakonfigurovali důvěryhodné zařízení k použití pro druhý faktor ověřování a ověřené pomocí dvoustupňového ověření.

Postupujte podle kroků v [co Azure Multi-Factor Authentication znamená pro mě nejlepší?](./../../multi-factor-authentication/end-user/multi-factor-authentication-end-user.md) pochopit a správně nakonfigurovat zařízení pro MFA s vaším uživatelským účtem.

## <a name="install-and-configure-nps-extension"></a>Instalace a konfigurace serveru NPS rozšíření
Tato část obsahuje pokyny ke konfiguraci vzdálené plochy infrastruktury pro použití Azure MFA pro ověřování klientů pomocí služby Brána vzdálené plochy.

### <a name="acquire-azure-active-directory-guid-id"></a>Získání ID identifikátor GUID služby Azure Active Directory

Jako součást konfigurace serveru NPS rozšíření budete muset zadat přihlašovací údaje správce a Azure AD ID pro vašeho tenanta Azure AD. Následující kroky vám ukážou, jak získat ID klienta.

1. Přihlaste se k [portál Azure](https://portal.azure.com) jako globální správce Azure klienta.
2. V levém navigačním panelu, vyberte **Azure Active Directory** ikonu.
3. Vyberte **vlastnosti**.
4. V okně Vlastnosti vedle ID adresáře, klikněte **kopie** ikonu, jak je ukázáno níže ID zkopírovat do schránky.

 ![Vlastnosti](./media/howto-mfa-nps-extension-rdg/image1.png)

### <a name="install-the-nps-extension"></a>Nainstalujte rozšíření serveru NPS
Nainstalujte rozšíření serveru NPS na serveru s nainstalovanou rolí síťové zásady a přístup služby (NPS). To funguje jako server RADIUS pro váš návrh. 

>[!Important]
> Ujistěte se, že neinstalovat rozšíření serveru NPS na serveru služby Brána vzdálené plochy.
> 

1. Stažení [NPS rozšíření](https://aka.ms/npsmfa). 
2. Zkopírujte instalační spustitelný soubor (NpsExtnForAzureMfaInstaller.exe) do serveru NPS.
3. Na serveru NPS, dvakrát klikněte na **NpsExtnForAzureMfaInstaller.exe**. Pokud se zobrazí výzva, klikněte na tlačítko **spustit**.
4. V dialogovém okně NPS rozšíření pro Azure MFA nastavení zkontrolovat licenční podmínky pro software, zkontrolujte **souhlasím s licenční podmínky a ujednání**a klikněte na tlačítko **nainstalovat**.
 
  ![Nastavení Azure MFA](./media/howto-mfa-nps-extension-rdg/image2.png)

5. V dialogovém okně NPS rozšíření pro Azure MFA nastavení, klikněte na **Zavřít**. 

  ![Server NPS rozšíření pro Azure MFA](./media/howto-mfa-nps-extension-rdg/image3.png)

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Konfigurace certifikátů pro použití s příponou NPS pomocí skriptu prostředí PowerShell
Dále musíte nakonfigurovat certifikáty pro použití rozšíření NPS k zajištění zabezpečené komunikace a záruky. Součásti serveru NPS zahrnují skript prostředí Windows PowerShell, který konfiguruje certifikát podepsaný svým držitelem pro použití se serverem NPS. 

Skript provede následující akce:

* Vytvoří certifikát podepsaný svým držitelem
* Přidruží veřejný klíč certifikátu instančního objektu v Azure AD
* Ukládá certifikát v úložišti místního počítače
* Uděluje přístup k privátní klíč certifikátu do síťového uživatele
* Restartuje službu serveru zásad sítě

Pokud chcete používat vlastní certifikáty, musíte přidružit veřejný klíč certifikátu do objektu služby v Azure AD a tak dále.

Chcete-li použít skript, poskytnout rozšíření pomocí svých přihlašovacích údajů správce Azure AD a ID klienta Azure AD, který jste zkopírovali dříve. Spuštění skriptu na každý server NPS, kam jste nainstalovali rozšíření serveru NPS. Potom udělejte následující:

1. Otevřete řádku prostředí Windows PowerShell pro správu.
2. Zadejte v příkazovém prostředí PowerShell, `cd ‘c:\Program Files\Microsoft\AzureMfa\Config’`a stiskněte klávesu **ENTER**.
3. Typ `.\AzureMfsNpsExtnConfigSetup.ps1`a stiskněte klávesu **ENTER**. Skript zkontroluje, zda je nainstalován modul Azure Active Directory PowerShell. Pokud nainstalovaná není, skript nainstaluje modul pro vás.

  ![Azure AD PowerShell](./media/howto-mfa-nps-extension-rdg/image4.png)
  
4. Po skript ověřuje instalace modulu prostředí PowerShell, se zobrazí dialogové okno modul Azure Active Directory PowerShell. V dialogovém okně zadejte přihlašovací údaje Správce služby Azure AD a heslo a klikněte na tlačítko **přihlásit**.

  ![Otevřete prostředí Powershell účet](./media/howto-mfa-nps-extension-rdg/image5.png)

5. Po zobrazení výzvy, vložte ID klienta, který jste zkopírovali dříve do schránky a stiskněte klávesu **ENTER**.

  ![Zadejte ID klienta](./media/howto-mfa-nps-extension-rdg/image6.png)

6. Tento skript vytvoří certifikát podepsaný svým držitelem a provede další změny v konfiguraci. Výstup by měl být jako obrázek vidíte níže.

  ![Certifikát podepsaný sám sebou](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>Konfigurace součástí serveru NPS na Brána vzdálené plochy
V této části nakonfigurujete zásady autorizace připojení Brána vzdálené plochy a další nastavení protokolu RADIUS.

Tok ověření vyžaduje, že zprávy protokolu RADIUS se vyměňují mezi služby Brána vzdálené plochy a na server NPS, kde je nainstalován NPS Server. To znamená, že musíte nakonfigurovat nastavení klientů RADIUS na Brána vzdálené plochy a na server NPS, kde je nainstalován server NPS rozšíření. 

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Nakonfigurujte zásady autorizace připojení Brána vzdálené plochy pro použití centrální úložiště
Zásady autorizace připojení ke vzdálené ploše (VP CAP) zadejte požadavky pro připojení k serveru služby Brána vzdálené plochy. VP CAP k vzdálené ploše lze ukládat místně (výchozí), nebo mohou být uloženy v centrální úložiště CAP k vzdálené ploše, které běží server NPS. Konfigurace integrace Azure mfa s vzdálené plochy, budete muset zadat použití centrální úložiště.

1. Na serveru služby Brána VP, otevřete **správce serveru**. 
2. V nabídce klikněte na tlačítko **nástroje**, přejděte na příkaz **služby Vzdálená plocha**a potom klikněte na **Správce brány vzdálené plochy**.

  ![Vzdálená plocha](./media/howto-mfa-nps-extension-rdg/image8.png)

3. V okně Správce brány vzdálené plochy klikněte pravým tlačítkem na  **\[název serveru\] (místní)** a klikněte na tlačítko **vlastnosti**.

  ![Název serveru](./media/howto-mfa-nps-extension-rdg/image9.png)

4. V dialogovém okně Vlastnosti vyberte **úložiště CAP k vzdálené ploše** kartě.
5. Na kartě úložiště CAP k vzdálené ploše vyberte **centrálního serveru NPS**. 
6. V **zadejte název nebo IP adresu pro server, na kterém běží server NPS** pole, zadejte IP adresu nebo server název serveru, kam jste nainstalovali rozšíření serveru NPS.

  ![Zadejte název nebo IP adresu](./media/howto-mfa-nps-extension-rdg/image10.png)
  
7. Klikněte na tlačítko **Add** (Přidat).
8. V **sdílený tajný klíč** dialogové okno, zadejte sdílený tajný klíč a pak klikněte na tlačítko **OK**. Ujistěte se, zaznamenejte tento sdílený tajný klíč a bezpečně uložit záznam.

 >[!NOTE]
 >Sdílený tajný klíč slouží k navázání vztahu důvěryhodnosti mezi klienty a servery RADIUS. Vytvoření tajného klíče dlouhá a složitá.
 >

 ![Sdílený tajný kód](./media/howto-mfa-nps-extension-rdg/image11.png)

9. Kliknutím na **OK** zavřete dialogové okno.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>Hodnota časového limitu protokolu RADIUS nakonfigurovat na NPS brány vzdálené plochy
Ujistěte se, že je na čase ověření pověření uživatele, proveďte dvoustupňové ověření, příjem odpovědí a reagovat na zprávy protokolu RADIUS, je potřeba nastavit hodnotu časového limitu protokolu RADIUS.

1. Na serveru služby Brána VP spusťte Správce serveru. V nabídce klikněte na tlačítko **nástroje**a potom klikněte na **Network Policy Server**. 
2. V **server NPS (místní)** rozbalte **klienti a servery RADIUS**a vyberte **vzdálený Server RADIUS**.

 ![Vzdálený Server RADIUS](./media/howto-mfa-nps-extension-rdg/image12.png)

3. V podokně podrobností klikněte dvakrát na **skupiny serverů brány TS**.

 >[!NOTE]
 >Této skupiny serverů RADIUS byla vytvořena, když jste nakonfigurovali centrální server pro zásady serveru NPS. Brána VP přeposílá zprávy protokolu RADIUS na tento server nebo skupiny serverů, pokud je více než jeden ve skupině.
 >

4. V **vlastností skupiny serverů brány TS** dialogovém okně vyberte IP adresu nebo název serveru NPS nakonfigurovat tak, aby úložiště CAP k vzdálené ploše a potom klikněte na **upravit**. 

 ![Skupiny serverů brány TS](./media/howto-mfa-nps-extension-rdg/image13.png)

5. V **upravit Server RADIUS** dialogové okno, vyberte **Vyrovnávání zatížení** kartě.
6. V **Vyrovnávání zatížení** ve **počet sekund bez odpovědi, než je žádost považována za zrušených** pole, změňte výchozí hodnotu od 3 do hodnotu v rozmezí 30 až 60 sekund.
7. V **počet sekund mezi požadavky, když je server identifikován jako nedostupný** pole, změňte výchozí hodnotu 30 sekund na hodnotu, která je rovna nebo větší než hodnota zadaná v předchozím kroku.

 ![Upravit Radius Server](./media/howto-mfa-nps-extension-rdg/image14.png)

8.  Klikněte na tlačítko **OK** dvakrát zavřete dialogová okna.

### <a name="verify-connection-request-policies"></a>Ověření zásad vyžádání nového připojení 
Ve výchozím nastavení při konfiguraci brány VP pro použití zásady centrálního úložiště pro zásady autorizace připojení, je Brána VP nakonfigurována pro předávání požadavků Zakončení na serveru NPS. Server NPS s příponou Azure MFA, která je nainstalovaná, zpracuje žádost o přístup protokolu RADIUS. Následující kroky ukazují, jak ověřit zásady vyžádání nového připojení výchozí. 

1. Na bráně VP v konzole serveru NPS (místní) rozbalte **zásady**a vyberte **zásady vyžádání nového připojení**.
2. Klikněte pravým tlačítkem na **zásady vyžádání nového připojení**a dvakrát klikněte na **ZÁSADA AUTORIZACE brány TS**.
3. V **ZÁSADA AUTORIZACE brány TS vlastnosti** dialogové okno, klikněte **nastavení** kartě.
4. Na **nastavení** karta, v části Přesměrování požadavku na připojení, klikněte na tlačítko **ověřování**. Klient protokolu RADIUS je nakonfigurovaná pro směrování požadavků pro ověřování.

 ![Nastavení ověřování](./media/howto-mfa-nps-extension-rdg/image15.png)
 
5. Klikněte na tlačítko **zrušit**. 

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>Konfigurace serveru NPS na serveru s nainstalovanou rozšíření serveru NPS
Server NPS, kde je nainstalován server NPS rozšíření musí být schopny vyměňovat zprávy protokolu RADIUS serveru NPS na služby Brána vzdálené plochy. Pokud chcete povolit toto exchange zprávu, musíte konfigurovat součásti serveru NPS na serveru s nainstalovanou službu NPS rozšíření. 

### <a name="register-server-in-active-directory"></a>Registrace serveru ve službě Active Directory
V tomto scénáři, fungovat správně, je potřeba zaregistrovat ve službě Active Directory serveru NPS.

1. Na serveru NPS otevřete **správce serveru**.
2. Ve Správci serveru klikněte na tlačítko **nástroje**a potom klikněte na **Network Policy Server**. 
3. V konzole serveru NPS klikněte pravým tlačítkem na **server NPS (místní)** a potom klikněte na **server zaregistrovat ve službě Active Directory**. 
4. Klikněte na tlačítko **OK** dvakrát.

 ![Registrace serveru ve službě Active Directory](./media/howto-mfa-nps-extension-rdg/image16.png)

5. Ponechte konzole otevřené pro následující postup.

### <a name="create-and-configure-radius-client"></a>Vytvoření a konfigurace klienta protokolu RADIUS 
Brána vzdálené plochy musí být nakonfigurovaný jako klient protokolu RADIUS na server NPS. 

1. Na serveru NPS, kde serveru NPS je rozšíření nainstalované, v **server NPS (místní)** konzoly, klikněte pravým tlačítkem na **klientů RADIUS** a klikněte na tlačítko **nový**.

 ![Noví klienti RADIUS](./media/howto-mfa-nps-extension-rdg/image17.png)

2. V **nový klient protokolu RADIUS** dialogové okno pole, zadejte popisný název, jako třeba _brány_a IP adresu nebo název DNS serveru brány vzdálené plochy. 
3. V **sdílený tajný klíč** a **potvrzení sdíleného tajného klíče** pole, zadejte stejný tajný klíč, který jste používali před.

 ![Název a adresu](./media/howto-mfa-nps-extension-rdg/image18.png)

4. Klikněte na tlačítko **OK** zavřete dialogové okno Nový klient protokolu RADIUS.

### <a name="configure-network-policy"></a>Konfigurace zásad sítě
Odvolat, zda je server NPS s příponou Azure MFA úložišti určené centrální zásady pro zásady autorizace připojení (CAP). Proto je třeba implementovat CAP na serveru NPS k autorizaci požadavků na platné připojení.  

1. Ve stromu konzoly NPS (místní počítač), rozbalte položku **zásady**a klikněte na tlačítko **zásady sítě**.
2. Klikněte pravým tlačítkem na **připojení k jiným serverům přístup**a klikněte na tlačítko **duplicitní zásady**. 

 ![Duplicitní zásad](./media/howto-mfa-nps-extension-rdg/image19.png)

3. Klikněte pravým tlačítkem na **kopírování připojení k jiným serverům přístup**a klikněte na tlačítko **vlastnosti**.

 ![Vlastnosti sítě](./media/howto-mfa-nps-extension-rdg/image20.png)

4. V **kopírování připojení k jiným serverům přístup** dialogu **název zásady**, zadejte vhodný název, například _RDG_CAP_. Zkontrolujte **povolenou zásadu**a vyberte **udělit přístup**. Volitelně můžete v **typ serveru přístup k síti**, vyberte **Brána vzdálené plochy**, nebo můžete nechat jej jako **nespecifikovaný**.

 ![Kopii připojení](./media/howto-mfa-nps-extension-rdg/image21.png)

5.  Klikněte **omezení** a zkontrolujte **povolit klientům připojit bez vyjednávání metodu ověřování**.

 ![Povolit klientům připojení](./media/howto-mfa-nps-extension-rdg/image22.png)

6. Volitelně můžete kliknout **podmínky** kartě a přidat podmínky, které musí být splněny pro připojení k autorizaci, například členství v určité skupině systému Windows.

 ![Podmínky](./media/howto-mfa-nps-extension-rdg/image23.png)

7. Klikněte na **OK**. Po zobrazení výzvy k zobrazení odpovídající téma nápovědy, klikněte na tlačítko **ne**.
8. Zkontrolujte, že nové zásady je v horní části seznamu, že je zásada povolená, a udělí přístup.

 ![Zásady sítě](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Ověření konfigurace
Pokud chcete ověřit konfiguraci, je nutné se přihlásit služby Brána vzdálené plochy s vhodný klientem RDP. Je nutné použít účet, který je povolen na základě vaší zásady autorizace připojení a je povolený pro Azure MFA. 

Jak je vidět na obrázku níže, můžete použít **Remote Desktop Web Access** stránky.

![Remote Desktop Web Access](./media/howto-mfa-nps-extension-rdg/image25.png)

Po úspěšně zadávat svoje přihlašovací údaje pro primární ověřování, dialogové okno připojení vzdálené plochy zobrazuje stav Inicializace vzdáleného připojení, jak je uvedeno níže. 

Pokud jste úspěšně ověřit pomocí sekundárního ověřování, které jste dříve nakonfigurovali v Azure MFA, jste připojení k prostředku. Pokud sekundárního ověřování není úspěšné, ale je odepřen přístup k prostředku. 

![Inicializace vzdáleného připojení](./media/howto-mfa-nps-extension-rdg/image26.png)

V následujícím příkladu se používá ověřovací aplikací na Windows phone pro zajištění sekundární ověřování.

![Účty](./media/howto-mfa-nps-extension-rdg/image27.png)

Jakmile jste úspěšně ověřen pomocí metody sekundární ověřování, se protokolují do služby Brána vzdálené plochy jako normální. Ale vzhledem k tomu, že je nutné použít metodu sekundární ověřování pomocí mobilní aplikace na důvěryhodném zařízení, proces přihlášení je bezpečnější, než by byla, jinak hodnota.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Zobrazit protokoly Prohlížeče událostí pro události úspěšného přihlášení
Zobrazení událostí úspěšné přihlášení v protokolech prohlížeče událostí systému Windows, můžete použít následující příkaz prostředí Windows PowerShell k dotazování v protokolech Terminálová služba systému Windows a zabezpečení systému Windows.

K dotazování úspěšné události přihlášení v provozních protokolech brány _(událost prohlížeč událostí\protokoly aplikací a služeb Logs\Microsoft\Windows\TerminalServices-Gateway\Operational)_, použijte následující příkazy prostředí PowerShell:

* _Get-WinEvent. – název protokolu Microsoft-Windows-TerminalServices brány nebo provozní_ | kde {$_.ID - eq '300'} | FL 
* Tento příkaz zobrazí události systému Windows, které se zobrazí uživateli splněny požadavky na zásady autorizace prostředků (VP pro autorizaci prostředků) a byl udělen přístup.

![Prohlížeč událostí zobrazení](./media/howto-mfa-nps-extension-rdg/image28.png)

* _Get-WinEvent. – název protokolu Microsoft-Windows-TerminalServices brány nebo provozní_ | kde {$_.ID - eq '200'} | FL
* Tento příkaz zobrazí události, které se zobrazí, když uživatel splněny požadavky na zásady autorizace připojení.

![Ověření připojení](./media/howto-mfa-nps-extension-rdg/image29.png)

Můžete také zobrazit tomuto protokolu a filtru na události ID, 300 a 200. K dotazování událostí úspěšného přihlášení v protokolech prohlížeče událostí zabezpečení, použijte následující příkaz:

* _Get-WinEvent. – název protokolu zabezpečení_ | kde {$_.ID - eq '6272'} | FL 
* Tento příkaz lze spustit na centrální server NPS nebo Server brány VP. 

![Události úspěšného přihlášení](./media/howto-mfa-nps-extension-rdg/image30.png)

Také můžete zobrazit protokol zabezpečení nebo vlastní zobrazení služby Síťové zásady a přístup, jak je uvedeno níže:

![Služba Síťové zásady a přístup](./media/howto-mfa-nps-extension-rdg/image31.png)

Na serveru, kam jste nainstalovali server NPS rozšíření pro Azure MFA, můžete najít protokoly Prohlížeče událostí aplikace specifické pro rozšíření na _aplikace a služby Logs\Microsoft\AzureMfa_. 

![Aplikace protokoly Prohlížeče událostí](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Řešení potíží s Průvodce

Pokud konfigurace nefungují podle očekávání, je spuštění řešení, nejdřív ověřte, že je správce nakonfigurován na použití Azure MFA. Uživatel připojit k [portál Azure](https://portal.azure.com). Pokud se zobrazí výzva k sekundární ověření uživatelů a můžete úspěšně ověřit, že můžete eliminovat nesprávnou konfiguraci Azure MFA.

Pokud Azure MFA funguje pro uživatele, přečtěte si relevantní protokoly událostí. Mezi ně patří událostí zabezpečení, brány provozní a Azure MFA protokoly, které jsou popsané v předchozí části. 

Níže je příklad výstupu zobrazuje událost přihlášení se nezdařilo (událost ID 6273) protokolu zabezpečení.

![Událost přihlášení se nezdařilo](./media/howto-mfa-nps-extension-rdg/image33.png)

Zde jsou související události z protokolů AzureMFA:

![Protokolů Azure MFA](./media/howto-mfa-nps-extension-rdg/image34.png)

K provedení rozšířené možnosti řešení potíží, zkontrolujte soubory protokolů formátu databáze NPS nainstalovanou službu NPS. Tyto soubory protokolu se vytvoří v _%SystemRoot%\System32\Logs_ složky jako soubory text oddělený čárkami. 

Popis těchto souborů protokolu najdete v tématu [interpretovat NPS formátu soubory protokolů databáze](https://technet.microsoft.com/library/cc771748.aspx). Položky v těchto protokolových souborech může být obtížné interpretovat bez jejich importování do tabulkového procesoru nebo databázi. Pomoc při interpretaci souborů protokolu můžete najít několik služby ověřování v Internetu analyzátory online. 

Následující obrázek ukazuje výstup jednoho takové ke stažení [shareware aplikace](http://www.deepsoftware.com/iasviewer). 

![Shareware aplikace](./media/howto-mfa-nps-extension-rdg/image35.png)

Nakonec pro další možnosti řešení potíží, můžete pomocí analyzátoru protokolu, takové [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx). 

Obrázek níže z Microsoft Message Analyzer ukazuje síťový provoz filtrováno protokolu RADIUS, který obsahuje uživatelské jméno **Contoso\AliceC**.

![Microsoft Message Analyzer](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>Další postup
[Jak získat službu Azure Multi-Factor Authentication](concept-mfa-licensing.md)

[Brána vzdálené plochy Azure Multi-Factor Authentication Server pomocí protokolu RADIUS](howto-mfaserver-nps-rdg.md)

[Integrace místních adresářů do služby Azure Active Directory](../connect/active-directory-aadconnect.md)
