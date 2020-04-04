---
title: Integrace RDG s rozšířením Azure MFA NPS – Azure Active Directory
description: Integrace infrastruktury brány vzdálené plochy s Azure MFA pomocí rozšíření Network Policy Server pro Microsoft Azure
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
ms.openlocfilehash: c61bea7f3ca1105edfec54501c5f0725a5a10225
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654114"
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>Integrace infrastruktury služby Brána vzdálené plochy pomocí rozšíření NPS (Network Policy Server) a Azure AD

Tento článek obsahuje podrobnosti o integraci infrastruktury brány vzdálené plochy s azure multi-factor authentication (MFA) pomocí rozšíření server síťových zásad (NPS) pro Microsoft Azure.

Rozšíření server NPS (Network Policy Server) pro Azure umožňuje zákazníkům chránit ověřování klientů služby RADIUS (Remote Authentication Dial-In User Service) pomocí cloudového [vícefaktorového ověřování (MFA) azure.](multi-factor-authentication.md) Toto řešení poskytuje dvoustupňové ověření pro přidání druhé vrstvy zabezpečení pro přihlášení uživatelů a transakce.

Tento článek obsahuje podrobné pokyny pro integraci infrastruktury NPS s Azure MFA pomocí rozšíření NPS pro Azure. To umožňuje zabezpečené ověření pro uživatele, kteří se pokoušejí přihlásit k bráně vzdálené plochy.

> [!NOTE]
> Tento článek by se neměl používat s nasazeními serveru MFA a měl by být používán pouze s nasazeními Azure MFA (Cloud-based).

Služba NPS (Network Policy and Access Services) umožňuje organizacím provést následující akce:

* Definujte centrální umístění pro správu a řízení síťových požadavků určením, kdo se může připojit, jaké denní doby jsou připojení povolena, doba trvání připojení a úroveň zabezpečení, kterou klienti musí použít k připojení a tak dále. Spíše než zadání těchto zásad na každém serveru brány VPN nebo vzdálené plochy (RD) lze tyto zásady zadat jednou v centrálním umístění. Protokol RADIUS poskytuje centralizované ověřování, autorizaci a účtování (AAA).
* Vytvořte a vynucujte zásady stavu klientů ochrany přístupu k síti (NAP), které určují, zda je zařízením udělen neomezený nebo omezený přístup k síťovým prostředkům.
* Poskytněte prostředky k vynucení ověřování a autorizace přístupu k bezdrátovým přístupovým bodům 802.1x a přepínačům Ethernet.

