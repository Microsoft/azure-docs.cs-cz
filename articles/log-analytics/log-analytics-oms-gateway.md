---
title: Připojte počítače pomocí brány OMS | Microsoft Docs
description: Připojení zařízení a nástroje Operations Manager monitorované počítače s bránou OMS k odesílání dat do Azure Automation a analýzy protokolů služby, když nemají přístup k Internetu.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: ecbc88ebaaa93215f85b57becc8a643dc3e168a0
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129036"
---
# <a name="connect-computers-without-internet-access-using-the-oms-gateway"></a>Připojte počítače bez přístupu k Internetu pomocí brány OMS
Tento dokument popisuje, jak nakonfigurovat komunikaci s Azure Automation a analýzy protokolů pomocí brány OMS při přímé připojení nebo Operations Manager monitorované počítače nemají přístup k Internetu.  Bránu OMS, což je předat dál proxy protokolu HTTP, podporující tunelování HTTP pomocí příkazu HTTP připojení, můžete shromažďovat data a odeslat do Azure Automation a analýzy protokolů jejich jménem.  

Podporuje OMS brána:

* Procesy služby Azure Automation Hybrid Runbook Worker  
* Počítače se systémem Windows pomocí Microsoft Monitoring Agent přímo připojené k pracovní prostor analýzy protokolů
* Počítače se systémem Linux s agentem OMS pro Linux přímo připojené k pracovní prostor analýzy protokolů  
* System Center Operations Manager 2012 SP1 s kumulativní aktualizací 7 nebo Operations Manager 2012 R2 UR3, Operations Manager 2016 a skupiny pro správu nástroje Operations Manager verze 1801 integrovat analýzy protokolů.  

Pokud vaše zásady zabezpečení IT neumožňují počítačů ve vaší síti pro připojení k Internetu, jako je například bod zařízení POS (POS) nebo serverech podporujících IT služeb, ale je potřeba jejich připojení k Azure Automation nebo analýzy protokolů, spravovat a monitorovat je , lze nastavit komunikovat přímo s bránou OMS přijímat konfigurace a předávání dat jejich jménem.  Pokud jsou tyto počítače nakonfigurované s agentem OMS k přímému připojení k pracovní prostor analýzy protokolů, všechny počítače se místo toho komunikovat s branou OMS.  Brána přenosu dat z agentů do služby přímo, nebudou analyzované žádné dat během přenosu.

Pokud skupiny pro správu nástroje Operations Manager je spojen s analýzy protokolů, možné nakonfigurovat servery pro správu pro připojení k bráně OMS shromážděná data v závislosti na řešení, které jste povolili odesílat a přijímat informace o konfiguraci.  Agenti nástroje Operations Manager odeslat některá data, například výstrahy nástroje Operations Manager, konfigurace hodnocení, prostoru instancí a kapacity dat k serveru pro správu. Další velkých objemů dat, například protokoly služby IIS, výkonu a události zabezpečení jsou odesílány přímo k bráně OMS.  Pokud máte jeden nebo více serverů brány nástroje Operations Manager, které jsou nasazené v hraniční sítě nebo jiné izolovanou síť k monitorování nedůvěryhodné systémů, nemůže komunikovat s bránu OMS.  Servery nástroje Operations Manager brány lze pouze sestavy k serveru pro správu.  Pokud skupinu správy nástroje Operations Manager je nakonfigurován pro komunikaci s bránou OMS, informace o konfiguraci proxy serveru je automaticky distribuován do každý počítač spravovaný agentem, nakonfigurovaný tak, aby shromažďování dat pro analýzy protokolů, i když nastavení je prázdný.    

Pro zajištění vysoké dostupnosti pro přímé připojení nebo Operations Management skupin, které komunikují s analýzy protokolů přes bránu, můžete použít vyrovnávání zatížení sítě k přesměrování a distribuce komunikace mezi několik serverů brány.  Pokud jeden server brány ocitne mimo provoz, provoz se přesměruje na jiný uzel k dispozici.  

OMS agent je nutný v počítači se systémem bránu OMS byl k identifikaci koncovým bodům služby, které potřebuje ke komunikaci s a monitorovat bránu OMS k analýze jeho výkonu nebo data události.

