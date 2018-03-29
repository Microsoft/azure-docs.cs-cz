---
title: Nasazení internetového Vyrovnávání zatížení s IPv6 - šablony Azure | Microsoft Docs
description: Postup nasazení podporu IPv6 pro vyrovnávání zatížení Azure a virtuálních počítačů s vyrovnáváním zatížení.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: protokol IPv6, nástroje pro vyrovnávání zatížení azure, duálním zásobníkem, veřejnou IP adresu, nativní protokol ipv6, mobilní, iot
ms.assetid: 2998e943-13fc-4ea9-a68c-875e53a08db3
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 431b43979ac364d943c58c40b4199b7f30f9acf6
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>Nasazení řešení ve Vyrovnávání zatížení internetového s IPv6 pomocí šablony

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Azure CLI](load-balancer-ipv6-internet-cli.md)
> * [Šablona](load-balancer-ipv6-internet-template.md)



Azure Load Balancer je nástroj pro vyrovnávání zatížení úrovně 4 (TCP, UDP). Nástroj pro vyrovnávání zatížení poskytuje vysokou dostupnost díky distribuci příchozích přenosů mezi instance služeb, které jsou v pořádku, v cloudových službách nebo virtuálních počítačích v sadě nástroje pro vyrovnávání zatížení. Azure Load Balancer můžete také tyto služby prezentovat na více portech, více IP adresách nebo obojím.

## <a name="example-deployment-scenario"></a>Příklad scénáře nasazení

Následující diagram znázorňuje řešení vyrovnávání zatížení nasazuje pomocí šablony příklad popsané v tomto článku.

