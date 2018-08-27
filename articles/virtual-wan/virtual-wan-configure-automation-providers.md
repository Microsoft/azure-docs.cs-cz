---
title: Konfigurace Azure virtuální sítě WAN automatizace - pro virtuální sítě WAN partneři | Dokumentace Microsoftu
description: Tento článek pomůže partneři řešení softwarově definované připojení nastavení Azure virtuální sítě WAN služby automation.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: cherylmc
Customer intent: As a Virtual WAN software-defined connectivity provider, I want to set up a provisioning environment.
ms.openlocfilehash: bac728f286c90550107b27da76a070623577ed82
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918896"
---
# <a name="configure-virtual-wan-automation---for-virtual-wan-partners-preview"></a>Konfigurace virtuální sítě WAN automatizace - pro virtuální sítě WAN partnery (Preview)

Tento článek vám pomůže pochopit nastavení automatizovaného prostředí pro připojení a konfigurace zařízení větev (v místním zařízení VPN zákazníka nebo SDWAN) pro Azure virtuální sítě WAN. Pokud se zprostředkovatel, který poskytuje zařízení větev, která zvládne připojení k síti VPN prostřednictvím protokolu IPsec nebo IKEv2, tento článek právě pro vás.

Připojení k softwarově definované řešení obvykle používají kontroleru nebo device provisioning System center ke správě svých zařízeních větve. Kontroler můžete použít rozhraní API služby Azure k automatizaci připojení k Azure virtuální sítě WAN. Tento typ připojení vyžaduje SDWAN nebo VPN zařízení místní, ke kterým mají externí určených pro veřejnou IP adresu přiřazenou.

##  <a name="access"></a>Řízení přístupu

Zákazníci musí mít možnost nastavit řízení přístupu na příslušné virtuální sítě WAN do uživatelského zařízení. Tato možnost se doporučuje pomocí instančního objektu služby Azure. Přístup na základě instanční objekt služby poskytuje příslušný ověřovací řadiče zařízení k odeslání informací o pobočkách. Další informace najdete v tématu [vytvořit instanční objekt](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application).

##  <a name="site"></a>Nahrát informace o větve

