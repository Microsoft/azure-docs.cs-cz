---
title: Vytvoření privátní zóny DNS pomocí webu Azure portal
description: V tomto kurzu vytvoříte a otestujete privátní zónu a záznam DNS v Azure DNS. Toto je podrobný návod, jak vytvořit a spravovat první privátní zóny DNS a záznamu pomocí webu Azure portal.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 6/15/2019
ms.author: victorh
ms.openlocfilehash: e3772d8f385ed93c96f8aa2f7ee2ded8f46d4e00
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147832"
---
# <a name="tutorial-create-an-azure-dns-private-zone-using-the-azure-portal"></a>Kurz: Vytvoření privátní zóny DNS pomocí webu Azure portal

Tento kurz vás provede kroky k vytvoření první privátní zóny DNS a záznamu pomocí webu Azure portal.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

K hostování záznamů DNS v určité doméně se používá zóna DNS. Pokud chcete začít hostovat svou doménu v DNS Azure, musíte vytvořit zónu DNS pro daný název domény. Všechny záznamy DNS pro vaši doménu se pak vytvoří v této zóně DNS. Když chcete publikovat privátní zónu DNS do virtuální sítě, zadáte seznam virtuálních sítí, které mají povoleno překládat záznamy v rámci této zóny.  Toto nastavení se nazývá *propojené* virtuální sítě. Pokud je povolená Automatická registrace, Azure DNS také aktualizuje záznamy zóny pokaždé, když je vytvořen virtuální počítač, změny jeho "IP adresu, nebo je odstranit.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření privátní zóny DNS
> * Vytvoření virtuální sítě
> * Propojení virtuální sítě
> * Vytvoření testovacích virtuálních počítačů
> * Vytvoření dalšího záznamu DNS
> * Testování privátní zóny

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Pokud dáváte přednost, můžete dokončit tento kurz pomocí [prostředí Azure PowerShell](private-dns-getstarted-powershell.md) nebo [rozhraní příkazového řádku Azure](private-dns-getstarted-cli.md).

## <a name="create-a-dns-private-zone"></a>Vytvoření privátní zóny DNS

Následující příklad vytvoří zónu DNS s názvem **private.contoso.com** ve skupině prostředků s názvem **MyAzureResourceGroup**.

Zóna DNS obsahuje záznamy DNS pro doménu. Pokud chcete začít hostovat svou doménu v Azure DNS, můžete vytvořit zónu DNS pro daný název domény.

![Privátní DNS zón hledání](media/private-dns-portal/search-private-dns.png)

1. Na panelu portálu hledání zadejte **zóny privátního dns** do textového pole pro vyhledávání a stiskněte klávesu **Enter**.
1. Vyberte **zóny privátního DNS**.
2. Vyberte **zóny privátního dns vytvořit**.

1. Na **vytvořit privátní zónu DNS** stránky zadejte nebo vyberte následující hodnoty:

   - **Skupina prostředků**: Vyberte **vytvořit nový**, zadejte *MyAzureResourceGroup*a vyberte **OK**. Název skupiny prostředků musí být jedinečný v rámci předplatného Azure. 
   -  **Název**: Typ *private.contoso.com* pro účely tohoto příkladu.
1. Pro **umístění skupiny prostředků**vyberte **střed USA – západ**.

1. Vyberte **zkontrolovat a vytvořit**.

1. Vyberte **Vytvořit**.

Vytvoření zóny může trvat několik minut.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. V levém horním rohu stránky portálu vyberte **vytvořit prostředek**, pak **sítě**a pak vyberte **virtuální síť**.
2. Pro **název**, typ **myAzureVNet**.
3. Pro **skupiny prostředků**vyberte **MyAzureResourceGroup**.
4. Pro **umístění**vyberte **střed USA – západ**.
5. Přijměte ostatní výchozí hodnoty a vyberte **vytvořit**.

## <a name="link-the-virtual-network"></a>Propojení virtuální sítě

Odkaz privátní zóny DNS na virtuální síť, můžete vytvořit propojení virtuální sítě.

![Přidat odkaz na virtuální síť](media/private-dns-portal/dns-add-virtual-network-link.png)

