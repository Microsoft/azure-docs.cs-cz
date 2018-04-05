---
title: Připojení počítačů s použitím brány OMS | Microsoft Docs
description: Připojení zařízení a nástroje Operations Manager monitorované počítače s bránou OMS k odesílání dat do Azure Automation a analýzy protokolů služby, když nemají přístup k Internetu.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: magoedte
ms.openlocfilehash: 66e5444f5346a44cfc8a43cf2b43dbaeacffedc9
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="connect-computers-without-internet-access-by-using-the-oms-gateway"></a>Připojte počítače bez připojení k Internetu pomocí brány OMS
Tento dokument popisuje, jak nakonfigurovat komunikaci s Azure Automation a analýzy protokolů pomocí brány OMS při přímo připojené počítače nebo Operations Manager monitorované počítače nemají přístup k Internetu. Brána OMS je předání HTTP proxy server, který podporuje protokol HTTP tunelové propojení pomocí příkazu HTTP připojit. Je-li shromažďovat data a odeslat do Azure Automation a analýzy protokolů jménem počítačů bez připojení k Internetu.  

Podporuje OMS brána:

* Procesy služby Azure Automation Hybrid Runbook Worker  
* Počítače se systémem Windows pomocí Microsoft Monitoring Agent přímo připojené k pracovní prostor analýzy protokolů
* Počítače se systémem Linux s agentem OMS pro Linux přímo připojené k pracovní prostor analýzy protokolů  
* System Center Operations Manager 2012 SP1 s kumulativní aktualizací 7 nebo Operations Manager 2012 R2 UR3, Operations Manager 2016 a skupiny pro správu nástroje Operations Manager verze 1801 integrovat analýzy protokolů  

Pokud vaše zásady zabezpečení IT Nepovolit určitých počítačů ve vaší síti pro připojení k Internetu (třeba zařízení POS nebo servery, které podporují služby IT), ale budete muset jejich připojení k Azure Automation nebo analýzy protokolů, spravovat a monitorovat je, že může být nakonfigurován pro komunikaci přímo s bránou OMS.

 Pokud jsou tyto počítače nakonfigurované s agentem OMS k přímému připojení k pracovní prostor analýzy protokolů, všechny počítače se místo toho komunikovat s branou OMS. Bránu OMS přenosu dat z agentů do služby přímo. Při přenosu ho nebude analyzovat žádná data.

Pokud skupiny pro správu nástroje Operations Manager je spojen s analýzy protokolů, možné nakonfigurovat servery pro správu pro připojení k bráně OMS odeslat shromážděná data a přijímat informace o konfiguraci. Agenti nástroje Operations Manager odeslat některá data, například výstrahy nástroje Operations Manager, konfigurace hodnocení, prostoru instancí a kapacity dat k serveru pro správu. Další vysoký počet data, například protokoly služby IIS, informace o výkonu a událostí zabezpečení jsou odesílány přímo k bráně OMS.  

Pokud máte jeden nebo více serverů brány nástroje Operations Manager, které jsou nasazeny v hraniční sítě nebo jiné izolovanou síť k monitorování nedůvěryhodné systémy, nemohou komunikovat s bránu OMS. Servery nástroje Operations Manager brány lze pouze sestavy k serveru pro správu. 

Pokud skupinu správy nástroje Operations Manager je nakonfigurován pro komunikaci s bránou OMS, informace o konfiguraci proxy serveru je automaticky distribuován do každý počítač spravovaný agentem, nakonfigurovaný tak, aby shromažďování dat pro analýzy protokolů, i když nastavení je prázdný.    

Pro zajištění vysoké dostupnosti pro přímo připojená k nebo Operations Management skupin, které komunikují s analýzy protokolů přes bránu OMS, můžete použít vyrovnávání zatížení sítě k přesměrování a distribuce komunikace mezi několik serverů brány OMS. Pokud jeden server brány ocitne mimo provoz, provoz se přesměruje na jiný uzel k dispozici.  

