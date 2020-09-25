---
title: Uzamčení odchozího provozu
description: Naučte se integrovat s Azure Firewall k zabezpečení odchozího provozu v rámci App Service prostředí.
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 09/24/2020
ms.author: ccompy
ms.custom: seodec18, references_regions
ms.openlocfilehash: 782074fc491c0b5e03ced36563bafa8679e78330
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91264656"
---
# <a name="locking-down-an-app-service-environment"></a>Uzamčení App Service Environment

App Service Environment (pomocného programu) má řadu externích závislostí, ke kterým vyžaduje přístup, aby bylo možné správně fungovat. Pomocného uživatele bydlí v rámci zákazníka Azure Virtual Network (VNet). Zákazníci musí povolit provoz závislosti s pomocným mechanismem, což je problém pro zákazníky, kteří chtějí z své virtuální sítě uzamknout veškerý výstup.

K dispozici je několik příchozích koncových bodů, které se používají ke správě pomocného bodu služby. Příchozí provoz správy nelze odeslat přes zařízení brány firewall. Zdrojové adresy tohoto provozu jsou známé a jsou publikovány v dokumentu [adresy pro správu App Service Environment](./management-addresses.md) . Existuje také značka služby s názvem AppServiceManagement, kterou lze použít se skupinami zabezpečení sítě (skupin zabezpečení sítě) k zabezpečení příchozího provozu.

Odchozí závislosti pomocného mechanismu jsou skoro zcela definované s plně kvalifikovanými názvy domén, které nejsou za nimi statické adresy. Nedostatek statických adres znamená, že skupiny zabezpečení sítě nelze použít k uzamknutí odchozího provozu z pomocného mechanismu. Adresy se často mění, takže jedna z nich nemůže nastavit pravidla na základě aktuálního řešení a použít je k vytvoření skupin zabezpečení sítě. 

Řešení pro zabezpečení odchozích adres spočívá v použití zařízení brány firewall, které umožňuje řídit odchozí přenosy na základě názvů domén. Azure Firewall může omezit odchozí přenosy HTTP a HTTPS na základě plně kvalifikovaného názvu domény cílového umístění.  

## <a name="system-architecture"></a>Architektura systému

Nasazení služby řízení přihlašování s odchozím provozem procházející přes bránu firewall vyžaduje změnu tras v podsíti pomocného programu. Trasy fungují na úrovni protokolu IP. Pokud nejste opatrní v definování tras, můžete vynutit příjem přenosu TCP na zdroj z jiné adresy. Pokud se adresa vaší odpovědi liší od přenosu adres, na který byl odeslán, problém se nazývá asymetrické směrování a dojde k přerušení protokolu TCP.

Musí být definované trasy, aby příchozí provoz do pomocného uživatelského rozhraní mohl odpovědět stejným způsobem jako v případě provozu. Trasy musí být definovány pro příchozí požadavky na správu a pro příchozí žádosti o aplikace.

Provoz do a z pomocného mechanismu řízení musí dodržovat následující konvence.

* Provoz do služby Azure SQL, úložiště a centra událostí není podporován při použití zařízení brány firewall. Tento provoz se musí odesílat přímo do těchto služeb. Způsob, jak to provést, je konfigurace koncových bodů služby pro tyto tři služby. 
* Pravidla směrovací tabulky musí být definovaná tak, aby odesílala příchozí provoz pro správu zpátky z místa, kde byla přijata.
* Pravidla směrovací tabulky musí být definovaná tak, aby odesílala příchozí provoz aplikací zpátky z místa, kde byla. 
* Veškerý ostatní provoz, který opouští pomocného mechanismu řízení, se dá odeslat do zařízení brány firewall s pravidlem směrovací tabulky.

![Pomocného programu s Azure Firewallm tokem připojení][5]

## <a name="locking-down-inbound-management-traffic"></a>Uzamčení příchozího provozu správy

Pokud se k vaší podsíti přiNSGho mechanismu PŘIŘÍZENÍ ještě nepřiřadila žádná, vytvořte ji. V rámci NSG nastavte první pravidlo tak, aby povolovalo provoz ze značky služby s názvem AppServiceManagement na portech 454, 455. Pravidlo pro povolení přístupu ze značky AppServiceManagement je jediným aspektem, který je z veřejných IP adres nutný ke správě vašeho přístupového mechanismu. Adresy za touto značkou služby se používají pouze ke správě Azure App Service. Provoz správy, který prochází těmito připojeními, je šifrovaný a zabezpečený pomocí ověřovacích certifikátů. Typický provoz na tomto kanálu zahrnuje věci, jako jsou příkazy iniciované zákazníkem a sondy stavu. 

