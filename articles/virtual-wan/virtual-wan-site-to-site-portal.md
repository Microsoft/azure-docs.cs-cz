---
title: 'Virtuální SÍŤ Azure: Vytvoření připojení mezi lokalitami'
description: V tomto kurzu se naučíte vytvořit připojení VPN typu site-to-site k Azure pomocí služby Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: b4278cb2e8c5152f522258a37c37acda5efbacf8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239683"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>Kurz: Vytvoření připojení typu site-to-site pomocí služby Azure Virtual WAN

V tomto kurzu se dozvíte, jak se pomocí služby Virtual WAN připojit ke svým prostředkům v Azure přes připojení VPN IPsec/IKE (IKEv1 a IKEv2). Tento typ připojení vyžaduje místní zařízení VPN, které má přiřazenou veřejnou IP adresu. Další informace o službě Virtual WAN najdete v tématu [Přehled služby Virtual WAN](virtual-wan-about.md).

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Vytvoření virtuální sítě WAN
> * Vytvoření rozbočovače
> * Vytvoření lokality
> * Připojení webu k rozbočovači
> * Připojení webu VPN k rozbočovači
> * Připojení virtuální sítě k rozbočovači
> * Stažení konfiguračního souboru
> * Zobrazení virtuální sítě WAN

> [!NOTE]
> Pokud máte hodně lokalit, doporučujeme využít k vytvoření této konfigurace [partnera pro Virtual WAN](https://aka.ms/virtualwan). Pokud se ale vyznáte ve vytváření sítí a umíte nakonfigurovat vlastní zařízení VPN, můžete tuto konfiguraci vytvořit sami.
>

![Diagram virtuální sítě WAN](./media/virtual-wan-about/virtualwan.png)

## <a name="before-you-begin"></a>Než začnete

Před zahájením konfigurace ověřte, že splňujete následující kritéria:

* Máte virtuální síť, ke které se chcete připojit. Ověřte, zda se žádná z podsítí místních sítí nepřekrývá s virtuálními sítěmi, ke kterým se chcete připojit. Pokud chcete vytvořit virtuální síť na webu Azure Portal, přečtěte si [úvodní příručku](../virtual-network/quick-create-portal.md).

* Virtuální síť nemá žádné brány virtuální sítě. Pokud vaše virtuální síť má bránu (vpn nebo ExpressRoute), musíte odebrat všechny brány. Tato konfigurace vyžaduje, aby virtuální sítě byly připojeny místo toho k bráně centra Virtuální WAN.

* Zařiďte rozsah IP adres pro oblast vašeho rozbočovače. Rozbočovač je virtuální síť, která je vytvořena a používávirtuální WAN. Rozsah adres, který zadáte pro rozbočovač, se nemůže překrývat s žádnou z existujících virtuálních sítí, ke kterým se připojujete. Taky se nesmí překrývat s rozsahy adres, ke kterým se připojujete v místním prostředí. Pokud nejste obeznámeni s rozsahy IP adres umístěnými v místní konfiguraci sítě, koordinujte je s někým, kdo vám tyto podrobnosti může poskytnout.

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Vytvoření virtuální sítě WAN

V prohlížeči přejděte na web Azure Portal a přihlaste se pomocí svého účtu Azure.

1. Přejděte na stránku Virtuální wan. Na portálu klikněte na **+ Vytvořit prostředek**. Do vyhledávacího pole zadejte **virtuální wan** a vyberte Enter.
2. Z výsledků vyberte **virtuální wan.** Na stránce Virtuální wan kliknutím na **Vytvořit** otevřete stránku Vytvořit wan.
3. Na stránce **Vytvořit wan** vyplňte na kartě **Základy** následující pole:

   ![Virtuální síť WAN](./media/virtual-wan-site-to-site-portal/vwan.png)

   * **Subscription** (Předplatné) – vyberte předplatné, které chcete použít.
   * **Skupina prostředků** – vytvořit nové nebo použít existující.
   * **Umístění skupiny prostředků** – z rozevíracího souboru zvolte umístění prostředků. Síť WAN je globální prostředek, takže se nenachází v určité oblasti. Přesto je ale potřeba oblast vybrat, abyste mohli snáz spravovat a vyhledávat prostředek sítě WAN, který vytvoříte.
   * **Název** – Zadejte název, který chcete volat wan.
   * **Typ:** Základní nebo standardní. Pokud vytvoříte základní wan, můžete vytvořit pouze základní rozbočovač. Základní rozbočovače jsou schopny pouze připojení k síti VPN.
4. Po vyplnění polí vyberte **Zkontrolovat +Vytvořit**.
5. Jakmile ověření projde, vyberte **Vytvořit** a vytvořte virtuální WAN.

## <a name="create-a-hub"></a><a name="hub"></a>Vytvoření rozbočovače

Rozbočovač je virtuální síť, která může obsahovat brány pro funkce site-to-site, ExpressRoute nebo point-to-site. Po vytvoření centra se vám bude centrum účtovat i v případě, že nepřipojíte žádné servery. Vytvoření brány VPN mezi lokalitami ve virtuálním rozbočovači trvá 30 minut.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="create-a-site"></a><a name="site"></a>Vytvoření lokality

Nyní jste připraveni vytvořit weby odpovídající vašim fyzickým umístěním. Můžete vytvořit libovolný počet lokalit odpovídajících fyzickým umístěním. Pokud máte třeba jednu pobočku v New Yorku, jednu v Londýně a jednu v Los Angeles, vytvoříte tři oddělené lokality. Tyto lokality obsahují koncové body místních zařízení VPN. Ve virtuální matné wan můžete vytvořit až 1000 webů na virtuální centrum. Pokud jste měli více rozbočovačů, můžete vytvořit 1000 na každý z těchto rozbočovačů. Pokud máte zařízení CPE virtual WAN partnera (link insert) CPE, obraťte se na ně, abyste se dozvěděli o jejich automatizaci do Azure. Automatizace obvykle vyžaduje jednoduché možnosti kliknutí pro export rozsáhlých informací o větvích do azure a nastavení připojení z CPE do brány Azure Virtual WAN VPN. Další informace najdete [v tématu Automatizace pokyny od Azure k partnerům CPE](virtual-wan-configure-automation-providers.md).

[!INCLUDE [Create a site](../../includes/virtual-wan-tutorial-s2s-site-include.md)]

## <a name="connect-the-vpn-site-to-the-hub"></a><a name="connectsites"></a>Připojení webu VPN k rozbočovači

V tomto kroku připojíte web VPN k rozbočovači.

[!INCLUDE [Connect VPN sites](../../includes/virtual-wan-tutorial-s2s-connect-vpn-site-include.md)]

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>Připojení virtuální sítě k rozbočovači

V tomto kroku vytvoříte připojení mezi rozbočovačem a virtuální sítí. Uvedený postup zopakujte pro všechny virtuální sítě, které chcete připojit.

1. Na stránce vaší virtuální sítě WAN klikněte na **Připojení k virtuální síti**.
2. Na stránce připojení k virtuální síti klikněte na **+Add connection** (Přidat připojení).
3. Na stránce **Add connection** (Přidat připojení) zadejte údaje do následujících polí:

    * **Connection name** (Název připojení) – zadejte název připojení.
    * **Hubs** (Rozbočovače) – vyberte rozbočovač, který chcete k tomuto připojení přidružit.
    * **Subscription** (Předplatné) – ověřte předplatné.
    * **Virtual network** (Virtuální síť) – vyberte virtuální síť, kterou chcete připojit k tomuto rozbočovači. Virtuální síť nesmí mít existující bránu virtuální sítě.
4. Chcete-li vytvořit připojení k virtuální síti, klepněte na tlačítko **OK.**

## <a name="download-vpn-configuration"></a><a name="device"></a>Stažení konfigurace zařízení VPN

Nakonfigurujte místní zařízení VPN pomocí konfigurace zařízení VPN.

1. Na stránce virtuální sítě WAN klikněte na **Overview** (Přehled).
2. V horní části stránky **->webu VPN** klikněte na **Stáhnout konfiguraci VPN**. Azure vytvoří účet úložiště ve skupině prostředků "microsoft-network-[location]", kde umístění je umístění WAN. Až tuto konfiguraci použijete ve svých zařízeních VPN, můžete tento účet úložiště odstranit.
3. Jakmile se dokončí vytváření souboru, můžete ho kliknutím na odkaz stáhnout.
4. Použijte konfiguraci na místní zařízení VPN.

### <a name="understanding-the-vpn-device-configuration-file"></a>Vysvětlení konfiguračního souboru zařízení VPN

Konfigurační soubor zařízení obsahuje nastavení, které se má použít při konfiguraci místního zařízení VPN. Při prohlížení souboru si všimněte následujících informací:

* **vpnSiteConfiguration** – tato část udává podrobnosti o zařízení nastaveném jako lokalita, která se připojuje k virtuální síti WAN. Obsahuje název a veřejnou IP adresu zařízení pobočky.
* **vpnSiteConnections -** Tato část obsahuje informace o následujících nastaveních:

    * **Adresní prostor** virtuální sítě virtuálních rozbočovačů<br>Příklad:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Adresní prostor** virtuálních sítí připojených k rozbočovači<br>Příklad:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * **IP adresy** brány sítě VPN virtuálního rozbočovače. Vzhledem k tomu, že každé připojení brány vpngateway se skládá ze dvou tunelových propojení v konfiguraci aktivní a aktivní, uvidíte obě IP adresy uvedené v tomto souboru. V tomto příkladu vidíte pro každou lokalitu položky Instance0 a Instance1.<br>Příklad:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Podrobnosti konfigurace připojení Vpngateway,** jako je BGP, předsdílený klíč atd. PSK je předsdílený klíč, který je automaticky generován pro vás. V případě vlastního předsdíleného klíče můžete připojení upravit na stránce Overview (Přehled).
  
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
* Nová virtuální wan může podporovat iKEv1 a IKEv2.
* Virtuální síť WAN může používat zařízení VPN založená na zásadách i na trase a pokyny zařízení.

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Zobrazení virtuální sítě WAN

1. Přejděte na virtuální síť WAN.
2. Na stránce **Přehled** představuje každý bod na mapě rozbočovač. Najeďte přes libovolný bod a zobrazte souhrn stavu centra, stav připojení a bajty dovnitř a ven.
3. V části Rozbočovače a připojení můžete zobrazit stav rozbočovače, weby VPN atd. Můžete kliknout na konkrétní název rozbočovače a přejít na stránku VPN pro další podrobnosti.

## <a name="next-steps"></a>Další kroky

Další informace o službě Virtual WAN najdete v článku [Přehled služby Virtual WAN](virtual-wan-about.md).
