---
title: Poznámky k verzi obecné dostupnosti brány Azure Data Box| Dokumenty společnosti Microsoft
description: Popisuje kritické otevřené problémy a řešení pro bránu Azure Data Box Gateway, která používá obecnou dostupnost.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/26/2019
ms.author: alkohli
ms.openlocfilehash: f4ee3a5bd754335ab1c7f124671e9c37307a6a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265399"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Poznámky k verzi verze pro obecné dostupnosti brány Azure Data Box Edge/Azure Data Box Gateway

## <a name="overview"></a>Přehled

Následující poznámky k verzi identifikují důležité problémy s otevřením a vyřešené problémy pro vydání obecné dostupnosti (GA) pro Azure Data Box Edge a Azure Data Box Gateway.

Poznámky k verzi jsou průběžně aktualizovány a jako kritické problémy, které vyžadují řešení jsou zjištěny, jsou přidány. Před nasazením brány Data Box Edge/Data Box Gateway pečlivě zkontrolujte informace obsažené v poznámkách k verzi.

Verze GA odpovídá verzím softwaru:

- **Brána datové schránky 1903 (1.5.814.447)**
- **Datová schránka Edge 1903 (1.5.814.447)**


## <a name="whats-new"></a>Co je nového

- **Nové image virtuálních disků** – Nové VHDX a VMDK jsou teď dostupné na webu Azure Portal. Stáhněte si tyto obrázky a zřiďte, nakonfigurujte a nasaďte nová zařízení GA brány datové schránky. Zařízení brány datové schránky vytvořená v dřívějších verzích preview nelze aktualizovat na tuto verzi. Další informace najdete v části [Příprava na nasazení brány Azure Data Box Gateway](data-box-gateway-deploy-prep.md).
- **Podpora služby NFS** – podpora služby NFS je momentálně ve verzi Preview a je k dispozici pro klienty v3.0 a v4.1, kteří přistupují k zařízením Data Box Edge a Data Box Gateway.
- **Odolnost úložiště** – vaše zařízení Data Box Edge může odolat selhání jednoho datového disku pomocí funkce odolnosti úložiště. Tato funkce je aktuálně ve verzi Preview. Odolnost úložiště můžete povolit výběrem možnosti **Pružnost** v **nastavení úložiště** v místním webovém uživatelském prostředí.


## <a name="known-issues-in-ga-release"></a>Známé problémy ve verzi GA

Následující tabulka obsahuje souhrn známých problémů pro bránu datové schránky spuštěnou verzi.

| Ne. | Funkce | Problém | Řešení/komentáře |
| --- | --- | --- | --- |
| **1.** |Typy souborů | Následující typy souborů nejsou podporovány: soubory znaků, blokové soubory, sokety, kanály, symbolické odkazy.  |Kopírování těchto souborů má za následek 0-délka souborů, které jsou vytvořeny ve sdílené složce systému souborů NFS. Tyto soubory zůstávají v chybovém stavu a jsou také hlášeny v *error.xml*. <br> Symbolické odkazy na adresáře mají za následek adresáře nikdy označeny offline. V důsledku toho se nemusí zobrazit šedý kříž v adresářích, který označuje, že adresáře jsou offline a veškerý přidružený obsah byl zcela odeslán do Azure. |
| **2.** |Odstranění | Z důvodu chyby v této verzi, pokud je sdílená společnost nfs odstraněna, sdílení nemusí být odstraněno. Ve stavu sdílené složky se zobrazí *odstranění*.  |K tomu dochází pouze v případě, že sdílená složka používá nepodporovaný název souboru. |
| **3.** |Kopírovat | Kopírování dat se nezdaří s chybou: Požadovanou operaci nelze dokončit z důvodu omezení systému souborů.  |Alternativní datový proud (ADS) přidružený k velikosti souboru větší než 128 kB není podporován.   |


## <a name="next-steps"></a>Další kroky

- [Připravte se na nasazení brány azure data boxů](data-box-gateway-deploy-prep.md).
- [Připravte se na nasazení Azure Data Box Edge](data-box-edge-deploy-prep.md).
