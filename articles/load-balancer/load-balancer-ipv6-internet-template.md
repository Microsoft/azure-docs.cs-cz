---
title: Nasazení přístupem k Internetu – nástroj pro vyrovnávání zatížení s protokolem IPv6 – šablona Azure | Dokumentace Microsoftu
description: Jak nasadit podpory protokolu IPv6 pro Azure Load Balancer a virtuálních počítačů s vyrovnáváním zatížení.
services: load-balancer
documentationcenter: na
author: KumudD
keywords: protokol IPv6, nástroje pro vyrovnávání zatížení azure, duálním zásobníkem, veřejné IP adresy, nativní protokol ipv6, mobilní zařízení, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 14a28fb341692c309ff4f965628b38a767c56633
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50740738"
---
# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>Nasazení řešení ve nástroje pro vyrovnávání zatížení přístupem k Internetu s protokolem IPv6 pomocí šablony

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Azure CLI](load-balancer-ipv6-internet-cli.md)
> * [Šablona](load-balancer-ipv6-internet-template.md)



Azure Load Balancer je nástroj pro vyrovnávání zatížení úrovně 4 (TCP, UDP). Nástroj pro vyrovnávání zatížení poskytuje vysokou dostupnost díky distribuci příchozích přenosů mezi instance služeb, které jsou v pořádku, v cloudových službách nebo virtuálních počítačích v sadě nástroje pro vyrovnávání zatížení. Azure Load Balancer můžete také tyto služby prezentovat na více portech, více IP adresách nebo obojím.

## <a name="example-deployment-scenario"></a>Příklad scénáře nasazení

Následující diagram znázorňuje řešení vyrovnávání zatížení nasazení pomocí šablony příklad popsaných v tomto článku.

