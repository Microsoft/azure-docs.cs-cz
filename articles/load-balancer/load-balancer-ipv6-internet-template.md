---
title: Nasazení internetového vyvažovače zatížení pomocí šablony IPv6 – Azure
titleSuffix: Azure Load Balancer
description: Zjistěte, jak nasadit podporu IPv6 pro Azure Load Balancer a virtuální počítače s vyrovnáváním zatížení pomocí šablony Azure.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, azure balancer, dual stack, public ip, nativní ipv6, mobilní, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: eb9703a1944a650f41d76c05d79764f8bdf8cd52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045446"
---
# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>Nasazení internetového řešení vyrovnávání zatížení pomocí iPv6 pomocí šablony

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Azure CLI](load-balancer-ipv6-internet-cli.md)
> * [Šablony](load-balancer-ipv6-internet-template.md)


>[!NOTE] 
>Tento článek popisuje úvodní funkci IPv6, která umožňuje základním vykladačům zatížení poskytovat připojení IPv4 i IPv6. Komplexní připojení IPv6 je teď k dispozici s [iPv6 pro virtuální sítě Azure,](../virtual-network/ipv6-overview.md) které integrují připojení IPv6 s virtuálními sítěmi a zahrnují klíčové funkce, jako jsou pravidla skupiny zabezpečení sítě IPv6, směrování definované uživatelem IPv6, vyrovnávání zatížení IPv6 basic a standardní vyrovnávání zatížení a další.  IPv6 pro virtuální užitky Azure je doporučený standard pro aplikace IPv6 v Azure. Viz [IPv6 pro nasazení Azure VNET Powershellu](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)  

Azure Load Balancer je nástroj pro vyrovnávání zatížení úrovně 4 (TCP, UDP). Nástroj pro vyrovnávání zatížení poskytuje vysokou dostupnost díky distribuci příchozích přenosů mezi instance služeb, které jsou v pořádku, v cloudových službách nebo virtuálních počítačích v sadě nástroje pro vyrovnávání zatížení. Azure Load Balancer můžete také tyto služby prezentovat na více portech, více IP adresách nebo obojím.

## <a name="example-deployment-scenario"></a>Příklad scénáře nasazení

Následující diagram znázorňuje řešení vyrovnávání zatížení, které se nasazuje pomocí ukázkové šablony popsané v tomto článku.

