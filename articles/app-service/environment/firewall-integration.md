---
title: Uzamčení odchozí provoz služby Azure App Service Environment
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
ms.date: 09/24/2018
ms.author: ccompy
ms.openlocfilehash: 5f2dd31488ae61bec061a81986a208bd328bf39b
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093614"
---
# <a name="locking-down-an-app-service-environment"></a>Používat jenom služby App Service Environment

App Service Environment (ASE) má řadu externích závislostí, které vyžaduje přístup k mohl správně fungovat. Služba ASE se nachází v zákazníků Azure Virtual Network (VNet). Zákazníci musí umožňovat provoz závislostí služby ASE, který je pro zákazníky, které chcete zamknout všechny výchozí přenosy ze své virtuální síti.

Existuje mnoho příchozí závislosti, které má služba ASE. Řízení příchozích přenosů nelze odeslat prostřednictvím zařízení brány firewall. Zdrojové adresy pro tento provoz jsou známé a jsou publikované ve [adresy pro správu služby App Service Environment](https://docs.microsoft.com/azure/app-service/environment/management-addresses) dokumentu. S touto informací k zabezpečení příchozího provozu, můžete vytvořit pravidla skupiny zabezpečení sítě.

Odchozí závislostí služby ASE jsou téměř úplně definovány pomocí plně kvalifikovaných názvů domén, které nemají statické adresy za nimi stojí. Chybějící statické adresy znamená, že skupiny zabezpečení sítě (Nsg) nelze použít pro uzamčení odchozí přenosy ze služby ASE. Adresy změnit dostatečně často, že jeden nelze nastavit pravidla založená na aktuální řešení a použít k vytvoření skupin zabezpečení sítě. 

Řešení zabezpečení odchozí adresy spočívá v použití zařízení brány firewall, které můžete řídit odchozí provoz na základě názvů domén. Tým Azure sítě zavedla nové síťové zařízení do volá Brána Firewall služby Azure ve verzi Preview. Brány Firewall Azure je schopen omezení odchozího přenosu dat HTTP a provoz HTTPS na základě názvu DNS cílového umístění.  

## <a name="configuring-azure-firewall-with-your-ase"></a>Konfigurace brány Firewall Azure s vaší služby ASE 

Postup uzamčení výchozí přenosy ze služby ASE s bránou Firewall služby Azure jsou:

1. Vytvoření brány Firewall Azure ve virtuální síti, kde je vaše služba ASE, nebo bude. [Azure jejich brány Firewall](https://docs.microsoft.com/azure/firewall/)
2. V uživatelském rozhraní brány Firewall Azure vyberte značku aplikace služby prostředí plně kvalifikovaný název domény
3. Vytvoření směrovací tabulky pomocí adresy pro správu z [adresy pro správu služby App Service Environment]( https://docs.microsoft.com/azure/app-service/environment/management-addresses) s dalším segmentem směrování do Internetu. Položky tabulky trasy je potřeba Předcházejte problémům asymetrického směrování. 
4. Přidání tras pro závislosti IP adresy uvedené níže v závislostech IP adresu s dalším segmentem směrování do Internetu. 
5. Přidání trasy do směrovací tabulky pro 0.0.0.0/0 s dalším směrováním, přičemž vaše Brána Firewall služby Azure síťové zařízení
6. Vytvoření koncových bodů služby pro podsítě služby ASE pro Azure SQL a službu Azure Storage
7. Přiřazení směrovací tabulku, kterou jste vytvořili k podsíti služby ASE  

## <a name="application-traffic"></a>Provoz aplikací 

Proveďte následující kroky vám umožní vaší služby ASE fungovat bez problémů. Je stále potřeba nakonfigurovat tak, aby vyhovovaly potřebám aplikace věcí. Existují dva problémy související s aplikací ve službě ASE, který je nakonfigurovaný s bránou Firewall služby Azure.  

- Závislost aplikace plně kvalifikovaných názvů domén musí být přidané do brány Firewall Azure nebo směrovací tabulky
- Trasy musí být vytvořeny pro adresy, které provoz budou přicházet z, abyste předešli problémům s asymetrického směrování

Pokud vaše aplikace mají závislosti, musí být přidán do vaše Brána Firewall služby Azure. Vytvoření aplikace pravidla, která umožňují přenosy HTTP/HTTPS a pravidla síťových pro všechno ostatní. 

Pokud víte, které váš provoz žádostí na aplikace budou přicházet z rozsahu adres, můžete přidat, do směrovací tabulky, který je přiřazen k podsíti služby ASE. Pokud rozsah adres je velký nebo neurčená, pak vám pomůže síťové zařízení, jako je Application Gateway získáte jednu adresu pro přidání do směrovací tabulky. Podrobnosti o tom, jak nakonfigurovat službu Application Gateway se vaše služba ASE s ILB, přečtěte si [integrace služby ILB ASE pomocí služby Application Gateway](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway)


## <a name="dependencies"></a>Závislosti

Azure App Service má řadu externích závislostí. Je možné jednoznačně rozdělit do několika hlavních oblastí:

- Koncový bod služby, které podporuje služby by měly být nastaveny koncových bodů služby s, pokud chcete zamezit odchozích síťových přenosů.
- Koncovými body IP adresy se nebudou řešit s názvem domény. To může být problém pro zařízení brány firewall, které veškerý provoz HTTPS na názvy domén používat očekávají. Koncovými body IP adresy, měli byste přidat do směrovací tabulky, který je nastaven na podsíti služby ASE.
- Koncové body HTTP/HTTPS plně kvalifikovaný název domény je možné použít ve vašem zařízení brány firewall.
- Koncové body HTTP/HTTPS zástupných znaků jsou závislosti, které se můžou lišit podle vaší služby ASE na základě počtu kvalifikátory. 
- Závislosti Linux jsou pouze žádný problém, pokud provádíte nasazení Linuxové aplikace do vaší služby ASE. Pokud se nasazení Linuxové aplikace do vaší služby ASE, pak tyto adresy není potřeba přidat do brány firewall. 


#### <a name="service-endpoint-capable-dependencies"></a>Koncový bod služby podporující závislosti 

| Koncový bod |
|----------|
| Azure SQL |
| Azure Storage |
| Azure KeyVault |


#### <a name="ip-address-dependencies"></a>IP adresa závislosti 

| Koncový bod |
|----------|
| 40.77.24.27:443 |
| 13.82.184.151:443 |
| 13.68.109.212:443 |
| 13.90.249.229:443 |
| 13.91.102.27:443 |
| 104.45.230.69:443 |
| 168.62.226.198:12000 |


#### <a name="fqdn-httphttps-dependencies"></a>Plně kvalifikovaný název domény HTTP/HTTPS závislosti 

| Koncový bod |
|----------|
|graph.windows.net:443 |
|login.live.com:443 |
|Login.Windows.com:443 |
|Login.Windows.NET:443 |
|login.microsoftonline.com:443 |
|Client.wns.Windows.com:443 |
|definitionupdates.microsoft.com:443 |
|go.microsoft.com:80 |
|go.microsoft.com:443 |
|www.microsoft.com:80 |
|www.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|OCSP.msocsp.com:443 |
|mscrl.microsoft.com:443 |
|mscrl.microsoft.com:80 |
|CRL.microsoft.com:443 |
|crl.microsoft.com:80 |
|www.Thawte.com:443 |
|crl3.digicert.com:80 |
|OCSP.digicert.com:80 |
|csc3 – 2009-2.crl.verisign.com:80 |
|CRL.VeriSign.com:80 |
|OCSP.VeriSign.com:80 |
|azperfcounters1.BLOB.Core.Windows .net: 443 |
|azurewatsonanalysis prod.core.windows.net:443 |
|Global.Metrics.nsatc.NET:80   |
|AZ prod.metrics.nsatc.net:443 |
|antares.Metrics.nsatc.NET:443 |
|azglobal black.azglobal.metrics.nsatc.net:443 |
|azglobal red.azglobal.metrics.nsatc.net:443 |
|antares black.antares.metrics.nsatc.net:443 |
|antares red.antares.metrics.nsatc.net:443 |
|maupdateaccount.BLOB.Core.Windows.NET:443 |
|clientconfig.Passport.NET:443 |
|Packages.microsoft.com:443 |
|schemas.microsoft.com:80 |
|schemas.microsoft.com:443 |
|Management.Core.Windows.NET:443 |
|Management.Core.Windows.NET:80 |
|www.msftconnecttest.com:80 |
|shavamanifestcdnprod1.azureedge .net: 443 |
|ověření v2.sls.microsoft.com:443 |
|flighting.CP.WD.microsoft.com:443 |
|DMD.metaservices.microsoft.com:80 |
|Admin.Core.Windows.NET:443 |
|azureprofileruploads.BLOB.Core.Windows.NET:443 |
|azureprofileruploads2.BLOB.Core.Windows .net: 443 |
|azureprofileruploads3.BLOB.Core.Windows .net: 443 |
|azureprofileruploads4.BLOB.Core.Windows .net: 443 |
|azureprofileruploads5.BLOB.Core.Windows .net: 443 |

#### <a name="wildcard-httphttps-dependencies"></a>Zástupný znak HTTP/HTTPS závislosti 

| Koncový bod |
|----------|
|GR-Prod -\*. cloudapp.net:443 |
| \*. management.azure.com:443 |
| \*. update.microsoft.com:443 |
| \*. windowsupdate.microsoft.com:443 |
|grmdsprod\*mini\*. servicebus.windows.net:443 |
|grmdsprod\*lini\*. servicebus.windows.net:443 |
|grsecprod\*mini\*. servicebus.windows.net:443 |
|grsecprod\*lini\*. servicebus.windows.net:443 |
|graudprod\*mini\*. servicebus.windows.net:443 |
|graudprod\*lini\*. servicebus.windows.net:443 |

#### <a name="linux-dependencies"></a>Závislosti pro Linux 

| Koncový bod |
|----------|
|wawsinfraprodbay063.BLOB.Core.Windows .net: 443 |
|1.docker.io:443 registru |
|auth.docker.IO:443 |
|Production.cloudflare.docker.com:443 |
|download.docker.com:443 |
|US.Archive.Ubuntu.com:80 |
|download.mono project.com:80 |
|Packages.treasuredata.com:80|
|Security.Ubuntu.com:80 |

