---
title: Vytvoření připojení site-to-site VPN mezi dvěma virtuálními sítěmi v různých prostředích Azure zásobníku Development Kit | Microsoft Docs
description: Podrobný postup, který správce cloudu používá k vytvoření připojení site-to-site VPN mezi dvěma prostředími Azure zásobníku Development Kit jeden uzel.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 3f1b4e02-dbab-46a3-8e11-a777722120ec
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/10/2017
ms.author: brenduns
ms.reviewer: scottnap
ms.openlocfilehash: 8bb10be63ef31ebe6241c15a79dafe71923a4cf7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-azure-stack-development-kit-environments"></a>Vytvoření připojení site-to-site VPN mezi dvěma virtuálními sítěmi v různých prostředích Azure zásobníku Development Kit
## <a name="overview"></a>Přehled
Tento článek ukazuje, jak vytvořit připojení site-to-site VPN mezi dvěma virtuálními sítěmi ve dvou samostatných prostředí Azure zásobníku Development Kit. Když konfigurujete připojení, zjistíte, jak fungují v zásobníku Azure VPN Gateway.

### <a name="connection-diagram"></a>Diagram připojení
Následující diagram znázorňuje, by měla vypadat konfigurace připojení po dokončení.

![Konfigurace připojení Site-to-site VPN](media/azure-stack-create-vpn-connection-one-node-tp2/OneNodeS2SVPN.png)

### <a name="before-you-begin"></a>Než začnete
K dokončení konfigurace připojení, ověřte, zda máte následující položky, než začnete:

