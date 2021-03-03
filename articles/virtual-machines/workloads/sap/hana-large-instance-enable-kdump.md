---
title: Skript pro povolení kdump v SAP HANA (velké instance) | Microsoft Docs
description: Skript pro povolení kdump v SAP HANA (velké instance) HLI typu I, HLI typ II
services: virtual-machines-linux
documentationcenter: ''
author: prtyag
manager: hrushib
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/30/2020
ms.author: prtyag
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4a9b3d534e74ccd44dfaa35ec5bf3b65f50641c7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101666655"
---
# <a name="kdump-for-sap-hana-on-azure-large-instances-hli"></a>Kdump pro SAP HANA ve velkých instancích Azure (HLI)

Konfigurace a povolení kdump je krok, který je nutný k řešení potíží se systémovými chybami, které nemají jasnou příčinu.
V některých případech dojde k neočekávanému selhání systému, který nelze vysvětlit při potížích s hardwarem nebo infrastrukturou.
V těchto případech může dojít k potížím s operačním systémem nebo aplikací a kdump umožní SUSE zjistit, proč došlo k chybě systému.

## <a name="enable-kdump-service"></a>Povolit službu kdump

Tento dokument popisuje podrobnosti o tom, jak povolit službu kdump ve velké instanci Azure HANA (**typ I a typ II**).

## <a name="supported-skus"></a>Podporované SKU

|  Typ velké instance Hana   |  Dodavatel operačního systému   |  Verze balíčku operačního systému   |  SKU |
|-----------------------------|--------------|-----------------------|-------------|
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S224m      |
|   Typ I                    |  SuSE        |   SLES 12 SP4         |  S224m      |
|   Typ I                    |  SuSE        |   SLES 12 SP2         |  S72        |
|   Typ I                    |  SuSE        |   SLES 12 SP2         |  S72m       |
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S72m       |
|   Typ I                    |  SuSE        |   SLES 12 SP2         |  S96        |
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S96        |
|   Typ I                    |  SuSE        |   SLES 12 SP2         |  S192       |
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S192       |
|   Typ I                    |  SuSE        |   SLES 12 SP4         |  S192       |
|   Typ I                    |  SuSE        |   SLES 12 SP2         |  S192m      |
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S192m      |
|   Typ I                    |  SuSE        |   SLES 12 SP4         |  S192m      |
|   Typ I                    |  SuSE        |   SLES 12 SP2         |  S144       |
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S144       |
|   Typ I                    |  SuSE        |   SLES 12 SP2         |  S144m      |
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S144m      |
|   Typ II                   |  SuSE        |   SLES 12 SP2         |  S384       |
|   Typ II                   |  SuSE        |   SLES 12 SP3         |  S384       |
|   Typ II                   |  SuSE        |   SLES 12 SP4         |  S384       |
|   Typ II                   |  SuSE        |   SLES 12 SP2         |  S384xm     |
|   Typ II                   |  SuSE        |   SLES 12 SP3         |  S384xm     |
|   Typ II                   |  SuSE        |   SLES 12 SP4         |  S384xm     |
|   Typ II                   |  SuSE        |   SLES 12 SP2         |  S576m      |
|   Typ II                   |  SuSE        |   SLES 12 SP3         |  S576m      |
|   Typ II                   |  SuSE        |   SLES 12 SP4         |  S576m      |

## <a name="prerequisites"></a>Požadavky

- Služba kdump používá `/var/crash` adresář k zápisu výpisů paměti, ujistěte se, že oddíl odpovídající tomuto adresáři má dostatek místa pro ukládání výpisů paměti.

## <a name="setup-details"></a>Podrobnosti o nastavení

- Skript, který povoluje kdump, najdete [tady](https://github.com/Azure/sap-hana/blob/master/tools/enable-kdump.sh) .
> [!NOTE]
> Tento skript se provede na základě našeho nastavení testovacího prostředí a zákazník se očekává, že se obrátí na dodavatele operačního systému za účelem dalšího ladění.
> Pro nové a stávající servery pro ukládání výpisů paměti se bude zřizovat samostatná logická jednotka (LUN) a skript se postará o konfiguraci systému souborů z logické jednotky (LUN).
> Společnost Microsoft nebude zodpovědná za analýzu výpisu paměti. Aby mohl zákazník získat analýzu, musí otevřít lístek s dodavatelem operačního systému.

- Spusťte tento skript pro rozsáhlou instanci HANA pomocí příkazu níže.

    > [!NOTE]
    > sudo oprávnění potřebné ke spuštění tohoto příkazu.

    ```bash
    sudo bash enable-kdump.sh
    ```

- Pokud je příkaz kdump Outputs úspěšně povolený, nezapomeňte restartovat systém, aby se změny projevily úspěšně.

- Pokud se výstupu příkazu nepovedlo provést určitou operaci, ukončí se!!!!, služba kdump není povolená. Informace najdete v části [problém podpory](#support-issue).

## <a name="test-kdump"></a>Kdump testu

> [!NOTE]
>  Operace níže spustí selhání jádra a restartování systému.

- Aktivace havárie jádra

    ```bash
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

## <a name="related-documents"></a>Související dokumenty
- Další informace o [konfiguraci kdump](https://www.suse.com/support/kb/doc/?id=3374462)
