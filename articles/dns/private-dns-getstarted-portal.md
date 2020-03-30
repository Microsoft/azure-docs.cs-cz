---
title: Úvodní příručka – vytvoření privátní zóny DNS Azure pomocí portálu Azure
description: V tomto rychlém startu vytvoříte a otestujete privátní zónu DNS a záznam v Azure DNS. Toto je podrobný průvodce pro vytvoření a správu první privátní zóny DNS a záznamu pomocí portálu Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/11/2019
ms.author: rohink
ms.openlocfilehash: 52bf9e061eb57c7ce6ea698b7468b5ba5e11b4e8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78244967"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-portal"></a>Úvodní příručka: Vytvoření privátní zóny DNS Azure pomocí portálu Azure

Tento rychlý start vás provede kroky k vytvoření první privátní zóny DNS a záznamu pomocí portálu Azure.

K hostování záznamů DNS pro konkrétní doménu se používá zóna DNS. Pokud chcete začít hostovat svou doménu v DNS Azure, musíte vytvořit zónu DNS pro daný název domény. Všechny záznamy DNS pro vaši doménu se pak vytvoří v této zóně DNS. Když chcete publikovat privátní zónu DNS do virtuální sítě, zadáte seznam virtuálních sítí, které mají povoleno překládat záznamy v rámci této zóny.  Tyto sítě se nazývají *propojené* virtuální sítě. Pokud je povolena automatická registrace, Azure DNS také aktualizuje záznamy zóny při každém vytvoření virtuálního počítače, změní jeho IP adresu nebo se odstraní.

V tomto rychlém startu se naučíte:

> [!div class="checklist"]
> * Vytvoření privátní zóny DNS
> * Vytvoření virtuální sítě
> * Propojení virtuální sítě
> * Vytvoření testovacích virtuálních počítačů
> * Vytvoření dalšího záznamu DNS
> * Testování privátní zóny

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

Pokud chcete, můžete tento rychlý start dokončit pomocí [Azure PowerShell](private-dns-getstarted-powershell.md) nebo [Azure CLI](private-dns-getstarted-cli.md).

## <a name="create-a-private-dns-zone"></a>Vytvoření privátní zóny DNS

Následující příklad vytvoří zónu DNS nazvanou **private.contoso.com** ve skupině prostředků s názvem **MyAzureResourceGroup**.

Zóna DNS obsahuje položky DNS pro doménu. Chcete-li začít hostovat svou doménu ve službě Azure DNS, vytvořte pro tento název domény zónu DNS.

![Vyhledávání zón DNS v soukromých službách](media/private-dns-portal/search-private-dns.png)

1. Na panelu vyhledávání na portálu zadejte do textového pole hledání **soukromé zóny DNS** a stiskněte **Enter**.
1. Vyberte **soukromou zónu DNS**.
2. Vyberte **Možnost Vytvořit soukromou zónu DNS**.

1. Na stránce **Vytvořit soukromou zónu DNS** zadejte nebo vyberte následující hodnoty:

   - **Skupina prostředků**: Vyberte **Vytvořit nový**, zadejte *MyAzureResourceGroup*a vyberte **OK**. Název skupiny prostředků musí být jedinečný v rámci předplatného Azure.
   -  **Název**: V tomto příkladu *zadejte private.contoso.com.*
1. V **umístění skupiny zdrojů**vyberte možnost Střed usa – **západ**.

1. Vyberte **zkontrolovat + vytvořit**.

1. Vyberte **Vytvořit**.

Vytvoření zóny může trvat několik minut.

## <a name="virtual-network-and-parameters"></a>Virtuální síť a parametry

V této části budete muset nahradit následující parametry v krocích s níže uvedenými informacemi:

| Parametr                   | Hodnota                |
|-----------------------------|----------------------|
| **\<>název skupiny prostředků**  | MyAzureResourceGroup (vyberte existující skupinu prostředků) |
| **\<>názvů virtuálních sítí** | MyAzureVNet          |
| **\<>názvu oblasti**          | USA – středozápad      |
| **\<>adresního prostoru IPv4**   | 10.2.0.0\16          |
| **\<>názvu podsítě**          | MyAzureSubnet        |
| **\<>rozsah emitované sítě** | 10.2.0.0\24          |


