---
title: Hostitelské zóny zpětného vyhledávání DNS v Azure DNS
description: Přečtěte si, jak používat Azure DNS k hostování reverzních vyhledávacích zón DNS pro rozsahy IP adres.
author: rohinkoul
ms.service: dns
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 78fc3428274be5e1998abe9189bea996f15e278c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454257"
---
# <a name="host-reverse-dns-lookup-zones-in-azure-dns"></a>Hostitelské zóny zpětného vyhledávání DNS v Azure DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Tento článek vysvětluje, jak hostovat reverzní zóny vyhledávání DNS pro přiřazené rozsahy IP adres v Azure DNS. Rozsahy IP adres reprezentované zónami zpětného vyhledávání musí být přiřazeny vaší organizaci, obvykle vaším poskytovateli.

Pokud chcete nakonfigurovat reverzní DNS pro IP adresu vlastněnou Azure, která je přiřazená k vaší službě Azure, přečtěte si informace [o konfiguraci reverzního DNS pro služby hostované v Azure](dns-reverse-dns-for-azure-services.md).

Než si přečtete tento článek, měli byste být obeznámeni s [přehledem reverzní DNS a podporu v Azure](dns-reverse-dns-overview.md).

Tento článek vás provede kroky k vytvoření první zóny DNS zpětného vyhledávání a záznamu pomocí portálu Azure, Azure PowerShell, Azure classic CLI nebo Azure CLI.

## <a name="create-a-reverse-lookup-dns-zone"></a>Vytvoření zóny DNS zpětného vyhledávání

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V nabídce **Hub** vyberte **Nová** > **síť**a pak vyberte **zónu DNS**.

   ![Výběr "Zóny DNS"](./media/dns-reverse-dns-hosting/figure1.png)

