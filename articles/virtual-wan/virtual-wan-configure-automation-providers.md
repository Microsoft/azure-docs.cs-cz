---
title: Pokyny pro automatizaci Azure Virtual WAN partneři | Microsoft Docs
description: Nastavte prostředí automatizace pro připojení a konfiguraci místního připojení VPN nebo SD-WAN nebo připojení k síti Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 29fff3a6a430e3bc1a0b3a13876b55d22f7cb545
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566465"
---
# <a name="automation-guidelines-for-virtual-wan-partners"></a>Automatizační pokyny pro partnery služby Virtual WAN

Tento článek vám pomůže pochopit, jak nastavit prostředí automatizace pro připojení a konfiguraci zařízení pobočky (místní zařízení VPN zákazníka nebo SDWAN CPE) pro Azure Virtual WAN. Pokud jste poskytovatel, který poskytuje Pobočková zařízení, která se můžou řídit připojením VPN přes IPsec/IKEv2 nebo IPsec/IKEv1, je tento článek pro vás.

Zařízení pobočky (místní zařízení VPN zákazníka nebo SDWAN CPE) obvykle používá zřízený řídicí panel kontroleru nebo zařízení. Správce řešení SD-WAN může často použít konzolu pro správu k předběžnému zřízení zařízení před tím, než se připojí k síti. Toto zařízení podporující síť VPN získá logiku řídicí plochy z kontroleru. Zařízení VPN nebo řadič SD-WAN můžou používat rozhraní API Azure k automatizaci připojení k virtuální síti WAN Azure. Tento typ připojení vyžaduje, aby na místním zařízení bylo přiřazeno externě veřejnou IP adresu.

## <a name="before-you-begin-automating"></a><a name ="before"></a>Než začnete s automatizací

