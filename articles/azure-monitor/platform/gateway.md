---
title: Připojení počítačů pomocí brány Log Analytics | Dokumenty společnosti Microsoft
description: Připojte svá zařízení a počítače monitorované pomocí nástroje Operations Manager pomocí brány Log Analytics k odesílání dat do služby Azure Automation a Log Analytics, pokud nemají přístup k internetu.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/24/2019
ms.openlocfilehash: a92e96a835f24ac54fa55b05086a35b9a91d609e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298338"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway-in-azure-monitor"></a>Připojení počítačů bez přístupu k internetu pomocí brány Log Analytics v Azure Monitoru

>[!NOTE]
>S přechodem sady Microsoft Operations Management Suite (OMS) na Microsoft Azure Monitor se terminologie mění. Tento článek odkazuje na bránu OMS jako bránu Azure Log Analytics. 
>

Tento článek popisuje, jak nakonfigurovat komunikaci s Azure Automation a Azure Monitor pomocí brány Log Analytics, když počítače, které jsou přímo připojené nebo které jsou monitorované Operations Manager nemají přístup k internetu. 

Brána Log Analytics je server proxy http forward, který podporuje tunelové propojení HTTP pomocí příkazu HTTP CONNECT. Tato brána odesílá data do Azure Automation a do pracovního prostoru Analýzy protokolů v Azure Monitoru jménem počítačů, které se nemůžou přímo připojit k internetu. 

Brána Log Analytics podporuje:

* Vytváření sestav až do stejných pracovních prostorů Analýzy protokolů nakonfigurovaných na každém agentovi za ním, které jsou nakonfigurované pomocí hybridních pracovníků runbooku Azure Automation.  
* Počítače se systémem Windows, ve kterých je agent monitorování Microsoftu přímo připojený k pracovnímu prostoru Analýzy protokolů ve službě Azure Monitor.
* Počítače s Linuxem, na kterých je agent Analýzy protokolů pro Linux přímo připojený k pracovnímu prostoru Log Analytics v Azure Monitoru.  
* System Center Operations Manager 2012 SP1 s UR7, Operations Manager 2012 R2 s UR3 nebo skupina pro správu v Operations Manager 2016 nebo novější, která je integrovaná s Log Analytics.  

Některé zásady zabezpečení IT neumožňují připojení k internetu pro síťové počítače. Tyto nepřipojené počítače mohou být například zařízení pokladního místa (POS) nebo servery podporující it služby. Chcete-li tato zařízení připojit k azure automatizaci nebo pracovní hostopočítače log analytics, takže je můžete spravovat a monitorovat, nakonfigurujte je pro přímou komunikaci s bránou Log Analytics. Brána Log Analytics může přijímat informace o konfiguraci a předávat data jejich jménem. Pokud jsou počítače nakonfigurovány s agentem Log Analytics pro přímé připojení k pracovnímu prostoru Analýzy protokolů, počítače místo toho komunikují s bránou Log Analytics.  

Brána Log Analytics přenáší data z agentů přímo do služby. Neanalyzuje žádná data při přenosu a brána neukládá data do mezipaměti, když ztratí připojení ke službě. Pokud brána nemůže komunikovat se službou, agent bude nadále spuštěn a zařadí shromážděná data do fronty na disk monitorovaného počítače. Po obnovení připojení agent odešle data uložená v mezipaměti shromážděná do Azure Monitoru.

Pokud je skupina pro správu nástroje Operations Manager integrována se službou Log Analytics, lze servery pro správu nakonfigurovat tak, aby se připojovalo k bráně Log Analytics a přijímali informace o konfiguraci a odesílali shromážděná data v závislosti na povoleném řešení. .  Agenti nástroje Operations Manager odesílají některá data na server pro správu. Agenti mohou například odesílat výstrahy nástroje Operations Manager, data vyhodnocení konfigurace, data prostoru instance a data kapacity. Další velkoobjemová data, například protokoly Internetové informační služby (IIS), data o výkonu a události zabezpečení, jsou odesílána přímo do brány Log Analytics. 

