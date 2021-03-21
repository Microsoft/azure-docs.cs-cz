---
title: Rychlý Start – vytvoření privátní zóny DNS Azure pomocí Azure Portal
description: V tomto rychlém startu vytvoříte a otestujete privátní zónu a záznam DNS v Azure DNS. Toto je podrobný Průvodce vytvořením a správou první privátní zóny DNS a záznamu pomocí Azure Portal.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: rohink
ms.openlocfilehash: d298dfd5f3ad0beb56a511c124bab056ca25fd27
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92310052"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-portal"></a>Rychlý Start: Vytvoření privátní zóny DNS Azure pomocí Azure Portal

Tento rychlý Start vás provede kroky k vytvoření první privátní zóny DNS a záznamu pomocí Azure Portal.

K hostování záznamů DNS pro konkrétní doménu se používá zóna DNS. Pokud chcete začít hostovat svou doménu v DNS Azure, musíte vytvořit zónu DNS pro daný název domény. Všechny záznamy DNS pro vaši doménu se pak vytvoří v této zóně DNS. Když chcete publikovat privátní zónu DNS do vaší virtuální sítě, zadáte seznam virtuálních sítí, které mají povolené překládání záznamů v rámci zóny.  Ty se nazývají *propojené* virtuální sítě. Pokud je povolena automatická registrace, Azure DNS aktualizuje také záznamy zón při každém vytvoření virtuálního počítače, změní jeho IP adresu nebo se odstraní.

## <a name="prerequisites"></a>Předpoklady

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Pokud budete chtít, můžete tento rychlý Start dokončit pomocí [Azure PowerShell](private-dns-getstarted-powershell.md) nebo [Azure CLI](private-dns-getstarted-cli.md).

## <a name="create-a-private-dns-zone"></a>Vytvoření privátní zóny DNS

Následující příklad vytvoří zónu DNS s názvem **Private.contoso.com** ve skupině prostředků s názvem **MyAzureResourceGroup**.

Zóna DNS obsahuje záznamy DNS pro doménu. Pokud chcete začít hostovat vaši doménu v Azure DNS, vytvořte pro tento název domény zónu DNS.

![Hledání zón Privátní DNS](media/private-dns-portal/search-private-dns.png)

1. Na panelu hledání na portálu zadejte do textového pole Hledat **privátní zóny DNS** a stiskněte klávesu **ENTER**.
1. Vyberte **zónu privátní DNS**.
2. Vyberte **vytvořit privátní zónu DNS**.

1. Na stránce **vytvořit privátní DNS zónu** zadejte nebo vyberte následující hodnoty:

   - **Skupina prostředků**: vyberte **vytvořit novou**, zadejte *MyAzureResourceGroup* a vyberte **OK**. Název skupiny prostředků musí být v rámci předplatného Azure jedinečný.
   -  **Název**: v tomto příkladu zadejte *Private.contoso.com* .
1. V případě **umístění skupiny prostředků** vyberte **středozápadní USA**.

1. Vyberte **Zkontrolovat a vytvořit**.

1. Vyberte **Vytvořit**.

Vytvoření zóny může trvat několik minut.

## <a name="virtual-network-and-parameters"></a>Virtuální síť a parametry

V této části budete muset v krocích níže nahradit následující parametry:

| Parametr                   | Hodnota                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | MyAzureResourceGroup (vyberte existující skupinu prostředků) |
| **\<virtual-network-name>** | MyAzureVNet          |
| **\<region-name>**          | USA – středozápad      |
| **\<IPv4-address-space>**   | 10.2.0.0 \ 16          |
| **\<subnet-name>**          | MyAzureSubnet        |
| **\<subnet-address-range>** | 10.2.0.0 \ 24          |


[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="link-the-virtual-network"></a>Propojit virtuální síť

Když chcete propojit privátní zónu DNS s virtuální sítí, vytvoříte propojení virtuální sítě.

![Přidat odkaz virtuální sítě](media/private-dns-portal/dns-add-virtual-network-link.png)

1. Otevřete skupinu prostředků **MyAzureResourceGroup** a vyberte privátní zónu **Private.contoso.com** .
2. V levém podokně vyberte **odkazy virtuální sítě**.
3. Vyberte **Přidat**.
4. Jako **název odkazu** zadejte **myLink** .
5. V případě **virtuální sítě** vyberte **myAzureVNet**.
6. Zaškrtněte políčko **Povolit automatickou registraci** .
7. Vyberte **OK**.

## <a name="create-the-test-virtual-machines"></a>Vytvoření testovacích virtuálních počítačů

Teď vytvořte dva virtuální počítače, abyste mohli privátní zónu DNS otestovat:

1. Na stránce portálu nahoře vlevo vyberte **vytvořit prostředek** a pak vyberte **Windows Server 2016 Datacenter**.
1. Jako skupinu prostředků vyberte **MyAzureResourceGroup** .
1. Jako název virtuálního počítače zadejte **myVM01** .
1. Pro **oblast** vyberte **středozápadní USA** .
1. Zadejte název uživatelského jména správce.
2. Zadejte heslo a potvrďte heslo.
5. U **veřejných příchozích portů** vyberte **Povolit vybrané porty** a pak pro **vybrat příchozí porty** vybrat **RDP (3389)** .
10. Přijměte ostatní výchozí hodnoty stránky a potom klikněte na **Další: disky >**.
11. Na stránce **disky** přijměte výchozí hodnoty a pak klikněte na **další: síťové >**.
1. Ujistěte se, že pro virtuální síť je vybraná možnost **myAzureVNet** .
1. Přijměte ostatní výchozí hodnoty stránky a potom klikněte na **Další: >správy**.
2. Pro **diagnostiku spouštění** vyberte **vypnuto**, Přijměte ostatní výchozí nastavení a pak vyberte **zkontrolovat + vytvořit**.
1. Zkontrolujte nastavení a klikněte na **vytvořit**.

Opakujte tyto kroky a vytvořte další virtuální počítač s názvem **myVM02**.

Dokončení obou virtuálních počítačů bude trvat několik minut.

## <a name="create-an-additional-dns-record"></a>Vytvoření dalšího záznamu DNS

 Následující příklad vytvoří záznam s relativní názvem **DB** v zóně DNS **Private.contoso.com** ve skupině prostředků **MyAzureResourceGroup**. Plně kvalifikovaný název sady záznamů je **DB.Private.contoso.com**. Typ záznamu je A s IP adresou **myVM01**.

1. Otevřete skupinu prostředků **MyAzureResourceGroup** a vyberte privátní zónu **Private.contoso.com** .
2. Vyberte **+ Sada záznamů**.
3. Jako **název** zadejte **DB**.
4. Do pole **IP adresa** zadejte IP adresu, kterou vidíte pro **myVM01**. Tato volba by měla být automaticky registrována při spuštění virtuálního počítače.
5. Vyberte **OK**.

## <a name="test-the-private-zone"></a>Testování privátní zóny

Teď můžete testovat překlad IP adres pro privátní zónu **Private.contoso.com** .

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
   Zobrazený výstup by měl vypadat nějak takto:
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
   Zobrazený výstup by měl vypadat nějak takto:
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

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků **MyAzureResourceGroup** a odstraňte prostředky vytvořené v rámci tohoto rychlého startu.


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Scénáře privátních zón Azure DNS](private-dns-scenarios.md)

