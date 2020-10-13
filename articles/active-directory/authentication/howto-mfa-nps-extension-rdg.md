---
title: Integrace RDG s rozšířením NPS pro Azure MFA – Azure Active Directory
description: Integrujte svoji Brána vzdálené plochy infrastrukturu s Azure MFA pomocí rozšíření serveru NPS (Network Policy Server) pro Microsoft Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7399c378053859f96746840942c8c549b7b89a3
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91964702"
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>Integrace infrastruktury Brána vzdálené plochy pomocí rozšíření serveru NPS (Network Policy Server) a Azure AD

Tento článek poskytuje podrobné informace o integraci Brána vzdálené plochy infrastruktury s Azure Multi-Factor Authentication (MFA) pomocí rozšíření NPS (Network Policy Server) pro Microsoft Azure.

Rozšíření serveru NPS (Network Policy Server) pro Azure umožňuje zákazníkům chránit ověřování klientů protokol RADIUS (Remote Authentication Dial-In User Service) (RADIUS) pomocí cloudové [Multi-Factor Authentication Azure (MFA)](./concept-mfa-howitworks.md). Toto řešení poskytuje dvoustupňové ověřování pro přidání druhé vrstvy zabezpečení do přihlašování a transakcí uživatelů.

Tento článek poskytuje podrobné pokyny pro integraci infrastruktury serveru NPS s Azure MFA pomocí rozšíření NPS pro Azure. To umožňuje zabezpečené ověřování pro uživatele, kteří se pokoušejí přihlásit k Brána vzdálené plochy.

> [!NOTE]
> Tento článek by se neměl používat s nasazeními MFA serveru a měl by se používat jenom s nasazeními Azure MFA (cloudové).

Služba Síťové zásady a přístup (NPS) poskytuje organizacím možnost provádět tyto akce:

* Definujte centrální umístění pro správu a kontrolu síťových požadavků tím, že zadáte, kdo se může připojit, jaká data se mají povolit, jaká je doba připojení a úroveň zabezpečení, kterou klienti musí používat k připojení a tak dále. Místo zadání těchto zásad na každém serveru brány VPN nebo vzdálené plochy (RD) můžete tyto zásady zadat jednou v centrálním umístění. Protokol RADIUS poskytuje centralizované ověřování, autorizaci a monitorování účtů (AAA).
* Vytvořte a vyvynuťte zásady stavu klienta architektury NAP (Network Access Protection), které určují, jestli mají zařízení udělený neomezený nebo omezený přístup k síťovým prostředkům.
* Poskytněte způsob, jak vymáhat ověřování a autorizaci pro přístup k bezdrátovým přístupovým bodům 802.1 x a přepínačům sítě Ethernet.

Organizace obvykle používají server NPS (RADIUS) k zjednodušení a centralizaci správy zásad sítě VPN. Mnoho organizací ale taky používá server NPS ke zjednodušení a centralizaci správy zásad autorizace připojení k vzdálené ploše (RD).

