---
title: Přehled plán, nabídka, kvóty a předplatné Azure zásobníku | Microsoft Docs
description: Jako operátor cloudu chcete pochopit plány, nabídky, kvóty a předplatná Azure zásobníku.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/20/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: fcf19f486ebdc739f3d5c7b25215ba8726462a56
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="plan-offer-quota-and-subscription-overview"></a>Přehled plánů, nabídek, kvót a předplatných

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

[Azure zásobníku](azure-stack-poc.md) umožňuje poskytovat širokou škálu služeb databáze systému SQL Server, SharePoint, Exchange, jako jsou virtuální počítače a dokonce i [položky Azure Marketplace](azure-stack-marketplace-azure-items.md). Jako operátor zásobník Azure konfiguraci a poskytování těchto služeb v zásobníku Azure pomocí plánů, nabídky a kvóty.

Nabízí obsahovat jeden nebo více plánů a každý plán zahrnuje jednu nebo více služeb. Vytvořením plány a jejich kombinace do jiné nabídky, řízení
- které službám a prostředkům uživatelé mají přístup k
- množství těchto prostředků, které uživatelé můžou využívat.
- oblasti, které mají přístup k prostředkům

Při předvádění službu, postupujte podle těchto kroků:

1. Přidáte službu, kterou chcete poskytovat uživatelům.
2. Vytvořte plán, který obsahuje jeden nebo více služeb. Při vytváření plánu, vyberte nebo vytvořte kvóty, které definují omezení prostředků každé služby v plánu.
3. Vytvořte nabídku, který obsahuje jeden nebo více plány (včetně základní a plány volitelné rozšíření).

Po vytvoření nabídky mohou vaši uživatelé přihlásit k službám a prostředkům, které poskytuje přístup. Uživatelé se mohou přihlásit k tolik nabídky požadovaným způsobem. Následující diagram ukazuje jednoduchý příklad uživatele, který se připojila ke dvěma nabídky. Každý nabídka má plán nebo dva a každý plán uděluje uživateli přístup ke službám.

![](media/azure-stack-key-features/image4.png)

## <a name="plans"></a>Plány

Plány jsou seskupení jednu nebo více služeb. Jako operátor zásobník Azure můžete [vytvářet plány](azure-stack-create-plan.md) a nabídnout uživatelům. Pak vaši uživatelé přihlásit k vaší nabídky pro plány a službách, které obsahují. Při vytváření plánů, nezapomeňte nastavit vaší kvóty, definovat základní plánu a zvážit, včetně plánů volitelné rozšíření.

### <a name="quotas"></a>Kvóty

Ke správě vašeho kapacitu cloudu, vyberte nebo vytvořte kvóty pro každou službu v plánu. Kvóty definovat limity horní prostředků, které uživatel předplatné můžete zřizovat nebo využívat. Například kvótu, mohou povolovat uživatel vytvoří až pět virtuálních počítačů. Kvóty můžete omezit přístup k celé řadě prostředků, jako jsou virtuální počítače, paměti RAM a procesoru omezení.

Kvóty lze nakonfigurovat podle oblasti. Plán obsahující výpočetní služby z oblasti A může mít například kvótu dva virtuální počítače, 4 GB paměti RAM a 10 jader procesoru. V Development Kit zásobník Azure, pouze jedna oblast (s názvem *místní*) je k dispozici.

Další informace o [typy kvót v zásobníku Azure](azure-stack-quota-types.md). 

### <a name="base-plan"></a>Základní plán

Při vytváření nabídky, Správce služeb můžete zahrnout základního plánu. Tyto základní plány jsou zahrnuté ve výchozím nastavení, pokud se uživatel přihlásí na tuto nabídku. Pokud se uživatel přihlásí, kterým mají přístup všichni zprostředkovatelé prostředků zadaný v těchto plánech základní (s odpovídající kvóty).

### <a name="add-on-plans"></a>Doplňkové plány

Rozšíření plány jsou volitelné plány, které přidáte do nabídky. Plány rozšíření nejsou zahrnuté ve výchozím nastavení v rámci předplatného. Rozšíření plány jsou další plány (s kvóty) k dispozici v nabídku, můžete k jejich odběry přidat odběratele. Například můžete nabízet základní plán s prostředků omezené zkušební verzi a plán rozšíření s více značné prostředky pro zákazníky, kteří se rozhodnete přijmout službu.

## <a name="offers"></a>Nabídky

Nabídky jsou skupiny jeden nebo více plánů, které vytvoříte tak, aby uživatelé se mohou přihlásit k nim. Nabízejí Alpha může například obsahovat plán A obsahující sadu výpočetní služby a plánování B obsahující sadu úložiště a síťové služby. 

Pokud jste [vytvořit nabídku](azure-stack-create-offer.md), musí obsahovat alespoň jeden základního plánu, ale můžete vytvořit také plány rozšíření, které uživatele můžete přidat do své předplatné.


## <a name="subscriptions"></a>Předplatná

Předplatné je, jak uživatelé přistupovat k vaší nabídky. Pokud jste Azure zásobníku operátor u poskytovatele služeb, uživatelé (klienty) koupit vašim službám prostřednictvím registrace k vaší nabídky. Pokud jste Azure zásobníku operátor v organizaci, uživatelé (zaměstnanci) může přihlásit k služby, které nabízejí bez placení. Každé kombinaci uživatele s nabídku je jedinečný předplatné. Proto uživatel může mít odběry více nabídky, ale každý odběr platí jenom jednu nabídku. Plány, nabídky a kvóty platí pouze pro každé předplatné jedinečné – nemohou být sdíleny mezi předplatnými. Každý prostředek, který uživatel vytvoří je přidružen jeden odběr.


### <a name="default-provider-subscription"></a>Výchozí zprostředkovatel předplatné

Výchozí zprostředkovatel předplatné se automaticky vytvoří při nasazení Azure zásobníku Development Kit. Toto předplatné slouží ke správě Azure zásobníku, nasadit další zprostředkovatelé prostředků a vytvořte plány a nabízí pro uživatele. Zabezpečení a licencování z důvodů by neměl lze použít ke spuštění úloh zákazníka a aplikace. 

## <a name="next-steps"></a>Další postup

[Vytvoření plánu](azure-stack-create-plan.md)
