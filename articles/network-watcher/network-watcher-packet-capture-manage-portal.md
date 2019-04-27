---
title: Správa zachytávání paketů pomocí služby Azure Network Watcher – Azure portal | Dokumentace Microsoftu
description: Další informace o správě funkce zachytávání paketů služby Network Watcher pomocí webu Azure portal.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 59edd945-34ad-4008-809e-ea904781d918
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: kumud
ms.openlocfilehash: 22bdd50f129a48ade97db323f904f7e652a00d39
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725852"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Správa zachytávání paketů pomocí služby Azure Network Watcher pomocí portálu

Zachytávání paketů Network Watcher umožňuje vytvořit relace zachycení sledujte provoz do a z virtuálního počítače. Filtry jsou k dispozici pro relace zachytávání zajistit, že zachytíte jenom provoz, který chcete. Zachytávání paketů pomáhá diagnostikovat sítě anomálie reaktivně i proaktivně. Další použití patří shromažďování statistik sítě, získáte informace o síti vniknutí pro komunikaci klient server ladění a mnoho dalšího. Nebudou moct vzdáleně aktivovat paketů zaznamená, snižuje zátěž ručně spustit zachytávání paketů na požadovaný virtuální počítač, což šetří cenný čas.

V tomto článku zjistíte, jak spustit, zastavit, stáhněte si a odstranit zachycení paketů. 

## <a name="before-you-begin"></a>Než začnete

Zachytávání paketů vyžaduje následující připojení:
* Odchozí připojení k účtu úložiště přes port 443.
* Příchozí a odchozí připojení k 169.254.169.254
* Příchozí a odchozí připojení k 168.63.129.16

Pokud skupinu zabezpečení sítě přidružené k síťové rozhraní nebo podsíť, která se síťové rozhraní nachází, ujistěte se, že existují pravidla, které umožní předchozí porty. 

## <a name="start-a-packet-capture"></a>Spustit zachytávání paketů

