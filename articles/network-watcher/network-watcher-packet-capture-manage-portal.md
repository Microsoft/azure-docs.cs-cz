---
title: Správa zachycení paketů – Azure Portal
titleSuffix: Azure Network Watcher
description: Naučte se spravovat funkci zachytávání paketů Network Watcher pomocí Azure Portal.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: d4112bd1d632ec21e2d8cb565027277eeb85452d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018255"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Správa zachytávání paketů pomocí Azure Network Watcher pomocí portálu

Network Watcher Capture paketů umožňuje vytvářet relace zachycení pro sledování provozu do a z virtuálního počítače. K dispozici jsou filtry pro relaci zachycení, aby bylo možné zachytit pouze požadovaný provoz. Zachytávání paketů pomáhá diagnostikovat anomálie sítě, jak proaktivní, tak i proaktivní. Mezi další použití patří shromažďování statistik sítě, získávání informací o neoprávněných vniknutích k síti, ladění komunikace mezi klientem a serverem a mnoho dalšího. Schopnost vzdáleně aktivovat zachycení paketů, usnadňuje zatížení ručního zachycení paketů na požadovaném virtuálním počítači, který šetří cenné časy.

V tomto článku se naučíte, jak spustit, zastavit, stáhnout a odstranit zachytávání paketů. 

## <a name="before-you-begin"></a>Než začnete

Zachytávání paketů vyžaduje následující odchozí připojení TCP:
- na vybraný účet úložiště přes port 443
- na 169.254.169.254 přes port 80
- na 168.63.129.16 přes port 8037

> [!NOTE]
> Porty zmíněné v druhých dvou případech jsou společné ve všech Network Watcherch funkcích, které zahrnují rozšíření Network Watcher a můžou příležitostně změnit.


Pokud je skupina zabezpečení sítě přidružená k síťovému rozhraní nebo podsíti, ve které je síťové rozhraní, zajistěte, aby existovala pravidla, která umožňují předchozí porty. Podobně přidávání uživatelem definovaných tras pro přenos do vaší sítě může bránit připojení k výše uvedeným IP adresám a portům. Ujistěte se prosím, že jsou dostupné. 

## <a name="start-a-packet-capture"></a>Spustit zachytávání paketů