Organizace obvykle používají server NPS (RADIUS) ke zjednodušení a centralizaci správy zásad vpn. Mnoho organizací však také používá server NPS ke zjednodušení a centralizaci správy zásad autorizace připojení k počítači v oblasti vzdáleného od pvP (RD.

Organizace mohou také integrovat server NPS s Azure MFA pro zvýšení zabezpečení a zajištění vysoké úrovně dodržování předpisů. To pomáhá zajistit, aby uživatelé vytvořit dvoustupňové ověření pro přihlášení k bráně vzdálené plochy. Aby byl uživatelům udělen přístup, musí poskytnout kombinaci uživatelského jména a hesla spolu s informacemi, které má uživatel pod kontrolou. Tyto informace musí být důvěryhodné a nesmí být snadno duplikovány, například číslo mobilního telefonu, číslo pevné linky, aplikace na mobilním zařízení a tak dále. RDG v současné době podporuje telefonní hovor a nabízená oznámení z metod aplikace Microsoft authenticator pro 2FA. Další informace o podporovaných metodách ověřování naleznete v části [Určení metod ověřování, které mohou uživatelé používat](howto-mfa-nps-extension.md#determine-which-authentication-methods-your-users-can-use).

Před dostupností rozšíření NPS pro Azure museli zákazníci, kteří chtěli implementovat dvoustupňové ověření pro integrovaná prostředí NPS a Azure MFA, nakonfigurovat a udržovat samostatný server MFA v místním prostředí, jak je popsáno v [bráně vzdálené plochy a serveru Azure Multi-Factor Authentication Server pomocí RADIUS](howto-mfaserver-nps-rdg.md).

Dostupnost rozšíření NPS pro Azure teď dává organizacím možnost nasadit místní řešení MFA založené nebo cloudové řešení MFA pro zabezpečení ověřování klientů RADIUS.

## <a name="authentication-flow"></a>Tok ověřování

Aby uživatelům byl udělen přístup k síťovým prostředkům prostřednictvím brány vzdálené plochy, musí splňovat podmínky uvedené v jedné zásadě autorizace připojení k VP (Rd CAP) a v jedné zásadě autorizace prostředků VP (Rd RAP). VP KP určují, kdo je oprávněn se připojit k bránám VP. Zásady VP RAPs určují síťové prostředky, jako jsou vzdálené plochy nebo vzdálené aplikace, ke kterým se uživatel může připojit prostřednictvím služby Brána VP.

Bránu VP lze nakonfigurovat tak, aby používala centrální úložiště zásad pro zásady vp. Zásady vyppp nemohou používat centrální zásady, protože jsou zpracovávány v bráně VP. Příkladem brány VP nakonfigurované pro použití centrálního úložiště zásad pro zásady vp.

Když je rozšíření NPS pro Azure integrované s bránou NPS a vzdálenou plochu, úspěšný tok ověřování je následující:

1. Server služby Brána vzdálené plochy obdrží od uživatele vzdálené plochy požadavek na ověření pro připojení k prostředku, například k relaci vzdálené plochy. Server služby RADIUS, který funguje jako klient RADIUS, převede požadavek na zprávu RADIUS Access-Request a odešle zprávu serveru RADIUS (NPS), kde je nainstalováno rozšíření NPS.
1. Kombinace uživatelského jména a hesla je ověřena ve službě Active Directory a uživatel je ověřen.
1. Pokud jsou splněny všechny podmínky uvedené v požadavku na připojení serveru NPS a zásadách sítě (například denní doba nebo omezení členství ve skupině), rozšíření NPS aktivuje požadavek na sekundární ověřování pomocí Azure MFA.
1. Azure MFA komunikuje s Azure AD, načte podrobnosti o uživateli a provádí sekundární ověřování pomocí podporovaných metod.
1. Po úspěchu výzvy mfa Azure MFA sděluje výsledek rozšíření NPS.
1. Server NPS, kde je rozšíření nainstalováno, odešle na server služby Brána vzdálené plochy zprávu RADIUS Access-Accept pro zásady v oblasti VP CAP.
1. Uživateli je udělen přístup k požadovanému síťovému prostředku prostřednictvím brány VP.

## <a name="prerequisites"></a>Požadavky

Tato část podrobně popisuje požadavky nezbytné před integrací Azure MFA s bránou vzdálené plochy. Než začnete, musíte mít následující požadavky na místě.  

* Infrastruktura služby Vzdálená plocha (RDS)
* Licence Azure MFA
* Software Windows Server
* Role síťových zásad a přístupových služeb (NPS)
* Služba Azure Active Directory synchronizovaná s místní službou Active Directory
* Azure Active Directory GUID ID

### <a name="remote-desktop-services-rds-infrastructure"></a>Infrastruktura služby Vzdálená plocha (RDS)

Musíte mít funkční infrastrukturu služby Vzdálené plochy (RDS). Pokud tak neučiníte, můžete tuto infrastrukturu rychle vytvořit v Azure pomocí následující šablony rychlého startu: [Vytvoření nasazení kolekce relací vzdálené plochy](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment).

Pokud chcete ručně vytvořit místní infrastrukturu RDS rychle pro účely testování, postupujte podle pokynů k nasazení jednoho.
**Další informace:** [Nasazení rds s Azure quickstart](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-in-azure) a [základní nasazení infrastruktury RDS](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

### <a name="azure-mfa-license"></a>Licence Azure MFA

Povinné je licence pro Azure MFA, která je dostupná prostřednictvím Azure AD Premium nebo jiných balíčků, které ji obsahují. Licence založené na spotřebě pro Azure MFA, například pro uživatele nebo licence pro ověřování, nejsou kompatibilní s rozšířením NPS. Další informace najdete v tématu [Jak získat Azure Multi-Factor Authentication](concept-mfa-licensing.md). Pro účely testování můžete použít zkušební předplatné.

### <a name="windows-server-software"></a>Software Windows Server

Rozšíření NPS vyžaduje systém Windows Server 2008 R2 SP1 nebo vyšší s nainstalovanou službou role SERVERS. Všechny kroky v této části byly provedeny pomocí systému Windows Server 2016.

### <a name="network-policy-and-access-services-nps-role"></a>Role síťových zásad a přístupových služeb (NPS)

Služba role NPS poskytuje funkce serveru RADIUS a klienta a také službu stavu zásad přístupu k síti. Tato role musí být nainstalována alespoň ve dvou počítačích ve vaší infrastruktuře: v bráně vzdálené plochy a v jiném členském serveru nebo řadiči domény. Ve výchozím nastavení je role již přítomna v počítači nakonfigurovaném jako brána vzdálené plochy.  Roli serveru NPS je také nutné nainstalovat alespoň do jiného počítače, například do řadiče domény nebo členského serveru.

Informace o instalaci služby role SERVER NPS windows server 2012 nebo starší naleznete [v tématu Instalace serveru zásad stavu nap](https://technet.microsoft.com/library/dd296890.aspx). Popis doporučených postupů pro server NPS, včetně doporučení k instalaci serveru NPS do řadiče domény, naleznete v [tématu Doporučené postupy pro server NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Služba Azure Active Directory synchronizovaná s místní službou Active Directory

Chcete-li použít rozšíření NPS, místní uživatelé musí být synchronizovány s Azure AD a povolena pro vícefaktorové informace. Tato část předpokládá, že místní uživatelé jsou synchronizovány s Azure AD pomocí Služby AD Connect. Informace o připojení Azure AD najdete [v tématu Integrace místních adresářů pomocí Služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory GUID ID

Chcete-li nainstalovat rozšíření NPS, musíte znát identifikátor GUID azure ad. Pokyny pro nalezení identifikátoru GUID azure ad jsou k dispozici níže.

## <a name="configure-multi-factor-authentication"></a>Konfigurace vícefaktorového ověřování

Tato část obsahuje pokyny pro integraci Azure MFA s bránou vzdálené plochy. Jako správce je nutné nakonfigurovat službu Azure MFA dříve, než uživatelé mohou sami zaregistrovat svá vícefaktorová zařízení nebo aplikace.

Postupujte podle kroků v [začínáme s Azure Multi-Factor Authentication v cloudu](howto-mfa-getstarted.md) povolit vícefaktorové ověřování pro uživatele Azure AD.

### <a name="configure-accounts-for-two-step-verification"></a>Konfigurace účtů pro dvoustupňové ověření

Jakmile je pro vícefaktorové ověřování povolen účet, nemůžete se přihlásit k prostředkům, které se řídí zásadami MFA, dokud úspěšně nenakonfigurujete důvěryhodné zařízení pro druhý faktor ověřování a neověříte pomocí dvoustupňového ověření.

Postupujte podle kroků v části [Co pro mě znamená Azure Multi-Factor Authentication?](../user-help/multi-factor-authentication-end-user.md)

## <a name="install-and-configure-nps-extension"></a>Instalace a konfigurace rozšíření serveru NPS

Tato část obsahuje pokyny pro konfiguraci infrastruktury RDS pro použití Azure MFA pro ověřování klientů pomocí brány vzdálené plochy.

### <a name="acquire-azure-active-directory-guid-id"></a>Získání Identifikátoru GUID služby Azure Active Directory

Jako součást konfigurace rozšíření NPS, musíte zadat přihlašovací údaje správce a Azure AD ID pro vašeho klienta Azure AD. Následující kroky ukazují, jak získat ID klienta.

1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako globální správce klienta Azure.
1. V nabídce Portál Azure vyberte **Azure Active Directory**nebo vyhledejte a vyberte Azure Active **Directory** z libovolné stránky.
1. Vyberte **vlastnosti**.
1. V okně Vlastnosti vedle ID adresáře klepněte na ikonu **Kopírovat,** jak je znázorněno níže, a zkopírujte ID do schránky.

   ![Získání ID adresáře z webu Azure Portal](./media/howto-mfa-nps-extension-rdg/azure-active-directory-id-in-azure-portal.png)

### <a name="install-the-nps-extension"></a>Instalace rozšíření NPS

Nainstalujte rozšíření NPS na server, na který je nainstalována role NPS (Network Policy and Access Services). To funguje jako server RADIUS pro váš návrh.

> [!Important]
> Ujistěte se, že nenainstalujete rozšíření NPS na server služby Brána vzdálené plochy.
>

1. Stáhněte si [rozšíření NPS](https://aka.ms/npsmfa).
1. Zkopírujte spustitelný soubor instalace (NpsExtnForAzureMfaInstaller.exe) na server NPS.
1. Na serveru NPS poklepejte na **položku NpsExtnForAzureMfaInstaller.exe**. Po zobrazení výzvy klepněte na tlačítko **Spustit**.
1. V dialogovém okně Nastavení služby Rozšíření serveru NPS pro Azure MFA zkontrolujte licenční podmínky softwaru, zkontrolujte, zda **souhlasím s licenčními podmínkami a**klepněte na tlačítko **Instalovat**.
1. V dialogovém okně Nastavení rozšíření serveru NPS pro Azure MFA klepněte na tlačítko **Zavřít**.

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Konfigurace certifikátů pro použití s rozšířením NPS pomocí skriptu Prostředí PowerShell

Dále je třeba nakonfigurovat certifikáty pro použití rozšířením NPS, abyste zajistili zabezpečenou komunikaci a záruku. Součásti serveru NPS obsahují skript prostředí Windows PowerShell, který konfiguruje certifikát podepsaný svým držitelem pro použití se serverem NPS.

Skript provádí následující akce:

* Vytvoří certifikát podepsaný svým držitelem.
* Přidruží veřejný klíč certifikátu k instančnímu objektu ve službě Azure AD
* Uskladnění certifikátu v místním úložišti strojů
* Uděluje přístup k soukromému klíči certifikátu uživateli sítě.
* Restartuje službu Network Policy Server.

Pokud chcete použít vlastní certifikáty, musíte přidružit veřejný klíč certifikátu k instančnímu objektu ve službě Azure AD a tak dále.

Chcete-li použít skript, zadejte rozšíření s přihlašovacími údaji správce Azure AD a ID klienta Azure AD, které jste zkopírovali dříve. Spusťte skript na každém serveru NPS, na kterém jste nainstalovali rozšíření NPS. Potom udělejte následující:

1. Otevřete výzvu prostředí Windows PowerShell pro správu.
1. Na výzvu prostředí `cd 'c:\Program Files\Microsoft\AzureMfa\Config'`PowerShell zadejte a stiskněte **klávesu ENTER**.
1. Zadejte `.\AzureMfaNpsExtnConfigSetup.ps1`a stiskněte **klávesu ENTER**. Skript zkontroluje, jestli je nainstalovaný modul Azure Active Directory PowerShell. Pokud není nainstalován, skript nainstaluje modul za vás.

   ![Spuštění AzureMfaNpsExtnConfigSetup.ps1 v prostředí Azure AD PowerShell](./media/howto-mfa-nps-extension-rdg/image4.png)
  
1. Poté, co skript ověří instalaci modulu PowerShell, zobrazí dialogové okno modulu Azure Active Directory PowerShell. V dialogovém okně zadejte přihlašovací údaje a heslo správce Azure AD a klikněte na **Přihlásit se**.

   ![Ověřování azure ad v PowerShellu](./media/howto-mfa-nps-extension-rdg/image5.png)

1. Po zobrazení výzvy vložte ID adresáře, které jste dříve zkopírovali do schránky, a stiskněte **klávesu ENTER**.

   ![Zadání ID adresáře v Prostředí PowerShell](./media/howto-mfa-nps-extension-rdg/image6.png)

1. Skript vytvoří certifikát podepsaný svým držitelem a provede další změny konfigurace. Výstup by měl být podobný obrázku, který je znázorněn níže.

   ![Výstup prostředí PowerShell s certifikátem podepsaným svým držitelem](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>Konfigurace součástí serveru NPS v bráně vzdálené plochy

V této části nakonfigurujete zásady autorizace připojení služby Brána vzdálené plochy a další nastavení radius.

Tok ověřování vyžaduje výměnu zpráv RADIUS mezi bránou vzdálené plochy a serverem NPS, na kterém je nainstalováno rozšíření NPS. To znamená, že je nutné nakonfigurovat nastavení klienta RADIUS na bráně vzdálené plochy i na serveru NPS, kde je nainstalováno rozšíření NPS.

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Konfigurace zásad autorizace připojení služby Brána vzdálené plochy pro použití centrálního úložiště

Zásady autorizace připojení ke vzdálené ploše (Rd EP CAPs) určují požadavky na připojení k serveru služby Brána vzdálené plochy. VP CAP mohou být uloženy místně (výchozí) nebo mohou být uloženy v centrálním úložišti VP CAP se systémem NPS. Chcete-li nakonfigurovat integraci Azure MFA s RDS, musíte zadat použití centrálního úložiště.

1. Na serveru služby Brána VP stevírat **Správce serveru**.
1. V nabídce klepněte na **položku Nástroje**, přejděte na **položku Vzdálená plocha**a potom klepněte na **položku Správce brány vzdálené plochy**.
1. Ve Správci brány VP klepněte pravým tlačítkem myši na ** \[položku Název\] serveru (Místní)** a klepněte na příkaz **Vlastnosti**.
1. V dialogovém okně Vlastnosti vyberte kartu **Úložiště VP CAP.**
1. Na kartě Obchod s vyvodužkou v pzu , vyberte **centrální server se systémem NPS**. 
1. Do pole **Zadejte název nebo adresu IP pro server se serverem se serverem NPS** zadejte adresu IP nebo název serveru, na který jste nainstalovali rozšíření NPS.

   ![Zadejte název nebo ADRESU IP serveru NPS.](./media/howto-mfa-nps-extension-rdg/image10.png)
  
1. Klikněte na **Přidat**.
1. V dialogovém okně **Sdílený tajný klíč** zadejte sdílený tajný klíč a klepněte na tlačítko **OK**. Ujistěte se, že záznam sdílený tajný klíč a bezpečně uložte záznam.

   >[!NOTE]
   >Sdílený tajný klíč se používá k vytvoření vztahu důvěryhodnosti mezi servery RADIUS a klienty. Vytvořte dlouhý a složitý tajný klíč.
   >

   ![Vytvoření sdíleného tajného klíče pro vytvoření vztahu důvěryhodnosti](./media/howto-mfa-nps-extension-rdg/image11.png)

1. Kliknutím na **OK** zavřete dialogové okno.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>Konfigurace hodnoty časového omezení RADIUS v serveru NPS služby Brána vzdálené plochy

Chcete-li zajistit, že je čas na ověření pověření uživatelů, provedení dvoustupňového ověření, přijímání odpovědí a odpovídání na zprávy RADIUS, je nutné upravit hodnotu časového času RADIUS.

1. Na serveru služby Brána VP otevřete Správce serveru. V nabídce klepněte na **položku Nástroje**a potom klepněte na **položku Server zásad sítě**.
1. V konzole **NPS (Local)** rozbalte **položku RADIUS Clients and Servers**a vyberte vzdálený server **RADIUS**.

   ![Konzola pro správu serveru síťových zásad zobrazující vzdálený server RADIUS](./media/howto-mfa-nps-extension-rdg/image12.png)

1. V podokně podrobností poklepejte na **položku TS GATEWAY SERVER GROUP**.

   >[!NOTE]
   >Tato skupina serverů RADIUS byla vytvořena při konfiguraci centrálního serveru pro zásady serveru NPS. Brána VP předává zprávy RADIUS na tento server nebo skupinu serverů, pokud je ve skupině více než jeden.
   >

1. V dialogovém okně **Vlastnosti skupiny serveru TS GATEWAY** vyberte adresu IP nebo název serveru NPS, který jste nakonfigurovali pro ukládání programů VP CAP, a klepněte na tlačítko **Upravit**.

   ![Vyberte dříve nakonfigurovanou adresu IP nebo název serveru NPS.](./media/howto-mfa-nps-extension-rdg/image13.png)

1. V dialogovém okně **Upravit server RADIUS** vyberte kartu **Vyrovnávání zatížení.**
1. Na kartě **Vyrovnávání zatížení** v **poli Počet sekund bez odpovědi před požadavkem změňte** výchozí hodnotu ze 3 na hodnotu mezi 30 a 60 sekundami.
1. V poli **Počet sekund mezi požadavky, pokud je server identifikován jako nedostupný,** změňte výchozí hodnotu 30 sekund na hodnotu, která je rovna nebo větší než hodnota zadaná v předchozím kroku.

   ![Úprava nastavení časového času serveru Radius na kartě vyrovnávání zatížení](./media/howto-mfa-nps-extension-rdg/image14.png)

1. Chcete-li dialogová okna zavřít, klepněte dvakrát na **tlačítko OK.**

### <a name="verify-connection-request-policies"></a>Ověření zásad požadavků na připojení

Ve výchozím nastavení je při konfiguraci služby Brána VP pro použití centrálního úložiště zásad pro zásady autorizace připojení brána VP nakonfigurována tak, aby předává požadavky CAP na server NPS. Server NPS s nainstalovaným rozšířením Azure MFA zpracuje požadavek na přístup RADIUS. Následující kroky ukazují, jak ověřit výchozí zásady žádosti o připojení.  

1. V bráně VP v konzole NPS (Místní) rozbalte **zásady**a vyberte **zásady požadavků na připojení**.
1. Poklepejte na **zásady autorizace služby TS GATEWAY**.
1. V dialogovém okně **Vlastnosti zásadautorizace brány TS** klikněte na kartu **Nastavení.**
1. Na kartě **Nastavení** klikněte v části Forwarding Connection Request na **Authentication**. Klient RADIUS je nakonfigurován tak, aby přesměroval požadavky na ověření.

   ![Konfigurace nastavení ověřování určující skupinu serverů](./media/howto-mfa-nps-extension-rdg/image15.png)

1. Klepněte na tlačítko **Storno**.

>[!NOTE]
> Další informace o vytvoření zásad y žádosti o připojení naleznete v článku Konfigurace dokumentace [zásad požadavků](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-crp-configure#add-a-connection-request-policy) na připojení pro stejné. 

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>Konfigurace serveru NPS na serveru, na kterém je nainstalováno rozšíření NPS

Server NPS, na kterém je nainstalováno rozšíření NPS, musí mít možnost vyměňovat si zprávy RADIUS se serverem NPS v bráně vzdálené plochy. Chcete-li povolit tuto výměnu zpráv, je třeba nakonfigurovat součásti serveru NPS na serveru, na kterém je nainstalována služba rozšíření nps.

### <a name="register-server-in-active-directory"></a>Registrace serveru ve službě Active Directory

Chcete-li v tomto scénáři správně fungovat, musí být server NPS zaregistrován ve službě Active Directory.

1. Na serveru NPS stevírat **Správce serveru**.
1. Ve Správci serveru klepněte na **položku Nástroje**a potom klepněte na **položku Server síťových zásad**.
1. V konzole Server zásad síťových zásad klepněte pravým tlačítkem myši na položku **NPS (Local)** a potom klepněte na příkaz **Registrovat server ve službě Active Directory**.
1. Dvakrát klikněte na **OK.**

   ![Registrace serveru NPS ve službě Active Directory](./media/howto-mfa-nps-extension-rdg/image16.png)

1. Nechte konzolu otevřenou pro další postup.

### <a name="create-and-configure-radius-client"></a>Vytvoření a konfigurace klienta RADIUS

Bránu vzdálené plochy je třeba nakonfigurovat jako klienta RADIUS pro server NPS.

1. Na serveru NPS, kde je nainstalováno rozšíření NPS, klepněte v konzole **NPS (Local)** pravým tlačítkem myši na **příkaz RADIUS Clients** a klepněte na příkaz **Nový**.

   ![Vytvoření nového klienta RADIUS v konzole NPS](./media/howto-mfa-nps-extension-rdg/image17.png)

1. V dialogovém okně **Nový klient RADIUS** zadejte popisný název, například _Brána_, a adresu IP nebo název DNS serveru služby Brána vzdálené plochy.
1. Do polí **Sdílený tajný klíč** a Potvrdit sdílený tajný **klíč** zadejte stejný tajný klíč, který jste použili dříve.

   ![Konfigurace popisné adresy a adresy IP nebo DNS](./media/howto-mfa-nps-extension-rdg/image18.png)

1. Klepnutím na **tlačítko OK** zavřete dialogové okno Nový klient RADIUS.

### <a name="configure-network-policy"></a>Konfigurace zásad sítě

Připomeňme, že server NPS s rozšířením Azure MFA je určené centrální úložiště zásad pro zásady autorizace připojení (CAP). Proto je třeba implementovat CAP na serveru NPS autorizovat platné požadavky na připojení.  

1. Na serveru NPS otevřete konzolu NPS (Local), **rozbalte položku Zásady**a klepněte na **položku Zásady sítě**.
1. Klepněte pravým tlačítkem myši na **položku Připojení k jiným serverům pro přístup**a klepněte na příkaz **Duplikovat zásady**.

   ![Duplikovat připojení k jiným zásadám přístupových serverů](./media/howto-mfa-nps-extension-rdg/image19.png)

1. Klepněte pravým tlačítkem myši na **příkaz Kopírovat připojení k jiným serverům pro přístup**a klepněte na příkaz **Vlastnosti**.
1. V dialogovém okně **Kopírovat připojení k jiným přístupovým serverům** zadejte do pole Název **zásady**vhodný název, například _RDG_CAP_. Zaškrtněte **políčko Zásady povoleno**a vyberte **možnost Udělit přístup**. Volitelně můžete v poli **Typ serveru pro přístup k síti**vybrat **bránu vzdálené plochy**nebo ji ponechat jako **Neurčenou**.

   ![Pojmenujte zásadu, povolte a udělte přístup](./media/howto-mfa-nps-extension-rdg/image21.png)

1. Klepněte na kartu **Omezení** a zaškrtněte **políčko Povolit klientům připojení bez vyjednávání metody ověřování**.

   ![Úprava metod ověřování tak, aby se klienti mohli připojit](./media/howto-mfa-nps-extension-rdg/image22.png)

1. Volitelně můžete kliknout na kartu **Podmínky** a přidat podmínky, které musí být splněny pro připojení, které má být autorizováno, například členství v určité skupině systému Windows.

   ![Volitelně zadejte podmínky připojení.](./media/howto-mfa-nps-extension-rdg/image23.png)

1. Klikněte na tlačítko **OK**. Po zobrazení odpovídajícího tématu nápovědy klepněte na tlačítko **Ne**.
1. Ujistěte se, že vaše nové zásady jsou v horní části seznamu, že je povolena zásada a že uděluje přístup.

   ![Přesunutí zásad na začátek seznamu](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Ověřit konfiguraci

Chcete-li ověřit konfiguraci, musíte se přihlásit k bráně vzdálené plochy pomocí vhodného klienta RDP. Ujistěte se, že používáte účet, který je povolen zásadami autorizace připojení a je povolený pro Azure MFA.

Jak ukazuje obrázek níže, můžete použít stránku **Webový přístup ke vzdálené ploše.**

![Testování ve webovém přístupu ke vzdálené ploše](./media/howto-mfa-nps-extension-rdg/image25.png)

Po úspěšném zadání přihlašovacích údajů pro primární ověřování se v dialogovém okně Připojení ke vzdálené ploše zobrazí stav Zahájení vzdáleného připojení, jak je znázorněno níže. 

Pokud se úspěšně ověříte pomocí metody sekundárního ověřování, kterou jste dříve nakonfigurovali v Azure MFA, jste připojeni k prostředku. Pokud však sekundární ověřování není úspěšné, bude vám odepřen přístup k prostředku. 

![Připojení ke vzdálené ploše při zahájení vzdáleného připojení](./media/howto-mfa-nps-extension-rdg/image26.png)

V níže uvedeném příkladu se aplikace Authenticator na telefonu s Windows používá k poskytování sekundárního ověřování.

![Příklad aplikace Ověřování ve Windows Phone zobrazující ověření](./media/howto-mfa-nps-extension-rdg/image27.png)

Po úspěšném ověření pomocí sekundární metody ověřování jste přihlášeni k bráně vzdálené plochy jako obvykle. Protože je však nutné použít metodu sekundárního ověřování pomocí mobilní aplikace na důvěryhodném zařízení, je proces přihlášení bezpečnější, než by tomu bylo jinak.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Zobrazit protokoly Prohlížeče událostí pro úspěšné události přihlášení

Chcete-li zobrazit úspěšné události přihlášení v protokolech prohlížeče událostí systému Windows, můžete vydat následující příkaz prostředí Windows PowerShell a zadat dotaz na protokoly Terminálové služby systému Windows a zabezpečení systému Windows.

Chcete-li zadat dotaz na úspěšné události přihlášení v provozních protokolech brány _(Prohlížeč událostí\Protokoly aplikací a služeb\Microsoft\Windows\TerminalServices-Gateway\Operational),_ použijte následující příkazy prostředí PowerShell:

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '300'} | FL`
* Tento příkaz zobrazí události systému Windows, které ukazují, že uživatel splnil požadavky zásad autorizace prostředků (VP RAP) a byl mu udělen přístup.

![Zobrazení událostí pomocí PowerShellu](./media/howto-mfa-nps-extension-rdg/image28.png)

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '200'} | FL`
* Tento příkaz zobrazí události, které se zobrazí, když uživatel splnil požadavky zásad autorizace připojení.

![zobrazení zásad autorizace připojení pomocí prostředí PowerShell](./media/howto-mfa-nps-extension-rdg/image29.png)

Můžete také zobrazit tento protokol a filtrovat id událostí 300 a 200. Chcete-li dotazovat na úspěšné události přihlášení v protokolech prohlížeče událostí zabezpečení, použijte následující příkaz:

* `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`
* Tento příkaz lze spustit na centrálním serveru NPS nebo na serveru služby Brána VP.

![Ukázka úspěšných událostí přihlášení](./media/howto-mfa-nps-extension-rdg/image30.png)

Můžete také zobrazit vlastní zobrazení protokolu zabezpečení nebo vlastní hospodaříce Se zásadami sítě a služby Access Services, jak je znázorněno níže:

![Prohlížeč událostí síťových zásad a přístupových služeb](./media/howto-mfa-nps-extension-rdg/image31.png)

Na serveru, na kterém jste nainstalovali rozšíření NPS pro Azure MFA, můžete najít protokoly aplikací Prohlížeče událostí specifické pro rozšíření v _protokolech aplikací a služeb\Microsoft\AzureMfa_.

![Protokoly aplikací AuthZ prohlížeče událostí](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Průvodce odstraňováním potíží

Pokud konfigurace nefunguje podle očekávání, první místo, kde můžete začít řešit potíže, je ověřit, že uživatel je nakonfigurovaný pro použití Azure MFA. Ponětit uživatele připojit k [portálu Azure](https://portal.azure.com). Pokud se uživatelům zobrazí výzva k sekundárnímu ověření a můžete se úspěšně ověřit, můžete eliminovat nesprávnou konfiguraci Azure MFA.

Pokud Azure MFA pracuje pro uživatele, měli byste zkontrolovat příslušné protokoly událostí. Patří mezi ně události zabezpečení, provozní brány a protokoly Azure MFA, které jsou popsány v předchozí části.

Níže je uveden příklad výstupu protokolu zabezpečení zobrazujícího neúspěšnou událost přihlášení (ID události 6273).

![Ukázka neúspěšné přihlašovací události](./media/howto-mfa-nps-extension-rdg/image33.png)

Níže je související událost z protokolů AzureMFA:

![Ukázkový protokol Azure MFA v Prohlížeči událostí](./media/howto-mfa-nps-extension-rdg/image34.png)

Chcete-li provést pokročilé možnosti řešení potíží, podívejte se do souborů protokolu formátu databáze serveru NPS, kde je nainstalována služba NPS. Tyto soubory protokolu jsou vytvořeny ve složce _%SystemRoot%\System32\Logs_ jako textové soubory oddělené čárkami.

Popis těchto souborů protokolu naleznete v [tématu Interpretovat soubory protokolu formátu databáze nps](https://technet.microsoft.com/library/cc771748.aspx). Položky v těchto souborech protokolu může být obtížné interpretovat bez jejich importu do tabulky nebo databáze. Můžete najít několik analyzátorů IAS online, které vám pomohou při interpretaci souborů protokolu.

Obrázek níže ukazuje výstup jednoho takového ke stažení [shareware aplikace](https://www.deepsoftware.com/iasviewer).

![Ukázkový analyzátor aplikace Shareware IAS](./media/howto-mfa-nps-extension-rdg/image35.png)

Nakonec pro další možnosti řešení potíží můžete použít analyzátor protokolů, [například Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx).

Obrázek níže z analyzátoru zpráv společnosti Microsoft zobrazuje síťový provoz filtrovaný v protokolu RADIUS, který obsahuje uživatelské jméno **CONTOSO\AliceC**.

![Analyzátor zpráv společnosti Microsoft zobrazující filtrovaný provoz](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>Další kroky

[Jak získat službu Azure Multi-Factor Authentication](concept-mfa-licensing.md)

[Brána vzdálené plochy Azure Multi-Factor Authentication Server pomocí protokolu RADIUS](howto-mfaserver-nps-rdg.md)

[Integrace místních adresářů do služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
