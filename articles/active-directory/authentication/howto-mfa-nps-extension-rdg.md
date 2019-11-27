---
title: Integrace RDG s rozšířením NPS pro Azure MFA – Azure Active Directory
description: Integrace infrastruktury služby Brána vzdálené plochy s Azure MFA pomocí rozšíření serveru Network Policy Server pro Microsoft Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ec402cf2c741d88d230e5734485bf9eb0dd1b03
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381817"
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>Integrace služby Brána vzdálené plochy infrastrukturu s použitím rozšíření serveru NPS (Network Policy Server) a Azure AD

Tento článek obsahuje podrobnosti pro integraci s Azure Multi-Factor Authentication (MFA) infrastrukturu služby Brána vzdálené plochy pomocí rozšíření serveru NPS (Network Policy Server) pro Microsoft Azure.

Rozšíření serveru NPS (Network Policy Server) pro Azure umožňuje zákazníkům chránit ověřování klientů protokol RADIUS (Remote Authentication Dial-In User Service) (RADIUS) pomocí cloudové [Multi-Factor Authentication Azure (MFA)](multi-factor-authentication.md). Toto řešení zajišťuje dvoustupňové ověření pro přidání druhou vrstvu zabezpečení uživatelská přihlášení a transakce.

Tento článek obsahuje podrobné pokyny pro integraci s Azure MFA NPS server infrastruktury pomocí rozšíření NPS pro Azure. To umožňuje zabezpečené ověřování pro uživatele pokoušející se přihlásit do služby Brána vzdálené plochy.

> [!NOTE]
> Tento článek by se neměl používat s nasazeními MFA serveru a měl by se používat jenom s nasazeními Azure MFA (cloudové).

Síťové zásady a přístup k službám (NPS) poskytuje organizacím možnost postupujte takto:

* Definujte centrální umístění pro řízení a kontrolu požadavků sítě tak, že určíte, kdo se může připojit, dobu připojení den jsou povolené a dobu trvání připojení a úroveň zabezpečení, které musí klienti používat pro připojení a tak dále. Místo zadání těchto zásad na každém serveru sítě VPN nebo Brána vzdálené plochy (RD), tyto zásady se dá nastavit jednou v centrálním umístění. Protokol RADIUS poskytuje centralizované ověřování, autorizaci a monitorování (AAA).
* Vytvořit a vynucovat zásady stavu klienta ochrany NAP (Network Access), které určují, jestli zařízení jsou udělena neomezeného nebo omezeného přístupu k síťovým prostředkům.
* Poskytují způsob vynucení ověřování a autorizace pro přístup k protokolu 802. 1 x podporuje bezdrátových přístupových bodů a přepínače sítě Ethernet.

Organizace obvykle používají server NPS (RADIUS) k zjednodušení a centralizaci správy zásad sítě VPN. Řada organizací ale používat taky NPS zjednodušují a centralizovat správu zásad autorizace připojení ke vzdálené ploše Desktop (CAP k vzdálené ploše).

