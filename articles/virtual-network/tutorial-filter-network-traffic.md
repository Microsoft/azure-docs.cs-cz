---
title: Filtrování síťového provozu – kurz – Azure Portal
titlesuffix: Azure Virtual Network
description: V tomto kurzu se dozvíte, jak na webu Azure Portal použít skupinu zabezpečení sítě k filtrování síťového provozu do podsítě.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.service: virtual-network
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/13/2018
ms.author: jdial
ms.openlocfilehash: caf9b91d5b98d028d7c9e971df30ad1f6ec448ad
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54019023"
---
# <a name="tutorial-filter-network-traffic-with-a-network-security-group-using-the-azure-portal"></a>Kurz: Filtrování síťového provozu se skupinou zabezpečení sítě pomocí webu Azure Portal

Příchozí a odchozí provoz podsítě virtuální sítě můžete filtrovat pomocí skupiny zabezpečení sítě. Skupiny zabezpečení sítě obsahují pravidla zabezpečení, která filtrují síťový provoz podle IP adresy, portu a protokolu. Pravidla zabezpečení se vztahují na prostředky nasazené v podsíti. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření skupiny zabezpečení sítě a pravidel zabezpečení
> * Vytvoření virtuální sítě a přidružení skupiny zabezpečení sítě k podsíti
> * Nasazení virtuálních počítačů do podsítě
> * Testování filtrů provozu

K dokončení tohoto kurzu můžete použít [Azure CLI](tutorial-filter-network-traffic-cli.md) nebo [PowerShell](tutorial-filter-network-traffic-powershell.md) podle toho, čemu dáváte přednost.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. V levém horním rohu webu Azure Portal vyberte **+ Vytvořit prostředek**.
2. Vyberte **Sítě** a pak vyberte **Virtuální síť**.
3. Zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **Vytvořit**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Název                    | myVirtualNetwork                                   |
    | Adresní prostor           | 10.0.0.0/16                                        |
    | Předplatné            | Vyberte své předplatné.                          |
    | Skupina prostředků          | Vyberte **Vytvořit novou** a zadejte *myResourceGroup*. |
    | Umístění                | Vyberte **USA – východ**.                                |
    | Podsíť – Název            | mySubnet                                           |
    | Podsíť – Rozsah adres  | 10.0.0.0/24                                        |

## <a name="create-application-security-groups"></a>Vytvoření skupin zabezpečení aplikací

Skupina zabezpečení aplikací umožňuje seskupovat servery s podobnými funkcemi, například webové servery.

1. V levém horním rohu webu Azure Portal vyberte **+ Vytvořit prostředek**.
2. Do pole **Hledat na Marketplace** zadejte *Skupina zabezpečení aplikace*. Jakmile se ve výsledcích hledání zobrazí **Skupina zabezpečení aplikace**, vyberte ji. V části **Všechno** vyberte **Skupina zabezpečení aplikace** a pak vyberte **Vytvořit**.
3. Zadejte nebo vyberte následující informace a pak vyberte **Vytvořit**:

    | Nastavení        | Hodnota                                                         |
    | ---            | ---                                                           |
    | Název           | myAsgWebServers                                               |
    | Předplatné   | Vyberte své předplatné.                                     |
    | Skupina prostředků | Vyberte **Použít existující** a pak vyberte **myResourceGroup**. |
    | Umístění       | USA – východ                                                       |

4. Zopakujte 3. krok s použitím následujících hodnot:

    | Nastavení        | Hodnota                                                         |
    | ---            | ---                                                           |
    | Název           | myAsgMgmtServers                                              |
    | Předplatné   | Vyberte své předplatné.                                     |
    | Skupina prostředků | Vyberte **Použít existující** a pak vyberte **myResourceGroup**. |
    | Umístění       | USA – východ                                                       |

## <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

1. V levém horním rohu webu Azure Portal vyberte **+ Vytvořit prostředek**.
2. Vyberte **Sítě** a pak **Skupina zabezpečení sítě**.
3. Zadejte nebo vyberte následující informace a pak vyberte **Vytvořit**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|myNsg|
    |Předplatné| Vyberte své předplatné.|
    |Skupina prostředků | Vyberte **Použít existující** a pak vyberte *myResourceGroup*.|
    |Umístění|USA – východ|

## <a name="associate-network-security-group-to-subnet"></a>Přidružení skupiny zabezpečení sítě k podsíti

1. Do pole *Hledat prostředky, služby a dokumenty* v horní části portálu začněte psát *myNsg*. Jakmile se ve výsledcích hledání zobrazí položka **myNsg**, vyberte ji.
2. V části **NASTAVENÍ** vyberte **Podsítě** a pak vyberte **+ Přidružit**. Je to vidět na následujícím obrázku:

    ![Přidružení NSG k podsíti](./media/tutorial-filter-network-traffic/associate-nsg-subnet.png)