Každý agent, musí mít síťové připojení k jeho brány tak, aby agenty lze automaticky provádět přenos dat do a z brány. Instalace brány na řadiči domény se nedoporučuje.

Následující diagram znázorňuje tok dat od přímé agentů do Azure Automation a analýzy protokolů pomocí serveru brány.  Agenti musí mít jejich konfiguraci proxy serveru, které odpovídají stejný port, který OMS brány je nakonfigurován pro komunikaci ve službě.  

![agent přímé komunikaci s diagram služby](./media/log-analytics-oms-gateway/oms-omsgateway-agentdirectconnect.png)

Následující diagram znázorňuje tok dat ze skupiny pro správu nástroje Operations Manager k analýze protokolů.   

![Operations Manager komunikace s diagram analýzy protokolů](./media/log-analytics-oms-gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="prerequisites"></a>Požadavky

Při určování počítač spustit bránu OMS, tento počítač musí mít následující:

* Windows 10, Windows 8.1, Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2,  Windows Server 2008
* Rozhraní .net framework 4.5
* Minimálně 4 jádra procesoru a 8 GB paměti 
* OMS agenta pro Windows 

### <a name="language-availability"></a>Jazyk dostupnosti

Bránu OMS je k dispozici v těchto jazycích:

- Čínština (zjednodušená)
- Čínština (tradiční)
- Čeština
- Holandština
- Angličtina
- Francouzština
- Němčina
- Maďarština
- italština
- Japonština
- Korejština
- polština
- Portugalština (Brazílie)
- Portugalština (Portugalsko)
- ruština
- Španělština (mezinárodní)

### <a name="supported-encryption-protocols"></a>Šifrování podporovaných protokolů
Bránu OMS podporuje pouze zabezpečení TLS (Transport Layer) 1.0, 1.1 a 1.2.  Nepodporuje Secure Sockets Layer (SSL).

### <a name="supported-number-of-agent-connections"></a>Podporované počet připojení agenta
V následující tabulce označuje podporovaný počet agentů komunikaci se serverem brány.  Tato podpora je založena na agentech odesílání ~ 200KB dat každých 6 sekund. Datový svazek na jednoho agenta testována je přibližně 2.7GB za den.

|brána |Asi počet agentů podporováno|  
|--------|----------------------------------|  
|-Procesoru: Intel XEON E5 procesoru 2660 v3 @ 2.6GHz 2 jádra<br> -Paměť: 4 GB<br> -Šířku pásma sítě: 1 GB/s| 600|  
|-Procesoru: Intel XEON E5 procesoru 2660 v3 @ 2.6GHz, 4 jádra<br> -Paměť: 8 GB<br> -Šířku pásma sítě: 1 GB/s| 1000|  

## <a name="download-the-oms-gateway"></a>Stáhněte bránu OMS

Existují dva způsoby, jak získat nejnovější verzi OMS brány instalační soubor.

1. Stáhnout z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=54443).

2. Stáhněte si z portálu Azure.  Po přihlášení k portálu Azure:  

   1. Procházet seznam služeb a pak vyberte **analýzy protokolů**.  
   2. Vyberte pracovní prostor.
   3. V okně prostoru v části **Obecné**, klikněte na tlačítko **rychlý Start**.
   4. V části **zvolit zdroj dat pro připojení do pracovního prostoru**, klikněte na tlačítko **počítače**.
   5. V **přímé agenta** okně klikněte na tlačítko **stáhnout brány OMS**.<br><br> ![Stáhněte si OMS brány](./media/log-analytics-oms-gateway/download-gateway.png)

nebo 

   1. V okně prostoru v části **nastavení**, klikněte na tlačítko **upřesňující nastavení**.
   2. Přejděte na **připojené zdroje** > **servery Windows** a klikněte na tlačítko **stáhnout brány OMS**.

## <a name="install-the-oms-gateway"></a>Nainstalujte bránu OMS

Pokud chcete nainstalovat bránu, proveďte následující kroky.  Pokud jste nainstalovali předchozí verze, dříve se označovaly jako *Log Analytics předávání*, se upgraduje na tuto verzi.  

