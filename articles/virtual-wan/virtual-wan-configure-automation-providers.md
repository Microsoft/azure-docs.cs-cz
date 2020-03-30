---
title: Pokyny pro automatizaci partnerů virtuální sítě Azure | Dokumenty společnosti Microsoft
description: Tento článek pomáhá partnerům nastavit azure virtual wan automatizace.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: cherylmc
ms.openlocfilehash: 7848dda09b39f446dd218b7ce1eb2a07664bcaa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190418"
---
# <a name="automation-guidelines-for-virtual-wan-partners"></a>Pokyny pro automatizaci pro partnery virtuální sítě WAN

Tento článek vám pomůže pochopit, jak nastavit prostředí automatizace pro připojení a konfiguraci pobočkového zařízení (místní zařízení VPN zákazníka nebo SDWAN CPE) pro virtuální síť Azure WAN. Pokud jste zprostředkovatel, který poskytuje pobočkové zařízení, které může pojmout připojení VPN přes IPsec/IKEv2 nebo IPsec/IKEv1, tento článek je pro vás.

Pobočkové zařízení (místní zařízení VPN zákazníka nebo SDWAN CPE) obvykle používá řídicí panel řadiče nebo zařízení, které mají být zřízeny. Správci řešení SD-WAN mohou často používat konzolu pro správu k předběžnému zřízení zařízení před připojením k síti. Toto zařízení podporující VPN získává logiku řídicí roviny z řadiče. Zařízení VPN nebo řadič SD-WAN můžou pomocí rozhraní API Azure automatizovat připojení k virtuální síti Azure. Tento typ připojení vyžaduje, aby místní zařízení mělo přiřazenu externí veřejnou IP adresu.

## <a name="before-you-begin-automating"></a><a name ="before"></a>Než začnete automatizovat

