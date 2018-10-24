---
title: Virtuální sítě WAN partneři Azure | Dokumentace Microsoftu
description: Tento článek pomůže partneři nastavení Azure virtuální sítě WAN služby automation.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: cherylmc
Customer intent: As a Virtual WAN software-defined connectivity provider, I want to set up a provisioning environment.
ms.openlocfilehash: a4664e628af5824b7b197cbdb5c5af602a3a4476
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49958682"
---
# <a name="virtual-wan-partners"></a>Virtuální sítě WAN partnerů

Tento článek vám pomůže pochopit nastavení automatizovaného prostředí pro připojení a konfigurace zařízení větev (v místním zařízení VPN zákazníka nebo SDWAN CPE) pro Azure virtuální sítě WAN. Pokud se zprostředkovatel, který poskytuje zařízení větve, které zvládne připojení k síti VPN prostřednictvím protokolu IPsec nebo IKEv2 nebo protokolu IPsec/IKEv1, tento článek právě pro vás.

Větev zařízení (zařízení VPN zákazníka v místním nebo SDWAN CPE) obvykle používá řídicí panel řadiče/zařízení zřídit. Správci řešení SD-WAN často slouží Konzola pro správu k předběžnému přidělení zařízení předtím, než získá zapojen do sítě. Toto zařízení VPN podporuje získává svou logikou rovina řízení z kontroleru. SD-WAN řadiče na zařízení VPN můžete použít rozhraní API služby Azure k automatizaci připojení k Azure virtuální sítě WAN. Tento typ připojení vyžaduje místní zařízení, aby mohl zvenčí veřejnou IP adresu přiřazenou.

## <a name ="before"></a>Než začnete, automatizace

