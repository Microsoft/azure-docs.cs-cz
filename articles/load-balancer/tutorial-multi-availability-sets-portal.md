---
title: 'Kurz: Vytvoření nástroje pro vyrovnávání zatížení s více než jednou sadou dostupnosti ve fondu back-endu – Azure Portal'
titleSuffix: Azure Load Balancer
description: V tomto kurzu nasadíte Azure Load Balancer s více než jednou sadou dostupnosti ve fondu back-end.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 04/21/2021
ms.custom: template-tutorial
ms.openlocfilehash: 71115da01f47572d77243f25204d5b1127db22cd
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107887157"
---
# <a name="tutorial-create-a-load-balancer-with-more-than-one-availability-set-in-the-backend-pool-using-the-azure-portal"></a>Kurz: Vytvoření nástroje pro vyrovnávání zatížení s více než jednou sadou dostupnosti ve fondu back-endu pomocí Azure Portal

V rámci nasazení s vysokou dostupností jsou virtuální počítače často seskupené do několika skupin dostupnosti. 

Load Balancer podporuje více než jednu skupinu dostupnosti s virtuálními počítači ve fondu back-end.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření virtuální sítě
> * Vytvoření brány NAT pro odchozí připojení
> * Vytvoření standardní SKU Azure Load Balancer
> * Vytvoření čtyř virtuálních počítačů a dvou skupin dostupnosti
> * Přidání virtuálních počítačů ve skupinách dostupnosti do back-endu fondu nástroje pro vyrovnávání zatížení
> * Testování Load Balanceru

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

V této části vytvoříte virtuální síť pro nástroj pro vyrovnávání zatížení a další prostředky použité v tomto kurzu.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

2. Do vyhledávacího pole v horní části portálu zadejte **Virtual Network**.

3. Ve výsledcích hledání vyberte možnost **virtuální sítě**.

4. Vyberte **+ vytvořit**.

5. Na kartě **základy** v části **vytvořit virtuální síť** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ------|
    | **Podrobnosti o projektu** |   |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte, že chcete **vytvořit novou** IP adresu. </br> Do **názvu** zadejte **TutorLBmultiAVS-RG** . |
    | **Podrobnosti o instancích** |   |
    | Name | Zadejte **myVNet**. |
    | Oblast | Vyberte **(USA) USA – západ 2**. |

6. Vyberte kartu **IP adresy** nebo tlačítko **Další: IP adresy** v dolní části stránky.

7. Na kartě **IP adresy** v části **název podsítě** vyberte **výchozí**.

8. V podokně **Upravit podsíť** v části **název podsítě** zadejte **myBackendSubnet**.

9. Vyberte **Uložit**.

10. V dolní části stránky vyberte kartu **zabezpečení** nebo tlačítko **Další: zabezpečení** .

11. Na kartě **zabezpečení** v **BastionHost** vyberte **Povolit**.

12. Zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název bastionu | Zadejte **MyBastionHost**. |
    | Adresní prostor AzureBastionSubnet | Zadejte **10.1.1.0/27**. |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. </br> Jako **název** zadejte **myBastionIP** . |

13. Vyberte kartu **Revize + vytvořit** nebo modré tlačítko **Revize + vytvořit** v dolní části stránky.

14. Vyberte **Vytvořit**.

## <a name="create-nat-gateway"></a>Vytvoření brány NAT 

V této části vytvoříte bránu NAT pro odchozí připojení virtuálních počítačů.

1. Do vyhledávacího pole v horní části portálu zadejte **bránu NAT**.

2. Ve výsledcích hledání vyberte **brány NAT** .

3. Vyberte **+ vytvořit**.

4. Na kartě **základy** pro **Vytvoření brány NAT (Network Address Translation)** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** |   |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **TutorLBmultiAVS-RG**. |
    | **Podrobnosti o instancích** |   |
    | Název brány NAT | Zadejte **myNATgateway**. |
    | Oblast | Vyberte **(USA) USA – západ 2**. |
    | Zóna dostupnosti | Vyberte **Žádná**. |
    | Časový limit nečinnosti (minuty) | Zadejte **15**. |

5. Vyberte kartu **odchozí IP adresa** nebo klikněte na tlačítko **Další: odchozí IP adresa** v dolní části stránky.

