---
title: Vytvoření připojení site-to-site VPN mezi dvěma virtuálními sítěmi v různých prostředích Azure Stack Development Kit | Dokumentace Microsoftu
description: Podrobný postup, který správce cloudu se použije k vytvoření připojení site-to-site VPN mezi dvěma prostředími Azure Stack Development Kit jedním uzlem.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3f1b4e02-dbab-46a3-8e11-a777722120ec
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/12/2018
ms.author: sethm
ms.reviewer: scottnap
ROBOTS: NOINDEX
ms.openlocfilehash: b17b1b077a1acac5ddb0e9eea8c4a2cf44697979
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078898"
---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-azure-stack-development-kit-environments"></a>Vytvoření připojení site-to-site VPN mezi dvěma virtuálními sítěmi v různých prostředích Azure Stack Development Kit
## <a name="overview"></a>Přehled
V tomto článku se dozvíte, jak vytvořit připojení site-to-site VPN mezi dvěma virtuálními sítěmi ve dvou samostatných prostředích Azure Stack Development Kit. Když konfigurujete připojení, se dozvíte, jak fungují brány VPN Gateway ve službě Azure Stack.

### <a name="connection-diagram"></a>Diagram připojení
Následující diagram znázorňuje konfiguraci připojení by měl vypadat až budete hotovi.

![Konfigurace připojení Site-to-site VPN](media/azure-stack-create-vpn-connection-one-node-tp2/OneNodeS2SVPN.png)

### <a name="before-you-begin"></a>Než začnete
K dokončení konfigurace připojení, zkontrolujte, že máte následující položky než začnete:

* Dva servery a další nezbytné součásti, které splňují požadavky na hardware Azure Stack Development Kit, jak je popsáno v [rychlý start: vyhodnocení Azure Stack Development Kit](azure-stack-deploy-overview.md). 
* [Azure Stack Development Kit](https://azure.microsoft.com/overview/azure-stack/try/) balíček pro nasazení.

## <a name="deploy-the-azure-stack-development-kit-environments"></a>Nasazení prostředí Azure Stack Development Kit
K dokončení konfigurace připojení, je nutné nasadit dvě prostředí Azure Stack Development Kit.
> [!NOTE] 
> Pro každý Azure Stack Development Kit, můžete nasadit, postupujte [pokyny k nasazení](azure-stack-run-powershell-script.md). V tomto článku se nazývají prostředích Azure Stack Development Kit *POC1* a *POC2*.


## <a name="prepare-an-offer-on-poc1-and-poc2"></a>Příprava nabídky v POC1 a POC2
V POC1 a POC2 připravte tak, aby uživatel může předplacení nabídky a nasazení virtuálních počítačů v rámci nabídky. Informace o tom, jak vytvořit nabídku najdete v tématu [zpřístupnit virtuálních počítačů pro vaše uživatele Azure stacku](azure-stack-tutorial-tenant-vm.md).

## <a name="review-and-complete-the-network-configuration-table"></a>Zkontrolujte a dokončete tabulce Konfigurace sítě
Následující tabulka shrnuje konfiguraci sítě pro obou prostředích Azure Stack Development Kit. Pomocí postupu, který se zobrazí pod tabulkou přidat externí BGPNAT adresu, která jsou specifická pro vaši síť.

**Síťové konfigurace tabulky**
|   |POC1|POC2|
|---------|---------|---------|
|Název virtuální sítě     |VNET-01|VNET-02 |
|Adresní prostor virtuální sítě |10.0.10.0/23|10.0.20.0/23|
|Název podsítě     |Podsíť-01|Podsíť-02|
|Rozsah adres podsítě|10.0.10.0/24 |10.0.20.0/24 |
|Podsíť brány     |10.0.11.0/24|10.0.21.0/24|
|Externí adresu BGPNAT     |         |         |

> [!NOTE]
> Externí BGPNAT IP adres v ukázkovém prostředí je 10.16.167.195 pro POC1 a 10.16.169.131 pro POC2. Pomocí následujícího postupu určete externí BGPNAT IP adres pro Azure Stack Development Kit hostitelů a pak je přidejte do předchozí tabulky konfigurace sítě.


### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>Získání IP adresy externího adaptéru virtuálního počítače pro překlad adres
1. Přihlaste se k fyzickému počítači Azure Stack pro POC1.
2. Upravte následující kód Powershellu k nahrazení hesla správce a spusťte kód na hostiteli POC:

   ```powershell
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "<your administrator password>" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "AzS-bgpnat01" `
    -Password $Password
   ```
3. Přidáte IP adresu do tabulky konfigurace sítě, která se zobrazí v předchozí části.

4. Opakujte tento postup v POC2.

## <a name="create-the-network-resources-in-poc1"></a>Vytvoření síťových prostředků v POC1
Teď vytvoříte POC1 síťové prostředky, které budete potřebovat pro vytvoření vaší brány. Následující pokyny ukazují, jak vytvořit prostředky pomocí portálu user portal. Kód Powershellu můžete použít také k vytvoření prostředků.

![Pracovní postup, který se používá k vytváření prostředků](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="sign-in-as-a-tenant"></a>Přihlaste se jako tenant
Správce služeb můžete přihlásit jako tenant k testovací plány, nabídky a předplatné, které můžou jeho tenanti používat. Pokud ho ještě nemáte, [vytvořte si účet tenanta](azure-stack-add-new-user-aad.md) před přihlášením.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Vytvoření virtuální sítě a podsítě virtuálních počítačů
1. Pro přihlášení k portálu user portal pomocí účtu tenanta.
2. Na portálu user portal, vyberte **+ vytvořit prostředek**.
3. Přejděte na **Marketplace**a pak vyberte **sítě**.
4. Vyberte **virtuální síť**.
5. Pro **název**, **adresní prostor**, **název podsítě**, a **rozsah adres podsítě**, použijte hodnoty, které se zobrazí dříve v síti Konfigurace tabulky.
6. V **předplatné**, zobrazí se předplatné, které jste vytvořili dříve.
7. Pro **skupiny prostředků**, můžete vytvořit skupinu prostředků nebo pokud ještě nemáte, vyberte **použít existující**.
8. Ověřte výchozí umístění.
9. Zaškrtněte **Připnout na řídicí panel**.
10. Vyberte **Vytvořit**.

### <a name="create-the-gateway-subnet"></a>Vytvoření podsítě brány
1. Na řídicím panelu otevřete prostředek virtuální sítě VNET-01, který jste vytvořili dříve.
2. V okně **Nastavení** vyberte **Podsítě**.
3. Chcete-li přidat podsíť brány k virtuální síti, **podsíť brány**.
   
    ![Přidání podsítě brány](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. Ve výchozím nastavení, název podsítě nastavený na **GatewaySubnet**.
   Podsítě brány jsou speciální. Aby fungovala správně, musíte použít *GatewaySubnet* název.
5. V **rozsah adres**, ověřte, že je adresa **10.0.11.0/24**.
6. Vyberte **OK** vytvořit podsíť brány.

### <a name="create-the-virtual-network-gateway"></a>Vytvoření brány virtuální sítě
1. Na webu Azure Portal, vyberte **+ vytvořit prostředek**. 
2. Přejděte na **Marketplace**a pak vyberte **sítě**.
3. V seznamu síťových prostředků, vyberte **Brána virtuální sítě**.
4. V **název**, zadejte **GW1**.
5. Vyberte **virtuální síť** položku, kterou chcete zvolit virtuální síť.
   Vyberte **připojení typu VNET-01** ze seznamu.
6. Vyberte **veřejnou IP adresu** položky nabídky. Když **zvolte veřejnou IP adresu** otevře se okno, vyberte **vytvořit nový**.
7. V **název**, zadejte **GW1-PiP**a pak vyberte **OK**.
8.  Ve výchozím nastavení pro **typ sítě VPN**, **založené na trasách** zaškrtnuto.
    Zachovat **založené na trasách** typ sítě VPN.
9. Ověřte, že nastavení **Předplatné** a **Umístění** jsou správná. Prostředek na řídicí panel můžete připnout. Vyberte **Vytvořit**.

### <a name="create-the-local-network-gateway"></a>Vytvoření brány místní sítě
Implementace *brány místní sítě* v tomto nasazení Azure Stack pro účely vyhodnocení je trochu jiná než ve skutečném nasazení Azure.

V nasazení služby Azure představuje bránu místní sítě místní (u tenanta) fyzického zařízení, který používáte pro připojení k bráně virtuální sítě v Azure. V tomto nasazení Azure Stack hodnocení jsou obou koncích připojení brány virtuální sítě!

Způsob obecněji dívat se, že prostředek brány místní sítě vždycky uvádí vzdálenou bránu na druhém konci připojení. Kvůli způsobu, jakým Azure Stack Development Kit byla navržena budete muset zadat IP adresu externího síťového adaptéru na překlad síťových adres (NAT) virtuální počítač v jiné Azure Stack Development Kit, jako veřejnou IP adresu brány místní sítě. Pak vytvoříte mapování překladu adres na tomto virtuálním počítači abyste měli jistotu, že jsou oba konce správně připojené.


### <a name="create-the-local-network-gateway-resource"></a>Vytváření prostředku brány místní sítě
1. Přihlaste se k fyzickému počítači Azure Stack pro POC1.
2. Na portálu user portal, vyberte **+ vytvořit prostředek**.
3. Přejděte na **Marketplace**a pak vyberte **sítě**.
4. V seznamu prostředků vyberte **bránu místní sítě**.
5. V **název**, zadejte **POC2-GW**.
6. V **IP adresu**, zadejte adresu externího BGPNAT POC2. Tato adresa se zobrazí výše v tabulce Konfigurace sítě.
7. V **adresní prostor**, adresní prostor sítě vnet POC2, který později vytvoříte, zadejte **10.0.20.0/23**.
8. Ověřte, že vaše **předplatné**, **skupiny prostředků**, a **umístění** jsou správné a pak vyberte **vytvořit**.

### <a name="create-the-connection"></a>Vytvoření připojení
1. Na portálu user portal, vyberte **+ vytvořit prostředek**.
2. Přejděte na **Marketplace**a pak vyberte **sítě**.
3. V seznamu prostředků vyberte **připojení**.
4. Na **Základy** okno nastavení pro **typ připojení**vyberte **Site-to-site (IPSec)**.
5. Vyberte **předplatné**, **skupiny prostředků**, a **umístění**a pak vyberte **OK**.
6. Na **nastavení** okně vyberte **Brána virtuální sítě**a pak vyberte **GW1**.
7. Vyberte **bránu místní sítě**a pak vyberte **POC2-GW**.
8. V **název připojení**, zadejte **POC1-POC2**.
9. V **sdílený klíč (PSK)**, zadejte **12345**a pak vyberte **OK**.
10. Na **Souhrn** okně vyberte **OK**.

### <a name="create-a-vm"></a>Vytvoření virtuálního počítače
Ověřit data přenášená přes připojení VPN, budete potřebovat virtuální počítače pro odesílání a příjem dat v jednotlivých Azure Stack Development Kit. Teď vytvořte virtuální počítač v POC1 a potom ve vaší virtuální síti, vložit ho podsítě virtuálních počítačů.

1. Na webu Azure Portal, vyberte **+ vytvořit prostředek**.
2. Přejděte na **Marketplace**a pak vyberte **Compute**.
3. Vyberte v seznamu imagí virtuálních počítačů **systému Windows Server 2016 Datacenter Eval** bitové kopie.
4. Na **Základy** okno v **název**, zadejte **VM01**.
5. Zadejte platné uživatelské jméno a heslo. Tento účet použijete pro přihlášení k virtuálnímu počítači po jeho vytvoření.
6. Zadejte **předplatné**, **skupiny prostředků**, a **umístění**a pak vyberte **OK**.
7. Na **velikost** okno pro tuto instanci, vyberte velikost virtuálního počítače a pak vyberte **vyberte**.
8. Na **nastavení** okno, přijměte výchozí hodnoty. Ujistěte se, **připojení typu VNET-01** je vybraná virtuální síť. Ověřte, že podsíť je nastavená na **10.0.10.0/24**. Pak vyberte **OK**.
9. Na **Souhrn** okno, zkontrolujte nastavení a pak vyberte **OK**.



## <a name="create-the-network-resources-in-poc2"></a>Vytvoření síťových prostředků v POC2

Dalším krokem je vytvoření síťových prostředků pro POC2. Následující pokyny ukazují, jak vytvořit prostředky pomocí portálu user portal.

### <a name="sign-in-as-a-tenant"></a>Přihlaste se jako tenant
Správce služeb můžete přihlásit jako tenant k testovací plány, nabídky a předplatné, které můžou jeho tenanti používat. Pokud ho ještě nemáte, [vytvořte si účet tenanta](azure-stack-add-new-user-aad.md) před přihlášením.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Vytvoření virtuální sítě a podsítě virtuálních počítačů

1. Přihlaste se pomocí účtu tenanta.
2. Na portálu user portal, vyberte **+ vytvořit prostředek**.
3. Přejděte na **Marketplace**a pak vyberte **sítě**.
4. Vyberte **virtuální síť**.
5. Použijte informace uvedené výše v tabulce Konfigurace sítě a určete hodnoty pro POC2 **název**, **adresní prostor**, **název podsítě**a **Rozsah adres podsítě**.
6. V **předplatné**, zobrazí se předplatné, které jste vytvořili dříve.
7. Pro **skupiny prostředků**, vytvořte novou skupinu prostředků, nebo pokud ještě nemáte, vyberte **použít existující**.
8. Ověření výchozího **umístění**.
9. Zaškrtněte **Připnout na řídicí panel**.
10. Vyberte **Vytvořit**.

### <a name="create-the-gateway-subnet"></a>Vytvoření podsítě brány
1. Otevřete prostředek virtuální sítě, jste vytvořili (**připojení typu VNET-02**) z řídicího panelu.
2. V okně **Nastavení** vyberte **Podsítě**.
3. Vyberte **podsíť brány** přidat podsíť brány k virtuální síti.
4. Ve výchozím nastavení je název této podsítě nastavený na **GatewaySubnet**.
   Podsítě brány jsou speciální a musí mít tento konkrétní název, aby fungovaly správně.
5. V **rozsah adres** pole, zkontrolujte, adresa **10.0.21.0/24**.
6. Vyberte **OK** vytvořit podsíť brány.

### <a name="create-the-virtual-network-gateway"></a>Vytvoření brány virtuální sítě
1. Na webu Azure Portal, vyberte **+ vytvořit prostředek**.  
2. Přejděte na **Marketplace**a pak vyberte **sítě**.
3. V seznamu síťových prostředků, vyberte **Brána virtuální sítě**.
4. V **název**, zadejte **GW2**.
5. Chcete-li zvolit virtuální síť, vyberte **virtuální síť**. Potom vyberte **připojení typu VNET-02** ze seznamu.
6. Vyberte **veřejnou IP adresu**. Když **zvolte veřejnou IP adresu** otevře se okno, vyberte **vytvořit nový**.
7. V **název**, zadejte **GW2-PiP**a pak vyberte **OK**.
8. Ve výchozím nastavení pro **typ sítě VPN**, **založené na trasách** zaškrtnuto.
    Zachovat **založené na trasách** typ sítě VPN.
9. Ověřte, že nastavení **Předplatné** a **Umístění** jsou správná. Prostředek na řídicí panel můžete připnout. Vyberte **Vytvořit**.

### <a name="create-the-local-network-gateway-resource"></a>Vytváření prostředku brány místní sítě

1. V portálu user portal POC2 vyberte **+ vytvořit prostředek**. 
4. Přejděte na **Marketplace**a pak vyberte **sítě**.
5. V seznamu prostředků vyberte **bránu místní sítě**.
6. V **název**, zadejte **POC1-GW**.
7. V **IP adresu**, zadejte adresu externího BGPNAT POC1, která je uvedena výše v tabulce Konfigurace sítě.
8. V **adresní prostor**, z POC1, zadejte **10.0.10.0/23** adresní prostor **připojení typu VNET-01**.
9. Ověřte, že vaše **předplatné**, **skupiny prostředků**, a **umístění** jsou správné a pak vyberte **vytvořit**.

## <a name="create-the-connection"></a>Vytvoření připojení
1. Na portálu user portal, vyberte **+ vytvořit prostředek**. 
2. Přejděte na **Marketplace**a pak vyberte **sítě**.
3. V seznamu prostředků vyberte **připojení**.
4. Na **základní** okno nastavení pro **typ připojení**, zvolte **Site-to-site (IPSec)**.
5. Vyberte **předplatné**, **skupiny prostředků**, a **umístění**a pak vyberte **OK**.
6. Na **nastavení** okně vyberte **Brána virtuální sítě**a pak vyberte **GW2**.
7. Vyberte **bránu místní sítě**a pak vyberte **POC1-GW**.
8. V **název připojení**, zadejte **POC2-POC1**.
9. V **sdílený klíč (PSK)**, zadejte **12345**. Pokud zvolíte jinou hodnotu, mějte na paměti, že *musí* odpovídat hodnotě pro sdílený klíč, který jste vytvořili v POC1. Vyberte **OK**.
10. Zkontrolujte **Souhrn** okna a pak vyberte **OK**.

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače
Teď vytvořte virtuální počítač v POC2 a umístí jej podsítě virtuálních počítačů ve vaší virtuální síti.

1. Na webu Azure Portal, vyberte **+ vytvořit prostředek**.
2. Přejděte na **Marketplace**a pak vyberte **Compute**.
3. Vyberte v seznamu imagí virtuálních počítačů **systému Windows Server 2016 Datacenter Eval** bitové kopie.
4. Na **Základy** okně pro **název**, zadejte **VM02**.
5. Zadejte platné uživatelské jméno a heslo. Tento účet použijete pro přihlášení k virtuálnímu počítači po jeho vytvoření.
6. Zadejte **předplatné**, **skupiny prostředků**, a **umístění**a pak vyberte **OK**.
7. Na **velikost** okno, vyberte velikost pro tuto instanci virtuálního počítače a pak vyberte **vyberte**.
8. Na **nastavení** okně můžete přijmout výchozí hodnoty. Ujistěte se, **připojení typu VNET-02** virtuální síť je vybraná a ověřte, že podsíť je nastavená na **10.0.20.0/24**. Vyberte **OK**.
9. Zkontrolujte nastavení na **Souhrn** okna a pak vyberte **OK**.

## <a name="configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal"></a>Konfigurace virtuálního počítače překladu adres na každé Azure Stack Development Kit pro přecházení přes bránu
Protože Azure Stack Development Kit je samostatné a izolované od sítě, na kterém je nasazený fyzický hostitel, *externí* není ve skutečnosti externí síť virtuálních IP adres, které jsou brány připojené k. Síť virtuálních IP adres je skrytá za směrovačem, který provádí překlad síťových adres. 

Tímto směrovačem je virtuální počítač s Windows serverem volá *AzS-bgpnat01*, role Služba Směrování a vzdálený přístup (RRAS), který běží v infrastruktuře Azure Stack Development Kit. Na virtuálním počítači povolit připojení VPN site-to-site pro připojení na obou koncích AzS-bgpnat01 musíte nakonfigurovat překladu adres. 

Pokud chcete nakonfigurovat připojení VPN, musíte vytvořit statickou trasu mapování NAT, která se mapuje externí rozhraní virtuálního počítače BGPNAT virtuální IP adresu fondu hraniční brány. Statickou trasu NAT mapy se vyžaduje pro každý z portů v připojení k síti VPN.

> [!NOTE]
> Tato konfigurace je nutná pouze v prostředích Azure Stack Development Kit.
> 
> 

### <a name="configure-the-nat"></a>Konfigurace zařízení NAT.
> [!IMPORTANT]
> Musíte dokončit tento postup pro *obě* prostředích Azure Stack Development Kit.

1. Určit, **interní IP adresa** pro použití v následující skript prostředí PowerShell. Otevřete bránu virtuální sítě (GW1 a GW2) a pak na **přehled** okno Uložit hodnotu **veřejnou IP adresu** pro pozdější použití.
![Interní IP adresa](media/azure-stack-create-vpn-connection-one-node-tp2/InternalIP.PNG)
2. Přihlaste se k fyzickému počítači Azure Stack pro POC1.
3. Zkopírujte a upravte následující skript prostředí PowerShell. Ke konfiguraci překladu síťových adres na každé sady Azure Stack Development Kit, spusťte skript se zvýšenými oprávněními prostředí PowerShell ISE Windows. Ve skriptu, přidejte hodnoty pro *adresu externího BGPNAT* a *interní IP adresa* zástupné symboly:

   ```powershell
   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 4499 `
      -PortEnd 4501}
   # create a static NAT mapping to map the external address to the Gateway
   # Public IP Address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 500 `
      -InternalPort 500}
   # Finally, configure NAT traversal which uses port 4500 to
   # successfully establish the complete IPSEC tunnel over NAT devices
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

