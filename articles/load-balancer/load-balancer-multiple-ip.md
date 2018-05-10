---
title: Víc konfigurací IP adres v Azure pro vyrovnávání zatížení | Microsoft Docs
description: Vyrovnávání zatížení napříč primární a sekundární konfigurace protokolu IP.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: na
ms.assetid: 244907cd-b275-4494-aaf7-dcfc4d93edfe
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 3bf2f793baec4a0fdacadd7fb6d3d6784aa17f29
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="load-balancing-on-multiple-ip-configurations-by-using-the-azure-portal"></a>Vyrovnávání zatížení na víc konfigurací IP adres pomocí portálu Azure

> [!div class="op_single_selector"]
> * [Azure Portal](load-balancer-multiple-ip.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)
> * [Rozhraní příkazového řádku](load-balancer-multiple-ip-cli.md)


V tomto článku vytvoříme ukazují, jak používat nástroj pro vyrovnávání zatížení Azure s více IP adres na sekundární síťový adaptér rozhraní (NIC). Následující diagram znázorňuje náš scénář:

![Scénář nástroje pro vyrovnávání zatížení](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

V našem scénáři používáme následující konfiguraci:

- Dva virtuální počítače (VM) se systémem Windows.
- Každý virtuální počítač má primární a sekundární síťový adaptér.
- Každé sekundární síťové KARTĚ má dvě konfigurace protokolu IP.
- Každý virtuální počítač hostuje dva weby: contoso.com a fabrikam.com.
- Každý web konfiguraci IP adres je vázána na sekundární síťový adaptér.
- Azure nástroj pro vyrovnávání zatížení se používá ke zveřejnění dvě front-end IP adresy, jednu pro každý web. Front-endu adresy se používají k distribuci provoz do příslušných konfiguraci protokolu IP pro každý web.
- Pro front-end IP adresy a fond back-end IP adresy se používá stejné číslo portu.

## <a name="prerequisites"></a>Požadavky

Náš scénář příklad předpokládá, že máte skupinu prostředků s názvem **contosofabrikam** nakonfigurovaný následujícím způsobem:

- Skupina prostředků obsahuje virtuální síť s názvem **myVNet**.
- **MyVNet** síť obsahuje dva virtuální počítače s názvem **VM1** a **virtuálního počítače 2**.
- VM1 a virtuálního počítače 2 jsou ve stejné skupině s názvem dostupnosti **myAvailset**. 
- VM1 a virtuálního počítače 2 mají primární síťový adaptér s názvem **VM1NIC1** a **VM2NIC1**, v uvedeném pořadí. 
- VM1 a virtuálního počítače 2 mají sekundární síťový adaptér s názvem **VM1NIC2** a **VM2NIC2**, v uvedeném pořadí.

Další informace o vytváření virtuálních počítačů s více síťovými kartami najdete v tématu [vytvoření virtuálního počítače s více síťovými kartami pomocí prostředí PowerShell](../virtual-machines/windows/multiple-nics.md).

## <a name="perform-load-balancing-on-multiple-ip-configurations"></a>Provádění na víc konfigurací IP adres služby Vyrovnávání zatížení

Proveďte následující kroky k dosažení scénáři uvedeném v tomto článku.

### <a name="step-1-configure-the-secondary-nics"></a>Krok 1: Konfigurace sekundární síťové karty

Pro každý virtuální počítač ve virtuální síti přidejte konfiguraci protokolu IP pro sekundární síťový adaptér:  

1. Přejděte do portálu Azure: http://portal.azure.com. Přihlaste se pomocí účtu Azure.

2. V levém horním rohu obrazovky, vyberte **skupiny prostředků** ikonu. Pak vyberte skupinu prostředků, kde se nachází virtuální počítače (například **contosofabrikam**). **Skupiny prostředků** podokně se zobrazí všechny prostředky a síťové adaptéry pro virtuální počítače.

3. Pro sekundární síťový adaptér každého virtuálního počítače přidejte konfiguraci protokolu IP:

    1. Vyberte sekundární síťové adaptéry, které chcete nakonfigurovat.
    
    2. Vyberte **konfigurace protokolu IP**. V podokně Další horní části, vyberte **přidat**.

    3. V části **přidat IP konfigurace**, přidejte druhý konfigurace protokolu IP na síťový adaptér: 

        1. Zadejte název sekundární konfiguraci IP adresy. (Například pro VM1 a virtuálního počítače 2, název konfigurace IP **VM1NIC2 ipconfig2** a **VM2NIC2 ipconfig2**, v uvedeném pořadí.)

        2. Pro **privátní IP adresa**, **přidělení** nastavení, vyberte **statické**.

        3. Vyberte **OK**.

Po druhé konfiguraci protokolu IP pro sekundární síťový adaptér je dokončení se zobrazí v části **konfigurace protokolu IP** nastavení pro daný síťový adaptér.

### <a name="step-2-create-the-load-balancer"></a>Krok 2: Vytvoření nástroje pro vyrovnávání zatížení

Vytvořte nástroj pro vyrovnávání zatížení pro konfiguraci:

1. Přejděte do portálu Azure: http://portal.azure.com. Přihlaste se pomocí účtu Azure.

2. V levém horním rohu obrazovky vyberte **vytvořit prostředek** > **sítě** > **nástroj pro vyrovnávání zatížení**. Potom vyberte **vytvořit**.

3. V části **vytvořit nástroj pro vyrovnávání zatížení**, zadejte název pro nástroj pro vyrovnávání zatížení. V tomto případě používáme název **mylb**.

4. V části **veřejnou IP adresu**, vytvořit novou veřejnou IP adresu názvem **PublicIP1**.

5. V části **skupiny prostředků**, vyberte existující skupinu prostředků pro virtuální počítače (například **contosofabrikam**). Vyberte umístění pro nasazení na nástroj pro vyrovnávání zatížení a potom vyberte **OK**.

Nástroje pro vyrovnávání zatížení spustí nasazení. Nasazení může trvat několik minut úspěšně dokončit. Po dokončení nasazení nástroje pro vyrovnávání zatížení se zobrazí jako prostředek ve vaší skupině prostředků.

### <a name="step-3-configure-the-front-end-ip-pool"></a>Krok 3: Konfigurace front-end fond IP adres

Pro každý web (contoso.com a fabrikam.com) nakonfigurujte front-endu fond IP adres na nástroj pro vyrovnávání zatížení:

1. Na portálu, vyberte **další služby**. Do pole filtru zadejte **veřejnou IP adresu** a pak vyberte **veřejné IP adresy**. V podokně Další horní části, vyberte **přidat**.

2. Nakonfigurovat dvě veřejné IP adresy (**PublicIP1** a **PublicIP2**) pro oba weby (contoso.com a fabrikam.com):

    1. Zadejte název pro vaše front-end IP adresu.

    2. Pro **skupiny prostředků**, vyberte existující skupinu prostředků pro virtuální počítače (například **contosofabrikam**).

    3. Pro **umístění**, vyberte stejné umístění jako virtuální počítače.

    4. Vyberte **OK**.

    Po vytvoření veřejné IP adresy, se zobrazí v části **veřejnou IP adresu** adresy.

3. <a name="step3-3"></a>Na portálu, vyberte **další služby**. Do pole filtru zadejte **nástroj pro vyrovnávání zatížení** a pak vyberte **Vyrovnávání zatížení**. 

4. Vyberte pro vyrovnávání zatížení (**mylb**), které chcete přidat front-end fondu IP.

5. V části **nastavení**, vyberte **konfigurace IP front-endu**. V podokně Další horní části, vyberte **přidat**.

6. Zadejte název pro vaše front-end IP adresu (například **contosofe** nebo **fabrikamfe**).

7. <a name="step3-7"></a>Vyberte **IP adresu**. V části **zvolte veřejnou IP adresu**, vyberte IP adresy pro vaší front-endu (**PublicIP1** nebo **PublicIP2**).

8. Vytvoření druhého front-end IP adresy opakováním <a href="#step3-3">krok 3</a> prostřednictvím <a href="#step3-7">krok 7</a> v této části.

Po dokončení konfigurace fondu front-end IP adresy se zobrazí v části nástroj pro vyrovnávání zatížení **konfigurace IP front-endu** nastavení. 
    
### <a name="step-4-configure-the-back-end-pool"></a>Krok 4: Konfigurace fondu back-end

Pro každý web (contoso.com a fabrikam.com) nakonfigurujte fond back-end adres na nástroj pro vyrovnávání zatížení:
        
1. Na portálu, vyberte **další služby**. Do pole filtru zadejte **nástroj pro vyrovnávání zatížení** a pak vyberte **Vyrovnávání zatížení**.

2. Vyberte pro vyrovnávání zatížení (**mylb**), které chcete přidat fond back-end, který má.

3. V části **nastavení**, vyberte **back-endové fondy**. Zadejte název back-end fondu (například **contosopool** nebo **fabrikampool**). V podokně Další horní části, vyberte **přidat**. 

4. Pro **přidružené k**, vyberte **sadu dostupnosti**.

5. Pro **sadu dostupnosti**, vyberte **myAvailset**.

6. Přidáte konfiguraci cílové sítě IP pro oba virtuální počítače: 

    ![Konfigurovat fondy back-end pro vyrovnávání zatížení](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
    
    1. Pro **cílový virtuální počítač**, vyberte virtuální počítač, který chcete přidat do fondu back-end (například **VM1** nebo **virtuálního počítače 2**).

    2. Pro **konfiguraci IP adresy sítě**, vyberte konfiguraci IP na sekundární síťový adaptér pro virtuální počítač, který jste vybrali v předchozím kroku (například **VM1NIC2 ipconfig2** nebo **VM2NIC2 ipconfig2** ).

7. Vyberte **OK**.

Po dokončení konfigurace fondu back-end adresy se zobrazí v části nástroj pro vyrovnávání zatížení **fond back-end** nastavení.

### <a name="step-5-configure-the-health-probe"></a>Krok 5: Konfigurace test stavu

Nakonfigurujte test stavu pro nástroj pro vyrovnávání zatížení:

1. Na portálu, vyberte **další služby**. Do pole filtru zadejte **nástroj pro vyrovnávání zatížení** a pak vyberte **Vyrovnávání zatížení**.

2. Vyberte pro vyrovnávání zatížení (**mylb**), které chcete přidat test stavu na.

3. V části **nastavení**, vyberte **test stavu**. V podokně Další horní části, vyberte **přidat**. 

4. Zadejte název pro test stavu (například **HTTP**). Vyberte **OK**.

### <a name="step-6-configure-load-balancing-rules"></a>Krok 6: Konfigurace pravidel Vyrovnávání zatížení

Pro každý web (contoso.com a fabrikam.com) nakonfigurujte pravidla Vyrovnávání zatížení:
    
1. <a name="step6-1"></a>V části **nastavení**, vyberte **test stavu**. V podokně Další horní části, vyberte **přidat**. 

2. Pro **název**, zadejte název pro pravidlo Vyrovnávání zatížení (například **HTTPc** pro doménu contoso.com, nebo **HTTPf** pro fabrikam.com).

3. Pro **adresu IP front-endu**, vyberte front-end IP adresu, kterou jste vytvořili (například **contosofe** nebo **fabrikamfe**).

4. Pro **Port** a **back-endový port**, ponechte výchozí hodnotu **80**.

5. Pro **plovoucí IP adresa (přímá odpověď ze serveru)**, vyberte **povoleno**.

6. <a name="step6-6"></a>Vyberte **OK**.

7. Vytvořte druhé pravidlo Vyrovnávání zatížení opakováním <a href="#step6-1">krok 1</a> prostřednictvím <a href="#step6-6">krok 6</a> v této části.

Po nakonfigurování pravidla jsou zobrazeny v části nástroj pro vyrovnávání zatížení **pravidla Vyrovnávání zatížení** nastavení.

### <a name="step-7-configure-dns-records"></a>Krok 7: Nakonfigurovat záznamy DNS

Jako poslední krok nakonfigurujte vaše záznamy prostředků DNS tak, aby odkazoval na příslušné front-end IP adresy pro nástroj pro vyrovnávání zatížení. Je možné hostovat vaše domény v Azure DNS. Další informace o používání s nástrojem pro vyrovnávání zatížení Azure DNS najdete v tématu [pomocí Azure DNS s jinými službami Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Další postup
- Další informace o tom, jak kombinací v Azure v rámci služby Vyrovnávání zatížení [pomocí služby Vyrovnávání zatížení v Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Zjistěte, jak můžete použít různé typy protokolů pro správu a řešení potíží pro vyrovnávání zatížení v [protokolu pro vyrovnávání zatížení Azure analytics](../load-balancer/load-balancer-monitor-log.md).