1. V prohlížeči přejděte na [Azure Portal](https://portal.azure.com) a vyberte **všechny služby** a potom vyberte **Network Watcher** v **části síť**.
2. V části nástroje pro **diagnostiku sítě** vyberte **zachycení paketů** . Budou uvedena všechna existující zachycení paketů bez ohledu na jejich stav.
3. Vyberte **Přidat** a vytvořte zachytávání paketů. Můžete vybrat hodnoty pro následující vlastnosti:
   - **Předplatné**: předplatné, pro které je virtuální počítač, pro který chcete vytvořit zachytávání paketů, je v.
   - **Skupina prostředků**: Skupina prostředků virtuálního počítače.
   - **Cílový virtuální počítač**: virtuální počítač, pro který chcete vytvořit zachytávání paketů.
   - **Název zachytávání paketů**: název zachytávání paketů.
   - **Účet úložiště nebo soubor**: vyberte **účet úložiště**, **soubor** nebo obojí. Pokud vyberete **soubor**, zachytávání se zapíše do cesty v rámci virtuálního počítače.
   - **Místní cesta k souboru**: místní cesta na virtuálním počítači, kam se bude zachytávání paketů ukládat (platí jenom v případě, že je vybraný *soubor* ). Cesta musí být platná cesta. Pokud používáte virtuální počítač se systémem Linux, cesta musí začínat na */var/Captures*.
   - **Účty úložiště**: Pokud jste vybrali *účet úložiště*, vyberte existující účet úložiště. Tato možnost je dostupná jenom v případě, že jste vybrali **úložiště**.
   
     > [!NOTE]
     > Účty Premium Storage se v tuto chvíli nepodporují pro ukládání zachycení paketů.

   - **Maximální počet bajtů na paket**: počet bajtů z každého zaznamenaného paketu. Pokud je ponecháno prázdné, všechny bajty budou zachyceny.
   - **Maximální počet bajtů na relaci**: celkový počet zaznamenaných bajtů. Jakmile je hodnota dosažena, zachytávání paketů se zastaví.
   - **Časový limit (sekundy)**: časový limit před zastavením zachytávání paketů. Výchozí hodnota je 18 000 sekund.
   - Filtrování (volitelné). Vybrat **+ Přidat filtr**
     - **Protokol**: protokol, který se má vyfiltrovat pro zachytávání paketů. Dostupné hodnoty jsou TCP, UDP a any.
     - **Místní IP adresa**: filtruje zachytávání paketů pro pakety, které místní IP adresa odpovídá této hodnotě.
     - **Místní port**: vyfiltruje zachytávání paketů pro pakety, které místní port odpovídá této hodnotě.
     - **Vzdálená IP adresa**: vyfiltruje zachytávání paketů pro pakety, které Vzdálená IP adresa odpovídá této hodnotě.
     - **Vzdálený port**: filtruje zachytávání paketů pro pakety, které vzdálený port odpovídá této hodnotě.
    
     > [!NOTE]
     > Hodnoty portu a IP adresy můžou být jedinou hodnotou, rozsahem hodnot nebo rozsahem, například 80-1024, pro port. Můžete definovat tolik filtrů, kolik potřebujete.

4. Vyberte **OK**.

Po vypršení časového limitu nastaveného pro zachytávání paketů je zachytávání paketů zastaveno a lze ho zkontrolovat. Můžete také ručně zastavit relaci zachytávání paketů.

> [!NOTE]
> Portál automaticky:
>  * Vytvoří sledovací proces sítě ve stejné oblasti, ve které se nachází virtuální počítač, ve kterém jste zvolili, pokud tato oblast ještě nemá sledovací proces sítě.
>  * Přidá rozšíření virtuálního počítače se [systémem](../virtual-machines/extensions/network-watcher-windows.md) *AzureNetworkWatcherExtension* [Linux](../virtual-machines/extensions/network-watcher-linux.md) nebo Windows k virtuálnímu počítači, pokud ještě není nainstalované.

## <a name="delete-a-packet-capture"></a>Odstranění zachytávání paketů

1. V zobrazení zachytávání paketů vyberte **...** na pravé straně zachytávání paketů nebo klikněte pravým tlačítkem na existující zachytávání paketů a vyberte **Odstranit**.
2. Zobrazí se výzva, abyste potvrdili, že chcete odstranit zachytávání paketů. Vyberte **Ano**.

> [!NOTE]
> Odstraněním zachytávání paketů nedojde k odstranění digitalizačního souboru v účtu úložiště nebo na virtuálním počítači.

## <a name="stop-a-packet-capture"></a>Zastavení zachytávání paketů

V zobrazení zachytávání paketů vyberte **...** na pravé straně zachytávání paketů nebo klikněte pravým tlačítkem na existující zachytávání paketů a vyberte **zastavit**.

## <a name="download-a-packet-capture"></a>Stažení zachytávání paketů

Po dokončení relace zachycení paketů se zachytávací soubor nahraje do úložiště objektů BLOB nebo do místního souboru na virtuálním počítači. Umístění úložiště zachytávání paketů je definované během vytváření zachycení paketů. Pohodlný nástroj pro přístup k zachyceným souborům uloženým do účtu úložiště je Průzkumník služby Microsoft Azure Storage, který si můžete [Stáhnout](https://storageexplorer.com/).

Pokud je zadaný účet úložiště, soubory zachytávání paketů se uloží do účtu úložiště v následujícím umístění:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

Pokud jste při vytváření zachytávání vybrali možnost **soubor** , můžete zobrazit nebo stáhnout soubor z cesty, kterou jste nakonfigurovali na virtuálním počítači.

## <a name="next-steps"></a>Další kroky

- Informace o automatizaci zachycení paketů s výstrahami virtuálních počítačů najdete v tématu [Vytvoření aktivované výstrahy zachytávání paketů](network-watcher-alert-triggered-packet-capture.md).
- Informace o tom, jestli je konkrétní provoz na virtuálním počítači povolený, najdete v tématu [Diagnostika problému s filtrováním síťového provozu virtuálního počítače](diagnose-vm-network-traffic-filtering-problem.md).