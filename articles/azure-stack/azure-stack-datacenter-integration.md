---
title: Integrované systémy pro datacenter Obecné aspekty integrace pro Azure Stack | Dokumentace Microsoftu
description: Zjistěte, co můžete dělat nyní plánování a přípravu integrace datového centra pomocí služby Azure Stack víc uzlů.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: wfayed
ms.lastreviewed: 09/12/2018
ms.openlocfilehash: 5ececb2d3c52a1da8c1a537e6223f17a9b83921f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56207530"
---
# <a name="datacenter-integration-considerations-for-azure-stack-integrated-systems"></a>Důležité informace o integraci Datacenter pro integrované systémy Azure Stack
Pokud vás zajímá systémech pro Azure Stack integrované, měli byste porozumět hlavní rozhodnutí při plánování nasazení a jak se systém zapadá do vašeho datového centra. Tento článek obsahuje základní přehled o tyto aspekty, které pomůžou při rozhodování důležitou infrastrukturu pro váš systém Azure Stack víc uzlů. Znalost těchto aspektů pomáhá při práci s OEM dodavatele hardwaru, jako jsou nasazení do vašeho datového centra Azure Stack.  

> [!NOTE]
> Více uzly systémy pro Azure Stack si můžete koupit jenom od dodavatelů povolený hardware. 

Nasazení Azure Stack, budete muset před zahájením nasazení usnadňují proces přejděte rychle a plynule poskytovat informace o plánování poskytovatele řešení. Informace požadované rozsahy adres napříč sítě, zabezpečení a informace o identitě s mnoha důležitých rozhodnutí, které mohou vyžadovat znalosti z mnoha různých oblastech a manažeři s rozhodovací pravomocí. Proto budete muset vyžádat uživatelé z několika týmy ve vaší organizaci, abyste měli jistotu, že máte všechny požadované informace, které jsou připravené před zahájením nasazení. Může pomoct komunikovat s vaším dodavatelem hardwaru při shromažďování těchto informací může být užitečné při rozhodování o vaše Rady, jak mají.

Při zkoumání a shromažďují se potřebné informace, může být potřeba udělat nějaké změny konfigurace před nasazením do prostředí vaší sítě. To může zahrnovat rezervace adresní prostory IP adres pro řešení Azure Stack, konfigurace směrovače, přepínače a brány firewall pro přípravu připojení k nové přepínače řešení Azure Stack. Ujistěte se, že jste expert oblasti subjektu u až, vám pomůžou s plánování.

## <a name="capacity-planning-considerations"></a>Co zvážit při plánování kapacity
Při vyhodnocování řešení Azure Stack určené pro získání, možnosti konfigurace hardwaru se musí provádět které mají přímý vliv na celkové kapacity své řešení Azure Stack. Patří mezi ně classic volby procesoru, paměti hustota, konfigurace úložiště a celkové řešení škálování (například počet serverů). Na rozdíl od tradiční virtualizaci řešení jednoduché aritmetické operace těchto komponent k určení využitelné kapacity se nevztahuje. Prvním důvodem je, že Azure Stack je navržený pro hostování součásti infrastruktury nebo správu v rámci vlastním řešením. Druhý důvodem je, že některé z kapacity řešení nebylo vyhrazeno podporu odolnost; aktualizace softwaru řešení způsobem, který minimalizuje přerušení úlohy klientů. 

