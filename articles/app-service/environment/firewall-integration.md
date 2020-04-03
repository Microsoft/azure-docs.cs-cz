---
title: Uzamčení odchozího provozu
description: Zjistěte, jak integrovat s Azure Firewall pro zabezpečení odchozího provozu z prostředí služby App Service.
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 03/31/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 3dadb57c6358623974de1a27e1601d99b28fee32
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584321"
---
# <a name="locking-down-an-app-service-environment"></a>Uzamčení prostředí služby App Service

Prostředí služby App Service (ASE) má řadu externích závislostí, které vyžaduje přístup, aby správně fungovaly. Služba ASE žije v zákaznické virtuální síti Azure (VNet). Zákazníci musí povolit provoz závislostí služby ASE, což je problém pro zákazníky, kteří chtějí uzamknout všechny odchozí přenosy dat z jejich virtuální sítě.

Existuje několik příchozíkoncové body, které se používají ke správě ase. Příchozí provoz správy nelze odeslat prostřednictvím zařízení brány firewall. Zdrojové adresy pro tento provoz jsou známé a jsou publikovány v dokumentu [adresy správy prostředí služby App Service.](https://docs.microsoft.com/azure/app-service/environment/management-addresses) K dispozici je také značka služby s názvem AppServiceManagement, které lze použít se skupinami zabezpečení sítě (NSGs) k zabezpečení příchozího provozu.

Odchozí závislosti služby ASE jsou téměř zcela definovány pomocí plně kvalifikovaných kvartonů, které nemají za sebou statické adresy. Nedostatek statických adres znamená, že skupiny zabezpečení sítě nelze použít k uzamčení odchozího provozu ze systému ASE. Adresy se mění dostatečně často, že nelze nastavit pravidla na základě aktuálního rozlišení a použít je k vytvoření nsG. 

Řešení zabezpečení odchozích adres spočívá v použití zařízení brány firewall, které může řídit odchozí provoz na základě názvů domén. Azure Firewall můžete omezit odchozí http a https provoz na základě hlavního názvového přístupu k cíli cíle.  

## <a name="system-architecture"></a>Architektura systému

Nasazení služby ASE s odchozím provozem procházejícím zařízením brány firewall vyžaduje změnu trasy v podsíti služby ASE. Trasy pracují na úrovni IP. Pokud nejste opatrní při definování tras, můžete vynutit přenos odpovědi TCP na zdroj z jiné adresy. Pokud se vaše adresa odpovědi liší od přenosu adres, na který byl odeslán, problém se nazývá asymetrické směrování a přeruší protokol TCP.

Musí být definovány trasy tak, aby příchozí provoz na ase můžete odpovědět zpět stejným způsobem provozu přišel. Trasy musí být definovány pro příchozí požadavky na správu a pro požadavky příchozích aplikací.

Provoz do a ze systému Řízení a řízení o pozemních reacích musí dodržovat následující úmluvy

* Provoz do Azure SQL, Úložiště a Event Hub nejsou podporovány s použitím zařízení brány firewall. Tento provoz musí být odeslán přímo těmto službám. Způsob, jak k tomu dojít, je konfigurace koncových bodů služby pro tyto tři služby. 
* Pravidla tabulky trasy musí být definována, aby odesílala příchozí provoz správy zpět z místa, odkud přišla.
* Pravidla tabulky trasy musí být definována, aby odesílala příchozí provoz aplikace zpět z místa, odkud byla odeslána. 
* Všechny ostatní přenosy opouštějící službu ASE lze odeslat do zařízení brány firewall s pravidlem směrovací tabulky.

![Služba ASE s tokem připojení k bráně Azure Firewall][5]

## <a name="locking-down-inbound-management-traffic"></a>Uzamčení příchozího provozu správy

Pokud k podsíti ase ještě není přiřazen soubor NSG, vytvořte jej. V rámci nsg nastavte první pravidlo povolit provoz z výrobního údaje s názvem AppServiceManagement na portech 454, 455. Pravidlo umožňující přístup ze značky AppServiceManagement je jediná věc, která je vyžadována z veřejných IP adresy pro správu služby ASE. Adresy, které jsou za tímto výrobním číslo, se používají pouze ke správě služby Azure App Service. Provoz správy, který prochází těmito připojeními, je šifrován a zabezpečen pomocí ověřovacích certifikátů. Typický provoz na tomto kanálu zahrnuje například příkazy iniciované zákazníkem a sondy stavu. 

Ases, které jsou provedeny prostřednictvím portálu s novou podsítí jsou vyrobeny s NSG, který obsahuje povolit pravidlo pro AppServiceManagement značky.  

Vaše ase musí také povolit příchozí požadavky ze značky vyrovnávání zatížení na portu 16001. Požadavky z balancer na portu 16001 jsou udržovat naživu kontroly mezi vyrovnávání zatížení a front-endů služby ASE. Pokud port 16001 je blokován, vaše ase přejde není v pořádku.

## <a name="configuring-azure-firewall-with-your-ase"></a>Konfigurace brány Azure Firewall se službou ASE 

Postup uzamčení odchozího přenosu dat z existující služby ASE pomocí Azure Firewall jsou:

1. Povolte koncové body služby sql, úložiště a centrum událostí v podsíti služby ASE. Chcete-li povolit koncové body služby, přejděte na síťový portál > podsítí a vyberte položky Microsoft.EventHub, Microsoft.SQL a Microsoft.Storage z rozevíracího přehledu koncových bodů služby. Když máte povolené koncové body služby Azure SQL, všechny závislosti Azure SQL, které vaše aplikace mají, musí být nakonfigurované také s koncovými body služby. 

   ![vybrat koncové body služby][2]
  
1. Vytvořte podsíť s názvem AzureFirewallSubnet ve virtuální síti, kde vaše služba ASE existuje. Podle pokynů v [dokumentaci](https://docs.microsoft.com/azure/firewall/) k Azure Firewall vytvořte Azure Firewall.

1. V kolekci pravidel pravidel aplikace brány Azure Firewall > pravidla > vyberte Přidat kolekci pravidel aplikace. Zadejte název, prioritu a nastavte povolit. V části Značky FQDN zadejte název, nastavte zdrojové adresy na * a vyberte značku FQDN prostředí služby App Service a Windows Update. 
   
   ![Přidat pravidlo aplikace][1]
   
1. V kolekci pravidel Azure Firewall > Pravidla > síť vyberte Přidat kolekci síťových pravidel. Zadejte název, prioritu a nastavte povolit. V části Pravidla v části IP adresy zadejte název, vyberte ptocol **libovolné**adresy , nastavte * na zdrojovou a cílovou adresu a nastavte porty na 123. Toto pravidlo umožňuje systému provádět synchronizaci hodin pomocí ntp. Vytvořte další pravidlo stejným způsobem port 12000 pomoci třídění všechny problémy se systémem. 

   ![Přidat pravidlo sítě NTP][3]
   
1. V kolekci pravidel Azure Firewall > Pravidla > síť vyberte Přidat kolekci síťových pravidel. Zadejte název, prioritu a nastavte povolit. V části Pravidla v části Service Tags zadejte název, vyberte protokol **libovolný**, nastavit * na zdrojové adresy, vyberte značku služby AzureMonitor a nastavte porty na 80, 443. Toto pravidlo umožňuje systému dodávat Azure Monitor se stavem a metriky informace.

   ![Přidat pravidlo sítě značek služeb NTP][6]
   
1. Vytvořte směrovací tabulku s adresami pro správu z [adres správy prostředí služby App Service]( https://docs.microsoft.com/azure/app-service/environment/management-addresses) s dalším směrováním internetu. Položky tabulky trasy jsou nutné, aby se zabránilo asymetrickým problémům s směrováním. Přidejte trasy pro závislosti ip adres uvedené níže v závislostech ip adres s dalším směrováním internetu. Přidejte trasu virtuálního zařízení do směrovací tabulky pro 0.0.0.0/0, přičemž další směrování je privátní IP adresa Azure Firewall. 

   ![Vytvoření tabulky postupu][4]
   
1. Přiřaďte vytvořenou směrovací tabulku k podsíti ase.

#### <a name="deploying-your-ase-behind-a-firewall"></a>Nasazení služby ASE za bránou firewall

Kroky k nasazení služby ASE za bránou firewall jsou stejné jako konfigurace stávající služby ASE pomocí brány Azure Firewall s tím rozdílem, že budete muset vytvořit podsíť služby ASE a potom postupujte podle předchozích kroků. Chcete-li vytvořit službu ASE v již existující podsíti, je třeba použít šablonu Správce prostředků, jak je popsáno v dokumentu [při vytváření služby ASE pomocí šablony Správce prostředků](https://docs.microsoft.com/azure/app-service/environment/create-from-template).

## <a name="application-traffic"></a>Provoz aplikací 

Výše uvedené kroky umožní vaší ase pracovat bez problémů. Stále je třeba nakonfigurovat věci tak, aby vyhovovaly potřebám vaší aplikace. Existují dva problémy pro aplikace ve službě ASE, která je nakonfigurovaná pomocí brány Azure Firewall.  

- Závislosti aplikací musí být přidány do brány Azure Firewall nebo směrovací tabulky. 
- Trasy musí být vytvořeny pro provoz aplikace, aby se zabránilo asymetrickým problémům se směrováním.

Pokud vaše aplikace mají závislosti, je třeba je přidat do brány Azure Firewall. Vytvořte pravidla aplikace pro povolení přenosu HTTP/HTTPS a síťových pravidel pro všechno ostatní. 

Pokud znáte rozsah adres, ze kterého bude pocházet provoz žádosti o aplikaci, můžete jej přidat do směrovací tabulky, která je přiřazena k podsíti služby ASE. Pokud je rozsah adres velký nebo nespecifikovaný, můžete použít síťové zařízení, jako je aplikační brána, a poskytnout vám jednu adresu, kterou chcete přidat do směrovací tabulky. Podrobnosti o konfiguraci aplikační brány se službou ILB ASE našlápnete [na článek Integrace služby ILB Se s aplikační bránou.](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway)

Toto použití aplikační brány je pouze jedním příkladem konfigurace systému. Pokud jste tuto cestu sledovali, bylo by nutné přidat trasu do směrovací tabulky podsítě ASE, aby se tam přímo předával přenos odpovědí odeslaný do brány aplikace. 

## <a name="logging"></a>protokolování 

Azure Firewall můžete odesílat protokoly do Azure Storage, Event Hub nebo Azure Monitor protokoly. Pokud chcete aplikaci integrovat s libovolným podporovaným cílem, přejděte na portál Azure Firewall > diagnostické protokoly a povolte protokoly pro požadovaný cíl. Pokud integrujete s protokoly Azure Monitor, pak uvidíte protokolování pro všechny přenosy odeslané do Azure Firewall. Chcete-li zobrazit provoz, který je odepřen, otevřete portál pracovního prostoru Log Analytics > protokoly a zadejte dotaz, jako je 

    AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
 
Integrace azure firewall u protokolů Azure Monitor je užitečné při prvním získání aplikace pracovní, když si nejste vědomi všech závislostí aplikací. Další informace o protokolech Azure Monitoru najdete v [aplikaci Analyzovat data protokolu v nástroji Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).
 
## <a name="dependencies"></a>Závislosti

Následující informace jsou vyžadovány pouze v případě, že chcete nakonfigurovat jiné zařízení brány firewall než Azure Firewall. 

- Služby podporující službu koncového bodu by měly být konfigurovány s koncovými body služby.
- Závislosti adres IP jsou určeny pro přenosy bez protokolu HTTP/S (přenosy TCP i UDP).
- Koncové body http/https fQDN mohou být umístěny do zařízení brány firewall.
- Koncové body HTTP/HTTPS se zástupnými body jsou závislosti, které se mohou lišit v závislosti na počtu kvalifikátorů. 
- Linux závislosti jsou pouze problém, pokud nasazujete linuxové aplikace do služby ASE. Pokud nenasazujete linuxové aplikace do služby ASE, není nutné tyto adresy přidávat do brány firewall. 

#### <a name="service-endpoint-capable-dependencies"></a>Závislosti podporující koncový bod služby 

| Koncový bod |
|----------|
| Azure SQL |
| Azure Storage |
| Azure Event Hub |

#### <a name="ip-address-dependencies"></a>Závislosti adres IP

| Koncový bod | Podrobnosti |
|----------| ----- |
| \*:123 | Kontrola hodin NTP. Provoz je kontrolován na více koncových bodech na portu 123 |
| \*:12000 | Tento port se používá pro některé monitorování systému. Pokud je blokován, pak některé problémy budou těžší třídění, ale vaše ASE bude i nadále fungovat |
| 40.77.24.27:80 | Potřebné ke sledování a upozornění na problémy se systémem ASE |
| 40.77.24.27:443 | Potřebné ke sledování a upozornění na problémy se systémem ASE |
| 13.90.249.229:80 | Potřebné ke sledování a upozornění na problémy se systémem ASE |
| 13.90.249.229:443 | Potřebné ke sledování a upozornění na problémy se systémem ASE |
| 104.45.230.69:80 | Potřebné ke sledování a upozornění na problémy se systémem ASE |
| 104.45.230.69:443 | Potřebné ke sledování a upozornění na problémy se systémem ASE |
| 13.82.184.151:80 | Potřebné ke sledování a upozornění na problémy se systémem ASE |
| 13.82.184.151:443 | Potřebné ke sledování a upozornění na problémy se systémem ASE |

Pomocí brány Azure Firewall získáte automaticky vše níže nakonfigurované pomocí značek FQDN. 

#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS závislosti 

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

#### <a name="wildcard-httphttps-dependencies"></a>Zástupné znakové http/https závislosti 

| Koncový bod |
|----------|
|gr-Prod-\*.cloudapp.net:443 |
| \*.management.azure.com:443 |
| \*.update.microsoft.com:443 |
| \*.windowsupdate.microsoft.com:443 |
| \*.identity.azure.net:443 |
| \*.ctldl.windowsupdate.com:80 |
| \*.ctldl.windowsupdate.com:443 |

#### <a name="linux-dependencies"></a>Závislosti na Linuxu 

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
| \*.cdn.mscr.io:443 |
|mcr.microsoft.com:443 |
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

## <a name="us-gov-dependencies"></a>Závislosti us gov

Pro ases v oblastech US Gov postupujte podle pokynů v [části Konfigurace brány Azure firewall s ase](https://docs.microsoft.com/azure/app-service/environment/firewall-integration#configuring-azure-firewall-with-your-ase) tohoto dokumentu a nakonfigurujte bránu Firewall Azure se službou ASE.

Pokud chcete v programu US Gov používat jiné zařízení než Azure Firewall 

* Služby podporující službu koncového bodu by měly být konfigurovány s koncovými body služby.
* Koncové body http/https fQDN mohou být umístěny do zařízení brány firewall.
* Koncové body HTTP/HTTPS se zástupnými body jsou závislosti, které se mohou lišit v závislosti na počtu kvalifikátorů.

Linux není k dispozici v oblastech US Gov, a proto není uveden jako volitelná konfigurace.

#### <a name="service-endpoint-capable-dependencies"></a>Závislosti podporující koncový bod služby ####

| Koncový bod |
|----------|
| Azure SQL |
| Azure Storage |
| Azure Event Hub |

#### <a name="ip-address-dependencies"></a>Závislosti adres IP

| Koncový bod | Podrobnosti |
|----------| ----- |
| \*:123 | Kontrola hodin NTP. Provoz je kontrolován na více koncových bodech na portu 123 |
| \*:12000 | Tento port se používá pro některé monitorování systému. Pokud je blokován, pak některé problémy budou těžší třídění, ale vaše ASE bude i nadále fungovat |
| 40.77.24.27:80 | Potřebné ke sledování a upozornění na problémy se systémem ASE |
| 40.77.24.27:443 | Potřebné ke sledování a upozornění na problémy se systémem ASE |
| 13.90.249.229:80 | Potřebné ke sledování a upozornění na problémy se systémem ASE |
| 13.90.249.229:443 | Potřebné ke sledování a upozornění na problémy se systémem ASE |
| 104.45.230.69:80 | Potřebné ke sledování a upozornění na problémy se systémem ASE |
| 104.45.230.69:443 | Potřebné ke sledování a upozornění na problémy se systémem ASE |
| 13.82.184.151:80 | Potřebné ke sledování a upozornění na problémy se systémem ASE |
| 13.82.184.151:443 | Potřebné ke sledování a upozornění na problémy se systémem ASE |

#### <a name="dependencies"></a>Závislosti ####

| Koncový bod |
|----------|
| \*.ctldl.windowsupdate.com:80 |
| \*.management.usgovcloudapi.net:80 |
| \*.update.microsoft.com:80 |
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
|mscrl.microsoft.com
|ocsp.digicert.0 |
|ocsp.msocsp.co|
|ocsp.verisign.0 |
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
|\*.management.usgovcloudapi.net:443 |
|\*.update.microsoft.com:443 |
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
