---
title: Správa zachytávání paketů – portál Azure
titleSuffix: Azure Network Watcher
description: Zjistěte, jak spravovat funkci zachytávání paketů sledovacího programu služby Network Watcher pomocí portálu Azure.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: damendo
ms.openlocfilehash: 6fc4a25e39fb8f27151b2e3bec1959d74a619233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840823"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Správa zachytávání paketů pomocí služby Azure Network Watcher pomocí portálu

Zachycení paketu Sledování sítě umožňuje vytvářet relace sběru pro sledování provozu do a z virtuálního počítače. Filtry jsou k dispozici pro relaci zachycení, které zajistí, že zachytíte pouze požadovaný provoz. Zachytávání paketů pomáhá diagnostikovat anomálie sítě, a to jak reaktivně, tak proaktivně. Mezi další použití patří shromažďování síťových statistik, získávání informací o síťových vniknutích, ladění komunikace mezi klientem a serverem a mnoho dalšího. Možnost vzdáleného zachytávání paketů usnadňuje ruční spuštění sběru paketů na požadovaném virtuálním počítači, což šetří cenný čas.

V tomto článku se naučíte spustit, zastavit, stáhnout a odstranit sběr paketů. 

## <a name="before-you-begin"></a>Než začnete

Sběr paketů vyžaduje následující připojení:
* Odchozí připojení k účtu úložiště přes port 443.
* Příchozí a odchozí připojení na 169.254.169.254
* Příchozí a odchozí připojení na 168.63.129.16

Pokud je k síťovému rozhraní nebo podsíti, ve které se síťové rozhraní nachází, přidružena skupina zabezpečení sítě, zajistěte, aby existovala pravidla umožňující předchozí porty. Podobně přidání uživatelem definovaných dopravních tras do sítě může zabránit připojení k výše uvedeným IP adresy a porty. Ujistěte se, že jsou dosažitelné. 

## <a name="start-a-packet-capture"></a>Spuštění sběru paketů