[Tabulky Plánovač kapacity Azure Stack](https://aka.ms/azstackcapacityplanner) pomáhá provedete informováni rozhodnutí ohledně plánování kapacity dvěma způsoby: buď výběrem nabídky hardwaru a pokusu přizpůsobit kombinace prostředků nebo tak, že definujete úlohy, které Azure Stack je určena pro spuštění zobrazíte dostupného hardwaru skladových položek, které může podporovat. Nakonec tabulky uvádí, které tento průvodce vám pomůžou dělat rozhodnutí související s Azure Stack plánování a konfigurace. 

Tabulka není určena pro sloužit jako náhradu pro zkoumání a analýzy.  Společnost Microsoft neposkytuje žádná vyjádření či záruky, vyjádřené nebo předpokládané, s ohledem na informace uvedené v tabulce.



## <a name="management-considerations"></a>Aspekty správy
Azure Stack je zapečetěná systému, kde infrastruktury je uzamčené i z oprávnění a síťové perspektivy. Sítě seznamy řízení přístupu (ACL) se použijí pro blokování všech neoprávněný příchozí provoz a všechny nepotřebné komunikaci mezi součástmi infrastruktury. Díky tomu je obtížné neoprávněným uživatelům přístup k systému.

Pro každodenní správu a provoz není k dispozici správce neomezený přístup k infrastruktuře. Operátoři Azure stacku musí spravovat systém prostřednictvím portálu správce nebo prostřednictvím Azure Resource Manageru (prostřednictvím Powershellu nebo rozhraní REST API). Neexistuje žádný přístup k systému – jiné nástroje pro správu, jako je například Správce technologie Hyper-V nebo Správce clusteru převzetí služeb při selhání. K ochraně systému, nelze nainstalovat software třetí strany (například agentů) uvnitř komponenty infrastruktury Azure stacku. Vzájemná funkční spolupráce s externí správu a zabezpečení softwaru dojde k prostřednictvím Powershellu nebo rozhraní REST API.

Vyšší úroveň přístupu je potřeba pro řešení potíží s problémy, které nejsou vyřešené výstrahy zprostředkování kroky, musí pracovat s Microsoft Support. Díky podpoře je metoda a zajistit tak dočasné správce s úplnými oprávněními přístup k systému provádět pokročilé operace. 

## <a name="identity-considerations"></a>Důležité informace o identity

### <a name="choose-identity-provider"></a>Vyberte zprostředkovatele identity
Musíte vzít v úvahu kterého zprostředkovatele identity, kterou chcete použít pro nasazení Azure Stack, Azure AD nebo AD FS. Po nasazení bez opětovného nasazení celé systému nelze přepnout zprostředkovatelé identity. Pokud nejste vlastníkem účtu služby Azure AD a používáte účet, které jste získali od poskytovatele cloudových služeb, a pokud se rozhodnete přepínání poskytovatele a jiné služby Azure AD pomocí účtu, v tomto okamžiku budete muset kontaktovat svého poskytovatele řešení k opětovnému nasazení řešení f nebo můžete na vaše náklady.

Zvoleného poskytovatele identity nemá žádný vliv na klientské virtuální počítače, identitu systému a účty, které používají, zda se můžete připojit doméně služby Active Directory atd. Toto je samostatný.

Další informace o výběru zprostředkovatele identity v [článku modelů připojení integrované systémy Azure Stack](./azure-stack-connection-models.md).

### <a name="ad-fs-and-graph-integration"></a>Integrace služby AD FS a graf
Pokud budete chtít nasadit Azure Stack pomocí služby AD FS jako zprostředkovatele identity, musíte integrovat instance služby AD FS v Azure stacku s existující instancí služby AD FS přes důvěryhodnost federace. To umožňuje identit v existující doménové struktury služby Active Directory k ověření pomocí prostředků ve službě Azure Stack.

Můžete také integrovat službu grafu ve službě Azure Stack s existující služby Active Directory. To umožňuje spravovat na základě rolí řízení přístupu (RBAC) v Azure stacku. Když se deleguje přístup k prostředku, komponenta grafu vyhledá uživatelský účet v existující doménové struktuře služby Active Directory pomocí protokolu LDAP.

Následující diagram znázorňuje integrované služby AD FS a grafu toku provozu.
![Diagram znázorňující tok provozu služby AD FS a grafu](media/azure-stack-datacenter-integration/ADFSIntegration.PNG)

## <a name="licensing-model"></a>Model licencování
Musíte rozhodnout, které licenčního modelu, který chcete použít. Dostupné možnosti závisí na Určuje, jestli nasazení Azure Stack připojený k Internetu:
- Pro [připojené nasazení](azure-stack-connected-deployment.md), můžete platit jako využití nebo založená na kapacitě licencování. Platba jako využití vyžaduje připojení k Azure pro využití sestav, které se pak fakturuje v rámci Azure commerce. 
- Pouze licencování na základě kapacity je podporován, když jste [nasazení odpojení](azure-stack-disconnected-deployment.md) z Internetu. 

Další informace o licenční modely najdete v tématu [Microsoft Azure Stack balení a ceny](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).


## <a name="naming-decisions"></a>Pojmenování rozhodnutí

Bude potřeba uvažovat o způsobu plánování oboru názvů služby Azure Stack, zejména název oblasti a název externí domény. Externí plně kvalifikovaný název domény (FQDN) nasazení Azure Stack pro veřejných koncových bodů je kombinací těchto dvou názvů: &lt; *oblasti*&gt;.&lt; *plně kvalifikovaný název domény*&gt;. Například *east.cloud.fabrikam.com*. V tomto příkladu bude k dispozici na na následující adresy URL na portálech Azure Stack:

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

> [!IMPORTANT]
> Název oblasti, kterou jste vybrali pro nasazení Azure Stack musí být jedinečný a zobrazí se v portálu adresy. 

Následující tabulka shrnuje tato rozhodnutí názvů domény.

| Název | Popis | 
| -------- | ------------- | 
|Název oblasti | Název vaší první oblasti Azure Stack. Tento název se používá jako součást plně kvalifikovaný název domény pro veřejnou virtuální IP adresy (VIP), které spravuje Azure Stack. Název oblasti obvykle by měl být identifikátor fyzické umístění, jako je například umístění datového centra.<br><br>Název oblasti musí obsahovat jenom písmena a číslice mezi 0 až 9. Žádné speciální znaky, například "-" nebo "#", atd. jsou povoleny.| 
| Název externí domény | Název zóny systému DNS (Domain Name) pro koncové body s externích virtuálních IP adres. Používá se v plně kvalifikovaný název domény pro tyto veřejné virtuální IP adresy. | 
| Název domény privátní (interní) | Název domény (a interní DNS zóny) vytvořené ve službě Azure Stack pro správu infrastruktury. 
| | |

## <a name="certificate-requirements"></a>Požadavky na certifikát

Pro nasazení budete muset poskytnout vrstvy SSL (Secure Sockets) certifikáty pro koncové body veřejnou. Na vysoké úrovni certifikáty mají následující požadavky:

- Můžete použít jeden zástupný znak certifikát nebo můžete použít sadu vyhrazených certifikáty a použít zástupné znaky pouze pro koncové body, jako jsou úložiště a služby Key Vault.
- Certifikáty můžou být vystavené důvěryhodné veřejné certifikační autority (CA) nebo spravované zákazníkem certifikační Autority.

Další informace o jaké infrastruktury veřejných KLÍČŮ certifikáty jsou nutné k nasazení Azure Stack a tom, jak je získat, najdete v článku, [požadavky na certifikáty infrastruktury veřejných klíčů v Azure stacku](azure-stack-pki-certs.md).  


> [!IMPORTANT]
> Zadané informace o certifikátu PKI by měla sloužit jako obecné pokyny. Než si koupíte jakékoli certifikáty infrastruktury veřejných KLÍČŮ pro Azure Stack, spolupráci s partnerem hardwaru výrobce OEM. Poskytnou podrobnější pokyny k certifikátu a požadavky.


## <a name="time-synchronization"></a>Čas synchronizace
Je nutné vybrat určitou dobu, kterou server s se používá k synchronizaci služby Azure Stack.  Synchronizaci času je důležité pro Azure Stack a jeho infrastruktury role, protože se používá ke generování lístky protokolu Kerberos, které se používají k ověření interních služeb mezi sebou.

Je nutné zadat, že integrační balíček pro server synchronizace času, i když většina komponent v infrastruktuře lze přeložit adresu URL, některé může podporovat jenom IP adresy. Pokud používáte možnost odpojené nasazení, musíte zadat, že čas v podnikové síti, kterou Opravdu se dá kontaktovat server ze sítě infrastruktury v Azure stacku.

## <a name="connect-azure-stack-to-azure"></a>Azure Stack připojení k Azure

U scénářů s hybridní cloud, musíte naplánovat, jak chcete pro připojení služby Azure Stack k Azure. Existují dvě podporované metody k propojení virtuálních sítí ve službě Azure Stack k virtuálním sítím v Azure: 
- **Site-to-site**. Připojení virtuální privátní sítě (VPN) prostřednictvím protokolu IPsec (IKE v1 a IKE v2). Tento typ připojení vyžaduje zařízení VPN nebo směrování a vzdálený přístup (RRAS). Další informace o VPN Gateway v Azure najdete v tématu [informace o službě VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Komunikace přes tento tunel je šifrovaný a je bezpečné. Šířka pásma je však omezená maximální propustnost tunelového propojení (100 – 200 MB/s).
- **Odchozí NAT**. Ve výchozím nastavení všechny virtuální počítače ve službě Azure Stack, budou mít připojení k externím sítím přes odchozí NAT. Každá virtuální síť, která se vytvoří ve službě Azure Stack získá přiřazenou k němu veřejnou IP adresu. Virtuální počítač je přímo přiřazena veřejná IP adresa nebo je za nástroj pro vyrovnávání zatížení s veřejnou IP adresu, bude mít odchozí přístup přes odchozí NAT pomocí virtuální IP adresu ve virtuální síti. Tento postup funguje pouze pro komunikaci, která je zahájeno virtuálním počítačem a je určený pro sítě externí (internet nebo intranet). Nelze použít ke komunikaci s virtuálním počítačem mimo.

### <a name="hybrid-connectivity-options"></a>Hybridní možnosti připojení

Hybridní připojení je důležité vzít v úvahu, jaký typ nasazení, které chcete nabízet a kam se nasadí. Musíte vzít v úvahu, jestli chcete izolovat síťový provoz na tenanta a určuje, zda bude nutné při nasazování intranetu nebo Internetu.

- **Azure Stack s jedním tenantem**. Nasazení služby Azure Stack, která vypadá, alespoň z hlediska sítě, jako by byl jednoho tenanta. Může být, že mnoho klientů předplatných, ale stejně jako služby intranetu, veškerý provoz bude procházet přes stejné sítě. Síťový provoz z jednoho předplatného bude procházet přes stejné připojení jako jiné předplatné a nemusí být izolované prostřednictvím šifrovaného tunelu.

- **Azure Stack s více tenanty**. Nasazení služby Azure Stack, kde musí být izolované od ostatních tenantů síťové přenosy tenanta předplatného provoz, který je vázaný sítě, které jsou externí vzhledem k Azure Stack.
 
- **Nasazení v síti intranet**. Nasazení služby Azure Stack, který je umístěný v podnikové síti intranet, obvykle v privátní adresní prostor IP adres a za jeden nebo více bran firewall. Veřejné IP adresy nejsou skutečně veřejné, protože nemůže být směrován přímo přes veřejný internet.

- **Nasazení Internet**. Nasazení služby Azure Stack, který je připojený k veřejného Internetu a používá směrovatelných v Internetu veřejné IP adresy pro veřejný rozsah virtuálních IP adres. Nasazení se můžete stále nacházejí za bránou firewall, ale veřejné rozsah virtuálních IP adres je přímo dostupné z veřejného Internetu a z Azure.
 
Následující tabulka shrnuje scénáře hybridních připojení, s profesionály v oboru, nevýhody a případy použití.

| Scénář | Připojení – metoda | V oblasti IT | Nevýhody | Vhodné pro |
| -- | -- | --| -- | --|
| Jeden tenant služby Azure Stack, nasazení v síti intranet | Odchozí NAT. | Lepší šířku pásma pro přenosy rychlejší. Jednoduchá implementace; není vyžadováno žádné brány. | Provoz nejsou šifrovaná; žádná izolace nebo šifrování mimo zásobníku. | Podnikové nasazení, kde jsou všechny tenanty stejně důvěryhodné.<br><br>Podniky, které mají okruh Azure ExpressRoute pro Azure. |
| Více tenantů Azure Stack, nasazení v síti intranet | Site-to-site VPN | Provoz z virtuální sítě tenanta do cíle je zabezpečená. | Šířka pásma je omezená tunelového připojení sítě VPN site-to-site.<br><br>Vyžaduje bránu virtuální sítě a zařízení sítě VPN v cílové síti. | Musí být zabezpečená podniková nasazení, kde některé přenosy dat klientů z jiných tenantů. |
| Jeden tenant služby Azure Stack, internet nasazení | Odchozí NAT. | Lepší šířku pásma pro přenosy rychlejší. | Provoz nejsou šifrovaná; žádná izolace nebo šifrování mimo zásobníku. | Hostování scénáře, ve kterém tenant získá vlastní nasazení Azure stacku a vyhrazeného okruhu do prostředí Azure Stack. Například ExpressRoute a Multiprotocol Label přepínání (MPLS).
| Více tenantů Azure Stack, internet nasazení | Site-to-site VPN | Provoz z virtuální sítě tenanta do cíle je zabezpečená. | Šířka pásma je omezená tunelového připojení sítě VPN site-to-site.<br><br>Vyžaduje bránu virtuální sítě a zařízení sítě VPN v cílové síti. | Scénáře, kdy chce nabízí Cloudová víceklientská poskytovatele hostování, ve kterém klienti nedůvěřovat mezi sebou a provozu musí být zašifrován.
|  |  |  |  |  |

### <a name="using-expressroute"></a>Pomocí ExpressRoute

Azure Stack se můžete připojit k Azure prostřednictvím [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) pro jednoho tenanta intranetu a scénářích s více tenanty. Budete potřebovat zřízené okruh ExpressRoute prostřednictvím [poskytovatelem připojení](https://docs.microsoft.com/azure/expressroute/expressroute-locations).

Následující diagram znázorňuje ExpressRoute pro jednoho tenanta scénář (kde ""zákazníka připojení je okruh ExpressRoute).

![Diagram znázorňující jednoho tenanta ExpressRoute scénář](media/azure-stack-datacenter-integration/ExpressRouteSingleTenant.PNG)

Následující diagram znázorňuje ExpressRoute pro scénářích s více tenanty.

![Diagram znázorňující víceklientské ExpressRoute scénář](media/azure-stack-datacenter-integration/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>Externí monitorování
Chcete-li získat jednotné zobrazení všech výstrah ze zařízení a nasazení Azure stacku a integrujte upozornění do existujících IT služby pracovních postupů správy pro vytváření tiketů, můžete [Azure Stack integrujte monitorování řešeníexterníchdatacenter](azure-stack-integrate-monitor.md).

Je součástí řešení Azure Stack, životní cyklus hostitelský hardware počítače mimo Azure Stack, na kterém běží OEM nástroje pro správu poskytnutých dodavatelem hardwaru. Můžete použít tyto nástroje nebo jiných řešeních, která se integrují přímo se stávajícími řešeními monitorování ve vašem datovém centru.

Následující tabulka shrnuje seznam aktuálně dostupných možností.

| Oblast | Externí řešení monitorování |
| -- | -- |
| Azure Stack software | [Azure Stack sady Management Pack pro nástroj Operations Manager](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>[Modul plug-in Nagios](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>Volání rozhraní API založených na REST | 
| Fyzické servery (pro správu základní desky přes rozhraní IPMI) | Výrobce OEM hardware - sady management pack Operations Manageru dodavatele<br>Výrobce OEM hardwaru poskytnutých dodavatelem řešení<br>Dodavatel hardwaru Nagios moduly plug-in | Výrobce OEM partnera podporováno monitorování řešení (zahrnout) | 
| Síťová zařízení (SNMP) | Zjišťování síťových zařízení pomocí Operations Manageru<br>Výrobce OEM hardwaru poskytnutých dodavatelem řešení<br>Přepnout modul plug-in Nagios |
| Monitorování stavu předplatného tenanta | [System Center Management Pack pro Windows Azure](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

Vezměte na vědomí následující požadavky:
- Řešení, které používáte, musí být bez agentů. Nemůžete instalovat agenty třetích stran v Azure stacku komponenty. 
- Pokud chcete použít System Center Operations Manager, Operations Manager 2012 R2 nebo Operations Manageru 2016 je vyžadována.

## <a name="backup-and-disaster-recovery"></a>Zálohování a zotavení po havárii

Plánování zálohování a zotavení po havárii zahrnuje plánování pro obě základní infrastruktury služby Azure Stack, který hostuje virtuální počítače IaaS a PaaS služby a pro klienta aplikace a data. Je nutné naplánovat odděleně pro tyto.

### <a name="protect-infrastructure-components"></a>Ochrana komponent infrastruktury

Je možné [zálohování Azure stacku](azure-stack-backup-back-up-azure-stack.md) součásti infrastruktury pro SMB sdílejí, že zadáte:

- Budete potřebovat externí sdílené složky protokolu SMB na existující soubor založený na Windows server nebo na zařízení třetích stran.
- Tato stejnou sdílenou složku byste měli použít pro zálohování síťové přepínače a životního cyklu hostitelský hardware. Výrobce OEM dodavatele hardwaru pomůže poskytnout pokyny pro zálohování a obnovení tyto součásti jsou tyto externí do služby Azure Stack. Jste zodpovědná za spuštění zálohování pracovních postupů podle doporučení výrobce OEM.

Pokud dojde ke ztrátě dat, můžete použít zálohování infrastruktury reseed nasazení dat. například certifikát kořenové certifikační Autority nasazení pro vstupy a identifikátory, účty služeb, federované prostředky (v odpojených nasazení), plány, nabídky, odběry, kvóty, přiřazení zásad a role RBAC a tajných kódů služby Key Vault.
 
### <a name="protect-tenant-applications-on-iaas-virtual-machines"></a>Ochrana aplikací tenanta na virtuálních počítačích IaaS

Azure Stack, ne back up tenanta aplikacím a datům. Je nutné naplánovat zálohování a po havárii obnovení ochrany na cíl externí do služby Azure Stack. Ochranu klienta je aktivita řízené tenanta. Pro virtuální počítače IaaS tenantů, můžete použít na hostovi technologie k ochraně souboru, složky, data aplikací a stavu systému. Jako zprostředkovatele enterprise nebo službu, můžete však nabízí řešení zálohování a obnovení ve stejném datacentru nebo externě v cloudu.

K zálohování virtuálních počítačů s Linuxem nebo Windows IaaS, musíte použít zálohování produktů díky přístupu k hostovanému operačnímu systému k ochraně souboru, složky, stav operačního systému a dat aplikací. Můžete použít Azure Backup, System Center Data Protection Manageru, nebo produkty třetích stran, které podporují.

Replikace dat do sekundárního umístění a orchestrovat převzetí služeb při selhání aplikace, pokud dojde k havárii, můžete použít Azure Site Recovery nebo podporované produkty třetích stran. Také aplikace, které podporují nativní replikace, jako je Microsoft SQL Server, můžete replikovat data do jiného umístění, ve kterém je spuštěná aplikace.

## <a name="learn-more"></a>Další informace

- Informace o případy použití, nákup, partnery a dodavateli hardwaru OEM, najdete v článku [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) stránce produktu.
- Informace o plánu a geografické dostupnosti pro službu Azure Stack integrované systémy, najdete v dokumentu white paper: [Azure Stack: Rozšíření Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 

## <a name="next-steps"></a>Další postup
[Modely připojení nasazení služby Azure Stack](azure-stack-connection-models.md)
