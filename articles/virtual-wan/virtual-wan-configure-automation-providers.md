---
title: Partneři Azure Virtual WAN | Microsoft Docs
description: Tento článek pomáhá partnerům nastavit automatizaci Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: cherylmc
ms.openlocfilehash: 430d90b2b372602072527c49796244c503778a3b
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959012"
---
# <a name="virtual-wan-partners"></a>Virtuální partneři sítě WAN

Tento článek vám pomůže pochopit, jak nastavit prostředí automatizace pro připojení a konfiguraci zařízení pobočky (místní zařízení VPN zákazníka nebo SDWAN CPE) pro Azure Virtual WAN. Pokud jste poskytovatel, který poskytuje Pobočková zařízení, která se můžou řídit připojením VPN přes IPsec/IKEv2 nebo IPsec/IKEv1, je tento článek pro vás.

Zařízení pobočky (místní zařízení VPN zákazníka nebo SDWAN CPE) obvykle používá zřízený řídicí panel kontroleru nebo zařízení. Správce řešení SD-WAN může často použít konzolu pro správu k předběžnému zřízení zařízení před tím, než se připojí k síti. Toto zařízení podporující síť VPN získá logiku řídicí plochy z kontroleru. Zařízení VPN nebo řadič SD-WAN můžou používat rozhraní API Azure k automatizaci připojení k virtuální síti WAN Azure. Tento typ připojení vyžaduje, aby na místním zařízení bylo přiřazeno externě veřejnou IP adresu.

## <a name ="before"></a>Než začnete s automatizací

