---
title: Nasazení internetového nástroje pro vyrovnávání zatížení pomocí šablony IPv6 – Azure
titleSuffix: Azure Load Balancer
description: Naučte se, jak nasadit podporu IPv6 pro virtuální počítače s Azure Load Balancer a s vyrovnáváním zatížení pomocí šablony Azure.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, Azure Load Balancer, duální zásobník, veřejná IP adresa, nativní IPv6, mobilní zařízení, IoT
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 415c95a441ac0cc6ed2dbf2d6a37f57d7a9e7341
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90562515"
---
# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>Nasazení řešení internetového vyrovnávání zatížení s protokolem IPv6 pomocí šablony

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Azure CLI](load-balancer-ipv6-internet-cli.md)
> * [Šablona](load-balancer-ipv6-internet-template.md)


>[!NOTE] 
>Tento článek popisuje úvodní funkci IPv6, která umožňuje základním nástrojům pro vyrovnávání zatížení poskytovat připojení protokolem IPv4 i IPv6. K dispozici je teď komplexní připojení pomocí protokolu [IPv6 pro Azure virtuální sítě](../virtual-network/ipv6-overview.md) , které integruje připojení IPv6 k vašim virtuálním sítím a obsahuje klíčové funkce, jako jsou pravidla skupiny zabezpečení sítě IPv6, uživatelem definované směrování IPv6, protokol IPv6 Basic a standardní vyrovnávání zatížení a další.  Protokol IPv6 pro Azure virtuální sítě je doporučený standard pro aplikace IPv6 v Azure. Viz [protokol IPv6 pro nasazení PowerShellu pro virtuální síť Azure](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)  

Azure Load Balancer je nástroj pro vyrovnávání zatížení úrovně 4 (TCP, UDP). Nástroj pro vyrovnávání zatížení poskytuje vysokou dostupnost díky distribuci příchozích přenosů mezi instance služeb, které jsou v pořádku, v cloudových službách nebo virtuálních počítačích v sadě nástroje pro vyrovnávání zatížení. Azure Load Balancer můžete také tyto služby prezentovat na více portech, více IP adresách nebo obojím.

## <a name="example-deployment-scenario"></a>Ukázkový scénář nasazení

Následující diagram znázorňuje nasazení řešení vyrovnávání zatížení pomocí ukázkové šablony popsané v tomto článku.

