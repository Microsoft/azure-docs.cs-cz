---
title: Instalace místní brány dat – Azure Logic Apps | Dokumentace Microsoftu
description: Jak si stáhnout a nainstalovat na místní bránu dat z aplikací logiky přistupujete k datům v místním prostředí
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/20/2018
ms.reviewer: yshoukry, LADocs
ms.suite: integration
ms.openlocfilehash: 09e3879ed91a0e9c6d27940cae53f3e3f0397d7b
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39145202"
---
# <a name="install-the-on-premises-data-gateway-for-azure-logic-apps"></a>Instalace místní brány dat pro Azure Logic Apps

Předtím, než aplikace logiky můžete připojit k místním zdrojům dat, stáhněte a nainstalujte na místní bránu dat na místním počítači. Brána funguje jako most poskytující rychlý datové přenosy a šifrování mezi zdroje dat v místním prostředí (ne v cloudu) a logic apps. Tento článek popisuje, jak můžete stáhnout, nainstalovat a nastavit na místní bránu dat. 

Můžete použít stejnou instalaci brány s ostatními službami, jako je například Power BI, Microsoft Flow, PowerApps a Azure Analysis Services. Další informace o [jak brána dat funguje](#gateway-cloud-service).

<a name="supported-connections"></a>

Brána podporuje [místní konektory](../connectors/apis-list.md#on-premises-connectors) v Azure Logic Apps pro tyto zdroje dat:

*   BizTalk Server 2016
*   Systém souborů
*   IBM DB2  
*   Databáze IBM Informix
*   IBM MQ
*   MySQL
*   Oracle Database
*   PostgreSQL
*   Aplikační server SAP 
*   Server zpráv SAP
*   SharePoint Server
*   SQL Server
*   Teradata

Informace o tom, jak použít bránu s ostatními službami, najdete v těchto článcích:

* [Microsoft Power BI – místní brána dat](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft PowerApps na místní bránu dat](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Microsoft Flow na místní bránu dat](https://flow.microsoft.com/documentation/gateway-manage/)
* [Služba Azure Analysis Services na místní brány dat](../analysis-services/analysis-services-gateway.md)

<a name="requirements"></a>

## <a name="prerequisites"></a>Požadavky

* A [pracovního nebo školního účtu](../active-directory/fundamentals/sign-up-organization.md) , který má [předplatného Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption-guide/adoption-intro/subscription-explainer). Během instalace brány přihlášení k tomuto účtu tak instalaci brány můžete přidružit k vašemu předplatnému Azure. Později také použít stejný účet při vytváření prostředku Azure pro vaši instalaci brány na webu Azure Portal. Pokud nemáte ještě předplatné Azure <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrovat si bezplatný účet Azure</a>.

* Tady jsou požadavky pro místní počítač:

  **Minimální požadavky**
  * Rozhraní .NET framework 4.5.2
  * 64bitová verze Windows 7 nebo Windows Server 2008 R2 (nebo novější)

  **Doporučené požadavky**
  * Procesor s 8 jádry
  * 8 GB paměti
  * 64bitová verze systému Windows Server 2012 R2 (nebo novější)

* **Důležité informace**

  * Místní brány dat můžete nainstalovat pouze na místním počítači, není řadičem domény. Nemáte ale bránu nainstalovat na stejném počítači jako zdroj dat. Budete také potřebovat pouze jednu bránu pro všechny vaše zdroje dat, proto není potřeba nainstalovat bránu pro každý datový zdroj.

    > [!TIP]
    > Abyste minimalizovali latenci, můžete bránu instalujete jak nejblíže ke zdroji dat nebo na stejném počítači, za předpokladu, že máte oprávnění.

  * Bránu nainstalovat na počítač, který *nebude* vypnout, přejděte do režimu spánku nebo připojení k Internetu. Za těchto podmínek nemůže fungovat brány. 
  Výkon brány může také snížit v bezdrátové síti.

  * Během instalace, můžete pouze přihlásit se [pracovního nebo školního účtu](../active-directory/sign-up-organization.md) , který je spravován pomocí služby Azure Active Directory (Azure AD) a účet Microsoft. 
  Také, ujistěte se, že tento účet není Azure B2B účtu (Host). 
  Při registraci vaší instalace brány tak, že vytvoříte prostředek Azure pro vaši bránu, musíte použít také stejný přihlašovací účet na webu Azure Portal. 
  Když vytvoříte připojení z aplikace logiky ke zdroji dat v místním, pak můžete vybrat tento prostředek brány. 
  [Proč musí používat Azure AD pracovní nebo školní účet?](#why-azure-work-school-account)

  > [!TIP]
  > Pokud jste si zaregistrovali nabídku Office 365 a neposkytli svůj skutečný pracovní e-mail, může mít přihlašovací adresu, která vypadá jako v tomto příkladu: `username@domain.onmicrosoft.com` 
  >
  > Na používání účtu Microsoft, který má [Visual Studio na úrovni Standard předplatného](https://visualstudio.microsoft.com/vs/pricing/), první [vytvořit adresář (tenant) v Azure Active Directory](../active-directory/develop/active-directory-howto-tenant.md), nebo použijte výchozí adresář se svým účtem Microsoft. 
  > Přidat uživatele s heslem k adresáři a poskytnout přístup k vašemu předplatnému. 
  > Potom přihlásíte během instalace brány se toto uživatelské jméno a heslo.

  * Oblast, kterou vyberete pro vaši instalaci brány určuje umístění, ve kterém později zaregistrujete bránu v Azure tak, že vytvoříte prostředek Azure. 
  Když vytvoříte tento prostředek brány v Azure, musíte vybrat *stejné* umístění jako instalace brány. Výchozí oblast je na stejné umístění jako vašeho tenanta Azure AD, která spravuje váš účet Azure, ale můžete změnit umístění během instalace brány.

  * Pokud už máte bránu, která se nastavuje pomocí instalačního programu starší než verze 14.16.6317.4, nelze změnit umístění vaší brány spuštěním nejnovější verzi Instalační služby. Však nastavit novou bránu s umístěním, které chcete místo toho můžete nejnovější verzi Instalační služby.
  
    Pokud jste instalační program brány, která je starší než verze 14.16.6317.4, ale nemáte nainstalovanou bránu ještě, můžete stáhnout a místo toho použijte nejnovější verzi Instalační služby.

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Instalace brány dat

1. [Stáhnout, uložte a spusťte instalační program brány na místním počítači](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409).

2. Přijměte výchozí cestu instalace, nebo zadejte umístění ve vašem počítači, ve které chcete nainstalovat bránu.

3. Přečtěte si a přijměte podmínky použití a ochrana osobních údajů a klikněte na tlačítko **nainstalovat**.

   ![Přijměte podmínky použití a ochrana osobních údajů](./media/logic-apps-gateway-install/accept-terms.png)

4. Po úspěšné instalaci brány zadejte e-mailovou adresu pro váš pracovní nebo školní účet a zvolte **přihlášení**.

   ![Přihlaste se pomocí pracovního nebo školního účtu](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

5. Zvolte **registrovat novou bránu na tomto počítači** > **Další**, který registruje instalaci brány se [Cloudová služba brány](#gateway-cloud-service). 

   ![Registrace brány](./media/logic-apps-gateway-install/register-new-gateway.png)

6. Zadejte informace pro vaši instalaci brány:

   * Název, který chcete použít pro instalaci 

   * Obnovovací klíč, který chcete vytvořit, který musí mít alespoň osm znaků

     > [!IMPORTANT]
     > Uložte a zachovat obnovovací klíč na bezpečném místě. Tento klíč budete potřebovat, když změníte umístění brány, nebo když migrovat, obnovit nebo převzít existující bránu.

   * Potvrzení pro obnovovací klíč 

     ![Nastavení brány](./media/logic-apps-gateway-install/set-up-gateway.png)

7. Zkontrolujte oblast vybraná Cloudová služba brány a služby Azure Service Bus, který se používá ve vaší instalaci brány. 

   ![Zkontrolujte oblast](./media/logic-apps-gateway-install/check-region.png)

   > [!IMPORTANT]
   > Chcete-li změnit této oblasti po dokončení instalace brány, musíte pro tuto instalaci brány obnovovací klíč. Kromě toho je třeba odinstalovat a znovu nainstalovat bránu. Další informace najdete v tématu [změnit umístění, migrovat, obnovit nebo převzít existující bránu](#update-gateway-installation).

   *Proč změnit oblast pro vaši instalaci brány?* 

   Například pokud chcete snížit latenci, můžete změnit oblast vaší brány do stejné oblasti jako aplikace logiky. 
   Nebo můžete vybrat oblast co nejblíže ke zdroji dat v místním prostředí. 
   Vaše *prostředku brány v Azure* a aplikaci logiky mohou mít jiná umístění.

8. Pokud chcete přijmout výchozí oblasti, zvolte **konfigurovat**. Nebo, pokud chcete změnit výchozí oblasti, postupujte podle těchto kroků:

   1. Vedle aktuální oblast a vyberte **změnit oblast**. 

      ![Změnit oblast](./media/logic-apps-gateway-install/change-region.png)

   2. Na další stránku, otevřete **vyberte oblast** seznamu, vyberte oblast a tlačítko **provádí**.

      ![Vyberte jiné oblasti](./media/logic-apps-gateway-install/select-region-gateway-install.png)

9. Jakmile se objeví na stránce potvrzení vyberte **Zavřít**. 

   Instalační program potvrdí, že brána je nyní online a připravená k použití.

   ![Dokončení brány](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

10. Teď mohli bránu registrovat v Azure pomocí [vytvoření prostředku Azure pro vaši instalaci brány](../logic-apps/logic-apps-gateway-connection.md). 

## <a name="enable-high-availability"></a>Povolit vysokou dostupnost

Místní brána dat podporuje vysokou dostupnost, když máte více než jedna instalace brány a nastavit je jako clustery. Pokud máte existující bránu, když přejdete na jinou bránu vytvořit, můžete volitelně vytvořit clustery s vysokou dostupností. Tyto clustery bran uspořádejte do skupin, které vám mohou pomoci Vyhýbejte se jediným bodů selhání. Chcete-li tuto funkci použít, zkontrolujte tyto požadavky a důležité informace:

* Pouze některé konektory podporují vysokou dostupnost, jako je například konektoru systému souborů a další se připravují. 
     
* Již musí mít alespoň jednu bránu instalace ve stejném předplatném Azure jako primární bráně a obnovovací klíč pro instalaci. 

* Primární brány musí být spuštěná aktualizace brány z listopadu 2017 nebo novější.

Po splnění těchto požadavků, když vytváříte další bránu, vyberte **přidat do existujícího clusteru bran**, vyberte na primární bráně clusteru a zadat obnovovací klíč pro tuto primární bránu.
Další informace najdete v tématu [clustery s vysokou dostupností pro místní bránu dat](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters).

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Změnit umístění, migrovat, obnovit nebo převzít existující bránu

Pokud musíte změnit umístění vaší brány, přesunutí instalace brány do nového počítače, zotavení poškozené brány nebo převzít vlastnictví pro existující bránu, je třeba obnovovací klíč, který jste zadali během instalace brány. Tato akce odpojí staré brány.

1. Z vašeho počítače **ovládací panely**, přejděte na stránku **programy a funkce**. V seznamu programů vyberte **On-premises data gateway**a klikněte na tlačítko **odinstalovat**.

2. [Znovu nainstalujte místní bránu dat](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409).

3. Až instalační program otevře, přihlaste se pomocí stejného pracovního nebo školního účtu, dříve použitým při instalaci brány.

4. Vyberte **migrace, obnovení nebo převzetí existující brány**a klikněte na tlačítko **Další**.

   ![Vyberte "Migrace, obnovení nebo převzetí existující brány"](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

5. V části **dostupné brány** nebo **dostupné clustery brány**, vyberte možnost instalace brány, kterou chcete změnit. Zadejte obnovovací klíč pro instalaci brány. 

   ![Vyberte primární brány](./media/logic-apps-gateway-install/select-existing-gateway.png)

6. Chcete-li změnit oblast, vyberte **změnit oblast** a nové oblasti.

7. Jakmile budete hotovi, zvolte **konfigurovat**.

## <a name="configure-proxy-or-firewall"></a>Konfigurace proxy serveru nebo brány firewall

Místní brána dat vytvoří odchozí připojení k [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). Pokud vaše pracovní prostředí vyžaduje, aby provoz směrován přes proxy server pro přístup k Internetu, toto omezení může bráně dat bránit v připojení ke cloudové službě brány. Pokud chcete zjistit, zda vaše síť používá proxy server, přečtěte si tento článek na webu superuser.com: 

[Jak zjistím, jaké proxy serveru používám? (Webu SuperUser.com)](https://superuser.com/questions/346372/how-do-i-know-what-proxy-server-im-using) 

Zadejte informace o proxy serveru pro bránu, najdete v článku [konfigurace nastavení proxy serveru](https://docs.microsoft.com/power-bi/service-gateway-proxy). Chcete-li zkontrolovat, zda proxy serveru nebo brány firewall může blokovat připojení, zkontrolujte, jestli váš počítač může ve skutečnosti připojení k Internetu a [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). Z příkazového řádku Powershellu spusťte tento příkaz:

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

> [!NOTE]
> Tento příkaz testuje pouze připojení k síti a připojení ke službě Azure Service Bus. Příkaz nic nedělá se brána nebo brána cloudovou službu a která zašifruje a uloží přihlašovací údaje a podrobnosti brány. 
>
> Také, tento příkaz je pouze k dispozici v systému Windows Server 2012 R2 nebo novější a Windows 8.1 nebo novější. V dřívějších verzích operačních systémů můžete použít protokol Telnet k testování připojení. Další informace o [Azure Service Bus a hybridní řešení](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

Vaše výsledky by měly vypadat podobně jako tento příklad s **TcpTestSucceeded** nastavena na **True**:

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

Pokud **TcpTestSucceeded** není nastavená na **True**, brána může být blokovaný bránou firewall. Pokud chcete být vyčerpávající, nahraďte **ComputerName** a **Port** hodnoty jsou hodnoty uvedené v části [konfigurace portů](#configure-ports) v tomto článku.

Brána firewall může také blokovat připojení, která provádí Azure Service Bus k datovým centrům Azure. Pokud k této situaci dojde, schválit (odblokovat) všechny IP adresy pro tato datová centra ve vaší oblasti. Pro tyto IP adresy [získat seznam adres IP adres Azure zde](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="configure-ports"></a>Konfigurace portů

Brána vytvoří odchozí připojení k [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) a komunikuje na odchozích portech: TCP 443 (výchozí), 5671, 5672, 9350 až 9354. Brána nevyžaduje příchozí porty. Další informace o [Azure Service Bus a hybridní řešení](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

Brána používá tyto plně kvalifikované názvy domény:

| Názvy domén | Odchozí porty | Popis | 
| ------------ | -------------- | ----------- | 
| *.analysis.windows.net | 443 | HTTPS | 
| *.core.windows.net | 443 | HTTPS | 
| *.frontend.clouddatahub.net | 443 | HTTPS | 
| *.login.windows.net | 443 | HTTPS | 
| *.microsoftonline-p.com | 443 | Slouží k ověření v závislosti na konfiguraci. | 
| *.msftncsi.com | 443 | Použít k testování připojení k Internetu, pokud brána není dostupná ve službě Power BI. | 
| *.servicebus.windows.net | 443, 9350-9354 | Naslouchací procesy u služby Service Bus Relay přes TCP (vyžaduje port 443 pro získání tokenu služby Access Control) | 
| *.servicebus.windows.net | 5671-5672 | Pokročilé řízení front zpráv (AMQP) protokolu | 
| login.microsoftonline.com | 443 | HTTPS | 
||||

V některých případech probíhají připojení k Azure Service Bus pomocí IP adresy místo plně kvalifikovaných názvů domény. Ano můžete do seznamu povolených IP adres pro vaši oblast v bráně firewall. Na seznamu povolených IP adres místo domény, můžete stáhnout a použít [seznamu rozsahů IP adres Datacentra Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). IP adresy v tomto seznamu jsou v [notace CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) zápis.

### <a name="force-https-communication-with-azure-service-bus"></a>Vynutit komunikaci přes protokol HTTPS s Azure Service Bus

Některé proxy servery povolit provoz pouze na portech 80 a 443. Ve výchozím nastavení probíhá komunikace se službou Azure Service Bus na jiné porty než 443.
Můžete přinutit bránu komunikovat s Azure Service Bus přes protokol HTTPS, nikoli TCP s přímým přístupem, ale to uděláte tak může výrazně snížit i výkon. Pokud chcete provést tuto úlohu, postupujte takto:

1. Přejděte do umístění pro místní data brány klienta, který obvykle najdete tady: ```C:\Program Files\On-premises data gateway\Microsoft.PowerBI.EnterpriseGateway.exe```

   V opačném případě se najít umístění klienta, otevřete konzolu služby ve stejném počítači, Najít **On-premises data gateway služby**a zobrazit **cesta ke spustitelnému souboru** vlastnost.

2. Tím otevřete *konfigurace* souboru: **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

3. Změnit **ServiceBusSystemConnectivityModeString** hodnotu **automatické rozpoznávání** k **Https**:

   ```html
   <setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
      <value>Https</value>
   </setting>
   ```

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Účet služby Windows

Místní brána dat je možné spustit jako služby Windows s názvem "On-premises data gateway service", ale používá "NT SERVICE\PBIEgwService" pro jeho "Přihlásit se jako" přihlašovací údaje účtu. Místní brána dat ve výchozím nastavení, má oprávnění "Přihlásit jako službu" pro počítač, ve kterém bránu instalujete. K vytváření a údržbě brány na webu Azure Portal, účet služby Windows musí mít alespoň **Přispěvatel** oprávnění. 

> [!NOTE]
> Účet služby Windows se liší od účet používaný pro připojení k místním zdrojům dat a z pracovní nebo školní účet použitý k přihlášení do cloudových služeb.

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Restartování brány

Brána dat běží jako služba okna, proto stejně jako jakoukoli jinou službu Windows můžete spustit a zastavit brána několika různými způsoby. Můžete například otevřít příkazový řádek se zvýšenými oprávněními na počítači, ve kterém je brána spuštěná a spusťte buď příkaz:

* Pokud chcete bránu zastavit, spusťte tento příkaz:
  
  `net stop PBIEgwService`

* Chcete-li spustit službu, spusťte tento příkaz:
  
  `net start PBIEgwService`

## <a name="tenant-level-administration"></a>Správa na úrovni tenantů 

V současné době neexistuje žádné jedno místo, kde můžou správci tenantů spravovat všechny brány, které nainstalovali a nakonfigurovali ostatní uživatelé. Pokud jste správcem tenanta, můžete chtít mít uživatelé ve vaší organizaci vás přidal jako správce pro každou bránu, kterou nainstalují. Tímto způsobem můžete spravovat všechny brány ve vaší organizaci prostřednictvím stránky nastavení brány nebo prostřednictvím [příkazy prostředí PowerShell](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters#powershell-support-for-gateway-clusters). 

<a name="gateway-cloud-service"></a>

## <a name="how-does-the-gateway-work"></a>Jak brána funguje?

Brána dat usnadňuje rychlé a zabezpečené komunikace mezi svou aplikaci logiky, cloudové službě brány a zdroje dat místní. Cloudová služba brány šifruje a ukládá přihlašovací údaje ke zdroji dat a podrobnosti brány. Služba také směrovat dotazy a jejich výsledky mezi svou aplikaci logiky, místní bránu dat a zdroje dat v místním prostředí. 

Brána funguje s branami firewall a používá jenom odchozí připojení. Veškerý provoz pochází jako zabezpečené odchozího provozu z agenta brány. Brána přenášet data z místních zdrojů přes šifrované kanálů prostřednictvím služby Azure Service Bus. Této služby Service bus vytvoří kanál mezi bránou a volání služeb, ale nejsou uloženy v žádná data. Se šifrují všechna data, která se přenáší prostřednictvím brány.

![diagram-for-on-premises-data-gateway-flow](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

Tyto kroky popisují, co se stane, když uživatel v cloudu komunikuje s elementem, který je připojený ke zdroji dat v místním:

1. Cloudová služba brány dotaz, společně se zašifrovanými přihlašovacími údaji pro zdroj dat, vytvoří a odešle dotaz do fronty pro zpracování branou.

2. Cloudová služba brány dotaz analyzuje a pošle žádost do služby Azure Service Bus.

3. Místní brána dat dotazuje Azure Service Bus na čekající žádosti.

4. Brána získá dotaz, dešifruje přihlašovací údaje a připojí se ke zdroji dat pomocí těchto přihlašovacích údajů.

5. Brána odešle dotaz na zdroj dat pro spuštění.

6. Výsledky se odesílají ze zdroje dat zpět do brány a ke cloudové službě brány. Cloudová služba brány potom výsledky použije.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="general"></a>Obecné

**Q**: Potřebuji bránu pro zdroje dat v cloudu, jako je Azure SQL Database? <br/>
**A**: Ne, brána se připojí k místním zdrojům dat pouze.

**Q**: Brána musí být nainstalovaný na stejném počítači jako zdroj dat? <br/>
**A**: Ne, se brána připojuje ke zdroji dat s použitím informací o zadané připojení. Vezměte v úvahu brány jako klientskou aplikaci v tomto smyslu. Brána musí pouze připojení k názvu serveru, který byl poskytnut.

<a name="why-azure-work-school-account"></a>

**Q**: Proč musí můžu použít pracovní nebo školní účet pro přihlášení? <br/>
**A**: pracovní nebo školní účet můžete použít jenom při instalaci na místní bránu dat. Váš účet přihlášení je uložený v tenantovi, který je spravovaný službou Azure Active Directory (Azure AD). Obvykle váš účet Azure AD hlavní název uživatele (UPN) odpovídá e-mailovou adresu.

**Q**: kde jsou uloženy Moje přihlašovací údaje? <br/>
**A**: přihlašovací údaje, které zadáte pro zdroj dat jsou zašifrované a uložené v cloudové službě brány. Přihlašovací údaje se dešifrují na místní data brány.

**Q**: existují nějaké požadavky na šířku pásma sítě? <br/>
**A**: Zkontrolujte, zda dobrou propustnost připojení k síti. Každé prostředí je jiné a množství dat odesílaných může mít vliv na výsledky. Pokud chcete zajistit úroveň propustnosti mezi místní zdroje dat a datacentry Azure, zkuste [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/). Chcete-li odhadnout propustnost, zkuste externího nástroje, jako je Azure Speed Test.

**Q**: Jaká je latence pro spouštění dotazů na zdroj dat z brány? Co je nejvhodnější architektura? <br/>
**A**: Pokud chcete snížit latenci sítě, nainstalujte bránu co nejblíže zdroji dat co nejvíce. Pokud bránu můžete nainstalovat na skutečný zdroj dat, toto blízkosti minimalizuje uvedenou latenci. Zvažte také vzdálenost k datovým centrům Azure. Například pokud vaše služba používá datové centrum západ USA a SQL Server je hostovaný na Virtuálním počítači Azure, pak můžete virtuální počítače Azure v oblasti USA – západ příliš. Tato blízkosti minimalizuje latenci a vyhnete poplatky za výchozí přenos dat na virtuálním počítači Azure.

**Q**: jak se výsledky odesílají zpět do cloudu? <br/>
**A**: výsledky se posílají přes Azure Service Bus.

**Q**: existují nějaká příchozí připojení k bráně z cloudu? <br/>
**A**: Ne, brána používá odchozí připojení ke službě Azure Service Bus.

**Q**: Co když blokuji odchozí připojení? Co je potřeba otevřít? <br/>
**A**: Podívejte se, portů a hostitelů, které brána používá.

**Q**: co je aktuální služby Windows volat? <br/>
**A**: na kartě služeb v programu Správce úloh, název služby je "PBIEgwService", nebo služba brány Enterprise Power BI. V konzole služby název služby je "On-premises data gateway service". Služba Windows používá "NT SERVICE\PBIEgwService" jako identifikátor SID služby (SSID).

**Q**: Služba Windows brány můžete spustit pomocí účtu služby Azure Active Directory? <br/>
**A**: Ne, služba Windows musí mít platný účet Windows.

### <a name="disaster-recovery"></a>Zotavení po havárii

**Q**: jaké možnosti jsou dostupné pro zotavení po havárii? <br/>
**A**: obnovovací klíč můžete použít k obnovení nebo přesunutí brány. Při instalaci brány zadejte obnovovací klíč.

**Q**: co je výhodou obnovovacího klíče? <br/>
**A**: obnovovací klíč poskytuje způsob, jak migrovat nebo obnovit nastavení brány po havárii.

## <a name="troubleshooting"></a>Řešení potíží

Tento oddíl popisuje některé běžné problémy, které mohou nastat při nastavování a pomocí místní brány dat.

**Q**: Proč můj instalace brány nezdaří? <br/>
**A**: Tento problém může stát, když antivirový software v cílovém počítači je zastaralá. Můžete buď aktualizovat antivirový software nebo zakažte antivirový software, ale pouze během instalace brány a potom znovu povolit softwaru.

**Q**: Proč nevidím instalaci brány při vytváření prostředku brány v Azure? <br/>
**A**: Tento problém může dojít z těchto důvodů:

* Instalace brány je už zaregistrované a převzatá podle jiného prostředku brány v Azure. Instalace brány nejsou zobrazeny v seznamu instance vytvořené prostředky brány pro ně.
Zkontrolujte registraci brány na webu Azure Portal, najdete v tématu všech vašich prostředků Azure s **On-premises Data Gateway** zadejte *všechny* předplatných Azure. 

* Identity Azure AD pro osobu, která je nainstalovaná brána se liší od osoby, která přihlášení k webu Azure portal. Zkontrolujte, že jste přihlášení se stejnou identitou, která instalaci brány.

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

**Q**: kde se nacházejí protokoly brány? <br/>
**A**: najdete v článku [ **protokoly** části](#logs) dále v tomto článku.

**Q**: Jak můžu zjistit, jaké dotazy se posílají do zdroje dat v místním? <br/>
**A**: můžete povolit trasování dotazů, které obsahuje dotazy, které se odesílají. Nezapomeňte změnit dotaz trasování zpět na původní hodnotu po dokončení odstraňování potíží. Trasování dotazů zapnuté vytvoří větší protokoly.

Můžete se také podívat na nástroje, které váš zdroj dat používá pro trasování dotazů. Například můžete použít Extended Events nebo SQL Profiler pro SQL Server a služby Analysis Services.

### <a name="outdated-gateway-version"></a>Verze brány zastaralá

Řadě problémů může docházet, když se stane verze brány zastaralá. Jako obecné vhodné Ujistěte se, že máte nejnovější verzi. Pokud bránu neaktualizovali měsíc nebo i delší dobu, může být zvažte instalaci nejnovější verzi brány a zobrazit, pokud je problém možné reprodukovat.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Chyba: Nepovedlo se přidat uživatele do skupiny. (-2147463168 PBIEgwService Performance Log Users)

Tato chyba může zobrazit, pokud se pokusíte nainstalovat bránu na řadiči domény, což není podporováno. Ujistěte se, že se že nasazení brány na počítači, který není řadičem domény.

<a name="logs"></a>

### <a name="logs"></a>Logs

Pomocný při řešení problémů, vždy začněte díky shromažďování a kontroly protokolů brány. Existuje několik způsobů pro shromažďování protokolů, ale je nejjednodušší možnost po instalaci brány pomocí uživatelského rozhraní Instalační program brány. 

1. V počítači spusťte instalační program brány místní data.
2. V nabídce vlevo vyberte **diagnostiky**.
3. V části **protokoly brány**vyberte **exportovat protokoly**.

   ![Exportovat protokoly z instalační program brány](./media/logic-apps-gateway-install/export-logs.png)

Tady jsou další lokality místo, kde najdete různé protokoly:

| Typ protokolu | Umístění | 
|----------|----------| 
| **Instalační protokoly** | %localappdata%\Temp\On-premises_data_gateway_ <*RRRRMMDD*>. <*číslo*> .log | 
| **Konfigurační protokoly** | C:\Users\<*uživatelské jméno*> \AppData\Local\Microsoft\On-premises data gateway\GatewayConfigurator <*RRRRMMDD*>. <*číslo*>. protokol | 
| **Protokoly služby brány Enterprise** | C:\Users\PBIEgwService\AppData\Local\Microsoft\On-Premises data gateway\Gateway <*RRRRMMDD*>. <*číslo*> .log | 
||| 

**Protokoly událostí**

Najít protokoly událostí pro bránu, postupujte podle těchto kroků:

1. Na počítači s instalací brány otevřete **Prohlížeč událostí**. 
2. Rozbalte **Prohlížeč událostí (místní)** > **protokoly aplikací a služeb**. 
3. Vyberte **On-premises data gateway služby**.

   ![Zobrazení protokolu událostí pro bránu](./media/logic-apps-gateway-install/event-viewer.png)

### <a name="telemetry"></a>Telemetrická data

Pro další funkce monitorování a řešení potíží můžete zapnout a shromažďovat telemetrii. 

1. Přejděte do umístění pro místní data brány klienta, který obvykle najdete tady: ```C:\Program Files\On-premises data gateway```

   V opačném případě se najít umístění klienta, otevřete konzolu služby ve stejném počítači, Najít **On-premises data gateway služby**a zobrazit **cesta ke spustitelnému souboru** vlastnost.

2. Tím otevřete *konfigurace* souboru: **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

3. Změnit **SendTelemetry** hodnota, která se **true**:

   ```html
   <setting name="SendTelemetry" serializeAs="String">
      <value>true</value>
   </setting>
   ```

4. Uložte změny a potom restartujte službu Windows.

### <a name="review-slow-query-performance"></a>Zkontrolovat výkon pomalých dotazů

Pokud zjistíte dotazy pomalý prostřednictvím brány, můžete zapnout dodatečné protokolování, jejichž výstupem jsou dotazy a jejich doby trvání. Tyto protokoly může pomoci najít, které dotazy jsou pomalé nebo dlouho probíhající. Pro ladění výkonu dotazů, bude pravděpodobně třeba upravit zdroj dat, upravte indexů pro dotazy systému SQL Server.

Pokud chcete určit dobu trvání dotazu, postupujte takto:

1. Přejděte do stejného umístění jako brána klienta, které obvykle najdete tady: ```C:\Program Files\On-premises data gateway```

   V opačném případě se najít umístění klienta, otevřete konzolu služby ve stejném počítači, Najít **On-premises data gateway služby**a zobrazit **cesta ke spustitelnému souboru** vlastnost.

2. Otevřít a upravit tyto konfigurační soubory, jak je popsáno:

   * **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

     V tomto souboru změnit **hodnotu EmitQueryTraces** hodnotu **false** k **true** tak vaše brána lze protokolovat dotazy odeslané z brány ke zdroji dat:

     ```html
     <setting name="EmitQueryTraces" serializeAs="String">
        <value>true</value>
     </setting>
     ```

     > [!IMPORTANT]
     > Zapnutí nastavení funkce EmitQueryTraces může významně zvětšit velikost protokolu závislosti na využití brány. Po dokončení kontroly protokolů hesla nezapomeňte nastavit hodnotu EmitQueryTraces na **false** znovu, spíše než toto políčko nechat v dlouhodobém horizontu.

   * **Microsoft.PowerBI.DataMovement.Pipeline.Diagnostics.dll.config**

     Chcete-li mít bránu zaznamenávat položky, včetně položek, které uvádí doba trvání, změňte **TracingVerbosity** hodnotu z **4** k **5** provedením buď krok: 

     * V tomto konfiguračním souboru změnit **TracingVerbosity** hodnotu **4** k **5** 

       ```html
       <setting name="TracingVerbosity" serializeAs="String">
          <value>5</value>
       </setting>
       ```

     * Spusťte instalační program brány, vyberte **diagnostiky**, zapněte **dodatečné protokolování**a klikněte na tlačítko **použít**:

       ![Zapnout dodatečné protokolování](./media/logic-apps-gateway-install/turn-on-additional-logging.png)

     > [!IMPORTANT]
     > Zapnutí nastavení TracingVerbosity může významně zvětšit velikost protokolu závislosti na využití brány. Po dokončení kontroly protokolů, ujistěte se, že vypnete **dodatečné protokolování** v instalační program brány nebo obnovit TracingVerbosity na **4** znovu v konfiguračním souboru, nikoli nechte u tohoto nastavení na dlouhodobém horizontu.

3. K vyhledání doba trvání dotazu, postupujte takto:

   1. [Export](#logs) a otevřete protokol brány.

   2. Najít dotaz, vyhledáte typ aktivity, například: 

      | Typ aktivity | Popis | 
      |---------------|-------------| 
      | MGEQ | Dotazy spouštěné přes ADO.NET. | 
      | MGEO | Dotazy spouštěné přes OLEDB. | 
      | MGEM | Dotazy spouštěné z modulu Mashup. | 
      ||| 

   3. Všimněte si druhého identifikátoru GUID, což je žádost o ID.

   4. Pokračujte ve vyhledávání pro typ aktivity, dokud nenajdete položku s názvem "Fireactivitycompletedsuccessfullyevent, která obsahuje", který má dobu trvání v milisekundách. 
   Potvrďte, že položka má stejné ID žádosti, například:

      ```text 
      DM.EnterpriseGateway Verbose: 0 : 2016-09-26T23:08:56.7940067Z DM.EnterpriseGateway    baf40f21-2eb4-4af1-9c59-0950ef11ec4a    5f99f566-106d-c8ac-c864-c0808c41a606    MGEQ    21f96cc4-7496-bfdd-748c-b4915cb4b70c    B8DFCF12 [DM.Pipeline.Common.TracingTelemetryService] Event: FireActivityCompletedSuccessfullyEvent (duration=5004)
      ```

      > [!NOTE] 
      > Položka "FireActivityCompletedSuccessfullyEvent" je položka s komentářem a není přihlášen, není-li "TracingVerbosity" nastavení na úrovni 5.

### <a name="trace-traffic-with-fiddler"></a>Přenos trasování pomocí Fiddleru

[Fiddler](http://www.telerik.com/fiddler) je bezplatný nástroj od společnosti Telerik, který sleduje provoz HTTP. Můžete zkontrolovat tento provoz se službou Power BI z klientského počítače. Tato služba může zobrazovat chyby a jiné související informace.

## <a name="next-steps"></a>Další postup
    
* [Připojení k místním datům z aplikace logiky](../logic-apps/logic-apps-gateway-connection.md)
* [Funkce integrace Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Konektory pro Azure Logic Apps](../connectors/apis-list.md)