![Scénář nástroje pro vyrovnávání zatížení](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

V tomto scénáři vytvoříte následující prostředky Azure:

* rozhraní virtuální sítě pro každý virtuální počítač s oba protokoly IPv4 a IPv6 adresy přiřazené
* Vyrovnávání zatížení straně Internetu s IPv4 a IPv6 veřejnou IP adresu
* dvě pravidla vyrovnávání mapovat veřejné virtuální privátní koncové body zatížení
* Skupiny dostupnosti, která obsahuje dva virtuální počítače
* dva virtuální počítače (VM)

## <a name="deploying-the-template-using-the-azure-portal"></a>Nasazení šablony pomocí portálu Azure

Tento článek odkazuje na šablonu, která je publikována v [šablon Azure rychlý Start](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/) galerie. Můžete stáhnout šablonu z Galerie nebo spusťte nasazení v Azure přímo z galerie. Tento článek předpokládá, že jste si stáhli šablony do místního počítače.

1. Otevřete portál Azure a přihlaste se pomocí účtu, který má oprávnění k vytvoření virtuálních počítačů a síťových prostředků v rámci předplatného Azure. Navíc pokud používáte stávající prostředky, účet potřebuje oprávnění pro vytvoření skupiny prostředků a účet úložiště.
2. Klikněte na tlačítko "+ nový" z nabídky pak typu "Šablona" do vyhledávacího pole. Ve výsledcích hledání vyberte "Nasazení šablony".

    ![lb-ipv6-portal-step2](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. Vše v okně klikněte na tlačítko "Šablonu nasazení."

    ![lb-ipv6-portal-step3](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. Klikněte na tlačítko "Vytvořit".

    ![lb-ipv6-portal-step4](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. Klikněte na "Upravit šablonu." Odstraňte existující obsah a zkopírujte a vložte v celý obsah souboru šablony (chcete zahrnout spuštění a ukončení {}) a pak klikněte na "Uložit."

    > [!NOTE]
    > Pokud používáte Microsoft Internet Explorer, když vložíte můžete zobrazit dialogové okno s výzvou k povolení přístupu k schránky systému Windows. Klikněte na možnost "Povolit přístup."

    ![lb-ipv6-portal-step5](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. Klikněte na "Upravit parametry." V okně Parametry zadejte hodnoty, podle pokynů v části Parametry šablony a pak klikněte na "Uložit" zavřete okna parametry. V okně Vlastní nasazení vyberte předplatné, existující skupinu prostředků nebo vytvořit. Pokud vytváříte skupinu prostředků, pak vyberte umístění pro skupinu prostředků. Klikněte na tlačítko **právní podmínky**, pak klikněte na tlačítko **nákupu** pro právní podmínky. Azure zahájí nasazení prostředky. Trvá několik minut nasadit všechny prostředky.

    ![lb-ipv6-portal-step6](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    Další informace o těchto parametrů, najdete v článku [šablony parametry a proměnné](#template-parameters-and-variables) později v tomto článku.

7. Chcete-li zobrazit prostředky vytvořené šablony, klikněte na tlačítko Procházet, přejděte dolů na seznamu v tématu "Prostředku skupiny" a pak klikněte na něj.

    ![lb-ipv6-portal-step7](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. V okně skupiny prostředků klikněte na název skupiny prostředků, kterou jste zadali v kroku 6. Zobrazí seznam všech prostředků, které byly nasazeny. Pokud se všechny správně, je by mělo být uvedeno "Succeeded" v části "Posledního nasazení." Pokud ne, ujistěte se, že účet, který používáte, má oprávnění k vytvoření potřebné prostředky.

    ![lb-ipv6-portal-step8](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [!NOTE]
    > Pokud hned po dokončení kroku 6 vaší skupiny prostředků, "posledního nasazení" se zobrazí stav "Nasazení" se se prostředky nasadí.

9. Klikněte na tlačítko "myIPv6PublicIP" v seznamu prostředků. Uvidíte, že má adresu IPv6 v rámci IP adresu a že jeho název DNS je hodnota zadaná pro parametr dnsNameforIPv6LbIP v kroku 6. Tento prostředek je veřejný IPv6 adres a hostitele název, který je přístupný pro internetové klienty.

    ![lb-ipv6-portal-step9](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>Ověření připojení

Po úspěšném nasazení šablony můžete připojení ověřit provedením následujících úloh:

1. Přihlaste se k portálu Azure a připojit k jednotlivým vytvořené nasazení šablony virtuálních počítačů. Pokud jste nasadili virtuální počítač Windows serveru, spusťte příkaz ipconfig/všechny z příkazového řádku. Uvidíte, že virtuální počítače mají adresy IPv4 a IPv6. Pokud nasadíte virtuální počítače s Linuxem, budete muset nakonfigurovat operační systém Linux přijímat dynamické adresy IPv6 pomocí pokynů pro vaše distribuci systému Linux.
2. Z klienta připojeného k Internetu IPv6 Inicializujte připojení na veřejnou adresu IPv6 služby Vyrovnávání zatížení. Potvrďte, že je nástroj pro vyrovnávání zatížení vyrovnávání mezi dvěma virtuálními počítači, můžete nainstalovat webový server jako internetové informační služby (IIS) na všech virtuálních počítačích. Výchozí webové stránky na každém serveru může obsahovat text "Server0" nebo "Server1" k jeho jednoznačné identifikaci. Potom otevřete internetový prohlížeč na klientech připojené k Internetu IPv6 a přejděte na název hostitele zadaný pro parametr dnsNameforIPv6LbIP nástroje pro vyrovnávání zatížení pro potvrzení připojení IPv6 začátku do konce pro každý virtuální počítač. Pokud se zobrazí pouze webové stránky z jenom jeden server, musíte vymazat mezipaměť prohlížeče. Otevřete více privátní procházení relací. Měli byste vidět odpovědi z jednotlivých serverů.
3. Z klienta připojeného k Internetu IPv4 Inicializujte připojení na veřejnou IPv4 adresu služby Vyrovnávání zatížení. Potvrďte, že je nástroj pro vyrovnávání zatížení dva virtuální počítače Vyrovnávání zatížení, může otestovat pomocí služby IIS, jak je podrobně uvedeno v kroku 2.
4. Z jednotlivých virtuálních počítačů zahájit odchozí připojení do zařízení s IPv6 nebo IPv4 připojená k Internetu. V obou případech zdrojové IP adresy pohledu cílové zařízení je veřejná adresa IPv4 nebo IPv6 pro vyrovnávání zatížení.

> [!NOTE]
> ICMP pro oba protokoly IPv4 a IPv6 je blokována v síti Azure. V důsledku toho ICMP nástroje jako vždy ping nezdaří. K otestování připojení, použijte alternativní TCP například TCPing nebo rutiny prostředí PowerShell Test NetConnection. Všimněte si, že IP adresy vidět na obrázku jsou příklady hodnot, které se můžou objevit. Vzhledem k tomu, že dynamicky přiřazená adresy IPv6, adresy, které se zobrazí, se budou lišit a může lišit podle oblasti. Navíc je běžné pro veřejnou adresu IPv6 na spuštění s jinou předponou než privátní adresy IPv6 ve fondu back-end pro vyrovnávání zatížení.

## <a name="template-parameters-and-variables"></a>Parametry šablony a proměnné

Šablonu Azure Resource Manager obsahuje více proměnných a parametry, které můžete přizpůsobit svým potřebám. Proměnné se používají pro pevné hodnoty, které nechcete, aby uživatel může změnit. Parametry se používají pro hodnoty, že chcete uživatele k zadání při nasazení šablony. Příklad šablony je nakonfigurován pro scénář popsaný v tomto článku. Tuto možnost můžete upravit potřebám vašeho prostředí.

Příklad šablony, použité v tomto článku zahrnuje následující proměnné a parametry:

| Parametr / proměnné | Poznámky |
| --- | --- |
| adminUsername |Zadejte název účtu správce použít k přihlášení k virtuálním počítačům s. |
| adminPassword |Zadejte heslo pro účet správce používá k přihlášení k virtuálním počítačům s. |
| dnsNameforIPv4LbIP |Zadejte název hostitele DNS, kterou chcete přiřadit jako veřejný název služby Vyrovnávání zatížení. Tento název se přeloží na Vyrovnávání zatížení veřejnou adresu IPv4. Název musí být psaný malými písmeny a odpovídat regex: ^ [a-z][a-z0-9-]{1,61}[a-z0-9]$. |
| dnsNameforIPv6LbIP |Zadejte název hostitele DNS, kterou chcete přiřadit jako veřejný název služby Vyrovnávání zatížení. Tento název se přeloží na Vyrovnávání zatížení veřejnou adresu IPv6. Název musí být psaný malými písmeny a odpovídat regex: ^ [a-z][a-z0-9-]{1,61}[a-z0-9]$. To může být stejný název jako adresu IPv4. Když klient odešle dotaz DNS pro tento název vrátí Azure A i AAAA zaznamenává, pokud je název sdílené. |
| vmNamePrefix |Zadejte předponu názvu virtuálního počítače. Šablona připojí číslo (0, 1, atd.) k názvu při vytváření virtuálních počítačů. |
| nicNamePrefix |Zadejte předponu názvu rozhraní sítě. Šablona připojí číslo (0, 1, atd.) k názvu při vytvoření rozhraní sítě. |
| storageAccountName |Zadejte název stávající účet úložiště nebo zadejte název šablony vytvořit novou. |
| availabilitySetName |Potom zadejte název pro skupinu dostupnosti pro použití s virtuálními počítači |
| addressPrefix |Předpona adresy používat k definování rozsah adres virtuální sítě |
| subnetName |Název podsítě v vytvoření sítě vnet |
| subnetPrefix |Předpona adresy používat k definování rozsah adres podsítě |
| vnetName |Zadejte název pro virtuální síť používá virtuální počítače. |
| ipv4PrivateIPAddressType |Použitá metoda přidělení privátní IP adresa (statické nebo dynamické) |
| ipv6PrivateIPAddressType |Metoda přidělení použitá pro privátní IP adresu (dynamické). Dynamické přidělení podporuje pouze protokol IPv6. |
| numberOfInstances |Počet instancí skupinu s vyrovnáváním zatížení, nasazení šablony |
| ipv4PublicIPAddressName |Zadejte název DNS, který chcete použít ke komunikaci s veřejnou IPv4 adresu služby Vyrovnávání zatížení. |
| ipv4PublicIPAddressType |Metoda přidělení používá pro veřejnou IP adresu (statické nebo dynamické) |
| Ipv6PublicIPAddressName |Zadejte název DNS, který chcete použít ke komunikaci s veřejnou adresu IPv6 pro vyrovnávání zatížení. |
| ipv6PublicIPAddressType |Metoda přidělení použitá pro veřejnou IP adresu (dynamické). Dynamické přidělení podporuje pouze protokol IPv6. |
| lbName |Zadejte název služby Vyrovnávání zatížení. Tento název se zobrazí na portálu nebo použít k odkazování na ho pomocí rozhraní CLI nebo Powershellu příkazu. |

Zbývající proměnné v šabloně obsahovat odvozené hodnoty, které jsou přiřazeny, když vytváří prostředky Azure. Neměňte tyto proměnné.
