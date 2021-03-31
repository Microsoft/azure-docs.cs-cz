---
title: Hostování více webů v Azure Application Gateway
description: Tento článek poskytuje přehled podpory Azure Application Gateway více lokalit.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 07/20/2020
ms.author: surmb
ms.topic: conceptual
ms.openlocfilehash: 53f6f37454de886934a483b40daad24204958baf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87474321"
---
# <a name="application-gateway-multiple-site-hosting"></a>Hostování více webů ve službě Application Gateway

Hostování více webů umožňuje konfigurovat více než jednu webovou aplikaci na stejném portu služby Application Gateway. Díky možnosti přidat do jedné služby Application Gateway více než 100 webů můžete nakonfigurovat efektivnější topologii vašich nasazení. Každou stránku lze přesměrovat na vlastní back-endový fond. Příklad: na IP adresu služby Application Gateway odkazují tři domény – contoso.com, fabrikam.com a adatum.com. Můžete vytvořit tři naslouchací procesy pro více webů a jednotlivé naslouchací procesy nakonfigurovat pro příslušené nastavení portu a protokolu. 

V naslouchacím procesu pro více webů můžete také definovat názvy hostitelů se zástupnými znaky a až 5 názvů hostitelů na naslouchací proces. Další informace najdete v tématu [názvy hostitelů se zástupnými znaky v naslouchacího procesu](#wildcard-host-names-in-listener-preview).

:::image type="content" source="./media/multiple-site-overview/multisite.png" alt-text="Application Gateway více lokalit":::

> [!IMPORTANT]
> Pravidla se zpracovávají v pořadí, v jakém jsou uvedena na portálu pro SKU v1. V případě SKU verze V2 mají přesné shody vyšší prioritu. Důrazně doporučujeme nakonfigurovat naslouchací procesy pro více webů před konfigurací základního naslouchacího procesu.  Tím se zajistí směrování provozu do správného back-endu. Pokud je základní naslouchací proces uveden jako první a odpovídá příchozímu požadavku, požadavek se zpracuje tímto naslouchacím procesem.

Žádosti na adresu `http://contoso.com` se směrují na ContosoServerPool a žádosti na adresu `http://fabrikam.com` na FabrikamServerPool.

Podobně můžete hostovat více subdomén stejné nadřazené domény ve stejném nasazení služby Application Gateway. Můžete například hostovat `http://blog.contoso.com` a `http://app.contoso.com` v jediném nasazení aplikační brány.

## <a name="wildcard-host-names-in-listener-preview"></a>Názvy hostitelů se zástupnými znaky v naslouchací službě (Preview)

Application Gateway umožňuje směrování na hostiteli pomocí naslouchacího procesu HTTP (S) více lokalit. Nyní máte možnost použít v názvu hostitele zástupné znaky, jako je hvězdička (*) a otazník (?), a až 5 názvů hostitelů pro naslouchací proces HTTP (S) více lokalit. Například, `*.contoso.com`.

Pomocí zástupného znaku v názvu hostitele můžete vyhledat více názvů hostitelů v rámci jednoho naslouchacího procesu. `*.contoso.com`Můžete například porovnat s, stejně `ecom.contoso.com` `b2b.contoso.com` jako `customer1.b2b.contoso.com` a tak dále. Pomocí pole názvů hostitelů můžete pro naslouchací proces nakonfigurovat více než jeden název hostitele, aby bylo možné směrovat požadavky do back-endu fondu. Naslouchací proces může například obsahovat, `contoso.com, fabrikam.com` který bude přijímat žádosti pro názvy hostitelů.

:::image type="content" source="./media/multiple-site-overview/wildcard-listener-diag.png" alt-text="Naslouchací proces se zástupnými znaky":::

>[!NOTE]
> Tato funkce je ve verzi Preview a je dostupná jenom pro Standard_v2 a WAF_v2 SKU Application Gateway. Další informace o verzi Preview najdete v tématu věnovaném [podmínkám použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

>[!NOTE]
>Tato funkce je aktuálně dostupná jenom prostřednictvím [Azure PowerShell](tutorial-multiple-sites-powershell.md) a [Azure CLI](tutorial-multiple-sites-cli.md). Už brzy bude dostupná podpora portálu.
> Počítejte s tím, že protože podpora portálu není plně dostupná, pokud používáte pouze parametr HostNames, posluchač se zobrazí jako základní naslouchací proces na portálu a sloupec název hostitele v zobrazení seznamu naslouchacího procesu nebude zobrazovat názvy hostitelů, které jsou nakonfigurovány. U všech změn pro naslouchací proces se zástupnými znaky se ujistěte, že používáte Azure PowerShell nebo CLI, dokud není na portálu podporován.

V [Azure PowerShell](tutorial-multiple-sites-powershell.md)je nutné použít `-HostNames` místo `-HostName` . U názvů hostitelů můžete uvést až 5 názvů hostitelů jako hodnot oddělených čárkami a použít zástupné znaky. Například `-HostNames "*.contoso.com,*.fabrikam.com"`.

V rozhraní příkazového [řádku Azure](tutorial-multiple-sites-cli.md)je nutné použít `--host-names` místo `--host-name` . U názvů hostitelů můžete uvést až 5 názvů hostitelů jako hodnot oddělených čárkami a použít zástupné znaky. Například `--host-names "*.contoso.com,*.fabrikam.com"`.

### <a name="allowed-characters-in-the-host-names-field"></a>Povolené znaky v poli názvy hostitelů:

* `(A-Z,a-z,0-9)` – alfanumerické znaky
* `-` – spojovník nebo mínus
* `.` – tečka jako oddělovač
*   `*` – může odpovídat více znakům v povoleném rozsahu.
*   `?` – může odpovídat jednomu znaku v povoleném rozsahu.

### <a name="conditions-for-using-wildcard-characters-and-multiple-host-names-in-a-listener"></a>Podmínky použití zástupných znaků a více názvů hostitelů v naslouchací službě:

*   V jednom naslouchací službě můžete uvést až 5 názvů hostitelů.
*   Hvězdičku `*` lze v rámci názvu doménového stylu nebo názvu hostitele zmínit pouze jednou. Například Component1 *. component2*. component3. `(*.contoso-*.com)` je platný.
*   Název hostitele může obsahovat jenom dvě hvězdičky `*` . Například `*.contoso.*` je platný a `*.contoso.*.*.com` je neplatný.
*   Název hostitele může obsahovat maximálně 4 zástupných znaků. Například `????.contoso.com` , `w??.contoso*.edu.*` jsou platné, ale `????.contoso.*` neplatné.
*   Použití hvězdičky `*` a otazníku `?` společně v součásti názvu hostitele ( `*?` nebo `?*` nebo `**` ) je neplatné. Například `*?.contoso.com` a `**.contoso.com` jsou neplatné.

### <a name="considerations-and-limitations-of-using-wildcard-or-multiple-host-names-in-a-listener"></a>Doporučení a omezení používání zástupných znaků nebo více názvů hostitelů v naslouchací službě:

*   [Ukončení protokolu SSL a kompletní protokol SSL](ssl-overview.md) vyžadují, abyste nakonfigurovali protokol jako https a nahráli certifikát, který se má použít v konfiguraci naslouchacího procesu. Pokud se jedná o naslouchací proces více lokalit, můžete zadat i název hostitele, obvykle se jedná o CN certifikátu SSL. Pokud zadáváte více názvů hostitelů v naslouchacího procesu nebo používáte zástupné znaky, je nutné vzít v úvahu následující skutečnosti:
    *   Pokud se jedná o zástupný název hostitele, jako je *. contoso.com, musíte nahrát certifikát se zástupnými znaky s CN jako *. contoso.com.
    *   Pokud je ve stejném naslouchací službě uveden více názvů hostitelů, je nutné nahrát certifikát sítě SAN (alternativní názvy předmětu) s propojenými názvy, které odpovídají názvům hostitelů uvedeným v tématu.
*   Nemůžete použít regulární výraz k uvedení názvu hostitele. Pro vytvoření vzoru názvu hostitele můžete použít jenom zástupné znaky, jako je hvězdička (*) a otazník (?).
*   U kontroly stavu back-endu nelze pro každé nastavení HTTP přidružit více [vlastních sond](application-gateway-probe-overview.md) . Místo toho můžete testovat jeden z webů v back-endu nebo použít "127.0.0.1" k testování místního hostitele back-end serveru. Pokud však v naslouchací službě používáte zástupný znak nebo více názvů hostitelů, budou požadavky na všechny určené vzorce domény směrovány do fondu back-end v závislosti na typu pravidla (základní nebo založené na cestě).
*   Vlastnosti "hostname" přebírají jako vstup jeden řetězec, kde můžete uvést pouze jeden název domény bez zástupných znaků a "názvy hostitelů" přebírají pole řetězců jako vstup, kde můžete uvést až 5 názvů domén se zástupnými znaky. Současně však nelze současně použít obě vlastnosti.
*   Pomocí cílového naslouchacího procesu, který používá zástupný znak nebo více názvů hostitelů, nelze vytvořit pravidlo [přesměrování](redirect-overview.md) .

Podrobný průvodce konfigurací názvů hostitelů se zástupnými znaky v rámci naslouchacího procesu pro více webů najdete v tématu [vytvoření více lokalit pomocí Azure PowerShell](tutorial-multiple-sites-powershell.md) nebo použití rozhraní příkazového [řádku Azure CLI](tutorial-multiple-sites-cli.md) .

## <a name="host-headers-and-server-name-indication-sni"></a>Hlavičky hostitele a Identifikace názvu serveru (SNI)

Existují tři běžné mechanismy pro povolení hostování více webů ve stejné infrastruktuře.

1. Hostování více webových aplikací, z nichž každá je na jedinečné IP adrese.
2. Použití názvu hostitele k hostování více webových aplikací na stejné IP adrese.
3. Použití různých portů k hostování více webových aplikací na stejné IP adrese.

V současné době Application Gateway podporuje jednu veřejnou IP adresu, kde naslouchá provozu. To znamená, že v současné době není podporována více aplikací, z nichž každá má vlastní IP adresu. 

Application Gateway podporuje několik aplikací, které každé naslouchá na různých portech, ale tento scénář vyžaduje, aby aplikace přijímaly provoz na nestandardních portech. To není často konfigurace, kterou požadujete.

Služba Application Gateway se při hostování více než jednoho webu na stejné veřejné IP adrese a portu spoléhá na hlavičky hostitele HTTP 1.1. Weby hostované v aplikační bráně můžou také podporovat snižování zátěže TLS s rozšířením Indikace názvu serveru (SNI) TLS. Tento scénář znamená, že klientský prohlížeč a back-endová webová farma musí podporovat HTTP/1.1 a rozšíření protokolu TLS, jak je definováno v dokumentu RFC 6066.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak nakonfigurovat hostování více webů v Application Gateway
* [Pomocí webu Azure Portal](create-multiple-sites-portal.md)
* [Použití Azure Powershell](tutorial-multiple-sites-powershell.md) 
* [Použití Azure CLI](tutorial-multiple-sites-cli.md)

Na stránce [Šablona Resource Manageru používající hostování více webů](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting) najdete kompletní nasazení založené na šabloně.