4. Opakujte tento postup v POC2.

## <a name="test-the-connection"></a>Otestování připojení
Teď, když se připojení site-to-site, měli byste ověřit, že dokážete zajistit provoz přes něj. Pokud chcete ověřit, přihlaste se k jednomu z virtuálních počítačů, které jste vytvořili v prostředí Azure Stack Development Kit. Pak odešlete zprávu ping virtuální počítač, který jste vytvořili v druhém prostředí. 

Pokud chcete mít jistotu, že je přenos odesílat přes připojení site-to-site, zkontrolujte pomocí příkazu ping adresa Direct IP (DIP) virtuálního počítače ve vzdálené podsíti, nikoli virtuální IP adresy. K tomuto účelu najdete IP adresu na druhém konci připojení. Uložte adresu pro pozdější použití.

### <a name="sign-in-to-the-tenant-vm-in-poc1"></a>Přihlaste se k virtuálnímu počítači v POC1 tenanta
1. Přihlaste se k fyzickému počítači Azure Stack pro POC1 a potom přihlásit k portálu user portal pomocí účtu tenanta.
2. V levém navigačním panelu vyberte **Compute**.
3. V seznamu virtuálních počítačů, najděte **VM01** , který jste vytvořili dříve a pak ho vyberte.
4. V okně pro virtuální počítač, klikněte na **připojit**a pak otevřete soubor VM01.rdp.
   
     ![Tlačítko pro připojení](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)
