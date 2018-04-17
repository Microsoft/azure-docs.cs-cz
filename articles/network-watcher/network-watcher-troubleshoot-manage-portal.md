---
title: Řešení potíží s brány virtuální sítě Azure a připojení - portál | Microsoft Docs
description: Tato stránka vysvětluje, jak používat Azure sledovací proces sítě řešení portálu
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: f6f0a813-38b6-4a1f-8cfc-1dfdf979f595
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: becc4f799e34aabba5c703f09e51a4b7d497c45a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-network-watcher-from-azure-portal"></a>Řešení potíží s brány virtuální sítě a připojení pomocí sledovací proces sítě z portálu Azure

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-troubleshoot-manage-portal.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI 1.0](network-watcher-troubleshoot-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

Sledovací proces sítě obsahuje řadu funkcí ve vztahu k pochopení síťovým prostředkům v Azure. Jeden z těchto funkcí je prostředek řešení potíží. Řešení potíží s prostředků je možné volat prostřednictvím portálu, prostředí PowerShell, rozhraní příkazového řádku nebo REST API. Při volání, sledovací proces sítě kontroluje stav bránu virtuální sítě nebo připojení a vrátí nalezených výsledcích.

## <a name="before-you-begin"></a>Než začnete

Tento scénář předpokládá, že už jste udělali kroky v [vytvořit sledovací proces sítě](network-watcher-create.md) vytvořit sledovací proces sítě.

Seznam podporovaných brány typy návštěvě [typy podporované brány](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Přehled

Řešení potíží s prostředků nabízí možnost odstraňování problémů, které nastat u brány virtuální sítě a připojení. Po odeslání žádosti k prostředku, řešení potíží s protokoly Probíhá dotaz a prověřovány. Po dokončení kontroly budou vráceny výsledky. Prostředek, který řešení problémů s požadavky jsou dlouho spuštěný žádosti, což může trvat několik minut vrácení výsledku. Protokoly z řešení potíží se ukládají v kontejneru v účtu úložiště, který je zadán.

## <a name="troubleshoot-a-gateway-or-connection"></a>Řešení potíží s bránu nebo připojení

1. Přejděte na [portál Azure](https://portal.azure.com) a klikněte na tlačítko **sítě** > **sledovací proces sítě** > **Diagnostika sítě VPN**
2. Vyberte **předplatné**, **skupiny prostředků**, a **umístění**.
3. Řešení potíží s prostředků vrátí data o stavu prostředku. Navíc šetří relevantní protokoly na účet úložiště mají být zkontrolovány. Klikněte na tlačítko **účet úložiště** a vyberte účet úložiště.
4. Na **účty úložiště** okně vyberte stávající účet úložiště, nebo klikněte na **+ účet úložiště** vytvoříte nový.
5. Na **kontejnery** okně zvolte existující kontejner, nebo klikněte na tlačítko **+ kontejner** vytvořit nový kontejner. Po dokončení klikněte na tlačítko **vyberte**
6. Vyberte prostředky, brány a připojení k řešení potíží a klikněte na tlačítko **spustit Poradce při potížích**

Pokud je vybraných víc zdrojů, řešení potíží s běží souběžně na prostředky brány. Nelze spustit na připojení a je přiřazen brány ve stejnou dobu. Doporučuje se odstraňování potíží s bránami nejprve odstraňování problémů s připojením je delší proces. Diagnostika sítě VPN spuštěného na prostředku **stav řešení potíží s** sloupec bude zobrazovat stav **systémem**

Po dokončení ve sloupci stav změní na **stavu v pořádku** nebo **není v pořádku**.

![řešení potíží s dokončení][2]

## <a name="understanding-the-results"></a>Seznámení s výsledky

V **podrobnosti** části okna, **stav** kartě se zobrazují stav poslední Poradce při potížích spuštění na vybrané prostředky. Výsledky nejnovější diagnostiky jsou uvedené xx minut po posledním spuštění.

|Vlastnost  |Popis  |
|---------|---------|
|Prostředek     | Odkaz na prostředek.        |
|Cestu k úložišti     |  Cesta k účtu úložiště a kontejneru, které obsahují protokolů (Pokud žádné byly vytvořeny během spuštění). Toto nastavení není zachována po opuštění portálu.        |
|Souhrn     | Shrnutí stavu prostředků.        |
|Detail     | Podrobné informace o stavu prostředků.        |
|Naposledy spuštěno     | Čas poslední čas Poradce při potížích se spustil.        |


**Akce** karta obsahuje obecné pokyny o tom, jak problém vyřešit. Pokud může být akce pro tento problém, je k dispozici odkaz s další pokyny. V případě, že tam, kde není žádná další pokyny, odpověď obsahuje adresu url pro otevření případu podpory.  Další informace o vlastnostech odpovědi a co je součástí najdete v článku [řešení sledovací proces sítě – přehled](network-watcher-troubleshoot-overview.md)


## <a name="next-steps"></a>Další postup

Pokud nastavení bylo změněno tohoto připojení VPN zastavit, přečtěte si téma [spravovat skupiny zabezpečení sítě](../virtual-network/manage-network-security-group.md) sledovat pravidla zabezpečení sítě skupiny a zabezpečení, které může být nejistá.


[2]: ./media/network-watcher-troubleshoot-manage-portal/2.png
