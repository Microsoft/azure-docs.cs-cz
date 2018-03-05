---
title: "Datacenter Obecné aspekty integrace pro Azure zásobníku integrované systémy | Microsoft Docs"
description: "Zjistěte, co můžete dělat teď plánování a přípravu integrace datového centra s několika uzly Azure zásobníku."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 25ef6ba9ff105486f39cee8b6181a8c63e64ec13
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
# <a name="datacenter-integration-considerations-for-azure-stack-integrated-systems"></a>Aspekty Datacenter integrace pro Azure zásobníku integrované systémy
Pokud byste chtěli v systému Azure zásobníku integrované, byste měli porozumět některé hlavní informace o plánování řešení nasazení a jak systému zapadá do vašeho datového centra. Tento článek obsahuje přehled těchto aspektů, který vám pomůže provádět rozhodnutí důležité infrastruktury pro systém Azure zásobníku více uzly. Pochopení těchto aspektů pomáhá při práci s dodavatele hardwaru, od výrobců OEM jako jejich nasazení do vašeho datového centra Azure zásobníku.  

> [!NOTE]
> Azure systémech s více uzly zásobníku lze pouze zakoupit od dodavatelů povolený hardware. 

K nasazení Azure zásobníku, potřebujete poskytovat informace o plánování pro poskytovatele řešení, před zahájením nasazení pomohou proces přejděte rychle a bez problémů. Informace požadované rozsahy napříč sítí, zabezpečení a informace o identitě s mnoha důležitých rozhodnutí, které mohou vyžadovat znalost z mnoha různých oblastech a vedoucím pracovníkům. Proto můžete chtít stáhnout osoby z více týmy ve vaší organizaci k zajištění, že máte všechny požadované informace, které jsou připravené před zahájením nasazení. Obraťte se na dodavatele hardwaru, při shromažďování těchto informací, protože mohou mít užitečné vaše rozhodování Rady, jak může pomoct.

Analýza a shromažďování požadované informace, možná budete muset udělat nějaké změny konfigurace před nasazením do prostředí vaší sítě. To může zahrnovat rezervování adresní prostory IP adres pro řešení Azure zásobníku, konfigurace směrovače, přepínače a brány firewall, aby příprava pro připojení k nové řešení přepínače zásobník Azure. Ujistěte se, že máte expert oblasti subjektu leží až, vám pomohou s plánování.

## <a name="capacity-planning-considerations"></a>Informace o plánování kapacity
Při posuzování o Azure zásobníku řešení pro získávání, musí být provedeny možnostmi konfigurace hardwaru, které mají přímý vliv na celkové kapacity své řešení Azure zásobníku. Mezi ně patří classic Výběr procesoru, paměti hustotu, konfigurace úložiště a škálování celkového řešení (například počet serverů). Na rozdíl od tradičních virtualizace řešení nevztahuje jednoduché aritmetické operace tyto součásti k určení použitelné kapacity. Prvním důvodem je, že zásobník Azure je navržen pro hostování součásti infrastruktury nebo správu v rámci řešení sám sebe. Druhý důvodem je skutečnost, že některé kapacity na řešení je vyhrazen na podporu odolnosti; aktualizace softwaru na řešení tak, aby minimalizuje narušení úlohy klientů. 

[Tabulky Plánovač kapacity zásobník Azure](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) pomáhá provedete informován rozhodnutí ohledně plánování kapacity dvěma způsoby: buď výběrem nabídku hardwaru a pokus o kombinaci prostředků nebo přizpůsobit tak, že definujete zatížení, které Azure zásobníku se má spustit zobrazíte dostupného hardwaru SKU, které ji podporují. Nakonec tabulku slouží jako vodítko při přijímání rozhodnutí související s Azure zásobník plánování a konfigurace. 

Tabulku není určeno k sloužit jako náhrada pro zkoumání a analýzu.  Společnost Microsoft neposkytuje žádná vyjádření či záruky, vyjádřené nebo předpokládané, s ohledem na informace uvedené v tabulce.



