---
title: Správa oprávnění k prostředkům na uživatele v Azure zásobníku (Správce služeb a klientů) | Microsoft Docs
description: Jako správce služeb nebo klienta zjistěte, jak ke správě oprávnění RBAC.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: fenila
editor: ''
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: mabrigg
ms.reviewer: thomas.roettinger
ms.openlocfilehash: 0e50ea44ebb0b0a7285dab04666dd55cad480c6a
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29385632"
---
# <a name="manage-role-based-access-control"></a>Správa řízení přístupu na základě rolí

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Uživatel v zásobníku Azure může být čtečky, vlastníka nebo přispěvatele pro každou instanci předplatné, skupinu prostředků nebo služby. Například uživatel A může mít čtečky oprávnění k odběru jeden, ale mají oprávnění vlastníka pro virtuální počítač sedm.

 - Čtečka: Zobrazit vše, co uživatel, ale nelze provést žádné změny.
 - Přispěvatel: Uživatel může spravovat všechno kromě přístupu k prostředkům.
 - Vlastník: Uživatel může spravovat všechno včetně přístupu k prostředkům.

## <a name="set-access-permissions-for-a-user"></a>Nastavte přístupová oprávnění pro uživatele

1. Přihlaste se pomocí účtu, který má oprávnění vlastníka prostředku, který chcete spravovat.
2. V okně pro prostředek, klikněte **přístup** ikonu ![](media/azure-stack-manage-permissions/image1.png).
3. V **uživatelé** okně klikněte na tlačítko **role**.
4. V **role** okně klikněte na tlačítko **přidat** se přidat oprávnění pro uživatele.

## <a name="set-access-permissions-for-a-universal-group"></a>Nastavte přístupová oprávnění pro skupinu jako univerzální 

> [!Note]  
Platí pouze pro služby Active Directory Federated Services (AD FS).

1. Přihlaste se pomocí účtu, který má oprávnění vlastníka prostředku, který chcete spravovat.
2. V okně pro prostředek, klikněte **přístup** ikonu ![](media/azure-stack-manage-permissions/image1.png).
3. V **uživatelé** okně klikněte na tlačítko **role**.
4. V **role** okně klikněte na tlačítko **přidat** přidání oprávnění pro univerzální skupiny Active Directory skupiny.

## <a name="next-steps"></a>Další postup
[Přidání tenanta Azure Stacku](azure-stack-add-new-user-aad.md)

