---
title: Stavy předplatných Azure
description: Popisuje různé stavy předplatných Azure.
keywords: azure subscription state status
author: anuragdalmia
ms.reviewer: andalmia
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: andalmia
ms.openlocfilehash: df8a60c0249eb51168e1a67cdd67116813312626
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200533"
---
# <a name="azure-subscription-states"></a>Stavy předplatných Azure
Tento článek popisuje různé stavy, které může mít předplatné Azure. Tyto stavy se v oknech předplatných zobrazují v poli Stav.

| Stav předplatného | Popis |
|-------------| ----------------|
| **Aktivní** | Vaše předplatné Azure je aktivní. Toto předplatné můžete využít k nasazení nových prostředků a správě existujících.|
| **Po splatnosti** | Vaše předplatné Azure má neuhrazenou platbu. Vaše předplatné je stále aktivní, ale neuhrazení poplatků může vést k jeho zablokování. [Řešení zůstatku po splatnosti u předplatného Azure](https://docs.microsoft.com/azure/billing/billing-azure-subscription-past-due-balance) |
| **Disabled** (Zakázáno) | Vaše předplatné Azure je zakázané a už ho nejde používat k vytváření nebo správě prostředků Azure. V tomto stavu se zruší přidělení vašich virtuálních počítačů, uvolní se dočasné IP adresy, úložiště přejde do režimu jen pro čtení a ostatní služby jsou zablokované. K zablokování předplatného může dojít, protože vypršela platnost vašeho kreditu, dosáhli jste nastaveného limitu útraty, překročili jste limit vaší platební karty nebo jste ho explicitně zakázali nebo zrušili. V závislosti na typu předplatného a důvodu jeho zablokování může předplatné zůstat zablokované 1 až 90 dnů a potom se trvale odstraní. [Opětovná aktivace zablokovaného předplatného Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)|
| **Odstraněné** | Vaše předplatné Azure se odstranilo společně se všemi příslušnými prostředky a daty. |
