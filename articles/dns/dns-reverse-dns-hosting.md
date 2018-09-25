---
title: Hostování zón reverzního vyhledávání DNS v Azure DNS | Dokumentace Microsoftu
description: Zjistěte, jak používat Azure DNS k hostování zón reverzního vyhledávání DNS pro vaši rozsahy IP adres
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: victorh
ms.openlocfilehash: e45b3bde0d5077a5d18369236e81bcd467527940
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990140"
---
# <a name="host-reverse-dns-lookup-zones-in-azure-dns"></a>Hostitel zón reverzního vyhledávání DNS v Azure DNS

Tento článek vysvětluje, jak k hostování zón reverzního vyhledávání DNS pro vaši přiřazené rozsahy IP adres v Azure DNS. Rozsahy IP adres, který je reprezentován zóny zpětného vyhledávání musí přiřazené pro vaši organizaci, obvykle podle svého poskytovatele internetových služeb.

Nakonfigurovat reverzních záznamů DNS pro Azure vlastní IP adresu, která je přiřazená ke službě Azure, najdete v části [konfigurovat zpětné vyhledávání DNS pro služby hostované v Azure](dns-reverse-dns-for-azure-services.md).

Předtím, než se pustíte do čtení tohoto článku, měli byste se seznámit s [přehled reverzní DNS a podporu v Azure](dns-reverse-dns-overview.md).

Tento článek vás provede kroky k vytvoření první zóny zpětného vyhledávání DNS a záznamu pomocí webu Azure portal, Azure Powershellu, příkazového řádku Azure classic nebo Azure CLI.

## <a name="create-a-reverse-lookup-dns-zone"></a>Vytvoření zóny zpětného vyhledávání DNS

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na **centra** nabídce vyberte možnost **nový** > **sítě**a pak vyberte **zónu DNS**.

   ![Výběr "Zóny DNS"](./media/dns-reverse-dns-hosting/figure1.png)

