---
title: Hostování zpětné zóny DNS vyhledávání v Azure DNS | Microsoft Docs
description: Naučte se používat Azure DNS pro hostování zpětné vyhledávání zóny DNS pro vaše rozsahy IP adres
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: kumud
ms.openlocfilehash: c5d53838ffb77ff8d33fee9d10aea4a74f3c523c
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
ms.locfileid: "32773676"
---
# <a name="host-reverse-dns-lookup-zones-in-azure-dns"></a>Hostitele zpětné vyhledávání zóny DNS v Azure DNS

Tento článek vysvětluje, jak k hostování zpětné vyhledávání zóny DNS pro vaše přiřazené rozsahy IP v Azure DNS. Rozsahy IP reprezentována zóny zpětného vyhledávání musí přiřazené pro vaši organizaci, obvykle podle vašeho poskytovatele internetových služeb.

Konfigurace zpětné DNS pro Azure vlastní IP adresu, která je přiřazena k službě Azure najdete v tématu [konfigurovat reverse pro služby hostované v Azure DNS](dns-reverse-dns-for-azure-services.md).

Předtím, než se pustíte do čtení tohoto článku, měli byste se seznámit s [přehled zpětné DNS a podpory v Azure](dns-reverse-dns-overview.md).

Tento článek vás provede kroky k vytvoření vaší první zóny zpětného vyhledávání DNS a záznamů pomocí portálu Azure, Azure PowerShell, Azure CLI 1.0 nebo 2.0 rozhraní příkazového řádku Azure.

## <a name="create-a-reverse-lookup-dns-zone"></a>Vytvořit zónu zpětného vyhledávání DNS

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na **rozbočovače** nabídce vyberte možnost **nový** > **sítě**a potom vyberte **zónu DNS**.

   ![Výběr "Zóny DNS"](./media/dns-reverse-dns-hosting/figure1.png)

