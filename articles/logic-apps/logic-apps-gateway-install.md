---
title: Instalace místní brány dat – Azure Logic Apps | Microsoft Docs
description: Než budete moct získat přístup k datům z Azure Logic Apps, Stáhněte a nainstalujte místní bránu dat.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 36fb40dcee010ab68dc87eb6f81c0b2fb8977914
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376377"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Instalace místní brány dat pro Azure Logic Apps

Než se budete moct připojit k místním zdrojům dat z Azure Logic Apps, Stáhněte a nainstalujte místní bránu dat do místního počítače. Brána funguje jako most, který poskytuje rychlý přenos dat a šifrování mezi zdroji dat místně (ne v cloudu) a vašimi Logic Apps. Tento článek ukazuje, jak si můžete stáhnout, nainstalovat a nastavit místní bránu dat.

Můžete použít stejnou instalaci brány s jinými službami, například Power BI, Microsoft Flow, PowerApps a Azure Analysis Services. Přečtěte si další informace o [fungování brány dat](#gateway-cloud-service).

<a name="supported-connections"></a>

Brána podporuje místní [konektory](../connectors/apis-list.md#on-premises-connectors) v Azure Logic Apps pro tyto zdroje dat:

* BizTalk Server 2016
* Systém souborů
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle Database
* PostgreSQL
* SAP
* SharePoint Server
* SQL Server
* Teradata

Informace o tom, jak používat bránu s dalšími službami, najdete v těchto článcích:

* [Místní brána dat Power BI Microsoftu](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft PowerApps místní bránu dat](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Microsoft Flow místní bránu dat](https://flow.microsoft.com/documentation/gateway-manage/)
* [Azure Analysis Services místní bránu dat](../analysis-services/analysis-services-gateway.md)

<a name="requirements"></a>

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

  Během instalace brány se přihlásíte k tomuto účtu, abyste mohli přidružit instalaci brány k vašemu předplatnému Azure. Později také použijete stejný účet při vytváření prostředku Azure pro instalaci brány v Azure Portal.

* Tady jsou požadavky na váš místní počítač:

  **Minimální požadavky**

  * .NET Framework 4.5.2
  * 64. bitová verze systému Windows 7 nebo Windows Server 2008 R2 (nebo novější)

  **Doporučené požadavky**

  * PROCESOR s 8 jádry
  * 8 GB paměti
  * 64. bitová verze systému Windows Server 2012 R2 (nebo novější)

  > [!NOTE]
  > Brána nepodporuje Windows Server 2016 Core.

* **Důležité informace**

  * Místní bránu dat můžete nainstalovat pouze v místním počítači, nikoli v řadiči domény. Bránu ale nemusíte instalovat na stejný počítač jako zdroj dat. K dispozici je také pouze jedna brána pro všechny zdroje dat, takže není nutné instalovat bránu pro každý zdroj dat.

    > [!TIP]
    > Abyste minimalizovali latenci, můžete bránu nainstalovat co nejblíže zdroji dat nebo do stejného počítače, za předpokladu, že máte oprávnění.

  * Nainstalujte bránu na počítač, který je připojený k Internetu, vždycky zapnutý a *nepřejde do* režimu spánku. V opačném případě se brána nedá spustit. Navíc může dojít k výraznému snížení výkonu bezdrátové sítě.

  * Během instalace se můžete přihlásit jenom pomocí [pracovního nebo školního účtu](../active-directory/sign-up-organization.md) spravovaného službou Azure Active Directory (Azure AD), například @contoso.onmicrosoft.com, a ne účtem Azure B2B (Guest) nebo osobním @hotmail.com účet Microsoft, jako je například nebo. @outlook.com. Ujistěte se, že používáte stejný přihlašovací účet při registraci instalace brány v Azure Portal vytvořením prostředku brány. Tento prostředek brány pak můžete vybrat, když vytvoříte připojení z aplikace logiky k místnímu zdroji dat. [Proč je nutné použít pracovní nebo školní účet služby Azure AD?](#why-azure-work-school-account)

  > [!TIP]
  > Pokud jste si zaregistrovali nabídku Office 365 a nezadali jste svůj skutečný pracovní e-mail, můžete mít přihlašovací adresu, která vypadá jako v tomto příkladu:`username@domain.onmicrosoft.com` 
  >
  > Pokud chcete použít účet Microsoft, který má [standardní předplatné sady Visual Studio](https://visualstudio.microsoft.com/vs/pricing/), nejprve [v Azure Active Directory vytvořte adresář (tenant)](../active-directory/develop/quickstart-create-new-tenant.md), nebo použijte výchozí adresář s účet Microsoft. 
  > Přidejte do adresáře uživatele s heslem a pak tomuto uživateli udělte přístup k vašemu předplatnému. 
  > Pak se můžete přihlásit při instalaci brány pomocí tohoto uživatelského jména a hesla.

  * Oblast, kterou vyberete pro instalaci brány, určuje umístění, kam později zaregistrujete bránu v Azure tím, že vytvoříte prostředek Azure. Při vytváření tohoto prostředku brány v Azure musíte vybrat *stejné* umístění jako instalace brány. Výchozí oblastí je stejné umístění jako váš tenant služby Azure AD, který spravuje váš účet Azure, ale během instalace brány můžete umístění změnit.

  * Pokud už máte bránu, kterou jste nastavili pomocí instalačního programu starší verze než 14.16.6317.4, nemůžete změnit umístění brány spuštěním nejnovějšího instalačního programu. Pomocí nejnovějšího instalačního programu ale můžete místo toho nastavit novou bránu s umístěním, které chcete.
  
    Pokud máte instalační program brány, který je starší než verze 14.16.6317.4, ale ještě jste nenainstalovali bránu, můžete místo toho stáhnout a použít nejnovější instalační program.

## <a name="high-availability-support"></a>Podpora vysoké dostupnosti

Místní brána dat podporuje vysokou dostupnost, pokud máte více než jednu instalaci brány a nastavíte ji jako clustery. Pokud máte existující bránu, když přejdete na vytvořit jinou bránu, můžete volitelně vytvořit clustery s vysokou dostupností. Tyto clustery organizují brány do skupin, které mohou přispět k zamezení selhání v jednom bodě. Všechny místní konektory pro bránu dat teď podporují vysokou dostupnost.

Pokud chcete použít místní bránu dat, přečtěte si tyto požadavky a požadavky:

* V rámci stejného předplatného Azure už musíte mít jako primární bránu a obnovovací klíč pro tuto instalaci alespoň jednu instalaci brány.

* V primární bráně musí být spuštěná aktualizace brány ze listopadu 2017 nebo novější.

Po splnění těchto požadavků můžete při vytváření další brány vybrat možnost **Přidat do existujícího clusteru brány**, vybrat primární bránu pro cluster a zadat obnovovací klíč pro tuto primární bránu. Další informace najdete v tématu [clustery s vysokou dostupností pro místní bránu dat](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters).

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Instalace brány dat

1. [Stáhněte, uložte a spusťte instalační program brány na místním počítači](https://aka.ms/on-premises-data-gateway-installer).

1. Přijměte výchozí instalační cestu nebo zadejte umístění na počítači, kam chcete bránu nainstalovat.

1. Přečtěte si a přijměte podmínky použití a prohlášení o zásadách ochrany osobních údajů a zvolte možnost **nainstalovat**.

   ![Přijmout podmínky použití a prohlášení o zásadách ochrany osobních údajů](./media/logic-apps-gateway-install/accept-terms.png)

1. Po úspěšné instalaci brány zadejte e-mailovou adresu svého pracovního nebo školního účtu a pak vyberte **Přihlásit**se.

   ![Přihlaste se pomocí pracovního nebo školního účtu.](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

1.  >  **V tomto počítači vyberte možnost zaregistrovat novou bránu** **, která**zaregistruje instalaci brány pomocí [cloudové služby brány](#gateway-cloud-service).

   ![Registrace brány](./media/logic-apps-gateway-install/register-new-gateway.png)

1. Zadejte tyto informace pro instalaci brány:

   * Název, který chcete nainstalovat

   * Obnovovací klíč, který chcete vytvořit, který musí mít aspoň osm znaků.

     > [!IMPORTANT]
     > Uložte a zachovejte obnovovací klíč na bezpečném místě. Tento klíč budete potřebovat při změně umístění brány nebo při migraci, obnovení nebo převzetí existující brány.

   * Potvrzení obnovovacího klíče

     ![Nastavení brány](./media/logic-apps-gateway-install/set-up-gateway.png)

1. Ověřte oblast vybranou pro cloudovou službu brány a Azure Service Bus, kterou používá instalace brány.

   ![Kontrolní oblast](./media/logic-apps-gateway-install/check-region.png)

   > [!IMPORTANT]
   > Pro změnu této oblasti po dokončení instalace brány budete potřebovat obnovovací klíč pro tuto instalaci brány. Také je nutné bránu odinstalovat a znovu nainstalovat. Další informace najdete v tématu [Změna umístění, migrace, obnovení nebo převzetí existující brány](#update-gateway-installation).

   *Proč změnit oblast pro instalaci brány?*

   Pokud například chcete snížit latenci, můžete změnit oblast brány na stejnou oblast jako aplikace logiky. Případně můžete vybrat oblast nejbližší k místnímu zdroji dat.    Váš *prostředek brány v Azure* a aplikace logiky můžou mít různá umístění.

1. Pokud chcete přijmout výchozí oblast, klikněte na **Konfigurovat**. Pokud chcete změnit výchozí oblast, použijte následující postup:

   1. Vedle aktuální oblasti vyberte **změnit oblast**.

      ![Změna oblasti](./media/logic-apps-gateway-install/change-region.png)

   1. Na další stránce otevřete seznam **Vybrat oblast** , vyberte požadovanou oblast a zvolte možnost **Hotovo**.

      ![Vyberte jinou oblast.](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. Po zobrazení stránky s potvrzením klikněte na tlačítko **Zavřít**.

   Instalační program potvrdí, že je vaše brána nyní online a připravená k použití.

   ![Dokončená brána](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. Nyní Zaregistrujte bránu v Azure [vytvořením prostředku Azure pro instalaci brány](../logic-apps/logic-apps-gateway-connection.md).

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Změna umístění, migrace, obnovení nebo převzetí existující brány

Pokud musíte změnit umístění brány, přesunout instalaci brány do nového počítače, obnovit poškozenou bránu nebo převzít vlastnictví existující brány, budete potřebovat obnovovací klíč, který byl k dispozici při instalaci brány. Tato akce odpojí starou bránu.

1. V Ovládacích panelech počítače, navštivte **ovládací panel** **programy a funkce**. V seznamu programy vyberte **místní brána dat**a pak zvolte **odinstalovat**.

1. [Přeinstalujte místní bránu dat](https://aka.ms/on-premises-data-gateway-installer).

1. Po otevření instalačního programu se přihlaste pomocí stejného pracovního nebo školního účtu, který jste předtím použili k instalaci brány.

1. Vyberte **migrace, obnovení nebo převzetí existující brány**a pak zvolte **Další**.

   ![Vyberte možnost migrace, obnovení nebo převzetí existující brány.](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. V části **dostupné brány** nebo **dostupné clustery brány**vyberte instalaci brány, kterou chcete změnit. Zadejte obnovovací klíč pro instalaci brány.

   ![Vybrat primární bránu](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. Chcete-li změnit oblast, vyberte možnost **změnit oblast** a novou oblast.

1. Až skončíte, klikněte na **Konfigurovat**.

## <a name="configure-proxy-or-firewall"></a>Konfigurace proxy serveru nebo brány firewall

Místní brána dat vytvoří odchozí připojení k [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). Pokud vaše pracovní prostředí vyžaduje, aby provoz procházel proxy serverem pro přístup k Internetu, může toto omezení zabránit bráně dat v připojení ke cloudové službě brány. Pokud chcete zjistit, jestli vaše síť používá proxy server, přečtěte si tento článek na adrese superuser.com:

[Návody vědět, co proxy server používám? (SuperUser.com)](https://superuser.com/questions/346372/how-do-i-know-what-proxy-server-im-using)

Informace o poskytnutí informací o proxy serveru pro bránu najdete v tématu [Konfigurace nastavení proxy serveru](https://docs.microsoft.com/power-bi/service-gateway-proxy). Pokud chcete zkontrolovat, jestli váš proxy server nebo brána firewall můžou blokovat připojení, zkontrolujte, jestli se Váš počítač může skutečně připojit k Internetu a [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). Z příkazového řádku PowerShellu spusťte tento příkaz:

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

> [!NOTE]
> Tento příkaz testuje jenom připojení k síti a připojení k Azure Service Bus. Příkaz nedělá cokoli s bránou nebo cloudovou službou brány, která šifruje a ukládá vaše přihlašovací údaje a podrobnosti o bráně. 
>
> Tento příkaz je také k dispozici pouze v systému Windows Server 2012 R2 nebo novějším a Windows 8.1 nebo novějším. V dřívějších verzích operačního systému můžete k otestování připojení použít program Telnet. Přečtěte si další informace o [Azure Service Bus a hybridních řešeních](../service-bus-messaging/service-bus-messaging-overview.md).

Výsledky by měly vypadat podobně jako v tomto příkladu s **TcpTestSucceeded** nastavenou na **hodnotu true**:

```text
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

Pokud **TcpTestSucceeded** není nastavené na **true**, brána firewall může bránu zablokovat. Pokud chcete být komplexní, nahraďte hodnoty **ComputerName** a **port** hodnotami uvedenými v části [Konfigurace portů](#configure-ports) v tomto článku.

Brána firewall může také blokovat připojení Azure Service Bus k datovým centrům Azure. Pokud k tomuto scénáři dojde, schvalte (odblokovat) všechny IP adresy pro ta datacentra ve vaší oblasti. Pro tyto IP adresy [Získejte seznam IP adres Azure](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="configure-ports"></a>Konfigurace portů

Brána vytvoří odchozí připojení k [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) a komunikuje na odchozích portech: TCP 443 (výchozí), 5671, 5672, 9350 až 9354. Brána nevyžaduje příchozí porty. Přečtěte si další informace o [Azure Service Bus a hybridních řešeních](../service-bus-messaging/service-bus-messaging-overview.md).

Brána používá tyto plně kvalifikované názvy domén:

| Názvy domén | Odchozí porty | Popis |
| ------------ | -------------- | ----------- |
| *.analysis.windows.net | 443 | HTTPS |
| *.core.windows.net | 443 | HTTPS |
| *.frontend.clouddatahub.net | 443 | HTTPS |
| *.login.windows.net | 443 | HTTPS |
| *.microsoftonline-p.com | 443 | Používá se pro ověřování v závislosti na konfiguraci. |
| *.msftncsi.com | 443 | Používá se k otestování připojení k Internetu, pokud je brána nedosažitelná služba Power BI. |
| *.servicebus.windows.net | 443, 9350-9354 | Naslouchací procesy v Service Bus Relay přes TCP (vyžaduje 443 pro získání tokenu Access Control) |
| *.servicebus.windows.net | 5671-5672 | Rozšířený protokol řízení front zpráv (AMQP) (AMQP) |
| login.microsoftonline.com | 443 | HTTPS |
||||

V některých případech se Azure Service Bus Připojení k IP adresám místo plně kvalifikovaných názvů domén. Můžete tedy chtít odblokovat IP adresy pro vaši oblast dat v bráně firewall. Pokud chcete místo domén použít přístup k IP adresám, můžete si stáhnout a použít [Seznam rozsahů IP adres Microsoft Azure datacentra](https://www.microsoft.com/download/details.aspx?id=41653). IP adresy v tomto seznamu jsou v zápisu [CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

### <a name="force-https-communication-with-azure-service-bus"></a>Vynutit komunikaci HTTPS s Azure Service Bus

Některé proxy servery umožňují provoz jenom přes porty 80 a 443. Ve výchozím nastavení probíhá komunikace s Azure Service Bus na jiných portech než 443. Bránu můžete vynutit, aby komunikovala s Azure Service Bus přes protokol HTTPS, a ne přímo protokol TCP, ale v takovém případě může výrazně snížit výkon. Chcete-li provést tuto úlohu, postupujte podle následujících kroků:

1. Přejděte do umístění pro místního klienta pro bránu dat, který můžete obvykle najít tady:```C:\Program Files\On-premises data gateway\Microsoft.PowerBI.EnterpriseGateway.exe```

   V opačném případě pro vyhledání umístění klienta otevřete konzolu služby ve stejném počítači, najděte **službu místní brány dat**a zobrazte **cestu ke spustitelné** vlastnosti.

1. Otevřete tento *konfigurační* soubor: **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

1. Změňte hodnotu **ServiceBusSystemConnectivityModeString** z **automatického rozpoznávání** na **https**:

   ```html
   <setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
      <value>Https</value>
   </setting>
   ```

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Účet služby systému Windows

V počítači, na který instalujete místní bránu dat, se brána spouští jako účet služby Windows s názvem "místní brána dat". Brána však používá pro přihlašovací údaje účtu "přihlásit se jako" název "NT SERVICE\PBIEgwService". Ve výchozím nastavení má brána oprávnění "přihlásit jako službu" na počítači, na který bránu instalujete. Účet služby systému Windows pro bránu se obvykle liší od účtu, který používáte pro připojení k místním zdrojům dat, a z pracovního nebo školního účtu, který používáte pro přihlášení ke cloudovým službám.

Chcete-li vytvořit a udržovat bránu v Azure Portal, musí mít tento účet služby Windows aspoň oprávnění **Přispěvatel** . Chcete-li ověřit tato oprávnění, přečtěte si téma [Správa přístupu pomocí RBAC a Azure Portal](../role-based-access-control/role-assignments-portal.md).

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Restartovat bránu

Brána dat se spouští jako služba systému Windows, takže stejně jako jakákoli jiná služba Windows můžete bránu spouštět a zastavovat různými způsoby. Můžete například otevřít příkazový řádek se zvýšenými oprávněními na počítači, na kterém je brána spuštěná, a spustit některý z těchto příkazů:

* Chcete-li službu zastavit, spusťte tento příkaz:
  
  `net stop PBIEgwService`

* Chcete-li spustit službu, spusťte tento příkaz:
  
  `net start PBIEgwService`

## <a name="tenant-level-administration"></a>Správa na úrovni tenanta

V současné době není k dispozici žádné jediné místo, kde můžou správci klientů spravovat všechny brány, které nainstalovali a nakonfigurovali ostatní uživatelé. Pokud jste správcem tenanta, můžete chtít, aby se uživatelé v organizaci mohli přidat jako správce každé brány, kterou nainstalují. Tímto způsobem můžete spravovat všechny brány ve vaší organizaci prostřednictvím stránky nastavení brány nebo pomocí [příkazů PowerShellu](/data-integration/gateway/service-gateway-powershell-support).

<a name="gateway-cloud-service"></a>

## <a name="how-does-the-gateway-work"></a>Jak brána funguje?

Brána dat zajišťuje rychlou a zabezpečenou komunikaci mezi vaší aplikací logiky, cloudovou službou brány a vaším místním zdrojem dat. Cloudová služba brány šifruje a ukládá vaše přihlašovací údaje ke zdroji dat a podrobnosti o bráně. Služba také směruje dotazy a jejich výsledky mezi vaší aplikací logiky, místní bránou dat a zdrojem dat místně.

Brána pracuje s branami firewall a používá jenom odchozí připojení. Veškerý provoz vychází z agenta brány na zabezpečený odchozí provoz. Brána přenáší data z místních zdrojů do šifrovaných kanálů prostřednictvím Azure Service Bus. Tato služba Service Bus vytváří kanál mezi bránou a volající službou, ale neukládá žádná data. Všechna data, která jsou přenášena přes bránu, jsou zašifrovaná.

![diagram-for-on-premises-data-gateway-flow](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

Tyto kroky popisují, co se stane, když uživatel v cloudu spolupracuje s elementem, který je připojený k místnímu zdroji dat:

1. Cloudová služba brány vytvoří dotaz společně se zašifrovanými přihlašovacími údaji pro zdroj dat a odešle dotaz do fronty, kterou má brána zpracovat.

1. Cloudová služba brány dotaz analyzuje a odešle požadavek do Azure Service Bus.

1. Místní brána dat se dotáže Azure Service Bus na nevyřízené žádosti.

1. Brána získá dotaz, dešifruje přihlašovací údaje a připojí se ke zdroji dat pomocí těchto přihlašovacích údajů.

1. Brána odešle dotaz do zdroje dat ke spuštění.

1. Výsledky se odešlou ze zdroje dat zpátky do brány a potom do cloudové služby brány. Cloudová služba brány pak výsledky použije.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="general"></a>Obecné

**OTÁZKA**: Potřebuji bránu pro zdroje dat v cloudu, jako je například Azure SQL Database? <br/>
**A**: Ne, brána se připojuje jenom k místním zdrojům dat.

**OTÁZKA**: Musí být brána nainstalovaná na stejném počítači jako zdroj dat? <br/>
**A**: Ne, brána se připojí ke zdroji dat pomocí zadaných informací o připojení. V tomto smyslu zvažte bránu jako klientskou aplikaci. Brána potřebuje jenom možnost připojit se k zadanému názvu serveru.

<a name="why-azure-work-school-account"></a>

**OTÁZKA**: Proč je nutné použít pracovní nebo školní účet k přihlášení? <br/>
**A**: Pracovní nebo školní účet můžete použít jenom při instalaci místní brány dat. Přihlašovací účet je uložený v tenantovi spravovaném pomocí Azure Active Directory (Azure AD). Obvykle se hlavní název uživatele (UPN) vašeho účtu Azure AD shoduje s e-mailovou adresou.

**OTÁZKA**: Kam se ukládají moje přihlašovací údaje? <br/>
**A**: Přihlašovací údaje, které zadáte pro zdroj dat, jsou zašifrované a uložené v cloudové službě brány. Přihlašovací údaje se dešifrují v místní bráně dat.

**OTÁZKA**: Existují nějaké požadavky na šířku pásma sítě? <br/>
**A**: Ověřte, že síťové připojení má dobrou propustnost. Každé prostředí je jiné a množství odesílaných dat může ovlivnit výsledky. Pokud chcete zaručit úroveň propustnosti mezi místním zdrojem dat a datacentry Azure, vyzkoušejte [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/). Pro zvýšení propustnosti Vyzkoušejte externí nástroj, jako je například test rychlosti Azure.

**OTÁZKA**: Jaká je latence pro spouštění dotazů na zdroj dat z brány? Jaká je nejlepší architektura? <br/>
**A**: Pokud chcete snížit latenci sítě, nainstalujte bránu co nejblíže zdroji dat. Pokud můžete bránu nainstalovat na skutečný zdroj dat, Tato blízkost minimalizuje zavedenou latenci. Zvažte také blízkost do datových center Azure. Pokud třeba vaše služba používá Západní USA Datacenter a máte SQL Server hostovaný na virtuálním počítači Azure, můžete chtít, aby virtuální počítač Azure byl také v oblasti Západní USA. Tato blízkost minimalizuje latenci a zabrání poplatkům za odchozí data na virtuálním počítači Azure.

**OTÁZKA**: Jak se výsledky odesílají zpět do cloudu? <br/>
**A**: Výsledky se odesílají prostřednictvím Azure Service Bus.

**OTÁZKA**: Existují nějaká příchozí připojení k bráně z cloudu? <br/>
**A**: Ne, brána používá odchozí připojení k Azure Service Bus.

**OTÁZKA**: Co když mi blokuje odchozí připojení? Co potřebuji otevřít? <br/>
**A**: Podívejte se na porty a hostitele, které brána používá.

**OTÁZKA**: Jaká je volaná skutečná služba systému Windows? <br/>
**A**: Na kartě služby ve Správci úloh je název služby "PBIEgwService" nebo služba brány Power BI Enterprise. V konzole služby je název služby "místní brána dat". Služba systému Windows používá "NT SERVICE\PBIEgwService" jako identifikátor SID služby (SSID).

**OTÁZKA**: Může služba brány systému Windows běžet s účtem Azure Active Directory? <br/>
**A**: Ne, služba systému Windows musí mít platný účet systému Windows.

### <a name="disaster-recovery"></a>Zotavení po havárii

**OTÁZKA**: Jaké možnosti jsou dostupné pro zotavení po havárii? <br/>
**A**: K obnovení nebo přesunutí brány můžete použít obnovovací klíč. Při instalaci brány zadejte obnovovací klíč.

**OTÁZKA**: Jaká je výhoda obnovovacího klíče? <br/>
**A**: Obnovovací klíč poskytuje způsob, jak migrovat nebo obnovit nastavení brány po havárii.

## <a name="troubleshooting"></a>Řešení potíží

Tato část popisuje některé běžné problémy, které byste mohli mít při nastavování a používání místní brány dat.

**OTÁZKA**: Proč se instalace brány nezdařila? <br/>
**A**: K tomuto problému může dojít, pokud je antivirový software v cílovém počítači zastaralý. Můžete buď aktualizovat antivirový software, nebo zakázat antivirový software, ale jenom během instalace brány, a pak software znovu povolit.

**OTÁZKA**: Proč se mi nezobrazuje instalace brány při vytváření prostředku brány v Azure? <br/>
**A**: K tomuto problému může dojít z těchto důvodů:

* Vaše instalace brány je už zaregistrovaná a deklarovaná jiným prostředkem brány v Azure. Po vytvoření prostředků brány se instalace brány nezobrazí v seznamu instance. Pokud chcete zkontrolovat registrace brány v Azure Portal, Projděte si všechny vaše prostředky Azure s typem **místních bran dat** pro *všechna* předplatná Azure.

* Identita Azure AD pro osobu, která nainstalovala bránu, se liší od osoby, která se přihlásila k Azure Portal. Ověřte, že jste přihlášeni se stejnou identitou, která bránu nainstalovala.

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

**OTÁZKA**: Kde se nacházejí protokoly brány? <br/>
**A**: Další informace najdete v [části **protokoly** ](#logs) dále v tomto článku.

**OTÁZKA**: Jak můžu zjistit, jaké dotazy se posílají do místního zdroje dat? <br/>
**A**: Můžete povolit trasování dotazů, které zahrnuje dotazy, které jsou odeslány. Po dokončení odstraňování potíží nezapomeňte změnit trasování dotazů zpátky na původní hodnotu. Když se zapne trasování dotazů, vytvoří se větší protokoly.

Můžete se také podívat na nástroje, které váš zdroj dat používá pro trasování dotazů. Můžete například použít rozšířené události nebo SQL Profiler pro SQL Server a Analysis Services.

### <a name="outdated-gateway-version"></a>Zastaralá verze brány

Pokud je verze brány zastaralá, mnohé problémy se můžou nastavovat. V rámci dobrého obecného postupu se ujistěte, že máte nejnovější verzi. Pokud jste bránu neaktualizovali na měsíc nebo déle, můžete zvážit instalaci nejnovější verze brány a zjistit, jestli můžete problém reprodukování.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Chyba: Nepovedlo se přidat uživatele do skupiny. (-2147463168 PBIEgwService Performance Log Users)

Tato chyba se může zobrazit, pokud se pokusíte nainstalovat bránu na řadič domény, což se nepodporuje. Ujistěte se, že jste nasadili bránu na počítač, který není řadičem domény.

<a name="logs"></a>

### <a name="logs"></a>Logs

Při řešení potíží vždy začněte shromažďováním a kontrolou protokolů brány. Protokoly můžete shromažďovat několika způsoby, ale nejjednodušší možností po instalaci brány prostřednictvím uživatelského rozhraní instalačního programu brány.

1. V počítači otevřete instalační program pro místní bránu dat.

1. V nabídce vlevo vyberte **Diagnostika**.

1. V části **protokoly brány**vyberte **exportovat protokoly**.

   ![Exportovat protokoly z instalačního programu brány](./media/logic-apps-gateway-install/export-logs.png)

Tady jsou další místa, kde můžete najít různé protokoly:

| Typ protokolu | Location |
|----------|----------|
| **Protokoly instalačního programu** | %localappdata%\Temp\On-premises_data_gateway_ <*rrrrmmdd*>. <*číslo*>. log |
| **Protokoly konfigurace** | C:\Users\<*username*> \AppData\Local\Microsoft\On-premises data gateway\GatewayConfigurator <*RRRRMMDD*>. <*Number*>. log |
| **Protokoly služby brány Enterprise** | C:\Users\PBIEgwService\AppData\Local\Microsoft\On-premises data gateway\Gateway <*rrrrmmdd*>. <*číslo*>. log |
|||

**Protokoly událostí**

Pokud chcete najít protokoly událostí pro bránu, postupujte podle těchto kroků:

1. V počítači s instalací brány otevřete **Prohlížeč událostí**.

1. Rozbalte **Prohlížeč událostí (místní)**  > **protokoly aplikací a služeb**.

1. Vyberte **službu místní brány dat**.

   ![Zobrazit protokoly událostí pro bránu](./media/logic-apps-gateway-install/event-viewer.png)

### <a name="review-slow-query-performance"></a>Kontrola pomalého výkonu dotazů

Pokud zjistíte, že dotazy běží pomalu prostřednictvím brány, můžete zapnout další protokolování, které vytváří výstup dotazů a jejich dob trvání. Tyto protokoly vám pomůžou najít, které dotazy jsou pomalé nebo dlouho spuštěné. Pro optimalizaci výkonu dotazů může být nutné upravit zdroj dat, například upravit indexy pro SQL Server dotazy.

Chcete-li určit dobu trvání dotazu, postupujte podle následujících kroků:

1. Přejděte do stejného umístění, ve kterém je klient brány, který můžete obvykle najít tady:```C:\Program Files\On-premises data gateway```

   V opačném případě pro vyhledání umístění klienta otevřete konzolu služby ve stejném počítači, najděte **službu místní brány dat**a zobrazte **cestu ke spustitelné** vlastnosti.

1. Otevřete a upravte tyto konfigurační soubory, jak je popsáno níže:

   * **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

     V tomto souboru změňte hodnotu **EmitQueryTraces** z **false** na **true** , aby brána mohla protokolovat dotazy odeslané z brány na zdroj dat:

     ```html
     <setting name="EmitQueryTraces" serializeAs="String">
        <value>true</value>
     </setting>
     ```

     > [!IMPORTANT]
     > Zapnutí nastavení EmitQueryTraces může významně zvýšit velikost protokolu na základě využití brány. Po dokončení kontroly protokolů se ujistěte, že jste znovu nahlásili EmitQueryTraces na **hodnotu false** , a nenechte toto nastavení pro dlouhou dobu.

   * **Microsoft.PowerBI.DataMovement.Pipeline.Diagnostics.dll.config**

     Pokud chcete mít k dispozici podrobné položky protokolu brány, včetně položek, které zobrazují dobu trvání, změňte hodnotu **TracingVerbosity** ze **4** na **5** pomocí některého z kroků:

     * V tomto konfiguračním souboru změňte hodnotu **TracingVerbosity** ze **4** na **5** .

       ```html
       <setting name="TracingVerbosity" serializeAs="String">
          <value>5</value>
       </setting>
       ```

     * Otevřete instalační program brány, vyberte **Diagnostika**, zapněte **Další protokolování**a pak zvolte **použít**:

       ![Zapnout další protokolování](./media/logic-apps-gateway-install/turn-on-additional-logging.png)

     > [!IMPORTANT]
     > Zapnutí nastavení TracingVerbosity může významně zvýšit velikost protokolu na základě využití brány. Až provedete kontrolu protokolů, ujistěte se, že jste znovu vypnuli **Další protokolování** v instalačním programu brány nebo znovu resetujete TracingVerbosity na **4** v konfiguračním souboru, ale nenechte toto nastavení pro dlouhou dobu.

1. Pro vyhledání trvání dotazu použijte následující postup:

   1. [Exportujte](#logs) a otevřete protokol brány.

   1. Chcete-li najít dotaz, vyhledejte typ aktivity, například:

      | Typ aktivity | Popis |
      |---------------|-------------|
      | MGEQ | Dotazy, které se spouštějí přes ADO.NET |
      | MGEO | Dotazy, které se spouštějí přes OLEDB |
      | MGEM | Dotazy, které se spouštějí z modulu hybridní webové aplikace |
      |||

   1. Poznamenejte si druhý identifikátor GUID, což je ID žádosti.

   1. Pokračujte v hledání typu aktivity, dokud nenajdete položku s názvem "FireActivityCompletedSuccessfullyEvent", která má dobu trvání v milisekundách. Potvrďte, že položka má stejné ID žádosti, například:

      ```text
      DM.EnterpriseGateway Verbose: 0 : 2016-09-26T23:08:56.7940067Z DM.EnterpriseGateway    baf40f21-2eb4-4af1-9c59-0950ef11ec4a    5f99f566-106d-c8ac-c864-c0808c41a606    MGEQ    21f96cc4-7496-bfdd-748c-b4915cb4b70c    B8DFCF12 [DM.Pipeline.Common.TracingTelemetryService] Event: FireActivityCompletedSuccessfullyEvent (duration=5004)
      ```

      > [!NOTE]
      > Položka "FireActivityCompletedSuccessfullyEvent" je podrobná a není protokolována, pokud není nastavení "TracingVerbosity" na úrovni 5.

### <a name="trace-traffic-with-fiddler"></a>Trasování provozu pomocí Fiddler

[Fiddler](https://www.telerik.com/fiddler) je bezplatný nástroj z Telerik, který monitoruje přenosy HTTP. Tento provoz můžete zkontrolovat pomocí služba Power BI z klientského počítače. Tato služba může zobrazit chyby a další související informace.

## <a name="next-steps"></a>Další kroky

* [Připojení k místním datům z Logic Apps](../logic-apps/logic-apps-gateway-connection.md)
* [Funkce Enterprise Integration](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Konektory pro Azure Logic Apps](../connectors/apis-list.md)