Doporučujeme nainstalovat agenta OMS na počítači, který je spuštěn software brány OMS ho monitorovat a analyzovat data výkonu nebo události. Navíc agenta pomáhá identifikovat koncovým bodům služby, které ke komunikaci s OMS brány.

Všichni agenti musí mít síťové připojení k jejich brány tak, aby se automaticky mohou přenášet data do a z brány. Nedoporučujeme instalaci gateway na řadiči domény.

Následující diagram znázorňuje tok dat od přímé agentů do Azure Automation a analýzy protokolů pomocí serveru brány. Konfiguraci proxy serveru agenta musí používat stejný port, který OMS brána používá ke komunikaci se službou.  

![agent přímé komunikaci s diagram služby](./media/log-analytics-oms-gateway/oms-omsgateway-agentdirectconnect.png)

Následující diagram znázorňuje tok dat ze skupiny pro správu nástroje Operations Manager k analýze protokolů.   

![Operations Manager komunikace s diagram analýzy protokolů](./media/log-analytics-oms-gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="prerequisites"></a>Požadavky

Pokud určíte počítač spustit bránu OMS, ujistěte se, zda že má následující součásti:

* Windows 10, Windows 8.1, Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2, Windows Server 2008
* Rozhraní .NET framework 4.5
* Minimálně 4 jádra procesoru a 8 GB paměti 

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
- portugalština (Portugalsko)
- ruština
- Španělština (mezinárodní)

### <a name="supported-encryption-protocols"></a>Šifrování podporovaných protokolů
Brána OMS podporuje pouze zabezpečení TLS (Transport Layer) 1.0, 1.1 a 1.2.  Nepodporuje Secure Sockets Layer (SSL).

### <a name="supported-number-of-agent-connections"></a>Podporované počet připojení agenta
Následující tabulka obsahuje podporované počet agentů, které komunikují se serverem brány. Tato podpora je založena na agentech odesílání přibližně 200 KB dat každých 6 sekund. Datový svazek na jednoho agenta testována je přibližně 2.7 GB za den.

|brána |Přibližný počet agentů, které jsou podporovány|  
|--------|----------------------------------|  
|-Procesoru: Intel XEON E5 procesoru 2660 v3 @ 2.6 GHz 2 jádra<br> -Paměť: 4 GB<br> -Šířku pásma sítě: 1 GB/s| 600|  
|-Procesoru: Intel XEON E5 procesoru 2660 v3 @ 2.6 GHz, 4 jádra<br> -Paměť: 8 GB<br> -Šířku pásma sítě: 1 GB/s| 1000|  

## <a name="download-the-oms-gateway"></a>Stáhněte bránu OMS

Existují dva způsoby, jak získat nejnovější verzi instalačního souboru OMS brány.

1. Stažení z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=54443).

2. Stáhněte z portálu Azure. Po přihlášení k portálu Azure, proveďte následující kroky:  

   1. Procházet seznam služeb a pak vyberte **analýzy protokolů**.  
   2. Vyberte pracovní prostor.
   3. V okně prostoru v části **Obecné**, vyberte **rychlý Start**.
   4. V části **zvolit zdroj dat pro připojení do pracovního prostoru**, vyberte **počítače**.
   5. V **přímé agenta** panel, vyberte **stáhnout brány OMS**.
   
    ![Stáhněte si OMS brány](./media/log-analytics-oms-gateway/download-gateway.png)

-- OR-- 

   1. V okně prostoru v části **nastavení**, vyberte **upřesňující nastavení**.
   2. Vyberte **připojené zdroje** > **servery Windows**. Potom vyberte **stáhnout brány OMS**.

## <a name="install-the-oms-gateway"></a>Nainstalujte bránu OMS

