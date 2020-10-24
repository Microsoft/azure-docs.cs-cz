---
title: Hostitel reverzních zón zpětného vyhledávání DNS v Azure DNS
description: Naučte se používat Azure DNS k hostování zón zpětného vyhledávání DNS pro rozsahy IP adres.
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 6a0aebc727233cdd838f3e1bf8eeb5cd247b9836
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489672"
---
# <a name="host-reverse-dns-lookup-zones-in-azure-dns"></a>Hostitel reverzních zón zpětného vyhledávání DNS v Azure DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Tento článek vysvětluje, jak hostovat zóny zpětného vyhledávání DNS pro přiřazené rozsahy IP adres v Azure DNS. Rozsahy IP adres reprezentované zónami zpětného vyhledávání musí být přiřazené vaší organizaci, obvykle prostřednictvím poskytovatele internetových služeb.

Informace o konfiguraci reverzního DNS pro IP adresu ve vlastnictví Azure, která je přiřazená službě Azure, najdete v tématu [Konfigurace reverzních DNS pro služby hostované v Azure](dns-reverse-dns-for-azure-services.md).

Před čtením tohoto článku byste měli být obeznámeni s [přehledem reverzních DNS a podpory v Azure](dns-reverse-dns-overview.md).

Tento článek vás provede kroky k vytvoření první zóny a záznamu DNS zpětného vyhledávání pomocí Azure Portal, Azure PowerShell, rozhraní příkazového řádku Azure Classic nebo rozhraní příkazového řádku Azure CLI.

## <a name="create-a-reverse-lookup-dns-zone"></a>Vytvoření zóny DNS zpětného vyhledávání

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V nabídce **centra** vyberte **Nový**  >  **síť**a pak vyberte **zóna DNS**.

   ![Výběr zóny DNS](./media/dns-reverse-dns-hosting/figure1.png)