![Diagram znázorňuje ukázkový scénář, který se používá v tomto článku, včetně klienta pracovní stanice připojeného k Azure Load Balancer prostřednictvím Internetu, který je připojený k dvěma virtuálním počítačům.](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

V tomto scénáři vytvoříte následující prostředky Azure:

* virtuální síťové rozhraní pro každý virtuální počítač s přiřazenými adresami IPv4 i IPv6
* Internetový Load Balancer s IPv4 a veřejnou IP adresou IPv6
* dvě pravidla vyrovnávání zatížení pro mapování veřejných virtuálních IP adres na privátní koncové body
* Skupina dostupnosti, která obsahuje dva virtuální počítače
* dva virtuální počítače

## <a name="deploying-the-template-using-the-azure-portal"></a>Nasazení šablony pomocí Azure Portal

Tento článek odkazuje na šablonu, která je publikovaná v galerii [šablon Azure pro rychlý Start](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/) . Šablonu si můžete stáhnout z Galerie nebo spustit nasazení v Azure přímo z galerie. V tomto článku se předpokládá, že jste stáhli šablonu do svého místního počítače.

1. Otevřete Azure Portal a přihlaste se pomocí účtu, který má oprávnění k vytváření virtuálních počítačů a síťových prostředků v rámci předplatného Azure. Kromě toho, pokud nepoužíváte stávající prostředky, účet potřebuje oprávnění k vytvoření skupiny prostředků a účtu úložiště.
2. V nabídce klikněte na + nový a potom do vyhledávacího pole zadejte Template. Ve výsledcích hledání vyberte "Template deployment".

    ![Snímek obrazovky zobrazuje Azure Portal s vybranými možnostmi New a Template deployment.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. V okně vše klikněte na Template deployment.

    ![Snímek obrazovky ukazuje Template deployment na webu Marketplace.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. Klikněte na vytvořit.

    ![Snímek obrazovky zobrazující popis Template deployment na webu Marketplace.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. Klikněte na Upravit šablonu. Odstraňte existující obsah a zkopírujte nebo vložte do celého obsahu souboru šablony (aby zahrnoval začátek a konec {}) a pak klikněte na Uložit.

    > [!NOTE]
    > Pokud používáte aplikaci Microsoft Internet Explorer, při vložení se zobrazí dialogové okno s výzvou k povolení přístupu ke schránce systému Windows. Klikněte na možnost "udělit přístup".

    ![Snímek obrazovky se zobrazí v kroku spuštění vlastního nasazení, které je upravit šablonu.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. Klikněte na upravit parametry. V okně Parametry zadejte hodnoty podle pokynů v části parametry šablony a potom kliknutím na Uložit zavřete okno parametry. V okně vlastní nasazení vyberte vaše předplatné, existující skupinu prostředků nebo vytvořte jednu z nich. Pokud vytváříte skupinu prostředků, vyberte umístění pro skupinu prostředků. V dalším kroku klikněte na **právní předpisy** a pak na **koupit** pro zákonné smlouvy. Azure začíná nasazovat prostředky. Nasazení všech prostředků trvá několik minut.

    ![Snímek obrazovky se zobrazí kroky, které jsou součástí vlastního nasazení, počínaje zadáním hodnot parametrů šablony.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    Další informace o těchto parametrech naleznete v části [parametry šablony a proměnné](#template-parameters-and-variables) dále v tomto článku.

7. Chcete-li zobrazit prostředky vytvořené šablonou, klikněte na tlačítko Procházet, přejděte v seznamu dolů, dokud neuvidíte "skupiny prostředků" a klikněte na něj.

    ![Snímek obrazovky zobrazuje Azure Portal s vybranými možnostmi Procházet a skupiny prostředků.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. V okně skupiny prostředků klikněte na název skupiny prostředků, kterou jste zadali v kroku 6. Zobrazí se seznam všech nasazených prostředků. Pokud vše bylo úspěšné, mělo by se v části poslední nasazení jednat o úspěch. Pokud ne, ujistěte se, že účet, který používáte, má oprávnění k vytváření potřebných prostředků.

    ![Snímek obrazovky zobrazuje stav posledního nasazení pro skupinu prostředků, v tomto příkladu bylo úspěšné.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [!NOTE]
    > Pokud prohlížíte skupiny prostředků hned po dokončení kroku 6, "poslední nasazení" zobrazí při nasazení prostředků stav "nasazování".

9. V seznamu prostředků klikněte na "myIPv6PublicIP". Uvidíte, že má v části IP adresa IPv6 adresu a že jeho název DNS je hodnota, kterou jste zadali pro parametr dnsNameforIPv6LbIP v kroku 6. Tento prostředek je veřejná adresa IPv6 a název hostitele, který je přístupný pro internetové klienty.

    ![Snímek obrazovky se zobrazí ve veřejné adrese IPv6.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>Ověřit připojení

Po úspěšném nasazení šablony můžete ověřit připojení provedením následujících úloh:

1. Přihlaste se k Azure Portal a připojte se ke každému virtuálnímu počítači vytvořenému nasazením šablony. Pokud jste nasadili virtuální počítač s Windows serverem, spusťte na příkazovém řádku příkaz ipconfig/all. Vidíte, že virtuální počítače mají adresy IPv4 i IPv6. Pokud jste nasadili virtuální počítače se systémem Linux, je nutné nakonfigurovat operační systém Linux pro příjem dynamických adres IPv6 podle pokynů pro distribuci systému Linux.
2. Z klienta připojeného k Internetu s protokolem IPv6 Iniciujte připojení k veřejné adrese IPv6 nástroje pro vyrovnávání zatížení. Chcete-li ověřit, že nástroj pro vyrovnávání zatížení vyrovnává mezi dvěma virtuálními počítači, můžete nainstalovat webový server, jako je například Microsoft Internetová informační služba (IIS) na každém virtuálním počítači. Výchozí webová stránka na každém serveru může obsahovat text "Server0" nebo "Server1" k jednoznačné identifikaci. Pak otevřete internetový prohlížeč na klientovi připojeném k Internetu s protokolem IPv6 a vyhledejte název hostitele, který jste zadali pro parametr dnsNameforIPv6LbIP nástroje pro vyrovnávání zatížení, a potvrďte tak koncové připojení protokolu IPv6 ke každému virtuálnímu počítači. Pokud se webová stránka zobrazí jenom jenom na jednom serveru, možná budete muset vymazat mezipaměť prohlížeče. Otevřete několik privátních relací procházení. Měla by se zobrazit odpověď z každého serveru.
3. Z klienta připojeného k Internetu s protokolem IPv4 Iniciujte připojení k veřejné IPv4 adrese nástroje pro vyrovnávání zatížení. Pokud chcete potvrdit, že nástroj pro vyrovnávání zatížení vyrovnává zatížení těchto dvou virtuálních počítačů, můžete otestovat pomocí služby IIS, jak je popsáno v kroku 2.
4. Z každého virtuálního počítače Iniciujte odchozí připojení k internetovému zařízení připojenému k síti IPv6 nebo IPv4. V obou případech je zdrojová adresa IP, kterou cílové zařízení uvidí, veřejnou adresou protokolu IPv4 nebo IPv6 nástroje pro vyrovnávání zatížení.

> [!NOTE]
> Protokol ICMP pro IPv4 i IPv6 je v síti Azure blokovaný. V důsledku toho nástroje protokolu ICMP, jako je třeba příkaz test, vždy selžou. K otestování připojení použijte alternativu protokolu TCP, jako je TCPing nebo rutina PowerShellu Test-NetConnection. Všimněte si, že IP adresy zobrazené v diagramu představují příklady hodnot, které mohou být zobrazeny. Vzhledem k tomu, že se adresy IPv6 přiřazují dynamicky, adresy, které obdržíte, se budou lišit a můžou se lišit podle oblasti. Také je běžné, že veřejná IPv6 adresa v nástroji pro vyrovnávání zatížení začíná jinou předponou než privátní IPv6 adresy ve fondu back-end.

## <a name="template-parameters-and-variables"></a>Parametry šablony a proměnné

Šablona Azure Resource Manager obsahuje více proměnných a parametrů, které můžete přizpůsobit podle svých potřeb. Proměnné jsou používány pro pevné hodnoty, které nechcete, aby uživatel měnil. Parametry se používají pro hodnoty, které má uživatel zadat při nasazování šablony. Ukázková šablona je nakonfigurována pro scénář popsaný v tomto článku. Můžete ho přizpůsobit potřebám vašeho prostředí.

Ukázková šablona použitá v tomto článku obsahuje následující proměnné a parametry:

| Parametr/proměnná | Poznámky |
| --- | --- |
| adminUsername |Zadejte název účtu správce, který se používá pro přihlášení k virtuálním počítačům. |
| adminPassword |Zadejte heslo pro účet správce, který se používá pro přihlášení k virtuálním počítačům. |
| dnsNameforIPv4LbIP |Zadejte název hostitele DNS, který chcete přiřadit jako veřejný název nástroje pro vyrovnávání zatížení. Tento název se překládá na veřejnou IPv4 adresu nástroje pro vyrovnávání zatížení. Název musí být malými písmeny a musí odpovídat regulárnímu výrazu: ^ [a-z] [a-Z0-9-] {1,61} [a-Z0-9] $. |
| dnsNameforIPv6LbIP |Zadejte název hostitele DNS, který chcete přiřadit jako veřejný název nástroje pro vyrovnávání zatížení. Tento název se přeloží na veřejnou IPv6 adresu nástroje pro vyrovnávání zatížení. Název musí být malými písmeny a musí odpovídat regulárnímu výrazu: ^ [a-z] [a-Z0-9-] {1,61} [a-Z0-9] $. Může se jednat o stejný název jako adresa IPv4. Když klient pošle dotaz DNS na tento název, Azure při sdílení názvu vrátí záznamy A i AAAA. |
| vmNamePrefix |Zadejte předponu názvu virtuálního počítače. Šablona připojí k názvu číslo (0, 1 atd.), když se vytvoří virtuální počítače. |
| nicNamePrefix |Zadejte předponu názvu síťového rozhraní. Šablona připojí číslo (0, 1 atd.) k názvu při vytvoření síťových rozhraní. |
| storageAccountName |Zadejte název existujícího účtu úložiště nebo zadejte název nového, který má šablona vytvořit. |
| availabilitySetName |Zadejte název skupiny dostupnosti, která se má používat s virtuálními počítači. |
| addressPrefix |Předpona adresy používaná k definování rozsahu adres Virtual Network |
| subnetName |Název podsítě vytvořené pro virtuální síť |
| subnetPrefix |Předpona adresy používaná k definování rozsahu adres podsítě |
| vnetName |Zadejte název virtuální sítě používané virtuálními počítači. |
| ipv4PrivateIPAddressType |Metoda alokace použitá pro privátní IP adresu (static nebo Dynamic) |
| ipv6PrivateIPAddressType |Metoda přidělení, která se používá pro privátní IP adresu (dynamická). IPv6 podporuje pouze dynamické přidělování. |
| numberOfInstances |Počet instancí s vyrovnáváním zatížení nasazených šablonou |
| ipv4PublicIPAddressName |Zadejte název DNS, který chcete použít ke komunikaci s veřejnou IPv4 adresou nástroje pro vyrovnávání zatížení. |
| ipv4PublicIPAddressType |Metoda přidělení, která se používá pro veřejnou IP adresu (static nebo Dynamic) |
| Ipv6PublicIPAddressName |Zadejte název DNS, který chcete použít ke komunikaci s veřejnou adresou IPv6 nástroje pro vyrovnávání zatížení. |
| ipv6PublicIPAddressType |Metoda přidělení, která se používá pro veřejnou IP adresu (dynamická). IPv6 podporuje pouze dynamické přidělování. |
| lbName |Zadejte název nástroje pro vyrovnávání zatížení. Tento název se zobrazí na portálu nebo použije se při odkazování pomocí příkazu CLI nebo PowerShellu. |

Zbývající proměnné v šabloně obsahují odvozené hodnoty, které jsou přiřazeny při vytváření prostředků v Azure. Neměňte tyto proměnné.

## <a name="next-steps"></a>Další kroky

Syntaxi a vlastnosti služby Vyrovnávání zatížení v šabloně najdete v tématu [Microsoft. Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers).
