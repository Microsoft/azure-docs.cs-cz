---
title: 'Kurz: provádění zachycení paketů v připojeních typu Site-to-Site z Azure Virtual WAN'
description: V tomto kurzu se dozvíte, jak provést zachytávání paketů na virtuální síti WAN na pracovišti VPN Gateway.
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 04/13/2021
ms.author: wellee
Customer intent: As someone with a networking background using Virtual WAN, I want to perform a packet capture on my Site-to-site VPN Gateway.
ms.openlocfilehash: dbe7e06484797063ed4122ee3fdde625dc66c41f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107879143"
---
# <a name="perform-packet-capture-on-the-azure-virtual-wan-site-to-site-vpn-gateway"></a>Zachytávání paketů na virtuální síti WAN Azure na pracovišti VPN Gateway 

Problémy související s připojením a výkonem jsou často složité. Může trvat poměrně dlouhou dobu a úsilí, abyste mohli zúžit příčinu problému. Zachytávání paketů vám může přispět k zúžení rozsahu problému na určité části sítě. Může vám to usnadnit určení toho, jestli se problém nachází na straně zákazníka sítě, na straně Azure v síti nebo někde mezi. Po zúžení problému je efektivnější ladit a provádět nápravné akce.

Následující článek popisuje, jak spustit a zastavit zachytávání paketů na webu Azure Virtual WAN typu Site-to-Site VPN Gateway. V současné době je tato funkce dostupná jenom prostřednictvím PowerShellu.

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít kroky v tomto článku, musíte mít ve svém prostředí již nastavenou následující konfiguraci:

* Virtuální síť WAN, virtuální rozbočovač a VPN Gateway typu Site-to-site nasazené ve virtuálním centru. Můžete mít také připojení připojující sítě VPN k vašemu VPN Gateway site-to-site.


### <a name="working-with-azure-powershell"></a>Práce s Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="set-up-the-environment"></a>Nastavení prostředí

Spuštěním následujícího skriptu v PowerShellu zkontrolujte, že jste v pravém kontextu předplatného. Tím zajistíte, že jste se přihlásili k uživateli, který má oprávnění k zachytávání paketů na VPN Gateway typu Site-to-site.

   ```azurepowershell-interactive
    $subid = “<insert Virtual WAN subscription ID here>”
    Set-AzContext -SubscriptionId $subid
   ```

## <a name="create-a-storage-account"></a><a name="createstorage"></a> Vytvoření účtu úložiště

V rámci vašeho předplatného Azure musíte vytvořit účet úložiště, abyste mohli ukládat výsledky zachytávání paketů. Informace o tom, jak vytvořit účet úložiště, najdete v tomto [dokumentu](.././storage/common/storage-account-create.md) .

Po vytvoření účtu spusťte následující příkazy, aby se vygenerovala adresa URL sdíleného přístupového podpisu (SAS). Výsledky zachytávání paketů budou uloženy prostřednictvím této adresy URL.
   ```azurepowershell-interactive
  $rgname = “<resource group name containing storage account>” 
$storeName = “<name of storage account> “
$containerName = “<name of container you want to store packet capture in>
$key = Get-AzStorageAccountKey -ResourceGroupName $rgname -Name $storeNAme
$context = New-AzStorageContext -StorageAccountName  $storeName -StorageAccountKey $key[0].value
New-AzStorageContainer -Name $containerName -Context $context
$container = Get-AzStorageContainer -Name $containerName  -Context $context
$now = get-date
$sasurl = New-AzureStorageContainerSASToken -Name $containerName -Context $context -Permission "rwd" -StartTime $now.AddHours(-1) -ExpiryTime $now.AddDays(1) -FullUri
   ```

## <a name="start-the-packet-capture"></a>Spustit zachytávání paketů

Chcete-li spustit zachytávání paketů, máte dvě možnosti zachytávání paketů v jednom připojení k síti VPN nebo v celém VPN Gateway typu Site-to-site. Všimněte si, že pokud se rozhodnete provést zachytávání připojení k síti VPN, můžete provádět pouze zachycení na 6 připojení.