Organizace také mohou integrovat server NPS s Azure MFA, aby zvýšil zabezpečení a poskytovala vysokou úroveň dodržování předpisů. To pomáhá zajistit, aby uživatelé při přihlášení k Brána vzdálené plochy navázali dvoustupňové ověřování. Uživatelům, kteří mají udělit přístup, musí zadat kombinaci uživatelského jména a hesla spolu s informacemi, které má uživatel v rámci svého řízení. Tyto informace musí být důvěryhodné a nelze je snadno duplikovat, například číslo mobilního telefonu, číslo pevné, aplikace v mobilním zařízení atd. RDG v současné době podporuje telefonní hovory a nabízená oznámení z metod aplikace Microsoft Authenticator pro 2FA. Další informace o podporovaných metodách ověřování najdete v části [Určení metod ověřování, které uživatelé mohou používat](howto-mfa-nps-extension.md#determine-which-authentication-methods-your-users-can-use).

Před dostupností rozšíření serveru NPS pro Azure museli zákazníci, kteří chtějí implementovat dvoustupňové ověřování pro integrovaná rozhraní NPS a Azure MFA, nakonfigurovat a udržovat samostatný server MFA v místním prostředí, jak je uvedeno v [Brána vzdálené plochy a v azure Multi-Factor Authentication Server pomocí protokolu RADIUS](howto-mfaserver-nps-rdg.md).

Dostupnost rozšíření serveru NPS pro Azure teď nabízí organizacím možnost nasadit řešení MFA založené na místním nasazení nebo cloudové řešení MFA, které zajistí ověřování klientů RADIUS.

## <a name="authentication-flow"></a>Tok ověřování

Aby mohli uživatelé udělit přístup k síťovým prostředkům prostřednictvím Brána vzdálené plochy, musí splňovat podmínky zadané v jedné zásadě autorizace připojení k vzdálené ploše a jedna zásada autorizace prostředků služby Vzdálená plocha (RAP). Cap k VP určují, kdo má oprávnění pro připojení k bránám VP. Zásady pro autorizaci VP určují síťové prostředky, jako jsou vzdálené plochy nebo vzdálené aplikace, ke kterým se uživatel může připojit prostřednictvím Brána VP.

Brána VP je možné nakonfigurovat tak, aby používala centrální úložiště zásad pro vzdálené plochy. Zásada zásad VP nemůže použít centrální zásadu, protože se zpracovává na Brána VP. Příkladem Brána VP nakonfigurovaného pro použití centrálního úložiště zásad pro službu Vzdálená plocha je klient RADIUS na jiný server NPS, který slouží jako centrální úložiště zásad.

Když je rozšíření serveru NPS pro Azure integrované s NPS a Brána vzdálené plochy, je postup úspěšného ověřování následující:

1. Server Brána vzdálené plochy obdrží požadavek na ověření od uživatele vzdálené plochy pro připojení k prostředku, jako je například relace vzdálené plochy. V případě klienta RADIUS Brána vzdálené plochy Server převede požadavek na zprávu Access-Request protokolu RADIUS a pošle zprávu na server RADIUS (NPS), na kterém je rozšíření NPS nainstalované.
1. Kombinace uživatelského jména a hesla se ověřuje ve službě Active Directory a uživatel je ověřený.
1. Pokud jsou splněné všechny podmínky uvedené v žádosti o připojení serveru NPS a zásady sítě (například omezení času nebo členství ve skupinách), rozšíření serveru NPS vyvolá požadavek na sekundární ověřování pomocí Azure MFA.
1. Azure MFA komunikuje s Azure AD, načítá podrobnosti o uživateli a provádí sekundární ověřování pomocí podporovaných metod.
1. Po úspěšném ověření MFA Azure MFA sdělí výsledek rozšíření serveru NPS.
1. Server NPS, ve kterém je rozšíření nainstalované, pošle na Brána vzdálené plochy Server zprávu RADIUS Access-Accept pro zásady CAP k vzdálené ploše.
1. Uživateli je udělen přístup k požadovanému síťovému prostředku prostřednictvím Brána VP.

## <a name="prerequisites"></a>Předpoklady

Tato část podrobně popisuje požadavky nezbytné před integrací Azure MFA s Brána vzdálené plochy. Než začnete, musíte mít splněné následující požadavky.  

* Infrastruktura služby Vzdálená plocha (RDS)
* Licence Azure MFA
* Software Windows serveru
* Role NPS (Network Policy and Access Services)
* Azure Active Directory synchronizován s místní službou Active Directory
* ID GUID Azure Active Directory

### <a name="remote-desktop-services-rds-infrastructure"></a>Infrastruktura služby Vzdálená plocha (RDS)

Je nutné, aby byla k dispozici fungující infrastruktura vzdálené plochy (Remote Desktop Services). Pokud to neuděláte, můžete tuto infrastrukturu v Azure rychle vytvořit pomocí následující šablony pro rychlé zprovoznění: [vytvoření nasazení kolekce relací vzdálené plochy](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment).

Pokud chcete rychle vytvořit místní infrastrukturu služby Vzdálená plocha pro účely testování, postupujte podle pokynů pro nasazení jednoho.
**Další informace**: [nasazení RDS pomocí rychlého startu Azure](/windows-server/remote/remote-desktop-services/rds-in-azure) a [základního nasazení infrastruktury VP](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)

### <a name="azure-mfa-license"></a>Licence Azure MFA

Vyžaduje se licence pro Azure MFA, která je dostupná prostřednictvím Azure AD Premium nebo jiných sad, které ji obsahují. Licence založené na spotřebě pro Azure MFA, třeba pro licence vázané na uživatele nebo na ověřování, nejsou kompatibilní s rozšířením NPS. Další informace najdete v tématu [Jak získat Azure Multi-Factor Authentication](concept-mfa-licensing.md). Pro účely testování můžete použít zkušební předplatné.

### <a name="windows-server-software"></a>Software Windows serveru

Rozšíření serveru NPS vyžaduje systém Windows Server 2008 R2 SP1 nebo novější s nainstalovanou službou role NPS. Všechny kroky v této části byly provedeny pomocí systému Windows Server 2016.

### <a name="network-policy-and-access-services-nps-role"></a>Role NPS (Network Policy and Access Services)

Služba role NPS poskytuje funkce serveru RADIUS a klienta a také službu stavu zásad přístupu k síti. Tato role musí být nainstalovaná aspoň na dvou počítačích v infrastruktuře: Brána vzdálené plochy a jiný členský server nebo řadič domény. Ve výchozím nastavení je role již přítomna v počítači nakonfigurovaném jako Brána vzdálené plochy.  Také je nutné nainstalovat roli serveru NPS alespoň do jiného počítače, jako je například řadič domény nebo členský server.

Informace o instalaci služby role NPS Windows Server 2012 nebo starší najdete v tématu [instalace serveru zásad stavu NAP](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd296890(v=ws.10)). Popis osvědčených postupů pro server NPS, včetně doporučení k instalaci NPS na řadič domény, najdete v tématu [osvědčené postupy pro server NPS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771746(v=ws.10)).

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Azure Active Directory synchronizován s místní službou Active Directory

Aby bylo možné použít rozšíření serveru NPS, musí být místní uživatelé synchronizováni se službou Azure AD a povolenými pro MFA. V této části se předpokládá, že místní uživatelé budou synchronizováni se službou Azure AD pomocí služby AD Connect. Informace o službě Azure AD Connect najdete v tématu [Integrace místních adresářů s Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>ID GUID Azure Active Directory

Pro instalaci rozšíření NPS potřebujete znát identifikátor GUID služby Azure AD. Pokyny pro vyhledání identifikátoru GUID služby Azure AD jsou uvedené níže.

## <a name="configure-multi-factor-authentication"></a>Konfigurace Multi-Factor Authentication

V této části najdete pokyny pro integraci Azure MFA s Brána vzdálené plochy. Jako správce musíte nakonfigurovat službu Azure MFA, aby mohli uživatelé sami zaregistrovat svoje zařízení nebo aplikace na více zařízeních.

Postupujte podle kroků v části [Začínáme s Azure Multi-Factor Authentication v cloudu](howto-mfa-getstarted.md) a povolte MFA pro uživatele Azure AD.

### <a name="configure-accounts-for-two-step-verification"></a>Konfigurace účtů pro dvoustupňové ověřování

Jakmile je pro MFA povolený účet, nebudete se moct přihlašovat k prostředkům, které řídí zásada MFA, dokud jste neúspěšně nenakonfigurovali důvěryhodné zařízení na použití pro druhý faktor ověřování a ověřili jste ověřování pomocí dvoustupňového ověřování.

Postupujte podle kroků v části [co Azure Multi-Factor Authentication znamená pro mě?](../user-help/multi-factor-authentication-end-user-first-time.md) k pochopení a správné konfiguraci zařízení pro MFA s vaším uživatelským účtem.

> [!IMPORTANT]
> Chování při přihlašování pro Brána vzdálené plochy neposkytuje možnost zadat ověřovací kód pomocí Azure Multi-Factor Authentication. Uživatelský účet musí být nakonfigurovaný pro ověřování na telefonu nebo pro aplikaci Microsoft Authenticator s nabízenými oznámeními.
>
> Pokud není jedna z těchto dvou metod ověřování pro uživatele nakonfigurovaná, nebude moct dokončit službu Azure Multi-Factor Authentication Challenge a přihlásit se k Brána vzdálené plochy.

## <a name="install-and-configure-nps-extension"></a>Instalace a konfigurace rozšíření serveru NPS

V této části najdete pokyny ke konfiguraci infrastruktury služby RDS pro použití Azure MFA pro ověřování klientů pomocí Brána vzdálené plochy.

### <a name="acquire-azure-active-directory-tenant-id"></a>Získat ID tenanta Azure Active Directory

V rámci konfigurace rozšíření serveru NPS musíte zadat přihlašovací údaje správce a ID Azure AD pro vašeho tenanta Azure AD. Chcete-li získat ID tenanta, proveďte následující kroky:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako globální správce tenanta Azure.
1. V nabídce Azure Portal vyberte **Azure Active Directory**nebo vyhledejte a vyberte **Azure Active Directory** na libovolné stránce.
1. Na stránce **Přehled** se zobrazí *informace o tenantovi* . Vedle *ID tenanta*vyberte ikonu **kopírování** , jak je znázorněno na následujícím ukázkovém snímku obrazovky:

   ![Získává se ID tenanta z Azure Portal.](./media/howto-mfa-nps-extension-rdg/azure-active-directory-tenant-id-portal.png)

### <a name="install-the-nps-extension"></a>Instalace rozšíření serveru NPS

Nainstalujte rozšíření serveru NPS na server, na kterém je nainstalovaná role NPS (Network Policy and Access Services). Tato funkce funguje jako server RADIUS pro návrh.

> [!IMPORTANT]
> Neinstalujte rozšíření serveru NPS na váš server Brána vzdálené plochy (RDG). Server RDG nepoužívá u svého klienta protokol RADIUS, takže toto rozšíření nemůže interpretovat a provádět MFA.
>
> Pokud jsou servery RDG a NPS s rozšířeními NPS různé servery, RDG používá server NPS interně ke komunikaci s ostatními servery NPS a k správné komunikaci používá protokol RADIUS.

1. Stáhněte si [rozšíření serveru NPS](https://aka.ms/npsmfa).
1. Zkopírujte spustitelný soubor instalačního programu (NpsExtnForAzureMfaInstaller.exe) na server NPS.
1. Na serveru NPS poklikejte na **NpsExtnForAzureMfaInstaller.exe**. Pokud se zobrazí výzva, klikněte na **Spustit**.
1. V dialogovém okně rozšíření NPS pro instalaci Azure MFA si přečtěte licenční podmínky pro software, zkontrolujte, že souhlasím **s licenčními podmínkami a ujednáními**, a klikněte na **nainstalovat**.
1. V dialogovém okně rozšíření serveru NPS pro instalaci Azure MFA klikněte na **Zavřít**.

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Konfigurace certifikátů pro použití s rozšířením NPS pomocí skriptu PowerShellu

Dále musíte nakonfigurovat certifikáty, které rozšíření serveru NPS použije k zajištění zabezpečené komunikace a záruky. Komponenty serveru NPS obsahují skript prostředí Windows PowerShell, který konfiguruje certifikát podepsaný svým držitelem pro použití se serverem NPS.

Skript provede následující akce:

* Vytvoří certifikát podepsaný svým držitelem.
* Přidruží veřejný klíč certifikátu k instančnímu objektu ve službě Azure AD.
* Uloží certifikát do úložiště místního počítače.
* Udělí uživateli v síti přístup k privátnímu klíči certifikátu.
* Restartování služby serveru NPS (Network Policy Server)

Pokud chcete používat vlastní certifikáty, musíte k instančnímu objektu ve službě Azure AD přidružit veřejný klíč certifikátu a tak dále.

Pokud chcete použít skript, zadejte rozšíření s přihlašovacími údaji správce Azure AD a s ID tenanta Azure AD, které jste zkopírovali dříve. Spusťte skript na každém serveru NPS, na který jste nainstalovali rozšíření serveru NPS. Potom udělejte následující:

1. Otevřete příkazový řádek Windows PowerShellu pro správu.
1. Na příkazovém řádku PowerShellu zadejte `cd 'c:\Program Files\Microsoft\AzureMfa\Config'` a stiskněte klávesu **ENTER**.
1. Zadejte `.\AzureMfaNpsExtnConfigSetup.ps1` a stiskněte klávesu **ENTER**. Skript zkontroluje, jestli je nainstalovaný modul PowerShellu Azure Active Directory. Pokud není nainstalován, skript nainstaluje modul za vás.

   ![Spuštění AzureMfaNpsExtnConfigSetup.ps1 ve službě Azure AD PowerShell](./media/howto-mfa-nps-extension-rdg/image4.png)
  
1. Jakmile skript ověří instalaci modulu PowerShellu, zobrazí se dialogové okno Azure Active Directory modulu PowerShellu. V dialogovém okně zadejte svoje přihlašovací údaje a heslo správce Azure AD a klikněte na **Přihlásit**se.

   ![Ověřování ve službě Azure AD v prostředí PowerShell](./media/howto-mfa-nps-extension-rdg/image5.png)

1. Po zobrazení výzvy vložte *ID tenanta* , které jste zkopírovali do schránky dříve, a stiskněte klávesu **ENTER**.

   ![Vložení ID tenanta do PowerShellu](./media/howto-mfa-nps-extension-rdg/image6.png)

1. Skript vytvoří certifikát podepsaný svým držitelem a provede další změny konfigurace. Výstup by měl vypadat podobně jako obrázek uvedený níže.

   ![Výstup PowerShellu ukazující certifikát podepsaný svým držitelem](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>Konfigurace součástí serveru NPS na Brána vzdálené plochy

V této části nakonfigurujete zásady autorizace Brána vzdálené plochy připojení a další nastavení protokolu RADIUS.

Tok ověřování vyžaduje, aby byly zprávy protokolu RADIUS vyměněny mezi Brána vzdálené plochy a serverem NPS, na kterém je nainstalováno rozšíření serveru NPS. To znamená, že musíte nakonfigurovat nastavení klienta RADIUS na Brána vzdálené plochy i na serveru NPS, na kterém je nainstalované rozšíření NPS.

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Konfigurace zásad autorizace Brána vzdálené plochy připojení pro použití centrálního úložiště

Zásady autorizace připojení ke vzdálené ploše (RD CAP) určují požadavky pro připojení k serveru Brána vzdálené plochy. Možnost Cap k VP se dá ukládat lokálně (ve výchozím nastavení) nebo můžou být uložená v centrálním úložišti CAP k vzdálené ploše, na kterém běží server NPS. Pokud chcete nakonfigurovat integraci Azure MFA s RDS, musíte zadat použití centrálního úložiště.

1. Na serveru Brána VP otevřete **Správce serveru**.
1. V nabídce klikněte na **nástroje**, přejděte na **Vzdálená plocha**a pak klikněte na **Správce brány vzdálené plochy**.
1. V Správce brány VP klikněte pravým tlačítkem myši na ** \[ název serveru \] (místní)** a klikněte na **vlastnosti**.
1. V dialogovém okně Vlastnosti vyberte kartu **úložiště Cap k vzdálené ploše** .
1. Na kartě úložiště CAP k vzdálené ploše vyberte **centrální server, na kterém běží server NPS**. 
1. Do pole **Zadejte název nebo IP adresu serveru, na kterém běží server NPS** , zadejte IP adresu nebo název serveru, na který jste nainstalovali rozšíření serveru NPS.

   ![Zadejte název nebo IP adresu vašeho serveru NPS.](./media/howto-mfa-nps-extension-rdg/image10.png)
  
1. Klikněte na **Přidat**.
1. V dialogovém okně **sdílený tajný klíč** zadejte sdílený tajný klíč a klikněte na tlačítko **OK**. Ujistěte se, že tento sdílený tajný klíč zaznamenáte a bezpečně uložíte záznam.

   >[!NOTE]
   >Sdílený tajný klíč se používá k navázání vztahu důvěryhodnosti mezi servery a klienty RADIUS. Vytvořte dlouhý a složitý tajný klíč.
   >

   ![Vytvoření sdíleného tajného klíče pro vytvoření vztahu důvěryhodnosti](./media/howto-mfa-nps-extension-rdg/image11.png)

1. Kliknutím na **OK** zavřete dialogové okno.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>Konfigurace hodnoty časového limitu protokolu RADIUS na Brána vzdálené plochy NPS

Aby bylo zajištěno, že bude k dispozici čas ověřovat přihlašovací údaje uživatelů, provádět dvoustupňové ověřování, přijímat odpovědi a reagovat na zprávy protokolu RADIUS, je nutné upravit hodnotu časového limitu POLOMĚRu.

1. Na serveru Brána VP otevřete Správce serveru. V nabídce klikněte na **nástroje**a potom klikněte na **Server NPS (Network Policy Server**).
1. V konzole **NPS (místní)** rozbalte položku **klienti a servery RADIUS**a vyberte **vzdálený server RADIUS**.

   ![Konzola pro správu serveru NPS (Network Policy Server) se vzdáleným serverem RADIUS](./media/howto-mfa-nps-extension-rdg/image12.png)

1. V podokně podrobností poklikejte na **skupinu serverů brány TS**.

   >[!NOTE]
   >Tato skupina serverů RADIUS byla vytvořena při konfiguraci centrálního serveru pro zásady serveru NPS. Brána VP přepošle zprávy protokolu RADIUS tomuto serveru nebo skupině serverů, pokud je ve skupině více než jedna.
   >

1. V dialogovém okně **Vlastnosti skupiny serverů brány TS** vyberte IP adresu nebo název serveru NPS, který jste nakonfigurovali pro ukládání Cap ke vzdálené ploše, a pak klikněte na **Upravit**.

   ![Vyberte IP adresu nebo název serveru NPS, který jste nakonfigurovali dříve.](./media/howto-mfa-nps-extension-rdg/image13.png)

1. V dialogovém okně **Upravit server RADIUS** vyberte kartu **Vyrovnávání zatížení** .
1. Na kartě **Vyrovnávání zatížení** v poli **počet sekund bez odpovědi, než je žádost považována za vyřazené** pole, změňte výchozí hodnotu ze 3 na hodnotu v rozmezí 30 až 60 sekund.
1. V poli **počet sekund mezi požadavky, když je server identifikován jako nedostupný** , změňte výchozí hodnotu 30 sekund na hodnotu, která je větší nebo rovna hodnotě, kterou jste zadali v předchozím kroku.

   ![Úprava nastavení časového limitu serveru RADIUS na kartě vyrovnávání zatížení](./media/howto-mfa-nps-extension-rdg/image14.png)

1. Dvakrát klikněte na **OK** , čímž zavřete dialogová okna.

### <a name="verify-connection-request-policies"></a>Ověření zásad žádosti o připojení

Ve výchozím nastavení platí, že když nakonfigurujete Brána VP pro použití centrálního úložiště zásad pro zásady autorizace připojení, Brána VP je nakonfigurované tak, aby servery NPS předaly požadavky na CAP. Server NPS s nainstalovaným rozšířením Azure MFA zpracovává žádost o přístup přes protokol RADIUS. Následující kroky ukazují, jak ověřit výchozí zásadu žádosti o připojení.  

1. Na Brána VP v konzole NPS (místní) rozbalte **zásady**a vyberte **zásady vyžádání nového připojení**.
1. Poklikejte na **zásady autorizace brány TS**.
1. V dialogovém okně **Vlastnosti zásad autorizace brány TS** klikněte na kartu **Nastavení** .
1. Na kartě **Nastavení** klikněte v části předávání žádosti o připojení na **ověřování**. Klient protokolu RADIUS je nakonfigurován pro přeposílání požadavků pro ověřování.

   ![Konfigurovat nastavení ověřování určující skupinu serverů](./media/howto-mfa-nps-extension-rdg/image15.png)

1. Klikněte na tlačítko **Storno**.

>[!NOTE]
> Další informace o vytvoření zásady vyžádání nového připojení najdete v článku Konfigurace dokumentace pro [zásady požadavků na připojení](/windows-server/networking/technologies/nps/nps-crp-configure#add-a-connection-request-policy) . 

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>Konfigurace serveru NPS na serveru, na kterém je nainstalováno rozšíření serveru NPS

Server NPS, na kterém je nainstalované rozšíření NPS, musí být schopný vyměňovat zprávy RADIUS se serverem NPS na Brána vzdálené plochy. Pokud chcete povolit výměnu zpráv, musíte nakonfigurovat komponenty serveru NPS na serveru, na kterém je nainstalovaná služba rozšíření NPS.

### <a name="register-server-in-active-directory"></a>Zaregistrovat server ve službě Active Directory

Aby bylo možné v tomto scénáři správně fungovat, musí být server NPS zaregistrován ve službě Active Directory.

1. Na serveru NPS otevřete **Správce serveru**.
1. V Správce serveru klikněte na **nástroje**a pak klikněte na **Server NPS (Network Policy Server**).
1. V konzole serveru NPS (Network Policy Server) klikněte pravým tlačítkem na **NPS (místní)** a pak klikněte na **zaregistrovat server ve službě Active Directory**.
1. Dvakrát klikněte na **OK** .

   ![Registrace serveru NPS ve službě Active Directory](./media/howto-mfa-nps-extension-rdg/image16.png)

1. Nechejte konzolu otevřenou pro další postup.

### <a name="create-and-configure-radius-client"></a>Vytvoření a konfigurace klienta protokolu RADIUS

Brána vzdálené plochy nutné nakonfigurovat jako klienta RADIUS serveru NPS.

1. Na serveru NPS, na kterém je nainstalované rozšíření NPS, klikněte v konzole **NPS (místní)** na **Klienti RADIUS** pravým tlačítkem myši a klikněte na **Nový**.

   ![Vytvoření nového klienta RADIUS v konzole NPS](./media/howto-mfa-nps-extension-rdg/image17.png)

1. V dialogovém okně **Nový klient protokolu RADIUS** zadejte popisný název, například _Brána_, a IP adresu nebo název DNS serveru Brána vzdálené plochy.
1. Do polí **sdílený tajný klíč** a **Potvrdit sdílený tajný klíč** zadejte stejný tajný klíč, který jste použili dříve.

   ![Konfigurace popisného názvu a IP adresy nebo adresy DNS](./media/howto-mfa-nps-extension-rdg/image18.png)

1. Kliknutím na tlačítko **OK** zavřete dialogové okno Nový klient protokolu RADIUS.

### <a name="configure-network-policy"></a>Konfigurace zásad sítě

Odvolá, že server NPS s rozšířením Azure MFA je určené centrální úložiště zásad pro zásady autorizace připojení (CAP). Proto je potřeba implementovat na server NPS CAP, aby se schvalovaly platné požadavky na připojení.  

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

1. Klikněte na **OK**. Po zobrazení výzvy k zobrazení odpovídajícího tématu nápovědy klikněte na tlačítko **ne**.
1. Ujistěte se, že nové zásady jsou v horní části seznamu, že je tato zásada povolená a že udělí přístup.

   ![Přesuňte zásadu na začátek seznamu.](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Ověřit konfiguraci

Pokud chcete konfiguraci ověřit, musíte se přihlásit k Brána vzdálené plochy pomocí vhodného klienta RDP. Ujistěte se, že používáte účet, který je povolený v zásadách autorizace připojení a je povolený pro Azure MFA.

Jak je znázorněno na obrázku níže, můžete použít stránku **Web Access vzdálené plochy** .

![Testování v Web Access vzdálené plochy](./media/howto-mfa-nps-extension-rdg/image25.png)

Po úspěšném zadání přihlašovacích údajů k primárnímu ověřování se v dialogovém okně připojení ke vzdálené ploše zobrazí stav iniciace vzdáleného připojení, jak je znázorněno níže. 

Pokud úspěšně ověříte sekundární metodou ověřování, kterou jste předtím nakonfigurovali v Azure MFA, budete připojeni k prostředku. Pokud se ale sekundární ověřování nezdařilo, budete mít přístup k prostředku odepřený. 

![Připojení ke vzdálené ploše iniciování vzdáleného připojení](./media/howto-mfa-nps-extension-rdg/image26.png)

V následujícím příkladu se ověřovací aplikace na Windows Phone používá k zajištění sekundárního ověřování.

![Příklad Windows Phone ověřovací aplikace ukazující ověřování](./media/howto-mfa-nps-extension-rdg/image27.png)

Po úspěšném ověření pomocí sekundární metody ověřování se k Brána vzdálené plochy přihlásíte jako normální. Vzhledem k tomu, že je třeba použít sekundární metodu ověřování s použitím mobilní aplikace na důvěryhodném zařízení, je proces přihlašování bezpečnější než v opačném případě.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Zobrazit protokoly Prohlížeč událostí pro úspěšné události přihlášení

Chcete-li zobrazit úspěšné události přihlášení v protokolech Prohlížeč událostí systému Windows, můžete zadat následující příkaz prostředí Windows PowerShell pro dotaz na protokoly zabezpečení Terminálové služby systému Windows a systému Windows.

K dotazování úspěšných přihlašovacích událostí v provozních protokolech brány _(Event prohlížeč and Services Logs\Microsoft\Windows\TerminalServices-Gateway\Operational)_ použijte následující příkazy PowerShellu:

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '300'} | FL`
* Tento příkaz zobrazí události systému Windows, které ukazují, že uživatel splnil požadavky na zásady autorizace prostředků (RAP) a byl mu udělen přístup.

![Zobrazení událostí pomocí prostředí PowerShell](./media/howto-mfa-nps-extension-rdg/image28.png)

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '200'} | FL`
* Tento příkaz zobrazí události, které se zobrazí, když uživatel splní požadavky na zásady autorizace připojení.

![Zobrazení zásad autorizace připojení pomocí PowerShellu](./media/howto-mfa-nps-extension-rdg/image29.png)

Můžete si také zobrazit tento protokol a filtr pro ID událostí, 300 a 200. Chcete-li zadat dotaz na události úspěšného přihlášení v protokolech prohlížeče událostí zabezpečení, použijte následující příkaz:

* `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`
* Tento příkaz lze spustit buď na centrálním serveru NPS, nebo na serveru Brána VP.

![Ukázka úspěšných událostí přihlášení](./media/howto-mfa-nps-extension-rdg/image30.png)

Můžete také zobrazit protokol zabezpečení nebo vlastní zobrazení služby síťové zásady a přístup, jak je znázorněno níže:

![Služba Síťové zásady a přístup Prohlížeč událostí](./media/howto-mfa-nps-extension-rdg/image31.png)

Na serveru, na který jste nainstalovali rozšíření serveru NPS pro Azure MFA, můžete najít Prohlížeč událostí protokoly aplikací specifické pro rozšíření na stránce _Application and Services Logs\Microsoft\AzureMfa_.

![Prohlížeč událostí protokoly aplikací AuthZ](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Průvodce odstraňováním potíží

Pokud konfigurace nefunguje podle očekávání, je první místo, kde začít s odstraňováním potíží, ověřit, jestli je uživatel nakonfigurovaný tak, aby používal Azure MFA. Připojte se k [Azure Portal](https://portal.azure.com)uživatele. Pokud se uživatelům zobrazí výzva k sekundárnímu ověření a je možné ho úspěšně ověřit, můžete eliminovat neplatnou konfiguraci Azure MFA.

Pokud pro uživatele pracuje Azure MFA, měli byste si projít příslušné protokoly událostí. Patří sem události zabezpečení, provozní brány a protokoly Azure MFA, které jsou popsány v předchozí části.

Níže je uvedený příklad výstupu protokolu zabezpečení zobrazujícího neúspěšnou událost přihlášení (ID události 6273).

![Ukázka události neúspěšného přihlášení](./media/howto-mfa-nps-extension-rdg/image33.png)

Níže je uvedená související událost z AzureMFA protokolů:

![Ukázka Prohlížeč událostí protokolu Azure MFA](./media/howto-mfa-nps-extension-rdg/image34.png)

Pokud chcete provádět pokročilé možnosti odstraňování potíží, Projděte si soubory protokolů ve formátu databáze NPS, kde je nainstalovaná služba NPS. Tyto soubory protokolu se vytvoří ve složce _%systemroot%\System32\Logs_ jako textové soubory s oddělovači.

Popis těchto souborů protokolu najdete v tématu [Interpretace souborů protokolu ve formátu databáze NPS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771748(v=ws.10)). Položky v těchto protokolových souborech se můžou obtížně interpretovat bez jejich importu do tabulky nebo databáze. Několik analyzátorů služby IAS můžete najít online, abyste vám pomohli interpretovat soubory protokolu.

Následující obrázek ukazuje výstup jedné takové [shareware aplikace](https://www.deepsoftware.com/iasviewer)ke stažení.

![Ukázka analyzátoru služby IAS pro shareware App](./media/howto-mfa-nps-extension-rdg/image35.png)

Nakonec můžete pro další možnosti řešení potíží použít analyzátor protokolů, jako je například [Microsoft Message Analyzer](/message-analyzer/microsoft-message-analyzer-operating-guide).

Následující obrázek v Microsoft Message Analyzer zobrazuje síťový provoz filtrovaný podle protokolu RADIUS, který obsahuje uživatelské jméno **CONTOSO\AliceC**.

![Microsoft Message Analyzer zobrazující filtrovaný provoz](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>Další kroky

[Jak získat službu Azure Multi-Factor Authentication](concept-mfa-licensing.md)

[Brána vzdálené plochy Azure Multi-Factor Authentication Server pomocí protokolu RADIUS](howto-mfaserver-nps-rdg.md)

[Integrace místních adresářů do služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md)