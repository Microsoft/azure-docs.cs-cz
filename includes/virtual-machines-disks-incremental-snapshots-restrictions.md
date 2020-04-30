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
ms.openlocfilehash: 39a013f5f4b587137366147ade77f0be1b353c4c
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204513"
---
- Přírůstkové snímky se aktuálně nedají přesunout mezi předplatnými.
- V současné době můžete v daném okamžiku generovat pouze identifikátory URI SAS až na pět snímků konkrétní rodiny snímků.
- Pro určitý disk nemůžete vytvořit přírůstkový snímek mimo předplatné tohoto disku.
- Až sedm přírůstkových snímků na disk lze vytvořit každých pět minut.
- Pro jeden disk lze vytvořit celkem 200 přírůstkových snímků.
- Nemůžete získat změny mezi snímky provedenými před a po změně velikosti nadřazeného disku napříč hranicí 4 TB. Je nutné znovu stáhnout úplnou kopii snímku vytvořeného po změně velikosti. Následně můžete získat změny mezi snímky vytvořenými po změně velikosti v rozmezí 4 TB. 
