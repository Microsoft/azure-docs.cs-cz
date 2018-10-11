---
title: Připojení k Azure pomocí VPN Azure Stack
description: Jak propojit virtuální sítě ve službě Azure Stack k virtuálním sítím v Azure pomocí připojení VPN.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/12/2018
ms.author: sethm
ms.reviewer: scottnap
ms.openlocfilehash: dcbe222d8dd3d3c658e5778fdc4bc1cc01b5c12d
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078881"
---
# <a name="connect-azure-stack-to-azure-using-vpn"></a>Připojení k Azure pomocí VPN Azure Stack

*Platí pro: integrované systémy Azure Stack*

V tomto článku se dozvíte, jak vytvořit VPN site-to-site pro připojení virtuální sítě ve službě Azure Stack na virtuální síť v Azure.

## <a name="before-you-begin"></a>Než začnete

K dokončení konfigurace připojení, ujistěte se, že máte následující položky, než začnete:

* Azure Stack integrované systémy (několikauzlovými) nasazení, který je přímo připojený k Internetu. Vaše externí veřejné rozsah IP adres musí být dosažitelná přímo z veřejného Internetu.
* Platné předplatné Azure. Pokud nemáte předplatné Azure, můžete vytvořit [bezplatný účet Azure zde](https://azure.microsoft.com/free/?b=17.06).

### <a name="vpn-connection-diagram"></a>Diagram připojení VPN

Následující diagram znázorňuje konfiguraci připojení by měl vypadat až to budete mít:

![Konfigurace připojení Site-to-site VPN](media/azure-stack-connect-vpn/image2.png)

### <a name="network-configuration-example-values"></a>Ukázkové hodnoty konfigurace sítě

V tabulce síťové konfigurace příklady jsou uvedeny hodnoty, které se používají příklady v tomto článku. Tyto hodnoty můžete použít nebo můžete použít k lepšímu pochopení příkladů v tomto článku.

**Příklady konfigurace sítě**

|   |Azure Stack|Azure|
|---------|---------|---------|
|Název virtuální sítě     |AZS-VNet|AzureVNet |
|Adresní prostor virtuální sítě |10.1.0.0/16|10.100.0.0/16|
|Název podsítě     |FrontEnd|FrontEnd|
|Rozsah adres podsítě|10.1.0.0/24 |10.100.0.0/24 |
|Podsíť brány     |10.1.1.0/24|10.100.1.0/24|

## <a name="create-the-network-resources-in-azure"></a>Vytvoření síťových prostředků v Azure

Nejprve vytvořte prostředky sítě pro Azure. Následující pokyny ukazují, jak vytvořit prostředky pomocí [webu Azure portal](http://portal.azure.com/).

### <a name="create-the-virtual-network-and-virtual-machine-vm-subnet"></a>Vytvoření virtuální sítě a podsítě virtuálních počítačů (VM)

1. Přihlaste se k [webu Azure portal](http://portal.azure.com/) pomocí svého účtu Azure.
2. Na portálu user portal, vyberte **+ vytvořit prostředek**.
3. Přejděte na **Marketplace**a pak vyberte **sítě**.
4. Vyberte **virtuální síť**.
5. Pomocí informací z tabulky konfigurace sítě a určete hodnoty pro Azure **název**, **adresní prostor**, **název podsítě**, a **adresa podsítě rozsah**.
6. Pro **skupiny prostředků**, vytvořte novou skupinu prostředků, nebo pokud ještě nemáte, vyberte **použít existující**.
7. Vyberte **umístění** vaší virtuální sítě.  Pokud používáte ukázkových hodnot, vyberte **USA – východ** nebo pokud chcete použít jiné umístění.
8. Zaškrtněte **Připnout na řídicí panel**.
9. Vyberte **Vytvořit**.

### <a name="create-the-gateway-subnet"></a>Vytvoření podsítě brány

1. Otevřete prostředek virtuální sítě, jste vytvořili (**AzureVNet**) z řídicího panelu.
2. Na **nastavení** vyberte **podsítě**.
3. Vyberte **podsíť brány** přidat podsíť brány k virtuální síti.
4. Ve výchozím nastavení je název této podsítě nastavený na **GatewaySubnet**.

   >[!IMPORTANT]
   >Podsítě brány jsou speciální a **musí** mít tento konkrétní název, aby správně fungoval.

5. V **rozsah adres** pole, zkontrolujte, adresa **10.100.1.0/24**.
6. Vyberte **OK** vytvořit podsíť brány.

### <a name="create-the-virtual-network-gateway"></a>Vytvoření brány virtuální sítě

1. Na webu Azure Portal, vyberte **+ vytvořit prostředek**.  
2. Přejděte na **Marketplace**a pak vyberte **sítě**.
3. V seznamu síťových prostředků, vyberte **Brána virtuální sítě**.
4. V **název**, typ **Azure-GW**.
5. Chcete-li zvolit virtuální síť, vyberte **virtuální síť**. Potom vyberte **AzureVnet** ze seznamu.
6. Vyberte **veřejnou IP adresu**. Když **zvolte veřejnou IP adresu** části otevře, vyberte **vytvořit nový**.
7. V **název**, typ **Azure-GW-PiP**a pak vyberte **OK**.
8. Ve výchozím nastavení pro **typ sítě VPN**, **založené na trasách** zaškrtnuto. Zachovat **založené na trasách** typ sítě VPN.
9. Ověřte, že nastavení **Předplatné** a **Umístění** jsou správná. Prostředek na řídicí panel můžete připnout. Vyberte **Vytvořit**.

### <a name="create-the-local-network-gateway-resource"></a>Vytváření prostředku brány místní sítě

1. Na webu Azure Portal, vyberte **+ vytvořit prostředek**.
2. Přejděte na **Marketplace**a pak vyberte **sítě**.
3. V seznamu prostředků vyberte **bránu místní sítě**.
4. V **název**, typ **Azs-GW**.
5. V **IP adresu**, zadejte veřejnou IP adresu pro vaše zásobníku brány virtuální sítě Azure, která je uvedena výše v tabulce Konfigurace sítě.
6. V **adresní prostor**, Azure Stack, zadejte **10.1.0.0/24** a **10.1.1.0/24** adresní prostor pro **AzureVNet**.
7. Ověřte, že vaše **předplatné**, **skupiny prostředků**, a **umístění** jsou správné a pak vyberte **vytvořit**.

## <a name="create-the-connection"></a>Vytvoření připojení

1. Na portálu user portal, vyberte **+ vytvořit prostředek**.
2. Přejděte na **Marketplace**a pak vyberte **sítě**.
3. V seznamu prostředků vyberte **připojení**.
4. Na **základní** části nastavení pro **typ připojení**, zvolte **Site-to-site (IPSec)**.
5. Vyberte **předplatné**, **skupiny prostředků**, a **umístění**a pak vyberte **OK**.
6. Na **nastavení** vyberte **Brána virtuální sítě**a pak vyberte **Azure-GW**.
7. Vyberte **bránu místní sítě**a pak vyberte **Azs-GW**.
8. V **název připojení**, typ **Azure Azs**.
9. V **sdílený klíč (PSK)**, typ **12345**. Vyberte **OK**.

   >[!NOTE]
   >Pokud použijete jinou hodnotu pro sdílený klíč, mějte na paměti, že *musí* odpovídat hodnotě pro sdílený klíč, kterou vytvoříte na druhém konci připojení.

10. Zkontrolujte **Souhrn** a potom vyberte **OK**.

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvoření virtuálního počítače v Azure teď a umístí ji podsítě virtuálních počítačů ve vaší virtuální síti.

1. Na webu Azure Portal, vyberte **+ vytvořit prostředek**.
2. Přejděte na **Marketplace**a pak vyberte **Compute**.
3. Vyberte v seznamu imagí virtuálních počítačů **systému Windows Server 2016 Datacenter Oval** bitové kopie.
4. Na **Základy** části pro **název**, typ **AzureVM**.
5. Zadejte platné uživatelské jméno a heslo. Tento účet použijete pro přihlášení k virtuálnímu počítači po jeho vytvoření.
6. Zadejte **předplatné**, **skupiny prostředků**, a **umístění**a pak vyberte **OK**.
7. Na **velikost** části, vyberte velikost virtuálního počítače pro tuto instanci a pak vyberte **vyberte**.
8. Na **nastavení** oddílu, můžete použít výchozí nastavení. Před klepnutím na tlačítko OK, ověřte, že:

   * **AzureVnet** je vybraná virtuální síť.
   * Že podsíť je nastavená na **10.100.0.0/24**.

   Vyberte **OK**.

9. Zkontrolujte nastavení na **Souhrn** a potom vyberte **OK**.

## <a name="create-the-network-resources-in-azure-stack"></a>Vytvoření síťových prostředků ve službě Azure Stack

Dále vytvoříte síťové prostředky ve službě Azure Stack.

### <a name="sign-in-as-a-user"></a>Přihlaste se jako uživatel

Správce služeb můžete přihlásit jako uživatel k testovací plány, nabídky a předplatné, které můžou používat své uživatele. Pokud ho ještě nemáte, [vytvoření uživatelského účtu](azure-stack-add-new-user-aad.md) před přihlášením.

### <a name="create-the-virtual-network-and-a-vm-subnet"></a>Vytvořte virtuální síť a podsíť virtuálních počítačů

1. Použijte uživatelský účet pro přihlášení k portálu user portal.
2. Na portálu user portal, vyberte **+ vytvořit prostředek**.

    ![Vytvořit novou virtuální síť](media/azure-stack-create-vpn-connection-one-node-tp2/image3.png)

3. Přejděte na **Marketplace**a pak vyberte **sítě**.
4. Vyberte **virtuální síť**.
5. Pro **název**, **adresní prostor**, **název podsítě**, a **rozsah adres podsítě**, použijte hodnoty z tabulky konfigurace sítě.
6. V **předplatné**, zobrazí se předplatné, které jste vytvořili dříve.
7. Pro **skupiny prostředků**, můžete vytvořit skupinu prostředků nebo pokud ještě nemáte, vyberte **použít existující**.
8. Ověřte výchozí umístění.
9. Zaškrtněte **Připnout na řídicí panel**.
10. Vyberte **Vytvořit**.

### <a name="create-the-gateway-subnet"></a>Vytvoření podsítě brány

1. Na řídicím panelu otevřete prostředek virtuální sítě Azs-virtuální síť, kterou jste vytvořili.
2. Na **nastavení** vyberte **podsítě**.
3. Chcete-li přidat podsíť brány k virtuální síti, **podsíť brány**.

    ![Přidání podsítě brány](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. Ve výchozím nastavení, název podsítě nastavený na **GatewaySubnet**. Podsítě brány jsou speciální. Aby fungovala správně, musíte použít *GatewaySubnet* název.
5. V **rozsah adres**, ověřte, že je adresa **10.1.1.0/24**.
6. Vyberte **OK** vytvořit podsíť brány.

### <a name="create-the-virtual-network-gateway"></a>Vytvoření brány virtuální sítě

1. Na portálu Azure Stack, vyberte **+ vytvořit prostředek**.
2. Přejděte na **Marketplace**a pak vyberte **sítě**.
3. V seznamu síťových prostředků, vyberte **Brána virtuální sítě**.
4. V **název**, typ **Azs-GW**.
5. Vyberte **virtuální síť** položku, kterou chcete zvolit virtuální síť. Vyberte **Azs-VNet** ze seznamu.
6. Vyberte **veřejnou IP adresu** položky nabídky. Když **zvolte veřejnou IP adresu** části otevře, vyberte **vytvořit nový**.
7. V **název**, typ **Azs-GW-PiP**a pak vyberte **OK**.
8. Ve výchozím nastavení **založené na trasách** vybraná **typ sítě VPN**. Zachovat **založené na trasách** typ sítě VPN.
9. Ověřte, že nastavení **Předplatné** a **Umístění** jsou správná. Prostředek na řídicí panel můžete připnout. Vyberte **Vytvořit**.

### <a name="create-the-local-network-gateway"></a>Vytvoření brány místní sítě

Koncept *bránu místní sítě* ve službě Azure Stack je trochu jiná než v nasazení Azure.

Brána místní sítě v nasazení Azure, představuje fyzické zařízení v místním (na umístění uživatele), připojení k bráně virtuální sítě v Azure. Ale ve službě Azure Stack obou koncích připojení brány virtuální sítě!

Obecnější způsob, jak přemýšlet o tom je, že prostředek brány místní sítě vždycky uvádí vzdálenou bránu na druhém konci připojení.

### <a name="create-the-local-network-gateway-resource"></a>Vytváření prostředku brány místní sítě

1. Přihlaste se k portálu Azure Stack.
2. Na portálu user portal, vyberte **+ vytvořit prostředek**.
3. Přejděte na **Marketplace**a pak vyberte **sítě**.
4. V seznamu prostředků vyberte **bránu místní sítě**.
5. V **název**, typ **Azure-GW**.
6. V **IP adresu**, zadejte veřejnou IP adresu brány virtuální sítě v Azure **Azure-GW-PiP**. Tato adresa se zobrazí výše v tabulce Konfigurace sítě.
7. V **adresní prostor**, adresní prostor virtuální sítě Azure, kterou jste vytvořili, zadejte **10.100.0.0/24** a **10.100.1.0/24**.
8. Ověřte, že vaše **předplatné**, **skupiny prostředků**, a **umístění** jsou správné a pak vyberte **vytvořit**.

### <a name="create-the-connection"></a>Vytvoření připojení

1. Na portálu user portal, vyberte **+ vytvořit prostředek**.
2. Přejděte na **Marketplace**a pak vyberte **sítě**.
3. V seznamu prostředků vyberte **připojení**.
4. Na **Základy** části nastavení pro **typ připojení**vyberte **Site-to-site (IPSec)**.
5. Vyberte **předplatné**, **skupiny prostředků**, a **umístění**a pak vyberte **OK**.
6. Na **nastavení** vyberte **Brána virtuální sítě**a pak vyberte **Azs-GW**.
7. Vyberte **bránu místní sítě**a pak vyberte **Azure-GW**.
8. V **název připojení**, typ **Azs-Azure**.
9. V **sdílený klíč (PSK)**, typ **12345**a pak vyberte **OK**.
10. Na **Souhrn** vyberte **OK**.

### <a name="create-a-virtual-machine-vm"></a>Vytvoření virtuálního počítače (VM)

Pokud chcete zkontrolovat připojení k síti VPN, je potřeba vytvořit dva virtuální počítače, jeden v Azure a jeden v Azure stacku. Jakmile vytvoříte tyto virtuální počítače, můžete je odesílat a přijímat data prostřednictvím tunelového připojení sítě VPN.

1. Na webu Azure Portal, vyberte **+ vytvořit prostředek**.
2. Přejděte na **Marketplace**a pak vyberte **Compute**.
3. Vyberte v seznamu imagí virtuálních počítačů **systému Windows Server 2016 Datacenter Oval** bitové kopie.
4. Na **Základy** sekci **název**, typ **Azs-VM**.
5. Zadejte platné uživatelské jméno a heslo. Tento účet použijete pro přihlášení k virtuálnímu počítači po jeho vytvoření.
6. Zadejte **předplatné**, **skupiny prostředků**, a **umístění**a pak vyberte **OK**.
7. Na **velikost** části této instance, vyberte velikost virtuálního počítače a pak vyberte **vyberte**.
8. Na **nastavení** části, přijměte výchozí hodnoty. Ujistěte se, že **Azs-VNet** je vybraná virtuální síť. Ověřte, že podsíť je nastavená na **10.1.0.0/24**. Pak vyberte **OK**.
9. Na **Souhrn** části, zkontrolujte nastavení a pak vyberte **OK**.

## <a name="test-the-connection"></a>Otestování připojení

Po vytvoření připojení site-to-site, měli byste ověřit, že můžete získat data v obou směrech. Tímto způsobem testu pomocí příkazu ping je nejjednodušší způsob, jak otestovat připojení:

* Přihlaste se k virtuálnímu počítači, který jste vytvořili v Azure stacku a příkaz ping virtuálnímu počítači v Azure.
* Přihlaste se k virtuálnímu počítači, který jste vytvořili v Azure a příkaz ping virtuálnímu počítači v Azure stacku.

>[!NOTE]
>Pokud chcete mít jistotu, že odesíláte přenosy přes připojení site-to-site, odešlete zprávu ping Direct IP (DIP) adresu virtuálního počítače ve vzdálené podsíti, nikoli virtuální IP adresy.

### <a name="sign-in-to-the-user-vm-in-azure-stack"></a>Přihlášení uživatele virtuálního počítače ve službě Azure Stack

1. Přihlaste se k portálu Azure Stack.
2. V levém navigačním panelu vyberte **virtuálních počítačů**.
3. V seznamu virtuálních počítačů, najděte **Azs-VM** , který jste vytvořili dříve a pak ho vyberte.
4. V části pro virtuální počítač, vyberte **připojit**a pak otevřete soubor Azs-VM.rdp.

     ![Tlačítko pro připojení](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)

5. Přihlaste se pomocí účtu, který jste nakonfigurovali při vytváření virtuálního počítače.
6. Otevřete zvýšenými **prostředí Windows PowerShell** okna.
7. Zadejte **ipconfig /all**.
8. Ve výstupu vyhledejte **IPv4 adresu**a potom uložte adresu pro pozdější použití. Jde o adresu, kterou použijete pro příkaz ping z Azure. V ukázkovém prostředí je adresa **10.1.0.4**, ale ve vašem prostředí může být jiná. By měla spadat do **10.1.0.0/24** podsítě, kterou jste vytvořili dříve.
9. Pokud chcete vytvořit pravidlo brány firewall, které umožňuje virtuálnímu počítači reagovat na příkazy ping pro zjištění, spusťte následující příkaz Powershellu:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-azure"></a>Přihlaste se k tenantovi virtuální počítač v Azure

1. Přihlaste se k portálu Azure.
2. V levém navigačním panelu vyberte **virtuálních počítačů**.
3. Ze seznamu virtuálních počítačů, Najít **virtuálního počítače Azure** , který jste vytvořili dříve a pak ho vyberte.
4. V části pro virtuální počítač, vyberte **připojit**.
5. Přihlaste se pomocí účtu, který jste nakonfigurovali při vytváření virtuálního počítače.
6. Otevřete zvýšenými **prostředí Windows PowerShell** okna.
7. Zadejte **ipconfig /all**.
8. Měla by se zobrazit IPv4 adresu, která spadá do **10.100.0.0/24**. V ukázkovém prostředí je adresa **10.100.0.4**, ale vaše adresa může být jiný.
9. Pokud chcete vytvořit pravidlo brány firewall, které umožňuje virtuálnímu počítači reagovat na příkazy ping pro zjištění, spusťte následující příkaz Powershellu:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

10. Z virtuálního počítače v Azure odešlete zprávu ping virtuálního počítače ve službě Azure Stack prostřednictvím tunelu. K tomu příkaz ping použijte DIP, který jste si poznamenali z Azs-VM. V ukázkovém prostředí je to **10.1.0.4**, ale je potřeba příkaz ping na adresu, kterou jste si poznamenali ve vaší laboratoři. Zobrazí se výsledek, který bude vypadat jako následující snímek obrazovky:

    ![Úspěšný příkaz ping](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)

11. Odpověď od vzdáleného virtuálního počítače označuje test proběhl úspěšně! V okně virtuálního počítače můžete zavřít.

Také byste měli dělat přísnější přenos dat testování. Například kopírování různých velikostí souborů v obou směrech.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Zobrazení statistiky přenosu dat prostřednictvím připojení brány

Pokud chcete vědět, kolik dat prochází připojení site-to-site, tyto informace jsou k dispozici na **připojení** oddílu. Tento test je také dalším způsobem, jak ověřit, že příkaz ping, který jste právě odeslali, skutečně prošel přes připojení VPN.

1. Když jste přihlášení k virtuálnímu počítači uživatele ve službě Azure Stack, pomocí uživatelského účtu k přihlášení k portálu user portal.
2. Přejděte na **všechny prostředky**a pak vyberte **Azs-Azure** připojení. **Připojení** se zobrazí.
3. Na **připojení** oddílu, statistiky pro **Data v** a **výstupní Data** zobrazí. Na následujícím snímku obrazovky jsou velké počty přiřadit přenos dalších souborů. Měli byste vidět některé nenulové hodnoty.

    ![Vstupní a výstupní data](media/azure-stack-connect-vpn/Connection.png)

## <a name="next-steps"></a>Další postup

[Nasazení aplikací do Azure a Azure Stack](azure-stack-solution-pipeline.md)