## <a name="management-considerations"></a>Aspekty správy
Azure zásobník je zapečetěné systému, kde infrastruktury je uzamčené z oprávnění i a síťový perspektivy. Sítě seznamy řízení přístupu (ACL) se použijí pro blokovat veškerý příchozí provoz neoprávněným a všechny zbytečné komunikace mezi součástmi infrastruktury. Díky tomu je obtížné neoprávněným uživatelům přístup k systému.

Pro každodenní správu a operace neexistuje žádný správce neomezený přístup k infrastruktuře. Operátory Azure zásobníku musí spravovat systém prostřednictvím portálu správce nebo prostřednictvím Správce Azure Resource Manager (prostřednictvím prostředí PowerShell nebo rozhraní REST API). Není k dispozici přístup k systému pomocí jiné nástroje pro správu, například Správce technologie Hyper-V nebo Správce clusteru převzetí služeb při selhání. K ochraně systému, nelze nainstalovat software jiných výrobců (například agenty) uvnitř součástí infrastruktury Azure zásobníku. Vzájemná funkční spolupráce s externí správu a zabezpečení softwaru dojde k pomocí prostředí PowerShell nebo rozhraní REST API.

Vyšší úroveň přístupu je potřeba pro řešení potíží, které nejsou přeložit prostřednictvím výstrahy zprostředkování kroky, musíte spolupracovat s Microsoft Support. Prostřednictvím podpory je metoda zajistit dočasný správce s úplnými oprávněními přístup k systému provádět pokročilejší operace. 

## <a name="identity-considerations"></a>Aspekty identity

### <a name="choose-identity-provider"></a>Vyberte poskytovatele správy identit
Musíte vzít v úvahu identity poskytovatele, kterého chcete použít pro nasazení zásobník Azure, Azure AD ani AD FS. Po nasazení bez opětovného nasazení úplnou nelze přepnout poskytovatelů identit.

Svou volbu zprostředkovatele identity nemá žádný vliv na klientské virtuální počítače, identitu systému a účty, které používají, zda bylo možné připojit doméně služby Active Directory atd. Toto je samostatný.

Další informace o výběru zprostředkovatele identity v [zásobník Azure integrované systémy připojení modely článku](.\azure-stack-connection-models.md).

### <a name="ad-fs-and-graph-integration"></a>Integrace služby AD FS a graf
Pokud se rozhodnete nasadit zásobník Azure pomocí služby AD FS jako zprostředkovatele identity, musíte integrovat instanci služby AD FS v zásobníku Azure s existující instancí služby AD FS přes důvěryhodnost federace. To umožňuje identit v existující doménové struktury služby Active Directory k ověření s prostředky v Azure zásobníku.

Můžete také integrovat službu grafu v zásobníku Azure s existující služby Active Directory. To umožňuje spravovat na základě rolí řízení přístupu (RBAC) v Azure zásobníku. Když je delegovaný přístup k prostředku, vyhledá komponentu grafu uživatelský účet v existující doménové struktuře služby Active Directory pomocí protokolu LDAP.

Následující diagram znázorňuje integrované služby AD FS a graf tok přenosů.
![Diagram zobrazuje tok přenosů služby AD FS a graf](media/azure-stack-datacenter-integration/ADFSIntegration.PNG)

## <a name="licensing-model"></a>Model správy licencí
Musíte rozhodnout, které licenčním modelu, který chcete použít. Dostupné možnosti závisí na, jestli nasazení Azure zásobníku připojení k Internetu:
- Pro [připojené nasazení](azure-stack-connected-deployment.md), můžete zvolit platím jako--používání nebo na základě kapacity licencování. Platím jako--používání vyžaduje připojení k Azure sestavy využití, které se pak fakturuje prostřednictvím Azure commerce. 
- Licencování pouze na základě kapacity jsou podporované, když jste [nasazení odpojení](azure-stack-disconnected-deployment.md) z Internetu. 