1. V prohlížeči přejděte na [portál Azure](https://portal.azure.com) a vyberte **Všechny služby**a v **části Síť**vyberte Sledovací **program sítě** .
2. V části **Nástroje pro diagnostiku sítě**vyberte **možnost Zachytávání paketů** . Všechny existující pakety zachycení jsou uvedeny, bez ohledu na jejich stav.
3. Chcete-li vytvořit sběr paketů, vyberte **přidat.** Můžete vybrat hodnoty pro následující vlastnosti:
   - **Předplatné**: Předplatné, které virtuální počítač, pro který chcete vytvořit sběr paketů, je v.
   - **Skupina prostředků**: Skupina prostředků virtuálního počítače.
   - **Cílový virtuální počítač**: Virtuální počítač, pro který chcete vytvořit sběr paketů.
   - **Název sběru paketů**: Název pro sběr paketů.
   - **Účet úložiště nebo soubor**: Vyberte **účet úložiště**, **soubor**nebo obojí. Pokud vyberete **Soubor**, sběr se zapíše do cesty ve virtuálním počítači.
   - **Cesta k místnímu souboru**: Místní cesta ve virtuálním počítači, kde bude sběr paketů uložen (platí pouze v případě, že je vybrán *soubor).* Cesta musí být platná cesta. Pokud používáte virtuální počítač Linux, cesta musí začínat */var/captures*.
   - **Účty úložiště**: Vyberte existující účet úložiště, pokud jste vybrali *účet úložiště*. Tato možnost je k dispozici pouze v případě, že jste vybrali **možnost Úložiště**.
   
     > [!NOTE]
     > Účty úložiště Premium nejsou aktuálně podporovány pro ukládání zachycení paketů.

   - **Maximální počet bajtů na paket**: Počet bajtů z každého paketu, který je zachycen. Pokud je ponecháno prázdné, jsou zachyceny všechny bajty.
   - **Maximální počet bajtů na relaci**: Celkový počet zachycených bajtů. Po dosažení hodnoty se sběr paketů zastaví.
   - **Časový limit (v sekundách):** Časový limit před zastavením sběru paketů. Výchozí hodnota je 18 000 sekund.
   - Filtrování (volitelné). Vybrat **+ Přidat filtr**
     - **Protokol**: Protokol pro filtrování sběru paketů. Dostupné hodnoty jsou TCP, UDP a Any.
     - **Místní ADRESA IP**: Filtruje sběr paketů pro pakety, u kterých místní adresa IP odpovídá této hodnotě.
     - **Místní port**: Filtruje sběr paketů pro pakety, u kterých se místní port shoduje s touto hodnotou.
     - **Vzdálená adresa IP**: Filtruje sběr paketů pro pakety, u kterých vzdálená adresa IP odpovídá této hodnotě.
     - **Vzdálený port**: Filtruje sběr paketů pro pakety, u kterých vzdálený port odpovídá této hodnotě.
    
     > [!NOTE]
     > Port a IP adresy hodnoty mohou být jednu hodnotu, rozsah hodnot nebo rozsah, například 80-1024, pro port. Můžete definovat libovolný počet filtrů.

4. Vyberte **OK**.

Po vypršení časového limitu nastaveného pro sběr paketů je sběr paketů zastaven a lze jej zkontrolovat. Můžete také ručně zastavit relaci sběru paketů.

> [!NOTE]
> Portál automaticky:
>  * Vytvoří sledovací proces sítě ve stejné oblasti jako oblast, ve které existuje vybraný virtuální počítač, pokud oblast ještě nemá sledovací proces sítě.
>  * Přidá rozšíření *AzureNetworkWatcherExtension* [Linux](../virtual-machines/linux/extensions-nwa.md) nebo [Windows](../virtual-machines/windows/extensions-nwa.md) virtuálního počítače do virtuálního počítače, pokud ještě není nainstalovaný.

## <a name="delete-a-packet-capture"></a>Odstranění zachycení paketu

1. V zobrazení zachytávání paketů vyberte **...** na pravé straně sběru paketů nebo klepněte pravým tlačítkem myši na existující sběr paketů a vyberte **odstranit**.
2. Budete vyzváni k potvrzení, že chcete odstranit sběr paketů. Vyberte **ano**.

> [!NOTE]
> Odstraněním sběru paketů neodstraníte sběrný soubor v účtu úložiště nebo ve virtuálním počítači.

## <a name="stop-a-packet-capture"></a>Zastavení sběru paketů

V zobrazení sběru paketů vyberte **...** na pravé straně sběru paketů nebo klepněte pravým tlačítkem myši na existující sběr paketů a vyberte **zastavit**.

## <a name="download-a-packet-capture"></a>Stažení zachytávání paketů

Po dokončení relace sběru paketů se soubor sběru nahraje do úložiště objektů blob nebo do místního souboru ve virtuálním počítači. Umístění úložiště sběru paketů je definováno při vytváření sběru paketů. Pohodlným nástrojem pro přístup k souborům pro digitalizaci uloženým v účtu úložiště je Microsoft Azure Storage Explorer, který si můžete [stáhnout](https://storageexplorer.com/).

Pokud je zadán účet úložiště, soubory pro digitalizaci paketů jsou uloženy do účtu úložiště v následujícím umístění:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

Pokud jste při vytváření sběru vybrali **soubor,** můžete soubor zobrazit nebo stáhnout z cesty, kterou jste nakonfigurovali ve virtuálním počítači.

## <a name="next-steps"></a>Další kroky

- Informace o automatizaci sběru paketů pomocí výstrah virtuálních strojů naleznete [v tématu Vytvoření záznamu aktivovaného záznamu paketů](network-watcher-alert-triggered-packet-capture.md).
- Pokud chcete zjistit, jestli je určitý provoz povolen ve virtuálním počítači nebo z jeho [počítače, přečtěte si část Diagnostika problému s filtrem síťového provozu virtuálního počítače](diagnose-vm-network-traffic-filtering-problem.md).