1. V **vytvořit zónu DNS** podokně pojmenujte zónu DNS. Název zóny je vytvořený jinak než u předpony IPv4 a IPv6. Postupujte podle pokynů pro [IPv4](#ipv4) nebo [IPv6](#ipv6) název vaší zóny můžete zobrazit. Jakmile budete hotovi, vyberte **vytvořit** k vytvoření zóny.

### <a name="ipv4"></a>IPv4

Název zóně zpětného vyhledávání IPv4 je založené na rozsahu IP, který představuje. By měla být v následujícím formátu: `<IPv4 network prefix in reverse order>.in-addr.arpa`. Příklady najdete v tématu [přehled reverzní DNS a podporu v Azure](dns-reverse-dns-overview.md#ipv4).

> [!NOTE]
> Při vytváření classless zón reverzního vyhledávání DNS v Azure DNS, je nutné použít pomlčkou (`-`) namísto lomítkem (`/`) v názvu zóny.
>
> Například pro rozsah 192.0.2.128/26 IP, je nutné použít `128-26.2.0.192.in-addr.arpa` jako název zóny místo `128/26.2.0.192.in-addr.arpa`.
>
> I když standardy DNS podporovat obě metody, Azure DNS nepodporuje názvy zón DNS, které obsahují lomítka (`/`) znaků.

Následující příklad ukazuje postup vytvoření třídy C reverzní zóny DNS s názvem `2.0.192.in-addr.arpa` v DNS Azure pomocí webu Azure portal:

 !["Vytvořit zónu DNS" podokno s vyplněno pole](./media/dns-reverse-dns-hosting/figure2.png)

**Umístění skupiny prostředků** definuje umístění pro skupinu prostředků. Nemá žádný vliv na zónu DNS. Umístění zóny DNS je vždy globální a nezobrazuje se.

Následující příklady ukazují, jak tento úkol provést pomocí prostředí Azure PowerShell a rozhraní příkazového řádku Azure.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsZone -Name 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klasické rozhraní příkazového řádku Azure

```azurecli
azure network dns zone create MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns zone create -g MyResourceGroup -n 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

Název zóny zpětného vyhledávání IPv6 by měl být ve tvaru: `<IPv6 network prefix in reverse order>.ip6.arpa`.  Příklady najdete v tématu [přehled reverzní DNS a podporu v Azure](dns-reverse-dns-overview.md#ipv6).


Následující příklad ukazuje, jak vytvořit IPv6 zpětného vyhledávání zónu DNS s názvem `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` v DNS Azure pomocí webu Azure portal:

 !["Vytvořit zónu DNS" podokno s vyplněno pole](./media/dns-reverse-dns-hosting/figure3.png)

**Umístění skupiny prostředků** definuje umístění pro skupinu prostředků. Nemá žádný vliv na zónu DNS. Umístění zóny DNS je vždy globální a nezobrazuje se.

Následující příklady ukazují, jak tento úkol provést pomocí prostředí Azure PowerShell a rozhraní příkazového řádku Azure.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsZone -Name 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klasické rozhraní příkazového řádku Azure

```azurecli
azure network dns zone create MyResourceGroup 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns zone create -g MyResourceGroup -n 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="delegate-a-reverse-dns-lookup-zone"></a>Delegování zóny zpětného vyhledávání DNS

Teď, když vytvoříte zónu zpětného vyhledávání DNS, musíte zajistit, že je zóna delegovaná z nadřazené zóny. Delegování DNS umožňuje proces překladu DNS k najde názvové servery, které hostují zónu zpětného vyhledávání DNS. Tyto názvové servery pak dokáže odpovědět zpětné dotazy DNS pro IP adresy ve vaší rozsah adres.

Pro zóny dopředného vyhledávání, je popsán proces delegování zóny DNS v [delegování domény do Azure DNS](dns-delegate-domain-azure-dns.md). Delegování zón zpětného vyhledávání funguje stejným způsobem. Jediným rozdílem je, že budete muset nakonfigurovat názvové servery, které se u poskytovatele internetových služeb, který poskytuje rozsahu IP adres, nikoli registrátora názvu domény.

## <a name="create-a-dns-ptr-record"></a>Vytvořit záznam DNS PTR

### <a name="ipv4"></a>IPv4

Následující příklad vás provede procesem vytvoření záznamů PTR v zpětné zóny DNS v Azure DNS. Informace o dalších typech záznamů a úpravě existujících záznamů najdete v tématu [Správa záznamů a sad záznamů DNS pomocí webu Azure Portal](dns-operations-recordsets-portal.md).

1. V horní části **zónu DNS** vyberte **+ sada záznamů** otevřít **přidat sadu záznamů** podokně.

   ![Tlačítko pro vytvoření sady záznamů](./media/dns-reverse-dns-hosting/figure4.png)

1. Název sady pro záznam PTR záznamů musí být zbývající část adresy IPv4 v obráceném pořadí. 

   V tomto příkladu jsou první tři oktety již předvyplněny jako součást názvu zóny (.2.0.192). Proto pouze poslední oktet není zadána ve **název** pole. Můžete například pojmenovat sadu záznamů **15** pro určitý prostředek, jehož IP adresa je 192.0.2.15.  
1. Pro **typ**vyberte **PTR**.  
1. Pro **název domény**, zadejte plně kvalifikovaný název domény (FQDN) prostředku, který používá IP adresu.
1. Vyberte **OK** v dolní části podokna DNS vytvořit záznam.

 !["Přidat sadu záznamů" podokno s vyplněno pole](./media/dns-reverse-dns-hosting/figure5.png)

Následující příklady ukazují, jak tento úkol provést pomocí Powershellu nebo rozhraní příkazového řádku Azure.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsRecordSet -Name 15 -RecordType PTR -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "dc1.contoso.com")
```
#### <a name="azure-classic-cli"></a>Klasické rozhraní příkazového řádku Azure

```azurecli
azure network dns record-set add-record MyResourceGroup 2.0.192.in-addr.arpa 15 PTR --ptrdname dc1.contoso.com  
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 2.0.192.in-addr.arpa -n 15 --ptrdname dc1.contoso.com
```

### <a name="ipv6"></a>IPv6

Následující příklad vás provede procesem vytvoření nových záznamů PTR. Informace o dalších typech záznamů a úpravě existujících záznamů najdete v tématu [Správa záznamů a sad záznamů DNS pomocí webu Azure Portal](dns-operations-recordsets-portal.md).

1. V horní části **zónu DNS** vyberte **+ sada záznamů** otevřít **přidat sadu záznamů** podokně.

   ![Tlačítko pro vytvoření sady záznamů](./media/dns-reverse-dns-hosting/figure6.png)

2. Název sady pro záznam PTR záznamů musí být zbývající část adresy IPv6 v obráceném pořadí. Nesmí obsahovat žádné nulové komprese. 

   V tomto příkladu jsou již předvyplněny prvních 64 bitů IPv6 jako součást názvu zóny (0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa). Proto je dodávána pouze 64 bitů **název** pole. Poslední 64 bitů IP adresy byly zadány v obráceném pořadí pomocí tečku jako oddělovač mezi každou šestnáctkové číslo. Můžete například pojmenovat sadu záznamů **e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f** pro určitý prostředek, jehož IP adresa je 2001:0db8:abdc:0000:f524:10bc:1af9:405e.  
3. Pro **typ**vyberte **PTR**.  
4. Pro **název domény**, zadejte plně kvalifikovaný název domény, který používá IP adresu prostředku.
5. Vyberte **OK** v dolní části podokna DNS vytvořit záznam.

!["Přidat sadu záznamů" podokno s vyplněno pole](./media/dns-reverse-dns-hosting/figure7.png)

Následující příklady ukazují, jak tento úkol provést pomocí Powershellu nebo rozhraní příkazového řádku Azure.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsRecordSet -Name "e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f" -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "dc2.contoso.com")
```

#### <a name="azure-classic-cli"></a>Klasické rozhraní příkazového řádku Azure

```
azure network dns record-set add-record MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azure-cli"></a>Azure CLI

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>Zobrazení záznamů

Chcete-li zobrazit záznamy, které jste vytvořili, přejděte na svoji zónu DNS na webu Azure Portal. V dolní části **zónu DNS** podokně se zobrazí záznamy pro zónu DNS. Měli byste vidět výchozí NS a SOA záznamy a nové záznamy, které jste vytvořili. Záznamy NS a SOA se vytvoří v každé zóně. 

### <a name="ipv4"></a>IPv4

**Zónu DNS** podokně se zobrazí záznamů IPv4 PTR:

![Podokno "Zóny DNS" se záznamy IPv4](./media/dns-reverse-dns-hosting/figure8.png)

Následující příklady znázorňují způsob zobrazení záznamů PTR pomocí Powershellu nebo rozhraní příkazového řádku Azure.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmDnsRecordSet -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klasické rozhraní příkazového řádku Azure

```azurecli
    azure network dns record-set list MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
    azure network dns record-set list -g MyResourceGroup -z 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

**Zónu DNS** podokně se zobrazí záznamů IPv6 PTR:

![Podokno "Zóny DNS" se záznamy IPv6](./media/dns-reverse-dns-hosting/figure9.png)

Následující příklady znázorňují způsob zobrazení záznamů pomocí Powershellu nebo rozhraní příkazového řádku Azure.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmDnsRecordSet -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klasické rozhraní příkazového řádku Azure

```azurecli
    azure network dns record-set list MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
    azure network dns record-set list -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="faq"></a>Nejčastější dotazy

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Můžete hostovat zón reverzního vyhledávání DNS pro moje bloky IP přiřazené poskytovatele internetových služeb v Azure DNS?

Ano. Hostování zón reverzního vyhledávání (ARPA) pro vlastní rozsahy IP adres v Azure DNS se plně podporuje.

V Azure DNS vytvoříte zónu zpětného vyhledávání, jak je popsáno v tomto článku a poté pracovat u svého poskytovatele [delegování zóny](dns-domain-delegation.md). Potom můžete spravovat záznamů PTR pro každý zpětného vyhledávání stejným způsobem jako ostatní typy záznamů.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>Jak moc se hostování Moje zpětná náklady vyhledávání zóny DNS?

Hostování zóny zpětného vyhledávání DNS pro vašeho poskytovatele internetových služeb přiřazené IP bloku v Azure DNS se k účtování nepovoleného [standardní sazby Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Můžete hostování zón reverzního vyhledávání DNS pro adresy IPv4 a IPv6 ve službě Azure DNS?

Ano. Tento článek vysvětluje, jak vytvořit IPv4 a IPv6 zón reverzního vyhledávání DNS v Azure DNS.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>Můžete importovat existující zónu zpětného vyhledávání DNS?

Ano. Rozhraní příkazového řádku Azure můžete použít k importu existujícími zónami DNS do Azure DNS. Tato metoda se dá použít pro zóny dopředného vyhledávání a zóny zpětného vyhledávání.

Další informace najdete v tématu [Import a export souboru zóny DNS pomocí Azure CLI](dns-import-export.md).

## <a name="next-steps"></a>Další postup

Další informace o reverzních záznamů DNS najdete v tématu [zpětného vyhledávání DNS v encyklopedii Wikipedia](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Zjistěte, jak [Správa reverzních záznamů DNS pro služby Azure](dns-reverse-dns-for-azure-services.md).