Další informace o licencování modelech najdete v tématu [Microsoft Azure zásobníku balení a ceny](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).


## <a name="naming-decisions"></a>Pojmenování rozhodnutí

Budete muset myslíte o tom, jak chcete naplánujte zásobník Azure obor názvů, zejména název oblast a název externí domény. Externí název plně kvalifikované domény (FQDN) nasazení Azure zásobníku pro veřejné koncové body se rozumí kombinace těchto dvou názvy: &lt; *oblast*&gt;.&lt; *plně kvalifikovaný název domény*&gt;. Například *east.cloud.fabrikam.com*. V tomto příkladu bude k dispozici na následující adresy URL portálů zásobník Azure:

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

> [!IMPORTANT]
> Název oblasti, které zvolíte pro vaše nasazení zásobník Azure musí být jedinečný a zobrazí se v portálu adresy. 

Následující tabulka shrnuje tato rozhodnutí pojmenování domény.

| Název | Popis | 
| -------- | ------------- | 
|Název oblasti | Název vaší první oblasti Azure zásobníku. Tento název se používá jako součást plně kvalifikovaný název domény pro veřejnou virtuální IP adresy (VIP) se spravuje zásobník Azure. Název oblasti obvykle, bude identifikátor fyzické umístění, jako je například umístění datového centra. | 
| Název externí domény | Název zóny, systému DNS (Domain Name) pro koncové body pomocí externího virtuální IP adresy. Použít ve plně kvalifikovaný název domény pro tyto veřejné virtuální IP adresy. | 
| Název domény privátní (interní) | Název domény (a interní DNS zóny) vytvořit v zásobníku Azure pro správu infrastruktury. 
| | |

## <a name="certificate-requirements"></a>Požadavky na certifikát

Pro nasazení budete potřebovat zajistit certifikáty, Secure Sockets Layer (SSL) pro veřejné koncové body. Certifikáty na vysoké úrovni, mají tyto požadavky:

- Můžete použít certifikát se zástupným znakem jednoho nebo můžete použít sadu vyhrazené certifikáty a použít zástupné znaky pouze pro koncové body, jako je například úložiště a Key Vault.
- Certifikáty, může být vydaný veřejnou důvěryhodné certifikační autority (CA) nebo spravované zákazníkem certifikační Autority.

Další informace o jaké infrastruktury veřejných KLÍČŮ jsou certifikáty potřebné k nasazení zásobník Azure a získat je najdete v tématu, [požadavky na certifikáty infrastruktury veřejných klíčů Azure zásobníku](azure-stack-pki-certs.md).  


> [!IMPORTANT]
> Zadané informace o certifikátu PKI slouží jako obecné pokyny. Než získáte všechny certifikáty infrastruktury veřejných KLÍČŮ pro Azure zásobníku, spolupracovat s partnerským hardwaru výrobce OEM. Zadá podrobnější pokyny certifikátu a požadavky.


## <a name="time-synchronization"></a>Synchronizaci času
Musíte vybrat určitý čas, který server s se používá k synchronizaci Azure zásobníku.  Čas symbolization je velmi důležité zásobník Azure a její role infrastruktury, jako se používá ke generování lístky protokolu Kerberos, které se používají k ověření interních služeb mezi sebou.

Je nutné zadat, že IP adresy pro čas synchronizace serveru, i když většina komponenty v infrastruktuře můžete vyřešit adresu URL, některé podporuje pouze IP adresy. Pokud jste se používá možnost odpojené nasazení, je nutné zadat čas serveru ve vaší podnikové síti, které jste opravdu dosažitelný z infrastruktury sítě v Azure zásobníku.

## <a name="connect-azure-stack-to-azure"></a>Připojit k Azure Azure zásobníku