### <a name="packet-capture-on-a-site-to-site-vpn-gateway-all-connections"></a>Zachytávání paketů na VPN Gateway typu Site-to-Site (všechna připojení)

Spusťte prosím následující příkazy. Název VPN Gateway site-to-site najdete tak, že přejdete k virtuálnímu rozbočovači a v části připojení kliknete na síť VPN (site-to-site).

:::image type="content" source="./media/virtual-wan-pcap-screenshots/vpn-gateway-name.png" alt-text="Obrázek názvu brány virtuální sítě WAN" lightbox="./media/virtual-wan-pcap-screenshots/vpn-gateway-name.png":::

   ```azurepowershell-interactive
Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>" -Sasurl $sasurl
   ```

### <a name="packet-capture-on-specific-site-to-site-vpn-connections"></a>Zachytávání paketů pro konkrétní připojení VPN typu Site-to-site

>[!NOTE]
> Upozorňujeme, že můžete spustit zachytávání paketů jenom na 5 připojeních VPN současně.


Spusťte prosím následující příkazy. Název připojení VPN typu Site-to-site najdete tak, že přejdete k virtuálnímu rozbočovači a v části připojení kliknete na síť VPN (site-to-site). Pak přejděte na web VPN, na kterém chcete zachytávání paketů provést, a potom klikněte na tři tečky na pravé straně. V nabídce, která se zobrazí, klikněte na **Upravit připojení k síti VPN** .

:::image type="content" source="./media/virtual-wan-pcap-screenshots/sample-connection.png" alt-text="Obrázek, jak najít názvy připojení k síti VPN." lightbox="./media/virtual-wan-pcap-screenshots/sample-connection.png":::

Název odkazů připojených ke konkrétní lokalitě sítě VPN najdete kliknutím na lokalitu VPN v předchozí části a prohlédnutím do tabulky **odkazy** . 

:::image type="content" source="./media/virtual-wan-pcap-screenshots/link-name-sample.png" alt-text="Obrázek, jak najít název odkazu VPN." lightbox="./media/virtual-wan-pcap-screenshots/link-name-sample.png":::

   ```azurepowershell-interactive
Start-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name "<name of the VPN connection>" -ParentResourceName “<name of the Gateway>” -LinkConnection “<comma separated list of links eg. "link1,link2">” -Sasurl $sasurl 
   ```

## <a name="optional-specifying-filters"></a>Volitelné: určení filtrů

K dispozici jsou některé běžně dostupné nástroje pro zachytávání paketů. Získání relevantních zachycení paketů pomocí těchto nástrojů může být náročné, zejména ve scénářích s vysokým objemem provozu. Pro zjednodušení zachycení paketů můžete určit filtry pro zachytávání paketů a soustředit se na konkrétní chování. Níže jsou uvedené dostupné parametry:

>[!NOTE]
> Pro TracingFlags a TCPFlags můžete zadat více protokolů přidáním číselných hodnot pro protokoly, které chcete zachytit (stejné jako logické nebo). Pokud byste například chtěli zachytit jenom pakety ESP a OPVN, zadali byste TracingFlag hodnotu 8 + 1 = 9.  

