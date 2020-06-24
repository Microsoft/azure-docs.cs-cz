---
title: Stavy předplatných Azure
description: Tento článek popisuje různé stavy předplatných Azure.
keywords: azure subscription state status
author: anuragdalmia
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: andalmia
ms.openlocfilehash: 8deda3d8f584c83b61ae50c86c3ee9f43b247ae2
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/12/2020
ms.locfileid: "84735400"
---
# <a name="azure-subscription-states"></a>Stavy předplatných Azure

Tento článek popisuje různé stavy, které může mít předplatné Azure. Tyto stavy se v oblastech předplatných na webu Azure Portal zobrazují v poli **Stav**.

| Stav předplatného | Popis |
|-------------| ----------------|
| **Aktivní** | Vaše předplatné Azure je aktivní. Předplatné můžete využít k nasazení nových prostředků a správě existujících.<br><br>Všechny operace (PUT, PATCH, DELETE, POST, GET) jsou k dispozici pro poskytovatele prostředků, kteří jsou [registrovaní pro vaše předplatné](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). |
| **Odstraněné** | Vaše předplatné Azure se odstranilo společně se všemi příslušnými prostředky a daty.<br><br>Nejsou dostupné žádné operace. |
| **Disabled** (Zakázáno) | Vaše předplatné Azure je zakázané a už ho nejde používat k vytváření nebo správě prostředků Azure. V tomto stavu se zruší přidělení vašich virtuálních počítačů, uvolní se dočasné IP adresy, úložiště přejde do režimu jen pro čtení a ostatní služby jsou zablokované. K zablokování předplatného může dojít z následujících důvodů: Možná vypršela platnost vašeho kreditu. Možná jste dosáhli svého limitu útraty. Máte fakturu po splatnosti. Překročili jste limit své platební karty. Případně mohlo dojít k explicitnímu zablokování nebo zrušení předplatného. V závislosti na typu předplatného může předplatné zůstat zablokované 1 až 90 dnů. Po uplynutí této doby se trvale odstraní. Další informace najdete v tématu [Opětovná aktivace zablokovaného předplatného Azure](subscription-disabled.md).<br><br>Operace pro vytváření nebo aktualizaci prostředků (PUT, PATCH) jsou zakázané. Operace, které provádějí akci (POST), jsou také zakázané. Můžete načítat nebo odstraňovat prostředky (GET, DELETE). Vaše prostředky jsou stále k dispozici. |
| **Platnost vypršela** | Platnost vašeho předplatného Azure vypršela, protože se zrušilo. Předplatné, jehož platnost vypršela, můžete znovu aktivovat. Další informace najdete v tématu [Opětovná aktivace zablokovaného předplatného Azure](subscription-disabled.md).<br><br>Operace pro vytváření nebo aktualizaci prostředků (PUT, PATCH) jsou zakázané. Operace, které provádějí akci (POST), jsou také zakázané. Můžete načítat nebo odstraňovat prostředky (GET, DELETE).|
| **Po splatnosti** | Vaše předplatné Azure má neuhrazenou platbu. Vaše předplatné je stále aktivní, ale neuhrazení poplatků může vést k jeho zablokování. Další informace najdete v tématu [Řešení zůstatku po splatnosti u předplatného Azure](resolve-past-due-balance.md).<br><br>Jsou dostupné všechny operace. |
