---
title: Správa zachytávání paketů pomocí služby Azure Network Watcher – Azure portal | Dokumentace Microsoftu
description: Tato stránka vysvětluje, jak spravovat funkce zachytávání paketů služby Network Watcher pomocí webu Azure portal
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
ms.openlocfilehash: 18e5f8eda51f8834f6346eef3d7ad31bc556290a
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090193"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Správa zachytávání paketů pomocí služby Azure Network Watcher pomocí portálu

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [Rozhraní Azure REST API](network-watcher-packet-capture-manage-rest.md)

Zachytávání paketů Network Watcher umožňuje vytvořit relace zachycení sledujte provoz do a z virtuálního počítače. Filtry jsou k dispozici pro relace zachytávání zajistit, že zachytíte jenom provoz, který chcete. Zachytávání paketů pomáhá diagnostikovat sítě anomálie reaktivně a proaktivně. Mezi další použití patří shromažďování statistických údajů sítě získat informace o síti vniknutí, chcete-li ladit komunikaci klienta se serverem a spoustu dalších věcí. Díky možnosti vzdáleně spustit zachytávání paketů, tato funkce usnadňuje si museli dělat starosti s zachycení paketů ručně a na požadovaný počítač, což šetří cenný čas.

Tento článek vás provede jiné úlohy, které jsou aktuálně k dispozici pro zachycení paketů.

