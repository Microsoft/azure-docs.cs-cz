---
title: 'Azure Virtual WAN: vytvoření připojení typu Site-to-site'
description: V tomto kurzu se naučíte vytvořit připojení VPN typu site-to-site k Azure pomocí služby Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 07/09/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: 5f39f5a8f758f390536e5d113629b185252c05d9
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91439372"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>Kurz: Vytvoření připojení typu site-to-site pomocí služby Azure Virtual WAN

V tomto kurzu se dozvíte, jak se pomocí služby Virtual WAN připojit ke svým prostředkům v Azure přes připojení VPN IPsec/IKE (IKEv1 a IKEv2). Tento typ připojení vyžaduje místní zařízení VPN, které má přiřazenou veřejnou IP adresu. Další informace o službě Virtual WAN najdete v tématu [Přehled služby Virtual WAN](virtual-wan-about.md).

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Vytvoření virtuální sítě WAN
> * Vytvoření rozbočovače
> * Vytvoření lokality
> * Připojení lokality k centru
> * Připojení lokality VPN k centru
> * Připojení virtuální sítě k rozbočovači
> * Stažení konfiguračního souboru
> * Konfigurace služby VPN Gateway

> [!NOTE]
> Pokud máte hodně lokalit, doporučujeme využít k vytvoření této konfigurace [partnera pro Virtual WAN](https://aka.ms/virtualwan). Pokud se ale vyznáte ve vytváření sítí a umíte nakonfigurovat vlastní zařízení VPN, můžete tuto konfiguraci vytvořit sami.
>

![Diagram virtuální sítě WAN](./media/virtual-wan-about/virtualwan.png)

## <a name="before-you-begin"></a>Než začnete

Před zahájením konfigurace ověřte, že splňujete následující kritéria:

* Máte virtuální síť, ke které se chcete připojit. Ověřte, že se žádná z podsítí místních sítí nepřekrývá s virtuálními sítěmi, ke kterým se chcete připojit. Pokud chcete vytvořit virtuální síť v Azure Portal, přečtěte si [rychlý Start](../virtual-network/quick-create-portal.md).

* Vaše virtuální síť nemá žádné brány virtuální sítě. Pokud má vaše virtuální síť bránu (buď VPN, nebo ExpressRoute), musíte odebrat všechny brány. Tato konfigurace vyžaduje, aby se virtuální sítě místo toho připojovaly k virtuální bráně WAN hub.

* Zařiďte rozsah IP adres pro oblast vašeho rozbočovače. Centrum je virtuální síť, kterou vytváří a používá virtuální síť WAN. Rozsah adres, který zadáte pro centrum, se nemůže překrývat s žádnou ze stávajících virtuálních sítí, ke kterým se připojujete. Taky se nesmí překrývat s rozsahy adres, ke kterým se připojujete v místním prostředí. Pokud neznáte rozsahy IP adres nacházející se v konfiguraci vaší místní sítě, zajistěte koordinaci s někým, kdo vám poskytne tyto podrobnosti.

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Vytvoření virtuální sítě WAN

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-hub"></a><a name="hub"></a>Vytvoření rozbočovače

Centrum je virtuální síť, která může obsahovat brány pro funkce typu Site-to-site, ExpressRoute nebo Point-to-site. Po vytvoření centra se vám bude centrum účtovat i v případě, že nepřipojíte žádné servery. Vytvoření brány VPN typu Site-to-site ve virtuálním rozbočovači trvá 30 minut.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="create-a-site"></a><a name="site"></a>Vytvoření lokality

Nyní jste připraveni vytvořit lokality odpovídající vašim fyzickým umístěním. Můžete vytvořit libovolný počet lokalit odpovídajících fyzickým umístěním. Pokud máte třeba jednu pobočku v New Yorku, jednu v Londýně a jednu v Los Angeles, vytvoříte tři oddělené lokality. Tyto lokality obsahují koncové body místních zařízení VPN. Můžete vytvořit až 1000 lokalit na jedno virtuální centrum ve virtuální síti WAN. Pokud máte více rozbočovačů, můžete pro každé z těchto Center vytvořit 1000. Pokud máte zařízení Virtual WAN partner (vložit Link) CPE, požádejte ho, aby se dozvěděl o automatizaci do Azure. Obvykle automatizace zahrnuje jednoduché možnosti kliknutí k exportu rozsáhlých informací o větvích do Azure a nastavení připojení ze CPE na Azure Virtual WAN VPN Gateway. Další informace najdete v tématu [pokyny pro automatizaci z Azure až po partnery na CPE](virtual-wan-configure-automation-providers.md).

[!INCLUDE [Create a site](../../includes/virtual-wan-tutorial-s2s-site-include.md)]

## <a name="connect-the-vpn-site-to-the-hub"></a><a name="connectsites"></a>Připojení lokality VPN k centru

V tomto kroku připojíte síť VPN k centru.

[!INCLUDE [Connect VPN sites](../../includes/virtual-wan-tutorial-s2s-connect-vpn-site-include.md)]

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>Připojení virtuální sítě k centru

[!INCLUDE [Connect](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="download-vpn-configuration"></a><a name="device"></a>Stažení konfigurace zařízení VPN

Nakonfigurujte místní zařízení VPN pomocí konfigurace zařízení VPN.

1. Na stránce virtuální sítě WAN klikněte na **Overview** (Přehled).
2. V horní části stránky **centra – >VPNSite** klikněte na **Stáhnout konfiguraci sítě VPN**. Azure vytvoří účet úložiště ve skupině prostředků ' Microsoft-Network-[location] ', kde umístění je umístění sítě WAN. Až tuto konfiguraci použijete ve svých zařízeních VPN, můžete tento účet úložiště odstranit.
3. Jakmile se dokončí vytváření souboru, můžete ho kliknutím na odkaz stáhnout.
4. Použijte konfiguraci na vaše místní zařízení VPN.

### <a name="understanding-the-vpn-device-configuration-file"></a>Vysvětlení konfiguračního souboru zařízení VPN

Konfigurační soubor zařízení obsahuje nastavení, které se má použít při konfiguraci místního zařízení VPN. Při prohlížení souboru si všimněte následujících informací:

* **vpnSiteConfiguration** – tato část udává podrobnosti o zařízení nastaveném jako lokalita, která se připojuje k virtuální síti WAN. Obsahuje název a veřejnou IP adresu zařízení pobočky.
* **vpnSiteConnections –** V této části najdete informace o následujících nastaveních:

    * **Adresní prostor** virtuální sítě virtuálních rozbočovačů<br>Příklad:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Adresní prostor** virtuálních sítí připojených k rozbočovači<br>Příklad:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * **IP adresy** brány sítě VPN virtuálního rozbočovače. Vzhledem k tomu, že každé připojení vpngateway se skládá ze dvou tunelů v konfiguraci aktivní-aktivní, zobrazí se obě IP adresy uvedené v tomto souboru. V tomto příkladu vidíte pro každou lokalitu položky Instance0 a Instance1.<br>Příklad:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Podrobnosti konfigurace připojení Vpngateway** , jako je protokol BGP, předsdílený klíč atd. PSK je předsdílený klíč, který se automaticky vygeneruje za vás. V případě vlastního předsdíleného klíče můžete připojení upravit na stránce Overview (Přehled).
  
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

### <a name="configuring-your-vpn-device"></a>Konfigurace zařízení VPN

>[!NOTE]
> Pokud pracujete s partnerským řešením pro Virtual WAN, konfigurace zařízení VPN se provede automaticky. Kontroler zařízení získá konfigurační soubor z Azure, použije ho na zařízení a tím nastaví připojení k Azure. To znamená, že nemusíte vědět, jak se zařízení VPN konfiguruje ručně.
>

Pokud potřebujete pokyny ke konfiguraci zařízení, můžete použít pokyny na [stránce se skripty konfigurace zařízení VPN](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts), pokud vezmete v úvahu následující upozornění:

* Pokyny na stránce zařízení VPN nejsou určené pro službu Virtual WAN, můžete ale použít hodnoty služby Virtual WAN z konfiguračního souboru a nakonfigurovat zařízení VPN ručně. 
* Skripty konfigurace zařízení ke stažení, které jsou určené pro službu VPN Gateway, pro službu Virtual WAN nefungují, protože se konfigurace liší.
* Nová virtuální síť WAN podporuje jak IKEv1, tak IKEv2.
* Virtuální síť WAN může používat jak zařízení VPN založená na zásadách, tak i pokyny k zařízením.

## <a name="configure-your-vpn-gateway"></a><a name="gateway-config"></a>Konfigurace služby VPN Gateway

Nastavení služby VPN Gateway můžete kdykoli zobrazit a nakonfigurovat tak, že vyberete **Zobrazit nebo konfigurovat**.

:::image type="content" source="media/virtual-wan-site-to-site-portal/view-configuration-1.png" alt-text="Snímek obrazovky zobrazující stránku VPN (site-to-site) se šipkou ukazující na akci zobrazit/konfigurovat." lightbox="media/virtual-wan-site-to-site-portal/view-configuration-1-expand.png":::

Na stránce **upravit VPN Gateway** můžete zobrazit následující nastavení:

* VPN Gateway veřejné IP adresy (přiděluje Azure)
* VPN Gateway privátní IP adresa (přiřazená Azure)
* VPN Gateway výchozí IP adresu protokolu BGP (přiřazený přes Azure)
* Možnost konfigurace pro vlastní IP adresu protokolu BGP: Toto pole je vyhrazené pro APIPa (automatické přidělování privátních IP adres). Azure podporuje IP adresu BGP v oblastech 169.254.21. * a 169.254.22. *. Azure v těchto rozsahech přijímá připojení BGP, ale vytočí připojení s výchozí IP adresou protokolu BGP.

   :::image type="content" source="media/virtual-wan-site-to-site-portal/view-configuration-2.png" alt-text="Snímek obrazovky zobrazující stránku VPN (site-to-site) se šipkou ukazující na akci zobrazit/konfigurovat." lightbox="media/virtual-wan-site-to-site-portal/view-configuration-2-expand.png":::

## <a name="next-steps"></a>Další kroky

Další informace o službě Virtual WAN najdete v článku [Přehled služby Virtual WAN](virtual-wan-about.md).
