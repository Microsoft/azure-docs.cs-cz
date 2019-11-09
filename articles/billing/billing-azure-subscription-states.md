---
title: Stavy předplatných Azure
description: Popisuje různé stavy předplatných Azure.
keywords: azure subscription state status
author: anuragdalmia
manager: andalmia
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: andalmia
ms.openlocfilehash: 27664fef04e9b52294f0689eba49f16f84433c16
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888456"
---
# <a name="azure-subscription-states"></a>Stavy předplatných Azure
Tento článek popisuje různé stavy, které může mít předplatné Azure. Tyto stavy se v oknech předplatných zobrazují v poli Stav.

| Stav předplatného | Popis |
|-------------| ----------------|
| **Aktivní** | Vaše předplatné Azure je aktivní. Toto předplatné můžete využít k nasazení nových prostředků a správě existujících.|
| **Po splatnosti** | Vaše předplatné Azure má neuhrazenou platbu. Vaše předplatné je stále aktivní, ale neuhrazení poplatků může vést k jeho zablokování. [Řešení zůstatku po splatnosti u předplatného Azure](https://docs.microsoft.com/azure/billing/billing-azure-subscription-past-due-balance) |
| **Disabled** (Zakázáno) | Vaše předplatné Azure je zakázané a už ho nejde používat k vytváření nebo správě prostředků Azure. V tomto stavu se zruší přidělení vašich virtuálních počítačů, uvolní se dočasné IP adresy, úložiště přejde do režimu jen pro čtení a ostatní služby jsou zablokované. K zablokování předplatného může dojít, protože vypršela platnost vašeho kreditu, dosáhli jste nastaveného limitu útraty, překročili jste limit vaší platební karty nebo jste ho explicitně zakázali nebo zrušili. V závislosti na typu předplatného a důvodu jeho zablokování může předplatné zůstat zablokované 1 až 90 dnů a potom se trvale odstraní. [Opětovná aktivace zablokovaného předplatného Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)|
| **Odstraněné** | Vaše předplatné Azure se odstranilo společně se všemi příslušnými prostředky a daty. |