* Ověřte, zda vaše zařízení podporuje protokol IPsec IKEv1/IKEv2. Viz [výchozí zásady](#default).
* Zobrazení [rest API,](#additional) které používáte k automatizaci připojení k Virtuální síti Azure WAN.
* Otestujte si prostředí portálu azure virtuální sítě WAN.
* Poté se rozhodněte, kterou část kroků připojení chcete automatizovat. Minimálně doporučujeme automatizovat:

  * Řízení přístupu
  * Nahrání informací o branch device do azure virtuální sítě WAN
  * Stažení konfigurace Azure a nastavení připojení z pobočkového zařízení do virtuální sítě Azure WAN

### <a name="additional-information"></a><a name ="additional"></a>Další informace

* [ROZHRANÍ REST API](https://docs.microsoft.com/rest/api/virtualwan/virtualhubs) pro automatizaci vytváření virtuálních hubů
* [ROZHRANÍ REST API](https://docs.microsoft.com/rest/api/virtualwan/vpngateways) pro automatizaci brány Azure VPN pro virtuální síť WAN
* [ROZHRANÍ REST API](https://docs.microsoft.com/rest/api/virtualwan/vpnconnections) pro připojení vpnsite k Centru Azure VPN Hub
* [Výchozí zásady IPsec](#default)

## <a name="customer-experience"></a><a name ="ae"></a>Zákaznická zkušenost

Seznamte se s očekávaným zákaznickým prostředím ve spojení s azure virtual wan.

  1. Virtuální uživatel sítě WAN obvykle zahájí proces vytvořením prostředku virtuální sítě WAN.
  2. Uživatel nastaví přístup ke skupině prostředků na základě hlavního serveru služeb pro místní systém (váš oborový řadič nebo software pro zřizování zařízení VPN) pro zápis informací o větvi do virtuální sítě Azure WAN.
  3. Uživatel se může rozhodnout, že se přihlásí do uživatelského rozhraní a nastaví pověření instančního objektu. Po dokončení by měl být váš řadič schopen nahrát informace o větvi s automatizací, kterou poskytnete. Ruční ekvivalent tohoto na straně Azure je "Vytvořit web".
  4. Jakmile jsou informace o webu (branch device) dostupné v Azure, uživatel web připojí k rozbočovači. Virtuální rozbočovač je virtuální síť spravovaná společností Microsoft. Rozbočovač obsahuje různé koncové body služby, které umožňují připojení z vaší místní sítě (vpnsite). Rozbočovač je základem vaší sítě v oblasti. Může existovat pouze jedno rozbočovač na oblast Azure a koncový bod VPN (vpngateway) uvnitř se vytvoří během tohoto procesu. Brána VPN je škálovatelná brána, která má velikost vhodně na základě šířky pásma a potřeb připojení. Můžete automatizovat vytváření virtuálního rozbočovače a vpngateway z řídicího panelu řadiče větve zařízení.
  5. Jakmile je virtuální centrum přidruženo k webu, vygeneruje se konfigurační soubor, který může uživatel ručně stáhnout. To je místo, kde vaše automatizace přichází a umožňuje bezproblémové uživatelské prostředí. Namísto toho, aby uživatel musel ručně stahovat a konfigurovat pobočkové zařízení, můžete nastavit automatizaci a poskytnout minimální možnosti prokliku v uživatelském rozhraní, čímž se zmírní typické problémy s připojením, jako je neshoda sdíleného klíče, parametr IPSec nesouladu, čitelnost konfiguračního souboru atd.
  6. Na konci tohoto kroku ve vašem řešení bude mít uživatel bezproblémové připojení mezi web-to-site mezi pobočkovým zařízením a virtuálním rozbočovačem. Můžete také nastavit další připojení mezi ostatními rozbočovači. Každé připojení je aktivní aktivní tunelové propojení. Zákazník se může rozhodnout použít pro každý odkaz pro tunelový propojení jiného isp.
  7. Zvažte poskytování funkcí pro řešení potíží a monitorování v rozhraní pro správu CPE. Mezi typické scénáře patří "Zákazník nemá přístup k prostředkům Azure z důvodu problému s CPE", "Zobrazit parametry IPsec na straně CPE" atd.

## <a name="automation-details"></a><a name ="understand"></a>Podrobnosti o automatizaci

###  <a name="access-control"></a><a name="access"></a>Řízení přístupu

Zákazníci musí být schopni nastavit odpovídající řízení přístupu pro virtuální WAN v uzdu zařízení. To se doporučuje používat azure service inizum. Přístup založený na primárním serveru služby poskytuje řadiči zařízení odpovídající ověření pro odesílání informací o větvi. Další informace naleznete v [tématu Vytvoření instančního objektu](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). I když tato funkce je mimo nabídku Azure Virtual WAN, uvádíme pod typickými kroky, které byly podniknuty k nastavení přístupu v Azure, po kterém se příslušné podrobnosti zadávají do řídicího panelu pro správu zařízení.

* Vytvořte aplikaci Azure Active Directory pro místní řadič zařízení.
* Získání ID aplikace a ověřovacího klíče
* Získání ID tenanta
* Přiřazení aplikace k roli Přispěvatel

###  <a name="upload-branch-device-information"></a><a name="branch"></a>Nahrání informací o zařízení pobočky

Měli byste navrhnout uživatelské prostředí pro nahrání informací o pobočkách (místníweb) do Azure. K vytvoření informací o webu ve virtuální síti WAN můžete použít [rest api](https://docs.microsoft.com/rest/api/virtualwan/vpnsites) pro vpnsite. Můžete zadat všechny pobočky SDWAN / VPN zařízení nebo vybrat vlastní nastavení zařízení podle potřeby.

### <a name="device-configuration-download-and-connectivity"></a><a name="device"></a>Stažení a připojení konfigurace zařízení

Tento krok zahrnuje stažení konfigurace Azure a nastavení připojení z pobočkového zařízení do virtuální sítě Azure WAN. V tomto kroku by zákazník, který nepoužívá zprostředkovatele, ručně stáhl konfiguraci Azure a použil ji na místní zařízení SDWAN/VPN. Jako zprostředkovatel byste měli tento krok automatizovat. Další informace naleznete v rozhraníCH [REST pro](https://docs.microsoft.com/rest/api/virtualwan/vpnsitesconfiguration/download) stahování. Řadič zařízení můžete volat rozhraní REST API GetVpnConfiguration ke stažení konfigurace Azure.

**Poznámky ke konfiguraci**

  * Pokud jsou virtuální sítě Azure připojené k virtuálnímu rozbočovači, zobrazí se jako ConnectedSubnets.
  * Připojení VPN používá konfiguraci založenou na trase a podporuje protokoly IKEv1 i IKEv2.

## <a name="device-configuration-file"></a><a name="devicefile"></a>Konfigurační soubor zařízení

Konfigurační soubor zařízení obsahuje nastavení, které se má použít při konfiguraci místního zařízení VPN. Při prohlížení souboru si všimněte následujících informací:

* **vpnSiteConfiguration** – tato část udává podrobnosti o zařízení nastaveném jako lokalita, která se připojuje k virtuální síti WAN. Obsahuje název a veřejnou IP adresu zařízení pobočky.
* **vpnSiteConnections** – tato část obsahuje následující informace:

    * **Adresní prostor** virtuální rozbočovače virtuální sítě.<br>Příklad:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Adresní prostor** virtuálních sítí, které jsou připojené k rozbočovači.<br>Příklad:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * **IP adresy** brány sítě VPN virtuálního rozbočovače. Vzhledem k tomu, že každé připojení brány sítě VPN se skládá ze 2 tunelů v konfiguraci aktivní-aktivní, uvidíte v tomto souboru uvedené obě IP adresy. V tomto příkladu vidíte pro každou lokalitu položky Instance0 a Instance1.<br>Příklad:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Podrobnosti konfigurace připojení Vpngateway,** jako je BGP, předsdílený klíč atd. PSK je předsdílený klíč, který je automaticky generován pro vás. V případě vlastního předsdíleného klíče můžete připojení upravit na stránce Overview (Přehled).
  
**Příklad konfiguračního souboru zařízení**

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
                  "10.3.0.0/16"
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

## <a name="connectivity-details"></a><a name="default"></a>Podrobnosti o připojení

Vaše místní zařízení SDWAN/VPN nebo konfigurace SD-WAN musí odpovídat nebo obsahovat následující algoritmy a parametry, které zadáte v zásadách Azure IPsec/IKE.

* Algoritmus šifrování protokolem IKE
* Algoritmus integrity protokolu IKE
* Skupina DH
* Algoritmus šifrování protokolem IPsec
* Algoritmus integrity protokolu IPsec
* Skupina PFS

### <a name="default-policies-for-ipsec-connectivity"></a><a name="default"></a>Výchozí zásady pro připojení IPsec

[!INCLUDE [IPsec Default](../../includes/virtual-wan-ipsec-include.md)]

### <a name="custom-policies-for-ipsec-connectivity"></a><a name="custom"></a>Vlastní zásady pro připojení k síti IPsec

[!INCLUDE [IPsec Custom](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="next-steps"></a>Další kroky

Další informace o virtuální wan, najdete [v tématu o Azure Virtual WAN](virtual-wan-about.md) a Azure Virtual WAN [FAQ](virtual-wan-faq.md).

Další informace získáte zasláním <azurevirtualwan@microsoft.com>e-mailu na adresu . V řádku předmětu uveďte název vaší společnosti v hranatých závorkách „[]“.