* Ověřte, že vaše zařízení podporuje protokol IPsec IKEv1/IKEv2. Viz [výchozí zásady](#default).
* Podívejte se na [rozhraní REST API](#additional) , která používáte k automatizaci připojení k virtuální síti WAN Azure.
* Otestujte možnosti portálu Azure Virtual WAN.
* Pak rozhodněte, která část kroků připojení byste chtěli automatizovat. Je minimálně vhodné automatizovat tyto akce:

  * Řízení přístupu
  * Nahrání informací o zařízeních z pobočky do Azure Virtual WAN
  * Stažení konfigurace Azure a nastavení připojení ze zařízení z větve do Azure Virtual WAN

### <a name ="additional"></a>Další informace

* [REST API](https://docs.microsoft.com/rest/api/virtualwan/virtualhubs) automatizaci vytváření virtuálních rozbočovačů
* [REST API](https://docs.microsoft.com/rest/api/virtualwan/vpngateways) pro automatizaci Azure VPN Gateway pro virtuální síť WAN
* [REST API](https://docs.microsoft.com/rest/api/virtualwan/vpnconnections) připojení VPNSite ke službě Azure VPN hub
* [Výchozí zásady protokolu IPsec](#default)

## <a name ="ae"></a>Prostředí pro zákazníky

Seznamte se s očekávaným prostředím pro zákazníky ve spojení s Azure Virtual WAN.

  1. Virtuální síť WAN obvykle spustí proces vytvořením virtuálního prostředku sítě WAN.
  2. Uživatel nastaví přístup skupiny prostředků na základě instančního objektu pro místní systém (váš řadič vaší pobočky nebo software pro zřizování zařízení VPN) a zapíše informace o větvi do služby Azure Virtual WAN.
  3. Uživatel se může rozhodnout v tuto chvíli, aby se přihlásil k uživatelskému rozhraní a nastavil přihlašovací údaje instančního objektu. Až to bude hotové, váš řadič by měl být schopný nahrávat informace o větvích se službou Automation, kterou poskytnete. Ruční ekvivalent této akce na straně Azure je možnost vytvořit web.
  4. Jakmile jsou informace o lokalitě (zařízení) k dispozici v Azure, uživatel připojení lokality k centru. Virtuální rozbočovač je virtuální síť spravovaná Microsoftem. Centrum obsahuje různé koncové body služby, které umožňují připojení z místní sítě (vpnsite). Rozbočovač je jádrem vaší sítě v určité oblasti. V rámci tohoto procesu může být pouze jeden uzel v každé oblasti Azure a koncový bod VPN (vpngateway). Brána sítě VPN je škálovatelná brána, která se vhodně přizpůsobí podle šířky pásma a potřeb připojení. Můžete se rozhodnout automatizovat virtuální rozbočovač a vytváření vpngateway z řídicího panelu řadiče zařízení ve větvi.
  5. Jakmile je virtuální rozbočovač přidružen k lokalitě, je pro uživatele vygenerován konfigurační soubor, který bude stažen ručně. Tady je místo, kde se vaše automatizace nachází, a umožní uživateli bezproblémové prostředí. Místo toho, aby uživatel musel ručně stahovat a konfigurovat pobočkové zařízení, můžete nastavit automatizaci a poskytnout minimální možnosti kliknutí na uživatelské rozhraní, čímž dojde k zmírnění typických potíží s připojením, jako je neshoda sdíleného klíče, parametr IPSec Neshoda, konfigurační soubor pro čtení atd.
  6. Na konci tohoto kroku v řešení bude mít uživatel bezproblémové připojení typu Site-to-site mezi zařízením pobočky a virtuálním rozbočovačem. Můžete také nastavit další připojení mezi ostatními rozbočovači. Každé připojení je tunel aktivní-aktivní. Zákazník se může rozhodnout pro každé z těchto propojení pro tunel použít jiného poskytovatele internetových služeb.
  7. Zvažte možnost řešení potíží a monitorování v rozhraní pro správu CPE. Mezi typické scénáře patří "zákazník neschopný přístup k prostředkům Azure z důvodu problému CPE", "Zobrazit parametry protokolu IPsec na straně CPE" atd.

## <a name ="understand"></a>Podrobnosti automatizace

###  <a name="access"></a>Řízení přístupu

Zákazníci musí být schopni nastavit příslušné řízení přístupu pro virtuální síť WAN v uživatelském rozhraní zařízení. Tento postup se doporučuje použít u instančního objektu Azure. Přístup na základě instančního objektu poskytuje řadiči zařízení vhodné ověření pro nahrání informací o větvi. Další informace najdete v tématu [Vytvoření instančního objektu](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). I když se tato funkce nachází mimo nabídku Azure Virtual WAN, seznam je uvedený pod běžnými kroky provedenými při nastavování přístupu v Azure, po kterém jsou příslušné podrobnosti zavedené do řídicího panelu správy zařízení.

* Vytvořte aplikaci Azure Active Directory pro místní řadič zařízení.
* Získat ID aplikace a ověřovací klíč
* Získat ID tenanta
* Přiřazení aplikace k roli Přispěvatel

###  <a name="branch"></a>Nahrát informace o zařízení pobočky

Měli byste navrhnout činnost koncového uživatele pro nahrání informací o větvích (místních lokalit) do Azure. Pomocí [rozhraní REST API](https://docs.microsoft.com/rest/api/virtualwan/vpnsites) pro VPNSite můžete vytvořit informace o lokalitě ve virtuální síti WAN. Můžete zadat všechna zařízení SDWAN ve větvi/VPN nebo podle potřeby vybrat vlastní nastavení zařízení.

### <a name="device"></a>Stažení konfigurace zařízení a připojení

Tento krok zahrnuje stažení konfigurace Azure a nastavení připojení ze zařízení z pobočky do Azure Virtual WAN. V tomto kroku zákazník, který nepoužívá poskytovatele, ručně stáhne konfiguraci Azure a použije ji na místní zařízení SDWAN/VPN. Jako poskytovatel byste měli tento krok automatizovat. Další informace najdete v části stažení [rozhraní REST API](https://docs.microsoft.com/rest/api/virtualwan/vpnsitesconfiguration/download) . Řadič zařízení může volat ' GetVpnConfiguration ' REST API ke stažení konfigurace Azure.

**Poznámky ke konfiguraci**

  * Pokud jsou Azure virtuální sítě připojené k virtuálnímu rozbočovači, zobrazí se jako ConnectedSubnets.
  * Připojení VPN používá konfiguraci založenou na směrování a podporuje protokoly IKEv1 a IKEv2.

## <a name="devicefile"></a>Konfigurační soubor zařízení

Konfigurační soubor zařízení obsahuje nastavení, která se použijí při konfiguraci místního zařízení VPN. Při prohlížení tohoto souboru si všimněte následujících informací:

* **vpnSiteConfiguration –** Tato část označuje podrobnosti o zařízení, které se nastavily jako lokalita připojující se k virtuální síti WAN. Obsahuje název a veřejnou IP adresu zařízení pobočky.
* **vpnSiteConnections –** V této části najdete informace o následujících částech:

    * **Adresní prostor** virtuální sítě virtuálních rozbočovačů.<br>Příklad:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Adresní prostor** virtuální sítě, který je připojený k rozbočovači.<br>Příklad:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * **IP adresy** virtuálního centra vpngateway. Vzhledem k tomu, že vpngateway má každé připojení tvořené 2 tunely v konfiguraci aktivní-aktivní, zobrazí se obě IP adresy uvedené v tomto souboru. V tomto příkladu se pro každou lokalitu zobrazí "Instance0" a "položku instance1".<br>Příklad:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Podrobnosti konfigurace připojení Vpngateway** , jako je protokol BGP, předsdílený klíč atd. PSK je předsdílený klíč, který se automaticky vygeneruje za vás. Připojení můžete vždycky upravit na stránce Přehled pro vlastní PSK.
  
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

## <a name="default"></a>Podrobnosti o připojení

Vaše místní zařízení SDWAN/VPN nebo konfigurace SD-WAN musí odpovídat nebo obsahovat následující algoritmy a parametry, které zadáte v zásadách IPsec v Azure/IKE.

* Šifrovací algoritmus IKE
* Algoritmus integrity IKE
* Skupina DH
* Šifrovací algoritmus IPsec
* Algoritmus integrity protokolu IPsec
* Skupina PFS

### <a name="default"></a>Výchozí zásady pro připojení IPsec 

Při práci s výchozími zásadami může Azure během nastavení tunelu IPsec fungovat jako iniciátor i respondér. Azure není podporován pouze jako respondér.

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-include.md)]

## <a name="next-steps"></a>Další kroky

Další informace o virtuální síti WAN najdete v tématu [o Azure Virtual WAN](virtual-wan-about.md) a [nejčastějších dotazech k Azure Virtual WAN](virtual-wan-faq.md).

Pokud chcete získat další informace, pošlete prosím e-mail na <azurevirtualwan@microsoft.com>. Do řádku předmětu zadejte název vaší společnosti v části "[]".