![Scénář nástroje pro vyrovnávání zatížení](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

V tomto scénáři vytvoříte následující prostředky Azure:

* Rozhraní virtuální sítě pro každý virtuální počítač s protokoly IPv4 a IPv6 adresy přiřazené
* Vyrovnávání zatížení přístupem k Internetu s IPv4 a IPv6 veřejnou IP adresu
* Dvě pravidla vyrovnávání přiřadit privátní koncových bodů veřejných virtuálních IP adres zatížení
* Skupinu dostupnosti, která obsahuje dva virtuální počítače
* Dva virtuální počítače (VM)

## <a name="deploying-the-template-using-the-azure-portal"></a>Nasazení pomocí webu Azure portal

Tento článek odkazuje na šablonu, která je publikovaná v [šablony pro rychlý start Azure](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/) galerie. Můžete šablonu stáhnout z galerie, nebo spusťte nasazení v Azure přímo z galerie. Tento článek předpokládá, že jste si stáhli šablonu do místního počítače.

1. Otevřete na webu Azure portal a přihlaste se pomocí účtu, který má oprávnění k vytvoření virtuálních počítačů a síťových prostředků v rámci předplatného Azure. Pokud používáte existujících prostředků, účet musí oprávnění k vytvoření skupiny prostředků a účtu úložiště.
2. Klikněte na tlačítko "+ nový" v nabídce a zadejte "Šablona" do vyhledávacího pole. Ve výsledcích hledání vyberte "Šablony nasazení".

    ![LB-ipv6 – portal – krok 2](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. Vše v okně klikněte na tlačítko "Šablony nasazení."

    ![lb-ipv6-portal-step3](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. Klikněte na možnost "Vytvořit".

    ![LB ipv6 portál step4](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. Klikněte na tlačítko "Upravit šablonu." Odstraňte existující obsah a kopírování a vkládání v celý obsah souboru šablony (chcete zahrnout spuštění a ukončení {}) a pak klikněte na možnost "Uložit."

    > [!NOTE]
    > Pokud používáte Microsoft Internet Explorer, můžete vložit zobrazí dialogové okno s výzvou k povolení přístupu do schránky Windows. Klikněte na možnost "Povolit přístup."

    ![lb-ipv6-portal-step5](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. Klikněte na tlačítko "Upravit parametry." V okně Parametry zadejte hodnoty podle pokynů v oddílu parametry šablony a pak klikněte na "Save (Uložit) zavřete okno parametry. V okně Vlastní nasazení vyberte své předplatné, existující skupinu prostředků nebo vytvořit novou. Pokud vytvoříte skupinu prostředků, vyberte umístění pro skupinu prostředků. Klepnutím na tlačítko **právní podmínky**, pak klikněte na tlačítko **nákupní** pro právní podmínky. Azure zahájí nasazení prostředků. Trvá několik minut, do které prostředky nasadíte.

    ![LB ipv6 portál step6](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    Další informace o těchto parametrech najdete v tématu [šablonu parametry a proměnné](#template-parameters-and-variables) části dále v tomto článku.

7. Pokud chcete zobrazit prostředky vytvořené v rámci šablony, klikněte na tlačítko Procházet, přejděte dolů na položku v seznamu naleznete v tématu "Skupiny prostředků", potom na ni klikněte.

    ![lb-ipv6-portal-step7](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. V okně skupiny prostředků klikněte na název skupiny prostředků, které jste zadali v kroku 6. Zobrazí seznam všech prostředků, které se nasadily. Pokud vše se podařilo, to by mělo být uvedeno "ÚSPĚCH" v části "Posledního nasazení." Pokud ne, ujistěte se, že účet, který používáte, má oprávnění k vytvoření potřebných prostředků.

    ![lb-ipv6-portal-step8](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [!NOTE]
    > Pokud hned po dokončení kroku 6 skupin prostředků, "posledního nasazení" se zobrazí stav "Nasazení" se se prostředky nasadí.

9. Klikněte na tlačítko "myIPv6PublicIP" v seznamu prostředků. Uvidíte, že na něm adresu IPv6 v oblasti IP adresa a že jeho název DNS je hodnota zadaná pro parametr dnsNameforIPv6LbIP v kroku 6. Tento prostředek je veřejnou IPv6 adresu a název hostitele, který je přístupný pro internetové klienty.

    ![LB ipv6 portál step9](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>Ověření připojení

Po úspěšném nasazení šablony můžete připojení ověřit provedením následujících úloh:

1. Přihlaste se k webu Azure portal a připojte se k každý virtuální počítač vytvořil šablonu nasazení. Pokud jste nasadili Server virtuálního počítače s Windows, spusťte příkaz ipconfig/vše z příkazového řádku. Uvidíte, že virtuální počítače mají adresy IPv4 a IPv6. Pokud jste nasadili virtuální počítače s Linuxem, budete muset nakonfigurovat operační systém Linux přijímat dynamické adresy IPv6 pomocí pokynů pro vaši Linuxovou distribuci.
2. Z klienta připojeného k Internetu s protokolem IPv6 Inicializujte připojení a veřejnou IPv6 adresu nástroje pro vyrovnávání zatížení. Pokud chcete potvrdit, že je nástroj pro vyrovnávání zatížení vyrovnávání mezi dvěma virtuálními počítači, můžete nainstalovat webový server jako internetové informační služby (IIS) na všech virtuálních počítačích. Výchozí webová stránka na každém serveru může obsahovat text "Server0" nebo "Server1", aby byl jednoznačně identifikovaný. Potom otevřete internetový prohlížeč na klientech připojeného k Internetu s protokolem IPv6 a přejděte na název hostitele zadaný pro parametr dnsNameforIPv6LbIP potvrdíte připojení IPv6 začátku do konce pro každý virtuální počítač nástroje pro vyrovnávání zatížení. Pokud se zobrazí pouze webové stránky z jenom jeden server, budete muset vymazat mezipaměť prohlížeče. Otevřete více privátní procházení relací. Měli byste vidět odpovědi z jednotlivých serverů.
3. Z klienta připojeného k Internetu s IPv4 Inicializujte připojení a veřejnou IPv4 adresu nástroje pro vyrovnávání zatížení. Abyste se ujistili, že nástroj pro vyrovnávání zatížení dva virtuální počítače pro vyrovnávání zatížení, je možné otestovat pomocí služby IIS, jak je uvedeno v kroku 2.
4. Z každého virtuálního počítače inicializujte odchozí připojení k zařízení s IPv6 nebo internetové připojení IPv4. V obou případech je zdrojová IP adresa vidět cílové zařízení veřejnou IPv4 nebo IPv6 adresu nástroje pro vyrovnávání zatížení.

> [!NOTE]
> ICMP pro protokol IPv4 i IPv6 je blokována v síti Azure. V důsledku toho ICMP nástroje jako vždy ping nezdaří. K otestování připojení, použijte alternativní TCP například použít příkaz TCPing nebo rutiny prostředí PowerShell Test-NetConnection. Všimněte si, že IP adresy je vidět na obrázku jsou příklady hodnot, které se mohou objevit. Protože IPv6 adres se přidělují dynamicky, adresy, které se zobrazí, se budou lišit a jednotlivých oblastech můžou lišit. Navíc je běžné, že veřejnou IPv6 adresu v nástroji pro vyrovnávání zatížení má začít s jinou předponu než privátními IPv6 adresami ve fondu back-end.

## <a name="template-parameters-and-variables"></a>Parametry šablony a proměnné

Šablony Azure Resource Manageru obsahuje několik proměnných a parametry, které můžete přizpůsobit svým potřebám. Proměnné se používají pro pevné hodnoty, které nechcete, aby uživatel změnit. Parametry se používají pro hodnoty, který má uživatel zadávat při nasazení šablony. Ukázková šablona je nakonfigurovaná pro scénář popsaný v tomto článku. Si ho můžete přizpůsobit podle potřeb vašeho prostředí.

Příklad šablony použité v tomto článku obsahuje následující proměnné a parametry:

| Parametr / proměnné | Poznámky |
| --- | --- |
| adminUsername |Zadejte název účtu správce používá k přihlášení k virtuálním počítačům s. |
| adminPassword |Zadejte heslo pro účet správce používá k přihlášení k virtuálním počítačům s. |
| dnsNameforIPv4LbIP |Zadejte název hostitele DNS, kterou chcete přiřadit jako veřejný název nástroje pro vyrovnávání zatížení. Tento název se přeloží na veřejnou IPv4 adresu nástroje pro vyrovnávání zatížení. Název musí být psaný malými písmeny a odpovídat regulárnímu: ^ [-z] [-z0 - 9-]{1,61}[a-z0-9] $. |
| dnsNameforIPv6LbIP |Zadejte název hostitele DNS, kterou chcete přiřadit jako veřejný název nástroje pro vyrovnávání zatížení. Tento název se přeloží na veřejnou IPv6 adresu nástroje pro vyrovnávání zatížení. Název musí být psaný malými písmeny a odpovídat regulárnímu: ^ [-z] [-z0 - 9-]{1,61}[a-z0-9] $. Může to být stejný název jako adresu IPv4. Když klient odešle dotaz DNS pro tento název bude vracet Azure A a AAAA zaznamenává, když se sdílí název. |
| vmNamePrefix |Zadejte předponu názvu virtuálního počítače. Šablona přidá číslo (0, 1, atd.) k názvu při vytváření virtuálních počítačů. |
| nicNamePrefix |Zadejte předponu názvu síťové rozhraní. Šablona přidá číslo (0, 1, atd.) k názvu při vytvoření síťových rozhraní. |
| storageAccountName |Zadejte název existující účet úložiště nebo zadejte název nové předplatné má být vytvořen pomocí šablony. |
| availabilitySetName |Potom zadejte název sady dostupnosti pro použití s virtuálními počítači |
| addressPrefix |Předpona adresy sloužících k definování rozsahu adres ve virtuální síti |
| subnetName |Název podsítě ve vytvořené pro virtuální síť |
| subnetPrefix |Předpona adresy sloužících k definování rozsahu adres podsítě. |
| vnetName |Zadejte název pro virtuální síť používat virtuální počítače. |
| ipv4PrivateIPAddressType |Metodu přidělování privátní IP adresy (statické nebo dynamické) |
| ipv6PrivateIPAddressType |Metodu přidělování pro privátní IP adresu (dynamickou). Dynamické přidělení podporuje pouze protokol IPv6. |
| numberOfInstances |Počet instancí s vyrovnáváním zatížení pomocí šablony nasazení |
| ipv4PublicIPAddressName |Zadejte název DNS, který chcete použít ke komunikaci s veřejnou IPv4 adresu nástroje pro vyrovnávání zatížení. |
| ipv4PublicIPAddressType |Metodu přidělování pro veřejnou IP adresu (statická nebo dynamická) |
| Ipv6PublicIPAddressName |Zadejte název DNS, který chcete použít ke komunikaci s veřejnou IPv6 adresu nástroje pro vyrovnávání zatížení. |
| ipv6PublicIPAddressType |Metodu přidělování pro veřejnou IP adresu (dynamickou). Dynamické přidělení podporuje pouze protokol IPv6. |
| lbName |Zadejte název nástroje pro vyrovnávání zatížení. Tento název se zobrazí na portálu nebo používají při odkazování na ji pomocí příkazu rozhraní příkazového řádku nebo Powershellu. |

Zbývající proměnné v šabloně obsahují odvozeným hodnotám, které jsou přiřazeny při Azure vytvoří potřebné prostředky. Neměňte těchto proměnných.
