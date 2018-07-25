---
title: Vytvoření připojení typu site-to-site k Azure pomocí virtuální sítě WAN Azure | Microsoft Docs
description: V tomto kurzu se naučíte vytvořit připojení VPN typu site-to-site k Azure pomocí služby Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 07/13/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: 1b61c335dec2c641862c08fd6f752d78b2ee5866
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39056660"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan-preview"></a>Kurz: Vytvoření připojení typu site-to-site pomocí Azure Virtual WAN (verze Preview)

V tomto kurzu se dozvíte, jak se připojit ke svým prostředkům v Azure přes připojení VPN IPsec/IKE (IKEv2) pomocí služby Virtual WAN. Tento typ připojení vyžaduje místní zařízení VPN, které má přiřazenou veřejnou IP adresu. Další informace o službě Virtual WAN najdete v článku [Přehled služby Virtual WAN](virtual-wan-about.md)

> [!NOTE]
> Pokud máte hodně lokalit, doporučujeme využít k vytvoření této konfigurace [partnera pro Virtual WAN](https://aka.ms.virtualwan). Pokud se ale vyznáte ve vytváření sítí a umíte nakonfigurovat vlastní zařízení VPN, můžete tuto konfiguraci vytvořit sami.
>

![Diagram služby Virtual WAN](./media/virtual-wan-about/virtualwan.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření sítě WAN
> * Vytvoření lokality
> * Vytvoření rozbočovače
> * Připojení rozbočovače k lokalitě
> * Připojení virtuální sítě k rozbočovači
> * Stažení a použití konfigurace zařízení VPN
> * Zobrazení virtuální sítě WAN
> * Zobrazení stavu prostředků
> * Monitorování připojení

> [!IMPORTANT]
> Azure Virtual WAN je aktuálně ve spravované verzi Public Preview. Pokud chcete Virtual WAN používat, [zaregistrujte si verzi Preview](#enroll).
>
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="before-you-begin"></a>Než začnete

Před zahájením konfigurace ověřte, že splňujete následující kritéria:

* Ujistěte se, že máte kompatibilní zařízení VPN založené na trasách, které podporuje protokol IKEv2, a někoho, kdo je umí nakonfigurovat. Pokud spolupracujete s partnerem pro Virtual WAN, nastavení konfigurace se vytvoří automaticky a vy si nemusíte dělat starosti s ruční konfigurací zařízení.
* Ověřte, že máte veřejnou IPv4 adresu pro vaše zařízení VPN. Tato IP adresa nesmí být umístěná za překladem adres (NAT).
* Pokud už máte virtuální síť, ke které se chcete připojit, zkontrolujte, jestli se žádná z podsítí vaší místní sítě nepřekrývá s virtuálními sítěmi, ke kterým se chcete připojit. Virtuální síť nevyžaduje podsíť s bránou a nesmí mít žádné brány virtuální sítě. Pokud nemáte virtuální síť, můžete ji vytvořit pomocí postupu v tomto článku.
* Zařiďte rozsah IP adres pro oblast vašeho rozbočovače. Rozbočovač je virtuální síť a rozsah adres, který pro oblast rozbočovače zadáte, se nesmí překrývat s žádnými existujícími virtuálními sítěmi, ke kterým se připojujete. Taky se nesmí překrývat s rozsahy adres, ke kterým se připojujete v místním prostředí. Pokud neznáte rozsahy IP adres v konfiguraci vaší místní sítě, budete se muset spojit s někým, kdo vám s tím pomůže.
* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="enroll"></a>1. Registrace verze Preview

Než budete moct nakonfigurovat Virtual WAN, je nejdřív potřeba zaregistrovat vaše předplatné ve verzi Preview. Jinak nebudete moct na portálu pracovat se službou Virtual WAN. Pokud se chcete zaregistrovat, pošlete e-mail s ID předplatného na adresu **azurevirtualwan@microsoft.com**. Jakmile se vaše předplatné zaregistruje, dostanete e-mail s potvrzením.

## <a name="vnet"></a>2. Vytvoření virtuální sítě

Pokud ještě nemáte virtuální síť, můžete si ji rychle vytvořit pomocí PowerShellu. Virtuální síť můžete vytvořit i pomocí webu Azure Portal.

* Nezapomeňte si ověřit, že se adresní prostor pro virtuální síť, kterou vytvoříte, nepřekrývá s žádným z rozsahů adres pro jiné virtuální sítě, ke kterým se chcete připojit, ani s adresními prostory místní sítě. 
* Pokud už virtuální síť máte, ověřte si, jestli splňuje povinná kritéria a nemá bránu virtuální sítě.

Virtuální síť můžete snadno vytvořit, když kliknutím na „Vyzkoušet“ v tomto článku otevřete konzolu PowerShellu. Upravte hodnoty, potom příkazy zkopírujte a vložte je do okna konzoly.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Upravte příkazy PowerShellu a potom vytvořte skupinu prostředků.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>Vytvoření virtuální sítě

Upravte příkazy PowerShellu a vytvořte virtuální síť kompatibilní s vaším prostředím.

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzureRmVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```

## <a name="wan"></a>3. Vytvoření virtuální sítě WAN

1. V prohlížeči přejděte na web [Azure Portal](https://portal.azure.com) a přihlaste se pomocí svého účtu Azure.
2. V současné době najdete službu Virtual WAN tak, že přejdete na **Všechny služby** a vyhledáte Virtual WAN. Můžete taky zadat Virtual WAN do pole hledání v horní části webu Azure Portal. Příslušnou stránku otevřete kliknutím na **Virtual WAN**.
3. Kliknutím na **Create** (Vytvořit) otevřete stránku **Create WAN** (Vytvořit síť WAN). Pokud není stránka dostupná, znamená to, že jste ještě neprošli schválením pro tuto verzi Preview.

  ![Create WAN (Vytvořit síť WAN)](./media/virtual-wan-site-to-site-portal/createwan.png)
4. Na stránce Create WAN (Vytvořit síť WAN) zadejte údaje do následujících polí.

  * **Name** (Název) – vyberte název, který chcete pro síť WAN použít.
  * **Subscription** (Předplatné) – vyberte předplatné, které chcete použít.
  * **Resource Group** (Skupina prostředků) – vytvořte novou nebo použijte existující.
  * **Resource Location** (Umístění prostředku) – v rozevíracím seznamu vyberte umístění prostředku. Síť WAN je globální prostředek, takže se nenachází v určité oblasti. Přesto je ale potřeba oblast vybrat, abyste mohli snáz spravovat a vyhledávat prostředek sítě WAN, který vytvoříte.
5. Vytvořte síť WAN kliknutím na **Create** (Vytvořit).

## <a name="site"></a>4. Vytvoření lokality

Můžete vytvořit libovolný počet lokalit odpovídajících fyzickým umístěním. Pokud máte třeba jednu pobočku v New Yorku, jednu v Londýně a jednu v Los Angeles, vytvoříte tři oddělené lokality. Tyto lokality obsahují koncové body místních zařízení VPN. V tuto chvíli můžete pro každou lokalitu určit jenom jeden privátní adresní prostor.

1. Přejděte na **All resources** (Všechny prostředky).
2. Klikněte na virtuální síť WAN, kterou jste vytvořili.
3. V horní části stránky klikněte na **+Create site** (Vytvořit lokalitu). Otevře se stránka **Create site** (Vytvořit lokalitu).

  ![nová lokalita](media/virtual-wan-site-to-site-portal/createsite.png)
4. Na stránce **Create site** (Vytvořit lokalitu) zadejte údaje do následujících polí:

  *  **Name** (Název) – název, kterým chcete odkazovat na místní lokalitu.
  *  **Public IP address** (Veřejná IP adresa) – veřejná IP adresa zařízení VPN, které se nachází v místní lokalitě.
  *  **Private address space** (Privátní adresní prostor) – prostor IP adres, který se nachází v místní lokalitě. Provoz určený do tohoto adresního prostoru se přesměruje do místní lokality.
  *  **Subscription** (Předplatné) – ověřte předplatné.
  *  **Resource Group** (Skupina prostředků) – skupina prostředků, kterou chcete použít.
5. Další nastavení zobrazíte kliknutím na **Show advanced** (Zobrazit rozšířené nastavení). Pomocí volitelného pole **Enable BGP** (Povolit BGP) můžete povolit tuto funkci u všech připojení vytvořených pro tuto lokalitu v Azure. Můžete taky zadat údaje do volitelného pole **Device information** (Informace o zařízení). Týmu Azure to může pomoct lépe pochopit vaše prostředí a v budoucnu přidávat další možnosti optimalizace, případně vám asistovat při řešení potíží.

  ![Protokol BGP](media/virtual-wan-site-to-site-portal/sitebgp.png)
6. Vytvořte lokalitu kliknutím na **Confirm** (Potvrdit).
7. Uvedené kroky zopakujte pro všechny lokality, které chcete vytvořit.

## <a name="hub"></a>5. Vytvoření rozbočovače a připojení lokalit

1. Na stránce virtuální sítě WAN klikněte na **Sites** (Lokality).
2. V části **Unassociated sites** (Nepřidružené lokality) se zobrazuje seznam lokalit, které ještě nejsou připojené k rozbočovači.
3. Vyberte lokality, které chcete přidružit.
4. V rozevíracím seznamu vyberte oblast, ke které má být rozbočovač přidružený. Rozbočovač byste měli přidružit k oblasti, ve které se nacházejí virtuální sítě, které chcete připojit.
5. Klikněte na **Confirm** (Potvrdit). Pokud ještě v této oblasti nemáte žádný rozbočovač, automaticky se vytvoří virtuální síť s virtuálním rozbočovačem. V takovém případě se zobrazí stránka **Create regional hubs** (Vytvořit oblastní rozbočovače).
6. Na stránce **Create regional hubs** (Vytvořit oblastní rozbočovače) zadejte rozsah adres pro virtuální síť rozbočovače. Tato virtuální síť bude obsahovat služby rozbočovače. Rozsah, který tu zadáte, musí být rozsahem privátních IP adres a nesmí se překrývat s žádným z místních adresních prostorů ani adresních prostorů virtuální sítě. Potom se ve virtuální síti rozbočovače vytvoří koncový bod sítě VPN. (Na portálu je dostupné jenom automatické vytváření rozbočovačů a bran.)
7. Klikněte na možnost **Vytvořit**.

## <a name="vnet"></a>6. Připojení virtuální sítě k rozbočovači

V tomto kroku vytvoříte partnerské připojení mezi rozbočovačem a určitou virtuální sítí. Uvedený postup zopakujte pro všechny virtuální sítě, které chcete připojit.

1. Na stránce vaší virtuální sítě WAN klikněte na **Virtual network connection** (Připojení k virtuální síti).
2. Na stránce připojení k virtuální síti klikněte na **+Add connection** (Přidat připojení).
3. Na stránce **Add connection** (Přidat připojení) zadejte údaje do následujících polí:

    * **Connection name** (Název připojení) – zadejte název připojení.
    * **Hubs** (Rozbočovače) – vyberte rozbočovač, který chcete k tomuto připojení přidružit.
    * **Subscription** (Předplatné) – ověřte předplatné.
    * **Virtual network** (Virtuální síť) – vyberte virtuální síť, kterou chcete připojit k tomuto rozbočovači. Virtuální síť nesmí mít existující bránu virtuální sítě.

## <a name="device"></a>7. Stažení konfigurace zařízení VPN

Nakonfigurujte místní zařízení VPN pomocí konfigurace zařízení VPN.

1. Na stránce virtuální sítě WAN klikněte na **Overview** (Přehled).
2. V horní části stránky přehledu klikněte na **Download VPN configuration** (Stáhnout konfiguraci VPN). Azure vytvoří účet úložiště ve skupině prostředků microsoft-network-[umístění], kde umístění znamená umístění sítě WAN. Až tuto konfiguraci použijete ve svých zařízeních VPN, můžete tento účet úložiště odstranit.
3. Jakmile se dokončí vytváření souboru, můžete ho kliknutím na odkaz stáhnout.
4. Použijte konfiguraci ve svém zařízení VPN.

### <a name="understanding-the-vpn-device-configuration-file"></a>Vysvětlení konfiguračního souboru zařízení VPN

Konfigurační soubor zařízení obsahuje nastavení, které se má použít při konfiguraci místního zařízení VPN. Při prohlížení souboru si všimněte následujících informací:

* **vpnSiteConfiguration** – tato část udává podrobnosti o zařízení nastaveném jako lokalita, která se připojuje k virtuální síti WAN. Obsahuje název a veřejnou IP adresu zařízení pobočky.
* **vpnSiteConnections** – tato část obsahuje následující informace:

    * **Adresní prostor** virtuální sítě virtuálních rozbočovačů<br>Příklad:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Adresní prostor** virtuálních sítí připojených k rozbočovači<br>Příklad:

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

### <a name="configuring-your-vpn-device"></a>Konfigurace zařízení VPN

>[!NOTE]
> Pokud pracujete s partnerským řešením Virtual WAN, konfigurace zařízení VPN se nastaví automaticky ve chvíli, kdy kontroler zařízení získá konfigurační soubor z Azure a použije ho v zařízení, aby vytvořil připojení k Azure. To znamená, že nemusíte vědět, jak se zařízení VPN konfiguruje ručně.
>

Pokud potřebujete pokyny ke konfiguraci zařízení, můžete použít pokyny na [stránce se skripty konfigurace zařízení VPN](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts), pokud vezmete v úvahu následující upozornění:

* Pokyny na stránce zařízení VPN nejsou určené pro službu Virtual WAN, můžete ale použít hodnoty služby Virtual WAN z konfiguračního souboru a nakonfigurovat zařízení VPN ručně. 
* Skripty konfigurace zařízení ke stažení, které jsou určené pro službu VPN Gateway, pro službu Virtual WAN nefungují, protože se konfigurace liší.
* Virtual WAN podporuje jenom protokol IKEv2, ne IKEv1.
* Virtual WAN smí používat jenom zařízení VPN a pokyny pro zařízení založené na trasách.

## <a name="viewwan"></a>8. Zobrazení virtuální sítě WAN

1. Přejděte na virtuální síť WAN.
2. Na stránce Overview (Přehled) každý bod na mapě představuje jeden rozbočovač. Podržením ukazatele na některém z těchto bodů zobrazíte souhrn stavu rozbočovače.
3. V části Hubs and connections (Rozbočovače a připojení) můžete zjistit stav rozbočovače, lokalitu, oblast, stav připojení VPN a přijaté a odeslané bajty.

## <a name="viewhealth"></a>9. Zobrazení stavu prostředků

1. Přejděte na svoji síť WAN.
2. Na stránce sítě WAN v části **SUPPORT + Troubleshooting** (Podpora a řešení potíží) klikněte na **Health** (Stav) a prohlédněte si stav svého prostředku.

## <a name="connectmon"></a>10. Monitorování připojení

Vytvořte připojení pro monitorování komunikace mezi virtuálním počítačem Azure a vzdálenou lokalitou. Informace o tom, jak nastavit monitorování připojení, najdete v článku [Monitorování síťové komunikace](~/articles/network-watcher/connection-monitor.md). Do pole zdroje zadejte IP adresu virtuálního počítače v Azure a cílovou IP adresou je IP adresa lokality.

## <a name="cleanup"></a>11. Vyčištění prostředků

Pokud už tyto prostředky nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít rutinu [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Položku myResourceGroup nahraďte názvem vaší skupiny prostředků a spusťte následující příkaz PowerShellu:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="feedback"></a>Zpětná vazba na verzi Preview

Vaší zpětné vazby si velmi vážíme. Chcete-li oznámit problémy nebo poskytnout zpětnou vazbu (kladnou i zápornou) na službu Virtual WAN, odešlete e-mail na adresu <azurevirtualwan@microsoft.com>. V řádku předmětu uveďte název vaší společnosti v hranatých závorkách „[]“. Pokud oznamujete problém, nezapomeňte taky uvést ID předplatného.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření sítě WAN
> * Vytvoření lokality
> * Vytvoření rozbočovače
> * Připojení rozbočovače k lokalitě
> * Připojení virtuální sítě k rozbočovači
> * Stažení a použití konfigurace zařízení VPN
> * Zobrazení virtuální sítě WAN
> * Zobrazení stavu prostředků
> * Monitorování připojení

Další informace o službě Virtual WAN najdete v článku [Přehled služby Virtual WAN](virtual-wan-about.md).