---
title: "Vytvoření internetového nástroje pro vyrovnávání zatížení – Azure Portal | Dokumentace Microsoftu"
description: "Zjistěte, jak vytvořit internetový nástroj pro vyrovnávání zatížení v Resource Manageru pomocí webu Azure Portal"
services: load-balancer
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: aa9d26ca-3d8a-4a99-83b7-c410dd20b9d0
ms.service: load-balancer
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 35632cc93c9a0650b45220ba84b4983679de3d9c
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="creating-an-internet-facing-load-balancer-using-the-azure-portal"></a>Vytvoření internetového nástroje pro vyrovnávání zatížení pomocí webu Azure Portal

> [!div class="op_single_selector"]
> * [Azure Portal](../load-balancer/load-balancer-get-started-internet-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-internet-arm-cli.md)
> * [Šablona](../load-balancer/load-balancer-get-started-internet-arm-template.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

Tento článek se týká modelu nasazení Resource Manager.

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

Toto téma zahrnuje pořadí jednotlivých kroků, které je třeba provést za účelem vytvoření nástroje pro vyrovnávání zatížení, a podrobně vysvětluje, co se provádí k dosažení tohoto cíle.

## <a name="what-is-required-to-create-an-internet-facing-load-balancer"></a>Co je potřeba k vytvoření internetového nástroje pro vyrovnávání zatížení?

Pokud chcete nasadit nástroj pro vyrovnávání zatížení, je nutné vytvořit a nakonfigurovat následující objekty.

* Konfigurace front-endových IP adres – obsahuje veřejné IP adresy pro příchozí síťový provoz.
* Back-endový fond adres – obsahuje síťová rozhraní, pomocí kterých virtuální počítače přijímají síťový provoz z nástroje pro vyrovnávání zatížení.
* Pravidla vyrovnávání zatížení – obsahuje pravidla mapující veřejný port v nástroji pro vyrovnávání zatížení na port v back-endovém fondu adres.
* Pravidla příchozího překladu adres (NAT) – obsahuje pravidla mapující veřejný port v nástroji pro vyrovnávání zatížení na port konkrétního virtuálního počítače v back-endovém fondu adres.
* Testy – obsahuje testy stavu sloužící ke kontrole dostupnosti instancí virtuálních počítačů v back-endovém fondu adres.

Další informace o komponentách nástroje pro vyrovnávání zatížení s Azure Resource Managerem najdete v tématu [Podpora nástroje Load Balancer v Azure Resource Manageru](load-balancer-arm.md).

## <a name="set-up-a-load-balancer-in-azure-portal"></a>Nastavení nástroje pro vyrovnávání zatížení na webu Azure Portal

> [!IMPORTANT]
> Tento příklad předpokládá, že máte virtuální síť s názvem **myVNet**. Pokyny k jejímu vytvoření najdete v tématu [Vytvoření virtuální sítě](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Dále předpokládá, že v rámci **myVNet** je podsíť s názvem **LB-Subnet-BE** a že v rámci jedné skupiny dostupnosti s názvem **myAvailSet** v síti **myVNet** jsou dva virtuální počítače s názvy **web1** a **web2**. Pokyny k vytvoření virtuálních počítačů najdete na [tomto odkazu](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

1. V prohlížeči přejděte na web Azure Portal: [http://portal.azure.com](http://portal.azure.com) a přihlaste se pomocí svého účtu Azure.
2. V levém horním rohu obrazovky vyberte **Vytvořit prostředek** > **Sítě** > **Nástroj pro vyrovnávání zatížení**.
3. V okně **Vytvořit nástroj pro vyrovnávání zatížení** zadejte název svého nástroje pro vyrovnávání zatížení. Tady jsme ho nazvali **myLoadBalancer**.
4. V části **Typ** vyberte **Veřejný**.
5. V části **Veřejná IP adresa** vytvořte novou veřejnou IP adresu s názvem **myPublicIP**.
6. V části Skupina prostředků vyberte **myRG**. Následně vyberte odpovídající **Umístění** a klikněte na **OK**. Nástroj pro vyrovnávání zatížení se pak začne nasazovat. Úspěšné dokončení nasazení trvá několik minut.

    ![Aktualizace skupiny prostředků nástroje pro vyrovnávání zatížení](./media/load-balancer-get-started-internet-portal/1-load-balancer.png)

## <a name="create-a-back-end-address-pool"></a>Vytvoření fondu back-endových adres

1. Jakmile je váš nástroj pro vyrovnávání zatížení úspěšně nasazen, vyberte jej z vašich prostředků. V nastavení vyberte Back-endové fondy. Zadejte název svého back-endového fondu. Potom klikněte na tlačítko **Přidat**, které se objeví v horní části okna.
2. V okně **Přidat back-endový fond** klikněte na **Přidat virtuální počítač**.  V části **Skupina dostupnosti** vyberte možnost **Vybrat skupinu dostupnosti** a vyberte **myAvailSet**. Dále v části Virtuální počítače tohoto okna vyberte možnost **Vybrat virtuální počítače** a klikněte na **web1** a **web2**, což jsou virtuální počítače vytvořené pro vyrovnávání zatížení. Ujistěte se, že u obou je zobrazena modrá značka zaškrtnutí, jak je znázorněno na následujícím obrázku. Potom v tomto okně klikněte na **Vybrat**, v okně **Vybrat virtuální počítače** klikněte na OK a v okně **Přidat back-endový fond** klikněte na **OK**.

    ![Přidání do back-endového fondu adres – ](./media/load-balancer-get-started-internet-portal/3-load-balancer-backend-02.png)

3. Zkontrolujte, že rozevírací seznam oznámení obsahuje aktualizace týkající se kromě aktualizací síťových rozhraní i ukládání back-endového fondu nástroje pro vyrovnávání zatížení, a to pro oba virtuální počítače **web1** a **web2**.

## <a name="create-a-probe-lb-rule-and-nat-rules"></a>Vytvoření testu, pravidla nástroje pro vyrovnávání zatížení a pravidel překladu adres (NAT)

1. Vytvořte test stavu.

    V části Nastavení vašeho nástroje pro vyrovnávání zatížení vyberte Testy. Potom klikněte na **Přidat** v horní části okna.

    Test můžete nakonfigurovat dvěma způsoby: HTTP nebo TCP. Tento příklad ukazuje HTTP, ale TCP lze nakonfigurovat podobným způsobem.
    Aktualizujte potřebné informace. Jak už bylo zmíněno, **myLoadBalancer** vyrovnává zatížení provozu na portu 80. Vybraná cesta je HealthProbe.aspx, interval je 15 sekund a prahová hodnota špatného stavu je 2. Jakmile budete hotovi, kliknutím na **OK** vytvořte test.

    Pokud najedete myší na ikonu „i“, zobrazí se další informace o těchto jednotlivých konfiguracích a jak je změnit, aby vyhovovaly vašim požadavkům.

    ![Přidání testu](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

2. Vytvořte pravidlo nástroje pro vyrovnávání zatížení.

    Klikněte na Pravidla vyrovnávání zatížení v části Nastavení svého nástroje pro vyrovnávání zatížení. V novém okně klikněte na **Přidat**. Zadejte název pravidla. Tady je to HTTP. Vyberte front-endový port a back-endový port. Tady je pro oba vybrán port 80. Vyberte **LB-backend** jako Back-endový fond a dříve vytvořený **HealthProbe** jako Test. Ostatní konfigurace můžete nastavit podle svých požadavků. Potom kliknutím na OK uložte pravidlo vyrovnávání zatížení.

    ![Přidání pravidla vyrovnávání zatížení](./media/load-balancer-get-started-internet-portal/5-load-balancing-rules.png)

3. Vytvořte pravidla příchozího překladu adres (NAT).

    Klikněte na Pravidla příchozího překladu adres (NAT) v části Nastavení svého nástroje pro vyrovnávání zatížení. V novém okně klikněte na **Přidat**. Potom zadejte název pravidla příchozího překladu adres (NAT). Tady jsme ho nazvali **inboundNATrule1**. Cílem by měla být veřejná IP adresa, kterou jste vytvořili v předchozích krocích. V části Služba vyberte Vlastní a vyberte protokol, který chcete použít. Tady je vybrán protokol TCP. Zadejte Port (3441) a Cílový port (v tomto případě 3389). Následně kliknutím na OK toto pravidlo uložte.

    Po vytvoření prvního pravidla zopakujte tento krok pro druhé pravidlo příchozího překladu adres (NAT) s názvem inboundNATrule2 z Portu 3442 na Cílový port 3389.

    ![Přidání pravidla příchozího překladu adres (NAT)](./media/load-balancer-get-started-internet-portal/6-load-balancer-inbound-nat-rules.png)

## <a name="remove-a-load-balancer"></a>Odebrání nástroje pro vyrovnávání zatížení

Pro odstranění nástroje pro vyrovnávání zatížení vyberte nástroj pro vyrovnávání zatížení, který chcete odebrat. V horní části okna *Load Balancer* klikněte na **Odstranit**. Po zobrazení výzvy pak vyberte **Ano**.

## <a name="next-steps"></a>Další kroky

[Začínáme s konfigurací interního nástroje pro vyrovnávání zatížení](load-balancer-get-started-ilb-arm-cli.md)

[Konfigurace distribučního režimu nástroje pro vyrovnávání zatížení](load-balancer-distribution-mode.md)

[Konfigurace nastavení časového limitu nečinnosti protokolu TCP pro nástroj pro vyrovnávání zatížení](load-balancer-tcp-idle-timeout.md)