5. Přihlaste se pomocí účtu, který jste nakonfigurovali při vytváření virtuálního počítače.
6. Otevřete zvýšenými **prostředí Windows PowerShell** okna.
7. Zadejte **ipconfig/all**.
8. Ve výstupu vyhledejte **IPv4 adresu**a potom uložte adresu pro pozdější použití. Jde o adresu, kterou použijete pro příkaz ping z POC2. V našem ukázkovém prostředí je touto adresou **10.0.10.4**, ale ve vašem prostředí tato hodnota může být jiná. By měla spadat do **10.0.10.0/24** podsítě, kterou jste vytvořili dříve.
9. Pokud chcete vytvořit pravidlo brány firewall, které umožňuje virtuálnímu počítači reagovat na příkazy ping pro zjištění, spusťte následující příkaz Powershellu:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-poc2"></a>Přihlaste se k virtuálnímu počítači v POC2 tenanta
1. Přihlaste se k fyzickému počítači Azure Stack pro POC2 a potom přihlásit k portálu user portal pomocí účtu tenanta.
2. V levém navigačním panelu klikněte na tlačítko **Compute**.
3. Ze seznamu virtuálních počítačů, Najít **VM02** , který jste vytvořili dříve a pak ho vyberte.
4. V okně pro virtuální počítač klikněte na **Připojit**.
5. Přihlaste se pomocí účtu, který jste nakonfigurovali při vytváření virtuálního počítače.
6. Otevřete zvýšenými **prostředí Windows PowerShell** okna.
7. Zadejte **ipconfig/all**.
8. Měla by se zobrazit IPv4 adresu, která spadá do **10.0.20.0/24**. V ukázkovém prostředí je adresa **10.0.20.4**, ale vaše adresa může být jiný.
9. Pokud chcete vytvořit pravidlo brány firewall, které umožňuje virtuálnímu počítači reagovat na příkazy ping pro zjištění, spusťte následující příkaz Powershellu:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