![Scénář nástroje pro vyrovnávání zatížení](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

V tomto scénáři vytvoříte následující prostředky Azure:

* Rozhraní virtuální sítě pro každý virtuální počítač s přiřazenými adresami IPv4 i IPv6
* Internetový vyrovnávání zatížení s IPv4 a veřejnou IP adresou IPv6
* dvě pravidla vyrovnávání zatížení pro mapování veřejných VIP na soukromé koncové body
* Sada dostupnosti, která obsahuje dva virtuální servery
* dva virtuální počítače (VM)

## <a name="deploying-the-template-using-the-azure-portal"></a>Nasazení šablony pomocí portálu Azure

Tento článek odkazuje na šablonu, která je publikována v galerii [šablon Azure QuickStart.](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/) Šablonu si můžete stáhnout z galerie nebo spustit nasazení v Azure přímo z galerie. Tento článek předpokládá, že jste šablonu stáhli do místního počítače.

1. Otevřete portál Azure a přihlaste se pomocí účtu, který má oprávnění k vytváření virtuálních počítačů a síťových prostředků v rámci předplatného Azure. Pokud také nepoužíváte existující prostředky, účet potřebuje oprávnění k vytvoření skupiny prostředků a účtu úložiště.
2. Klikněte na "+Nový" z nabídky a do vyhledávacího pole zadejte "šablona". Ve výsledcích hledání vyberte možnost Nasazení šablony.

    ![lb-ipv6-portál-krok2](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. V okně Vše klikněte na "Nasazení šablony".

    ![lb-ipv6-portál-krok3](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. Klikněte na tlačítko Vytvořit.

    ![lb-ipv6-portál-krok4](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. Klikněte na "Upravit šablonu". Odstraňte existující obsah a zkopírujte nebo vložte do celého obsahu souboru šablony (chcete-li zahrnout začátek a konec { }), pak klikněte na tlačítko Uložit.

    > [!NOTE]
    > Pokud používáte aplikaci Microsoft Internet Explorer, obdržíte při vložení dialogové okno s žádostí o povolení přístupu ke schránce systému Windows. Klikněte na možnost Povolit přístup.

    ![lb-ipv6-portál-krok5](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. Klikněte na tlačítko Upravit parametry. V okně Parametry zadejte hodnoty podle pokynů v části Parametry šablony a klepnutím na tlačítko "Uložit" zavřete okno Parametry. V okně Vlastní nasazení vyberte předplatné, existující skupinu prostředků nebo ji vytvořte. Pokud vytváříte skupinu prostředků, vyberte umístění pro skupinu prostředků. Dále klikněte na **Právní podmínky**a potom na **položku Nákup.** Azure začne nasazovat prostředky. Nasazení všech prostředků trvá několik minut.

    ![lb-ipv6-portál-krok6](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    Další informace o těchto parametrech naleznete v části [Parametry šablony a proměnné](#template-parameters-and-variables) dále v tomto článku.

7. Pokud chcete zobrazit prostředky vytvořené šablonou, klikněte na Procházet, posuňte se v seznamu dolů, dokud se nezobrazí "Skupiny prostředků", a klikněte na něj.

    ![lb-ipv6-portál-krok7](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. V okně Skupiny prostředků klikněte na název skupiny prostředků, kterou jste zadali v kroku 6. Zobrazí se seznam všech prostředků, které byly nasazeny. Pokud vše proběhlo dobře, mělo by se tam říkat "Úspěšné" v části "Poslední nasazení". Pokud ne, ujistěte se, že účet, který používáte, má oprávnění k vytvoření potřebných prostředků.

    ![lb-ipv6-portál-krok8](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [!NOTE]
    > Pokud procházíte skupiny prostředků ihned po dokončení kroku 6, "Poslední nasazení" zobrazí stav "Nasazení", zatímco prostředky jsou nasazovány.

9. Klikněte na "myIPv6PublicIP" v seznamu zdrojů. Uvidíte, že má adresu IPv6 pod adresou IP a že její název DNS je hodnota, kterou jste zadali pro parametr dnsNameforIPv6LbIP v kroku 6. Tento prostředek je veřejná adresa IPv6 a název hostitele, který je přístupný internetovým klientům.

    ![lb-ipv6-portál-krok9](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>Ověřit připojení

Pokud je šablona úspěšně nasazena, můžete ověřit připojení provedením následujících úkolů:

1. Přihlaste se k portálu Azure a připojte se ke každému virtuálnímu počítači vytvořenému nasazením šablony. Pokud jste nasadili virtuální počítač se systémem Windows Server, spusťte ipconfig /all z příkazového řádku. Zjistíte, že virtuální aplikace mají adresy IPv4 i IPv6. Pokud jste nasadili virtuální počítače s Linuxem, musíte nakonfigurovat operační systém Linux tak, aby přijímal dynamické adresy IPv6 podle pokynů pro distribuci Linuxu.
2. Z klienta připojeného k Internetu IPv6 iniciujte připojení k veřejné adrese IPv6 v systému vyrovnávání zatížení. Chcete-li ověřit, že vyrovnávání mezi dvěma virtuálními počítači, můžete nainstalovat webový server, jako je Microsoft Internet Information Services (IIS) na každý z virtuálních počítačů. Výchozí webová stránka na každém serveru může obsahovat text "Server0" nebo "Server1", aby byl jednoznačně identifikován. Potom otevřete internetový prohlížeč v klientovi připojeném k Internetu IPv6 a přejděte na název hostitele, který jste zadali pro parametr dnsNameforIPv6LbIP v systému vyrovnávání zatížení, abyste potvrdili komplexní připojení IPv6 ke každému virtuálnímu počítači. Pokud se webová stránka zobrazuje pouze z jednoho serveru, bude pravděpodobně nutné vymazat mezipaměť prohlížeče. Otevřete více relací soukromého procházení. Měli byste vidět odpověď z každého serveru.
3. Z klienta Připojeného k Internetu IPv4 iniciujte připojení k veřejné adrese IPv4 v systému vyrovnávání zatížení. Chcete-li ověřit, že vydělávač zatížení vyrovnává dva virtuální aplikace, můžete otestovat pomocí služby IIS, jak je podrobně popsáno v kroku 2.
4. Z každého virtuálního virtuálního aplikace iniciujte odchozí připojení k internetovému zařízení připojenému k IPv6 nebo IPv4. V obou případech je zdrojová IP adresa získaná cílovým zařízením veřejná adresa IPv4 nebo IPv6 nástroj pro vyrovnávání zatížení.

> [!NOTE]
> ICMP pro IPv4 a IPv6 je blokován v síti Azure. V důsledku toho nástroje ICMP, jako je ping vždy nezdaří. Chcete-li otestovat připojení, použijte alternativu TCP, jako je tcping nebo rutina PowerShell Test-NetConnection. Všimněte si, že IP adresy uvedené v diagramu jsou příklady hodnot, které se mohou zobrazit. Vzhledem k tomu, že adresy IPv6 jsou přiřazovány dynamicky, budou se zadané adresy lišit a mohou se lišit podle oblasti. Je také běžné, že veřejná adresa IPv6 v záložně na vyrovnávání zatížení začíná jinou předponou než privátní adresy IPv6 v back-endovém fondu.

## <a name="template-parameters-and-variables"></a>Parametry a proměnné šablony

Šablona Azure Resource Manager obsahuje více proměnných a parametrů, které můžete přizpůsobit vašim potřebám. Proměnné se používají pro pevné hodnoty, které nechcete, aby uživatel měnit. Parametry se používají pro hodnoty, které má uživatel poskytnout při nasazování šablony. Ukázková šablona je nakonfigurována pro scénář popsaný v tomto článku. Můžete přizpůsobit to potřebám vašeho prostředí.

Ukázková šablona použitá v tomto článku obsahuje následující proměnné a parametry:

| Parametr / Proměnná | Poznámky |
| --- | --- |
| adminUsername |Zadejte název účtu správce, který se používá k přihlášení k virtuálním počítačům. |
| adminPassword |Zadejte heslo pro účet správce, který se používá k přihlášení k virtuálním počítačům. |
| dnsNameforIPv4LbIP |Zadejte název hostitele DNS, který chcete přiřadit jako veřejný název systému vyrovnávání zatížení. Tento název se překládá na veřejnou adresu IPv4 vykladače zatížení. Název musí být malý a musí odpovídat regulárnímu výrazu: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$. |
| dnsNameforIPv6LbIP |Zadejte název hostitele DNS, který chcete přiřadit jako veřejný název systému vyrovnávání zatížení. Tento název se překládá na veřejnou adresu IPv6 vykladače zatížení. Název musí být malý a musí odpovídat regulárnímu výrazu: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$. Může se pojmenovat stejně jako adresa IPv4. Když klient odešle dotaz DNS pro tento název Azure vrátí záznamy A a AAAA při sdílení názvu. |
| vmNamePrefix |Zadejte předponu názvu virtuálního soudu. Šablona připojí číslo (0, 1, atd.) k názvu při vytvoření virtuálních počítače. |
| nicNamePrefix |Zadejte předponu názvu síťového rozhraní. Šablona připojí číslo (0, 1, atd.) k názvu při vytvoření síťových rozhraní. |
| storageAccountName |Zadejte název existujícího účtu úložiště nebo zadejte název nového účtu, který má šablona vytvořit. |
| availabilitySetName |Zadejte název skupiny dostupnosti, která má být použita s virtuálními mísami. |
| addressPrefix |Předpona adresy použitá k definování rozsahu adres virtuální sítě |
| název podsítě |Název podsítě vytvořené pro virtuální síť |
| subnetPrefix |Předpona adresy použitá k definování rozsahu adres podsítě |
| vnetName |Zadejte název virtuální sítě používané virtuálními sítěmi. |
| Ipv4PrivateIPAddressTyp |Metoda přidělení použitá pro privátní IP adresu (statickou nebo dynamickou) |
| Ipv6PrivateIPAddressType |Metoda přidělení použitá pro privátní IP adresu (Dynamic). Protokol IPv6 podporuje pouze dynamickou alokaci. |
| numberOfInstances |Počet instancí s vyrovnáváním zatížení nasazených šablonou |
| Ipv4PublicIPAddressNázev |Zadejte název DNS, který chcete použít ke komunikaci s veřejnou adresou IPv4 v systému vyrovnávání zatížení. |
| ipv4PublicIPAddressType |Metoda přidělení použitá pro veřejnou IP adresu (statickou nebo dynamickou) |
| Ipv6PublicIPAddressName |Zadejte název DNS, který chcete použít ke komunikaci s veřejnou adresou IPv6 v systému vyrovnávání zatížení. |
| ipv6PublicIPAddressType |Metoda přidělení použitá pro veřejnou IP adresu (Dynamic). Protokol IPv6 podporuje pouze dynamickou alokaci. |
| lbNázev |Zadejte název provynace zatížení. Tento název se zobrazí na portálu nebo se používá při odkazování na něj příkazem CLI nebo PowerShell. |

Zbývající proměnné v šabloně obsahují odvozené hodnoty, které jsou přiřazeny při azure vytvoří prostředky. Tyto proměnné neměňte.

## <a name="next-steps"></a>Další kroky

Syntaxe JSON a vlastnosti vykladače zatížení v šabloně naleznete v tématu [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers).