U scénářů s hybridní cloudové budete muset naplánovat, jak se chcete připojit zásobník Azure do Azure. Existují dvě podporované metody pro připojení virtuálních sítí v Azure zásobníku virtuálních sítí v Azure: 
- **Site-to-site**. Připojení virtuální privátní sítě (VPN) prostřednictvím protokolu IPsec (IKE v1 a IKE v2). Tento typ připojení vyžaduje zařízení VPN nebo směrování a vzdálený přístup (RRAS). Další informace o branách VPN v Azure najdete v tématu [o službě VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Komunikace přes tento tunel je šifrovaný a je bezpečné. Ale je omezena šířka pásma maximální propustnost tunelového propojení (100 – 200 MB/s).
- **Odchozí NAT**. Ve výchozím nastavení budou všechny virtuální počítače v Azure zásobníku mít připojení k externím sítím přes odchozí adres (NAT) Každý virtuální sítě, který je vytvořen v zásobníku Azure získá přiřazen veřejnou IP adresu. Ať už virtuální počítač je přímo přiřazenou veřejnou IP adresu, nebo je za službou Vyrovnávání zatížení s veřejnou IP adresu, bude mít odchozí přístup přes odchozí NAT pomocí virtuální IP adresu ve virtuální síti. Tento postup funguje pouze pro komunikaci, která se spouští virtuální počítač a je určený pro externí sítě (Internetu nebo intranetu). Nelze se používá ke komunikaci s virtuálním počítačem mimo.

### <a name="hybrid-connectivity-options"></a>Možnosti hybridní připojení

Hybridní připojení je důležité vzít v úvahu, jaký typ nasazení, můžete chtít nabízet a kde bude nasazen. Budete muset zvážit, jestli chcete izolovat síťový provoz na každého klienta, a jestli budete mít k intranetu nebo Internetu nasazení.

- **Jednoho klienta Azure zásobníku**. Nasazení zásobník Azure, která vypadá, alespoň z hlediska sítě, pokud je jeden klienta. Může být, že mnoho klientů odběry, ale stejně jako služby intranetu, veškerý provoz přenáší přes stejné sítě. Síťový provoz z jednoho předplatného přenáší přes síťové připojení stejné jako jiné předplatné a nemusí být izolované přes šifrované tunelové propojení.

- **Zásobník Azure víceklientské**. Nasazení služby Azure zásobníku, kde musí být každého klienta předplatného provoz, který je vázaný sítě, které jsou externí vzhledem k Azure zásobníku izolované od ostatních klientů síťový provoz.
 
- **Nasazení v síti intranet**. Nasazení služby Azure zásobníku, která se nachází v podnikovém intranetu, obvykle na prostor privátní IP adresy a za jeden nebo více bran firewall. Veřejné IP adresy nejsou skutečně veřejné, protože nedá se trasovat přímo prostřednictvím veřejného Internetu.

- **Nasazení Internet**. Nasazení služby zásobník Azure, která je připojena k veřejnosti Internetu a používá směrovatelných v Internetu veřejné IP adresy pro veřejný rozsah virtuálních IP adres. Nasazení můžete stále nacházejí za bránou firewall, ale veřejné rozsah virtuálních IP adres je přímo dosažitelný z veřejného Internetu a Azure.
 
Následující tabulka shrnuje hybridní scénáře připojení s výhody, nevýhody a případy použití.

| Scénář | Metoda připojení | Odborníci na | Nevýhody | Je vhodný pro |
| -- | -- | --| -- | --|
| Jeden klient Azure zásobník nasazení v síti intranet | Odchozí NAT | Lepší šířku pásma pro přenosy na rychlejší. Jednoduchá implementace; nemá žádné brány vyžaduje. | Provoz nejsou šifrovaná; žádná izolace nebo šifrování nad rámec TOR. | Podnikové nasazení, kde jsou rovnoměrně důvěryhodné všichni klienti.<br><br>Podnikům, které mají okruh Azure ExpressRoute do Azure. |
| Víceklientské Azure zásobník nasazení v síti intranet | Site-to-site VPN | Je bezpečné provoz z virtuální sítě klienta do cílové. | Tunelové propojení VPN typu site-to-site je omezena šířka pásma.<br><br>Vyžaduje bránu virtuální sítě a zařízení v cílové síti VPN. | Musí být zabezpečená podnikového nasazení, kde některé přenosy dat klientů od ostatních klientů. |
| Jeden klient Azure zásobník internet nasazení | Odchozí NAT | Lepší šířku pásma pro přenosy na rychlejší. | Provoz nejsou šifrovaná; žádná izolace nebo šifrování nad rámec TOR. | Hostování scénáře, kde klient získá vlastní nasazení Azure zásobníku a vyhrazené okruhu do prostředí Azure zásobníku. Například ExpressRoute a Multiprotocol Label přepínání (MPLS).
| Víceklientské Azure zásobník internet nasazení | Site-to-site VPN | Je bezpečné provoz z virtuální sítě klienta do cílové. | Tunelové propojení VPN typu site-to-site je omezena šířka pásma.<br><br>Vyžaduje bránu virtuální sítě a zařízení v cílové síti VPN. | Scénáře, kde chce víceklientská Cloudová nabízejí poskytovatele hostování, kde není klienti důvěřovat navzájem a přenosy dat musí být zašifrován.
|  |  |  |  |  |

### <a name="using-expressroute"></a>Pomocí služby ExpressRoute

Zásobník Azure můžete připojit k Azure přes [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) pro jednoho klienta intranetu i víceklientské scénáře. Budete potřebovat zřízené okruh ExpressRoute prostřednictvím [poskytovatele připojení](https://docs.microsoft.com/azure/expressroute/expressroute-locations).

Následující diagram znázorňuje ExpressRoute pro jednoho klienta scénář (kde "Zákazníka připojení" je okruh ExpressRoute).

![Diagram znázorňující jednoho klienta ExpressRoute scénář](media/azure-stack-datacenter-integration/ExpressRouteSingleTenant.PNG)

Následující diagram znázorňuje ExpressRoute pro scénář více klientů.

![Diagram znázorňující víceklientské ExpressRoute scénář](media/azure-stack-datacenter-integration/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>Externí monitorování
Získat jediné zobrazení všech výstrah ze zařízení a nasazení Azure zásobníku a integrovat do existujících IT služby pracovních postupů správy pro zpracování lístků výstrahy, můžete [integrovat externí datacenter sledování řešeníAzurezásobníku](azure-stack-integrate-monitor.md).

Je součástí řešení Azure zásobníku, hostitele životního cyklu hardwaru počítače mimo Azure zásobníku, který spouští nástroje pro správu poskytuje výrobce OEM pro hardware. Můžete použít tyto nástroje nebo jiná řešení, které přímo integrovat existující řešení monitorování ve vašem datovém centru.

Následující tabulka shrnuje seznam aktuálně dostupných možností.

| Oblast | Externí řešení monitorování |
| -- | -- |
| Azure softwaru zásobníku | [Azure zásobníku sady Management Pack pro nástroj Operations Manager](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>[Modul plug-in Nagios](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>Na základě REST API – volání | 
| Fyzické servery (přes rozhraní IPMI BMC) | Hardware OEM – sada management pack Operations Manager dodavatele<br>Řešení poskytuje dodavatel hardwaru výrobce OEM<br>Dodavatele hardwaru Nagios moduly plug-in | Výrobce OEM partnera podporováno monitorování řešení (součást) | 
| Síťová zařízení (SNMP) | Zjišťování síťových zařízení Operations Manager<br>Řešení poskytuje dodavatel hardwaru výrobce OEM<br>Přepínač Nagios modulu plug-in |
| Sledování stavu předplatného klienta | [Sada System Center Management Pack pro systém Windows Azure](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

Vezměte na vědomí následující požadavky:
- Řešení, které používáte, musí být bez agenta. Nelze instalovat agenty třetích stran uvnitř součásti zásobníku Azure. 
- Pokud chcete použít System Center Operations Manager, je třeba nástroje Operations Manager 2012 R2 nebo Operations Manager 2016.

## <a name="backup-and-disaster-recovery"></a>Zálohování a zotavení po havárii

Plánování zálohování a zotavení po havárii zahrnuje plánování pro obě základní zásobník Azure infrastrukturu, která hostuje virtuální počítače IaaS a PaaS služby a pro klienta aplikace a data. Musíte naplánovat pro tyto samostatně.

### <a name="protect-infrastructure-components"></a>Chránit součásti infrastruktury

Můžete [zálohování Azure zásobníku](azure-stack-backup-back-up-azure-stack.md) sdílet součásti infrastruktury pro SMB, zadejte:

- Budete potřebovat externí sdílené složce SMB na existující server systému Windows soubor nebo zařízení třetích stran.
- Měli byste použít této stejné sdílené složce pro zálohování síťové přepínače a životního cyklu hostitele hardwaru. Dodavatele hardwaru, od výrobců OEM pomůže poskytnout pokyny pro zálohování a obnovení všechny tyto komponenty jsou tyto externí do protokolů Azure. Jste zodpovědná za spuštění zálohování pracovních postupů na základě doporučení výrobce OEM.

Pokud dojde k závažné ztráty dat, můžete použít infrastrukturu zálohy dat nasazení opakovaným přidáním jako certifikát kořenové certifikační Autority nasazení pro vstupy a identifikátory, účty služby, federované prostředky (v odpojených nasazení), plánů, nabídky odběry, kvóty, RBAC roli zásady a přiřazení a tajné klíče Key Vault.
 
### <a name="protect-tenant-applications-on-iaas-virtual-machines"></a>Chránit aplikace od klienta na virtuální počítače IaaS

Azure zásobníku nezálohuje až klienta aplikace a data. Je nutné naplánovat zálohování a po havárii obnovení ochrany na cíl, externí do protokolů Azure. Ochranu klienta je aktivita řízené klienta. Pro virtuální počítače IaaS můžete klientů používat technologie v hosta k ochraně souborů, složek, data aplikací a stavu systému. Jako poskytovatel enterprise nebo službě, můžete však nabízí řešení zálohování a obnovení ve stejném datovém centru nebo externě v cloudu.

Zálohování virtuálních počítačů Linux nebo IaaS ve Windows, musíte použít zálohování produktů s přístupem k hostovaného operačního systému k ochraně souboru, složky, stav operačního systému a dat aplikací. Zálohování Azure, System Center Data Center Protection Manager, můžete použít nebo podporovány produkty třetích stran.

Pokud chcete replikovat data do sekundárního umístění a orchestraci převzetí služeb při selhání aplikace, pokud dojde k havárii, můžete použít Azure Site Recovery nebo podporované produkty třetích stran. (Na počáteční verze integrované systémy Azure Site Recovery nebudou podporovat navrácení služeb po obnovení. Však můžete dosáhnout navrácení služeb po obnovení ruční proces.) Aplikace, které podporují nativní replikace (jako je Microsoft SQL Server) navíc můžete replikovat data do jiného umístění, kde je aplikace spuštěna.

> [!IMPORTANT]
> V původním vydáním integrované systémy podporujeme ochrany technologie, které fungují na úrovni hosta virtuálního počítače IaaS. Nelze instalovat agenty na základní servery infrastruktury.

## <a name="learn-more"></a>Další informace

- Informace o případy použití, nákup, partneři a OEM výrobci hardwaru najdete v tématu [zásobník Azure](https://azure.microsoft.com/overview/azure-stack/) stránky produktu.
- Informace o plán a geografická dostupnosti pro zásobník Azure integrované systémy, najdete v dokumentu white paper: [Azure zásobník: rozšíření Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 

## <a name="next-steps"></a>Další postup
[Azure modelů připojení nasazení zásobníku](azure-stack-connection-models.md)