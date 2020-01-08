---
title: Vytvoření mezipaměti prostředí Azure HPC
description: Vytvoření instance mezipaměti prostředí Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: rohogue
ms.openlocfilehash: 793a80e7019e72c1cb3087da02d5642639cb8d5e
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647152"
---
# <a name="create-an-azure-hpc-cache"></a>Vytvoření mezipaměti prostředí Azure HPC

K vytvoření mezipaměti použijte Azure Portal.

![snímek obrazovky s přehledem mezipaměti v Azure Portal s tlačítkem vytvořit v dolní části](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Definovat základní podrobnosti

![snímek stránky s podrobnostmi projektu v Azure Portal](media/hpc-cache-create-basics.png)

V části **Project Details (podrobnosti projektu**) vyberte předplatné a skupinu prostředků, které budou hostovat mezipaměť. Ujistěte se, že je odběr v seznamu [přístupu](hpc-cache-prereqs.md#azure-subscription) .

V části **Podrobnosti o službě**nastavte název mezipaměti a tyto ostatní atributy:

* Umístění – vyberte jednu z [podporovaných oblastí](hpc-cache-overview.md#region-availability).
* Virtuální síť – můžete vybrat existující virtuální síť nebo vytvořit novou.
* Podsíť – vyberte nebo vytvořte podsíť s aspoň 64 IP adresami (/24), která se použije jenom pro tuto instanci mezipaměti Azure HPC.

## <a name="set-cache-capacity"></a>Nastavit kapacitu mezipaměti
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Na stránce **mezipaměť** musíte nastavit kapacitu mezipaměti. Nastavené hodnoty určují, kolik dat vaše mezipaměť může uchovávat a jak rychle může obsluhovat požadavky klientů.

Kapacita má vliv také na náklady na mezipaměť.

Určete kapacitu nastavením těchto dvou hodnot:

* Maximální rychlost přenosu dat pro mezipaměť (propustnost), v GB/s
* Velikost úložiště přidělená pro data uložená v mezipaměti v TB

Vyberte jednu z dostupných hodnot propustnosti a velikosti úložiště mezipaměti.

Pamatujte, že skutečná rychlost přenosu dat závisí na zatížení, rychlosti sítě a typu cílů úložiště. Hodnoty, které zvolíte, nastaví maximální propustnost pro celý systém mezipaměti, ale některé z nich se použijí pro úlohy s režijními náklady. Pokud třeba klient požaduje soubor, který už není uložený v mezipaměti, nebo pokud je soubor označený jako zastaralý, mezipaměť použije určitou propustnost k načtení z back-endu úložiště.

Azure HPC cache spravuje, které soubory jsou uložené do mezipaměti a předem se načítají, aby se maximalizovala míra přístupů do mezipaměti. Obsah mezipaměti se průběžně vyhodnocuje a soubory se přesunou do dlouhodobého úložiště, když se k nim méně často přistupuje. Vyberte velikost úložiště mezipaměti, která může pohodlně uchovávat aktivní sadu pracovních souborů a další místo pro metadata a další režii.

![snímek stránky s nastavením velikosti mezipaměti](media/hpc-cache-create-capacity.png)

## <a name="add-resource-tags-optional"></a>Přidat značky prostředků (volitelné)

Stránka **značky** umožňuje přidat [značky prostředků](https://go.microsoft.com/fwlink/?linkid=873112) do instance mezipaměti prostředí Azure HPC.

## <a name="finish-creating-the-cache"></a>Dokončení vytváření mezipaměti

Po konfiguraci nové mezipaměti klikněte na kartu **Revize + vytvořit** . Portál ověří vaše výběry a umožní vám zkontrolovat své volby. Pokud je vše správné, klikněte na **vytvořit**.

Vytvoření mezipaměti trvá přibližně 10 minut. Průběh můžete sledovat na panelu oznámení Azure Portal.

![snímek obrazovky se stránkou nasazení v mezipaměti a stránkami oznámení na portálu](media/hpc-cache-deploy-status.png)

Po dokončení vytváření se zobrazí oznámení s odkazem na novou instanci mezipaměti Azure HPC a mezipaměť se zobrazí v seznamu **prostředků** vašeho předplatného.
<!-- double check on notification -->

![snímek obrazovky instance mezipaměti HPC Azure v Azure Portal](media/hpc-cache-new-overview.png)

## <a name="next-steps"></a>Další kroky

Po zobrazení mezipaměti v seznamu **prostředky** definujte cíle úložiště, aby přístup k vašemu datovému zdroji poskytovala vaše mezipaměť.

* [Přidat cíle úložiště](hpc-cache-add-storage.md)