Služby ASE vytvořené prostřednictvím portálu s novou podsítí jsou vytvořeny pomocí NSG, který obsahuje pravidlo povolení pro značku AppServiceManagement.  

Vaše pomocného mechanismu musí taky povolit příchozí požadavky z Load Balancer značky na portu 16001. Žádosti z Load Balancer na portu 16001 jsou aktivními kontrolami mezi Load Balancer a front-endu pro pomocného uživatelského rozhraní. Pokud je port 16001 zablokovaný, váš pomocného služby nebude v pořádku.

## <a name="configuring-azure-firewall-with-your-ase"></a>Konfigurace Azure Firewall pomocí pomocného mechanismu 

Postup, jak uzamknout výstup z vašeho stávajícího pomocného programu pomocí Azure Firewall:

1. Povolte koncové body služby do SQL, úložiště a centra událostí v podsíti přihlášeného správce sítě. Pokud chcete povolit koncové body služby, přečtěte si portál sítě > podsítě a v rozevíracím seznamu koncové body služby vyberte Microsoft. EventHub, Microsoft. SQL a Microsoft. Storage. Po povolení koncových bodů služby pro Azure SQL musí být všechny závislosti Azure SQL, které vaše aplikace mají, nakonfigurované také s koncovými body služby. 

   ![Výběr koncových bodů služby][2]
  
1. Ve virtuální síti, kde se nachází váš správce přihlašování, vytvořte podsíť s názvem AzureFirewallSubnet. Pokud chcete vytvořit Azure Firewall, postupujte podle pokynů v [dokumentaci k Azure firewall](../../firewall/index.yml) .

1. Z > pravidla Azure Firewall uživatelského rozhraní > kolekce pravidel aplikace vyberte přidat kolekci pravidel aplikace. Zadejte název, prioritu a nastavte povoleno. V části značky plně kvalifikovaného názvu domény zadejte název, nastavte zdrojové adresy na * a vyberte App Service Environment značku plně kvalifikovaného názvu domény a web Windows Update. 
   
   ![Přidat pravidlo aplikace][1]
   
1. Z > pravidla Azure Firewall uživatelského rozhraní > kolekce pravidel sítě vyberte přidat kolekci pravidel sítě. Zadejte název, prioritu a nastavte povoleno. V části pravidla v části IP adresy zadejte název, vyberte protokol **libovolné**, nastavené * na zdrojové a cílové adresy a nastavte porty na 123. Toto pravidlo umožňuje systému provádět synchronizaci hodin pomocí protokolu NTP. Vytvořte další pravidlo stejným způsobem jako port 12000, který vám může pomáhat s tříděním všech systémových problémů. 

   ![Přidat pravidlo sítě NTP][3]
   
1. Z > pravidla Azure Firewall uživatelského rozhraní > kolekce pravidel sítě vyberte přidat kolekci pravidel sítě. Zadejte název, prioritu a nastavte povoleno. V části pravidla v části značky služby zadejte název, vyberte protokol **libovolné**, nastavené * na zdrojové adresy, vyberte značku služby AzureMonitor a nastavte porty na 80, 443. Toto pravidlo umožňuje systému poskytovat Azure Monitor s informacemi o stavu a metrikách.

   ![Přidat síťové pravidlo pro značku služby NTP][6]
   
1. Vytvořte směrovací tabulku s adresami správy z [App Service Environment adres pro správu]( ./management-addresses.md) s dalším segmentem směrování Internetu. Aby se předešlo problémům s asymetrickým směrováním, je třeba zadat položky v tabulce směrování. Přidejte trasy pro závislosti IP adres uvedené níže v závislostech IP adres s dalším segmentem směrování Internetu. Přidejte trasu virtuálního zařízení do směrovací tabulky pro 0.0.0.0/0 s dalším segmentem směrování Azure Firewall privátní IP adresou. 

   ![Vytvoření směrovací tabulky][4]
   
1. Přiřaďte směrovací tabulku, kterou jste vytvořili, do podsítě pomocného mechanismu služby.

#### <a name="deploying-your-ase-behind-a-firewall"></a>Nasazení služby pomocného mechanismu za bránou firewall

Postup nasazení pomocného mechanismu služby za bránou firewall je stejný jako při konfiguraci vašeho stávajícího pomocného objektu s Azure Firewall s tím rozdílem, že budete muset vytvořit podsíť pomocného mechanismu řízení a potom postupovat podle předchozích kroků. Pokud chcete vytvořit správce přihlášený v již existující podsíti, je potřeba použít šablonu Správce prostředků, jak je popsáno v dokumentu o [vytvoření vašeho POmocného programu pomocí šablony Správce prostředků](./create-from-template.md).

