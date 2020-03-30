---
title: Vyrovnávání zatížení ve více konfiguracích IP – portál Azure
titleSuffix: Azure Load Balancer
description: V tomto článku se dozvíte o vyrovnávání zatížení napříč primární a sekundární konfigurace IP pomocí portálu Azure.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: se0dec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 4bf74986462ecb2659505f8a1261b9b24aba3fee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74077005"
---
# <a name="load-balancing-on-multiple-ip-configurations-by-using-the-azure-portal"></a>Vyrovnávání zatížení na více konfiguracích IP pomocí portálu Azure

> [!div class="op_single_selector"]
> * [Portál](load-balancer-multiple-ip.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)
> * [Cli](load-balancer-multiple-ip-cli.md)


V tomto článku vám ukážeme, jak používat Azure Load Balancer s více IP adresami na sekundární řadič síťového rozhraní (NIC). Následující diagram znázorňuje náš scénář:

![Scénář nástroje pro vyrovnávání zatížení](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

V našem scénáři používáme následující konfiguraci:

- Dva virtuální počítače (VM), které jsou se systémem Windows.
- Každý virtuální virtuální virtuální ms má primární a sekundární nic.
- Každá sekundární nic má dvě konfigurace IP.
- Každý virtuální virtuální virtuální mše hostuje dva weby: contoso.com a fabrikam.com.
- Každý web je vázán na konfiguraci IP na sekundární nic.
- Azure Load Balancer se používá k vystavení dvou front-endIP adres, jednu pro každý web. Front-endové adresy se používají k distribuci provozu do příslušné konfigurace IP pro každou webovou stránku.
- Stejné číslo portu se používá pro front-endové IP adresy i ip adresy back-endového fondu.

## <a name="prerequisites"></a>Požadavky

Náš příklad scénáře předpokládá, že máte skupinu prostředků s názvem **contosofabrikam,** která je nakonfigurována následujícím způsobem:

- Skupina prostředků zahrnuje virtuální síť s názvem **myVNet**.
- Síť **myVNet** obsahuje dva virtuální mích s názvem **VM1** a **VM2**.
- VM1 a VM2 jsou ve stejné sadě dostupnosti s názvem **myAvailset**. 
- VM1 a VM2 každý mají primární nic s názvem **VM1NIC1** a **VM2NIC1**, příslušně. 
- VM1 a VM2 každý mají sekundární nic s názvem **VM1NIC2** a **VM2NIC2**, příslušně.

Další informace o vytváření virtuálních počítačů s více síťové karty najdete [v tématu vytvoření virtuálního počítače s více síťové karty pomocí Prostředí PowerShell](../virtual-machines/windows/multiple-nics.md).

## <a name="perform-load-balancing-on-multiple-ip-configurations"></a>Provádění vyrovnávání zatížení ve více konfiguracích IP

K dosažení scénáře popsaného v tomto článku proveďte následující kroky.

### <a name="step-1-configure-the-secondary-nics"></a>Krok 1: Konfigurace sekundárních nicotnic

Pro každý virtuální počítač ve virtuální síti přidejte konfiguraci IP pro sekundární síťovou síť:  

1. Přejděte na portál https://portal.azure.comAzure: . Přihlaste se pomocí svého účtu Azure.

2. V levém horním rohu obrazovky vyberte ikonu **Skupina prostředků.** Pak vyberte skupinu prostředků, kde jsou umístěny vaše virtuální počítače (například **contosofabrikam**). Podokno **Skupiny prostředků** zobrazuje všechny prostředky a síťové karty pro virtuální počítače.

3. Pro sekundární nic každého virtuálního počítače přidejte konfiguraci IP:

    1. Vyberte sekundární síťovou síťovou konfigurace, kterou chcete konfigurovat.
    
    2. Vyberte **konfigurace protokolu IP**. V dalším podokně v horní části vyberte **Přidat**.

    3. V části **Přidat konfigurace IP**přidejte druhou konfiguraci IP do nic: 

        1. Zadejte název sekundární konfigurace IP. (Například pro VM1 a VM2 pojmenujte konfiguraci IP **VM1NIC2-ipconfig2** a **VM2NIC2-ipconfig2.)**

        2. U **soukromé IP adresy**, **Nastavení přidělení** vyberte **Statický**.

        3. Vyberte **OK**.

Po dokončení druhé konfigurace PROTOKOLU IP pro sekundární ninici se zobrazí pod **nastavením konfigurace IP** pro danou nic.

### <a name="step-2-create-the-load-balancer"></a>Krok 2: Vytvoření nástroje pro vyrovnávání zatížení

Vytvořte nástroj pro vyrovnávání zatížení pro konfiguraci:

1. Přejděte na portál https://portal.azure.comAzure: . Přihlaste se pomocí svého účtu Azure.

2. V levém horním rohu obrazovky vyberte Vytvořit nástroje**pro vyrovnávání zatížení****sítě** >  **.** >  Dále vyberte **Vytvořit**.

3. V části **Vytvořit balancer**zadejte název pro vyrovnávání zatížení. V tomto scénáři používáme název **mylb**.

4. V části **Veřejná IP adresa**vytvořte novou veřejnou IP adresu s názvem **PublicIP1**.

5. V **části Skupina prostředků**vyberte existující skupinu prostředků pro virtuální počítače (například **contosofabrikam).** Vyberte umístění, do které chcete nasadit zařízení pro vyrovnávání zatížení, a pak vyberte **OK**.

Začal se nasazovat vykladač zatížení. Úspěšné dokončení nasazení může trvat několik minut. Po dokončení nasazení se systém vyrovnávání zatížení zobrazí jako prostředek ve skupině prostředků.

### <a name="step-3-configure-the-front-end-ip-pool"></a>Krok 3: Konfigurace fondu ip adres front-endu

Pro každý web (contoso.com a fabrikam.com) nakonfigurujte fond front-endIP v zařízení pro vyrovnávání zatížení:

1. Na portálu vyberte **Další služby**. Do pole filtru zadejte **veřejnou IP adresu** a vyberte **Veřejné IP adresy**. V dalším podokně v horní části vyberte **Přidat**.

2. Konfigurace dvou veřejných IP adres **(PublicIP1** a **PublicIP2**) pro oba weby (contoso.com a fabrikam.com):

   1. Zadejte název adresy FRONT-END IP.

   2. V **části Skupina prostředků**vyberte existující skupinu prostředků pro virtuální počítače (například **contosofabrikam**).

   3. V **části Umístění**vyberte stejné umístění jako virtuální hod.

   4. Vyberte **OK**.

      Po vytvoření veřejných IP adres jsou zobrazeny pod **veřejnými IP** adresami.

3. <a name="step3-3"></a>Na portálu vyberte **Další služby**. V poli filtru zadejte **balancer a** pak vyberte **Balancer zatížení**. 

4. Vyberte zařízení pro vyrovnávání zatížení **(mylb),** do kterého chcete přidat fond ip adres front-end.

5. V části **Nastavení**vyberte **front-endová konfigurace IP**. V dalším podokně v horní části vyberte **Přidat**.

6. Zadejte název adresy FRONT-END IP (například **contosofe** nebo **fabrikamfe).**

7. <a name="step3-7"></a>Vyberte **adresu IP**. V části **Zvolit veřejnou IP adresu**vyberte IP adresy pro váš front-end **(PublicIP1** nebo **PublicIP2**).

8. Vytvořte druhou front-endovou IP adresu opakováním <a href="#step3-3">kroku 3</a> až <a href="#step3-7">kroku 7</a> v této části.

Po konfiguraci front-endového fondu se ip adresy zobrazí pod **nastavením konfigurace front-endu front-end.** 
    
### <a name="step-4-configure-the-back-end-pool"></a>Krok 4: Konfigurace fondu back-end

Pro každý web (contoso.com a fabrikam.com) nakonfigurujte fond back-endových adres v zařízení pro vyrovnávání zatížení:
        
1. Na portálu vyberte **Další služby**. V poli filtru zadejte **balancer a** pak vyberte **Balancer zatížení**.

2. Vyberte balancer **(mylb),** do kterého chcete přidat back-endový fond.

3. V části **Nastavení**vyberte **Back-end Ové fondy**. Zadejte název back-endového fondu (například **contosopool** nebo **fabrikampool**). V dalším podokně v horní části vyberte **Přidat**. 

4. V **možnosti Přidruženo k**vyberte **položku Dostupnost .**

5. V **sadě dostupnosti**vyberte **možnost myAvailset**.

6. Přidejte konfigurace IP adres cílové sítě pro oba virtuální počítače: 

    ![Konfigurace back-endových fondů pro balancer na zatížení](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
    
    1. V **části Cílový virtuální počítač**vyberte virtuální počítač, který chcete přidat do back-endového fondu (například **VM1** nebo **VM2).**

    2. Pro **konfiguraci ip sítě**vyberte konfiguraci IP sekundární síťové konto pro virtuální počítač, který jste vybrali v předchozím kroku (například **VM1NIC2-ipconfig2** nebo **VM2NIC2-ipconfig2**).

7. Vyberte **OK**.

Po konfiguraci back-endového fondu se adresy zobrazí pod nastavením **back-endového fondu** vykladačů zatížení.

### <a name="step-5-configure-the-health-probe"></a>Krok 5: Konfigurace sondy stavu

Konfigurace sondy stavu pro vyrovnávání zatížení:

1. Na portálu vyberte **Další služby**. V poli filtru zadejte **balancer a** pak vyberte **Balancer zatížení**.

2. Vyberte systém vyrovnávání zatížení **(mylb),** do kterého chcete přidat sondu stavu.

3. V části **Nastavení**vyberte **sondu stavu**. V dalším podokně v horní části vyberte **Přidat**. 

4. Zadejte název sondy stavu (například **HTTP**). Vyberte **OK**.

### <a name="step-6-configure-load-balancing-rules"></a>Krok 6: Konfigurace pravidel vyrovnávání zatížení

Pro každý web (contoso.com a fabrikam.com) nakonfigurujte pravidla vyrovnávání zatížení:
    
1. <a name="step6-1"></a>V části **Nastavení**vyberte **Pravidla vyrovnávání zatížení**. V dalším podokně v horní části vyberte **Přidat**. 

2. Do **pole Název**zadejte název pravidla vyrovnávání zatížení (například **HTTPc** pro contoso.com nebo **HTTPf** pro fabrikam.com).

3. V **části Front-end IP adresa**vyberte dříve vytvořenou front-endovou ADRESU IP (například **contosofe** nebo **fabrikamfe**).

4. U **portového** a **back-endového portu**ponechte výchozí hodnotu **80**.

5. V **případě plovoucí IP adresy (přímé vrácení serveru)** vyberte možnost **Zakázáno**.

6. <a name="step6-6"></a>Vyberte **OK**.

7. Vytvořte druhé pravidlo pro vyrovnávání zatížení opakováním <a href="#step6-1">kroku 1</a> až <a href="#step6-6">kroku 6</a> v této části.

Po konfiguraci pravidel se zobrazí pod nastavením **pravidel vyrovnávání zatížení vyrovnávání zatížení.**

### <a name="step-7-configure-dns-records"></a>Krok 7: Konfigurace záznamů DNS

Jako poslední krok nakonfigurujte záznamy o prostředcích DNS tak, aby ukazovaly na příslušné front-endové IP adresy pro vykladač zatížení. Domény můžete hostovat ve službě Azure DNS. Další informace o používání Azure DNS s Balancer, najdete [v tématu použití Azure DNS s jinými službami Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Další kroky
- Další informace o tom, jak kombinovat služby vyrovnávání zatížení v Azure v [použití služeb vyrovnávání zatížení v Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Zjistěte, jak můžete použít různé typy protokolů ke správě a odstraňování problémů s vyrovnávání zatížení v [protokolech Azure Monitor pro Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).
