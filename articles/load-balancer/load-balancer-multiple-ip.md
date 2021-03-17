---
title: Vyrovnávání zatížení u více konfigurací IP adres – Azure Portal
titleSuffix: Azure Load Balancer
description: V tomto článku se dozvíte víc o vyrovnávání zatížení napříč primárními a sekundárními konfiguracemi IP adres pomocí Azure Portal.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: se0dec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 3d28946aad263af635a0139e68d424a77a1eab25
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2021
ms.locfileid: "103417820"
---
# <a name="load-balancing-on-multiple-ip-configurations-by-using-the-azure-portal"></a>Vyrovnávání zatížení u více konfigurací protokolu IP pomocí Azure Portal

> [!div class="op_single_selector"]
> * [Azure Portal](load-balancer-multiple-ip.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)
> * [Rozhraní příkazového řádku](load-balancer-multiple-ip-cli.md)

V tomto článku vám ukážeme, jak používat Azure Load Balancer s více IP adresami na sekundárním řadiči síťového rozhraní (NIC). Následující diagram znázorňuje náš scénář:

![Scénář nástroje pro vyrovnávání zatížení](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

V našem scénáři používáme následující konfiguraci:

- Dva virtuální počítače se systémem Windows.
- Každý virtuální počítač má primární a sekundární síťovou kartu.
- Každé sekundární síťové kartě má dvě konfigurace protokolu IP.
- Každý virtuální počítač je hostitelem dvou webů: contoso.com a fabrikam.com.
- Každý web je vázán na konfiguraci protokolu IP v sekundárním síťovém adaptéru.
- Azure Load Balancer slouží k vystavení dvou front-endové IP adresy, jednu pro každý web. Adresy front-endu slouží k distribuci provozu do příslušné konfigurace protokolu IP pro každý web.
- Stejné číslo portu se používá pro IP adresy front-endu i pro IP adresy fondu back-endu.

## <a name="prerequisites"></a>Požadavky

Příklad našeho scénáře předpokládá, že máte skupinu prostředků s názvem **contosofabrikam** , která je nakonfigurována následujícím způsobem:

- Skupina prostředků zahrnuje virtuální síť s názvem **myVNet**.
- **MyVNet** síť obsahuje dva virtuální počítače s názvem **VM1** a **VM2**.
- VM1 a VM2 jsou ve stejné skupině dostupnosti s názvem **myAvailset**. 
- VM1 a VM2 mají primární síťovou kartu s názvem **VM1NIC1** a **VM2NIC1**, v uvedeném pořadí. 
- VM1 a VM2 mají sekundární síťové rozhraní s názvem **VM1NIC2** a **VM2NIC2**, v uvedeném pořadí.

Další informace o vytváření virtuálních počítačů s více síťovými kartami najdete v tématu [Vytvoření virtuálního počítače s několika síťovými kartami pomocí PowerShellu](../virtual-machines/windows/multiple-nics.md).

## <a name="perform-load-balancing-on-multiple-ip-configurations"></a>Provádění vyrovnávání zatížení u více konfigurací IP adres

Provedením následujících kroků dosáhnete scénáře popsaného v tomto článku.

### <a name="step-1-configure-the-secondary-nics"></a>Krok 1: Konfigurace sekundárních síťových karet

Pro každý virtuální počítač ve vaší virtuální síti přidejte konfiguraci protokolu IP pro sekundární síťovou kartu:  

1. Přejděte k Azure Portal: https://portal.azure.com . Přihlaste se pomocí svého účtu Azure.

2. V levém horním rohu obrazovky vyberte ikonu **skupiny prostředků** . Pak vyberte skupinu prostředků, ve které se vaše virtuální počítače nacházejí (například **contosofabrikam**). V podokně **skupiny prostředků** se zobrazí všechny prostředky a síťové karty pro virtuální počítače.

3. Pro sekundární síťovou kartu každého virtuálního počítače přidejte konfiguraci IP adresy:

    1. Vyberte sekundární síťovou kartu, kterou chcete nakonfigurovat.
    
    2. Vyberte **Konfigurace protokolu IP**. V dalším podokně blízko horní části vyberte **Přidat**.

    3. V části **Přidat konfigurace protokolu IP** přidejte do síťové karty druhou konfiguraci IP adresy: 

        1. Zadejte název sekundární konfigurace IP adresy. (Například pro VM1 a VM2 název konfigurace protokolu IP **VM1NIC2-ipconfig2** a **VM2NIC2-ipconfig2**, v uvedeném pořadí)

        2. V poli **soukromá IP adresa**, nastavení **přidělení** vyberte **static**.

        3. Vyberte **OK**.

Po dokončení druhé konfigurace protokolu IP pro sekundární síťovou kartu se zobrazí v nastavení **Konfigurace protokolu IP** pro danou síťovou kartu.

### <a name="step-2-create-the-load-balancer"></a>Krok 2: Vytvoření nástroje pro vyrovnávání zatížení

Vytvořte Nástroj pro vyrovnávání zatížení pro konfiguraci:

1. Přejděte k Azure Portal: https://portal.azure.com . Přihlaste se pomocí svého účtu Azure.

2. V levém horním rohu obrazovky vyberte **vytvořit prostředek**  >  **síť**  >  **Load Balancer**. V dalším kroku vyberte **vytvořit**.

3. V části **vytvořit nástroj pro vyrovnávání zatížení** zadejte název nástroje pro vyrovnávání zatížení. V tomto scénáři používáme název **mylb**.

4. V části **Veřejná IP adresa** vytvořte novou veřejnou IP adresu s názvem **PublicIP1**.

5. V části **Skupina prostředků** vyberte existující skupinu prostředků pro virtuální počítače (například **contosofabrikam**). Vyberte umístění, do kterého chcete nasadit nástroj pro vyrovnávání zatížení, a pak vyberte **OK**.

Nástroj pro vyrovnávání zatížení začne nasazovat. Úspěšné dokončení nasazení může trvat několik minut. Po dokončení nasazení se nástroj pro vyrovnávání zatížení zobrazí jako prostředek ve vaší skupině prostředků.

### <a name="step-3-configure-the-front-end-ip-pool"></a>Krok 3: Konfigurace fondu front-end IP adres

Pro každý web (contoso.com a fabrikam.com) nakonfigurujte fond front-end IP adres v nástroji pro vyrovnávání zatížení:

1. Na portálu vyberte **Další služby**. Do pole Filtr zadejte **veřejnou IP adresu** a potom vyberte **veřejné IP adresy**. V dalším podokně blízko horní části vyberte **Přidat**.

2. Nakonfigurujte dvě veřejné IP adresy (**PublicIP1** a **PublicIP2**) pro obě websites (contoso.com a Fabrikam.com):

   1. Zadejte název pro front-end IP adresu.

   2. V poli **Skupina prostředků** vyberte existující skupinu prostředků pro vaše virtuální počítače (například **contosofabrikam**).

   3. V poli **umístění** vyberte stejné umístění jako virtuální počítače.

   4. Vyberte **OK**.

      Jakmile se veřejné IP adresy vytvoří, zobrazí se v části **veřejné IP** adresy.

3. <a name="step3-3"></a>Na portálu vyberte **Další služby**. Do pole Filtr zadejte **Nástroj pro vyrovnávání zatížení** a pak vyberte **Load Balancer**. 

4. Vyberte nástroj pro vyrovnávání zatížení (**mylb**), do kterého chcete přidat fond front-end IP adres.

5. V části **Nastavení** vyberte **Konfigurace IP adresy front-endu**. V dalším podokně blízko horní části vyberte **Přidat**.

6. Zadejte název pro front-end IP adresu (například **contosofe** nebo **fabrikamfe**).

7. <a name="step3-7"></a>Vyberte **IP adresa**. V části **zvolit veřejnou IP adresu** vyberte IP adresy pro front-end (**PublicIP1** nebo **PublicIP2**).

8. Vytvořte druhou front-end IP adresu opakováním <a href="#step3-3">kroku 3</a> až <a href="#step3-7">7</a> v této části.

Po nakonfigurování front-end fondu se IP adresy zobrazí v části nastavení **Konfigurace protokolu IP front-endu** pro vyrovnávání zatížení. 
    
### <a name="step-4-configure-the-back-end-pool"></a>Krok 4: Konfigurace fondu back-end

Pro každý web (contoso.com a fabrikam.com) nakonfigurujte fond back-end adres na vašem nástroji pro vyrovnávání zatížení:
        
1. Na portálu vyberte **Další služby**. Do pole Filtr zadejte **Nástroj pro vyrovnávání zatížení** a pak vyberte **Load Balancer**.

2. Vyberte nástroj pro vyrovnávání zatížení (**mylb**), do kterého chcete přidat fond back-endu.

3. V části **Nastavení** vyberte **back-endové fondy**. Zadejte název pro svůj fond back-end (například **contosopool** nebo **fabrikampool**). V dalším podokně blízko horní části vyberte **Přidat**. 

4. V případě **přidruženého k** vyberte **skupinu dostupnosti**.

5. Jako **skupinu dostupnosti** vyberte **myAvailset**.

6. Přidejte cílové konfigurace sítě IP pro oba virtuální počítače: 

    ![Konfigurace fondů back-endu pro nástroj pro vyrovnávání zatížení](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
    
    1. U **cílového virtuálního počítače** vyberte virtuální počítač, který chcete přidat do fondu back-end (například **VM1** nebo **VM2**).

    2. V poli **Konfigurace sítě IP** vyberte konfiguraci IP sekundární síťové karty pro virtuální počítač, který jste vybrali v předchozím kroku (například **VM1NIC2-ipconfig2** nebo **VM2NIC2-ipconfig2**).

7. Vyberte **OK**.

Po nakonfigurování fondu back-end se adresy zobrazí v části nastavení **fondu back-endu** nástroje pro vyrovnávání zatížení.

### <a name="step-5-configure-the-health-probe"></a>Krok 5: Konfigurace sondy stavu

Konfigurace sondy stavu pro váš nástroj pro vyrovnávání zatížení:

1. Na portálu vyberte **Další služby**. Do pole Filtr zadejte **Nástroj pro vyrovnávání zatížení** a pak vyberte **Load Balancer**.

2. Vyberte nástroj pro vyrovnávání zatížení (**mylb**), do kterého chcete přidat sondu stavu.

3. V části **Nastavení** vyberte **sonda stavu**. V dalším podokně blízko horní části vyberte **Přidat**. 

4. Zadejte název sondy stavu (například **http**). Vyberte **OK**.

### <a name="step-6-configure-load-balancing-rules"></a>Krok 6: Konfigurace pravidel vyrovnávání zatížení

Pro každý web (contoso.com a fabrikam.com) nakonfigurujte pravidla vyrovnávání zatížení:
    
1. <a name="step6-1"></a>V části **Nastavení** vyberte **pravidla vyrovnávání zatížení**. V dalším podokně blízko horní části vyberte **Přidat**. 

2. Do pole **název** zadejte název pravidla vyrovnávání zatížení (například **HTTPc** pro contoso.com nebo **HTTPf** pro Fabrikam.com).

3. V poli **IP adresa front-endu** vyberte front-end IP adresu, kterou jste dříve vytvořili (například **contosofe** nebo **fabrikamfe**).

4. Pro **port** a **back-end port** ponechte výchozí hodnotu **80**.

5. V případě **plovoucí IP adresy (přímé vrácení serveru)** vyberte možnost **zakázáno**.

6. <a name="step6-6"></a>Vyberte **OK**.

7. Vytvořte druhé pravidlo nástroje pro vyrovnávání zatížení opakováním <a href="#step6-1">kroků 1</a> až <a href="#step6-6">6</a> v této části.

Po nakonfigurování pravidel se zobrazí pod nastavením **pravidel vyrovnávání zatížení nástroje pro vyrovnávání** zatížení.

### <a name="step-7-configure-dns-records"></a>Krok 7: Konfigurace záznamů DNS

V posledním kroku nakonfigurujte záznamy prostředků DNS tak, aby odkazovaly na příslušné front-endové IP adresy vašeho nástroje pro vyrovnávání zatížení. Domény můžete hostovat v Azure DNS. Další informace o použití Azure DNS s Load Balancer najdete v tématu [použití Azure DNS s dalšími službami Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o tom, jak kombinovat služby Vyrovnávání zatížení v Azure [pomocí služeb vyrovnávání zatížení v Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Přečtěte si, jak můžete pomocí různých typů protokolů spravovat a řešit potíže nástroje pro vyrovnávání zatížení v [Azure monitor protokoly pro Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).
