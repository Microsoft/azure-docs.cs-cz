---
title: Stavy předplatných Azure
description: Tento článek popisuje různé stavy předplatných Azure.
keywords: azure subscription state status
author: anuragdalmia
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: andalmia
ms.openlocfilehash: 29af3d064b481548cdb9b9518e9735eb34aaf034
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270239"
---
# <a name="azure-subscription-states"></a>Stavy předplatných Azure

Tento článek popisuje různé stavy, které může mít předplatné Azure. Tyto stavy se v oblastech předplatných na webu Azure Portal zobrazují v poli **Stav**.

| Stav předplatného | Popis |
|-------------| ----------------|
| **Aktivní** | Vaše předplatné Azure je aktivní. Předplatné můžete využít k nasazení nových prostředků a správě existujících.|
| **Odstraněné** | Vaše předplatné Azure se odstranilo společně se všemi příslušnými prostředky a daty. |
| **Disabled** (Zakázáno) | Vaše předplatné Azure je zakázané a už ho nejde používat k vytváření nebo správě prostředků Azure. V tomto stavu se zruší přidělení vašich virtuálních počítačů, uvolní se dočasné IP adresy, úložiště přejde do režimu jen pro čtení a ostatní služby jsou zablokované. K zablokování předplatného může dojít z následujících důvodů: Možná vypršela platnost vašeho kreditu. Možná jste dosáhli svého limitu útraty. Máte fakturu po splatnosti. Překročili jste limit své platební karty. Případně mohlo dojít k explicitnímu zablokování nebo zrušení předplatného. V závislosti na typu předplatného může předplatné zůstat zablokované 1 až 90 dnů. Po uplynutí této doby se trvale odstraní. Další informace najdete v tématu [Opětovná aktivace zablokovaného předplatného Azure](subscription-disabled.md). |
| **Platnost vypršela** | Platnost vašeho předplatného Azure vypršela, protože se zrušilo. Předplatné, jehož platnost vypršela, můžete znovu aktivovat. Další informace najdete v tématu [Opětovná aktivace zablokovaného předplatného Azure](subscription-disabled.md).|
| **Po splatnosti** | Vaše předplatné Azure má neuhrazenou platbu. Vaše předplatné je stále aktivní, ale neuhrazení poplatků může vést k jeho zablokování. Další informace najdete v tématu [Řešení zůstatku po splatnosti u předplatného Azure](resolve-past-due-balance.md). |
