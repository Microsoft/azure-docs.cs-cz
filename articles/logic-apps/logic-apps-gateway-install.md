---
title: Instalace místní brány dat – Azure Logic Apps
description: Než budete moct získat přístup k datům z Azure Logic Apps, Stáhněte a nainstalujte místní bránu dat.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: 657bc704e33e89b1646dffa6123a27169e6c317a
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860755"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Instalace místní brány dat pro Azure Logic Apps

Než se budete moct připojit k místním zdrojům dat z Azure Logic Apps, Stáhněte a nainstalujte místní bránu dat do místního počítače. Brána funguje jako most, který poskytuje rychlý přenos dat a šifrování mezi zdroji dat místně (ne v cloudu) a vašimi Logic Apps. Stejnou instalaci brány můžete použít i u jiných cloudových služeb, jako je Power BI, Microsoft Flow, PowerApps a Azure Analysis Services. Informace o tom, jak používat bránu s těmito službami, najdete v těchto článcích:

* [Místní brána dat Power BI Microsoftu](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft PowerApps místní bránu dat](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Microsoft Flow místní bránu dat](https://flow.microsoft.com/documentation/gateway-manage/)
* [Azure Analysis Services místní bránu dat](../analysis-services/analysis-services-gateway.md)

Tento článek ukazuje, jak stáhnout, nainstalovat a nastavit místní bránu dat, abyste mohli přistupovat k místním zdrojům dat z Azure Logic Apps. Další informace o [tom, jak brána pro data funguje](#gateway-cloud-service) , najdete dál v tomto tématu.

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

I když samotné brány neúčtují další náklady, [Logic Apps cenový model](../logic-apps/logic-apps-pricing.md) se vztahuje na tyto konektory a další operace v Azure Logic Apps.

<a name="requirements"></a>

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

  * Abyste mohli nainstalovat a spravovat bránu, musíte použít stejný účet Azure. Během instalace použijete tento účet Azure k přidružení brány k vašemu počítači k předplatnému Azure. Později použijete stejný účet Azure při vytváření prostředku Azure v Azure Portal pro instalaci brány. 

  * Musíte se přihlásit pomocí pracovního nebo školního účtu, který je známý taky jako účet *organizace* , který vypadá nějak `username@contoso.com`takto. Nemůžete použít účty Azure B2B (Guest) ani osobní účty Microsoft, například @hotmail.com nebo @outlook.com.

    > [!TIP]
    > Pokud jste si zaregistrovali nabídku Office 365 a nezadali jste svoji pracovní e-mailovou adresu, může vaše `username@domain.onmicrosoft.com`adresa vypadat jako. Váš účet je uložený v rámci tenanta v Azure Active Directory (Azure AD). Ve většině případů je hlavní název uživatele (UPN) pro váš účet Azure AD stejný jako vaše e-mailová adresa.
    >
    > Pokud chcete použít [standardní předplatné sady Visual Studio](https://visualstudio.microsoft.com/vs/pricing/) , které je přidružené k účet Microsoft, [vytvořte nejprve tenanta ve službě Azure AD](../active-directory/develop/quickstart-create-new-tenant.md)nebo použijte výchozí adresář. Přidejte do adresáře uživatele s heslem a pak tomuto uživateli udělte přístup k vašemu předplatnému. 
    > Pak se můžete přihlásit při instalaci brány pomocí tohoto uživatelského jména a hesla.

* Tady jsou požadavky na váš místní počítač:

  **Minimální požadavky**

  * .NET Framework 4.6
  * 64. bitová verze systému Windows 7 nebo Windows Server 2008 R2 (nebo novější)

  **Doporučené požadavky**

  * PROCESOR s 8 jádry
  * 8 GB paměti
  * 64. bitová verze systému Windows Server 2012 R2 nebo novější
  * Úložiště SSD (Solid-State Drive) pro zařazování

  > [!NOTE]
  > Brána nepodporuje Windows Server 2016 Core.

* **Související otázky**

  * Místní bránu dat můžete nainstalovat pouze v místním počítači, nikoli v řadiči domény. Bránu ale nemusíte instalovat na stejný počítač jako zdroj dat. Pro všechny zdroje dat potřebujete jenom jednu bránu, takže nemusíte bránu instalovat pro každý zdroj dat.

    > [!TIP]
    > Abyste minimalizovali latenci, můžete bránu nainstalovat co nejblíže zdroji dat nebo do stejného počítače, za předpokladu, že máte oprávnění.

  * Nainstalujte bránu na počítač, který je v kabelové síti, připojený k Internetu, vždycky zapnutý a nepřejde do režimu spánku. V opačném případě se brána nemůže spustit a výkon se může zpomalit v bezdrátové síti.

  * Pokud plánujete používat ověřování systému Windows, ujistěte se, že jste nainstalovali bránu do počítače, který je členem stejného prostředí služby Active Directory jako vaše zdroje dat.

  * Oblast, kterou vyberete pro instalaci brány, je stejné umístění, které musíte vybrat při pozdějším vytvoření prostředku brány Azure pro vaši aplikaci logiky. Ve výchozím nastavení se jedná o stejné umístění jako váš tenant služby Azure AD, který spravuje váš účet Azure. Umístění však můžete změnit během instalace brány.

  * Brána má dva režimy: standardní režim a osobní režim, které platí jenom pro Power BI. Na stejném počítači nemůžete mít spuštěnou více než jednu bránu ve stejném režimu.

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Instalace brány dat

1. [Stáhněte a spusťte instalační program brány na místním počítači](https://aka.ms/on-premises-data-gateway-installer).

1. Po otevření instalačního programu vyberte **Další**.

   ![Úvod k instalačnímu programu](./media/logic-apps-gateway-install/gateway-intro-screen.png)

1. Vyberte **místní bránu dat (doporučeno)** , což je standardní režim, a pak vyberte **Další**.

   ![Vybrat režim brány](./media/logic-apps-gateway-install/select-gateway-mode.png)

1. Zkontrolujte minimální požadavky, ponechte výchozí instalační cestu, přijměte podmínky použití a pak vyberte **nainstalovat**.

   ![Kontrola požadavků a přijetí podmínek použití](./media/logic-apps-gateway-install/accept-terms.png)

1. Po úspěšné instalaci brány zadejte e-mailovou adresu účtu vaší organizace a pak vyberte **Přihlásit**se, například:

   ![Přihlaste se pomocí pracovního nebo školního účtu.](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   Nyní jste přihlášeni ke svému účtu.

1. **V tomto počítači** > vyberte zaregistrovat novou bránu. Tento krok zaregistruje instalaci brány pomocí [cloudové služby brány](#gateway-cloud-service).

   ![Registrace brány](./media/logic-apps-gateway-install/register-gateway.png)

1. Zadejte tyto informace pro instalaci brány:

   * Název brány, který je jedinečný v rámci vašeho tenanta Azure AD
   * Obnovovací klíč, který musí obsahovat alespoň osm znaků, které chcete použít
   * Potvrzení obnovovacího klíče

   ![Nastavení brány](./media/logic-apps-gateway-install/set-up-gateway.png)

   > [!IMPORTANT]
   > Uložte a zachovejte obnovovací klíč na bezpečném místě. Tento klíč budete potřebovat, pokud někdy chcete změnit umístění, přesunout, obnovit nebo převzít instalaci brány.

   Poznamenejte si možnost **Přidat existující cluster brány**, který vyberete při instalaci dalších bran pro [scénáře s vysokou dostupností](#high-availability).

1. Ověřte oblast pro cloudovou službu brány a [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) , kterou používá instalace brány. Ve výchozím nastavení má tato oblast stejné umístění jako tenant Azure AD pro váš účet Azure.

   ![Kontrolní oblast](./media/logic-apps-gateway-install/check-region.png)

1. Pokud chcete přijmout výchozí oblast, vyberte **Konfigurovat**. Pokud však výchozí oblast není ta, která je pro vás nejblíže, můžete změnit oblast.

   *Proč změnit oblast pro instalaci brány?*

   Pokud například chcete snížit latenci, můžete změnit oblast brány na stejnou oblast jako aplikace logiky. Případně můžete vybrat oblast nejbližší k místnímu zdroji dat. Váš *prostředek brány v Azure* a aplikace logiky můžou mít různá umístění.

   1. Vedle aktuální oblasti vyberte **změnit oblast**.

      ![Změna oblasti](./media/logic-apps-gateway-install/change-region.png)

   1. Na další stránce otevřete seznam **Vybrat oblast** , vyberte požadovanou oblast a vyberte **Hotovo**.

      ![Vyberte jinou oblast.](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. Přečtěte si informace v okně poslední potvrzení. V tomto příkladu se používá stejný účet pro Logic Apps, Power BI, PowerApps a Microsoft Flow, takže brána je dostupná pro všechny tyto služby. Až budete připraveni, vyberte **Zavřít**.

   ![Dokončená brána](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. Nyní [vytvořte prostředek Azure pro instalaci brány](../logic-apps/logic-apps-gateway-connection.md).

<a name="high-availability"></a>

## <a name="high-availability-support"></a>Podpora vysoké dostupnosti

Aby nedocházelo k jednomu bodu selhání pro místní přístup k datům, můžete mít k dispozici několik instalací brány (jenom standardní režim) s každou v jiném počítači a nastavit je jako cluster nebo skupina. Tímto způsobem, pokud je primární brána nedostupná, požadavky na data se směrují do druhé brány atd. Vzhledem k tomu, že na počítač můžete nainstalovat jenom jednu standardní bránu, musíte nainstalovat každou další bránu, která je v clusteru v jiném počítači. Všechny konektory, které pracují s místní bránou dat, podporují vysokou dostupnost. 

* V rámci stejného předplatného Azure už musíte mít jako primární bránu a obnovovací klíč pro tuto instalaci alespoň jednu instalaci brány.

* V primární bráně musí být spuštěná aktualizace brány ze listopadu 2017 nebo novější.

Po nastavení primární brány, když přejdete k instalaci jiné brány, vyberte **Přidat do existujícího clusteru brány**, vyberte primární bránu, která je první bránu, kterou jste nainstalovali, a zadejte pro tuto bránu obnovovací klíč. Další informace najdete v tématu [clustery s vysokou dostupností pro místní bránu dat](https://docs.microsoft.com/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster).

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Změna umístění, migrace, obnovení nebo převzetí existující brány

Pokud musíte změnit umístění brány, přesunout instalaci brány do nového počítače, obnovit poškozenou bránu nebo převzít vlastnictví existující brány, budete potřebovat obnovovací klíč, který byl k dispozici při instalaci brány.

1. Spusťte instalační program brány na počítači, který má existující bránu. Pokud nemáte nejnovější instalační program brány, Stáhněte si [nejnovější verzi brány](https://aka.ms/on-premises-data-gateway-installer).

   > [!NOTE]
   > Před obnovením brány na počítači, který má původní instalaci brány, musíte nejdřív odinstalovat bránu na tomto počítači. Tato akce odpojí původní bránu.
   > Pokud odeberete nebo odstraníte cluster brány pro jakoukoli cloudovou službu, nemůžete tento cluster obnovit.

1. Po otevření instalačního programu se přihlaste pomocí stejného účtu Azure, který jste použili k instalaci brány.

1. Vyberte možnost **migrovat, obnovit nebo převzetí existující brány** >  **, například**:

   ![Vyberte možnost migrace, obnovení nebo převzetí existující brány.](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. Vyberte z dostupných clusterů a bran a zadejte obnovovací klíč pro vybranou bránu, například:

   ![Vybrat bránu](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. Chcete-li změnit oblast, vyberte možnost **změnit oblast**a vyberte novou oblast.

1. Až budete připraveni, vyberte **Konfigurovat** , aby bylo možné dokončit úlohu.

## <a name="configure-proxy-or-firewall"></a>Konfigurace proxy serveru nebo brány firewall

Pokud vaše pracovní prostředí vyžaduje, aby provoz prochází proxy serverem pro přístup k Internetu, může toto omezení zabránit místní bráně dat v připojení ke cloudové službě brány a [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md). Další informace najdete v tématu [Konfigurace nastavení proxy serveru pro místní bránu dat](https://docs.microsoft.com/power-bi/service-gateway-proxy).

Pokud chcete zkontrolovat, jestli váš proxy server nebo brána firewall můžou blokovat připojení, zkontrolujte, jestli se počítač může skutečně připojit k Internetu a Azure Service Bus. Z příkazového řádku PowerShellu spusťte tento příkaz:

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

<a name="configure-ports"></a>

## <a name="configure-ports"></a>Konfigurace portů

Brána vytvoří odchozí připojení k Azure Service Bus a komunikuje na odchozích portech: TCP 443 (výchozí), 5671, 5672, 9350 až 9354. Brána nevyžaduje příchozí porty. Přečtěte si další informace o [Azure Service Bus a hybridních řešeních](../service-bus-messaging/service-bus-messaging-overview.md).

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

Některé proxy servery umožňují provoz jenom přes porty 80 a 443. Ve výchozím nastavení probíhá komunikace s Azure Service Bus na jiných portech než 443. Bránu můžete vynutit, aby komunikovala s Azure Service Bus přes protokol HTTPS, a ne přímo protokol TCP, ale v takovém případě může výrazně snížit výkon. Další informace najdete v tématu [vynucení komunikace HTTPS s Azure Service Bus](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication#force-https-communication-with-azure-service-bus).

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Účet služby systému Windows

Ve výchozím nastavení se instalace brány na místním počítači spouští jako účet služby systému Windows s názvem "místní služba brány dat". Instalace brány ale používá `NT SERVICE\PBIEgwService` název pro přihlašovací údaje účtu "přihlásit se jako" a má oprávnění "přihlásit jako službu".

> [!NOTE]
> Váš účet služby Windows se liší od účtu používaného pro připojení k místním zdrojům dat a z účtu Azure, který používáte při přihlašování ke cloudovým službám.

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Restartovat bránu

Brána dat se spouští jako služba systému Windows, takže stejně jako jakákoli jiná služba Windows můžete bránu spouštět a zastavovat různými způsoby. Další informace najdete v tématu [restart místní brány dat](https://docs.microsoft.com/data-integration/gateway/service-gateway-restart).

## <a name="tenant-level-administration"></a>Správa na úrovni tenanta

Aby bylo možné získat přehled o všech místních branách dat v tenantovi Azure AD, můžou se globální správci v tomto tenantovi přihlašovat do centra pro [správu Power Platform](https://powerplatform.microsoft.com) jako správce tenanta a vybrat možnost **brány dat** . Další informace najdete v tématu [Správa na úrovni tenanta pro místní bránu dat](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin).

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>Jak brána funguje

Brána dat zajišťuje rychlou a zabezpečenou komunikaci mezi vaší aplikací logiky, cloudovou službou brány a vaším místním zdrojem dat. Cloudová služba brány šifruje a ukládá vaše přihlašovací údaje ke zdroji dat a podrobnosti o bráně. Služba také směruje dotazy a jejich výsledky mezi vaší aplikací logiky, místní bránou dat a zdrojem dat místně.

Brána pracuje s branami firewall a používá jenom odchozí připojení. Veškerý provoz vychází z agenta brány na zabezpečený odchozí provoz. Brána přenáší data z místních zdrojů do šifrovaných kanálů prostřednictvím Azure Service Bus. Tato služba Service Bus vytváří kanál mezi bránou a volající službou, ale neukládá žádná data. Všechna data, která jsou přenášena přes bránu, jsou zašifrovaná.

![Architektura pro místní bránu dat](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

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