1. V cílové složce poklikejte na **OMS Gateway.msi**.
2. Na **úvodní** stránce klikněte na **Další**.<br><br> ![Průvodce instalací brány](./media/log-analytics-oms-gateway/gateway-wizard01.png)<br> 
3. Na **licenční smlouvy** vyberte **s podmínkami licenční smlouvy souhlasím** souhlas s se smlouvou EULA, a potom klikněte na **Další**.
4. Na **Port a proxy adres** stránky:
   1. Zadejte číslo portu TCP, který se má použít pro bránu. Instalační program nakonfiguruje příchozí pravidlo s tímto číslem portu v bráně Windows firewall.  Výchozí hodnota je 8080.
      Platný rozsah číslo portu je 1 – 65 535. Pokud vstup do tohoto rozsahu nespadá, zobrazí se chybová zpráva.
   2. Případně pokud serveru s nainstalovanou bránu musí komunikovat prostřednictvím proxy serveru, zadejte adresu proxy serveru, kde je potřeba připojit bránu. Například, `http://myorgname.corp.contoso.com:80`.  Pokud je pole prázdné, brány se pokusí připojit přímo k Internetu.  Pokud proxy server vyžaduje ověřování, zadejte uživatelské jméno a heslo.<br><br> ![Konfigurace proxy serveru brány Průvodce](./media/log-analytics-oms-gateway/gateway-wizard02.png)<br>   
   3. Klikněte na **Další**.
5. Pokud nemáte povolenu službu Microsoft Update, zobrazí se stránky Microsoft Update, kde můžete povolit. Proveďte výběr a potom klikněte na **Další**. V opačném pokračujte k dalšímu kroku.
6. Na **cílovou složku** stránky, buď ponechejte výchozí složka C:\Program Files\OMS brány, nebo zadejte umístění, kam chcete bránu nainstalovat a potom klikněte na **Další**.
7. Na **připravené k instalaci** klikněte na tlačítko **nainstalovat**. Řízení uživatelských účtů, může se objevit požadavku na oprávnění k instalaci. Pokud ano, klikněte na tlačítko **Ano**.
8. Po dokončení instalace klikněte na tlačítko **Dokončit**. Můžete ověřit, že je služba je spuštěna tak, že otevřete modul snap-in services.msc a ověřte, zda **OMS brány** se zobrazí v seznamu služeb a jeho stav se **systémem**.<br><br> ![Services – OMS brány](./media/log-analytics-oms-gateway/gateway-service.png)  

## <a name="configure-network-load-balancing"></a>Konfigurace služby Vyrovnávání zatížení sítě 
Můžete nakonfigurovat bránu pro vysokou dostupnost, pomocí služby Vyrovnávání zatížení sítě (NLB) pomocí Microsoft Network Load Balancing (NLB) nebo nástroje pro vyrovnávání zatížení založené na hardwaru.  Nástroje pro vyrovnávání zatížení spravuje přenosy přesměrováním požadované připojení od OMS agentů nebo serverů pro správu nástroje Operations Manager mezi jeho uzlů. Pokud jeden server brány ocitne mimo provoz, provoz se přesměruje na jiných uzlech.

Další informace o návrhu a nasazení clusteru služby Vyrovnávání zatížení sítě systému Windows Server 2016 najdete v tématu [Vyrovnávání zatížení sítě](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing).  Následující kroky popisují postup konfigurace clusteru služby Vyrovnávání zatížení sítě společnosti Microsoft.  

1. Přihlaste do Windows serveru, který je členem clusteru vyrovnávání zatížení sítě k účtu správce.  
2. Ve Správci serveru otevřete Správce vyrovnávání zatížení sítě, klikněte na tlačítko **nástroje**a potom klikněte na **Správce vyrovnávání zatížení sítě**.
3. Pro připojení serveru brány OMS s nainstalovaným agentem monitorování společnosti Microsoft, klikněte pravým tlačítkem na IP adresu clusteru a pak klikněte na tlačítko **přidat hostitele do clusteru**.<br><br> ![Zatížení sítě vyrovnávání Manager – přidání hostitele do clusteru](./media/log-analytics-oms-gateway/nlb02.png)<br> 
4. Zadejte IP adresu serveru brány, kterému se chcete připojit.<br><br> ![Sítě Správce vyrovnávání zatížení – přidání hostitele do clusteru: připojení](./media/log-analytics-oms-gateway/nlb03.png) 
    
