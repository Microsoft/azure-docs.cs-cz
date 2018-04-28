---
title: Spravovat zachycení paketů s sledovací proces sítě Azure - portálu Azure | Microsoft Docs
description: Tato stránka vysvětluje, jak spravovat funkci zachycení paketu sledovací proces sítě pomocí portálu Azure
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 59edd945-34ad-4008-809e-ea904781d918
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 508b9e7eef757277d4bc0e93a26f3a63045f31e4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Spravovat zachycení paketů s sledovací proces sítě Azure pomocí portálu

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [CLI 1.0](network-watcher-packet-capture-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-packet-capture-manage-cli.md)
> - [Rozhraní API Azure REST](network-watcher-packet-capture-manage-rest.md)

Zachytáváním paketů sledovací proces sítě vám umožní vytvořit relace zachytávání sledovat provoz do a z virtuálního počítače. Filtry jsou k dispozici pro relaci zachytávání zajistit, že zaznamenáte pouze provoz, který chcete. Při diagnostice sítě anomálií reaktivně a proaktivně pomáhá zachytáváním paketů. Jiné účely zahrnují shromažďování statistiku sítě, získá informace o síti vniknutí, k ladění komunikaci klienta se serverem a mnoho dalšího. Díky vzdáleně aktivovat paketu zachycení, tato funkce snižuje zátěž spuštěných zachytáváním paketů ručně a na požadované počítače, který úspora času.

Tento článek vás provede úloh jiný správy, které jsou aktuálně dostupné pro zachytávání paketů.