1. V prohlížeči přejděte [webu Azure portal](https://portal.azure.com) a vyberte **všechny služby**a pak vyberte **Network Watcher** v **části sítě**.
2. Vyberte **zachytávání paketů** pod **síťové diagnostické nástroje**. Bez ohledu na jejich stavu jsou uvedeny všechny existující zachycení paketů.
3. Vyberte **přidat** vytvořit zachytávání paketů. Můžete vybrat hodnoty pro následující vlastnosti:
   - **Předplatné**: Předplatné, pro zachycení virtuálního počítače, které chcete vytvořit paketu probíhá.
   - **Skupina prostředků**: Skupina prostředků virtuálního počítače.
   - **Cílit na virtuální počítač**: Virtuální počítač, který chcete vytvořit pro zachycení paketů.
   - **Název zachycení paketu**: Název zachycení paketů.
   - **Účet úložiště nebo soubor**: Vyberte **účtu úložiště**, **souboru**, nebo obojí. Pokud vyberete **souboru**, v zachytávání se zapisují do na cestu v rámci virtuálního počítače.
   - **Místní cesta k souboru**: Místní cestu na virtuálním počítači, kam bude uložena zachytávání paketů (platné pouze tehdy, když *souboru* je vybrána). Cesta musí být platná cesta. Pokud používáte virtuální počítač s Linuxem, musí cesta začínat */var/zachycuje*.
   - **Účty úložiště**: Vyberte existující účet úložiště, pokud jste vybrali *účtu úložiště*. Tato možnost je k dispozici, pokud jste vybrali pouze **úložiště**.
   
     > [!NOTE]
     > Účty úložiště úrovně Premium nejsou aktuálně podporovány pro ukládání paketů zachytí.

   - **Maximální počet bajtů na paket**: Počet bajtů z jednotlivých paketů, které jsou zachyceny. Pokud je ponecháno prázdné, jsou zachyceny všechny bajty.
   - **Maximální počet bajtů na relaci**: Celkový počet bajtů, které jsou zachyceny. Hodnota po dosažení zarážky zachycení paketů.
   - **Časový limit (sekundy)**: Časový limit před zastavením zachytávání paketů. Výchozí hodnota je 18 000 sekund.
   - Filtrování (nepovinné). Vyberte **+ přidat filtr**
     - **Protokol**: Protokol pro filtrování pro zachycení paketů. Dostupné hodnoty jsou TCP, UDP a všechny.
     - **Místní IP adresa**: Filtry paketů zachycení paketů, kde místní IP adresa odpovídá této hodnotě.
     - **Místní port**: Filtry paketů zachycení paketů, kde na místní port odpovídá této hodnotě.
     - **Vzdálená IP adresa**: Filtry paketů zachycení paketů, kde Vzdálená IP adresa odpovídá této hodnotě.
     - **Vzdálený port**: Pokud vzdálený port odpovídá této hodnotě filtry paketů zachycení paketů.
    
     > [!NOTE]
     > Hodnoty portu a IP adresa může být jedna hodnota, rozsah hodnot nebo rozsah, jako je například 80 – 1024, pro port. Můžete definovat tolik filtry podle potřeby.

4. Vyberte **OK**.

Po vypršení časového limitu určeného v zachytávání paketů zachycení paketů se zastaví a si můžete prohlédnout. Můžete také ručně zastavit relace zachytávání paketů.

> [!NOTE]
> Na portálu automaticky:
>  * Vytvoří sledovací proces sítě ve stejné oblasti jako oblast, kterou existuje virtuální počítač, který jste vybrali, pokud oblast nemá doposud sledovací proces sítě.
>  * Přidá *AzureNetworkWatcherExtension* [Linux](../virtual-machines/linux/extensions-nwa.md) nebo [Windows](../virtual-machines/windows/extensions-nwa.md) rozšíření virtuálního počítače k virtuálnímu počítači, pokud ještě není nainstalovaná.

## <a name="delete-a-packet-capture"></a>Odstranit zachycení paketů

1. V zobrazení pro zachytávání paketů, vyberte **...**  na pravé straně paketu zachycení, nebo klikněte pravým tlačítkem na existující zachycení paketů a vyberte **odstranit**.
2. Zobrazí se výzva k potvrzení, že chcete odstranit zachycení paketů. Vyberte **Ano**.

> [!NOTE]
> Odstraňuje se zachycení paketů nedojde k odstranění zachytávací soubor v účtu úložiště nebo na virtuálním počítači.

## <a name="stop-a-packet-capture"></a>Zastavit zachytávání paketů

V zobrazení pro zachytávání paketů, vyberte **...**  na pravé straně paketu zachycení, nebo klikněte pravým tlačítkem na existující zachycení paketů a vyberte **Zastavit**.

## <a name="download-a-packet-capture"></a>Stáhněte si zachytávání paketů

Po dokončení relace zachycení paketů zachytávací soubor nahraje do služby blob storage nebo do místního souboru na virtuálním počítači. Umístění úložiště pro zachytávání paketů je definován při vytváření zachytávání paketů. Praktický nástroj pro přístup k zachycení soubory uložené na účet úložiště je Microsoft Azure Storage Explorer, který můžete [Stáhnout](https://storageexplorer.com/).

Pokud je určen účet úložiště, jsou uloženy soubory zachytávání paketů do účtu úložiště v následujícím umístění:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

Pokud jste vybrali **souboru** při vytváření zachytávání můžete zobrazení nebo stažení souboru z cesty, které jste nakonfigurovali na virtuálním počítači.

## <a name="next-steps"></a>Další postup

- Zjistěte, jak automatizovat zachytávání paketů pomocí virtuálního počítače výstrahy, najdete v článku [vytvořit zachytávání paketů upozornění aktivovaných](network-watcher-alert-triggered-packet-capture.md).
- Pokud chcete zjistit, jestli konkrétní provoz je povolený, nebo z virtuálního počítače, naleznete v tématu [Diagnostika potíží s filtrováním virtuální počítač síťové přenosy](diagnose-vm-network-traffic-filtering-problem.md).