1. V **zóny DNS vytvořte** podokně název zóny DNS. Název zóny je jinak vytvořených pro předpony IPv4 a IPv6. Postupujte podle pokynů pro [IPv4](#ipv4) nebo [IPv6](#ipv6) na název zóny. Jakmile budete hotovi, vyberte **vytvořit** k vytvoření zóny.

### <a name="ipv4"></a>IPv4

Název zóny zpětného vyhledávání IPv4 vychází z rozsahu IP, která reprezentuje. Musí být v následujícím formátu: `<IPv4 network prefix in reverse order>.in-addr.arpa`. Příklady najdete v tématu [přehled zpětné DNS a podpory v Azure](dns-reverse-dns-overview.md#ipv4).

> [!NOTE]
> Pokud vytváříte classless zóny zpětného vyhledávání DNS v Azure DNS, je nutné použít pomlčka (`-`) namísto lomítkem (`/`) v názvu zóny.
>
> Například pro 192.0.2.128/26 rozsah IP, musíte použít `128-26.2.0.192.in-addr.arpa` jako název zóny místo `128/26.2.0.192.in-addr.arpa`.
>
> I když standardech DNS podporovat obě metody, Azure DNS nepodporuje názvy zón DNS, které obsahují lomítkem (`/`) znaků.

Následující příklad ukazuje postup vytvoření třídy C zpětné zóny DNS s názvem `2.0.192.in-addr.arpa` v Azure DNS prostřednictvím portálu Azure:

 ![Vyplněno "Vytvoření zóny DNS" podokně polí](./media/dns-reverse-dns-hosting/figure2.png)

**Umístění skupiny prostředků** definuje umístění pro skupinu prostředků. Nemá žádný vliv na zónu DNS. Umístění zóny DNS je vždy globální a nezobrazuje se.

Následující příklady ukazují, jak tuto úlohu dokončit pomocí prostředí Azure PowerShell a rozhraní příkazového řádku Azure.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsZone -Name 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
azure network dns zone create MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
az network dns zone create -g MyResourceGroup -n 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

Název zóny zpětného vyhledávání IPv6 musí být v následující podobě: `<IPv6 network prefix in reverse order>.ip6.arpa`.  Příklady najdete v tématu [přehled zpětné DNS a podpory v Azure](dns-reverse-dns-overview.md#ipv6).


Následující příklad ukazuje, jak vytvořit IPv6 zpětného vyhledávání zónu DNS s názvem `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` v Azure DNS prostřednictvím portálu Azure:

 ![Vyplněno "Vytvoření zóny DNS" podokně polí](./media/dns-reverse-dns-hosting/figure3.png)

**Umístění skupiny prostředků** definuje umístění pro skupinu prostředků. Nemá žádný vliv na zónu DNS. Umístění zóny DNS je vždy globální a nezobrazuje se.

Následující příklady ukazují, jak tuto úlohu dokončit pomocí prostředí Azure PowerShell a rozhraní příkazového řádku Azure.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsZone -Name 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
azure network dns zone create MyResourceGroup 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
az network dns zone create -g MyResourceGroup -n 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="delegate-a-reverse-dns-lookup-zone"></a>Delegování zóny zpětného vyhledávání DNS

Teď, když jste vytvořili zóny zpětného vyhledávání DNS, je nutné zajistit, že je zóna delegovaná z nadřazené zóně. Delegování DNS umožňuje proces překladu názvů DNS pro najde názvové servery, které hostují zónu zpětného vyhledávání DNS. Tyto servery název potom může odpovědět zpětné dotazy DNS pro IP adresy ve vaší rozsah adres.

Pro zóny dopředného vyhledávání, je popsán proces delegování zóny DNS v [delegování domény do Azure DNS](dns-delegate-domain-azure-dns.md). Delegování zón zpětného vyhledávání funguje stejným způsobem. Jediným rozdílem je, že budete muset nakonfigurovat názvové servery s poskytovatele internetových služeb, které poskytuje rozsahu IP adres, nikoli vaším registrátorem názvu domény.

## <a name="create-a-dns-ptr-record"></a>Vytvořit záznam DNS PTR

### <a name="ipv4"></a>IPv4

Následující příklad vás provede procesem vytvoření záznam PTR v zpětné zóny DNS v Azure DNS. Informace o dalších typech záznamů a úpravě existujících záznamů najdete v tématu [Správa záznamů a sad záznamů DNS pomocí webu Azure Portal](dns-operations-recordsets-portal.md).

1. V horní části **zónu DNS** podokně, vyberte **+ sady záznamů** otevřete **přidat sadu záznamů** podokně.

   ![Tlačítko pro vytvoření sady záznamů](./media/dns-reverse-dns-hosting/figure4.png)

1. Název sady pro záznam PTR záznamů musí být zbytek adresy IPv4 v obráceném pořadí. 

   V tomto příkladu jsou první tři oktety už vyplněné jako součást název zóny (.2.0.192). Proto je zadán pouze poslední oktet v **název** pole. Může být například název vaší sady záznamů **15** pro prostředek, jehož IP adresa je 192.0.2.15.  
1. Pro **typ**, vyberte **PTR**.  
1. Pro **název domény**, zadejte plně kvalifikovaný název domény (FQDN) prostředku, který používá IP adresu.
1. Vyberte **OK** v dolní části podokna DNS vytvořit záznam.

 !["Přidat sadu záznamů" podokně vyplněno pole](./media/dns-reverse-dns-hosting/figure5.png)

Následující příklady ukazují, jak tuto úlohu dokončit pomocí prostředí PowerShell nebo rozhraní příkazového řádku Azure.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsRecordSet -Name 15 -RecordType PTR -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "dc1.contoso.com")
```
#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
azure network dns record-set add-record MyResourceGroup 2.0.192.in-addr.arpa 15 PTR --ptrdname dc1.contoso.com  
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 2.0.192.in-addr.arpa -n 15 --ptrdname dc1.contoso.com
```

### <a name="ipv6"></a>IPv6

Následující příklad vás provede procesem vytvoření nové záznam PTR. Informace o dalších typech záznamů a úpravě existujících záznamů najdete v tématu [Správa záznamů a sad záznamů DNS pomocí webu Azure Portal](dns-operations-recordsets-portal.md).

1. V horní části **zónu DNS** podokně, vyberte **+ sady záznamů** otevřete **přidat sadu záznamů** podokně.

   ![Tlačítko pro vytvoření sady záznamů](./media/dns-reverse-dns-hosting/figure6.png)

2. Název sady pro záznam PTR záznamů musí být zbytek adresu IPv6 v obráceném pořadí. Nesmí obsahovat nula komprese. 

   V tomto příkladu jsou už vyplněné prvních 64 bitů IPv6 v rámci název zóny (0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa). Proto jsou pouze 64bitová verze zadaná v **název** pole. Posledních 64 bitů IP adresy se zadávají v obráceném pořadí s dobou jako oddělovač mezi každou hexadecimální číslo. Může být například název vaší sady záznamů **e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f** pro prostředek, jehož IP adresa je 2001:0db8:abdc:0000:f524:10bc:1af9:405e.  
3. Pro **typ**, vyberte **PTR**.  
4. Pro **název domény**, zadejte plně kvalifikovaný název domény prostředku, který používá IP adresu.
5. Vyberte **OK** v dolní části podokna DNS vytvořit záznam.

!["Přidat sadu záznamů" podokně vyplněno pole](./media/dns-reverse-dns-hosting/figure7.png)

Následující příklady ukazují, jak tuto úlohu dokončit pomocí prostředí PowerShell nebo rozhraní příkazového řádku Azure.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsRecordSet -Name "e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f" -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "dc2.contoso.com")
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```
azure network dns record-set add-record MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>Zobrazení záznamů

Pokud chcete zobrazit záznamy, které jste vytvořili, přejděte do zóny DNS na portálu Azure. V dolní části **zónu DNS** podokně, zobrazí se záznamy pro zónu DNS. Měli byste vidět výchozí NS a SOA, záznamy a všechny nové záznamy, které jste vytvořili. V každé zóny jsou vytvořit záznamy NS a SOA. 

### <a name="ipv4"></a>IPv4

**Zónu DNS** podokně se zobrazují záznamů IPv4 PTR:

!["Zóny DNS" podokně se záznamy IPv4](./media/dns-reverse-dns-hosting/figure8.png)

Následující příklady ukazují, jak pomocí prostředí PowerShell nebo rozhraní příkazového řádku Azure zobrazení záznamů PTR.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmDnsRecordSet -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
    azure network dns record-set list MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
    azure network dns record-set list -g MyResourceGroup -z 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

**Zónu DNS** podokně se zobrazují záznamů IPv6 PTR:

!["Zóny DNS" podokně se záznamy IPv6](./media/dns-reverse-dns-hosting/figure9.png)

Následující příklady ukazují, jak zobrazit záznamy pomocí prostředí PowerShell nebo rozhraní příkazového řádku Azure.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmDnsRecordSet -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
    azure network dns record-set list MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
    azure network dns record-set list -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="faq"></a>Nejčastější dotazy

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Může hostovat zóny zpětného vyhledávání DNS pro moje bloky IP přiřazené poskytovatele internetových služeb ve službě Azure DNS?

Ano. Hostování zóny zpětného vyhledávání (ARPA) pro vlastní rozsahy IP v Azure DNS je plně podporována.

Vytvoření zóny zpětného vyhledávání v Azure DNS, jak je popsáno v tomto článku a potom začít u svého poskytovatele [delegování zóny](dns-domain-delegation.md). Pak můžete spravovat záznamů PTR pro každý zpětného vyhledávání stejným způsobem jako jiné typy záznamů.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>Kolik nemá hostování Moje zpětné zóny náklady vyhledávání DNS?

Hostování zóny zpětného vyhledávání DNS pro blok vašeho poskytovatele internetových služeb přiřadit IP v Azure DNS je účtován na [standardních sazeb Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Může hostovat zpětné zóny vyhledávání DNS pro adresu IPv4 i IPv6 adresu v Azure DNS?

Ano. Tento článek vysvětluje, jak vytvořit oba protokoly IPv4 a IPv6 zpětné zóny vyhledávání DNS v Azure DNS.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>Můžete importovat existující zóně zpětného vyhledávání DNS?

Ano. Rozhraní příkazového řádku Azure můžete importovat existující zóny DNS do Azure DNS. Tato metoda funguje pro zóny dopředného vyhledávání a zóny zpětného vyhledávání.

Další informace najdete v tématu [Import a export souboru zóny DNS pomocí rozhraní příkazového řádku Azure](dns-import-export.md).

## <a name="next-steps"></a>Další postup

Další informace o zpětné DNS najdete v tématu [zpětného vyhledávání DNS na webu Wikipedia](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Zjistěte, jak [zpětné záznamy DNS pro služeb Azure spravovat](dns-reverse-dns-for-azure-services.md).
