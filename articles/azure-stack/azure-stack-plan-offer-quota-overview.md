---
title: Přehled plánu, nabídky, kvót a předplatného Azure Stack | Dokumentace Microsoftu
description: Jako operátor cloudu chci pochopit plány, nabídky, kvóty a předplatných Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/12/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 343c29db4ac5fba37d8ea1b17862b86566ae15ca
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2018
ms.locfileid: "49309191"
---
# <a name="plan-offer-quota-and-subscription-overview"></a>Přehled plánů, nabídek, kvót a předplatných

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

[Azure Stack](azure-stack-poc.md) umožňuje poskytovat celou řadu služeb, jako jsou virtuální počítače, databáze systému SQL Server, SharePoint, Exchange a dokonce i [položky Azure Marketplace](azure-stack-marketplace-azure-items.md). Jako operátory Azure stacku konfigurace a poskytování těchto služeb ve službě Azure Stack pomocí plánů, nabídek a kvót.

Nabídky obsahovat jeden nebo více plánů a každý plán obsahuje jednu nebo víc služeb. Vytváření plánů a jejich sloučením do různých nabídek, můžete spravovat:

- Které služby a vaši uživatelé měli přístup k prostředkům.
- Množství prostředků, které uživatelé můžou využívat.
- Oblasti, které mají přístup k prostředkům.

Při předvádění služby postupujte podle těchto kroků:

1. Přidáte službu, kterou chcete doručovat uživatelům.
2. Vytvořte plán, který má jednu nebo víc služeb. Při vytváření plánu, vyberte nebo vytvořte kvóty, které definují omezení prostředků každé služby v plánu.
3. Vytvořte nabídku, která obsahuje jeden nebo více plánů. Nabídka může obsahovat základní plány a volitelné doplňkové plány.

Po vytvoření nabídky, uživatelé mohou přihlásit k mu umožní přístup k službám a prostředkům, které poskytuje tuto nabídku. Uživatelé můžou přihlásit k libovolný počet nabídek, jak chtějí. Následující obrázek znázorňuje jednoduchý příklad uživatele, který se připojila k dvou nabídek. Každá nabídka má plán jedné až dvou a každý plán jim uděluje přístup ke službám.

![Předplatného tenanta pomocí nabídky a plány](media/azure-stack-key-features/image4.png)

## <a name="plans"></a>Plány

Plány představují seskupení jedné nebo více služeb. Jako operátory Azure stacku můžete [vytváření plánů](azure-stack-create-plan.md) nabízí svým uživatelům. Uživatelé pak předplatit nabídek, aby mohli používat plány a služby, které patří mezi ně. Při vytváření plánů, ujistěte se, že pro nastavení svoje kvóty, definujte základní plány a zvážit volitelné doplňkové plány.

### <a name="quotas"></a>Kvóty

Vám pomůžou spravovat kapacitu cloudu, můžete použít předem nakonfigurované *kvóty*, nebo vytvořit novou kvótu pro každou službu v plánu. Kvóty definovat omezení horní prostředků, které můžete zřizovat nebo využívat předplatného uživatele. Například může kvótu umožňují uživateli vytvořit až pět virtuálních počítačů (VM).

Můžete konfigurovat kvóty podle oblasti. Plán, který poskytuje výpočetních služeb pro oblast A může mít například kvótu dva virtuální počítače s 4 GB paměti RAM a 8 jader procesoru.

>[!NOTE]
>V sadě Azure Stack Development Kit, pouze jedna oblast (s názvem *místní*) je k dispozici.

Další informace o [typy kvót ve službě Azure Stack](azure-stack-quota-types.md).

### <a name="base-plan"></a>Základní plán

Při vytváření nabídky, Správce služeb můžete zahrnout základní plán. Tyto základní plány jsou zahrnuté ve výchozím nastavení, pokud se uživatel přihlásí k této nabídce. Pokud se uživatel přihlásí, mají přístup všechny poskytovatele prostředků podle těchto základních plánů (s odpovídajícím kvóty).

### <a name="add-on-plans"></a>Doplňkové plány

Doplňkové plány jsou volitelné plány, které přidáte do nabídky. Doplňkové plány nejsou zahrnuté ve výchozím nastavení v rámci předplatného. Doplňkové plány jsou další plány (s kvótami) k dispozici v nabídce, odběratele můžete přidat do svých předplatných. Například můžete nabízet základní plán s omezenými zdroji pro zkušební verzi a doplňkový plán s více prostředky pro zákazníky, kteří se rozhodnete přijmout službu.

## <a name="offers"></a>Nabídky

Nabídky jsou skupiny jednoho nebo několika plánů, které vytvoříte tak, aby uživatelé se mohou přihlásit k nim. Alfa nabízí může obsahovat například A plán, který poskytuje sadu výpočetních služeb a plánování B, která poskytuje sada služeb úložiště a sítě.

Pokud jste [vytvořte nabídku](azure-stack-create-offer.md), musí obsahovat aspoň jeden základní plán, ale můžete také vytvořit doplňkové plány, které uživatelé můžou přidávat do svého předplatného.

## <a name="subscriptions"></a>Předplatná

Předplatné je, jak uživatelé přistupovat k vaší nabídky. Pokud jste operátory Azure stacku pro poskytovatele služeb, uživatelé (tenantů) koupit služby prostřednictvím přihlášení odběru na vaše nabídky. Pokud jste operátory Azure stacku v organizaci, uživatelé (zaměstnanci) může přihlásit k službám, které nabízejí bez nutnosti platit.

Každá kombinace uživatele, jejichž nabídka je jedinečný předplatné. Uživatel může mít předplatné více nabídek, ale každé předplatné se vztahuje pouze na jednu nabídku. Plánů, nabídek a kvót pouze použít na jedinečných předplatné – nemohou být sdíleny mezi předplatnými. Každý prostředek, který uživatel vytvoří je přidružená k jednomu předplatnému.

### <a name="default-provider-subscription"></a>Výchozí předplatné poskytovatele.

Při nasazení Azure Stack Development Kit, je automaticky vytvořeno výchozí předplatné poskytovatele. Toto předplatné můžete použít ke správě služby Azure Stack, nasadit další prostředků poskytovatele a vytvořit plány a nabídky pro uživatele. Zabezpečení a správy licencí z důvodů není vhodné používat pro spouštění úloh zákazníka a aplikací.

## <a name="next-steps"></a>Další postup

Další informace o plány a nabídky, naleznete v tématu [vytvořit plán](azure-stack-create-plan.md).