1. V podokně **Vytvořit zónu DNS** pojmenujte zónu DNS. Název zóny je vytvořen odlišně pro předpony IPv4 a IPv6. Použijte pokyny pro [IPv4](#ipv4) nebo [IPv6](#ipv6) k pojmenování zóny. Až budete hotovi, vyberte **Vytvořit,** chcete-li vytvořit zónu.

### <a name="ipv4"></a>IPv4

Název zóny zpětného vyhledávání IPv4 je založen na rozsahu IP adres, který představuje. Mělo by být v `<IPv4 network prefix in reverse order>.in-addr.arpa`následujícím formátu: . Příklady najdete [v tématu Přehled reverzní DNS a podpora v Azure](dns-reverse-dns-overview.md#ipv4).

> [!NOTE]
> Při vytváření beztřídních reverzních vyhledávacích zón DNS v Azure DNS`-`je nutné v`/`názvu zóny použít místo lomítka ( ).
>
> Například pro rozsah IP 192.0.2.128/26 je `128-26.2.0.192.in-addr.arpa` nutné použít jako `128/26.2.0.192.in-addr.arpa`název zóny namísto .
>
> Přestože standardy DNS podporují obě metody, Azure DNS nepodporuje názvy zón DNS, které obsahují znak lomítka (`/`).

Následující příklad ukazuje, jak vytvořit reverzní zónu DNS třídy C pojmenovanou `2.0.192.in-addr.arpa` v Azure DNS prostřednictvím portálu Azure:

 ![Podokno "Vytvořit zónu DNS" s vyplněnými poli](./media/dns-reverse-dns-hosting/figure2.png)

**Umístění skupiny prostředků** definuje umístění pro skupinu prostředků. Nemá žádný vliv na zónu DNS. Umístění zóny DNS je vždy globální a nezobrazuje se.

Následující příklady ukazují, jak tento úkol dokončit pomocí Azure PowerShellu a Azure CLI.

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

Název zóny zpětného vyhledávání IPv6 by měl být `<IPv6 network prefix in reverse order>.ip6.arpa`v následujícím tvaru: .  Příklady najdete [v tématu Přehled reverzní DNS a podpora v Azure](dns-reverse-dns-overview.md#ipv6).


Následující příklad ukazuje, jak vytvořit zónu zpětného vyhledávání `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` DNS IPv6 pojmenovanou v Azure DNS prostřednictvím webu Azure Portal:

 ![Podokno "Vytvořit zónu DNS" s vyplněnými poli](./media/dns-reverse-dns-hosting/figure3.png)

**Umístění skupiny prostředků** definuje umístění pro skupinu prostředků. Nemá žádný vliv na zónu DNS. Umístění zóny DNS je vždy globální a nezobrazuje se.

Následující příklady ukazují, jak tento úkol dokončit pomocí Azure PowerShellu a Azure CLI.

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

Nyní, když jste vytvořili zónu zpětného vyhledávání DNS, musíte zajistit, aby zóna byla delegována z nadřazené zóny. Delegování DNS umožňuje procesu překladu názvů DNS najít názvové servery, které jsou hostiteli reverzní zóny vyhledávání DNS. Tyto názvové servery pak mohou odpovídat na reverzní dotazy DNS pro adresy IP v rozsahu adres.

U zón vyhledávání vpřed je proces delegování zóny DNS popsán v části [Delegování domény na Azure DNS](dns-delegate-domain-azure-dns.md). Delegování pro zóny zpětného vyhledávání funguje stejným způsobem. Jediným rozdílem je, že je třeba nakonfigurovat názvové servery s isp, který za předpokladu, rozsah IP, spíše než vaše doménové jméno registrátora.

## <a name="create-a-dns-ptr-record"></a>Vytvoření záznamu PTR DNS

### <a name="ipv4"></a>IPv4

Následující příklad vás provede procesem vytváření záznamu PTR v reverzní zóně DNS v Azure DNS. Informace o dalších typech záznamů a úpravě existujících záznamů najdete v tématu [Správa záznamů a sad záznamů DNS pomocí webu Azure Portal](dns-operations-recordsets-portal.md).

1. V horní části podokna **zóny DNS** vyberte + **Record set,** chcete-li otevřít podokno **Přidat sadu záznamů.**

   ![Tlačítko pro vytvoření sady záznamů](./media/dns-reverse-dns-hosting/figure4.png)

1. Název záznamu nastaveného pro záznam PTR musí být zbytek adresy IPv4 v opačném pořadí. 

   V tomto příkladu jsou první tři oktety již naplněny jako součást názvu zóny (.2.0.192). Proto je do pole **Název** dodáván pouze poslední oktet. Můžete například pojmenovat sadu záznamů **15** pro prostředek, jehož ADRESA IP je 192.0.2.15.  
1. V **pole Typ**vyberte možnost **PTR**.  
1. Do **pole DOMAIN NAME**zadejte plně kvalifikovaný název domény (Plně kvalifikovaný název domény) prostředku, který používá ip adresu.
1. **Chcete-li** vytvořit záznam DNS, vyberte v dolní části podokna ok.

   ![Podokno "Přidat sadu záznamů" s vyplněnými rámečky](./media/dns-reverse-dns-hosting/figure5.png)

Následující příklady ukazují, jak dokončit tento úkol pomocí PowerShellu nebo Azure CLI.

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

Následující příklad vás provede procesem vytváření nového záznamu PTR. Informace o dalších typech záznamů a úpravě existujících záznamů najdete v tématu [Správa záznamů a sad záznamů DNS pomocí webu Azure Portal](dns-operations-recordsets-portal.md).

1. V horní části podokna **zóny DNS** vyberte + **Record set,** chcete-li otevřít podokno **Přidat sadu záznamů.**

   ![Tlačítko pro vytvoření sady záznamů](./media/dns-reverse-dns-hosting/figure6.png)

2. Název záznamu nastaveného pro záznam PTR musí být zbytek adresy IPv6 v opačném pořadí. Nesmí obsahovat žádnou nulovou kompresi. 

   V tomto příkladu je již vyplněno prvních 64 bitů Protokolu IPv6 jako součást názvu zóny (0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa). Proto jsou do pole **Název** dodávány pouze posledních 64 bitů. Posledních 64 bitů adresy IP je zadáno v opačném pořadí s tečkou jako oddělovač mezi jednotlivými šestnáctkovým číslem. Můžete například pojmenovat sadu záznamů **e.5.0.4.9.a.1.c.b.0.1.4.2.5.f** pro prostředek, jehož IP adresa je 2001:0db8:abdc:0000:f524:10bc:1af9:405e.  
3. V **pole Typ**vyberte možnost **PTR**.  
4. Do **pole NÁZEV DOMÉNY**zadejte hlavní název domény prostředku, který používá ip adresu.
5. **Chcete-li** vytvořit záznam DNS, vyberte v dolní části podokna ok.

![Podokno "Přidat sadu záznamů" s vyplněnými rámečky](./media/dns-reverse-dns-hosting/figure7.png)

Následující příklady ukazují, jak dokončit tento úkol pomocí PowerShellu nebo Azure CLI.

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

Pokud chcete zobrazit záznamy, které jste vytvořili, přejděte do zóny DNS na webu Azure Portal. V dolní části podokna **zóny DNS** se zobrazí záznamy zóny DNS. Měli byste vidět výchozí záznamy NS a SOA a všechny nové záznamy, které jste vytvořili. Záznamy NS a SOA jsou vytvářeny v každé zóně. 

### <a name="ipv4"></a>IPv4

Podokno **zóny DNS** zobrazuje záznamy PTR IPv4:

![Podokno Zóna DNS se záznamy IPv4](./media/dns-reverse-dns-hosting/figure8.png)

Následující příklady ukazují, jak zobrazit záznamy PTR pomocí PowerShellu nebo Azure CLI.

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

Podokno **zóny DNS** zobrazuje záznamy PTR IPv6:

![Podokno Zóna DNS se záznamy IPv6](./media/dns-reverse-dns-hosting/figure9.png)

Následující příklady ukazují, jak zobrazit záznamy pomocí PowerShellu nebo Azure CLI.

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

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Můžu v Azure DNS hostovat reverzní zóny vyhledávání DNS pro své ip bloky přiřazené k isp?

Ano. Hostování zón zpětného vyhledávání (ARPA) pro vaše vlastní rozsahy IP adres v Azure DNS je plně podporováno.

Vytvořte zónu zpětného vyhledávání v Azure DNS, jak je vysvětleno v tomto článku, a pak spolupracujte s poskytovatelem služeb IsP na [delegování zóny](dns-domain-delegation.md). Potom můžete spravovat záznamy PTR pro každé zpětné vyhledávání stejným způsobem jako ostatní typy záznamů.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>Kolik stojí hostování v zóně zpětného vyhledávání DNS?

Hostování zóny zpětného vyhledávání DNS pro váš blok IP přiřazený poskytovateli služeb Internet AP v Azure DNS se účtuje [standardnísazby Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Můžu v Azure DNS hostovat reverzní zóny vyhledávání DNS pro adresy IPv4 i IPv6?

Ano. Tento článek vysvětluje, jak vytvořit reverzní zóny vyhledávání DNS IPv4 a IPv6 ve službě Azure DNS.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>Mohu importovat existující zónu zpětného vyhledávání DNS?

Ano. Azure CLI můžete použít k importu existujících zón DNS do Azure DNS. Tato metoda funguje jak pro zóny dopředné vyhledávání, tak pro zóny zpětného vyhledávání.

Další informace najdete [v tématu Import a export souboru zóny DNS pomocí azure cli](dns-import-export.md).

## <a name="next-steps"></a>Další kroky

Další informace o reverzním DNS naleznete v [tématu reverzní vyhledávání DNS na Wikipedii](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Přečtěte si, jak [spravovat reverzní záznamy DNS pro vaše služby Azure](dns-reverse-dns-for-azure-services.md).