* Ověřte, že vaše zařízení podporuje protokol IPsec IKEv1/IKEv2. Viz [výchozí zásady](#default).
* Podívejte se na [rozhraní REST API](#additional) , která používáte k automatizaci připojení k virtuální síti WAN Azure.
* Otestujte možnosti portálu Azure Virtual WAN.
* Pak rozhodněte, která část kroků připojení byste chtěli automatizovat. Je minimálně vhodné automatizovat tyto akce:

  * Řízení přístupu
  * Nahrání informací o zařízeních z pobočky do Azure Virtual WAN
  * Stažení konfigurace Azure a nastavení připojení ze zařízení z větve do Azure Virtual WAN

### <a name="additional-information"></a><a name ="additional"></a>Další informace

* [REST API](/rest/api/virtualwan/virtualhubs) automatizaci vytváření virtuálních rozbočovačů
* [REST API](/rest/api/virtualwan/vpngateways) pro automatizaci Azure VPN Gateway pro virtuální síť WAN
* [REST API](/rest/api/virtualwan/vpnconnections) připojení VPNSite ke službě Azure VPN hub
* [Výchozí zásady protokolu IPsec](#default)

## <a name="customer-experience"></a><a name ="ae"></a>Prostředí pro zákazníky

Seznamte se s očekávaným prostředím pro zákazníky ve spojení s Azure Virtual WAN.

  1. Virtuální síť WAN obvykle spustí proces vytvořením virtuálního prostředku sítě WAN.
  2. Uživatel nastaví přístup skupiny prostředků na základě instančního objektu pro místní systém (váš řadič vaší pobočky nebo software pro zřizování zařízení VPN) a zapíše informace o větvi do služby Azure Virtual WAN.
  3. Uživatel se může rozhodnout v tuto chvíli, aby se přihlásil k uživatelskému rozhraní a nastavil přihlašovací údaje instančního objektu. Až to bude hotové, váš řadič by měl být schopný nahrávat informace o větvích se službou Automation, kterou poskytnete. Ruční ekvivalent této akce na straně Azure je možnost vytvořit web.
  4. Jakmile jsou informace o lokalitě (zařízení) k dispozici v Azure, uživatel připojení lokality k centru. Virtuální rozbočovač je virtuální síť spravovaná Microsoftem. Rozbočovač obsahuje různé koncové body služby, které umožňují připojení z vaší místní sítě (vpnsite). Rozbočovač je základem vaší sítě v oblasti. V rámci tohoto procesu může být pouze jeden uzel v každé oblasti Azure a koncový bod VPN (vpngateway). Brána sítě VPN je škálovatelná brána, která se vhodně přizpůsobí podle šířky pásma a potřeb připojení. Můžete se rozhodnout automatizovat virtuální rozbočovač a vytváření vpngateway z řídicího panelu řadiče zařízení ve větvi.
  5. Jakmile je virtuální rozbočovač přidružen k lokalitě, je pro uživatele vygenerován konfigurační soubor, který bude stažen ručně. Tady je místo, kde se vaše automatizace nachází, a umožní uživateli bezproblémové prostředí. Místo toho, aby uživatel musel ručně stahovat a konfigurovat pobočkové zařízení, můžete nastavit automatizaci a poskytnout minimální možnosti na používání uživatelského rozhraní, čímž dojde k zmírnění typických potíží s připojením, jako je neshoda sdíleného klíče, neshoda parametrů protokolu IPSec, čtení konfiguračního souboru atd.
  6. Na konci tohoto kroku v řešení bude mít uživatel bezproblémové připojení typu Site-to-site mezi zařízením pobočky a virtuálním rozbočovačem. Můžete také nastavit další připojení mezi ostatními rozbočovači. Každé připojení je tunel aktivní-aktivní. Zákazník se může rozhodnout pro každé z těchto propojení pro tunel použít jiného poskytovatele internetových služeb.
  7. Zvažte možnost řešení potíží a monitorování v rozhraní pro správu CPE. Mezi typické scénáře patří "zákazník neschopný přístup k prostředkům Azure z důvodu problému CPE", "Zobrazit parametry protokolu IPsec na straně CPE" atd.

## <a name="automation-details"></a><a name ="understand"></a>Podrobnosti automatizace

###  <a name="access-control"></a><a name="access"></a>Řízení přístupu

Zákazníci musí být schopni nastavit příslušné řízení přístupu pro virtuální síť WAN v uživatelském rozhraní zařízení. Tento postup se doporučuje použít u instančního objektu Azure. Přístup na základě instančního objektu poskytuje řadiči zařízení vhodné ověření pro nahrání informací o větvi. Další informace najdete v tématu [Vytvoření instančního objektu](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal). I když se tato funkce nachází mimo nabídku Azure Virtual WAN, seznam je uvedený pod běžnými kroky provedenými při nastavování přístupu v Azure, po kterém jsou příslušné podrobnosti zavedené do řídicího panelu správy zařízení.

* Vytvořte aplikaci Azure Active Directory pro místní řadič zařízení.
* Získat ID aplikace a ověřovací klíč
* Získání ID tenanta
* Přiřazení aplikace k roli Přispěvatel

###  <a name="upload-branch-device-information"></a><a name="branch"></a>Nahrát informace o zařízení pobočky

Měli byste navrhnout činnost koncového uživatele pro nahrání informací o větvích (místních lokalit) do Azure. Pomocí [rozhraní REST API](/rest/api/virtualwan/vpnsites) pro VPNSite můžete vytvořit informace o lokalitě ve virtuální síti WAN. Můžete zadat všechna zařízení SDWAN ve větvi/VPN nebo podle potřeby vybrat vlastní nastavení zařízení.

### <a name="device-configuration-download-and-connectivity"></a><a name="device"></a>Stažení konfigurace zařízení a připojení

Tento krok zahrnuje stažení konfigurace Azure a nastavení připojení ze zařízení z pobočky do Azure Virtual WAN. V tomto kroku zákazník, který nepoužívá poskytovatele, ručně stáhne konfiguraci Azure a použije ji na místní zařízení SDWAN/VPN. Jako poskytovatel byste měli tento krok automatizovat. Další informace najdete v části stažení [rozhraní REST API](/rest/api/virtualwan/vpnsitesconfiguration/download) . Řadič zařízení může volat ' GetVpnConfiguration ' REST API ke stažení konfigurace Azure.

**Poznámky ke konfiguraci**

  * Pokud jsou Azure virtuální sítě připojené k virtuálnímu rozbočovači, zobrazí se jako ConnectedSubnets.
  * Připojení VPN používá konfiguraci založenou na směrování a podporuje protokoly IKEv1 a IKEv2.

## <a name="device-configuration-file"></a><a name="devicefile"></a>Konfigurační soubor zařízení

Konfigurační soubor zařízení obsahuje nastavení, které se má použít při konfiguraci místního zařízení VPN. Při prohlížení souboru si všimněte následujících informací:

* **vpnSiteConfiguration** – tato část udává podrobnosti o zařízení nastaveném jako lokalita, která se připojuje k virtuální síti WAN. Obsahuje název a veřejnou IP adresu zařízení pobočky.
* **vpnSiteConnections** – tato část obsahuje následující informace:

    * **Adresní prostor** virtuální sítě virtuálních rozbočovačů.<br>Příklad:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Adresní prostor** virtuální sítě, který je připojený k rozbočovači.<br>Příklad:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * **IP adresy** brány sítě VPN virtuálního rozbočovače. Vzhledem k tomu, že každé připojení brány sítě VPN se skládá ze 2 tunelů v konfiguraci aktivní-aktivní, uvidíte v tomto souboru uvedené obě IP adresy. V tomto příkladu vidíte pro každou lokalitu položky Instance0 a Instance1.<br>Příklad:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Podrobnosti konfigurace připojení Vpngateway** , jako je protokol BGP, předsdílený klíč atd. PSK je předsdílený klíč, který se automaticky vygeneruje za vás. V případě vlastního předsdíleného klíče můžete připojení upravit na stránce Overview (Přehled).
  
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

Vaše místní zařízení SDWAN/VPN nebo konfigurace SD-WAN musí odpovídat nebo obsahovat následující algoritmy a parametry, které zadáte v zásadách IPsec v Azure/IKE.

* Algoritmus šifrování protokolem IKE
* Algoritmus integrity protokolu IKE
* Skupina DH
* Algoritmus šifrování protokolem IPsec
* Algoritmus integrity protokolu IPsec
* Skupina PFS

### <a name="default-policies-for-ipsec-connectivity"></a><a name="default"></a>Výchozí zásady pro připojení IPsec

[!INCLUDE [IPsec Default](../../includes/virtual-wan-ipsec-include.md)]

### <a name="custom-policies-for-ipsec-connectivity"></a><a name="custom"></a>Vlastní zásady pro připojení IPsec

[!INCLUDE [IPsec Custom](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="next-steps"></a>Další kroky

Další informace o virtuální síti WAN najdete v tématu [o Azure Virtual WAN](virtual-wan-about.md) a [nejčastějších dotazech k Azure Virtual WAN](virtual-wan-faq.md).

Pokud potřebujete další informace, pošlete prosím e-mail na adresu <azurevirtualwan@microsoft.com> . V řádku předmětu uveďte název vaší společnosti v hranatých závorkách „[]“.