Pokud chcete nainstalovat bránu, proveďte následující kroky. Pokud jste nainstalovali předchozí verzi, dříve se označovaly jako *Log Analytics předávání*, se upgraduje na tuto verzi.  

1. V cílové složce, vyberte **OMS Gateway.msi**.
2. Na **úvodní** stránce vyberte **Další**.

 ![Průvodce instalací brány](./media/log-analytics-oms-gateway/gateway-wizard01.png)
  
3. Na **licenční smlouvy** vyberte **s podmínkami licenční smlouvy souhlasím**. Pak vyberte **Další**.

4. Na **Port a proxy adres** stránky:

   a. Zadejte číslo portu TCP, který se má použít pro bránu. Instalační program nakonfiguruje příchozí pravidlo s tímto číslem portu v bráně Windows firewall. Výchozí hodnota je 8080. Platný rozsah číslo portu je 1 až 65535. Pokud vstup do tohoto rozsahu nespadá, zobrazí se chybová zpráva.

   b. Pokud serveru s nainstalovanou bránu musí komunikovat prostřednictvím proxy serveru, Volitelně můžete zadejte proxy server, na kterou potřebuje připojit bránu. Příkladem může být `http://myorgname.corp.contoso.com:80`, jak je znázorněno na následujícím snímku obrazovky. Pokud toto pole ponecháte prázdné, brána se pokusí připojit přímo k Internetu.  Pokud proxy server vyžaduje ověřování, zadejte uživatelské jméno a heslo.
   
    ![Konfigurace proxy serveru brány Průvodce](./media/log-analytics-oms-gateway/gateway-wizard02.png)

4. Vyberte **Next** (Další).

5. Pokud nemáte povolena služba Microsoft Update, zobrazí se stránka Microsoft Update a můžete ji povolit. Proveďte výběr a potom vyberte **Další**. V opačném pokračujte k dalšímu kroku.

6. Na **cílovou složku** stránky, nechte výchozí složku jako **C:\Program Files\OMS brány** nebo zadejte umístění, kam chcete nainstalovat bránu OMS. Pak vyberte **Další**.

7. Na **připravené k instalaci** vyberte **nainstalovat**. Řízení uživatelských účtů, může se objevit požadavku na oprávnění k instalaci. Pokud se žádost o souhlas, vyberte **Ano**.

8. Po dokončení instalace, vyberte **Dokončit**. Můžete ověřit, že je služba spuštěná tak, že otevřete modul snap-in services.msc a ověřte, zda **OMS brány** se zobrazí v seznamu služeb, a že má stav **systémem**.

    ![Services – OMS brány](./media/log-analytics-oms-gateway/gateway-service.png)  

## <a name="configure-network-load-balancing"></a>Konfigurace služby Vyrovnávání zatížení sítě 
Brána pro zajištění vysoké dostupnosti můžete nakonfigurovat pomocí vyrovnávání zatížení sítě (NLB). Použijte Vyrovnávání zatížení sítě nebo nástroje pro vyrovnávání zatížení založené na hardwaru.  Nástroje pro vyrovnávání zatížení spravuje přenosy přesměrováním požadované připojení od OMS agentů nebo serverů pro správu nástroje Operations Manager mezi jeho uzlů. Pokud jeden server brány ocitne mimo provoz, provoz se přesměruje na jiných uzlech.

Další informace o návrhu a nasazení clusteru služby Vyrovnávání zatížení sítě systému Windows Server 2016 najdete v tématu [Vyrovnávání zatížení sítě](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing).  Následující kroky popisují postup konfigurace clusteru služby Vyrovnávání zatížení sítě společnosti Microsoft.  

1.  Přihlaste se pomocí účtu správce na systému Windows server, který je členem clusteru programu NLB.  

2.  Ve Správci serveru otevřete Správce vyrovnávání zatížení sítě. Vyberte **nástroje**a potom vyberte **Správce vyrovnávání zatížení sítě**.