- [**Spustit zachytávání paketů**](#start-a-packet-capture)
- [**Zastavit zachytávání paketů**](#stop-a-packet-capture)
- [**Odstranit zachytávání paketů**](#delete-a-packet-capture)
- [**Stáhnout zachytáváním paketů**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte v následujících zdrojích informací:

- Instance sledovací proces sítě v oblasti, kterou chcete vytvořit zachytávání paketů
- Virtuální počítač s příponou zachytávání paketů povoleno.

> [!IMPORTANT]
> Rozšíření virtuálního počítače vyžaduje zachytáváním paketů `AzureNetworkWatcherExtension`. Instalaci rozšíření na virtuální počítač s Windows najdete v článku [rozšíření virtuálního počítače Azure sítě sledovacích procesů agenta pro Windows](../virtual-machines/windows/extensions-nwa.md) a u virtuálního počítače s Linuxem, navštivte [rozšíření virtuálního počítače Azure sítě sledovacích procesů agenta pro Linux](../virtual-machines/linux/extensions-nwa.md).

### <a name="packet-capture-agent-extension-through-the-portal"></a>Rozšíření agenta zachytávání paketů prostřednictvím portálu

Chcete-li nainstalovat agenta virtuálního počítače zachytávání paketů prostřednictvím portálu, přejděte k virtuálnímu počítači, klikněte na **rozšíření** > **přidat** a vyhledejte **sítě sledovacích procesů agenta pro Windows**

![zobrazení agenta][agent]

## <a name="packet-capture-overview"></a>Přehled zachytávání paketů

Přejděte na [portál Azure](https://portal.azure.com) a klikněte na tlačítko **sítě** > **sledovací proces sítě** > **zachytávání paketů**

Přehled stránka obsahuje že seznam všech paketů zaznamená, který neexistuje bez ohledu na stav.

> [!NOTE]
> Zachytáváním paketů vyžaduje následující připojování.
> * Odchozí připojení k účtu úložiště přes port 443.
> * Příchozí a odchozí připojení k 169.254.169.254
> * Příchozí a odchozí připojení k 168.63.129.16

![obrazovka Přehled zachytávání paketů][1]

## <a name="start-a-packet-capture"></a>Spustit zachytávání paketů

Klikněte na tlačítko **přidat** vytvořit zachytáváním paketů.

Vlastnosti, které lze definovat na zachytáváním paketů jsou:

**Hlavní nastavení**

- **Předplatné** – tato hodnota je odběr, který se používá, je potřeba instanci sledovací proces sítě v každém předplatném.
- **Skupina prostředků** – prostředek skupiny virtuální počítač, který je cílem.
- **Cílové virtuální počítač** -virtuální počítač, který běží v zachytávání paketů
- **Název zachycení paketu** – tato hodnota je název zachytáváním paketů.

**Zachycení konfigurace**

- **Místní cesta** – místní cesta na virtuálním počítači, kde je uložena zachytáváním paketů (platná pouze tehdy, když **[soubor]** je vybraný). Je nutné zadat platnou cestu. Pokud používáte virtuální počítač s Linuxem, cesta musí začínat znakem / var / zaznamená.
- **Účet úložiště** -Určuje, pokud se zachytáváním paketů je uložen v účtu úložiště.
- **Soubor** -Určuje, pokud se zachytáváním paketů se místně uloží na virtuálním počítači.
- **Účty úložiště** – vybraný účet úložiště se zachytáváním paketů v uložit. Výchozí umístění je id name}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscription účtu https://{storage} /resourcegroups/ {název počítače name}/providers/microsoft.compute/virtualmachines/{virtual skupiny prostředků} / {RR} / {MM} / {DD} / {HH} packetcapture__{MM}_ CAP _ {XXX} {SS}. (Aktivní, pouze pokud **úložiště** je vybraná)
- **Cesta k souboru místní** -místní cestu na virtuální počítač uložit zachytáváním paketů. (Aktivní, pouze pokud **soubor** je vybraný). Je nutné zadat platnou cestu. Pro virtuální počítač s Linuxem, musí cesta začínat */var/zaznamená*.
- **Maximální počet bajtů paketu** – počet bajtů z jednotlivých paketů, které jsou zachyceny, všechny bajty zachyceny, pokud je ponecháno prázdné.
- **Maximální počet bajtů za relace** – celkový počet bajtů, které jsou zachyceny, hodnota v případě dosažení Zastaví zachytávání paketů.
- **Časový limit (sekundy)** -nastaví časový limit pro zachytávání paketů zastavit. Výchozí hodnota je 18000 sekund.

> [!NOTE]
> Účty služby Premium storage aktuálně nejsou podporované pro ukládání paketu zaznamená.

**Filtrování (volitelné)**

- **Protokol** – protokol, který se zachytáváním paketů filtrovat. Dostupné hodnoty jsou TCP, UDP a všechny.
- **Místní IP adresu** -tuto hodnotu filtry zachytáváním paketů na pakety, kde místní IP adresa odpovídá této hodnotě filtru.
- **Místní port** -tuto hodnotu filtry zachytáváním paketů na pakety, kde místního portu odpovídá této hodnotě filtru.
- **Vzdálené IP adresy** -tuto hodnotu filtry zachytáváním paketů na pakety, kde vzdálené IP odpovídá této hodnotě filtru.
- **Vzdálený port** -tuto hodnotu filtry zachytáváním paketů na pakety, kde vzdálený port odpovídá této hodnotě filtru.

> [!NOTE]
> Hodnoty port a IP adres může být jedna hodnota, rozsah hodnot nebo u sady. (to znamená, 80 – 1024 pro port) Můžete definovat libovolný počet filtrů tak, jak chcete.

Po vyplnění hodnoty, klikněte na **OK** vytvořit zachytáváním paketů.

![Vytvoření zachytávání paketů][2]

Po vypršení časového limitu určeného v zachytáváním paketů zachytáváním paketů se zastaví a můžete zkontrolovat. Můžete také ručně zastavit relací zachycení paketů.

## <a name="delete-a-packet-capture"></a>Odstranit zachytávání paketů

V zobrazení zachytávání paketů, klikněte na tlačítko **kontextovou nabídku** (...) nebo klikněte pravým tlačítkem a klikněte na tlačítko **odstranit** zastavit zachytávání paketů

![Odstranit zachytávání paketů][3]

> [!NOTE]
> Odstraňuje se zachytáváním paketů nedojde k odstranění souboru v účtu úložiště.

Jste vyzváni k potvrzení chcete odstranit zachytáváním paketů, klikněte na **Ano**

![Potvrzení][4]

## <a name="stop-a-packet-capture"></a>Zastavit zachytávání paketů

V zobrazení zachytávání paketů, klikněte na tlačítko **kontextovou nabídku** (...) nebo klikněte pravým tlačítkem a klikněte na tlačítko **Zastavit** zastavit zachytávání paketů

## <a name="download-a-packet-capture"></a>Stáhnout zachytáváním paketů

Po dokončení relace zachytávání paketů zachycení soubor nahraje do úložiště objektů blob nebo do místního souboru virtuálního počítače. Umístění úložiště pro zachytávání paketů se definuje při vytvoření relace. Nástroj vhodné pro přístup k těmto zachycení soubory uložené na účet úložiště je Microsoft Azure Storage Explorer, kterou můžete stáhnout tady:  http://storageexplorer.com/

Pokud je zadaný účet úložiště, soubory zachytávání paketů ukládají na účet úložiště v následujícím umístění:
```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Další postup

Informace o automatizaci paketu zachytává se virtuální počítač výstrahy zobrazením [vytvořit zaznamenání výstrahy spouštěná paketu](network-watcher-alert-triggered-packet-capture.md)

Najít, pokud určité provoz je povolený v nebo z virtuálního počítače navštivte stránky [zkontrolujte IP tok ověření](diagnose-vm-network-traffic-filtering-problem.md)

<!-- Image references -->
[1]: ./media/network-watcher-packet-capture-manage-portal/figure1.png
[2]: ./media/network-watcher-packet-capture-manage-portal/figure2.png
[3]: ./media/network-watcher-packet-capture-manage-portal/figure3.png
[4]: ./media/network-watcher-packet-capture-manage-portal/figure4.png
[agent]: ./media/network-watcher-packet-capture-manage-portal/agent.png