Organizace také můžete integrovat server NPS s Azure MFA pro zvýšení zabezpečení a poskytovat vysokou úroveň dodržování předpisů. To pomáhá zajistit, že uživatelé vytvořit dvoustupňové ověření pro přihlášení k Brána vzdálené plochy. Pro uživatele, který má být udělen přístup musí zadat jejich kombinace uživatelského jména a hesla společně s informacemi, jestli nemá uživatel v jejich řízení. Tyto informace musí být důvěryhodné a duplicitní nejsou snadno, jako je například číslo mobilního telefonu, číslo linky, aplikace na mobilním zařízení a tak dále. RDG v současné době podporuje telefonní hovory a nabízená oznámení z metod aplikace Microsoft Authenticator pro 2FA. Další informace o podporovaných metodách ověřování najdete v části [Určení metod ověřování, které uživatelé mohou používat](howto-mfa-nps-extension.md#determine-which-authentication-methods-your-users-can-use).

Před dostupností rozšíření serveru NPS pro Azure museli zákazníci, kteří chtějí implementovat dvoustupňové ověřování pro integrovaná rozhraní NPS a Azure MFA, nakonfigurovat a udržovat samostatný server MFA v místním prostředí, jak je uvedeno v [Brána vzdálené plochy a v azure Multi-Factor Authentication Server pomocí protokolu RADIUS](howto-mfaserver-nps-rdg.md).

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

Musíte mít funkční infrastrukturu služby Vzdálená plocha (RDS) na místě. Pokud to neuděláte, můžete tuto infrastrukturu v Azure rychle vytvořit pomocí následující šablony pro rychlé zprovoznění: [vytvoření nasazení kolekce relací vzdálené plochy](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment).

Pokud chcete ručně vytvořit místní infrastrukturu RDS rychle pro účely testování, postupujte podle kroků, které chcete nasadit některé.
**Další informace**: [nasazení RDS pomocí rychlého startu Azure](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-in-azure) a [základního nasazení infrastruktury VP](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)

### <a name="azure-mfa-license"></a>Licence Azure MFA

Vyžaduje se licence pro Azure MFA, která je dostupná prostřednictvím Azure AD Premium nebo dalších sad, které ho obsahují. Založenou na skutečné spotřebě licence pro Azure MFA, například podle uživatele nebo na ověřování licencí, nejsou kompatibilní s rozšíření serveru NPS. Další informace najdete v tématu [Jak získat Azure Multi-Factor Authentication](concept-mfa-licensing.md). Pro účely testování můžete použít zkušební verzi předplatného.

### <a name="windows-server-software"></a>Software Windows serveru

Rozšíření NPS server vyžaduje Windows Server 2008 R2 SP1 nebo novějším s nainstalovanou službou role NPS. Všechny kroky v této části byly provedeny pomocí Windows serveru 2016.

### <a name="network-policy-and-access-services-nps-role"></a>Síťové zásady a přístup k službám (NPS)

Službu role NPS poskytuje server protokolu RADIUS a klientské funkce, jakož i služba health service zásady přístupu k síti. Tato role musí nainstalovat na nejméně dva počítače ve vaší infrastruktuře: služby Brána vzdálené plochy a další členského serveru nebo řadiče domény. Ve výchozím nastavení role již existuje v počítač nakonfigurovaný jako Brána vzdálené plochy.  Také nainstalujte NPS role na alespoň na jiném počítači, jako jsou řadiče domény nebo členském serveru.

Informace o instalaci služby role NPS Windows Server 2012 nebo starší najdete v tématu [instalace serveru zásad stavu NAP](https://technet.microsoft.com/library/dd296890.aspx). Popis osvědčených postupů pro server NPS, včetně doporučení k instalaci NPS na řadič domény, najdete v tématu [osvědčené postupy pro server NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Azure Active Directory synchronizovat s místní služby Active Directory

Použití rozšíření serveru NPS, musí být místním uživatelům synchronizaci se službou Azure AD a povolené pro vícefaktorové ověřování. V této části se předpokládá, že jsou v místním uživatelům synchronizovat s Azure AD pomocí AD Connect. Informace o službě Azure AD Connect najdete v tématu [Integrace místních adresářů s Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>Identifikátor GUID služby Azure Active Directory

Pokud chcete nainstalovat rozšíření serveru NPS, je potřeba vědět identifikátor GUID služby Azure AD. Níže jsou uvedené pokyny, jak najít identifikátor GUID služby Azure AD.

## <a name="configure-multi-factor-authentication"></a>Konfigurace ověřování službou Multi-Factor Authentication

Tato část obsahuje pokyny k integraci Azure MFA s bránou vzdálení plochy. Jako správce musíte nakonfigurovat službu Azure MFA, předtím, než mohou uživatelé registrovat svá zařízení služby Multi-Factor Authentication nebo aplikace.

Postupujte podle kroků v části [Začínáme s Azure Multi-Factor Authentication v cloudu](howto-mfa-getstarted.md) a povolte MFA pro uživatele Azure AD.

### <a name="configure-accounts-for-two-step-verification"></a>Konfigurace účtů pro dvoustupňové ověřování.

Jakmile účet povolen pro vícefaktorové ověřování, nemůžete se přihlásit k prostředkům řídí zásady vícefaktorového ověřování, dokud se úspěšně jste nakonfigurovali důvěryhodné zařízení pro druhý faktor ověřování a jste se ověřili pomocí dvoustupňového ověření.

Postupujte podle kroků v části [co Azure Multi-Factor Authentication znamená pro mě?](../user-help/multi-factor-authentication-end-user.md) k pochopení a správné konfiguraci zařízení pro MFA s vaším uživatelským účtem.

## <a name="install-and-configure-nps-extension"></a>Instalace a konfigurace rozšíření serveru NPS

Tato část obsahuje pokyny ke konfiguraci vzdálené plochy infrastruktury pro ověřování klienta s bránou vzdálení plochy pomocí Azure MFA.

### <a name="acquire-azure-active-directory-guid-id"></a>Získat Identifikátor GUID služby Azure Active Directory

Jako součást konfigurace rozšíření serveru NPS budete muset zadat přihlašovací údaje správce a Azure AD ID pro vašeho tenanta Azure AD. Následující kroky ukazují, jak získat ID tenanta.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako globální správce tenanta Azure.
1. V levém navigačním panelu vyberte ikonu **Azure Active Directory** .
1. Vyberte **Vlastnosti**.
1. V okně Vlastnosti vedle ID adresáře klikněte na ikonu **Kopírovat** , jak je vidět níže, a zkopírujte ID do schránky.

   ![Získává se ID adresáře z Azure Portal.](./media/howto-mfa-nps-extension-rdg/image1.png)

### <a name="install-the-nps-extension"></a>Instalace rozšíření serveru NPS

Instalace rozšíření serveru NPS na serveru s nainstalovanou rolí síťové zásady a přístup k službám (NPS). Tato operace funguje jako server RADIUS pro návrh.

> [!Important]
> Ujistěte se, že nenainstalujete rozšíření NPS na serveru služby Brána vzdálené plochy.
>

1. Stáhněte si [rozšíření serveru NPS](https://aka.ms/npsmfa).
1. Zkopírujte spustitelný soubor instalace (NpsExtnForAzureMfaInstaller.exe) na server NPS.
1. Na serveru NPS poklikejte na **NpsExtnForAzureMfaInstaller. exe**. Pokud se zobrazí výzva, klikněte na **Spustit**.
1. V dialogovém okně rozšíření NPS pro instalaci Azure MFA si přečtěte licenční podmínky pro software, zkontrolujte, že souhlasím **s licenčními podmínkami a ujednáními**, a klikněte na **nainstalovat**.
1. V dialogovém okně rozšíření serveru NPS pro instalaci Azure MFA klikněte na **Zavřít**.

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
1. Na příkazovém řádku prostředí PowerShell zadejte příkaz `cd ‘c:\Program Files\Microsoft\AzureMfa\Config’`a stiskněte klávesu **ENTER**.
1. Zadejte `.\AzureMfaNpsExtnConfigSetup.ps1`a stiskněte klávesu **ENTER**. Skript zkontroluje, zda je nainstalován modul Powershellu pro Azure Active Directory. Pokud nainstalovaná není, skript nainstaluje modul pro vás.

   ![Spuštění AzureMfaNpsExtnConfigSetup. ps1 ve službě Azure AD PowerShell](./media/howto-mfa-nps-extension-rdg/image4.png)
  
1. Jakmile skript ověří instalace modulu prostředí PowerShell, zobrazí dialogové okno modul Powershellu pro Azure Active Directory. V dialogovém okně zadejte svoje přihlašovací údaje a heslo správce Azure AD a klikněte na **Přihlásit**se.

   ![Ověřování ve službě Azure AD v prostředí PowerShell](./media/howto-mfa-nps-extension-rdg/image5.png)

1. Po zobrazení výzvy vložte ID adresáře, které jste zkopírovali do schránky dříve, a stiskněte klávesu **ENTER**.

   ![Vložení ID adresáře do PowerShellu](./media/howto-mfa-nps-extension-rdg/image6.png)

1. Tento skript vytvoří certifikát podepsaný svým držitelem a provede další změny v konfiguraci. Výstup by měl být podobně jako na následujícím obrázku.

   ![Výstup PowerShellu ukazující certifikát podepsaný svým držitelem](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>Konfigurace součástí serveru NPS na Brána vzdálené plochy

V této části nakonfigurujete zásady autorizace připojení brány vzdálené plochy a další nastavení protokolu RADIUS.

Tok ověřování vyžaduje, aby byly zprávy protokolu RADIUS vyměněny mezi Brána vzdálené plochy a serverem NPS, na kterém je nainstalováno rozšíření serveru NPS. To znamená, že nastavení klienta RADIUS musíte nakonfigurovat na Brána vzdálené plochy a instalaci rozšíření NPS server NPS.

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Nakonfigurujte zásady autorizace připojení brány vzdálené plochy pro použití centrální úložiště

Zásady autorizace připojení ke vzdálené ploše (CAP k vzdálené ploše) zadejte požadavky pro připojení k serveru služby Brána vzdálené plochy. CAP k vzdálené ploše můžete ukládat místně (výchozí) nebo může být uložený v centrální úložiště CAP ke vzdálené ploše, na kterém běží server NPS. Konfigurace integrace Azure MFA s vzdálené plochy, budete muset zadat příkaz use centrálního úložiště.

1. Na serveru Brána VP otevřete **Správce serveru**.
1. V nabídce klikněte na **nástroje**, přejděte na **Vzdálená plocha**a pak klikněte na **Správce brány vzdálené plochy**.
1. V Správce brány VP klikněte pravým tlačítkem na **\[název serveru\] (místní)** a klikněte na **vlastnosti**.
1. V dialogovém okně Vlastnosti vyberte kartu **úložiště Cap k vzdálené ploše** .
1. Na kartě úložiště CAP k vzdálené ploše vyberte **centrální server, na kterém běží server NPS**. 
1. Do pole **Zadejte název nebo IP adresu serveru, na kterém běží server NPS** , zadejte IP adresu nebo název serveru, na který jste nainstalovali rozšíření serveru NPS.

   ![Zadejte název nebo IP adresu vašeho serveru NPS.](./media/howto-mfa-nps-extension-rdg/image10.png)
  
1. Klikněte na **Přidat**.
1. V dialogovém okně **sdílený tajný klíč** zadejte sdílený tajný klíč a klikněte na tlačítko **OK**. Zkontrolujte záznam sdílený tajný klíč a bezpečně uložit záznam.

   >[!NOTE]
   >Sdílený tajný klíč se používá k navázání vztahu důvěryhodnosti mezi klienty a servery RADIUS. Vytvoření tajného klíče dlouhá a složitá.
   >

   ![Vytvoření sdíleného tajného klíče pro vytvoření vztahu důvěryhodnosti](./media/howto-mfa-nps-extension-rdg/image11.png)

1. Kliknutím na **OK** zavřete dialogové okno.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>Hodnota časového limitu protokolu RADIUS nakonfigurovat na NPS brány vzdálené plochy

Jak zajistit, že je čas na ověření přihlašovacích údajů uživatelů, provedení dvoustupňového ověřování, dostávat odpovědi a reagovat na zprávy pomocí protokolu RADIUS, je nutné upravit hodnoty časového limitu protokolu RADIUS.

1. Na serveru služby Brána VP otevřete Správce serveru. V nabídce klikněte na **nástroje**a potom klikněte na **Server NPS (Network Policy Server**).
1. V konzole **NPS (místní)** rozbalte položku **klienti a servery RADIUS**a vyberte **vzdálený server RADIUS**.

   ![Konzola pro správu serveru NPS (Network Policy Server) se vzdáleným serverem RADIUS](./media/howto-mfa-nps-extension-rdg/image12.png)

1. V podokně podrobností poklikejte na **skupinu serverů brány TS**.

   >[!NOTE]
   >Tato skupina serveru RADIUS byla vytvořena při konfiguraci centrálního serveru NPS zásady. Brána VP přeposílá zprávy pomocí protokolu RADIUS na tento server nebo skupinu serverů, pokud více než jedna ve skupině.
   >

1. V dialogovém okně **Vlastnosti skupiny serverů brány TS** vyberte IP adresu nebo název serveru NPS, který jste nakonfigurovali pro ukládání Cap ke vzdálené ploše, a pak klikněte na **Upravit**.

   ![Vyberte IP adresu nebo název serveru NPS, který jste nakonfigurovali dříve.](./media/howto-mfa-nps-extension-rdg/image13.png)

1. V dialogovém okně **Upravit server RADIUS** vyberte kartu **Vyrovnávání zatížení** .
1. Na kartě **Vyrovnávání zatížení** v poli **počet sekund bez odpovědi, než je žádost považována za vyřazené** pole, změňte výchozí hodnotu ze 3 na hodnotu v rozmezí 30 až 60 sekund.
1. V poli **počet sekund mezi požadavky, když je server identifikován jako nedostupný** , změňte výchozí hodnotu 30 sekund na hodnotu, která je větší nebo rovna hodnotě, kterou jste zadali v předchozím kroku.

   ![Úprava nastavení časového limitu serveru RADIUS na kartě vyrovnávání zatížení](./media/howto-mfa-nps-extension-rdg/image14.png)

1. Dvakrát klikněte na **OK** , čímž zavřete dialogová okna.

### <a name="verify-connection-request-policies"></a>Ověření zásad vyžádání nového připojení

Ve výchozím nastavení při konfiguraci brány VP pro použití zásady centrálního úložiště pro zásady autorizace připojení, je Brána VP nakonfigurované ke směrování žádostí Zakončení na server NPS. Server NPS pomocí rozšíření Azure MFA nainstalovali, zpracuje žádost o přístup protokolu RADIUS. Následující kroky ukazují, jak ověřit zásady vyžádání nového připojení výchozí.

1. Na Brána VP v konzole NPS (místní) rozbalte **zásady**a vyberte **zásady vyžádání nového připojení**.
1. Poklikejte na **zásady autorizace brány TS**.
1. V dialogovém okně **Vlastnosti zásad autorizace brány TS** klikněte na kartu **Nastavení** .
1. Na kartě **Nastavení** klikněte v části předávání žádosti o připojení na **ověřování**. Klient protokolu RADIUS konfigurován pro směrování požadavků pro ověřování.

   ![Konfigurovat nastavení ověřování určující skupinu serverů](./media/howto-mfa-nps-extension-rdg/image15.png)

1. Klikněte na tlačítko **Storno**.

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>Konfigurace serveru NPS na serveru, kde je nainstalovaná rozšíření NPS

Server NPS, kde je nainstalovaná rozšíření NPS musí být schopen výměna zpráv pomocí protokolu RADIUS se serverem NPS na Brána vzdálené plochy. Pokud chcete povolit tento výměně zpráv, musíte nakonfigurovat komponenty NPS na serveru, ve kterém je nainstalovaná služba rozšíření NPS.

### <a name="register-server-in-active-directory"></a>Registrace serveru ve službě Active Directory

Fungovat správně v tomto scénáři, NPS server musí být zaregistrované ve službě Active Directory.

1. Na serveru NPS otevřete **Správce serveru**.
1. V Správce serveru klikněte na **nástroje**a pak klikněte na **Server NPS (Network Policy Server**).
1. V konzole serveru NPS (Network Policy Server) klikněte pravým tlačítkem na **NPS (místní)** a pak klikněte na **zaregistrovat server ve službě Active Directory**.
1. Dvakrát klikněte na **OK** .

   ![Registrace serveru NPS ve službě Active Directory](./media/howto-mfa-nps-extension-rdg/image16.png)

1. Nechte konzolu otevřený pro další postup.

### <a name="create-and-configure-radius-client"></a>Vytvoření a konfigurace klienta protokolu RADIUS

Brána vzdálené plochy musí být nakonfigurován jako klienta RADIUS serveru NPS.

1. Na serveru NPS, na kterém je nainstalované rozšíření NPS, klikněte v konzole **NPS (místní)** na **Klienti RADIUS** pravým tlačítkem myši a klikněte na **Nový**.

   ![Vytvoření nového klienta RADIUS v konzole NPS](./media/howto-mfa-nps-extension-rdg/image17.png)

1. V dialogovém okně **Nový klient protokolu RADIUS** zadejte popisný název, například _Brána_, a IP adresu nebo název DNS serveru Brána vzdálené plochy.
1. Do polí **sdílený tajný klíč** a **Potvrdit sdílený tajný klíč** zadejte stejný tajný klíč, který jste použili dříve.

   ![Konfigurace popisného názvu a IP adresy nebo adresy DNS](./media/howto-mfa-nps-extension-rdg/image18.png)

1. Kliknutím na tlačítko **OK** zavřete dialogové okno Nový klient protokolu RADIUS.

### <a name="configure-network-policy"></a>Konfigurovat zásady sítě

Připomínáme, že server NPS pomocí rozšíření Azure MFA je úložiště určené centrální zásady pro povolení zásad připojení (CAP). Proto budete muset implementovat LIMITU na serveru NPS k autorizaci požadavků na platné připojení.  

1. Na serveru NPS otevřete konzolu NPS (místní), rozbalte **zásady**a klikněte na **zásady sítě**.
1. Klikněte pravým tlačítkem na **připojení k ostatním serverům pro přístup**a klikněte na **Duplikovat zásady**.

   ![Duplikovat připojení k zásadám jiných přístupových serverů](./media/howto-mfa-nps-extension-rdg/image19.png)

1. Klikněte pravým tlačítkem na **Kopírovat připojení k ostatním serverům pro přístup**a klikněte na **vlastnosti**.
1. V dialogovém okně **kopie připojení k jiným přístupovým serverům** zadejte do pole **název zásady**vhodný název, například _RDG_CAP_. Zaškrtněte políčko **Povolit zásadu**a vyberte **udělit přístup**. V případě potřeby můžete v **části Typ serveru pro přístup k síti**vybrat možnost **Brána vzdálené plochy**, nebo ji nechat **nespecifikovanou**.

   ![Pojmenování zásady, povolení a udělení přístupu](./media/howto-mfa-nps-extension-rdg/image21.png)

1. Klikněte na kartu **omezení** a zaškrtněte políčko **umožňuje klientům připojit se bez vyjednávání metody ověřování**.

   ![Úprava metod ověřování, aby se klienti mohly připojit](./media/howto-mfa-nps-extension-rdg/image22.png)

1. Případně můžete kliknout na kartu **podmínky** a přidat podmínky, které musí být splněny, aby bylo připojení autorizováno, například členství v určité skupině systému Windows.

   ![Volitelně zadejte podmínky připojení](./media/howto-mfa-nps-extension-rdg/image23.png)

1. Klikněte na tlačítko **OK**. Po zobrazení výzvy k zobrazení odpovídajícího tématu nápovědy klikněte na tlačítko **ne**.
1. Ujistěte se, že nové zásady se v horní části seznamu, že je zásada povolená, a že uděluje přístup.

   ![Přesuňte zásadu na začátek seznamu.](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Ověření konfigurace

Pokud chcete ověřit konfiguraci, budete muset přihlásit do služby Brána vzdálené plochy pomocí vhodné klienta protokolu RDP. Nezapomeňte použít účet, který povoluje vaše zásady autorizace připojení a je povolen pro Azure MFA.

Jak je znázorněno na obrázku níže, můžete použít stránku **Web Access vzdálené plochy** .

![Testování v Web Access vzdálené plochy](./media/howto-mfa-nps-extension-rdg/image25.png)

Po úspěšném zadání přihlašovacích údajů pro primární ověřování, dialogové okno připojení k vzdálené ploše zobrazí stav spuštění vzdáleného připojení, jak je znázorněno níže. 

Pokud jste úspěšně ověřit pomocí sekundární ověřování, které jste dříve nakonfigurovali v Azure MFA, jste připojení k prostředku. Pokud sekundární ověření není úspěšné, ale je odepřen přístup k prostředku. 

![Připojení ke vzdálené ploše iniciování vzdáleného připojení](./media/howto-mfa-nps-extension-rdg/image26.png)

V následujícím příkladu se používá ověřovací aplikaci na Windows phone pro sekundární ověřování.

![Příklad Windows Phone ověřovací aplikace ukazující ověřování](./media/howto-mfa-nps-extension-rdg/image27.png)

Jakmile úspěšně jste se ověřili pomocí metody sekundární ověřování, jste přihlášení jako za normálních okolností Brána vzdálené plochy. Vzhledem k tomu, že je třeba použít sekundární metodu ověřování s použitím mobilní aplikace na důvěryhodném zařízení, je proces přihlašování bezpečnější než v opačném případě.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Zobrazit protokoly Prohlížeče událostí pro události úspěšného přihlášení

Zobrazení událostí úspěšného přihlášení v protokolech prohlížeče událostí Windows, můžete použít následující příkaz prostředí Windows PowerShell, který dotazování protokolů zabezpečení Windows a Windows Terminálové služby.

K dotazování úspěšných přihlašovacích událostí v provozních protokolech brány _(Event prohlížeč and Services Logs\Microsoft\Windows\TerminalServices-Gateway\Operational)_ použijte následující příkazy PowerShellu:

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '300'} | FL`
* Tento příkaz zobrazí události Windows, které uživatel splněny požadavky na zásady autorizace prostředků (VP pro autorizaci prostředků) a má umožněný přístup.

![Zobrazení událostí pomocí prostředí PowerShell](./media/howto-mfa-nps-extension-rdg/image28.png)

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '200'} | FL`
* Tento příkaz zobrazí události, které zobrazit, když uživatel splněny požadavky na zásady autorizace připojení.

![Zobrazení zásad autorizace připojení pomocí PowerShellu](./media/howto-mfa-nps-extension-rdg/image29.png)

Můžete také zobrazit tento protokol a filtru na události s ID, 300 a 200. K dotazování událostí úspěšného přihlášení v protokolech prohlížeče událostí zabezpečení, použijte následující příkaz:

* `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`
* Tento příkaz můžete spustit na centrální server NPS nebo Server brány VP.

![Ukázka úspěšných událostí přihlášení](./media/howto-mfa-nps-extension-rdg/image30.png)

Můžete také zobrazit v protokolu zabezpečení nebo vlastní zobrazení síťové zásady a přístup ke službám, jak je znázorněno níže:

![Služba Síťové zásady a přístup Prohlížeč událostí](./media/howto-mfa-nps-extension-rdg/image31.png)

Na serveru, na který jste nainstalovali rozšíření serveru NPS pro Azure MFA, můžete najít Prohlížeč událostí protokoly aplikací specifické pro rozšíření na stránce _Application and Services Logs\Microsoft\AzureMfa_.

![Prohlížeč událostí protokoly aplikací AuthZ](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Řešení potíží s Průvodce

Pokud konfiguraci nefunguje podle očekávání, je prvním místem, kde začít řešení potíží s Ověřte, že je správce nakonfigurován na použití Azure MFA. Připojte se k [Azure Portal](https://portal.azure.com)uživatele. Pokud je uživatelé vyzváni k zadání sekundární ověřovací a můžete úspěšně ověřit, můžete eliminovat nesprávnou konfiguraci Azure MFA.

Pokud Azure MFA funguje pro uživatele, přečtěte si relevantní protokoly událostí. Patří mezi ně zabezpečení, provozní brány, protokoly událostí a Azure MFA, které jsou popsané v předchozí části.

Níže je příklad výstupu protokolu zabezpečení znázorňující neúspěšné událostí (událost 6273 ID).

![Ukázka události neúspěšného přihlášení](./media/howto-mfa-nps-extension-rdg/image33.png)

Níže je související události z protokolů AzureMFA:

![Ukázka Prohlížeč událostí protokolu Azure MFA](./media/howto-mfa-nps-extension-rdg/image34.png)

K provádění rozšířené možnosti řešení problémů, zkontrolujte soubory protokolu serveru NPS databáze v formát, ve kterém je nainstalovaná služba NPS. Tyto soubory protokolu se vytvoří ve složce _%systemroot%\System32\Logs_ jako textové soubory s oddělovači.

Popis těchto souborů protokolu najdete v tématu [Interpretace souborů protokolu ve formátu databáze NPS](https://technet.microsoft.com/library/cc771748.aspx). Položky v těchto protokolových souborech může být obtížné pro interpretaci bez jejich importování do tabulky nebo databáze. Pomoc při interpretaci souborů protokolu můžete najít několik služby ověřování v Internetu analyzátory online.

Následující obrázek ukazuje výstup jedné takové [shareware aplikace](https://www.deepsoftware.com/iasviewer)ke stažení.

![Ukázka analyzátoru služby IAS pro shareware App](./media/howto-mfa-nps-extension-rdg/image35.png)

Nakonec můžete pro další možnosti řešení potíží použít analyzátor protokolů, jako je například [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx).

Následující obrázek v Microsoft Message Analyzer zobrazuje síťový provoz filtrovaný podle protokolu RADIUS, který obsahuje uživatelské jméno **CONTOSO\AliceC**.

![Microsoft Message Analyzer zobrazující filtrovaný provoz](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>Další kroky

[Jak získat službu Azure Multi-Factor Authentication](concept-mfa-licensing.md)

[Brána vzdálené plochy Azure Multi-Factor Authentication Server pomocí protokolu RADIUS](howto-mfaserver-nps-rdg.md)

[Integrace místních adresářů do služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