Pokud je jeden nebo více serverů brány Operations Manager nasazeny k monitorování nedůvěryhodných systémů v hraniční síti nebo v izolované síti, nemohou tyto servery komunikovat s bránou Log Analytics.  Servery brány Operations Manager mohou podávat zprávy pouze serveru pro správu.  Když je skupina pro správu nástroje Operations Manager nakonfigurována pro komunikaci s bránou Log Analytics, informace o konfiguraci proxy serveru se automaticky distribuují do každého počítače spravovaného agentem, který je nakonfigurovaný pro shromažďování dat protokolu pro Azure Monitor, i v případě, že je nastavení prázdné.

Chcete-li zajistit vysokou dostupnost pro přímo připojené skupiny nebo skupiny správy operací, které komunikují s pracovním prostorem Log Analytics prostřednictvím brány, použijte službu Vyrovnávání zatížení sítě (NLB) k přesměrování a distribuci provozu mezi více servery brány. Tímto způsobem, pokud jeden server brány přejde dolů, provoz je přesměrován na jiný dostupný uzel.  

Počítač, ve který běží brána Log Analytics, vyžaduje, aby agent systému Windows analýzy protokolů identifikoval koncové body služby, se kterými brána potřebuje komunikovat. Agent také potřebuje nasměrovat bránu do sestavy do stejných pracovních prostorů, se kterými jsou konfigurováni agenti nebo skupina pro správu nástroje Operations Manager za bránou. Tato konfigurace umožňuje bráně a agentovi komunikovat s přiřazeným pracovním prostorem.

Bránu lze nastavit s více domovními nastaveními až do čtyř pracovních prostorů. Jedná se o celkový počet pracovních prostorů, které agent systému Windows podporuje.  

