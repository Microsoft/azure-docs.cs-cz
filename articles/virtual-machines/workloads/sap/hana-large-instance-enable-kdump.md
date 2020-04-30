---
title: Skript pro povolení kdump v SAP HANA (velké instance) | Microsoft Docs
description: Skript pro povolení kdump v SAP HANA (velké instance) HLI typu I, HLI typ II
services: virtual-machines-linux
documentationcenter: ''
author: prtyag
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/30/2020
ms.author: prtyag
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 16dc15b4369904643d0138a4b8e5b94c47868d31
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204933"
---
# <a name="enable-kdump-service"></a>Povolit službu kdump

Tento dokument popisuje podrobnosti o tom, jak povolit službu kdump ve velké instanci Azure HANA (**typ I a typ II**).

## <a name="supported-skus"></a>Podporované SKU

|  Typ velké instance Hana   |  Dodavatel operačního systému   |  Verze balíčku operačního systému   |  Skladová jednotka (SKU)        |
|-----------------------------|--------------|-----------------------|-------------|
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S224m      |
|   Typ I                    |  SuSE        |   SLES 12 SP4         |  S224m      |
|   Typ I                    |  SuSE        |   SLES 12 SP2         |  S72m       |
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S72m       |
|   Typ I                    |  SuSE        |   SLES 12 SP2         |  S96        |
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S96        |
|   Typ II                   |  SuSE        |   SLES 12 SP3         |  S384       |
|   Typ II                   |  SuSE        |   SLES 12 SP3         |  S576m      |
|   Typ II                   |  SuSE        |   SLES 12 SP3         |  S384xm     |
|   Typ II                   |  SuSE        |   SLES 12 SP4         |  S384xm     |

## <a name="prerequisites"></a>Požadavky

- Služba kdump používá `/var/crash` adresář k zápisu výpisů paměti, ujistěte se, že oddíl odpovídající tomuto adresáři má dostatek místa pro ukládání výpisů paměti.

## <a name="setup-details"></a>Podrobnosti o nastavení

- Skript, který povoluje kdump, najdete [tady](https://github.com/Azure/sap-hana/blob/master/tools/enable-kdump.sh) .

- Spusťte tento skript pro rozsáhlou instanci HANA pomocí příkazu níže.

    > [!NOTE]
    > sudo oprávnění potřebné ke spuštění tohoto příkazu.

    ```bash
    sudo bash enable-kdump.sh
    ```

- Pokud je příkaz kdump Outputs úspěšně povolený, restartujte prosím systém, aby se změna projevila, a pak se kdump úspěšně povolí. Změny se projeví až po restartování systému.

- Pokud se výstupu příkazu nepovedlo provést určitou operaci, ukončí se!!!!, služba kdump není povolená. Informace najdete v části [problém podpory](#support-issue).

## <a name="test-kdump"></a>Kdump testu

> [!NOTE]
>  Operace níže spustí selhání jádra a restartování systému.

- Aktivace havárie jádra

    ```bash
    echo 1 > /proc/sys/kernel/sysrq
    echo c > /proc/sysrq-trigger
    ```

- Po úspěšném restartování systému vyhledejte v `/var/crash` adresáři protokoly selhání jádra.

- Pokud `/var/crash` má adresář s aktuálním datem, je kdump úspěšně povolen.

## <a name="support-issue"></a>Problém podpory

Pokud se skript nepovede s chybou nebo kdump není povolený, vyvolejte žádost o službu s týmem podpory Microsoftu s následujícími podrobnostmi.

* ID předplatného HLI

* Název serveru

* Dodavatel operačního systému

* Verze operačního systému

* Verze jádra