1. Otevřít **MyAzureResourceGroup** prostředku, skupiny a vyberte **private.contoso.com** privátní zónu.
2. V levém podokně vyberte **propojení virtuální sítě**.
3. Vyberte **Přidat**.
4. Typ **odkaz** pro **název odkazu**.
5. Pro **virtuální síť**vyberte **myAzureVNet**.
6. Vyberte **povolení automatické registrace** zaškrtávací políčko.
7. Vyberte **OK**.

## <a name="create-the-test-virtual-machines"></a>Vytvoření testovacích virtuálních počítačů

Teď vytvořte dva virtuální počítače, abyste mohli privátní zónu DNS otestovat:

1. V levém horním rohu stránky portálu vyberte **vytvořit prostředek**a pak vyberte **systému Windows Server 2016 Datacenter**.
1. Vyberte **MyAzureResourceGroup** pro skupinu prostředků.
1. Typ **myVM01** – název virtuálního počítače.
1. Vyberte **střed USA – západ** pro **oblasti**.
1. Typ **azureadmin** pro uživatelské jméno správce.
2. Typ **Azure12345678** pro heslo a potvrzení hesla.

5. Pro **veřejné příchozí porty**vyberte **povolit vybrané porty**a pak vyberte **protokolu RDP (3389)** pro **vyberte příchozí porty**.
10. Přijměte ostatní výchozí hodnoty na stránce a potom klikněte na tlačítko **Další: Disky >** .
11. Přijměte výchozí hodnoty na **disky** stránce a potom klikněte na **Další: Sítě >** .
1. Ujistěte se, že **myAzureVNet** vybraná virtuální síť.
1. Přijměte ostatní výchozí hodnoty na stránce a potom klikněte na tlačítko **Další: Správa >** .
2. Pro **Diagnostika spouštění**vyberte **vypnout**, přijměte ostatní výchozí hodnoty a pak vyberte **revize + vytvořit**.
1. Zkontrolujte nastavení a potom klikněte na tlačítko **vytvořit**.

Opakováním těchto kroků a vytvořte další virtuální počítač s názvem **myVM02**.

Bude trvat několik minut, než se oba virtuální počítače pro dokončení.

## <a name="create-an-additional-dns-record"></a>Vytvoření dalšího záznamu DNS

 Následující příklad vytvoří záznam s relativním názvem **db** v zóně DNS **private.contoso.com**, ve skupině prostředků **MyAzureResourceGroup**. Plně kvalifikovaný název sady záznamů je **db.private.contoso.com**. Typ záznamu je "A" s IP adresou **myVM01**.

1. Otevřít **MyAzureResourceGroup** prostředku, skupiny a vyberte **private.contoso.com** privátní zónu.
2. Vyberte **+ Sada záznamů**.
3. Pro **název**, typ **db**.
4. Pro **IP adresu**, zadejte IP adresu, se zobrazí pro **myVM01**. To by měl být automaticky zaregistrovali při spuštění virtuálního počítače.
5. Vyberte **OK**.

## <a name="test-the-private-zone"></a>Testování privátní zóny

Teď můžete otestovat překlad názvů pro vaše **private.contoso.com** privátní zónu.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Konfigurace virtuálních počítačů pro povolení příchozích přenosů ICMP

Překlad adres můžete otestovat pomocí příkazu ping. Za tím účelem nakonfigurujte bránu firewall na obou virtuálních počítačích tak, aby povolovala příchozí pakety ICMP.

1. Připojte se k počítači myVM01 a otevřete okno Windows PowerShellu s oprávněními správce.
2. Spusťte následující příkaz:

   ```powershell
   New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
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

Pokud už nejsou potřeba, můžete všechny prostředky vytvořené v rámci tohoto kurzu odstranit odstraněním skupiny prostředků **MyAzureResourceGroup**.


## <a name="next-steps"></a>Další postup

V tomto kurzu jste nasadili privátní zónu DNS, vytvořili záznam DNS nasadila a zónu otestovali.
Teď se můžete o privátních zónách DNS dozvědět podrobnější informace.

> [!div class="nextstepaction"]
> [Použití Azure DNS pro privátní domény](private-dns-overview.md)
