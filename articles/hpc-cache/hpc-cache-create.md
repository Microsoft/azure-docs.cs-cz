---
title: Vytvoření mezipaměti Azure HPC
description: Jak vytvořit instanci mezipaměti Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 11/11/2019
ms.author: rohogue
ms.openlocfilehash: c6090d19ce530829b79dca69636c2123e2519961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129557"
---
# <a name="create-an-azure-hpc-cache"></a>Vytvoření mezipaměti Azure HPC

K vytvoření mezipaměti použijte portál Azure.

![snímek obrazovky s přehledem mezipaměti na Webu Azure Portal s tlačítkem Vytvořit dole](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Definování základních podrobností

![snímek obrazovky se stránkou s podrobnostmi o projektu na webu Azure Portal](media/hpc-cache-create-basics.png)

V **části Podrobnosti projektu**vyberte předplatný a skupinu prostředků, která bude hostovat mezipaměť. Ujistěte se, že předplatné je v seznamu [přístupu.](hpc-cache-prereqs.md#azure-subscription)

V **části Podrobnosti o službě**nastavte název mezipaměti a tyto další atributy:

* Umístění – vyberte jednu z [podporovaných oblastí](hpc-cache-overview.md#region-availability).
* Virtuální síť – můžete vybrat existující nebo vytvořit novou virtuální síť.
* Podsíť – zvolte nebo vytvořte podsíť s alespoň 64 IP adresami (/24), která se použije jenom pro tuto instanci mezipaměti Azure HPC.

## <a name="set-cache-capacity"></a>Nastavení kapacity mezipaměti
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Na stránce **Mezipaměť** je nutné nastavit kapacitu mezipaměti. Zde nastavené hodnoty určují, kolik dat může vaše mezipaměť uchovat a jak rychle může obsluhovat požadavky klientů.

Kapacita také ovlivňuje náklady mezipaměti.

Zvolte kapacitu nastavením těchto dvou hodnot:

* Maximální rychlost přenosu dat pro mezipaměť (propustnost) v GB/s
* Velikost úložiště přiděleného pro data uložená v mezipaměti v TB

Zvolte jednu z dostupných hodnot propustnosti a velikosti úložiště mezipaměti.

Mějte na paměti, že skutečná rychlost přenosu dat závisí na zatížení, rychlosti sítě a typu cílů úložiště. Hodnoty, které zvolíte, nastaví maximální propustnost pro celý systém mezipaměti, ale některé z těchto hodnot se používají pro režijní úlohy. Pokud například klient požaduje soubor, který ještě není uložený v mezipaměti, nebo pokud je soubor označen jako zastaralý, vaše mezipaměť používá část své propustnosti k jeho načtení z back-endového úložiště.

Azure HPC Cache spravuje, které soubory jsou uloženy do mezipaměti a předinstalovány, aby se maximalizovaly míry přístupů do mezipaměti. Obsah mezipaměti je průběžně vyhodnocován a soubory jsou přesunuty do dlouhodobého úložiště, pokud jsou méně často přístupné. Zvolte velikost úložiště mezipaměti, která může pohodlně pojmout aktivní sadu pracovních souborů s dalším prostorem pro metadata a další režii.

![snímek obrazovky stránky pro změna velikosti mezipaměti](media/hpc-cache-create-capacity.png)

## <a name="add-resource-tags-optional"></a>Přidání značek prostředků (volitelné)

Stránka **Značky** umožňuje přidat [značky prostředků](https://go.microsoft.com/fwlink/?linkid=873112) do instance mezipaměti Azure HPC.

## <a name="finish-creating-the-cache"></a>Dokončení vytváření mezipaměti

Po konfiguraci nové mezipaměti klikněte na kartu **Revize + vytvoření.** Portál ověřuje vaše výběry a umožňuje zkontrolovat vaše volby. Pokud je vše v pořádku, klepněte na tlačítko **Vytvořit**.

Vytvoření mezipaměti trvá přibližně 10 minut. Průběh můžete sledovat na panelu oznámení na portálu Azure.

![snímek obrazovky se stránkami vytváření mezipaměti "nasazení probíhá" a "oznámení" na portálu](media/hpc-cache-deploy-status.png)

Po dokončení vytváření se zobrazí oznámení s odkazem na novou instanci mezipaměti Azure HPC a mezipaměť se zobrazí v seznamu **prostředků** vašeho předplatného.
<!-- double check on notification -->

![snímek obrazovky s instancí Azure HPC Cache na webu Azure Portal](media/hpc-cache-new-overview.png)

## <a name="next-steps"></a>Další kroky

Po zobrazení mezipaměti v seznamu **Zdroje** definujte cíle úložiště, které vám pomohou přístup k vašim zdrojům dat.

* [Přidání cílů úložiště](hpc-cache-add-storage.md)
