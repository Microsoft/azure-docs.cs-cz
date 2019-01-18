---
title: Uzamčení odchozí provoz služby App Service Environment – Azure
description: Popisuje, jak integrovat s bránou Firewall Azure zabezpečit odchozí provoz
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 87331ed0d9e5a4ff51e3669390d1b40dea58574a
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54389231"
---
# <a name="locking-down-an-app-service-environment"></a>Používat jenom služby App Service Environment

App Service Environment (ASE) má řadu externích závislostí, které vyžaduje přístup k mohl správně fungovat. Služba ASE se nachází v zákazníků Azure Virtual Network (VNet). Zákazníci musí umožňovat provoz závislostí služby ASE, který je pro zákazníky, které chcete zamknout všechny výchozí přenosy ze své virtuální síti.

Existuje mnoho příchozí závislosti, které má služba ASE. Řízení příchozích přenosů nelze odeslat prostřednictvím zařízení brány firewall. Zdrojové adresy pro tento provoz jsou známé a jsou publikované ve [adresy pro správu služby App Service Environment](https://docs.microsoft.com/azure/app-service/environment/management-addresses) dokumentu. S touto informací k zabezpečení příchozího provozu, můžete vytvořit pravidla skupiny zabezpečení sítě.

Odchozí závislostí služby ASE jsou téměř úplně definovány pomocí plně kvalifikovaných názvů domén, které nemají statické adresy za nimi stojí. Chybějící statické adresy znamená, že skupiny zabezpečení sítě (Nsg) nelze použít pro uzamčení odchozí přenosy ze služby ASE. Adresy změnit dostatečně často, že jeden nelze nastavit pravidla založená na aktuální řešení a použít k vytvoření skupin zabezpečení sítě. 

Řešení zabezpečení odchozí adresy spočívá v použití zařízení brány firewall, které můžete řídit odchozí provoz na základě názvů domén. Brány Firewall na Azure můžete omezit odchozí přenosy HTTP i HTTPS založené na plně kvalifikovaný název domény cílový.  

## <a name="configuring-azure-firewall-with-your-ase"></a>Konfigurace brány Firewall Azure s vaší služby ASE 

Postup uzamčení odchozího přenosu dat z existující službu ASE s bránou Firewall služby Azure jsou:

1. Povolení koncových bodů služby SQL, úložiště a centra událostí v podsíti služby ASE. Provedete to tak, přejděte na portál pro sítě > podsítí a vyberte Microsoft.EventHub Microsoft.SQL a Microsoft.Storage z rozevíracího seznamu koncových bodů služby. Až budete mít koncové body služby povolené do Azure SQL, musí být všechny závislosti Azure SQL, které vaše aplikace mají nakonfigurovaný s koncovými body služby také. 

   ![Vyberte koncové body služby][2]
  
1. Vytvořte podsíť s názvem AzureFirewallSubnet ve virtuální síti, které se nachází vaše služba ASE. Postupujte podle pokynů [dokumentaci Brána Firewall služby Azure](https://docs.microsoft.com/azure/firewall/) vytvořit vaše Brána Firewall služby Azure.
1. V uživatelském rozhraní brány Firewall Azure > pravidla > kolekce pravidel aplikace, vyberte Přidat aplikaci pravidlo kolekce. Zadejte název, priority a nastavte povolit. V části značky, plně kvalifikovaný název domény, zadejte název, nastavte zdrojové adresy na * a vyberte značku aplikace služby prostředí plně kvalifikovaný název domény a aktualizace Windows. 
   
   ![Přidat pravidlo aplikace][1]
   
1. V uživatelském rozhraní brány Firewall Azure > pravidla > kolekce pravidel sítě, vyberte Přidat kolekci pravidel sítě. Zadejte název, priority a nastavte povolit. V části pravidla pro zadejte název, vyberte **jakékoli**, nastavte * na zdrojové a cílové adresy a porty hodnotu 123. Toto pravidlo umožňuje systému provést synchronizaci hodin pomocí NTP. Vytvořte další pravidlo pro port 12000 stejně ke třídění problémy systému.

   ![Přidat pravidlo sítě NTP][3]

1. Vytvoření směrovací tabulky pomocí adresy pro správu z [adresy pro správu služby App Service Environment]( https://docs.microsoft.com/azure/app-service/environment/management-addresses) s dalším segmentem směrování do Internetu. Položky tabulky trasy je potřeba Předcházejte problémům asymetrického směrování. Přidání tras pro závislosti IP adresy uvedené níže v závislostech IP adresu s dalším segmentem směrování do Internetu. Přidání virtuálního zařízení trasy do směrovací tabulky pro 0.0.0.0/0 s dalším směrováním, přičemž vaše Brána Firewall služby Azure privátní IP adresu. 

   ![Vytvoření směrovací tabulky][4]
   
1. Přiřazení směrovací tabulku, kterou jste vytvořili k podsíti služby ASE.

#### <a name="deploying-your-ase-behind-a-firewall"></a>Nasazování služby ASE za bránou firewall

Postup nasazení služby ASE za bránou firewall, jsou stejná jako konfigurace stávající služby ASE s bránou Firewall Azure s výjimkou budete potřebovat k vytvoření podsíti služby ASE a pak postupujte podle předchozích kroků. K vytvoření služby ASE v již existující podsíti, je potřeba použít šablonu Resource Manageru, jak je popsáno v dokumentu na [vytvoření vaší služby ASE pomocí šablony Resource Manageru](https://docs.microsoft.com/azure/app-service/environment/create-from-template).

## <a name="application-traffic"></a>Provoz aplikací 

Proveďte následující kroky vám umožní vaší služby ASE fungovat bez problémů. Je stále potřeba nakonfigurovat tak, aby vyhovovaly potřebám aplikace věcí. Existují dva problémy související s aplikací ve službě ASE, který je nakonfigurovaný s bránou Firewall služby Azure.  

- Závislosti aplikace musíte přidat do brány Firewall Azure nebo směrovací tabulky. 
- Trasy musí být vytvořeny pro provoz aplikace, abyste předešli problémům s asymetrického směrování

Pokud vaše aplikace mají závislosti, musí být přidán do vaše Brána Firewall služby Azure. Vytvoření aplikace pravidla, která umožňují přenosy HTTP/HTTPS a pravidla síťových pro všechno ostatní. 

Pokud víte, které váš provoz žádostí na aplikace budou přicházet z rozsahu adres, můžete přidat, do směrovací tabulky, který je přiřazen k podsíti služby ASE. Pokud rozsah adres je velký nebo neurčená, pak vám pomůže síťové zařízení, jako je Application Gateway získáte jednu adresu pro přidání do směrovací tabulky. Podrobnosti o tom, jak nakonfigurovat službu Application Gateway se vaše služba ASE s ILB, přečtěte si [integrace služby ILB ASE pomocí služby Application Gateway](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway)

![Služba ASE s flowem připojovací Brána Firewall služby Azure][5]

Toto použití služby Application Gateway je jenom jedním z příkladů, jak nakonfigurovat váš systém. Pokud postupovat podle této cesty, pak musíte přidat trasy do směrovací tabulky podsítě služby ASE, aby odpovědi provoz odeslaný ke službě Application Gateway by tam přejít přímo. 

## <a name="logging"></a>Protokolování 

Brány Azure můžete odeslat protokoly do služby Azure Storage, Centrum událostí a Log Analytics. K integraci vaší aplikace pomocí jakéhokoli podporovaného cíle, přejděte na portál Brána Firewall služby Azure > diagnostické protokoly a povolení protokolování pro váš požadovaný cíl. Pokud integrujete se službou Log Analytics, můžete zobrazit protokolování pro veškerý provoz odeslaný na Brána Firewall služby Azure. Chcete-li sledovat provoz, který je mu odepřen, otevřete portál Log Analytics > protokoly a zadejte dotaz podobný tomuto: 

    AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
 
Integrování vaše Brána Firewall služby Azure Log Analytics je velmi užitečné, když nejdřív Začínáme aplikací pracovat, když si jich nejste vědomi všechny závislosti aplikací. Další informace o službě Log Analytics z [Log Analytics analyzovat data ve službě Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)
 
## <a name="dependencies"></a>Závislosti

Tyto informace je jenom nutné, pokud chcete nakonfigurovat zařízení brány firewall než Brána Firewall služby Azure. 

- Koncový bod služby podporující služby by měly být nakonfigurované koncové body služby.
- IP adresa závislosti se pro přenosy mimo HTTP/S
- Koncové body HTTP/HTTPS plně kvalifikovaný název domény je možné použít ve vašem zařízení brány firewall.
- Koncové body HTTP/HTTPS zástupných znaků jsou závislosti, které se můžou lišit podle vaší služby ASE na základě počtu kvalifikátory. 
- Závislosti Linux jsou pouze žádný problém, pokud provádíte nasazení Linuxové aplikace do vaší služby ASE. Pokud se nasazení Linuxové aplikace do vaší služby ASE, pak tyto adresy není potřeba přidat do brány firewall. 


#### <a name="service-endpoint-capable-dependencies"></a>Koncový bod služby podporující závislosti 

| Koncový bod |
|----------|
| Azure SQL |
| Azure Storage |
| Centrum událostí Azure |

#### <a name="ip-address-dependencies"></a>IP adresa závislosti

| Koncový bod | Podrobnosti |
|----------| ----- |
| \*:123 | Kontrola hodin NTP. Kontroluje provoz při více koncových bodů na portu 123 |
| \*:12000 | Tento port se používá pro některé monitorování systému. Pokud se zablokuje, pak bude obtížnější třídění některé problémy, ale vaše služba ASE bude i nadále fungovat |

S bránou Firewall Azure automaticky získáte všechno pod nakonfigurované se značkami plně kvalifikovaný název domény. 

#### <a name="fqdn-httphttps-dependencies"></a>Plně kvalifikovaný název domény HTTP/HTTPS závislosti 

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
|global.metrics.nsatc.net:80   |
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
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |

#### <a name="wildcard-httphttps-dependencies"></a>Zástupný znak HTTP/HTTPS závislosti 

| Koncový bod |
|----------|
|gr-Prod-\*.cloudapp.net:443 |
| \*.management.azure.com:443 |
| \*.update.microsoft.com:443 |
| \*.windowsupdate.microsoft.com:443 |
|grmdsprod\*mini\*.servicebus.windows.net:443 |
|grmdsprod\*lini\*.servicebus.windows.net:443 |
|grsecprod\*mini\*.servicebus.windows.net:443 |
|grsecprod\*lini\*.servicebus.windows.net:443 |
|graudprod\*mini\*.servicebus.windows.net:443 |
|graudprod\*lini\*.servicebus.windows.net:443 |

#### <a name="linux-dependencies"></a>Závislosti pro Linux 

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
