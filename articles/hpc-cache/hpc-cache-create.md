---
title: Vytvoření mezipaměti prostředí Azure HPC (verze Preview)
description: Vytvoření instance mezipaměti prostředí Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: tutorial
ms.date: 09/24/2019
ms.author: v-erkell
ms.openlocfilehash: a0590c14032595bea685c69962ef27dca14d1d69
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300021"
---
# <a name="create-an-azure-hpc-cache-preview"></a>Vytvoření mezipaměti prostředí Azure HPC (verze Preview)

K vytvoření mezipaměti použijte Azure Portal. 

![snímek obrazovky s přehledem mezipaměti v Azure Portal s tlačítkem vytvořit v dolní části](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Definovat základní podrobnosti

![snímek stránky s podrobnostmi projektu v Azure Portal](media/hpc-cache-create-basics.png)

V části **Project Details (podrobnosti projektu**) vyberte předplatné a skupinu prostředků, které budou hostovat mezipaměť. Ujistěte se, že je předplatné v seznamu [přístupu Preview](hpc-cache-prereqs.md#azure-subscription) .

V části **Podrobnosti o službě**nastavte název mezipaměti a tyto ostatní atributy:

* Umístění – vyberte jednu z [podporovaných oblastí](hpc-cache-overview.md#region-availability).
* Virtuální síť – můžete vybrat existující virtuální síť nebo vytvořit novou.
* Podsíť – vyberte nebo vytvořte podsíť s aspoň 64 IP adresami (/24), která se použije jenom pro tuto instanci mezipaměti Azure HPC.

## <a name="set-cache-capacity"></a>Nastavit kapacitu mezipaměti
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Na stránce **mezipaměť** musíte nastavit kapacitu mezipaměti. Tato hodnota určuje, kolik dat vaše mezipaměť může uchovávat a jak rychle může obsluhovat požadavky klientů. 

Po období verze Public Preview ovlivní kapacita také náklady na mezipaměť.

Kapacita mezipaměti se měří ve vstupních/výstupních operacích za sekundu (IOPS). Určete kapacitu nastavením těchto dvou hodnot:

* Maximální rychlost přenosu dat pro mezipaměť (propustnost), v GB/s
* Velikost úložiště přidělená pro data uložená v mezipaměti v TB

Vyberte jednu z dostupných hodnot propustnosti a velikosti úložiště mezipaměti. Kapacita IOPS se vypočítá a zobrazí se pod selektory hodnot.

Pamatujte, že skutečná rychlost přenosu dat závisí na zatížení, rychlosti sítě a typu cílů úložiště. Hodnota, kterou zvolíte, nastaví maximální propustnost pro celou mezipaměť a ne všechny jsou k dispozici pro požadavky klientů. Pokud třeba klient požaduje soubor, který už není uložený v mezipaměti, nebo pokud je soubor označený jako zastaralý, mezipaměť použije určitou propustnost k načtení z back-endu úložiště.

Azure HPC cache spravuje, které soubory jsou uložené do mezipaměti a předem se načítají, aby se maximalizovala míra přístupů do mezipaměti. Obsah mezipaměti se průběžně vyhodnocuje a soubory se přesunou do dlouhodobého úložiště, když se k nim méně často přistupuje. Vyberte velikost úložiště mezipaměti, která může pohodlně uchovávat aktivní sadu pracovních souborů a další místo pro metadata a další režii.

![snímek stránky s nastavením velikosti mezipaměti](media/hpc-cache-create-iops.png)

## <a name="add-storage-targets"></a>Přidání cílů úložiště

Cíle úložiště jsou back-endové dlouhodobé úložiště pro obsah vaší mezipaměti.

Při vytváření mezipaměti můžete definovat cíle úložiště, ale můžete je také přidat později pomocí odkazu v části **Konfigurace** stránky vaší mezipaměti na portálu.

![snímek obrazovky se stránkou cílů úložiště](media/hpc-cache-storage-targets-pop.png)

Kliknutím na **odkaz Přidat cíl úložiště** definujte své back-endové úložné systémy. Úložiště může být kontejnery objektů blob Azure nebo místní systémy souborů NFS.

Můžete definovat až deset různých cílů úložiště.

Podrobné pokyny pro přidání cíle úložiště jsou zahrnuté v tématu [Přidání cílů úložiště](hpc-cache-add-storage.md). Postup se liší pro úložiště objektů BLOB nebo pro export souborů NFS.

Tady je několik tipů:

* U obou typů úložiště musíte určit, jak se má najít back-end systém úložiště (buď adresa NFS, nebo název kontejneru objektů BLOB) a cesta k oboru názvů směřující na klienta.

* Při vytváření cíle úložiště objektů BLOB se ujistěte, že má mezipaměť přístupová oprávnění k účtu úložiště, jak je popsáno v tématu [Přidání rolí řízení přístupu](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account). Pokud si nejste jistí, že konfigurace role bude úspěšná, vytvořte nejdřív mezipaměť a pak přidejte úložiště objektů BLOB.

* Při vytváření cíle úložiště NFS zadejte [model využití](hpc-cache-add-storage.md#choose-a-usage-model). Nastavení modelu využití pomáhá mezipaměti optimalizovat váš pracovní postup.

## <a name="add-resource-tags-optional"></a>Přidat značky prostředků (volitelné)

Stránka **značky** umožňuje přidat [značky prostředků](https://go.microsoft.com/fwlink/?linkid=873112) do instance mezipaměti prostředí Azure HPC.

## <a name="finish-creating-the-cache"></a>Dokončení vytváření mezipaměti

Po konfiguraci nové mezipaměti klikněte na kartu **Revize + vytvořit** . Portál ověří vaše výběry a umožní vám zkontrolovat své volby. Pokud je vše správné, klikněte na **vytvořit**. 

Vytvoření mezipaměti trvá přibližně 10 minut. Průběh můžete sledovat na panelu oznámení Azure Portal. 

![snímek obrazovky se stránkou nasazení v mezipaměti a stránkami oznámení na portálu](media/hpc-cache-deploy-status.png)

Po dokončení vytváření se zobrazí oznámení s odkazem na novou instanci mezipaměti Azure HPC a mezipaměť se zobrazí v seznamu **prostředků** vašeho předplatného. 

![snímek obrazovky instance mezipaměti HPC Azure v Azure Portal](media/hpc-cache-new-overview.png)

## <a name="next-steps"></a>Další kroky

Po zobrazení mezipaměti v seznamu **prostředky** ji můžete připojit pro klientský přístup, použít ji k přesunutí dat pracovní sady do nového cíle úložiště objektů BLOB v Azure nebo definování dalších zdrojů dat.

* [Připojení mezipaměti HPC Azure](hpc-cache-mount.md)
* [Přesun dat do Azure Blob Storage pro Azure HPC cache](hpc-cache-ingest.md)
* [Přidat cíle úložiště](hpc-cache-add-storage.md)