| Parametr | Popis | Výchozí hodnoty | Dostupné hodnoty|
|--- |--- | --- | ---|
| TracingFlags | Celé číslo, které určuje typy paketů, které jsou zachyceny | 11 (ESP, IKE, OVPN) | ESP = 1 IKE = 2 OPVN = 8 |
| TCPFlags | Celé číslo, které určuje, které typy paketů TCP jsou zachyceny | 0 (žádné) | FIN = 1, SYN = 2, RST = 4, PSH = 8, ACK = 16, URG = 32, EHK: 64, CWR = 128| 
| MaxPacketBufferSize|Maximální velikost zachyceného paketu v bajtech Pokud je větší než zadaná hodnota, pakety se zkrátí. |120|Všechny|
| MaxFileSize |Maximální velikost souboru zachycení v MB Zachycená data jsou uložená v cyklické vyrovnávací paměti, takže přetečení se zpracovává metodou FIFO (starší odebrané pakety jako první).|100|Všechny|
|SourceSubnets|Pakety ze zadaných rozsahů CIDR jsou zachyceny. Zadáno jako pole.|[] (všechny adresy IPv4)|Pole podsítí IPV4 oddělených čárkou|
| DestinationSubnets |Jsou zachyceny pakety určené pro zadané rozsahy CIDR. Zadáno jako pole. |[] (všechny adresy IPv4)|Pole podsítí IPV4 oddělených čárkou|
|SourcePort |Budou zachyceny pakety se zdrojem v zadaných rozsahech. Zadáno jako pole.|[] (všechny porty)|Pole portů oddělených čárkami|
|DestinationPort |Pakety s cílem v zadaných rozsahech jsou zachyceny. Zadáno jako pole.|[] (všechny porty)|Pole portů oddělených čárkami|
| CaptureSingleDirectionTrafficOnly |Pokud je nastaveno na true, v zachytávání paketů se zobrazí pouze jeden směr obousměrného toku. Tím se zachytí všechny možné kombinované IP adresy a porty.|Ano|True, false|
|Protokol|Pole celých čísel, která odpovídají protokolům IANA. |[] (všechny protokoly)| [Zde](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml) nalezené protokoly |

Níže je příklad zachytávání paketů pomocí řetězce filtru. Parametry můžete změnit tak, aby vyhovovaly vašim potřebám podle předchozí tabulky.  

   ```azurepowershell-interactive
$filter="{`"TracingFlags`":11,`"MaxPacketBufferSize`":120,`"MaxFileSize`":500,`"Filters`":[{`"SourceSubnets`":[`"10.19.0.4/32`",`"10.20.0.4/32`"],`"DestinationSubnets`":[`"10.20.0.4/32`",`"10.19.0.4/32`"],`"TcpFlags`":9,`"CaptureSingleDirectionTrafficOnly`":true}]}"
Start-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name "<name of the VPN connection>" -ParentResourceName “<name of the Gateway>” -LinkConnection “<comma separated list of links>” -Sasurl $sasurl -FilterData $filter

Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>" -Sasurl $sasurl -FilterData $filter
   ```

## <a name="stopping-the-packet-capture"></a>Zastavuje se zachytávání paketů
Doporučuje se, aby se zachytávání paketů spouštělo minimálně 600 sekund. Kvůli problémům s synchronizací mezi několika součástmi na cestě nemusí být zachytávání paketů poskytovat úplná data. Až budete připraveni zastavit zachytávání paketů, spusťte následující příkaz.

Parametry jsou podobné těm, které jsou v části spuštění zachytávání paketů. Adresa URL SAS se vygenerovala v části [Vytvoření účtu úložiště](#createstorage) .

### <a name="gateway-level-packet-capture"></a>Zachytávání paketů na úrovni brány

   ```azurepowershell-interactive
Stop-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name <GatewayName> -SasUrl $sas
   ```

### <a name="connection-level-packet-captures"></a>Zachytávání paketů na úrovni připojení

   ```azurepowershell-interactive
Stop-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name <name of the VPN connection> -ParentResourceName "<name of VPN Gateway>" -LinkConnectionName <comma separated list of links e.g. "link1,link2">-SasUrl $sas
   ```

## <a name="viewing-your-packet-capture"></a>Zobrazení zachytávání paketů

V Azure Portal přejděte na účet úložiště vytvořený v části Vytvoření účtu úložiště, klikněte na svůj kontejner a Stáhněte si soubor. Soubory dat zachycení paketů se generují ve formátu PCAP. K otevření souborů PCAP můžete použít Nástroj Wireshark nebo jinou běžně dostupnou aplikaci.

## <a name="next-steps"></a>Další kroky

Další informace o virtuální síti WAN najdete tady:

> [!div class="nextstepaction"]
> * [Nejčastější dotazy ke službě Virtual WAN](virtual-wan-faq.md)