* Ověřte, že vaše zařízení podporuje protokol IPsec IKEv1 nebo IKEv2. Zobrazit [výchozí zásady](#default).
* Zobrazit [rozhraní REST API](https://docs.microsoft.com/rest/api/azure/) , že budete používat k automatizaci připojení k Azure virtuální sítě WAN.
* Otestování portálového prostředí z Azure virtuální sítě WAN.
* Rozhodněte, kterou část kroky připojení chcete automatizovat. Na co nejnižší doporučujeme automatizace:

  * Access Control
  * Nahrání informací o zařízeních větev do Azure virtuální sítě WAN
  * Stažení Azure konfigurace a nastavení připojení z větve zařízení v Azure virtuální sítě WAN

* Pochopte očekávané uživatelské prostředí ve spojení s Azure virtuální sítě WAN.

  1. Uživatel virtuální sítě WAN obvykle, spustí proces tak, že vytvoříte prostředek virtuální sítě WAN.
  2. Uživatel se nastavit přístup k službě prostředek založený na objektu skupiny pro místní systém (vaše pobočka řadiči nebo zřizování softwaru zařízení VPN) pro zápis informací o větvi do Azure virtuální sítě WAN.
  3. Uživatel může rozhodnout v tuto chvíli k přihlášení do uživatelského rozhraní a nastavení přihlašovacích údajů instančního objektu služby. Jednou, který je dokončeno, kontrolér měl odeslat větev informace díky službě automation, který bude poskytovat. Manuální ekvivalent tohoto objektu na straně Azure je vytvořit web.
  4. Až informace o lokalitě (zařízení větev) k dispozici v Azure, uživatel přidružíte lokalitu k rozbočovači. Virtuální rozbočovač je spravovaná Microsoftem virtuální sítě. Rozbočovač obsahuje různé koncové body služby, které umožňují připojení z vaší místní sítě (vpnsite). Rozbočovač je základem vaší sítě v oblasti. Může existovat pouze jedno centrum každou oblast Azure a koncovým bodem vpn (vpngateway) uvnitř se během tohoto procesu. Brána VPN je škálovatelnou bránu, která velikosti odpovídajícím způsobem na základě šířky pásma a připojení potřebám. Můžete automatizovat virtuální rozbočovač a vytváření vpngateway z řídicí panel řadiče zařízení větve.
  5. Po přidružení k webu virtuální rozbočovač konfigurační soubor se generuje pro uživatele ručně stáhnout. Toto je kde je k dispozici ve vaší automatizace a zajišťuje bezproblémovou uživatelům. Místo na uživatele, museli ručně stáhnout a nakonfigurovat zařízení větve můžete nastavit automatizace a poskytovat minimální proklikávání na uživatelské rozhraní, a tím boj typické připojením problémy, jako je sdílené klíče se neshodují, parametrů protokolu IPSec Neshoda, konfigurační soubor čitelnost atd.
  6. Na konci tohoto kroku ve vašem řešení uživatel bude mít bezproblémové připojení site-to-site mezi zařízením větve a virtuální rozbočovač. Můžete také nastavit další připojení mezi ostatními centry. Každé připojení je tunelové propojení typu aktivní aktivní. Zákazník rozhodnout pro použití jiné poskytovatele internetových služeb pro jednotlivé odkazy pro tunelové propojení.

## <a name ="understand"></a>Principy automatizace podrobnosti


###  <a name="access"></a>Řízení přístupu

Zákazníci musí mít možnost nastavit řízení přístupu na příslušné virtuální sítě WAN do uživatelského zařízení. Tato možnost se doporučuje pomocí instančního objektu služby Azure. Přístup na základě instanční objekt služby poskytuje příslušný ověřovací řadiče zařízení k odeslání informací o pobočkách. Další informace najdete v tématu [vytvořit instanční objekt](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Tato funkce je mimo Azure virtuální sítě WAN nabídky, uvádíme níže Typická opatření k nastavení přístupu v Azure, po jejímž uplynutí je detekován relevantní podrobnosti na řídicí panel pro správu zařízení

* Vytvoření aplikace Azure Active Directory pro vaše místní zařízení kontroler.
* Získejte ID a ověřovacího klíče aplikace
* Získání ID tenanta
* Přiřaďte aplikaci roli "Přispěvatel"

###  <a name="branch"></a>Nahrání informací o zařízení větve

Návrh uživatelského prostředí k odesílání informací o větev (v místním lokalitě) do Azure. [REST API](https://docs.microsoft.com/rest/api/virtualwan/vpnsites) pro VPNSite lze použít k vytvoření informací o lokalitě do virtuální sítě WAN. Můžete zadat všechna zařízení SDWAN/VPN větev nebo vyberte vlastní nastavení zařízení podle potřeby.


### <a name="device"></a>Stažení konfigurace zařízení a připojení

Tento krok zahrnuje stahování Azure konfigurace a nastavení připojení ze zařízení větev do Azure virtuální sítě WAN. V tomto kroku by zákazník, který nepoužívá zprostředkovatele ručně stáhnout konfiguraci Azure a použijte ji do svého zařízení v místním SDWAN/VPN. Jako poskytovatel byste automatizovat tento krok. Řadiče zařízení můžete volat rozhraní REST API "GetVpnConfiguration" stáhnout konfiguraci Azure, který bude obvykle vypadat podobně jako následující soubor.

**Poznámky ke konfiguraci**

  * Pokud se virtuálních sítí Azure jsou připojené k virtuální rozbočovač, zobrazí se jako ConnectedSubnets.
  * Připojení k síti VPN používá IKEv2/IKEv1 a konfigurace založená na trasách.

#### <a name="understanding-the-device-configuration-file"></a>Principy konfigurační soubor zařízení

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
  
#### <a name="example-device-configuration-file"></a>Příklad konfiguračního souboru zařízení

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

## <a name="default"></a>Výchozí zásady pro připojení protokolu IPsec

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

Vaše místní zařízení SDWAN/VPN nebo SD-WAN konfigurace musí odpovídat nebo obsahovat následující algoritmy a parametry, které zadáte v zásadách Azure IPsec/IKE.

* Algoritmus šifrování protokolem IKE
* Algoritmus integrity protokolu IKE
* Skupina DH
* Algoritmus šifrování protokolem IPsec
* Algoritmus integrity protokolu IPsec
* Skupina PFS

## <a name="next-steps"></a>Další postup

Další informace o virtuální sítě WAN, naleznete v tématu [o Azure virtuální sítě WAN](virtual-wan-about.md) a [Azure virtuální sítě WAN – nejčastější dotazy](virtual-wan-faq.md).

Další informace, pošlete e-mailu <azurevirtualwan@microsoft.com>. V řádku předmětu uveďte název vaší společnosti v hranatých závorkách „[]“.