- [**Spustit zachytávání paketů**](#start-a-packet-capture)
- [**Zastavit zachytávání paketů**](#stop-a-packet-capture)
- [**Odstranit zachycení paketů**](#delete-a-packet-capture)
- [**Stáhněte si zachytávání paketů**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte následující prostředky:

- Instance služby Network Watcher v oblasti, kterou chcete vytvořit zachytávání paketů
- Virtuální počítač s příponou zachycení paketů povoleno.

> [!IMPORTANT]
> Zachytávání paketů vyžaduje rozšíření virtuálního počítače `AzureNetworkWatcherExtension`. Instalaci rozšíření na virtuálním počítači s Windows najdete [rozšíření virtuálního počítače Azure Network Watcher Agent pro Windows](../virtual-machines/windows/extensions-nwa.md) a pro virtuální počítač s Linuxem, navštivte [rozšíření virtuálního počítače Azure Network Watcher Agent pro Linux](../virtual-machines/linux/extensions-nwa.md).

### <a name="packet-capture-agent-extension-through-the-portal"></a>Rozšíření agenta zachytávání paketů pomocí portálu

Pokud chcete nainstalovat agenta virtuálního počítače zachytávání paketů prostřednictvím portálu, přejděte ke svému virtuálnímu počítači, klikněte na **rozšíření** > **přidat** a vyhledejte **síťových sledovacích procesů agenta pro Windows**

![zobrazení agenta][agent]

## <a name="packet-capture-overview"></a>Přehled zachytávání paketů

Přejděte [webu Azure portal](https://portal.azure.com) a klikněte na tlačítko **sítě** > **Network Watcher** > **zachytávání paketů**

Na stránce s přehledem zobrazuje že seznam všech paketů zaznamená, která existuje bez ohledu na stav.

> [!NOTE]
> Zachytávání paketů se vyžaduje následující připojování.
> * Odchozí připojení k účtu úložiště přes port 443.
> * Příchozí a odchozí připojení k 169.254.169.254
> * Příchozí a odchozí připojení k 168.63.129.16

![obrazovka Přehled zachytávání paketů][1]

## <a name="start-a-packet-capture"></a>Spustit zachytávání paketů

Klikněte na tlačítko **přidat** vytvořit zachytávání paketů.

Vlastnosti, které lze definovat v zachytávání paketů jsou:

**Hlavní nastavení**

- **Předplatné** – tato hodnota je předplatné, které se používá, instance služby network watcher je potřeba v každém předplatném.
- **Skupina prostředků** -skupinu prostředků virtuálního počítače, které se usiluje.
- **Cílit na virtuální počítač** – virtuální počítač, na kterém běží zachytávání paketů
- **Název zachycení paketu** – tato hodnota je název zachycení paketů.

**Zachycení konfigurace**

- **Místní cesta k souboru** – místní cesta na virtuálním počítači, ve kterém je uložený zachytávání paketů (platné pouze tehdy, když **[soubor]** je vybrána). Musíte zadat platnou cestu. Pokud používáte virtuální počítač s Linuxem, cesta musí začínat znakem / var / zachytí.
- **Účet úložiště** – Určuje, pokud se zachytávání paketů se uloží do účtu úložiště.
- **Soubor** – Určuje, pokud se zachytávání paketů se uloží místně na virtuálním počítači.
- **Účty úložiště** – vybraný účet úložiště k ukládání zachytávání paketů v. Výchozí umístění je id name}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscription účtu https://{storage} /resourcegroups/ {počítač name}/providers/microsoft.compute/virtualmachines/{virtual skupiny prostředků s názvem} / {RR} / {MM} / {DD} / {HH} packetcapture__{MM}_{SS} _ {XXX} CAP. (Povolena, pouze pokud **úložiště** zaškrtnuto)
- **Místní cesta k souboru** – místní cestu na virtuální počítač uložit zachytávání paketů. (Povolena, pouze pokud **souboru** je vybrána). Je potřeba zadat platnou cestu. Pro virtuální počítač s Linuxem, musí cesta začínat */var/zachycuje*.
- **Maximální počet bajtů na paket** – počet bajtů z jednotlivých paketů, které jsou zachyceny, jsou zachyceny všechny bajty, pokud je ponecháno prázdné.
- **Maximální počet bajtů na relaci** – celkový počet bajtů, které jsou zachyceny hodnota po dosažení zarážky zachycení paketů.
- **Časový limit (sekundy)** – nastaví časový limit pro zachytávání paketů zastavit. Výchozí hodnota je 18000 sekund.

> [!NOTE]
> Účty úložiště úrovně Premium nejsou aktuálně podporovány pro ukládání paketů zachytí.

**Filtrování (nepovinné)**

- **Protokol** – protokol, který se filtr pro zachycení paketů. Dostupné hodnoty jsou TCP, UDP a všechny.
- **Místní IP adresa** -tuto hodnotu filtry paketů zachycení paketů, kde místní IP adresa odpovídá této hodnotě filtru.
- **Místní port** -tuto hodnotu filtry zachytávání paketů na pakety, kde na místní port odpovídá této hodnotě filtr.
- **Vzdálená IP adresa** -tuto hodnotu filtry paketů zachycení paketů, kde vzdálené IP odpovídá této hodnotě filtr.
- **Vzdálený port** -tuto hodnotu filtry paketů zachycení paketů, kde vzdálený port odpovídá této hodnotě filtru.

> [!NOTE]
> Hodnoty portu a IP adresa může být jedna hodnota, rozsah hodnot nebo sadu. (to znamená, 80-1024 pro port) Můžete definovat libovolný počet filtrů, jak chcete.

Po vyplnění hodnot klikněte na tlačítko **OK** vytvořit zachytávání paketů.

![Vytvoření zachytávání paketů][2]

Po vypršení časového limitu určeného v zachytávání paketů zachycení paketů se zastaví a si můžete prohlédnout. Můžete také ručně zastavit relace zachycení paketů.

## <a name="delete-a-packet-capture"></a>Odstranit zachycení paketů

V zobrazení pro zachytávání paketů, klikněte na tlačítko **kontextovou nabídku** (...) nebo klikněte pravým tlačítkem myši a klikněte na tlačítko **odstranit** se zastavit zachytávání paketů

![Odstranit zachycení paketů][3]

> [!NOTE]
> Odstraňuje se zachycení paketů nedojde k odstranění souboru v účtu úložiště.

Zobrazí se výzva k potvrzení, které chcete odstranit zachycení paketů, klikněte na tlačítko **Ano**

![Potvrzení][4]

## <a name="stop-a-packet-capture"></a>Zastavit zachytávání paketů

V zobrazení pro zachytávání paketů, klikněte na tlačítko **kontextovou nabídku** (...) nebo klikněte pravým tlačítkem myši a klikněte na tlačítko **Zastavit** se zastavit zachytávání paketů

## <a name="download-a-packet-capture"></a>Stáhněte si zachytávání paketů

Po dokončení relace zachycení paketů zachytávací soubor nahraje do služby blob storage nebo do místního souboru na virtuálním počítači. Umístění úložiště pro zachytávání paketů je definován při vytvoření relace. Praktický nástroj pro přístup k těmto zachycení soubory uložené na účet úložiště je Microsoft Azure Storage Explorer, které je možné stáhnout tady:  http://storageexplorer.com/

Pokud je určen účet úložiště, jsou uloženy soubory zachytávání paketů do účtu úložiště v následujícím umístění:
```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Další postup

Zjistěte, jak automatizovat zachytávání paketů pomocí virtuálního počítače výstrahy zobrazením [vytvořit zachytávání paketů upozornění aktivovaných](network-watcher-alert-triggered-packet-capture.md)

Najít, jestli některé je povolený provoz do nebo ze svého virtuálního počítače návštěvou [ověření toku protokolu IP zkontrolujte](diagnose-vm-network-traffic-filtering-problem.md)

<!-- Image references -->
[1]: ./media/network-watcher-packet-capture-manage-portal/figure1.png
[2]: ./media/network-watcher-packet-capture-manage-portal/figure2.png
[3]: ./media/network-watcher-packet-capture-manage-portal/figure3.png
[4]: ./media/network-watcher-packet-capture-manage-portal/figure4.png
[agent]: ./media/network-watcher-packet-capture-manage-portal/agent.png