## <a name="configure-oms-agent-and-operations-manager-management-group"></a>Konfigurace agenta OMS a skupiny pro správu nástroje Operations Manager
Následující část obsahuje kroky pro konfiguraci přímo připojené OMS agentů, skupinu pro správu nástroje Operations Manager nebo procesy Azure Automation Hybrid Runbook Worker s bránou OMS komunikovat s Azure Automation nebo analýzy protokolů.  

### <a name="configure-standalone-oms-agent"></a>Konfigurace agenta OMS samostatné
Zjistit požadavky a kroky k instalaci agenta OMS na počítačích s Windows připojení přímo k Log Analytics najdete v části [počítače se systémem Windows se připojit k analýze protokolů](log-analytics-windows-agents.md) nebo Linux počítačů najdete v tématu [připojit Linux počítače k analýze protokolů](log-analytics-quick-collect-linux-computer.md). Místo zadání proxy server při konfiguraci agenta, nahraďte tuto hodnotu IP adresu serveru brány OMS a jeho číslo portu.  Pokud jste nasadili několik serverů brány za službou Vyrovnávání zatížení sítě, je konfigurace proxy serveru agenta OMS virtuální IP adresy služby NLB.  

Informace související s Automation Hybrid Runbook Worker najdete v tématu [nasazení Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-operations-manager---all-agents-use-the-same-proxy-server"></a>Konfigurace nástroje Operations Manager – všechny agenty používat stejný server proxy
Chcete-li přidat server brány nástroje Operations Manager nakonfigurujete.  Konfiguraci proxy serveru nástroje Operations Manager je automaticky použita pro všechny agenty k nástroji Operations Manager reporting, i když je toto nastavení prázdné.  

Pro použití brány na podporu nástroje Operations Manager, musíte mít:

* Microsoft Monitoring Agent (verze agenta – **8.0.10900.0** nebo novější) na serveru brány nainstalovaný a nakonfigurovaný pro pracovních prostorů analýzy protokolů, se kterými chcete komunikovat.
* Brány musí mít připojení k Internetu nebo připojení k proxy serveru, který nemá.

> [!NOTE]
> Pokud nezadáte hodnotu pro bránu, prázdné hodnoty odesílají na všechny agenty.
> 

Pokud je prvním pracovní prostor analýzy protokolů je registrace skupiny pro správu nástroje Operations Manager, můžete zadat konfiguraci proxy serveru pro skupinu pro správu není k dispozici v konzoli Operations console.  Tato možnost bude dostupná až potom, co bude skupina pro správu ve službě úspěšně zaregistrovaná.  Aby bylo možné nakonfigurovat integraci, jakož i všechny servery pro správu ve skupině pro správu, je potřeba v systému, ze kterého spouštíte konzolu Operations Console, aktualizovat systémovou konfiguraci proxy serveru pomocí nástroje Netsh.  

1. Otevřete příkazový řádek se zvýšenými oprávněními.
   a. Přejděte na **spustit** a typ **cmd**.
   b. Klikněte pravým tlačítkem na **příkazového řádku** a vyberte spustit jako správce **.
2. Zadejte následující příkaz a stiskněte **Enter**:

    `netsh winhttp set proxy <proxy>:<port>`

Po dokončení integrace s analýzy protokolů, můžete odebrat spuštěním změnu `netsh winhttp reset proxy` a potom pomocí **konfigurovat proxy server** možnost v konzoli Operations console zadat server brány OMS. 

1. Otevřete konzolu nástroje Operations Manager a v části **Operations Management Suite**, klikněte na tlačítko **připojení** a pak klikněte na **nakonfigurovat Proxy Server**.<br><br> ![Nástroje Operations Manager – nakonfigurujte Proxy Server](./media/log-analytics-oms-gateway/scom01.png)<br> 
2. Vyberte **používat proxy server pro přístup k službě Operations Management Suite** a poté zadejte IP adresu serveru brány OMS nebo virtuální IP adresy služby NLB. Ujistěte se, že začínáte s `http://` předponu.<br><br> ![Nástroj Operations Manager – adresu proxy serveru.](./media/log-analytics-oms-gateway/scom02.png)<br> 
3. Klikněte na **Dokončit**. Skupiny pro správu nástroje Operations Manager je nyní nakonfigurováno pro komunikaci prostřednictvím serveru brány ke službě Analýza protokolů.

### <a name="configure-operations-manager---specific-agents-use-proxy-server"></a>Konfigurace nástroje Operations Manager – konkrétní agenty použít proxy server
V prostředích velká nebo složitá pouze můžete použít server brány OMS konkrétních serverů (nebo skupiny).  Pro tyto servery nelze aktualizovat agenta nástroje Operations Manager přímo, protože tato hodnota se přepíše globální hodnotou pro skupinu pro správu.  Místo toho je třeba přepsat pravidlo použít tyto hodnoty.  

> [!NOTE] 
> Tento stejný postup konfigurace lze použít k povolení použití více serverů brány OMS ve vašem prostředí.  Může například vyžadovat konkrétní servery brány OMS ho zadat na základě podle oblasti.
>  

1. Otevřete konzolu nástroje Operations Manager a vyberte **vytváření** pracovního prostoru.  
2. V pracovním prostoru vytváření obsahu, vyberte **pravidla** a klikněte na **oboru** tlačítka na panelu nástrojů nástroje Operations Manager. Pokud toto tlačítko není k dispozici, zkontrolujte, že máte vybrán objekt, a nikoliv složka, v podokně monitorování. **Obor objektů sady Management Pack** dialogové okno zobrazí seznam běžných cílové třídy, skupiny nebo objekty. 
3. Typ **služba Health Service** v **vyhledejte** pole a vyberte ho ze seznamu.  Klikněte na **OK**.  
4. Vyhledejte pravidlo **pravidlo nastavení proxy serveru Advisor** a nástrojů kontroly Operations console klikněte na **přepsání** a pak přejděte na **přepsat Rule\For konkrétní objekt třídy: Služba Health Service**  a vybrat konkrétní objekt ze seznamu.  Volitelně můžete vytvořit vlastní skupiny obsahující objektu stavu služby ze serverů, který chcete použít pro toto přepsání a pak použít přepsání do této skupiny.
5. V **vlastnosti přepsání** dialogové okno, kliknutím na toto políčko zaškrtněte v **přepsat** vedle sloupce **WebProxyAddress** parametr.  V **hodnotu přepsání** pole, zadejte adresu URL pro zajištění serveru brány OMS jste začínající `http://` předponu.  

    >[!NOTE]
    > Není nutné k povolení pravidla, jak je již spravován automaticky přepsáním obsažené v sadě Microsoft System Center Advisor zabezpečené referenční Override management pack cílení na Microsoft System Center Advisor monitorování skupiny serverů.
    >   

6. Buď vyberte sadu management pack z **vyberte cílovou sadu management pack** seznamu nebo vytvořte novou sadu management pack kliknutím **nový**. 
7. Po dokončení změny klikněte na tlačítko **OK**. 

### <a name="configure-for-automation-hybrid-workers"></a>Konfigurace pro automatizaci hybridní pracovní procesy
Pokud máte automatizace procesů Hybrid Runbook Worker ve vašem prostředí, zadejte následující kroky ručně, dočasné řešení konfigurace brány, které je podporují.

V následujících krocích musíte znát oblasti Azure, kde je umístěn účet Automation. Vyhledejte umístění:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Vyberte službu Azure Automation.
3. Vyberte příslušný účet Azure Automation.
4. Zobrazit jeho oblasti pod **umístění**.<br><br> ![Portál Azure – umístění účtu Automation.](./media/log-analytics-oms-gateway/location.png)  

Pomocí následující tabulky Identifikujte adresu URL pro každé umístění:

**Úloha adresy URL služby dat za běhu**

| **location** | **Adresa URL** |
| --- | --- |
| Střed USA – sever |ncus-jobruntimedata-prod-su1.azure-automation.net |
| Západní Evropa |we-jobruntimedata-prod-su1.azure-automation.net |
| Střed USA – jih |scus-jobruntimedata-prod-su1.azure-automation.net |
| Východní USA 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| Střední Kanada |cc-jobruntimedata-prod-su1.azure-automation.net |
| Severní Evropa |ne-jobruntimedata-prod-su1.azure-automation.net |
| Jihovýchodní Asie |sea-jobruntimedata-prod-su1.azure-automation.net |
| Střed Indie |cid-jobruntimedata-prod-su1.azure-automation.net |
| Japonsko |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Austrálie |ase-jobruntimedata-prod-su1.azure-automation.net |

**Adresy URL služby agenta**

| **location** | **Adresa URL** |
| --- | --- |
| Střed USA – sever |ncus-agentservice-prod-1.azure-automation.net |
| Západní Evropa |we-agentservice-prod-1.azure-automation.net |
| Střed USA – jih |scus-agentservice-prod-1.azure-automation.net |
| Východní USA 2 |eus2-agentservice-prod-1.azure-automation.net |
| Střední Kanada |cc-agentservice-prod-1.azure-automation.net |
| Severní Evropa |ne-agentservice-prod-1.azure-automation.net |
| Jihovýchodní Asie |sea-agentservice-prod-1.azure-automation.net |
| Střed Indie |cid-agentservice-prod-1.azure-automation.net |
| Japonsko |jpe-agentservice-prod-1.azure-automation.net |
| Austrálie |ase-agentservice-prod-1.azure-automation.net |

Pokud váš počítač je registrován jako hybridní pracovní proces Runbooku automaticky pro opravy pomocí řešení správy aktualizací, postupujte takto:

1. Adresy URL služby modulu Runtime Data úlohy přidáte do seznamu hostitelů povolené na bráně OMS. Příklad: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
2. Restartujte službu OMS brány pomocí následující rutiny prostředí PowerShell: `Restart-Service OMSGatewayService`

Pokud váš počítač je pomocí rutiny registrace hybridní pracovní proces Runbooku na zahrnuté do Azure Automation, postupujte podle těchto kroků:

1. Přidáte adresu URL pro registraci služby agenta do seznamu hostitelů povolené na bráně OMS. Příklad: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
2. Adresy URL služby modulu Runtime Data úlohy přidáte do seznamu hostitelů povolené na bráně OMS. Příklad: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
3. Restartujte službu brány OMS.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Užitečné rutiny prostředí PowerShell
Rutiny můžete dokončit úkoly, které jsou potřeba aktualizovat nastavení konfigurace brány OMS. Předtím, než je použijete, nezapomeňte:

1. Nainstalujte bránu OMS (MSI).
2. Otevřete okno konzoly prostředí PowerShell.
3. Chcete-li importovat modul, zadejte tento příkaz: `Import-Module OMSGateway`
4. Pokud k žádné chybě došlo v předchozím kroku, modul byl úspěšně importován a použít rutiny. Typ `Get-Module OMSGateway`
5. Když provedete změny pomocí rutin, ujistěte se, restartujte službu brány.

Modul nebyl importován, pokud dojde k chybě v kroku 3. K chybě může dojít, když nelze nalézt modul prostředí PowerShell. Najdete ho v instalační cestě pro bránu: *C:\Program Files\Microsoft OMS Gateway\PowerShell\OmsGateway*.

| **Cmdlet** | **Parametry** | **Popis** | **Příklad** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Klíč |Získá konfiguraci této služby |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Klíč (povinné) <br> Hodnota |Změny konfigurace služby |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Získá adresu proxy serveru (nadřazený) předávání |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Adresa<br> Uživatelské jméno<br> Heslo |Nastaví adresy (a přihlašovacích údajů) předávání přes proxy (nadřazený) |1. Nastavení proxy serveru pro předávání a přihlašovací údaje:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Nastavte předávání přes proxy server, který nevyžaduje ověření: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Zrušte zaškrtnutí políčka předávání nastavení proxy serveru:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Získá aktuálně povolené hostitele (pouze místně nakonfigurované povolené hostitele, nezahrnuje automaticky stažené povolené hostitele) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Hostitel (povinné) |Přidá hostitele do seznamu povolených |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Hostitel (povinné) |Odebere hostitele ze seznamu povolených |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Předmět (povinné) |Přidá certifikát klienta může seznam povolených položek |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Předmět (povinné) |Odebere ze seznamu povolených předmětu certifikátu klienta |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Získá aktuálně povolené klienta předměty certifikátu (pouze místně nakonfigurované povolené témata, nezahrnuje povolené témata automaticky stažené) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Řešení potíží
Shromažďovat události zapsané podle bránu, musíte také být nainstalován agent OMS.<br><br> ![Prohlížeč událostí – v protokolu brány OMS](./media/log-analytics-oms-gateway/event-viewer.png)

**ID událostí brány OMS a popisy**

V následující tabulce jsou uvedeny ID událostí a popisy pro OMS brány protokolu událostí.

| **ID** | **Popis** |
| --- | --- |
| 400 |Chyby aplikace, která nemá specifické ID |
| 401 |Chybná konfigurace. Příklad: listenPort = "text" místo celé číslo |
| 402 |Výjimka při analýze TLS handshake zprávy |
| 403 |Došlo k chybě sítě. Příklad: Nelze se připojit k cílovému serveru |
| 100 |Obecné informace |
| 101 |Služba byla spuštěna |
| 102 |Služba byla zastavena |
| 103 |Příkaz připojení protokolu HTTP přijatých od klienta |
| 104 |Není připojení protokolu HTTP příkaz |
| 105 |Cílový server se nenachází v seznamu povolených nebo cílový port není zabezpečený port (443) <br> <br> Zkontrolujte, že agenta MMA na serveru brány a agenty komunikaci s bránou připojeni k ve stejném pracovním prostoru analýzy protokolů. |
| 105 |Chyba TcpConnection – neplatný klientský certifikát: CN = brány <br><br> Zajistěte, aby: <br>    <br> &#149;Používáte bránu s číslem verze 1.0.395.0 nebo vyšší. <br> &#149;Agent MMA na serveru brány a agenty komunikaci s bránou jsou připojené k ve stejném pracovním prostoru analýzy protokolů. |
| 106 |Bránu OMS podporuje jenom TLS 1.0, TLS 1.1 a 1.2.  Nepodporuje SSL. OMS brány pro všechny nepodporovanou verzi protokolu TLS/SSL, vygeneruje událost ID 106.|
| 107 |Relace protokolu TLS byla ověřena. |

**Čítače výkonu ke shromažďování**

V následující tabulce jsou uvedeny dostupných čítačů výkonu pro bránu OMS. Můžete přidat čítače pomocí sledování výkonu.

| **Název** | **Popis** |
| --- | --- |
| Připojení klienta brány/aktivní OMS |Počet připojení active klientské sítě (TCP) |
| Počet brány nebo chyb OMS |Počet chyb |
| OMS brány nebo připojení klienta |Počet připojených klientů |
| Počet brány nebo odmítání OMS |Počet zamítnutí z důvodu chyby ověřování TLS |

![Čítače výkonu OMS brány](./media/log-analytics-oms-gateway/counters.png)

## <a name="get-assistance"></a>Získejte pomoc
Když se přihlásíte na portál Azure, můžete vytvořit žádost o pomoc s bránu OMS nebo jiné služby Azure nebo funkce služby.
Požádat o pomoc, klikněte na symbol otazník v pravém horním rohu portálu a potom klikněte na **nová žádost o podporu**. Dokončete nový formulář žádosti o podporu.

![Nová žádost o podporu](./media/log-analytics-oms-gateway/support.png)

## <a name="next-steps"></a>Další postup
[Přidat zdroje dat](log-analytics-data-sources.md) pro shromažďování dat z připojení zdrojů a uložte ji do pracovního prostoru analýzy protokolů.
