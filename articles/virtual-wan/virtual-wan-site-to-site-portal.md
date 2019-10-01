---
title: Vytvoření připojení typu Site-to-site k Azure pomocí Azure Virtual WAN | Microsoft Docs
description: V tomto kurzu se dozvíte, jak pomocí Azure Virtual WAN vytvořit připojení VPN typu Site-to-site k Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 07/25/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: 357cc23c9dedb49dfd19dc897102762066ffd1b2
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679349"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>Kurz: vytvoření připojení typu Site-to-site pomocí Azure Virtual WAN

V tomto kurzu se dozvíte, jak pomocí virtuální sítě WAN se připojit k prostředkům v Azure přes připojení VPN pomocí protokolu IPsec/IKE (IKEv1 a IKEv2). Tento typ připojení vyžaduje místní zařízení VPN, které má přiřazenou externě veřejnou IP adresu. Další informace o virtuální síti WAN najdete v tématu [Přehled služby Virtual WAN](virtual-wan-about.md).

> [!NOTE]
> Máte-li mnoho webů, obvykle byste k vytvoření této konfigurace použili [virtuální síť WAN partner](https://aka.ms/virtualwan) . Tuto konfiguraci si ale můžete vytvořit sami, pokud jste obeznámení se sítí a zdatní při konfiguraci vlastního zařízení VPN.
>

![Diagram virtuální sítě WAN](./media/virtual-wan-about/virtualwan.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření sítě WAN
> * Vytvoření webu
> * Vytvoření centra
> * Připojit rozbočovač k lokalitě
> * Vytvoření kompatibilní virtuální sítě (pokud ji ještě nemáte)
> * Připojení virtuální sítě k centru
> * Stažení a použití konfigurace zařízení VPN
> * Zobrazení virtuální sítě WAN
> * Zobrazit stav prostředku
> * Monitorování připojení

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="openvwan"></a>1. vytvoření virtuální sítě WAN

V prohlížeči přejděte na [Azure Portal](https://aka.ms/azurevirtualwanpreviewfeatures) a přihlaste se pomocí svého účtu Azure.

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="site"></a>2. vytvoření webu

Vytvořte tolik webů, kolik potřebujete, aby odpovídaly vašim fyzickým umístěním. Například pokud máte pobočku na NY, pobočku v Londýně a na pobočce a LA, vytvoříte tři samostatné lokality. Tyto lokality obsahují vaše místní koncové body zařízení VPN. V tuto chvíli můžete pro svůj web zadat jenom jeden privátní adresní prostor.

1. Klikněte na síť WAN, kterou jste vytvořili. Na stránce WAN v části **virtuální síť WAN**klikněte na **sítě VPN** a otevřete stránku sítě VPN.
2. Na stránce sítě **VPN** klikněte na **+ vytvořit lokalitu**.
3. Na stránce **vytvořit web** vyplňte následující pole:

   * **Název** – název, podle kterého chcete odkazovat na místní lokalitu.
   * **Veřejná IP adresa** – veřejná IP adresa zařízení VPN, která se nachází na vaší místní lokalitě.
   * **Soukromý adresní prostor** – jedná se o adresní prostor IP adres umístěný na vaší místní lokalitě. Provoz určený pro tento adresní prostor je směrován do vaší místní lokality.
   * **Předplatné** – ověřte předplatné.
   * **Skupina prostředků** – skupina prostředků, kterou chcete použít.
   * **Poloha**
4. Kliknutím na **Zobrazit podrobnosti** zobrazíte další nastavení. 

   Můžete vybrat protokol **BGP** a povolit protokol BGP. tím se povolí funkce protokolu BGP u všech připojení vytvořených pro tento web v Azure. Konfigurace protokolu BGP ve virtuální síti WAN je rovnocenná konfiguraci protokolu BGP v bráně Azure VPN. Vaše místní adresa partnerského uzlu BGP *nesmí* být stejná jako veřejná IP adresa vaší sítě VPN k zařízení nebo adresní prostor virtuální sítě sítě VPN. Pro IP adresu partnerského uzlu BGP použijte jinou IP adresu na zařízení VPN. Může to být adresa přiřazená k rozhraní zpětné smyčky v zařízení. Nejedná se však o APIPA (169,254). *x*. *x*) adresa. Zadejte tuto adresu v odpovídající bráně místní sítě představující umístění. Požadavky protokolu BGP najdete v tématu [informace o protokolu BGP s Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md).

   Můžete také zadat **informace o zařízení** (volitelné pole). Díky tomu může tým Azure lépe pochopit vaše prostředí, aby v budoucnu přidal další možnosti optimalizace, nebo vám pomůže při odstraňování potíží.
   
5. Klikněte na **Potvrdit**.
6. Po kliknutí na tlačítko **Potvrdit**se zobrazí stav na stránce weby sítě VPN. Lokalita bude přecházet od **zřizování** po **zřízení**.

## <a name="hub"></a>3. vytvoření centra

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="associate"></a>4. přidružte lokality k centru

Centra by obecně měla být přidružena k lokalitám, které jsou ve stejné oblasti, ve které se nachází virtuální síť.

1. Na stránce sítě **VPN** vyberte web nebo weby, které chcete přidružit k centru, a pak klikněte na **+ nové přidružení centra**.
2. Na stránce **přidružte weby k jedné nebo více** centrům vyberte z rozevíracího seznamu rozbočovač. Lokalitu můžete přidružit k dalším rozbočovačům kliknutím na **+ Přidat přidružení**.
3. Můžete taky přidat konkrétního **PSK** , nebo použít výchozí.
4. Klikněte na **Potvrdit**.
5. Stav připojení můžete zobrazit na stránce **weby sítě VPN** .

## <a name="vnet"></a>5. vytvoření virtuální sítě

Pokud ještě nemáte virtuální síť, můžete ji rychle vytvořit pomocí PowerShellu nebo Azure Portal. Pokud již máte virtuální síť, ověřte, že splňuje požadovaná kritéria a nemá bránu virtuální sítě.

[!INCLUDE [Create a virtual network](../../includes/virtual-wan-tutorial-vnet-include.md)]

## <a name="vnet"></a>6. Připojte svoji virtuální síť k centru

V tomto kroku vytvoříte připojení partnerského vztahu mezi vaším rozbočovačem a virtuální sítí. Opakujte tyto kroky pro každou virtuální síť, kterou chcete připojit.

1. Na stránce pro virtuální síť WAN klikněte na **připojení k virtuální síti**.
2. Na stránce připojení k virtuální síti klikněte na **+ Přidat připojení**.
3. Na stránce **Přidat připojení** vyplňte následující pole:

    * **Název připojení** – pojmenování připojení
    * **Centra** – vyberte centrum, které chcete přidružit k tomuto připojení.
    * **Předplatné** – ověřte předplatné.
    * **Virtuální síť** – vyberte virtuální síť, kterou chcete připojit k tomuto rozbočovači. Virtuální síť už nemůže mít existující bránu virtuální sítě.
4. Kliknutím na tlačítko **OK** vytvořte připojení partnerského vztahu.

## <a name="device"></a>7. Stáhněte si konfiguraci sítě VPN.

Ke konfiguraci místního zařízení VPN použijte konfiguraci zařízení VPN.

1. Na stránce pro virtuální síť WAN klikněte na **Přehled**.
2. V horní části stránky přehled klikněte na **Stáhnout konfiguraci sítě VPN**. Azure vytvoří účet úložiště ve skupině prostředků ' Microsoft-Network-[location] ', kde umístění je umístění sítě WAN. Po použití konfigurace na zařízení VPN můžete tento účet úložiště odstranit.
3. Po vytvoření souboru můžete kliknout na odkaz a stáhnout si ho.
4. Použijte konfiguraci na zařízení VPN.

### <a name="understanding-the-vpn-device-configuration-file"></a>Porozumění konfiguračnímu souboru zařízení VPN

Konfigurační soubor zařízení obsahuje nastavení, která se použijí při konfiguraci místního zařízení VPN. Při prohlížení tohoto souboru si všimněte následujících informací:

* **vpnSiteConfiguration –** Tato část označuje podrobnosti o zařízení, které se nastavily jako lokalita připojující se k virtuální síti WAN. Obsahuje název a veřejnou IP adresu zařízení pobočky.
* **vpnSiteConnections –** V této části najdete informace o následujících nastaveních:

    * **Adresní prostor** virtuálních sítí s virtuálními rozbočovači<br>Příklad:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Adresní prostor** virtuální sítě, který je připojený k centru<br>Příklad:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * **IP adresy** virtuálního centra vpngateway. Vzhledem k tomu, že každé připojení vpngateway se skládá ze dvou tunelů v konfiguraci aktivní-aktivní, zobrazí se obě IP adresy uvedené v tomto souboru. V tomto příkladu se pro každou lokalitu zobrazí "Instance0" a "položku instance1".<br>Příklad:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Podrobnosti konfigurace připojení Vpngateway** , jako je protokol BGP, předsdílený klíč atd. PSK je předsdílený klíč, který se automaticky vygeneruje za vás. Připojení můžete vždycky upravit na stránce Přehled pro vlastní PSK.
  
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

### <a name="configuring-your-vpn-device"></a>Konfigurace zařízení VPN

>[!NOTE]
> Pokud pracujete s virtuálním partnerem sítě WAN, dojde k automatické konfiguraci zařízení VPN. Řadič zařízení získá konfigurační soubor z Azure a vztahuje se na zařízení, aby se nastavilo připojení k Azure. To znamená, že nemusíte znát způsob ruční konfigurace zařízení VPN.
>

Pokud potřebujete pokyny ke konfiguraci zařízení, můžete použít pokyny na [stránce skripty pro konfiguraci zařízení VPN](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) s následujícími upozorněními:

* Pokyny na stránce zařízení VPN nejsou napsány pro virtuální síť WAN, ale můžete použít hodnoty virtuální sítě WAN z konfiguračního souboru a ručně nakonfigurovat vaše zařízení VPN. 
* Skripty pro konfiguraci zařízení ke stažení, které jsou určené pro VPN Gateway, nefungují pro virtuální síť WAN, protože konfigurace se liší.
* Nová virtuální síť WAN podporuje jak IKEv1, tak IKEv2.
* Virtuální síť WAN může používat jenom zařízení VPN založená na trasách a pokyny pro zařízení.

## <a name="viewwan"></a>8. Podívejte se na virtuální síť WAN

1. Přejděte k virtuální síti WAN.
2. Na stránce Přehled představuje každý bod na mapě rozbočovač. Pokud chcete zobrazit souhrn stavu centra, najeďte myší na libovolný bod.
3. V části centra a připojení můžete zobrazit stav centra, lokalitu, oblast, stav připojení VPN a v a v bajtech.

## <a name="viewhealth"></a>9. zobrazení stavu prostředku

1. Přejděte do sítě WAN.
2. Na stránce sítě WAN klikněte v části **Podpora a řešení potíží** na **stav** a zobrazení prostředku.

## <a name="connectmon"></a>10. monitorování připojení

Vytvořte připojení pro monitorování komunikace mezi virtuálním počítačem Azure a vzdáleným webem. Informace o tom, jak nastavit monitorování připojení, najdete v tématu [monitorování síťové komunikace](~/articles/network-watcher/connection-monitor.md). Pole zdroj je IP adresa virtuálního počítače v Azure a cílová IP adresa je adresa IP lokality.

## <a name="cleanup"></a>11. vyčištění prostředků

Pokud už tyto prostředky nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít [příkaz Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) . Nahraďte "myResourceGroup" názvem vaší skupiny prostředků a spusťte následující příkaz prostředí PowerShell:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak:

> [!div class="checklist"]
> * Vytvoření sítě WAN
> * Vytvoření webu
> * Vytvoření centra
> * Připojit rozbočovač k lokalitě
> * Připojení virtuální sítě k centru
> * Stažení a použití konfigurace zařízení VPN
> * Zobrazení virtuální sítě WAN
> * Zobrazit stav prostředku
> * Monitorování připojení

Další informace o virtuální síti WAN najdete na stránce s [přehledem virtuální sítě WAN](virtual-wan-about.md) .
