---
title: Windows Virtual Desktop RDP ShortPath (Preview)
titleSuffix: Azure
description: Jak nastavit ShortPath protokolu RDP (Preview) pro virtuální počítač s Windows
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: bfcd8b3c482b4d429a9e3a4d7bc75e27ada63a98
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98134394"
---
# <a name="windows-virtual-desktop-rdp-shortpath-preview"></a>Windows Virtual Desktop RDP ShortPath (Preview)

> [!IMPORTANT]
> Protokol RDP ShortPath je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučujeme ji používat pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

RDP ShortPath je funkce virtuálního klienta Windows, která vytváří přímý přenos založený na UDP mezi klientem vzdálené plochy a hostitelem relace. Protokol RDP používá tento přenos k doručování vzdálené plochy a vzdálené aplikace RemoteApp a zároveň nabízí lepší spolehlivost a konzistentní latenci.

## <a name="key-benefits"></a>Klíčové výhody

* Přenos RDP ShortPath je založený na vysoce efektivním  [protokolu URCP (Universal Rate Control Protocol)](https://www.microsoft.com/en-us/research/publication/urcp-universal-rate-control-protocol-for-real-time-communication-applications/). URCP vylepšuje UDP s aktivním monitorováním podmínek sítě a nabízí spravedlivé a plné využití propojení. URCP pracuje s nízkým zpožděním a úrovněmi ztrát, které vyžaduje Vzdálená plocha. URCP dosahuje nejlepšího výkonu Díky dynamickému učení síťových parametrů a poskytování protokolu pomocí mechanismu řízení sazeb.
* Protokol RDP ShortPath vytváří přímé připojení mezi klientem vzdálené plochy a hostitelem relace. Přímé připojení snižuje závislost na branách virtuálních klientů Windows, zlepšuje spolehlivost připojení a zvyšuje šířku pásma dostupnou pro každou relaci uživatele.
* Odstranění dalšího přenosu zkracuje dobu odezvy, což zlepšuje uživatelské prostředí s aplikacemi citlivými na latenci a vstupními metodami.
* Protokol RDP ShortPath přináší podporu pro [konfiguraci priority QoS (Quality of Service)](./rdp-quality-of-service-qos.md) pro připojení RDP prostřednictvím značek DSCP (diferencované Services Code Point).
* Přenos ShortPath RDP umožňuje [omezit odchozí síťový provoz](./rdp-bandwidth.md#limit-network-bandwidth-use-with-throttle-rate) zadáním míry omezení pro každou relaci.

## <a name="connection-security"></a>Zabezpečení připojení

ShortPath RDP rozšiřuje možnosti vícenásobného přenosu protokolu RDP. Nenahrazuje přenos opačného připojení, ale doplňuje ho. Všechny počáteční zprostředkovatele relací se spravují prostřednictvím infrastruktury virtuálních počítačů s Windows.

Port UDP 3390 se používá jenom pro příchozí provoz ShortPath, který se ověřuje přes přenos přes reverzní připojení. Naslouchací proces RDP ShortPath ignoruje všechny pokusy o připojení k naslouchacímu procesu, pokud se neshodují s relací reverzního připojení.

Protokol RDP ShortPath používá připojení TLS mezi klientem a hostitelem relace pomocí certifikátů hostitele relace. Ve výchozím nastavení se certifikát používaný pro šifrování RDP během nasazení vygeneruje v operačním systému sami. V případě potřeby můžou zákazníci nasazovat centrálně spravované certifikáty vydané certifikační autoritou organizace. Další informace o konfiguracích certifikátů najdete v [dokumentaci k Windows serveru](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations).

## <a name="rdp-shortpath-connection-sequence"></a>Pořadí připojení ShortPath RDP

Po instalaci [přenosu zpětného připojení](./network-connectivity.md)klient a hostitel relace vytvoří připojení RDP a vyjednají možnosti vícenásobného přenosu. Další kroky popsané níže:

1. Hostitel relace odesílá seznam svých privátních a veřejných adres IPv4 a IPv6 klientovi.
2. Klient spustí vlákno na pozadí pro vytvoření paralelního přenosu založeného na protokolu UDP přímo na jednu z IP adres hostitele.
3. I když klient zjistí zadané IP adresy, pokračuje v počátečním připojení přes přenos zpětného připojení, aby nedošlo k prodlevě v připojení uživatele.
4. Pokud má klient přímý dohled a konfigurace brány firewall je správná, klient vytvoří zabezpečené připojení TLS s hostitelem relace.
5. Po zřízení přenosu ShortPath přesune protokol RDP všechny dynamické virtuální kanály (DVCs), včetně vzdálené grafiky, vstupu a přesměrování zařízení do nového přenosu.
6. Pokud brána firewall nebo topologie sítě brání klientovi v navázání přímé připojení UDP, bude pokračovat s přenosem opačným připojením.

Následující diagram obsahuje podrobný přehled síťového připojení ShortPath RDP.

:::image type="content" source="media/rdp-shortpath-connections.svg" alt-text="Diagram síťových připojení ShortPath RDP" lightbox="media/rdp-shortpath-connections.svg":::

## <a name="requirements"></a>Požadavky

Aby bylo možné podporovat protokol RDP ShortPath, klient virtuální plochy systému Windows potřebuje přímý dohled nad hostitelem relace. Pomocí jedné z následujících technologií můžete získat přímý přehled o pohledu:

* [Soukromý partnerský vztah ExpressRoute](../expressroute/expressroute-circuit-peerings.md)
* [Síť VPN typu Site-to-Site (založená na protokolu IPsec)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [SÍŤ VPN typu Point-to-Site (založená na protokolu IPsec)](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [Přiřazení veřejné IP adresy](../virtual-network/virtual-network-public-ip-address.md)

Pokud pro připojení k virtuální síti Azure používáte jiné typy VPN, doporučujeme pro dosažení nejlepších výsledků použít síť VPN založenou na protokolu UDP. Většina řešení sítě VPN založených na protokolu TCP zapouzdřuje všechny pakety IP, včetně protokolu UDP, ale přidává zděděnou režii řízení zahlcení protokolu TCP, která by zpomalila výkon protokolu RDP.

Přímým pohledem na dohled znamená, že brány firewall neblokují port UDP 3390 a klient se může připojit přímo k hostiteli relace.

## <a name="enabling-rdp-shortpath-preview"></a>Povolení protokolu RDP ShortPath ve verzi Preview

Chcete-li se zapojit do verze Preview ShortPath protokolu RDP, je nutné povolit naslouchací proces protokolu RDP ShortPath na hostiteli relace. Protokol RDP ShortPath můžete povolit pro libovolný počet hostitelů relací používaných ve vašem prostředí. Neexistuje žádný požadavek na povolení ShortPath RDP na všech hostitelích ve fondu.
Chcete-li povolit naslouchací proces ShortPath, je nutné nakonfigurovat následující hodnoty registru:

> [!WARNING]
> Pokud nesprávně změníte registr pomocí Editoru registru nebo jiné metody, může dojít k vážným problémům. Tyto problémy mohou vyžadovat přeinstalaci operačního systému. Společnost Microsoft nemůže zaručit, že se tyto problémy podaří vyřešit. Registr upravujte na vlastní riziko.

1. Na hostiteli relace spusťte Regedit.exe a pak přejděte do následujícího umístění:

    ```cmd
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations
    ```

2. Vytvořte novou hodnotu **DWORD** s názvem **fUseUdpPortRedirector** a nastavte ji na hodnotu **1** (desítková).
3. Vytvořte novou hodnotu **DWORD** s názvem **UdpPortNumber** a nastavte ji na **3390** (desítkově).
4. Ukončete Editor registru.
5. Restartovat hostitele relace

Můžete taky spustit následující rutiny v okně PowerShellu se zvýšenými oprávněními a nastavit tyto hodnoty registru:

```powershell
$WinstationsKey = 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations'
New-ItemProperty -Path $WinstationsKey -Name 'fUseUdpPortRedirector' -ErrorAction:SilentlyContinue -PropertyType:dword -Value 1 -Force
New-ItemProperty -Path $WinstationsKey -Name 'UdpPortNumber' -ErrorAction:SilentlyContinue -PropertyType:dword -Value 3390 -Force
```

Ke konfiguraci zásad skupiny můžete použít taky PowerShell.

```powershell
# Replace $domainName value with the name of your Active Directory domain
# Replace $policyName value with the name of existing Group Policy Object
$domainName = "contoso.com"
$policyName = "RDP Shortpath Policy"
Set-GPPrefRegistryValue -Key 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations' -ValueName 'fUseUdpPortRedirector' -Value 1 -Type:DWord  -Action:Create -Context:Computer -Name $policyName -Domain $domainName
Set-GPPrefRegistryValue -Key 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations' -ValueName 'UdpPortNumber' -Value 3390 -Type:DWord  -Action:Create -Context:Computer -Name $policyName -Domain $domainName
```

## <a name="configure-windows-defender-firewall-with-advanced-security"></a>Konfigurace firewallu v programu Windows Defender s pokročilým zabezpečením

Pokud chcete povolit příchozí síťový provoz pro ShortPath protokolu RDP, použijte k vytvoření pravidel brány firewall v modulu snap-in konzoly MMC pro správu Zásady skupiny k tomu uzel firewall v programu Windows Defender s pokročilým zabezpečením.

1. Otevřete Konzola pro správu zásad skupiny pro [firewall v programu Windows Defender s pokročilým zabezpečením](/windows/security/threat-protection/windows-firewall/open-the-group-policy-management-console-to-windows-firewall-with-advanced-security).
2. V navigačním podokně vyberte **příchozí pravidla**.
3. Vyberte **akci** a pak vyberte **nové pravidlo**.
4. Na stránce **Typ pravidla** v Průvodci vytvořením nového příchozího pravidla vyberte možnost **vlastní** a poté vyberte možnost **Další**.
5. Na stránce **program** vyberte možnost **cesta k programu**, zadejte příkaz% systemroot% \system32\svchost.exe a pak vyberte možnost **Další**.
6. Na stránce **protokol a porty** vyberte typ protokolu UDP. V **místním portu** vyberte konkrétní porty a zadejte 3390.
7. Na stránce **Obor** můžete specifikovat, že pravidlo platí jen pro síťový provoz na IP adresy nebo z IP adres uvedených na této stránce. Nakonfigurujte podle potřeby návrh a pak vyberte **Další**.
8. Na stránce **Akce** vyberte možnost **Povolení připojení** a pak vyberte možnost **Další**.
9. Na stránce **profil** vyberte typy síťových umístění, pro které platí toto pravidlo, a pak vyberte **Další**.
10. Na stránce **název** zadejte název a popis pravidla a potom vyberte **Dokončit**.

Můžete ověřit, že nové pravidlo odpovídá snímkům obrazovky níže: :::image type="content" source="media/rdp-shortpath-firewall-general-tab.png" alt-text="snímek obrazovky s kartou obecné pro konfiguraci brány firewall pro síťová připojení SHORTPATH RDP" lightbox="media/rdp-shortpath-firewall-general-tab.png":::

:::image type="content" source="media/rdp-shortpath-firewall-service-settings.png" alt-text="Snímek obrazovky s kartou programy a služby pro konfiguraci brány firewall pro síťová připojení ShortPath RDP" lightbox="media/rdp-shortpath-firewall-service-settings.png":::

:::image type="content" source="media/rdp-shortpath-firewall-protocol-and-ports.png" alt-text="Snímek obrazovky s kartou protokoly a porty pro konfiguraci brány firewall pro síťová připojení ShortPath RDP" lightbox="media/rdp-shortpath-firewall-protocol-and-ports.png":::

Ke konfiguraci brány Windows Firewall můžete použít taky PowerShell:

```powershell
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP'  -PolicyStore PersistentStore -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True
```

### <a name="using-powershell-to-configure-windows-defender-firewall"></a>Použití prostředí PowerShell ke konfiguraci firewallu v programu Windows Defender

Ke konfiguraci zásad skupiny můžete použít taky PowerShell.

```powershell
# Replace $domainName value with the name of your Active Directory domain
# Replace $policyName value with the name of existing Group Policy Object
$domainName = "contoso.com"
$policyName = "RDP Shortpath Policy"
$gpoSession = Open-NetGPO -PolicyStore "$domainName\$policyName"
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP' -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True -GPOSession $gpoSession
Save-NetGPO -GPOSession $gpoSession
```

## <a name="configuring-azure-network-security-group"></a>Konfiguruje se skupina zabezpečení sítě Azure.

Pokud chcete povolit přístup k naslouchacímu procesu RDP ShortPath napříč hranicemi zabezpečení sítě, musíte nakonfigurovat skupinu zabezpečení sítě Azure tak, aby povolovala příchozí port UDP 3390.
Pomocí [dokumentace skupiny zabezpečení sítě](../virtual-machines/windows/nsg-quickstart-portal.md) vytvořte příchozí pravidlo zabezpečení, které umožní provoz s následujícími parametry:

* **Zdroj**  -  **Libovolný** rozsah IP adres, ve kterém se klienti nacházejí
* **Zdrojové rozsahy portů** – * *\** _ _ **cíl**  -  **Any**
* **Rozsahy**  -  cílových portů **3390**
* **Protokol**  -  **Protokol UDP**
* **Akce**  -  **Povolení**
* Volitelně můžete změnit **prioritu**. Priorita má vliv na pořadí, ve kterém jsou použita pravidla: čím nižší je číselná hodnota, použije se předchozí pravidlo.
* **Název** – **ShortPath RDP**

### <a name="disabling-rdp-shortpath-for-a-specific-subnet"></a>Zakázání protokolu RDP ShortPath pro určitou podsíť

Pokud potřebujete blokovat konkrétní podsítě pomocí přenosu RDP ShortPath, můžete nakonfigurovat další skupiny zabezpečení sítě, které určují zdrojové rozsahy IP adres.

## <a name="verifying-the-connectivity"></a>Ověřuje se připojení.

### <a name="using-connection-information-dialog"></a>Použití dialogového okna informace o připojení

Pokud chcete ověřit, jestli připojení používají ShortPath RDP, otevřete dialog "informace o připojení" kliknutím na ikonu antény na panelu nástrojů připojení.

:::image type="content" source="media/rdp-shortpath-connection-bar.png" alt-text="Obrázek panelu Připojení ke vzdálené ploše":::

:::image type="content" source="media/rdp-shortpath-connection-info.png" alt-text="Obrázek dialogového okna s informacemi o Připojení ke vzdálené ploše":::

### <a name="using-event-logs"></a>Použití protokolů událostí

Ověření, že relace používá ShortPath přenos RDP:

1. Připojte se k ploše virtuálního počítače pomocí klienta virtuální plochy Windows.
2. Spusťte Prohlížeč událostí a přejděte do následujícího uzlu: **protokoly aplikací a služeb > Microsoft > Windows > RemoteDesktopServices-RdpCoreCDV > Microsoft-Windows-RemoteDesktopServices-RdpCoreCDV/Operational**
3. Pokud chcete zjistit, jestli se používá přenos ShortPath RDP, vyhledejte událost s ID 131.

### <a name="using-log-analytics-to-verify-shortpath-connectivity"></a>Ověření připojení ShortPath pomocí Log Analytics

Pokud používáte [Azure Log Analytics](./diagnostics-log-analytics.md), můžete monitorovat připojení pomocí dotazu na [tabulku WVDConnections](/azure/azure-monitor/reference/tables/wvdconnections). Sloupec s názvem UdpUse označuje, zda zásobník RDP virtuálních počítačů s Windows používá protokol UDP při připojení aktuálního uživatele.
Možné hodnoty jsou:

* **0** – připojení uživatele nepoužívá protokol RDP ShortPath
* **1** – připojení uživatele používá ShortPath RDP.
  
Následující seznam dotazů vám umožní zkontrolovat informace o připojení. Tento dotaz můžete spustit v [Editoru dotazů Log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md#write-a-query). Pro každý dotaz nahraďte `userupn` hlavní název uživatele (UPN), kterého chcete vyhledat.

```kusto
let Events = WVDConnections | where UserName == "userupn" ;
Events
| where State == "Connected"
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated, UdpUse, SessionHostName, SessionHostSxSStackVersion
| join (Events
| where State == "Completed"
| project EndTime=TimeGenerated, CorrelationId, UdpUse)
on CorrelationId
| project StartTime, Duration = EndTime - StartTime, ResourceAlias, UdpUse,  SessionHostName, SessionHostSxSStackVersion
| sort by StartTime asc
```

## <a name="troubleshooting"></a>Řešení potíží

### <a name="verify-shortpath-listener"></a>Ověření naslouchacího procesu ShortPath

Chcete-li ověřit, zda je povolen naslouchací proces UDP, použijte následující příkaz prostředí PowerShell na hostiteli relace:

```powershell
Get-NetUDPEndpoint -OwningProcess ((Get-WmiObject win32_service -Filter "name = 'TermService'").ProcessId)  -LocalPort 3390
```

Pokud je tato možnost povolená, zobrazí se výstup podobný následujícímu:

```dos
LocalAddress                             LocalPort
------------                             ---------
::                                       3390
0.0.0.0                                  3390
```

Pokud dojde ke konfliktu, můžete pomocí následujícího příkazu identifikovat proces, který bude tento port tvořit.

```powershell
Get-Process -id (Get-NetUDPEndpoint  -LocalPort 3390 -LocalAddress 0.0.0.0).OwningProcess
```

## <a name="disabling-rdp-shortpath"></a>Zakázání protokolu RDP ShortPath

V některých případech možná budete muset zakázat přenos RDP ShortPath. Protokol RDP ShortPath můžete zakázat pomocí zásad skupiny.

### <a name="disabling-rdp-shortpath-on-the-client"></a>Zakázání ShortPath RDP na klientovi

Pokud chcete zakázat ShortPath RDP pro konkrétního klienta, můžete použít následující Zásady skupiny k zakázání podpory UDP:

1. Na straně klienta spusťte **gpedit. msc**.
2. Přejděte na **Konfigurace počítače > šablony pro správu > součásti systému Windows > Vzdálená plocha > připojení ke vzdálené ploše Client**.
3. Nastavení možnosti **vypnout UDP u klienta** na **povoleno**

### <a name="disabling-rdp-shortpath-on-the-session-host"></a>Zakázání protokolu RDP ShortPath na hostiteli relace

Pokud chcete zakázat ShortPath RDP pro konkrétního hostitele relace, můžete použít následující Zásady skupiny k zakázání podpory UDP:

1. Na hostiteli relace spusťte **gpedit. msc**.
2. Přejděte na **Konfigurace počítače > šablony pro správu > součásti systému Windows > > služby Vzdálená plocha připojení ke vzdálené ploše připojení k hostiteli >**.
3. Nastavte nastavení **"Vyberte přenosové protokoly RDP"** **jenom na TCP** .

## <a name="public-preview-feedback"></a>Zpětná vazba veřejné verze Preview

Rádi bychom od vás slyšeli o vašich zkušenostech s touto verzí Public Preview!
* Pro otázky, požadavky, komentáře a další zpětnou vazbu [použijte tento formulář zpětné vazby](https://aka.ms/RDPShortpathFeedback).

## <a name="next-steps"></a>Další kroky

* Další informace o připojení k síti virtuálních počítačů s Windows najdete v tématu [Principy připojení k síti virtuálních počítačů s Windows](network-connectivity.md).
* Pokud chcete začít se službou QoS (Quality of Service) pro virtuální počítače s Windows, přečtěte si téma [implementace technologie QoS (Quality of Service) pro virtuální počítače s Windows](rdp-quality-of-service-qos.md).