3. V části **Přidružit podsíť** vyberte **Virtuální síť** a pak vyberte **myVirtualNetwork**. Vyberte **Podsíť**, vyberte **mySubnet** a pak vyberte **OK**.

## <a name="create-security-rules"></a>Vytvoření pravidel zabezpečení

1. V části **NASTAVENÍ** vyberte **Příchozí pravidla zabezpečení** a pak vyberte **+ Přidat**. Je to vidět na následujícím obrázku:

    ![Přidání příchozího pravidla zabezpečení](./media/tutorial-filter-network-traffic/add-inbound-rule.png)

2. Vytvořte pravidlo zabezpečení, které skupině zabezpečení aplikace **myAsgWebServers** povolí porty 80 a 443. V části **Přidat příchozí pravidlo zabezpečení** zadejte nebo vyberte následující hodnoty, potvrďte zbývající výchozí hodnoty a vyberte **Přidat**:

    | Nastavení                 | Hodnota                                                                                                           |
    | ---------               | ---------                                                                                                       |
    | Cíl             | Vyberte **Skupina zabezpečení aplikace** a pak v položce **Skupina zabezpečení aplikace** vyberte **myAsgWebServers**.  |
    | Rozsahy cílových portů | Zadejte 80, 443.                                                                                                    |
    | Protocol (Protokol)                | Vyberte TCP.                                                                                                      |
    | Název                    | Allow-Web-All                                                                                                   |

3. Zopakujte 2. krok s použitím následujících hodnot:

    | Nastavení                 | Hodnota                                                                                                           |
    | ---------               | ---------                                                                                                       |
    | Cíl             | Vyberte **Skupina zabezpečení aplikace** a pak v položce **Skupina zabezpečení aplikace** vyberte **myAsgMgmtServers**. |
    | Rozsahy cílových portů | Zadejte 3389.                                                                                                      |
    | Protocol (Protokol)                | Vyberte TCP.                                                                                                      |
    | Priorita                | Zadejte 110.                                                                                                       |
    | Název                    | Allow-RDP-All                                                                                                   |

    V tomto kurzu je z internetu přístupný protokol RDP (port 3389) pro virtuální počítač, který je zařazený do skupiny zabezpečení aplikace *myAsgMgmtServers*. V produkčním prostředí se místo portu 3389 přístupného z internetu doporučuje použít pro připojení k prostředkům Azure, které chcete spravovat, síť VPN nebo privátní síťové připojení.

Po dokončení kroků 1–3 zkontrolujte vytvořená pravidla. Seznam by měl být podobný jako na následujícím obrázku:

![Pravidla zabezpečení](./media/tutorial-filter-network-traffic/security-rules.png)

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte ve virtuální síti dva virtuální počítače.

### <a name="create-the-first-vm"></a>Vytvoření prvního virtuálního počítače

1. V levém horním rohu webu Azure Portal vyberte **+ Vytvořit prostředek**.
2. Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**.
3. Zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **OK**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|myVmWeb|
    |Uživatelské jméno| Zadejte libovolné uživatelské jméno.|
    |Heslo| Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Předplatné| Vyberte své předplatné.|
    |Skupina prostředků| Vyberte **Použít existující** a pak vyberte **myResourceGroup**.|
    |Umístění| Vyberte **USA – východ**.|

4. Vyberte velikost virtuálního počítače a pak vyberte **Vybrat**.
5. V části **Nastavení** vyberte následující hodnoty, potvrďte zbývající výchozí hodnoty a vyberte **OK**:

    |Nastavení|Hodnota|
    |---|---|
    |Virtuální síť |Vyberte **myVirtualNetwork**.|
    |Skupina zabezpečení sítě | Vyberte **Upřesnit**.|
    |Skupina zabezpečení sítě (brána firewall)| Vyberte **(new) myVmWeb-nsg** a pak v části **Vyberte skupinu zabezpečení sítě** vyberte **Žádná**. |

6. V části **Vytvořit** na kartě **Souhrn** vyberte **Vytvořit** a spusťte nasazování virtuálního počítače.

### <a name="create-the-second-vm"></a>Vytvoření druhého virtuálního počítače

Zopakujte kroky 1 až 6, ale ve 3. kroku pojmenujte virtuální počítač *myVmMgmt*. Nasazení virtuálního počítače trvá několik minut. Nepokračujte v dalším kroku, dokud není virtuální počítač nasazený.

## <a name="associate-network-interfaces-to-an-asg"></a>Přidružení síťových rozhraní ke skupině zabezpečení aplikace

Když portál vytvoří virtuální počítače, vytvoří pro každý z nich síťové rozhraní, které připojí k virtuálnímu počítači. Síťové rozhraní každého virtuálního počítače přidejte k jedné ze dříve vytvořených skupin zabezpečení aplikace:

1. Do pole *Hledat prostředky, služby a dokumenty* v horní části portálu začněte psát *myVmWeb*. Jakmile se ve výsledcích hledání zobrazí virtuální počítač **myVmWeb**, vyberte ho.
2. V části **NASTAVENÍ** vyberte **Sítě**.  Vyberte **Konfigurovat skupiny zabezpečení aplikace**. V poli **Skupiny zabezpečení aplikace** vyberte **myAsgWebServers** a pak vyberte **Uložit**. Je to vidět na následujícím obrázku:

    ![Přidružení ke skupině zabezpečení aplikace](./media/tutorial-filter-network-traffic/associate-to-asg.png)

3. Zopakujte kroky 1 a 2, najděte virtuální počítač **myVmMgmt** a vyberte skupinu zabezpečení aplikace **myAsgMgmtServers**.

## <a name="test-traffic-filters"></a>Testování filtrů provozu

1. Připojte se k virtuálnímu počítači *myVmMgmt*. Do vyhledávacího pole nahoře v portálu zadejte *myVmMgmt*. Jakmile se ve výsledcích hledání zobrazí položka **myVmMgmt**, vyberte ji. Klikněte na tlačítko **Připojit**.
2. Vyberte **Stáhnout soubor RDP**.
3. Otevřete stažený soubor RDP a vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná bude nutné vybrat **Další možnosti** a pak **Použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.
4. Vyberte **OK**.
5. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se toto upozornění zobrazí, vyberte **Ano** nebo **Pokračovat** a pokračujte v připojování.

    Připojení bude úspěšné, protože do skupiny zabezpečení aplikací *myAsgMgmtServers*, která obsahuje síťové rozhraní připojené k virtuálnímu počítači *myVmMgmt*, je povolený příchozí provoz z internetu na portu 3389.

6. Z virtuálního počítače *myVmMgmt* se připojte k virtuálnímu počítači *myVmWeb*. V relaci PowerShellu zadejte následující příkaz:

    ``` 
    mstsc /v:myVmWeb
    ```

    Z virtuálního počítače myVmMgmt se můžete připojit k virtuálnímu počítači myVmWeb, protože ve výchozím nastavení spolu mohou virtuální počítače ve stejné virtuální síti navzájem komunikovat přes jakýkoli port. K virtuálnímu počítači *myVmWeb* se ale nemůžete připojit jako ke vzdálené ploše z internetu, protože pravidlo zabezpečení pro skupinu zabezpečení aplikace *myAsgWebServers* nepovoluje na portu 3389 příchozí provoz z internetu. Ve výchozím nastavení je příchozí provoz z internetu u všech prostředků zakázaný.

7. Pokud chcete na virtuální počítač *myVmWeb* nainstalovat Microsoft IIS, zadejte v relaci PowerShellu na virtuálním počítači *myVmWeb* následující příkaz:

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

8. Po dokončení instalace služby IIS se odpojte od virtuálního počítače *myVmWeb*. Stále zůstanete připojeni ke vzdálené ploše virtuálního počítače *myVmMgmt*.
9. Odpojte se od virtuálního počítače *myVmMgmt*.
10. Do pole *Hledat prostředky, služby a dokumenty* v horní části webu Azure Portal začněte na svém počítači psát *myVmWeb*. Jakmile se ve výsledcích hledání zobrazí položka **myVmWeb**, vyberte ji. Poznamenejte si **veřejnou IP adresu** svého virtuálního počítače. Na následujícím obrázku je vidět adresa 137.135.84.74, ale vaše bude jiná:

    ![Veřejná IP adresa](./media/tutorial-filter-network-traffic/public-ip-address.png)
  
11. Pokud si chcete ověřit, že máte z internetu přístup k webovému serveru *myVmWeb*, otevřete na počítači internetový prohlížeč a přejděte na adresu `http://<public-ip-address-from-previous-step>`. Zobrazí se úvodní obrazovka služby IIS, protože skupina zabezpečení aplikace *myAsgWebServers*, ve které je síťové rozhraní připojené k virtuálnímu počítači *myVmWeb*, povoluje na portu 80 příchozí provoz z internetu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Do pole **Hledat** v horní části portálu zadejte *myResourceGroup*. Jakmile se ve výsledcích hledání zobrazí skupina prostředků **myResourceGroup**, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. V části **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ** zadejte *myResourceGroup* a vyberte **Odstranit**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili skupinu zabezpečení sítě a přidružili jste ji k podsíti virtuální sítě. Další informace o skupinách zabezpečení sítě najdete v tématech [Přehled skupin zabezpečení sítě](security-overview.md) a [Správa skupiny zabezpečení sítě](manage-network-security-group.md).

Provoz mezi podsítěmi směruje ve výchozím nastavení Azure. Místo toho se můžete rozhodnout směrovat provoz mezi podsítěmi například prostřednictvím virtuálního počítače, který slouží jako brána firewall. Pokud chcete zjistit, jak vytvořit směrovací tabulku, pokračujte k dalšímu kurzu.

> [!div class="nextstepaction"]
> [Vytvoření směrovací tabulky](./tutorial-create-route-table-portal.md)