6. Na kartě **odchozí IP** **Adresa vyberte vytvořit novou veřejnou IP adresu** vedle **veřejné IP adresy** .

7. Jako **název** zadejte **myPublicIP-NAT** .

8. Vyberte **OK**.

9. Vyberte kartu **podsíť** nebo vyberte tlačítko **Další: podsíť** v dolní části stránky.

10. V rozevírací nabídce v části **virtuální síť** vyberte **myVNet** .

11. Zaškrtněte políčko vedle **myBackendSubnet**.

12. Vyberte kartu **Revize + vytvořit** nebo v dolní části stránky vyberte tlačítko modrá **recenze + vytvořit** .

13. Vyberte **Vytvořit**.

## <a name="create-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

V této části vytvoříte Nástroj pro vyrovnávání zatížení pro virtuální počítače.

1. Do vyhledávacího pole v horní části portálu zadejte **Nástroj pro vyrovnávání zatížení**.

2. Ve výsledcích hledání vyberte nástroje pro **Vyrovnávání zatížení** .

3. Vyberte **+ vytvořit**.

4. Na kartě **základy** pro možnost **vytvořit nástroj pro vyrovnávání zatížení** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** |   |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **TutorLBmultiAVS-RG**. |
    | **Podrobnosti o instancích** |   |
    | Name | Zadejte **myLoadBalancer**. |
    | Oblast | Vyberte **(USA) USA – západ 2**. |
    | Typ | Nechte výchozí nastavení **veřejné**. |
    | SKU | Ponechte výchozí hodnotu **Standard**. |
    | Úroveň | Ponechte výchozí nastavení **místní**. |
    | **Veřejná IP adresa** |   |
    | Veřejná IP adresa | Ponechte výchozí hodnotu **vytvořit novou**. |
    | Název veřejné IP adresy | Zadejte **myPublicIP-kg**. |
    | Zóna dostupnosti | Vyberte **zóna – redundantní**. |
    | Přidat veřejnou IPv6 adresu | Ponechte výchozí hodnotu **ne**. |
    | Předvolba směrování | Ponechte výchozí **síť Microsoft**. |

5. Vyberte kartu **Revize + vytvořit** nebo v dolní části stránky vyberte tlačítko modrá **recenze + vytvořit** .

6. Vyberte **Vytvořit**.

### <a name="configure-load-balancer-settings"></a>Konfigurace nastavení nástroje pro vyrovnávání zatížení

V této části vytvoříte back-end fond pro **myLoadBalancer**.

Vytvoříte sondu stavu pro monitorování **protokolu HTTP** a **portu 80**. Sonda stavu bude monitorovat stav virtuálních počítačů ve fondu back-endu. 

Vytvoříte pravidlo vyrovnávání zatížení pro **Port 80** a odchozí SNAT je zakázané. Brána NAT, kterou jste vytvořili dříve, zpracuje odchozí připojení virtuálních počítačů.

1. Do vyhledávacího pole v horní části portálu zadejte **Nástroj pro vyrovnávání zatížení**.

2. Ve výsledcích hledání vyberte nástroje pro **Vyrovnávání zatížení** .

3. Vyberte **myLoadBalancer**.

4. V **myLoadBalancer** vyberte v **Nastavení** **fondy back-endu** .

5. Ve **fondech back-end** vyberte **+ Přidat** .

6. V části **Přidat back-end fond** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte **myBackendPool**. |
    | Virtuální síť | Vyberte **myVNet**. |
    | Konfigurace fondu back-endu | Ponechte výchozí nastavení **síťové karty**. |
    | Verze protokolu IP | Ponechte výchozí hodnotu **IPv4**. |

7. Vyberte **Přidat**.

8. Vyberte **sondy stavu**.

9. Vyberte **+ Přidat**.