Návrh uživatelského prostředí k odesílání informací o větev (v místním lokalitě) do Azure. [REST API](https://docs.microsoft.com/rest/api/virtualwan/vpnsites) pro **VPNSite** slouží k vytvoření informací o lokalitě do virtuální sítě WAN. Můžete zadat všechna zařízení SDWAN/VPN větev nebo vyberte vlastní nastavení zařízení podle potřeby.

##  <a name="hub"></a>Centra a služby

Jakmile je zařízení pobočku nahráli do Azure, zákazník obvykle provést výběr oblast centra a/nebo služby na webu Azure Portal, který volá sadu operací, chcete-li vytvořit virtuální síť centra a koncového bodu sítě VPN v rozbočovači. Brána VPN je škálovatelnou bránu, která velikosti odpovídajícím způsobem na základě šířky pásma a připojení potřebám.

## <a name="device"></a>Konfigurace zařízení

V tomto kroku by zákazník, který nepoužívá zprostředkovatele ručně stáhnout konfiguraci Azure a použijte ji do svého zařízení v místním SDWAN/VPN. Jako poskytovatel byste automatizovat tento krok. Kontroler může volat **GetVpnConfiguration** rozhraní REST API pro konfiguraci Azure, který bude obvykle vypadat podobně jako následující soubor stáhnout.

**Poznámky ke konfiguraci**

  * Pokud se virtuálních sítí Azure jsou připojené k virtuální rozbočovač, zobrazí se jako ConnectedSubnets.
  * Připojení k síti VPN používá konfigurace založené na směrování a IKEv2.

### <a name="understanding-the-device-configuration-file"></a>Principy konfigurační soubor zařízení

Konfigurační soubor zařízení obsahuje nastavení, které se má použít při konfiguraci místního zařízení VPN. Při prohlížení souboru si všimněte následujících informací:

* **vpnSiteConfiguration** – tato část udává podrobnosti o zařízení nastaveném jako lokalita, která se připojuje k virtuální síti WAN. Obsahuje název a veřejnou IP adresu zařízení pobočky.
* **vpnSiteConnections** – tato část obsahuje následující informace:

    * **Adresní prostor** z hubu virtuální sítě VNet.<br>Příklad:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Adresní prostor** virtuálních sítí, které jsou připojené k rozbočovači.<br>Příklad:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * **IP adresy** brány sítě VPN virtuálního rozbočovače. Vzhledem k tomu, že každé připojení brány sítě VPN se skládá ze 2 tunelů v konfiguraci aktivní-aktivní, uvidíte v tomto souboru uvedené obě IP adresy. V tomto příkladu vidíte pro každou lokalitu položky Instance0 a Instance1.<br>Příklad:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Podrobnosti o konfiguraci připojení brány sítě VPN**, jako je protokol BGP, předsdílený klíč atd. PSK je předsdílený klíč, který se vám automaticky vygeneruje. V případě vlastního předsdíleného klíče můžete připojení upravit na stránce Overview (Přehled).
  
### <a name="example-device-configuration-file"></a>Příklad konfiguračního souboru zařízení

  ```
  { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"r403583d-9c82-4cb8-8570-1cbbcd9983b5"
      },
      "vpnSiteConfiguration":{ 
         "Name":"testsite1",
         "IPAddress":"73.239.3.208"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe",
               "ConnectedSubnets":[ 
                  "10.2.0.0/16",
                  "10.30.0.0/16"
               ]
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.186",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"bkOWe5dPPqkx0DfFE3tyuP7y3oYqAEbI",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"1f33f891-e1ab-42b8-8d8c-c024d337bcac"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite2",
         "IPAddress":"66.193.205.122"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"XzODPyAYQqFs4ai9WzrJour0qLzeg7Qg",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"cd1e4a23-96bd-43a9-93b5-b51c2a945c7"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite3",
         "IPAddress":"182.71.123.228"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"YLkSdSYd4wjjEThR3aIxaXaqNdxUwSo9",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   }
  ```

## <a name="default"></a>Výchozí zásady

### <a name="initiator"></a>Iniciátor

V následujících částech kombinace podporovaných zásad Azure po iniciátor pro tunelové propojení.

**Fáze 1**

* DH_GROUP_2 AES_256, SHA1,
* DH_GROUP_2 AES_256, SHA_256,
* AES_128, SHA1, DH_GROUP_2
* AES_128, SHA_256, DH_GROUP_2
* DH_GROUP_2 3DES, SHA1,
* DH_GROUP_2 3DES, SHA_256,

**Fáze 2**

* GCM_AES_256 GCM_AES_256, PFS_NONE
* AES_256 SHA_1, PFS_NONE
* CBC_3DES, SHA_1, PFS_NONE
* AES_256, SHA_256, PFS_NONE
* AES_128, SHA_1, PFS_NONE
* CBC_3DES, SHA_256, PFS_NONE


### <a name="responder"></a>Respondér

V následujících částech kombinace podporovaných zásad Azure po respondér pro tunelové propojení.

**Fáze 1**

* DH_GROUP_2 AES_256, SHA1,
* DH_GROUP_2 AES_256, SHA_256,
* AES_128, SHA1, DH_GROUP_2
* AES_128, SHA_256, DH_GROUP_2
* DH_GROUP_2 3DES, SHA1,
* DH_GROUP_2 3DES, SHA_256,

**Fáze 2**

* GCM_AES_256 GCM_AES_256, PFS_NONE
* AES_256 SHA_1, PFS_NONE
* CBC_3DES, SHA_1, PFS_NONE
* AES_256, SHA_256, PFS_NONE
* AES_128, SHA_1, PFS_NONE
* CBC_3DES, SHA_256, PFS_NONE
* PFS_NONE CBC_DES, SHA_1, 
* PFS_1 AES_256 SHA_1,
* PFS_2 AES_256 SHA_1,
* PFS_14 AES_256 SHA_1,
* AES_128, SHA_1, PFS_1
* AES_128, SHA_1, PFS_2
* AES_128, SHA_1, PFS_14
* PFS_1 CBC_3DES, SHA_1,
* PFS_2 CBC_3DES, SHA_1,
* PFS_2 CBC_3DES, SHA_256,
* PFS_1 AES_256, SHA_256,
* PFS_2 AES_256, SHA_256,
* PFS_14 AES_256, SHA_256,
* PFS_24 AES_256 SHA_1,
* PFS_24 AES_256, SHA_256,
* AES_128, SHA_256, PFS_NONE
* AES_128, SHA_256, PFS_1
* AES_128, SHA_256, PFS_2
* AES_128, SHA_256, PFS_14
* PFS_14 CBC_3DES, SHA_1,

### <a name="does-everything-need-to-match-between-the-virtual-hub-vpngateway-policy-and-my-on-premises-sdwanvpn-device-or-sd-wan-configuration"></a>Všechno, co musí shodovat zásad vpngateway virtuální rozbočovač a Moje zařízení SDWAN/VPN v místním nebo SD-WAN konfigurace?

Vaše místní zařízení SDWAN/VPN nebo SD-WAN konfigurace musí odpovídat nebo obsahovat následující algoritmy a parametry, které zadáte v zásadách Azure IPsec/IKE. Doba života přidružení zabezpečení jsou pouze místní specifikace a nemusí odpovídat.

* Algoritmus šifrování protokolem IKE
* Algoritmus integrity protokolu IKE
* Skupina DH
* Algoritmus šifrování protokolem IPsec
* Algoritmus integrity protokolu IPsec
* Skupina PFS

## <a name="feedback"></a>Zpětná vazba na verzi Preview

Vaší zpětné vazby si velmi vážíme. Chcete-li oznámit problémy nebo nám poskytnout zpětnou vazbu (kladnou i zápornou) na službu Virtual WAN, odešlete e-mail na adresu <azurevirtualwan@microsoft.com>. V řádku předmětu uveďte název vaší společnosti v hranatých závorkách „[]“. Pokud oznamujete problém, nezapomeňte také uvést ID předplatného.

## <a name="next-steps"></a>Další postup

Další informace o virtuální sítě WAN, naleznete v tématu [o Azure virtuální sítě WAN](virtual-wan-about.md) a [Azure virtuální sítě WAN – nejčastější dotazy](virtual-wan-faq.md).