* Dva servery a další nezbytné součásti, které splňují požadavky na hardware Azure zásobníku Development Kit, jak je popsáno v [rychlý start: vyhodnocení Development Kit zásobník Azure](azure-stack-deploy-overview.md). 
* [Azure zásobníku Development Kit](https://azure.microsoft.com/en-us/overview/azure-stack/try/) balíček pro nasazení.

## <a name="deploy-the-azure-stack-development-kit-environments"></a>Nasazení prostředí Azure zásobníku Development Kit
K dokončení konfigurace připojení, je nutné nasadit dvě prostředí Azure zásobníku Development Kit.
> [!NOTE] 
> Pro každý Azure zásobníku Development Kit který nasazujete, postupujte [pokyny k nasazení](azure-stack-run-powershell-script.md). V tomto článku, se nazývají prostředí Azure zásobníku Development Kit *POC1* a *POC2*.


## <a name="prepare-an-offer-on-poc1-and-poc2"></a>Příprava nabídku na POC1 a POC2
Na POC1 i POC2 připravte nabídku tak, aby uživatele můžete se přihlásit nabídku a nasazení virtuálních počítačů. Informace o tom, jak vytvořit nabídku najdete v tématu [virtuální počítače zpřístupnit uživatelům Azure zásobníku](azure-stack-tutorial-tenant-vm.md).

## <a name="review-and-complete-the-network-configuration-table"></a>Zkontrolujte a dokončete tabulky konfigurace sítě
Následující tabulka shrnuje konfiguraci sítě pro obě prostředí Azure zásobníku Development Kit. Použijte postup, který se zobrazí po tabulku, kterou chcete přidat externí BGPNAT adresu, která je specifická pro vaši síť.

**Tabulka konfigurace sítě**
|   |POC1|POC2|
|---------|---------|---------|
|Název virtuální sítě     |VNET-01|VNET-02 |
|Adresní prostor virtuální sítě |10.0.10.0/23|10.0.20.0/23|
|Název podsítě     |Subnet-01|Subnet-02|
|Rozsah adres podsítě|10.0.10.0/24 |10.0.20.0/24 |
|Podsíť brány     |10.0.11.0/24|10.0.21.0/24|
|Externí adresu BGPNAT     |         |         |

> [!NOTE]
> Externí BGPNAT IP adresy v příkladu prostředí jsou 10.16.167.195 pro POC1 a 10.16.169.131 pro POC2. Pomocí následujícího postupu k určení externí BGPNAT IP adresy pro Azure zásobníku Development Kit hostitele a pak je přidejte do předchozí tabulce Konfigurace sítě.


### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>Získání IP adresy externího adaptéru virtuálního počítače pro překlad adres
1. Přihlaste se k Azure zásobníku fyzický počítač pro POC1.
2. Upravte následující kód Powershell nahradit heslo správce a pak spustit kód na hostiteli POC:

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
3. Přidejte IP adresu do tabulky konfigurace sítě, která se zobrazí v předchozí části.

4. Opakujte tento postup u POC2.

## <a name="create-the-network-resources-in-poc1"></a>Vytvoření síťové prostředky v POC1
Teď vytvoříte POC1 síťové prostředky, které je nutné nastavit až vašich bran. Následující pokyny popisují, jak pro vytvoření prostředků pomocí portálu pro uživatele. Můžete taky kód prostředí PowerShell pro vytvoření prostředků.

![Pracovní postup, který se používá k vytvoření prostředky](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="sign-in-as-a-tenant"></a>Přihlaste se jako klient
Správce služeb můžete přihlásit jako klient a testovací plány, nabídky a odběry, které můžou používat svým klientům. Pokud jste ještě nemáte, [vytvořit účet klienta](azure-stack-add-new-user-aad.md) před přihlášením.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Vytvoření virtuální sítě a podsítě virtuálních počítačů
1. Použijte účet klienta se přihlásit k portálu user portal.
2. V portálu pro uživatele, vyberte **nový**.

    ![Vytvořit novou virtuální síť](media/azure-stack-create-vpn-connection-one-node-tp2/image3.png)

3. Přejděte na **Marketplace**a potom vyberte **sítě**.
4. Vyberte **virtuální síť**.
5. Pro **název**, **adresní prostor**, **název podsítě**, a **rozsah adres podsítě**, použít hodnoty, které se zobrazují v síti Konfigurace tabulky.
6. V **předplatné**, zobrazí se předplatné, které jste vytvořili dříve.
7. Pro **skupiny prostředků**, můžete vytvořit skupinu prostředků nebo pokud již účet máte, vyberte **použít existující**.
8. Ověřte výchozí umístění.
9. Zaškrtněte **Připnout na řídicí panel**.
10. Vyberte **Vytvořit**.

### <a name="create-the-gateway-subnet"></a>Vytvoření podsítě brány
1. Na řídicím panelu otevřete prostředek virtuální sítě VNET-01, který jste vytvořili dříve.
2. V okně **Nastavení** vyberte **Podsítě**.
3. Chcete-li přidat podsíť brány virtuální sítě, vyberte **podsíť brány**.
   
    ![Přidání podsítě brány](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. Ve výchozím nastavení, je název podsítě hodnotu **GatewaySubnet**.
   Podsítě brány jsou speciální. Chcete-li správně fungoval, musíte použít *GatewaySubnet* název.
5. V **rozsahu adres**, ověřte, že adresa je **10.0.11.0/24**.
6. Vyberte **OK** vytvořit podsíť brány.

### <a name="create-the-virtual-network-gateway"></a>Vytvoření brány virtuální sítě
1. Na portálu Azure vyberte **nový**. 
2. Přejděte na **Marketplace**a potom vyberte **sítě**.
3. V seznamu síťových prostředků, vyberte **Brána virtuální sítě**.
4. V **název**, zadejte **GW1**.
5. Vyberte **virtuální síť** položky vyberte virtuální síť.
   Vyberte **VNET-01** ze seznamu.
6. Vyberte **veřejnou IP adresu** položku nabídky. Když **zvolte veřejnou IP adresu** otevře se okno, vyberte **vytvořit nový**.
7. V **název**, zadejte **GW1 PiP**a potom vyberte **OK**.
8.  Ve výchozím nastavení pro **typ sítě VPN**, **založené na trasách** je vybrána.
    Zachovat **založené na trasách** typ sítě VPN.
9. Ověřte, že nastavení **Předplatné** a **Umístění** jsou správná. Budete moct připnout prostředků na řídicí panel. Vyberte **Vytvořit**.

### <a name="create-the-local-network-gateway"></a>Vytvoření brány místní sítě
Implementace *brány místní sítě* v tomto nasazení Azure Stack pro účely vyhodnocení je trochu jiná než ve skutečném nasazení Azure.

V nasazení služby Azure představuje bránu místní sítě fyzického zařízení místní (na klienta), který používáte pro připojení k bráně virtuální sítě v Azure. V tomto nasazení vyhodnocení zásobník Azure jsou obou konců připojení brány virtuální sítě!

Způsob, jak více obecně myslíte o této je, že prostředek brány místní sítě vždycky uvádí služby Brána vzdálené na druhém konci připojení. Kvůli způsobu, kterým byl navržen Development Kit zásobník Azure budete muset zadat IP adresu externího síťového adaptéru na překlad síťových adres (NAT) virtuální počítač jiné Azure zásobníku Development Kit, jako veřejná IP adresa brány místní sítě. Pak vytvoříte mapování NAT pro virtuální počítač NAT a ujistěte se, že jsou oba elementy end správně připojeny.


### <a name="create-the-local-network-gateway-resource"></a>Vytvořte prostředek brány místní sítě
1. Přihlaste se k Azure zásobníku fyzický počítač pro POC1.
2. V portálu pro uživatele, vyberte **nový**.
3. Přejděte na **Marketplace**a potom vyberte **sítě**.
4. V seznamu zdrojů vyberte **brány místní sítě**.
5. V **název**, zadejte **POC2-GW**.
6. V **IP adresu**, zadejte adresu externího BGPNAT POC2. Tato adresa se zobrazí dříve v tabulce Konfigurace sítě.
7. V **adresní prostor**, adresní prostor sítě vnet POC2, který později vytvoříte, zadejte **10.0.20.0/23**.
8. Ověřte, že vaše **předplatné**, **skupiny prostředků**, a **umístění** jsou správné a potom vyberte **vytvořit**.

### <a name="create-the-connection"></a>Vytvoření připojení
1. V portálu pro uživatele, vyberte **nový**.
2. Přejděte na **Marketplace**a potom vyberte **sítě**.
3. V seznamu zdrojů vyberte **připojení**.
4. Na **Základy** okno nastavení pro **typ připojení**, vyberte **Site-to-site (IPSec)**.
5. Vyberte **předplatné**, **skupiny prostředků**, a **umístění**a potom vyberte **OK**.
6. Na **nastavení** vyberte **Brána virtuální sítě**a potom vyberte **GW1**.
7. Vyberte **brány místní sítě**a potom vyberte **POC2-GW**.
8. V **název připojení**, zadejte **POC1 POC2**.
9. V **sdílený klíč (PSK)**, zadejte **12345**a potom vyberte **OK**.
10. Na **Souhrn** vyberte **OK**.

### <a name="create-a-vm"></a>Vytvoření virtuálního počítače
Pro ověření dat, který se přenáší prostřednictvím připojení VPN, musíte virtuální počítače k odesílání a příjmu dat v každém Azure zásobníku Development Kit. Vytvoření virtuálního počítače v POC1 nyní a pak ve virtuální síti, přesuňte ho na podsíť virtuálních počítačů.

1. Na portálu Azure vyberte **nový**.
2. Přejděte na **Marketplace**a potom vyberte **výpočetní**.
3. Vyberte v seznamu bitové kopie virtuálních počítačů, **Windows Server 2016 Datacenter Eval** bitové kopie.
4. Na **Základy** okno v **název**, zadejte **VM01**.
5. Zadejte platné uživatelské jméno a heslo. Tento účet používáte k přihlášení k virtuálnímu počítači po jeho vytvoření.
6. Zadejte **předplatné**, **skupiny prostředků**, a **umístění**a potom vyberte **OK**.
7. Na **velikost** okno pro tuto instanci vybrat velikost virtuálního počítače a pak vyberte **vyberte**.
8. Na **nastavení** okno, přijměte výchozí hodnoty. Ujistěte se, že **VNET-01** virtuální síť je vybrána. Ověřte, že podsíť je nastavena na **10.0.10.0/24**. Pak vyberte **OK**.
9. Na **Souhrn** okno, zkontrolujte nastavení a potom vyberte **OK**.



## <a name="create-the-network-resources-in-poc2"></a>Vytvoření síťové prostředky v POC2

Dalším krokem je vytvoření síťovým prostředkům pro POC2. Následující pokyny ukazují, jak vytvořit prostředky pomocí portálu pro uživatele.

### <a name="sign-in-as-a-tenant"></a>Přihlaste se jako klient
Správce služeb můžete přihlásit jako klient a testovací plány, nabídky a odběry, které můžou používat svým klientům. Pokud jste ještě nemáte, [vytvořit účet klienta](azure-stack-add-new-user-aad.md) před přihlášením.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Vytvoření virtuální sítě a podsítě virtuálních počítačů

1. Přihlaste se pomocí účtu klienta.
2. V portálu pro uživatele, vyberte **nový**.
3. Přejděte na **Marketplace**a potom vyberte **sítě**.
4. Vyberte **virtuální síť**.
5. Pomocí informací uvedených výše v tabulce Konfigurace sítě Identifikujte hodnoty pro POC2 **název**, **adresní prostor**, **název podsítě**a **Rozsah adres podsítě**.
6. V **předplatné**, zobrazí se předplatné, které jste vytvořili dříve.
7. Pro **skupiny prostředků**, vytvořte novou skupinu prostředků nebo, pokud již účet máte, vyberte **použít existující**.
8. Ověření výchozího **umístění**.
9. Zaškrtněte **Připnout na řídicí panel**.
10. Vyberte **Vytvořit**.

### <a name="create-the-gateway-subnet"></a>Vytvoření podsítě brány
1. Otevřete prostředek virtuální sítě, jste vytvořili (**VNET-02**) na řídicím panelu.
2. V okně **Nastavení** vyberte **Podsítě**.
3. Vyberte **podsíť brány** přidat podsíť brány virtuální sítě.
4. Ve výchozím nastavení je název této podsítě nastavený na **GatewaySubnet**.
   Podsítě brány jsou speciální a musí mít tento konkrétní název, aby fungovaly správně.
5. V **rozsahu adres** pole, ověřte adresu **10.0.21.0/24**.
6. Vyberte **OK** vytvořit podsíť brány.

### <a name="create-the-virtual-network-gateway"></a>Vytvoření brány virtuální sítě
1. Na portálu Azure vyberte **nový**.  
2. Přejděte na **Marketplace**a potom vyberte **sítě**.
3. V seznamu síťových prostředků, vyberte **Brána virtuální sítě**.
4. V **název**, zadejte **GW2**.
5. Vyberte virtuální síť, vyberte **virtuální síť**. Potom vyberte **VNET-02** ze seznamu.
6. Vyberte **veřejnou IP adresu**. Když **zvolte veřejnou IP adresu** otevře se okno, vyberte **vytvořit nový**.
7. V **název**, zadejte **GW2 PiP**a potom vyberte **OK**.
8. Ve výchozím nastavení pro **typ sítě VPN**, **založené na trasách** je vybrána.
    Zachovat **založené na trasách** typ sítě VPN.
9. Ověřte, že nastavení **Předplatné** a **Umístění** jsou správná. Budete moct připnout prostředků na řídicí panel. Vyberte **Vytvořit**.

### <a name="create-the-local-network-gateway-resource"></a>Vytvořte prostředek brány místní sítě

1. V portálu user portal POC2 vyberte **nový**. 
4. Přejděte na **Marketplace**a potom vyberte **sítě**.
5. V seznamu zdrojů vyberte **brány místní sítě**.
6. V **název**, zadejte **POC1-GW**.
7. V **IP adresu**, zadejte adresu externího BGPNAT POC1, která je uvedena výše v tabulce Konfigurace sítě.
8. V **adresní prostor**, z POC1, zadejte **10.0.10.0/23** adresní prostor z **VNET-01**.
9. Ověřte, že vaše **předplatné**, **skupiny prostředků**, a **umístění** jsou správné a potom vyberte **vytvořit**.

## <a name="create-the-connection"></a>Vytvoření připojení
1. V portálu pro uživatele, vyberte **nový**. 
2. Přejděte na **Marketplace**a potom vyberte **sítě**.
3. V seznamu zdrojů vyberte **připojení**.
4. Na **základní** okno nastavení pro **typ připojení**, zvolte **Site-to-site (IPSec)**.
5. Vyberte **předplatné**, **skupiny prostředků**, a **umístění**a potom vyberte **OK**.
6. Na **nastavení** vyberte **Brána virtuální sítě**a potom vyberte **GW2**.
7. Vyberte **brány místní sítě**a potom vyberte **POC1-GW**.
8. V **název připojení**, zadejte **POC2 POC1**.
9. V **sdílený klíč (PSK)**, zadejte **12345**. Pokud zvolíte jinou hodnotu, mějte na paměti, že IT oddělení *musí* odpovídají hodnotě pro sdílený klíč, který jste vytvořili na POC1. Vyberte **OK**.
10. Zkontrolujte **Souhrn** a pak vyberte **OK**.

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače
Vytvoření virtuálního počítače v POC2 nyní a umístí jej podsíť virtuálních počítačů ve virtuální síti.

1. Na portálu Azure vyberte **nový**.
2. Přejděte na **Marketplace**a potom vyberte **výpočetní**.
3. Vyberte v seznamu bitové kopie virtuálních počítačů, **Windows Server 2016 Datacenter Eval** bitové kopie.
4. Na **Základy** okně pro **název**, zadejte **VM02**.
5. Zadejte platné uživatelské jméno a heslo. Tento účet používáte k přihlášení k virtuálnímu počítači po jeho vytvoření.
6. Zadejte **předplatné**, **skupiny prostředků**, a **umístění**a potom vyberte **OK**.
7. Na **velikost** okně, vyberte virtuální počítač velikost pro tuto instanci a potom vyberte **vyberte**.
8. Na **nastavení** okně můžete přijmout výchozí hodnoty. Ujistěte se, že **VNET-02** virtuální síť je vybrána a ověřte, že podsíť je nastavena na **10.0.20.0/24**. Vyberte **OK**.
9. Zkontrolujte nastavení na **Souhrn** a pak vyberte **OK**.

## <a name="configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal"></a>Nakonfigurujte virtuální počítač NAT na každý Development Kit pro přecházení přes brány Azure zásobníku
Protože Azure zásobníku Development Kit je úplný a samostatný a izolované od sítě, na kterém je nasazený fyzického hostitele, *externí* VIP síť, která brány jsou připojené k není ve skutečnosti externí. Místo toho sítě VIP skrytá za směrovač, který provádí překlad síťových adres. 

Směrovači je virtuální počítač Windows serveru, volá se *AzS bgpnat01*, který spouští role směrování a služby vzdálený přístup (RRAS) v infrastruktuře Azure zásobníku Development Kit. NAT musíte nakonfigurovat na virtuálním počítači AzS bgpnat01 umožňující připojení site-to-site VPN na obou koncích připojení. 

Pokud chcete konfigurovat připojení k síti VPN, musíte vytvořit statickou trasu mapy NAT, která se mapuje na externí rozhraní na virtuálním počítači BGPNAT na virtuální IP adresu z fondu hraniční brány. Statické trasy mapy NAT je vyžadován pro každý port v připojení k síti VPN.

> [!NOTE]
> Tato konfigurace je nutná pro jenom prostředí Azure zásobníku Development Kit.
> 
> 

### <a name="configure-the-nat"></a>Nakonfigurujte zařízení NAT.
> [!IMPORTANT]
> Musíte provést tento postup pro *i* prostředí Azure zásobníku Development Kit.

1. Určení **interní IP adresu** pro použití v následující skript prostředí PowerShell. Otevřete bránu virtuální sítě (GW1 a GW2) a pak na **přehled** okno Uložit hodnota **veřejnou IP adresu** pro pozdější použití.
![Interní IP adresu](media/azure-stack-create-vpn-connection-one-node-tp2/InternalIP.PNG)
2. Přihlaste se k Azure zásobníku fyzický počítač pro POC1.
3. Zkopírujte a upravte následující skript prostředí PowerShell. Na každém Azure zásobníku Development Kit nakonfigurovat NAT, spusťte skript v zvýšenými ISE Windows PowerShell. Ve skriptu, přidejte hodnoty do *adresu externího BGPNAT* a *interní IP adresu* zástupné symboly:

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

4. Opakujte tento postup u POC2.

## <a name="test-the-connection"></a>Otestování připojení
Teď, když připojení site-to-site je třeba ověřit, získáte provoz přes něj. Chcete-li ověřit, přihlaste se do jednoho z virtuálních počítačů, které jste vytvořili v prostředí Azure zásobníku Development Kit. Pak odeslat příkaz ping virtuální počítač, který jste vytvořili v druhém prostředí. 

Aby odesílat provoz prostřednictvím připojení site-to-site, ujistěte se, příkazem ping otestovat adresu přímé IP (DIP) virtuálního počítače ve vzdálené podsíti, není VIP. K tomuto účelu najít adresu vyhrazené IP adresy na druhém konci připojení. Uložte adresu pro pozdější použití.

### <a name="sign-in-to-the-tenant-vm-in-poc1"></a>Přihlaste se k klienta virtuálního počítače v POC1
1. Přihlaste se k Azure zásobníku fyzický počítač pro POC1 a pak použít účet klienta se přihlásit k portálu user portal.
2. V levém navigačním panelu, vyberte **výpočetní**.
3. V seznamu virtuálních počítačů, Najít **VM01** který jste vytvořili dříve a pak ho vyberte.
4. V okně pro virtuální počítač, klikněte na **Connect**a pak otevřete soubor VM01.rdp.
   
     ![Tlačítko pro připojení](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)
5. Přihlaste se pomocí účtu, který se konfiguruje při vytváření virtuálního počítače.
6. Otevřít se zvýšenými oprávněními **prostředí Windows PowerShell** okno.
7. Zadejte **ipconfig/all**.
8. Ve výstupu najít **IPv4 adresu**a potom uložte adresu pro pozdější použití. Toto je adresa, která bude příkaz ping ze POC2. V našem ukázkovém prostředí je touto adresou **10.0.10.4**, ale ve vašem prostředí tato hodnota může být jiná. Musí spadat do **10.0.10.0/24** podsíť, kterou jste vytvořili dříve.
9. Pokud chcete vytvořit pravidlo brány firewall, která umožňuje virtuálního počítače, které neodpovídají na ověřování aktivity, spusťte následující příkaz prostředí PowerShell:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-poc2"></a>Přihlaste se k klienta virtuálního počítače v POC2
1. Přihlaste se k Azure zásobníku fyzický počítač pro POC2 a pak použít účet klienta se přihlásit k portálu user portal.
2. V levém navigačním panelu klikněte na **výpočetní**.
3. Ze seznamu virtuálních počítačů, Najít **VM02** který jste vytvořili dříve a pak ho vyberte.
4. V okně pro virtuální počítač klikněte na **Připojit**.
5. Přihlaste se pomocí účtu, který se konfiguruje při vytváření virtuálního počítače.
6. Otevřít se zvýšenými oprávněními **prostředí Windows PowerShell** okno.
7. Zadejte **ipconfig/all**.
8. Měli byste vidět IPv4 adresu, která spadá do **10.0.20.0/24**. V příkladu prostředí, je na adresu **10.0.20.4**, ale vaše adresa může být odlišné.
9. Pokud chcete vytvořit pravidlo brány firewall, která umožňuje virtuálního počítače, které neodpovídají na ověřování aktivity, spusťte následující příkaz prostředí PowerShell:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

10. Z virtuálního počítače na POC2 zadejte příkaz ping na virtuální počítač na POC1, prostřednictvím tunelu. K tomuto účelu odeslat příkaz ping vyhrazené IP adresy, které jste si poznamenali ze VM01.
   V příkladu prostředí, je to **10.0.10.4**, ale ujistěte se, na příkaz ping na adresu, které jste si poznamenali ve svém testovacím prostředí. Měli byste vidět výsledek, který vypadá takto:
   
    ![Úspěšný příkaz ping](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
11. Odpověď od vzdáleného virtuálního počítače označuje testu úspěšné! Můžete zavřít okno virtuálního počítače. Chcete-li otestovat připojení, můžete se pokusit jinými druhy přenosů dat, jako je kopírování souborů.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Zobrazení statistiky přenosu dat prostřednictvím připojení brány
Pokud chcete vědět, kolik dat prochází připojení site-to-site, tyto informace jsou k dispozici na **připojení** okno. Tento test je také jiný způsob, jak ověřit, že příkazem ping, které jste poslali ve skutečnosti se prostřednictvím připojení VPN.

1. Když jste přihlášení k virtuálnímu počítači klienta v POC2, pomocí účtu klienta se přihlásit k portálu user portal.
2. Přejděte na **všechny prostředky**a pak vyberte **POC2 POC1** připojení. **Připojení** se zobrazí.
4. Na **připojení** okno, statistiku **Data v** a **Data** zobrazí. Na následujícím snímku obrazovky jsou velké počty přičítat přenos dalších souborů. Měli byste vidět některé nenulové hodnoty.
   
    ![Vstupní a výstupní data](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)