10. V části **Přidat sondu stavu** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte **myHTTPProbe**. |
    | Protokol | Vyberte **http**. |
    | Port | Ponechte výchozí hodnotu **80**. |
    | Cesta | Ponechte výchozí hodnotu **/** . |
    | Interval | Ponechte výchozí hodnotu **5** sekund. |
    | Prahová hodnota pro poškozený stav | Ponechte výchozí **2** po sobě jdoucí selhání. |

11. Vyberte **Přidat**.

12. Vyberte **pravidla vyrovnávání zatížení**. 

13. Vyberte **+ Přidat**.

14. V části **Přidat pravidlo vyrovnávání zatížení** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte **myHTTPRule**. |
    | Verze protokolu IP | Ponechte výchozí hodnotu **IPv4**. |
    | IP adresa front-endu | Vyberte **LoadBalancerFrontEnd**. |
    | Protokol | Vyberte výchozí nastavení **TCP**. |
    | Port | Zadejte **80**. |
    | Back-endový port | Zadejte **80**. |
    | Back-endový fond | Vyberte **myBackendPool**. |
    | Sonda stavu | Vyberte **myHTTPProbe**. |
    | Trvalost relace | Ponechte výchozí **hodnotu None**. |
    | Časový limit nečinnosti (minuty) | Změňte posuvník na **15**. |
    | Resetování protokolu TCP | Vyberte **Povoleno**. |
    | Plovoucí IP adresa | Ponechte výchozí hodnotu **disabled (zakázáno**). |
    | Překlad odchozích adres zdrojové sítě (SNAT) | Ponechte výchozí nastavení **(doporučeno) použít odchozí pravidla a poskytněte členům fondu back-end přístup k Internetu.** |

5. Vyberte **Přidat**.

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

V této části vytvoříte dvě skupiny dostupnosti se dvěma virtuálními počítači na skupinu. Tyto počítače se během vytváření přidají do fondu back-endu nástroje pro vyrovnávání zatížení. 

### <a name="create-first-set-of-vms"></a>Vytvořit první sadu virtuálních počítačů

1. V levém horním oddílu portálu vyberte **+ vytvořit prostředek** .

2. V **nové** vyberte **COMPUTE**  >  **virtuální počítač**.

3. Na kartě **základy** pro **Vytvoření virtuálního počítače** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** |   |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **TutorLBmultiAVS-RG**. |
    | **Podrobnosti o instancích** |   |
    | Název virtuálního počítače | Zadejte **myVM1**. |
    | Oblast | Vyberte **(USA) USA – západ 2**. |
    | Možnosti dostupnosti | Vyberte **skupinu dostupnosti**. |
    | Skupina dostupnosti | Vyberte, že chcete **vytvořit novou** IP adresu. </br> Jako **název** zadejte **myAvailabilitySet1** . </br> Vyberte **OK**. |
    | Image | Vyberte **Windows Server 2019 Datacenter – Gen1**. |
    | Instance Azure Spot | Nechte výchozí nastavení nezaškrtnuto. |
    | Velikost | Vyberte velikost virtuálního počítače. |
    | **Účet správce** |   |
    | Uživatelské jméno | Zadejte uživatelské jméno. |
    | Heslo | Zadejte heslo. |
    | **Pravidla portů pro příchozí spojení** |   |
    | Veřejné příchozí porty | Vyberte **Žádná**. |

4. Vyberte kartu **síť** , nebo klikněte na tlačítko **Další: disky**, **Další: síťové** tlačítko v dolní části stránky.

5. Na kartě **sítě** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Síťové rozhraní** |   |
    | Virtuální síť | Vyberte **myVNet**. |
    | Podsíť | Vyberte **myBackendSubnet**. |
    | Veřejná IP adresa | Vyberte **Žádná**. |
    | Skupina zabezpečení sítě NIC | Vyberte **Upřesnit**. |
    | Konfigurovat skupinu zabezpečení sítě | Vyberte, že chcete **vytvořit novou** IP adresu. </br> Do **název** zadejte **myNSG**. </br> V **příchozích pravidlech** vyberte **+ Přidat příchozí pravidlo** . </br> Vyberte **http** pro **službu**. </br> Jako **název** zadejte **myHTTPrule** . </br> Vyberte **Přidat**. </br> Vyberte **OK**. | 
    | **Vyrovnávání zatížení** |   |
    | Umístit tento virtuální počítač za stávající řešení vyrovnávání zatížení? | Zaškrtněte toto políčko. |
    | **Nastavení vyrovnávání zatížení** |   |
    | Možnosti vyrovnávání zatížení | Vyberte **Azure Load Balancer**. |
    | Vyberte nástroj pro vyrovnávání zatížení. | Vyberte **myLoadBalancer**. |
    | Vybrat back-end fond | Vyberte **myBackendPool**. |

6. Vyberte kartu **Revize + vytvořit** nebo v dolní části stránky vyberte tlačítko modrá **recenze + vytvořit** .

7. Vyberte **Vytvořit**.

8. Opakováním kroků 1 až 7 vytvořte druhý virtuální počítač sady. Nahraďte nastavení virtuálního počítače následujícími informacemi:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte **myVM2**. |
    | Skupina dostupnosti | Vyberte **myAvailabilitySet1**. |
    | Virtual Network | Vyberte **myVNet**. |
    | Podsíť | Vyberte **myBackendSubnet**. |
    | Veřejná IP adresa | Vyberte **Žádná**. |
    | Skupina zabezpečení sítě | Vyberte **myNSG**. |
    | Možnosti vyrovnávání zatížení | Vyberte **Azure Load Balancer**. |
    | Vyberte nástroj pro vyrovnávání zatížení. | Vyberte **myLoadBalancer**. |
    | Vybrat back-end fond | Vyberte **myBackendPool**. |

### <a name="create-second-set-of-vms"></a>Vytvořit druhou sadu virtuálních počítačů

1. V levém horním oddílu portálu vyberte **+ vytvořit prostředek** .

2. V **nové** vyberte **COMPUTE**  >  **virtuální počítač**.

3. Na kartě **základy** pro **Vytvoření virtuálního počítače** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** |   |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **TutorLBmultiAVS-RG**. |
    | **Podrobnosti o instancích** |   |
    | Název virtuálního počítače | Zadejte **myVM3**. |
    | Oblast | Vyberte **(USA) USA – západ 2**. |
    | Možnosti dostupnosti | Vyberte **skupinu dostupnosti**. |
    | Skupina dostupnosti | Vyberte, že chcete **vytvořit novou** IP adresu. </br> Jako **název** zadejte **myAvailabilitySet2** . </br> Vyberte **OK**. |
    | Image | Vyberte **Windows Server 2019 Datacenter – Gen1**. |
    | Instance Azure Spot | Nechte výchozí nastavení nezaškrtnuto. |
    | Velikost | Vyberte velikost virtuálního počítače. |
    | **Účet správce** |   |
    | Uživatelské jméno | Zadejte uživatelské jméno. |
    | Heslo | Zadejte heslo. |
    | **Pravidla portů pro příchozí spojení** |   |
    | Veřejné příchozí porty | Vyberte **Žádná**. |

4. Vyberte kartu **síť** , nebo klikněte na tlačítko **Další: disky**, **Další: síťové** tlačítko v dolní části stránky.

5. Na kartě **sítě** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Síťové rozhraní** |   |
    | Virtuální síť | Vyberte **myVNet**. |
    | Podsíť | Vyberte **myBackendSubnet**. |
    | Veřejná IP adresa | Vyberte **Žádná**. |
    | Skupina zabezpečení sítě NIC | Vyberte **Upřesnit**. |
    | Konfigurovat skupinu zabezpečení sítě | Vyberte **myNSG**. | 
    | **Vyrovnávání zatížení** |   |
    | Umístit tento virtuální počítač za stávající řešení vyrovnávání zatížení? | Zaškrtněte toto políčko. |
    | **Nastavení vyrovnávání zatížení** |   |
    | Možnosti vyrovnávání zatížení | Vyberte **Azure Load Balancer**. |
    | Vyberte nástroj pro vyrovnávání zatížení. | Vyberte **myLoadBalancer**. |
    | Vybrat back-end fond | Vyberte **myBackendPool**. |

6. Vyberte kartu **Revize + vytvořit** nebo v dolní části stránky vyberte tlačítko modrá **recenze + vytvořit** .

7. Vyberte **Vytvořit**.

8. Opakováním kroků 1 až 7 vytvořte druhý virtuální počítač sady. Nahraďte nastavení virtuálního počítače následujícími informacemi:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte **myVM4**. |
    | Skupina dostupnosti | Vyberte **myAvailabilitySet2**. |
    | Virtual Network | Vyberte **myVNet**. |
    | Skupina zabezpečení sítě | Vyberte **myNSG**. |
    | Možnosti vyrovnávání zatížení | Vyberte **Azure Load Balancer**. |
    | Vyberte nástroj pro vyrovnávání zatížení. | Vyberte **myLoadBalancer**. |
    | Vybrat back-end fond | Vyberte **myBackendPool**. |

## <a name="install-iis"></a>Instalace služby IIS

V této části použijete hostitele Azure bastionu, který jste dříve vytvořili pro připojení k virtuálním počítačům a instalaci služby IIS.

1. Do vyhledávacího pole v horní části portálu zadejte **virtuální počítač**.

2. Ve výsledcích hledání vyberte **virtuální počítače** .

3. Vyberte **myVM1**.

4. Na stránce **Přehled** v myVM1 vyberte **připojit**  >  **bastionu**.

5. Vyberte **použít bastionu**.

6. Zadejte **uživatelské jméno** a **heslo** , které jste vytvořili při vytváření virtuálního počítače.

7. Vyberte **Connect** (Připojit).

7. Na ploše serveru přejděte do části **Nástroje pro správu Windows**  >  **Windows PowerShell**.

8. V okně PowerShellu spusťte následující příkazy pro:

    * Instalace serveru služby IIS
    * Odebrat výchozí soubor iisstart.htm
    * Přidejte nový soubor iisstart.htm, který zobrazuje název virtuálního počítače:

   ```powershell
    # Install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    Remove-Item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
8. Zavřete relaci bastionu s **myVM1**.

9. Opakujte kroky 1 až 8 pro **myVM2**, **myVM3** a **myVM4**.

## <a name="test-the-load-balancer"></a>Testování Load Balanceru

V této části zjistíte veřejnou IP adresu nástroje pro vyrovnávání zatížení. Tuto IP adresu použijete k otestování operace nástroje pro vyrovnávání zatížení.

1. Do vyhledávacího pole v horní části portálu zadejte **veřejnou IP adresu**.

2. Ve výsledcích hledání vyberte **veřejné IP adresy** .

3. Vyberte **myPublicIP-kg**.

4. Poznamenejte si veřejnou IP adresu uvedenou na **adrese IP** na stránce **Přehled** **myPublicIP-9,1**:

    :::image type="content" source="./media/tutorial-multi-availability-sets-portal/find-public-ip.png" alt-text="Vyhledejte veřejnou IP adresu nástroje pro vyrovnávání zatížení." border="true":::

5. Otevřete webový prohlížeč a zadejte veřejnou IP adresu do adresního řádku:

    :::image type="content" source="./media/tutorial-multi-availability-sets-portal/verify-load-balancer.png" alt-text="Otestujte Nástroj pro vyrovnávání zatížení pomocí webového prohlížeče." border="true":::

6. V prohlížeči vyberte aktualizovat, abyste viděli provoz vyvážený k ostatním virtuálním počítačům ve fondu back-endu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte nástroj pro vyrovnávání zatížení a podpůrné prostředky pomocí následujících kroků:

1. Do vyhledávacího pole v horní části portálu zadejte **skupinu prostředků**.

2. Ve výsledcích hledání vyberte **skupiny prostředků** .

3. Vyberte **TutorLBmultiAVS-RG**.

4. Na stránce Přehled v **TutorLBmultiAVS-RG** vyberte **Odstranit skupinu prostředků**.

5. Do **pole zadejte název skupiny prostředků** zadejte **TutorLBmultiAVS-RG** .

6. Vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

* Vytvořili jste virtuální síť a hostitele Azure bastionu.
* Vytvořili Standard Load Balancer Azure.
* Vytvořili jste dvě skupiny dostupnosti se dvěma virtuálními počítači na jednu sadu.
* Nainstalovaná služba IIS a otestování nástroje pro vyrovnávání zatížení.

V dalším článku se dozvíte, jak vytvořit Azure Load Balancer mezi oblastmi:
> [!div class="nextstepaction"]
> [Vytvoření nástroje pro vyrovnávání zatížení mezi oblastmi](tutorial-cross-region-portal.md)