Každý agent musí mít připojení k bráně v síti, aby agenti mohli automaticky přenášet data do brány a z ní. Neinstalujte bránu do řadiče domény. Počítače s Linuxem, které jsou za serverem brány, nemohou k instalaci agenta Log Analytics pro Linux použít metodu [instalace obálkového skriptu.](agent-linux.md#install-the-agent-using-wrapper-script) Agent musí být stažen ručně, zkopírován do počítače a nainstalován ručně, protože brána podporuje jenom komunikaci se službami Azure uvedenými výše.

Následující diagram znázorňuje data tok z přímých agentů, přes bránu, do Azure Automation a Log Analytics. Konfigurace proxy agenta musí odpovídat portu, se kterým je brána Log Analytics nakonfigurována.  

![Schéma přímé komunikace agentů se službami](./media/gateway/oms-omsgateway-agentdirectconnect.png)

Následující diagram znázorňuje tok dat ze skupiny správy nástroje Operations Manager do analýzy protokolů.   

![Diagram komunikace nástroje Operations Manager s analýzou protokolů](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>Nastavení systému

Počítače určené ke spuštění brány Log Analytics musí mít následující konfiguraci:

* Windows 10, Windows 8.1 nebo Windows 7
* Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 nebo Windows Server 2008
* Microsoft .NET Framework 4.5
* Alespoň 4jádrový procesor a 8 GB paměti 
* [Agent analýzy protokolů pro systém Windows,](agent-windows.md) který je nakonfigurován tak, aby se hlásil do stejného pracovního prostoru jako agenti, kteří komunikují prostřednictvím brány

### <a name="language-availability"></a>Dostupnost jazyka

Brána Log Analytics je k dispozici v těchto jazycích:

- Čínština (zjednodušená)
- Čínština (tradiční)
- Čeština
- Nizozemština
- Angličtina
- Francouzština
- Němčina
- Maďarština
- Italština
- Japonština
- Korejština
- Polština
- Portugalština (Brazílie)
- portugalština (Portugalsko)
- Ruština
- Španělština (mezinárodní)

### <a name="supported-encryption-protocols"></a>Podporované šifrovací protokoly

Brána Log Analytics podporuje pouze zabezpečení transportní vrstvy (TLS) 1.0, 1.1 a 1.2.  Nepodporuje ssl (Secure Sockets Layer).  Chcete-li zajistit zabezpečení dat při přenosu do Log Analytics, nakonfigurujte bránu tak, aby používala alespoň TLS 1.2. Starší verze TLS nebo SSL jsou ohroženy. I když v současné době umožňují zpětnou kompatibilitu, vyhněte se jejich použití.  

Další informace naleznete v recenzi [Odesílání dat bezpečně pomocí TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

### <a name="supported-number-of-agent-connections"></a>Podporovaný počet připojení agentů

V následující tabulce je přibližně uvedeno, kolik agentů může komunikovat se serverem brány. Podpora je založena na agentech, kteří každých 6 sekund nahrají přibližně 200 kB dat. Pro každého testovaného agenta je objem dat přibližně 2,7 GB za den.

|brána |Agenti podporováni (přibližné)|  
|--------|----------------------------------|  
|CPU: Procesor Intel Xeon E5-2660 v3 \@ 2,6 GHz 2 jádra<br> Paměť: 4 GB<br> Šířka pásma sítě: 1 Gb/s| 600|  
|CPU: Procesor Intel Xeon E5-2660 v3 \@ 2,6 GHz 4 jádra<br> Paměť: 8 GB<br> Šířka pásma sítě: 1 Gb/s| 1000|  

## <a name="download-the-log-analytics-gateway"></a>Stažení brány Log Analytics

Získejte nejnovější verzi instalačního souboru brány Log Analytics z webu Microsoft Download Center[(Odkaz ke stažení)](https://go.microsoft.com/fwlink/?linkid=837444)nebo z portálu Azure.

Pokud chcete získat bránu Log Analytics z webu Azure Portal, postupujte takto:

1. Prohlédněte si seznam služeb a vyberte **log analytics**. 
1. Vyberte pracovní prostor.
1. V okně pracovní plochy vyberte v části **Obecné** **položku Rychlý start**. 
1. V části **Výběr zdroje dat, který se chcete připojit k pracovnímu prostoru**, vyberte **Počítače**.
1. V okně **Direct Agent** vyberte **Stáhnout bránu Log Analytics**.
 
   ![Snímek obrazovky s postupem stažení brány Log Analytics](./media/gateway/download-gateway.png)

– nebo – 

1. V okně pracovní plochy vyberte v části **Nastavení** **upřesnit nastavení**.
1. Přejděte na**servery Windows** **připojené zdroje** > a vyberte **Stáhnout bránu Log Analytics**.

## <a name="install-log-analytics-gateway-using-setup-wizard"></a>Instalace brány Log Analytics pomocí průvodce nastavením

Chcete-li nainstalovat bránu pomocí průvodce instalací, postupujte takto. 

1. V cílové složce poklikejte na **soubor Log Analytics gateway.msi**.
1. Na **úvodní** stránce vyberte **Další**.

   ![Snímek obrazovky úvodní stránky v průvodci nastavením brány](./media/gateway/gateway-wizard01.png)

1. Na stránce **Licenční smlouva** vyberte souhlasím **s podmínkami licenční smlouvy,** abychom souhlasili s licenčními podmínkami pro software společnosti Microsoft, a pak vyberte **další**.
1. Na stránce **Port a proxy adresa:**

   a. Zadejte číslo portu TCP, které má být použito pro bránu. Instalační program používá toto číslo portu ke konfiguraci příchozího pravidla brány Windows Firewall.  Výchozí hodnota je 8080.
      Platný rozsah čísla portu je 1 až 65535. Pokud vstup nespadá do tohoto rozsahu, zobrazí se chybová zpráva.

   b. Pokud server, na kterém je brána nainstalována, potřebuje komunikovat prostřednictvím proxy serveru, zadejte adresu proxy, na kterou se brána potřebuje připojit. Zadejte například `http://myorgname.corp.contoso.com:80`.  Pokud toto pole ponecháte prázdné, brána se pokusí připojit přímo k Internetu.  Pokud proxy server vyžaduje ověření, zadejte uživatelské jméno a heslo.

   c. Vyberte **další**.

   ![Snímek obrazovky s konfigurací pro proxy server brány](./media/gateway/gateway-wizard02.png)

1. Pokud nemáte povolenou službu Microsoft Update, zobrazí se stránka Microsoft Update a můžete ji povolit. Proveďte výběr a pak vyberte **Další**. V opačném případě pokračujte k dalšímu kroku.
1. Na stránce **Cílová složka** ponechejte výchozí složku C:\Program Files\OMS Gateway nebo zadejte umístění, kam chcete bránu nainstalovat. Pak vyberte **Další**.
1. Na stránce **Připraveno k instalaci** vyberte **Instalovat**. Pokud nástroj Řízení uživatelských účtů požaduje oprávnění k instalaci, vyberte **možnost Ano**.
1. Po dokončení instalace vyberte **dokončit**. Chcete-li ověřit, zda je služba spuštěna, otevřete modul snap-in services.msc a ověřte, zda se **brána OMS** zobrazuje v seznamu služeb a zda je její stav **spuštěn**.

   ![Snímek obrazovky s místními službami, který ukazuje, že je spuštěna brána OMS](./media/gateway/gateway-service.png)

## <a name="install-the-log-analytics-gateway-using-the-command-line"></a>Instalace brány Log Analytics pomocí příkazového řádku

Stažený soubor brány je balíček Instalační služby systému Windows, který podporuje tichou instalaci z příkazového řádku nebo jiné automatizované metody. Pokud nejste obeznámeni se standardními možnostmi příkazového řádku Instalační služby systému Windows, přečtěte [si informace o možnostech příkazového řádku](https://docs.microsoft.com/windows/desktop/Msi/command-line-options).
 
V následující tabulce jsou zvýrazněny parametry podporované nastavením.

|Parametry| Poznámky|
|----------|------| 
|Portnumber | Číslo portu TCP, na které má brána poslouchat |
|Proxy | IP adresa proxy serveru |
|INSTALLDIR | Plně kvalifikovaná cesta k určení instalačního adresáře softwarových souborů brány |
|USERNAME | ID uživatele k ověření pomocí proxy serveru |
|Heslo | Heslo ID uživatele k ověření pomocí proxy serveru |
|LicenseAccepted | Zadejte hodnotu **1,** abyste ověřili, že přijímáte licenční smlouvu. |
|Hasauth | Zadejte hodnotu **1** při zadání parametrů USERNAME/PASSWORD |
|APLIKACE HASPROXY | Zadání hodnoty **1** při zadávání adresy IP pro parametr **PROXY** |

Chcete-li bránu bezobslužně nainstalovat a nakonfigurovat pomocí konkrétní adresy proxy, čísla portu, zadejte následující příkaz:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 LicenseAccepted=1 
```

Pomocí možnosti příkazového řádku /qn skryje nastavení , /qb ukazuje nastavení během tiché instalace.  

Pokud potřebujete zadat pověření k ověření pomocí proxy serveru, zadejte následující příkaz:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 HASAUTH=1 USERNAME="<username>" PASSWORD="<password>" LicenseAccepted=1 
```

Po instalaci můžete potvrdit, že nastavení jsou přijata (s výjimkou uživatelského jména a hesla) pomocí následujících rutin prostředí PowerShell:

- **Get-OMSGatewayConfig** – Vrátí port TCP, na který je brána nakonfigurována pro naslouchání.
- **Get-OMSGatewayRelayProxy** – Vrátí IP adresu proxy serveru, se kterým jste jej nakonfigurovali pro komunikaci.

## <a name="configure-network-load-balancing"></a>Konfigurace vyrovnávání zatížení sítě

Bránu můžete nakonfigurovat pro vysokou dostupnost pomocí služby Vyrovnávání zatížení sítě (NLB) pomocí služby [Vyrovnávání zatížení sítě (NLB)](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing)společnosti Microsoft , [služby Azure Load Balancer](../../load-balancer/load-balancer-overview.md)nebo hardwarových vykladačů zatížení. Nástroj pro vyrovnávání zatížení spravuje provoz přesměrováním požadovaných připojení z agentů Analýzy protokolů nebo serverů pro správu nástroje Operations Manager napříč jeho uzly. Pokud jeden server brány přejde dolů, provoz se přesměruje do jiných uzlů.

### <a name="microsoft-network-load-balancing"></a>Vyrovnávání zatížení sítě společnosti Microsoft

Informace o návrhu a nasazení clusteru pro vyrovnávání zatížení sítě Windows Server 2016 naleznete v [tématu Vyrovnávání zatížení sítě](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing). Následující kroky popisují, jak nakonfigurovat cluster pro vyrovnávání zatížení sítě společnosti Microsoft.  

1. Přihlaste se k serveru Windows, který je členem clusteru služby Vyrovnávání zatížení sítě s účtem správce.  
2. Spusťte ve Správci serveru Správce zatížení sítě, klepněte na tlačítko **Nástroje**a potom klepněte na položku **Správce vyrovnávání zatížení sítě**.
3. Chcete-li připojit server brány Log Analytics s nainstalovaným agentem monitorování společnosti Microsoft, klepněte pravým tlačítkem myši na adresu IP clusteru a potom klepněte na příkaz **Přidat hostitele do clusteru**. 

    ![Správce vyrovnávání zatížení sítě – přidat hostitele do clusteru](./media/gateway/nlb02.png)
 
4. Zadejte IP adresu serveru brány, který chcete připojit. 

    ![Správce vyrovnávání zatížení sítě – přidat hostitele do clusteru: Připojit](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Azure Load Balancer

Informace o tom, jak navrhnout a nasadit Azure Balancer, najdete v tématu [Co je Azure Load Balancer?](../../load-balancer/load-balancer-overview.md). Chcete-li nasadit základní nástroj pro vyrovnávání zatížení, postupujte podle kroků uvedených v tomto [rychlém startu](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) s výjimkou kroků popsaných v části Vytvoření **serverů back-end**.   

> [!NOTE]
> Konfigurace nástroje pro vyrovnávání zatížení Azure pomocí **základní skladové položky**vyžaduje, aby virtuální počítače Azure patřily do skupiny dostupnosti. Další informace o sadách dostupnosti najdete [v tématu Správa dostupnosti virtuálních počítačů s Windows v Azure](../../virtual-machines/windows/manage-availability.md). Pokud chcete přidat existující virtuální počítače do skupiny dostupnosti, přečtěte [si informace o nastavení sady dostupnosti virtuálních počítačů Azure Správce prostředků](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4).
> 

Po vytvoření systému vyrovnávání zatížení je třeba vytvořit back-endový fond, který distribuuje provoz na jeden nebo více serverů brány. Postupujte podle kroků popsaných v části článku quickstart [Vytvoření prostředků pro nástrojů pro vyrovnávání zatížení](../../load-balancer/quickstart-load-balancer-standard-public-portal.md).  

>[!NOTE]
>Při konfiguraci sondy stavu by měl být nakonfigurován tak, aby používal port TCP serveru brány. Sonda stavu dynamicky přidává nebo odebere servery brány z rotace vykladače zatížení na základě jejich odpovědi na kontroly stavu. 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>Konfigurace skupiny pro správu agenta analýzy protokolů a nástroje Operations Manager

V této části uvidíte, jak nakonfigurovat přímo připojené agenty Analýzy protokolů, skupinu pro správu Operations Manager nebo hybridní pracovníky Runbook azure automation s bránou Log Analytics pro komunikaci s Azure Automation nebo Log Analytics.  

### <a name="configure-a-standalone-log-analytics-agent"></a>Konfigurace samostatného agenta analýzy protokolů

Při konfiguraci agenta Analýzy protokolů nahraďte hodnotu proxy serveru ip adresou serveru brány Log Analytics a jeho číslem portu. Pokud jste nasadili více serverů brány za nástroj pro vyrovnávání zatížení, konfigurace proxy agenta Analýzy protokolů je virtuální IP adresa nástroje pro vyrovnávání zatížení.  

>[!NOTE]
>Pokud chcete nainstalovat agenta Log Analytics na bránu a počítače se systémem Windows, které se přímo připojují k Log Analytics, přečtěte si informace [o připojení počítačů se systémem Windows ke službě Log Analytics v Azure](agent-windows.md). Informace o připojení počítačů s Linuxem najdete [v tématu Připojení počítačů S Linuxem k Azure Monitoru](agent-linux.md). 
>

Po instalaci agenta na server brány jej nakonfigurujte tak, aby se hlásil agentům pracovního prostoru nebo pracovního prostoru, kteří komunikují s bránou. Pokud agent systému Windows Log Analytics není nainstalován v bráně, událost 300 je zapsána do protokolu událostí brány OMS, což znamená, že agent musí být nainstalován. Pokud je agent nainstalován, ale není nakonfigurován tak, aby se hlásil do stejného pracovního prostoru jako agenti, kteří prostřednictvím něj komunikují, událost 105 je zapsána do stejného protokolu, což znamená, že agent na bráně musí být nakonfigurován tak, aby se hlásil do stejného pracovního prostoru jako agenti, kteří komunikovat s bránou.

Po dokončení konfigurace restartujte službu **OMS Gateway** a použijte změny. V opačném případě brána odmítne agenty, kteří se pokoušejí komunikovat s Log Analytics a bude hlásit událost 105 v protokolu událostí brány OMS. K tomu dojde také při přidání nebo odebrání pracovního prostoru z konfigurace agenta na serveru brány.

Informace týkající se pracovníka hybridního runbooku automatizace naleznete [v tématu Automatizace prostředků v datovém centru nebo cloudu pomocí hybridního pracovníka runbooku](../../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>Konfigurace nástroje Operations Manager, kde všichni agenti používají stejný proxy server

Konfigurace proxy nástroje Operations Manager je automaticky použita pro všechny agenty, kteří jsou v systému Operations Manager, a to i v případě, že je nastavení prázdné.  

Chcete-li používat bránu OMS k podpoře nástroje Operations Manager, musíte mít:

* Microsoft Monitoring Agent (verze 8.0.10900.0 nebo novější) nainstalovaný na serveru brány OMS a nakonfigurovaný se stejnými pracovními prostory služby Log Analytics, do kterých je vaše skupina pro správu nakonfigurována.
* Připojení k internetu. OMS Gateway musí být také připojena k proxy serveru, který je připojen k Internetu.

> [!NOTE]
> Pokud zadáte žádnou hodnotu pro bránu, prázdné hodnoty jsou nabízeny všem agentům.
>

Pokud se vaše skupina pro správu nástroje Operations Manager registruje v pracovním prostoru Analýzy protokolů poprvé, nezobrazí se možnost určit konfiguraci serveru proxy pro skupinu pro správu v konzole Operations Console. Tato možnost je k dispozici pouze v případě, že skupina pro správu byla zaregistrována ve službě.  

Chcete-li nakonfigurovat integraci, aktualizujte konfiguraci systémového proxy serveru pomocí programu Netsh v systému, ve kterém používáte konzolu Operations console, a na všech serverech pro správu ve skupině pro správu. Postupujte následovně:

1. Otevřete příkazový řádek se zvýšenými oprávněními:

   a. Vyberte **Start** a zadejte **cmd**.  

   b. Klepněte pravým tlačítkem myši na **příkazový řádek** a vyberte **příkaz Spustit jako správce**.  

1. Zadejte následující příkaz:

   `netsh winhttp set proxy <proxy>:<port>`

Po dokončení integrace s Log Analytics, `netsh winhttp reset proxy`odebrat změnu spuštěním . Potom v konzole Operations console použijte možnost **Konfigurovat proxy server** k určení serveru brány Log Analytics. 

1. Na konzole Operations Manager vyberte v části **Operations Management Suite** **položku Connection**a pak vyberte Konfigurovat proxy **server**.

   ![Snímek obrazovky nástroje Operations Manager s výběrem Konfigurovat proxy server](./media/gateway/scom01.png)

1. Vyberte **Vyberte Použít proxy server pro přístup k sadě Operations Management Suite** a zadejte IP adresu serveru brány Log Analytics nebo virtuální IP adresu nástroje pro vyrovnávání zatížení. Dávejte pozor, abyste začali `http://`s předponou .

   ![Snímek obrazovky nástroje Operations Manager s adresou proxy serveru](./media/gateway/scom02.png)

1. Vyberte **Finish** (Dokončit). Vaše skupina pro správu nástroje Operations Manager je nyní nakonfigurována pro komunikaci prostřednictvím serveru brány se službou Log Analytics.

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>Konfigurace nástroje Operations Manager, kde určití agenti používají proxy server

Pro velká nebo složitá prostředí můžete chtít používat server brány Log Analytics pouze určité servery (nebo skupiny).  U těchto serverů nelze aktualizovat agenta nástroje Operations Manager přímo, protože tato hodnota je přepsána globální hodnotou pro skupinu pro správu.  Místo toho přepsat pravidlo použité k nabízení těchto hodnot.  

> [!NOTE] 
> Tuto konfigurační techniku použijte, pokud chcete povolit více serverů brány Log Analytics ve vašem prostředí.  Můžete například vyžadovat, aby byly konkrétní servery brány Log Analytics určeny na místním základě.
>

Postup konfigurace konkrétních serverů nebo skupin pro použití serveru brány Log Analytics: 

1. Otevřete konzolu Nástroje pro nástroj Operations Manager a vyberte pracovní prostor **vytváření.**  
1. V pracovním prostoru Vytváření vyberte **Pravidla**. 
1. Na panelu nástrojů Nástroje nástroje Operations Manager vyberte tlačítko **Obor.** Pokud toto tlačítko není k dispozici, ujistěte se, že jste v podokně **Monitorování** vybrali objekt, nikoli složku. Dialogové okno **Objekty sady Management Pack oboru** zobrazuje seznam běžných cílových tříd, skupin nebo objektů. 
1. Do pole **Vyhledat** zadejte **službu Zdravotnictví** a vyberte ji ze seznamu. Vyberte **OK**.  
1. Vyhledejte **pravidlo nastavení proxy serveru poradce**. 
1. Na panelu nástrojů Nástroje nástroje Operations Manager vyberte **Přepsat** a pak přejděte na **Přepsat pravidlo\Pro určitý objekt třídy: Služba Zdraví** a vyberte objekt ze seznamu.  Nebo vytvořte vlastní skupinu, která obsahuje objekt služby health service serverů, na které chcete toto přepsání použít. Potom použijte přepsání na vlastní skupinu.
1. V dialogovém okně **Přepsat vlastnosti** přidejte zaškrtnutí ve sloupci **Přepsat** vedle parametru **WebProxyAddress.**  Do pole **Přepsat hodnotu** zadejte adresu URL serveru brány Log Analytics. Dávejte pozor, abyste začali `http://`s předponou .  

    >[!NOTE]
    > Není nutné povolit pravidlo. Je již spravován automaticky s přepsáním v sadě Management Pack Secure Reference Override společnosti Microsoft System Center Advisor, která se zaměřuje na skupinu monitorovacích serverů poradce microsoft system center.
    > 

1. Vyberte sadu Management Pack ze seznamu **Vybrat cílovou sadu Management Pack** nebo vytvořte novou nezapečetěnou sadu Management Pack výběrem možnosti **Nový**. 
1. Jakmile budete hotovi, vyberte **OK**. 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>Konfigurace pro hybridní pracovníky runbooku automatizace

Pokud máte automatizace hybridní runbook pracovníků ve vašem prostředí, postupujte podle následujících kroků k konfiguraci brány pro podporu pracovníků.

Adresa URL pro každou oblast naleznete v části [Konfigurace sítě](../../automation/automation-hybrid-runbook-worker.md#network-planning) v dokumentaci k automatizaci.

Pokud je váš počítač automaticky zaregistrován jako hybridní pracovník sady Runbook, například pokud je pro jeden nebo více virtuálních počítačů povoleno řešení správy aktualizací, postupujte takto:

1. Přidejte adresy URL datové služby za běhu úlohy do seznamu Povolený hostitel v bráně Log Analytics. Příklad: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Restartujte službu brány Log Analytics pomocí následující rutiny prostředí PowerShell:`Restart-Service OMSGatewayService`

Pokud je váš počítač připojen k Azure Automation pomocí rutiny registrace hybridního runbooku worker, postupujte takto:

1. Přidejte adresu URL registrace služby agenta do seznamu Povolený hostitel v bráně Log Analytics. Příklad: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. Přidejte adresy URL datové služby za běhu úlohy do seznamu Povolený hostitel v bráně Log Analytics. Příklad: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Restartujte službu brány Log Analytics.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Užitečné rutiny prostředí PowerShell

Pomocí rutin můžete dokončit úkoly k aktualizaci nastavení konfigurace brány Log Analytics. Před použitím rutin nezapomeňte:

1. Nainstalujte bránu Log Analytics (Instalační služba systému Microsoft Windows).
1. Otevřete okno konzoly PowerShellu.
1. Importujte modul zadáním tohoto příkazu:`Import-Module OMSGateway`
1. Pokud v předchozím kroku nedošlo k žádné chybě, modul byl úspěšně importován a lze je použít. Zadejte`Get-Module OMSGateway`
1. Po použití rutiny provést změny, restartujte službu OMS Gateway.

Chyba v kroku 3 znamená, že modul nebyl importován. K chybě může dojít, když prostředí PowerShell nemůže modul najít. Modul naleznete v instalační cestě brány OMS: *C:\Program Files\Microsoft OMS Gateway\PowerShell\OmsGateway*.

| **Rutina** | **Parametry** | **Popis** | **Příklad** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Klíč |Získá konfiguraci služby |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Klíč (povinné) <br> Hodnota |Změní konfiguraci služby. |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Získá adresu relé (upstream) proxy |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Adresa<br> Uživatelské jméno<br> Heslo (zabezpečený řetězec) |Nastaví adresu (a pověření) relé (upstream) proxy |1. Nastavte proxy relé a pověření:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Nastavte relé proxy, který nepotřebuje ověřování:`Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Zrušte nastavení proxy relé:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Získá aktuálně povoleného hostitele (pouze místně nakonfigurovaný povolený hostitel, ne automaticky stažený povolený hostitelům) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Hostitel (povinné) |Přidá hostitele do seznamu povolených. |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Hostitel (povinné) |Odebere hostitele ze seznamu povolených. |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Předmět (povinné) |Přidá klientský certifikát jako subjekt do seznamu povolených. |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Předmět (povinné) |Odebere subjekt klientského certifikátu ze seznamu povolených. |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Získá aktuálně povolené předměty klientského certifikátu (pouze místně nakonfigurované povolené předměty, ne automaticky stažené povolené předměty) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Řešení potíží

Chcete-li shromažďovat události zaznamenané bránou, měli byste mít nainstalovaný agent a analýzu protokolů.

![Snímek obrazovky se seznamem Prohlížeč událostí v protokolu brány Log Analytics](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>ID a popisy událostí brány Log Analytics

V následující tabulce jsou uvedeny ID událostí a popisy událostí protokolu Analytics brány protokolu protokolu.

| **Id** | **Popis** |
| --- | --- |
| 400 |Jakákoli chyba aplikace, která nemá žádné konkrétní ID. |
| 401 |Špatná konfigurace. Například listenPort = "text" místo celé číslo. |
| 402 |Výjimka v analýzě zpráv tls handshake. |
| 403 |Chyba sítě. Nelze se například připojit k cílovému serveru. |
| 100 |Obecné informace. |
| 101 |Služba byla spuštěna. |
| 102 |Služba byla zastavena. |
| 103 |Z klienta byl přijat příkaz HTTP CONNECT. |
| 104 |Není příkaz HTTP CONNECT. |
| 105 |Cílový server není v seznamu povolených nebo cílový port není zabezpečený (443). <br> <br> Ujistěte se, že agent MMA na serveru brány OMS a agenti, kteří komunikují s bránou OMS, jsou připojeni ke stejnému pracovnímu prostoru Log Analytics. |
| 105 |Chyba TcpConnection – neplatný klientský certifikát: CN=Gateway. <br><br> Ujistěte se, že používáte bránu OMS verze 1.0.395.0 nebo vyšší. Také se ujistěte, že agent MMA na serveru brány OMS a agenti komunikující s bránou OMS jsou připojeni ke stejnému pracovnímu prostoru Log Analytics. |
| 106 |Nepodporovaná verze protokolu TLS/SSL.<br><br> Brána Log Analytics podporuje pouze TLS 1.0, TLS 1.1 a 1.2. Nepodporuje SSL.|
| 107 |Relace TLS byla ověřena. |

### <a name="performance-counters-to-collect"></a>Čítače výkonu pro sběr

V následující tabulce jsou uvedeny čítače výkonu, které jsou k dispozici pro bránu Log Analytics. Pomocí sledování výkonu přidejte čítače.

| **Název** | **Popis** |
| --- | --- |
| Brána log analytics/Aktivní připojení klienta |Počet aktivních připojení klientské sítě (TCP) |
| Počet bran/chyb služby Log Analytics |Počet chyb |
| Brána log analytics/připojený klient |Počet připojených klientů |
| Počet brány/odmítnutí analýzy protokolů |Počet odmítnutí z důvodu chyby ověření TLS |

![Snímek obrazovky s rozhraním brány Log Analytics zobrazujícíčí čítače výkonu](./media/gateway/counters.png)

## <a name="assistance"></a>Asistence

Když jste přihlášení k portálu Azure, můžete získat pomoc s bránou Log Analytics nebo jakoukoli jinou službou nebo funkcí Azure.
Chcete-li získat nápovědu, vyberte ikonu otazníku v pravém horním rohu portálu a vyberte **Nový požadavek na podporu**. Poté vyplňte nový formulář žádosti o podporu.

![Snímek obrazovky s novou žádostí o podporu](./media/gateway/support.png)

## <a name="next-steps"></a>Další kroky

[Přidejte zdroje dat](../../azure-monitor/platform/agent-data-sources.md) pro shromažďování dat z připojených zdrojů a ukládejte je do pracovního prostoru Log Analytics.
