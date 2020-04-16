---
title: Co dělat v případě přerušení služby Azure, které má vliv na Azure Key Vault – Azure Key Vault | Dokumenty společnosti Microsoft
description: Zjistěte, co dělat v případě přerušení služby Azure, které má vliv na Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 96929dcbe3d51589b0c3c0df89671dadb20e37cc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422946"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Dostupnost a redundance azure key vaultu

Azure Key Vault obsahuje několik vrstev redundance a ujistěte se, že vaše klíče a tajné klíče zůstanou dostupné pro vaši aplikaci i v případě, že jednotlivé součásti služby se nezdaří.

Obsah trezoru klíčů se replikuje v rámci oblasti a do sekundární oblasti vzdálené alespoň 150 mil, ale ve stejné zeměpisné oblasti. To udržuje vysokou odolnost vašich klíčů a tajemství. Podrobnosti o konkrétních párech oblastí najdete v dokumentu [spárovaných oblastí Azure.](../../best-practices-availability-paired-regions.md)

Pokud se nezdaří jednotlivé součásti v rámci služby trezoru klíčů, alternativní součásti v rámci oblasti krok slouží váš požadavek a ujistěte se, že nedochází ke snížení funkčnosti. Není nutné provádět žádné akce k aktivaci tohoto. Stane se to automaticky a bude to pro vás transparentní.

Ve výjimečných případech, že není k dispozici celá oblast Azure, požadavky, které provedete z Azure Key Vault v této oblasti jsou automaticky směrovány *(převzetí služby při selhání)* do sekundární oblasti. Pokud je primární oblast opět k dispozici, požadavky jsou směrovány zpět *(se nezdařilo zpět)* do primární oblasti. Opět platí, že není nutné provádět žádnou akci, protože k tomu dochází automaticky.

Díky tomuto návrhu s vysokou dostupností azure key vault nevyžaduje žádné prostoje pro aktivity údržby.

Existuje několik upozornění, které je třeba si uvědomit:

* V případě převzetí služeb při selhání oblasti může trvat několik minut pro službu převzetí služeb převzetí služeb. Požadavky, které jsou provedeny během této doby může selhat, dokud převzetí služeb při selhání dokončí.
* Po dokončení převzetí služeb při selhání je trezor klíčů v režimu jen pro čtení. Požadavky, které jsou podporovány v tomto režimu jsou:
  * Seznam trezorů klíčů
  * Získání vlastností trezorů klíčů
  * Seznam tajemství
  * Získejte tajemství
  * Klíče seznamu
  * Získat (vlastnosti) klíčů
  * Šifrování
  * Dešifrování
  * Zábal
  * Rozbalit
  * Ověřit
  * Znaménko
  * Backup
* Po selhání převzetí služeb při selhání jsou k dispozici všechny typy požadavků (včetně požadavků na čtení *a* zápis).