[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="link-the-virtual-network"></a>Propojení virtuální sítě

Chcete-li propojit privátní zónu DNS s virtuální sítí, vytvořte propojení virtuální sítě.

![Přidání propojení virtuální sítě](media/private-dns-portal/dns-add-virtual-network-link.png)

1. Otevřete skupinu prostředků **MyAzureResourceGroup** a vyberte **private.contoso.com** privátní zónu.
2. V levém podokně vyberte **Propojení virtuálních sítí**.
3. Vyberte **Přidat**.
4. Zadejte název **odkazu** **myLink** .
5. V **případě virtuální sítě**vyberte **myAzureVNet**.
6. Zaškrtněte políčko **Povolit automatickou registraci.**
7. Vyberte **OK**.

## <a name="create-the-test-virtual-machines"></a>Vytvoření testovacích virtuálních počítačů

Teď vytvořte dva virtuální počítače, abyste mohli privátní zónu DNS otestovat:

1. Na levé horní stránce **portálu**vyberte Vytvořit prostředek a pak vyberte **Windows Server 2016 Datacenter**.
1. Vyberte **MyAzureResourceGroup** pro skupinu prostředků.
1. Zadejte **myVM01** - pro název virtuálního počítače.
1. Vyberte **západní střední USA** pro **oblast**.
1. Zadejte jméno uživatelského jména správce.
2. Zadejte heslo a potvrďte heslo.
5. V **části Veřejné příchozí porty**vyberte **Povolit vybrané porty**a potom vyberte **příkaz RDP (3389)** pro **možnost Vybrat příchozí porty**.
10. Přijměte další výchozí hodnoty stránky a klepněte na tlačítko **Další: Disky >**.
11. Přijměte výchozí hodnoty na stránce **Disky a** klepněte na tlačítko **Další: Síťové >**.
1. Ujistěte se, že je pro virtuální síť vybraná moje **AzureVNet.**
1. Přijměte další výchozí hodnoty stránky a klikněte na **Další: Správa >**.
2. V **části Diagnostika spuštění**vyberte Možnost **Vypnuto**, přijměte ostatní výchozí hodnoty a pak vyberte **Možnost Revize + vytvoření**.
1. Zkontrolujte nastavení a klepněte na tlačítko **Vytvořit**.

Opakujte tyto kroky a vytvořte jiný virtuální počítač s názvem **myVM02**.

Dokončení obou virtuálních počítačů bude trvat několik minut.

## <a name="create-an-additional-dns-record"></a>Vytvoření dalšího záznamu DNS

 Následující příklad vytvoří záznam s relativním názvem **db** v **zóně**DNS private.contoso.com ve skupině prostředků **MyAzureResourceGroup**. Plně kvalifikovaný název sady záznamů je **db.private.contoso.com**. Typ záznamu je "A", s IP adresou **myVM01**.

1. Otevřete skupinu prostředků **MyAzureResourceGroup** a vyberte **private.contoso.com** privátní zónu.
2. Vyberte **+ Sada záznamů**.
3. Do **pole Název**zadejte **db**.
4. Do **pole IP adresa**zadejte adresu IP, která se zobrazí pro **myVM01**. To by mělo být automaticky registrováno při spuštění virtuálního počítače.
5. Vyberte **OK**.

## <a name="test-the-private-zone"></a>Testování privátní zóny

Nyní můžete otestovat překlad názvů pro **private.contoso.com** soukromé zóny.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Konfigurace virtuálních počítačů pro povolení příchozích přenosů ICMP

Překlad adres můžete otestovat pomocí příkazu ping. Za tím účelem nakonfigurujte bránu firewall na obou virtuálních počítačích tak, aby povolovala příchozí pakety ICMP.

1. Připojte se k počítači myVM01 a otevřete okno Windows PowerShellu s oprávněními správce.
2. Spusťte následující příkaz:

   ```powershell
   New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
   ```

Totéž zopakujte pro virtuální počítač myVM02.

### <a name="ping-the-vms-by-name"></a>Odeslání příkazu ping na virtuální počítače podle názvu

1. Z příkazového řádku ve Windows PowerShellu virtuálního počítače myVM02 odešlete příkaz ping do virtuálního počítače myVM01 a použijte v něm automaticky zaregistrovaný název hostitele:
   ```
   ping myVM01.private.contoso.com
   ```
   Zobrazený výstup by měl vypadat zhruba takto:
   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```
2. Teď odešlete příkaz ping na název **db**, který jste předtím vytvořili:
   ```
   ping db.private.contoso.com
   ```
   Zobrazený výstup by měl vypadat zhruba takto:
   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Odstranění všech prostředků

Pokud už není potřeba, odstraňte skupinu prostředků **MyAzureResourceGroup** a odstraňte prostředky vytvořené v tomto rychlém startu.


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Scénáře pro Azure DNS Private Zones](private-dns-scenarios.md)

