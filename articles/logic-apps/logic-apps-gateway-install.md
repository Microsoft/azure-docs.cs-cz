---
title: Nainstalovat bránu dat místní - Azure Logic Apps | Microsoft Docs
description: Než budete přistupovat ke zdrojům dat místně, nainstalujte bránu místní dat pro přenos dat rychlý a šifrování mezi zdrojů dat na místní a aplikacích logiky
keywords: přístup k datům na místní, přenos dat, šifrování, zdroje dat
services: logic-apps
documentationcenter: ''
author: jeffhollan
manager: jeconnoc
editor: ''
ms.assetid: 47e3024e-88a0-4017-8484-8f392faec89d
ms.service: logic-apps
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/14/2017
ms.author: LADocs; millopis; estfan
ms.openlocfilehash: 63ec26325e045d2ddc027194377e1604d083d82c
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300533"
---
# <a name="install-the-on-premises-data-gateway-for-azure-logic-apps"></a>Nainstalujte bránu dat na místě pro Azure Logic Apps

Předtím, než aplikace logiky můžete přistupovat ke zdrojům dat místně, budete muset nainstalovat a nastavit bránu dat na místě. Brána funguje jako mostu, který poskytuje přenos rychlé dat a šifrování mezi místními systémy a aplikace logiky. Brána předává data z místního zdroje na šifrované kanály přes Azure Service Bus. Veškerý provoz pochází jako zabezpečené odchozí provoz z agenta brány. Další informace o [fungování bránu dat](#gateway-cloud-service).

Brána podporuje připojení k těmto zdrojům dat místně:

*   BizTalk Server 2016
*   DB2  
*   Systém souborů
*   Informix
*   MQ
*   MySQL
*   Oracle Database
*   PostgreSQL
*   Aplikační server SAP 
*   Server zpráv SAP
*   SharePoint
*   SQL Server
*   Teradata

Tyto kroky ukazují, jak nejprve nainstalovat bránu dat na místě před [nastavit připojení mezi bránou rozhraní a aplikace logiky](./logic-apps-gateway-connection.md). Další informace o podporované konektory najdete v tématu [konektory pro Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list). 

Informace o tom, jak používat bránu s jinými službami, najdete v těchto článcích:

*   [Microsoft Power BI místní brány dat](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
*   [Služba Azure gateway místní dat služby Analysis Services](../analysis-services/analysis-services-gateway.md)
*   [Microsoft Flow místní brány dat](https://flow.microsoft.com/documentation/gateway-manage/)
*   [Microsoft PowerApps místní brány dat](https://powerapps.microsoft.com/tutorials/gateway-management/)

<a name="requirements"></a>

## <a name="requirements"></a>Požadavky

**minimální**

* Rozhraní .NET 4.5 framework
* 64bitová verze systému Windows 7 nebo Windows Server 2008 R2 (nebo novější)

**Doporučená**

* 8 jader procesoru
* 8 GB paměti
* 64bitová verze systému Windows 2012 R2 (nebo novější)

**Důležité informace**

* Nainstalujte bránu dat místně pouze v místním počítači.
Nelze nainstalovat bránu na řadiči domény.

   > [!TIP]
   > Nemáte bránu nainstalovat na stejném počítači jako zdroj dat. Pro zajištění minimální latence, můžete nainstalovat bránu jak nejblíže ke zdroji dat, nebo na stejném počítači, za předpokladu, že máte oprávnění.

* Neinstalujte bránu v počítači, který vypne, přejde do režimu spánku, nebo nemá připojení k Internetu, protože v těchto případech nelze spustit bránu. Navíc může sníží výkon brány přes bezdrátové sítě.

* Během instalace, budete muset přihlásit [pracovní nebo školní účet](https://docs.microsoft.com/azure/active-directory/sign-up-organization) , který je spravován službou Azure Active Directory (Azure AD), nikoli účet Microsoft.

  > [!TIP]
  > Pokud chcete použít účet Microsoft, který má Visual Studio s předplatné MSDN, nejprve [vytvořit adresář (klientů) v Azure Active Directory](../active-directory/develop/active-directory-howto-tenant.md) s účtem Microsoft, nebo použijte výchozí adresář. Přidat uživatele s heslem k adresáři a potom poskytnout přístup k vašemu předplatnému. Můžete pak přihlásit během instalace brány se toto uživatelské jméno a heslo.

  Musíte použít stejný pracovní nebo školní účet později na portálu Azure při vytváření a přidružte prostředek brány k vaší instalace brány. Když vytvoříte připojení mezi svou aplikaci logiky a místní zdroj dat pak vyberete tento prostředek brány. [Proč musím použít Azure AD pracovní nebo školní účet?](#why-azure-work-school-account)

  > [!TIP]
  > Pokud jste se zaregistrovali do nabídky služeb Office 365 a nedodal e-mailu samotnou práci, může vypadat přihlašovací adresa jeff@contoso.onmicrosoft.com. 

* Pokud máte existující bránu, kterou jste vytvořili pomocí Instalační program, který je starší než verze 14.16.6317.4, nelze změnit umístění vaší brány spuštěním nejnovější verzi Instalační služby. Můžete však použít nejnovější verzi instalačního programu nastavit novou bránu s umístění, které chcete místo toho.
  
  Pokud máte bránu instalační program, který je starší než verze 14.16.6317.4, ale nemáte nainstalovanou bránu ještě, můžete stáhnout a použít nejnovější verzi Instalační služby.

<a name="install-gateway"></a>

## <a name="install-the-data-gateway"></a>Nainstalujte bránu dat

1. [Stáhněte a spusťte instalační program brány na místním počítači](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409).

2. Přečtěte si a přijměte podmínky použití a ochrana osobních údajů příkaz.

3. Zadejte cestu v místním počítači, kam chcete bránu nainstalovat.

4. Pokud budete vyzváni, přihlaste se pomocí vaší Azure pracovního nebo školního účtu, nikoli účet Microsoft.

   ![Přihlaste se pomocí Azure pracovní nebo školní účet](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

5. Nyní zaregistrovat bránu nainstalované s [cloudové službě Brána pro](#gateway-cloud-service). Zvolte **registrace nové brány na tomto počítači**.

   Cloudové služby brány šifruje a ukládá pověření ke zdroji dat a podrobnosti brány. 
   Služba také směrování dotazy a jejich výsledky mezi svou aplikaci logiky, místní brána dat a zdroj dat místně.

6. Zadejte název pro instalaci brány. Vytvořit klíče pro obnovení a pak potvrďte obnovovací klíč. 

   > [!IMPORTANT] 
   > Obnovovací klíč musí obsahovat alespoň osm znaků. Zajistěte, aby si uložit a zachovat klíč na bezpečném místě. Tento klíč je také nutné, pokud chcete migrovat, obnovení nebo převzetí existující brány.

   1. Chcete-li změnit výchozí oblast pro cloudové službě Brána a používané při instalaci vaší brány Azure Service Bus, zvolte **změnit oblast**.

      ![Oblast změny](./media/logic-apps-gateway-install/change-region-gateway-install.png)

      Výchozí oblast je oblast přidruženou klientovi Azure AD.

   2. V podokně Další otevřete **vyberte oblast** vybrat jiné oblasti.

      ![Vyberte jinou oblast](./media/logic-apps-gateway-install/select-region-gateway-install.png)

      Například můžete vybrat stejné oblasti jako svou aplikaci logiky nebo vyberte oblast, která je nejblíže k místní zdroje dat, můžete snížit latenci. Brána prostředků a logiku aplikace může mít jiné umístění.

      > [!IMPORTANT]
      > Tato oblast nelze změnit po instalaci. Tato oblast také určuje a omezuje umístění, kde si můžete vytvořit prostředků Azure pro bránu. Když vytvoříte prostředek brány v Azure, proto ujistěte, že umístění prostředků odpovídá oblast, kterou jste vybrali v průběhu instalace brány.
      > 
      > Pokud chcete použít v jiné oblasti pro bránu později, musíte nastavit novou bránu.

   3. Až budete připraveni, zvolte **provádí**.

7. Teď můžete pomocí těchto kroků na portálu Azure, abyste mohli [vytvořit prostředek služby Azure pro bránu](../logic-apps/logic-apps-gateway-connection.md). 

Další informace o [fungování bránu dat](#gateway-cloud-service).

## <a name="migrate-restore-or-take-over-an-existing-gateway"></a>Migrace, obnovení nebo převzetí existující brány

K provedení těchto úloh, musí mít obnovovací klíč, který byl zadán při instalaci brány.

1. Z nabídky Start v počítači, zvolte **místní brána dat**.

2. Po otevření instalační program, přihlaste se pomocí Azure stejný pracovní nebo školní účet, který byl dříve používán se nainstalovat bránu.

3. Zvolte **migrace, obnovení nebo převzetí existující brány**.

4. Zadejte název a obnovení klíče pro bránu, kterou chcete migrovat, obnovení nebo převzít.

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Účet služby systému Windows

Místní brána dat běží jako služby systému Windows a jsou nastaveny na použití `NT SERVICE\PBIEgwService` Windows služby přihlašovací údaje. Ve výchozím nastavení brány má právo "Přihlásit jako službu" pro počítač, kde instalujete bránu. K vytváření a údržbě brány na portálu Azure, účet služby systému Windows musí mít minimálně **Přispěvatel** oprávnění. 

> [!NOTE]
> Účet služby systému Windows se liší od účtu použité pro připojování k místní datové zdroje a z Azure pracovní nebo školní účet použitý k přihlášení do cloudových služeb:

<a name="restart-gateway"></a>

## <a name="restart-the-gateway"></a>Restartujte bránu

Stejně jako ostatní služby systému Windows můžete spustit a zastavit službu několika způsoby. Můžete například otevřete příkazový řádek se zvýšenými oprávněními v počítači se spuštěným systémem brány a spusťte buď tyto příkazy:

* Chcete-li zastavit službu, spusťte tento příkaz:
  
    `net stop PBIEgwService`

* Chcete-li spustit službu, spusťte tento příkaz:
  
    `net start PBIEgwService`

## <a name="configure-a-firewall-or-proxy"></a>Konfigurace brány firewall nebo proxy server

Vytvoří odchozí připojení k bráně [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). Pokud chcete zadat informace o proxy serveru pro bránu, najdete v části [konfigurace nastavení proxy serveru](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy/).

Pokud chcete zkontrolovat, zda brána firewall nebo proxy, může blokovat připojení, zkontrolujte, jestli váš počítač ve skutečnosti připojit k Internetu a [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). Z řádku prostředí PowerShell spusťte tento příkaz:

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

> [!NOTE]
> Tento příkaz pouze Otestuje připojení k síti a připojení k Azure Service Bus. Příkaz proto nemá nic dělat s bránu nebo cloudové službě brány, který šifruje a ukládá přihlašovací údaje a podrobnosti brány. 
>
> Tento příkaz je taky pouze k dispozici v systému Windows Server 2012 R2 nebo novější a Windows 8.1 nebo novější. V dřívějších verzích operačního systému můžete k testování připojení služby Telnet. Další informace o [Azure Service Bus a hybridní řešení](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

Výsledky by měl vypadat podobně jako tento příklad:

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

Pokud **TcpTestSucceeded** není nastavený na **True**, vám může blokována bránou firewall. Pokud chcete být komplexní, nahraďte **ComputerName** a **Port** a hodnoty uvedené v části [konfigurace portů](#configure-ports) v tomto článku.

Brána firewall, mohou také blokovat připojení, které provádí Azure Service Bus v datových centrech Azure. Pokud tento scénář se stane, schválit (odblokovat) všechny IP adresy pro těchto datových center ve vašem regionu. Pro tyto IP adresy [získat seznam adres Azure IP zde](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="configure-ports"></a>Konfigurace portů

Vytvoří odchozí připojení k bráně [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) a komunikuje na odchozí porty: TCP 443 (výchozí), 5671, 5672, 9350 prostřednictvím 9354. Brána nevyžaduje příchozí porty. Další informace o [Azure Service Bus a hybridní řešení](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

| Názvy domén | Odchozí porty | Popis |
| ------------ | -------------- | ----------- |
| *.analysis.windows.net | 443 | HTTPS | 
| *.login.windows.net | 443 | HTTPS | 
| *.servicebus.windows.net | 5671-5672 | Pokročilé zpráv služby Řízení front Protocol (AMQP) | 
| *.servicebus.windows.net | 443, 9350-9354 | Moduly pro naslouchání na předávání přes Service Bus přes TCP (vyžaduje 443 pro získání tokenu řízení přístupu) | 
| *.frontend.clouddatahub.net | 443 | HTTPS | 
| *.core.windows.net | 443 | HTTPS | 
| login.microsoftonline.com | 443 | HTTPS | 
| *.msftncsi.com | 443 | Použít k testování připojení k Internetu, když brána nedostupná pro službu Power BI. | 
||||

Pokud budete muset schválit IP adresy místo domény, můžete stáhnout a použít [rozsahy IP Datacentra Azure Microsoft seznamu](https://www.microsoft.com/download/details.aspx?id=41653). V některých případech jsou vytvářeny připojení Azure Service Bus s IP adresou, nikoli plně kvalifikované názvy domény.

<a name="gateway-cloud-service"></a>

## <a name="how-does-the-data-gateway-work"></a>Jak funguje bránu dat?

Bránu dat umožňuje rychle a bezpečně komunikaci mezi svou aplikaci logiky, cloudové službě brány a zdroje dat na místě. 

![diagram-for-on-premises-data-gateway-flow](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

Takže když uživatel v cloudu komunikuje element, který je připojen k místní zdroj dat:

1. Cloudové služby brány vytvoří dotaz, společně s zašifrované přihlašovací údaje pro zdroje dat a odešle dotaz do fronty pro bránu ke zpracování.

2. Cloudové služby brány analyzuje dotaz a nabízených oznámení požadavek na Azure Service Bus.

3. Bránu dat místní dotáže Azure Service Bus pro žádosti čekající na vyřízení.

4. Brána získá dotaz, dešifruje přihlašovací údaje a připojí se ke zdroji dat pomocí těchto přihlašovacích údajů.

5. Brána odešle dotaz na zdroj dat pro spuštění.

6. Výsledky jsou odesílány ze zdroje dat zpět k bráně a ke cloudové službě brány. Cloudové služby Brána pak použije výsledky.

<a name="faq"></a>

## <a name="tenant-level-administration"></a>Úroveň správy klienta 

Aktuálně neexistuje žádné jediné místo, kde správci klientů můžou spravovat všechny brány, které jiní uživatelé nainstalován a nakonfigurován.  Pokud jste správce klienta, doporučujeme, abyste se požádat uživatele ve vaší organizaci můžete přidat jako správce pro všechny brány, který se má nainstalovat. To vám umožňuje spravovat všechny brány ve vaší organizaci prostřednictvím stránky nastavení brány, nebo přes [příkazy prostředí PowerShell](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters#powershell-support-for-gateway-clusters). 


## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="general"></a>Obecné

**Q**: Potřebuji bránu pro zdroje dat v cloudu, jako je například SQL Azure? <br/>
**A**: Ne. Bránu se připojí k místním zdrojům dat pouze.

**Q**: nemá brány musí být instalovány na stejném počítači jako zdroj dat? <br/>
**A**: Ne. Se brána připojí ke zdroji dat pomocí informací o připojení, který byl poskytnut. Vezměte v úvahu brány jako klientskou aplikaci v tomto smyslu. Brány musí právě schopnost připojit k názvu serveru, který byl poskytnut.

<a name="why-azure-work-school-account"></a>

**Q**: Proč mám používat Azure pracovní nebo školní účet pro přihlášení? <br/>
**A**: pouze můžete použít Azure pracovní nebo školní účet, když instalujete místní brána data. Váš přihlašovací účet je uložený v klientovi, který je spravován pomocí služby Azure Active Directory (Azure AD). Obvykle účet Azure AD hlavní název uživatele (UPN) odpovídá e-mailovou adresu.

**Q**: uložení pověření? <br/>
**A**: přihlašovací údaje, které zadáte pro zdroj dat jsou zašifrovány a uložené v cloudové službě brány. Přihlašovací údaje jsou v bráně místní data dešifrovat.

**Q**: existují všechny požadavky pro šířku pásma sítě? <br/>
**A**: doporučujeme, aby připojení k síti dobrý propustnost. Každé prostředí je jiné a množství dat odesílaných ovlivňuje výsledky. Pomocí ExpressRoute může pomoct zajistit úrovní propustnosti mezi místními a datových centrech Azure.
Můžete aplikaci Azure rychlost testovací nástroj třetí strany ke měřidla vaší propustnost.

**Q**: co je latence spouštět dotazy na zdroj dat z brány? Co je nejlepší architektura? <br/>
**A**: Chcete-li snížit latenci sítě, nainstalujte bránu co nejblíže zdroji dat jako možné. Pokud nainstalujete brány na skutečná data zdroj tento blízkosti minimalizuje latenci zavedená. Příliš zvažte v datacentrech. Například pokud používá službu západní USA datacenter a vy musíte SQL Server hostované ve virtuálním počítači Azure, svého virtuálního počítače Azure musí být v západní USA příliš. Tato blízkosti minimalizuje latenci a zabraňuje s nimi spojeným nákladům na virtuálním počítači Azure.

**Q**: jak se výsledky odesílají zpět do cloudu? <br/>
**A**: výsledky se odesílají přes Azure Service Bus.

**Q**: existují všechna příchozí připojení k bráně z cloudu? <br/>
**A**: Ne. Brána používá odchozí připojení k Azure Service Bus.

**Q**: Co když blokovat odchozí připojení? Co je potřeba otevřít? <br/>
**A**: najdete v části Nastavení portů a hostitelů, které používá bránu.

**Q**: co je aktuální služby Windows volána?<br/>
**A**: V služby brány se nazývá služba Power BI Enterprise Gateway.

**Q**: můžete bránu služby systému Windows spustit pomocí účtu Azure Active Directory? <br/>
**A**: Ne. Služba systému Windows musí mít platný účet systému Windows. Ve výchozím nastavení spouští službu s SID služby, NT SERVICE\PBIEgwService.

### <a name="high-availability-and-disaster-recovery"></a>Vysoká dostupnost a zotavení po havárii

**Q**: jaké možnosti jsou dostupné pro zotavení po havárii? <br/>
**A**: obnovovací klíč můžete použít k obnovení nebo přesunout bránu. Při instalaci brány, zadejte obnovovací klíč.

**Q**: co je výhodou obnovovací klíč? <br/>
**A**: obnovovací klíč poskytuje způsob, jak migrovat nebo obnovení po havárii nastavení brány.

**Q**: existují všechny plány pro povolení scénáře s vysokou dostupností s bránou? <br/>
**A**: některé konektory podporují scénáře s vysokou dostupností, jako systém souborů konektoru a dalších na cestě. Další informace najdete v tématu [clusterů vysokou dostupnost pro bránu místní data](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters).

## <a name="troubleshooting"></a>Řešení potíží

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

**Q**: jak můžete zjistit, co jsou dotazy se posílá místní zdroj dat? <br/>
**A**: můžete povolit trasování dotazů, který obsahuje dotazy, které se odesílají. Nezapomeňte změnit dotaz trasování zpět na původní hodnotu po dokončení odstraňování potíží. Trasování dotazů, které jsou zapnuté vytvoří větší protokoly.

Můžete také zobrazit nástroje, které má váš zdroj dat pro trasování dotazů. Můžete například použít rozšířených událostí nebo profileru SQL pro SQL Server a služby Analysis Services.

**Q**: kde jsou protokoly gateway? <br/>
**A**: viz nástroje později v tomto článku.

### <a name="update-to-the-latest-version"></a>Aktualizovat na nejnovější verzi

Mnohé problémy můžete surface, když se stane zastaralé verze brány. Jako vhodné obecné Ujistěte se, že používáte nejnovější verzi. Pokud jste neprovedli aktualizaci brány pro měsíc i déle, můžete zvažte instalaci nejnovější verze brány a v tématu, pokud jste reprodukujte problém.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Chyba: Nepodařilo se přidat uživatele do skupiny. (-2147463168 PBIEgwService výkonu protokolu uživatelů)

Může se tato chyba, pokud se pokusíte nainstalovat bránu na řadič domény, což není podporováno. Ujistěte se, která můžete nasadit bránu na počítači, který není řadičem domény.

## <a name="tools"></a>Nástroje

### <a name="collect-logs-from-the-gateway-configurer"></a>Shromažďování protokolů z brány configurer

Můžete shromáždit několik protokolů pro bránu. Vždy spusťte s protokoly!

#### <a name="installer-logs"></a>Instalační protokoly

`%localappdata%\Temp\Power_BI_Gateway_–Enterprise.log`

#### <a name="configuration-logs"></a>Konfigurace protokolů

`%localappdata%\Microsoft\Power BI Enterprise Gateway\GatewayConfigurator.log`

#### <a name="enterprise-gateway-service-logs"></a>Protokoly služby Enterprise gateway

`C:\Users\PBIEgwService\AppData\Local\Microsoft\Power BI Enterprise Gateway\EnterpriseGateway.log`

#### <a name="event-logs"></a>Protokoly událostí

Můžete najít v protokolech Brána pro správu dat a PowerBIGateway pod **protokoly aplikací a služeb**.

### <a name="fiddler-trace"></a>Fiddler trasování

[Fiddler](http://www.telerik.com/fiddler) je bezplatný nástroj na webu Telerik, který monitoruje provoz protokolu HTTP. Zobrazí se tato komunikace se službou Power BI v klientském počítači. Tato služba může zobrazit chyby a další související informace.

## <a name="next-steps"></a>Další postup
    
* [Připojit k místním datům z aplikace logiky](../logic-apps/logic-apps-gateway-connection.md)
* [Funkce Integrace organizace](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Konektory pro Azure Logic Apps](../connectors/apis-list.md)