10. Z virtuálního počítače v POC2 odešlete zprávu ping virtuálnímu počítači v POC1, prostřednictvím tunelu. K tomu příkaz ping použijte DIP, který jste si poznamenali z VM01.
   V ukázkovém prostředí je to **10.0.10.4**, ale je potřeba příkaz ping na adresu, kterou jste si poznamenali ve vaší laboratoři. Zobrazí se výsledek, který vypadá takto:
   
    ![Úspěšný příkaz ping](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
11. Odpověď od vzdáleného virtuálního počítače označuje test proběhl úspěšně! V okně virtuálního počítače můžete zavřít. Pokud chcete otestovat připojení, můžete zkusit jinými druhy přenosů dat, třeba kopírování souborů.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Zobrazení statistiky přenosu dat prostřednictvím připojení brány
Pokud chcete vědět, kolik dat prochází připojení site-to-site, tyto informace jsou k dispozici na **připojení** okno. Tento test je také dalším způsobem, jak ověřit, že příkaz ping, který jste právě odeslali, skutečně prošel přes připojení VPN.

1. Když jste přihlášení k virtuálnímu počítači tenanta v POC2, přihlaste se k portálu user portal pomocí účtu tenanta.
2. Přejděte na **všechny prostředky**a pak vyberte **POC2-POC1** připojení. **Připojení** se zobrazí.
4. Na **připojení** okno, Statistika **Data v** a **výstupní Data** zobrazí. Na následujícím snímku obrazovky jsou velké počty přiřadit přenos dalších souborů. Měli byste vidět některé nenulové hodnoty.
   
    ![Vstupní a výstupní data](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)