## <a name="application-traffic"></a>Provoz aplikace 

Výše uvedené kroky umožní vašemu pomocnému mechanismu fungovat bez problémů. Pořád musíte nakonfigurovat věci, aby vyhovovaly potřebám vaší aplikace. Existují dva problémy pro aplikace v mechanismu řízení služby, které jsou nakonfigurovány s Azure Firewall.  

- Závislosti aplikace musí být přidány do Azure Firewall nebo směrovací tabulky. 
- Pro přenos aplikací se musí vytvořit trasy, aby se předešlo problémům s asymetrickým směrováním.

Pokud vaše aplikace mají závislosti, musí být přidány do Azure Firewall. Vytvořte pravidla aplikací pro povolení přenosů HTTP/HTTPS a síťových pravidel pro všechno ostatní. 

Pokud znáte rozsah adres, ze kterého bude požadavek na provoz vaší aplikace pocházet, můžete ho přidat do směrovací tabulky, která je přiřazena k podsíti přihlášek. Pokud je rozsah adres velký nebo neurčený, můžete použít síťové zařízení, jako je Application Gateway, a získat tak jednu adresu, kterou chcete přidat do směrovací tabulky. Podrobnosti o konfiguraci Application Gateway s pomocným mechanismem interního nástroje najdete v tématu věnovaném [integraci vašich interního nástrojech POmocného mechanismu pro přístup k Application Gateway](./integrate-with-application-gateway.md)

Toto použití Application Gateway je pouze jedním z příkladů konfigurace systému. Pokud jste použili tuto cestu, pak byste museli přidat trasu do tabulky směrování podsítě pomocného mechanismu, aby přenos odpovědí odeslaných do Application Gateway přešel přímo. 

## <a name="logging"></a>protokolování 

Azure Firewall může odesílat protokoly do Azure Storage, centra událostí nebo protokolů Azure Monitor. Pokud chcete svou aplikaci integrovat s jakýmkoli podporovaným cílem, na portálu Azure Firewall > diagnostické protokoly a povolte protokoly pro požadovaný cíl. Pokud provádíte integraci s protokoly Azure Monitor, můžete zobrazit protokolování pro veškerý provoz odeslaný do Azure Firewall. Pokud chcete zobrazit zamítnutý provoz, otevřete Log Analytics portálu pracovního prostoru > protokoly a zadejte dotaz jako 

```kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Integrace vašich Azure Firewall s protokoly Azure Monitor je užitečná při prvním získání aplikace, když si nejste vědomi všech závislostí aplikace. Další informace o protokolech Azure Monitor můžete získat z [analýzy dat protokolu v Azure monitor](../../azure-monitor/log-query/log-query-overview.md).
 
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
| Azure Event Hub |

#### <a name="ip-address-dependencies"></a>Závislosti IP adres

| Koncový bod | Podrobnosti |
|----------| ----- |
| \*: 123 | Kontroluje se čas NTP. Provoz se kontroluje na více koncových bodech na portu 123. |
| \*: 12000 | Tento port se používá pro monitorování systému. Pokud je zablokované, pak se některé problémy budou obtížnější rozlišit, ale bude i nadále fungovat. |
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
|graph.microsoft.com:443 |
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
|ocsp.msocsp.com:80 |
|oneocsp.microsoft.com:80 |
|oneocsp.microsoft.com:443 |
|mscrl.microsoft.com:443 |
|mscrl.microsoft.com:80 |
|crl.microsoft.com:443 |
|crl.microsoft.com:80 |
|www.thawte.com:443 |
|crl3.digicert.com:80 |
|ocsp.digicert.com:80 |
|ocsp.digicert.com:443 |
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
|prod.microsoftmetrics.com:443 |
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
|gcs.prod.monitoring.core.windows.net:80|
|gcs.prod.monitoring.core.windows.net:443|
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 | 
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|settings-win.data.microsoft.com:443 |
|maupdateaccount2.blob.core.windows.net:443 |
|maupdateaccount3.blob.core.windows.net:443 |
|dc.services.visualstudio.com:443 |
|gmstorageprodsn1.blob.core.windows.net:443 |
|gmstorageprodsn1.file.core.windows.net:443 |
|gmstorageprodsn1.queue.core.windows.net:443 |
|gmstorageprodsn1.table.core.windows.net:443 |
|rteventservice.trafficmanager.net:443 |
|ctldl.windowsupdate.com:80 |
|ctldl.windowsupdate.com:443 |

#### <a name="wildcard-httphttps-dependencies"></a>Závislosti HTTP/HTTPS se zástupnými znaky 

| Koncový bod |
|----------|
|GR-prod- \* . cloudapp.NET:443 |
| \*. management.azure.com:443 |
| \*. update.microsoft.com:443 |
| \*. windowsupdate.microsoft.com:443 |
| \*. identity.azure.net:443 |
| \*. ctldl.windowsupdate.com:80 |
| \*. ctldl.windowsupdate.com:443 |

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
|oryx-cdn.microsoft.io:443 |
| \*. cdn.mscr.io:443 |
|mcr.microsoft.com:443 |
|\*. data.mcr.microsoft.com:443 |
|packages.fluentbit.io:80 |
|packages.fluentbit.io:443 |
|apt-mo.trafficmanager.net:80 |
|apt-mo.trafficmanager.net:443 |
|azure.archive.ubuntu.com:80 |
|azure.archive.ubuntu.com:443 |
|changelogs.ubuntu.com:80 |
|13.74.252.37:11371 |
|13.75.127.55:11371 |
|13.76.190.189:11371 |
|13.80.10.205:11371 |
|13.91.48.226:11371 |
|40.76.35.62:11371 |
|104.215.95.108:11371 |

## <a name="us-gov-dependencies"></a>US Gov závislosti

V případě služby ASE v oblasti US Gov postupujte podle pokynů v části [konfigurace Azure firewall s vaším dokumentem pro POmocného](#configuring-azure-firewall-with-your-ase) programu v tomto dokumentu a nakonfigurujte Azure firewall s pomocným mechanismem řízení.

Pokud chcete použít jiné zařízení než Azure Firewall v US Gov 

* Služby podporující koncový bod služby by měly být nakonfigurované s koncovými body služby.
* Do zařízení brány firewall lze umístit koncové body s plně kvalifikovaným názvem domény (FQDN) HTTP/HTTPS.
* Koncové body HTTP/HTTPS se zástupnými znaky jsou závislosti, které se můžou u vašeho mechanismu přihlašování měnit na základě několika kvalifikátorů.

Linux není dostupný v US Gov oblastech a není tak uvedený jako volitelná konfigurace.

#### <a name="service-endpoint-capable-dependencies"></a>Závislosti podporující koncový bod služby ####

| Koncový bod |
|----------|
| Azure SQL |
| Azure Storage |
| Azure Event Hub |

#### <a name="ip-address-dependencies"></a>Závislosti IP adres

| Koncový bod | Podrobnosti |
|----------| ----- |
| \*: 123 | Kontroluje se čas NTP. Provoz se kontroluje na více koncových bodech na portu 123. |
| \*: 12000 | Tento port se používá pro monitorování systému. Pokud je zablokované, pak se některé problémy budou obtížnější rozlišit, ale bude i nadále fungovat. |
| 40.77.24.27:80 | Monitorování a upozornění na problémy s MECHANISMem řízení |
| 40.77.24.27:443 | Monitorování a upozornění na problémy s MECHANISMem řízení |
| 13.90.249.229:80 | Monitorování a upozornění na problémy s MECHANISMem řízení |
| 13.90.249.229:443 | Monitorování a upozornění na problémy s MECHANISMem řízení |
| 104.45.230.69:80 | Monitorování a upozornění na problémy s MECHANISMem řízení |
| 104.45.230.69:443 | Monitorování a upozornění na problémy s MECHANISMem řízení |
| 13.82.184.151:80 | Monitorování a upozornění na problémy s MECHANISMem řízení |
| 13.82.184.151:443 | Monitorování a upozornění na problémy s MECHANISMem řízení |

#### <a name="dependencies"></a>Závislosti ####

| Koncový bod |
|----------|
| \*. ctldl.windowsupdate.com:80 |
| \*. management.usgovcloudapi.net:80 |
| \*. update.microsoft.com:80 |
|admin.core.usgovcloudapi.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azprofileruploads1.blob.core.windows.net:80 |
|azprofileruploads10.blob.core.windows.net:80 |
|azprofileruploads2.blob.core.windows.net:80 |
|azprofileruploads3.blob.core.windows.net:80 |
|azprofileruploads4.blob.core.windows.net:80 |
|azprofileruploads5.blob.core.windows.net:80 |
|azprofileruploads6.blob.core.windows.net:80 |
|azprofileruploads7.blob.core.windows.net:80 |
|azprofileruploads8.blob.core.windows.net:80 |
|azprofileruploads9.blob.core.windows.net:80 |
|azureprofilerfrontdoor.cloudapp.net:80 |
|azurewatsonanalysis.usgovcloudapp.net:80 |
|cacerts.digicert.com:80 |
|client.wns.windows.com:80 |
|crl.microsoft.com:80 |
|crl.verisign.com:80 |
|crl3.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|ctldl.windowsupdate.com:80 |
|definitionupdates.microsoft.com:80 |
|download.windowsupdate.com:80 |
|fairfax.warmpath.usgovcloudapi.net:80 |
|flighting.cp.wd.microsoft.com:80 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:80 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:80 |
|global.metrics.nsatc.net:80 |
|go.microsoft.com:80 |
|gr-gcws-prod-bd3.usgovcloudapp.net:80 |
|gr-gcws-prod-bn1.usgovcloudapp.net:80 |
|gr-gcws-prod-dd3.usgovcloudapp.net:80 |
|gr-gcws-prod-dm2.usgovcloudapp.net:80 |
|gr-gcws-prod-phx20.usgovcloudapp.net:80 |
|gr-gcws-prod-sn5.usgovcloudapp.net:80 |
|login.live.com:80 |
|login.microsoftonline.us:80 |
|management.core.usgovcloudapi.net:80 |
|management.usgovcloudapi.net:80 |
|maupdateaccountff.blob.core.usgovcloudapi.net:80 |
|mscrl.microsoft.com:80
|ocsp.digicert.com:80 |
|ocsp.verisign.com:80 |
|rteventse.trafficmanager.net:80 |
|settings-n.data.microsoft.com:80 |
|shavamafestcdnprod1.azureedge.net:80 |
|shavanifestcdnprod1.azureedge.net:80 |
|v10ortex-win.data.microsoft.com:80 |
|wp.microsoft.com:80 |
|dcpalt.microsoft.com:80 |
|www.microsoft.com:80 |
|www.msftconnecttest.com:80 |
|www.thawte.com:80 |
|\*ctldl.windowsupdate.com:443 |
|\*. management.usgovcloudapi.net:443 |
|\*. update.microsoft.com:443 |
|admin.core.usgovcloudapi.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads5.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 |
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|azurewatsonanalysis.usgovcloudapp.net:443 |
|cacerts.digicert.com:443 |
|client.wns.windows.com:443 |
|crl.microsoft.com:443 |
|crl.verisign.com:443 |
|crl3.digicert.com:443 |
|csc3-2009-2.crl.verisign.com:443 |
|ctldl.windowsupdate.com:443 |
|definitionupdates.microsoft.com:443 |
|download.windowsupdate.com:443 |
|fairfax.warmpath.usgovcloudapi.net:443 |
|gcs.monitoring.core.usgovcloudapi.net:443 |
|flighting.cp.wd.microsoft.com:443 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:443 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:443 |
|global.metrics.nsatc.net:443 |
|go.microsoft.com:443 |
|gr-gcws-prod-bd3.usgovcloudapp.net:443 |
|gr-gcws-prod-bn1.usgovcloudapp.net:443 |
|gr-gcws-prod-dd3.usgovcloudapp.net:443 |
|gr-gcws-prod-dm2.usgovcloudapp.net:443 |
|gr-gcws-prod-phx20.usgovcloudapp.net:443 |
|gr-gcws-prod-sn5.usgovcloudapp.net:443 |
|login.live.com:443 |
|login.microsoftonline.us:443 |
|management.core.usgovcloudapi.net:443 |
|management.usgovcloudapi.net:443 |
|maupdateaccountff.blob.core.usgovcloudapi.net:443 |
|mscrl.microsoft.com:443 |
|ocsp.digicert.com:443 |
|ocsp.msocsp.com:443 |
|ocsp.msocsp.com:80 |
|oneocsp.microsoft.com:80 |
|oneocsp.microsoft.com:443 |
|ocsp.verisign.com:443 |
|rteventservice.trafficmanager.net:443 |
|settings-win.data.microsoft.com:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|v10.vortex-win.data.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|www.microsoft.com:443 |
|www.msftconnecttest.com:443 |
|www.thawte.com:443 |

<!--Image references-->
[1]: ./media/firewall-integration/firewall-apprule.png
[2]: ./media/firewall-integration/firewall-serviceendpoints.png
[3]: ./media/firewall-integration/firewall-ntprule.png
[4]: ./media/firewall-integration/firewall-routetable.png
[5]: ./media/firewall-integration/firewall-topology.png
[6]: ./media/firewall-integration/firewall-ntprule-monitor.png
