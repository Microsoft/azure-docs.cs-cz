---
title: Rozložení zátěže pro více konfigurací IP v Azure | Dokumentace Microsoftu
description: Vyrovnávání zatížení napříč primární a sekundární konfigurací IP.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 0f092c471a7908eabe481adc8c722993818840b8
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219507"
---
# <a name="load-balancing-on-multiple-ip-configurations-by-using-the-azure-portal"></a>Vyrovnávání zatížení na více konfigurací protokolu IP pomocí webu Azure portal

> [!div class="op_single_selector"]
> * [Azure Portal](load-balancer-multiple-ip.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)
> * [Rozhraní příkazového řádku](load-balancer-multiple-ip-cli.md)


V tomto článku jsme teď ukazují, jak používat Azure Load Balancer s několika IP adresami na sekundární síťový adaptér rozhraní (NIC). Následující diagram znázorňuje scénáři:

![Scénář nástroje pro vyrovnávání zatížení](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

V tomto scénáři používáme následující konfiguraci:

- Dva virtuální počítače (VM), na kterých běží Windows.
- Každý virtuální počítač má primární a sekundární síťové rozhraní
- Každé sekundární síťové rozhraní se dvěma konfiguracemi IP.
- Každý virtuální počítač hostuje dva weby: contoso.com a fabrikam.com.
- Každý z těchto webů je vázán na konfiguraci IP na sekundární síťové rozhraní
- Nástroj Azure Load Balancer se používá k vystavení dva front-endové IP adresy, jeden pro každý web. Adresy front-endu se používají k distribuci provozu do příslušné konfigurace protokolu IP pro každý z těchto webů.
- Pro front-endových IP adres a back endového fondu IP adres se používá stejné číslo portu.

## <a name="prerequisites"></a>Požadavky

Náš příklad scénáře se předpokládá, že máte skupinu prostředků s názvem **contosofabrikam** , který je konfigurován takto:

- Skupina prostředků obsahuje virtuální síť s názvem **myVNet**.
- **MyVNet** síť obsahuje dva virtuální počítače s názvem **VM1** a **VM2**.
- VM1 a VM2 jsou ve stejné skupině dostupnosti s názvem **myAvailset**. 
- VM1 a VM2 mají primární síťové rozhraní s názvem **VM1NIC1** a **VM2NIC1**v uvedeném pořadí. 
- VM1 a VM2 mít sekundární síťové rozhraní s názvem **VM1NIC2** a **VM2NIC2**v uvedeném pořadí.

Další informace o vytváření virtuálních počítačů s několika síťovými kartami, naleznete v tématu [vytvoření virtuálního počítače s několika síťovými kartami s použitím prostředí PowerShell](../virtual-machines/windows/multiple-nics.md).

## <a name="perform-load-balancing-on-multiple-ip-configurations"></a>Provést pro více konfigurací IP pro vyrovnávání zatížení

Proveďte následující kroky k dosažení scénář popsaný v tomto článku.

### <a name="step-1-configure-the-secondary-nics"></a>Krok 1: Konfigurace sekundární síťová rozhraní

Pro každý virtuální počítač ve virtuální síti přidáte konfiguraci IP adresy pro sekundární síťové rozhraní:  

1. Přejděte na web Azure Portal: http://portal.azure.com. Přihlaste se pomocí svého účtu Azure.

2. V levém horním rohu obrazovky vyberte **skupiny prostředků** ikonu. Pak vyberte skupinu prostředků, ve kterém jsou umístěné vaše virtuální počítače (například **contosofabrikam**). **Skupiny prostředků** podokně se zobrazí všechny prostředky a síťových karet pro virtuální počítače.

3. Pro sekundární síťové rozhraní každého virtuálního počítače přidáte konfiguraci IP adresy:

    1. Vyberte sekundární síťové rozhraní, který chcete konfigurovat.
    
    2. Vyberte **konfigurací protokolu IP**. V dalším podokně, v horní části vyberte **přidat**.

    3. V části **přidat IP adresu konfigurace**, přidejte druhá konfigurace IP adresy k síťovému rozhraní: 

        1. Zadejte název sekundární konfigurace IP adresy. (Například název konfigurace protokolu IP pro VM1 a VM2 **VM1NIC2 ipconfig2** a **VM2NIC2 ipconfig2**v uvedeném pořadí.)

        2. Pro **privátní IP adresa**, **přidělení** vyberte **statické**.

        3. Vyberte **OK**.

Po druhá konfigurace IP adresy pro sekundární síťové rozhraní se dokončí, zobrazí se pod **konfigurací protokolu IP** nastavení pro daný síťový adaptér

### <a name="step-2-create-the-load-balancer"></a>Krok 2: Vytvoření nástroje pro vyrovnávání zatížení

Vytvořte nástroj pro vyrovnávání zatížení pro konfiguraci:

1. Přejděte na web Azure Portal: http://portal.azure.com. Přihlaste se pomocí svého účtu Azure.

2. V levém horním rohu obrazovky vyberte **vytvořit prostředek** > **sítě** > **nástroje pro vyrovnávání zatížení**. V dalším kroku vyberte **vytvořit**.

3. V části **vytvořit nástroj pro vyrovnávání zatížení**, zadejte název vašeho nástroje pro vyrovnávání zatížení. V tomto případě používáme název **mylb**.

4. V části **veřejnou IP adresu**, vytvořte novou veřejnou IP adresu volá **PublicIP1**.

5. V části **skupiny prostředků**, vyberte existující skupinu prostředků pro virtuální počítače (například **contosofabrikam**). Vyberte umístění pro nasazení na nástroj pro vyrovnávání zatížení a potom vyberte **OK**.

Nástroje pro vyrovnávání zatížení začne nasazovat. Nasazení může trvat několik minut na úspěšné dokončení. Po dokončení nasazení se zobrazí se nástroj pro vyrovnávání zatížení jako prostředek ve vaší skupině prostředků.

### <a name="step-3-configure-the-front-end-ip-pool"></a>Krok 3: Konfigurace front-endový fond IP adres

Pro každý web (contoso.com a fabrikam.com) konfigurace front-endový fond IP adres na nástroj pro vyrovnávání zatížení:

1. Na portálu vyberte **další služby**. V dialogovém okně Filtr zadejte **veřejnou IP adresu** a pak vyberte **veřejné IP adresy**. V dalším podokně, v horní části vyberte **přidat**.

2. Nakonfigurujte dvě veřejné IP adresy (**PublicIP1** a **PublicIP2**) pro oba weby (contoso.com a fabrikam.com):

    1. Zadejte název pro vaši front-endovou IP adresu.

    2. Pro **skupiny prostředků**, vyberte existující skupinu prostředků pro virtuální počítače (například **contosofabrikam**).

    3. Pro **umístění**, vybrat stejné umístění jako virtuální počítače.

    4. Vyberte **OK**.

    Po vytvoření veřejné IP adresy, se zobrazí v části **veřejnou IP adresu** adresy.

3. <a name="step3-3"></a>Na portálu vyberte **další služby**. V dialogovém okně Filtr zadejte **nástroj pro vyrovnávání zatížení** a pak vyberte **Load Balancer**. 

4. Vyberte nástroje pro vyrovnávání zatížení (**mylb**), které chcete přidat front-endový fond IP adres pro.

5. V části **nastavení**vyberte **konfigurace protokolu IP front-endu**. V dalším podokně, v horní části vyberte **přidat**.

6. Zadejte název pro vaši front-endovou IP adresu (například **contosofe** nebo **fabrikamfe**).

7. <a name="step3-7"></a>Vyberte **IP adresu**. V části **zvolte veřejnou IP adresu**, vyberte IP adresy pro vaši front-endu (**PublicIP1** nebo **PublicIP2**).

8. Vytvořte druhou IP adresu front-endu opakováním <a href="#step3-3">kroku 3</a> prostřednictvím <a href="#step3-7">kroku 7</a> v této části.

Po dokončení konfigurace front-endový fond IP adres se zobrazí v části vašeho nástroje pro vyrovnávání zatížení **konfigurace protokolu IP front-endu** nastavení. 
    
### <a name="step-4-configure-the-back-end-pool"></a>Krok 4: Konfigurace back endového fondu

Pro každý web (contoso.com a fabrikam.com) nakonfigurujte fond back endových adres na nástroj pro vyrovnávání zatížení:
        
1. Na portálu vyberte **další služby**. V dialogovém okně Filtr zadejte **nástroj pro vyrovnávání zatížení** a pak vyberte **Load Balancer**.

2. Vyberte nástroje pro vyrovnávání zatížení (**mylb**), které chcete přidat back endového fondu do.

3. V části **nastavení**vyberte **back-endové fondy**. Zadejte název vaší back endového fondu (například **contosopool** nebo **fabrikampool**). V dalším podokně, v horní části vyberte **přidat**. 

4. Pro **přidružené k**vyberte **dostupnosti**.

5. Pro **dostupnosti**vyberte **myAvailset**.

6. Přidáte cílové konfigurace protokolu IP sítě pro oba virtuální počítače: 

    ![Nakonfigurujte fondy back-end pro nástroj pro vyrovnávání zatížení](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
    
    1. Pro **cílový virtuální počítač**, vyberte virtuální počítač, který chcete přidat do fondu back-end (například **VM1** nebo **VM2**).

    2. Pro **konfigurace protokolu IP sítě**, vyberte konfigurace IP adresy sekundární síťové karty pro virtuální počítač, který jste vybrali v předchozím kroku (například **VM1NIC2 ipconfig2** nebo **VM2NIC2 ipconfig2** ).

7. Vyberte **OK**.

Po dokončení konfigurace back endového fondu adres se zobrazí v části vašeho nástroje pro vyrovnávání zatížení **back-endový fond** nastavení.

### <a name="step-5-configure-the-health-probe"></a>Krok 5: Konfigurace sondy stavu

Konfigurace sondy stavu pro nástroj pro vyrovnávání zatížení:

1. Na portálu vyberte **další služby**. V dialogovém okně Filtr zadejte **nástroj pro vyrovnávání zatížení** a pak vyberte **Load Balancer**.

2. Vyberte nástroje pro vyrovnávání zatížení (**mylb**), které chcete přidat sondu stavu do.

3. V části **nastavení**vyberte **sondu stavu**. V dalším podokně, v horní části vyberte **přidat**. 

4. Zadejte název sondy stavu (například **HTTP**). Vyberte **OK**.

### <a name="step-6-configure-load-balancing-rules"></a>Krok 6: Konfigurace pravidel Vyrovnávání zatížení

Pro každý web (contoso.com a fabrikam.com) konfigurace pravidel Vyrovnávání zatížení:
    
1. <a name="step6-1"></a>V části **nastavení**vyberte **pravidla Vyrovnávání zatížení**. V dalším podokně, v horní části vyberte **přidat**. 

2. Pro **název**, zadejte název pravidla Vyrovnávání zatížení (například **HTTPc** pro doménu contoso.com, nebo **HTTPf** pro fabrikam.com).

3. Pro **front-endovou IP adresu**, vyberte front-endovou IP adresu, kterou jste vytvořili (například **contosofe** nebo **fabrikamfe**).

4. Pro **Port** a **back-endový port**, ponechte výchozí hodnotu **80**.

5. Pro **plovoucí IP (přímá odpověď ze serveru vrácené)** vyberte **zakázané**.

6. <a name="step6-6"></a>Vyberte **OK**.

7. Vytvoření druhého pravidla služby load balancer opakováním <a href="#step6-1">kroku 1</a> prostřednictvím <a href="#step6-6">kroku 6</a> v této části.

Po nakonfigurování pravidla jsou zobrazeny v rámci vašeho nástroje pro vyrovnávání zatížení **pravidla Vyrovnávání zatížení** nastavení.

### <a name="step-7-configure-dns-records"></a>Krok 7: Konfigurace záznamů DNS

V posledním kroku nakonfigurujte svoje záznamy prostředků DNS tak, aby odkazoval na příslušných front-endové IP adresy pro nástroj pro vyrovnávání zatížení. Můžete hostovat svoje domény v Azure DNS. Další informace o použití Azure DNS s nástrojem pro vyrovnávání zatížení najdete v tématu [pomocí Azure DNS s ostatními službami Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Další postup
- Další informace o tom, jak kombinací služeb Vyrovnávání zatížení v Azure v [pomocí služby Vyrovnávání zatížení v Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Zjistěte, jak můžete pomocí různých typů protokolů pro správu a řešení potíží s nástroj pro vyrovnávání zatížení v [Log analytics pro Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).