3. Chcete-li připojit serveru brány OMS s nainstalovaným agentem monitorování společnosti Microsoft, klikněte pravým tlačítkem na IP adresu clusteru a pak vyberte **přidat hostitele do clusteru**.

 ![Zatížení sítě vyrovnávání Manager – přidání hostitele do clusteru](./media/log-analytics-oms-gateway/nlb02.png)

4. Zadejte IP adresu serveru brány, ke kterému se chcete připojit.

    ![Sítě Správce vyrovnávání zatížení – přidání hostitele do clusteru: připojení](./media/log-analytics-oms-gateway/nlb03.png) 
    
## <a name="configure-the-oms-agent-and-the-operations-manager-management-group"></a>Konfigurace agenta OMS a skupině pro správu nástroje Operations Manager
Následující části v tomto článku zahrnují kroky o tom, jak nakonfigurovat bránu OMS komunikovat s Azure Automation nebo analýzy protokolů přímo připojené OMS agentů, skupinu pro správu nástroje Operations Manager nebo Azure Automation Hybrid Runbook Worker.  

Zjistit požadavky a kroky pro instalaci agenta OMS na počítačích s Windows, které se připojují přímo k Log Analytics najdete v části [shromažďovat data z počítačů ve vašem prostředí s analýzy protokolů](log-analytics-concept-hybrid.md#prerequisites).

 U počítačů, Linux, najdete v části [počítače se systémem Linux připojit k analýze protokolů](log-analytics-quick-collect-linux-computer.md). Informace související s Automation Hybrid Runbook Worker najdete v tématu [nasazení Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-the-standalone-oms-agent"></a>Konfigurace agenta OMS samostatné
Informace o konfiguraci agenta použít proxy server (což je v tomto případě brány) najdete v tématu [shromažďovat data z počítačů ve vašem prostředí s analýzy protokolů](log-analytics-concept-hybrid.md#prerequisites). Pokud jste nasadili několik serverů brány za službou Vyrovnávání zatížení sítě, je konfigurace proxy serveru agenta OMS virtuální IP adresy služby NLB:

![Vlastnosti agenta Microsoft Monitoring – nastavení proxy serveru](./media/log-analytics-oms-gateway/nlb04.png)

### <a name="configure-operations-manager-all-agents-use-the-same-proxy-server"></a>Konfigurace nástroje Operations Manager: všechny agenty používat stejný server proxy
Chcete-li přidat server brány nástroje Operations Manager nakonfigurujete.  Konfiguraci proxy serveru nástroje Operations Manager je automaticky použita pro všechny agenty, které podléhají Operations Manager, i když je toto nastavení prázdné.  

Pro použití brány OMS na podporu nástroje Operations Manager, musí být místní následující součásti:

* Microsoft Monitoring Agent (verze agenta **8.0.10900.0** nebo novější) na serveru brány nainstalovaný a nakonfigurovaný pro analýzy protokolů pracovních prostorů, se kterými chcete komunikovat.

* Brána, která má připojení k Internetu nebo připojení k proxy serveru, který nemá.

> [!NOTE]
> Pokud nezadáte hodnotu pro bránu, prázdné hodnoty odesílají na všechny agenty.
> 

Pokud je prvním pracovní prostor analýzy protokolů je registrace skupiny pro správu nástroje Operations Manager, nebudete mít k dispozici v konzoli Operations console můžete zadat konfiguraci proxy serveru pro skupinu pro správu. 

Skupina pro správu musí být úspěšně registrován u služby, než tato možnost je k dispozici. Aktualizujte konfiguraci proxy serveru systému použití příkazu Netsh ve stejném systému, na kterém se spouští konzoli Operations console a všechny servery pro správu ve skupině pro správu.

1. Otevřete příkazový řádek se zvýšenými oprávněními.

    a. Přejděte na **spustit**. Pak zadejte **cmd**.
    
    b. Klikněte pravým tlačítkem na **příkazového řádku**. Potom vyberte **spustit jako správce**.
    
2. Zadejte následující příkaz a potom vyberte **Enter**:

    `netsh winhttp set proxy <proxy>:<port>`

Po dokončení integrace s analýzy protokolů změn můžete odebrat spuštěním `netsh winhttp reset proxy`. Potom pomocí **konfigurovat proxy server** možnost v konzoli Operations console zadat server brány OMS. 

1. Otevřete konzolu nástroje Operations Manager. V části **Operations Management Suite**, vyberte **připojení**. Potom vyberte **nakonfigurovat Proxy Server**.

    ![Nástroje Operations Manager – nakonfigurujte Proxy Server](./media/log-analytics-oms-gateway/scom01.png)

2. Vyberte **používat proxy server pro přístup k službě Operations Management Suite**. Zadejte IP adresu serveru brány OMS nebo virtuální IP adresy služby NLB. Ujistěte se, že začínáte s `http://` předponu.

    ![Nástroj Operations Manager – adresu proxy serveru.](./media/log-analytics-oms-gateway/scom02.png)

3. Vyberte **Finish** (Dokončit). Skupiny pro správu nástroje Operations Manager je nyní nakonfigurováno pro komunikaci prostřednictvím serveru brány ke službě Analýza protokolů.

### <a name="configure-operations-manager-specific-agents-use-proxy-server"></a>Konfigurace nástroje Operations Manager: konkrétní agentů, použít proxy server
V prostředích velká nebo složitá pouze můžete použít server brány OMS konkrétních serverů (nebo skupiny). Pro tyto servery nelze aktualizovat agenta nástroje Operations Manager přímo, protože tato hodnota se přepíše globální hodnotou pro skupinu pro správu. Místo toho můžete přepsat pravidlo, které se používá k nabízení tyto hodnoty.  

> [!NOTE] 
> Tento stejný postup konfigurace lze použít k povolení použití více serverů brány OMS ve vašem prostředí. Může například vyžadovat konkrétní servery brány OMS ho zadat na základě podle oblasti.
>  

1. Otevřete konzolu nástroje Operations Manager a pak vyberte **vytváření** pracovního prostoru.

2. V pracovním prostoru vytváření obsahu, vyberte **pravidla**. Vyberte **oboru** tlačítka na panelu nástrojů nástroje Operations Manager. Pokud toto tlačítko není k dispozici, zkontrolujte, ujistěte se, že máte, nikoliv složka v vybrán objekt **monitorování** podokně. **Obor objektů sady Management Pack** dialogové okno zobrazí seznam běžných cílové třídy, skupiny nebo objekty. 

3. v **vyhledejte** zadejte **služba Health Service**. Potom vyberte ho ze seznamu. Vyberte **OK**.  

4. Vyhledejte pravidlo **pravidlo nastavení proxy serveru Advisor**. V nástrojů kontroly Operations console vyberte **přepsání**. Potom vyberte **přepsat Rule\For konkrétní objekt třídy: Služba Health Service**. 

5. Potom vyberte konkrétní objekt ze seznamu. 

6. (Volitelné) Vytvořte vlastní skupinu, která obsahuje objekt služby stavu serverů, na které chcete použít toto přepsání. Pak lze následně použít přepsání do této skupiny.

7. V **vlastnosti přepsání** dialogové okno, kliknutím na toto políčko zaškrtněte v **přepsat** vedle sloupce **WebProxyAddress** parametr.  V **hodnotu přepsání** pole, zadejte adresu URL serveru brány OMS, zajistíte, že je spuštěna s `http://` předponu.  

    >[!NOTE]
    > Nemusíte povolení pravidla. Již je automaticky spravován pomocí přepsání, který je obsažen v sadě management pack Microsoft System Center Advisor zabezpečené referenční Override s cílem Microsoft System Center Advisor monitorování skupiny serverů.
    >   

8. Buď vyberte sadu management pack z **vyberte cílovou sadu management pack** seznamu nebo vytvořte novou sadu management pack tak, že vyberete **nový**. 

9. Po dokončení změny vyberte **OK**. 

### <a name="configure-the-oms-gateway-for-automation-hybrid-runbook-workers"></a>Konfigurace brány OMS pro automatizaci procesů Hybrid Runbook Worker
Pokud máte automatizace procesů Hybrid Runbook Worker ve vašem prostředí, zadejte následující kroky ručně, dočasné řešení a nakonfigurujte bránu OMS, které je podporují.

V následujících krocích musíte znát oblasti Azure, kde je umístěn účet Automation. A vyhledejte umístění, proveďte následující kroky:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Vyberte službu Azure Automation.
3. Vyberte příslušný účet Azure Automation.
4. V části **umístění**, zobrazit oblast účtu.

    ![Portál Azure – umístění účtu Automation.](./media/log-analytics-oms-gateway/location.png)  

Pomocí následující tabulky Identifikujte adresu URL pro každé umístění:

**Úloha adresy URL služby dat za běhu**

| **Umístění** | **Adresa URL** |
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

| **Umístění** | **Adresa URL** |
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

Pokud váš počítač se automaticky registruje jako hybridní pracovní proces Runbooku pro použití dílčích oprav pomocí řešení správy aktualizací, postupujte podle těchto kroků:

1. Adresy URL služby modulu Runtime Data úlohy přidáte do seznamu hostitelů povolené na bráně OMS. Můžete například zadejte následující: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`

2. Restartujte službu OMS brány pomocí následující rutiny prostředí PowerShell: `Restart-Service OMSGatewayService`

Pokud jste integrovaného počítače pro Azure Automation pomocí rutiny registrace hybridní pracovní proces Runbooku, postupujte podle těchto kroků:

1. Přidáte adresu URL pro registraci služby agenta do seznamu hostitelů povolené na bráně OMS. Můžete například zadejte následující: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`

2. Adresy URL služby modulu Runtime Data úlohy přidáte do seznamu hostitelů povolené na bráně OMS. Můžete například zadejte následující: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`

3. Restartujte službu OMS brány:  `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Užitečné rutiny prostředí PowerShell
Rutiny můžete dokončit úkoly, které jsou nezbytné pro aktualizaci konfiguračních nastavení brány OMS. Předtím, než je použijete, nezapomeňte provést následující kroky:

1. Nainstalujte bránu OMS (MSI).
2. Otevřete okno konzoly prostředí PowerShell.
3. Naimportujte modul zadáním následujícího příkazu: `Import-Module OMSGateway`.
4. Pokud k žádné chybě došlo v předchozím kroku, modul byl úspěšně importován a použít rutiny. Zadejte `Get-Module OMSGateway`.
5. Když provedete změny pomocí rutin, ujistěte se, službu Brána OMS.

Modul nebyl importován, pokud dojde k chybě v kroku 3. K chybě může dojít, když nelze nalézt modul prostředí PowerShell. Najdete ho v instalační cestě OMS brány: *C:\Program Files\Microsoft OMS Gateway\PowerShell*.

| **Cmdlet** | **Parametry** | **Popis** | **Příklad** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Klíč |Získá konfiguraci této služby |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Klíč (povinné) <br> Hodnota |Změny konfigurace služby |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Získá adresu proxy serveru předávání (nadřazený) |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Adresa<br> Uživatelské jméno<br> Heslo |Nastaví adresu (a přihlašovacích údajů) (nadřazený) proxy předávání |1. Nastavení proxy serveru pro předávání a přihlašovací údaje:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Nastavte předávání přes proxy server, který nevyžaduje ověření: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Zrušte zaškrtnutí políčka předávání nastavení proxy serveru:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Získá aktuálně povolené hostitele (pouze místně nakonfigurované povolené hostitele; nezahrnuje automaticky stažené povolené hostitele) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Hostitel (povinné) |Přidá hostitele do seznamu povolených |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Hostitel (povinné) |Odebere hostitele ze seznamu povolených |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Předmět (povinné) |Přidá certifikát klienta může seznam povolených položek |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Předmět (povinné) |Odebere ze seznamu povolených předmětu certifikátu klienta |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Získá aktuálně povolené klienta předměty certifikátu (pouze místně nakonfigurované povolené témata; nezahrnuje povolené témata automaticky stažené) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Řešení potíží
Shromažďování událostí, které se protokolují bránou, musíte také být nainstalován agent OMS.

![Prohlížeč událostí – v protokolu brány OMS](./media/log-analytics-oms-gateway/event-viewer.png)

**ID událostí OMS brány a popisy**

Následující tabulka uvádí ID událostí a popisy pro OMS brány protokolu událostí:

| **ID** | **Popis** |
| --- | --- |
| 400 |Chyby aplikace, která nemá konkrétním ID. |
| 401 |Chybná konfigurace. Příklad: listenPort = "text" místo celé číslo. |
| 402 |Výjimka při analýze zprávy metody handshake TLS. |
| 403 |Došlo k chybě sítě. Příklad: Nelze se připojit k cílovému serveru. |
| 100 |Obecné informace. |
| 101 |Služba byla spuštěna. |
| 102 |Služba byla zastavena. |
| 103 |Příkaz připojení protokolu HTTP přijatých od klienta. |
| 104 |Není připojení protokolu HTTP příkaz. |
| 105 |Cílový server se nenachází v seznamu povolených nebo cílový port není zabezpečený port (443). <br> <br> Zkontrolujte, že agent MMA na serveru brány a agenty, kteří komunikují s bránou připojeni k ve stejném pracovním prostoru analýzy protokolů. |
| 105 |Chyba TcpConnection – neplatný klientský certifikát: CN = brány <br><br> Zajistěte, aby: <br>    <br> -Používáte bránu s číslem verze 1.0.395.0 nebo vyšší. <br> – Agenta MMA na serveru brány a agenty, kteří komunikují pomocí brány jsou připojené k ve stejném pracovním prostoru analýzy protokolů. |
| 106 |Bránu OMS podporuje jenom TLS 1.0, TLS 1.1 a 1.2.  Nepodporuje SSL. OMS brány pro všechny nepodporovanou verzi protokolu TLS/SSL, vygeneruje událost ID 106.|
| 107 |Relace protokolu TLS byla ověřena. |

**Čítače výkonu ke shromažďování**

Následující tabulka uvádí čítače výkonu, které jsou k dispozici pro bránu OMS. Čítače můžete přidat pomocí sledování výkonu.

| **Název** | **Popis** |
| --- | --- |
| Připojení klienta brány/aktivní OMS |Počet připojení active klientské sítě (TCP) |
| Počet brány nebo chyb OMS |Počet chyb |
| OMS brány nebo připojení klienta |Počet připojených klientů |
| Počet brány nebo odmítání OMS |Počet zamítnutí z důvodu chyby ověřování TLS |

![Čítače výkonu OMS brány](./media/log-analytics-oms-gateway/counters.png)

## <a name="get-assistance"></a>Získejte pomoc
Když se přihlásíte na portál Azure, můžete vytvořit žádost o pomoc s bránu OMS nebo jiné služby Azure nebo funkce služby.

Chcete-li požádat o pomoc, vyberte symbol otazník v pravém horním rohu portálu. Potom vyberte **nová žádost o podporu**. Dokončení nový formulář žádosti o podporu.

![Nová žádost o podporu](./media/log-analytics-oms-gateway/support.png)

## <a name="next-steps"></a>Další postup
[Přidat zdroje dat](log-analytics-data-sources.md) pro shromažďování dat z připojených zdrojů a uložte ji do pracovního prostoru analýzy protokolů.
