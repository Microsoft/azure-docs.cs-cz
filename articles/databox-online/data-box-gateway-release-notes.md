---
title: Poznámky k verzi Azure Data Box brány obecné dostupnosti | Dokumentace Microsoftu
description: Popisuje důležité otevřených problémů a jejich řešení Azure Data Box brány se systémem verzi všeobecné dostupnosti.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/26/2019
ms.author: alkohli
ms.openlocfilehash: f4ee3a5bd754335ab1c7f124671e9c37307a6a28
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58499875"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Poznámky k verzi Azure dat pole Edge nebo Azure Data Box brány obecné dostupnosti

## <a name="overview"></a>Přehled

Následující zpráva k vydání verze Identifikujte kritické otevřené problémy a vyřešené problémy pro obecné dostupnosti (GA) verzi nástroje Azure Data Box Edge a Azure Data Box brány.

Zpráva k vydání verze se průběžně aktualizuje, a při zjištění zásadních problémů vyžadujících alternativní řešení, se přidají. Před nasazením brány Data pole Edge/dat pole, pečlivě si prostudujte informace obsažené v poznámkách k verzi.

Verze GA odpovídá verzí softwaru:

- **Brána pole dat 1903 (1.5.814.447)**
- **Data Box Edge 1903 (1.5.814.447)**


## <a name="whats-new"></a>Co je nového

- **Nové Image virtuálního disku** – nové soubory VHDX a VMDK jsou teď dostupné na webu Azure Portal. Stáhněte si tyto Image, jak zřizovat, konfigurovat a nasazování nových zařízení Data Box brány všeobecné dostupnosti. Brána pole dat zařízení, vytvořené ve starší verzi preview verze nelze aktualizovat na tuto verzi. Další informace najdete v části [Příprava na nasazení Azure Data Box Gateway](data-box-gateway-deploy-prep.md).
- **Podpora systému souborů NFS** -podporu systému souborů NFS je aktuálně ve verzi preview a dostupné pro verze 3.0 a v4.1 klienty, kteří přístup k zařízení Data Box Edge a brána dat pole.
- **Odolnost proti chybám úložiště** – vaše Data Box hraniční zařízení dokázal selhání jedním datovým diskem s funkcí úložiště odolnost proti chybám. Tato funkce je aktuálně ve verzi Preview. Můžete povolit úložiště odolnost proti chybám tak, že vyberete **odolné** možnost **nastavení úložiště** místní webové uživatelské rozhraní.


## <a name="known-issues-in-ga-release"></a>Známých problémech ve verzi GA

Následující tabulka obsahuje souhrn známé problémy pro bránu dat pole verze.

| Ne. | Funkce | Problém | Alternativní řešení a poznámky |
| --- | --- | --- | --- |
| **1.** |Typy souborů | Nejsou podporovány následující typy souborů: znak soubory, soubory bloku, sokety, kanály, symbolické odkazy.  |Kopírování tyto souborů sdílet výsledky v 0-length souborech vytvářených v systému souborů NFS. Tyto soubory zůstávají v chybovém stavu a jsou rovněž uvedeny v *error.xml*. <br> Symbolické odkazy k adresářům za následek adresáře nikdy načtení označený v režimu offline. V důsledku toho nemusí zobrazit šedé napříč na adresáře, které označuje, že adresáře jsou offline a související obsah byl zcela nahráli do Azure. |
| **2.** |Odstranění | Z důvodu chyby v této verzi Pokud se odstraní sdílenou složku systému souborů NFS, pak sdílenou složku se možná neodstranily. Stav sdílené složky se zobrazí *odstranění*.  |K tomu dojde pouze v případě, že sdílená složka používá představuje název souboru. |
| **3.** |Kopírovat | Kopírování dat se nezdaří s chybou:  Požadovanou operaci nelze dokončit z důvodu omezení systému souborů.  |Alternativní datový Stream (reklamy) přidružené k souboru větší než 128 KB se nepodporuje.   |


## <a name="next-steps"></a>Další postup

- [Příprava na nasazení Azure Data Box Gateway](data-box-gateway-deploy-prep.md).
- [Příprava na nasazení Azure Data Box Edge](data-box-edge-deploy-prep.md).
