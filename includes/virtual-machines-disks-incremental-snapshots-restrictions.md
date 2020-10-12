---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f4af8a150b062526f08c1d15581ec26e2fe12d8c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87102553"
---
- Přírůstkové snímky se aktuálně nedají přesunout mezi předplatnými.
- V současné době můžete v daném okamžiku generovat pouze identifikátory URI SAS až na pět snímků konkrétní rodiny snímků.
- Pro určitý disk nemůžete vytvořit přírůstkový snímek mimo předplatné tohoto disku.
- Až sedm přírůstkových snímků na disk lze vytvořit každých pět minut.
- Pro jeden disk lze vytvořit celkem 200 přírůstkových snímků.
- Nemůžete získat změny mezi snímky provedenými před a poté, co jste změnili velikost nadřazeného disku na hranici 4 TB. Například jste pořídili postup přírůstkového snímku – a když velikost disku byla 2 TB. Nyní jste zvýšili velikost disku na 6 TB a potom byl vytvořen další snímek přírůstkového snímku – b. Nemůžete získat změny mezi snímky snímků a a a snímkem b. Je nutné znovu stáhnout úplnou kopii snímku b vytvořenou po změně velikosti. Následně můžete získat změny mezi snímky-b a snímky vytvořenými po snímku b. 
