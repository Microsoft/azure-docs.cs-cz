---
title: Poznámky k verzi Azure Data Box Gateway obecné dostupnosti | Microsoft Docs
description: Popisuje kritické otevřené problémy a řešení pro Azure Data Box Gateway, ve kterých je spuštěná verze s obecnou dostupností.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: 2984f7990b9570c5ec57633de7f7e50162fb6f46
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96582321"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Poznámky k verzi pro obecné dostupnosti Azure Data Box Edge/Azure Data Box Gateway

## <a name="overview"></a>Přehled

Následující poznámky k verzi identifikují kritické otevřené problémy a vyřešené problémy pro vydání obecné dostupnosti (GA) pro Azure Data Box Edge a Azure Data Box Gateway. 

Poznámky k verzi se průběžně aktualizují a při zjištění kritických problémů vyžadujících alternativní řešení se přidají. Před nasazením Data Box Edge/Data Box Gateway pečlivě zkontrolujte informace obsažené v poznámkách k verzi.

Verze GA odpovídá verzím softwaru:

- **Data Box Gateway 1903 (1.5.814.447)**
- **Data Box Edge 1903 (1.5.814.447)**


## <a name="whats-new"></a>Novinky

- **Nové image virtuálních disků** – nové soubory VHDX a VMDK jsou teď dostupné v Azure Portal. Stáhněte si tyto image, abyste zřídili, nakonfigurovali a nasadili nová Data Box Gateway zařízení GA. Zařízení Data Box Gateway vytvořená v dřívějších verzích Preview nelze aktualizovat na tuto verzi. Další informace najdete na webu [Příprava na nasazení Azure Data box Gateway](data-box-gateway-deploy-prep.md).
- **Podpora NFS** – podpora NFS je momentálně ve verzi Preview a dostupná pro klienty v 3.0 a v 4.1, kteří přistupují k Data Box Edge a data box Gatewaym zařízením.
- **Odolnost úložiště** – zařízení data box Edge může naodolat selhání jednoho datového disku pomocí funkce odolnosti úložiště. Tato funkce je aktuálně ve verzi Preview. Odolnost úložiště můžete povolit výběrem možnosti **odolnosti** v **Nastavení úložiště** v místním webovém uživatelském rozhraní.


## <a name="known-issues-in-ga-release"></a>Známé problémy ve verzi GA

Následující tabulka poskytuje souhrn známých problémů Data Box Gateway vydaných verzí.

| No. | Funkce | Problém | Alternativní řešení/komentáře |
| --- | --- | --- | --- |
| **1.** |Typy souborů | Následující typy souborů nejsou podporovány: soubory znaků, blokovat soubory, sokety, kanály, symbolické odkazy.  |Kopírování těchto souborů způsobí, že se ve sdílené složce NFS vytvoří soubory s délkou 0. Tyto soubory zůstávají v chybovém stavu a jsou také hlášeny v *error.xml*. <br> Symbolické odkazy na adresáře mají za následek, že se v adresářích nikdy nezobrazuje označení offline. V důsledku toho se nezobrazuje šedý průřez v adresářích, které ukazují, že adresáře jsou offline a veškerý přidružený obsah byl zcela nahrán do Azure. |
| **2.** |Odstranění | Kvůli chybě v této verzi, pokud je odstraněna sdílená složka NFS, sdílená složka nemusí být smazána. V části stav sdílení se zobrazí *odstranění*.  |K tomu dojde pouze v případě, že sdílená složka používá nepodporovaný název souboru. |
| **3.** |Kopírovat | Kopírování dat se nepovedlo s chybou: požadovanou operaci nešlo dokončit kvůli omezení systému souborů.  |Alternativní datový proud (ADS) přidružený k velikosti souboru větší než 128 KB není podporován.   |


## <a name="next-steps"></a>Další kroky

- [Příprava na nasazení Azure Data box Gateway](data-box-gateway-deploy-prep.md).
- [Příprava na nasazení Azure Data box Edge](../databox-online/azure-stack-edge-deploy-prep.md).