1. V podokně **vytvořit ZÓNU DNS** pojmenujte zónu DNS. Název zóny je pro předpony IPv4 a IPv6 vytvořený jinak. Pro pojmenování zóny použijte pokyny pro [IPv4](#ipv4) nebo [IPv6](#ipv6) . Až budete hotovi, vyberte **vytvořit** a vytvořte zónu.

### <a name="ipv4"></a>IPv4

Název zóny zpětného vyhledávání IPv4 je založený na rozsahu IP adres, který představuje. Měl by být v následujícím formátu: `<IPv4 network prefix in reverse order>.in-addr.arpa` . Příklady najdete v tématu [Přehled reverzních DNS a podpory v Azure](dns-reverse-dns-overview.md#ipv4).

> [!NOTE]
> Při vytváření reverzních zón zpětného vyhledávání DNS bez třídy v Azure DNS musíte použít spojovník ( `-` ) místo lomítka ( `/` ) v názvu zóny.
>
> Například pro rozsah IP adres 192.0.2.128/26 musíte `128-26.2.0.192.in-addr.arpa` jako název zóny použít místo `128/26.2.0.192.in-addr.arpa` .
>
> I když standardy DNS podporují obě metody, Azure DNS nepodporuje názvy zón DNS, které obsahují znak lomítka ( `/` ).

Následující příklad ukazuje, jak vytvořit reverzní zónu DNS třídy C s názvem `2.0.192.in-addr.arpa` v Azure DNS prostřednictvím Azure Portal:

 ![Snímek obrazovky, který ukazuje, jak vytvořit reverzní zónu DNS třídy C s názvem 2.0.192.in-addr. arpa v Azure DNS prostřednictvím Azure Portal.](./media/dns-reverse-dns-hosting/figure2.png)

**Umístění skupiny prostředků** definuje umístění pro skupinu prostředků. Nemá žádný vliv na zónu DNS. Umístění zóny DNS je vždy globální a nezobrazuje se.

Následující příklady ukazují, jak tuto úlohu dokončit pomocí Azure PowerShell a Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsZone -Name 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Azure Classic CLI

```azurecli
azure network dns zone create MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns zone create -g MyResourceGroup -n 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

Název zóny zpětného vyhledávání IPv6 by měl být v následujícím tvaru: `<IPv6 network prefix in reverse order>.ip6.arpa` .  Příklady najdete v tématu [Přehled reverzních DNS a podpory v Azure](dns-reverse-dns-overview.md#ipv6).


Následující příklad ukazuje, jak vytvořit zónu zpětného vyhledávání DNS s reverzním protokolem IPv6 s názvem `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` v Azure DNS prostřednictvím Azure Portal:

 ![Podokno vytvořit zónu DNS s vyplněnými poli](./media/dns-reverse-dns-hosting/figure3.png)

**Umístění skupiny prostředků** definuje umístění pro skupinu prostředků. Nemá žádný vliv na zónu DNS. Umístění zóny DNS je vždy globální a nezobrazuje se.

Následující příklady ukazují, jak tuto úlohu dokončit pomocí Azure PowerShell a Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsZone -Name 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Azure Classic CLI

```azurecli
azure network dns zone create MyResourceGroup 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns zone create -g MyResourceGroup -n 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="delegate-a-reverse-dns-lookup-zone"></a>Delegování zóny zpětného vyhledávání DNS

Teď, když jste vytvořili zónu zpětného vyhledávání DNS, musíte zajistit, aby byla zóna delegovaná z nadřazené zóny. Delegování DNS umožňuje procesu překladu názvů DNS najít názvové servery, které jsou hostiteli vaší zóny zpětného vyhledávání DNS. Tyto názvové servery pak můžou odpovědět na zpětné dotazy DNS na IP adresy v rozsahu adres.

V případě zón dopředného vyhledávání je proces delegování zóny DNS popsaný v tématu [delegování vaší domény na Azure DNS](dns-delegate-domain-azure-dns.md). Delegování pro zóny zpětného vyhledávání funguje stejným způsobem. Jediným rozdílem je, že je třeba nakonfigurovat názvové servery pomocí poskytovatele internetových služeb, který je zadaný jako rozsah IP adres, a ne podle registrátora názvu domény.

## <a name="create-a-dns-ptr-record"></a>Vytvoření záznamu DNS PTR

### <a name="ipv4"></a>IPv4

Následující příklad vás provede procesem vytvoření záznamu PTR v zóně reverzního DNS v Azure DNS. Informace o dalších typech záznamů a úpravě existujících záznamů najdete v tématu [Správa záznamů a sad záznamů DNS pomocí webu Azure Portal](dns-operations-recordsets-portal.md).

1. V horní části podokna **zóna DNS** vyberte **+ Sada záznamů** a otevřete podokno **Přidat sadu záznamů** .

   ![Snímek obrazovky s podoknem zóny DNS se šipkou ukazující na tlačítku + sada záznamů](./media/dns-reverse-dns-hosting/figure4.png)

1. Název sady záznamů pro záznam PTR musí být zbytek adresy IPv4 v opačném pořadí. 

   V tomto příkladu jsou již první tři oktety vyplněny jako součást názvu zóny (. 2.0.192). Proto je do pole **název** zadán pouze poslední oktet. Například můžete pojmenovat sadu záznamů **15** pro prostředek, jehož IP adresa je 192.0.2.15.  
1. Jako **typ**vyberte **PTR**.  
1. Jako **název domény**zadejte plně kvalifikovaný název domény (FQDN) prostředku, který používá IP adresu.
1. V dolní části podokna vyberte **OK** a vytvořte záznam DNS.

   ![Podokno přidat sadu záznamů s vyplněnými poli](./media/dns-reverse-dns-hosting/figure5.png)

Následující příklady ukazují, jak tuto úlohu dokončit pomocí PowerShellu nebo rozhraní příkazového řádku Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsRecordSet -Name 15 -RecordType PTR -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "dc1.contoso.com")
```
#### <a name="azure-classic-cli"></a>Azure Classic CLI

```azurecli
azure network dns record-set add-record MyResourceGroup 2.0.192.in-addr.arpa 15 PTR --ptrdname dc1.contoso.com  
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set ptr add-record -g MyResourceGroup -z 2.0.192.in-addr.arpa -n 15 --ptrdname dc1.contoso.com
```

### <a name="ipv6"></a>IPv6

Následující příklad vás provede procesem vytvoření nového záznamu PTR. Informace o dalších typech záznamů a úpravě existujících záznamů najdete v tématu [Správa záznamů a sad záznamů DNS pomocí webu Azure Portal](dns-operations-recordsets-portal.md).

1. V horní části podokna **zóna DNS** vyberte **+ Sada záznamů** a otevřete podokno **Přidat sadu záznamů** .

   ![Tlačítko pro vytvoření sady záznamů](./media/dns-reverse-dns-hosting/figure6.png)

2. Název sady záznamů pro záznam PTR musí být zbytek adresy IPv6 v opačném pořadí. Nesmí obsahovat žádnou kompresi s nulovou hodnotou. 

   V tomto příkladu jsou již první 64 bitů protokolu IPv6 vyplněny jako součást názvu zóny (0.0.0.0. c. d. b. a. 8. b. d. 0.1.0.0.2. ip6. arpa). V poli **název** proto jsou zadány pouze poslední 64 bitů. Poslední 64 bity IP adresy se zadávají v opačném pořadí, jako oddělovač mezi každým hexadecimálním číslem. Můžete například pojmenovat sadu záznamů **e. 5.0.4.9. f. a. 1. c. b. 0.1.4.2.5. f** pro prostředek, jehož IP adresa je 2001:0db8: ABDC: 0000: f524:10bc: 1af9:405e.  
3. Jako **typ**vyberte **PTR**.  
4. Jako **název domény**zadejte plně kvalifikovaný název domény prostředku, který používá IP adresu.
5. V dolní části podokna vyberte **OK** a vytvořte záznam DNS.

![Snímek obrazovky, který zobrazuje podokno přidat sadu záznamů s šipkou ukazující na hodnotu v poli Typ](./media/dns-reverse-dns-hosting/figure7.png)

Následující příklady ukazují, jak tuto úlohu dokončit pomocí PowerShellu nebo rozhraní příkazového řádku Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsRecordSet -Name "e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f" -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "dc2.contoso.com")
```

#### <a name="azure-classic-cli"></a>Azure Classic CLI

```azurecli
azure network dns record-set add-record MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set ptr add-record -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>Zobrazení záznamů

Chcete-li zobrazit záznamy, které jste vytvořili, přejděte do zóny DNS v Azure Portal. V dolní části podokna **zóna DNS** vidíte záznamy pro zónu DNS. Měli byste vidět výchozí záznamy NS a SOA spolu s případnými novými záznamy, které jste vytvořili. Záznamy NS a SOA se vytvářejí v každé zóně. 

### <a name="ipv4"></a>IPv4

V podokně **zóna DNS** se zobrazují záznamy protokolu IPv4 PTR:

![Podokno DNS zóna s záznamy IPv4](./media/dns-reverse-dns-hosting/figure8.png)

Následující příklady ukazují, jak zobrazit záznamy PTR pomocí prostředí PowerShell nebo rozhraní příkazového řádku Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzDnsRecordSet -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Azure Classic CLI

```azurecli
azure network dns record-set list MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set list -g MyResourceGroup -z 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

V podokně **zóna DNS** se zobrazují záznamy protokolu IPv6 PTR:

![Podokno DNS zóna s záznamy IPv6](./media/dns-reverse-dns-hosting/figure9.png)

Následující příklady ukazují, jak zobrazit záznamy pomocí prostředí PowerShell nebo rozhraní příkazového řádku Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzDnsRecordSet -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Azure Classic CLI

```azurecli
azure network dns record-set list MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set list -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="faq"></a>Nejčastější dotazy

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Můžu na Azure DNS hostovat zóny zpětného vyhledávání DNS pro své bloky IP adres přiřazené poskytovateli internetových služeb?

Ano. Hostování zón zpětného vyhledávání (ARPA) pro vlastní rozsahy IP adres v Azure DNS je plně podporované.

Vytvořte zónu zpětného vyhledávání v Azure DNS, jak je vysvětleno v tomto článku, a potom Spolupracujte se svým poskytovatelem služeb při [delegování zóny](dns-domain-delegation.md). Pak můžete spravovat záznamy PTR pro každé zpětné vyhledávání stejným způsobem jako jiné typy záznamů.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>Kolik stojí hostitelé reverzních zón zpětného vyhledávání DNS?

Hostování zóny reverzního vyhledávání DNS pro váš blok IP adres přiřazený poskytovatelem internetových služeb v Azure DNS se účtuje podle [standardních Azure DNS sazeb](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Můžu v Azure DNS hostovat zóny zpětného vyhledávání DNS pro adresy IPv4 i IPv6?

Ano. Tento článek vysvětluje, jak vytvořit zóny zpětného vyhledávání IPv4 a IPv6 v Azure DNS.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>Můžu importovat existující zónu zpětného vyhledávání DNS?

Ano. Pomocí Azure CLI můžete importovat existující zóny DNS do Azure DNS. Tato metoda se používá pro zóny dopředného vyhledávání a zóny zpětného vyhledávání.

Další informace najdete v tématu [Import a export souboru zóny DNS pomocí rozhraní příkazového řádku Azure CLI](dns-import-export.md).

## <a name="next-steps"></a>Další kroky

Další informace o reverzních DNS najdete v tématu [reverzní DNS Lookup v Wikipedii](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Naučte se [Spravovat reverzní záznamy DNS pro služby Azure](dns-reverse-dns-for-azure-services.md).
