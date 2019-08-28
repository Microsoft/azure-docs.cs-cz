---
title: Uzamčení App Service Environment odchozího provozu – Azure
description: Popisuje postup při integraci s Azure Firewall pro zabezpečení odchozího provozu.
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/29/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: a3136939b27a5703aef8213225dee3d4d525754d
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70069490"
---
# <a name="locking-down-an-app-service-environment"></a>Uzamčení App Service Environment

App Service Environment (pomocného programu) má řadu externích závislostí, ke kterým vyžaduje přístup, aby bylo možné správně fungovat. Pomocného uživatele bydlí v rámci zákazníka Azure Virtual Network (VNet). Zákazníci musí povolit provoz závislosti s pomocným mechanismem, což je problém pro zákazníky, kteří chtějí z své virtuální sítě uzamknout veškerý výstup.

Existuje několik příchozích závislostí, které má pomocným mechanismem řízení. Příchozí provoz správy nelze odeslat přes zařízení brány firewall. Zdrojové adresy tohoto provozu jsou známé a jsou publikovány v dokumentu [adresy pro správu App Service Environment](https://docs.microsoft.com/azure/app-service/environment/management-addresses) . Můžete vytvořit pravidla skupiny zabezpečení sítě s těmito informacemi pro zabezpečení příchozího provozu.

Odchozí závislosti pomocného mechanismu jsou skoro zcela definované s plně kvalifikovanými názvy domén, které nejsou za nimi statické adresy. Nedostatek statických adres znamená, že se skupiny zabezpečení sítě (skupin zabezpečení sítě) nedají použít k uzamknutí odchozího provozu z pomocného mechanismu. Adresy se často mění, takže jedna z nich nemůže nastavit pravidla na základě aktuálního řešení a použít je k vytvoření skupin zabezpečení sítě. 

Řešení pro zabezpečení odchozích adres spočívá v použití zařízení brány firewall, které umožňuje řídit odchozí přenosy na základě názvů domén. Azure Firewall může omezit odchozí přenosy HTTP a HTTPS na základě plně kvalifikovaného názvu domény cílového umístění.  

## <a name="system-architecture"></a>Architektura systému

Nasazení služby řízení přihlašování s odchozím provozem procházející přes bránu firewall vyžaduje změnu tras v podsíti pomocného programu. Trasy fungují na úrovni protokolu IP. Pokud nejste opatrní v definování tras, můžete vynutit příjem přenosu TCP na zdroj z jiné adresy. Nazývá se to asymetrické směrování a dojde k přerušení protokolu TCP.

Musí být definované trasy, aby příchozí provoz do pomocného uživatelského rozhraní mohl odpovědět stejným způsobem jako v případě provozu. To platí pro příchozí požadavky na správu a platí pro příchozí žádosti o aplikace.

Provoz do a z pomocného mechanismu řízení musí dodržovat následující konvence.

* Provoz do služby Azure SQL, úložiště a centra událostí není podporován při použití zařízení brány firewall. Tento provoz se musí odesílat přímo do těchto služeb. Způsob, jak to provést, je konfigurace koncových bodů služby pro tyto tři služby. 
* Pravidla směrovací tabulky musí být definovaná tak, aby odesílala příchozí provoz pro správu zpátky z místa, kde byla přijata.
* Pravidla směrovací tabulky musí být definovaná tak, aby odesílala příchozí provoz aplikací zpátky z místa, kde byla. 
* Veškerý ostatní provoz, který opouští pomocného mechanismu řízení, se dá odeslat do zařízení brány firewall s pravidlem směrovací tabulky.

![Pomocného programu s Azure Firewallm tokem připojení][5]

## <a name="configuring-azure-firewall-with-your-ase"></a>Konfigurace Azure Firewall pomocí pomocného mechanismu 

Postup, jak uzamknout výstup z vašeho stávajícího pomocného programu pomocí Azure Firewall:

1. Povolte koncové body služby do SQL, úložiště a centra událostí v podsíti přihlášeného správce sítě. Provedete to tak, že přejdete do portálu sítě > podsítě a v rozevíracím seznamu koncové body služby vyberete Microsoft. EventHub, Microsoft. SQL a Microsoft. Storage. Po povolení koncových bodů služby pro Azure SQL musí být všechny závislosti Azure SQL, které vaše aplikace mají, nakonfigurované také s koncovými body služby. 

   ![Výběr koncových bodů služby][2]
  
1. Ve virtuální síti, kde se nachází váš správce přihlašování, vytvořte podsíť s názvem AzureFirewallSubnet. Pokud chcete vytvořit Azure Firewall, postupujte podle pokynů v [dokumentaci k Azure firewall](https://docs.microsoft.com/azure/firewall/) .
1. Z > pravidla Azure Firewall uživatelského rozhraní > kolekce pravidel aplikace vyberte přidat kolekci pravidel aplikace. Zadejte název, prioritu a nastavte povoleno. V části značky plně kvalifikovaného názvu domény zadejte název, nastavte zdrojové adresy na * a vyberte App Service Environment značku plně kvalifikovaného názvu domény a web Windows Update. 
   
   ![Přidat pravidlo aplikace][1]
   
1. Z > pravidla Azure Firewall uživatelského rozhraní > kolekce pravidel sítě vyberte přidat kolekci pravidel sítě. Zadejte název, prioritu a nastavte povoleno. V části pravidla zadejte název, vyberte **libovolné**, nastavte * na zdrojové a cílové adresy a nastavte porty na 123. Toto pravidlo umožňuje systému provádět synchronizaci hodin pomocí protokolu NTP. Vytvořte další pravidlo stejným způsobem jako port 12000, který vám může pomáhat s tříděním všech systémových problémů.

   ![Přidat pravidlo sítě NTP][3]

1. Vytvořte směrovací tabulku s adresami správy z [App Service Environment adres pro správu]( https://docs.microsoft.com/azure/app-service/environment/management-addresses) s dalším segmentem směrování Internetu. Aby se předešlo problémům s asymetrickým směrováním, je třeba zadat položky v tabulce směrování. Přidejte trasy pro závislosti IP adres uvedené níže v závislostech IP adres s dalším segmentem směrování Internetu. Přidejte trasu virtuálního zařízení do směrovací tabulky pro 0.0.0.0/0 s dalším segmentem směrování Azure Firewall privátní IP adresou. 

   ![Vytvoření směrovací tabulky][4]
   
1. Přiřaďte směrovací tabulku, kterou jste vytvořili, do podsítě pomocného mechanismu služby.

#### <a name="deploying-your-ase-behind-a-firewall"></a>Nasazení služby pomocného mechanismu za bránou firewall

Postup nasazení pomocného mechanismu služby za bránou firewall je stejný jako při konfiguraci vašeho stávajícího pomocného objektu s Azure Firewall s tím rozdílem, že budete muset vytvořit podsíť pomocného mechanismu řízení a potom postupovat podle předchozích kroků. Pokud chcete vytvořit správce přihlášený v již existující podsíti, je potřeba použít šablonu Správce prostředků, jak je popsáno v dokumentu o [vytvoření vašeho POmocného programu pomocí šablony Správce prostředků](https://docs.microsoft.com/azure/app-service/environment/create-from-template).

## <a name="application-traffic"></a>Provoz aplikace 

Výše uvedené kroky umožní vašemu pomocnému mechanismu fungovat bez problémů. Pořád musíte nakonfigurovat věci, aby vyhovovaly potřebám vaší aplikace. Existují dva problémy pro aplikace v mechanismu řízení služby, které jsou nakonfigurovány s Azure Firewall.  

- Závislosti aplikace musí být přidány do Azure Firewall nebo směrovací tabulky. 
- Pro přenos aplikací se musí vytvořit trasy, aby se předešlo problémům s asymetrickým směrováním.

Pokud vaše aplikace mají závislosti, musí být přidány do Azure Firewall. Vytvořte pravidla aplikací pro povolení přenosů HTTP/HTTPS a síťových pravidel pro všechno ostatní. 

Pokud znáte rozsah adres, ze kterého bude požadavek na provoz vaší aplikace pocházet, můžete ho přidat do směrovací tabulky, která je přiřazena k podsíti přihlášek. Pokud je rozsah adres velký nebo neurčený, můžete použít síťové zařízení, jako je Application Gateway, a získat tak jednu adresu, kterou chcete přidat do směrovací tabulky. Podrobnosti o konfiguraci Application Gateway s pomocným mechanismem interního nástroje najdete v tématu věnovaném [integraci vašich interního nástrojech POmocného mechanismu pro přístup k Application Gateway](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway)

Toto použití Application Gateway je pouze jedním z příkladů konfigurace systému. Pokud jste použili tuto cestu, pak byste museli přidat trasu do tabulky směrování podsítě pomocného mechanismu, aby přenos odpovědí odeslaných do Application Gateway přešel přímo. 

## <a name="logging"></a>Protokolování 

Azure Firewall může odesílat protokoly do Azure Storage, centra událostí nebo protokolů Azure Monitor. Pokud chcete svou aplikaci integrovat s jakýmkoli podporovaným cílem, na portálu Azure Firewall > diagnostické protokoly a povolte protokoly pro požadovaný cíl. Pokud provádíte integraci s protokoly Azure Monitor, můžete zobrazit protokolování pro veškerý provoz odeslaný do Azure Firewall. Pokud chcete zobrazit zamítnutý provoz, otevřete Log Analytics portálu pracovního prostoru > protokoly a zadejte dotaz jako 

    AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
 
Integrace vašich Azure Firewall s protokoly Azure Monitor je velmi užitečná při prvním získání aplikace, když si nejste vědomi všech závislostí aplikace. Další informace o protokolech Azure Monitor můžete získat z [analýzy dat protokolu v Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)
 
## <a name="dependencies"></a>Závislosti

Následující informace jsou požadovány pouze v případě, že chcete nakonfigurovat jiné zařízení brány firewall než Azure Firewall. 

- Služby podporující koncový bod služby by měly být nakonfigurované s koncovými body služby.
- Závislosti IP adres pro přenos bez HTTP/S (provoz TCP i UDP)
- Do zařízení brány firewall lze umístit koncové body s plně kvalifikovaným názvem domény (FQDN) HTTP/HTTPS.
- Koncové body HTTP/HTTPS se zástupnými znaky jsou závislosti, které se můžou u vašeho mechanismu přihlašování měnit na základě několika kvalifikátorů. 
- Závislosti Linux se týkají pouze v případě, že nasazujete aplikace pro Linux do svého pomocného mechanismu služby. Pokud do svého pomocného mechanismu nasazujete aplikace pro Linux, pak tyto adresy není nutné přidávat do brány firewall. 

#### <a name="service-endpoint-capable-dependencies"></a>Závislosti podporující koncový bod služby 

| Koncový bod |
|----------|
| Azure SQL |
| Azure Storage |
| Centrum událostí Azure |

#### <a name="ip-address-dependencies"></a>Závislosti IP adres

| Koncový bod | Podrobnosti |
|----------| ----- |
| \*:123 | Kontroluje se čas NTP. Provoz se kontroluje na více koncových bodech na portu 123. |
| \*:12000 | Tento port se používá pro monitorování systému. Pokud je zablokované, budou se některé problémy obtížnější rozlišit, ale váš pomocným mechanismem bude fungovat i nadále. |
| 40.77.24.27:80 | Monitorování a upozornění na problémy s MECHANISMem řízení |
| 40.77.24.27:443 | Monitorování a upozornění na problémy s MECHANISMem řízení |
| 13.90.249.229:80 | Monitorování a upozornění na problémy s MECHANISMem řízení |
| 13.90.249.229:443 | Monitorování a upozornění na problémy s MECHANISMem řízení |
| 104.45.230.69:80 | Monitorování a upozornění na problémy s MECHANISMem řízení |
| 104.45.230.69:443 | Monitorování a upozornění na problémy s MECHANISMem řízení |
| 13.82.184.151:80 | Monitorování a upozornění na problémy s MECHANISMem řízení |
| 13.82.184.151:443 | Monitorování a upozornění na problémy s MECHANISMem řízení |

U Azure Firewall automaticky získáte vše, co je nakonfigurováno pomocí značek plně kvalifikovaného názvu domény. 

#### <a name="fqdn-httphttps-dependencies"></a>Závislosti HTTP/HTTPS v plně kvalifikovaném názvu domény 

| Koncový bod |
|----------|
|graph.windows.net:443 |
|login.live.com:443 |
|login.windows.com:443 |
|login.windows.net:443 |
|login.microsoftonline.com:443 |
|client.wns.windows.com:443 |
|definitionupdates.microsoft.com:443 |
|go.microsoft.com:80 |
|go.microsoft.com:443 |
|www.microsoft.com:80 |
|www.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|ocsp.msocsp.com:443 |
|mscrl.microsoft.com:443 |
|mscrl.microsoft.com:80 |
|crl.microsoft.com:443 |
|crl.microsoft.com:80 |
|www.thawte.com:443 |
|crl3.digicert.com:80 |
|ocsp.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|crl.verisign.com:80 |
|ocsp.verisign.com:80 |
|cacerts.digicert.com:80 |
|azperfcounters1.blob.core.windows.net:443 |
|azurewatsonanalysis-prod.core.windows.net:443 |
|global.metrics.nsatc.net:80 |
|global.metrics.nsatc.net:443 |
|az-prod.metrics.nsatc.net:443 |
|antares.metrics.nsatc.net:443 |
|azglobal-black.azglobal.metrics.nsatc.net:443 |
|azglobal-red.azglobal.metrics.nsatc.net:443 |
|antares-black.antares.metrics.nsatc.net:443 |
|antares-red.antares.metrics.nsatc.net:443 |
|maupdateaccount.blob.core.windows.net:443 |
|clientconfig.passport.net:443 |
|packages.microsoft.com:443 |
|schemas.microsoft.com:80 |
|schemas.microsoft.com:443 |
|management.core.windows.net:443 |
|management.core.windows.net:80 |
|management.azure.com:443 |
|www.msftconnecttest.com:80 |
|shavamanifestcdnprod1.azureedge.net:443 |
|validation-v2.sls.microsoft.com:443 |
|flighting.cp.wd.microsoft.com:443 |
|dmd.metaservices.microsoft.com:80 |
|admin.core.windows.net:443 |
|prod.warmpath.msftcloudes.com:443 |
|prod.warmpath.msftcloudes.com:80 |
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |

#### <a name="wildcard-httphttps-dependencies"></a>Závislosti HTTP/HTTPS se zástupnými znaky 

| Koncový bod |
|----------|
|gr-Prod-\*.cloudapp.net:443 |
| \*.management.azure.com:443 |
| \*.update.microsoft.com:443 |
| \*.windowsupdate.microsoft.com:443 |

#### <a name="linux-dependencies"></a>Závislosti Linux 

| Koncový bod |
|----------|
|wawsinfraprodbay063.blob.core.windows.net:443 |
|registry-1.docker.io:443 |
|auth.docker.io:443 |
|production.cloudflare.docker.com:443 |
|download.docker.com:443 |
|us.archive.ubuntu.com:80 |
|download.mono-project.com:80 |
|packages.treasuredata.com:80|
|security.ubuntu.com:80 |

<!--Image references-->
[1]: ./media/firewall-integration/firewall-apprule.png
[2]: ./media/firewall-integration/firewall-serviceendpoints.png
[3]: ./media/firewall-integration/firewall-ntprule.png
[4]: ./media/firewall-integration/firewall-routetable.png
[5]: ./media/firewall-integration/firewall-topology.png
